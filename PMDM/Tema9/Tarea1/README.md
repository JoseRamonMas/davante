# Tema 9. Servicios en red II

**Tarea 1: "Cliente Android Nativo para API REST"**

## 1. Introducción

En el Tema 8 desarrollaste el **Backend** (tu API REST en PHP). En este Tema 9, vamos a construir el **Frontend**: una aplicación Android que consuma esos datos.

Esta tarea es **guiada**. El objetivo es que entiendas qué ocurre "bajo el capó" cuando una app se conecta a internet. Por eso, **no usaremos librerías externas** que facilitan el trabajo ni librerías de mapeo. Lo haremos todo "a mano" utilizando las clases nativas de Java y Android.

Al terminar, habrás conectado tu propia API con tu propia App, cerrando el ciclo de desarrollo Full Stack.

## 2. Objetivos de aprendizaje

  * Configurar el Manifiesto para permitir tráfico HTTP local (`cleartext`).
  * Entender la dirección IP especial `10.0.2.2` para emuladores.
  * Implementar el patrón MVC en Android (Paquetes: `modelo`, `controlador/red`, `vista`).
  * Realizar peticiones `GET` con `HttpURLConnection`.
  * Parsear JSON manualmente usando `JSONArray` y `JSONObject`.
  * Gestionar hilos con `ExecutorService` y `Handler` para no bloquear la interfaz.

-----
## 3. Requisitos previos

Antes de empezar, asegúrate de tener tu entorno preparado:

1.  **Servidor Backend Activo:** Debes tener XAMPP (o tu servidor Apache) funcionando con la API de la **Tarea 2** desplegada.
2.  **Base de datos:** Asegúrate de que tu tabla de `productos` tiene al menos 5 o 6 registros insertados para poder visualizar la lista correctamente.
3.  **Prueba de acceso:** Verifica que puedes acceder a tu API desde el navegador de tu PC mediante: `http://localhost/productosapi/` (o la ruta que definieras).

-----

## 4. Desarrollo de la práctica

### Estructura del Proyecto

Para mantener el orden y seguir el patrón MVC, vamos a organizar nuestro código en paquetes. Crea la siguiente estructura en tu proyecto Android:

  * `com.ejemplo.tienda` (Paquete raíz / Vista - Activities)
  * `com.ejemplo.tienda.modelo` (Clases POJO)
  * `com.ejemplo.tienda.controlador` (Lógica de conexión)

### Paso 1: Configuración del Manifiesto

Android, por defecto, bloquea el tráfico que no es HTTPS (cifrado). Como estamos trabajando en local (localhost), necesitamos permitir tráfico de texto plano. Además, necesitamos permiso para salir a Internet.

**Acción:** Abre `AndroidManifest.xml` y añade lo siguiente:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" ...>

    <uses-permission android:name="android.permission.INTERNET" />

    <application
        ...
        android:usesCleartextTraffic="true"
        ...>
        
        </application>
</manifest>
```

### Paso 2: La Capa Modelo

Necesitamos una clase que represente la información que viene del JSON. Los nombres de las variables no tienen por qué coincidir con el JSON, pero es recomendable para mantener la coherencia.

**Acción:** En el paquete `modelo`, crea la clase `Producto.java`.

```java
package com.ejemplo.tienda.modelo;

public class Producto {
    private String codigo;
    private String nombre;
    private double precio;
    // Puedes añadir más campos si tu API los devuelve (ej. imagen, descripción)

    public Producto(String codigo, String nombre, double precio) {
        this.codigo = codigo;
        this.nombre = nombre;
        this.precio = precio;
    }

    public String getCodigo() { return codigo; }
    public String getNombre() { return nombre; }
    public double getPrecio() { return precio; }
}
```

### Paso 3: El Controlador de Conexión

Aquí es donde ocurre la "magia" de la red. Vamos a crear una clase encargada exclusivamente de conectar con una URL y devolver el texto (JSON) que responda el servidor. Esto separa la lógica de red de la interfaz gráfica.

Usaremos `HttpURLConnection`, la forma nativa de Java para conectarse.

**Acción:** En el paquete `controlador`, crea la clase `HttpManager.java`.

```java
package com.ejemplo.tienda.controlador;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;

public class HttpManager {

