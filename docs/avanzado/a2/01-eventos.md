# WA2.1 Eventos a fondo: delegación, propagación y eventos propios

!!! info "Cómo se han comprobado los ejemplos"
    Los editores con vista previa **se ejecutan en tu navegador** al abrir la página: puedes cambiarlos y ver el resultado. Los fragmentos «ejecutado con Node» (lógica sin `document`) se han ejecutado de verdad al construir la web y su salida es la real.

## El problema: mil botones, mil oyentes

Imagina una lista de tareas con un botón «borrar» en cada fila. La solución ingenua es registrar un `addEventListener` en **cada botón**. Con cien filas son cien oyentes, y las filas **que se añadan después no tienen ninguno**.

## Propagación: el evento sube

Cuando haces clic en un elemento, el navegador avisa **a ese elemento y luego a todos sus padres**, hacia arriba, hasta el documento. Es la **fase de burbuja** (*bubbling*). Eso es lo que hace posible la técnica más útil de esta página.

<div class="demo" data-alto="13rem" data-consola="1" data-code="&lt;div id=&quot;abuelo&quot; style=&quot;padding:12px;background:#fde68a&quot;&gt;&#10;  abuelo&#10;  &lt;div id=&quot;padre&quot; style=&quot;padding:12px;background:#fca5a5&quot;&gt;&#10;    padre&#10;    &lt;button id=&quot;hijo&quot;&gt;Pulsa aquí&lt;/button&gt;&#10;  &lt;/div&gt;&#10;&lt;/div&gt;&#10;&lt;script&gt;&#10;const nombres = [&quot;abuelo&quot;, &quot;padre&quot;, &quot;hijo&quot;];&#10;nombres.forEach(id =&gt; {&#10;  document.getElementById(id).addEventListener(&quot;click&quot;, e =&gt; {&#10;    console.log(&quot;recibe el clic:&quot;, id, &quot;| target:&quot;, e.target.id, &quot;| currentTarget:&quot;, e.currentTarget.id);&#10;  });&#10;});&#10;&lt;/script&gt;"></div>

Un solo clic en el botón se **oye tres veces**: primero en `hijo`, luego en `padre` y por último en `abuelo`. Dos propiedades del evento son la clave:

| Propiedad | Qué es |
|---|---|
| `event.target` | El elemento **donde ocurrió** el clic (el más profundo) |
| `event.currentTarget` | El elemento **al que pertenece el oyente** que se está ejecutando |

## Delegación de eventos

Como el evento sube, basta **un solo oyente en el padre**, que mira `event.target` para saber qué hijo lo provocó. Funciona para los elementos que ya existen **y para los que se añadan después**.

<div class="demo" data-alto="14rem" data-code="&lt;ul id=&quot;lista&quot;&gt;&lt;/ul&gt;&#10;&lt;button id=&quot;anadir&quot;&gt;Añadir tarea&lt;/button&gt;&#10;&lt;script&gt;&#10;const lista = document.getElementById(&quot;lista&quot;);&#10;let n = 0;&#10;&#10;// UN SOLO oyente para todos los botones, presentes y futuros&#10;lista.addEventListener(&quot;click&quot;, e =&gt; {&#10;  const boton = e.target.closest(&quot;button[data-accion]&quot;);     // ¿se pulsó un botón nuestro (o algo dentro de él)?&#10;  if (!boton || !lista.contains(boton)) return;              // clic en otro sitio: ignorar&#10;  const fila = boton.closest(&quot;li&quot;);&#10;  if (boton.dataset.accion === &quot;borrar&quot;) fila.remove();&#10;  if (boton.dataset.accion === &quot;hecha&quot;) fila.classList.toggle(&quot;hecha&quot;);&#10;});&#10;&#10;document.getElementById(&quot;anadir&quot;).addEventListener(&quot;click&quot;, () =&gt; {&#10;  n++;&#10;  const li = document.createElement(&quot;li&quot;);&#10;  li.innerHTML = `Tarea ${n} &lt;button data-accion=&quot;hecha&quot;&gt;✔&lt;/button&gt; &lt;button data-accion=&quot;borrar&quot;&gt;🗑&lt;/button&gt;`;&#10;  lista.appendChild(li);&#10;});&#10;&lt;/script&gt;&#10;&lt;style&gt;.hecha { text-decoration: line-through; color: gray }&lt;/style&gt;"></div>

