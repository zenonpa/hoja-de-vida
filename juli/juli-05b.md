# ¿Si tuvieran que realizar una auditoría a una base de datos, qué criterios tendrían en cuenta para evaluar la información guardada, el diseño y las modificaciones?

## Auditoría Integral de Bases de Datos: Criterios Avanzados para la Evaluación de Información, Diseño y Modificaciones en Entornos DBMS y Oracle PL/SQL

## Introducción

En la era actual, las organizaciones se han vuelto concéntricas en los datos (data-centric), convirtiendo a la información en el activo más valioso para la toma de decisiones.[1] Ante esto, la auditoría de un Sistema de Gestión de Bases de Datos (SGBD) ha dejado de ser reactiva para convertirse en un proceso preventivo que salvaguarda la confidencialidad, integridad y disponibilidad, alineándose con los marcos de Gobierno, Riesgo y Cumplimiento (GRC).[2] 

Para auditar infraestructuras relacionales complejas como Oracle Database y PL/SQL, los criterios de evaluación deben dividirse en tres pilares: la información guardada (calidad), el diseño lógico y arquitectónico, y las modificaciones o transacciones (trazabilidad y pistas de auditoría).

## Pilar I: Evaluación de la Información Guardada (Calidad de Datos)

De nada sirve contar con infraestructura segura si los datos carecen de veracidad. Los algoritmos y decisiones de negocio nunca superarán el nivel de calidad de los datos subyacentes.[1]

### Calidad de Datos según ISO/IEC 25012
El auditor debe basarse en marcos robustos como la norma ISO/IEC 25012, evaluando sistemáticamente mediante scripts las siguientes dimensiones [3, 4]:
*   **Exactitud y Consistencia:** Ausencia de valores atípicos y contradicciones lógicas intra o inter-tablas.
*   **Completitud:** Evaluar la proporción de datos esperados frente a nulos mediante fórmulas matemáticas relacionales, como el índice $X = A / B$, donde $A$ representa el conteo de valores no nulos y $B$ el total de tuplas.[4]
*   **Actualidad y Trazabilidad:** Verificación de latencias transaccionales y capacidad de rastrear el origen de los cambios.

### Restricciones de Integridad del Modelo
La integridad es la validez y consistencia irrefutable de los datos, implementada mediante reglas que el SGBD no puede violar (Connolly & Begg, 2015).[5] El auditor debe revisar el diccionario de datos para garantizar:
*   **Integridad de Entidad y Dominio:** Uso obligatorio de claves primarias indexadas sin valores nulos y restricciones `CHECK` para validar lógicamente el ingreso de datos.[6, 7]
*   **Integridad Referencial:** Correcto uso de claves foráneas para evitar registros "huérfanos". Cualquier desviación en la que las restricciones estén deshabilitadas (`NOVALIDATE`) representa un riesgo crítico de auditoría.[6]

## Pilar II: Diseño Lógico y Arquitectura de Seguridad

Evaluar el SGBD implica analizarlo como un organismo lógico estructurado, no como un mero repositorio físico.[8]

### Normalización Relacional
Como establece C.J. Date, el diseño relacional es una disciplina formal que requiere "control intelectual" para mantener los modelos simples y desenredados.[9] El auditor debe exigir estricta adherencia a la Tercera Forma Normal (3NF) en bases de datos transaccionales, documentando y justificando cualquier desnormalización orientada al rendimiento (como en entornos OLAP).[10, 11]

### Arquitectura de Seguridad y Reducción de la Superficie de Ataque
La protección de los datos debe ser intrínseca al diseño (Silberschatz et al., 2019).[11] El auditor debe examinar:
1.  **Catálogo y Privilegios:** Restringir el acceso a los metadatos (como el esquema `SYS` en Oracle). Implementar estrictamente Control de Acceso Basado en Roles (RBAC) y el principio de mínimo privilegio.[11, 12]
2.  **Configuraciones por Defecto y Rol PUBLIC:** Las vulnerabilidades a menudo nacen de instalaciones predeterminadas.[13] Es imperativo revocar permisos del rol `PUBLIC` sobre utilidades críticas como `UTL_FILE` o `DBMS_SQL` y eliminar usuarios de fábrica para prevenir escaladas de privilegios.[12, 14]
3.  **Cifrado Transversal:** Exigir el encriptado de sesiones en red y protección de datos en reposo utilizando metodologías como Transparent Data Encryption (TDE) de Oracle.[14, 15]

### Segregación de Entornos
Las normativas estipulan la segregación estricta de funciones y de entornos (Desarrollo, Pruebas y Producción).[16] El acceso de los desarrolladores a Producción debe estar bloqueado, y cualquier información real transportada a Desarrollo debe someterse a procesos de enmascaramiento irreversible.[16]

## Pilar III: Modificaciones, Trazabilidad y Trazas Forenses

Para cumplir con regulaciones internacionales, la organización debe poseer trazabilidad forense irrefutable sobre la mutabilidad de la información: quién alteró un registro, cuándo, desde dónde y cuáles fueron los valores alterados.[2]

