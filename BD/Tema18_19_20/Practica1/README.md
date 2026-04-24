# Unidades 18/19/20: Práctica 1 — Bases de Datos Objeto-Relacionales

## 1. Contexto del escenario de trabajo

Para esta práctica trabajamos con el sistema de gestión de **RentaCar Levante**, una empresa de alquiler de
vehículos con delegaciones en varias ciudades. El modelo de datos está diseñado con tipos objeto en Oracle 21c
para que puedas practicar la definición, el uso y la persistencia de objetos, abarcando los contenidos de los
tres temas.

| Tipo / Tabla             | Descripción                                                        |
|--------------------------|--------------------------------------------------------------------|
| `tipo_ubicacion`         | Tipo auxiliar: dirección de una sede o delegación                  |
| `tipo_lista_extras`      | Tipo colección (VARRAY): lista de extras opcionales del alquiler   |
| `tipo_vehiculo`          | Supertipo abstracto: atributos comunes a todo vehículo             |
| `tipo_turismo`           | Subtipo concreto de `tipo_vehiculo`: turismo con puertas y cambio  |
| `tipo_furgoneta`         | Subtipo concreto de `tipo_vehiculo`: furgoneta con carga y volumen |
| `turismos`               | Tabla de objetos `OF tipo_turismo`                                 |
| `furgonetas`             | Tabla de objetos `OF tipo_furgoneta`                               |
| `alquileres`             | Tabla mixta con columnas simples y objetos embebidos               |
| `delegaciones`           | Tabla mixta con columna `REF tipo_turismo`                         |

> **Nota:** Ejecuta el script de inicialización antes de comenzar cada bloque de ejercicios para
> partir de un estado limpio y predecible.

---

## 2. Script de inicialización

