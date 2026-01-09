# Tema 12. Desarrollo de videojuegos

**Tarea 1: "El corazón del juego: Implementando el Game Loop"**

## 1. Introducción

En el Tema 11 diseñaste tu juego y creaste tus propios gráficos (sprites). Ahora llega el momento de la verdad: vamos a programar el "motor" que hará que todo eso se mueva.

A diferencia de las aplicaciones Android convencionales que esperan a que el usuario toque un botón (programación orientada a eventos), un videojuego necesita redibujar la pantalla constantemente (30 o 60 veces por segundo) para crear la ilusión de movimiento. Para lograr esto, no nos sirven los `Layouts` tradicionales; necesitamos una superficie de dibujo de alto rendimiento llamada **SurfaceView** y un hilo de ejecución paralelo (**Thread**) que controle el ciclo de vida del juego.

En esta tarea guiada vamos a construir la arquitectura técnica base de cualquier juego en Android nativo.

## 2. Objetivos de aprendizaje

* Entender la diferencia entre `View` y `SurfaceView`.
* Implementar la interfaz `SurfaceHolder.Callback` para gestionar la superficie de dibujo.
* Crear un **Hilo (Thread)** dedicado para el bucle de juego (Game Loop).
* Gestionar el bloqueo y desbloqueo del `Canvas` para dibujar fotogramas.
* Integrar una vista personalizada dentro del XML de Android.

---

## 3. Requisitos previos

* Tener Android Studio abierto.
* Haber completado la lectura del Tema 12 (especialmente los puntos 1 y 2).

---

## 4. Desarrollo de la práctica

### EJERCICIO 1. Estructura del proyecto y clases

Vamos a crear la estructura básica. A diferencia de otras apps, aquí la lógica no estará en el `MainActivity`, sino en nuestras clases personalizadas.

1. Crea un nuevo proyecto en Android Studio:
* **Nombre:** `MotorJuego2D`
* **Plantilla:** Empty Views Activity.
* **Lenguaje:** Java.


2. Crea un nuevo paquete Java llamado `clases`.
3. Dentro de ese paquete, crea dos archivos (clases) vacíos:
* `Motor.java` (Será el cerebro que controla el tiempo).
* `PantallaVideojuego.java` (Será el lienzo donde dibujaremos).



### EJERCICIO 2. Creando el lienzo (PantallaVideojuego)

La clase `PantallaVideojuego` será nuestra vista personalizada. Debe heredar de `SurfaceView` para permitir dibujos rápidos.

**Código a implementar:**
Abre `PantallaVideojuego.java` y copia el siguiente código. Fíjate en que implementamos `SurfaceHolder.Callback`, lo que nos obligará a añadir tres métodos (`surfaceCreated`, `surfaceChanged`, `surfaceDestroyed`) para saber cuándo la pantalla está lista.

```java
package com.ejemplo.motorjuego2d.clases;

import android.content.Context;
import android.graphics.Canvas;
import android.graphics.Color;
import android.util.AttributeSet;
import android.view.SurfaceHolder;
import android.view.SurfaceView;
import androidx.annotation.NonNull;

public class PantallaVideojuego extends SurfaceView implements SurfaceHolder.Callback {

    private Motor motor; // Referencia a nuestro hilo de ejecución

    // Constructor necesario para instanciar desde código
    public PantallaVideojuego(Context context) {
        super(context);
        getHolder().addCallback(this); // Nos registramos para recibir eventos de la superficie
    }

    // Constructor necesario para instanciar desde XML
    public PantallaVideojuego(Context context, AttributeSet attrs) {
        super(context, attrs);
        getHolder().addCallback(this);
    }

    @Override
    public void surfaceCreated(@NonNull SurfaceHolder holder) {
        // Se ejecuta cuando la pantalla del móvil ya está lista para dibujar.
        // Aquí iniciaremos el motor (Ejercicio 4)
    }

    @Override
    public void surfaceChanged(@NonNull SurfaceHolder holder, int format, int width, int height) {
        // Se ejecuta si giramos la pantalla (no lo usaremos por ahora)
    }

    @Override
    public void surfaceDestroyed(@NonNull SurfaceHolder holder) {
        // Se ejecuta cuando cerramos la app. Debemos parar el motor para que no consuma memoria.
        boolean reintentar = true;
        motor.setRunning(false); // Avisamos al motor de que pare
        
        while (reintentar) {
            try {
                motor.join(); // Esperamos a que el hilo termine limpiamente
                reintentar = false;
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    /**
     * Método propio donde pondremos las instrucciones de dibujo
     */
    public void dibujar(Canvas canvas) {
        if (canvas != null) {
            // Pinta el fondo de color NEGRO para limpiar el frame anterior
            canvas.drawColor(Color.BLACK);
            
            // AQUÍ pintaremos más adelante nuestros sprites
        }
    }
}

```

### EJERCICIO 3. El corazón (Clase Motor)

Ahora necesitamos el "bucle infinito" que llame al método `dibujar()` una y otra vez. Esto **nunca** debe hacerse en el hilo principal de la app o la bloquearíamos. Usaremos la clase `Thread`.

**Código a implementar:**
Abre `Motor.java` e implementa la lógica del Game Loop.

