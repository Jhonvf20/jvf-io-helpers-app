# Siebel Helpers

Herramienta web interna para administración y soporte de ambientes Oracle Siebel CRM.

---

## Requisitos

- **Node.js** v18 o superior — [https://nodejs.org](https://nodejs.org)
- **Acceso a internet** (solo si se desea auto-actualización)
- **7-Zip** instalado (solo Windows, necesario para auto-actualización) — [https://7-zip.org](https://7-zip.org)

---

## Instalación desde cero

### 1. Descargar

Ve a la sección de [Releases](../../releases/latest) y descarga el archivo `.zip` que corresponda a tu perfil:

| Archivo | Perfil | Descripción |
|---|---|---|
| `siebel-helpers-full.zip` | Full | Todos los módulos |
| `siebel-helpers-lead.zip` | Lead | Migración, monitor, análisis |
| `siebel-helpers-dev1.zip` | Dev 1 | Herramientas básicas de desarrollo |
| `siebel-helpers-dev2.zip` | Dev 2 | Herramientas extendidas de desarrollo |
| `siebel-helpers-funcional.zip` | Funcional | Monitor y utilidades de consulta |

> Si no sabes cuál descargar, consulta con tu líder técnico.

### 2. Descomprimir

Descomprime el `.zip` en la ruta de tu preferencia. Por ejemplo:

**Windows:**
```
C:\jvfio_tools\siebel-helpers\
```

**macOS / Linux:**
```
/opt/siebel-helpers/
```

> El contenido del zip debe quedar directamente en la carpeta, sin carpetas intermedias. Deberías ver `index.js`, `package.json`, `dist/`, etc. en la raíz.

### 3. Instalar dependencias

Abre una terminal en la carpeta donde descomprimiste y ejecuta:

```bash
npm install --production
```

### 4. Configurar (opcional)

Si deseas habilitar la auto-actualización, crea un archivo `.env` en la misma carpeta (junto a `index.js`). Puedes usar `.env.example` como base:

```bash
cp .env.example .env
```

Edita `.env` y configura la contraseña de actualización que te fue proporcionada:

```
UPDATE_ZIP_PASSWORD=tu_contraseña_aqui
```

### 5. Ejecutar

```bash
npm run start
```

La aplicación estará disponible en:

```
http://localhost:23443
```

> Para cambiar el puerto, define la variable de entorno `PORT` antes de ejecutar:
> ```bash
> PORT=8080 npm run start
> ```
> En Windows:
> ```bash
> set PORT=8080 && npm run start
> ```

---

## Actualización manual

Si prefieres actualizar manualmente o la máquina no tiene acceso a internet:

1. Descarga el `.zip` de la nueva versión desde [Releases](../../releases/latest) (el mismo perfil que tienes instalado).
2. Detén la aplicación si está corriendo.
3. Descomprime el `.zip` reemplazando los archivos existentes en la carpeta de instalación.
4. Si el `package.json` cambió, ejecuta nuevamente:
   ```bash
   npm install --production
   ```
5. Inicia la aplicación:
   ```bash
   npm run start
   ```

> Tu archivo `.env` no será reemplazado porque no viene incluido en el zip.

---

## Actualización automática

La aplicación puede actualizarse automáticamente al iniciar. Este es el comportamiento por defecto cuando se cumplen estas condiciones:

1. **La máquina tiene acceso a internet** (específicamente a `api.github.com` y `github.com`).
2. **El archivo `.env` tiene configurada la contraseña** (`UPDATE_ZIP_PASSWORD`).
3. **Windows:** 7-Zip está instalado (se busca automáticamente en `C:\Program Files\7-Zip\`).

### ¿Cómo funciona?

Cada vez que la aplicación arranca, el auto-updater:

1. Lee la versión actual desde `version.json`.
2. Consulta este repositorio para verificar si hay una versión más reciente.
3. Si hay una nueva versión disponible:
   - Descarga el `.zip` correspondiente a tu perfil.
   - Lo descomprime usando la contraseña configurada en `.env`.
   - Reemplaza los archivos de la aplicación (`dist/`, `index.js`, etc.).
   - Reinicia automáticamente con la nueva versión.
4. Si no hay actualización o si ocurre algún error, la aplicación continúa normalmente con la versión actual.

### Verificar la versión

Al iniciar, la consola muestra la versión actual:

```
Siebel Helpers v1.2.0 (full)
Listening on http://localhost:23443
```

También puedes consultar la versión desde el navegador:

```
http://localhost:23443/api/version
```

### Desactivar auto-actualización

Si deseas desactivar el chequeo automático, agrega en tu `.env`:

```
UPDATE_DISABLED=true
```

---

## Estructura de archivos

Después de instalar, la carpeta debería verse así:

```
siebel-helpers/
├── index.js          ← Servidor Express
├── updater.js        ← Módulo de auto-actualización
├── package.json      ← Dependencias del servidor
├── .npmrc            ← Configuración de seguridad npm
├── .env.example      ← Ejemplo de configuración
├── .env              ← Tu configuración local (crear manualmente)
├── version.json      ← Versión y perfil actual
├── profile.json      ← Metadatos del build
├── dist/             ← Frontend compilado (Vue)
└── node_modules/     ← Dependencias (después de npm install)
```

---

## Solución de problemas

**La app no inicia / "Cannot find module"**
→ Ejecuta `npm install --production` en la carpeta de instalación.

**"No version.json found — skipping update check"**
→ Normal en desarrollo. En producción, `version.json` se genera automáticamente al empaquetar.

**"7-Zip is required to extract password-protected updates on Windows"**
→ Instala 7-Zip desde [https://7-zip.org](https://7-zip.org). Se detecta automáticamente.

**El auto-update descarga pero falla al extraer**
→ Verifica que `UPDATE_ZIP_PASSWORD` en `.env` sea correcto.

**No se actualiza automáticamente**
→ Verifica que la máquina tenga acceso a internet. Prueba acceder a `https://api.github.com` desde un navegador. Si no hay acceso, usa la actualización manual.

**El puerto 23443 está ocupado**
→ Cambia el puerto con la variable `PORT` en `.env` o como variable de entorno.
