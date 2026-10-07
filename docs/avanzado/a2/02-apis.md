# WA2.2 APIs del navegador: observadores, formularios, URL y estado

!!! info "Cómo se han comprobado los ejemplos"
    Los editores con vista previa **se ejecutan en tu navegador** al abrir la página: puedes cambiarlos y ver el resultado. Los fragmentos «ejecutado con Node» (lógica sin `document`) se han ejecutado de verdad al construir la web y su salida es la real.

## Observadores: enterarse de cambios sin preguntar todo el rato

Antes se escuchaba el evento `scroll` y se calculaba la posición de cada elemento **en cada píxel de desplazamiento**: lento y con tirones. Los **observadores** invierten el planteamiento: el navegador **avisa** cuando ocurre lo que te interesa.

| Observador | Avisa cuando… | Para qué |
|---|---|---|
| `IntersectionObserver` | Un elemento **entra o sale de la zona visible** | Carga perezosa, animar al hacer scroll, «scroll infinito» |
| `ResizeObserver` | Un elemento **cambia de tamaño** | Componentes que se adaptan a su caja, no a la pantalla |
| `MutationObserver` | El **DOM cambia** (hijos, atributos, texto) | Reaccionar a cambios que hace otro script |

<div class="demo" data-alto="14rem" data-consola="1" data-code="&lt;div style=&quot;height:130px;overflow:auto;border:1px solid #94a3b8&quot; id=&quot;zona&quot;&gt;&#10;  &lt;p style=&quot;margin:0;height:200px;padding:8px&quot;&gt;⬇ Desplázate hacia abajo dentro de este recuadro ⬇&lt;/p&gt;&#10;  &lt;div id=&quot;tarjeta&quot; style=&quot;margin:8px;padding:16px;background:#fde68a;opacity:.2;transition:opacity .5s&quot;&gt;Soy una tarjeta que se activa al verse&lt;/div&gt;&#10;  &lt;p style=&quot;margin:0;height:200px;padding:8px&quot;&gt;Más contenido&lt;/p&gt;&#10;&lt;/div&gt;&#10;&lt;script&gt;&#10;const tarjeta = document.getElementById(&quot;tarjeta&quot;);&#10;const observador = new IntersectionObserver((entradas) =&gt; {&#10;  for (const entrada of entradas) {&#10;    tarjeta.style.opacity = entrada.isIntersecting ? 1 : 0.2;&#10;    console.log(entrada.isIntersecting ? &quot;la tarjeta ENTRA en la zona visible&quot; : &quot;la tarjeta SALE de la zona visible&quot;);&#10;  }&#10;}, { root: document.getElementById(&quot;zona&quot;), threshold: 0.5 });   // avisa cuando se ve al menos el 50 %&#10;&#10;observador.observe(tarjeta);&#10;&lt;/script&gt;"></div>

`threshold: 0.5` significa «avisa cuando se vea la mitad». Con una imagen, el mismo patrón sirve para **cargarla solo cuando se acerca a la pantalla**: se guarda la dirección en `data-src` y, al aparecer, se copia a `src`. (Hoy muchas veces basta con `<img loading="lazy">`, pero el observador sirve para todo lo demás.)

## Formularios: `FormData` y la API de validación

Leer cada campo con `getElementById(...).value` no escala. **`FormData`** recoge **todo el formulario** de una vez, respetando los nombres (`name`) de los campos:

<div class="demo" data-alto="17rem" data-consola="1" data-code="&lt;form id=&quot;f&quot; novalidate&gt;&#10;  &lt;label&gt;Nombre &lt;input name=&quot;nombre&quot; required minlength=&quot;3&quot;&gt;&lt;/label&gt;&lt;br&gt;&#10;  &lt;label&gt;Correo &lt;input name=&quot;correo&quot; type=&quot;email&quot; required&gt;&lt;/label&gt;&lt;br&gt;&#10;  &lt;label&gt;Edad &lt;input name=&quot;edad&quot; type=&quot;number&quot; min=&quot;16&quot; max=&quot;99&quot;&gt;&lt;/label&gt;&lt;br&gt;&#10;  &lt;label&gt;&lt;input name=&quot;acepto&quot; type=&quot;checkbox&quot; required&gt; Acepto las condiciones&lt;/label&gt;&lt;br&gt;&#10;  &lt;button&gt;Enviar&lt;/button&gt;&#10;&lt;/form&gt;&#10;&lt;script&gt;&#10;const f = document.getElementById(&quot;f&quot;);&#10;&#10;f.addEventListener(&quot;submit&quot;, e =&gt; {&#10;  e.preventDefault();&#10;  if (!f.checkValidity()) {                       // la API de validación integrada en HTML&#10;    for (const campo of f.elements) {&#10;      if (campo.name &amp;&amp; !campo.validity.valid) console.log(campo.name + &quot;: &quot; + campo.validationMessage);&#10;    }&#10;    return;&#10;  }&#10;  const datos = Object.fromEntries(new FormData(f));   // todos los campos como un objeto&#10;  console.log(&quot;válido, se enviaría:&quot;, JSON.stringify(datos));&#10;});&#10;&lt;/script&gt;"></div>

