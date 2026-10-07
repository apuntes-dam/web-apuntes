# WA3 · Ejercicios de CSS moderno

<div class="ej-gate" data-unit="a3" data-nombre="WA3 · CSS moderno"></div>

Estos ejercicios son de **CSS**: se hacen en una página HTML y **se comprueban en el navegador** (no hay salida de consola que comparar, salvo donde se indica). Crea un `index.html` con el HTML del enunciado y escribe tu CSS en una etiqueta `<style>` o en un `estilos.css` enlazado. Las soluciones modelo se han comprobado en un navegador real. Los marcados con ⭐ son más difíciles.

## Ejercicio WA3.1

**Un tema con variables.** Parte de esta tarjeta. Define **cuatro variables** en `:root` (fondo, texto, acento y radio), úsalas en todas las reglas de la tarjeta y haz que `<body data-tema="oscuro">` cambie **solo las variables** (no ninguna regla de la tarjeta) para obtener un tema oscuro. Prueba el tema oscuro cambiando el atributo en las herramientas del navegador.

```html
<article class="tarjeta">
  <h2>Tema con variables</h2>
  <p>Un texto de ejemplo.</p>
  <button>Aceptar</button>
</article>
```

<details class="sol" data-key="web/av-a3/WA3.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>&lt;article class="tarjeta"&gt;
  &lt;h2&gt;Tema con variables&lt;/h2&gt;
  &lt;p&gt;Un texto de ejemplo.&lt;/p&gt;
  &lt;button&gt;Aceptar&lt;/button&gt;
&lt;/article&gt;
&lt;style&gt;
&lt;!-- ... (resto de la solución bloqueado) --&gt;</code></pre></div>
</details>

## Ejercicio WA3.2

**Título fluido.** Haz que el `<h1>` mida **1,5 rem como mínimo, 3 rem como máximo** y, entre medias, `1rem + 4vw`, **con una sola declaración** (sin consultas de medios). Comprueba en la consola cuánto mide con la ventana a distintos anchos: a 400 px debe medir **unos 32 px** (16 + 16; algo menos si la página tiene barra de desplazamiento, que resta unos píxeles al ancho) y a 1000 px debe quedarse en **48 px**.

```html
<h1 id="titulo">Título fluido</h1>
<script>
  const mostrar = () => console.log(innerWidth + " px ->", getComputedStyle(document.getElementById("titulo")).fontSize);
  mostrar(); addEventListener("resize", mostrar);
</script>
```

<details class="sol" data-key="web/av-a3/WA3.2">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>&lt;h1 id="titulo"&gt;Título fluido&lt;/h1&gt;
&lt;style&gt;
  h1 { font-size: clamp(1.5rem, 1rem + 4vw, 3rem); }
&lt;/style&gt;
&lt;script&gt;
  const mostrar = () =&gt; console.log(innerWidth + " px -&gt;", getComputedStyle(document.getElementById("titulo")).fontSize);
&lt;!-- ... (resto de la solución bloqueado) --&gt;</code></pre></div>
</details>

## Ejercicio WA3.3

**El selector «padre».** Con **solo CSS** (sin JavaScript):

1. Cada `<label class="op">` debe tener **borde azul y fondo azul claro** cuando **su** casilla está marcada.
2. Cada `<article class="producto">` que **contenga una `<img>`** debe tener un borde grueso; las que no, borde fino.

```html
<label class="op"><input type="checkbox"> Opción A</label>
<label class="op"><input type="checkbox"> Opción B</label>

<article class="producto"><img alt="" width="40" src="data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='40' height='40'><rect width='40' height='40' fill='tomato'/></svg>"> Con foto</article>
<article class="producto">Sin foto</article>
```

