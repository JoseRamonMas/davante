# Tema 12. Realización de pruebas

**Práctica 1: "Auditoría de calidad: La suite financiera FinanzasPro"**

## 1. Introducción

Tu consultora de calidad (QA) ha recibido un encargo urgente. El banco "FinanzasPro" está a punto de lanzar su nueva librería de cálculo de préstamos y gestión de cuentas, pero el desarrollador principal se ha marchado de la empresa dejando el código sin probar.

La dirección sospecha que el código compila bien, pero tiene **errores de cálculo financiero** y **brechas de seguridad** en la validación de usuarios.

No se trata solo de encontrar un fallo, sino de certificar la calidad del software. Deberás diseñar un plan de pruebas, implementar una batería de tests exhaustiva con JUnit (cubriendo casos borde y excepciones), documentar los bugs encontrados, corregir el código y demostrar mediante pruebas de regresión que el sistema es estable.

## 2. Objetivos de la práctica

* Analizar código de terceros para identificar puntos críticos susceptibles de fallo.
* Diseñar e implementar una suite de pruebas con **JUnit** que cubra al menos el 80% de los casos de uso (positivos, negativos y límites).
* Realizar pruebas de **caja blanca** (lógica interna) y **caja negra** (validación de requisitos).
* Evaluar el rendimiento del sistema ante cargas de trabajo elevadas.
* Documentar técnicamente el ciclo de vida del defecto: detección, reporte y resolución.

---

## 3. El código a auditar (SUT)

Prepara tu entorno. Crea un proyecto en Eclipse llamado `Practica1_Finanzas` y crea el paquete `com.finanzas.core`. Copia las siguientes tres clases.

**Nota:** Este código contiene errores funcionales, de seguridad y de rendimiento. **NO lo corrijas todavía**. Tu trabajo es escribir los tests que demuestren que este código falla.

**Clase 1: `CuentaBancaria.java**`

```java
package com.finanzas.core;

public class CuentaBancaria {
    private String titular;
    private double saldo;
    // Permite un descubierto (saldo negativo) hasta cierta cantidad
    private double limiteDescubierto; 

    public CuentaBancaria(String titular, double saldoInicial, double limiteDescubierto) {
        this.titular = titular;
        this.saldo = saldoInicial;
        this.limiteDescubierto = limiteDescubierto;
    }

    public void depositar(double cantidad) {
        // BUG POTENCIAL: No valida cantidades negativas
        this.saldo += cantidad;
    }

    public boolean retirar(double cantidad) {
        // La lógica debe permitir retirar si (saldo + limite) >= cantidad
        // BUG: La lógica está invertida o mal calculada
        if (saldo - cantidad < -limiteDescubierto) { 
            return false; // Fondos insuficientes
        }
        this.saldo -= cantidad;
        return true;
    }
    
    public boolean transferir(CuentaBancaria destino, double cantidad) {
        if (this.retirar(cantidad)) {
            destino.depositar(cantidad);
            return true;
        }
        return false;
    }

    public double getSaldo() {
        return saldo;
    }
}

```

**Clase 2: `CalculadoraPrestamos.java**`

```java
package com.finanzas.core;

public class CalculadoraPrestamos {

    /**
     * Calcula el interés total a pagar.
     * @param monto Cantidad solicitada
     * @param tipoInteres Porcentaje (ej: 5.0 para 5%)
     * @param años Duración
     */
    public double calcularInteresTotal(double monto, double tipoInteres, int años) {
        if (monto <= 0 || años <= 0) {
            throw new IllegalArgumentException("Datos inválidos");
        }
        
        // BUG: División entera inadvertida o fórmula simplista que ignora decimales pequeños
        // Al dividir tipoInteres (double) entre 100 (int), debería funcionar, 
        // pero ¿qué pasa si el interés es muy bajo o el cálculo se trunca en algún punto?
        // Audita si esta fórmula simple I = P * r * t es robusta.
        return monto * (tipoInteres / 100) * años;
    }
    
    /**
     * Determina si un cliente es apto para el préstamo.
     * Regla: Debe tener más de 18 años y sueldo anual >= 20.000
     */
    public boolean esElegible(int edad, double sueldoAnual) {
        // BUG: Confusión con los operadores lógicos o límites de edad
        return edad > 18 || sueldoAnual >= 20000; 
    }
}

```

**Clase 3: `ValidadorSeguridad.java**`

```java
package com.finanzas.core;

public class ValidadorSeguridad {
    
    // La contraseña debe tener al menos 8 caracteres y contener un número
    public boolean esClaveSegura(String password) {
        if (password == null) return false;
        
        // BUG: Solo comprueba la longitud, olvida comprobar el número
        if (password.length() < 8) {
            return false;
        }
        return true; 
    }
    
    // Genera un ID de transacción (simulado)
    public String generarToken() {
        // BUG DE RENDIMIENTO: Uso ineficiente de String en bucle grande
        String token = "";
        for (int i = 0; i < 10000; i++) {
            token += "a"; 
        }
        return "TOKEN-" + token.substring(0, 10);
    }
}

```

---

## 4. Desarrollo de la auditoría

### EJERCICIO 1. Planificación y estrategia de pruebas

Antes de programar, debes analizar qué vas a probar. Redacta un documento llamado `Plan_de_Pruebas_Finanzas.pdf` donde definas:

1. **Alcance:** ¿Qué clases se probarán y cuáles son críticas? (Ej: El dinero no puede desaparecer).
2. **Casos de prueba teóricos:** Define (sin código aún) al menos 3 casos para cada clase.
* *Ejemplo:* `CuentaBancaria` -> Probar ingreso negativo (debería fallar), probar retirada exacta del límite, probar retirada superior al límite.
* *Ejemplo:* `CalculadoraPrestamos` -> Probar elegibilidad de un menor de edad con mucho dinero (debería ser falso).



### EJERCICIO 2. Implementación de la suite de tests (JUnit)

Crea un paquete `com.finanzas.tests`. Debes implementar una clase de test para cada clase del *core*. **Tu objetivo es poner el código en rojo (fallar).**

**Requisitos mínimos de cobertura:**

1. **Test de CuentaBancaria (`CuentaBancariaTest.java`):**
* Prueba que verifique que el saldo inicial es correcto.
* Prueba de depósito válido.
* Prueba de **depósito negativo**: Debe fallar (el código actual lo permite, tu test debe revelar ese fallo).
* Prueba de **retirada válida**: Dentro del saldo.
* Prueba de **retirada en descubierto**: Dentro del límite permitido.
* Prueba de **retirada excesiva**: Fuera del límite (debe devolver false).


2. **Test de Préstamos (`CalculadoraPrestamosTest.java`):**
* Prueba de cálculo de interés con valores estándar.
* Prueba de excepción: Monto 0 o años 0.
* Prueba de **Elegibilidad**:
* Caso 1: Adulto con sueldo alto (True).
* Caso 2: Menor con sueldo bajo (False).
* Caso 3 (El Bug): Menor de edad con sueldo alto (Debería ser False, pero el código tiene un `OR` en vez de `AND`).




3. **Test de Seguridad (`ValidadorSeguridadTest.java`):**
* Prueba contraseña corta ("abc").
* Prueba contraseña larga sin números ("abcdefgh"). El código actual dirá que es válida, pero tu test debe esperar que sea `false` porque las reglas dicen que necesita número.
* Prueba contraseña correcta ("abcdefgh1").



### EJERCICIO 3. Prueba de rendimiento y estrés

El método `generarToken()` de la clase `ValidadorSeguridad` se sospecha que es lento.

1. Crea una clase `TestRendimiento.java` (con `main` o JUnit).
2. Ejecuta el método `generarToken()` **50.000 veces** dentro de un bucle.
3. Mide el tiempo con `System.currentTimeMillis()`.
4. Si tarda más de 2 segundos, se considera un fallo de rendimiento. Documenta el resultado.

### EJERCICIO 4. Reparación y regresión

Una vez que tengas tu panel de JUnit lleno de barras rojas (fallos detectados):

1. **Corrige el código** en el paquete `com.finanzas.core`:
* Impide depósitos negativos en `CuentaBancaria`.
* Arregla la lógica de `retirar` para respetar correctamente el límite descubierto.
* Corrige el operador lógico en `esElegible` (`&&` en vez de `||`).
* Implementa la validación de números en `esClaveSegura` (puedes usar `matches(".*\\d.*")`).
* Optimiza `generarToken` usando `StringBuilder` en lugar de concatenar `String`.


2. **Regresión:** Vuelve a ejecutar todos los tests del Ejercicio 2 y 3.
* **Objetivo:** Conseguir el 100% de barras verdes.



### EJERCICIO 5. Informe de auditoría

Redacta un informe final de bugs (puede ser la segunda parte de tu PDF del Ejercicio 1). Para cada fallo encontrado y corregido, incluye:

* **ID del Bug:** (ej: BUG-FIN-01).
* **Componente:** Clase y método afectado.
* **Descripción:** Qué ocurría antes de la corrección.
* **Prueba:** Captura del test de JUnit que fallaba.
* **Solución:** Breve explicación técnica de cómo lo arreglaste (ej: "Se cambió String por StringBuilder").

---

## 5. Criterios de evaluación

Esta práctica tiene un peso importante. Se valorará la exhaustividad de las pruebas más que la complejidad del código Java.

| Criterio | Puntos | Descripción |
| --- | --- | --- |
| **Planificación** | 1.5 ptos | El documento inicial define claramente qué se va a probar y por qué. |
| **Cobertura JUnit** | 3.5 ptos | Se han implementado todos los tests solicitados. Existen pruebas para casos positivos (funciona) y negativos (errores de entrada). |
| **Detección de Bugs** | 2 ptos | Los tests diseñados fueron capaces de poner en "rojo" la suite antes de las correcciones (se evidencia en el informe). |
| **Corrección y Regresión** | 1.5 ptos | El código final es robusto, seguro y eficiente. La ejecución final de JUnit da verde al completo. |
| **Calidad Documental** | 1.5 ptos | El informe de bugs sigue un formato profesional (ID, descripción, pasos, solución). |

---

## 6. Entrega

Sube a la plataforma:

1. El archivo **PDF** con el Plan de Pruebas y el Informe de Auditoría.
2. Un archivo **ZIP** con la carpeta `src` de tu proyecto Eclipse (conteniendo tanto el paquete `core` corregido como el paquete `tests`).
