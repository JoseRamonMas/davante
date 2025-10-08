# TEMA 4: CREACIÓN DE COMPONENTES

## PRÁCTICA NO GUIADA (EVALUABLE): Panel Gráfico Personalizado

### Enunciado

Desarrolla un componente JavaBean llamado `PanelDibujo` que extienda `JPanel` y permita dibujar formas geométricas básicas utilizando la clase Graphics.

### Requisitos Funcionales (80 puntos)

#### 1. Implementación JavaBean (20 puntos)

- Extender de `JPanel`
- Implementar `Serializable`
- Constructor sin parámetros
- Al menos 3 propiedades con métodos get/set:
    - `colorFigura`: Color de la figura a dibujar
    - `tipoFigura`: String ("rectangulo", "oval", "linea")
    - `tamaño`: int (tamaño de la figura)


#### 2. Funcionalidades Gráficas (30 puntos)

- Sobrescribir método `paint(Graphics g)`
- Dibujar al menos 3 tipos de figuras diferentes
- Usar las propiedades del componente para personalizar el dibujo
- Centrar las figuras en el panel


#### 3. Interactividad Básica (20 puntos)

- Implementar evento de clic para cambiar entre tipos de figura
- Mostrar información de la figura actual (tooltip o label)


#### 4. Aplicación de Prueba (10 puntos)

- Crear aplicación que demuestre el funcionamiento
- Incluir controles para modificar las propiedades
- Botones para cambiar tipo de figura y color


### Requisitos Técnicos (20 puntos)

#### 5. Estructura de Código

- Código limpio y comentado
- Uso correcto de la herencia
- Manejo adecuado de eventos


### Ejemplo de estructura base:

```java
package componentes;

import javax.swing.JPanel;
import java.awt.Graphics;
import java.awt.Color;
import java.io.Serializable;

public class PanelDibujo extends JPanel implements Serializable {
    
    private Color colorFigura;
    private String tipoFigura;
    private int tamaño;
    
    // Constructor sin parámetros
    public PanelDibujo() {
        // Inicializar propiedades por defecto
        // Configurar eventos de mouse
    }
    
    // Métodos get y set para todas las propiedades
    
    @Override
    public void paint(Graphics g) {
        super.paint(g);
        // Implementar dibujo según tipoFigura
    }
    
    // Métodos para cambiar figura y manejar eventos
}
```


### Criterios de Evaluación

| Concepto | Puntos | Descripción |
| :-- | :-- | :-- |
| **JavaBean correcto** | 20 | Implementa Serializable, constructor sin parámetros, métodos get/set |
| **Uso de Graphics** | 30 | Sobrescribe paint() correctamente, dibuja múltiples figuras |
| **Propiedades funcionales** | 20 | Las propiedades modifican efectivamente la apariencia |
| **Interactividad** | 10 | Responde a eventos de usuario |
| **Aplicación de prueba** | 10 | Demuestra todas las funcionalidades |
| **Calidad de código** | 10 | Código limpio, comentado y bien estructurado |

### Entrega

- **Formato:** Archivo ZIP con proyecto completo
- **Nombre:** `PanelDibujo_[Apellido]_[Nombre].zip`
- **Incluir:**
    - Código fuente del componente
    - Aplicación de prueba funcional
    - README con instrucciones básicas


### Ejemplos de Figuras a Implementar

```java
// En el método paint(Graphics g)
switch(tipoFigura) {
    case "rectangulo":
        g.setColor(colorFigura);
        g.fillRect(x, y, tamaño, tamaño);
        break;
    case "oval":
        g.setColor(colorFigura);
        g.fillOval(x, y, tamaño, tamaño);
        break;
    case "linea":
        g.setColor(colorFigura);
        g.drawLine(x1, y1, x2, y2);
        break;
}
```