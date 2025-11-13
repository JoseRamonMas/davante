# Tema 8. Confección de informes I

**Tarea 2: "Informe de gestión de biblioteca municipal".**

## 1. Introducción

En esta tarea vas a diseñar y documentar un **sistema completo de informes** para una biblioteca municipal utilizando **Jaspersoft Studio Community** y la biblioteca **JasperReports**.  El objetivo final es que domines el proceso completo de creación de informes.

***

## 2. Objetivos de aprendizaje

Al finalizar esta tarea, deberás ser capaz de:

* Diseñar e implementar una **base de datos relacional** completa con tablas relacionadas.
* Poblar tablas con **datos realistas** de prueba.
* Crear **consultas SQL con JOIN** para combinar información de múltiples tablas.
* Diseñar informes utilizando todas las **bandas** disponibles en JasperReports.
* Crear y configurar **variables calculadas** (Sum, Count, Average, etc.).
* Aplicar **funciones de manipulación de texto** en expresiones de campo.
* **Formatear números, fechas y textos** de forma profesional.
* Organizar visualmente los elementos del informe con atención al **diseño y la estética**.
* Exportar informes a **diferentes formatos** según el contexto de uso.

***

## 3. Descripción general del proyecto

El **Sistema de informes de biblioteca municipal** consiste en crear un informe completo que muestre información sobre el préstamo de libros en una biblioteca pública.

El informe incluirá:

* **Listado detallado de préstamos**: mostrará todos los préstamos realizados con información del libro, socio y fechas.
* **Información formateada**: aplicarás funciones de texto para presentar nombres en mayúsculas, títulos de libros acortados, fechas formateadas, etc.
* **Cálculos agregados**: totales de préstamos, recuentos por categoría, duración media de préstamos, etc.
* **Diseño visual cuidado**: distribución ordenada de elementos, uso de colores, bordes, alineaciones y espaciados.
* **Exportación múltiple**: generación del informe en PDF, HTML y Excel.

Este proyecto te permitirá trabajar con datos realistas en un contexto familiar (biblioteca) y aplicar todos los conceptos básicos vistos en el tema de forma exhaustiva.

***

## 4. Estructura de la tarea

La tarea se divide en **cinco ejercicios secuenciales**, más una **fase final de documentación**.
Sigue el orden indicado y guarda capturas de pantalla detalladas de cada paso.

***

### EJERCICIO 1. Diseño e implementación de la base de datos

En esta tarea trabajarás con una **base de datos propia** que deberás crear y poblar con datos realistas. Esto te permitirá comprender el flujo completo desde la fuente de datos hasta el informe final.

#### 1.1. Creación de la base de datos

Crea una base de datos llamada `biblioteca_municipal` con las siguientes tablas:

**Tabla `socios`:**


| Campo | Tipo | Descripción |
| :-- | :-- | :-- |
| id_socio | INT (PK, AUTO_INCREMENT) | Identificador único del socio |
| nombre | VARCHAR(50) | Nombre del socio |
| apellidos | VARCHAR(100) | Apellidos del socio |
| email | VARCHAR(100) | Correo electrónico |
| telefono | VARCHAR(15) | Teléfono de contacto |
| fecha_alta | DATE | Fecha de registro en la biblioteca |

**Tabla `libros`:**


| Campo | Tipo | Descripción |
| :-- | :-- | :-- |
| id_libro | INT (PK, AUTO_INCREMENT) | Identificador único del libro |
| titulo | VARCHAR(200) | Título completo del libro |
| autor | VARCHAR(100) | Autor del libro |
| editorial | VARCHAR(100) | Editorial |
| categoria | VARCHAR(50) | Categoría (Novela, Ensayo, Técnico, Infantil, Historia) |
| isbn | VARCHAR(20) | Código ISBN |
| año_publicacion | INT | Año de publicación |

**Tabla `prestamos`:**


| Campo | Tipo | Descripción |
| :-- | :-- | :-- |
| id_prestamo | INT (PK, AUTO_INCREMENT) | Identificador único del préstamo |
| id_socio | INT (FK → socios.id_socio) | Socio que realiza el préstamo |
| id_libro | INT (FK → libros.id_libro) | Libro prestado |
| fecha_prestamo | DATE | Fecha de inicio del préstamo |
| fecha_devolucion | DATE | Fecha de devolución efectiva (puede ser NULL si aún no se ha devuelto) |
| dias_prestamo | INT | Días de duración del préstamo |

