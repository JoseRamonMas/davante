# Tema 10. Documentación de aplicaciones: Ayudas

**Tarea 2: "Ingeniería documental con MkDocs Material e integración completa en Swing".**

## 1. Introducción y contexto profesional

En las prácticas anteriores has trabajado con **JavaHelp**, el estándar clásico de Java. Sin embargo, el desarrollo de software actual ha migrado hacia soluciones más ágiles, estéticas y basadas en tecnologías web.

Las empresas de software actuales (Google, Microsoft, Netflix) no escriben documentación en Word ni en herramientas propietarias. Utilizan la filosofía **"Docs as Code"** (Documentación como Código). Esto significa:

1. Se escribe en texto plano (**Markdown**).
2. Se gestiona con control de versiones.
3. Se compila y genera una web estática (**Static Site Generator**).

En esta práctica vas a montar todo este ecosistema utilizando **MkDocs** con el tema **Material** (el más usado del mundo) y lo integrarás en una aplicación Java Swing, no solo para abrir la ayuda, sino para hacerlo de forma **contextual** e inteligente.

---

## 2. Fase 1: Preparación del entorno

Para usar herramientas modernas, necesitamos preparar el sistema operativo. MkDocs se basa en Python. No necesitas saber programar en Python, pero sí necesitas tener su "motor" instalado.

### Paso 1.1: Instalación de Python

**Opción A: Estoy en Windows**

