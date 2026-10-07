# WA1 · Ejercicios de JavaScript moderno y asíncrono

<div class="ej-gate" data-unit="a1" data-nombre="WA1 · JavaScript moderno y asíncrono"></div>

Cada ejercicio parte de un esqueleto. Escribe la solución en un archivo `.js` (o `.mjs`) y ejecútalo con `node archivo.mjs`: tu salida debe ser **idéntica** al resultado esperado, que se ha obtenido ejecutando la solución modelo con Node de verdad. Los ejercicios con `await` en el nivel superior necesitan que el archivo se llame `.mjs`. Los marcados con ⭐ son más difíciles.

## Ejercicio WA1.1

**Desestructurar.** Escribe `resumen` con desestructuración en el parámetro.

```javascript
// Devuelve «Ana: media 7.5 (2 datos extra)». Si no hay notas, «Ana: sin notas (0 datos extra)».
// Usa desestructuración en el parámetro, con notas = [] por defecto, y el resto de propiedades con ...otros.
function resumen(/* ... */) {
  // ...
}
```

Para comprobarlo:

```javascript
console.log(resumen({ nombre: "Ana", notas: [7, 8], curso: "2º", grupo: "A" }));
console.log(resumen({ nombre: "Luis" }));
console.log(resumen({ nombre: "Marta", notas: [10] , edad: 17 }));
```

**Resultado esperado** (ejecutado con Node):

```text
Ana: media 7.5 (2 datos extra)
Luis: sin notas (0 datos extra)
Marta: media 10.0 (1 datos extra)
```

<details class="sol" data-key="web/av-a1/WA1.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>function resumen({ nombre, notas = [], ...otros }) {
  const media = notas.length
    ? (notas.reduce((t, n) =&gt; t + n, 0) / notas.length).toFixed(1)
    : null;
  const extra = Object.keys(otros).length;
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio WA1.2

**`?.` y `??`.** Completa las dos funciones sin escribir ningún `if`.

```javascript
// emailDe: el email del contacto del usuario, o «sin email» si no hay contacto o no tiene email.
// volumen: el volumen de la configuración, o 50 si no está definido (¡pero un 0 es un volumen válido!).
function emailDe(usuario) { /* ... */ }
function volumen(config) { /* ... */ }
```

Para comprobarlo:

```javascript
console.log(emailDe({ contacto: { email: "ana@ejemplo.com" } }));
console.log(emailDe({ contacto: null }));
console.log(emailDe({}));
console.log(emailDe(undefined));
console.log(volumen({ volumen: 0 }), volumen({}), volumen({ volumen: 80 }), volumen(null));
```

**Resultado esperado** (ejecutado con Node):

```text
ana@ejemplo.com
sin email
sin email
sin email
0 50 80 50
```

