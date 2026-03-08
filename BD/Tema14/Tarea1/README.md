
# 📝 TAREA 1 TCL: Conceptos básicos

**Instrucciones:** Visualiza la siguiente serie de tres vídeos introductorios sobre el Lenguaje de Control de Transacciones (TCL) y los problemas de acceso concurrente a los datos.

* Vídeo 1: [Lenguaje de Control de Transacciones (TCL)](https://www.youtube.com/watch?v=gjssTWHz36E)
* Vídeo 2: [Acceso concurrente a los datos (Teoría)](https://www.youtube.com/watch?v=FlZRYJX1zBY)
* Vídeo 3: [Acceso concurrente a los datos (Práctica)](https://www.youtube.com/watch?v=ky7zUJwfWQc)

Tras verlos, responde de forma breve y con tus propias palabras a las siguientes preguntas, divididas en una parte de comprensión conceptual y otra de investigación aplicada a nuestro entorno de trabajo actual.

## Parte 1: Comprensión de conceptos (basado en los vídeos)

1. **El concepto de Transacción y ACID:** Utilizando el ejemplo de la transferencia bancaria del primer vídeo, explica qué problema de consistencia ocurriría si el sistema se cayera a mitad de la operación. Relaciona esto explicando brevemente qué significan la "A" (Atomicidad) y la "I" (Aislamiento) de las propiedades ACID.
2. **Control parcial:** ¿Para qué sirve establecer un *Savepoint* (punto de guardado) dentro de una transacción en lugar de utilizar un `ROLLBACK` estándar?
3. **Problemas de concurrencia:** En los vídeos de concurrencia se explican tres problemas clásicos. Explica con un ejemplo inventado por ti qué es una **"Lectura sucia" (Dirty read)** y por qué es peligroso para la integridad de los datos.
4. **Bloqueos preventivos:** En el vídeo práctico, al usar el nivel de aislamiento *Serializable*, una de las sesiones se queda "bloqueada" al intentar hacer un `INSERT`. ¿Por qué ocurre esto y qué problema específico de concurrencia intenta evitar el SGBD?

## Parte 2: Adaptación a Oracle 21c

*Nota: Los vídeos están grabados utilizando el SGBD MariaDB (motor InnoDB). Este curso estamos trabajando con Oracle 21c. Aunque el estándar SQL es la base de ambos, la filosofía y gestión interna de las transacciones y la concurrencia cambia de un motor a otro. Investiga en la documentación de Oracle para responder:*

5. **Inicio de transacciones y AUTOCOMMIT:** En los vídeos se usan comandos como `BEGIN` o `START TRANSACTION` para iniciar explícitamente una transacción, y se menciona que el AUTOCOMMIT viene activado por defecto. Investiga cómo funciona esto en **Oracle 21c**:

    * ¿Existe un comando como `BEGIN` para iniciar una transacción o cómo sabe Oracle que ha empezado una?
    * Al usar herramientas como SQL*Plus o SQL Developer, ¿el AUTOCOMMIT está activado o desactivado por defecto?

6. **Niveles de aislamiento:** El estándar ANSI SQL define 4 niveles de aislamiento (los que se ven en el vídeo para MariaDB). Sin embargo, Oracle tiene una arquitectura multiversión muy robusta y gestiona esto de forma distinta.

    * ¿Cuáles son los **únicos dos niveles** del estándar SQL que Oracle permite configurar de forma estándar?
    * ¿Cuál es el nivel de aislamiento que Oracle utiliza **por defecto**? Sabiendo esto, ¿es posible que en Oracle ocurra el problema de las "lecturas sucias" por defecto?

## Formato de entrega

Una vez hayas redactado las respuestas a todas las preguntas, guarda tu documento y entrégalo en formato PDF con el nombre **TareaTema14-*TusApellidos*.pdf**.
