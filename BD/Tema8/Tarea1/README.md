# 📝 TAREA 1 DDL: Diseño y Definición de Datos (Gestión de Stock)

**Objetivo:** Poner en práctica las sentencias de definición de datos (DDL). El alumno deberá traducir un modelo lógico a físico, eligiendo los tipos de datos adecuados y asegurando la integridad mediante restricciones (`CONSTRAINTS`).
**Requisitos:** Tener el servidor Oracle arrancado y SQL Developer operativo.

-----

## 🔌 Paso 0: Conexión al entorno

No utilizaremos el usuario de sistema. Debes trabajar en el entorno seguro (PDB) que creaste durante la instalación.

1.  Abre **SQL Developer**.
2.  Conéctate usando la conexión **`DAVANTE_Curso`** (o la conexión a tu PDB `DAVANTE`).
3.  Abre una nueva **Hoja de Trabajo SQL** (SQL Worksheet).
4.  Guarda el fichero como `practica_tema8_ddl.sql`.

> **⚠️ Aviso importante:** En Oracle, las sentencias DDL (`CREATE`, `ALTER`, `DROP`) ejecutan un **auto-commit**. Los cambios se guardan al instante y no se pueden deshacer con `ROLLBACK`. Lee dos veces antes de ejecutar.

-----

## 🟢 Nivel 1: Creación de tablas maestras (Entidades fuertes)

Empezaremos creando las tablas que no dependen de otras. Practicaremos los tipos de datos básicos y restricciones simples.

### Ejercicio 1.1: Categorías

Crea una tabla llamada `U8T1_CATEGORIAS` para organizar los productos.

  * `ID_CATEGORIA`: Numérico. Será la Clave Primaria (PK).
  * `NOMBRE`: Texto variable (50 caracteres). Obligatorio (`NOT NULL`) y no puede repetirse (`UNIQUE`).
  * `DESCRIPCION`: Texto variable (200 caracteres). Opcional.

<details>
<summary>🔻 Solución propuesta</summary>

```sql
CREATE TABLE U8T1_CATEGORIAS (
    ID_CATEGORIA NUMBER CONSTRAINT PK_U8T1_CATEGORIA PRIMARY KEY,
    NOMBRE VARCHAR2(50) NOT NULL CONSTRAINT UQ_U8T1_CAT_NOMBRE UNIQUE,
    DESCRIPCION VARCHAR2(200)
);
```

</details>

### Ejercicio 1.2: Almacenes

Crea una tabla llamada `U8T1_ALMACENES` para gestionar el stock físico.

  * `CODIGO`: Numérico (3 dígitos). Clave Primaria.
  * `UBICACION`: Texto (100 caracteres). Obligatorio.
  * `CAPACIDAD_M2`: Numérico. Debe ser siempre mayor que 0 (Usa `CHECK`).

<details>
<summary>🔻 Solución propuesta</summary>

```sql
CREATE TABLE U8T1_ALMACENES (
    CODIGO NUMBER(3) PRIMARY KEY,
    UBICACION VARCHAR2(100) NOT NULL,
    CAPACIDAD_M2 NUMBER CHECK (CAPACIDAD_M2 > 0)
);
```

</details>

### Ejercicio 1.3: Proveedores (Con valores por defecto)

Crea la tabla `U8T1_PROVEEDORES`.

  * `CIF`: Texto (9 caracteres). Clave Primaria.
  * `EMPRESA`: Texto (100 caracteres). Obligatorio.
  * `TELEFONO`: Numérico (9 dígitos).
  * `PAIS`: Texto (20 caracteres). Si no se especifica, debe guardar el valor 'España' (`DEFAULT`).
  * `ACTIVO`: Numérico (1 dígito). Solo puede valer 0 o 1 (Usa `CHECK` con `IN`).

<details>
<summary>🔻 Solución propuesta</summary>

```sql
CREATE TABLE U8T1_PROVEEDORES (
    CIF VARCHAR2(9) PRIMARY KEY,
    EMPRESA VARCHAR2(100) NOT NULL,
    TELEFONO NUMBER(9),
    PAIS VARCHAR2(20) DEFAULT 'España',
    ACTIVO NUMBER(1) CHECK (ACTIVO IN (0, 1))
);
```

</details>

-----

## 🟡 Nivel 2: Tablas con relaciones (Foreign Keys)

Ahora crearemos tablas que dependen de las anteriores. Presta atención a la integridad referencial.

### Ejercicio 2.1: Productos (Constraints "Inline")

