# WA5.2 Seguridad y publicación

!!! info "Cómo se han comprobado los ejemplos"
    Los comandos de **npm**, las pruebas con **`node --test`**, el servidor de **CORS** y la lógica de seguridad se han **ejecutado de verdad** al construir la web: lo que ves es su salida. Lo que necesita descargar paquetes (Vite, ESLint, `npm audit`) o servicios externos (GitHub Pages) **no se ha ejecutado aquí** y está marcado «sin ejecutar». Las versiones de las herramientas cambian: comprueba su documentación al usarlas.

## XSS: cuando la página ejecuta código ajeno

**XSS** (*cross-site scripting*) ocurre cuando un atacante consigue que **su código JavaScript se ejecute dentro de tu página**. Desde ahí puede leer lo que el usuario escribe, hacer peticiones en su nombre o robar datos guardados. Casi siempre empieza igual: **tomas un texto que no controlas** (un comentario, un nombre, un parámetro de la URL) y lo **metes en la página como HTML**.

Un ejemplo, con un editor en vivo que se ejecuta en un entorno aislado (inofensivo):

<div class="demo" data-alto="15rem" data-consola="1" data-code="&lt;p&gt;Escribe un nombre (prueba con la sugerencia del botón):&lt;/p&gt;&#10;&lt;input id=&quot;n&quot; value=&quot;Ana&quot; size=&quot;40&quot;&gt;&#10;&lt;button id=&quot;atk&quot;&gt;Poner un nombre «malicioso»&lt;/button&gt;&lt;br&gt;&lt;br&gt;&#10;&lt;button id=&quot;mal&quot;&gt;Saludar con innerHTML (INSEGURO)&lt;/button&gt;&#10;&lt;button id=&quot;bien&quot;&gt;Saludar con textContent (seguro)&lt;/button&gt;&#10;&lt;div id=&quot;salida&quot; style=&quot;margin-top:.5rem;padding:.5rem;background:#f1f5f9&quot;&gt;&lt;/div&gt;&#10;&lt;script&gt;&#10;const n = document.getElementById(&quot;n&quot;), salida = document.getElementById(&quot;salida&quot;);&#10;document.getElementById(&quot;atk&quot;).addEventListener(&quot;click&quot;, () =&gt; {&#10;  n.value = &#x27;&lt;img src=x onerror=&quot;console.log(\&#x27;¡CÓDIGO AJENO EJECUTADO!\&#x27;)&quot;&gt;&#x27;;&#10;});&#10;document.getElementById(&quot;mal&quot;).addEventListener(&quot;click&quot;, () =&gt; { salida.innerHTML = &quot;Hola, &quot; + n.value; });&#10;document.getElementById(&quot;bien&quot;).addEventListener(&quot;click&quot;, () =&gt; { salida.textContent = &quot;Hola, &quot; + n.value; });&#10;&lt;/script&gt;"></div>

Pulsa «Poner un nombre malicioso» y luego los dos saludos. Con **`innerHTML`**, el navegador interpreta `<img src=x onerror=...>` como una imagen que falla al cargar y **ejecuta el código del atributo `onerror`** (verás el mensaje en la consola). Con **`textContent`**, el mismo texto se muestra **tal cual**, como texto inofensivo. Esa es la defensa principal: **tratar siempre lo que viene de fuera como texto, no como HTML**.

Cuando de verdad necesitas construir HTML con datos, hay que **escapar** los caracteres especiales:

```javascript
const escapar = s => String(s).replace(/[&<>"']/g, c =>
  ({ "&": "&amp;", "<": "&lt;", ">": "&gt;", '"': "&quot;", "'": "&#39;" }[c]));

const ataque = `<img src=x onerror="alert('hola')">`;
console.log("sin escapar:", "<p>Hola, " + ataque + "</p>");
console.log("escapado:  ", "<p>Hola, " + escapar(ataque) + "</p>");
console.log(escapar("Tom & Jerry <3 \"cine\""));
```

**Salida (ejecutado con Node):**

```text
sin escapar: <p>Hola, <img src=x onerror="alert('hola')"></p>
escapado:   <p>Hola, &lt;img src=x onerror=&quot;alert(&#39;hola&#39;)&quot;&gt;</p>
Tom &amp; Jerry &lt;3 &quot;cine&quot;
```

