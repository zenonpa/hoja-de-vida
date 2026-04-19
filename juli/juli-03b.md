# ¿Qué son los privilegios en una base de datos, qué tipos de privilegios existen y cómo se asignan en los objetos de una base de datos (tablas, paquetes, procedimientos, etc.)?

## Tratado sobre Arquitectura de Privilegios y Control de Acceso

## 1. Clasificación de Privilegios en SGBD
La seguridad estructural se divide en dos dominios de autorización para garantizar la segregación de funciones:

* **Privilegios de Sistema:** Facultades de alto nivel que permiten alterar la arquitectura del SGBD. Ejemplos críticos incluyen `CREATE SESSION`, `CREATE ANY TABLE` y `AUDIT SYSTEM`. Su uso indebido puede comprometer la integridad global del sistema.
* **Privilegios de Objeto:** Derechos granulares sobre entidades específicas (tablas, vistas, procedimientos). Incluyen acciones como `SELECT`, `INSERT`, `UPDATE` y el privilegio especializado `READ`, que permite consultas sin riesgo de bloqueos de recursos.

## 2. Modelos de Ejecución y Autorización (AUTHID)
La seguridad en la lógica programable se gestiona mediante dos esquemas de derechos:
1.  **Definer’s Rights (AUTHID DEFINER):** El procedimiento se ejecuta con los permisos del creador, permitiendo exponer datos de forma controlada a usuarios sin privilegios directos.
2.  **Invoker’s Rights (AUTHID CURRENT_USER):** El procedimiento actúa bajo las restricciones del usuario que lo ejecuta, ideal para operaciones que deben respetar la privacidad individual.

## 3. Administración de la Confianza: GRANT y REVOKE
La delegación de autoridad se rige por reglas de propagación:
* **WITH GRANT OPTION:** Aplicable a objetos; si se revoca el privilegio al otorgante, se genera una **revocación en cascada** para todos sus delegados.
* **WITH ADMIN OPTION:** Aplicable a privilegios de sistema; la revocación al otorgante **no afecta** a los usuarios a quienes este les haya cedido el permiso previamente.

---

## Resumen
* La seguridad se fundamenta en la tríada de **confidencialidad, integridad y disponibilidad**, utilizando privilegios como unidad mínima de control.
* Se recomienda el uso de **RBAC (Roles)** para evitar el "privilege creep" (acumulación innecesaria de permisos).
* La cláusula **AUTHID** es vital para determinar si un proceso corre con derechos del propietario o del ejecutor.
* La gobernanza moderna exige el cumplimiento del **Principio de Menor Privilegio (PoLP)** y auditorías constantes.

---

## Referencias Bibliográficas (APA 7ma Edición)

CyberArk. (2026, 19 de abril). *¿Qué es la gestión del acceso con privilegios (PAM)? - Definición*. [https://www.cyberark.com/es/what-is/privileged-access-management/](https://www.cyberark.com/es/what-is/privileged-access-management/)

DataSunrise. (2026, 19 de abril). *Deslizamiento de Privilegios | Centro del Conocimiento*. [https://www.datasunrise.com/es/centro-de-conocimiento/expansion-de-privilegios/](https://www.datasunrise.com/es/centro-de-conocimiento/expansion-de-privilegios/)

Oracle Help Center. (2026, 19 de abril). *Configuring Privilege and Role Authorization*. [https://docs.oracle.com/en/database/oracle/oracle-database/23/dbseg/configuring-privilege-and-role-authorization.html](https://docs.oracle.com/en/database/oracle/oracle-database/23/dbseg/configuring-privilege-and-role-authorization.html)

Oracle Help Center. (2026, 19 de abril). *Managing Security for Definer's Rights and Invoker's Rights*. [https://docs.oracle.com/en/database/oracle/oracle-database/19/dbseg/managing-security-for-definers-rights-and-invokers-rights.html](https://docs.oracle.com/en/database/oracle/oracle-database/19/dbseg/managing-security-for-definers-rights-and-invokers-rights.html)

Trend Micro. (2026, 19 de abril). *¿Cuál es el Principio de Privilegio Mínimo (POLP)?*. [https://www.trendmicro.com/es_es/what-is/what-is-zero-trust/principle-of-least-privilege.html](https://www.trendmicro.com/es_es/what-is/what-is-zero-trust/principle-of-least-privilege.html)
