# Práctica 3 – Integración de Supabase en Android con Kotlin

**Estudiante:** Moisés Panamá · mpanamam@uteq.edu.ec<br>
**Asignatura:** Aplicaciones Móviles – UTEQ 2026<br>
**Repositorio:** [Practica-3-integracion-supabase-alumnos](https://github.com/MoisesPanama/Practica-3-integracion-supabase-alumnos)

Aplicación Android nativa desarrollada en Kotlin para la **Guía Práctica 02: Integración de Supabase en Android con Kotlin**. El sistema se conecta a una base de datos PostgreSQL en Supabase mediante el SDK oficial (`postgrest-kt`), consulta la tabla `public.alumnos` de forma asíncrona con corutinas y presenta los registros ordenados alfabéticamente en una interfaz XML.

## Objetivo de la práctica

Aplicar los conceptos de consumo de servicios en la nube y persistencia remota vistos en clase:

- Configurar una tabla relacional en PostgreSQL (Supabase) y restringir su acceso mediante políticas **Row Level Security (RLS)**.
- Proteger la URL y la clave publicable del proyecto mediante `local.properties` y `BuildConfig` sin exponer credenciales en el repositorio.
- Centralizar el cliente de conexión mediante un patrón **Singleton** (`object SupabaseManager`).
- Deserializar respuestas JSON en modelos Kotlin con `@Serializable` (`kotlinx.serialization`).
- Ejecutar consultas asíncronas con `lifecycleScope` y gestionar errores de red mediante diálogos Material Design.

## ¿Qué hace?

Al iniciar la aplicación, `MainActivity` lanza una corutina que consulta los registros de la tabla `alumnos` en Supabase ordenados ascendentemente por el campo `nombres` (`Order.ASCENDING`). Mientras se realiza la petición HTTPS, se muestra un indicador de carga (`ProgressBar`); al recibir la respuesta, los datos se formatean y se despliegan dentro de un `ScrollView` y un `TextView` (`txtVistaAlumnos`) debajo del encabezado institucional de la UTEQ. Si ocurre una falla de conexión o de autenticación, `SupabaseErrorHandler` captura la excepción y muestra una alerta descriptiva.

## Flujo de la aplicación

```text
MainActivity (onCreate)
        │
        │  lifecycleScope.launch
        ▼
SupabaseManager (Postgrest) ──► Supabase Cloud (public.alumnos + RLS)
        │
        ├── Éxito ──► Lista de Alumno (@Serializable) ──► txtVistaAlumnos (Orden A-Z)
        └── Error ──► SupabaseErrorHandler ──► MaterialAlertDialogBuilder
```

## Funcionalidades

- [x] Tabla `public.alumnos` configurada en PostgreSQL con campos `id`, `created_at`, `foto`, `nombres`, `correo`, `paralelo` y `telefono`.
- [x] Política RLS (`FOR SELECT TO anon`) habilitada para lectura segura de registros.
- [x] Configuración de credenciales mediante `local.properties` y `BuildConfig`.
- [x] Permiso `android.permission.INTERNET` declarado en `AndroidManifest.xml`.
- [x] Cliente único reutilizable (`SupabaseManager`) con módulo `Postgrest`.
- [x] Modelo de datos `Alumno` anotado con `@Serializable` y soporte para campos opcionales nulos.
- [x] Consulta asíncrona ordenada alfabéticamente (`Order.ASCENDING`).
- [x] Indicador visual de carga (`ProgressBar`) y desplazamiento vertical (`ScrollView`).
- [x] Manejo centralizado de errores con `MaterialAlertDialogBuilder`.

## Tecnologías

| Tecnología | Uso en el proyecto |
| --- | --- |
| Kotlin | Lenguaje principal de la aplicación y corutinas. |
| Android Studio | Entorno de desarrollo integrado. |
| Supabase SDK (`postgrest-kt` 3.6.0) | Cliente oficial para consultas a PostgreSQL. |
| Ktor Client (`3.5.0`) | Motor HTTP asíncrono para Android. |
| Kotlinx Serialization | Conversión de respuestas JSON a objetos `Alumno`. |
| ConstraintLayout & ScrollView | Diseño responsivo y desplazamiento de la lista. |
| Material Components | Diálogos de alerta (`MaterialAlertDialogBuilder`). |
| Android SDK 36 / minSdk 26 | Compilación y compatibilidad desde Android 8.0. |

## Requisitos

- Android Studio instalado.
- JDK 11 configurado en Gradle.
- Cuenta y proyecto activo en [Supabase](https://supabase.com) con la tabla `public.alumnos` creada.
- Emulador o dispositivo físico con Android 8.0 (API 26) o superior y conexión a Internet.

## Estructura del proyecto

```text
AlumnosSupabaseUTEQ/
├── app/src/main/
│   ├── java/com/uteq/software/alumnossupabaseuteq/
│   │   ├── data/
│   │   │   └── SupabaseManager.kt         # Cliente Singleton de Supabase
│   │   ├── models/
│   │   │   └── Alumno.kt                  # Modelo @Serializable de la tabla
│   │   ├── utils/
│   │   │   └── SupabaseErrorHandler.kt    # Gestión centralizada de errores
│   │   └── MainActivity.kt                # Consulta asíncrona y renderizado UI
│   ├── res/
│   │   ├── drawable/
│   │   │   └── logo_uteq.png              # Logo institucional de la UTEQ
│   │   ├── layout/
│   │   │   └── activity_main.xml          # Interfaz ConstraintLayout + ScrollView
│   │   └── values/
│   │       ├── colors.xml
│   │       ├── strings.xml
│   │       └── themes.xml
│   └── AndroidManifest.xml                # Declaración de permiso INTERNET
├── .gitignore                             # Excluye local.properties
├── README.md
└── build.gradle.kts
```

## Recursos gráficos

| Recurso | Uso en la aplicación |
| --- | --- |
| `logo_uteq.png` | Logo institucional de la Universidad Técnica Estatal de Quevedo en el encabezado. |

## Cómo ejecutar

1. Clona el repositorio:
   ```bash
   git clone https://github.com/MoisesPanama/Practica-3-integracion-supabase-alumnos.git
   ```
2. Abre la carpeta del proyecto en Android Studio.
3. En la raíz del proyecto, abre o crea el archivo `local.properties` y agrega tus credenciales de Supabase (sin comillas):
   ```properties
   SUPABASE_URL=https://TU_PROYECTO.supabase.co
   SUPABASE_KEY=TU_CLAVE_PUBLICABLE
   ```
4. Sincroniza el proyecto con **Sync Project with Gradle Files**.
5. Ejecuta la aplicación en un emulador o dispositivo físico con **Run ▶**.

También puedes compilar el APK de depuración desde la terminal:

```bash
# Windows
.\gradlew.bat assembleDebug

# Linux / macOS
./gradlew assembleDebug
```

## Evidencias

Capturas del entorno en Supabase, la ejecución exitosa en dispositivo Android y la validación de manejo de errores sin conexión.

### 1. Tabla `public.alumnos` en Supabase
*(Arrastra aquí tu captura de Supabase)*

### 2. Listado de alumnos ordenados alfabéticamente (A-Z)
*(Arrastra aquí tu captura de la app funcionando)*

### 3. Prueba controlada de error sin conexión
*(Arrastra aquí tu captura del diálogo de error)*

## Alcance de la práctica

- La aplicación realiza operaciones de lectura (`SELECT`) sobre la tabla `public.alumnos` mediante acceso anónimo controlado por políticas RLS.
- Las credenciales reales no se incluyen en el repositorio gracias a `local.properties` y `.gitignore`.

## Verificación

- Sincronización de Gradle y compilación correcta con `.\gradlew.bat assembleDebug`.
- Consulta ordenada alfabéticamente y manejo de excepciones de red verificados en ejecución.
