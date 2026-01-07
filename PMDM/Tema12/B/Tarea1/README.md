# Tema 12B. Unity para Android

**Tarea 1: "AstroJump: Desarrollo profesional con Unity"**

## 1. Introducción

Durante el curso has aprendido a crear aplicaciones Android usando Java y Android Studio. En el Tema 12 viste lo complejo que es crear un videojuego "desde cero" (nativo): tuviste que gestionar hilos (`Threads`), dibujar píxel a píxel en el `Canvas` y calcular colisiones manualmente.

En esta tarea vamos a dar el salto a la industria profesional. Vas a desarrollar un videojuego completo usando **Unity**, el motor de videojuegos más utilizado del mundo. Verás cómo tareas que en Java te llevaban horas (como detectar una colisión o añadir físicas), en Unity se hacen en segundos arrastrando componentes.

**El objetivo:** Crear un clon sencillo del famoso "Flappy Bird" llamado **"AstroJump"**, donde un cubo debe esquivar obstáculos, y exportarlo como un archivo `.apk` real para tu móvil Android.

***

## 2. Objetivos de aprendizaje

Al finalizar esta tarea serás capaz de:

- Instalar y configurar el entorno de desarrollo Unity para Android
- Comprender la interfaz de Unity (escena, jerarquía, inspector, proyecto)
- Utilizar el sistema de físicas 2D (Rigidbody, colliders)
- Programar scripts básicos en C# (movimiento y lógica de juego)
- Utilizar el sistema de prefabs para instanciar enemigos infinitos
- Compilar y firmar una aplicación (build) desde Unity hacia Android

***

## 3. FASE 1: Instalación y configuración del entorno

Esta es la parte más crítica. Si no configuras bien la instalación, no podrás exportar a Android. **Lee cada paso con atención y márcalo cuando lo completes.**

### Paso 1.1: Descargar e instalar Unity Hub

**Unity Hub** es el gestor de versiones y proyectos de Unity. Es como un "launcher" que te permite tener múltiples versiones del editor instaladas.

