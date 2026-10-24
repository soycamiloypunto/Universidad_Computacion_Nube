# Diagramas de Arquitectura — AgroSilvania Conectada (AWS Cloud)
**Estudiante:** Cristian Camilo Tabares Villanueva  
**Materia:** COMPUTACION EN LA NUBE / 55550 / PRIMER BLOQUE / 26P05  
**Docente:** Mario A. Reales  
**Institución:** Corporación Unificada Nacional de Educación Superior (CUN)

---

## 1. Diagrama Lógico de Red y Topología Multi-AZ (AWS VPC)

El siguiente diagrama en **Mermaid** describe la topología de red distribuida en dos Zonas de Disponibilidad (`us-east-1a` y `us-east-1b`), con tres niveles de aislamiento: Subredes Públicas (ALB e Internet Gateway), Subredes Privadas de Aplicación (AWS ECS Fargate con microservicios backend en Java) y Subredes Privadas de Datos (Amazon Aurora Serverless MySQL y Amazon DynamoDB).

```mermaid
flowchart TD
    subgraph InternetClients ["Clientes y Dispositivos Externos"]
        CampesinoDevice["Sensores IoT ESP32 (Fincas Silvania)"]
        WebUsers["Consumidores Urbanos / Navegador Web"]
        Admins["Administrador / Operaciones TI"]
    end

    subgraph AWSCloud ["Amazon Web Services (Región: us-east-1)"]
        subgraph EdgeLayer ["Capa de Borde y Distribución Global"]
            CloudFront["Amazon CloudFront (CDN + SSL)"]
            S3Web["Amazon S3 Bucket (SPA React Frontend)"]
            WAF["AWS WAF (Firewall Web + DDoS Shield)"]
            IoTCore["AWS IoT Core (MQTT Broker / X.509 TLS 1.3)"]
        end

        subgraph VPC ["AWS Virtual Private Cloud (10.0.0.0/16)"]
            subgraph PublicSubnets ["Subredes Públicas (Multi-AZ: 10.0.1.0/24 & 10.0.2.0/24)"]
                IGW["Internet Gateway"]
                ALB["Application Load Balancer (ALB)"]
                NATGateway["NAT Gateways (Salida Segura a Internet)"]
            end

            subgraph PrivateAppSubnets ["Subredes Privadas de Aplicación (10.0.10.0/24 & 10.0.20.0/24)"]
                subgraph AZ1_App ["Zona de Disponibilidad us-east-1a"]
                    ECS_Task1["AWS ECS Fargate Task (Java Spring Boot)"]
                end
                subgraph AZ2_App ["Zona de Disponibilidad us-east-1b"]
                    ECS_Task2["AWS ECS Fargate Task (Java Spring Boot)"]
                end
                AutoScaling["Auto Scaling Group (CPU > 70% / HTTP Requests)"]
            end

            subgraph PrivateDataSubnets ["Subredes Privadas de Datos (10.0.30.0/24 & 10.0.40.0/24)"]
                subgraph AuroraCluster ["Amazon Aurora Serverless v2 (MySQL ACID)"]
                    AuroraWriter["Aurora MySQL Writer (us-east-1a)"]
                    AuroraReader["Aurora MySQL Reader Replica (us-east-1b)"]
                end
            end
        end

        subgraph ServerlessIoT ["Capa de Procesamiento Serverless & NoSQL"]
            LambdaIoT["AWS Lambda (Procesamiento de Reglas & Alertas)"]
            DynamoDB[("Amazon DynamoDB\nTelemetría Sensores IoT")]
            SNS["Amazon SNS (Alertas SMS / Push Heladas)"]
            SecretsManager["AWS Secrets Manager (Credenciales BD)"]
            KMS["AWS KMS (Cifrado AES-256 Llaves Maestras)"]
        end
    end

    %% Conexiones
    CampesinoDevice -->|MQTT sobre TLS 1.3| IoTCore
    IoTCore -->|Regla IoT| LambdaIoT
    LambdaIoT -->|Persistencia Series Temporales| DynamoDB
    LambdaIoT -->|Temperatura < 3°C| SNS
    SNS -->|Alerta Helada SMS/Push| CampesinoDevice

    WebUsers -->|HTTPS / TLS 1.3| CloudFront
    CloudFront -->|Lectura Assets Estáticos| S3Web
    CloudFront -->|Llamadas API REST /api/*| WAF
    WAF --> ALB
    ALB --> IGW
    ALB -->|Balanceo Round-Robin| ECS_Task1
    ALB -->|Balanceo Round-Robin| ECS_Task2

    ECS_Task1 -->|Queries Transaccionales| AuroraWriter
    ECS_Task2 -->|Queries de Consulta| AuroraReader
    AuroraWriter -.->|Replicación Sincrónica Multi-AZ| AuroraReader

    ECS_Task1 -.->|Credenciales Seguras| SecretsManager
    ECS_Task2 -.->|Credenciales Seguras| SecretsManager
    ECS_Task1 -.->|Consulta Telemetría| DynamoDB
    ECS_Task2 -.->|Consulta Telemetría| DynamoDB

    KMS -.->|Cifrado en Reposo| AuroraCluster
    KMS -.->|Cifrado en Reposo| DynamoDB
    KMS -.->|Cifrado en Reposo| S3Web
```

