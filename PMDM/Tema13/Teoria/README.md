# TEMA 13: Fundamentos del lenguaje Swift

## Índice

1. [Introducción y contextualización](#1-introducción-y-contextualización)
   1. [Filosofía del lenguaje](#11-filosofía-del-lenguaje)
   2. [Swift vs Java: diferencias arquitectónicas clave](#12-swift-vs-java-diferencias-arquitectónicas-clave)
   3. [Casos de uso de Swift](#13-casos-de-uso-de-swift)
2. [Entorno de desarrollo](#2-entorno-de-desarrollo)
   1. [Ecosistema oficial: macOS y Xcode](#21-ecosistema-oficial-macos-y-xcode)
   2. [Alternativas multiplataforma para Windows/Linux](#22-alternativas-multiplataforma-para-windowslinux)
3. [Sintaxis básica: variables, constantes y tipos](#3-sintaxis-básica-variables-constantes-y-tipos)
   1. [Declaración: `let` vs `var`](#31-declaración-let-vs-var)
   2. [Inferencia de tipos](#32-inferencia-de-tipos)
   3. [Tipos de datos fundamentales](#33-tipos-de-datos-fundamentales)
   4. [Interpolación de cadenas](#34-interpolación-de-cadenas)
   5. [Conversión entre tipos](#35-conversión-entre-tipos)
   6. [Literales numéricos y notaciones especiales](#36-literales-numéricos-y-notaciones-especiales)
4. [El sistema de tipos seguro: opcionales](#4-el-sistema-de-tipos-seguro-opcionales)
   1. [El problema en Java: null references](#41-el-problema-en-java-null-references)
   2. [La solución de Swift: opcionales explícitos](#42-la-solución-de-swift-opcionales-explícitos)
   3. [Representación conceptual](#43-representación-conceptual)
   4. [Desempaquetado forzado (`!`) - Uso desaconsejado](#44-desempaquetado-forzado---uso-desaconsejado)
   5. [Binding seguro con `if let` - Forma recomendada](#45-binding-seguro-con-if-let---forma-recomendada)
   6. [Guard statement: validación temprana](#46-guard-statement-validación-temprana)
   7. [Operador Nil Coalescing (`??`)](#47-operador-nil-coalescing-)
   8. [Optional chaining](#48-optional-chaining)
   9. [Implicitly Unwrapped Optionals (`String!`)](#49-implicitly-unwrapped-optionals-string)
   10. [Comparativa: gestión de nulos en Java vs Swift](#410-comparativa-gestión-de-nulos-en-java-vs-swift)
5. [Tuplas: agrupación ligera de valores](#5-tuplas-agrupación-ligera-de-valores)
   1. [Declaración y acceso básico](#51-declaración-y-acceso-básico)
   2. [Tuplas heterogéneas](#52-tuplas-heterogéneas)
   3. [Desestructuración (destructuring)](#53-desestructuración-destructuring)
   4. [Tuplas como valores de retorno](#54-tuplas-como-valores-de-retorno)
6. [Colecciones básicas](#6-colecciones-básicas)
   1. [Arrays (listas ordenadas)](#61-arrays-listas-ordenadas)
   2. [Diccionarios (mapas clave-valor)](#62-diccionarios-mapas-clave-valor)
   3. [Sets (conjuntos sin orden ni duplicados)](#63-sets-conjuntos-sin-orden-ni-duplicados)
   4. [Comparativa de colecciones](#64-comparativa-de-colecciones)
7. [Control de flujo](#7-control-de-flujo)
   1. [Condicionales: `if`, `else if`, `else`](#71-condicionales-if-else-if-else)
   2. [Switch: pattern matching avanzado](#72-switch-pattern-matching-avanzado)
   3. [Bucles](#73-bucles)
8. [Funciones básicas](#8-funciones-básicas)
   1. [Declaración básica](#81-declaración-básica)
   2. [Argument labels (etiquetas de argumentos)](#82-argument-labels-etiquetas-de-argumentos)
   3. [Parámetros con valores por defecto](#83-parámetros-con-valores-por-defecto)
   4. [Parámetros variádicos](#84-parámetros-variádicos)
   5. [Parámetros `inout` (paso por referencia)](#85-parámetros-inout-paso-por-referencia)
   6. [Funciones como tipos](#86-funciones-como-tipos)
   7. [Funciones de orden superior básicas](#87-funciones-de-orden-superior-básicas)
9. [Enumeraciones básicas](#9-enumeraciones-básicas)
   1. [Declaración básica](#91-declaración-básica)
   2. [Switch con enums](#92-switch-con-enums)
   3. [Raw values (valores asociados)](#93-raw-values-valores-asociados)
   4. [Associated values (valores asociados)](#94-associated-values-valores-asociados)
   5. [Métodos en enums](#95-métodos-en-enums)
10. [Programación orientada a objetos: estructuras y clases](#10-programación-orientada-a-objetos-estructuras-y-clases)
    1. [Estructuras (structs): tipos de valor](#101-estructuras-structs-tipos-de-valor)
    2. [Clases: tipos de referencia](#102-clases-tipos-de-referencia)
    3. [Herencia básica](#103-herencia-básica)
    4. [Polimorfismo](#104-polimorfismo)
    5. [Protocols básicos (interfaces)](#105-protocols-básicos-interfaces)
    6. [Propiedades y métodos de tipo (static/class)](#106-propiedades-y-métodos-de-tipo-staticclass)
    7. [¿Cuándo usar struct vs class?](#107-cuándo-usar-struct-vs-class)
11. [Extensions básicas](#11-extensions-básicas)
    1. [Extender tipos propios](#111-extender-tipos-propios)
    2. [Extender tipos de la biblioteca estándar](#112-extender-tipos-de-la-biblioteca-estándar)
    3. [Conformar a protocols mediante extensions](#113-conformar-a-protocols-mediante-extensions)
12. [Manejo de errores básico](#12-manejo-de-errores-básico)
    1. [Definir errores](#121-definir-errores)
    2. [Lanzar errores con `throw`](#122-lanzar-errores-con-throw)
    3. [Capturar errores con `do-catch`](#123-capturar-errores-con-do-catch)
    4. [Propagación de errores](#124-propagación-de-errores)
    5. [Alternativas: `try?` y `try!`](#125-alternativas-try-y-try)
    6. [Statement `defer`](#126-statement-defer)
13. [Modificadores de acceso](#13-modificadores-de-acceso)
    1. [Niveles de acceso](#131-niveles-de-acceso)
    2. [Ejemplo práctico](#132-ejemplo-práctico)
    3. [Guía de uso](#133-guía-de-uso)
14. [Comparación final: Swift vs Java](#14-comparación-final-swift-vs-java)
15. [Actividades prácticas](#15-actividades-prácticas)
16. [Recursos adicionales y siguientes pasos](#16-recursos-adicionales-y-siguientes-pasos)


## 1. Introducción y contextualización

Swift es un lenguaje de programación moderno, seguro y expresivo desarrollado por Apple e introducido en 2014 como sucesor de Objective-C. A diferencia de Java, que ejecuta código en una máquina virtual (JVM), Swift compila directamente a código máquina nativo utilizando la infraestructura LLVM (Low Level Virtual Machine), lo que le proporciona un rendimiento excepcional comparable a lenguajes como C++.

### 1.1. Filosofía del lenguaje

Swift fue diseñado siguiendo tres pilares fundamentales:

**Seguridad (Safety):** El sistema de tipos de Swift está diseñado para eliminar categorías enteras de errores comunes en programación. El compilador previene errores como accesos a memoria no válidos, desbordamientos de índices en arrays, y especialmente, el famoso `NullPointerException` que tanto conocéis de Java.

**Rendimiento (Speed):** Al compilar a código nativo optimizado y no depender de un garbage collector tradicional, Swift ofrece un rendimiento comparable a C++. Utiliza ARC (Automatic Reference Counting) para gestionar la memoria de forma automática pero predecible.

**Expresividad (Expressiveness):** Swift combina lo mejor de múltiples paradigmas de programación. Su sintaxis es limpia, concisa y moderna, permitiendo escribir código que se lee casi como lenguaje natural en inglés.

### 1.2. Swift vs Java: diferencias arquitectónicas clave

| Aspecto | Java | Swift |
| :-- | :-- | :-- |
| **Ejecución** | Bytecode en JVM (multiplataforma) | Código nativo (específico de arquitectura) |
| **Gestión de memoria** | Garbage Collection (no determinista) | ARC - Automatic Reference Counting (determinista) |
| **Tipos primitivos** | `int`, `boolean`, etc. (no son objetos) | Todo son tipos de primera clase (structs o classes) |
| **Null safety** | Cualquier objeto puede ser `null` | Sistema de opcionales explícito (`?`) |
| **Paradigma** | Orientado a Objetos principalmente | Multi-paradigma (OOP + Funcional + Protocolo) |
| **Mutabilidad por defecto** | Variables mutables (`final` opcional) | Inmutabilidad preferida (`let` vs `var`) |

> [!IMPORTANT]
> El cambio más significativo al pasar de Java a Swift es el **sistema de gestión de opcionales**. En Java, cualquier variable puede ser `null` en cualquier momento. En Swift, debes declarar explícitamente si una variable puede no tener valor, y el compilador te obligará a manejarlo de forma segura.

### 1.3. Casos de uso de Swift

Aunque Swift nació para desarrollo iOS/macOS, actualmente se utiliza en:

- **Desarrollo iOS/iPadOS:** Apps móviles nativas para dispositivos Apple
- **Desarrollo macOS:** Aplicaciones de escritorio
- **Desarrollo de servidores:** Frameworks como Vapor o Kitura
- **Scripting del sistema:** Swift puede usarse como lenguaje de scripting
- **Multiplataforma:** Con SwiftUI, código compartido entre iOS, macOS, watchOS y tvOS

***

## 2. Entorno de desarrollo

### 2.1. Ecosistema oficial: macOS y Xcode

El entorno natural de Swift es **Xcode**, el IDE oficial de Apple disponible solo para macOS. Xcode incluye:

- Compilador Swift integrado
- Interface Builder (diseño visual de interfaces)
- Simuladores de iOS y otros dispositivos
- Debugger y herramientas de análisis de rendimiento
- Swift Playgrounds (entorno interactivo de aprendizaje)


### 2.2. Alternativas multiplataforma para Windows/Linux

Dado que en el aula trabajamos en equipos Windows, utilizaremos alternativas online o multiplataforma:

**Opciones recomendadas:**

1. **Swift Playgrounds Online** (https://online.swiftplayground.run/)
2. **Replit** (https://replit.com/) - Soporta Swift y permite proyectos completos
3. **SwiftFiddle** (https://swiftfiddle.com/) - Ideal para pruebas rápidas
4. **Visual Studio Code** + Swift for Windows (instalación local)

> [!TIP]
> Para seguir este tema, se recomienda abrir uno de estos entornos online en paralelo para probar todos los ejemplos de código. La mejor manera de aprender Swift es escribiendo y ejecutando código real.

***

## 3. Sintaxis básica: variables, constantes y tipos

### 3.1. Declaración: `let` vs `var`

En Java, la tendencia natural es declarar todo como variable mutable. Swift invierte esta filosofía: **la inmutabilidad es el comportamiento por defecto**.

```swift
// CONSTANTE (inmutable) - Usa esto por defecto
let nombre = "Juan"
let edad = 25

// Si intentamos cambiar el valor
// nombre = "Pedro"  // ❌ ERROR: Cannot assign to value: 'nombre' is a 'let' constant

// VARIABLE (mutable) - Solo cuando realmente necesites cambiar el valor
var saldo = 100.0
saldo = 150.0  // ✅ Correcto
saldo += 50.0  // ✅ Correcto
```

**Equivalencias con Java:**

```swift
// Java
final int edad = 25;  // Constante

// Swift
let edad = 25  // Constante (más conciso)
```

> [!NOTE]
> **Buena práctica de Swift:** Siempre empieza declarando con `let`. Si más adelante el compilador se queja porque necesitas modificar el valor, entonces y solo entonces cambia a `var`. Este enfoque previene errores de mutación accidental.

### 3.2. Inferencia de tipos

Swift es un lenguaje de **tipado estático fuerte**, igual que Java. Sin embargo, el compilador de Swift es capaz de **inferir automáticamente el tipo** de una variable basándose en el valor inicial que le asignas.

```swift
// El compilador infiere automáticamente los tipos
let numero = 42              // Infiere: Int
let precio = 19.99           // Infiere: Double
let mensaje = "Hola mundo"   // Infiere: String
let activo = true            // Infiere: Bool
let letra = "A"              // ⚠️ Infiere: String (no Character)

// Declaración explícita de tipos (como en Java)
let peso: Double = 70.5
let inicial: Character = "A"  // Ahora sí es un Character
let aprobado: Bool = true
```

> [!WARNING]
> En Swift, las comillas dobles `"A"` crean un `String` por defecto, no un `Character` como el `char` de Java. Si necesitas un carácter individual, debes especificar el tipo explícitamente: `let c: Character = "A"`.

### 3.3. Tipos de datos fundamentales

Swift proporciona tipos nativos altamente optimizados. A diferencia de Java, donde `int`, `double`, etc. son primitivos y `Integer`, `Double` son wrappers, **en Swift todos los tipos son estructuras (structs)** de primera clase.

#### Tipos numéricos

```swift
// Enteros con signo
let entero: Int = 42           // Tamaño dependiente de arquitectura (32/64 bits)
let entero8: Int8 = 127        // 8 bits con signo (-128 a 127)
let entero16: Int16 = 32767    // 16 bits con signo
let entero32: Int32 = 2147483647
let entero64: Int64 = 9223372036854775807

// Enteros sin signo
let natural: UInt = 42         // Solo valores positivos
let byte: UInt8 = 255          // 0 a 255

// Números de punto flotante
let flotante: Float = 3.14     // 32 bits (6 decimales de precisión)
let doble: Double = 3.14159265 // 64 bits (15 decimales) - PREFERIDO
```

> [!TIP]
> Usa `Int` para enteros y `Double` para decimales por defecto. Solo especifica tamaños concretos (`Int32`, `Float`) cuando necesites optimizar memoria o interoperar con APIs que lo requieran.

#### Tipo booleano

```swift
let esMayorDeEdad: Bool = true
let haAprobado: Bool = false

// ⚠️ A diferencia de C/Java, Swift NO permite valores numéricos como booleanos
let x = 1
// if x { }  // ❌ ERROR: Type 'Int' cannot be used as a boolean

// Debes hacer comparaciones explícitas
if x == 1 {  // ✅ Correcto
    print("x es 1")
}
```


#### Tipo cadena de texto

```swift
let saludo: String = "Hola"
let nombreCompleto = "Ana" + " " + "García"  // Concatenación

// Cadenas multilínea con triple comilla
let poema = """
    En un lugar de La Mancha,
    de cuyo nombre no quiero acordarme,
    no ha mucho tiempo que vivía un hidalgo.
    """

// Caracteres especiales
let nuevaLinea = "Primera línea\nSegunda línea"
let tabulador = "Columna1\tColumna2"
let comillas = "Él dijo: \"Hola\""
```


### 3.4. Interpolación de cadenas

Esta es una de las características más útiles de Swift. En lugar de concatenar strings con el operador `+` (engorroso y propenso a errores), puedes **inyectar valores directamente** dentro de la cadena usando `\(expresión)`.

```swift
let nombre = "María"
let edad = 22

// ❌ Forma antigua (estilo Java)
let mensaje1 = "Hola, me llamo " + nombre + " y tengo " + String(edad) + " años"

// ✅ Forma Swift (interpolación)
let mensaje2 = "Hola, me llamo \(nombre) y tengo \(edad) años"

// Puedes incluir expresiones complejas
let precioSinIVA = 100.0
let mensaje3 = "El precio con IVA es \(precioSinIVA * 1.21)€"
// Resultado: "El precio con IVA es 121.0€"

// Incluso llamadas a funciones
let usuario = "admin"
let mensaje4 = "Usuario: \(usuario.uppercased())"
// Resultado: "Usuario: ADMIN"
```


### 3.5. Conversión entre tipos

En Java, algunas conversiones numéricas son implícitas (`int` a `long`). En Swift, **todas las conversiones son explícitas** para evitar pérdida de datos accidental.

```swift
let entero: Int = 42
let decimal: Double = 3.14

// ❌ Esto NO compila en Swift
// let suma = entero + decimal  // ERROR: Binary operator '+' cannot be applied

// ✅ Conversión explícita requerida
let suma = Double(entero) + decimal  // 45.14

// Conversión de String a números
let texto = "123"
let numero = Int(texto)  // ⚠️ Devuelve Int? (opcional) porque puede fallar

// Si el texto no es un número válido
let textoInvalido = "abc"
let resultado = Int(textoInvalido)  // resultado es nil
```


### 3.6. Literales numéricos y notaciones especiales

```swift
// Notación decimal estándar
let decimal = 17

// Notación binaria (prefijo 0b)
let binario = 0b10001  // 17 en binario

// Notación octal (prefijo 0o)
let octal = 0o21  // 17 en octal

// Notación hexadecimal (prefijo 0x)
let hexadecimal = 0x11  // 17 en hexadecimal

// Notación científica
let cientifica = 1.25e2  // 1.25 × 10² = 125.0

// Guiones bajos para legibilidad (ignorados por el compilador)
let millon = 1_000_000
let pi = 3.141_592_653_589_793
```


***

## 4. El sistema de tipos seguro: opcionales

Esta es **la característica más importante y distintiva de Swift**. El sistema de opcionales elimina completamente el problema de los punteros nulos (el famoso error de los mil millones de dólares de Tony Hoare).

### 4.1. El problema en Java: null references

En Java, cualquier objeto puede ser `null` en cualquier momento:

```java
// Java
String nombre = obtenerNombre();  // Puede devolver null
int longitud = nombre.length();   // 💥 NullPointerException si nombre es null
```

El compilador de Java no te avisa de este peligro. Solo lo descubres cuando la aplicación crashea en tiempo de ejecución.

### 4.2. La solución de Swift: opcionales explícitos

En Swift, **una variable normal NUNCA puede ser `nil`** (el equivalente a `null` en Java):

```swift
var nombre: String = "Ana"
// nombre = nil  // ❌ ERROR DE COMPILACIÓN: 'nil' cannot be assigned to type 'String'
```

Para indicar que una variable **puede no tener valor**, debemos declararla como **Opcional** añadiendo `?` al tipo:

```swift
var respuesta: String?  // Este es un Optional<String>
respuesta = "Sí"
respuesta = nil  // ✅ Ahora sí es válido
```

> [!IMPORTANT]
> Un opcional es como una "caja": puede contener un valor del tipo especificado, o puede estar vacía (`nil`). Antes de usar el valor, debes "abrir la caja" (unwrap) de forma segura.

### 4.3. Representación conceptual

```
String normal:     [  "Hola"  ]  ← Siempre contiene un String
String opcional:   [  "Hola"  ]  ← Puede contener un String
String opcional:   [   nil    ]  ← O puede estar vacío
```


### 4.4. Desempaquetado forzado (`!`) - Uso desaconsejado

Puedes "forzar" el desempaquetado usando `!`, pero es **peligroso**:

```swift
var numero: Int? = 5

// Desempaquetado forzado
print(numero!)  // 5

// ⚠️ Si numero fuera nil, la app crashea
numero = nil
// print(numero!)  // 💥 Fatal error: Unexpectedly found nil while unwrapping an Optional value
```

> [!CAUTION]
> El desempaquetado forzado (`!`) solo debe usarse cuando estés **100% seguro** de que el opcional tiene valor. En producción, evítalo siempre que sea posible.

### 4.5. Binding seguro con `if let` - Forma recomendada

Esta es la manera **idiomática y segura** de trabajar con opcionales:

```swift
var nombre: String? = "Carlos"

if let nombreSeguro = nombre {
    // Dentro de este bloque, 'nombreSeguro' es un String normal (no opcional)
    print("El nombre es \(nombreSeguro)")
    print("Tiene \(nombreSeguro.count) letras")
} else {
    print("No hay nombre disponible")
}
```

**Múltiples bindings en cascada:**

```swift
var nombre: String? = "Ana"
var edad: Int? = 25

if let n = nombre, let e = edad {
    print("\(n) tiene \(e) años")
} else {
    print("Faltan datos")
}
```

**Binding con condiciones adicionales:**

```swift
var edad: Int? = 17

if let e = edad, e >= 18 {
    print("Mayor de edad con \(e) años")
} else {
    print("Menor de edad o dato no disponible")
}
```


### 4.6. Guard statement: validación temprana

`guard` es una variante de `if let` especialmente útil en funciones. Valida la condición al inicio y sale de la función si falla, evitando la "pirámide de la muerte" (anidación excesiva).

```swift
func procesarPedido(usuario: String?, cantidad: Int?) {
    // Validación temprana: si falla, salimos inmediatamente
    guard let user = usuario else {
        print("Error: Usuario no especificado")
        return
    }
    
    guard let cant = cantidad, cant > 0 else {
        print("Error: Cantidad inválida")
        return
    }
    
    // A partir de aquí, 'user' y 'cant' son valores seguros (no opcionales)
    print("Procesando pedido de \(cant) unidades para \(user)")
    // ... resto de la lógica
}

procesarPedido(usuario: "Ana", cantidad: 5)
procesarPedido(usuario: nil, cantidad: 3)     // Imprime error y sale
procesarPedido(usuario: "Luis", cantidad: -1) // Imprime error y sale
```

> [!TIP]
> Usa `guard` cuando necesites validar condiciones al inicio de una función. Mantiene el "happy path" (flujo principal) sin indentación excesiva.

### 4.7. Operador Nil Coalescing (`??`)

Proporciona un **valor por defecto** si el opcional es `nil`:

```swift
var nombreUsuario: String? = nil

// Sin nil coalescing
let nombre1: String
if let n = nombreUsuario {
    nombre1 = n
} else {
    nombre1 = "Invitado"
}

// Con nil coalescing (mucho más conciso)
let nombre2 = nombreUsuario ?? "Invitado"

print(nombre2)  // "Invitado"

// Ejemplo práctico
var descuento: Double? = nil
let precioFinal = 100 - (descuento ?? 0)  // Si no hay descuento, usa 0
print(precioFinal)  // 100.0
```


### 4.8. Optional chaining

Permite acceder a propiedades, métodos o subíndices de un opcional de forma segura. Si el opcional es `nil`, toda la expresión devuelve `nil` sin crashear.

```swift
struct Direccion {
    var calle: String
    var numero: Int
}

struct Persona {
    var nombre: String
    var direccion: Direccion?
}

var persona: Persona? = Persona(nombre: "Ana", direccion: nil)

// Optional chaining con '?'
let calle = persona?.direccion?.calle
// calle es String? (opcional) porque cualquier eslabón puede ser nil

// Si persona o direccion son nil, no se ejecuta nada, simplemente devuelve nil
print(calle ?? "Dirección desconocida")  // "Dirección desconocida"

// Asignamos una dirección
persona?.direccion = Direccion(calle: "Gran Vía", numero: 10)
let calleAhora = persona?.direccion?.calle
print(calleAhora ?? "Sin calle")  // "Gran Vía"
```

**Con métodos:**

```swift
var nombre: String? = "  swift  "
let nombreLimpio = nombre?.trimmingCharacters(in: .whitespaces)
// Si nombre es nil, nombreLimpio será nil
// Si nombre tiene valor, nombreLimpio será String? con el valor procesado

print(nombreLimpio ?? "Sin nombre")  // "swift"
```


### 4.9. Implicitly Unwrapped Optionals (`String!`)

Son opcionales que se desempaquetan automáticamente cuando se usan. Se declaran con `!` en lugar de `?`:

```swift
var configuracion: String! = "Modo oscuro"

// No necesitas unwrap
print(configuracion)  // ✅ Imprime "Modo oscuro" directamente

// Pero sigue siendo un opcional internamente
configuracion = nil
// print(configuracion)  // 💥 Crashea si intentas usar mientras es nil
```

> [!WARNING]
> Los Implicitly Unwrapped Optionals solo deben usarse cuando estés seguro de que el valor se inicializará antes de ser usado. En código nuevo, evítalos.

### 4.10. Comparativa: gestión de nulos en Java vs Swift

| Situación | Java | Swift |
| :-- | :-- | :-- |
| Valor que nunca es nulo | `@NonNull String nombre` (anotación, no garantía) | `let nombre: String` ✅ Garantizado por compilador |
| Valor que puede ser nulo | `String nombre` (cualquiera puede ser null) | `var nombre: String?` Explícito |
| Comprobar nulidad | `if (nombre != null)` | `if let nombre = nombre` |
| Valor por defecto | `nombre != null ? nombre : "Invitado"` | `nombre ?? "Invitado"` |
| Acceso seguro a propiedades | `nombre != null ? nombre.length() : null` | `nombre?.count` |


***

## 5. Tuplas: agrupación ligera de valores

Las tuplas permiten agrupar múltiples valores en uno solo sin necesidad de crear una clase o estructura. Son especialmente útiles para devolver múltiples valores desde una función.

### 5.1. Declaración y acceso básico

```swift
// Tupla sin nombres (acceso por índice)
let coordenadas = (40.4168, -3.7038)
print("Latitud: \(coordenadas.0)")   // 40.4168
print("Longitud: \(coordenadas.1)")  // -3.7038

// Tupla con nombres (más legible)
let ubicacion = (latitud: 40.4168, longitud: -3.7038)
print("Latitud: \(ubicacion.latitud)")   // 40.4168
print("Longitud: \(ubicacion.longitud)") // -3.7038
```


### 5.2. Tuplas heterogéneas

Las tuplas pueden contener tipos diferentes:

```swift
let producto = (nombre: "iPhone 15", precio: 999.0, disponible: true)
print("\(producto.nombre) cuesta \(producto.precio)€")

// Tupla con tipos mixtos sin nombres
let resultado = ("OK", 200, true)
```


### 5.3. Desestructuración (destructuring)

Puedes "desempaquetar" los valores de una tupla en variables separadas:

```swift
let persona = (nombre: "Luis", edad: 30, ciudad: "Madrid")

// Desestructuración
let (n, e, c) = persona
print("\(n) tiene \(e) años y vive en \(c)")

// Ignorar valores que no necesites con _
let (nombre, _, ciudad) = persona
print("\(nombre) vive en \(ciudad)")
```


### 5.4. Tuplas como valores de retorno

```swift
func obtenerEstadisticas(numeros: [Int]) -> (min: Int, max: Int, suma: Int) {
    let minimo = numeros.min() ?? 0
    let maximo = numeros.max() ?? 0
    //let suma = numeros.reduce(0, +) //Lo veremos en el tema 15
    var suma = 0
    for numero in numeros {
        suma += numero
    }
    return (minimo, maximo, suma)
}

let stats = obtenerEstadisticas(numeros: [3, 7, 2, 9, 1])
print("Mínimo: \(stats.min)")   // 1
print("Máximo: \(stats.max)")   // 9
print("Suma: \(stats.suma)")    // 22
```

> [!NOTE]
> En Java tendrías que crear una clase `Estadisticas` solo para devolver estos tres valores. Las tuplas de Swift son perfectas para estos casos simples.

***

## 6. Colecciones básicas

Swift proporciona tres tipos de colecciones fundamentales: Arrays, Diccionarios y Sets. A diferencia de Java, donde `ArrayList` es mutable por defecto, en Swift la mutabilidad depende de si usas `let` o `var`.

### 6.1. Arrays (listas ordenadas)

#### Declaración e inicialización

```swift
// Array vacío especificando el tipo
var numeros: [Int] = []
var nombres = [String]()  // Sintaxis alternativa

// Array con valores iniciales (inferencia de tipo)
var frutas = ["Manzana", "Pera", "Naranja"]  // Infiere [String]

// Array con valor por defecto repetido
var ceros = Array(repeating: 0, count: 5)  // [0, 0, 0, 0, 0]
```


#### Mutabilidad: `let` vs `var`

```swift
let frutasInmutables = ["Manzana", "Pera"]
// frutasInmutables.append("Naranja")  // ❌ ERROR: Cannot use mutating member

var frutasMutables = ["Manzana", "Pera"]
frutasMutables.append("Naranja")  // ✅ Correcto
```


#### Operaciones básicas

```swift
var colores = ["Rojo", "Verde"]

// Añadir elementos
colores.append("Azul")              // ["Rojo", "Verde", "Azul"]
colores.insert("Amarillo", at: 1)   // ["Rojo", "Amarillo", "Verde", "Azul"]
colores += ["Negro", "Blanco"]      // Concatenación

// Acceder a elementos
let primero = colores[0]            // "Rojo"
let ultimo = colores[colores.count - 1]  // "Blanco"

// Propiedades útiles
print(colores.count)       // 6
print(colores.isEmpty)     // false
print(colores.first)       // Optional("Rojo")
print(colores.last)        // Optional("Blanco")

// Modificar elementos
colores[1] = "Naranja"     // Reemplaza "Amarillo" por "Naranja"

// Eliminar elementos
colores.remove(at: 2)      // Elimina "Verde", devuelve el valor eliminado
colores.removeLast()       // Elimina "Blanco"
colores.removeAll()        // Vacía el array
```


#### Búsqueda y comprobación

```swift
let numeros = [10, 20, 30, 40, 50]

// Comprobar si contiene un elemento
if numeros.contains(30) {
    print("El 30 está en la lista")
}

// Encontrar índice
if let indice = numeros.firstIndex(of: 40) {
    print("El 40 está en la posición \(indice)")  // 3
}
```


#### Iteración

```swift
let frutas = ["Manzana", "Pera", "Naranja"]

// Iterar sobre valores
for fruta in frutas {
    print(fruta)
}

// Iterar con índice
for (indice, fruta) in frutas.enumerated() {
    print("\(indice): \(fruta)")
}
// 0: Manzana
// 1: Pera
// 2: Naranja

// Iterar sobre un rango de índices
for i in 0..<frutas.count {
    print(frutas[i])
}
```


### 6.2. Diccionarios (mapas clave-valor)

Los diccionarios almacenan asociaciones clave-valor desordenadas. Son equivalentes a `HashMap` en Java.

#### Declaración e inicialización

```swift
// Diccionario vacío
var edades: [String: Int] = [:]
var capitales = [String: String]()  // Sintaxis alternativa

// Diccionario con valores iniciales
var paises = [
    "España": "Madrid",
    "Francia": "París",
    "Italia": "Roma"
]
```


#### Operaciones básicas

```swift
var puntuaciones = ["Ana": 95, "Luis": 87]

// Añadir o modificar
puntuaciones["Carlos"] = 92        // Añade nueva entrada
puntuaciones["Ana"] = 98           // Modifica valor existente

// Acceder (devuelve opcional)
if let puntosAna = puntuaciones["Ana"] {
    print("Ana tiene \(puntosAna) puntos")
}

// Valor por defecto si la clave no existe
let puntosPedro = puntuaciones["Pedro", default: 0]
print(puntosPedro)  // 0

// Eliminar
puntuaciones["Luis"] = nil         // Elimina la entrada
puntuaciones.removeValue(forKey: "Carlos")  // Alternativa que devuelve el valor eliminado

// Propiedades
print(puntuaciones.count)          // 1
print(puntuaciones.isEmpty)        // false
print(puntuaciones.keys)           // ["Ana"]
print(puntuaciones.values)         // [98]
```


#### Iteración

```swift
let capitales = [
    "España": "Madrid",
    "Francia": "París",
    "Italia": "Roma"
]

// Iterar sobre pares clave-valor
for (pais, capital) in capitales {
    print("La capital de \(pais) es \(capital)")
}

// Iterar solo sobre claves
for pais in capitales.keys {
    print(pais)
}

// Iterar solo sobre valores
for capital in capitales.values {
    print(capital)
}
```


### 6.3. Sets (conjuntos sin orden ni duplicados)

Los Sets almacenan valores únicos sin orden específico. Son útiles cuando necesitas garantizar unicidad o realizar operaciones de conjuntos.

#### Declaración e inicialización

```swift
// Set vacío
var numeros = Set<Int>()

// Set con valores iniciales
var coloresPrimarios: Set<String> = ["Rojo", "Azul", "Amarillo"]

// ⚠️ Sin anotación de tipo, el compilador crearía un Array
var colores: Set = ["Rojo", "Verde", "Azul"]  // Necesitas especificar Set
```


#### Operaciones básicas

```swift
var lenguajes: Set<String> = ["Swift", "Java", "Kotlin"]

// Añadir (devuelve tupla indicando si se insertó)
let (insertado, _) = lenguajes.insert("Python")  // (true, "Python")
lenguajes.insert("Java")  // No duplica, ya existe

// Comprobar pertenencia (muy rápido, O(1))
if lenguajes.contains("Swift") {
    print("Conoces Swift")
}

// Eliminar
lenguajes.remove("Java")  // Devuelve el elemento o nil
lenguajes.removeAll()

print(lenguajes.count)    // 3
```


#### Operaciones de conjuntos

```swift
let setA: Set = [1, 2, 3, 4]
let setB: Set = [3, 4, 5, 6]

// Unión (todos los elementos)
let union = setA.union(setB)
print(union)  // [1, 2, 3, 4, 5, 6]

// Intersección (elementos comunes)
let interseccion = setA.intersection(setB)
print(interseccion)  // [3, 4]

// Diferencia (elementos en A pero no en B)
let diferencia = setA.subtracting(setB)
print(diferencia)  // [1, 2]

// Diferencia simétrica (elementos no comunes)
let simetrica = setA.symmetricDifference(setB)
print(simetrica)  // [1, 2, 5, 6]

// Comprobaciones
print(setA.isSubset(of: union))        // true
print(setA.isSuperset(of: interseccion))  // true
print(setA.isDisjoint(with: setB))     // false (tienen elementos en común)
```


### 6.4. Comparativa de colecciones

| Característica | Array | Dictionary | Set |
| :-- | :-- | :-- | :-- |
| **Orden** | Sí (por índice) | No | No |
| **Duplicados** | Permitidos | Claves únicas | No permitidos |
| **Acceso** | Por índice `[0]` | Por clave `["key"]` | Por valor |
| **Rendimiento búsqueda** | O(n) lineal | O(1) constante | O(1) constante |
| **Uso típico** | Listas ordenadas | Mapeos clave-valor | Unicidad, operaciones matemáticas |


***

## 7. Control de flujo

### 7.1. Condicionales: `if`, `else if`, `else`

La sintaxis es similar a Java, pero los paréntesis en la condición son opcionales. Las llaves `{}` son obligatorias incluso para una sola línea.

```swift
let temperatura = 25

if temperatura > 30 {
    print("Hace calor")
} else if temperatura > 20 {
    print("Temperatura agradable")
} else {
    print("Hace frío")
}

// Los paréntesis son opcionales pero permitidos
if (temperatura >= 0 && temperatura <= 10) {
    print("Hace mucho frío")
}
```


#### `if` como expresión

A diferencia de Java, en Swift puedes usar `if` directamente en asignaciones:

```swift
let edad = 17
let categoria = if edad >= 18 { "Adulto" } else { "Menor" }
print(categoria)  // "Menor"

// Útil para inicializar constantes condicionalmente
let descuento = if esClienVIP { 20.0 } else { 5.0 }
```


### 7.2. Switch: pattern matching avanzado

El `switch` de Swift es mucho más potente que el de Java. Características clave:

- **No necesita `break`** (no hay fall-through automático)
- **Debe ser exhaustivo** (cubrir todos los casos o incluir `default`)
- Soporta **rangos, tuplas, y pattern matching**
- Cada caso puede ejecutar múltiples líneas


#### Switch básico

```swift
let diaSemana = 3

switch diaSemana {
case 1:
    print("Lunes")
case 2:
    print("Martes")
case 3:
    print("Miércoles")
case 4:
    print("Jueves")
case 5:
    print("Viernes")
case 6, 7:  // Múltiples valores en un caso
    print("Fin de semana")
default:
    print("Día inválido")
}
```


#### Switch con rangos

```swift
let nota = 7

switch nota {
case 0..<5:
    print("Suspenso")
case 5..<7:
    print("Aprobado")
case 7..<9:
    print("Notable")
case 9...10:  // Incluye el 10
    print("Sobresaliente")
default:
    print("Nota no válida")
}
```


#### Switch con tuplas

```swift
let punto = (x: 1, y: 0)

switch punto {
case (0, 0):
    print("Origen")
case (_, 0):  // '_' ignora el valor de x
    print("En el eje X")
case (0, _):
    print("En el eje Y")
case (-2...2, -2...2):
    print("Dentro del cuadrado central")
default:
    print("Fuera del cuadrado")
}
```


#### Value binding en switch

```swift
let punto = (x: 2, y: 3)

switch punto {
case (0, 0):
    print("Origen")
case (let x, 0):  // Captura el valor de x
    print("En el eje X, posición \(x)")
case (0, let y):
    print("En el eje Y, posición \(y)")
case let (x, y):  // Captura ambos valores
    print("Punto en (\(x), \(y))")
}
```


#### Where clause en switch

```swift
let numero = 25

switch numero {
case let n where n % 2 == 0:
    print("\(n) es par")
case let n where n % 2 != 0:
    print("\(n) es impar")
default:
    break
}
```

> [!TIP]
> El `switch` de Swift es tan potente que reemplaza muchos patrones que en Java requerirían múltiples `if-else`. Úsalo siempre que evalúes múltiples condiciones sobre la misma variable.

### 7.3. Bucles

#### For-in con rangos

```swift
// Rango cerrado (incluye el final)
for i in 1...5 {
    print(i)  // 1, 2, 3, 4, 5
}

// Rango semicerrado (excluye el final)
for i in 1..<5 {
    print(i)  // 1, 2, 3, 4
}

// Rango inverso
for i in (1...5).reversed() {
    print(i)  // 5, 4, 3, 2, 1
}

// Saltos (stride)
for i in stride(from: 0, to: 10, by: 2) {
    print(i)  // 0, 2, 4, 6, 8
}

// Si no necesitas la variable iteradora
for _ in 1...3 {
    print("Hola")  // Se imprime 3 veces
}
```


#### For-in con colecciones

```swift
let frutas = ["Manzana", "Pera", "Naranja"]

// Iterar valores
for fruta in frutas {
    print(fruta)
}

// Iterar con índices
for (indice, fruta) in frutas.enumerated() {
    print("\(indice + 1). \(fruta)")
}
// 1. Manzana
// 2. Pera
// 3. Naranja

// Iterar diccionarios
let edades = ["Ana": 25, "Luis": 30]
for (nombre, edad) in edades {
    print("\(nombre) tiene \(edad) años")
}
```


#### Where en bucles

```swift
// Solo números pares
for numero in 1...10 where numero % 2 == 0 {
    print(numero)  // 2, 4, 6, 8, 10
}

// Con colecciones
let nombres = ["Ana", "Luis", "María", "Pedro"]
for nombre in nombres where nombre.count > 4 {
    print(nombre)  // Luis, María, Pedro
}
```


#### While y repeat-while

```swift
// While (evalúa condición antes de ejecutar)
var contador = 0
while contador < 5 {
    print(contador)
    contador += 1
}

// Repeat-while (evalúa después, equivalente a do-while en Java)
var numero = 0
repeat {
    print(numero)
    numero += 1
} while numero < 5
```


#### Control de bucles: break y continue

```swift
// Continue: salta a la siguiente iteración
for i in 1...10 {
    if i % 2 == 0 {
        continue  // Salta los números pares
    }
    print(i)  // Solo imprime impares
}

// Break: sale del bucle
for i in 1...100 {
    if i > 5 {
        break  // Sale cuando i es mayor que 5
    }
    print(i)
}

// Etiquetas en bucles anidados
outerLoop: for i in 1...3 {
    for j in 1...3 {
        if i * j > 4 {
            break outerLoop  // Sale de ambos bucles
        }
        print("\(i) x \(j) = \(i * j)")
    }
}
```


***

## 8. Funciones básicas

Las funciones en Swift son ciudadanas de primera clase: pueden asignarse a variables, pasarse como parámetros y devolverse desde otras funciones.

### 8.1. Declaración básica

```swift
// Sintaxis: func nombre(parametro: Tipo) -> TipoRetorno { ... }

func saludar(nombre: String) -> String {
    return "Hola, \(nombre)"
}

let mensaje = saludar(nombre: "Ana")
print(mensaje)  // "Hola, Ana"

// Función sin parámetros
func obtenerSaludo() -> String {
    return "¡Bienvenido!"
}

// Función sin valor de retorno (devuelve Void o simplemente se omite)
func imprimirMensaje(texto: String) {
    print(texto)
}

// Alternativa explícita
func imprimirMensaje2(texto: String) -> Void {
    print(texto)
}
```


### 8.2. Argument labels (etiquetas de argumentos)

Una de las características distintivas de Swift es que los nombres de parámetros forman parte de la firma de la función, haciendo que las llamadas se lean como frases en inglés.

#### Etiquetas externas e internas

```swift
// 'desde' es la etiqueta externa (usada al llamar)
// 'origen' es el nombre interno (usado dentro de la función)
func viajar(desde origen: String, hasta destino: String) {
    print("Viajando de \(origen) a \(destino)")
}

// La llamada se lee naturalmente
viajar(desde: "Madrid", hasta: "Barcelona")
```


#### Omitir la etiqueta externa con `_`

```swift
func sumar(_ a: Int, _ b: Int) -> Int {
    return a + b
}

// Llamada sin etiquetas
let resultado = sumar(5, 3)  // 8
```


#### Etiquetas por defecto

```swift
// Si no especificas etiqueta externa, se usa el nombre del parámetro
func multiplicar(numero a: Int, por b: Int) -> Int {
    return a * b
}

multiplicar(numero: 5, por: 3)  // 15
```

> [!NOTE]
> Las argument labels hacen que el código Swift sea mucho más legible que Java. Compara:
> ```java > // Java (poco claro) > calcularPrecio(100, 0.21, true); >  > // Swift (autoexplicativo) > calcularPrecio(base: 100, iva: 0.21, aplicarDescuento: true) > ```

### 8.3. Parámetros con valores por defecto

```swift
func reservarMesa(personas: Int, hora: String = "20:00", fumador: Bool = false) {
    print("Mesa para \(personas) personas a las \(hora)")
    print("Zona de fumadores: \(fumador ? "Sí" : "No")")
}

// Llamadas con diferentes combinaciones
reservarMesa(personas: 4)                          // Usa valores por defecto
reservarMesa(personas: 2, hora: "21:00")          // Especifica solo hora
reservarMesa(personas: 6, hora: "19:00", fumador: true)  // Especifica todo
```


### 8.4. Parámetros variádicos

Permiten pasar un número variable de argumentos del mismo tipo. Dentro de la función, se reciben como un array.

```swift
func calcularMedia(_ numeros: Double...) -> Double {
    guard !numeros.isEmpty else { return 0 }
    
    // let suma = numeros.reduce(0, +) // Lo veremos en el tema 15
    var suma = 0
    for numero in numeros {
        suma += numero
    }
    return suma / Double(numeros.count)
}

print(calcularMedia(5, 10, 15))           // 10.0
print(calcularMedia(3.5, 7.2, 9.1, 2.8))  // 5.65
```

> [!WARNING]
> Solo puedes tener un parámetro variádico por función, y debe ser el último parámetro (o seguido solo de parámetros con valores por defecto).

### 8.5. Parámetros `inout` (paso por referencia)

Por defecto, los parámetros son constantes (`let`). Si necesitas modificar el valor original, usa `inout`:

```swift
func incrementar(_ numero: inout Int) {
    numero += 1
}

var valor = 5
incrementar(&valor)  // ⚠️ Nota el & al pasar la variable
print(valor)  // 6

// Ejemplo más útil: intercambiar valores
func intercambiar(_ a: inout Int, _ b: inout Int) {
    let temp = a
    a = b
    b = temp
}

var x = 10
var y = 20
intercambiar(&x, &y)
print("x: \(x), y: \(y)")  // x: 20, y: 10
```


### 8.6. Funciones como tipos

Las funciones pueden asignarse a variables y constantes. El tipo de una función se define por sus parámetros y valor de retorno.

```swift
func sumar(_ a: Int, _ b: Int) -> Int {
    return a + b
}

func restar(_ a: Int, _ b: Int) -> Int {
    return a - b
}

// El tipo de estas funciones es (Int, Int) -> Int
var operacion: (Int, Int) -> Int

operacion = sumar
print(operacion(5, 3))  // 8

operacion = restar
print(operacion(5, 3))  // 2
```


### 8.7. Funciones de orden superior básicas

Una función de orden superior es aquella que acepta otras funciones como parámetros o devuelve funciones.

#### Funciones que aceptan funciones

```swift
func aplicarOperacion(_ a: Int, _ b: Int, operacion: (Int, Int) -> Int) -> Int {
    return operacion(a, b)
}

func sumar(_ a: Int, _ b: Int) -> Int { a + b }
func multiplicar(_ a: Int, _ b: Int) -> Int { a * b }

let resultado1 = aplicarOperacion(5, 3, operacion: sumar)
print(resultado1)  // 8

let resultado2 = aplicarOperacion(5, 3, operacion: multiplicar)
print(resultado2)  // 15
```


#### Funciones que devuelven funciones

```swift
func crearSumador(incremento: Int) -> (Int) -> Int {
    // Devuelve una función que suma el incremento especificado
    func sumar(numero: Int) -> Int {
        return numero + incremento
    }
    return sumar
}

let sumarCinco = crearSumador(incremento: 5)
print(sumarCinco(10))  // 15
print(sumarCinco(20))  // 25

let sumarDiez = crearSumador(incremento: 10)
print(sumarDiez(10))  // 20
```

> [!TIP]
> Las funciones de orden superior son fundamentales en programación funcional. Las verás con mucha más profundidad en el Tema 15.

***

## 9. Enumeraciones básicas

Las enumeraciones (enums) definen un conjunto finito de valores relacionados. En Swift son tipos de primera clase con capacidades mucho más avanzadas que en Java.

### 9.1. Declaración básica

```swift
enum DiaSemana {
    case lunes
    case martes
    case miercoles
    case jueves
    case viernes
    case sabado
    case domingo
}

// Sintaxis compacta
enum Direccion {
    case norte, sur, este, oeste
}

// Uso
var dia = DiaSemana.lunes
dia = .martes  // El tipo ya es conocido, puedes omitir el enum

print(dia)  // martes
```


### 9.2. Switch con enums

Los enums y switch son una combinación perfecta en Swift. El compilador verifica exhaustividad:

```swift
enum EstadoPedido {
    case pendiente, procesando, enviado, entregado, cancelado
}

let estado = EstadoPedido.enviado

switch estado {
case .pendiente:
    print("Pedido recibido")
case .procesando:
    print("Preparando pedido")
case .enviado:
    print("Pedido en camino")
case .entregado:
    print("Pedido entregado")
case .cancelado:
    print("Pedido cancelado")
}
// No necesita default porque cubrimos todos los casos
```


### 9.3. Raw values (valores asociados)

Los enums pueden tener valores "crudos" del mismo tipo para todos los casos:

```swift
enum Planeta: Int {
    case mercurio = 1
    case venus = 2
    case tierra = 3
    case marte = 4
}

let planeta = Planeta.tierra
print(planeta.rawValue)  // 3

// Crear desde raw value (devuelve opcional)
if let p = Planeta(rawValue: 2) {
    print(p)  // venus
}

// Con String (auto-asigna el nombre del caso)
enum Moneda: String {
    case euro
    case dolar
    case libra
}

print(Moneda.euro.rawValue)  // "euro"
```


### 9.4. Associated values (valores asociados)

Los enums pueden almacenar valores adicionales específicos de cada instancia:

```swift
enum CodigoBarras {
    case upc(Int, Int, Int, Int)  // UPC tiene 4 números
    case qr(String)               // QR tiene un string
}

var producto = CodigoBarras.upc(8, 85909, 51226, 3)
producto = .qr("ABCDEFGHIJKLMNOP")

// Extraer valores con switch
switch producto {
case .upc(let sistema, let fabricante, let producto, let control):
    print("UPC: \(sistema), \(fabricante), \(producto), \(control)")
case .qr(let codigo):
    print("QR code: \(codigo)")
}

// Sintaxis abreviada cuando todos son let o var
switch producto {
case let .upc(sistema, fabricante, producto, control):
    print("UPC: \(sistema)-\(fabricante)-\(producto)-\(control)")
case let .qr(codigo):
    print("QR: \(codigo)")
}
```


### 9.5. Métodos en enums

```swift
enum Operacion {
    case suma, resta, multiplicacion, division
    
    func aplicar(_ a: Double, _ b: Double) -> Double {
        switch self {
        case .suma:
            return a + b
        case .resta:
            return a - b
        case .multiplicacion:
            return a * b
        case .division:
            return b != 0 ? a / b : 0
        }
    }
    
    var simbolo: String {
        switch self {
        case .suma: return "+"
        case .resta: return "-"
        case .multiplicacion: return "×"
        case .division: return "÷"
        }
    }
}

let op = Operacion.suma
print("\(op.simbolo): \(op.aplicar(5, 3))")  // +: 8.0
```


***

## 10. Programación orientada a objetos: estructuras y clases

Swift combina estructuras (value types) y clases (reference types). Esta distinción es fundamental y difiere significativamente de Java.

### 10.1. Estructuras (structs): tipos de valor

Las estructuras se copian cuando se asignan. Son ideales para datos simples y modelos inmutables.

#### Declaración básica

```swift
struct Rectangulo {
    var ancho: Double
    var alto: Double
}

// Inicializador automático (memberwise initializer)
var rect1 = Rectangulo(ancho: 10, alto: 5)
print(rect1.ancho)  // 10

// Se copian al asignar
var rect2 = rect1
rect2.ancho = 20

print(rect1.ancho)  // 10 (no ha cambiado)
print(rect2.ancho)  // 20
```


#### Propiedades computadas

Las propiedades computadas calculan su valor cada vez que se acceden:

```swift
struct Rectangulo {
    var ancho: Double
    var alto: Double
    
    // Propiedad computada de solo lectura
    var area: Double {
        return ancho * alto
    }
    
    // Propiedad computada con getter y setter
    var perimetro: Double {
        get {
            return 2 * (ancho + alto)
        }
        set(nuevoPerimetro) {
            // Simplificación: asumimos cuadrado
            let lado = nuevoPerimetro / 4
            ancho = lado
            alto = lado
        }
    }
}

var rect = Rectangulo(ancho: 10, alto: 5)
print(rect.area)       // 50.0
print(rect.perimetro)  // 30.0

rect.perimetro = 40
print(rect.ancho)  // 10.0
print(rect.alto)   // 10.0
```


#### Property observers (observadores de propiedades)

Ejecutan código cuando una propiedad está a punto de cambiar (`willSet`) o acaba de cambiar (`didSet`):

```swift
struct CuentaBancaria {
    var saldo: Double {
        willSet(nuevoSaldo) {
            print("El saldo va a cambiar de \(saldo) a \(nuevoSaldo)")
        }
        didSet {
            if saldo < 0 {
                print("⚠️ Saldo negativo!")
            }
            print("Saldo actualizado. Anterior: \(oldValue), Nuevo: \(saldo)")
        }
    }
}

var cuenta = CuentaBancaria(saldo: 100)
cuenta.saldo = 50
// Imprime:
// El saldo va a cambiar de 100.0 a 50.0
// Saldo actualizado. Anterior: 100.0, Nuevo: 50.0

cuenta.saldo = -20
// ⚠️ Saldo negativo!
```


#### Métodos

```swift
struct Punto {
    var x: Double
    var y: Double
    
    func distanciaAlOrigen() -> Double {
        return (x * x + y * y).squareRoot()
    }
    
    func distancia(a otro: Punto) -> Double {
        let dx = x - otro.x
        let dy = y - otro.y
        return (dx * dx + dy * dy).squareRoot()
    }
}

let p1 = Punto(x: 3, y: 4)
print(p1.distanciaAlOrigen())  // 5.0

let p2 = Punto(x: 0, y: 0)
print(p1.distancia(a: p2))     // 5.0
```


#### Métodos mutantes

Por defecto, las estructuras son inmutables dentro de sus métodos. Para modificar propiedades, marca el método como `mutating`:

```swift
struct Contador {
    var valor = 0
    
    mutating func incrementar() {
        valor += 1
    }
    
    mutating func incrementar(en cantidad: Int) {
        valor += cantidad
    }
    
    mutating func reiniciar() {
        valor = 0
    }
}

var contador = Contador()
contador.incrementar()
contador.incrementar(en: 5)
print(contador.valor)  // 6
```

> [!IMPORTANT]
> Si intentas llamar a un método `mutating` sobre una constante (`let`), obtendrás un error de compilación. Las constantes de struct son completamente inmutables.

```swift
let contadorConstante = Contador()
// contadorConstante.incrementar()  // ❌ ERROR: Cannot use mutating member on immutable value
```


### 10.2. Clases: tipos de referencia

Las clases funcionan como objetos en Java: se pasan por referencia y comparten la misma instancia en memoria.

#### Declaración básica

```swift
class Persona {
    var nombre: String
    var edad: Int
    
    // Las clases requieren inicializadores explícitos
    init(nombre: String, edad: Int) {
        self.nombre = nombre
        self.edad = edad
    }
    
    func presentarse() {
        print("Hola, soy \(nombre) y tengo \(edad) años")
    }
}

let persona1 = Persona(nombre: "Ana", edad: 25)
persona1.presentarse()  // "Hola, soy Ana y tengo 25 años"
```


#### Comportamiento de referencia

```swift
class Coche {
    var marca: String
    var velocidad: Int
    
    init(marca: String, velocidad: Int) {
        self.marca = marca
        self.velocidad = velocidad
    }
}

let coche1 = Coche(marca: "Toyota", velocidad: 100)
let coche2 = coche1  // SE COPIA LA REFERENCIA (no el objeto)

coche2.velocidad = 200

print(coche1.velocidad)  // 200 (cambió!)
print(coche2.velocidad)  // 200

// Ambas variables apuntan al mismo objeto en memoria
```


#### Comparación de identidad

```swift
let coche3 = Coche(marca: "Toyota", velocidad: 100)
let coche4 = Coche(marca: "Toyota", velocidad: 100)
let coche5 = coche3

// Identidad (mismo objeto en memoria): === y !==
print(coche3 === coche5)  // true (mismo objeto)
print(coche3 === coche4)  // false (objetos diferentes aunque contengan los mismos valores)

// Para comparar contenido, necesitarías implementar Equatable
```


### 10.3. Herencia básica

Solo las clases (no las estructuras) soportan herencia.

#### Definición de jerarquía

```swift
// Clase base
class Vehiculo {
    var marca: String
    var velocidadMaxima: Int
    
    init(marca: String, velocidadMaxima: Int) {
        self.marca = marca
        self.velocidadMaxima = velocidadMaxima
    }
    
    func describir() {
        print("Vehículo \(marca) con velocidad máxima de \(velocidadMaxima) km/h")
    }
}

// Clase derivada
class Coche: Vehiculo {
    var numeroPuertas: Int
    
    init(marca: String, velocidadMaxima: Int, numeroPuertas: Int) {
        self.numeroPuertas = numeroPuertas
        // Primero inicializamos propiedades propias
        
        super.init(marca: marca, velocidadMaxima: velocidadMaxima)
        // Luego llamamos al inicializador de la superclase
    }
}

let coche = Coche(marca: "Toyota", velocidadMaxima: 180, numeroPuertas: 4)
coche.describir()  // Hereda el método de Vehiculo
```


#### Sobrescritura de métodos con `override`

```swift
class Moto: Vehiculo {
    var tieneSidecar: Bool
    
    init(marca: String, velocidadMaxima: Int, tieneSidecar: Bool) {
        self.tieneSidecar = tieneSidecar
        super.init(marca: marca, velocidadMaxima: velocidadMaxima)
    }
    
    // Sobrescribir método de la clase padre
    override func describir() {
        print("Moto \(marca) con velocidad máxima de \(velocidadMaxima) km/h")
        if tieneSidecar {
            print("Equipada con sidecar")
        }
    }
}

let moto = Moto(marca: "Harley", velocidadMaxima: 150, tieneSidecar: true)
moto.describir()
// Imprime:
// Moto Harley con velocidad máxima de 150 km/h
// Equipada con sidecar
```


#### Llamadas a `super`

```swift
class Deportivo: Coche {
    var turbo: Bool
    
    init(marca: String, velocidadMaxima: Int, numeroPuertas: Int, turbo: Bool) {
        self.turbo = turbo
        super.init(marca: marca, velocidadMaxima: velocidadMaxima, numeroPuertas: numeroPuertas)
    }
    
    override func describir() {
        super.describir()  // Llama a la versión de la clase padre
        if turbo {
            print("Con turbo activado")
        }
    }
}

let deportivo = Deportivo(marca: "Ferrari", velocidadMaxima: 320, numeroPuertas: 2, turbo: true)
deportivo.describir()
// Vehículo Ferrari con velocidad máxima de 320 km/h
// Con turbo activado
```


#### Prevenir herencia con `final`

```swift
// Clase que no puede ser heredada
final class Utilidad {
    static func calcular() -> Int {
        return 42
    }
}

// class SubUtilidad: Utilidad { }  // ❌ ERROR: Cannot inherit from final class

// Método que no puede ser sobrescrito
class Base {
    final func metodoFijo() {
        print("Este método no puede sobrescribirse")
    }
}

class Derivada: Base {
    // override func metodoFijo() { }  // ❌ ERROR: Cannot override final method
}
```


### 10.4. Polimorfismo

El polimorfismo permite tratar objetos de diferentes clases de manera uniforme a través de su superclase:

```swift
class Animal {
    func hacerSonido() {
        print("Algún sonido")
    }
}

class Perro: Animal {
    override func hacerSonido() {
        print("Guau guau")
    }
}

class Gato: Animal {
    override func hacerSonido() {
        print("Miau")
    }
}

// Array de tipo Animal (superclase)
let animales: [Animal] = [
    Perro(),
    Gato(),
    Perro(),
    Gato()
]

// Cada animal ejecuta su propia versión del método
for animal in animales {
    animal.hacerSonido()
}
// Guau guau
// Miau
// Guau guau
// Miau
```


#### Type casting: `is`, `as?`, `as!`

```swift
let animal: Animal = Perro()

// Comprobar tipo con 'is'
if animal is Perro {
    print("Es un perro")
}

// Downcasting condicional con 'as?'
if let perro = animal as? Perro {
    print("Confirmado, es un perro")
}

// Downcasting forzado con 'as!' (peligroso)
let perroForzado = animal as! Perro  // Crashea si no es un Perro
```


### 10.5. Protocols básicos (interfaces)

Los protocols definen un "contrato" de métodos y propiedades que los tipos deben implementar. Son similares a interfaces en Java.

#### Definición y conformidad

```swift
protocol Volador {
    var altitudMaxima: Double { get }
    func volar()
    func aterrizar()
}

class Avion: Volador {
    var altitudMaxima: Double = 10000
    
    func volar() {
        print("Avión despegando...")
    }
    
    func aterrizar() {
        print("Avión aterrizando...")
    }
}

class Pajaro: Volador {
    var altitudMaxima: Double = 500
    
    func volar() {
        print("Pájaro volando...")
    }
    
    func aterrizar() {
        print("Pájaro posándose...")
    }
}

// Polimorfismo con protocols
let voladores: [Volador] = [Avion(), Pajaro()]
for volador in voladores {
    volador.volar()
}
```


#### Protocols con propiedades

```swift
protocol Identificable {
    var id: String { get }  // Solo lectura
    var nombre: String { get set }  // Lectura y escritura
}

struct Usuario: Identificable {
    let id: String  // Como es { get }, puede ser let
    var nombre: String  // Debe ser var porque es { get set }
}
```


#### Herencia de protocols

```swift
protocol Nombrable {
    var nombre: String { get }
}

protocol Identificable: Nombrable {
    var id: Int { get }
}

struct Producto: Identificable {
    var id: Int
    var nombre: String
    var precio: Double
}
```


#### Múltiples protocols

```swift
protocol Dibujable {
    func dibujar()
}

protocol Animable {
    func animar()
}

class Sprite: Dibujable, Animable {
    func dibujar() {
        print("Dibujando sprite...")
    }
    
    func animar() {
        print("Animando sprite...")
    }
}
```


### 10.6. Propiedades y métodos de tipo (static/class)

#### Propiedades estáticas

```swift
struct Matematicas {
    static let pi = 3.141592653589793
    static let e = 2.718281828459045
    
    static func factorial(_ n: Int) -> Int {
        guard n > 0 else { return 1 }
        return n * factorial(n - 1)
    }
}

print(Matematicas.pi)  // 3.141592653589793
print(Matematicas.factorial(5))  // 120
```


#### Diferencia entre `static` y `class`

```swift
class Base {
    // 'static' no puede sobrescribirse
    static func metodoEstatico() {
        print("Método estático")
    }
    
    // 'class' puede sobrescribirse
    class func metodoClase() {
        print("Método de clase")
    }
}

class Derivada: Base {
    // static override metodoEstatico() { }  // ❌ ERROR
    
    override class func metodoClase() {
        print("Método de clase sobrescrito")
    }
}

Base.metodoClase()      // "Método de clase"
Derivada.metodoClase()  // "Método de clase sobrescrito"
```


### 10.7. ¿Cuándo usar struct vs class?

| Usa **Struct** cuando | Usa **Class** cuando |
| :-- | :-- |
| Modelas datos simples sin identidad | Necesitas identidad única (referencia) |
| Quieres semántica de copia | Quieres compartir y modificar el mismo estado |
| Los datos son relativamente pequeños | Necesitas herencia |
| No necesitas herencia | Trabajas con APIs de Objective-C/UIKit |
| Prefieres inmutabilidad | Necesitas desinicializadores (`deinit`) |

> [!TIP]
> **Regla general en Swift:** Usa struct por defecto. Cambia a class solo cuando necesites herencia o semántica de referencia.

***

## 11. Extensions básicas

Las extensions permiten añadir funcionalidad a tipos existentes sin modificar su código original ni heredar de ellos.

### 11.1. Extender tipos propios

```swift
struct Rectangulo {
    var ancho: Double
    var alto: Double
}

extension Rectangulo {
    var area: Double {
        return ancho * alto
    }
    
    var perimetro: Double {
        return 2 * (ancho + alto)
    }
    
    func escalar(factor: Double) -> Rectangulo {
        return Rectangulo(ancho: ancho * factor, alto: alto * factor)
    }
}

let rect = Rectangulo(ancho: 10, alto: 5)
print(rect.area)  // 50.0
let rectGrande = rect.escalar(factor: 2)
print(rectGrande.ancho)  // 20.0
```


### 11.2. Extender tipos de la biblioteca estándar

```swift
extension Int {
    var cuadrado: Int {
        return self * self
    }
    
    var esPar: Bool {
        return self % 2 == 0
    }
    
    func veces(accion: () -> Void) {
        for _ in 0..<self {
            accion()
        }
    }
}

print(5.cuadrado)  // 25
print(8.esPar)     // true

3.veces {
    print("Hola")
}
// Hola
// Hola
// Hola
```


### 11.3. Conformar a protocols mediante extensions

```swift
protocol Descriptible {
    func describir() -> String
}

struct Punto {
    var x: Double
    var y: Double
}

// Añadimos conformidad a Descriptible mediante extension
extension Punto: Descriptible {
    func describir() -> String {
        return "Punto en (\(x), \(y))"
    }
}

let punto = Punto(x: 3, y: 4)
print(punto.describir())  // "Punto en (3.0, 4.0)"
```

> [!NOTE]
> Las extensions son una herramienta poderosa que permite mantener el código organizado. Puedes tener la definición básica de un tipo en un archivo y las conformidades a diferentes protocols en extensions separadas.

***

## 12. Manejo de errores básico

Swift utiliza un sistema de manejo de errores estructurado basado en el protocol `Error`.

### 12.1. Definir errores

```swift
enum ErrorCuenta: Error {
    case saldoInsuficiente
    case cuentaBloqueada
    case limiteExcedido(limite: Double)
}
```


### 12.2. Lanzar errores con `throw`

Las funciones que pueden lanzar errores deben marcarse con `throws`:

```swift
class CuentaBancaria {
    var saldo: Double
    var bloqueada: Bool = false
    let limiteRetiro: Double = 500
    
    init(saldo: Double) {
        self.saldo = saldo
    }
    
    func retirar(cantidad: Double) throws {
        guard !bloqueada else {
            throw ErrorCuenta.cuentaBloqueada
        }
        
        guard cantidad <= limiteRetiro else {
            throw ErrorCuenta.limiteExcedido(limite: limiteRetiro)
        }
        
        guard cantidad <= saldo else {
            throw ErrorCuenta.saldoInsuficiente
        }
        
        saldo -= cantidad
        print("Retirados \(cantidad)€. Saldo: \(saldo)€")
    }
}
```


### 12.3. Capturar errores con `do-catch`

```swift
let cuenta = CuentaBancaria(saldo: 1000)

do {
    try cuenta.retirar(cantidad: 200)
    print("Operación exitosa")
} catch ErrorCuenta.saldoInsuficiente {
    print("Error: No tienes suficiente saldo")
} catch ErrorCuenta.cuentaBloqueada {
    print("Error: La cuenta está bloqueada")
} catch ErrorCuenta.limiteExcedido(let limite) {
    print("Error: No puedes retirar más de \(limite)€")
} catch {
    print("Error desconocido: \(error)")
}
```


### 12.4. Propagación de errores

Si no quieres manejar el error en la función actual, puedes propagarlo añadiendo `throws`:

```swift
func procesarTransaccion(cuenta: CuentaBancaria, cantidad: Double) throws {
    try cuenta.retirar(cantidad: cantidad)
    // Si retirar lanza un error, esta función lo propaga automáticamente
    print("Transacción completada")
}

// Quien llame a procesarTransaccion debe manejar el error
do {
    try procesarTransaccion(cuenta: cuenta, cantidad: 300)
} catch {
    print("Error en la transacción: \(error)")
}
```


### 12.5. Alternativas: `try?` y `try!`

```swift
// try? convierte el resultado en opcional (nil si hay error)
let resultado1 = try? cuenta.retirar(cantidad: 100)
// resultado1 es Void? - nil si hubo error, () si fue exitoso

// try! fuerza la ejecución (crashea si hay error)
// try! cuenta.retirar(cantidad: 10000)  // 💥 Crashea

// try? es útil cuando no te importa el tipo específico de error
if let _ = try? cuenta.retirar(cantidad: 50) {
    print("Éxito")
} else {
    print("Falló por alguna razón")
}
```


### 12.6. Statement `defer`

`defer` ejecuta código justo antes de salir del scope actual, independientemente de cómo salgas (return, throw, break):

```swift
func procesarArchivo(nombre: String) throws {
    print("Abriendo archivo \(nombre)")
    
    defer {
        print("Cerrando archivo \(nombre)")
        // Este código se ejecuta SIEMPRE antes de salir
    }
    
    // Simulamos procesamiento
    guard nombre != "corrupto.txt" else {
        throw ErrorCuenta.cuentaBloqueada  // defer se ejecuta antes del throw
    }
    
    print("Procesando archivo...")
}

do {
    try procesarArchivo(nombre: "datos.txt")
} catch {
    print("Error: \(error)")
}
// Imprime:
// Abriendo archivo datos.txt
// Procesando archivo...
// Cerrando archivo datos.txt

do {
    try procesarArchivo(nombre: "corrupto.txt")
} catch {
    print("Error al procesar")
}
// Imprime:
// Abriendo archivo corrupto.txt
// Cerrando archivo corrupto.txt
// Error al procesar
```

> [!TIP]
> `defer` es ideal para garantizar limpieza de recursos (cerrar archivos, conexiones, liberar locks) sin importar cómo salga la función.

***

## 13. Modificadores de acceso

Swift proporciona cinco niveles de control de acceso para encapsular implementación:

### 13.1. Niveles de acceso

```swift
// open: Accesible desde cualquier módulo, puede heredarse/sobrescribirse
open class VehiculoPublico {
    open func arrancar() { }
}

// public: Accesible desde cualquier módulo, NO puede heredarse (solo en su módulo)
public class Utilidad {
    public static func helper() { }
}

// internal: Accesible solo dentro del mismo módulo (DEFAULT)
internal class ServicioInterno {
    func procesar() { }
}

// fileprivate: Accesible solo dentro del mismo archivo
fileprivate class HelperPrivado {
    func ayudar() { }
}

// private: Accesible solo dentro de la misma declaración (y extensions en el mismo archivo)
class Persona {
    private var password: String = ""
    
    func cambiarPassword(nueva: String) {
        password = nueva  // OK, mismo contexto
    }
}
```


### 13.2. Ejemplo práctico

```swift
public class CuentaBancaria {
    // Propiedad privada, no accesible desde fuera
    private var saldo: Double
    
    // Propiedad pública de solo lectura
    public var titular: String
    
    // Inicializador público
    public init(titular: String, saldoInicial: Double) {
        self.titular = titular
        self.saldo = saldoInicial
    }
    
    // Método público
    public func consultarSaldo() -> Double {
        return saldo
    }
    
    // Método interno (usado por otras clases del módulo)
    internal func ajustarSaldo(_ cantidad: Double) {
        saldo += cantidad
    }
    
    // Método privado (solo esta clase)
    private func validarOperacion(_ cantidad: Double) -> Bool {
        return cantidad > 0 && cantidad <= saldo
    }
    
    public func retirar(_ cantidad: Double) -> Bool {
        guard validarOperacion(cantidad) else {
            return false
        }
        saldo -= cantidad
        return true
    }
}

let cuenta = CuentaBancaria(titular: "Ana", saldoInicial: 1000)
print(cuenta.consultarSaldo())  // 1000.0
// print(cuenta.saldo)  // ❌ ERROR: 'saldo' is inaccessible due to 'private' protection level
```


### 13.3. Guía de uso

| Modificador | ¿Cuándo usarlo? |
| :-- | :-- |
| `open` | APIs públicas de frameworks que otros deben poder subclasificar |
| `public` | APIs públicas que no deben subclasificarse externamente |
| `internal` | Implementación interna del módulo (default, no necesitas escribirlo) |
| `fileprivate` | Compartir entre tipos en el mismo archivo |
| `private` | Encapsular detalles de implementación de un tipo específico |

> [!NOTE]
> A diferencia de Java con `protected`, Swift no tiene un modificador para "visible en subclases". Usa `internal` o `public` según necesites.

***

## 14. Comparación final: Swift vs Java

| Concepto | Java | Swift |
| :-- | :-- | :-- |
| **Ejecución** | JVM (bytecode) | Código nativo (LLVM) |
| **Memoria** | Garbage Collection | ARC (Automatic Reference Counting) |
| **Null safety** | Cualquier objeto puede ser `null` | Sistema de opcionales (`?`) obligatorio |
| **Inmutabilidad** | `final` (opcional) | `let` (por defecto) |
| **Tipos primitivos** | `int`, `double` separados de objetos | Todo son tipos de primera clase |
| **String** | Objeto inmutable | Struct mutable con semántica de valor |
| **Arrays** | `int[]` primitivos o `ArrayList<Integer>` | `[Int]` unificado |
| **Clases** | Todo son clases | Clases + Structs + Enums |
| **Herencia** | Cualquier clase puede heredarse | Clases `open` o `public` explícitamente |
| **Interfaces** | `interface` | `protocol` |
| **Métodos estáticos** | `static` | `static` (structs/enums) o `class` (clases) |
| **Excepciones** | Checked y unchecked | Solo errores `throws` explícitos |
| **Switch** | Necesita `break`, solo valores constantes | Sin `break`, soporta rangos/tuplas/patterns |
| **Propiedades** | Getters/setters explícitos | Propiedades computadas y observadas |
| **Extensions** | No soportadas (excepto herencia) | Pueden extender cualquier tipo |
| **Genéricos** | Type erasure | Reified (mantienen tipo en runtime) |
| **Inferencia de tipos** | Limitada (desde Java 10) | Completa y sofisticada |
| **Sobrecarga** | Por número/tipo de parámetros | Por argument labels también |
| **Constructores** | Nombre de la clase | `init` |
| **Package/módulo** | `package` | `import ModuleName` |


***

## 15. Actividades prácticas

### Actividad 1: El poder de los opcionales

**Objetivo:** Comprender el sistema de opcionales y su gestión segura.

```swift
// Completa las siguientes funciones usando diferentes técnicas de unwrapping

// 1. Usando if let
func obtenerLongitud(texto: String?) -> String {
    // Devuelve "El texto tiene X caracteres" o "No hay texto"
    // TU CÓDIGO AQUÍ
}

// 2. Usando guard let
func procesarNombre(nombre: String?) {
    // Si nombre es nil, imprime error y sal
    // Si existe, imprímelo en mayúsculas
    // TU CÓDIGO AQUÍ
}

// 3. Usando nil coalescing
func saludar(nombre: String?) -> String {
    // Devuelve "Hola, [nombre]" o "Hola, Invitado" si es nil
    // TU CÓDIGO AQUÍ
}

// 4. Usando optional chaining
struct Direccion {
    var calle: String
}

struct Usuario {
    var nombre: String
    var direccion: Direccion?
}

func obtenerCalle(usuario: Usuario?) -> String {
    // Devuelve la calle del usuario o "Dirección desconocida"
    // TU CÓDIGO AQUÍ
}

// Pruebas
print(obtenerLongitud(texto: "Swift"))  // "El texto tiene 5 caracteres"
print(obtenerLongitud(texto: nil))      // "No hay texto"
procesarNombre(nombre: "ana")           // "ANA"
procesarNombre(nombre: nil)             // "Error: nombre no proporcionado"
print(saludar(nombre: "Luis"))          // "Hola, Luis"
print(saludar(nombre: nil))             // "Hola, Invitado"
```

**Solución:**

```swift
func obtenerLongitud(texto: String?) -> String {
    if let t = texto {
        return "El texto tiene \(t.count) caracteres"
    } else {
        return "No hay texto"
    }
}

func procesarNombre(nombre: String?) {
    guard let n = nombre else {
        print("Error: nombre no proporcionado")
        return
    }
    print(n.uppercased())
}

func saludar(nombre: String?) -> String {
    return "Hola, \(nombre ?? "Invitado")"
}

func obtenerCalle(usuario: Usuario?) -> String {
    return usuario?.direccion?.calle ?? "Dirección desconocida"
}
```


***

### Actividad 2: Structs vs Classes

**Objetivo:** Entender la diferencia entre tipos de valor y referencia.

```swift
// Experimenta con este código y predice qué se imprimirá

struct PuntoStruct {
    var x: Int
    var y: Int
}

class PuntoClass {
    var x: Int
    var y: Int
    
    init(x: Int, y: Int) {
        self.x = x
        self.y = y
    }
}

// Experimento 1: Structs
var puntoA = PuntoStruct(x: 10, y: 20)
var puntoB = puntoA
puntoB.x = 999

print("Struct - puntoA.x: \(puntoA.x)")  // ¿Qué imprime?
print("Struct - puntoB.x: \(puntoB.x)")  // ¿Qué imprime?

// Experimento 2: Classes
var puntoC = PuntoClass(x: 10, y: 20)
var puntoD = puntoC
puntoD.x = 999

print("Class - puntoC.x: \(puntoC.x)")  // ¿Qué imprime?
print("Class - puntoD.x: \(puntoD.x)")  // ¿Qué imprime?

// Desafío: Crea un struct Rectangulo con width y height
// Añade un método mutating para escalar el rectángulo
// Añade una propiedad computada para calcular el área
```

**Respuestas esperadas:**

- Struct puntoA.x: **10** (no cambió, se copió el valor)
- Struct puntoB.x: **999** (cambió su copia)
- Class puntoC.x: **999** (cambió porque comparten referencia)
- Class puntoD.x: **999** (misma instancia)

***

### Actividad 3: Switch potente

**Objetivo:** Dominar el pattern matching con switch.

```swift
// Crea una función que clasifique coordenadas

func clasificarPunto(_ punto: (x: Int, y: Int)) {
    switch punto {
    case (0, 0):
        print("Origen")
    case (_, 0):
        print("Sobre el eje X")
    case (0, _):
        print("Sobre el eje Y")
    case (-10...10, -10...10):
        print("Cerca del origen")
    case let (x, y) where x == y:
        print("Sobre la diagonal principal en (\(x), \(y))")
    case let (x, y) where x == -y:
        print("Sobre la diagonal secundaria en (\(x), \(y))")
    default:
        print("Lejos del origen")
    }
}

// Pruebas
clasificarPunto((0, 0))     // "Origen"
clasificarPunto((5, 0))     // "Sobre el eje X"
clasificarPunto((5, 5))     // "Sobre la diagonal principal en (5, 5)"
clasificarPunto((15, 15))   // "Sobre la diagonal principal en (15, 15)"
clasificarPunto((100, 200)) // "Lejos del origen"
```

**Desafío:** Crea un sistema de clasificación de notas usando switch con rangos que también identifique "matrícula de honor" (exactamente 10).

***

### Actividad 4: Calculadora con enums

**Objetivo:** Usar enumeraciones con métodos y associated values.

```swift
enum Operacion {
    case suma
    case resta
    case multiplicacion
    case division
    case potencia(exponente: Int)
    
    func aplicar(a: Double, b: Double) -> Double {
        // IMPLEMENTA ESTE MÉTODO
        // Para potencia, usa pow(a, Double(exponente))
    }
    
    var simbolo: String {
        // IMPLEMENTA ESTA PROPIEDAD COMPUTADA
    }
}

// Pruebas
let ops: [Operacion] = [
    .suma,
    .multiplicacion,
    .division,
    .potencia(exponente: 3)
]

for op in ops {
    let resultado = op.aplicar(a: 4, b: 2)
    print("\(op.simbolo): \(resultado)")
}

// Salida esperada:
// +: 6.0
// ×: 8.0
// ÷: 2.0
// ^3: 64.0
```


***

### Actividad 5: Sistema de estudiantes con protocols

**Objetivo:** Crear una jerarquía con herencia y protocols.

```swift
protocol Evaluable {
    var notas: [Double] { get set }
    func calcularMedia() -> Double
    func aprobado() -> Bool
}

class Persona {
    var nombre: String
    var edad: Int
    
    init(nombre: String, edad: Int) {
        self.nombre = nombre
        self.edad = edad
    }
}

class Estudiante: Persona, Evaluable {
    var notas: [Double] = []
    var curso: String
    
    init(nombre: String, edad: Int, curso: String) {
        self.curso = curso
        super.init(nombre: nombre, edad: edad)
    }
    
    func calcularMedia() -> Double {
        // IMPLEMENTA: Calcula la media de las notas
        // Si no hay notas, devuelve 0
    }
    
    func aprobado() -> Bool {
        // IMPLEMENTA: Devuelve true si la media es >= 5
    }
    
    override func presentarse() {
        // IMPLEMENTA: Sobrescribe para incluir curso
    }
}

// Añade un método a Persona
extension Persona {
    func presentarse() {
        print("Hola, soy \(nombre) y tengo \(edad) años")
    }
}

// Pruebas
let estudiante = Estudiante(nombre: "Ana", edad: 20, curso: "CFGS DAM")
estudiante.notas = [7.5, 8.0, 9.0, 6.5]
print(estudiante.calcularMedia())  // 7.75
print(estudiante.aprobado())       // true
estudiante.presentarse()           // "Hola, soy Ana, tengo 20 años y estudio CFGS DAM"
```


***

### Actividad 6: Sistema de manejo de errores

**Objetivo:** Implementar un sistema completo de validación con errores.

```swift
enum ErrorValidacion: Error {
    case nombreVacio
    case edadInvalida
    case emailInvalido
}

struct Registro {
    var nombre: String
    var edad: Int
    var email: String
    
    init(nombre: String, edad: Int, email: String) throws {
        // Valida que nombre no esté vacío
        guard !nombre.isEmpty else {
            throw ErrorValidacion.nombreVacio
        }
        
        // Valida que edad esté entre 16 y 100
        guard edad >= 16 && edad <= 100 else {
            throw ErrorValidacion.edadInvalida
        }
        
        // Valida que email contenga @
        guard email.contains("@") else {
            throw ErrorValidacion.emailInvalido
        }
        
        self.nombre = nombre
        self.edad = edad
        self.email = email
    }
}

// Prueba el sistema
do {
    let registro1 = try Registro(nombre: "Ana", edad: 25, email: "ana@example.com")
    print("Registro exitoso: \(registro1.nombre)")
} catch ErrorValidacion.nombreVacio {
    print("Error: El nombre no puede estar vacío")
} catch ErrorValidacion.edadInvalida {
    print("Error: Edad debe estar entre 16 y 100")
} catch ErrorValidacion.emailInvalido {
    print("Error: Email inválido")
} catch {
    print("Error desconocido")
}

// Prueba con datos inválidos
let registroInvalido = try? Registro(nombre: "", edad: 15, email: "invalido")
print(registroInvalido)  // nil
```


***

### Proyecto final: Sistema de biblioteca

**Objetivo:** Integrar todos los conceptos aprendidos.

```swift
// Crea un sistema de gestión de biblioteca que incluya:

// 1. Protocol Prestable
protocol Prestable {
    var titulo: String { get }
    var disponible: Bool { get set }
    func prestar() -> Bool
    func devolver()
}

// 2. Enum para tipos de material
enum TipoMaterial {
    case libro, revista, dvd
}

// 3. Struct Material que implemente Prestable
struct Material: Prestable {
    let titulo: String
    let tipo: TipoMaterial
    var disponible: Bool
    
    mutating func prestar() -> Bool {
        guard disponible else { return false }
        disponible = false
        return true
    }
    
    mutating func devolver() {
        disponible = true
    }
}

// 4. Class Biblioteca
class Biblioteca {
    var materiales: [Material] = []
    
    func agregarMaterial(_ material: Material) {
        materiales.append(material)
    }
    
    func buscar(titulo: String) -> Material? {
        // IMPLEMENTA: Busca un material por título
    }
    
    func materialesDisponibles() -> [Material] {
        // IMPLEMENTA: Devuelve solo materiales disponibles
    }
    
    func intentarPrestar(titulo: String) throws {
        // IMPLEMENTA: Intenta prestar un material
        // Lanza error si no existe o no está disponible
    }
}

// Prueba el sistema completo
let biblioteca = Biblioteca()
biblioteca.agregarMaterial(Material(titulo: "1984", tipo: .libro, disponible: true))
biblioteca.agregarMaterial(Material(titulo: "El Quijote", tipo: .libro, disponible: true))

do {
    try biblioteca.intentarPrestar(titulo: "1984")
    print("Libro prestado exitosamente")
    
    // Intentar prestar el mismo libro otra vez
    try biblioteca.intentarPrestar(titulo: "1984")
} catch {
    print("Error: El libro no está disponible")
}
```


***

## 16. Recursos adicionales y siguientes pasos

### Documentación oficial

- **Swift.org:** Documentación oficial y guías
- **Apple Developer:** APIs de iOS y frameworks
- **Swift Forums:** Comunidad oficial


### Práctica online

- **Swift Playgrounds (iPad/Mac):** Entorno interactivo de aprendizaje
- **HackerRank Swift:** Ejercicios de programación
- **LeetCode:** Problemas algorítmicos en Swift


### Próximos temas

En el **Tema 14** aprenderás Kotlin, que comparte muchas similitudes con Swift (null safety, inmutabilidad por defecto, funciones de primera clase). En el **Tema 15** profundizarás en programación funcional avanzada comparando ambos lenguajes, y explorarás sistemas modernos de persistencia como Room y Core Data.

***

## Resumen del tema

Has aprendido los fundamentos de Swift necesarios para el desarrollo iOS:

✅ **Sistema de tipos:** Inferencia, opcionales, tuplas
✅ **Colecciones:** Arrays, Diccionarios, Sets con mutabilidad controlada
✅ **Control de flujo:** Switch potente, bucles con rangos
✅ **Funciones:** Argument labels, valores por defecto, funciones de orden superior
✅ **Enumeraciones:** Con métodos, raw values y associated values
✅ **POO:** Structs vs Classes, herencia, polimorfismo, protocols
✅ **Extensions:** Añadir funcionalidad a tipos existentes
✅ **Manejo de errores:** Sistema estructurado con throws/catch

***

## Autoevaluación

Completa este cuestionario para comprobar que has asimilado los conceptos fundamentales de Swift antes de pasar al siguiente tema.

**1. ¿Cuál es la diferencia principal entre `let` y `var` en Swift?**
- a) `let` es para enteros y `var` para cadenas
- b) `let` declara constantes inmutables y `var` variables mutables
- c) `let` es más rápido que `var`
- d) No hay diferencia, son intercambiables

**2. ¿Qué imprime el siguiente código Swift?**
```swift
var nombre: String? = nil
print(nombre ?? "Invitado")
```
- a) nil
- b) Error de compilación
- c) Invitado
- d) Optional(nil)

**3. ¿Cuál es la forma MÁS segura de desempaquetar un opcional en Swift?**
- a) Usar `!` (desempaquetado forzado)
- b) Usar `if let` (binding seguro)
- c) Ignorar el opcional y usarlo directamente
- d) Convertirlo a String

**4. ¿Qué tipo de dato infiere Swift en esta declaración?**
```swift
let temperatura = 36.5
```
- a) Float
- b) Int
- c) Double
- d) Decimal

**5. En Swift, ¿cuál es la diferencia principal entre `struct` y `class`?**
- a) Los structs no pueden tener métodos
- b) Los structs son tipos de valor (se copian), las clases son tipos de referencia
- c) Las clases no pueden heredar
- d) No hay diferencia significativa

**6. ¿Qué devuelve el operador `as?` en Swift si el casting falla?**
- a) Lanza una excepción
- b) Devuelve nil
- c) Crashea la aplicación
- d) Devuelve el tipo original

**7. ¿Cuál es el resultado de este código?**
```swift
let numeros = [1, 2, 3, 4, 5]
let resultado = numeros.filter { $0 % 2 == 0 }.map { $0 * 2 }
```
- a)[2]
- b) 
- c)[3][1][2]
- d) Error de compilación

**8. ¿Qué palabra clave se usa en Swift para validar condiciones al inicio de una función y salir anticipadamente si fallan?**
- a) if let
- b) guard
- c) check
- d) validate

**9. En Swift, ¿qué keyword se usa para propagar errores desde una función sin capturarlos?**
- a) throw
- b) throws
- c) error
- d) raise

**10. ¿Cuál de estas afirmaciones sobre los enum en Swift es INCORRECTA?**
- a) Pueden tener métodos asociados
- b) Pueden tener raw values (valores brutos)
- c) Pueden tener associated values (valores asociados)
- d) Solo pueden contener valores numéricos

**Respuestas correctas:** 1-b, 2-c, 3-b, 4-c, 5-b, 6-b, 7-b, 8-b, 9-b, 10-d

***