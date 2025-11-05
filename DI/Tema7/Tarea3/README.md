# **TEMA 7: Usabilidad**

## **Tarea 3: Diseño e implementación guiada de formulario usable**

## **Objetivo y alcance**

Diseñar e implementar paso a paso un formulario web de alta de cliente que minimice errores, reduzca tiempos y mejore la satisfacción del usuario, aplicando las directrices de la norma **ISO 9241-17** (diálogos por cumplimentación de formularios). Al finalizar, cada miembro de la pareja evaluará el formulario de su compañero utilizando casos de prueba y un checklist ISO 9241-17.

***

## **Estructura inicial del proyecto**

Crear una carpeta con la siguiente estructura:

```text
formulario-alta-cliente/
├── index.html
├── styles.css
└── app.js
```

***

**DESPLIEGA Y LEE EL SIGUIENTE APARTADO ANTES DE PASAR A LA SOLUCIÓN GUIADA**

<details>
<summary>Enunciado sin ayuda</summary>
### **Campos obligatorios del formulario**

El formulario debe incluir los siguientes campos con sus características específicas:

**1. Nombre completo**

- Tipo: texto (`<input type="text">`)
- Etiqueta clara con `<label for="nombre">`
- Ejemplo visible: "Ej: María García López"

**2. Correo electrónico**

- Tipo: email (`<input type="email">`)
- Validación de formato en tiempo real
- Mensaje de ayuda: "Formato: usuario@dominio.com"

**3. Contraseña**

