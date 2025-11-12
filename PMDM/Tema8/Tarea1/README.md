# Tema 8. Servicios en red I

**Tarea 1: "Catálogo de videojuegos con JSON"**

## 1. Introducción

En esta práctica vas a diseñar, programar y documentar una aplicación Android denominada **"Catálogo de videojuegos con JSON"**.
Su finalidad es poner en práctica la lectura y procesamiento de datos en formato JSON desde la carpeta `assets` de Android, incluyendo:

* Lectura de archivos JSON desde la carpeta **assets**.
* Uso de las clases **JSONArray** y **JSONObject** para parsear datos.
* Procesamiento de estructuras JSON con múltiples campos.
* Visualización de datos con **CardView** en disposición de cuadrícula (GridLayoutManager).
* Implementación de **filtros y búsqueda** sobre los datos cargados.
* **Documentación técnica y funcional** de la aplicación.

El objetivo final es que domines el manejo de datos JSON en Android, comprendiendo cómo leer archivos externos, procesarlos y presentar la información de forma visual y funcional.


## 2. Objetivos de aprendizaje

Al finalizar esta práctica, deberás ser capaz de:

* Crear y colocar archivos JSON en la carpeta assets de un proyecto Android.
* Leer archivos desde assets usando InputStream y BufferedReader.
* Parsear datos JSON con JSONArray y JSONObject.
* Extraer campos de diferentes tipos (String, int, boolean) desde objetos JSON.
* Implementar GridLayoutManager para mostrar datos en formato cuadrícula.
* Crear diseños con CardView para una presentación atractiva.
* Implementar funcionalidades de búsqueda y filtrado sobre datos JSON.
* Documentar correctamente una aplicación con lectura de datos externos.


## 3. Descripción general del proyecto

El **Catálogo de videojuegos con JSON** es una aplicación que lee información de videojuegos desde un archivo JSON almacenado en assets y los muestra en una cuadrícula visual con tarjetas.
Cada videojuego tendrá información como título, género, plataforma, año de lanzamiento, puntuación y si está disponible.

A diferencia del ejemplo de pescados visto en clase (que usa LinearLayoutManager vertical), esta aplicación usará **GridLayoutManager** para mostrar los videojuegos en formato de cuadrícula de 2 columnas, y además incluirá funcionalidades de **búsqueda por título** y **filtrado por género**.

## 4. Estructura de la tarea

La tarea se divide en **cinco ejercicios secuenciales**, más una **fase final de documentación**.
Sigue el orden indicado y guarda capturas de pantalla de cada paso.

### EJERCICIO 1. Configuración inicial y archivo JSON

1. Crea un nuevo proyecto Android en **Android Studio** con:
    * Nombre: "CatalogoVideojuegos"
    * Lenguaje: Java
    * API mínima: API 24 (Android 7.0)
    * Plantilla: Empty Views Activity
    * Build configuration language: Groovy DSL
2. **Crea la carpeta assets:**
    * En la vista de proyecto Android, click derecho sobre `app` → New → Directory → Assets Folder.
    * Acepta la ubicación `src/main/assets`
3. **Crea el archivo JSON con los videojuegos:**

Dentro de la carpeta `assets`, crea un archivo llamado `videojuegos.json` con el siguiente contenido:

