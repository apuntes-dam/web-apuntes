# WA4 · Ejercicios de movimiento, accesibilidad y rendimiento

<div class="ej-gate" data-unit="a4" data-nombre="WA4 · Movimiento, accesibilidad y rendimiento"></div>

Los ejercicios WA4.1 a WA4.4 son de JavaScript sin DOM: escríbelos en un `.js`/`.mjs` y ejecútalos con `node`; tu salida debe ser **idéntica** al resultado esperado, obtenido ejecutando la solución modelo con Node de verdad. Los WA4.5 y WA4.6 son de HTML/DOM: se hacen en una página y **se comprueban en el navegador**. Los marcados con ⭐ son más difíciles.

## Ejercicio WA4.1

**Calcular el contraste.** Completa `contraste` (la función `luminancia` ya está hecha).

```javascript
function luminancia(hex) {
  const [r, g, b] = [1, 3, 5].map(i => parseInt(hex.slice(i, i + 2), 16) / 255)
    .map(c => c <= 0.03928 ? c / 12.92 : ((c + 0.055) / 1.055) ** 2.4);
  return 0.2126 * r + 0.7152 * g + 0.0722 * b;
}

// contraste(a, b): la proporción de contraste WCAG entre dos colores #rrggbb, SIEMPRE mayor o igual que 1
// (el color más claro va en el numerador): (Lclaro + 0.05) / (Loscuro + 0.05)
function contraste(a, b) {
  // ...
}
```

Para comprobarlo:

```javascript
console.log(contraste("#000000", "#ffffff").toFixed(2));
console.log(contraste("#ffffff", "#000000").toFixed(2));
console.log(contraste("#777777", "#ffffff").toFixed(2));
console.log(contraste("#ffffff", "#2563eb").toFixed(2));
```

**Resultado esperado** (ejecutado con Node):

```text
21.00
21.00
4.48
5.17
```

<details class="sol" data-key="web/av-a4/WA4.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>function luminancia(hex) {
  const [r, g, b] = [1, 3, 5].map(i =&gt; parseInt(hex.slice(i, i + 2), 16) / 255)
    .map(c =&gt; c &lt;= 0.03928 ? c / 12.92 : ((c + 0.055) / 1.055) ** 2.4);
  return 0.2126 * r + 0.7152 * g + 0.0722 * b;
}
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio WA4.2

**¿Pasa la prueba?** Escribe `nivel`.

```javascript
// (usa la función contraste del ejercicio anterior)
// nivel(texto, fondo, grande = false) devuelve "AAA", "AA" o "suspenso":
//   texto normal:  AAA si contraste >= 7,   AA si >= 4.5
//   texto grande:  AAA si contraste >= 4.5, AA si >= 3
function nivel(texto, fondo, grande = false) {
  // ...
}
```

Para comprobarlo:

```javascript
console.log(nivel("#000000", "#ffffff"));
console.log(nivel("#767676", "#ffffff"));
console.log(nivel("#777777", "#ffffff"));
console.log(nivel("#777777", "#ffffff", true));
console.log(nivel("#aaaaaa", "#ffffff", true));
```

**Resultado esperado** (ejecutado con Node):

```text
AAA
AA
suspenso
AA
suspenso
```

<details class="sol" data-key="web/av-a4/WA4.2">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>function luminancia(hex) {
  const [r, g, b] = [1, 3, 5].map(i =&gt; parseInt(hex.slice(i, i + 2), 16) / 255)
    .map(c =&gt; c &lt;= 0.03928 ? c / 12.92 : ((c + 0.055) / 1.055) ** 2.4);
  return 0.2126 * r + 0.7152 * g + 0.0722 * b;
}
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio WA4.3

**`debounce`.** Escríbelo desde cero.

```javascript
// debounce(fn, ms): devuelve una función que, al llamarla, espera `ms` milisegundos SIN nuevas llamadas
// y entonces ejecuta fn UNA vez, con los argumentos de la ÚLTIMA llamada.
function debounce(fn, ms) {
  // ...
}
```

Para comprobarlo:

```javascript
const dormir = ms => new Promise(r => setTimeout(r, ms));
const vistos = [];
const guardar = debounce(valor => vistos.push(valor), 60);

for (const v of ["a", "b", "c"]) { guardar(v); await dormir(10); }
await dormir(150);
guardar("d");
await dormir(10);
guardar("e");
await dormir(150);
console.log(vistos);
```

**Resultado esperado** (ejecutado con Node):

```text
[ 'c', 'e' ]
```

