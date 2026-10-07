# WA1.2 Asincronía: promesas, async/await y cancelación

!!! info "Cómo se han comprobado los ejemplos"
    Cada ejemplo con **salida** se ha **ejecutado de verdad con Node** al construir esta web: lo que ves es lo que imprime. Los navegadores y Node comparten el lenguaje, pero no todo el entorno (por ejemplo, `document` y `window` solo existen en el navegador), y esta página lo señala cuando importa.

## Un solo hilo, un bucle de eventos

JavaScript ejecuta **una sola cosa a la vez** en un hilo. Pero una página tiene que esperar muchas cosas (una respuesta de la red, un temporizador, un clic) **sin quedarse congelada**. Lo consigue con una cola de tareas y un **bucle de eventos**: ejecuta lo que toca, y cuando termina, coge lo siguiente de la cola.

Hay dos colas con **prioridades distintas**:

| Cola | Qué entra | Cuándo se vacía |
|---|---|---|
| **Microtareas** | `.then`, `await`, `queueMicrotask` | **Antes** de pasar a la siguiente tarea, siempre completa |
| **Tareas** (macrotareas) | `setTimeout`, eventos, red | De una en una, tras vaciar las microtareas |

El orden resultante sorprende al principio:

```javascript
console.log("1 síncrono");

setTimeout(() => console.log("5 setTimeout con 0 ms"), 0);

Promise.resolve().then(() => console.log("3 microtarea: then"));
queueMicrotask(() => console.log("4 microtarea: queueMicrotask"));

console.log("2 síncrono");
```

**Salida (ejecutado con Node):**

```text
1 síncrono
2 síncrono
3 microtarea: then
4 microtarea: queueMicrotask
5 setTimeout con 0 ms
```

Primero **todo el código síncrono**; después **todas las microtareas** (aunque el `setTimeout` se hubiera pedido antes); y por último las **tareas**, como el temporizador. Un `setTimeout(..., 0)` no significa «ahora»: significa «cuando termine lo demás».

!!! warning "Una función larga congela la página"
    Mientras JavaScript ejecuta un bucle pesado, **no se atiende ningún clic ni se repinta nada**: no hay otro hilo que lo haga. Lo que dure más de ~50 ms se nota. Para cálculo pesado existen los *Web Workers*; para esperar, nunca un bucle `while`.

## Promesas

Una **promesa** (`Promise`) representa un valor que **llegará más tarde**. Tiene tres estados: *pendiente*, *cumplida* (con un valor) o *rechazada* (con un error). Se encadena con `.then()` (si sale bien), `.catch()` (si falla) y `.finally()` (siempre).

```javascript
const esperar = (ms, valor, fallar = false) =>
  new Promise((resolver, rechazar) => {
    setTimeout(() => fallar ? rechazar(new Error(valor)) : resolver(valor), ms);
  });

esperar(50, 10)
  .then(n => { console.log("recibido", n); return n * 2; })       // lo que devuelves pasa al siguiente then
  .then(n => esperar(20, n + 1))                                    // si devuelves una promesa, se espera
  .then(n => { console.log("tras esperar de nuevo", n); throw new Error("algo falló"); })
  .then(() => console.log("esto NO se ejecuta"))                    // se salta: hay un error pendiente
  .catch(e => { console.log("capturado:", e.message); return "recuperado"; })
  .finally(() => console.log("finally: se ejecuta siempre"))
  .then(v => console.log("y la cadena sigue con", v));
```

**Salida (ejecutado con Node):**

```text
recibido 10
tras esperar de nuevo 21
capturado: algo falló
finally: se ejecuta siempre
y la cadena sigue con recuperado
```

Un `.catch` **recoge cualquier error de lo anterior** de la cadena y, si devuelve un valor, la cadena **continúa** con normalidad.

## `async` / `await`: promesas que parecen código normal

`await` pausa **la función** (no la página) hasta que la promesa se resuelva, y devuelve su valor. Solo puede usarse dentro de una función `async` (o en el nivel superior de un módulo). El error de una promesa rechazada se captura con un `try/catch` de toda la vida.

```javascript
const esperar = (ms, valor, fallar = false) =>
  new Promise((resolver, rechazar) => {
    setTimeout(() => fallar ? rechazar(new Error(valor)) : resolver(valor), ms);
  });

async function cargarPerfil() {
  try {
    const usuario = await esperar(30, { id: 7, nombre: "Ana" });
    console.log("usuario:", usuario.nombre);
    const pedidos = await esperar(30, ["libro", "lápiz"]);
    console.log("pedidos:", pedidos.length);
    await esperar(10, "servidor caído", true);               // aquí se lanza una excepción
    console.log("esto no se imprime");
  } catch (e) {
    console.log("error:", e.message);
  } finally {
    console.log("fin de cargarPerfil");
  }
  return "terminado";
}

console.log("antes");
const promesa = cargarPerfil();                                // una función async SIEMPRE devuelve una promesa
console.log("justo después de llamar:", promesa instanceof Promise);
console.log("resultado:", await promesa);
```

