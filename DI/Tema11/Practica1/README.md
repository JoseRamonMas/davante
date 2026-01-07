# Tema 11. Distribución de aplicaciones

**Práctica 1: "Ingeniería de lanzamiento: Suite corporativa completa"**

## 1. Introducción

En esta práctica vas a simular el despliegue de un paquete de software completo. Tu empresa ha desarrollado la suite **"AdminPack v1.0"**, un conjunto de herramientas para administradores de sistemas que consta de dos aplicaciones independientes: **"Monitor de Red"** y **"Gestor de Usuarios"**.

Tu responsabilidad como técnico de despliegue no es programar la lógica compleja, sino preparar el ecosistema de distribución. Deberás generar los ejecutables de ambas herramientas, dotarlas de una identidad visual profesional (iconos y pantallas de carga), firmarlas digitalmente y empaquetarlas en un **único instalador maestro** que permita al usuario final instalar toda la suite de una vez, incluyendo manuales de usuario.

## 2. Objetivos de la práctica

* Gestionar la arquitectura de distribución de una suite con **múltiples ejecutables**.
* Configurar **Launch4j** para múltiples aplicaciones, incluyendo metadatos de versión y *splash screens*.
* Diseñar un instalador avanzado en **Inno Setup** que gestione accesos directos múltiples, licencias, idiomas e imágenes personalizadas del asistente.
* Implementar la firma digital en todos los componentes Java de la solución.
* Incluir y gestionar archivos no ejecutables (documentación PDF/TXT) dentro del proceso de instalación.

---

## 3. Especificaciones del proyecto

### Fase 1: Preparación de recursos y código (La materia prima)

Antes de empaquetar, necesitas construir el software y reunir los recursos gráficos.

1. **Estructura de proyecto:** Crea un proyecto en Eclipse llamado `Practica1_Suite`.
2. **Recursos gráficos:** Necesitarás descargar o crear (puedes usar Paint/Canva) los siguientes archivos y guardarlos organizados:
* 2 Iconos diferentes (`.ico`): Uno para el Monitor y otro para el Gestor.
* 2 Imágenes de carga (`.bmp`): Una para cada aplicación.
* 1 Imagen vertical para el lateral del instalador (tipo *banner* de 164x314 px aprox).
* 1 Archivo de texto llamado `manual_usuario.txt` con instrucciones ficticias.
* 1 Archivo de texto llamado `eula.txt` con el acuerdo de licencia.


3. **Código fuente:** Crea dos clases principales distintas en paquetes diferentes:
* **Clase 1:** `net.admin.MonitorRed.java`
*(Copia el siguiente código simulado)*
```java
package net.admin;
import javax.swing.*;
import java.awt.*;
public class MonitorRed extends JFrame {
    public MonitorRed() {
        setTitle("Monitor de Red Corporativo");
        setSize(400, 200);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLocationRelativeTo(null);
        add(new JLabel("<html><h2 style='color:blue'>Estado: CONECTADO</h2><p>Analizando paquetes...</p></html>", SwingConstants.CENTER));
    }
    public static void main(String[] args) {
        try { Thread.sleep(2000); } catch (InterruptedException e) {} // Simula carga
        SwingUtilities.invokeLater(() -> new MonitorRed().setVisible(true));
    }
}

```


* **Clase 2:** `auth.users.GestorUsuarios.java`
*(Copia el siguiente código simulado)*
```java
package auth.users;
import javax.swing.*;
import java.awt.*;
public class GestorUsuarios extends JFrame {
    public GestorUsuarios() {
        setTitle("Gestión de Usuarios v1.0");
        setSize(400, 200);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLocation(100, 100);
        add(new JLabel("<html><h2 style='color:green'>Base de datos: ACTIVA</h2><p>Esperando credenciales...</p></html>", SwingConstants.CENTER));
    }
    public static void main(String[] args) {
        try { Thread.sleep(2000); } catch (InterruptedException e) {} // Simula carga
        SwingUtilities.invokeLater(() -> new GestorUsuarios().setVisible(true));
    }
}

```




4. **Generación de JARs:** Exporta **dos archivos JAR ejecutables** independientes: `monitor.jar` y `gestor.jar`. Asegúrate de probar que ambos funcionan por separado.

### Fase 2: Conversión a nativo (Launch4j)

El cliente exige ejecutables `.exe` profesionales. Debes procesar **ambos** JARs con Launch4j.

**Requisitos para CADA ejecutable:**

1. **Icono propio:** Cada app debe tener su icono correspondiente.
2. **Splash screen:** Configura la pantalla de carga (`.bmp`) para que se muestre durante los 2 segundos que tarda en arrancar la app.
3. **Metadatos de versión:** En la pestaña "Version Info" de Launch4j, debes rellenar los campos para que, al hacer clic derecho en el `.exe` en Windows -> Propiedades -> Detalles, se vea:
* *File Description:* "Herramienta de la Suite AdminPack".
* *Copyright:* "Tu Nombre © 2025".



