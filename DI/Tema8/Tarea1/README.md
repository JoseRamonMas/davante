# Tema 8. Confección de informes I

**Tarea 1: "Sistema de informes con JasperReports".**

## 1. Introducción

En esta práctica vas a diseñar, programar y documentar un sistema de generación de informes utilizando **Jaspersoft Studio Community** y la biblioteca **JasperReports**.
Su finalidad es poner en práctica los contenidos vistos en el **Tema 8 – Confección de informes I**, especialmente:

* Instalación y configuración de **Jaspersoft Studio Community**.
* Creación de **conexiones a orígenes de datos**.
* Diseño de **plantillas de informes** (reportes).
* Estructura de un informe: **bandas, campos y variables**.
* Generación de **informes en diferentes formatos** de salida (PDF, HTML, XLS, CSV).
* Inclusión de **valores calculados, recuentos y totales**.
* **Documentación técnica y funcional** del proceso de diseño.

El objetivo final es que domines el proceso completo de creación de informes profesionales desde cero, comprendiendo la estructura de un informe, el uso de variables y la exportación a diferentes formatos según las necesidades del usuario final.

Además de desarrollar los informes, tendrás que **documentar tu proceso**, describiendo con tus propias palabras cómo has trabajado, los problemas encontrados y las decisiones que has tomado.
No se evaluará solo el resultado final, sino también el proceso de desarrollo y tu capacidad para explicarlo.

## 2. Objetivos de aprendizaje

Al finalizar esta práctica, deberás ser capaz de:

* Instalar y configurar Jaspersoft Studio Community.
* Crear y configurar adaptadores de datos (conexiones a bases de datos).
* Diseñar informes utilizando las bandas y elementos visuales de JasperReports.
* Trabajar con campos, variables predefinidas y variables de usuario.
* Implementar operaciones de cálculo (sumas, recuentos, promedios).
* Exportar informes a múltiples formatos según el contexto de uso.
* Documentar correctamente el proceso de diseño de informes.


## 3. Descripción general del proyecto

El **Sistema de informes con JasperReports** consiste en crear una serie de informes profesionales a partir de una base de datos de ejemplo.
Los informes mostrarán información sobre productos, clientes o ventas (según la base de datos que utilices), con diferentes niveles de complejidad: desde listados simples hasta informes con cálculos agregados y formatos personalizados.

El usuario final podrá visualizar estos informes en diferentes formatos (PDF, HTML, Excel) según el propósito: impresión, visualización web o análisis de datos.

## 4. Estructura de la práctica

La práctica se divide en **cinco ejercicios secuenciales**, más una **fase final de documentación**.
Sigue el orden indicado y guarda capturas de pantalla de cada paso.

***

### EJERCICIO 1. Instalación y configuración del entorno