#### 1.2. Inserción de datos de prueba realistas

Debes insertar **datos suficientes** para que el informe sea significativo:

* **Al menos 15-20 socios** con nombres y apellidos variados, emails realistas (por ejemplo: `juan.garcia@email.com`), teléfonos con formato correcto y fechas de alta distribuidas en diferentes meses y años.
* **Al menos 25-30 libros** distribuidos entre las diferentes categorías:
    * Novela: "Cien años de soledad", "Don Quijote de la Mancha", "1984", etc.
    * Ensayo: "Sapiens", "El mundo y sus demonios", etc.
    * Técnico: "Fundamentos de bases de datos", "Introducción a Java", etc.
    * Infantil: "El principito", "Harry Potter y la piedra filosofal", etc.
    * Historia: "Roma: auge y caída de un imperio", etc.
* **Al menos 30-40 registros de préstamos** con:
    * Fechas de préstamo variadas (últimos 6-12 meses).
    * Algunos préstamos ya devueltos (con `fecha_devolucion` completada).
    * Algunos préstamos aún activos (con `fecha_devolucion` NULL).
    * Valores realistas en `dias_prestamo` (entre 7 y 30 días típicamente).

💡 **Sugerencia**: Utiliza nombres y títulos realistas. Esto hará que tu informe sea más profesional y te ayudará a visualizar mejor el resultado final. Para hacer ese poblado de datos puedes usar la IA.

#### 1.3. Configuración del Data Adapter en Jaspersoft Studio

1. Abre **Jaspersoft Studio Community**.
2. Ve a `Window` → `Show View` → `Repository Explorer`.
3. En `Data Adapters`, clic derecho → `Create Data Adapter`.
4. Selecciona **Database JDBC Connection**.
5. Configura la conexión:
    * **Name**: `BibliotecaMunicipal`
    * **JDBC Driver**: `com.mysql.jdbc.Driver` (o `com.mysql.cj.jdbc.Driver` para versiones recientes de MySQL)
    * **JDBC URL**: `jdbc:mysql://localhost:3306/biblioteca_municipal`
    * **Username**: tu usuario de MySQL (por ejemplo, `root`)
    * **Password**: tu contraseña de MySQL
6. Haz clic en **Test** para verificar la conexión.
7. Guarda el adaptador.

**En el documento a entregar:**

* Captura de pantalla del **Data Adapter configurado y probado** correctamente (mensaje de conexión exitosa).
* Captura de alguna **consulta de verificación** en MySQL mostrando los datos insertados (por ejemplo, `SELECT * FROM prestamos LIMIT 10`).

***

### EJERCICIO 2. Diseño de la consulta SQL con JOIN

Antes de diseñar el informe, necesitas preparar la **consulta SQL** que obtendrá todos los datos necesarios combinando las tres tablas.

#### 2.1. Consulta SQL completa

Crea una consulta que obtenga la siguiente información combinando las tres tablas mediante **JOIN**:

```sql
SELECT 
    p.id_prestamo,
    s.nombre AS nombre_socio,
    s.apellidos AS apellidos_socio,
    s.telefono AS telefono_socio,
    l.titulo AS titulo_libro,
    l.autor AS autor_libro,
    l.categoria AS categoria_libro,
    l.editorial AS editorial_libro,
    p.fecha_prestamo,
    p.fecha_devolucion,
    p.dias_prestamo
FROM prestamos p
INNER JOIN socios s ON p.id_socio = s.id_socio
INNER JOIN libros l ON p.id_libro = l.id_libro
ORDER BY p.fecha_prestamo DESC
```


#### 2.2. Prueba de la consulta

1. Ejecuta esta consulta **directamente en MySQL** para verificar que devuelve los datos esperados.
2. Verifica que:
    * Todos los préstamos aparecen con la información completa del socio y del libro.
    * No hay valores NULL inesperados (excepto en `fecha_devolucion` si el préstamo está activo).
    * Los datos están ordenados correctamente.


**En el documento a entregar:**

