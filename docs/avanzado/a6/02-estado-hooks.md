# WA6.2 Estado y eventos con hooks

!!! info "Cómo se han comprobado los ejemplos"
    Aquí no hay `npm` ni paso de compilación: cada editor carga **React, ReactDOM y Babel standalone desde un CDN**, con versiones fijas, igual que cualquier otro script de esta web. Babel traduce el JSX a JavaScript **en el propio navegador**, al vuelo. Es la forma más simple de probar React; en un proyecto real se usa una herramienta como Vite (la tienes en la [WA5](../a5/index.md)).

## `useState`: la memoria de un componente

Una variable normal de JavaScript se **olvida** cada vez que el componente se vuelve a ejecutar. Para que un componente **recuerde** algo entre repintados (y para que React sepa que debe repintar cuando cambia), se usa el *hook* `useState`:

<div class="demo" data-alto="9rem" data-code="&lt;script src=&quot;https://unpkg.com/react@18.3.1/umd/react.development.js&quot;&gt;&lt;/script&gt;&#10;&lt;script src=&quot;https://unpkg.com/react-dom@18.3.1/umd/react-dom.development.js&quot;&gt;&lt;/script&gt;&#10;&lt;script src=&quot;https://unpkg.com/@babel/standalone@7.26.4/babel.min.js&quot;&gt;&lt;/script&gt;&#10;&lt;div id=&quot;app&quot;&gt;&lt;/div&gt;&#10;&lt;script type=&quot;text/babel&quot;&gt;&#10;function Contador() {&#10;  const [cuenta, setCuenta] = React.useState(0);&#10;  return (&#10;    &lt;div&gt;&#10;      &lt;p&gt;Valor: {cuenta}&lt;/p&gt;&#10;      &lt;button onClick={() =&gt; setCuenta(cuenta + 1)}&gt;+1&lt;/button&gt;&#10;      &lt;button onClick={() =&gt; setCuenta(cuenta - 1)}&gt;-1&lt;/button&gt;&#10;    &lt;/div&gt;&#10;  );&#10;}&#10;const raiz = ReactDOM.createRoot(document.getElementById(&quot;app&quot;));&#10;raiz.render(&lt;Contador /&gt;);&#10;&lt;/script&gt;"></div>

`useState(0)` devuelve un array de dos elementos: el **valor actual** (`cuenta`) y una **función para cambiarlo** (`setCuenta`). Nunca se hace `cuenta = cuenta + 1` directamente: hay que llamar a `setCuenta`, porque es esa llamada la que le dice a React "algo cambió, vuelve a pintar".

Esto es la misma idea que el `estado`/`reducir` de la WA2.2, pero React se encarga de llamar a tu función de vista por ti cada vez que cambias el estado: ya no hace falta una función `pintar()` propia.

## Eventos: `onClick`, `onChange` y los inputs controlados

Los eventos en JSX se escriben en estilo *camelCase* (`onClick`, no `onclick`) y reciben **una función**, nunca el resultado de llamarla (por eso `onClick={() => setCuenta(cuenta + 1)}`, con flecha, y no `onClick={setCuenta(cuenta + 1)}`, que la llamaría al pintar, en bucle). Para un campo de texto, el patrón habitual es el **input controlado**: el valor del input vive en el estado, y cada tecla lo actualiza:

<div class="demo" data-alto="9rem" data-code="&lt;script src=&quot;https://unpkg.com/react@18.3.1/umd/react.development.js&quot;&gt;&lt;/script&gt;&#10;&lt;script src=&quot;https://unpkg.com/react-dom@18.3.1/umd/react-dom.development.js&quot;&gt;&lt;/script&gt;&#10;&lt;script src=&quot;https://unpkg.com/@babel/standalone@7.26.4/babel.min.js&quot;&gt;&lt;/script&gt;&#10;&lt;div id=&quot;app&quot;&gt;&lt;/div&gt;&#10;&lt;script type=&quot;text/babel&quot;&gt;&#10;function Nombre() {&#10;  const [texto, setTexto] = React.useState(&quot;&quot;);&#10;  return (&#10;    &lt;div&gt;&#10;      &lt;input value={texto} onChange={e =&gt; setTexto(e.target.value)} placeholder=&quot;Escribe tu nombre&quot; /&gt;&#10;      &lt;p&gt;Hola, {texto || &quot;desconocido&quot;}. Llevas {texto.length} caracteres.&lt;/p&gt;&#10;    &lt;/div&gt;&#10;  );&#10;}&#10;const raiz = ReactDOM.createRoot(document.getElementById(&quot;app&quot;));&#10;raiz.render(&lt;Nombre /&gt;);&#10;&lt;/script&gt;"></div>

`value={texto}` hace que el input **siempre** muestre lo que dice el estado; `onChange` actualiza el estado con lo que el usuario acaba de escribir. Entre las dos cosas, React vuelve a pintar y el input se "actualiza a sí mismo". Si quitaras `value`, el input seguiría funcionando (es HTML normal), pero React ya no sabría qué hay escrito: dejaría de ser *controlado*.

## La trampa: `setEstado` no cambia el valor al momento

<div class="demo" data-alto="9rem" data-code="&lt;script src=&quot;https://unpkg.com/react@18.3.1/umd/react.development.js&quot;&gt;&lt;/script&gt;&#10;&lt;script src=&quot;https://unpkg.com/react-dom@18.3.1/umd/react-dom.development.js&quot;&gt;&lt;/script&gt;&#10;&lt;script src=&quot;https://unpkg.com/@babel/standalone@7.26.4/babel.min.js&quot;&gt;&lt;/script&gt;&#10;&lt;div id=&quot;app&quot;&gt;&lt;/div&gt;&#10;&lt;script type=&quot;text/babel&quot;&gt;&#10;function ContadorRoto() {&#10;  const [cuenta, setCuenta] = React.useState(0);&#10;  function sumarDos() {&#10;    setCuenta(cuenta + 1);&#10;    setCuenta(cuenta + 1);      // &quot;cuenta&quot; aquí sigue siendo la de ANTES de pulsar&#10;  }&#10;  return (&#10;    &lt;div&gt;&#10;      &lt;p&gt;Valor: {cuenta}&lt;/p&gt;&#10;      &lt;button onClick={sumarDos}&gt;Sumar 2 (no funciona)&lt;/button&gt;&#10;    &lt;/div&gt;&#10;  );&#10;}&#10;const raiz = ReactDOM.createRoot(document.getElementById(&quot;app&quot;));&#10;raiz.render(&lt;ContadorRoto /&gt;);&#10;&lt;/script&gt;"></div>

Pulsa el botón: el contador sube de **1 en 1**, no de 2 en 2. Dentro de `sumarDos`, `cuenta` es una constante capturada en **ese repintado concreto**: las dos llamadas a `setCuenta(cuenta + 1)` parten del mismo valor viejo, así que la segunda pisa a la primera.

La solución es pasarle a `setCuenta` una **función** que recibe el valor más reciente, en vez de un valor calculado de antemano:

<div class="demo" data-alto="9rem" data-code="&lt;script src=&quot;https://unpkg.com/react@18.3.1/umd/react.development.js&quot;&gt;&lt;/script&gt;&#10;&lt;script src=&quot;https://unpkg.com/react-dom@18.3.1/umd/react-dom.development.js&quot;&gt;&lt;/script&gt;&#10;&lt;script src=&quot;https://unpkg.com/@babel/standalone@7.26.4/babel.min.js&quot;&gt;&lt;/script&gt;&#10;&lt;div id=&quot;app&quot;&gt;&lt;/div&gt;&#10;&lt;script type=&quot;text/babel&quot;&gt;&#10;function ContadorBueno() {&#10;  const [cuenta, setCuenta] = React.useState(0);&#10;  function sumarDos() {&#10;    setCuenta(c =&gt; c + 1);&#10;    setCuenta(c =&gt; c + 1);      // cada &quot;c&quot; es el valor más reciente en ese momento&#10;  }&#10;  return (&#10;    &lt;div&gt;&#10;      &lt;p&gt;Valor: {cuenta}&lt;/p&gt;&#10;      &lt;button onClick={sumarDos}&gt;Sumar 2 (sí funciona)&lt;/button&gt;&#10;    &lt;/div&gt;&#10;  );&#10;}&#10;const raiz = ReactDOM.createRoot(document.getElementById(&quot;app&quot;));&#10;raiz.render(&lt;ContadorBueno /&gt;);&#10;&lt;/script&gt;"></div>