---

## 2. Diagrama de Flujo de Datos: De la Finca a la Mesa

```mermaid
sequenceDiagram
    autonumber
    actor Sensor as Sensor IoT (Silvania)
    participant Broker as AWS IoT Core
    participant Lambda as AWS Lambda
    participant Dynamo as Amazon DynamoDB
    actor Campesino as Productor Agrícola
    actor Comprador as Consumidor Urbano
    participant Frontend as CloudFront + S3 (React)
    participant Backend as ECS Fargate (Java)
    participant Aurora as Amazon Aurora MySQL

    Note over Sensor,Dynamo: 1. Flujo de Telemetría Agrícola Continua
    Sensor->>Broker: Publica MQTT /silvania/finca1/sensores (Temp, Humedad)
    Broker->>Lambda: Dispara Regla IoT en milisegundos
    Lambda->>Dynamo: Almacena registro con TTL y partición SensorId
    opt Si Temperatura < 3°C (Riesgo de Helada)
        Lambda-->>Campesino: Notificación SMS inmediata de activación de aspersores
    end

    Note over Campesino,Aurora: 2. Publicación de Cosecha y Compra Directa
    Campesino->>Frontend: Registra cosecha disponible (200 kg Mora, $4.500/kg)
    Frontend->>Backend: POST /api/v1/productos (Token JWT)
    Backend->>Aurora: INSERT INTO productos (Stock, Precio, ProductorId)
    Comprador->>Frontend: Explora catálogo y genera orden de compra
    Frontend->>Backend: POST /api/v1/pedidos (Checkout)
    Backend->>Aurora: Transacción ACID (Genera Pedido, descuenta Stock)
    Backend-->>Campesino: Notificación de pedido listo para despacho
    Backend-->>Comprador: Confirmación de compra y entrega directa
```

---

## 3. Modelo de Responsabilidad Compartida en AWS

```mermaid
graph TD
    subgraph ClienteResponsabilidad ["Seguridad EN la Nube (Responsabilidad de Cristian Tabares / Equipo)"]
        DataGov["Datos del Cliente y Cumplimiento Ley 1581 de 2012"]
        IAMConfig["Gestión de Identidades y Accesos (IAM con Least Privilege)"]
        AppSec["Seguridad de Microservicios Java (OWASP, Parches Spring Boot)"]
        NetConfig["Configuración de Red (Security Groups, Subredes, Tablas de Ruteo)"]
        Encryption["Cifrado de Datos en Tránsito (TLS 1.3) y en Reposo (KMS AES-256)"]
    end

    subgraph AWSResponsabilidad ["Seguridad DE la Nube (Responsabilidad de Amazon Web Services)"]
        Hardware["Servidores Físicos, Racks y Datacenters Globales"]
        Hypervisor["Virtualización, Hipervisores Nitro y Aislamiento de Cómputo"]
        Facilities["Seguridad Física, Climatización, Red Eléctrica y Detección de Fuego"]
        GlobalNet["Cables Submarinos, Regiones y Zonas de Disponibilidad (Multi-AZ)"]
    end

    DataGov --> IAMConfig --> AppSec --> NetConfig --> Encryption
    Hardware --> Hypervisor --> Facilities --> GlobalNet
```

---
*Este documento y sus diagramas en formato Mermaid pueden ser renderizados directamente en GitHub, Visual Studio Code o herramientas como Mermaid Live Editor.*
