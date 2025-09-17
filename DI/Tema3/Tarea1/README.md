# Tarea Guiada: Generación de interfaces a partir de documentos XML

## Introducción

Esta tarea guiada te permitirá practicar los conceptos fundamentales del Tema 3 a través de ejercicios progresivos que cubren XML, XHTML, SVG y eventos. Cada ejercicio incluye un análisis del problema y una solución completa paso a paso para que puedas seguirlo sin dificultad.

## Ejercicio 1: Estructura básica de un documento XML

### Objetivo

Crear un documento XML bien formado que almacene información sobre una biblioteca personal digital.

### Análisis del problema

Necesitamos crear un sistema para gestionar información de libros. Piensa en qué estructura sería la más adecuada:

- ¿Cuál debería ser el elemento raíz?
- ¿Qué información es fundamental para cada libro?
- ¿Qué datos deberían ser elementos y cuáles atributos?
- ¿Cómo podemos clasificar los libros por tipos?

**Reflexiona sobre estas preguntas antes de ver la solución.**

### Paso 1: Crear el archivo XML

Crea un nuevo archivo llamado `biblioteca_personal.xml` en tu editor de texto preferido.

### Paso 2: Declaración XML

Escribe la declaración XML al inicio del documento:

```xml
<?xml version="1.0" encoding="UTF-8"?>
```

**Explicación**: Esta línea indica que usamos XML versión 1.0 con codificación UTF-8.

### Paso 3: Elemento raíz

Añade el elemento raíz que contendrá toda la información:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<biblioteca_personal>

</biblioteca_personal>
```

### Paso 4: Añadir libros con atributos

Dentro del elemento raíz, añade varios libros con sus datos:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<biblioteca_personal>
    <libro tipo="programacion" isbn="978-84-9171-324-1">
        <titulo>Desarrollo de Interfaces Web</titulo>
        <autor>Diana García-Miguel López</autor>
        <editorial>Síntesis</editorial>
        <año>2020</año>
        <paginas>210</paginas>
        <disponible>true</disponible>
    </libro>
    
    <libro tipo="novela" isbn="978-84-376-0494-7">
        <titulo>Cien años de soledad</titulo>
        <autor>Gabriel García Márquez</autor>
        <editorial>Cátedra</editorial>
        <año>1967</año>
        <paginas>471</paginas>
        <disponible>false</disponible>
    </libro>
    
    <libro tipo="tecnico" isbn="978-84-415-3582-5">
        <titulo>Programación en Java</titulo>
        <autor>Luis Joyanes Aguilar</autor>
        <editorial>McGraw-Hill</editorial>
        <año>2019</año>
        <paginas>856</paginas>
        <disponible>true</disponible>
    </libro>
</biblioteca_personal>
```

### Paso 5: Verificar la estructura

Guarda el archivo y verifica que:

- Todas las etiquetas están correctamente cerradas
- Los atributos están entre comillas
- La estructura jerárquica es correcta

### Paso 6: Validación online del documento XML

**IMPORTANTE**: Debes validar tu documento XML usando un validador online:

1. Accede a **https://www.xmlvalidation.com/**
2. Copia y pega el contenido de tu archivo `biblioteca_personal.xml` en el área de texto
3. Haz clic en "Validate XML"
4. Si aparecen errores, corrígelos en tu archivo local
5. Repite el proceso hasta que el validador muestre "XML is valid"
6. **Toma una captura de pantalla** del mensaje de validación exitosa

**Alternativamente**, puedes usar:

- **https://codebeautify.org/xmlvalidator**
- **https://www.freeformatter.com/xml-validator-xsd.html**

**Resultado esperado**: Un documento XML válido que representa una biblioteca personal con tres libros, cada uno con sus metadatos correspondientes, y confirmación de validez mediante validador online.

## Ejercicio 2: Creación de una página HTML

### Objetivo

Crear una página web HTML que muestre la información de nuestra biblioteca personal.

### Análisis del problema

Ahora necesitamos mostrar los datos de la biblioteca en una página web. Considera:

- ¿Qué elementos XHTML son obligatorios?
- ¿Cómo podemos estructurar visualmente los libros?
- ¿Qué estilos CSS harían más atractiva la presentación?
- ¿Cómo diferenciamos libros disponibles de los no disponibles?