Prueba a enviarlo vacío y luego a rellenarlo bien. Las reglas (`required`, `minlength`, `type="email"`, `min`, `max`, `pattern`) están **en el HTML**, y JavaScript solo las **consulta**:

| API | Qué da |
|---|---|
| `form.checkValidity()` | `true` si todos los campos cumplen sus reglas |
| `campo.validity.valid` / `.valueMissing` / `.typeMismatch`… | El detalle de qué regla falla |
| `campo.validationMessage` | El mensaje del navegador (en el idioma del usuario) |
| `campo.setCustomValidity("texto")` | Una regla **propia**; con `""` se quita el error |
| `new FormData(form)` | Todos los valores. `Object.fromEntries(...)` lo convierte en un objeto |

El atributo `novalidate` del `<form>` desactiva los **bocadillos** del navegador para que muestres **tus** mensajes; la validación sigue disponible para consultar.

## La URL como estado: `URL` y `URLSearchParams`

Los filtros, la página de una lista o el término buscado **conviene guardarlos en la URL** (`?q=kotlin&pagina=2`): así se puede **compartir** el enlace y el botón «atrás» funciona. Las clases `URL` y `URLSearchParams` evitan trocear cadenas a mano (y los errores de codificación):

```javascript
const url = new URL("https://ejemplo.com/buscar?q=kotlin&pagina=2&etiqueta=android&etiqueta=compose#resultados");

console.log(url.pathname, "|", url.hash);
console.log(url.searchParams.get("q"), Number(url.searchParams.get("pagina")));
console.log(url.searchParams.getAll("etiqueta"));          // un parámetro repetido
console.log(url.searchParams.get("no-existe"));            // null

// Construir una URL: los caracteres especiales se codifican solos
const nueva = new URL("https://ejemplo.com/buscar");
nueva.searchParams.set("q", "café con leche & más");
nueva.searchParams.set("pagina", 3);
console.log(nueva.href);

// Convertir los parámetros en un objeto y viceversa
console.log(Object.fromEntries(url.searchParams));           // OJO: con repetidos, se queda con el último
console.log(new URLSearchParams({ q: "a b", n: 1 }).toString());
```

**Salida (ejecutado con Node):**

```text
/buscar | #resultados
kotlin 2
[ 'android', 'compose' ]
null
https://ejemplo.com/buscar?q=caf%C3%A9+con+leche+%26+m%C3%A1s&pagina=3
{ q: 'kotlin', pagina: '2', etiqueta: 'compose' }
q=a+b&n=1
```

Fíjate en cómo `"café con leche & más"` se codifica sin que escribas nada: el `&` **no** rompe los parámetros. Hacerlo a mano con `+` es una fuente de errores y de agujeros de seguridad.

### Cambiar la URL sin recargar: la API `History`

Cuando el usuario filtra una lista, puedes **actualizar la URL sin recargar la página**:

```javascript
// Sin ejecutar: necesita el navegador (y un servidor, no file://)
function guardarFiltro(texto) {
  const url = new URL(location.href);
  url.searchParams.set("q", texto);
  history.pushState({ q: texto }, "", url);          // añade una entrada al historial: «atrás» vuelve al filtro anterior
  // history.replaceState(...) sustituye la entrada actual sin añadir otra (útil mientras se escribe)
}

window.addEventListener("popstate", (e) => {         // el usuario pulsó «atrás» o «adelante»
  const q = new URL(location.href).searchParams.get("q") ?? "";
  pintar(q);
});
```

## Estado → vista: la idea detrás de React y Vue

Cuando una página crece, actualizar el DOM «a trocitos» (cambia un texto aquí, un elemento allá) se enreda enseguida. El patrón que resuelve esto es el mismo que viste en **Android (AA1)**: **todo lo que se ve se calcula a partir de un único objeto de estado**, con una función **pura**.

La parte sin DOM, la función que convierte estado en HTML, es lógica normal y se puede ejecutar y comprobar:

```javascript
const escapar = s => String(s).replace(/[&<>"']/g, c => ({ "&": "&amp;", "<": "&lt;", ">": "&gt;", '"': "&quot;", "'": "&#39;" }[c]));

// La vista: una función PURA. Mismo estado, mismo HTML. No toca el DOM.
function vista(estado) {
  const visibles = estado.tareas.filter(t =>
    estado.filtro === "todas" ? true : estado.filtro === "hechas" ? t.hecha : !t.hecha);
  const pendientes = estado.tareas.filter(t => !t.hecha).length;
  return [
    `<p>${pendientes} pendientes</p>`,
    "<ul>",
    ...visibles.map(t => `  <li class="${t.hecha ? "hecha" : ""}">${escapar(t.texto)}</li>`),
    "</ul>"
  ].join("\n");
}

// El «reductor»: (estado, acción) -> estado nuevo, sin modificar el anterior
function reducir(estado, accion) {
  switch (accion.tipo) {
    case "añadir": return { ...estado, tareas: [...estado.tareas, { id: estado.tareas.length + 1, texto: accion.texto, hecha: false }] };
    case "marcar": return { ...estado, tareas: estado.tareas.map(t => t.id === accion.id ? { ...t, hecha: !t.hecha } : t) };
    case "filtrar": return { ...estado, filtro: accion.filtro };
    default: return estado;
  }
}

let estado = { tareas: [], filtro: "todas" };
for (const accion of [
  { tipo: "añadir", texto: "Estudiar <JS>" },
  { tipo: "añadir", texto: "Hacer la compra" },
  { tipo: "marcar", id: 1 },
  { tipo: "filtrar", filtro: "hechas" }
]) {
  estado = reducir(estado, accion);
}
console.log(vista(estado));
```

**Salida (ejecutado con Node):**

```text
<p>1 pendientes</p>
<ul>
  <li class="hecha">Estudiar &lt;JS&gt;</li>
</ul>
```

La versión con DOM es **la misma lógica** más dos líneas: cada vez que cambia el estado, se vuelve a pintar. Aquí, funcionando en vivo:

<div class="demo" data-alto="17rem" data-code="&lt;div id=&quot;app&quot;&gt;&lt;/div&gt;&#10;&lt;style&gt;.hecha { text-decoration: line-through; color: gray } button { margin-right: 4px }&lt;/style&gt;&#10;&lt;script&gt;&#10;const escapar = s =&gt; String(s).replace(/[&amp;&lt;&gt;&quot;&#x27;]/g, c =&gt; ({ &quot;&amp;&quot;: &quot;&amp;amp;&quot;, &quot;&lt;&quot;: &quot;&amp;lt;&quot;, &quot;&gt;&quot;: &quot;&amp;gt;&quot;, &#x27;&quot;&#x27;: &quot;&amp;quot;&quot;, &quot;&#x27;&quot;: &quot;&amp;#39;&quot; }[c]));&#10;&#10;let estado = { tareas: [{ id: 1, texto: &quot;Estudiar JavaScript&quot;, hecha: false }], filtro: &quot;todas&quot; };&#10;&#10;function vista(e) {&#10;  const visibles = e.tareas.filter(t =&gt; e.filtro === &quot;todas&quot; || (e.filtro === &quot;hechas&quot;) === t.hecha);&#10;  return `&#10;    &lt;p&gt;&lt;input id=&quot;nueva&quot; placeholder=&quot;Nueva tarea&quot;&gt; &lt;button data-accion=&quot;añadir&quot;&gt;Añadir&lt;/button&gt;&lt;/p&gt;&#10;    &lt;p&gt;${[&quot;todas&quot;, &quot;pendientes&quot;, &quot;hechas&quot;].map(f =&gt; `&lt;button data-accion=&quot;filtrar&quot; data-filtro=&quot;${f}&quot; ${e.filtro === f ? &quot;disabled&quot; : &quot;&quot;}&gt;${f}&lt;/button&gt;`).join(&quot;&quot;)}&lt;/p&gt;&#10;    &lt;ul&gt;${visibles.map(t =&gt; `&lt;li class=&quot;${t.hecha ? &quot;hecha&quot; : &quot;&quot;}&quot;&gt;&lt;label&gt;&lt;input type=&quot;checkbox&quot; data-accion=&quot;marcar&quot; data-id=&quot;${t.id}&quot; ${t.hecha ? &quot;checked&quot; : &quot;&quot;}&gt; ${escapar(t.texto)}&lt;/label&gt;&lt;/li&gt;`).join(&quot;&quot;)}&lt;/ul&gt;&#10;    &lt;p&gt;${e.tareas.filter(t =&gt; !t.hecha).length} pendientes&lt;/p&gt;`;&#10;}&#10;&#10;function pintar() { document.getElementById(&quot;app&quot;).innerHTML = vista(estado); }   // todo el DOM sale del estado&#10;&#10;const app = document.getElementById(&quot;app&quot;);&#10;app.addEventListener(&quot;click&quot;, e =&gt; {&#10;  const b = e.target.closest(&quot;button[data-accion]&quot;);&#10;  if (!b) return;&#10;  if (b.dataset.accion === &quot;añadir&quot;) {&#10;    const texto = document.getElementById(&quot;nueva&quot;).value.trim();&#10;    if (texto) estado = { ...estado, tareas: [...estado.tareas, { id: Date.now(), texto, hecha: false }] };&#10;  } else if (b.dataset.accion === &quot;filtrar&quot;) {&#10;    estado = { ...estado, filtro: b.dataset.filtro };&#10;  }&#10;  pintar();&#10;});&#10;app.addEventListener(&quot;change&quot;, e =&gt; {&#10;  if (e.target.dataset.accion === &quot;marcar&quot;) {&#10;    estado = { ...estado, tareas: estado.tareas.map(t =&gt; t.id === Number(e.target.dataset.id) ? { ...t, hecha: e.target.checked } : t) };&#10;    pintar();&#10;  }&#10;});&#10;pintar();&#10;&lt;/script&gt;"></div>