> **Resultado esperado:** Debes obtener `MonitorRed.exe` y `GestorUsuarios.exe`.

### Fase 3: Firma digital

Para garantizar la seguridad de la suite:

1. Genera un almacén de claves (keystore) válido por 6 meses.
2. Firma digitalmente **ambos** archivos JAR originales (`monitor.jar` y `gestor.jar`) usando `jarsigner`.
3. Verifica por consola que la firma es correcta.

### Fase 4: El instalador maestro (Inno Setup)

Debes crear un único instalador llamado `Instalador_AdminPack_v1.exe`. Este es el paso más laborioso.

**Requisitos del instalador:**

1. **Archivos:** Debe instalar los dos `.exe`, los dos `.jar` necesarios (si no están empaquetados dentro) y el `manual_usuario.txt`.
2. **Estructura en destino:** El instalador debe crear una carpeta en Archivos de Programa con esta estructura:
* `\AdminPack` (raíz)
* `MonitorRed.exe`
* `GestorUsuarios.exe`
* `\docs` (aquí dentro debe ir el `manual_usuario.txt`)




3. **Accesos directos:**
* Debe crear una carpeta en el Menú Inicio llamada "AdminPack" que contenga accesos directos a las dos aplicaciones y al manual.
* Debe ofrecer crear icono en el escritorio **solo** para el "Monitor de Red".


4. **Licencia y estética:**
* Debe mostrar tu archivo `eula.txt` y obligar a aceptarlo.
* Debe sustituir la imagen lateral clásica del asistente de instalación por tu imagen personalizada (Propiedad `WizardImageFile` en Inno Setup).


5. **Idiomas:** Debe estar disponible en Español, Catalán e Inglés.
6. **Acción final:** Al terminar la instalación, debe ofrecer una casilla (checkbox) para "Abrir manual de usuario ahora".

---

## 4. Criterios de evaluación

La práctica se evaluará sobre **10 puntos**. Se prestará especial atención a que todos los elementos de la suite funcionen de forma integrada.

| Criterio | Puntos | Descripción |
| --- | --- | --- |
| **Generación de ejecutables** | 2 ptos | Se han creado correctamente los dos `.exe` independientes y ambos funcionan. |
| **Identidad visual (Splash/Iconos)** | 2 ptos | Ambas apps tienen iconos distintos y muestran sus respectivas pantallas de carga (splash screen) al iniciarse. Los metadatos de versión son visibles en Windows. |
| **Configuración del instalador** | 3 ptos | El instalador coloca los archivos en la estructura de carpetas solicitada (incluyendo la subcarpeta `docs`), muestra la licencia `eula.txt` y tiene la imagen lateral personalizada. |
| **Accesos directos y acciones** | 1.5 ptos | Se crean correctamente los accesos en el Menú Inicio para ambas apps y el manual. La acción final de "Abrir manual" funciona. |
| **Firma digital** | 1.5 ptos | Se demuestra la firma correcta de los componentes Java mediante verificación en consola. |

---

## 5. Documentación a entregar

Genera un documento PDF titulado `Apellido_Nombre_Practica1_Tema11.pdf` que contenga las siguientes evidencias. **Organiza el documento claramente por apartados**:

1. **Evidencia de recursos:** Captura del explorador de archivos mostrando todos los iconos, imágenes y ejecutables generados antes de crear el instalador.
2. **Evidencia de Launch4j:**
* Captura de la configuración de "Version Info" de una de las apps.
* Captura de las "Propiedades > Detalles" del `.exe` generado en Windows mostrando tu nombre en el Copyright.
* Captura del funcionamiento del *splash screen* (foto de pantalla o captura rápida).


3. **Evidencia de firma:** Captura de consola con el comando de verificación de firma sobre ambos JARs.
4. **Evidencia del instalador (Inno Setup):**
* Captura del asistente de instalación mostrando la **imagen lateral personalizada** y el texto de licencia.
* Captura de la pantalla final con la opción "Abrir manual de usuario".


5. **Resultado final:**
* Captura de la carpeta "Archivos de Programa/AdminPack" mostrando la estructura creada (incluida la carpeta `docs`).
* Captura del Menú Inicio desplegado mostrando el grupo "AdminPack" con los tres accesos directos.



## 6. Entrega

Sube a la plataforma únicamente el **archivo PDF**. Conserva todos los archivos generados en tu equipo hasta recibir la calificación.

## ANEXO I: Alumnos con MacOS

Aquí tienes el texto redactado como un anexo listo para copiar y pegar al final de tu documento de práctica. He mantenido el tono académico y las instrucciones precisas.

---

# ANEXO I: Guía de adaptación para macOS

<details>
<summary>Despliga el anexo si es tu caso</summary>

