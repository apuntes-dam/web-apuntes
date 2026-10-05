# 3.3 Arrays y objetos

## Arrays

<div class="demo" data-alto="15rem" data-consola="1" data-code="const notas = [6, 9, 4, 7];&#10;notas.push(10);                   // añade al final&#10;console.log(notas.length, notas[0]);&#10;&#10;const aprobadas = notas.filter(n =&gt; n &gt;= 5);&#10;const dobles = notas.map(n =&gt; n * 2);&#10;const suma = notas.reduce((total, n) =&gt; total + n, 0);&#10;&#10;console.log(aprobadas);&#10;console.log(dobles);&#10;console.log(&quot;Media:&quot;, suma / notas.length);&#10;console.log(notas.includes(4), notas.indexOf(9));"></div>

| Método | Qué hace |
|---|---|
| `push` / `pop` | Añade / quita al final |
| `shift` / `unshift` | Quita / añade al principio |
| `filter(f)` | Nuevo array con los que cumplen `f` |
| `map(f)` | Nuevo array transformando cada elemento |
| `reduce(f, ini)` | Reduce el array a un solo valor |
| `forEach(f)` | Ejecuta `f` con cada elemento |
| `includes(x)` / `indexOf(x)` | Busca un elemento |
| `sort()` · `reverse()` | Ordena · invierte (modifican el array) |

## Objetos

Un objeto agrupa datos relacionados en pares **clave: valor**:

<div class="demo" data-alto="16rem" data-consola="1" data-code="const alumno = {&#10;  nombre: &quot;Lucía&quot;,&#10;  notas: [8, 6, 9],&#10;  activo: true,&#10;  promedio() {                       // un método&#10;    return this.notas.reduce((t, n) =&gt; t + n, 0) / this.notas.length;&#10;  }&#10;};&#10;&#10;console.log(alumno.nombre, alumno[&quot;activo&quot;]);&#10;alumno.curso = &quot;2º DAM&quot;;             // añadir una propiedad&#10;console.log(alumno.promedio().toFixed(2));&#10;console.log(Object.keys(alumno));&#10;console.log(JSON.stringify({ a: 1, b: [1, 2] }));"></div>

* Se accede con `objeto.propiedad` o `objeto["propiedad"]`.
* `JSON.stringify(obj)` convierte un objeto a texto JSON y `JSON.parse(texto)` hace lo contrario.
* Se pueden **desestructurar**: `const { nombre, curso } = alumno;`
