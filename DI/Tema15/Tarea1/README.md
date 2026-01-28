# Tema 15. Nuevos paradigmas - Interfaces declarativas

**Tarea 1: "Despegando con Jetpack Compose: App TechEvents".**

## 1. Introducción

Como hemos visto en el Tema 15, el desarrollo de interfaces en Android ha sufrido su mayor revolución en 10 años. Hemos pasado del modelo imperativo (XML + `findViewById`) al modelo declarativo (**Jetpack Compose**), donde la interfaz es una función directa del estado de los datos.

En esta tarea guiada vas a experimentar la transición práctica. Olvídate de los Layouts XML, los Adapters y los ViewHolders. Vamos a construir una aplicación moderna completa, escrita 100% en Kotlin, aplicando los conceptos de **Atomic Design**, **Composables**, **LazyColumn** y gestión de **Estado**.

Construiremos **"TechEvents"**, una aplicación para listar y gestionar conferencias tecnológicas, donde aplicaremos estilos, listas infinitas e interactividad.

## 2. Objetivos de aprendizaje

Al finalizar esta práctica guiada, deberás ser capaz de:

* Configurar un proyecto **Android Studio** preparado para Jetpack Compose.
* Crear funciones **@Composable** y previsualizarlas con `@Preview`.
* Entender y aplicar la jerarquía de layouts (**Column, Row, Box**).
* Sustituir el antiguo `RecyclerView` por el componente moderno **LazyColumn**.
* Gestionar el **Estado (State)** y la recomposición para hacer la app interactiva.
* Implementar patrones de diseño **Material 3** (Scaffold, TopAppBar, FAB).

---

## 3. Desarrollo de la práctica

### EJERCICIO 1. Configuración del proyecto y "Hola Compose"

**Cometido:**
Crear el entorno de trabajo y entender la estructura básica de una Activity basada en Compose, eliminando cualquier rastro de XML.

**Pasos:**

1. Abre **Android Studio** y selecciona **New Project**.
2. **IMPORTANTE:** Selecciona la plantilla **"Empty Activity"** (asegúrate de que el icono tiene el logo de Compose, o que la descripción menciona Jetpack Compose). *No selecciones "Empty Views Activity"*.
3. Configuración:
    * **Name:** `Tema15_TechEvents`
    * **Package name:** `com.tu_nombre.techevents`
    * **Language:** Kotlin
    * **Build configuration language:** Kotlin DSL (Recommended)

4. Espera a que Gradle sincronice (puede tardar unos minutos).

**Limpieza inicial:**
Abre `MainActivity.kt`. Verás que Android genera código de ejemplo. Bórralo (excepto el paquete y los imports) y déjalo así para empezar limpio:

```kotlin
package com.tu_nombre.techevents // Tu paquete aquí

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.padding
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Surface
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
import androidx.compose.ui.tooling.preview.Preview

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MaterialTheme {
                Surface(modifier = Modifier.fillMaxSize()) {
                    // Aquí llamaremos a nuestra pantalla principal
                    EventListScreen()
                }
            }
        }
    }
}

// Nuestro primer Composable
@Composable
fun EventListScreen() {
    Text(
        text = "Bienvenido a TechEvents", 
        modifier = Modifier.padding(16.dp)
    )
}

@Preview(showBackground = true)
@Composable
fun EventListScreenPreview() {
    MaterialTheme {
        EventListScreen()
    }
}

```

> **Nota:** Fíjate que ya no hay `setContentView(R.layout.activity_main)`. Ahora usamos `setContent { }` y llamamos a funciones de Kotlin.
> **Prueba:** Pulsa en "Split" (arriba a la derecha) para ver la *Preview* sin necesidad de ejecutar el emulador.

---

### EJERCICIO 2. Diseño atómico: creando la "Tarjeta de Evento"

**Cometido:**
En lugar de diseñar una pantalla gigante, en Compose diseñamos componentes pequeños y reutilizables. Vamos a crear la **"Molécula"** principal de nuestra app: la tarjeta que representa un evento.

**Pasos:**

1. Crea un nuevo archivo Kotlin llamado `EventCard.kt` (Clic derecho en el paquete > New > Kotlin Class/File > File).
2. Primero, necesitamos un modelo de datos. Añade esto al principio del archivo:

    ```kotlin
    package com.tu_nombre.techevents

    import androidx.compose.material.icons.Icons
    import androidx.compose.material.icons.filled.DateRange
    import androidx.compose.material.icons.filled.Place
    import androidx.compose.ui.graphics.vector.ImageVector

    // Modelo de datos simple
    data class Evento(
        val nombre: String,
        val ubicacion: String,
        val fecha: String,
        val imagen: ImageVector = Icons.Default.DateRange // Icono por defecto
    )

    ```

