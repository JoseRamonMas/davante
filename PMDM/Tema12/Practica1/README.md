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

## 5. Entrega y validación de la práctica

Para dar por finalizada esta práctica, deberás cumplir dos fases: la entrega digital de la documentación y la validación presencial de la autoría.

### 5.1. Documentación a entregar (Fase digital)

Sube a la plataforma un único documento en formato PDF que contenga las siguientes evidencias:

1. **Código de la clase Sprite:** Copia y pega el código completo de tu clase `Sprite.java`. Resalta en negrita o comenta la línea exacta donde calculas el ancho de cada frame individual.
2. **Lógica de límites:** Captura de pantalla o fragmento de código (en `Sprite` o `PantallaVideojuego`) donde gestionas los condicionales (`if`) para que el personaje no salga de la pantalla.
3. **Evidencia de funcionamiento:** Una captura del emulador en ejecución donde se aprecie claramente a **tu personaje personalizado** (no el círculo de prueba ni gráficos genéricos de internet) sobre el fondo de la aplicación.
4. **Reflexión técnica:** Responde a la siguiente cuestión:
* *Has tenido que calcular `width = bitmap.getWidth() / numeroFrames`. Explica por qué es necesario este cálculo en lugar de usar el ancho total de la imagen. ¿Qué efecto visual se produciría en pantalla si usáramos el ancho total en el método `drawBitmap`?*



### 5.2. Validación de autoría presencial (Fase obligatoria)

**Atención:** La entrega en la plataforma es **necesaria pero no suficiente** para aprobar la práctica.

Tras la fecha de entrega, se realizará una breve validación individual en el aula. El profesor podrá solicitarte que realices una pequeña modificación sobre tu código en tiempo real o que respondas a preguntas concretas sobre el funcionamiento de tu algoritmo (por ejemplo: *"Haz que el personaje se mueva al doble de velocidad"* o *"Explícame qué hace esta línea en el método draw"*).

La nota final de la práctica tendrá un **techo máximo** que dependerá exclusivamente de tu desempeño en esta validación:

* 🟢 **Validación satisfactoria (Semáforo verde):**
Demuestras autoría completa y comprensión del código. Respondes con soltura y realizas las modificaciones sin problemas.
* **Consecuencia:** Se corrige la práctica sobre **10 puntos**, aplicando los criterios técnicos del apartado 6.


* 🟡 **Validación con dificultades (Semáforo amarillo):**
Necesitas ayuda significativa del profesor para explicar tu código, muestras dudas sobre la lógica que tú mismo has entregado o te cuesta realizar cambios sencillos.
* **Consecuencia:** La nota máxima a la que podrás optar, independientemente de la calidad del código, será de **6,0**.


* 🔴 **Validación insuficiente (Semáforo rojo):**
Has entregado la práctica pero no eres capaz de explicarla o modificarla. Existe evidencia de desconocimiento del funcionamiento del motor o de copia no asimilada.
* **Consecuencia:** Se valorará únicamente el cumplimiento administrativo de la entrega documental. La nota máxima será de **4,0** (suspenso).



---

## 6. Criterios de evaluación técnica

Una vez establecido el techo de nota según la validación de autoría (Semáforo verde/amarillo), se aplicarán los siguientes porcentajes para calcular la calificación numérica:

* **Clase Sprite correcta (30%):** La clase está bien encapsulada, gestiona su propio bitmap y coordenadas. El cálculo del tamaño del frame (ancho/columnas) es exacto.
* **Control y límites (30%):** La interacción táctil funciona según lo enunciado (división de pantalla izquierda/derecha) y el personaje respeta los bordes de la pantalla (rebota o se detiene, no desaparece).
* **Separación lógica/dibujo (20%):** El bucle del juego (`Motor`) invoca diferenciadamente a `actualizar()` y `dibujar()`. El movimiento se percibe fluido.
* **Integración de assets (20%):** Se visualiza correctamente el diseño propio creado en el Tema 11 ("tu personaje"). No se utilizan recursos predeterminados.