* **Resultado de la ejecución** en MySQL (captura mostrando al menos las primeras 10-15 filas).

***

### EJERCICIO 3. Creación del informe básico con todas las bandas

Ahora crearás el informe en Jaspersoft Studio utilizando la consulta preparada.

#### 3.1. Creación del informe

1. En Jaspersoft Studio: `File` → `New` → `Jasper Report`.
2. Selecciona la plantilla **Blank A4**.
3. Nombre del archivo: `InformePrestamosLibros.jrxml`.
4. En el asistente, selecciona el **Data Adapter** `BibliotecaMunicipal`.
5. Introduce la **consulta SQL** del Ejercicio 2.
6. El asistente añadirá automáticamente todos los campos a la estructura del informe.

#### 3.2. Diseño de la banda Title

En la banda `Title`, añade:

* Un **Static Text** con el texto: "BIBLIOTECA MUNICIPAL SAN FERNANDO"
    * Fuente: Arial, 18pt, negrita, centrado.
    * Color: azul oscuro.
* Un segundo **Static Text** con: "Informe de Préstamos de Libros"
    * Fuente: Arial, 14pt, negrita, centrado.
    * Color: gris oscuro.
* Un **rectángulo** de fondo con color azul muy claro que abarque toda la banda.


#### 3.3. Diseño de la banda Page Header

En la banda `Page Header`, añade:

* **Fecha de generación del informe**:
    * Añade un campo de texto con la expresión: `new java.util.Date()`
    * Aplica el formato de fecha: `dd/MM/yyyy HH:mm`
    * Etiqueta descriptiva: "Fecha de generación:"
* **Total de registros en el informe**:
    * Añade un campo de texto con la variable: `$V{REPORT_COUNT}`
    * Etiqueta descriptiva: "Total de préstamos:"


#### 3.4. Diseño de la banda Column Header

En la banda `Column Header`, crea las siguientes **columnas** con sus etiquetas:


| Etiqueta | Ancho aprox. | Alineación |
| :-- | :-- | :-- |
| Nº | 30px | Centro |
| Socio | 120px | Izquierda |
| Teléfono | 80px | Centro |
| Título del libro | 180px | Izquierda |
| Autor | 100px | Izquierda |
| Categoría | 80px | Centro |
| F. Préstamo | 70px | Centro |
| Días | 40px | Centro |

Aplica a todas las etiquetas:

* Fuente: Arial, 10pt, negrita.
* Fondo: gris claro.
* Bordes: línea inferior de 1px.


#### 3.5. Diseño de la banda Detail

En la banda `Detail`, coloca los **campos de datos** correspondientes a cada columna definida en el Column Header.

**IMPORTANTE**: Aquí aplicarás las **funciones de manipulación de texto** que se detallan en el siguiente ejercicio.

**En el documento a entregar:**

* Captura de la **vista de diseño** (pestaña Design) mostrando todas las bandas configuradas.

***

### EJERCICIO 4. Aplicación de funciones de manipulación de texto y formato

Este es el ejercicio central de la tarea, donde trabajarás intensivamente con **expresiones y funciones** de manipulación de datos.

#### 4.1. Numeración de líneas

En la primera columna (Nº), crea un campo de texto con la expresión:

```java
$V{REPORT_COUNT}
```

Esto numerará automáticamente cada línea del informe.

#### 4.2. Nombre completo del socio en mayúsculas

En la columna "Socio", en lugar de mostrar simplemente los campos `nombre_socio` y `apellidos_socio`, crea una expresión que:

* Concatene nombre y apellidos.
* Convierta todo a mayúsculas.
* Elimine espacios adicionales.

```java
($F{apellidos_socio} + ", " + $F{nombre_socio}).toUpperCase().trim()
```


#### 4.3. Formato de teléfono

En la columna "Teléfono", aplica un formato visual al número. Si el teléfono es `612345678`, debe mostrarse como `612 34 56 78`:

```java
$F{telefono_socio}.substring(0,3) + " " + 
$F{telefono_socio}.substring(3,5) + " " + 
$F{telefono_socio}.substring(5,7) + " " + 
$F{telefono_socio}.substring(7,9)
```

💡 **Nota**: Esta expresión asume que todos los teléfonos tienen 9 dígitos. Si no es así, añade validaciones.