```sql
/* =============================================================
   ESQUEMA: RENTACAR LEVANTE — Unidades 18/19/20
   Tipos:   tipo_ubicacion, tipo_lista_extras,
            tipo_vehiculo, tipo_turismo, tipo_furgoneta
   Tablas:  turismos, furgonetas, alquileres, delegaciones
   ============================================================= */

-- ── 0. Limpieza (tablas primero, luego tipos en orden inverso de dependencias) ──

BEGIN EXECUTE IMMEDIATE 'DROP TABLE delegaciones'; EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN EXECUTE IMMEDIATE 'DROP TABLE alquileres'; EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN EXECUTE IMMEDIATE 'DROP TABLE furgonetas'; EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN EXECUTE IMMEDIATE 'DROP TABLE turismos'; EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN EXECUTE IMMEDIATE 'DROP TYPE tipo_furgoneta FORCE'; EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN EXECUTE IMMEDIATE 'DROP TYPE tipo_turismo FORCE'; EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN EXECUTE IMMEDIATE 'DROP TYPE tipo_vehiculo FORCE'; EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN EXECUTE IMMEDIATE 'DROP TYPE tipo_lista_extras'; EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN EXECUTE IMMEDIATE 'DROP TYPE tipo_ubicacion'; EXCEPTION WHEN OTHERS THEN NULL; END;
/

-- ── 1. Tipo auxiliar: dirección ──────────────────────────────────────────────

CREATE OR REPLACE TYPE tipo_ubicacion AS OBJECT (
    calle    VARCHAR2(100),
    ciudad   VARCHAR2(50),
    telefono VARCHAR2(15)
);
/

-- ── 2. Tipo colección: lista de extras del alquiler ──────────────────────────

CREATE OR REPLACE TYPE tipo_lista_extras AS VARRAY(8) OF VARCHAR2(30);
/

-- ── 3. Supertipo abstracto: tipo_vehiculo ────────────────────────────────────

CREATE OR REPLACE TYPE tipo_vehiculo AS OBJECT (
    matricula  VARCHAR2(8),
    marca      VARCHAR2(30),
    modelo     VARCHAR2(50),
    anio       NUMBER(4),
    precio_dia NUMBER(6,2),

    NOT INSTANTIABLE MEMBER FUNCTION descripcion RETURN VARCHAR2,
    MEMBER FUNCTION calcular_precio RETURN NUMBER,
    MEMBER FUNCTION calcular_precio (p_dias NUMBER) RETURN NUMBER
) NOT INSTANTIABLE NOT FINAL;
/

CREATE OR REPLACE TYPE BODY tipo_vehiculo AS
    MEMBER FUNCTION calcular_precio RETURN NUMBER IS
    BEGIN
        RETURN SELF.precio_dia;
    END calcular_precio;

    MEMBER FUNCTION calcular_precio (p_dias NUMBER) RETURN NUMBER IS
    BEGIN
        RETURN ROUND(SELF.precio_dia * p_dias, 2);
    END calcular_precio;
END;
/

-- ── 4. Subtipo concreto: tipo_turismo ────────────────────────────────────────

CREATE OR REPLACE TYPE tipo_turismo UNDER tipo_vehiculo (
    num_puertas  NUMBER(1),
    cambio       VARCHAR2(10),

    OVERRIDING MEMBER FUNCTION descripcion RETURN VARCHAR2,
    MAP MEMBER FUNCTION criterio_orden RETURN VARCHAR2,
    MEMBER FUNCTION calcular_precio (p_dias NUMBER, p_descuento NUMBER) RETURN NUMBER,
    STATIC FUNCTION matricula_valida (p_mat VARCHAR2) RETURN BOOLEAN
) INSTANTIABLE NOT FINAL;
/

CREATE OR REPLACE TYPE BODY tipo_turismo AS
    OVERRIDING MEMBER FUNCTION descripcion RETURN VARCHAR2 IS
    BEGIN
        RETURN 'TURISMO: ' || SELF.marca || ' ' || SELF.modelo || ' (' || SELF.anio || ') — ' || SELF.num_puertas || ' puertas, cambio ' || SELF.cambio;
    END descripcion;

    MAP MEMBER FUNCTION criterio_orden RETURN VARCHAR2 IS
    BEGIN
        RETURN UPPER(SELF.marca) || ' ' || UPPER(SELF.modelo);
    END criterio_orden;

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

-- ── 5. Subtipo concreto: tipo_furgoneta ──────────────────────────────────────

CREATE OR REPLACE TYPE tipo_furgoneta UNDER tipo_vehiculo (
    carga_kg   NUMBER(5),
    volumen_m3 NUMBER(5,2),

    OVERRIDING MEMBER FUNCTION descripcion RETURN VARCHAR2,
    ORDER MEMBER FUNCTION comparar (otra tipo_furgoneta) RETURN INTEGER
) INSTANTIABLE NOT FINAL;
/

CREATE OR REPLACE TYPE BODY tipo_furgoneta AS
    OVERRIDING MEMBER FUNCTION descripcion RETURN VARCHAR2 IS
    BEGIN
        RETURN 'FURGONETA: ' || SELF.marca || ' ' || SELF.modelo || ' — Carga: ' || SELF.carga_kg || ' kg, Volumen: ' || SELF.volumen_m3 || ' m3';
    END descripcion;

    ORDER MEMBER FUNCTION comparar (otra tipo_furgoneta) RETURN INTEGER IS
    BEGIN
        IF    SELF.carga_kg > otra.carga_kg THEN RETURN -1;
        ELSIF SELF.carga_kg < otra.carga_kg THEN RETURN  1;
        ELSE  RETURN 0;
        END IF;
    END comparar;
END;
/

-- ── 6. Tablas ────────────────────────────────────────────────────────────────

CREATE TABLE turismos OF tipo_turismo (matricula PRIMARY KEY);
CREATE TABLE furgonetas OF tipo_furgoneta (matricula PRIMARY KEY);

CREATE TABLE alquileres (
    id_alquiler    NUMBER(6)    PRIMARY KEY,
    cliente_nombre VARCHAR2(80) NOT NULL,
    cliente_dni    VARCHAR2(9),
    fecha_inicio   DATE         NOT NULL,
    dias           NUMBER(3)    NOT NULL,
    vehiculo       tipo_turismo,
    extras         tipo_lista_extras
);

CREATE TABLE delegaciones (
    id_del            NUMBER(3)    PRIMARY KEY,
    nombre            VARCHAR2(60) NOT NULL,
    ubicacion         tipo_ubicacion,
    vehiculo_estrella REF tipo_turismo
);

-- ── 7. Datos de ejemplo ──────────────────────────────────────────────────────

INSERT INTO turismos VALUES (tipo_turismo('1234 ABC', 'Seat', 'Ibiza', 2019, 39.90, 5, 'MANUAL'));
INSERT INTO turismos VALUES (tipo_turismo('5678 DEF', 'Volkswagen', 'Golf', 2021, 55.00, 5, 'AUTOMATICO'));
INSERT INTO turismos VALUES (tipo_turismo('9999 ZZZ', 'BMW', 'Serie 3', 2023, 89.00, 4, 'AUTOMATICO'));

INSERT INTO furgonetas VALUES (tipo_furgoneta('1111 VAN', 'Ford', 'Transit', 2020, 75.00, 1200, 6.5));
INSERT INTO furgonetas VALUES (tipo_furgoneta('2222 TRK', 'Mercedes', 'Sprinter', 2022, 95.00, 1800, 11.0));

INSERT INTO alquileres VALUES (1001, 'Luis García Ruiz', '11111111H', DATE '2024-06-01', 5, tipo_turismo('1234 ABC', 'Seat', 'Ibiza', 2019, 39.90, 5, 'MANUAL'), tipo_lista_extras('GPS', 'Seguro Total'));
INSERT INTO alquileres VALUES (1002, 'María López Vera', '22222222J', DATE '2024-06-15', 14, tipo_turismo('5678 DEF', 'Volkswagen', 'Golf', 2021, 55.00, 5, 'AUTOMATICO'), tipo_lista_extras('Silla Bebé', 'GPS', 'Wi-Fi'));

INSERT INTO delegaciones (id_del, nombre, ubicacion, vehiculo_estrella)
SELECT 1, 'Delegación Valencia Centro', tipo_ubicacion('Plaza del Ayuntamiento 1', 'Valencia', '963100200'), REF(t) FROM turismos t WHERE t.matricula = '1234 ABC';

COMMIT;
```

