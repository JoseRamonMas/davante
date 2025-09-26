# Tema 2 — Tareas guiadas (bloque de componentes)

**Nota común a todas las tareas (leer antes de empezar)**

1. Todas las tareas se realizan con **Eclipse + WindowBuilder** (Design: Palette / Structure / Properties). Usa la vista *Design* para modificar propiedades y renombrar variables en *Structure*. Solo edita *Source* cuando se indique.
2. **IDENTIFICACIÓN OBLIGATORIA:** en cada tarea, antes de ejecutar la aplicación, añade un `JLabel` llamado `lblAlumno` en el `JFrame` o `JDialog` principal y pon en su propiedad `text` TU **NOMBRE Y APELLIDOS** (ej. `María Pérez Gómez`). Ese `lblAlumno` debe ser visible en las capturas de entrega.

## Tarea 1 — `JFrame` + `JPanel` (interacción básica)

**Objetivo:** crear una ventana principal (`JFrame`) con `JPanel`, usar `BorderLayout`, añadir `JLabel`, `JTextField` y `JButton`; actualizar `JLabel` desde `JTextField`.

**Pasos:**

1. `File > New > Java Project` → `IF_PracticasSwing`.
2. `File > New > Other... > WindowBuilder > Swing Designer > JFrame` → nombre `MainWindow`. En *Properties* del `JFrame`: `defaultCloseOperation=EXIT_ON_CLOSE`, `resizable=false` (opcional), `bounds` `100,100,500,300`.
3. Arrastra un `JPanel` al `contentPane`. En *Properties* del panel: `layout` → `BorderLayout`.
4. NORTH: arrastra `JLabel` → renómbralo en *Structure* como `lblBienvenido`. *Properties*: `text=Bienvenido`, `horizontalAlignment=CENTER`. Si cambia `background`, marca `opaque=true`.
5. CENTER: arrastra un `JPanel` (subpanel) con `FlowLayout`. Dentro añade:

   * `JTextField` → `name=txtName`, `columns=20`, `toolTipText=Introduce tu nombre`.
   * `JButton` → `name=btnUpdate`, `text=Actualizar saludo`.
6. SOUTH: `JButton` → `name=btnExit`, `text=Salir`.
7. **OBLIGATORIO:** añade `JLabel lblAlumno` en un lugar visible y pon **tu NOMBRE Y APELLIDOS** en su `text`.
8. En *Design* haz doble clic en `btnUpdate` para añadir `ActionListener`. Pega el siguiente código en `actionPerformed`:

```java
String nombre = txtName.getText().trim();
if(nombre.isEmpty()) {
    JOptionPane.showMessageDialog(null, "Introduce un nombre");
} else {
    lblBienvenido.setText("Hola, " + nombre + "!");
}
```

9. `btnExit` → `actionPerformed` con `System.exit(0);`

**Capturas obligatorias (entregar):**

* A: Aplicación ejecutada mostrando claramente `lblAlumno` con tu nombre y apellidos.
* B: Vista *Structure* del diseñador mostrando `txtName`, `btnUpdate`, `lblBienvenido`, `lblAlumno`.

---

## Tarea 2 — `JDialog` modal y no-modal

**Objetivo:** crear un `JDialog` (constructor `(Frame parent, boolean modal)`), recoger datos y devolverlos al `JFrame` padre; comprobar modal vs no-modal.

**Pasos:**

1. `File > New > JDialog` → `NameInputDialog`. Cambia constructor a:

```java
public NameInputDialog(Frame parent, boolean modal) {
    super(parent, "Introduce tu nombre", modal);
    // initialize();
}
```

2. En el diálogo (Design): `JLabel` "Nombre:", `JTextField txtInput` (`columns=18`), `JButton btnOK`, `JButton btnCancel`.
3. Declara campo privado en clase:

```java
private String result = null;
public String getResult() { return result; }
```

