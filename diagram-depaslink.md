# 🏢 Diagrama de Flujo — Sistema depas.link

## Arquitectura General

```mermaid
flowchart TD
    subgraph ACCESO["🔐 Control de Acceso"]
        A([Usuario Visita el Sitio]) --> B{¿Sesión Activa?}
        B -- No --> C[Página de Login\n/login]
        C --> D[Firebase Auth\nEmail + Password]
        D -- Error --> C
        D -- Éxito --> E{Identificar Perfil}
        B -- Sí --> E
    end

    subgraph ROLES["👥 Sistema de Roles"]
        E --> F{¿Email en ADMIN_EMAILS?}
        F -- Sí --> G["🔶 ADMINISTRADOR GLOBAL\nzenonpa@gmail.com\narnaldoparedes@gmail.com"]
        F -- No --> H[Consultar 'arrendadores'\nen Firestore]
        H --> I{¿Tiene tipoPerfil?}
        I -- REGISTRADOR_LECTURAS --> J["🟣 REGISTRADOR DE LECTURAS\nSolo puede subir lecturas\ny evidencias fotográficas"]
        I -- ARRENDADOR o sin perfil --> K["🔵 ARRENDADOR / PROPIETARIO\nGestiona sus inmuebles\nasignados"]
        H --> L[Consultar 'user_profiles'\nen Firestore]
        L --> M{¿Tiene role?}
        M -- role encontrado --> N[Actualizar userRole\nen tiempo real con onSnapshot]
    end
```

## Dashboard — Navegación por Perfil

```mermaid
flowchart TD
    subgraph NAV["🗂️ Navegación por Perfil"]
        direction TB
        P1["ADMINISTRADOR GLOBAL"] --> NA["✅ Todas las pestañas\n+ Perfil & Roles"]
        P2["ARRENDADOR"] --> NB["✅ Inicio\n✅ Inmuebles\n✅ Apartamentos\n✅ Arrendadores\n✅ Arrendatarios\n✅ Listado Agrupado\n✅ Subir Lecturas\n✅ Recibo & Servicios\n✅ Mi Cuenta"]
        P3["REGISTRADOR DE LECTURAS"] --> NC["✅ Inicio\n✅ Subir Lecturas\n✅ Mi Cuenta\n🚫 Todo lo demás bloqueado"]
    end
```

## Módulos del Sistema

```mermaid
flowchart LR
    subgraph MODULOS["📦 Módulos del Sistema"]
        direction TB

        M1["🏠 Tab Inicio\nAccesos rápidos\nresumidos por perfil"]

        M2["🏢 Tab Inmuebles\nCRUD de propiedades\nRegistro y administración\n[Solo Admin/Arrendador]"]

        M3["🏠 Tab Apartamentos\nCRUD de unidades\nContrato, inquilino,\ngarantía, precio\n[Solo Admin/Arrendador]"]

        M4["👤 Tab Arrendadores\nRegistro de usuarios:\n• Arrendador\n• Registrador de Lecturas\nAsignación de inmuebles\n[Solo Admin]"]

        M5["👥 Tab Arrendatarios\nRegistro de inquilinos\nDocumentos, residentes,\nfechas de contrato\nEditar popup drag+resize\n[Solo Admin/Arrendador]"]

        M6["📊 Tab Listado Agrupado\nVista consolidada por\ninmueble y arrendatario\n[Solo Admin/Arrendador]"]

        M7["📸 Tab Subir Lecturas\nCarga de imágenes a AWS S3\nRegistro en Firestore\nFiltrado por inmueble\nasignado al usuario\n[Todos los perfiles]"]

        M8["🧾 Tab Recibo & Servicios\nGeneración de recibos\nservicios de luz/agua/internet\n[Solo Admin/Arrendador]"]

        M9["👤 Tab Mi Cuenta\nEditar nombre,\nteléfono, cargo\n[Todos los perfiles]"]

        M10["🔒 Tab Perfil & Roles\nConfigurar rol y\npermisos por usuario\nProtección superadmin\n[Solo Administradores]"]
    end
```

## Flujo de Subida de Lecturas (TabCargaInfo)

```mermaid
flowchart TD
    subgraph CARGA["📸 Subir Lecturas — Flujo Completo"]
        C1([Usuario abre\nSubir Lecturas]) --> C2{¿Es Admin?}
        C2 -- Sí --> C3[Muestra todos los\ninmuebles y filtros\npor arrendador]
        C2 -- No --> C4[Consultar inmuebles\nasignados en 'arrendadores'\ny 'user_profiles']
        C4 --> C5[Mostrar solo\ninmuebles asignados]
        C3 --> C6[Seleccionar:\nInmueble → Departamento\nPeríodo → Tipo servicio]
        C5 --> C6
        C6 --> C7[Cargar imagen\ndel medidor]
        C7 --> C8[Comprimir imagen\nhasta 1920px / JPEG 85%]
        C8 --> C9[POST /api/upload\nAWS S3]
        C9 -- Error --> C10[Mostrar error]
        C9 -- OK --> C11[Guardar en Firestore\n'evidencias_medidores']
        C11 --> C12[Renovar presigned URL\n/api/getDownloadUrl]
        C12 --> C13[Mostrar historial\ncon foto renovada]
    end
```

