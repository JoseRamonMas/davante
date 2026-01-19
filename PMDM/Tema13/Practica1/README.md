# Tema 13. Fundamentos del lenguaje Swift

**Práctica 1: "Inmersión en Swift"**

## 1. Introducción

En esta unidad damos el salto al desarrollo nativo para plataformas Apple. Swift es un lenguaje moderno, seguro y potente que difiere de Java en aspectos fundamentales como la gestión de nulos y el tratamiento de la memoria.

Esta práctica consiste en una **batería de 20 ejercicios de codificación** diseñados para asentar la sintaxis. A diferencia de prácticas anteriores, los enunciados no te dirán *qué* instrucción usar, sino *qué* problema resolver. Como futuro desarrollador, deberás decidir si la solución requiere un `guard`, un `if let`, un `struct` o una `class`.

**⚠️ AVISO IMPORTANTE SOBRE LA AUTORÍA:**
El código de estos ejercicios es sencillo de generar mediante herramientas de IA. Por ello, **la entrega del archivo es solo un trámite administrativo**. La evaluación real de esta práctica será presencial: deberás demostrar al profesor que entiendes cada línea que has escrito, explicar el "porqué" de tus decisiones y realizar modificaciones en vivo sobre tu propio código.

## 2. Objetivos de aprendizaje

* Dominar la sintaxis básica y la inferencia de tipos.
* Gestionar la seguridad del código ("Null Safety") mediante el sistema de opcionales.
* Manipular estructuras de datos nativas y control de flujo avanzado.
* Diferenciar en la práctica el comportamiento de memoria entre Tipos de Valor y Tipos de Referencia.
* Modelar estados complejos mediante Enumeraciones con valores asociados.
* Implementar un manejo de errores robusto.

## 3. Descripción de la práctica

Debes crear un único archivo con extensión `.swift` (puedes llamarlo `Practica1.swift` o `main.swift`). Dentro de este archivo, resolverás los ejercicios secuencialmente.

**Requisito de formato:**
Debes separar cada ejercicio con un comentario visible (`// MARK: - EJERCICIO X`) para facilitar la corrección y la lectura en la revisión presencial.

---

### BLOQUE 1. Tipado y fundamentos

**Ejercicio 1. Cálculos y formateo**
Calcula el área de un círculo. Crea las constantes necesarias para un radio de 5 unidades. Imprime el resultado usando interpolación de cadenas: *"El área del círculo es [resultado]"*. Justo debajo imprime *"El área redondeada a dos decimales es [areaRedondeada]"* mostrando el resultado redondeado a dos decimales.

**Ejercicio 2. Conversión de tipos**
Declara tres variables: un entero (`10`), un decimal (`5.5`) y un string que contenga un número (`"20"`). Crea una cuarta variable `suma` que almacene la suma de los tres números. 

**Ejercicio 3. Tuplas**
Define una tupla que represente el estado de una conexión a servidor. Debe contener: un código numérico, un mensaje de estado y un booleano indicando si está activa. Accede e imprime únicamente el mensaje de estado utilizando su **etiqueta** (no el índice numérico).

---

### BLOQUE 2. Seguridad y opcionales

**Ejercicio 4. El concepto de nil**
Declara una cadena de texto opcional que inicialmente no tenga valor. Intenta imprimirla. Posteriormente, asígnale tu nombre. Vuelve a imprimirla. Observa y comenta brevemente en el código lo que aparece por consola y su diferencia con un String normal.

**Ejercicio 5. Desempaquetado seguro**
Tienes una variable `fotoPerfil: String?` que puede contener una URL o ser nula. Escribe la lógica necesaria para que:

* Si existe URL, se imprima "Cargando imagen de: [URL]".
* Si es nula, se imprima "Cargando imagen por defecto".
* **Requisito:** No puedes usar el operador de coalescencia (`??`) ni desempaquetado forzado (`!`).

**Ejercicio 6. Validación temprana**
Crea una función `login(usuario: String?, password: String?)`.

* La función debe comprobar primero si ambos parámetros tienen valor. Si alguno es nulo, debe imprimir "Datos incompletos" y abortar la ejecución inmediatamente.
* Si ambos tienen valor, imprime "Iniciando sesión...".
* **Requisito:** La estructura del código no debe anidarse (evita la "pirámide de la muerte").

