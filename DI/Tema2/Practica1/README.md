# Práctica Evaluable - Tema 2

En esta práctica vas a aplicar los conceptos estudiados en el tema 2 del módulo **Desarrollo de Interfaces**. El objetivo es diseñar y programar una aplicación en Java con Swing que incluya varias ventanas, diferentes componentes gráficos y el uso de distintos Layout Managers. La práctica debe entregarse acompañada de un informe con capturas de pantalla y explicaciones en tus propias palabras sobre cada paso realizado.

## Objetivos de la práctica

- Practicar la inserción, configuración y uso de componentes Swing (`JButton`, `JLabel`, `JTextField`, `JCheckBox`, `JRadioButton`, `JComboBox`)
- Utilizar diferentes Layout Managers (`FlowLayout`, `BorderLayout`, `GridLayout`)
- Implementar varias ventanas (`JFrame` y `JDialog`) y conectar eventos entre ellas
- Documentar el proceso con capturas de pantalla y explicaciones propias

## Enunciado general

Vas a desarrollar una aplicación llamada **"Gestor de Encuestas"**. La aplicación constará de varias ventanas conectadas entre sí y deberá usar los principales componentes y layouts estudiados. Además del código, deberás entregar un informe con capturas y explicaciones.

## Parte 1: Ventana principal (JFrame)

Crea una ventana principal con título **"Gestor de Encuestas"**. En esta ventana debe aparecer:

- Un `JLabel` de bienvenida con el texto: *"Bienvenido al gestor de encuestas"*
- Un `JTextField` donde el usuario pueda escribir su nombre
- Un `JButton` con el texto *"Comenzar encuesta"*
- Un `JButton` con el texto *"Salir"*

### Requerimientos del layout

La distribución de los elementos deberá hacerse con un **BorderLayout**:

- **NORTH**: Mensaje de bienvenida
- **CENTER**: Campo de texto
- **SOUTH**: Botones

## Parte 2: Ventana de encuesta (JDialog)

Al pulsar el botón *"Comenzar encuesta"* debe abrirse un **JDialog modal** con la siguiente interfaz:

### Componentes requeridos

- Una etiqueta que salude al usuario por su nombre (usando el texto de la ventana principal)
- **3 JCheckBox** con diferentes aficiones:
    - "Leer"
    - "Deporte"
    - "Música"
- **3 JRadioButton** para franja de edad (usar `ButtonGroup` para exclusividad):
    - "<18"
    - "18-30"
    - "30+"
- **JComboBox** para nivel de satisfacción:
    - "Alta"
    - "Media"
    - "Baja"
- Un `JButton` con texto *"Finalizar encuesta"*

### Requerimientos del layout

Utilizar **GridLayout** para organizar las preguntas de forma ordenada.

## Parte 3: Ventana de resultados (JFrame)

Cuando se pulse *"Finalizar encuesta"*, se debe cerrar el JDialog y abrir una nueva ventana **JFrame** con un resumen de respuestas.

### Información a mostrar

- **Nombre del usuario**
- **Aficiones marcadas**
- **Franja de edad seleccionada**
- **Nivel de satisfacción elegido**

### Requerimientos de layout

La ventana debe organizarse con **FlowLayout**.

## Parte 4: Ampliación de la aplicación

Para completar la práctica, amplía tu aplicación con:

### Funcionalidades adicionales

- **Botón "Nueva encuesta"** en ventana de resultados (volver a ventana principal)
- **Validación**: JDialog de aviso si se pulsa "Comenzar encuesta" sin nombre
- **Personalización**: Modificar fuente, color y tamaño de botones/etiquetas
- **Menú JMenuBar** en ventana principal:
    - "Acerca de" → JDialog con información del programa
    - "Salir"

## Parte 5: Documentación y entrega

Además del código, entregar **informe en PDF** con:

### Apartados del informe

1. **Introducción**: Qué hace tu aplicación
2. **Capturas**: Todas las ventanas creadas (principal, encuesta, resultados, diálogos)
3. **Explicación Layout Managers**: En tus propias palabras
4. **Código fuente**: Con comentarios en apartados relevantes
5. **Reflexión final**: Dificultades encontradas y aprendizaje

## Criterios de evaluación

| **Apartado** | **Puntuación** | **Descripción** |
| :-- | :-- | :-- |
| Ventana Principal | **2.0 pts** | Implementación correcta con BorderLayout |
| Ventana Encuesta | **2.0 pts** | JDialog modal correcto con GridLayout |
| Ventana Resultados | **2.0 pts** | Implementación correcta con FlowLayout |
| Ampliaciones | **1.0 pt** | Validación, menú, personalización |
| Documentación | **1.5 pts** | Informe completo con capturas y explicaciones |
| Calidad Código | **1.5 pts** | Comentarios, legibilidad, estructura |

> **Consejo**: Asegúrate de probar todas las funcionalidades antes de la entrega y documenta cada paso con capturas de pantalla claras.
