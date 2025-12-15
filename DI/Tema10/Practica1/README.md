# Tema 10. Documentación de aplicaciones: Ayudas

**Práctica 1: "Sistema de Ayuda Integral para Gestión Deportiva"**

## 1. Introducción

En esta práctica vas a simular un encargo real. Una empresa de desarrollo de software deportivo te ha contratado para implementar el subsistema de ayuda y documentación de su nueva aplicación de escritorio: **"GymManager Pro"**.

A diferencia de la tarea guiada, aquí no recibirás el código paso a paso. Partirás de unos requisitos funcionales que debes cubrir. Tendrás que diseñar una interfaz gráfica (mockup funcional) y, sobre ella, construir un sistema de ayuda completo utilizando **JavaHelp**.

El objetivo es que demuestres que puedes enfrentarte a una estructura de ficheros compleja y resolver la integración de la ayuda contextual por tu cuenta.

## 2. Objetivos de la práctica

* Diseñar una interfaz Swing (GUI) con múltiples componentes que simule una aplicación real.
* Planificar y estructurar una documentación HTML jerárquica y profesional.
* Implementar todos los ficheros de configuración de JavaHelp (`map`, `toc`, `index`, `helpset`) sin asistencia guiada.
* Vincular la ayuda sensible al contexto (**F1**) a componentes específicos (tablas, campos de texto y desplegables).
* Generar un **Índice Alfabético** funcional dentro del visor de ayuda.

---

## 3. Fase 1: Diseño de la Aplicación Base (Swing)

Como paso previo a la documentación, debes construir la aplicación "GymManager Pro". No es necesario que la aplicación tenga lógica de base de datos real (los botones pueden no hacer nada o solo mostrar un `System.out.println`), pero **los componentes visuales deben existir** para poder vincularles la ayuda.

**Requisitos de la Interfaz (JFrame `MainWindow`):**

La ventana principal debe contener un **Menú Superior** y un sistema de pestañas (`JTabbedPane`) con al menos **3 pestañas** diferentes:

1.  **Pestaña "Alta de Socios":**
    * Campos de texto para: Nombre, Apellidos, DNI.
    * Un `JComboBox` (desplegable) para seleccionar el "Tipo de Tarifa" (ej: Mañana, Tarde, Full-Pass).
    * Un `JSpinner` o campo numérico para la "Edad".
    * Botón "Dar de Alta".

2.  **Pestaña "Reservas de Clases":**
    * Una lista (`JList` o simulada visualmente) con clases: Yoga, CrossFit, Pilates.
    * Un selector de fecha (o campo de texto simple).
    * Un componente `JCheckBox` que diga: "Solicitar monitor personal".
    * Botón "Confirmar Reserva".

3.  **Pestaña "Administración":**
    * Esta pestaña simulará la configuración técnica.
    * Debe contener al menos dos opciones de configuración (ej: "Activar notificaciones", "Modo oscuro").
    * Botón "Resetear Sistema".

> **Nota:** Dedica el tiempo justo al diseño visual para que sea ordenado. Lo importante en esta práctica no es la belleza de la interfaz, sino que existan los componentes suficientes para documentarlos después.

---

## 4. Fase 2: Arquitectura del Sistema de Ayuda

La documentación debe ser rica y navegable. Se exige que crees una estructura de carpetas ordenada dentro de tu proyecto.

### 4.1. Contenido HTML
Debes redactar y crear **al menos 6 archivos HTML** diferentes. No sirve con poner "lorem ipsum", el texto debe simular una ayuda real (aunque sea breve).

* **Bienvenida:** Visión general de la app.
* **Gestión de Socios:** Explicación general de la pestaña 1.
* **Tipos de Tarifas:** Un HTML específico detallando qué incluye cada tarifa (esto servirá para ayuda contextual muy específica).
* **Cómo Reservar:** Guía paso a paso para la pestaña 2.
* **Monitores Personales:** Explicación de las condiciones del monitor.
* **Herramientas de Admin:** Advertencias sobre el uso de la pestaña 3.

### 4.2. Configuración JavaHelp (XML)
Debes crear manualmente los ficheros de configuración. Como novedad respecto a la tarea anterior, se exige la inclusión de un **Índice Alfabético**.

1.  **Map (`map.jhm`):** Asigna IDs lógicos a tus 6+ archivos HTML.
2.  **Table of Contents (`toc.xml`):** Debe tener una estructura **anidada** de al menos 2 niveles.
    * *Ejemplo:* Una carpeta "Manual de Usuario" que contenga dentro "Socios" y "Reservas", y otra carpeta "Técnico" para "Administración".
3.  **Index (`index.xml`):** Debes crear un índice de palabras clave.
    * Debes incluir términos como: "Tarifa", "Baja", "Monitor", "Resetear", "Horarios".
    * Al hacer clic en estas palabras en la pestaña "Índice" del visor, deben llevar al tema HTML correspondiente.
4.  **HelpSet (`helpset.hs`):** Debe integrar tanto la vista de Tabla de Contenidos (`TOCView`) como la vista de Índice (`IndexView`).

---

## 5. Fase 3: Integración y Contexto (F1)

Esta es la parte core de la evaluación. La aplicación debe responder a la tecla **F1** de forma inteligente según dónde esté el foco del usuario.