Tras escapar, `<` se convierte en `&lt;`: el navegador lo **muestra** como un signo menor, **no lo interpreta** como el comienzo de una etiqueta. (Las librerías de plantillas y los *frameworks* como React o Vue escapan por defecto; el riesgo aparece cuando lo desactivas o usas `innerHTML` a mano.)

### Enlaces: la trampa de `javascript:`

Otro agujero: un enlace cuyo destino viene de un usuario puede empezar por `javascript:`, que **ejecuta código al hacer clic**. Hay que **aceptar solo los protocolos esperados** (lista blanca, no lista negra):

```javascript
function enlaceSeguro(texto) {
  try {
    const url = new URL(texto);
    return ["http:", "https:", "mailto:"].includes(url.protocol) ? url.href : null;
  } catch {
    return null;                                              // ni siquiera es una URL
  }
}

for (const t of ["https://ejemplo.com/ruta?a=1", "mailto:ana@ejemplo.com", "javascript:alert(1)", "JaVaScRiPt:alert(1)", "data:text/html,<script>1</script>", "esto no es una url"]) {
  console.log(t.padEnd(40), "→", enlaceSeguro(t));
}
```

**Salida (ejecutado con Node):**

```text
https://ejemplo.com/ruta?a=1             → https://ejemplo.com/ruta?a=1
mailto:ana@ejemplo.com                   → mailto:ana@ejemplo.com
javascript:alert(1)                      → null
JaVaScRiPt:alert(1)                      → null
data:text/html,<script>1</script>        → null
esto no es una url                       → null
```

Fíjate en que `JaVaScRiPt:` (mezclando mayúsculas para saltarse filtros ingenuos) también se rechaza: `URL` normaliza el protocolo. **Una lista de lo permitido es siempre más segura que una lista de lo prohibido.**

## Política de seguridad de contenido (CSP)

La **CSP** es una cabecera HTTP (`Content-Security-Policy`) con la que **tú le dices al navegador desde dónde se puede cargar código**. Es la **segunda línea de defensa** contra XSS: aunque alguien consiga colar un `<script>`, el navegador **se niega a ejecutarlo** si no cumple la política.

```text
Sin ejecutar: una cabecera de ejemplo (se configura en el servidor)
Content-Security-Policy: default-src 'self'; script-src 'self'; img-src 'self' data: https:; object-src 'none'; base-uri 'self'
```

| Directiva | Significado |
|---|---|
| `default-src 'self'` | Por defecto, **solo** recursos del propio sitio |
| `script-src 'self'` | Solo scripts de tu dominio: **bloquea los scripts en línea** (`<script>…</script>` y `onerror="..."`) |
| `img-src 'self' data: https:` | Imágenes del sitio, `data:` y cualquier `https:` |
| `object-src 'none'` | Sin `<object>` ni `<embed>` |

En GitHub Pages no puedes poner cabeceras, pero sí una etiqueta `<meta http-equiv="Content-Security-Policy" content="...">` (con algunas limitaciones). Una CSP estricta **obliga a sacar el JavaScript a archivos `.js`**, que es una buena práctica de todos modos.

## CORS: por qué tu `fetch` falla desde otra web

El navegador aplica la **política del mismo origen**: una página de `https://mi-web.es` **no puede leer** la respuesta de una petición a `https://otra-api.com`, salvo que **ese servidor lo autorice**. Un *origen* es **protocolo + dominio + puerto**. La autorización se da con **cabeceras de CORS** (*Cross-Origin Resource Sharing*) en la respuesta del servidor. Para ver las cabeceras de verdad, aquí un servidor real con `node:http` y dos peticiones desde orígenes distintos:

```javascript
import http from "node:http";

const permitidos = new Set(["https://mi-web.es"]);

const servidor = http.createServer((req, res) => {
  const origen = req.headers.origin;
  if (permitidos.has(origen)) {                         // solo a las webs de la lista
    res.setHeader("Access-Control-Allow-Origin", origen);
    res.setHeader("Vary", "Origin");
  }
  if (req.method === "OPTIONS") {                       // la «consulta previa» (preflight)
    res.setHeader("Access-Control-Allow-Methods", "GET, POST");
    res.setHeader("Access-Control-Allow-Headers", "Content-Type");
    res.statusCode = 204;
    return res.end();
  }
  res.setHeader("Content-Type", "application/json");
  res.end(JSON.stringify({ ok: true }));
});

await new Promise(r => servidor.listen(0, r));
const url = `http://localhost:${servidor.address().port}/datos`;

