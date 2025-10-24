# Tema 7. Multimedia y bases de datos

**TAREA 1: "Reproductor interactivo de sonidos"**

## Introducción

En esta tarea vas a diseñar, programar y documentar una aplicación Android denominada **"Reproductor interactivo de sonidos"**.
Su finalidad es poner en práctica los contenidos vistos en el **Tema 7 – Multimedia y bases de datos**, específicamente la integración de sonido en aplicaciones Android, incluyendo:

* Uso de la clase **MediaPlayer** para archivos de audio largos.
* Uso de la clase **SoundPool** para efectos de sonido cortos.
* Gestión del **ciclo de vida** de recursos multimedia.
* Configuración de **recursos de audio** en el proyecto Android.
* Implementación de **controles de reproducción** (reproducir, pausar, detener).


## Descripción general del proyecto

El **Reproductor interactivo de sonidos** es una aplicación que permite al usuario reproducir diferentes tipos de audio: música de fondo mediante MediaPlayer y efectos de sonido cortos mediante SoundPool.
La aplicación contará con una interfaz con botones para controlar la reproducción, pausar, reanudar y detener el audio, además de reproducir efectos de sonido independientes.

### EJERCICIO 1. Configuración inicial y preparación de recursos

1. Crea un nuevo proyecto Android en **Android Studio** con:
    * Nombre: "ReproductorSonidos"
    * Lenguaje: Java
    * API mínima: API 21 (Android 5.0)
    * Plantilla: Empty Views Activity
2. Prepara los recursos de audio:
    * Crea la carpeta `raw` dentro de `res` (Click derecho sobre `res` → New → Android Resource Directory → Tipo: raw)
    * Descarga o prepara los siguientes archivos de audio:
        * Un archivo de música de fondo (formato .mp3, duración 30-60 segundos). Nómbralo `musica_fondo.mp3`
        * Dos efectos de sonido cortos (formato .mp3 o .wav, máximo 1 MB cada uno). Nómbralos `efecto1.mp3` y `efecto2.mp3`
    * Copia estos archivos en la carpeta `res/raw`
3. Verifica que el proyecto compila correctamente y la actividad principal se ejecuta sin errores.


### EJERCICIO 2. Diseño de la interfaz gráfica

Diseña una interfaz en el archivo `activity_main.xml` que contenga los siguientes componentes:


| Elemento | Tipo de componente | ID sugerido | Descripción |
| :-- | :-- | :-- | :-- |
| Título de la aplicación | `TextView` | `tvTitulo` | "Reproductor Interactivo de Sonidos" |
| Sección música | `TextView` | `tvSeccionMusica` | "Control de música de fondo" |
| Botón reproducir música | `Button` | `btnReproducir` | Texto: "Reproducir Música" |
| Botón pausar música | `Button` | `btnPausar` | Texto: "Pausar Música" |
| Botón detener música | `Button` | `btnDetener` | Texto: "Detener Música" |
| Sección efectos | `TextView` | `tvSeccionEfectos` | "Efectos de sonido" |
| Botón efecto 1 | `Button` | `btnEfecto1` | Texto: "Efecto 1" |
| Botón efecto 2 | `Button` | `btnEfecto2` | Texto: "Efecto 2" |
| Indicador de estado | `TextView` | `tvEstado` | Muestra el estado actual de la reproducción |

Organiza los componentes de forma clara usando `LinearLayout` o `ConstraintLayout`. Establece márgenes y padding adecuados para mejorar la apariencia visual.


***

### EJERCICIO 3. Implementación de MediaPlayer para música de fondo

Implementa la funcionalidad para reproducir música de fondo usando MediaPlayer:

1. **Declaración de variables:**
    * Declara un objeto `MediaPlayer` como variable de clase privada.
    * Declara una variable booleana para controlar el estado de pausa.
2. **Reproducir música:**
    * Al pulsar el botón "Reproducir Música":
        * Si el MediaPlayer no existe, créalo con `MediaPlayer.create(this, R.raw.musica_fondo)`
        * Inicia la reproducción con `start()`
        * Actualiza el TextView de estado con: "Música reproduciéndose"
        * Configura `setLooping(true)` para que la música se repita automáticamente
3. **Pausar música:**
    * Al pulsar el botón "Pausar Música":
        * Si el MediaPlayer está reproduciendo, llama a `pause()`
        * Actualiza el estado a: "Música pausada"
        * Si está pausado, llama a `start()` para reanudar
        * Actualiza el estado a: "Música reanudada"
4. **Detener música:**
    * Al pulsar el botón "Detener Música":
        * Si el MediaPlayer existe, llama a `stop()`
        * Libera los recursos con `release()`
        * Establece el MediaPlayer a `null`
        * Actualiza el estado a: "Música detenida"
5. **Configuración del volumen:**
    * En el método `onCreate()`, añade la línea:

