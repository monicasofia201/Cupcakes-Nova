# Cupcakes Nova — sitio de una sola página

Sitio web de una sola página (one-page) para una pastelería de autor:
selección de productos, detalle en modal, carrito de pedido persistente y
checkout por WhatsApp, sin backend ni paso de build.

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