3. Ahora, diseña la tarjeta visualmente. Copia el siguiente código en `EventCard.kt`. Fíjate en el uso de `Card`, `Row` (horizontal) y `Column` (vertical).

    ```kotlin
    import androidx.compose.foundation.Image
    import androidx.compose.foundation.layout.*
    import androidx.compose.foundation.shape.RoundedCornerShape
    import androidx.compose.material3.*
    import androidx.compose.runtime.Composable
    import androidx.compose.ui.Alignment
    import androidx.compose.ui.Modifier
    import androidx.compose.ui.draw.clip
    import androidx.compose.ui.graphics.Color
    import androidx.compose.ui.layout.ContentScale
    import androidx.compose.ui.text.font.FontWeight
    import androidx.compose.ui.tooling.preview.Preview
    import androidx.compose.ui.unit.dp

    @Composable
    fun EventCard(evento: Evento) {
        Card(
            modifier = Modifier
                .fillMaxWidth()
                .padding(8.dp),
            elevation = CardDefaults.cardElevation(4.dp),
            colors = CardDefaults.cardColors(containerColor = Color(0xFFF0F4F8)) // Un gris azulado suave
        ) {
            Row(
                modifier = Modifier.padding(16.dp),
                verticalAlignment = Alignment.CenterVertically
            ) {
                // 1. Imagen / Icono a la izquierda
                Surface(
                    modifier = Modifier.size(50.dp),
                    shape = RoundedCornerShape(25.dp), // Círculo
                    color = MaterialTheme.colorScheme.primary
                ) {
                    Icon(
                        imageVector = evento.imagen,
                        contentDescription = "Icono evento",
                        tint = Color.White,
                        modifier = Modifier.padding(12.dp)
                    )
                }

                Spacer(modifier = Modifier.width(16.dp))

                // 2. Columna de textos (Nombre y Lugar)
                Column(modifier = Modifier.weight(1f)) {
                    Text(
                        text = evento.nombre,
                        style = MaterialTheme.typography.titleMedium,
                        fontWeight = FontWeight.Bold
                    )
                    Spacer(modifier = Modifier.height(4.dp))
                    Row(verticalAlignment = Alignment.CenterVertically) {
                        Icon(
                            imageVector = Icons.Default.Place,
                            contentDescription = null,
                            modifier = Modifier.size(16.dp),
                            tint = Color.Gray
                        )
                        Text(
                            text = evento.ubicacion,
                            style = MaterialTheme.typography.bodySmall,
                            color = Color.Gray
                        )
                    }
                }
                
                // 3. Fecha a la derecha
                Text(
                    text = evento.fecha,
                    style = MaterialTheme.typography.labelMedium,
                    color = MaterialTheme.colorScheme.primary
                )
            }
        }
    }

    // Previsualización para ver cómo queda sin compilar
    @Preview
    @Composable
    fun EventCardPreview() {
        EventCard(
            evento = Evento("Droidcon Madrid", "Cines Callao", "18 Jun")
        )
    }

    ```

> **Analiza el código:** Hemos traducido la estructura mental "Caja horizontal con icono, textos y fecha" a `Row { Icon, Column { Text, Text }, Text }`.

---

### EJERCICIO 3. Listas eficientes (LazyColumn)

**Cometido:**
Olvídate del `RecyclerView` y sus Adapters. Vamos a mostrar una lista de 50 eventos con apenas unas líneas de código.

**Pasos:**

1. Vuelve a `MainActivity.kt`.
2. Vamos a simular datos. Añade esta función auxiliar fuera de la clase:

    ```kotlin
    fun generarEventosFicticios(): List<Evento> {
        return listOf(
            Evento("Google I/O", "San Francisco", "14 May"),
            Evento("Mobile World Congress", "Barcelona", "28 Feb"),
            Evento("KotlinConf", "Amsterdam", "12 Apr"),
            Evento("Apple WWDC", "California", "06 Jun"),
            Evento("Tech Summit", "Madrid", "20 Sep"),
            Evento("DevFest", "Sevilla", "15 Oct"),
            Evento("Android Makers", "Paris", "25 Apr"),
            Evento("React Native EU", "Wroclaw", "01 Sep")
        ) + List(20) { Evento("Evento Local $it", "Online", "TBA") } // Añadimos 20 genéricos
    }

    ```

