# 2.6 Responsive, hover y transiciones

## Diseño adaptable (*responsive*)

Una web debe verse bien en móvil, tableta y ordenador. Dos ingredientes:

1. La etiqueta `viewport` en el `<head>`:

```html
<meta name="viewport" content="width=device-width, initial-scale=1">
```

2. Las **consultas de medios** (*media queries*): reglas que solo se aplican si se cumple una condición.

<div class="demo" data-alto="12rem" data-code="&lt;style&gt;&#10;  .fila { display: grid; grid-template-columns: repeat(3, 1fr); gap: 8px; }&#10;  .fila div { background: #ffe0b2; padding: 16px; text-align: center; border-radius: 6px; }&#10;  /* en pantallas estrechas, una sola columna */&#10;  @media (max-width: 400px) {&#10;    .fila { grid-template-columns: 1fr; }&#10;  }&#10;&lt;/style&gt;&#10;&lt;p&gt;Cambia el ancho del navegador (o prueba la versión móvil con F12) para ver cómo pasa de 3 columnas a 1.&lt;/p&gt;&#10;&lt;div class=&quot;fila&quot;&gt;&lt;div&gt;A&lt;/div&gt;&lt;div&gt;B&lt;/div&gt;&lt;div&gt;C&lt;/div&gt;&lt;/div&gt;"></div>

!!! tip "Móvil primero"
    Escribe primero el CSS para pantallas pequeñas y añade `@media (min-width: 768px) { ... }` para las grandes: suele quedar más limpio.

Otras ayudas para adaptar: `max-width: 100%` en imágenes, unidades relativas (`%`, `rem`, `vw`) y `flex-wrap`.

## Estados: `:hover`, `:focus`, `:active`

<div class="demo" data-alto="8rem" data-code="&lt;style&gt;&#10;  .boton { background: #1976d2; color: white; border: 0; padding: 10px 18px; border-radius: 6px;&#10;           cursor: pointer; transition: background .25s, transform .15s; }&#10;  .boton:hover  { background: #0d47a1; }&#10;  .boton:active { transform: scale(.95); }&#10;  .boton:focus-visible { outline: 3px solid #ffb300; }&#10;&lt;/style&gt;&#10;&lt;button class=&quot;boton&quot;&gt;Pasa el ratón por encima&lt;/button&gt;"></div>

* `:hover` – el ratón está encima. `:focus` – el elemento tiene el foco (teclado). `:active` – mientras se pulsa.
* `transition: propiedad duración;` anima el cambio suavemente.

!!! note ":hover frente a JavaScript"
    Si el cambio es **solo visual**, usa `:hover`. Reserva JavaScript para cuando haga falta lógica (unidad 3).

## Variables CSS

```css
:root { --color-principal: #1976d2; }
.boton { background: var(--color-principal); }
```

Permiten cambiar un valor en un solo sitio.

## Herramientas modernas que conviene conocer

Todas están disponibles en los navegadores actuales. Comprueba la compatibilidad en [caniuse.com](https://caniuse.com) si tu proyecto debe funcionar en navegadores antiguos.

| Herramienta | Ejemplo | Para qué sirve |
|---|---|---|
| `clamp()` | `font-size: clamp(1rem, 2.5vw, 2rem)` | Un tamaño que crece con la pantalla, pero con mínimo y máximo |
| `aspect-ratio` | `aspect-ratio: 16 / 9` | Mantiene la proporción de una caja o un vídeo |
| `prefers-color-scheme` | `@media (prefers-color-scheme: dark) { ... }` | Aplica estilos si el usuario usa modo oscuro |
| `:has()` | `.tarjeta:has(img) { padding: 0 }` | Estiliza un elemento según lo que contiene |
| Consultas de contenedor | `@container (min-width: 400px) { ... }` | Cambia el estilo según el tamaño del contenedor, no de la pantalla |
| Anidamiento | `.menu { a { color: red } }` | Escribir selectores dentro de otros, como en Sass |

```css
:root { --fondo: #ffffff; --texto: #1b1b1b; }
@media (prefers-color-scheme: dark) {
  :root { --fondo: #121212; --texto: #eeeeee; }
}
body { background: var(--fondo); color: var(--texto); }
```

Este ejemplo junta dos ideas de esta unidad: las variables CSS y una consulta de medios para ofrecer modo oscuro.
