# Tema 12. Realización de pruebas

**Tarea 1: "Suite completa de calidad de software: El caso E-Shop"**

## 1. Introducción

En esta unidad hemos aprendido que las pruebas no son una tarea opcional, sino una fase crítica del ciclo de vida del software. Hemos visto que existen pruebas de caja blanca (evaluando el código interno) y de caja negra (evaluando la funcionalidad externa) .

En esta práctica guiada vas a asumir el rol de un **Ingeniero de pruebas (QA)**. Te vamos a entregar el código fuente de una aplicación de gestión de pedidos ("E-Shop") que ha sido escrita por un desarrollador junior. Tu misión es crítica: el código contiene errores de lógica ocultos y problemas de validación.

Utilizarás **JUnit** para pruebas unitarias e integración , realizarás **depuración de código** para localizar los fallos  y finalmente realizarás pruebas de rendimiento básicas y documentación de errores.

## 2. Objetivos de aprendizaje

Al finalizar esta práctica extensa, deberás ser capaz de:

* Configurar un entorno de pruebas con **JUnit 5** en Eclipse.
* Implementar **pruebas unitarias** exhaustivas para validar métodos individuales.
* Implementar **pruebas de integración** para verificar la comunicación entre clases.
* Identificar y corregir **errores de lógica** mediante el uso del depurador y el análisis de tests fallidos.
* Ejecutar **pruebas de regresión** para asegurar que los arreglos funcionan.
* Redactar un informe de incidencias profesional siguiendo estándares de la industria.

---

## 3. Desarrollo de la práctica

### EJERCICIO 1. Preparación del entorno "defectuoso"

Vamos a crear el proyecto base. Este código compila perfectamente (no tiene errores de sintaxis), pero tiene **errores lógicos** graves que tus pruebas deberán detectar.

**Pasos:**

1. Crea un proyecto Java en Eclipse llamado `Tema12_QA_Suite`.
2. Crea un paquete llamado `com.eshop.core`.
3. Crea las siguientes tres clases copiando exactamente el código proporcionado (incluyendo los fallos):

**Clase 1: `Producto.java**`
Representa un artículo de la tienda.

```java
package com.eshop.core;

public class Producto {
    private String nombre;
    private double precio;

    public Producto(String nombre, double precio) {
        this.nombre = nombre;
        // ERROR INTENCIONADO 1: Permitimos precios negativos
        this.precio = precio; 
    }

    public String getNombre() {
        return nombre;
    }

    public double getPrecio() {
        return precio;
    }
}

```

**Clase 2: `Cliente.java**`
Representa al comprador. Puede ser VIP o normal.

```java
package com.eshop.core;

public class Cliente {
    private String nombre;
    private String email;
    private boolean esVip;

    public Cliente(String nombre, String email, boolean esVip) {
        this.nombre = nombre;
        this.email = email;
        this.esVip = esVip;
    }

    public boolean esVip() {
        return esVip;
    }

    // Método para validar email simple (debe contener '@' y '.')
    public boolean validarEmail() {
        if (email == null) return false;
        // ERROR INTENCIONADO 2: Lógica OR en vez de AND
        return email.contains("@") || email.contains("."); 
    }
    
    public String getEmail() {
        return email;
    }
}

```

**Clase 3: `CarritoCompras.java**`
Gestiona la lista de productos y el total.

```java
package com.eshop.core;

import java.util.ArrayList;
import java.util.List;

public class CarritoCompras {
    private List<Producto> productos = new ArrayList<>();
    private Cliente cliente;

    public CarritoCompras(Cliente cliente) {
        this.cliente = cliente;
    }

    public void agregarProducto(Producto p) {
        if(p != null) {
            productos.add(p);
        }
    }
    
    public int getCantidadProductos() {
        return productos.size();
    }

    public void vaciarCarrito() {
        productos.clear();
    }

    public double calcularTotal() {
        double total = 0;
        for (Producto p : productos) {
            total += p.getPrecio();
        }
        
        // Aplicar descuento VIP del 10%
        if (cliente.esVip()) {
            // ERROR INTENCIONADO 3: Restamos el total en lugar del porcentaje
            total = total - 0.10; 
        }
        return total;
    }
}

```

---

### EJERCICIO 2. Implementación de pruebas unitarias (Caja blanca)

Ahora actuaremos como "testers". Vamos a crear pruebas para verificar el funcionamiento aislado de las clases `Producto` y `Cliente`. Veremos cómo algunas pasarán y otras fallarán.

**Pasos:**

1. Asegúrate de tener JUnit en el *Build Path*. (Clic derecho en proyecto > Build Path > Add Libraries > JUnit).
2. Crea un nuevo paquete `com.eshop.tests`.
3. Crea la clase de prueba `ClienteTest.java` con el siguiente código. Fíjate que estamos probando múltiples escenarios para un mismo método (validación de email).

