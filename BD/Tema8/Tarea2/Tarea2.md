# 📝 TAREA 1 DDL: Diseño de la base de datos "GAMER_SHOP"

**Objetivo:** Dominar el lenguaje de definición de datos (DDL) mediante la creación progresiva y detallada de un esquema de base de datos completo.
**Requisitos:** Tener el servidor Oracle 21c XE arrancado y SQL Developer operativo.

-----

## 🔌 Paso 0: Conexión al entorno

No utilizaremos el usuario de sistema. Debes trabajar en el entorno seguro (PDB) que creaste durante la instalación.

1.  Abre **SQL Developer**.
2.  Conéctate usando la conexión **`DAVANTE_Curso`** (Usuario: `alumno` o `administrador`, Contraseña: `alumno`).
3.  Abre una nueva **Hoja de Trabajo SQL** (SQL Worksheet).

> **⚠️ Aviso importante:** En Oracle, las sentencias DDL (`CREATE`, `ALTER`, `DROP`) ejecutan un **auto-commit**. Los cambios se guardan al instante y no se pueden deshacer con `ROLLBACK`. Lee dos veces antes de ejecutar.

-----

## 🟢 Nivel 1: Creación de tablas base (Entidades fuertes)

Empezaremos creando las tablas que no dependen de nadie (las tablas "padre").

### Ejercicio 1.1: El catálogo de juegos

Crea la tabla `GAMER_JUEGOS`. De momento, solo definiremos las columnas y sus tipos de datos.

  * `id_juego`: Número entero.
  * `titulo`: Texto variable (máximo 150 caracteres).
  * `precio`: Número.
  * `fecha_lanzamiento`: Fecha.
  * `stock`: Número entero (cantidad de copias físicas disponibles).

<details>
<summary>🔻 Solución propuesta</summary>

```sql
CREATE TABLE GAMER_JUEGOS (
    id_juego NUMBER,
    titulo VARCHAR2(150),
    precio NUMBER,
    fecha_lanzamiento DATE,
    stock NUMBER
);
```

</details>

### Ejercicio 1.2: Los estudios de desarrollo

Crea la tabla `GAMER_STUDIOS`.

  * `id_studio`: Número.
  * `nombre`: Texto (máximo 100).
  * `pais`: Texto (máximo 50).
  * `fecha_fundacion`: Fecha.
  * `web_oficial`: Texto (máximo 200).

<details>
<summary>🔻 Solución propuesta</summary>

```sql
CREATE TABLE GAMER_STUDIOS (
    id_studio NUMBER,
    nombre VARCHAR2(100),
    pais VARCHAR2(50),
    fecha_fundacion DATE,
    web_oficial VARCHAR2(200)
);
```

</details>

### Ejercicio 1.3: Los clientes

Crea la tabla `GAMER_CLIENTES`.

  * `id_cliente`: Número.
  * `nombre_completo`: Texto (máximo 100).
  * `email`: Texto (máximo 100).
  * `telefono`: Texto (máximo 15).
  * `puntos_fidelidad`: Número (para descuentos futuros).

<details>
<summary>🔻 Solución propuesta</summary>

```sql
CREATE TABLE GAMER_CLIENTES (
    id_cliente NUMBER,
    nombre_completo VARCHAR2(100),
    email VARCHAR2(100),
    telefono VARCHAR2(15),
    puntos_fidelidad NUMBER
);
```

</details>

-----

## 🟡 Nivel 2: Modificación estructural (ALTER TABLE)

Los requisitos han cambiado. Debemos adaptar las tablas existentes sin borrarlas.

### Ejercicio 2.1: Ajuste de precisión numérica

El departamento de contabilidad indica que el `precio` en `GAMER_JUEGOS` necesita controlar los céntimos y evitar precios desorbitados.

  * Modifica la columna para que acepte un máximo de 6 dígitos, de los cuales 2 sean decimales (ejemplo máximo: 9999.99).

<details>
<summary>🔻 Solución propuesta</summary>

