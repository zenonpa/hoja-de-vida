# ¿Qué son los privilegios en una base de datos, qué tipos de privilegios existen y cómo se asignan en los objetos de una base de datos (tablas, paquetes, procedimientos, etc.)?

# Privilegios en Base de Datos: Tipos y Asignación

Los **Privilegios** son permisos específicos que permiten a un usuario o rol realizar acciones sobre la base de datos. Se rigen por el principio de seguridad de "mínimo privilegio", otorgando solo lo necesario para la operación.

## 1. Clasificación de Privilegios

Existen dos categorías principales dependiendo del alcance del permiso:

### A. Privilegios de Sistema
Permiten realizar acciones que afectan a la **instancia o al esquema completo**, no a un objeto individual.
* **Ejemplos:** `CREATE SESSION` (conectar), `CREATE TABLE` (crear tablas en su esquema), `BACKUP DATABASE`.
* **Uso:** Generalmente asignados a DBAs o desarrolladores en entornos de pruebas.

### B. Privilegios de Objeto
Permiten realizar acciones sobre **objetos específicos** (tablas, vistas, procedimientos, etc.).
* **Sobre Tablas:** `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `REFERENCES`.
* **Sobre Lógica Programable:** `EXECUTE` (para Procedimientos, Funciones y Paquetes).



---

## 2. Asignación de Privilegios por Objeto

La asignación se realiza mediante el comando `GRANT` y se retira con `REVOKE`.

### En Tablas y Vistas
Se asignan para controlar el flujo de datos (DML).
```sql
-- Ejemplo: Permitir que un usuario lea y añada registros
GRANT SELECT, INSERT ON dbo.Facturas TO Rol_Contabilidad;