<details class="sol" data-key="web/av-a1/WA1.2">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>function emailDe(usuario) {
  return usuario?.contacto?.email ?? "sin email";
}
function volumen(config) {
  return config?.volumen ?? 50;
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio WA1.3

**Un cierre.** Escribe `crearHistorial` con un array privado.

```javascript
// crearHistorial devuelve un objeto con tres métodos y un array PRIVADO (nadie de fuera puede tocarlo):
//   añadir(x)  guarda x            deshacer()  quita y devuelve el último (o undefined)
//   ver()      devuelve una COPIA del array (para que no se pueda modificar desde fuera)
function crearHistorial() {
  // ...
}
```

Para comprobarlo:

```javascript
const h = crearHistorial();
h.añadir("a"); h.añadir("b"); h.añadir("c");
console.log(h.deshacer(), h.ver());
const copia = h.ver();
copia.push("intruso");
console.log(h.ver(), h.items);
console.log(crearHistorial().deshacer());
```

**Resultado esperado** (ejecutado con Node):

```text
c [ 'a', 'b' ]
[ 'a', 'b' ] undefined
undefined
```

<details class="sol" data-key="web/av-a1/WA1.3">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>function crearHistorial() {
  const items = [];
  return {
    añadir(x) { items.push(x); },
    deshacer() { return items.pop(); },
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio WA1.4

**Una cosa tras otra.** Escribe `resumenUsuario` con `async/await`. Fíjate en que los pedidos **dependen** del `id` del usuario.

```javascript
const esperar = (ms, valor) => new Promise(r => setTimeout(() => r(valor), ms));
const obtenerUsuario = () => esperar(20, { id: 7, nombre: "Ana" });
const obtenerPedidos = (id) => esperar(20, id === 7 ? ["libro", "lápiz", "mochila"] : []);

// Pide el usuario y, CON su id, sus pedidos. Devuelve «Ana tiene 3 pedidos».
// Si algo falla, devuelve «No se pudo cargar: » seguido del mensaje del error.
async function resumenUsuario() {
  // ...
}
```

Para comprobarlo:

```javascript
console.log(await resumenUsuario());
```

**Resultado esperado** (ejecutado con Node):

```text
Ana tiene 3 pedidos
```

<details class="sol" data-key="web/av-a1/WA1.4">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>const esperar = (ms, valor) =&gt; new Promise(r =&gt; setTimeout(() =&gt; r(valor), ms));
const obtenerUsuario = () =&gt; esperar(20, { id: 7, nombre: "Ana" });
const obtenerPedidos = (id) =&gt; esperar(20, id === 7 ? ["libro", "lápiz", "mochila"] : []);
async function resumenUsuario() {
  try {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio WA1.5

**Que falle uno no hunde a los demás.** Escribe `cargarPaneles` con `Promise.allSettled`.

```javascript
const esperar = (ms, valor, fallar = false) =>
  new Promise((ok, mal) => setTimeout(() => fallar ? mal(new Error(valor)) : ok(valor), ms));

// Recibe una lista de FUNCIONES que devuelven promesas, las lanza a la vez y devuelve
// { correctos: [valores de las que salieron bien], errores: [mensajes de las que fallaron] }, en su orden.
async function cargarPaneles(tareas) {
  // ...
}
```

Para comprobarlo:

```javascript
const r = await cargarPaneles([
  () => esperar(60, "noticias"),
  () => esperar(20, "el tiempo no responde", true),
  () => esperar(30, "agenda"),
  () => esperar(10, "publicidad caída", true)
]);
console.log(r);
```

**Resultado esperado** (ejecutado con Node):

```text
{
  correctos: [ 'noticias', 'agenda' ],
  errores: [ 'el tiempo no responde', 'publicidad caída' ]
}
```

<details class="sol" data-key="web/av-a1/WA1.5">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>const esperar = (ms, valor, fallar = false) =&gt;
  new Promise((ok, mal) =&gt; setTimeout(() =&gt; fallar ? mal(new Error(valor)) : ok(valor), ms));
async function cargarPaneles(tareas) {
  const resultados = await Promise.allSettled(tareas.map(t =&gt; t()));
  return {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio WA1.6

⭐ **Un límite de tiempo.** Escribe `conLimite` con `Promise.race`, sin dejar el temporizador vivo cuando la tarea termina antes.

```javascript
const esperar = (ms, valor) => new Promise(r => setTimeout(() => r(valor), ms));

// Ejecuta tarea() y devuelve su resultado, pero si tarda más de `ms` milisegundos rechaza con
// un Error cuyo mensaje sea «tiempo agotado tras X ms». Debe LIMPIAR el temporizador si la tarea acaba antes.
function conLimite(tarea, ms) {
  // ...
}
```

Para comprobarlo:

```javascript
console.log(await conLimite(() => esperar(20, "a tiempo"), 100));
try { await conLimite(() => esperar(300, "tarde"), 50); }
catch (e) { console.log(e.message); }
```

**Resultado esperado** (ejecutado con Node):

```text
a tiempo
tiempo agotado tras 50 ms
```

<details class="sol" data-key="web/av-a1/WA1.6">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>const esperar = (ms, valor) =&gt; new Promise(r =&gt; setTimeout(() =&gt; r(valor), ms));
function conLimite(tarea, ms) {
  let id;
  const limite = new Promise((_, rechazar) =&gt; {
    id = setTimeout(() =&gt; rechazar(new Error(`tiempo agotado tras ${ms} ms`)), ms);
// ... (resto de la solución bloqueado)</code></pre></div>
</details>