1. Abre tu navegador y ve a [unity.com/download](https://unity.com/download)
2. Haz clic en el botón verde **"Download for Windows"** (o el sistema que uses)
3. Se descargará un archivo ejecutable llamado `UnityHubSetup.exe` (o similar)
4. Ejecuta el instalador descargado y sigue el asistente:
   - Acepta los términos de licencia
   - Deja la ruta de instalación por defecto (normalmente `C:\Program Files\Unity Hub`)
   - Pulsa **Install** y espera a que termine
5. Al finalizar, marca la opción **"Launch Unity Hub"** y pulsa **Finish**

**✓ Verificación:** Se abrirá Unity Hub. Verás una ventana oscura con el logo de Unity.

### Paso 1.2: Crear cuenta Unity ID

Para usar Unity necesitas una cuenta gratuita.

1. En Unity Hub, verás un botón **"Sign in"** arriba a la derecha. Haz clic
2. Se abrirá tu navegador. Haz clic en **"Create account"**
3. Rellena el formulario:
   - Email (usa tu email de estudiante si tienes)
   - Contraseña (guárdala bien, la necesitarás)
   - Nombre completo
   - Username (nombre de usuario único)
4. Acepta los términos y haz clic en **"Create a Unity ID"**
5. Revisa tu correo y verifica la cuenta haciendo clic en el enlace que te enviarán
6. Vuelve a Unity Hub. Debería detectar que iniciaste sesión automáticamente
7. Te preguntará **"How will you be using Unity?"** → Selecciona **"Student"** o **"Individual"** (ambos son gratuitos)
8. Pulsa **"Continue"**

**✓ Verificación:** Arriba a la derecha de Unity Hub verás tu nombre de usuario en lugar de "Sign in".

### Paso 1.3: Instalar el editor de Unity con módulos Android

Aquí viene **el paso más importante**. Vamos a instalar el editor de Unity Y los componentes necesarios para crear APKs de Android.

1. En Unity Hub, mira la barra lateral izquierda. Verás varios iconos. Haz clic en el icono que dice **"Installs"** (icono de una flecha hacia abajo)
2. La pantalla estará vacía porque aún no tienes ninguna versión instalada
3. Arriba a la derecha, haz clic en el botón azul **"Install Editor"**
4. Te aparecerá una lista de versiones disponibles. **¡IMPORTANTE!** Busca una que tenga la etiqueta **"LTS"** (Long Term Support) de color verde
   - Ejemplo: `2022.3.17f1 (LTS)` o `2023.2.X (LTS)` o posterior
   - Las versiones LTS son las más estables
5. Haz clic en el botón **"Install"** junto a la versión LTS que elijas
6. Aparecerá una ventana titulada **"Add modules"**. Aquí es donde mucha gente se equivoca. Debes marcar:

**MÓDULOS OBLIGATORIOS** (márcalos todos o no podrás exportar a Android):

```
☑ Microsoft Visual Studio Community 2022
   (Editor de código C#, si ya tienes Visual Studio puedes desmarcarlo)

☑ Android Build Support
   └─ ☑ OpenJDK
   └─ ☑ Android SDK & NDK Tools
```

**Cómo marcar los submódulos:**
- Primero marca **Android Build Support**
- Verás una **flechita ▼** a la izquierda. Haz clic para desplegar
- Dentro verás dos opciones más: **OpenJDK** y **Android SDK & NDK Tools**
- Márcalas AMBAS

7. Revisa que todo esté marcado correctamente. Tu lista debería verse así:

```
☑ Android Build Support
  ☑ OpenJDK
  ☑ Android SDK & NDK Tools
☑ Microsoft Visual Studio Community 2022 (opcional si ya lo tienes)
```

8. Haz clic en **"Continue"** (esquina inferior derecha)
9. Acepta los términos de licencia de Android SDK haciendo clic en **"I have read and agree"** y luego **"Done"**
10. Ahora comenzará la descarga e instalación. **Esto tardará bastante tiempo** (30-60 minutos dependiendo de tu conexión):
    - Se descargarán aproximadamente 8-12 GB
    - La barra de progreso mostrará el porcentaje
    - Puedes seguir con otras tareas, pero no cierres Unity Hub

**✓ Verificación:** Cuando termine, en la pestaña "Installs" verás una tarjeta con:
- El número de versión (ej: `2022.3.17f1`)
- El logo de Android verde (confirma que los módulos Android están instalados)
- Un botón de engranaje ⚙️ a la derecha

**⚠️ CONSEJO:** Si durante la instalación te da un error relacionado con Android SDK, es posible que tu antivirus lo esté bloqueando. Desactívalo temporalmente durante la instalación.

***

## 4. FASE 2: Creación del proyecto y familiarización con la interfaz

### Paso 2.1: Crear un nuevo proyecto 2D

1. En Unity Hub, haz clic en la pestaña lateral **"Projects"** (icono de carpeta)
2. Arriba a la derecha, haz clic en el botón azul **"New project"**
3. Te aparecerá una ventana con plantillas de proyecto. Verás varias opciones:
   - 3D (Core)
   - 3D (URP) / 3D (HDRP)
   - **2D (Core)** ← **Selecciona esta**
   - 2D (URP)
4. Haz clic en la plantilla **"2D (Core)"**. Se pondrá con un borde azul
5. En la parte derecha de la ventana, configura:
   - **Project name:** `AstroJump_TuNombre` (sustituye TuNombre por tu nombre o apellido, sin espacios)
   - **Location:** Deja la ruta por defecto o elige una carpeta donde guardar tu proyecto (por ejemplo `Documentos/Unity Projects/`)
   - **Organization:** Deja el valor por defecto
6. Haz clic en el botón azul **"Create project"** (esquina inferior derecha)
7. **Espera pacientemente.** Unity tardará 2-5 minutos en crear el proyecto y abrir el editor por primera vez

**✓ Verificación:** Se abrirá el editor de Unity. Verás una interfaz compleja con varias ventanas. No te asustes, vamos a explicarlas.

### Paso 2.2: Conocer la interfaz de Unity (explicación visual)

Cuando Unity se abra, verás esta distribución (pueden variar ligeramente las posiciones, pero están todas):

```
┌─────────────────────────────────────────────────────────────┐
│  Archivo Edit Assets GameObject Component Window Help      │  ← BARRA DE MENÚS
├─────────────────────────────────────────────────────────────┤
│  ◄ ► ▶ ⏸       [Mano] [Mover] [Rotar] [Escalar] [Rect]   │  ← BARRA DE HERRAMIENTAS
├──────────┬──────────────────────────────────┬──────────────┤
│ HIERARCHY│         SCENE / GAME             │  INSPECTOR   │
│          │                                  │              │
│ Lista de │    Vista 2D donde ves y editas   │ Propiedades  │
│ objetos  │    los objetos del juego         │ del objeto   │
│ en la    │                                  │ seleccionado │
│ escena   │                                  │              │
│          │                                  │              │
├──────────┴──────────────────────────────────┤              │
│         PROJECT                             │              │
│  Carpetas y archivos del proyecto           │              │
│  (Assets, scripts, prefabs...)              │              │
└─────────────────────────────────────────────┴──────────────┘
```

**Ventana HIERARCHY (jerarquía)** - Panel izquierdo:
- Lista todos los objetos que existen en tu escena actual
- Es como el "árbol de vistas" de Android Studio
- Por defecto verás: `Main Camera` (la cámara que muestra el juego) y `Global Volume` (efectos visuales)

**Ventana SCENE/GAME (escena/juego)** - Panel central:
- Tiene dos pestañas:
  - **Scene:** Donde EDITAS el juego (arrastras objetos, los colocas, etc.)
  - **Game:** Cómo se verá el juego cuando lo ejecutes
- Puedes hacer zoom con la rueda del ratón
- Puedes moverte manteniendo clic derecho y moviendo el ratón
- Puedes arrastrar la vista manteniendo clic central (rueda) y moviendo el ratón

**Ventana INSPECTOR** - Panel derecho:
- Muestra las **propiedades** del objeto que tengas seleccionado
- Es como el panel "Attributes" de Android Studio, pero MUCHO más potente
- Aquí verás componentes como Transform, Sprite Renderer, Rigidbody, etc.

**Ventana PROJECT** - Panel inferior:
- Es tu **explorador de archivos** del proyecto
- Aquí están tus scripts, imágenes, sonidos, prefabs, escenas, etc.
- Carpeta principal: `Assets` (todos tus archivos van aquí)

**Barra de herramientas superior:**
- **Botón ▶️ (Play):** Ejecuta el juego para probarlo
- **Botón ⏸ (Pause):** Pausa el juego
- **Botón ▶▶ (Step):** Avanza el juego frame a frame

**⚠️ MUY IMPORTANTE:** Cuando pulses ▶️ (Play) para probar el juego, cualquier cambio que hagas NO se guardará. Es solo para testear. Si quieres guardar cambios, PARA el juego primero (vuelve a pulsar ▶️).

### Paso 2.3: Configurar la vista para móvil vertical

Por defecto, Unity muestra una vista de juego genérica. Vamos a configurarla para que se vea como una pantalla de móvil vertical (como se verá realmente en tu teléfono).

1. Localiza la ventana **Game** (pestaña central, al lado de "Scene"). Haz clic en la pestaña **"Game"**
2. Justo debajo de la pestaña "Game", verás un desplegable que dice **"Free Aspect"**
3. Haz clic en "Free Aspect" y se abrirá un menú
4. Busca en la lista **"Full HD (1080x1920)"** y selecciónalo
   - Si no lo ves, no te preocupes, sigue al paso 5
5. Si no existe esa opción, vamos a crearla:
   - Haz clic en el desplegable "Free Aspect" de nuevo
   - Abajo del todo, haz clic en el **"+"** (signo más)
   - Te aparecerá un menú "Add aspect ratio"
   - Configura:
     - **Type:** Aspect Ratio
     - **Width:** 1080
     - **Height:** 1920
     - **Label:** Móvil Portrait
   - Pulsa **"OK"**
6. Ahora selecciona tu nueva resolución creada

**✓ Verificación:** La ventana Game ahora se ve vertical y estrecha, como la pantalla de un teléfono.

### Paso 2.4: Configurar la cámara para 2D

1. En la ventana **Hierarchy**, haz clic en **"Main Camera"**
2. Mira en el **Inspector** (panel derecho)
3. Busca el componente **"Camera"** (tiene un icono de cámara)
4. Localiza la propiedad **"Projection"**
5. Cámbiala de "Perspective" a **"Orthographic"** (si no lo está ya)
   - Orthographic es la proyección adecuada para juegos 2D (sin perspectiva 3D)
6. Cambia el valor de **"Size"** a **5** (esto controla el "zoom" de la cámara)

**✓ Verificación:** Si haces clic en la pestaña "Scene", deberías ver una cuadrícula gris. Estás listo para empezar a crear objetos.

***

## 5. FASE 3: Crear el jugador (físicas y control)

Vamos a crear el personaje principal. En Java tenías que dibujar un `Bitmap`; aquí usaremos un `Sprite` (imagen 2D) por defecto que Unity trae incorporado.

### Paso 3.1: Crear el objeto jugador

1. Asegúrate de estar en la pestaña **"Scene"** (ventana central)
2. En la ventana **Hierarchy** (panel izquierdo), haz clic derecho en un área vacía (donde está el espacio negro)
3. En el menú contextual que aparece, navega por: **2D Object → Sprites → Square**
   - Nota: Si ves "2D Object" directamente, haz clic ahí. Si no, busca "GameObject → 2D Object"
4. Aparecerá un cuadrado blanco en el centro de la pantalla (en Scene) y un nuevo elemento en Hierarchy llamado **"Square"**

**✓ Verificación:** En la vista Scene deberías ver un cuadradito blanco en el centro.

5. Ahora vamos a renombrarlo. En Hierarchy, haz clic derecho sobre **"Square"** → **"Rename"** (o selecciónalo y pulsa F2)
6. Escribe: **`Jugador`** y pulsa Enter

### Paso 3.2: Configurar la posición y aspecto

1. Asegúrate de que el objeto **"Jugador"** esté seleccionado en Hierarchy (debe verse resaltado en azul)
2. Mira en el **Inspector** (panel derecho). Verás varios componentes. El primero es **"Transform"**

**Componente Transform (transformación):**
- **Position (posición):** Dónde está el objeto en la escena (X, Y, Z)
  - X: Horizontal (negativo = izquierda, positivo = derecha)
  - Y: Vertical (negativo = abajo, positivo = arriba)
  - Z: Profundidad (en 2D siempre lo dejaremos en 0)
- **Rotation (rotación):** Giro del objeto en grados
- **Scale (escala):** Tamaño del objeto (1 = tamaño original)

3. Configura el **Transform** así:
   - **Position:** X = `0`, Y = `0`, Z = `0`
   - **Rotation:** X = `0`, Y = `0`, Z = `0`
   - **Scale:** X = `1`, Y = `1`, Z = `1`

4. Ahora busca el componente **"Sprite Renderer"** (renderizador de sprite) en el Inspector
5. Localiza la propiedad **"Color"** (tiene un recuadro blanco)
6. Haz clic en ese recuadro blanco
7. Se abrirá un selector de color. Elige el color que quieras para tu personaje (por ejemplo, **azul** o **verde**)
8. Cierra el selector de color

**✓ Verificación:** Tu cuadrado ahora debería tener el color que elegiste.

### Paso 3.3: Añadir físicas - El componente Rigidbody 2D

Aquí empieza la "magia" de Unity. En lugar de programar manualmente la gravedad (`y = y + velocidad`), Unity lo hace automáticamente con componentes.

**¿Qué es un Rigidbody 2D?**
- Es un componente que le da **propiedades físicas** a un objeto
- Con él, el objeto tiene: masa, gravedad, velocidad, inercia
- Unity calcula automáticamente la física en cada frame

**Cómo añadirlo:**

1. Asegúrate de que **"Jugador"** esté seleccionado
2. En el Inspector, baja hasta el final y encontrarás un botón **"Add Component"**
3. Haz clic en ese botón
4. Aparecerá un cuadro de búsqueda. Escribe: **`Rigidbody 2D`**
5. En los resultados, haz clic en **"Rigidbody 2D"** (fíjate que sea **2D**, no "Rigidbody" a secas)

**✓ Verificación:** En el Inspector aparecerá un nuevo componente llamado "Rigidbody 2D" con muchas propiedades (Mass, Gravity Scale, etc.).

6. Dentro del componente Rigidbody 2D recién añadido, localiza **"Gravity Scale"** (escala de gravedad)
7. Verifica que tenga el valor **1** (es el valor por defecto)
   - Este valor controla qué tan fuerte es la gravedad. 1 = gravedad normal, 2 = el doble, 0 = sin gravedad

**Hagamos una prueba rápida:**

8. Pulsa el botón **▶️ (Play)** en la parte superior central
9. ¡Verás que el cuadrado cae hacia abajo como si tuviera gravedad!
10. Pulsa **▶️ (Play)** de nuevo para detener el juego

**¿Qué acaba de pasar?**
- Unity aplicó automáticamente gravedad al objeto
- El Rigidbody 2D hace todos los cálculos físicos por ti
- En Java, tenías que programar esto manualmente en el `update()` con: `velocidadY += gravedad; jugadorY += velocidadY;`

### Paso 3.4: Añadir colisiones - El componente Box Collider 2D

Ahora vamos a añadir la capacidad de **detectar colisiones**.

**¿Qué es un Collider?**
- Es una "caja invisible" que rodea al objeto
- Unity usa esta caja para detectar cuando dos objetos se tocan
- Sin Collider, los objetos se atravesarían entre sí como fantasmas

**Cómo añadirlo:**

1. Con **"Jugador"** seleccionado, haz clic de nuevo en **"Add Component"**
2. Escribe: **`Box Collider 2D`**
3. Selecciona **"Box Collider 2D"** de los resultados

**✓ Verificación:** Aparecerá un nuevo componente "Box Collider 2D" en el Inspector. Además, si miras en la vista Scene, verás un **contorno verde** alrededor de tu cuadrado. Ese es el collider.

**Probemos la física completa:**

4. Pulsa **▶️ (Play)** nuevamente
5. El cuadrado caerá al vacío infinito (porque no hay suelo todavía)
6. Detén el juego con **▶️ (Play)**

***

### Paso 3.5: Programar el movimiento - Tu primer script en C#

Ahora viene la parte de programación. Vamos a crear un script (programa) que haga que el jugador salte cuando toques la pantalla.

**Crear el archivo del script:**

1. En la ventana **Project** (panel inferior), localiza la carpeta **"Assets"**
2. Haz clic derecho sobre la carpeta "Assets" (o en el espacio vacío dentro de ella)
3. En el menú, selecciona: **Create → C# Script**
4. Aparecerá un archivo nuevo con el nombre resaltado en azul, listo para escribir
5. Escribe exactamente: **`ControlJugador`** (sin espacios, respetando mayúsculas y minúsculas)
6. Pulsa **Enter**

**⚠️ IMPORTANTE:** El nombre del archivo debe coincidir exactamente con el nombre de la clase dentro del código. Si lo escribes mal, Unity dará error.

**✓ Verificación:** En la ventana Project verás un archivo con el icono de C# (un documento con el símbolo #) llamado "ControlJugador".

**Editar el script:**

7. Haz **doble clic** sobre el archivo "ControlJugador"
8. Se abrirá **Visual Studio** (o el editor de código que tengas configurado)
9. Verás un código base que Unity genera automáticamente. Se ve así:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class ControlJugador : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        
    }
}
```

10. **BORRA TODO** ese código
11. **Copia y pega** el siguiente código:

```csharp
using UnityEngine;

