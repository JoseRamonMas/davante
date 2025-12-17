# Tarea 2: Integración de servicios en red

**Título del proyecto:** "GeoTienda: Localizador de comercios en tiempo real"

## 1. Introducción y contexto

Hasta ahora has trabajado los componentes por separado: has creado una API en PHP (Backend), has consumido datos JSON "a mano" y con librerías, y has desplegado mapas de Google.

En el mundo profesional, estos tres elementos conviven en el 90% de las aplicaciones comerciales. En esta práctica, vas a actuar como un **Desarrollador Full Stack Mobile**. Tu cliente ficticio, una franquicia de tiendas, te pide una aplicación que descargue su lista de sucursales desde una base de datos central y las pinte en un mapa interactivo para que los usuarios puedan localizarlas.

Tendrás que reutilizar código de tus prácticas anteriores, adaptar tu servidor local y tomar decisiones técnicas sobre qué librería de conexión utilizar.

## 2. Objetivos de aprendizaje

* **Integración Full Stack:** Conectar un Backend propio (PHP/MySQL) con un Frontend Android complejo.
* **Manejo de coordenadas:** Trabajar con datos de geolocalización (`double` latitud/longitud) desde la base de datos hasta el mapa.
* **Uso de librerías de terceros:** Aplicar **Retrofit** o **Volley** (a tu elección) + **GSON** para el parseo automático.
* **Lógica de mapas:** Iterar sobre una colección de datos para generar marcadores dinámicos.

---

## 3. Fase 1: Evolución del backend (PHP y MySQL)

Para pintar tiendas en un mapa, tu base de datos actual de `productos` se queda corta. Necesitamos coordenadas.

**Tarea 1.1: Base de datos**
Accede a tu phpMyAdmin. En la base de datos que ya usas (`catalogo_productos` o similar), crea una nueva tabla llamada `tiendas`.

* Debes definir tú los campos, pero obligatoriamente debe tener: `id`, `nombre`, `latitud` (DECIMAL 10,8), `longitud` (DECIMAL 10,8) y `tipo` (ej: "Ropa", "Electrónica").
* **Reto:** Inserta manualmente (mediante SQL o interfaz) al menos **4 tiendas**.
* *Pista:* Usa Google Maps en tu navegador para sacar las coordenadas reales de 4 lugares de tu ciudad (ej: tu instituto, un centro comercial, etc.) para que luego el mapa se vea realista.


**Tarea 1.2: El Endpoint de la API**
Basándote en el código que hiciste en la **Tarea 2 del Tema 8**, crea un nuevo controlador o modifica el existente para exponer el siguiente endpoint:

* `GET /api/tiendas` -> Debe devolver el JSON con todas las tiendas.

> [!NOTE]
> Debes ser capaz de replicar la lógica de `ControladorProductos.php` para crear `ControladorTiendas.php` y exponer la ruta en tu `index.php`. Comprueba que funciona accediendo desde el navegador: `http://localhost/.../api/tiendas`.

---

## 4. Fase 2: Arquitectura de la App (Android)

Crea un nuevo proyecto en Android Studio llamado **"GeoTienda"**.

**Tarea 2.1: Configuración y Dependencias**
Configura tu `build.gradle` y tu `AndroidManifest.xml`.

* Necesitarás permisos de Internet y Ubicación.
* Necesitarás configurar el tráfico `cleartext` (http) si usas XAMPP local.
* **Decisión técnica:** Debes elegir e implementar **Retrofit** o **Volley**. Añade también **GSON** y la dependencia de **Google Maps**.

**Tarea 2.2: El Modelo de Datos**
Crea la clase `Tienda.java`.

* Esta clase debe "mapear" exactamente los campos que devuelve tu JSON.
* *Consejo:* Si usas GSON, recuerda que los nombres de las variables deben coincidir con las claves del JSON o usar la anotación `@SerializedName`.

---

## 5. Fase 3: La interfaz y el mapa

Aquí es donde integramos la UI con los datos. La aplicación tendrá una única actividad principal con un mapa que ocupe toda la pantalla (o la mitad, tú decides el diseño).