Repintar **todo** el DOM a cada cambio es lo más simple y vale para páginas pequeñas (fíjate en que `escapar` evita el XSS al usar `innerHTML`). Librerías como React, Vue o Svelte hacen exactamente esto, pero **comparando** lo anterior con lo nuevo y tocando **solo lo que cambió**.

## Guardar datos que cambian de forma: versiones en `localStorage`

`localStorage` guarda **texto**, para siempre, en el navegador de cada persona. Si cambias la **forma** de tus datos en una versión nueva de la página, los usuarios que ya tenían datos viejos guardados **romperán la página**. La solución es guardar un número de **versión** y **migrar** al leer:

```javascript
// Simulamos localStorage para ejecutarlo en Node
const almacen = new Map();
const ls = { getItem: k => almacen.get(k) ?? null, setItem: (k, v) => almacen.set(k, String(v)) };

const VERSION = 2;

function migrar(datos) {
  let d = datos;
  if (d.version === 1) {                                   // v1: { nombre }  ->  v2: { nombre, apellidos, tema }
    d = { version: 2, nombre: d.nombre, apellidos: "", tema: "claro" };
  }
  return d;
}

function cargar() {
  const texto = ls.getItem("perfil");
  if (texto === null) return { version: VERSION, nombre: "", apellidos: "", tema: "claro" };
  try {
    return migrar(JSON.parse(texto));
  } catch {
    return { version: VERSION, nombre: "", apellidos: "", tema: "claro" };    // datos corruptos: empezar de cero
  }
}

ls.setItem("perfil", JSON.stringify({ version: 1, nombre: "Ana" }));   // lo que guardó la versión antigua
console.log("migrado:", cargar());

ls.setItem("perfil", "{esto no es JSON");
console.log("corrupto:", cargar());
```

**Salida (ejecutado con Node):**

```text
migrado: { version: 2, nombre: 'Ana', apellidos: '', tema: 'claro' }
corrupto: { version: 2, nombre: '', apellidos: '', tema: 'claro' }
```

Dos reglas para guardar datos en el navegador:

* **Siempre `try/catch` al leer**: el usuario (o una extensión) pudo modificar el valor.
* **Nunca guardes contraseñas, tokens ni datos sensibles** en `localStorage`: cualquier script de la página puede leerlos.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Escuchar `scroll` y calcular posiciones a cada píxel | `IntersectionObserver` |
| Leer cada campo del formulario a mano | `new FormData(form)` y `Object.fromEntries` |
| Validar con expresiones propias lo que el HTML ya valida | `required`, `type`, `min`, `pattern` + `checkValidity()` |
| Construir URLs concatenando cadenas | `URL` y `URLSearchParams` |
| Modificar el DOM «a trocitos» en una página con mucho estado | Un objeto de estado y una función `vista(estado)` |
| `innerHTML` con texto que viene del usuario | Escapar o usar `textContent` |
| Leer `JSON.parse(localStorage...)` sin `try/catch` ni versión | Guardar una versión y migrar |

## Para practicar

Los ejercicios WA2.1, WA2.2 y WA2.4 de [WA2 · Ejercicios](ejercicios.md) practican `URLSearchParams` y la migración de datos.
