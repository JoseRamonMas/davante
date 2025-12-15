# Tema 10. Documentación de aplicaciones: Ayudas

**Tarea 1: "Implementación paso a paso de un sistema de ayuda JavaHelp"**

## 1. Introducción

En el desarrollo de software profesional, una aplicación no está terminada hasta que está documentada. El usuario final rara vez tiene conocimientos técnicos, y una buena ayuda contextual puede marcar la diferencia entre una aplicación exitosa y una abandonada.

En esta unidad vamos a implementar un sistema de ayuda profesional utilizando **JavaHelp**. A diferencia de los simples `ToolTips` o mensajes en consola, JavaHelp permite crear un sistema de documentación navegable, con tabla de contenidos jerárquica, índice y búsquedas, totalmente integrado en tu aplicación Java Swing.

Aunque el código Java necesario no es muy extenso, la complejidad radica en la **arquitectura de ficheros**: necesitaremos coordinar archivos HTML, XML y el código fuente. En esta tarea crearás un sistema de ayuda robusto con múltiples secciones y navegación anidada.

## 2. Objetivos de aprendizaje

Al finalizar esta práctica guiada, deberás ser capaz de:

  * Entender y replicar la estructura de directorios necesaria para un proyecto JavaHelp.
  * Crear y configurar los ficheros de mapeo (`map.jhm`), tabla de contenidos (`toc.xml`) y el descriptor (`helpset.hs`).
  * Diseñar temas de ayuda en formato HTML organizados por funcionalidad.
  * Integrar la ayuda en una aplicación Swing compleja con múltiples pestañas.
  * Implementar ayuda sensible al contexto (tecla F1) diferenciada para cada sección de la aplicación.

-----

## 3. Desarrollo de la práctica

Vamos a crear una aplicación ficticia llamada **"InventoryManager"**, una herramienta para gestionar el stock de una tienda y su cartera de proveedores. Implementaremos su interfaz completa y, posteriormente, todo su sistema de ayuda detallado.

### EJERCICIO 1. Preparación del entorno y UI completa

**Cometido:**
Configurar el proyecto en Eclipse y crear la interfaz gráfica base. Esta vez la interfaz será más densa, incluyendo gestión de productos y proveedores, para justificar una ayuda más extensa.

**Pasos:**

1.  **Crear Proyecto:** Crea un proyecto Java en Eclipse llamado `InventoryManager_TuNombre`.
2.  **Importar Librerías:**
      * Necesitas la librería de JavaHelp (`jhall.jar`). Añádela al `Build Path` de tu proyecto (Botón derecho en proyecto > Build Path > Configure Build Path > Libraries > Classpath > Add External JARs).
3.  **Código de la Interfaz:**
    Crea una clase llamada `MainWindow.java` y pega el siguiente código. Es una interfaz con Menú y 3 pestañas funcionales.

<!-- end list -->