---

## 3. Bloque I — Tema 18: Definición de tipos objeto

*Ejecuta el script de inicialización antes de comenzar este bloque.*

> Los ejercicios de este bloque te piden que **escribas** las sentencias de creación de tipos tal y como se explican. Al estar ya creados por el script, Oracle los reemplazará correctamente (`CREATE OR REPLACE TYPE`). Para los marcados como **⚠️ solo escritura**, coméntalos para evitar errores por dependencias.

### Ejercicio 1.1 — Tipos simples y colecciones
Escribe las sentencias `CREATE OR REPLACE TYPE` para:
1. `tipo_ubicacion`: con los atributos `calle` (VARCHAR2(100)), `ciudad` (VARCHAR2(50)) y `telefono` (VARCHAR2(15)).
2. `tipo_lista_extras`: como un VARRAY de hasta 8 elementos de tipo VARCHAR2(30).

**Responde en un comentario:** ¿Cuál es la diferencia principal entre un VARRAY y una Nested Table y por qué no hace falta un `BODY` para el `tipo_ubicacion`?

### Ejercicio 1.2 — `tipo_vehiculo`: supertipo abstracto
Escribe **en un solo script** la especificación y el cuerpo de `tipo_vehiculo`:
- Atributos: `matricula` (VARCHAR2(8)), `marca` (VARCHAR2(30)), `modelo` (VARCHAR2(50)), `anio` (NUMBER(4)), `precio_dia` (NUMBER(6,2)).
- Método abstracto `descripcion` (devuelve VARCHAR2).
- Dos versiones sobrecargadas de `calcular_precio`: sin parámetros (devuelve `SELF.precio_dia`) y con el parámetro `p_dias NUMBER` (devuelve el precio multiplicado por los días, redondeado a 2 decimales).
- El tipo debe ser `NOT INSTANTIABLE NOT FINAL`.

**Responde en un comentario:** ¿Qué diferencia hay entre declarar un tipo `NOT FINAL` y `FINAL`?

