# 3.7 `localStorage` y `fetch`

## `localStorage`: guardar datos en el navegador

Guarda pares clave/valor (solo **texto**) que **se conservan al cerrar la pestaña**:

<div class="demo" data-alto="9rem" data-code="&lt;p id=&quot;salida&quot;&gt;&lt;/p&gt;&#10;&lt;button id=&quot;mas&quot;&gt;Visitas +1&lt;/button&gt;&#10;&lt;script&gt;&#10;let visitas = Number(localStorage.getItem(&quot;visitas&quot;)) || 0;   // si no existe, 0&#10;const salida = document.getElementById(&quot;salida&quot;);&#10;salida.textContent = &quot;Contador guardado: &quot; + visitas;&#10;&#10;document.getElementById(&quot;mas&quot;).addEventListener(&quot;click&quot;, () =&gt; {&#10;  visitas++;&#10;  localStorage.setItem(&quot;visitas&quot;, visitas);&#10;  salida.textContent = &quot;Contador guardado: &quot; + visitas;&#10;});&#10;&lt;/script&gt;"></div>

| Método | Qué hace |
|---|---|
| `localStorage.setItem("clave", "valor")` | Guarda |
| `localStorage.getItem("clave")` | Lee (`null` si no existe) |
| `localStorage.removeItem("clave")` | Borra una clave |
| `localStorage.clear()` | Borra todo |

Para guardar un objeto o un array, conviértelo con `JSON.stringify` y recupéralo con `JSON.parse`:

```js
localStorage.setItem("tareas", JSON.stringify(["a", "b"]));
const tareas = JSON.parse(localStorage.getItem("tareas")) || [];
```

!!! warning "No guardes secretos"
    `localStorage` es legible por cualquier script de esa página. No guardes contraseñas ni datos sensibles.

## `fetch`: pedir datos a un servidor

`fetch` hace una petición y devuelve una **promesa**. Con `async`/`await` se lee como código normal:

<div class="demo" data-alto="12rem" data-code="&lt;ul id=&quot;posts&quot;&gt;Cargando...&lt;/ul&gt;&#10;&lt;script&gt;&#10;async function cargar() {&#10;  try {&#10;    const respuesta = await fetch(&quot;https://jsonplaceholder.typicode.com/posts?_limit=3&quot;);&#10;    if (!respuesta.ok) throw new Error(&quot;Error &quot; + respuesta.status);&#10;    const datos = await respuesta.json();            // convierte el JSON en objetos&#10;    const lista = document.getElementById(&quot;posts&quot;);&#10;    lista.textContent = &quot;&quot;;&#10;    datos.forEach(p =&gt; {&#10;      const li = document.createElement(&quot;li&quot;);&#10;      li.textContent = p.title;&#10;      lista.appendChild(li);&#10;    });&#10;  } catch (error) {&#10;    document.getElementById(&quot;posts&quot;).textContent = &quot;No se pudo cargar: &quot; + error.message;&#10;  }&#10;}&#10;cargar();&#10;&lt;/script&gt;"></div>

* `await` espera el resultado sin bloquear la página.
* `try`/`catch` captura los fallos de red.
* Necesita **conexión a internet**; si falla, el ejemplo muestra el mensaje de error.
