# Tema 12. Desarrollo de videojuegos

**Tarea 2: "Escenario y amenazas: Construyendo el mundo"**

## 1. Introducción

En la Tarea 1 creaste el motor (el corazón) y en la Práctica 1 le diste un protagonista controlado por el jugador. Pero un juego no es divertido si el personaje flota en el vacío sin nada que hacer.

En esta segunda tarea guiada vamos a convertir ese fondo negro en un **escenario real** utilizando la técnica de *Tiling* (mosaico), que permite cubrir pantallas de cualquier tamaño usando una imagen pequeña repetida. Además, introduciremos la tensión al juego añadiendo **Enemigos** que se moverán de forma autónoma por la pantalla.

Aprenderás a gestionar múltiples objetos a la vez (listas de enemigos) y a dibujar por capas (fondo detrás, personajes delante).

## 2. Objetivos de aprendizaje

* Implementar fondos texturizados usando `BitmapDrawable` y `TileMode`.
* Entender el orden de pintado (Z-Order) en el objeto `Canvas`.
* Crear una clase `Enemigo` con inteligencia artificial básica (movimiento automático y rebote).
* Gestionar colecciones de objetos (`ArrayList`) dentro del bucle de juego.

---

## 3. Requisitos previos

* Tener el proyecto "MotorJuego2D" con la Tarea 1 y la Práctica 1 terminadas.
* Tener preparados los archivos gráficos creados en el Tema 11:
* `suelo_tile.png` (Tu baldosa de suelo).
* `enemigo_spritesheet.png` (Tu hoja de sprites del enemigo).



---

## 4. Desarrollo de la práctica

### EJERCICIO 1. El fondo infinito (Tiling)

Los juegos 2D no suelen cargar una imagen gigante de 1920x1080 para el fondo porque consumiría mucha memoria. En su lugar, usan una imagen pequeña (ej: 64x64) y la repiten como si fueran azulejos. Android nos facilita esto con la clase `BitmapDrawable`.

**Paso 1: Preparar el recurso**
Copia tu imagen `suelo_tile.png` a la carpeta `res/drawable` del proyecto.

**Paso 2: Código del fondo**
Abre tu clase `PantallaVideojuego.java`. Vamos a añadir la lógica para cargar y configurar el fondo.

1. Añade una variable global para el fondo:
```java
private android.graphics.drawable.BitmapDrawable fondo;

```


2. En el método `surfaceCreated`, justo antes de iniciar el motor, añade este bloque de código. Fíjate cómo activamos el modo `REPEAT`:
```java
// 1. Cargar la imagen del suelo
android.graphics.Bitmap bitmapFondo = android.graphics.BitmapFactory.decodeResource(getResources(), R.drawable.suelo_tile);

// 2. Crear el Drawable a partir del Bitmap
fondo = new android.graphics.drawable.BitmapDrawable(getResources(), bitmapFondo);

// 3. Configurar para que se repita en X e Y (efecto mosaico)
fondo.setTileModeX(android.graphics.Shader.TileMode.REPEAT);
fondo.setTileModeY(android.graphics.Shader.TileMode.REPEAT);

```


3. Modifica el método `dibujar(Canvas canvas)`. Ahora, en lugar de pintar de negro (`Color.BLACK`), pintaremos el fondo texturizado.
**Importante:** El fondo debe pintarse lo *primero* de todo, para que quede detrás del personaje.
```java
public void dibujar(Canvas canvas) {
    if (canvas != null) {
        // A. Definir que el fondo ocupa TODA la pantalla actual
        fondo.setBounds(0, 0, canvas.getWidth(), canvas.getHeight());

        // B. Pintar el fondo
        fondo.draw(canvas);

        // C. Pintar el personaje (código que ya tenías de la Práctica 1)
        personaje.dibujar(canvas);
    }
}

```



*Ejecuta la app. Deberías ver cómo tu pequeña baldosa de suelo cubre ahora toda la pantalla del móvil.*

### EJERCICIO 2. La clase Enemigo

Los enemigos son similares al protagonista (tienen imagen, posición y velocidad), pero no los controla el usuario, sino el código. Vamos a crear una clase específica para ellos.

**Paso 1: Crear la clase**
Crea una nueva clase Java llamada `Enemigo.java`. Puedes copiar gran parte del código de tu clase `Sprite` (o hacer que herede de ella si te sientes cómodo con la herencia), pero con cambios clave en el movimiento.

**Paso 2: Implementación**
Asegúrate de que tu clase `Enemigo` tenga esta estructura lógica:

```java
package com.ejemplo.motorjuego2d.clases;

import android.graphics.Bitmap;
import android.graphics.Canvas;
import android.graphics.Rect;
import java.util.Random;

public class Enemigo {
    // Atributos de posición y velocidad
    public int x, y;
    public int velocidadX, velocidadY;
    
    // Atributos gráficos
    private Bitmap bitmap;
    private int anchoFrame, altoFrame; // Dimensiones de un solo frame del enemigo

    // Constructor: Recibe el bitmap y la pantalla para colocarse aleatoriamente
    public Enemigo(Bitmap bitmap, int anchoPantalla, int altoPantalla) {
        this.bitmap = bitmap;
        // Asumimos que el sprite del enemigo tiene, por ejemplo, 3 columnas. 
        // Ajusta este número a TU dibujo real.
        this.anchoFrame = bitmap.getWidth() / 3; 
        this.altoFrame = bitmap.getHeight();

        Random generador = new Random();
        
        // Posición inicial aleatoria dentro de la pantalla
        this.x = generador.nextInt(anchoPantalla - anchoFrame);
        this.y = generador.nextInt(altoPantalla - altoFrame);

        // Velocidad aleatoria (entre 5 y 15 para que sea interesante)
        this.velocidadX = 10 + generador.nextInt(10); 
        this.velocidadY = 10 + generador.nextInt(10);
        
        // Decidir dirección inicial aleatoria (positiva o negativa)
        if (generador.nextBoolean()) velocidadX *= -1;
        if (generador.nextBoolean()) velocidadY *= -1;
    }

    public void actualizar(int anchoPantalla, int altoPantalla) {
        // Moverse
        x += velocidadX;
        y += velocidadY;

        // Rebote en los bordes (Inteligencia Artificial básica)
        // Si toca la derecha o la izquierda, invierte velocidad X
        if (x >= (anchoPantalla - anchoFrame) || x <= 0) {
            velocidadX *= -1;
        }
        
        // Si toca arriba o abajo, invierte velocidad Y
        if (y >= (altoPantalla - altoFrame) || y <= 0) {
            velocidadY *= -1;
        }
    }

    public void dibujar(Canvas canvas) {
        // Recorte del primer frame (puedes animarlo más adelante si quieres)
        Rect origen = new Rect(0, 0, anchoFrame, altoFrame);
        Rect destino = new Rect(x, y, x + anchoFrame, y + altoFrame);
        
        canvas.drawBitmap(bitmap, origen, destino, null);
    }
}

```

### EJERCICIO 3. Generando el ejército (Listas)

Un juego suele tener más de un enemigo. Para gestionarlos, no crearemos variables sueltas (`enemigo1`, `enemigo2`...), sino una lista (`ArrayList`).

**Paso 1: Preparar PantallaVideojuego**
Vuelve a `PantallaVideojuego.java`.

1. Importa `java.util.ArrayList`.
2. Declara la lista como variable global:
```java
private java.util.ArrayList<Enemigo> listaEnemigos;

```


3. Copia tu imagen `enemigo_spritesheet.png` a `res/drawable`.

**Paso 2: Instanciar enemigos**
En el método `surfaceCreated`, inicializa la lista y crea, por ejemplo, 5 enemigos.

```java
// Dentro de surfaceCreated...

// Inicializar lista
listaEnemigos = new java.util.ArrayList<>();

// Cargar bitmap del enemigo
android.graphics.Bitmap bmpEnemigo = android.graphics.BitmapFactory.decodeResource(getResources(), R.drawable.enemigo_spritesheet);

// Crear bucle para generar 5 enemigos
for (int i = 0; i < 5; i++) {
    Enemigo e = new Enemigo(bmpEnemigo, getWidth(), getHeight());
    listaEnemigos.add(e);
}

```

**Paso 3: Actualizar y Dibujar el ejército**
Finalmente, debemos asegurarnos de que el motor mueva y pinte a *todos* los enemigos en cada frame.

1. En el método `actualizar()` de `PantallaVideojuego`:
```java
public void actualizar() {
    // Actualizar al protagonista (código previo)
    personaje.actualizar();

    // Actualizar a CADA enemigo de la lista
    for (Enemigo e : listaEnemigos) {
        e.actualizar(getWidth(), getHeight());
    }
}

```


2. En el método `dibujar(Canvas canvas)`:
```java
public void dibujar(Canvas canvas) {
    if (canvas != null) {
        // 1. Fondo
        fondo.setBounds(0, 0, canvas.getWidth(), canvas.getHeight());
        fondo.draw(canvas);

        // 2. Enemigos (Pintarlos antes que el personaje para que si se cruzan, 
        // el protagonista aparezca por encima)
        for (Enemigo e : listaEnemigos) {
            e.dibujar(canvas);
        }

        // 3. Personaje principal
        personaje.dibujar(canvas);
    }
}

```



---

## 5. Documentación a entregar

Genera un documento PDF con las siguientes evidencias:

1. **Código del Enemigo:** Copia y pega tu clase `Enemigo.java` completa. Resalta la parte donde calculas la velocidad aleatoria.
2. **Captura del "SurfaceCreated":** Muestra el fragmento de código en `PantallaVideojuego` donde configuras el fondo (`TileMode`) y el bucle `for` donde creas los enemigos.
3. **Captura de funcionamiento:** Una captura del emulador donde se vea:
* El fondo texturizado cubriendo toda la pantalla.
* Múltiples enemigos distribuidos por la pantalla.
* Tu personaje principal.


4. **Reflexión técnica:**
* ¿Por qué hemos pintado a los enemigos *antes* que al personaje principal dentro del método `dibujar`? ¿Qué pasaría visualmente si lo hiciéramos al revés?
* ¿Qué ventaja tiene usar un `ArrayList` para los enemigos en lugar de crear 5 variables `Enemigo e1, e2, e3...`?

---

## 6. Criterios de evaluación

* **Implementación del Fondo (25%):** El fondo se ve correctamente repetido (mosaico) y cubre toda la pantalla sin deformarse.
* **Clase Enemigo (25%):** La clase está bien estructurada y encapsula su propia lógica de movimiento y rebote.
* **Gestión de Listas (25%):** Se utiliza correctamente `ArrayList` para gestionar un número arbitrario de enemigos.
* **Bucle de Juego Completo (25%):** El juego funciona fluido, actualizando y dibujando tanto al protagonista como a todos los enemigos simultáneamente.