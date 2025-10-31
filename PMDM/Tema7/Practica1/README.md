# Tema 7. Multimedia y bases de datos

**PRÁCTICA 1: "Agenda de contactos con SQLite"**

## 1. Introducción

En esta práctica vas a diseñar, programar y documentar una aplicación Android denominada **"Agenda de contactos con SQLite"**.
Su finalidad es poner en práctica los contenidos vistos en el **Tema 7 – Multimedia y bases de datos**, específicamente el trabajo con bases de datos locales SQLite, incluyendo:

* Creación de bases de datos **SQLite** embebidas en Android.
* Uso de la clase **SQLiteOpenHelper** para gestionar la base de datos.
* Implementación de las operaciones **CRUD** (Crear, Leer, Actualizar, Eliminar).
* Uso de **ContentValues** para insertar y actualizar datos.
* Consulta de datos mediante **Cursor** y el método `query()`.
* Visualización de datos con **RecyclerView** y adaptadores personalizados.
* **Documentación técnica y funcional** de la aplicación.

El objetivo final es que domines la persistencia de datos en aplicaciones Android, comprendiendo cómo crear y gestionar bases de datos locales, y cómo realizar las operaciones básicas de manera eficiente.

Además de desarrollar el código, tendrás que **documentar tu proceso**, describiendo con tus propias palabras cómo has trabajado, los problemas encontrados y las decisiones que has tomado.

## 2. Objetivos de aprendizaje

Al finalizar esta práctica, deberás ser capaz de:

* Crear y gestionar bases de datos SQLite en Android.
* Heredar de SQLiteOpenHelper e implementar los métodos `onCreate()` y `onUpgrade()`.
* Insertar, actualizar, eliminar y consultar datos en tablas SQLite.
* Utilizar ContentValues para preparar datos a insertar o actualizar.
* Trabajar con el objeto Cursor para recorrer resultados de consultas.
* Implementar RecyclerView con adaptadores personalizados para mostrar datos.
* Gestionar correctamente el ciclo de vida de la base de datos.
* Documentar correctamente una aplicación con persistencia de datos.


## 3. Descripción general del proyecto

La **Agenda de contactos con SQLite** es una aplicación que permite al usuario gestionar una lista de contactos almacenados localmente en el dispositivo.
La aplicación contará con funcionalidades para añadir nuevos contactos con información detallada (nombre, teléfono, email, ciudad), visualizar la lista completa de contactos, actualizar información existente y eliminar contactos de la agenda.

Los datos se almacenarán en una base de datos SQLite embebida que persistirá incluso cuando se cierre la aplicación, proporcionando una experiencia completa de gestión de datos locales.

## 4. Estructura de la práctica

La práctica se divide en **cinco ejercicios secuenciales**, más una **fase final de documentación**.
Sigue el orden indicado y guarda capturas de pantalla de cada paso.

### EJERCICIO 1. Configuración inicial y modelo de datos

1. Crea un nuevo proyecto Android en **Android Studio** con:
    * Nombre: "AgendaContactos"
    * Lenguaje: Java
    * Build Configuration: Groovy DSL
    * Plantilla: Empty Views Activity
2. **Crea la clase de modelo `Contacto.java`:**
    * Crea un nuevo paquete llamado `model` dentro del paquete principal.
    * Dentro de este paquete, crea la clase `Contacto` con los siguientes atributos privados:
        * `int id` (código identificador)
        * `String nombre`
        * `String telefono`
        * `String email`
        * `String ciudad`
    * Genera el constructor con todos los parámetros.
    * Genera los getters y setters para todos los atributos.
    * Sobrescribe los métodos `toString()` y `equals()`.
3. Verifica que el proyecto compila correctamente.

**En el documento a entregar:**

* Explica con tus palabras qué es un modelo de datos y por qué es importante separarlo en una clase independiente.

***

### EJERCICIO 2. Creación de la clase de gestión de base de datos

Implementa la clase que gestionará toda la lógica de la base de datos SQLite:

1. **Crea la clase `BDContactos.java` dentro del paquete `model`:**
    * Esta clase debe extender de `SQLiteOpenHelper`.
    * Declara las siguientes constantes:

```java
public static final int DATABASE_VERSION = 1;
public static final String DATABASE_NAME = "DBContactos.db";
public static final String TABLA_CONTACTOS = "Contactos";
```

2. **Define las sentencias SQL:**
    * Crea una constante `String SQLCREATE` con la sentencia para crear la tabla:

```sql
CREATE TABLE Contactos (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    nombre TEXT NOT NULL,
    telefono TEXT,
    email TEXT,
    ciudad TEXT
)
```

    * Crea una constante `String SQLDROP` para eliminar la tabla:

```sql
DROP TABLE IF EXISTS Contactos
```

3. **Implementa el constructor:**

```java
public BDContactos(Context contexto) {
    super(contexto, DATABASE_NAME, null, DATABASE_VERSION);
}
```

4. **Sobrescribe los métodos obligatorios:**
    * `onCreate(SQLiteDatabase db)`: Ejecuta la sentencia `SQLCREATE`.
    * `onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)`: Elimina y recrea la tabla.
5. **Crea un método para cerrar la base de datos:**

```java
public void cerrarBD() {
    SQLiteDatabase bd = getWritableDatabase();
    if (bd != null && bd.isOpen()) {
        bd.close();
    }
}
```


**En el documento a entregar:**

* Explica qué es SQLiteOpenHelper y por qué se usa en Android.
* Describe la diferencia entre los métodos `onCreate()` y `onUpgrade()`.
* Explica qué significa que la base de datos sea "embebida" y qué ocurre si desinstalas la aplicación.

***

### EJERCICIO 3. Implementación de operaciones de inserción y consulta

Amplía la clase `BDContactos` con métodos para insertar y consultar contactos:

1. **Método para insertar contactos:**

```java
public void insertarContacto(String nombre, String telefono, String email, String ciudad) throws Exception {
    SQLiteDatabase bd = getWritableDatabase();
    try {
        ContentValues values = new ContentValues();
        values.put("nombre", nombre);
        values.put("telefono", telefono);
        values.put("email", email);
        values.put("ciudad", ciudad);
        
        long resultado = bd.insert(TABLA_CONTACTOS, null, values);
        if (resultado == -1) {
            throw new Exception("Error al insertar el contacto");
        }
    } catch (Exception e) {
        throw new Exception(e.toString());
    } finally {
        bd.close();
    }
}
```

2. **Método para obtener todos los contactos:**

```java
public ArrayList<Contacto> obtenerTodosContactos() {
    ArrayList<Contacto> contactos = new ArrayList<>();
    SQLiteDatabase bd = getReadableDatabase();
    
    try {
        String sortOrder = "nombre ASC";
        Cursor c = bd.query(
            TABLA_CONTACTOS,    // Tabla
            null,               // Columnas (null = todas)
            null,               // WHERE
            null,               // Argumentos del WHERE
            null,               // GROUP BY
            null,               // HAVING
            sortOrder           // ORDER BY
        );
        
        if (c.moveToFirst()) {
            do {
                int id = c.getInt(0);
                String nombre = c.getString(1);
                String telefono = c.getString(2);
                String email = c.getString(3);
                String ciudad = c.getString(4);
                
                Contacto contacto = new Contacto(id, nombre, telefono, email, ciudad);
                contactos.add(contacto);
            } while (c.moveToNext());
        }
        c.close();
    } catch (Exception e) {
        throw new RuntimeException(e);
    } finally {
        bd.close();
    }
    return contactos;
}
```

3. **Crea un diseño básico en `activity_main.xml`:**
    * Añade `EditText` para nombre, teléfono, email y ciudad.
    * Añade un `Button` con id `btnInsertar` y texto "Añadir Contacto".
    * Añade un `Button` con id `btnConsultar` y texto "Mostrar Contactos".
    * Añade un `RecyclerView` con id `rvContactos` para listar los contactos.
4. **Implementa la funcionalidad básica en `MainActivity.java`:**
    * Declara las variables para los componentes y la base de datos.
    * En `onCreate()`, inicializa la base de datos y vincula los elementos.
    * Implementa el listener del botón insertar para recoger datos y llamar a `insertarContacto()`.
    * Muestra un `Toast` confirmando la inserción correcta.

**En el documento a entregar:**

* Explica qué es ContentValues y por qué se utiliza en lugar de SQL directo.
* Describe qué hace el objeto Cursor y cómo se recorren los resultados.
* Añade capturas mostrando la inserción exitosa de al menos 3 contactos diferentes.

***

### EJERCICIO 4. Visualización de datos con RecyclerView

Implementa la visualización de contactos usando RecyclerView y un adaptador personalizado:

1. **Crea el layout del item `item_contacto.xml`:**
    * Diseña un layout con `LinearLayout` vertical que contenga:
        * `TextView` con id `tvId` para mostrar el ID
        * `TextView` con id `tvNombre` para el nombre (texto grande y en negrita)
        * `TextView` con id `tvTelefono` para el teléfono
        * `TextView` con id `tvEmail` para el email
        * `TextView` con id `tvCiudad` para la ciudad
    * Usa márgenes y padding para que se vea ordenado.