```java
import java.awt.EventQueue;
import javax.swing.*;
import java.awt.BorderLayout;

public class MainWindow {

    private JFrame frame;
    // Componentes pestaña Productos
    private JTextField txtNombreProducto;
    private JTextField txtStock;
    private JButton btnGuardarProducto;
    
    // Componentes pestaña Proveedores
    private JTextField txtNombreProv;
    private JTextField txtEmailProv;
    private JButton btnGuardarProv;
    
    // Menu
    private JMenuItem mntmAyuda; 
    private JMenuItem mntmSoporte;
    
    private JTabbedPane tabbedPane;

    public static void main(String[] args) {
        EventQueue.invokeLater(() -> {
            try {
                MainWindow window = new MainWindow();
                window.frame.setVisible(true);
            } catch (Exception e) {
                e.printStackTrace();
            }
        });
    }

    public MainWindow() {
        initialize();
        // AQUI LLAMAREMOS AL METODO DE AYUDA MAS ADELANTE
    }

    private void initialize() {
        frame = new JFrame();
        frame.setBounds(100, 100, 600, 450);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setTitle("Inventory Manager v2.0 Enterprise");

        // --- MENU ---
        JMenuBar menuBar = new JMenuBar();
        frame.setJMenuBar(menuBar);

        JMenu mnArchivo = new JMenu("Archivo");
        menuBar.add(mnArchivo);
        JMenuItem mntmSalir = new JMenuItem("Salir");
        mnArchivo.add(mntmSalir);

        JMenu mnAyuda = new JMenu("Ayuda");
        menuBar.add(mnAyuda);
        mntmAyuda = new JMenuItem("Contenido de Ayuda");
        mnAyuda.add(mntmAyuda);
        mntmSoporte = new JMenuItem("Soporte Técnico Online");
        mnAyuda.add(mntmSoporte);

        // --- PESTAÑAS ---
        tabbedPane = new JTabbedPane(JTabbedPane.TOP);
        frame.getContentPane().add(tabbedPane, BorderLayout.CENTER);

        // Pestaña 1: Alta de Productos
        JPanel panelProductos = new JPanel();
        panelProductos.setLayout(null);
        tabbedPane.addTab("Productos", null, panelProductos, "Gestión de Stock");

        JLabel lblNombre = new JLabel("Producto:");
        lblNombre.setBounds(20, 30, 120, 20);
        panelProductos.add(lblNombre);

        txtNombreProducto = new JTextField();
        txtNombreProducto.setBounds(100, 30, 200, 20);
        panelProductos.add(txtNombreProducto);

        JLabel lblStock = new JLabel("Unidades:");
        lblStock.setBounds(20, 70, 120, 20);
        panelProductos.add(lblStock);

        txtStock = new JTextField();
        txtStock.setBounds(100, 70, 80, 20);
        panelProductos.add(txtStock);

        btnGuardarProducto = new JButton("Registrar Stock");
        btnGuardarProducto.setBounds(100, 120, 150, 30);
        panelProductos.add(btnGuardarProducto);

        // Pestaña 2: Proveedores
        JPanel panelProveedores = new JPanel();
        panelProveedores.setLayout(null);
        tabbedPane.addTab("Proveedores", null, panelProveedores, "Gestión de Partners");
        
        JLabel lblProv = new JLabel("Empresa:");
        lblProv.setBounds(20, 30, 120, 20);
        panelProveedores.add(lblProv);
        
        txtNombreProv = new JTextField();
        txtNombreProv.setBounds(100, 30, 200, 20);
        panelProveedores.add(txtNombreProv);
        
        JLabel lblEmail = new JLabel("Email:");
        lblEmail.setBounds(20, 70, 120, 20);
        panelProveedores.add(lblEmail);
        
        txtEmailProv = new JTextField();
        txtEmailProv.setBounds(100, 70, 200, 20);
        panelProveedores.add(txtEmailProv);
        
        btnGuardarProv = new JButton("Guardar Proveedor");
        btnGuardarProv.setBounds(100, 120, 180, 30);
        panelProveedores.add(btnGuardarProv);

        // Pestaña 3: Listados
        JPanel panelListado = new JPanel();
        panelListado.add(new JLabel("Consulta global del sistema..."));
        tabbedPane.addTab("Informes", null, panelListado, "Ver estadísticas");
    }
    
    // Método cargarAyuda() pendiente de implementar
}
```

-----

### EJERCICIO 2. Creación del contenido extendido (HTML)

**Cometido:**
Necesitamos crear una documentación robusta. Vamos a generar 5 archivos HTML para cubrir las distintas secciones.

**Pasos:**

1.  En la raíz de tu proyecto, crea una carpeta llamada `help`.

2.  Dentro de `help`, crea una carpeta llamada `html`.

