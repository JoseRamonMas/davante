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

> **Nota importante:** Para que el aprendizaje sea progresivo, irás construyendo la base de datos a lo largo de los ejercicios. Ejecuta el siguiente script antes de empezar para asegurar que partes de un entorno limpio y para cargar la jerarquía de vehículos que usarás en el Bloque II.

---

## 2. Script de Inicialización y Limpieza

```sql
/* =============================================================
   ESQUEMA: RENTACAR LEVANTE — Script de Limpieza y Preparación
   ============================================================= */

-- ── 1. Limpieza total (ignora los errores si es la primera vez que lo ejecutas) ──
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

-- ── 2. Creación de la Jerarquía de Vehículos (Para analizar en el Bloque II) ──

CREATE OR REPLACE TYPE tipo_vehiculo AS OBJECT (
    matricula  VARCHAR2(8),
    marca      VARCHAR2(30),
    modelo     VARCHAR2(50),
    anio       NUMBER(4),
    precio_dia NUMBER(6,2),

    NOT INSTANTIABLE MEMBER FUNCTION descripcion RETURN VARCHAR2,
    MEMBER FUNCTION calcular_precio (p_dias NUMBER) RETURN NUMBER,
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
```

---

## 3. Bloque I — Tema 18: Definición de tipos objeto

*En este bloque practicaremos la creación de tipos base construyendo nuestro esquema. No usaremos colecciones (VARRAY) ni herencia (UNDER).*

### Ejercicio 1.1 — Tipo sin métodos (`tipo_ubicacion`)
Escribe y ejecuta la sentencia `CREATE OR REPLACE TYPE` para `tipo_ubicacion` con los atributos `calle` (VARCHAR2(100)), `ciudad` (VARCHAR2(50)) y `telefono` (VARCHAR2(15)). 
* Responde en un comentario: ¿Por qué no es necesario crear un `CREATE TYPE BODY` para este tipo?

### Ejercicio 1.2 — Especificación de tipo con métodos (`tipo_seguro`)
Escribe y ejecuta la **especificación** de `tipo_seguro` incluyendo:
- Atributos: `codigo` (VARCHAR2(10)), `aseguradora` (VARCHAR2(50)), `cobertura` (VARCHAR2(20)) y `precio_diario` (NUMBER(5,2)).
- Métodos: la función `calcular_coste(p_dias NUMBER)` que devuelva NUMBER, y el procedimiento `aplicar_descuento(p_pct NUMBER)`.
- Un método **estático** llamado `validar_cobertura(p_cob VARCHAR2)` que devuelva un `BOOLEAN`.

### Ejercicio 1.3 — Cuerpo del tipo (`tipo_seguro`)
Escribe y ejecuta el `CREATE OR REPLACE TYPE BODY` para `tipo_seguro` implementando los métodos de la especificación:
- `calcular_coste`: devuelve el `precio_diario` multiplicado por los días.
- `aplicar_descuento`: reduce el `precio_diario` en el porcentaje indicado (`p_pct`).
- `validar_cobertura`: devuelve `TRUE` si la cobertura recibida es 'BASICA', 'FRANQUICIA' o 'TODO RIESGO'.

### Ejercicio 1.4 — Constructor personalizado
Modifica (reescribe y vuelve a ejecutar) la especificación y el cuerpo de `tipo_seguro` para incluir un **constructor personalizado** que reciba solo tres parámetros: `p_cod`, `p_aseg` y `p_cob`. El constructor debe asignar automáticamente un `precio_diario` de 20.00 si la cobertura es 'TODO RIESGO' (usa la función UPPER para asegurarte), y 10.00 en cualquier otro caso. 
* Responde en un comentario: ¿Por qué el constructor debe declarar obligatoriamente `RETURN SELF AS RESULT` en lugar de devolver el tipo directamente?

### Ejercicio 1.5 — Parámetro SELF y ALTER TYPE
**a)** En un comentario, explica qué error de lógica ocurriría si en un método declaras un parámetro llamado exactamente igual que un atributo (por ejemplo `precio_diario NUMBER`) y en el cuerpo haces `precio_diario := precio_diario;` sin usar la palabra clave `SELF`.
**b) ⚠️ Solo escritura:** Escribe (pero comenta para no ejecutarla) la sentencia `ALTER TYPE` que añadiría el atributo `condiciones VARCHAR2(200)` a `tipo_seguro`.

**Ejercicio 1.6 — Sobrecarga de métodos**
Modifica una vez más la especificación y el cuerpo de `tipo_seguro` para añadir una versión **sobrecargada** del método `aplicar_descuento`. 
* La versión que ya tienes recibe un porcentaje (`p_pct NUMBER`).
* Crea una **nueva versión** que se llame exactamente igual, pero que reciba una cantidad fija a descontar (`p_cantidad_fija NUMBER(5,2)`) y la reste directamente del `precio_diario`.
* Responde en un comentario: ¿Por qué Oracle permite que existan dos métodos con el mismo nombre dentro del mismo tipo objeto sin dar error de compilación?

---

## 4. Bloque II — Tema 19: Utilización, Herencia y Polimorfismo

### Ejercicio 2.1 — Instanciación y variables
Escribe un bloque PL/SQL anónimo que:
1. Declare una variable `v_seguro` de tipo `tipo_seguro`.
2. La instancie usando el constructor personalizado que creaste en el Ejercicio 1.4 (ej: código 'S1', aseguradora 'Mapfre', cobertura 'TODO RIESGO').
3. Llame al método `aplicar_descuento(10)` (versión porcentaje) y muestre por pantalla el nuevo precio diario.

