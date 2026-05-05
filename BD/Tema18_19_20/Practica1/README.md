# Unidades 18/19/20: Práctica 1 — Bases de Datos Objeto-Relacionales

## 1. Contexto del escenario de trabajo

Para esta práctica trabajamos con el sistema de gestión de **RentaCar Levante**, una empresa de alquiler de vehículos con delegaciones en varias ciudades. El modelo de datos está diseñado con tipos objeto en Oracle 21c para que puedas practicar la definición, el uso y la persistencia de objetos, abarcando los contenidos de los tres temas secuencialmente.

| Tipo / Tabla             | Descripción                                                        |
|--------------------------|--------------------------------------------------------------------|
| `tipo_ubicacion`         | Tipo auxiliar (Tema 18): dirección de una sede o delegación        |
| `tipo_seguro`            | Tipo base (Tema 18): atributos, métodos, sobrecarga y constructores|
| `tipo_vehiculo`          | Supertipo abstracto (Tema 19): raíz de la herencia de vehículos    |
| `tipo_turismo`           | Subtipo concreto (Tema 19): hereda de vehículo                     |
| `tipo_furgoneta`         | Subtipo concreto (Tema 19): hereda de vehículo                     |
| `tipo_lista_extras`      | Tipo colección VARRAY (Tema 20): lista de extras del alquiler      |
| `turismos` / `furgonetas`| Tablas de objetos (Tema 20)                                        |
| `alquileres`             | Tabla mixta con columnas simples y objetos embebidos (Tema 20)     |
| `delegaciones`           | Tabla mixta con columna `REF` (Tema 20)                            |

> **Nota:** Ejecuta el script de inicialización antes de comenzar. Este script prepara la base de datos con todos los elementos de los tres temas para que funcione correctamente, aunque en los ejercicios se te pedirá reescribir y analizar partes concretas de este código.

---

## 2. Script de inicialización