### Auditoría Unificada y Auditoría de Grano Fino (FGA)
Las mejores prácticas actuales dictan abandonar parámetros antiguos de auditoría en favor de la "Auditoría Unificada" de Oracle, que consolida y protege eventos forenses en tablespaces dedicados.[17] Si se emplean arquitecturas clásicas, debe exigirse la captura de sentencias completas y variables bindeables (`SQL_TEXT` y `SQL_BIND`).[18]
Adicionalmente, el uso de Auditoría de Grano Fino (FGA) mediante el paquete `DBMS_FGA` permite auditar consultas sensibles a nivel de columna (ej. número de seguro social) bajo condiciones dinámicas, integrándose con Seguridad a Nivel de Fila (VPD) para un control microscópico.[15, 19]

### El Riesgo de los Triggers PL/SQL
Aunque son populares para crear pistas de auditoría sincrónicas [2], los disparadores (triggers) DML conllevan peligros severos identificados por autores como Steven Feuerstein.[20, 21] El auditor debe detectar riesgos como:
*   **Penalización de Rendimiento:** El cambio de contexto (context switching) entre los motores SQL y PL/SQL al auditar grandes lotes de datos.[21] Debe recomendarse la refactorización empleando vinculación masiva (Bulk Binding).
*   **Seguridad Transaccional:** Uso de bloques `EXCEPTION` y directivas como `PRAGMA AUTONOMOUS_TRANSACTION` para evitar que un fallo en la auditoría genere un rollback en la transacción original de negocio.[22]

### El Problema de la Identidad en Aplicaciones Multicapa
En aplicaciones modernas de tres capas, el servidor de aplicaciones a menudo se conecta a la base de datos usando un solo usuario genérico de pool de conexiones (`APP_USER`).[19] Esto destruye la trazabilidad forense, ya que la base de datos no reconoce al humano real operando el sistema. Como solución, los consultores de seguridad de bases de datos prescriben el uso del paquete `DBMS_SESSION.SET_IDENTIFIER` en la aplicación intermedia. Esto inyecta el identificador del usuario real en la memoria de sesión temporal, permitiendo capturar el autor verdadero mediante `SYS_CONTEXT('USERENV', 'CLIENT_IDENTIFIER')` en cualquier política de auditoría o disparador posterior.[19]

## Síntesis y Elaboración del Informe

Una auditoría rigurosa no es una simple aplicación mecánica de listas de verificación; es un análisis estructurado frente a amenazas internas, particularmente de súper-usuarios altamente privilegiados.[2] El diagnóstico se materializa en un informe final, el cual es crítico para el éxito del proceso.[23] Dicho documento debe contemplar [24, 25]:
*   **Resumen Ejecutivo y Alcance:** Panorama de exposición al riesgo para la Alta Dirección y delimitación exacta de servidores y esquemas analizados.
*   **Matriz de Hallazgos:** Vulnerabilidades categorizadas técnicamente, respaldadas por evidencia extraída de los logs.
*   **Plan de Mitigación y Ejecución Post-Auditoría:** Transitar hacia un enfoque sistemático de mejora continua en la organización [26], aplicando "Data Quality by Design".[3] Involucra la refactorización de código PL/SQL inseguro, la gestión del ciclo de vida de auditoría (con paquetes como `DBMS_AUDIT_MGMT` [17]), y la implementación de herramientas de Monitoreo de Actividad de Base de Datos (DAM) en tiempo real para evitar escaladas de privilegios y garantizar el cumplimiento normativo integral.[14]

***

## Referencias

*   Connolly, T. M., & Begg, C. E. (2015). *Database Systems: A Practical Approach to Design, Implementation, and Management* (6th ed.). Pearson Education.
*   Date, C. J. (2012). *Database Design and Relational Theory: Normal Forms and All That Jazz*. O'Reilly Media.
*   Feuerstein, S. (2010). *A fresh look at auditing row changes: Refactoring audit trigger code*. Oracle Connect.
*   Finnigan, P. (2012). *Identity in the Oracle Database*. PeteFinnigan.com Limited.
*   Guerra-García, C., Jiménez, S., & Perez-Gonzalez, H. (2023). ISO/IEC 25012-based methodology for managing data quality requirements in the development of information systems: Towards Data Quality by Design. *Data & Knowledge Engineering*, 145, 102152.
*   Piattini Velthuis, M. G. (2020). *Gestión en las organizaciones basada en datos de calidad*. AENOR: Revista de la normalización y la certificación, 358, 11-20.
*   Silberschatz, A., Korth, H. F., & Sudarshan, S. (2019). *Database System Concepts* (7th ed.). McGraw-Hill Education.
*   Tapia, J. (2022). *Mejores Prácticas de Auditoría de Bases de Datos*. Universidad del Caribe.
*   The Institute of Internal Auditors (IIA). (2020). *GTAG: IT Change Management: Critical for Organizational Success* (3rd ed.). The IIA.
*   University of Wollongong Library. (2000). *Managing change as a system-wide approach and continuous review*. IATUL Proceedings.
