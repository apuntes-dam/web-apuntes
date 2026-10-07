# WA4.1 Animaciones y accesibilidad

!!! info "Cómo se han comprobado los ejemplos"
    Los ejemplos con **editor en vivo** se ejecutan en tu navegador y se han comprobado en un navegador real. El JavaScript sin DOM (cálculo de contraste, `debounce`, `throttle`) se ha **ejecutado con Node** al construir la web y su salida es la real. Las cifras de rendimiento (por ejemplo, los umbrales de Core Web Vitals) las fija Google y pueden revisarse: consulta [web.dev](https://web.dev/vitals/) para los valores actuales.

## Transiciones: cambiar suavemente entre dos estados

Una **transición** hace que, cuando una propiedad cambia de valor (por ejemplo al pasar el ratón), el cambio **no sea instantáneo** sino gradual.

<div class="demo" data-alto="6rem" data-code="&lt;button class=&quot;b&quot;&gt;Pasa el ratón o pulsa Tab&lt;/button&gt;&#10;&lt;style&gt;&#10;  .b { padding: .6rem 1rem; border: 0; border-radius: 8px; background: #2563eb; color: white; cursor: pointer;&#10;       transition: transform .25s ease, background-color .25s ease, box-shadow .25s ease; }&#10;  .b:hover, .b:focus-visible { transform: translateY(-3px); background: #1d4ed8; box-shadow: 0 6px 12px rgba(0,0,0,.25); }&#10;  .b:active { transform: translateY(0); }&#10;&lt;/style&gt;"></div>

La propiedad `transition` lleva: **qué propiedades** animar, **cuánto dura** y la **curva** (`ease`, `linear`, `ease-in-out`, `cubic-bezier(...)`). Se escribe en el estado **de partida** (el normal), de modo que anima tanto «hacia» el estado `:hover` como «de vuelta».

!!! tip "Anima `transform` y `opacity`, y casi nada más"
    El navegador puede animar `transform` (mover, escalar, girar) y `opacity` **directamente en la tarjeta gráfica**, sin recalcular la página. Animar `width`, `height`, `top`, `left` o `margin` **obliga a recalcular el diseño en cada fotograma** y se ve a tirones, sobre todo en móviles. Para mover algo, `transform: translateX(...)` en lugar de `left`.

## Animaciones con `@keyframes`

Para algo más que un cambio entre dos estados (repetirse, tener pasos intermedios, empezar solo), se definen **fotogramas clave** y se aplican con `animation`:

<div class="demo" data-alto="9rem" data-code="&lt;div class=&quot;latido&quot;&gt;♥&lt;/div&gt;&#10;&lt;div class=&quot;entrada&quot;&gt;Aparezco con un efecto al cargar la página&lt;/div&gt;&#10;&lt;style&gt;&#10;  @keyframes latir {&#10;    0%, 100% { transform: scale(1); }&#10;    50%      { transform: scale(1.3); }&#10;  }&#10;  @keyframes aparecer {&#10;    from { opacity: 0; transform: translateY(12px); }&#10;    to   { opacity: 1; transform: none; }&#10;  }&#10;  .latido  { display: inline-block; font-size: 2rem; color: crimson; animation: latir 1s ease-in-out infinite; }&#10;  .entrada { margin-top: 1rem; padding: .75rem; background: #dcfce7; border-radius: 8px;&#10;             animation: aparecer .6s ease-out both; }&#10;&lt;/style&gt;"></div>

`animation: latir 1s ease-in-out infinite` significa: nombre, duración, curva y **cuántas veces** (`infinite`: sin parar). `both` hace que se **respeten el primer y el último fotograma** fuera de la animación (aquí: que no se vea el elemento antes de empezar).

## Respetar a quien no quiere movimiento

Para algunas personas (con trastornos vestibulares, migrañas, epilepsia o simplemente cansancio), el **movimiento en pantalla produce mareo o malestar**. Los sistemas operativos tienen un ajuste «reducir movimiento», y CSS lo lee con **`prefers-reduced-motion`**:

<div class="demo" data-alto="9rem" data-code="&lt;div class=&quot;rueda&quot;&gt;⚙&lt;/div&gt;&#10;&lt;p id=&quot;estado&quot; style=&quot;font:14px monospace&quot;&gt;&lt;/p&gt;&#10;&lt;style&gt;&#10;  @keyframes girar { to { transform: rotate(360deg); } }&#10;  .rueda { display: inline-block; font-size: 3rem; animation: girar 2s linear infinite; }&#10;&#10;  @media (prefers-reduced-motion: reduce) {          /* si el usuario pidió menos movimiento */&#10;    .rueda { animation: none; }&#10;  }&#10;&lt;/style&gt;&#10;&lt;script&gt;&#10;const pide = matchMedia(&quot;(prefers-reduced-motion: reduce)&quot;).matches;&#10;document.getElementById(&quot;estado&quot;).textContent = pide&#10;  ? &quot;Tu sistema pide reducir movimiento: la rueda NO gira.&quot;&#10;  : &quot;Tu sistema no pide reducir movimiento: la rueda gira. (Actívalo en el sistema y recarga)&quot;;&#10;&lt;/script&gt;"></div>

Una buena costumbre es **desactivar o simplificar** las animaciones dentro de ese bloque (quitar las que se repiten sin parar, los desplazamientos grandes y los parpadeos). No hace falta eliminar todo: un cambio de color es inofensivo; lo que marea es el **movimiento grande y repetido**.

!!! warning "Nada que parpadee más de tres veces por segundo"
    Un contenido que parpadea con frecuencia puede provocar **crisis epilépticas fotosensibles**. Es una de las pocas normas de accesibilidad que no admite excepciones.

## Teclado: ¿se puede usar sin ratón?

Muchas personas usan la web **solo con el teclado** (por una discapacidad motora, por lector de pantalla, o porque les resulta más rápido). Comprobar una página es fácil: **deja el ratón a un lado y usa Tab, Shift+Tab, Intro, Espacio y Escape**.

| Qué comprobar | Cómo se consigue |
|---|---|
| Todo lo interactivo **recibe el foco** | Usar los elementos nativos: `<button>`, `<a href>`, `<input>`, `<select>` |
| El foco **se ve** | Nunca `outline: none` a secas; usa `:focus-visible` para controlarlo |
| El orden del foco es **lógico** | Escribir el HTML en el orden en que se lee; no depender del CSS para reordenar |
| Puedes **saltar** la cabecera | Un enlace «Saltar al contenido» al principio de la página |
| Un diálogo **atrapa** el foco | El elemento `<dialog>` lo hace solo (ver más abajo) |

### `tabindex`

| Valor | Efecto |
|---|---|
| `tabindex="0"` | El elemento **entra** en el orden natural del teclado (para widgets propios) |
| `tabindex="-1"` | **No** se alcanza con Tab, pero se puede enfocar **por código** (`el.focus()`) |
| `tabindex="1"` o mayor | **Nunca lo uses**: altera el orden y lo vuelve imposible de mantener |

La regla de oro: **un `<div onclick>` no es un botón.** No recibe el foco, no responde a Intro ni Espacio, y un lector de pantalla no lo anuncia como botón. Usa `<button>`.

### Un enlace para saltar la navegación

<div class="demo" data-alto="9rem" data-code="&lt;a class=&quot;saltar&quot; href=&quot;#principal&quot;&gt;Saltar al contenido&lt;/a&gt;&#10;&lt;nav&gt;Menú: &lt;a href=&quot;#&quot;&gt;Inicio&lt;/a&gt; &lt;a href=&quot;#&quot;&gt;Cursos&lt;/a&gt; &lt;a href=&quot;#&quot;&gt;Contacto&lt;/a&gt; &lt;a href=&quot;#&quot;&gt;…&lt;/a&gt;&lt;/nav&gt;&#10;&lt;main id=&quot;principal&quot; tabindex=&quot;-1&quot;&gt;&lt;h2&gt;Contenido principal&lt;/h2&gt;&lt;p&gt;Pulsa Tab nada más cargar para ver el enlace.&lt;/p&gt;&lt;/main&gt;&#10;&lt;style&gt;&#10;  .saltar { position: absolute; inset-inline-start: .5rem; top: -3rem; background: #111; color: #fff; padding: .5rem .75rem; border-radius: 6px; }&#10;  .saltar:focus { top: .5rem; }                           /* aparece solo cuando recibe el foco */&#10;&lt;/style&gt;"></div>

El enlace está **fuera de la pantalla** (no con `display: none`, que lo haría inalcanzable) y **aparece al recibir el foco**. Quien usa el teclado evita tener que pasar por todo el menú en cada página.

### `<dialog>`: la ventana modal que ya sabe hacer todo

Hacer una ventana modal accesible a mano (atrapar el foco, cerrar con Escape, devolver el foco al cerrar, bloquear el fondo) es difícil. El elemento **`<dialog>`** lo resuelve cuando se abre con `showModal()`:

<div class="demo" data-alto="12rem" data-code="&lt;button id=&quot;abrir&quot;&gt;Abrir diálogo&lt;/button&gt;&#10;&lt;dialog id=&quot;dlg&quot;&gt;&#10;  &lt;form method=&quot;dialog&quot;&gt;&#10;    &lt;h3&gt;¿Seguro?&lt;/h3&gt;&#10;    &lt;p&gt;Esta acción no se puede deshacer.&lt;/p&gt;&#10;    &lt;button value=&quot;cancelar&quot;&gt;Cancelar&lt;/button&gt;&#10;    &lt;button value=&quot;aceptar&quot; autofocus&gt;Aceptar&lt;/button&gt;&#10;  &lt;/form&gt;&#10;&lt;/dialog&gt;&#10;&lt;p id=&quot;resultado&quot; style=&quot;font:14px monospace&quot;&gt;&lt;/p&gt;&#10;&lt;style&gt;dialog::backdrop { background: rgba(0,0,0,.5); } dialog { border: 0; border-radius: 12px; }&lt;/style&gt;&#10;&lt;script&gt;&#10;const dlg = document.getElementById(&quot;dlg&quot;);&#10;document.getElementById(&quot;abrir&quot;).addEventListener(&quot;click&quot;, () =&gt; { dlg.returnValue = &quot;&quot;; dlg.showModal(); });   // returnValue se conserva entre aperturas: se borra al abrir&#10;dlg.addEventListener(&quot;close&quot;, () =&gt; {&#10;  document.getElementById(&quot;resultado&quot;).textContent = &quot;cerrado con: &quot; + (dlg.returnValue || &quot;Escape&quot;);&#10;});&#10;&lt;/script&gt;"></div>

Con `showModal()` el navegador, **sin código tuyo**: pone el diálogo **encima de todo** con un fondo oscurecido (`::backdrop`), **mueve el foco dentro** (al elemento con `autofocus`), **impide interactuar con el resto de la página**, lo **cierra con Escape** y **devuelve el foco** al botón que lo abrió. Un `<form method="dialog">` cierra el diálogo al enviar y deja en `returnValue` el `value` del botón pulsado.

## ARIA: describir lo que HTML no puede

**ARIA** son atributos (`role`, `aria-*`) que dan información extra a los lectores de pantalla. Tiene una regla de oro que lo resume todo:

!!! danger "La primera regla de ARIA: si puedes usar un elemento nativo, no uses ARIA"
    Un `<button>` ya tiene rol, foco y teclado. Un `<div role="button">` **solo cambia lo que se anuncia**: tendrías que programar tú el foco, Intro, Espacio... Y **ARIA mal puesto es peor que no ponerlo**: promete algo que no ocurre.

Los usos más habituales, cuando **sí** hacen falta:

| Atributo | Para qué |
|---|---|
| `aria-label="Cerrar"` | Un nombre accesible cuando el elemento **no tiene texto visible** (un botón con solo un icono) |
| `aria-labelledby="id"` | Que el nombre sea el **texto de otro elemento** |
| `aria-expanded="true/false"` | Un botón que **despliega** algo: ¿está desplegado? |
| `aria-controls="id"` | Qué elemento controla el botón |
| `aria-live="polite"` | Una zona cuyos cambios **se anuncian** solos (avisos de «guardado», errores) |
| `aria-hidden="true"` | **Oculta** un elemento decorativo a los lectores de pantalla |
| `aria-current="page"` | El enlace de la página en la que estás |

<div class="demo" data-alto="9rem" data-code="&lt;button id=&quot;b&quot; aria-expanded=&quot;false&quot; aria-controls=&quot;panel&quot;&gt;Más información&lt;/button&gt;&#10;&lt;div id=&quot;panel&quot; hidden&gt;Este contenido se muestra y se oculta, y el botón lo anuncia.&lt;/div&gt;&#10;&#10;&lt;button id=&quot;g&quot;&gt;Guardar&lt;/button&gt;&#10;&lt;p id=&quot;aviso&quot; role=&quot;status&quot; aria-live=&quot;polite&quot; style=&quot;min-height:1.5em&quot;&gt;&lt;/p&gt;&#10;&lt;script&gt;&#10;const b = document.getElementById(&quot;b&quot;), panel = document.getElementById(&quot;panel&quot;);&#10;b.addEventListener(&quot;click&quot;, () =&gt; {&#10;  const abierto = b.getAttribute(&quot;aria-expanded&quot;) === &quot;true&quot;;&#10;  b.setAttribute(&quot;aria-expanded&quot;, String(!abierto));       // el estado que oye el lector de pantalla...&#10;  panel.hidden = abierto;                                   // ...y el estado visible, siempre sincronizados&#10;});&#10;document.getElementById(&quot;g&quot;).addEventListener(&quot;click&quot;, () =&gt; {&#10;  document.getElementById(&quot;aviso&quot;).textContent = &quot;✔ Cambios guardados&quot;;   // se anuncia sin mover el foco&#10;});&#10;&lt;/script&gt;"></div>

Fíjate en que **el estado visible y el estado ARIA deben cambiar siempre juntos**. Y en que la zona `aria-live` debe **existir ya** (vacía) en la página: si se crea a la vez que su texto, algunos lectores no anuncian nada.

## Contraste de color

Un texto gris claro sobre fondo blanco es ilegible para mucha gente (baja visión, pantallas con reflejos, personas mayores). Las pautas de accesibilidad **WCAG** fijan un **mínimo de contraste** entre el color del texto y el del fondo:

| Nivel | Texto normal | Texto grande (≥ 24 px, o ≥ 18,7 px en negrita) |
|---|---|---|
| **AA** (el mínimo que se suele exigir) | **4,5 : 1** | **3 : 1** |
| **AAA** (mejorado) | 7 : 1 | 4,5 : 1 |

El contraste se **calcula**: primero la **luminancia relativa** de cada color, y después la proporción entre ambas. Es una fórmula pura, así que se ejecuta y se comprueba:

```javascript
// Luminancia relativa de un color #rrggbb (fórmula de WCAG)
function luminancia(hex) {
  const [r, g, b] = [1, 3, 5].map(i => parseInt(hex.slice(i, i + 2), 16) / 255)
    .map(c => c <= 0.03928 ? c / 12.92 : ((c + 0.055) / 1.055) ** 2.4);   // quita la «curva» de la pantalla
  return 0.2126 * r + 0.7152 * g + 0.0722 * b;                            // el ojo es más sensible al verde
}

function contraste(a, b) {
  const [claro, oscuro] = [luminancia(a), luminancia(b)].sort((x, y) => y - x);
  return (claro + 0.05) / (oscuro + 0.05);
}

const pares = [["#000000", "#ffffff"], ["#777777", "#ffffff"], ["#767676", "#ffffff"], ["#ffffff", "#2563eb"], ["#ffffff", "#60a5fa"]];
for (const [texto, fondo] of pares) {
  const c = contraste(texto, fondo);
  console.log(texto, "sobre", fondo, "→", c.toFixed(2) + " : 1", c >= 4.5 ? "AA ✔" : c >= 3 ? "solo texto grande" : "NO cumple");
}
```

**Salida (ejecutado con Node):**

```text
#000000 sobre #ffffff → 21.00 : 1 AA ✔
#777777 sobre #ffffff → 4.48 : 1 solo texto grande
#767676 sobre #ffffff → 4.54 : 1 AA ✔
#ffffff sobre #2563eb → 5.17 : 1 AA ✔
#ffffff sobre #60a5fa → 2.54 : 1 NO cumple
```

Un detalle famoso: **`#777777` sobre blanco da 4,48 : 1 y NO cumple** el mínimo para texto normal; basta oscurecerlo un poco (`#767676`) para llegar a 4,54. En las herramientas del navegador (inspector de color) y en extensiones como *Lighthouse* o *WAVE* se mide sin escribir nada.

!!! tip "El color no debe ser lo único que informa"
    Un campo con error **solo en rojo** no lo ve una persona con daltonismo. Añade también un **icono, un texto o un subrayado** («Error: el correo no es válido»).

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| `<div onclick>` en lugar de un botón | `<button>`: foco, Intro y Espacio vienen incluidos |
| `outline: none` sin sustituto | `:focus-visible` con un estilo propio y visible |
| `tabindex` positivo | Solo `0` o `-1` |
| Animaciones constantes sin alternativa | `@media (prefers-reduced-motion: reduce)` |
| Animar `width`, `top` o `margin` | `transform` y `opacity` |
| Una ventana modal hecha a mano | `<dialog>` con `showModal()` |
| ARIA que contradice o repite lo nativo | Primero HTML nativo; ARIA solo lo que falta |
| Texto gris claro «porque queda elegante» | Calcular el contraste (4,5 : 1 como mínimo) |
| Información solo por color | Añadir icono o texto |

## Para practicar

Los ejercicios WA4.1 y WA4.2 de [WA4 · Ejercicios](ejercicios.md) calculan el contraste; el WA4.5 construye un menú desplegable accesible.
