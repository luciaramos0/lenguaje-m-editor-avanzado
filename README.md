# Lenguaje M en el Editor Avanzado: limpieza de datos sin usar la interfaz gráfica

Script en lenguaje M que limpia un archivo de ventas exportado de un sistema legacy.
> Nota: el paso `Origen` es un placeholder y no corre sin modificarlo.

### ¿Qué hace exactamente el bloque let...in en lenguaje M? ¿Por qué cada paso puede referenciar al anterior?
let ... in es la estructura básica de una consulta M. Entre let e in se definen variables (los pasos), y después de in se indica cuál de ellas es el resultado final de la consulta.
Cada paso es una variable con nombre; como son variables, cualquier paso puede usar el nombre de otro para tomarlo como entrada. Así se arma la cadena.

---

### ¿Por qué M es Case Sensitive y qué consecuencia práctica tiene? Dá un ejemplo de un error que esto puede causar.
M distingue mayúsculas de minúsculas en nombres de funciones, nombres de pasos, nombres de columnas y comparaciones de texto. Es una decisión de diseño del lenguaje: Prueba y prueba son cadenas de caracteres distintas porque sus códigos de carácter son distintos, y M las compara tal cual, sin normalizar.
Consecuencia práctica: un error de una sola letra en mayúscula rompe la consulta o, peor, produce resultados incorrectos sin avisar.

Ejemplos de errores:
- Error de nombre de función: escribir text.trim en vez de Text.Trim da el mensaje “El nombre ‘text.trim’ no se reconoció”.
- Error de nombre de columna: escribir {"Categoria", Text.Proper, type text} cuando la columna se llama categoria da error de columna inexistente.
- Error silencioso en filtros: each [categoria] <> "Prueba" no elimina las filas que dicen "PRUEBA" o "prueba", porque para M son valores distintos. La consulta corre sin error, pero quedan registros de prueba en los datos.

---

### ¿Cuál es la diferencia entre usar Text.Trim y Text.Clean en M?
| Función	| Qué elimina |
|-----------|----------------------|
| Text.Trim |	Espacios en blanco al inicio y al final del texto. No toca los del medio. |
| Text.Clean | Caracteres de control no imprimibles (saltos de línea, tabuladores, retornos de carro) en cualquier posición del texto. |

Ejemplos:
```
Text.Trim("  Mouse Logitech  ")   // "Mouse Logitech"
Text.Trim("Mouse   Logitech")     // "Mouse   Logitech" (no cambia los del medio)
Text.Clean("Mouse#(lf)Logitech")  // "MouseLogitech" (quita el salto de línea)
```
Se usan para problemas distintos: Text.Trim para espacios sobrantes en los bordes, y Text.Clean para caracteres invisibles que suelen venir de exportaciones de sistemas legacy.

---

### ¿Por qué filtraste los registros "PRUEBA" después de estandarizar la categoría y no antes?
Porque la comparación [categoria] <> "Prueba" distingue mayúsculas y minúsculas. Si el filtro se aplicara antes, solo eliminaría las filas escritas exactamente como "Prueba", y las variantes "PRUEBA" o "prueba" pasarían el filtro y quedarían en los datos.
Al aplicar primero Text.Proper, todas las variantes se convierten en "Prueba", y recién entonces el filtro puede atraparlas a todas con una sola condición.
