# Tema 10. Servicios en red III

**Práctica 1: "Mapas interactivos y despliegue profesional"**

## 1. Introducción

En la Tarea guiada aprendiste a configurar lo esencial: obtener una API Key y mostrar un mapa estático. Sin embargo, una aplicación de mapas real debe ser dinámica. El usuario espera poder interactuar con el mapa, cambiar su visualización y obtener feedback de sus acciones.

En esta práctica, vas a desarrollar una aplicación llamada **"MisLugares"**. El objetivo es crear un visor de mapas que permita cambiar el tipo de terreno (satélite, normal, híbrido) mediante una botonera y, lo más importante, **interactuar con el mapa** permitiendo añadir nuevos marcadores al tocar la pantalla.

Finalmente, simularemos un ciclo de mantenimiento real: cambiarás la versión de la aplicación y generarás un nuevo APK firmado, listo para una hipotética actualización en tienda.

## 2. Objetivos de aprendizaje

  * Consolidar la integración del SDK de Google Maps.
  * Diseñar interfaces superpuestas (Botones sobre el mapa).
  * Programar el cambio de tipo de mapa en tiempo real.
  * Gestionar eventos de entrada (`OnMapClickListener`) para añadir marcadores dinámicamente.
  * Gestionar el versionado (`versionCode`) y generar un APK firmado de actualización.

## 3. Requisitos previos

* Haber completado la **Tarea 1** (necesitarás la misma API Key, así ahorras tiempo de configuración en la consola de Google).
* Tener el emulador configurado con **Google Play Services**.

-----

## 4. Desarrollo de la práctica

### EJERCICIO 1. Estructura y diseño (UI)

1.  Crea un nuevo proyecto en Android Studio llamado **"MisLugares"**.
2.  **Gradle:** Añade la dependencia de `play-services-maps` en tu `build.gradle` (como hiciste en la Tarea 1) y sincroniza.
3.  **Manifiesto:**
    * Añade los permisos de Internet y Localización.
    * Añade el bloque `<meta-data>` con tu **API Key**.
    * *Truco:* Puedes usar la misma API Key que generaste en la Tarea 1. No hace falta crear una nueva en la consola de Google, a menos que la restringieras mucho.

**El Reto de Diseño:**
A diferencia de la tarea anterior, no queremos solo el mapa. Necesitamos una barra de controles flotante.
En tu `activity_main.xml`, el mapa debe ocupar toda la pantalla, y sobre él (en la parte superior) debe haber **3 Botones** o "Chips" con los textos: "Normal", "Satélite" e "Híbrido".

> 💡 **Pista:** Para poner elementos encima de otros, no uses `LinearLayout` como raíz. Utiliza `FrameLayout` o `ConstraintLayout`.
>
> ```xml
> <FrameLayout ...>
>     <fragment ... match_parent ... /> >     <LinearLayout ...> >         <Button android:text="Normal" ... />
>         ...
>     </LinearLayout>
> </FrameLayout>
> ```

*Pistas del procedimiento:*

1.  El mapa debe ocupar **toda la pantalla** (`match_parent`).
2.  En la parte superior (o inferior), debes colocar una fila de **3 Botones** con los textos:
    * "Normal"
    * "Satélite"
    * "Híbrido"


### EJERCICIO 2. Inicialización y marcadores fijos

En el código Java, implementa la interfaz necesaria para cargar el mapa.
Una vez el mapa esté listo (`onMapReady`), añade **3 marcadores fijos** correspondientes a tres monumentos famosos de Europa (ej: Torre Eiffel, Coliseo, Sagrada Familia).

  * Deben tener **Título** y **Snippet** (subtítulo).
  * La cámara debe iniciar centrada en uno de ellos con zoom suficiente.

### EJERCICIO 3. Control de Tipos de Mapa

Programa la lógica de los 3 botones que creaste en el Ejercicio 1. Al pulsar cada botón, el mapa debe cambiar su aspecto.

Investiga en la documentación o en el autocompletado de Android Studio los métodos del objeto `GoogleMap`.

*Pistas del procedimiento:*

1.  Vincula los botones del layout con tu código Java (`findViewById`).
2.  Añade un `OnClickListener` a cada botón.
3.  Dentro de cada listener, usa el método `setMapType` del objeto `GoogleMap`. 

