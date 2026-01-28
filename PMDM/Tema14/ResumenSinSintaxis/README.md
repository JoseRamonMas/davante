# TEMA 14 (SIN SINTAXIS): Kotlin: La evolución moderna de Java

## Índice

1. [Introducción: ¿Por qué estamos aquí?](#1-introducci%C3%B3n-por-qu%C3%A9-estamos-aqu%C3%AD)
    1. [El problema de Java](#11-el-problema-de-java)
    2. [La solución: Kotlin](#12-la-soluci%C3%B3n-kotlin)
    3. [Interoperabilidad total](#13-interoperabilidad-total)

2. [El fin del "Error del Billón de Dólares": Null Safety](#2-el-fin-del-error-del-bill%C3%B3n-de-d%C3%B3lares-null-safety)
    1. [El peligro de la referencia nula](#21-el-peligro-de-la-referencia-nula)
    2. [Sistema de tipos seguro](#22-sistema-de-tipos-seguro)
    3. [Comparativa visual: Java vs Kotlin](#23-comparativa-visual-java-vs-kotlin)

3. [Muerte al código "Boilerplate"](#3-muerte-al-c%C3%B3digo-boilerplate)
    1. [El peso de las clases de datos en Java](#31-el-peso-de-las-clases-de-datos-en-java)
    2. [La elegancia de las Data Classes](#32-la-elegancia-de-las-data-classes)

4. [Superpoderes modernos](#4-superpoderes-modernos)
    1. [Inmutabilidad por defecto (`val` vs `var`)](#41-inmutabilidad-por-defecto-val-vs-var)
    2. [Extensions: Mejorando lo ajeno](#42-extensions-mejorando-lo-ajeno)
    3. [Expresividad: String Templates](#43-expresividad-string-templates)

5. [Introducción al pensamiento funcional](#5-introducci%C3%B3n-al-pensamiento-funcional)
    1. [Imperativo vs Declarativo](#51-imperativo-vs-declarativo)
    2. [Tuberías de datos](#52-tuber%C3%ADas-de-datos)

6. [Conclusión: Tu futuro con Kotlin](#6-conclusi%C3%B3n-tu-futuro-con-kotlin)
7. [Actividades de análisis](#7-actividades-de-an%C3%A1lisis)

---

## 1. Introducción: ¿Por qué estamos aquí?

Hasta ahora habéis trabajado duro con **Java**. Es un lenguaje legendario, robusto y omnipresente. Sin embargo, también habéis notado que a veces se siente "viejo": requiere escribir mucho código para hacer cosas sencillas y es fácil cometer errores que solo se descubren cuando la aplicación falla (crashea).

### 1.1. El problema de Java

Java fue diseñado en los 90. Sus problemas principales hoy en día son:

* **Verbosidad:** Necesitas escribir muchas líneas de código "ceremonial" (boilerplate) que no aportan lógica real.
* **Inseguridad:** El sistema permite que cualquier objeto sea `null`, provocando el temido `NullPointerException`.
* **Evolución lenta:** Aunque ha mejorado, arrastra mucha herencia del pasado para mantener compatibilidad.

### 1.2. La solución: Kotlin

Kotlin no nació en una universidad, sino en la industria. Fue creado por **JetBrains** (los creadores de IntelliJ y Android Studio) con un objetivo pragmático: **hacer un Java mejor, más seguro y más conciso**.

En 2017, Google anunció que Kotlin sería un lenguaje oficial para Android, y en 2019 lo declaró el lenguaje **preferido**.

> [!IMPORTANT]
> Kotlin no es un lenguaje "raro" ni académico. Es la respuesta de la industria moderna a los dolores de cabeza que sufrimos con Java. Si sabéis Java y Swift, ya entendéis el 80% de Kotlin.

### 1.3. Interoperabilidad total

La característica más brillante de Kotlin es que **es 100% compatible con Java**.

* Puedes tener un proyecto con archivos `.java` y `.kt` mezclados.
* Puedes llamar a código Java desde Kotlin y viceversa.
* No necesitas tirar tu código antiguo a la basura para empezar a usar Kotlin.

---

## 2. El fin del "Error del Billón de Dólares": Null Safety

Tony Hoare, el inventor de la referencia nula, llamó a su creación "el error del billón de dólares" debido a la cantidad de sistemas que han fallado y dinero perdido por culpa de errores de punteros nulos.

### 2.1. El peligro de la referencia nula

En Java, cualquier variable de objeto puede no tener valor (`null`). El compilador no te obliga a comprobarlo.

```java
// JAVA - El código compila, pero explota al ejecutarse
String usuario = obtenerUsuario(); // ¿Devuelve null? No lo sabemos.
System.out.println(usuario.toUpperCase()); // 💥 CRASH si usuario es null

```

### 2.2. Sistema de tipos seguro

Kotlin (al igual que Swift con sus Opcionales) integra la nulidad en el **sistema de tipos**.

* Una variable `String` **NUNCA** puede ser null.
* Si quieres que pueda ser null, debes declararla explícitamente como `String?`.

> [!NOTE]
> Observad la similitud con Swift. En Swift usáis `?` para los Optionals. En Kotlin se usa `?` para los tipos Nullable. El concepto es idéntico: una caja que puede estar vacía.

### 2.3. Comparativa visual: Java vs Kotlin

Mira cómo Kotlin elimina la "pirámide de la muerte" (anidación excesiva de `if`) usando el operador **Safe Call** (`?.`).

**El estilo defensivo de Java:**

```java
// Java
if (usuario != null) {
    if (usuario.direccion != null) {
        if (usuario.direccion.calle != null) {
            System.out.println(usuario.direccion.calle);
        }
    }
}

```

**El estilo moderno de Kotlin:**

```kotlin
// Kotlin
println(usuario?.direccion?.calle)

```

Si cualquiera de los pasos es `null`, la expresión completa devuelve `null` en lugar de romper el programa. Sin `if`, sin llaves, sin ruido.

---

## 3. Muerte al código "Boilerplate"

El "boilerplate" es ese código repetitivo y aburrido que tenemos que escribir en Java por obligación de la sintaxis, pero que no aporta valor al negocio.

### 3.1. El peso de las clases de datos en Java

Imaginad que queremos una clase simple para guardar datos de un `Usuario`. En Java necesitamos:

1. Definir campos privados.
2. Crear constructores.
3. Crear Getters y Setters para cada campo.
4. Sobrescribir `toString()` para poder imprimirlo.
5. Sobrescribir `equals()` y `hashCode()` para poder compararlos.

**Resultado:** Más de 50 líneas de código para algo trivial.

### 3.2. La elegancia de las Data Classes

Kotlin introduce el concepto de `data class` (muy similar a los `struct` de Swift). En **una sola línea**, el compilador genera todo lo anterior automáticamente.

```kotlin
// Kotlin: Todo lo que necesitas
data class Usuario(val nombre: String, val edad: Int, val email: String)

```

> [!TIP]
> **Menos código es mejor código.**
> Menos líneas significan menos superficie para esconder bugs y mucho menos tiempo leyendo código irrelevante.

---

## 4. Superpoderes modernos

Kotlin adopta características que facilitan la vida del desarrollador y hacen el código más robusto.

### 4.1. Inmutabilidad por defecto (`val` vs `var`)

Al igual que en Swift (`let` vs `var`), Kotlin distingue fuertemente entre referencias de solo lectura y referencias mutables.

| Lenguaje | Constante (Inmutable) | Variable (Mutable) | Recomendación |
| --- | --- | --- | --- |
| **Java** | `final String x = "..."` | `String x = "..."` | Java tiende a mutabilidad por defecto. |
| **Swift** | `let x = "..."` | `var x = "..."` | Usar `let` siempre que sea posible. |
| **Kotlin** | `val x = "..."` | `var x = "..."` | Usar `val` siempre que sea posible. |

**¿Por qué es importante?**
Si usas `val`, garantizas que el valor no cambiará inesperadamente en otra parte del código. Esto es vital para evitar errores lógicos y problemas en aplicaciones multihilo.

### 4.2. Extensions: Mejorando lo ajeno

¿Alguna vez has querido añadir un método a la clase `String` de Java? No puedes, porque es una clase final que no te pertenece. En Java, acabamos creando clases feas llamadas `StringUtils` o `Helpers`.

Kotlin permite **Extension Functions**: añadir funciones a clases existentes sin heredarlas.

```kotlin
// Definimos una extensión sobre String
fun String.esEmailValido(): Boolean {
    return this.contains("@") && this.contains(".")
}

// Uso (parece un método nativo del lenguaje)
val email = "alumno@fp.com"
if (email.esEmailValido()) {
    println("Correcto")
}

```

### 4.3. Expresividad: String Templates

Despedíos de concatenar con `+` o usar `String.format`. Kotlin permite inyectar variables directamente en el texto.

```kotlin
val nombre = "Ana"
val edad = 25
// Kotlin
println("Hola, me llamo $nombre y el año que viene tendré ${edad + 1}")

```

---

## 5. Introducción al pensamiento funcional

Aunque veremos esto a fondo en el Tema 15, es importante entender que Kotlin (al igual que Swift) cambia la forma de procesar datos: pasamos del estilo **imperativo** al **declarativo**.

### 5.1. Imperativo vs Declarativo

* **Imperativo (Java antiguo):** Te centras en el **CÓMO**. Micro-gestionas los bucles, los índices y los estados temporales.
* **Declarativo (Kotlin/Swift):** Te centras en el **QUÉ**. Describes la transformación de los datos.

### 5.2. Tuberías de datos

Imaginad que tenéis una lista de números y queréis:

1. Quedaros solo con los pares.
2. Multiplicarlos por 10.
3. Sumar el resultado.

**Enfoque Kotlin (y Swift):**

```kotlin
val numeros = listOf(1, 2, 3, 4, 5, 6)

val resultado = numeros
    .filter { it % 2 == 0 }  // 1. Filtra (Pares)
    .map { it * 10 }         // 2. Transforma (x10)
    .sum()                   // 3. Reduce (Suma)

println(resultado) // 120

```

> [!IMPORTANT]
> No hay bucles `for`, no hay variables temporales, no hay riesgo de salirnos del índice del array. Es una lectura natural de izquierda a derecha.

---

## 6. Conclusión: Tu futuro con Kotlin

Al terminar este curso, tendréis una base sólida en **Java** y en **Swift**. La buena noticia es que **Kotlin es el hijo prodigio de ambos mundos**:

* Tiene la potencia y el ecosistema de Java (JVM).
* Tiene la sintaxis moderna, segura y elegante de Swift.

Cuando salgáis al mercado laboral, especialmente en el desarrollo móvil (Android) o en el desarrollo backend moderno, os encontraréis con Kotlin. No os asustéis: **ya sabéis pensarlo, solo tendréis que adaptaros ligeramente a su "acento"**.

### Resumen de ventajas frente a Java:

1. **Seguridad:** Null Safety integrado.
2. **Concisión:** Mucho menos código para hacer lo mismo (Data Classes).
3. **Modernidad:** Lambdas, extensiones y programación funcional de serie.
4. **Interoperabilidad:** Funciona perfecto con todo el código Java existente.

---

## 7. Actividades de análisis

*Nota: Estas actividades no requieren escribir código desde cero, sino analizar y comprender la filosofía detrás del cambio.*

**Actividad 1: Detectando el peligro**
Observa el siguiente código Java. Identifica al menos 3 puntos donde podría producirse un `NullPointerException`. ¿Cómo lo solucionarías conceptualmente en Kotlin?

```java
public void procesarPedido(Pedido pedido) {
    String calle = pedido.getCliente().getDireccion().getCalle();
    System.out.println("Enviando a: " + calle);
}

```

**Actividad 2: Refactorización mental**
Tienes una clase Java `Producto` con 60 líneas de código (getters, setters, constructores...). Describe cómo sería su equivalente en Kotlin y qué ventajas obtendrías inmediatamente aparte del ahorro de líneas.

**Actividad 3: Inmutabilidad**
En el siguiente escenario, explica por qué usar `val` (inmutable) es más seguro que `var` (mutable):

* *Escenario:* Una aplicación bancaria descarga el saldo del usuario al iniciar sesión. Ese saldo se muestra en varias pantallas. ¿Debería guardarse en una variable `val` o `var`? ¿Qué riesgos hay si usamos la incorrecta?