```sql
ALTER TABLE GAMER_JUEGOS MODIFY (precio NUMBER(6,2));
```

</details>

### Ejercicio 2.2: Nuevos datos necesarios

Se nos olvidó clasificar los juegos.

  * Añade una columna `genero` a `GAMER_JUEGOS` (Texto, max 30).
  * Añade una columna `es_indie` a `GAMER_STUDIOS` para saber si es un estudio independiente. Usaremos un solo carácter ('S' o 'N').

<details>
<summary>🔻 Solución propuesta</summary>

```sql
ALTER TABLE GAMER_JUEGOS ADD (genero VARCHAR2(30));
ALTER TABLE GAMER_STUDIOS ADD (es_indie CHAR(1));
```

</details>

### Ejercicio 2.3: Limpieza y renombrado

La columna `web_oficial` en los estudios ocupa mucho espacio y casi no se usa.

  * Elimina la columna `web_oficial` de `GAMER_STUDIOS`.
  * Renombra la columna `nombre` de `GAMER_STUDIOS` a `nombre_studio` para evitar ambigüedades.

<details>
<summary>🔻 Solución propuesta</summary>

```sql
ALTER TABLE GAMER_STUDIOS DROP COLUMN web_oficial;
ALTER TABLE GAMER_STUDIOS RENAME COLUMN nombre TO nombre_studio;
```

</details>

-----

## 🟠 Nivel 3: Integridad de entidad (PK y NOT NULL)

Vamos a asegurar que cada registro sea único y tenga los datos mínimos obligatorios.

### Ejercicio 3.1: Claves primarias (Primary Keys)

  * Establece `id_juego` como PK de `GAMER_JUEGOS` (Nombre restricción: `PK_JUEGOS`).
  * Establece `id_studio` como PK de `GAMER_STUDIOS` (Nombre restricción: `PK_STUDIOS`).
  * Establece `id_cliente` como PK de `GAMER_CLIENTES` (Nombre restricción: `PK_CLIENTES`).

<details>
<summary>🔻 Solución propuesta</summary>

```sql
ALTER TABLE GAMER_JUEGOS ADD CONSTRAINT PK_JUEGOS PRIMARY KEY (id_juego);
ALTER TABLE GAMER_STUDIOS ADD CONSTRAINT PK_STUDIOS PRIMARY KEY (id_studio);
ALTER TABLE GAMER_CLIENTES ADD CONSTRAINT PK_CLIENTES PRIMARY KEY (id_cliente);
```

</details>

### Ejercicio 3.2: Campos obligatorios (NOT NULL)

  * En `GAMER_JUEGOS`: El `titulo` no puede estar vacío.
  * En `GAMER_CLIENTES`: El `email` es obligatorio.
  * En `GAMER_STUDIOS`: El `nombre_studio` es obligatorio.

> *Nota:* En Oracle, añadir NOT NULL se suele hacer modificando la columna.

<details>
<summary>🔻 Solución propuesta</summary>

```sql
ALTER TABLE GAMER_JUEGOS MODIFY (titulo NOT NULL);
ALTER TABLE GAMER_CLIENTES MODIFY (email NOT NULL);
ALTER TABLE GAMER_STUDIOS MODIFY (nombre_studio NOT NULL);
```

</details>

-----

## 🔵 Nivel 4: Reglas de negocio (UNIQUE, CHECK y DEFAULT)

Vamos a aplicar lógica de negocio para evitar errores humanos en la introducción de datos.

### Ejercicio 4.1: Valores por defecto (DEFAULT)

Si no especificamos datos al insertar, queremos que Oracle ponga valores automáticos.

  * Modifica `GAMER_JUEGOS`: El `stock` inicial por defecto debe ser 0.
  * Modifica `GAMER_CLIENTES`: Los `puntos_fidelidad` por defecto deben ser 0.

<details>
<summary>🔻 Solución propuesta</summary>

```sql
ALTER TABLE GAMER_JUEGOS MODIFY (stock DEFAULT 0);
ALTER TABLE GAMER_CLIENTES MODIFY (puntos_fidelidad DEFAULT 0);
```

