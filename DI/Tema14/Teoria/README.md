# TEMA 14: Prototipado avanzado y accesibilidad universal

## Tabla de contenidos

1. [Introducción: del papel al píxel](#1-introducci%C3%B3n-del-papel-al-p%C3%ADxel)
2. [Niveles de fidelidad en diseño](#2-niveles-de-fidelidad-en-dise%C3%B1o)
    - 2.1. [Wireframes: el esqueleto](#21-wireframes-el-esqueleto)
    - 2.2. [Mockups: el diseño visual](#22-mockups-el-dise%C3%B1o-visual)
    - 2.3. [Prototipos interactivos: la simulación](#23-prototipos-interactivos-la-simulaci%C3%B3n)
3. [Figma: la herramienta profesional](#3-figma-la-herramienta-profesional)
    - 3.1. [Conceptos fundamentales](#31-conceptos-fundamentales)
    - 3.2. [Componentes: diseño modular](#32-componentes-dise%C3%B1o-modular)
    - 3.3. [Auto Layout: el layout engine](#33-auto-layout-el-layout-engine)
    - 3.4. [Variantes de componentes](#34-variantes-de-componentes)
    - 3.5. [Estilos y design tokens](#35-estilos-y-design-tokens)
4. [Prototipado interactivo](#4-prototipado-interactivo)
    - 4.1. [Conexiones y flujos](#41-conexiones-y-flujos)
    - 4.2. [Smart Animate](#42-smart-animate)
    - 4.3. [Overlays y modales](#43-overlays-y-modales)
    - 4.4. [Estados interactivos](#44-estados-interactivos)
5. [Developer handoff](#5-developer-handoff)
6. [Accesibilidad universal (A11y)](#6-accesibilidad-universal-a11y)
    - 6.1. [Principios WCAG 2.1](#61-principios-wcag-21)
    - 6.2. [Accesibilidad visual](#62-accesibilidad-visual)
    - 6.3. [Accesibilidad motora](#63-accesibilidad-motora)
    - 6.4. [Accesibilidad cognitiva](#64-accesibilidad-cognitiva)
    - 6.5. [Lectores de pantalla](#65-lectores-de-pantalla)
7. [Herramientas de auditoría](#7-herramientas-de-auditor%C3%ADa)
8. [Casos prácticos integrados](#8-casos-pr%C3%A1cticos-integrados)
9. [Recursos y bibliografía](#9-recursos-y-bibliograf%C3%ADa)

***

## 1. Introducción: del papel al píxel

En el desarrollo de software profesional, **jamás se empieza a programar sin un plano detallado**. Esta afirmación no es exageración: el coste de corregir un error en la fase de diseño es aproximadamente **100 veces menor** que corregirlo una vez que el código está escrito, compilado y desplegado en producción.

### El coste real de no prototipar

Imaginad este escenario real en una empresa:

```
SIN PROTOTIPO:
1. Desarrollador programa pantalla de login (4 horas)
2. Cliente la ve y dice: "No, el botón debe estar arriba"
3. Refactorizar código + layouts + tests (2 horas)
4. Cliente: "Ahora quiero que tenga Google Sign-In"
5. Integrar API + rehacer UI (6 horas)
TOTAL: 12 horas de desarrollo

CON PROTOTIPO:
1. Diseñador crea 3 variantes en Figma (30 minutos)
2. Cliente elige una y solicita cambios (10 minutos más)
3. Desarrollador implementa diseño final (4 horas)
TOTAL: 4.5 horas + diseño validado antes de programar
```


### Objetivos de este tema

Al finalizar este tema, seréis capaces de:

1. Crear prototipos de alta fidelidad que se comportan como apps reales
2. Auditar interfaces para garantizar accesibilidad universal
3. Usar Figma como herramienta de comunicación con desarrolladores
4. Aplicar las normativas WCAG 2.1 en diseños móviles
5. Identificar y corregir barreras de accesibilidad visual, motora y cognitiva

> [!IMPORTANT]
> Este tema conecta el diseño visual (Tema 13) con la ingeniería de software. Aprenderéis que "hacer dibujitos" en Figma tiene una estructura lógica (Auto Layout) que se traduce directamente a código, y que la accesibilidad no es solo "ética", sino un requisito técnico y legal.

***

## 2. Niveles de fidelidad en diseño

No todos los diseños sirven para el mismo propósito. Debemos distinguir tres fases claramente diferenciadas.

### 2.1. Wireframes: el esqueleto

**Definición:** Representación básica de la estructura y distribución de elementos, sin estilo visual.

**Características:**

- **Monocromáticos** (grises, blancos, negros)
- **Sin imágenes reales** (se usan placeholders)
- **Tipografía simple** (sin fuentes de marca)
- **Iconos básicos** (cuadrados, círculos para representar)
- **Enfoque:** Navegación y jerarquía de información

**Herramientas:**

- Papel y lápiz (ideal para sesiones de brainstorming)
- Pizarra blanca
- Balsamiq (wireframing específico)
- Figma en modo low-fidelity

**Ejemplo visual de wireframe:**

```
┌─────────────────────────┐
│ [Logo]    [☰ Menú]      │  ← Header simple
├─────────────────────────┤
│                         │
│  [Imagen grande]        │  ← Placeholder de imagen
│                         │
├─────────────────────────┤
│  Título del artículo    │  ← Texto sin estilo
│                         │
│  Lorem ipsum dolor sit  │  ← Cuerpo de texto
│  amet, consectetur...   │
│                         │
│  [Botón Acción]         │  ← Botón simple
│                         │
└─────────────────────────┘
```

**Cuándo usar wireframes:**

- Fase inicial de proyecto (validar ideas rápidamente)
- Presentaciones a stakeholders no técnicos
- Testeo de flujos de navegación (card sorting)
- Workshops de co-diseño con clientes

> [!TIP]
> Los wireframes deben ser deliberadamente "feos". Si se ven muy bonitos, el cliente se centrará en comentar los colores en lugar de validar la estructura de navegación.

### 2.2. Mockups: el diseño visual

**Definición:** Diseño visual estático con todos los elementos gráficos finales aplicados.

**Características:**

- **Colores finales** siguiendo el sistema de diseño (Material/HIG)
- **Tipografías reales** (Roboto, San Francisco, fuentes de marca)
- **Imágenes y fotografías** reales o muy cercanas al contenido final
- **Iconografía definitiva** (Material Icons, SF Symbols, custom icons)
- **Sombras, elevaciones, bordes redondeados** según especificaciones
- **Sin interacción** (es una imagen estática)

**Objetivo:** Validar la identidad visual antes de invertir tiempo en hacer el diseño interactivo.

**Herramientas:**

- Figma (mayoritario en la industria)
- Adobe XD
- Sketch (solo macOS)
- Photoshop (legacy, en desuso para UI)

**Del wireframe al mockup:**

```
WIREFRAME                    MOCKUP
┌─────────────┐             ┌─────────────────────────┐
│ [Logo] [☰]  │    →        │ [Logo color] [≡ azul]   │
├─────────────┤             │                         │
│             │             │  ╔═══════════════════╗  │
│ [Imagen]    │             │  ║  Foto real HD     ║  │
│             │             │  ║  con filtro       ║  │
│ Título      │             │  ╚═══════════════════╝  │
│ Texto...    │             │                         │
│ [Botón]     │             │  "Explora destinos"     │
│             │             │  Roboto Bold 24sp       │
└─────────────┘             │                         │
                            │  Descripción texto...   │
                            │  Roboto Regular 16sp    │
                            │                         │
                            │  ┏━━━━━━━━━━━━━━━━━┓  │
                            │  ┃  VER MÁS INFO   ┃  │
                            │  ┗━━━━━━━━━━━━━━━━━┛  │
                            │  Material Button       │
                            └─────────────────────────┘
```


### 2.3. Prototipos interactivos: la simulación

**Definición:** Mockup con capacidad de simular interacciones reales (clics, gestos, transiciones).

**Características:**

- **Hotspots clicables:** Áreas que desencadenan acciones
- **Transiciones animadas:** Cambios de pantalla con efectos
- **Scrolls funcionales:** Listas que se pueden desplazar
- **Estados de componentes:** Botones en hover, pressed, disabled
- **Flujos completos:** Desde login hasta confirmación de compra
- **Se puede "navegar"** como una app real sin escribir código

**Objetivo:** Validar la experiencia de usuario (UX) antes de escribir una sola línea de código.

**Ventajas del prototipado:**


| Sin prototipo | Con prototipo |
| :-- | :-- |
| El cliente imagina el resultado | El cliente **ve y prueba** el resultado |
| Feedback en fase de desarrollo (caro) | Feedback en fase de diseño (barato) |
| Cambios requieren refactorizar código | Cambios son mover elementos en Figma |
| Testing de UX con app compilada | Testing de UX con prototipo Figma |
| 100% del tiempo en implementación | 20% diseño + 80% implementación eficiente |

> [!NOTE]
> Los grandes equipos de producto (Google, Apple, Airbnb) no escriben código de producción hasta que el prototipo ha sido validado por usuarios reales en sesiones de testing. Esto reduce el desperdicio de recursos en un 70-80%.

***

## 3. Figma: la herramienta profesional

**Figma** se ha convertido en el estándar mundial para diseño de interfaces porque funciona en el navegador, es colaborativo en tiempo real y permite el "developer handoff" (entrega a desarrolladores).

### 3.1. Conceptos fundamentales

#### Frames vs Groups

**Frames:**

- Contenedores inteligentes (equivalente a `ViewGroup` en Android o `View` en SwiftUI)
- Pueden tener Auto Layout (sistema de constraints)
- Definen límites de clipping (contenido que se sale se oculta)
- Pueden tener fondo, borde, sombras propias
- **Uso:** Pantallas, tarjetas, botones, cualquier componente

**Groups:**

- Agrupación simple de elementos
- No tienen propiedades de layout
- Transparentes al clipping (no cortan contenido desbordado)
- **Uso:** Agrupar iconos relacionados, elementos decorativos

```
Frame (como un <LinearLayout> de Android)
├── Frame hijo 1 (como un <CardView>)
│   ├── Text "Título"
│   └── Image "foto.png"
└── Frame hijo 2 (otro <CardView>)
    ├── Text "Subtítulo"
    └── Button "Acción"

vs.

Group (solo agrupa visualmente)
├── Icon "estrella"
├── Icon "estrella"
└── Icon "estrella"
(no tiene comportamiento de layout)
```


#### Capas y jerarquía

Figma funciona con **capas** igual que Photoshop, pero con estructura de árbol:

```
📄 Página: "Diseño móvil"
  └── 📱 Frame: "iPhone 15 Pro" (390x844)
      ├── 🔲 Frame: "Navigation Bar"
      │   ├── ← Text: "Atrás"
      │   ├── 💬 Text: "Mensajes"
      │   └── ➕ Icon: "Nuevo mensaje"
      ├── 📋 Frame: "Lista de chats" (Auto Layout vertical)
      │   ├── 💬 Component: "Chat Item" (instancia)
      │   ├── 💬 Component: "Chat Item" (instancia)
      │   └── 💬 Component: "Chat Item" (instancia)
      └── ⌨️ Frame: "Tab Bar"
          ├── 🏠 Icon: "home"
          └── 👤 Icon: "perfil"
```


### 3.2. Componentes: diseño modular

Los **componentes** son el concepto más importante de Figma. Funcionan exactamente como las clases en programación.

#### Componentes principales (Main Component)

**Definición:** El "maestro" original. Cualquier cambio aquí se propaga a todas las instancias.

**Creación:**

1. Diseñar el elemento (ej: un botón)
2. Seleccionarlo
3. `Cmd/Ctrl + Alt + K` o menú "Create Component"
4. Se marca con el icono ⬥ morado

**Ejemplo conceptual:**

```
MAIN COMPONENT: "Botón Primario"
┏━━━━━━━━━━━━━━━━━┓
┃   ACEPTAR       ┃  ← Fondo azul #2196F3
┗━━━━━━━━━━━━━━━━━┛    Texto blanco, Roboto Medium 16sp
                       Padding 12dp vertical, 24dp horizontal
                       Border radius 8dp
```

Si cambias el color azul a verde en el Main Component, **todas las instancias** del botón en tu diseño se actualizan automáticamente.

#### Instancias (Instances)

**Definición:** "Copias enlazadas" del componente principal.

**Propiedades:**

- Heredan automáticamente los cambios del Main Component
- Pueden tener **overrides** (cambios específicos):
    - Texto diferente (útil para botones: "Guardar", "Cancelar", "Enviar")
    - Visibilidad de capas (mostrar/ocultar iconos)
    - Cambios de color si el Main Component tiene variantes

**Uso en código análogo:**

```kotlin
// Kotlin - Clase y objetos
class Boton(val texto: String, val color: Color) {
    // Propiedades del Main Component
}

val botonGuardar = Boton("Guardar", Color.Blue)  // Instancia 1
val botonCancelar = Boton("Cancelar", Color.Blue) // Instancia 2
```


### 3.3. Auto Layout: el layout engine

**Auto Layout** es el equivalente directo a `ConstraintLayout` (Android), `Flexbox` (CSS) o `VStack/HStack` (SwiftUI).

#### Propiedades de Auto Layout

**1. Dirección (Direction):**

- **Horizontal:** Elementos en fila (como `Row` o `LinearLayout horizontal`)
- **Vertical:** Elementos en columna (como `Column` o `LinearLayout vertical`)

**2. Espaciado (Spacing):**

- Distancia entre elementos hijos
- Equivale a `android:layout_marginBottom` entre cada hijo

**3. Padding:**

- Margen interno del contenedor
- Equivale a `android:padding`

**4. Alineación:**

- **Horizontal:** Start, Center, End, Space Between
- **Vertical:** Top, Center, Bottom

**5. Comportamiento de tamaño:**

- **Hug Contents:** El frame se ajusta al tamaño de sus hijos (wrap_content)
- **Fill Container:** El hijo ocupa todo el espacio disponible (match_parent)
- **Fixed:** Tamaño fijo en píxeles


#### Ejemplo práctico: diseño de tarjeta de contacto

```
SIN AUTO LAYOUT (posiciones absolutas):
Frame "Tarjeta Contacto" (300x80)
├── Image "avatar" (X: 16, Y: 16, 48x48)  ← Posiciones manuales
├── Text "Nombre" (X: 80, Y: 20)
└── Text "Email" (X: 80, Y: 45)

Problema: Si el nombre es muy largo, se sale de la tarjeta.
         Si cambias el tamaño del avatar, debes reposicionar todo manualmente.

CON AUTO LAYOUT:
Frame "Tarjeta Contacto" (Auto Layout horizontal)
├── Padding: 16dp
├── Spacing: 12dp
├── Children:
│   ├── Image "avatar" (48x48, Fixed)
│   └── Frame (Auto Layout vertical, Fill container)
│       ├── Text "Nombre" (Hug contents)
│       └── Text "Email" (Hug contents)

Ventajas:
✅ El texto largo empuja el tamaño de la tarjeta automáticamente
✅ Cambiar el avatar no requiere reposicionar nada
✅ Añadir un tercer texto (teléfono) solo requiere arrastrarlo al frame
✅ Se comporta exactamente como un LinearLayout de Android
```

**Implementación paso a paso en Figma:**

1. Crear un Frame para la tarjeta
2. Añadir elementos (avatar, textos)
3. Seleccionar el Frame padre
4. Activar Auto Layout (Shift + A)
5. Configurar:
    - Direction: Horizontal
    - Spacing: 12
    - Padding: 16
    - Alignment: Center (vertical)
6. Seleccionar el Frame de textos
7. Activar Auto Layout en él también
8. Configurar:
    - Direction: Vertical
    - Spacing: 4

> [!IMPORTANT]
> Si diseñas sin Auto Layout, tu diseño se romperá cuando el contenido cambie (textos más largos, idiomas diferentes, diferentes tamaños de pantalla). Auto Layout garantiza que el diseño sea **responsive** igual que los layouts de Android/iOS.

### 3.4. Variantes de componentes

Las **variantes** permiten agrupar estados diferentes de un mismo componente (equivalente a StateListDrawable en Android).

#### Ejemplo: botón con estados

**Estados típicos de un botón:**

- **Default:** Estado normal
- **Hover:** Al pasar el cursor (web/tablet)
- **Pressed:** Al pulsar
- **Disabled:** Cuando no está disponible
- **Loading:** Mostrando spinner de carga

**Configuración en Figma:**

```
Component Set: "Botón Primario"
└── Properties:
    ├── State: Default | Hover | Pressed | Disabled | Loading
    └── Size: Small | Medium | Large

Esto genera automáticamente:
├── Botón / Default / Small
├── Botón / Default / Medium
├── Botón / Default / Large
├── Botón / Hover / Small
├── Botón / Hover / Medium
└── ... (25 variantes en total: 5 estados × 5 tamaños)
```

**Ventajas:**

- Cambiar entre estados es instantáneo en prototipos
- El desarrollador ve todos los estados posibles
- Puedes configurar transiciones automáticas entre variantes

**Uso en prototipo interactivo:**

```
Botón instancia en pantalla
├── On Tap → Change to: Pressed (80ms)
└── After delay (80ms) → Navigate to: Pantalla 2
```


### 3.5. Estilos y design tokens

Los **estilos** son valores reutilizables (como variables CSS o constantes en código).

#### Tipos de estilos en Figma

**1. Color Styles:**

```
Primary/500: #2196F3
Primary/700: #1976D2
Error/500: #F44336
Surface: #FFFFFF
On-Surface: #000000
```

Equivalente en Android:

```xml
<color name="primary_500">#2196F3</color>
```

**2. Text Styles:**

```
Heading/Large: Roboto Bold 28sp, line-height 36sp
Body/Medium: Roboto Regular 16sp, line-height 24sp
Caption: Roboto Regular 12sp, line-height 16sp
```

Equivalente en Android:

```xml
<style name="TextAppearance.Heading.Large">
    <item name="android:textSize">28sp</item>
    <item name="android:fontFamily">@font/roboto_bold</item>
</style>
```

**3. Effect Styles (sombras, blurs):**

```
Elevation/2dp:
  - Shadow: X:0 Y:1 Blur:3 Color:#00000033
  - Shadow: X:0 Y:1 Blur:1 Color:#00000024
```


#### Uso en equipos grandes

```
DISEÑADOR 1                DISEÑADOR 2
└── Usa "Primary/500"      └── Usa "Primary/500"
    en botones                 en iconos

Si el Product Manager decide cambiar el azul a verde:
└── Cambiar "Primary/500" de #2196F3 a #4CAF50
    └── Todos los botones E iconos se actualizan automáticamente
```

> [!TIP]
> Los estilos en Figma se exportan directamente como Design Tokens (JSON/XML) que los desarrolladores pueden importar en su código. Esto garantiza que el color `Primary/500` en Figma sea exactamente el mismo `primary_500` en la app compilada.

***

## 4. Prototipado interactivo

Una vez que tenemos el diseño visual (mockups), el siguiente paso es hacerlo interactivo.

### 4.1. Conexiones y flujos

**Concepto básico:** Conectar hotspots (áreas clicables) con destinos (otras pantallas).

#### Tipos de triggers (disparadores)

| Trigger | Descripción | Uso típico |
| :-- | :-- | :-- |
| **On Tap** | Al hacer clic/tocar | Botones, tarjetas clicables |
| **On Drag** | Al arrastrar | Swipe entre pantallas, dismiss de modales |
| **While Hovering** | Al pasar el cursor encima | Tooltips, estados hover (web) |
| **While Pressing** | Mientras se mantiene pulsado | Botones con feedback visual |
| **After Delay** | Tras X milisegundos | Splash screens, animaciones automáticas |
| **Key/Gamepad** | Al pulsar tecla específica | Testing con teclado |

#### Tipos de acciones

| Acción | Comportamiento | Ejemplo |
| :-- | :-- | :-- |
| **Navigate to** | Va a otra pantalla (destructivo) | Login → Home |
| **Open Overlay** | Abre modal encima (no destructivo) | Botón → Diálogo de confirmación |
| **Scroll to** | Desplaza hasta un elemento | Ancla de navegación |
| **Back** | Vuelve a la pantalla anterior | Botón "X" o "Cancelar" |
| **Close Overlay** | Cierra el modal actual | Botón "Cerrar" del diálogo |
| **Change to** | Cambia variante del componente | Botón Default → Pressed |

#### Ejemplo: flujo de login

```
PANTALLA 1: "Bienvenida"
└── Botón "Iniciar sesión"
    └── Trigger: On Tap
        └── Action: Navigate to "Login" (Instant)

PANTALLA 2: "Login"
├── Botón "< Atrás"
│   └── Trigger: On Tap → Action: Back
├── Input email (no interactivo en prototipo)
├── Input contraseña (no interactivo en prototipo)
└── Botón "Entrar"
    └── Trigger: On Tap
        └── Action 1: Change to "Botón/Loading" (0ms)
        └── After Delay (1500ms): Navigate to "Home"

PANTALLA 3: "Home"
└── [Fin del flujo]
```


### 4.2. Smart Animate

**Smart Animate** es la función más poderosa de Figma. Crea animaciones automáticas interpolando cambios entre dos frames.

#### Cómo funciona

Figma compara dos pantallas y busca elementos con **el mismo nombre de capa**:

- Si encuentra coincidencias, anima la transición (posición, tamaño, rotación, opacidad)
- Si un elemento existe en Frame A pero no en Frame B, hace fade out
- Si un elemento existe en Frame B pero no en Frame A, hace fade in


#### Ejemplo: expansión de tarjeta

```
FRAME A: "Lista"
└── Card "Producto 1" (ancho 300, alto 100)
    ├── Image "foto" (80x80)
    ├── Text "Nombre producto"
    └── Text "19.99€"

FRAME B: "Detalle Producto"
└── Card "Producto 1" (ancho 390, alto 600)  ← Mismo nombre!
    ├── Image "foto" (390x300)  ← Mismo nombre!
    ├── Text "Nombre producto"  ← Mismo nombre!
    ├── Text "19.99€"
    └── Text "Descripción larga..." ← Nuevo elemento (fade in)
    └── Button "Comprar" ← Nuevo elemento (fade in)

Conexión:
Card en Frame A
└── On Tap → Navigate to Frame B (Smart Animate, 300ms, Ease Out)

Resultado:
✨ La tarjeta crece suavemente de 300x100 a 390x600
✨ La imagen se expande de 80x80 a 390x300
✨ La descripción y botón aparecen con fade in
✨ Todo en una animación fluida de 300ms
```

> [!TIP]
> Para que Smart Animate funcione correctamente, los nombres de capa deben ser **idénticos** en ambos frames. Un error común es tener "Boton" en Frame A y "Botón" (con tilde) en Frame B, lo que impide la interpolación.

#### Ejemplo: toggle de switch

```
Component "Switch"
└── Variants:
    ├── State: Off
    │   ├── Track (gris, ancho 50)
    │   └── Thumb (círculo blanco, X:2)  ← Posición izquierda
    └── State: On
        ├── Track (azul, ancho 50)
        └── Thumb (círculo blanco, X:28)  ← Posición derecha

Prototipo:
Switch/Off
└── On Tap → Change to: Switch/On (Smart Animate, 200ms)

Switch/On
└── On Tap → Change to: Switch/Off (Smart Animate, 200ms)

Resultado:
✨ El círculo se desliza suavemente de izquierda a derecha
✨ El color del track cambia de gris a azul con transición
```


### 4.3. Overlays y modales

Los **overlays** son elementos que aparecen encima del contenido existente sin destruir la pantalla subyacente.

#### Configuración de overlay

**Propiedades principales:**

1. **Position:**
    - Top Left, Top Center, Top Right
    - Center (más común para diálogos)
    - Bottom Left, Bottom Center (Action Sheets iOS), Bottom Right
2. **Close when:**
    - Click outside: El usuario toca fuera del modal
    - Press Escape: Presiona tecla Esc (testing desktop)
    - Manual: Solo con botón "Cerrar" explícito
3. **Background:**
    - Transparent
    - Solid (color sólido)
    - Dim (oscurecer fondo típico: \#000000 con 40% opacidad)

#### Ejemplo: modal de confirmación

```
PANTALLA BASE: "Configuración"
└── Botón "Eliminar cuenta"
    └── On Tap → Open Overlay "Modal Confirmar"
        ├── Position: Center
        ├── Close when: Click outside
        └── Background: Dim (#000000, 50%)

OVERLAY: "Modal Confirmar" (frame 300x200)
├── Background: White, Border radius 16
├── Text "¿Eliminar cuenta?"
├── Text "Esta acción no se puede deshacer"
├── Botón "Cancelar"
│   └── On Tap → Close Overlay
└── Botón "Eliminar" (rojo)
    └── On Tap → Navigate to "Cuenta eliminada"
```


### 4.4. Estados interactivos

Los estados permiten dar feedback visual inmediato al usuario.

#### Patrón típico: botón con loading

```
Component "Botón Submit"
└── Variants:
    ├── State: Default
    │   └── Text "Enviar"
    ├── State: Pressed
    │   └── Text "Enviar" (opacidad 80%, scale 0.98)
    └── State: Loading
        ├── Spinner (animated)
        └── Text "Enviando..." (opacidad 60%)

Prototipo en Frame "Formulario":
Botón instancia
├── On Tap → Change to: Pressed (0ms)
├── After Delay 80ms → Change to: Loading
└── After Delay 2000ms → Navigate to "Éxito"
```


***

## 5. Developer handoff

El **developer handoff** es el proceso de entregar el diseño a los programadores con toda la información necesaria para implementarlo.

### Dev Mode en Figma

Figma tiene un **modo de inspección** específico para desarrolladores.

**Información que proporciona automáticamente:**

1. **Código CSS/iOS/Android:**
```
Seleccionas un botón en Figma

→ Dev Mode muestra:

CSS:
background: #2196F3;
border-radius: 8px;
padding: 12px 24px;
font-family: 'Roboto';
font-weight: 500;
font-size: 16px;

Android XML:
<Button
    android:background="@color/primary_500"
    android:backgroundTint="#2196F3"
    android:paddingVertical="12dp"
    android:paddingHorizontal="24dp"
    android:textSize="16sp"
    android:textStyle="bold" />

SwiftUI:
Button("Aceptar") {
    // Acción
}
.padding(.vertical, 12)
.padding(.horizontal, 24)
.background(Color(hex: "#2196F3"))
.cornerRadius(8)
.font(.system(size: 16, weight: .medium))
```

2. **Medidas exactas:**
    - Distancias entre elementos (constraints)
    - Tamaños absolutos y relativos
    - Márgenes y paddings
3. **Assets listos para exportar:**
    - Iconos en PNG, SVG, PDF
    - Imágenes en @1x, @2x, @3x (Android)
    - Imágenes en @1x, @2x, @3x (iOS)

### Plugins para exportar código

**Anima:** Convierte diseños Figma a código React, Vue, HTML/CSS
**Figma to Code:** Genera código Android XML o SwiftUI
**DhiWise:** Genera proyectos completos Android/iOS desde Figma

> [!WARNING]
> El código generado automáticamente nunca es perfecto. Úsalo como punto de partida, pero siempre requiere revisión y refactorización por un desarrollador experimentado.

***

## 6. Accesibilidad universal (A11y)

La accesibilidad (abreviada como **A11y** porque hay 11 letras entre la 'A' y la 'y') no es diseñar "versiones especiales para discapacitados". Es diseñar productos robustos que funcionen para todos.

### El efecto "curb cut"

> **Curb Cut Effect (Efecto rampa de acera):** Las rampas en las aceras se inventaron para sillas de ruedas, pero benefician a:
> - Padres con carritos de bebé
> - Personas con maletas con ruedas
> - Repartidores con carretillas
> - Ciclistas
> - Ancianos con andadores
>
> **La accesibilidad beneficia al 100% de los usuarios**.

### Datos relevantes

- **15% de la población mundial** tiene algún tipo de discapacidad (1.000 millones de personas)
- **8% de hombres** y **0.5% de mujeres** tienen daltonismo
- **100% de usuarios** experimenta discapacidades **temporales** (brazo roto, infección ocular)
- **100% de usuarios** experimenta discapacidades **situacionales** (usar el móvil bajo el sol brillante, en un autobús ruidoso)


### 6.1. Principios WCAG 2.1

Las **Web Content Accessibility Guidelines** (WCAG) son el estándar internacional de accesibilidad.

#### Los 4 principios POUR

**1. Perceptible (Perceivable)**
El usuario debe poder percibir la información presentada.

- Texto alternativo en imágenes
- Contraste de color adecuado
- Contenido adaptable a diferentes tamaños de texto

**2. Operable**
El usuario debe poder navegar y usar la interfaz.

- Navegación con teclado
- Tiempo suficiente para leer
- Evitar contenido que cause convulsiones (flashes rápidos)

**3. Comprensible (Understandable)**
La información y la operación de la UI deben ser comprensibles.

- Lenguaje claro
- Comportamiento predecible
- Ayuda en formularios con mensajes de error claros

**4. Robusto (Robust)**
El contenido debe ser compatible con tecnologías asistivas.

- HTML semántico correcto
- Compatible con lectores de pantalla
- Funciona en diferentes navegadores y dispositivos


#### Niveles de conformidad

| Nivel | Requisitos | Descripción |
| :-- | :-- | :-- |
| **A** | Mínimo | Accesibilidad básica (obligatoria por ley en UE) |
| **AA** | Recomendado | Estándar de la industria (Google, Apple lo exigen) |
| **AAA** | Máximo | Excelencia (difícil de alcanzar en todos los casos) |

> [!IMPORTANT]
> En la Unión Europea, la **Directiva de Accesibilidad Web** obliga a que todos los sitios web y apps de entidades públicas cumplan **WCAG 2.1 nivel AA** desde 2020. Muchos países tienen leyes similares (ADA en USA).

### 6.2. Accesibilidad visual

Afecta a personas con ceguera total, visión reducida o daltonismo.

#### Contraste de color

**Requisitos WCAG 2.1 nivel AA:**


| Tipo de contenido | Ratio mínimo |
| :-- | :-- |
| **Texto normal** (< 18pt o < 14pt bold) | **4.5:1** |
| **Texto grande** (≥ 18pt o ≥ 14pt bold) | **3:1** |
| **Componentes UI** (iconos, bordes de inputs) | **3:1** |

**Cálculo del ratio de contraste:**

```
Ratio = (L1 + 0.05) / (L2 + 0.05)

Donde:
L1 = Luminosidad relativa del color más claro
L2 = Luminosidad relativa del color más oscuro

Luminosidad relativa se calcula según fórmula sRGB
(disponible en herramientas automáticas)
```

**Ejemplos:**

```
✅ PASA (4.5:1)
Texto negro #000000 sobre fondo blanco #FFFFFF
→ Ratio: 21:1 (excelente)

✅ PASA (4.5:1)
Texto azul oscuro #1565C0 sobre fondo blanco #FFFFFF
→ Ratio: 8.59:1

❌ FALLA (< 4.5:1)
Texto gris claro #999999 sobre fondo blanco #FFFFFF
→ Ratio: 2.85:1 (ilegible bajo el sol)

❌ FALLA (< 4.5:1)
Texto amarillo #FFEB3B sobre fondo blanco #FFFFFF
→ Ratio: 1.19:1 (prácticamente invisible)
```

**Herramientas de comprobación:**

- **WebAIM Contrast Checker:** webaim.org/resources/contrastchecker
- **Contrast Ratio:** contrast-ratio.com
- **Plugin Figma "Stark":** Comprueba contraste automáticamente
- **Plugin Figma "Contrast":** Verifica toda la página de una vez


#### No depender solo del color

**Regla:** Nunca uses solo el color para transmitir información.

```
❌ MAL:
Formulario con campos normales y "campos en rojo están mal"
→ Un daltónico no ve la diferencia de color

✅ BIEN:
Formulario con:
- Campo con borde rojo
- Icono ⚠️ al lado
- Texto "Error: El email no es válido"
```

**Ejemplo en diseño de gráficos:**

```
❌ MAL:
Gráfico de barras con:
- Barra verde = Aprobados
- Barra roja = Suspensos
(Los daltónicos rojo-verde no lo distinguen)

✅ BIEN:
- Barra verde con ✓ = Aprobados
- Barra roja con ✗ = Suspensos
- Leyenda con texto explícito
```


#### Simuladores de daltonismo

**Tipos principales:**

- **Protanopia:** Dificultad con el rojo
- **Deuteranopia:** Dificultad con el verde (8% de hombres)
- **Tritanopia:** Dificultad con el azul (rara)

**Herramientas:**

- **Figma plugin "Color Blind":** Simula cómo se ve tu diseño
- **App "Sim Daltonism" (macOS):** Overlay en tiempo real
- **Chromatic Vision Simulator (Android/iOS):** Usa la cámara


### 6.3. Accesibilidad motora

Afecta a personas con temblores (Parkinson), artritis, uso de una sola mano, o control por switch.

#### Tamaños mínimos de touch targets

**Especificaciones oficiales:**


| Plataforma | Mínimo absoluto | Recomendado | Crítico |
| :-- | :-- | :-- | :-- |
| **Android (Material)** | 48x48 dp | 48x48 dp | - |
| **iOS (HIG)** | 44x44 pt | 44x44 pt | 60x60 pt* |
| **WCAG 2.1 (AA)** | 24x24 px | 44x44 px | - |
| **WCAG 2.5.5 (AAA)** | - | **44x44 px** | - |

*iOS recomienda 60x60pt para acciones destructivas o muy frecuentes

**Visualización:**

```
48dp = Aproximadamente 9mm en pantalla real
(tamaño de la yema del dedo índice)

❌ Icono visual 16x16 con área táctil 16x16
   → Requiere precisión imposible

✅ Icono visual 16x16 con área táctil 48x48
   → Padding de 16dp alrededor del icono
```

**Implementación:**

```xml
<!-- Android: área táctil > área visual -->
<ImageButton
    android:layout_width="48dp"
    android:layout_height="48dp"
    android:padding="16dp"
    android:src="@drawable/ic_delete_16dp"
    android:background="?attr/selectableItemBackgroundBorderless" />
```

```swift
// SwiftUI: frame mínimo de 44pt
Button {
    deleteItem()
} label: {
    Image(systemName: "trash")
        .font(.system(size: 16))
}
.frame(minWidth: 44, minHeight: 44)
```


#### Espaciado entre elementos interactivos

**WCAG 2.1 Success Criterion 2.5.5:** Los touch targets deben tener mínimo **8px de espacio** entre ellos (o estar claramente separados visualmente).

```
❌ MAL:
[Botón 1][Botón 2][Botón 3]  ← 0dp de espacio
(El usuario pulsa botones adyacentes por error)

✅ BIEN:
[Botón 1]  8dp  [Botón 2]  8dp  [Botón 3]
```


#### Thumb zones (zonas del pulgar)

Como vimos en el Tema 13, la parte inferior de la pantalla es la más accesible.

**Implicaciones para usuarios con movilidad reducida:**

- **Personas con artritis:** Prefieren toques en la zona inferior (no requiere doblar el pulgar hacia arriba)
- **Usuarios con temblores:** Necesitan targets más grandes (considera 60x60pt en lugar de 44x44pt)
- **Uso con una mano:** Botones críticos siempre en la mitad inferior


### 6.4. Accesibilidad cognitiva

Afecta a personas con dislexia, TDAH, autismo, ansiedad, o simplemente usuarios bajo estrés o fatiga.

#### Principios de diseño cognitivo

**1. Evitar muros de texto**

```
❌ MAL:
Párrafo largo sin saltos de línea ni puntos suspensivos de más de
150 palabras que satura al usuario y hace imposible encontrar la
información relevante especialmente en pantallas pequeñas donde...

✅ BIEN:
Párrafo dividido en secciones:

**Introducción** (2 líneas)
Texto corto explicativo.

**Características principales** (lista con bullet points)
- Punto 1
- Punto 2

**Conclusión** (1 línea)
```

**2. Feedback visual constante**

```
❌ MAL:
[Botón "Enviar"]
(Al pulsar, nada cambia visualmente durante 2 segundos)
→ Usuario piensa que no funcionó y pulsa 5 veces más

✅ BIEN:
[Botón "Enviar"]
→ Al pulsar: cambia a [Botón "Enviando..." con spinner]
→ Usuario sabe que la acción está en proceso
```

**3. Navegación consistente**

```
❌ MAL:
Pantalla A: Menú en la parte superior
Pantalla B: Menú en la parte inferior
Pantalla C: Menú lateral
→ El usuario se desorienta

✅ BIEN:
Todas las pantallas: Tab Bar inferior (iOS) o Bottom Nav (Android)
→ El usuario sabe siempre dónde buscar
```

**4. Lenguaje claro y directo**

```
❌ MAL:
"Se ha producido una excepción no controlada en el módulo de
autenticación debido a credenciales inválidas"

✅ BIEN:
"Usuario o contraseña incorrectos"
```


#### Animaciones y movimiento

**WCAG 2.3.1:** El contenido no debe parpadear más de 3 veces por segundo (riesgo de convulsiones fotosensibles).

**Consideraciones:**

- Ofrecer opción de **"reducir movimiento"** (iOS/Android lo detectan automáticamente)
- Evitar animaciones largas (>500ms) que bloqueen la interacción
- No usar parallax agresivo (personas con mareos)

```swift
// SwiftUI: respetar preferencias del sistema
@Environment(\.accessibilityReduceMotion) var reduceMotion

if reduceMotion {
    // Transición instantánea
    view.transition(.opacity)
} else {
    // Transición animada
    view.transition(.scale)
}
```


### 6.5. Lectores de pantalla

Los **lectores de pantalla** leen en voz alta el contenido de la pantalla para personas ciegas o con baja visión.

#### Lectores principales

| Plataforma | Lector de pantalla | Activación |
| :-- | :-- | :-- |
| **Android** | TalkBack | Ajustes → Accesibilidad → TalkBack |
| **iOS** | VoiceOver | Ajustes → Accesibilidad → VoiceOver |
| **Web** | JAWS, NVDA, VoiceOver | Depende del SO |

#### Requisitos para compatibilidad

**1. Etiquetas descriptivas (contentDescription / accessibility label)**

```xml
<!-- Android -->
<ImageButton
    android:src="@drawable/ic_search"
    android:contentDescription="Buscar productos" />
<!-- Sin contentDescription, TalkBack dice: "Botón sin etiqueta" -->

<ImageView
    android:src="@drawable/decorative_pattern"
    android:contentDescription="@null" />
<!-- Decorativo, se debe marcar explícitamente como null -->
```

```swift
// SwiftUI
Image(systemName: "magnifyingglass")
    .accessibilityLabel("Buscar productos")

Image("decorative-pattern")
    .accessibilityHidden(true) // Decorativo, ocultar de VoiceOver
```

**2. Orden de lectura lógico**

El lector de pantalla sigue el orden del árbol de vistas (top to bottom, left to right).

```
❌ MAL (orden visual vs orden del DOM):
Visualmente:
[Logo arriba izquierda] [Menú arriba derecha]
[Título centrado]

Pero en el código:
<View>
  <Menu />       ← Lee primero
  <Logo />       ← Lee segundo
  <Title />      ← Lee tercero
</View>

→ El usuario escucha: "Menú, Logo, Título" (confuso)

✅ BIEN:
<View>
  <Logo />
  <Title />
  <Menu />
</View>
→ O usar accessibilityOrder en iOS / android:accessibilityTraversalAfter
```

**3. Estados y cambios dinámicos**

```xml
<!-- Android: anunciar cambios -->
<TextView
    android:text="Cargando..."
    android:accessibilityLiveRegion="polite" />
<!-- TalkBack anuncia cuando el texto cambia sin necesidad de foco -->
```

```swift
// SwiftUI
Text("Cargando...")
    .accessibilityElement()
    .accessibilityLiveRegion(.polite)
```


#### Diseño en Figma para lectores de pantalla

**Anotaciones necesarias:**

1. **Etiquetar elementos no textuales:**
```
Icono de corazón → Anotar: "Añadir a favoritos"
Avatar del usuario → Anotar: "Foto de perfil de [nombre]"
```

2. **Marcar elementos decorativos:**
```
Imagen de fondo abstracta → Anotar: "Decorativo, ignorar"
Línea divisoria → Anotar: "Decorativo"
```

3. **Describir el orden de lectura:**
```
Usar números o flechas en el diseño para indicar:
1 → Logo
2 → Título
3 → Descripción
4 → Botón de acción
```


***

## 7. Herramientas de auditoría

No hace falta adivinar si tu diseño es accesible. Existen herramientas automáticas.

### Plugins de Figma

**1. Stark (https://www.getstark.co/)**

- ✅ **Comprobación de contraste:** Marca automáticamente texto con ratio < 4.5:1
- ✅ **Simulación de daltonismo:** 8 tipos diferentes
- ✅ **Sugerencias de corrección:** Propone colores alternativos
- ✅ **Generación de reportes:** PDF con todos los problemas encontrados

**Uso:**

1. Instalar plugin Stark desde Figma Community
2. Seleccionar frame o página completa
3. Ejecutar "Check Contrast"
4. Corregir elementos marcados en rojo

**2. Contrast (https://contrast.app/)**

- Comprobador de contraste minimalista
- Muestra ratio en tiempo real al seleccionar elementos
- Gratuito

**3. A11y - Color Contrast Checker**

- Específico para WCAG 2.1
- Indica nivel (A, AA, AAA)


### Herramientas móviles

**Android: Accessibility Scanner**

Aplicación oficial de Google que analiza apps instaladas.

**Instalación:**

1. Descargar desde Google Play: "Accessibility Scanner"
2. Activar en Ajustes → Accesibilidad → Accessibility Scanner
3. Aparece un botón flotante azul

**Uso:**

1. Abrir tu app
2. Pulsar el botón flotante
3. Tomar captura con el botón de Accessibility Scanner
4. Ver sugerencias:
    - ✅ "Touch target too small" → Indica elementos < 48dp
    - ✅ "Low contrast ratio" → Texto ilegible
    - ✅ "Missing contentDescription" → Iconos sin etiqueta

**iOS: Accessibility Inspector (Xcode)**

Herramienta integrada en Xcode para desarrolladores.

**Características:**

- Auditoría automática de la UI
- Simulación de VoiceOver sin activarlo globalmente
- Comprobación de dynamic type (tamaños de fuente ajustables)


### Herramientas web

**WebAIM Contrast Checker**

- URL: https://webaim.org/resources/contrastchecker/
- Input: Color de texto (hex) + Color de fondo (hex)
- Output: Ratio calculado + nivel WCAG

**Who Can Use**

- URL: https://whocanuse.com/
- Muestra cómo diferentes personas (con diferentes tipos de visión) ven tu combinación de colores

***

## 8. Casos prácticos integrados

### Práctica 1: diseño de formulario accesible

**Contexto:** Formulario de registro en app de delivery de comida.

**Campos requeridos:**

- Nombre completo
- Email
- Contraseña
- Confirmación de contraseña
- Dirección de entrega
- Teléfono

**Checklist de accesibilidad a cumplir:**

```
✅ Contraste texto/fondo ≥ 4.5:1
✅ Labels claros encima de cada input
✅ Touch targets de 48dp/44pt mínimo
✅ Mensajes de error descriptivos (no solo "Error")
✅ Indicador de campo obligatorio (* o "Obligatorio" en texto)
✅ No depender solo del color para mostrar errores
✅ Botón submit con tamaño mínimo 48x48
✅ Espaciado mínimo 8dp entre inputs
✅ Iconos con contentDescription/accessibilityLabel
✅ Orden de tabulación lógico
```

**Entrega:**

- Frame en Figma con el formulario diseñado
- Anotar en cada elemento su accessibilityLabel
- Captura del plugin Stark mostrando 0 errores de contraste


### Práctica 2: rediseño de app con problemas de accesibilidad

**Contexto:** Se proporciona captura de pantalla de app real con múltiples violaciones WCAG.

**Problemas típicos insertados:**

- Texto gris claro sobre fondo blanco (ratio 2.1:1)
- Iconos de 20x20dp sin padding
- Botones de solo 32dp de altura
- Error de formulario solo indicado con borde rojo
- 5 botones pequeños juntos sin separación

**Tarea:**

1. Identificar y documentar todos los problemas
2. Rediseñar en Figma corrigiendo cada uno
3. Justificar cada cambio con la regla WCAG correspondiente

***

## 9. Recursos y bibliografía

### Documentación oficial

**Figma:**

- Web oficial: https://www.figma.com
- Figma Learn: https://help.figma.com/hc/en-us
- Curso "Figma 101": https://www.youtube.com/figma
- Auto Layout playground: https://www.figma.com/community/file/784448220678228461

**WCAG:**

- Web oficial: https://www.w3.org/WAI/WCAG21/quickref/
- How to Meet WCAG (guía interactiva): https://www.w3.org/WAI/WCAG21/quickref/
- WCAG 2.1 en español: https://www.w3.org/WAI/WCAG21/Translations/

**Material Design Accessibility:**

- https://m3.material.io/foundations/accessible-design/overview

**Apple Accessibility:**

- https://developer.apple.com/accessibility/


### Herramientas mencionadas

**Comprobación de contraste:**

- WebAIM: https://webaim.org/resources/contrastchecker/
- Contrast Ratio: https://contrast-ratio.com/
- Who Can Use: https://whocanuse.com/

**Plugins de Figma:**

- Stark: https://www.getstark.co/
- A11y - Color Contrast Checker
- Able – Friction free accessibility
- Adee: Design for good (auditoría automática)

**Apps móviles:**

- Accessibility Scanner (Android - Google Play)
- Sim Daltonism (macOS App Store)
- Color Oracle (Windows/Mac/Linux)


### Libros recomendados

- **"Inclusive Design Patterns"** - Heydon Pickering (accesibilidad práctica)
- **"Mismatch: How Inclusion Shapes Design"** - Kat Holmes (filosofía de diseño inclusivo)
- **"Form Design Patterns"** - Adam Silver (formularios accesibles)
- **"Refactoring UI"** - Adam Wathan (principios visuales de UI)


### Canales de YouTube

- **Figma Official:** Tutoriales oficiales
- **Flux Academy:** Figma avanzado
- **DesignCourse:** UI/UX general
- **Google Chrome Developers:** Accesibilidad web (aplicable a móvil)

### Comunidades y recursos online

**UX/UI Design:**

- Dribbble (inspiración de diseño): https://dribbble.com/tags/mobile-ui
- Behance (portfolios profesionales): https://www.behance.net/
- Mobbin (biblioteca de patrones UI móvil): https://mobbin.com/

**Accesibilidad:**

- A11y Project: https://www.a11yproject.com/
- WebAIM: https://webaim.org/
- Deque University (cursos gratuitos): https://dequeuniversity.com/


### Normativas legales

**España:**

- Real Decreto 1112/2018 sobre accesibilidad de sitios web y apps móviles del sector público
- Exigencia: WCAG 2.1 nivel AA desde septiembre 2020

**Unión Europea:**

- European Accessibility Act (EAA) - obligatorio desde junio 2025
- Aplica a comercio electrónico, banca, transporte

**Estados Unidos:**

- Americans with Disabilities Act (ADA) - sección 508
- Demandas legales millonarias por apps inaccesibles (Domino's Pizza vs. Robles, 2019)

***

## Conclusión

Este tema os ha preparado para dos habilidades críticas en el desarrollo moderno de interfaces:

### 1. Prototipado profesional

Ahora sabéis:

- ✅ Crear wireframes para validar ideas rápidamente
- ✅ Diseñar mockups de alta fidelidad con sistemas de diseño coherentes
- ✅ Construir prototipos interactivos que simulan apps reales sin código
- ✅ Usar componentes, Auto Layout y variantes como desarrolladores usan clases
- ✅ Colaborar con desarrolladores mediante Dev Mode y especificaciones precisas

**Figma no es solo una herramienta de diseño, es una herramienta de comunicación** entre diseñadores, desarrolladores, product managers y clientes.

### 2. Accesibilidad universal

La accesibilidad no es:

- ❌ Un checkbox legal que cumplir al final del proyecto
- ❌ Diseñar "versiones especiales" para personas con discapacidad
- ❌ Algo que solo beneficia al 15% de usuarios

**La accesibilidad es:**

- ✅ Diseño robusto que funciona en cualquier contexto (sol brillante, autobús ruidoso, manos ocupadas)
- ✅ Requisito técnico que mejora la UX para el 100% de usuarios
- ✅ Obligación legal en la mayoría de países desarrollados
- ✅ Ventaja competitiva (apps accesibles tienen mejor valoración en tiendas)

> [!IMPORTANT]
> El coste de implementar accesibilidad desde el diseño inicial es prácticamente cero. El coste de arreglarla después del lanzamiento puede ser de miles de horas de refactorización. **Diseñad accesible desde el día 1**.

### Puntos clave para recordar

1. **El prototipo es el MVP del diseño:** Valida con usuarios reales antes de escribir código
2. **Auto Layout no es opcional:** Si tu diseño no usa Auto Layout, se romperá con contenido real
3. **Componentes = Clases:** Diseña modular igual que programas modular
4. **Contraste 4.5:1 es ley, no sugerencia:** Comprueba siempre con herramientas automáticas
5. **48dp/44pt mínimo:** Los touch targets pequeños excluyen a millones de usuarios
6. **Los lectores de pantalla existen:** Cada icono necesita contentDescription/accessibilityLabel
7. **El color nunca viaja solo:** Acompaña siempre con texto o iconos


### Reflexión final

Como futuros desarrolladores de aplicaciones multiplataforma, vuestro trabajo no termina al escribir código que compila. **Vuestro trabajo termina cuando cualquier persona, independientemente de sus capacidades, puede usar exitosamente la app que habéis creado**.

Las empresas tecnológicas líderes (Apple, Google, Microsoft, Airbnb) tienen equipos dedicados de accesibilidad. En entrevistas de trabajo os preguntarán sobre WCAG, contraste de color y lectores de pantalla. Este conocimiento no es "extra", es fundamental.

> **El mejor diseño es invisible para quien puede ver, pero audible para quien no puede. Es operable con un dedo, con diez dedos, o sin dedos. Es comprensible en cualquier idioma, cualquier contexto, cualquier momento del día.**