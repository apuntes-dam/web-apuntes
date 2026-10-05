# H1 · Ejercicios de HTML

<div class="ej-gate" data-unit="u01" data-nombre="U1 · HTML"></div>

Haz cada ejercicio en su propio archivo `.html`, **sin CSS** (nada de `<style>`): solo HTML. Comprueba el resultado en el navegador y valida el código con el validador del W3C.

## Ejercicio H1.1

**Estructura base.** Crea una carpeta con tres archivos `index.html`, `estilos.css` y `script.js` (vacíos los dos últimos). En `index.html` escribe un esqueleto HTML5 con idioma español, `charset` UTF-8, el título «Repaso», el enlace al CSS en el `<head>` y el `<script>` justo antes de cerrar el `<body>`.

<details class="sol" data-key="web/u01/H1.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>&lt;!DOCTYPE html&gt;
&lt;html lang="es"&gt;
&lt;head&gt;
  &lt;meta charset="utf-8"&gt;
  &lt;title&gt;Repaso&lt;/title&gt;
  &lt;link rel="stylesheet" href="estilos.css"&gt;
&lt;!-- ... (resto de la solución bloqueado) --&gt;</code></pre></div>
</details>

## Ejercicio H1.2

**Texto con formato.** Escribe la frase «Hola mundo! Estoy programando» ocho veces, cada una con un formato distinto: negrita, cursiva, subrayada, pequeña, tachada, subíndice, superíndice y como código.

## Ejercicio H1.3

**Una biografía.** Crea una página sobre un personaje de la informática (por ejemplo, Ada Lovelace) con un `h1`, tres secciones con `h2`, párrafos, una cita con `blockquote` y una línea final con la fuente. Usa los títulos por su nivel, no por su tamaño.

## Ejercicio H1.4

**Listas.** Crea una lista desordenada de tus asignaturas, una lista ordenada con los pasos para hacer una tortilla y una lista **anidada** de ciudades agrupadas por país.

## Ejercicio H1.5

**Lista de definiciones.** Crea un pequeño glosario con cinco términos de informática usando `<dl>`, `<dt>` y `<dd>`.

## Ejercicio H1.6

**Enlaces.** Crea una página con: tres enlaces externos que se abran en una pestaña nueva (con `rel="noopener"`), un enlace a otra página tuya, un enlace de correo, un enlace de teléfono y un índice con **anclas** a tres secciones de la misma página.

## Ejercicio H1.7

**Imágenes.** Inserta una imagen con su `alt` y su tamaño, una imagen dentro de `<figure>` con `<figcaption>` y una imagen que sea a la vez un enlace. Explica qué pasa si la ruta de `src` es incorrecta.

## Ejercicio H1.8

**Una tabla.** Crea la tabla de tu horario semanal con `<caption>`, cabecera, cuerpo y pie. Haz que una celda ocupe dos columnas (`colspan`) y otra dos filas (`rowspan`).

<details class="sol" data-key="web/u01/H1.8">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>&lt;table border="1"&gt;
  &lt;caption&gt;Mi horario&lt;/caption&gt;
  &lt;thead&gt;
    &lt;tr&gt;&lt;th&gt;Hora&lt;/th&gt;&lt;th&gt;Lunes&lt;/th&gt;&lt;th&gt;Martes&lt;/th&gt;&lt;th&gt;Miércoles&lt;/th&gt;&lt;/tr&gt;
  &lt;/thead&gt;
  &lt;tbody&gt;
&lt;!-- ... (resto de la solución bloqueado) --&gt;</code></pre></div>
</details>

## Ejercicio H1.9

**Formulario de contacto.** Crea un formulario con: nombre (texto), correo (email), curso (`select`), experiencia (dos botones de opción), una casilla de «acepto las condiciones» y un mensaje (`textarea`), todo con su `<label>` y un botón de envío.

<details class="sol" data-key="web/u01/H1.9">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>&lt;form action="#" method="post"&gt;
  &lt;p&gt;
    &lt;label for="nombre"&gt;Nombre:&lt;/label&gt;
    &lt;input type="text" id="nombre" name="nombre" required&gt;
  &lt;/p&gt;
  &lt;p&gt;
&lt;!-- ... (resto de la solución bloqueado) --&gt;</code></pre></div>
</details>

## Ejercicio H1.10

**Formulario de registro con validación.** Crea un formulario con usuario (mínimo 4 caracteres), contraseña (mínimo 8), edad (número entre 16 y 99), fecha de nacimiento y DNI (8 números y una letra, con `pattern`). Todos los campos obligatorios. Comprueba que el navegador avisa de los errores.

## Ejercicio H1.11

**Página semántica.** Maqueta una página con `header` (logo y `nav` con tres enlaces), `main` con una `section` y dos `article`, un `aside` con enlaces relacionados y un `footer`. No uses ningún `div`.

<details class="sol" data-key="web/u01/H1.11">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>&lt;body&gt;
  &lt;header&gt;
    &lt;h1&gt;Mi blog&lt;/h1&gt;
    &lt;nav&gt;
      &lt;a href="#"&gt;Inicio&lt;/a&gt;
      &lt;a href="#"&gt;Artículos&lt;/a&gt;
&lt;!-- ... (resto de la solución bloqueado) --&gt;</code></pre></div>
</details>

## Ejercicio H1.12

**Reto: tabla compleja.** Reproduce un horario de clases con una celda de «Recreo» que ocupe toda la fila y una asignatura de dos horas seguidas (`rowspan`).

## Ejercicio H1.13

**Reto: tu currículum.** Crea tu currículum solo con HTML: datos personales, foto, formación y experiencia en listas, habilidades en una tabla y un formulario de contacto. Debe ser semántico y accesible.

## Ejercicio H1.14

**Arregla el código.** Este fragmento tiene al menos seis problemas de accesibilidad y estructura. Encuéntralos y corrígelos:

```html
<div>Mi sitio</div>
<img src="foto.jpg">
<a href="info.html">pincha aquí</a>
<h3>Contacto</h3>
<input type="text" name="correo">
<h1>Otro título</h1>
```