3. Modifica el composable `EventListScreen` para usar `LazyColumn`.

    ```kotlin
    import androidx.compose.foundation.lazy.LazyColumn
    import androidx.compose.foundation.lazy.items // Importante importar este

    @Composable
    fun EventListScreen() {
        // Obtenemos los datos
        val listaEventos = generarEventosFicticios()

        // LazyColumn = El nuevo RecyclerView
        LazyColumn(
            contentPadding = PaddingValues(16.dp),
            modifier = Modifier.fillMaxSize()
        ) {
            // Cabecera de la lista (opcional, como un Header en Adapter)
            item {
                Text(
                    text = "Próximos Eventos",
                    style = MaterialTheme.typography.headlineMedium,
                    modifier = Modifier.padding(bottom = 16.dp)
                )
            }

            // Elementos de la lista
            items(listaEventos) { evento ->
                EventCard(evento = evento)
            }
        }
    }

    ```

> **Ejecuta la app:** Ahora verás una lista con scroll perfectamente fluida. Compara esto con crear un Adapter, un ViewHolder y el XML del item. El ahorro de código es del 70%.

---

### EJERCICIO 4. Interactividad y estado

**Cometido:**
Una UI estática es aburrida. Vamos a añadir un botón de "Favorito" (Corazón) a cada tarjeta. Esto requiere gestionar **Estado**: cuando el usuario pulsa, la UI debe "repintarse" para mostrar el corazón lleno.

**Pasos:**

1. Ve a `EventCard.kt`.
2. Modifica la función `EventCard` para incluir interactividad. Necesitamos añadir un icono de corazón al final y una variable de estado.

```kotlin
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.filled.FavoriteBorder
import androidx.compose.runtime.* // Necesario para remember y mutableStateOf

@Composable
fun EventCard(evento: Evento) {
    // ESTADO: Recordamos si esta tarjeta específica está marcada
    // use 'var' porque el valor cambiará
    var isFavorite by remember { mutableStateOf(false) }

    Card(
        // ... (mismo modificador y colores que antes)
    ) {
        Row(
            modifier = Modifier.padding(16.dp),
            verticalAlignment = Alignment.CenterVertically
        ) {
            // ... (Icono y Textos igual que antes) ...
            
            // COPIAR TODO LO ANTERIOR DEL EJERCICIO 2 AQUÍ PARA LA IMAGEN Y COLUMNA
            // ...
            
            // SUSTITUIMOS LA FECHA SIMPLE POR UNA COLUMNA CON FECHA E ICONO
             Column(
                horizontalAlignment = Alignment.End
            ) {
                Text(
                    text = evento.fecha,
                    style = MaterialTheme.typography.labelMedium,
                    color = MaterialTheme.colorScheme.primary
                )
                
                IconButton(onClick = { 
                    isFavorite = !isFavorite // Cambiamos el estado (true <-> false)
                }) {
                    Icon(
                        imageVector = if (isFavorite) Icons.Default.Favorite else Icons.Default.FavoriteBorder,
                        contentDescription = "Favorito",
                        tint = if (isFavorite) Color.Red else Color.Gray
                    )
                }
            }
        }
    }
}

```

> **Concepto clave (recomposición):** Al hacer clic, cambiamos `isFavorite`. Compose detecta que esa variable se usó para pintar el icono. Automáticamente vuelve a ejecutar la función `EventCard` solo para esa tarjeta, actualizando el icono de `FavoriteBorder` a `Favorite`.

---

### EJERCICIO 5. Scaffold y Material Design 3

**Cometido:**
Vamos a dar el toque final profesional añadiendo una barra superior (TopBar) y un botón flotante (FAB) utilizando el componente `Scaffold`, que es el esqueleto estándar de Material Design.

**Pasos:**

1. Ve a `MainActivity.kt`.
2. Vamos a "envolver" nuestra lista en un `Scaffold`. Modifica `EventListScreen`:

    ```kotlin
    import androidx.compose.material.icons.filled.Add
    import androidx.compose.material3.* // Importamos Scaffold y TopAppBarDefaults

    @OptIn(ExperimentalMaterial3Api::class) // TopAppBar es experimental en Material3
    @Composable
    fun EventListScreen() {
        val listaEventos = generarEventosFicticios()

        Scaffold(
            topBar = {
                TopAppBar(
                    title = { Text("TechEvents") },
                    colors = TopAppBarDefaults.topAppBarColors(
                        containerColor = MaterialTheme.colorScheme.primary,
                        titleContentColor = Color.White
                    )
                )
            },
            floatingActionButton = {
                FloatingActionButton(
                    onClick = { /* Acción futura */ },
                    containerColor = MaterialTheme.colorScheme.secondaryContainer
                ) {
                    Icon(Icons.Default.Add, contentDescription = "Añadir")
                }
            }
        ) { paddingValues -> 
            // Scaffold nos da 'paddingValues' para que el contenido no quede oculto tras la barra
            
            LazyColumn(
                contentPadding = paddingValues, // Usamos el padding del Scaffold
                modifier = Modifier
                    .fillMaxSize()
                    .padding(16.dp) // Padding extra estético
            ) {
                items(listaEventos) { evento ->
                    EventCard(evento = evento)
                    Spacer(modifier = Modifier.height(8.dp)) // Espacio entre items
                }
            }
        }
    }

    ```

