# WA1.1 JavaScript moderno: datos, funciones y módulos

!!! info "Cómo se han comprobado los ejemplos"
    Cada ejemplo con **salida** se ha **ejecutado de verdad con Node** al construir esta web: lo que ves es lo que imprime. Los navegadores y Node comparten el lenguaje, pero no todo el entorno (por ejemplo, `document` y `window` solo existen en el navegador), y esta página lo señala cuando importa.

## Desestructurar: sacar piezas de un objeto o una lista

**Desestructurar** es declarar varias variables de golpe a partir de un objeto o un array. Ahorra mucho código repetitivo (`const nombre = usuario.nombre; const ciudad = ...`).

```javascript
const usuario = { nombre: "Ana", dir: { ciudad: "Lugo" }, tags: ["web", "js", "css"] };

const { nombre, dir: { ciudad }, edad = 18 } = usuario;   // edad no existe: toma el valor por defecto
console.log(nombre, ciudad, edad);

const [primero, ...resto] = usuario.tags;                 // «resto» recoge lo que sobra
console.log(primero, resto);

function presentar({ nombre, edad = 0 }) {                // también en los parámetros
  return `${nombre} (${edad})`;
}
console.log(presentar(usuario), presentar({ nombre: "Luis", edad: 30 }));

let a = 1, b = 2;
[a, b] = [b, a];                                          // intercambiar sin variable auxiliar
console.log(a, b);
```

**Salida (ejecutado con Node):**

```text
Ana Lugo 18
web [ 'js', 'css' ]
Ana (0) Luis (30)
2 1
```

| Forma | Qué hace |
|---|---|
| `const { x, y } = obj` | Variables `x` e `y` con las propiedades del mismo nombre |
| `const { x: nuevo } = obj` | Renombrar: la propiedad `x` va a la variable `nuevo` |
| `const { x = 5 } = obj` | Valor por defecto si `x` es `undefined` |
| `const [a, b] = lista` | Por **posición** |
| `const [a, ...resto] = lista` | `resto` es un array con lo demás |

## Spread y rest: los tres puntos

Los tres puntos `...` hacen dos cosas según dónde estén: **esparcir** (*spread*) un contenido, o **recoger** (*rest*) lo que sobra.

```javascript
const base = { color: "rojo", talla: "M" };
const pedido = { ...base, talla: "L", unidades: 2 };      // copia base y sobrescribe talla
console.log(pedido);

const a = [1, 2, 3], b = [4, 5];
console.log([...a, ...b, 6]);                              // unir arrays
console.log(Math.max(...a));                               // pasar una lista como argumentos

function sumar(...numeros) {                               // rest: recoge todos los argumentos
  return numeros.reduce((t, n) => t + n, 0);
}
console.log(sumar(1, 2, 3, 4));

// OJO: la copia es SUPERFICIAL
const original = { nombre: "Ana", dir: { ciudad: "Lugo" } };
const copia = { ...original };
copia.dir.ciudad = "Vigo";
console.log(original.dir.ciudad, copia.dir === original.dir);

// Para una copia profunda
const profunda = structuredClone(original);
profunda.dir.ciudad = "León";
console.log(original.dir.ciudad, profunda.dir.ciudad);
```

**Salida (ejecutado con Node):**

```text
{ color: 'rojo', talla: 'L', unidades: 2 }
[ 1, 2, 3, 4, 5, 6 ]
3
10
Vigo true
Vigo León
```

La copia con `...` solo duplica el **primer nivel**: los objetos anidados se **comparten**, y cambiar uno cambia el otro. Para copiar de verdad todo el árbol, `structuredClone`.

## Encadenamiento opcional y fusión de nulos

Leer una propiedad de algo que puede no existir lanza un error. `?.` corta la cadena en cuanto encuentra `null` o `undefined` y devuelve `undefined`; `??` da un valor por defecto **solo** si lo de la izquierda es `null` o `undefined`.

