# Tema 15. Programación funcional en Swift

**Tarea 1: "Thinking in pipelines"**

## 1. Introducción

El paradigma funcional es la base del desarrollo moderno en iOS (SwiftUI, Combine). El objetivo es dejar de pensar en "cómo recorrer los datos" (bucles) y empezar a pensar en "qué hacer con los datos" (transformaciones).

Esta práctica consta de **30 ejercicios** que van desde la sintaxis básica de los closures hasta la construcción de pipelines complejos de procesamiento de datos.

## 2. Objetivos

* Dominar la sintaxis de **Closures** (trailing, argumentos anónimos).
* Interiorizar el uso de **Map**, **Filter** y **Reduce**.
* Manejar datos opcionales y arrays anidados con **CompactMap** y **FlatMap**.
* Agrupar y transformar datos complejos.
* Entender la **Evaluación perezosa (Lazy)**.
* **Refactorizar** código imperativo a declarativo.

## 3. Instrucciones de entrega

1. Crea un archivo `.swift` único.
2. Separa cada ejercicio con `// MARK: - EJERCICIO X`.
3. **IMPORTANTE:** En los ejercicios complejos, añade un breve comentario explicando qué hace la operación.

---

### BLOQUE 1: Calentamiento con closures

**Ejercicio 1. Tu primer closure explícito**
Declara una variable llamada `saludar` que sea un closure.

* Tipo: `(String) -> Void`.
* Comportamiento: Imprime "Hola, [nombre]".
* Ejecútalo con tu nombre.

**Ejercicio 2. Tipos inferidos**
Declara una variable `sumar` que acepte dos enteros y devuelva su suma. No especifiques los tipos de los parámetros ni el retorno en la definición del closure, deja que Swift los infiera.

**Ejercicio 3. Trailing Closure**
Crea una función `operar(a: Int, b: Int, accion: (Int, Int) -> Int) -> Int`.
Llama a esta función para multiplicar 5 y 3, utilizando la sintaxis de **trailing closure** (el closure fuera de los paréntesis).

**Ejercicio 4. Argumentos anónimos ($0)**
Tienes el array `["Pepe", "Juan", "Ana"]`.
Usa el método `.sorted()` para ordenarlos alfabéticamente. Luego, intenta ordenarlos por longitud con `.sorted(by: )`
**Requisito:** No uses nombres de parámetros en el closure, usa `$0`y`$1`. Todo en una sola línea.

**Ejercicio 5. Captura de valores**
Crea una función `crearIncrementador(cantidad: Int) -> () -> Int`.
Debe devolver un closure que, cada vez que se llame, incremente una variable interna (capturada) en la `cantidad` indicada y la devuelva.
Prueba creando un incrementador de 10 en 10 y llámalo 3 veces.

---

### BLOQUE 2: Transformaciones con map

**Ejercicio 6. Cuadrados**
Dado `[2, 4, 6, 8]`, obtén un nuevo array con los números elevados al cuadrado usando `map`.

**Ejercicio 7. Longitud de cadenas**
Dado `["Swift", "Java", "Kotlin", "Python"]`, obtén un array de enteros que contenga la longitud de cada cadena.

**Ejercicio 8. Conversión de tipos**
Dado `["10", "20", "30"]` (Strings), conviértelos a `Int` usando `map`. (Asume que siempre son válidos, no uses opcionales aquí, usa `Int(...)!`).

**Ejercicio 9. Formateo de precios**
Dado un array de `Double` `[12.5, 9.99, 100.0]`, usa `map` para devolver un array de Strings con el formato "12.50 €", etc.

**Ejercicio 10. Extracción de propiedades**
Dado el struct:

```swift
struct Libro { var titulo: String; var paginas: Int }
let biblioteca = [Libro(titulo: "1984", paginas: 300), Libro(titulo: "Hobbit", paginas: 250)]

```

Usa `map` para obtener un array solo con los **títulos** de los libros.

---

### BLOQUE 3: Filtrado con filter

**Ejercicio 11. Números pares**
Dado un array del 1 al 10, obtén solo los números pares.

**Ejercicio 12. Búsqueda de texto**
Dado `["Mesa", "Silla", "Armario", "Sofá"]`, filtra aquellos que contengan la letra "a" (o "A"). Sugerencia: usa `.lowercased()`.

**Ejercicio 13. Objetos complejos**
Usando el struct `Libro` del ejercicio 10, filtra los libros que tengan más de 280 páginas.

**Ejercicio 14. Filtrado de opcionales**
Dado `let valores: [Int?] = [10, nil, 20, nil, 30]`, primero filtra los que no sean `nil` (aunque el tipo seguirá siendo `Int?`). *Nota: Veremos cómo desempaquetarlos más adelante, aquí solo queremos quitar los niles.*

---

### BLOQUE 4: Reducción con reduce

**Ejercicio 15. Suma básica**
Calcula la suma de `[10, 20, 30, 40, 50]` usando `reduce` empezando en 0.

