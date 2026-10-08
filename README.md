# Tu Soporte Dev — Landing page

Sitio estático de [tusoportedev.dev](https://tusoportedev.dev).  
Stack: HTML y CSS puros, sin framework, sin build step.  
Despliegue: Cloudflare Pages.

---

## Marcadores pendientes de reemplazar

Antes de desplegar, busca y reemplaza estos marcadores en `public/index.html`:

| Marcador | Qué poner |
|---|---|
| `[TU NOMBRE]` | Tu nombre completo o como quieras que te llamen |
| `[NÚMERO WHATSAPP]` | Número con código de país, sin espacios ni `+` (ej: `573001234567`) |
| `[FOTO]` | Ver instrucciones en la sección "Reemplazar la foto" más abajo |
| `[ENLACE FACEBOOK]` | URL completa de tu página de Facebook |

Aparecen en los botones de WhatsApp (3 lugares), la sección "Sobre mí" y el botón de Facebook.

---

## Fuentes — descarga requerida

Las fuentes están declaradas con `@font-face` pero los archivos `.woff2` **no están incluidos** en el repositorio porque pesan varios MB. Descárgalos y colócalos en `public/fonts/`:

### Space Grotesk
1. Ve a [fontsource.org/fonts/space-grotesk](https://fontsource.org/fonts/space-grotesk) → Download
2. Del ZIP, extrae del directorio `files/`:
   - `space-grotesk-latin-500-normal.woff2` → renómbralo a `SpaceGrotesk-Medium.woff2`
   - `space-grotesk-latin-700-normal.woff2` → renómbralo a `SpaceGrotesk-Bold.woff2`

### Inter
1. Ve a [fontsource.org/fonts/inter](https://fontsource.org/fonts/inter) → Download
2. Del ZIP, extrae:
   - `inter-latin-400-normal.woff2` → renómbralo a `Inter-Regular.woff2`
   - `inter-latin-600-normal.woff2` → renómbralo a `Inter-SemiBold.woff2`

### JetBrains Mono
1. Ve a [fontsource.org/fonts/jetbrains-mono](https://fontsource.org/fonts/jetbrains-mono) → Download
2. Del ZIP, extrae:
   - `jetbrains-mono-latin-500-normal.woff2` → renómbralo a `JetBrainsMono-Medium.woff2`

El resultado debe ser:
```
public/fonts/
  SpaceGrotesk-Medium.woff2
  SpaceGrotesk-Bold.woff2
  Inter-Regular.woff2
  Inter-SemiBold.woff2
  JetBrainsMono-Medium.woff2
```

> **Nota:** hasta que descargues las fuentes, el navegador usa las fuentes del sistema (Segoe UI en Windows, San Francisco en Mac). La página se ve bien de todas formas.

---

## Imagen Open Graph

El archivo `public/og-image.svg` está listo. Para convertirlo a `og-image.png` (1200 × 630):

**Opción A — Inkscape (gratis):**
```
inkscape public/og-image.svg --export-type=png --export-filename=public/og-image.png --export-width=1200 --export-height=630
```

**Opción B — Browser:**
1. Abre `og-image.svg` en Chrome o Firefox
2. Zoom al 100 %, captura de pantalla de 1200 × 630 px

**Opción C — Online:**  
Sube `og-image.svg` a [svgtopng.com](https://svgtopng.com) o similar y descarga a 1200 × 630.

---

## Reemplazar la foto ("Sobre mí")

1. Prepara una foto tuya: cuadrada o apaisada, buena luz, fondo simple. No tiene que ser profesional.
2. Expórtala como `foto-geordy.webp` (o `.jpg`) y colócala en `public/img/`.
3. En `public/index.html`, busca el bloque `.foto-placeholder` y reemplázalo por:

```html
<img src="/img/foto-geordy.webp"
     alt="[TU NOMBRE], fundador de Tu Soporte Dev"
     width="140" height="140"
     class="foto-real">
```

4. En `public/styles.css`, añade al final:

```css
.foto-real {
  width: 140px;
  height: 140px;
  border-radius: 50%;
  object-fit: cover;
  flex-shrink: 0;
}
```

---

## 1. Probar en local

No hace falta instalar nada. Tienes dos opciones:

**Opción A — VS Code Live Server:**
1. Instala la extensión "Live Server" en VS Code
2. Abre la carpeta `landing/` en VS Code
3. Clic derecho sobre `public/index.html` → "Open with Live Server"

**Opción B — Python (si lo tienes instalado):**
```bash
cd landing/public
python -m http.server 8080
```
Luego abre `http://localhost:8080` en el navegador.

**Opción C — Node `serve` (si tienes Node):**
```bash
npx serve public -p 8080
```

> La página funciona sin servidor (abriendo el HTML directo), pero las fuentes y los SVG externos solo cargan correctamente desde un servidor local.

---

## 2. Subir el proyecto a GitHub

```bash
# Desde la carpeta landing/
git init
git add .
git commit -m "Landing inicial Tu Soporte Dev"
git branch -M main
git remote add origin https://github.com/TU_USUARIO/tusoportedev-landing.git
git push -u origin main
```

Crea el repositorio vacío primero en [github.com/new](https://github.com/new).  
El repositorio puede ser privado; Cloudflare Pages lo puede leer igual si le das permiso.

---

## 3. Desplegar en Cloudflare Pages

### Primera vez (conectando GitHub):

1. Ve a [dash.cloudflare.com](https://dash.cloudflare.com) → tu cuenta → **Workers & Pages** → **Create** → **Pages**
2. **Connect to Git** → selecciona tu repositorio `tusoportedev-landing`
3. Configuración de build:
   - **Framework preset:** None
   - **Build command:** *(dejar vacío)*
   - **Build output directory:** `public`
4. Clic en **Save and Deploy**

Cloudflare Pages desplegará automáticamente cada vez que hagas `git push` a `main`.

### Despliegue manual con Wrangler (alternativa):

```bash
npm install -g wrangler
wrangler login
wrangler pages deploy public --project-name=tusoportedev-landing
```

---

## 4. Conectar el dominio y el www

Tu dominio `tusoportedev.dev` ya está en Cloudflare. En el dashboard de Pages:

1. Ve a tu proyecto Pages → **Custom domains** → **Set up a custom domain**
2. Escribe `tusoportedev.dev` → **Continue** → **Activate domain**  
   Cloudflare actualiza el DNS automáticamente (apunta al Worker de Pages).
3. Repite con `www.tusoportedev.dev`  
   Cloudflare Pages crea automáticamente un redirect de `www` → sin `www`.

> **No toques** los registros MX, SPF ni DMARC. Cloudflare Pages solo añade/modifica los registros `A` o `CNAME` del dominio raíz y el `www`.

El HTTPS se activa solo; no necesitas hacer nada más.

---

## 5. Actualizaciones posteriores

Cualquier cambio que hagas y subas con `git push` se despliega solo en 1–2 minutos.

Para actualizar contenido sin tocar código: busca los marcadores en `index.html` con cualquier editor de texto (Bloc de notas, VS Code) y edita directamente.

---

## Estructura del proyecto

```
landing/
├── public/
│   ├── index.html          ← Toda la página
│   ├── styles.css          ← Todos los estilos
│   ├── _headers            ← Encabezados HTTP (CSP, HSTS, etc.)
│   ├── robots.txt
│   ├── sitemap.xml
│   ├── favicon.svg         ← Ícono del navegador
│   ├── apple-touch-icon.png← Pendiente: exportar desde tsd-simbolo.png (180×180)
│   ├── og-image.svg        ← Plantilla para la imagen de redes
│   ├── og-image.png        ← Pendiente: exportar desde og-image.svg (1200×630)
│   ├── fonts/              ← Pendiente: descargar woff2 (ver README)
│   └── img/                ← Logos copiados de recursos/
├── .gitignore
└── README.md
```

---

## Pendientes antes de publicar

- [ ] Descargar fuentes woff2 → `public/fonts/`
- [ ] Exportar `og-image.svg` → `og-image.png` (1200 × 630)
- [ ] Exportar `apple-touch-icon.png` desde `tsd-simbolo.png` (180 × 180)
- [ ] Reemplazar `[TU NOMBRE]`
- [ ] Reemplazar `[NÚMERO WHATSAPP]` (aparece 3 veces)
- [ ] Reemplazar `[FOTO]` con imagen real
- [ ] Reemplazar `[ENLACE FACEBOOK]`