```java
setVolumeControlStream(AudioManager.STREAM_MUSIC);
```

    * Esto permite que los botones físicos del dispositivo controlen el volumen de la aplicación.

💡 Asegúrate de verificar que el MediaPlayer no sea `null` antes de llamar a sus métodos.

***

### EJERCICIO 4. Implementación de SoundPool para efectos de sonido

Implementa la funcionalidad para reproducir efectos de sonido cortos usando SoundPool:

1. **Declaración de variables:**
    * Declara un objeto `SoundPool` como variable de clase privada.
    * Declara dos variables `int` para almacenar los IDs de los sonidos cargados (`idEfecto1` e `idEfecto2`).
    * Declara una variable booleana `loaded` para verificar que los sonidos se han cargado correctamente.
2. **Crear el método `crearSoundPool()`:**
    * Crea un método privado que configure el SoundPool:

```java
private void crearSoundPool() {
    AudioAttributes attributes = new AudioAttributes.Builder()
        .setUsage(AudioAttributes.USAGE_GAME)
        .setContentType(AudioAttributes.CONTENT_TYPE_SONIFICATION)
        .build();
    
    sp = new SoundPool.Builder()
        .setMaxStreams(5)
        .setAudioAttributes(attributes)
        .build();
    
    idEfecto1 = sp.load(this, R.raw.efecto1, 1);
    idEfecto2 = sp.load(this, R.raw.efecto2, 1);
    
    sp.setOnLoadCompleteListener(new SoundPool.OnLoadCompleteListener() {
        @Override
        public void onLoadComplete(SoundPool soundPool, int sampleId, int status) {
            loaded = true;
        }
    });
}
```

3. **Llamar al método en `onCreate()`:**
    * Invoca `crearSoundPool()` al final del método `onCreate()`.
4. **Reproducir efectos:**
    * Al pulsar "Efecto 1":

```java
if (loaded) {
    sp.play(idEfecto1, 1, 1, 0, 0, 1);
    tvEstado.setText("Reproduciendo Efecto 1");
}
```

    * Al pulsar "Efecto 2":

```java
if (loaded) {
    sp.play(idEfecto2, 1, 1, 0, 0, 1);
    tvEstado.setText("Reproduciendo Efecto 2");
}
```

5. **Liberar recursos en `onDestroy()`:**
    * Sobrescribe el método `onDestroy()` para liberar el SoundPool:

```java
@Override
protected void onDestroy() {
    super.onDestroy();
    if (sp != null) {
        sp.release();
        sp = null;
    }
    if (mp != null) {
        mp.release();
        mp = null;
    }
}
```

***

### EJERCICIO 5. Extensión funcional: control de volumen y lista de reproducción

Amplía tu aplicación con funcionalidades adicionales:

1. **Control de volumen con SeekBar:**
    * Añade un `SeekBar` en el XML con id `seekBarVolumen` y un `TextView` con id `tvVolumen` que muestre el porcentaje.
    * Configura el SeekBar con valores de 0 a 100.
    * Implementa un `OnSeekBarChangeListener` que ajuste el volumen del MediaPlayer:

```java
seekBarVolumen.setOnSeekBarChangeListener(new SeekBar.OnSeekBarChangeListener() {
    @Override
    public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
        float volume = progress / 100f;
        if (mp != null) {
            mp.setVolume(volume, volume);
        }
        tvVolumen.setText("Volumen: " + progress + "%");
    }
    // ... otros métodos
});
```

2. **Barra de progreso de reproducción:**
    * Añade un `SeekBar` con id `seekBarProgreso` para mostrar el progreso de la canción.
    * Usa un `Handler` y un `Runnable` para actualizar la barra cada segundo:

```java
private void actualizarBarraProgreso() {
    if (mp != null && mp.isPlaying()) {
        int posicionActual = mp.getCurrentPosition();
        int duracionTotal = mp.getDuration();
        seekBarProgreso.setMax(duracionTotal);
        seekBarProgreso.setProgress(posicionActual);
    }
    handler.postDelayed(this::actualizarBarraProgreso, 1000);
}
```

3. **Contador de reproducciones:**
    * Añade un contador que registre cuántas veces se ha reproducido cada efecto de sonido.
    * Muestra esta información en un `TextView` con formato: "Efecto 1: X veces | Efecto 2: Y veces"
4. **Botón de silencio:**
    * Añade un botón "Silenciar" que pause tanto la música como desactive los efectos de sonido.
    * Cambia el texto del botón a "Activar sonido" cuando esté silenciado.

***

## Entrega

**Formato de entrega:** archivo `.zip` con:

* Carpeta del proyecto Android completo (⚠️**Importante:** añadir tu nombre como comentario inicial en cada fichero Java).
* Archivos de audio utilizados (dentro de la carpeta `res/raw` del proyecto).

**Nombre del archivo:**
`Apellido_Nombre_PracticaTema7_Sonidos.zip`
