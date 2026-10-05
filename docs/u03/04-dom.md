# 3.4 El DOM

El navegador convierte tu HTML en un **árbol de objetos** llamado **DOM** (*Document Object Model*). JavaScript lo usa para **leer y cambiar** la página.

```text
document
 └── html
      ├── head → title
      └── body → h1, p, button ...
```

## Seleccionar elementos

<div class="demo" data-alto="13rem" data-consola="1" data-code="&lt;p id=&quot;saludo&quot;&gt;Hola&lt;/p&gt;&#10;&lt;ul&gt;&lt;li class=&quot;item&quot;&gt;Uno&lt;/li&gt;&lt;li class=&quot;item&quot;&gt;Dos&lt;/li&gt;&lt;/ul&gt;&#10;&lt;script&gt;&#10;const saludo = document.getElementById(&quot;saludo&quot;);        // por id&#10;const primero = document.querySelector(&quot;.item&quot;);         // el primero que coincide con un selector CSS&#10;const todos = document.querySelectorAll(&quot;.item&quot;);        // todos (lista)&#10;console.log(saludo.textContent, primero.textContent, todos.length);&#10;&lt;/script&gt;"></div>

`querySelector` y `querySelectorAll` aceptan **cualquier selector CSS** (`#id`, `.clase`, `ul > li`…).

## Cambiar contenido, estilo y atributos

<div class="demo" data-alto="11rem" data-code="&lt;p id=&quot;saludo&quot;&gt;Hola&lt;/p&gt;&#10;&lt;img id=&quot;foto&quot; alt=&quot;Un cuadrado&quot; width=&quot;60&quot;&#10;     src=&quot;data:image/svg+xml;utf8,&lt;svg xmlns=&#x27;http://www.w3.org/2000/svg&#x27; width=&#x27;60&#x27; height=&#x27;60&#x27;&gt;&lt;rect width=&#x27;60&#x27; height=&#x27;60&#x27; fill=&#x27;tomato&#x27;/&gt;&lt;/svg&gt;&quot;&gt;&#10;&lt;script&gt;&#10;const saludo = document.getElementById(&quot;saludo&quot;);&#10;saludo.textContent = &quot;¡Hola, DOM!&quot;;             // cambia el texto&#10;saludo.style.color = &quot;royalblue&quot;;                // cambia un estilo concreto&#10;saludo.style.fontSize = &quot;24px&quot;;                  // las propiedades se escriben en camelCase&#10;saludo.classList.add(&quot;destacado&quot;);               // añade una clase&#10;document.title = &quot;Título cambiado desde JS&quot;;     // título de la pestaña&#10;document.getElementById(&quot;foto&quot;).alt = &quot;Ahora el alt es otro&quot;;&#10;&lt;/script&gt;"></div>

| Qué quiero | Cómo |
|---|---|
| Cambiar el texto | `el.textContent = "..."` |
| Cambiar HTML (con etiquetas) | `el.innerHTML = "<b>...</b>"` |
| Cambiar un estilo | `el.style.color = "red"` |
| Añadir / quitar / alternar clase | `el.classList.add("x")` · `.remove("x")` · `.toggle("x")` |
| Leer / cambiar un atributo | `el.getAttribute("src")` · `el.setAttribute("src", "...")` · `el.src = "..."` |
| Cambiar el título de la pestaña | `document.title = "..."` |

!!! warning "`textContent` frente a `innerHTML`"
    `textContent` trata el valor como **texto**. `innerHTML` interpreta **etiquetas**: si el valor viene del usuario, es una puerta a ataques (XSS). Prefiere `textContent`.

## Crear y añadir elementos

<div class="demo" data-alto="9rem" data-code="&lt;ul id=&quot;lista&quot;&gt;&lt;/ul&gt;&#10;&lt;script&gt;&#10;const lista = document.getElementById(&quot;lista&quot;);&#10;[&quot;Pan&quot;, &quot;Leche&quot;, &quot;Huevos&quot;].forEach(texto =&gt; {&#10;  const li = document.createElement(&quot;li&quot;);       // crea el elemento&#10;  li.textContent = texto;&#10;  lista.appendChild(li);                         // lo añade al final&#10;});&#10;lista.firstElementChild.remove();                // quita el primero&#10;&lt;/script&gt;"></div>

`createElement` + `appendChild` permite **añadir** contenido sin borrar el que ya había.
