# Tema 2 — Tareas guiadas (bloque de layouts)

**Nota común a todas las tareas (leer antes de empezar)**

1. Todas las tareas se realizan con **Eclipse + WindowBuilder**. Usad la vista **Design** para colocar componentes y la vista **Structure** para renombrar variables. Editad **Source** sólo cuando se indique código.
2. **IDENTIFICACIÓN OBLIGATORIA:** antes de ejecutar la aplicación, añadid `JLabel lblAlumno` y poned en su `text` vuestro **NOMBRE Y APELLIDOS**. `lblAlumno` debe ser visible en las capturas.

## Tarea L1 — FlowLayout (flujo)

**Objetivo:** comprender el comportamiento de `FlowLayout` (alineaciones, gaps, alineación por eje) y cómo afecta cuando la ventana se redimensiona.

**Pasos:**

1. `File > New > JFrame` → `FlowDemoFrame`. En *Properties*: `defaultCloseOperation = EXIT_ON_CLOSE`, `bounds = 100,100,520,220`.
2. Arrastra un `JPanel` al `contentPane`. En *Properties* del panel: `layout` → `FlowLayout`. (El panel princpal "contentPane" debe ser de tipo BorderLayout)

   * Ajusta `hgap = 10`, `vgap = 10`, `alignment = CENTER` (prueba también `LEFT` y `RIGHT`).
3. Añade los siguientes componentes al panel (en ese orden):

   * `JLabel lblAlumno` (pon tu **NOMBRE APELLIDOS** en `text`) — obligatorio.
   * `JButton btnA` (`text = A`), `JButton btnB` (`text = B`), `JButton btnC` (`text = C`), `JButton btnD` (`text = D`), `JButton btnE` (`text = E`).
4. Desde *Structure* renombra variables: `lblAlumno`, `btnA`… `btnE`.
5. Ejecuta la ventana y **redimensiona** la ventana para observar cómo los botones fluyen a la siguiente línea cuando no hay espacio.
6. **Cambio por Properties:** selecciona el `JPanel` y prueba `alignment = LEFT` y `RIGHT` para ver la diferencia.
7. (Opcional interno al tema) en *Properties* de cada `JButton` prueba `preferredSize` y observa efecto.

**Código de ejemplo** (no es necesario, pero útil para añadir comportamiento):

```java
btnA.addActionListener(e -> JOptionPane.showMessageDialog(this, "Pulsado A"));
```

**Capturas obligatorias:**

* A: Ventana ejecutada con `lblAlumno` visible.
* B: La misma ventana **redimensionada** donde se aprecie que los botones han pasado a la siguiente línea (muestra `lblAlumno` también).

---

## Tarea L2 — BorderLayout

**Objetivo:** dominar `BorderLayout` (NORTH, SOUTH, EAST, WEST, CENTER) y cómo cada región gestiona su tamaño y comportamiento.

**Pasos:**

1. `File > New > JFrame` → `BorderDemoFrame`. `bounds = 100,100,600,360`.
2. En el `contentPane` pon su `layout = BorderLayout`.
3. Añade componentes en las cinco regiones:

   * `BorderLayout.NORTH`: `JPanel` pequeño con `JLabel lblTitle` (`text = Título`) y `lblAlumno` (NOMBRE y APELLIDOS) dentro del mismo panel norte (ponerlos alineados con FlowLayout).
   * `BorderLayout.SOUTH`: `JPanel` con `JButton btnAceptar` y `JButton btnCancelar`.
   * `BorderLayout.WEST`: `JPanel` con `BoxLayout` vertical (o GridLayout 3x1) con tres `JButton` (`btnLeft1/2/3`).
   * `BorderLayout.EAST`: `JPanel` con un `JLabel` o `JList` pequeño (por ejemplo `JLabel lblInfo`).
   * `BorderLayout.CENTER`: `JPanel` grande con un `JTextArea` o `JLabel` que ocupe el centro (`lblCentro`).
4. Observa: cambia el `preferredSize` del panel WEST en *Properties* y mira cómo CENTER se reajusta.
5. Ejecuta y prueba distintos tamaños de ventana.

**Código útil (ejemplo para btnAceptar):**

```java
btnAceptar.addActionListener(e -> JOptionPane.showMessageDialog(this, "Aceptado"));
```

**Capturas obligatorias:**

* A: Ejecución mostrando claramente `lblAlumno` en NORTH y los cinco sectores visibles.
* B: Structure del diseñador mostrando las regiones y los nombres de variables (incluyendo `lblAlumno`).

