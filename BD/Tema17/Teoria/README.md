# Unidad 17 — Subprogramas y Disparadores en PL/SQL (Oracle 21c)

## Índice

1. [Introducción y esquema de base de datos común](#1-introducción-y-esquema-de-base-de-datos-común)
2. [Procedimientos almacenados](#2-procedimientos-almacenados)
   - 2.1 [Concepto y sintaxis](#21-concepto-y-sintaxis)
   - 2.2 [Modos de parámetros: IN, OUT, IN OUT](#22-modos-de-parámetros-in-out-in-out)
   - 2.3 [Invocación de procedimientos](#23-invocación-de-procedimientos)
   - 2.4 [Eliminación y consulta de procedimientos](#24-eliminación-y-consulta-de-procedimientos)
3. [Funciones almacenadas](#3-funciones-almacenadas)
   - 3.1 [Concepto y sintaxis](#31-concepto-y-sintaxis)
   - 3.2 [Diferencias clave con los procedimientos](#32-diferencias-clave-con-los-procedimientos)
   - 3.3 [Llamadas desde SQL y limitaciones](#33-llamadas-desde-sql-y-limitaciones)
   - 3.4 [Sobrecarga y recursividad](#34-sobrecarga-y-recursividad)
4. [Paquetes](#4-paquetes)
   - 4.1 [Concepto y estructura](#41-concepto-y-estructura)
   - 4.2 [Especificación del paquete](#42-especificación-del-paquete)
   - 4.3 [Cuerpo del paquete](#43-cuerpo-del-paquete)
   - 4.4 [Uso de elementos privados](#44-uso-de-elementos-privados)
5. [Disparadores (Triggers)](#5-disparadores-triggers)
   - 5.1 [Concepto](#51-concepto)
   - 5.2 [Sintaxis general](#52-sintaxis-general)
   - 5.3 [Disparadores de instrucción vs. disparadores de fila](#53-disparadores-de-instrucción-vs-disparadores-de-fila)
   - 5.4 [Ejemplos completos paso a paso](#54-ejemplos-completos-paso-a-paso)
6. [Explotación de disparadores: restricciones, orden y gestión](#6-explotación-de-disparadores-restricciones-orden-y-gestión)
   - 6.1 [Restricciones importantes](#61-restricciones-importantes)
   - 6.2 [Orden de ejecución](#62-orden-de-ejecución)
   - 6.3 [Borrado, activación y desactivación](#63-borrado-activación-y-desactivación)
7. [Referencias NEW y OLD, y cláusulas condicionales DML](#7-referencias-new-y-old-y-cláusulas-condicionales-dml)
   - 7.1 [Referencias :NEW y :OLD](#71-referencias-new-y-old)
   - 7.2 [Cláusulas IF INSERTING, IF UPDATING, IF DELETING](#72-cláusulas-if-inserting-if-updating-if-deleting)
8. [Disparadores INSTEAD OF](#8-disparadores-instead-of)
9. [Información sobre disparadores en el catálogo](#9-información-sobre-disparadores-en-el-catálogo)
10. [Automatización de la ejecución de scripts](#10-automatización-de-la-ejecución-de-scripts)
    - 10.1 [Oracle Scheduler](#101-oracle-scheduler)
    - 10.2 [DBMS\_JOB (legado)](#102-dbms_job-legado)
    - 10.3 [Shell Scripts con SQL\*Plus y Cron](#103-shell-scripts-con-sqlplus-y-cron)
11. [APIs de acceso a bases de datos Oracle](#11-apis-de-acceso-a-bases-de-datos-oracle)
12. [Errores frecuentes y buenas prácticas](#12-errores-frecuentes-y-buenas-prácticas)
13. [Resumen y mapa conceptual](#13-resumen-y-mapa-conceptual)

---

## 1. Introducción y esquema de base de datos común

A lo largo de esta unidad utilizaremos **un único esquema de base de datos** para todos los ejemplos. Esto permite ver cómo los distintos elementos (procedimientos, funciones, paquetes y disparadores) se relacionan entre sí dentro de un sistema real y coherente.

El esquema representa la gestión de **empleados, departamentos, proyectos y auditoría** de una empresa:

```sql
-- ================================================
-- ESQUEMA DE LA EMPRESA "TechCorp"
-- Usar antes de ejecutar cualquier ejemplo
-- ================================================

CREATE TABLE DEPARTAMENTOS (
    id_departamento  NUMBER          GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    nombre           VARCHAR2(50)    NOT NULL,
    presupuesto      NUMBER(12, 2)   DEFAULT 0,
    fecha_creacion   DATE            DEFAULT SYSDATE
);

CREATE TABLE EMPLEADOS (
    id_empleado      NUMBER          GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    nombre           VARCHAR2(50)    NOT NULL,
    apellidos        VARCHAR2(100)   NOT NULL,
    email            VARCHAR2(100)   UNIQUE NOT NULL,
    salario          NUMBER(9, 2)    NOT NULL,
    fecha_alta       DATE            DEFAULT SYSDATE,
    id_departamento  NUMBER          REFERENCES DEPARTAMENTOS(id_departamento),
    activo           NUMBER(1)       DEFAULT 1 CHECK (activo IN (0, 1))
);

CREATE TABLE PROYECTOS (
    id_proyecto      NUMBER          GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    nombre           VARCHAR2(100)   NOT NULL,
    fecha_inicio     DATE,
    fecha_fin        DATE,
    presupuesto      NUMBER(12, 2),
    id_departamento  NUMBER          REFERENCES DEPARTAMENTOS(id_departamento)
);

CREATE TABLE EMPLEADOS_PROYECTOS (
    id_empleado      NUMBER          REFERENCES EMPLEADOS(id_empleado),
    id_proyecto      NUMBER          REFERENCES PROYECTOS(id_proyecto),
    rol              VARCHAR2(50),
    PRIMARY KEY (id_empleado, id_proyecto)
);

CREATE TABLE AUDITORIA_EMPLEADOS (
    id_auditoria      NUMBER          GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    id_empleado       NUMBER,
    accion            VARCHAR2(10),       -- 'INSERT', 'UPDATE', 'DELETE'
    campo_modificado  VARCHAR2(50),
    valor_anterior    VARCHAR2(200),
    valor_nuevo       VARCHAR2(200),
    usuario_bd        VARCHAR2(50)        DEFAULT USER,
    fecha_hora        TIMESTAMP           DEFAULT SYSTIMESTAMP
);
```

**Datos de prueba iniciales:**

```sql
-- Departamentos
INSERT INTO DEPARTAMENTOS (nombre, presupuesto)
VALUES ('Desarrollo', 150000);           -- id = 1
INSERT INTO DEPARTAMENTOS (nombre, presupuesto)
VALUES ('Recursos Humanos', 80000);      -- id = 2
INSERT INTO DEPARTAMENTOS (nombre, presupuesto)
VALUES ('Sistemas', 120000);             -- id = 3

-- Empleados
INSERT INTO EMPLEADOS (nombre, apellidos, email, salario, id_departamento)
VALUES ('Laura',  'García Sanz',    'l.garcia@techcorp.es',  2800, 1);
INSERT INTO EMPLEADOS (nombre, apellidos, email, salario, id_departamento)
VALUES ('Marcos', 'López Ruiz',     'm.lopez@techcorp.es',   2400, 1);
INSERT INTO EMPLEADOS (nombre, apellidos, email, salario, id_departamento)
VALUES ('Ana',    'Martínez Díaz',  'a.martinez@techcorp.es',3200, 3);
INSERT INTO EMPLEADOS (nombre, apellidos, email, salario, id_departamento)
VALUES ('Pedro',  'Fernández Gil',  'p.fernandez@techcorp.es',1950, 2);
INSERT INTO EMPLEADOS (nombre, apellidos, email, salario, id_departamento)
VALUES ('Sofía',  'Romero Vega',    's.romero@techcorp.es',  2650, 1);
COMMIT;
```

> **Nota:** Oracle 21c soporta `GENERATED ALWAYS AS IDENTITY` para autoincremento. Evitamos el uso de secuencias separadas (sintaxis previa a Oracle 12c) en todos los ejemplos.

---

## 2. Procedimientos almacenados

### 2.1 Concepto y sintaxis

Un **procedimiento almacenado** (*stored procedure*) es un bloque PL/SQL con nombre, guardado de forma permanente en la base de datos, que encapsula una lógica reutilizable. A diferencia de los bloques anónimos (Unidad 15), el procedimiento:

- Se **compila una sola vez** y se almacena en forma compilada → mejor rendimiento.
- Puede ser **invocado** desde cualquier otro bloque PL/SQL, desde SQL\*Plus o desde aplicaciones externas.
- **No devuelve un valor directamente** (a diferencia de las funciones), aunque puede devolver datos a través de parámetros `OUT`.

**Sintaxis completa en Oracle 21c:**

```sql
CREATE [OR REPLACE] PROCEDURE nombre_procedimiento
    [ (
        parametro1 [modo] tipo_dato [:= valor_defecto],
        parametro2 [modo] tipo_dato [:= valor_defecto],
        ...
    ) ]
{ IS | AS }
    -- Sección de declaraciones (equivale a DECLARE)
    variable_local tipo_dato;
    ...
BEGIN
    -- Cuerpo del procedimiento
    ...
[EXCEPTION
    WHEN nombre_excepcion THEN
        ...
]
END [nombre_procedimiento];
/
```

> **`OR REPLACE`:** Si ya existe un procedimiento con ese nombre, lo sustituye sin necesidad de borrarlo primero. Muy útil durante el desarrollo.  
> **`IS` / `AS`:** Son equivalentes; ambas introducen la sección de declaraciones locales. Por convención se suele usar `IS`.  
> **La barra `/` al final:** Necesaria en SQL\*Plus y Oracle SQL Developer para ejecutar bloques de código PL/SQL completos.

---

### 2.2 Modos de parámetros: IN, OUT, IN OUT

Cada parámetro de un procedimiento (o función) puede tener uno de estos tres modos:

| Modo      | Descripción                                                                    | Puede leer | Puede modificar |
|-----------|--------------------------------------------------------------------------------|:----------:|:---------------:|
| `IN`      | El procedimiento recibe un valor del exterior (solo lectura dentro). **Por defecto.** | ✅ | ❌ |
| `OUT`     | El procedimiento envía un valor al exterior (no tiene valor de entrada útil).  | ❌ | ✅ |
| `IN OUT`  | Ambas cosas: entra con un valor y puede modificarlo para el llamador.          | ✅ | ✅ |

**Ejemplo 1 — solo parámetros `IN`: mostrar datos de un empleado**

```sql
CREATE OR REPLACE PROCEDURE mostrar_empleado (
    p_id_empleado IN EMPLEADOS.id_empleado%TYPE
)
IS
    v_nombre    EMPLEADOS.nombre%TYPE;
    v_apellidos EMPLEADOS.apellidos%TYPE;
    v_salario   EMPLEADOS.salario%TYPE;
    v_depto     DEPARTAMENTOS.nombre%TYPE;
BEGIN
    SELECT e.nombre, e.apellidos, e.salario, d.nombre
    INTO   v_nombre, v_apellidos, v_salario, v_depto
    FROM   EMPLEADOS e
    JOIN   DEPARTAMENTOS d ON e.id_departamento = d.id_departamento
    WHERE  e.id_empleado = p_id_empleado;

    DBMS_OUTPUT.PUT_LINE('=== Ficha del empleado ID: ' || p_id_empleado || ' ===');
    DBMS_OUTPUT.PUT_LINE('Nombre     : ' || v_nombre || ' ' || v_apellidos);
    DBMS_OUTPUT.PUT_LINE('Salario    : ' || v_salario || ' €');
    DBMS_OUTPUT.PUT_LINE('Departamento: ' || v_depto);
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('ERROR: No existe ningún empleado con ID ' || p_id_empleado);
END mostrar_empleado;
/
```

Llamada desde un bloque anónimo:

```sql
SET SERVEROUTPUT ON
BEGIN
    mostrar_empleado(1);  -- Mostrará datos de Laura García
END;
/
```

Salida esperada:
```
=== Ficha del empleado ID: 1 ===
Nombre     : Laura García Sanz
Salario    : 2800 €
Departamento: Desarrollo
```

---

**Ejemplo 2 — parámetros `IN` y `OUT`: calcular estadísticas de un departamento**

```sql
CREATE OR REPLACE PROCEDURE estadisticas_departamento (
    p_id_departamento  IN  DEPARTAMENTOS.id_departamento%TYPE,
    p_num_empleados    OUT NUMBER,
    p_salario_medio    OUT NUMBER,
    p_salario_maximo   OUT NUMBER
)
IS
BEGIN
    SELECT COUNT(*),
           ROUND(AVG(salario), 2),
           MAX(salario)
    INTO   p_num_empleados,
           p_salario_medio,
           p_salario_maximo
    FROM   EMPLEADOS
    WHERE  id_departamento = p_id_departamento
      AND  activo = 1;
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        p_num_empleados  := 0;
        p_salario_medio  := 0;
        p_salario_maximo := 0;
END estadisticas_departamento;
/
```

Uso con variables `OUT`:

```sql
SET SERVEROUTPUT ON
DECLARE
    v_num    NUMBER;
    v_media  NUMBER;
    v_maximo NUMBER;
BEGIN
    estadisticas_departamento(
        p_id_departamento => 1,   -- Departamento de Desarrollo
        p_num_empleados   => v_num,
        p_salario_medio   => v_media,
        p_salario_maximo  => v_maximo
    );
    DBMS_OUTPUT.PUT_LINE('Empleados activos : ' || v_num);
    DBMS_OUTPUT.PUT_LINE('Salario medio     : ' || v_media || ' €');
    DBMS_OUTPUT.PUT_LINE('Salario máximo    : ' || v_maximo || ' €');
END;
/
```

Salida esperada:
```
Empleados activos : 3
Salario medio     : 2616.67 €
Salario máximo    : 2800 €
```

> **Notación con `=>`:** En Oracle se puede usar tanto la notación posicional (`estadisticas_departamento(1, v_num, v_media, v_maximo)`) como la notación por nombre (`p_id_departamento => 1, ...`). La **notación por nombre es más legible** y recomendada cuando hay muchos parámetros.

---

**Ejemplo 3 — parámetro `IN OUT`: aplicar subida de salario**

```sql
CREATE OR REPLACE PROCEDURE aplicar_subida_salarial (
    p_id_empleado IN     EMPLEADOS.id_empleado%TYPE,
    p_porcentaje  IN     NUMBER,
    p_salario     IN OUT EMPLEADOS.salario%TYPE
)
IS
BEGIN
    p_salario := ROUND(p_salario * (1 + p_porcentaje / 100), 2);

    UPDATE EMPLEADOS
    SET    salario = p_salario
    WHERE  id_empleado = p_id_empleado;

    COMMIT;
    DBMS_OUTPUT.PUT_LINE('Salario actualizado a: ' || p_salario || ' €');
END aplicar_subida_salarial;
/
```

```sql
SET SERVEROUTPUT ON
DECLARE
    v_salario EMPLEADOS.salario%TYPE := 2800;  -- salario actual de Laura
BEGIN
    -- Subida del 5%
    aplicar_subida_salarial(
        p_id_empleado => 1,
        p_porcentaje  => 5,
        p_salario     => v_salario    -- entra como 2800, sale como 2940
    );
    DBMS_OUTPUT.PUT_LINE('Nuevo salario en la variable: ' || v_salario);
END;
/
```

---

### 2.3 Invocación de procedimientos

Los procedimientos pueden invocarse de tres maneras:

**a) Desde un bloque PL/SQL anónimo** (como en los ejemplos anteriores):
```sql
BEGIN
    mostrar_empleado(3);
END;
/
```

**b) Desde SQL\*Plus o SQL Developer con `EXECUTE`:**
```sql
EXECUTE mostrar_empleado(3);
-- Forma abreviada:
EXEC mostrar_empleado(3);
```

**c) Desde otro procedimiento o función:**
```sql
CREATE OR REPLACE PROCEDURE informe_completo_empresa
IS
    CURSOR c_deptos IS
        SELECT id_departamento FROM DEPARTAMENTOS;
    v_num    NUMBER;
    v_media  NUMBER;
    v_maximo NUMBER;
BEGIN
    DBMS_OUTPUT.PUT_LINE('===== INFORME COMPLETO =====');
    FOR r IN c_deptos LOOP
        estadisticas_departamento(r.id_departamento, v_num, v_media, v_maximo);
        DBMS_OUTPUT.PUT_LINE('Depto ' || r.id_departamento
            || ' | Empleados: ' || v_num
            || ' | Media: '     || v_media   || ' €'
            || ' | Máximo: '    || v_maximo  || ' €');
    END LOOP;
END informe_completo_empresa;
/
```

---

### 2.4 Eliminación y consulta de procedimientos

```sql
-- Eliminar un procedimiento
DROP PROCEDURE mostrar_empleado;

-- Ver el código fuente almacenado en el catálogo
SELECT text
FROM   USER_SOURCE
WHERE  name = 'ESTADISTICAS_DEPARTAMENTO'   -- siempre en MAYÚSCULAS
  AND  type = 'PROCEDURE'
ORDER BY line;

-- Obtener descripción de parámetros (similar a DESCRIBE en tablas)
DESC estadisticas_departamento
```

> **USER\_SOURCE** es una vista del diccionario de datos que almacena el código fuente de todos los objetos PL/SQL del usuario actual (procedimientos, funciones, paquetes, triggers, etc.).

---

## 3. Funciones almacenadas

### 3.1 Concepto y sintaxis

Una **función** es casi idéntica a un procedimiento, con una diferencia fundamental: **siempre devuelve un único valor** mediante la cláusula `RETURN`. Por ello se usan principalmente para cálculos y transformaciones que producen un resultado concreto.

**Sintaxis en Oracle 21c:**

```sql
CREATE [OR REPLACE] FUNCTION nombre_funcion
    [ (
        parametro1 [IN] tipo_dato [:= valor_defecto],
        ...
    ) ]
RETURN tipo_dato_retorno
{ IS | AS }
    -- Sección de declaraciones locales
BEGIN
    -- Cuerpo
    ...
    RETURN expresion;   -- Obligatorio; puede haber varios RETURN
[EXCEPTION
    WHEN ... THEN
        RETURN valor_alternativo;
]
END [nombre_funcion];
/
```

> **Importante en Oracle 21c:** Los parámetros de una función solo deberían ser `IN` (por defecto). Aunque Oracle permite `OUT` e `IN OUT` en funciones, su uso no está permitido cuando la función se llama desde SQL, lo que la limita mucho. La práctica recomendada es que las funciones solo tengan parámetros `IN`.

---

**Ejemplo 4 — función que devuelve un NUMBER: calcular antigüedad**

```sql
CREATE OR REPLACE FUNCTION calcular_antiguedad (
    p_id_empleado IN EMPLEADOS.id_empleado%TYPE
)
RETURN NUMBER
IS
    v_fecha_alta EMPLEADOS.fecha_alta%TYPE;
    v_anios      NUMBER;
BEGIN
    SELECT fecha_alta
    INTO   v_fecha_alta
    FROM   EMPLEADOS
    WHERE  id_empleado = p_id_empleado;

    -- MONTHS_BETWEEN devuelve meses; dividimos entre 12 para obtener años
    v_anios := TRUNC(MONTHS_BETWEEN(SYSDATE, v_fecha_alta) / 12);
    RETURN v_anios;
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        RETURN NULL;
END calcular_antiguedad;
/
```

Uso desde un bloque anónimo:

```sql
SET SERVEROUTPUT ON
DECLARE
    v_anios NUMBER;
BEGIN
    v_anios := calcular_antiguedad(1);
    DBMS_OUTPUT.PUT_LINE('Antigüedad: ' || v_anios || ' año(s)');
END;
/
```

---

**Ejemplo 5 — función que devuelve un VARCHAR2: obtener nombre completo**

```sql
CREATE OR REPLACE FUNCTION nombre_completo (
    p_id_empleado IN EMPLEADOS.id_empleado%TYPE
)
RETURN VARCHAR2
IS
    v_resultado VARCHAR2(152);  -- 50 (nombre) + 1 (espacio) + 100 (apellidos) + 1
BEGIN
    SELECT nombre || ' ' || apellidos
    INTO   v_resultado
    FROM   EMPLEADOS
    WHERE  id_empleado = p_id_empleado;

    RETURN v_resultado;
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        RETURN 'Empleado desconocido';
END nombre_completo;
/
```

---

**Ejemplo 6 — función que devuelve BOOLEAN: comprobar si un salario supera la media del departamento**

```sql
CREATE OR REPLACE FUNCTION salario_superior_media (
    p_id_empleado IN EMPLEADOS.id_empleado%TYPE
)
RETURN BOOLEAN
IS
    v_salario    EMPLEADOS.salario%TYPE;
    v_id_depto   EMPLEADOS.id_departamento%TYPE;
    v_media      NUMBER;
BEGIN
    SELECT salario, id_departamento
    INTO   v_salario, v_id_depto
    FROM   EMPLEADOS
    WHERE  id_empleado = p_id_empleado;

    SELECT AVG(salario)
    INTO   v_media
    FROM   EMPLEADOS
    WHERE  id_departamento = v_id_depto;

    RETURN v_salario > v_media;
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        RETURN NULL;
END salario_superior_media;
/
```

Uso:

```sql
SET SERVEROUTPUT ON
BEGIN
    IF salario_superior_media(1) THEN
        DBMS_OUTPUT.PUT_LINE(nombre_completo(1) || ' cobra por encima de la media.');
    ELSIF salario_superior_media(1) IS NULL THEN
        DBMS_OUTPUT.PUT_LINE('Empleado no encontrado.');
    ELSE
        DBMS_OUTPUT.PUT_LINE(nombre_completo(1) || ' cobra por debajo de la media.');
    END IF;
END;
/
```

---

### 3.2 Diferencias clave con los procedimientos

| Característica              | Procedimiento                         | Función                                    |
|-----------------------------|---------------------------------------|--------------------------------------------|
| Devuelve valor              | No (solo mediante `OUT`)              | Sí, siempre mediante `RETURN`              |
| Uso en SQL                  | No directamente                       | Sí (con restricciones)                     |
| Parámetros `OUT` / `IN OUT` | Sí, sin restricciones                 | No recomendado (limita su uso desde SQL)   |
| Uso principal               | Ejecutar acciones (INSERT, UPDATE...) | Calcular y devolver un resultado           |
| `RETURN` en cabecera        | No                                    | Obligatorio (tipo de retorno)              |

---

### 3.3 Llamadas desde SQL y limitaciones

Una función PL/SQL puede usarse directamente en una consulta SQL **si cumple estas condiciones**:

- Solo tiene parámetros `IN`.
- No realiza operaciones DML (`INSERT`, `UPDATE`, `DELETE`) — salvo que se declaren con `PRAGMA AUTONOMOUS_TRANSACTION`, que se escapa del alcance de esta unidad.
- No lee ni escribe tablas del paquete al que pertenece la consulta SQL (para evitar mutaciones).

```sql
-- Uso de nombre_completo() directamente en una consulta SELECT:
SELECT id_empleado,
       nombre_completo(id_empleado)  AS nombre_completo,
       salario,
       calcular_antiguedad(id_empleado) AS anios_empresa
FROM   EMPLEADOS
WHERE  activo = 1
ORDER BY salario DESC;
```

> **¿Por qué BOOLEAN no funciona en SQL?** El tipo `BOOLEAN` es exclusivo de PL/SQL y Oracle SQL no lo reconoce. Si necesitas devolver un resultado lógico en una columna SQL, usa `NUMBER(1)` (0/1) o `VARCHAR2` ('S'/'N').

---

### 3.4 Sobrecarga y recursividad

**Sobrecarga:** Varias funciones (o procedimientos) con el **mismo nombre** pero distintos parámetros. Oracle los diferencia por la firma (número o tipos de parámetros). Solo es posible dentro de un **paquete** (ver apartado 4).

**Recursividad:** Una función que se llama a sí misma. Útil para algoritmos como factorial, Fibonacci, recorridos de árboles, etc.

```sql
-- Función recursiva: factorial de N
CREATE OR REPLACE FUNCTION factorial (n IN NUMBER)
RETURN NUMBER
IS
BEGIN
    IF n <= 1 THEN
        RETURN 1;
    ELSE
        RETURN n * factorial(n - 1);  -- llamada recursiva
    END IF;
END factorial;
/

-- Prueba:
SELECT factorial(5) AS resultado FROM DUAL;  -- devuelve 120
SELECT factorial(10) AS resultado FROM DUAL; -- devuelve 3628800
```

> **Precaución con la recursividad:** Si no se define correctamente el caso base, se producirá un desbordamiento de pila (`STACK_OVERFLOW`). Oracle limita la profundidad de recursión.

---

## 4. Paquetes

### 4.1 Concepto y estructura

Un **paquete** (*package*) es un contenedor que agrupa procedimientos, funciones, tipos, cursores, variables y constantes relacionados entre sí bajo un nombre común. Se usa para:

- **Organizar** el código en módulos cohesionados (principio de responsabilidad única).
- Controlar la **visibilidad**: los elementos declarados solo en el cuerpo son privados (no accesibles desde fuera).
- Mejorar el **rendimiento**: Oracle carga el paquete completo en memoria la primera vez que se usa, evitando cargas repetidas.
- Permitir la **sobrecarga** de procedimientos y funciones.

Un paquete se compone de **dos partes separadas**:

```
┌───────────────────────────────────────────────────────┐
│  ESPECIFICACIÓN del paquete  (la "interfaz pública")  │
│  Declara qué es visible desde fuera                   │
├───────────────────────────────────────────────────────┤
│  CUERPO del paquete  (la "implementación")            │
│  Define la lógica real; puede tener elementos privados│
└───────────────────────────────────────────────────────┘
```

---

### 4.2 Especificación del paquete

```sql
CREATE [OR REPLACE] PACKAGE nombre_paquete
{ IS | AS }
    -- Variables y constantes públicas
    -- Declaraciones de cursores (sin cuerpo)
    -- Declaraciones de tipos
    -- Cabeceras de procedimientos y funciones públicos
END nombre_paquete;
/
```

**Ejemplo: especificación del paquete `pkg_empleados`**

```sql
CREATE OR REPLACE PACKAGE pkg_empleados
AS
    -- Constante pública: salario mínimo de la empresa
    C_SALARIO_MINIMO CONSTANT NUMBER := 1200;

    -- Declaración de tipo público
    TYPE t_info_empleado IS RECORD (
        nombre_completo VARCHAR2(152),
        salario         EMPLEADOS.salario%TYPE,
        departamento    DEPARTAMENTOS.nombre%TYPE
    );

    -- Cabeceras de subprogramas públicos
    PROCEDURE mostrar_empleado    (p_id IN EMPLEADOS.id_empleado%TYPE);
    PROCEDURE subir_salario       (p_id IN EMPLEADOS.id_empleado%TYPE,
                                   p_porcentaje IN NUMBER);
    FUNCTION  obtener_info        (p_id IN EMPLEADOS.id_empleado%TYPE)
              RETURN t_info_empleado;
    FUNCTION  contar_por_depto    (p_id_depto IN DEPARTAMENTOS.id_departamento%TYPE)
              RETURN NUMBER;

END pkg_empleados;
/
```

---

### 4.3 Cuerpo del paquete

```sql
CREATE OR REPLACE PACKAGE BODY pkg_empleados
AS
    -- ── Procedimiento privado auxiliar (no está en la especificación) ──────────
    PROCEDURE registrar_log (p_mensaje IN VARCHAR2)
    IS
    BEGIN
        DBMS_OUTPUT.PUT_LINE('[LOG ' || TO_CHAR(SYSTIMESTAMP, 'HH24:MI:SS.FF3')
                             || '] ' || p_mensaje);
    END registrar_log;

    -- ── Implementación de MOSTRAR_EMPLEADO ─────────────────────────────────────
    PROCEDURE mostrar_empleado (p_id IN EMPLEADOS.id_empleado%TYPE)
    IS
        v_info t_info_empleado;
    BEGIN
        v_info := obtener_info(p_id);   -- reutilizamos la función del mismo paquete
        DBMS_OUTPUT.PUT_LINE('Nombre      : ' || v_info.nombre_completo);
        DBMS_OUTPUT.PUT_LINE('Salario     : ' || v_info.salario || ' €');
        DBMS_OUTPUT.PUT_LINE('Departamento: ' || v_info.departamento);
        registrar_log('mostrar_empleado ejecutado para ID=' || p_id);
    EXCEPTION
        WHEN NO_DATA_FOUND THEN
            DBMS_OUTPUT.PUT_LINE('Empleado con ID ' || p_id || ' no encontrado.');
    END mostrar_empleado;

    -- ── Implementación de SUBIR_SALARIO ────────────────────────────────────────
    PROCEDURE subir_salario (
        p_id         IN EMPLEADOS.id_empleado%TYPE,
        p_porcentaje IN NUMBER
    )
    IS
        v_salario_actual EMPLEADOS.salario%TYPE;
        v_salario_nuevo  EMPLEADOS.salario%TYPE;
    BEGIN
        IF p_porcentaje <= 0 OR p_porcentaje > 30 THEN
            RAISE_APPLICATION_ERROR(-20001,
                'El porcentaje debe estar entre 0.01 y 30.');
        END IF;

        SELECT salario INTO v_salario_actual
        FROM   EMPLEADOS
        WHERE  id_empleado = p_id;

        v_salario_nuevo := ROUND(v_salario_actual * (1 + p_porcentaje / 100), 2);

        IF v_salario_nuevo < C_SALARIO_MINIMO THEN
            RAISE_APPLICATION_ERROR(-20002,
                'El nuevo salario no puede ser inferior al mínimo de la empresa ('
                || C_SALARIO_MINIMO || ' €).');
        END IF;

        UPDATE EMPLEADOS SET salario = v_salario_nuevo
        WHERE  id_empleado = p_id;
        COMMIT;

        registrar_log('Salario de empleado ' || p_id || ' actualizado: '
                      || v_salario_actual || ' → ' || v_salario_nuevo);
    EXCEPTION
        WHEN NO_DATA_FOUND THEN
            RAISE_APPLICATION_ERROR(-20003, 'Empleado no encontrado: ID=' || p_id);
    END subir_salario;

    -- ── Implementación de OBTENER_INFO ─────────────────────────────────────────
    FUNCTION obtener_info (p_id IN EMPLEADOS.id_empleado%TYPE)
    RETURN t_info_empleado
    IS
        v_info t_info_empleado;
    BEGIN
        SELECT e.nombre || ' ' || e.apellidos,
               e.salario,
               d.nombre
        INTO   v_info.nombre_completo,
               v_info.salario,
               v_info.departamento
        FROM   EMPLEADOS e
        JOIN   DEPARTAMENTOS d ON e.id_departamento = d.id_departamento
        WHERE  e.id_empleado = p_id;

        RETURN v_info;
    END obtener_info;

    -- ── Implementación de CONTAR_POR_DEPTO ─────────────────────────────────────
    FUNCTION contar_por_depto (p_id_depto IN DEPARTAMENTOS.id_departamento%TYPE)
    RETURN NUMBER
    IS
        v_total NUMBER;
    BEGIN
        SELECT COUNT(*) INTO v_total
        FROM   EMPLEADOS
        WHERE  id_departamento = p_id_depto
          AND  activo = 1;
        RETURN v_total;
    END contar_por_depto;

END pkg_empleados;
/
```

---

### 4.4 Uso de elementos privados

El procedimiento `registrar_log` **no está declarado en la especificación**, por lo que es **privado**: solo puede ser llamado desde dentro del propio cuerpo del paquete. El código externo no puede invocarlo directamente.

**Invocación del paquete desde el exterior:**

```sql
SET SERVEROUTPUT ON

-- Llamada con la notación paquete.elemento
BEGIN
    pkg_empleados.mostrar_empleado(2);
END;
/

-- Subir el salario de Ana un 8%
BEGIN
    pkg_empleados.subir_salario(
        p_id         => 3,
        p_porcentaje => 8
    );
END;
/

-- Usar la función en una consulta SQL
SELECT d.nombre AS departamento,
       pkg_empleados.contar_por_depto(d.id_departamento) AS empleados_activos
FROM   DEPARTAMENTOS d
ORDER BY d.nombre;
```

---

## 5. Disparadores (Triggers)

### 5.1 Concepto

Un **disparador** (*trigger*) es un bloque PL/SQL que **se ejecuta automáticamente** cuando ocurre un evento concreto sobre un objeto de la base de datos. No se invoca explícitamente: Oracle lo dispara por sí solo.

Los eventos que pueden activar un disparador son principalmente:

| Categoría     | Eventos                                      |
|---------------|----------------------------------------------|
| **DML**       | `INSERT`, `UPDATE`, `DELETE` sobre una tabla o vista |
| **DDL**       | `CREATE`, `ALTER`, `DROP` (disparadores del sistema) |
| **Base de datos** | `STARTUP`, `SHUTDOWN`, `LOGON`, `LOGOFF`  |

En esta unidad nos centramos en los **disparadores DML**, que son los más habituales en el desarrollo de aplicaciones.

**Usos típicos de los disparadores:**

- **Auditoría:** registrar quién modificó qué y cuándo.
- **Validación compleja:** reglas de negocio que no se pueden expresar con `CHECK`.
- **Mantenimiento de integridad:** sincronizar tablas relacionadas automáticamente.
- **Generación de valores derivados:** calcular campos que dependen de otros.

---

### 5.2 Sintaxis general

```sql
CREATE [OR REPLACE] TRIGGER nombre_trigger
{ BEFORE | AFTER | INSTEAD OF }
{ INSERT [OR] | UPDATE [OF columna1, columna2, ...] [OR] | DELETE }
ON { nombre_tabla | nombre_vista }
[ REFERENCING OLD AS nombre_antiguo NEW AS nombre_nuevo ]
[ FOR EACH ROW ]
[ WHEN (condicion) ]
{ IS | AS }
    -- Sección de declaraciones locales (sin la palabra DECLARE)
BEGIN
    -- Cuerpo del trigger
    ...
[EXCEPTION
    ...
]
END [nombre_trigger];
/
```

**Descripción de las cláusulas:**

| Cláusula | Significado |
|---|---|
| `BEFORE` / `AFTER` | Si se ejecuta antes o después de la operación DML |
| `INSTEAD OF` | Solo en vistas; reemplaza la operación DML |
| `INSERT OR UPDATE OR DELETE` | Uno o varios eventos que activan el trigger |
| `ON tabla` | La tabla o vista sobre la que actúa |
| `FOR EACH ROW` | Si se omite → disparador de instrucción; si se incluye → disparador de fila |
| `WHEN (condición)` | Solo para disparadores de fila; filtra en qué filas se ejecuta |

---

### 5.3 Disparadores de instrucción vs. disparadores de fila

**Disparador de instrucción (sin `FOR EACH ROW`):**
- Se ejecuta **una sola vez** por cada sentencia DML, independientemente del número de filas afectadas.
- No tiene acceso a `:NEW` ni `:OLD`.
- Útil para tareas a nivel de operación: registrar que se hizo un UPDATE, controlar horarios, etc.

**Disparador de fila (con `FOR EACH ROW`):**
- Se ejecuta **una vez por cada fila** afectada por la sentencia DML.
- Tiene acceso a `:NEW` (nuevos valores) y `:OLD` (valores anteriores).
- Útil para auditoría campo a campo, validaciones sobre cada fila, etc.

**Ilustración con un ejemplo numérico:**

```
UPDATE EMPLEADOS SET salario = salario * 1.1 WHERE id_departamento = 1;
→ Afecta a 3 filas (Laura, Marcos, Sofía)

Disparador de instrucción → se ejecuta 1 vez en total
Disparador de fila        → se ejecuta 3 veces (una por cada empleado modificado)
```

---

### 5.4 Ejemplos completos paso a paso

**Ejemplo 7 — Disparador de instrucción: controlar horario de modificaciones**

Este trigger impide que se realicen modificaciones de salario fuera del horario laboral (lunes a viernes, de 8:00 a 18:00).

```sql
CREATE OR REPLACE TRIGGER trg_horario_update_salario
BEFORE UPDATE OF salario ON EMPLEADOS
-- Sin FOR EACH ROW → disparador de instrucción
AS
    v_dia_semana NUMBER;
    v_hora       NUMBER;
BEGIN
    v_dia_semana := TO_NUMBER(TO_CHAR(SYSDATE, 'D'));  -- 1=domingo, 2=lunes...7=sábado
    v_hora       := TO_NUMBER(TO_CHAR(SYSTIMESTAMP, 'HH24'));

    -- Fuera de horario laboral (sábado=7, domingo=1, o antes de 8 o después de 18)
    IF v_dia_semana IN (1, 7) OR v_hora < 8 OR v_hora >= 18 THEN
        RAISE_APPLICATION_ERROR(
            -20010,
            'Las modificaciones de salario solo están permitidas de lunes a viernes entre las 08:00 y las 18:00.'
        );
    END IF;
END trg_horario_update_salario;
/
```

> **`RAISE_APPLICATION_ERROR(código, mensaje)`:** Es el mecanismo estándar de Oracle para lanzar errores personalizados desde PL/SQL. Los códigos deben estar en el rango **-20000 a -20999** para errores de usuario.

---

**Ejemplo 8 — Disparador de fila AFTER: auditoría de cambios en salarios**

Este es uno de los usos más habituales de los triggers en entornos empresariales: registrar en una tabla de auditoría todos los cambios producidos.

```sql
CREATE OR REPLACE TRIGGER trg_auditoria_salario
AFTER UPDATE OF salario ON EMPLEADOS
FOR EACH ROW                   -- Se ejecuta una vez por cada empleado actualizado
WHEN (OLD.salario != NEW.salario)  -- Solo si el salario realmente cambió
AS
BEGIN
    INSERT INTO AUDITORIA_EMPLEADOS (
        id_empleado,
        accion,
        campo_modificado,
        valor_anterior,
        valor_nuevo,
        usuario_bd,
        fecha_hora
    ) VALUES (
        :OLD.id_empleado,
        'UPDATE',
        'SALARIO',
        TO_CHAR(:OLD.salario),
        TO_CHAR(:NEW.salario),
        USER,
        SYSTIMESTAMP
    );
    -- No hace falta COMMIT; el trigger participa de la transacción del UPDATE
END trg_auditoria_salario;
/
```

Prueba:

```sql
SET SERVEROUTPUT ON

-- Actualizar el salario de Marcos (id=2)
UPDATE EMPLEADOS SET salario = 2600 WHERE id_empleado = 2;
COMMIT;

-- Verificar la auditoría
SELECT id_empleado, accion, campo_modificado, valor_anterior,
       valor_nuevo, usuario_bd, TO_CHAR(fecha_hora, 'DD/MM/YYYY HH24:MI:SS') AS cuando
FROM   AUDITORIA_EMPLEADOS
ORDER BY id_auditoria;
```

---

**Ejemplo 9 — Disparador BEFORE INSERT: asignar valores automáticamente**

Este trigger asegura que el email siempre se almacene en minúsculas y que si no se proporciona, se genere uno automáticamente.

```sql
CREATE OR REPLACE TRIGGER trg_normalizar_empleado
BEFORE INSERT ON EMPLEADOS
FOR EACH ROW
AS
BEGIN
    -- Normalizar email a minúsculas
    IF :NEW.email IS NOT NULL THEN
        :NEW.email := LOWER(TRIM(:NEW.email));
    ELSE
        -- Generar email automático basado en nombre y apellidos
        :NEW.email := LOWER(
            SUBSTR(:NEW.nombre, 1, 1) || '.' ||
            REGEXP_REPLACE(LOWER(:NEW.apellidos), '[^a-z]', '')
            || '@techcorp.es'
        );
    END IF;

    -- Asegurarse de que nombre y apellidos estén en formato Título
    :NEW.nombre    := INITCAP(:NEW.nombre);
    :NEW.apellidos := INITCAP(:NEW.apellidos);
END trg_normalizar_empleado;
/
```

Prueba:

```sql
INSERT INTO EMPLEADOS (nombre, apellidos, salario, id_departamento)
VALUES ('CARLOS', 'SÁNCHEZ MORA', 2100, 2);
COMMIT;

SELECT nombre, apellidos, email FROM EMPLEADOS WHERE nombre = 'Carlos';
-- Resultado: Carlos | Sánchez Mora | c.sánchezmora@techcorp.es
```

---

**Ejemplo 10 — Disparador BEFORE DELETE: impedir borrado de empleados activos en proyectos**

```sql
CREATE OR REPLACE TRIGGER trg_proteger_empleado_activo
BEFORE DELETE ON EMPLEADOS
FOR EACH ROW
AS
    v_num_proyectos NUMBER;
BEGIN
    SELECT COUNT(*)
    INTO   v_num_proyectos
    FROM   EMPLEADOS_PROYECTOS
    WHERE  id_empleado = :OLD.id_empleado;

    IF v_num_proyectos > 0 THEN
        RAISE_APPLICATION_ERROR(
            -20020,
            'No se puede eliminar al empleado ' || :OLD.nombre || ' ' || :OLD.apellidos
            || ' porque está asignado a ' || v_num_proyectos || ' proyecto(s).'
        );
    END IF;
END trg_proteger_empleado_activo;
/
```

---

## 6. Explotación de disparadores: restricciones, orden y gestión

### 6.1 Restricciones importantes

Al programar disparadores en Oracle hay ciertas limitaciones que debes conocer para evitar errores en tiempo de ejecución:

| Restricción | Motivo |
|---|---|
| **No se permiten `COMMIT`, `ROLLBACK`, `SAVEPOINT` dentro del trigger** | El trigger participa de la misma transacción que la sentencia DML que lo disparó. Hacer un COMMIT dentro anularía el control transaccional del bloque llamador. |
| **No se pueden declarar variables `LONG` o `LONG RAW`** | Tipos obsoletos no soportados en este contexto. |
| **No se pueden referenciar tablas mutantes** (*mutating table*) | En un disparador de fila `FOR EACH ROW`, no puedes hacer SELECT sobre la misma tabla que está siendo modificada (la tabla "está en mutación"). Si lo necesitas, debes usar un disparador de instrucción o un enfoque con paquetes. |
| **No se puede llamar a procedimientos que realicen `COMMIT` o `ROLLBACK`** | Por la misma razón que el primer punto. |

**Ejemplo del error de tabla mutante y cómo evitarlo:**

```sql
-- ❌ INCORRECTO: trigger de fila que lee la misma tabla que se está actualizando
CREATE OR REPLACE TRIGGER trg_mal_diseño
AFTER UPDATE OF salario ON EMPLEADOS
FOR EACH ROW
AS
    v_media NUMBER;
BEGIN
    -- ESTO FALLARÁ con ORA-04091: table EMPLEADOS is mutating
    SELECT AVG(salario) INTO v_media FROM EMPLEADOS;
    ...
END trg_mal_diseño;
/

-- ✅ SOLUCIÓN: usar un disparador de instrucción (sin FOR EACH ROW)
-- En un disparador de instrucción la tabla ya no está "mutando"
CREATE OR REPLACE TRIGGER trg_buen_diseño
AFTER UPDATE OF salario ON EMPLEADOS
AS
    v_media NUMBER;
BEGIN
    SELECT AVG(salario) INTO v_media FROM EMPLEADOS; -- OK aquí
    DBMS_OUTPUT.PUT_LINE('Salario medio actualizado: ' || v_media);
END trg_buen_diseño;
/
```

---

### 6.2 Orden de ejecución

Cuando una misma operación DML puede activar múltiples disparadores, Oracle los ejecuta en este orden fijo:

```
┌─────────────────────────────────────────────────────────────────┐
│  1. Triggers de INSTRUCCIÓN  →  BEFORE                          │
│                                                                 │
│  ─── Se inicia el procesamiento fila a fila ──────────────────  │
│                                                                 │
│  2. Triggers de FILA          →  BEFORE  (por cada fila)        │
│  3.   [ La sentencia DML modifica la fila ]                     │
│  4. Triggers de FILA          →  AFTER   (por cada fila)        │
│                                                                 │
│  ─── Fin del procesamiento fila a fila ────────────────────── ─ │
│                                                                 │
│  5. Triggers de INSTRUCCIÓN  →  AFTER                           │
└─────────────────────────────────────────────────────────────────┘
```

**Ejemplo de traza con múltiples triggers:**

Si tienes estos cuatro triggers sobre `EMPLEADOS` y ejecutas `UPDATE EMPLEADOS SET salario = 3000 WHERE id_departamento = 1` (3 filas afectadas), el orden de ejecución sería:

```
1. trg_instruccion_before   → 1 vez
2. trg_fila_before          → 3 veces (Laura, Marcos, Sofía)
   [Oracle aplica el UPDATE a cada fila]
3. trg_fila_after           → 3 veces (Laura, Marcos, Sofía)
4. trg_instruccion_after    → 1 vez
```

---

### 6.3 Borrado, activación y desactivación

```sql
-- Eliminar un trigger definitivamente
DROP TRIGGER trg_auditoria_salario;

-- Deshabilitar un trigger (queda guardado pero no se ejecuta)
ALTER TRIGGER trg_horario_update_salario DISABLE;

-- Habilitar un trigger deshabilitado
ALTER TRIGGER trg_horario_update_salario ENABLE;

-- Deshabilitar TODOS los triggers de una tabla a la vez
ALTER TABLE EMPLEADOS DISABLE ALL TRIGGERS;

-- Habilitar TODOS los triggers de una tabla a la vez
ALTER TABLE EMPLEADOS ENABLE ALL TRIGGERS;
```

> **¿Cuándo deshabilitar triggers?** Un caso frecuente es durante cargas masivas de datos iniciales (*data loading*): se deshabilitan los triggers de auditoría para no generar millones de registros en la tabla de auditoría, y se vuelven a habilitar al finalizar.

---

## 7. Referencias NEW y OLD, y cláusulas condicionales DML

### 7.1 Referencias :NEW y :OLD

Disponibles **solo en disparadores de fila** (`FOR EACH ROW`), permiten acceder a los valores de cada columna antes y después de la operación DML:

| Pseudovariable | Disponible en    | Contiene                        |
|----------------|------------------|---------------------------------|
| `:OLD.columna` | `UPDATE`, `DELETE` | El valor **antes** de la operación |
| `:NEW.columna` | `INSERT`, `UPDATE` | El valor **después** de la operación |

**Reglas de disponibilidad según la operación:**

| Operación | `:OLD` | `:NEW` |
|-----------|:------:|:------:|
| `INSERT`  | `NULL` | El valor insertado |
| `UPDATE`  | Valor anterior | Valor nuevo |
| `DELETE`  | Valor que se borrará | `NULL` |

**Importante:** Dentro de un trigger `BEFORE`, puedes **modificar** `:NEW` para cambiar el valor que finalmente se almacenará. En un trigger `AFTER`, `:NEW` ya está fijado y no se puede modificar.

**Ejemplo 11 — Auditoría completa de múltiples campos con :NEW y :OLD**

```sql
CREATE OR REPLACE TRIGGER trg_auditoria_completa_empleados
AFTER INSERT OR UPDATE OR DELETE ON EMPLEADOS
FOR EACH ROW
AS
BEGIN
    IF INSERTING THEN
        INSERT INTO AUDITORIA_EMPLEADOS
            (id_empleado, accion, campo_modificado, valor_anterior, valor_nuevo)
        VALUES
            (:NEW.id_empleado, 'INSERT', 'REGISTRO COMPLETO', NULL,
             :NEW.nombre || ' ' || :NEW.apellidos || ' | sal=' || :NEW.salario);

    ELSIF UPDATING THEN
        -- Auditar cambio de salario
        IF :OLD.salario != :NEW.salario THEN
            INSERT INTO AUDITORIA_EMPLEADOS
                (id_empleado, accion, campo_modificado, valor_anterior, valor_nuevo)
            VALUES
                (:OLD.id_empleado, 'UPDATE', 'SALARIO',
                 TO_CHAR(:OLD.salario), TO_CHAR(:NEW.salario));
        END IF;
        -- Auditar cambio de departamento
        IF NVL(:OLD.id_departamento, -1) != NVL(:NEW.id_departamento, -1) THEN
            INSERT INTO AUDITORIA_EMPLEADOS
                (id_empleado, accion, campo_modificado, valor_anterior, valor_nuevo)
            VALUES
                (:OLD.id_empleado, 'UPDATE', 'DEPARTAMENTO',
                 TO_CHAR(:OLD.id_departamento), TO_CHAR(:NEW.id_departamento));
        END IF;

    ELSIF DELETING THEN
        INSERT INTO AUDITORIA_EMPLEADOS
            (id_empleado, accion, campo_modificado, valor_anterior, valor_nuevo)
        VALUES
            (:OLD.id_empleado, 'DELETE', 'REGISTRO COMPLETO',
             :OLD.nombre || ' ' || :OLD.apellidos || ' | sal=' || :OLD.salario, NULL);
    END IF;
END trg_auditoria_completa_empleados;
/
```

---

### 7.2 Cláusulas IF INSERTING, IF UPDATING, IF DELETING

Cuando un trigger se activa por más de un evento DML (definido con `INSERT OR UPDATE OR DELETE`), se usan estas cláusulas para identificar **cuál fue el evento** que lo disparó:

| Predicado      | Devuelve `TRUE` cuando... |
|----------------|---------------------------|
| `INSERTING`    | La operación fue un `INSERT` |
| `UPDATING`     | La operación fue un `UPDATE` |
| `UPDATING('columna')` | El UPDATE afectó específicamente a esa columna |
| `DELETING`     | La operación fue un `DELETE` |

> **Nota:** En el código del trigger **no se usan con `IF` directamente** sino que se evalúan como booleanos: `IF INSERTING THEN ...` (sin los dos puntos de `:NEW` / `:OLD`).

**Ejemplo 12 — Trigger multi-evento con IF INSERTING / UPDATING / DELETING**

```sql
CREATE OR REPLACE TRIGGER trg_control_proyectos
BEFORE INSERT OR UPDATE OR DELETE ON PROYECTOS
FOR EACH ROW
AS
    v_msg VARCHAR2(200);
BEGIN
    IF INSERTING THEN
        -- Validar que la fecha de fin no sea anterior a la de inicio
        IF :NEW.fecha_fin IS NOT NULL AND :NEW.fecha_fin < :NEW.fecha_inicio THEN
            RAISE_APPLICATION_ERROR(-20030,
                'La fecha de fin no puede ser anterior a la fecha de inicio.');
        END IF;
        v_msg := 'Proyecto insertado: ' || :NEW.nombre;

    ELSIF UPDATING('presupuesto') THEN
        -- Validar que el nuevo presupuesto no baje más de un 20%
        IF :NEW.presupuesto < :OLD.presupuesto * 0.8 THEN
            RAISE_APPLICATION_ERROR(-20031,
                'El presupuesto no puede reducirse más de un 20% en una sola operación.');
        END IF;
        v_msg := 'Presupuesto actualizado: ' || :OLD.presupuesto
                 || ' → ' || :NEW.presupuesto;

    ELSIF UPDATING THEN
        v_msg := 'Proyecto modificado: ' || NVL(:NEW.nombre, :OLD.nombre);

    ELSIF DELETING THEN
        -- No permitir borrar proyectos con empleados asignados
        DECLARE
            v_asignados NUMBER;
        BEGIN
            SELECT COUNT(*) INTO v_asignados
            FROM   EMPLEADOS_PROYECTOS
            WHERE  id_proyecto = :OLD.id_proyecto;
            IF v_asignados > 0 THEN
                RAISE_APPLICATION_ERROR(-20032,
                    'No se puede eliminar el proyecto "' || :OLD.nombre
                    || '" porque tiene ' || v_asignados || ' empleado(s) asignado(s).');
            END IF;
        END;
        v_msg := 'Proyecto eliminado: ' || :OLD.nombre;
    END IF;

    DBMS_OUTPUT.PUT_LINE('[PROYECTOS] ' || v_msg);
END trg_control_proyectos;
/
```

---

## 8. Disparadores INSTEAD OF

### Concepto

Los disparadores `INSTEAD OF` son un tipo especial que **solo puede crearse sobre vistas** (no sobre tablas). Se usan cuando quieres que las operaciones DML sobre una vista (que normalmente no serían posibles o serían incorrectas) ejecuten una lógica personalizada en su lugar.

**¿Por qué no se puede modificar una vista directamente?**  
Una vista que combina datos de múltiples tablas mediante `JOIN` no tiene una correspondencia unívoca entre cada fila de la vista y las filas de las tablas base. Oracle no sabe a cuál de las tablas dirigir el `INSERT` o `UPDATE`, por lo que lo rechaza con un error.

**Ejemplo 13 — Vista con JOIN y trigger INSTEAD OF**

```sql
-- Primero creamos la vista
CREATE OR REPLACE VIEW v_empleados_detalle AS
SELECT e.id_empleado,
       e.nombre       || ' ' || e.apellidos AS nombre_completo,
       e.email,
       e.salario,
       d.id_departamento,
       d.nombre AS nombre_departamento
FROM   EMPLEADOS    e
JOIN   DEPARTAMENTOS d ON e.id_departamento = d.id_departamento
WHERE  e.activo = 1;
```

Sin el trigger, intentar insertar en la vista daría error. Con él:

```sql
CREATE OR REPLACE TRIGGER trg_iof_empleados_detalle
INSTEAD OF INSERT ON v_empleados_detalle
FOR EACH ROW
AS
    v_id_depto DEPARTAMENTOS.id_departamento%TYPE;
BEGIN
    -- Verificar que el departamento indicado existe
    BEGIN
        SELECT id_departamento INTO v_id_depto
        FROM   DEPARTAMENTOS
        WHERE  id_departamento = :NEW.id_departamento;
    EXCEPTION
        WHEN NO_DATA_FOUND THEN
            RAISE_APPLICATION_ERROR(-20040,
                'El departamento con ID ' || :NEW.id_departamento || ' no existe.');
    END;

    -- Insertar en la tabla base real
    INSERT INTO EMPLEADOS (nombre, apellidos, email, salario, id_departamento)
    VALUES (
        SUBSTR(:NEW.nombre_completo, 1, INSTR(:NEW.nombre_completo, ' ') - 1),
        SUBSTR(:NEW.nombre_completo, INSTR(:NEW.nombre_completo, ' ') + 1),
        :NEW.email,
        :NEW.salario,
        :NEW.id_departamento
    );
END trg_iof_empleados_detalle;
/
```

Ahora se puede insertar a través de la vista:

```sql
-- Esto funciona gracias al trigger INSTEAD OF:
INSERT INTO v_empleados_detalle (nombre_completo, email, salario, id_departamento)
VALUES ('Elena Torres', 'e.torres@techcorp.es', 2300, 3);
COMMIT;
```

---

## 9. Información sobre disparadores en el catálogo

Oracle almacena metadatos de todos los objetos de la base de datos en las vistas del **diccionario de datos**. Las más útiles para consultar información sobre triggers son:

```sql
-- Ver todos los triggers del usuario actual
SELECT trigger_name,
       trigger_type,     -- BEFORE/AFTER EACH ROW, etc.
       triggering_event, -- INSERT, UPDATE, DELETE
       table_name,
       status            -- ENABLED / DISABLED
FROM   USER_TRIGGERS
ORDER BY table_name, trigger_name;

-- Ver el código fuente de un trigger específico
SELECT text
FROM   USER_SOURCE
WHERE  name = 'TRG_AUDITORIA_SALARIO'  -- siempre en MAYÚSCULAS
  AND  type = 'TRIGGER'
ORDER BY line;

-- Descripción de la estructura de USER_TRIGGERS
DESC USER_TRIGGERS
```

Ejemplo de salida de `USER_TRIGGERS`:

```
TRIGGER_NAME                    TRIGGER_TYPE      TRIGGERING_EVENT  TABLE_NAME    STATUS
------------------------------- ----------------- ----------------- ------------- --------
TRG_AUDITORIA_COMPLETA_EMPLEADOS AFTER EACH ROW   INSERT OR UPDATE OR DELETE EMPLEADOS ENABLED
TRG_HORARIO_UPDATE_SALARIO       BEFORE STATEMENT UPDATE            EMPLEADOS     ENABLED
TRG_NORMALIZAR_EMPLEADO          BEFORE EACH ROW  INSERT            EMPLEADOS     ENABLED
TRG_PROTEGER_EMPLEADO_ACTIVO     BEFORE EACH ROW  DELETE            EMPLEADOS     ENABLED
TRG_CONTROL_PROYECTOS            BEFORE EACH ROW  INSERT OR UPDATE OR DELETE PROYECTOS ENABLED
```

---

## 10. Automatización de la ejecución de scripts

### 10.1 Oracle Scheduler

Oracle Scheduler (`DBMS_SCHEDULER`) es el mecanismo recomendado en Oracle 21c para programar la ejecución automática de scripts PL/SQL. Sustituye al antiguo `DBMS_JOB`.

**Conceptos clave:**

- **Job:** tarea programada con nombre, acción y planificación.
- **Schedule:** define cuándo y con qué frecuencia se ejecuta (puede ser reutilizable).
- **Program:** encapsula la acción a ejecutar (bloque PL/SQL, procedimiento, script externo).

**Crear un job simple:**

```sql
-- Job que ejecuta diariamente a las 02:00 el mantenimiento del sistema
BEGIN
    DBMS_SCHEDULER.CREATE_JOB (
        job_name        => 'JOB_MANTENIMIENTO_DIARIO',
        job_type        => 'PLSQL_BLOCK',
        job_action      => '
            BEGIN
                -- Desactivar empleados que llevan más de 2 años sin actividad
                UPDATE EMPLEADOS
                SET    activo = 0
                WHERE  activo = 1
                  AND  fecha_alta < ADD_MONTHS(SYSDATE, -24);
                COMMIT;
                DBMS_OUTPUT.PUT_LINE(''Mantenimiento completado: '' || SQL%ROWCOUNT || '' registros.'');
            END;
        ',
        start_date      => SYSTIMESTAMP,
        repeat_interval => 'FREQ=DAILY; BYHOUR=2; BYMINUTE=0; BYSECOND=0',
        enabled         => TRUE,
        comments        => 'Mantenimiento nocturno de empleados inactivos'
    );
END;
/
```

**Crear un job que llama a un procedimiento almacenado:**

```sql
BEGIN
    DBMS_SCHEDULER.CREATE_JOB (
        job_name        => 'JOB_INFORME_SEMANAL',
        job_type        => 'STORED_PROCEDURE',
        job_action      => 'PKG_EMPLEADOS.MOSTRAR_EMPLEADO',  -- paquete.procedimiento
        start_date      => NEXT_DAY(TRUNC(SYSDATE), 'MONDAY'),  -- próximo lunes
        repeat_interval => 'FREQ=WEEKLY; BYDAY=MON; BYHOUR=7; BYMINUTE=30',
        enabled         => TRUE,
        comments        => 'Informe semanal de empleados los lunes a las 7:30'
    );
END;
/
```

**Gestión de jobs:**

```sql
-- Habilitar / deshabilitar
BEGIN DBMS_SCHEDULER.ENABLE('JOB_MANTENIMIENTO_DIARIO');  END; /
BEGIN DBMS_SCHEDULER.DISABLE('JOB_MANTENIMIENTO_DIARIO'); END; /

-- Ejecutar manualmente (fuera de su planificación)
BEGIN DBMS_SCHEDULER.RUN_JOB('JOB_MANTENIMIENTO_DIARIO'); END; /

-- Detener un job en ejecución
BEGIN DBMS_SCHEDULER.STOP_JOB('JOB_MANTENIMIENTO_DIARIO'); END; /

-- Modificar un parámetro (ej: cambiar el intervalo)
BEGIN
    DBMS_SCHEDULER.SET_ATTRIBUTE(
        name      => 'JOB_MANTENIMIENTO_DIARIO',
        attribute => 'repeat_interval',
        value     => 'FREQ=DAILY; BYHOUR=3; BYMINUTE=0'
    );
END;
/

-- Eliminar definitivamente
BEGIN DBMS_SCHEDULER.DROP_JOB('JOB_MANTENIMIENTO_DIARIO'); END; /

-- Consultar todos los jobs del usuario
SELECT job_name, job_type, enabled, state, last_start_date, next_run_date
FROM   USER_SCHEDULER_JOBS
ORDER BY job_name;
```

---

### 10.2 DBMS_JOB (legado)

`DBMS_JOB` es el predecesor de Oracle Scheduler y está disponible por compatibilidad, pero **no se recomienda en Oracle 12c en adelante**. En Oracle 21c su uso está desaconsejado para nuevos desarrollos.

```sql
-- Ejemplo DBMS_JOB (solo para contexto histórico; usar DBMS_SCHEDULER en su lugar)
DECLARE
    v_job_id NUMBER;
BEGIN
    DBMS_JOB.SUBMIT(
        job       => v_job_id,
        what      => 'BEGIN pkg_empleados.mostrar_empleado(1); END;',
        next_date => SYSDATE + 1/24,  -- en 1 hora
        interval  => 'SYSDATE + 1'    -- cada día
    );
    COMMIT;
    DBMS_OUTPUT.PUT_LINE('Job registrado con ID: ' || v_job_id);
END;
/
```

---

### 10.3 Shell Scripts con SQL\*Plus y Cron

Para automatizar la ejecución de scripts desde el sistema operativo (Linux/Unix), se combinan **SQL\*Plus** (cliente de línea de comandos de Oracle) con **cron**.

**Script de shell (`mantenimiento.sh`):**

```bash
#!/bin/bash
# Script de ejecución de mantenimiento nocturno
# Requiere que la variable ORACLE_HOME esté configurada

export ORACLE_HOME=/opt/oracle/product/21c/dbhomeXE
export PATH=$ORACLE_HOME/bin:$PATH
export ORACLE_SID=XE

LOG="/var/log/oracle/mantenimiento_$(date +%Y%m%d).log"

sqlplus -s usuario/contraseña@localhost:1521/XEPDB1 <<EOF >> "$LOG" 2>&1
    SET SERVEROUTPUT ON
    SET FEEDBACK OFF
    EXEC pkg_empleados.mostrar_empleado(1);
    EXIT;
EOF

echo "Script finalizado. Revisar: $LOG"
```

**Programar con cron** (ejecutar todos los días a las 02:00):

```
# Editar con: crontab -e
0 2 * * * /opt/scripts/oracle/mantenimiento.sh
```

> **Advertencia de seguridad:** Nunca incluyas usuario y contraseña en texto plano en scripts de producción. Usa Oracle Wallet o variables de entorno protegidas.

---

## 11. APIs de acceso a bases de datos Oracle

En aplicaciones modernas, el acceso a Oracle desde lenguajes externos se realiza a través de **drivers y APIs específicas**. Las más relevantes para un desarrollador DAM son:

| API / Tecnología | Lenguaje | Descripción |
|---|---|---|
| **JDBC (Oracle JDBC Driver)** | Java | Driver oficial de Oracle para Java. Dos variantes: `thin` (puro Java, sin cliente Oracle) y `OCI` (requiere cliente Oracle). |
| **cx_Oracle / python-oracledb** | Python | Librería oficial para conectar Python con Oracle. `python-oracledb` es la versión moderna (Oracle 21c+). |
| **ODP.NET (Oracle Data Provider for .NET)** | C# / .NET | Proveedor ADO.NET oficial de Oracle para aplicaciones .NET. |
| **Node-oracledb** | Node.js | Driver oficial para JavaScript/TypeScript con Node.js. |
| **Oracle APEX** | Low-code / SQL | Plataforma web integrada en Oracle DB para crear aplicaciones sin apenas programación externa. |

**Ejemplo de conexión Java con JDBC (thin driver):**

```java
import java.sql.*;

public class EjemploOracle {
    public static void main(String[] args) throws Exception {
        // Cadena de conexión: jdbc:oracle:thin:@//host:puerto/servicio
        String url  = "jdbc:oracle:thin:@//localhost:1521/XEPDB1";
        String user = "techcorp";
        String pass = "miContraseña";

        try (Connection conn = DriverManager.getConnection(url, user, pass)) {
            // Llamar a un procedimiento almacenado con parámetros OUT
            String sql = "{ call pkg_empleados.estadisticas_departamento(?, ?, ?, ?) }";
            try (CallableStatement cs = conn.prepareCall(sql)) {
                cs.setInt(1, 1);                              // IN: id_departamento
                cs.registerOutParameter(2, Types.INTEGER);    // OUT: num_empleados
                cs.registerOutParameter(3, Types.DECIMAL);    // OUT: salario_medio
                cs.registerOutParameter(4, Types.DECIMAL);    // OUT: salario_maximo
                cs.execute();

                System.out.println("Empleados  : " + cs.getInt(2));
                System.out.println("Media sal. : " + cs.getDouble(3) + " €");
                System.out.println("Máximo sal.: " + cs.getDouble(4) + " €");
            }
        }
    }
}
```

**Ejemplo de conexión Python con python-oracledb:**

```python
import oracledb

# Modo "thin" (no requiere cliente Oracle instalado)
conn = oracledb.connect(
    user="techcorp",
    password="miContraseña",
    dsn="localhost:1521/XEPDB1"
)

cursor = conn.cursor()

# Llamar a una función almacenada
resultado = cursor.callfunc(
    "pkg_empleados.contar_por_depto",   # nombre del paquete.función
    int,                                 # tipo de retorno Python
    [1]                                  # parámetro IN: id_departamento
)
print(f"Empleados en departamento 1: {resultado}")

# Ejecutar un procedimiento
num_emp  = cursor.var(int)
sal_med  = cursor.var(float)
sal_max  = cursor.var(float)
cursor.callproc(
    "pkg_empleados.estadisticas_departamento",
    [1, num_emp, sal_med, sal_max]
)
print(f"Empleados: {num_emp.getvalue()}, Media: {sal_med.getvalue():.2f} €")

cursor.close()
conn.close()
```

---

## 12. Errores frecuentes y buenas prácticas

### Errores frecuentes

| Error | Causa habitual | Solución |
|---|---|---|
| `ORA-04091: table is mutating` | Trigger de fila hace SELECT/DML sobre la misma tabla | Usar trigger de instrucción o variables de paquete |
| `ORA-04088: error during execution of trigger` | Error en el código del trigger | Revisar con `SHOW ERRORS TRIGGER nombre` |
| `ORA-06512: at trigger, line N` | Error en una línea concreta del trigger | Depurar con `DBMS_OUTPUT` o SQL Developer |
| `ORA-20XXX` (errores custom) | `RAISE_APPLICATION_ERROR` | Diseñar bien los códigos de error personalizados |
| `PLS-00363: expression cannot be used as assignment target` | Intentar asignar `:OLD` en un trigger BEFORE | Solo `:NEW` puede modificarse en BEFORE |
| `ORA-04021: timeout occurred while waiting to lock object` | Deadlock entre triggers | Revisar dependencias entre disparadores |

### Buenas prácticas

1. **Nombrar con prefijo:** Usar `trg_` para triggers, `pkg_` para paquetes, `prc_` / `fnc_` para procedimientos y funciones.
2. **Documentar el código:** Incluir comentarios con propósito, autor y fecha de última modificación.
3. **Minimizar triggers:** Crear solo los estrictamente necesarios. Los triggers ocultos complican el mantenimiento.
4. **Evitar lógica de negocio compleja en triggers:** Si la lógica es compleja, llamar a un procedimiento del paquete correspondiente desde el trigger.
5. **No usar COMMIT/ROLLBACK en triggers:** Participan de la transacción del llamador.
6. **Usar `OR REPLACE` siempre:** Facilita el ciclo de desarrollo iterativo.
7. **Compilar y verificar errores tras crear objetos:**

```sql
-- Verificar si hay errores de compilación tras crear un procedimiento/función/trigger
SHOW ERRORS

-- También se puede consultar en el diccionario de datos
SELECT line, position, text
FROM   USER_ERRORS
WHERE  name = 'PKG_EMPLEADOS'
  AND  type = 'PACKAGE BODY'
ORDER BY sequence;
```

---

## 13. Resumen y mapa conceptual

```
SUBPROGRAMAS Y DISPARADORES EN PL/SQL (Oracle 21c)
│
├── PROCEDIMIENTOS ALMACENADOS
│   ├── Sintaxis: CREATE [OR REPLACE] PROCEDURE
│   ├── Parámetros: IN · OUT · IN OUT
│   ├── Invocación: bloque anónimo · EXECUTE · desde otro subprograma
│   └── Gestión: DROP PROCEDURE · USER_SOURCE · DESC
│
├── FUNCIONES ALMACENADAS
│   ├── Sintaxis: CREATE [OR REPLACE] FUNCTION ... RETURN tipo
│   ├── Siempre devuelven un valor con RETURN
│   ├── Usables desde SQL (con restricciones)
│   ├── Sobrecarga (solo en paquetes)
│   └── Recursividad
│
├── PAQUETES
│   ├── Especificación (interfaz pública)
│   │   └── Declara tipos, constantes, cursores, cabeceras
│   ├── Cuerpo (implementación)
│   │   ├── Implementa los subprogramas públicos
│   │   └── Puede contener elementos privados
│   └── Referencia: nombre_paquete.nombre_elemento
│
├── DISPARADORES (TRIGGERS)
│   ├── Tipos por momento: BEFORE · AFTER · INSTEAD OF
│   ├── Tipos por evento: INSERT · UPDATE · DELETE (combinables con OR)
│   ├── Granularidad:
│   │   ├── Instrucción (sin FOR EACH ROW) → 1 ejecución por sentencia
│   │   └── Fila (con FOR EACH ROW)        → 1 ejecución por fila afectada
│   ├── Pseudovariables: :NEW · :OLD
│   ├── Predicados: INSERTING · UPDATING · DELETING
│   ├── INSTEAD OF → solo sobre vistas
│   ├── Restricciones: no COMMIT/ROLLBACK · no tablas mutantes
│   ├── Orden de ejecución: instrucción BEFORE → fila BEFORE → DML → fila AFTER → instrucción AFTER
│   └── Gestión: DROP TRIGGER · ALTER TRIGGER ENABLE/DISABLE · ALTER TABLE ... ALL TRIGGERS
│
└── AUTOMATIZACIÓN
    ├── Oracle Scheduler (DBMS_SCHEDULER) → recomendado en Oracle 21c
    │   ├── CREATE_JOB · ENABLE · DISABLE · RUN_JOB · STOP_JOB · DROP_JOB
    │   └── Tipos: PLSQL_BLOCK · STORED_PROCEDURE
    ├── DBMS_JOB → legado, no recomendado
    ├── Shell Scripts + SQL*Plus + Cron (Linux/Unix)
    └── APIs externas: JDBC (Java) · python-oracledb (Python) · ODP.NET (C#)
```

---

> **¿Quieres practicar?** Todos los ejemplos de esta unidad están diseñados para ejecutarse sobre el esquema `TechCorp` definido en el apartado 1. Crea las tablas, inserta los datos de prueba y ejecuta cada bloque de código en Oracle SQL Developer o en SQL\*Plus conectado a tu instancia Oracle 21c (XE o superior).