Dado que en macOS no se utilizan archivos `.exe` ni instaladores tipo Wizard (Siguiente > Siguiente), el objetivo de la práctica se adapta para seguir los estándares de distribución de la industria en Apple: **Paquetes de Aplicación (`.app`)** distribuidos dentro de una **Imagen de Disco (`.dmg`)**.

### Adaptación Fase 1: Recursos

1. **Iconos:** macOS no utiliza archivos `.ico`. Debes convertir tus imágenes o buscar iconos en formato **`.icns`** (Apple Icon Image). Existen conversores online gratuitos para pasar de PNG a ICNS.
2. **Imágenes:** Mantén las imágenes BMP/PNG para el *splash screen*.

### Adaptación Fase 2: Conversión a nativo (Reemplaza Launch4j)

En lugar de usar Launch4j para crear un `.exe`, utilizarás la herramienta **`jpackage`** (incluida en el JDK a partir de la versión 14) para generar aplicaciones nativas `.app`.

**Pasos:**

1. Abre la **Terminal**.
2. Navega hasta la carpeta donde tienes tus JARs y recursos (usa `cd`).
3. Ejecuta el siguiente comando para **cada aplicación** (Monitor y Gestor), ajustando los nombres de tus archivos:

**Para el Monitor de Red:**

```bash
jpackage --input . --name "MonitorRed" --main-jar monitor.jar --main-class net.admin.MonitorRed --icon icono_monitor.icns --copyright "Tu Nombre 2025" --app-version 1.0 --type app-image --splash splash_monitor.bmp

```

**Para el Gestor de Usuarios:**

```bash
jpackage --input . --name "GestorUsuarios" --main-jar gestor.jar --main-class auth.users.GestorUsuarios --icon icono_gestor.icns --copyright "Tu Nombre 2025" --app-version 1.0 --type app-image --splash splash_gestor.bmp

```

> **Nota:** El parámetro `--splash` incrustará tu imagen de carga para que aparezca automáticamente al abrir la aplicación, cumpliendo el requisito de identidad visual.

### Adaptación Fase 3: Firma Digital

Este paso es idéntico al de Windows, pero realizado en la Terminal de macOS.

1. Genera el keystore con `keytool`.
2. Firma los archivos JAR originales (`monitor.jar` y `gestor.jar`) con `jarsigner` antes de ejecutar los comandos de `jpackage` del paso anterior.

### Adaptación Fase 4: El Instalador Maestro (Reemplaza Inno Setup)

En macOS no crearemos un `setup.exe`. Crearemos una **Imagen de Disco (`.dmg`)** profesional que contenga la suite.

**Pasos:**

1. **Preparación de la carpeta:**
* Crea una carpeta en tu escritorio llamada `AdminPack_Distribucion`.
* Mueve dentro las dos aplicaciones generadas (`MonitorRed.app` y `GestorUsuarios.app`).
* Crea una subcarpeta llamada `docs` y coloca dentro el `manual_usuario.txt`.
* Copia el archivo `eula.txt` en la raíz de la carpeta.


2. **Crear acceso directo a Aplicaciones:**
* Haz clic derecho (o Ctrl+Clic) sobre la carpeta de sistema "Aplicaciones" en Finder y selecciona "Crear alias".
* Mueve ese alias dentro de tu carpeta `AdminPack_Distribucion`. Esto permite al usuario arrastrar la app para instalarla.


3. **Generar el DMG:**
* Abre la **Utilidad de Discos** (Disk Utility).
* En el menú superior: `Archivo > Nueva imagen > Imagen de la carpeta...`
* Selecciona tu carpeta `AdminPack_Distribucion`.
* En formato de imagen selecciona "Lectura/escritura" o "Comprimida" y guárdalo como `Instalador_AdminPack_v1.dmg`.



---

### Documentación a entregar (Adaptación)

Para los alumnos de macOS, las evidencias del PDF se sustituyen de la siguiente manera:

1. **Evidencia de recursos:** Igual que en el enunciado original (captura de Finder).
2. **Evidencia de Launch4j (Sustituida por jpackage):**
* Captura de la terminal mostrando el comando `jpackage` ejecutado.
* Captura de la ventana "Obtener información" (Cmd + I) sobre una de las apps (`.app`), donde se vea el **Copyright** y la **Versión 1.0**.
* Captura/Foto del **Splash Screen** funcionando al abrir la app.


3. **Evidencia de firma:** Captura de terminal con `jarsigner -verify`.
4. **Evidencia del instalador (Sustituida por DMG):**
* Captura del archivo `.dmg` abierto (montado en el escritorio). Debe verse una distribución estética: las dos Apps, la carpeta `docs`, el archivo de licencia y el acceso directo a Aplicaciones.


5. **Resultado final:**
* Captura del **Launchpad** o de la carpeta **Aplicaciones** de tu Mac mostrando que las apps se han copiado y están listas para usarse.

</details>