**Piensa en el diseño antes de implementar la solución.**

### Paso 1: Estructura básica HTML

Crea un archivo llamado `biblioteca.html` con la estructura obligatoria:

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <title>Mi Biblioteca Personal</title>
    <meta charset="utf-8" />
</head>
<body>

</body>
</html>
```

### Paso 2: Añadir contenido en el body

Completa el contenido dentro del elemento `<body>`:

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <title>Mi Biblioteca Personal</title>
    <meta charset="utf-8" />
    <style type="text/css">
        body { font-family: Arial, sans-serif; margin: 20px; }
        h1 { color: #2c3e50; }
        .libro { border: 1px solid #bdc3c7; margin: 10px 0; padding: 15px; border-radius: 5px; }
        .disponible { background-color: #d5f4e6; }
        .no-disponible { background-color: #fadbd8; }
        .isbn { font-size: 0.9em; color: #7f8c8d; }
    </style>
</head>
<body>
    <h1>Mi Biblioteca Personal</h1>
    
    <div class="libro disponible">
        <h2>Desarrollo de Interfaces Web</h2>
        <p><strong>Autor:</strong> Diana García-Miguel López</p>
        <p><strong>Editorial:</strong> Síntesis</p>
        <p><strong>Año:</strong> 2020</p>
        <p><strong>Páginas:</strong> 210</p>
        <p><strong>Tipo:</strong> Programación</p>
        <p class="isbn"><strong>ISBN:</strong> 978-84-9171-324-1</p>
        <p><strong>Estado:</strong> Disponible</p>
    </div>
    
    <div class="libro no-disponible">
        <h2>Cien años de soledad</h2>
        <p><strong>Autor:</strong> Gabriel García Márquez</p>
        <p><strong>Editorial:</strong> Cátedra</p>
        <p><strong>Año:</strong> 1967</p>
        <p><strong>Páginas:</strong> 471</p>
        <p><strong>Tipo:</strong> Novela</p>
        <p class="isbn"><strong>ISBN:</strong> 978-84-376-0494-7</p>
        <p><strong>Estado:</strong> No disponible</p>
    </div>
    
    <div class="libro disponible">
        <h2>Programación en Java</h2>
        <p><strong>Autor:</strong> Luis Joyanes Aguilar</p>
        <p><strong>Editorial:</strong> McGraw-Hill</p>
        <p><strong>Año:</strong> 2019</p>
        <p><strong>Páginas:</strong> 856</p>
        <p><strong>Tipo:</strong> Técnico</p>
        <p class="isbn"><strong>ISBN:</strong> 978-84-415-3582-5</p>
        <p><strong>Estado:</strong> Disponible</p>
    </div>
</body>
</html>
```

### Paso 3: Verificar cumplimiento XHTML

Asegúrate de que el código cumple las reglas XHTML:

- Elementos correctamente anidados 
- Todas las etiquetas cerradas 
- Elementos y atributos en minúsculas 
- Valores de atributos entre comillas 

### Paso 4: Validación online del documento XHTML

**IMPORTANTE**: Debes validar tu documento HTML usando el validador oficial del W3C:

1. Accede a **https://validator.w3.org/**
2. Selecciona la pestaña "Validate by Direct Input"
3. Copia y pega el contenido completo de tu archivo `biblioteca.xhtml`
4. Haz clic en "Check"
5. Si aparecen errores o advertencias, corrígelos en tu archivo local
6. Repite el proceso hasta que aparezca "Document checking completed. No errors or warnings to show."
7. **Toma una captura de pantalla** del resultado de validación exitosa

**Alternativamente**, puedes usar:

- **https://html5.validator.nu/**
- **https://www.freeformatter.com/html-validator.html**

**Resultado esperado**: Una página web que muestra los libros de la biblioteca con estilos CSS, estructura HTML válida y confirmación de validez mediante el validador W3C.

## Ejercicio 3: Creación de elementos gráficos con SVG

### Objetivo

Crear un logo para la biblioteca usando gráficos vectoriales escalables (SVG).

### Análisis del problema

