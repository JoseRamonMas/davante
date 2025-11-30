# Tema 10. Servicios en red III

**Tarea 1: "Integración de mapas y despliegue de aplicaciones"**

## 1. Introducción

En esta unidad damos un salto cualitativo. Dejamos atrás la comunicación con nuestros propios servidores (Backend propio) para empezar a consumir **servicios profesionales de terceros**.

El servicio más utilizado y demandado en el desarrollo móvil es **Google Maps**. Integrar un mapa no es solo "dibujarlo" en pantalla; implica gestionar credenciales de seguridad, configurar permisos de geolocalización y entender el ciclo de vida de los fragmentos de mapa.

Además, simularemos la fase final de cualquier desarrollo: el **firmado digital** de la aplicación (`Signed APK`), un requisito obligatorio para garantizar la autoría antes de subir cualquier app a la Google Play Store.

## 2. Objetivos de aprendizaje

Al finalizar esta práctica guiada, serás capaz de:

  * Entender y gestionar la **Google Cloud Console** para habilitar APIs específicas.
  * Generar y restringir una **API Key** para uso seguro.
  * Configurar el entorno de Android Studio (Gradle y Manifiesto) para soportar los **Google Play Services**.
  * Implementar mapas interactivos mediante `SupportMapFragment`.
  * Manipular la cámara y añadir marcadores (`Markers`) mediante código Java.
  * Generar un certificado de seguridad (Keystore) y firmar una aplicación para su distribución.

-----

## 3. Desarrollo de la práctica

### EJERCICIO 1. Gestión de credenciales en Google Cloud Console

**Cometido:**
Antes de tocar una sola línea de código, Google necesita saber quién eres y qué aplicación va a usar sus mapas. Esto se hace por seguridad y para controlar las cuotas de uso. Debemos crear un "Proyecto" en la nube y obtener una llave de acceso (API Key).

**Pasos para la resolución:**