Prueba a añadir varias tareas, marcarlas y borrarlas: **todas funcionan con un único `addEventListener`**. Dos detalles importantes:

* **`closest(selector)`** sube por los padres hasta encontrar uno que coincida. Es imprescindible porque el clic puede caer en un icono *dentro* del botón: `e.target` sería el icono, no el botón.
* **`data-accion`** guarda en el HTML lo que quiere hacer cada botón. Se lee con **`dataset`** (`data-accion` → `dataset.accion`).

## Parar la propagación y cancelar la acción por defecto

Son dos cosas **distintas** que se confunden mucho:

| Método | Qué evita |
|---|---|
| `event.preventDefault()` | La **acción por defecto** del navegador (seguir un enlace, enviar un formulario, abrir el menú contextual) |
| `event.stopPropagation()` | Que el evento **siga subiendo** a los padres |

<div class="demo" data-alto="12rem" data-consola="1" data-code="&lt;div id=&quot;caja&quot; style=&quot;padding:10px;background:#bfdbfe&quot;&gt;&#10;  &lt;a id=&quot;enlace&quot; href=&quot;https://example.com&quot;&gt;Un enlace que NO se seguirá&lt;/a&gt;&#10;  &lt;button id=&quot;b1&quot;&gt;Con stopPropagation&lt;/button&gt;&#10;  &lt;button id=&quot;b2&quot;&gt;Sin stopPropagation&lt;/button&gt;&#10;&lt;/div&gt;&#10;&lt;script&gt;&#10;document.getElementById(&quot;caja&quot;).addEventListener(&quot;click&quot;, () =&gt; console.log(&quot;  → la caja lo oye&quot;));&#10;&#10;document.getElementById(&quot;enlace&quot;).addEventListener(&quot;click&quot;, e =&gt; {&#10;  e.preventDefault();                                    // no navega&#10;  console.log(&quot;enlace pulsado, pero no se navega&quot;);&#10;});&#10;document.getElementById(&quot;b1&quot;).addEventListener(&quot;click&quot;, e =&gt; { e.stopPropagation(); console.log(&quot;b1: la caja NO se enterará&quot;); });&#10;document.getElementById(&quot;b2&quot;).addEventListener(&quot;click&quot;, () =&gt; console.log(&quot;b2: la caja SÍ se enterará&quot;));&#10;&lt;/script&gt;"></div>

!!! warning "Usa `stopPropagation` con cuidado"
    Si cortas la propagación, **otros oyentes de arriba dejan de enterarse** (un menú que debía cerrarse al hacer clic fuera, un sistema de estadísticas...) y es muy difícil encontrar por qué. Casi siempre se puede resolver con una comprobación en el propio oyente.

## Opciones de `addEventListener`

El tercer parámetro admite opciones:

| Opción | Efecto |
|---|---|
| `{ once: true }` | El oyente se **ejecuta una sola vez** y se elimina solo |
| `{ passive: true }` | Promete que **no llamará a `preventDefault`**: el navegador puede desplazar la página sin esperar. Útil en `scroll`, `touchmove` y `wheel` |
| `{ capture: true }` | Escucha en la fase **descendente** (de arriba abajo), antes que los hijos |
| `{ signal }` | Un `AbortSignal`: al cancelarlo, el oyente **se elimina** |

La última es la forma más cómoda de **limpiar varios oyentes de golpe**, sin tener que guardar cada función para llamar a `removeEventListener`:

<div class="demo" data-alto="12rem" data-consola="1" data-code="&lt;button id=&quot;b&quot;&gt;Pulsa (se desactiva tras 3 clics)&lt;/button&gt;&#10;&lt;button id=&quot;parar&quot;&gt;Quitar todos los oyentes&lt;/button&gt;&#10;&lt;script&gt;&#10;const control = new AbortController();&#10;let cuenta = 0;&#10;const b = document.getElementById(&quot;b&quot;);&#10;&#10;b.addEventListener(&quot;click&quot;, () =&gt; { cuenta++; console.log(&quot;clic&quot;, cuenta); if (cuenta === 3) control.abort(); }, { signal: control.signal });&#10;b.addEventListener(&quot;mouseenter&quot;, () =&gt; console.log(&quot;ratón encima&quot;), { signal: control.signal });&#10;document.getElementById(&quot;parar&quot;).addEventListener(&quot;click&quot;, () =&gt; { control.abort(); console.log(&quot;oyentes eliminados&quot;); });&#10;&#10;b.addEventListener(&quot;click&quot;, () =&gt; console.log(&quot;(este oyente se ejecuta solo una vez)&quot;), { once: true });&#10;&lt;/script&gt;"></div>

## Eventos propios: `CustomEvent`

Los elementos pueden **emitir sus propios eventos**. Es una forma de que las piezas de una página se comuniquen **sin conocerse**: una emite, otra escucha. `EventTarget` y `CustomEvent` existen también en Node, así que se puede comprobar con un ejemplo ejecutado:

```javascript
const bus = new EventTarget();                          // cualquier objeto «escuchable»

bus.addEventListener("pedido:nuevo", (e) => {
  console.log("cocina recibe:", e.detail.plato, "x", e.detail.cantidad);
});
bus.addEventListener("pedido:nuevo", (e) => {
  console.log("caja cobra:", (e.detail.cantidad * e.detail.precio).toFixed(2), "€");
});

bus.dispatchEvent(new CustomEvent("pedido:nuevo", { detail: { plato: "tortilla", cantidad: 2, precio: 4.5 } }));
bus.dispatchEvent(new CustomEvent("otro:evento"));       // nadie lo escucha: no pasa nada
```

**Salida (ejecutado con Node):**

```text
cocina recibe: tortilla x 2
caja cobra: 9.00 €
```

`detail` lleva los datos. En una página, el evento sale de un elemento y **sube por el DOM** si lo creas con `{ bubbles: true }`:

<div class="demo" data-alto="10rem" data-consola="1" data-code="&lt;div id=&quot;app&quot; style=&quot;padding:10px;background:#d1fae5&quot;&gt;&#10;  &lt;button id=&quot;aceptar&quot;&gt;Aceptar&lt;/button&gt;&#10;&lt;/div&gt;&#10;&lt;script&gt;&#10;// Un «componente»: el botón anuncia lo que ocurre; no sabe quién escucha&#10;document.getElementById(&quot;aceptar&quot;).addEventListener(&quot;click&quot;, e =&gt; {&#10;  e.target.dispatchEvent(new CustomEvent(&quot;aceptado&quot;, { bubbles: true, detail: { anio: new Date().getFullYear() } }));&#10;});&#10;&#10;// Otra parte de la página escucha en un padre&#10;document.getElementById(&quot;app&quot;).addEventListener(&quot;aceptado&quot;, e =&gt; {&#10;  console.log(&quot;alguien aceptó (año &quot; + e.detail.anio + &quot;) — origen:&quot;, e.target.id);&#10;});&#10;&lt;/script&gt;"></div>

Con eventos propios, el botón **no necesita saber** quién reacciona a «aceptado»: se pueden añadir oyentes (analítica, un aviso, un guardado) sin tocar el botón.

## `<template>` y `dataset`: pintar listas sin pegar cadenas

Construir HTML pegando cadenas (`innerHTML = "<li>" + dato + "</li>"`) es incómodo y, con datos del usuario, **peligroso** (XSS). El elemento **`<template>`** guarda un trozo de HTML **que no se muestra**, y se **clona** tantas veces como haga falta:

<div class="demo" data-alto="19rem" data-consola="1" data-code="&lt;template id=&quot;plantilla-tarjeta&quot;&gt;&#10;  &lt;article class=&quot;tarjeta&quot;&gt;&#10;    &lt;h3&gt;&lt;/h3&gt;&#10;    &lt;p&gt;&lt;/p&gt;&#10;    &lt;button&gt;Ver&lt;/button&gt;&#10;  &lt;/article&gt;&#10;&lt;/template&gt;&#10;&lt;div id=&quot;contenedor&quot;&gt;&lt;/div&gt;&#10;&lt;style&gt;&#10;  .tarjeta { border: 1px solid #94a3b8; border-radius: 8px; padding: 8px 12px; margin: 6px 0; }&#10;  .tarjeta h3 { margin: 0 0 4px }&#10;&lt;/style&gt;&#10;&lt;script&gt;&#10;const datos = [&#10;  { id: 1, titulo: &quot;HTML&quot;, texto: &quot;La estructura&quot; },&#10;  { id: 2, titulo: &quot;CSS&quot;, texto: &quot;El aspecto&quot; },&#10;  { id: 3, titulo: &quot;&lt;b&gt;JS&lt;/b&gt;&quot;, texto: &quot;El comportamiento (el &lt;b&gt; se muestra como texto, no se interpreta)&quot; }&#10;];&#10;&#10;const plantilla = document.getElementById(&quot;plantilla-tarjeta&quot;);&#10;const fragmento = document.createDocumentFragment();          // una «caja» fuera de la página&#10;&#10;for (const d of datos) {&#10;  const copia = plantilla.content.cloneNode(true);            // clona el contenido de la plantilla&#10;  copia.querySelector(&quot;h3&quot;).textContent = d.titulo;           // textContent: seguro frente a XSS&#10;  copia.querySelector(&quot;p&quot;).textContent = d.texto;&#10;  copia.querySelector(&quot;button&quot;).dataset.id = d.id;            // guarda el id en data-id&#10;  fragmento.appendChild(copia);&#10;}&#10;document.getElementById(&quot;contenedor&quot;).appendChild(fragmento); // UNA sola inserción en el DOM&#10;&#10;document.getElementById(&quot;contenedor&quot;).addEventListener(&quot;click&quot;, e =&gt; {&#10;  const b = e.target.closest(&quot;button[data-id]&quot;);&#10;  if (b) console.log(&quot;tarjeta pulsada, id =&quot;, b.dataset.id);&#10;});&#10;&lt;/script&gt;"></div>

Tres ideas en un solo ejemplo:

* **`template.content.cloneNode(true)`** da una copia nueva de la plantilla cada vez.
* **`DocumentFragment`** permite montar todo **fuera de la página** y añadirlo **de una vez**: tocar el DOM es lo caro, así que cuantas menos veces, mejor.
* **`textContent`** en lugar de `innerHTML`: el `<b>JS</b>` del título aparece **como texto**, sin interpretarse. Si el dato viniera de un usuario, no podría colar código.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Un oyente por cada elemento de una lista | Delegación: un oyente en el padre y `e.target.closest(...)` |
| Usar `e.target` directamente cuando el botón tiene un icono dentro | `e.target.closest("button")` |
| Confundir `preventDefault` con `stopPropagation` | Uno cancela la acción del navegador; el otro corta la subida |
| `stopPropagation` por todas partes | Comprobar la condición dentro del propio oyente |
| Oyentes que se acumulan al repintar una parte de la página | `{ signal }` con un `AbortController` por cada repintado |
| Construir HTML pegando cadenas con datos del usuario | `<template>`, `createElement` y `textContent` |
| Añadir elementos uno a uno dentro de un bucle | `DocumentFragment` y una sola inserción |

## Para practicar

Los ejercicios WA2.5 y WA2.6 de [WA2 · Ejercicios](ejercicios.md) son de DOM; el WA2.3 usa un bus de eventos.
