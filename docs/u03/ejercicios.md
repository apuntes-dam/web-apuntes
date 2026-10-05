# J3 · Ejercicios de JavaScript

<div class="ej-gate" data-unit="u03" data-nombre="U3 · JavaScript"></div>

Los ejercicios 3.1 a 3.7 se pueden hacer en la consola o en un `script.js`. Del 3.8 en adelante necesitas una página con `index.html`, `estilos.css` y `script.js` enlazados. **Comprueba cada ejercicio en el navegador** antes de pasar al siguiente.

## Ejercicio J3.1

**Tipos.** Declara cinco variables de tipos distintos (número, texto, booleano, `undefined` y `null`) y muestra por consola el valor y el `typeof` de cada una. ¿Qué te sorprende?

## Ejercicio J3.2

**Conversión.** Pide dos números con `prompt()` y muestra su suma. Comprueba qué pasa si **no** los conviertes con `Number()` y explica por qué.

## Ejercicio J3.3

**Nota a calificación.** Escribe una función `calificacion(nota)` que devuelva «Suspenso», «Aprobado», «Notable» o «Sobresaliente», o «Nota no válida» si no está entre 0 y 10. Pruébala con varios valores.

<details class="sol" data-key="web/u03/J3.3">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>function calificacion(nota) {
  if (typeof nota !== "number" || Number.isNaN(nota) || nota &lt; 0 || nota &gt; 10) {
    return "Nota no válida";
  }
  if (nota &gt;= 9) return "Sobresaliente";
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio J3.4

**Bucles.** Muestra la tabla del 7, la suma de los números del 1 al `n` y los números del 1 al 30 sustituyendo los múltiplos de 3 por `Fizz`, los de 5 por `Buzz` y los de ambos por `FizzBuzz`.

## Ejercicio J3.5

**Funciones.** Escribe `esPar(n)`, `media(array)` y una versión con función flecha de `cuadrado(n)`. Usa un valor por defecto en una de ellas.

<details class="sol" data-key="web/u03/J3.5">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>function esPar(n) {
  return n % 2 === 0;
}
function media(numeros, decimales = 2) {
  const suma = numeros.reduce((total, n) =&gt; total + n, 0);
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio J3.6

**Arrays.** Con `[6.5, 4.2, 9.1, 7, 3.8, 5]` calcula, con `filter`, `map` y `reduce`: las aprobadas, las notas redondeadas y la media. Muestra también la mayor sin usar un bucle `for`.

<details class="sol" data-key="web/u03/J3.6">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>const notas = [6.5, 4.2, 9.1, 7, 3.8, 5];
const aprobadas = notas.filter(n =&gt; n &gt;= 5);
const redondeadas = notas.map(n =&gt; Math.round(n));
const media = notas.reduce((total, n) =&gt; total + n, 0) / notas.length;
const mayor = Math.max(...notas);
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio J3.7

**Objetos.** Crea un objeto `alumno` con nombre, un array de notas y un método `promedio()`. Añade una propiedad nueva y muestra todas las claves con `Object.keys`.

## Ejercicio J3.8

**Seleccionar elementos.** Crea una página con dos párrafos (`id="saludo"` e `id="nombreAlumno"`), un campo de texto, una imagen y cinco botones (`btn1` a `btn5`), todos con `id`. En `script.js`, guarda las referencias con `document.getElementById` y muestra alguna por consola para comprobarlo.

## Ejercicio J3.9

**Evento `click`: cambiar un color.** Al pulsar el botón 2, cambia el color del texto del párrafo `#saludo`. Haz que cada pulsación **alterne** entre dos colores.

<details class="sol" data-key="web/u03/J3.9">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>const saludo = document.getElementById("saludo");
const btn2 = document.getElementById("btn2");
btn2.addEventListener("click", () =&gt; {
  saludo.style.color = saludo.style.color === "blue" ? "black" : "blue";
});
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio J3.10

**`mouseover` frente a `:hover`.** Haz que al pasar el ratón por `#nombreAlumno` aumente el tamaño de su letra y que se deshaga al salir (`mouseover` y `mouseout`). Después consigue lo mismo solo con CSS y decide cuál es mejor en este caso.

## Ejercicio J3.11

**`click` frente a `dblclick`.** Sobre el botón 5, un clic simple pone el nombre en cursiva y un doble clic pone el fondo del saludo en verde claro. Comprueba que ambos eventos conviven.

## Ejercicio J3.12

**Leer un campo.** Al pulsar el botón 3, toma el texto del campo y muéstralo en el párrafo `#saludo`, sustituyendo lo que hubiera. Si el campo está vacío, muestra un aviso.

<details class="sol" data-key="web/u03/J3.12">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>const campo = document.getElementById("campoTexto");
const saludo = document.getElementById("saludo");
const btn3 = document.getElementById("btn3");
btn3.addEventListener("click", () =&gt; {
  const texto = campo.value.trim();
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio J3.13

**Título de la pestaña.** Al pulsar el botón 4, cambia `document.title`. Observa dónde se ve el cambio.

## Ejercicio J3.14

**Rotar imágenes.** Guarda tres rutas de imagen en un array y haz que cada pulsación del botón 1 muestre la siguiente, volviendo a la primera al llegar al final (con el operador `%`).

<details class="sol" data-key="web/u03/J3.14">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>const foto = document.getElementById("foto");
const btn1 = document.getElementById("btn1");
const imagenes = ["img/a.jpg", "img/b.jpg", "img/c.jpg"];
let actual = 0;
btn1.addEventListener("click", () =&gt; {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio J3.15

**Lista de tareas.** Un campo, un botón «Añadir» y una lista. Cada tarea nueva se añade con `createElement` y `appendChild` **sin borrar las anteriores**. Opcional: un botón para borrar cada tarea.

<details class="sol" data-key="web/u03/J3.15">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>const campo = document.getElementById("nuevaTarea");
const boton = document.getElementById("anadir");
const lista = document.getElementById("tareas");
boton.addEventListener("click", () =&gt; {
  const texto = campo.value.trim();
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio J3.16

**Validación de un formulario.** Crea un formulario de registro (usuario, correo, contraseña de al menos 8 caracteres). Valídalo en el evento `submit` con `preventDefault()`, mostrando el error junto al campo y un «Registro correcto» si todo está bien.

## Ejercicio J3.17

**`localStorage`.** Crea un contador con un botón que sume 1 y que **conserve el valor al recargar la página**. Añade un botón para ponerlo a cero.

<details class="sol" data-key="web/u03/J3.17">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>const salida = document.getElementById("contador");
const mas = document.getElementById("mas");
const cero = document.getElementById("cero");
let cuenta = Number(localStorage.getItem("cuenta")) || 0;
salida.textContent = cuenta;
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio J3.18

**`fetch`.** Pide a una API pública de pruebas (por ejemplo `https://jsonplaceholder.typicode.com/users?_limit=5`) una lista de usuarios y píntala en una tabla. Controla el error si no hay conexión.