1.  **Acceso a la consola:** Entra en [Google Cloud Console](https://console.cloud.google.com/) e inicia sesión con tu cuenta de Google/Gmail.
2.  **Crear Proyecto:**
      * En la barra superior azul, verás un desplegable (a veces dice "My First Project"). Haz clic y selecciona **"Proyecto Nuevo"**.
      * Nombre del proyecto: `PracticaMapas-TuNombre`.
      * Pulsa **Crear** y espera unos segundos a que la notificación te diga que se ha creado. **Importante:** Asegúrate de seleccionar el proyecto recién creado en el desplegable superior.
3.  **Habilitar la API:**
      * Abre el menú lateral (las tres rayitas arriba a la izquierda) y ve a **"APIs y servicios" > "Biblioteca"**.
      * En el buscador escribe: `Maps SDK for Android`.
      * Selecciona el resultado exacto con ese nombre y pulsa el botón azul **Habilitar**. (Esto "enciende" el servicio de mapas para tu proyecto).
4.  **Generar la API Key:**
      * Una vez habilitada, ve al menú lateral **"APIs y servicios" > "Credenciales"**.
      * En la parte superior, pulsa **+ CREAR CREDENCIALES** y elige **Clave de API**.
      * Se abrirá una ventana emergente con un código largo que empieza por `AIza...`.
      * **Copia ese código** y guárdalo en un bloc de notas. Esa es tu llave maestra para que el mapa funcione.

-----

### EJERCICIO 2. Configuración del proyecto Android

**Cometido:**
Un proyecto estándar de Android no trae "de serie" las librerías para mostrar mapas de Google, ya que estas no pertenecen al núcleo de Android (AOSP), sino a los **Google Play Services**. Debemos importar estas librerías y configurar los permisos necesarios.

**Pasos para la resolución:**

1.  **Crear el proyecto:** Abre Android Studio y crea un nuevo proyecto "Empty Views Activity" (o Empty Activity clásica) llamado `MisMapas`.
2.  **Añadir dependencias (Gradle):**
      * Abre el archivo `build.gradle (Module: app)`.
      * En la sección `dependencies`, añade la librería de mapas. Google actualiza esto a menudo, pero una versión estable es:
        ```groovy
        implementation 'com.google.android.gms:play-services-maps:18.2.0'
        ```
      * Pulsa el botón **"Sync Now"** (sincronizar ahora) que aparece arriba a la derecha y espera a que termine.
3.  **Configurar el Manifiesto (AndroidManifest.xml):**
    Abre este archivo y añade lo siguiente:
      * **Permisos:** Fuera de la etiqueta `<application>`, pide permiso para usar internet y la ubicación precisa:
        ```xml
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
        ```
      * **Metadatos:** Dentro de la etiqueta `<application>`, pero fuera de `<activity>`, debemos pegar nuestra API Key para que la app se autentique al iniciarse:
        ```xml
        <meta-data
            android:name="com.google.android.geo.API_KEY"
            android:value="PEGA_AQUI_TU_CLAVE_COPIADA_DEL_EJERCICIO_1"/>
        ```
        *(Sustituye el texto del valor por tu clave real que empieza por `AIza...`)*.

-----

### EJERCICIO 3. Creación de la interfaz y el fragmento de mapa

**Cometido:**
Los mapas en Android se muestran dentro de un contenedor llamado `Fragment`. Específicamente, usaremos `SupportMapFragment`. Debemos diseñar el layout para alojar este fragmento y conectar la lógica en Java para saber cuándo el mapa se ha cargado totalmente en la memoria del dispositivo.

**Pasos para la resolución:**

1.  **Diseño XML (`activity_main.xml`):**
    Sustituye el contenido por un diseño que tenga un título y el fragmento del mapa ocupando el resto de la pantalla.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Mapa de Tiendas"
            android:textSize="20sp"
            android:textStyle="bold"
            android:gravity="center"
            android:padding="16dp"
            android:background="#E0E0E0"/>

        <fragment
            android:id="@+id/mapa"
            android:name="com.google.android.gms.maps.SupportMapFragment"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />
    </LinearLayout>
    ```

2.  **Lógica Java (`MainActivity.java`):**
    Para interactuar con el mapa, nuestra actividad debe implementar una interfaz llamada `OnMapReadyCallback`. Esta interfaz tiene un método (`onMapReady`) que Android ejecutará automáticamente **solo cuando el mapa esté listo** para usarse (evitando errores por intentar pintar en un mapa que aún no existe).

    Copia y analiza este código:

    ```java
    // Importaciones necesarias (Alt+Enter si salen en rojo)
    import androidx.annotation.NonNull;
    import androidx.appcompat.app.AppCompatActivity;
    import android.os.Bundle;
    import com.google.android.gms.maps.CameraUpdateFactory;
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.OnMapReadyCallback;
    import com.google.android.gms.maps.SupportMapFragment;
    import com.google.android.gms.maps.model.LatLng;
    import com.google.android.gms.maps.model.MarkerOptions;

    // 1. Añadimos "implements OnMapReadyCallback"
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {

        private GoogleMap miMapa;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            // 2. Buscamos el fragmento en el diseño
            SupportMapFragment mapFragment = (SupportMapFragment) getSupportFragmentManager()
                    .findFragmentById(R.id.mapa);

            // 3. Solicitamos que nos avise cuando el mapa esté cargado
            if (mapFragment != null) {
                mapFragment.getMapAsync(this);
            }
        }

        // 4. Este método se ejecuta cuando el mapa ya está listo
        @Override
        public void onMapReady(@NonNull GoogleMap googleMap) {
            miMapa = googleMap;
            
            // Aquí ya podemos configurar el mapa (Ejercicio 4)
            miMapa.setMapType(GoogleMap.MAP_TYPE_NORMAL);
        }
    }
    ```

-----

### EJERCICIO 4. Añadir marcadores (markers) y cámara

**Cometido:**
Un mapa vacío no aporta valor. Vamos a aprender a situar puntos de interés (POIs) utilizando coordenadas geográficas (Latitud y Longitud) y a mover la "cámara" del mapa para enfocar una zona concreta al iniciar la app.

**Pasos para la resolución:**

1.  **Buscar coordenadas:** Entra en Google Maps desde el navegador de tu PC. Busca tu instituto o tu casa. Haz clic derecho sobre el punto y copia los números que aparecen (ej: `40.416, -3.703`). Esas son la latitud y longitud.

2.  **Añadir el código:** Dentro del método `onMapReady` que creaste en el ejercicio anterior, añade lo siguiente:

    ```java
    // A. Definir un punto geográfico (Sustituye con TUS coordenadas)
    LatLng miInstituto = new LatLng(36.7213, -4.4216); // Ejemplo: Málaga

    // B. Añadir un marcador en ese punto
    miMapa.addMarker(new MarkerOptions()
            .position(miInstituto)
            .title("Mi Instituto")
            .snippet("Aquí estudiamos desarrollo móvil"));

    // C. Mover la cámara automáticamente a ese punto con Zoom
    // El número 15 es el nivel de zoom (1=Mundo, 20=Edificio)
    miMapa.moveCamera(CameraUpdateFactory.newLatLngZoom(miInstituto, 15));
    ```

3.  **Probar:** Ejecuta la aplicación en el emulador.

      * *Nota:* Asegúrate de que tu emulador tiene los "Google Play Services" instalados (debe tener el icono de la Play Store al crearlo en el AVD Manager). Si el mapa sale en blanco, revisa que la API Key en el Manifiesto sea correcta y que la API esté habilitada en la consola.

-----

### EJERCICIO 5. Generación del APK firmado (Signed APK)

**Cometido:**
Hasta ahora has ejecutado la app en modo "Debug". Para subir una app a Google Play o compartirla profesionalmente, debe estar firmada digitalmente. Esto garantiza que nadie ha modificado tu código y certifica que tú eres el autor. Vamos a generar una "Keystore" (caja fuerte de claves) y firmar la app.

**Pasos para la resolución:**

1.  En el menú superior de Android Studio, ve a **Build > Generate Signed Bundle / APK...**
2.  Selecciona la opción **APK** y pulsa Next.
3.  En el campo "Key store path", pulsa **Create new...** (Vamos a crear tu certificado digital).
      * **Path:** Elige la carpeta de tu proyecto y dale un nombre al archivo (ej: `mi_keystore.jks`).
      * **Password:** Inventa una contraseña (ej: `123456`) y repítela.
      * **Alias:** Déjalo como `key0`.
      * **Certificate:** Rellena al menos tu nombre y apellidos y el código de país (`ES`).
      * Pulsa OK.
4.  De vuelta en la ventana anterior, asegúrate de que las contraseñas están puestas y pulsa Next.
5.  Selecciona la variante **release** (versión final) y pulsa **Create** o **Finish**.
6.  Android Studio compilará la app. Al terminar, aparecerá una notificación abajo a la derecha ("Generate Signed APK"). Pulsa en "locate" para abrir la carpeta donde está tu archivo `.apk` final.

-----

## 4. Documentación a entregar

Debes crear un documento PDF que demuestre que has realizado cada paso correctamente. Incluye:

1.  **Captura de Google Console:** Donde se vea que la API "Maps SDK for Android" está habilitada y aparece una gráfica de uso (aunque esté a cero).
2.  **Captura del mapa funcionando:** Una captura del emulador donde se vea el mapa cargado y el marcador de tu instituto visible.
3.  **Captura del detalle del marcador:** Haz clic sobre el marcador rojo en el emulador para que se despliegue el título ("Mi Instituto") y haz captura.
4.  **Captura del fichero APK:** Una captura del explorador de archivos de Windows/Mac mostrando el archivo `app-release.apk` generado en el Ejercicio 5.
5.  **Reflexión:** Responde brevemente: ¿Por qué crees que es necesario generar un certificado (Keystore) y firmar la aplicación antes de distribuirla, en lugar de pasar simplemente el archivo que usamos al probar en el emulador?

## 5. Entrega

Sube a la plataforma educativa **únicamente el archivo PDF** con las capturas y respuestas. No es necesario subir el código fuente para esta tarea.