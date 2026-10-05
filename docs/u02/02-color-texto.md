# 2.2 Color y texto

## Formas de escribir un color

```css
color: crimson;              /* nombre */
color: #3366cc;              /* hexadecimal (rojo, verde, azul) */
color: rgb(51, 102, 204);    /* rojo, verde, azul (0-255) */
color: rgba(51, 102, 204, .5); /* con transparencia */
color: hsl(220, 60%, 50%);   /* tono, saturación, luminosidad */
```

* `color`: el color del **texto**. `background-color`: el del **fondo**.

## Propiedades de texto

<div class="demo" data-alto="15rem" data-code="&lt;style&gt;&#10;  body { font-family: Georgia, serif; }&#10;  h1 { color: #3366cc; text-align: center; letter-spacing: 2px; }&#10;  .intro { font-size: 18px; line-height: 1.6; color: #333; }&#10;  .mono { font-family: &quot;Courier New&quot;, monospace; background: #f4f4f4; }&#10;  .negrita { font-weight: bold; text-transform: uppercase; }&#10;  a { text-decoration: none; border-bottom: 1px dotted; }&#10;&lt;/style&gt;&#10;&lt;h1&gt;Un título&lt;/h1&gt;&#10;&lt;p class=&quot;intro&quot;&gt;Texto con tamaño 18px y altura de línea 1.6, que se lee mejor en bloques largos.&lt;/p&gt;&#10;&lt;p class=&quot;mono&quot;&gt;Fuente de ancho fijo&lt;/p&gt;&#10;&lt;p class=&quot;negrita&quot;&gt;negrita y mayúsculas&lt;/p&gt;&#10;&lt;p&gt;Un &lt;a href=&quot;#&quot;&gt;enlace sin subrayado&lt;/a&gt;.&lt;/p&gt;"></div>

| Propiedad | Ejemplo | Para qué |
|---|---|---|
| `font-family` | `Arial, sans-serif` | Tipo de letra (con alternativas) |
| `font-size` | `16px`, `1.2rem` | Tamaño |
| `font-weight` | `bold`, `400`, `700` | Grosor |
| `font-style` | `italic` | Cursiva |
| `line-height` | `1.5` | Altura de línea |
| `text-align` | `left`, `center`, `right`, `justify` | Alineación |
| `text-decoration` | `none`, `underline` | Subrayado |
| `text-transform` | `uppercase` | Mayúsculas/minúsculas |
| `letter-spacing` | `2px` | Espacio entre letras |

## Unidades de medida

| Unidad | Significado |
|---|---|
| `px` | Píxeles (fija) |
| `%` | Porcentaje del elemento padre |
| `em` | Relativa al tamaño de letra del elemento |
| `rem` | Relativa al tamaño de letra de la raíz (recomendada para textos) |
| `vw` / `vh` | 1 % del ancho / alto de la ventana |

!!! tip "Contraste"
    Comprueba que el texto se lee bien sobre el fondo (relación de contraste mínima 4,5:1). Hay verificadores gratuitos en línea.