```sql
/* =============================================================
   ESQUEMA: RENTACAR LEVANTE — Unidades 18/19/20
   Script corregido y validado para Oracle 21c
   ============================================================= */

-- ── 0. Limpieza (tablas primero, luego tipos en orden inverso) ──
BEGIN EXECUTE IMMEDIATE 'DROP TABLE delegaciones CASCADE CONSTRAINTS'; EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN EXECUTE IMMEDIATE 'DROP TABLE alquileres CASCADE CONSTRAINTS'; EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN EXECUTE IMMEDIATE 'DROP TABLE furgonetas CASCADE CONSTRAINTS'; EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN EXECUTE IMMEDIATE 'DROP TABLE turismos CASCADE CONSTRAINTS'; EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN EXECUTE IMMEDIATE 'DROP TYPE tipo_furgoneta FORCE'; EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN EXECUTE IMMEDIATE 'DROP TYPE tipo_turismo FORCE'; EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN EXECUTE IMMEDIATE 'DROP TYPE tipo_vehiculo FORCE'; EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN EXECUTE IMMEDIATE 'DROP TYPE tipo_seguro FORCE'; EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN EXECUTE IMMEDIATE 'DROP TYPE tipo_lista_extras FORCE'; EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN EXECUTE IMMEDIATE 'DROP TYPE tipo_ubicacion FORCE'; EXCEPTION WHEN OTHERS THEN NULL; END;
/

-- ── 1. Tipos Base (Tema 18) ──────────────────────────────────────────────────

CREATE OR REPLACE TYPE tipo_ubicacion AS OBJECT (
    calle    VARCHAR2(100),
    ciudad   VARCHAR2(50),
    telefono VARCHAR2(15)
);
/

CREATE OR REPLACE TYPE tipo_seguro AS OBJECT (
    codigo         VARCHAR2(10),
    aseguradora    VARCHAR2(50),
    cobertura      VARCHAR2(20),
    precio_diario  NUMBER(5,2),

    MEMBER FUNCTION calcular_coste(p_dias NUMBER) RETURN NUMBER,
    MEMBER PROCEDURE aplicar_descuento(p_pct NUMBER),
    STATIC FUNCTION validar_cobertura(p_cob VARCHAR2) RETURN BOOLEAN,
    
    -- Constructor personalizado
    CONSTRUCTOR FUNCTION tipo_seguro(
        p_cod VARCHAR2, p_aseg VARCHAR2, p_cob VARCHAR2
    ) RETURN SELF AS RESULT,
    
    -- Método ORDER (Tema 19)
    ORDER MEMBER FUNCTION comparar(otro tipo_seguro) RETURN INTEGER
);
/

CREATE OR REPLACE TYPE BODY tipo_seguro AS
    MEMBER FUNCTION calcular_coste(p_dias NUMBER) RETURN NUMBER IS
    BEGIN 
        RETURN SELF.precio_diario * p_dias; 
    END calcular_coste;

    MEMBER PROCEDURE aplicar_descuento(p_pct NUMBER) IS
    BEGIN 
        SELF.precio_diario := SELF.precio_diario * (1 - p_pct/100); 
    END aplicar_descuento;

    STATIC FUNCTION validar_cobertura(p_cob VARCHAR2) RETURN BOOLEAN IS
    BEGIN 
        RETURN UPPER(p_cob) IN ('BASICA', 'FRANQUICIA', 'TODO RIESGO'); 
    END validar_cobertura;

    CONSTRUCTOR FUNCTION tipo_seguro(
        p_cod VARCHAR2, p_aseg VARCHAR2, p_cob VARCHAR2
    ) RETURN SELF AS RESULT IS
    BEGIN
        SELF.codigo := p_cod;
        SELF.aseguradora := p_aseg;
        SELF.cobertura := UPPER(p_cob);
        IF SELF.cobertura = 'TODO RIESGO' THEN SELF.precio_diario := 20.00;
        ELSE SELF.precio_diario := 10.00; END IF;
        RETURN;
    END;

    ORDER MEMBER FUNCTION comparar(otro tipo_seguro) RETURN INTEGER IS
    BEGIN
        IF SELF.precio_diario > otro.precio_diario THEN RETURN 1;
        ELSIF SELF.precio_diario < otro.precio_diario THEN RETURN -1;
        ELSE RETURN 0; END IF;
    END comparar;
END;
/

-- ── 2. Colecciones (Tema 20) ─────────────────────────────────────────────────

CREATE OR REPLACE TYPE tipo_lista_extras AS VARRAY(8) OF VARCHAR2(30);
/

-- ── 3. Herencia y Tipos Abstractos (Tema 19) ─────────────────────────────────

CREATE OR REPLACE TYPE tipo_vehiculo AS OBJECT (
    matricula  VARCHAR2(8),
    marca      VARCHAR2(30),
    modelo     VARCHAR2(50),
    anio       NUMBER(4),
    precio_dia NUMBER(6,2),

    NOT INSTANTIABLE MEMBER FUNCTION descripcion RETURN VARCHAR2,
    MEMBER FUNCTION calcular_precio (p_dias NUMBER) RETURN NUMBER,
    
    -- El método MAP debe ir en la raíz de la jerarquía en Oracle
    MAP MEMBER FUNCTION criterio_orden RETURN VARCHAR2
) NOT INSTANTIABLE NOT FINAL;
/

CREATE OR REPLACE TYPE BODY tipo_vehiculo AS
    MEMBER FUNCTION calcular_precio (p_dias NUMBER) RETURN NUMBER IS
    BEGIN
        RETURN ROUND(SELF.precio_dia * p_dias, 2);
    END calcular_precio;

    MAP MEMBER FUNCTION criterio_orden RETURN VARCHAR2 IS
    BEGIN
        RETURN UPPER(SELF.marca) || ' ' || UPPER(SELF.modelo);
    END criterio_orden;
END;
/

CREATE OR REPLACE TYPE tipo_turismo UNDER tipo_vehiculo (
    num_puertas  NUMBER(1),
    cambio       VARCHAR2(10),

    OVERRIDING MEMBER FUNCTION descripcion RETURN VARCHAR2,
    MEMBER FUNCTION calcular_precio (p_dias NUMBER, p_descuento NUMBER) RETURN NUMBER,
    STATIC FUNCTION matricula_valida (p_mat VARCHAR2) RETURN BOOLEAN
) INSTANTIABLE NOT FINAL;
/

CREATE OR REPLACE TYPE BODY tipo_turismo AS
    OVERRIDING MEMBER FUNCTION descripcion RETURN VARCHAR2 IS
    BEGIN
        RETURN 'TURISMO: ' || SELF.marca || ' ' || SELF.modelo || ' (' || SELF.anio || ')';
    END descripcion;

    MEMBER FUNCTION calcular_precio (p_dias NUMBER, p_descuento NUMBER) RETURN NUMBER IS
    BEGIN
        RETURN ROUND(SELF.precio_dia * p_dias * (1 - p_descuento / 100), 2);
    END calcular_precio;

    STATIC FUNCTION matricula_valida (p_mat VARCHAR2) RETURN BOOLEAN IS
    BEGIN
        RETURN REGEXP_LIKE(p_mat, '^\d{4} [A-Z]{3}$');
    END matricula_valida;
END;
/

CREATE OR REPLACE TYPE tipo_furgoneta UNDER tipo_vehiculo (
    carga_kg   NUMBER(5),
    volumen_m3 NUMBER(5,2),

    OVERRIDING MEMBER FUNCTION descripcion RETURN VARCHAR2
) INSTANTIABLE NOT FINAL;
/

CREATE OR REPLACE TYPE BODY tipo_furgoneta AS
    OVERRIDING MEMBER FUNCTION descripcion RETURN VARCHAR2 IS
    BEGIN
        RETURN 'FURGONETA: ' || SELF.marca || ' ' || SELF.modelo || ' - ' || SELF.carga_kg || 'kg';
    END descripcion;
END;
/

-- ── 4. Tablas (Tema 20) ──────────────────────────────────────────────────────

CREATE TABLE turismos OF tipo_turismo (matricula PRIMARY KEY);
CREATE TABLE furgonetas OF tipo_furgoneta (matricula PRIMARY KEY);

CREATE TABLE alquileres (
    id_alquiler    NUMBER(6)    PRIMARY KEY,
    cliente_nombre VARCHAR2(80) NOT NULL,
    fecha_inicio   DATE         NOT NULL,
    dias           NUMBER(3)    NOT NULL,
    vehiculo       tipo_turismo,       
    seguro         tipo_seguro,
    extras         tipo_lista_extras   
);

CREATE TABLE delegaciones (
    id_del            NUMBER(3)    PRIMARY KEY,
    nombre            VARCHAR2(60) NOT NULL,
    ubicacion         tipo_ubicacion,
    vehiculo_estrella REF tipo_turismo   
);

-- ── 5. Datos de ejemplo ──────────────────────────────────────────────────────

INSERT INTO turismos VALUES (tipo_turismo('1234 ABC', 'Seat', 'Ibiza', 2019, 39.90, 5, 'MANUAL'));
INSERT INTO turismos VALUES (tipo_turismo('5678 DEF', 'Volkswagen', 'Golf', 2021, 55.00, 5, 'AUTOMATICO'));
INSERT INTO furgonetas VALUES (tipo_furgoneta('1111 VAN', 'Ford', 'Transit', 2020, 75.00, 1200, 6.5));

INSERT INTO alquileres VALUES (
    1001, 'Luis García', DATE '2024-06-01', 5,
    tipo_turismo('1234 ABC', 'Seat', 'Ibiza', 2019, 39.90, 5, 'MANUAL'),
    tipo_seguro('SEG01', 'Mapfre', 'TODO RIESGO'),
    tipo_lista_extras('GPS', 'Silla Bebé')
);

INSERT INTO delegaciones SELECT 1, 'Delegación Central', tipo_ubicacion('Plaza Mayor', 'Valencia', '960111222'), REF(t) FROM turismos t WHERE matricula = '1234 ABC';

COMMIT;
```

