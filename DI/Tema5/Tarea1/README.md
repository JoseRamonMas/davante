# TEMA 5

## TAREA GUIADA: Pruebas con JUnit 5

### Objetivo

Aprender a crear y ejecutar pruebas unitarias usando JUnit 5 en Eclipse, aplicando las mejores prácticas para probar tanto lógica de negocio como componentes Swing.

### Descripción

Desarrollaremos una calculadora básica con interfaz gráfica y crearemos pruebas unitarias completas para verificar su correcto funcionamiento.

### Paso 1: Configuración del proyecto

1. Crea un nuevo proyecto Java llamado `CalculadoraConPruebas`
2. Configura JUnit 5: **Click derecho en proyecto → Build Path → Add Libraries → JUnit → JUnit 5**
3. Crea los siguientes paquetes:
   - `modelo` - Para las clases de lógica de negocio
   - `vista` - Para los componentes Swing
   - `pruebas` - Para las clases de test

### Paso 2: Implementar la clase Calculadora (Modelo)

```java
package modelo;

public class Calculadora {
    
    public int sumar(int a, int b) {
        return a + b;
    }
    
    public int restar(int a, int b) {
        return a - b;
    }
    
    public int multiplicar(int a, int b) {
        return a * b;
    }
    
    public double dividir(int a, int b) {
        if (b == 0) {
            throw new ArithmeticException("No se puede dividir por cero");
        }
        return (double) a / b;
    }
    
    public boolean esPar(int numero) {
        return numero % 2 == 0;
    }
    
    public int potencia(int base, int exponente) {
        if (exponente < 0) {
            throw new IllegalArgumentException("El exponente no puede ser negativo");
        }
        return (int) Math.pow(base, exponente);
    }
}
```

### Paso 3: Implementar la interfaz gráfica (Vista)

```java
package vista;

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import modelo.Calculadora;

public class CalculadoraSwing extends JFrame {
    private JTextField txtNumero1, txtNumero2;
    private JLabel lblResultado;
    private JButton btnSumar, btnRestar, btnMultiplicar, btnDividir;
    private Calculadora calculadora;
    
    public CalculadoraSwing() {
        calculadora = new Calculadora();
        setupVista();
        setupEventos();
    }
    
    private void setupVista() {
        setTitle("Calculadora con Pruebas");
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new GridLayout(6, 2, 10, 10));
        
        add(new JLabel("Número 1:"));
        txtNumero1 = new JTextField();
        add(txtNumero1);
        
        add(new JLabel("Número 2:"));
        txtNumero2 = new JTextField();
        add(txtNumero2);
        
        btnSumar = new JButton("Sumar");
        btnRestar = new JButton("Restar");
        btnMultiplicar = new JButton("Multiplicar");
        btnDividir = new JButton("Dividir");
        
        add(btnSumar);
        add(btnRestar);
        add(btnMultiplicar);
        add(btnDividir);
        
        add(new JLabel("Resultado:"));
        lblResultado = new JLabel("0");
        lblResultado.setBorder(BorderFactory.createLineBorder(Color.GRAY));
        add(lblResultado);
        
        pack();
        setLocationRelativeTo(null);
    }
    
    private void setupEventos() {
        btnSumar.addActionListener(e -> realizarOperacion("sumar"));
        btnRestar.addActionListener(e -> realizarOperacion("restar"));
        btnMultiplicar.addActionListener(e -> realizarOperacion("multiplicar"));
        btnDividir.addActionListener(e -> realizarOperacion("dividir"));
    }
    
    private void realizarOperacion(String operacion) {
        try {
            int num1 = Integer.parseInt(txtNumero1.getText());
            int num2 = Integer.parseInt(txtNumero2.getText());
            double resultado = switch (operacion) {
                case "sumar" -> calculadora.sumar(num1, num2);
                case "restar" -> calculadora.restar(num1, num2);
                case "multiplicar" -> calculadora.multiplicar(num1, num2);
                case "dividir" -> calculadora.dividir(num1, num2);
                default -> 0;
            };
            
            lblResultado.setText(String.valueOf(resultado));
            lblResultado.setForeground(Color.BLACK);
            
        } catch (NumberFormatException ex) {
            mostrarError("Error: Entrada inválida");
        } catch (ArithmeticException ex) {
            mostrarError("Error: " + ex.getMessage());
        } catch (IllegalArgumentException ex) {
            mostrarError("Error: " + ex.getMessage());
        }
    }
    
    private void mostrarError(String mensaje) {
        lblResultado.setText(mensaje);
        lblResultado.setForeground(Color.RED);
    }
    
    // Métodos para pruebas
    public void setNumeros(String num1, String num2) {
        txtNumero1.setText(num1);
        txtNumero2.setText(num2);
    }
    
    public void clickSumar() {
        btnSumar.doClick();
    }
    
    public void clickRestar() {
        btnRestar.doClick();
    }
    
    public void clickMultiplicar() {
        btnMultiplicar.doClick();
    }
    
    public void clickDividir() {
        btnDividir.doClick();
    }
    
    public String getResultado() {
        return lblResultado.getText();
    }
    
    public Color getColorResultado() {
        return lblResultado.getForeground();
    }
    
    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            new CalculadoraSwing().setVisible(true);
        });
    }
}
```