    /**
     * Método estático que recibe una URL y devuelve la respuesta del servidor en String.
     */
    public static String obtenerRespuesta(String urlString) throws IOException {
        HttpURLConnection conn = null;
        StringBuilder resultado = new StringBuilder();

        try {
            URL url = new URL(urlString);
            conn = (HttpURLConnection) url.openConnection();
            
            // Configuración básica de la conexión
            conn.setRequestMethod("GET");
            conn.setReadTimeout(10000); // 10 segundos espera lectura
            conn.setConnectTimeout(15000); // 15 segundos espera conexión
            conn.connect();

            // Verificar si la respuesta es correcta (HTTP 200)
            int responseCode = conn.getResponseCode();
            if (responseCode == HttpURLConnection.HTTP_OK) {
                
                // Leer el flujo de datos (InputStream)
                BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream()));
                String linea;
                while ((linea = reader.readLine()) != null) {
                    resultado.append(linea);
                }
                reader.close();
            } else {
                throw new IOException("Error en la conexión. Código: " + responseCode);
            }

        } finally {
            if (conn != null) {
                conn.disconnect();
            }
        }
        return resultado.toString();
    }
}
```

### Paso 4: La Vista (Interfaz Gráfica)

Necesitamos un `RecyclerView` para mostrar los datos.

**Acción:**

1.  En `activity_main.xml`, añade un `RecyclerView` que ocupe toda la pantalla.
2.  Crea un layout llamado `item_producto.xml` que defina cómo se ve cada fila (usa un `CardView` con tres `TextView`: uno para código, otro para nombre y otro para precio).

*(Se omite el código XML por brevedad, ya que es similar a prácticas anteriores, pero recuerda asignar IDs a los TextViews).*

### Paso 5: El Adaptador

Al igual que en temas anteriores, necesitamos un adaptador para vincular los datos (`ArrayList<Producto>`) con la vista.

**Acción:** Crea `ProductoAdapter.java` (puede ir en el paquete raíz o en un subpaquete `vista`).

```java
package com.ejemplo.tienda;

import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.TextView;
import androidx.annotation.NonNull;
import androidx.recyclerview.widget.RecyclerView;
import com.ejemplo.tienda.modelo.Producto;
import java.util.ArrayList;

public class ProductoAdapter extends RecyclerView.Adapter<ProductoAdapter.ViewHolder> {

    private ArrayList<Producto> listaProductos;

    public ProductoAdapter(ArrayList<Producto> listaProductos) {
        this.listaProductos = listaProductos;
    }

    @NonNull
    @Override
    public ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext())
                .inflate(R.layout.item_producto, parent, false);
        return new ViewHolder(view);
    }

    @Override
    public void onBindViewHolder(@NonNull ViewHolder holder, int position) {
        Producto p = listaProductos.get(position);
        holder.txtNombre.setText(p.getNombre());
        holder.txtCodigo.setText("Ref: " + p.getCodigo());
        holder.txtPrecio.setText(String.format("%.2f €", p.getPrecio()));
    }

    @Override
    public int getItemCount() {
        return listaProductos.size();
    }

    public static class ViewHolder extends RecyclerView.ViewHolder {
        TextView txtNombre, txtCodigo, txtPrecio;

        public ViewHolder(@NonNull View itemView) {
            super(itemView);
            // Asegúrate que estos IDs coinciden con tu item_producto.xml
            txtNombre = itemView.findViewById(R.id.txtNombre);
            txtCodigo = itemView.findViewById(R.id.txtCodigo);
            txtPrecio = itemView.findViewById(R.id.txtPrecio);
        }
    }
}
```

### Paso 6: Conectando todo en el MainActivity

Aquí es donde realizaremos la llamada asíncrona.
Recordatorio importante: **No podemos hacer peticiones de red en el Hilo Principal (UI Thread)**.

Para solucionarlo de forma sencilla y estándar en Java, usaremos:

1.  **ExecutorService:** Para crear un hilo de fondo y descargar el JSON.
2.  **Handler (MainLooper):** Para volver al hilo principal y actualizar el RecyclerView.
3.  **Parsing Manual:** Usaremos `JSONArray` para recorrer la respuesta.

**Acción:** Implementa el siguiente código en tu `MainActivity.java`.

```java
package com.ejemplo.tienda;