### Ejercicio 1.3 — `tipo_turismo` y `tipo_furgoneta`: subtipos concretos
Escribe la **especificación y el cuerpo** para los dos subtipos (`UNDER tipo_vehiculo`, `INSTANTIABLE NOT FINAL`):
1. **`tipo_turismo`**:
   - Atributos: `num_puertas` (NUMBER(1)), `cambio` (VARCHAR2(10)).
   - Implementa (`OVERRIDING`) el método `descripcion` devolviendo cadena tipo: `'TURISMO: Seat Ibiza (2019) — 5 puertas, cambio MANUAL'`.
   - `MAP MEMBER FUNCTION criterio_orden RETURN VARCHAR2` (devuelve marca y modelo concatenados en mayúsculas).
   - Sobrecarga de `calcular_precio (p_dias NUMBER, p_descuento NUMBER)` aplicando un porcentaje de descuento al precio total.
   - `STATIC FUNCTION matricula_valida (p_mat VARCHAR2) RETURN BOOLEAN` comprobando formato (ej: `'1234 ABC'`) usando `REGEXP_LIKE(p_mat, '^\d{4} [A-Z]{3}$')`.

2. **`tipo_furgoneta`**:
   - Atributos: `carga_kg` (NUMBER(5)), `volumen_m3` (NUMBER(5,2)).
   - Implementa `descripcion` (ej: `'FURGONETA: Ford Transit — Carga: 1200 kg...'`).
   - `ORDER MEMBER FUNCTION comparar (otra tipo_furgoneta) RETURN INTEGER` ordenando de mayor a menor carga (devuelve -1 si SELF es mayor, 1 si menor, 0 si igual).

### Ejercicio 1.4 — ALTER TYPE y SELF
**a)** El siguiente código tiene un error relacionado con `SELF`. Escribe la versión corregida y explica el fallo en un comentario:
```sql
MEMBER PROCEDURE actualizar_precio (precio_dia NUMBER) IS BEGIN
    precio_dia := precio_dia * 1.10;
END actualizar_precio;
```

**b) ⚠️ Solo escritura** — Escribe la sentencia `ALTER TYPE` para añadir el atributo `color` (VARCHAR2(20)) a `tipo_turismo`. ¿Qué cláusula tendrías que añadir al final de la sentencia si este tipo ya se está usando en la tabla `turismos` para que Oracle no lance un error?

---

## 4. Bloque II — Tema 19: Utilización de los objetos en PL/SQL

*Ejecuta el script de inicialización antes de comenzar este bloque. Asegúrate de tener activa la salida DBMS Output.*

### Ejercicio 2.1 — Manipulación básica y Polimorfismo
Escribe un único bloque PL/SQL que realice lo siguiente:
1. Declare e instancie `v_tur` (`tipo_turismo` de 5 puertas, manual) y `v_fur` (`tipo_furgoneta` de 1200kg). Inventa los datos base.
2. Modifique directamente en la variable instanciada el precio por día del turismo a `60` usando la notación de punto.
3. Imprima usando `DBMS_OUTPUT` el resultado del método `descripcion()` de ambos objetos.
4. Imprima el resultado de llamar a `v_tur.calcular_precio(10)` y `v_tur.calcular_precio(10, 15)` (10 días con 15% descuento).
5. **Comenta:** ¿Por qué Oracle ejecuta código diferente para `descripcion()` en cada variable si el método se llama igual?

### Ejercicio 2.2 — Métodos Estáticos y Comparadores (MAP/ORDER)
Escribe un bloque PL/SQL que:
1. Instancie dos objetos `tipo_turismo` y dos `tipo_furgoneta`.
2. Imprima el resultado devuelto por el método MAP (`criterio_orden()`) de los turismos.
3. Use el método ORDER (`comparar()`) para enfrentar las dos furgonetas e imprima en pantalla la marca de la que tenga mayor capacidad de carga.
4. Pruebe el método estático `tipo_turismo.matricula_valida()` pasándole una matrícula correcta (`'1111 AAA'`) y otra incorrecta (`'11AA'`), imprimiendo si son válidas o no.
5. **Comenta:** ¿Has necesitado la variable instanciada para llamar a `matricula_valida`? ¿Qué diferencia clave hay al elegir entre implementar un MAP o un ORDER en un tipo?

