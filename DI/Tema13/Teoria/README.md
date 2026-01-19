# TEMA 13: Sistemas de diseño móvil - Material Design vs Human Interface Guidelines

## Tabla de contenidos

1. [Introducción: ¿qué es un sistema de diseño?](#1-introducci%C3%B3n-qu%C3%A9-es-un-sistema-de-dise%C3%B1o)
2. [Material Design: la filosofía de Google](#2-material-design-la-filosof%C3%ADa-de-google)
    - 2.1. [Fundamentos: "quantum paper"](#21-fundamentos-quantum-paper)
    - 2.2. [Material Design 3 y Material You](#22-material-design-3-y-material-you)
    - 2.3. [Componentes característicos de Android](#23-componentes-caracter%C3%ADsticos-de-android)
    - 2.4. [Elevación y sombras](#24-elevaci%C3%B3n-y-sombras)
    - 2.5. [Tipografía en Material Design](#25-tipograf%C3%ADa-en-material-design)
    - 2.6. [Sistema de color dinámico](#26-sistema-de-color-din%C3%A1mico)
3. [Human Interface Guidelines: la filosofía de Apple](#3-human-interface-guidelines-la-filosof%C3%ADa-de-apple)
    - 3.1. [Los tres pilares de iOS](#31-los-tres-pilares-de-ios)
    - 3.2. [Navegación en iOS](#32-navegaci%C3%B3n-en-ios)
    - 3.3. [Componentes nativos de iOS](#33-componentes-nativos-de-ios)
    - 3.4. [Tipografía: San Francisco](#34-tipograf%C3%ADa-san-francisco)
    - 3.5. [Iconografía: SF Symbols](#35-iconograf%C3%ADa-sf-symbols)
4. [Comparativa directa: Android vs iOS](#4-comparativa-directa-android-vs-ios)
    - 4.1. [Navegación y patrones de interacción](#41-navegaci%C3%B3n-y-patrones-de-interacci%C3%B3n)
    - 4.2. [Componentes equivalentes](#42-componentes-equivalentes)
    - 4.3. [Controles de formulario](#43-controles-de-formulario)
    - 4.4. [Diálogos y alertas](#44-di%C3%A1logos-y-alertas)
5. [Anatomía de la pantalla móvil](#5-anatom%C3%ADa-de-la-pantalla-m%C3%B3vil)
    - 5.1. [Safe area y notch](#51-safe-area-y-notch)
    - 5.2. [Zonas de interacción: thumb zones](#52-zonas-de-interacci%C3%B3n-thumb-zones)
    - 5.3. [Tamaños mínimos de touch targets](#53-tama%C3%B1os-m%C3%ADnimos-de-touch-targets)
6. [Grids, spacing y unidades de medida](#6-grids-spacing-y-unidades-de-medida)
7. [Estrategia de diseño multiplataforma](#7-estrategia-de-dise%C3%B1o-multiplataforma)
8. [Casos prácticos: análisis de apps reales](#8-casos-pr%C3%A1cticos-an%C3%A1lisis-de-apps-reales)
9. [Recursos y bibliografía](#9-recursos-y-bibliograf%C3%ADa)

***

## 1. Introducción: ¿qué es un sistema de diseño?

Un **sistema de diseño** (design system) es mucho más que una simple guía de estilo visual. Es una colección completa y estructurada de principios, reglas, componentes reutilizables, patrones de interacción y mejores prácticas que rigen cómo se construye un producto digital.

### La analogía del LEGO

Imagina que estás construyendo una casa con piezas de LEGO:

- **Una guía de estilo simple** te diría: "Usa ladrillos rojos para las paredes y azules para el tejado"
- **Un sistema de diseño completo** te proporciona:
    - Los ladrillos prefabricados (ventanas, puertas, techos)
    - Las instrucciones de cómo encajan entre sí
    - Las especificaciones técnicas (cuánto peso soportan)
    - La filosofía de diseño (qué sensación deben transmitir)
    - Los principios de accesibilidad (altura mínima de puertas)


### ¿Por qué necesitamos sistemas de diseño?

En el desarrollo de software profesional, los sistemas de diseño garantizan:

1. **Consistencia visual** entre todas las pantallas de la aplicación
2. **Velocidad de desarrollo** al reutilizar componentes ya definidos
3. **Mejor comunicación** entre diseñadores y desarrolladores
4. **Experiencia de usuario predecible** que respeta las convenciones de cada plataforma
5. **Mantenibilidad** del código a largo plazo

> [!NOTE]
> En el mundo móvil actual, existen dos "religiones" visuales dominantes que todo diseñador de interfaces debe dominar: **Material Design** (Google/Android) y **Human Interface Guidelines** (Apple/iOS). Cada una tiene su propia filosofía, componentes y reglas.

***

## 2. Material Design: la filosofía de Google

Lanzado en 2014 y actualizado a **Material Design 3** (también conocido como **Material You**) en 2021, es el sistema de diseño oficial de Google para Android y productos web.

### 2.1. Fundamentos: "quantum paper"

Material Design se basa en una metáfora del mundo físico: imagina que la pantalla de tu móvil está formada por **láminas de papel mágico** (quantum paper).

**Propiedades del material:**

- El "papel" puede expandirse, contraerse y cambiar de forma
- Cada lámina tiene un grosor estándar de **1dp** (density-independent pixel)
- Las láminas pueden moverse en tres dimensiones (X, Y, Z)
- **La luz y las sombras son reales:** si un elemento está encima de otro (eje Z), debe proyectar sombra sobre el que está debajo
- Los elementos responden a la física: tienen inercia, rebotes sutiles y transiciones suaves

**Principios clave:**

1. **Material es la metáfora:** Inspirado en el estudio de papel y tinta, pero tecnológicamente avanzado
2. **Audaz, gráfico e intencional:** Inspirado en diseño gráfico impreso con uso deliberado de color, tipografía y espacio
3. **El movimiento proporciona significado:** El motion design refuerza la jerarquía y las relaciones espaciales

### 2.2. Material Design 3 y Material You

La última versión introduce el concepto de **personalización dinámica**:

```
Material Design 2 (2018-2021)
└─> Colores fijos definidos por el diseñador

Material Design 3 / Material You (2021-presente)
└─> Colores extraídos del fondo de pantalla del usuario
    └─> La app se adapta automáticamente a los gustos personales
```

**Color dinámico en acción:**

Si el usuario tiene un fondo de pantalla con tonos **azul oceánico** y **naranja atardecer**, el sistema operativo Android 12+ genera automáticamente una paleta de colores y la aplica a:

- Botones
- Barras de navegación
- Menús desplegables
- Notificaciones
- Incluso apps de terceros que soporten Material You

<img src="https://photos5.appleinsider.com/gallery/42079-81562-android-12-material-you-xl.jpg" alt="Ejemplo de cómo Android 12+ extrae colores del wallpaper y los aplica a toda la interfaz" style="max-width:400px"/>

> [!TIP]
> El color dinámico no es aleatorio. Android usa algoritmos de extracción de color que identifican los tonos dominantes y genera paletas armónicas siguiendo teoría del color (complementarios, análogos, tríadas).

### 2.3. Componentes característicos de Android

#### Floating Action Button (FAB)

El [**FAB**](https://m3.material.io/components/floating-action-button) es el componente más icónico de Material Design.

**Características:**

- Botón circular que "flota" sobre el contenido
- Posición típica: esquina inferior derecha
- Representa la **acción principal** de la pantalla actual
- Tiene elevación alta (proyecta sombra pronunciada)


**Ejemplo conceptual en XML:**

```xml
<com.google.android.material.floatingactionbutton.FloatingActionButton
    android:id="@+id/fab"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_gravity="bottom|end"
    android:layout_margin="16dp"
    android:contentDescription="@string/crear_nuevo"
    app:srcCompat="@drawable/ic_add" />
```

**Uso correcto:**

- En una app de correo: **"Redactar nuevo mensaje"**
- En una app de notas: **"Crear nueva nota"**
- En una app de fotos: **"Tomar foto"**

**Uso incorrecto:**

- Acciones destructivas (eliminar, cerrar)
- Acciones secundarias o poco frecuentes
- Múltiples FABs en la misma pantalla (confunde al usuario)


<img src="https://miro.medium.com/v2/resize:fit:1400/1*UCzqEAdZJPwzXzAv0n1PPg.png" alt="Ejemplos incorrectos de FAB" style="max-width:400px"/>

#### Cards (tarjetas)

<img src="https://lh3.googleusercontent.com/CKf_anfIz5fJnDPe9fEHT2Ct-Vd4NGDl14KgZ7YYFpknSaL2NHZsfm0HCMTHnQEtuwmeG-juQXoeYiQ4qHKWLkNtH8Gnz8TdEwokNNuZeNay7igGyS4=w1064-v0" alt="Cards" style="max-width:400px"/>

[Cars](https://m3.material.io/components/cards) son contenedores elevados con bordes redondeados que agrupan información relacionada.

```xml
<com.google.android.material.card.MaterialCardView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:cardCornerRadius="12dp"
    app:cardElevation="4dp"
    app:cardUseCompatPadding="true">
    
    <!-- Contenido de la tarjeta -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="16dp">
        
        <TextView
            android:text="Título de la tarjeta"
            android:textSize="20sp"
            android:textStyle="bold" />
        
        <TextView
            android:text="Descripción o contenido..."
            android:textSize="14sp" />
    </LinearLayout>
</com.google.android.material.card.MaterialCardView>
```


#### Navigation Drawer

[Navigation Drawer](https://m3.material.io/components/navigation-drawer) es el famoso **"menú hamburguesa"** (≡) que despliega un panel lateral desde la izquierda.

**Cuándo usarlo:**

- Apps con 5+ secciones principales
- Navegación entre categorías de contenido
- Acceso a configuración y perfil de usuario

**Cuándo NO usarlo:**

- Apps con 3-4 secciones (mejor usar Bottom Navigation)
- Acceso a acciones frecuentes (quedan muy ocultas)

>[!IMPORTANT]
> A partir de Material 3 Expressive (año 2025) se desaconseja el uso de Navigation Drawer, apostando por [Navigation Rail](https://m3.material.io/components/navigation-rail)


#### Bottom Navigation Bar

<img src="https://lh3.googleusercontent.com/14jax4TqtGJ1R7uteNETMHtrB4_83FOVLCsLW3VxWh17Mle2RiCPiUwPgN0hEXqkCr-281geh88fDMuEZyGVTyhZ_j8hO9RHElEa=w1064-v0" alt="Bottom Navigation Bar" style="max-width:400px"/>

Barra inferior con 3-5 iconos para cambiar entre secciones principales.

```xml
<com.google.android.material.bottomnavigation.BottomNavigationView
    android:id="@+id/bottom_navigation"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_gravity="bottom"
    app:menu="@menu/bottom_nav_menu" />
```

> [!IMPORTANT]
> Material Design recomienda usar [**Bottom Navigation**](https://m3.material.io/components/navigation-bar) en lugar de Drawer cuando la app tiene entre 3 y 5 secciones principales, ya que es más accesible (no requiere abrir un menú oculto).

### 2.4. Elevación y sombras

La **elevación** ([elevation](https://m3.material.io/styles/elevation)) es fundamental en Material Design. Define la jerarquía visual mediante el eje Z.

**Escala de elevación estándar:**


| Componente | Elevación (dp) | Uso |
| :-- | :-- | :-- |
| Bottom Navigation | 8dp | Navegación principal |
| App Bar (Toolbar) | 4dp | Cabecera de pantalla |
| Card (reposo) | 2dp | Contenedores de contenido |
| Card (hover) | 8dp | Al pasar el cursor/dedo |
| FAB (reposo) | 6dp | Acción principal |
| FAB (pressed) | 12dp | Al pulsar el botón |
| Dialog | 24dp | Diálogos modales |

**Implementación en código:**

```xml
<!-- La elevación genera sombra automáticamente en M2 y cambio de color en M3 -->
<Button
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Botón elevado"
    android:elevation="6dp" />
```

> [!WARNING]
> No abuses de las [sombras](https://m2.material.io/design/environment/light-shadows.html). Demasiada elevación en todos los elementos crea una interfaz caótica. Reserva elevaciones altas (>12dp) solo para elementos que realmente necesiten destacar.

### 2.5. Tipografía en Material Design

Material Design usa **Roboto** como tipografía principal, aunque Material You permite personalizar.

**Escala tipográfica de Material Design 3:**


| Estilo | Tamaño | Peso | Uso típico |
| :-- | :-- | :-- | :-- |
| Display Large | 57sp | Regular | Hero text (pantalla de bienvenida) |
| Display Medium | 45sp | Regular | Títulos principales |
| Display Small | 36sp | Regular | Títulos de sección |
| Headline Large | 32sp | Regular | Títulos destacados |
| Headline Medium | 28sp | Regular | Títulos de pantalla |
| Headline Small | 24sp | Regular | Subtítulos importantes |
| Title Large | 22sp | Medium | Títulos de tarjetas grandes |
| Title Medium | 16sp | Medium | Títulos de tarjetas |
| Title Small | 14sp | Medium | Títulos de listas |
| Body Large | 16sp | Regular | Texto principal largo |
| Body Medium | 14sp | Regular | Texto estándar |
| Body Small | 12sp | Regular | Texto secundario |
| Label Large | 14sp | Medium | Botones |
| Label Medium | 12sp | Medium | Chips, badges |
| Label Small | 11sp | Medium | Timestamps, metadatos |

**Ejemplo de implementación:**

```xml
<TextView
    android:text="Título de la pantalla"
    android:textAppearance="?attr/textAppearanceHeadlineMedium" />

<TextView
    android:text="Cuerpo del texto con información detallada..."
    android:textAppearance="?attr/textAppearanceBodyMedium" />
```


### 2.6. Sistema de color dinámico

Material You introduce **roles de color** en lugar de colores fijos:

```
Paleta generada del fondo de pantalla:
├── Primary (color principal de la marca)
│   ├── On Primary (texto sobre primary)
│   └── Primary Container (versión suave)
├── Secondary (color de acento)
│   ├── On Secondary
│   └── Secondary Container
├── Tertiary (color terciario)
├── Error (rojos para errores)
├── Surface (fondos de componentes)
└── Background (fondo de la app)
```

**Ejemplo de uso en código:**

```xml
<Button
    android:backgroundTint="?attr/colorPrimary"
    android:textColor="?attr/colorOnPrimary"
    android:text="Botón principal" />

<com.google.android.material.card.MaterialCardView
    app:cardBackgroundColor="?attr/colorSurfaceVariant">
    <!-- Contenido -->
</com.google.android.material.card.MaterialCardView>
```

> [!TIP]
> Al usar roles de color (colorPrimary, colorSurface) en lugar de valores hexadecimales fijos (\#FF5722), tu app se adapta automáticamente al tema del usuario (claro/oscuro) y a Material You.

***

## 3. Human Interface Guidelines: la filosofía de Apple

Las **Human Interface Guidelines** (HIG) son el sistema de diseño de Apple para iOS, iPadOS, watchOS y macOS. A diferencia de Material Design, iOS prioriza la **invisibilidad de la interfaz**.

### 3.1. Los tres pilares de iOS

<img src="https://www.jhavtech.com.au/wp-content/uploads/2025/06/Blog-1-Image-1-for-the-Body-1024x648.png.webp" alt="Pilares HIG" style="max-width:400px"/>

#### 1. Claridad (Clarity)

**Principio:** El texto debe ser perfectamente legible a cualquier tamaño, los iconos precisos y nítidos, y los adornos sutiles y apropiados.

- Tipografía con alto contraste
- Uso generoso de espacio en blanco (whitespace)
- Jerarquías visuales claras mediante tamaño y peso de fuente
- Los bordes y sombras son sutiles, nunca recargados

**Ejemplo visual:**

```
❌ MAL (poco claro):
[Botón con borde grueso, sombra pesada, texto pequeño en gris claro]

✅ BIEN (claridad iOS):
[Botón azul sistema con texto en negrita blanca, sin bordes visibles]
```


#### 2. Deferencia (Deference)

**Principio:** La interfaz debe ser "invisible" y permitir que el contenido del usuario sea la estrella.

- Uso de transparencias y efectos blur (desenfoque)
- Los controles UI se desvanecen cuando no se usan
- Animaciones fluidas que no distraen
- El contenido (fotos, textos, videos) ocupa el máximo espacio posible

**Características técnicas:**

- Navigation bars con efecto translúcido
- Toolbars que desaparecen al hacer scroll
- Fullscreen sin elementos UI cuando se visualiza contenido


#### 3. Profundidad (Depth)

**Principio:** Las capas visuales y el motion design comunican jerarquía y relaciones espaciales.

- En lugar de sombras duras (Material), iOS usa **capas translúcidas**
- Efecto **blur/frosted glass** (cristal esmerilado) para indicar superposición
- Transiciones que respetan la física (parallax, zoom realistas)
- Distintas "capas" de interfaz con diferente opacidad


<img src="https://i.sstatic.net/HZIGD.png" alt="depth" style="max-width:400px"/>


### 3.2. Navegación en iOS

Una de las diferencias más críticas con Android: **iOS no tiene botón físico ni virtual de "Atrás"**.

#### Navigation Bar (barra de navegación superior)

<img src="https://cdn.dribbble.com/userupload/5816676/file/original-9a85592a69b54a480ca6164655d5d449.png?resize=1024x768&vertical=center" alt="navigation bar" style="max-width:400px"/>

**Características:**

- Siempre presente en la parte superior
- Título centrado (por defecto) o alineado a la izquierda
- Botón "< Atrás" en la esquina superior izquierda
- El texto del botón atrás indica la pantalla previa (no solo "<")
- Botones de acción en la esquina superior derecha

**Ejemplo SwiftUI:**

```swift
NavigationView {
    VStack {
        Text("Contenido de la pantalla")
    }
    .navigationTitle("Configuración")
    .navigationBarTitleDisplayMode(.large)
    .toolbar {
        ToolbarItem(placement: .navigationBarTrailing) {
            Button("Guardar") {
                // Acción
            }
        }
    }
}
```


#### Tab Bar (barra de pestañas inferior)

<img src="https://spectrum.adobe.com/static/images1x/tab-bar_hero@2x_1649804213304.png" alt="tab bar" style="max-width:400px"/>

La navegación principal en iOS se hace mediante **Tab Bar** en la parte inferior.

**Reglas de diseño:**

- Mínimo 2, máximo 5 pestañas
- Iconos simples con etiqueta de texto debajo
- La pestaña activa se resalta (color + icono relleno)
- Nunca uses menú lateral (hamburger menu) para navegación principal en iOS

**Ejemplo SwiftUI:**

```swift
TabView {
    HomeView()
        .tabItem {
            Label("Inicio", systemImage: "house.fill")
        }
    
    SearchView()
        .tabItem {
            Label("Buscar", systemImage: "magnifyingglass")
        }
    
    ProfileView()
        .tabItem {
            Label("Perfil", systemImage: "person.fill")
        }
}
```

> [!IMPORTANT]
> **Diferencia crítica:** Android usa Navigation Drawer (menú lateral) como patrón principal. iOS usa Tab Bar (pestañas inferiores). Nunca confundas estos patrones en tus diseños.

### 3.3. Componentes nativos de iOS

#### Lists (listas)

<img src="https://assets.alexandria.kodeco.com/books/35e7aced11dae16036897e3c91d92b539052d652baffbb54bb290d6ed22fdc9b/images/2bb4ebf85dab8364cfa83543f3e32ad6/original.png" alt="swiftui list" style="max-width:400px"/>

El componente más usado en iOS. Limpio, eficiente, reconocible.

```swift
List {
    Section(header: Text("General")) {
        NavigationLink(destination: NotificationsView()) {
            Label("Notificaciones", systemImage: "bell")
        }
        NavigationLink(destination: PrivacyView()) {
            Label("Privacidad", systemImage: "hand.raised")
        }
    }
    
    Section(header: Text("Cuenta")) {
        Button("Cerrar sesión") {
            // Acción
        }
        .foregroundColor(.red)
    }
}
.listStyle(.insetGrouped)
```


#### Action Sheets

<img src="https://i.ytimg.com/vi/pYOjDPNlWak/hq720.jpg?sqp=-oaymwEhCK4FEIIDSFryq4qpAxMIARUAAAAAGAElAADIQj0AgKJD&rs=AOn4CLAV0she7fYVsTQ9UEBvi8BN2tuUjA" alt="action sheets" style="max-width:400px"/>

Los diálogos en iOS **aparecen desde abajo** como una cortina.

```swift
.actionSheet(isPresented: $showingOptions) {
    ActionSheet(
        title: Text("Opciones"),
        message: Text("Elige una acción"),
        buttons: [
            .default(Text("Guardar")) { },
            .default(Text("Compartir")) { },
            .destructive(Text("Eliminar")) { },
            .cancel()
        ]
    )
}
```

> [!IMPORTANT]
> En Material Expressive 3 tenemos algo similar con los [Bottom sheets](https://m3.material.io/components/bottom-sheets)


#### SF Symbols

Sistema de iconos vectoriales de Apple con más de 5000 símbolos.

**Ventajas:**

- Se adaptan automáticamente al tamaño del texto
- Tienen variantes de peso (ultralight, regular, bold, black)
- Versiones outlined y filled
- Soporte para animaciones

```swift
Image(systemName: "heart.fill")
    .foregroundColor(.red)
    .font(.system(size: 40, weight: .bold))
```


### 3.4. Tipografía: San Francisco

**San Francisco** (SF Pro) es la tipografía oficial de Apple.

**Características únicas:**

- **Tipografía adaptativa:** cambia automáticamente su forma según el tamaño
    - **SF Pro Text:** para tamaños pequeños (< 20pt) - más abierta y legible
    - **SF Pro Display:** para tamaños grandes (≥ 20pt) - más estilizada
- Soporte para Dynamic Type (tamaños ajustables por preferencias de usuario)
- Tracking (espaciado entre letras) dinámico

**Escala tipográfica de iOS:**


| Estilo | Tamaño | Peso | Uso |
| :-- | :-- | :-- | :-- |
| Large Title | 34pt | Regular/Bold | Títulos de pantalla principal |
| Title 1 | 28pt | Regular/Bold | Títulos destacados |
| Title 2 | 22pt | Regular/Bold | Subtítulos de sección |
| Title 3 | 20pt | Regular/Bold | Títulos de grupo |
| Headline | 17pt | Semi Bold | Encabezados de tarjetas |
| Body | 17pt | Regular | Texto principal |
| Callout | 16pt | Regular | Texto secundario |
| Subhead | 15pt | Regular | Subtextos |
| Footnote | 13pt | Regular | Notas al pie |
| Caption 1 | 12pt | Regular | Metadatos pequeños |
| Caption 2 | 11pt | Regular | Timestamps, etiquetas |

```swift
Text("Título grande")
    .font(.largeTitle)
    .fontWeight(.bold)

Text("Cuerpo de texto normal")
    .font(.body)

Text("Nota secundaria")
    .font(.caption)
    .foregroundColor(.secondary)
```


### 3.5. Iconografía: SF Symbols

Apple proporciona más de **5000 iconos** optimizados que se integran perfectamente con San Francisco.

**Variantes disponibles:**

```
heart → heart.fill
star → star.fill → star.leadinghalf.filled
person → person.fill → person.circle → person.circle.fill
```

**Ejemplo de uso:**

```swift
// Icono simple
Label("Favoritos", systemImage: "star.fill")

// Icono con personalización
Image(systemName: "person.crop.circle")
    .symbolRenderingMode(.multicolor)
    .font(.system(size: 50))
    .symbolEffect(.bounce, options: .repeating)
```

> [!TIP]
> SF Symbols 5 (iOS 17+) incluye animaciones predefinidas: bounce, pulse, scale, replace. Aprovéchalas para dar feedback visual sin necesidad de animar manualmente.

***

## 4. Comparativa directa: Android vs iOS

Esta sección es **crítica** para vuestro trabajo como diseñadores de interfaces. Respetar estas diferencias es lo que separa un diseño amateur de uno profesional.

### 4.1. Navegación y patrones de interacción

| Elemento | Android (Material) | iOS (HIG) |
| :-- | :-- | :-- |
| **Navegación principal** | Bottom Navigation (3-5 items) o Navigation Drawer (menú lateral) | Tab Bar inferior (2-5 items) - **Nunca drawer** |
| **Acción primaria** | FAB (Floating Action Button) en esquina inferior derecha | Botón en Navigation Bar superior derecha |
| **Botón "Atrás"** | Botón del sistema (hardware/software) + Flecha arriba-izquierda | "< [Nombre Pantalla]" en Navigation Bar arriba-izquierda |
| **Búsqueda** | Search bar persistente o icono lupa en App Bar | Search bar que se oculta al hacer scroll |
| **Menú contextual** | Three-dot menu (⋮) arriba derecha | Sheet desde abajo con opciones listadas |
| **Pestañas de contenido** | Tabs debajo del App Bar (scrollable si >5) | Segmented Control (máximo 5 opciones) |

**Visualización conceptual:**

```
ANDROID                           iOS
┌─────────────────────┐          ┌─────────────────────┐
│ [☰] App Title  [⋮]  │          │ < Atrás    Título   │
├─────────────────────┤          │                [+]  │
│ [Tab1][Tab2][Tab3]  │          ├─────────────────────┤
├─────────────────────┤          │                     │
│                     │          │    Contenido        │
│    Contenido        │          │                     │
│                     │          │                     │
│                     │          ├─────────────────────┤
│              [FAB]  │          │[🏠][🔍][➕][👤][⚙️]│
└─────────────────────┘          └─────────────────────┘
Bottom Nav Bar                    Tab Bar
```


### 4.2. Componentes equivalentes

| Función | Android | iOS | Diferencia visual clave |
| :-- | :-- | :-- | :-- |
| **Tarjeta de contenido** | Card (bordes redondeados + sombra) | Card-like view (sin sombra, borde sutil) | Android: elevación visible; iOS: plano |
| **Alerta simple** | Snackbar (abajo, autohide) | Toast-like / Banner (arriba) | Posición y duración diferente |
| **Diálogo de confirmación** | AlertDialog (centro pantalla) | Alert (centro) o Action Sheet (desde abajo) | Action Sheet es más común en iOS |
| **Campo de texto** | TextInputLayout + EditText | TextField / TextEditor | Android muestra hint flotante, iOS placeholder |
| **Switch/Toggle** | Switch (con pista rectangular) | Toggle (pista más redondeada) | Aspecto visual distintivo |
| **Lista con avatar** | RecyclerView + CardView | List con disclosure indicators | iOS usa chevron ">" a la derecha |
| **Barra de carga** | Linear/Circular ProgressIndicator | ProgressView / Activity Indicator | iOS spinner es más minimalista |

### 4.3. Controles de formulario

#### Checkbox vs Checkmark

```
ANDROID                    iOS
□ Opción 1 (unchecked)    Opción 1
☑ Opción 2 (checked)      Opción 2 ✓ (checkmark azul a la derecha)

Radio buttons:            Selection List:
◯ Opción A                Opción A
● Opción B                Opción B ✓
◯ Opción C                Opción C
```

**Implementación Android:**

```xml
<CheckBox
    android:text="Acepto los términos"
    android:checked="false" />

<RadioGroup>
    <RadioButton android:text="Opción 1" />
    <RadioButton android:text="Opción 2" />
</RadioGroup>
```

**Implementación iOS:**

```swift
// iOS no tiene CheckBox nativo, se usa Toggle o checkmark en List
List {
    Toggle("Notificaciones", isOn: $isEnabled)
    
    ForEach(options, id: \.self) { option in
        Button {
            selectedOption = option
        } label: {
            HStack {
                Text(option)
                Spacer()
                if selectedOption == option {
                    Image(systemName: "checkmark")
                        .foregroundColor(.blue)
                }
            }
        }
    }
}
```


### 4.4. Diálogos y alertas

**Android AlertDialog:**

```xml
<!-- Aparece centrado con fondo oscurecido -->
MaterialAlertDialogBuilder(context)
    .setTitle("Eliminar elemento")
    .setMessage("Esta acción no se puede deshacer")
    .setPositiveButton("Eliminar") { dialog, _ ->
        // Acción
    }
    .setNegativeButton("Cancelar", null)
    .show()
```

> [!NOTE]
> Más información sobre [Dialogs](https://m3.material.io/components/dialogs)

**iOS Alert:**

```swift
// Centrado, estilo iOS distintivo
.alert("Eliminar elemento", isPresented: $showAlert) {
    Button("Eliminar", role: .destructive) {
        // Acción
    }
    Button("Cancelar", role: .cancel) { }
} message: {
    Text("Esta acción no se puede deshacer")
}
```

**iOS Action Sheet (más común):**

```swift
// Aparece desde abajo como cortina
.confirmationDialog("Opciones", isPresented: $showSheet) {
    Button("Guardar") { }
    Button("Compartir") { }
    Button("Eliminar", role: .destructive) { }
    Button("Cancelar", role: .cancel) { }
}
```

> [!WARNING]
> **Error común:** Usar Action Sheets de iOS en diseños Android, o AlertDialogs centrados en iOS. Cada plataforma tiene sus propias convenciones que los usuarios esperan encontrar.

***

## 5. Anatomía de la pantalla móvil

Diseñar para móvil requiere entender las restricciones físicas de los dispositivos modernos.

### 5.1. Safe area y notch

Los smartphones modernos tienen pantallas que llegan hasta los bordes, pero incluyen recortes para cámaras y sensores.

**Zonas de una pantalla moderna:**

```
┌─────────────────────────┐
│   Status Bar (20-44pt)  │ ← Hora, batería, señal
│  ┌─────────────────┐    │
│  │    [notch]      │    │ ← Cámara frontal (iPhone)
│  └─────────────────┘    │
├─────────────────────────┤
│                         │
│   SAFE AREA             │ ← Contenido interactivo AQUÍ
│   (diseña aquí)         │
│                         │
│                         │
├─────────────────────────┤
│   Home Indicator        │ ← Barra para volver (iOS)
│   ─────────             │
└─────────────────────────┘
```

**Safe Area insets (iOS):**


| Zona | Margen típico |
| :-- | :-- |
| Top (con notch) | 44-59pt |
| Top (sin notch) | 20pt |
| Bottom (con home indicator) | 34pt |
| Bottom (sin home indicator) | 0pt |
| Lados (pantallas curvadas) | 0pt (pero considera esquinas redondeadas) |

**Implementación SwiftUI:**

```swift
VStack {
    Text("Contenido")
}
.padding(.horizontal) // Respeta safe area lateral
.ignoresSafeArea(.keyboard) // Pero permite teclado encima
```

**Implementación Android:**

```xml
<androidx.constraintlayout.widget.ConstraintLayout
    android:fitsSystemWindows="true">
    <!-- El contenido respeta system bars automáticamente -->
</androidx.constraintlayout.widget.ConstraintLayout>
```

> [!IMPORTANT]
> **Regla de oro:** Nunca pongas contenido interactivo (botones, formularios) pegado a los bordes absolutos. El usuario puede tocar accidentalmente el home indicator o los gestos del sistema.

### 5.2. Zonas de interacción: thumb zones

No todas las áreas de la pantalla son igual de cómodas para el pulgar.

**Mapa de calor de accesibilidad:**

<img src="https://media.licdn.com/dms/image/v2/D4D22AQFplsEyquUblQ/feedshare-shrink_800/B4DZboE6PPIEAk-/0/1747650324214?e=2147483647&v=beta&t=gBVJmt5r3_1mQ-WttLK0d1TFdo9n1WT1fyYC5l1Kw6E" style="max-width: 400px" />

**Implicaciones de diseño:**

✅ **Pon en la parte inferior:**

- Acciones principales (botones de envío, navegación)
- FAB de Android (ya está en la esquina inferior)
- Tab Bar de iOS (nativo en la parte inferior)

❌ **Evita poner en la parte superior:**

- Acciones críticas frecuentes
- Controles que requieren precisión
- Múltiples botones pequeños juntos

**Consideraciones para tablets y plegables:**

En dispositivos grandes (>10"), las manos sujetan los bordes:


<img src="https://miro.medium.com/v2/resize:fit:4800/format:webp/0*GKzHSK9q0tNdRSRV" style="max-width: 400px" />


### 5.3. Tamaños mínimos de touch targets

El dedo humano es impreciso. Las guías oficiales definen tamaños mínimos.
<img src="https://blog.logrocket.com/ux-design-all-accessible-touch-target-sizes/attachment/various-icons-and-target-sizes/">


**Especificaciones oficiales:**


| Plataforma | Tamaño mínimo | Tamaño recomendado | Espacio entre elementos |
| :-- | :-- | :-- | :-- |
| **Android** | 48x48 dp | 48x48 dp | 8 dp |
| **iOS** | 44x44 pt | 44x44 pt | 8 pt |
| **Web (WCAG)** | 44x44 px | 48x48 px | - |

> [!NOTE]
> **dp (density-independent pixels) vs pt (points):** Ambas son unidades independientes de la densidad de pantalla. 1dp en Android ≈ 1pt en iOS en dispositivos de densidad similar.

**Ejemplo Android:**

```xml
<!-- Aunque el icono visual sea 24dp, el área táctil debe ser 48dp -->
<ImageButton
    android:layout_width="48dp"
    android:layout_height="48dp"
    android:padding="12dp"
    android:src="@drawable/ic_delete"
    android:contentDescription="Eliminar" />
```

**Ejemplo iOS:**

```swift
Button {
    // Acción
} label: {
    Image(systemName: "trash")
        .font(.system(size: 20))
}
.frame(width: 44, height: 44) // Área táctil mínima
```

**Violaciones comunes del tamaño mínimo:**

```
❌ Botón "X" de cerrar: 20x20 dp (muy pequeño)
✅ Botón "X" de cerrar: 48x48 dp con icono 20x20 centrado

❌ Iconos en barra inferior: 32x32 dp con 0dp de padding
✅ Iconos en barra inferior: 24x24 dp dentro de 48x48 dp

❌ Links en texto corrido: altura de línea 18sp
✅ Links en texto corrido: padding vertical añadido para alcanzar 48dp
```


***

## 6. Grids, spacing y unidades de medida

### Sistema de espaciado

Ambos sistemas usan **grids de 8dp/8pt** como base.

**Escala de espaciado Material Design:**

```
4dp  = Espacio mínimo entre elementos relacionados
8dp  = Espacio estándar entre componentes
16dp = Márgenes laterales de pantalla (móvil)
24dp = Márgenes laterales de pantalla (tablet)
32dp = Separación entre secciones mayores
```

**Ejemplo de implementación:**

```xml
<!-- Android: todos los márgenes son múltiplos de 8 -->
<TextView
    android:layout_marginTop="16dp"
    android:layout_marginStart="16dp"
    android:padding="8dp" />
```

```swift
// iOS: SwiftUI usa grids implícitos
VStack(spacing: 16) { // 16pt entre elementos
    Text("Título")
    Text("Descripción")
}
.padding(.horizontal, 16) // Márgenes laterales
```


### Grid responsivo

**Android (columnas de 4dp):**


| Dispositivo | Columnas | Márgenes |
| :-- | :-- | :-- |
| Teléfono (< 600dp) | 4 | 16dp |
| Tablet (600-840dp) | 8 | 24dp |
| Desktop (> 840dp) | 12 | 24dp |

**iOS (puntos fijos):**


| Dispositivo | Márgenes recomendados |
| :-- | :-- |
| iPhone (compact) | 16-20pt |
| iPad (regular) | 20-24pt |


***

## 7. Estrategia de diseño multiplataforma

Cuando diseñas una app para Android e iOS simultáneamente, surge la pregunta: **¿diseño idéntico o adaptado?**

### La decisión correcta: identidad + navegación nativa

✅ **Mantén consistente (Brand Identity):**

- Logo y colores de marca
- Ilustraciones y fotografías
- Tono de voz y copy
- Iconos personalizados de tu marca
- Características únicas de tu producto

✅ **Adapta a cada plataforma (Native Navigation):**

- Posición de la navegación principal (Drawer vs Tab Bar)
- Botón de acción principal (FAB vs Toolbar button)
- Diálogos y alertas (Dialog vs Action Sheet)
- Gestos y comportamientos del sistema
- Tipografía del sistema (Roboto vs San Francisco)


### Casos de estudio reales

**Instagram:**

- ✅ **Consistente:** Logo, colores, grid de fotos, filtros
- ✅ **Adaptado:** Tab bar inferior en ambos, pero iconos y espaciado siguen las guías de cada plataforma

**WhatsApp:**

- ✅ **Consistente:** Verde característico, burbujas de chat, iconos de estado
- ✅ **Adaptado:**
    - Android: Tabs arriba (Chats/Estado/Llamadas) + FAB
    - iOS: Tab bar abajo + botón "Nuevo chat" arriba derecha

**Spotify:**

- ✅ **Consistente:** Verde neón, carátulas de álbumes, reproductores
- ✅ **Adaptado:**
    - Android: Navigation drawer lateral
    - iOS: Tab bar inferior

> [!TIP]
> **Regla práctica:** Si un usuario de Android o iOS nota que tu app "se siente rara", probablemente has violado las convenciones de navegación de su plataforma. El contenido puede ser idéntico, pero los patrones de interacción deben ser nativos.

### Errores que debes evitar

❌ **Usar hamburger menu (≡) como navegación principal en iOS**

- Los usuarios de iPhone esperan Tab Bar inferior
- El menú lateral viola las HIG

❌ **Poner FAB en iOS**

- El FAB es un elemento distintivo de Material Design
- iOS usa botones en la Navigation Bar

❌ **Ignorar el gesto de swipe-back en iOS**

- Los usuarios de iPhone esperan poder deslizar desde el borde izquierdo para volver
- No bloquees este gesto con tu propia navegación

❌ **Usar Action Sheets de iOS en Android**

- Los diálogos de Android aparecen centrados, no desde abajo
- Usar patrones incorrectos confunde al usuario

***

## 8. Casos prácticos: análisis de apps reales

### Actividad 1: auditoría de componentes

**Objetivo:** Identificar componentes de Material Design en apps Android populares.

**Instrucciones:**

1. Abre **Gmail** en un dispositivo Android (o captura de pantalla)
2. Identifica y documenta:
    - ¿Dónde está el FAB? ¿Qué acción representa?
    - Encuentra 3 Cards y describe su elevación
    - ¿Los botones siguen la paleta Material You?
    - ¿Qué tipografía usan los títulos?

**Entrega esperada:**

- Captura de pantalla anotada
- Lista de componentes identificados con su nombre técnico
- Análisis de si la app sigue correctamente Material Design 3


### Actividad 2: traducción de interfaces

**Ejercicio: "El traductor de interfaces"**

**Contexto:** Has diseñado una pantalla de perfil de usuario para Android. Ahora debes adaptarla para iOS.

**Pantalla Android original:**

```
┌─────────────────────────┐
│ [☰] Mi Perfil      [⋮]  │
├─────────────────────────┤
│                         │
│    [Foto perfil]        │
│    Nombre Usuario       │
│    @username            │
│                         │
│ ┌─────────────────────┐ │
│ │ Seguidores    1.2K  │ │ (Card con elevación)
│ │ Seguidos      450   │ │
│ └─────────────────────┘ │
│                         │
│ ┌─────────────────────┐ │
│ │ Publicaciones       │ │ (Card)
│ │ - Post 1            │ │
│ │ - Post 2            │ │
│ └─────────────────────┘ │
│                         │
│                  [FAB]  │
└─────────────────────────┘
  [🏠][🔍][➕][❤️][👤]    ← Bottom Nav
```

**Tu tarea:** Rediseñar esta pantalla para iOS respetando HIG.

**Checklist de adaptación:**

- [ ] Eliminar menú hamburguesa (≡)
- [ ] Mover navegación a Tab Bar inferior
- [ ] Convertir FAB en botón Navigation Bar
- [ ] Eliminar sombras pesadas de las Cards
- [ ] Centrar título en Navigation Bar
- [ ] Añadir botón "< Atrás" arriba izquierda
- [ ] Usar SF Symbols en lugar de Material Icons
- [ ] Ajustar tipografía a San Francisco

**Entrega:** Wireframe dibujado a mano o en Figma.

***

## 9. Recursos y bibliografía

### Documentación oficial

**Material Design:**

- Web oficial: [m3.material.io](https://m3.material.io)
- Guía de color: [material.io/design/color](https://material.io/design/color)
- Componentes: [m3.material.io/components](https://m3.material.io/components)
- Blog de Material Design: [material.io/blog](https://material.io/blog)

**Apple Human Interface Guidelines:**

- Web oficial: [developer.apple.com/design/human-interface-guidelines](https://developer.apple.com/design/human-interface-guidelines)
- iOS específico: [developer.apple.com/design/human-interface-guidelines/ios](https://developer.apple.com/design/human-interface-guidelines/ios)
- SF Symbols: [developer.apple.com/sf-symbols](https://developer.apple.com/sf-symbols)


### Herramientas de diseño

**Figma Community:**

- Material 3 Design Kit: buscar "Material 3 Design Kit" en Figma Community
- iOS 17 UI Kit: buscar "iOS 17 UI Kit" o "Apple Design Resources"

**Plugins de Figma útiles:**

- **Material Theme Builder:** genera paletas Material You automáticamente
- **Stark:** verifica contraste de color y accesibilidad
- **Iconify:** acceso a Material Icons y SF Symbols
- **Auto Layout Pro:** mejora el uso de Auto Layout


### Apps recomendadas para estudio

**Ejemplos de Material Design bien implementado:**

- Google Photos
- Gmail
- Google Maps
- Telegram (versión Android)

**Ejemplos de HIG bien implementado:**

- Apple Music
- Safari
- Configuración (Settings)
- Salud (Health)


### Lecturas complementarias

- **"Refactoring UI"** - Adam Wathan \& Steve Schoger (principios de diseño UI prácticos)
- **"Don't Make Me Think"** - Steve Krug (usabilidad fundamental)
- **Blog de Material Design:** actualizaciones y casos de estudio


### Canales de YouTube

- **Figma Official:** tutoriales de diseño de interfaces
- **Google Design:** charlas sobre Material Design
- **Apple Developer:** WWDC sessions sobre HIG

***

## Conclusión

Dominar Material Design y Human Interface Guidelines no es memorizar reglas arbitrarias, es **entender las razones** detrás de cada decisión de diseño.

**Puntos clave para recordar:**

1. **Material Design** se basa en la metáfora del papel (elevación, sombras, movimiento físico)
2. **iOS HIG** se basa en claridad, deferencia y profundidad (interfaces invisibles, contenido primero)
3. **La navegación** es la diferencia más crítica entre plataformas (Drawer/Bottom Nav vs Tab Bar)
4. **Respeta las convenciones nativas** pero mantén la identidad de marca consistente
5. **Los touch targets mínimos** (48dp/44pt) no son sugerencias, son requisitos de accesibilidad
6. **Diseñar para móvil** requiere entender anatomía física (safe areas, thumb zones, notches)

> [!IMPORTANT]
> Como futuros desarrolladores de aplicaciones móviles, vuestro conocimiento de estos sistemas de diseño será evaluado en entrevistas de trabajo. Las empresas buscan profesionales que entiendan que **el diseño no es decoración, es arquitectura de la experiencia de usuario**.

En el próximo tema (Tema 14), aplicaréis estos conocimientos en **Figma** para crear prototipos interactivos de alta fidelidad que respeten estas guías.