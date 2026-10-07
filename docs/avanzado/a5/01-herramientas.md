# WA5.1 Herramientas: npm, pruebas y calidad

!!! info "Cómo se han comprobado los ejemplos"
    Los comandos de **npm**, las pruebas con **`node --test`**, el servidor de **CORS** y la lógica de seguridad se han **ejecutado de verdad** al construir la web: lo que ves es su salida. Lo que necesita descargar paquetes (Vite, ESLint, `npm audit`) o servicios externos (GitHub Pages) **no se ha ejecutado aquí** y está marcado «sin ejecutar». Las versiones de las herramientas cambian: comprueba su documentación al usarlas.

## `npm` y `package.json`

**Node.js** ejecuta JavaScript fuera del navegador, y trae **`npm`** (*Node Package Manager*), el gestor de paquetes más grande del mundo: millones de librerías que se instalan con un comando. Un proyecto se describe en un archivo **`package.json`**:

```console
$ npm start
> mi-proyecto@1.0.0 start
> node index.js

hola desde npm
```

Con `package.json`, los **scripts** ponen **nombre** a los comandos largos (`npm start`, `npm test`, `npm run build`...) y todo el que clone el proyecto sabe cómo usarlo. Los campos principales:

| Campo | Para qué |
|---|---|
| `name`, `version` | Identifican el proyecto (la versión sigue las reglas de abajo) |
| `type: "module"` | Los archivos `.js` son **módulos ES** (con `import`/`export`) |
| `scripts` | Comandos con nombre. `start` y `test` se ejecutan con `npm start` y `npm test`; los demás, con `npm run nombre` |
| `dependencies` | Librerías que la aplicación **necesita para funcionar** |
| `devDependencies` | Librerías **solo para desarrollar** (pruebas, formateador, empaquetador) |

```console
$ npm run no-existe
npm error Missing script: "no-existe"
npm error
npm error To see a list of scripts, run:
npm error   npm run
```

### Instalar paquetes (sin ejecutar)

```bash
# Sin ejecutar: necesita descargar paquetes de internet
npm install                   # instala todo lo que pide package.json
npm install dayjs             # añade una librería a «dependencies»
npm install --save-dev eslint # la añade a «devDependencies»
npm ci                        # instalación EXACTA según package-lock.json (lo que se usa en integración continua)
```

Instalar crea dos cosas que conviene entender:

* **`node_modules/`**: la carpeta con **todo el código de los paquetes** (y de **sus** dependencias). Puede tener miles de archivos y **nunca se sube a Git**: se añade a `.gitignore` y se regenera con `npm install`.
* **`package-lock.json`**: la lista **exacta** de versiones instaladas. Este sí **se sube**: garantiza que todo el equipo (y el servidor) usan **las mismas versiones**.

## Versiones: ^, ~ y el versionado semántico

Las versiones tienen tres números, **mayor.menor.parche** (*SemVer*), con un significado acordado:

| Cambia | Significa | Ejemplo |
|---|---|---|
| **Parche** (1.2.**3**) | Arreglo de fallos, **compatible** | 1.2.3 → 1.2.4 |
| **Menor** (1.**2**.3) | Funciones nuevas, **compatibles** | 1.2.3 → 1.3.0 |
| **Mayor** (**1**.2.3) | Cambios que **rompen** la compatibilidad | 1.2.3 → 2.0.0 |

En `package.json` el **rango** de versiones que aceptas se escribe con un símbolo delante. Es lógica sencilla, así que se puede escribir y comprobar:

```javascript
const partes = v => v.split(".").map(Number);

function satisface(version, rango) {
  const [M, m, p] = partes(version);
  const op = rango[0];
  if (op !== "^" && op !== "~") return version === rango;           // sin símbolo: exactamente esa versión

  const [RM, Rm, Rp] = partes(rango.slice(1));
  const esIgualOMayor = M > RM || (M === RM && (m > Rm || (m === Rm && p >= Rp)));
  if (!esIgualOMayor) return false;

  if (op === "~") return M === RM && m === Rm;                       // ~1.2.3: solo cambia el parche
  if (RM > 0) return M === RM;                                       // ^1.2.3: el mayor no cambia
  if (Rm > 0) return M === 0 && m === Rm;                            // ^0.2.3: el menor no cambia (versiones 0.x son inestables)
  return M === 0 && m === 0 && p === Rp;                             // ^0.0.3: nada cambia
}

for (const [rango, versiones] of [["^1.2.3", ["1.2.3", "1.9.0", "2.0.0", "1.2.2"]], ["~1.2.3", ["1.2.9", "1.3.0"]], ["^0.2.3", ["0.2.9", "0.3.0"]], ["^0.0.3", ["0.0.3", "0.0.4"]], ["4.17.21", ["4.17.21", "4.17.22"]]]) {
  console.log(rango.padEnd(7), versiones.map(v => v + (satisface(v, rango) ? " ✔" : " ✘")).join("   "));
}
```