**Requisitos de vinculación:**

1.  **Ayuda General:** Si el foco está en la ventana general (o en un área neutra), F1 abre la "Bienvenida".
2.  **Ayuda de Sección:** Si el usuario está en la pestaña "Alta de Socios" (en el campo Nombre, por ejemplo), F1 debe abrir la ayuda general de "Gestión de Socios".
3.  **Ayuda de Detalle (Granularidad Fina):**
    * Si el usuario pone el foco específicamente en el desplegable (`JComboBox`) de **"Tipo de Tarifa"**, al pulsar F1 se debe abrir directamente el archivo HTML de **"Tipos de Tarifas"** (no el general de socios).
    * Si el usuario marca el `JCheckBox` de **"Solicitar monitor personal"**, al pulsar F1 se debe abrir la página de **"Monitores Personales"**.
4.  **Ayuda de Peligro:**
    * Si el usuario pone el foco en el botón **"Resetear Sistema"** de la pestaña Administración, F1 debe llevar a la página de "Herramientas de Admin".

> 💡 **Tip:** Recuerda que para tener múltiples vistas (TOC e Index) en tu `helpset.hs`, debes añadir múltiples etiquetas `<view>`. Revisa la documentación o los apuntes para ver la sintaxis correcta de `javax.help.IndexView`.

---

## 6. Criterios de Evaluación

La evaluación de esta práctica no se basa únicamente en la entrega de archivos. Se aplicará un sistema de **validación de autoría presencial** para garantizar la adquisición de las competencias.

### 6.1. Desglose de puntuación (Sobre 10)

| Criterio | Puntos | Descripción |
| :--- | :--- | :--- |
| **Interfaz Swing** | 2 ptos | La aplicación cumple con los requisitos de las 3 pestañas y contiene todos los componentes solicitados (ComboBox, CheckBox, Spinner, etc.). |
| **Estructura HTML** | 2 ptos | Existen al menos 6 ficheros HTML con contenido coherente y están bien organizados en carpetas. |
| **Configuración XML** | 2.5 ptos | Los ficheros `map`, `toc` y `helpset` son correctos. El TOC muestra jerarquía. El `index.xml` funciona y muestra palabras clave buscables. |
| **Contextualidad (F1)** | 2.5 ptos | La ayuda reacciona al foco. Se demuestra granularidad fina (ayuda específica en el ComboBox de tarifas y CheckBox de monitor). |
| **Calidad documental** | 1 pto | El documento PDF entregado es claro, las capturas son legibles y se explica el proceso seguido. |

### 6.2. Validación de autoría presencial

Para obtener una calificación positiva, es indispensable superar una validación presencial en el aula. Las reglas son las siguientes:

1.  **Entrega necesaria pero no suficiente:** Subir la práctica a la plataforma es un requisito para tener derecho a corrección, pero no garantiza el aprobado.

2.  **Defensa individual:** Tras la entrega, el profesor podrá una breve validación en clase. Se podrá requerir al alumno que realice una pequeña modificación sobre su código en tiempo real o responda preguntas sobre el funcionamiento interno de la ayuda y la estructura de archivos.

3.  **Techo de calificación:** La nota final de la práctica tendrá un límite máximo dependiendo del desempeño en esta validación:

      * 🟢 **Validación satisfactoria (Semáforo verde):** El alumno demuestra autoría, soltura y comprensión profunda del código. Se corrige la práctica sobre **10 puntos**.
      * 🟡 **Validación con dificultades (Semáforo amarillo):** El alumno necesita ayuda del profesor para ubicar partes de su propio código, comete errores de sintaxis al modificarlo o muestra dudas al responder a las preguntas. La nota máxima de la práctica será de **6,0**.
      * 🔴 **Validación insuficiente (Semáforo rojo):** El alumno ha entregado la práctica pero no es capaz de explicarla, modificarla o ejecutarla en el entorno de desarrollo. Evidencia de copia o desconocimiento total. Se valorará únicamente el cumplimiento de la entrega documental, siendo la nota máxima de **4,0**.

---

## 7. Entregables

Debes subir a la plataforma un único documento **PDF** titulado `Apellido_Nombre_Practica1_Tema10.pdf` con el siguiente contenido:

1.  **Portada:** Nombre, ciclo y fecha.
2.  **Diseño GUI:** Una captura de pantalla de la aplicación ejecutándose, mostrando una de las pestañas.
3.  **Ficheros de Configuración:**
    * Captura del código de tu `helpset.hs` (donde se vean las dos vistas: TOC e Index).
    * Captura del código de tu `index.xml`.
4.  **Evidencias de Funcionamiento (Capturas clave):**
    * **Prueba 1 (Índice):** Captura del visor de ayuda con la pestaña "Índice" seleccionada, mostrando las palabras clave.
    * **Prueba 2 (Ayuda Contextual):** Captura de pantalla donde se vea que el foco está en el **ComboBox de Tarifas** y, al lado, la ventana de ayuda abierta mostrando el HTML específico de tarifas (no la portada).
5.  **Reflexión Técnica:**
    * Explica brevemente cómo has resuelto la estructura del `helpset.hs` para incluir el índice.
    * ¿Qué dificultad has encontrado al vincular componentes específicos como el `JCheckBox` con su ayuda concreta?