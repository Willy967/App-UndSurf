# App-UndSurf
App para gestión de pozos en mina subterránea y superficie. Util para transmitir los datos en forma ágil del Geologo que planifica el plan de perforación a los perforistas y luego compartir estos datos a otros profesionales como el ingeniero geotécnico o el ingeniero en minas para sus visitas a terreno.

📡 Undsurf - Underground & Surface Drilling Management
🌍 Español
Descripción General
Undsurf es una aplicación móvil y web diseñada para la gestión integral de pozos de perforación en minas subterráneas y a cielo abierto (superficie).

Su objetivo principal es agilizar la transmisión de datos entre los diferentes actores del área de mina. El flujo de trabajo comienza con el Geólogo de turno, quien planifica el plan de perforación y carga los datos iniciales. Inmediatamente, los Perforistas reciben esta información en terreno para ejecutar la perforación, y pueden reportar el avance en tiempo real. Finalmente, el Ingeniero Geotécnico y el Ingeniero de Minas utilizan estos datos actualizados para sus análisis, visitas a terreno y toma de decisiones, eliminando los errores de la comunicación por papel o mensajería informal.

👥 Roles y Permisos de Usuario
Rol	Permisos y Funcionalidades
👨‍🔬 Geólogo (Administrador)	Usuario principal. Carga los planos en PDF/JPG/PNG, sube las tablas CSV iniciales. Tiene control total sobre la edición de HOLE_ID (incluso después de finalizado el pozo). Registra modificaciones y visualiza el historial completo (quién, cuándo y qué cambió).
⛏️ Perforista	Actualiza el avance de la perforación. Solo puede modificar la columna HOLE_ID (máximo 4 veces), pasadas esas ediciones debe solicitar permiso al Geólogo. Puede agregar observaciones (profundidades de veta, problemas como pérdida de retorno de agua por fallas).
📐 Ingeniero Geotécnico	Acceso de solo lectura a los datos de perforación. Puede subir sus propias planillas CSV de geotecnia y agregar observaciones geotécnicas con colores (fecha y hora) debajo de la pestaña de locación, sin alterar los datos críticos de perforación.
🚀 Características Principales
🗂️ Gestor de HOLE_ID: Nombres únicos con formato estricto (Mayúsculas + Guión + Números). El sistema valida automáticamente si un HOLE_ID ya existe en la base de datos, mostrando una alerta para evitar duplicados.

🔒 Bloqueo de Pozos: Una vez que un pozo finaliza su perforación, el HOLE_ID y sus datos se bloquean (no se pueden editar). Solo el Geólogo Administrador tiene la potestad de desbloquearlo para cambios excepcionales.

📊 Visualización de Planos y Riesgos (Semáforo): Los usuarios pueden subir planos (Planta y Sección) en formatos PDF, JPG, PNG, MP4, etc. Estos planos se superponen con un sistema semafórico (Rojo, Amarillo, Verde) para señalar riesgos geológicos o de infraestructura subterránea.

📂 Carga de Datos Técnicos: Soporte para subir archivos CSV de:

Datos de perforación (obligatorio).

Datos de Geotecnia (opcional).

Datos de Survey / Desviación del pozo (opcional).

Datos Litológicos (opcional).

🔎 Búsqueda y Filtros Avanzados: Buscador por HOLE_ID, Locación, Mina, Tipo (Underground/Surface), Perforista, Geólogo, Ingeniero, Fecha y Riesgo (color semáforo).

📱 Inicio Inteligente: La pantalla principal siempre muestra primero los pozos en proceso de perforación o avance, junto a sus riesgos asociados (sistema semafórico), para una rápida toma de decisiones.

🔐 Seguridad y Auditoría:

Login con Usuario/Contraseña (primera vez).

Datos Biométricos (Huella dactilar) para inicios de sesión posteriores.

Recuperación de contraseña vía correo electrónico.

Registro completo de trazabilidad: cada modificación (Geólogo o Perforista) queda registrada con hora y fecha exacta.

