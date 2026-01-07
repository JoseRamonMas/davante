# Tema 11. Introducción a los videojuegos

**Tarea 1: "Pre-producción: Diseño, Motores y Creación de Assets"**

## 1. Introducción

En el desarrollo de software tradicional, primero se toman requisitos y luego se programa. En el desarrollo de videojuegos, esto es aún más crítico. Antes de escribir una sola línea de código en Android (algo que haremos en el próximo tema), necesitamos definir la "Biblia" de nuestro juego y crear los recursos gráficos que vamos a utilizar.

En esta tarea actuarás como el **Game Designer** y el **Artista 2D** de tu propio estudio indie. No vamos a programar todavía, vamos a **diseñar**. Vas a investigar qué motor te conviene más, vas a definir las mecánicas de tu juego y, lo más importante, vas a dibujar pixel a pixel tu propio personaje y sus animaciones (Sprites), preparándolos para cuando tengamos que darles vida con código.

## 2. Objetivos de aprendizaje

* Entender y elaborar un **GDD (Game Design Document)** básico.
* Investigar y comparar las diferencias entre **Motores de Juego** (Unity, Unreal, Godot) frente al desarrollo nativo.
* Comprender la diferencia técnica y artística entre gráficos 2D y 3D.
* **Crear recursos gráficos propios (Pixel Art):** Diseñar personajes y generar "Sprite Sheets" (hojas de sprites) listas para programación.

---

## 3. Desarrollo de la tarea

### EJERCICIO 1. El documento de diseño (GDD)

Todo videojuego nace de una idea. Basándote en la teoría del Tema 11 sobre la clasificación de los videojuegos (Acción, Aventura, RPG, etc.), debes definir el juego que "hipotéticamente" desarrollarías.

**Tu misión:** Crea un documento de texto breve (1 página) definiendo:

1. **Título del Juego:** (Inventa algo creativo).
2. **Género:** Justifica tu elección basándote en el PDF (p.ej. *Plataformas*, porque requiere saltos y precisión; *RPG*, porque hay evolución de personaje, etc.).
3. **Plataforma objetivo:** ¿Móvil, PC, Consola? ¿Por qué? (Ten en cuenta los controles: pantalla táctil vs mando).
4. **Mecánica Principal:** Describe en 3 líneas qué hace el jugador (saltar, disparar, resolver puzles...).
5. **Sinopsis:** Breve historia o contexto del juego.

### EJERCICIO 2. Investigación: ¿Motor o nativo?

El PDF del tema menciona la importancia de los **Motores de videojuegos** y cómo abstraen la complejidad del hardware. Como futuro desarrollador, debes saber elegir.

**Tu misión:** Realiza una investigación y crea una tabla comparativa.
Compara el desarrollo **Nativo en Android** (usando Java y Canvas, como veremos en el tema siguiente) frente a usar un motor como **Unity** o **Godot**.

La tabla debe tener estas filas:

* Curva de aprendizaje (¿Cuál es más difícil de empezar?).
* Peso de la aplicación final (¿Cuál genera APKs más pesados?).
* Potencial 2D vs 3D.
* Lenguaje de programación utilizado.

*Reflexión:* Redacta un párrafo final explicando: ¿Por qué crees que para aprender los fundamentos es mejor empezar "desde cero" (nativo) antes de saltar a un motor gigante como Unity?

### EJERCICIO 3. Taller de artista: Creación de Sprites y SpriteSheets

El PDF explica que un **Sprite** es la representación gráfica del personaje y que, para optimizar la memoria, se suelen agrupar en una sola imagen (Sprite Sheet). **En este ejercicio vas a crear tus propios gráficos.** No vale descargar imágenes de internet; deben ser creadas por ti.

**Herramientas recomendadas (Gratuitas y Online):**

* **Piskel** (piskelapp.com) - Muy recomendada.
* **Pixilart** (pixilart.com)

**Tu misión:**

1. **Diseño del personaje (Idle):**
* Dibuja un personaje principal en una cuadrícula de **64x64 píxeles**.
* Debe ser original (un robot, un animal, un guerrero, tú mismo...).
* Esta imagen será el estado "Idle" (parado).


2. **Animación de movimiento (Walk Cycle):**
* Crea 3 o 4 frames adicionales que simulen que el personaje está caminando.
* No necesitas ser un experto artista, basta con mover las piernas y los brazos en cada frame.


3. **Generación del Sprite Sheet:**
* Aquí viene la parte técnica clave para el futuro. No queremos imágenes sueltas. Queremos **una sola imagen PNG** que contenga la secuencia.
* Si usas Piskel, usa la opción de exportar como "PNG Sprite Sheet".
* Si usas otro programa, coloca las imágenes una al lado de la otra en una tira horizontal.
* *El fondo debe ser transparente.*

**Resultado esperado:** Debes obtener un archivo `.png` (tu Sprite Sheet) que se vea similar a las tiras de imágenes que aparecen en los apuntes (ej: el personaje de Kirby o el soldado).

### EJERCICIO 4. Diseño de escenario (Tiles)

Los juegos 2D suelen usar "Tiles" (baldosas) para construir el mundo.

**Tu misión:**
Diseña un bloque de suelo de **64x64 píxeles** (tierra, césped, metal...) que sea "tileable" (que si pones uno al lado del otro, no se note el corte).

---

## 4. Documentación a entregar

Debes subir a la plataforma un único documento PDF que contenga:

1. **El GDD:** Con los puntos del ejercicio 1.
2. **La investigación:** La tabla comparativa y tu reflexión sobre motores vs nativo (ejercicio 2).
3. **Evidencias de diseño (capturas obligatorias):**
    * Captura de la herramienta de dibujo (Piskel/GIMP) **mientras estabas dibujando**, para demostrar autoría. Se deben ver las capas o la cuadrícula de trabajo.
    * **La imagen final del Sprite Sheet** insertada en el documento.
    * Una breve explicación de tu personaje: ¿Por qué has elegido esos colores? ¿Qué representa?

4. **Auto-crítica:** ¿Qué dificultad has encontrado al intentar hacer que la animación del personaje parezca fluida?

**Nota:** Conserva los archivos `.png` originales que has creado, ya que **serán necesarios para realizar la práctica del tema 12**, donde programaremos el código para que este personaje que acabas de dibujar se mueva por la pantalla.