#### 4.4. Título del libro con primera letra en mayúscula

En la columna "Título del libro", formatea el título para que:

* La primera letra esté en mayúscula.
* El resto en minúsculas.
* Si el título es muy largo (más de 40 caracteres), acórtalo y añade "..." al final.

```java
$F{titulo_libro}.length() > 40 ? 
    $F{titulo_libro}.substring(0,1).toUpperCase() + 
    $F{titulo_libro}.substring(1,40).toLowerCase() + "..." :
    $F{titulo_libro}.substring(0,1).toUpperCase() + 
    $F{titulo_libro}.substring(1).toLowerCase()
```


#### 4.5. Autor en formato "Apellido, N."

Si el autor se guarda como "Gabriel García Márquez", muéstralo como "García Márquez, G.":

```java
$F{autor_libro}.substring($F{autor_libro}.indexOf(" ") + 1) + ", " + 
$F{autor_libro}.substring(0, 1) + "."
```

💡 **Nota**: Esta expresión es simplificada. Para mayor robustez, habría que considerar autores con nombres compuestos.

#### 4.6. Categoría con indicador visual

En la columna "Categoría", añade un **prefijo visual** según la categoría:

```java
$F{categoria_libro}.equals("Novela") ? "📖 " + $F{categoria_libro} :
$F{categoria_libro}.equals("Ensayo") ? "📝 " + $F{categoria_libro} :
$F{categoria_libro}.equals("Técnico") ? "💻 " + $F{categoria_libro} :
$F{categoria_libro}.equals("Infantil") ? "🎨 " + $F{categoria_libro} :
$F{categoria_libro}.equals("Historia") ? "📜 " + $F{categoria_libro} :
$F{categoria_libro}
```


#### 4.7. Formato de fecha

En la columna "F. Préstamo", aplica un formato personalizado a la fecha:

```java
new java.text.SimpleDateFormat("dd-MMM-yyyy").format($F{fecha_prestamo})
```

Esto mostrará la fecha como "15-Nov-2024".

#### 4.8. Días de préstamo con formato

En la columna "Días", muestra el número de días con formato:

```java
String.format("%02d días", $F{dias_prestamo})
```

Esto mostrará "07 días" en lugar de "7".

**En el documento a entregar:**

* Captura de la **vista de diseño** mostrando las expresiones configuradas en las propiedades de los campos.
* Captura del **informe generado en Preview** mostrando los datos formateados correctamente.

***

### EJERCICIO 5. Variables calculadas y banda Summary

En este ejercicio añadirás **cálculos agregados** que se mostrarán en la banda `Summary` al final del informe.

#### 5.1. Contador total de préstamos

Ya tienes la variable predefinida `$V{REPORT_COUNT}` que cuenta el total de registros.

En la banda `Summary`, añade:

* Etiqueta: "Total de préstamos registrados:"
* Campo de texto con: `$V{REPORT_COUNT}`


#### 5.2. Total de días de préstamo

Crea una variable personalizada para sumar todos los días de préstamo:

1. En `Outline`, clic derecho en `Variables` → `Create Variable`.
2. Nombre: `TotalDiasPrestamo`
3. Variable Class: `java.lang.Integer`
4. Calculation: `Sum`
5. Variable Expression: `$F{dias_prestamo}`
6. Initial Value Expression: `new Integer(0)`

En la banda `Summary`, añade:

* Etiqueta: "Total de días acumulados:"
* Campo con: `$V{TotalDiasPrestamo}`


#### 5.3. Promedio de días de préstamo

Crea una variable para calcular el promedio:

1. Crea otra variable llamada `PromedioDiasPrestamo`.
2. Variable Class: `java.lang.Double`
3. Calculation: `Average`
4. Variable Expression: `$F{dias_prestamo}`

En la banda `Summary`, añade:

* Etiqueta: "Promedio de días por préstamo:"
* Campo con formato:

```java
String.format("%.2f días", $V{PromedioDiasPrestamo})
```


#### 5.4. Recuento por categoría

Crea variables para contar préstamos de cada categoría:

1. Variable: `ContadorNovela`
    * Variable Class: `java.lang.Integer`
    * Calculation: `Sum`
    * Variable Expression: `$F{categoria_libro}.equals("Novela") ? 1 : 0`
    * Initial Value: `new Integer(0)`
