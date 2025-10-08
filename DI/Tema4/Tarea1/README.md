# TEMA 4: CREACIÓN DE COMPONENTES

## TAREA GUIADA: Mi Primer JavaBean

### Objetivo

Crear un componente JavaBean personalizado que extienda `JTextField` y añadir propiedades personalizadas siguiendo los principios del Tema 4.

### Descripción

Desarrollaremos un componente `CampoTextoMejorado` que extienda `JTextField` y añada funcionalidades como validación visual y propiedades personalizadas.

### Paso 1: Crear el proyecto base

1. Crea un nuevo proyecto Java llamado `MiPrimerComponente`.
2. Crea un paquete llamado `componentes`.

### Paso 2: Implementar el JavaBean

```java
package componentes;

import javax.swing.JTextField;
import java.awt.Color;
import java.io.Serializable;

public class CampoTextoMejorado extends JTextField implements Serializable {
    
    // Propiedades privadas del componente
    private boolean esRequerido;
    private String textoAyuda;
    private Color colorError;
    
    // Constructor sin parámetros (requisito JavaBean)
    public CampoTextoMejorado() {
        super();
        inicializarPropiedades();
    }
    
    // Constructor con texto
    public CampoTextoMejorado(String texto) {
        super(texto);
        inicializarPropiedades();
    }
    
    // Inicializar valores por defecto
    private void inicializarPropiedades() {
        this.esRequerido = false;
        this.textoAyuda = "";
        this.colorError = Color.RED;
    }
    
    // Métodos get y set para las propiedades
    public boolean isEsRequerido() {
        return esRequerido;
    }
    
    public void setEsRequerido(boolean esRequerido) {
        this.esRequerido = esRequerido;
        actualizarApariencia();
    }
    
    public String getTextoAyuda() {
        return textoAyuda;
    }
    
    public void setTextoAyuda(String textoAyuda) {
        this.textoAyuda = textoAyuda;
        setToolTipText(textoAyuda);
    }
    
    public Color getColorError() {
        return colorError;
    }
    
    public void setColorError(Color colorError) {
        this.colorError = colorError;
    }
    
    // Método para validar el campo
    public boolean validar() {
        boolean esValido = true;
        
        if (esRequerido && getText().trim().isEmpty()) {
            esValido = false;
            setBackground(colorError);
        } else {
            setBackground(Color.WHITE);
        }
        
        return esValido;
    }
    
    // Actualizar apariencia según propiedades
    private void actualizarApariencia() {
        if (esRequerido) {
            setBorder(javax.swing.BorderFactory.createLineBorder(Color.BLUE, 2));
        } else {
            setBorder(javax.swing.BorderFactory.createLineBorder(Color.GRAY, 1));
        }
    }
}
```


### Paso 3: Crear aplicación de prueba

```java
package prueba;

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import componentes.CampoTextoMejorado;

public class PruebaComponente extends JFrame {
    
    private CampoTextoMejorado campoNombre;
    private CampoTextoMejorado campoEmail;
    private JButton btnValidar;
    
    public PruebaComponente() {
        initComponents();
        setupWindow();
        setupEvents();
    }
    
    private void initComponents() {
        // Crear componentes personalizados
        campoNombre = new CampoTextoMejorado();
        campoNombre.setBounds(100, 50, 200, 25);
        campoNombre.setEsRequerido(true);
        campoNombre.setTextoAyuda("Ingrese su nombre completo");
        
        campoEmail = new CampoTextoMejorado();
        campoEmail.setBounds(100, 100, 200, 25);
        campoEmail.setEsRequerido(true);
        campoEmail.setTextoAyuda("Ingrese su email");
        campoEmail.setColorError(Color.PINK);
        
        // Etiquetas
        JLabel lblNombre = new JLabel("Nombre:");
        lblNombre.setBounds(30, 50, 60, 25);
        
        JLabel lblEmail = new JLabel("Email:");
        lblEmail.setBounds(30, 100, 60, 25);
        
        // Botón de validación
        btnValidar = new JButton("Validar Campos");
        btnValidar.setBounds(100, 150, 120, 30);
        
        // Añadir al frame
        add(lblNombre);
        add(campoNombre);
        add(lblEmail);
        add(campoEmail);
        add(btnValidar);
    }
    
    private void setupWindow() {
        setTitle("Prueba CampoTextoMejorado");
        setSize(350, 250);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(null);
        setLocationRelativeTo(null);
    }
    
    private void setupEvents() {
        btnValidar.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                boolean nombreValido = campoNombre.validar();
                boolean emailValido = campoEmail.validar();
                
                if (nombreValido && emailValido) {
                    JOptionPane.showMessageDialog(null, "¡Todos los campos son válidos!");
                } else {
                    JOptionPane.showMessageDialog(null, "Por favor, complete los campos requeridos.");
                }
            }
        });
    }
    
    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            new PruebaComponente().setVisible(true);
        });
    }
}
```


### Paso 4: Empaquetado y documentación

1. Exportar como JAR siguiendo los pasos del Tema 4


