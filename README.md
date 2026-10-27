# 🎮 PokeDex — Despliegue en Google Cloud

> Aplicación Angular que permite explorar especies de Pokémon con información detallada sobre sus características y habilidades.  
> Desarrollada originalmente por [Keiler Mora](https://github.com/keilermora), desplegada en Google Cloud por **Pueblo Paleta Inc.**

---

## 📋 Tabla de Contenidos

- [Descripción del Proyecto](#descripción-del-proyecto)
- [Tecnologías Utilizadas](#tecnologías-utilizadas)
- [Creación de Cuenta en Google Cloud](#creación-de-cuenta-en-google-cloud)
- [Requisitos Previos](#requisitos-previos)
- [Instalación Local](#instalación-local)
- [Despliegue en Google Cloud](#despliegue-en-google-cloud)
- [Seguridad](#seguridad)
- [Referencias](#referencias)

---

## 📖 Descripción del Proyecto

**PokeDex** es una aplicación web desarrollada con Angular que consume la API RESTful [PokéAPI](https://pokeapi.co/). Permite a los usuarios:

- Explorar la lista completa de Pokémon
- Ver detalles de cada Pokémon (tipos, estadísticas, habilidades)
- Buscar Pokémon por nombre o número
- Visualizar información de evoluciones

---

## 🛠 Tecnologías Utilizadas

| Tecnología | Versión | Descripción |
|------------|---------|-------------|
| Angular | 14.x | Framework de desarrollo frontend |
| TypeScript | 4.6.x | Lenguaje de programación |
| PokéAPI | v2 | API RESTful de datos Pokémon |
| Apollo GraphQL | 3.x | Cliente GraphQL |
| Google Cloud App Engine | - | Plataforma de despliegue |

---

## ☁️ Creación de Cuenta en Google Cloud

### Paso 1 — Acceder al sitio de Google Cloud

1. Abre tu navegador y dirígete a [https://cloud.google.com/](https://cloud.google.com/)
2. Haz clic en el botón **"Empieza gratis"** o **"Get started for free"** (esquina superior derecha)

### Paso 2 — Iniciar sesión con cuenta Google

1. Si ya tienes una cuenta de Google (Gmail), ingresa tus credenciales
2. Si no tienes cuenta, haz clic en **"Crear cuenta"** y sigue los pasos:
   - Ingresa tu nombre y apellido
   - Elige un nombre de usuario (correo Gmail)
   - Establece una contraseña segura
   - Verifica tu número de teléfono

### Paso 3 — Registro en Google Cloud Free Tier

1. Una vez autenticado, serás redirigido al formulario de registro de Google Cloud
2. Acepta los **Términos de Servicio** de Google Cloud
3. Ingresa tu **país** y selecciona el uso que darás a la cuenta
4. En la sección **"Información de pago"**, deberás ingresar una tarjeta de crédito o débito
   > ⚠️ **Nota:** Google requiere una tarjeta para verificar identidad. No se realiza ningún cobro durante el Free Tier (primeros 90 días con USD 300 de crédito gratuito)
5. Completa los datos de tu tarjeta y haz clic en **"Empezar mi prueba gratuita"**

### Paso 4 — Configurar el proyecto inicial

1. Una vez registrado, serás llevado a la **Consola de Google Cloud** ([console.cloud.google.com](https://console.cloud.google.com))
2. Google creará automáticamente un proyecto llamado **"My First Project"**
3. Para crear un proyecto dedicado para PokeDex:
   - Haz clic en el selector de proyectos (parte superior de la pantalla)
   - Selecciona **"Nuevo proyecto"**
   - Nombre: `pokedex-app`
   - ID del proyecto: `pokedex-app-[tu-id-unico]`
   - Haz clic en **"Crear"**

### Paso 5 — Instalar Google Cloud CLI (gcloud)

1. Descarga el instalador desde: [https://cloud.google.com/sdk/docs/install](https://cloud.google.com/sdk/docs/install)
2. Ejecuta el instalador y sigue las instrucciones
3. Abre una terminal y ejecuta:
   ```bash
   gcloud init
   ```
4. Sigue las instrucciones para autenticarte y seleccionar tu proyecto

---

## 🖥️ Requisitos Previos

Antes de instalar y ejecutar el proyecto, asegúrate de tener:

- **Node.js** v16 o superior → [Descargar](https://nodejs.org/)
- **npm** v8 o superior (viene con Node.js)
- **Angular CLI** v14 → `npm install -g @angular/cli@14`
- **Git** → [Descargar](https://git-scm.com/)
- **Google Cloud CLI** → [Instalar](https://cloud.google.com/sdk/docs/install)

Verifica las instalaciones:
```bash
node --version
npm --version
ng version
gcloud --version
```

---

## 💻 Instalación Local

```bash
# 1. Clonar el repositorio
git clone https://github.com/TU_USUARIO/pokedex.git
cd pokedex

# 2. Instalar dependencias
npm install

# 3. Ejecutar en modo desarrollo
npm start
```

Abre el navegador en: **http://localhost:4200**

---

## 🚀 Despliegue en Google Cloud

Para el proceso completo de despliegue, consulta el archivo [Despliegue.md](Despliegue.md).

---

## 🔒 Seguridad

La aplicación fue escaneada con [SecurityHeaders.com](https://securityheaders.com) y cuenta con los siguientes encabezados de seguridad configurados:

| Header | Valor | Descripción |
|--------|-------|-------------|
| `Strict-Transport-Security` | `max-age=31536000; includeSubDomains; preload` | Fuerza HTTPS |
| `X-Content-Type-Options` | `nosniff` | Previene MIME sniffing |
| `X-Frame-Options` | `DENY` | Previene Clickjacking |
| `X-XSS-Protection` | `1; mode=block` | Protección XSS legacy |
| `Referrer-Policy` | `strict-origin-when-cross-origin` | Control de referrer |
| `Content-Security-Policy` | Política restrictiva | Previene XSS y inyección |
| `Permissions-Policy` | Desactiva APIs peligrosas | Control de permisos del navegador |

---

## 📚 Referencias

- [PokéAPI](https://pokeapi.co/) — API de datos Pokémon
- [Angular Documentation](https://angular.io/docs)
- [Google Cloud App Engine](https://cloud.google.com/appengine)
- [SecurityHeaders.com](https://securityheaders.com)
- [Keiler Mora — Autor original](https://github.com/keilermora/pokedex-angular)