</details>

### Ejercicio 4.2: Validaciones lógicas (CHECK)

  * En `GAMER_JUEGOS`: El `precio` nunca puede ser negativo. (Restricción: `CK_PRECIO_POS`).
  * En `GAMER_STUDIOS`: El campo `es_indie` solo debe aceptar los valores 'S' (Sí) o 'N' (No). (Restricción: `CK_INDIE_VALIDO`).
  * En `GAMER_CLIENTES`: El `email` debe contener al menos una arroba '@'. (Pista: Usa `LIKE '%@%'`).

<details>
<summary>🔻 Solución propuesta</summary>

```sql
ALTER TABLE GAMER_JUEGOS ADD CONSTRAINT CK_PRECIO_POS CHECK (precio >= 0);
ALTER TABLE GAMER_STUDIOS ADD CONSTRAINT CK_INDIE_VALIDO CHECK (es_indie IN ('S', 'N'));
ALTER TABLE GAMER_CLIENTES ADD CONSTRAINT CK_EMAIL_FORMATO CHECK (email LIKE '%@%');
```

</details>

### Ejercicio 4.3: Unicidad (UNIQUE)

  * No puede haber dos clientes con el mismo `email`. (Restricción: `UQ_CLIENTE_EMAIL`).
  * No queremos dos estudios con el mismo nombre. (Restricción: `UQ_NOMBRE_STUDIO`).

<details>
<summary>🔻 Solución propuesta</summary>

```sql
ALTER TABLE GAMER_CLIENTES ADD CONSTRAINT UQ_CLIENTE_EMAIL UNIQUE (email);
ALTER TABLE GAMER_STUDIOS ADD CONSTRAINT UQ_NOMBRE_STUDIO UNIQUE (nombre_studio);
```

</details>

-----

## 🟣 Nivel 5: Relaciones 1:N (Foreign Keys)

Llega el momento de conectar las tablas. Un estudio desarrolla muchos juegos.

### Ejercicio 5.1: Preparar la tabla hija

Para vincular los juegos con su creador, `GAMER_JUEGOS` necesita una columna que guarde el ID del estudio.

  * Añade la columna `id_studio` a `GAMER_JUEGOS`. Asegúrate de que tenga el **mismo tipo de dato** que la PK de `GAMER_STUDIOS`.

<details>
<summary>🔻 Solución propuesta</summary>

```sql
ALTER TABLE GAMER_JUEGOS ADD (id_studio NUMBER);
```

</details>

### Ejercicio 5.2: Crear el vínculo referencial

Crea la clave foránea que impida asignar un juego a un estudio inexistente.

  * Tabla origen: `GAMER_JUEGOS`.
  * Tabla destino: `GAMER_STUDIOS`.
  * Si se borra un estudio, no queremos que se borren sus juegos, sino que el campo se quede vacío (NULL). Configura **ON DELETE SET NULL**.

<details>
<summary>🔻 Solución propuesta</summary>

```sql
ALTER TABLE GAMER_JUEGOS ADD CONSTRAINT FK_JUEGO_STUDIO 
    FOREIGN KEY (id_studio) 
    REFERENCES GAMER_STUDIOS (id_studio)
    ON DELETE SET NULL;
```

</details>

-----

## 🟤 Nivel 6: Relaciones 1:1 (Foreign Key Única)

A veces queremos separar información muy pesada o específica en otra tabla, pero que pertenezca exclusivamente a un solo registro. Vamos a crear los **Requerimientos Técnicos** de cada juego.

### Ejercicio 6.1: Tabla de requerimientos

Crea la tabla `GAMER_REQUISITOS`.

  * `id_juego`: Número. (Será PK y FK a la vez).
  * `espacio_disco`: Número (en GB).
  * `ram_minima`: Número (en GB).
  * `tarjeta_grafica`: Texto (max 100).

<details>
<summary>🔻 Solución propuesta</summary>

