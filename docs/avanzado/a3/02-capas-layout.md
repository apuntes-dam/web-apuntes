# WA3.2 Capas, adaptación por contenedor y layouts avanzados

!!! info "Cómo se han comprobado los ejemplos"
    Todos los ejemplos son **editores con vista previa que se ejecutan en tu navegador**: cámbialos y mira el resultado. Los valores que se citan en el texto (tamaños, número de columnas, qué color gana) se han **comprobado en un navegador real**. Las funciones de esta unidad funcionan en los navegadores actuales; si tu proyecto debe funcionar en navegadores antiguos, mira [Can I use](https://caniuse.com/) antes de usarlas.

## La cascada, y por qué a veces ganan «los que no deben»

Cuando dos reglas chocan, el navegador decide por este orden:

1. **Origen e importancia** (estilos del navegador, del usuario, del autor; y `!important`).
2. **Capa** (`@layer`), si las hay.
3. **Especificidad** del selector.
4. **Orden**: la última gana.

En proyectos grandes (con un reset, una librería de componentes y tu propio CSS), la lucha de especificidades acaba en `!important` por todas partes. Las **capas** lo evitan: **tú decides el orden de importancia de grupos enteros de reglas**, sin importar lo específicos que sean sus selectores.

## `@layer`: capas de estilos

Se declara el **orden** de las capas (de **menos a más** prioridad) y se mete cada grupo de reglas en la suya. **Una capa posterior gana a una anterior aunque su selector sea mucho menos específico.**

<div class="demo" data-alto="8rem" data-consola="1" data-code="&lt;button id=&quot;b1&quot; class=&quot;btn&quot;&gt;Botón 1&lt;/button&gt;&#10;&lt;button id=&quot;b2&quot; class=&quot;btn&quot;&gt;Botón 2 (con regla sin capa)&lt;/button&gt;&#10;&lt;style&gt;&#10;  @layer base, componentes;                 /* orden: componentes pesa MÁS que base */&#10;&#10;  @layer base {&#10;    #b1 { background: crimson; color: white; }    /* selector con id: MUY específico... */&#10;  }&#10;  @layer componentes {&#10;    .btn { background: royalblue; color: white; } /* ...pero la capa «componentes» va después */&#10;  }&#10;&#10;  /* Los estilos SIN capa ganan a cualquier capa */&#10;  #b2 { background: seagreen; }&#10;&lt;/style&gt;&#10;&lt;script&gt;&#10;for (const id of [&quot;b1&quot;, &quot;b2&quot;]) console.log(id, getComputedStyle(document.getElementById(id)).backgroundColor);&#10;&lt;/script&gt;"></div>

`#b1` tiene un selector de id (muy específico) pero está en la capa `base`, que pierde contra `componentes`: el botón sale **azul**. El `#b2`, sin capa, gana a todo: **verde**. Un esquema habitual:

```css
@layer reset, base, componentes, utilidades;   /* de menor a mayor prioridad */
```

| Capa | Contenido | Por qué en ese orden |
|---|---|---|
| `reset` | Normalizar márgenes, `box-sizing`… | Lo más fácil de pisar |
| `base` | Estilos de `body`, `a`, `h1`… | Valores por defecto |
| `componentes` | Botones, tarjetas, menús | El CSS de cada pieza |
| `utilidades` | `.oculto`, `.centrado` | Siempre deben ganar |

Además, una librería de terceros que cargues **dentro de una capa** (`@import url(...) layer(libreria);`) nunca pisará tu CSS, por muy específico que sea el suyo.

!!! info "`!important` en capas va al revés"
    Con `!important` el orden de las capas se **invierte**: gana la capa **anterior**. Es una función pensada para que un reset o una regla de accesibilidad se pueda proteger. Si no entiendes por qué algo no cambia, mira las herramientas del navegador: indican la capa de cada regla.

## Consultas de contenedor

Una consulta de medios (`@media`) mira el **tamaño de la ventana**. Pero un componente (una tarjeta) puede estar en una barra lateral estrecha **o** en la zona principal ancha **con la misma ventana**. Las **consultas de contenedor** miran **el espacio que tiene el componente**, así que la tarjeta se adapta a donde está, no a la pantalla.

Hay que hacer dos cosas: declarar **quién es el contenedor** (`container-type`) y escribir la consulta con `@container`.

<div class="demo" data-alto="15rem" data-code="&lt;p style=&quot;margin:0 0 .5rem&quot;&gt;Arrastra la esquina inferior derecha de la caja gris para cambiar su ancho ↘&lt;/p&gt;&#10;&lt;div class=&quot;caja&quot;&gt;&#10;  &lt;article class=&quot;tarjeta&quot;&gt;&#10;    &lt;div class=&quot;foto&quot;&gt;🖼&lt;/div&gt;&#10;    &lt;div class=&quot;texto&quot;&gt;&lt;h3&gt;Tarjeta adaptable&lt;/h3&gt;&lt;p&gt;Pasa a verse de lado cuando hay sitio.&lt;/p&gt;&lt;/div&gt;&#10;  &lt;/article&gt;&#10;&lt;/div&gt;&#10;&lt;style&gt;&#10;  .caja { resize: horizontal; overflow: auto; width: 20rem; max-width: 100%; border: 2px dashed #94a3b8; padding: .5rem;&#10;          container-type: inline-size; container-name: caja; }          /* esta caja es el contenedor */&#10;  .tarjeta { display: grid; gap: .5rem; background: #e0f2fe; padding: .5rem; border-radius: 8px; }&#10;  .foto { font-size: 2.5rem; text-align: center; background: #bae6fd; border-radius: 8px; }&#10;  h3 { margin: 0 } p { margin: 0 }&#10;&#10;  @container caja (min-width: 28rem) {         /* se aplica cuando EL CONTENEDOR mide 28 rem o más */&#10;    .tarjeta { grid-template-columns: 8rem 1fr; align-items: center; }&#10;  }&#10;&lt;/style&gt;"></div>

Al ensanchar la caja por encima de 28 rem la tarjeta pasa de **columna** a **fila**, **sin que cambie la ventana**. Dos detalles:

* `container-type: inline-size` hace que el elemento **controle su propio ancho** (la consulta mira solo su anchura).
* Los estilos de dentro **no pueden cambiar el propio contenedor** con la consulta (sería un círculo), solo a sus descendientes.

Con contenedores también hay **unidades relativas a su tamaño**: `cqw` (1 % del ancho del contenedor), `cqi`, `cqh`.

## Grid avanzado

### Columnas que se adaptan solas

La combinación **`repeat(auto-fit, minmax(…, 1fr))`** crea tantas columnas como quepan, y **todas del mismo ancho**, sin una sola consulta de medios:

<div class="demo" data-alto="14rem" data-code="&lt;div class=&quot;rejilla&quot;&gt;&#10;  &lt;div&gt;1&lt;/div&gt;&lt;div&gt;2&lt;/div&gt;&lt;div&gt;3&lt;/div&gt;&lt;div&gt;4&lt;/div&gt;&lt;div&gt;5&lt;/div&gt;&lt;div&gt;6&lt;/div&gt;&#10;&lt;/div&gt;&#10;&lt;p id=&quot;info&quot; style=&quot;font:14px monospace&quot;&gt;&lt;/p&gt;&#10;&lt;style&gt;&#10;  .rejilla { display: grid; gap: .5rem;&#10;             grid-template-columns: repeat(auto-fit, minmax(min(100%, 9rem), 1fr)); }   /* tantas como quepan, de 9 rem como mínimo */&#10;  .rejilla div { background: #fde68a; padding: 1rem; text-align: center; border-radius: 8px; }&#10;&lt;/style&gt;&#10;&lt;script&gt;&#10;const columnas = () =&gt; getComputedStyle(document.querySelector(&quot;.rejilla&quot;)).gridTemplateColumns.split(&quot; &quot;).length;&#10;document.getElementById(&quot;info&quot;).textContent = &quot;ancho de la ventana &quot; + innerWidth + &quot; px → &quot; + columnas() + &quot; columna(s)&quot;;&#10;addEventListener(&quot;resize&quot;, () =&gt; document.getElementById(&quot;info&quot;).textContent = &quot;ancho de la ventana &quot; + innerWidth + &quot; px → &quot; + columnas() + &quot; columna(s)&quot;);&#10;&lt;/script&gt;"></div>

`minmax(9rem, 1fr)` quiere decir «cada columna mide **al menos 9 rem** y se reparte lo que sobra». `auto-fit` crea las columnas que quepan. El `min(100%, 9rem)` evita que, en pantallas más estrechas que 9 rem, la columna se desborde.

| | `auto-fit` | `auto-fill` |
|---|---|---|
| Con **pocos** elementos | Las columnas **se estiran** para ocupar todo el ancho | Se **conservan** columnas vacías |

### `subgrid`: alinear el interior de tarjetas distintas

Un problema clásico: tres tarjetas con títulos de **distinta longitud**; el texto y el pie de cada una quedan **desalineados** entre sí. Con **`subgrid`**, las tarjetas **comparten las filas** de la rejilla padre:

<div class="demo" data-alto="12rem" data-code="&lt;div class=&quot;rejilla&quot;&gt;&#10;  &lt;article class=&quot;t&quot;&gt;&lt;h3&gt;Corto&lt;/h3&gt;&lt;p&gt;Texto breve.&lt;/p&gt;&lt;footer&gt;Ver más&lt;/footer&gt;&lt;/article&gt;&#10;  &lt;article class=&quot;t&quot;&gt;&lt;h3&gt;Un título bastante más largo que ocupa varias líneas&lt;/h3&gt;&lt;p&gt;Texto.&lt;/p&gt;&lt;footer&gt;Ver más&lt;/footer&gt;&lt;/article&gt;&#10;  &lt;article class=&quot;t&quot;&gt;&lt;h3&gt;Medio&lt;/h3&gt;&lt;p&gt;Un texto algo más largo para esta tarjeta de ejemplo, que ocupa más.&lt;/p&gt;&lt;footer&gt;Ver más&lt;/footer&gt;&lt;/article&gt;&#10;&lt;/div&gt;&#10;&lt;style&gt;&#10;  .rejilla { display: grid; gap: .75rem; grid-template-columns: repeat(3, 1fr); }&#10;  .t { display: grid; grid-row: span 3;                 /* cada tarjeta ocupa 3 filas de la rejilla padre... */&#10;       grid-template-rows: subgrid;                     /* ...y las COMPARTE con las demás */&#10;       gap: .25rem; background: #e0f2fe; padding: .5rem; border-radius: 8px; }&#10;  .t h3, .t p { margin: 0 } footer { font-weight: bold; color: #0369a1 }&#10;&lt;/style&gt;"></div>

Sin `subgrid`, cada tarjeta calcularía sus filas por su cuenta. Con él, **«Ver más» queda a la misma altura en las tres**, aunque los títulos tengan distinta longitud.

## Más herramientas útiles

| Propiedad | Para qué |
|---|---|
| `aspect-ratio: 16 / 9` | Mantener la **proporción** de una caja sin trucos de `padding` |
| `object-fit: cover` | Una imagen que **llena** su caja recortándose, sin deformarse |
| `position: sticky; top: 0` | Un elemento que se **queda pegado** al hacer scroll (cabeceras de tabla, menús) |
| `scroll-snap-type` | Carruseles que **se detienen** en cada elemento |
| `text-wrap: balance` | Reparte el texto de un título en líneas **equilibradas** |
| `color-mix(in srgb, red 30%, white)` | **Mezclar colores** en CSS (tonos de un color de marca) |
| `@supports (propiedad: valor)` | Usar algo solo **si el navegador lo entiende** |
| `gap` | Espacio entre elementos en Flexbox y Grid (¡sin márgenes!) |

<div class="demo" data-alto="11rem" data-consola="1" data-code="&lt;div style=&quot;display:flex; gap:.75rem; flex-wrap:wrap; align-items:flex-start&quot;&gt;&#10;  &lt;div class=&quot;a&quot; style=&quot;aspect-ratio:16/9; width:11rem; background:#bfdbfe; display:grid; place-items:center&quot;&gt;16 / 9&lt;/div&gt;&#10;  &lt;div class=&quot;a&quot; style=&quot;aspect-ratio:1; width:6rem; background:#fecaca; display:grid; place-items:center&quot;&gt;1 / 1&lt;/div&gt;&#10;  &lt;div class=&quot;mezcla&quot; style=&quot;padding:.75rem; color:white&quot;&gt;color-mix&lt;/div&gt;&#10;&lt;/div&gt;&#10;&lt;style&gt;&#10;  .mezcla { background: color-mix(in srgb, royalblue 60%, crimson); }&#10;  @supports not (aspect-ratio: 1) { .a { height: 6rem } }       /* alternativa para navegadores muy antiguos */&#10;&lt;/style&gt;&#10;&lt;script&gt;&#10;for (const c of document.querySelectorAll(&quot;.a&quot;)) console.log(&quot;caja&quot;, c.textContent, &quot;-&gt;&quot;, Math.round(c.offsetWidth), &quot;x&quot;, Math.round(c.offsetHeight), &quot;px&quot;);&#10;&lt;/script&gt;"></div>

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| `!important` por todas partes para ganar a una librería | `@layer`: la librería en una capa anterior |
| `@media` para algo que depende del espacio del componente, no de la pantalla | `@container` |
| Olvidar `container-type` y preguntarse por qué la consulta no hace nada | El contenedor **debe** declararlo |
| `auto-fill` cuando se quieren columnas que llenen el ancho | `auto-fit` |
| `minmax(12rem, 1fr)` en una pantalla de 10 rem (se desborda) | `minmax(min(100%, 12rem), 1fr)` |
| Alinear el interior de tarjetas con alturas fijas | `subgrid` |
| `padding-top: 56%` para fijar una proporción | `aspect-ratio` |

## Para practicar

Los ejercicios WA3.4 a WA3.6 de [WA3 · Ejercicios](ejercicios.md) practican `@layer`, consultas de contenedor y rejillas adaptables.
