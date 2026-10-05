# 2.5 CSS Grid

**Grid** coloca los elementos en **dos dimensiones**: filas y columnas a la vez. Es ideal para la maquetación general de la página y galerías.

<div class="demo" data-alto="13rem" data-code="&lt;style&gt;&#10;  .galeria { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; }&#10;  .galeria div { background: #c8e6c9; padding: 20px; text-align: center; border-radius: 6px; }&#10;  .ancha { grid-column: span 2; background: #a5d6a7 !important; }&#10;&lt;/style&gt;&#10;&lt;div class=&quot;galeria&quot;&gt;&#10;  &lt;div&gt;1&lt;/div&gt;&lt;div&gt;2&lt;/div&gt;&lt;div&gt;3&lt;/div&gt;&#10;  &lt;div class=&quot;ancha&quot;&gt;4 (ocupa dos columnas)&lt;/div&gt;&lt;div&gt;5&lt;/div&gt;&#10;  &lt;div&gt;6&lt;/div&gt;&lt;div&gt;7&lt;/div&gt;&lt;div&gt;8&lt;/div&gt;&#10;&lt;/div&gt;"></div>

| Propiedad | Ejemplo | Efecto |
|---|---|---|
| `display` | `grid` | Activa la rejilla |
| `grid-template-columns` | `repeat(3, 1fr)` · `200px 1fr` | Define las columnas (`fr` = fracción del espacio) |
| `grid-template-rows` | `auto 1fr auto` | Define las filas |
| `gap` | `10px` | Espacio entre celdas |
| `grid-column` / `grid-row` | `span 2` · `1 / 3` | Un elemento ocupa varias celdas |

## Maquetación con áreas

<div class="demo" data-alto="16rem" data-code="&lt;style&gt;&#10;  .pagina { display: grid; gap: 8px; min-height: 220px;&#10;    grid-template-columns: 120px 1fr;&#10;    grid-template-areas:&#10;      &quot;cabecera cabecera&quot;&#10;      &quot;menu     contenido&quot;&#10;      &quot;pie      pie&quot;; }&#10;  .pagina &gt; * { padding: 10px; border-radius: 6px; }&#10;  .cabecera { grid-area: cabecera; background: #ffcc80; }&#10;  .menu     { grid-area: menu;     background: #b3e5fc; }&#10;  .contenido{ grid-area: contenido; background: #e1bee7; }&#10;  .pie      { grid-area: pie;      background: #cfd8dc; }&#10;&lt;/style&gt;&#10;&lt;div class=&quot;pagina&quot;&gt;&#10;  &lt;header class=&quot;cabecera&quot;&gt;Cabecera&lt;/header&gt;&#10;  &lt;nav class=&quot;menu&quot;&gt;Menú&lt;/nav&gt;&#10;  &lt;main class=&quot;contenido&quot;&gt;Contenido principal&lt;/main&gt;&#10;  &lt;footer class=&quot;pie&quot;&gt;Pie&lt;/footer&gt;&#10;&lt;/div&gt;"></div>

!!! note "¿Flexbox o Grid?"
    **Flexbox** para colocar elementos **en una línea** (un menú, una fila de botones). **Grid** para la **estructura de la página** o una rejilla de elementos. Se pueden combinar: un Grid para la página y Flexbox dentro de cada zona.