### Paso 4: Crear pruebas unitarias para el Modelo

```java
package pruebas;

import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.Timeout;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;

import modelo.Calculadora;

class CalculadoraTest {
    
    private Calculadora calculadora;
    
    @BeforeEach
    @DisplayName("Configuración inicial antes de cada prueba")
    void setUp() {
        calculadora = new Calculadora();
        System.out.println("Preparando calculadora para nueva prueba...");
    }

    @Test
    @DisplayName("Suma de dos números positivos")
    void testSumarPositivos() {
        // Arrange
        int a = 5, b = 3;
        int resultadoEsperado = 8;
        
        // Act
        int resultadoReal = calculadora.sumar(a, b);
        
        // Assert
        assertEquals(resultadoEsperado, resultadoReal, 
                   "La suma de 5 + 3 debería ser 8");
    }
    
    @Test
    @DisplayName("Suma con números negativos")
    void testSumarNegativos() {
        int resultado = calculadora.sumar(-5, -3);
        assertEquals(-8, resultado, 
                    "La suma de -5 + (-3) debería ser -8");
    }
    
    @ParameterizedTest
    @CsvSource({
        "10, 4, 6",
        "5, 5, 0", 
        "0, 5, -5"
    })
    @DisplayName("Pruebas parametrizadas de resta")
    void testRestar(int a, int b, int expected) {
        assertEquals(expected, calculadora.restar(a, b));
    }
    
    @Test
    @DisplayName("Multiplicación de números")
    void testMultiplicar() {
        assertAll("Grupo de multiplicaciones",
            () -> assertEquals(20, calculadora.multiplicar(4, 5)),
            () -> assertEquals(0, calculadora.multiplicar(10, 0)),
            () -> assertEquals(-15, calculadora.multiplicar(3, -5))
        );
    }
    
    @Test
    @DisplayName("División normal con resultado decimal")
    void testDividirDecimal() {
        double resultado = calculadora.dividir(5, 2);
        assertEquals(2.5, resultado, 0.001, 
                    "La división de 5 / 2 debería ser 2.5");
    }
    
    @Test
    @DisplayName("División por cero debe lanzar excepción")
    void testDividirPorCero() {
        ArithmeticException exception = assertThrows(
            ArithmeticException.class, 
            () -> calculadora.dividir(10, 0)
        );
        
        assertEquals("No se puede dividir por cero", exception.getMessage());
    }
    
    @Test
    @DisplayName("Verificación de números pares e impares")
    void testEsPar() {
        assertAll("Verificación de paridad",
            () -> assertTrue(calculadora.esPar(4), "4 debería ser par"),
            () -> assertFalse(calculadora.esPar(7), "7 no debería ser par"),
            () -> assertTrue(calculadora.esPar(0), "0 debería ser par")
        );
    }
    
    @Test
    @DisplayName("Cálculo de potencias")
    @Timeout(2)
    void testPotencia() {
        assertEquals(8, calculadora.potencia(2, 3));
        assertEquals(1, calculadora.potencia(5, 0));
        assertEquals(25, calculadora.potencia(5, 2));
    }
    
    @Test
    @DisplayName("Exponente negativo debe lanzar excepción")
    void testPotenciaExponenteNegativo() {
        IllegalArgumentException exception = assertThrows(
            IllegalArgumentException.class,
            () -> calculadora.potencia(2, -1)
        );
        assertTrue(exception.getMessage().contains("negativo"));
    }
}
```

