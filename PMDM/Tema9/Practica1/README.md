# Tema 9. Servicios en red II

**Práctica 1: "Investigación y modernización: Volley vs Retrofit"**

## 1. Introducción

En la tarea anterior (**Tarea 1**) construiste un cliente HTTP "a mano", utilizando `HttpURLConnection` y parseando el JSON línea a línea. Es un ejercicio excelente para entender las bases, pero en el desarrollo profesional actual **nadie trabaja así**. Es propenso a errores, tedioso y difícil de mantener.

Hoy en día, los desarrolladores Android utilizan librerías potentes que automatizan la conexión y la conversión de datos (de JSON a Objetos Java). Las dos más famosas históricamente han sido **Volley** (de Google) y **Retrofit** (de Square).

En esta práctica, tu papel cambia. Ya no solo eres un programador que sigue instrucciones, sino un **Ingeniero de Software** que debe investigar, comparar tecnologías y tomar decisiones, apoyándote en herramientas de **Inteligencia Artificial** (ChatGPT, Gemini, Copilot, etc.) para acelerar tu desarrollo.

## 2. Objetivos de aprendizaje

* Utilizar la **IA Generativa** como asistente de investigación técnica y generación de código.
* Comparar librerías de terceros (**Volley vs Retrofit**) basándose en popularidad, facilidad de uso y mantenimiento.
* Implementar la librería ganadora para conectar con una API REST.
* Utilizar **GSON** para la serialización/deserialización automática (adiós al parseo manual).
* Documentar el proceso de investigación y la "conversación" con la IA.

## 3. Descripción de la práctica

La práctica se divide en dos fases claras: **Investigación** y **Desarrollo**. Deberás generar un documento detallado de la primera fase y una aplicación funcional en la segunda.

### Fase 1: Investigación asistida por IA

Debes usar una IA (la que prefieras) para responder a las siguientes cuestiones. **Es vital que guardes los "prompts" (preguntas) que le haces.**

1.  **Comparativa:** Pide a la IA que compare **Volley** y **Retrofit**. Pregúntale cuál es el estándar de la industria actualmente y por qué.
2.  **Sintaxis:** Pide ejemplos de código sencillos de cómo se hace una petición GET con cada una.
3.  **Decisión:** Basándote en lo que te diga la IA, elige una de las dos para tu proyecto.

### Fase 2: Desarrollo de la App

Debes crear una nueva aplicación que haga **exactamente lo mismo que la Tarea 1** (listar productos desde tu API PHP local), pero con las siguientes reglas:

1.  **Prohibido `HttpURLConnection`:** Debes usar la librería que hayas elegido (Retrofit o Volley).
2.  **Prohibido `JSONObject` manual:** Debes integrar la librería **GSON**. Los datos deben convertirse de JSON a `ArrayList<Producto>` automáticamente.
3.  **Arquitectura:** Debes mantener la separación de código.
    * Si usas **Retrofit**, deberás definir una `Interface` para los endpoints.
    * Si usas **Volley**, deberás gestionar la `RequestQueue` preferiblemente en un patrón Singleton.

El resultado visual para el usuario debe ser idéntico al de la tarea anterior (un RecyclerView con los productos), pero el código interno será mucho más limpio y moderno.

---

## 4. Instrucciones paso a paso para el uso de la IA

No se trata solo de copiar y pegar código. Debes iterar con la IA. Aquí tienes un ejemplo de flujo de trabajo recomendado:

1.  *Prompt de investigación:* "Actúa como un desarrollador Android experto. Compárame las librerías Volley y Retrofit. ¿Cuál se usa más hoy en día y por qué? Hazme una tabla comparativa."
2.  *Prompt de implementación:* "He decidido usar [Librería elegida]. Tengo un JSON que devuelve una lista de productos con los campos 'codigo', 'nombre' y 'precio'. ¿Cómo configuro [Librería] junto con GSON para recibir directamente una lista de objetos Java? Dame el código para el archivo build.gradle y para la clase de conexión."
3.  *Prompt de corrección:* Si el código falla (por ejemplo, por el tema de `localhost` vs `10.0.2.2`), pregúntale a la IA cómo solucionarlo.

---

## 5. Documentación a entregar

**ATENCIÓN:** La evaluación de esta práctica se realizará **exclusivamente a través del documento PDF**. No se corregirá el código fuente subido, por lo que las capturas deben ser claras y legibles.

El documento debe seguir la siguiente estructura:

### A. Diario de investigación (IA)
1.  **Los Prompts:** Copia y pega las preguntas exactas que le hiciste a la IA.
2.  **La Elección:** Explica brevemente: *"He elegido [Retrofit/Volley] porque la IA me indicó que..."* (Resume los argumentos principales: rendimiento, comunidad, facilidad, etc.).
3.  **Tabla Comparativa:** Incluye la tabla o resumen de diferencias que te generó la IA.

### B. Documentación técnica
1.  **Dependencias:** Captura de tu `build.gradle` mostrando las librerías añadidas (Retrofit/Volley + GSON).
2.  **La Interfaz/Conexión:**
    * Si es Retrofit: Captura de la **Interface** donde defines los métodos `@GET`.
    * Si es Volley: Captura de donde realizas la `JsonArrayRequest`.
3.  **El Modelo:** Captura de tu clase `Producto.java`. ¿Tuviste que añadir anotaciones como `@SerializedName`? Explícalo.
4.  **La Llamada:** Captura de la parte del `MainActivity` (o Controlador) donde ejecutas la llamada. Observa cuánto se ha reducido el código comparado con la Tarea 1.

### C. Evidencias
Para demostrar que la práctica es tuya y funciona en tu equipo, debes realizar lo siguiente:
1.  Ve a tu base de datos (PHPMyAdmin) o al código de tu API.
2.  Modifica el **nombre** del primer producto para que incluya tu primer apellido entre paréntesis.
    * *Ejemplo:* Si el producto es "Monitor 24", cámbialo a "**Monitor 24 (García)**".
3.  Ejecuta la app en el emulador.
4.  **Haz una captura del emulador** donde se vea el listado y aparezca claramente ese primer producto con tu apellido.

### D. Reflexión personal
Responde a estas preguntas finales:
* ¿Qué diferencia has notado en la cantidad de código escrito entre la Tarea 1 (Nativa) y esta Práctica (Librerías)?
* ¿Te ha resultado útil el código generado por la IA o has tenido que corregirlo?
* ¿Por qué crees que es importante usar librerías de parseo automático (GSON) en lugar de hacerlo a mano?

---
## 6. Criterios de evaluación

La nota se asignará completamente en base al contenido del PDF entregado:

* **Investigación y uso de IA (30%):**
    * Se incluyen los prompts utilizados.
    * La justificación de la elección de la librería es coherente con la información extraída.
* **Implementación técnica - capturas (30%):**
    * Las capturas demuestran el uso correcto de la librería elegida (no hay `HttpURLConnection`).
    * Las capturas demuestran el uso de GSON (no hay `JSONObject` manuales).
    * El código mostrado sigue una estructura limpia.
* **Verificación de autoría (25%):**
    * **Imprescindible:** Aparece la captura del emulador funcionando.
    * **Imprescindible:** En la lista de productos aparece el apellido del alumno en el primer ítem. **(Si no aparece el apellido, la práctica se considerará no apta).**
* **Reflexión y calidad del documento (15%):**
    * Respuestas coherentes a las preguntas finales.
    * El documento está bien formateado y las capturas son legibles.

## 7. Entrega

Sube a la plataforma únicamente:
* **Informe PDF** con toda la documentación solicitada.