**Tarea 3.1: Preparar el mapa**
Implementa la interfaz `OnMapReadyCallback` para cargar un mapa de Google.

* Configura tu API Key de Google (reutiliza la del Tema 10).
* Centra la cámara inicialmente en tu ciudad con un zoom medio (ej: 12 o 13).

**Tarea 3.2: Consumo de la API**
En el `onCreate` de tu actividad, realiza la llamada a tu servidor (`http://10.0.2.2/...`) usando la librería elegida (Retrofit/Volley).

* **Objetivo:** Debes obtener un `ArrayList<Tienda>` o `List<Tienda>` con los datos de la base de datos.
* Muestra un `Toast` o un log que diga: "Se han descargado X tiendas".

---

## 6. Fase 4: Lógica de negocio

Esta es la parte central de la práctica. Tienes una lista de objetos Java con coordenadas y tienes un objeto `GoogleMap`. Haz que hablen entre ellos.

**Tarea 4.1: Bucle de marcadores**
Dentro del método que procesa la respuesta exitosa de la API (en el `onResponse` de Volley o el `onResponse` de Retrofit), debes:

1. Recorrer la lista de tiendas con un bucle (`for` o `foreach`).
2. Por cada tienda, extraer su latitud y longitud.
3. Crear un objeto `LatLng`.
4. Añadir un marcador al mapa (`mMap.addMarker`).
    * El **título** del marcador debe ser el nombre de la tienda.
    * El **snippet** (subtítulo) debe ser el tipo de tienda.

**Tarea 4.2: Interactividad**
Implementa un `OnInfoWindowClickListener`.

* Cuando el usuario pulse en la ventana de información del marcador, debe aparecer un `Toast` (o un `Snackbar`) que diga: "Navegando a [Nombre de la tienda]...".

---

## 7. Reto avanzado (opcional)

Si has terminado lo anterior y te sobra tiempo, implementa **una** de las siguientes mejoras:

* **Opción A (filtrado visual):** Añade botones sobre el mapa (Chips) para filtrar por tipo. Si pulso "Electrónica", borra el mapa (`map.clear()`) y vuelve a pintar solo los marcadores de ese tipo.
* **Opción B (inserción remota):** Crea un formulario flotante (Floating Action Button que abre un Dialog) para añadir una nueva tienda. Debe hacer un `POST` a tu API PHP, guardar en MySQL y, si todo va bien, añadir el marcador al mapa sin reiniciar la app.

---

## 8. Entregables y documentación

Debes generar un documento PDF que demuestre que has conectado todos los puntos. Estructura:

1. **Evidencia del backend:**
    * Captura de la tabla `tiendas` en phpMyAdmin con los datos insertados.
    * Captura del navegador mostrando el JSON que devuelve tu API.

2. **Evidencia de código (Android):**
    * Captura de tu clase modelo `Tienda.java`.
    * Captura de la parte del código donde haces el bucle `for` para añadir los marcadores (MarkerOptions).

3. **Evidencia de Funcionamiento:**
    * **Captura clave:** El emulador mostrando el mapa con los marcadores en las posiciones correctas (que coincidan con lo que metiste en la BD).
    * Captura del Toast al pulsar en una ventana de información.

4. **Reflexión técnica:**
    * ¿Qué librería elegiste (Retrofit o Volley) y por qué?
    * Explica brevemente qué problemas te encontraste al usar coordenadas (`double`) que vienen de un JSON (`String` o `Number`) y cómo lo gestionaste.

---

### Consejos

> [!TIP]
>
> * **Divide y vencerás:** No intentes pintar el mapa si aún no logras ver el JSON en el `Logcat`. Primero asegura los datos, luego la visualización.
> * **IP del emulador:** Recuerda que para el emulador Android, tu `localhost` es `10.0.2.2`.
> * **Depuración:** Si el mapa sale en blanco, revisa la API Key. Si no salen marcadores, pon puntos de ruptura (breakpoints) en el bucle `for` para ver si la lista está vacía.