public class ControlJugador : MonoBehaviour
{
    // Variable pública: se puede ajustar desde el Inspector de Unity
    public float fuerzaSalto = 5f; // Fuerza con la que saltará el jugador
    
    // Variable privada: solo se usa dentro de este script
    private Rigidbody2D rb;

    // Start se ejecuta UNA VEZ cuando el objeto se crea (es como el onCreate() de Android)
    void Start()
    {
        // Obtenemos la referencia al componente Rigidbody2D que añadimos antes
        // Es como hacer un findViewById(), pero para componentes
        rb = GetComponent<Rigidbody2D>();
    }

    // Update se ejecuta EN CADA FRAME (60 veces por segundo normalmente)
    // Es como el bucle del juego que hacías en Java
    void Update()
    {
        // Input.GetMouseButtonDown(0) detecta el PRIMER FRAME donde se toca la pantalla
        // En el móvil, un toque = clic de ratón
        // (0) significa botón izquierdo = primer dedo
        if (Input.GetMouseButtonDown(0))
        {
            // Antes de saltar, reseteamos la velocidad a cero
            // Esto evita que los saltos se "acumulen" si pulsas muy rápido
            rb.velocity = Vector2.zero;
            
            // AddForce añade un impulso de fuerza al objeto
            // Vector2.up es el vector (0, 1), es decir, hacia arriba
            // ForceMode2D.Impulse significa que es un golpe instantáneo (no continuo)
            // fuerzaSalto multiplica la fuerza (a mayor valor, mayor salto)
            rb.AddForce(Vector2.up * fuerzaSalto, ForceMode2D.Impulse);
        }
    }
}
```

12. **Guarda el archivo:** Pulsa `Ctrl + S` (Windows) o `Cmd + S` (Mac)
13. Vuelve a **Unity** (no cierres Visual Studio, lo usaremos más tarde)

**Asignar el script al jugador:**

Ahora que has creado el script, necesitas "pegarlo" al objeto Jugador. En Unity, los scripts son componentes que se arrastran a los objetos.

14. En la ventana **Project**, localiza tu script **"ControlJugador"**
15. **Arrastra** el archivo "ControlJugador" desde Project...
16. ...y **suéltalo** sobre el objeto **"Jugador"** en la ventana **Hierarchy**
    - También puedes soltarlo directamente en el **Inspector** con "Jugador" seleccionado

**✓ Verificación:** 
- Selecciona "Jugador" en Hierarchy
- En el Inspector, al final de todo, deberías ver un nuevo componente llamado **"Control Jugador (Script)"**
- Dentro de ese componente verás la propiedad **"Fuerza Salto"** con el valor 5

**Probar el juego:**

17. Pulsa **▶️ (Play)**
18. Haz **clic** con el ratón en la ventana Game (o pulsa la barra espaciadora)
19. El cuadrado debería **saltar hacia arriba** cada vez que hagas clic

**✓ Verificación:** Si al hacer clic el cuadrado salta, ¡lo has conseguido! Ya tienes físicas y control funcionando.

**Si no funciona, revisa:**
- ¿El script está asignado al objeto "Jugador"? (debe aparecer en el Inspector)
- ¿El objeto "Jugador" tiene el componente "Rigidbody 2D"?
- ¿Hay algún error en la consola? (pestaña "Console" abajo, al lado de "Project")

20. Detén el juego con **▶️ (Play)**

***

## 6. FASE 4: Crear el entorno (suelo, techo y obstáculos)

Ahora vamos a crear un suelo para que el jugador no caiga al infinito, un techo para limitarlo por arriba, y obstáculos que esquivar.

### Paso 4.1: Crear el suelo

1. En **Hierarchy**, haz clic derecho → **2D Object → Sprites → Square**
2. Aparecerá un nuevo cuadrado llamado "Square"
3. Renómbralo a: **`Suelo`** (clic derecho → Rename, o F2)

**Configurar el suelo:**

4. Selecciona "Suelo" en Hierarchy
5. En el **Inspector**, configura el componente **Transform**:
   - **Position:** X = `0`, Y = `-4`, Z = `0`
     - (Y negativo lo coloca abajo en la pantalla)
   - **Scale:** X = `20`, Y = `1`, Z = `1`
     - (Lo hacemos muy ancho para que cubra toda la pantalla)

6. En **Sprite Renderer**, cambia el **Color** a otro diferente (por ejemplo, marrón o gris oscuro)

7. Añade un componente **Box Collider 2D**:
   - Botón "Add Component" → busca `Box Collider 2D` → selecciónalo

**✓ Verificación:** Deberías ver una barra horizontal larga en la parte inferior de la pantalla.

**Probemos:**

8. Pulsa **▶️ (Play)**
9. Haz clic para hacer saltar al jugador
10. El jugador debería **caer y posarse sobre el suelo** en lugar de caer al infinito

11. Detén el juego

### Paso 4.2: Crear el techo

Vamos a duplicar el suelo para crear el techo (es más rápido que crearlo desde cero).

1. Selecciona el objeto **"Suelo"** en Hierarchy
2. Pulsa `Ctrl + D` (Windows) o `Cmd + D` (Mac) para duplicarlo
3. Aparecerá un objeto llamado "Suelo (1)"
4. Renómbralo a: **`Techo`**
5. En el Inspector, en **Transform**, cambia solo la **Position Y** a: `4`
   - (Y positivo lo coloca arriba)

**✓ Verificación:** Ahora tienes dos barras horizontales: una abajo (Suelo) y otra arriba (Techo).

***

### Paso 4.3: Crear el obstáculo

Ahora crearemos un obstáculo vertical (como las tuberías de Flappy Bird).

1. Crea un nuevo cuadrado: **2D Object → Sprites → Square**
2. Renómbralo a: **`Obstaculo`**

**Configurar el obstáculo:**

3. Selecciona "Obstaculo" en Hierarchy
4. En el Inspector, **Transform**:
   - **Position:** X = `5`, Y = `0`, Z = `0`
     - (X positivo lo coloca a la derecha, fuera de la pantalla inicialmente)
   - **Scale:** X = `0.8`, Y = `8`, Z = `1`
     - (Lo hacemos alto y estrecho, como una columna)

5. En **Sprite Renderer**, cambia el **Color** a rojo (o el que quieras para los obstáculos)

6. Añade un componente **Box Collider 2D**

7. **MUY IMPORTANTE:** Dentro del componente **Box Collider 2D**, marca la casilla **"Is Trigger"**

**¿Qué significa "Is Trigger"?**
- Sin "Is Trigger": Los objetos rebotan físicamente (colisión sólida)
- Con "Is Trigger": Los objetos se atraviesan, pero podemos detectar el toque con código
- Lo necesitamos para detectar cuando el jugador choca y hacer "Game Over"

**✓ Verificación:** Deberías ver un rectángulo vertical rojo (o del color que elegiste) a la derecha de la pantalla.

***

### Paso 4.4: Script para mover el obstáculo

Los obstáculos deben moverse hacia la izquierda automáticamente (como en Flappy Bird). Vamos a programarlo.

1. En la ventana **Project**, haz clic derecho en "Assets" → **Create → C# Script**
2. Nómbralo: **`MoverObstaculo`**
3. Haz **doble clic** para abrirlo en Visual Studio
4. **Borra todo** el código que trae por defecto
5. **Copia y pega** este código:

```csharp
using UnityEngine;