```java
package com.ejemplo.motorjuego2d.clases;

import android.graphics.Canvas;
import android.view.SurfaceHolder;

public class Motor extends Thread {

    private SurfaceHolder surfaceHolder; // El "manejador" de la pantalla
    private PantallaVideojuego pantalla; // La vista donde dibujaremos
    private boolean running;             // Controla si el bucle sigue activo

    public Motor(SurfaceHolder surfaceHolder, PantallaVideojuego pantalla) {
        this.surfaceHolder = surfaceHolder;
        this.pantalla = pantalla;
        this.running = false;
    }

    public void setRunning(boolean running) {
        this.running = running;
    }

    @Override
    public void run() {
        Canvas canvas;
        
        // BUCLE PRINCIPAL DEL JUEGO
        while (running) {
            canvas = null;
            try {
                // 1. Bloqueamos el canvas para que nadie más dibuje a la vez
                canvas = surfaceHolder.lockCanvas();
                
                // 2. Ejecutamos el dibujo (si tenemos canvas)
                synchronized (surfaceHolder) {
                    if (canvas != null) {
                        pantalla.dibujar(canvas);
                    }
                }
            } finally {
                // 3. Desbloqueamos el canvas y mostramos lo dibujado en pantalla
                if (canvas != null) {
                    surfaceHolder.unlockCanvasAndPost(canvas);
                }
            }
        }
    }
}

```

### EJERCICIO 4. Conectando las piezas

Ya tenemos las dos clases, pero el `Motor` nunca se arranca. Debemos volver a `PantallaVideojuego.java` y completar el método `surfaceCreated` que dejamos vacío en el Ejercicio 2.

**Modificación:**

```java
// En PantallaVideojuego.java

@Override
public void surfaceCreated(@NonNull SurfaceHolder holder) {
    // Instanciamos el motor pasándole el manejador y esta propia pantalla
    motor = new Motor(getHolder(), this);
    
    // Arrancamos el bucle
    motor.setRunning(true);
    motor.start(); // Esto llama al método run() del Motor en segundo plano
}

```

### EJERCICIO 5. Integración en la interfaz (XML)

Por último, debemos decirle a Android que use nuestra `PantallaVideojuego` en lugar de un layout normal.

1. Abre `res/layout/activity_main.xml`.
2. Elimina el `TextView` "Hello World".
3. Cambia el diseño para incluir tu vista personalizada. **Importante:** Debes escribir la ruta completa de tu paquete.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.ejemplo.motorjuego2d.clases.PantallaVideojuego
        android:id="@+id/main"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</androidx.constraintlayout.widget.ConstraintLayout>

```

### EJERCICIO 6. Prueba de funcionamiento

Para verificar que el motor funciona, vamos a hacer que dibuje algo sencillo que pruebe que el bucle está "vivo".

1. En `PantallaVideojuego`, añade dos variables globales: `int x = 0;` e `int y = 0;`.
2. Modifica el método `dibujar()`:

```java
public void dibujar(Canvas canvas) {
    if (canvas != null) {
        canvas.drawColor(Color.BLACK); // Limpiar fondo

        // Preparar pincel rojo
        android.graphics.Paint pincel = new android.graphics.Paint();
        pincel.setColor(Color.RED);
        pincel.setTextSize(50);

        // Dibujar un círculo que se mueve solo
        canvas.drawCircle(x, 200, 50, pincel);
        
        // Dibujar texto informativo
        canvas.drawText("Motor Activo: " + x, 50, 400, pincel);
        
        // Aumentar x para que se mueva en el siguiente frame
        x = x + 5;
        if (x > getWidth()) x = 0; // Volver al inicio si sale de pantalla
    }
}

```

Ejecuta la aplicación. Deberías ver un círculo rojo moviéndose suavemente de izquierda a derecha sobre fondo negro. **¡Enhorabuena! Has creado tu primer motor gráfico.**

---

## 5. Documentación a entregar

Genera un documento PDF con las evidencias de tu trabajo:

1. **Captura de la estructura:** Una captura del explorador de proyectos de Android Studio mostrando el paquete `clases` con los archivos creados.
2. **Captura del código:** Captura legible del método `run()` dentro de la clase `Motor.java`.
3. **Evidencia de funcionamiento:** Una captura del emulador donde se vea el círculo rojo y el texto dibujados en pantalla.
4. **Reflexión técnica:**
* Explica con tus palabras: ¿Por qué usamos `lockCanvas` y `unlockCanvasAndPost`? ¿Qué pasaría si intentáramos dibujar en el canvas sin bloquearlo primero?
* ¿Por qué es necesario el método `surfaceDestroyed`? ¿Qué riesgo corremos si cerramos la app y no paramos el hilo del motor?

---

## 6. Criterios de evaluación

* **Arquitectura de clases (40%):** Se han implementado correctamente las clases `Motor` y `PantallaVideojuego` siguiendo la herencia adecuada (`Thread` y `SurfaceView`).
* **Gestión del hilo (30%):** El bucle `run()` está correctamente implementado con control de la variable `running` y gestión de excepciones.
* **Integración UI (15%):** La vista personalizada se carga correctamente desde el XML principal.

* **Funcionalidad (15%):** La aplicación compila, no se cierra inesperadamente y muestra elementos gráficos animados.