---

## Tarea L3 — GridLayout

**Objetivo:** comprender `GridLayout` (filas/columnas, gaps, comportamiento uniforme de celdas) y diseñar un panel tipo formulario en rejilla.

**Pasos:**

1. `File > New > JFrame` → `GridDemoFrame`. `bounds = 100,100,520,320`.
2. En `contentPane` pon `layout = BorderLayout`. En `CENTER` inserta un `JPanel` con `layout = GridLayout` y configura `rows = 4`, `cols = 2`, `hgap = 8`, `vgap = 8`.
3. Rellena la rejilla como formulario (fila por fila):

   * Fila 1: `JLabel lblAlumno` (tu NOMBRE y APELLIDOS — obligatorio) | `JLabel lblPlaceholder` (o `JTextField`).
   * Fila 2: `JLabel "Email:"` | `JTextField txtEmail`.
   * Fila 3: `JLabel "Teléfono:"` | `JTextField txtPhone`.
   * Fila 4: `JLabel "Comentarios:"` | `JTextArea` pequeño dentro de `JScrollPane` (si ocupa mucho, pon un `JLabel` y deja el `JTextArea` en otra región).
4. Observa que todas las celdas tienen el **mismo** tamaño. Cambia `rows` y `cols` para ver efecto.
5. Desde *Structure* renombra controladamente: `txtEmail`, `txtPhone`, etc.

**Fragmento para `JTextArea` en `JScrollPane`:**

```java
JTextArea ta = new JTextArea(3, 20);
ta.setLineWrap(true);
JScrollPane sp = new JScrollPane(ta);
```

**Capturas obligatorias:**

* A: Ejecución con `lblAlumno` visible y el formulario completo.
* B: Structure mostrando `GridLayout` configurado y los nombres de componentes.

---

## Tarea L4 — GridBagLayout (control fino del grid)

**Objetivo:** aprender `GridBagLayout` y `GridBagConstraints` para posicionamientos flexibles (peso, relleno, anclaje, spanning).

**Pasos:**

1. `File > New > JFrame` → `GridBagDemoFrame`. `bounds = 100,100,640,420`.
2. Selecciona el `contentPane` y en *Properties* pon `layout = GridBagLayout`. (WindowBuilder ofrece un editor visual para GridBagConstraints en cada componente).
3. Diseña una interfaz tipo formulario donde algunos campos ocupen varias columnas:

   * Fila 0 (col 0..2): `JLabel lblAlumno` (colspan 3) — obligatorio. Establecer `gridwidth = 3`, `fill = HORIZONTAL`, `weightx = 1.0`.
   * Fila 1: `JLabel "Nombre:"` (col 0), `JTextField txtNombre` (col 1..2, `gridwidth=2`, `fill=HORIZONTAL`, `weightx=1.0`).
   * Fila 2: `JLabel "Apellidos:"` (col0), `JTextField txtApellidos` (col1..2).
   * Fila 3: `JLabel "Comentarios:"` (col0), `JScrollPane` con `JTextArea` (col1..2, `gridheight=2`, `fill=BOTH`, `weightx=1.0`, `weighty=1.0`).
   * Fila 5: botones `btnAceptar` y `btnCancelar` (alineados a la derecha: usar `anchor = EAST` y `gridwidth` adecuados).
4. En cada componente ajusta `GridBagConstraints` en Properties:

   * `gridx`, `gridy`, `gridwidth`, `gridheight`, `weightx`, `weighty`, `fill` (NONE/HORIZONTAL/VERTICAL/BOTH), `insets` (margenes), `anchor` (NORTH/EAST/etc).
5. Ejecuta y redimensiona la ventana; observa cómo los componentes con `weightx/weighty` y `fill=BOTH` se expanden; los que no lo tienen permanecen con tamaño preferido.
6. Añade `lblAlumno` en la primera fila (colspan) con tu nombre visible.

**Ejemplo de código (si editas Source):**

```java
GridBagConstraints gbc = new GridBagConstraints();
gbc.gridx = 0; gbc.gridy = 1; gbc.anchor = GridBagConstraints.EAST;
contentPane.add(lblNombre, gbc);

gbc = new GridBagConstraints();
gbc.gridx = 1; gbc.gridy = 1; gbc.gridwidth = 2; gbc.fill = GridBagConstraints.HORIZONTAL; gbc.weightx = 1.0;
contentPane.add(txtNombre, gbc);
```

