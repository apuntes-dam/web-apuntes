# WA6.1 Componentes, JSX y props

!!! info "Cómo se han comprobado los ejemplos"
    Aquí no hay `npm` ni paso de compilación: cada editor carga **React, ReactDOM y Babel standalone desde un CDN**, con versiones fijas, igual que cualquier otro script de esta web. Babel traduce el JSX a JavaScript **en el propio navegador**, al vuelo. Es la forma más simple de probar React; en un proyecto real se usa una herramienta como Vite (la tienes en la [WA5](../a5/index.md)).

## Un componente es una función

En la [WA2.2](../a2/02-apis.md#estado-vista-la-idea-detras-de-react-y-vue) viste una función `vista(estado)` que devolvía un *string* de HTML, y una llamada a `innerHTML` que lo pintaba. Un **componente** de React es esa misma idea, con una diferencia: en vez de devolver texto, devuelve **JSX**.

```jsx
function Saludo() {
  return <p>Hola, mundo</p>;
}
```

- El nombre empieza por **mayúscula** (`Saludo`, no `saludo`): así React distingue un componente propio (`<Saludo />`) de una etiqueta HTML normal (`<p>`).
- `<p>Hola, mundo</p>` dentro de la función **parece** HTML, pero no lo es.

## JSX no es HTML: se traduce a JavaScript

JSX es **azúcar sintáctico**. Esto:

```jsx
const elemento = <p className="aviso">Hola</p>;
```

es exactamente esto, traducido por Babel antes de ejecutarse:

```javascript
const elemento = React.createElement("p", { className: "aviso" }, "Hola");
```

`React.createElement(etiqueta, props, hijos)` no toca el DOM: solo describe **qué debería verse**, como un plano. Por eso en JSX se usa `className` y no `class` (es una propiedad de JavaScript, y `class` es palabra reservada) y por eso puedes meter **cualquier expresión de JavaScript** entre llaves `{ }`:

<div class="demo" data-alto="7rem" data-code="&lt;script src=&quot;https://unpkg.com/react@18.3.1/umd/react.development.js&quot;&gt;&lt;/script&gt;&#10;&lt;script src=&quot;https://unpkg.com/react-dom@18.3.1/umd/react-dom.development.js&quot;&gt;&lt;/script&gt;&#10;&lt;script src=&quot;https://unpkg.com/@babel/standalone@7.26.4/babel.min.js&quot;&gt;&lt;/script&gt;&#10;&lt;div id=&quot;app&quot;&gt;&lt;/div&gt;&#10;&lt;script type=&quot;text/babel&quot;&gt;&#10;function Reloj() {&#10;  const ahora = new Date();&#10;  return &lt;p&gt;Son las {ahora.getHours()}:{String(ahora.getMinutes()).padStart(2, &quot;0&quot;)}&lt;/p&gt;;&#10;}&#10;const raiz = ReactDOM.createRoot(document.getElementById(&quot;app&quot;));&#10;raiz.render(&lt;Reloj /&gt;);&#10;&lt;/script&gt;"></div>

`{ahora.getHours()}` es JavaScript normal evaluado en ese punto; fuera de las llaves, todo es JSX.

## Props: pasarle datos a un componente

Un componente sin datos propios no sirve de mucho. Las **props** son los parámetros de esa función, y se pasan como si fueran atributos HTML:

<div class="demo" data-alto="11rem" data-code="&lt;script src=&quot;https://unpkg.com/react@18.3.1/umd/react.development.js&quot;&gt;&lt;/script&gt;&#10;&lt;script src=&quot;https://unpkg.com/react-dom@18.3.1/umd/react-dom.development.js&quot;&gt;&lt;/script&gt;&#10;&lt;script src=&quot;https://unpkg.com/@babel/standalone@7.26.4/babel.min.js&quot;&gt;&lt;/script&gt;&#10;&lt;div id=&quot;app&quot;&gt;&lt;/div&gt;&#10;&lt;script type=&quot;text/babel&quot;&gt;&#10;function Tarjeta({ nombre, lenguaje }) {&#10;  return (&#10;    &lt;div style={{ border: &quot;1px solid #ccc&quot;, borderRadius: 8, padding: 10, marginBottom: 6 }}&gt;&#10;      &lt;strong&gt;{nombre}&lt;/strong&gt; programa en {lenguaje}&#10;    &lt;/div&gt;&#10;  );&#10;}&#10;function App() {&#10;  return (&#10;    &lt;div&gt;&#10;      &lt;Tarjeta nombre=&quot;Ana&quot; lenguaje=&quot;JavaScript&quot; /&gt;&#10;      &lt;Tarjeta nombre=&quot;Luis&quot; lenguaje=&quot;Python&quot; /&gt;&#10;    &lt;/div&gt;&#10;  );&#10;}&#10;const raiz = ReactDOM.createRoot(document.getElementById(&quot;app&quot;));&#10;raiz.render(&lt;App /&gt;);&#10;&lt;/script&gt;"></div>

`{ nombre, lenguaje }` es *destructuring* sobre el único argumento que recibe el componente: un objeto con todas sus props (aquí sería `{nombre: "Ana", lenguaje: "JavaScript"}`). Fíjate también en `style={{ ... }}`: las primeras llaves son "aquí va JavaScript" y las segundas son el objeto de estilos en sí; por eso son dobles.

!!! warning "Las props no se modifican"
    Un componente nunca debe cambiar las props que recibe: son de **quien lo usa**. Si `Tarjeta` necesitara cambiar algo con el tiempo, eso es **estado**, no una prop — lo ves en la [WA6.2](02-estado-hooks.md).

## Listas: `.map` y la `key`

Para pintar una lista, se recorre un array con `.map` y cada elemento se convierte en JSX. React necesita que cada elemento de la lista lleve una prop especial, `key`, con un valor **único y estable**, para saber cuál es cuál si la lista cambia:

<div class="demo" data-alto="9rem" data-code="&lt;script src=&quot;https://unpkg.com/react@18.3.1/umd/react.development.js&quot;&gt;&lt;/script&gt;&#10;&lt;script src=&quot;https://unpkg.com/react-dom@18.3.1/umd/react-dom.development.js&quot;&gt;&lt;/script&gt;&#10;&lt;script src=&quot;https://unpkg.com/@babel/standalone@7.26.4/babel.min.js&quot;&gt;&lt;/script&gt;&#10;&lt;div id=&quot;app&quot;&gt;&lt;/div&gt;&#10;&lt;script type=&quot;text/babel&quot;&gt;&#10;const alumnos = [&#10;  { id: 1, nombre: &quot;Ana&quot; },&#10;  { id: 2, nombre: &quot;Luis&quot; },&#10;  { id: 3, nombre: &quot;Eva&quot; },&#10;];&#10;function Lista() {&#10;  return (&#10;    &lt;ul&gt;&#10;      {alumnos.map(a =&gt; &lt;li key={a.id}&gt;{a.nombre}&lt;/li&gt;)}&#10;    &lt;/ul&gt;&#10;  );&#10;}&#10;const raiz = ReactDOM.createRoot(document.getElementById(&quot;app&quot;));&#10;raiz.render(&lt;Lista /&gt;);&#10;&lt;/script&gt;"></div>

Si quitas `key={a.id}` (pruébalo: borra esos cinco caracteres y mira la consola), la lista se sigue viendo igual, pero React avisa por consola:

<div class="demo" data-alto="11rem" data-consola="1" data-code="&lt;script src=&quot;https://unpkg.com/react@18.3.1/umd/react.development.js&quot;&gt;&lt;/script&gt;&#10;&lt;script src=&quot;https://unpkg.com/react-dom@18.3.1/umd/react-dom.development.js&quot;&gt;&lt;/script&gt;&#10;&lt;script src=&quot;https://unpkg.com/@babel/standalone@7.26.4/babel.min.js&quot;&gt;&lt;/script&gt;&#10;&lt;div id=&quot;app&quot;&gt;&lt;/div&gt;&#10;&lt;script type=&quot;text/babel&quot;&gt;&#10;const alumnos = [&#10;  { id: 1, nombre: &quot;Ana&quot; },&#10;  { id: 2, nombre: &quot;Luis&quot; },&#10;  { id: 3, nombre: &quot;Eva&quot; },&#10;];&#10;function Lista() {&#10;  return (&#10;    &lt;ul&gt;&#10;      {alumnos.map(a =&gt; &lt;li&gt;{a.nombre}&lt;/li&gt;)}&#10;    &lt;/ul&gt;&#10;  );&#10;}&#10;const raiz = ReactDOM.createRoot(document.getElementById(&quot;app&quot;));&#10;raiz.render(&lt;Lista /&gt;);&#10;&lt;/script&gt;"></div>

`key` no es una prop que reciba tu componente (no aparece en `props`): es información **solo para React**, para que al reordenar, añadir o quitar elementos sepa cuál es cuál en vez de repintar la lista entera. Usa un id real de tus datos (nunca el índice del array si la lista puede reordenarse o filtrarse: entonces el índice "miente").

## Renderizado condicional

Como el cuerpo de un componente es JavaScript, mostrar algo u otro es un `if`, un operador ternario o `&&`:

<div class="demo" data-alto="7rem" data-code="&lt;script src=&quot;https://unpkg.com/react@18.3.1/umd/react.development.js&quot;&gt;&lt;/script&gt;&#10;&lt;script src=&quot;https://unpkg.com/react-dom@18.3.1/umd/react-dom.development.js&quot;&gt;&lt;/script&gt;&#10;&lt;script src=&quot;https://unpkg.com/@babel/standalone@7.26.4/babel.min.js&quot;&gt;&lt;/script&gt;&#10;&lt;div id=&quot;app&quot;&gt;&lt;/div&gt;&#10;&lt;script type=&quot;text/babel&quot;&gt;&#10;function Estado({ conectado }) {&#10;  return (&#10;    &lt;p&gt;&#10;      {conectado ? &quot;🟢 Conectado&quot; : &quot;🔴 Desconectado&quot;}&#10;      {conectado &amp;&amp; &quot; (todo en orden)&quot;}&#10;    &lt;/p&gt;&#10;  );&#10;}&#10;const raiz = ReactDOM.createRoot(document.getElementById(&quot;app&quot;));&#10;raiz.render(&lt;div&gt;&lt;Estado conectado={true} /&gt;&lt;Estado conectado={false} /&gt;&lt;/div&gt;);&#10;&lt;/script&gt;"></div>

`conectado && "..."` solo se muestra si `conectado` es verdadero; si es falso, la expresión vale `false`, y React no pinta `false`, `null` ni `undefined` (pero sí pintaría `0`: ten cuidado con `cantidad && <Lista />` cuando `cantidad` puede ser `0`).