## Flujo de Gestión de Arrendadores y Roles

```mermaid
flowchart TD
    subgraph GESTION["⚙️ Gestión de Usuarios y Roles"]
        R1([Admin abre\nArrendadores]) --> R2{¿Nuevo o Editar?}
        R2 -- Nuevo --> R3["Seleccionar Tipo de Perfil:\n• Arrendador\n• Registrador de Lecturas"]
        R3 --> R4[Ingresar nombre,\ncorreo, teléfono]
        R4 --> R5[Asignar 1 o más\ninmuebles]
        R5 --> R6[Guardar en 'arrendadores'\nFirestore]
        R6 --> R7[Si no existe en\n'user_profiles', crear\ncon permisos automáticos]
        R7 --> R8[Enviar correo de\nbienvenida /api/notify-arrendador]

        R2 -- Editar --> R9[Modificar datos\no cambiar tipoPerfil]
        R9 --> R10[Actualizar 'arrendadores'\ny sincronizar 'user_profiles']

        S1([Admin abre\nPerfil & Roles]) --> S2[Seleccionar usuario\nde la lista]
        S2 --> S3{¿Es zenonpa@gmail.com?}
        S3 -- Sí --> S4[🔒 Superadmin Protegido\nInmutable]
        S3 -- No --> S5["Elegir Rol:\n• ADMINISTRADOR\n• ARRENDADOR\n• REGISTRADOR_LECTURAS\n• ARRENDATARIO"]
        S5 --> S6[Configurar matriz\nde permisos:\n• crear arrendadores\n• asignar inmuebles\n• gestión arrendatarios\n• subir S3\n• emitir recibos]
        S6 --> S7[Guardar en\n'user_profiles' Firestore]
    end
```

## Infraestructura y API Routes

```mermaid
flowchart LR
    subgraph INFRA["🌐 Infraestructura Técnica"]
        direction TB

        FE["Next.js 14\nApp Router\n/app"] --> API["API Routes\n/api"]

        API --> UP["/api/upload\nMultipart → AWS S3\nGenera URL pública"]
        API --> DL["/api/getDownloadUrl\nPresigned URL\nAWS S3 temporal"]
        API --> SD["/api/save-data\nGuardar datos\nen Firestore"]
        API --> NF["/api/notify-arrendador\nNodemailer\nCorreo de bienvenida"]

        FE --> FB["Firebase Auth\nAutenticación\nGoogle"]
        FE --> FS["Cloud Firestore\nBase de datos\nen tiempo real"]

        subgraph COLECCIONES["Colecciones Firestore"]
            FS --> col1[inmuebles]
            FS --> col2[apartamentos]
            FS --> col3[arrendadores]
            FS --> col4[arrendatarios]
            FS --> col5[evidencias_medidores]
            FS --> col6[user_profiles]
        end

        subgraph RUTAS["Páginas / Rutas"]
            FE --> pg1["/\nLanding Page"]
            FE --> pg2["/login\nAutenticación"]
            FE --> pg3["/dashboard\nPanel PC Completo"]
            FE --> pg4["/movil\nApp Móvil Simplificada"]
            FE --> pg5["/descarga\nDescarga de Archivos S3"]
            FE --> pg6["/contacto"]
        end
    end
```

## Vista Móvil (/movil)

```mermaid
flowchart TD
    subgraph MOVIL["📱 Módulo Móvil — /movil"]
        MV1([Usuario accede\na /movil]) --> MV2[Firebase Auth\nVerificar sesión]
        MV2 -- No autenticado --> MV3[Redirigir /login]
        MV2 -- Autenticado --> MV4[Cargar perfil del usuario\nde 'arrendadores'\ny 'user_profiles']
        MV4 --> MV5{¿Es Admin?}
        MV5 -- Sí --> MV6[Mostrar todos\nlos inmuebles]
        MV5 -- No --> MV7[Filtrar solo\ninmuebles asignados]
        MV6 --> MV8[Seleccionar:\nInmueble → Período]
        MV7 --> MV8
        MV8 --> MV9[Seleccionar departamento\nfiltrado por inmueble]
        MV9 --> MV10[Capturar foto\ncámara trasera]
        MV10 --> MV11[Ingresar lectura\nkWh y descripción]
        MV11 --> MV12[Comprimir + subir\na S3 + guardar Firestore]
        MV12 --> MV13[Ver historial\nde lecturas recientes]
    end
