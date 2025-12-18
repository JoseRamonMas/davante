# Tema 10. Documentación de aplicaciones: Ayudas

**Tarea 2: "Ingeniería documental con MkDocs Material e integración completa en Swing".**

## 1. Introducción y contexto profesional

En las prácticas anteriores has trabajado con **JavaHelp**, el estándar clásico de Java. Sin embargo, el desarrollo de software actual ha migrado hacia soluciones más ágiles, estéticas y basadas en tecnologías web.

Las empresas modernas no escriben documentación en Word ni en herramientas propietarias. Utilizan la filosofía **"Docs as Code"** (Documentación como Código). Esto significa:

1. Se escribe en texto plano (**Markdown**).
2. Se gestiona con control de versiones.
3. Se compila y genera una web estática (**Static Site Generator**).

En esta práctica vas a montar todo este ecosistema utilizando **MkDocs** con el tema **Material** (el más usado del mundo) y lo integrarás en una aplicación Java Swing para la gestión de una biblioteca (**BiblioTech**), implementando una ayuda **contextual** e inteligente.

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

Vamos a crear la documentación para nuestra aplicación ficticia "**BiblioTech v3.0**".

No vamos a mezclar la documentación con el código Java todavía. Primero crearemos el sitio web de ayuda.

1. Crea una carpeta en tu escritorio llamada `Practica_Ayuda_Biblio`.
2. Abre la terminal dentro de esa carpeta.
3. Ejecuta el comando de inicialización:
```bash
mkdocs new biblio_docs

```


4. Entra en el directorio creado: `cd biblio_docs`.

### Paso 2.1: Configuración avanzada (`mkdocs.yml`)

El archivo `mkdocs.yml` es el cerebro de tu documentación. Vamos a configurarlo con opciones avanzadas (colores, idioma, plugins de búsqueda y diagrama).

Abre `mkdocs.yml` con tu editor (VS Code, Notepad++, etc.), **borra todo** y pega esta configuración. **Lee los comentarios** para entender qué hace cada línea:

```yaml
site_name: BiblioTech Docs
site_url: http://localhost:8000
site_author: Tu Nombre y Apellido
copyright: Copyright &copy; 2025 BiblioTech Systems

# Configuración visual del tema Material
theme:
  name: material
  language: es
  logo: assets/logo.png  # Más adelante crearemos esta carpeta
  favicon: assets/favicon.png
  palette:
    # Modo Claro (Día)
    - scheme: default
      primary: teal    # Color verde azulado para biblioteca
      accent: amber
      toggle:
        icon: material/weather-sunny
        name: Cambiar a modo oscuro
    # Modo Oscuro (Noche)
    - scheme: slate
      primary: teal
      accent: amber
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
  - Gestión Lectores:
    - Alta de Lector: lectores/alta.md
    - Carnet Digital: lectores/carnet.md
  - Préstamos y Catálogo:
    - Política de Préstamos: prestamos/politica.md
    - Sanciones por Retraso: prestamos/sanciones.md
  - Guía Técnica:
    - Diagrama de Búsqueda: tecnico/diagrama.md
    - Errores de Conexión: tecnico/troubleshooting.md

```

---

## 4. Fase 3: Creación de contenido rico (markdown avanzado)

Ahora debes rellenar el contenido. MkDocs busca los archivos `.md` dentro de la carpeta `docs`.

### Paso 3.1: Estructura de carpetas

Dentro de la carpeta `docs`, crea las subcarpetas necesarias para que coincidan con tu `mkdocs.yml`:

* `docs/assets` (Aquí guardarás las imágenes)
* `docs/lectores`
* `docs/prestamos`
* `docs/tecnico`

### Paso 3.2: Redacción del contenido

Debes crear los archivos `.md` con contenido real. A continuación, se especifican los requisitos obligatorios para cada archivo:

**A. Archivo `index.md` (Bienvenida)**
Debe contener un saludo y un bloque de "Novedades". Usa el componente **Admonition**:

```markdown
# Bienvenido a BiblioTech v3.0

Esta es la documentación oficial del sistema de gestión bibliotecaria. Utilice el menú superior para navegar por las secciones o pulse `S` para buscar.

!!! info "Novedades de la versión"
    Se ha integrado la conexión con la base de datos nacional de ISBN. Ahora las fichas se autocompletan.

## Requisitos del sistema
* Java JDK 17 o superior.
* Lector de códigos de barras USB (opcional).

!!! warning "Importante"
    El proceso de inventario anual bloquea los préstamos. Realícelo fuera del horario de apertura.

```

**B. Archivo `prestamos/sanciones.md` (Tablas)**
Debes crear una tabla de sanciones (multas) usando sintaxis Markdown.

* **Requisito:** Debe tener al menos 3 columnas (Tipo de Material, Días de Gracia, Sanción Diaria) y 4 filas de datos (Libros, Revistas, DVDs, E-Readers).

**C. Archivo `tecnico/diagrama.md` (Diagramas como código)**
Aquí usaremos **Mermaid.js**. Copia este código para visualizar el flujo de préstamo:

