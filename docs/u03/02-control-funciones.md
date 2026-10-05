# 3.2 Control de flujo y funciones

## Condicionales

<div class="demo" data-alto="14rem" data-consola="1" data-code="const nota = 7;&#10;if (nota &gt;= 9) {&#10;  console.log(&quot;Sobresaliente&quot;);&#10;} else if (nota &gt;= 7) {&#10;  console.log(&quot;Notable&quot;);&#10;} else if (nota &gt;= 5) {&#10;  console.log(&quot;Aprobado&quot;);&#10;} else {&#10;  console.log(&quot;Suspenso&quot;);&#10;}&#10;&#10;const mayor = 20 &gt;= 18 ? &quot;mayor de edad&quot; : &quot;menor de edad&quot;;   // operador ternario&#10;console.log(mayor);"></div>

## Bucles

<div class="demo" data-alto="14rem" data-consola="1" data-code="for (let i = 1; i &lt;= 3; i++) {&#10;  console.log(&quot;Vuelta &quot; + i);&#10;}&#10;&#10;let cuenta = 3;&#10;while (cuenta &gt; 0) {&#10;  console.log(cuenta);&#10;  cuenta--;&#10;}&#10;&#10;for (const fruta of [&quot;pera&quot;, &quot;uva&quot;]) {&#10;  console.log(fruta);&#10;}"></div>

!!! tip "Cortar un bucle"
    Puedes cortar con `break` y saltar con `continue`, pero muchas veces queda más claro usar una variable de control (una *bandera*) en la condición del `while`.

## Funciones

<div class="demo" data-alto="13rem" data-consola="1" data-code="function suma(a, b) {&#10;  return a + b;&#10;}&#10;&#10;const doble = (x) =&gt; x * 2;              // función flecha&#10;const saludar = (nombre = &quot;mundo&quot;) =&gt; `Hola, ${nombre}`;   // valor por defecto&#10;&#10;console.log(suma(2, 3));&#10;console.log(doble(21));&#10;console.log(saludar());&#10;console.log(saludar(&quot;Ana&quot;));"></div>

* Una función puede **devolver** un valor con `return`.
* Las funciones son **valores**: se guardan en variables y se pasan a otras funciones (por ejemplo, a un `addEventListener`).
* El ámbito: una variable declarada con `let` o `const` dentro de `{ }` solo existe ahí.