```sql
CREATE TABLE GAMER_REQUISITOS (
    id_juego NUMBER,
    espacio_disco NUMBER,
    ram_minima NUMBER,
    tarjeta_grafica VARCHAR2(100)
);
```

</details>

### Ejercicio 6.2: Restricciones 1:1

Para que sea una relación 1:1 pura:

1.  Haz que `id_juego` sea la **Primary Key** de esta nueva tabla.
2.  Haz que `id_juego` sea también **Foreign Key** apuntando a `GAMER_JUEGOS(id_juego)`.
3.  Añade una regla: si se borra el juego, se deben borrar sus requisitos automáticamente (**ON DELETE CASCADE**).

<details>
<summary>🔻 Solución propuesta</summary>

```sql
-- Definir PK
ALTER TABLE GAMER_REQUISITOS ADD CONSTRAINT PK_REQUISITOS PRIMARY KEY (id_juego);

-- Definir FK con borrado en cascada
ALTER TABLE GAMER_REQUISITOS ADD CONSTRAINT FK_REQ_JUEGO 
    FOREIGN KEY (id_juego) 
    REFERENCES GAMER_JUEGOS (id_juego) 
    ON DELETE CASCADE;
```

</details>

-----

## ⚫ Nivel 7: Relaciones N:M (Tablas intermedias)

Vamos a registrar las ventas. Un cliente compra muchos juegos, un juego es comprado por muchos clientes. Necesitamos una tabla intermedia.

### Ejercicio 7.1: Crear tabla de Ventas

Crea la tabla `GAMER_VENTAS`.

  * Debe contener: `id_cliente`, `id_juego`, `fecha_compra` y `precio_pagado` (por si cambia el precio original).
  * Define las columnas con los tipos adecuados.

<details>
<summary>🔻 Solución propuesta</summary>

```sql
CREATE TABLE GAMER_VENTAS (
    id_cliente NUMBER,
    id_juego NUMBER,
    fecha_compra DATE DEFAULT SYSDATE,
    precio_pagado NUMBER(6,2) NOT NULL
);
```

</details>

### Ejercicio 7.2: La clave primaria compuesta

Un cliente puede comprar el mismo juego varias veces (por ejemplo, para regalar), pero NO en el mismo instante exacto.

  * Crea una **Primary Key Compuesta** formada por: `id_cliente`, `id_juego` y `fecha_compra`.

<details>
<summary>🔻 Solución propuesta</summary>

```sql
ALTER TABLE GAMER_VENTAS ADD CONSTRAINT PK_VENTAS 
    PRIMARY KEY (id_cliente, id_juego, fecha_compra);
```

</details>

### Ejercicio 7.3: Las claves foráneas

Asegura la integridad referencial.

  * FK hacia Clientes.
  * FK hacia Juegos.

<details>
<summary>🔻 Solución propuesta</summary>

```sql
ALTER TABLE GAMER_VENTAS ADD CONSTRAINT FK_VENTA_CLIENTE 
    FOREIGN KEY (id_cliente) REFERENCES GAMER_CLIENTES(id_cliente);

ALTER TABLE GAMER_VENTAS ADD CONSTRAINT FK_VENTA_JUEGO 
    FOREIGN KEY (id_juego) REFERENCES GAMER_JUEGOS(id_juego);
```

</details>

-----

## 🛡️ Nivel 8: Documentación y Mantenimiento

Una buena base de datos está documentada.

### Ejercicio 8.1: Comentarios

Añade comentarios al diccionario de datos para que otros desarrolladores entiendan tu modelo.

  * Añade un comentario a la tabla `GAMER_VENTAS`: "Registro histórico de transacciones".
  * Añade un comentario a la columna `es_indie` de `GAMER_STUDIOS`: "S=Sí, N=No".

<details>
<summary>🔻 Solución propuesta</summary>

```sql
COMMENT ON TABLE GAMER_VENTAS IS 'Registro histórico de transacciones';
COMMENT ON COLUMN GAMER_STUDIOS.es_indie IS 'S=Sí, N=No';
```

