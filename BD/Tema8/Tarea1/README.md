# 🗄️ TAREA 1: Diseño y Definición de Datos (DDL) en Oracle

  

**Unidad:** Tema 8 - El Lenguaje SQL  
**Base de Datos:** `DAVANTE`  
**Usuario:** `alumno`

## 🎯 Objetivo

Poner en práctica las sentencias de definición de datos (**DDL**) vistas en la unidad. El alumno deberá ser capaz de traducir un modelo de datos lógico a físico, eligiendo los tipos de datos adecuados (`NUMBER`, `VARCHAR2`, `DATE`) y asegurando la integridad de la información mediante restricciones (`CONSTRAINTS`).

-----

## ⚙️ Instrucciones previas

1.  Abre tu **SQL Developer** y conecta a la base de datos `DAVANTE` (la PDB que creamos en la instalación).
2.  Crea un nuevo fichero SQL (Hoja de trabajo) llamado `practica_tema8_ddl.sql`.
3.  Todas las sentencias deben escribirse en ese fichero.
4.  **Importante:** Añade comentarios antes de cada bloque de código explicando qué hace.
    ```sql
    -- Ejemplo: Creación de la tabla de pruebas
    CREATE TABLE ...
    ```

-----

## 🚀 Bloque 1: Creación de tablas maestras (entidades fuertes)

En este bloque crearemos las tablas que no dependen de otras. Practicaremos los tipos de datos básicos y restricciones simples.

### Ejercicio 1.1: Categorías

Crea una tabla llamada `CATEGORIAS` para organizar los productos.

  * **ID\_CATEGORIA:** Numérico. Será la Clave Primaria (PK).
  * **NOMBRE:** Texto variable (50 caracteres). Obligatorio (`NOT NULL`) y no puede repetirse (`UNIQUE`).
  * **DESCRIPCION:** Texto variable (200 caracteres). Opcional.

### Ejercicio 1.2: Almacenes

Crea una tabla llamada `ALMACENES` para gestionar el stock físico.

  * **CODIGO:** Numérico (3 dígitos). Clave Primaria.
  * **UBICACION:** Texto (100 caracteres). Obligatorio.
  * **CAPACIDAD\_M2:** Numérico. Debe ser siempre mayor que 0 (Usa `CHECK`).

### Ejercicio 1.3: Proveedores (Con valores por defecto)

Crea la tabla `PROVEEDORES`.

  * **CIF:** Texto (9 caracteres). Clave Primaria.
  * **EMPRESA:** Texto (100 caracteres). Obligatorio.
  * **TELEFONO:** Numérico (9 dígitos).
  * **PAIS:** Texto (20 caracteres). Si no se especifica, debe guardar el valor 'España' (`DEFAULT`).
  * **ACTIVO:** Numérico (1 dígito). Solo puede valer 0 o 1 (Usa `CHECK` con `IN`).

-----

## 🔗 Bloque 2: Tablas con relaciones (Foreign Keys)

Ahora crearemos tablas que dependen de las anteriores. Presta atención a la integridad referencial.

### Ejercicio 2.1: Productos (Constraints "Inline")

Crea la tabla `PRODUCTOS` definiendo las restricciones en la misma línea de la columna (Inline).

  * **ID\_PRODUCTO:** Numérico (5 dígitos). PK.
  * **NOMBRE:** Texto (100). Not Null.
  * **PRECIO:** Numérico (con 2 decimales).
  * **ID\_CATEGORIA:** Numérico. Debe ser Clave Foránea (`FK`) que apunte a `CATEGORIAS`.
  * **PROVEEDOR\_CIF:** Texto (9). FK que apunte a `PROVEEDORES`.

### Ejercicio 2.2: Empleados (Constraints "Out-of-line")

Crea la tabla `EMPLEADOS`. Define las restricciones **al final** de la sentencia `CREATE TABLE` (Out-of-line), asignándoles nombres personalizados (ej: `PK_EMPLEADOS`, `FK_EMP_ALMACEN`).

  * **DNI:** Texto (9). PK.
  * **NOMBRE:** Texto (50).
  * **APELLIDOS:** Texto (100).
  * **SALARIO:** Numérico.
  * **COD\_ALMACEN:** Numérico (3). FK hacia `ALMACENES`.
  * **FECHA\_CONTRATO:** Fecha (`DATE`). Por defecto la fecha actual (`SYSDATE`).

> **Restricción extra:** Añade un `CHECK` llamado `CK_SALARIO_MIN` para asegurar que el salario sea al menos el Salario Mínimo (ej. \> 1000).

-----

## 🛠️ Bloque 3: Modificación de la estructura (ALTER TABLE)