public class MoverObstaculo : MonoBehaviour
{
    // Velocidad a la que se moverá el obstáculo hacia la izquierda
    public float velocidad = 3f;

    // Update se ejecuta en cada frame
    void Update()
    {
        // transform.Translate mueve el objeto
        // Vector2.left es el vector (-1, 0), es decir, hacia la izquierda
        // velocidad controla qué tan rápido se mueve
        // Time.deltaTime es el tiempo transcurrido desde el último frame
        // (Time.deltaTime asegura que la velocidad sea igual en móviles rápidos y lentos)
        transform.Translate(Vector2.left * velocidad * Time.deltaTime);

        // Si el obstáculo se sale mucho por la izquierda de la pantalla
        // lo destruimos para ahorrar memoria
        if (transform.position.x < -10)
        {
            Destroy(gameObject); // Destruye este objeto
        }
    }
}
```

6. **Guarda** el archivo (`Ctrl + S`)
7. Vuelve a **Unity**

**Asignar el script al obstáculo:**

8. Arrastra el script **"MoverObstaculo"** desde Project...
9. ...hacia el objeto **"Obstaculo"** en Hierarchy

**✓ Verificación:** Selecciona "Obstaculo" en Hierarchy. En el Inspector deberías ver el componente "Mover Obstaculo (Script)" con la propiedad "Velocidad" = 3.

**Probar:**

10. Pulsa **▶️ (Play)**
11. El obstáculo debería **moverse hacia la izquierda** automáticamente
12. El jugador puede saltar y atravesarlo (porque es Trigger)
13. Detén el juego

***

### Paso 4.5: Convertir el obstáculo en Prefab (molde reutilizable)

Ahora mismo solo tienes UN obstáculo. Queremos crear **infinitos obstáculos** que aparezcan continuamente. Para eso usaremos **Prefabs**.

**¿Qué es un Prefab?**
- Un Prefab es un "molde" o "plantilla" de un objeto
- Puedes crear copias de ese molde desde código
- Es como tener una clase de la que creas múltiples instancias

**Crear el Prefab:**

1. En la ventana **Project**, haz clic derecho en "Assets" → **Create → Folder**
2. Nombra la carpeta: **`Prefabs`**
3. Haz doble clic en la carpeta "Prefabs" para entrar en ella
4. Ahora, **arrastra** el objeto **"Obstaculo"** desde **Hierarchy**...
5. ...y **suéltalo** dentro de la carpeta **"Prefabs"** en la ventana Project

**✓ Verificación:** 
- En la carpeta "Prefabs" aparecerá un cubo azul llamado "Obstaculo"
- En Hierarchy, el objeto "Obstaculo" ahora se verá de **color azul** (indica que es un Prefab)

6. Ahora **ELIMINA** el objeto "Obstaculo" de la Hierarchy:
   - Selecciona "Obstaculo" en Hierarchy
   - Pulsa la tecla **Supr** (Delete)
   - Confirma si te pregunta

**¿Por qué lo borramos?**
- No lo hemos perdido, está guardado como Prefab en la carpeta
- Lo generaremos infinitas veces desde código
- Si lo dejamos en la escena, tendríamos uno fijo además de los generados

***

### Paso 4.6: Crear el generador de obstáculos (Spawner)

Vamos a crear un objeto invisible que genere obstáculos automáticamente cada pocos segundos.

**Crear el objeto generador:**

1. En **Hierarchy**, haz clic derecho en un espacio vacío → **Create Empty**
2. Aparecerá un objeto llamado "GameObject"
3. Renómbralo a: **`Generador`**

**Configurar la posición:**

4. Selecciona "Generador" en Hierarchy
5. En el **Inspector**, en **Transform**:
   - **Position:** X = `5`, Y = `0`, Z = `0`
     - (A la derecha de la pantalla, fuera de la vista)

**Crear el script del generador:**

6. En **Project**, clic derecho en "Assets" → **Create → C# Script**
7. Nómbralo: **`GeneradorObstaculos`**
8. Ábrelo con doble clic
9. Borra todo y pega este código:

```csharp
using UnityEngine;

public class GeneradorObstaculos : MonoBehaviour
{
    // Esta variable guardará la referencia al Prefab del obstáculo
    // "public GameObject" significa que aceptará cualquier objeto de Unity
    // Lo asignaremos desde el Inspector arrastrando el Prefab
    public GameObject prefabObstaculo;
    
    // Cada cuántos segundos se genera un obstáculo
    public float tiempoEspera = 2f;
    
    // Rango de altura aleatoria para variar la posición Y de los obstáculos
    public float rangoAlturaMin = -2f;
    public float rangoAlturaMax = 2f;

    // Start se ejecuta una vez al inicio
    void Start()
    {
        // InvokeRepeating llama a una función repetidamente
        // Parámetros: ("nombreFunción", retrasoInicial, intervaloRepetición)
        // Esto llamará a la función "Crear" inmediatamente (0f), y luego cada 2 segundos
        InvokeRepeating("Crear", 0f, tiempoEspera);
    }

