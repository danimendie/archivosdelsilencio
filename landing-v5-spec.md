# SPEC: Los Archivos del Silencio — Landing Interactiva
## Opción B: Exploración de Archivos

---

## 1. Concept & Vision

Un usuario llega a lo que parece ser un **sistema de archivos clasificado**. No una landing page — una *interfaz de archivo reservado*. La sensación es de haber encontrado algo que no debías ver: una pantalla de terminal institucional, fría, burocrática, con documentos que parpadean en negro sobre negro.

Cada "documento" es un archivo del volumen 1. El usuario puede explorar los que quiera. Un **timer** corre en background (urgencia sutil, no agressiva). Después de explorar suficiente, el archivo le pregunta si quiere el documento completo. Todos los caminos llevan a la conversión.

**Tono:** Black Mirror meets Edgar Allan Poe. Frío, institucional, opresivo. No es una página web — es un *sistema de archivos intervenido*.

---

## 2. Design Language

### Aesthetic
- **Terminal/institucional oscura** — como el sistema de archivos de una institución olvidado en los 90s
- Fondo negro absoluto (#000000)
- Texto en verde fósfor o ámbar (como pantallas CRT antiguas)
- Scanlines sutiles (no agresivo como landing-v2 — más institucional, menos effecto de TV)
- Tipografía monospace para UI metadata; serif para excerpts de documentos reales

### Color Palette
- Background: `#000000` (negro puro)
- Primary text: `#00ff41` (verde terminal) o `#d4a574` (ámbar suave — decidir en build)
- Secondary text: `#666666` (gris medio para metadata)
- Accent/highlight: `#8b0000` (rojo oscuro — para elementos que deben notarse)
- Border/divider: `#1a1a1a` (negro muy oscuro)
- Timer: `#ff4444` (rojo urgency, discreto)

### Typography
- **UI/metadata:** `IBM Plex Mono` (sistema de archivos, timestamps, números de documento)
- **Excerpts de testimonios:** `Libre Baskerville` o `Cormorant Garamond` (contraste elegante con la frialdad de la UI)
- **Títulos/secciones:** `Playfair Display` (para el nombre del archivo cuando se abre)

### Spatial System
- Mobile-first: la UI es estrecha, como una terminal móvil
- Padding interno mínimo — todo compactado, institucional
- Máximo 320px de contenido visible en mobile (como una pantalla pequeña)
- Desktop: centrado con max-width 640px (sigue pareciendo una terminal)

### Motion Philosophy
- Scanlines animadas (CSS, muy sutil, 0.03 opacity)
- Cursor blink en elementos interactivos (como en una terminal)
- Aparición de documentos: typewriter effect (letra por letra, rápido, no lento)
- Transiciones entre estados: fade rápido (150ms), no hay nada suave aquí
- Timer: números que cambian con un flash sutil
- NO hay transiciones elegantes — todo es abrupto, como un sistema viejo

### Visual Assets
- **Sin imágenes** — solo texto, bordes, y la sensación de terminal
- Un pequeño icono de "archivo" generado con CSS o un SVG mínimo
- Números de documento estilizados como `[DOC-1947-19]`

---

## 3. Layout & Structure

### Arquitectura de página

```
┌─────────────────────────────────────┐
│  HEADER: "SISTEMA DE ARCHIVOS"      │
│  "Acceso restringido — Log 001"    │
│  Timer: [ARCHIVO SE CIERRA EN 12:47]│
├─────────────────────────────────────┤
│                                     │
│  LISTADO DE DOCUMENTOS              │
│  (los 7 testimonios)                │
│                                     │
│  [DOC-1947-01] Hermanas de...      │
│  [DOC-1947-02] La celda 19...      │
│  [DOC-1947-03] Fray Domingo...     │
│  ... (7 total)                    │
│                                     │
├─────────────────────────────────────┤
│  ÁREA DE VISOR DE DOCUMENTO         │
│  (aparece cuando hacés click)      │
│                                     │
│  [DOC-1947-XX]                    │
│  Excerpt del testimonio            │
│  (3-5 párrafos)                   │
│                                     │
│  [CERRAR]                          │
├─────────────────────────────────────┤
│  PROMPT DE CONVERSIÓN              │
│  (aparece después de 2 docs)       │
│                                     │
│  "El archivo completo requiere..." │
│  [ACCEDER AL ARCHIVO COMPLETO]     │
│                                     │
└─────────────────────────────────────┘
```

### Responsive Strategy
- Mobile: una columna, todo apilado, UI compacta
- Desktop: centrado, max-width 640px, sigue pareciendo terminal
- LA CLAVE: en desktop NO se expande a todo el ancho — sigue siendo una venta de terminal estrecha

---

## 4. Features & Interactions

### 4.1 Sistema de archivos (listado)

**Comportamiento:**
- 7 documentos listados en orden cronológico ficticio
- Cada documento muestra: código `[DOC-Año-XX]`, nombre breve, fecha ficticia
- Hover: el documento se ilumina (cambio de color sutil)
- Click: se abre el visor de documento (el documento seleccionado)
- Solo 1 documento abierto a la vez
- Documentos ya "abiertos" se marcan con un indicador

**Estados de cada documento:**
- `disponible` — no abierto aún, verde/ámbar normal
- `abierto` — clickeado, visible en visor
- `explorado` — ya visto, indicador sutil
- `bloqueado` — algunos docs tienen "nivel de acceso" y no se pueden ver completos (esto es teatro, todos llevan al mismo prompt)

### 4.2 Visor de documento

**Comportamiento:**
- Aparece abajo del listado cuando hacés click en un documento
- Muestra: código del doc, título completo, excerpt (3-5 párrafos) del testimonio
- El excerpt es genuinamente perturbador / intrigante (de los testimonios reales del ebook)
- Al final del excerpt: "[...]" con mensaje "Este documento continúa en el archivo completo"
- Botón [CERRAR] para volver al listado
- Animación: typewriter rápido para el texto (50ms por palabra, no letra por letra)

**Estados:**
- `cerrado` — no visible
- `abriendo` — animación de apertura
- `abierta` — texto visible
- `cerrando` — animación de cierre

### 4.3 Timer de urgencia

**Comportamiento:**
- Cuenta regresiva desde 15:00 minutos (o configurable)
- Se muestra en header como: `[ARCHIVO SE CIERRA EN 12:47]`
- Cuando llega a 0:00 — los documentos "se bloquean" con mensaje "Sesión expirada. El archivo se ha cerrado."
- Si el timer llega a 0, muestra un mensaje final: "Este archivo ha sido cerrado. Si deseas acceder al archivo completo, lo encontrarás en:"
- Link al ebook (purchase)
- Timer se puede reiniciar si el usuario abre otro documento (simulación de "sesión activa")

**Diseño del timer:**
- Números en rojo apagado (#ff4444)
- Formato MM:SS
- Pulso sutil cada segundo (cambio de opacity)
- NO es agresivo tipo countdown de tienda — es frío, institucional

### 4.4 Prompt de conversión

**Cuándo aparece:**
- Después de que el usuario explora 2 o más documentos
- NO aparece como popup intrusivo — se integra en el flujo
- Se muestra como un "documento más" al final del listado: `[DOC-ACCESO]`
- O aparece sutilmente debajo del visor cuando cerrás un documento

**Contenido:**
- "El archivo completo contiene 7 testimonios + documento extra"
- "Tu sesión actual solo permite fragmentos."
- "Accede al archivo completo por USD 5.90"
- Botones: [EPUB + PDF] [COMPRAR AHORA]

**Si el timer llegó a 0:**
- El prompt cambia: "Tu sesión ha expirado. El archivo completo está disponible para compra."
- Mismo precio y CTAs

### 4.5 Marcação de documentos explorados

**Comportamiento:**
- Los documentos ya abiertos tienen un indicador visual (un asterisco `*` o `[·]`)
- Si el usuario闭了一圈 sin ver nada, el timer sigue corriendo
- La cantidad de docs explorados se guarda en sessionStorage (no se pierde al navegar)

### 4.6 Estados de error y edge cases

- **Sin JavaScript:** mostrar un mensaje de "Este archivo requiere JavaScript" con copy elegante
- **Timer expirado:** experiencia alternativa donde se muestra el prompt de conversión
- **Móvil:** funcionar igual que desktop, solo más compacto

---

## 5. Component Inventory

### 5.1 Header del sistema
```
┌─────────────────────────────────────────┐
│  SISTEMA DE ARCHIVOS — ACCESO RESTRINGIDO│
│  Log 001 · Sesión activa               │
│  [ARCHIVO SE CIERRA EN 12:47]           │
└─────────────────────────────────────────┘
```
- Fondo: `#0a0a0a` (negro casi puro)
- Texto: `IBM Plex Mono`, verde/ámbar
- Timer: rojo, más grande

**Estados:** Normal, timer bajo (< 2:00 — se ilumina más), expirado (todo en rojo)

### 5.2 Item de documento (en el listado)
```
[DOC-1947-01]  Hermanas de la Vega     · 1947
```
- `IBM Plex Mono`
- Hover: cambio de color a rojo oscuro
- Click: abre visor
- Ya explorado: indicador `*` después del código

**Estados:** disponible, abierto, explorado, hover, clickeado

### 5.3 Visor de documento
```
┌─────────────────────────────────────────┐
│ [DOC-1947-01]                           │
│ Hermanas de la Vega — Testimony I       │
│ ─────────────────────────────────────── │
│                                         │
│  (Excerpt del testimonio en Cormorant)  │
│                                         │
│  [continúa...]                          │
│                                         │
│                        [CERRAR]         │
└─────────────────────────────────────────┘
```
- Fondo: `#050505` (negro profundo)
- Excerpt: `Libre Baskerville`, 1rem, interlineado generoso
- Borde izquierdo: línea roja sutil

**Estados:** cerrado, abriéndose (typewriter), abierto, cerrándose

### 5.4 Prompt de conversión
```
┌─────────────────────────────────────────┐
│ [DOC-ACCESO]                            │
│ ACCESO AL ARCHIVO COMPLETO              │
│ ─────────────────────────────────────── │
│                                         │
│ 7 testimonios completos                 │
│ 1 documento extra                      │
│ EPUB + PDF                             │
│                                         │
│     USD 5.90                           │
│                                         │
│  [ACCEDER AL ARCHIVO COMPLETO]          │
│                                         │
└─────────────────────────────────────────┘
```
- Diseño similar al visor pero con elementos de purchase
- Precio destacado
- Botón estilo terminal pero claramente clickeable

### 5.5 Mensaje de sesión expirada
```
┌─────────────────────────────────────────┐
│                                         │
│  SESIÓN EXPIRADA                        │
│  El archivo se ha cerrado por           │
│  inactividad.                           │
│                                         │
│  Si deseas acceder al archivo completo: │
│                                         │
│  [COMPRAR AHORA — USD 5.90]             │
│                                         │
└─────────────────────────────────────────┘
```

### 5.6 Disclaimer/Footer
```
Los documentos presentados en este archivo son fragmentos
de una obra de ficción. Los nombres han sido alterados.
Archivo intervenido por discreción editorial.
```

---

## 6. Technical Approach

### Stack
- HTML5 + CSS3 + Vanilla JavaScript
- No frameworks — portability total
- Google Fonts: IBM Plex Mono, Cormorant Garamond

### Arquitectura de archivos
```
los-archivos/
├── index.html          # Todo junto (simplifica deployment)
├── css/
│   └── styles.css
└── js/
    └── app.js
```

### Data model (JavaScript)
```javascript
const documentos = [
  {
    id: "DOC-1947-01",
    titulo: "Hermanas de la Vega",
    subtitulo: "Testimonio I",
    fecha: "1947",
    excerpt: "...", // 3-5 párrafos del testimonio real
    nivelAcceso: "publico" // vs "restringido" (teatro)
  },
  // ... 7 testimonios
];

let estado = {
  docsExplorados: [], // array de IDs
  docAbierto: null,
  timerStart: null,
  timerDuration: 15 * 60, // 15 minutos en segundos
  sesionActiva: true
};
```

### Timer implementation
- `setInterval` cada 1 segundo
- Se guarda `timerStart` en sessionStorage para persistencia en la sesión
- Cuando llega a 0: cambiar `sesionActiva = false`, mostrar mensaje de expiración

### Excerpts del ebook
Usar fragmentos REALES del archivo `Los_Archivos_del_Silencio_Vol1.md`. Seleccionar 3-5 párrafos intensos y autónomos por testimonio.

### sessionStorage
- `docsExplorados`: array de IDs ya abiertos
- `timerStart`: timestamp de inicio
- `sesionActiva`: boolean

### Accessibility
- Todo funciona con teclado (navegación por Tab)
- `aria-label` en elementos interactivos
- `prefers-reduced-motion`: si está activo, deshabilitar typewriter y animaciones
- Contraste suficiente (verde sobre negro = alto contraste)

---

## 7. Copy — Fragmentos de los Testimonios

(Usar тексты reales del ebook para los excerpts. Aquí van los títulos y la dirección de cada fragmento.)

1. **[DOC-1947-01] Hermanas de la Vega** — Testimonio I
   - Excerpt: la llegada de sor Inés, el silencio del convento, la primera noche

2. **[DOC-1947-02] La celda 19** — Testimonio II  
   - Excerpt: la celda que no debía asignarse, lo que encontraron en la pared

3. **[DOC-1947-03] Fray Domingo de la Trinidad** — Testimonio III
   - Excerpt: el archivero anterior, sus notas, su desaparición

4. **[DOC-1947-04] Las trece velas** — Testimonio IV
   - Excerpt: la ceremonia nocturna, el número 13 en todas partes

5. **[DOC-1947-05] El libro rojo** — Testimonio V
   - Excerpt: el libro que no estaba catalogado, los nombres codificados

6. **[DOC-1947-06] La biblioteca** — Testimonio VI
   - Excerpt: los libros que faltaban, el índice que no coincidía

7. **[DOC-1947-07] El llave de la celda 19** — Testimonio VII
   - Excerpt: el archivero nuevo, la llave que no debería existir

---

## 8. NO hacer (prohibido)

- ❌ No usar efectos de glitch fuertes tipo landing-v2
- ❌ No usar Space Mono (ya fue rechazado)
- ❌ No hacer que el timer sea agressivo tipo "¡COMPRA AHORA!"
- ❌ No revelar que es "interactive fiction" o "elige tu aventura"
- ❌ No mezclar idiomas — TODO en español
- ❌ No usar humor, emojis, o tono amigable
- ❌ No mostrar el precio antes de que el usuario explore al menos 1-2 docs
