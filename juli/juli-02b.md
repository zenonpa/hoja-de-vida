 # ¿Cómo se gestionan los usuarios en cada SGBD y qué tipo de operaciones se pueden hacer?
 
## Análisis de la Gestión de Usuarios y Seguridad en SGBD

La seguridad en los Sistemas de Gestión de Bases de Datos (SGBD) contemporáneos constituye el núcleo de la infraestructura de datos de misión crítica. Esta gestión se rige por procesos de **identificación, autenticación criptográfica y autorización granular**, desplegados bajo modelos de Control de Acceso Basado en Roles (**RBAC**), Discrecional (**DAC**) y Obligatorio (**MAC**).

A continuación, se comparan las arquitecturas de las plataformas líderes:

* **Oracle Database:** Implementa un vínculo indivisible uno a uno entre el **usuario** (entidad de autenticación) y el **esquema** (contenedor de objetos). Utiliza cuentas críticas como **SYS** (núcleo del sistema con privilegios `SYSDBA`) y **SYSTEM** (administración rutinaria).
* **PostgreSQL:** Adopta un enfoque flexible donde los conceptos de usuario, grupo y esquema están desacoplados. Utiliza el constructo global de **Rol** para unificar identidades y grupos, reforzando la seguridad con el aislamiento de procesos a nivel de kernel.
* **Microsoft SQL Server:** Separa la autenticación de la autorización mediante una jerarquía de *principals*: los **Logins** (nivel servidor) gestionan la conexión, mientras que los **Users** (nivel base de datos) gestionan el acceso a los datos.
* **MySQL:** Se basa en una **matriz de identidad topográfica** (`usuario@host`), vinculando las credenciales al origen de la red. Su gestión de privilegios se divide en ámbitos globales, de base de datos y de objeto.



## Resumen

* La gestión de usuarios se fundamenta en la triada de **identificación, autenticación y autorización granular** para proteger datos sensibles.
* Mientras **Oracle** vincula usuario y esquema, **PostgreSQL** y **SQL Server** utilizan abstracciones de roles y separan el acceso al servidor del acceso a los datos.
* Las tendencias para 2025 apuntan hacia filosofías de **Zero Trust** (Confianza Cero) e integración de **Inteligencia Artificial** para la detección de anomalías en tiempo real.

---

## Referencias Bibliográficas

Connolly, T. M., & Begg, C. E. (2015). *Database Systems: A Practical Approach to Design, Implementation, and Management* (6th ed.). Pearson Education.

Elmasri, R., & Navathe, S. B. (2015). *Fundamentals of Database Systems* (7th ed.). Pearson.

Garcia-Molina, H., Ullman, J. D., & Widom, J. (2008). *Database Systems: The Complete Book* (2nd ed.). Prentice Hall.

Gartner. (2024). *Las principales tendencias en ciberseguridad para 2025*. https://www.gartner.es/es/ciberseguridad/temas/tendencias-ciberseguridad

Silberschatz, A., Korth, H. F., & Sudarshan, S. (2011). *Database System Concepts* (6th ed.). McGraw-Hill.
