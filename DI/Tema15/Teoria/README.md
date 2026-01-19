# TEMA 15: Nuevos paradigmas - Interfaces declarativas y multiplataforma

## Tabla de contenidos

1. [Introducción: la evolución del desarrollo de interfaces](#1-introducci%C3%B3n-la-evoluci%C3%B3n-del-desarrollo-de-interfaces)
2. [Paradigma imperativo vs declarativo](#2-paradigma-imperativo-vs-declarativo)
    - 2.1. [El enfoque imperativo tradicional](#21-el-enfoque-imperativo-tradicional)
    - 2.2. [El enfoque declarativo moderno](#22-el-enfoque-declarativo-moderno)
    - 2.3. [La ecuación fundamental](#23-la-ecuaci%C3%B3n-fundamental)
3. [Jetpack Compose: el futuro de Android](#3-jetpack-compose-el-futuro-de-android)
    - 3.1. [Fundamentos de Compose](#31-fundamentos-de-compose)
    - 3.2. [Composables y jerarquía](#32-composables-y-jerarqu%C3%ADa)
    - 3.3. [Estado y recomposición](#33-estado-y-recomposici%C3%B3n)
    - 3.4. [Layouts en Compose](#34-layouts-en-compose)
4. [SwiftUI: la visión de Apple](#4-swiftui-la-visi%C3%B3n-de-apple)
    - 4.1. [Fundamentos de SwiftUI](#41-fundamentos-de-swiftui)
    - 4.2. [Views y modificadores](#42-views-y-modificadores)
    - 4.3. [Property wrappers](#43-property-wrappers)
    - 4.4. [Comparativa Compose vs SwiftUI](#44-comparativa-compose-vs-swiftui)
5. [Flutter: desarrollo multiplataforma](#5-flutter-desarrollo-multiplataforma)
    - 5.1. [La filosofía de Flutter](#51-la-filosof%C3%ADa-de-flutter)
    - 5.2. [El árbol de widgets](#52-el-%C3%A1rbol-de-widgets)
    - 5.3. [Stateless vs Stateful widgets](#53-stateless-vs-stateful-widgets)
    - 5.4. [Cuándo usar Flutter vs nativo](#54-cu%C3%A1ndo-usar-flutter-vs-nativo)
6. [Atomic Design aplicado a código](#6-atomic-design-aplicado-a-c%C3%B3digo)
7. [Del diseño de Figma al código declarativo](#7-del-dise%C3%B1o-de-figma-al-c%C3%B3digo-declarativo)
8. [Panorama laboral y decisiones tecnológicas](#8-panorama-laboral-y-decisiones-tecnol%C3%B3gicas)
9. [Caso práctico: ingeniería inversa de interfaces](#9-caso-pr%C3%A1ctico-ingenier%C3%ADa-inversa-de-interfaces)
10. [Recursos y bibliografía](#10-recursos-y-bibliograf%C3%ADa)

***

## 1. Introducción: la evolución del desarrollo de interfaces

Durante más de una década, el desarrollo de interfaces móviles siguió un patrón estricto: **separación de archivos**.

### El modelo tradicional (2008-2019)

```
ANDROID CLÁSICO:
├── Layout (Vista)
│   └── activity_main.xml
│       └── Define estructura UI en XML
│
└── Logic (Lógica)
    └── MainActivity.java
        └── Manipula la UI programáticamente

iOS CLÁSICO:
├── Interface (Vista)
│   └── Main.storyboard
│       └── Define UI gráficamente
│
└── Logic (Lógica)
    └── ViewController.swift
        └── Conecta outlets y acciones
```


### El problema fundamental

**1. Código de "pegamento" excesivo**

```java
// Android tradicional: mucho código para conectar
TextView titleText = findViewById(R.id.title);
Button actionButton = findViewById(R.id.action_btn);
ImageView profileImage = findViewById(R.id.profile_img);

actionButton.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        titleText.setText("Clicked!");
        actionButton.setEnabled(false);
    }
});
```

**2. Sincronización manual del estado**

Si cambias el nombre de un elemento en XML (`android:id="@+id/title"`) y olvidas actualizar el código Java, la app se cierra (crash).

**3. Dificultad para animaciones y listas dinámicas**

Crear una lista con diferentes tipos de elementos requería `RecyclerView.Adapter` con múltiples ViewHolders y cientos de líneas de código repetitivo.

### La revolución declarativa (2019-presente)

Alrededor de 2019, Google y Apple tomaron la misma decisión casi simultáneamente: **eliminar la separación vista/lógica**.

```
NUEVO PARADIGMA:
└── UI como código
    ├── Jetpack Compose (Google, 2021)
    ├── SwiftUI (Apple, 2019)
    └── Flutter (Google, 2017 - pionero)
```

> [!IMPORTANT]
> Este cambio no es una "tendencia pasajera". Google anunció en 2021 que Jetpack Compose es el futuro oficial de Android, y Apple declaró SwiftUI como la forma recomendada de desarrollar para todo su ecosistema (iOS, macOS, watchOS, tvOS).

***

## 2. Paradigma imperativo vs declarativo

Para entender el cambio fundamental, usemos una analogía de la vida real.

### 2.1. El enfoque imperativo tradicional

**Analogía: Cocinar tú mismo**

Controlas cada paso del proceso:

1. Coge una sartén del armario
2. Pon aceite (20ml exactamente)
3. Enciende el fuego a temperatura media
4. Espera 30 segundos
5. Echa el huevo
6. Espera 2 minutos
7. Comprueba si está listo
8. Si no, espera 30 segundos más
9. Saca el huevo

**En código Android tradicional:**

```java
// Imperativo: Describes CÓMO hacer cada cosa
Button button = findViewById(R.id.my_button);
TextView counter = findViewById(R.id.counter);

int count = 0;

button.setOnClickListener(v -> {
    count++;  // 1. Cambia el dato
    counter.setText(String.valueOf(count));  // 2. Actualiza UI manualmente
    
    if (count >= 10) {
        button.setEnabled(false);  // 3. Cambia estado del botón
        button.setBackgroundColor(Color.GRAY);  // 4. Cambia color manualmente
    }
});
```

**Problemas:**

- ❌ Si olvidas actualizar `counter.setText()`, la UI no refleja el estado real
- ❌ Tienes que recordar cambiar el color del botón
- ❌ Si hay múltiples lugares que modifican `count`, debes actualizar la UI en todos


### 2.2. El enfoque declarativo moderno

**Analogía: Ir a un restaurante**

Tú solo pides el resultado final:

- "Quiero un huevo frito"
- El chef decide cómo cocinarlo
- Si quieres cambiarlo: "Ahora quiero un huevo pasado por agua"
- El chef se encarga de todo el proceso

**En código Jetpack Compose:**

```kotlin
// Declarativo: Describes QUÉ quieres mostrar
@Composable
fun CounterScreen() {
    var count by remember { mutableStateOf(0) }
    
    Column {
        Text("Contador: $count")
        
        Button(
            onClick = { count++ },
            enabled = count < 10,
            colors = ButtonDefaults.buttonColors(
                containerColor = if (count >= 10) Color.Gray else Color.Blue
            )
        ) {
            Text("Incrementar")
        }
    }
}
```

**Ventajas:**

- ✅ El texto se actualiza automáticamente cuando `count` cambia
- ✅ El botón se deshabilita automáticamente cuando `count >= 10`
- ✅ El color cambia automáticamente según la condición
- ✅ Solo describes la UI basándote en el estado, el sistema se encarga del resto


### 2.3. La ecuación fundamental

> **UI = f(Estado)**
>
> La Interfaz de Usuario es una función directa del Estado de los datos.

**Explicación:**

```kotlin
// En cualquier momento, la UI se puede calcular desde el estado
Estado actual:
├── count = 5
├── nombre = "Ana"
└── isLoggedIn = true

UI resultante:
├── Text muestra "Contador: 5"
├── Text muestra "Bienvenida, Ana"
└── Button muestra "Cerrar sesión"

Si count cambia a 6:
└── La UI se "repinta" automáticamente mostrando "Contador: 6"
```

**No hay sincronización manual:**

```
IMPERATIVO (Android clásico):
Estado cambia → Tú actualizas la UI manualmente en cada lugar

DECLARATIVO (Compose/SwiftUI):
Estado cambia → El framework repinta la UI automáticamente
```

> [!NOTE]
> Esta filosofía viene del desarrollo web moderno (React, Vue) que demostró que es mucho más mantenible declarar "cómo debería verse la UI dado un estado" que "qué pasos seguir para actualizar cada elemento".

***

## 3. Jetpack Compose: el futuro de Android

**Jetpack Compose** es el toolkit moderno de Google para construir interfaces nativas de Android, escrito 100% en Kotlin.

### 3.1. Fundamentos de Compose

#### Funciones Composables

En Compose, la UI se construye con **funciones componibles** marcadas con `@Composable`.

```kotlin
// Función composable simple
@Composable
fun Greeting(name: String) {
    Text(text = "Hola, $name")
}

// Uso en otra composable
@Composable
fun MainScreen() {
    Greeting(name = "Ana")
    Greeting(name = "Carlos")
}

// Resultado en pantalla:
// Hola, Ana
// Hola, Carlos
```

**Características clave:**

1. **Funciones, no clases:** A diferencia de XML donde defines `<TextView>`, en Compose llamas a `Text()`
2. **Pueden recibir parámetros:** Como cualquier función de Kotlin
3. **Se pueden reutilizar:** Llama a `Greeting()` tantas veces como necesites
4. **Composición:** Puedes componer funciones complejas a partir de simples

#### Ventajas sobre XML

```xml
<!-- XML: Estático, verboso, sin lógica -->
<LinearLayout
    android:orientation="vertical">
    
    <TextView android:text="Usuario 1" />
    <TextView android:text="Usuario 2" />
    <TextView android:text="Usuario 3" />
    <!-- Para mostrar 100 usuarios, necesitas código Java/Kotlin adicional -->
</LinearLayout>
```

```kotlin
// Compose: Dinámico, conciso, con lógica Kotlin
@Composable
fun UserList(users: List<String>) {
    Column {
        users.forEach { user ->
            Text(text = user)
        }
    }
}

// Uso:
UserList(users = listOf("Usuario 1", "Usuario 2", "Usuario 3"))
// Para 100 usuarios, mismo código!
```


### 3.2. Composables y jerarquía

#### Layouts básicos

**Column (equivalente a LinearLayout vertical)**

```kotlin
@Composable
fun VerticalExample() {
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp),
        verticalArrangement = Arrangement.SpaceBetween,
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        Text("Elemento 1")
        Text("Elemento 2")
        Text("Elemento 3")
    }
}
```

**Row (equivalente a LinearLayout horizontal)**

```kotlin
@Composable
fun HorizontalExample() {
    Row(
        modifier = Modifier
            .fillMaxWidth()
            .padding(8.dp),
        horizontalArrangement = Arrangement.SpaceEvenly,
        verticalAlignment = Alignment.CenterVertically
    ) {
        Icon(Icons.Default.Home, contentDescription = "Inicio")
        Text("Inicio")
        Icon(Icons.Default.Settings, contentDescription = "Ajustes")
    }
}
```

**Box (equivalente a FrameLayout)**

```kotlin
@Composable
fun OverlayExample() {
    Box(
        modifier = Modifier.size(200.dp),
        contentAlignment = Alignment.Center
    ) {
        Image(/* imagen de fondo */)
        Text("Texto encima", color = Color.White)
    }
}
```


#### Jerarquía visual

```kotlin
@Composable
fun ProfileCard(name: String, email: String, imageUrl: String) {
    Card(
        modifier = Modifier
            .fillMaxWidth()
            .padding(16.dp),
        elevation = CardDefaults.cardElevation(4.dp)
    ) {
        Row(
            modifier = Modifier.padding(16.dp),
            verticalAlignment = Alignment.CenterVertically
        ) {
            // Avatar
            AsyncImage(
                model = imageUrl,
                contentDescription = "Foto de $name",
                modifier = Modifier
                    .size(60.dp)
                    .clip(CircleShape)
            )
            
            Spacer(modifier = Modifier.width(16.dp))
            
            // Información
            Column {
                Text(
                    text = name,
                    style = MaterialTheme.typography.titleMedium,
                    fontWeight = FontWeight.Bold
                )
                Text(
                    text = email,
                    style = MaterialTheme.typography.bodyMedium,
                    color = Color.Gray
                )
            }
        }
    }
}
```

**Equivalente en estructura de Figma:**

```
Card (Auto Layout horizontal, padding 16dp)
├── AsyncImage (60x60, circular)
├── Spacer (16dp)
└── Column (Auto Layout vertical)
    ├── Text (titleMedium, bold)
    └── Text (bodyMedium, gray)
```


### 3.3. Estado y recomposición

#### Estado con remember

```kotlin
@Composable
fun Counter() {
    // remember: El valor persiste entre recomposiciones
    var count by remember { mutableStateOf(0) }
    
    Column(
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        Text(
            text = "Has pulsado $count veces",
            style = MaterialTheme.typography.headlineMedium
        )
        
        Button(onClick = { count++ }) {
            Text("Pulsar")
        }
    }
}
```

**Cómo funciona:**

1. Primera vez que se ejecuta `Counter()`:
    - `remember` crea `count = 0`
    - Se dibuja la UI con "Has pulsado 0 veces"
2. Usuario pulsa el botón:
    - `count++` cambia el estado a `count = 1`
    - Compose detecta el cambio y **recompone** la función
    - Se redibuja solo el `Text` (optimización automática)
3. Usuario pulsa de nuevo:
    - `count = 2` → Recomposición → UI actualizada

#### Estado compartido (hoisting)

```kotlin
// MAL: Estado interno, no se puede controlar desde fuera
@Composable
fun SearchBar() {
    var query by remember { mutableStateOf("") }
    TextField(
        value = query,
        onValueChange = { query = it }
    )
}

// BIEN: Estado elevado (state hoisting)
@Composable
fun SearchBar(
    query: String,
    onQueryChange: (String) -> Unit
) {
    TextField(
        value = query,
        onValueChange = onQueryChange
    )
}

// Uso:
@Composable
fun SearchScreen() {
    var searchQuery by remember { mutableStateOf("") }
    
    Column {
        SearchBar(
            query = searchQuery,
            onQueryChange = { searchQuery = it }
        )
        
        // Otros componentes pueden usar searchQuery
        Text("Buscando: $searchQuery")
    }
}
```


### 3.4. Layouts en Compose

#### LazyColumn (lista eficiente)

```kotlin
@Composable
fun TaskList(tasks: List<Task>) {
    LazyColumn {
        items(tasks) { task ->
            TaskItem(task)
        }
    }
}

@Composable
fun TaskItem(task: Task) {
    Row(
        modifier = Modifier
            .fillMaxWidth()
            .padding(16.dp),
        verticalAlignment = Alignment.CenterVertically
    ) {
        Checkbox(
            checked = task.isDone,
            onCheckedChange = { /* actualizar tarea */ }
        )
        Text(
            text = task.title,
            modifier = Modifier.padding(start = 8.dp)
        )
    }
}
```

**Ventajas sobre RecyclerView:**

- ✅ No necesitas Adapter, ViewHolder ni LayoutManager
- ✅ Reciclaje automático de elementos (igual que RecyclerView)
- ✅ Solo 5 líneas vs 50+ líneas de RecyclerView tradicional

***

## 4. SwiftUI: la visión de Apple

**SwiftUI** es el framework declarativo de Apple para construir interfaces en todo su ecosistema.

### 4.1. Fundamentos de SwiftUI

#### Estructuras View

```swift
// SwiftUI usa structs que conforman el protocolo View
struct ContentView: View {
    var body: some View {
        Text("Hola, SwiftUI")
    }
}
```

**Diferencias con Compose:**


| Aspecto | Jetpack Compose | SwiftUI |
| :-- | :-- | :-- |
| Lenguaje | Kotlin | Swift |
| Anotación | `@Composable` | `View` protocol |
| Tipo | Funciones | Structs |
| Body | Implícito | `var body: some View` |

### 4.2. Views y modificadores

#### Layouts básicos

**VStack (vertical)**

```swift
struct VerticalExample: View {
    var body: some View {
        VStack(spacing: 16) {
            Text("Elemento 1")
            Text("Elemento 2")
            Text("Elemento 3")
        }
        .padding()
    }
}
```

**HStack (horizontal)**

```swift
struct HorizontalExample: View {
    var body: some View {
        HStack(spacing: 12) {
            Image(systemName: "house.fill")
            Text("Inicio")
        }
    }
}
```

**ZStack (capas)**

```swift
struct OverlayExample: View {
    var body: some View {
        ZStack {
            Image("background")
            Text("Texto encima")
                .foregroundColor(.white)
        }
    }
}
```


#### Modificadores (orden importa)

```swift
Text("Hola")
    .padding()
    .background(Color.blue)
// Resultado: Texto con padding, luego fondo azul (grande)

Text("Hola")
    .background(Color.blue)
    .padding()
// Resultado: Texto con fondo azul (pequeño), luego padding transparente
```


### 4.3. Property wrappers

#### @State (estado local)

```swift
struct Counter: View {
    @State private var count = 0
    
    var body: some View {
        VStack {
            Text("Has pulsado \(count) veces")
                .font(.headline)
            
            Button("Pulsar") {
                count += 1
            }
        }
    }
}
```


#### @Binding (estado compartido)

```swift
struct TextField: View {
    @Binding var text: String
    
    var body: some View {
        TextField("Introduce texto", text: $text)
    }
}

struct ParentView: View {
    @State private var userInput = ""
    
    var body: some View {
        VStack {
            TextField(text: $userInput)
            Text("Has escrito: \(userInput)")
        }
    }
}
```


### 4.4. Comparativa Compose vs SwiftUI

**Ejemplo idéntico en ambos frameworks:**

```kotlin
// Jetpack Compose
@Composable
fun ProfileView(name: String, onEditClick: () -> Unit) {
    Column(
        modifier = Modifier.padding(16.dp)
    ) {
        Text(
            text = name,
            style = MaterialTheme.typography.headlineMedium
        )
        Button(onClick = onEditClick) {
            Text("Editar perfil")
        }
    }
}
```

```swift
// SwiftUI
struct ProfileView: View {
    let name: String
    let onEditClick: () -> Void
    
    var body: some View {
        VStack(spacing: 8) {
            Text(name)
                .font(.headline)
            
            Button("Editar perfil", action: onEditClick)
        }
        .padding()
    }
}
```

**Similitudes:**

- ✅ Sintaxis casi idéntica conceptualmente
- ✅ Jerarquía de contenedores (Column/VStack)
- ✅ Modificadores para estilo (.padding())
- ✅ Callbacks para acciones (onClick/action)

***

## 5. Flutter: desarrollo multiplataforma

**Flutter** es el framework de Google para crear apps que funcionan en Android, iOS, Web y Desktop desde un único código base.

### 5.1. La filosofía de Flutter

#### El motor de renderizado propio

A diferencia de otros frameworks multiplataforma, Flutter **no traduce** tu código a componentes nativos.

```
OTROS FRAMEWORKS (React Native, Xamarin):
JavaScript/C# → Puente → Componentes nativos
                        ├─ Android Button (Nativo)
                        └─ iOS UIButton (Nativo)

Ventaja: Aspecto 100% nativo
Desventaja: Problemas de rendimiento por el "puente"

FLUTTER:
Dart → Motor Skia/Impeller → Dibuja píxeles directamente
                            └─ Botón dibujado pixel a pixel

Ventaja: Rendimiento nativo (60fps garantizado)
Desventaja: No usa componentes del sistema (más peso app)
```

**Visualización:**

```
Flutter funciona como un motor de videojuegos:
├── Canvas en blanco
├── Motor de renderizado (Skia)
└── Dibuja cada elemento desde cero
    ├── Rectángulo azul → Botón
    ├── Texto blanco → Label
    └── Sombra gris → Elevación
```


### 5.2. El árbol de widgets

En Flutter, **TODO es un Widget**.

```dart
// Incluso el padding es un widget que envuelve a otro
Padding(
  padding: EdgeInsets.all(16.0),
  child: Text('Hola')
)

// El centrado es un widget
Center(
  child: Text('Centrado')
)

// Incluso el color de fondo es un widget Container
Container(
  color: Colors.blue,
  child: Text('Con fondo')
)
```

**Jerarquía visual:**

```dart
void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('Mi App'),
        ),
        body: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              Text('Hola Flutter'),
              ElevatedButton(
                onPressed: () {},
                child: Text('Pulsar'),
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

**Equivalente en estructura:**

```
MaterialApp (raíz de la app)
└── Scaffold (estructura básica)
    ├── AppBar (barra superior)
    │   └── Text "Mi App"
    └── Body
        └── Center
            └── Column
                ├── Text "Hola Flutter"
                └── ElevatedButton
                    └── Text "Pulsar"
```


### 5.3. Stateless vs Stateful widgets

#### StatelessWidget (sin estado)

```dart
class Greeting extends StatelessWidget {
  final String name;
  
  Greeting({required this.name});
  
  @override
  Widget build(BuildContext context) {
    return Text('Hola, $name');
  }
}
```


#### StatefulWidget (con estado)

```dart
class Counter extends StatefulWidget {
  @override
  _CounterState createState() => _CounterState();
}

class _CounterState extends State<Counter> {
  int _count = 0;
  
  void _increment() {
    setState(() {
      _count++;
    });
  }
  
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text('Contador: $_count'),
        ElevatedButton(
          onPressed: _increment,
          child: Text('Incrementar'),
        ),
      ],
    );
  }
}
```


### 5.4. Cuándo usar Flutter vs nativo

**Ventajas de Flutter:**

✅ **Un código → Múltiples plataformas:**

```
1 codebase en Dart
├── Android (APK)
├── iOS (IPA)
├── Web (HTML/JS)
├── Windows (EXE)
├── macOS (APP)
└── Linux (binario)
```

✅ **Hot Reload:** Cambios visibles en <1 segundo sin recompilar

✅ **UI consistente:** Se ve idéntico en Android 6 y Android 14

✅ **Productividad:** Desarrollo 40-60% más rápido que nativo

**Desventajas de Flutter:**

❌ **Tamaño de app:** APK inicial ~15MB (vs ~2MB nativo)

❌ **Acceso a APIs nativas:** Requiere plugins o código nativo

❌ **No sigue guías de plataforma:** No se ve "nativo" automáticamente

**Decisión:**

```
USA FLUTTER SI:
├── Startup con presupuesto limitado
├── Necesitas app en ambas tiendas YA
├── Diseño visual muy custom (no sigue Material/HIG)
├── Equipo pequeño (1-3 desarrolladores)
└── App de contenido (no hardware complejo)

USA NATIVO SI:
├── App de banco, gobierno (máxima seguridad)
├── Uso intensivo de hardware (Bluetooth LE, NFC, ARKit)
├── Performance crítico (juegos 3D, edición video)
├── Equipo grande con expertos iOS y Android
└── Presupuesto y tiempo no son limitantes
```


***

## 6. Atomic Design aplicado a código

La metodología **Atomic Design** conecta perfectamente con las interfaces declarativas.

### Jerarquía de componentes

```
ÁTOMOS (elementos indivisibles)
├── Colors.kt / Colors.swift
│   └── val Primary = Color(0xFF2196F3)
├── Typography.kt
│   └── val BodyMedium = TextStyle(fontSize = 16.sp)
└── Icons.kt
    └── val IconHeart = Icons.Default.Favorite

MOLÉCULAS (combinación de átomos)
├── PrimaryButton.kt
│   └── @Composable fun PrimaryButton(text: String, onClick: () -> Unit)
├── ProfileAvatar.kt
│   └── @Composable fun ProfileAvatar(imageUrl: String, size: Dp)
└── SearchBar.kt
    └── @Composable fun SearchBar(query: String, onQueryChange: (String) -> Unit)

ORGANISMOS (secciones complejas)
├── NavigationBar.kt
│   └── @Composable fun AppNavigationBar()
├── ProductCard.kt
│   └── @Composable fun ProductCard(product: Product)
└── UserHeader.kt
    └── @Composable fun UserHeader(user: User)

PLANTILLAS (layouts de página)
├── HomeTemplate.kt
│   └── @Composable fun HomeTemplate(content: @Composable () -> Unit)
└── DetailTemplate.kt

PÁGINAS (pantallas completas)
├── HomeScreen.kt
├── ProfileScreen.kt
└── SettingsScreen.kt
```

**Ejemplo de composición:**

```kotlin
// Átomo
@Composable
fun AppIcon(icon: ImageVector) {
    Icon(
        imageVector = icon,
        contentDescription = null,
        tint = MaterialTheme.colorScheme.primary
    )
}

// Molécula
@Composable
fun NavigationItem(
    icon: ImageVector,
    label: String,
    onClick: () -> Unit
) {
    Column(
        horizontalAlignment = Alignment.CenterHorizontally,
        modifier = Modifier.clickable(onClick = onClick)
    ) {
        AppIcon(icon = icon)
        Text(text = label, style = MaterialTheme.typography.labelSmall)
    }
}

// Organismo
@Composable
fun BottomNavigationBar(
    currentRoute: String,
    onNavigate: (String) -> Unit
) {
    Row(
        modifier = Modifier.fillMaxWidth(),
        horizontalArrangement = Arrangement.SpaceEvenly
    ) {
        NavigationItem(
            icon = Icons.Default.Home,
            label = "Inicio",
            onClick = { onNavigate("home") }
        )
        NavigationItem(
            icon = Icons.Default.Search,
            label = "Buscar",
            onClick = { onNavigate("search") }
        )
        NavigationItem(
            icon = Icons.Default.Person,
            label = "Perfil",
            onClick = { onNavigate("profile") }
        )
    }
}

// Página
@Composable
fun HomeScreen() {
    Scaffold(
        bottomBar = {
            BottomNavigationBar(
                currentRoute = "home",
                onNavigate = { /* navegación */ }
            )
        }
    ) { paddingValues ->
        // Contenido de Home
    }
}
```


***

## 7. Del diseño de Figma al código declarativo

### Traducción directa

**Figma Auto Layout → Jetpack Compose:**

```
Frame (Auto Layout)
├── Direction: Vertical
├── Spacing: 16
├── Padding: 20
└── Alignment: Center

↓ SE TRADUCE A ↓

Column(
    modifier = Modifier.padding(20.dp),
    verticalArrangement = Arrangement.spacedBy(16.dp),
    horizontalAlignment = Alignment.CenterHorizontally
) {
    // Hijos
}
```

**Componente de Figma → Composable:**

```
Component "Botón Primario"
├── Property: text (String)
├── Variant: state (Default, Pressed, Disabled)
└── Style:
    ├── Background: Primary color
    ├── Text: White, 16sp, Medium
    ├── Padding: 12dp vertical, 24dp horizontal
    └── Border radius: 8dp

↓ SE TRADUCE A ↓

@Composable
fun PrimaryButton(
    text: String,
    onClick: () -> Unit,
    enabled: Boolean = true
) {
    Button(
        onClick = onClick,
        enabled = enabled,
        colors = ButtonDefaults.buttonColors(
            containerColor = MaterialTheme.colorScheme.primary
        ),
        shape = RoundedCornerShape(8.dp),
        contentPadding = PaddingValues(
            horizontal = 24.dp,
            vertical = 12.dp
        )
    ) {
        Text(
            text = text,
            style = MaterialTheme.typography.labelLarge
        )
    }
}
```


### Herramientas de conversión

**Figma → Flutter:**

- Plugin "Figma to Code" (genera widgets de Flutter)
- Plugin "FlutLab" (exporta directamente a proyecto Flutter)

**Figma → Compose:**

- Plugin "Relay" (Google oficial - preview)
- Conversión manual siguiendo estructuras de Auto Layout

> [!WARNING]
> El código generado automáticamente siempre necesita refactorización. Úsalo como punto de partida, no como producto final.

***

## 8. Panorama laboral y decisiones tecnológicas

### Qué aprender según tu objetivo profesional

**Quieres trabajar en grandes corporaciones (bancos, seguros, gobierno):**

```
PRIORIDAD 1: Nativo
├── Jetpack Compose (Android)
└── SwiftUI (iOS)

Razón: Máxima seguridad, acceso completo a APIs, performance óptimo
Ejemplos: BBVA, CaixaBank, Mapfre, Correos
```

**Quieres trabajar en startups o consultoras:**

```
PRIORIDAD 1: Flutter
PRIORIDAD 2: Nativo (conocimiento básico)

Razón: Time-to-market rápido, un developer hace ambas plataformas
Ejemplos: Glovo (partes de la app), Aliexpress, BMW, eBay
```

**Quieres ser freelance:**

```
PRIORIDAD 1: Flutter
PRIORIDAD 2: React Native (alternativa)

Razón: Cobras por proyecto, no por plataforma. El doble de ingresos con el mismo tiempo
```


### Tendencias del mercado (2026)

**Ofertas de empleo en España (datos aproximados):**

- Android nativo (Kotlin/Compose): 40%
- iOS nativo (Swift/SwiftUI): 30%
- Flutter: 20%
- React Native: 8%
- Otros (Xamarin, Ionic): 2%

**Salarios medios (junior, España):**

- Desarrollador iOS: 24-28K€
- Desarrollador Android: 22-26K€
- Desarrollador Flutter: 23-27K€
- Fullstack iOS + Android: 30-35K€

***

## 9. Caso práctico: ingeniería inversa de interfaces

### Ejercicio: descomposición de Instagram

**Contexto:** Observa la pantalla principal de Instagram.

**Tarea:** Describe la estructura en formato de árbol de widgets/composables.

**Solución esperada:**

```
Scaffold
├── TopAppBar
│   ├── Logo "Instagram" (Image)
│   ├── Spacer (ocupa espacio restante)
│   └── Row (iconos derecha)
│       ├── Icon "Corazón" (notificaciones)
│       └── Icon "Messenger"
│
├── Body (LazyColumn con scroll vertical)
│   ├── StoriesRow (LazyRow con scroll horizontal)
│   │   ├── StoryItem (Avatar + Nombre) × N
│   │   └── ...
│   │
│   └── PostList (LazyColumn)
│       ├── PostItem
│       │   ├── PostHeader (Row)
│       │   │   ├── Avatar
│       │   │   ├── Username
│       │   │   ├── Spacer
│       │   │   └── Icon "Tres puntos"
│       │   │
│       │   ├── PostImage (AsyncImage)
│       │   │
│       │   ├── ActionButtons (Row)
│       │   │   ├── Icon "Corazón"
│       │   │   ├── Icon "Comentar"
│       │   │   ├── Icon "Compartir"
│       │   │   ├── Spacer
│       │   │   └── Icon "Guardar"
│       │   │
│       │   └── PostFooter (Column)
│       │       ├── Text "X likes"
│       │       ├── Text "Username: Caption..."
│       │       └── Text "Ver X comentarios"
│       │
│       └── ... (más posts)
│
└── BottomNavigationBar
    ├── NavItem "Home"
    ├── NavItem "Buscar"
    ├── NavItem "Reels"
    ├── NavItem "Shop"
    └── NavItem "Perfil"
```

**Objetivo del ejercicio:**

Entender que independientemente del lenguaje (Kotlin, Swift, Dart), la estructura mental de una interfaz declarativa es siempre una **jerarquía de contenedores**.

***

## 10. Recursos y bibliografía

### Documentación oficial

**Jetpack Compose:**

- Web oficial: https://developer.android.com/jetpack/compose
- Curso oficial: https://developer.android.com/courses/jetpack-compose/course
- Ejemplos: https://github.com/android/compose-samples
- Compose Pathway (Codelabs): https://developer.android.com/courses/pathways/compose

**SwiftUI:**

- Web oficial: https://developer.apple.com/xcode/swiftui/
- Tutoriales Apple: https://developer.apple.com/tutorials/swiftui
- WWDC sessions: https://developer.apple.com/videos/
- SwiftUI by Example: https://www.hackingwithswift.com/quick-start/swiftui

**Flutter:**

- Web oficial: https://flutter.dev
- Widget catalog: https://flutter.dev/docs/development/ui/widgets
- Codelabs: https://docs.flutter.dev/codelabs
- Flutter samples: https://github.com/flutter/samples
- Showcase: https://flutter.dev/showcase (apps famosas que usan Flutter)


### Cursos recomendados

**Gratuitos:**

- Google Codelabs (Compose y Flutter)
- Apple Tutorials (SwiftUI)
- YouTube: Philipp Lackner (Compose)
- YouTube: Paul Hudson / Hacking with Swift (SwiftUI)
- YouTube: The Net Ninja (Flutter)

**De pago:**

- Udemy: "Jetpack Compose Masterclass" - Denis Panjuta
- Udemy: "SwiftUI Masterclass" - Robert Petras
- Udemy: "The Complete Flutter Development Bootcamp" - Angela Yu


### Libros recomendados

- **"Jetpack Compose by Tutorials"** - Kodeco (antes raywenderlich.com)
- **"SwiftUI by Tutorials"** - Kodeco
- **"Flutter Complete Reference"** - Alberto Miola
- **"Atomic Design"** - Brad Frost (metodología de componentes)


### Comunidades

**Reddit:**

- r/androiddev (Android/Compose)
- r/iOSProgramming (iOS/SwiftUI)
- r/FlutterDev (Flutter)

**Discord/Slack:**

- Kotlin Slack (\#compose channel)
- iOS Developers Slack
- Flutter Community Discord


### Herramientas útiles

**Playgrounds online:**

- Jetpack Compose Playground: https://developer.android.com/jetpack/compose/tutorial
- SwiftUI Lab: Xcode Playgrounds (local)
- DartPad: https://dartpad.dev (Flutter online)

**Design-to-Code:**

- Figma → Flutter: DhiWise, FlutLab
- Sketch → iOS: Anima
- Figma → Compose: Relay by Google (beta)

***

### Puntos clave para recordar

1. **El paradigma declarativo es el futuro:** Compose, SwiftUI y Flutter representan cómo se desarrollarán interfaces los próximos 10+ años
2. **UI = f(Estado):** La interfaz es una función del estado. Cambia el estado, la UI se actualiza automáticamente
3. **Todo es componible:** Desde un botón hasta una pantalla completa, construyes composiciones de elementos más simples
4. **La estructura importa más que el lenguaje:** Un `Column` en Compose, un `VStack` en SwiftUI y un `Column` en Flutter son conceptualmente idénticos
5. **Figma y código son hermanos:** Auto Layout en Figma se traduce directamente a Column/Row/VStack/HStack
6. **Accesibilidad desde el código:** Los `contentDescription`/`accessibilityLabel` que anotasteis en Figma se implementan directamente en código


### La realidad del desarrollo moderno

Actualmente, las empresas tecnológicas buscan desarrolladores que:

- ✅ Entiendan diseño (no solo "copien" mockups)
- ✅ Dominen frameworks declarativos (Compose/SwiftUI/Flutter)
- ✅ Piensen en componentes reutilizables
- ✅ Sepan justificar decisiones técnicas

**No necesitáis ser expertos en Compose, SwiftUI Y Flutter.** Pero si domináis uno profundamente, los otros dos se aprenden en semanas porque **la filosofía es la misma**.

