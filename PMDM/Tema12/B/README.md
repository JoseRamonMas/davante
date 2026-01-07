# Tema 12B. Desarrollo con Unity para Android

## Índice

1. [Introducción: Del desarrollo nativo a Unity](#1-introducción-del-desarrollo-nativo-a-unity)
2. [Instalación y configuración de Unity](#2-instalación-y-configuración-de-unity)
3. [Primeros pasos: La interfaz de Unity](#3-primeros-pasos-la-interfaz-de-unity)
4. [Conceptos fundamentales](#4-conceptos-fundamentales)
5. [Scripting en C# para Unity](#5-scripting-en-c-para-unity)
6. [Sistema de entrada (Input)](#6-sistema-de-entrada-input)
7. [Físicas y colisiones](#7-físicas-y-colisiones)
8. [Interfaz de usuario (UI)](#8-interfaz-de-usuario-ui)
9. [Exportación a Android](#9-exportación-a-android)
10. [Caso práctico completo: "Recolector 2D"](#10-caso-práctico-completo-recolector-2d)

---

## 1. Introducción: Del desarrollo nativo a Unity

En los temas anteriores, has aprendido a crear videojuegos en Android de forma nativa usando Java. Tuviste que gestionar el hilo de ejecución (`Thread`), el bucle de juego (`GameLoop`), el bloqueo del lienzo (`Canvas`) y pintar los píxeles manualmente. Esto te ha dado una comprensión sólida de cómo funciona un videojuego "por dentro": el bucle de juego, el renderizado frame a frame, la gestión de sprites y las colisiones.

Sin embargo, en la industria profesional, la mayoría de los juegos móviles se desarrollan con **motores de videojuegos** como Unity, Unreal Engine o Godot. ¿Por qué?

**Ventajas de usar Unity:**

- **Multiplataforma**: Escribe una vez, exporta a Android, iOS, PC, Web, consolas
- **Herramientas visuales**: Editor gráfico para posicionar objetos sin calcular coordenadas manualmente
- **Física integrada**: Motor Box2D incorporado para gravedad, colisiones realistas
- **Asset Store**: Miles de recursos gratuitos y de pago (sprites, música, efectos)
- **Comunidad masiva**: Millones de tutoriales, foros y soluciones

**Lo que NO cambia:**

- Sigues necesitando lógica de programación
- Los algoritmos son los mismos (detección de colisiones, movimiento, puntuaciones)
- El pensamiento computacional es fundamental

Unity **abstrae** el trabajo repetitivo (pintar píxeles, calcular intersecciones), pero **no** programa el juego por ti.

### Diferencias clave: Nativo vs Unity

| Característica | Android Nativo (Java) | Unity (C#) |
| --- | --- | --- |
| **Renderizado** | Tú dibujas cada frame (`canvas.drawBitmap`). | El motor dibuja por ti automáticamente. |
| **Físicas** | Tú programas la gravedad y colisiones (`Rect.intersect`). | Usas componentes (`Rigidbody`, `Collider`) que ya simulan física real. |
| **Entorno** | Android Studio (Todo código). | Editor Visual (Arrastrar y soltar) + Visual Studio (Código). |
| **Multiplataforma** | Solo funciona en Android. | Con un clic exportas a Android, iOS, PC, Consola... |

---

## 2. Instalación y configuración de Unity

### 2.1. Requisitos del sistema

**Mínimos:**

- Windows 10+ (64-bit) / macOS 10.15+ / Ubuntu 20.04+
- 8 GB RAM (16 GB recomendado)
- GPU con soporte DirectX 11 o Metal
- 10 GB de espacio en disco

### 2.2. Descarga de Unity Hub

Unity Hub es el gestor centralizado de versiones de Unity.

1. Ve a [https://unity.com/download](https://unity.com/download)
2. Descarga **Unity Hub** (no el editor directamente)
3. Instala Unity Hub y crea una cuenta gratuita (Unity Personal)

### 2.3. Instalación del Editor Unity

1. Abre Unity Hub
2. Ve a la pestaña **Installs**
3. Haz clic en **Install Editor**
4. Selecciona la versión LTS (Long Term Support) más reciente, por ejemplo **6.3 LTS**

    ⚠️ **CRÍTICO**: En la ventana de módulos, **debes marcar**:
    - ✅ **Android Build Support**
    - ✅ Android SDK & NDK Tools
    - ✅ OpenJDK

    Si no marcas esto, no podrás generar APKs. Puedes agregarlo después, pero es más lento.

5. Acepta los términos y espera (la instalación puede tardar 30-60 minutos)

### 2.4. Crear tu primer proyecto

1. En Unity Hub, pestaña **Projects**, clic en **New project**
2. Selecciona la plantilla **2D Core** (no 3D)
3. Nombre del proyecto: `MiPrimerJuegoUnity`
4. Ubicación: Elige una carpeta en tu PC
5. Clic en **Create project**

Unity abrirá el editor. La primera vez tarda unos minutos en compilar.

---

## 3. Primeros pasos: La interfaz de Unity

Al abrir Unity verás una interfaz dividida en paneles:

```plaintext
┌─────────────────────────────────────────────────────────┐
│  Menú: File  Edit  Assets  GameObject  Component  ...  │
├───────────┬─────────────────────────────┬───────────────┤
│           │                             │               │
│ Hierarchy │         Scene View          │   Inspector   │
│           │   (Vista de la escena)      │   (Detalles   │
│ (Objetos) │                             │   del objeto) │
│           │                             │               │
├───────────┴─────────────────────────────┤               │
│                                         │               │
│          Project (Assets)               │               │
│       (Tus archivos: sprites,           │               │
│         scripts, sonidos...)            │               │
└─────────────────────────────────────────┴───────────────┘
```

### Paneles principales

| Panel | Función |
|-------|---------|
| **Hierarchy** | Lista de GameObjects en la escena actual. Es como el "árbol" de tu nivel. |
| **Scene View** | Vista 2D/3D donde colocas visualmente los objetos (personaje, enemigos, plataformas). |
| **Game View** | Pestaña junto a Scene. Muestra cómo se ve el juego desde la cámara al presionar Play. |
| **Inspector** | Muestra y edita las propiedades del objeto seleccionado (posición, escala, componentes). |
| **Project** | Explorador de archivos del proyecto. Aquí están tus sprites (.png), scripts (.cs), audio (.mp3). |
| **Console** | Muestra mensajes de `Debug.Log()`, errores y advertencias. |

**Atajos útiles:**

- `F` en Scene View: Centra la cámara en el objeto seleccionado
- `Ctrl + D`: Duplicar objeto
- `Ctrl + S`: Guardar escena
- `Ctrl + Play`: Iniciar/pausar el juego

---

## 4. Conceptos fundamentales

### 4.1. GameObject: El contenedor universal

En Android nativo, creabas clases como `Jugador extends Sprite`. En Unity, todo es un **GameObject**.

Un GameObject es un contenedor vacío. Por sí solo no hace nada. Su poder viene de los **Componentes** que le añades.

**Analogía**: Un GameObject es un cuerpo humano sin órganos. Los componentes son los órganos que le das para que funcione.

### 4.2. Componentes: El sistema de composición

En lugar de herencia, Unity usa **composición**.

Ejemplo: Quieres crear una moneda que brille y suene al tocarla.

```plaintext
GameObject: "Moneda"
├─ Transform (posición, rotación, escala)
├─ Sprite Renderer (imagen de la moneda)
├─ Circle Collider 2D / Box Collider 2D (para detectar contacto)
├─ Audio Source (sonido "ding")
└─ Script: GiraMoneda.cs (rota la moneda)
```

Cada componente es independiente. Si quitas el `Audio Source`, la moneda funciona pero no suena.

### 4.3. Transform: El componente omnipresente

**Todo** GameObject tiene un componente `Transform` (en 2D se llama `RectTransform` en UI).

El Transform define:

- **Position**: Coordenadas (x, y, z) en el mundo
- **Rotation**: Ángulo de rotación (en grados)
- **Scale**: Tamaño (1 = tamaño original, 2 = doble, 0.5 = mitad)

```csharp
// Acceder al Transform desde un script
transform.position = new Vector3(0, 5, 0); // Mover a x=0, y=5
transform.Rotate(0, 0, 45); // Rotar 45° en eje Z
```

### 4.4. Prefabs: Plantillas reutilizables

Un **Prefab** es un GameObject guardado como plantilla. Si creas 10 enemigos y luego quieres cambiarles el color, sin Prefabs tendrías que cambiar los 10 manualmente. Con Prefabs, cambias el original y todos se actualizan.

**Crear un Prefab:**

1. Crea un GameObject en la escena (por ejemplo, un enemigo)
2. Arrástralo desde Hierarchy a la carpeta Project
3. Ahora es azul en Hierarchy (indica que es instancia de un Prefab)

---

## 5. Scripting en C# para Unity

Unity usa **C#** (pronunciado "C Sharp"). Si sabes Java, aprenderás C# en minutos.

### 5.1. Diferencias Java vs C#

| Concepto | Java | C# |
|----------|------|-----|
| **Clase base de scripts** | `extends Activity` | `: MonoBehaviour` |
| **Importar librerías** | `import java.util.*;` | `using System.Collections;` |
| **Imprimir en consola** | `System.out.println("Hola");` | `Debug.Log("Hola");` |
| **Tipo booleano** | `boolean` | `bool` |
| **Tipo cadena** | `String` | `string` (minúscula) |
| **Constantes** | `final` | `const` o `readonly` |
| **Convención de nombres** | `miMetodo()` (camelCase) | `MiMetodo()` (PascalCase) |
| **Propiedades** | Getters/Setters manuales | `{ get; set; }` automáticos |

### 5.2. Estructura de un script en Unity

Todos los scripts heredan de `MonoBehaviour`. Este es el equivalente al `Thread` de tu juego nativo.

```csharp
using UnityEngine; // Librería principal de Unity

public class MiPrimerScript : MonoBehaviour
{
    // Variables públicas (aparecen en el Inspector)
    public float velocidad = 5f;
    
    // Variables privadas (no se ven en el Inspector)
    private int puntuacion = 0;
    
    // Awake: Se ejecuta ANTES de Start (inicialización temprana)
    void Awake()
    {
        Debug.Log("Awake ejecutado");
    }
    
    // Start: Se ejecuta UNA VEZ cuando el objeto se activa
    // Equivale al constructor o onCreate()
    void Start()
    {
        Debug.Log("El juego ha comenzado");
        puntuacion = 0;
    }
    
    // Update: Se ejecuta EN CADA FRAME (30-120 veces/segundo)
    // Equivale al bucle while(true) de tu motor nativo
    void Update()
    {
        // Aquí va la lógica del juego (movimiento, input...)
        Debug.Log("Frame ejecutado");
    }
    
    // FixedUpdate: Se ejecuta a intervalos fijos (50 veces/segundo)
    // Se usa para físicas (Rigidbody)
    void FixedUpdate()
    {
        // Movimiento con físicas aquí
    }
}
```

### 5.3. Variables públicas vs privadas

```csharp
public class Jugador : MonoBehaviour
{
    [Header("Configuración de Vida")]
    public float vida = 100f;
    
    [Header("Configuración de Munición")]
    [SerializeField, Tooltip("Cantidad inicial de munición")]
    private int municion = 30;
    
    [Header("Configuración de Audio")]
    [Range(0, 10), Tooltip("Volumen del jugador")]
    public float volumen = 5f;
    
    // Privado y oculto en Inspector
    private bool estaMuerto = false;
    
    // Propiedad para acceder a munición de forma controlada
    public int MunicionActual => municion;
    
    void Start()
    {
        // Inicialización adicional si es necesaria
    }
}
```

### 5.4. El ciclo de vida (Lifecycle)

En Android nativo creabas un `while(true)` para el bucle de juego. En Unity, **no escribes el bucle**. Unity ya tiene el bucle interno y te llama en momentos específicos:

```plaintext
Juego inicia
    ↓
Awake()  ← Inicialización temprana
    ↓
OnEnable()  ← El objeto se activa
    ↓
Start()  ← Inicialización principal
    ↓
┌──────────────────────────┐
│   BUCLE DE JUEGO         │
│   ↓                      │
│ Update()  ← Cada frame   │
│   ↓                      │
│ FixedUpdate() ← Físicas  │
│   ↓                      │
│ LateUpdate() ← Después   │
│   ↓                      │
│ (Renderiza frame)        │
│   ↓                      │
│ (Vuelve a Update)        │
└──────────────────────────┘
    ↓
OnDisable()  ← Objeto desactivado
    ↓
OnDestroy()  ← Objeto destruido
```

**Cuándo usar cada uno:**

- `Start()`: Inicializar variables, buscar referencias a otros objetos
- `Update()`: Input del jugador, movimiento sin físicas, lógica del juego
- `FixedUpdate()`: Movimiento con `Rigidbody`, aplicar fuerzas
- `LateUpdate()`: Mover la cámara siguiendo al jugador (se ejecuta después de todos los Updates)

### 5.5. Ejemplo: Rotación continua

Crea este script y añádelo a cualquier objeto (por ejemplo, un cuadrado):

```csharp
using UnityEngine;

public class Girador : MonoBehaviour
{
    public float velocidadRotacion = 50f; // Grados por segundo
    
    void Update()
    {
        // Rotar en el eje Z (en 2D, Z es "hacia ti")
        // Time.deltaTime = tiempo transcurrido desde el último frame
        // Esto hace que rote igual en PC rápidos y lentos
        transform.Rotate(0, 0, velocidadRotacion * Time.deltaTime);
    }
}
```

**¿Por qué `Time.deltaTime`?**

- PC potente: 120 FPS → `Update()` se llama 120 veces/segundo → `deltaTime = 0.008s`
- Móvil lento: 30 FPS → `Update()` se llama 30 veces/segundo → `deltaTime = 0.033s`
- Sin `deltaTime`, el PC rotaría 4 veces más rápido. Con `deltaTime`, ambos rotan a la misma velocidad.

---

## 6. Sistema de entrada (Input)

### 6.1. Input táctil (Touch)

Para móviles Android, usamos `Input.touches`.

```csharp
using UnityEngine;

public class DetectorToques : MonoBehaviour
{
    void Update()
    {
        // ¿Hay al menos un dedo en la pantalla?
        if (Input.touchCount > 0)
        {
            // Obtener el primer toque (índice 0)
            Touch primerToque = Input.GetTouch(0);
            
            // Posición del toque en píxeles
            Vector2 posicionToque = primerToque.position;
            
            // Fase del toque
            switch (primerToque.phase)
            {
                case TouchPhase.Began:
                    // El dedo acaba de tocar
                    Debug.Log("Toque iniciado en: " + posicionToque);
                    break;
                    
                case TouchPhase.Moved:
                    // El dedo se está moviendo
                    Debug.Log("Dedo moviéndose: " + primerToque.deltaPosition);
                    break;
                    
                case TouchPhase.Ended:
                    // El dedo se levantó
                    Debug.Log("Toque finalizado");
                    break;
            }
        }
    }
}
```

### 6.2. Input de teclado (para pruebas en PC)

```csharp
void Update()
{
    // Detectar teclas específicas
    if (Input.GetKeyDown(KeyCode.Space))
    {
        Debug.Log("¡Saltaste!");
    }
    
    // Ejes configurados (flechas o WASD)
    float horizontal = Input.GetAxis("Horizontal"); // -1 (izquierda) a 1 (derecha)
    float vertical = Input.GetAxis("Vertical");     // -1 (abajo) a 1 (arriba)
}
```

### 6.3. Mover un objeto con input

```csharp
using UnityEngine;

public class MovimientoJugador : MonoBehaviour
{
    public float velocidad = 5f;
    
    void Update()
    {
        float horizontal = Input.GetAxis("Horizontal");
        float vertical = Input.GetAxis("Vertical");
        
        // Crear un vector de movimiento
        Vector3 movimiento = new Vector3(horizontal, vertical, 0);
        
        // Mover el objeto
        // Time.deltaTime asegura velocidad constante en todos los dispositivos
        transform.Translate(movimiento * velocidad * Time.deltaTime);
    }
}
```

### 6.4. Mover objeto hacia donde tocas (móvil)

```csharp
using UnityEngine;

public class MoverHaciaToque : MonoBehaviour
{
    public float velocidad = 3f;
    private Vector3 objetivo;
    private Camera camara;
    
    void Start()
    {
        camara = Camera.main;
        objetivo = transform.position;
    }
    
    void Update()
    {
        // Si tocas la pantalla
        if (Input.touchCount > 0 && Input.GetTouch(0).phase == TouchPhase.Began)
        {
            // Convertir posición del toque (píxeles) a posición del mundo
            Vector3 toquePixeles = Input.GetTouch(0).position;
            Vector3 toqueMundo = camara.ScreenToWorldPoint(toquePixeles);
            toqueMundo.z = 0; // En 2D, Z siempre es 0
            
            objetivo = toqueMundo;
        }
        
        // Mover hacia el objetivo
        transform.position = Vector3.MoveTowards(
            transform.position, 
            objetivo, 
            velocidad * Time.deltaTime
        );
    }
}
```

---

## 7. Físicas y colisiones

Unity tiene un motor de físicas 2D (Box2D) integrado. Olvídate de calcular `Rect.intersect()` manualmente.

### 7.1. Componentes de física

| Componente | Función |
|------------|---------|
| **Rigidbody 2D** | Da masa y gravedad al objeto. Se mueve con fuerzas. |
| **Collider 2D** | Define la "forma" de choque (caja, círculo, polígono). |
| **Physics Material 2D** | Define fricción y rebote (bounciness). |

### 7.2. Tipos de Colliders 2D

- **Box Collider 2D**: Rectángulo (para plataformas, cajas)
- **Circle Collider 2D**: Círculo (para monedas, pelotas)
- **Polygon Collider 2D**: Forma irregular (Unity lo genera automáticamente del sprite)
- **Edge Collider 2D**: Línea (para suelos inclinados)

### 7.3. Rigidbody 2D: Tipos de cuerpo

```csharp
Rigidbody2D.bodyType = RigidbodyType2D.Dynamic;    // Afectado por gravedad y fuerzas
Rigidbody2D.bodyType = RigidbodyType2D.Kinematic;  // No le afecta gravedad, se mueve por script
Rigidbody2D.bodyType = RigidbodyType2D.Static;     // Inmóvil (para plataformas fijas)
```

### 7.4. Crear un objeto con física

**Pasos:**

1. Crea un GameObject con sprite (por ejemplo, un círculo)
2. Añadir componente: `Add Component` → `Rigidbody 2D`
3. Añadir componente: `Add Component` → `Circle Collider 2D`
4. Presiona Play → ¡Cae por la gravedad!

### 7.5. Detectar colisiones: OnCollisionEnter2D

Este método se llama automáticamente cuando dos objetos con Colliders **sólidos** chocan.

```csharp
using UnityEngine;

public class Jugador : MonoBehaviour
{
    void OnCollisionEnter2D(Collision2D colision)
    {
        // "colision.gameObject" es el objeto con el que chocamos
        Debug.Log("Choqué con: " + colision.gameObject.name);
        
        // ¿Es el suelo?
        if (colision.gameObject.CompareTag("Suelo"))
        {
            Debug.Log("Estoy en el suelo, puedo saltar");
        }
        
        // ¿Es un enemigo?
        if (colision.gameObject.CompareTag("Enemigo"))
        {
            Debug.Log("¡Game Over!");
            Destroy(gameObject); // Destruirse a sí mismo
        }
    }
    
    // Mientras sigue chocando
    void OnCollisionStay2D(Collision2D colision)
    {
        Debug.Log("Sigo chocando con: " + colision.gameObject.name);
    }
    
    // Cuando deja de chocar
    void OnCollisionExit2D(Collision2D colision)
    {
        Debug.Log("Dejé de chocar con: " + colision.gameObject.name);
    }
}
```

### 7.6. Detectar triggers: OnTriggerEnter2D

Un **Trigger** es un Collider "fantasma" que no bloquea el movimiento, solo detecta que algo entró.

**Usos comunes:**

- Monedas recolectables
- Zonas de muerte
- Puntos de control

```csharp
using UnityEngine;

public class Moneda : MonoBehaviour
{
    // 1. Marcar el Collider2D como "Is Trigger" en el Inspector
    
    void OnTriggerEnter2D(Collider2D otro)
    {
        // "otro" es el Collider que entró en contacto
        if (otro.CompareTag("Player"))
        {
            Debug.Log("¡Moneda recogida!");
            
            // Sumar puntos (necesitarías un GameManager)
            // GameManager.instance.SumarPuntos(10);
            
            // Destruir la moneda
            Destroy(gameObject);
        }
    }
}
```

**Diferencia clave:**

| Método | Requiere | Bloquea movimiento | Uso |
|--------|----------|-------------------|-----|
| `OnCollisionEnter2D` | Dos Colliders normales | ✅ Sí | Choques físicos (jugador vs suelo) |
| `OnTriggerEnter2D` | Al menos un Collider marcado "Is Trigger" | ❌ No | Recolectables, zonas especiales |

### 7.7. Aplicar fuerzas con Rigidbody2D

```csharp
using UnityEngine;

public class Saltador : MonoBehaviour
{
    public float fuerzaSalto = 10f;
    private Rigidbody2D rb;
    
    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
    }
    
    void Update()
    {
        // Detectar toque o espacio
        if (Input.GetKeyDown(KeyCode.Space) || Input.touchCount > 0)
        {
            // Aplicar fuerza hacia arriba
            rb.AddForce(Vector2.up * fuerzaSalto, ForceMode2D.Impulse);
        }
    }
}
```

---

## 8. Interfaz de usuario (UI)

Unity tiene un sistema UI separado que se dibuja "encima" del juego.

### 8.1. Canvas: El contenedor de UI

Todo elemento UI debe estar dentro de un **Canvas**.

**Crear UI:**

1. Click derecho en Hierarchy → `UI` → `Text - TextMeshPro` (primera vez, instalará TMP)
2. Automáticamente se crea un Canvas y un EventSystem

El Canvas tiene modos de renderizado:

- **Screen Space - Overlay**: UI siempre encima (para HUD, botones)
- **Screen Space - Camera**: UI relativa a una cámara
- **World Space**: UI en el mundo 3D/2D (para letreros en el juego)

### 8.2. Anchors (Anclas): Clave para múltiples resoluciones

Los **Anchors** definen cómo se escala la UI en diferentes pantallas.

```plaintext
┌─────────────────────────────┐
│  📍                     📍  │  Anclas en las esquinas:
│                             │  El botón mantiene su distancia
│                             │  a la esquina superior derecha
│               [Botón] ◄─────┼─ Sin importar el tamaño de pantalla
│                             │
└─────────────────────────────┘
```

**Configurar anchors:**

1. Selecciona el elemento UI
2. En Inspector, haz clic en el cuadrado de Anchors
3. Mantén `Alt` y elige una posición

### 8.3. Ejemplo: Marcador de puntos

```csharp
using UnityEngine;
using TMPro; // Para TextMeshPro

public class GameManager : MonoBehaviour
{
    public TextMeshProUGUI textoPuntos;
    private int puntos = 0;
    
    void Start()
    {
        ActualizarUI();
    }
    
    public void SumarPuntos(int cantidad)
    {
        puntos += cantidad;
        ActualizarUI();
    }
    
    void ActualizarUI()
    {
        textoPuntos.text = "Puntos: " + puntos;
    }
}
```

### 8.4. Botones

```csharp
using UnityEngine;
using UnityEngine.UI; // Para Button

public class BotonPausa : MonoBehaviour
{
    public Button botonPausa;
    
    void Start()
    {
        // Agregar listener al botón
        botonPausa.onClick.AddListener(Pausar);
    }
    
    void Pausar()
    {
        // Pausar el juego
        Time.timeScale = 0f; // 0 = pausa, 1 = velocidad normal
        Debug.Log("Juego pausado");
    }
    
    public void Reanudar()
    {
        Time.timeScale = 1f;
    }
}
```

---

## 9. Exportación a Android

### 9.1. Configurar el proyecto para Android

1. **File** → **Build Settings**
2. Selecciona **Android** en la lista
3. Clic en **Switch Platform** (tarda 5-10 min la primera vez)

### 9.2. Player Settings (configuración crucial)

Clic en **Player Settings** (botón inferior izquierdo):

**Pestaña "Player":**

- **Company Name**: Tu nombre o empresa (ej: `MiEstudio`)
- **Product Name**: Nombre del juego (ej: `Recolector2D`)
- **Default Icon**: Logo de tu juego (arrastra una imagen .png)

**Pestaña "Other Settings":**

- **Package Name**: Debe ser único (ej: `com.miestudio.recolector2d`)
  - Formato: `com.empresa.juego` (todo en minúsculas, sin espacios)
- **Version**: `1.0`
- **Bundle Version Code**: `1` (increméntalo en cada actualización)
- **Minimum API Level**: `Android 7.0 (API 24)` o superior
- **Target API Level**: `Automatic (highest installed)`

**Pestaña "Publishing Settings":**

- **Create symbols.zip**: ✅ (para debugging)

### 9.3. Generar APK

**Opción A: Build (solo generar APK).**

1. **Build Settings** → **Build**
2. Elige carpeta de destino (ej: `Builds/`)
3. Espera 5-10 minutos
4. Copia el APK a tu móvil y lo instalas

**Opción B: Build And Run (compilar e instalar).**

1. Conecta tu móvil Android por USB
2. Activa **Depuración USB** en tu móvil:
   - Ajustes → Acerca del teléfono → Pulsa 7 veces en "Número de compilación"
   - Ajustes → Opciones de desarrollador → Depuración USB ✅
3. **Build Settings** → **Build And Run**
4. Unity compila, instala y abre el juego automáticamente

### 9.4. Optimización para móviles

**Configuración recomendada:**

- **Edit** → **Project Settings** → **Quality**
  - Nivel de calidad: `Medium` o `Low` para móviles antiguos
  - Anti Aliasing: `Disabled` (consume mucha GPU)
  - Shadows: `Disable Shadows` (en 2D no son necesarias)

- **Project Settings** → **Player** → **Other Settings**
  - **Graphics APIs**: Quitar Vulkan si da problemas, dejar solo OpenGLES3

---

## 10. Caso práctico completo: "Recolector 2D"

Vamos a crear un juego completo funcional paso a paso.

**Concepto del juego:**

- El jugador controla un cuadrado azul que se mueve con el dedo
- Caen monedas desde arriba
- El jugador debe recoger las monedas
- Cada moneda suma 10 puntos
- Si una moneda llega al suelo, pierdes 1 vida
- El juego termina al perder 3 vidas

### Paso 1: Crear el proyecto

1. Unity Hub → New Project → 2D Core
2. Nombre: `Recolector2D`

### Paso 2: Crear el jugador

1. Hierarchy → Click derecho → `2D Object` → `Sprites` → `Square`
2. Renombrar a `Jugador`
3. Inspector → Transform:
   - Position: `(0, -4, 0)`
   - Scale: `(1, 1, 1)`
4. Cambiar color:
   - Inspector → Sprite Renderer → Color: Azul

5. Añadir componentes:
   - `Add Component` → `Box Collider 2D`
   - `Add Component` → `Rigidbody 2D`
     - Body Type: `Dynamic`
     - Gravity Scale: 0

### Paso 3: Script de movimiento del jugador

> [!DANGER]
>
> Es importante hacer una configuración para simplificar el movimiento:
> Edit → Project Settings → Player
> Other Settings → **Active Input Handling**
> Selecciona: **Input Manager (Old)**
> Unity te pedirá reiniciar → Sí

1. Project → Click derecho → `Create` → `Folder` → Nombre: `Scripts`
2. Dentro de Scripts → Click derecho → `Create` → `MonoBehaviour Script` → Nombre: `MovimientoJugador`
3. Doble clic en el script (se abre Visual Studio)

    **MovimientoJugador.cs:**

    ```csharp
    using UnityEngine;

    public class MovimientoJugador : MonoBehaviour
    {
        public float velocidad = 5f;
        private Camera camara;
        private float limiteDerecha;
        private float limiteIzquierda;
        
        void Start()
        {
            camara = Camera.main;
            
            // Calcular límites de la pantalla
            float alturaCamara = camara.orthographicSize;
            float anchoCamara = alturaCamara * camara.aspect;
            
            limiteIzquierda = -anchoCamara + 0.5f; // +0.5 para dejar margen
            limiteDerecha = anchoCamara - 0.5f;
        }
        
        void Update()
        {
            // Movimiento con teclado (para pruebas en PC)
            float horizontal = Input.GetAxis("Horizontal");
            transform.Translate(Vector2.right * horizontal * velocidad * Time.deltaTime);
            
            // Movimiento táctil (para móvil)
            if (Input.touchCount > 0)
            {
                Touch toque = Input.GetTouch(0);
                Vector3 posicionMundo = camara.ScreenToWorldPoint(toque.position);
                
                // Mover solo en X, mantener Y fija
                Vector3 nuevaPos = transform.position;
                nuevaPos.x = posicionMundo.x;
                transform.position = nuevaPos;
            }
            
            // Limitar movimiento a los bordes de la pantalla
            float x = Mathf.Clamp(transform.position.x, limiteIzquierda, limiteDerecha);
            transform.position = new Vector3(x, transform.position.y, 0);
        }
    }
    ```

4. Arrastra el script `MovimientoJugador` al objeto `Jugador` en Hierarchy
5. Presiona Play → Mueve con flechas o arrastrando

### Paso 4: Crear la moneda (Prefab)

1. Hierarchy → `2D Object` → `Sprites` → `Circle`
2. Renombrar a `Moneda`
3. Transform:
   - Position: `(0, 5, 0)` (arriba de la pantalla)
   - Scale: `(0.5, 0.5, 1)` (más pequeña)
4. Sprite Renderer → Color: Amarillo
5. Añadir componentes:
   - `Add Component` → `Circle Collider 2D`
     - **Marcar la casilla "Is Trigger"** ✅
   - `Add Component` → `Rigidbody 2D`
     - Gravity Scale: `1` (para que caiga)

6. Crear script `Moneda.cs` en la carpeta Scripts:

    **Moneda.cs:**

    ```csharp
    using UnityEngine;

    public class Moneda : MonoBehaviour
    {
        void Update()
        {
            // Si la moneda cae fuera de la pantalla (abajo)
            if (transform.position.y < -6f)
            {
                // Notificar al GameManager que se perdió una vida
                GameManager.instance.PerderVida();
                
                // Destruir la moneda
                Destroy(gameObject);
            }
        }
        
        void OnTriggerEnter2D(Collider2D otro)
        {
            // Si la moneda toca al jugador
            if (otro.CompareTag("Player"))
            {
                // Sumar puntos
                GameManager.instance.SumarPuntos(10);
                
                // Reproducir sonido (opcional)
                // AudioSource audio = GetComponent<AudioSource>();
                // audio.Play();
                
                // Destruir la moneda
                Destroy(gameObject);
            }
        }
    }
    ```

7. Añadir script `Moneda` al objeto Moneda
8. Seleccionar el objeto `Jugador` → Inspector → Tag: `Player`
9. Crea una carpeta "Prefab" en Assets. Arrastra `Moneda` desde Hierarchy a la carpeta Prefab.
10. Elimina el objeto Moneda de Hierarchy (ya no lo necesitamos)

### Paso 5: Generador de monedas

1. Hierarchy → Click derecho → `Create Empty`
2. Renombrar a `GeneradorMonedas`
3. Transform → Position: `(0, 6, 0)`

4. Crear script `GeneradorMonedas.cs`:

    **GeneradorMonedas.cs:**

    ```csharp
    using UnityEngine;

    public class GeneradorMonedas : MonoBehaviour
    {
        public GameObject prefabMoneda; // Arrastra el Prefab aquí
        public float intervaloGeneracion = 2f; // Segundos entre monedas
        public float rangoX = 8f; // Cuánto se puede desviar en X
        
        private float tiempoSiguienteMoneda;
        
        void Start()
        {
            tiempoSiguienteMoneda = intervaloGeneracion;
        }
        
        void Update()
        {
            // Countdown
            tiempoSiguienteMoneda -= Time.deltaTime;
            
            // ¿Es hora de generar una moneda?
            if (tiempoSiguienteMoneda <= 0f)
            {
                GenerarMoneda();
                tiempoSiguienteMoneda = intervaloGeneracion;
            }
        }
        
        void GenerarMoneda()
        {
            // Posición aleatoria en X
            float posX = Random.Range(-rangoX, rangoX);
            Vector3 posicion = new Vector3(posX, transform.position.y, 0);
            
            // Instanciar (crear) la moneda
            Instantiate(prefabMoneda, posicion, Quaternion.identity);
        }
    }
    ```

5. Añadir script al objeto `GeneradorMonedas`
6. Seleccionar `GeneradorMonedas` → Inspector → Script:
   - Arrastra el **Prefab** `Moneda` desde Project al campo `Prefab Moneda`

### Paso 6: Game Manager (controlador del juego)

1. Hierarchy → Create Empty → Renombrar a `GameManager`
2. Crear script `GameManager.cs`:

    **GameManager.cs:**

    ```csharp
    using UnityEngine;
    using TMPro; // Para TextMeshPro
    using UnityEngine.SceneManagement; // Para reiniciar

    public class GameManager : MonoBehaviour
    {
        // Singleton (solo una instancia)
        public static GameManager instance;
        
        public TextMeshProUGUI textoPuntos;
        public TextMeshProUGUI textoVidas;
        public GameObject panelGameOver;
        
        private int puntos = 0;
        private int vidas = 3;
        
        void Awake()
        {
            // Patrón Singleton
            if (instance == null)
            {
                instance = this;
            }
            else
            {
                Destroy(gameObject);
            }
        }
        
        void Start()
        {
            ActualizarUI();
            panelGameOver.SetActive(false); // Ocultar panel al inicio
        }
        
        public void SumarPuntos(int cantidad)
        {
            puntos += cantidad;
            ActualizarUI();
        }
        
        public void PerderVida()
        {
            vidas--;
            ActualizarUI();
            
            if (vidas <= 0)
            {
                GameOver();
            }
        }
        
        void ActualizarUI()
        {
            textoPuntos.text = "Puntos: " + puntos;
            textoVidas.text = "Vidas: " + vidas;
        }
        
        void GameOver()
        {
            Debug.Log("¡Game Over!");
            panelGameOver.SetActive(true);
            Time.timeScale = 0f; // Pausar el juego
        }
        
        public void Reiniciar()
        {
            Time.timeScale = 1f;
            SceneManager.LoadScene(SceneManager.GetActiveScene().name);
        }
    }
    ```

3. Añadir script al objeto `GameManager`

### Paso 7: Crear la interfaz (UI)

1. **Texto de puntos:**
   - Hierarchy → UI → Text - TextMeshPro (instalar si pregunta)
   - Renombrar a `TextoPuntos`
   - Posicionar arriba a la izquierda:
     - RectTransform → Anchors: Esquina superior izquierda
     - Pos X: `100`, Pos Y: `-30`
   - TextMeshPro → Text: `Puntos: 0`
   - Font Size: `36`

2. **Texto de vidas:**
   - Duplicar `TextoPuntos` (Ctrl+D)
   - Renombrar a `TextoVidas`
   - Posicionar arriba a la derecha:
     - Anchors: Esquina superior derecha
     - Pos X: `-100`, Pos Y: `-30`
   - Text: `Vidas: 3`

3. **Panel Game Over:**
   - Hierarchy → UI → Panel
   - Renombrar a `PanelGameOver`
   - Cambiar color a semi-transparente (negro con Alpha 180)
   - Dentro del Panel:
     - UI → Text - TMP → Nombre: `TextoGameOver`
       - Text: `¡GAME OVER!`
       - Font Size: `72`, Color: Rojo
       - Centrado
     - UI → Button - TextMeshPro → Nombre: `BotonReiniciar`
       - Texto del botón: `Reiniciar`
       - Posicionarlo debajo del texto Game Over

4. **Conectar UI con GameManager:**
   - Seleccionar `GameManager` → Inspector → Script:
     - Texto Puntos: Arrastra `TextoPuntos` desde Hierarchy
     - Texto Vidas: Arrastra `TextoVidas`
     - Panel Game Over: Arrastra `PanelGameOver`

5. **Configurar botón:**
   - Seleccionar `BotonReiniciar`
   - Inspector → Button → On Click ()
     - Clic en `+`
     - Arrastra `GameManager` al campo de objeto
     - Function: `GameManager` → `Reiniciar()`

### Paso 8: Probar el juego

1. Presiona **Play**
2. Mueve el jugador con las flechas
3. Recoge monedas → Puntos aumentan
4. Deja caer 3 monedas → Game Over
5. Clic en Reiniciar → Juego vuelve a empezar

### Paso 9: Exportar a Android

1. File → Build Settings → Android → Switch Platform
2. Player Settings:
   - Company: `TuNombre`
   - Product Name: `Recolector2D`
3. Conecta tu móvil
4. Build

---

## Mejoras sugeridas (ejercicios)

1. **Diferentes tipos de monedas:**
   - Monedas plateadas (5 puntos)
   - Monedas doradas (10 puntos)
   - Monedas rojas (restan 10 puntos)

2. **Dificultad progresiva:**
   - Cada 10 monedas, reducir `intervaloGeneracion` en 0.1s

3. **Efectos de sonido:**
   - Sonido al recoger moneda
   - Música de fondo
   - Sonido de Game Over

4. **Efectos visuales:**
   - Partículas al recoger moneda
   - Rotación de las monedas mientras caen
   - Animación del jugador

5. **Menú principal:**
   - Crear una escena de menú con botón "Jugar"
   - Guardar puntuación máxima con `PlayerPrefs`

---

## Recursos adicionales

- **Unity Learn:** [https://learn.unity.com](https://learn.unity.com) - Tutoriales oficiales gratuitos
- **Brackeys (YouTube):** Tutoriales en inglés, muy claros
- **Asset Store:** [https://assetstore.unity.com](https://assetstore.unity.com) - Sprites, sonidos, scripts gratuitos
- **Documentación oficial:** [https://docs.unity3d.com](https://docs.unity3d.com)

---

## Conclusión

Has aprendido a:

- ✅ Instalar y configurar Unity para Android
- ✅ Crear GameObjects y usar componentes
- ✅ Programar en C# con el ciclo de vida de Unity
- ✅ Implementar input táctil y de teclado
- ✅ Usar el sistema de físicas y colisiones 2D
- ✅ Crear interfaces de usuario (UI)
- ✅ Exportar juegos a dispositivos Android
- ✅ Desarrollar un juego completo funcional desde cero

**Próximo paso:** Explora tutoriales más avanzados (animaciones con Animator, audio con AudioMixer, guardado de datos con PlayerPrefs) y crea tu propio proyecto original.