2. **Añade la dependencia de RecyclerView en `build.gradle`:**

```gradle
implementation 'androidx.recyclerview:recyclerview:1.3.2'
```

3. **Crea la clase `AdaptadorContactos.java` en el paquete `model`:**

```java
public class AdaptadorContactos extends RecyclerView.Adapter<AdaptadorContactos.ContactoViewHolder> {
    
    private Context context;
    private ArrayList<Contacto> listaContactos;
    
    public AdaptadorContactos(Context context, ArrayList<Contacto> listaContactos) {
        this.context = context;
        this.listaContactos = listaContactos;
    }
    
    @NonNull
    @Override
    public ContactoViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext())
            .inflate(R.layout.item_contacto, parent, false);
        return new ContactoViewHolder(view);
    }
    
    @Override
    public void onBindViewHolder(@NonNull ContactoViewHolder holder, int position) {
        Contacto contacto = listaContactos.get(position);
        holder.tvId.setText(String.valueOf(contacto.getId()));
        holder.tvNombre.setText(contacto.getNombre());
        holder.tvTelefono.setText("Tel: " + contacto.getTelefono());
        holder.tvEmail.setText("Email: " + contacto.getEmail());
        holder.tvCiudad.setText("Ciudad: " + contacto.getCiudad());
    }
    
    @Override
    public int getItemCount() {
        return listaContactos.size();
    }
    
    public void refrescar() {
        notifyDataSetChanged();
    }
    
    public static class ContactoViewHolder extends RecyclerView.ViewHolder {
        TextView tvId, tvNombre, tvTelefono, tvEmail, tvCiudad;
        
        public ContactoViewHolder(@NonNull View itemView) {
            super(itemView);
            tvId = itemView.findViewById(R.id.tvId);
            tvNombre = itemView.findViewById(R.id.tvNombre);
            tvTelefono = itemView.findViewById(R.id.tvTelefono);
            tvEmail = itemView.findViewById(R.id.tvEmail);
            tvCiudad = itemView.findViewById(R.id.tvCiudad);
        }
    }
}
```

4. **Implementa el botón consultar en MainActivity:**

```java
if (v.getId() == R.id.btnConsultar) {
    rvContactos.setHasFixedSize(true);
    LinearLayoutManager llm = new LinearLayoutManager(this);
    rvContactos.setLayoutManager(llm);
    
    try {
        ArrayList<Contacto> contactosTotales = bd.obtenerTodosContactos();
        AdaptadorContactos adaptador = new AdaptadorContactos(this, contactosTotales);
        rvContactos.setAdapter(adaptador);
        adaptador.refrescar();
        Toast.makeText(this, "Contactos cargados: " + contactosTotales.size(), 
            Toast.LENGTH_SHORT).show();
    } catch (Exception e) {
        Toast.makeText(this, "Error: " + e.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```


**En el documento a entregar:**

* Explica qué es un RecyclerView y por qué se prefiere sobre ListView.
* Describe el patrón ViewHolder y su función en el rendimiento de la lista.
* Añade capturas mostrando el RecyclerView con al menos 3 contactos visualizados.
* Explica qué hace el método `notifyDataSetChanged()` del adaptador.

***

### EJERCICIO 5. Implementación de actualización y eliminación

Completa las operaciones CRUD añadiendo funcionalidades de actualización y eliminación:

1. **Añade el método de actualización en `BDContactos.java`:**

```java
public void actualizarContacto(int id, String nombre, String telefono, String email, String ciudad) throws Exception {
    SQLiteDatabase bd = getWritableDatabase();
    try {
        ContentValues values = new ContentValues();
        values.put("nombre", nombre);
        values.put("telefono", telefono);
        values.put("email", email);
        values.put("ciudad", ciudad);
        
        String selection = "id = ?";
        String[] updateArgs = {String.valueOf(id)};
        
        int filasAfectadas = bd.update(TABLA_CONTACTOS, values, selection, updateArgs);
        if (filasAfectadas == 0) {
            throw new Exception("No se encontró el contacto a actualizar");
        }
    } catch (Exception e) {
        throw new Exception(e.toString());
    } finally {
        bd.close();
    }
}
```

2. **Añade el método de eliminación:**

