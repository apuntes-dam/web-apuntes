# 1.1 Estructura de una página

Una página HTML es un archivo de texto con extensión `.html` formado por **etiquetas** (*tags*) que se escriben entre `<` y `>`. Casi todas se abren y se cierran:

```html
<etiqueta atributo="valor">contenido</etiqueta>
```

* **Elemento**: la etiqueta de apertura, su contenido y la de cierre.
* **Atributo**: información extra dentro de la etiqueta de apertura (`href`, `src`, `alt`, `id`, `class`…).
* Algunos elementos no tienen contenido y no se cierran: `<br>`, `<img>`, `<input>`, `<hr>`.

## El esqueleto mínimo

<div class="demo" data-alto="14rem" data-code="&lt;!DOCTYPE html&gt;&#10;&lt;html lang=&quot;es&quot;&gt;&#10;&lt;head&gt;&#10;  &lt;meta charset=&quot;utf-8&quot;&gt;&#10;  &lt;title&gt;Mi primera página&lt;/title&gt;&#10;&lt;/head&gt;&#10;&lt;body&gt;&#10;  &lt;h1&gt;Hola, mundo&lt;/h1&gt;&#10;  &lt;p&gt;Este es mi primer párrafo.&lt;/p&gt;&#10;&lt;/body&gt;&#10;&lt;/html&gt;"></div>

| Parte | Para qué sirve |
|---|---|
| `<!DOCTYPE html>` | Indica que es HTML5 |
| `<html lang="es">` | Raíz del documento; `lang` declara el idioma |
| `<head>` | Información **sobre** la página (no se ve): título, codificación, enlaces a CSS y JS |
| `<meta charset="utf-8">` | Para que se vean bien las tildes y la ñ |
| `<title>` | El título de la pestaña del navegador |
| `<body>` | Todo lo que se **ve** en la página |

!!! tip "Cómo trabajar"
    Escribe el código en VS Code, guárdalo como `index.html` y ábrelo en el navegador (doble clic). Tras cada cambio, guarda y pulsa **F5** para recargar.

## Comentarios y sangría

```html
<!-- Esto es un comentario: el navegador lo ignora -->
```

Sangra (indenta) los elementos anidados: es más fácil de leer y de depurar.

## Los tres archivos de una web

Una web real separa estructura, estilo y comportamiento:

```text
index.html   <- HTML (contenido)
estilos.css  <- CSS (aspecto)
script.js    <- JavaScript (comportamiento)
```

Se enlazan desde el HTML:

```html
<head>
  <link rel="stylesheet" href="estilos.css">
</head>
<body>
  ...
  <script src="script.js"></script>   <!-- justo antes de cerrar </body> -->
</body>
```

El `<script>` va al final del `body` para que el HTML ya exista cuando el JavaScript lo busque.