---

## 3. Bloque I — Tema 18: Definición de tipos objeto

*En este bloque practicaremos la creación de tipos base. No usaremos colecciones (VARRAY) ni herencia (UNDER).*

### Ejercicio 1.1 — Tipo sin métodos (`tipo_ubicacion`)
Escribe la sentencia `CREATE OR REPLACE TYPE` para `tipo_ubicacion` con los atributos `calle` (VARCHAR2(100)), `ciudad` (VARCHAR2(50)) y `telefono` (VARCHAR2(15)). 
* Responde en un comentario: ¿Por qué no es necesario crear un `CREATE TYPE BODY` para este tipo?

### Ejercicio 1.2 — Especificación de tipo con métodos (`tipo_seguro`)
Escribe la **especificación** de `tipo_seguro` incluyendo:
- Atributos: `codigo`, `aseguradora`, `cobertura` y `precio_diario`.
- Métodos: la función `calcular_coste(p_dias NUMBER)` y el procedimiento `aplicar_descuento(p_pct NUMBER)`.
- Un método **estático** llamado `validar_cobertura(p_cob VARCHAR2)` que devuelva un `BOOLEAN`.
*(Nota: omite el método ORDER por ahora, lo añadiremos en el bloque II).*

### Ejercicio 1.3 — Cuerpo del tipo (`tipo_seguro`)
Escribe el `CREATE OR REPLACE TYPE BODY` para `tipo_seguro` implementando los métodos de la especificación:
- `calcular_coste`: devuelve el `precio_diario` multiplicado por los días.
- `aplicar_descuento`: reduce el `precio_diario` en el porcentaje indicado.
- `validar_cobertura`: devuelve `TRUE` si la cobertura es 'BASICA', 'FRANQUICIA' o 'TODO RIESGO'.