### Ejercicio 2.3 — Constructores personalizados (SELF AS RESULT)
Escribe la **declaración e implementación** de un constructor personalizado `CONSTRUCTOR FUNCTION tipo_turismo` que acepte únicamente matrícula, marca, modelo y precio, asignando internamente y por defecto `anio := 2024`, `num_puertas := 5` y `cambio := 'MANUAL'`.
- **Responde en un comentario:** ¿Por qué la firma del método constructor debe finalizar con `RETURN SELF AS RESULT`?
*(Para probarlo, deberás hacer un CREATE OR REPLACE TYPE FORCE e instanciarlo en un bloque PL/SQL).*

---

## 5. Bloque III — Tema 20: Organización y acceso a objetos en SQL

*Ejecuta el script de inicialización antes de comenzar este bloque.*

### Ejercicio 3.1 — Tablas de Objetos y DML Básico
1. **CREATE:** Escribe (comentada) la sentencia para crear la tabla de objetos `turismos OF tipo_turismo` con la matrícula como PK.
2. **INSERT:** Inserta un nuevo turismo directamente usando `VALUES` y el constructor del objeto (sin variables PL/SQL previas).
3. **UPDATE:** Sube el precio un 10% a todos los turismos del año 2019 o anteriores directamente en la tabla de objetos `turismos`.
4. **DELETE:** Elimina de `furgonetas` aquella con menor `carga_kg` (usando una subconsulta).

### Ejercicio 3.2 — Tablas Mixtas: Embebidos y Colecciones
La tabla `alquileres` tiene columnas normales, el objeto `vehiculo` embebido, y la colección `extras`.
1. Inserta un nuevo alquiler inventado para 7 días, incrustando el constructor del vehículo Volkswagen Golf en la columna `vehiculo` y los extras `'GPS'` y `'Seguro'` en la colección.
2. Realiza un `UPDATE` en `alquileres` para reemplazar *completamente* el objeto embebido del alquiler 1001 por un nuevo `tipo_turismo` con datos modificados.

### Ejercicio 3.3 — Consultas SELECT (Navegación de Objetos)
Escribe una consulta SQL para cada caso:
**a)** Muestra nombre del cliente, modelo del vehículo alquilado (usando notación de punto `a.vehiculo.modelo`) y el coste total estimado (`precio_dia` del vehículo embebido * `dias`).
**b)** Muestra el ID del alquiler y la cantidad de extras contratados usando el método `COUNT` sobre la colección de extras.

### Ejercicio 3.4 — Cláusula VALUE e Inserciones
**a)** Crea una tabla vacía `turismos_baratos` (del tipo `tipo_turismo`). Usando `INSERT INTO ... SELECT VALUE(t)`, copia en ella todos los turismos de la tabla original cuyo precio por día sea menor a 60€.
**b)** **Comenta:** ¿Cuál es la diferencia técnica entre hacer `SELECT t.precio_dia FROM turismos t` y `SELECT VALUE(t) FROM turismos t`?

### Ejercicio 3.5 — Punteros: REF y DEREF
1. Inserta en la tabla `delegaciones` una nueva sede, asignando su `vehiculo_estrella` al turismo `'9999 ZZZ'` usando la función `REF()`.
2. Escribe una consulta SQL sobre la tabla `delegaciones` que muestre el nombre de la delegación y recupere la marca y modelo del vehículo estrella original utilizando la función `DEREF()`.

---

## Criterios de entrega
1. Entrega un único fichero `.sql`.
2. Añade al principio del fichero: `-- Autor: Nombre Apellido`.
3. Numera cada ejercicio con un comentario antes del código.
4. Los ejercicios marcados como **⚠️ solo escritura** deben entregarse **comentados**.
5. El código debe ejecutar sin errores de compilación en Oracle. Usa *DBMS Output* para tus comprobaciones de PL/SQL.