<details class="sol" data-key="web/av-a3/WA3.3">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>&lt;label class="op"&gt;&lt;input type="checkbox"&gt; Opción A&lt;/label&gt;
&lt;label class="op"&gt;&lt;input type="checkbox"&gt; Opción B&lt;/label&gt;
&lt;article class="producto"&gt;&lt;img alt="" width="40" src="data:image/svg+xml;utf8,&lt;svg xmlns='http://www.w3.org/2000/svg' width='40' height='40'&gt;&lt;rect width='40' height='40' fill='tomato'/&gt;&lt;/svg&gt;"&gt; Con foto&lt;/article&gt;
&lt;article class="producto"&gt;Sin foto&lt;/article&gt;
&lt;style&gt;
  .op { display: block; margin: .25rem 0; padding: .4rem; border: 2px solid #cbd5e1; border-radius: 8px; }
&lt;!-- ... (resto de la solución bloqueado) --&gt;</code></pre></div>
</details>

## Ejercicio WA3.4

**Ganar sin `!important`.** Este botón debería ser **verde** (la regla `.btn`), pero la regla `#acceso` de la hoja «de la librería» lo pinta de rojo por ser más específica. **Sin tocar los selectores y sin `!important`**, usa `@layer` para que gane el verde. Comprueba el color calculado del botón en la consola.

```html
<button id="acceso" class="btn">Entrar</button>
<style>
  /* hoja de la «librería» */
  #acceso { background: crimson; color: white; }

  /* tu hoja */
  .btn { background: seagreen; color: white; }
</style>
<script>console.log(getComputedStyle(document.getElementById("acceso")).backgroundColor);</script>
```

<details class="sol" data-key="web/av-a3/WA3.4">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>&lt;button id="acceso" class="btn"&gt;Entrar&lt;/button&gt;
&lt;style&gt;
  @layer libreria, mio;                 /* «mio» pesa más que «libreria» */
  @layer libreria {
    #acceso { background: crimson; color: white; }
  }
&lt;!-- ... (resto de la solución bloqueado) --&gt;</code></pre></div>
</details>

## Ejercicio WA3.5

**Una tarjeta que se adapta a su sitio.** Dentro de una caja redimensionable, haz que la tarjeta se muestre **en columna** (imagen arriba, texto debajo) y pase a **dos columnas** (imagen a la izquierda, texto a la derecha) **solo cuando la caja mida 30 rem o más**, con una consulta de **contenedor** (no de medios).

```html
<div class="caja">
  <article class="tarjeta"><div class="foto">🖼</div><div><h3>Título</h3><p>Texto de la tarjeta.</p></div></article>
</div>
<style>
  .caja { resize: horizontal; overflow: auto; width: 20rem; max-width: 100%; border: 2px dashed gray; padding: .5rem; }
  .foto { font-size: 3rem; text-align: center; }
</style>
```

<details class="sol" data-key="web/av-a3/WA3.5">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>&lt;div class="caja"&gt;
  &lt;article class="tarjeta"&gt;&lt;div class="foto"&gt;🖼&lt;/div&gt;&lt;div&gt;&lt;h3&gt;Título&lt;/h3&gt;&lt;p&gt;Texto de la tarjeta.&lt;/p&gt;&lt;/div&gt;&lt;/article&gt;
&lt;/div&gt;
&lt;style&gt;
  .caja { resize: horizontal; overflow: auto; width: 20rem; max-width: 100%; border: 2px dashed gray; padding: .5rem;
          container-type: inline-size; }
&lt;!-- ... (resto de la solución bloqueado) --&gt;</code></pre></div>
</details>

## Ejercicio WA3.6

⭐ **Galería adaptable con pies alineados.** Con **solo CSS**: una rejilla de 6 tarjetas con **columnas de al menos 10 rem** que se reparten el ancho (usando `auto-fit` y `minmax`, sin consultas de medios), de forma que **el pie de cada tarjeta quede alineado con los de su fila** aunque los textos tengan longitudes distintas (usa `subgrid`).

```html
<div class="galeria">
  <article class="t"><h3>Uno</h3><p>Texto corto.</p><footer>Ver</footer></article>
  <article class="t"><h3>Dos con título largo largo largo</h3><p>Texto.</p><footer>Ver</footer></article>
  <article class="t"><h3>Tres</h3><p>Un texto más largo que ocupa varias líneas en la tarjeta.</p><footer>Ver</footer></article>
  <article class="t"><h3>Cuatro</h3><p>Texto.</p><footer>Ver</footer></article>
  <article class="t"><h3>Cinco</h3><p>Texto corto.</p><footer>Ver</footer></article>
  <article class="t"><h3>Seis</h3><p>Texto.</p><footer>Ver</footer></article>
</div>
```

<details class="sol" data-key="web/av-a3/WA3.6">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>&lt;div class="galeria"&gt;
  &lt;article class="t"&gt;&lt;h3&gt;Uno&lt;/h3&gt;&lt;p&gt;Texto corto.&lt;/p&gt;&lt;footer&gt;Ver&lt;/footer&gt;&lt;/article&gt;
  &lt;article class="t"&gt;&lt;h3&gt;Dos con título largo largo largo&lt;/h3&gt;&lt;p&gt;Texto.&lt;/p&gt;&lt;footer&gt;Ver&lt;/footer&gt;&lt;/article&gt;
  &lt;article class="t"&gt;&lt;h3&gt;Tres&lt;/h3&gt;&lt;p&gt;Un texto más largo que ocupa varias líneas en la tarjeta.&lt;/p&gt;&lt;footer&gt;Ver&lt;/footer&gt;&lt;/article&gt;
  &lt;article class="t"&gt;&lt;h3&gt;Cuatro&lt;/h3&gt;&lt;p&gt;Texto.&lt;/p&gt;&lt;footer&gt;Ver&lt;/footer&gt;&lt;/article&gt;
  &lt;article class="t"&gt;&lt;h3&gt;Cinco&lt;/h3&gt;&lt;p&gt;Texto corto.&lt;/p&gt;&lt;footer&gt;Ver&lt;/footer&gt;&lt;/article&gt;
&lt;!-- ... (resto de la solución bloqueado) --&gt;</code></pre></div>
</details>