El diseño rara vez es perfecto a la primera. Vamos a realizar cambios estructurales sin borrar las tablas.

### Ejercicio 3.1: Añadir columnas

Nos hemos olvidado del correo electrónico de los empleados.

  * Añade una columna `EMAIL` a la tabla `EMPLEADOS` de tipo `VARCHAR2(100)`.

### Ejercicio 3.2: Modificar tipo de dato

La descripción de las categorías se ha quedado corta.

  * Modifica la columna `DESCRIPCION` de la tabla `CATEGORIAS` para que acepte hasta **500 caracteres**.

### Ejercicio 3.3: Renombrar columnas

En la tabla `PROVEEDORES`, el campo `EMPRESA` debería llamarse `NOMBRE_FISCAL`.

  * Renombra dicha columna.

### Ejercicio 3.4: Añadir restricciones a posteriori

Queremos asegurar que todos los emails de los empleados sean únicos, pero se nos olvidó ponerlo al crear la tabla.

  * Añade una restricción `UNIQUE` a la columna `EMAIL` de la tabla `EMPLEADOS` mediante el comando `ALTER TABLE`. Llama a la restricción `UQ_EMP_EMAIL`.

-----

## 🛒 Bloque 4: La tabla transaccional

Vamos a crear la tabla que registra los movimientos. Esta tabla unirá Clientes (que no tenemos, asume que es una venta anónima o crea una tabla rápida si quieres practicar más) y Productos.

### Ejercicio 4.1: Ventas

Crea la tabla `VENTAS`.

  * **ID\_VENTA:** Numérico. PK.
  * **FECHA\_VENTA:** Fecha.
  * **PRODUCTO\_ID:** Numérico. FK hacia `PRODUCTOS`.
  * **CANTIDAD:** Numérico.
  * **EMPLEADO\_DNI:** Texto(9). FK hacia `EMPLEADOS`.

> **Importante:** Configura la FK de `PRODUCTO_ID` para que si se borra un producto del catálogo, **se borren automáticamente** todas sus ventas asociadas (`ON DELETE CASCADE`).

-----

## 🗑️ Bloque 5: Limpieza y mantenimiento (DROP y TRUNCATE)

### Ejercicio 5.1: Diferencia entre Truncate y Drop

1.  Inserta un dato ficticio en categorías (opcional, solo para verificar).
2.  Ejecuta el comando `TRUNCATE TABLE CATEGORIAS`. (Nota: Si da error por claves foráneas, ignóralo o usa `DELETE`, pero el objetivo es intentar usar TRUNCATE). *Comenta en tu código para qué sirve TRUNCATE.*
3.  Intenta borrar la tabla `PROVEEDORES` con `DROP TABLE`. ¿Te deja? ¿Por qué?

### Ejercicio 5.2: Borrado total

Escribe las sentencias necesarias para borrar **todas** las tablas creadas en esta práctica, en el orden correcto para no violar la integridad referencial (o usando `CASCADE CONSTRAINTS`).

-----

## ✅ Checklist de entrega

Antes de guardar tu script, verifica:

  - [ ] He creado las 6 tablas (`CATEGORIAS`, `ALMACENES`, `PROVEEDORES`, `PRODUCTOS`, `EMPLEADOS`, `VENTAS`).
  - [ ] He utilizado restricciones `INLINE` y `OUT-OF-LINE`.
  - [ ] He aplicado correctamente los tipos de datos `NUMBER`, `VARCHAR2` y `DATE`.
  - [ ] He configurado valores por defecto con `DEFAULT` y `SYSDATE`.
  - [ ] He modificado tablas existentes con `ALTER TABLE` (ADD, MODIFY, RENAME).
  - [ ] He probado el borrado de tablas (`DROP`).
  - [ ] El código está comentado y ordenado.

-----

## 🕵️ Solucionario

<details>
  
<summary><strong>⬇️ Haz clic aquí para desplegar la solución completa</strong></summary>