> 💡 **Pista:** Busca métodos que empiecen por `setMap...`. Las constantes que necesitas son del tipo:
>
>   * `GoogleMap.MAP_TYPE_NORMAL`
>   * `GoogleMap.MAP_TYPE_SATELLITE`
>   * `GoogleMap.MAP_TYPE_HYBRID`

### EJERCICIO 4. Eventos: Añadir marcadores al tocar

Aquí viene la parte más interesante. Queremos que, si el usuario hace clic largo o corto en cualquier punto del mapa (donde no haya nada), **se cree un nuevo marcador** en esa posición exacta.

Para ello, tendrás que configurar el "Listener" adecuado en tu objeto `miMapa`.

> 💡 **Pista:** El método para escuchar clics en el mapa es `setOnMapClickListener`.
>
> ```java
> miMapa.setOnMapClickListener(new GoogleMap.OnMapClickListener() {
>     @Override
>     public void onMapClick(@NonNull LatLng latLng) {
>         // El objeto 'latLng' contiene las coordenadas donde pulsaste.
>         // 1. Crea un MarkerOptions usando ese latLng.
>         // 2. Añade el marcador al mapa con addMarker().
>     }
> });
> ```

*Pistas del procedimiento:*

1.  En el método `onMapReady`, asigna un escuchador de clics a los marcadores: `miMapa.setOnMarkerClickListener(this);`. (Tendrás que hacer que tu clase implemente `GoogleMap.OnMarkerClickListener`).
2.  En el método `onMarkerClick(Marker marker)`, implementa la siguiente lógica:
    * Muestra un **Toast** que diga: "Has seleccionado: [Título del marcador]".
    * *Nota:* Puedes obtener el título usando `marker.getTitle()`.
    * **Importante:** Devuelve `false` al final del método para que se mantenga el comportamiento por defecto (abrir la ventanita de información y centrar el mapa).

### EJERCICIO 5. Versionado y actualización

Imagina que ya lanzaste la versión 1.0. Ahora vas a lanzar la actualización con estas mejoras.

1.  Ve al archivo `build.gradle (Module: app)`.
2.  Localiza `defaultConfig`.
3.  Cambia el `versionCode` a **2** y el `versionName` a **"1.1"**.
4.  Genera el **APK Firmado**.
      * **Importante:** Debes usar el **mismo archivo `.jks` (Keystore)** que creaste en la Tarea 1. En la vida real, si pierdes esa llave, no puedes actualizar tu app en Google Play.

-----

## 5. Documentación a entregar

La evaluación se realizará **exclusivamente a través del documento PDF**. No debes entregar el código fuente. El PDF debe contener las siguientes evidencias claras:

1.  **Captura del Diseño XML:** Captura de la vista "Design" o del código XML donde se aprecie cómo has superpuesto los botones al mapa.
2.  **Capturas de Funcionalidad (Tipos de mapa):**
      * Una captura del emulador mostrando el mapa en modo **Satélite**.
      * Una captura mostrando el mapa en modo **Híbrido**.
3.  **Captura de Interacción (Marcadores dinámicos):**
      * Una captura donde se vea el mapa con varios marcadores añadidos por ti manualmente (haciendo clic en zonas vacías) además de los 3 fijos.
4.  **Captura del Código Java:** Captura legible de `MainActivity.java`, concretamente de la zona donde gestionas el `setOnMapClickListener`.
5.  **Evidencia de Versionado:** Captura del fichero `build.gradle` con la versión 1.1 y captura del explorador de archivos mostrando el nuevo `app-release.apk` generado.

-----

## 6. Criterios de evaluación

La calificación se asignará en base a las evidencias mostradas en el PDF:

  * **Integración de Mapa y UI (30%):** Se muestra el mapa correctamente con los botones flotantes bien posicionados (no tapan el logo de Google ni controles de zoom).
  * **Lógica de Tipos de Mapa (20%):** Las capturas demuestran que los botones funcionan y cambian la visualización del terreno.
  * **Interactividad y Eventos (30%):** Se evidencia que el alumno sabe capturar las coordenadas de un clic (`LatLng`) y usarlas para instanciar un nuevo marcador en tiempo real.
  * **Gestión de Despliegue (20%):** Se ha modificado correctamente el versionado en Gradle y se ha generado el APK firmado.