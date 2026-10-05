# 2.3 El modelo de caja

En CSS **todo elemento es una caja** con cuatro capas, de dentro hacia fuera:

```text
+-----------------------------+
|           margin            |   <- espacio exterior
|   +---------------------+   |
|   |       border        |   |   <- borde
|   |   +-------------+   |   |
|   |   |   padding   |   |   |   <- espacio interior
|   |   |  +-------+  |   |   |
|   |   |  |content|  |   |   |   <- el contenido
|   |   |  +-------+  |   |   |
|   |   +-------------+   |   |
|   +---------------------+   |
+-----------------------------+
```

<div class="demo" data-alto="14rem" data-code="&lt;style&gt;&#10;  .caja {&#10;    width: 200px;&#10;    padding: 16px;&#10;    border: 4px solid steelblue;&#10;    margin: 20px;&#10;    background: #e3f2fd;&#10;    border-radius: 12px;&#10;    box-sizing: border-box;   /* el width incluye padding y borde */&#10;  }&#10;&lt;/style&gt;&#10;&lt;div class=&quot;caja&quot;&gt;Una caja con padding, borde, margen y esquinas redondeadas.&lt;/div&gt;&#10;&lt;div class=&quot;caja&quot;&gt;Otra caja igual.&lt;/div&gt;"></div>

| Propiedad | Ejemplo |
|---|---|
| `width` / `height` | `200px`, `50%` |
| `padding` | `16px` · `10px 20px` (vertical, horizontal) · `5px 10px 5px 10px` (arriba, derecha, abajo, izquierda) |
| `border` | `2px solid black` (grosor, estilo, color) |
| `margin` | igual que `padding`; `margin: 0 auto` centra un bloque con ancho |
| `border-radius` | `8px` · `50%` (círculo) |
| `box-shadow` | `2px 2px 8px rgba(0,0,0,.3)` |

!!! warning "`box-sizing`"
    Por defecto, `width` mide solo el **contenido**: el padding y el borde se **suman**. Con `box-sizing: border-box` el ancho total incluye todo y es mucho más fácil calcular. Se suele aplicar a toda la página:
    ```css
    *, *::before, *::after { box-sizing: border-box; }
    ```

## `display` y posición

| `display` | Comportamiento |
|---|---|
| `block` | Ocupa toda la línea (`div`, `p`, `h1`) |
| `inline` | Fluye dentro del texto (`span`, `a`); ignora `width` y `height` |
| `inline-block` | En línea, pero admite tamaño |
| `none` | No se muestra |
| `flex` / `grid` | Cajas flexibles / rejilla (siguientes apartados) |

```css
.contenedor { position: relative; }
.insignia   { position: absolute; top: 0; right: 0; }   /* respecto al contenedor */
```
