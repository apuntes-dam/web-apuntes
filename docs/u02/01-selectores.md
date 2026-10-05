# 2.1 Cómo añadir CSS y selectores

## Una regla CSS

```css
selector {
  propiedad: valor;
  otra-propiedad: valor;
}
```

El **selector** dice a qué elementos se aplica; dentro de las llaves van las **declaraciones** (`propiedad: valor;`).

## Tres formas de añadir CSS

```html
<!-- 1. Archivo externo (la recomendada) -->
<link rel="stylesheet" href="estilos.css">

<!-- 2. Bloque dentro del HTML -->
<style> p { color: blue; } </style>

<!-- 3. En línea (evítala) -->
<p style="color: blue;">Texto</p>
```

## Selectores básicos

<div class="demo" data-alto="15rem" data-code="&lt;style&gt;&#10;  /* por elemento */&#10;  p { font-family: sans-serif; }&#10;  /* por clase (empieza por punto) */&#10;  .aviso { background: #fff3cd; padding: 8px; }&#10;  /* por id (empieza por almohadilla) */&#10;  #titulo { color: crimson; }&#10;  /* varios a la vez */&#10;  h2, h3 { color: darkslateblue; }&#10;&lt;/style&gt;&#10;&lt;h1 id=&quot;titulo&quot;&gt;Título con id&lt;/h1&gt;&#10;&lt;h2&gt;Un h2&lt;/h2&gt;&#10;&lt;p&gt;Párrafo normal.&lt;/p&gt;&#10;&lt;p class=&quot;aviso&quot;&gt;Párrafo con clase &quot;aviso&quot;.&lt;/p&gt;&#10;&lt;p class=&quot;aviso&quot;&gt;Otro con la misma clase.&lt;/p&gt;"></div>

| Selector | Ejemplo | Elige |
|---|---|---|
| Elemento | `p` | Todos los párrafos |
| Clase | `.aviso` | Los que tienen `class="aviso"` |
| Id | `#titulo` | El que tiene `id="titulo"` |
| Agrupación | `h2, h3` | Varios selectores a la vez |
| Descendiente | `nav a` | Los `a` dentro de un `nav` |
| Hijo directo | `ul > li` | Los `li` hijos directos de un `ul` |
| Atributo | `input[type="text"]` | Los `input` de ese tipo |
| Universal | `*` | Todo |

## Pseudo-clases y pseudo-elementos

```css
a:hover { color: red; }              /* ratón encima */
li:nth-child(even) { background: #eee; }   /* filas pares */
p::first-letter { font-size: 2em; }  /* primera letra */
input:focus { outline: 2px solid blue; }
```

## Cascada y especificidad

Si dos reglas chocan, gana la **más específica**: `id` > `clase` > `elemento`. Si empatan, gana la **última** escrita. `!important` fuerza una regla, pero conviene **evitarlo**.

!!! tip "Usa clases"
    Para dar estilo, usa sobre todo **clases**: reutilizables y con especificidad moderada. Los `id` se reservan para enlaces y JavaScript.
