# Bases de Datos Objeto-Relacionales en Oracle 21c

## Índice General

- [Tema 18 — Bases de Datos Objeto-Relacionales](#tema-18--bases-de-datos-objeto-relacionales)
  - [1. Introducción](#1-introducción)
  - [2. Definición y características](#2-definición-y-características)
  - [3. Tipos de datos objeto](#3-tipos-de-datos-objeto)
  - [4. Definición de tipos de datos objeto](#4-definición-de-tipos-de-datos-objeto)
  - [5. Declaración de atributos](#5-declaración-de-atributos)
  - [6. Declaración de métodos](#6-declaración-de-métodos)
  - [7. Métodos estáticos](#7-métodos-estáticos)
  - [8. Parámetro SELF](#8-parámetro-self)
  - [9. Sobrecarga de métodos](#9-sobrecarga-de-métodos)
  - [10. Constructores](#10-constructores)
  - [11. Resumen Tema 18](#11-resumen-tema-18)
- [Tema 19 — Utilización de los Objetos](#tema-19--utilización-de-los-objetos)
  - [1. Introducción](#1-introducción-1)
  - [2. Declaración de objetos como variables](#2-declaración-de-objetos-como-variables)
  - [3. Instancias de objetos](#3-instancias-de-objetos)
  - [4. Referencias a atributos](#4-referencias-a-atributos)
  - [5. Llamadas a métodos](#5-llamadas-a-métodos)
  - [6. Herencia](#6-herencia)
  - [7. Polimorfismo](#7-polimorfismo)
  - [8. Método MAP](#8-método-map)
  - [9. Método ORDER](#9-método-order)
  - [10. Palabras clave avanzadas](#10-palabras-clave-avanzadas)
  - [11. Resumen Tema 19](#11-resumen-tema-19)
- [Tema 20 — Organización de los Tipos de Datos Objeto](#tema-20--organización-de-los-tipos-de-datos-objeto)
  - [1. Introducción](#1-introducción-2)
  - [2. Tipos de datos colección](#2-tipos-de-datos-colección)
  - [3. Tablas de objetos](#3-tablas-de-objetos)
  - [4. Tablas con columnas tipo objeto](#4-tablas-con-columnas-tipo-objeto)
  - [5. Consultas SELECT](#5-consultas-select)
  - [6. Inserción de objetos](#6-inserción-de-objetos)
  - [7. Modificación de objetos](#7-modificación-de-objetos)
  - [8. Eliminación de objetos](#8-eliminación-de-objetos)
  - [9. Cláusula VALUE](#9-cláusula-value)
  - [10. Cláusula REF](#10-cláusula-ref)
  - [11. Resumen Tema 20](#11-resumen-tema-20)
- [Apéndice: Script completo del escenario](#apéndice-script-completo-del-escenario)

---

# Tema 18 — Bases de Datos Objeto-Relacionales

## 1. Introducción

Las bases de datos **relacionales** tradicionales organizan la información en tablas de filas y columnas con tipos de
dato simples (NUMBER, VARCHAR2, DATE…). Este modelo es muy sólido para datos tabulares, pero cuando la
realidad a modelar es compleja (un coche con motor, ruedas y chasis; un alumno con dirección, notas y tutores)
resulta incómodo: hay que fragmentar la información en muchas tablas y reconstruirla con JOINs.

Las bases de datos **objeto-relacionales (BDOR)** añaden al modelo relacional la capacidad de definir **tipos de
datos propios** (como clases en Java), que encapsulan atributos y comportamiento (métodos). Oracle incorpora
esta capacidad desde Oracle 8i y la mantiene y mejora en Oracle 21c.

> **¿Por qué importa en el módulo de DAM?**
> El lenguaje PL/SQL que habéis estudiado se integra directamente con los tipos objeto. Los métodos de un tipo
> se implementan en PL/SQL, por lo que todo lo que sabéis ya es aplicable aquí.

---

## 2. Definición y características

Una BDOR es una **base de datos híbrida**: mantiene la solidez del modelo relacional (SQL, transacciones, índices)
y añade las ventajas de la orientación a objetos (encapsulación, herencia, polimorfismo).

| Característica | BD Relacional | BD Objeto-Relacional |
|---|---|---|
| Tipos de dato | Simples (NUMBER, VARCHAR2…) | Simples **y** definidos por el usuario |
| Lenguaje de consulta | SQL estándar | SQL extendido (VALUE, REF, TREAT…) |
| Encapsulación | No | Sí (métodos dentro del tipo) |
| Herencia | No | Sí (UNDER) |
| Polimorfismo | No | Sí (OVERRIDING) |
| Complejidad | Menor | Mayor |
| Integración con POO | Indirecta | Directa (PL/SQL, Java Stored Procs) |

### Correspondencia con el modelo relacional

| Modelo OO | Modelo Relacional |
|---|---|
| Clase / Tipo | Relación (tabla) |
| Objeto / Instancia | Tupla (fila) |
| Atributo | Columna |
| Método | Procedimiento almacenado |

---

## 3. Tipos de datos objeto

Un **tipo de datos objeto** es una plantilla que el programador define para representar una entidad del mundo
real. Se compone de:

- **Nombre:** identifica el tipo (ej. `tipo_profesor`).
- **Atributos:** propiedades con nombre y tipo de dato (pueden ser tipos Oracle estándar u otros tipos objeto).
- **Métodos:** procedimientos o funciones implementadas en PL/SQL que actúan sobre los atributos.

Un tipo objeto se estructura en dos bloques separados:

```
┌──────────────────────────────────────┐
│           ESPECIFICACIÓN (pública)    │
│  • Declaración de atributos           │
│  • Declaración de métodos (firma)     │
└──────────────────────────────────────┘
┌──────────────────────────────────────┐
│           CUERPO (privado)            │
│  • Implementación de los métodos      │
└──────────────────────────────────────┘
```

Esta separación es fundamental: las aplicaciones cliente solo ven la especificación. Si cambia la implementación
interna de un método, no es necesario recompilar las aplicaciones.

Una **instancia** es un objeto concreto creado a partir de ese tipo con valores reales en sus atributos:
- Tipo: `tipo_profesor` (la plantilla)
- Instancia: el profesor "Ana López Ruiz, DNI 12345678A, Matemáticas, salario 1800"

---

## 4. Definición de tipos de datos objeto

### Sintaxis básica

```sql
-- Crear un tipo nuevo
CREATE TYPE nombre_tipo AS OBJECT (
    atributo1  tipo_dato1,
    atributo2  tipo_dato2,
    -- ... más atributos ...
    MEMBER FUNCTION  nombre_funcion  RETURN tipo_retorno,
    MEMBER PROCEDURE nombre_procedimiento (param tipo_param)
);
/

-- Crear el cuerpo con la implementación
CREATE TYPE BODY nombre_tipo AS
    MEMBER FUNCTION nombre_funcion RETURN tipo_retorno IS
    BEGIN
        -- implementación
    END nombre_funcion;

    MEMBER PROCEDURE nombre_procedimiento (param tipo_param) IS
    BEGIN
        -- implementación
    END nombre_procedimiento;
END;
/
```

### Otras operaciones sobre tipos

```sql
-- Reemplazar un tipo existente (útil durante el desarrollo)
CREATE OR REPLACE TYPE nombre_tipo AS OBJECT ( ... );
/

-- Eliminar un tipo
DROP TYPE nombre_tipo;

-- Eliminar aunque haya dependencias (¡con cuidado!)
DROP TYPE nombre_tipo FORCE;
```

> **⚠️ Importante en Oracle 21c:**
> - Los atributos deben declararse **antes** que los métodos en la especificación.
> - No se pueden declarar constantes, excepciones, cursores ni tipos dentro de la especificación.
> - Si un tipo es referenciado por una tabla, **no se puede eliminar** sin eliminar primero la tabla o usar `FORCE`.

### Primer ejemplo de nuestro escenario: tipo dirección

El tipo `tipo_direccion` lo usaremos como **atributo anidado** dentro de otros tipos (profesor, alumno). Así
evitamos repetir los mismos campos en cada tipo.

```sql
-- Tipo auxiliar: una dirección postal
CREATE OR REPLACE TYPE tipo_direccion AS OBJECT (
    calle          VARCHAR2(100),
    ciudad         VARCHAR2(50),
    codigo_postal  NUMBER(5)
);
/
```

Este tipo no tiene métodos (solo atributos), lo cual es perfectamente válido en Oracle.

---

## 5. Declaración de atributos

Un **atributo** funciona como una variable dentro del tipo objeto. Su tipo puede ser:

- Cualquier tipo Oracle estándar: `NUMBER`, `VARCHAR2`, `DATE`, `CHAR`, `CLOB`, `BLOB`…
- **Otro tipo objeto** previamente declarado.

### Tipos **NO** permitidos como atributos

| Tipo prohibido | Alternativa |
|---|---|
| `LONG`, `LONG RAW` | `CLOB`, `BLOB` |
| `NCHAR`, `NCLOB`, `NVARCHAR2` | `CHAR`, `CLOB`, `VARCHAR2` |
| `ROWID`, `MLSLABEL` | — |
| `BOOLEAN`, `BINARY_INTEGER`, `%TYPE`, `%ROWTYPE` | Tipos SQL equivalentes |
| Tipos declarados dentro de un paquete PL/SQL | Declararlos a nivel de esquema |

> **Nota:** La restricción `NOT NULL` **no está permitida** en atributos de tipos objeto dentro de la especificación.
> Si necesitas esa validación, hazla dentro de los métodos (por ejemplo en el constructor).

### Modificar atributos de un tipo existente con ALTER TYPE

```sql
-- Añadir un atributo nuevo
ALTER TYPE tipo_direccion ADD ATTRIBUTE provincia VARCHAR2(50);

-- Modificar el tamaño de un atributo existente
ALTER TYPE tipo_direccion MODIFY ATTRIBUTE calle VARCHAR2(150);

-- Eliminar un atributo
ALTER TYPE tipo_direccion DROP ATTRIBUTE provincia;
```

> **⚠️ Cuidado:** `ALTER TYPE` puede invalidar los objetos ya creados con ese tipo si estos están almacenados en
> tablas. Oracle intentará revalidarlos, pero puede requerir acción manual con `ALTER TABLE ... UPGRADE INCLUDING DATA`.

### Ejemplo: atributo de tipo objeto anidado

```sql
-- El tipo profesor usa tipo_direccion como uno de sus atributos
CREATE OR REPLACE TYPE tipo_profesor AS OBJECT (
    dni            VARCHAR2(9),
    nombre         VARCHAR2(50),
    apellidos      VARCHAR2(100),
    fecha_nac      DATE,
    especialidad   VARCHAR2(50),
    salario        NUMBER(8,2),
    domicilio      tipo_direccion    -- ← atributo cuyo tipo es otro objeto
);
/
```

Aquí `domicilio` no es un VARCHAR2 ni un NUMBER: es un objeto `tipo_direccion` completo, con sus propios
atributos `calle`, `ciudad` y `codigo_postal`. Esto elimina la necesidad de una tabla separada para las direcciones.

---

## 6. Declaración de métodos

Los métodos **de instancia** se declaran con la palabra clave `MEMBER` y pueden ser de dos tipos:

| Palabra clave | ¿Devuelve valor? | Uso típico |
|---|---|---|
| `MEMBER FUNCTION` | Sí (`RETURN tipo`) | Calcular y devolver un valor derivado de los atributos |
| `MEMBER PROCEDURE` | No | Modificar atributos, realizar acciones con efectos secundarios |

**Regla:** un método **no puede tener el mismo nombre que un atributo** del tipo. La única excepción es cuando
el nombre coincide con el del propio tipo: en ese caso se convierte en un **constructor personalizado** (ver sección 10).

### Ejemplo completo: especificación y cuerpo

Vamos a ampliar `tipo_profesor` con dos métodos:
- `obtener_antiguedad()`: función que calcula los años trabajados desde la fecha de alta.
- `aplicar_subida(porcentaje)`: procedimiento que aumenta el salario un porcentaje dado.

```sql
-- ─── ESPECIFICACIÓN ───────────────────────────────────────────────
CREATE OR REPLACE TYPE tipo_profesor AS OBJECT (
    dni            VARCHAR2(9),
    nombre         VARCHAR2(50),
    apellidos      VARCHAR2(100),
    fecha_alta     DATE,           -- fecha de incorporación al centro
    especialidad   VARCHAR2(50),
    salario        NUMBER(8,2),
    domicilio      tipo_direccion,

    -- Función: devuelve los años completos desde la fecha de alta hasta hoy
    MEMBER FUNCTION obtener_antiguedad RETURN NUMBER,

    -- Procedimiento: aplica una subida salarial (porcentaje entre 0 y 100)
    MEMBER PROCEDURE aplicar_subida (p_porcentaje NUMBER)
);
/
```

```sql
-- ─── CUERPO ───────────────────────────────────────────────────────
CREATE OR REPLACE TYPE BODY tipo_profesor AS

    MEMBER FUNCTION obtener_antiguedad RETURN NUMBER IS
        v_anios NUMBER;
    BEGIN
        -- MONTHS_BETWEEN devuelve meses; dividimos entre 12 para obtener años
        v_anios := TRUNC(MONTHS_BETWEEN(SYSDATE, SELF.fecha_alta) / 12);
        RETURN v_anios;
    END obtener_antiguedad;

    MEMBER PROCEDURE aplicar_subida (p_porcentaje NUMBER) IS
    BEGIN
        -- Validación: el porcentaje debe estar entre 0 y 100
        IF p_porcentaje < 0 OR p_porcentaje > 100 THEN
            RAISE_APPLICATION_ERROR(-20001,
                'El porcentaje debe estar entre 0 y 100');
        END IF;
        -- SELF.salario se refiere al atributo del objeto que invoca el método
        SELF.salario := SELF.salario * (1 + p_porcentaje / 100);
    END aplicar_subida;

END;
/
```

---

## 7. Métodos estáticos

Los métodos `STATIC` pertenecen al **tipo** (como los métodos estáticos en Java), no a una instancia concreta.
Por eso:
- **No tienen acceso** a `SELF` ni a los atributos de instancia.
- Se invocan con `NombreTipo.nombre_metodo()`, sin necesidad de crear un objeto.
- Son útiles para utilidades generales asociadas al tipo (validaciones de formato, factorías, constantes calculadas…).

### Ejemplo: validador de DNI español

```sql
CREATE OR REPLACE TYPE tipo_profesor AS OBJECT (
    dni           VARCHAR2(9),
    nombre        VARCHAR2(50),
    apellidos     VARCHAR2(100),
    fecha_alta    DATE,
    especialidad  VARCHAR2(50),
    salario       NUMBER(8,2),
    domicilio     tipo_direccion,

    MEMBER FUNCTION  obtener_antiguedad RETURN NUMBER,
    MEMBER PROCEDURE aplicar_subida (p_porcentaje NUMBER),

    -- Método estático: valida el formato de un DNI (8 dígitos + 1 letra)
    STATIC FUNCTION dni_valido (p_dni VARCHAR2) RETURN BOOLEAN
);
/

CREATE OR REPLACE TYPE BODY tipo_profesor AS

    MEMBER FUNCTION obtener_antiguedad RETURN NUMBER IS
    BEGIN
        RETURN TRUNC(MONTHS_BETWEEN(SYSDATE, SELF.fecha_alta) / 12);
    END obtener_antiguedad;

    MEMBER PROCEDURE aplicar_subida (p_porcentaje NUMBER) IS
    BEGIN
        IF p_porcentaje < 0 OR p_porcentaje > 100 THEN
            RAISE_APPLICATION_ERROR(-20001, 'Porcentaje fuera de rango');
        END IF;
        SELF.salario := SELF.salario * (1 + p_porcentaje / 100);
    END aplicar_subida;

    STATIC FUNCTION dni_valido (p_dni VARCHAR2) RETURN BOOLEAN IS
        v_letras CONSTANT VARCHAR2(23) := 'TRWAGMYFPDXBNJZSQVHLCKE';
        v_num    NUMBER;
        v_letra  CHAR(1);
    BEGIN
        -- El DNI debe tener exactamente 9 caracteres
        IF LENGTH(p_dni) <> 9 THEN RETURN FALSE; END IF;
        -- Los 8 primeros deben ser dígitos
        IF NOT REGEXP_LIKE(SUBSTR(p_dni,1,8), '^\d{8}$') THEN
            RETURN FALSE;
        END IF;
        -- La letra de control debe coincidir
        v_num   := TO_NUMBER(SUBSTR(p_dni,1,8));
        v_letra := SUBSTR(v_letras, MOD(v_num, 23) + 1, 1);
        RETURN UPPER(SUBSTR(p_dni,9,1)) = v_letra;
    END dni_valido;

END;
/
```

### Invocación de un método estático

```sql
DECLARE
    v_ok BOOLEAN;
BEGIN
    -- No hace falta crear ningún objeto tipo_profesor
    v_ok := tipo_profesor.dni_valido('12345678Z');

    IF v_ok THEN
        DBMS_OUTPUT.PUT_LINE('DNI válido');
    ELSE
        DBMS_OUTPUT.PUT_LINE('DNI inválido');
    END IF;
END;
/
```

---

## 8. Parámetro SELF

`SELF` es un parámetro **implícito** que Oracle pasa automáticamente a todos los métodos de instancia
(`MEMBER`). Representa **el propio objeto** que está invocando el método, de forma análoga a `this` en Java.

### Comportamiento por defecto según el tipo de método

| Tipo de método | Modo de SELF por defecto | Puede modificar atributos |
|---|---|---|
| `MEMBER FUNCTION` | `IN` (solo lectura) | No |
| `MEMBER PROCEDURE` | `IN OUT` (lectura y escritura) | Sí |

Si en una función necesitas modificar atributos (poco común pero posible), debes declarar `SELF` explícitamente
como `IN OUT`:

```sql
MEMBER FUNCTION transformar (SELF IN OUT tipo_profesor) RETURN tipo_profesor
```

### ¿Cuándo es necesario escribir SELF explícitamente?

En la mayoría de los casos Oracle resuelve los atributos sin necesidad de `SELF.`. Sin embargo, es **obligatorio**
cuando existe ambigüedad de nombres entre un atributo y un parámetro del método:

```sql
-- Ejemplo de ambigüedad: el parámetro y el atributo se llaman igual
MEMBER PROCEDURE set_salario (salario NUMBER) IS
BEGIN
    -- Sin SELF: Oracle no sabe si "salario" es el parámetro o el atributo
    SELF.salario := salario;  -- ← izquierda: atributo; derecha: parámetro
END set_salario;
```

Sin el `SELF.` en la parte izquierda, Oracle asignaría el parámetro sobre sí mismo y el atributo quedaría sin cambiar.

### Ejemplo con SELF en un procedimiento que encadena lógica

```sql
MEMBER PROCEDURE actualizar_datos (
    p_especialidad VARCHAR2,
    p_salario      NUMBER
) IS
BEGIN
    SELF.especialidad := p_especialidad;
    SELF.salario      := p_salario;
    -- Llamamos a otro método del mismo objeto usando SELF
    SELF.aplicar_subida(5);   -- aplica además un 5% adicional
END actualizar_datos;
```

---

## 9. Sobrecarga de métodos

La **sobrecarga** permite definir varios métodos con el **mismo nombre** dentro del mismo tipo, diferenciados
por sus parámetros. Oracle determina qué versión ejecutar en función de los argumentos recibidos.

### Reglas para que la sobrecarga sea válida

La sobrecarga es válida si los métodos difieren en:

| Criterio | ¿Permite sobrecarga? |
|---|---|
| Número de parámetros distinto | ✅ Sí |
| Tipo de dato de algún parámetro distinto | ✅ Sí |
| Orden de los tipos de parámetros distinto | ✅ Sí |
| Solo difieren en el nombre del parámetro | ❌ No |
| Solo difieren en el tipo de retorno (funciones) | ❌ No |

### Ejemplo: cálculo de bonificación con sobrecarga

Queremos calcular la bonificación de un profesor de varias formas:
- Sin parámetros → bonificación fija del 10 %.
- Con un porcentaje → bonificación según ese porcentaje.
- Con un porcentaje y un máximo → bonificación con tope.

```sql
CREATE OR REPLACE TYPE tipo_profesor AS OBJECT (
    dni           VARCHAR2(9),
    nombre        VARCHAR2(50),
    apellidos     VARCHAR2(100),
    fecha_alta    DATE,
    especialidad  VARCHAR2(50),
    salario       NUMBER(8,2),
    domicilio     tipo_direccion,

    MEMBER FUNCTION obtener_antiguedad RETURN NUMBER,
    MEMBER PROCEDURE aplicar_subida (p_porcentaje NUMBER),
    STATIC FUNCTION  dni_valido (p_dni VARCHAR2) RETURN BOOLEAN,

    -- Tres versiones sobrecargadas de calcular_bonificacion:
    MEMBER FUNCTION calcular_bonificacion RETURN NUMBER,
    MEMBER FUNCTION calcular_bonificacion (p_pct NUMBER) RETURN NUMBER,
    MEMBER FUNCTION calcular_bonificacion (p_pct NUMBER, p_maximo NUMBER)
        RETURN NUMBER
);
/

CREATE OR REPLACE TYPE BODY tipo_profesor AS

    -- ... otros métodos ...

    -- Versión 1: bonificación fija del 10%
    MEMBER FUNCTION calcular_bonificacion RETURN NUMBER IS
    BEGIN
        RETURN ROUND(SELF.salario * 0.10, 2);
    END calcular_bonificacion;

    -- Versión 2: bonificación con porcentaje personalizado
    MEMBER FUNCTION calcular_bonificacion (p_pct NUMBER) RETURN NUMBER IS
    BEGIN
        RETURN ROUND(SELF.salario * p_pct / 100, 2);
    END calcular_bonificacion;

    -- Versión 3: bonificación con porcentaje y tope máximo
    MEMBER FUNCTION calcular_bonificacion (p_pct NUMBER, p_maximo NUMBER)
        RETURN NUMBER IS
        v_boni NUMBER;
    BEGIN
        v_boni := ROUND(SELF.salario * p_pct / 100, 2);
        -- Si la bonificación supera el máximo, devolvemos el máximo
        RETURN LEAST(v_boni, p_maximo);
    END calcular_bonificacion;

END;
/
```

### Invocación de métodos sobrecargados

```sql
DECLARE
    p1     tipo_profesor;
    v_boni NUMBER;
BEGIN
    p1 := NEW tipo_profesor(
              '11223344A', 'Carlos', 'García Martín',
              DATE '2015-09-01', 'Física', 2200,
              NEW tipo_direccion('Calle Mayor 5', 'Alicante', 03001)
          );

    -- Oracle elige la versión correcta según los argumentos:
    v_boni := p1.calcular_bonificacion();          -- → 220 (10% de 2200)
    v_boni := p1.calcular_bonificacion(15);        -- → 330 (15% de 2200)
    v_boni := p1.calcular_bonificacion(15, 200);   -- → 200 (tope aplicado)

    DBMS_OUTPUT.PUT_LINE('Bonificación con tope: ' || v_boni);
END;
/
```

---

## 10. Constructores

### Constructor por defecto

Oracle genera automáticamente un constructor con el mismo nombre que el tipo y un parámetro por cada
atributo, **en el mismo orden** en que se declararon. Se invoca con la palabra clave `NEW`:

```sql
-- Constructor por defecto de tipo_direccion
-- Oracle genera implícitamente:
--   CONSTRUCTOR FUNCTION tipo_direccion(
--       calle VARCHAR2, ciudad VARCHAR2, codigo_postal NUMBER
--   ) RETURN SELF AS RESULT

-- Uso:
DECLARE
    dir1 tipo_direccion;
BEGIN
    dir1 := NEW tipo_direccion('Avenida Constitución 10', 'Valencia', 46001);
    DBMS_OUTPUT.PUT_LINE(dir1.calle);  -- 'Avenida Constitución 10'
END;
/
```

> **⚠️ El orden importa.** Si declaraste los atributos como `(calle, ciudad, codigo_postal)`, no puedes llamar
> al constructor como `tipo_direccion(46001, 'Valencia', 'Calle Mayor')`. Oracle devolverá un error de tipo.

### Constructor personalizado con `CONSTRUCTOR FUNCTION`

Un constructor personalizado permite añadir **lógica de inicialización** (validaciones, valores por defecto…).
Se declara con `CONSTRUCTOR FUNCTION` y **debe devolver `SELF AS RESULT`**.

```sql
CREATE OR REPLACE TYPE tipo_alumno AS OBJECT (
    nia             NUMBER(6),       -- Número de Identificación del Alumno
    nombre          VARCHAR2(50),
    apellidos       VARCHAR2(100),
    fecha_nac       DATE,
    curso           VARCHAR2(10),
    nota_media      NUMBER(4,2),
    domicilio       tipo_direccion,

    -- Constructor personalizado: calcula automáticamente la nota_media
    -- si no se proporciona (la deja a 0) y valida el NIA
    CONSTRUCTOR FUNCTION tipo_alumno (
        p_nia      NUMBER,
        p_nombre   VARCHAR2,
        p_ape      VARCHAR2,
        p_fnac     DATE,
        p_curso    VARCHAR2,
        p_dom      tipo_direccion
    ) RETURN SELF AS RESULT,

    MEMBER FUNCTION esta_aprobado RETURN BOOLEAN
);
/

CREATE OR REPLACE TYPE BODY tipo_alumno AS

    CONSTRUCTOR FUNCTION tipo_alumno (
        p_nia    NUMBER,
        p_nombre VARCHAR2,
        p_ape    VARCHAR2,
        p_fnac   DATE,
        p_curso  VARCHAR2,
        p_dom    tipo_direccion
    ) RETURN SELF AS RESULT IS
    BEGIN
        -- Validación: el NIA debe ser positivo
        IF p_nia <= 0 THEN
            RAISE_APPLICATION_ERROR(-20010, 'El NIA debe ser un número positivo');
        END IF;
        -- Asignamos cada atributo explícitamente
        SELF.nia        := p_nia;
        SELF.nombre     := p_nombre;
        SELF.apellidos  := p_ape;
        SELF.fecha_nac  := p_fnac;
        SELF.curso      := p_curso;
        SELF.nota_media := 0;      -- valor por defecto, aún sin notas
        SELF.domicilio  := p_dom;
        RETURN;                    -- Oracle devuelve SELF automáticamente
    END tipo_alumno;

    MEMBER FUNCTION esta_aprobado RETURN BOOLEAN IS
    BEGIN
        RETURN SELF.nota_media >= 5;
    END esta_aprobado;

END;
/
```

### Múltiples constructores (sobrecarga)

Podemos declarar varios constructores con parámetros diferentes. El constructor sin dirección usa un valor `NULL`:

```sql
-- Dentro de la especificación de tipo_alumno podríamos añadir:
CONSTRUCTOR FUNCTION tipo_alumno (
    p_nia    NUMBER,
    p_nombre VARCHAR2,
    p_ape    VARCHAR2,
    p_fnac   DATE,
    p_curso  VARCHAR2
) RETURN SELF AS RESULT,   -- sin dirección
```

```sql
-- En el cuerpo:
CONSTRUCTOR FUNCTION tipo_alumno (
    p_nia    NUMBER,
    p_nombre VARCHAR2,
    p_ape    VARCHAR2,
    p_fnac   DATE,
    p_curso  VARCHAR2
) RETURN SELF AS RESULT IS
BEGIN
    SELF.nia        := p_nia;
    SELF.nombre     := p_nombre;
    SELF.apellidos  := p_ape;
    SELF.fecha_nac  := p_fnac;
    SELF.curso      := p_curso;
    SELF.nota_media := 0;
    SELF.domicilio  := NULL;   -- sin dirección por ahora
    RETURN;
END tipo_alumno;
```

---

## 11. Resumen Tema 18

```
BASES DE DATOS OBJETO-RELACIONALES
│
├── Definición     → Híbrido relacional + OO; el usuario define sus propios tipos
│
├── Tipo objeto    → Nombre + Atributos + Métodos
│   ├── Especificación (pública):  atributos y firmas de métodos
│   └── Cuerpo (privado):          implementación de métodos en PL/SQL
│
├── Atributos      → Como variables: nombre + tipo (puede ser otro tipo objeto)
│   └── ALTER TYPE: ADD / MODIFY / DROP ATTRIBUTE
│
├── Métodos        → MEMBER FUNCTION (devuelve valor)  /  MEMBER PROCEDURE (no devuelve)
│
├── Métodos STATIC → Pertenecen al tipo, no a la instancia; sin acceso a SELF
│
├── SELF           → Referencia al objeto que invoca el método
│   ├── FUNCTION  → modo IN por defecto (solo lectura)
│   └── PROCEDURE → modo IN OUT por defecto (lectura/escritura)
│
├── Sobrecarga     → Mismo nombre, distinta firma de parámetros
│   ├── Válida:    número, tipo u orden de parámetros distinto
│   └── Inválida:  solo difieren en nombre de param o tipo de retorno
│
└── Constructores
    ├── Por defecto:    generado por Oracle; mismo nombre que el tipo
    └── Personalizado:  CONSTRUCTOR FUNCTION … RETURN SELF AS RESULT
```

---

# Tema 19 — Utilización de los Objetos

## 1. Introducción

En el Tema 18 aprendimos a **definir** tipos objeto (la plantilla). Ahora aprenderemos a **usarlos**: declarar
variables de esos tipos, crear instancias concretas, acceder a sus atributos, llamar a sus métodos y aprovechar la
herencia y el polimorfismo.

---

## 2. Declaración de objetos como variables

Una vez definido un tipo objeto, podemos declarar variables de ese tipo **en cualquier lugar donde se acepten
declaraciones**: bloques PL/SQL, parámetros de funciones/procedimientos, tipos de retorno, columnas de tablas...

### En un bloque PL/SQL

```sql
DECLARE
    v_profe  tipo_profesor;    -- variable sin inicializar (NULL)
    v_alumno tipo_alumno;      -- igual, NULL hasta que se use NEW
BEGIN
    -- Aquí v_profe y v_alumno son NULL.
    -- Intentar acceder a sus atributos sin inicializar produce ORA-06530.
    NULL;
END;
/
```

### Como parámetros de procedimientos y funciones

```sql
-- Procedimiento que recibe un objeto profesor
CREATE OR REPLACE PROCEDURE registrar_profesor (p_prof IN tipo_profesor) IS
BEGIN
    DBMS_OUTPUT.PUT_LINE('Registrando: ' || p_prof.nombre
                         || ' ' || p_prof.apellidos);
END registrar_profesor;
/

-- Función que devuelve un objeto alumno dado su NIA
CREATE OR REPLACE FUNCTION buscar_alumno (p_nia NUMBER) RETURN tipo_alumno IS
    v_alum tipo_alumno;
BEGIN
    -- En la práctica, aquí iría un SELECT INTO desde la tabla
    -- (lo veremos en el Tema 20). Por ahora devolvemos un objeto de prueba.
    v_alum := NEW tipo_alumno(
                  p_nia, 'Lucía', 'Fernández Mora',
                  DATE '2005-03-15', '2DAM',
                  NEW tipo_direccion('Calle Poeta 3', 'Murcia', 30001)
              );
    RETURN v_alum;
END buscar_alumno;
/
```

---

## 3. Instancias de objetos

Para crear una instancia (objeto concreto con valores reales) se usa **`NEW`** seguido del nombre del
constructor:

```sql
variable := NEW nombre_tipo (valor1, valor2, ..., valorN);
```

### Ejemplo completo: crear instancias de profesor y alumno

```sql
DECLARE
    -- Variables de tipo objeto
    v_dir1   tipo_direccion;
    v_profe1 tipo_profesor;
    v_alum1  tipo_alumno;

BEGIN
    -- Instanciamos primero la dirección (objeto auxiliar)
    v_dir1 := NEW tipo_direccion(
                  'Calle Rosales 22',  -- calle
                  'Valencia',          -- ciudad
                  46010                -- codigo_postal
              );

    -- Instanciamos el profesor usando la dirección ya creada
    -- El orden debe coincidir exactamente con la declaración de atributos:
    -- (dni, nombre, apellidos, fecha_alta, especialidad, salario, domicilio)
    v_profe1 := NEW tipo_profesor(
                    '12345678A',
                    'Ana',
                    'López Ruiz',
                    DATE '2018-09-01',
                    'Matemáticas',
                    1850.00,
                    v_dir1           -- atributo de tipo tipo_direccion
                );

    -- Instanciamos un alumno usando el constructor personalizado
    -- (sin dirección en este caso)
    v_alum1 := NEW tipo_alumno(
                   100001,
                   'Marcos',
                   'Sánchez Gil',
                   DATE '2006-07-20',
                   '1DAM'
               );

    -- Mostramos datos
    DBMS_OUTPUT.PUT_LINE('Profesor: ' || v_profe1.nombre
                         || ' — Antigüedad: '
                         || v_profe1.obtener_antiguedad() || ' años');

    DBMS_OUTPUT.PUT_LINE('Alumno: ' || v_alum1.nombre
                         || ' — Aprobado: '
                         || CASE WHEN v_alum1.esta_aprobado
                                 THEN 'Sí' ELSE 'No' END);
END;
/
```

> **Error habitual:** pasar los valores en orden incorrecto.
> Si `tipo_profesor` declara primero `dni` y luego `nombre`, pero en el `NEW` ponemos el nombre primero,
> Oracle dará un error `ORA-06553: PLS-306: wrong number or types of arguments`.

---

## 4. Referencias a atributos

La sintaxis para acceder a un atributo es `objeto.atributo`, igual que en Java.

### A. Leer el valor de un atributo

```sql
DECLARE
    v_profe1  tipo_profesor;
    v_nombre  VARCHAR2(50);
    v_cp      NUMBER;
BEGIN
    v_profe1 := NEW tipo_profesor(
                    '12345678A', 'Ana', 'López Ruiz',
                    DATE '2018-09-01', 'Matemáticas', 1850,
                    NEW tipo_direccion('Calle Rosales 22', 'Valencia', 46010)
                );

    -- Leer un atributo simple
    v_nombre := v_profe1.nombre;                      -- 'Ana'

    -- Leer un atributo de un objeto anidado (dos niveles de punto)
    v_cp := v_profe1.domicilio.codigo_postal;         -- 46010

    DBMS_OUTPUT.PUT_LINE('Nombre: '   || v_nombre);
    DBMS_OUTPUT.PUT_LINE('C. Postal: ' || v_cp);
END;
/
```

### B. Pasar un atributo como argumento a una función

```sql
DECLARE
    v_profe1 tipo_profesor;
BEGIN
    v_profe1 := NEW tipo_profesor(
                    '12345678A', 'Ana', 'López Ruiz',
                    DATE '2018-09-01', 'Matemáticas', 1850,
                    NEW tipo_direccion('Calle Rosales 22', 'Valencia', 46010)
                );

    -- Pasamos el atributo dni como argumento
    IF tipo_profesor.dni_valido(v_profe1.dni) THEN
        DBMS_OUTPUT.PUT_LINE('El DNI del profesor es válido');
    END IF;
END;
/
```

### C. Modificar el valor de un atributo directamente

```sql
DECLARE
    v_profe1 tipo_profesor;
BEGIN
    v_profe1 := NEW tipo_profesor(
                    '12345678A', 'Ana', 'López Ruiz',
                    DATE '2018-09-01', 'Matemáticas', 1850,
                    NEW tipo_direccion('Calle Rosales 22', 'Valencia', 46010)
                );

    -- Modificar atributo simple
    v_profe1.especialidad := 'Álgebra y Cálculo';

    -- Modificar atributo de objeto anidado
    v_profe1.domicilio.ciudad := 'Castellón';

    -- Sustituir el objeto anidado completo
    v_profe1.domicilio := NEW tipo_direccion('Avda. Mar 1', 'Castellón', 12001);

    DBMS_OUTPUT.PUT_LINE('Nueva ciudad: ' || v_profe1.domicilio.ciudad);
END;
/
```

### D. Acceso a atributos de objetos anidados en profundidad

Si un objeto `A` tiene un atributo de tipo `B`, y `B` tiene un atributo de tipo `C`, puedes encadenar puntos:
`a.atrib_b.atrib_c`. No hay límite de niveles, aunque más de dos o tres suele ser señal de diseño complejo.

---

## 5. Llamadas a métodos

La sintaxis es también `objeto.metodo(argumentos)`, igual que acceder a un atributo.

### A. Método sin parámetros

```sql
DECLARE
    v_profe1   tipo_profesor;
    v_antig    NUMBER;
    v_boni     NUMBER;
BEGIN
    v_profe1 := NEW tipo_profesor(
                    '12345678A', 'Ana', 'López Ruiz',
                    DATE '2010-09-01', 'Matemáticas', 1850,
                    NEW tipo_direccion('Calle Rosales 22', 'Valencia', 46010)
                );

    v_antig := v_profe1.obtener_antiguedad();
    DBMS_OUTPUT.PUT_LINE('Antigüedad: ' || v_antig || ' años');
    -- Resultado (si estamos en 2025): 15 años

    v_boni  := v_profe1.calcular_bonificacion();  -- sobrecarga sin param
    DBMS_OUTPUT.PUT_LINE('Bonificación estándar: ' || v_boni || ' €');
    -- Resultado: 185 €
END;
/
```

### B. Método con parámetros

```sql
BEGIN
    v_profe1.aplicar_subida(8);   -- subida del 8%
    DBMS_OUTPUT.PUT_LINE('Nuevo salario: ' || v_profe1.salario);
    -- 1850 * 1.08 = 1998 €
END;
/
```

### C. Encadenamiento de métodos sobre objetos anidados

Si el método de un objeto devuelve otro objeto, podemos encadenar llamadas. El método de la izquierda debe
devolver un objeto del tipo al que pertenece el método de la derecha:

```sql
-- Imaginemos que tipo_asignatura tiene un método get_profesor() que devuelve
-- un tipo_profesor. Entonces podemos escribir:
v_salario := v_asignatura.get_profesor().salario;
```

### D. Diferencia entre MEMBER y STATIC en la invocación

```sql
DECLARE
    v_profe1 tipo_profesor;
BEGIN
    v_profe1 := NEW tipo_profesor( ... );

    -- MEMBER: se invoca sobre la instancia
    v_profe1.aplicar_subida(5);

    -- STATIC: se invoca sobre el tipo (sin instancia)
    IF tipo_profesor.dni_valido('98765432W') THEN
        DBMS_OUTPUT.PUT_LINE('DNI correcto');
    END IF;
END;
/
```

---

## 6. Herencia

La **herencia** permite crear un nuevo tipo (**subtipo**) que extiende a uno existente (**supertipo**), heredando
todos sus atributos y métodos, y pudiendo añadir los suyos propios.

En Oracle se declara con `UNDER`:

```sql
CREATE OR REPLACE TYPE subtipo UNDER supertipo (
    atributo_extra tipo_dato,
    MEMBER FUNCTION metodo_nuevo RETURN tipo_retorno
) [FINAL | NOT FINAL];
/
```

### Control de la extensibilidad

| Palabra clave | Significado |
|---|---|
| `NOT FINAL` | El tipo **puede** tener subtipos. Es necesario declararlo explícitamente si queremos que pueda ser extendido. |
| `FINAL` (por defecto) | El tipo **no puede** tener subtipos. Es el comportamiento por defecto si no se especifica nada. |

### Ejemplo: jerarquía Persona → Profesor / Alumno

Vamos a refactorizar nuestro escenario para que `tipo_persona` sea el supertipo y `tipo_profesor`/`tipo_alumno`
sean subtipos:

```sql
-- ─── SUPERTIPO ────────────────────────────────────────────────────
CREATE OR REPLACE TYPE tipo_persona AS OBJECT (
    dni          VARCHAR2(9),
    nombre       VARCHAR2(50),
    apellidos    VARCHAR2(100),
    fecha_nac    DATE,
    domicilio    tipo_direccion,

    MEMBER FUNCTION obtener_nombre_completo RETURN VARCHAR2
) NOT FINAL;    -- ← imprescindible para poder crear subtipos
/

CREATE OR REPLACE TYPE BODY tipo_persona AS
    MEMBER FUNCTION obtener_nombre_completo RETURN VARCHAR2 IS
    BEGIN
        RETURN SELF.nombre || ' ' || SELF.apellidos;
    END obtener_nombre_completo;
END;
/
```

```sql
-- ─── SUBTIPO PROFESOR ──────────────────────────────────────────────
CREATE OR REPLACE TYPE tipo_profesor UNDER tipo_persona (
    -- Atributos adicionales (los de tipo_persona se heredan automáticamente)
    fecha_alta    DATE,
    especialidad  VARCHAR2(50),
    salario       NUMBER(8,2),

    MEMBER FUNCTION  obtener_antiguedad  RETURN NUMBER,
    MEMBER PROCEDURE aplicar_subida      (p_porcentaje NUMBER),
    MEMBER FUNCTION  calcular_bonificacion RETURN NUMBER,
    MEMBER FUNCTION  calcular_bonificacion (p_pct NUMBER) RETURN NUMBER,
    STATIC FUNCTION  dni_valido          (p_dni VARCHAR2) RETURN BOOLEAN
) NOT FINAL;    -- lo dejamos NOT FINAL por si queremos subtipos de profesor
/

CREATE OR REPLACE TYPE BODY tipo_profesor AS

    MEMBER FUNCTION obtener_antiguedad RETURN NUMBER IS
    BEGIN
        RETURN TRUNC(MONTHS_BETWEEN(SYSDATE, SELF.fecha_alta) / 12);
    END obtener_antiguedad;

    MEMBER PROCEDURE aplicar_subida (p_porcentaje NUMBER) IS
    BEGIN
        IF p_porcentaje < 0 OR p_porcentaje > 100 THEN
            RAISE_APPLICATION_ERROR(-20001, 'Porcentaje fuera de rango');
        END IF;
        SELF.salario := SELF.salario * (1 + p_porcentaje / 100);
    END aplicar_subida;

    MEMBER FUNCTION calcular_bonificacion RETURN NUMBER IS
    BEGIN
        RETURN ROUND(SELF.salario * 0.10, 2);
    END calcular_bonificacion;

    MEMBER FUNCTION calcular_bonificacion (p_pct NUMBER) RETURN NUMBER IS
    BEGIN
        RETURN ROUND(SELF.salario * p_pct / 100, 2);
    END calcular_bonificacion;

    STATIC FUNCTION dni_valido (p_dni VARCHAR2) RETURN BOOLEAN IS
        v_letras CONSTANT VARCHAR2(23) := 'TRWAGMYFPDXBNJZSQVHLCKE';
        v_num   NUMBER;
        v_letra CHAR(1);
    BEGIN
        IF LENGTH(p_dni) <> 9 THEN RETURN FALSE; END IF;
        IF NOT REGEXP_LIKE(SUBSTR(p_dni,1,8), '^\d{8}$') THEN
            RETURN FALSE;
        END IF;
        v_num   := TO_NUMBER(SUBSTR(p_dni,1,8));
        v_letra := SUBSTR(v_letras, MOD(v_num, 23) + 1, 1);
        RETURN UPPER(SUBSTR(p_dni,9,1)) = v_letra;
    END dni_valido;

END;
/
```

```sql
-- ─── SUBTIPO ALUMNO ───────────────────────────────────────────────
CREATE OR REPLACE TYPE tipo_alumno UNDER tipo_persona (
    nia         NUMBER(6),
    curso       VARCHAR2(10),
    nota_media  NUMBER(4,2),

    MEMBER FUNCTION esta_aprobado RETURN BOOLEAN
) FINAL;    -- no permite subtipos de alumno
/

CREATE OR REPLACE TYPE BODY tipo_alumno AS
    MEMBER FUNCTION esta_aprobado RETURN BOOLEAN IS
    BEGIN
        RETURN SELF.nota_media >= 5;
    END esta_aprobado;
END;
/
```

### Instanciar un subtipo (todos los atributos, incluidos los heredados)

Al crear una instancia de un subtipo, el constructor espera **primero los atributos del supertipo** (en el orden
en que fueron declarados) y luego los propios del subtipo:

```sql
DECLARE
    v_profe1 tipo_profesor;
    v_alum1  tipo_alumno;
BEGIN
    -- Constructor de tipo_profesor:
    -- Hereda de tipo_persona: (dni, nombre, apellidos, fecha_nac, domicilio)
    -- Propios:                (fecha_alta, especialidad, salario)
    v_profe1 := NEW tipo_profesor(
                    '12345678A',                                   -- dni
                    'Ana',                                         -- nombre
                    'López Ruiz',                                  -- apellidos
                    DATE '1980-04-10',                             -- fecha_nac
                    NEW tipo_direccion('C/Rosales 22','Valencia',46010), -- domicilio
                    DATE '2010-09-01',                             -- fecha_alta
                    'Matemáticas',                                 -- especialidad
                    1850                                           -- salario
                );

    -- Constructor de tipo_alumno:
    -- Hereda de tipo_persona: (dni, nombre, apellidos, fecha_nac, domicilio)
    -- Propios:                (nia, curso, nota_media)
    v_alum1 := NEW tipo_alumno(
                   '98765432W',                                    -- dni
                   'Lucía',                                        -- nombre
                   'Pérez Vidal',                                  -- apellidos
                   DATE '2006-05-30',                              -- fecha_nac
                   NEW tipo_direccion('Avda. Mar 3','Alicante',03001), -- domicilio
                   100025,                                         -- nia
                   '1DAM',                                         -- curso
                   7.8                                             -- nota_media
               );

    DBMS_OUTPUT.PUT_LINE(v_profe1.obtener_nombre_completo());
    -- 'Ana López Ruiz'  ← método heredado de tipo_persona

    DBMS_OUTPUT.PUT_LINE(v_alum1.obtener_nombre_completo());
    -- 'Lucía Pérez Vidal'
END;
/
```

---

## 7. Polimorfismo

El **polimorfismo** permite que un método con el mismo nombre se comporte de forma diferente en el supertipo
y en el subtipo. Para reemplazar la implementación del supertipo en el subtipo se usa `OVERRIDING`.

### Ejemplo: `obtener_nombre_completo` con comportamiento diferente en alumno

```sql
-- El supertipo tipo_persona ya tiene obtener_nombre_completo()
-- que devuelve "nombre apellidos".
-- En el subtipo tipo_alumno queremos que devuelva "nombre apellidos (curso)"

CREATE OR REPLACE TYPE tipo_alumno UNDER tipo_persona (
    nia         NUMBER(6),
    curso       VARCHAR2(10),
    nota_media  NUMBER(4,2),

    -- Indicamos que vamos a REEMPLAZAR la implementación del supertipo
    OVERRIDING MEMBER FUNCTION obtener_nombre_completo RETURN VARCHAR2,
    MEMBER FUNCTION esta_aprobado RETURN BOOLEAN
) FINAL;
/

CREATE OR REPLACE TYPE BODY tipo_alumno AS

    OVERRIDING MEMBER FUNCTION obtener_nombre_completo RETURN VARCHAR2 IS
    BEGIN
        -- La implementación del supertipo devolvía "nombre apellidos"
        -- La nuestra añade el curso entre paréntesis
        RETURN SELF.nombre || ' ' || SELF.apellidos
               || ' (' || SELF.curso || ')';
    END obtener_nombre_completo;

    MEMBER FUNCTION esta_aprobado RETURN BOOLEAN IS
    BEGIN
        RETURN SELF.nota_media >= 5;
    END esta_aprobado;

END;
/
```

```sql
DECLARE
    v_persona tipo_persona;
    v_alumno  tipo_alumno;
BEGIN
    v_persona := NEW tipo_persona(
                     '11111111A', 'Pedro', 'García',
                     DATE '1990-01-01',
                     NEW tipo_direccion('C/Sol 1', 'Madrid', 28001)
                 );

    v_alumno := NEW tipo_alumno(
                    '22222222B', 'Sara', 'Molina',
                    DATE '2006-06-06',
                    NEW tipo_direccion('C/Luna 2', 'Sevilla', 41001),
                    100030, '2DAM', 8.5
                );

    -- El mismo método, comportamiento diferente según el tipo real del objeto
    DBMS_OUTPUT.PUT_LINE(v_persona.obtener_nombre_completo());
    -- → 'Pedro García'

    DBMS_OUTPUT.PUT_LINE(v_alumno.obtener_nombre_completo());
    -- → 'Sara Molina (2DAM)'   ← versión OVERRIDING
END;
/
```

---

## 8. Método MAP

El método `MAP` permite a Oracle **comparar y ordenar** instancias de un tipo objeto, convirtiendo cada objeto
a un valor escalar (NUMBER, VARCHAR2, DATE…) que se usa para la comparación.

**Reglas:**
- Solo puede haber **un** método MAP por tipo objeto.
- Debe ser una `MEMBER FUNCTION` sin parámetros (aparte de SELF).
- El tipo de retorno debe ser escalar: `NUMBER`, `VARCHAR2`, `DATE`, `CHAR`, `INTEGER`, `REAL`.
- Se declara con la palabra clave `MAP` antes de `MEMBER`.

### Ejemplo: ordenar profesores por apellidos y nombre

```sql
CREATE OR REPLACE TYPE tipo_profesor UNDER tipo_persona (
    fecha_alta   DATE,
    especialidad VARCHAR2(50),
    salario      NUMBER(8,2),

    -- MAP: convierte el objeto a un string que Oracle usará para ordenar
    MAP MEMBER FUNCTION criterio_orden RETURN VARCHAR2,

    MEMBER FUNCTION  obtener_antiguedad  RETURN NUMBER,
    MEMBER PROCEDURE aplicar_subida      (p_porcentaje NUMBER)
    -- ... resto de métodos
) NOT FINAL;
/

CREATE OR REPLACE TYPE BODY tipo_profesor AS

    MAP MEMBER FUNCTION criterio_orden RETURN VARCHAR2 IS
    BEGIN
        -- Ordenamos por apellidos y luego por nombre
        -- RPAD garantiza que todos los valores tengan la misma longitud
        -- para que la comparación lexicográfica sea correcta
        RETURN RPAD(UPPER(SELF.apellidos), 100) || UPPER(SELF.nombre);
    END criterio_orden;

    MEMBER FUNCTION obtener_antiguedad RETURN NUMBER IS
    BEGIN
        RETURN TRUNC(MONTHS_BETWEEN(SYSDATE, SELF.fecha_alta) / 12);
    END obtener_antiguedad;

    MEMBER PROCEDURE aplicar_subida (p_porcentaje NUMBER) IS
    BEGIN
        IF p_porcentaje < 0 OR p_porcentaje > 100 THEN
            RAISE_APPLICATION_ERROR(-20001, 'Porcentaje fuera de rango');
        END IF;
        SELF.salario := SELF.salario * (1 + p_porcentaje / 100);
    END aplicar_subida;

END;
/
```

Una vez definido MAP, Oracle puede usar `ORDER BY`, `MIN`, `MAX` y comparaciones (`<`, `>`, `=`) directamente
sobre la tabla de objetos (lo veremos en el Tema 20):

```sql
-- Esto funcionará gracias al método MAP
SELECT p.nombre, p.apellidos
FROM   profesores p
ORDER BY VALUE(p);   -- Oracle llama a criterio_orden() internamente
```

---

## 9. Método ORDER

El método `ORDER` es la alternativa a MAP para **comparar dos objetos del mismo tipo**. Mientras MAP convierte
cada objeto a un escalar, ORDER recibe **el objeto con el que compararse** como parámetro y devuelve:

| Retorno | Significado |
|---|---|
| Valor **negativo** | `SELF` es menor que el parámetro |
| `0` | `SELF` es igual al parámetro |
| Valor **positivo** | `SELF` es mayor que el parámetro |
| `NULL` | alguno de los dos objetos es `NULL` |

**Reglas:**
- Solo puede haber **un** método ORDER por tipo (y no puede coexistir con MAP en el mismo tipo).
- El parámetro debe ser del mismo tipo que SELF.
- Para ordenar grandes volúmenes de datos, MAP suele ser más eficiente.

### Ejemplo: comparar alumnos por nota media

```sql
CREATE OR REPLACE TYPE tipo_alumno UNDER tipo_persona (
    nia         NUMBER(6),
    curso       VARCHAR2(10),
    nota_media  NUMBER(4,2),

    OVERRIDING MEMBER FUNCTION obtener_nombre_completo RETURN VARCHAR2,
    MEMBER FUNCTION esta_aprobado RETURN BOOLEAN,

    -- ORDER: compara este alumno con otro por nota_media
    ORDER MEMBER FUNCTION comparar (otro tipo_alumno) RETURN INTEGER
) FINAL;
/

CREATE OR REPLACE TYPE BODY tipo_alumno AS

    OVERRIDING MEMBER FUNCTION obtener_nombre_completo RETURN VARCHAR2 IS
    BEGIN
        RETURN SELF.nombre || ' ' || SELF.apellidos
               || ' (' || SELF.curso || ')';
    END obtener_nombre_completo;

    MEMBER FUNCTION esta_aprobado RETURN BOOLEAN IS
    BEGIN
        RETURN SELF.nota_media >= 5;
    END esta_aprobado;

    ORDER MEMBER FUNCTION comparar (otro tipo_alumno) RETURN INTEGER IS
    BEGIN
        -- Ordenamos de mayor a menor nota (ponemos el mejor alumno primero)
        IF SELF.nota_media > otro.nota_media THEN
            RETURN -1;   -- SELF va antes (es "menor" en orden DESC)
        ELSIF SELF.nota_media < otro.nota_media THEN
            RETURN 1;
        ELSE
            RETURN 0;
        END IF;
    END comparar;

END;
/
```

```sql
-- Uso del método ORDER en un bloque PL/SQL
DECLARE
    a1 tipo_alumno;
    a2 tipo_alumno;
BEGIN
    a1 := NEW tipo_alumno('11111111A','Marcos','Gil',DATE '2007-01-01',
              NEW tipo_direccion('C/A 1','Valencia',46001),100001,'1DAM',6.5);
    a2 := NEW tipo_alumno('22222222B','Lucía','Vera',DATE '2007-02-02',
              NEW tipo_direccion('C/B 2','Valencia',46002),100002,'1DAM',8.2);

    IF a1.comparar(a2) < 0 THEN
        DBMS_OUTPUT.PUT_LINE(a1.nombre || ' tiene mejor nota');
    ELSIF a1.comparar(a2) > 0 THEN
        DBMS_OUTPUT.PUT_LINE(a2.nombre || ' tiene mejor nota');
    ELSE
        DBMS_OUTPUT.PUT_LINE('Empate');
    END IF;
    -- Resultado: 'Lucía tiene mejor nota'
END;
/
```

---

## 10. Palabras clave avanzadas

### 10.1. FINAL (en métodos)

Además de `FINAL` en tipos (que impide crear subtipos), puede aplicarse a **métodos individuales** para impedir
que los subtipos los reemplacen con `OVERRIDING`:

```sql
CREATE OR REPLACE TYPE tipo_persona AS OBJECT (
    dni       VARCHAR2(9),
    nombre    VARCHAR2(50),
    apellidos VARCHAR2(100),
    fecha_nac DATE,
    domicilio tipo_direccion,

    -- Este método NO podrá ser sobreescrito en ningún subtipo
    FINAL MEMBER FUNCTION obtener_dni RETURN VARCHAR2,

    -- Este método SÍ puede ser sobreescrito
    MEMBER FUNCTION obtener_nombre_completo RETURN VARCHAR2
) NOT FINAL;
/
```

**Caso de uso:** métodos que implementan lógica crítica de negocio (cálculo de impuestos, validaciones legales)
que no deben modificarse en subtipos por riesgo de inconsistencia.

### 10.2. NOT INSTANTIABLE (tipo abstracto)

Un tipo `NOT INSTANTIABLE` equivale a una **clase abstracta** en Java: no se puede instanciar directamente,
solo sirve como supertipo. Es útil para definir una interfaz común sin forzar una implementación concreta.

```sql
-- tipo_persona_abstracta: no se puede crear con NEW,
-- solo sirve como supertipo
CREATE OR REPLACE TYPE tipo_persona_abstracta AS OBJECT (
    dni       VARCHAR2(9),
    nombre    VARCHAR2(50),
    apellidos VARCHAR2(100),

    -- Método abstracto: los subtipos DEBEN implementarlo
    MEMBER FUNCTION obtener_identificador RETURN VARCHAR2
) NOT INSTANTIABLE NOT FINAL;
/
```

Cuando un subtipo hereda de un supertipo `NOT INSTANTIABLE`, debe indicar `INSTANTIABLE` explícitamente
si quiere poder crear instancias:

```sql
CREATE OR REPLACE TYPE tipo_profesor UNDER tipo_persona_abstracta (
    especialidad VARCHAR2(50),
    salario      NUMBER(8,2),

    OVERRIDING MEMBER FUNCTION obtener_identificador RETURN VARCHAR2
) INSTANTIABLE NOT FINAL;
/

CREATE OR REPLACE TYPE BODY tipo_profesor AS
    OVERRIDING MEMBER FUNCTION obtener_identificador RETURN VARCHAR2 IS
    BEGIN
        -- El profesor se identifica por su DNI y especialidad
        RETURN SELF.dni || ' — ' || SELF.especialidad;
    END obtener_identificador;
END;
/
```

```sql
DECLARE
    -- p1 tipo_persona_abstracta;  ← ERROR: ORA-22826, no es instanciable
    p2 tipo_profesor;
BEGIN
    p2 := NEW tipo_profesor('12345678A','Ana','López','Matemáticas',1850);
    DBMS_OUTPUT.PUT_LINE(p2.obtener_identificador());
    -- → '12345678A — Matemáticas'
END;
/
```

### 10.3. SELF AS RESULT

Cuando defines un constructor personalizado, Oracle **requiere** que la función devuelva `SELF AS RESULT`.
Esto le indica que el resultado es el propio objeto recién construido. Ya lo vimos en el Tema 18, pero aquí
aclaramos su rol semántico:

```sql
CONSTRUCTOR FUNCTION tipo_alumno (
    p_nia    NUMBER,
    p_nombre VARCHAR2,
    ...
) RETURN SELF AS RESULT IS
BEGIN
    SELF.nia    := p_nia;
    SELF.nombre := p_nombre;
    -- ...
    RETURN;   -- Oracle entiende que devuelve SELF
END tipo_alumno;
```

`RETURN SELF AS RESULT` es obligatorio en la firma. No puede usarse `RETURN tipo_alumno` (eso sería una
función normal, no un constructor).

---

## 11. Resumen Tema 19

```
UTILIZACIÓN DE OBJETOS
│
├── Declaración    → Como cualquier tipo: variables, parámetros, retornos de funciones
│
├── Instanciación  → NEW tipo_objeto(valor1, valor2, ...)
│                    ¡El orden de parámetros debe coincidir con la declaración!
│
├── Atributos      → objeto.atributo  /  objeto.obj_anidado.atributo
│   ├── Lectura, escritura y paso como argumento
│   └── Modificación directa: objeto.atributo := nuevo_valor
│
├── Métodos        → objeto.metodo()  /  objeto.metodo(param1, param2)
│   ├── MEMBER: sobre instancias   (objeto.metodo())
│   └── STATIC: sobre el tipo      (Tipo.metodo())
│
├── Herencia       → UNDER supertipo / NOT FINAL / FINAL
│                    Constructor incluye atributos del supertipo primero
│
├── Polimorfismo   → OVERRIDING MEMBER FUNCTION/PROCEDURE
│
├── MAP            → Convierte a escalar para ORDER BY / comparaciones
│                    MAP MEMBER FUNCTION nombre RETURN escalar
│
├── ORDER          → Compara dos instancias directamente
│                    ORDER MEMBER FUNCTION nombre (otro tipo) RETURN INTEGER
│   └── MAP o ORDER: solo uno por tipo; MAP preferido para grandes volúmenes
│
└── Palabras clave
    ├── FINAL (método)       → no sobreescribible en subtipos
    ├── NOT INSTANTIABLE     → tipo abstracto, no se puede instanciar
    ├── INSTANTIABLE         → subtipo concreto de un tipo abstracto
    └── SELF AS RESULT       → obligatorio en constructores personalizados
```

---

# Tema 20 — Organización de los Tipos de Datos Objeto

## 1. Introducción

En los temas anteriores definimos tipos objeto y los usamos como variables en bloques PL/SQL. Sin embargo, la
verdadera potencia de las BDOR reside en **almacenar objetos en tablas** (persistencia), de forma que estén
disponibles para todas las aplicaciones y sobrevivan más allá de la ejecución de un bloque de código.

En este tema veremos:
1. Tipos de datos **colección** (VARRAY y Nested Table) para atributos multi-valor.
2. **Tablas de objetos**: una tabla entera formada por objetos de un tipo.
3. **Tablas con columnas de tipo objeto**: tablas mixtas donde algunas columnas son tipos objeto.
4. Operaciones DML (SELECT, INSERT, UPDATE, DELETE) sobre estas tablas.
5. Las cláusulas especiales **VALUE** y **REF**.

---

## 2. Tipos de datos colección

Una **colección** es un tipo de dato que almacena varios elementos del mismo tipo bajo un mismo nombre, de
manera similar a un array o lista en programación.

### Los dos tipos de colección en Oracle

| Característica | VARRAY | Nested Table (Tabla Anidada) |
|---|---|---|
| ¿Tiene orden? | Sí (índice desde 1) | No garantizado |
| ¿Tiene límite? | Sí, máximo definido al crear | No |
| ¿Se puede usar en SQL? | Solo recuperación completa | Sí, se puede usar con `TABLE()` |
| ¿Cuándo usarlo? | Número conocido y pequeño de elementos | Número variable o grande |

### Sintaxis de declaración

```sql
-- VARRAY con capacidad máxima
CREATE OR REPLACE TYPE tipo_lista_telefonos
    AS VARRAY(5) OF VARCHAR2(15);
/

-- Nested Table sin límite
CREATE OR REPLACE TYPE tipo_lista_asignaturas
    AS TABLE OF VARCHAR2(50);
/
```

### 2.1. Declaración y uso en PL/SQL

```sql
DECLARE
    -- Declaración usando los tipos colección creados arriba
    v_telefonos   tipo_lista_telefonos;
    v_asignaturas tipo_lista_asignaturas;
BEGIN
    -- Inicialización con el constructor (como los tipos objeto)
    v_telefonos := tipo_lista_telefonos(
                       '600123456',
                       '963456789',
                       '912345678'
                   );

    v_asignaturas := tipo_lista_asignaturas(
                         'Matemáticas',
                         'Bases de Datos',
                         'Programación',
                         'Sistemas'
                     );

    -- Acceso por índice (empieza en 1, no en 0)
    DBMS_OUTPUT.PUT_LINE('Primer teléfono: ' || v_telefonos(1));
    -- → '600123456'

    -- Modificar un elemento
    v_telefonos(2) := '963999888';

    -- Añadir elemento a una Nested Table (no posible en VARRAY sin redimensionar)
    v_asignaturas.EXTEND;                         -- hace sitio
    v_asignaturas(v_asignaturas.LAST) := 'Redes'; -- asigna el nuevo elemento

    -- Recorrer con bucle
    FOR i IN 1..v_asignaturas.COUNT LOOP
        DBMS_OUTPUT.PUT_LINE(i || ': ' || v_asignaturas(i));
    END LOOP;
END;
/
```

### Colección como atributo de un tipo objeto

Podemos usar una colección como atributo de un tipo objeto. Así, un `tipo_alumno` puede tener una lista de
teléfonos de contacto:

```sql
-- Primero el tipo colección
CREATE OR REPLACE TYPE tipo_lista_telefonos AS VARRAY(5) OF VARCHAR2(15);
/

-- Luego el tipo objeto que lo usa
CREATE OR REPLACE TYPE tipo_alumno UNDER tipo_persona (
    nia         NUMBER(6),
    curso       VARCHAR2(10),
    nota_media  NUMBER(4,2),
    telefonos   tipo_lista_telefonos,     -- atributo de tipo colección

    OVERRIDING MEMBER FUNCTION obtener_nombre_completo RETURN VARCHAR2,
    MEMBER FUNCTION esta_aprobado RETURN BOOLEAN
) FINAL;
/
```

```sql
DECLARE
    v_alum tipo_alumno;
BEGIN
    v_alum := NEW tipo_alumno(
                  '98765432W', 'Lucía', 'Pérez Vidal',
                  DATE '2006-05-30',
                  NEW tipo_direccion('Avda. Mar 3','Alicante',03001),
                  100025,
                  '1DAM',
                  7.8,
                  tipo_lista_telefonos('600111222', '963444555')
              );

    DBMS_OUTPUT.PUT_LINE('Teléfono principal: ' || v_alum.telefonos(1));
    -- → '600111222'
END;
/
```

---

## 3. Tablas de objetos

### 3.1. Concepto

Una **tabla de objetos** es una tabla relacional donde **cada fila es una instancia** de un tipo objeto. Los atributos
del tipo se convierten en las "columnas virtuales" de la tabla. Oracle genera automáticamente una `OID` (Object
Identifier) para cada fila, que actúa como clave de referencia.

### 3.2. Creación de tablas de objetos

```sql
CREATE TABLE nombre_tabla OF tipo_objeto;
```

```sql
-- Tabla de objetos: cada fila es un tipo_profesor completo
CREATE TABLE profesores OF tipo_profesor;

-- Tabla de objetos con restricciones adicionales
-- (por ejemplo, declarar que el atributo dni actúa como PRIMARY KEY)
CREATE TABLE profesores OF tipo_profesor (
    dni PRIMARY KEY    -- indica que el atributo dni es la clave primaria
);
```

> **Importante:** una vez creada la tabla, **no se puede redefinir** el tipo objeto sobre el que se basa
> (no se puede hacer `CREATE OR REPLACE TYPE tipo_profesor` si hay una tabla `profesores`). Habría que
> eliminar la tabla primero, o usar `ALTER TYPE` con precaución.

```sql
-- Tabla de objetos para alumnos
CREATE TABLE alumnos OF tipo_alumno (
    nia PRIMARY KEY
);
```

---

## 4. Tablas con columnas tipo objeto

En lugar de que toda la tabla sea de un tipo objeto, podemos tener tablas mixtas donde **algunas columnas son de
tipo objeto** y otras son tipos simples:

```sql
-- Tabla que mezcla columnas simples y columnas de tipo objeto
CREATE TABLE asignaturas (
    cod_asignatura  NUMBER(4)    PRIMARY KEY,
    nombre          VARCHAR2(80) NOT NULL,
    horas_semana    NUMBER(2),
    tutor           tipo_profesor,    -- columna de tipo objeto
    delegado        tipo_alumno       -- otra columna de tipo objeto
);
```

Esto permite modelar relaciones donde no necesitamos una tabla aparte para el profesor ni para el alumno
delegado: el objeto completo vive dentro de la fila de `asignaturas`.

---

## 5. Consultas SELECT

Las consultas sobre tablas de objetos y tablas con columnas de tipo objeto utilizan SQL estándar, con algunas
particularidades.

### A. SELECT sobre tabla de objetos (todos los atributos)

```sql
-- Selecciona todas las columnas (= todos los atributos) de la tabla de objetos
SELECT *
FROM   profesores;

-- Con alias (necesario para acceder a atributos y métodos)
SELECT p.dni, p.nombre, p.apellidos, p.salario
FROM   profesores p
ORDER BY p.apellidos, p.nombre;
```

### B. SELECT sobre tabla de objetos (atributos de objeto anidado)

```sql
-- Accedemos al código postal del domicilio de cada profesor
-- El alias 'p' es OBLIGATORIO para acceder a objetos anidados
SELECT p.nombre,
       p.apellidos,
       p.domicilio.ciudad       AS ciudad,
       p.domicilio.codigo_postal AS cp
FROM   profesores p
WHERE  p.domicilio.ciudad = 'Valencia';
```

> **Sin alias:** `SELECT profesores.domicilio.ciudad FROM profesores` daría
> `ORA-00904` o resultados incorrectos. Siempre usa alias con columnas de tipo objeto.

### C. SELECT sobre tabla con columnas de tipo objeto

```sql
-- La tabla asignaturas tiene una columna 'tutor' de tipo tipo_profesor
-- Necesitamos alias para acceder a los atributos del objeto anidado
SELECT a.cod_asignatura,
       a.nombre                AS asignatura,
       a.tutor.nombre          AS nombre_tutor,    -- atributo del objeto columna
       a.tutor.apellidos       AS apellidos_tutor,
       a.tutor.especialidad    AS especialidad
FROM   asignaturas a
WHERE  a.tutor.especialidad = 'Matemáticas';
```

### D. Usar cláusulas WHERE, ORDER BY, JOIN

```sql
-- Profesores con más de 5 años de antigüedad y salario mayor de 1500€
SELECT p.nombre, p.apellidos, p.salario
FROM   profesores p
WHERE  MONTHS_BETWEEN(SYSDATE, p.fecha_alta) / 12 > 5
  AND  p.salario > 1500
ORDER BY p.salario DESC;

-- Alumnos aprobados de 1DAM
SELECT a.nia, a.nombre, a.apellidos, a.nota_media
FROM   alumnos a
WHERE  a.curso = '1DAM'
  AND  a.nota_media >= 5
ORDER BY a.nota_media DESC;

-- Funciones de agregado sobre atributos de tipo objeto
SELECT AVG(p.salario) AS salario_medio,
       MAX(p.salario) AS salario_maximo,
       MIN(p.salario) AS salario_minimo
FROM   profesores p;
```

---

## 6. Inserción de objetos

### A. Insertar en tabla de objetos (instancia previa)

```sql
DECLARE
    v_profe1 tipo_profesor;
    v_profe2 tipo_profesor;
BEGIN
    v_profe1 := NEW tipo_profesor(
                    '12345678A', 'Ana', 'López Ruiz',
                    DATE '1980-04-10',
                    NEW tipo_direccion('C/Rosales 22', 'Valencia', 46010),
                    DATE '2010-09-01', 'Matemáticas', 1850
                );

    v_profe2 := NEW tipo_profesor(
                    '87654321B', 'Roberto', 'Mora Díaz',
                    DATE '1975-11-20',
                    NEW tipo_direccion('Avda. Constitución 7', 'Valencia', 46001),
                    DATE '2005-09-01', 'Física', 2100
                );

    -- INSERT con VALUES y el objeto instanciado
    INSERT INTO profesores VALUES (v_profe1);
    INSERT INTO profesores VALUES (v_profe2);

    COMMIT;
END;
/
```

### B. Insertar en tabla de objetos (constructor inline)

```sql
-- Sin declarar variable previa, construimos el objeto directamente en VALUES
INSERT INTO profesores VALUES (
    tipo_profesor(
        '11223344C', 'Elena', 'Vázquez Torres',
        DATE '1988-03-05',
        tipo_direccion('C/Poeta Querol 3', 'Valencia', 46002),
        DATE '2020-09-01', 'Bases de Datos', 1700
    )
);
COMMIT;
```

### C. Insertar en tabla con columnas de tipo objeto

```sql
-- La tabla asignaturas tiene columnas normales y columnas de tipo objeto
INSERT INTO asignaturas (cod_asignatura, nombre, horas_semana, tutor, delegado)
VALUES (
    1001,
    'Bases de Datos',
    6,
    -- Objeto tipo_profesor inline:
    tipo_profesor(
        '11223344C', 'Elena', 'Vázquez Torres',
        DATE '1988-03-05',
        tipo_direccion('C/Poeta Querol 3', 'Valencia', 46002),
        DATE '2020-09-01', 'Bases de Datos', 1700
    ),
    -- Objeto tipo_alumno inline:
    tipo_alumno(
        '98765432W', 'Lucía', 'Pérez Vidal',
        DATE '2006-05-30',
        tipo_direccion('Avda. Mar 3', 'Alicante', 03001),
        100025, '2DAM', 8.5,
        tipo_lista_telefonos('600111222')
    )
);
COMMIT;
```

---

## 7. Modificación de objetos

La sentencia `UPDATE` funciona con tablas de objetos usando un **alias** para referenciar los atributos:

### A. Modificar un atributo simple en tabla de objetos

```sql
-- Actualizar el salario del profesor con DNI '12345678A'
UPDATE profesores p
SET    p.salario = p.salario * 1.05   -- subida del 5%
WHERE  p.dni = '12345678A';

COMMIT;
```

### B. Modificar un atributo de objeto anidado

```sql
-- Actualizar la ciudad del domicilio de todos los profesores de Almería
UPDATE profesores p
SET    p.domicilio.ciudad = 'Almería Centro'
WHERE  p.domicilio.ciudad = 'Almería';

COMMIT;
```

### C. Reemplazar el objeto anidado completo

```sql
-- Cambiar la dirección completa del profesor '87654321B'
UPDATE profesores p
SET    p.domicilio = tipo_direccion('Gran Vía 100', 'Madrid', 28013)
WHERE  p.dni = '87654321B';

COMMIT;
```

### D. Modificar en tabla con columnas de tipo objeto

```sql
-- En asignaturas, actualizar el nombre del tutor de la asignatura 1001
UPDATE asignaturas a
SET    a.tutor.nombre = 'Elena María'
WHERE  a.cod_asignatura = 1001;

COMMIT;

-- Reemplazar el objeto tutor completo
UPDATE asignaturas a
SET    a.tutor = tipo_profesor(
                     '11223344C', 'Elena María', 'Vázquez Torres',
                     DATE '1988-03-05',
                     tipo_direccion('C/Nueva 5', 'Valencia', 46003),
                     DATE '2020-09-01', 'Bases de Datos', 1750
                 )
WHERE  a.cod_asignatura = 1001;

COMMIT;
```

---

## 8. Eliminación de objetos

### A. DELETE en tabla de objetos

```sql
-- Eliminar un profesor específico
DELETE FROM profesores p
WHERE  p.dni = '12345678A';

COMMIT;

-- Eliminar todos los profesores con salario menor de 1000€
DELETE FROM profesores p
WHERE  p.salario < 1000;

COMMIT;

-- Eliminar todos los registros (sin WHERE)
-- DELETE FROM profesores;  ← ¡cuidado! borra todo
```

### B. DELETE en tabla con columnas de tipo objeto

```sql
-- Eliminar la asignatura cuyo tutor tiene el DNI '11223344C'
DELETE FROM asignaturas a
WHERE  a.tutor.dni = '11223344C';

COMMIT;
```

> **Nota:** `DELETE` en tablas de objetos no elimina el *tipo* objeto, solo las filas. El tipo permanece en el
> esquema hasta que se ejecute `DROP TYPE`.

---

## 9. Cláusula VALUE

La cláusula `VALUE(alias)` devuelve el **objeto completo** de una fila en una tabla de objetos, en lugar de
devolver sus atributos por separado. Es útil cuando queremos manipular o copiar el objeto entero.

### A. SELECT con VALUE: recuperar el objeto completo

```sql
-- Recupera cada fila como un objeto tipo_profesor completo
SELECT VALUE(p)
FROM   profesores p
WHERE  p.salario > 1500;
```

> En Oracle SQL Developer, el resultado aparece como un objeto serializado `TIPO_PROFESOR(...)`.
> En PL/SQL es donde realmente se aprovecha:

```sql
DECLARE
    v_prof tipo_profesor;
BEGIN
    -- Almacenar el resultado de VALUE en una variable de tipo objeto
    SELECT VALUE(p) INTO v_prof
    FROM   profesores p
    WHERE  p.dni = '12345678A';

    -- Ahora v_prof tiene todos los datos del profesor
    DBMS_OUTPUT.PUT_LINE('Salario: ' || v_prof.salario);
    DBMS_OUTPUT.PUT_LINE('Ciudad:  ' || v_prof.domicilio.ciudad);

    -- Podemos llamar a sus métodos
    DBMS_OUTPUT.PUT_LINE('Antigüedad: ' || v_prof.obtener_antiguedad());
END;
/
```

### B. Copiar objetos entre tablas con VALUE

```sql
-- Copiar todos los profesores de Matemáticas a una tabla de archivo
-- (imaginemos que existe 'profesores_historico' del mismo tipo)
INSERT INTO profesores_historico
SELECT VALUE(p)
FROM   profesores p
WHERE  p.especialidad = 'Matemáticas';

COMMIT;
```

### C. Comparar objetos con VALUE

```sql
-- Los JOIN entre tablas de objetos del mismo tipo se hacen con VALUE
SELECT p1.nombre || ' ' || p1.apellidos AS profe_activo,
       ph.nombre || ' ' || ph.apellidos AS profe_historico
FROM   profesores p1
JOIN   profesores_historico ph
  ON   VALUE(p1) = VALUE(ph);   -- compara los objetos completos (usa MAP si existe)
```

### D. VALUE con INTO (recuperar y modificar)

```sql
DECLARE
    v_prof1  tipo_profesor;
    v_prof2  tipo_profesor;
BEGIN
    SELECT VALUE(p) INTO v_prof1
    FROM   profesores p
    WHERE  p.dni = '87654321B';

    -- Hacemos una copia del objeto y la modificamos
    v_prof2            := v_prof1;
    v_prof2.salario    := v_prof2.salario * 1.10;
    v_prof2.especialidad := v_prof1.especialidad || ' (Senior)';

    DBMS_OUTPUT.PUT_LINE('Salario original: ' || v_prof1.salario);
    DBMS_OUTPUT.PUT_LINE('Salario propuesto: ' || v_prof2.salario);
END;
/
```

---

## 10. Cláusula REF

La cláusula `REF` devuelve un **puntero lógico** (referencia) a un objeto almacenado en una tabla de objetos.
Esta referencia es un `OID` (Object Identifier) que Oracle usa para localizar el objeto sin duplicar sus datos.

**¿Cuándo usar REF en lugar de incluir el objeto completo?**

| Situación | ¿Objeto embebido o REF? |
|---|---|
| Los datos del sub-objeto raramente cambian | Objeto embebido (como hemos visto) |
| El sub-objeto existe independientemente y lo referencia mucha gente | REF (para evitar duplicados) |
| Necesitamos que los cambios en el objeto original se reflejen automáticamente | REF |

### A. Crear una tabla con columna de tipo REF

```sql
-- Tabla de cursos donde la columna 'coordinador' apunta a un profesor
-- de la tabla 'profesores' (no duplica los datos del profesor)
CREATE TABLE cursos (
    id_curso    NUMBER(4)    PRIMARY KEY,
    nombre      VARCHAR2(80) NOT NULL,
    horas_total NUMBER(4),
    coordinador REF tipo_profesor   -- referencia, no copia del objeto
);
```

### B. Insertar con REF

```sql
-- Insertar un curso vinculándolo al profesor con DNI '11223344C'
-- que ya existe en la tabla 'profesores'
INSERT INTO cursos (id_curso, nombre, horas_total, coordinador)
SELECT 2001, 'CFGS DAM', 2000, REF(p)
FROM   profesores p
WHERE  p.dni = '11223344C';

COMMIT;
```

`REF(p)` devuelve la referencia (OID) del objeto `p`, no sus datos.

### C. Acceder a los datos a través de una referencia (DEREF)

Para leer los atributos de un objeto apuntado por una REF se usa `DEREF()`:

```sql
-- Ver los datos del coordinador de cada curso (siguiendo la referencia)
SELECT c.nombre                        AS curso,
       DEREF(c.coordinador).nombre     AS nombre_coord,
       DEREF(c.coordinador).apellidos  AS apellidos_coord,
       DEREF(c.coordinador).salario    AS salario_coord
FROM   cursos c;
```

### D. Comparar VALUE y REF

```sql
-- VALUE: devuelve el objeto completo (una copia de los datos)
SELECT VALUE(p) FROM profesores p WHERE p.dni = '11223344C';

-- REF:   devuelve una referencia al objeto (como un puntero)
SELECT REF(p)   FROM profesores p WHERE p.dni = '11223344C';
```

| | VALUE | REF |
|---|---|---|
| Devuelve | Copia del objeto | Puntero al objeto |
| Ocupa en memoria | Todos los atributos | Solo el OID |
| Cambios en el original | No se reflejan | Sí se reflejan (vía DEREF) |
| Uso típico | Manipular/copiar objetos en PL/SQL | Relacionar tablas sin duplicar datos |

---

## 11. Resumen Tema 20

```
ORGANIZACIÓN DE TIPOS DE DATOS OBJETO
│
├── Colecciones
│   ├── VARRAY(n) OF tipo    → conjunto ordenado con límite máximo
│   └── TABLE OF tipo        → conjunto sin límite ni orden garantizado
│       └── Acceso por índice: coleccion(i) := valor  /  x := coleccion(i)
│
├── Tablas de objetos
│   └── CREATE TABLE tabla OF tipo_objeto [(atributo PRIMARY KEY)]
│       • Cada fila = una instancia del tipo objeto
│       • Los atributos actúan como columnas
│
├── Tablas con columnas tipo objeto
│   └── CREATE TABLE tabla (col_normal tipo, col_objeto tipo_objeto, ...)
│       • Mezcla de columnas simples y columnas de tipo objeto
│
├── Operaciones DML
│   ├── SELECT   p.atributo, p.obj_anidado.atributo FROM tabla p
│   │            ¡Alias OBLIGATORIO para objetos anidados!
│   ├── INSERT   INTO tabla VALUES (tipo_objeto(...)) o VALUES (variable)
│   ├── UPDATE   tabla alias SET alias.atributo = valor WHERE alias.atrib = cond
│   └── DELETE   FROM tabla alias WHERE alias.atributo = valor
│
└── Cláusulas especiales
    ├── VALUE(alias)  → devuelve el objeto completo de una fila
    │   └── Útil para: SELECT INTO variable, INSERT...SELECT, comparaciones
    └── REF(alias)    → devuelve una referencia (OID) al objeto
        └── DEREF(ref) → sigue la referencia y devuelve el objeto
```

---

# Apéndice: Script completo del escenario

El siguiente script crea desde cero todos los tipos y tablas del centro educativo utilizados a lo largo de los tres
temas. Puedes ejecutarlo en Oracle 21c (SQL Developer, SQL*Plus o Live SQL).

```sql
-- =============================================================
--  SCRIPT COMPLETO: Centro Educativo - Oracle 21c
--  Orden de creación respetando dependencias entre tipos
-- =============================================================

-- ── 0. Limpiar (por si ejecutamos varias veces) ─────────────
BEGIN
    EXECUTE IMMEDIATE 'DROP TABLE cursos';
EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN
    EXECUTE IMMEDIATE 'DROP TABLE asignaturas';
EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN
    EXECUTE IMMEDIATE 'DROP TABLE alumnos';
EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN
    EXECUTE IMMEDIATE 'DROP TABLE profesores';
EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN
    EXECUTE IMMEDIATE 'DROP TYPE tipo_alumno    FORCE';
EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN
    EXECUTE IMMEDIATE 'DROP TYPE tipo_profesor  FORCE';
EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN
    EXECUTE IMMEDIATE 'DROP TYPE tipo_persona   FORCE';
EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN
    EXECUTE IMMEDIATE 'DROP TYPE tipo_lista_telefonos FORCE';
EXCEPTION WHEN OTHERS THEN NULL; END;
/
BEGIN
    EXECUTE IMMEDIATE 'DROP TYPE tipo_direccion FORCE';
EXCEPTION WHEN OTHERS THEN NULL; END;
/

-- ── 1. Tipo dirección (sin dependencias) ────────────────────
CREATE OR REPLACE TYPE tipo_direccion AS OBJECT (
    calle          VARCHAR2(100),
    ciudad         VARCHAR2(50),
    codigo_postal  NUMBER(5)
);
/

-- ── 2. Colección de teléfonos ────────────────────────────────
CREATE OR REPLACE TYPE tipo_lista_telefonos AS VARRAY(5) OF VARCHAR2(15);
/

-- ── 3. Supertipo persona (NOT FINAL para permitir herencia) ──
CREATE OR REPLACE TYPE tipo_persona AS OBJECT (
    dni          VARCHAR2(9),
    nombre       VARCHAR2(50),
    apellidos    VARCHAR2(100),
    fecha_nac    DATE,
    domicilio    tipo_direccion,

    MEMBER FUNCTION obtener_nombre_completo RETURN VARCHAR2
) NOT FINAL;
/

CREATE OR REPLACE TYPE BODY tipo_persona AS
    MEMBER FUNCTION obtener_nombre_completo RETURN VARCHAR2 IS
    BEGIN
        RETURN SELF.nombre || ' ' || SELF.apellidos;
    END obtener_nombre_completo;
END;
/

-- ── 4. Subtipo profesor ──────────────────────────────────────
CREATE OR REPLACE TYPE tipo_profesor UNDER tipo_persona (
    fecha_alta    DATE,
    especialidad  VARCHAR2(50),
    salario       NUMBER(8,2),

    MAP MEMBER FUNCTION  criterio_orden      RETURN VARCHAR2,
    MEMBER FUNCTION      obtener_antiguedad  RETURN NUMBER,
    MEMBER PROCEDURE     aplicar_subida      (p_porcentaje NUMBER),
    MEMBER FUNCTION      calcular_bonificacion              RETURN NUMBER,
    MEMBER FUNCTION      calcular_bonificacion (p_pct NUMBER) RETURN NUMBER,
    MEMBER FUNCTION      calcular_bonificacion (p_pct NUMBER, p_max NUMBER)
                                             RETURN NUMBER,
    STATIC FUNCTION      dni_valido          (p_dni VARCHAR2) RETURN BOOLEAN
) NOT FINAL;
/

CREATE OR REPLACE TYPE BODY tipo_profesor AS

    MAP MEMBER FUNCTION criterio_orden RETURN VARCHAR2 IS
    BEGIN
        RETURN RPAD(UPPER(SELF.apellidos), 100) || UPPER(SELF.nombre);
    END criterio_orden;

    MEMBER FUNCTION obtener_antiguedad RETURN NUMBER IS
    BEGIN
        RETURN TRUNC(MONTHS_BETWEEN(SYSDATE, SELF.fecha_alta) / 12);
    END obtener_antiguedad;

    MEMBER PROCEDURE aplicar_subida (p_porcentaje NUMBER) IS
    BEGIN
        IF p_porcentaje < 0 OR p_porcentaje > 100 THEN
            RAISE_APPLICATION_ERROR(-20001, 'Porcentaje fuera de rango (0-100)');
        END IF;
        SELF.salario := SELF.salario * (1 + p_porcentaje / 100);
    END aplicar_subida;

    MEMBER FUNCTION calcular_bonificacion RETURN NUMBER IS
    BEGIN
        RETURN ROUND(SELF.salario * 0.10, 2);
    END calcular_bonificacion;

    MEMBER FUNCTION calcular_bonificacion (p_pct NUMBER) RETURN NUMBER IS
    BEGIN
        RETURN ROUND(SELF.salario * p_pct / 100, 2);
    END calcular_bonificacion;

    MEMBER FUNCTION calcular_bonificacion (p_pct NUMBER, p_max NUMBER)
        RETURN NUMBER IS
        v_b NUMBER;
    BEGIN
        v_b := ROUND(SELF.salario * p_pct / 100, 2);
        RETURN LEAST(v_b, p_max);
    END calcular_bonificacion;

    STATIC FUNCTION dni_valido (p_dni VARCHAR2) RETURN BOOLEAN IS
        v_letras CONSTANT VARCHAR2(23) := 'TRWAGMYFPDXBNJZSQVHLCKE';
        v_num   NUMBER;
        v_letra CHAR(1);
    BEGIN
        IF LENGTH(p_dni) <> 9 THEN RETURN FALSE; END IF;
        IF NOT REGEXP_LIKE(SUBSTR(p_dni,1,8), '^\d{8}$') THEN RETURN FALSE; END IF;
        v_num   := TO_NUMBER(SUBSTR(p_dni,1,8));
        v_letra := SUBSTR(v_letras, MOD(v_num, 23) + 1, 1);
        RETURN UPPER(SUBSTR(p_dni,9,1)) = v_letra;
    END dni_valido;

END;
/

-- ── 5. Subtipo alumno ────────────────────────────────────────
CREATE OR REPLACE TYPE tipo_alumno UNDER tipo_persona (
    nia         NUMBER(6),
    curso       VARCHAR2(10),
    nota_media  NUMBER(4,2),
    telefonos   tipo_lista_telefonos,

    OVERRIDING MEMBER FUNCTION obtener_nombre_completo RETURN VARCHAR2,
    MEMBER FUNCTION esta_aprobado RETURN BOOLEAN,
    ORDER MEMBER FUNCTION comparar (otro tipo_alumno) RETURN INTEGER,

    CONSTRUCTOR FUNCTION tipo_alumno (
        p_nia    NUMBER,  p_nombre VARCHAR2, p_ape VARCHAR2,
        p_fnac   DATE,    p_dom    tipo_direccion,
        p_nia2   NUMBER,  p_curso  VARCHAR2
    ) RETURN SELF AS RESULT
) FINAL;
/

CREATE OR REPLACE TYPE BODY tipo_alumno AS

    OVERRIDING MEMBER FUNCTION obtener_nombre_completo RETURN VARCHAR2 IS
    BEGIN
        RETURN SELF.nombre || ' ' || SELF.apellidos || ' (' || SELF.curso || ')';
    END obtener_nombre_completo;

    MEMBER FUNCTION esta_aprobado RETURN BOOLEAN IS
    BEGIN
        RETURN SELF.nota_media >= 5;
    END esta_aprobado;

    ORDER MEMBER FUNCTION comparar (otro tipo_alumno) RETURN INTEGER IS
    BEGIN
        IF    SELF.nota_media > otro.nota_media THEN RETURN -1;
        ELSIF SELF.nota_media < otro.nota_media THEN RETURN  1;
        ELSE RETURN 0;
        END IF;
    END comparar;

    CONSTRUCTOR FUNCTION tipo_alumno (
        p_nia    NUMBER,  p_nombre VARCHAR2, p_ape VARCHAR2,
        p_fnac   DATE,    p_dom    tipo_direccion,
        p_nia2   NUMBER,  p_curso  VARCHAR2
    ) RETURN SELF AS RESULT IS
    BEGIN
        IF p_nia2 <= 0 THEN
            RAISE_APPLICATION_ERROR(-20010, 'NIA inválido');
        END IF;
        SELF.dni        := p_nia;   -- reutilizamos p_nia como DNI temporal
        SELF.nombre     := p_nombre;
        SELF.apellidos  := p_ape;
        SELF.fecha_nac  := p_fnac;
        SELF.domicilio  := p_dom;
        SELF.nia        := p_nia2;
        SELF.curso      := p_curso;
        SELF.nota_media := 0;
        SELF.telefonos  := NULL;
        RETURN;
    END tipo_alumno;

END;
/

-- ── 6. Tablas ────────────────────────────────────────────────
CREATE TABLE profesores OF tipo_profesor (
    dni PRIMARY KEY
);

CREATE TABLE alumnos OF tipo_alumno (
    nia PRIMARY KEY
);

CREATE TABLE asignaturas (
    cod_asignatura  NUMBER(4)    PRIMARY KEY,
    nombre          VARCHAR2(80) NOT NULL,
    horas_semana    NUMBER(2),
    tutor           tipo_profesor,
    delegado        tipo_alumno
);

CREATE TABLE cursos (
    id_curso    NUMBER(4)    PRIMARY KEY,
    nombre      VARCHAR2(80) NOT NULL,
    horas_total NUMBER(4),
    coordinador REF tipo_profesor
);

-- ── 7. Datos de ejemplo ──────────────────────────────────────
INSERT INTO profesores VALUES (
    tipo_profesor(
        '12345678A', 'Ana', 'López Ruiz', DATE '1980-04-10',
        tipo_direccion('C/Rosales 22','Valencia',46010),
        DATE '2010-09-01', 'Matemáticas', 1850
    )
);
INSERT INTO profesores VALUES (
    tipo_profesor(
        '87654321B', 'Roberto', 'Mora Díaz', DATE '1975-11-20',
        tipo_direccion('Avda. Constitución 7','Valencia',46001),
        DATE '2005-09-01', 'Física', 2100
    )
);
INSERT INTO profesores VALUES (
    tipo_profesor(
        '11223344C', 'Elena', 'Vázquez Torres', DATE '1988-03-05',
        tipo_direccion('C/Poeta Querol 3','Valencia',46002),
        DATE '2020-09-01', 'Bases de Datos', 1700
    )
);

INSERT INTO alumnos VALUES (
    tipo_alumno(
        '98765432W', 'Lucía', 'Pérez Vidal', DATE '2006-05-30',
        tipo_direccion('Avda. Mar 3','Alicante',03001),
        100025, '2DAM', 8.5,
        tipo_lista_telefonos('600111222','963444555')
    )
);
INSERT INTO alumnos VALUES (
    tipo_alumno(
        '11111111H', 'Marcos', 'Sánchez Gil', DATE '2006-07-20',
        tipo_direccion('C/Mayor 10','Alicante',03002),
        100026, '2DAM', 4.8,
        tipo_lista_telefonos('655987654')
    )
);

COMMIT;

-- ── 8. Pruebas rápidas ───────────────────────────────────────

-- Todos los profesores ordenados por apellido (usa MAP internamente)
SELECT p.nombre, p.apellidos, p.especialidad, p.salario
FROM   profesores p
ORDER BY VALUE(p);

-- Alumnos aprobados de 2DAM
SELECT a.nia, a.nombre, a.apellidos, a.nota_media
FROM   alumnos a
WHERE  a.curso = '2DAM'
  AND  a.nota_media >= 5;

-- Antigüedad de cada profesor (llamada a método en SELECT)
SELECT p.nombre,
       p.apellidos,
       p.obtener_antiguedad() AS anios_antiguedad
FROM   profesores p;

-- Bonificación estándar (10%) de cada profesor
SELECT p.nombre,
       p.salario,
       p.calcular_bonificacion() AS bonificacion
FROM   profesores p;
```

---