# Tema 9. Confección de informes II

**Práctica 1: "Sistema de informes para tienda de electrónica"**

## 1. Introducción

En esta práctica vas a consolidar tus conocimientos sobre **Jaspersoft Studio** simulando un entorno real de trabajo. A diferencia de las tareas guiadas anteriores, aquí no contarás con instrucciones paso a paso. Se te presentarán unos requisitos funcionales que debes cubrir, y tendrás autonomía para decidir cómo implementarlos.

Para evitar trabajar siempre sobre los mismos datos, esta vez comenzarás **diseñando tu propia estructura de datos** para un contexto de negocio diferente: una tienda de componentes electrónicos.

## 2. Objetivos de la práctica

* Diseñar e implementar una base de datos relacional sencilla desde cero.
* Poblar tablas con datos coherentes para realizar pruebas.
* Crear informes dinámicos utilizando **parámetros de entrada**.
* Representar información estadística mediante **gráficos**.
* Implementar estructuras maestro-detalle mediante **subinformes**.
* Aplicar lógica condicional para el formato de datos (estilos condicionales).

---

## 3. Fase 1: Diseño e implementación de la base de datos

Como desarrollador, el primer paso es preparar el origen de datos. Debes crear una base de datos llamada `tienda_electronica` con al menos **3 tablas relacionadas**.

### 3.1. Requisitos de la estructura

Eres libre de definir los campos exactos, pero la estructura mínima debe permitir relacionar clientes con compras. Se sugiere el siguiente esquema simplificado:

1.  **Tabla `clientes`:** Debe contener información personal (id, nombre, apellidos, ciudad, etc.).
2.  **Tabla `productos`:** Debe contener el catálogo (id, nombre, categoría, precio, stock). *Ejemplos de categorías: "Monitores", "Componentes", "Periféricos".*
3.  **Tabla `ventas`:** Esta tabla relacionará las dos anteriores. Debe registrar qué cliente compró qué producto, la fecha de la compra y la cantidad.

### 3.2. Poblado de datos

Debes insertar registros suficientes para que los informes tengan sentido:
* Al menos **10 clientes**.
* Al menos **15 productos** de diferentes categorías y precios.
* Al menos **20-30 registros de ventas** (asegúrate de que un mismo cliente tenga varias compras realizadas en diferentes fechas para que el subinforme funcione correctamente).

> **Nota:** Recuerda crear las claves foráneas (Foreign Keys) correctamente en la tabla `ventas` para poder hacer los `JOIN` en tus consultas SQL.

---

## 4. Fase 2: Especificaciones del informe

La gerencia de la tienda necesita un **"Informe de actividad de clientes"**. Este documento debe ser un PDF generado dinámicamente que cumpla con los siguientes bloques funcionales.

### Bloque A: Parametrización y portada dinámica

El informe no puede ser estático; debe adaptarse a lo que el usuario quiera consultar en ese momento. Al ejecutarse, debe solicitar:

1.  **Filtro de ciudad:** Un parámetro que permita filtrar a los clientes por su ciudad (ej: mostrar solo clientes de "Madrid"). Si el parámetro se deja vacío o nulo, debería idealmente mostrar todos (esto es un reto de SQL, si no lo consigues, que al menos filtre por una ciudad obligatoria).
2.  **Rango de fechas:** Dos parámetros (`FechaDesde`, `FechaHasta`) para filtrar las ventas mostradas en el subinforme.

**Requisitos visuales de la portada:**
* Título del informe.
* Logotipo de la empresa (imagen insertada como recurso del proyecto, no ruta absoluta).
* Nombre del empleado que genera el informe (puedes poner tu nombre fijo o pedirlo como parámetro).

### Bloque B: Gráfico estadístico (Dashboard)

En la banda de resumen (`Summary`) o al inicio del informe, incluye un gráfico para analizar el catálogo:

* **Tipo:** Gráfico de pastel (Pie Chart) o de barras.
* **Datos:** Debe mostrar la **distribución de productos por categoría** (ej: cuántos productos hay de tipo "Monitores", cuántos de "Discos Duros", etc.).
* **Configuración:** Debe incluir leyenda y etiquetas con los valores.

### Bloque C: Listado maestro-detalle (Subinformes)

Esta es la parte central. El informe debe listar los clientes y, para cada uno, detallar sus compras.

1.  **Informe maestro (Padre):**
    * Muestra la lista de clientes filtrada por el parámetro de ciudad.
    * Datos: Nombre completo, ciudad y teléfono.
    * Usa el editor de expresiones para mostrar el nombre en formato `APELLIDOS, Nombre` (todo mayúsculas los apellidos, tipo título el nombre).

