# WA2 · Ejercicios de DOM y eventos avanzados

<div class="ej-gate" data-unit="a2" data-nombre="WA2 · DOM y eventos avanzados"></div>

Los ejercicios WA2.1 a WA2.4 son de JavaScript sin DOM: escríbelos en un `.js` y ejecútalos con `node archivo.js`; tu salida debe ser **idéntica** al resultado esperado, obtenido ejecutando la solución modelo con Node de verdad. Los WA2.5 y WA2.6 son de DOM: se hacen en una página HTML y **se comprueban en el navegador**; no tienen salida de consola. Los marcados con ⭐ son más difíciles.

## Ejercicio WA2.1

**Leer filtros de la URL.** Escribe `leerFiltros` con `URL` y `URLSearchParams`.

```javascript
// Recibe una URL (texto) y devuelve un objeto con sus parámetros:
//  - «pagina» y «precioMax» pasan a NÚMERO si existen;
//  - «q» se queda como texto (o "" si no existe);
//  - «etiquetas» es un array con TODOS los valores de «etiqueta» (vacío si no hay).
function leerFiltros(url) {
  // ...
}
```

Para comprobarlo:

```javascript
console.log(leerFiltros("https://tienda.es/lista?q=caf%C3%A9&pagina=2&precioMax=15.5&etiqueta=bio&etiqueta=oferta"));
console.log(leerFiltros("https://tienda.es/lista"));
```

**Resultado esperado** (ejecutado con Node):

```text
{
  q: 'café',
  etiquetas: [ 'bio', 'oferta' ],
  pagina: 2,
  precioMax: 15.5
}
{ q: '', etiquetas: [] }
```

<details class="sol" data-key="web/av-a2/WA2.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>function leerFiltros(url) {
  const p = new URL(url).searchParams;
  const filtros = { q: p.get("q") ?? "", etiquetas: p.getAll("etiqueta") };
  if (p.has("pagina")) filtros.pagina = Number(p.get("pagina"));
  if (p.has("precioMax")) filtros.precioMax = Number(p.get("precioMax"));
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio WA2.2

**Construir una URL.** Escribe `urlDe` sin pegar cadenas a mano.

```javascript
// Construye la URL de búsqueda a partir de un objeto de estado {q, pagina, etiquetas}.
// Solo incluye «q» si no está vacío, «pagina» si es mayor que 1, y una «etiqueta» por cada elemento del array.
// La base es siempre https://tienda.es/lista
function urlDe(estado) {
  // ...
}
```

Para comprobarlo:

```javascript
console.log(urlDe({ q: "té verde & menta", pagina: 3, etiquetas: ["bio", "oferta"] }));
console.log(urlDe({ q: "", pagina: 1 }));
```

**Resultado esperado** (ejecutado con Node):

```text
https://tienda.es/lista?q=t%C3%A9+verde+%26+menta&pagina=3&etiqueta=bio&etiqueta=oferta
https://tienda.es/lista
```