Necesitamos diseñar un logo representativo para nuestra biblioteca. Reflexiona sobre:

- ¿Qué elementos visuales representan mejor una biblioteca?
- ¿Qué formas geométricas básicas de SVG podemos usar?
- ¿Cómo podemos combinar colores y formas para crear un diseño atractivo?
- ¿Qué ventajas tiene usar SVG frente a imágenes bitmap?

**Piensa en el concepto del logo antes de programarlo.**

### Paso 1: Estructura básica del SVG

Crea un archivo llamado `logo_biblioteca.html` con el contenido SVG integrado:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Logo de la Biblioteca</title>
</head>
<body>
    <h1>Logo de Mi Biblioteca Personal</h1>
    
    <svg width="440" height="200" style="border: 1px solid #ccc;">
        <!-- Aquí irán nuestros elementos gráficos -->
    </svg>
</body>
</html>
```

### Paso 2: Añadir elementos geométricos básicos

Completa el SVG con diferentes formas geométricas:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>Logo de la Biblioteca</title>
</head>
<body>
    <h1>Logo de Mi Biblioteca Personal</h1>
    
    <svg width="440" height="200" style="border: 1px solid #ccc;">
        <!-- Fondo del logo -->
        <rect x="20" y="20" width="400" height="160" fill="#f8f9fa" stroke="#2c3e50" stroke-width="2" />
        
        <!-- Representación de libros (rectángulos) -->
        <rect x="40" y="60" width="15" height="80" fill="#e74c3c" />
        <rect x="60" y="50" width="15" height="90" fill="#3498db" />
        <rect x="80" y="70" width="15" height="70" fill="#2ecc71" />
        <rect x="100" y="45" width="15" height="95" fill="#f39c12" />
        <rect x="120" y="65" width="15" height="75" fill="#9b59b6" />
        
        <!-- Círculo decorativo -->
        <circle cx="200" cy="100" r="30" fill="none" stroke="#2c3e50" stroke-width="3" />
        
        <!-- Texto del logo -->
        <text x="250" y="80" font-family="Arial, sans-serif" font-size="18" font-weight="bold" fill="#2c3e50">
            MI BIBLIOTECA
        </text>
        <text x="280" y="100" font-family="Arial, sans-serif" font-size="14" fill="#7f8c8d">
            PERSONAL
        </text>
        
        <!-- Línea decorativa -->
        <line x1="250" y1="110" x2="390" y2="110" stroke="#bdc3c7" stroke-width="2" />
        
        <!-- Polígono decorativo (estrella) -->
        <polygon points="320,130 325,140 335,140 327,147 330,157 320,150 310,157 313,147 305,140 315,140" 
                 fill="#f1c40f" stroke="#f39c12" stroke-width="1" />
    </svg>
    
    <p>Este logo representa una colección de libros diversos, simbolizando el conocimiento almacenado en nuestra biblioteca personal.</p>
</body>
</html>

```

**Explicación de elementos SVG utilizados**:

- `<rect>`: Rectángulos para representar libros y marco
- `<circle>`: Círculo decorativo
- `<text>`: Texto del logo
- `<line>`: Línea decorativa
- `<polygon>`: Estrella decorativa


### Paso 3: Añadir elementos más complejos

Mejora el logo añadiendo una elipse y más detalles:

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="utf-8" />
    <title>Logo de la Biblioteca</title>