🛠️ Tech Stack (Sugerido / Estructura de Datos)
Base de Datos: Relacional (ej. PostgreSQL) con tablas para Pozos, Usuarios, Archivos CSV, Observaciones e Historial de Cambios.

Backend: API RESTful para manejar la lógica de permisos y la validación de unicidad del HOLE_ID.

Frontend: Aplicación móvil/híbrida con soporte para cámara (escaneo de planos) y lectores biométricos.

DIAGRAMA ENTIDAD-RELACIÓN COMPLETO
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              USUARIOS Y PERMISOS                                │
└─────────────────────────────────────────────────────────────────────────────────┘

┌──────────────────┐       ┌──────────────────┐       ┌──────────────────┐
│     usuarios     │       │  roles_usuario   │       │  permisos_rol    │
├──────────────────┤       ├──────────────────┤       ├──────────────────┤
│ id_usuario (PK)  │◄──────│ id_rol (PK)      │◄──────│ id_permiso (PK)  │
│ username         │       │ nombre_rol       │       │ id_rol (FK)      │
│ password_hash    │       │ descripcion      │       │ accion           │
│ email            │       └──────────────────┘       │ tabla_afectada   │
│ huella_hash      │                                  └──────────────────┘
│ rol (FK)         │
│ activo           │
│ fecha_registro   │
│ ultimo_login     │
└────────┬─────────┘
         │
         │ (1 a muchos)
         │
         ▼
┌──────────────────┐
│   login_log      │
├──────────────────┤
│ id_login (PK)    │
│ id_usuario (FK)  │
│ fecha_hora       │
│ ip_address       │
│ tipo_auth        │  -- 'password' o 'biometrico'
│ exitoso          │
└──────────────────┘


┌─────────────────────────────────────────────────────────────────────────────────┐
│                              POZOS (DATOS PRINCIPALES)                          │
└─────────────────────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────────────────┐
│                                  pozos                                       │
├──────────────────────────────────────────────────────────────────────────────┤
│ id_pozo (PK)                                                                 │
│ orden                                                                        │
│ rec_id                                                                       │
│ hole_id (UNIQUE)                                                             │
│ este                                                                         │
│ norte                                                                        │
│ cota                                                                         │
│ length                                                                       │
│ target                                                                       │
│ az                                                                           │
│ dip                                                                          │
│ plataforma                                                                   │
│ mina                                                                         │
│ tipo_ubicacion (ENUM: 'Underground', 'Surface')                             │
│ estado (ENUM: 'planificado','en_perforacion','completado','suspendido')     │
│ id_geologo_creador (FK → usuarios)                                          │
│ id_perforista_asignado (FK → usuarios)                                      │
│ id_geotecnico_asignado (FK → usuarios)                                      │
│ fecha_creacion                                                               │
│ fecha_inicio_perforacion                                                     │
│ fecha_fin_perforacion                                                        │
│ perforacion_finalizada (BOOLEAN)                                             │
│ ediciones_hole_id_restantes (DEFAULT 4)                                      │
└──────────┬───────────────────────────────────────────────────────────────────┘
           │
           │ (1 a muchos)
           │
           ├──────────────────────────────────────────────────────────────┐
           │                          │                │                 │
           ▼                          ▼                ▼                 ▼
┌────────────────────┐  ┌──────────────────┐  ┌──────────────┐  ┌──────────────┐
│  auditoria_pozos   │  │ observaciones_   │  │  problemas   │  │   riesgos    │
├────────────────────┤  │   perforista     │  │   pozo       │  │   pozo       │
│ id_auditoria (PK)  │  ├──────────────────┤  ├──────────────┤  ├──────────────┤
│ id_pozo (FK)       │  │ id_obs (PK)      │  │ id_prob (PK) │  │ id_riesgo(PK)│
│ id_usuario (FK)    │  │ id_pozo (FK)     │  │ id_pozo (FK) │  │ id_pozo (FK) │
│ campo_modificado   │  │ id_usuario (FK)  │  │ id_usuario   │  │ id_usuario   │
│ valor_anterior     │  │ fecha_hora       │  │ (FK)         │  │ (FK)         │
│ valor_nuevo        │  │ profundidad      │  │ fecha_hora   │  │ fecha_hora   │
│ fecha_hora         │  │ descripcion      │  │ descripcion  │  │ descripcion  │
│ ip_address         │  │ tipo (ENUM:      │  │ tipo_falla   │  │ nivel_semaforo│
└────────────────────┘  │  'corte_veta',   │  │ acciones     │  │ (ENUM:       │
                        │  'corte_estéril')│  │ tomados      │  │ 'verde',     │
                        └──────────────────┘  │ resueltas    │  │ 'amarillo',  │
                                              │ (BOOLEAN)    │  │ 'rojo')      │
                                              └──────────────┘  └──────────────┘


