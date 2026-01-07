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

## 5. Documentación a entregar

Genera un documento PDF con las siguientes evidencias:

1. **Fuentes de Audio:** Indica el nombre de los archivos de sonido que has usado y la URL de donde los descargaste (para verificar licencias).
2. **Código de Colisiones:** Copia el fragmento del método `actualizar` donde gestionas la intersección y la eliminación de tesoros.
3. **Captura "Jugando":** Pantalla con personaje, enemigos, tesoros y la **puntuación** dibujada en una esquina.
4. **Captura "Game Over":** Haz que tu personaje choque a propósito y captura el momento en que sale el texto de Fin de Juego.
5. **Reflexión técnica:** Al eliminar un tesoro de la lista, ¿por qué da error si usamos un bucle normal `for (Tesoro t : lista)` y hacemos `lista.remove(t)` dentro? Explica brevemente qué solución has usado tú.

---

## 6. Criterios de evaluación

* **Recursos Sonoros (10%):** Los archivos de audio están correctamente integrados en `res/raw` y son formatos válidos.
* **Detección de Colisiones (30%):** El código detecta correctamente el contacto entre sprites usando la clase `Rect`.
* **Lógica de Estado (20%):** El juego se detiene correctamente al chocar y muestra el mensaje de fin de partida.
* **Mecánica de Puntuación (20%):** Los tesoros desaparecen al ser tocados (gestión correcta de la lista) y el contador sube.
* **Implementación de Sonido (20%):** El `SoundPool` está bien configurado y los efectos suenan en el momento exacto del evento.