**Consejos:** usa el editor de GridBag que ofrece WindowBuilder para no escribir a mano todas las `GridBagConstraints`.

**Capturas obligatorias:**

* A: Ejecución con `lblAlumno` visible y la ventana en tamaño normal.
* B: Ventana redimensionada donde se aprecie expansión del `JTextArea` (muestra `lblAlumno` igualmente).
* C: Vista *Structure* o propiedades del componente que muestre `GridBagConstraints` (gridx/gridy/gridwidth/weightx/weighty) para al menos 3 componentes.

---

## Tarea L5 — BoxLayout (alineamiento en un eje)

**Objetivo:** usar `BoxLayout` para ordenar componentes en una columna o fila

**Pasos:**

1. `File > New > JFrame` → `BoxDemoFrame`. `bounds = 100,100,480,340`.
2. En `contentPane` pon `layout = BoxLayout` (axis = Y\_AXIS) para apilar verticalmente.
3. Añade componentes en orden vertical:

   * `JLabel lblAlumno` (tu NOMBRE APELLIDOS — obligatorio).
   * `JLabel lblTitle` (centrado, usa `setAlignmentX(Component.CENTER_ALIGNMENT)` en Properties si el editor lo permite).
   * `JTextField txtField` (fill horizontal: en BoxLayout debe usarse `setMaximumSize` o envolver en panel).
   * `JButton btnAction` (centrado).
4. Entre componentes añade `RigidArea` o `VerticalStrut` desde Palette (si WindowBuilder lo permite) para espaciar; alternativa: jugar con `border`/`EmptyBorder`.
5. Experimenta con `setAlignmentX` en distintos componentes (`LEFT_ALIGNMENT`, `CENTER_ALIGNMENT`, `RIGHT_ALIGNMENT`) y observa cómo cambian las posiciones.

**Fragmento útil (Source) para alinear:**

```java
lblTitle.setAlignmentX(Component.CENTER_ALIGNMENT);
btnAction.setAlignmentX(Component.CENTER_ALIGNMENT);
txtField.setMaximumSize(new Dimension(Integer.MAX_VALUE, txtField.getPreferredSize().height));
```

**Capturas obligatorias:**

* A: Interfaz ejecutada con `lblAlumno` visible y el apilamiento vertical.
* B: Structure mostrando `BoxLayout` y los componentes con `alignment` (si se indica en Properties).

---

## Tarea L6 — CardLayout (múltiples “pantallas” intercambiables)

**Objetivo:** usar `CardLayout` para crear varias “vistas” o pantallas dentro de la misma área y navegar entre ellas (simulando wizard o pestañas controladas).

**Pasos:**

1. `File > New > JFrame` → `CardDemoFrame`. `bounds = 100,100,520,320`.
2. En `contentPane` pon `layout = BorderLayout`. En `CENTER` añade un `JPanel` con `layout = CardLayout` (nómbralo `cardsPanel`).
3. Crea 3 paneles que serán cartas:

   * `card1`: `JPanel` con `JLabel lblAlumno` (tu NOMBRE y APELLIDOS obligatorio) y `JLabel` con texto `Vista 1`.
   * `card2`: `JPanel` con `JLabel` `Vista 2` y algún control (ej. `JTextField`).
   * `card3`: `JPanel` con `JLabel` `Vista 3`.
     Añade las tres cartas a `cardsPanel` con nombres (String) `CARD1`, `CARD2`, `CARD3`.
4. En `SOUTH` coloca un `JPanel` con 3 `JButton`: `btnPrev`, `btnNext`, `btnGoTo2` (ejemplo).
5. Código para cambiar cartas:

```java
CardLayout cl = (CardLayout)(cardsPanel.getLayout());
btnNext.addActionListener(e -> cl.next(cardsPanel));
btnPrev.addActionListener(e -> cl.previous(cardsPanel));
btnGoTo2.addActionListener(e -> cl.show(cardsPanel, "CARD2"));
```

6. Asegúrate de que `lblAlumno` sea visible en **al menos una carta** (obligatorio). Se recomienda además incluirlo en la carta principal (CARD1).

**Capturas obligatorias:**

* A: Ejecución mostrando `card1` con `lblAlumno`.
* B: Ejecución mostrando otra carta (CARD2 o CARD3) tras pulsar `btnNext` o `btnGoTo2`, y `lblAlumno` visible en captura A (en la carta donde esté).