```java
public void eliminarContacto(int id) throws Exception {
    SQLiteDatabase bd = getWritableDatabase();
    try {
        String selection = "id = ?";
        String[] deleteArgs = {String.valueOf(id)};
        
        int filasAfectadas = bd.delete(TABLA_CONTACTOS, selection, deleteArgs);
        if (filasAfectadas == 0) {
            throw new Exception("No se encontró el contacto a eliminar");
        }
    } catch (Exception e) {
        throw new Exception(e.toString());
    } finally {
        bd.close();
    }
}
```

3. **Añade un método de búsqueda específica:**

```java
public Contacto buscarContactoPorId(int id) {
    SQLiteDatabase bd = getReadableDatabase();
    Contacto contacto = null;
    
    try {
        String selection = "id = ?";
        String[] selectionArgs = {String.valueOf(id)};
        
        Cursor c = bd.query(TABLA_CONTACTOS, null, selection, selectionArgs, 
            null, null, null);
        
        if (c.moveToFirst()) {
            contacto = new Contacto(
                c.getInt(0),
                c.getString(1),
                c.getString(2),
                c.getString(3),
                c.getString(4)
            );
        }
        c.close();
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        bd.close();
    }
    return contacto;
}
```

4. **Amplía la interfaz en `activity_main.xml`:**
    * Añade un `EditText` con id `etIdBuscar` para buscar por ID.
    * Añade un `Button` con id `btnBuscar` y texto "Buscar Contacto".
    * Añade un `Button` con id `btnActualizar` y texto "Actualizar Contacto".
    * Añade un `Button` con id `btnEliminar` y texto "Eliminar Contacto".
5. **Implementa las nuevas funcionalidades en MainActivity:**
    * Buscar: busca un contacto por ID y muestra sus datos en los campos de texto.
    * Actualizar: actualiza el contacto cuyo ID esté en `etIdBuscar` con los datos de los campos.
    * Eliminar: elimina el contacto con el ID especificado en `etIdBuscar`.
    * Después de cada operación, actualiza el RecyclerView automáticamente.
6. **Añade validaciones:**
    * Verifica que los campos no estén vacíos antes de insertar o actualizar.
    * Verifica que el ID sea válido antes de buscar, actualizar o eliminar.
    * Muestra mensajes de error con Toast cuando las validaciones fallen.

**En el documento a entregar:**

* Explica la diferencia entre los métodos `insert()`, `update()` y `delete()` de SQLiteDatabase.
* Describe qué son los argumentos de selección y por qué son importantes para la seguridad.
* Incluye capturas mostrando todas las operaciones CRUD funcionando correctamente:
    * Inserción de un nuevo contacto
    * Búsqueda de un contacto existente
    * Actualización de los datos de un contacto
    * Eliminación de un contacto
    * RecyclerView actualizado después de cada operación


***

### FASE FINAL: Documentación del proyecto

Crea un informe con los apartados que has ido redactando en cada ejercicio y una reflexión final.

***

## 5. Entrega

**Formato de entrega:** archivo `.zip` con:

* Carpeta del proyecto Android completo (⚠️**Importante:** añadir tu nombre como comentario inicial en cada fichero Java).
* Documento en formato PDF.

**Nombre del archivo:**
`Apellido_Nombre_PracticaTema7_BaseDatos.zip`

***

## 6. Criterios de evaluaciónS

| Criterio | Descripción | Puntos |
| :-- | :-- | :-- |
| Funcionalidad general | La aplicación realiza correctamente todas las operaciones CRUD. | 3 |
| Implementación correcta de SQLiteOpenHelper | Se crea y gestiona la base de datos correctamente con herencia adecuada. | 1.5 |
| Operaciones de base de datos | Se implementan correctamente insert, update, delete y query con validaciones. | 1.5 |
| Implementación de RecyclerView | Se visualizan los datos correctamente con adaptador personalizado. | 1.5 |
| Documentación propia y redacción | Se explica el proceso, con claridad y en lenguaje personal. | 1.5 |
| Estilo, diseño y gestión de recursos | Interfaz cuidada, código limpio y cierre correcto de base de datos. | 1 |


***

## 7. Recomendaciones

* Comenta todo el código con explicaciones breves y claras.
* Usa nombres de variables descriptivos en español o inglés, pero de forma consistente.
* Guarda capturas frecuentes durante tu progreso.
* Prueba todas las operaciones CRUD exhaustivamente antes de entregar.
* No copies código sin entenderlo.
* Recuerda siempre cerrar la base de datos después de cada operación para liberar recursos.
* Valida los datos de entrada antes de realizar operaciones en la base de datos.
* Si la aplicación se cierra inesperadamente, revisa el Logcat de Android Studio para identificar errores.