</details>

### Ejercicio 8.2: Desactivar restricciones

Imagina que necesitamos hacer una carga masiva de datos antiguos que no cumplen la regla del precio positivo.

  * Desactiva (DISABLE) temporalmente la restricción `CK_PRECIO_POS` de la tabla `GAMER_JUEGOS`.
  * (Opcional) Vuelve a activarla (ENABLE) después.

<details>
<summary>🔻 Solución propuesta</summary>

```sql
ALTER TABLE GAMER_JUEGOS DISABLE CONSTRAINT CK_PRECIO_POS;
-- Para reactivarla:
-- ALTER TABLE GAMER_JUEGOS ENABLE CONSTRAINT CK_PRECIO_POS;
```

</details>

-----

## 🚀 Nivel 9: El desafío final (Diseño libre)

El negocio se expande. Ahora necesitamos gestionar **Reseñas (Reviews)** de los usuarios.

**Requerimientos:**

1.  Crea una tabla `GAMER_REVIEWS`.
2.  Debe relacionar un cliente con un juego.
3.  Debe tener una puntuación (`rating`) numérica.
4.  Debe tener un comentario de texto.
5.  Aplica las siguientes reglas obligatorias:
      * La puntuación debe ser obligatoria y estar entre 1 y 5.
      * Un usuario SOLO puede escribir una reseña por juego (Pista: Clave Primaria o Unique compuesta).
      * Si se borra el usuario, se borran sus reseñas.

*Intenta hacerlo todo en una sola sentencia `CREATE TABLE`.*

<details>
<summary>🔻 Solución propuesta</summary>

```sql
CREATE TABLE GAMER_REVIEWS (
    id_cliente NUMBER,
    id_juego NUMBER,
    rating NUMBER NOT NULL,
    comentario VARCHAR2(500),
    fecha_review DATE DEFAULT SYSDATE,
    
    -- Restricciones
    CONSTRAINT PK_REVIEWS PRIMARY KEY (id_cliente, id_juego),
    CONSTRAINT CK_RATING_RANGO CHECK (rating BETWEEN 1 AND 5),
    
    CONSTRAINT FK_REVIEW_CLIENTE FOREIGN KEY (id_cliente) 
        REFERENCES GAMER_CLIENTES(id_cliente) ON DELETE CASCADE,
        
    CONSTRAINT FK_REVIEW_JUEGO FOREIGN KEY (id_juego) 
        REFERENCES GAMER_JUEGOS(id_juego) ON DELETE CASCADE
);
```

</details>

-----

## 🗑️ Nivel 10: Destrucción ordenada (DROP)

El proyecto ha terminado. Debes entregar el equipo limpio. Debes eliminar todas las tablas creadas.

Si intentas borrar `GAMER_STUDIOS` primero, Oracle te dará un error porque existen juegos que dependen de ella.

**Tu misión:** Escribe las sentencias `DROP TABLE` en el orden lógico exacto para que no ocurra ningún error de integridad referencial. (Intenta no usar `CASCADE CONSTRAINTS` para practicar el orden lógico).

<details>
<summary>🔻 Solución propuesta (Orden de borrado)</summary>

El orden es siempre de **Hijos -> Padres**.

1.  `GAMER_REVIEWS` (Depende de Clientes y Juegos)
2.  `GAMER_VENTAS` (Depende de Clientes y Juegos)
3.  `GAMER_REQUISITOS` (Depende de Juegos)
4.  `GAMER_CLIENTES` (Ya nadie depende de ella)
5.  `GAMER_JUEGOS` (Ya nadie depende de ella tras borrar los anteriores)
6.  `GAMER_STUDIOS` (Padre original)

<!-- end list -->

```sql
DROP TABLE GAMER_REVIEWS;
DROP TABLE GAMER_VENTAS;
DROP TABLE GAMER_REQUISITOS;
DROP TABLE GAMER_CLIENTES;
DROP TABLE GAMER_JUEGOS;
DROP TABLE GAMER_STUDIOS;
```

</details>