### Ejercicio 1.4 — Constructor personalizado
Modifica (reescribe) la especificación y el cuerpo de `tipo_seguro` para incluir un **constructor personalizado** que reciba solo el código, la aseguradora y la cobertura. El constructor debe asignar automáticamente un `precio_diario` de 20 si la cobertura es 'TODO RIESGO', y 10 en cualquier otro caso. 
* Responde en un comentario: ¿Por qué el constructor debe declarar obligatoriamente `RETURN SELF AS RESULT`?

### Ejercicio 1.5 — Parámetro SELF y ALTER TYPE
**a)** En un comentario, explica qué error de lógica ocurriría si en un método declaras un parámetro llamado exactamente igual que un atributo (por ejemplo `precio_diario`) y haces `precio_diario := precio_diario;` sin usar la palabra clave `SELF`.
**b) ⚠️ Solo escritura:** Escribe la sentencia `ALTER TYPE` que añadiría el atributo `condiciones VARCHAR2(200)` a `tipo_seguro`.

**Ejercicio 1.6 — Sobrecarga de métodos**
Modifica (reescribe) la especificación y el cuerpo de `tipo_seguro` para añadir una versión sobrecargada del método `aplicar_descuento`. 
* La versión que ya tienes recibe un porcentaje (`p_pct NUMBER`).
* Crea una **nueva versión** que se llame exactamente igual, pero que reciba una cantidad fija a descontar (`p_cantidad_fija NUMBER(5,2)`) y la reste directamente del `precio_diario`.
* Responde en un comentario: ¿Por qué Oracle permite que existan dos métodos con el mismo nombre dentro del mismo tipo objeto sin dar error de compilación?

---

## 4. Bloque II — Tema 19: Utilización, Herencia y Polimorfismo

### Ejercicio 2.1 — Instanciación y variables
Escribe un bloque PL/SQL anónimo que:
1. Declare una variable `v_seguro` de tipo `tipo_seguro`.
2. La instancie usando el constructor personalizado (ej: código 'S1', aseguradora 'Allianz', cobertura 'TODO RIESGO').
3. Llame al método `aplicar_descuento(10)` y muestre por pantalla el nuevo precio diario.

### Ejercicio 2.2 — Herencia y Tipos Abstractos (`NOT INSTANTIABLE`)
Analiza cómo está creado `tipo_vehiculo` en el script inicial.
* Responde en un comentario: ¿Qué significan las cláusulas `NOT INSTANTIABLE` y `NOT FINAL`? ¿Qué error daría si en PL/SQL intentamos hacer `v_veh := NEW tipo_vehiculo(...)`?

### Ejercicio 2.3 — Polimorfismo (`OVERRIDING`)
Escribe un bloque PL/SQL que instancie un `tipo_turismo` y un `tipo_furgoneta`. Llama al método `.descripcion()` de ambos e imprímelos por pantalla con `DBMS_OUTPUT`. 
* Responde en un comentario: ¿Por qué Oracle es capaz de devolver textos distintos si el método se llama exactamente igual en ambos objetos?

### Ejercicio 2.4 — Método MAP
Analiza el método `MAP MEMBER FUNCTION criterio_orden` del script inicial, situado en `tipo_vehiculo`. 
* Escribe en un comentario por qué, según las normas de Oracle, este método debe situarse obligatoriamente en el supertipo (la raíz de la jerarquía) y no en el subtipo `tipo_turismo`.

### Ejercicio 2.5 — Método ORDER
Reescribe (con un `CREATE OR REPLACE TYPE`) la especificación y el cuerpo de `tipo_seguro` para añadirle el método `ORDER MEMBER FUNCTION comparar(otro tipo_seguro) RETURN INTEGER`. Debe devolver 1 si el precio diario del seguro actual es mayor que el del otro, -1 si es menor, y 0 si son iguales.
Crea un pequeño bloque PL/SQL que instancie dos seguros distintos y use el método `comparar` para imprimir cuál es más caro.