3.  Crea los siguientes archivos HTML dentro de `html` con un contenido básico pero descriptivo:

      * `main.html`: **Bienvenida**. Debe explicar que es una app de gestión integral.
      * `productos.html`: **Gestión de Stock**. Explica qué es el campo "Unidades" y cómo registrar un producto.
      * `proveedores.html`: **Gestión de Partners**. Explica que es obligatorio introducir un Email válido para dar de alta un proveedor.
      * `informes.html`: **Visualización**. Explica brevemente qué datos se muestran en la pestaña de informes.
      * `faq.html`: **Preguntas Frecuentes**. Crea una lista con 2 o 3 preguntas ficticias (ej: "¿Cómo recupero mi contraseña?", "¿Cómo hago una copia de seguridad?").

> **Nota:** Puedes usar un editor externo (Bloc de notas, VS Code) o el editor de texto de Eclipse. Lo importante es que el contenido sea coherente.

-----

### EJERCICIO 3. Configuración avanzada de javaHelp (XML)

**Cometido:**
Vamos a conectar todas las piezas. Crearemos los ficheros de configuración necesarios para que Java reconozca nuestra estructura de ayuda.

#### 3.1. Fichero de mapeo (`map.jhm`)

Asociaremos un ID único a cada archivo HTML.

Crea `help/map.jhm`:

```xml
<?xml version='1.0' encoding='ISO-8859-1'?>
<!DOCTYPE map
PUBLIC "-//Sun Microsystems Inc.//DTD JavaHelp Map Version 2.0//EN"
"http://java.sun.com/products/javahelp/map_2_0.dtd">
<map version="1.0">
   <mapID target="inicio" url="html/main.html" />
   <mapID target="seccion_productos" url="html/productos.html" />
   <mapID target="seccion_proveedores" url="html/proveedores.html" />
   <mapID target="seccion_informes" url="html/informes.html" />
   <mapID target="seccion_faq" url="html/faq.html" />
</map>
```

#### 3.2. Tabla de contenidos jerárquica (`toc.xml`)

Aquí definiremos el árbol de navegación. Queremos que "Productos", "Proveedores" e "Informes" aparezcan anidados dentro de una categoría llamada "Manual de Usuario", y las FAQ vayan aparte.

Crea `help/toc.xml`:

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE toc
  PUBLIC "-//Sun Microsystems Inc.//DTD JavaHelp TOC Version 1.0//EN"
  "http://java.sun.com/products/javahelp/toc_1_0.dtd">
<toc version="1.0">
   <tocitem text="Ayuda Inventory Manager" target="inicio">
      
       <tocitem text="Manual de Usuario" target="inicio">
          <tocitem text="Gestión de Productos" target="seccion_productos"/>
          <tocitem text="Gestión de Proveedores" target="seccion_proveedores"/>
          <tocitem text="Ver Informes" target="seccion_informes"/>
      </tocitem>

      <tocitem text="Preguntas Frecuentes (FAQ)" target="seccion_faq"/>
      
   </tocitem>
</toc>
```

#### 3.3. Archivo del HelpSet (`helpset.hs`)

El archivo maestro que unifica mapa y vista.

Crea `help/helpset.hs`:

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE helpset
  PUBLIC "-//Sun Microsystems Inc.//DTD JavaHelp HelpSet Version 1.0//EN"
  "http://java.sun.com/products/javahelp/helpset_1_0.dtd">
<helpset version="1.0">
   <title>Ayuda Inventory Manager</title>
   <maps>
      <homeID>inicio</homeID>
      <mapref location="map.jhm"/>
   </maps>

   <view>
      <name>Tabla Contenidos</name>
      <label>Tabla de contenidos</label>
      <type>javax.help.TOCView</type>
      <data>toc.xml</data>
   </view>
</helpset>
```

-----

### EJERCICIO 4. Integración sensible al contexto (Java)

**Cometido:**
Ahora conectaremos la aplicación. Queremos que la tecla **F1** sea inteligente: si el usuario está escribiendo un email de proveedor y pulsa F1, la ayuda debe abrirse directamente en la página de proveedores, no en la de inicio.

**Pasos:**

