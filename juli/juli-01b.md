# ¿Cómo se hace el almacenamiento físico (en disco) de los registros procesados en cada SGBD?

## Resumen: Almacenamiento Físico en SGBD

El almacenamiento físico en los Sistemas Gestores de Bases de Datos (SGBD) separa la abstracción lógica (tablas y filas) de la implementación física en disco, utilizando una arquitectura jerárquica estandarizada basada en **archivos, extensiones y páginas o bloques lógicos**.[1, 2] Para almacenar registros de tamaño variable de manera eficiente y permitir modificaciones internas sin romper los punteros externos de los índices, la industria emplea mayoritariamente la **arquitectura de páginas ranuradas** (slotted pages).[3] En esta estructura, los metadatos y punteros crecen desde el inicio de la página hacia el centro, mientras que los datos reales de las tuplas se escriben desde el final físico de la página hacia arriba.[4, 5]

A continuación, se sintetizan los enfoques específicos de los cuatro principales motores de bases de datos:

**1. Oracle Database**
Organiza los datos lógicamente en contenedores llamados *Tablespaces*, los cuales están respaldados por archivos de datos físicos divididos en bloques (generalmente de 8 KB).[6, 7] El espacio se asigna en *extensiones* que forman los *segmentos* (tablas o índices).[6] Su direccionamiento físico es de los más robustos mediante el uso del seudocampo `ROWID`, el cual proporciona una ubicación absoluta en disco codificada en base 64 que incluye el objeto de datos, el archivo relativo, el bloque exacto y la ranura.[8]

**2. PostgreSQL**
Almacena la información en archivos "montículo" (*heap files*) compuestos por páginas estáticas de 8 KB.[4] Su arquitectura difiere drásticamente debido a su modelo de Control de Concurrencia Multiversión (MVCC) de tipo *append-only*.[9] Las actualizaciones no sobrescriben los datos originales, sino que insertan una nueva tupla y marcan la anterior como obsoleta mediante campos internos (`xmin` y `xmax`).[9] Esto evita bloqueos pero requiere el proceso constante de `VACUUM` para reclamar el espacio de las tuplas muertas.[9] Utiliza el identificador relativo `ctid` (número de página y tupla) para ubicar registros.[10]

**3. Microsoft SQL Server**
Estructura sus datos en páginas de 8 KB que se agrupan rígidamente en extensiones de 64 KB (ocho páginas).[11] Destaca por un control granular del espacio libre a través de un complejo ecosistema de páginas de asignación. Utiliza mapas de bits en páginas GAM (Global Allocation Map) y SGAM para rastrear extensiones libres, y páginas PFS (Page Free Space) con un sistema de "mapa de bytes" que identifica porcentajes exactos de llenado por bloque.[11, 12] Las filas se ubican mediante un identificador de registro físico `RID`.[8]

**4. MySQL (Motor InnoDB)**
Agrupa sus páginas (que por defecto son de 16 KB) en extensiones más grandes de 1 MB, almacenadas dentro de *Tablespaces* generales (como `ibdata1`) o archivos individuales por tabla (`.ibd`).[13, 14] Su diferencia fundamental es el uso de tablas organizadas por índices (Index-Organized Tables). La tabla principal es en sí misma un árbol B+, donde las hojas inferiores almacenan la tupla completa y no solo punteros.[15] Para evitar la corrupción de disco por páginas rotas o escrituras parciales, InnoDB implementa un búfer de seguridad llamado *Doublewrite Buffer* antes de guardar los datos en su ubicación final.[13, 16]

---

### Referencias

Delaney, K. (2017). *SQL Server Internals: In-Memory OLTP Inside the SQL Server 2016 Hekaton Engine* (2nd ed.). Simple Talk Publishing. 

Elmasri, R., & Navathe, S. B. (2015). *Fundamentals of Database Systems* (7th ed.). Pearson. 

Hellerstein, J. M., Stonebraker, M., & Hamilton, J. (2007). Architecture of a Database System. *Foundations and Trends in Databases*, 1(2), 141-259. 

Kyte, T. (2010). *Expert Oracle Database Architecture: Oracle Database 9i, 10g, and 11g Programming Techniques and Solutions* (2nd ed.). Apress. 

Momjian, B. (2001). *PostgreSQL: Introduction and Concepts*. Addison-Wesley.