for (const origin of ["https://mi-web.es", "https://otra-web.com"]) {
  const r = await fetch(url, { headers: { Origin: origin } });
  console.log(origin.padEnd(20), "->", r.status, "| Access-Control-Allow-Origin:", r.headers.get("access-control-allow-origin"));
}

const previa = await fetch(url, { method: "OPTIONS", headers: { Origin: "https://mi-web.es", "Access-Control-Request-Method": "POST" } });
console.log("consulta previa:", previa.status, "| métodos permitidos:", previa.headers.get("access-control-allow-methods"));
servidor.close();
```

**Salida (ejecutado con Node):**

```text
https://mi-web.es    -> 200 | Access-Control-Allow-Origin: https://mi-web.es
https://otra-web.com -> 200 | Access-Control-Allow-Origin: null
consulta previa: 204 | métodos permitidos: GET, POST
```

Qué enseña la salida:

* El servidor **responde a las dos** peticiones (el estado es 200 en ambas): **el servidor no bloquea nada**.
* Solo para el origen de la lista añade `Access-Control-Allow-Origin`. Para el otro, **la cabecera no existe** (el `null` de la salida es lo que devuelve JavaScript al pedir una cabecera ausente).
* **Es el navegador quien decide**: sin esa cabecera, **no entrega la respuesta al JavaScript** de la página y muestra el error de CORS en la consola. (Node no aplica esta política, por eso aquí se ve la respuesta; el bloqueo ocurre solo en navegadores.)
* Para peticiones «no simples» (un `POST` con JSON, cabeceras personalizadas) el navegador envía antes una **consulta previa** (`OPTIONS`) para preguntar qué se permite.

!!! danger "El error de CORS se arregla en el servidor, nunca «desactivándolo»"
    Si tu `fetch` falla por CORS, **la solución es configurar el servidor de la API** para que envíe las cabeceras. Las extensiones del navegador que «desactivan CORS» solo funcionan en tu máquina y **enseñan una mala solución**. Poner `Access-Control-Allow-Origin: *` está bien para datos **públicos**, pero **jamás** para una API que use cookies o datos privados.

## Cookies, almacenamiento y secretos

| Dónde | Lo lee JavaScript | Se envía en cada petición | Para qué |
|---|---|---|---|
| `localStorage` | **Sí** (cualquier script) | No | Preferencias; **nada sensible** |
| `sessionStorage` | Sí | No | Datos de una pestaña |
| **Cookie** normal | Sí | Sí | Poco recomendable para sesiones |
| **Cookie `HttpOnly`** | **No** | Sí | **Sesiones y credenciales**: un XSS no puede robarla |

Las cookies de sesión deben llevar tres atributos:

| Atributo | Efecto |
|---|---|
| `HttpOnly` | JavaScript **no puede leerla** |
| `Secure` | Solo viaja por **HTTPS** |
| `SameSite=Lax` (o `Strict`) | No se envía en peticiones desde **otras webs** (frena los ataques CSRF) |

!!! danger "Todo lo que va en el JavaScript del navegador es público"
    **Cualquier persona puede leer** el código de tu web y las claves que lleve dentro. **Nunca pongas en el cliente** contraseñas, claves de API privadas ni *tokens* secretos: aunque las «escondas» en una variable, se ven en las herramientas del navegador. Lo secreto vive **en el servidor**. Y **no los subas a Git**: un secreto publicado en un repositorio hay que darlo por perdido y **cambiarlo**.

## Dependencias: código ajeno en tu proyecto

Cada paquete de npm es código que **ejecutas con los permisos de tu proyecto**, y con **sus** dependencias son cientos. Tres hábitos:

* Instalar **solo lo necesario** y paquetes **mantenidos y conocidos** (mira descargas, fecha de la última versión, repositorio).
* `package-lock.json` en el repositorio y `npm ci` en el servidor.
* **`npm audit`** (sin ejecutar: consulta una base de datos en internet) avisa de **vulnerabilidades conocidas** en tus dependencias; revísalo de vez en cuando.

## HTTPS

Todo el tráfico debe ir por **HTTPS**: cifra la conexión (nadie en medio puede leer o modificar la página) y hoy es **requisito** para muchas funciones del navegador (geolocalización, cámara, *service workers*...). GitHub Pages, Netlify y Vercel lo dan **gratis y automático**. Lo que debes evitar es el **contenido mixto**: una página HTTPS que carga un script o una imagen por `http://`.

