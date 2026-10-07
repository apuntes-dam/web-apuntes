# 1.3 Imágenes y listas

## Imágenes

```html
<img src="foto.jpg" alt="Descripción de la foto" width="300">
```

* `src`: ruta de la imagen (relativa o una URL).
* `alt`: **texto alternativo**, obligatorio: lo leen los lectores de pantalla y se muestra si la imagen no carga.
* `width` / `height`: tamaño en píxeles (conviene indicarlo para que la página no "salte" al cargar).
* `loading="lazy"`: el navegador descarga la imagen solo cuando está cerca de verse. Úsalo en las imágenes que están más abajo en la página, no en la primera que ve el usuario.
* `srcset` y `sizes`: ofrecen la misma imagen en varios tamaños y el navegador elige la más adecuada para cada pantalla (`<img src="foto-800.jpg" srcset="foto-400.jpg 400w, foto-800.jpg 800w" sizes="(max-width: 600px) 400px, 800px" alt="...">`).

<div class="demo" data-alto="14rem" data-code="&lt;figure&gt;&#10;  &lt;img src=&quot;data:image/svg+xml;utf8,&lt;svg xmlns=&#x27;http://www.w3.org/2000/svg&#x27; width=&#x27;160&#x27; height=&#x27;90&#x27;&gt;&lt;rect width=&#x27;160&#x27; height=&#x27;90&#x27; fill=&#x27;%2342a5f5&#x27;/&gt;&lt;circle cx=&#x27;80&#x27; cy=&#x27;45&#x27; r=&#x27;28&#x27; fill=&#x27;%23ffd54f&#x27;/&gt;&lt;/svg&gt;&quot;&#10;       alt=&quot;Un sol sobre fondo azul&quot; width=&quot;160&quot; height=&quot;90&quot;&gt;&#10;  &lt;figcaption&gt;Un sol (figure + figcaption)&lt;/figcaption&gt;&#10;&lt;/figure&gt;&#10;&lt;a href=&quot;https://example.com&quot;&gt;&lt;img src=&quot;data:image/svg+xml;utf8,&lt;svg xmlns=&#x27;http://www.w3.org/2000/svg&#x27; width=&#x27;60&#x27; height=&#x27;30&#x27;&gt;&lt;rect width=&#x27;60&#x27; height=&#x27;30&#x27; fill=&#x27;%2366bb6a&#x27;/&gt;&lt;/svg&gt;&quot; alt=&quot;Imagen que es un enlace&quot;&gt;&lt;/a&gt;"></div>

!!! note "Formatos"
    **JPG** para fotos, **PNG** para imágenes con transparencia, **SVG** para iconos y dibujos que se adaptan a cualquier tamaño, **WebP** como alternativa moderna y ligera.

## Listas

<div class="demo" data-alto="22rem" data-code="&lt;h3&gt;Lista desordenada&lt;/h3&gt;&#10;&lt;ul&gt;&#10;  &lt;li&gt;Manzanas&lt;/li&gt;&#10;  &lt;li&gt;Peras&#10;    &lt;ul&gt;&#10;      &lt;li&gt;Conferencia&lt;/li&gt;&#10;      &lt;li&gt;Blanquilla&lt;/li&gt;&#10;    &lt;/ul&gt;&#10;  &lt;/li&gt;&#10;&lt;/ul&gt;&#10;&#10;&lt;h3&gt;Lista ordenada&lt;/h3&gt;&#10;&lt;ol&gt;&#10;  &lt;li&gt;Abrir el editor&lt;/li&gt;&#10;  &lt;li&gt;Escribir el código&lt;/li&gt;&#10;  &lt;li&gt;Guardar y recargar&lt;/li&gt;&#10;&lt;/ol&gt;&#10;&#10;&lt;h3&gt;Lista de definiciones&lt;/h3&gt;&#10;&lt;dl&gt;&#10;  &lt;dt&gt;HTML&lt;/dt&gt;&#10;  &lt;dd&gt;Lenguaje de marcado para la estructura.&lt;/dd&gt;&#10;  &lt;dt&gt;CSS&lt;/dt&gt;&#10;  &lt;dd&gt;Lenguaje de estilos para el aspecto.&lt;/dd&gt;&#10;&lt;/dl&gt;"></div>

| Lista | Etiquetas |
|---|---|
| Con viñetas | `<ul>` + `<li>` |
| Numerada | `<ol>` + `<li>` (admite `start`, `reversed`, `type`) |
| De definiciones | `<dl>` + `<dt>` (término) + `<dd>` (descripción) |

Las listas se pueden **anidar**: una lista dentro de un `<li>`.
