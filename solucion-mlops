# Propuesta Técnica: Sistema Inteligente de Monitoreo y Análisis de Tipos de Cambio
**Cliente:** Servicios Generales Electricidad y Pintura K & S S.A.C.S.
**Preparado por:** Zenon Maximo Paredes Ayala
**Fecha:** 17 de abril de 2026

## 1. Resumen Ejecutivo
La presente solución propone la implementación de una arquitectura de datos moderna tipo "Lake House" en AWS. El objetivo es automatizar la extracción, procesamiento y análisis de tipos de cambio (USD, PEN, COP) con un histórico centralizado, permitiendo al equipo directivo ejecutar tareas y visualizar reportes de alta precisión directamente desde Telegram, integrándose de forma nativa con la seguridad corporativa de Microsoft Active Directory.

## 2. Flujo Operativo del Sistema

### 2.1. Interfaz de Control (Capa de Usuario)
* **Canal:** Bot de Telegram personalizado.
* **Acción:** El usuario envía comandos (ej. `/ReporteDelDia`) para disparar procesos.
* **Orquestación:** Un motor de automatización **n8n** recibe la petición y activa una **AWS Lambda** que coordina los servicios de datos en la nube.

### 2.2. Ingesta y Procesamiento de Datos (Capa de Datos)
* **Extracción:** **AWS Glue** realiza el scraping de fuentes web y utiliza su *Feature Store* para normalizar los datos.
* **Carga Temporal:** Los datos crudos se cargan en **Amazon Redshift** para una estructuración rápida y limpieza masiva.
* **Optimización de Almacenamiento:** Los resultados se exportan a **Amazon S3** en formato **Parquet** (columnar y comprimido) para maximizar la velocidad de consulta y reducir costos de almacenamiento. Una vez exportados, los temporales en Redshift se eliminan para optimizar recursos.

### 2.3. Inteligencia y Explotación (Capa de Análisis)
* **Consultas Híbridas:** Mediante **Redshift Spectrum**, se consultan los históricos en S3 sin necesidad de mover los datos de nuevo.
* **Modelado Predictivo:** **Amazon SageMaker** utiliza los datos históricos para generar proyecciones y análisis avanzados mediante motores Spark.

## 3. Seguridad y Gobierno de Datos
La solución garantiza la máxima seguridad mediante:
1.  **AD Connector:** Integración directa con el **Active Directory (AD)** de Microsoft existente. No se requieren contraseñas nuevas; se utilizan las credenciales corporativas actuales.
2.  **AWS IAM:** Control de acceso basado en roles (RBAC), asegurando que solo el personal autorizado según su grupo en el AD pueda visualizar la información.
3.  **Visualización:** Los reportes finales se entregan vía **Amazon QuickSight**, con acceso restringido y autenticación única (SSO).

## 4. Beneficios de la Solución
* **Movilidad Total:** Control y recepción de resultados directamente en el celular.
* **Eficiencia de Costos:** Arquitectura mayoritariamente *Serverless* (se paga solo por lo procesado).
* **Escalabilidad:** Capacidad de manejar años de histórico sin degradación del rendimiento.
* **Decisión Basada en Datos:** Modelos de Machine Learning que aprenden del histórico para predecir fluctuaciones cambiarias.

---
*Este documento es de carácter confidencial y para uso exclusivo de la gerencia de Servicios Generales Electricidad y Pintura K & S S.A.C.S.*
