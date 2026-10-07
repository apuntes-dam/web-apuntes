# WA3.1 Variables, funciones y selectores modernos

!!! info "Cómo se han comprobado los ejemplos"
    Todos los ejemplos son **editores con vista previa que se ejecutan en tu navegador**: cámbialos y mira el resultado. Los valores que se citan en el texto (tamaños, número de columnas, qué color gana) se han **comprobado en un navegador real**. Las funciones de esta unidad funcionan en los navegadores actuales; si tu proyecto debe funcionar en navegadores antiguos, mira [Can I use](https://caniuse.com/) antes de usarlas.

## Variables CSS (propiedades personalizadas)

Una **variable CSS** se declara con dos guiones (`--acento: #2563eb;`) y se usa con `var(--acento)`. A diferencia de las variables de un preprocesador, **viven en el navegador**: se **heredan**, se pueden **cambiar según el contexto** y JavaScript puede modificarlas en directo.

<div class="demo" data-alto="15rem" data-code="&lt;div class=&quot;tarjeta&quot; id=&quot;t&quot;&gt;&#10;  &lt;h3&gt;Tarjeta con tema&lt;/h3&gt;&#10;  &lt;p&gt;Todo el aspecto sale de cuatro variables.&lt;/p&gt;&#10;  &lt;button id=&quot;b&quot;&gt;Cambiar tema&lt;/button&gt;&#10;  &lt;label&gt;Acento &lt;input type=&quot;color&quot; id=&quot;c&quot; value=&quot;#2563eb&quot;&gt;&lt;/label&gt;&#10;&lt;/div&gt;&#10;&lt;style&gt;&#10;  :root { --fondo: #ffffff; --texto: #1e293b; --acento: #2563eb; --radio: 12px; }&#10;  [data-tema=&quot;oscuro&quot;] { --fondo: #0f172a; --texto: #e2e8f0; --acento: #38bdf8; }   /* solo redefine las variables */&#10;&#10;  .tarjeta { background: var(--fondo); color: var(--texto); border: 2px solid var(--acento); border-radius: var(--radio); padding: 1rem; }&#10;  .tarjeta button { background: var(--acento); color: var(--fondo); border: 0; padding: .4rem .8rem; border-radius: calc(var(--radio) / 2); }&#10;&lt;/style&gt;&#10;&lt;script&gt;&#10;const t = document.getElementById(&quot;t&quot;);&#10;document.getElementById(&quot;b&quot;).addEventListener(&quot;click&quot;, () =&gt; {&#10;  t.dataset.tema = t.dataset.tema === &quot;oscuro&quot; ? &quot;&quot; : &quot;oscuro&quot;;                    // un atributo cambia TODO el tema&#10;  t.style.removeProperty(&quot;--acento&quot;);&#10;});&#10;document.getElementById(&quot;c&quot;).addEventListener(&quot;input&quot;, e =&gt; t.style.setProperty(&quot;--acento&quot;, e.target.value));   // JS cambia una variable&#10;&lt;/script&gt;"></div>

Tres ideas en un ejemplo:

* **Un tema entero** (claro/oscuro) es solo **redefinir las variables** bajo otro selector; ninguna regla de las tarjetas cambia.
* La variable se declara en `:root` (el `<html>`) para que **todo el documento** la herede, pero **puedes redefinirla en cualquier elemento** y solo afectará a sus descendientes.
* JavaScript la cambia con `elemento.style.setProperty("--acento", valor)` y el navegador actualiza **todo lo que la use**.

`var()` acepta un **valor de reserva**: `var(--acento, hotpink)` usa `hotpink` si la variable no está definida.

## Funciones matemáticas: `calc()`, `min()`, `max()` y `clamp()`

| Función | Qué hace | Ejemplo |
|---|---|---|
| `calc()` | Operaciones, **mezclando unidades** | `width: calc(100% - 2rem)` |
| `min(a, b)` | El **menor** de los valores | `width: min(100%, 40rem)` |
| `max(a, b)` | El **mayor** | `padding: max(1rem, 3vw)` |
| `clamp(mín, ideal, máx)` | `ideal`, pero **nunca menor que `mín` ni mayor que `máx`** | `font-size: clamp(1.2rem, 1rem + 3vw, 2.5rem)` |

`clamp()` es la base de la **tipografía fluida**: el tamaño crece con la pantalla **sin saltos** y sin escribir ninguna consulta de medios. Con `clamp(1.2rem, 1rem + 3vw, 2.5rem)` (y 1 rem = 16 px), la fórmula `1rem + 3vw` da:

| Ancho de la ventana | `1rem + 3vw` | Tamaño final |
|---|---|---|
| 200 px | 16 + 6 = 22 px | **22 px** |
| 400 px | 16 + 12 = 28 px | **28 px** |
| 1000 px | 16 + 30 = 46 px | **40 px** (se corta en el máximo, 2,5 rem) |

<div class="demo" data-alto="12rem" data-consola="1" data-code="&lt;h1 id=&quot;titulo&quot; style=&quot;font-size: clamp(1.2rem, 1rem + 3vw, 2.5rem); margin:0&quot;&gt;Título fluido&lt;/h1&gt;&#10;&lt;p style=&quot;width: min(100%, 28rem); background:#e2e8f0; padding:.5rem&quot;&gt;Este párrafo ocupa todo el ancho hasta un máximo de 28 rem.&lt;/p&gt;&#10;&lt;p&gt;Cambia el tamaño de la ventana: el título crece y se detiene en el máximo.&lt;/p&gt;&#10;&lt;script&gt;console.log(&quot;tamaño del título ahora:&quot;, getComputedStyle(document.getElementById(&quot;titulo&quot;)).fontSize, &quot;| ancho de la ventana:&quot;, innerWidth + &quot;px&quot;);&lt;/script&gt;"></div>

!!! tip "Tamaños fluidos en `rem`, no solo en `vw`"
    Si usas **solo `vw`** para el texto (`font-size: 3vw`), un usuario que **aumenta el tamaño de letra** de su navegador no verá ningún cambio: es un problema de accesibilidad. Mezclar `rem` con `vw` (como en `1rem + 3vw`) lo respeta.

## Selectores modernos

### `:is()` y `:where()`: agrupar sin repetir

Los dos hacen lo mismo (aceptan una **lista** de selectores y coinciden con cualquiera) pero **no pesan igual** en la cascada:

| Selector | Especificidad que aporta |
|---|---|
| `:is(.a, #b)` | La del **más específico** de la lista (aquí, la de `#b`) |
| `:where(.a, #b)` | **Cero**, siempre |

Sin ellos, `article h2, section h2, aside h2 { ... }` se repite. Con `:is`: `:is(article, section, aside) h2 { ... }`. Y `:where` es ideal para **estilos base que se puedan pisar fácilmente**:

<div class="demo" data-alto="9rem" data-consola="1" data-code="&lt;div class=&quot;aviso x&quot;&gt;&lt;p id=&quot;p1&quot;&gt;Párrafo con la regla de :is()&lt;/p&gt;&lt;/div&gt;&#10;&lt;div class=&quot;aviso y&quot;&gt;&lt;p id=&quot;p2&quot;&gt;Párrafo con la regla de :where()&lt;/p&gt;&lt;/div&gt;&#10;&lt;style&gt;&#10;  :is(.x, #nada) p { color: red; }       /* peso = el del MÁS específico de la lista (#nada, un id): (1,0,1) */&#10;  :where(.y, #nada) p { color: red; }    /* peso = 0 siempre: solo cuenta la «p»: (0,0,1) */&#10;  .aviso p { color: blue; }              /* (0,1,1), y va DESPUÉS de las dos */&#10;&lt;/style&gt;&#10;&lt;script&gt;&#10;console.log(&quot;p1 (:is):   &quot;, getComputedStyle(document.getElementById(&quot;p1&quot;)).color);&#10;console.log(&quot;p2 (:where):&quot;, getComputedStyle(document.getElementById(&quot;p2&quot;)).color);&#10;&lt;/script&gt;"></div>

Las dos reglas dicen lo mismo (rojo), pero no pesan igual. `:is(.x, #nada)` toma la especificidad **del más específico** de su lista (el `#nada`, un id), así que pesa **(1,0,1)** y gana a `.aviso p` **(0,1,1)** aunque ese vaya después: el párrafo sale **rojo**. `:where(...)` aporta **cero**, pesa solo **(0,0,1)**, y pierde contra `.aviso p`: sale **azul**. Por eso `:where()` es ideal para **estilos base fáciles de pisar** (una librería, un reset): cualquier regla del usuario gana sin pelear.

### `:has()`: el selector «padre»

`:has()` selecciona un elemento **según lo que contiene**. Es lo que se pedía desde siempre: «estiliza la tarjeta **si** tiene una imagen», o «el campo **si** su input es inválido».

<div class="demo" data-alto="13rem" data-code="&lt;form&gt;&#10;  &lt;label class=&quot;opcion&quot;&gt;&lt;input type=&quot;radio&quot; name=&quot;p&quot; checked&gt; Plan básico&lt;/label&gt;&#10;  &lt;label class=&quot;opcion&quot;&gt;&lt;input type=&quot;radio&quot; name=&quot;p&quot;&gt; Plan pro&lt;/label&gt;&#10;  &lt;label class=&quot;opcion&quot;&gt;&lt;input type=&quot;radio&quot; name=&quot;p&quot;&gt; Plan equipo&lt;/label&gt;&#10;  &lt;div class=&quot;campo&quot;&gt;&lt;input type=&quot;email&quot; placeholder=&quot;correo (escribe algo mal)&quot; required&gt;&lt;/div&gt;&#10;&lt;/form&gt;&#10;&lt;style&gt;&#10;  .opcion { display: block; padding: .5rem; margin: .25rem 0; border: 2px solid #cbd5e1; border-radius: 8px; }&#10;  .opcion:has(input:checked) { border-color: #2563eb; background: #dbeafe; }     /* la etiqueta se ilumina si SU radio está marcado */&#10;  .campo:has(input:invalid:not(:placeholder-shown)) { outline: 2px solid crimson; padding: 4px; }&#10;  .campo:has(input:valid) { outline: 2px solid seagreen; padding: 4px; }&#10;&lt;/style&gt;"></div>

Sin `:has()` habría que escribir JavaScript que añadiera y quitara una clase a la etiqueta. Aquí **el CSS reacciona solo** a lo que ocurre dentro.

| Selector | Significado |
|---|---|
| `.tarjeta:has(img)` | Tarjeta que **contiene** una imagen |
| `li:has(> a.activo)` | `li` con un **hijo directo** `a.activo` |
| `h2:has(+ p)` | `h2` **seguido** de un párrafo |
| `label:has(:focus-visible)` | Etiqueta cuyo control tiene el foco del teclado |
| `body:has(dialog[open])` | Toda la página, **si hay un diálogo abierto** (para bloquear el scroll) |

### `:focus-visible` y `:focus-within`

* **`:focus-visible`**: el foco **solo cuando se nota que es del teclado**. Evita el anillo al hacer clic con el ratón, sin quitárselo a quien navega con el teclado (nunca pongas `outline: none` a secas).
* **`:focus-within`**: un contenedor **cuando cualquiera de sus hijos tiene el foco** (resaltar la fila de un formulario).

## Anidamiento nativo

El CSS ya permite **anidar reglas**, como en Sass, sin preprocesador. `&` representa al selector padre:

<div class="demo" data-alto="8rem" data-code="&lt;nav class=&quot;menu&quot;&gt;&#10;  &lt;a href=&quot;#&quot; class=&quot;activo&quot;&gt;Inicio&lt;/a&gt;&#10;  &lt;a href=&quot;#&quot;&gt;Cursos&lt;/a&gt;&#10;  &lt;a href=&quot;#&quot;&gt;Contacto&lt;/a&gt;&#10;&lt;/nav&gt;&#10;&lt;style&gt;&#10;  .menu {&#10;    display: flex; gap: 1rem; padding: .5rem; background: #1e293b;&#10;&#10;    a {                                     /* = .menu a */&#10;      color: #cbd5e1; text-decoration: none; padding: .25rem .5rem; border-radius: 6px;&#10;&#10;      &amp;:hover { background: #334155; }      /* = .menu a:hover */&#10;      &amp;.activo { background: #2563eb; color: white; }   /* = .menu a.activo */&#10;    }&#10;&#10;    @media (max-width: 400px) { flex-direction: column; }   /* las consultas también se anidan */&#10;  }&#10;&lt;/style&gt;"></div>

!!! warning "No anides sin medida"
    Cada nivel **aumenta la especificidad** y ata el CSS a la estructura del HTML. Más de dos o tres niveles suele ser señal de que conviene usar clases más concretas.

## Propiedades lógicas: adiós a «izquierda» y «derecha»

`margin-left` y `padding-right` suponen que el texto va de izquierda a derecha. En árabe o hebreo (de derecha a izquierda) o en escritura vertical, **«inicio» y «final»** son los que se mantienen. Las **propiedades lógicas** usan esos conceptos:

| Física (fija) | Lógica (se adapta) |
|---|---|
| `margin-left` / `margin-right` | `margin-inline-start` / `margin-inline-end` |
| `margin-top` / `margin-bottom` | `margin-block-start` / `margin-block-end` |
| `padding: 0 1rem` (horizontal) | `padding-inline: 1rem` |
| `padding: 1rem 0` (vertical) | `padding-block: 1rem` |
| `width` / `height` | `inline-size` / `block-size` |
| `top: 0; left: 0; right: 0; bottom: 0` | `inset: 0` |

<div class="demo" data-alto="8rem" data-code="&lt;p dir=&quot;ltr&quot; class=&quot;a&quot;&gt;Español (izquierda a derecha)&lt;/p&gt;&#10;&lt;p dir=&quot;rtl&quot; class=&quot;a&quot;&gt;עברית (derecha a izquierda)&lt;/p&gt;&#10;&lt;style&gt;&#10;  .a { border-inline-start: 6px solid #2563eb; padding-inline: 1rem; background: #eff6ff; }   /* la barra va siempre al «inicio» */&#10;&lt;/style&gt;"></div>

La barra azul está a la **izquierda** en el primer párrafo y a la **derecha** en el segundo, **con la misma regla**.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Valores repetidos por toda la hoja de estilos | Variables CSS en `:root` |
| Tamaño de letra solo en `vw` | Mezclar con `rem`: `clamp(1rem, 0.8rem + 2vw, 2rem)` |
| Creer que `:is()` y `:where()` pesan igual | `:where()` pesa cero: ideal para estilos base |
| Usar JavaScript para marcar un contenedor según su contenido | `:has()` |
| `outline: none` para quitar el anillo de foco | `:focus-visible` para controlarlo sin quitarlo |
| Anidar CSS en cinco niveles | Máximo dos o tres; clases más concretas |
| `margin-left`/`right` en interfaces que pueden ser RTL | Propiedades lógicas |

## Para practicar

Los ejercicios WA3.1 a WA3.3 de [WA3 · Ejercicios](ejercicios.md) practican variables, `clamp()` y `:has()`.
