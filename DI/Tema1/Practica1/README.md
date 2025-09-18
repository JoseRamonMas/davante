# Práctica: Introducción a la confección de interfaces con Java Swing y Eclipse

## Descripción General

En esta práctica vas a poner en práctica los conceptos estudiados en el **Tema 1** de la asignatura *Desarrollo de Interfaces*. El objetivo es que seas capaz de instalar y preparar el entorno de trabajo, conocer los distintos paradigmas de programación, y crear tus primeras interfaces gráficas en Java utilizando la librería Swing en el entorno de desarrollo Eclipse.

> **Enfoque:** Consolidación de conceptos ya trabajados en el tema
>
> **Evaluación:** Se valorará tanto el resultado final como el proceso de trabajo y la calidad de la documentación

## Objetivos

Al completar esta práctica serás capaz de:

- **Comprender y explicar** con tus palabras los principales paradigmas de programación y su relación con el desarrollo de interfaces
- **Instalar y configurar** el entorno Eclipse con la librería Swing y la extensión WindowBuilder
- **Diseñar y programar** interfaces gráficas simples en Java, tanto de forma manual como usando el modo Design de Eclipse
- **Documentar** el proceso de trabajo con capturas de pantalla y explicaciones propias


## Entregables

Al finalizar la práctica, deberás entregar un **único archivo en formato PDF** que contenga:

- Las **capturas de pantalla** de cada paso, mostrando tu nombre de usuario del sistema operativo o una marca personal
- **Explicaciones redactadas por ti** (3-5 líneas por apartado) que demuestren que comprendes el proceso realizado
- Al final del documento, incluye un **archivo ZIP** con el código fuente de los proyectos de Eclipse generados durante la práctica

***

## Desarrollo de la práctica

### 1. Preparación del entorno

**Tareas a realizar:**

1. Descarga e instala `Eclipse IDE for Enterprise Java Developers`
2. Instala la extensión `WindowBuilder` siguiendo las instrucciones del Tema 1
3. Comprueba que puedes crear y ejecutar un proyecto Java sencillo

**Entrega:**

- Incluye una captura de pantalla de Eclipse ya instalado y ejecutándose, donde se vea claramente tu nombre de usuario en el sistema
- Escribe un breve texto explicando cualquier dificultad encontrada durante la instalación y cómo la resolviste


### 2. Investigación breve sobre paradigmas

Redacta en tus **propias palabras**:

- La diferencia entre **programación imperativa y declarativa**, indicando en cuál de ellas encaja el uso de Java Swing
- Un breve resumen (4-5 líneas) de la diferencia entre **programación orientada a objetos**, **basada en eventos** y **basada en componentes**, relacionando cada una con el desarrollo de interfaces gráficas

> **Importante:** Texto redactado por ti, sin copiar definiciones literales de internet

### 3. Primera interfaz solo con código

Crea una clase Java llamada `MiPrimeraVentana` que abra un `JFrame` de **400×400 píxeles** con un título de tu elección.

⚠️ **Restricción:** No utilices la creación automática de JFrame de Eclipse; escribe el código manualmente.

**Incluye en el documento:**

- El **código completo comentado**
- Una **captura** de la ventana ejecutándose
- Una breve explicación de qué hace cada método clave utilizado, como `setSize()`, `setDefaultCloseOperation()` o `setVisible()`


### 4. Primera interfaz con modo Design

Ahora crea un nuevo `JFrame` utilizando la herramienta **WindowBuilder** en modo Design:

- Añade dos botones: **"Aceptar"** y **"Cancelar"**
- Cambia las **propiedades de color y fuente** de cada botón desde la pestaña Properties
- Añade un `JPanel` para agrupar los botones

**Entrega:**

- Capturas de la vista Design, de la ventana final y del código generado en la pestaña Source
- Explica las **diferencias** que has encontrado entre crear la interfaz de forma manual y utilizar el modo Design


### 5. Gestión de un evento básico

Programa la siguiente funcionalidad:

- Al pulsar **"Aceptar"**, debe aparecer un `JOptionPane` con el texto `"Has aceptado"`
- Al pulsar **"Cancelar"**, la ventana debe cerrarse

**Entrega:**

- Código comentado del `ActionListener`
- Una captura de pantalla que muestre el cuadro de diálogo


### 6. Mini-prototipo de calculadora

Diseña un boceto de una calculadora sencilla en modo Design:

- **Botones** para los dígitos del 0 al 9
- **Botones** para las operaciones básicas (+, −, ×, ÷)
- Un `JLabel` o `JTextField` para mostrar los resultados

> **Nota:** No es necesario que la calculadora funcione; se trata solo de preparar el diseño de la interfaz

**Entrega:**

- Captura de la interfaz en modo Design
- Un breve esquema (puede ser un dibujo o una lista) en el que indiques qué componentes has usado y por qué


### 7. Reflexión final

Para finalizar, redacta un breve texto (5-6 líneas) en el que comentes:

- Qué parte de la práctica te ha resultado **más sencilla** y cuál te ha parecido **más complicada**
- Una posible **mejora o ampliación** que te gustaría implementar si tuvieras más tiempo

***

## Recomendaciones para evitar copia literal

- Asegúrate de que en cada captura aparezca tu **nombre de usuario** del sistema operativo o una marca personal
- La redacción debe ser **completamente personal**. Las copias literales de internet o de compañeros serán penalizadas
- Se valorará especialmente la **claridad y calidad** de las explicaciones


## Criterios de evaluación

La práctica se calificará sobre **10 puntos**, distribuidos de la siguiente forma:


| Apartado | Puntos |
| :-- | :-- |
| Instalación y preparación del entorno | 1 punto |
| Investigación sobre paradigmas | 1 punto |
| Ventana solo con código | 1 punto |
| Ventana con modo Design | 2 puntos |
| Gestión de eventos | 1 punto |
| Prototipo de calculadora | 2 puntos |
| Documentación y capturas | 2 puntos |
| **Total** | **10 puntos** |
