# Pregunta: ¿Cómo se hace el almacenamiento físico (en disco) de los registros procesados en cada SGBD?

## Almacenamiento Físico en SGBD Relacionales

El almacenamiento en disco es el proceso de persistir la información lógica en estructuras físicas optimizadas para el rendimiento y la seguridad.

## 1. Relación entre Lógica y Física
* **Modelo Relacional y Normalización:** Como bien señalas, la normalización es clave. Al eliminar la redundancia lógica, se optimiza el **espacio físico**, ya que el motor evita almacenar datos duplicados, reduciendo el número de escrituras en disco.
* **Índices:** Son estructuras adicionales (normalmente Árboles-B) que funcionan como un mapa. Guardan punteros hacia la ubicación física de los registros para evitar la lectura total de los archivos.

## 2. Unidades de Medida en Disco
Los SGBD no gestionan registros individuales en el disco, sino que los agrupan para minimizar los movimientos del cabezal de lectura:

* **Páginas (SQL Server) / Bloques (Oracle):** Es la unidad mínima de I/O. Generalmente son de **8 KB**. Aquí es donde residen físicamente tus filas de datos.
* **Extensiones:** Conjuntos de 8 páginas/bloques contiguos. Es la unidad que el SGBD reserva cuando una tabla necesita más espacio.



## 3. Tipos de Archivos Principales
Para garantizar la integridad (propiedades ACID), el almacenamiento se divide en:

| Tipo de Archivo | Función |
| :--- | :--- |
| **Archivos de Datos** (`.mdf`, `.dbf`) | Contienen las páginas con los registros de las tablas y los índices. |
| **Log de Transacciones** (`.ldf`, `Redo Log`) | Registro secuencial de todos los cambios. Se escribe en disco **antes** que el dato real (Write-Ahead Logging) para permitir la recuperación en caso de fallo. |

## 4. Implementaciones Específicas

### SQL Server
Organiza los datos en **Archivos de Datos** y **Archivos de Log**. Utiliza un mapa de asignación llamado **IAM (Index Allocation Map)** para saber qué páginas del archivo pertenecen a cada tabla.

### Oracle
Añade una capa de abstracción superior:
* **Tablespace:** Es un contenedor lógico (ej. "Ventas") que puede estar formado por varios archivos físicos (`.dbf`).
* **Jerarquía:** Tablespace → Segmento → Extensión → Bloque. Esto permite una administración muy flexible, permitiendo mover archivos de disco sin afectar la lógica del sistema.
