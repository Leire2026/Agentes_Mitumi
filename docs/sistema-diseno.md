# Sistema de Diseño — MITÜMI Backstage

> Documentación técnica del Design System del proyecto MITÜMI Backstage Frontend.

---

## Índice

1. [Arquitectura de archivos](#arquitectura-de-archivos)
2. [Punto de entrada — style.scss](#punto-de-entrada--stylescss)
3. [Reset — _reset.scss](#reset--_resetscss)
4. [Variables — _variables.scss](#variables--_variablesscss)
5. [Mixins — _mixins.scss](#mixins--_mixinsscss)
6. [Tipografía — _fonts.scss](#tipografía--_fontsscss)
7. [Estructura — _estructura.scss](#estructura--_estructurascss)
8. [Elementos — _elements.scss](#elementos--_elementsscss)
9. [Partials de componentes](#partials-de-componentes)
10. [Convenciones BEM](#convenciones-bem)

---

## Arquitectura de archivos

```
src/
├── styles/                         ← Sistema de diseño global
│   ├── style.scss                  ← Punto de entrada (forward)
│   ├── _reset.scss                 ← Normalización base
│   ├── _variables.scss             ← Design tokens
│   ├── _mixins.scss                ← Utilidades reutilizables
│   ├── _fonts.scss                 ← Estilos tipográficos
│   ├── _estructura.scss            ← Layout global y contenedores
│   └── _elements.scss              ← Componentes UI base (btn, input, card)
│
└── components/
    └── partials/                   ← Estilos por componente
        ├── _header.scss
        ├── _navbar.scss
        ├── _footer.scss
        ├── _agente.scss
        ├── _cliente.scss
        ├── _presupuestos.scss
        ├── _presupuestoForm.scss
        ├── _concursos.scss
        └── ... (un archivo por componente)
```

Los archivos de la carpeta `styles/` son **globales**: se aplican a toda la aplicación. Los archivos de `partials/` son **locales**: cada componente importa solo los que necesita.

---

## Punto de entrada — `style.scss`

```scss
@forward 'fonts';
@forward 'variables';
@forward 'mixins';
@forward 'reset';
@forward 'estructura';
@forward 'elements';
```

Este archivo actúa como barril del sistema de diseño. Usa `@forward` (no `@use`) para re-exportar todos los módulos globales. Se importa una única vez en `main.jsx` o `App.jsx`.

---

## Reset — `_reset.scss`

Normaliza los estilos por defecto del navegador para garantizar consistencia entre entornos.

**Qué hace:**
- Aplica `box-sizing: border-box` a todos los elementos.
- Elimina márgenes y paddings por defecto.
- Establece `scroll-behavior: smooth` y `-webkit-font-smoothing: antialiased`.
- Normaliza imágenes (`max-width: 100%`, `display: block`).
- Elimina estilos de botones, listas, enlaces y campos de formulario del navegador.
- Aplica `font-family: inherit` a inputs, textareas y selects.

```scss
*,
*::before,
*::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
```

---

## Variables — `_variables.scss`

El núcleo del sistema de diseño. Define todos los **design tokens** del proyecto como variables SCSS.

### Paleta de colores

| Variable | Valor | Uso |
|---|---|---|
| `$color-primary` | `#2B7A8E` | Botones primarios, bordes activos, foco de inputs |
| `$color-accent` | `#00B4C8` | Texto "BACKSTAGE", detalles de marca |
| `$color-dot` | `#5AC85A` | Puntos verdes del logotipo, estados aprobados |
| `$color-text` | `#1A1A1A` | Texto principal |
| `$color-text-muted` | `#888888` | Textos secundarios y etiquetas |
| `$color-border` | `#D0D0D0` | Bordes de inputs y separadores |
| `$color-bg` | `#FFFFFF` | Fondo general |
| `$color-footer-bg` | `#F5F5F5` | Fondo del footer |
| `$color-error` | `#D32F2F` | Mensajes de error, validaciones |
| `$color-logout` | `#EA4452` | Botón de logout |
| `$color-white` | `#ffffff` | Blanco puro |

**Escala de grises** (de más oscuro a más claro):

| Variable | Valor |
|---|---|
| `$color-black-90` | `#191919` |
| `$color-black-85` | `#262626` |
| `$color-black-70` | `#4c4c4c` |
| `$color-black-55` | `#737373` |
| `$color-black-40` | `#999` |
| `$color-black-25` | `#bfbfbf` |
| `$color-black-10` | `#e5e5e5` |
| `$color-black-5` | `#f2f2f2` |

### Tipografía

| Variable | Valor | Uso |
|---|---|---|
| `$font-primary` | `'Montserrat', sans-serif` | Títulos, botones, navegación |
| `$font-secondary` | `'Lato', sans-serif` | Texto corrido, párrafos |
| `$font-menu` | `'Maven Pro', sans-serif` | Menú de navegación |

**Pesos tipográficos:**

| Variable | Valor |
|---|---|
| `$fw-light` | `300` |
| `$fw-regular` | `400` |
| `$fw-medium` | `500` |
| `$fw-semibold` | `600` |
| `$fw-bold` | `700` |

**Line heights:**

| Variable | Valor | Uso |
|---|---|---|
| `$lh-normal` | `1.4` | Texto corrido |
| `$lh-titulo` | `1.1` | Títulos y headings |

**Escala de tamaños de texto:**

| Variable | rem | px equivalente | Uso |
|---|---|---|---|
| `$txt-xs` | `0.75rem` | 12px | Labels pequeños, metadatos |
| `$txt-sm` | `0.8125rem` | 13px | Texto secundario, badges |
| `$txt-base` | `0.875rem` | 14px | Texto base de la interfaz |
| `$txt-md` | `0.9375rem` | 15px | Variante media |
| `$txt-lg` | `1rem` | 16px | Texto grande |
| `$txt-xl` | `1.4rem` | ~22px | Subtítulos |
| `$txt-title` | `1.5rem` | ~24px | Títulos de sección |
| `$txt-xxl` | `1.8rem` | ~29px | Títulos principales |

### Espaciado

El espaciado sigue una escala predefinida para garantizar consistencia visual. Todos los paddings, margins y gaps deben usar estas variables.

| Variable | Valor |
|---|---|
| `$sp-4` | `4px` |
| `$sp-8` | `8px` |
| `$sp-12` | `12px` |
| `$sp-16` | `16px` |
| `$sp-20` | `20px` |
| `$sp-24` | `24px` |
| `$sp-32` | `32px` |
| `$sp-48` | `48px` |
| `$sp-64` | `64px` |
| `$sp-96` | `96px` |
| `$sp-128` | `128px` |

### Border radius

| Variable | Valor | Uso |
|---|---|---|
| `$radius-sm` | `4px` | Badges, tags pequeños |
| `$radius-md` | `6px` | Inputs, botones |
| `$radius-lg` | `12px` | Cards, modales, contenedores |

### Breakpoints

| Variable | Valor |
|---|---|
| `$bp-480` | `480px` |
| `$bp-768` | `768px` |
| `$bp-990` | `990px` |
| `$bp-1200` | `1200px` |

---

## Mixins — `_mixins.scss`

Colección de utilidades reutilizables. Reducen la repetición de código y centralizan patrones comunes de layout.

### Flex

```scss
@mixin flex($direction: row, $justify: flex-start, $align: stretch)
```

Shortcut configurable para `display: flex`. Parámetros con valores por defecto.

**Ejemplo de uso:**
```scss
@include flex(column, flex-start, stretch);
@include flex(row, space-between, center);
```

---

```scss
@mixin flex-center
```
Centra el contenido tanto horizontal como verticalmente.

```scss
@mixin flex-between
```
Distribuye los elementos con `justify-content: space-between` y los alinea al centro verticalmente. Se usa en headers y barras de navegación.

---

### Grid

```scss
@mixin grid
```
Grid de una columna con `gap: $sp-24`. Base para ampliar con breakpoints.

---

### Breakpoints

Todos los breakpoints se invocan como mixins. Los estilos dentro se aplican a partir del ancho indicado.

```scss
@mixin bp-768   // ≥ 768px  — tablet
@mixin bp-990   // ≥ 990px  — tablet grande / escritorio pequeño
@mixin bp-1200  // ≥ 1200px — escritorio
```

**Ejemplo de uso:**
```scss
.mi-grid {
  grid-template-columns: 1fr;           // móvil: 1 columna

  @include bp-768 {
    grid-template-columns: repeat(2, 1fr); // tablet: 2 columnas
  }

  @include bp-1200 {
    grid-template-columns: repeat(3, 1fr); // escritorio: 3 columnas
  }
}
```

---

### Tipografía

```scss
@mixin truncate
```
Corta el texto con puntos suspensivos cuando supera el ancho disponible.

---

## Tipografía — `_fonts.scss`

Define los estilos tipográficos base para los elementos HTML estándar. Estos estilos se aplican globalmente y no necesitan clases adicionales.

**Headings (h1–h6):** fuente primaria (Montserrat), `$fw-bold`, color `$color-text`, sin margen.

| Etiqueta | Tamaño |
|---|---|
| `h1` | `$txt-title` (1.5rem) |
| `h2` | `1.375rem` |
| `h3` | `1.125rem` |

**Párrafos (`p`):** fuente secundaria (Lato), `$txt-base`, `line-height: 1.5`.

**Enlaces (`a`):** heredan color del padre, sin subrayado.

**Small:** `$txt-xs`, color `$color-text-muted`.

---

## Estructura — `_estructura.scss`

Define el layout global de la aplicación y los contenedores de página.

### `#root`

```scss
#root {
  min-height: 100svh;
  display: flex;
  flex-direction: column;
}
```

Garantiza que la app ocupe al menos el 100% del viewport y permite que el footer quede siempre al pie de la página con `main { flex: 1 }`.

### `.container`

Contenedor de ancho máximo responsivo, centrado horizontalmente. Se usa en el `<main>` de cada página.

| Breakpoint | Max-width | Padding |
|---|---|---|
| Móvil (base) | 100% | `$sp-24` |
| ≥ 768px | 768px | `$sp-24` |
| ≥ 990px | 990px | `$sp-32` |
| ≥ 1200px | 1200px | `$sp-48` |

### `header.titlePage`

Cabecera de sección interior. Fondo `$color-primary`, texto blanco en mayúsculas. Distribuye título (izquierda) y botón de acción (derecha) con `flex-between`.

| Breakpoint | Padding horizontal |
|---|---|
| Móvil | `$sp-16` |
| ≥ 768px | `$sp-24` |
| ≥ 990px | `$sp-48` |
| ≥ 1200px | `$sp-64` |

**Uso:**
```jsx
<header className="titlePage">
  <h1>Nombre de sección</h1>
  <button className="btn btn--anadir">+ Añadir</button>
</header>
```

### `.gridClientes`

Grid responsivo para listados de cards.

| Breakpoint | Columnas |
|---|---|
| Móvil | 1 |
| ≥ 768px | 2 |
| ≥ 990px | 3 |
| ≥ 1200px | 4 |

---

## Elementos — `_elements.scss`

Componentes UI atómicos reutilizables. Son las piezas base del sistema.

### Botones — `.btn`

Base compartida para todos los botones: `inline-flex`, centrado, padding `$sp-8 $sp-16`, `$radius-md`, fuente Montserrat medium, transición 0.2s.

| Modificador | Descripción |
|---|---|
| `.btn--primary` | Fondo `$color-primary`, texto blanco. Hover: oscurece 8%. |
| `.btn--outline` | Borde y texto `$color-primary`, fondo transparente. Hover: rellena de primary. |
| `.btn--anadir` | Fondo blanco, texto primary. Hover: rellena de primary. |
| `.btn--logout` | Texto `$color-logout` (#EA4452), borde gris. Hover: fondo logout. |
| `.btn--sm` | Tamaño reducido: `$txt-xs`, padding `$sp-4 $sp-12`. |
| `:disabled` | Opacidad 50%, cursor not-allowed. Se aplica a todos los modificadores. |

**Ejemplo:**
```jsx
<button className="btn btn--primary">Guardar</button>
<button className="btn btn--outline">Cancelar</button>
<button className="btn btn--logout">Logout</button>
```

### Inputs — `.input`

Campo de texto estándar. Ancho 100%, padding `$sp-12 $sp-16`, borde `$color-border`, fondo semitransparente `$color-black-5`.

| Modificador | Descripción |
|---|---|
| `:focus` | Borde cambia a `$color-primary`. |
| `.input--error` | Borde `$color-error`. |
| `:disabled` | Fondo gris, cursor not-allowed. |

**Ejemplo:**
```jsx
<input className="input" type="text" placeholder="Escribe aquí..." />
<input className="input input--error" type="text" />
```

### Cards — `.card`

Contenedor genérico con fondo blanco, borde `$color-border`, `$radius-lg` y padding `$sp-16`. Flex column con gap `$sp-8`.

---

## Partials de componentes

Cada componente con estilos propios tiene su archivo en `src/components/partials/`. Siguen la convención `_nombreComponente.scss`.

**Regla de importación:** cada partial debe declarar sus propios `@use` al inicio. No heredan los imports globales automáticamente.

```scss
@use '../../styles/variables' as *;
@use '../../styles/mixins' as *;
```

### Listado de partials

| Archivo | Componente |
|---|---|
| `_header.scss` | Header global con logo, logout y hamburguesa |
| `_navbar.scss` | Menú de navegación (hamburguesa en móvil, horizontal en escritorio) |
| `_footer.scss` | Footer global |
| `_agente.scss` | Página de agente de consultas |
| `_cliente.scss` | Listado de clientes |
| `_presupuestos.scss` | Cards y grid de presupuestos |
| `_presupuestoForm.scss` | Formulario de creación/edición de presupuesto |
| `_concursos.scss` | Cards de concursos públicos |
| `_eventoCard.scss` | Card individual de evento |
| `_eventoDetail.scss` | Vista de detalle de evento |
| `_eventoInfo.scss` | Información de evento |
| `_eventos.scss` | Listado de eventos |
| `_formEvento.scss` | Formulario de evento |
| `_facturacion.scss` | Sección de facturación |
| `_datosPonencia.scss` | Datos de una ponencia |
| `_datosPonente.scss` | Datos de un ponente |
| `_ponente.scss` | Vista de ponente |
| `_ponenteCard.scss` | Card de ponente |
| `_headerEvento.scss` | Header específico de la vista de evento |
| `_navbarInterno.scss` | Navegación interna de la vista de evento |
| `_lugarDefinitivo.scss` | Sección de lugar del evento |
| `_serviciosIncluidos.scss` | Servicios incluidos en el evento |
| `_seccionDatosEvento.scss` | Sección de datos generales del evento |
| `_nuevaPonencia.scss` | Formulario de nueva ponencia |
| `_opcionesAgente.scss` | Opciones del agente de consultas |
| `_fileUpload.scss` | Componente de subida de archivos |

---

## Convenciones BEM

**Ejemplos reales del proyecto:**

```scss
.presupuesto-card              // bloque
.presupuesto-card__row         // elemento
.presupuesto-card__value       // elemento
.presupuesto-card__value--aprobado  // elemento con modificador
.presupuesto-card__actions     // elemento

.btn                           // bloque
.btn--primary                  // modificador
.btn--logout                   // modificador
.btn--sm                       // modificador

.main-navbar                   // bloque
.main-navbar--open             // modificador (estado activo del menú)
```

---

*Documentación generada para MITÜMI Backstage Frontend — Julio 2026*
