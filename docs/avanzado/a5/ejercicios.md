# WA5 · Ejercicios de herramientas, seguridad y despliegue

<div class="ej-gate" data-unit="a5" data-nombre="WA5 · Herramientas, seguridad y despliegue"></div>

Los ejercicios WA5.1, WA5.2 y WA5.5 son de JavaScript sin DOM: escríbelos en un `.js`/`.mjs` y ejecútalos con `node`; tu salida debe ser **idéntica** al resultado esperado, obtenido ejecutando la solución modelo con Node de verdad. El WA5.4 se comprueba ejecutando tus pruebas con `node --test`. Los WA5.3 (`package.json`) y WA5.6 (flujo de GitHub Actions) son de configuración: **no se pueden ejecutar aquí**; compáralos con la solución modelo. Los marcados con ⭐ son más difíciles.

## Ejercicio WA5.1

**Escapar HTML.** Escribe `escapar`.

```javascript
// escapar(texto): convierte & < > " ' en sus entidades (&amp; &lt; &gt; &quot; &#39;) para poder meter el texto en HTML sin riesgo.
// El & debe sustituirse a la vez que los demás (no después, o se duplicaría). Cualquier valor se trata como texto (String(...)).
function escapar(texto) {
  // ...
}
```

Para comprobarlo:

```javascript
console.log(escapar("<script>alert('xss')</script>"));
console.log(escapar('Tom & "Jerry"'));
console.log(escapar(42), escapar(null));
console.log(escapar("&lt;"));
```

**Resultado esperado** (ejecutado con Node):

```text
&lt;script&gt;alert(&#39;xss&#39;)&lt;/script&gt;
Tom &amp; &quot;Jerry&quot;
42 null
&amp;lt;
```

<details class="sol" data-key="web/av-a5/WA5.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>function escapar(texto) {
  return String(texto).replace(/[&amp;&lt;&gt;"']/g, c =&gt;
    ({ "&amp;": "&amp;amp;", "&lt;": "&amp;lt;", "&gt;": "&amp;gt;", '"': "&amp;quot;", "'": "&amp;#39;" }[c]));
}
console.log(escapar("&lt;script&gt;alert('xss')&lt;/script&gt;"));
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio WA5.2

**Solo enlaces seguros.** Escribe `enlaceSeguro` con la clase `URL`.

```javascript
// enlaceSeguro(texto): devuelve la URL normalizada (url.href) si el protocolo es http:, https: o mailto:,
// y null en cualquier otro caso (javascript:, data:, un texto que no es una URL...). Sin usar expresiones regulares.
function enlaceSeguro(texto) {
  // ...
}
```

Para comprobarlo:

```javascript
for (const t of ["https://ejemplo.com/a b", "HTTP://EJEMPLO.COM", "mailto:ana@ejemplo.com", "javascript:alert(1)", "  javascript:alert(1)", "ftp://ejemplo.com", "/ruta/relativa"]) {
  console.log(JSON.stringify(t), "→", enlaceSeguro(t));
}
```

**Resultado esperado** (ejecutado con Node):

```text
"https://ejemplo.com/a b" → https://ejemplo.com/a%20b
"HTTP://EJEMPLO.COM" → http://ejemplo.com/
"mailto:ana@ejemplo.com" → mailto:ana@ejemplo.com
"javascript:alert(1)" → null
"  javascript:alert(1)" → null
"ftp://ejemplo.com" → null
"/ruta/relativa" → null
```