```markdown
    # Flujo de Préstamo de Material

    Este es el proceso lógico cuando un usuario solicita llevarse un libro:

    ```mermaid
    graph TD
        A[Inicio: Escanear Carnet] --> B{¿Usuario Sancionado?}
        B -- Sí --> C[BLOQUEAR: Mostrar Alerta Roja]
        B -- No --> D[Escanear Código Libro]
        D --> E{¿Stock > 0?}
        E -- No --> F[Sugerir Reserva]
        E -- Sí --> G[Registrar Salida en BD]
        G --> H[Imprimir Ticket]
    ```

```

**D. Archivo `lectores/alta.md` (Imágenes)**

* Abre Paint o cualquier herramienta. Crea una imagen pequeña (un icono de un libro o usuario) y guárdala como `logo.png` en `docs/assets`.
* En este archivo markdown, inserta la imagen:

```markdown
# Alta de nuevo Lector

![Icono Sistema](../assets/logo.png) Para dar de alta un nuevo usuario en la biblioteca, acceda al formulario principal.

!!! tip "Consejo Pro"
    Si el usuario es estudiante universitario, marque la casilla "Bonificación" para extender sus préstamos automáticamente.

## Campos obligatorios

| Campo | Descripción | Formato |
| :--- | :--- | :--- |
| **Nombre** | Nombre completo | Texto |
| **DNI/NIE** | Documento oficial | 12345678X |
| **Categoría** | Tipo de lector | Desplegable (Infantil/Adulto/Investigador) |

## Validación de errores
Si el DNI ya tiene un carnet asociado:
    ERROR: El usuario con ID [X] ya está registrado en la sucursal Norte.

```

**E. Archivo `lectores/carnet.md**`

En base a lo visto anteriormente, crea este fichero explicando brevemente cómo generar o renovar el carnet digital. Puedes usar imágenes, tablas, o admonitions a tu gusto.

### Paso 3.3: Visualización en tiempo real

Es hora de ver el resultado.

1. En la terminal: `mkdocs serve` (desde la carpeta donde está `mkdocs.yml`).
2. Abre tu navegador en `http://127.0.0.1:8000`.
3. Navega por tu documentación. Comprueba que el color principal es "Teal" (verde azulado) y que el diagrama Mermaid se dibuja bien.
4. **Mantén la terminal abierta.**

---

## 5. Fase 4: Desarrollo de la interfaz Swing (GUI)

Ahora vamos a Eclipse. Crea un nuevo proyecto Java llamado `BiblioHelpSystem`.

Copia el siguiente código para crear la ventana principal.

**Clase `MainWindow.java**`:

```java
import javax.swing.*;
import java.awt.*;

public class MainWindow extends JFrame {

    private JTabbedPane tabbedPane;
    private JButton btnAyudaGeneral;

    public MainWindow() {
        // Configuración básica de la ventana
        setTitle("BiblioTech v3.0 - Gestión Bibliotecaria");
        setSize(800, 500);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLocationRelativeTo(null);
        setLayout(new BorderLayout());

        // 1. Barra de Herramientas Superior
        JToolBar toolbar = new JToolBar();
        btnAyudaGeneral = new JButton("Ayuda (?)");
        btnAyudaGeneral.setBackground(new Color(200, 255, 200)); // Un verde suave
        toolbar.add(Box.createHorizontalGlue()); 
        toolbar.add(btnAyudaGeneral);
        add(toolbar, BorderLayout.NORTH);

        // 2. Panel de Pestañas (El núcleo de la navegación)
        tabbedPane = new JTabbedPane();

        // Pestaña 1: LECTORES
        JPanel pnlLectores = new JPanel(new FlowLayout());
        pnlLectores.add(new JLabel("DNI del Lector: "));
        pnlLectores.add(new JTextField(15));
        pnlLectores.add(new JButton("Buscar Ficha"));
        tabbedPane.addTab("Gestión de Lectores", pnlLectores);

        // Pestaña 2: CARNET DIGITAL (Simulado)
        JPanel pnlCarnet = new JPanel(new FlowLayout());
        pnlCarnet.add(new JLabel("Escanear QR Carnet: "));
        pnlCarnet.add(new JTextField(20));
        pnlCarnet.add(new JButton("Validar Acceso"));
        tabbedPane.addTab("Carnet Digital", pnlCarnet);

        // Pestaña 3: PRÉSTAMOS Y SANCIONES
        JPanel pnlPrestamos = new JPanel(new FlowLayout());
        pnlPrestamos.add(new JLabel("ISBN Libro: "));
        pnlPrestamos.add(new JTextField(15));
        String[] acciones = {"Prestar", "Devolver", "Sancionar"};
        pnlPrestamos.add(new JComboBox<>(acciones));
        tabbedPane.addTab("Préstamos y Sanciones", pnlPrestamos);

        // Pestaña 4: TÉCNICO
        JPanel pnlTec = new JPanel(new FlowLayout());
        pnlTec.add(new JLabel("Conexión al servidor ISBN: ESTABLE"));
        pnlTec.add(new JButton("Reiniciar Conexión"));
        tabbedPane.addTab("Soporte Técnico", pnlTec);

        add(tabbedPane, BorderLayout.CENTER);
        
        // Inicializamos la lógica de ayuda
        initHelpSystem();
    }

    private void initHelpSystem() {
        // TODO: Aquí conectaremos con MkDocs
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> new MainWindow().setVisible(true));
    }
}

```