<details class="sol" data-key="web/av-a4/WA4.3">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>function debounce(fn, ms) {
  let id;
  return (...args) =&gt; {
    clearTimeout(id);
    id = setTimeout(() =&gt; fn(...args), ms);
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio WA4.4

**`throttle`.** Escríbelo desde cero, con un reloj inyectado para poder probarlo sin esperar.

```javascript
// throttle(fn, ms, ahora = Date.now): devuelve una función que ejecuta fn como mucho UNA vez cada `ms` ms:
// la primera llamada pasa; las siguientes se ignoran hasta que hayan pasado `ms` desde la última que SÍ pasó.
// Devuelve lo que devuelve fn cuando se ejecuta, y undefined cuando se ignora la llamada.
function throttle(fn, ms, ahora = Date.now) {
  // ...
}
```

Para comprobarlo:

```javascript
let reloj = 0;
const f = throttle(x => "pasó " + x, 100, () => reloj);
for (const [t, x] of [[0, "A"], [50, "B"], [99, "C"], [100, "D"], [150, "E"], [199, "F"], [200, "G"]]) {
  reloj = t;
  console.log(t, "→", f(x));
}
```

**Resultado esperado** (ejecutado con Node):

```text
0 → pasó A
50 → undefined
99 → undefined
100 → pasó D
150 → undefined
199 → undefined
200 → pasó G
```

<details class="sol" data-key="web/av-a4/WA4.4">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>function throttle(fn, ms, ahora = Date.now) {
  let ultima = -Infinity;
  return (...args) =&gt; {
    const t = ahora();
    if (t - ultima &gt;= ms) {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio WA4.5

**Un menú desplegable accesible.** *(DOM: se comprueba en el navegador.)* Con este HTML, escribe el JavaScript para que:

* el botón **abra y cierre** el menú, manteniendo **`aria-expanded`** sincronizado con su estado (el menú se oculta con el atributo `hidden`);
* **Escape** cierre el menú **y devuelva el foco al botón**;
* un clic **fuera** del menú lo cierre.

Comprueba todo **solo con el teclado** (Tab, Intro, Escape).

```html
<button id="boton" aria-expanded="false" aria-controls="menu">Menú</button>
<ul id="menu" hidden>
  <li><a href="#inicio">Inicio</a></li>
  <li><a href="#cursos">Cursos</a></li>
  <li><a href="#contacto">Contacto</a></li>
</ul>
```

<details class="sol" data-key="web/av-a4/WA4.5">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>&lt;button id="boton" aria-expanded="false" aria-controls="menu"&gt;Menú&lt;/button&gt;
&lt;ul id="menu" hidden&gt;
  &lt;li&gt;&lt;a href="#inicio"&gt;Inicio&lt;/a&gt;&lt;/li&gt;
  &lt;li&gt;&lt;a href="#cursos"&gt;Cursos&lt;/a&gt;&lt;/li&gt;
  &lt;li&gt;&lt;a href="#contacto"&gt;Contacto&lt;/a&gt;&lt;/li&gt;
&lt;/ul&gt;
&lt;!-- ... (resto de la solución bloqueado) --&gt;</code></pre></div>
</details>

## Ejercicio WA4.6

⭐ **Una imagen responsiva.** *(HTML: se comprueba en las herramientas del navegador.)* Escribe el HTML de una imagen de portada que:

* ofrezca **tres anchos** (`480w`, `960w` y `1600w`) con `srcset`, y que ocupe **toda la pantalla en móviles y la mitad a partir de 60 rem** (`sizes`),
* ofrezca el formato **WebP** con `<picture>` y **JPG** como alternativa,
* **reserve su hueco** (proporción 16:9, 1600 × 900) para evitar saltos de diseño,
* tenga un **texto alternativo** descriptivo,
* sea **la imagen principal de la página**: no debe cargarse de forma perezosa y debe tener **prioridad alta**.

*(Los archivos no existen: lo que se comprueba es la estructura. En la pestaña «Red» de las herramientas del navegador verás qué archivos intenta pedir.)*

<details class="sol" data-key="web/av-a4/WA4.6">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>&lt;picture&gt;
  &lt;source type="image/webp"
          srcset="portada-480.webp 480w, portada-960.webp 960w, portada-1600.webp 1600w"
          sizes="(min-width: 60rem) 50vw, 100vw"&gt;
  &lt;img src="portada-960.jpg"
       srcset="portada-480.jpg 480w, portada-960.jpg 960w, portada-1600.jpg 1600w"
&lt;!-- ... (resto de la solución bloqueado) --&gt;</code></pre></div>
</details>