**Salida (ejecutado con Node):**

```text
^1.2.3  1.2.3 ✔   1.9.0 ✔   2.0.0 ✘   1.2.2 ✘
~1.2.3  1.2.9 ✔   1.3.0 ✘
^0.2.3  0.2.9 ✔   0.3.0 ✘
^0.0.3  0.0.3 ✔   0.0.4 ✘
4.17.21 4.17.21 ✔   4.17.22 ✘
```

| Rango | Acepta | Se usa para |
|---|---|---|
| `^1.2.3` | `>= 1.2.3` y `< 2.0.0` | **Lo habitual** (npm lo escribe por defecto): mejoras y arreglos, nunca cambios incompatibles |
| `~1.2.3` | `>= 1.2.3` y `< 1.3.0` | Solo arreglos |
| `1.2.3` | Solo esa | Cuando necesitas fijarla del todo |

(Con versiones `0.x` el símbolo `^` es más estricto: se considera que todo puede romperse.) El `package-lock.json` es lo que **fija de verdad** la versión instalada dentro del rango.

## Pruebas automáticas con `node --test`

Probar a mano, abriendo el navegador, no escala: cada cambio puede romper algo que ya funcionaba. Node trae su **propio ejecutor de pruebas** (`node:test`) y funciones de comprobación (`node:assert`), sin instalar nada. Con la función que se prueba y su archivo de pruebas:

```javascript
// calc.mjs
export const suma = (a, b) => a + b;

export function dividir(a, b) {
  if (b === 0) throw new RangeError("no se puede dividir entre cero");
  return a / b;
}
```

```javascript
// calc.test.mjs
import { test, describe } from "node:test";
import assert from "node:assert/strict";
import { suma, dividir } from "./calc.mjs";

describe("calc", () => {
  test("suma dos números", () => {
    assert.equal(suma(2, 3), 5);
  });

  test("dividir entre cero lanza un error", () => {
    assert.throws(() => dividir(1, 0), RangeError);
  });

  test("dividir devuelve un decimal", async () => {              // las pruebas pueden ser asíncronas
    assert.equal(await Promise.resolve(dividir(7, 2)), 3.5);
  });
});
```

**Salida de `node --test calc.test.mjs` (ejecutado con Node):**

```text
▶ calc
  ✔ suma dos números
  ✔ dividir entre cero lanza un error
  ✔ dividir devuelve un decimal
✔ calc
ℹ tests 3
ℹ pass 3
ℹ fail 0
```

Cada `test(...)` es una prueba independiente y `describe(...)` las agrupa. Las pruebas pueden ser **asíncronas** (`async`). Las comprobaciones más usadas de `assert`:

| Función | Comprueba |
|---|---|
| `assert.equal(real, esperado)` | Igualdad estricta (con `assert/strict`) |
| `assert.deepEqual(real, esperado)` | Igualdad **de contenido** en objetos y arrays |
| `assert.ok(valor)` | Que el valor sea «verdadero» |
| `assert.throws(() => ..., RangeError)` | Que el código **lance** ese error |
| `assert.rejects(promesa)` | Que la promesa **se rechace** |

Y lo importante: una prueba que nunca ha fallado no demuestra nada. Esta tiene un error a propósito:

```javascript
// calc.mjs
export const suma = (a, b) => a + b;

export function dividir(a, b) {
  if (b === 0) throw new RangeError("no se puede dividir entre cero");
  return a / b;
}
```

```javascript
// calc.test.mjs
import { test } from "node:test";
import assert from "node:assert/strict";
import { suma } from "./calc.mjs";

test("suma bien", () => assert.equal(suma(2, 3), 5));
test("esta prueba está mal escrita a propósito", () => assert.equal(suma(2, 2), 5));
```

**Salida de `node --test calc.test.mjs` (ejecutado con Node):**

