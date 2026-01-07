# Tema 12. Desarrollo de videojuegos

**Práctica 1: "Dando vida a tu personaje"**

## 1. Introducción

En la Tarea 1 construiste el "chasis" de tu coche (el Motor y la Pantalla). Ahora toca ponerle el "piloto". En esta práctica, vas a recuperar el **Sprite Sheet** (hoja de sprites) que diseñaste en el Tema 11 y vas a programar la lógica necesaria para que aparezca en pantalla, se anime y se mueva.

Esta práctica es menos guiada. Tienes el código de referencia en los anexos del tema (`EjemploVideojuego2D` y `Sprite`), pero deberás adaptarlo para que funcione con **tus** gráficos y **tus** dimensiones.

## 2. Objetivos de aprendizaje

* Crear una clase `Sprite` que gestione su propia posición y gráfico.
* Entender la técnica de "recorte" de un Sprite Sheet para crear animación.
* Modificar el bucle del juego (`Motor`) para separar la lógica (`actualizar`) del dibujo (`dibujar`).
* Implementar control táctil básico y detección de límites de pantalla.

---

## 3. Requisitos previos

* Tener el proyecto "MotorJuego2D" funcionando (resultado de la Tarea 1).
* Tener el archivo `.png` de tu personaje (creado en el Tema 11) con fondo transparente.

---

## 4. Desarrollo de la práctica

### EJERCICIO 1. Preparación de recursos

1. Copia tu archivo `personaje_spritesheet.png` (o como lo llamaras) dentro de la carpeta `res/drawable` de tu proyecto Android.
2. Asegúrate de saber **cuántos frames** (dibujos) tiene tu hoja y cuál es el ancho total de la imagen.
* *Ejemplo:* Si dibujaste 4 posturas de 64px cada una, tu imagen medirá 256px de ancho. Este dato es vital para la programación.



### EJERCICIO 2. La clase Sprite

Basándote en la teoría y los ejemplos del tema, crea una nueva clase Java llamada `Sprite`. Esta clase no hereda de nada, es una clase POJO (*Plain Old Java Object*) que representa a tu personaje.

**Requisitos de implementación:**
La clase debe tener, al menos, los siguientes atributos y métodos. Debes deducir el código necesario o consultarlo en el anexo `EjemploVideojuego2D.txt` y adaptarlo:

* **Atributos:**
* `x`, `y`: Coordenadas enteras o flotantes para la posición.
* `velocidadX`, `velocidadY`: Velocidad de movimiento.
* `bmp`: El objeto `Bitmap` que contiene la imagen completa.
* `width`, `height`: El ancho y alto **de un solo frame** (no de la imagen completa).


* **Constructor:**
* Debe recibir el `Bitmap` y calcular el ancho de cada frame (dividiendo el ancho del bitmap entre el número de columnas/sprites que dibujaste).


* **Método `actualizar()`:**
* Debe sumar la velocidad a la posición `x` e `y`.
* **Reto de límites:** Añade lógica `if` para que, si el personaje toca el borde derecho (`x > pantallaAncho`) o izquierdo, rebote (invierta su velocidad) o se detenga. *Pista: Necesitarás pasar el ancho de la pantalla a este método o al constructor.*


* **Método `dibujar(Canvas canvas)`:**
* Debe usar `canvas.drawBitmap`.
* **Importante:** Aquí está la clave de la animación. No debes pintar todo el bitmap. Debes definir un rectángulo de origen (`src`) que coja solo el trozo del sprite actual, y un rectángulo de destino (`dst`) donde se pintará en pantalla.



### EJERCICIO 3. Evolución del motor (actualizar vs dibujar)

En la Tarea 1, nuestro motor solo llamaba a `pantalla.dibujar()`. Un videojuego real tiene dos fases: primero calcula las nuevas posiciones (lógica) y luego pinta (renderizado).

1. Modifica tu clase `Motor.java`:
* Dentro del bucle `while(running)`, justo antes de bloquear el canvas, añade una llamada a un nuevo método: `pantalla.actualizar()`.
* El bloqueo del canvas (`lockCanvas`) debe seguir protegiendo solo al método `dibujar`. La actualización lógica puede ir fuera o dentro, pero idealmente se separa.


2. Modifica tu clase `PantallaVideojuego.java`:
* Instancia tu objeto `Sprite` en el método `surfaceCreated`. Carga el bitmap usando `BitmapFactory.decodeResource`.
* Crea el método `actualizar()`: Este método debe llamar a `personaje.actualizar()`.
* Modifica el método `dibujar()`: Elimina el círculo rojo de la Tarea 1 e invoca a `personaje.dibujar(canvas)`.



### EJERCICIO 4. Interactividad táctil

Vamos a hacer que el personaje reaccione al jugador. En lugar de usar botones como en el PDF, usaremos la pantalla táctil directamente para aprovechar la clase `SurfaceView`.

1. En `PantallaVideojuego`, sobrescribe el método `onTouchEvent`.
2. Implementa la siguiente lógica:
* Si el usuario toca la mitad **derecha** de la pantalla, la velocidad X del sprite debe ser positiva (moverse a la derecha).
* Si el usuario toca la mitad **izquierda**, la velocidad X debe ser negativa (moverse a la izquierda).
* Si levanta el dedo (`ACTION_UP`), el personaje se detiene (velocidad 0).



---

## 5. Documentación a entregar

Genera un documento PDF con las siguientes evidencias:

1. **Código de la clase Sprite:** Copia y pega el código de tu clase `Sprite.java`. Resalta o comenta dónde calculas el ancho de cada frame.
2. **Evidencia de límites:** Captura del código (en `Sprite` o `PantallaVideojuego`) donde gestionas que el personaje no se salga de la pantalla (los `if` de control de bordes).
3. **Captura de funcionamiento:** Una captura del emulador donde se vea a **tu personaje personalizado** (no el círculo rojo ni gráficos de internet) dibujado sobre el fondo negro.
4. **Reflexión:**
* Has tenido que calcular `width = bitmap.getWidth() / numeroFrames`. Explica por qué hacemos esto en lugar de usar el ancho total de la imagen. ¿Qué pasaría en pantalla si usáramos el ancho total en el método `drawBitmap`?



---

## 6. Criterios de evaluación

* **Clase Sprite correcta (30%):** La clase gestiona su propio bitmap y coordenadas. Se calcula correctamente el tamaño del frame individual.
* **Separación Lógica/Dibujo (20%):** El Motor llama a `actualizar` y `dibujar` por separado. El personaje se mueve fluidamente.
* **Integración de Assets (20%):** Se visualiza el diseño propio creado en el Tema 11. No se usan recursos por defecto.
* **Control y Límites (30%):** El personaje responde al tacto (izquierda/derecha) y no desaparece de la pantalla al llegar a los bordes (rebota o para).