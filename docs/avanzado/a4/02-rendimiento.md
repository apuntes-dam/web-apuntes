# WA4.2 Rendimiento: cargar rápido y medir

!!! info "Cómo se han comprobado los ejemplos"
    Los ejemplos con **editor en vivo** se ejecutan en tu navegador y se han comprobado en un navegador real. El JavaScript sin DOM (cálculo de contraste, `debounce`, `throttle`) se ha **ejecutado con Node** al construir la web y su salida es la real. Las cifras de rendimiento (por ejemplo, los umbrales de Core Web Vitals) las fija Google y pueden revisarse: consulta [web.dev](https://web.dev/vitals/) para los valores actuales.

## Qué mide el usuario: Core Web Vitals

Google resume la experiencia de carga en tres métricas, llamadas **Core Web Vitals**. Los valores «buenos» actuales (al revisarse con el tiempo, mira [web.dev](https://web.dev/vitals/)):

| Métrica | Qué mide | Bueno |
|---|---|---|
| **LCP** (*Largest Contentful Paint*) | Cuánto tarda en verse **lo más grande** de la pantalla (una imagen, un titular) | ≤ 2,5 s |
| **INP** (*Interaction to Next Paint*) | Cuánto tarda la página en **responder** a un clic o una pulsación | ≤ 200 ms |
| **CLS** (*Cumulative Layout Shift*) | Cuánto **se mueve** el contenido de forma inesperada mientras carga | ≤ 0,1 |

Se miden con **Lighthouse** (en las herramientas del navegador, pestaña *Lighthouse*), **PageSpeed Insights** (una web de Google) o la pestaña *Rendimiento* (*Performance*). Y **siempre en un móvil medio y con red lenta**, que es donde se ve el problema: un ordenador potente con fibra lo disimula todo.

## Cómo carga el navegador un script

Un `<script>` normal **detiene la lectura del HTML** hasta que se descarga y se ejecuta. Se controla con dos atributos:

| Forma | Descarga | Se ejecuta | Orden | Para qué |
|---|---|---|---|---|
| `<script src>` | **Bloquea** el HTML | Al llegar | Garantizado | Casi nunca (solo si el HTML depende de él) |
| `<script defer src>` | En paralelo | **Cuando el HTML ya está leído** | **Respeta el orden** | **Lo habitual** para el código de la página |
| `<script async src>` | En paralelo | **En cuanto llega**, sin esperar | **No** garantizado | Código **independiente** (estadísticas, anuncios) |
| `<script type="module">` | En paralelo | Como `defer` | Respeta el orden | Módulos ES |

La recomendación por defecto: **`defer` en el `<head>`** (se descarga cuanto antes sin bloquear nada) o los módulos.

Otras pistas que se dan al navegador en el `<head>` para ganar tiempo:

| Pista | Para qué |
|---|---|
| `<link rel="preconnect" href="https://fonts.gstatic.com">` | Abrir ya la conexión con un servidor que **seguro** vas a usar |
| `<link rel="preload" as="image" href="portada.webp">` | Pedir **ya** un recurso importante que el navegador descubriría tarde |
| `<link rel="stylesheet">` | Las hojas de estilo **bloquean el dibujado** (deben estar, pero que sean pequeñas) |

## Imágenes: lo que más pesa

Las imágenes suelen ser **más de la mitad** del peso de una página. Con unas pocas decisiones, se reduce mucho:

<div class="demo" data-alto="9rem" data-code="&lt;picture&gt;&#10;  &lt;!-- el navegador elige la primera fuente que entiende --&gt;&#10;  &lt;source type=&quot;image/avif&quot; srcset=&quot;portada.avif&quot;&gt;&#10;  &lt;source type=&quot;image/webp&quot; srcset=&quot;portada.webp&quot;&gt;&#10;  &lt;img src=&quot;portada.jpg&quot;&#10;       srcset=&quot;portada-480.jpg 480w, portada-960.jpg 960w, portada-1600.jpg 1600w&quot;&#10;       sizes=&quot;(min-width: 60rem) 50vw, 100vw&quot;&#10;       width=&quot;1600&quot; height=&quot;900&quot;&#10;       alt=&quot;Una montaña al amanecer&quot;&#10;       loading=&quot;lazy&quot; decoding=&quot;async&quot;&gt;&#10;&lt;/picture&gt;&#10;&lt;p style=&quot;font:13px sans-serif&quot;&gt;(Las imágenes de este ejemplo no existen: mira la estructura. El navegador mostrará el texto alternativo.)&lt;/p&gt;"></div>

Qué hace cada pieza:

| Pieza | Efecto |
|---|---|
| **`srcset` + `sizes`** | El navegador elige la versión **más pequeña que se vea bien** según el ancho de la pantalla y la densidad (retina). `480w` son los píxeles reales de ese archivo; `sizes` dice cuánto ocupará la imagen |
| **`<picture>` + `<source type>`** | Ofrece **formatos modernos** (AVIF y WebP pesan mucho menos que JPG con la misma calidad) y `.jpg` como alternativa |
| **`width` y `height`** | El navegador **reserva el hueco** antes de que la imagen llegue: evita que el contenido «salte» (arregla el **CLS**) |
| **`loading="lazy"`** | No descarga la imagen **hasta que se acerca a la pantalla** |
| **`decoding="async"`** | Decodificarla sin bloquear el resto |
| **`fetchpriority="high"`** | Para la imagen principal (**la de LCP**): descargar antes que el resto |

!!! warning "No pongas `loading=lazy` a la imagen principal"
    La imagen grande que se ve nada más abrir la página (la de **LCP**) debe cargarse **cuanto antes**. Pon `lazy` a las que están **por debajo** del primer pantallazo, no a la de arriba.

## Trabajo del JavaScript: no más de lo necesario

El hilo principal solo puede hacer **una cosa a la vez** (lo viste en la [WA1](../a1/02-asincrono.md)): si un script tarda 300 ms, la página no responde durante 300 ms. Eventos como `scroll`, `resize` o `input` se disparan **decenas de veces por segundo**, y ejecutar un cálculo en cada uno es receta para el desastre. Dos técnicas lo controlan:

| Técnica | Qué hace | Ejemplo típico |
|---|---|---|
| **`debounce`** | Espera a que **pase un tiempo sin nuevas llamadas** y entonces ejecuta **una vez** | Buscar al terminar de escribir |
| **`throttle`** | Ejecuta **como mucho una vez cada X ms** | Reaccionar al scroll |

```javascript
// debounce: «espera a que dejen de llamarme»
function debounce(fn, ms) {
  let id;
  return (...args) => { clearTimeout(id); id = setTimeout(() => fn(...args), ms); };
}

// throttle: «como mucho una vez cada ms». El reloj se inyecta para poder probarlo sin esperar
function throttle(fn, ms, ahora = Date.now) {
  let ultima = -Infinity;
  return (...args) => {
    const t = ahora();
    if (t - ultima >= ms) { ultima = t; return fn(...args); }
  };
}

// --- debounce: 5 llamadas muy seguidas, una pausa y otra llamada ---
const llamadasD = [];
const buscar = debounce(texto => llamadasD.push(texto), 60);
for (const t of ["k", "ko", "kot", "kotl", "kotli"]) { buscar(t); await new Promise(r => setTimeout(r, 10)); }
await new Promise(r => setTimeout(r, 150));
buscar("kotlin"); await new Promise(r => setTimeout(r, 150));
console.log("debounce ejecutó con:", llamadasD);

// --- throttle con un reloj de mentira ---
let reloj = 0;
const ejecuciones = [];
const alScroll = throttle(pos => ejecuciones.push(pos), 100, () => reloj);
for (const [t, pos] of [[0, "a"], [30, "b"], [60, "c"], [100, "d"], [130, "e"], [250, "f"], [260, "g"]]) {
  reloj = t; alScroll(pos);
}
console.log("throttle (100 ms) dejó pasar:", ejecuciones);
```

**Salida (ejecutado con Node):**

```text
debounce ejecutó con: [ 'kotli', 'kotlin' ]
throttle (100 ms) dejó pasar: [ 'a', 'd', 'f' ]
```

En el `debounce`, las cinco pulsaciones seguidas dan **una sola** ejecución con la **última** («kotli»), y la llamada posterior otra. En el `throttle`, de siete llamadas pasan **solo las que están separadas al menos 100 ms**: la primera, la de t=100, y la de t=250. (Esta versión de `throttle` ejecuta la primera de cada ventana y descarta el resto; hay variantes que también ejecutan la última.)

!!! tip "Para el scroll y las animaciones, `requestAnimationFrame`"
    Cuando se trata de **dibujar** (mover algo al hacer scroll), lo ideal no es un tiempo fijo sino **sincronizarse con el repintado** del navegador: `requestAnimationFrame(() => ...)` ejecuta tu código justo antes de cada fotograma (normalmente 60 veces por segundo). Y para saber si algo es visible, `IntersectionObserver` (visto en la [WA2](../a2/02-apis.md)) es mejor que escuchar el scroll.

### No leer y escribir el diseño a la vez

Pedirle al navegador una medida (`offsetHeight`, `getBoundingClientRect()`) **le obliga a calcular el diseño en ese mismo instante**. Si alternas **leer y escribir** en un bucle, lo recalcula **en cada vuelta**. La solución es **agrupar**: primero todas las lecturas, luego todas las escrituras.

```javascript
// Sin ejecutar: ejemplo ilustrativo del patrón en el navegador
// MAL: lee y escribe alternando; fuerza un recálculo del diseño en cada vuelta
for (const caja of cajas) {
  caja.style.height = caja.offsetHeight * 2 + "px";
}

// BIEN: todas las lecturas primero y todas las escrituras después
const alturas = cajas.map(c => c.offsetHeight);
cajas.forEach((c, i) => { c.style.height = alturas[i] * 2 + "px"; });
```

## Caché, compresión y archivos pequeños

La carga más rápida es la que **no se hace**. En el servidor (y en GitHub Pages ya viene resuelto en gran parte):

| Técnica | Efecto |
|---|---|
| **Caché del navegador** (`Cache-Control`) | Los archivos que no cambian **no se vuelven a descargar** |
| **Nombres con huella** (`estilos.a1b2c3.css`) | Cambia el nombre cuando cambia el contenido: se puede cachear **para siempre** sin servir versiones viejas |
| **Compresión** (gzip, Brotli) | El texto (HTML, CSS, JS) viaja **mucho más pequeño** |
| **Minificar** | Quitar espacios y comentarios de CSS y JS |
| **Menos peticiones y menos JavaScript de terceros** | Cada script externo es un riesgo de rendimiento y de privacidad |

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| `<script src>` sin `defer` en el `<head>` | `defer` (o `type="module"`) |
| Imágenes sin `width` y `height` | Darlas siempre: evita el salto de diseño (CLS) |
| Subir fotos de 5 MB para mostrarlas a 300 px | `srcset`, tamaños adecuados y WebP/AVIF |
| `loading="lazy"` en la imagen principal | `fetchpriority="high"` para esa |
| Cálculo pesado en cada evento `scroll` o `input` | `debounce`, `throttle` o `IntersectionObserver` |
| Alternar lecturas y escrituras del DOM en un bucle | Agrupar: leer todo, luego escribir todo |
| Optimizar «a ojo» | Medir con Lighthouse antes y después |
| Probar solo en tu ordenador con fibra | Simular un móvil y red lenta en las herramientas del navegador |

## Para practicar

Los ejercicios WA4.3, WA4.4 y WA4.6 de [WA4 · Ejercicios](ejercicios.md) practican `debounce`, `throttle` y una imagen responsiva.
