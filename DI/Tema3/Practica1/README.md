# Práctica Evaluable - Tema 3

## Generación de interfaces a partir de documentos XML

### Contexto del Proyecto

La empresa **TechMuseum** necesita desarrollar una interfaz web para su nuevo museo interactivo de tecnología. Requieren que toda la información esté estructurada en XML para facilitar el intercambio de datos entre diferentes plataformas y que la interfaz sea escalable y multiplataforma.

### Objetivos

- Crear documentos XML bien formados siguiendo las reglas sintácticas
- Implementar gráficos vectoriales escalables (SVG)
- Generar interfaces HTML a partir de datos XML
- Aplicar eventos básicos con plantillas de código proporcionadas
- Analizar y validar la estructura de documentos XML

### Entregables

Debes entregar una carpeta comprimida **Tema3.zip** que contenga:

1. **museo.xml** - Base de datos del museo
2. **logo.html** - Logo vectorial del museo
3. **interface.html** - Interfaz principal
4. **eventos.html** - Página con eventos interactivos (con plantillas proporcionadas)
5. **analisis.md** - Análisis de la estructura XML

## **EJERCICIO 1: Estructura de datos XML (2.5 puntos)**

Crea un archivo **museo.xml** que contenga información sobre **6 exposiciones** del museo tecnológico. Cada exposición debe incluir:

### Estructura requerida

- `id` (atributo único)
- `categoria` (atributo: "hardware", "software", "inteligencia-artificial")
- `nombre` (elemento)
- `descripcion` (elemento)
- `fechas` (elemento contenedor con subelementos `inicio` y `fin`)
- `precio` (elemento con atributo `moneda`)
- `ubicacion` (elemento con atributos `piso` y `sala`)

### Criterios de evaluación

- Documento bien formado (0.5 pts)
- Declaración XML correcta (0.3 pts)
- Estructura jerárquica adecuada (0.7 pts)
- Uso correcto de atributos y elementos (0.5 pts)
- Datos coherentes y realistas (0.5 pts)

## **EJERCICIO 2: Logo vectorial con SVG (2.5 puntos)**

Diseña el logo del **TechMuseum** en un archivo **logo.html** utilizando SVG. El logo debe representar visualmente la temática tecnológica e incluir:

### Especificaciones técnicas

- Dimensiones del SVG: **300x150 píxeles**
- Mínimo **4 formas geométricas** diferentes (círculo, rectángulo, polígono, elipse)
- **3 colores diferentes** que representen las categorías del museo
- **Texto vectorial** con el nombre "TechMuseum"

### Criterios de evaluación

- Sintaxis SVG correcta (0.5 pts)
- Variedad de formas geométricas (0.8 pts)
- Diseño estético y coherente (0.7 pts)
- Uso adecuado de colores y efectos (0.5 pts)

## **EJERCICIO 3: Interfaz HTML (2.5 puntos)**

Desarrolla una página **interface.html** en HTML que muestre la información del museo **copiando manualmente** los datos del XML. Debe incluir:

### Contenido obligatorio

- Declaración DOCTYPE XHTML correcta
- Metadatos apropiados (charset, autor, descripción)
- Logo creado en el ejercicio anterior
- Lista de las 6 exposiciones organizadas por categoría
- Tabla con horarios y precios
- Formulario de contacto (nombre, email, consulta)

**Nota:** Los datos del XML se incluirán manualmente en el HTML. No es necesario cargar el XML dinámicamente.

### Criterios de evaluación

- Sintaxis XHTML válida (0.8 pts)
- Estructura semántica correcta (0.6 pts)
- Integración del logo SVG (0.4 pts)
- Presentación organizada de la información (0.4 pts)
- Formulario funcional y completo (0.3 pts)

## **EJERCICIO 4: Eventos interactivos con plantillas (2 puntos)**

Crea un archivo **eventos.html** que demuestre el manejo de eventos básicos utilizando las **plantillas de código proporcionadas**:

### 1. Click - Mostrar/Ocultar detalles

```html
<script>
function toggleDetails(elementId) {
    var element = document.getElementById(elementId);
    if (element.style.display === "none") {
        element.style.display = "block";
    } else {
        element.style.display = "none";
    }
}
</script>

<!-- Uso: -->
<h3 onclick="toggleDetails('detalle1')">Exposición 1 (click para ver detalles)</h3>
<div id="detalle1" style="display:none;">
    <p>Detalles de la exposición...</p>
</div>
```

### 2. MouseEnter/MouseLeave - Cambiar colores

```html
<script>
function cambiarColor(elemento, color) {
    elemento.style.backgroundColor = color;
}
</script>

<!-- Uso: -->
<div onmouseenter="cambiarColor(this, 'lightblue')" 
     onmouseleave="cambiarColor(this, 'white')">
    Pasa el ratón por aquí
</div>
```

### 3. Change - Filtro por categoría

```html
<script>
function filtrarCategoria() {
    var categoria = document.getElementById('filtro').value;
    var exposiciones = document.getElementsByClassName('exposicion');
    
    for (var i = 0; i < exposiciones.length; i++) {
        if (categoria === 'todas' || exposiciones[i].classList.contains(categoria)) {
            exposiciones[i].style.display = 'block';
        } else {
            exposiciones[i].style.display = 'none';
        }
    }
}
</script>

<!-- Uso: -->
<select id="filtro" onchange="filtrarCategoria()">
    <option value="todas">Todas las categorías</option>
    <option value="hardware">Hardware</option>
    <option value="software">Software</option>
    <option value="inteligencia-artificial">IA</option>
</select>
```

### 4. Focus/Blur - Resaltar campos del formulario

```html
<script>
function resaltarCampo(elemento) {
    elemento.style.borderColor = 'blue';
    elemento.style.borderWidth = '2px';
}

function quitarResaltado(elemento) {
    elemento.style.borderColor = 'gray';
    elemento.style.borderWidth = '1px';
}
</script>

<!-- Uso: -->
<input type="text" 
       onfocus="resaltarCampo(this)" 
       onblur="quitarResaltado(this)" 
       placeholder="Nombre">
```

### Criterios de evaluación

- Implementación correcta de 4 eventos diferentes (1.2 pts)
- Funcionalidad intuitiva y coherente (0.4 pts)
- Código limpio y comentado (0.4 pts)

## **EJERCICIO 5: Análisis estructural (0.5 puntos)**

Redacta un archivo **analisis.md** (formato Markdown) que contenga:

1. **Justificación de la estructura XML elegida** (¿por qué esa jerarquía?)
2. **Ventajas del uso de XML** en este proyecto específico
3. **Comparación breve** entre almacenar los datos en XML vs. una base de datos relacional para este caso
4. **Editor utilizado** y justificación de la elección

**Formato de entrega:** Subir archivo comprimido a la plataforma educativa con el nombre **Tema3.zip**