```javascript
const usuario = { nombre: "Ana", contacto: null, config: { volumen: 0, tema: "" } };

console.log(usuario.contacto?.email);                      // undefined, sin error
console.log(usuario.amigos?.[0]);                          // también con índices
console.log(usuario.saludar?.());                          // y con métodos que pueden no existir

// «||» trata como «falso» el 0, la cadena vacía y false. «??» solo null y undefined.
console.log(usuario.config.volumen || 50);                 // 50: ¡pisa el 0 válido!
console.log(usuario.config.volumen ?? 50);                 // 0: respeta el 0
console.log(usuario.config.tema || "claro", "|", usuario.config.tema ?? "claro", "|");

let opciones = { ruido: null };
opciones.ruido ??= 10;                                     // asigna solo si es null o undefined
opciones.paso ||= 1;                                       // asigna si es «falso»
console.log(opciones);
```

**Salida (ejecutado con Node):**

```text
undefined
undefined
undefined
50
0
claro |  |
{ ruido: 10, paso: 1 }
```

La diferencia entre `||` y `??` es una fuente clásica de fallos: con `||`, un volumen de `0` o un texto vacío **se consideran «no hay valor»** y se sustituyen. Cuando `0`, `""` o `false` son valores válidos, usa `??`.

## Cierres (*closures*)

Una función **recuerda** las variables del lugar donde se creó, aunque ese lugar ya haya terminado. Es lo que permite tener **estado privado**:

```javascript
function crearContador(paso = 1) {
  let cuenta = 0;                         // privada: nadie de fuera puede tocarla
  return {
    subir() { cuenta += paso; return cuenta; },
    ver() { return cuenta; }
  };
}

const a = crearContador();
const b = crearContador(10);
a.subir(); a.subir();
b.subir();
console.log(a.ver(), b.ver());            // cada contador tiene SU cuenta
console.log(a.cuenta);                    // undefined: la variable no es accesible

// El clásico de los bucles
const con_var = [];
for (var i = 0; i < 3; i++) con_var.push(() => i);
const con_let = [];
for (let j = 0; j < 3; j++) con_let.push(() => j);
console.log(con_var.map(f => f()), con_let.map(f => f()));
```

**Salida (ejecutado con Node):**

```text
2 10
undefined
[ 3, 3, 3 ] [ 0, 1, 2 ]
```

El último ejemplo es el error más famoso de JavaScript: con `var`, las tres funciones comparten **la misma** `i` (que al terminar el bucle vale 3). Con `let`, **cada vuelta tiene su propia `j`**. Por eso hoy se usa `let` y `const`, y casi nunca `var`.

## `this`: quién llama

`this` dentro de una función depende de **cómo se llama**, no de dónde se escribió. Se pierde con facilidad al pasar un método como si fuera una función suelta. Las **funciones flecha** no tienen `this` propio: usan el del sitio donde se escribieron.

```javascript
"use strict";
const contador = {
  n: 0,
  subir() { this.n++; return this.n; },
  subirFlecha: () => typeof this,        // una flecha NO tiene this propio
  programar() {
    return [1, 2].map(() => this.n);     // la flecha de dentro usa el this de «programar»: el objeto
  }
};

console.log(contador.subir());           // this = contador
const suelta = contador.subir;
try { suelta(); } catch (e) { console.log("método suelto ->", e.name); }

const atada = contador.subir.bind(contador);       // fija this para siempre
console.log(atada());
console.log(contador.subir.call({ n: 100 }));      // this = el objeto que le pasas

console.log(contador.programar());
```

**Salida (ejecutado con Node):**

```text
1
método suelto -> TypeError
2
101
[ 2, 2 ]
```

| Cómo se llama | Valor de `this` |
|---|---|
| `objeto.metodo()` | `objeto` |
| `funcion()` suelta (modo estricto) | `undefined` (sin modo estricto, el objeto global) |
| `f.call(x)` / `f.apply(x)` / `f.bind(x)` | `x` |
| Una función **flecha** | El `this` de **donde se escribió** |
| `new Clase()` | El objeto nuevo |

!!! tip "Regla práctica"
    Para métodos de un objeto o clase, la forma normal `metodo() { ... }`. Para **callbacks** dentro de un método (`map`, `setTimeout`, un evento), una **flecha**, que conserva el `this` del método.

## Colecciones y utilidades modernas