## Publicar en GitHub Pages (sin ejecutar)

Es la forma más sencilla de publicar una web estática gratis:

1. Sube tu proyecto a un repositorio de GitHub.
2. En **Settings → Pages**, elige de dónde sale la web: una **rama** (y carpeta) o **GitHub Actions**.
3. Tras unos minutos estará en `https://usuario.github.io/repositorio/`.

Dos tropiezos clásicos de la **ruta base**: una web de proyecto vive en **`/repositorio/`**, no en `/`. Un enlace como `<a href="/estilos.css">` (con barra inicial) apunta a la **raíz del dominio** y **falla**: usa **rutas relativas** (`estilos.css`, `./img/foto.png`) o configura la base de tu empaquetador (en Vite, `base: "/repositorio/"`). Y para tener una página de error propia, un archivo **`404.html`** en la raíz.

Si usas un empaquetador (Vite), lo habitual es construir y publicar con **GitHub Actions**:

```yaml
# Sin ejecutar: .github/workflows/pages.yml (los números de versión de las acciones cambian: mira su documentación)
name: Publicar
on:
  push:
    branches: [main]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  construir:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 22
      - run: npm ci
      - run: npm test
      - run: npm run build
      - uses: actions/upload-pages-artifact@v3
        with:
          path: dist

  desplegar:
    needs: construir
    runs-on: ubuntu-latest
    environment:
      name: github-pages
    steps:
      - uses: actions/deploy-pages@v4
```

Fíjate en el orden: **`npm test` antes de `npm run build`**. Si una prueba falla, **no se publica nada**. Es la integración continua que se estudió en la [A5 de Git](https://apuntes-dam.github.io/git-apuntes/avanzado/a5/), aplicada a una web.

## Lista de comprobación antes de publicar

| ¿Hecho? | Comprobación |
|---|---|
| ☐ | Nada que venga de un usuario entra como HTML (`textContent` o escapar) |
| ☐ | Los enlaces con destino variable aceptan solo `http`, `https` o `mailto` |
| ☐ | Ninguna clave privada, contraseña o *token* en el código ni en el repositorio |
| ☐ | Todo por **HTTPS**, sin contenido mixto |
| ☐ | Rutas **relativas** (o `base` configurada): probada en la URL real de publicación |
| ☐ | `npm test` pasa y `npm audit` revisado |
| ☐ | `node_modules/` en `.gitignore` y `package-lock.json` subido |
| ☐ | Probada en el móvil, con el teclado y con Lighthouse (rendimiento y accesibilidad de la [WA4](../a4/index.md)) |

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| `innerHTML` con texto de un usuario | `textContent`, o escapar |
| Un enlace cuyo destino viene del usuario, sin validar | Lista blanca de protocolos |
| «Arreglar» CORS con una extensión o con `*` en todas partes | Configurar el servidor de la API con los orígenes concretos |
| Guardar el *token* de sesión en `localStorage` | Cookie `HttpOnly; Secure; SameSite` |
| Una clave de API privada en el JavaScript del navegador | Ponerla en un servidor y que el navegador hable con él |
| Rutas con `/` inicial en una web de proyecto de GitHub Pages | Rutas relativas |
| Subir un secreto «solo un momento» | Darlo por filtrado y cambiarlo |
| Publicar sin ejecutar las pruebas | `npm test` antes del `build` en el flujo de publicación |

## Para practicar

Los ejercicios WA5.1, WA5.2 y WA5.5 de [WA5 · Ejercicios](ejercicios.md) practican el escapado, la validación de enlaces y la cabecera de CORS; el WA5.6 pide el flujo de publicación.
