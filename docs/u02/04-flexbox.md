# 2.4 Flexbox

**Flexbox** coloca los elementos en **una dimensión** (fila o columna) y reparte el espacio entre ellos. Es ideal para barras de navegación, tarjetas en fila y centrar contenido.

Se activa en el **contenedor** (el padre); sus hijos directos pasan a ser elementos flexibles.

<div class="demo" data-alto="16rem" data-code="&lt;style&gt;&#10;  .barra { display: flex; justify-content: space-between; align-items: center;&#10;           background: #263238; padding: 10px 16px; }&#10;  .barra a { color: #fff; text-decoration: none; }&#10;  .tarjetas { display: flex; flex-wrap: wrap; gap: 10px; margin-top: 12px; }&#10;  .tarjeta { flex: 1 1 100px; background: #e1f5fe; padding: 12px; border-radius: 8px; text-align: center; }&#10;&lt;/style&gt;&#10;&lt;div class=&quot;barra&quot;&gt;&#10;  &lt;strong style=&quot;color:#fff&quot;&gt;Mi web&lt;/strong&gt;&#10;  &lt;span&gt;&lt;a href=&quot;#&quot;&gt;Inicio&lt;/a&gt; &amp;nbsp; &lt;a href=&quot;#&quot;&gt;Cursos&lt;/a&gt; &amp;nbsp; &lt;a href=&quot;#&quot;&gt;Contacto&lt;/a&gt;&lt;/span&gt;&#10;&lt;/div&gt;&#10;&lt;div class=&quot;tarjetas&quot;&gt;&#10;  &lt;div class=&quot;tarjeta&quot;&gt;Uno&lt;/div&gt;&lt;div class=&quot;tarjeta&quot;&gt;Dos&lt;/div&gt;&#10;  &lt;div class=&quot;tarjeta&quot;&gt;Tres&lt;/div&gt;&lt;div class=&quot;tarjeta&quot;&gt;Cuatro&lt;/div&gt;&#10;&lt;/div&gt;"></div>

## Propiedades del contenedor

| Propiedad | Valores habituales | Efecto |
|---|---|---|
| `display` | `flex` | Activa flexbox |
| `flex-direction` | `row` (por defecto), `column` | Eje principal |
| `justify-content` | `flex-start`, `center`, `space-between`, `space-around` | Reparto **en el eje principal** |
| `align-items` | `stretch`, `center`, `flex-start` | Alineación **en el eje transversal** |
| `flex-wrap` | `nowrap`, `wrap` | Si pasan a la línea siguiente |
| `gap` | `10px` | Espacio entre elementos |

## Propiedades de los elementos

| Propiedad | Efecto |
|---|---|
| `flex: 1` | Reparte el espacio sobrante a partes iguales |
| `flex: 1 1 200px` | Puede crecer, encoger y parte de 200px |
| `align-self` | Alineación propia, distinta a la del contenedor |

!!! tip "Centrar algo, el truco clásico"
    ```css
    .centro { display: flex; justify-content: center; align-items: center; min-height: 100vh; }
    ```
