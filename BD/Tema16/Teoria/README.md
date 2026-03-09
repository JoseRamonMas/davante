# Unidad 16 — Tipos de datos compuestos y cursores


## Índice

1. [Introducción](#1-introducción)
2. [Esquema de base de datos de referencia](#2-esquema-de-base-de-datos-de-referencia)
3. [Registros (`RECORD`)](#3-registros-record)
   - 3.1. [Concepto y declaración](#31-concepto-y-declaración)
   - 3.2. [Atributos `%TYPE` y `%ROWTYPE`](#32-atributos-type-y-rowtype)
   - 3.3. [Asignación y restricciones](#33-asignación-y-restricciones)
   - 3.4. [Ejemplos completos](#34-ejemplos-completos)
4. [Arrays (`VARRAY`)](#4-arrays-varray)
   - 4.1. [Concepto y declaración](#41-concepto-y-declaración)
   - 4.2. [Inicialización y acceso](#42-inicialización-y-acceso)
   - 4.3. [Métodos disponibles](#43-métodos-disponibles)
   - 4.4. [Ejemplos completos](#44-ejemplos-completos)
5. [Arrays asociativos (`INDEX BY TABLE`)](#5-arrays-asociativos-index-by-table)
   - 5.1. [Concepto y características](#51-concepto-y-características)
   - 5.2. [Declaración, inicialización y acceso](#52-declaración-inicialización-y-acceso)
   - 5.3. [Métodos disponibles](#53-métodos-disponibles)
   - 5.4. [Ejemplos completos](#54-ejemplos-completos)
6. [Tablas anidadas (`NESTED TABLE`)](#6-tablas-anidadas-nested-table)
   - 6.1. [Concepto y diferencias con `VARRAY`](#61-concepto-y-diferencias-con-varray)
   - 6.2. [Declaración, inicialización y acceso](#62-declaración-inicialización-y-acceso)
   - 6.3. [Métodos disponibles](#63-métodos-disponibles)
   - 6.4. [Ejemplos completos](#64-ejemplos-completos)
7. [Comparativa de colecciones](#7-comparativa-de-colecciones)
8. [Cursores](#8-cursores)
   - 8.1. [Concepto](#81-concepto)
   - 8.2. [Propiedades de los cursores](#82-propiedades-de-los-cursores)
9. [Tipos de cursores](#9-tipos-de-cursores)
   - 9.1. [Cursores implícitos](#91-cursores-implícitos)
   - 9.2. [Cursores explícitos](#92-cursores-explícitos)
   - 9.3. [Cursores explícitos con parámetros](#93-cursores-explícitos-con-parámetros)
   - 9.4. [Cursores con `FOR LOOP` (cursor loop)](#94-cursores-con-for-loop-cursor-loop)
10. [Ciclo de vida de los cursores explícitos](#10-ciclo-de-vida-de-los-cursores-explícitos)
    - 10.1. [Apertura: `OPEN`](#101-apertura-open)
    - 10.2. [Extracción de datos: `FETCH`](#102-extracción-de-datos-fetch)
    - 10.3. [Cierre: `CLOSE`](#103-cierre-close)
    - 10.4. [Ejemplo completo del ciclo de vida](#104-ejemplo-completo-del-ciclo-de-vida)
11. [Actualización de registros con cursores (`FOR UPDATE`)](#11-actualización-de-registros-con-cursores-for-update)
12. [Casos prácticos resueltos](#12-casos-prácticos-resueltos)
    - 12.1. [Caso 1: Informe de empleados por departamento](#121-caso-1-informe-de-empleados-por-departamento)
    - 12.2. [Caso 2: Revisión salarial con cursor parametrizado](#122-caso-2-revisión-salarial-con-cursor-parametrizado)
    - 12.3. [Caso 3: Catálogo de habilidades con VARRAY](#123-caso-3-catálogo-de-habilidades-con-varray)
13. [Errores frecuentes y buenas prácticas](#13-errores-frecuentes-y-buenas-prácticas)
14. [Resumen visual](#14-resumen-visual)
15. [Ejercicios propuestos](#15-ejercicios-propuestos)

---

## 1. Introducción

En la unidad anterior aprendimos las bases de PL/SQL: bloques anónimos, variables, estructuras de control y procedimientos. En esta unidad damos un salto cualitativo incorporando **tipos de datos compuestos** y **cursores**, dos pilares fundamentales para escribir código PL/SQL capaz de resolver problemas reales de gestión de datos.

Los **tipos de datos compuestos** permiten agrupar varios datos relacionados en una sola estructura, de forma similar a cómo un registro en papel puede contener nombre, dirección y teléfono de una persona. En PL/SQL disponemos de tres tipos principales:

- **Registros (`RECORD`)** — agrupan campos de tipos heterogéneos, similar a una fila de tabla.
- **Arrays (`VARRAY`)** — colecciones ordenadas de tamaño máximo fijo.
- **Tablas anidadas (`NESTED TABLE`)** — colecciones de tamaño dinámico.
- **Arrays asociativos (`INDEX BY TABLE`)** — colecciones clave-valor, solo en memoria.

Los **cursores** resuelven uno de los problemas más habituales en PL/SQL: las consultas SQL que devuelven **múltiples filas**. Una variable normal solo puede almacenar un valor; un cursor permite recorrer fila a fila el resultado de una `SELECT`.

---

## 2. Esquema de base de datos de referencia

Para que todos los ejemplos de esta unidad sean coherentes y fáciles de relacionar entre sí, trabajaremos siempre sobre el mismo esquema de base de datos: una **empresa de desarrollo de software** con empleados, departamentos y proyectos.

Ejecuta los siguientes scripts en Oracle SQL Developer para crear y poblar las tablas antes de practicar los ejemplos.

```sql
-- ============================================================
-- CREACIÓN DE TABLAS
-- ============================================================

CREATE TABLE DEPARTAMENTOS (
    id_dpto    NUMBER(3)     CONSTRAINT pk_dpto PRIMARY KEY,
    nombre     VARCHAR2(50)  NOT NULL,
    ciudad     VARCHAR2(50)  NOT NULL,
    presupuesto NUMBER(10,2) DEFAULT 0
);

CREATE TABLE EMPLEADOS (
    id_emp      NUMBER(5)     CONSTRAINT pk_emp PRIMARY KEY,
    nombre      VARCHAR2(50)  NOT NULL,
    apellidos   VARCHAR2(100) NOT NULL,
    puesto      VARCHAR2(50),
    salario     NUMBER(8,2)   NOT NULL,
    fecha_alta  DATE          DEFAULT SYSDATE,
    id_dpto     NUMBER(3)     CONSTRAINT fk_emp_dpto
                              REFERENCES DEPARTAMENTOS(id_dpto)
);

CREATE TABLE PROYECTOS (
    id_proyecto NUMBER(4)     CONSTRAINT pk_proyecto PRIMARY KEY,
    nombre      VARCHAR2(100) NOT NULL,
    presupuesto NUMBER(10,2)  NOT NULL,
    fecha_inicio DATE,
    id_dpto     NUMBER(3)     CONSTRAINT fk_proy_dpto
                              REFERENCES DEPARTAMENTOS(id_dpto)
);

CREATE TABLE ASIGNACIONES (
    id_emp      NUMBER(5) REFERENCES EMPLEADOS(id_emp),
    id_proyecto NUMBER(4) REFERENCES PROYECTOS(id_proyecto),
    horas       NUMBER(5) DEFAULT 0,
    CONSTRAINT pk_asig PRIMARY KEY (id_emp, id_proyecto)
);

-- ============================================================
-- DATOS DE PRUEBA
-- ============================================================

INSERT INTO DEPARTAMENTOS VALUES (10, 'Backend',         'Madrid',    150000);
INSERT INTO DEPARTAMENTOS VALUES (20, 'Frontend',        'Barcelona', 120000);
INSERT INTO DEPARTAMENTOS VALUES (30, 'Base de Datos',   'Sevilla',    90000);
INSERT INTO DEPARTAMENTOS VALUES (40, 'Ciberseguridad',  'Valencia',  200000);

INSERT INTO EMPLEADOS VALUES (1, 'Ana',     'García López',    'Desarrolladora',  35000, DATE '2020-03-15', 10);
INSERT INTO EMPLEADOS VALUES (2, 'Carlos',  'Martínez Ruiz',   'Arquitecto',      52000, DATE '2018-06-01', 10);
INSERT INTO EMPLEADOS VALUES (3, 'Lucía',   'Fernández Mora',  'Diseñadora UI',   31000, DATE '2021-09-10', 20);
INSERT INTO EMPLEADOS VALUES (4, 'David',   'Sánchez Pérez',   'DBA',             47000, DATE '2019-01-20', 30);
INSERT INTO EMPLEADOS VALUES (5, 'Elena',   'Torres Jiménez',  'Analista',        41000, DATE '2022-04-05', 30);
INSERT INTO EMPLEADOS VALUES (6, 'Pablo',   'Romero Vega',     'Pentester',       55000, DATE '2017-11-30', 40);
INSERT INTO EMPLEADOS VALUES (7, 'Sofía',   'Díaz Castro',     'Desarrolladora',  36000, DATE '2023-01-12', 20);
INSERT INTO EMPLEADOS VALUES (8, 'Marcos',  'Alonso Gil',      'DBA Senior',      60000, DATE '2015-05-22', 30);

INSERT INTO PROYECTOS VALUES (101, 'App móvil corporativa', 80000,  DATE '2024-01-01', 20);
INSERT INTO PROYECTOS VALUES (102, 'Migración a la nube',   200000, DATE '2024-03-01', 10);
INSERT INTO PROYECTOS VALUES (103, 'Auditoría de seguridad',150000, DATE '2024-02-15', 40);
INSERT INTO PROYECTOS VALUES (104, 'Rediseño BBDD',          50000, DATE '2024-04-01', 30);

INSERT INTO ASIGNACIONES VALUES (1, 102, 320);
INSERT INTO ASIGNACIONES VALUES (2, 102, 500);
INSERT INTO ASIGNACIONES VALUES (3, 101, 400);
INSERT INTO ASIGNACIONES VALUES (4, 104, 280);
INSERT INTO ASIGNACIONES VALUES (5, 104, 300);
INSERT INTO ASIGNACIONES VALUES (6, 103, 600);
INSERT INTO ASIGNACIONES VALUES (7, 101, 350);
INSERT INTO ASIGNACIONES VALUES (8, 104, 450);

COMMIT;
```

> 💡 **Consejo:** mantén estas tablas en un esquema de pruebas (p. ej. `PRACTICAS`) para no mezclarlas con otros proyectos.

---

## 3. Registros (`RECORD`)

### 3.1. Concepto y declaración

Un **registro** es una estructura que agrupa varios campos relacionados, cada uno con su propio nombre y tipo de dato. Es el equivalente en PL/SQL a una fila de una tabla: igual que una fila de `EMPLEADOS` tiene `id_emp`, `nombre`, `salario`, etc., un registro puede agrupar esos mismos datos en una única variable.

**Sintaxis de declaración:**

```sql
TYPE nombre_tipo IS RECORD (
    campo1  tipo_dato1 [NOT NULL] [:= valor_por_defecto],
    campo2  tipo_dato2 [NOT NULL] [:= valor_por_defecto],
    ...
    campoN  tipo_datoN [NOT NULL] [:= valor_por_defecto]
);
nombre_variable nombre_tipo;
```

**Ejemplo básico** — registro para almacenar datos de un empleado:

```sql
DECLARE
    -- 1. Definimos el tipo de registro
    TYPE t_empleado IS RECORD (
        id_emp    NUMBER(5),
        nombre    VARCHAR2(50),
        apellidos VARCHAR2(100),
        salario   NUMBER(8,2)
    );

    -- 2. Declaramos una variable de ese tipo
    v_emp t_empleado;

BEGIN
    -- 3. Asignamos valores campo a campo
    v_emp.id_emp    := 99;
    v_emp.nombre    := 'Test';
    v_emp.apellidos := 'Usuario';
    v_emp.salario   := 30000;

    -- 4. Mostramos el contenido
    DBMS_OUTPUT.PUT_LINE('Empleado: ' || v_emp.nombre || ' ' || v_emp.apellidos);
    DBMS_OUTPUT.PUT_LINE('Salario : ' || v_emp.salario || ' €');
END;
/
```

**Salida esperada:**
```
Empleado: Test Usuario
Salario : 30000 €
```

El acceso a los campos individuales se realiza siempre con la notación **`nombre_variable.nombre_campo`**.

---

### 3.2. Atributos `%TYPE` y `%ROWTYPE`

Escribir manualmente el tipo de cada campo es propenso a errores: si alguien cambia `VARCHAR2(50)` a `VARCHAR2(100)` en la tabla, nuestro código PL/SQL podría fallar. Oracle ofrece dos atributos para **anclar** el tipo de un campo al tipo real de la tabla:

| Atributo | Significado | Uso típico |
|---|---|---|
| `%TYPE` | Toma el tipo de dato de **una columna** concreta de una tabla | Un campo del registro |
| `%ROWTYPE` | Toma la estructura completa de **una fila** de una tabla | Todo el registro de una vez |

#### Usando `%TYPE` campo a campo

```sql
DECLARE
    TYPE t_resumen_emp IS RECORD (
        id_emp    EMPLEADOS.id_emp%TYPE,      -- NUMBER(5), igual que en la tabla
        nombre    EMPLEADOS.nombre%TYPE,      -- VARCHAR2(50)
        salario   EMPLEADOS.salario%TYPE      -- NUMBER(8,2)
    );

    v_resumen t_resumen_emp;
BEGIN
    SELECT id_emp, nombre, salario
      INTO v_resumen.id_emp, v_resumen.nombre, v_resumen.salario
      FROM EMPLEADOS
     WHERE id_emp = 4;   -- David, el DBA

    DBMS_OUTPUT.PUT_LINE('ID     : ' || v_resumen.id_emp);
    DBMS_OUTPUT.PUT_LINE('Nombre : ' || v_resumen.nombre);
    DBMS_OUTPUT.PUT_LINE('Salario: ' || v_resumen.salario || ' €');
END;
/
```

**Salida esperada:**
```
ID     : 4
Nombre : David
Salario: 47000 €
```

#### Usando `%ROWTYPE` — la forma más cómoda

Cuando necesitamos trabajar con todos (o casi todos) los campos de una tabla, `%ROWTYPE` crea automáticamente un registro con la misma estructura que una fila completa de esa tabla.

```sql
DECLARE
    -- v_emp tiene exactamente los mismos campos que una fila de EMPLEADOS:
    -- id_emp, nombre, apellidos, puesto, salario, fecha_alta, id_dpto
    v_emp EMPLEADOS%ROWTYPE;
BEGIN
    SELECT *
      INTO v_emp
      FROM EMPLEADOS
     WHERE id_emp = 6;   -- Pablo, el pentester

    DBMS_OUTPUT.PUT_LINE('--- Ficha del empleado ---');
    DBMS_OUTPUT.PUT_LINE('Nombre  : ' || v_emp.nombre || ' ' || v_emp.apellidos);
    DBMS_OUTPUT.PUT_LINE('Puesto  : ' || v_emp.puesto);
    DBMS_OUTPUT.PUT_LINE('Salario : ' || v_emp.salario || ' €');
    DBMS_OUTPUT.PUT_LINE('Dpto    : ' || v_emp.id_dpto);
    DBMS_OUTPUT.PUT_LINE('Alta    : ' || TO_CHAR(v_emp.fecha_alta, 'DD/MM/YYYY'));
END;
/
```

**Salida esperada:**
```
--- Ficha del empleado ---
Nombre  : Pablo Romero Vega
Puesto  : Pentester
Salario : 55000 €
Dpto    : 40
Alta    : 30/11/2017
```

> ⚠️ **Importante:** `%ROWTYPE` refleja la estructura de la tabla **en el momento de compilación**. Si añades una columna nueva a la tabla, deberás recompilar el bloque o procedimiento que lo usa.

---

### 3.3. Asignación y restricciones

PL/SQL permite asignar un registro completo a otro **solo si ambos son del mismo tipo declarado**. Si los tipos son distintos, aunque tengan los mismos campos con los mismos tipos, la asignación directa está prohibida y hay que hacerla campo a campo.

Tampoco es posible comparar dos registros directamente con `=` o `<>`, ni siquiera siendo del mismo tipo.

```sql
DECLARE
    TYPE t_emp_a IS RECORD (id NUMBER, nombre VARCHAR2(50));
    TYPE t_emp_b IS RECORD (id NUMBER, nombre VARCHAR2(50));

    v_a t_emp_a;
    v_b t_emp_a;   -- mismo tipo que v_a
    v_c t_emp_b;   -- tipo diferente, aunque estructura idéntica
BEGIN
    v_a.id := 1; v_a.nombre := 'Ana';

    v_b := v_a;   -- ✅ CORRECTO: mismo tipo
    -- v_c := v_a; -- ❌ ERROR ORA-06550: tipos incompatibles

    -- Para copiar a v_c hay que hacerlo campo a campo:
    v_c.id     := v_a.id;
    v_c.nombre := v_a.nombre;

    -- ❌ Comparación directa NO permitida:
    -- IF v_a = v_b THEN ...

    DBMS_OUTPUT.PUT_LINE('v_b.nombre = ' || v_b.nombre);
    DBMS_OUTPUT.PUT_LINE('v_c.nombre = ' || v_c.nombre);
END;
/
```

---

### 3.4. Ejemplos completos

#### Ejemplo A — Insertar usando un registro

```sql
DECLARE
    v_dpto DEPARTAMENTOS%ROWTYPE;
BEGIN
    v_dpto.id_dpto      := 50;
    v_dpto.nombre       := 'DevOps';
    v_dpto.ciudad       := 'Bilbao';
    v_dpto.presupuesto  := 175000;

    INSERT INTO DEPARTAMENTOS VALUES v_dpto;
    COMMIT;

    DBMS_OUTPUT.PUT_LINE('Departamento ' || v_dpto.nombre || ' insertado correctamente.');
END;
/
```

> 💡 Fíjate en que `INSERT INTO DEPARTAMENTOS VALUES v_dpto` usa directamente el registro, sin enumerar columnas. Esto solo funciona cuando el registro es de tipo `%ROWTYPE` de esa misma tabla y cuando todos los campos del registro coinciden en orden y número con las columnas de la tabla.

#### Ejemplo B — Leer, modificar y actualizar con un registro

```sql
DECLARE
    v_emp EMPLEADOS%ROWTYPE;
BEGIN
    -- 1. Leemos los datos actuales del empleado 5
    SELECT * INTO v_emp FROM EMPLEADOS WHERE id_emp = 5;

    DBMS_OUTPUT.PUT_LINE('Salario anterior de ' || v_emp.nombre || ': ' || v_emp.salario || ' €');

    -- 2. Aplicamos un incremento del 8% al salario
    v_emp.salario := v_emp.salario * 1.08;

    -- 3. Actualizamos la tabla
    UPDATE EMPLEADOS
       SET salario = v_emp.salario
     WHERE id_emp  = v_emp.id_emp;

    COMMIT;

    DBMS_OUTPUT.PUT_LINE('Nuevo salario de ' || v_emp.nombre || ': ' || ROUND(v_emp.salario, 2) || ' €');
END;
/
```

**Salida esperada:**
```
Salario anterior de Elena: 41000 €
Nuevo salario de Elena: 44280 €
```

---

## 4. Arrays (`VARRAY`)

### 4.1. Concepto y declaración

Un **array** (o `VARRAY`, de *Variable-size ARRAY*) es una colección ordenada de elementos **del mismo tipo** con un **tamaño máximo fijo** que se define en el momento de su creación. Los elementos se acceden por su posición numérica (índice), que en Oracle **empieza en 1** (no en 0 como en Java o Python).

**Sintaxis de declaración:**

```sql
TYPE nombre_tipo IS VARRAY(capacidad_maxima) OF tipo_elemento [NOT NULL];
nombre_variable nombre_tipo;
```

Los tipos de elementos pueden ser cualquier tipo escalar de PL/SQL (`NUMBER`, `VARCHAR2`, `DATE`, `BOOLEAN`, tipos de objetos…), excepto `LONG`, `LONG RAW` y algunos tipos obsoletos.

**Ejemplo de declaración:**

```sql
-- Array de hasta 10 cadenas de texto
TYPE t_lista_nombres IS VARRAY(10) OF VARCHAR2(100);

-- Array de hasta 5 números
TYPE t_lista_salarios IS VARRAY(5) OF NUMBER(8,2);

-- Array de hasta 3 filas de EMPLEADOS (usando %ROWTYPE)
TYPE t_lista_empleados IS VARRAY(3) OF EMPLEADOS%ROWTYPE;
```

---

### 4.2. Inicialización y acceso

Un `VARRAY` **nace vacío**. Para poder usarlo hay que inicializarlo con el constructor del tipo (que tiene el mismo nombre que el tipo), pasando los valores iniciales entre paréntesis.

```sql
DECLARE
    TYPE t_ciudades IS VARRAY(6) OF VARCHAR2(50);
    v_ciudades t_ciudades;  -- En este punto está vacío (NULL)
BEGIN
    -- Inicialización con constructor: añade 3 elementos
    v_ciudades := t_ciudades('Madrid', 'Barcelona', 'Sevilla');

    -- Acceso por índice (comienza en 1)
    DBMS_OUTPUT.PUT_LINE('Primera ciudad : ' || v_ciudades(1));
    DBMS_OUTPUT.PUT_LINE('Segunda ciudad : ' || v_ciudades(2));
    DBMS_OUTPUT.PUT_LINE('Tercera ciudad : ' || v_ciudades(3));
END;
/
```

**Salida esperada:**
```
Primera ciudad : Madrid
Segunda ciudad : Barcelona
Tercera ciudad : Sevilla
```

Para añadir nuevos elementos tras la inicialización, es necesario primero extender el array con `EXTEND` (ver siguiente apartado).

---

### 4.3. Métodos disponibles

Los métodos de colección en PL/SQL se invocan con la notación `nombre_variable.METODO(argumentos)`.

| Método | Descripción | Ejemplo |
|---|---|---|
| `COUNT` | Número de elementos actuales | `v_arr.COUNT` → `3` |
| `LIMIT` | Capacidad máxima declarada | `v_arr.LIMIT` → `10` |
| `FIRST` | Índice del primer elemento | `v_arr.FIRST` → `1` |
| `LAST` | Índice del último elemento | `v_arr.LAST` → `3` |
| `PRIOR(i)` | Índice anterior a `i` | `v_arr.PRIOR(3)` → `2` |
| `NEXT(i)` | Índice siguiente a `i` | `v_arr.NEXT(1)` → `2` |
| `EXTEND` | Añade 1 elemento nulo al final | `v_arr.EXTEND` |
| `EXTEND(n)` | Añade `n` elementos nulos al final | `v_arr.EXTEND(3)` |
| `EXTEND(n,i)` | Añade `n` copias del elemento en posición `i` | `v_arr.EXTEND(2,1)` |
| `TRIM` | Elimina el último elemento | `v_arr.TRIM` |
| `TRIM(n)` | Elimina los últimos `n` elementos | `v_arr.TRIM(2)` |
| `DELETE` | Vacía completamente el array | `v_arr.DELETE` |

> ⚠️ `VARRAY` no permite `DELETE(i)` sobre un índice concreto (eso sí está disponible en tablas anidadas). Para modificar un elemento simplemente reasigna: `v_arr(2) := nuevo_valor`.

---

### 4.4. Ejemplos completos

#### Ejemplo A — Recorrer puestos de trabajo con un VARRAY

```sql
DECLARE
    TYPE t_puestos IS VARRAY(10) OF VARCHAR2(50);
    v_puestos t_puestos := t_puestos('Desarrolladora', 'Arquitecto', 'DBA', 'Pentester', 'Diseñadora UI');
    i         NUMBER;
BEGIN
    DBMS_OUTPUT.PUT_LINE('=== Listado de puestos (' || v_puestos.COUNT || ' en total) ===');

    -- Recorremos con FIRST..LAST para mayor robustez
    i := v_puestos.FIRST;
    WHILE i <= v_puestos.LAST LOOP
        DBMS_OUTPUT.PUT_LINE('  [' || i || '] ' || v_puestos(i));
        i := v_puestos.NEXT(i);
    END LOOP;
END;
/
```

**Salida esperada:**
```
=== Listado de puestos (5 en total) ===
  [1] Desarrolladora
  [2] Arquitecto
  [3] DBA
  [4] Pentester
  [5] Diseñadora UI
```

#### Ejemplo B — Usar EXTEND para añadir elementos dinámicamente

```sql
DECLARE
    TYPE t_salarios IS VARRAY(10) OF NUMBER(8,2);
    v_salarios t_salarios := t_salarios();   -- Inicializado vacío
    v_total    NUMBER := 0;
BEGIN
    -- Añadimos los salarios del departamento 30 (Base de Datos)
    FOR r IN (SELECT salario FROM EMPLEADOS WHERE id_dpto = 30 ORDER BY id_emp) LOOP
        v_salarios.EXTEND;                        -- Hacemos sitio para un elemento más
        v_salarios(v_salarios.COUNT) := r.salario; -- Lo asignamos al último hueco
    END LOOP;

    DBMS_OUTPUT.PUT_LINE('Salarios del dpto. Base de Datos:');
    FOR i IN 1 .. v_salarios.COUNT LOOP
        DBMS_OUTPUT.PUT_LINE('  ' || v_salarios(i) || ' €');
        v_total := v_total + v_salarios(i);
    END LOOP;
    DBMS_OUTPUT.PUT_LINE('  ----------------------');
    DBMS_OUTPUT.PUT_LINE('  Total  : ' || v_total || ' €');
    DBMS_OUTPUT.PUT_LINE('  Media  : ' || ROUND(v_total / v_salarios.COUNT, 2) || ' €');
    DBMS_OUTPUT.PUT_LINE('Capacidad máxima del array: ' || v_salarios.LIMIT);
END;
/
```

**Salida esperada:**
```
Salarios del dpto. Base de Datos:
  47000 €
  41000 €
  60000 €
  ----------------------
  Total  : 148000 €
  Media  : 49333,33 €
Capacidad máxima del array: 10
```

#### Ejemplo C — Recorrido en orden inverso

```sql
DECLARE
    TYPE t_nombres IS VARRAY(6) OF VARCHAR2(50);
    -- Ciudades en orden alfabético
    v_nombres t_nombres := t_nombres('Alicante', 'Barcelona', 'Bilbao',
                                     'Madrid', 'Sevilla', 'Valencia');
BEGIN
    DBMS_OUTPUT.PUT_LINE('=== Ciudades en orden INVERSO ===');
    -- FOR i IN REVERSE recorre los índices de mayor a menor
    FOR i IN REVERSE v_nombres.FIRST .. v_nombres.LAST LOOP
        DBMS_OUTPUT.PUT_LINE('  ' || v_nombres(i));
    END LOOP;
END;
/
```

**Salida esperada:**
```
=== Ciudades en orden INVERSO ===
  Valencia
  Sevilla
  Madrid
  Bilbao
  Barcelona
  Alicante
```

---

## 5. Arrays asociativos (`INDEX BY TABLE`)

### 5.1. Concepto y características

Los **arrays asociativos** (también llamados *index-by tables* o *tablas PL/SQL*) son colecciones de pares **clave → valor**. A diferencia de los `VARRAY`, las claves no tienen por qué ser números correlativos: pueden ser cadenas de texto o enteros arbitrarios.

Sus características principales:

| Característica | Detalle |
|---|---|
| **Tamaño** | Ilimitado y dinámico (no hay `LIMIT`) |
| **Claves** | `PLS_INTEGER`, `BINARY_INTEGER` o `VARCHAR2(n)` |
| **Valores** | Cualquier tipo escalar o `%ROWTYPE` |
| **Persistencia** | Solo existen en memoria (no se pueden guardar en tablas de la BD directamente) |
| **Uso ideal** | Búsquedas rápidas tipo diccionario / caché en memoria |

---

### 5.2. Declaración, inicialización y acceso

**Sintaxis:**

```sql
TYPE nombre_tipo IS TABLE OF tipo_valor INDEX BY tipo_clave;
nombre_variable nombre_tipo;
```

Donde `tipo_clave` puede ser `PLS_INTEGER`, `BINARY_INTEGER` o `VARCHAR2(n)`.

Los arrays asociativos **no tienen constructor**, se inicializan asignando valores directamente:

```sql
nombre_variable(clave) := valor;
```

Y se accede a un valor con:

```sql
nombre_variable(clave)
```

---

### 5.3. Métodos disponibles

| Método | Descripción |
|---|---|
| `COUNT` | Número de pares clave-valor almacenados |
| `EXISTS(c)` | `TRUE` si existe la clave `c`, `FALSE` en caso contrario |
| `FIRST` | Primera clave (la más pequeña si es numérica; la primera alfabéticamente si es `VARCHAR2`) |
| `LAST` | Última clave |
| `NEXT(c)` | Clave siguiente a `c` |
| `PRIOR(c)` | Clave anterior a `c` |
| `DELETE` | Elimina todos los elementos |
| `DELETE(c)` | Elimina el par con clave `c` |
| `DELETE(c1,c2)` | Elimina todos los pares entre claves `c1` y `c2` |

> ⚠️ `LIMIT` **no existe** en arrays asociativos (tampoco en tablas anidadas), ya que no tienen tamaño máximo.

---

### 5.4. Ejemplos completos

#### Ejemplo A — Diccionario de ciudades y presupuestos (clave VARCHAR2)

```sql
DECLARE
    -- Tabla asociativa: ciudad → presupuesto del departamento
    TYPE t_presupuestos IS TABLE OF NUMBER(10,2) INDEX BY VARCHAR2(50);
    v_presup t_presupuestos;
    v_ciudad VARCHAR2(50);
BEGIN
    -- Cargamos los datos desde la tabla DEPARTAMENTOS
    FOR r IN (SELECT ciudad, presupuesto FROM DEPARTAMENTOS) LOOP
        v_presup(r.ciudad) := r.presupuesto;
    END LOOP;

    -- Acceso directo por clave (como un diccionario)
    DBMS_OUTPUT.PUT_LINE('Presupuesto de Madrid  : ' || v_presup('Madrid')    || ' €');
    DBMS_OUTPUT.PUT_LINE('Presupuesto de Sevilla : ' || v_presup('Sevilla')   || ' €');
    DBMS_OUTPUT.PUT_LINE('Presupuesto de Valencia: ' || v_presup('Valencia')  || ' €');

    -- Recorremos todas las ciudades
    DBMS_OUTPUT.PUT_LINE('--- Todos los departamentos ---');
    v_ciudad := v_presup.FIRST;
    WHILE v_ciudad IS NOT NULL LOOP
        DBMS_OUTPUT.PUT_LINE('  ' || v_ciudad || ': ' || v_presup(v_ciudad) || ' €');
        v_ciudad := v_presup.NEXT(v_ciudad);
    END LOOP;
END;
/
```

**Salida esperada:**
```
Presupuesto de Madrid  : 150000 €
Presupuesto de Sevilla : 90000 €
Presupuesto de Valencia: 200000 €
--- Todos los departamentos ---
  Barcelona: 120000 €
  Madrid: 150000 €
  Sevilla: 90000 €
  Valencia: 200000 €
```

> 💡 Observa que el recorrido con `VARCHAR2` como clave devuelve los elementos en **orden alfabético ascendente**.

#### Ejemplo B — Caché de salarios por id_emp (clave numérica)

```sql
DECLARE
    -- Tabla asociativa: id_emp → salario
    TYPE t_cache_salarios IS TABLE OF EMPLEADOS.salario%TYPE INDEX BY PLS_INTEGER;
    v_cache t_cache_salarios;
    v_id    PLS_INTEGER;
BEGIN
    -- Cargamos la caché
    FOR r IN (SELECT id_emp, salario FROM EMPLEADOS) LOOP
        v_cache(r.id_emp) := r.salario;
    END LOOP;

    -- Consulta rápida sin tocar la BD
    DBMS_OUTPUT.PUT_LINE('Salario empleado 2 (Carlos)  : ' || v_cache(2) || ' €');
    DBMS_OUTPUT.PUT_LINE('Salario empleado 6 (Pablo)   : ' || v_cache(6) || ' €');

    -- Comprobamos si existe un id antes de acceder (buena práctica)
    IF v_cache.EXISTS(99) THEN
        DBMS_OUTPUT.PUT_LINE('Empleado 99: ' || v_cache(99));
    ELSE
        DBMS_OUTPUT.PUT_LINE('El empleado 99 no existe en la caché.');
    END IF;

    -- Eliminamos un elemento de la caché
    v_cache.DELETE(2);
    DBMS_OUTPUT.PUT_LINE('Tras borrar id 2, la caché tiene ' || v_cache.COUNT || ' elementos.');
END;
/
```

**Salida esperada:**
```
Salario empleado 2 (Carlos)  : 52000 €
Salario empleado 6 (Pablo)   : 55000 €
El empleado 99 no existe en la caché.
Tras borrar id 2, la caché tiene 7 elementos.
```

---

## 6. Tablas anidadas (`NESTED TABLE`)

### 6.1. Concepto y diferencias con `VARRAY`

Las **tablas anidadas** son colecciones ordenadas, sin límite de tamaño, que pueden contener elementos del mismo tipo. Son similares a los `VARRAY` pero con dos diferencias clave:

| Característica | `VARRAY` | `NESTED TABLE` |
|---|---|---|
| Tamaño máximo | Fijo (declarado) | Dinámico (ilimitado) |
| Índices | Siempre correlativos | Pueden tener "huecos" al borrar |
| `DELETE(i)` sobre índice | ❌ No permitido | ✅ Permitido |
| Método `LIMIT` | ✅ Devuelve capacidad máx. | ❌ No existe |
| Método `TRIM` | ✅ Disponible | ✅ Disponible |
| Almacenable en tabla BD | Limitado | Sí (con `STORE AS`) |

---

### 6.2. Declaración, inicialización y acceso

**Sintaxis:**

```sql
TYPE nombre_tipo IS TABLE OF tipo_elemento [NOT NULL];
nombre_variable nombre_tipo;
```

La inicialización y el acceso son iguales que en `VARRAY`:

```sql
-- Inicialización con constructor
v_tabla := nombre_tipo(valor1, valor2, valor3);

-- Acceso por índice
v_tabla(2)  -- segundo elemento
```

---

### 6.3. Métodos disponibles

Todos los de `VARRAY` más:

| Método | Descripción |
|---|---|
| `TRIM(n)` | Elimina los últimos `n` elementos (o 1 si no se especifica) |
| `DELETE` | Elimina **todos** los elementos |
| `DELETE(i)` | Elimina el elemento en la posición `i` (deja un "hueco") |
| `DELETE(i,j)` | Elimina los elementos desde `i` hasta `j` |

> ⚠️ Tras un `DELETE(i)`, el índice `i` deja de existir. Por eso es imprescindible usar `EXISTS(i)` antes de acceder a un elemento cuando puede haber habido borrados.

---

### 6.4. Ejemplos completos

#### Ejemplo A — Gestión de una lista de proyectos

```sql
DECLARE
    TYPE t_lista_proyectos IS TABLE OF VARCHAR2(100);
    v_proyectos t_lista_proyectos := t_lista_proyectos(
        'App móvil corporativa',
        'Migración a la nube',
        'Auditoría de seguridad',
        'Rediseño BBDD'
    );
    i NUMBER;
BEGIN
    DBMS_OUTPUT.PUT_LINE('Proyectos iniciales: ' || v_proyectos.COUNT);

    -- Añadimos un proyecto nuevo
    v_proyectos.EXTEND;
    v_proyectos(v_proyectos.LAST) := 'Portal del empleado';
    DBMS_OUTPUT.PUT_LINE('Tras EXTEND: ' || v_proyectos.COUNT || ' proyectos');

    -- Eliminamos el segundo proyecto (por índice)
    v_proyectos.DELETE(2);
    DBMS_OUTPUT.PUT_LINE('Tras DELETE(2): ' || v_proyectos.COUNT || ' proyectos');

    -- Recorremos con EXISTS para evitar errores por huecos
    DBMS_OUTPUT.PUT_LINE('--- Lista final de proyectos ---');
    i := v_proyectos.FIRST;
    WHILE i IS NOT NULL LOOP
        IF v_proyectos.EXISTS(i) THEN
            DBMS_OUTPUT.PUT_LINE('  [' || i || '] ' || v_proyectos(i));
        END IF;
        i := v_proyectos.NEXT(i);
    END LOOP;
END;
/
```

**Salida esperada:**
```
Proyectos iniciales: 4
Tras EXTEND: 5 proyectos
Tras DELETE(2): 4 proyectos
--- Lista final de proyectos ---
  [1] App móvil corporativa
  [3] Auditoría de seguridad
  [4] Rediseño BBDD
  [5] Portal del empleado
```

> 💡 Observa que el índice `[2]` ya no aparece porque fue borrado con `DELETE(2)`. Los demás índices **no se reordenan**, por eso hay que usar `EXISTS` o iterar con `NEXT`.

#### Ejemplo B — Acumular nombres de empleados de un departamento

```sql
DECLARE
    TYPE t_nombres IS TABLE OF VARCHAR2(150);
    v_nombres t_nombres := t_nombres();
BEGIN
    -- Llenamos la tabla anidada con empleados del dpto. 30
    FOR r IN (SELECT nombre || ' ' || apellidos AS nombre_completo
                FROM EMPLEADOS
               WHERE id_dpto = 30
               ORDER BY nombre) LOOP
        v_nombres.EXTEND;
        v_nombres(v_nombres.LAST) := r.nombre_completo;
    END LOOP;

    DBMS_OUTPUT.PUT_LINE('Empleados del departamento "Base de Datos" (' || v_nombres.COUNT || '):');
    FOR i IN v_nombres.FIRST .. v_nombres.LAST LOOP
        DBMS_OUTPUT.PUT_LINE('  · ' || v_nombres(i));
    END LOOP;

    -- Eliminamos los dos últimos con TRIM
    v_nombres.TRIM(2);
    DBMS_OUTPUT.PUT_LINE('Tras TRIM(2): quedan ' || v_nombres.COUNT || ' elemento(s).');
END;
/
```

**Salida esperada:**
```
Empleados del departamento "Base de Datos" (3):
  · David Sánchez Pérez
  · Elena Torres Jiménez
  · Marcos Alonso Gil
Tras TRIM(2): quedan 1 elemento(s).
```

---

## 7. Comparativa de colecciones

| | `VARRAY` | `NESTED TABLE` | Array asociativo |
|---|:---:|:---:|:---:|
| Tamaño máximo fijo | ✅ | ❌ | ❌ |
| Orden garantizado | ✅ | ✅ | ✅ (por clave) |
| Clave personalizada (`VARCHAR2`) | ❌ | ❌ | ✅ |
| `DELETE` por índice | ❌ | ✅ | ✅ |
| Método `LIMIT` | ✅ | ❌ | ❌ |
| Almacenable en BD | Limitado | ✅ | ❌ |
| Huecos tras `DELETE` | No hay | Puede haberlos | Puede haberlos |
| Inicialización con constructor | ✅ | ✅ | ❌ (asignación directa) |

---

## 8. Cursores

### 8.1. Concepto

Cada vez que PL/SQL ejecuta una consulta SQL, Oracle reserva en memoria un área de trabajo privada llamada **área de contexto** (*context area*). El **cursor** es el puntero que nos da acceso a esa área y a los resultados que contiene.

Cuando una consulta `SELECT` devuelve **una sola fila**, podemos usar `SELECT ... INTO variable` directamente. Pero cuando la consulta devuelve **varias filas**, necesitamos un cursor para recorrerlas una a una.

Una forma intuitiva de imaginarlo: si la consulta es una lista de la compra, el cursor es tu dedo que va señalando cada artículo de la lista, uno tras otro, en orden.

```
Resultado de SELECT salario FROM EMPLEADOS WHERE id_dpto = 30:
┌──────────┐
│  47000   │  ← FETCH 1 (apunta aquí primero)
│  41000   │  ← FETCH 2
│  60000   │  ← FETCH 3
└──────────┘
              ← %NOTFOUND = TRUE (ya no hay más filas)
```

---

### 8.2. Propiedades de los cursores

Los cursores exponen cuatro atributos que permiten conocer su estado en cada momento:

| Atributo | Tipo devuelto | Descripción |
|---|---|---|
| `%FOUND` | `BOOLEAN` | `TRUE` si el último `FETCH` devolvió una fila |
| `%NOTFOUND` | `BOOLEAN` | `TRUE` si el último `FETCH` no devolvió ninguna fila (fin del resultado) |
| `%ISOPEN` | `BOOLEAN` | `TRUE` si el cursor está abierto actualmente |
| `%ROWCOUNT` | `NUMBER` | Número de filas procesadas por `FETCH` hasta el momento |

Estos atributos se usan como `nombre_cursor%ATRIBUTO` para cursores explícitos, y como `SQL%ATRIBUTO` para cursores implícitos.

---

## 9. Tipos de cursores

### 9.1. Cursores implícitos

Oracle crea automáticamente un cursor implícito cada vez que se ejecuta una sentencia `SELECT INTO`, `INSERT`, `UPDATE` o `DELETE`. Este cursor se llama siempre **`SQL`** y sus atributos reflejan el resultado de la última sentencia DML o `SELECT INTO` ejecutada.

**Ejemplo — comprobar si un UPDATE afectó a alguna fila:**

```sql
BEGIN
    -- Aumentamos un 10% el salario a todos los empleados de Ciberseguridad
    UPDATE EMPLEADOS
       SET salario = salario * 1.10
     WHERE id_dpto = 40;

    -- Comprobamos el resultado usando el cursor implícito SQL
    IF SQL%FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Actualización correcta. Filas modificadas: ' || SQL%ROWCOUNT);
    ELSE
        DBMS_OUTPUT.PUT_LINE('Ningún empleado en ese departamento.');
    END IF;

    COMMIT;
END;
/
```

**Salida esperada:**
```
Actualización correcta. Filas modificadas: 1
```

**Ejemplo — comprobar un DELETE:**

```sql
BEGIN
    DELETE FROM ASIGNACIONES WHERE id_emp = 99;   -- id inexistente

    IF SQL%NOTFOUND THEN
        DBMS_OUTPUT.PUT_LINE('No se encontró ninguna asignación para el empleado 99.');
    END IF;
END;
/
```

> ⚠️ `SQL%ISOPEN` siempre devuelve `FALSE` para cursores implícitos, porque Oracle los abre y cierra automáticamente en cada sentencia.

---

### 9.2. Cursores explícitos

Los cursores explícitos son definidos y gestionados completamente por el programador. Se usan cuando una `SELECT` devuelve varias filas y necesitamos procesarlas una a una.

**Se declaran en la sección `DECLARE`:**

```sql
DECLARE
    CURSOR nombre_cursor IS
        sentencia_SELECT;
```

**Ejemplo — cursor sobre todos los empleados del departamento de Backend:**

```sql
DECLARE
    CURSOR cur_backend IS
        SELECT e.id_emp,
               e.nombre || ' ' || e.apellidos AS nombre_completo,
               e.salario
          FROM EMPLEADOS e
         WHERE e.id_dpto = 10
         ORDER BY e.salario DESC;

    -- Variable para recibir los datos del FETCH
    -- Usamos %ROWTYPE del cursor para mayor comodidad
    v_fila cur_backend%ROWTYPE;
BEGIN
    OPEN cur_backend;

    LOOP
        FETCH cur_backend INTO v_fila;
        EXIT WHEN cur_backend%NOTFOUND;

        DBMS_OUTPUT.PUT_LINE(
            RPAD(v_fila.nombre_completo, 30) || ' → ' || v_fila.salario || ' €'
        );
    END LOOP;

    DBMS_OUTPUT.PUT_LINE('Total de empleados procesados: ' || cur_backend%ROWCOUNT);
    CLOSE cur_backend;
END;
/
```

**Salida esperada:**
```
Carlos Martínez Ruiz           → 52000 €
Ana García López               → 35000 €
Total de empleados procesados: 2
```

---

### 9.3. Cursores explícitos con parámetros

Los parámetros permiten reutilizar el mismo cursor con diferentes valores de filtrado, como si fuera una consulta parametrizada.

**Sintaxis:**

```sql
CURSOR nombre_cursor (parametro1 tipo1, parametro2 tipo2, ...) IS
    SELECT ...
     WHERE columna = parametro1
       AND otra_columna = parametro2;
```

> ⚠️ En Oracle, los tipos de los parámetros de un cursor **no llevan precisión** (no se escribe `VARCHAR2(50)`, sino simplemente `VARCHAR2`).

**Ejemplo — cursor parametrizado por departamento y salario mínimo:**

```sql
DECLARE
    CURSOR cur_emp_filtrado (p_id_dpto NUMBER, p_salario_min NUMBER) IS
        SELECT nombre, apellidos, salario, puesto
          FROM EMPLEADOS
         WHERE id_dpto    = p_id_dpto
           AND salario    >= p_salario_min
         ORDER BY salario DESC;

    v_fila cur_emp_filtrado%ROWTYPE;
BEGIN
    DBMS_OUTPUT.PUT_LINE('=== Empleados del dpto 30 con salario >= 45000 € ===');
    OPEN cur_emp_filtrado(30, 45000);   -- Pasamos los parámetros al abrir

    LOOP
        FETCH cur_emp_filtrado INTO v_fila;
        EXIT WHEN cur_emp_filtrado%NOTFOUND;

        DBMS_OUTPUT.PUT_LINE(
            v_fila.nombre || ' ' || v_fila.apellidos ||
            ' (' || v_fila.puesto || '): ' || v_fila.salario || ' €'
        );
    END LOOP;

    IF cur_emp_filtrado%ROWCOUNT = 0 THEN
        DBMS_OUTPUT.PUT_LINE('No se encontraron empleados con esos criterios.');
    END IF;

    CLOSE cur_emp_filtrado;
END;
/
```

**Salida esperada:**
```
=== Empleados del dpto 30 con salario >= 45000 € ===
Marcos Alonso Gil (DBA Senior): 60000 €
David Sánchez Pérez (DBA): 47000 €
```

---

### 9.4. Cursores con `FOR LOOP` (cursor loop)

Oracle ofrece una sintaxis abreviada muy elegante que elimina la necesidad de `OPEN`, `FETCH` y `CLOSE` explícitos: el **cursor `FOR` loop**. En cada iteración del bucle, la variable de registro se carga automáticamente con la siguiente fila.

**Sintaxis con cursor declarado:**

```sql
FOR variable_registro IN nombre_cursor LOOP
    -- Usa variable_registro.campo
END LOOP;
```

**Sintaxis inline (sin declarar el cursor):**

```sql
FOR variable_registro IN (SELECT ...) LOOP
    -- Usa variable_registro.campo
END LOOP;
```

**Ejemplo — listar todos los empleados con su departamento:**

```sql
BEGIN
    -- Inline cursor FOR loop: no hace falta DECLARE, OPEN, FETCH, CLOSE
    FOR r IN (
        SELECT e.nombre || ' ' || e.apellidos AS nombre_completo,
               e.salario,
               d.nombre AS dpto
          FROM EMPLEADOS e
          JOIN DEPARTAMENTOS d ON e.id_dpto = d.id_dpto
         ORDER BY d.nombre, e.apellidos
    ) LOOP
        DBMS_OUTPUT.PUT_LINE(
            RPAD(r.dpto, 20) || RPAD(r.nombre_completo, 30) || r.salario || ' €'
        );
    END LOOP;
END;
/
```

**Salida esperada:**
```
Backend             Carlos Martínez Ruiz          52000 €
Backend             Ana García López              35000 €
Base de Datos       Marcos Alonso Gil             60000 €
Base de Datos       David Sánchez Pérez           47000 €
Base de Datos       Elena Torres Jiménez          41000 €
Ciberseguridad      Pablo Romero Vega             60500 €
Frontend            Sofía Díaz Castro             36000 €
Frontend            Lucía Fernández Mora          31000 €
```

> 💡 El cursor `FOR` loop es la forma **más recomendada** cuando solo necesitas leer datos. Es más conciso, menos propenso a errores (no te olvidas de cerrar el cursor) y Oracle lo optimiza internamente leyendo múltiples filas de golpe (*bulk fetch*).

---

## 10. Ciclo de vida de los cursores explícitos

Cuando usamos `OPEN`/`FETCH`/`CLOSE` explícitos (en lugar del cursor `FOR` loop), hay que respetar este ciclo de vida:

```
   DECLARE         OPEN            FETCH           CLOSE
┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐
│ Definimos│ → │ Ejecuta  │ → │ Lee una  │ → │ Libera   │
│ el cursor│   │ la SELECT│   │ fila     │   │ recursos │
└──────────┘   └──────────┘   └──────────┘   └──────────┘
                                   ↑  ↓
                               (repite hasta
                               %NOTFOUND)
```

### 10.1. Apertura: `OPEN`

`OPEN` ejecuta la consulta `SELECT` asociada al cursor, carga el resultado en el área de contexto y posiciona el puntero **antes de la primera fila**.

```sql
-- Sin parámetros
OPEN cur_backend;

-- Con parámetros
OPEN cur_emp_filtrado(30, 45000);
```

> ⚠️ Si intentas abrir un cursor que ya está abierto, Oracle lanza `ORA-06511: PL/SQL: cursor already open`. Usa `%ISOPEN` para comprobarlo:

```sql
IF NOT cur_backend%ISOPEN THEN
    OPEN cur_backend;
END IF;
```

---

### 10.2. Extracción de datos: `FETCH`

`FETCH` avanza el puntero a la siguiente fila y copia sus valores en las variables indicadas tras `INTO`. Cada llamada a `FETCH` lee **una sola fila**.

```sql
FETCH nombre_cursor INTO variable1, variable2, ...;
-- O bien con un registro:
FETCH nombre_cursor INTO v_registro;
```

Las variables deben coincidir en número, orden y tipo con las columnas del `SELECT` del cursor.

```sql
DECLARE
    CURSOR cur_proyectos IS
        SELECT id_proyecto, nombre, presupuesto FROM PROYECTOS ORDER BY presupuesto DESC;

    v_id     PROYECTOS.id_proyecto%TYPE;
    v_nombre PROYECTOS.nombre%TYPE;
    v_pres   PROYECTOS.presupuesto%TYPE;
BEGIN
    OPEN cur_proyectos;
    LOOP
        FETCH cur_proyectos INTO v_id, v_nombre, v_pres;
        EXIT WHEN cur_proyectos%NOTFOUND;

        DBMS_OUTPUT.PUT_LINE(
            '[' || v_id || '] ' || RPAD(v_nombre, 30) || v_pres || ' €'
        );
    END LOOP;
    CLOSE cur_proyectos;
END;
/
```

**Salida esperada:**
```
[102] Migración a la nube             200000 €
[103] Auditoría de seguridad          150000 €
[101] App móvil corporativa           80000 €
[104] Rediseño BBDD                   50000 €
```

---

### 10.3. Cierre: `CLOSE`

`CLOSE` libera el área de contexto y marca el cursor como cerrado. Es obligatorio cerrar los cursores cuando ya no se necesitan para evitar fugas de recursos.

```sql
CLOSE nombre_cursor;
```

Tras cerrar un cursor, intentar hacer `FETCH` sobre él provoca `ORA-01001: invalid cursor`.

---

### 10.4. Ejemplo completo del ciclo de vida

El siguiente ejemplo muestra el ciclo completo (`OPEN` → `FETCH` → `CLOSE`) con comprobación de estado mediante `%ISOPEN` y uso de `%ROWCOUNT`:

```sql
DECLARE
    CURSOR cur_asig IS
        SELECT e.nombre || ' ' || e.apellidos AS empleado,
               p.nombre                       AS proyecto,
               a.horas
          FROM ASIGNACIONES a
          JOIN EMPLEADOS    e ON a.id_emp      = e.id_emp
          JOIN PROYECTOS    p ON a.id_proyecto = p.id_proyecto
         ORDER BY a.horas DESC;

    v_fila cur_asig%ROWTYPE;
BEGIN
    -- Comprobamos que no esté ya abierto
    IF NOT cur_asig%ISOPEN THEN
        OPEN cur_asig;
        DBMS_OUTPUT.PUT_LINE('Cursor abierto. Estado %ISOPEN = ' ||
                             CASE WHEN cur_asig%ISOPEN THEN 'TRUE' ELSE 'FALSE' END);
    END IF;

    DBMS_OUTPUT.PUT_LINE(RPAD('Empleado', 30) || RPAD('Proyecto', 30) || 'Horas');
    DBMS_OUTPUT.PUT_LINE(RPAD('-', 70, '-'));

    LOOP
        FETCH cur_asig INTO v_fila;
        EXIT WHEN cur_asig%NOTFOUND;

        DBMS_OUTPUT.PUT_LINE(
            RPAD(v_fila.empleado, 30) ||
            RPAD(v_fila.proyecto, 30) ||
            v_fila.horas
        );
    END LOOP;

    DBMS_OUTPUT.PUT_LINE(RPAD('-', 70, '-'));
    DBMS_OUTPUT.PUT_LINE('Total de asignaciones leídas: ' || cur_asig%ROWCOUNT);

    CLOSE cur_asig;
    DBMS_OUTPUT.PUT_LINE('Cursor cerrado. Estado %ISOPEN = ' ||
                         CASE WHEN cur_asig%ISOPEN THEN 'TRUE' ELSE 'FALSE' END);
END;
/
```

**Salida esperada:**
```
Cursor abierto. Estado %ISOPEN = TRUE
Empleado                       Proyecto                       Horas
----------------------------------------------------------------------
Pablo Romero Vega              Auditoría de seguridad         600
Marcos Alonso Gil              Rediseño BBDD                  450
Carlos Martínez Ruiz           Migración a la nube            500
Lucía Fernández Mora           App móvil corporativa          400 (*)
...
----------------------------------------------------------------------
Total de asignaciones leídas: 8
Cursor cerrado. Estado %ISOPEN = FALSE
```

*(El orden exacto depende de los datos; la clave es el uso correcto de los atributos.)*

---

## 11. Actualización de registros con cursores (`FOR UPDATE`)

Cuando queremos **modificar** las filas que estamos recorriendo con un cursor, hay un problema potencial: entre el momento en que el cursor lee una fila y el momento en que la actualizamos, otro usuario podría haber modificado esa misma fila. Esto podría generar inconsistencias.

La solución es bloquear las filas en el momento de abrir el cursor, usando `FOR UPDATE`:

```sql
CURSOR nombre_cursor IS
    SELECT ...
      FROM ...
     WHERE ...
    FOR UPDATE [OF columna1, columna2] [NOWAIT | WAIT n];
```

- **`FOR UPDATE`**: bloquea todas las filas del resultado al abrir el cursor.
- **`OF columna`**: especifica qué columnas se van a actualizar (documentación, no restringe el bloqueo).
- **`NOWAIT`**: si las filas ya están bloqueadas por otro usuario, falla inmediatamente con `ORA-00054`.
- **`WAIT n`**: espera hasta `n` segundos antes de fallar.

Para actualizar la fila **actual** del cursor (sin necesidad de repetir el `WHERE`), usamos `WHERE CURRENT OF nombre_cursor`:

```sql
UPDATE tabla
   SET columna = nuevo_valor
 WHERE CURRENT OF nombre_cursor;
```

**Ejemplo completo — subida salarial del 5% a empleados con salario inferior a 40.000 €:**

```sql
DECLARE
    CURSOR cur_revision IS
        SELECT id_emp, nombre, apellidos, salario
          FROM EMPLEADOS
         WHERE salario < 40000
        FOR UPDATE OF salario NOWAIT;

    v_fila     cur_revision%ROWTYPE;
    v_nuevo    EMPLEADOS.salario%TYPE;
    v_contador NUMBER := 0;
BEGIN
    OPEN cur_revision;

    LOOP
        FETCH cur_revision INTO v_fila;
        EXIT WHEN cur_revision%NOTFOUND;

        v_nuevo := ROUND(v_fila.salario * 1.05, 2);

        -- Actualizamos la fila actual del cursor
        UPDATE EMPLEADOS
           SET salario = v_nuevo
         WHERE CURRENT OF cur_revision;

        DBMS_OUTPUT.PUT_LINE(
            v_fila.nombre || ' ' || v_fila.apellidos ||
            ': ' || v_fila.salario || ' € → ' || v_nuevo || ' €'
        );
        v_contador := v_contador + 1;
    END LOOP;

    CLOSE cur_revision;
    COMMIT;

    DBMS_OUTPUT.PUT_LINE('Revisión completada. Empleados actualizados: ' || v_contador);
EXCEPTION
    WHEN OTHERS THEN
        -- Si algo falla, deshacemos todos los cambios
        ROLLBACK;
        CLOSE cur_revision;
        DBMS_OUTPUT.PUT_LINE('ERROR: ' || SQLERRM);
        RAISE;
END;
/
```

**Salida esperada:**
```
Ana García López: 35000 € → 36750 €
Lucía Fernández Mora: 31000 € → 32550 €
Sofía Díaz Castro: 36000 € → 37800 €
Revisión completada. Empleados actualizados: 3
```

> 💡 Fíjate en el bloque `EXCEPTION`: cuando usamos `FOR UPDATE` con `NOWAIT` y hay un error, es imprescindible hacer `ROLLBACK` para liberar los bloqueos y `CLOSE` para liberar el cursor.

### 11.1 La pseudocolumna ROWID y su relación con la actualización

En Oracle, cada fila de una tabla tiene asignada una pseudocolumna llamada **`ROWID`**. Esta columna oculta contiene la **dirección física** de la fila en el disco (identificando de forma codificada el archivo de datos, el bloque exacto y la posición de la fila en dicho bloque). Es, con diferencia, el identificador más rápido y directo que existe para acceder a un registro específico.

**¿Para qué sirve el `ROWID` en el contexto de cursores?**

Cuando utilizamos la cláusula `WHERE CURRENT OF nombre_cursor` vista en el apartado anterior, internamente Oracle está extrayendo y utilizando el `ROWID` de esa fila para localizarla y actualizarla de manera instantánea. 

Sin embargo, hay situaciones complejas donde `WHERE CURRENT OF` no está soportado (por ejemplo, en cursores que involucran múltiples tablas). En esos casos, podemos extraer y utilizar el `ROWID` de forma explícita:

```sql
DECLARE
    -- Añadimos la pseudocolumna ROWID a la consulta
    CURSOR c_empleados IS
        SELECT id_empleado, salario, ROWID AS id_fisico
        FROM empleados
        WHERE departamento_id = 30
        FOR UPDATE;
BEGIN
    FOR r_emp IN c_empleados LOOP
        -- Actualización usando explícitamente el ROWID almacenado
        UPDATE empleados
        SET salario = r_emp.salario + 100
        WHERE ROWID = r_emp.id_fisico; 
    END LOOP;
END;
/

```

> 🚀 **Ventaja de rendimiento:** Actualizar apuntando al `ROWID` (ya sea explícitamente o de forma implícita con `WHERE CURRENT OF`) es la técnica de actualización más eficiente, ya que el motor de base de datos no necesita perder tiempo buscando la fila en la tabla ni recorriendo los índices utilizando la clave primaria. Va directo a la ubicación física en el disco.

---

## 12. Casos prácticos resueltos

### 12.1. Caso 1: Informe de empleados por departamento

**Enunciado:** Genera un informe que muestre, para cada departamento, el listado de sus empleados con nombre, puesto y salario, además del salario medio del departamento.

```sql
DECLARE
    -- Cursor 1: recorre los departamentos
    CURSOR cur_dptos IS
        SELECT id_dpto, nombre FROM DEPARTAMENTOS ORDER BY nombre;

    -- Cursor 2 parametrizado: recorre empleados de un departamento concreto
    CURSOR cur_emps (p_id_dpto NUMBER) IS
        SELECT nombre || ' ' || apellidos AS nombre_completo,
               puesto,
               salario
          FROM EMPLEADOS
         WHERE id_dpto = p_id_dpto
         ORDER BY salario DESC;

    v_dpto     cur_dptos%ROWTYPE;
    v_emp      cur_emps%ROWTYPE;
    v_suma_sal NUMBER;
    v_n_emps   NUMBER;
BEGIN
    OPEN cur_dptos;
    LOOP
        FETCH cur_dptos INTO v_dpto;
        EXIT WHEN cur_dptos%NOTFOUND;

        DBMS_OUTPUT.PUT_LINE('');
        DBMS_OUTPUT.PUT_LINE('══════════════════════════════════════════════');
        DBMS_OUTPUT.PUT_LINE(' Departamento: ' || v_dpto.nombre);
        DBMS_OUTPUT.PUT_LINE('══════════════════════════════════════════════');

        v_suma_sal := 0;
        v_n_emps   := 0;

        OPEN cur_emps(v_dpto.id_dpto);
        LOOP
            FETCH cur_emps INTO v_emp;
            EXIT WHEN cur_emps%NOTFOUND;

            DBMS_OUTPUT.PUT_LINE(
                '  · ' || RPAD(v_emp.nombre_completo, 28) ||
                RPAD(v_emp.puesto, 18) || v_emp.salario || ' €'
            );
            v_suma_sal := v_suma_sal + v_emp.salario;
            v_n_emps   := v_n_emps + 1;
        END LOOP;
        CLOSE cur_emps;

        IF v_n_emps > 0 THEN
            DBMS_OUTPUT.PUT_LINE('  ── Salario medio: ' ||
                ROUND(v_suma_sal / v_n_emps, 2) || ' €  (' || v_n_emps || ' empleados)');
        ELSE
            DBMS_OUTPUT.PUT_LINE('  (Sin empleados)');
        END IF;
    END LOOP;
    CLOSE cur_dptos;
END;
/
```

> 💡 Este ejemplo muestra el uso de **cursores anidados**: el cursor externo recorre departamentos y, para cada uno, el cursor interno recorre sus empleados. Los cursores explícitos con parámetros son ideales para este patrón.

---

### 12.2. Caso 2: Revisión salarial con cursor parametrizado

**Enunciado:** La empresa quiere aplicar aumentos salariales distintos según el departamento. Usa un cursor parametrizado para aplicar: +8% al departamento de Ciberseguridad (id 40), +5% al resto.

```sql
DECLARE
    TYPE t_ajuste IS RECORD (
        id_dpto  NUMBER(3),
        pct      NUMBER(5,2)
    );

    TYPE t_lista_ajustes IS TABLE OF t_ajuste INDEX BY PLS_INTEGER;
    v_ajustes t_lista_ajustes;

    CURSOR cur_emp_dpto (p_id_dpto NUMBER) IS
        SELECT id_emp, nombre, apellidos, salario
          FROM EMPLEADOS
         WHERE id_dpto = p_id_dpto
        FOR UPDATE OF salario NOWAIT;

    v_emp    cur_emp_dpto%ROWTYPE;
    v_nuevo  NUMBER(8,2);
    i        PLS_INTEGER;
BEGIN
    -- Definimos los ajustes por departamento
    v_ajustes(1).id_dpto := 40;  v_ajustes(1).pct := 8;
    v_ajustes(2).id_dpto := 10;  v_ajustes(2).pct := 5;
    v_ajustes(3).id_dpto := 20;  v_ajustes(3).pct := 5;
    v_ajustes(4).id_dpto := 30;  v_ajustes(4).pct := 5;

    i := v_ajustes.FIRST;
    WHILE i IS NOT NULL LOOP
        DBMS_OUTPUT.PUT_LINE('');
        DBMS_OUTPUT.PUT_LINE('Aplicando +' || v_ajustes(i).pct ||
                             '% al departamento ' || v_ajustes(i).id_dpto || ':');

        OPEN cur_emp_dpto(v_ajustes(i).id_dpto);
        LOOP
            FETCH cur_emp_dpto INTO v_emp;
            EXIT WHEN cur_emp_dpto%NOTFOUND;

            v_nuevo := ROUND(v_emp.salario * (1 + v_ajustes(i).pct / 100), 2);

            UPDATE EMPLEADOS SET salario = v_nuevo WHERE CURRENT OF cur_emp_dpto;

            DBMS_OUTPUT.PUT_LINE('  ' || v_emp.nombre || ' ' || v_emp.apellidos ||
                                 ': ' || v_emp.salario || ' → ' || v_nuevo || ' €');
        END LOOP;
        CLOSE cur_emp_dpto;

        i := v_ajustes.NEXT(i);
    END LOOP;

    COMMIT;
    DBMS_OUTPUT.PUT_LINE('');
    DBMS_OUTPUT.PUT_LINE('Revisión salarial completada y confirmada (COMMIT).');
EXCEPTION
    WHEN OTHERS THEN
        ROLLBACK;
        IF cur_emp_dpto%ISOPEN THEN CLOSE cur_emp_dpto; END IF;
        DBMS_OUTPUT.PUT_LINE('ERROR — ROLLBACK ejecutado: ' || SQLERRM);
        RAISE;
END;
/
```

---

### 12.3. Caso 3: Catálogo de habilidades con VARRAY

**Enunciado:** Cada puesto de trabajo tiene un conjunto fijo de habilidades requeridas (máximo 5). Construye un bloque que muestre el catálogo de habilidades asociado a cada puesto.

```sql
DECLARE
    TYPE t_habilidades IS VARRAY(5) OF VARCHAR2(50);

    -- Tabla asociativa: puesto → lista de habilidades
    TYPE t_catalogo IS TABLE OF t_habilidades INDEX BY VARCHAR2(50);
    v_catalogo t_catalogo;

    v_puesto VARCHAR2(50);
BEGIN
    -- Definimos las habilidades por puesto
    v_catalogo('Desarrolladora')  := t_habilidades('Java', 'Spring Boot', 'Git', 'SQL', 'REST APIs');
    v_catalogo('Arquitecto')      := t_habilidades('Microservicios', 'Cloud', 'Java', 'Kafka', 'Docker');
    v_catalogo('DBA')             := t_habilidades('Oracle', 'SQL', 'PL/SQL', 'Tuning', 'Backups');
    v_catalogo('DBA Senior')      := t_habilidades('Oracle RAC', 'Data Guard', 'Exadata', 'PL/SQL', 'RMAN');
    v_catalogo('Pentester')       := t_habilidades('Kali Linux', 'Metasploit', 'Python', 'Redes', 'OWASP');
    v_catalogo('Diseñadora UI')   := t_habilidades('Figma', 'HTML/CSS', 'UX', 'Accessibility', 'React');
    v_catalogo('Analista')        := t_habilidades('SQL', 'Excel', 'Power BI', 'Requisitos', 'UML');

    DBMS_OUTPUT.PUT_LINE('=== CATÁLOGO DE HABILIDADES POR PUESTO ===');

    -- Recorremos el catálogo por puestos
    v_puesto := v_catalogo.FIRST;
    WHILE v_puesto IS NOT NULL LOOP
        DBMS_OUTPUT.PUT_LINE('');
        DBMS_OUTPUT.PUT_LINE('▶ ' || v_puesto || ':');

        -- Mostramos las habilidades del VARRAY
        FOR j IN 1 .. v_catalogo(v_puesto).COUNT LOOP
            DBMS_OUTPUT.PUT_LINE('    [' || j || '] ' || v_catalogo(v_puesto)(j));
        END LOOP;

        v_puesto := v_catalogo.NEXT(v_puesto);
    END LOOP;
END;
/
```

**Fragmento de salida esperada:**
```
=== CATÁLOGO DE HABILIDADES POR PUESTO ===

▶ Analista:
    [1] SQL
    [2] Excel
    [3] Power BI
    [4] Requisitos
    [5] UML

▶ Arquitecto:
    [1] Microservicios
    [2] Cloud
    [3] Java
    [4] Kafka
    [5] Docker
...
```

> 💡 Este caso ilustra cómo **combinar** arrays asociativos y `VARRAY`: el diccionario externo usa el puesto como clave, y el valor de cada entrada es un `VARRAY` de habilidades.

---

## 13. Errores frecuentes y buenas prácticas

### Errores más comunes

| Error Oracle | Causa habitual | Solución |
|---|---|---|
| `ORA-01403: no data found` | `SELECT INTO` sin resultados | Usar `EXCEPTION WHEN NO_DATA_FOUND` o un cursor |
| `ORA-01422: exact fetch returns more than requested` | `SELECT INTO` devuelve más de una fila | Usar un cursor explícito o `FOR` loop |
| `ORA-06511: cursor already open` | `OPEN` sobre un cursor ya abierto | Comprobar `%ISOPEN` antes de `OPEN` |
| `ORA-01001: invalid cursor` | `FETCH` o `CLOSE` sobre un cursor cerrado | Comprobar `%ISOPEN` o usar `FOR` loop |
| `ORA-06530: Reference to uninitialized composite` | Usar un `VARRAY` o tabla anidada sin inicializar | Inicializar con el constructor: `v := tipo()` |
| `ORA-06533: Subscript beyond count` | Acceder a un índice inexistente en colección | Comprobar `COUNT` antes de acceder |
| `ORA-00054: resource busy` | `FOR UPDATE NOWAIT` con filas bloqueadas | Usar `WAIT n` o gestionar la excepción |

### Buenas prácticas

1. **Prefiere el cursor `FOR` loop** cuando solo vayas a leer datos; es más seguro y conciso.
2. **Usa `%ROWTYPE` y `%TYPE`** en lugar de tipos codificados a mano; así el código se adapta automáticamente a cambios en la tabla.
3. **Cierra siempre los cursores** al terminar, idealmente en un bloque `EXCEPTION` para garantizarlo aunque haya errores.
4. **Usa `EXISTS(i)` antes de acceder** a elementos de tablas anidadas si ha habido `DELETE(i)` previos.
5. **Haz `COMMIT` o `ROLLBACK` siempre** después de un bloque con `FOR UPDATE`; de lo contrario los bloqueos no se liberan.
6. **Evita cursores dentro de bucles** cuando es posible reemplazarlos con una única `SELECT` con `JOIN`; los cursores tienen coste de contexto.
7. **Nombra los cursores con un prefijo** (ej. `cur_`) para diferenciarlos fácilmente de otras variables.

---

## 14. Resumen visual

```
TIPOS DE DATOS COMPUESTOS
│
├── RECORD (%ROWTYPE / %TYPE)
│   ├── Agrupa campos heterogéneos
│   ├── Acceso: variable.campo
│   └── Asignación entre registros del mismo tipo
│
├── VARRAY
│   ├── Tamaño máximo fijo
│   ├── Índices correlativos (desde 1)
│   ├── Métodos: COUNT, LIMIT, FIRST, LAST, PRIOR, NEXT, EXTEND, TRIM
│   └── NO permite DELETE(i) ni huecos
│
├── NESTED TABLE
│   ├── Tamaño dinámico (sin límite)
│   ├── Permite DELETE(i) → genera huecos
│   ├── Métodos: COUNT, FIRST, LAST, PRIOR, NEXT, EXTEND, TRIM, DELETE
│   └── Usar EXISTS(i) al iterar si hubo DELETE(i)
│
└── ARRAY ASOCIATIVO (INDEX BY TABLE)
    ├── Clave-valor (clave: PLS_INTEGER o VARCHAR2)
    ├── Tamaño dinámico, solo en memoria
    ├── Métodos: COUNT, EXISTS, FIRST, LAST, NEXT, PRIOR, DELETE
    └── No tiene constructor (asignación directa)

CURSORES
│
├── IMPLÍCITOS (SQL%)
│   ├── Creados automáticamente por SELECT INTO, INSERT, UPDATE, DELETE
│   └── Atributos: SQL%FOUND, SQL%NOTFOUND, SQL%ROWCOUNT
│
└── EXPLÍCITOS
    ├── Definidos por el programador en DECLARE
    ├── Ciclo de vida: OPEN → FETCH (loop) → CLOSE
    ├── Atributos: %FOUND, %NOTFOUND, %ISOPEN, %ROWCOUNT
    ├── Con parámetros: OPEN cursor(valor)
    ├── FOR UPDATE + WHERE CURRENT OF → actualización segura
    └── Cursor FOR loop → forma más concisa (recomendada para lectura)
```

---

## 15. Ejercicios propuestos

> Resuelve los siguientes ejercicios sobre las tablas `DEPARTAMENTOS`, `EMPLEADOS`, `PROYECTOS` y `ASIGNACIONES` creadas al inicio de la unidad.

**Nivel básico**

1. Declara un registro de tipo `PROYECTOS%ROWTYPE` y cárgalo con los datos del proyecto 103 ("Auditoría de seguridad"). Muestra por pantalla su nombre, presupuesto y fecha de inicio con formato `DD/MM/YYYY`.

2. Crea un `VARRAY` de hasta 8 elementos `VARCHAR2(100)` y cárgalo con los nombres de todos los empleados del departamento de Backend. Muéstralos en orden inverso.

3. Usa un array asociativo con clave `VARCHAR2` para almacenar el número de empleados de cada departamento (clave = nombre del departamento, valor = número de empleados). Muestra el resultado de todos los departamentos.

**Nivel intermedio**

4. Escribe un bloque con un cursor explícito que liste los proyectos junto con el número de empleados asignados a cada uno y el total de horas. Usa `%ROWCOUNT` al final para mostrar cuántos proyectos se procesaron.

5. Implementa un cursor parametrizado que reciba un presupuesto mínimo y máximo de proyecto y liste los proyectos comprendidos en ese rango junto con el nombre del departamento responsable.

6. Usa una tabla anidada para acumular los nombres completos de los empleados asignados al proyecto 102 ("Migración a la nube"). Elimina con `DELETE(i)` aquellos que tengan menos de 400 horas asignadas y muestra la lista final usando `EXISTS`.

**Nivel avanzado**

7. Escribe un bloque que aplique la siguiente política salarial usando un cursor `FOR UPDATE`:
   - Empleados con salario < 35.000 €: +10%
   - Empleados con salario entre 35.000 € y 50.000 €: +6%
   - Empleados con salario > 50.000 €: +3%
   
   Al final muestra el ahorro/coste total de la revisión (suma de todos los incrementos).

8. Combina una tabla anidada con un cursor: declara una tabla anidada de registros `EMPLEADOS%ROWTYPE`. Usa un cursor para cargar en ella todos los empleados del departamento 30 ordenados por salario descendente. Luego, elimina de la tabla anidada al empleado con menor salario (el último tras el `DELETE`), y muestra el contenido final.
```