┌─────────────────────────────────────────────────────────────────────────────────┐
│                              ARCHIVOS Y DATOS ADICIONALES                       │
└─────────────────────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────────────────┐
│                            archivos_adjuntos                                 │
├──────────────────────────────────────────────────────────────────────────────┤
│ id_archivo (PK)                                                              │
│ id_pozo (FK)                                                                 │
│ id_usuario_subio (FK → usuarios)                                            │
│ tipo_archivo (ENUM: 'planta','seccion','geotecnia','survey','litologia')    │
│ nombre_archivo                                                               │
│ ruta_archivo                                                                 │
│ formato (ENUM: 'pdf','jpg','jpeg','png','tiff','raw','mpeg','mp4','csv')    │
│ fecha_subida                                                                 │
│ descripcion                                                                  │
└──────────────────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────────────────┐
│                            datos_geotecnicos                                 │
├──────────────────────────────────────────────────────────────────────────────┤
│ id_geotecnia (PK)                                                            │
│ id_pozo (FK)                                                                 │
│ id_usuario_subio (FK → usuarios)                                            │
│ fecha_subida                                                                 │
│ archivo_origen                                                               │
│ datos_json (JSON)  -- Contiene todos los datos del CSV                      │
└──────────────────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────────────────┐
│                            datos_survey                                      │
├──────────────────────────────────────────────────────────────────────────────┤
│ id_survey (PK)                                                               │
│ id_pozo (FK)                                                                 │
│ id_usuario_subio (FK → usuarios)                                            │
│ fecha_subida                                                                 │
│ profundidad_from                                                             │
│ profundidad_to                                                               │
│ desviacion_azimut                                                            │
│ desviacion_dip                                                               │
│ archivo_origen                                                               │
└──────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────┐
│    datos_litologicos    │
├─────────────────────────┤
│ id_litologia (PK)       │
│ id_pozo (PK)            │
│ profundidad_desde_m     │
│ profundidad_hasta_m     │
│ tipo_roca               │
│ mineral_presente        │
│ ley_mineral             │
│ textura                 │
│ alteracion              │
│ id_usuario_subio (FK)   │
│ fecha_subida            │
│ archivo_origen_csv      │
└─────────────────────────┘
📱 FLUJO DE LA APLICACIÓN

┌─────────────────────────────────────────────────────────────┐
│                    PANTALLA DE LOGIN                        │
│  [Usuario] [Contraseña]  o  [🔒 Biométrico]                │
│  ¿Olvidó su contraseña? [Recuperar por email]              │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│                    PANTALLA DE INICIO                       │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ 🔴 LEK-0325 - En perforación - Riesgo ALTO         │   │
│  │ 🟡 LEK-0326 - En perforación - Riesgo MEDIO        │   │
│  │ 🟢 LEK-0327 - En perforación - Riesgo BAJO         │   │
│  │ ⚪ LEK-0328 - Planificado                           │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  [🔍 Buscar]  [➕ Nuevo Pozo]  [📊 Reportes]               │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│                    DETALLE DEL POZO                         │
│  HOLE_ID: LEK-0325  [Editar] (solo si tiene permisos)      │
│  Ubicación: GTW 554 - Underground                           │
│  Estado: En perforación                                     │
│                                                             │
│  [📋 Datos] [📝 Observaciones] [📎 Archivos] [⚠️ Riesgos]  │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ 📋 DATOS DE PERFORACIÓN (del CSV)                  │   │
│  │ Este: 2394067.33  Norte: 4808282.14  Cota: 559     │   │
│  │ Length: 125  AZ: 42  DIP: -47                       │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ 📎 ARCHIVOS ADJUNTOS                                │   │
│  │ [📄 Planta.pdf] [📄 Seccion.jpg] [📄 Geotecnia.csv]│   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘

