# 3.6 Formularios y validación

Con JavaScript puedes **leer** lo que escribe el usuario (`.value`) y **comprobarlo** antes de enviarlo.

<div class="demo" data-alto="17rem" data-code="&lt;form id=&quot;registro&quot;&gt;&#10;  &lt;p&gt;&lt;label&gt;Nombre: &lt;input id=&quot;nombre&quot;&gt;&lt;/label&gt;&lt;/p&gt;&#10;  &lt;p&gt;&lt;label&gt;Edad: &lt;input id=&quot;edad&quot; type=&quot;number&quot;&gt;&lt;/label&gt;&lt;/p&gt;&#10;  &lt;p&gt;&lt;button&gt;Enviar&lt;/button&gt;&lt;/p&gt;&#10;  &lt;p id=&quot;mensaje&quot;&gt;&lt;/p&gt;&#10;&lt;/form&gt;&#10;&lt;script&gt;&#10;const form = document.getElementById(&quot;registro&quot;);&#10;const mensaje = document.getElementById(&quot;mensaje&quot;);&#10;&#10;form.addEventListener(&quot;submit&quot;, (e) =&gt; {&#10;  e.preventDefault();                          // no recargar la página&#10;  const nombre = document.getElementById(&quot;nombre&quot;).value.trim();&#10;  const edad = Number(document.getElementById(&quot;edad&quot;).value);&#10;&#10;  if (nombre === &quot;&quot;) {&#10;    mensaje.textContent = &quot;El nombre es obligatorio&quot;;&#10;  } else if (!Number.isInteger(edad) || edad &lt; 0 || edad &gt; 120) {&#10;    mensaje.textContent = &quot;La edad debe ser un número entre 0 y 120&quot;;&#10;  } else {&#10;    mensaje.textContent = `Bienvenido, ${nombre} (${edad} años)`;&#10;  }&#10;});&#10;&lt;/script&gt;"></div>

| Idea | Cómo |
|---|---|
| Leer un campo | `input.value` (siempre texto) |
| Convertir a número | `Number(input.value)` |
| Quitar espacios | `valor.trim()` |
| Marcar/leer una casilla | `checkbox.checked` |
| Opción elegida de un `select` | `select.value` |
| Evitar el envío | `e.preventDefault()` en el evento `submit` |
| Vaciar un campo | `input.value = ""` |

!!! warning "La validación en el navegador no es suficiente"
    Un usuario puede saltarse tu JavaScript. Si los datos van a un servidor, **hay que validarlos también allí**. La validación del navegador sirve para dar un mejor aviso al usuario.
