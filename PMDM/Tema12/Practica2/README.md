# Tema 12. Desarrollo de videojuegos

**Práctica 2: "Mecánicas de juego: Colisiones y Sonido"**

## 1. Introducción

¡Ya casi tienes un juego completo! Tienes un motor que funciona, un personaje que controlas y enemigos que se mueven. Sin embargo, ahora mismo tu juego es una "fantasía": los personajes se atraviesan como fantasmas, no hay consecuencias y, lo que es peor, el juego es mudo.

En esta última práctica vas a implementar las reglas del juego (Game Over y Puntos) usando la técnica de **Cajas de Delimitación (Bounding Boxes)**. Además, dado que en el Tema 11 nos centramos solo en los gráficos, ahora tendrás que actuar como **Ingeniero de Sonido**: buscarás efectos de audio adecuados y los programarás para que suenen en el momento exacto del impacto.

## 2. Objetivos de aprendizaje

* Buscar y adaptar recursos sonoros (FX) libres de derechos.
* Implementar detección de colisiones 2D mediante la clase `Rect`.
* Gestionar estados de juego (Jugando vs Game Over).
* Integrar efectos de sonido de baja latencia con la clase `SoundPool`.

---

## 3. Requisitos previos

* Proyecto "MotorJuego2D" con las tareas anteriores (Fondo y Enemigos) funcionando.
* Un gráfico para el objeto recolectable (ej: `moneda.png` o `tesoro.png`).

---

## 4. Desarrollo de la práctica

### EJERCICIO 1. Búsqueda de recursos sonoros (FX)

Antes de programar, necesitas los archivos de audio. Como no los generamos en el tema anterior, debes buscarlos ahora.

1. Busca en internet dos efectos de sonido cortos (menos de 2 segundos) en formato `.wav` o `.mp3`.
    * **Sonido 1 (Positivo):** Para cuando coges un tesoro (ej: *coin, ding, chime*).
    * **Sonido 2 (Negativo):** Para cuando chocas con un enemigo (ej: *crash, explosion, punch*).
    * *Fuentes recomendadas:* Freesound.org, Kenny Assets o bibliotecas de sonidos libres.

2. Crea una carpeta llamada `raw` dentro de `res` (`res/raw`) en tu proyecto Android.
3. Copia tus archivos allí. **Importante:** Renómbralos para que solo tengan minúsculas y números (ej: `sonido_punto.wav`, `sonido_choque.mp3`), o Android dará error.

### EJERCICIO 2. El objeto recolectable (Tesoro)

Necesitamos un objetivo positivo para que el jugador tenga algo que hacer además de huir.

1. Crea una clase llamada `Tesoro` (puedes basarte en la clase `Enemigo`).
2. El tesoro debe aparecer en una posición aleatoria al crearse, pero **no debe moverse** (velocidad 0).
3. En `PantallaVideojuego`, crea una lista `ArrayList<Tesoro>` y añade varios tesoros repartidos por la pantalla en el método `surfaceCreated`.
4. Dibuja los tesoros en el método `dibujar`.

### EJERCICIO 3. Sistema de colisiones (Hitbox)

Android nos facilita saber si dos imágenes se tocan usando rectángulos invisibles.

**Tu misión:**

1. Añade un método llamado `getRect()` a tus clases `Sprite` (protagonista), `Enemigo` y `Tesoro`.
2. Este método debe devolver un objeto `android.graphics.Rect` con las coordenadas actuales del objeto.
* *Pista:* El constructor de `Rect` pide `(left, top, right, bottom)`.
* En tu caso será: `(x, y, x + anchoFrame, y + altoFrame)`.



### EJERCICIO 4. Lógica de juego: Puntos y Muerte

Ahora debes modificar el bucle de juego (método `actualizar` en `PantallaVideojuego`) para comprobar las reglas en cada frame.

**Regla A: Recolectar (Intersección con Tesoros)**
Recorre la lista de tesoros. Si el `Rect` del personaje intersecta (`Rect.intersect(otroRect)`) con el de un tesoro:

1. Suma 10 puntos a una variable contador.
2. Elimina ese tesoro de la lista para que desaparezca.
* *Nota técnica:* Si intentas borrar un elemento de una lista dentro de un bucle `for-each`, Java lanzará una excepción. Investiga cómo hacerlo correctamente (usando un `Iterator` o un bucle `for` inverso).



**Regla B: Game Over (Intersección con Enemigos)**
Recorre la lista de enemigos. Si el `Rect` del personaje intersecta con el de un enemigo:

1. Cambia una variable de estado (ej: `juegoTerminado = true`).
2. Detén el motor (`motor.setRunning(false)`).
3. En el método `dibujar`, añade una condición: si `juegoTerminado` es true, dibuja un texto grande y rojo en el centro de la pantalla que diga "GAME OVER".