2. Repite para las demás categorías: `ContadorEnsayo`, `ContadorTecnico`, `ContadorInfantil`, `ContadorHistoria`.

En la banda `Summary`, crea una **tabla resumen** con las categorías y sus recuentos:


| Categoría | Cantidad de préstamos |
| :-- | :-- |
| Novela | `$V{ContadorNovela}` |
| Ensayo | `$V{ContadorEnsayo}` |
| Técnico | `$V{ContadorTecnico}` |
| Infantil | `$V{ContadorInfantil}` |
| Historia | `$V{ContadorHistoria}` |

#### 5.5. Libro más prestado

Añade un **Static Text** con información adicional (puedes calcular esto manualmente mirando los datos o añadir un texto descriptivo).

#### 5.6. Diseño visual de la banda Summary

* Añade un **rectángulo de fondo** gris claro.
* Usa **líneas separadoras** entre las diferentes secciones de resumen.
* Aplica **negrita** a los totales importantes.
* Añade un **título** para esta sección: "Resumen estadístico".

**En el documento a entregar:**

* Captura de la **configuración de variables** en el panel de propiedades.
* Captura del **informe completo generado en PDF** mostrando claramente la banda Summary con todos los cálculos.

***

### EJERCICIO 6. Banda Page Footer y refinamiento del diseño

#### 6.1. Diseño del Page Footer

En la banda `Page Footer`, añade:

* **Número de página con formato**:
    * Usa el elemento predefinido "Page X of Y" desde la paleta (en `Composite Elements`).
    * Personaliza el texto: "Página " + `$V{PAGE_NUMBER}` + " de " + `$V{PAGE_NUMBER}`.
* **Texto del pie**:
    * "Biblioteca Municipal San Fernando - Informe confidencial"
    * Fuente: Arial, 8pt, cursiva, color gris.
* **Línea decorativa** en la parte superior del footer.


#### 6.2. Refinamiento general del diseño

Revisa todo el informe y ajusta:

* **Alineaciones**: todos los números deben estar alineados a la derecha, los textos a la izquierda, los códigos al centro.
* **Bordes**: añade bordes sutiles (0.5px) entre las filas de la banda Detail.
* **Colores alternados**: configura la banda Detail para que las filas pares tengan un fondo ligeramente gris (`#F5F5F5`).
    * En las propiedades de la banda Detail, añade una expresión en `Print When Expression`:
    * Para el fondo alterno, crea un rectángulo en la banda Detail con expresión condicional:

```java
new Boolean($V{REPORT_COUNT}.intValue() % 2 == 0)
```

* **Espaciados**: ajusta los márgenes y espaciados entre elementos para que el informe no se vea apretado.
* **Tamaños de fuente**: asegúrate de que todos los datos sean legibles (mínimo 9pt).

**En el documento a entregar:**

* Captura del **diseño completo** del informe (vista Design con todas las bandas visibles).
* Captura del **informe final generado en PDF** con el diseño refinado.

***

### EJERCICIO 7. Exportación a múltiples formatos y análisis comparativo

#### 7.1. Exportación del informe

Genera el informe en los siguientes formatos:

1. **PDF**: para impresión y distribución formal.
2. **HTML**: para visualización en navegador web.
3. **XLS (Excel)**: para análisis de datos y manipulación posterior.
4. **CSV**: para importación en otras aplicaciones.

Para exportar:

* Ve a la pestaña `Preview`.
* Haz clic en el icono del disquete con flecha hacia abajo.
* Selecciona cada formato y guarda con nombres descriptivos:
    * `InformePrestamos_2024.pdf`
    * `InformePrestamos_2024.html`
    * `InformePrestamos_2024.xls`
    * `InformePrestamos_2024.csv`


**En el documento a entregar:**

* **Capturas de cada formato** abierto en su aplicación correspondiente (Adobe Reader, navegador, Excel).

***

### FASE FINAL: Documentación exhaustiva del proyecto

Redacta el informe completo junto con la reflexión personal y tus conclusiones.


## 5. Entrega

* **Documento PDF**:
    * `Apellido_Nombre_InformeTecnico.pdf` (informe exhaustivo con todos los apartados,).