    // Función que crea un obstáculo
    void Crear()
    {
        // Generamos una altura aleatoria entre el rango mínimo y máximo
        float alturaRandom = Random.Range(rangoAlturaMin, rangoAlturaMax);
        
        // Creamos una nueva posición basada en la posición del Generador
        // pero con la altura aleatoria
        Vector3 posicion = new Vector3(transform.position.x, alturaRandom, 0);
        
        // Instantiate crea una copia del Prefab en esa posición
        // Quaternion.identity significa "sin rotación"
        Instantiate(prefabObstaculo, posicion, Quaternion.identity);
    }
}
```

10. **Guarda** el archivo (`Ctrl + S`)
11. Vuelve a **Unity**

**Asignar el script al Generador:**

12. Arrastra el script **"GeneradorObstaculos"** desde Project hacia el objeto **"Generador"** en Hierarchy

**Asignar el Prefab al script:**

Este paso es crucial. El script necesita saber QUÉ objeto debe generar.

13. Selecciona **"Generador"** en Hierarchy
14. Mira en el **Inspector**. Verás el componente "Generador Obstaculos (Script)"
15. Dentro de ese componente verás una propiedad llamada **"Prefab Obstaculo"** con un campo vacío que dice "None (Game Object)"
16. Abre la carpeta **"Prefabs"** en la ventana Project
17. **Arrastra** el Prefab **"Obstaculo"** (el cubo azul)...
18. ...y **suéltalo** en el campo "Prefab Obstaculo" del Inspector

**✓ Verificación:** El campo "Prefab Obstaculo" ahora debería mostrar "Obstaculo" en lugar de "None".

**¡Momento de la verdad!**

19. Pulsa **▶️ (Play)**
20. Deberías ver que cada 2 segundos aparece un obstáculo a la derecha y se mueve hacia la izquierda
21. Los obstáculos aparecen a diferentes alturas aleatoriamente
22. El jugador puede saltar con clic

**✓ Verificación:** Si ves obstáculos generándose infinitamente, ¡has completado la mecánica principal del juego!

23. Detén el juego

**Si no funciona:**
- ¿El campo "Prefab Obstaculo" está asignado? (no debe decir "None")
- ¿El script "GeneradorObstaculos" está en el objeto "Generador"?
- ¿Hay errores en la consola? (pestaña Console)

***

## 7. FASE 5: Lógica de Game Over (detectar colisiones)

Ahora el juego funciona, pero no pasa nada cuando el jugador choca. Vamos a añadir lógica de "Game Over": cuando el jugador toque un obstáculo, el techo o el suelo, el juego se reiniciará.

### Paso 5.1: Marcar objetos como Trigger

Primero, asegúrate de que los objetos con los que queremos detectar colisiones están configurados correctamente.

1. Selecciona **"Suelo"** en Hierarchy
2. En el **Inspector**, en el componente **Box Collider 2D**, marca **"Is Trigger"**

3. Repite con **"Techo"**:
   - Selecciona "Techo"
   - Marca "Is Trigger" en su Box Collider 2D

4. Verifica que **"Obstaculo"** (el Prefab en la carpeta Prefabs) también tenga "Is Trigger" marcado
   - Haz clic en el Prefab "Obstaculo" en Project → Carpeta Prefabs
   - Mira en el Inspector
   - Box Collider 2D → "Is Trigger" debe estar marcado

**¿Por qué todos Trigger?**
- Si son Trigger, podemos detectar el choque con código
- Si no fueran Trigger, el jugador rebotaría físicamente contra ellos

### Paso 5.2: Añadir detección de colisiones al jugador

Ahora vamos a modificar el script del jugador para detectar cuando toca algo.

1. En **Project**, localiza el script **"ControlJugador"**
2. Haz **doble clic** para abrirlo en Visual Studio

**Añadir la librería de escenas:**

3. En la parte superior del código, donde dice `using UnityEngine;`, añade debajo una nueva línea:

```csharp
using UnityEngine;
using UnityEngine.SceneManagement; // AÑADE ESTA LÍNEA
```

**¿Para qué sirve?**
- Esta librería nos permite recargar escenas (reiniciar el juego)

**Añadir la función de detección de colisiones:**

4. Ve al final del script, DENTRO de la clase (antes de la última llave `}`), y añade esta función:

```csharp
    // Esta función se ejecuta automáticamente cuando un objeto con Trigger toca al jugador
    // "collision" contiene información sobre QUÉ objeto nos tocó
    private void OnTriggerEnter2D(Collider2D collision)
    {
        // Cuando chocamos con CUALQUIER objeto (suelo, techo, obstáculo)
        // recargamos la escena actual, lo que reinicia el juego
        SceneManager.LoadScene(SceneManager.GetActiveScene().name);
    }
```

5. El código completo debería verse así:

```csharp
using UnityEngine;
using UnityEngine.SceneManagement; // Librería para recargar escenas

public class ControlJugador : MonoBehaviour
{
    public float fuerzaSalto = 5f;
    private Rigidbody2D rb;

    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    void Update()
    {
        if (Input.GetMouseButtonDown(0))
        {
            rb.velocity = Vector2.zero;
            rb.AddForce(Vector2.up * fuerzaSalto, ForceMode2D.Impulse);
        }
    }

