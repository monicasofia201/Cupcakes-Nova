# Cupcakes Nova — sitio de una sola página

Sitio web de una sola página (one-page) para una pastelería de autor:
selección de productos, detalle en modal, carrito de pedido persistente y
checkout por WhatsApp, sin backend ni paso de build.

## Stack

- **HTML5 semántico** — `index.html` es solo estructura (`<header>`, `<main>`, `<section>`, `<footer>`, `<aside>`) con atributos ARIA donde corresponde.
- **CSS3 con metodología ITCSS** (custom properties como tokens, sin preprocesador ni bundler).
- **JavaScript vanilla, módulos ES6** (`import`/`export`), sin frameworks ni dependencias de `npm`.
- **PWA mínima** vía `manifest.json`.
- Sin paso de build: se sirve tal cual, como sitio estático.

## Cómo ejecutar

**Opción A — doble clic sobre `index.html`.** Funciona tal cual. `index.html`
carga `dist/app.bundle.js` (ver más abajo), un `<script>` clásico sin
`type="module"`, así que no depende de servidor.

**Opción B — servidor local** (recomendado solo si vas a editar `src/js/` y
quieres probar los módulos ES6 originales, o si vas a desplegar a un hosting
real de todas formas):

```bash
npx serve .
# o
python -m http.server 8000
```

## `src/js/` vs. `dist/app.bundle.js` — cuál se usa y por qué

`index.html` **no** carga `src/js/main.js` directamente. Carga
`dist/app.bundle.js`, una concatenación manual de todo `src/js/**/*.js` (sin
`import`/`export`, envuelta en un único IIFE). La razón: los navegadores
bloquean `<script type="module">` bajo `file://` (política CORS), y este
sitio necesita abrirse con doble clic para usuarios no técnicos.

- **`src/js/`** sigue siendo la fuente de verdad: módulos ES6 reales,
  separados por capa, con `import`/`export`. Edítalo aquí.
- **`dist/app.bundle.js`** es el artefacto que de verdad se sirve. Es
  exactamente lo que produciría un bundler real (Vite/esbuild/webpack) — aquí
  se generó a mano porque el proyecto no tiene paso de build.

**Si modificas algo en `src/js/`, tienes que reflejarlo también en
`dist/app.bundle.js`** (misma lógica, sin `import`/`export`, dentro del IIFE).
El encabezado de `dist/app.bundle.js` documenta el orden exacto de las
secciones para que sea mecánico. Si en algún momento el proyecto suma un
paso de build real, este archivo generado a mano deja de ser necesario y
`index.html` puede volver a apuntar a `src/js/main.js` con `type="module"`.

## Estructura de carpetas

```
p1/
├── index.html                      # Estructura semántica. Enlaza main.css y dist/app.bundle.js. Cero <style>/<script> embebido con lógica.
├── manifest.json                   # Metadatos PWA (nombre, colores, ícono).
├── README.md                       # Este archivo.
├── dist/
│   └── app.bundle.js               # Artefacto servido de verdad (ver sección de arriba). Generado a mano desde src/js/.
├── docs/
│   └── NOTA.md                     # Explicación funcional para el dueño del negocio (no técnica).
├── assets/
│   ├── fonts/                      # Reservada para tipografías auto-hospedadas (ver assets/fonts/README.md).
│   ├── images/                     # Reservada para fotografía/logo propios (ver assets/images/README.md).
│   └── icons/                      # Fuente canónica de los íconos SVG usados en la interfaz.
└── src/
    ├── styles/
    │   ├── base/
    │   │   ├── _reset.css          # Normalización + utilidad .visually-hidden.
    │   │   └── _typography.css     # Fuente, peso y color de texto por defecto.
    │   ├── tokens/
    │   │   └── _variables.css      # Design tokens: color, sombra, transición, tipografía, layout.
    │   ├── layout/
    │   │   └── _grid.css           # Contenedor centrado, ritmo de <section>, utilidad .fade-in.
    │   ├── components/
    │   │   ├── _buttons.css
    │   │   ├── _inputs.css
    │   │   ├── _navigation.css
    │   │   ├── _hero.css
    │   │   ├── _about.css
    │   │   ├── _card.css
    │   │   ├── _discount.css
    │   │   ├── _testimonial.css
    │   │   ├── _gallery.css
    │   │   ├── _trust.css
    │   │   ├── _newsletter.css
    │   │   ├── _footer.css
    │   │   ├── _lightbox.css
    │   │   └── _cart-drawer.css
    │   └── main.css                # Orquesta todos los @import, en orden de cascada ITCSS.
    └── js/
        ├── core/
        │   ├── config.js           # Configuración global (número de WhatsApp, claves de storage, timings).
        │   └── constants.js        # Selectores DOM, clases CSS de estado y mensajes de texto.
        ├── services/
        │   ├── cartService.js      # Lógica de negocio del carrito (estado + localStorage). Sin DOM.
        │   └── whatsappService.js  # Arma el mensaje y la URL de checkout hacia WhatsApp.
        ├── utils/
        │   ├── dom.js              # qs / qsAll / createElement — helpers puros de DOM.
        │   ├── format.js           # Formateo/parseo de moneda COP.
        │   └── validators.js       # Validación de email.
        ├── components/
        │   ├── Header.js           # Header fijo + menú móvil.
        │   ├── ScrollReveal.js     # Animación de aparición al hacer scroll (IntersectionObserver).
        │   ├── Newsletter.js       # Validación y feedback del formulario de suscripción.
        │   ├── ProductCatalog.js   # Revela productos ocultos en lotes al pulsar "Ver más productos".
        │   ├── Lightbox.js         # Modal de detalle de producto/foto.
        │   └── CartDrawer.js       # Panel lateral del carrito (única pieza que pinta cartService en el DOM).
        ├── controllers/
        │   └── uiController.js     # Bootstrap: inicializa componentes y conecta los flujos cruzados.
        └── main.js                 # Entry point real (módulos ES6). Ver nota sobre dist/app.bundle.js arriba.
```

