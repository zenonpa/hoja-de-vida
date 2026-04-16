# ¿Cómo se gestionan los usuarios en cada SGBD y qué tipo de operaciones se pueden hacer?
# Gestión de Usuarios y Seguridad en SGBD

La seguridad en los SGBD relacionales se basa en el control de acceso para garantizar que cada entidad tenga los permisos mínimos necesarios para su función.

## 1. Conceptos de Identidad
Cada motor gestiona la "identidad" del usuario con ligeras variaciones arquitectónicas:

* **SQL Server:** Separa la autenticación de la autorización.
    * **Login:** Identidad a nivel de instancia (servidor).
    * **User:** Identidad a nivel de base de datos específica, mapeada a un Login.
* **Oracle:** Maneja el concepto de **Esquema (Schema)**. Un usuario es, por definición, el dueño de su propio esquema de objetos (tablas, vistas, índices).

## 2. Tipos de Operaciones (Privilegios)
Las acciones que un usuario puede ejecutar se clasifican según el estándar SQL:

### A. Gestión de Estructura (DDL)
Permisos para definir o modificar el catálogo:
* `CREATE`, `ALTER`, `DROP`.
* *Uso:* Típico de administradores (DBA) o procesos de despliegue.

### B. Manipulación de Datos (DML)
Permisos sobre la información contenida en las tablas:
* `SELECT` (Lectura), `INSERT` (Escritura), `UPDATE` (Modificación), `DELETE` (Borrado).
* *Uso:* Usuarios de aplicaciones o analistas de datos.

### C. Control de Acceso (DCL)
Comandos para administrar los permisos mismos:
* **GRANT:** Otorga un privilegio específico.
* **REVOKE:** Retira un privilegio otorgado.
* **DENY (SQL Server):** Prohíbe explícitamente una acción (prevalece sobre cualquier GRANT).

## 3. Administración Basada en Roles (RBAC)
Para evitar la gestión individual, se utilizan **Roles** (contenedores de permisos):

1.  Se crea un **Rol** (ej. `Rol_Lectura_Finanzas`).
2.  Se asignan permisos al Rol (`GRANT SELECT ON Tablas_Ventas TO Rol_Lectura_Finanzas`).
3.  Se añaden **Usuarios** al Rol.

| SGBD | Característica de Roles |
| :--- | :--- |
| **SQL Server** | Incluye roles fijos como `sysadmin` o `db_owner`. |
| **Oracle** | Permite jerarquías de roles (un rol puede heredar de otro). |

## 4. Ejemplo de Implementación (SQL)
```sql
-- Crear identidad
CREATE LOGIN AnalistaLog WITH PASSWORD = 'ClaveSegura123!';
CREATE USER AnalistaUser FOR LOGIN AnalistaLog;

-- Gestionar permisos vía Roles
CREATE ROLE RolReportes;
GRANT SELECT ON dbo.Ventas TO RolReportes;

-- Asignar usuario al grupo
ALTER ROLE RolReportes ADD MEMBER AnalistaUser;
