# 1.2 Texto y enlaces

## Títulos y párrafos

```html
<h1>Título principal</h1>      <!-- solo uno por página -->
<h2>Sección</h2>
<h3>Subsección</h3>            <!-- hasta h6 -->
<p>Un párrafo de texto.</p>
<br>                            <!-- salto de línea -->
<hr>                            <!-- línea horizontal -->
```

!!! note "Los títulos son jerarquía, no tamaño"
    Usa `h1`…`h6` por el **nivel** del contenido, no para hacer letras grandes (eso es trabajo del CSS).

## Dar énfasis al texto

<div class="demo" data-alto="13rem" data-code="&lt;p&gt;Texto &lt;strong&gt;importante&lt;/strong&gt; y &lt;em&gt;enfatizado&lt;/em&gt;.&lt;/p&gt;&#10;&lt;p&gt;H&lt;sub&gt;2&lt;/sub&gt;O y x&lt;sup&gt;2&lt;/sup&gt;. &lt;del&gt;Tachado&lt;/del&gt;, &lt;ins&gt;subrayado&lt;/ins&gt;, &lt;small&gt;pequeño&lt;/small&gt;.&lt;/p&gt;&#10;&lt;p&gt;Una tecla: &lt;kbd&gt;Ctrl&lt;/kbd&gt; + &lt;kbd&gt;S&lt;/kbd&gt;. Código: &lt;code&gt;let x = 1;&lt;/code&gt;&lt;/p&gt;&#10;&lt;blockquote&gt;Una cita larga va dentro de blockquote.&lt;/blockquote&gt;&#10;&lt;pre&gt;Texto   con&#10;   espacios y saltos&#10;conservados&lt;/pre&gt;"></div>

| Etiqueta | Significa |
|---|---|
| `<strong>` | Importante (negrita) |
| `<em>` | Énfasis (cursiva) |
| `<sub>` / `<sup>` | Subíndice / superíndice |
| `<del>` / `<ins>` | Texto eliminado / añadido |
| `<small>` | Letra pequeña (notas legales) |
| `<code>` / `<kbd>` | Código / tecla |
| `<pre>` | Respeta espacios y saltos de línea |

## Enlaces

```html
<a href="https://developer.mozilla.org/">Documentación de MDN</a>
```

<div class="demo" data-alto="15rem" data-code="&lt;p&gt;&lt;a href=&quot;https://developer.mozilla.org/&quot; target=&quot;_blank&quot; rel=&quot;noopener&quot;&gt;Abrir MDN en otra pestaña&lt;/a&gt;&lt;/p&gt;&#10;&lt;p&gt;&lt;a href=&quot;#abajo&quot;&gt;Ir al final de esta página (ancla)&lt;/a&gt;&lt;/p&gt;&#10;&lt;p&gt;&lt;a href=&quot;mailto:alguien@example.com&quot;&gt;Escribir un correo&lt;/a&gt;&lt;/p&gt;&#10;&lt;p&gt;&lt;a href=&quot;tel:+34600000000&quot;&gt;Llamar por teléfono&lt;/a&gt;&lt;/p&gt;&#10;&lt;div style=&quot;height:120px&quot;&gt;&lt;/div&gt;&#10;&lt;h2 id=&quot;abajo&quot;&gt;Aquí estaba el ancla&lt;/h2&gt;"></div>

| Tipo de enlace | `href` |
|---|---|
| Otra web (absoluto) | `https://...` |
| Otra página de tu web (relativo) | `contacto.html` · `carpeta/pagina.html` |
| Un punto de la misma página | `#identificador` (el elemento lleva `id="identificador"`) |
| Correo / teléfono | `mailto:...` · `tel:...` |

!!! tip "Abrir en otra pestaña"
    `target="_blank"` abre en una pestaña nueva. Los navegadores actuales ya aplican `noopener` por defecto a estos enlaces, pero escribir `rel="noopener"` sigue siendo buena costumbre (y es necesario en navegadores antiguos).