1. Descarga e instala **Jaspersoft Studio Community** desde la página oficial:
[https://community.jaspersoft.com/project/jaspersoft-studio](https://community.jaspersoft.com/project/jaspersoft-studio)
2. Una vez instalado, abre la aplicación y familiarízate con la interfaz:
    * **Project Explorer**: muestra los proyectos y archivos `.jrxml`.
    * **Repository Explorer**: permite gestionar conexiones a orígenes de datos.
    * **Zona de diseño**: lienzo donde diseñarás visualmente el informe.
    * **Paleta de elementos**: componentes que puedes arrastrar al diseño (etiquetas, campos, imágenes, etc.).
    * **Panel de propiedades**: configuración de cada elemento seleccionado.
3. Crea un **nuevo proyecto JasperReports**:
    * `File` → `New` → `Project...` → `JasperReports Project`.
    * Asigna un nombre descriptivo al proyecto (por ejemplo, `InformesTema8_TuApellido`).

**En el documento a entregar:**

* Describe con tus palabras el proceso de instalación de Jaspersoft Studio.
* Explica brevemente qué diferencias existen entre **Jaspersoft Studio**, **iReport** y **JasperReports** (consulta los apuntes del tema).
* Incluye una captura de pantalla de la interfaz de Jaspersoft Studio con tu proyecto creado.

***

### EJERCICIO 2. Configuración del origen de datos

Para que un informe pueda mostrar información, necesita conectarse a una **fuente de datos**. En este ejercicio configurarás un adaptador de datos.

1. **Opción A:** Utiliza la base de datos de ejemplo **Sample DB** que viene incluida en Jaspersoft Studio:
    * En el menú superior, ve a `Window` → `Show View` → `Repository Explorer`.
    * Expande `Data Adapters` y verifica que existe **Sample DB**.
    * Haz clic derecho sobre **Sample DB** → `Test` para comprobar que funciona correctamente.
2. **Opción B (más real):** Trabaja con tu propia base de datos (MySQL, PostgreSQL, etc.):
    * Crea una base de datos sencilla con al menos una tabla (por ejemplo, una tabla `productos` con campos como `id`, `nombre`, `categoria`, `precio`, `stock`).
    * En Jaspersoft Studio, crea un nuevo adaptador de datos:
        * `Repository Explorer` → clic derecho en `Data Adapters` → `Create Data Adapter`.
        * Selecciona el tipo de base de datos y completa los datos de conexión (URL, usuario, contraseña).
        * Incluye la librería correspondiente (.jar)
        * Prueba la conexión con el botón `Test Connection`.

**En el documento a entregar:**

* Explica qué es un **origen de datos** y por qué es necesario en la creación de informes.
* Incluye una captura de pantalla mostrando la conexión exitosa al origen de datos.

***

### EJERCICIO 3. Creación del primer informe básico

Ahora crearás tu primer informe que muestre un listado simple de datos.

1. Crea un nuevo informe:
    * `File` → `New` → `Jasper Report`.
    * Selecciona una plantilla (**template**). Para este ejercicio, elige `Blank A4`.
    * Asigna un nombre al informe (por ejemplo, `Listado_Productos.jrxml`).
    * En el asistente, selecciona el **Data Adapter** configurado en el ejercicio anterior.
2. Define la **consulta SQL** o dataset:
    * Si usas Sample DB, puedes utilizar: `SELECT * FROM PRODUCT` (consulta la estructura de las tablas disponibles).
    * Si usas tu propia base de datos, escribe una consulta apropiada (por ejemplo, `SELECT id, nombre, categoria, precio FROM productos`).
3. **Diseña el informe**:
    * Observa las **bandas** del informe: `Title`, `Page Header`, `Column Header`, `Detail`, `Column Footer`, `Page Footer`, `Summary`.
    * Añade un **título** en la banda `Title`:
        * Arrastra un elemento `Static Text` desde la paleta y escribe "Listado de Productos".
        * Cambia el formato del texto (tamaño, negrita, color) desde el panel de propiedades.
    * Añade los **campos** en las bandas correspondientes:
        * En `Column Header`, añade etiquetas (`Static Text`) para los nombres de las columnas: "ID", "Nombre", "Categoría", "Precio".
        * En `Detail`, arrastra los **campos** (`Text Field`) correspondientes desde la ventana `Outline` → `Fields`.
4. **Previsualiza el informe**:
    * Haz clic en la pestaña `Preview` para ver cómo se genera el informe.
    * Verifica que los datos se muestren correctamente.

*Ejemplo visual esperado: Un informe con encabezado, columnas y filas de datos.*

**En el documento a entregar:**

* Explica con tus palabras qué función cumple cada **banda** del informe.
* Inserta una captura de la **zona de diseño** mostrando la estructura del informe.
* Inserta una captura de la **vista previa** del informe generado.

***

### EJERCICIO 4. Inclusión de variables y valores calculados

Amplía el informe anterior para incluir **operaciones de cálculo** sobre los datos.

1. **Añade un recuento de registros**:
    * En la banda `Summary`, añade un elemento `Text Field`.
    * Configura su expresión para mostrar el número total de productos utilizando la variable predefinida `$V{REPORT_COUNT}`.
    * Añade una etiqueta (`Static Text`) que diga "Total de productos:", seguida del campo con el recuento.
2. **Calcula el total de precios (suma)**:
    * Crea una **nueva variable** que sume todos los precios:
        * En la ventana `Outline`, haz clic derecho en `Variables` → `Create Variable`.
        * Asigna un nombre (por ejemplo, `TotalPrecios`).
        * Configura:
            * **Variable Class**: `java.lang.Double` o `java.math.BigDecimal`.
            * **Calculation**: `Sum`.
            * **Variable Expression**: selecciona el campo numérico correspondiente (por ejemplo, `$F{PRODUCT_PRICE}` o el campo de precio de tu tabla).
    * En la banda `Summary`, añade un campo de texto que muestre esta variable: `$V{TotalPrecios}`.
    * Añade una etiqueta descriptiva: "Precio total:".
    * Investiga como puedes mostrar los precios con solo dos decimales
3. **Añade numeración de líneas**:
    * En la banda `Detail`, añade un campo de texto que muestre el número de línea utilizando la variable predefinida `$V{REPORT_COUNT}`.
4. **Añade el número de página**:
    * En la banda `Page Footer`, inserta un elemento `Page X of Y` desde la paleta (en `Composite Elements`).
    * Personaliza el formato si lo deseas (por ejemplo, "Página 1 de 2").

**En el documento a entregar:**

* Explica cómo creaste la variable para calcular el total de precios.
* Inserta capturas del diseño y de la vista previa mostrando los valores calculados.

***

### EJERCICIO 5. Exportación a diferentes formatos

Los informes deben poder exportarse en función del contexto de uso. En este ejercicio exportarás el informe a varios formatos.

1. Desde la pestaña `Preview`, utiliza el menú de exportación (icono del disquete con flecha) para guardar el informe en los siguientes formatos:
    * **PDF**: para impresión o envío por correo electrónico.
    * **HTML**: para visualización en navegador web.
    * **XLS** o **XLSX**: para análisis de datos en Excel.
    * **CSV**: para importación en otras aplicaciones o bases de datos.


**En el documento a entregar:**

* Incluye capturas de pantalla de los archivos generados en al menos dos formatos diferentes (por ejemplo, PDF y HTML abiertos).

***

### FASE FINAL: Documentación del proyecto

Redacta un pequeño informe con la documentación que has ido elaborando y una reflexión final.


***

## 5. Entrega

* Documento en formato **PDF** con tu informe técnico y capturas.