1. Entra en [python.org/downloads](https://www.python.org/downloads/).
2. Descarga el instalador "Windows Installer (64-bit)".
3. **CRÍTICO:** Al ejecutarlo, en la primera pantalla, marca la casilla inferior que dice: **`Add Python to PATH`**.
* *¿Por qué?* Si no lo haces, Windows no sabrá qué es "python" cuando lo escribas en la consola y la práctica fallará.


4. Dale a "Install Now".

**Opción B: Estoy en MacOS**

1. Abre la Terminal (Comando + Espacio -> Terminal).
2. Instala el gestor de paquetes Homebrew (si no lo tienes):
`/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`
3. Instala Python 3:
`brew install python`

### Paso 1.2: Verificación de la instalación

Abre una **nueva** terminal (PowerShell en Windows o Terminal en Mac) y ejecuta estos dos comandos. Si alguno da error, repite el paso anterior.

```bash
python --version
# Debería decir algo como Python 3.10.x o superior

pip --version
# Pip es el instalador de librerías de Python. Debe aparecer una versión.

```

### Paso 1.3: Instalación de la suite de documentación

Vamos a instalar MkDocs y el tema Material. Además, instalaremos plugins para mejorar la búsqueda. Ejecuta en tu terminal:

```bash
pip install mkdocs-material

```

*(Nota para usuarios de Mac: si `pip` falla, prueba usando `pip3`)*.

---

## 3. Fase 2: Arquitectura del proyecto de ayuda

Vamos a crear la documentación para una aplicación ficticia llamada "**GymManager Pro**".

No vamos a mezclar la documentación con el código Java todavía. Primero crearemos el sitio web de ayuda.

1. Crea una carpeta en tu escritorio llamada `Practica_Ayuda_Moderna`.
2. Abre la terminal dentro de esa carpeta.
3. Ejecuta el comando de inicialización:

    ```bash
    mkdocs new gym_docs
    ```

4. Entra en el directorio creado: `cd gym_docs`.

### Paso 2.1: Configuración avanzada (`mkdocs.yml`)

El archivo `mkdocs.yml` es el cerebro de tu documentación. Vamos a configurarlo con opciones avanzadas (colores, idioma, plugins de búsqueda y diagrama).

Abre `mkdocs.yml` con tu editor (VS Code, Notepad++, etc.), **borra todo** y pega esta configuración. **Lee los comentarios** para entender qué hace cada línea:

```yaml
site_name: GymManager Pro Docs
site_url: http://localhost:8000
site_author: Tu Nombre y Apellido
copyright: Copyright &copy; 2025 GymManager Systems

# Configuración visual del tema Material
theme:
  name: material
  language: es
  logo: assets/logo.png  # Más adelante crearemos esta carpeta
  favicon: assets/favicon.png
  palette:
    # Modo Claro (Día)
    - scheme: default
      primary: indigo
      accent: cyan
      toggle:
        icon: material/weather-sunny
        name: Cambiar a modo oscuro
    # Modo Oscuro (Noche)
    - scheme: slate
      primary: indigo
      accent: cyan
      toggle:
        icon: material/weather-night
        name: Cambiar a modo claro
  features:
    - navigation.tabs       # Menú superior en pestañas
    - navigation.top        # Botón para volver arriba
    - search.suggest        # Búsqueda inteligente
    - content.code.copy     # Botón copiar en bloques de código

# Plugins extra (funcionalidad avanzada)
markdown_extensions:
  - admonition              # Para notas de advertencia
  - pymdownx.details        # Para bloques desplegables
  - pymdownx.superfences:   # Para diagramas
      custom_fences:
        - name: mermaid
          class: mermaid
          format: !!python/name:pymdownx.superfences.fence_code_format

# Árbol de navegación (TOC)
nav:
  - Bienvenida: index.md
  - Módulo Socios:
    - Alta de Socio: socios/alta.md
    - Panel de Listado: socios/listado.md
  - Módulo Económico:
    - Tarifas Vigentes: economico/tarifas.md
    - Facturación: economico/facturacion.md
  - Guía Técnica:
    - Diagrama de Datos: tecnico/diagrama.md
    - Errores Comunes: tecnico/troubleshooting.md

```

---

## 4. Fase 3: Creación de contenido rico (markdown avanzado)

Ahora debes rellenar el contenido. MkDocs busca los archivos `.md` dentro de la carpeta `docs`.

### Paso 3.1: Estructura de carpetas

Dentro de la carpeta `docs`, crea las subcarpetas necesarias para que coincidan con tu `mkdocs.yml`:

* `docs/assets` (Aquí guardarás las imágenes)
* `docs/socios`
* `docs/economico`
* `docs/tecnico`

### Paso 3.2: Redacción del contenido

Debes crear los archivos `.md` con contenido real. A continuación, se especifican los requisitos obligatorios para cada archivo:

**A. Archivo `index.md` (Bienvenida)**
Debe contener un saludo y un bloque de "Novedades". Usa el componente **Admonition**:

```markdown
# Bienvenido a GymManager Pro

Esta es la documentación oficial de la versión 2.0. Utilice el menú superior para navegar por las distintas secciones o utilice la barra de búsqueda (Atajo: Tecla `S`).

!!! info "Novedades de la versión"
    Ahora es posible exportar los listados a PDF directamente desde el menú de socios.

## Requisitos del sistema
* Java JDK 17 o superior.
* Conexión a Internet para la sincronización de datos.

!!! warning "Importante"
    Recuerde realizar copias de seguridad semanales desde el módulo de **Administración**.
```

**B. Archivo `economico/tarifas.md` (Tablas)**
Debes crear una tabla de precios usando sintaxis Markdown.

* Requisito: Debe tener al menos 3 columnas (Tarifa, Horario, Precio) y 4 filas de datos.

**C. Archivo `tecnico/diagrama.md` (Diagramas como código)**
Aquí usaremos una característica avanzada: **Mermaid.js**. En lugar de pegar una imagen, "programaremos" el diagrama. Copia este código en tu archivo y observa la magia cuando lo visualices:

```markdown
    # Flujo de Datos

    Este es el proceso interno cuando se registra un usuario:

    ```mermaid
    graph TD
        A[Inicio: Usuario rellena formulario] --> B{¿Datos válidos?}
        B -- No --> C[Mostrar Error en Swing]
        B -- Sí --> D[Guardar en Base de Datos]
        D --> E[Enviar Email de Bienvenida]
        E --> F[Fin]
    ```
```

**D. Archivo `socios/alta.md` (Imágenes)**

* Abre Paint o cualquier herramienta para dibujar. Crea una imagen pequeña y guárdala como `logo.png` en la carpeta `docs/assets`.
* En este archivo markdown, inserta la imagen como se muestra a continuación:

```markdown
# Alta de nuevo socio

![Logotipo corporativo](../assets/logo.png) Para registrar un cliente, rellene el formulario principal.

!!! tip "Consejo Pro"
    Puede usar la tecla `Enter` para saltar entre campos rápidamente.

## Campos obligatorios

| Campo | Descripción | Restricciones |
| :--- | :--- | :--- |
| **Nombre** | Nombre completo | Mínimo 2 caracteres |
| **DNI** | Documento de identidad | Formato 12345678X |
| **Tarifa** | Plan de precios | Elegir del desplegable |

## Validación de errores
Si el sistema detecta un DNI duplicado, verá el siguiente error:
    ERROR: El socio con ID [X] ya existe en la base de datos
```

**E. Archivo `socios/reservas.md`**

En base a lo visto anteriormente, ahora crea este fichero explicando brevemente como reservar plaza. Puedes usar imagenes, tablas, admonitions...

### Paso 3.3: Visualización en tiempo real

Es hora de ver el resultado.

1. En la terminal, ejecuta: `mkdocs serve` (asegúrate de estar en la carpeta donde está `mkdocs.yml`)
2. Abre tu navegador en `http://127.0.0.1:8000`.
3. Navega por tu documentación. Prueba el botón de modo oscuro (arriba a la derecha) y verifica que el diagrama se ha dibujado correctamente.
4. **Mantén la terminal abierta.** Si la cierras, la web dejará de funcionar.

---

## 5. Fase 4: Desarrollo de la interfaz Swing (GUI)

Ahora vamos a Eclipse. Crea un nuevo proyecto Java llamado `GymHelpSystem`.

Vamos a simular una aplicación real. Copia el siguiente código para crear la ventana principal. Fíjate que hemos añadido nombres internos (`setName`) a los componentes para identificarlos si fuera necesario.

**Clase `MainWindow.java**`:

```java
import javax.swing.*;
import java.awt.*;

public class MainWindow extends JFrame {

    private JTabbedPane tabbedPane;
    private JButton btnAyudaGeneral;

    public MainWindow() {
        // Configuración básica de la ventana
        setTitle("GymManager v2.0 - Sistema de Gestión");
        setSize(800, 500);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLocationRelativeTo(null);
        setLayout(new BorderLayout());

        // 1. Barra de Herramientas Superior
        JToolBar toolbar = new JToolBar();
        btnAyudaGeneral = new JButton("Ayuda (?)");
        // Truco: Ponemos un borde llamativo al botón de ayuda
        btnAyudaGeneral.setBackground(new Color(255, 255, 200));
        toolbar.add(Box.createHorizontalGlue()); // Empujar a la derecha
        toolbar.add(btnAyudaGeneral);
        add(toolbar, BorderLayout.NORTH);

        // 2. Panel de Pestañas (El núcleo de la navegación)
        tabbedPane = new JTabbedPane();

        // Pestaña 1: SOCIOS
        JPanel pnlSocios = new JPanel(new FlowLayout());
        pnlSocios.add(new JLabel("Nombre del Socio: "));
        pnlSocios.add(new JTextField(20));
        pnlSocios.add(new JButton("Registrar"));
        tabbedPane.addTab("Gestión de Socios", pnlSocios);

        // Pestaña 2: RESERVAS
        JPanel pnlReservas = new JPanel(new FlowLayout());
        pnlReservas.add(new JLabel("Seleccione fecha: "));
        pnlReservas.add(new JTextField(20));
        pnlReservas.add(new JButton("Reservar"));
        tabbedPane.addTab("Gestión de Reservas", pnlReservas);

        // Pestaña 3: ECONÓMICO
        JPanel pnlEco = new JPanel(new FlowLayout());
        pnlEco.add(new JLabel("Seleccione Tarifa: "));
        String[] tarifas = {"Mañana", "Tarde", "Completa"};
        pnlEco.add(new JComboBox<>(tarifas));
        tabbedPane.addTab("Facturación y Tarifas", pnlEco);

        // Pestaña 4: TÉCNICO
        JPanel pnlTec = new JPanel(new FlowLayout());
        pnlTec.add(new JLabel("Estado del servidor: OK"));
        pnlTec.add(new JButton("Ver Logs"));
        tabbedPane.addTab("Soporte Técnico", pnlTec);

        add(tabbedPane, BorderLayout.CENTER);
        
        // Inicializamos la lógica de ayuda (Implementaremos esto en el siguiente paso)
        initHelpSystem();
    }

    private void initHelpSystem() {
        // TODO: Aquí conectaremos con MkDocs
    }

    public static void main(String[] args) {
        // Ejecutar en el hilo de despacho de eventos
        SwingUtilities.invokeLater(() -> new MainWindow().setVisible(true));
    }
}

```

---

## 6. Fase 5: Lógica de integración

A diferencia de JavaHelp, aquí no mostramos la ayuda dentro de Swing, sino que ordenamos al sistema operativo que abra el navegador predeterminado del usuario.

### Paso 5.1: Clase `HelpManager`

Crea una nueva clase `HelpManager.java`. Esta clase encapsulará la complejidad de llamar al sistema operativo.

```java
import java.awt.Desktop;
import java.net.URI;
import java.io.IOException;
import java.net.URISyntaxException;

public class HelpManager {
    
    // URL donde MkDocs está sirviendo la ayuda.
    // NOTA: En una app real, esto podría ser una URL pública en internet.
    private static final String BASE_URL = "http://127.0.0.1:8000/";

    /**
     * Abre el navegador por defecto en una página específica
     * @param section La subruta del archivo markdown (ej: "socios/alta/")
     */
    public static void showHelp(String section) {
        // Aseguramos que la sección no sea null
        if (section == null) section = "";
        
        String fullPath = BASE_URL + section;
        
        try {
            // Verificamos si el sistema soporta la clase Desktop
            if (Desktop.isDesktopSupported() && Desktop.getDesktop().isSupported(Desktop.Action.BROWSE)) {
                Desktop.getDesktop().browse(new URI(fullPath));
                System.out.println("Navegador abierto en: " + fullPath);
            } else {
                System.err.println("Error: El sistema operativo no soporta abrir navegador.");
            }
        } catch (IOException | URISyntaxException e) {
            e.printStackTrace();
            javax.swing.JOptionPane.showMessageDialog(null, 
                "No se pudo abrir la ayuda. Asegúrese de que el servidor MkDocs está corriendo.", 
                "Error de Ayuda", 
                javax.swing.JOptionPane.ERROR_MESSAGE);
        }
    }
}

```

### Paso 5.2: Detectar el contexto (F1)

Ahora viene la parte más importante: la **Ayuda Sensible al Contexto**. Queremos que al pulsar F1, el programa sepa qué pestaña está mirando el usuario.

Vuelve a `MainWindow.java` y sustituye el método `initHelpSystem` por este código completo:

```java
private void initHelpSystem() {
    
    // 1. Ayuda Global (Clic en botón)
    // Al hacer clic en el botón de la barra, vamos al índice general
    btnAyudaGeneral.addActionListener(e -> HelpManager.showHelp(""));

    // 2. Ayuda Contextual (Tecla F1)
    // Usamos KeyboardFocusManager para interceptar F1 en CUALQUIER lugar de la ventana
    KeyboardFocusManager.getCurrentKeyboardFocusManager().addKeyEventDispatcher(e -> {
        
        // Verificamos si la tecla es F1 y si es el evento de "pulsar" (para no disparar dos veces)
        if (e.getID() == java.awt.event.KeyEvent.KEY_PRESSED && e.getKeyCode() == java.awt.event.KeyEvent.VK_F1) {
            
            // Lógica de decisión: ¿Qué pestaña está seleccionada?
            int selectedTab = tabbedPane.getSelectedIndex();
            
            switch (selectedTab) {
                case 0: // Pestaña Socios
                    // Apuntamos a la carpeta 'socios/alta/' definida en MkDocs
                    HelpManager.showHelp("socios/alta/");
                    break;

                case 1: // Pestaña Reservas
                    // Apuntamos a la carpeta 'socios/reservas/' definida en MkDocs
                    HelpManager.showHelp("socios/reservas/");
                    break;
                    
                case 2: // Pestaña Económico
                    // Apuntamos a la carpeta 'economico/tarifas/'
                    HelpManager.showHelp("economico/tarifas/");
                    break;
                    
                case 3: // Pestaña Técnico
                    // Apuntamos al diagrama técnico
                    HelpManager.showHelp("tecnico/diagrama/");
                    break;
                    
                default:
                    HelpManager.showHelp(""); // Fallback al inicio
            }
            return true; // Consumimos el evento (para que no se propague más)
        }
        return false; // Dejamos pasar otras teclas
    });
}

```

---

## 7. Fase 6: Pruebas y generación estática

### Paso 6.1: Test de Integración

1. Asegúrate de que la terminal con `mkdocs serve` sigue abierta y sin errores.
2. Ejecuta tu aplicación Java.
3. Selecciona la pestaña **"Facturación y Tarifas"**. Pulsa **F1**.
    * *Resultado esperado:* Se abre el navegador automáticamente y muestra tu tabla de tarifas.
4. Selecciona la pestaña **"Soporte Técnico"**. Pulsa **F1**.
    * *Resultado esperado:* Se abre el navegador mostrando el Diagrama de Flujo que programaste con Mermaid.

### Paso 6.2: El concepto de "Build" (Construcción)

El comando `serve` es solo para desarrollo. Si quisieras entregar esta ayuda a un cliente para que la aloje en su servidor, debes "compilarla".

1. Cierra el servidor (Ctrl+C en la terminal).
2. Ejecuta el comando:

    ```bash
    mkdocs build
    ```

3. Observa tu carpeta de proyecto. Ha aparecido una carpeta nueva llamada **`site`**.
    * Esta carpeta `site` contiene solo HTML, CSS y JS puro. **Esto es lo que se subiría a internet**. Ya no necesita Python para funcionar, solo un servidor web.

---

## 8. Entregables

Para superar esta tarea, debes generar un PDF titulado `Apellido_Nombre_Tarea2_MkDocs.pdf` que contenga las siguientes **5 evidencias**:

1. **Captura de instalación:** Una captura de pantalla de tu terminal ejecutando `pip freeze` donde se vea instalado `mkdocs-material`.
2. **Captura de mermaid:** Una captura de tu navegador mostrando el **Diagrama de Flujo** visualizado correctamente dentro de la web de ayuda.
3. **Captura de código java:** Captura del método `initHelpSystem` en tu IDE donde se vea el `switch` que gestiona las pestañas.
4. **Prueba de contexto:** Una captura de pantalla completa de tu escritorio donde se vea, a la izquierda, la aplicación Java en la pestaña "Socios", y a la derecha el navegador abierto automáticamente en la página "Alta de Socios".
5. **Pregunta Teórica:**
    * Abre la carpeta `site` generada en la Fase 6. Haz doble clic en `index.html`. ¿Se ve bien la web o falla algo? Investiga y explica brevemente por qué los navegadores modernos bloquean ciertas funcionalidades (como la búsqueda) cuando abres un archivo HTML local (`file://`) en lugar de usar un servidor (`http://`)