- Tipo: password (`<input type="password">`)
- Requisitos visibles antes de escribir:
    - Mínimo 8 caracteres
    - Al menos una mayúscula
    - Al menos un número
    - Al menos un carácter especial (@, \#, \$, etc.)
- Validación progresiva mientras se escribe

**4. Región**

- Tipo: lista desplegable (`<select>`)
- Opciones: Andalucía, Cataluña, Madrid, Comunidad Valenciana
- Al seleccionar, se activa el campo provincia

**5. Provincia**

- Tipo: lista desplegable dependiente de región
- Deshabilitado inicialmente hasta seleccionar región
- Al cambiar región, se limpia y deshabilita
- Opciones según región:
    - Andalucía: Sevilla, Málaga, Cádiz, Granada
    - Cataluña: Barcelona, Girona, Tarragona, Lleida
    - Madrid: Madrid
    - Comunidad Valenciana: Valencia, Alicante, Castellón

**6. Localidad**

- Tipo: lista desplegable dependiente de provincia
- Deshabilitado hasta seleccionar provincia
- Al cambiar provincia o región, se limpia y deshabilita
- Opciones según provincia (ejemplos):
    - Sevilla: Sevilla capital, Dos Hermanas, Alcalá de Guadaíra, Utrera
    - Málaga: Málaga capital, Marbella, Mijas, Torremolinos
    - Barcelona: Barcelona capital, Hospitalet, Badalona, Terrassa
    - (completar con más opciones según necesidad)

**7. Dirección postal**

- Tipo: texto (`<input type="text">`)
- Ejemplo: "Calle, número, piso, puerta"

**8. Prefijo telefónico**

- Tipo: texto (`<input type="text">` con atributo `readonly` temporal)
- Se autocompleta según región seleccionada:
    - Andalucía: +34 95
    - Cataluña: +34 93
    - Madrid: +34 91
    - Comunidad Valenciana: +34 96
- Debe permitir ajuste manual si el usuario lo necesita


### **Funcionalidades obligatorias**

**Dependencias en cascada:**

- Al seleccionar región → habilitar provincia y autocompletar prefijo
- Al cambiar región → limpiar y deshabilitar provincia y localidad
- Al seleccionar provincia → habilitar localidad
- Al cambiar provincia → limpiar y deshabilitar localidad

**Validaciones en tiempo real:**

- Email: validar formato mientras se escribe, mostrar ✓ o ✗
- Contraseña: mostrar requisitos cumplidos/pendientes con indicadores visuales
- Campos obligatorios: indicar visualmente los campos vacíos

**Control del botón de envío:**

- Deshabilitado (`disabled`) inicialmente
- Se habilita solo cuando todos los campos obligatorios son válidos
- Texto del botón: "Crear cuenta"

**Mensajes de error:**

- Específicos por campo (no genéricos)
- Lenguaje positivo y constructivo (no culpabilizador)
- Indican qué sucedió, por qué y cómo resolverlo
- Aparecen bajo el campo correspondiente
- Ejemplo CORRECTO: "El email debe incluir @ y un dominio válido"
- Ejemplo INCORRECTO: "Email inválido"

**Navegación y foco:**

- Foco automático (`autofocus`) en el primer campo al cargar
- Orden de tabulación lógico (de arriba a abajo)
- Estados `:focus` visibles con borde o contorno destacado
- Sin saltos inesperados de foco

**Preservación de datos:**

- Al corregir un error, mantener los datos válidos del resto de campos
- No borrar todo el formulario por un solo error

***

</details>

## **Paso 1: Estructura HTML básica**

### **Objetivo**

Crear la estructura semántica del formulario con los campos necesarios y sus etiquetas asociadas.

### **Campos obligatorios**

- Nombre completo
- Correo electrónico
- Contraseña
- Región (select)
- Provincia (select dependiente)
- Localidad (select dependiente)
- Dirección postal
- Prefijo telefónico (autocompletado)


### **Código: index.html**

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Alta de Cliente</title>
    <link rel="stylesheet" href="styles.css">
    <script src="app.js" defer></script>
</head>
<body>
    <main>
        <h1>Formulario de Alta de Cliente</h1>
        <form id="formAlta" novalidate>
            <fieldset>
                <legend>Datos personales</legend>
                
                <div class="campo">
                    <label for="nombre">Nombre completo *</label>
                    <input type="text" id="nombre" name="nombre" required autofocus>
                    <small>Ej: María García López</small>
                    <span class="error" id="error-nombre"></span>
                </div>

                <div class="campo">
                    <label for="email">Correo electrónico *</label>
                    <input type="email" id="email" name="email" required>
                    <small>Formato: usuario@dominio.com</small>
                    <span class="error" id="error-email"></span>
                </div>

                <div class="campo">
                    <label for="password">Contraseña *</label>
                    <input type="password" id="password" name="password" required>
                    <div class="requisitos-password">
                        <p>Requisitos de contraseña:</p>
                        <ul>
                            <li id="req-longitud">✗ Mínimo 8 caracteres</li>
                            <li id="req-mayuscula">✗ Al menos una mayúscula</li>
                            <li id="req-numero">✗ Al menos un número</li>
                            <li id="req-especial">✗ Al menos un carácter especial (@#$%&*)</li>
                        </ul>
                    </div>
                    <span class="error" id="error-password"></span>
                </div>
            </fieldset>

            <fieldset>
                <legend>Ubicación</legend>
                
                <div class="campo">
                    <label for="region">Región *</label>
                    <select id="region" name="region" required>
                        <option value="">-- Seleccione una región --</option>
                        <option value="andalucia">Andalucía</option>
                        <option value="cataluna">Cataluña</option>
                        <option value="madrid">Madrid</option>
                        <option value="valencia">Comunidad Valenciana</option>
                    </select>
                    <span class="error" id="error-region"></span>
                </div>

                <div class="campo">
                    <label for="provincia">Provincia *</label>
                    <select id="provincia" name="provincia" required disabled>
                        <option value="">-- Primero seleccione región --</option>
                    </select>
                    <span class="error" id="error-provincia"></span>
                </div>

                <div class="campo">
                    <label for="localidad">Localidad *</label>
                    <select id="localidad" name="localidad" required disabled>
                        <option value="">-- Primero seleccione provincia --</option>
                    </select>
                    <span class="error" id="error-localidad"></span>
                </div>

                <div class="campo">
                    <label for="direccion">Dirección postal *</label>
                    <input type="text" id="direccion" name="direccion" required>
                    <small>Calle, número, piso, puerta</small>
                    <span class="error" id="error-direccion"></span>
                </div>

                <div class="campo">
                    <label for="prefijo">Prefijo telefónico</label>
                    <input type="text" id="prefijo" name="prefijo" readonly>
                    <small>Se autocompleta según la región</small>
                </div>
            </fieldset>

            <button type="submit" id="btnEnviar" disabled>Crear cuenta</button>
            <div id="mensaje-exito" class="exito" style="display:none;">
                ✓ Cuenta creada correctamente
            </div>
        </form>
    </main>
</body>
</html>
```


### **Explicación de elementos clave**

- **`<label for="...">`**: Asocia etiqueta con campo para accesibilidad
- **`type="email"` y `type="password"`**: Tipos específicos de input
- **`required`**: Marca campos obligatorios
- **`autofocus`**: Foco automático en primer campo
- **`novalidate`**: Desactiva validación HTML5 nativa para usar personalizada
- **`disabled`**: Deshabilita selects dependientes y botón inicialmente

```
- **`<fieldset>` y `<legend>`**: Agrupa campos relacionados
```


***

## **Paso 2: Estilos CSS**

### **Objetivo**

Definir la presentación visual con énfasis en estados de foco, validación y legibilidad.

### **Código: styles.css**

```css
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background-color: #f5f5f5;
    padding: 20px;
    line-height: 1.6;
    color: #333;
}

main {
    max-width: 650px;
    margin: 0 auto;
    background: white;
    padding: 35px;
    border-radius: 8px;
    box-shadow: 0 2px 15px rgba(0,0,0,0.1);
}

h1 {
    text-align: center;
    color: #2c3e50;
    margin-bottom: 30px;
    font-size: 1.8em;
}

fieldset {
    border: 2px solid #e0e0e0;
    padding: 25px;
    margin-bottom: 25px;
    border-radius: 6px;
    background-color: #fafafa;
}

legend {
    font-weight: bold;
    font-size: 1.2em;
    padding: 0 10px;
    color: #2c3e50;
}

.campo {
    margin-bottom: 20px;
}

label {
    display: block;
    margin-bottom: 6px;
    font-weight: 600;
    color: #2c3e50;
    font-size: 0.95em;
}

input[type="text"],
input[type="email"],
input[type="password"],
select {
    width: 100%;
    padding: 12px;
    border: 2px solid #d1d1d1;
    border-radius: 5px;
    font-size: 15px;
    transition: all 0.3s ease;
    font-family: inherit;
}

/* Estados de foco - IMPORTANTE para accesibilidad */
input:focus,
select:focus {
    outline: none;
    border-color: #3498db;
    box-shadow: 0 0 8px rgba(52, 152, 219, 0.4);
    background-color: #f0f8ff;
}

/* Estados de validación visual */
input.valido {
    border-color: #27ae60;
    background-color: #f0fff4;
}

input.invalido {
    border-color: #e74c3c;
    background-color: #fff5f5;
}

/* Campos deshabilitados */
input:disabled,
select:disabled {
    background-color: #e8e8e8;
    color: #999;
    cursor: not-allowed;
    border-color: #ccc;
}

/* Textos de ayuda */
small {
    display: block;
    margin-top: 6px;
    color: #7f8c8d;
    font-size: 0.85em;
    font-style: italic;
}

/* Mensajes de error */
.error {
    display: block;
    color: #e74c3c;
    font-size: 0.9em;
    margin-top: 6px;
    font-weight: 500;
    min-height: 20px;
}

/* Requisitos de contraseña */
.requisitos-password {
    margin-top: 12px;
    padding: 15px;
    background-color: #ecf0f1;
    border-left: 4px solid #3498db;
    border-radius: 5px;
}

.requisitos-password p {
    margin-bottom: 10px;
    font-weight: 600;
    color: #2c3e50;
    font-size: 0.95em;
}

.requisitos-password ul {
    list-style: none;
    padding-left: 0;
}

.requisitos-password li {
    padding: 5px 0;
    font-size: 0.9em;
    transition: color 0.3s;
}

.requisitos-password li.cumplido {
    color: #27ae60;
    font-weight: 600;
}

.requisitos-password li:not(.cumplido) {
    color: #e74c3c;
}

/* Botón de envío */
button[type="submit"] {
    width: 100%;
    padding: 15px;
    background-color: #27ae60;
    color: white;
    border: none;
    border-radius: 6px;
    font-size: 17px;
    font-weight: 700;
    cursor: pointer;
    transition: all 0.3s ease;
    text-transform: uppercase;
    letter-spacing: 0.5px;
}

button[type="submit"]:hover:not(:disabled) {
    background-color: #229954;
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(39, 174, 96, 0.3);
}

button[type="submit"]:disabled {
    background-color: #bdc3c7;
    cursor: not-allowed;
    transform: none;
}

/* Mensaje de éxito */
.exito {
    margin-top: 20px;
    padding: 15px;
    background-color: #d4edda;
    color: #155724;
    border: 2px solid #c3e6cb;
    border-radius: 6px;
    text-align: center;
    font-weight: 600;
    font-size: 1.05em;
}
```


### **Puntos clave de usabilidad en CSS**

- **Contraste suficiente**: Colores con ratio mínimo 4.5:1
- **Estados de foco visibles**: Borde y sombra en azul
- **Indicadores de validación**: Verde para válido, rojo para inválido
- **Espaciado adecuado**: Facilita lectura y navegación
- **Transiciones suaves**: Mejoran percepción de respuesta

***

## **Paso 3: Datos y referencias del DOM (JavaScript - Parte 1)**

### **Objetivo**

Preparar los datos necesarios para selects dependientes y obtener referencias a elementos del DOM.

### **Código: app.js (inicio)**

```javascript
// ============================================
// DATOS PARA SELECTS DEPENDIENTES
// ============================================

// Provincias organizadas por región
const datosProvincia = {
    andalucia: [
        { value: 'sevilla', text: 'Sevilla' },
        { value: 'malaga', text: 'Málaga' },
        { value: 'cadiz', text: 'Cádiz' },
        { value: 'granada', text: 'Granada' }
    ],
    cataluna: [
        { value: 'barcelona', text: 'Barcelona' },
        { value: 'girona', text: 'Girona' },
        { value: 'tarragona', text: 'Tarragona' },
        { value: 'lleida', text: 'Lleida' }
    ],
    madrid: [
        { value: 'madrid', text: 'Madrid' }
    ],
    valencia: [
        { value: 'valencia', text: 'Valencia' },
        { value: 'alicante', text: 'Alicante' },
        { value: 'castellon', text: 'Castellón' }
    ]
};

// Localidades organizadas por provincia
const datosLocalidad = {
    sevilla: ['Sevilla capital', 'Dos Hermanas', 'Alcalá de Guadaíra', 'Utrera'],
    malaga: ['Málaga capital', 'Marbella', 'Mijas', 'Torremolinos'],
    cadiz: ['Cádiz capital', 'Jerez de la Frontera', 'Algeciras', 'San Fernando'],
    granada: ['Granada capital', 'Motril', 'Almuñécar', 'Baza'],
    barcelona: ['Barcelona capital', 'Hospitalet de Llobregat', 'Badalona', 'Terrassa'],
    girona: ['Girona capital', 'Figueres', 'Blanes', 'Lloret de Mar'],
    tarragona: ['Tarragona capital', 'Reus', 'Cambrils', 'Salou'],
    lleida: ['Lleida capital', 'Tàrrega', 'Mollerussa', 'Balaguer'],
    madrid: ['Madrid capital', 'Móstoles', 'Alcalá de Henares', 'Fuenlabrada'],
    valencia: ['Valencia capital', 'Torrent', 'Gandía', 'Paterna'],
    alicante: ['Alicante capital', 'Elche', 'Torrevieja', 'Benidorm'],
    castellon: ['Castellón capital', 'Vila-real', 'Burriana', 'Vinaròs']
};

// Prefijos telefónicos por región
const prefijosRegion = {
    andalucia: '+34 95',
    cataluna: '+34 93',
    madrid: '+34 91',
    valencia: '+34 96'
};

// ============================================
// REFERENCIAS A ELEMENTOS DEL DOM
// ============================================

const formAlta = document.getElementById('formAlta');
const selectRegion = document.getElementById('region');
const selectProvincia = document.getElementById('provincia');
const selectLocalidad = document.getElementById('localidad');
const inputPrefijo = document.getElementById('prefijo');
const inputEmail = document.getElementById('email');
const inputPassword = document.getElementById('password');
const inputNombre = document.getElementById('nombre');
const inputDireccion = document.getElementById('direccion');
const btnEnviar = document.getElementById('btnEnviar');
const mensajeExito = document.getElementById('mensaje-exito');
```


### **Explicación**

- **Objetos de datos**: Organizan las opciones de forma jerárquica
- **Referencias DOM**: Evitan búsquedas repetidas con `getElementById`

***

## **Paso 4: Selects dependientes en cascada**

### **Objetivo**

Implementar la dependencia región → provincia → localidad con limpieza automática al cambiar niveles superiores.

### **Código: app.js (continuación)**

```javascript
// ============================================
// EVENTO: CAMBIO DE REGIÓN
// ============================================

selectRegion.addEventListener('change', function() {
    const regionSeleccionada = this.value;
    
    // Limpiar y deshabilitar provincia y localidad
    selectProvincia.innerHTML = '<option value="">-- Primero seleccione región --</option>';
    selectProvincia.disabled = true;
    selectLocalidad.innerHTML = '<option value="">-- Primero seleccione provincia --</option>';
    selectLocalidad.disabled = true;
    
    if (regionSeleccionada) {
        // Habilitar y cargar provincias
        selectProvincia.disabled = false;
        selectProvincia.innerHTML = '<option value="">-- Seleccione una provincia --</option>';
        
        // Añadir opciones de provincias según región
        datosProvincia[regionSeleccionada].forEach(function(prov) {
            const option = document.createElement('option');
            option.value = prov.value;
            option.textContent = prov.text;
            selectProvincia.appendChild(option);
        });
        
        // Autocompletar prefijo telefónico
        inputPrefijo.value = prefijosRegion[regionSeleccionada];
        inputPrefijo.removeAttribute('readonly');
    } else {
        // Si no hay región, limpiar prefijo
        inputPrefijo.value = '';
        inputPrefijo.setAttribute('readonly', 'readonly');
    }
    
    // Revalidar formulario completo
    validarFormularioCompleto();
});

// ============================================
// EVENTO: CAMBIO DE PROVINCIA
// ============================================

selectProvincia.addEventListener('change', function() {
    const provinciaSeleccionada = this.value;
    
    // Limpiar y deshabilitar localidad
    selectLocalidad.innerHTML = '<option value="">-- Primero seleccione provincia --</option>';
    selectLocalidad.disabled = true;
    
    if (provinciaSeleccionada) {
        // Habilitar y cargar localidades
        selectLocalidad.disabled = false;
        selectLocalidad.innerHTML = '<option value="">-- Seleccione una localidad --</option>';
        
        // Añadir opciones de localidades según provincia
        datosLocalidad[provinciaSeleccionada].forEach(function(loc) {
            const option = document.createElement('option');
            option.value = loc.toLowerCase().replace(/ /g, '-');
            option.textContent = loc;
            selectLocalidad.appendChild(option);
        });
    }
    
    // Revalidar formulario completo
    validarFormularioCompleto();
});

// ============================================
// EVENTO: CAMBIO DE LOCALIDAD
// ============================================

selectLocalidad.addEventListener('change', function() {
    // Revalidar formulario al seleccionar localidad
    validarFormularioCompleto();
});
```


### **Explicación técnica**

- **Limpieza en cascada**: Al cambiar región, se resetean provincia y localidad
- **Deshabilitación**: Los selects dependientes se deshabilitan hasta tener valor en el nivel superior
- **`createElement` y `appendChild`**: Crean opciones dinámicamente
- **Autocompletado**: El prefijo se rellena automáticamente según región

***

## **Paso 5: Validación de email en tiempo real**

### **Objetivo**

Validar formato de email mientras el usuario escribe, mostrando mensajes específicos.

### **Código: app.js (continuación)**

```javascript
// ============================================
// VALIDACIÓN: EMAIL EN TIEMPO REAL
// ============================================

inputEmail.addEventListener('input', function() {
    const email = this.value;
    const errorEmail = document.getElementById('error-email');
    
    // Expresión regular para validar email
    const regexEmail = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    
    if (email === '') {
        // Campo vacío: sin mensaje ni clase
        errorEmail.textContent = '';
        this.classList.remove('valido', 'invalido');
    } else if (!regexEmail.test(email)) {
        // Email inválido: mostrar mensaje constructivo
        errorEmail.textContent = 'El email debe incluir @ y un dominio válido (ej: usuario@ejemplo.com)';
        this.classList.remove('valido');
        this.classList.add('invalido');
    } else {
        // Email válido: limpiar mensaje y marcar como válido
        errorEmail.textContent = '';
        this.classList.remove('invalido');
        this.classList.add('valido');
    }
    
    // Revalidar formulario completo
    validarFormularioCompleto();
});
```


### **Explicación de validación**

- **Expresión regular**: `/^[^\s@]+@[^\s@]+\.[^\s@]+$/` valida estructura básica
- **Mensajes específicos**: Indica exactamente qué falta (@ y dominio)
- **Clases CSS**: `valido` e `invalido` cambian el estilo visual
- **Lenguaje positivo**: "debe incluir" en lugar de "error en email"

***

## **Paso 6: Validación de contraseña con requisitos visuales**

### **Objetivo**

Validar contraseña en tiempo real mostrando requisitos cumplidos/pendientes con indicadores visuales.

### **Código: app.js (continuación)**

```javascript
// ============================================
// VALIDACIÓN: CONTRASEÑA EN TIEMPO REAL
// ============================================

inputPassword.addEventListener('input', function() {
    const password = this.value;
    
    // Validar cada requisito
    const tieneLongitud = password.length >= 8;
    const tieneMayuscula = /[A-Z]/.test(password);
    const tieneNumero = /[0-9]/.test(password);
    const tieneEspecial = /[@#$%&*]/.test(password);
    
    // Actualizar indicadores visuales de cada requisito
    actualizarRequisito('req-longitud', tieneLongitud);
    actualizarRequisito('req-mayuscula', tieneMayuscula);
    actualizarRequisito('req-numero', tieneNumero);
    actualizarRequisito('req-especial', tieneEspecial);
    
    // Marcar campo como válido o inválido
    const passwordValido = tieneLongitud && tieneMayuscula && tieneNumero && tieneEspecial;
    
    if (password === '') {
        this.classList.remove('valido', 'invalido');
    } else if (passwordValido) {
        this.classList.remove('invalido');
        this.classList.add('valido');
    } else {
        this.classList.remove('valido');
        this.classList.add('invalido');
    }
    
    // Revalidar formulario completo
    validarFormularioCompleto();
});

// ============================================
// FUNCIÓN AUXILIAR: ACTUALIZAR REQUISITO
// ============================================

function actualizarRequisito(idRequisito, cumplido) {
    const elemento = document.getElementById(idRequisito);
    
    if (cumplido) {
        // Requisito cumplido: cambiar ✗ por ✓
        elemento.textContent = elemento.textContent.replace('✗', '✓');
        elemento.classList.add('cumplido');
    } else {
        // Requisito pendiente: cambiar ✓ por ✗
        elemento.textContent = elemento.textContent.replace('✓', '✗');
        elemento.classList.remove('cumplido');
    }
}
```


### **Explicación de validación**

- **Expresiones regulares**:
    - `/[A-Z]/`: Al menos una mayúscula
    - `/[0-9]/`: Al menos un número
    - `/[@#$%&*]/`: Al menos un carácter especial
- **Feedback progresivo**: Cada requisito se actualiza independientemente
- **Indicadores visuales**: ✓ verde para cumplido, ✗ rojo para pendiente

***

## **Paso 7: Validación completa del formulario**

### **Objetivo**

Verificar que todos los campos sean válidos para habilitar/deshabilitar el botón de envío.

### **Código: app.js (continuación)**

```javascript
// ============================================
// VALIDACIÓN: FORMULARIO COMPLETO
// ============================================

function validarFormularioCompleto() {
    // Verificar cada campo obligatorio
    const nombreValido = inputNombre.value.trim() !== '';
    const emailValido = inputEmail.classList.contains('valido');
    const passwordValido = inputPassword.classList.contains('valido');
    const regionValida = selectRegion.value !== '';
    const provinciaValida = selectProvincia.value !== '';
    const localidadValida = selectLocalidad.value !== '';
    const direccionValida = inputDireccion.value.trim() !== '';
    
    // Formulario válido si todos los campos son válidos
    const formularioValido = nombreValido && emailValido && passwordValido && 
                             regionValida && provinciaValida && localidadValida && 
                             direccionValida;
    
    // Habilitar o deshabilitar botón de envío
    btnEnviar.disabled = !formularioValido;
}

// ============================================
// EVENTOS: VALIDAR AL ESCRIBIR EN CAMPOS DE TEXTO
// ============================================

inputNombre.addEventListener('input', validarFormularioCompleto);
inputDireccion.addEventListener('input', validarFormularioCompleto);
```


### **Explicación de lógica**

- **Verificación individual**: Cada campo se comprueba independientemente
- **Operador AND**: Todos deben ser válidos para habilitar botón
- **`.trim()`**: Elimina espacios al inicio/final para evitar campos "vacíos" con espacios
- **`.classList.contains()`**: Verifica si el campo tiene clase `valido`

***

## **Paso 8: Manejo del envío del formulario**

### **Objetivo**

Procesar el envío, mostrar mensaje de éxito y resetear el formulario.

### **Código: app.js (continuación - final)**

```javascript
// ============================================
// EVENTO: ENVÍO DEL FORMULARIO
// ============================================

formAlta.addEventListener('submit', function(event) {
    // Prevenir envío real del formulario
    event.preventDefault();
    
    // Mostrar mensaje de éxito
    mensajeExito.style.display = 'block';
    
    // Opcional: Resetear formulario después de 2 segundos
    setTimeout(function() {
        // Resetear todos los campos
        formAlta.reset();
        mensajeExito.style.display = 'none';
        btnEnviar.disabled = true;
        
        // Deshabilitar selects dependientes
        selectProvincia.disabled = true;
        selectLocalidad.disabled = true;
        selectProvincia.innerHTML = '<option value="">-- Primero seleccione región --</option>';
        selectLocalidad.innerHTML = '<option value="">-- Primero seleccione provincia --</option>';
        
        // Limpiar prefijo
        inputPrefijo.value = '';
        inputPrefijo.setAttribute('readonly', 'readonly');
        
        // Limpiar clases de validación
        document.querySelectorAll('.valido, .invalido').forEach(function(elemento) {
            elemento.classList.remove('valido', 'invalido');
        });
        
        // Resetear requisitos de contraseña
        document.querySelectorAll('.requisitos-password li').forEach(function(li) {
            li.textContent = li.textContent.replace('✓', '✗');
            li.classList.remove('cumplido');
        });
        
        // Devolver foco al primer campo
        inputNombre.focus();
    }, 2000);
});
```


### **Explicación del proceso**

- **`event.preventDefault()`**: Evita el envío real del formulario
- **`setTimeout`**: Espera 2 segundos antes de resetear
- **`form.reset()`**: Limpia todos los campos del formulario
- **Limpieza manual**: Selects dependientes y clases CSS requieren limpieza adicional
- **Foco inicial**: Devuelve el foco al primer campo tras resetear

***
<details>
<summary>PASO 9 (OPCIONAL)</summary>

## **Paso 9: Mejoras de accesibilidad con ARIA**

### **Objetivo**

Mejorar la accesibilidad del formulario para usuarios de lectores de pantalla y otras tecnologías de asistencia, cumpliendo con las pautas WCAG 2.1 nivel AA mediante atributos ARIA.

### **¿Por qué es importante?**

Los usuarios con discapacidad visual utilizan lectores de pantalla (NVDA, JAWS, VoiceOver) que necesitan información adicional para interpretar cambios dinámicos, estados de validación y relaciones entre elementos. Los atributos ARIA (Accessible Rich Internet Applications) proporcionan esta información semántica.

***

### **9.1. Anuncio automático de errores con role="alert" y aria-live**

**Problema**: Cuando aparece un mensaje de error, los usuarios de lectores de pantalla no son notificados automáticamente.

**Solución**: Usar `role="alert"` y `aria-live="assertive"` para anunciar errores inmediatamente.

#### **Modificación en index.html**

Actualizar los contenedores de error para incluir los atributos ARIA:

```html
<!-- Ejemplo para el campo email -->
<div class="campo">
    <label for="email">Correo electrónico *</label>
    <input type="email" id="email" name="email" required 
           aria-describedby="ayuda-email error-email"
           aria-invalid="false">
    <small id="ayuda-email">Formato: usuario@dominio.com</small>
    <span class="error" id="error-email" role="alert" aria-live="assertive"></span>
</div>

<!-- Ejemplo para el campo nombre -->
<div class="campo">
    <label for="nombre">Nombre completo *</label>
    <input type="text" id="nombre" name="nombre" required autofocus
           aria-describedby="ayuda-nombre"
           aria-required="true">
    <small id="ayuda-nombre">Ej: María García López</small>
    <span class="error" id="error-nombre" role="alert" aria-live="assertive"></span>
</div>

<!-- Ejemplo para contraseña -->
<div class="campo">
    <label for="password">Contraseña *</label>
    <input type="password" id="password" name="password" required
           aria-describedby="ayuda-password requisitos-password"
           aria-invalid="false"
           aria-required="true">
    <small id="ayuda-password">Debe cumplir los requisitos indicados abajo</small>
    <div class="requisitos-password" id="requisitos-password" aria-live="polite">
        <p>Requisitos de contraseña:</p>
        <ul>
            <li id="req-longitud" aria-label="Mínimo 8 caracteres: no cumplido">
                <span class="icono-estado" aria-hidden="true">✗</span> Mínimo 8 caracteres
            </li>
            <li id="req-mayuscula" aria-label="Al menos una mayúscula: no cumplido">
                <span class="icono-estado" aria-hidden="true">✗</span> Al menos una mayúscula
            </li>
            <li id="req-numero" aria-label="Al menos un número: no cumplido">
                <span class="icono-estado" aria-hidden="true">✗</span> Al menos un número
            </li>
            <li id="req-especial" aria-label="Al menos un carácter especial: no cumplido">
                <span class="icono-estado" aria-hidden="true">✗</span> Al menos un carácter especial (@#$%&*)
            </li>
        </ul>
    </div>
    <span class="error" id="error-password" role="alert" aria-live="assertive"></span>
</div>
```

**Explicación de atributos ARIA**:

- **`role="alert"`**: Indica que el elemento contiene un mensaje urgente que debe anunciarse inmediatamente
- **`aria-live="assertive"`**: Los cambios se anuncian interrumpiendo al lector (para errores críticos)
- **`aria-live="polite"`**: Los cambios se anuncian cuando el lector termina de hablar (para actualizaciones no urgentes)
- **`aria-describedby`**: Asocia el campo con sus textos de ayuda y mensajes de error
- **`aria-invalid="false"`**: Indica el estado de validez del campo (se cambiará dinámicamente)
- **`aria-required="true"`**: Marca explícitamente campos obligatorios para lectores de pantalla
- **`aria-label`**: Proporciona descripción textual del estado de cada requisito
- **`aria-hidden="true"`**: Oculta iconos decorativos (✗, ✓) para lectores de pantalla

***

### **9.2. Actualización del estado aria-invalid en JavaScript**

**Objetivo**: Cambiar dinámicamente `aria-invalid` según el estado de validación.

#### **Modificación en app.js - Validación de email**

```javascript
// ============================================
// VALIDACIÓN: EMAIL CON ARIA
// ============================================

inputEmail.addEventListener('input', function() {
    const email = this.value;
    const errorEmail = document.getElementById('error-email');
    
    const regexEmail = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    
    if (email === '') {
        errorEmail.textContent = '';
        this.classList.remove('valido', 'invalido');
        this.setAttribute('aria-invalid', 'false'); // NUEVO: actualizar ARIA
    } else if (!regexEmail.test(email)) {
        errorEmail.textContent = 'El email debe incluir @ y un dominio válido (ej: usuario@ejemplo.com)';
        this.classList.remove('valido');
        this.classList.add('invalido');
        this.setAttribute('aria-invalid', 'true'); // NUEVO: marcar como inválido
    } else {
        errorEmail.textContent = '';
        this.classList.remove('invalido');
        this.classList.add('valido');
        this.setAttribute('aria-invalid', 'false'); // NUEVO: marcar como válido
    }
    
    validarFormularioCompleto();
});
```


#### **Modificación en app.js - Validación de contraseña con aria-label**

```javascript
// ============================================
// VALIDACIÓN: CONTRASEÑA CON ARIA
// ============================================

inputPassword.addEventListener('input', function() {
    const password = this.value;
    
    const tieneLongitud = password.length >= 8;
    const tieneMayuscula = /[A-Z]/.test(password);
    const tieneNumero = /[0-9]/.test(password);
    const tieneEspecial = /[@#$%&*]/.test(password);
    
    // Actualizar cada requisito con accesibilidad
    actualizarRequisitoConARIA('req-longitud', tieneLongitud, 'Mínimo 8 caracteres');
    actualizarRequisitoConARIA('req-mayuscula', tieneMayuscula, 'Al menos una mayúscula');
    actualizarRequisitoConARIA('req-numero', tieneNumero, 'Al menos un número');
    actualizarRequisitoConARIA('req-especial', tieneEspecial, 'Al menos un carácter especial');
    
    const passwordValido = tieneLongitud && tieneMayuscula && tieneNumero && tieneEspecial;
    
    if (password === '') {
        this.classList.remove('valido', 'invalido');
        this.setAttribute('aria-invalid', 'false'); // NUEVO
    } else if (passwordValido) {
        this.classList.remove('invalido');
        this.classList.add('valido');
        this.setAttribute('aria-invalid', 'false'); // NUEVO
    } else {
        this.classList.remove('valido');
        this.classList.add('invalido');
        this.setAttribute('aria-invalid', 'true'); // NUEVO
    }
    
    validarFormularioCompleto();
});

// ============================================
// FUNCIÓN AUXILIAR: ACTUALIZAR REQUISITO CON ARIA
// ============================================

function actualizarRequisitoConARIA(idRequisito, cumplido, textoRequisito) {
    const elemento = document.getElementById(idRequisito);
    const icono = elemento.querySelector('.icono-estado');
    
    if (cumplido) {
        // Requisito cumplido
        icono.textContent = '✓';
        elemento.classList.add('cumplido');
        // NUEVO: actualizar aria-label para lectores de pantalla
        elemento.setAttribute('aria-label', textoRequisito + ': cumplido');
    } else {
        // Requisito pendiente
        icono.textContent = '✗';
        elemento.classList.remove('cumplido');
        // NUEVO: actualizar aria-label para lectores de pantalla
        elemento.setAttribute('aria-label', textoRequisito + ': no cumplido');
    }
}
```


***

### **9.3. Iconos además de colores en validación**

**Problema**: Los usuarios con daltonismo o baja visión pueden no distinguir verde/rojo.

**Solución**: Añadir iconos además de colores para indicar estados de validación.

#### **Modificación en styles.css**

```css
/* Iconos de validación - accesibles sin depender solo del color */
input.valido {
    border-color: #27ae60;
    background-color: #f0fff4;
    background-image: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 20 20"><path fill="%2327ae60" d="M8 14.5l-4.5-4.5L2 11.5l6 6 12-12L18.5 4z"/></svg>');
    background-repeat: no-repeat;
    background-position: right 10px center;
    padding-right: 40px;
}

input.invalido {
    border-color: #e74c3c;
    background-color: #fff5f5;
    background-image: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 20 20"><path fill="%23e74c3c" d="M10 0C4.5 0 0 4.5 0 10s4.5 10 10 10 10-4.5 10-10S15.5 0 10 0zm5 13.5L13.5 15 10 11.5 6.5 15 5 13.5 8.5 10 5 6.5 6.5 5 10 8.5 13.5 5 15 6.5 11.5 10 15 13.5z"/></svg>');
    background-repeat: no-repeat;
    background-position: right 10px center;
    padding-right: 40px;
}

/* Asegurar que iconos de requisitos de contraseña sean visibles */
.requisitos-password .icono-estado {
    display: inline-block;
    width: 20px;
    font-weight: bold;
    margin-right: 5px;
}
```

**Explicación**:

- Se añaden iconos SVG inline (✓ para válido, ✗ para inválido) además del color
- Los iconos están integrados en el CSS para no requerir archivos externos
- `aria-hidden="true"` en los iconos evita que se anuncien redundantemente

***

### **9.4. Actualización completa del HTML con mejoras de accesibilidad**

Para que las modificaciones queden claras, aquí están los campos actualizados completos:

```html
<!-- REGIÓN con ARIA -->
<div class="campo">
    <label for="region">Región *</label>
    <select id="region" name="region" required aria-required="true">
        <option value="">-- Seleccione una región --</option>
        <option value="andalucia">Andalucía</option>
        <option value="cataluna">Cataluña</option>
        <option value="madrid">Madrid</option>
        <option value="valencia">Comunidad Valenciana</option>
    </select>
    <span class="error" id="error-region" role="alert" aria-live="assertive"></span>
</div>

<!-- PROVINCIA con ARIA -->
<div class="campo">
    <label for="provincia">Provincia *</label>
    <select id="provincia" name="provincia" required disabled 
            aria-required="true" aria-disabled="true">
        <option value="">-- Primero seleccione región --</option>
    </select>
    <span class="error" id="error-provincia" role="alert" aria-live="assertive"></span>
</div>

<!-- LOCALIDAD con ARIA -->
<div class="campo">
    <label for="localidad">Localidad *</label>
    <select id="localidad" name="localidad" required disabled
            aria-required="true" aria-disabled="true">
        <option value="">-- Primero seleccione provincia --</option>
    </select>
    <span class="error" id="error-localidad" role="alert" aria-live="assertive"></span>
</div>

<!-- DIRECCIÓN con ARIA -->
<div class="campo">
    <label for="direccion">Dirección postal *</label>
    <input type="text" id="direccion" name="direccion" required
           aria-describedby="ayuda-direccion"
           aria-required="true">
    <small id="ayuda-direccion">Calle, número, piso, puerta</small>
    <span class="error" id="error-direccion" role="alert" aria-live="assertive"></span>
</div>

<!-- PREFIJO con ARIA -->
<div class="campo">
    <label for="prefijo">Prefijo telefónico</label>
    <input type="text" id="prefijo" name="prefijo" readonly
           aria-describedby="ayuda-prefijo"
           aria-readonly="true">
    <small id="ayuda-prefijo">Se autocompleta según la región</small>
</div>
```


***

### **9.5. Actualización de selects dependientes con aria-disabled**

Modificar el JavaScript para gestionar `aria-disabled` dinámicamente:

```javascript
// ============================================
// EVENTO: CAMBIO DE REGIÓN CON ARIA
// ============================================

selectRegion.addEventListener('change', function() {
    const regionSeleccionada = this.value;
    
    selectProvincia.innerHTML = '<option value="">-- Primero seleccione región --</option>';
    selectProvincia.disabled = true;
    selectProvincia.setAttribute('aria-disabled', 'true'); // NUEVO
    
    selectLocalidad.innerHTML = '<option value="">-- Primero seleccione provincia --</option>';
    selectLocalidad.disabled = true;
    selectLocalidad.setAttribute('aria-disabled', 'true'); // NUEVO
    
    if (regionSeleccionada) {
        selectProvincia.disabled = false;
        selectProvincia.setAttribute('aria-disabled', 'false'); // NUEVO
        selectProvincia.innerHTML = '<option value="">-- Seleccione una provincia --</option>';
        
        datosProvincia[regionSeleccionada].forEach(function(prov) {
            const option = document.createElement('option');
            option.value = prov.value;
            option.textContent = prov.text;
            selectProvincia.appendChild(option);
        });
        
        inputPrefijo.value = prefijosRegion[regionSeleccionada];
        inputPrefijo.removeAttribute('readonly');
        inputPrefijo.setAttribute('aria-readonly', 'false'); // NUEVO
    } else {
        inputPrefijo.value = '';
        inputPrefijo.setAttribute('readonly', 'readonly');
        inputPrefijo.setAttribute('aria-readonly', 'true'); // NUEVO
    }
    
    validarFormularioCompleto();
});

// ============================================
// EVENTO: CAMBIO DE PROVINCIA CON ARIA
// ============================================

selectProvincia.addEventListener('change', function() {
    const provinciaSeleccionada = this.value;
    
    selectLocalidad.innerHTML = '<option value="">-- Primero seleccione provincia --</option>';
    selectLocalidad.disabled = true;
    selectLocalidad.setAttribute('aria-disabled', 'true'); // NUEVO
    
    if (provinciaSeleccionada) {
        selectLocalidad.disabled = false;
        selectLocalidad.setAttribute('aria-disabled', 'false'); // NUEVO
        selectLocalidad.innerHTML = '<option value="">-- Seleccione una localidad --</option>';
        
        datosLocalidad[provinciaSeleccionada].forEach(function(loc) {
            const option = document.createElement('option');
            option.value = loc.toLowerCase().replace(/ /g, '-');
            option.textContent = loc;
            selectLocalidad.appendChild(option);
        });
    }
    
    validarFormularioCompleto();
});
```


***

### **9.6. Mejora del botón de envío con estados ARIA**

```html
<button type="submit" id="btnEnviar" disabled 
        aria-disabled="true" 
        aria-describedby="estado-boton">
    Crear cuenta
</button>
<span id="estado-boton" class="sr-only"></span>
<div id="mensaje-exito" class="exito" role="status" aria-live="polite" style="display:none;">
    ✓ Cuenta creada correctamente
</div>
```

**Añadir clase auxiliar en CSS para lectores de pantalla:**

```css
/* Clase para contenido solo visible para lectores de pantalla */
.sr-only {
    position: absolute;
    width: 1px;
    height: 1px;
    padding: 0;
    margin: -1px;
    overflow: hidden;
    clip: rect(0, 0, 0, 0);
    white-space: nowrap;
    border-width: 0;
}
```

**Actualizar JavaScript del botón:**

```javascript
function validarFormularioCompleto() {
    const nombreValido = inputNombre.value.trim() !== '';
    const emailValido = inputEmail.classList.contains('valido');
    const passwordValido = inputPassword.classList.contains('valido');
    const regionValida = selectRegion.value !== '';
    const provinciaValida = selectProvincia.value !== '';
    const localidadValida = selectLocalidad.value !== '';
    const direccionValida = inputDireccion.value.trim() !== '';
    
    const formularioValido = nombreValido && emailValido && passwordValido && 
                             regionValida && provinciaValida && localidadValida && 
                             direccionValida;
    
    btnEnviar.disabled = !formularioValido;
    btnEnviar.setAttribute('aria-disabled', formularioValido ? 'false' : 'true'); // NUEVO
    
    // NUEVO: Actualizar mensaje de estado para lectores de pantalla
    const estadoBoton = document.getElementById('estado-boton');
    if (formularioValido) {
        estadoBoton.textContent = 'Formulario completo. Puede enviar.';
    } else {
        estadoBoton.textContent = 'Formulario incompleto. Complete todos los campos obligatorios.';
    }
}
```


***

### **Resumen de mejoras de accesibilidad implementadas**

| **Atributo ARIA** | **Función** | **Beneficio** |
| :-- | :-- | :-- |
| `role="alert"` | Anuncia mensajes de error inmediatamente | Usuarios de lectores de pantalla reciben notificación instantánea de errores |
| `aria-live="assertive"` | Interrumpe anuncios actuales para mensajes urgentes | Errores críticos se comunican sin demora |
| `aria-live="polite"` | Anuncia cambios cuando el lector termina de hablar | Actualizaciones no urgentes no interrumpen la navegación |
| `aria-describedby` | Asocia campos con ayudas contextuales | Lectores anuncian automáticamente las ayudas al enfocar el campo |
| `aria-invalid` | Indica estado de validación del campo | Anuncia si el campo contiene un error |
| `aria-required` | Marca campos obligatorios explícitamente | Lectores anuncian "requerido" al enfocar el campo |
| `aria-label` | Proporciona descripción textual de elementos | Requisitos de contraseña se anuncian con su estado actual |
| `aria-disabled` | Indica que un elemento está deshabilitado | Lectores anuncian que un campo no está disponible actualmente |
| `aria-readonly` | Indica que un campo es de solo lectura | Usuarios saben que no pueden modificar el prefijo inicialmente |
| `aria-hidden="true"` | Oculta elementos decorativos | Iconos (✗, ✓) no se anuncian redundantemente |
| `role="status"` | Indica mensajes de estado no urgentes | Mensaje de éxito se anuncia de forma cortés |


***

### **Actualización del Checklist ISO 9241-17 con criterios de accesibilidad**

Añadir una nueva sección al checklist de evaluación cruzada:

***

### **10. Accesibilidad (WCAG 2.1 / ARIA)**

| **Criterio** | **Cumple** | **No cumple** | **Observaciones** |
| :-- | :-- | :-- | :-- |
| Los mensajes de error tienen `role="alert"` | ☐ | ☐ |  |
| Los campos usan `aria-describedby` para ayudas | ☐ | ☐ |  |
| Los campos obligatorios tienen `aria-required="true"` | ☐ | ☐ |  |
| Los campos actualizan `aria-invalid` dinámicamente | ☐ | ☐ |  |
| Los requisitos de contraseña tienen `aria-label` | ☐ | ☐ |  |
| Los iconos decorativos tienen `aria-hidden="true"` | ☐ | ☐ |  |
| Los selects deshabilitados usan `aria-disabled` | ☐ | ☐ |  |
| Se usan iconos además de colores para validación | ☐ | ☐ |  |
| El mensaje de éxito tiene `role="status"` | ☐ | ☐ |  |


</details>
## **Verificación del formulario implementado**

### **Comprobaciones básicas**

Tras completar la implementación, verificar:

1. **Carga correcta**: Abrir `index.html` en un navegador
2. **Foco inicial**: El cursor debe estar en el campo "Nombre completo"
3. **Tabulación**: Al presionar Tab, el foco avanza lógicamente
4. **Botón deshabilitado**: El botón "Crear cuenta" debe estar deshabilitado (gris)

### **Pruebas funcionales**

**Prueba A: Dependencias en cascada**

1. Seleccionar región: Andalucía
2. Verificar que provincia se habilita y muestra opciones
3. Seleccionar provincia: Sevilla
4. Verificar que localidad se habilita
5. Cambiar región a Cataluña
6. Verificar que provincia y localidad se limpian y deshabilitan

**Prueba B: Validación de email**

1. Escribir: "usuario" (sin @)
2. Verificar mensaje: "El email debe incluir @ y un dominio válido"
3. Escribir: "usuario@ejemplo.com"
4. Verificar que mensaje desaparece y campo tiene borde verde

**Prueba C: Validación de contraseña**

1. Escribir: "abc"
2. Verificar que todos los requisitos muestran ✗ en rojo
3. Escribir: "Abc123\#\$"
4. Verificar que todos los requisitos muestran ✓ en verde

**Prueba D: Autocompletado de prefijo**

1. Seleccionar región: Madrid
2. Verificar que prefijo muestra: "+34 91"
3. Cambiar región a Valencia
4. Verificar que prefijo cambia a: "+34 96"

***

## **Casos de prueba para evaluación cruzada**

Una vez finalizado el formulario, cada miembro de la pareja evaluará el formulario de su compañero utilizando estos casos:

### **Caso A: Alta exitosa completa**

**Objetivo**: Verificar que el formulario permite una alta completa sin errores.

**Datos a introducir**:

- Nombre: Juan Pérez García
- Email: juan.perez@ejemplo.com
- Contraseña: Segura123\#
- Región: Andalucía
- Provincia: Sevilla
- Localidad: Dos Hermanas
- Dirección: Calle Mayor 15, 2ºA

**Resultado esperado**:

- Todos los campos aceptan los datos sin mensajes de error
- El botón "Crear cuenta" se habilita (verde)
- Al hacer clic en el botón, aparece mensaje de éxito
- El formulario se resetea tras 2 segundos

**Registrar**:

- ¿Se completó sin errores? (Sí/No)
- Tiempo aproximado (en segundos)
- Observaciones

***

### **Caso B: Corrección de error de email**

**Objetivo**: Verificar que el formulario valida email y preserva datos al corregir.

**Pasos**:

1. Completar nombre: María López
2. Introducir email inválido: "mariasindominio"
3. Verificar que aparece mensaje de error específico
4. Completar contraseña: Fuerte456\#
5. Seleccionar Cataluña → Barcelona → Barcelona capital
6. Completar dirección: Rambla 20
7. Corregir email a: maria.lopez@ejemplo.com
8. Verificar que mensaje desaparece y otros campos mantienen sus datos

**Resultado esperado**:

- Mensaje de error específico para email (no genérico)
- Al corregir email, el mensaje desaparece
- Resto de campos conservan sus valores
- Botón se habilita tras corrección

**Registrar**:

- ¿El mensaje de error es específico y constructivo? (Sí/No)
- ¿Se preservaron los datos del resto de campos? (Sí/No)
- Tiempo aproximado (en segundos)
- Observaciones

***

### **Caso C: Cambio de región tras selección completa**

**Objetivo**: Verificar que cambiar región limpia dependencias correctamente.

**Pasos**:

1. Seleccionar: Andalucía → Sevilla → Dos Hermanas
2. Verificar que prefijo muestra: "+34 95"
3. Cambiar región a: Madrid
4. Verificar que provincia se limpia y deshabilita
5. Verificar que localidad se limpia y deshabilita
6. Verificar que prefijo cambia a: "+34 91"
7. Verificar que botón "Crear cuenta" se deshabilita

**Resultado esperado**:

- Provincia y localidad se limpian automáticamente
- Provincia y localidad se deshabilitan
- Prefijo se actualiza según nueva región
- Botón de envío se deshabilita por falta de datos

**Registrar**:

- ¿Se limpiaron correctamente las dependencias? (Sí/No)
- ¿El prefijo se actualizó correctamente? (Sí/No)
- ¿El botón se deshabilitó correctamente? (Sí/No)
- Observaciones

***

## **Checklist ISO 9241-17 para evaluación cruzada**

Cada miembro de la pareja completará este checklist evaluando el formulario de su compañero. Marcar: **Cumple** / **No cumple** / **Observaciones**

### **1. Estructura y relación campo-etiqueta**

| **Criterio** | **Cumple** | **No cumple** | **Observaciones** |
| :-- | :-- | :-- | :-- |
| Cada campo tiene una etiqueta asociada visible | ☐ | ☐ |  |
| Las etiquetas son claras y específicas | ☐ | ☐ |  |
| Los campos están agrupados lógicamente con fieldset | ☐ | ☐ |  |
| El orden de los campos sigue un flujo lógico | ☐ | ☐ |  |


***

### **2. Formatos y restricciones visibles**

| **Criterio** | **Cumple** | **No cumple** | **Observaciones** |
| :-- | :-- | :-- | :-- |
| Los formatos requeridos son visibles antes de escribir | ☐ | ☐ |  |
| El campo email muestra ejemplo del formato | ☐ | ☐ |  |
| Los requisitos de contraseña son visibles antes de escribir | ☐ | ☐ |  |
| Los tipos de input son apropiados (email, password, text) | ☐ | ☐ |  |


***

### **3. Selects dependientes (entradas de elección)**

| **Criterio** | **Cumple** | **No cumple** | **Observaciones** |
| :-- | :-- | :-- | :-- |
| La dependencia región → provincia → localidad funciona | ☐ | ☐ |  |
| Al cambiar región, se limpian provincia y localidad | ☐ | ☐ |  |
| Los selects dependientes se deshabilitan apropiadamente | ☐ | ☐ |  |
| Las opciones de los selects son claras | ☐ | ☐ |  |


***

### **4. Autocompletado**

| **Criterio** | **Cumple** | **No cumple** | **Observaciones** |
| :-- | :-- | :-- | :-- |
| El prefijo se autocompleta al seleccionar región | ☐ | ☐ |  |
| El prefijo autocompletado permite ajuste manual | ☐ | ☐ |  |


***

### **5. Validaciones en tiempo real**

| **Criterio** | **Cumple** | **No cumple** | **Observaciones** |
| :-- | :-- | :-- | :-- |
| La validación de email se ejecuta mientras se escribe | ☐ | ☐ |  |
| La validación de contraseña se ejecuta mientras se escribe | ☐ | ☐ |  |
| Los requisitos de contraseña se actualizan visualmente | ☐ | ☐ |  |
| El botón está deshabilitado hasta cumplir requisitos | ☐ | ☐ |  |


***

### **6. Mensajes y feedback**

| **Criterio** | **Cumple** | **No cumple** | **Observaciones** |
| :-- | :-- | :-- | :-- |
| Los mensajes de error son específicos (no genéricos) | ☐ | ☐ |  |
| Los mensajes indican qué pasó y cómo resolver | ☐ | ☐ |  |
| El lenguaje de los mensajes es positivo | ☐ | ☐ |  |
| Hay retroalimentación visual de éxito tras envío | ☐ | ☐ |  |


***

### **7. Navegación y accesibilidad**

| **Criterio** | **Cumple** | **No cumple** | **Observaciones** |
| :-- | :-- | :-- | :-- |
| El primer campo tiene foco automático | ☐ | ☐ |  |
| El orden de tabulación es lógico | ☐ | ☐ |  |
| Los estados de foco son visibles (borde destacado) | ☐ | ☐ |  |
| No hay saltos inesperados de foco | ☐ | ☐ |  |


***

### **8. Preservación de datos**

| **Criterio** | **Cumple** | **No cumple** | **Observaciones** |
| :-- | :-- | :-- | :-- |
| Al corregir un error, se mantienen datos válidos | ☐ | ☐ |  |
| El formulario no se borra por error en un solo campo | ☐ | ☐ |  |


***

### **9. Prevención de errores**

| **Criterio** | **Cumple** | **No cumple** | **Observaciones** |
| :-- | :-- | :-- | :-- |
| El botón se deshabilita hasta cumplir requisitos mínimos | ☐ | ☐ |  |
| Los campos dependientes se deshabilitan apropiadamente | ☐ | ☐ |  |
| Las validaciones previenen envío de datos inválidos | ☐ | ☐ |  |


***

## **Presentación al profesor**

Al finalizar la práctica, cada pareja debe:

### **1. Mostrar formulario funcionando**

- Ejecutar `index.html` en navegador
- Demostrar funcionamiento de los 3 casos de prueba
- Explicar las decisiones de implementación


### **2. Presentar checklists completados**

- Cada miembro presenta el checklist que completó evaluando a su compañero

### **3. Comentar resultados con el profesor**

- **Dificultades encontradas**: ¿Qué aspecto fue más complejo de implementar?
- **Decisiones de diseño**: ¿Por qué se eligieron ciertos estilos o validaciones?
- **Evaluación cruzada**: ¿Qué diferencias se encontraron entre ambos formularios?
- **Aprendizajes**: ¿Qué principio ISO 9241-17 resultó más relevante?


## **Recursos de apoyo**

- **MDN Web Docs**: https://developer.mozilla.org/es/docs/Learn/Forms
- **ISO 9241-17**: Resumen de principios para diálogos por cumplimentación de formularios
- **WCAG 2.1**: Pautas de accesibilidad para formularios web