```sql
/* SOLUCIÓN PRÁCTICA TEMA 8 - DDL
   Autor: Profesor DAM
   Base de Datos: DAVANTE
*/

-- ==========================================
-- BLOQUE 1: TABLAS MAESTRAS
-- ==========================================

-- 1.1 CATEGORIAS
CREATE TABLE CATEGORIAS (
    ID_CATEGORIA NUMBER CONSTRAINT PK_CATEGORIA PRIMARY KEY,
    NOMBRE VARCHAR2(50) NOT NULL CONSTRAINT UQ_CAT_NOMBRE UNIQUE,
    DESCRIPCION VARCHAR2(200)
);

-- 1.2 ALMACENES
CREATE TABLE ALMACENES (
    CODIGO NUMBER(3) PRIMARY KEY,
    UBICACION VARCHAR2(100) NOT NULL,
    CAPACIDAD_M2 NUMBER CHECK (CAPACIDAD_M2 > 0)
);

-- 1.3 PROVEEDORES
CREATE TABLE PROVEEDORES (
    CIF VARCHAR2(9) PRIMARY KEY,
    EMPRESA VARCHAR2(100) NOT NULL,
    TELEFONO NUMBER(9),
    PAIS VARCHAR2(20) DEFAULT 'España',
    ACTIVO NUMBER(1) CHECK (ACTIVO IN (0, 1))
);

-- ==========================================
-- BLOQUE 2: RELACIONES
-- ==========================================

-- 2.1 PRODUCTOS (Constraints Inline)
CREATE TABLE PRODUCTOS (
    ID_PRODUCTO NUMBER(5) PRIMARY KEY,
    NOMBRE VARCHAR2(100) NOT NULL,
    PRECIO NUMBER(10, 2), -- 10 dígitos, 2 decimales
    ID_CATEGORIA NUMBER REFERENCES CATEGORIAS(ID_CATEGORIA),
    PROVEEDOR_CIF VARCHAR2(9) REFERENCES PROVEEDORES(CIF)
);

-- 2.2 EMPLEADOS (Constraints Out-of-line)
CREATE TABLE EMPLEADOS (
    DNI VARCHAR2(9),
    NOMBRE VARCHAR2(50),
    APELLIDOS VARCHAR2(100),
    SALARIO NUMBER,
    COD_ALMACEN NUMBER(3),
    FECHA_CONTRATO DATE DEFAULT SYSDATE,
    -- Definición de restricciones al final
    CONSTRAINT PK_EMPLEADOS PRIMARY KEY (DNI),
    CONSTRAINT FK_EMP_ALMACEN FOREIGN KEY (COD_ALMACEN) REFERENCES ALMACENES(CODIGO),
    CONSTRAINT CK_SALARIO_MIN CHECK (SALARIO > 1000)
);

-- ==========================================
-- BLOQUE 3: ALTER TABLE
-- ==========================================

-- 3.1 Añadir columna
ALTER TABLE EMPLEADOS ADD (EMAIL VARCHAR2(100));

-- 3.2 Modificar tipo de dato
ALTER TABLE CATEGORIAS MODIFY (DESCRIPCION VARCHAR2(500));

-- 3.3 Renombrar columna
ALTER TABLE PROVEEDORES RENAME COLUMN EMPRESA TO NOMBRE_FISCAL;

-- 3.4 Añadir restricción a posteriori
ALTER TABLE EMPLEADOS ADD CONSTRAINT UQ_EMP_EMAIL UNIQUE (EMAIL);

-- ==========================================
-- BLOQUE 4: TRANSACCIONAL
-- ==========================================

-- 4.1 VENTAS
CREATE TABLE VENTAS (
    ID_VENTA NUMBER PRIMARY KEY,
    FECHA_VENTA DATE DEFAULT SYSDATE,
    PRODUCTO_ID NUMBER(5),
    CANTIDAD NUMBER,
    EMPLEADO_DNI VARCHAR2(9),
    -- FK con borrado en cascada
    CONSTRAINT FK_VENTA_PROD FOREIGN KEY (PRODUCTO_ID) 
        REFERENCES PRODUCTOS(ID_PRODUCTO) ON DELETE CASCADE,
    -- FK estándar
    CONSTRAINT FK_VENTA_EMP FOREIGN KEY (EMPLEADO_DNI) 
        REFERENCES EMPLEADOS(DNI)
);

-- ==========================================
-- BLOQUE 5: LIMPIEZA
-- ==========================================

/* 5.1 TRUNCATE vs DROP
   TRUNCATE elimina todos los datos de la tabla pero mantiene la estructura.
   Es más rápido que DELETE. No se puede usar si hay FK apuntando a la tabla (salvo cascade).
   
   DROP TABLE elimina datos Y estructura.
*/

-- 5.2 BORRADO TOTAL (Orden inverso a la creación o usando CASCADE)
DROP TABLE VENTAS;
DROP TABLE EMPLEADOS;
DROP TABLE PRODUCTOS;
DROP TABLE PROVEEDORES;
DROP TABLE ALMACENES;
DROP TABLE CATEGORIAS;

-- Opción alternativa "bruta" para no pensar en el orden:
-- DROP TABLE CATEGORIAS CASCADE CONSTRAINTS;
```


</details>
