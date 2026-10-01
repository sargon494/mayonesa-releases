# 🥪 Mayonesa con Guisantes 🟢

> **Pizarra compartida en tiempo real para parejas: lo que dibuja tu pareja aparece como fondo de tu pantalla de bloqueo e inicio en Android.**

---

## 📖 Descripción del Proyecto

**Mayonesa con Guisantes** es una aplicación móvil desarrollada en **Flutter** pensada para parejas a distancia o que quieren mantenerse conectadas de una forma íntima y creativa. 

Permite dibujar notas, mensajes y bocetos en un lienzo compartido sincronizado en tiempo real mediante **Google Cloud Firestore**. Lo más destacado es que cada vez que tu pareja dibuja, un servicio en segundo plano compone su dibujo sobre **tu fondo base** y lo pone como fondo de pantalla (bloqueo e inicio), incluido un fondo animado opcional (*live wallpaper*), sin necesidad de abrir la aplicación.

### 💡 Principios de Diseño y Arquitectura
- **Coste Cero Permanente**: Diseñado para funcionar 100% dentro del **Spark Plan gratuito de Firebase** (sin Cloud Functions ni Firebase Storage de pago).
- **Eficiencia de Red**: Implementa el algoritmo de reducción de trazos **Ramer-Douglas-Peucker (RDP)** antes de enviar las coordenadas a Firestore, ahorrando cuota de datos y acelerando la renderización.
- **Seguridad Granular**: Control de acceso mediante reglas en Firestore (`firestore.rules`): nada se puede listar, las parejas solo se leen por ID y por sus miembros, unirse exige conocer el código (`invites/{code}`) y cada escritura valida esquema, tamaño y autoría.
- **Renderizado Off-Screen**: un único painter (`paintCanvasScene`) dibuja la pizarra en pantalla, al exportar y al generar el fondo de pantalla, así que los tres resultados coinciden.

---

## ✨ Características Principales

- 🎨 **Lienzo Interactivo Fluido**:
  - Trazos suaves con `CustomPainter` y optimización en tiempo real.
  - Paleta de colores seleccionables y ajuste de grosor de trazo.
  - Función de **Deshacer (Undo)** que elimina únicamente tu último trazo realizado.
  - Función de **Borrar Pizarra** instantánea para ambos miembros.
- 📱 **Fondo de pantalla sincronizado (Android)**:
  - Un servicio en primer plano escucha Firestore y, cuando dibuja tu pareja, actualiza tu fondo de bloqueo e inicio.
  - Solo se toca tu pantalla si has elegido antes un **fondo base** (para no pisar el tuyo).
  - Fondo animado opcional (`LiveWallpaperService`).
  - Notificación cuando tu pareja dibuja.
- 👥 **Sistema de Emparejamiento por Código**:
  - Código único y seguro de 8 caracteres alfanuméricos.
  - Flujo de creación o unión mediante código.
  - Enrutamiento inteligente con **GoRouter**: una vez emparejados, ambos acceden automáticamente al lienzo.
- 🌙 **Diseño Moderno y Cuidado**:
  - Tema oscuro (*Dark Mode*) con contrastes equilibrados.
  - Tipografía moderna **Outfit** integrada.

---

## 🛠️ Stack Tecnológico