4. `btnOK` `ActionListener`:

```java
result = txtInput.getText();
setVisible(false); // cierra el diálogo
```

`btnCancel`:

```java
result = null;
setVisible(false);
```

5. Desde `MainWindow` (o el `JFrame` padre) añade botón `Abrir diálogo` que haga:

```java
NameInputDialog dlg = new NameInputDialog(this, true); // modal
dlg.setLocationRelativeTo(this);
dlg.setVisible(true); // aquí bloquea mientras modal=true
String nombre = dlg.getResult();
if(nombre != null) {
    lblBienvenido.setText("Hola, " + nombre + "!");
}
```

6. **OBLIGATORIO:** en el `JFrame` padre tener `lblAlumno` con tu nombre visible.

**Capturas obligatorias:**

* A: Diálogo abierto (mostrar `txtInput`) y, en la misma o en otra captura, el `lblAlumno` visible en la ventana padre.
* B: Resultado tras aceptar: ventana padre mostrando el nombre recibido y `lblAlumno` visible.

---

## Tarea 3 — Conexión `JFrame` → `JFrame`: Login (usuario/contraseña)

**Objetivo:** implementar `LoginFrame` y `WelcomeFrame`. Uso de `JPasswordField`, validación simple, `dispose()` y `JOptionPane`.

**Pasos:**

1. Crea `LoginFrame` con `JLabel Usuario:`, `JTextField txtUser`, `JLabel Contraseña:`, `JPasswordField pwdPass`, `JButton btnInicio`, `JButton btnSalir`.
2. `btnInicio` `actionPerformed`:

```java
String user = txtUser.getText().trim();
String pwd = String.copyValueOf(pwdPass.getPassword());
if("admin".equals(user) && "1234".equals(pwd)) {
    WelcomeFrame wf = new WelcomeFrame(user);
    wf.setLocationRelativeTo(this);
    wf.setVisible(true);
    this.dispose();
} else {
    JOptionPane.showMessageDialog(this, "Usuario o contraseña incorrectos", "Error", JOptionPane.ERROR_MESSAGE);
    pwdPass.setText("");
    txtUser.requestFocus();
}
```

3. `WelcomeFrame` tiene `JLabel lblWelcome`. Constructor:

```java
public WelcomeFrame(String user) {
     // initialize();
    lblWelcome.setText("Bienvenido, " + user + "!");
}
```

4. **OBLIGATORIO:** añade `lblAlumno` (tu nombre/apellidos) en `LoginFrame` **y** en `WelcomeFrame` (ambas ventanas deben mostrarlo).

**Capturas obligatorias:**

* A: `LoginFrame` mostrando `lblAlumno` antes de iniciar sesión.
* B: `WelcomeFrame` abierto mostrando `Bienvenido, admin!` (o el user correcto) y `lblAlumno` visible.

---

## Tarea 4 — `JCheckBox` + `JRadioButton` (con `ButtonGroup`)

**Objetivo:** usar `JCheckBox` para estilos (negrita/cursiva/subrayado) y `JRadioButton` en `ButtonGroup` para tamaño; combinar y actualizar la visualización.

**Pasos:**

1. `OptionsFrame` con:

   * 3 `JCheckBox`: `chkBold`, `chkItalic`, `chkUnderline`.
   * 3 `JRadioButton`: `rdoSmall`, `rdoMedium`, `rdoLarge` (agrupados con `ButtonGroup`). Por defecto `rdoMedium.selected=true`.
   * `JLabel lblPreview` con `text="Texto de ejemplo"`, font tamaño 16 y `opaque=true`.
2. **OBLIGATORIO:** añade `lblAlumno` visible con tu nombre.
3. Implementa método `updatePreviewFont()`:

```java
private void updatePreviewFont() {
    int style = Font.PLAIN;
    if (chkBold.isSelected()) style |= Font.BOLD;
    if (chkItalic.isSelected()) style |= Font.ITALIC;
    int size = rdoSmall.isSelected() ? 12 : rdoMedium.isSelected() ? 16 : 20;
    lblPreview.setFont(new Font("SansSerif", style, size));
    if (chkUnderline.isSelected()) {
        lblPreview.setText("<html><u>Texto de ejemplo</u></html>");
    } else {
        lblPreview.setText("Texto de ejemplo");
    }
}
```

4. Añade `ItemListener`/`ActionListener` a los checkboxes y radios para llamar a `updatePreviewFont()`.

**Capturas obligatorias:**

* A: Aplicación mostrando `lblPreview` y `lblAlumno` (ambos visibles).
* B: Vista *Structure* mostrando `chkBold`, `chkItalic`, `chkUnderline`, `rdoSmall/rdoMedium/rdoLarge`, `lblPreview`, `lblAlumno`.

---

## Tarea 5 — `JComboBox` (editable) + `JSlider`

**Objetivo:** gestionar un `JComboBox` editable con `DefaultComboBoxModel` (añadir/eliminar ítems) y un `JSlider` (ticks/labels) para controlar el tamaño de una `JLabel`.

**Pasos:**

1. `ControlsFrame` con:

   * `JComboBox cmbFonts` (`editable=true`) — modelo inicial: `SansSerif, Serif, Monospaced, Arial, Verdana`.
   * `JSlider sldSize` propiedades: `minimum=8`, `maximum=48`, `value=16`, `majorTickSpacing=8`, `minorTickSpacing=2`, `paintTicks=true`, `paintLabels=true`.
   * `JLabel lblSample` (texto de ejemplo).
   * `JButton btnAddFont`, `JButton btnRemoveFont`.
2. **OBLIGATORIO:** `lblAlumno` con tu nombre visible.
3. Código (modelo y handlers):

```java
DefaultComboBoxModel<String> model = new DefaultComboBoxModel<>(new String[] {"SansSerif","Serif","Monospaced","Arial","Verdana"});
cmbFonts.setModel(model);

btnAddFont.addActionListener(e -> {
    String nuevo = JOptionPane.showInputDialog(this, "Nombre de fuente a añadir:");
    if(nuevo != null && !nuevo.trim().isEmpty()) model.addElement(nuevo.trim());
});
btnRemoveFont.addActionListener(e -> {
    Object sel = cmbFonts.getSelectedItem();
    if(sel != null) model.removeElement(sel);
});

cmbFonts.addActionListener(e -> updateLabelFont());
sldSize.addChangeListener(e -> updateLabelFont());

private void updateLabelFont() {
    String fontName = String.valueOf(cmbFonts.getSelectedItem());
    int size = sldSize.getValue();
    try {
        lblSample.setFont(new Font(fontName, Font.PLAIN, size));
    } catch(Exception ex) {
        lblSample.setFont(new Font("SansSerif", Font.PLAIN, size)); // fallback
    }
}
```

**Capturas obligatorias:**

* A: `lblSample` con la fuente/tamaño aplicados y `lblAlumno` visible.
* B: *Structure* mostrando `cmbFonts`, `sldSize`, `btnAddFont`, `btnRemoveFont`, `lblAlumno`.

---

## Tarea 6 — `JTextArea` + `JScrollPane` + `JTabbedPane` (editor multipestaña)

**Objetivo:** crear un editor simple de pestañas donde cada pestaña contiene un `JTextArea` dentro de `JScrollPane`. Guardado básico con `JFileChooser`.

**Pasos:**

1. `EditorFrame` con:

   * `JTabbedPane tabs`.
   * Botones: `btnNewTab`, `btnCloseTab`, `btnSaveTab`.
2. **OBLIGATORIO:** `lblAlumno` visible en la ventana.
3. Implementar `createNewTab(String title)`:

```java
private void createNewTab(String title) {
    JTextArea ta = new JTextArea();
    ta.setLineWrap(true);
    ta.setWrapStyleWord(true);
    ta.setFont(new Font("Monospaced", Font.PLAIN, 14));
    JScrollPane sp = new JScrollPane(ta);
    tabs.addTab(title, sp);
    tabs.setSelectedComponent(sp);
}
```

4. `btnNewTab` → `createNewTab("Doc " + (tabs.getTabCount()+1));`
   `btnCloseTab` → `tabs.removeTabAt(tabs.getSelectedIndex());` (comprobar índice)
   `btnSaveTab` → abrir `JFileChooser` y guardar el contenido:

```java
Component comp = tabs.getSelectedComponent();
if(comp instanceof JScrollPane) {
    JTextArea ta = (JTextArea)((JScrollPane)comp).getViewport().getView();
    JFileChooser fc = new JFileChooser();
    if(fc.showSaveDialog(this)==JFileChooser.APPROVE_OPTION) {
        File f = fc.getSelectedFile();
        try(FileWriter fw = new FileWriter(f)) {
            fw.write(ta.getText());
        } catch(IOException ex) {
            JOptionPane.showMessageDialog(this, "Error al guardar: " + ex.getMessage());
        }
    }
}
```

**Capturas obligatorias:**

* A: `EditorFrame` con al menos **2 pestañas** abiertas y `lblAlumno` visible.
* B: Diálogo `JFileChooser` (o fichero guardado visible) y `lblAlumno` visible en la aplicación.

---

## Tarea 7 — `JList` + `DefaultListModel` (mover y ordenar elementos)

**Objetivo:** usar dos `JList` con `DefaultListModel` y botones para mover elementos entre listas y ordenar dentro de una lista (up/down).

**Pasos:**

1. `ListsFrame` con `BorderLayout`. CENTER: panel con `GridLayout(1,2)` que contiene:

   * Izquierda: `JList lstAvailable` dentro de `JScrollPane` (`modelAvailable`).
   * Derecha: `JList lstSelected` dentro de `JScrollPane` (`modelSelected`).
2. Entre listas o debajo: botones `btnToRight`, `btnToLeft`, `btnUp`, `btnDown`.
3. **OBLIGATORIO:** `lblAlumno` con tu nombre visible.
4. Inicializa modelos:

```java
DefaultListModel<String> modelAvailable = new DefaultListModel<>();
modelAvailable.addElement("Elemento A");
modelAvailable.addElement("Elemento B");
// ...
lstAvailable.setModel(modelAvailable);
DefaultListModel<String> modelSelected = new DefaultListModel<>();
lstSelected.setModel(modelSelected);
```

5. `btnToRight` (mover seleccionados de available a selected):

```java
btnToRight.addActionListener(e -> {
    List<String> sel = lstAvailable.getSelectedValuesList();
    for(String s: sel) modelSelected.addElement(s);
    int[] idxs = lstAvailable.getSelectedIndices();
    for(int i=idxs.length-1;i>=0;i--) modelAvailable.remove(idxs[i]);
});
```

6. `btnUp` / `btnDown` (ordenar dentro de `modelSelected`), ejemplo Up:

```java
btnUp.addActionListener(e -> {
    int idx = lstSelected.getSelectedIndex();
    if(idx > 0) {
        String s = modelSelected.getElementAt(idx);
        modelSelected.remove(idx);
        modelSelected.add(idx-1, s);
        lstSelected.setSelectedIndex(idx-1);
    }
});
```

7. Establecer `selectionMode` en `MULTIPLE_INTERVAL_SELECTION` si se desea selección múltiple.

**Capturas obligatorias:**

* A: Ambas listas en ejecución mostrando `lblAlumno`.
* B: *Structure* mostrando `lstAvailable`, `lstSelected`, `btnToRight`, `btnToLeft`, `btnUp`, `btnDown`, `lblAlumno`.