Crea la tabla `U8T1_PRODUCTOS` definiendo las restricciones en la misma línea de la columna (Inline).

  * `ID_PRODUCTO`: Numérico (5 dígitos). PK.
  * `NOMBRE`: Texto (100). Not Null.
  * `PRECIO`: Numérico (con 2 decimales).
  * `ID_CATEGORIA`: Numérico. Debe ser Clave Foránea (`FK`) que apunte a `U8T1_CATEGORIAS`.
  * `PROVEEDOR_CIF`: Texto (9). FK que apunte a `U8T1_PROVEEDORES`.

<details>
<summary>🔻 Solución propuesta</summary>

```sql
CREATE TABLE U8T1_PRODUCTOS (
    ID_PRODUCTO NUMBER(5) PRIMARY KEY,
    NOMBRE VARCHAR2(100) NOT NULL,
    PRECIO NUMBER(10, 2), 
    ID_CATEGORIA NUMBER REFERENCES U8T1_CATEGORIAS(ID_CATEGORIA),
    PROVEEDOR_CIF VARCHAR2(9) REFERENCES U8T1_PROVEEDORES(CIF)
);
```

</details>

### Ejercicio 2.2: Empleados (Constraints "Out-of-line")

Crea la tabla `U8T1_EMPLEADOS`. Define las restricciones **al final** de la sentencia `CREATE TABLE` (Out-of-line), asignándoles nombres personalizados (ej: `PK_EMPLEADOS`, `FK_EMP_ALMACEN`).

  * `DNI`: Texto (9). PK.
  * `NOMBRE`: Texto (50).
  * `APELLIDOS`: Texto (100).
  * `SALARIO`: Numérico.
  * `COD_ALMACEN`: Numérico (3). FK hacia `U8T1_ALMACENES`.
  * `FECHA_CONTRATO`: Fecha (`DATE`). Por defecto la fecha actual (`SYSDATE`).

> **Restricción extra:** Añade un `CHECK` llamado `CK_SALARIO_MIN` para asegurar que el salario sea al menos el Salario Mínimo (ej. > 1000).

<details>
<summary>🔻 Solución propuesta</summary>

```sql
CREATE TABLE U8T1_EMPLEADOS (
    DNI VARCHAR2(9),
    NOMBRE VARCHAR2(50),
    APELLIDOS VARCHAR2(100),
    SALARIO NUMBER,
    COD_ALMACEN NUMBER(3),
    FECHA_CONTRATO DATE DEFAULT SYSDATE,
    -- Definición de restricciones al final
    CONSTRAINT PK_U8T1_EMPLEADOS PRIMARY KEY (DNI),
    CONSTRAINT FK_U8T1_EMP_ALMACEN FOREIGN KEY (COD_ALMACEN) REFERENCES U8T1_ALMACENES(CODIGO),
    CONSTRAINT CK_U8T1_SALARIO_MIN CHECK (SALARIO > 1000)
);
```

</details>

-----

## 🟠 Nivel 3: Modificación de la estructura (ALTER TABLE)

El diseño rara vez es perfecto a la primera. Vamos a realizar cambios estructurales sin borrar las tablas.

### Ejercicio 3.1: Añadir columnas

Nos hemos olvidado del correo electrónico de los empleados.

  * Añade una columna `EMAIL` a la tabla `U8T1_EMPLEADOS` de tipo `VARCHAR2(100)`.

<details>
<summary>🔻 Solución propuesta</summary>

```sql
ALTER TABLE U8T1_EMPLEADOS ADD (EMAIL VARCHAR2(100));
```

</details>

### Ejercicio 3.2: Modificar tipo de dato

La descripción de las categorías se ha quedado corta.

  * Modifica la columna `DESCRIPCION` de la tabla `U8T1_CATEGORIAS` para que acepte hasta **500 caracteres**.

<details>
<summary>🔻 Solución propuesta</summary>

```sql
ALTER TABLE U8T1_CATEGORIAS MODIFY (DESCRIPCION VARCHAR2(500));
```

</details>

### Ejercicio 3.3: Renombrar columnas

En la tabla `U8T1_PROVEEDORES`, el campo `EMPRESA` debería llamarse `NOMBRE_FISCAL`.

  * Renombra dicha columna.

<details>
<summary>🔻 Solución propuesta</summary>

```sql
ALTER TABLE U8T1_PROVEEDORES RENAME COLUMN EMPRESA TO NOMBRE_FISCAL;
```

</details>

### Ejercicio 3.4: Añadir restricciones a posteriori

