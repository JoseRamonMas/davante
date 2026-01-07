
# Tema 11. Distribución de aplicaciones

**Tarea 1: "Despliegue profesional: De Eclipse al instalador final".**

## 1. Introducción

Como desarrolladores, a menudo nos centramos en el código, pero olvidamos que el usuario final no sabe lo que es Eclipse, ni la consola, ni un `.class`. Para que una aplicación sea exitosa, debe ser fácil de instalar y ejecutar.

En el **Tema 11** hemos visto la teoría sobre empaquetado, dependencias y firmas digitales. En esta tarea guiada vamos a cerrar el ciclo de desarrollo de software transformando un proyecto Java en un producto profesional distribuible.

Aprenderás a "sacar" tu aplicación del IDE, envolverla en un ejecutable nativo de Windows (`.exe`) para que tenga su propio icono y, finalmente, crear un **Asistente de Instalación** (*Wizard*) profesional que cree accesos directos y gestione la desinstalación.

## 2. Objetivos de aprendizaje

Al finalizar esta práctica guiada, deberás ser capaz de:

* Exportar correctamente un proyecto Java desde Eclipse generando un archivo **JAR ejecutable**.
* Utilizar **Launch4j** para encapsular un JAR dentro de un contenedor `.exe` nativo de Windows.
* Crear un instalador profesional (`setup.exe`) utilizando **Inno Setup Compiler**, gestionando rutas y licencias.
* Comprender el concepto de **Firma Digital** y utilizar `jarsigner` para firmar tu aplicación.

---

## 3. Desarrollo de la práctica

Vamos a simular que hemos terminado una pequeña utilidad llamada **"SystemInfo Tool"** y queremos distribuirla a los clientes.

### EJERCICIO 1. Preparación del artefacto Java (JAR)

**Cometido:**
Crear una pequeña aplicación Swing y exportarla correctamente asegurando que los recursos (imágenes) se empaquetan dentro.

**Pasos:**

1. **Proyecto en Eclipse:** Crea un proyecto llamado `Tema11_Distribucion`.
2. **Recursos:**
* Crea una carpeta llamada `res` (resources) dentro de `src`.
* Busca una imagen pequeña (tipo logo o icono png) y guárdala ahí con el nombre `logo.png`.


3. **Código:** Crea la clase `SystemInfo.java` con este código (fíjate en cómo cargamos la imagen para que no falle al exportar):

```java
import javax.swing.*;
import java.awt.*;
import java.net.URL;

public class SystemInfo extends JFrame {

    public SystemInfo() {
        setTitle("SystemInfo Tool v1.0");
        setSize(400, 300);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLocationRelativeTo(null);
        setLayout(new BorderLayout());

        // Cargar imagen de forma segura para JAR
        URL imgURL = getClass().getResource("/res/logo.png");
        if (imgURL != null) {
            setIconImage(new ImageIcon(imgURL).getImage());
        }

        JLabel lblInfo = new JLabel("<html><center><h1>Sistema Listo</h1>"
                + "OS: " + System.getProperty("os.name") + "<br>"
                + "Java: " + System.getProperty("java.version") + "</center></html>", SwingConstants.CENTER);
        
        add(lblInfo, BorderLayout.CENTER);
        
        JButton btnSalir = new JButton("Cerrar Aplicación");
        btnSalir.addActionListener(e -> System.exit(0));
        add(btnSalir, BorderLayout.SOUTH);
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> new SystemInfo().setVisible(true));
    }
}

```

4. **Exportación:**
* Haz clic derecho en el proyecto > `Export` > `Java` > `Runnable JAR file`.
* **Launch configuration:** Selecciona tu clase `SystemInfo`.
* **Export destination:** Guárdalo en tu escritorio dentro de una carpeta nueva llamada `Deploy` con el nombre `app.jar`.
* **Library handling:** Selecciona "Extract required libraries into generated JAR".



> **Prueba:** Ve a la carpeta `Deploy` y haz doble clic en `app.jar`. Debería abrirse la ventana. Si no abre, revisa si tienes instalado el JRE correctamente.

---

### EJERCICIO 2. Creación del ejecutable (.exe) con Launch4j

**Cometido:**
Los usuarios desconfían de los archivos `.jar`. Vamos a "disfrazarlo" de `.exe` y ponerle un icono propio.

**Requisitos previos:**