## Decisiones de arquitectura

- **Separación por capas.** `core` no importa nada (configuración pura). `utils`
  son funciones puras sin estado ni DOM. `services` contienen lógica de
  negocio (carrito, WhatsApp) y no tocan el DOM directamente. `components`
  son las únicas piezas que leen/escriben el DOM, y `controllers/uiController.js`
  es la única capa que conoce la aplicación completa y conecta unas piezas con otras.
  Esto permite, por ejemplo, testear `cartService.js` sin un navegador.

- **`CartDrawer` como única fuente de verdad del DOM del carrito.** Tanto los
  botones "Añadir al pedido" de las tarjetas de producto como el botón
  equivalente dentro del `Lightbox` delegan en `CartDrawer.addItem()` en vez
  de manipular `cartService` por su cuenta — evita que dos piezas distintas
  dupliquen la lógica de renderizado.

- **El componente de galería/tabs del enunciado original se adaptó al dominio
  real de la app.** La plantilla de referencia (`DataTypeSelector.js`,
  `Customizer.js`, `_tabs.css`, etc.) estaba pensada para un generador de QR;
  aquí no hay pestañas ni selector de tipo de dato, así que esos archivos se
  sustituyeron por los componentes que sí existen en este dominio: navegación,
  hero, tarjeta de producto, lightbox y carrito — mismo principio de
  responsabilidad única, adaptado al problema real.

- **`@import` nativo en `main.css`, sin bundler.** El sitio no tiene paso de
  build. Con un bundler (Vite/Webpack/PostCSS) estos imports se resolverían en
  tiempo de compilación en un único archivo minificado; aquí se resuelven en
  el navegador, que es aceptable para un sitio de este tamaño.

- **Módulos ES6 en `src/js/`, pero servidos vía `dist/app.bundle.js`.** Cada
  archivo `.js` de `src/js/` es un módulo real (`import`/`export`). Cargarlos
  así con `<script type="module">` exigiría servir el sitio por HTTP, porque
  los navegadores bloquean módulos bajo `file://` — inaceptable para un sitio
  que un dueño de pastelería sin conocimientos técnicos necesita poder abrir
  con doble clic. Por eso `index.html` carga el bundle generado, no los
  módulos directamente (ver sección dedicada arriba).

- **Los productos "extra" empiezan ocultos con `display:none` (clase
  `.oculto`), no ausentes del DOM.** `ProductCatalog.js` los revela en lotes
  de `CONFIG.productsPerReveal` al pulsar "Ver más productos", en vez de
  pedirlos a un servidor (no hay backend). Como ya están en el HTML, sus
  botones "Añadir al pedido" y su lightbox quedan enganchados desde el
  arranque — no hace falta re-enganchar listeners al revelarlos.

- **Checkout por WhatsApp en vez de pasarela de pago.** No hay backend. El
  "checkout" arma un mensaje de texto con el detalle del pedido y abre
  `wa.me` con ese mensaje prellenado — el patrón estándar para pequeños
  negocios en LatAm sin plataforma de pagos propia. El bloque de confianza
  "Pago en línea 100% seguro" queda como contenido de marketing a futuro,
  cuando se integre una pasarela real.

- **Persistencia del carrito en `localStorage`**, para que el pedido
  sobreviva a un refresco de página. Todo acceso a `localStorage` está
  envuelto en `try/catch` (puede fallar en modo incógnito estricto o con
  almacenamiento lleno).

- **Fuentes vía Google Fonts CDN, no auto-hospedadas.** Ver `assets/fonts/README.md`
  para el razonamiento y cómo migrar si hace falta.

## Accesibilidad

- Landmarks semánticos: `<header>`, `<nav>`, `<main>`, `<section>`, `<footer>`, `<aside>`.
- `aria-label` / `aria-labelledby` en secciones y controles sin texto visible propio.
- `role="dialog"` + `aria-modal="true"` en el lightbox y el carrito.
- `aria-live="polite"` en el badge del carrito y en el mensaje del newsletter, para que lectores de pantalla anuncien los cambios.
- `<label>` asociado al campo de correo (oculto visualmente con `.visually-hidden`, no con `display:none`).
- Iconografía decorativa marcada con `aria-hidden="true"`.

## Pendientes antes de producción

1. Reemplazar `whatsappNumber` en **ambos** `src/js/core/config.js` y `dist/app.bundle.js` por el número real del negocio.
2. Reemplazar los enlaces `https://www.instagram.com/` (footer y sección de galería) por el perfil real.
3. Sustituir las fotografías de Pexels por fotografía propia cuando esté disponible (ver `assets/images/README.md`).
4. Exportar íconos PNG de 192×192 y 512×512 para `manifest.json` si se necesita compatibilidad total de ícono de instalación en todas las plataformas (hoy usa un SVG único, soportado en Chrome/Edge/Android).