🌍 English
Overview
Undsurf is a mobile and web application designed for the comprehensive management of drilling holes in underground and open-pit (surface) mines.

Its primary goal is to streamline data transmission between different mine site stakeholders. The workflow starts with the Shift Geologist, who plans the drilling program and uploads the initial data. Instantly, Drillers receive this information on-site to execute the drilling and report progress in real-time. Finally, the Geotechnical Engineer and Mining Engineer utilize this updated data for analysis, site visits, and decision-making, eliminating the errors associated with paper-based or informal communication.

👥 User Roles & Permissions
Role	Permissions & Functionalities
👨‍🔬 Geologist (Admin)	Primary user. Uploads plans (PDF/JPG/PNG) and initial CSV tables. Has full control over HOLE_ID editing (even after the hole is completed). Logs all modifications and views complete history (who, when, and what changed).
⛏️ Driller	Updates drilling progress. Can only modify the HOLE_ID column (maximum 4 times); after that, they must request permission from the Geologist. Can add observations (vein depths, issues like water return loss due to faults).
📐 Geotechnical Engineer	Read-only access to drilling data. Can upload their own geotechnical CSV spreadsheets and add geotechnical observations with color codes (timestamped) under the location tab, without altering critical drilling data.
🚀 Key Features
🗂️ HOLE_ID Manager: Unique names with a strict format (Uppercase + Hyphen + Numbers). The system automatically validates if a HOLE_ID already exists in the database, displaying an alert to prevent duplicates.

🔒 Hole Locking: Once a hole is completed, the HOLE_ID and its data are locked (cannot be edited). Only the Geologist Admin has the authority to unlock it for exceptional changes.

📊 Plan & Risk Visualization (Traffic Light): Users can upload plans (Plan view and Section) in PDF, JPG, PNG, MP4, etc. These plans are overlaid with a traffic light system (Red, Yellow, Green) to flag geological or underground infrastructure risks.

📂 Technical Data Upload: Support for uploading CSV files for:

Drilling data (mandatory).

Geotechnical data (optional).

Survey / Hole deviation data (optional).

Lithological data (optional).

🔎 Advanced Search & Filters: Search by HOLE_ID, Location, Mine, Type (Underground/Surface), Driller, Geologist, Engineer, Date, and Risk (traffic light color).

📱 Smart Dashboard: The home screen always displays holes currently in progress or advancing first, along with their associated risks (traffic light system), enabling rapid decision-making.

🔐 Security & Auditing:

Login with Username/Password (first time).

Biometric data (Fingerprint) for subsequent logins.

Password recovery via email.

Full traceability log: every modification (Geologist or Driller) is recorded with exact timestamps.

🛠️ Tech Stack (Suggested / Data Structure)
Database: Relational (e.g., PostgreSQL) with tables for Holes, Users, CSV Files, Observations, and Change History.

Backend: RESTful API to handle permission logic and HOLE_ID uniqueness validation.

Frontend: Mobile/Hybrid application with support for camera (plan scanning) and biometric readers.

COMPLETE ENTITY-RELATIONSHIP DIAGRAM

┌─────────────────────────────────────────────────────────────────────────────────┐
│                              USUARIOS Y PERMISOS                                │
└─────────────────────────────────────────────────────────────────────────────────┘

