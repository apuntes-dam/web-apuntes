# 1.5 Formularios

Un formulario recoge datos del usuario. Se compone de un `<form>` con controles (`<input>`, `<select>`, `<textarea>`…), cada uno con su `<label>`.

<div class="demo" data-alto="26rem" data-code="&lt;form action=&quot;#&quot; method=&quot;post&quot;&gt;&#10;  &lt;p&gt;&#10;    &lt;label for=&quot;nombre&quot;&gt;Nombre:&lt;/label&gt;&#10;    &lt;input type=&quot;text&quot; id=&quot;nombre&quot; name=&quot;nombre&quot; placeholder=&quot;Tu nombre&quot; required&gt;&#10;  &lt;/p&gt;&#10;  &lt;p&gt;&#10;    &lt;label for=&quot;correo&quot;&gt;Correo:&lt;/label&gt;&#10;    &lt;input type=&quot;email&quot; id=&quot;correo&quot; name=&quot;correo&quot; required&gt;&#10;  &lt;/p&gt;&#10;  &lt;p&gt;&#10;    &lt;label for=&quot;curso&quot;&gt;Curso:&lt;/label&gt;&#10;    &lt;select id=&quot;curso&quot; name=&quot;curso&quot;&gt;&#10;      &lt;option value=&quot;1&quot;&gt;1º DAM&lt;/option&gt;&#10;      &lt;option value=&quot;2&quot; selected&gt;2º DAM&lt;/option&gt;&#10;    &lt;/select&gt;&#10;  &lt;/p&gt;&#10;  &lt;p&gt;Experiencia:&#10;    &lt;label&gt;&lt;input type=&quot;radio&quot; name=&quot;exp&quot; value=&quot;poca&quot; checked&gt; Poca&lt;/label&gt;&#10;    &lt;label&gt;&lt;input type=&quot;radio&quot; name=&quot;exp&quot; value=&quot;mucha&quot;&gt; Mucha&lt;/label&gt;&#10;  &lt;/p&gt;&#10;  &lt;p&gt;&lt;label&gt;&lt;input type=&quot;checkbox&quot; name=&quot;acepto&quot; required&gt; Acepto las condiciones&lt;/label&gt;&lt;/p&gt;&#10;  &lt;p&gt;&#10;    &lt;label for=&quot;msg&quot;&gt;Mensaje:&lt;/label&gt;&lt;br&gt;&#10;    &lt;textarea id=&quot;msg&quot; name=&quot;msg&quot; rows=&quot;3&quot; cols=&quot;30&quot;&gt;&lt;/textarea&gt;&#10;  &lt;/p&gt;&#10;  &lt;button type=&quot;submit&quot;&gt;Enviar&lt;/button&gt;&#10;&lt;/form&gt;"></div>

## Los tipos de `<input>` más usados

| `type` | Qué recoge |
|---|---|
| `text` | Texto corto |
| `email`, `url`, `tel` | Correo, dirección web, teléfono (el móvil muestra el teclado adecuado) |
| `password` | Texto oculto |
| `number` | Número (admite `min`, `max`, `step`) |
| `date`, `time` | Fecha, hora |
| `radio` | Una opción entre varias (mismo `name`) |
| `checkbox` | Casilla marcable |
| `range`, `color` | Deslizador, selector de color |
| `file` | Subir un archivo |
| `submit` | Botón de envío |

## Validación incorporada

Sin JavaScript, el navegador ya valida con atributos:

| Atributo | Efecto |
|---|---|
| `required` | Obligatorio |
| `minlength` / `maxlength` | Longitud mínima / máxima |
| `min` / `max` | Rango de un número o fecha |
| `pattern="..."` | Expresión regular que debe cumplir |

```html
<input type="text" name="dni" pattern="[0-9]{8}[A-Za-z]" title="8 números y una letra" required>
```

!!! warning "Siempre `label`"
    Cada control debe tener su `<label for="id">`: mejora la accesibilidad y permite hacer clic en el texto. El valor de `for` es el `id` del control. El `name` es el nombre con el que viajará el dato al enviarlo.