<details class="sol" data-key="web/av-a2/WA2.2">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>function urlDe({ q = "", pagina = 1, etiquetas = [] }) {
  const url = new URL("https://tienda.es/lista");
  if (q) url.searchParams.set("q", q);
  if (pagina &gt; 1) url.searchParams.set("pagina", pagina);
  for (const e of etiquetas) url.searchParams.append("etiqueta", e);
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio WA2.3

**Un bus de eventos.** Escribe `crearBus` con un `Map` y `Set`.

```javascript
// crearBus devuelve { escuchar(nombre, fn), emitir(nombre, datos) } SIN usar EventTarget:
//  - escuchar devuelve una FUNCIÓN que, al llamarla, deja de escuchar;
//  - emitir llama a todas las funciones de ese nombre con los datos, y devuelve cuántas se llamaron.
function crearBus() {
  // ...
}
```

Para comprobarlo:

```javascript
const bus = crearBus();
const dejar = bus.escuchar("saludo", n => console.log("hola,", n));
bus.escuchar("saludo", n => console.log("buenas,", n));
console.log(bus.emitir("saludo", "Ana"));
dejar();
console.log(bus.emitir("saludo", "Luis"));
console.log(bus.emitir("nadie", 1));
```

**Resultado esperado** (ejecutado con Node):

```text
hola, Ana
buenas, Ana
2
buenas, Luis
1
0
```

<details class="sol" data-key="web/av-a2/WA2.3">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>function crearBus() {
  const oyentes = new Map();
  return {
    escuchar(nombre, fn) {
      if (!oyentes.has(nombre)) oyentes.set(nombre, new Set());
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio WA2.4

**Migrar datos guardados.** Escribe `migrar`.

```javascript
// Los datos guardados pueden estar en la versión 1 o la 2. Escribe `migrar`:
//   v1: { version: 1, nombre: "Ana Pérez" }
//   v2: { version: 2, nombre: "Ana", apellidos: "Pérez" }   (el primer espacio separa nombre y apellidos)
// Si ya es v2, se devuelve igual. Si no tiene versión o no es un objeto, devuelve { version: 2, nombre: "", apellidos: "" }.
function migrar(datos) {
  // ...
}
```

Para comprobarlo:

```javascript
console.log(migrar({ version: 1, nombre: "Ana María Pérez Gil" }));
console.log(migrar({ version: 2, nombre: "Luis", apellidos: "Ruiz" }));
console.log(migrar({ nombre: "sin versión" }));
console.log(migrar(null), migrar("texto"));
```

**Resultado esperado** (ejecutado con Node):

```text
{ version: 2, nombre: 'Ana', apellidos: 'María Pérez Gil' }
{ version: 2, nombre: 'Luis', apellidos: 'Ruiz' }
{ version: 2, nombre: '', apellidos: '' }
{ version: 2, nombre: '', apellidos: '' } { version: 2, nombre: '', apellidos: '' }
```

<details class="sol" data-key="web/av-a2/WA2.4">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>function migrar(datos) {
  const vacio = { version: 2, nombre: "", apellidos: "" };
  if (datos === null || typeof datos !== "object") return vacio;
  if (datos.version === 2) return datos;
  if (datos.version === 1) {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio WA2.5

**Lista de la compra con delegación.** *(DOM: se comprueba en el navegador, no hay salida de consola.)* Parte de este HTML y escribe el JavaScript para que:

* el botón «Añadir» añada el producto escrito (si no está vacío) a la lista **con un solo `addEventListener` para todos los botones «Quitar»**,
* al pulsar «Quitar» en cualquier fila, esa fila desaparezca (también las añadidas después),
* el texto del producto se inserte con `textContent`, de forma que si escribes `<b>pan</b>` se vea **como texto**.

```html
<input id="producto" placeholder="Producto">
<button id="anadir">Añadir</button>
<ul id="lista"></ul>
```

<details class="sol" data-key="web/av-a2/WA2.5">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>&lt;input id="producto" placeholder="Producto"&gt;
&lt;button id="anadir"&gt;Añadir&lt;/button&gt;
&lt;ul id="lista"&gt;&lt;/ul&gt;
&lt;script&gt;
const lista = document.getElementById("lista");
const entrada = document.getElementById("producto");
&lt;!-- ... (resto de la solución bloqueado) --&gt;</code></pre></div>
</details>

## Ejercicio WA2.6

⭐ **Tarjetas que aparecen al verse.** *(DOM: se comprueba en el navegador.)* Una página con 12 `<div class="tarjeta">`, cada una de 150 px de alto y con `opacity: 0` al principio, debe hacer que **cada tarjeta se vuelva visible (con una transición) la primera vez que entra en la pantalla**, y que **deje de observarse** a partir de ese momento. Usa `IntersectionObserver`.

```html
<style>
  .tarjeta { height: 150px; margin: 20px; background: #fde68a; opacity: 0; transition: opacity .6s; }
  .tarjeta.visible { opacity: 1; }
</style>
<!-- 12 tarjetas, creadas por script -->
<div id="contenedor"></div>
```

<details class="sol" data-key="web/av-a2/WA2.6">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>&lt;style&gt;
  .tarjeta { height: 150px; margin: 20px; background: #fde68a; opacity: 0; transition: opacity .6s; }
  .tarjeta.visible { opacity: 1; }
&lt;/style&gt;
&lt;div id="contenedor"&gt;&lt;/div&gt;
&lt;script&gt;
&lt;!-- ... (resto de la solución bloqueado) --&gt;</code></pre></div>
</details>