* Descarga e instala [Launch4j](http://launch4j.sourceforge.net/).
* Consigue un archivo de icono (`.ico`). Puedes convertir cualquier imagen a .ico usando convertidores online gratuitos. Guárdalo en tu carpeta `Deploy` como `app_icon.ico`.

**Pasos:**

1. Abre **Launch4j**.
2. Pestaña **Basic**:
    * **Output file:** Selecciona la ruta de destino y ponle nombre, ej: `C:\Users\...\Deploy\SystemInfo.exe`.
    * **Jar:** Selecciona tu `app.jar` creado en el ejercicio anterior.
    * **Icon:** Selecciona tu `app_icon.ico`.

3. Pestaña **JRE**:
    * **Min JRE version:** Escribe `1.8.0` (o la versión que uses). Esto asegurará que el usuario tenga Java.

4. Pestaña **Header**:
    * Asegúrate de que `GUI` está seleccionado (para que no se abra una consola negra detrás).

5. **Construir:**
    * Pulsa el icono del engranaje ("Build wrapper"). Te pedirá guardar la configuración (XML), guárdala como `config.xml`.
    * Launch4j generará el archivo `SystemInfo.exe`.

> **Prueba:** Ejecuta el nuevo `.exe`. Verás que ahora tiene tu icono personalizado y se comporta como una aplicación nativa.

---

> [!WARNING]
> Si usas MacOS:
>
> ```bash
> jpackage --input . --name SystemInfo --main-jar app.jar --main-class SystemInfo --icon logo.icns --type app-image
>```
>
> *O bien usando `Platypus`*

---

### EJERCICIO 3. Creación del asistente de instalación (Setup)

**Cometido:**
Ahora crearemos el distribuible final: un archivo `setup_v1.exe` que el usuario descargará para instalar el programa en "Archivos de Programa", crear accesos directos y configurar el desinstalador. Usaremos **Inno Setup** (mencionado en el caso práctico 2 de tus apuntes).

**Requisitos previos:**

* Descarga e instala [Inno Setup](https://jrsoftware.org/isdl.php).

**Pasos:**

1. Abre Inno Setup y selecciona **"Create a new script file using the Script Wizard"**.
2. **Application Info:**
    * App Name: "SystemInfo Tool".
    * App Version: "1.0".
    * App Publisher: Tu Nombre.

3. **Application Folder:** Deja los valores por defecto (permitir al usuario cambiar la carpeta).
4. **Application Files (IMPORTANTE):**
    * **Application main executable file:** Selecciona el `SystemInfo.exe` que creaste con Launch4j (¡No el JAR!).
    * **Other application files:** Aquí debes añadir "Add files..." y seleccionar también el `app.jar` y cualquier librería si la tuvieras externa (aunque en este caso el exe busca el jar o lo envuelve, dependiendo de cómo configuraste Launch4j, para asegurar, incluye el jar si Launch4j no lo empaquetó dentro). *Nota: Si en Launch4j no marcaste "Don't wrap the jar", el exe ya contiene el jar y no hace falta añadirlo aquí. Asumiremos que el exe es autosuficiente.*

5. **Shortcuts:** Marca "Create a shortcut to the main executable in the Start Menu folder" y "Allow user to create a desktop shortcut".
6. **Setup Install Mode:** Deja por defecto.
7. **Finalizar:** Sigue hasta el final y deja que compile el script. Inno Setup te preguntará si quieres compilar ahora. Di que **SÍ**.

> **Resultado:** En la carpeta `Output` (dentro de donde guardaste el script) aparecerá un archivo `mysetup.exe`. ¡Ese es tu instalador final!

---

> [!WARNING]
> Si usas MasOS:
>
> 1. Crea una carpeta nueva en el escritorio llamada `Installer`.
> 2. Mete dentro tu nueva `SystemInfo.app`.
> 3. Crea un enlace simbólico (alias) a la carpeta de Aplicaciones (opcional, pero profesional) para facilitar al usuario arrastrar la app.
> 4. Abre la **Utilidad de Discos** (Disk Utility, viene instalada en Mac).
> 5. Ve a `Archivo > Nueva imagen > Imagen de la carpeta...`
> 6. Selecciona la carpeta `Installer`.
> 7. Guárdala como `SystemInfo_Installer.dmg`.

---

### EJERCICIO 4. Firma Digital (Concepto)

**Cometido:**
Como vimos en el Tema 11 (apartado 9), para garantizar la integridad, debemos firmar el código. Vamos a simular este proceso firmando nuestro JAR original.

**Pasos:**

1. Abre una terminal (CMD o PowerShell) y navega hasta tu carpeta `Deploy`.
2. **Crear un almacén de claves (Keystore) autofirmado:**
Ejecuta el siguiente comando (necesitas tener el JDK instalado y en el PATH):

```bash
keytool -genkey -alias miFirma -keyalg RSA -keystore miAlmacen.jks -validity 365

```

*(Si no encuentra `keytool`  ni `jarsigner` en el PATH, en powershell)*

```bash
& "C:\Program Files\Java\jdk-21\bin\keytool.exe" -genkeypair -alias miFirma -keyalg RSA -keystore miAlmacen.jks -validity 365
```

*Te pedirá una contraseña y datos personales. Rellénalos.*
3. **Firmar el JAR:**
Ahora usaremos `jarsigner` para estampar la firma en el jar:

```bash
jarsigner -keystore miAlmacen.jks -storepass tu_contraseña app.jar miFirma
```

* *(Sustituye `tu_contraseña` por la que pusiste en el paso anterior).*
* *(Si no encuentra `keytool`  ni `jarsigner` en el PATH, haz como en el paso anterior)*

4. **Verificar:**

Comprueba que está firmado con:

```bash
jarsigner -verify -verbose -certs app.jar
```

Deberías ver una salida que indica `jar verified`.

---

## 4. Documentación a entregar

Genera un documento PDF que incluya las siguientes evidencias visuales y respuestas:

1. **Evidencia JAR:** Captura de pantalla de Eclipse mostrando la estructura del proyecto (carpeta `res`) y captura de la carpeta `Deploy` con el `app.jar` generado.
2. **Evidencia Launch4j:** Captura de la configuración de Launch4j donde se vea la ruta del JAR y la selección del icono.
3. **Evidencia Instalador:**

    * Captura del asistente de instalación (el "Wizard") de Inno Setup ejecutándose.
    * Captura de tu escritorio con el **Acceso Directo** creado automáticamente tras la instalación.

4. **Evidencia Firma:** Captura de la consola tras ejecutar el comando de verificación (`jarsigner -verify`) mostrando que el jar está verificado.
5. **Reflexión:**
    * ¿Por qué crees que es preferible distribuir un `.exe` (que internamente lanza Java) en lugar de dar directamente el `.jar` al cliente final?
    * Explica brevemente qué ocurriría si un hacker modificara tu `app.jar` después de haberlo firmado digitalmente en el Ejercicio 4.

## 5. Entrega

Sube a la plataforma **únicamente el archivo PDF** con las capturas y respuestas.