```javascript
const palabras = ["pera", "manzana", "pera", "kiwi", "manzana", "pera"];

const unicas = new Set(palabras);                          // sin repetidos
console.log(unicas.size, [...unicas]);

const cuenta = new Map();                                  // claves de CUALQUIER tipo, con orden
for (const p of palabras) cuenta.set(p, (cuenta.get(p) ?? 0) + 1);
console.log(cuenta, Object.fromEntries(cuenta));

console.log(palabras.at(-1), palabras.includes("kiwi"));   // at(-1): el último
const ordenadas = palabras.toSorted();                     // NO modifica el original (ES2023)
console.log(ordenadas.slice(0, 3), palabras[0]);

const personas = [{ n: "Ana", edad: 17 }, { n: "Luis", edad: 25 }, { n: "Marta", edad: 17 }];
console.log(Object.groupBy(personas, p => p.edad));        // agrupar por clave
```

**Salida (ejecutado con Node):**

```text
3 [ 'pera', 'manzana', 'kiwi' ]
Map(3) { 'pera' => 3, 'manzana' => 2, 'kiwi' => 1 } { pera: 3, manzana: 2, kiwi: 1 }
pera true
[ 'kiwi', 'manzana', 'manzana' ] pera
[Object: null prototype] {
  '17': [ { n: 'Ana', edad: 17 }, { n: 'Marta', edad: 17 } ],
  '25': [ { n: 'Luis', edad: 25 } ]
}
```

!!! info "Funciones nuevas: comprueba la compatibilidad"
    `toSorted`, `Object.groupBy` y similares son recientes. Funcionan en los navegadores actuales, pero conviene comprobarlo en [Can I use](https://caniuse.com/) si tu proyecto debe funcionar en navegadores antiguos.

## Módulos: repartir el código en archivos

Un proyecto real no cabe en un solo `script.js`. Los **módulos ES** permiten que cada archivo **exporte** lo que quiere compartir y **importe** lo que necesita. Lo que no se exporta queda **privado** al archivo.

```javascript
// matematicas.mjs
const IVA = 0.21;                         // privado: no se exporta

export function conIva(precio) {
  return Math.round(precio * (1 + IVA) * 100) / 100;
}

export const REDONDEO = 2;

export default function descuento(precio, porcentaje) {   // exportación por defecto: una por archivo
  return precio * (1 - porcentaje / 100);
}
```

```javascript
// main.mjs
import descuento, { conIva, REDONDEO } from "./matematicas.mjs";
import * as mates from "./matematicas.mjs";           // todo junto bajo un nombre

console.log(conIva(100), REDONDEO);
console.log(descuento(50, 10));
console.log(Object.keys(mates));                       // IVA no aparece: es privado

// Importación DINÁMICA: se carga cuando hace falta, y devuelve una promesa
const { conIva: iva } = await import("./matematicas.mjs");
console.log(iva(10));
```

**Salida de `node main.mjs`:**

```text
121 2
45
[ 'REDONDEO', 'conIva', 'default' ]
12.1
```

En una página HTML, un módulo se enlaza con **`<script type="module" src="main.js"></script>`**. Tres diferencias con un script normal:

* Se carga con `defer` automático: se ejecuta cuando el HTML ya está leído, así que **puede ir en el `<head>`**.
* Tiene **ámbito propio**: sus variables no se filtran al resto (ni a `window`).
* Va en **modo estricto** siempre.
* Hay que servir la página desde un **servidor** (no abriendo el archivo con doble clic): los módulos no se cargan desde `file://`.

!!! warning "Ese servidor puede ser el más sencillo"
    Basta con `npx serve` o la extensión *Live Server* de VS Code. No hace falta nada más para probar módulos en local.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Usar `||` para valores por defecto cuando `0` o `""` son válidos | `??` |
| Creer que `{ ...obj }` copia todo | Es superficial: `structuredClone` para copiar en profundidad |
| Perder el `this` al pasar `objeto.metodo` como callback | `bind`, una flecha, o llamarlo dentro de una flecha |
| `var` en bucles con funciones dentro | `let` / `const` |
| `import` en un `.js` normal sin `type="module"` | `<script type="module">` y servir por HTTP |
| Una exportación por defecto cuyo nombre cambia en cada importación | Preferir exportaciones **con nombre**: el editor las autocompleta y no se confunden |

## Para practicar

Los ejercicios WA1.1 a WA1.3 de [WA1 · Ejercicios](ejercicios.md) practican desestructurar, `?.`/`??` y cierres.