### EJERCICIO 5. Integración de sonido con SoundPool

Vamos a hacer que el juego suene. Usaremos `SoundPool`, que está diseñado para efectos rápidos en videojuegos (a diferencia de `MediaPlayer` que es para música).

**Tu misión:**

1. En `PantallaVideojuego`, declara e instancia un objeto `SoundPool`.
2. En el método `surfaceCreated`, carga tus dos sonidos usando `soundPool.load(...)` y guarda los IDs que te devuelve en variables enteras (ej: `idSonidoPunto`, `idSonidoChoque`).
3. **Reproducción:**
* Justo cuando detectes la colisión con el tesoro (Ejercicio 4), llama a `soundPool.play(idSonidoPunto, ...)`.
* Justo cuando detectes la colisión con el enemigo, llama a `soundPool.play(idSonidoChoque, ...)`.


---

## 5. Entrega y validación de la práctica

Para completar esta unidad, el proceso de evaluación consta de dos partes: la entrega de la memoria técnica en la plataforma y la defensa del código en el aula.

### 5.1. Documentación a entregar (Fase digital)

Genera un único documento PDF que incluya las siguientes evidencias:

1. **Fuentes de audio:** Tabla o lista indicando el nombre de los archivos de sonido utilizados y la URL exacta de origen (para verificar que son libres de derechos/Creative Commons).
2. **Código de colisiones:** Copia el fragmento del método `actualizar` donde gestionas la intersección de rectángulos y la eliminación de los tesoros de la lista.
3. **Captura "Jugando":** Una captura de pantalla con el juego en marcha donde se vean simultáneamente: el personaje, al menos un enemigo, un tesoro y la **puntuación actual** dibujada en pantalla.
4. **Captura "Game Over":** Fuerza un choque con el enemigo y captura el momento en que aparece el texto de "GAME OVER" (o "FIN") en pantalla.
5. **Reflexión técnica:** Responde razonadamente a la siguiente cuestión:
* *Al eliminar un tesoro de la lista, ¿por qué se produce un error en Java si usamos un bucle `for-each` convencional (`for (Tesoro t : lista)`) y ejecutamos `lista.remove(t)` en su interior? Explica brevemente qué solución técnica (iterador, bucle inverso, etc.) has implementado para evitarlo.*



### 5.2. Validación de autoría presencial (Fase obligatoria)

**Atención:** La entrega en la plataforma es **necesaria pero no suficiente** para aprobar.

Tras la fecha de entrega, se realizará una validación individual en clase. El profesor podrá pedirte que realices cambios en el código (ej: *"Cambia el sonido de colisión por otro"*, *"Haz que el tesoro reste puntos en vez de sumar"* o *"Modifica el área de colisión (hitbox)"*) o que expliques el funcionamiento de `SoundPool` y `Rect`.

La nota final tendrá un **techo máximo** basado en esta validación:

* 🟢 **Validación satisfactoria (Semáforo verde):**
El alumno demuestra total dominio del código entregado, explica el uso de las listas y los sonidos con claridad.
* **Consecuencia:** Se corrige la práctica sobre **10 puntos**, aplicando los criterios técnicos del apartado 6.


* 🟡 **Validación con dificultades (Semáforo amarillo):**
El alumno requiere asistencia del profesor para explicar su lógica, tiene dudas sobre cómo funciona el bucle de colisiones o le cuesta realizar modificaciones sencillas.
* **Consecuencia:** La nota máxima de la práctica será de **6,0**.


* 🔴 **Validación insuficiente (Semáforo rojo):**
El alumno no es capaz de explicar el código entregado o no sabe realizar cambios básicos. Evidencia de copia o desconocimiento de los conceptos clave (Colisiones/Sonido).
* **Consecuencia:** Se valorará únicamente el cumplimiento de la entrega documental (PDF). La nota máxima será de **4,0** (suspenso).



---

## 6. Criterios de evaluación técnica

Si la validación de autoría es satisfactoria, la nota se calculará según los siguientes porcentajes:

* **Detección de colisiones (30%):** Se utiliza correctamente la clase `Rect` y el método `intersect()` para detectar el contacto entre sprites. Las "cajas" (bounding boxes) se ajustan bien al gráfico.
* **Lógica de estado y puntuación (20%):** El juego transiciona correctamente a "Game Over" al chocar. Los tesoros desaparecen de la lista limpiamente al ser recolectados y el marcador suma puntos.
* **Implementación de sonido (20%):** El `SoundPool` está bien configurado. Los efectos suenan con baja latencia y en el momento exacto del evento (no en bucle ni con retraso).
* **Recursos sonoros (10%):** Los archivos de audio están integrados en la carpeta `res/raw`, tienen formatos válidos y son adecuados al contexto del juego.
* **Calidad del código y limpieza (20%):** El código es legible, la gestión de listas es eficiente y la estructura del proyecto es ordenada.