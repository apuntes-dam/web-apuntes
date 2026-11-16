# WA6 · Ejercicios de React

<div class="ej-gate" data-unit="a6" data-nombre="WA6 · React"></div>

Todos son de DOM: ábrelos como editor en vivo y compruébalos en el navegador; no tienen salida de consola. El WA6.5 está marcado con ⭐ por ser el más conceptual.

## Ejercicio WA6.1

**Tarjeta de alumno.** Escribe el componente `Tarjeta`, que recibe las props `nombre` y `curso`, y pinta `<strong>{nombre}</strong> — {curso}` dentro de un `<div>`. Luego úsalo dos veces en `App` con alumnos distintos.

```html
<script src="https://unpkg.com/react@18.3.1/umd/react.development.js"></script>
<script src="https://unpkg.com/react-dom@18.3.1/umd/react-dom.development.js"></script>
<script src="https://unpkg.com/@babel/standalone@7.26.4/babel.min.js"></script>
<div id="app"></div>
<script type="text/babel">
function Tarjeta(/* ...props... */) {
  // tu código aquí
}
function App() {
  return (
    <div>
      {/* usa <Tarjeta ... /> dos veces, con nombre y curso distintos */}
    </div>
  );
}
const raiz = ReactDOM.createRoot(document.getElementById("app"));
raiz.render(<App />);
</script>
```