```text
✔ suma bien
✖ esta prueba está mal escrita a propósito
ℹ tests 2
ℹ pass 1
ℹ fail 1

✖ failing tests:

✖ esta prueba está mal escrita a propósito
  AssertionError [ERR_ASSERTION]: Expected values to be strictly equal:

  4 !== 5
```

Node dice **qué prueba falló** y **qué se esperaba y qué se obtuvo** (`4 !== 5`). Con el script `"test": "node --test"` basta `npm test` para ejecutar todas las pruebas del proyecto (archivos que terminan en `.test.js` o `.test.mjs`):

```console
$ npm test
> mi-proyecto@1.0.0 test
> node --test

▶ calc
  ✔ suma dos números
  ✔ dividir entre cero lanza un error
  ✔ dividir devuelve un decimal
✔ calc
ℹ tests 3
ℹ pass 3
ℹ fail 0
```

!!! tip "Prueba la lógica, no el DOM"
    Lo más fácil de probar es la **lógica sin `document`**: validaciones, cálculos, reductores de estado, `URLSearchParams`... Por eso, en la [WA2](../a2/02-apis.md), `vista(estado)` y `reducir(...)` eran funciones puras. Para probar la interfaz real se usan herramientas como Playwright o Testing Library, que ya son otro nivel.

## Calidad del código: ESLint y Prettier (sin ejecutar)

Dos herramientas que se instalan como `devDependencies` y resuelven cosas distintas:

| Herramienta | Qué hace | Ejemplo |
|---|---|---|
| **ESLint** | **Detecta errores y malas prácticas**: variables sin usar, `==` en lugar de `===`, `await` olvidado | «`x` está definida pero nunca se usa» |
| **Prettier** | **Da formato** al código (comillas, sangría, longitud de línea). Acaba con las discusiones de estilo | Reescribe el archivo entero con un estilo único |

```javascript
// Sin ejecutar: eslint.config.js (configuración «plana» actual de ESLint)
import js from "@eslint/js";

export default [
  js.configs.recommended,
  {
    rules: {
      "no-unused-vars": "warn",
      "eqeqeq": "error"              // obliga a usar === y !==
    }
  }
];
```

```json
{
  "scripts": {
    "lint": "eslint .",
    "format": "prettier --write ."
  }
}
```

Lo normal es **ejecutarlas automáticamente**: en el editor (se corrige al guardar), antes de cada *commit* con un hook de Git (como el de la [A5 de Git](https://apuntes-dam.github.io/git-apuntes/avanzado/a5/index.md)) y en la integración continua del servidor.

## Empaquetar con Vite (sin ejecutar)

Cuando un proyecto crece (decenas de módulos, librerías de npm, TypeScript, CSS por piezas), el navegador no puede cargar todo por separado de forma eficiente. Un **empaquetador** (*bundler*) junta, **minifica** y optimiza todo en unos pocos archivos. Hoy la opción más sencilla es **Vite**:

```bash
# Sin ejecutar: necesita descargar paquetes de internet
npm create vite@latest mi-web      # plantilla de proyecto
cd mi-web
npm install
npm run dev                         # servidor local con recarga instantánea al guardar
npm run build                       # genera la carpeta dist/ con la web lista para publicar
```

`npm run build` deja en **`dist/`** los archivos **minificados y con nombres con huella** (`index-a1b2c3.js`), listos para subir a un servidor. **Para una web pequeña** (unas páginas, un `script.js`) **no hace falta ningún empaquetador**: los módulos nativos de la [WA1](../a1/01-moderno.md) bastan. Se usa cuando el proyecto lo pide, no por costumbre.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Subir `node_modules/` a Git | `.gitignore` con `node_modules/` |
| No subir `package-lock.json` | Se sube: fija las versiones |
| Todo en `dependencies` | Lo que solo sirve para desarrollar, a `devDependencies` |
| Instalar un paquete para una tontería (una función de dos líneas) | Cada dependencia es código ajeno que mantener y revisar |
| Pruebas que nunca han fallado | Comprobar que fallan cuando deben |
| Probar solo a mano en el navegador | `npm test` para la lógica |
| Discutir estilo en las revisiones | Prettier para el formato, ESLint para los errores |
| Empaquetador en una web de dos páginas | Módulos nativos hasta que haga falta |

## Para practicar

Los ejercicios WA5.3 y WA5.4 de [WA5 · Ejercicios](ejercicios.md) practican `package.json` y las pruebas con `node --test`.