**Ejercicio 7. Valores por defecto**
Tienes una lista de puntuaciones donde algunos valores son nulos: `[10, nil, 8, nil, 5]`. Calcula la suma total de los puntos, asumiendo que los `nil` cuentan como 0. Hazlo iterando el array y asegurando el valor en una sola línea de código dentro del bucle.

---

### BLOQUE 3. Colecciones y algoritmia

**Ejercicio 8. Manipulación de arrays**
Crea un array mutable de Strings con 5 nombres de ciudades. Realiza las siguientes operaciones secuenciales: Añade una nueva ciudad al final. Inserta otra ciudad en la posición 2. Elimina la última ciudad. Imprime la cantidad total de elementos.

**Ejercicio 9. Diccionarios**
 Crea un diccionario donde la clave sea el nombre de un alumno y el valor su nota. Incluye al menos 4 alumnos. Posteriormente, modifica la nota del segundo alumno. Usa un bucle `for-in` para recorrer el diccionario e imprimir: *"El alumno [Nombre] tiene un [Nota]"*.

**Ejercicio 10. Operaciones de conjuntos**
Crea un array con elementos repetidos: `"Java", "Swift", "Kotlin", "Java", "Swift"`. Crea un `Set` a partir de ese array para eliminar los duplicados automáticamente. Imprime el conjunto creado anteriormente. Finalmente comprueba si el set contiene "Python" (imprime true/false).
---

### BLOQUE 4. Control de flujo

**Ejercicio 11. Switch con rangos**
Crea una variable `edad`. Utiliza un `switch` para clasificar a la persona. Debe imprimir un texto según el rango de edad: "Bebé" (0-2 años), "Niño" (3-12), "Adolescente" (13-17) y "Adulto" (18+). Asegúrate de que el código sea exhaustivo.

**Ejercicio 12. Bucles condicionales**
Utiliza un bucle para imprimir los números del 1 al 20, pero excluyendo los múltiplos de 3. **Requisito:** Utiliza la cláusula `where`

---

### BLOQUE 5. Funciones

**Ejercicio 13. Argument Labels**
Crea una función para transferir dinero que reciba: cantidad, emisor y receptor.
Diseña la firma de la función para que al llamarla se lea exactamente así:
`transferir(50, de: "Cuenta1", a: "Cuenta2")`. En ese caso de llamada, función simplemente imprimirá "Transferencia de 50€ de la Cuenta1 a la Cuenta2"

**Ejercicio 14. Retorno de valores múltiples**
Crea una función analizarVentas que reciba un array de enteros [Int] que representa las ventas diarias de una tienda durante un periodo de tiempo. La función debe devolver una tupla con la siguiente información: `(maxima: Int, minima: Int, promedio: Double, diasPositivos: Int)`. Requisitos:

* `maxima`: el valor de ventas más alto.
* `minima`: el valor de ventas más bajo.
* `promedio`: la media aritmética de las ventas (usa Double).
* `diasPositivos`: número de días con ventas mayores que cero.

*Si el array está vacío, la función debe devolver nil (por tanto, la función devolverá una tupla opcional).*

**Ejercicio 15. Parámetros in-out**
Crea una función `intercambiar` que reciba dos enteros y permute sus valores (el valor de A pasa a B y viceversa). Comprueba que las variables originales fuera de la función han cambiado.

**Ejercicio 16. Funciones como parámetros**
Crea una función `mostrarSi` que reciba un array de valores Double y un predicado (función que toma un Double y devuelve un Bool). La función debe imprimir solo los elementos que cumplan la condición. Úsala por ejemplo para mostrar los valores positivos de [-3.5, 0.0, 2.1, 10.8, -1.2].

---

### BLOQUE 6. Enumeraciones

**Ejercicio 17. Enum**
 Crea un enum `DiaSemana` con los 7 días. Crea una función que reciba un `DiaSemana` y devuelva `true` si es fin de semana (sábado o domingo) y `false` en caso contrario. Comprueba que funciona imprimiendo para sábado y para lunes.