</head>
<body>
    <h1>Logo de Mi Biblioteca Personal</h1>
    
    <svg width="440" height="200" style="border: 1px solid #ccc;">
        <!-- Fondo del logo -->
        <rect x="20" y="20" width="400" height="160" fill="#f8f9fa" stroke="#2c3e50" stroke-width="2" />
        
        <!-- Elipse de fondo -->
        <ellipse cx="200" cy="100" rx="180" ry="80" fill="none" stroke="#ecf0f1" stroke-width="1" opacity="0.5" />
        
        <!-- Representación de libros (rectángulos) -->
        <rect x="40" y="60" width="15" height="80" fill="#e74c3c" />
        <rect x="60" y="50" width="15" height="90" fill="#3498db" />
        <rect x="80" y="70" width="15" height="70" fill="#2ecc71" />
        <rect x="100" y="45" width="15" height="95" fill="#f39c12" />
        <rect x="120" y="65" width="15" height="75" fill="#9b59b6" />
        
        <!-- Detalles en los libros -->
        <rect x="42" y="65" width="11" height="2" fill="#c0392b" />
        <rect x="62" y="55" width="11" height="2" fill="#2980b9" />
        <rect x="82" y="75" width="11" height="2" fill="#27ae60" />
        <rect x="102" y="50" width="11" height="2" fill="#e67e22" />
        <rect x="122" y="70" width="11" height="2" fill="#8e44ad" />
        
        <!-- Círculo decorativo -->
        <circle cx="200" cy="100" r="30" fill="none" stroke="#2c3e50" stroke-width="3" />
        <circle cx="200" cy="100" r="25" fill="none" stroke="#3498db" stroke-width="1" />
        
        <!-- Texto del logo -->
        <text x="250" y="80" font-family="Arial, sans-serif" font-size="18" font-weight="bold" fill="#2c3e50">
            MI BIBLIOTECA
        </text>
        <text x="280" y="100" font-family="Arial, sans-serif" font-size="14" fill="#7f8c8d">
            PERSONAL
        </text>
        
        <!-- Línea decorativa -->
        <line x1="250" y1="110" x2="390" y2="110" stroke="#bdc3c7" stroke-width="2" />
        
        <!-- Polígono decorativo (estrella) -->
        <polygon points="320,130 325,140 335,140 327,147 330,157 320,150 310,157 313,147 305,140 315,140" 
                 fill="#f1c40f" stroke="#f39c12" stroke-width="1" />
    </svg>
    
    <p>Este logo vectorial es completamente escalable y representa nuestra biblioteca personal con elementos gráficos diversos.</p>
</body>
</html>
```

**Resultado esperado**: Un logo completo creado con SVG que incluye múltiples elementos geométricos y es completamente escalable.


## Ejercicio 4: Implementación de eventos

### Objetivo

Añadir interactividad a nuestros elementos utilizando eventos JavaScript.

### Análisis del problema

Queremos hacer nuestra biblioteca interactiva. Piensa en:

- ¿Qué tipos de eventos serían útiles para los usuarios?
- ¿Cómo podemos proporcionar retroalimentación visual?
- ¿Qué funcionalidades mejorarían la experiencia del usuario?
- ¿Cómo podemos combinar diferentes tipos de eventos (click, hover, focus, etc.)?

**Considera la experiencia del usuario antes de implementar.**

### Paso 1: Crear la estructura HTML con elementos interactivos

Crea un archivo llamado `biblioteca_interactiva.html`:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Biblioteca Interactiva</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; }
        .libro { 
            border: 2px solid #bdc3c7; 
            margin: 10px 0; 
            padding: 15px; 
            border-radius: 5px; 
            cursor: pointer;
            transition: all 0.3s ease;
        }
        .libro:hover { 
            background-color: #ecf0f1; 
            border-color: #3498db;
        }
        .libro.seleccionado { 
            background-color: #d5f4e6; 
            border-color: #2ecc71;
        }
        .detalles { 
            display: none; 
            margin-top: 10px; 
            padding: 10px; 
            background-color: #f8f9fa; 
            border-radius: 3px;
        }
        #contador { 
            position: fixed; 
            top: 10px; 
            right: 10px; 
            background-color: #3498db; 
            color: white; 
            padding: 10px; 
            border-radius: 5px;
        }
        .boton { 
            background-color: #2ecc71; 
            color: white; 
            border: none; 
            padding: 8px 16px; 
            border-radius: 4px; 
            cursor: pointer; 
            margin: 5px;
        }
        .boton:hover { background-color: #27ae60; }
    </style>
</head>
<body>
    <h1>Mi Biblioteca Interactiva</h1>
    <div id="contador">Libros seleccionados: 0</div>
    
    <div class="libro" id="libro1">
        <h3>Desarrollo de Interfaces Web</h3>
        <p><strong>Autor:</strong> Diana García-Miguel López</p>
        <button class="boton" onclick="mostrarDetalles('detalles1')">Ver Detalles</button>
        <div class="detalles" id="detalles1">
            <p><strong>Editorial:</strong> Síntesis</p>
            <p><strong>Año:</strong> 2020</p>
            <p><strong>Páginas:</strong> 210</p>
            <p><strong>ISBN:</strong> 978-84-9171-324-1</p>
        </div>
    </div>
    
    <div class="libro" id="libro2">
        <h3>Cien años de soledad</h3>
        <p><strong>Autor:</strong> Gabriel García Márquez</p>
        <button class="boton" onclick="mostrarDetalles('detalles2')">Ver Detalles</button>
        <div class="detalles" id="detalles2">
            <p><strong>Editorial:</strong> Cátedra</p>
            <p><strong>Año:</strong> 1967</p>
            <p><strong>Páginas:</strong> 471</p>
            <p><strong>ISBN:</strong> 978-84-376-0494-7</p>
        </div>
    </div>
    
    <div class="libro" id="libro3">
        <h3>Programación en Java</h3>
        <p><strong>Autor:</strong> Luis Joyanes Aguilar</p>
        <button class="boton" onclick="mostrarDetalles('detalles3')">Ver Detalles</button>
        <div class="detalles" id="detalles3">
            <p><strong>Editorial:</strong> McGraw-Hill</p>
            <p><strong>Año:</strong> 2019</p>
            <p><strong>Páginas:</strong> 856</p>
            <p><strong>ISBN:</strong> 978-84-415-3582-5</p>
        </div>
    </div>
</body>
</html>
```