**Ejercicio 2.6 — Restricción de polimorfismo (`FINAL`)**
Imagina que por reglas de negocio de RentaCar Levante, la fórmula base para calcular el precio de un vehículo genérico por días **nunca** debe ser modificada por los subtipos.
* Escribe (en comentario, como **⚠️ solo escritura**) cómo quedaría la línea de la especificación de `tipo_vehiculo` para el método `calcular_precio(p_dias NUMBER)` si le añadimos la palabra reservada `FINAL`.
* Responde en el mismo comentario: ¿Qué ocurriría a nivel de base de datos si, tras poner ese `FINAL` en el supertipo, intentáramos escribir un `OVERRIDING MEMBER FUNCTION calcular_precio...` dentro de `tipo_turismo` o `tipo_furgoneta`?

---

## 5. Bloque III — Tema 20: Organización y Acceso (DML)

### Ejercicio 3.1 — Colecciones (VARRAY)
Escribe la sentencia de creación para `tipo_lista_extras` como un VARRAY de un máximo de 8 elementos de tipo VARCHAR2(30). Escribe en un comentario en qué se diferencia un VARRAY de una Nested Table.

### Ejercicio 3.2 — Tablas de Objetos
Escribe la sentencia `CREATE TABLE` para crear la tabla de objetos `turismos` basada en `tipo_turismo`, asignando la matrícula como clave primaria.

### Ejercicio 3.3 — Inserción con objetos embebidos
La tabla `alquileres` tiene columnas simples y objetos completos embebidos. Realiza un `INSERT` en esta tabla inventando los datos, pero instanciando los objetos `tipo_turismo`, `tipo_seguro` y `tipo_lista_extras` directamente en la cláusula `VALUES` (constructor *inline*).

### Ejercicio 3.4 — Consultas SELECT (DML)
Escribe consultas SQL para:
**a)** Mostrar la matrícula, marca y número de puertas de todos los turismos.
**b)** Mostrar el nombre del cliente y el modelo del vehículo de todos los alquileres (necesitas acceder al objeto anidado). ¡Recuerda usar alias de tabla!

### Ejercicio 3.5 — Modificación (UPDATE)
Escribe un `UPDATE` sobre la tabla `alquileres` que cambie la cobertura del seguro anidado a 'BASICA' para el alquiler con ID 1001.

### Ejercicio 3.6 — Cláusula VALUE
Escribe un bloque PL/SQL que recupere el objeto completo del turismo con matrícula '1234 ABC' de la tabla de objetos `turismos` y lo guarde en una variable usando `SELECT VALUE(t) INTO ...`. Imprime la marca del coche recuperado.

### Ejercicio 3.7 — Cláusula REF y DEREF
En la tabla `delegaciones`, la columna `vehiculo_estrella` es un puntero (`REF`).
**a)** Escribe un `INSERT` en delegaciones usando `REF(t)` apuntando a un vehículo existente en la tabla `turismos`.
**b)** Escribe un `SELECT` que muestre el nombre de la delegación y la marca del vehículo estrella usando la función `DEREF()`.

**Ejercicio 3.8 — Eliminación de objetos (DELETE)**
Escribe las siguientes sentencias de borrado y confírmalas con un `SELECT` (o haz un `ROLLBACK` al terminar si quieres mantener los datos):
**a)** Elimina de la tabla de objetos `turismos` aquel vehículo cuya matrícula sea `'1234 ABC'`.
**b)** Elimina de la tabla mixta `alquileres` aquellos registros donde el vehículo asignado (que es un objeto embebido) tenga un precio por día superior a 50€. (Pista: tendrás que usar el alias de la tabla para acceder a `alias.vehiculo.precio_dia` en la cláusula `WHERE`).
* Responde en un comentario: Al hacer un `DELETE` sobre la tabla `turismos`, ¿se elimina también la definición del objeto `tipo_turismo` del sistema? ¿Por qué?

---

## Criterios de entrega
1. Entrega un único fichero `.sql`.
2. Añade al principio del fichero: `-- Autor: Nombre Apellido`.
3. Numera cada ejercicio con un comentario: `-- Ejercicio 1.1`, `-- Ejercicio 2.4`, etc.
4. Los ejercicios marcados como **⚠️ solo escritura** o que piden analizar código deben entregarse **comentados**.
5. El fichero debe ejecutarse de forma secuencial en Oracle 21c sin errores de compilación.
6. Activa **DBMS Output** en SQL Developer antes de ejecutar los bloques PL/SQL.