1.  Vuelve a `MainWindow.java`.

2.  Importa las clases:

    ```java
    import java.io.File;
    import java.net.URL;
    import javax.help.HelpBroker;
    import javax.help.HelpSet;
    ```

3.  Implementa el método `cargarAyuda()` dentro de la clase `MainWindow`. Copia y estudia cómo se asignan los distintos IDs:

    ```java
    private void cargarAyuda() {
        try {
            // 1. Cargar el fichero de configuración
            File fichero = new File("help/helpset.hs");
            URL hsURL = fichero.toURI().toURL();

            // 2. Crear el HelpBroker
            HelpSet helpset = new HelpSet(getClass().getClassLoader(), hsURL);
            HelpBroker hb = helpset.createHelpBroker();

            // 3. Ayuda General (Menú y F1 global)
            hb.enableHelpOnButton(mntmAyuda, "inicio", helpset);
            hb.enableHelpKey(frame.getRootPane(), "inicio", helpset);
            
            // 4. Ayuda Contextual - Pestaña Productos
            // Al pulsar F1 sobre el campo nombre o el botón guardar, va a productos.html
            hb.enableHelpKey(txtNombreProducto, "seccion_productos", helpset);
            hb.enableHelpKey(btnGuardarProducto, "seccion_productos", helpset);

            // 5. Ayuda Contextual - Pestaña Proveedores
            // Al pulsar F1 sobre el email o el botón proveedor, va a proveedores.html
            hb.enableHelpKey(txtEmailProv, "seccion_proveedores", helpset);
            hb.enableHelpKey(btnGuardarProv, "seccion_proveedores", helpset);
            
            // 6. Ayuda para Soporte (Menú secundario)
            // Vinculamos el item de menú "Soporte" a la página de FAQ
            hb.enableHelpOnButton(mntmSoporte, "seccion_faq", helpset);

        } catch (Exception e) {
            e.printStackTrace();
            JOptionPane.showMessageDialog(frame, "Error al cargar la ayuda: " + e.getMessage());
        }
    }
    ```

4.  **Importante:** Llama al método al final del constructor:

    ```java
    public MainWindow() {
        initialize();
        cargarAyuda(); // <--- IMPRESCINDIBLE
    }
    ```

5.  **Verificación:** Ejecuta la app.

      * Ve a la pestaña **Proveedores**. Haz clic en el campo "Email". Pulsa **F1**. ¿Se abre la sección de Proveedores?
      * Pulsa en el menú **Ayuda > Soporte Técnico**. ¿Se abre la sección de FAQ?

-----

## 4. Documentación a entregar

Genera un documento PDF que incluya las siguientes evidencias visuales y respuestas:

1.  **Estructura del proyecto:** Captura del explorador de Eclipse mostrando la carpeta `help` desplegada con los 3 XML y la subcarpeta `html` con los 5 archivos HTML.
2.  **Código XML:** Captura legible del contenido de `toc.xml` donde se aprecie la estructura anidada (la carpeta "Manual de Usuario").
3.  **Prueba de contexto (Productos):** Captura de pantalla pulsando F1 mientras el foco está en el botón "Registrar Stock", mostrando que la ayuda se abre en `productos.html`.
4.  **Prueba de contexto (Proveedores):** Captura de pantalla pulsando F1 mientras el foco está en el campo "Email", mostrando que la ayuda se abre en `proveedores.html`.
5.  **Prueba de ,enú:** Captura tras pulsar el ítem de menú "Soporte Técnico", mostrando la página de `faq.html`.
6.  **Reflexión:**
      * Explica con tus palabras la diferencia entre `enableHelpOnButton` y `enableHelpKey`.
      * ¿Por qué crees que es útil agrupar items en el `toc.xml` (como hicimos con "Manual de Usuario") en lugar de ponerlos todos en una lista plana?

## 5. Entrega

Sube a la plataforma **únicamente el archivo PDF** con las capturas y respuestas.