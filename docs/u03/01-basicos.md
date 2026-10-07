# 3.1 Primeros pasos: variables y tipos

JavaScript se puede probar en la **consola** del navegador (F12) o en un archivo `script.js`. Para mostrar algo en la consola:

<div class="demo" data-alto="8rem" data-consola="1" data-code="console.log(&quot;Hola, JavaScript&quot;);&#10;console.log(2 + 3);"></div>

## Variables y constantes

```js
let edad = 20;          // variable (puede cambiar)
const IVA = 0.21;       // constante (no se reasigna)
edad = 21;
```

!!! warning "Evita `var`"
    `var` es la forma antigua y tiene reglas confusas. Usa `const` por defecto y `let` solo si el valor cambia.

## Tipos de datos

<div class="demo" data-alto="11rem" data-consola="1" data-code="const nombre = &quot;Ana&quot;;        // string&#10;const edad = 20;             // number (enteros y decimales)&#10;const activo = true;         // boolean&#10;let sinValor;                // undefined (sin asignar)&#10;const vacio = null;          // null (vacío a propósito)&#10;&#10;console.log(typeof nombre, typeof edad, typeof activo);&#10;console.log(typeof sinValor, typeof vacio);   // ¡null da &quot;object&quot;! (un fallo histórico)&#10;console.log(`Hola, ${nombre}. El año que viene tendrás ${edad + 1}.`);"></div>

| Tipo | Ejemplo | Nota |
|---|---|---|
| `number` | `42`, `3.14` | Un único tipo para enteros y decimales |
| `string` | `"hola"`, `'hola'`, `` `hola ${x}` `` | Las comillas invertidas permiten insertar valores con `${}` |
| `boolean` | `true`, `false` | |
| `undefined` | `let x;` | Variable sin valor |
| `null` | `null` | Ausencia intencionada |
| `object` / `array` | `{}` · `[]` | Se ven en el apartado 3.3 |

## Operadores

| Grupo | Operadores |
|---|---|
| Aritméticos | `+ - * / % **` |
| Comparación | `=== !== < > <= >=` |
| Lógicos | `&& \|\| !` |
| Asignación | `= += -= ++ --` |

!!! danger "Usa `===`, no `==`"
    `==` convierte tipos y da sorpresas: `"5" == 5` es `true`. `===` compara valor **y** tipo: `"5" === 5` es `false`.

## Conversión de tipos

<div class="demo" data-alto="11rem" data-consola="1" data-code="console.log(&quot;5&quot; + 3);              // &quot;53&quot; (concatena)&#10;console.log(Number(&quot;5&quot;) + 3);      // 8&#10;console.log(parseInt(&quot;42px&quot;));     // 42&#10;console.log(parseFloat(&quot;3.5&quot;));    // 3.5&#10;console.log(Number(&quot;hola&quot;));       // NaN (no es un número)&#10;console.log(String(123), (7).toString());"></div>

Lo que devuelve `prompt()` o lee un `input` es **siempre texto**: conviértelo con `Number(...)` antes de operar. `prompt()` y `alert()` sirven para aprender, pero en una web real se usan formularios y elementos de la página, que se ven en los apartados siguientes.