> **Atención:** Si Android Studio marca error en `TopAppBar` o `Scaffold`, asegúrate de tener los imports de `androidx.compose.material3.*`.

---

### EJERCICIO FINAL. Reto: filtrado de lista (State Hoisting)

Hasta ahora el estado estaba *dentro* de la tarjeta. Pero, ¿y si queremos filtrar la lista desde fuera? Necesitamos un campo de texto en la pantalla principal que filtre la lista dinámica.

**Pasos:**

1. En `MainActivity.kt`, dentro de `EventListScreen`, crea un estado para el texto de búsqueda:
`var searchText by remember { mutableStateOf("") }`
2. Añade un `TextField` antes de la `LazyColumn` (necesitarás envolver ambos en un `Column` porque `Scaffold` solo acepta un hijo directo).
3. Filtra la lista basándote en ese texto.

**Código final de `EventListScreen` (Sustituye todo el método anterior):**

```kotlin
@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun EventListScreen() {
    // 1. Estado de la búsqueda
    var searchText by remember { mutableStateOf("") }
    
    // 2. Datos originales
    val todosLosEventos = remember { generarEventosFicticios() }
    
    // 3. Lógica de filtrado (Reactiva: se recalcula si cambia searchText)
    val eventosFiltrados = todosLosEventos.filter { 
        it.nombre.contains(searchText, ignoreCase = true) || 
        it.ubicacion.contains(searchText, ignoreCase = true)
    }

    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("TechEvents") },
                colors = TopAppBarDefaults.topAppBarColors(
                    containerColor = MaterialTheme.colorScheme.primary,
                    titleContentColor = Color.White
                )
            )
        }
    ) { paddingValues ->
        Column(
            modifier = Modifier
                .padding(paddingValues)
                .fillMaxSize()
        ) {
            // Buscador
            OutlinedTextField(
                value = searchText,
                onValueChange = { searchText = it }, // Actualizamos estado al escribir
                label = { Text("Buscar evento...") },
                modifier = Modifier
                    .fillMaxWidth()
                    .padding(16.dp),
                leadingIcon = { Icon(Icons.Default.Search, null) }
            )

            // Lista filtrada
            LazyColumn(
                contentPadding = PaddingValues(horizontal = 16.dp),
                modifier = Modifier.fillMaxSize()
            ) {
                items(eventosFiltrados) { evento ->
                    EventCard(evento = evento)
                    Spacer(modifier = Modifier.height(8.dp))
                }
            }
        }
    }
}

```

---

## 4. Documentación a entregar

Genera un documento PDF único que incluya las siguientes evidencias:

1. **Evidencia de configuración:** Captura de pantalla de `MainActivity.kt` mostrando el código limpio y los imports de Compose.
2. **Diseño atómico:** Captura de la **Preview** de `EventCard` (Ejercicio 2) mostrando cómo se ve una sola tarjeta aislada.
3. **Lista interactiva:** Captura de la aplicación ejecutándose en el emulador, mostrando la lista de eventos. Debes haber pulsado el corazón en al menos dos eventos para que se vea el **icono rojo (Estado activado)**.
4. **Búsqueda funcional:** Captura de pantalla donde hayas escrito "Madrid" (u otra ciudad) en el buscador, mostrando cómo la lista se ha filtrado automáticamente para mostrar solo esos eventos.
5. **Reflexión breve:** Escribe un párrafo de 5 líneas comparando tu experiencia haciendo esta lista con Compose frente a cómo lo hacíamos con `RecyclerView` y XML en temas anteriores. ¿Qué te ha parecido más difícil? ¿Qué te ha parecido más rápido?

## 5. Entrega

Sube a la plataforma el documento **PDF** con las capturas y respuestas. No es necesario subir el código fuente comprimido.