```json
[
  {
    "titulo": "The Legend of Zelda: Breath of the Wild",
    "genero": "Aventura",
    "plataforma": "Nintendo Switch",
    "anio": 2017,
    "puntuacion": 97,
    "disponible": true
  },
  {
    "titulo": "God of War",
    "genero": "Acción",
    "plataforma": "PlayStation 4",
    "anio": 2018,
    "puntuacion": 94,
    "disponible": true
  },
  {
    "titulo": "Red Dead Redemption 2",
    "genero": "Aventura",
    "plataforma": "Multi",
    "anio": 2018,
    "puntuacion": 97,
    "disponible": true
  },
  {
    "titulo": "Minecraft",
    "genero": "Sandbox",
    "plataforma": "Multi",
    "anio": 2011,
    "puntuacion": 93,
    "disponible": true
  },
  {
    "titulo": "Dark Souls III",
    "genero": "RPG",
    "plataforma": "Multi",
    "anio": 2016,
    "puntuacion": 89,
    "disponible": true
  },
  {
    "titulo": "Fortnite",
    "genero": "Battle Royale",
    "plataforma": "Multi",
    "anio": 2017,
    "puntuacion": 81,
    "disponible": true
  },
  {
    "titulo": "Hollow Knight",
    "genero": "Metroidvania",
    "plataforma": "Multi",
    "anio": 2017,
    "puntuacion": 90,
    "disponible": true
  },
  {
    "titulo": "Overwatch",
    "genero": "Shooter",
    "plataforma": "Multi",
    "anio": 2016,
    "puntuacion": 91,
    "disponible": false
  },
  {
    "titulo": "Stardew Valley",
    "genero": "Simulación",
    "plataforma": "Multi",
    "anio": 2016,
    "puntuacion": 89,
    "disponible": true
  },
  {
    "titulo": "Celeste",
    "genero": "Plataformas",
    "plataforma": "Multi",
    "anio": 2018,
    "puntuacion": 92,
    "disponible": true
  },
  {
    "titulo": "Sekiro: Shadows Die Twice",
    "genero": "Acción",
    "plataforma": "Multi",
    "anio": 2019,
    "puntuacion": 90,
    "disponible": true
  },
  {
    "titulo": "Among Us",
    "genero": "Party",
    "plataforma": "Multi",
    "anio": 2018,
    "puntuacion": 72,
    "disponible": true
  }
]
```

4. **Crea la clase modelo `Videojuego.java`:**
    * Crea un paquete llamado `modelo`.
    * Crea la clase `Videojuego` con los atributos: titulo, genero, plataforma, anio, puntuacion y disponible.
    * Genera constructor completo, getters, setters, `toString()` y `equals()`.

💡 **Importante:** Fíjate en que el JSON tiene más campos que el ejemplo de pescados. Analiza bien la estructura antes de crear tu clase modelo.

**En el documento a entregar:**

* Explica qué es la carpeta assets y para qué se utiliza en Android.
* Describe (brevemente) la estructura del archivo JSON: qué representa el array principal y qué contiene cada objeto.

***

### EJERCICIO 2. Lectura y parseo del archivo JSON

Implementa la funcionalidad para leer y procesar el archivo JSON:

1. **En MainActivity, crea el método para leer el archivo desde assets:**

Utiliza el siguiente código que lee desde la carpeta assets:

```java
/**
 * Lee el contenido de un archivo desde la carpeta assets
 * @param nombreArchivo Nombre del archivo a leer
 * @return Contenido del archivo como String
 */
private String leerArchivoAssets(String nombreArchivo) {
    String contenido = "";
    try {
        InputStream is = getAssets().open(nombreArchivo);
        BufferedReader reader = new BufferedReader(new InputStreamReader(is));
        
        StringBuilder sb = new StringBuilder();
        String linea;
        while ((linea = reader.readLine()) != null) {
            sb.append(linea);
        }
        contenido = sb.toString();
        
        reader.close();
        is.close();
        
    } catch (IOException e) {
        e.printStackTrace();
    }
    return contenido;
}
```

2. **Crea el método para parsear el JSON y crear objetos Videojuego:**

En el método `onCreate()`, después de leer el archivo, implementa la lógica de parseo siguiendo el patrón del ejemplo de pescados:

* Crea un `ArrayList<Videojuego>`.
* Crea un `JSONArray` con el contenido leído.
* Recorre el JSONArray con un bucle for.
* Para cada elemento, obtén un `JSONObject` y extrae todos los campos.
* Crea objetos `Videojuego` y añádelos al ArrayList.

💡 **Referencia:** En el ejemplo de pescados usabas `getString()` para textos y `getInt()` para números enteros. Para el campo booleano `disponible`, usa `getBoolean()`.

3. **Gestiona las excepciones:**
    * Envuelve todo el código de lectura y parseo en un try-catch para `JSONException`.
    * Muestra un Toast o un log si hay errores.

**En el documento a entregar:**

* Explica qué hace el método `leerArchivoAssets()` línea por línea.
* Describe la diferencia entre `JSONArray` y `JSONObject`.
* Explica qué hace el método `getJSONObject(i)` del JSONArray.

***

### EJERCICIO 3. Diseño de interfaz con GridLayoutManager

A diferencia del ejemplo de pescados (que usa lista vertical), vamos a crear una cuadrícula de 2 columnas:

1. **Diseña `activity_main.xml`:**

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="8dp">
    
    <!-- Título de la app -->
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Catálogo de Videojuegos"
        android:textSize="24sp"
        android:textStyle="bold"
        android:gravity="center"
        android:padding="16dp"
        android:textColor="#2196F3" />
    
    <!-- RecyclerView para los videojuegos -->
    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/rvVideojuegos"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:clipToPadding="false"
        android:padding="4dp" />
    
</LinearLayout>
```

2. **Crea el diseño del item `elemento_videojuego.xml`:**

Crea un layout con CardView que muestre la información de forma atractiva:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.cardview.widget.CardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="8dp"
    app:cardCornerRadius="12dp"
    app:cardElevation="6dp">
    
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="16dp"
        android:background="#F5F5F5">
        
        <!-- Icono de videojuego (puedes usar un icono genérico) -->
        <ImageView
            android:id="@+id/ivIcono"
            android:layout_width="80dp"
            android:layout_height="80dp"
            android:layout_gravity="center"
            android:src="@drawable/ic_videogame"
            android:contentDescription="Icono videojuego" />
        
        <!-- Título del videojuego -->
        <TextView
            android:id="@+id/tvTitulo"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Título del juego"
            android:textSize="16sp"
            android:textStyle="bold"
            android:textColor="#000"
            android:gravity="center"
            android:layout_marginTop="8dp"
            android:maxLines="2"
            android:ellipsize="end" />
        
        <!-- Género -->
        <TextView
            android:id="@+id/tvGenero"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Género"
            android:textSize="12sp"
            android:textColor="#666"
            android:gravity="center"
            android:layout_marginTop="4dp" />
        
        <!-- Plataforma y Año -->
        <TextView
            android:id="@+id/tvPlataformaAnio"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Plataforma - 2020"
            android:textSize="11sp"
            android:textColor="#888"
            android:gravity="center"
            android:layout_marginTop="2dp" />
        
        <!-- Puntuación -->
        <TextView
            android:id="@+id/tvPuntuacion"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="★ 95"
            android:textSize="14sp"
            android:textStyle="bold"
            android:textColor="#FFC107"
            android:layout_gravity="center"
            android:layout_marginTop="8dp" />
        
        <!-- Estado de disponibilidad -->
        <TextView
            android:id="@+id/tvDisponibilidad"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="DISPONIBLE"
            android:textSize="10sp"
            android:textStyle="bold"
            android:textColor="#4CAF50"
            android:background="@android:color/transparent"
            android:padding="4dp"
            android:layout_gravity="center"
            android:layout_marginTop="4dp" />
        
    </LinearLayout>
    
</androidx.cardview.widget.CardView>
```

3. **Añade un icono genérico de videojuego:**
    * Descarga o crea un icono simple de mando de videojuegos.
    * Colócalo en `res/drawable/` con el nombre `ic_videogame.xml` o `ic_videogame.png`.
    * O simplemente usa un icono vectorial de Android Studio (Vector Asset).

**En el documento a entregar:**

* Describe qué hace el atributo `app:cardElevation` de CardView.
* Incluye capturas del diseño en el editor visual de Android Studio.
* Explica por qué usamos `maxLines="2"` y `ellipsize="end"` en el TextView del título.

***

### EJERCICIO 4. Implementación del adaptador con GridLayoutManager

Crea el adaptador para mostrar los videojuegos en cuadrícula:

1. **Crea el paquete `vista` y la clase `AdaptadorVideojuegos.java`:**
    * Extiende de `RecyclerView.Adapter<AdaptadorVideojuegos.VideojuegoViewHolder>`.
    * Crea la estructura básica siguiendo el patrón de `AdaptadorPescado`, pero adaptada a tu modelo Videojuego.
    * Define las variables `Context contexto` y `ArrayList<Videojuego> datos`.
    * Implementa el constructor, los métodos obligatorios del adaptador y la clase interna ViewHolder.
2. **Implementa el ViewHolder:**
    * Declara todos los TextView e ImageView necesarios (ivIcono, tvTitulo, tvGenero, tvPlataformaAnio, tvPuntuacion, tvDisponibilidad).
    * En el constructor del ViewHolder, vincula cada vista con `findViewById()`.