```java
package com.eshop.tests;

import static org.junit.Assert.*;
import org.junit.Test;
import com.eshop.core.Cliente;

public class ClienteTest {

    @Test
    public void testEmailValido() {
        Cliente c = new Cliente("Ana", "ana@test.com", false);
        assertTrue("Un email con @ y . debería ser válido", c.validarEmail());
    }

    @Test
    public void testEmailSinArroba() {
        // Este test fallará debido al Error Intencionado 2
        Cliente c = new Cliente("Pepe", "pepe.com", false);
        assertFalse("Un email sin @ no debería ser válido", c.validarEmail());
    }
    
    @Test
    public void testEmailSinPunto() {
        // Este test fallará debido al Error Intencionado 2
        Cliente c = new Cliente("Luis", "luis@com", false);
        assertFalse("Un email sin punto no debería ser válido", c.validarEmail());
    }
    
    @Test
    public void testEmailNull() {
        Cliente c = new Cliente("Juan", null, false);
        assertFalse("Un email null debe ser inválido", c.validarEmail());
    }
}

```

4. **Ejecuta el test:** Clic derecho en `ClienteTest.java` > Run As > JUnit Test.
* **Resultado esperado:** Verás una barra **ROJA**.
* Analiza el panel de JUnit: `testEmailSinArroba` y `testEmailSinPunto` habrán fallado.
* *Nota:* No corrijas el código todavía. Estamos en fase de detección.



---

### EJERCICIO 3. Implementación de pruebas de integración

Las pruebas de integración verifican que los módulos funcionan bien juntos. Probaremos la interacción entre `Cliente`, `Producto` y `Carrito`.

**Pasos:**

1. Crea la clase `CarritoTest.java` en el paquete `com.eshop.tests`.

```java
package com.eshop.tests;

import static org.junit.Assert.*;
import org.junit.Before;
import org.junit.Test;
import com.eshop.core.CarritoCompras;
import com.eshop.core.Cliente;
import com.eshop.core.Producto;

public class CarritoTest {
    
    private Producto p1;
    private Producto p2;
    
    // @Before se ejecuta antes de CADA test para preparar el entorno
    @Before
    public void setUp() {
        p1 = new Producto("Camiseta", 20.0);
        p2 = new Producto("Pantalón", 30.0);
    }

    @Test
    public void testCalculoTotalClienteNormal() {
        Cliente clienteNormal = new Cliente("User", "u@u.com", false);
        CarritoCompras carro = new CarritoCompras(clienteNormal);
        carro.agregarProducto(p1);
        carro.agregarProducto(p2);
        
        // Esperamos 50.0 (20 + 30)
        assertEquals(50.0, carro.calcularTotal(), 0.001);
    }

    @Test
    public void testCalculoTotalClienteVip() {
        // Este test fallará por el Error Intencionado 3
        Cliente clienteVip = new Cliente("Vip", "v@v.com", true);
        CarritoCompras carro = new CarritoCompras(clienteVip);
        carro.agregarProducto(p1); // 20
        carro.agregarProducto(p2); // 30
        
        // Total = 50. Descuento 10% = 5. Total esperado = 45.
        assertEquals("El VIP debería pagar un 10% menos", 45.0, carro.calcularTotal(), 0.001);
    }
    
    @Test
    public void testVaciarCarrito() {
        Cliente c = new Cliente("Test", "t@t.com", false);
        CarritoCompras carro = new CarritoCompras(c);
        carro.agregarProducto(p1);
        carro.vaciarCarrito();
        assertEquals(0, carro.getCantidadProductos());
    }
}

```

2. **Ejecuta el test:** Run As > JUnit Test.
* **Resultado esperado:** Barra **ROJA**. Fallará `testCalculoTotalClienteVip`.
* Observa el fallo: JUnit te dirá algo como `Expected: 45.0 but was: 49.9`. Esto nos indica que el cálculo matemático está mal.



---

### EJERCICIO 4. Depuración y pruebas de regresión

Hemos detectado fallos gracias a las pruebas. Ahora pasamos a la fase de **Depuración**.

**Parte A: Corregir Cliente (Lógica Booleana)**

1. Abre `Cliente.java`.
2. Localiza el método `validarEmail`.
3. El error es el operador `||` (OR). Un email debe tener arroba **Y** punto.
4. Cambia `||` por `&&`.
5. 
Prueba de Regresión: Vuelve a ejecutar `ClienteTest.java`.


* **Resultado:** ¡Barra **VERDE**! Hemos arreglado el bug sin romper nada más.



**Parte B: Corregir Carrito (Error de lógica matemática)**

1. Abre `CarritoCompras.java`.
2. Localiza el cálculo del descuento VIP.
3. El código dice `total = total - 0.10;`. Esto resta 10 céntimos, no el 10%.
4. Corrígelo: `total = total - (total * 0.10);` o `total = total * 0.90;`.
5. **Prueba de Regresión:** Vuelve a ejecutar `CarritoTest.java`.
* **Resultado:** Barra **VERDE**.