2.  **Subinforme (Hijo):**
    * Debe aparecer incrustado debajo de cada cliente.
    * Muestra el historial de compras de *ese* cliente específico.
    * Datos: Nombre del producto comprado, fecha de venta, cantidad y precio unitario.
    * **Importante:** Debes vincular el ID del cliente entre el padre y el hijo, y además pasar los parámetros de fechas para que solo salgan las compras en el rango seleccionado.

### Bloque D: Estilos condicionales y lógica

Para facilitar la lectura rápida de los datos financieros en el subinforme:

* Calcula el **total de la línea** (Precio x Cantidad) en el subinforme.
* **Estilo condicional:** Si el importe de una venta supera los **500€**, esa línea (o el texto del precio) debe aparecer en color **VERDE** y en **NEGRITA** para destacar que es una venta de alto valor.

---

## 5. Criterios de evaluación

La calificación de la práctica se compondrá de la revisión técnica de la memoria entregada y de una validación presencial obligatoria.

### 5.1. Rúbrica de la memoria técnica

La evaluación del documento PDF entregado en la plataforma se basará en la siguiente distribución:

| Criterio | Peso | Descripción |
| :--- | :--- | :--- |
| **Documentación de datos** | 20% | Explicación clara del diseño de la base de datos (tablas y relaciones) y capturas que demuestren datos de prueba coherentes. |
| **Prueba de parametrización** | 20% | Capturas comparativas que demuestren que el informe cambia según los parámetros (ej. filtrado por ciudades distintas). |
| **Estructura y subinformes** | 30% | El informe muestra una jerarquía correcta. Se verifica que los productos listados corresponden inequívocamente al cliente padre. |
| **Elementos visuales** | 15% | El gráfico es legible. Los estilos condicionales (precios resaltados) se aprecian claramente en las capturas. |
| **Calidad de la redacción**| 15% | Memoria profesional, terminología técnica correcta y explicación del Data Adapter y vínculos del subinforme. |

### 5.2. Validación de autoría presencial

Para garantizar la adquisición de competencias, se aplicarán las siguientes normas de calificación:

1.  **La entrega en la plataforma es necesaria pero no suficiente.** Subir la memoria a tiempo habilita el derecho a ser evaluado, pero no garantiza el aprobado.
2.  **Validación en el aula.** Tras la entrega, el profesor podrá requerir al alumno que realice una pequeña modificación sobre su proyecto en JasperSoft Studio o responda preguntas breves sobre su funcionamiento en tiempo real.
3.  **Techo de calificación.** La nota final dependerá del desempeño en esta validación según el siguiente semáforo:

    * 🟢 **Validación satisfactoria (Semáforo verde):** El alumno demuestra autoría total y comprensión profunda del funcionamiento de subinformes y parámetros. Se corrige la práctica sobre **10 puntos**.
    * 🟡 **Validación con dificultades (Semáforo amarillo):** El alumno necesita ayuda del profesor para ubicar elementos, comete errores conceptuales o muestra dudas sobre su propio código SQL/Java. La nota máxima de la práctica será de **6,0**.
    * 🔴 **Validación insuficiente (Semáforo rojo):** El alumno ha entregado la práctica pero no es capaz de explicarla, no sabe cómo compilar el informe o no sabe realizar cambios. Se valorará únicamente el cumplimiento administrativo de la entrega, siendo la nota máxima de **4,0**.

---

## 6. Entregables

Deberás subir a la plataforma un **único documento PDF** titulado `Apellido_Nombre_Practica1_Tema9.pdf`. Este documento debe contener obligatoriamente los siguientes apartados:

1.  **Portada:** Título del proyecto, tu nombre y fecha.
2.  **Diseño de datos:**
    * Breve descripción de las tablas creadas (`clientes`, `productos`, `ventas`).
    * Captura de pantalla de la base de datos (desde tu gestor de BD) mostrando algunos datos de prueba insertados.
3.  **Diseño del informe (JasperStudio):**
    * Capturas de la "Zona de Diseño" tanto del informe maestro como del subinforme.
    * Explicación breve de cómo has resuelto técnicamente la comunicación entre el informe padre y el hijo (paso de parámetros).
4.  **Resultados obtenidos (evidencias):**
    * **Prueba 1:** Captura del informe final generado en PDF mostrando el listado de clientes de una ciudad específica.
    * **Prueba 2:** Captura donde se vea claramente el **gráfico estadístico**.
    * **Prueba 3:** Detalle ampliado donde se aprecie el **estilo condicional** funcionando (una venta superior a 500€ resaltada en verde).
5.  **Reflexión Personal:** Breve comentario sobre las principales diferencias que has notado entre diseñar un informe simple (Tema 8) y uno con subinformes (Tema 9).