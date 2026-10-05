# 1.6 Semántica y accesibilidad

## Elementos semánticos

HTML5 incluye etiquetas que **dicen qué es** cada parte de la página, en lugar de usar `<div>` para todo:

```html
<body>
  <header>   <!-- cabecera: logo y título -->
    <nav>    <!-- menú de navegación -->
      <a href="#">Inicio</a>
    </nav>
  </header>
  <main>     <!-- contenido principal (uno por página) -->
    <section>   <!-- bloque temático -->
      <article> <!-- contenido independiente: una noticia, un comentario -->
      </article>
    </section>
    <aside>     <!-- contenido relacionado: barra lateral -->
    </aside>
  </main>
  <footer>   <!-- pie: contacto, derechos -->
  </footer>
</body>
```

| Etiqueta | Úsala para |
|---|---|
| `header` / `footer` | Cabecera / pie de la página o de una sección |
| `nav` | Un bloque de enlaces de navegación |
| `main` | El contenido principal |
| `section` | Un apartado con su propio título |
| `article` | Algo que tiene sentido por sí solo |
| `aside` | Contenido secundario |
| `div` / `span` | Contenedores **sin significado** (para agrupar y dar estilo) |

!!! tip "Por qué importa"
    Los lectores de pantalla y los buscadores entienden la estructura; además, el código es más legible.

## Accesibilidad básica

* **`alt`** en todas las imágenes con significado (y `alt=""` si son decorativas).
* **`label`** en cada campo de formulario.
* **Jerarquía de títulos** ordenada (`h1` → `h2` → `h3`), sin saltos.
* **Texto de enlace descriptivo**: "Descargar el temario (PDF)" mejor que "pincha aquí".
* **`lang`** en la etiqueta `<html>`.
* **Contraste** suficiente entre el texto y el fondo, y no transmitir información solo con el color.
* Que se pueda usar **con el teclado** (tabulador).

## Atributos globales

| Atributo | Para qué |
|---|---|
| `id` | Identificador **único** en la página (para enlaces, CSS y JS) |
| `class` | Etiqueta reutilizable (varios elementos pueden compartirla) |
| `title` | Texto de ayuda al pasar el ratón |
| `style` | Estilo en línea (mejor evitarlo: usa CSS) |
| `hidden` | Oculta el elemento |

## Validar tu HTML

Comprueba que está bien escrito con el [validador del W3C](https://validator.w3.org/) y mira la consola del navegador (**F12**) si algo no se ve como esperas.