3. **Implementa `onBindViewHolder()`:**
    * Asigna los datos del videojuego a cada vista.
    * Para `tvPlataformaAnio`, concatena plataforma y año: `videojuego.getPlataforma() + " - " + videojuego.getAnio()`.
    * Para `tvPuntuacion`, añade la estrella: `"★ " + videojuego.getPuntuacion()`.
    * Para `tvDisponibilidad`:

```java
if (videojuego.isDisponible()) {
    holder.tvDisponibilidad.setText("DISPONIBLE");
    holder.tvDisponibilidad.setTextColor(ContextCompat.getColor(contexto, android.R.color.holo_green_dark));
} else {
    holder.tvDisponibilidad.setText("NO DISPONIBLE");
    holder.tvDisponibilidad.setTextColor(ContextCompat.getColor(contexto, android.R.color.holo_red_dark));
}
```

4. **En MainActivity, configura el RecyclerView con GridLayoutManager:**

A diferencia del ejemplo de pescados que usa `LinearLayoutManager`, usa este código:

```java
// Configurar el RecyclerView con cuadrícula de 2 columnas
rvVideojuegos.setHasFixedSize(true);
GridLayoutManager glm = new GridLayoutManager(this, 2);  // 2 columnas
rvVideojuegos.setLayoutManager(glm);

// Crear y asignar el adaptador
adaptador = new AdaptadorVideojuegos(this, listaVideojuegos);
rvVideojuegos.setAdapter(adaptador);
```


**En el documento a entregar:**

* Explica el parámetro `2` en `GridLayoutManager(this, 2)`.
* Añade capturas mostrando la aplicación funcionando con los videojuegos en cuadrícula de 2 columnas.
* Explica qué hace `ContextCompat.getColor()` y por qué se usa.

***

### EJERCICIO 5. Implementación de búsqueda y filtros

Añade funcionalidades de búsqueda y filtrado que no existían en el ejemplo de pescados:

1. **Añade componentes de búsqueda en `activity_main.xml`:**

Modifica el layout principal para incluir:

```xml
<!-- Añade esto ANTES del RecyclerView -->

<!-- Campo de búsqueda -->
<EditText
    android:id="@+id/etBusqueda"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Buscar por título..."
    android:inputType="text"
    android:padding="12dp"
    android:layout_margin="8dp"
    android:background="@android:drawable/edit_text"
    android:drawableStart="@android:drawable/ic_menu_search"
    android:drawablePadding="8dp" />

<!-- Spinner para filtrar por género -->
<Spinner
    android:id="@+id/spinnerGenero"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:padding="12dp"
    android:layout_marginStart="8dp"
    android:layout_marginEnd="8dp"
    android:layout_marginBottom="8dp" />

<!-- Botón para resetear filtros -->
<Button
    android:id="@+id/btnResetear"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Mostrar todos"
    android:layout_gravity="center"
    android:layout_marginBottom="8dp" />
```

2. **Añade métodos de utilidad al adaptador:**

Añade estos métodos a `AdaptadorVideojuegos`:

```java
// Lista completa original (para restaurar después de filtrar)
private ArrayList<Videojuego> datosOriginales;

// Modifica el constructor para guardar una copia
public AdaptadorVideojuegos(Context contexto, ArrayList<Videojuego> datos) {
    this.contexto = contexto;
    this.datos = new ArrayList<>(datos);
    this.datosOriginales = new ArrayList<>(datos);
}

/**
 * Filtra los videojuegos por título (búsqueda)
 */
public void filtrarPorTitulo(String texto) {
    datos.clear();
    if (texto.isEmpty()) {
        datos.addAll(datosOriginales);
    } else {
        String textoBusqueda = texto.toLowerCase();
        for (Videojuego v : datosOriginales) {
            if (v.getTitulo().toLowerCase().contains(textoBusqueda)) {
                datos.add(v);
            }
        }
    }
    notifyDataSetChanged();
}

/**
 * Filtra los videojuegos por género
 */
public void filtrarPorGenero(String genero) {
    datos.clear();
    if (genero.equals("Todos")) {
        datos.addAll(datosOriginales);
    } else {
        for (Videojuego v : datosOriginales) {
            if (v.getGenero().equals(genero)) {
                datos.add(v);
            }
        }
    }
    notifyDataSetChanged();
}

/**
 * Restaura la lista completa
 */
public void restaurarLista() {
    datos.clear();
    datos.addAll(datosOriginales);
    notifyDataSetChanged();
}
```