    // Detecta cuando el jugador toca un Trigger
    private void OnTriggerEnter2D(Collider2D collision)
    {
        // Recarga la escena actual (reinicia el juego)
        SceneManager.LoadScene(SceneManager.GetActiveScene().name);
    }
}
```

6. **Guarda** el archivo (`Ctrl + S`)
7. Vuelve a **Unity**

**Probar el Game Over:**

8. Pulsa **▶️ (Play)**
9. **No hagas clic** (no saltes)
10. El jugador caerá y tocará el suelo
11. En cuanto lo toque, el juego debería **reiniciarse automáticamente**

12. Ahora prueba tocando obstáculos:
    - Haz clic para saltar
    - Deja que el jugador choque contra un obstáculo
    - El juego debería reiniciarse

**✓ Verificación:** Si el juego se reinicia cada vez que tocas algo, ¡funciona perfectamente!

**Si no funciona:**
- ¿Añadiste `using UnityEngine.SceneManagement;` al principio del script?
- ¿Guardaste el script después de editarlo?
- ¿Los Colliders tienen "Is Trigger" marcado?

***

## 8. FASE 6: Exportar a Android (crear el APK)

¡Llegó el momento! Vamos a convertir tu juego en una aplicación Android real que puedas instalar en tu móvil.

### Paso 6.1: Preparar el móvil (activar modo desarrollador)

Antes de conectar tu móvil, necesitas activar las opciones de desarrollador.

**En tu móvil Android:**

1. Ve a **Ajustes** (Settings)
2. Busca **"Acerca del teléfono"** o **"Información del dispositivo"**
3. Busca la opción **"Número de compilación"** o **"Número de versión"**
4. **Toca 7 veces seguidas** sobre "Número de compilación"
   - Te dirá "Ahora eres un desarrollador"
5. Vuelve atrás en Ajustes
6. Ahora verás una nueva opción: **"Opciones de desarrollador"** o **"Developer options"**
7. Entra en "Opciones de desarrollador"
8. Activa la opción **"Depuración por USB"** o **"USB Debugging"**
9. Puede que te pida confirmar, acepta

**Conectar el móvil:**

10. Conecta tu móvil al PC con un **cable USB**
11. En el móvil te aparecerá un mensaje: **"¿Permitir depuración por USB?"**
12. Marca **"Permitir siempre desde este ordenador"** y pulsa **"Aceptar"**

**✓ Verificación:** El móvil debería hacer un sonido de conexión y no deberías ver errores.

### Paso 6.2: Configurar Unity para Android

Ahora le diremos a Unity que queremos compilar para Android (en lugar de PC).

**Abrir Build Settings:**

1. En Unity, ve al menú superior: **File → Build Settings**
2. Se abrirá una ventana titulada "Build Settings"

**Cambiar la plataforma a Android:**

3. En la ventana "Build Settings", verás una lista de plataformas a la izquierda:
   - PC, Mac & Linux Standalone
   - iOS
   - **Android** ← Esta es la que queremos
   - WebGL
   - etc.

4. Haz clic en **"Android"** (solo un clic, para seleccionarlo)
5. Abajo a la derecha, haz clic en el botón **"Switch Platform"**

**⚠️ IMPORTANTE:** Esto tardará varios minutos (5-15 minutos). Unity necesita reconvertir todos los recursos del proyecto al formato Android. La barra de progreso mostrará "Hold on...".

**✓ Verificación:** Cuando termine, el logo de Unity junto a "Android" se verá **más grande y resaltado**. Además, el botón "Switch Platform" desaparecerá.

**Añadir la escena al build:**

6. En la ventana "Build Settings", busca el recuadro **"Scenes In Build"** (arriba)
7. Probablemente esté vacío (o tenga una escena con el ícono de desactivado)
8. Haz clic en el botón **"Add Open Scenes"** (abajo del recuadro)
9. Debería aparecer una línea que dice:
   - `Scenes/SampleScene` (o cómo se llame tu escena)
   - Con una marca ☑ a la izquierda

**✓ Verificación:** Hay al menos una escena en la lista con la casilla marcada.

### Paso 6.3: Configurar las propiedades del jugador (Player Settings)

Ahora vamos a configurar el nombre de tu juego, el identificador de paquete y otros detalles importantes.

**Abrir Player Settings:**

1. En la ventana "Build Settings", busca abajo a la izquierda el botón **"Player Settings..."**
2. Haz clic en ese botón
3. Se abrirá el panel "Inspector" con muchas opciones

**Configuración básica:**

4. En la parte superior del Inspector, verás:
   - **Company Name:** Escribe tu nombre o el de tu "empresa" ficticia (ej: "EstudiosGamer")
   - **Product Name:** Escribe: **`AstroJump`**

5. Baja un poco y verás varios íconos con pestañas. Haz clic en el **ícono de Android** (el robot verde)

**Configurar el paquete (Package Name):**

6. Busca la sección **"Other Settings"** y despliégala (haz clic en la flechita si está cerrada)
7. Busca el apartado **"Identification"**
8. Verás **"Package Name"**. Por defecto dice algo como `com.CompanyName.ProductName`
9. **Cámbialo** por: `com.tunombre.astrojump`
   - Ejemplo: `com.alberto.astrojump`
   - **Importante:** Todo en minúsculas, sin espacios, sin caracteres especiales
   - Este identificador es único para tu app en Android

**Configurar la versión mínima de Android:**

10. En la misma sección "Other Settings", busca **"Minimum API Level"**
11. Cámbialo a: **"Android 7.0 'Nougat' (API level 24)"** o superior
    - Si tu móvil es más antiguo, elige la versión correspondiente

**Configurar la orientación de pantalla:**

12. Baja hasta la sección **"Resolution and Presentation"**
13. Busca **"Default Orientation"**
14. Cámbialo a: **"Portrait"** (vertical)
    - Esto fuerza que el juego siempre se vea vertical

15. Desmarca las opciones:
    - **"Portrait Upside Down"** (para que no se voltee al revés)
    - **"Landscape Right"** y **"Landscape Left"** (para que no se gire a horizontal)
    - Deja solo **"Portrait"** marcado

**Configurar el backend de scripting (opcional pero recomendado):**

16. Vuelve a "Other Settings"
17. Busca **"Scripting Backend"**
18. Cámbialo a: **"IL2CPP"**
    - IL2CPP genera APKs más rápidos y compatibles
    - Mono es más rápido de compilar pero menos optimizado

19. Debajo verás **"Target Architectures"**
20. Marca al menos:
    - ☑ **ARMv7** (dispositivos antiguos)
    - ☑ **ARM64** (dispositivos modernos)

**✓ Verificación:** Revisa que tengas configurado:
- Company Name: tu nombre
- Product Name: AstroJump
- Package Name: com.tunombre.astrojump
- Minimum API Level: Android 7.0 o superior
- Default Orientation: Portrait

### Paso 6.4: Compilar el juego (Build)

¡Ahora sí! Vamos a generar el archivo APK.

1. Asegúrate de que tu móvil siga conectado por USB
2. Vuelve a la ventana **"Build Settings"** (si la cerraste: File → Build Settings)
3. En la parte inferior, busca **"Run Device"**
4. Haz clic en el desplegable junto a "Run Device"
5. Debería aparecer tu móvil en la lista (puede tardar unos segundos)
   - Si dice "No devices detected", haz clic en el botón de **"Refresh"** (actualizar)
   - Si aún no aparece, revisa que la depuración USB esté activada

6. Selecciona tu dispositivo en la lista

**Compilar y ejecutar:**

7. Haz clic en el botón **"Build And Run"** (esquina inferior derecha)
8. Te pedirá dónde guardar el archivo APK
9. Crea una carpeta nueva llamada **"Builds"** dentro de la carpeta de tu proyecto
10. Nombra el archivo: **`AstroJump.apk`**
11. Haz clic en **"Guardar"**

**¡Ahora viene la espera!**

12. Unity empezará a compilar. Verás una barra de progreso con varios pasos:
    - "Building Player..."
    - "Processing scene..."
    - "Building gradle project..."
    - "Compiling scripts..."
    - etc.

**⚠️ ESTO PUEDE TARDAR:** La primera vez que compiles para Android puede tardar **10-30 minutos** dependiendo de tu PC. Las siguientes veces será mucho más rápido (2-5 minutos).

**✓ Verificación exitosa:** 
- Unity mostrará "Build completed successfully"
- El juego se **instalará y abrirá automáticamente** en tu móvil
- Verás tu juego AstroJump corriendo en la pantalla del teléfono

**¡FELICIDADES! Has creado tu primer juego Android con Unity.**

### Paso 6.5: Solución de problemas comunes

**Si aparece un error sobre Android SDK/NDK:**
- Ve a Edit → Preferences → External Tools
- Verifica que las rutas de Android SDK y NDK estén correctamente asignadas
- Unity debería haberlas configurado automáticamente en la instalación

**Si el móvil no se detecta:**
- Desconecta y vuelve a conectar el cable USB
- Revisa que hayas aceptado "Depuración USB" en el móvil
- Prueba con otro cable USB (algunos cables solo cargan, no transmiten datos)
- En Windows, puede que necesites instalar drivers USB de tu fabricante de móvil

**Si el build falla con "Gradle error":**
- Ve a Edit → Preferences → External Tools
- Marca "Android" en la lista
- Haz clic en "Gradle" y selecciona una versión específica o deja que Unity use la suya

**Si el juego no se abre en el móvil:**
- Busca el ícono de "AstroJump" en el cajón de aplicaciones de tu móvil
- Ábrelo manualmente

***

## 9. Mejoras opcionales (si te sobra tiempo)

Si terminas antes de tiempo y quieres mejorar tu juego, aquí tienes ideas:

### Mejora 1: Sistema de puntuación

**Objetivo:** Mostrar cuántos obstáculos has esquivado.

1. Crea un objeto de texto UI: **GameObject → UI → Text - TextMeshPro** (si te pide instalar TMP Essentials, acepta)
2. Llámalo "PuntuacionTexto"
3. Colócalo en la parte superior de la pantalla
4. Crea un script "SistemaPuntuacion" que:
   - Tenga una variable `int puntos = 0`
   - Incremente los puntos cada vez que un obstáculo se destruye
   - Actualice el texto en pantalla

### Mejora 2: Sonidos

**Objetivo:** Añadir sonido al saltar y al chocar.

1. Descarga sonidos gratuitos de [freesound.org](https://freesound.org)
2. Importa los archivos .wav o .mp3 a tu carpeta Assets
3. Añade componente **Audio Source** al jugador
4. En el script ControlJugador, añade:
   - `public AudioClip sonidoSalto;`
   - En la función Update, cuando salte: `GetComponent<AudioSource>().PlayOneShot(sonidoSalto);`

### Mejora 3: Múltiples vidas

**Objetivo:** Dar 3 vidas al jugador antes del Game Over.

1. Añade una variable `int vidas = 3` al script ControlJugador
2. En OnTriggerEnter2D, en lugar de recargar inmediatamente:
   - Resta 1 vida
   - Si vidas == 0, recarga la escena
   - Si vidas > 0, reposiciona al jugador

### Mejora 4: Menú de inicio

**Objetivo:** Pantalla inicial con botón "Jugar".

1. Crea una nueva escena: **File → New Scene**
2. Añade un Canvas con un botón
3. El botón debe cargar la escena del juego con `SceneManager.LoadScene("SampleScene");`
4. En Build Settings, añade ambas escenas (la del menú primero)

***

## 10. Documentación a entregar

Para demostrar que has completado la tarea correctamente, debes entregar:

### Archivo 1: El APK
- Archivo: **AstroJump.apk**
- Ubicación: En la carpeta "Builds" de tu proyecto

### Archivo 2: Informe PDF

Crea un documento PDF con las siguientes capturas de pantalla y textos:

**1. Portada:**
- Título: "Tarea 1 - AstroJump"
- Tu nombre completo
- Curso y fecha

**2. Captura del proyecto en Unity:**
- Haz una captura de la ventana completa de Unity
- Debe verse la escena con el jugador, suelo, techo
- Debe verse la jerarquía con todos los objetos

**3. Captura del Inspector del Jugador:**
- Selecciona el objeto "Jugador" en Hierarchy
- Haz captura del panel Inspector
- Debe verse:
  - Transform
  - Sprite Renderer
  - Rigidbody 2D
  - Box Collider 2D
  - Control Jugador (Script) con "Fuerza Salto = 5"

**4. Captura del script ControlJugador:**
- Abre el script "ControlJugador.cs" en Visual Studio
- Haz captura donde se vea todo el código
- Debe verse claramente la función OnTriggerEnter2D

**5. Captura del Generador configurado:**
- Selecciona el objeto "Generador"
- Haz captura del Inspector
- Debe verse el script "Generador Obstaculos" con el campo "Prefab Obstaculo" asignado

**6. Captura de Build Settings:**
- Abre File → Build Settings
- Haz captura donde se vea:
  - Plataforma "Android" seleccionada
  - La escena añadida en "Scenes In Build"

**7. Foto del juego en tu móvil:**
- Con otro móvil (o una cámara), haz una foto de tu móvil ejecutando el juego
- Debe verse claramente la pantalla con el juego corriendo
- **Alternativa:** Captura de pantalla desde el móvil

**8. Reflexión personal (1-2 párrafos):**

Responde a estas preguntas:

> **a) Comparación con el desarrollo nativo:**
> Compara esta experiencia con la práctica del Tema 12 (juego nativo en Java). 
> - ¿Qué te ha resultado más fácil/difícil?
> - ¿Qué diferencias notaste al usar Rigidbody para la gravedad en lugar de programar `y = y + velocidad` manualmente?
> - ¿Qué ventajas ves en Unity para el desarrollo de juegos?

> **b) Dificultades encontradas:**
> - ¿Qué paso te resultó más complicado? (instalación, programación, exportación...)
> - ¿Cómo lo resolviste?

> **c) Aprendizaje:**
> - ¿Qué es lo más importante que has aprendido en esta tarea?
> - ¿Te gustaría seguir desarrollando en Unity? ¿Por qué?


**Formato del PDF:**
- Usa un procesador de textos (Word, LibreOffice, Google Docs)
- Inserta las capturas de pantalla con buena calidad
- Añade pies de foto explicativos (ej: "Figura 1: Inspector del objeto Jugador")
- Exporta como PDF
- Nombra el archivo: **`AstroJump_Informe_TuNombre.pdf`**

### Cómo subir los archivos a la plataforma

1. Comprime ambos archivos en un ZIP:
   - `AstroJump.apk`
   - `AstroJump_Informe_TuNombre.pdf`
2. Nombra el ZIP: **`AstroJump_TuNombre.zip`**
3. Sube el archivo ZIP a la plataforma Moodle en la tarea correspondiente

---

## 11. Criterios de evaluación

Tu tarea será evaluada según los siguientes criterios:

| Criterio | Peso | Descripción |
|----------|------|-------------|
| **Configuración del entorno** | 20% | Se ha instalado correctamente Unity Hub, el editor y los módulos Android. Se ha conseguido generar el APK funcional (esto demuestra que la configuración fue correcta). |
| **Mecánicas de juego** | 30% | El personaje tiene físicas (Rigidbody 2D), responde a la gravedad y salta correctamente al tocar la pantalla. El movimiento es fluido y predecible. |
| **Generación procedural** | 30% | Los obstáculos aparecen infinitamente desde un Prefab, se mueven hacia la izquierda, aparecen a alturas aleatorias y se autodestruyen al salir de pantalla. |
| **Lógica de juego** | 10% | El juego detecta colisiones y se reinicia correctamente cuando el jugador choca con obstáculos, suelo o techo. |
| **Documentación** | 10% | El informe PDF está completo, con todas las capturas requeridas, legibles y bien organizadas. La reflexión personal es coherente y demuestra comprensión del proceso. |

**Puntuación adicional (opcional, hasta +1 punto):**
- Sistema de puntuación funcional: +0.5 puntos
- Sonidos añadidos (salto y colisión): +0.3 puntos
- Menú de inicio: +0.2 puntos

### Rúbrica detallada

**Configuración del entorno (20%):**
- **Excelente (18-20):** APK generado, instalado y funciona perfectamente en el móvil
- **Bueno (14-17):** APK generado pero con algún problema menor (orientación incorrecta, resolución, etc.)
- **Suficiente (10-13):** APK generado pero no se ha probado en móvil real
- **Insuficiente (0-9):** No se ha conseguido generar el APK o tiene errores graves

**Mecánicas de juego (30%):**
- **Excelente (27-30):** Jugador con física perfecta, salto responsivo, valores bien ajustados
- **Bueno (21-26):** Jugador funciona pero el salto es demasiado fuerte/débil o hay pequeños problemas
- **Suficiente (15-20):** Jugador funciona pero tiene problemas evidentes de física
- **Insuficiente (0-14):** El jugador no salta o las físicas no funcionan

**Generación procedural (30%):**
- **Excelente (27-30):** Obstáculos generados infinitamente, altura aleatoria, autodestrucción funcional
- **Bueno (21-26):** Obstáculos se generan pero sin variación de altura o sin autodestrucción
- **Suficiente (15-20):** Obstáculos se generan pero hay problemas (velocidad, frecuencia)
- **Insuficiente (0-14):** Los obstáculos no se generan o el Prefab no funciona

**Lógica de juego (10%):**
- **Excelente (9-10):** Game Over funciona perfectamente con todos los obstáculos
- **Bueno (7-8):** Game Over funciona pero solo con algunos obstáculos
- **Suficiente (5-6):** Game Over implementado pero con errores
- **Insuficiente (0-4):** No hay detección de colisiones

**Documentación (10%):**
- **Excelente (9-10):** Todas las capturas presentes, legibles, reflexión profunda y bien redactada
- **Bueno (7-8):** Falta alguna captura menor o la reflexión es superficial
- **Suficiente (5-6):** Faltan varias capturas o la reflexión es muy breve
- **Insuficiente (0-4):** Documentación incompleta o de muy baja calidad

---


### Recomendaciones importantes:

**⚠️ GUARDA FRECUENTEMENTE:**
- Pulsa `Ctrl + S` después de cada cambio en scripts
- Unity guarda automáticamente la escena, pero es mejor asegurarse: **File → Save** o `Ctrl + S`

**⚠️ NO TOQUES NADA EN MODO PLAY:**
- Cualquier cambio que hagas mientras el juego está corriendo (▶️ activo) **se perderá** al parar
- Si quieres modificar algo, **para el juego primero**

**⚠️ BACKUPS:**
- Al final de cada sesión, haz una copia de la carpeta completa de tu proyecto
- Guárdala en una USB o en la nube (Google Drive, OneDrive)

**⚠️ ERRORES DE COMPILACIÓN:**
- Si Unity muestra errores rojos en la consola, **no podrás ejecutar el juego**
- Lee el error, suele indicar qué línea de código está mal
- Si no entiendes el error, consulta al profesor

**⚠️ TIEMPO DE COMPILACIÓN ANDROID:**
- La primera compilación a Android puede tardar 30 minutos
- **Empieza la exportación con tiempo suficiente**, no lo dejes para el último día

---

## 13. Recursos adicionales y ayuda

### Documentación oficial de Unity:
- **Manual de Unity:** [docs.unity3d.com/Manual](https://docs.unity3d.com/Manual/index.html)
- **Scripting API:** [docs.unity3d.com/ScriptReference](https://docs.unity3d.com/ScriptReference/index.html)
- **Tutoriales oficiales:** [learn.unity.com](https://learn.unity.com)

### Vídeos recomendados:
- **Brackeys** (YouTube): Tutoriales Unity en inglés muy bien explicados
- **GameDevBeginner** (YouTube): Guías paso a paso para principiantes
- **Unity Learn:** Cursos interactivos gratuitos dentro de Unity Hub

### Comunidad y ayuda:
- **Unity Forums:** [forum.unity.com](https://forum.unity.com)
- **Stack Overflow:** Busca errores específicos con "Unity" + tu problema

### Si te quedas atascado:

1. **Lee el mensaje de error completo** en la consola de Unity
2. **Revisa que hayas seguido cada paso** de esta guía
3. **Compara tu código** con el código de ejemplo (puede haber un typo)
4. **Busca en Google** el error exacto + "Unity"
5. **Pregunta al profesor** con capturas del error y explicación de qué intentabas hacer

---

## 14. Preguntas frecuentes (FAQ)

**P: ¿Puedo usar otro color/forma para los objetos?**
R: Sí, puedes personalizar colores, formas y tamaños como quieras. Lo importante es que las mecánicas funcionen.

**P: ¿Puedo añadir imágenes en lugar de cuadrados?**
R: Sí. Arrastra una imagen (PNG, JPG) a la carpeta Assets, y luego en el Sprite Renderer del objeto, arrastra la imagen al campo "Sprite".

**P: ¿Qué hago si mi móvil no se detecta?**
R: Revisa que la depuración USB esté activada, prueba otro cable USB, reinicia Unity y reconecta el móvil.

**P: ¿Puedo probar el juego sin móvil?**
R: Sí. En la ventana "Game" de Unity puedes jugar con el ratón. El clic equivale a tocar la pantalla.

**P: El juego va muy lento en mi móvil, ¿qué hago?**
R: Reduce el número de obstáculos que se generan (aumenta "tiempoEspera" en el Generador a 3 o 4 segundos).

**P: ¿Puedo trabajar en casa?**
R: Sí, siempre que instales Unity en tu ordenador personal (es gratis). El proyecto es portable, puedes copiarlo en USB.

**P: Unity dice "Script missing" o "None (Script)":**
R: Significa que el archivo del script ha sido renombrado o movido después de asignarlo. Vuelve a arrastrar el script al objeto.

**P: Los obstáculos no se generan:**
R: Verifica que el campo "Prefab Obstaculo" en el Inspector del Generador tenga asignado el Prefab (no debe decir "None").

**P: El jugador atraviesa el suelo:**
R: Asegúrate de que tanto el Jugador como el Suelo tengan Box Collider 2D. Si el suelo es "Is Trigger", el jugador lo atravesará físicamente (usa OnCollisionEnter2D en lugar de OnTriggerEnter2D, o mantén el suelo como collider sólido).

**P: No puedo instalar Unity porque no tengo espacio:**
R: Unity + módulos Android ocupan unos 15 GB. Libera espacio o usa un disco externo para la instalación.

**P: ¿Puedo hacer esta tarea en equipo?**
R: No, es una tarea individual. Cada alumno debe entregar su propio APK y documentación.

**P: He perdido mi proyecto, ¿qué hago?**
R: Si no tienes backup, deberás empezar de nuevo. Por eso es MUY importante hacer copias al final de cada sesión.

---

## 15. Glosario de términos Unity

Para ayudarte a entender mejor los conceptos:

- **Asset:** Cualquier archivo que uses en tu proyecto (scripts, imágenes, sonidos, etc.)
- **Build:** Proceso de compilar tu proyecto en un ejecutable o APK
- **Canvas:** Contenedor para elementos de interfaz (botones, texto, etc.)
- **Collider:** Componente que define la forma de colisión de un objeto
- **Component:** Pieza de funcionalidad que se añade a un GameObject (Transform, Rigidbody, scripts, etc.)
- **Frame:** Una imagen del juego. El juego se ejecuta a 60 frames por segundo (FPS)
- **GameObject:** Cualquier objeto en tu escena (jugador, enemigo, cámara, etc.)
- **Hierarchy:** Panel que muestra todos los GameObjects de la escena actual
- **Inspector:** Panel que muestra las propiedades del objeto seleccionado
- **Instantiate:** Crear una copia de un objeto (especialmente Prefabs) durante el juego
- **Prefab:** Plantilla reutilizable de un GameObject que puedes instanciar múltiples veces
- **Rigidbody:** Componente que añade física (gravedad, masa, velocidad) a un objeto
- **Scene:** Una pantalla o nivel de tu juego (menú, nivel 1, nivel 2, etc.)
- **Script:** Archivo de código C# que controla el comportamiento de GameObjects
- **Sprite:** Imagen 2D usada en juegos 2D
- **Transform:** Componente que define posición, rotación y escala de un objeto
- **Trigger:** Collider que detecta contacto pero no tiene colisión física sólida
- **Update:** Función que se ejecuta en cada frame del juego

---

## 16. Comparativa Unity vs Android nativo (para tu reflexión)

Para ayudarte con la reflexión del informe, aquí tienes una tabla comparativa:

| Aspecto | Android Nativo (Java) | Unity |
|---------|----------------------|-------|
| **Gravedad** | Programar manualmente: `velocidadY += gravedad;` | Añadir componente Rigidbody2D |
| **Colisiones** | Calcular distancias entre rectángulos manualmente | Unity lo detecta automáticamente con Colliders |
| **Dibujar sprites** | `canvas.drawBitmap()` en cada frame | Arrastra imagen al objeto, Unity lo dibuja |
| **Movimiento** | Actualizar X, Y manualmente en bucle | `transform.Translate()` o modificar `velocity` |
| **Físicas realistas** | Programar rebotes, inercia, fricción a mano | Rigidbody lo hace automáticamente |
| **Tiempo de desarrollo** | Días/semanas para algo simple | Horas para lo mismo |
| **Curva de aprendizaje** | Conceptos de Java, Android, UI | Interfaz de Unity, C# básico |
| **Debugging** | Logcat, breakpoints en Android Studio | Console de Unity, Visual Studio |
| **Exportar APK** | Gradle, firma manual, configuración compleja | Build Settings, un botón |
| **Multiplataforma** | Solo Android | Android, iOS, PC, consolas, Web |

**Ventajas de Unity:**
- Desarrollo mucho más rápido
- Físicas y colisiones automáticas
- Editor visual potente
- Multiplataforma sin cambiar código
- Enorme comunidad y Asset Store

**Ventajas de Android nativo:**
- Control total del rendimiento
- Mejor para apps no-juegos
- Acceso directo a APIs de Android
- APKs más ligeros para apps simples

---

## 17. Checklist final antes de entregar

Antes de subir tu tarea, verifica esta lista:

### ✓ Proyecto Unity:
- [ ] El jugador salta correctamente al hacer clic
- [ ] El jugador tiene Rigidbody 2D y Box Collider 2D
- [ ] Hay suelo y techo con Colliders
- [ ] Los obstáculos se generan infinitamente
- [ ] Los obstáculos se mueven hacia la izquierda
- [ ] Los obstáculos aparecen a alturas aleatorias
- [ ] Los obstáculos se autodestruyen al salir de pantalla
- [ ] El juego se reinicia al chocar con cualquier obstáculo
- [ ] El juego está configurado en orientación Portrait (vertical)

### ✓ APK:
- [ ] He generado el APK correctamente (sin errores)
- [ ] He instalado el APK en mi móvil
- [ ] He probado el juego en el móvil y funciona
- [ ] El archivo se llama `AstroJump.apk`

### ✓ Informe PDF:
- [ ] Portada con título, nombre, curso y fecha
- [ ] Captura del proyecto completo en Unity
- [ ] Captura del Inspector del Jugador (con todos los componentes)
- [ ] Captura del código del script ControlJugador
- [ ] Captura del Generador con el Prefab asignado
- [ ] Captura de Build Settings configurado para Android
- [ ] Foto/captura del juego ejecutándose en el móvil
- [ ] Reflexión personal completa (mínimo 200 palabras)
- [ ] El archivo se llama `AstroJump_Informe_TuNombre.pdf`

### ✓ Entrega:
- [ ] He comprimido ambos archivos en un ZIP
- [ ] El ZIP se llama `AstroJump_TuNombre.zip`
- [ ] He subido el ZIP a la plataforma Moodle
- [ ] He verificado que la subida fue exitosa

---

## Conclusión

¡Enhorabuena por completar tu primer videojuego con Unity! Has aprendido conceptos fundamentales del desarrollo de videojuegos profesional:

- **Motores de juego:** Entiendes la diferencia entre desarrollo nativo y usar un motor
- **Físicas 2D:** Sabes cómo funcionan Rigidbody y Colliders
- **Programación en C#:** Has dado tus primeros pasos en C#, el lenguaje de Unity
- **Prefabs e instanciación:** Comprendes cómo crear objetos dinámicamente
- **Pipeline de compilación:** Has exportado tu juego a Android

Unity es usado por estudios AAA (grandes) e indies (pequeños) de todo el mundo. Juegos famosos como:
- **Hollow Knight**
- **Cuphead**
- **Among Us**
- **Pokémon GO**
- **Hearthstone**

...todos están hechos en Unity.

Este es solo el comienzo. Con práctica y creatividad, puedes crear juegos increíbles. ¡Buena suerte y disfruta programando!