<details class="sol" data-key="web/av-a6/WA6.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>&lt;script src="https://unpkg.com/react@18.3.1/umd/react.development.js"&gt;&lt;/script&gt;
&lt;script src="https://unpkg.com/react-dom@18.3.1/umd/react-dom.development.js"&gt;&lt;/script&gt;
&lt;script src="https://unpkg.com/@babel/standalone@7.26.4/babel.min.js"&gt;&lt;/script&gt;
&lt;div id="app"&gt;&lt;/div&gt;
&lt;script type="text/babel"&gt;
function Tarjeta({ nombre, curso }) {
&lt;!-- ... (resto de la solución bloqueado) --&gt;</code></pre></div>
</details>

## Ejercicio WA6.2

**Lista de lenguajes.** Tienes un array de objetos `{ id, nombre }`. Píntalos en una `<ul>` con `.map`, cada uno en su `<li>`, con la `key` puesta correctamente (y sin usar el índice del array).

```html
<script src="https://unpkg.com/react@18.3.1/umd/react.development.js"></script>
<script src="https://unpkg.com/react-dom@18.3.1/umd/react-dom.development.js"></script>
<script src="https://unpkg.com/@babel/standalone@7.26.4/babel.min.js"></script>
<div id="app"></div>
<script type="text/babel">
const lenguajes = [
  { id: 1, nombre: "JavaScript" },
  { id: 2, nombre: "Python" },
  { id: 3, nombre: "Kotlin" },
];
function Lista() {
  return (
    <ul>
      {/* tu .map aquí */}
    </ul>
  );
}
const raiz = ReactDOM.createRoot(document.getElementById("app"));
raiz.render(<Lista />);
</script>
```

<details class="sol" data-key="web/av-a6/WA6.2">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>&lt;script src="https://unpkg.com/react@18.3.1/umd/react.development.js"&gt;&lt;/script&gt;
&lt;script src="https://unpkg.com/react-dom@18.3.1/umd/react-dom.development.js"&gt;&lt;/script&gt;
&lt;script src="https://unpkg.com/@babel/standalone@7.26.4/babel.min.js"&gt;&lt;/script&gt;
&lt;div id="app"&gt;&lt;/div&gt;
&lt;script type="text/babel"&gt;
const lenguajes = [
&lt;!-- ... (resto de la solución bloqueado) --&gt;</code></pre></div>
</details>

## Ejercicio WA6.3

**Contador con límite.** Escribe `Contador`: un botón «+1» que suma, y que al llegar a 5 se deshabilite (`disabled`) y muestre «¡Máximo alcanzado!» debajo.

```html
<script src="https://unpkg.com/react@18.3.1/umd/react.development.js"></script>
<script src="https://unpkg.com/react-dom@18.3.1/umd/react-dom.development.js"></script>
<script src="https://unpkg.com/@babel/standalone@7.26.4/babel.min.js"></script>
<div id="app"></div>
<script type="text/babel">
function Contador() {
  // tu estado aquí
  return (
    <div>
      {/* valor, botón y aviso */}
    </div>
  );
}
const raiz = ReactDOM.createRoot(document.getElementById("app"));
raiz.render(<Contador />);
</script>
```

<details class="sol" data-key="web/av-a6/WA6.3">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>&lt;script src="https://unpkg.com/react@18.3.1/umd/react.development.js"&gt;&lt;/script&gt;
&lt;script src="https://unpkg.com/react-dom@18.3.1/umd/react-dom.development.js"&gt;&lt;/script&gt;
&lt;script src="https://unpkg.com/@babel/standalone@7.26.4/babel.min.js"&gt;&lt;/script&gt;
&lt;div id="app"&gt;&lt;/div&gt;
&lt;script type="text/babel"&gt;
function Contador() {
&lt;!-- ... (resto de la solución bloqueado) --&gt;</code></pre></div>
</details>

## Ejercicio WA6.4

**Contador de caracteres.** Un `<textarea>` controlado por estado que muestra cuántos caracteres llevas escritos, y en rojo si pasas de 20.

```html
<script src="https://unpkg.com/react@18.3.1/umd/react.development.js"></script>
<script src="https://unpkg.com/react-dom@18.3.1/umd/react-dom.development.js"></script>
<script src="https://unpkg.com/@babel/standalone@7.26.4/babel.min.js"></script>
<div id="app"></div>
<script type="text/babel">
function Editor() {
  // tu estado aquí
  return (
    <div>
      {/* textarea controlado + contador */}
    </div>
  );
}
const raiz = ReactDOM.createRoot(document.getElementById("app"));
raiz.render(<Editor />);
</script>
```

<details class="sol" data-key="web/av-a6/WA6.4">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>&lt;script src="https://unpkg.com/react@18.3.1/umd/react.development.js"&gt;&lt;/script&gt;
&lt;script src="https://unpkg.com/react-dom@18.3.1/umd/react-dom.development.js"&gt;&lt;/script&gt;
&lt;script src="https://unpkg.com/@babel/standalone@7.26.4/babel.min.js"&gt;&lt;/script&gt;
&lt;div id="app"&gt;&lt;/div&gt;
&lt;script type="text/babel"&gt;
function Editor() {
&lt;!-- ... (resto de la solución bloqueado) --&gt;</code></pre></div>
</details>

## Ejercicio WA6.5

**⭐ Arregla el contador doble.** Este `Doble` debería sumar 2 de golpe con un solo clic, pero (como viste en la teoría) solo sube de 1 en 1. Arréglalo sin tocar nada fuera de `sumarDos`.

```html
<script src="https://unpkg.com/react@18.3.1/umd/react.development.js"></script>
<script src="https://unpkg.com/react-dom@18.3.1/umd/react-dom.development.js"></script>
<script src="https://unpkg.com/@babel/standalone@7.26.4/babel.min.js"></script>
<div id="app"></div>
<script type="text/babel">
function Doble() {
  const [cuenta, setCuenta] = React.useState(0);
  function sumarDos() {
    setCuenta(cuenta + 1);
    setCuenta(cuenta + 1);
  }
  return (
    <div>
      <p>Valor: {cuenta}</p>
      <button onClick={sumarDos}>Sumar 2</button>
    </div>
  );
}
const raiz = ReactDOM.createRoot(document.getElementById("app"));
raiz.render(<Doble />);
</script>
```

<details class="sol" data-key="web/av-a6/WA6.5">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>&lt;script src="https://unpkg.com/react@18.3.1/umd/react.development.js"&gt;&lt;/script&gt;
&lt;script src="https://unpkg.com/react-dom@18.3.1/umd/react-dom.development.js"&gt;&lt;/script&gt;
&lt;script src="https://unpkg.com/@babel/standalone@7.26.4/babel.min.js"&gt;&lt;/script&gt;
&lt;div id="app"&gt;&lt;/div&gt;
&lt;script type="text/babel"&gt;
function Doble() {
&lt;!-- ... (resto de la solución bloqueado) --&gt;</code></pre></div>
</details>
