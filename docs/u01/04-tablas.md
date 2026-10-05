# 1.4 Tablas

Las tablas sirven para mostrar **datos tabulares** (horarios, precios, resultados), no para colocar la maquetación de la página.

<div class="demo" data-alto="17rem" data-code="&lt;table border=&quot;1&quot;&gt;&#10;  &lt;caption&gt;Horario&lt;/caption&gt;&#10;  &lt;thead&gt;&#10;    &lt;tr&gt;&lt;th&gt;Hora&lt;/th&gt;&lt;th&gt;Lunes&lt;/th&gt;&lt;th&gt;Martes&lt;/th&gt;&lt;/tr&gt;&#10;  &lt;/thead&gt;&#10;  &lt;tbody&gt;&#10;    &lt;tr&gt;&lt;td&gt;9:00&lt;/td&gt;&lt;td&gt;Programación&lt;/td&gt;&lt;td&gt;Bases de datos&lt;/td&gt;&lt;/tr&gt;&#10;    &lt;tr&gt;&lt;td&gt;10:00&lt;/td&gt;&lt;td colspan=&quot;2&quot;&gt;Tutoría (ocupa dos columnas)&lt;/td&gt;&lt;/tr&gt;&#10;    &lt;tr&gt;&lt;td&gt;11:00&lt;/td&gt;&lt;td rowspan=&quot;2&quot;&gt;Taller&lt;/td&gt;&lt;td&gt;Inglés&lt;/td&gt;&lt;/tr&gt;&#10;    &lt;tr&gt;&lt;td&gt;12:00&lt;/td&gt;&lt;td&gt;Sistemas&lt;/td&gt;&lt;/tr&gt;&#10;  &lt;/tbody&gt;&#10;  &lt;tfoot&gt;&#10;    &lt;tr&gt;&lt;td colspan=&quot;3&quot;&gt;Curso 2026/27&lt;/td&gt;&lt;/tr&gt;&#10;  &lt;/tfoot&gt;&#10;&lt;/table&gt;"></div>

| Etiqueta | Para qué sirve |
|---|---|
| `<table>` | La tabla entera |
| `<caption>` | Título de la tabla |
| `<thead>`, `<tbody>`, `<tfoot>` | Cabecera, cuerpo y pie |
| `<tr>` | Una fila |
| `<th>` | Celda de **cabecera** (negrita y centrada por defecto) |
| `<td>` | Celda de datos |
| `colspan="n"` / `rowspan="n"` | Celda que ocupa `n` columnas / filas |

!!! tip "El borde"
    El atributo `border="1"` es solo para ver las líneas mientras aprendes. El aspecto real de la tabla se hace con CSS.