┌──────────────────┐       ┌──────────────────┐       ┌──────────────────┐
│     usuarios     │       │  roles_usuario   │       │  permisos_rol    │
├──────────────────┤       ├──────────────────┤       ├──────────────────┤
│ id_usuario (PK)  │◄──────│ id_rol (PK)      │◄──────│ id_permiso (PK)  │
│ username         │       │ nombre_rol       │       │ id_rol (FK)      │
│ password_hash    │       │ descripcion      │       │ accion           │
│ email            │       └──────────────────┘       │ tabla_afectada   │
│ huella_hash      │                                  └──────────────────┘
│ rol (FK)         │
│ activo           │
│ fecha_registro   │
│ ultimo_login     │
└────────┬─────────┘
         │
         │ (1 a muchos)
         │
         ▼
┌──────────────────┐
│   login_log      │
├──────────────────┤
│ id_login (PK)    │
│ id_usuario (FK)  │
│ fecha_hora       │
│ ip_address       │
│ tipo_auth        │  -- 'password' o 'biometrico'
│ exitoso          │
└──────────────────┘


┌─────────────────────────────────────────────────────────────────────────────────┐
│                              POZOS (DATOS PRINCIPALES)                          │
└─────────────────────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────────────────┐
│                                  pozos                                       │
├──────────────────────────────────────────────────────────────────────────────┤
│ id_pozo (PK)                                                                 │
│ orden                                                                        │
│ rec_id                                                                       │
│ hole_id (UNIQUE)                                                             │
│ este                                                                         │
│ norte                                                                        │
│ cota                                                                         │
│ length                                                                       │
│ target                                                                       │
│ az                                                                           │
│ dip                                                                          │
│ plataforma                                                                   │
│ mina                                                                         │
│ tipo_ubicacion (ENUM: 'Underground', 'Surface')                             │
│ estado (ENUM: 'planificado','en_perforacion','completado','suspendido')     │
│ id_geologo_creador (FK → usuarios)                                          │
│ id_perforista_asignado (FK → usuarios)                                      │
│ id_geotecnico_asignado (FK → usuarios)                                      │
│ fecha_creacion                                                               │
│ fecha_inicio_perforacion                                                     │
│ fecha_fin_perforacion                                                        │
│ perforacion_finalizada (BOOLEAN)                                             │
│ ediciones_hole_id_restantes (DEFAULT 4)                                      │
└──────────┬───────────────────────────────────────────────────────────────────┘
           │
           │ (1 a muchos)
           │
           ├──────────────────────────────────────────────────────────────┐
           │                          │                │                 │
           ▼                          ▼                ▼                 ▼
┌────────────────────┐  ┌──────────────────┐  ┌──────────────┐  ┌──────────────┐
│  auditoria_pozos   │  │ observaciones_   │  │  problemas   │  │   riesgos    │
├────────────────────┤  │   perforista     │  │   pozo       │  │   pozo       │
│ id_auditoria (PK)  │  ├──────────────────┤  ├──────────────┤  ├──────────────┤
│ id_pozo (FK)       │  │ id_obs (PK)      │  │ id_prob (PK) │  │ id_riesgo(PK)│
│ id_usuario (FK)    │  │ id_pozo (FK)     │  │ id_pozo (FK) │  │ id_pozo (FK) │
│ campo_modificado   │  │ id_usuario (FK)  │  │ id_usuario   │  │ id_usuario   │
│ valor_anterior     │  │ fecha_hora       │  │ (FK)         │  │ (FK)         │
│ valor_nuevo        │  │ profundidad      │  │ fecha_hora   │  │ fecha_hora   │
│ fecha_hora         │  │ descripcion      │  │ descripcion  │  │ descripcion  │
│ ip_address         │  │ tipo (ENUM:      │  │ tipo_falla   │  │ nivel_semaforo│
└────────────────────┘  │  'corte_veta',   │  │ acciones     │  │ (ENUM:       │
                        │  'corte_estéril')│  │ tomados      │  │ 'verde',     │
                        └──────────────────┘  │ resueltas    │  │ 'amarillo',  │
                                              │ (BOOLEAN)    │  │ 'rojo')      │
                                              └──────────────┘  └──────────────┘