**Ejercicio 16. Multiplicación (Producto)**
Calcula el producto de `[2, 3, 4]` (resultado 24). Usa la sintaxis simplificada del operador (`*`) dentro del reduce.

**Ejercicio 17. Concatenación**
Dado `["Hola", " ", "Mundo", "!"]`, redúcelo a un solo String completo.

**Ejercicio 18. Búsqueda del máximo**
Usa `reduce` para encontrar el número más grande en `[12, 5, 29, 8, 15]`.
*Pista: El acumulador debe ir guardando el mayor valor visto hasta el momento.*

---

### BLOQUE 5: Aplanamiento y limpieza (FlatMap & CompactMap)

**Ejercicio 19. Aplanar arrays (Flatten)**
Dado `[[1, 2], [3, 4], [5, 6]]`, obtén un array plano `[1, 2, 3, 4, 5, 6]` usando `flatMap`.

**Ejercicio 20. CompactMap básico**
Dado `["10", "hola", "20", "adios", "30"]`, usa `compactMap` para obtener un `[Int]` limpio, descartando los que no son números.

**Ejercicio 21. Transformación + filtrado de nulos**
Tienes `let urls: [String?] = ["img1.jpg", nil, "img2.jpg", nil]`. Usa `compactMap` para obtener solo las URLs válidas y además agrégales el prefijo `https://servidor.com/` a cada una.

**Ejercicio 22. combinando map y flatmap**
Dado:

```swift
struct Usuario { var nombre: String; var emails: [String] }
let usuarios = [
    Usuario(nombre: "Ana", emails: ["ana@gmail.com", "ana@work.com"]),
    Usuario(nombre: "Luis", emails: ["luis@hotmail.com"])
]

```

Obtén un array único con **todos** los emails de todos los usuarios (un `[String]` plano) usando `flatMap`.

---

### BLOQUE 6: Ordenación y agrupación

**Ejercicio 23. Ordenación compleja**
Dado el struct `Usuario(nombre: String, edad: Int)`, crea un array de 4 usuarios.
Ordénalos por edad de mayor a menor.

**Ejercicio 24. Agrupación (Dictionary grouping)**
Dado `let numeros = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]`.
Agrupalos en un diccionario `[String: [Int]]` donde las claves sean "Pares" e "Impares".

---

### BLOQUE 7: Pipelines (encadenamiento maestro)

**Ejercicio 25. Pipeline de texto**
Dada la frase: `"  Swift es GENIAL   "`.
Crea una cadena de operaciones para:

1. Eliminar espacios en blanco (trim).
2. Convertir a minúsculas.
3. Contar cuántas vocales tiene.
*(Nota: Puedes convertir el string a array de caracteres si lo necesitas).*

**Ejercicio 26. Pipeline de ventas**

```swift
struct Venta { var precio: Double; var fecha: String }
let ventas = [
    Venta(precio: 100, fecha: "01/01"),
    Venta(precio: 200, fecha: "05/01"),
    Venta(precio: 50, fecha: "10/01")
]

```

Calcula el total de dinero ganado de las ventas que superen los 80 euros. (Filter + Map + Reduce).

**Ejercicio 27. Pipeline de strings a números**
Dado `["1,5", "2,3", "Error", "4,0"]` (usa coma decimal).

1. Filtra los errores.
2. Reemplaza la coma por punto.
3. Convierte a Double.
4. Calcula la media.

---

### BLOQUE 8: Evaluación perezosa y refactorización

**Ejercicio 28. Lazy Evaluation**
Crea un array del 1 al 1000.
Aplica `.map { $0 * 2 }` y `.filter { $0 < 10 }`.
Hazlo primero de forma normal y luego con `.lazy`.
Escribe un comentario explicando por qué `.lazy` es más eficiente si solo fuéramos a acceder al primer elemento del resultado.

**Ejercicio 29. Refactorización I**
Transforma este código imperativo a funcional (una línea):

```swift
var aprobados: [String] = []
let notas = ["Ana": 8, "Luis": 4, "Maria": 9]
for (nombre, nota) in notas {
    if nota >= 5 {
        aprobados.append(nombre)
    }
}
aprobados.sort()

```

**Ejercicio 30. Refactorización II**
Tienes una lista de productos en el carrito, algunos repetidos.
`let carrito = ["Manzana", "Pera", "Manzana", "Uva", "Pera", "Manzana"]`
Genera un diccionario que contenga el conteo de cada item: `["Manzana": 3, "Pera": 2, "Uva": 1]`.
Intenta hacerlo usando `reduce` (iniciando el acumulador como un diccionario vacío `[:]`).

---

## 4. Criterios de calificación

La práctica se calificará basándose en :

* **Correctitud:** Todos los ejercicios funcionan y arrojan el resultado esperado.
* **Estilo funcional:** Se penalizará el uso de bucles `for` o `while` en ejercicios que piden explícitamente soluciones funcionales. Se valora el uso de sintaxis abreviada (`$0`).
* **Comentarios y claridad:** Código bien identado y comentarios explicativos en los ejercicios 25 a 30, demostrando que se entiende la lógica del pipeline.

---