<details class="sol" data-key="web/av-a5/WA5.2">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>function enlaceSeguro(texto) {
  try {
    const url = new URL(texto);
    return ["http:", "https:", "mailto:"].includes(url.protocol) ? url.href : null;
  } catch {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio WA5.3

**Un `package.json` completo.** Escribe el `package.json` de un proyecto llamado `calculadora` (versión `0.1.0`) que:

* use **módulos ES** (`"type": "module"`),
* tenga los scripts `start` (ejecuta `node src/index.js`), `test` (ejecuta las pruebas con el ejecutor de Node), `lint` (`eslint .`) y `format` (`prettier --write .`),
* tenga **una dependencia** de ejecución, `dayjs` en `^1.11.0`, y **dos de desarrollo**, `eslint` en `^9.0.0` y `prettier` en `~3.3.0`.

*Este ejercicio es de configuración: no hay salida que comparar. Compara tu solución con la modelo, y comprueba con `npm run` que ves los cuatro scripts.*

<details class="sol" data-key="web/av-a5/WA5.3">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>{
  "name": "calculadora",
  "version": "0.1.0",
  "type": "module",
  "scripts": {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio WA5.4

**Tus primeras pruebas con `node --test`.** Estas dos funciones están en `primos.mjs`. Escribe `primos.test.mjs` con **cuatro pruebas** (usa `node:test` y `node:assert/strict`):

1. `esPrimo` devuelve `true` para 2, 13 y 97.
2. `esPrimo` devuelve `false` para 0, 1, 15 y para un decimal como 2.5.
3. `primerosPrimos(5)` es `[2, 3, 5, 7, 11]` (usa `deepEqual`).
4. `primerosPrimos(-1)` lanza un `RangeError`.

```javascript
// primos.mjs
export function esPrimo(n) {
  if (!Number.isInteger(n) || n < 2) return false;
  for (let i = 2; i * i <= n; i++) if (n % i === 0) return false;
  return true;
}

export function primerosPrimos(cuantos) {
  if (cuantos < 0) throw new RangeError("cuantos no puede ser negativo");
  const lista = [];
  for (let n = 2; lista.length < cuantos; n++) if (esPrimo(n)) lista.push(n);
  return lista;
}
```

Ejecútalas con `node --test primos.test.mjs`.

**Resultado esperado** al ejecutar `node --test primos.test.mjs`:

```text
✔ esPrimo acepta primos
✔ esPrimo rechaza los que no lo son
✔ primerosPrimos(5)
✔ primerosPrimos con un número negativo lanza RangeError
ℹ tests 4
ℹ pass 4
ℹ fail 0
```

<details class="sol" data-key="web/av-a5/WA5.4">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import { test } from "node:test";
import assert from "node:assert/strict";
import { esPrimo, primerosPrimos } from "./primos.mjs";
test("esPrimo acepta primos", () =&gt; {
  for (const n of [2, 13, 97]) assert.equal(esPrimo(n), true);
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio WA5.5

**Cabeceras de CORS.** Escribe `cabecerasCors`: solo los orígenes de la lista reciben permiso, y nunca `*`.

```javascript
// cabecerasCors(origen, permitidos): devuelve un OBJETO con las cabeceras de CORS que debe enviar el servidor.
//  - Si `origen` está en el array `permitidos`: { "Access-Control-Allow-Origin": origen, "Vary": "Origin" }
//  - Si no está (o es undefined): un objeto vacío {}  (NUNCA "*", NUNCA el origen sin comprobar)
function cabecerasCors(origen, permitidos) {
  // ...
}
```

Para comprobarlo:

```javascript
const ok = ["https://mi-web.es", "https://admin.mi-web.es"];
console.log(cabecerasCors("https://mi-web.es", ok));
console.log(cabecerasCors("https://admin.mi-web.es", ok));
console.log(cabecerasCors("https://mi-web.es.malvado.com", ok));
console.log(cabecerasCors(undefined, ok));
```

**Resultado esperado** (ejecutado con Node):

```text
{ 'Access-Control-Allow-Origin': 'https://mi-web.es', Vary: 'Origin' }
{
  'Access-Control-Allow-Origin': 'https://admin.mi-web.es',
  Vary: 'Origin'
}
{}
{}
```

<details class="sol" data-key="web/av-a5/WA5.5">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>function cabecerasCors(origen, permitidos) {
  if (origen !== undefined &amp;&amp; permitidos.includes(origen)) {
    return { "Access-Control-Allow-Origin": origen, "Vary": "Origin" };
  }
  return {};
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio WA5.6

⭐ **Publicar solo si pasan las pruebas.** Escribe el archivo `.github/workflows/web.yml` de un proyecto con `npm` y Vite que, **en cada `push` a `main`**:

* instale las dependencias **de forma exacta** (con el archivo de bloqueo),
* **ejecute las pruebas**,
* **construya** la web (`npm run build`, que deja el resultado en `dist/`),
* y **solo si todo lo anterior ha ido bien**, publique `dist/` en GitHub Pages.

*Es un archivo de configuración: no se puede ejecutar aquí. Compáralo con la solución modelo; comprueba el orden de los pasos y que la publicación sea un trabajo separado que **dependa** del anterior.*

<details class="sol" data-key="web/av-a5/WA5.6">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>name: Publicar web
on:
  push:
    branches: [main]
permissions:
# ... (resto de la solución bloqueado)</code></pre></div>
</details>
