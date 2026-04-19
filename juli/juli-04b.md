# ¿Qué son los roles en una base de datos y cómo de administran?

## Análisis de la Gestión de Roles en Sistemas de Bases de Datos

## 1. Fundamentos Teóricos: El Modelo RBAC
El **Control de Acceso Basado en Roles (RBAC)** es una metodología que restringe el acceso a recursos según las funciones laborales de los usuarios en una organización. A diferencia de otros modelos, el RBAC es neutral respecto a la política y se organiza en torno a cargos con autoridad definida.

La jerarquía **RBAC96**, propuesta por Sandhu et al., establece cuatro niveles de complejidad:
* **RBAC0 (Base):** Define los elementos mínimos como usuarios, roles, permisos y sesiones.
* **RBAC1 (Jerarquías):** Introduce la herencia de roles, donde un rol superior hereda privilegios de uno inferior.
* **RBAC2 (Restricciones):** Añade reglas de integridad como la segregación de funciones (*Separation of Duties*).
* **RBAC3 (Consolidado):** Integra tanto jerarquías como restricciones en una implementación completa.

## 2. Implementación en Oracle Database
En Oracle, un rol es un objeto independiente en el diccionario de datos que actúa como contenedor de privilegios. Su administración se realiza mediante el lenguaje de control de datos (DCL) y permite diversos métodos de identificación, desde contraseñas hasta validaciones externas mediante servicios de directorio como LDAP.

### Comandos Principales
* `CREATE ROLE`: Crea el objeto rol en el sistema.
* `GRANT`: Asocia permisos al rol o asigna el rol a un usuario.
* `SET ROLE`: Habilita el rol en la sesión actual.
* `DROP ROLE`: Elimina permanentemente el rol.

## 3. Roles en PL/SQL y Seguridad Contextual
La interacción entre roles y código procedural (PL/SQL) depende del modelo de derechos:
* **Derechos del Definidor (AUTHID DEFINER):** Los privilegios de roles se deshabilitan durante la ejecución para evitar escaladas de privilegios accidentales.
* **Derechos del Invocador (AUTHID CURRENT_USER):** La unidad programática respeta los roles habilitados de quien la invoca.
* **Roles de Aplicación Segura:** Requieren que un paquete PL/SQL valide la lógica de negocio (como IP o horario) antes de activar el rol.

## 4. Gobernanza y Desafíos Avanzados
Las organizaciones enfrentan la **"Explosión de Roles"**, donde la creación desmedida de objetos genera vulnerabilidades. Para mitigar esto, se emplea la **"Minería de Roles"**, utilizando ciencia de datos para optimizar la estructura de permisos. Asimismo, el futuro apunta hacia el **Control de Acceso Basado en Atributos (ABAC)**, un modelo dinámico que evalúa el contexto en tiempo real.

---

# Resumen
* **Administración Simplificada:** Los roles permiten gestionar privilegios de forma colectiva, facilitando la escalabilidad y reduciendo errores humanos.
* **Modelos de Seguridad:** La implementación de RBAC (niveles 0 a 3) asegura que la jerarquía organizacional se refleje correctamente en la base de datos.
* **Control en Oracle:** El uso de roles en PL/SQL bajo esquemas de "Invocador" y "Definidor" es crítico para la integridad del sistema.
* **Evolución Tecnológica:** La transición hacia ABAC y el uso de Minería de Roles son las tendencias actuales para resolver la complejidad en la gobernanza de datos.

---

# Referencias Bibliográficas (Normativa APA 7ma Edición)

Date, C. J. (2004). *An introduction to database systems* (8th ed.). Pearson Addison-Wesley.

Elmasri, R., & Navathe, S. B. (2016). *Fundamentals of database systems* (7th ed.). Pearson.

Sandhu, R. S., Coyne, E. J., Feinstein, H. L., & Youman, C. E. (1996). Role-based access control models. *IEEE Computer*, 29(2), 38-47. [https://doi.org/10.1109/2.485845](https://doi.org/10.1109/2.485845)

Silberschatz, A., Korth, H. F., & Sudarshan, S. (2020). *Database system concepts* (7th ed.). McGraw-Hill Education.