Con `c => c + 1`, cada llamada recibe el valor que dejó la anterior, en vez de la "foto" del estado al empezar la función. Regla práctica: si el valor nuevo depende del valor anterior, usa la forma con función.

## `useEffect`: lo que pasa fuera del pintado

Pintar JSX a partir del estado cubre casi todo, pero algunas cosas (cambiar el título de la pestaña, pedir datos a un servidor, arrancar un temporizador) no son "pintar": son **efectos secundarios**. `useEffect` ejecuta una función **después** de pintar:

<div class="demo" data-alto="10rem" data-consola="1" data-code="&lt;script src=&quot;https://unpkg.com/react@18.3.1/umd/react.development.js&quot;&gt;&lt;/script&gt;&#10;&lt;script src=&quot;https://unpkg.com/react-dom@18.3.1/umd/react-dom.development.js&quot;&gt;&lt;/script&gt;&#10;&lt;script src=&quot;https://unpkg.com/@babel/standalone@7.26.4/babel.min.js&quot;&gt;&lt;/script&gt;&#10;&lt;div id=&quot;app&quot;&gt;&lt;/div&gt;&#10;&lt;script type=&quot;text/babel&quot;&gt;&#10;function Titulo() {&#10;  const [cuenta, setCuenta] = React.useState(0);&#10;&#10;  React.useEffect(() =&gt; {&#10;    document.title = &quot;Pulsado &quot; + cuenta + &quot; veces&quot;;      // en una pestaña de verdad, esto SÍ se vería ahí&#10;    console.log(&quot;Efecto ejecutado: cuenta =&quot;, cuenta);&#10;  }, [cuenta]);          // solo se repite cuando &quot;cuenta&quot; cambia&#10;&#10;  return &lt;button onClick={() =&gt; setCuenta(cuenta + 1)}&gt;Pulsado {cuenta} veces (mira la consola)&lt;/button&gt;;&#10;}&#10;const raiz = ReactDOM.createRoot(document.getElementById(&quot;app&quot;));&#10;raiz.render(&lt;Titulo /&gt;);&#10;&lt;/script&gt;"></div>

(Este editor vive dentro de un `<iframe>` con su propio documento, así que el cambio de `document.title` no se ve en la pestaña del navegador; por eso el ejemplo también deja constancia por consola. En una página real sí cambiaría el título de la pestaña, tal cual.)

El segundo argumento, el **array de dependencias**, decide cuándo se repite el efecto:

| Array de dependencias | Cuándo se ejecuta |
|---|---|
| (sin segundo argumento) | Después de **cada** repintado |
| `[]` (vacío) | Solo **una vez**, al montar el componente |
| `[cuenta]` | Al montar, y de nuevo cada vez que `cuenta` cambie |

Si el efecto deja algo en marcha (un temporizador, un oyente de eventos), la función puede **devolver otra función** que lo limpie; React la llama antes de repetir el efecto o al desmontar el componente. Es la contrapartida exacta de `addEventListener`/`removeEventListener` que ya viste en la WA2.1, pero con el ciclo de vida del componente en vez del tuyo.

## Para practicar

Los ejercicios de [WA6 · Ejercicios](ejercicios.md) practican componentes, props, listas, estado y eventos.