Queremos asegurar que todos los emails de los empleados sean únicos, pero se nos olvidó ponerlo al crear la tabla.

  * Añade una restricción `UNIQUE` a la columna `EMAIL` de la tabla `U8T1_EMPLEADOS` mediante el comando `ALTER TABLE`. Llama a la restricción `UQ_EMP_EMAIL`.

<details>
<summary>🔻 Solución propuesta</summary>

```sql
ALTER TABLE U8T1_EMPLEADOS ADD CONSTRAINT UQ_U8T1_EMP_EMAIL UNIQUE (EMAIL);
```

</details>

-----

## 🔵 Nivel 4: La tabla transaccional

Vamos a crear la tabla que registra los movimientos. Esta tabla unirá Empleados y Productos.

### Ejercicio 4.1: Ventas

Crea la tabla `U8T1_VENTAS`.

  * `ID_VENTA`: Numérico. PK.
  * `FECHA_VENTA`: Fecha. `DEFAULT SYSDATE`.
  * `PRODUCTO_ID`: Numérico. FK hacia `U8T1_PRODUCTOS`.
  * `CANTIDAD`: Numérico.
  * `EMPLEADO_DNI`: Texto(9). FK hacia `U8T1_EMPLEADOS`.

> **Importante:** Configura la FK de `PRODUCTO_ID` para que si se borra un producto del catálogo, **se borren automáticamente** todas sus ventas asociadas (`ON DELETE CASCADE`).

<details>
<summary>🔻 Solución propuesta</summary>

```sql
CREATE TABLE U8T1_VENTAS (
    ID_VENTA NUMBER PRIMARY KEY,
    FECHA_VENTA DATE DEFAULT SYSDATE,
    PRODUCTO_ID NUMBER(5),
    CANTIDAD NUMBER,
    EMPLEADO_DNI VARCHAR2(9),
    
    -- FK con borrado en cascada
    CONSTRAINT FK_U8T1_VENTA_PROD FOREIGN KEY (PRODUCTO_ID) 
        REFERENCES U8T1_PRODUCTOS(ID_PRODUCTO) ON DELETE CASCADE,
        
    -- FK estándar
    CONSTRAINT FK_U8T1_VENTA_EMP FOREIGN KEY (EMPLEADO_DNI) 
        REFERENCES U8T1_EMPLEADOS(DNI)
);
```

</details>

-----

## 🗑️ Nivel 5: Limpieza y mantenimiento (DROP y TRUNCATE)

### Ejercicio 5.1: Diferencia entre Truncate y Drop

1.  Inserta un dato ficticio en categorías (opcional, solo para verificar).
2.  Ejecuta el comando `TRUNCATE TABLE U8T1_CATEGORIAS`. (Nota: Si da error por claves foráneas, ignóralo o usa `DELETE`, pero el objetivo es intentar usar TRUNCATE). *Comenta en tu código para qué sirve TRUNCATE.*
3.  Intenta borrar la tabla `U8T1_PROVEEDORES` con `DROP TABLE`. ¿Te deja? ¿Por qué?

### Ejercicio 5.2: Borrado total

Escribe las sentencias necesarias para borrar **todas** las tablas creadas en esta práctica, en el orden correcto para no violar la integridad referencial.

<details>
<summary>🔻 Solución propuesta (Orden de borrado)</summary>

El orden es siempre de **Hijos -> Padres**.

1.  `U8T1_VENTAS` (Depende de Productos y Empleados)
2.  `U8T1_EMPLEADOS` (Depende de Almacenes)
3.  `U8T1_PRODUCTOS` (Depende de Categorías y Proveedores)
4.  `U8T1_PROVEEDORES`
5.  `U8T1_ALMACENES`
6.  `U8T1_CATEGORIAS`

<!-- end list -->

```sql
DROP TABLE U8T1_VENTAS;
DROP TABLE U8T1_EMPLEADOS;
DROP TABLE U8T1_PRODUCTOS;
DROP TABLE U8T1_PROVEEDORES;
DROP TABLE U8T1_ALMACENES;
DROP TABLE U8T1_CATEGORIAS;
```

</details>

-----

## ✅ Checklist de entrega

Antes de guardar tu script, verifica:

  - [ ] He creado las 6 tablas con el prefijo `U8T1_`.
  - [ ] He utilizado restricciones `INLINE` y `OUT-OF-LINE`.
  - [ ] He configurado valores por defecto con `DEFAULT` y `SYSDATE`.
  - [ ] He modificado tablas existentes con `ALTER TABLE` (ADD, MODIFY, RENAME).
  - [ ] He probado el borrado de tablas (`DROP`) en el orden correcto.
  - [ ] El código está comentado y ordenado.