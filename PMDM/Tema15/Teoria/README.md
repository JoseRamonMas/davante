# TEMA 15: Estructuras de datos modernas, programación funcional y persistencia


## Tabla de contenidos

1. [Introducción: el cambio de paradigma](#1-introducci%C3%B3n-el-cambio-de-paradigma)
    - 1.1. [Programación imperativa vs declarativa](#11-programaci%C3%B3n-imperativa-vs-declarativa)
    - 1.2. [Inmutabilidad y estado](#12-inmutabilidad-y-estado)
2. [Funciones como ciudadanos de primera clase](#2-funciones-como-ciudadanos-de-primera-clase)
    - 2.1. [Lambdas y closures: Swift vs Kotlin](#21-lambdas-y-closures-swift-vs-kotlin)
    - 2.2. [Captura de valores](#22-captura-de-valores)
    - 2.3. [Trailing closures en Swift](#23-trailing-closures-en-swift)
    - 2.4. [Funciones como parámetros](#24-funciones-como-par%C3%A1metros)
    - 2.5. [Funciones que devuelven funciones](#25-funciones-que-devuelven-funciones)
3. [Operaciones funcionales sobre colecciones](#3-operaciones-funcionales-sobre-colecciones)
    - 3.1. [Map: transformación](#31-map-transformaci%C3%B3n)
    - 3.2. [Filter: filtrado](#32-filter-filtrado)
    - 3.3. [Reduce: acumulación](#33-reduce-acumulaci%C3%B3n)
    - 3.4. [FlatMap y compactMap: aplanamiento](#34-flatmap-y-compactmap-aplanamiento)
    - 3.5. [Sorted y reversed: ordenación](#35-sorted-y-reversed-ordenaci%C3%B3n)
    - 3.6. [GroupBy y partition: agrupación](#36-groupby-y-partition-agrupaci%C3%B3n)
    - 3.7. [Otras operaciones útiles](#37-otras-operaciones-%C3%BAtiles)
    - 3.8. [Encadenamiento de operaciones](#38-encadenamiento-de-operaciones)
4. [Evaluación perezosa (lazy evaluation)](#4-evaluaci%C3%B3n-perezosa-lazy-evaluation)
    - 4.1. [Sequences en Kotlin](#41-sequences-en-kotlin)
    - 4.2. [Lazy collections en Swift](#42-lazy-collections-en-swift)
    - 4.3. [Operaciones intermedias vs terminales](#43-operaciones-intermedias-vs-terminales)
5. [Scope functions de Kotlin](#5-scope-functions-de-kotlin)
    - 5.1. [let](#51-let)
    - 5.2. [run](#52-run)
    - 5.3. [with](#53-with)
    - 5.4. [apply](#54-apply)
    - 5.5. [also](#55-also)
    - 5.6. [Comparativa y cuándo usar cada una](#56-comparativa-y-cu%C3%A1ndo-usar-cada-una)
6. [Persistencia declarativa moderna](#6-persistencia-declarativa-moderna)
    - 6.1. [Android: Room con Flow](#61-android-room-con-flow)
    - 6.2. [iOS: SwiftData y Core Data](#62-ios-swiftdata-y-core-data)
7. [Arquitecturas reactivas: introducción](#7-arquitecturas-reactivas-introducci%C3%B3n)
    - 7.1. [MVVM (Model-View-ViewModel)](#71-mvvm-model-view-viewmodel)
    - 7.2. [Repository Pattern](#72-repository-pattern)
    - 7.3. [Single Source of Truth](#73-single-source-of-truth)
8. [Proyecto integrador: gestor de tareas](#8-proyecto-integrador-gestor-de-tareas)
9. [Ejercicios de refactorización](#9-ejercicios-de-refactorizaci%C3%B3n)

***

## 1. Introducción: el cambio de paradigma

### 1.1. Programación imperativa vs declarativa

Hasta ahora habéis programado principalmente de forma **imperativa** en Java: le decís al ordenador **cómo** hacer las cosas paso a paso (crea una variable, abre un bucle, incrementa el contador, comprueba condiciones, guarda el valor).

Swift y Kotlin brillan en la **programación declarativa y funcional**: le decimos al ordenador **qué** queremos conseguir, y delegamos el **cómo** a las funciones del lenguaje.

**Ejemplo comparativo:**

```kotlin
// IMPERATIVO (estilo Java/antiguo)
val numeros = listOf(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
val resultado = mutableListOf<Int>()

for (numero in numeros) {
    if (numero % 2 == 0) {
        val cuadrado = numero * numero
        resultado.add(cuadrado)
    }
}
println(resultado)  // [4, 16, 36, 64, 100]

// DECLARATIVO (estilo funcional)
val resultado = numeros
    .filter { it % 2 == 0 }
    .map { it * it }
println(resultado)  // [4, 16, 36, 64, 100]
```

> [!NOTE]
> El código funcional es más conciso, expresivo y fácil de mantener. Cada operación tiene un propósito claro y el código se lee como una secuencia de transformaciones.

### 1.2. Inmutabilidad y estado

En programación funcional, evitamos cambiar el estado de las variables. En lugar de modificar objetos, creamos **copias modificadas**:

**Kotlin:**

```kotlin
data class Usuario(val nombre: String, val edad: Int)

val usuario1 = Usuario("Ana", 25)
// usuario1.edad = 26  // ❌ ERROR: val cannot be reassigned

// Creamos una copia modificada
val usuario2 = usuario1.copy(edad = 26)
// usuario1 sigue siendo Ana con 25 años
// usuario2 es Ana con 26 años
```

**Swift:**

```swift
struct Usuario {
    let nombre: String
    var edad: Int
}

let usuario1 = Usuario(nombre: "Ana", edad: 25)
// usuario1.edad = 26  // ❌ ERROR: Cannot modify immutable value

var usuario2 = usuario1
usuario2.edad = 26
// usuario1 sigue siendo Ana con 25 (copia por valor)
```

**Ventajas de la inmutabilidad:**

1. **Seguridad en concurrencia:** Múltiples hilos pueden leer sin riesgo de race conditions
2. **Predictibilidad:** Sabes que nadie ha modificado tus datos
3. **Facilita el debugging:** El estado no cambia inesperadamente
4. **Optimizaciones del compilador:** Puede cachear valores inmutables

***

## 2. Funciones como ciudadanos de primera clase

En lenguajes modernos, las funciones son **first-class citizens**: pueden asignarse a variables, pasarse como parámetros y devolverse desde otras funciones.

### 2.1. Lambdas y closures: Swift vs Kotlin

#### Kotlin: Lambdas

```kotlin
// Lambda básica
val saludar = { nombre: String -> "Hola, $nombre" }
println(saludar("Ana"))  // "Hola, Ana"

// Lambda con múltiples parámetros
val sumar = { a: Int, b: Int -> a + b }
println(sumar(5, 3))  // 8

// Lambda sin parámetros
val obtenerMensaje = { "¡Bienvenido!" }
println(obtenerMensaje())  // "¡Bienvenido!"

// Parámetro implícito 'it' (cuando hay un solo parámetro)
val doble = { numero: Int -> numero * 2 }
val dobleImplicito: (Int) -> Int = { it * 2 }

println(doble(5))  // 10
println(dobleImplicito(5))  // 10
```


#### Swift: Closures

```swift
// Closure básico (sintaxis completa)
let saludar = { (nombre: String) -> String in
    return "Hola, \(nombre)"
}
print(saludar("Ana"))  // "Hola, Ana"

// Closure con múltiples parámetros
let sumar = { (a: Int, b: Int) -> Int in
    return a + b
}
print(sumar(5, 3))  // 8

// Closure sin parámetros
let obtenerMensaje = { () -> String in
    return "¡Bienvenido!"
}
print(obtenerMensaje())  // "¡Bienvenido!"

// Sintaxis abreviada (inferencia de tipos)
let doble: (Int) -> Int = { numero in
    numero * 2
}

// Sintaxis ultra-abreviada con $0, $1...
let dobleCorto: (Int) -> Int = { $0 * 2 }
print(dobleCorto(5))  // 10
```


### 2.2. Captura de valores

Los closures/lambdas pueden **capturar** variables del contexto donde fueron definidos:

**Kotlin:**

```kotlin
fun crearContador(): () -> Int {
    var contador = 0
    return { ++contador }  // Captura 'contador'
}

val contador1 = crearContador()
println(contador1())  // 1
println(contador1())  // 2
println(contador1())  // 3

val contador2 = crearContador()
println(contador2())  // 1 (nuevo contador independiente)
```

**Swift:**

```swift
func crearContador() -> () -> Int {
    var contador = 0
    return {
        contador += 1
        return contador
    }
}

let contador1 = crearContador()
print(contador1())  // 1
print(contador1())  // 2
print(contador1())  // 3

let contador2 = crearContador()
print(contador2())  // 1
```


### 2.3. Trailing closures en Swift

Cuando el último parámetro de una función es un closure, Swift permite escribirlo fuera de los paréntesis:

```swift
// Función que acepta un closure
func procesar(numeros: [Int], transformacion: (Int) -> Int) -> [Int] {
    return numeros.map(transformacion)
}

// Llamada tradicional
let resultado1 = procesar(numeros: [1, 2, 3], transformacion: { $0 * 2 })

// Con trailing closure (más legible)
let resultado2 = procesar(numeros: [1, 2, 3]) { $0 * 2 }

// Si el closure es el único parámetro, puedes omitir los paréntesis
let numeros = [1, 2, 3, 4, 5]
let pares = numeros.filter { $0 % 2 == 0 }
```


### 2.4. Funciones que aceptan funciones

**Kotlin:**

```kotlin
fun calcular(a: Int, b: Int, operacion: (Int, Int) -> Int): Int {
    return operacion(a, b)
}

// Uso con diferentes operaciones
val suma = calcular(5, 3) { x, y -> x + y }
val resta = calcular(5, 3) { x, y -> x - y }
val multiplicacion = calcular(5, 3) { x, y -> x * y }

println(suma)  // 8
println(resta)  // 2
println(multiplicacion)  // 15

// Ejemplo más práctico: repetir acción n veces
fun repetir(veces: Int, accion: () -> Unit) {
    repeat(veces) {
        accion()
    }
}

repetir(3) {
    println("Hola")
}
// Hola
// Hola
// Hola
```

**Swift:**

```swift
func calcular(a: Int, b: Int, operacion: (Int, Int) -> Int) -> Int {
    return operacion(a, b)
}

// Uso
let suma = calcular(a: 5, b: 3) { $0 + $1 }
let resta = calcular(a: 5, b: 3) { $0 - $1 }
let multiplicacion = calcular(a: 5, b: 3) { $0 * $1 }

print(suma)  // 8
print(resta)  // 2
print(multiplicacion)  // 15

// Ejemplo: validar con condición personalizada
func validar(_ valor: Int, condicion: (Int) -> Bool) -> Bool {
    return condicion(valor)
}

print(validar(10) { $0 > 5 })  // true
print(validar(10) { $0 % 2 == 0 })  // true
```


### 2.5. Funciones que devuelven funciones

**Kotlin:**

```kotlin
fun crearMultiplicador(factor: Int): (Int) -> Int {
    return { numero -> numero * factor }
}

val duplicar = crearMultiplicador(2)
val triplicar = crearMultiplicador(3)

println(duplicar(5))  // 10
println(triplicar(5))  // 15

// Ejemplo más complejo: crear validadores
fun crearValidadorRango(min: Int, max: Int): (Int) -> Boolean {
    return { valor -> valor in min..max }
}

val validarEdad = crearValidadorRango(18, 65)
println(validarEdad(25))  // true
println(validarEdad(70))  // false
```

**Swift:**

```swift
func crearMultiplicador(factor: Int) -> (Int) -> Int {
    return { numero in numero * factor }
}

let duplicar = crearMultiplicador(factor: 2)
let triplicar = crearMultiplicador(factor: 3)

print(duplicar(5))  // 10
print(triplicar(5))  // 15

// Composición de funciones
func componer<A, B, C>(
    _ f: @escaping (B) -> C,
    _ g: @escaping (A) -> B
) -> (A) -> C {
    return { a in f(g(a)) }
}

let añadirUno: (Int) -> Int = { $0 + 1 }
let multiplicarPorDos: (Int) -> Int = { $0 * 2 }

let funcion = componer(multiplicarPorDos, añadirUno)
print(funcion(5))  // 12 (primero suma 1, luego multiplica por 2)
```


***

## 3. Operaciones funcionales sobre colecciones

Aquí es donde la programación funcional muestra su verdadero poder. Estas operaciones eliminan la necesidad de bucles `for` explícitos y hacen el código más expresivo.

### 3.1. Map: transformación

Transforma cada elemento de una colección aplicando una función:

**Kotlin:**

```kotlin
val numeros = listOf(1, 2, 3, 4, 5)

// Duplicar cada número
val dobles = numeros.map { it * 2 }
println(dobles)  // [2, 4, 6, 8, 10]

// Aplicar IVA a precios
val precios = listOf(10.0, 20.0, 30.0)
val preciosConIva = precios.map { it * 1.21 }
println(preciosConIva)  // [12.1, 24.2, 36.3]

// Transformar objetos
data class Persona(val nombre: String, val edad: Int)
val personas = listOf(
    Persona("Ana", 25),
    Persona("Luis", 30),
    Persona("María", 28)
)

val nombres = personas.map { it.nombre }
println(nombres)  // [Ana, Luis, María]

val edadesEnMeses = personas.map { it.edad * 12 }
println(edadesEnMeses)  // [300, 360, 336]

// mapIndexed: transformar con índice
val numerados = nombres.mapIndexed { index, nombre -> 
    "${index + 1}. $nombre" 
}
println(numerados)  // [1. Ana, 2. Luis, 3. María]

// mapNotNull: transformar ignorando nulos
val textos = listOf("1", "abc", "3", "xyz", "5")
val numerosValidos = textos.mapNotNull { it.toIntOrNull() }
println(numerosValidos)  // [1, 3, 5]
```

**Swift:**

```swift
let numeros = [1, 2, 3, 4, 5]

// Duplicar cada número
let dobles = numeros.map { $0 * 2 }
print(dobles)  // [2, 4, 6, 8, 10]

// Aplicar IVA
let precios = [10.0, 20.0, 30.0]
let preciosConIva = precios.map { $0 * 1.21 }
print(preciosConIva)  // [12.1, 24.2, 36.3]

// Transformar objetos
struct Persona {
    let nombre: String
    let edad: Int
}

let personas = [
    Persona(nombre: "Ana", edad: 25),
    Persona(nombre: "Luis", edad: 30),
    Persona(nombre: "María", edad: 28)
]

let nombres = personas.map { $0.nombre }
print(nombres)  // ["Ana", "Luis", "María"]

// compactMap: transformar ignorando nulos
let textos = ["1", "abc", "3", "xyz", "5"]
let numerosValidos = textos.compactMap { Int($0) }
print(numerosValidos)  // [1, 3, 5]
```


### 3.2. Filter: filtrado

Selecciona elementos que cumplen una condición:

**Kotlin:**

```kotlin
val numeros = listOf(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)

// Números pares
val pares = numeros.filter { it % 2 == 0 }
println(pares)  // [2, 4, 6, 8, 10]

// Números mayores que 5
val mayores = numeros.filter { it > 5 }
println(mayores)  // [6, 7, 8, 9, 10]

// filterNot: lo contrario de filter
val impares = numeros.filterNot { it % 2 == 0 }
println(impares)  // [1, 3, 5, 7, 9]

// Filtrar objetos
data class Producto(val nombre: String, val precio: Double, val disponible: Boolean)

val productos = listOf(
    Producto("Laptop", 999.0, true),
    Producto("Mouse", 29.0, false),
    Producto("Teclado", 79.0, true)
)

val disponibles = productos.filter { it.disponible }
println(disponibles)  // [Laptop, Teclado]

val caros = productos.filter { it.precio > 50.0 }
println(caros)  // [Laptop, Teclado]

// filterIsInstance: filtrar por tipo
val mixto: List<Any> = listOf(1, "dos", 3, "cuatro", 5)
val soloNumeros = mixto.filterIsInstance<Int>()
println(soloNumeros)  // [1, 3, 5]
```

**Swift:**

```swift
let numeros = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

// Números pares
let pares = numeros.filter { $0 % 2 == 0 }
print(pares)  // [2, 4, 6, 8, 10]

// Números mayores que 5
let mayores = numeros.filter { $0 > 5 }
print(mayores)  // [6, 7, 8, 9, 10]

// Filtrar objetos
struct Producto {
    let nombre: String
    let precio: Double
    let disponible: Bool
}

let productos = [
    Producto(nombre: "Laptop", precio: 999.0, disponible: true),
    Producto(nombre: "Mouse", precio: 29.0, disponible: false),
    Producto(nombre: "Teclado", precio: 79.0, disponible: true)
]

let disponibles = productos.filter { $0.disponible }
print(disponibles)  // [Laptop, Teclado]

let caros = productos.filter { $0.precio > 50.0 }
print(caros)  // [Laptop, Teclado]

// first(where:) encuentra el primer elemento que cumple la condición
if let primerDisponible = productos.first(where: { $0.disponible }) {
    print(primerDisponible.nombre)  // "Laptop"
}
```


### 3.3. Reduce: acumulación

Colapsa una colección en un único valor:

**Kotlin:**

```kotlin
val numeros = listOf(1, 2, 3, 4, 5)

// Suma total
val suma = numeros.reduce { acumulador, numero -> acumulador + numero }
println(suma)  // 15

// Versión simplificada con operador
val suma2 = numeros.reduce { acc, n -> acc + n }

// fold: como reduce pero con valor inicial
val sumaConInicio = numeros.fold(100) { acc, n -> acc + n }
println(sumaConInicio)  // 115 (100 + 15)

// Producto de todos
val producto = numeros.reduce { acc, n -> acc * n }
println(producto)  // 120

// Concatenar strings
val palabras = listOf("Hola", "mundo", "desde", "Kotlin")
val frase = palabras.reduce { acc, palabra -> "$acc $palabra" }
println(frase)  // "Hola mundo desde Kotlin"

// fold con tipo diferente
val numeros2 = listOf(1, 2, 3, 4, 5)
val texto = numeros2.fold("Números: ") { acc, n -> "$acc$n, " }
println(texto)  // "Números: 1, 2, 3, 4, 5, "

// Caso práctico: calcular total de carrito
data class Item(val nombre: String, val precio: Double, val cantidad: Int)
val carrito = listOf(
    Item("Laptop", 999.0, 1),
    Item("Mouse", 29.0, 2),
    Item("Cable", 15.0, 3)
)

val total = carrito.fold(0.0) { acc, item -> 
    acc + (item.precio * item.cantidad)
}
println("Total: €$total")  // Total: €1102.0
```

**Swift:**

```swift
let numeros = [1, 2, 3, 4, 5]

// Suma total
let suma = numeros.reduce(0) { $0 + $1 }
print(suma)  // 15

// Versión simplificada (operador como función)
let suma2 = numeros.reduce(0, +)
print(suma2)  // 15

// Producto
let producto = numeros.reduce(1, *)
print(producto)  // 120

// Concatenar strings
let palabras = ["Hola", "mundo", "desde", "Swift"]
let frase = palabras.reduce("") { $0 + " " + $1 }
print(frase)  // " Hola mundo desde Swift"

// Mejor con joined
let fraseMejor = palabras.joined(separator: " ")
print(fraseMejor)  // "Hola mundo desde Swift"

// Caso práctico
struct Item {
    let nombre: String
    let precio: Double
    let cantidad: Int
}

let carrito = [
    Item(nombre: "Laptop", precio: 999.0, cantidad: 1),
    Item(nombre: "Mouse", precio: 29.0, cantidad: 2),
    Item(nombre: "Cable", precio: 15.0, cantidad: 3)
]

let total = carrito.reduce(0.0) { acumulador, item in
    acumulador + (item.precio * Double(item.cantidad))
}
print("Total: €\(total)")  // Total: €1102.0
```


### 3.4. FlatMap y compactMap: aplanamiento

**Kotlin:**

```kotlin
// flatMap: aplana colecciones anidadas
val cursos = listOf(
    listOf("Ana", "Luis"),
    listOf("María", "Carlos", "Pedro"),
    listOf("Laura")
)

val todosLosAlumnos = cursos.flatMap { it }
println(todosLosAlumnos)  // [Ana, Luis, María, Carlos, Pedro, Laura]

// flatMap con transformación
data class Pedido(val id: Int, val productos: List<String>)
val pedidos = listOf(
    Pedido(1, listOf("Laptop", "Mouse")),
    Pedido(2, listOf("Teclado")),
    Pedido(3, listOf("Monitor", "Cable", "Webcam"))
)

val todosLosProductos = pedidos.flatMap { it.productos }
println(todosLosProductos)  
// [Laptop, Mouse, Teclado, Monitor, Cable, Webcam]

// flatten: aplana sin transformación
val numeros = listOf(
    listOf(1, 2, 3),
    listOf(4, 5),
    listOf(6, 7, 8, 9)
)
val plano = numeros.flatten()
println(plano)  // [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

**Swift:**

```swift
// flatMap: aplana y transforma
let cursos = [
    ["Ana", "Luis"],
    ["María", "Carlos", "Pedro"],
    ["Laura"]
]

let todosLosAlumnos = cursos.flatMap { $0 }
print(todosLosAlumnos)  // ["Ana", "Luis", "María", "Carlos", "Pedro", "Laura"]

// flatMap con transformación
struct Pedido {
    let id: Int
    let productos: [String]
}

let pedidos = [
    Pedido(id: 1, productos: ["Laptop", "Mouse"]),
    Pedido(id: 2, productos: ["Teclado"]),
    Pedido(id: 3, productos: ["Monitor", "Cable", "Webcam"])
]

let todosLosProductos = pedidos.flatMap { $0.productos }
print(todosLosProductos)
// ["Laptop", "Mouse", "Teclado", "Monitor", "Cable", "Webcam"]

// compactMap: elimina nulos
let textos = ["1", "abc", "3", "xyz", "5"]
let numeros = textos.compactMap { Int($0) }
print(numeros)  // [1, 3, 5]
```


### 3.5. Sorted y reversed: ordenación

**Kotlin:**

```kotlin
val numeros = listOf(5, 2, 8, 1, 9, 3)

// sorted: ordenación ascendente
val ordenados = numeros.sorted()
println(ordenados)  // [1, 2, 3, 5, 8, 9]

// sortedDescending: ordenación descendente
val descendente = numeros.sortedDescending()
println(descendente)  // [9, 8, 5, 3, 2, 1]

// sortedBy: ordenar por criterio
data class Persona(val nombre: String, val edad: Int)
val personas = listOf(
    Persona("Ana", 30),
    Persona("Luis", 25),
    Persona("María", 28)
)

val porEdad = personas.sortedBy { it.edad }
println(porEdad)  // [Luis(25), María(28), Ana(30)]

val porNombre = personas.sortedBy { it.nombre }
println(porNombre)  // [Ana, Luis, María]

// reversed: invertir orden
val invertido = numeros.reversed()
println(invertido)  // [3, 9, 1, 8, 2, 5]
```

**Swift:**

```swift
let numeros = [5, 2, 8, 1, 9, 3]

// sorted: ordenación ascendente
let ordenados = numeros.sorted()
print(ordenados)  // [1, 2, 3, 5, 8, 9]

// sorted con closure personalizado
let descendente = numeros.sorted { $0 > $1 }
print(descendente)  // [9, 8, 5, 3, 2, 1]

// sorted(by:) con objetos
struct Persona {
    let nombre: String
    let edad: Int
}

let personas = [
    Persona(nombre: "Ana", edad: 30),
    Persona(nombre: "Luis", edad: 25),
    Persona(nombre: "María", edad: 28)
]

let porEdad = personas.sorted { $0.edad < $1.edad }
print(porEdad)  // [Luis(25), María(28), Ana(30)]

// reversed: invertir
let invertido = numeros.reversed()
print(Array(invertido))  // [3, 9, 1, 8, 2, 5]
```


### 3.6. GroupBy y partition: agrupación

**Kotlin:**

```kotlin
// groupBy: agrupa elementos por criterio
val numeros = listOf(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
val agrupados = numeros.groupBy { if (it % 2 == 0) "pares" else "impares" }
println(agrupados)
// {impares=[1, 3, 5, 7, 9], pares=[2, 4, 6, 8, 10]}

data class Estudiante(val nombre: String, val nota: Double)
val estudiantes = listOf(
    Estudiante("Ana", 8.5),
    Estudiante("Luis", 6.0),
    Estudiante("María", 9.0),
    Estudiante("Carlos", 5.5)
)

val porCalificacion = estudiantes.groupBy {
    when {
        it.nota >= 9.0 -> "Sobresaliente"
        it.nota >= 7.0 -> "Notable"
        it.nota >= 5.0 -> "Aprobado"
        else -> "Suspenso"
    }
}
println(porCalificacion)

// partition: divide en dos grupos (true/false)
val (aprobados, suspensos) = estudiantes.partition { it.nota >= 5.0 }
println("Aprobados: $aprobados")
println("Suspensos: $suspensos")
```

**Swift:**

```swift
// Dictionary(grouping:) agrupa por criterio
let numeros = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
let agrupados = Dictionary(grouping: numeros) { $0 % 2 == 0 ? "pares" : "impares" }
print(agrupados)
// ["impares": [1, 3, 5, 7, 9], "pares": [2, 4, 6, 8, 10]]

struct Estudiante {
    let nombre: String
    let nota: Double
}

let estudiantes = [
    Estudiante(nombre: "Ana", nota: 8.5),
    Estudiante(nombre: "Luis", nota: 6.0),
    Estudiante(nombre: "María", nota: 9.0),
    Estudiante(nombre: "Carlos", nota: 5.5)
]

let porCalificacion = Dictionary(grouping: estudiantes) { estudiante -> String in
    switch estudiante.nota {
    case 9.0...: return "Sobresaliente"
    case 7.0..<9.0: return "Notable"
    case 5.0..<7.0: return "Aprobado"
    default: return "Suspenso"
    }
}
print(porCalificacion)
```


### 3.7. Otras operaciones útiles

**Kotlin:**

```kotlin
val numeros = listOf(1, 2, 3, 4, 5)

// sum, average, min, max
println(numeros.sum())  // 15
println(numeros.average())  // 3.0
println(numeros.minOrNull())  // 1
println(numeros.maxOrNull())  // 5

// count: contar elementos que cumplen condición
val pares = numeros.count { it % 2 == 0 }
println(pares)  // 2

// any: ¿alguno cumple?
println(numeros.any { it > 4 })  // true
println(numeros.any { it > 10 })  // false

// all: ¿todos cumplen?
println(numeros.all { it > 0 })  // true
println(numeros.all { it > 3 })  // false

// none: ¿ninguno cumple?
println(numeros.none { it < 0 })  // true

// take y drop
println(numeros.take(3))  // [1, 2, 3]
println(numeros.drop(3))  // [4, 5]

// distinct: eliminar duplicados
val conDuplicados = listOf(1, 2, 2, 3, 3, 3, 4)
println(conDuplicados.distinct())  // [1, 2, 3, 4]

// zip: combinar dos listas
val nombres = listOf("Ana", "Luis", "María")
val edades = listOf(25, 30, 28)
val personas = nombres.zip(edades)
println(personas)  // [(Ana, 25), (Luis, 30), (María, 28)]
```

**Swift:**

```swift
let numeros = [1, 2, 3, 4, 5]

// Operaciones numéricas
print(numeros.reduce(0, +))  // 15 (suma)
print(numeros.min())  // Optional(1)
print(numeros.max())  // Optional(5)

// contains
print(numeros.contains(3))  // true
print(numeros.contains(10))  // false

// contains(where:)
print(numeros.contains { $0 > 4 })  // true

// allSatisfy: ¿todos cumplen?
print(numeros.allSatisfy { $0 > 0 })  // true
print(numeros.allSatisfy { $0 > 3 })  // false

// prefix y dropFirst
print(numeros.prefix(3))  // [1, 2, 3]
print(numeros.dropFirst(3))  // [4, 5]

// removingDuplicates (requiere extension)
extension Array where Element: Hashable {
    func removingDuplicates() -> [Element] {
        var seen = Set<Element>()
        return filter { seen.insert($0).inserted }
    }
}

let conDuplicados = [1, 2, 2, 3, 3, 3, 4]
print(conDuplicados.removingDuplicates())  // [1, 2, 3, 4]

// zip
let nombres = ["Ana", "Luis", "María"]
let edades = [25, 30, 28]
let personas = zip(nombres, edades).map { ($0, $1) }
print(personas)  // [("Ana", 25), ("Luis", 30), ("María", 28)]
```


### 3.8. Encadenamiento de operaciones

La verdadera potencia viene de **combinar** operaciones:

**Kotlin:**

```kotlin
data class Producto(val nombre: String, val precio: Double, val categoria: String)

val productos = listOf(
    Producto("Laptop", 999.0, "Electrónica"),
    Producto("Mouse", 29.0, "Electrónica"),
    Producto("Silla", 150.0, "Muebles"),
    Producto("Teclado", 79.0, "Electrónica"),
    Producto("Mesa", 300.0, "Muebles"),
    Producto("Monitor", 250.0, "Electrónica")
)

// Encadenamiento complejo
val resultado = productos
    .filter { it.categoria == "Electrónica" }  // Solo electrónica
    .filter { it.precio < 500.0 }              // Menos de 500€
    .sortedBy { it.precio }                    // Ordenar por precio
    .map { it.nombre }                         // Solo nombres
    .take(3)                                   // Primeros 3

println(resultado)  // [Mouse, Teclado, Monitor]

// Ejemplo práctico: estadísticas de ventas
data class Venta(val producto: String, val cantidad: Int, val precioUnitario: Double)

val ventas = listOf(
    Venta("Laptop", 5, 999.0),
    Venta("Mouse", 20, 29.0),
    Venta("Teclado", 15, 79.0)
)

val totalFacturado = ventas
    .map { it.cantidad * it.precioUnitario }
    .sum()

println("Total facturado: €$totalFacturado")  // €6780.0

val top3Productos = ventas
    .sortedByDescending { it.cantidad * it.precioUnitario }
    .take(3)
    .map { "${it.producto}: €${it.cantidad * it.precioUnitario}" }

println(top3Productos)  // [Laptop: €4995.0, Teclado: €1185.0, Mouse: €580.0]
```

**Swift:**

```swift
struct Producto {
    let nombre: String
    let precio: Double
    let categoria: String
}

let productos = [
    Producto(nombre: "Laptop", precio: 999.0, categoria: "Electrónica"),
    Producto(nombre: "Mouse", precio: 29.0, categoria: "Electrónica"),
    Producto(nombre: "Silla", precio: 150.0, categoria: "Muebles"),
    Producto(nombre: "Teclado", precio: 79.0, categoria: "Electrónica"),
    Producto(nombre: "Mesa", precio: 300.0, categoria: "Muebles"),
    Producto(nombre: "Monitor", precio: 250.0, categoria: "Electrónica")
]

// Encadenamiento
let resultado = productos
    .filter { $0.categoria == "Electrónica" }
    .filter { $0.precio < 500.0 }
    .sorted { $0.precio < $1.precio }
    .map { $0.nombre }
    .prefix(3)

print(Array(resultado))  // ["Mouse", "Teclado", "Monitor"]

// Estadísticas
struct Venta {
    let producto: String
    let cantidad: Int
    let precioUnitario: Double
}

let ventas = [
    Venta(producto: "Laptop", cantidad: 5, precioUnitario: 999.0),
    Venta(producto: "Mouse", cantidad: 20, precioUnitario: 29.0),
    Venta(producto: "Teclado", cantidad: 15, precioUnitario: 79.0)
]

let totalFacturado = ventas
    .map { Double($0.cantidad) * $0.precioUnitario }
    .reduce(0, +)

print("Total: €\(totalFacturado)")  // Total: €6780.0
```


***

## 4. Evaluación perezosa (lazy evaluation)

La evaluación perezosa no ejecuta operaciones hasta que realmente se necesita el resultado. Esto es crucial para optimizar el rendimiento con colecciones grandes.

### 4.1. Sequences en Kotlin

```kotlin
// Sin lazy (eager evaluation)
val numerosGrandes = (1..1_000_000).toList()

val resultado1 = numerosGrandes
    .map { it * 2 }        // Procesa 1 millón
    .filter { it > 100 }   // Procesa 1 millón
    .take(10)              // Toma solo 10

// Con lazy (lazy evaluation con Sequence)
val resultado2 = (1..1_000_000).asSequence()
    .map { it * 2 }        // NO se ejecuta todavía
    .filter { it > 100 }   // NO se ejecuta todavía
    .take(10)              // AHORA sí, pero solo procesa lo necesario
    .toList()

println(resultado2)  // [52, 54, 56, 58, 60, 62, 64, 66, 68, 70]

// Ejemplo más claro
fun transformar(x: Int): Int {
    println("Transformando $x")
    return x * 2
}

// Con lista normal (eager)
println("=== EAGER ===")
val lista = listOf(1, 2, 3, 4, 5)
    .map { transformar(it) }
    .take(2)
println(lista)
// Imprime "Transformando" 5 veces (procesa todo)

// Con secuencia (lazy)
println("=== LAZY ===")
val secuencia = listOf(1, 2, 3, 4, 5)
    .asSequence()
    .map { transformar(it) }
    .take(2)
    .toList()
println(secuencia)
// Imprime "Transformando" solo 2 veces (solo lo necesario)
```

> [!IMPORTANT]
> Las secuencias son especialmente útiles cuando:
> - Trabajas con colecciones muy grandes
> - Realizas múltiples operaciones encadenadas
> - Puede que no necesites todos los resultados (take, first, etc.)
> - Las operaciones son costosas computacionalmente

### 4.2. Lazy collections en Swift

```swift
// Con array normal (eager)
let numeros = Array(1...1_000_000)

let resultado1 = numeros
    .map { $0 * 2 }
    .filter { $0 > 100 }
    .prefix(10)

// Con lazy
let resultado2 = (1...1_000_000)
    .lazy
    .map { $0 * 2 }
    .filter { $0 > 100 }
    .prefix(10)

print(Array(resultado2))  // Solo procesa lo necesario

// Ejemplo demostrativo
func transformar(_ x: Int) -> Int {
    print("Transformando \(x)")
    return x * 2
}

// Eager
print("=== EAGER ===")
let lista = [1, 2, 3, 4, 5]
    .map { transformar($0) }
    .prefix(2)
print(Array(lista))

// Lazy
print("=== LAZY ===")
let lazyLista = [1, 2, 3, 4, 5]
    .lazy
    .map { transformar($0) }
    .prefix(2)
print(Array(lazyLista))
```


### 4.3. Operaciones intermedias vs terminales

**Kotlin:**

```kotlin
val secuencia = sequenceOf(1, 2, 3, 4, 5)

// Operaciones INTERMEDIAS (lazy, no ejecutan nada)
val operaciones = secuencia
    .map { println("map $it"); it * 2 }
    .filter { println("filter $it"); it > 5 }

println("Operaciones definidas, pero no ejecutadas aún")

// Operación TERMINAL (ejecuta toda la cadena)
val resultado = operaciones.toList()
println(resultado)

// Operaciones terminales comunes:
// - toList(), toSet(), toMap()
// - count(), sum(), average()
// - first(), last(), find()
// - forEach()
// - reduce(), fold()
```


***

## 5. Scope functions de Kotlin

Las scope functions son funciones especiales que ejecutan un bloque de código en el contexto de un objeto. Son únicas de Kotlin y muy potentes.

### 5.1. let

Ejecuta el bloque y devuelve el resultado. El objeto se referencia como `it`:

```kotlin
// Uso principal: trabajar con nullables
val nombre: String? = "Ana"
nombre?.let {
    println("Nombre: $it")
    println("Longitud: ${it.length}")
}

// Encadenar operaciones
val resultado = "  kotlin  "
    .let { it.trim() }
    .let { it.uppercase() }
    .let { "Lenguaje: $it" }
println(resultado)  // "Lenguaje: KOTLIN"

// Evitar variables temporales
val numeros = listOf(1, 2, 3, 4, 5)
val sumaDobles = numeros
    .map { it * 2 }
    .let { it.sum() }  // 'it' es la lista transformada
println(sumaDobles)  // 30
```


### 5.2. run

Similar a `let`, pero el objeto es `this` (acceso directo a propiedades):

```kotlin
data class Persona(var nombre: String, var edad: Int)

val persona = Persona("Ana", 25)

val descripcion = persona.run {
    edad += 1  // Acceso directo (this.edad)
    "Después del cumpleaños: $nombre tiene $edad años"
}
println(descripcion)

// Útil para configuración compleja
class ConfiguracionServidor {
    var host = "localhost"
    var puerto = 8080
    var ssl = false
    
    fun conectar() {
        println("Conectando a $host:$puerto (SSL: $ssl)")
    }
}

val config = ConfiguracionServidor().run {
    host = "produccion.com"
    puerto = 443
    ssl = true
    this  // Devuelve el objeto configurado
}
config.conectar()
```


### 5.3. with

Función global (no extension) para ejecutar múltiples operaciones sobre un objeto:

```kotlin
data class Rectangulo(var ancho: Int, var alto: Int) {
    fun area() = ancho * alto
}

val rect = Rectangulo(10, 5)

val resultado = with(rect) {
    ancho = 20
    alto = 10
    println("Área: ${area()}")
    area()  // Valor de retorno
}
println(resultado)  // 200

// Caso práctico: construcción de string complejo
val mensaje = with(StringBuilder()) {
    append("Hola")
    append(" ")
    append("mundo")
    append("!")
    toString()
}
println(mensaje)  // "Hola mundo!"
```


### 5.4. apply

Configura un objeto y lo devuelve (útil para builders):

```kotlin
data class Usuario(
    var nombre: String = "",
    var email: String = "",
    var edad: Int = 0,
    var premium: Boolean = false
)

val usuario = Usuario().apply {
    nombre = "Ana"
    email = "ana@example.com"
    edad = 25
    premium = true
}
println(usuario)

// Muy útil con builders de Android
class Notificacion {
    var titulo: String = ""
    var mensaje: String = ""
    var icono: String = ""
    var prioridad: Int = 0
    
    fun mostrar() {
        println("[$prioridad] $titulo: $mensaje ($icono)")
    }
}

val notif = Notificacion().apply {
    titulo = "Nuevo mensaje"
    mensaje = "Tienes 3 mensajes sin leer"
    icono = "📧"
    prioridad = 1
}
notif.mostrar()
```


### 5.5. also

Ejecuta acciones adicionales sin modificar el objeto (útil para logging):

```kotlin
data class Usuario(val id: Int, val nombre: String)

val usuario = Usuario(1, "Ana")
    .also { println("Usuario creado: ${it.id}") }
    .also { println("Nombre: ${it.nombre}") }

// Caso práctico: debugging en cadenas
val resultado = listOf(1, 2, 3, 4, 5)
    .map { it * 2 }
    .also { println("Después de map: $it") }
    .filter { it > 5 }
    .also { println("Después de filter: $it") }
    .sum()

println("Resultado final: $resultado")
```


### 5.6. Comparativa y cuándo usar cada una

| Función | Contexto | Retorno | Uso principal |
| :-- | :-- | :-- | :-- |
| **let** | `it` | Resultado del bloque | Transformaciones, trabajar con nullables |
| **run** | `this` | Resultado del bloque | Ejecutar lógica y calcular resultado |
| **with** | `this` | Resultado del bloque | Operar sobre objeto sin extension |
| **apply** | `this` | El objeto | Configurar/inicializar objetos |
| **also** | `it` | El objeto | Efectos secundarios (logging, validación) |

```kotlin
// Comparación práctica
data class Config(var host: String = "", var port: Int = 0)

// let: transformar
val mensaje = Config("localhost", 8080).let {
    "Servidor: ${it.host}:${it.port}"
}

// run: ejecutar y retornar resultado
val esValido = Config("localhost", 8080).run {
    host.isNotEmpty() && port > 0
}

// apply: configurar y retornar objeto
val config = Config().apply {
    host = "localhost"
    port = 8080
}

// also: logging sin modificar flujo
val config2 = Config("localhost", 8080)
    .also { println("Configuración creada") }
```


***

## 6. Persistencia declarativa moderna

### 6.1. Android: Room con Flow

Room es la biblioteca ORM oficial de Android que elimina el SQL manual y proporciona seguridad de tipos [file:3].

**Definir entidades:**

```kotlin
@Entity(tableName = "usuarios")
data class Usuario(
    @PrimaryKey(autoGenerate = true)
    val id: Int = 0,
    
    @ColumnInfo(name = "nombre")
    val nombre: String,
    
    @ColumnInfo(name = "email")
    val email: String,
    
    @ColumnInfo(name = "edad")
    val edad: Int
)
```

**Data Access Object (DAO):**

```kotlin
@Dao
interface UsuarioDao {
    // Flow se actualiza automáticamente cuando cambian los datos
    @Query("SELECT * FROM usuarios")
    fun obtenerTodos(): Flow<List<Usuario>>
    
    @Query("SELECT * FROM usuarios WHERE edad >= :edadMinima")
    fun obtenerMayoresDe(edadMinima: Int): Flow<List<Usuario>>
    
    @Insert
    suspend fun insertar(usuario: Usuario)
    
    @Update
    suspend fun actualizar(usuario: Usuario)
    
    @Delete
    suspend fun eliminar(usuario: Usuario)
}
```

**Base de datos:**

```kotlin
@Database(entities = [Usuario::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun usuarioDao(): UsuarioDao
}
```

**Uso con programación funcional:**

```kotlin
// En tu ViewModel o Repository
val usuariosAdultos: Flow<List<Usuario>> = usuarioDao
    .obtenerTodos()
    .map { usuarios -> 
        usuarios.filter { it.edad >= 18 }
    }

val nombresUsuarios: Flow<List<String>> = usuarioDao
    .obtenerTodos()
    .map { usuarios -> 
        usuarios.map { it.nombre }
    }
```

> [!NOTE]
> `Flow` es reactivo: cuando los datos cambian en la base de datos, los observadores se actualizan automáticamente. Es perfecto para arquitecturas MVVM.

### 6.2. iOS: SwiftData y Core Data

**SwiftData (iOS 17+):**

```swift
import SwiftData

@Model
class Usuario {
    @Attribute(.unique) var id: Int
    var nombre: String
    var email: String
    var edad: Int
    
    init(id: Int, nombre: String, email: String, edad: Int) {
        self.id = id
        self.nombre = nombre
        self.email = email
        self.edad = edad
    }
}

// Consultar con predicados funcionales
@Query(filter: #Predicate<Usuario> { $0.edad >= 18 })
var usuariosAdultos: [Usuario]

@Query(sort: \Usuario.nombre)
var usuariosOrdenados: [Usuario]
```

**Core Data (versiones anteriores):**

```swift
// Fetch con predicado
let fetchRequest: NSFetchRequest<Usuario> = Usuario.fetchRequest()
fetchRequest.predicate = NSPredicate(format: "edad >= %d", 18)
fetchRequest.sortDescriptors = [NSSortDescriptor(key: "nombre", ascending: true)]

let usuarios = try? context.fetch(fetchRequest)

// Operaciones funcionales sobre resultados
let nombres = usuarios?.map { $0.nombre }
let mayores30 = usuarios?.filter { $0.edad > 30 }
```


***

## 7. Arquitecturas reactivas: introducción

### 7.1. MVVM (Model-View-ViewModel)

Separa la lógica de negocio (ViewModel) de la UI (View):

**Kotlin (Android):**

```kotlin
// Model
data class Tarea(val id: Int, val titulo: String, val completada: Boolean)

// ViewModel
class TareasViewModel : ViewModel() {
    private val repository = TareasRepository()
    
    val tareas: Flow<List<Tarea>> = repository.obtenerTareas()
        .map { lista -> 
            lista.sortedBy { it.titulo }
        }
    
    val tareasPendientes: Flow<List<Tarea>> = tareas
        .map { lista -> 
            lista.filter { !it.completada }
        }
    
    fun completarTarea(id: Int) = viewModelScope.launch {
        repository.marcarCompletada(id)
    }
}

// View (Fragment/Activity)
class TareasFragment : Fragment() {
    private val viewModel: TareasViewModel by viewModels()
    
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        // Observar cambios automáticamente
        viewLifecycleOwner.lifecycleScope.launch {
            viewModel.tareasPendientes.collect { tareas ->
                actualizarUI(tareas)
            }
        }
    }
}
```

**Swift (iOS):**

```swift
// Model
struct Tarea: Identifiable {
    let id: Int
    var titulo: String
    var completada: Bool
}

// ViewModel
class TareasViewModel: ObservableObject {
    @Published var tareas: [Tarea] = []
    
    var tareasPendientes: [Tarea] {
        tareas.filter { !$0.completada }
    }
    
    func completarTarea(id: Int) {
        if let index = tareas.firstIndex(where: { $0.id == id }) {
            tareas[index].completada = true
        }
    }
}

// View (SwiftUI)
struct TareasView: View {
    @StateObject var viewModel = TareasViewModel()
    
    var body: some View {
        List(viewModel.tareasPendientes) { tarea in
            Text(tarea.titulo)
        }
    }
}
```


### 7.2. Repository Pattern

Abstrae el origen de datos (local, remoto, caché):

```kotlin
class UsuariosRepository(
    private val localDataSource: UsuarioDao,
    private val remoteDataSource: ApiService
) {
    fun obtenerUsuarios(): Flow<List<Usuario>> = flow {
        // Primero emite datos locales
        emit(localDataSource.obtenerTodos().first())
        
        // Luego actualiza desde remoto
        try {
            val remotos = remoteDataSource.fetchUsuarios()
            localDataSource.insertarTodos(remotos)
            emit(remotos)
        } catch (e: Exception) {
            // Manejo de errores
        }
    }
}
```


### 7.3. Single Source of Truth

Los datos fluyen en una sola dirección:

```
Base de datos (Room) 
    ↓ Flow
Repository
    ↓ Flow  
ViewModel (transformaciones funcionales)
    ↓ StateFlow/LiveData
View (UI reacciona automáticamente)
```

> [!TIP]
> Estos conceptos son fundamentales en desarrollo móvil moderno. El uso de programación funcional (map, filter, etc.) sobre streams de datos (Flow, Combine) hace el código predecible y fácil de mantener.

***

## 8. Proyecto integrador: gestor de tareas

Implementa un gestor de tareas completo usando todo lo aprendido:

**Kotlin:**

```kotlin
// === MODEL ===
data class Tarea(
    val id: Int,
    val titulo: String,
    val descripcion: String,
    val prioridad: Prioridad,
    val completada: Boolean,
    val fechaCreacion: Long
)

enum class Prioridad { BAJA, MEDIA, ALTA }

// === REPOSITORY ===
class TareasRepository {
    private val tareas = mutableListOf<Tarea>()
    
    fun obtenerTodas(): List<Tarea> = tareas.toList()
    
    fun agregar(tarea: Tarea) {
        tareas.add(tarea)
    }
    
    fun completar(id: Int) {
        tareas.find { it.id == id }?.let { tarea ->
            val index = tareas.indexOf(tarea)
            tareas[index] = tarea.copy(completada = true)
        }
    }
}

// === OPERACIONES FUNCIONALES ===
class TareasService(private val repository: TareasRepository) {
    
    fun obtenerPendientes(): List<Tarea> {
        return repository.obtenerTodas()
            .filter { !it.completada }
    }
    
    fun obtenerPorPrioridad(prioridad: Prioridad): List<Tarea> {
        return repository.obtenerTodas()
            .filter { it.prioridad == prioridad }
            .sortedByDescending { it.fechaCreacion }
    }
    
    fun obtenerEstadisticas(): Map<String, Int> {
        val todas = repository.obtenerTodas()
        return mapOf(
            "total" to todas.size,
            "completadas" to todas.count { it.completada },
            "pendientes" to todas.count { !it.completada },
            "alta_prioridad" to todas.count { it.prioridad == Prioridad.ALTA }
        )
    }
    
    fun buscar(query: String): List<Tarea> {
        return repository.obtenerTodas()
            .filter { 
                it.titulo.contains(query, ignoreCase = true) ||
                it.descripcion.contains(query, ignoreCase = true)
            }
    }
    
    fun obtenerResumen(): String {
        val tareas = repository.obtenerTodas()
        val porPrioridad = tareas.groupBy { it.prioridad }
        
        return buildString {
            appendLine("=== RESUMEN DE TAREAS ===")
            porPrioridad.forEach { (prioridad, lista) ->
                val completadas = lista.count { it.completada }
                appendLine("$prioridad: ${lista.size} tareas ($completadas completadas)")
            }
        }
    }
}
```


***

## 9. Ejercicios de refactorización

### Ejercicio 1: De imperativo a funcional

```kotlin
// CÓDIGO IMPERATIVO (refactorizar)
val numeros = listOf(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
val resultado = mutableListOf<String>()

for (numero in numeros) {
    if (numero % 2 == 0) {
        val cuadrado = numero * numero
        if (cuadrado > 10) {
            resultado.add("Número: $cuadrado")
        }
    }
}
println(resultado)

// SOLUCIÓN FUNCIONAL:
val resultadoFuncional = numeros
    .filter { it % 2 == 0 }           // Solo pares
    .map { it * it }                  // Al cuadrado
    .filter { it > 10 }               // Mayores que 10
    .map { "Número: $it" }            // Formatear
    
println(resultadoFuncional)
```


### Ejercicio 2: Análisis de ventas

```kotlin
data class Venta(val producto: String, val cantidad: Int, val precio: Double, val fecha: String)

val ventas = listOf(
    Venta("Laptop", 2, 999.0, "2024-01-15"),
    Venta("Mouse", 10, 29.0, "2024-01-15"),
    Venta("Teclado", 5, 79.0, "2024-01-16"),
    Venta("Laptop", 1, 999.0, "2024-01-16"),
    Venta("Monitor", 3, 250.0, "2024-01-17")
)

// TU TAREA: Implementa usando programación funcional

// 1. Total facturado
val totalFacturado = ventas
    .map { it.cantidad * it.precio }
    .sum()

// 2. Producto más vendido (por cantidad)
val productoMasVendido = ventas
    .groupBy { it.producto }
    .mapValues { (_, lista) -> lista.sumOf { it.cantidad } }
    .maxByOrNull { it.value }

// 3. Ventas por día
val ventasPorDia = ventas
    .groupBy { it.fecha }
    .mapValues { (_, lista) -> 
        lista.sumOf { it.cantidad * it.precio }
    }

// 4. Top 3 productos por facturación
val top3Productos = ventas
    .groupBy { it.producto }
    .mapValues { (_, lista) -> 
        lista.sumOf { it.cantidad * it.precio }
    }
    .toList()
    .sortedByDescending { it.second }
    .take(3)
    .map { "${it.first}: €${it.second}" }

println("Total: €$totalFacturado")
println("Más vendido: $productoMasVendido")
println("Por día: $ventasPorDia")
println("Top 3: $top3Productos")
```


### Ejercicio 3: Pipeline complejo

```kotlin
// Procesar log de servidor
val logs = listOf(
    "ERROR 2024-01-01 10:23:45 Database connection failed",
    "INFO 2024-01-01 10:24:00 User logged in",
    "ERROR 2024-01-01 10:25:12 Timeout on external API",
    "WARN 2024-01-01 10:26:30 High memory usage",
    "ERROR 2024-01-01 10:27:45 Database connection failed"
)

// Implementa:
// 1. Extraer solo errores
// 2. Obtener el mensaje sin fecha/hora
// 3. Contar errores por tipo
// 4. Devolver los 3 errores más frecuentes

val analisisErrores = logs
    .filter { it.startsWith("ERROR") }
    .map { 
        it.substringAfter("ERROR ")
            .substringAfter(" ")
            .substringAfter(" ")
    }
    .groupingBy { it }
    .eachCount()
    .toList()
    .sortedByDescending { it.second }
    .take(3)

println(analisisErrores)
```

***

## Comparativa final: Java → Kotlin/Swift

| Lo que era... | Se convierte en... |
| :-- | :-- |
| Bucles `for` explícitos | `map`, `filter`, `reduce` |
| `if (x != null) { x.method() }` | `x?.method()` o `x.let { }` |
| Getters/setters manuales | Properties automáticos |
| Clases para todo | Data classes / Structs |
| Código repetitivo | Código conciso y expresivo |
| Errores en runtime | Errores en compilación |
| Estado mutable | Inmutabilidad por defecto |

## Habilidades adquiridas

✅ Escribir código funcional expresivo y conciso
✅ Transformar colecciones con operaciones encadenadas
✅ Gestionar nulos de forma segura
✅ Diseñar arquitecturas reactivas
✅ Optimizar rendimiento con evaluación perezosa
✅ Pensar en términos de flujos de datos inmutables

## Próximos pasos

**Para profundizar en Kotlin:**

- Corrutinas avanzadas (async/await)
- Kotlin Multiplatform (compartir código iOS/Android)
- DSLs (Domain Specific Languages)
- Arrow library (programación funcional avanzada)

**Para profundizar en Swift:**

- SwiftUI (UI declarativa)
- Combine framework (programación reactiva)
- Async/await y concurrencia estructurada
- Swift Package Manager

**Arquitecturas avanzadas:**

- Clean Architecture
- MVI (Model-View-Intent)
- Redux/Flux patterns
- Dependency Injection


### Reflexión final

Habéis pasado de escribir código que dice **cómo** hacer las cosas (imperativo) a código que declara **qué** queremos lograr (declarativo). Este cambio de mentalidad es fundamental en el desarrollo moderno.

La programación funcional no es solo una moda: es una forma de pensar sobre el código que reduce errores, facilita el testing y hace el mantenimiento mucho más sencillo. Los conceptos que habéis aprendido (inmutabilidad, funciones puras, transformaciones de datos) son aplicables a cualquier lenguaje moderno.

> [!NOTE]
> El mejor código es el que se lee como prosa: claro, conciso y sin sorpresas. Las herramientas de programación funcional que habéis aprendido os ayudan a escribir exactamente ese tipo de código.

## Autoevaluación

Verifica que has comprendido los conceptos de programación funcional y arquitecturas modernas antes de finalizar.

**1. ¿Cuál es la diferencia principal entre programación imperativa y declarativa?**
- a) La imperativa es más moderna
- b) La imperativa dice "cómo" hacer algo paso a paso, la declarativa dice "qué" queremos conseguir
- c) La declarativa no permite bucles
- d) No hay diferencia práctica

**2. ¿Qué hace la operación `map` sobre una colección?**
- a) Filtra elementos según una condición
- b) Transforma cada elemento aplicando una función
- c) Suma todos los elementos
- d) Ordena la colección

**3. ¿Cuál es el resultado de este código?**
```kotlin
val numeros = listOf(1, 2, 3, 4, 5, 6)
val resultado = numeros.filter { it % 2 == 0 }.map { it * it }
```
- a)[1]
- b) 
- c)[2]
- d) 56

**4. ¿Qué operación funcional colapsa una colección en un único valor?**
- a) map
- b) filter
- c) reduce
- d) flatMap

**5. En programación funcional, ¿qué ventaja proporciona la inmutabilidad?**
- a) El código es más lento
- b) Facilita el trabajo con concurrencia y hace el estado predecible
- c) Ocupa menos memoria
- d) Permite modificar objetos más fácilmente

**6. ¿Cuál es el propósito de `flatMap`?**
- a) Filtrar elementos nulos
- b) Aplanar colecciones anidadas mientras se aplica una transformación
- c) Ordenar una colección
- d) Invertir el orden de los elementos

**7. En Kotlin, ¿qué scope function devuelve el contexto del objeto y es útil para configuración?**
- a) let
- b) run
- c) apply
- d) also

**8. ¿Qué es la evaluación perezosa (lazy evaluation)?**
- a) Ejecutar operaciones inmediatamente
- b) Retrasar el cómputo hasta que el resultado sea necesario
- c) Cachear todos los resultados
- d) Paralelizar operaciones automáticamente

**9. En el patrón MVVM, ¿cuál es el rol del ViewModel?**
- a) Renderizar la interfaz de usuario
- b) Contener la lógica de negocio y exponer datos observables a la vista
- c) Gestionar la base de datos directamente
- d) Manejar eventos táctiles

**10. ¿Cuál es el resultado de encadenar múltiples operaciones funcionales?**
```kotlin
listOf(1, 2, 3).map { it * 2 }.filter { it > 3 }.sum()
```
- a) 6
- b) 10
- c) 12
- d) 

**Respuestas correctas:** 1-b, 2-b, 3-b, 4-c, 5-b, 6-b, 7-c, 8-b, 9-b, 10-b


***
