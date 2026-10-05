# 3.5 Eventos

Un **evento** es algo que ocurre en la página: un clic, pasar el ratón, escribir, enviar un formulario. Con `addEventListener` le dices a JavaScript qué función ejecutar cuando ocurra.

<div class="demo" data-alto="11rem" data-code="&lt;p id=&quot;saludo&quot;&gt;Pulsa los botones&lt;/p&gt;&#10;&lt;button id=&quot;b1&quot;&gt;Cambiar color&lt;/button&gt;&#10;&lt;button id=&quot;b2&quot;&gt;Contador&lt;/button&gt;&#10;&lt;script&gt;&#10;const saludo = document.getElementById(&quot;saludo&quot;);&#10;let veces = 0;&#10;&#10;document.getElementById(&quot;b1&quot;).addEventListener(&quot;click&quot;, () =&gt; {&#10;  saludo.style.color = saludo.style.color === &quot;red&quot; ? &quot;black&quot; : &quot;red&quot;;&#10;});&#10;&#10;document.getElementById(&quot;b2&quot;).addEventListener(&quot;click&quot;, () =&gt; {&#10;  veces++;&#10;  saludo.textContent = &quot;Has pulsado &quot; + veces + &quot; veces&quot;;&#10;});&#10;&lt;/script&gt;"></div>

## Eventos habituales

| Evento | Cuándo se produce |
|---|---|
| `click` | Un clic |
| `dblclick` | Doble clic |
| `mouseover` / `mouseout` | El ratón entra / sale del elemento |
| `input` | Cada cambio mientras se escribe en un campo |
| `change` | El valor cambió (al salir del campo, en `select`, `checkbox`…) |
| `submit` | Se envía un formulario |
| `keydown` | Se pulsa una tecla |
| `DOMContentLoaded` | El HTML ya está cargado |

Un mismo elemento puede tener **varios** `addEventListener`, incluso de eventos distintos, sin que interfieran.

## El objeto `event`

La función recibe un objeto con datos del evento:

<div class="demo" data-alto="11rem" data-code="&lt;input id=&quot;campo&quot; placeholder=&quot;Escribe aquí&quot;&gt;&#10;&lt;p id=&quot;eco&quot;&gt;&lt;/p&gt;&#10;&lt;a id=&quot;enlace&quot; href=&quot;https://example.com&quot;&gt;Un enlace que no te lleva a ningún sitio&lt;/a&gt;&#10;&lt;script&gt;&#10;document.getElementById(&quot;campo&quot;).addEventListener(&quot;input&quot;, (e) =&gt; {&#10;  document.getElementById(&quot;eco&quot;).textContent = &quot;Has escrito: &quot; + e.target.value;&#10;});&#10;document.getElementById(&quot;enlace&quot;).addEventListener(&quot;click&quot;, (e) =&gt; {&#10;  e.preventDefault();               // cancela la acción por defecto&#10;  document.getElementById(&quot;eco&quot;).textContent = &quot;Enlace bloqueado&quot;;&#10;});&#10;&lt;/script&gt;"></div>

* `e.target` es el elemento que provocó el evento.
* `e.preventDefault()` evita el comportamiento normal (seguir un enlace, enviar un formulario).

!!! note "`mouseover` y `:hover`"
    Un efecto solo visual al pasar el ratón se hace mejor con CSS (`:hover`). Usa el evento `mouseover` cuando necesites lógica.
