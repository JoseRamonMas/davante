# Tutorial: Crear tu Primer Juego Android con Unity

## 📱 Juego "Block Dodge"

Tutorial paso a paso para crear un juego completo de Android usando Unity. En este juego, el jugador se moverá izquierda y derecha para esquivar bloques que caen desde arriba.

**Basado en:** [Build A Complete Android Game Today - Unity Android Tutorial](https://www.youtube.com/watch?v=3y28i5n2g0Q)

## 🎯 Objetivos de Aprendizaje

Al finalizar este tutorial, serás capaz de:

- Configurar Unity para desarrollo Android
- Crear sprites y objetos 2D
- Programar movimiento del jugador con toques en pantalla
- Implementar física básica con Rigidbody2D
- Gestionar colisiones entre objetos
- Crear un sistema de puntuación
- Construir y exportar un APK para Android

***

## 📋 Requisitos Previos

### Software Necesario

- **Unity Hub** (última versión)
- **Unity 2023** o superior
- **Android Build Support** (SDK, NDK, JDK)
- **Visual Studio** o editor de código


### Recursos Gráficos

Descarga los sprites desde: [Kenney.nl - Puzzle Pack](https://www.kenney.nl/assets/puzzle-pack) o usa los proporcionados en el video.

***

## 🚀 Parte 1: Configuración Inicial del Proyecto [[01:00](https://www.youtube.com/watch?v=3y28i5n2g0Q&t=60)]

### Paso 1.1: Crear el Proyecto

1. Abre **Unity Hub**
2. Haz clic en **New Project**
3. Selecciona la plantilla **2D**
4. Nombre del proyecto: `BlockDodgeGame` o `AndroidGame`
5. Elige la ubicación donde guardar el proyecto
6. Haz clic en **Create Project**

### Paso 1.2: Configurar la Plataforma Android

1. Ve a **File → Build Settings**
2. Selecciona **Android** en la lista de plataformas
3. Haz clic en **Switch Platform** (espera a que termine el proceso)
4. Cierra la ventana

> ⚠️ **Importante**: Asegúrate de tener instalado **Android Build Support** en Unity Hub:
> - Unity Hub → Installs → Gear Icon (⚙️) → Add Modules
> - Marca: Android Build Support, Android SDK \& NDK Tools, OpenJDK

### Paso 1.3: Configurar la Resolución [[02:36](https://www.youtube.com/watch?v=3y28i5n2g0Q&t=156)]

1. En la pestaña **Game**, cambia de "Free Aspect" a **16:9 Portrait**
2. Alternativamente, puedes crear una resolución personalizada de **1080x1920** (Portrait)
3. Esta será la resolución móvil que utilizaremos

### Paso 1.4: Organizar el Proyecto [[03:08](https://www.youtube.com/watch?v=3y28i5n2g0Q&t=188)]

1. En la carpeta **Assets**, renombra la escena "SampleScene" a **Game**
2. Crea las siguientes carpetas dentro de Assets:
    - `Sprites` (para imágenes)
    - `Scripts` (para código C\#)
    - `Prefab` (para objetos reutilizables)

***

## 🎨 Parte 2: Escena y Elementos Visuales [[03:58](https://www.youtube.com/watch?v=3y28i5n2g0Q&t=238)]

### Paso 2.1: Importar Sprites

1. Selecciona la carpeta **Sprites**
2. Arrastra y suelta los siguientes archivos:
    - Sprite del suelo (ej. `groundTile.png`)
    - Sprite del jugador (ej. `pig.png` o personaje)
    - Sprite del obstáculo (ej. `crate.png` o bloque)

### Paso 2.2: Crear el Suelo

1. Arrastra el sprite del suelo desde Sprites a la **Scene**
2. Renómbralo como **Ground**
3. Posiciónalo en la parte inferior (Y: -4 aproximadamente)
4. En el componente **Sprite Renderer** [[04:42](https://www.youtube.com/watch?v=3y28i5n2g0Q&t=282)]:
    - Cambia **Draw Mode** de "Simple" a **Tiled**
5. Usa la herramienta **Rect Tool** (tecla T) para estirar el suelo horizontalmente hasta cubrir toda la pantalla

### Paso 2.3: Configurar el Fondo

1. Selecciona **Main Camera** en la jerarquía
2. En el componente **Camera**, cambia **Background** a un color azul cielo claro

### Paso 2.4: Crear el Jugador

1. Arrastra el sprite del jugador a la escena
2. Renómbralo como **Player**
3. Posiciónalo: X: 0, Y: -2 (sobre el suelo)
4. Ajusta el tamaño (Scale) presionando **Shift** y escalando uniformemente
5. En **Sprite Renderer**, cambia **Order in Layer** a **1** (para que aparezca delante del suelo)

### Paso 2.5: Crear el Obstáculo

1. Arrastra el sprite del bloque a la escena
2. Renómbralo como **Block**
3. Posiciónalo temporalmente en Y: 2 o más arriba
4. Ajusta su tamaño según prefieras

***

## ⚙️ Parte 3: Configuración de Física [[06:00](https://www.youtube.com/watch?v=3y28i5n2g0Q&t=360)]

### Paso 3.1: Configurar el Jugador (Player)

1. Selecciona **Player**
2. Haz clic en **Add Component**:
    - **Physics 2D → Rigidbody 2D**
    - Configura:
        - **Gravity Scale**: **0** (no queremos que caiga)
        - **Constraints → Freeze Rotation Z**: ✓ (marcado)
        - **Constraints → Freeze Position Y**: ✓ (marcado - opcional según versión)
3. Añade otro componente:
    - **Physics 2D → Box Collider 2D**
    - Haz clic en **Edit Collider** y ajusta el tamaño para que se ajuste mejor al sprite del personaje

### Paso 3.2: Configurar el Obstáculo (Block) [[07:26](https://www.youtube.com/watch?v=3y28i5n2g0Q&t=446)]

1. Selecciona **Block**
2. Añade **Rigidbody 2D** (dejar configuración por defecto, con gravedad = 1 para que caigan)
3. Añade **Box Collider 2D** y ajústalo al sprite

### Paso 3.3: Crear Tag para el Block [[08:15](https://www.youtube.com/watch?v=3y28i5n2g0Q&t=495)]

1. Con **Block** seleccionado, ve a **Tag** (arriba en el Inspector) → **Add Tag**
2. Haz clic en el **+** y crea un tag llamado `Block`
3. Vuelve a seleccionar **Block** y asígnale el tag **Block**

***

## 💻 Parte 4: Programación - Script del Block

### Paso 4.1: Crear el Script

1. Ve a la carpeta **Scripts**
2. Clic derecho → **Create → C\# Script**
3. Nómbralo: `Block`

### Paso 4.2: Código del Block

Abre el script `Block.cs` y escribe:

```csharp
using UnityEngine;

public class Block : MonoBehaviour
{
    void Update()
    {
        // Destruir el bloque si sale de la pantalla (cae demasiado)
        if (transform.position.y < -6f)
        {
            Destroy(gameObject);
        }
    }
}
```

**Explicación**: Este código elimina el bloque cuando cae fuera de la pantalla (por debajo de Y = -6) para liberar memoria y optimizar el juego.

### Paso 4.3: Asignar el Script

1. Selecciona **Block** en la jerarquía
2. Arrastra el script `Block` desde la carpeta Scripts al inspector del objeto Block

### Paso 4.4: Crear Prefab del Block [[10:47](https://www.youtube.com/watch?v=3y28i5n2g0Q&t=647)]

1. Arrastra **Block** desde la jerarquía a la carpeta **Prefab**
2. El objeto se volverá azul (indica que es un prefab)
3. **Elimina** o desactiva el Block de la jerarquía (ahora lo generaremos por código)

***

## 🎮 Parte 5: Movimiento del Jugador [[11:16](https://www.youtube.com/watch?v=3y28i5n2g0Q&t=676)]

### Paso 5.1: Crear el Script del Player

1. En la carpeta **Scripts**, crea un nuevo script llamado `Player`
2. Selecciona **Player** en la jerarquía
3. Arrastra el script al inspector

### Paso 5.2: Código del Player

Abre `Player.cs` y escribe:

```csharp
using UnityEngine;
using UnityEngine.SceneManagement;

public class Player : MonoBehaviour
{
    public float moveSpeed = 10f;
    private Rigidbody2D rb;

    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    void Update()
    {
        // Input.GetMouseButton(0) funciona para clic y toque en pantalla
        if (Input.GetMouseButton(0))
        {
            // Convertir posición del toque/clic a coordenadas del mundo
            Vector3 touchPos = Camera.main.ScreenToWorldPoint(Input.mousePosition);

            if (touchPos.x < 0)
            {
                // Tocar lado izquierdo -> Mover izquierda
                rb.AddForce(Vector2.left * moveSpeed);
            }
            else
            {
                // Tocar lado derecho -> Mover derecha
                rb.AddForce(Vector2.right * moveSpeed);
            }
        }
        else
        {
            // Si no tocamos, detener el movimiento
            rb.velocity = Vector2.zero;
        }
    }

    // Detectar colisión con bloques [[20:05](https://www.youtube.com/watch?v=3y28i5n2g0Q&t=1205)]
    void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.gameObject.tag == "Block")
        {
            // Reiniciar la escena actual
            SceneManager.LoadScene(SceneManager.GetActiveScene().name);
        }
    }
}
```

**Explicación del código**:

- `GetMouseButton(0)`: Detecta toques/clics continuos (0 = botón izquierdo/toque)
- `ScreenToWorldPoint`: Convierte coordenadas de pantalla a posición del mundo del juego
- `AddForce`: Aplica fuerza al Rigidbody para mover el jugador suavemente
- `rb.velocity = Vector2.zero`: Detiene el movimiento cuando no se toca
- `OnCollisionEnter2D`: Se ejecuta automáticamente cuando hay una colisión física


### Paso 5.3: Probar el Movimiento

1. Haz clic en **Play**
2. Toca/haz clic en la mitad izquierda de la pantalla → el jugador se mueve a la izquierda
3. Toca/haz clic en la mitad derecha → el jugador se mueve a la derecha
4. El jugador debe detenerse cuando no tocas

***

## 🧱 Parte 6: Crear Límites (Paredes Invisibles) [[17:32](https://www.youtube.com/watch?v=3y28i5n2g0Q&t=1052)]

Para evitar que el jugador se salga de la pantalla, crearemos paredes invisibles a los lados.

### Paso 6.1: Crear el Límite Izquierdo

1. En la jerarquía, **Create Empty** (clic derecho → Create Empty)
2. Renómbralo como **WallLeft**
3. Posición: X: **-2.5**, Y: 0, Z: 0
4. Añade componente **Box Collider 2D**:
    - Size X: **0.5**
    - Size Y: **10**

### Paso 6.2: Crear el Límite Derecho

1. Duplica **WallLeft** (Ctrl+D o Cmd+D)
2. Renómbralo como **WallRight**
3. Cambia posición X: **2.5** (positivo)

### Paso 6.3: Organizar (Opcional)

1. Crea un objeto vacío llamado **Walls**
2. Arrastra **WallLeft** y **WallRight** dentro de **Walls** como hijos

***

## 🎲 Parte 7: Sistema de Spawning (GameManager) [[22:37](https://www.youtube.com/watch?v=3y28i5n2g0Q&t=1357)]

### Paso 7.1: Crear GameManager y SpawnPoint

1. **Create Empty** en la jerarquía
2. Renómbralo como **GameManager**
3. Crea otro **Empty** llamado **SpawnPoint**
4. Posiciona **SpawnPoint**: X: 0, Y: **6**, Z: 0 (arriba de la cámara, fuera de vista)

### Paso 7.2: Crear el Script GameManager

1. En **Scripts**, crea `GameManager.cs`
2. Arrástralo al objeto **GameManager**

### Paso 7.3: Código del GameManager (Versión Base)

Abre `GameManager.cs`:

```csharp
using UnityEngine;

public class GameManager : MonoBehaviour
{
    public GameObject blockPrefab;
    public Transform spawnPoint;
    public float spawnRate = 1f;
    public float maxX = 2.5f;

    private bool gameStarted = false;

    void Update()
    {
        // Iniciar el juego con el primer toque [[29:48](https://www.youtube.com/watch?v=3y28i5n2g0Q&t=1788)]
        if (Input.GetMouseButtonDown(0) && !gameStarted)
        {
            StartSpawning();
            gameStarted = true;
        }
    }

    void StartSpawning()
    {
        InvokeRepeating("SpawnBlock", 0.5f, spawnRate);
    }

    void SpawnBlock()
    {
        // Calcular posición X aleatoria dentro de los límites
        Vector3 spawnPos = spawnPoint.position;
        spawnPos.x = Random.Range(-maxX, maxX);

        // Instanciar (crear) el bloque en la posición calculada
        Instantiate(blockPrefab, spawnPos, Quaternion.identity);
    }
}
```

**Explicación**:

- `InvokeRepeating`: Llama a una función repetidamente cada X segundos
- `Random.Range(-maxX, maxX)`: Genera un número aleatorio entre -2.5 y 2.5 para la posición X
- `Instantiate`: Crea una copia del prefab en el juego
- `Quaternion.identity`: Rotación por defecto (sin rotación)


### Paso 7.4: Asignar Referencias

1. Selecciona **GameManager**
2. En el inspector:
    - **Block Prefab**: Arrastra el prefab Block desde la carpeta Prefab
    - **Max X**: **2.5** (o 2 según el ancho de tu pantalla)
    - **Spawn Point**: Arrastra el objeto SpawnPoint desde la jerarquía
    - **Spawn Rate**: **1** (genera un bloque cada segundo)

### Paso 7.5: Probar el Sistema de Spawning

1. Haz clic en **Play**
2. Toca la pantalla para comenzar
3. Los bloques deberían caer aleatoriamente desde arriba
4. Mueve el jugador para esquivarlos
5. Si tocas un bloque, el juego debe reiniciarse

***

## 📊 Parte 8: Sistema de Puntuación (UI) [[34:15](https://www.youtube.com/watch?v=3y28i5n2g0Q&t=2055)]

### Paso 8.1: Crear Canvas

1. Clic derecho en jerarquía → **UI → Canvas**
2. Dentro del Canvas, crea:
    - **UI → Text - TextMeshPro** (si es la primera vez, acepta importar **TMP Essentials**)
    - Renómbralo como **ScoreText**

### Paso 8.2: Configurar ScoreText

1. Con ScoreText seleccionado:
    - **Rect Transform**: Posición Y: **800** (parte superior de la pantalla)
    - **Text Input**: "0" o "Score: 0"
    - **Font Size**: **60** o más grande
    - **Alignment**: Centro horizontal y vertical
    - **Color**: Blanco

### Paso 8.3: Crear Texto de Inicio

1. Duplica **ScoreText** (Ctrl+D)
2. Renómbralo como **TapToStartText**
3. Configuración:
    - Posición: X: 0, Y: 0 (centro de la pantalla)
    - **Text**: "Tap To Start"
    - **Font Size**: **80** o más grande

### Paso 8.4: Actualizar GameManager Script

Modifica `GameManager.cs` para incluir la puntuación:

```csharp
using UnityEngine;
using TMPro; // ¡Importante! Añadir esta línea para TextMeshPro

public class GameManager : MonoBehaviour
{
    public GameObject blockPrefab;
    public Transform spawnPoint;
    public float spawnRate = 1f;
    public float maxX = 2.5f;

    public TextMeshProUGUI scoreText; // Referencia al texto de puntuación
    public GameObject tapText; // Referencia al texto "Tap to Start"

    private bool gameStarted = false;
    private int score = 0;

    void Update()
    {
        if (Input.GetMouseButtonDown(0) && !gameStarted)
        {
            StartSpawning();
            gameStarted = true;
            tapText.SetActive(false); // Ocultar el texto de inicio
        }
    }

    void StartSpawning()
    {
        InvokeRepeating("SpawnBlock", 0.5f, spawnRate);
    }

    void SpawnBlock()
    {
        Vector3 spawnPos = spawnPoint.position;
        spawnPos.x = Random.Range(-maxX, maxX);
        
        Instantiate(blockPrefab, spawnPos, Quaternion.identity);
        
        // Incrementar puntuación cada vez que aparece un bloque
        ScoreUp();
    }

    void ScoreUp()
    {
        score++;
        scoreText.text = score.ToString();
        // O si prefieres: scoreText.text = "Score: " + score;
    }
}
```

**Explicación**:

- `TMPro`: Necesario para usar TextMeshProUGUI
- `SetActive(false)`: Oculta el GameObject del texto
- `ToString()`: Convierte el número a texto


### Paso 8.5: Asignar Referencias UI

1. Selecciona **GameManager**
2. En el inspector:
    - Arrastra **ScoreText** al campo "Score Text"
    - Arrastra **TapToStartText** al campo "Tap Text"

### Paso 8.6: Probar el Sistema Completo

1. Haz clic en **Play**
2. Deberías ver "Tap To Start" en el centro
3. Al tocar, el texto desaparece y comienza el juego
4. La puntuación debe aumentar conforme aparecen bloques

***

## 📱 Parte 9: Configuración Android y Build [[45:10](https://www.youtube.com/watch?v=3y28i5n2g0Q&t=2710)]

### Paso 9.1: Configurar Build Settings

1. **File → Build Settings**
2. Asegúrate de que la escena **Game** esté en "Scenes in Build"
    - Si no está, haz clic en **Add Open Scenes**
3. En **Platform**, asegúrate de que **Android** esté seleccionado

### Paso 9.2: Configurar Player Settings

1. En Build Settings, haz clic en **Player Settings**
2. En la sección **Company Name**: Escribe tu nombre o nombre de la empresa
3. En **Product Name**: Nombre del juego (ej. "Block Dodge")
4. **Default Icon**: Arrastra el sprite de tu jugador al cuadro de icono para usarlo como ícono de la app
5. En **Resolution and Presentation**:
    - **Default Orientation**: Portrait
    - Desactiva **Auto Rotation** (desmarca todas las orientaciones excepto Portrait)
6. En **Other Settings → Identification**:
    - **Override Default Package Name**: Marca esta opción
    - **Package Name**: `com.TuNombre.BlockDodge` (sin espacios, formato: com.empresa.juego)
    - **Version**: 1.0
    - **Bundle Version Code**: 1
    - **Minimum API Level**: Android 5.0 o superior

### Paso 9.3: Construir el APK

1. En Build Settings, haz clic en **Build** (no "Build and Run" todavía)
2. Elige dónde guardar el APK (crea una carpeta "Builds")
3. Nómbralo: `BlockDodge.apk` o `juego.apk`
4. Espera a que termine el proceso (puede tardar varios minutos)

### Paso 9.4: Instalar en Android

1. Conecta tu dispositivo Android por USB al ordenador
2. En el teléfono, activa:
    - **Opciones de desarrollador** (buscar en Ajustes)
    - **Depuración USB** dentro de opciones de desarrollador
3. Transfiere el archivo APK al teléfono (por cable, email, o Drive)
4. En el teléfono, localiza el APK con el explorador de archivos
5. Toca para instalar (puede requerir permisos para "Orígenes desconocidos" o "Instalar apps desconocidas")

> 💡 **Consejo**: También puedes usar **Build and Run** en Unity si el dispositivo está correctamente conectado y configurado.

***

## 🎉 Parte 10: Mejoras Opcionales

### Ideas para Expandir el Juego

**Nivel Fácil:**

- Cambiar colores, sprites y fondo del juego
- Añadir música de fondo y efectos de sonido
- Crear diferentes tipos de obstáculos con sprites distintos
- Modificar la velocidad de caída de los bloques

**Nivel Intermedio:**

- Sistema de vidas (3 colisiones antes de Game Over)
- Pantalla de Game Over con botón de reinicio
- Aumentar velocidad de caída progresivamente (dificultad incremental)
- Guardar high score con `PlayerPrefs`

**Nivel Avanzado:**

- Power-ups que caen (escudos, slow-motion, puntos dobles)
- Diferentes escenarios o niveles
- Animaciones para el jugador y bloques
- Menú principal y pantalla de ajustes
- Efectos de partículas al colisionar

***

## 🐛 Solución de Problemas Comunes

### El jugador no se mueve

- Verifica que el script `Player` esté asignado al objeto Player
- Comprueba que Rigidbody2D tenga **Gravity Scale en 0**
- Asegúrate de que `moveSpeed` tenga un valor positivo (ej: 10)
- Revisa que no haya errores en la consola


### Los bloques no caen

- Verifica que Rigidbody2D del Block tenga **Gravity Scale en 1** (valor por defecto)
- Comprueba que el prefab Block esté correctamente asignado en GameManager
- El Block en la jerarquía debe estar desactivado/eliminado


### Los bloques no se generan

- Asegúrate de tocar la pantalla para iniciar el juego
- Verifica que SpawnPoint esté correctamente asignado en GameManager
- Comprueba que el prefab Block esté asignado en el campo "Block Prefab"
- Revisa los valores de `maxX` y `spawnRate`


### El APK no se construye

- Verifica que Android Build Support esté instalado correctamente
- Comprueba que el Package Name no tenga espacios ni caracteres especiales
- Asegúrate de tener suficiente espacio en disco
- Revisa la consola de Unity para mensajes de error específicos


### El juego no reinicia al colisionar

- Verifica que el tag "Block" esté creado y asignado a los bloques
- Comprueba que `using UnityEngine.SceneManagement;` esté al inicio del script Player
- Asegúrate de que la escena "Game" esté añadida en Build Settings

***

## 📚 Conceptos Clave Aprendidos

- **Rigidbody2D**: Componente que añade física a objetos 2D (gravedad, fuerzas, velocidad)
- **Collider2D**: Define el área de colisión de un objeto para detectar contacto con otros
- **Prefabs**: Plantillas de objetos reutilizables que puedes instanciar múltiples veces
- **Tags**: Etiquetas para identificar y clasificar tipos de objetos en el código
- **Canvas**: Contenedor para elementos de interfaz de usuario (UI)
- **InvokeRepeating**: Ejecuta una función automáticamente cada cierto tiempo
- **Instantiate**: Crea copias de un objeto (prefab) durante el juego
- **ScreenToWorldPoint**: Convierte coordenadas de pantalla a coordenadas del mundo del juego
- **OnCollisionEnter2D**: Método que se ejecuta automáticamente cuando dos colliders colisionan

***

## 📖 Recursos Adicionales

- [Documentación oficial de Unity](https://docs.unity3d.com/)
- [Unity Learn - Tutoriales oficiales](https://learn.unity.com/)
- [Kenney.nl - Assets gratuitos](https://www.kenney.nl/assets)
- [Unity Scripting API](https://docs.unity3d.com/ScriptReference/)
- [Video original del tutorial](https://www.youtube.com/watch?v=3y28i5n2g0Q)

***

## ✅ Checklist Final

Antes de construir tu APK, verifica:

- [ ] La escena está guardada (File → Save)
- [ ] Todos los scripts están asignados a sus objetos correspondientes
- [ ] No hay errores en la consola de Unity
- [ ] El juego funciona correctamente en el editor (botón Play)
- [ ] La plataforma está configurada como Android
- [ ] Player Settings tiene un Package Name válido
- [ ] La escena Game está en Build Settings
- [ ] Los sprites y prefabs están correctamente configurados

***

**¡Felicidades!** 🎉 Has creado tu primer juego completo para Android con Unity. Este proyecto te ha enseñado los fundamentos del desarrollo de videojuegos móviles que podrás aplicar en proyectos más complejos.