### Ejercicio 2.2 — Herencia y Tipos Abstractos (`NOT INSTANTIABLE`)
Analiza cómo está creado `tipo_vehiculo` en el script inicial (apartado 2 del documento).
* Responde en un comentario: ¿Qué significan las cláusulas `NOT INSTANTIABLE` y `NOT FINAL`? ¿Qué error daría si en PL/SQL intentamos hacer `v_veh := NEW tipo_vehiculo(...)`?

### Ejercicio 2.3 — Polimorfismo (`OVERRIDING`)
Escribe un bloque PL/SQL que instancie un `tipo_turismo` y un `tipo_furgoneta` (inventa los datos usando el constructor por defecto). Llama al método `.descripcion()` de ambos e imprímelos por pantalla con `DBMS_OUTPUT`. 
* Responde en un comentario: ¿Por qué Oracle es capaz de devolver textos distintos si el método invocado se llama exactamente igual en ambos objetos?

### Ejercicio 2.4 — Método MAP
Analiza el método `MAP MEMBER FUNCTION criterio_orden` del script inicial, situado en `tipo_vehiculo`. 
* Escribe en un comentario por qué, según las normas de Oracle, este método debe situarse obligatoriamente en el supertipo (la raíz de la jerarquía) y no en el subtipo `tipo_turismo`.

### Ejercicio 2.5 — Método ORDER
Reescribe (con un `CREATE OR REPLACE TYPE` y su respectivo `BODY`) el `tipo_seguro` para añadirle el método `ORDER MEMBER FUNCTION comparar(otro tipo_seguro) RETURN INTEGER`. Debe devolver 1 si el `precio_diario` de `SELF` es mayor que el del `otro`, -1 si es menor, y 0 si son iguales.
* Crea un pequeño bloque PL/SQL que instancie dos seguros distintos y use el método `comparar` para imprimir cuál es más caro.

**Ejercicio 2.6 — Restricción de polimorfismo (`FINAL`)**
Imagina que por reglas de negocio, la fórmula base para calcular el precio de un vehículo genérico por días **nunca** debe ser modificada por los subtipos.
* Escribe (en comentario, como **⚠️ solo escritura**) cómo quedaría la línea de la especificación de `tipo_vehiculo` para el método `calcular_precio(p_dias NUMBER)` si le añadimos la palabra reservada `FINAL`.
* Responde en el mismo comentario: ¿Qué ocurriría a nivel de base de datos si, tras poner ese `FINAL` en el supertipo, intentáramos escribir un `OVERRIDING MEMBER FUNCTION calcular_precio...` dentro de `tipo_turismo` o `tipo_furgoneta`?

---

## 5. Bloque III — Tema 20: Organización y Acceso (DML)

### Ejercicio 3.1 — Colecciones (VARRAY)
Escribe y ejecuta la sentencia de creación para `tipo_lista_extras` como un VARRAY de un máximo de 8 elementos de tipo VARCHAR2(30). 
* Escribe en un comentario en qué se diferencia un VARRAY de una Nested Table.

### Ejercicio 3.2 — Tablas de Objetos
Escribe y ejecuta la sentencia `CREATE TABLE` para crear la tabla de objetos `turismos` basada en `tipo_turismo`, asignando la `matricula` como clave primaria.

---
> **¡Pausa para cargar datos!**
> Ahora que has creado con éxito los tipos y la primera tabla, ejecuta el siguiente script para crear el resto de tablas de RentaCar e introducir datos de prueba. *(Nota: Si el script te da error al crear `alquileres`, significa que tus tipos `tipo_seguro` o `tipo_lista_extras` no tienen exactamente la estructura solicitada en los ejercicios anteriores. Revísalos).*

```sql
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

### Ejercicio 3.3 — Inserción con objetos embebidos
La tabla `alquileres` tiene columnas simples y objetos completos embebidos. Realiza un `INSERT` en esta tabla inventando los datos para el alquiler 1002, instanciando los objetos `tipo_turismo`, `tipo_seguro` y `tipo_lista_extras` directamente en la cláusula `VALUES` (constructor *inline*).

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
**a)** Escribe un `INSERT` en delegaciones usando `REF(t)` apuntando al vehículo '5678 DEF' existente en la tabla `turismos`.
**b)** Escribe un `SELECT` que muestre el nombre de la delegación y la marca del vehículo estrella usando la función `DEREF()`.

**Ejercicio 3.8 — Eliminación de objetos (DELETE)**
Escribe las siguientes sentencias de borrado y confírmalas con un `SELECT` (o haz un `ROLLBACK` al terminar si quieres mantener los datos):
**a)** Elimina de la tabla de objetos `turismos` aquel vehículo cuya matrícula sea `'1234 ABC'`.
**b)** Elimina de la tabla mixta `alquileres` aquellos registros donde el vehículo asignado (que es un objeto embebido) tenga un precio por día superior a 50€. (Pista: tendrás que usar el alias de la tabla para acceder a `alias.vehiculo.precio_dia` en la cláusula `WHERE`).
* Responde en un comentario: Al hacer un `DELETE` sobre la tabla `turismos`, ¿se elimina también la definición del objeto `tipo_turismo` de la base de datos? ¿Por qué?

---

## Criterios de entrega
1. Entrega un único fichero `.sql`.
2. Añade al principio del fichero: `-- Autor: Nombre Apellido`.
3. Numera cada ejercicio con un comentario: `-- Ejercicio 1.1`, `-- Ejercicio 2.4`, etc.
4. Los ejercicios marcados como **⚠️ solo escritura** o que piden analizar código deben entregarse **comentados**.
5. El fichero debe ejecutarse de forma secuencial en Oracle 21c sin errores de compilación (salvo en los errores forzados o controlados de teoría).
6. Activa **DBMS Output** en SQL Developer antes de ejecutar los bloques PL/SQL.