### Paso 2: Añadir funcionalidad JavaScript

Completa el archivo añadiendo el JavaScript antes de cerrar `</body>`:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Biblioteca Interactiva</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; }
        .libro { 
            border: 2px solid #bdc3c7; 
            margin: 10px 0; 
            padding: 15px; 
            border-radius: 5px; 
            cursor: pointer;
            transition: all 0.3s ease;
        }
        .libro:hover { 
            background-color: #ecf0f1; 
            border-color: #3498db;
        }
        .libro.seleccionado { 
            background-color: #d5f4e6; 
            border-color: #2ecc71;
        }
        .detalles { 
            display: none; 
            margin-top: 10px; 
            padding: 10px; 
            background-color: #f8f9fa; 
            border-radius: 3px;
        }
        #contador { 
            position: fixed; 
            top: 10px; 
            right: 10px; 
            background-color: #3498db; 
            color: white; 
            padding: 10px; 
            border-radius: 5px;
        }
        .boton { 
            background-color: #2ecc71; 
            color: white; 
            border: none; 
            padding: 8px 16px; 
            border-radius: 4px; 
            cursor: pointer; 
            margin: 5px;
        }
        .boton:hover { background-color: #27ae60; }
        .mensaje { 
            position: fixed; 
            bottom: 20px; 
            left: 20px; 
            background-color: #2c3e50; 
            color: white; 
            padding: 10px; 
            border-radius: 5px; 
            display: none;
        }
    </style>
</head>
<body>
    <h1>Mi Biblioteca Interactiva</h1>
    <div id="contador">Libros seleccionados: 0</div>
    <div id="mensaje" class="mensaje"></div>
    
    <div class="libro" id="libro1" onclick="seleccionarLibro(this)">
        <h3>Desarrollo de Interfaces Web</h3>
        <p><strong>Autor:</strong> Diana García-Miguel López</p>
        <button class="boton" onclick="mostrarDetalles('detalles1', event)">Ver Detalles</button>
        <div class="detalles" id="detalles1">
            <p><strong>Editorial:</strong> Síntesis</p>
            <p><strong>Año:</strong> 2020</p>
            <p><strong>Páginas:</strong> 210</p>
            <p><strong>ISBN:</strong> 978-84-9171-324-1</p>
        </div>
    </div>
    
    <div class="libro" id="libro2" onclick="seleccionarLibro(this)">
        <h3>Cien años de soledad</h3>
        <p><strong>Autor:</strong> Gabriel García Márquez</p>
        <button class="boton" onclick="mostrarDetalles('detalles2', event)">Ver Detalles</button>
        <div class="detalles" id="detalles2">
            <p><strong>Editorial:</strong> Cátedra</p>
            <p><strong>Año:</strong> 1967</p>
            <p><strong>Páginas:</strong> 471</p>
            <p><strong>ISBN:</strong> 978-84-376-0494-7</p>
        </div>
    </div>
    
    <div class="libro" id="libro3" onclick="seleccionarLibro(this)">
        <h3>Programación en Java</h3>
        <p><strong>Autor:</strong> Luis Joyanes Aguilar</p>
        <button class="boton" onclick="mostrarDetalles('detalles3', event)">Ver Detalles</button>
        <div class="detalles" id="detalles3">
            <p><strong>Editorial:</strong> McGraw-Hill</p>
            <p><strong>Año:</strong> 2019</p>
            <p><strong>Páginas:</strong> 856</p>
            <p><strong>ISBN:</strong> 978-84-415-3582-5</p>
        </div>
    </div>

    <script>
        let librosSeleccionados = 0;
        
        // Evento Click: Seleccionar libro
        function seleccionarLibro(elemento) {
            if (elemento.classList.contains('seleccionado')) {
                elemento.classList.remove('seleccionado');
                librosSeleccionados--;
                mostrarMensaje('Libro deseleccionado');
            } else {
                elemento.classList.add('seleccionado');
                librosSeleccionados++;
                mostrarMensaje('Libro seleccionado');
            }
            
            // Actualizar contador
            document.getElementById('contador').textContent = 
                'Libros seleccionados: ' + librosSeleccionados;
        }
        
        // Evento Click: Mostrar detalles
        function mostrarDetalles(idDetalles, evento) {
            // Prevenir que se ejecute el click del libro padre
            evento.stopPropagation();
            
            const detalles = document.getElementById(idDetalles);
            if (detalles.style.display === 'none' || detalles.style.display === '') {
                detalles.style.display = 'block';
                mostrarMensaje('Detalles mostrados');
            } else {
                detalles.style.display = 'none';
                mostrarMensaje('Detalles ocultados');
            }
        }
        
        // Función para mostrar mensajes temporales
        function mostrarMensaje(texto) {
            const mensaje = document.getElementById('mensaje');
            mensaje.textContent = texto;
            mensaje.style.display = 'block';
            
            // Ocultar mensaje después de 2 segundos
            setTimeout(() => {
                mensaje.style.display = 'none';
            }, 2000);
        }
        
        // Evento MouseEnter y MouseLeave para efectos adicionales
        document.querySelectorAll('.libro').forEach(libro => {
            libro.addEventListener('mouseenter', function() {
                this.style.transform = 'scale(1.02)';
            });
            
            libro.addEventListener('mouseleave', function() {
                this.style.transform = 'scale(1.0)';
            });
        });
        
        // Evento Change: Para demostrar eventos en formularios
        window.addEventListener('load', function() {
            mostrarMensaje('Biblioteca cargada correctamente');
        });
    </script>
</body>
</html>
```

**Explicación de eventos implementados**:

- **Click**: Para seleccionar libros y mostrar detalles
- **MouseEnter/MouseLeave**: Para efectos visuales al pasar el ratón
- **Load**: Se ejecuta cuando la página termina de cargar
- **Change**: Ejemplo de evento para formularios

**Resultado esperado**: Una página interactiva donde los usuarios pueden seleccionar libros, ver detalles, y recibir retroalimentación visual de sus acciones.

## Entrega de la tarea

### Archivos a entregar

Debes crear un archivo comprimido llamado **`TareasTema3.zip`** que contenga los siguientes archivos:

1. **`biblioteca_personal.xml`** - Documento XML de la biblioteca
2. **`biblioteca.xhtml`** - Página XHTML con los libros
3. **`logo_biblioteca.html`** - Logo SVG de la biblioteca
4. **`biblioteca_interactiva.html`** - Página con eventos interactivos
5. **Capturas de pantalla**:
    - `validacion_xml.png` - Captura de la validación exitosa del XML
    - `validacion_xhtml.png` - Captura de la validación exitosa del XHTML

**¡Recuerda que esta es una tarea guiada no evaluable, pero te prepara para la práctica evaluable del tema!**