**Salida (ejecutado con Node):**

```text
antes
justo después de llamar: true
usuario: Ana
pedidos: 2
error: servidor caído
fin de cargarPerfil
resultado: terminado
```

Fíjate en que `cargarPerfil()` devuelve **enseguida** una promesa y el programa sigue; por eso «justo después de llamar» sale antes que el resto. Y cualquier función `async` devuelve una promesa aunque no uses `return`.

### Secuencial o paralelo: el fallo de rendimiento más común

Dos `await` seguidos esperan **uno detrás de otro**, aunque no dependan entre sí. Si son independientes, hay que lanzarlas **a la vez**:

```javascript
const esperar = (ms, valor) => new Promise(r => setTimeout(() => r(valor), ms));

async function medir(nombre, funcion) {
  const inicio = performance.now();
  const resultado = await funcion();
  const ms = performance.now() - inicio;
  console.log(nombre, resultado, ms >= 280 && ms < 450 ? "≈ 300 ms" : ms >= 580 ? "≈ 600 ms" : "?");
}

await medir("secuencial:", async () => {
  const a = await esperar(300, "A");
  const b = await esperar(300, "B");
  return [a, b];
});

await medir("paralelo:  ", async () => {
  const [a, b] = await Promise.all([esperar(300, "A"), esperar(300, "B")]);
  return [a, b];
});
```

**Salida (ejecutado con Node):**

```text
secuencial: [ 'A', 'B' ] ≈ 600 ms
paralelo:   [ 'A', 'B' ] ≈ 300 ms
```

## Combinar varias promesas

| Función | Se cumple cuando… | Si una falla |
|---|---|---|
| `Promise.all([...])` | **Todas** se cumplen (array de resultados, **en orden**) | Falla **al instante** con el primer error |
| `Promise.allSettled([...])` | **Todas** terminan, bien o mal | Nunca falla: da el estado de cada una |
| `Promise.race([...])` | La **primera** que termina (cumplida o rechazada) | Si es la primera, falla |
| `Promise.any([...])` | La primera que **se cumple** | Solo falla si fallan **todas** |

```javascript
const esperar = (ms, valor, fallar = false) =>
  new Promise((resolver, rechazar) => setTimeout(() => fallar ? rechazar(new Error(valor)) : resolver(valor), ms));

const lenta = () => esperar(120, "lenta");
const rapida = () => esperar(30, "rápida");
const rota = () => esperar(60, "rota", true);

console.log("all (todas bien):", await Promise.all([lenta(), rapida()]));

try { await Promise.all([lenta(), rota(), rapida()]); }
catch (e) { console.log("all con una rota ->", e.message); }

const estados = await Promise.allSettled([lenta(), rota(), rapida()]);
console.log(estados.map(e => e.status + (e.status === "fulfilled" ? ":" + e.value : ":" + e.reason.message)));

console.log("race:", await Promise.race([lenta(), rapida()]));
console.log("any:", await Promise.any([rota(), lenta()]));            // ignora la rota y espera la primera buena
```

**Salida (ejecutado con Node):**

```text
all (todas bien): [ 'lenta', 'rápida' ]
all con una rota -> rota
[ 'fulfilled:lenta', 'rejected:rota', 'fulfilled:rápida' ]
race: rápida
any: lenta
```

Una página que carga **tres paneles independientes** debe usar `allSettled`: si uno falla, los otros dos deben seguir viéndose. `Promise.all` es para cuando necesitas **todo** o nada.

## Cancelar: `AbortController`

Una petición que el usuario ya no necesita (cambió de página, escribió otra búsqueda) debe **cancelarse**. La API estándar es `AbortController`: creas uno, pasas su `signal` a quien trabaja, y llamas a `abort()` cuando quieras. `fetch` ya la entiende:

```javascript
// Sin ejecutar: usa fetch y el navegador
const control = new AbortController();
fetch("/api/buscar?q=kotlin", { signal: control.signal })
  .then(r => r.json())
  .catch(e => { if (e.name !== "AbortError") throw e; });   // cancelar NO es un error «de verdad»
control.abort();                                            // a los pocos segundos, o al escribir otra letra
```

Y tú también puedes hacer que **tus** funciones asíncronas sean cancelables:

```javascript
function esperarCancelable(ms, signal) {
  return new Promise((resolver, rechazar) => {
    if (signal.aborted) return rechazar(signal.reason);
    const id = setTimeout(() => resolver("terminó"), ms);
    signal.addEventListener("abort", () => { clearTimeout(id); rechazar(signal.reason); }, { once: true });
  });
}

// 1) Cancelar a mano
const control = new AbortController();
const trabajo = esperarCancelable(1000, control.signal);
setTimeout(() => control.abort(), 50);
try { await trabajo; } catch (e) { console.log("cancelada a mano:", e.name); }

// 2) Límite de tiempo, sin escribir temporizadores
try { await esperarCancelable(1000, AbortSignal.timeout(50)); }
catch (e) { console.log("límite de tiempo:", e.name); }

// 3) Si nadie cancela, termina con normalidad
console.log(await esperarCancelable(20, new AbortController().signal));
```

**Salida (ejecutado con Node):**

```text
cancelada a mano: AbortError
límite de tiempo: TimeoutError
terminó
```

`AbortSignal.timeout(ms)` crea una señal que se activa sola pasado el tiempo: es la forma más corta de poner un **límite** a cualquier cosa que acepte una señal, incluido `fetch`.

## Errores que nadie captura

Una promesa rechazada **que nadie captura** es un fallo silencioso en el navegador (solo sale un aviso en la consola) y puede cerrar el programa en Node. Dos reglas:

* Cada `await` que pueda fallar necesita un `try/catch` en algún sitio **por encima**.
* Si lanzas una promesa **sin** `await` (*fire and forget*), añádele un `.catch(...)`, porque nadie más lo va a hacer.

```javascript
const esperar = (ms, valor, fallar = false) =>
  new Promise((resolver, rechazar) => setTimeout(() => fallar ? rechazar(new Error(valor)) : resolver(valor), ms));

process.on("unhandledRejection", (e) => console.log("¡rechazo SIN capturar!:", e.message));

esperar(10, "nadie me captura", true);                 // sin await ni catch
esperar(10, "a mí sí me capturan", true).catch(e => console.log("capturado:", e.message));

await esperar(50, "fin");
console.log("fin del programa");
```

**Salida (ejecutado con Node):**

```text
¡rechazo SIN capturar!: nadie me captura
capturado: a mí sí me capturan
fin del programa
```

(En el navegador, el equivalente es el evento `unhandledrejection` de `window`.)

## Un patrón útil: reintentar con espera

Si una petición falla por algo pasajero, se puede reintentar. Es el mismo patrón que se estudió en Android, escrito con `async/await`:

```javascript
const dormir = ms => new Promise(r => setTimeout(r, ms));

async function reintentar(tarea, intentos = 3, espera = 20) {
  for (let n = 1; n <= intentos; n++) {
    try {
      return await tarea(n);
    } catch (e) {
      if (n === intentos) throw e;                       // se acabaron los intentos: que falle
      console.log(`intento ${n} falló (${e.message}); espero ${espera} ms`);
      await dormir(espera);
      espera *= 2;                                       // cada vez esperamos el doble
    }
  }
}

console.log(await reintentar(async n => {
  if (n < 3) throw new Error("sin red");
  return `datos recibidos en el intento ${n}`;
}));

try { await reintentar(async () => { throw new Error("servidor caído"); }, 2, 10); }
catch (e) { console.log("rendido:", e.message); }
```

**Salida (ejecutado con Node):**

```text
intento 1 falló (sin red); espero 20 ms
intento 2 falló (sin red); espero 40 ms
datos recibidos en el intento 3
intento 1 falló (servidor caído); espero 10 ms
rendido: servidor caído
```

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Olvidar `await` y trabajar con la promesa como si fuera el valor | Si ves `[object Promise]` o `Promise { <pending> }`, falta un `await` |
| Varios `await` seguidos de cosas independientes | `Promise.all` |
| `Promise.all` cuando cada parte debe poder fallar sola | `Promise.allSettled` |
| `await` dentro de `forEach` | `forEach` no espera: usa `for...of` (o `Promise.all` + `map`) |
| Promesas lanzadas sin `catch` | Siempre un `catch` o un `try/catch` por encima |
| Peticiones que no se cancelan al cambiar de pantalla o de búsqueda | `AbortController` |
| Bucles de cálculo largos en el hilo principal | Trocear con `await`, o un Web Worker |

## Para practicar

Los ejercicios WA1.4 a WA1.6 de [WA1 · Ejercicios](ejercicios.md) practican `await`, las combinaciones de promesas y el reintento.