### Paso 5: Crear Pruebas para la Interfaz Gráfica

```java
package pruebas;

import static org.junit.jupiter.api.Assertions.*;
import javax.swing.*;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.Timeout;
import java.awt.*;

import vista.CalculadoraSwing;

class CalculadoraSwingTest {
    
    @Test
    @DisplayName("Suma correcta en la interfaz gráfica")
    @Timeout(3)
    void testSumaCorrectaEnInterfaz() {
        SwingUtilities.invokeLater(() -> {
            CalculadoraSwing vista = new CalculadoraSwing();
            
            // Simular entrada del usuario
            vista.setNumeros("10", "5");
            vista.clickSumar();
            
            // Verificar resultado
            assertEquals("15", vista.getResultado());
            assertEquals(Color.BLACK, vista.getColorResultado());
        });
    }
    
    @Test
    @DisplayName("Resta correcta en la interfaz gráfica")
    @Timeout(3)
    void testRestaCorrectaEnInterfaz() {
        SwingUtilities.invokeLater(() -> {
            CalculadoraSwing vista = new CalculadoraSwing();
            
            vista.setNumeros("10", "4");
            vista.clickRestar();
            
            assertEquals("6", vista.getResultado());
        });
    }
    
    @Test
    @DisplayName("Manejo de entrada inválida en la interfaz")
    @Timeout(3)
    void testEntradaInvalidaEnInterfaz() {
        SwingUtilities.invokeLater(() -> {
            CalculadoraSwing vista = new CalculadoraSwing();
            
            vista.setNumeros("abc", "5");
            vista.clickSumar();
            
            assertTrue(vista.getResultado().contains("Error"));
            assertEquals(Color.RED, vista.getColorResultado());
        });
    }
    
    @Test
    @DisplayName("División por cero en la interfaz gráfica")
    @Timeout(3)
    void testDivisionPorCeroEnInterfaz() {
        SwingUtilities.invokeLater(() -> {
            CalculadoraSwing vista = new CalculadoraSwing();
            
            vista.setNumeros("10", "0");
            vista.clickDividir();
            
            assertTrue(vista.getResultado().contains("dividir"));
            assertEquals(Color.RED, vista.getColorResultado());
        });
    }
    
    @Test
    @DisplayName("La interfaz se crea correctamente")
    @Timeout(3)
    void testInterfazCreadaCorrectamente() {
        SwingUtilities.invokeLater(() -> {
            CalculadoraSwing vista = new CalculadoraSwing();
            
            assertTrue(vista.isVisible());
            assertEquals("Calculadora con Pruebas", vista.getTitle());
            
            // Verificar que los componentes críticos están presentes
            assertNotNull(vista.getResultado());
        });
    }
}
```

### Paso 6: Ejecutar y analizar las pruebas

1. **Ejecutar todas las pruebas:**
   - Click derecho en el proyecto → **Run As → JUnit Test**

2. **Ejecutar pruebas específicas:**
   - Click derecho en `CalculadoraTest.java` → **Run As → JUnit Test**
   - Click derecho en `CalculadoraSwingTest.java` → **Run As → JUnit Test**

3. **Ejecutar una prueba individual:**
   - Click derecho en un método de test → **Run As → JUnit Test**

### Análisis de resultados

- **Barra VERDE**: Todas las pruebas pasaron ✅
- **Barra ROJA**: Al menos una prueba falló ❌
- **Vista de JUnit**: Muestra detalles de pruebas exitosas y fallidas

### Ejercicios de ampliación

1. **Añadir nueva funcionalidad:**
   - Implementar método `raizCuadrada()` en `Calculadora`
   - Crear las pruebas correspondientes

2. **Mejorar la interfaz:**
   - Añadir botón para calcular potencia
   - Crear pruebas para la nueva funcionalidad

3. **Pruebas parametrizadas:**
   - Crear más pruebas usando `@ParameterizedTest`

### Entrega

- Proyecto Eclipse completo con todas las clases