import android.os.Bundle;
import android.os.Handler;
import android.os.Looper;
import android.util.Log;
import android.widget.Toast;
import androidx.appcompat.app.AppCompatActivity;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;

import com.ejemplo.tienda.controlador.HttpManager;
import com.ejemplo.tienda.modelo.Producto;

import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;

import java.util.ArrayList;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class MainActivity extends AppCompatActivity {

    // Añade puerto si no estás usando el 80
    // Asegúrate de que la ruta /productosapi/ es correcta en tu XAMPP
    private static final String URL_API = "http://10.0.2.2/productosapi/";

    private RecyclerView recyclerView;
    private ProductoAdapter adapter;
    private ArrayList<Producto> listaProductos;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Configuración inicial del RecyclerView
        recyclerView = findViewById(R.id.recyclerView);
        recyclerView.setLayoutManager(new LinearLayoutManager(this));
        listaProductos = new ArrayList<>();
        adapter = new ProductoAdapter(listaProductos);
        recyclerView.setAdapter(adapter);

        // Iniciamos la carga de datos
        cargarDatosDesdeApi();
    }

    private void cargarDatosDesdeApi() {
        // 1. Executor para ejecutar la tarea de red en segundo plano (Background)
        ExecutorService executor = Executors.newSingleThreadExecutor();
        
        // 2. Handler para poder pintar en la interfaz de usuario (UI Thread)
        Handler handler = new Handler(Looper.getMainLooper());

        executor.execute(() -> {
            String respuestaJson = null;
            try {
                // LLAMADA AL CONTROLADOR (Operación lenta)
                respuestaJson = HttpManager.obtenerRespuesta(URL_API);
            } catch (Exception e) {
                e.printStackTrace();
            }

            // Guardamos el resultado final para usarlo dentro del handler
            final String jsonFinal = respuestaJson;

            // 3. Volvemos al hilo principal para procesar y pintar
            handler.post(() -> {
                if (jsonFinal != null) {
                    procesarJson(jsonFinal);
                } else {
                    Toast.makeText(MainActivity.this, 
                        "Error al conectar con el servidor", Toast.LENGTH_SHORT).show();
                }
            });
        });
    }

    /**
     * Método para parsear manualmente el JSON y rellenar la lista
     */
    private void procesarJson(String jsonString) {
        try {
            // Asumimos que la API devuelve un Array JSON directamente [...]
            // Si tu API devuelve un objeto { "estado":..., "datos": [...] } ajusta esto.
            JSONArray jsonArray = new JSONArray(jsonString);

            for (int i = 0; i < jsonArray.length(); i++) {
                JSONObject obj = jsonArray.getJSONObject(i);

                // ACCESO MANUAL A LOS DATOS
                // Las claves ("codigo", "nombre", etc) deben coincidir con tu JSON/PHP
                String codigo = obj.getString("codigo");
                String nombre = obj.getString("nombre");
                // Cuidado con los tipos: getDouble, getInt, etc.
                double precio = obj.getDouble("precio"); 

                // Crear objeto modelo y añadir a la lista
                Producto prod = new Producto(codigo, nombre, precio);
                listaProductos.add(prod);
            }

            // Notificar al adaptador que hay datos nuevos
            adapter.notifyDataSetChanged();

        } catch (JSONException e) {
            e.printStackTrace();
            Toast.makeText(this, "Error al procesar los datos JSON", Toast.LENGTH_SHORT).show();
        }
    }
}
```

-----

## 5. Documentación a entregar

Crea un documento PDF con el siguiente contenido:

1.  **Explicación de la IP:** Explica con tus palabras por qué usamos `10.0.2.2` y qué pasaría si pusiéramos `localhost` en el código Android.
2.  **Capturas de funcionamiento:**
      * Captura del navegador de tu PC mostrando el JSON de tu API (`http://localhost/...`).
      * Captura del emulador Android mostrando esos mismos datos en el RecyclerView.
3.  **Reflexión MVC:** Explica brevemente qué código has puesto en cada paquete (`modelo`, `controlador`, `vista`) y por qué es útil separarlo así.

-----

## 6. Entrega

Sube a la plataforma:

  * El **PDF** con la documentación.

  * El **proyecto Android** comprimido en ZIP (Puedes hacer *Build > Clean Project* antes de comprimir para reducir el peso).
