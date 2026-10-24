# ACA · Pitch — Computación en la Nube
## Desafío de Arquitectura Estratégica: Transformación Digital en Colombia
**Caso C: Agro-Industria IoT — Proyecto "AgroSilvania Conectada"**

* **Estudiante:** Cristian Camilo Tabares Villanueva
* **Institución:** Corporación Unificada Nacional de Educación Superior (CUN) — Facultad de Ingeniería de Sistemas
* **Asignatura:** COMPUTACION EN LA NUBE
* **Grupo / Bloque / Ciclo:** 55550 / PRIMER BLOQUE / 26P05
* **Docente Tutor:** Mario A. Reales
* **Repositorio Remoto:** [Universidad_Computacion_Nube](https://github.com/soycamiloypunto/Universidad_Computacion_Nube.git)
* **Presentación Web (GitHub Pages):** [https://soycamiloypunto.github.io/Universidad_Computacion_Nube/](https://soycamiloypunto.github.io/Universidad_Computacion_Nube/)

---

## 🎥 Sustentación en Video (Pitch)
* **Enlace de Sustentación en YouTube (No Listado):** [https://youtu.be/HBWzbVZswPk](https://youtu.be/HBWzbVZswPk)  
*(Sustentación oral de 10 minutos abordando los roles de Arquitecto de Soluciones, Analista FinOps y Oficial de Seguridad).*

---

## 📌 Descripción del Proyecto
Diseño, evaluación técnico-financiera (FinOps) y sustentación de una arquitectura elástica en la nube sobre **Amazon Web Services (AWS)** para solucionar dos problemáticas críticas en el municipio de **Silvania, Cundinamarca**:
1. **Intermediación comercial abusiva:** Plataforma marketplace directo productor-consumidor para venta de café especial, mora, uchuva y hortalizas, eliminando intermediarios que retenían hasta el 65% del margen.
2. **Vulnerabilidad climática:** Telemetría agroclimática en tiempo real con sensores IoT (humedad de suelo, temperatura, radiación) y alertas preventivas tempranas contra heladas.

---

## 📂 Contenido del Paquete y Entregables

| Archivo | Formato | Descripción del Contenido |
| :--- | :---: | :--- |
| **[`ACA_Computacion_Nube_Cristian_Tabares.pdf`](./ACA_Computacion_Nube_Cristian_Tabares.pdf)** | PDF Oficial | Versión final del documento formal para lectura y entrega académica directa en plataforma CUN. |
| **[`ACA_Computacion_Nube_Cristian_Tabares.docx`](./ACA_Computacion_Nube_Cristian_Tabares.docx)** | Word APA 7 | Documento formal editable bajo normas APA 7 (interlineado 1.5, sangría en primera línea, portada institucional, justificación técnica, arquitectura AWS, evaluación FinOps, seguridad, soberanía de datos Ley 1581 y referencias). |
| **[`Modelo_FinOps_TCO_ROI_VAN.xlsx`](./Modelo_FinOps_TCO_ROI_VAN.xlsx)** | Excel | Modelo financiero completo con fórmulas nativas: comparativa On-Premise ($90.700 USD) vs AWS ($28.680 USD), ahorro del 68.3%, ROI del 196.4% y cálculo de VAN ($41.350 USD) a tasa WACC del 12%. |
| **[`Diagramas_Arquitectura.md`](./Diagramas_Arquitectura.md)** | Markdown / Mermaid | Diagrama lógico de red VPC Multi-AZ (`us-east-1`), flujo de datos IoT de la finca a la mesa, y Modelo de Responsabilidad Compartida en código Mermaid listo para renderizar. |
| **[`index.html`](./index.html)** | HTML5 Interactivo | Diapositivas interactivas listas para proyectar o desplegar en GitHub Pages, con temporizador regresivo de 10 minutos integrado y atajos de teclado `[← / →]`. |

---

## 🚀 Cómo Visualizar la Presentación Interactiva
Puedes abrirla de dos formas:
1. **En línea mediante GitHub Pages:** [https://soycamiloypunto.github.io/Universidad_Computacion_Nube/](https://soycamiloypunto.github.io/Universidad_Computacion_Nube/)
2. **Localmente en tu navegador:** Haz doble clic sobre [`index.html`](./index.html) o ejecútalo con `start index.html`.

---

## 📊 Resumen Ejecutivo FinOps (AWS vs Datacenter On-Premise)
* **CAPEX Inicial:** Reducción de USD $38.500 (físico) a solo USD $4.200 (Cloud setup en IaC).
* **OPEX Mensual:** Reducción de USD $1.450 a USD $680 mediante *Compute Savings Plans* y *Aurora Serverless v2*.
* **TCO a 36 Meses:** Ahorro neto de **USD $62.020 (68.3% de reducción)**.
* **Métricas de Inversión:** ROI del **196.4%**, VAN de **USD $41.350** y Payback en **4.8 meses**.

---

## ⚖️ Seguridad, Continuidad y Soberanía de Datos
* **SLA de Disponibilidad:** 99.95% Multi-AZ activo.
* **Métricas de Recuperación:** RPO < 5 minutos y RTO < 15 minutos mediante Terraform.
* **Protección de Datos:** Cumplimiento de la **Ley Estatutaria 1581 de 2012** de Colombia, cifrado en reposo con **AWS KMS (AES-256)** y en tránsito con **TLS 1.3**.
* **Sostenibilidad:** Reducción de hasta un **88% de huella de carbono** certificada por *AWS Customer Carbon Footprint Tool*.

---
**Autor:** Cristian Camilo Tabares Villanueva  
*Ingeniería de Sistemas — CUN 2026*