**Parte C: Robustez (Prevenir precios negativos)**

1. Abre `Producto.java`.
2. Modifica el constructor para que no acepte precios negativos (programación defensiva).

```java
    public Producto(String nombre, double precio) {
        this.nombre = nombre;
        if (precio < 0) {
            throw new IllegalArgumentException("El precio no puede ser negativo");
        }
        this.precio = precio; 
    }

```

3. Ahora debemos **crear un nuevo test** para verificar esta protección. Crea `ProductoTest.java`:

```java
package com.eshop.tests;
import org.junit.Test;
import com.eshop.core.Producto;

public class ProductoTest {
    // Este test PASA si se lanza la excepción esperada
    @Test(expected = IllegalArgumentException.class)
    public void testPrecioNegativo() {
        new Producto("Malo", -5.0);
    }
}

```

4. Ejecuta este test y verifica que pasa (barra verde).

---

### EJERCICIO 5. Simulación de prueba de rendimiento

Aunque existen herramientas avanzadas como JMeter, podemos simular una prueba de carga básica con Java para ver cómo se comporta el sistema creando miles de objetos. Esto es una prueba de **volumen/carga** simplificada.

**Pasos:**

1. Crea una clase `PruebaRendimiento.java` en el paquete `com.eshop.tests`.

```java
package com.eshop.tests;

import com.eshop.core.Cliente;
import com.eshop.core.CarritoCompras;
import com.eshop.core.Producto;

public class PruebaRendimiento {

    public static void main(String[] args) {
        System.out.println("Iniciando prueba de carga masiva...");
        
        long inicio = System.currentTimeMillis();
        
        Cliente cliente = new Cliente("Tester", "test@test.com", true);
        CarritoCompras carrito = new CarritoCompras(cliente);
        
        // Simulamos la carga de 1 millón de productos
        for (int i = 0; i < 1000000; i++) {
            carrito.agregarProducto(new Producto("Producto " + i, 10.0));
        }
        
        double total = carrito.calcularTotal();
        
        long fin = System.currentTimeMillis();
        
        System.out.println("Total calculado: " + total);
        System.out.println("Tiempo de ejecución: " + (fin - inicio) + " ms");
        
        if ((fin - inicio) < 1000) {
            System.out.println("RENDIMIENTO: ÓPTIMO (Menos de 1 segundo)");
        } else {
            System.out.println("RENDIMIENTO: MEJORABLE");
        }
    }
}

```

2. Ejecútalo como Java Application. Analiza el tiempo que tarda tu ordenador en procesar 1 millón de registros en memoria.

---

### EJERCICIO 6. Documentación de errores (Caja negra)

Finalmente, simularemos una prueba de **Caja Negra** sobre la interfaz (hipotética). Imagina que ejecutas la versión visual de la app y encuentras el siguiente error. Debes documentarlo formalmente.

**Escenario:**
Al intentar registrar un usuario nuevo en la pantalla de "Registro", si el usuario introduce un nombre con tildes (ej: "José"), la aplicación muestra el símbolo  y guarda "Jos?" en la base de datos.

**Tarea:**
Abre un documento de texto y redacta un **Informe de Bug** simulando que lo subes a una herramienta como Bugzilla. Debe contener:

1. **Resumen:** Título corto del problema.
2. **Plataforma:** Sistema operativo y navegador simulado.
3. **Severidad:** (Critica/Normal/Menor) y justificación.
4. **Pasos para reproducir:** Lista detallada (1, 2, 3...) para que el desarrollador replique el fallo.
5. **Resultado esperado:** Qué debería pasar.
6. **Resultado actual:** Qué es lo que pasa realmente.

---

## 4. Documentación a entregar

Genera un documento PDF único que incluya las siguientes evidencias:

1. **Evidencia de Tests Fallidos:** Captura de pantalla de JUnit con la **barra roja** obtenida en el Ejercicio 2 o 3 (donde se vean los fallos de `testEmailSinArroba` o `testCalculoTotalClienteVip`).
2. **Código Corregido:** Copia el código final de los métodos `validarEmail` (Cliente) y `calcularTotal` (Carrito) tras aplicar las correcciones del Ejercicio 4.
3. **Evidencia de Regresión:** Captura de pantalla de JUnit con la **barra verde** tras ejecutar todos los tests (ClienteTest, CarritoTest, ProductoTest) después de las correcciones.
4. **Prueba de Rendimiento:** Captura de la consola tras ejecutar el script de carga de 1 millón de productos, mostrando el tiempo en milisegundos.
5. **Informe de Bug:** El texto redactado en el Ejercicio 6 sobre el problema de codificación de caracteres.

## 5. Entrega

Sube a la plataforma el documento **PDF** con las capturas y respuestas. No es necesario subir el código `.java`.