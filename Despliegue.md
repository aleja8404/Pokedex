# 📦 Despliegue.md — PokeDex en Google Cloud

> Guía detallada paso a paso para desplegar la aplicación **PokeDex Angular** en **Google Cloud**.  
> Incluye configuración de encabezados de seguridad para obtener calificación **A+** en [SecurityHeaders.com](https://securityheaders.com).

---

## 📋 Tabla de Contenidos

1. [Prerrequisitos](#1-prerrequisitos)
2. [Preparar el Proyecto Angular](#2-preparar-el-proyecto-angular)
3. [Configurar Encabezados de Seguridad](#3-configurar-encabezados-de-seguridad)
4. [Crear el archivo app.yaml](#4-crear-el-archivo-appyaml)
5. [Construir el Proyecto (Build)](#5-construir-el-proyecto-build)
6. [Desplegar en Google Cloud App Engine](#6-desplegar-en-google-cloud-app-engine)
7. [Verificar el Despliegue](#7-verificar-el-despliegue)
8. [Escaneo de Seguridad](#8-escaneo-de-seguridad)
9. [Configuración del Repositorio GitHub](#9-configuración-del-repositorio-github)
10. [Comandos de Referencia Rápida](#10-comandos-de-referencia-rápida)

---

## 1. Prerrequisitos

Asegúrate de tener instalado lo siguiente antes de comenzar:

```bash
# Verificar Node.js (requerido v16+)
node --version

# Verificar npm
npm --version

# Instalar Angular CLI versión 14 globalmente
npm install -g @angular/cli@14

# Verificar Angular CLI
ng version

# Verificar Google Cloud CLI
gcloud --version
```

Si no tienes `gcloud` instalado:
1. Ve a [https://cloud.google.com/sdk/docs/install](https://cloud.google.com/sdk/docs/install)
2. Descarga el instalador para tu sistema operativo
3. Ejecuta el instalador y luego en la terminal:
   ```bash
   gcloud init
   ```

---

## 2. Preparar el Proyecto Angular

### 2.1 Clonar el repositorio

```bash
git clone https://github.com/TU_USUARIO/pokedex.git
cd pokedex
```

### 2.2 Instalar dependencias

```bash
npm install
```

> ⚠️ Si aparece un error con `husky`, ejecuta:
> ```bash
> npm install --ignore-scripts
> ```

### 2.3 Verificar que la aplicación funciona localmente

```bash
npm start
```

Abre el navegador en **http://localhost:4200** y verifica que la aplicación carga correctamente con los Pokémon listados.

---

## 3. Configurar Encabezados de Seguridad

Los encabezados de seguridad HTTP son fundamentales para obtener una buena calificación en SecurityHeaders.com.

### 3.1 Encabezados configurados en `src/index.html`

Agrega los siguientes meta tags dentro del `<head>` del archivo `src/index.html`:

```html
<!-- Security Headers (via meta tags) -->
<meta http-equiv="X-Content-Type-Options" content="nosniff" />
<meta http-equiv="X-Frame-Options" content="DENY" />
<meta http-equiv="Referrer-Policy" content="strict-origin-when-cross-origin" />
<meta http-equiv="Permissions-Policy" content="camera=(), microphone=(), geolocation=(), payment=()" />
<meta http-equiv="Content-Security-Policy" 
  content="default-src 'self'; 
           script-src 'self' 'unsafe-inline'; 
           style-src 'self' 'unsafe-inline'; 
           img-src 'self' data: https://raw.githubusercontent.com https://pokeapi.co; 
           connect-src 'self' https://pokeapi.co https://beta.pokeapi.co; 
           font-src 'self' data:; 
           object-src 'none'; 
           base-uri 'self'; 
           form-action 'self';" />
```

### 3.2 Descripción de cada encabezado

| Header | Propósito |
|--------|-----------|
| `X-Content-Type-Options: nosniff` | Evita que el navegador adivine el tipo MIME del contenido, previniendo ataques de MIME sniffing |
| `X-Frame-Options: DENY` | Impide que la página sea embebida en un `<iframe>`, previniendo ataques de Clickjacking |
| `Referrer-Policy` | Controla qué información de referencia se envía al navegar a otros sitios |
| `Permissions-Policy` | Desactiva acceso a APIs del navegador como cámara, micrófono y geolocalización |
| `Content-Security-Policy` | Política de seguridad que define qué recursos puede cargar el navegador, previniendo XSS |
| `Strict-Transport-Security` | Fuerza conexiones HTTPS (configurado en app.yaml a nivel de servidor) |

---

## 4. Crear el archivo app.yaml

El archivo `app.yaml` configura Google Cloud App Engine para servir los archivos estáticos y define los encabezados HTTP de seguridad a nivel de servidor.

Crea el archivo `app.yaml` en la raíz del proyecto:

```yaml
runtime: python39
api_version: 1

handlers:
  - url: /
    static_files: dist/pokedex-angular/index.html
    upload: dist/pokedex-angular/index.html
    http_headers:
      Strict-Transport-Security: "max-age=31536000; includeSubDomains; preload"
      X-Content-Type-Options: "nosniff"
      X-Frame-Options: "DENY"
      X-XSS-Protection: "1; mode=block"
      Referrer-Policy: "strict-origin-when-cross-origin"
      Permissions-Policy: "camera=(), microphone=(), geolocation=(), payment=()"
      Content-Security-Policy: "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline'; img-src 'self' data: https://raw.githubusercontent.com https://pokeapi.co; connect-src 'self' https://pokeapi.co https://beta.pokeapi.co; font-src 'self' data:; object-src 'none'; base-uri 'self'; form-action 'self';"

  - url: /(.+)
    static_files: dist/pokedex-angular/\1
    upload: dist/pokedex-angular/(.+)
    http_headers:
      Strict-Transport-Security: "max-age=31536000; includeSubDomains; preload"
      X-Content-Type-Options: "nosniff"
      X-Frame-Options: "DENY"
      X-XSS-Protection: "1; mode=block"
      Referrer-Policy: "strict-origin-when-cross-origin"
      Permissions-Policy: "camera=(), microphone=(), geolocation=(), payment=()"
      Content-Security-Policy: "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline'; img-src 'self' data: https://raw.githubusercontent.com https://pokeapi.co; connect-src 'self' https://pokeapi.co https://beta.pokeapi.co; font-src 'self' data:; object-src 'none'; base-uri 'self'; form-action 'self';"
    fallback_files: dist/pokedex-angular/index.html
```

> 💡 **Nota:** El encabezado `Strict-Transport-Security` (HSTS) **solo puede configurarse a nivel de servidor HTTP**, no mediante meta tags en HTML. Por eso es crucial incluirlo en el `app.yaml`.

---

## 5. Construir el Proyecto (Build)

Para generar los archivos estáticos optimizados para producción:

```bash
# Build de producción (genera la carpeta dist/pokedex-angular)
ng build --configuration production
```

O alternativamente:

```bash
npm run build
```

Al finalizar, se crea la carpeta `dist/pokedex-angular/` con todos los archivos listos para desplegar.

**Verificar el contenido del build:**
```bash
# Windows
dir dist\pokedex-angular

# Mac/Linux
ls dist/pokedex-angular
```

Deberías ver archivos como: `index.html`, `main.[hash].js`, `styles.[hash].css`, etc.

---

## 6. Desplegar en Google Cloud

### 6.1 Autenticar con Google Cloud

```bash
gcloud auth login
```

Esto abrirá el navegador para que inicies sesión con tu cuenta de Google.

### 6.2 Configurar el proyecto

```bash
# Listar proyectos disponibles
gcloud projects list

# Establecer el proyecto activo
gcloud config set project TU_PROJECT_ID
```

Reemplaza `TU_PROJECT_ID` con el ID de tu proyecto Google Cloud.

### 6.3 Habilitar la API

```bash
gcloud app create --region=us-central
```

> ⚠️ Solo se necesita ejecutar este comando la primera vez. Selecciona la región más cercana a tus usuarios.

### 6.4 Verificar la estructura de archivos

Antes de desplegar, verifica que tienes:
```
pokedex/
├── app.yaml
├── dist/
│   └── pokedex-angular/
│       ├── index.html
│       ├── main.[hash].js
│       └── ...
├── src/
├── package.json
└── ...
```

### 6.5 Desplegar la aplicación

```bash
gcloud run deploy --source .
```

El sistema te preguntará confirmación. Escribe `Y` y presiona Enter.

**Salida esperada:**
```
Beginning deployment of service [default]...
...
Deployed service [default] to [https://TU_PROJECT_ID.appspot.com]
```

### 6.6 Abrir la aplicación en el navegador

```bash
gcloud app browse
```

Esto abrirá automáticamente la URL de tu aplicación desplegada.

---

## 7. Verificar el Despliegue

### 7.1 Ver el estado del servicio

```bash
gcloud app versions list
```

### 7.2 Ver los logs

```bash
gcloud app logs tail -s default
```

### 7.3 Verificar la URL

La aplicación estará disponible en:
```
https://TU_PROJECT_ID.appspot.com
```

Verifica que:
- ✅ La página carga correctamente
- ✅ Los Pokémon aparecen en la lista
- ✅ Puedes ver los detalles de cada Pokémon
- ✅ La URL usa HTTPS

---

## 8. Escaneo de Seguridad

### 8.1 Escanear con SecurityHeaders.com

1. Ve a [https://securityheaders.com/](https://securityheaders.com/)
2. Ingresa la URL de tu aplicación: `https://TU_PROJECT_ID.appspot.com`
3. Haz clic en **"Scan"**
4. Verifica los headers configurados

**Encabezados que deberías ver en verde (✅):**
- `Strict-Transport-Security`
- `Content-Security-Policy`
- `X-Frame-Options`
- `X-Content-Type-Options`
- `Referrer-Policy`
- `Permissions-Policy`

### 8.2 Calificación esperada

Con la configuración de headers implementada, se espera obtener una calificación **A** o **A+** en SecurityHeaders.com.

### 8.3 Auditoría adicional con SSL Labs (Opcional)

1. Ve a [https://www.ssllabs.com/ssltest/](https://www.ssllabs.com/ssltest/)
2. Ingresa la URL de tu aplicación
3. Espera el análisis completo (puede tardar 1-2 minutos)
4. Verifica que el certificado SSL obtenga calificación **A** o **A+**

---

## 9. Configuración del Repositorio GitHub

### 9.1 Crear repositorio en GitHub

1. Ve a [https://github.com/new](https://github.com/new)
2. Nombre del repositorio: `pokedex`
3. Descripción: "PokeDex Angular - Desplegado en Google Cloud"
4. Visibilidad: **Público**
5. Haz clic en **"Create repository"**

### 9.2 Subir el código

```bash
# Inicializar git en el proyecto
git init

# Agregar todos los archivos
git add .

# Primer commit
git commit -m "feat: inicial PokeDex Angular con configuración Google Cloud"

# Vincular con el repositorio remoto
git remote add origin https://github.com/TU_USUARIO/pokedex.git

# Subir al repositorio
git push -u origin main
```

---

## 10. Comandos de Referencia Rápida

```bash
# Instalar dependencias
npm install

# Ejecutar localmente
npm start

# Build de producción
ng build --configuration production

# Autenticar con Google Cloud
gcloud auth login

# Seleccionar proyecto
gcloud config set project TU_PROJECT_ID

# Desplegar
gcloud app deploy

# Ver la aplicación
gcloud app browse

# Ver logs en tiempo real
gcloud app logs tail -s default

# Listar versiones desplegadas
gcloud app versions list
```

---

## 🆘 Solución de Problemas Comunes

### Error: "Module not found"
```bash
# Limpiar y reinstalar dependencias
rm -rf node_modules
npm install
```

### Error: "ng: command not found"
```bash
# Reinstalar Angular CLI
npm install -g @angular/cli@14
```

### Error: "gcloud: command not found"
- Reinstala Google Cloud SDK desde [https://cloud.google.com/sdk/docs/install](https://cloud.google.com/sdk/docs/install)

### Build falla con error de presupuesto
```bash
# Build con configuración de desarrollo (sin optimizaciones)
ng build --configuration development
```

---

*Documento creado usando [StackEdit](https://stackedit.io/) — Editor Markdown en línea*
