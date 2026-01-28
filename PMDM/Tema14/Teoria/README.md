# TEMA 14: Fundamentos del lenguaje Kotlin

## Tabla de contenidos

1. [Introducción y contexto](#1-introducci%C3%B3n-y-contexto)
    - 1.1. [¿Qué es Kotlin?](#11-qu%C3%A9-es-kotlin)
    - 1.2. [Kotlin vs Java: diferencias filosóficas](#12-kotlin-vs-java-diferencias-filos%C3%B3ficas)
    - 1.3. [Casos de uso de Kotlin](#13-casos-de-uso-de-kotlin)
2. [Entorno de desarrollo](#2-entorno-de-desarrollo)
    - 2.1. [Android Studio](#21-android-studio)
    - 2.2. [IntelliJ IDEA](#22-intellij-idea)
    - 2.3. [Compilación y ejecución](#23-compilaci%C3%B3n-y-ejecuci%C3%B3n)
3. [Variables, constantes y tipos de datos](#3-variables-constantes-y-tipos-de-datos)
    - 3.1. [Declaración: `val` vs `var`](#31-declaraci%C3%B3n-val-vs-var)
    - 3.2. [Inferencia de tipos](#32-inferencia-de-tipos)
    - 3.3. [Tipos de datos básicos](#33-tipos-de-datos-b%C3%A1sicos)
    - 3.4. [Conversión explícita entre tipos](#34-conversi%C3%B3n-expl%C3%ADcita-entre-tipos)
    - 3.5. [String templates](#35-string-templates)
4. [Null safety: el fin del NullPointerException](#4-null-safety-el-fin-del-nullpointerexception)
    - 4.1. [El problema en Java](#41-el-problema-en-java)
    - 4.2. [Tipos nullable y non-nullable](#42-tipos-nullable-y-non-nullable)
    - 4.3. [Operador safe call (`?.`)](#43-operador-safe-call-)
    - 4.4. [Operador Elvis (`?:`)](#44-operador-elvis-)
    - 4.5. [Operador not-null assertion (`!!`)](#45-operador-not-null-assertion-)
    - 4.6. [Safe casts (`as?`)](#46-safe-casts-as)
    - 4.7. [Función `let` para manejo seguro](#47-funci%C3%B3n-let-para-manejo-seguro)
5. [Colecciones básicas](#5-colecciones-b%C3%A1sicas)
    - 5.1. [Listas: `List` y `MutableList`](#51-listas-list-y-mutablelist)
    - 5.2. [Conjuntos: `Set` y `MutableSet`](#52-conjuntos-set-y-mutableset)
    - 5.3. [Mapas: `Map` y `MutableMap`](#53-mapas-map-y-mutablemap)
    - 5.4. [Inmutabilidad vs mutabilidad](#54-inmutabilidad-vs-mutabilidad)
6. [Control de flujo](#6-control-de-flujo)
    - 6.1. [`if` como expresión](#61-if-como-expresi%C3%B3n)
    - 6.2. [`when`: el super-switch](#62-when-el-super-switch)
    - 6.3. [Rangos](#63-rangos)
    - 6.4. [Bucles: `for`, `while`, `do-while`](#64-bucles-for-while-do-while)
7. [Funciones básicas](#7-funciones-b%C3%A1sicas)
    - 7.1. [Declaración y sintaxis](#71-declaraci%C3%B3n-y-sintaxis)
    - 7.2. [Funciones de expresión única](#72-funciones-de-expresi%C3%B3n-%C3%BAnica)
    - 7.3. [Parámetros con valores por defecto](#73-par%C3%A1metros-con-valores-por-defecto)
    - 7.4. [Parámetros nombrados](#74-par%C3%A1metros-nombrados)
    - 7.5. [Funciones vararg](#75-funciones-vararg)
    - 7.6. [Funciones locales](#76-funciones-locales)
8. [Programación orientada a objetos](#8-programaci%C3%B3n-orientada-a-objetos)
    - 8.1. [Clases básicas](#81-clases-b%C3%A1sicas)
    - 8.2. [Propiedades y backing fields](#82-propiedades-y-backing-fields)
    - 8.3. [Constructores: primario y secundarios](#83-constructores-primario-y-secundarios)
    - 8.4. [Herencia](#84-herencia)
    - 8.5. [Sobrescritura de métodos](#85-sobrescritura-de-m%C3%A9todos)
    - 8.6. [Clases abstractas](#86-clases-abstractas)
    - 8.7. [Interfaces](#87-interfaces)
    - 8.8. [Data classes](#88-data-classes)
    - 8.9. [Sealed classes](#89-sealed-classes)
    - 8.10. [Enum classes](#810-enum-classes)
9. [Object y companion object](#9-object-y-companion-object)
    - 9.1. [Object declarations (singleton)](#91-object-declarations-singleton)
    - 9.2. [Companion objects](#92-companion-objects)
    - 9.3. [Object expressions (clases anónimas)](#93-object-expressions-clases-an%C3%B3nimas)
10. [Extensions](#10-extensions)
    - 10.1. [Funciones de extensión](#101-funciones-de-extensi%C3%B3n)
    - 10.2. [Propiedades de extensión](#102-propiedades-de-extensi%C3%B3n)
    - 10.3. [Extensions sobre tipos nullable](#103-extensions-sobre-tipos-nullable)
11. [Modificadores de visibilidad](#11-modificadores-de-visibilidad)
    - 11.1. [Niveles de acceso](#111-niveles-de-acceso)
    - 11.2. [Comparación con Java](#112-comparaci%C3%B3n-con-java)
12. [Comparación final: Kotlin vs Java](#12-comparaci%C3%B3n-final-kotlin-vs-java)
13. [Actividades prácticas](#13-actividades-pr%C3%A1cticas)
14. [Resumen del tema](#14-resumen-del-tema)

***

## 1. Introducción y contexto

### 1.1. ¿Qué es Kotlin?

Kotlin es un lenguaje de programación moderno, expresivo y seguro desarrollado por **JetBrains** (la empresa creadora de IntelliJ IDEA) y lanzado oficialmente en 2011. En mayo de 2017, Google lo declaró **lenguaje oficial para el desarrollo Android**, y desde 2019 es el lenguaje **preferido** por Google para nuevas aplicaciones Android.

**Características principales:**

- **100% interoperable con Java:** Puedes usar código Java desde Kotlin y viceversa sin problemas
- **Corre sobre la JVM:** Compila a bytecode de Java, por lo que funciona en cualquier plataforma que soporte Java
- **Conciso:** Reduce significativamente el código boilerplate
- **Seguro:** Sistema de tipos diseñado para eliminar NullPointerException
- **Multiplataforma:** Kotlin/Native, Kotlin/JS, Kotlin Multiplatform


### 1.2. Kotlin vs Java: diferencias filosóficas

| Aspecto | Java | Kotlin |
| :-- | :-- | :-- |
| **Verbosidad** | Alta (mucho código repetitivo) | Baja (sintaxis concisa) |
| **Null safety** | No integrada (cualquier objeto puede ser null) | Integrada en el sistema de tipos |
| **Mutabilidad** | Variables mutables por defecto | Promueve inmutabilidad (`val`) |
| **Getters/Setters** | Explícitos y manuales | Automáticos (properties) |
| **Type inference** | Limitada (desde Java 10 con `var`) | Completa y potente |
| **Funciones** | Dentro de clases (métodos) | Top-level (fuera de clases) |
| **Data classes** | Requiere escribir mucho código | Una línea: `data class` |
| **Smart casts** | Casting manual necesario | Automático tras comprobación de tipo |
| **Checked exceptions** | Obligatorias | No existen (todas son unchecked) |
| **Punto y coma** | Obligatorio | Opcional (generalmente no se usa) |

> [!IMPORTANT]
> Kotlin **NO** es un reemplazo que obliga a reescribir todo tu código Java. Puedes adoptar Kotlin gradualmente en proyectos existentes, archivo por archivo, y ambos lenguajes coexistirán perfectamente.

### 1.3. Casos de uso de Kotlin

- **Desarrollo Android:** El caso de uso principal y donde más ha crecido
- **Backend/Servidores:** Frameworks como Ktor, Spring Boot con Kotlin
- **Aplicaciones de escritorio:** Con JavaFX o Compose for Desktop
- **Kotlin Multiplatform:** Compartir lógica entre Android, iOS, Web y Desktop
- **Scripting:** Kotlin se puede usar como lenguaje de scripting (.kts)
- **Data Science:** Con bibliotecas como Kotlin DataFrame y integración con Jupyter

***

## 2. Entorno de desarrollo

### 2.1. Android Studio

**Android Studio** es el IDE oficial para desarrollo Android y tiene soporte completo para Kotlin desde la versión 3.0 (2017). Es una versión modificada de IntelliJ IDEA específicamente para Android.

**Características:**

- Conversión automática de Java a Kotlin (aunque requiere revisión manual)
- Autocompletado inteligente de código Kotlin
- Refactorización segura
- Kotlin REPL integrado (para ejecutar código rápidamente)
- Templates de proyectos en Kotlin


### 2.2. IntelliJ IDEA

**IntelliJ IDEA** (Community o Ultimate) es el IDE desarrollado por JetBrains y tiene el mejor soporte para Kotlin, ya que los mismos desarrolladores mantienen ambos proyectos.

### 2.3. Compilación y ejecución

```kotlin
// Archivo: Main.kt
fun main() {
    println("Hola desde Kotlin")
}
```

**Compilación desde línea de comandos:**

```bash
kotlinc Main.kt -include-runtime -d Main.jar
java -jar Main.jar
```

> [!TIP]
> Para seguir este tema, si trabajáis en Windows sin Android Studio, podéis usar **Kotlin Playground** online (https://play.kotlinlang.org/) para probar todos los ejemplos de código de forma inmediata.

***

## 3. Variables, constantes y tipos de datos

### 3.1. Declaración: `val` vs `var`

Kotlin promueve la **inmutabilidad por defecto**, al igual que Swift. Esta decisión de diseño previene muchos errores relacionados con el estado mutable.

```kotlin
// val (value) - Referencia inmutable (read-only)
val nombre = "Ana"
// nombre = "Luis"  // ❌ ERROR: Val cannot be reassigned

// var (variable) - Referencia mutable
var edad = 25
edad = 26  // ✅ Correcto
edad += 1  // ✅ Correcto
```

**Equivalencia con Java:**

```java
// Java
final String nombre = "Ana";  // Constante

// Kotlin
val nombre = "Ana"  // Más conciso y expresivo
```

> [!NOTE]
> **Buena práctica:** Usa siempre `val` por defecto. Solo cambia a `var` cuando realmente necesites modificar el valor. Esta práctica hace tu código más predecible y seguro en entornos concurrentes.

### 3.2. Inferencia de tipos

Kotlin tiene un sistema de **inferencia de tipos muy potente**. El compilador deduce el tipo automáticamente en la mayoría de situaciones:

```kotlin
// El compilador infiere los tipos automáticamente
val numero = 42              // Int
val precio = 19.99           // Double
val mensaje = "Hola"         // String
val activo = true            // Boolean
val letra = 'A'              // Char

// Declaración explícita de tipos (opcional pero a veces útil)
val peso: Double = 70.5
val inicial: Char = 'M'
val aprobado: Boolean = true
```


### 3.3. Tipos de datos básicos

En Kotlin, **todo es un objeto**. No existen tipos primitivos como en Java (`int`, `double`, etc.). Sin embargo, el compilador los optimiza a primitivos JVM cuando es posible.

#### Números

```kotlin
// Enteros
val byte: Byte = 127                    // 8 bits (-128 a 127)
val short: Short = 32767                // 16 bits
val int: Int = 2147483647               // 32 bits (tipo por defecto)
val long: Long = 9223372036854775807L   // 64 bits (sufijo L)

// Números de punto flotante
val float: Float = 3.14f                // 32 bits (sufijo f o F)
val double: Double = 3.14159265         // 64 bits (tipo por defecto)

// Números sin signo (desde Kotlin 1.3)
val uByte: UByte = 255u
val uInt: UInt = 4294967295u
val uLong: ULong = 18446744073709551615u
```


#### Otros tipos básicos

```kotlin
// Booleanos
val verdadero: Boolean = true
val falso: Boolean = false

// Caracteres (comillas simples)
val letra: Char = 'K'
val numero: Char = '5'
val simbolo: Char = '@'

// Strings (comillas dobles)
val saludo: String = "Hola Kotlin"
```


#### Literales numéricos

```kotlin
// Decimal
val decimal = 123

// Hexadecimal (prefijo 0x)
val hexadecimal = 0x1F  // 31 en decimal

// Binario (prefijo 0b)
val binario = 0b1010    // 10 en decimal

// No hay literales octales en Kotlin

// Guiones bajos para legibilidad
val millon = 1_000_000
val bytes = 0xFF_EC_DE_5E
val pi = 3.141_592_653_589_793
```


### 3.4. Conversión explícita entre tipos

A diferencia de Java, **Kotlin NO permite conversiones implícitas** entre tipos numéricos para evitar errores sutiles:

```kotlin
val entero: Int = 10
val largo: Long = 100L

// ❌ Esto NO compila en Kotlin
// val suma = entero + largo  // ERROR: Type mismatch

// ✅ Conversión explícita requerida
val sumaCorrecta = entero.toLong() + largo  // 110
val sumaInt = entero + largo.toInt()        // 110

// Métodos de conversión disponibles
val x = 42
val xByte = x.toByte()
val xShort = x.toShort()
val xLong = x.toLong()
val xFloat = x.toFloat()
val xDouble = x.toDouble()
val xChar = x.toChar()  // Convierte a carácter por código ASCII
val xString = x.toString()
```

**Conversión desde String:**

```kotlin
val texto = "123"
val numero = texto.toInt()           // 123
val decimal = "3.14".toDouble()      // 3.14

// Conversión segura (devuelve null si falla)
val textoInvalido = "abc"
val resultado = textoInvalido.toIntOrNull()  // null
println(resultado ?: "Conversión fallida")   // "Conversión fallida"
```


### 3.5. String templates

Una de las características más útiles de Kotlin es la **interpolación de strings**. Permite insertar expresiones directamente dentro de strings usando `$`:

```kotlin
val nombre = "Carlos"
val edad = 28

// Interpolación simple con $
val mensaje = "Hola, soy $nombre y tengo $edad años"
println(mensaje)  // "Hola, soy Carlos y tengo 28 años"

// Expresiones complejas con ${}
val precioBase = 100.0
val iva = 0.21
val precioFinal = "El precio final es ${precioBase * (1 + iva)}€"
println(precioFinal)  // "El precio final es 121.0€"

// Acceso a propiedades
val usuario = "admin"
val bienvenida = "Usuario: ${usuario.uppercase()}"
println(bienvenida)  // "Usuario: ADMIN"

// Escape del símbolo $ si lo necesitas literal
val precio = "El producto cuesta \$50"
println(precio)  // "El producto cuesta $50"
```

**Strings multilínea:**

```kotlin
// Triple comillas para strings multilínea
val poema = """
    En un lugar de La Mancha,
    de cuyo nombre no quiero acordarme,
    no ha mucho tiempo que vivía un hidalgo.
""".trimIndent()

// Con interpolación
val nombre = "Don Quijote"
val texto = """
    Personaje: $nombre
    Autor: Miguel de Cervantes
    Año: 1605
""".trimIndent()

// trimMargin con prefijo personalizado
val html = """
    |<html>
    |  <body>
    |    <h1>Título</h1>
    |  </body>
    |</html>
""".trimMargin()
```


***

## 4. Null safety: el fin del NullPointerException

Esta es **la característica más importante** de Kotlin y la razón principal por la que reduce drásticamente los crashes en producción.

### 4.1. El problema en Java

```java
// Java - El compilador no ayuda
String nombre = obtenerNombre();  // Puede devolver null
int longitud = nombre.length();   // 💥 NullPointerException si es null

// Solución manual (propensa a olvidos)
if (nombre != null) {
    int longitud = nombre.length();
}
```


### 4.2. Tipos nullable y non-nullable

En Kotlin, **el sistema de tipos distingue entre referencias que pueden ser null y las que no**:

```kotlin
// Tipo NON-NULLABLE (por defecto)
var nombre: String = "Ana"
// nombre = null  // ❌ ERROR: Null can not be a value of a non-null type String

// Tipo NULLABLE (añadiendo ?)
var apellido: String? = "García"
apellido = null  // ✅ Correcto

// El compilador no te deja usar un nullable directamente
val longitud = apellido.length  // ❌ ERROR: Only safe (?.) or non-null asserted (!!.) calls are allowed
```

> [!IMPORTANT]
> La decisión de si una variable puede ser null **debe tomarse en el momento de la declaración**. Esto obliga a los desarrolladores a pensar explícitamente sobre la nulabilidad desde el diseño.

### 4.3. Operador safe call (`?.`)

Ejecuta una operación **solo si** el valor no es null. Si es null, toda la expresión devuelve null:

```kotlin
val nombre: String? = "Kotlin"
val longitud: Int? = nombre?.length  // 6 (Int? porque nombre puede ser null)

val nombreNull: String? = null
val longitudNull: Int? = nombreNull?.length  // null (no crashea)

// Encadenamiento de safe calls
data class Direccion(val calle: String, val numero: Int)
data class Persona(val nombre: String, val direccion: Direccion?)

val persona: Persona? = Persona("Ana", null)
val calle = persona?.direccion?.calle  // null (sin errores)
```

**Con métodos:**

```kotlin
val texto: String? = "  kotlin  "
val textoLimpio = texto?.trim()?.uppercase()
// Si texto es null, textoLimpio será null
// Si texto tiene valor, textoLimpio será String? con el resultado

println(textoLimpio)  // "KOTLIN"
```


### 4.4. Operador Elvis (`?:`)

Proporciona un **valor por defecto** cuando el operando izquierdo es null. Se llama Elvis porque `?:` parece el peinado de Elvis Presley visto de lado 😄

```kotlin
val nombreUsuario: String? = null

// Sin Elvis (verboso)
val nombre1 = if (nombreUsuario != null) nombreUsuario else "Invitado"

// Con Elvis (conciso)
val nombre2 = nombreUsuario ?: "Invitado"
println(nombre2)  // "Invitado"

// Combinado con safe call
val longitud = nombreUsuario?.length ?: 0
println(longitud)  // 0

// Útil para return early
fun procesarUsuario(nombre: String?) {
    val nombreValido = nombre ?: return  // Sale si es null
    println("Procesando: $nombreValido")
}

procesarUsuario(null)       // Sale inmediatamente
procesarUsuario("Carlos")   // "Procesando: Carlos"
```

**Lanzar excepciones con Elvis:**

```kotlin
fun obtenerConfiguracion(clave: String): String {
    val valor: String? = configuraciones[clave]
    return valor ?: throw IllegalStateException("Configuración '$clave' no encontrada")
}
```


### 4.5. Operador not-null assertion (`!!`)

Convierte un tipo nullable a non-nullable **de forma forzada**. Si el valor es null, lanza `NullPointerException`:

```kotlin
val nombre: String? = "Kotlin"
val longitudForzada: Int = nombre!!.length  // ✅ Funciona (nombre no es null)

val nombreNull: String? = null
val crash = nombreNull!!.length  // 💥 NullPointerException
```

> [!CAUTION]
> El operador `!!` es **peligroso** y debe evitarse en código de producción. Solo úsalo cuando estés 100% seguro de que el valor no es null, o en tests unitarios. Si lo usas, añade un comentario explicando por qué estás seguro.

### 4.6. Safe casts (`as?`)

El casting tradicional (`as`) lanza `ClassCastException` si falla. El safe cast (`as?`) devuelve null:

```kotlin
val objeto: Any = "Hola"

// Casting tradicional (puede crashear)
val numero: Int = objeto as Int  // 💥 ClassCastException

// Safe cast (devuelve null si falla)
val numeroSeguro: Int? = objeto as? Int
println(numeroSeguro)  // null

val texto: String? = objeto as? String
println(texto)  // "Hola"

// Combinado con Elvis
val longitudTexto = (objeto as? String)?.length ?: 0
```


### 4.7. Función `let` para manejo seguro

`let` ejecuta un bloque de código solo si el valor no es null:

```kotlin
val nombre: String? = "Ana"

// Sin let (verboso)
if (nombre != null) {
    println("Nombre: $nombre")
    println("Longitud: ${nombre.length}")
}

// Con let (idiomático)
nombre?.let {
    // Dentro de este bloque, 'it' es String (non-nullable)
    println("Nombre: $it")
    println("Longitud: ${it.length}")
}

// Con nombre personalizado
nombre?.let { n ->
    println("Hola $n")
}

// Ejemplo práctico: validación
fun procesarPedido(usuario: String?, cantidad: Int?) {
    usuario?.let { u ->
        cantidad?.let { c ->
            println("Pedido de $c unidades para $u")
        }
    }
}

procesarPedido("Ana", 5)    // "Pedido de 5 unidades para Ana"
procesarPedido(null, 5)     // No imprime nada
procesarPedido("Ana", null) // No imprime nada
```


***

## 5. Colecciones básicas

Kotlin distingue entre **colecciones inmutables** (read-only) y **colecciones mutables**. Esta separación es fundamental para la seguridad del código.

### 5.1. Listas: `List` y `MutableList`

#### List (inmutable/read-only)

```kotlin
// Creación con listOf (inmutable)
val frutas: List<String> = listOf("Manzana", "Pera", "Naranja")
val numeros = listOf(1, 2, 3, 4, 5)  // Inferencia de tipo List<Int>

// Acceso por índice
println(frutas[0])        // "Manzana"
println(frutas.get(1))    // "Pera"

// Propiedades
println(frutas.size)      // 3
println(frutas.isEmpty()) // false

// No se puede modificar
// frutas.add("Plátano")  // ❌ ERROR: Unresolved reference: add
```


#### MutableList (mutable)

```kotlin
// Creación con mutableListOf
val colores = mutableListOf("Rojo", "Verde", "Azul")

// Añadir elementos
colores.add("Amarillo")
colores.add(1, "Negro")  // Inserta en índice 1
colores += "Blanco"      // Operador += equivale a add

println(colores)  // [Rojo, Negro, Verde, Azul, Amarillo, Blanco]

// Modificar elementos
colores[0] = "Rojo oscuro"

// Eliminar elementos
colores.remove("Negro")        // Elimina por valor
colores.removeAt(2)           // Elimina por índice
colores.clear()               // Vacía la lista

// Crear lista mutable vacía con tipo
val nombres = mutableListOf<String>()
nombres.add("Ana")
nombres.add("Luis")
```


#### Operaciones comunes

```kotlin
val lista = listOf(10, 20, 30, 40, 50)

// Búsqueda
println(lista.contains(30))        // true
println(30 in lista)              // true (equivalente)
println(lista.indexOf(40))        // 3
println(lista.indexOf(99))        // -1 (no encontrado)

// Sublistas
println(lista.subList(1, 3))      // [20, 30] (hasta índice 3 excluido)

// Primero y último
println(lista.first())            // 10
println(lista.last())             // 50
println(lista.firstOrNull())      // 10 (null si está vacía)

// Iterar
for (numero in lista) {
    println(numero)
}

for ((index, valor) in lista.withIndex()) {
    println("$index: $valor")
}
```


### 5.2. Conjuntos: `Set` y `MutableSet`

Los Sets almacenan elementos únicos sin orden garantizado:

```kotlin
// Set inmutable
val numeros: Set<Int> = setOf(1, 2, 3, 2, 1)
println(numeros)  // [1, 2, 3] (duplicados eliminados)

// MutableSet
val lenguajes = mutableSetOf("Kotlin", "Java", "Swift")

// Añadir (devuelve true si se añadió, false si ya existía)
println(lenguajes.add("Python"))  // true
println(lenguajes.add("Kotlin"))  // false (ya existe)

// Eliminar
lenguajes.remove("Java")

// Comprobar pertenencia (muy eficiente O(1))
println("Kotlin" in lenguajes)  // true

// Operaciones de conjuntos
val set1 = setOf(1, 2, 3, 4)
val set2 = setOf(3, 4, 5, 6)

println(set1.union(set2))              // [1, 2, 3, 4, 5, 6]
println(set1.intersect(set2))          // [3, 4]
println(set1.subtract(set2))           // [1, 2]
```


### 5.3. Mapas: `Map` y `MutableMap`

Los mapas almacenan pares clave-valor:

```kotlin
// Map inmutable
val capitales: Map<String, String> = mapOf(
    "España" to "Madrid",
    "Francia" to "París",
    "Italia" to "Roma"
)

// Acceso (devuelve nullable)
val capitalEspana: String? = capitales["España"]  // "Madrid"
val capitalAlemania: String? = capitales["Alemania"]  // null

// Acceso con valor por defecto
val capital = capitales.getOrDefault("Alemania", "Desconocida")

// MutableMap
val edades = mutableMapOf(
    "Ana" to 25,
    "Luis" to 30,
    "María" to 28
)

// Añadir o modificar
edades["Carlos"] = 35  // Añade
edades["Ana"] = 26     // Modifica
edades.put("Pedro", 40)

// Eliminar
edades.remove("Luis")

// Propiedades
println(edades.size)        // 4
println(edades.isEmpty())   // false
println(edades.keys)        // [Ana, María, Carlos, Pedro]
println(edades.values)      // [26, 28, 35, 40]

// Comprobar existencia
println(edades.containsKey("Ana"))      // true
println(edades.containsValue(28))       // true

// Iterar
for ((nombre, edad) in edades) {
    println("$nombre tiene $edad años")
}

// Solo claves
for (nombre in edades.keys) {
    println(nombre)
}

// Solo valores
for (edad in edades.values) {
    println(edad)
}
```


### 5.4. Inmutabilidad vs mutabilidad

```kotlin
// Inmutable (read-only) - No se puede modificar
val listaInmutable: List<String> = listOf("A", "B", "C")
// listaInmutable.add("D")  // ❌ ERROR

// Mutable - Se puede modificar
val listaMutable: MutableList<String> = mutableListOf("A", "B", "C")
listaMutable.add("D")  // ✅ OK

// ⚠️ IMPORTANTE: val/var controla la referencia, no el contenido
val lista = mutableListOf(1, 2, 3)
lista.add(4)  // ✅ OK (la lista es mutable)
// lista = mutableListOf(5, 6)  // ❌ ERROR (la referencia es val)

var listaVar = mutableListOf(1, 2, 3)
listaVar.add(4)  // ✅ OK
listaVar = mutableListOf(5, 6)  // ✅ OK (la referencia es var)
```

> [!TIP]
> **Regla de oro:** Usa colecciones inmutables (`List`, `Set`, `Map`) por defecto. Solo usa las versiones mutables cuando realmente necesites modificar la colección. Esto hace tu código más seguro y predecible.

***

## 6. Control de flujo

### 6.1. `if` como expresión

En Java, `if` es una sentencia. En Kotlin, `if` es una **expresión** que devuelve un valor, eliminando la necesidad del operador ternario:

```kotlin
// Java ternario: condicion ? valorTrue : valorFalse
// En Kotlin no existe, usamos if como expresión

val edad = 17
val categoria = if (edad >= 18) "Adulto" else "Menor"
println(categoria)  // "Menor"

// If-else multi-línea
val nota = 7
val calificacion = if (nota >= 9) {
    println("¡Excelente!")
    "Sobresaliente"
} else if (nota >= 7) {
    println("Muy bien")
    "Notable"
} else if (nota >= 5) {
    "Aprobado"
} else {
    "Suspenso"
}
println(calificacion)  // "Notable"

// El último valor del bloque es el retornado
```


### 6.2. `when`: el super-switch

`when` reemplaza al `switch` de Java pero es muchísimo más potente:

#### When básico

```kotlin
val diaSemana = 3

when (diaSemana) {
    1 -> println("Lunes")
    2 -> println("Martes")
    3 -> println("Miércoles")
    4 -> println("Jueves")
    5 -> println("Viernes")
    6, 7 -> println("Fin de semana")  // Múltiples valores
    else -> println("Día inválido")
}
```


#### When como expresión

```kotlin
val nota = 7
val calificacion = when (nota) {
    0, 1, 2, 3, 4 -> "Suspenso"
    5, 6 -> "Aprobado"
    7, 8 -> "Notable"
    9, 10 -> "Sobresaliente"
    else -> "Nota inválida"
}
println(calificacion)  // "Notable"
```


#### When con rangos

```kotlin
val edad = 25

when (edad) {
    in 0..12 -> println("Niño")
    in 13..17 -> println("Adolescente")
    in 18..64 -> println("Adulto")
    in 65..120 -> println("Adulto mayor")
    else -> println("Edad inválida")
}
```


#### When sin argumento (como múltiples if)

```kotlin
val temperatura = 28
val humedad = 80

when {
    temperatura > 35 -> println("Calor extremo")
    temperatura > 30 && humedad > 70 -> println("Calor húmedo")
    temperatura > 25 -> println("Calor agradable")
    temperatura > 15 -> println("Templado")
    else -> println("Frío")
}
```


#### When con is (type checking)

```kotlin
fun describir(objeto: Any) {
    when (objeto) {
        is String -> println("Es un String de longitud ${objeto.length}")
        is Int -> println("Es un entero: $objeto")
        is Boolean -> println("Es un booleano")
        in 1..10 -> println("Es un número entre 1 y 10")
        else -> println("Tipo desconocido")
    }
}

describir("Kotlin")  // "Es un String de longitud 6"
describir(42)        // "Es un entero: 42"
describir(true)      // "Es un booleano"
```

> [!NOTE]
> Observa el **smart cast**: después de comprobar `is String`, Kotlin trata automáticamente `objeto` como String dentro de esa rama, permitiéndote acceder a `objeto.length` sin casting manual.

### 6.3. Rangos

Kotlin proporciona una forma concisa de trabajar con secuencias de valores:

```kotlin
// Rango inclusivo (cerrado)
val rango1 = 1..10  // 1, 2, 3, ..., 10
val rango2 = 'a'..'z'  // a, b, c, ..., z

// Rango exclusivo (hasta, sin incluir el final)
val rango3 = 1 until 10  // 1, 2, 3, ..., 9

// Rango descendente
val rango4 = 10 downTo 1  // 10, 9, 8, ..., 1

// Rango con paso
val rango5 = 0..20 step 5  // 0, 5, 10, 15, 20
val rango6 = 20 downTo 0 step 2  // 20, 18, 16, ..., 0

// Comprobar pertenencia
println(5 in 1..10)      // true
println(15 in 1..10)     // false
println(5 !in 1..10)     // false

// Iterar sobre rangos
for (i in 1..5) {
    print("$i ")  // 1 2 3 4 5
}
```


### 6.4. Bucles: `for`, `while`, `do-while`

#### For con rangos y colecciones

```kotlin
// Rango numérico
for (i in 1..5) {
    println(i)
}

// Rango exclusivo
for (i in 1 until 5) {
    println(i)  // 1, 2, 3, 4
}

// Con paso
for (i in 0..10 step 2) {
    println(i)  // 0, 2, 4, 6, 8, 10
}

// Descendente
for (i in 10 downTo 1 step 2) {
    println(i)  // 10, 8, 6, 4, 2
}

// Iterar lista
val frutas = listOf("Manzana", "Pera", "Naranja")
for (fruta in frutas) {
    println(fruta)
}

// Con índice
for ((index, fruta) in frutas.withIndex()) {
    println("$index: $fruta")
}

// Iterar mapa
val edades = mapOf("Ana" to 25, "Luis" to 30)
for ((nombre, edad) in edades) {
    println("$nombre tiene $edad años")
}
```


#### While y do-while

```kotlin
// While (evalúa condición antes)
var contador = 0
while (contador < 5) {
    println(contador)
    contador++
}

// Do-while (evalúa después, al menos una iteración)
var numero = 0
do {
    println(numero)
    numero++
} while (numero < 5)
```


#### Break y continue

```kotlin
// Continue: salta a la siguiente iteración
for (i in 1..10) {
    if (i % 2 == 0) continue  // Salta pares
    println(i)  // Solo imprime impares
}

// Break: sale del bucle
for (i in 1..100) {
    if (i > 5) break
    println(i)
}

// Etiquetas para bucles anidados
outer@ for (i in 1..3) {
    for (j in 1..3) {
        if (i * j > 4) break@outer  // Sale del bucle externo
        println("$i x $j = ${i * j}")
    }
}
```


***

## 7. Funciones básicas

### 7.1. Declaración y sintaxis

```kotlin
// Sintaxis básica: fun nombre(parámetro: Tipo): TipoRetorno { ... }

fun sumar(a: Int, b: Int): Int {
    return a + b
}

val resultado = sumar(5, 3)
println(resultado)  // 8

// Función sin retorno (Unit es equivalente a void, y es opcional)
fun saludar(nombre: String) {
    println("Hola, $nombre")
}

// Equivalente explícito
fun saludar2(nombre: String): Unit {
    println("Hola, $nombre")
}
```


### 7.2. Funciones de expresión única

Cuando una función solo devuelve el resultado de una expresión, se puede simplificar:

```kotlin
// Forma tradicional
fun multiplicar(a: Int, b: Int): Int {
    return a * b
}

// Forma simplificada (single-expression function)
fun multiplicar2(a: Int, b: Int): Int = a * b

// Con inferencia de tipo (más conciso aún)
fun multiplicar3(a: Int, b: Int) = a * b

// Ejemplos más complejos
fun esPositivo(numero: Int) = numero > 0

fun maximo(a: Int, b: Int) = if (a > b) a else b

fun cuadrado(x: Int) = x * x
```


### 7.3. Parámetros con valores por defecto

Elimina la necesidad de sobrecarga de métodos:

```kotlin
fun conectar(
    host: String,
    puerto: Int = 8080,
    timeout: Int = 5000,
    ssl: Boolean = false
) {
    println("Conectando a $host:$puerto (timeout: $timeout, ssl: $ssl)")
}

// Llamadas con diferentes combinaciones
conectar("localhost")
// "Conectando a localhost:8080 (timeout: 5000, ssl: false)"

conectar("localhost", 9000)
// "Conectando a localhost:9000 (timeout: 5000, ssl: false)"

conectar("localhost", 9000, 10000)
// "Conectando a localhost:9000 (timeout: 10000, ssl: false)"
```


### 7.4. Parámetros nombrados

Mejoran la legibilidad al llamar funciones con muchos parámetros:

```kotlin
fun crearUsuario(
    nombre: String,
    email: String,
    edad: Int,
    premium: Boolean
) {
    println("Usuario: $nombre ($email), $edad años, Premium: $premium")
}

// Sin nombres (confuso)
crearUsuario("Ana", "ana@example.com", 25, true)

// Con nombres (muy claro)
crearUsuario(
    nombre = "Ana",
    email = "ana@example.com",
    edad = 25,
    premium = true
)

// Combinar con valores por defecto (orden arbitrario)
fun reservar(
    personas: Int,
    fecha: String = "Hoy",
    hora: String = "20:00",
    fumador: Boolean = false
) { }

reservar(personas = 4, hora = "21:00")  // Salta fecha y fumador
reservar(personas = 2, fumador = true, fecha = "Mañana")  // Orden diferente
```


### 7.5. Funciones vararg

Permiten pasar un número variable de argumentos:

```kotlin
fun sumarTodos(vararg numeros: Int): Int {
    var suma = 0
    for (numero in numeros) {
        suma += numero
    }
    return suma
}

// Llamadas con diferentes cantidades de argumentos
println(sumarTodos(1, 2, 3))           // 6
println(sumarTodos(5, 10, 15, 20))     // 50
println(sumarTodos(100))               // 100

// Spread operator (*) para pasar array
val numerosArray = intArrayOf(1, 2, 3, 4, 5)
println(sumarTodos(*numerosArray))     // 15

// Combinar vararg con parámetros normales
fun formatear(prefijo: String, vararg palabras: String, sufijo: String = "."): String {
    return prefijo + palabras.joinToString(" ") + sufijo
}

println(formatear("Lista:", "Uno", "Dos", "Tres"))
// "Lista: Uno Dos Tres."
```


### 7.6. Funciones locales

Puedes definir funciones dentro de otras funciones para encapsular lógica:

```kotlin
fun calcularEstadisticas(numeros: List<Int>) {
    // Función local (solo visible dentro de calcularEstadisticas)
    fun validar() {
        if (numeros.isEmpty()) {
            throw IllegalArgumentException("La lista no puede estar vacía")
        }
    }
    
    validar()  // Llamada a función local
    
    val suma = numeros.sum()
    val media = suma.toDouble() / numeros.size
    val maximo = numeros.maxOrNull() ?: 0
    
    println("Suma: $suma, Media: $media, Máximo: $maximo")
}

calcularEstadisticas(listOf(5, 10, 15, 20))
// Suma: 50, Media: 12.5, Máximo: 20
```


***

## 8. Programación orientada a objetos

### 8.1. Clases básicas

```kotlin
// Clase simple
class Persona {
    var nombre: String = ""
    var edad: Int = 0
}

val persona = Persona()
persona.nombre = "Ana"
persona.edad = 25
println("${persona.nombre} tiene ${persona.edad} años")
```


### 8.2. Propiedades y backing fields

En Kotlin, las propiedades reemplazan a los getters/setters de Java:

```kotlin
class Rectangulo {
    var ancho: Double = 0.0
        get() = field  // Getter por defecto
        set(value) {   // Setter personalizado
            field = if (value > 0) value else 0.0
        }
    
    var alto: Double = 0.0
    
    // Propiedad computada (sin backing field)
    val area: Double
        get() = ancho * alto
    
    // Propiedad con setter privado
    var perimetro: Double = 0.0
        private set
}

val rect = Rectangulo()
rect.ancho = 10.0
rect.alto = 5.0
println(rect.area)  // 50.0 (calculado dinámicamente)

rect.ancho = -5.0  // El setter lo convierte a 0.0
println(rect.ancho)  // 0.0
```

**Equivalencia con Java:**

```java
// Java (verboso)
class Rectangulo {
    private double ancho;
    private double alto;
    
    public double getAncho() { return ancho; }
    public void setAncho(double ancho) {
        this.ancho = ancho > 0 ? ancho : 0;
    }
    public double getAlto() { return alto; }
    public void setAlto(double alto) { this.alto = alto; }
    public double getArea() { return ancho * alto; }
}

// Kotlin (conciso)
class Rectangulo {
    var ancho: Double = 0.0
        set(value) { field = if (value > 0) value else 0.0 }
    var alto: Double = 0.0
    val area: Double
        get() = ancho * alto
}
```


### 8.3. Constructores: primario y secundarios

#### Constructor primario

El constructor primario forma parte de la cabecera de la clase:

```kotlin
// Constructor primario con parámetros
class Persona(val nombre: String, var edad: Int) {
    // Al usar val/var en el constructor, las propiedades se crean automáticamente
    
    // Bloque init se ejecuta al crear la instancia
    init {
        println("Persona creada: $nombre, $edad años")
    }
}

val persona = Persona("Ana", 25)
// Imprime: "Persona creada: Ana, 25 años"

println(persona.nombre)  // "Ana"
persona.edad = 26  // Modificable porque es var
```


#### Constructores secundarios

```kotlin
class Persona(val nombre: String, var edad: Int) {
    var email: String = ""
    
    // Constructor secundario (debe llamar al primario)
    constructor(nombre: String, edad: Int, email: String) : this(nombre, edad) {
        this.email = email
        println("Email configurado: $email")
    }
}

val persona1 = Persona("Ana", 25)
val persona2 = Persona("Luis", 30, "luis@example.com")
```


#### Parámetros con valores por defecto (elimina necesidad de múltiples constructores)

```kotlin
class Persona(
    val nombre: String,
    var edad: Int = 0,
    var email: String = "",
    var premium: Boolean = false
) {
    init {
        println("Usuario $nombre creado")
    }
}

// Diferentes formas de instanciar
val p1 = Persona("Ana")
val p2 = Persona("Luis", 30)
val p3 = Persona("María", 28, "maria@example.com")
val p4 = Persona(nombre = "Carlos", edad = 35, premium = true)
```


### 8.4. Herencia

Por defecto, todas las clases en Kotlin son **`final`** (no pueden heredarse). Para permitir herencia, deben marcarse como `open`:

```kotlin
// Clase base (open permite herencia)
open class Vehiculo(val marca: String, val velocidadMaxima: Int) {
    // Método open puede sobrescribirse
    open fun describir() {
        println("Vehículo $marca, velocidad máxima: $velocidadMaxima km/h")
    }
    
    // Método sin open NO puede sobrescribirse
    fun arrancar() {
        println("Arrancando...")
    }
}

// Clase derivada
class Coche(
    marca: String,
    velocidadMaxima: Int,
    val numeroPuertas: Int
) : Vehiculo(marca, velocidadMaxima) {
    
    init {
        println("Coche con $numeroPuertas puertas")
    }
}

val coche = Coche("Toyota", 180, 4)
coche.describir()
// Vehículo Toyota, velocidad máxima: 180 km/h
```


### 8.5. Sobrescritura de métodos

```kotlin
open class Animal(val nombre: String) {
    open fun hacerSonido() {
        println("$nombre hace algún sonido")
    }
}

class Perro(nombre: String) : Animal(nombre) {
    override fun hacerSonido() {
        println("$nombre ladra: Guau guau")
    }
}

class Gato(nombre: String) : Animal(nombre) {
    override fun hacerSonido() {
        println("$nombre maúlla: Miau miau")
    }
}

val animales = listOf(
    Perro("Rex"),
    Gato("Mishi"),
    Perro("Bobby")
)

for (animal in animales) {
    animal.hacerSonido()
}
// Rex ladra: Guau guau
// Mishi maúlla: Miau miau
// Bobby ladra: Guau guau
```


#### Prevenir sobrescritura adicional

```kotlin
open class A {
    open fun metodo() { }
}

open class B : A() {
    final override fun metodo() { }  // No puede sobrescribirse más
}

class C : B() {
    // override fun metodo() { }  // ❌ ERROR: 'metodo' in 'B' is final
}
```


### 8.6. Clases abstractas

Las clases abstractas no pueden instanciarse y pueden contener métodos abstractos:

```kotlin
abstract class Figura {
    abstract val nombre: String
    abstract fun calcularArea(): Double
    
    // Método concreto (con implementación)
    fun describir() {
        println("Figura: $nombre, Área: ${calcularArea()}")
    }
}

class Circulo(val radio: Double) : Figura() {
    override val nombre = "Círculo"
    
    override fun calcularArea(): Double {
        return Math.PI * radio * radio
    }
}

class Cuadrado(val lado: Double) : Figura() {
    override val nombre = "Cuadrado"
    
    override fun calcularArea(): Double {
        return lado * lado
    }
}

val figuras: List<Figura> = listOf(
    Circulo(5.0),
    Cuadrado(4.0)
)

for (figura in figuras) {
    figura.describir()
}
// Figura: Círculo, Área: 78.53981633974483
// Figura: Cuadrado, Área: 16.0
```


### 8.7. Interfaces

Las interfaces definen contratos que las clases deben cumplir:

```kotlin
interface Volador {
    val altitudMaxima: Double  // Propiedad abstracta
    
    fun volar()
    fun aterrizar()
    
    // Método con implementación por defecto
    fun descripcion() {
        println("Puedo volar hasta $altitudMaxima metros")
    }
}

interface Nadador {
    fun nadar()
}

// Una clase puede implementar múltiples interfaces
class Pato : Volador, Nadador {
    override val altitudMaxima = 100.0
    
    override fun volar() {
        println("El pato vuela")
    }
    
    override fun aterrizar() {
        println("El pato aterriza")
    }
    
    override fun nadar() {
        println("El pato nada")
    }
}

val pato = Pato()
pato.volar()
pato.nadar()
pato.descripcion()
```


#### Resolver conflictos de múltiples interfaces

```kotlin
interface A {
    fun metodo() {
        println("A")
    }
}

interface B {
    fun metodo() {
        println("B")
    }
}

class C : A, B {
    override fun metodo() {
        super<A>.metodo()  // Llama a A
        super<B>.metodo()  // Llama a B
        println("C")
    }
}

val c = C()
c.metodo()
// A
// B
// C
```


### 8.8. Data classes

Las data classes son perfectas para clases que solo almacenan datos. El compilador genera automáticamente:

- `equals()` y `hashCode()`
- `toString()`
- `copy()`
- `componentN()` para destructuring

```kotlin
// Una línea genera toda la funcionalidad
data class Usuario(val id: Int, val nombre: String, val email: String)

val usuario1 = Usuario(1, "Ana", "ana@example.com")
val usuario2 = Usuario(1, "Ana", "ana@example.com")
val usuario3 = Usuario(2, "Luis", "luis@example.com")

// toString automático
println(usuario1)
// Usuario(id=1, nombre=Ana, email=ana@example.com)

// equals automático (compara por contenido)
println(usuario1 == usuario2)  // true (mismo contenido)
println(usuario1 === usuario2) // false (diferentes objetos)
println(usuario1 == usuario3)  // false (diferente contenido)

// copy (crear copia modificada)
val usuario4 = usuario1.copy(email = "ana.nueva@example.com")
println(usuario4)
// Usuario(id=1, nombre=Ana, email=ana.nueva@example.com)

// Destructuring
val (id, nombre, email) = usuario1
println("ID: $id, Nombre: $nombre, Email: $email")
```

**Equivalencia con Java:**

```java
// Java (decenas de líneas)
public class Usuario {
    private final int id;
    private final String nombre;
    private final String email;
    
    public Usuario(int id, String nombre, String email) {
        this.id = id;
        this.nombre = nombre;
        this.email = email;
    }
    
    // ... getters, equals, hashCode, toString (más de 30 líneas)
}

// Kotlin (una línea)
data class Usuario(val id: Int, val nombre: String, val email: String)
```


### 8.9. Sealed classes

Las sealed classes representan jerarquías restringidas. Todas las subclases deben estar en el mismo archivo:

```kotlin
sealed class Resultado
data class Exito(val datos: String) : Resultado()
data class Error(val mensaje: String) : Resultado()
object Cargando : Resultado()

fun manejarResultado(resultado: Resultado) {
    when (resultado) {
        is Exito -> println("Datos: ${resultado.datos}")
        is Error -> println("Error: ${resultado.mensaje}")
        Cargando -> println("Cargando...")
        // No necesita 'else' porque el compilador sabe que están todos los casos
    }
}

// Uso
manejarResultado(Exito("Datos del servidor"))
manejarResultado(Error("Conexión fallida"))
manejarResultado(Cargando)
```

**Caso de uso real: estados de UI**

```kotlin
sealed class EstadoPantalla {
    object Cargando : EstadoPantalla()
    data class Exito(val usuarios: List<String>) : EstadoPantalla()
    data class Error(val mensaje: String) : EstadoPantalla()
}

fun renderizar(estado: EstadoPantalla) {
    when (estado) {
        EstadoPantalla.Cargando -> {
            println("Mostrando spinner...")
        }
        is EstadoPantalla.Exito -> {
            println("Mostrando ${estado.usuarios.size} usuarios")
        }
        is EstadoPantalla.Error -> {
            println("Error: ${estado.mensaje}")
        }
    }
}
```


### 8.10. Enum classes

```kotlin
enum class DiaSemana {
    LUNES, MARTES, MIERCOLES, JUEVES, VIERNES, SABADO, DOMINGO
}

val hoy = DiaSemana.MIERCOLES
println(hoy)  // MIERCOLES

// Enums con propiedades
enum class Color(val rgb: Int) {
    ROJO(0xFF0000),
    VERDE(0x00FF00),
    AZUL(0x0000FF)
}

println(Color.ROJO.rgb)  // 16711680 (0xFF0000 en decimal)

// Enums con métodos
enum class Operacion {
    SUMA {
        override fun aplicar(a: Int, b: Int) = a + b
    },
    RESTA {
        override fun aplicar(a: Int, b: Int) = a - b
    },
    MULTIPLICACION {
        override fun aplicar(a: Int, b: Int) = a * b
    },
    DIVISION {
        override fun aplicar(a: Int, b: Int) = if (b != 0) a / b else 0
    };
    
    abstract fun aplicar(a: Int, b: Int): Int
}

println(Operacion.SUMA.aplicar(5, 3))  // 8

// Iterar sobre valores de enum
for (dia in DiaSemana.values()) {
    println(dia)
}
```


***

## 9. Object y companion object

### 9.1. Object declarations (singleton)

`object` define una clase y crea una única instancia automáticamente:

```kotlin
object BaseDatos {
    private var conexiones = 0
    
    fun conectar() {
        conexiones++
        println("Conexión establecida. Total: $conexiones")
    }
    
    fun desconectar() {
        conexiones--
        println("Conexión cerrada. Total: $conexiones")
    }
}

// Uso directo (no se instancia con constructor)
BaseDatos.conectar()  // "Conexión establecida. Total: 1"
BaseDatos.conectar()  // "Conexión establecida. Total: 2"
BaseDatos.desconectar()  // "Conexión cerrada. Total: 1"
```


### 9.2. Companion objects

Kotlin no tiene `static`. En su lugar, usa `companion object` para miembros asociados a la clase (no a instancias):

```kotlin
class Matematica {
    companion object {
        const val PI = 3.141592653589793
        const val E = 2.718281828459045
        
        fun factorial(n: Int): Int {
            return if (n <= 1) 1 else n * factorial(n - 1)
        }
    }
}

// Acceso como si fueran static
println(Matematica.PI)  // 3.141592653589793
println(Matematica.factorial(5))  // 120

// No necesitas instanciar Matematica
```

**Factory pattern con companion object:**

```kotlin
class Usuario private constructor(
    val id: Int,
    val nombre: String,
    val email: String
) {
    companion object {
        fun crearDesdeJson(json: String): Usuario {
            // Parseo simplificado
            return Usuario(1, "Ana", "ana@example.com")
        }
        
        fun crearAdministrador(nombre: String): Usuario {
            return Usuario(0, nombre, "$nombre@admin.com")
        }
    }
}

val usuario1 = Usuario.crearDesdeJson("{...}")
val admin = Usuario.crearAdministrador("SuperAdmin")
```


### 9.3. Object expressions (clases anónimas)

Equivalente a clases anónimas en Java:

```kotlin
interface ClickListener {
    fun onClick()
}

fun configurarBoton() {
    val listener = object : ClickListener {
        override fun onClick() {
            println("Botón clickeado")
        }
    }
    
    // Usar listener...
}

// Object expression con múltiples interfaces
val multiListener = object : ClickListener, Runnable {
    override fun onClick() {
        println("Click")
    }
    
    override fun run() {
        println("Run")
    }
}
```


***

## 10. Extensions

Las extensions permiten añadir funcionalidad a clases existentes sin modificarlas ni heredar:

### 10.1. Funciones de extensión

```kotlin
// Extender la clase String
fun String.esPalindromo(): Boolean {
    val limpio = this.lowercase().replace(" ", "")
    return limpio == limpio.reversed()
}

println("radar".esPalindromo())  // true
println("kotlin".esPalindromo())  // false
println("A man a plan a canal Panama".lowercase().replace(" ", "").esPalindromo())  // true

// Extender tipos numéricos
fun Int.esPar() = this % 2 == 0
fun Int.esImpar() = this % 2 != 0

println(4.esPar())   // true
println(7.esImpar()) // true

// Extension más compleja
fun String.truncar(longitud: Int, sufijo: String = "..."): String {
    return if (this.length <= longitud) {
        this
    } else {
        this.substring(0, longitud) + sufijo
    }
}

println("Este es un texto muy largo".truncar(10))  // "Este es un..."
```


### 10.2. Propiedades de extensión

```kotlin
val String.ultimoCaracter: Char
    get() = this[this.length - 1]

val String.primeraMayuscula: String
    get() = this.replaceFirstChar { it.uppercase() }

println("kotlin".ultimoCaracter)  // 'n'
println("kotlin".primeraMayuscula)  // "Kotlin"

// Extension property para colecciones
val <T> List<T>.penultimo: T?
    get() = if (this.size >= 2) this[this.size - 2] else null

println(listOf(1, 2, 3, 4, 5).penultimo)  // 4
```


### 10.3. Extensions sobre tipos nullable

```kotlin
fun String?.esNullOVacio(): Boolean {
    return this == null || this.isEmpty()
}

val texto1: String? = null
val texto2: String? = ""
val texto3: String? = "Kotlin"

println(texto1.esNullOVacio())  // true
println(texto2.esNullOVacio())  // true
println(texto3.esNullOVacio())  // false
```

> [!TIP]
> Las extensions son perfectas para añadir utilidades específicas de tu dominio a tipos de la biblioteca estándar sin contaminar el código con clases helper estáticas.

***

## 11. Modificadores de visibilidad

### 11.1. Niveles de acceso

Kotlin tiene cuatro modificadores de visibilidad:

```kotlin
// public - Visible en todas partes (por defecto)
public class ClasePublica {
    public val propiedadPublica = "Visible en todas partes"
}

// internal - Visible solo dentro del mismo módulo
internal class ClaseInterna {
    internal val propiedadInterna = "Solo en este módulo"
}

// protected - Visible en la clase y subclases
open class Base {
    protected val protegido = "Visible en subclases"
    
    protected fun metodoProtegido() {
        println("Solo accesible desde subclases")
    }
}

class Derivada : Base() {
    fun acceder() {
        println(protegido)  // ✅ OK
        metodoProtegido()   // ✅ OK
    }
}

// private - Visible solo en la declaración
class ConPrivados {
    private val secreto = "No accesible fuera"
    
    private fun metodoPrivado() {
        println(secreto)  // ✅ OK aquí
    }
    
    fun metodoPublico() {
        metodoPrivado()  // ✅ OK (mismo contexto)
    }
}

val obj = ConPrivados()
// println(obj.secreto)  // ❌ ERROR: Cannot access 'secreto'
```


### 11.2. Comparación con Java

| Modificador | Java | Kotlin |
| :-- | :-- | :-- |
| **public** | Accesible desde cualquier lugar | Accesible desde cualquier lugar (default) |
| **protected** | Mismo paquete + subclases | Solo subclases (más restrictivo) |
| **default** (sin modificador) | Mismo paquete | No existe (usa internal) |
| **private** | Solo dentro de la clase | Solo dentro de la declaración |
| **internal** | No existe | Mismo módulo (JAR/proyecto) |


***

## 12. Comparación final: Kotlin vs Java

| Concepto | Java | Kotlin |
| :-- | :-- | :-- |
| **Punto y coma** | Obligatorio | Opcional (no se usa) |
| **Variables** | `type name` | `val`/`var name: Type` |
| **Inferencia** | Limitada (`var` desde Java 10) | Completa |
| **Null safety** | No integrada | Sistema de tipos nullable/non-nullable |
| **String templates** | `String.format()` o concatenación | `"Valor: $variable"` |
| **Igualdad** | `==` (referencia), `.equals()` (contenido) | `==` (contenido), `===` (referencia) |
| **Getters/Setters** | Explícitos | Automáticos (properties) |
| **Data classes** | Mucho código manual | `data class` (una línea) |
| **Constructor** | `new ClassName()` | `ClassName()` (sin `new`) |
| **Herencia** | Todas las clases heredables | Todas `final` por defecto (`open`) |
| **Static** | `static` keyword | `companion object` |
| **Switch** | `switch` con `break` | `when` sin `break` |
| **Operador ternario** | `cond ? a : b` | `if (cond) a else b` |
| **Checked exceptions** | Obligatorias | No existen |
| **Extensions** | No soportadas | Soportadas nativamente |
| **Funciones top-level** | No (solo métodos) | Sí (fuera de clases) |
| **Smart casts** | No | Sí (automático tras type check) |
| **Ranges** | No nativos | `1..10`, `'a'..'z'` |


***

## 13. Actividades prácticas

### Actividad 1: Null safety en acción

```kotlin
// Completa las siguientes funciones usando diferentes técnicas de null safety

// 1. Usando safe call (?.)
fun obtenerLongitud(texto: String?): Int {
    // Devuelve la longitud o 0 si es null
    // TU CÓDIGO AQUÍ
}

// 2. Usando Elvis (?:)
fun saludar(nombre: String?): String {
    // Devuelve "Hola, [nombre]" o "Hola, Invitado"
    // TU CÓDIGO AQUÍ
}

// 3. Usando let
fun procesarEmail(email: String?) {
    // Imprime el email en mayúsculas solo si no es null
    // TU CÓDIGO AQUÍ
}

// 4. Combinación de operadores
data class Usuario(val nombre: String, val email: String?)
data class Perfil(val usuario: Usuario?)

fun obtenerEmail(perfil: Perfil?): String {
    // Devuelve el email del usuario o "Sin email"
    // TU CÓDIGO AQUÍ
}

// Pruebas
println(obtenerLongitud("Kotlin"))  // 6
println(obtenerLongitud(null))      // 0
println(saludar("Ana"))             // "Hola, Ana"
println(saludar(null))              // "Hola, Invitado"
procesarEmail("test@example.com")   // "TEST@EXAMPLE.COM"
procesarEmail(null)                 // No imprime nada
```


***

### Actividad 2: Data classes y copy

```kotlin
// Define una data class para representar un producto
data class Producto(
    val id: Int,
    val nombre: String,
    var precio: Double,
    val disponible: Boolean = true
)

// Crea algunos productos
val producto1 = Producto(1, "Laptop", 999.99)
val producto2 = Producto(2, "Mouse", 29.99, false)

// Usa copy para crear variantes
// TU CÓDIGO AQUÍ: Crea producto3 = producto1 con 10% descuento
// TU CÓDIGO AQUÍ: Crea producto4 = producto2 pero disponible

// Destructuring
// TU CÓDIGO AQUÍ: Extrae id, nombre y precio de producto1

// Colección de productos
val productos = listOf(producto1, producto2)
// TU CÓDIGO AQUÍ: Imprime todos los productos
```


***

### Actividad 3: Sealed classes para estados

```kotlin
// Crea un sistema de gestión de pedidos usando sealed classes

sealed class EstadoPedido {
    // Completa los estados:
    // - Pendiente (sin datos adicionales)
    // - EnProceso (con fecha de inicio: String)
    // - Enviado (con código de seguimiento: String)
    // - Entregado (con fecha de entrega: String)
    // - Cancelado (con motivo: String)
}

fun procesarPedido(estado: EstadoPedido) {
    when (estado) {
        // TU CÓDIGO AQUÍ: Maneja cada estado imprimiendo información relevante
    }
}

// Pruebas
procesarPedido(EstadoPedido.Pendiente)
procesarPedido(EstadoPedido.EnProceso("2025-01-01"))
procesarPedido(EstadoPedido.Enviado("TRACK123"))
procesarPedido(EstadoPedido.Cancelado("Cliente cambió de opinión"))
```


***

### Actividad 4: Extensions útiles

```kotlin
// Crea las siguientes extensions

// 1. Extension para Int que devuelva el factorial
fun Int.factorial(): Long {
    // TU CÓDIGO AQUÍ
}

// 2. Extension para String que cuente vocales
fun String.contarVocales(): Int {
    // TU CÓDIGO AQUÍ
}

// 3. Extension para List<Int> que devuelva solo los pares
fun List<Int>.solosPares(): List<Int> {
    // TU CÓDIGO AQUÍ
}

// 4. Extension property para String que devuelva si es un email válido (contiene @)
val String.esEmailValido: Boolean
    get() = // TU CÓDIGO AQUÍ

// Pruebas
println(5.factorial())  // 120
println("Kotlin".contarVocales())  // 2
println(listOf(1, 2, 3, 4, 5).solosPares())  // [2, 4]
println("test@example.com".esEmailValido)  // true
println("testexample.com".esEmailValido)  // false
```


***

### Actividad 5: Sistema de biblioteca completo

```kotlin
// Crea un sistema de gestión de biblioteca

// 1. Data class para Material
data class Material(
    val id: Int,
    val titulo: String,
    val tipo: TipoMaterial,
    var disponible: Boolean = true
)

// 2. Enum para tipos
enum class TipoMaterial {
    LIBRO, REVISTA, DVD, AUDIOLIBRO
}

// 3. Sealed class para resultado de operaciones
sealed class ResultadoOperacion {
    data class Exito(val mensaje: String) : ResultadoOperacion()
    data class Error(val mensaje: String) : ResultadoOperacion()
}

// 4. Clase Biblioteca
class Biblioteca {
    private val materiales = mutableListOf<Material>()
    
    fun agregarMaterial(material: Material): ResultadoOperacion {
        // TU CÓDIGO AQUÍ
    }
    
    fun prestar(id: Int): ResultadoOperacion {
        // TU CÓDIGO AQUÍ: Busca el material y márcalo como no disponible
    }
    
    fun devolver(id: Int): ResultadoOperacion {
        // TU CÓDIGO AQUÍ: Busca el material y márcalo como disponible
    }
    
    fun buscarPorTitulo(titulo: String): Material? {
        // TU CÓDIGO AQUÍ
    }
    
    fun listarDisponibles(): List<Material> {
        // TU CÓDIGO AQUÍ
    }
    
    fun estadisticasPorTipo(): Map<TipoMaterial, Int> {
        // TU CÓDIGO AQUÍ: Cuenta cuántos materiales hay de cada tipo
    }
}

// Companion object con materiales de ejemplo
companion object {
    fun crearBibliotecaEjemplo(): Biblioteca {
        // TU CÓDIGO AQUÍ: Crea una biblioteca con materiales de prueba
    }
}

// Prueba el sistema
fun main() {
    val biblioteca = Biblioteca.crearBibliotecaEjemplo()
    
    when (val resultado = biblioteca.prestar(1)) {
        is ResultadoOperacion.Exito -> println(resultado.mensaje)
        is ResultadoOperacion.Error -> println("Error: ${resultado.mensaje}")
    }
    
    println("Disponibles: ${biblioteca.listarDisponibles()}")
    println("Estadísticas: ${biblioteca.estadisticasPorTipo()}")
}
```


***

## 14. Resumen del tema

Has aprendido los fundamentos de Kotlin necesarios para el desarrollo Android moderno:

✅ **Sintaxis básica:** val/var, inferencia de tipos, string templates
✅ **Null safety:** Sistema de tipos nullable/non-nullable, operadores ?., ?:, !!
✅ **Colecciones básicas:** List, Set, Map (inmutables y mutables)
✅ **Control de flujo:** if como expresión, when potente, rangos
✅ **Funciones:** Parámetros por defecto, nombrados, expresiones únicas
✅ **POO:** Clases, propiedades, constructores, herencia, polimorfismo, interfaces
✅ **Clases especiales:** Data classes, sealed classes, enum classes
✅ **Object y companion object:** Singleton pattern, miembros de clase
✅ **Extensions:** Añadir funcionalidad a tipos existentes sin herencia
✅ **Modificadores de visibilidad:** public, internal, protected, private

Con esta base sólida en Kotlin, estás preparado para:

- Desarrollar aplicaciones Android modernas con el lenguaje preferido por Google
- Escribir código más seguro y conciso que en Java tradicional
- Aprovechar el null safety para eliminar la mayoría de NullPointerException
- Usar data classes y sealed classes para modelar datos de forma elegante
- Aplicar programación orientada a objetos con las mejoras de Kotlin


### Próximos pasos

En el **Tema 15** profundizarás en:

- **Programación funcional avanzada:** Lambdas, funciones de orden superior, closures
- **Operaciones sobre colecciones:** map, filter, reduce, flatMap, y toda la potencia del procesamiento funcional
- **Scope functions:** let, run, with, apply, also
- **Secuencias y evaluación perezosa:** Optimización de operaciones sobre colecciones grandes
- **Comparativa Kotlin vs Swift:** Similitudes y diferencias en programación funcional
- **Persistencia moderna:** Room para Android con Flow y corrutinas
- **Arquitecturas reactivas:** MVVM, Repository Pattern, Single Source of Truth


### Recursos adicionales para profundizar

**Documentación oficial:**

- **Kotlin Docs:** https://kotlinlang.org/docs/home.html - Documentación completa y actualizada
- **Android Developers Kotlin:** https://developer.android.com/kotlin - Guías específicas para Android
- **Kotlin Koans:** Ejercicios interactivos para practicar

**Práctica online:**

- **Kotlin Playground:** https://play.kotlinlang.org/ - Ejecuta código Kotlin en el navegador
- **Exercism Kotlin Track:** Ejercicios con mentoría de la comunidad
- **LeetCode / HackerRank:** Problemas algorítmicos en Kotlin

**Comunidad:**

- **Kotlin Slack:** kotlinlang.slack.com
- **Reddit r/Kotlin:** Comunidad activa de desarrolladores
- **Stack Overflow:** Etiqueta [kotlin] con miles de respuestas


### Diferencias clave con Swift (adelanto del Tema 15)

Ahora que conoces tanto Swift como Kotlin, algunas similitudes importantes:


| Característica | Swift | Kotlin |
| :-- | :-- | :-- |
| **Null safety** | Opcionales (`?`) | Nullable types (`?`) |
| **Inmutabilidad** | `let` vs `var` | `val` vs `var` |
| **Inferencia** | Completa | Completa |
| **Extensions** | Soportadas | Soportadas |
| **Pattern matching** | `switch` potente | `when` potente |
| **Funcional** | Closures | Lambdas |
| **Data classes** | Structs (valor) | Data classes (referencia JVM) |

Ambos lenguajes nacieron con objetivos similares: modernizar el desarrollo móvil eliminando las limitaciones de Objective-C y Java respectivamente. Por eso comparten tantas características progresistas.

***

## Notas finales para el alumno

**¿Por qué aprender Kotlin después de Java?**

1. **Productividad:** Escribes menos código para lograr lo mismo
2. **Seguridad:** El compilador te protege de errores comunes
3. **Modernidad:** Paradigmas modernos de programación integrados
4. **Demanda laboral:** Google lo recomienda oficialmente para Android
5. **Interoperabilidad:** Convive perfectamente con código Java existente

**Consejo de estudio:**

No intentes memorizar toda la sintaxis. Enfócate en:

1. **Entender los conceptos:** Null safety, inmutabilidad, expresiones vs sentencias
2. **Practicar con ejemplos pequeños:** Escribe código Kotlin todos los días
3. **Comparar con Java:** Cada vez que aprendas algo, piensa "¿cómo sería esto en Java?"
4. **Leer código real:** Explora proyectos open source en GitHub escritos en Kotlin
5. **Usar las herramientas:** Deja que Android Studio te ayude con autocompletado y sugerencias

## Autoevaluación

Completa este cuestionario para verificar tu comprensión de los fundamentos de Kotlin antes de continuar.

**1. ¿Cuál es la principal ventaja del sistema de null safety de Kotlin sobre Java?**
- a) Es más rápido en ejecución
- b) Distingue en tiempo de compilación entre tipos nullable y non-nullable
- c) Permite usar null sin restricciones
- d) No tiene ninguna ventaja

**2. ¿Qué devuelve esta expresión en Kotlin?**
```kotlin
val nombre: String? = null
val longitud = nombre?.length ?: 0
```
- a) null
- b) NullPointerException
- c) 0
- d) Error de compilación

**3. ¿Cuál es la diferencia entre `List` y `MutableList` en Kotlin?**
- a) No hay diferencia
- b) `List` es solo lectura, `MutableList` permite modificaciones
- c) `List` es más rápida
- d) `MutableList` no existe en Kotlin

**4. ¿Qué hace el operador Elvis (`?:`) en Kotlin?**
- a) Lanza una excepción
- b) Proporciona un valor por defecto cuando el operando izquierdo es null
- c) Convierte tipos
- d) Compara dos valores

**5. En Kotlin, `when` es equivalente a cuál estructura en Java?**
- a) if-else
- b) switch
- c) while
- d) for

**6. ¿Qué palabra clave se usa en Kotlin para declarar una constante inmutable?**
- a) const
- b) var
- c) val
- d) let

**7. ¿Cuál es el resultado de este código?**
```kotlin
val numeros = listOf(1, 2, 3)
val resultado = numeros.map { it * 2 }.sum()
```
- a) 6
- b) 12
- c)[2]
- d) Error de compilación

**8. ¿Qué tipo de clase en Kotlin genera automáticamente `equals()`, `hashCode()`, `toString()` y `copy()`?**
- a) abstract class
- b) open class
- c) data class
- d) sealed class

**9. ¿Cuál de estas afirmaciones sobre las funciones de extensión en Kotlin es CORRECTA?**
- a) Modifican la clase original
- b) Permiten añadir funcionalidad a clases existentes sin heredar
- c) Solo funcionan con clases propias
- d) Requieren acceso al código fuente original

**10. ¿Qué hace el operador `!!` en Kotlin?**
- a) Verifica si un valor es null de forma segura
- b) Fuerza el desempaquetado de un nullable, lanzando excepción si es null
- c) Duplica un valor
- d) Es un operador de negación lógica

**Respuestas correctas:** 1-b, 2-c, 3-b, 4-b, 5-b, 6-c, 7-b, 8-c, 9-b, 10-b

***