**Ejercicio 18. Valores asociados**
Crea un enum `MetodoPago` con los casos efectivo (sin valor asociado) y tarjeta (con un String asociado). Usa un switch para extraer e imprimir el número de tarjeta si corresponde (por ejemplo que el switch imprima "Pago en efectivo" si es efectivo o bien "Pago con tarjeta nº X" si es de tipo tarjeta).

---

### BLOQUE 7. Gestión de memoria y errores

**Ejercicio 19. Struct vs Class**
Diseña un experimento en código que demuestre la diferencia entre copiar un `struct`y un `class`

Crea un `struct` PersonaStruct y una `class` PersonaClass, ambos con propiedades `nombre` (String) y `edad` (Int). Instancia cada uno y asígnalos a nuevas variables (en total tendrás 4 variables). Luego, modifica las propiedades de las variables copiadas. Imprime los valores originales y observa qué ha cambiado. Explica en un comentario por qué ocurre esta diferencia.

**Ejercicio 20. Manejo de errores**
Diseña un sistema de venta de entradas.

* Define un `ErrorVenta` (enum que cumple con el protocolo `Error`) con casos: `entradasAgotadas` y `saldoInsuficiente`.
* Crea una clase `Taquilla` con atributos `entradasDisponibles` y `precioEntrada` con valores asociados por defecto; así como un método `vender(cantidad: Int, dinero: Double) throws`.
* Implementa la llamada a este método dentro de una estructura `do-catch` completa que capture y gestione cada tipo de error por separado.

---

## 4. Validación de autoría (fase obligatoria)

La entrega del fichero `.swift` en la plataforma es un **requisito indispensable** para ser evaluado, pero **no otorga nota por sí misma**.

Tras la fecha de entrega, se realizará una **defensa individual** en el aula donde el profesor revisará tu código contigo. La nota final de la práctica (0-10) se calculará estrictamente según la siguiente rúbrica durante dicha defensa:

### A. Modificación de código en vivo (4 puntos)

El profesor te solicitará realizar **modificaciones** sobre tu propio código en tiempo real.

* **4 ptos:** Realizas los cambios con soltura, sin errores de sintaxis y sin ayuda.
* **2-3 ptos:** Realizas los cambios pero con dudas, consultas o requieres una pequeña guía sintáctica.
* **0-1 ptos:** No eres capaz de escribir el código sin que se te dicte línea a línea o te bloqueas.

### B. Comprensión conceptual (5 puntos)

El profesor te formulará **preguntas** sobre la lógica de tu código (ej: *"¿Por qué aquí has usado un Struct y no una Class?"*, *"¿Qué pasa si esta variable vale nil?"*).

* **5 ptos:** Respondes correctamente a todas las preguntas usando vocabulario técnico preciso (*unwrapping, referencia, scope, etc.*).
* **3-4 ptos:** Entiendes el funcionamiento general pero fallas en explicaciones profundas o matices.
* **0-2 ptos:** Desconoces conceptos básicos o tus respuestas evidencian que no entiendes el código entregado.

### C. Calidad y limpieza (1 punto)

* **1 pto:** El código sigue las convenciones de Swift, usa nombres descriptivos, está bien indentado e incluye los comentarios de separación solicitados (`// MARK: - EJERCICIO X`).

---

### ☠️ Líneas rojas

Independientemente de la puntuación anterior, la práctica se calificará automáticamente con un suspenso si durante la defensa se detecta alguno de los siguientes fallos graves de base

1. **Desconocimiento del uso de `nil`**
2. **Ignorancia de mutabilidad**
3. **Errores de tipado**
4. **Falta de autoría**

---

### Resumen de Calificación (Semáforo)

* 🟢 **Semáforo Verde [8-10]:** Dominio total. Modificaciones rápidas y respuestas teóricas precisas.
* 🟡 **Semáforo Amarillo [5-7]:** Código funcional pero defensa dubitativa. Necesitas ayuda para modificarlo o te cuesta explicar conceptos clave.
* 🔴 **Semáforo Rojo [0-4]:** No defiendes el código. No superas las modificaciones o incurres en criterios de suspenso directo.

## 5. Entrega

Sube a la plataforma únicamente el siguiente archivo:

* **Archivo `.swift**` (ej: `Practica1.swift`) que contenga el código fuente de los 20 ejercicios, debidamente comentados y separados.