| Capa | Tecnología |
| :--- | :--- |
| **Framework** | [Flutter](https://flutter.dev/) (Canal estable, Dart 3.x) |
| **Gestión de Estado** | [Riverpod](https://riverpod.dev/) (`flutter_riverpod`) |
| **Navegación** | [GoRouter](https://pub.dev/packages/go_router) |
| **Backend & Base de Datos** | [Firebase Cloud Firestore](https://firebase.google.com/docs/firestore) |
| **Autenticación** | [Firebase Authentication](https://firebase.google.com/docs/auth) (Email & Contraseña) |
| **Fondo de pantalla** | [wallpaper_manager_plus](https://pub.dev/packages/wallpaper_manager_plus), [flutter_foreground_task](https://pub.dev/packages/flutter_foreground_task) + Kotlin (`LiveWallpaperService`) |
| **Optimización de Trazos** | Algoritmo Ramer-Douglas-Peucker (`rdp_simplifier.dart`) |

---

## 📂 Estructura del Código

```text
esclavo/
├── android/                        # Configuración nativa de Android
│   └── app/src/main/kotlin/.../
│       ├── MainActivity.kt         # Canal para abrir los ajustes del fondo animado
│       └── LiveWallpaperService.kt # Fondo animado
├── assets/
│   └── fonts/                      # Fuentes tipográficas (Outfit)
├── lib/
│   ├── core/                       # Constantes, tema y utilidades
│   │   ├── constants.dart          # Colores, grosores, límites y rutas
│   │   ├── theme.dart              # Tema visual de la aplicación
│   │   ├── routing/app_router.dart # Enrutamiento y redirecciones
│   │   └── utils/
│   │       ├── rdp_simplifier.dart # Algoritmo de compresión de trazos
│   │       └── stroke_renderer.dart# Painter compartido + generador de PNG
│   ├── features/
│   │   ├── auth/                   # Registro e inicio de sesión
│   │   ├── pairing/                # Generación y unión por código de pareja
│   │   ├── canvas/                 # Pizarra, herramientas y sincronización
│   │   └── lockscreen/             # Servicio en segundo plano y fondo de pantalla
│   ├── firebase_options.dart       # Opciones autogeneradas por FlutterFire
│   ├── app.dart                    # Configuración de MaterialApp y router
│   └── main.dart                   # Punto de entrada de la aplicación
├── firestore.rules                 # Reglas de seguridad de Firestore
├── test/                           # Tests unitarios
├── firestore.indexes.json          # Índices compuestos de Firestore
├── firebase.json                   # Configuración del proyecto Firebase
└── pubspec.yaml                    # Dependencias de Flutter
```

---

## 🚀 Requisitos Previos e Instalación

### Requisitos
- [Flutter SDK](https://docs.flutter.dev/get-started/install) (`>= 3.4.0`)
- [Android Studio](https://developer.android.com/studio) con Android SDK (API 34 recomendada)
- [Firebase CLI](https://firebase.google.com/docs/cli) instalado y autenticado (`firebase login`)
- Un dispositivo Android físico o emulador con Google Play Services

### Pasos de Configuración

1. **Clonar el repositorio:**
   ```bash
   git clone <URL_DE_TU_REPOSITORIO_PRIVADO>
   cd esclavo
   ```

2. **Instalar dependencias de Flutter:**
   ```bash
   flutter pub get
   ```

3. **Configuración de Firebase:**
   > ℹ️ *Al ser este un repositorio privado, los archivos `android/app/google-services.json` y `lib/firebase_options.dart` ya se encuentran configurados para el proyecto Firebase `mayonesa-con-guisantes`.*

   Si necesitas desplegar o actualizar las reglas e índices de Firestore:
   ```bash
   firebase deploy --only firestore:rules,firestore:indexes
   ```

4. **Ejecutar en modo depuración:**
   ```bash
   flutter run
   ```

5. **Compilar APK de lanzamiento:**
   ```bash
   flutter build apk --split-per-abi
   ```
   Los archivos `.apk` resultantes se generarán en `build/app/outputs/flutter-apk/`.

---

## 📱 Cómo activar el fondo sincronizado

1. Instala y abre la app en tu teléfono.
2. Inicia sesión y empareja tu cuenta con tu pareja.
3. En el icono de fondo de pantalla de la barra superior, elige **Elegir mi fondo base**.
4. (Opcional) Elige **Activar fondo animado en Inicio** para el fondo en movimiento.
5. Deja el servicio en marcha: cuando tu pareja dibuje, tu fondo se actualizará.

---

## 🔒 Subir este Proyecto a GitHub (Repositorio Privado)

Si aún no has vinculado tu repositorio remoto, sigue estos pasos desde la consola en la carpeta raíz del proyecto:

1. **Verificar el estado de Git:**
   ```bash
   git status
   ```

2. **Añadir todos los archivos y realizar el primer commit:**
   ```bash
   git add .
   git commit -m "feat: versión inicial de Mayonesa con Guisantes con fondo sincronizado"
   ```

3. **Crear la rama principal y enlazar tu repositorio remoto privado:**
   ```bash
   git branch -M main
   git remote add origin https://github.com/<tu-usuario>/<tu-repositorio-privado>.git
   ```

4. **Subir los cambios a GitHub:**
   ```bash
   git push -u origin main
   ```

---

## 🤖 CI/CD y Generación Automática de APKs (GitHub Releases)

El repositorio incluye un flujo automatizado de GitHub Actions ([.github/workflows/release.yml](file:///.github/workflows/release.yml)) para compilar y publicar automáticamente el instalador `.apk` en la sección **Releases** de tu repositorio.

### Método 1: Publicar mediante etiquetas (Git Tags)
Cada vez que crees y subas una etiqueta de versión que comience por `v`, GitHub compilará el APK y creará la release automáticamente:

```bash
# 1. Crear una etiqueta (ejemplo: v1.0.0)
git tag v1.0.0

# 2. Subir la etiqueta a GitHub
git push origin v1.0.0
```

### Método 2: Disparo manual desde GitHub (Sin comandos)
1. Entra a tu repositorio en GitHub y ve a la pestaña **Actions**.
2. En el menú de la izquierda, selecciona **Build & Release APK**.
3. Pulsa el botón **Run workflow** en la derecha.
4. (Opcional) Introduce el número de versión (ej. `v1.0.0`) y haz clic en **Run workflow**.
5. En unos minutos, el APK aparecerá disponible para descargar en **Releases** en la página principal del repositorio.

---

## 🔐 Firma del APK de release

El APK de release **debe** firmarse siempre con la misma clave; si no, Android rechaza las actualizaciones sobre una versión ya instalada. El workflow firma con un keystore propio guardado en GitHub Secrets:

1. Genera el keystore una sola vez y **guárdalo en un lugar seguro** (si lo pierdes no podrás actualizar la app):
   ```bash
   keytool -genkeypair -v -keystore upload-keystore.jks -alias upload \
     -keyalg RSA -keysize 2048 -validity 10000
   ```
2. Codifícalo: `base64 -w0 upload-keystore.jks` (en macOS: `base64 -i upload-keystore.jks`).
3. En GitHub → Settings → Secrets and variables → Actions, crea:
   `ANDROID_KEYSTORE_BASE64`, `ANDROID_KEYSTORE_PASSWORD`, `ANDROID_KEY_ALIAS`, `ANDROID_KEY_PASSWORD`.
4. Para compilar release en local, crea `android/key.properties` (ignorado por git) con `storePassword`, `keyPassword`, `keyAlias` y `storeFile` (ruta al `.jks`). Sin él, el build local cae a la clave debug.

> Las apps ya instaladas con la clave debug antigua tendrán que desinstalarse una vez para instalar la primera versión con la nueva firma.

---

## 🔄 Actualizaciones automáticas (dentro de la app)

La app se actualiza sola, sin apps externas: al abrirla consulta en GitHub si hay una versión más nueva, la descarga con barra de progreso y la instala. También hay un botón **Buscar actualizaciones** en el menú de la cuenta (⋮).

- En Android 12+ la instalación puede hacerse **sin confirmación** (la app se actualiza a sí misma y declara `UPDATE_PACKAGES_WITHOUT_USER_ACTION`). Si tu móvil pide confirmar, será un toque.
- Android solo acepta la actualización si el APK está firmado con **la misma clave** que la app instalada, así que un APK manipulado no se instalaría.
- La primera vez, Android pide permitir a la app "instalar aplicaciones" (un ajuste, una sola vez).
- Tras actualizar, Android reinicia la app.

### Configuración (una sola vez)

La app necesita descargar el APK sin iniciar sesión, por lo que las releases van a un **repo público solo de releases** (el código sigue privado):

1. Crea un repo público nuevo, por ejemplo `mayonesa-releases`, **con un README inicial** (hace falta al menos un commit).
2. Crea un token: GitHub → Settings → Developer settings → Fine-grained tokens. Acceso solo a ese repo, permiso **Contents: Read and write**.
3. En el repo del código: Settings → Secrets and variables → Actions:
   - Secreto `RELEASES_REPO_TOKEN` = el token.
   - Variable `RELEASES_REPO` = `tu-usuario/mayonesa-releases`.
4. En `lib/core/constants.dart` pon `kUpdateRepo = 'tu-usuario/mayonesa-releases'` y publica esa versión. Mientras esté vacío, las actualizaciones están desactivadas.

Las dos primeras instalaciones (una por móvil) se hacen a mano; desde esa versión en adelante, todo es automático.

### Publicar una versión

```bash
# 1) sube version: en pubspec.yaml (p. ej. 1.3.0+7) y haz commit
git tag v1.3.0
git push origin v1.3.0
```

- El número tras el `+` debe ser **siempre mayor** que el anterior, o Android no instalará encima.
- El tag debe coincidir con la versión del `pubspec.yaml` (`v1.3.0` ↔ `1.3.0+7`). **El workflow lo comprueba y falla si no**, porque la app compara el tag con la versión instalada: si no coincidieran, se ofrecería la misma actualización sin parar.
- Las versiones `prerelease` y los borradores se ignoran.

---

## 🧪 Calidad

El workflow ejecuta `flutter analyze` y `flutter test` antes de compilar. En local:

```bash
flutter analyze
flutter test
```

---

## 📄 Licencia

Proyecto privado para uso personal. Todos los derechos reservados.