┌─────────────────────────────────────────────────────────────────────────────────┐
│                              ARCHIVOS Y DATOS ADICIONALES                       │
└─────────────────────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────────────────┐
│                            archivos_adjuntos                                 │
├──────────────────────────────────────────────────────────────────────────────┤
│ id_archivo (PK)                                                              │
│ id_pozo (FK)                                                                 │
│ id_usuario_subio (FK → usuarios)                                            │
│ tipo_archivo (ENUM: 'planta','seccion','geotecnia','survey','litologia')    │
│ nombre_archivo                                                               │
│ ruta_archivo                                                                 │
│ formato (ENUM: 'pdf','jpg','jpeg','png','tiff','raw','mpeg','mp4','csv')    │
│ fecha_subida                                                                 │
│ descripcion                                                                  │
└──────────────────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────────────────┐
│                            datos_geotecnicos                                 │
├──────────────────────────────────────────────────────────────────────────────┤
│ id_geotecnia (PK)                                                            │
│ id_pozo (FK)                                                                 │
│ id_usuario_subio (FK → usuarios)                                            │
│ fecha_subida                                                                 │
│ archivo_origen                                                               │
│ datos_json (JSON)  -- Contiene todos los datos del CSV                      │
└──────────────────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────────────────┐
│                            datos_survey                                      │
├──────────────────────────────────────────────────────────────────────────────┤
│ id_survey (PK)                                                               │
│ id_pozo (FK)                                                                 │
│ id_usuario_subio (FK → usuarios)                                            │
│ fecha_subida                                                                 │
│ profundidad_from                                                             │
│ profundidad_to                                                               │
│ desviacion_azimut                                                            │
│ desviacion_dip                                                               │
│ archivo_origen                                                               │
└──────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────┐
│     datos_survey        │
├─────────────────────────┤
│ id_survey (PK)          │
│ id_pozo (FK)            │
│ profundidad_m           │
│ azimuth                 │
│ dip                     │
│ desviacion_horizontal   │
│ desviacion_vertical     │
│ id_usuario_subio (FK)   │
│ fecha_subida            │
│ archivo_origen_csv      │
└─────────────────────────┘

┌─────────────────────────┐
│    datos_litologicos    │
├─────────────────────────┤
│ id_litologia (PK)       │
│ id_pozo (PK)            │
│ profundidad_desde_m     │
│ profundidad_hasta_m     │
│ tipo_roca               │
│ mineral_presente        │
│ ley_mineral             │
│ textura                 │
│ alteracion              │
│ id_usuario_subio (FK)   │
│ fecha_subida            │
│ archivo_origen_csv      │
└─────────────────────────┘
FLOW APP

┌─────────────────────────────────────────────────────────────┐
│                    PANTALLA DE LOGIN                        │
│  [Usuario] [Contraseña]  o  [🔒 Biométrico]                │
│  ¿Olvidó su contraseña? [Recuperar por email]              │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│                    PANTALLA DE INICIO                       │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ 🔴 LEK-0325 - En perforación - Riesgo ALTO         │   │
│  │ 🟡 LEK-0326 - En perforación - Riesgo MEDIO        │   │
│  │ 🟢 LEK-0327 - En perforación - Riesgo BAJO         │   │
│  │ ⚪ LEK-0328 - Planificado                           │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  [🔍 Buscar]  [➕ Nuevo Pozo]  [📊 Reportes]               │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│                    DETALLE DEL POZO                         │
│  HOLE_ID: LEK-0325  [Editar] (solo si tiene permisos)      │
│  Ubicación: GTW 554 - Underground                           │
│  Estado: En perforación                                     │
│                                                             │
│  [📋 Datos] [📝 Observaciones] [📎 Archivos] [⚠️ Riesgos]  │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ 📋 DATOS DE PERFORACIÓN (del CSV)                  │   │
│  │ Este: 2394067.33  Norte: 4808282.14  Cota: 559     │   │
│  │ Length: 125  AZ: 42  DIP: -47                       │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ 📎 ARCHIVOS ADJUNTOS                                │   │
│  │ [📄 Planta.pdf] [📄 Seccion.jpg] [📄 Geotecnia.csv]│   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