---

## 6. Fase 5: Lógica de integración

Aquí ordenamos al sistema operativo que abra el navegador con la página correcta.

### Paso 5.1: Clase `HelpManager`

Crea una nueva clase `HelpManager.java`.

```java
import java.awt.Desktop;
import java.net.URI;
import java.io.IOException;
import java.net.URISyntaxException;

public class HelpManager {
    
    // URL donde MkDocs está sirviendo la ayuda.
    private static final String BASE_URL = "http://127.0.0.1:8000/";

    /**
     * Abre el navegador por defecto en una página específica
     * @param section La subruta del archivo markdown (ej: "lectores/alta/")
     */
    public static void showHelp(String section) {
        if (section == null) section = "";
        
        String fullPath = BASE_URL + section;
        
        try {
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

Implementamos la **Ayuda Sensible al Contexto** en `MainWindow.java`. Sustituye el método `initHelpSystem`:

```java
private void initHelpSystem() {
    
    // 1. Ayuda Global (Clic en botón)
    btnAyudaGeneral.addActionListener(e -> HelpManager.showHelp(""));

    // 2. Ayuda Contextual (Tecla F1)
    KeyboardFocusManager.getCurrentKeyboardFocusManager().addKeyEventDispatcher(e -> {
        
        if (e.getID() == java.awt.event.KeyEvent.KEY_PRESSED && e.getKeyCode() == java.awt.event.KeyEvent.VK_F1) {
            
            // ¿Qué pestaña está seleccionada?
            int selectedTab = tabbedPane.getSelectedIndex();
            
            switch (selectedTab) {
                case 0: // Pestaña Lectores
                    // Apuntamos a la documentación de Alta de Lector
                    HelpManager.showHelp("lectores/alta/");
                    break;

                case 1: // Pestaña Carnet Digital
                    // Apuntamos a la documentación del Carnet
                    HelpManager.showHelp("lectores/carnet/");
                    break;
                    
                case 2: // Pestaña Préstamos y Sanciones
                    // Apuntamos a la tabla de sanciones
                    HelpManager.showHelp("prestamos/sanciones/");
                    break;
                    
                case 3: // Pestaña Técnico
                    // Apuntamos al diagrama técnico
                    HelpManager.showHelp("tecnico/diagrama/");
                    break;
                    
                default:
                    HelpManager.showHelp(""); 
            }
            return true; // Consumimos el evento
        }
        return false; 
    });
}

```

---

## 7. Fase 6: Pruebas y generación estática

### Paso 6.1: Test de Integración

1. Asegúrate de que la terminal con `mkdocs serve` sigue abierta.
2. Ejecuta tu aplicación Java (`BiblioHelpSystem`).
3. Selecciona la pestaña **"Préstamos y Sanciones"**. Pulsa **F1**.
* *Resultado esperado:* Se abre el navegador en la página que contiene la Tabla de Sanciones.


4. Selecciona la pestaña **"Soporte Técnico"**. Pulsa **F1**.
* *Resultado esperado:* Se abre el navegador mostrando el Diagrama de Flujo del préstamo (Mermaid).



### Paso 6.2: Build (Construcción)

Para entregar la documentación final al cliente:

1. Cierra el servidor (Ctrl+C en la terminal).
2. Ejecuta:
```bash
mkdocs build

```


3. Verifica que se ha creado la carpeta **`site`**.

---

## 8. Entregables

Para superar esta tarea, genera un PDF titulado `Apellido_Nombre_Tarea2_MkDocs.pdf` con estas **5 evidencias**:

1. **Captura de instalación:** Terminal ejecutando `pip freeze` donde se vea `mkdocs-material`.
2. **Captura de mermaid:** Captura de tu navegador mostrando el **Diagrama de Flujo de Préstamo** visualizado correctamente.
3. **Captura de código java:** Captura del método `initHelpSystem` en tu IDE mostrando el `switch` para las pestañas de la biblioteca.
4. **Prueba de contexto:** Captura de pantalla completa del escritorio: a la izquierda la aplicación Java en la pestaña "**Gestión de Lectores**", y a la derecha el navegador abierto automáticamente en la página "**Alta de Lector**".
5. **Pregunta Teórica:**
* Abre la carpeta `site` generada en la Fase 6. Haz doble clic en `index.html`. ¿Se ve bien la web o falla algo? Investiga y explica brevemente por qué los navegadores bloquean funcionalidades como la búsqueda ("Search") cuando abres un archivo HTML local (`file://`) en lugar de usar un servidor (`http://`).
