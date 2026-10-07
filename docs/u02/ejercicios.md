# C2 · Ejercicios de CSS

<div class="ej-gate" data-unit="u02" data-nombre="U2 · CSS"></div>

Parte de tus páginas HTML de la unidad 1 y enlaza un archivo `estilos.css`. Haz cada ejercicio en un archivo aparte.

## Ejercicio C2.1

**Enlazar el CSS.** Enlaza `estilos.css` desde una página y, desde el CSS, cambia el color de fondo de la página y la fuente del texto. Comprueba que los cambios se ven al recargar.

## Ejercicio C2.2

**Selectores.** En una página con cinco párrafos, da estilo a: todos los párrafos (por elemento), solo a dos (por **clase**) y a uno concreto (por **id**). Explica qué regla gana cuando se aplican a la vez.

## Ejercicio C2.3

**Colores.** Crea cinco cuadrados con el mismo color escrito de cinco formas distintas: nombre, hexadecimal, `rgb`, `rgba` con transparencia y `hsl`.

## Ejercicio C2.4

**Texto.** Da formato a un artículo: tipografía con alternativas, tamaño en `rem`, altura de línea 1.6, título centrado con espaciado entre letras, enlaces sin subrayado y la primera letra del primer párrafo más grande.

## Ejercicio C2.5

**Modelo de caja: una tarjeta.** Crea una tarjeta de 300px de ancho con `padding`, borde, margen, esquinas redondeadas y sombra. Usa `box-sizing: border-box` y explica qué cambia si no lo usas.

<details class="sol" data-key="web/u02/C2.5">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>*, *::before, *::after {
  box-sizing: border-box;
}
.tarjeta {
  width: 300px;
  padding: 20px;
/* ... (resto de la solución bloqueado) */</code></pre></div>
</details>

## Ejercicio C2.6

**Botones y estados.** Crea un botón con `:hover`, `:active` y `:focus-visible` y una transición suave de color. Comprueba también que se puede usar con el teclado.

## Ejercicio C2.7

**Pseudo-clases.** En una lista de diez elementos: filas alternas de distinto color con `:nth-child`, el primero en negrita y el último con un borde inferior (`:first-child`, `:last-child`).

## Ejercicio C2.8

**Posición.** Crea una caja con `position: relative` y una insignia («Nuevo») con `position: absolute` pegada a su esquina superior derecha.

## Ejercicio C2.9

**Flexbox: barra de navegación.** Crea una barra con el logo a la izquierda y tres enlaces a la derecha, alineados verticalmente al centro, usando Flexbox.

<details class="sol" data-key="web/u02/C2.9">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>.barra {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 20px;
  background: #263238;
/* ... (resto de la solución bloqueado) */</code></pre></div>
</details>

## Ejercicio C2.10

**Flexbox: tarjetas que se envuelven.** Crea seis tarjetas en fila con `gap`, que crezcan para repartirse el espacio y pasen a la línea siguiente cuando no caben (`flex-wrap`).

## Ejercicio C2.11

**Grid: galería.** Crea una galería de 3 columnas iguales con `gap`, en la que una de las imágenes ocupe **dos columnas**.

<details class="sol" data-key="web/u02/C2.11">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>.galeria {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 10px;
}
.galeria img {
/* ... (resto de la solución bloqueado) */</code></pre></div>
</details>

## Ejercicio C2.12

**Grid: la página completa.** Maqueta con `grid-template-areas` una página con cabecera, menú lateral, contenido y pie.

## Ejercicio C2.13

**Diseño adaptable.** Haz que la galería del ejercicio C2.11 pase de 3 columnas a 2 en pantallas medianas y a 1 en móvil con `@media`. No olvides la etiqueta `viewport`.

<details class="sol" data-key="web/u02/C2.13">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>.galeria {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 10px;
}
@media (max-width: 900px) {
/* ... (resto de la solución bloqueado) */</code></pre></div>
</details>

## Ejercicio C2.14

**Reto: página de aterrizaje.** Crea una página de presentación de un producto con cabecera con menú, una sección «hero» a pantalla completa centrada con Flexbox, tres tarjetas de características con Grid, un formulario de contacto y un pie. Debe verse bien en móvil y en ordenador.