3. **En MainActivity, implementa la funcionalidad de búsqueda y filtros:**

```java
// Declarar las variables
private EditText etBusqueda;
private Spinner spinnerGenero;
private Button btnResetear;

// En onCreate(), después de configurar el RecyclerView:

// Vincular elementos
etBusqueda = findViewById(R.id.etBusqueda);
spinnerGenero = findViewById(R.id.spinnerGenero);
btnResetear = findViewById(R.id.btnResetear);

// Configurar el Spinner con los géneros
ArrayList<String> generos = obtenerGenerosUnicos(listaVideojuegos);
generos.add(0, "Todos");  // Añadir opción "Todos" al principio
ArrayAdapter<String> adapterSpinner = new ArrayAdapter<>(this, 
    android.R.layout.simple_spinner_item, generos);
adapterSpinner.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
spinnerGenero.setAdapter(adapterSpinner);

// Listener para la búsqueda en tiempo real
etBusqueda.addTextChangedListener(new TextWatcher() {
    @Override
    public void beforeTextChanged(CharSequence s, int start, int count, int after) {}
    
    @Override
    public void onTextChanged(CharSequence s, int start, int before, int count) {
        adaptador.filtrarPorTitulo(s.toString());
    }
    
    @Override
    public void afterTextChanged(Editable s) {}
});

// Listener para el Spinner de géneros
spinnerGenero.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
    @Override
    public void onItemSelected(AdapterView<?> parent, View view, int position, long id) {
        String generoSeleccionado = parent.getItemAtPosition(position).toString();        
        // Filtrar los datos manualmente en MainActivity
        ArrayList<Videojuego> datosFiltrados = new ArrayList<>();
        
        if (generoSeleccionado.equals("Todos")) {
            datosFiltrados.addAll(datosVideojuegos);
        } else {
            for (Videojuego v : datosVideojuegos) {
                if (v.getGenero().equals(generoSeleccionado)) {
                    datosFiltrados.add(v);
                }
            }
        }
           
        // Crear un NUEVO adaptador con los datos filtrados
        adaptador = new AdaptadorVideojuegos(MainActivity.this, datosFiltrados);
        rvVideojuegos.setAdapter(adaptador);
        
        // Limpiar la búsqueda
        etBusqueda.setText("");
        
        // Toast de confirmación
        Toast.makeText(MainActivity.this, 
            "Mostrando " + datosFiltrados.size() + " videojuegos de " + generoSeleccionado, 
            Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onNothingSelected(AdapterView<?> parent) {}
});

// Listener para el botón resetear
btnResetear.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        adaptador.restaurarLista();
        etBusqueda.setText("");
        spinnerGenero.setSelection(0);
        Toast.makeText(MainActivity.this, "Mostrando todos los videojuegos", 
            Toast.LENGTH_SHORT).show();
    }
});
```

4. **Añade el método auxiliar para obtener géneros únicos:**

```java
/**
 * Obtiene una lista de géneros únicos del catálogo
 */
private ArrayList<String> obtenerGenerosUnicos(ArrayList<Videojuego> lista) {
    ArrayList<String> generos = new ArrayList<>();
    for (Videojuego v : lista) {
        if (!generos.contains(v.getGenero())) {
            generos.add(v.getGenero());
        }
    }
    return generos;
}
```


💡 **Importante:** Estas funcionalidades de búsqueda y filtrado no estaban en el ejemplo de pescados. Son nuevas y te permiten demostrar comprensión avanzada del manejo de datos.

**En el documento a entregar:**

* Explica qué es un `TextWatcher` y para qué sirve cada uno de sus métodos.
* Describe cómo funciona el método `filtrarPorTitulo()` del adaptador.
* Explica por qué necesitamos mantener una copia de `datosOriginales` en el adaptador.
* Incluye capturas mostrando:
    - La búsqueda funcionando (escribe un título parcial y muestra los resultados filtrados)
    - El filtro por género funcionando
    - El botón "Mostrar todos" restaurando la lista completa
* ¿Qué ventajas tiene filtrar en el adaptador en lugar de recargar el JSON cada vez?

***

## 5. Entrega

* Carpeta del proyecto Android completo comprimida en `.zip` (⚠️**Importante:** añadir tu nombre como comentario inicial en cada fichero Java).
* Documento en formato PDF con tu informe y capturas.

***

