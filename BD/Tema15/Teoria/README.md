# Unidad 15: El Lenguaje PL/SQL

> **Módulo:** Bases de Datos · CFGS Desarrollo de Aplicaciones Multiplataforma  
> **Sistema gestor:** Oracle Database 21c  
> **Nivel:** Ciclo Formativo de Grado Superior

---

## Índice

1. [Introducción y contextualización práctica](#1-introducción-y-contextualización-práctica)
2. [PL/SQL: Definición y reglas básicas](#2-plsql-definición-y-reglas-básicas)
3. [Sintaxis del lenguaje PL/SQL: Unidades léxicas](#3-sintaxis-del-lenguaje-plsql-unidades-léxicas)
   - 3.1 [Delimitadores](#31-delimitadores)
   - 3.2 [Identificadores](#32-identificadores)
   - 3.3 [Literales](#33-literales)
   - 3.4 [Comentarios](#34-comentarios)
4. [Tipos de datos simples en PL/SQL](#4-tipos-de-datos-simples-en-plsql)
5. [Variables, constantes y operadores](#5-variables-constantes-y-operadores)
   - 5.1 [Variables y constantes](#51-variables-y-constantes)
   - 5.2 [Operadores](#52-operadores)
6. [Estructuras de control de flujo: Condicionales](#6-estructuras-de-control-de-flujo-condicionales)
   - 6.1 [Sentencia IF](#61-sentencia-if)
   - 6.2 [Sentencia CASE](#62-sentencia-case)
7. [Estructuras de control de flujo: Bucles](#7-estructuras-de-control-de-flujo-bucles)
   - 7.1 [Bucle LOOP básico](#71-bucle-loop-básico)
   - 7.2 [Bucle WHILE](#72-bucle-while)
   - 7.3 [Bucle FOR](#73-bucle-for)
   - 7.4 [Bucles anidados y etiquetas](#74-bucles-anidados-y-etiquetas)
8. [Manejo de errores: Excepciones](#8-manejo-de-errores-excepciones)
   - 8.1 [Excepciones predefinidas por Oracle](#81-excepciones-predefinidas-por-oracle)
   - 8.2 [Excepciones de Oracle sin nombre](#82-excepciones-de-oracle-sin-nombre)
   - 8.3 [Excepciones definidas por el usuario](#83-excepciones-definidas-por-el-usuario)
9. [Librerías básicas y de funciones en PL/SQL](#9-librerías-básicas-y-de-funciones-en-plsql)
   - 9.1 [Librerías básicas (paquetes del sistema)](#91-librerías-básicas-paquetes-del-sistema)
   - 9.2 [Librerías de funciones](#92-librerías-de-funciones)
10. [Entornos de desarrollo, depuración y control de código](#10-entornos-de-desarrollo-depuración-y-control-de-código)
11. [Tipos de scripts y scripts simples](#11-tipos-de-scripts-y-scripts-simples)
    - 11.1 [Tipos de scripts en PL/SQL](#111-tipos-de-scripts-en-plsql)
    - 11.2 [Scripts simples o anónimos](#112-scripts-simples-o-anónimos)
12. [Casos prácticos resueltos](#12-casos-prácticos-resueltos)
13. [Resumen del tema](#13-resumen-del-tema)
14. [Bibliografía y recursos](#14-bibliografía-y-recursos)
15. [ANEXO: Errores del PDF](#15-anexo-errores-del-pdf)

---

## Base de datos de ejemplo: Sistema Hospitalario

A lo largo de toda esta unidad utilizaremos de manera consistente el siguiente esquema de base de datos, que modela la gestión básica de un hospital. Todos los ejemplos de código harán referencia a estas tablas.

```sql
-- Tabla de pacientes del hospital
CREATE TABLE PACIENTES (
    id_paciente    NUMBER(6)      PRIMARY KEY,
    nombre         VARCHAR2(50)   NOT NULL,
    apellidos      VARCHAR2(100)  NOT NULL,
    fecha_nac      DATE           NOT NULL,
    sexo           CHAR(1)        CHECK (sexo IN ('M','F')),
    telefono       VARCHAR2(15),
    email          VARCHAR2(100),
    num_historial  VARCHAR2(20)   UNIQUE
);

-- Tabla de médicos
CREATE TABLE MEDICOS (
    id_medico      NUMBER(6)      PRIMARY KEY,
    nombre         VARCHAR2(50)   NOT NULL,
    apellidos      VARCHAR2(100)  NOT NULL,
    especialidad   VARCHAR2(60)   NOT NULL,
    salario        NUMBER(8,2)    NOT NULL,
    fecha_alta     DATE           DEFAULT SYSDATE
);

-- Tabla de citas médicas
CREATE TABLE CITAS (
    id_cita        NUMBER(8)      PRIMARY KEY,
    id_paciente    NUMBER(6)      REFERENCES PACIENTES(id_paciente),
    id_medico      NUMBER(6)      REFERENCES MEDICOS(id_medico),
    fecha_cita     DATE           NOT NULL,
    motivo         VARCHAR2(200),
    estado         VARCHAR2(20)   DEFAULT 'PENDIENTE'
                                  CHECK (estado IN ('PENDIENTE','REALIZADA','CANCELADA'))
);

-- Tabla de medicamentos prescritos
CREATE TABLE MEDICAMENTOS (
    id_medicamento NUMBER(6)      PRIMARY KEY,
    nombre         VARCHAR2(100)  NOT NULL,
    principio_act  VARCHAR2(100),
    stock          NUMBER(6)      DEFAULT 0,
    precio_unidad  NUMBER(6,2)    NOT NULL
);

-- Tabla de prescripciones (relación cita-medicamento)
CREATE TABLE PRESCRIPCIONES (
    id_prescripcion NUMBER(8)     PRIMARY KEY,
    id_cita         NUMBER(8)     REFERENCES CITAS(id_cita),
    id_medicamento  NUMBER(6)     REFERENCES MEDICAMENTOS(id_medicamento),
    dosis           VARCHAR2(50),
    duracion_dias   NUMBER(3)
);
```

> 💡 **Nota:** Puedes ejecutar este script en Oracle SQL Developer o en SQL*Plus para tener las tablas disponibles mientras practicas los ejemplos de la unidad.

---

## 1. Introducción y contextualización práctica

SQL es un lenguaje declarativo: le decimos *qué* queremos obtener, pero no *cómo* obtenerlo. Esto es muy potente para consultar y manipular datos, pero tiene limitaciones claras cuando necesitamos implementar lógica de negocio compleja.

Imagina que el hospital quiere automatizar estas tareas:

- Enviar una alerta cuando el stock de un medicamento baje de 10 unidades.
- Calcular automáticamente la edad de un paciente y clasificarlo en grupos de riesgo.
- Generar un informe mensual de citas por especialidad.
- Validar que no se asigne una cita a un médico que ya tiene otra en ese mismo tramo horario.

Con SQL puro estas tareas son imposibles o extremadamente difíciles. Para resolverlas necesitamos un lenguaje con estructuras de control, variables, manejo de errores y la capacidad de interactuar directamente con la base de datos: ese lenguaje es **PL/SQL**.

### ¿Por qué PL/SQL y no otro lenguaje?

| Característica | SQL puro | PL/SQL | Java/Python externo |
|---|---|---|---|
| Consulta de datos | ✅ Excelente | ✅ Excelente | ✅ Bueno |
| Lógica condicional | ❌ No | ✅ Sí | ✅ Sí |
| Bucles e iteraciones | ❌ No | ✅ Sí | ✅ Sí |
| Manejo de errores | ❌ Limitado | ✅ Completo | ✅ Completo |
| Rendimiento con BD | ✅ Máximo | ✅ Máximo | ⚠️ Latencia de red |
| Integración nativa | ✅ Nativa | ✅ Nativa | ❌ Requiere driver |

PL/SQL se ejecuta **dentro del motor de Oracle**, lo que significa que no hay latencia de red entre la lógica del programa y los datos. Para operaciones que afectan a miles de filas, esto supone una ventaja de rendimiento enorme frente a lógica implementada en una capa externa.

---

## 2. PL/SQL: Definición y reglas básicas

**PL/SQL** (Procedural Language/Structured Query Language) es el lenguaje de programación procedimental propietario de Oracle. Extiende SQL añadiéndole todas las características de un lenguaje de programación estructurado.

### La estructura de bloque

Todo código PL/SQL se organiza en **bloques**. Un bloque tiene tres secciones, de las cuales solo `BEGIN...END` es obligatoria:

```
[DECLARE]
    -- Sección de declaración (opcional)
    -- Aquí se declaran variables, constantes, cursores y excepciones
BEGIN
    -- Sección ejecutable (obligatoria)
    -- Aquí va la lógica del programa
[EXCEPTION]
    -- Sección de manejo de errores (opcional)
    -- Aquí se capturan y gestionan las excepciones
END;
/
```

> ⚠️ **Importante:** La barra `/` al final no forma parte del bloque PL/SQL. Es un comando de SQL*Plus/SQL Developer que le indica al cliente que ejecute el bloque que ha recibido. Sin ella, el bloque no se ejecutará.

### Ejemplo mínimo

```sql
BEGIN
    DBMS_OUTPUT.PUT_LINE('Hola desde el hospital!');
END;
/
```

> 💡 `DBMS_OUTPUT.PUT_LINE` es el equivalente al `print()` de Python o `System.out.println()` de Java. Para ver su salida en SQL Developer, activa primero la vista *DBMS Output* desde el menú *Ver*.

### Reglas básicas de escritura

| Regla | Descripción | Ejemplo correcto |
|---|---|---|
| **No distingue mayúsculas/minúsculas** | Las palabras clave, nombres de tablas y variables son insensibles al caso | `begin` = `BEGIN` = `Begin` |
| **Punto y coma obligatorio** | Cada instrucción debe terminar con `;` | `v_edad := 30;` |
| **DECLARE antes de BEGIN** | Las variables se declaran antes de la sección ejecutable | Ver sección 5 |
| **Bloques anidados** | Un bloque puede contener otros bloques en su interior | Ver sección 8 |
| **Una variable por línea** | Cada declaración ocupa su propia línea | `v_nombre VARCHAR2(50);` |

### Bloques anidados

Los bloques PL/SQL pueden anidarse unos dentro de otros. Las variables declaradas en un bloque interno no son accesibles desde el bloque externo (ámbito local), pero sí al contrario:

```sql
DECLARE
    v_hospital VARCHAR2(50) := 'Hospital Central';  -- Variable del bloque externo
BEGIN
    DBMS_OUTPUT.PUT_LINE('Hospital: ' || v_hospital);

    -- Bloque interno (anidado)
    DECLARE
        v_planta NUMBER := 3;  -- Variable del bloque interno
    BEGIN
        -- Puede acceder a v_hospital (bloque externo) y a v_planta (bloque interno)
        DBMS_OUTPUT.PUT_LINE('Planta ' || v_planta || ' de ' || v_hospital);
    END;

    -- Aquí v_planta ya NO existe, daría error si intentamos usarla
END;
/
```

---

## 3. Sintaxis del lenguaje PL/SQL: Unidades léxicas

Las **unidades léxicas** son los elementos básicos con los que se construye cualquier programa PL/SQL, igual que las letras y signos de puntuación lo son para un idioma escrito. El compilador de PL/SQL analiza el código descomponiéndolo en estas unidades antes de interpretarlo.

### 3.1 Delimitadores

Los delimitadores son símbolos con un significado especial para el compilador. Muchos provienen directamente de SQL.

| Símbolo | Nombre | Uso | Ejemplo |
|---|---|---|---|
| `;` | Terminador de instrucción | Finaliza cada sentencia | `v_edad := 30;` |
| `:=` | Asignación | Asigna un valor a una variable | `v_nombre := 'Ana';` |
| `=` | Comparación de igualdad | Solo en condiciones | `IF v_edad = 18 THEN` |
| `<>` o `!=` | Distinto de | Comparación de desigualdad | `IF v_estado <> 'ACTIVO'` |
| `<`, `>`, `<=`, `>=` | Comparadores relacionales | En condiciones | `IF v_stock < 10 THEN` |
| `\|\|` | Concatenación | Une cadenas de texto | `'Paciente: ' \|\| v_nombre` |
| `**` | Potencia | Operador de exponenciación | `v_area := 3.14 * radio**2;` |
| `--` | Comentario de línea | Comenta hasta el final de la línea | `-- Esto es un comentario` |
| `/* */` | Comentario de bloque | Comenta múltiples líneas | `/* Comentario largo */` |
| `( )` | Paréntesis | Agrupan expresiones o parámetros | `(v_a + v_b) * v_c` |
| `'...'` | Delimitador de cadena | Encierra literales de texto | `'Madrid'` |
| `%` | Atributo de tipo | Referencia a tipo de columna/fila | `v_nom PACIENTES.nombre%TYPE` |
| `@` | Enlace a base de datos | Acceso a BD remota | `SELECT * FROM tabla@enlace` |
| `.` | Selector de componente | Accede a atributo de objeto o paquete | `DBMS_OUTPUT.PUT_LINE` |

### 3.2 Identificadores

Los identificadores son los nombres que damos a los elementos del programa: variables, constantes, procedimientos, funciones, cursores, etc.

**Reglas de nomenclatura:**
- Deben comenzar por una **letra** (A-Z).
- Pueden contener letras, dígitos (0-9) y los símbolos `$`, `#`, `_`.
- Longitud máxima de **128 caracteres** (Oracle 12c en adelante; en versiones anteriores era 30).
- No pueden coincidir con una **palabra reservada** de PL/SQL.

**Ejemplos de identificadores válidos e inválidos:**

```sql
-- VÁLIDOS
v_nombre
p_id_paciente
c_MAX_REINTENTOS
fecha_nacimiento$
_contador#

-- INVÁLIDOS
2nombre       -- Empieza por número
mi-variable   -- Contiene guión (-)
BEGIN         -- Es una palabra reservada
nombre cita   -- Contiene un espacio
```

**Convención de nomenclatura recomendada** (no obligatoria, pero muy extendida en Oracle):

| Prefijo | Tipo de elemento | Ejemplo |
|---|---|---|
| `v_` | Variable local | `v_nombre_paciente` |
| `p_` | Parámetro de procedimiento/función | `p_id_medico` |
| `c_` | Constante | `c_IVA_SANITARIO` |
| `cur_` | Cursor | `cur_citas_hoy` |
| `e_` | Excepción definida por usuario | `e_stock_insuficiente` |

### 3.3 Literales

Un literal es un valor fijo escrito directamente en el código fuente.

```sql
DECLARE
    -- Literal numérico entero
    v_num_camas     NUMBER := 250;

    -- Literal numérico decimal
    v_temperatura   NUMBER := 36.7;

    -- Literal de cadena (siempre entre comillas SIMPLES)
    v_ciudad        VARCHAR2(30) := 'Sevilla';

    -- Literal de cadena con apóstrofe (se escapa duplicando la comilla simple)
    v_mensaje       VARCHAR2(50) := 'El paciente ''Juan'' no ha venido';

    -- Literal booleano (solo existe en PL/SQL, no en SQL)
    v_urgente       BOOLEAN := TRUE;
    v_dado_alta     BOOLEAN := FALSE;
    v_sin_dato      BOOLEAN := NULL;

    -- Literal de fecha (usando función TO_DATE para mayor control)
    v_fecha_ingreso DATE := TO_DATE('2024-03-15', 'YYYY-MM-DD');
BEGIN
    NULL;  -- Instrucción que no hace nada; útil como marcador de posición
END;
/
```

> ⚠️ **Error frecuente:** En PL/SQL las cadenas de texto usan **comillas simples** (`'`), nunca dobles. Las comillas dobles (`"`) se usan para identificadores con caracteres especiales o palabras reservadas: `"mi tabla"`.

### 3.4 Comentarios

Los comentarios son esenciales para mantener el código legible. El compilador los ignora completamente.

```sql
DECLARE
    v_edad NUMBER; -- Esta variable almacena la edad calculada del paciente
BEGIN
    /*
     * Bloque de cálculo de edad a partir de la fecha de nacimiento.
     * Usamos TRUNC para obtener solo la parte entera de los años.
     * Autor: Prof. García
     * Fecha: 2024-09-01
     */
    SELECT TRUNC(MONTHS_BETWEEN(SYSDATE, fecha_nac) / 12)
    INTO v_edad
    FROM PACIENTES
    WHERE id_paciente = 1;

    DBMS_OUTPUT.PUT_LINE('Edad del paciente: ' || v_edad || ' años');
END;
/
```

> 💡 **Buena práctica:** Comenta el *propósito* del código, no lo que es obvio. `-- Suma 1 a x` encima de `x := x + 1` no aporta nada; `-- Incrementa el contador de reintentos` sí lo hace.

---

## 4. Tipos de datos simples en PL/SQL

PL/SQL hereda todos los tipos de datos de SQL de Oracle y añade algunos propios (como `BOOLEAN`). Conocer bien los tipos de datos es fundamental para escribir programas correctos y eficientes.

### 4.1 Tipos numéricos

| Tipo | Descripción | Rango / Precisión | Cuándo usarlo |
|---|---|---|---|
| `NUMBER(p,s)` | Número de precisión variable | Hasta 38 dígitos significativos | Valores monetarios, medidas, contadores generales |
| `NUMBER(p)` | Entero de `p` dígitos | Hasta 38 dígitos | IDs, contadores grandes |
| `INTEGER` o `INT` | Alias de `NUMBER(38,0)` | Enteros hasta 38 dígitos | IDs, contadores |
| `PLS_INTEGER` | Entero nativo de 32 bits con signo | -2.147.483.648 a 2.147.483.647 | Contadores en bucles (más eficiente que NUMBER) |
| `BINARY_INTEGER` | Similar a PLS_INTEGER (deprecado) | -2.147.483.648 a 2.147.483.647 | Compatibilidad con código antiguo |
| `BINARY_FLOAT` | Flotante de precisión simple IEEE 754 | ~7 dígitos decimales | Cálculos científicos donde la velocidad prima sobre la precisión exacta |
| `BINARY_DOUBLE` | Flotante de precisión doble IEEE 754 | ~15 dígitos decimales | Cálculos científicos de alta precisión |

**Ejemplo práctico:**

```sql
DECLARE
    -- Para el salario de un médico: necesitamos decimales y precisión exacta
    v_salario       NUMBER(8,2)   := 3500.75;   -- hasta 999.999,99

    -- Para contar citas en un bucle: PLS_INTEGER es más rápido
    v_contador      PLS_INTEGER   := 0;

    -- Para calcular un IMC: decimales suficientes
    v_imc           NUMBER(5,2);

    v_peso          NUMBER(5,2)   := 78.5;      -- kg
    v_altura        NUMBER(4,2)   := 1.75;      -- metros
BEGIN
    v_imc := v_peso / (v_altura ** 2);
    DBMS_OUTPUT.PUT_LINE('IMC del paciente: ' || v_imc);
    -- Salida: IMC del paciente: 25.63
END;
/
```

### 4.2 Tipo booleano

El tipo `BOOLEAN` es **exclusivo de PL/SQL** y no existe en las tablas SQL de Oracle (no puedes tener una columna BOOLEAN en una tabla). Puede tomar tres valores: `TRUE`, `FALSE` y `NULL`.

```sql
DECLARE
    v_es_urgente    BOOLEAN := FALSE;
    v_tiene_cita    BOOLEAN;   -- Valor inicial: NULL
BEGIN
    v_es_urgente := TRUE;

    IF v_es_urgente THEN
        DBMS_OUTPUT.PUT_LINE('Atención prioritaria requerida');
    END IF;

    -- Cuidado con NULL: NULL no es TRUE ni FALSE
    IF v_tiene_cita IS NULL THEN
        DBMS_OUTPUT.PUT_LINE('No se sabe si el paciente tiene cita');
    END IF;
END;
/
```

> ⚠️ **Trampa habitual:** `NULL = NULL` devuelve `NULL` (no `TRUE`). Para comprobar si algo es nulo, usa siempre `IS NULL` o `IS NOT NULL`.

### 4.3 Tipos de carácter

| Tipo | Descripción | Tamaño máximo | Cuándo usarlo |
|---|---|---|---|
| `CHAR(n)` | Cadena de longitud **fija** (se rellena con espacios) | 32.767 bytes | Códigos de longitud fija: sexo ('M'/'F'), códigos postales |
| `VARCHAR2(n)` | Cadena de longitud **variable** | 32.767 bytes en PL/SQL | Nombres, descripciones, texto general |
| `NCHAR(n)` | Como CHAR pero con soporte Unicode (UTF-16) | 32.767 bytes | Texto multiidioma de longitud fija |
| `NVARCHAR2(n)` | Como VARCHAR2 pero con soporte Unicode | 32.767 bytes | Texto multiidioma de longitud variable |
| `LONG` | Texto muy largo (obsoleto, usar CLOB) | 32.760 bytes | Compatibilidad con código antiguo |

```sql
DECLARE
    -- CHAR: siempre ocupa exactamente n caracteres
    v_sexo          CHAR(1)       := 'F';

    -- VARCHAR2: longitud variable, más eficiente en memoria
    v_nombre        VARCHAR2(50)  := 'María';
    v_apellidos     VARCHAR2(100) := 'García López';

    -- Combinamos con el operador de concatenación ||
    v_nombre_completo VARCHAR2(151);
BEGIN
    v_nombre_completo := v_nombre || ' ' || v_apellidos;
    DBMS_OUTPUT.PUT_LINE('Paciente: ' || v_nombre_completo);
    -- Salida: Paciente: María García López
END;
/
```

### 4.4 Tipos de fecha y hora

| Tipo | Descripción | Precisión |
|---|---|---|
| `DATE` | Fecha y hora (año, mes, día, hora, min, seg) | Segundos |
| `TIMESTAMP` | Como DATE pero con fracciones de segundo | Hasta nanosegundos (9 decimales) |
| `TIMESTAMP WITH TIME ZONE` | TIMESTAMP incluyendo zona horaria | Nanosegundos + zona |
| `INTERVAL YEAR TO MONTH` | Duración en años y meses | Años/meses |
| `INTERVAL DAY TO SECOND` | Duración en días, horas, minutos y segundos | Hasta nanosegundos |

```sql
DECLARE
    v_fecha_nac     DATE      := TO_DATE('1985-06-20', 'YYYY-MM-DD');
    v_ahora         DATE      := SYSDATE;
    v_edad_anios    NUMBER;
    v_tiempo_reg    TIMESTAMP := SYSTIMESTAMP;  -- Incluye microsegundos

    -- Intervalo: tiempo desde que abrió el servicio de urgencias
    v_tiempo_espera INTERVAL DAY TO SECOND;
BEGIN
    -- Calcular edad en años completos
    v_edad_anios := TRUNC(MONTHS_BETWEEN(v_ahora, v_fecha_nac) / 12);
    DBMS_OUTPUT.PUT_LINE('Edad: ' || v_edad_anios || ' años');

    -- Mostrar fecha/hora de registro con precisión de microsegundos
    DBMS_OUTPUT.PUT_LINE('Registro: ' || TO_CHAR(v_tiempo_reg, 'DD/MM/YYYY HH24:MI:SS.FF3'));
END;
/
```

### 4.5 Tipos LOB (Large Object)

| Tipo | Descripción | Tamaño máximo |
|---|---|---|
| `CLOB` | Character Large Object — texto de gran tamaño | 128 TB (Oracle 21c) |
| `NCLOB` | Como CLOB con soporte Unicode | 128 TB |
| `BLOB` | Binary Large Object — datos binarios (imágenes, PDFs, etc.) | 128 TB |

```sql
DECLARE
    -- Un informe médico completo podría necesitar CLOB
    v_informe_medico CLOB;
BEGIN
    v_informe_medico := 'Paciente de 38 años que acude a urgencias por dolor torácico. '
                     || 'Se realizan las siguientes pruebas: ECG, analítica completa... '
                     || '[El informe puede contener miles de caracteres]';
    DBMS_OUTPUT.PUT_LINE('Longitud del informe: ' || LENGTH(v_informe_medico) || ' caracteres');
END;
/
```

### 4.6 Atributos de tipo: `%TYPE` y `%ROWTYPE`

Estos son dos atributos muy potentes y exclusivos de PL/SQL que permiten declarar variables del mismo tipo que una columna o fila de una tabla, sin necesidad de conocer ni especificar el tipo concreto. Si el tipo de la columna cambia en la base de datos, la variable se adapta automáticamente.

```sql
DECLARE
    -- %TYPE: la variable tendrá el mismo tipo que la columna indicada
    v_nombre_pac    PACIENTES.nombre%TYPE;        -- VARCHAR2(50)
    v_salario_med   MEDICOS.salario%TYPE;         -- NUMBER(8,2)

    -- %ROWTYPE: la variable es un "registro" con todos los campos de la tabla
    r_paciente      PACIENTES%ROWTYPE;
    r_medico        MEDICOS%ROWTYPE;
BEGIN
    -- Cargar una fila entera de PACIENTES en r_paciente
    SELECT * INTO r_paciente
    FROM PACIENTES
    WHERE id_paciente = 1;

    -- Acceder a los campos mediante la notación punto
    DBMS_OUTPUT.PUT_LINE('Nombre: ' || r_paciente.nombre);
    DBMS_OUTPUT.PUT_LINE('Apellidos: ' || r_paciente.apellidos);
    DBMS_OUTPUT.PUT_LINE('F.Nacimiento: ' || TO_CHAR(r_paciente.fecha_nac, 'DD/MM/YYYY'));
END;
/
```

> 💡 **Buena práctica:** Usa siempre `%TYPE` y `%ROWTYPE` en lugar de especificar tipos explícitamente. Hace el código más mantenible y evita errores cuando el esquema de la base de datos cambia.

---

## 5. Variables, constantes y operadores

### 5.1 Variables y constantes

#### Declaración de variables

La sintaxis completa para declarar una variable es:

```
nombre_variable [CONSTANT] tipo_dato [NOT NULL] [:= | DEFAULT valor_inicial];
```

Todos los elementos entre corchetes son opcionales.

```sql
DECLARE
    -------------------------------------------------------
    -- Variables simples
    -------------------------------------------------------
    v_contador      PLS_INTEGER := 0;
    v_mensaje       VARCHAR2(200);          -- Valor inicial: NULL
    v_fecha_hoy     DATE := SYSDATE;        -- Función como valor inicial

    -------------------------------------------------------
    -- Constantes (CONSTANT + valor obligatorio)
    -------------------------------------------------------
    c_MAX_CITAS_DIA CONSTANT PLS_INTEGER    := 20;
    c_HOSPITAL      CONSTANT VARCHAR2(50)   := 'Hospital Universitario Central';
    c_IVA           CONSTANT NUMBER(4,2)    := 0.10;  -- 10% de IVA

    -------------------------------------------------------
    -- Variables NOT NULL (deben tener valor inicial)
    -------------------------------------------------------
    v_activo        BOOLEAN NOT NULL := TRUE;

    -------------------------------------------------------
    -- Variables con tipo anclado a tabla
    -------------------------------------------------------
    v_nombre_pac    PACIENTES.nombre%TYPE;
    v_id_med        MEDICOS.id_medico%TYPE := 1;

BEGIN
    -- Asignación dentro del bloque
    v_contador  := v_contador + 1;
    v_mensaje   := 'Bienvenido a ' || c_HOSPITAL;

    DBMS_OUTPUT.PUT_LINE(v_mensaje);
    DBMS_OUTPUT.PUT_LINE('Máximo de citas por día: ' || c_MAX_CITAS_DIA);

    -- c_HOSPITAL := 'Otro hospital';  -- ¡ERROR! No se puede modificar una constante
END;
/
```

#### Asignación desde una consulta SQL: `SELECT INTO`

Una forma muy habitual de dar valor a variables en PL/SQL es ejecutar una consulta SQL que devuelva **exactamente una fila**:

```sql
DECLARE
    v_nombre        PACIENTES.nombre%TYPE;
    v_apellidos     PACIENTES.apellidos%TYPE;
    v_num_citas     NUMBER;
BEGIN
    -- Recuperar datos de un paciente concreto
    SELECT nombre, apellidos
    INTO v_nombre, v_apellidos
    FROM PACIENTES
    WHERE id_paciente = 42;

    -- Contar las citas pendientes de ese paciente
    SELECT COUNT(*)
    INTO v_num_citas
    FROM CITAS
    WHERE id_paciente = 42
      AND estado = 'PENDIENTE';

    DBMS_OUTPUT.PUT_LINE('Paciente: ' || v_nombre || ' ' || v_apellidos);
    DBMS_OUTPUT.PUT_LINE('Citas pendientes: ' || v_num_citas);
END;
/
```

> ⚠️ **Cuidado:** Si la consulta `SELECT INTO` devuelve **cero filas**, se lanza la excepción `NO_DATA_FOUND`. Si devuelve **más de una fila**, se lanza `TOO_MANY_ROWS`. Siempre hay que gestionar estas excepciones (ver sección 8).

### 5.2 Operadores

#### Operadores aritméticos

| Operador | Operación | Ejemplo | Resultado |
|---|---|---|---|
| `+` | Suma | `v_total := 10 + 5;` | 15 |
| `-` | Resta | `v_diff := 10 - 3;` | 7 |
| `*` | Multiplicación | `v_area := 5 * 3;` | 15 |
| `/` | División | `v_media := 15 / 4;` | 3.75 |
| `**` | Potencia | `v_cuad := 4 ** 2;` | 16 |
| `-` (unario) | Cambio de signo | `v_neg := -v_pos;` | Negativo |

#### Operadores de comparación

| Operador | Significado | Ejemplo |
|---|---|---|
| `=` | Igual a | `IF v_estado = 'ACTIVO'` |
| `<>` o `!=` | Distinto de | `IF v_estado <> 'BAJA'` |
| `<` | Menor que | `IF v_edad < 18` |
| `>` | Mayor que | `IF v_stock > 0` |
| `<=` | Menor o igual | `IF v_edad <= 65` |
| `>=` | Mayor o igual | `IF v_salario >= 2000` |
| `IS NULL` | Es nulo | `IF v_telefono IS NULL` |
| `IS NOT NULL` | No es nulo | `IF v_email IS NOT NULL` |
| `LIKE` | Patrón de texto | `IF v_nombre LIKE 'A%'` |
| `IN` | En una lista | `IF v_estado IN ('PENDIENTE','REALIZADA')` |
| `BETWEEN` | En un rango | `IF v_edad BETWEEN 18 AND 65` |

#### Operadores lógicos

| Operador | Función | Ejemplo |
|---|---|---|
| `AND` | Ambas condiciones deben ser verdaderas | `IF v_a > 0 AND v_b > 0` |
| `OR` | Al menos una condición debe ser verdadera | `IF v_a > 0 OR v_b > 0` |
| `NOT` | Niega la condición | `IF NOT v_activo` |

#### Operador de concatenación

```sql
DECLARE
    v_nombre    VARCHAR2(50) := 'Juan';
    v_apellido  VARCHAR2(50) := 'Martínez';
    v_edad      NUMBER       := 45;
    v_resultado VARCHAR2(200);
BEGIN
    -- El operador || concatena cadenas
    -- Para concatenar números, Oracle los convierte a texto automáticamente
    v_resultado := 'Dr. ' || v_nombre || ' ' || v_apellido
                || ' (edad: ' || v_edad || ' años)';
    DBMS_OUTPUT.PUT_LINE(v_resultado);
    -- Salida: Dr. Juan Martínez (edad: 45 años)
END;
/
```

#### Precedencia de operadores (de mayor a menor)

```
1. ** (potencia)
2. +, - (unarios)
3. *, / (multiplicación y división)
4. +, -, || (suma, resta, concatenación)
5. =, <, >, <=, >=, <>, !=, IS NULL, LIKE, IN, BETWEEN (comparación)
6. NOT (negación lógica)
7. AND (conjunción lógica)
8. OR (disyunción lógica)
```

> 💡 **Consejo:** Cuando tengas dudas sobre el orden de evaluación, usa paréntesis. `(v_a + v_b) * v_c` es siempre más claro que `v_a + v_b * v_c`, aunque el resultado sea diferente.

---

## 6. Estructuras de control de flujo: Condicionales

Las estructuras condicionales permiten que el programa tome decisiones: ejecutar unas instrucciones u otras en función del valor de ciertas condiciones.

### 6.1 Sentencia IF

#### Variante 1: IF simple

Ejecuta el bloque solo si la condición es verdadera.

```sql
-- Sintaxis
IF condición THEN
    instrucciones;
END IF;
```

**Ejemplo:** Alertar si el stock de un medicamento es bajo:

```sql
DECLARE
    v_stock         MEDICAMENTOS.stock%TYPE;
    v_nombre_med    MEDICAMENTOS.nombre%TYPE;
BEGIN
    SELECT stock, nombre
    INTO v_stock, v_nombre_med
    FROM MEDICAMENTOS
    WHERE id_medicamento = 5;

    IF v_stock < 10 THEN
        DBMS_OUTPUT.PUT_LINE('⚠️ ALERTA: Stock bajo para ' || v_nombre_med
                            || '. Quedan ' || v_stock || ' unidades.');
    END IF;
END;
/
```

#### Variante 2: IF-ELSE

Ejecuta un bloque si la condición es verdadera y otro si es falsa.

```sql
-- Sintaxis
IF condición THEN
    instrucciones_verdadero;
ELSE
    instrucciones_falso;
END IF;
```

**Ejemplo:** Clasificar una cita como urgente o normal:

```sql
DECLARE
    v_motivo    CITAS.motivo%TYPE;
    v_tipo      VARCHAR2(20);
BEGIN
    SELECT motivo
    INTO v_motivo
    FROM CITAS
    WHERE id_cita = 101;

    IF UPPER(v_motivo) LIKE '%URGENTE%' OR UPPER(v_motivo) LIKE '%DOLOR%' THEN
        v_tipo := 'URGENTE';
    ELSE
        v_tipo := 'NORMAL';
    END IF;

    DBMS_OUTPUT.PUT_LINE('Tipo de cita: ' || v_tipo);
END;
/
```

#### Variante 3: IF-ELSIF-ELSE

Evalúa múltiples condiciones en orden. Solo se ejecuta el bloque de la **primera condición verdadera**.

```sql
-- Sintaxis
IF condición1 THEN
    instrucciones1;
ELSIF condición2 THEN
    instrucciones2;
ELSIF condición3 THEN
    instrucciones3;
[...]
ELSE
    instrucciones_por_defecto;
END IF;
```

**Ejemplo:** Clasificar la edad de un paciente en grupos de riesgo:

```sql
DECLARE
    v_fecha_nac     PACIENTES.fecha_nac%TYPE;
    v_edad          NUMBER;
    v_grupo_riesgo  VARCHAR2(30);
    v_recomendacion VARCHAR2(200);
BEGIN
    SELECT fecha_nac
    INTO v_fecha_nac
    FROM PACIENTES
    WHERE id_paciente = 15;

    v_edad := TRUNC(MONTHS_BETWEEN(SYSDATE, v_fecha_nac) / 12);

    IF v_edad < 12 THEN
        v_grupo_riesgo  := 'PEDIÁTRICO';
        v_recomendacion := 'Derivar a pediatría';
    ELSIF v_edad < 18 THEN
        v_grupo_riesgo  := 'ADOLESCENTE';
        v_recomendacion := 'Valoración por medicina general';
    ELSIF v_edad < 65 THEN
        v_grupo_riesgo  := 'ADULTO';
        v_recomendacion := 'Seguimiento estándar';
    ELSIF v_edad < 80 THEN
        v_grupo_riesgo  := 'TERCERA EDAD';
        v_recomendacion := 'Revisión anual obligatoria';
    ELSE
        v_grupo_riesgo  := 'GRAN DEPENDIENTE';
        v_recomendacion := 'Atención domiciliaria recomendada';
    END IF;

    DBMS_OUTPUT.PUT_LINE('Paciente de ' || v_edad || ' años');
    DBMS_OUTPUT.PUT_LINE('Grupo de riesgo: ' || v_grupo_riesgo);
    DBMS_OUTPUT.PUT_LINE('Recomendación: ' || v_recomendacion);
END;
/
```

> ⚠️ **Error frecuente:** Nota que en PL/SQL es `ELSIF` (sin segunda 'E'), no `ELSEIF` como en otros lenguajes. Escribirlo como `ELSEIF` provoca un error de compilación.

### 6.2 Sentencia CASE

La sentencia `CASE` es más elegante que una cadena de `IF/ELSIF` cuando estamos comparando **una misma variable** contra múltiples valores posibles. Hay dos formas:

#### CASE simple (compara una expresión contra valores concretos)

```sql
-- Sintaxis
CASE expresión
    WHEN valor1 THEN instrucciones1;
    WHEN valor2 THEN instrucciones2;
    [...]
    [ELSE instrucciones_por_defecto;]
END CASE;
```

**Ejemplo:** Traducir el código de especialidad médica:

```sql
DECLARE
    v_especialidad  MEDICOS.especialidad%TYPE;
    v_descripcion   VARCHAR2(100);
BEGIN
    SELECT especialidad
    INTO v_especialidad
    FROM MEDICOS
    WHERE id_medico = 3;

    CASE v_especialidad
        WHEN 'CARDIO'  THEN v_descripcion := 'Cardiología - Enfermedades del corazón';
        WHEN 'NEURO'   THEN v_descripcion := 'Neurología - Sistema nervioso';
        WHEN 'TRAUMA'  THEN v_descripcion := 'Traumatología - Aparato locomotor';
        WHEN 'PEDIATR' THEN v_descripcion := 'Pediatría - Pacientes infantiles';
        WHEN 'ONCOL'   THEN v_descripcion := 'Oncología - Tratamiento del cáncer';
        ELSE                v_descripcion := 'Medicina General';
    END CASE;

    DBMS_OUTPUT.PUT_LINE('Especialidad: ' || v_descripcion);
END;
/
```

#### CASE buscado (evalúa condiciones independientes)

```sql
-- Sintaxis
CASE
    WHEN condición1 THEN instrucciones1;
    WHEN condición2 THEN instrucciones2;
    [...]
    [ELSE instrucciones_por_defecto;]
END CASE;
```

**Ejemplo:** Calcular el descuento en medicamentos según el tipo de paciente y su edad:

```sql
DECLARE
    v_edad          NUMBER := 72;
    v_tiene_seguro  BOOLEAN := TRUE;
    v_num_cronicas  NUMBER := 3;       -- Número de enfermedades crónicas
    v_descuento     NUMBER;
    v_razon         VARCHAR2(100);
BEGIN
    CASE
        WHEN v_edad >= 65 AND v_num_cronicas >= 2 THEN
            v_descuento := 90;
            v_razon     := 'Pensionista con pluripatología';
        WHEN v_edad >= 65 THEN
            v_descuento := 60;
            v_razon     := 'Pensionista mayor de 65 años';
        WHEN v_tiene_seguro AND v_num_cronicas >= 1 THEN
            v_descuento := 40;
            v_razon     := 'Asegurado con enfermedad crónica';
        WHEN v_tiene_seguro THEN
            v_descuento := 10;
            v_razon     := 'Beneficiario de seguro estándar';
        ELSE
            v_descuento := 0;
            v_razon     := 'Sin descuento aplicable';
    END CASE;

    DBMS_OUTPUT.PUT_LINE('Descuento: ' || v_descuento || '%');
    DBMS_OUTPUT.PUT_LINE('Motivo: ' || v_razon);
END;
/
```

#### CASE como expresión (devuelve un valor)

Además de como sentencia de control, `CASE` puede usarse como **expresión** dentro de una asignación:

```sql
DECLARE
    v_estado    CITAS.estado%TYPE := 'REALIZADA';
    v_icono     VARCHAR2(5);
BEGIN
    -- CASE como expresión que se asigna directamente
    v_icono := CASE v_estado
                   WHEN 'PENDIENTE'  THEN '⏳'
                   WHEN 'REALIZADA'  THEN '✅'
                   WHEN 'CANCELADA'  THEN '❌'
                   ELSE '❓'
               END;

    DBMS_OUTPUT.PUT_LINE('Estado: ' || v_icono || ' ' || v_estado);
END;
/
```

---

## 7. Estructuras de control de flujo: Bucles

Los bucles permiten repetir un bloque de instrucciones un número determinado o indeterminado de veces. PL/SQL ofrece tres tipos de bucles, cada uno con su semántica particular.

### 7.1 Bucle LOOP básico

El bucle `LOOP` es el más simple: se repite **indefinidamente** hasta que se ejecuta explícitamente una instrucción `EXIT` o `EXIT WHEN`.

```sql
-- Sintaxis
LOOP
    instrucciones;
    EXIT [WHEN condición];
END LOOP;
```

**Ejemplo:** Generar recordatorios para las próximas citas hasta un máximo de 5:

```sql
DECLARE
    v_contador  PLS_INTEGER := 1;
    v_max_avisos CONSTANT PLS_INTEGER := 5;
BEGIN
    LOOP
        DBMS_OUTPUT.PUT_LINE('Enviando recordatorio #' || v_contador
                            || ' de ' || v_max_avisos);

        v_contador := v_contador + 1;

        EXIT WHEN v_contador > v_max_avisos;
    END LOOP;

    DBMS_OUTPUT.PUT_LINE('Proceso de recordatorios completado.');
END;
/
```

**Salida:**
```
Enviando recordatorio #1 de 5
Enviando recordatorio #2 de 5
Enviando recordatorio #3 de 5
Enviando recordatorio #4 de 5
Enviando recordatorio #5 de 5
Proceso de recordatorios completado.
```

> ⚠️ **Bucle infinito:** Si olvidas la condición `EXIT WHEN`, el bucle nunca terminará. En Oracle SQL Developer puedes cancelar la ejecución con el botón de parada (⏹).

### 7.2 Bucle WHILE

El bucle `WHILE` evalúa la condición **antes** de cada iteración. Si la condición es falsa desde el inicio, el cuerpo del bucle no se ejecuta nunca.

```sql
-- Sintaxis
WHILE condición LOOP
    instrucciones;
END LOOP;
```

**Ejemplo:** Acumular el coste total de medicamentos de una prescripción hasta que el importe supere el límite de la cobertura del seguro:

```sql
DECLARE
    v_coste_total   NUMBER(8,2) := 0;
    v_limite_seguro CONSTANT NUMBER(8,2) := 500.00;
    v_iteracion     PLS_INTEGER := 1;
    v_precio_unitario NUMBER(6,2);

    -- Simulamos precios de medicamentos (normalmente vendría de tabla)
    TYPE t_precios IS TABLE OF NUMBER INDEX BY PLS_INTEGER;
    v_precios t_precios;
BEGIN
    -- Cargamos precios simulados
    v_precios(1) := 45.20;
    v_precios(2) := 128.50;
    v_precios(3) := 89.90;
    v_precios(4) := 210.00;
    v_precios(5) := 75.30;

    WHILE v_iteracion <= v_precios.COUNT AND v_coste_total <= v_limite_seguro LOOP
        v_precio_unitario := v_precios(v_iteracion);
        v_coste_total     := v_coste_total + v_precio_unitario;

        DBMS_OUTPUT.PUT_LINE('Medicamento #' || v_iteracion
                            || ': +' || v_precio_unitario
                            || ' € → Total: ' || v_coste_total || ' €');

        v_iteracion := v_iteracion + 1;
    END LOOP;

    IF v_coste_total > v_limite_seguro THEN
        DBMS_OUTPUT.PUT_LINE('⚠️ Límite de cobertura superado (' || v_limite_seguro || ' €)');
    ELSE
        DBMS_OUTPUT.PUT_LINE('✅ Coste total dentro de cobertura: ' || v_coste_total || ' €');
    END IF;
END;
/
```

### 7.3 Bucle FOR

El bucle `FOR` numérico itera sobre un rango de enteros de forma automática. A diferencia de otros lenguajes, **la variable de control se declara implícitamente** (no hay que declararla en `DECLARE`) y es de solo lectura dentro del bucle.

```sql
-- Sintaxis
FOR variable IN [REVERSE] inicio..fin LOOP
    instrucciones;
END LOOP;
```

**Ejemplo:** Generar una tabla de ocupación de camas para los próximos 7 días:

```sql
DECLARE
    v_fecha_base    DATE := TRUNC(SYSDATE);
    v_num_citas     NUMBER;
BEGIN
    DBMS_OUTPUT.PUT_LINE('=== Previsión de citas próximos 7 días ===');

    FOR i IN 1..7 LOOP
        SELECT COUNT(*)
        INTO v_num_citas
        FROM CITAS
        WHERE TRUNC(fecha_cita) = v_fecha_base + i
          AND estado = 'PENDIENTE';

        DBMS_OUTPUT.PUT_LINE(
            TO_CHAR(v_fecha_base + i, 'DD/MM/YYYY DY') || ': '
            || v_num_citas || ' citas pendientes'
        );
    END LOOP;
END;
/
```

**Ejemplo con REVERSE** — contar atrás:

```sql
BEGIN
    DBMS_OUTPUT.PUT_LINE('Cuenta atrás para inicio de turno:');
    FOR i IN REVERSE 1..5 LOOP
        DBMS_OUTPUT.PUT_LINE(i || '...');
    END LOOP;
    DBMS_OUTPUT.PUT_LINE('¡Turno iniciado!');
END;
/
```

> 💡 La variable `i` en el bucle `FOR` no necesita declararse y no puede modificarse dentro del bucle. Si intentas hacer `i := i + 1` dentro del bucle, Oracle lanzará un error de compilación.

### 7.4 Bucles anidados y etiquetas

Los bucles pueden anidarse unos dentro de otros. Cuando se usan bucles anidados, las instrucciones `EXIT` y `CONTINUE` afectan solo al bucle más interno. Para controlar un bucle externo desde uno interno, se usan **etiquetas** (`<<nombre_etiqueta>>`):

```sql
DECLARE
    v_total_citas PLS_INTEGER := 0;
BEGIN
    <<bucle_medicos>>
    FOR id_med IN 1..5 LOOP

        <<bucle_dias>>
        FOR dia IN 1..7 LOOP
            DECLARE
                v_citas_dia NUMBER;
            BEGIN
                SELECT COUNT(*)
                INTO v_citas_dia
                FROM CITAS
                WHERE id_medico = id_med
                  AND TRUNC(fecha_cita) = TRUNC(SYSDATE) + dia
                  AND estado = 'PENDIENTE';

                v_total_citas := v_total_citas + v_citas_dia;

                -- Si un médico supera 15 citas en un día, pasamos al siguiente médico
                IF v_citas_dia > 15 THEN
                    DBMS_OUTPUT.PUT_LINE('Médico ' || id_med || ': agenda saturada en día ' || dia);
                    EXIT bucle_dias;  -- Sale del bucle interno, continúa con el externo
                END IF;

            EXCEPTION
                WHEN NO_DATA_FOUND THEN NULL;
            END;
        END LOOP bucle_dias;

    END LOOP bucle_medicos;

    DBMS_OUTPUT.PUT_LINE('Total citas en la semana: ' || v_total_citas);
END;
/
```

---

## 8. Manejo de errores: Excepciones

En PL/SQL, cuando ocurre un error en tiempo de ejecución, se dice que se ha **lanzado una excepción**. Si esa excepción no está capturada por ningún bloque `EXCEPTION`, el programa termina abruptamente con un mensaje de error.

La estructura general del manejo de excepciones es:

```sql
BEGIN
    -- Código que puede generar errores
    instrucciones;
EXCEPTION
    WHEN nombre_excepcion1 THEN
        -- Qué hacer si ocurre la excepción 1
        instrucciones_error1;
    WHEN nombre_excepcion2 THEN
        -- Qué hacer si ocurre la excepción 2
        instrucciones_error2;
    WHEN OTHERS THEN
        -- Captura cualquier excepción no prevista anteriormente
        -- Usar SQLCODE y SQLERRM para diagnóstico
        DBMS_OUTPUT.PUT_LINE('Error ' || SQLCODE || ': ' || SQLERRM);
END;
/
```

### 8.1 Excepciones predefinidas por Oracle

Oracle tiene una lista de excepciones predefinidas con nombre. Las más importantes son:

| Excepción | Código SQLCODE | Cuándo se lanza |
|---|---|---|
| `NO_DATA_FOUND` | +100 | `SELECT INTO` no devuelve ninguna fila |
| `TOO_MANY_ROWS` | -1422 | `SELECT INTO` devuelve más de una fila |
| `ZERO_DIVIDE` | -1476 | División entre cero |
| `VALUE_ERROR` | -6502 | Error de conversión o truncamiento de valor |
| `INVALID_NUMBER` | -1722 | Conversión inválida de texto a número |
| `DUP_VAL_ON_INDEX` | -1 | Violación de restricción UNIQUE o PRIMARY KEY |
| `INVALID_CURSOR` | -1001 | Operación inválida sobre un cursor |
| `CURSOR_ALREADY_OPEN` | -6511 | Intentar abrir un cursor ya abierto |
| `STORAGE_ERROR` | -6500 | Sin memoria disponible en el servidor |
| `TIMEOUT_ON_RESOURCE` | -51 | Tiempo de espera agotado para un recurso |

**Ejemplo completo:** Buscar un paciente gestionando los casos de error:

```sql
DECLARE
    r_paciente      PACIENTES%ROWTYPE;
    v_id_buscar     NUMBER := 9999;  -- ID que no existe
BEGIN
    SELECT *
    INTO r_paciente
    FROM PACIENTES
    WHERE id_paciente = v_id_buscar;

    -- Si llegamos aquí, el paciente existe
    DBMS_OUTPUT.PUT_LINE('Paciente encontrado: '
                        || r_paciente.nombre || ' ' || r_paciente.apellidos);
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('❌ Error: No existe ningún paciente con ID ' || v_id_buscar);

    WHEN TOO_MANY_ROWS THEN
        -- Esto no debería pasar (id_paciente es PK), pero es buena práctica contemplarlo
        DBMS_OUTPUT.PUT_LINE('❌ Error: La consulta devolvió múltiples resultados inesperadamente.');

    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('❌ Error inesperado [' || SQLCODE || ']: ' || SQLERRM);
END;
/
```

**Ejemplo con ZERO_DIVIDE:**

```sql
DECLARE
    v_total_pacientes   NUMBER;
    v_total_medicos     NUMBER;
    v_ratio             NUMBER(6,2);
BEGIN
    SELECT COUNT(*) INTO v_total_pacientes FROM PACIENTES;
    SELECT COUNT(*) INTO v_total_medicos   FROM MEDICOS;

    -- Si no hay médicos, esto lanzaría ZERO_DIVIDE
    v_ratio := v_total_pacientes / v_total_medicos;

    DBMS_OUTPUT.PUT_LINE('Ratio pacientes/médico: ' || v_ratio);
EXCEPTION
    WHEN ZERO_DIVIDE THEN
        DBMS_OUTPUT.PUT_LINE('⚠️ No hay médicos registrados en el sistema. '
                            || 'No se puede calcular el ratio.');
END;
/
```

### 8.2 Excepciones de Oracle sin nombre

Hay errores de Oracle que no tienen un nombre asignado en el estándar de PL/SQL. Para capturarlos específicamente (en lugar de usar `WHEN OTHERS`), podemos asociarles un nombre con la directiva de compilación `PRAGMA EXCEPTION_INIT`.

**Sintaxis:**
```sql
DECLARE
    nombre_excepcion EXCEPTION;
    PRAGMA EXCEPTION_INIT(nombre_excepcion, codigo_error_oracle);
```

**Ejemplo:** Capturar el error de restricción de integridad referencial (ORA-02292):

```sql
DECLARE
    e_tiene_citas_asociadas EXCEPTION;
    PRAGMA EXCEPTION_INIT(e_tiene_citas_asociadas, -2292);
    -- ORA-02292: integrity constraint violated - child record found

    v_id_medico_borrar NUMBER := 3;
BEGIN
    DELETE FROM MEDICOS
    WHERE id_medico = v_id_medico_borrar;

    DBMS_OUTPUT.PUT_LINE('Médico eliminado correctamente.');
EXCEPTION
    WHEN e_tiene_citas_asociadas THEN
        DBMS_OUTPUT.PUT_LINE('❌ No se puede eliminar el médico ' || v_id_medico_borrar
                            || ' porque tiene citas registradas en el sistema.');
        ROLLBACK;
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error inesperado: ' || SQLCODE || ' - ' || SQLERRM);
        ROLLBACK;
END;
/
```

### 8.3 Excepciones definidas por el usuario

El programador puede definir sus propias excepciones para controlar situaciones de negocio que no corresponden a errores del sistema Oracle.

**Pasos:**
1. Declarar la excepción con la palabra clave `EXCEPTION` en `DECLARE`.
2. Lanzarla con `RAISE nombre_excepcion` en el lugar adecuado del código.
3. Capturarla en el bloque `EXCEPTION` con `WHEN nombre_excepcion THEN`.

**Ejemplo:** Validar que la edad de un paciente es coherente antes de registrarlo:

```sql
DECLARE
    -- Paso 1: Declarar excepciones de negocio
    e_edad_invalida         EXCEPTION;
    e_paciente_duplicado    EXCEPTION;

    -- Datos del nuevo paciente a insertar
    v_nombre        VARCHAR2(50)  := 'Luis';
    v_apellidos     VARCHAR2(100) := 'Fernández Torres';
    v_fecha_nac     DATE         := TO_DATE('1890-01-01', 'YYYY-MM-DD'); -- Fecha sospechosa
    v_edad_calc     NUMBER;
    v_existe        NUMBER;
BEGIN
    -- Calcular edad
    v_edad_calc := TRUNC(MONTHS_BETWEEN(SYSDATE, v_fecha_nac) / 12);

    -- Paso 2: Lanzar excepción si la validación falla
    IF v_edad_calc > 130 OR v_edad_calc < 0 THEN
        RAISE e_edad_invalida;
    END IF;

    -- Verificar si el paciente ya existe (mismo nombre + misma fecha de nacimiento)
    SELECT COUNT(*) INTO v_existe
    FROM PACIENTES
    WHERE nombre = v_nombre
      AND apellidos = v_apellidos
      AND fecha_nac = v_fecha_nac;

    IF v_existe > 0 THEN
        RAISE e_paciente_duplicado;
    END IF;

    -- Si todo está bien, insertar el paciente
    INSERT INTO PACIENTES (id_paciente, nombre, apellidos, fecha_nac, num_historial)
    VALUES (SEQ_PACIENTES.NEXTVAL, v_nombre, v_apellidos, v_fecha_nac, 'H-'||SEQ_PACIENTES.CURRVAL);

    COMMIT;
    DBMS_OUTPUT.PUT_LINE('✅ Paciente registrado correctamente.');

-- Paso 3: Capturar excepciones
EXCEPTION
    WHEN e_edad_invalida THEN
        DBMS_OUTPUT.PUT_LINE('❌ Error de validación: La edad calculada ('
                            || v_edad_calc || ' años) no es válida para un paciente.');
        ROLLBACK;

    WHEN e_paciente_duplicado THEN
        DBMS_OUTPUT.PUT_LINE('❌ Error: Ya existe un paciente con estos datos en el sistema.');
        ROLLBACK;

    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('❌ Error inesperado [' || SQLCODE || ']: ' || SQLERRM);
        ROLLBACK;
END;
/
```

#### Propagación de excepciones entre bloques anidados

Cuando una excepción no se captura en un bloque interno, se **propaga** al bloque externo:

```sql
BEGIN
    -- Bloque externo
    DBMS_OUTPUT.PUT_LINE('Inicio del proceso');

    BEGIN
        -- Bloque interno
        DECLARE
            v_resultado NUMBER;
        BEGIN
            v_resultado := 10 / 0;  -- Lanza ZERO_DIVIDE
        END;
        -- Esta línea no se ejecuta porque el error propaga hacia arriba
        DBMS_OUTPUT.PUT_LINE('Esto no se imprime');
    EXCEPTION
        WHEN ZERO_DIVIDE THEN
            -- La excepción SÍ se captura aquí (bloque externo)
            DBMS_OUTPUT.PUT_LINE('⚠️ División por cero capturada en el bloque externo.');
    END;

    DBMS_OUTPUT.PUT_LINE('El proceso continúa tras gestionar el error.');
END;
/
```

---

## 9. Librerías básicas y de funciones en PL/SQL

Oracle proporciona una extensa colección de **paquetes predefinidos** (librerías) que amplían enormemente las capacidades de PL/SQL. Un paquete es un contenedor de procedimientos y funciones relacionadas.

### 9.1 Librerías básicas (paquetes del sistema)

#### `DBMS_OUTPUT` — Salida de texto

El paquete más utilizado para depuración y pruebas.

```sql
BEGIN
    -- PUT_LINE: imprime texto y añade salto de línea
    DBMS_OUTPUT.PUT_LINE('Procesando pacientes...');

    -- PUT: imprime texto sin salto de línea
    DBMS_OUTPUT.PUT('Nombre: ');
    DBMS_OUTPUT.PUT('Juan ');
    DBMS_OUTPUT.NEW_LINE;   -- Salto de línea manual

    -- Imprimir el valor de una variable
    DECLARE v_n NUMBER := 42;
    BEGIN
        DBMS_OUTPUT.PUT_LINE('El número es: ' || v_n);
    END;
END;
/
```

#### `UTL_FILE` — Lectura y escritura de ficheros

Permite leer y escribir ficheros en el sistema operativo del servidor Oracle. Muy útil para generar informes o leer datos de entrada.

```sql
DECLARE
    v_fichero   UTL_FILE.FILE_TYPE;
    v_linea     VARCHAR2(500);
    v_num_pac   NUMBER;
BEGIN
    -- El directorio 'INFORMES_DIR' debe estar creado previamente como DIRECTORY en Oracle
    -- CREATE OR REPLACE DIRECTORY INFORMES_DIR AS '/opt/oracle/informes';

    v_fichero := UTL_FILE.FOPEN('INFORMES_DIR', 'informe_citas_' ||
                                TO_CHAR(SYSDATE,'YYYYMMDD') || '.txt', 'W');

    UTL_FILE.PUT_LINE(v_fichero, 'INFORME DE CITAS - ' || TO_CHAR(SYSDATE, 'DD/MM/YYYY'));
    UTL_FILE.PUT_LINE(v_fichero, RPAD('=', 50, '='));

    FOR rec IN (SELECT p.nombre || ' ' || p.apellidos AS paciente,
                       c.fecha_cita, m.apellidos AS medico, c.estado
                FROM CITAS c
                JOIN PACIENTES p ON c.id_paciente = p.id_paciente
                JOIN MEDICOS m   ON c.id_medico   = m.id_medico
                WHERE TRUNC(c.fecha_cita) = TRUNC(SYSDATE)
                ORDER BY c.fecha_cita)
    LOOP
        v_linea := TO_CHAR(rec.fecha_cita, 'HH24:MI') || ' | '
                || RPAD(rec.paciente, 30) || ' | Dr. '
                || RPAD(rec.medico, 20) || ' | ' || rec.estado;
        UTL_FILE.PUT_LINE(v_fichero, v_linea);
    END LOOP;

    UTL_FILE.FCLOSE(v_fichero);
    DBMS_OUTPUT.PUT_LINE('✅ Informe generado correctamente.');
EXCEPTION
    WHEN UTL_FILE.INVALID_PATH THEN
        DBMS_OUTPUT.PUT_LINE('❌ Error: Ruta de directorio no válida o no configurada.');
    WHEN OTHERS THEN
        IF UTL_FILE.IS_OPEN(v_fichero) THEN
            UTL_FILE.FCLOSE(v_fichero);
        END IF;
        DBMS_OUTPUT.PUT_LINE('❌ Error: ' || SQLERRM);
END;
/
```

#### `DBMS_SCHEDULER` — Planificación de tareas

Permite programar la ejecución automática de bloques PL/SQL o scripts en momentos determinados.

```sql
BEGIN
    -- Crear un trabajo que se ejecuta cada día a las 23:00
    -- para enviar recordatorios de citas del día siguiente
    DBMS_SCHEDULER.CREATE_JOB(
        job_name        => 'JOB_RECORDATORIO_CITAS',
        job_type        => 'PLSQL_BLOCK',
        job_action      => 'BEGIN
                                -- Aquí iría el código de envío de recordatorios
                                DBMS_OUTPUT.PUT_LINE(''Recordatorios enviados: '' || SYSDATE);
                            END;',
        start_date      => SYSTIMESTAMP,
        repeat_interval => 'FREQ=DAILY; BYHOUR=23; BYMINUTE=0; BYSECOND=0',
        end_date        => NULL,   -- Sin fecha de fin
        enabled         => TRUE,
        comments        => 'Envío diario de recordatorios de citas para el día siguiente'
    );
    DBMS_OUTPUT.PUT_LINE('✅ Tarea programada creada correctamente.');
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error al crear tarea: ' || SQLERRM);
END;
/
```

#### `DBMS_LOCK` — Control de bloqueos concurrentes

```sql
DECLARE
    v_resultado     NUMBER;
    v_handle        VARCHAR2(128);
    c_LOCK_AGENDA   CONSTANT VARCHAR2(30) := 'LOCK_AGENDA_MED_5';
BEGIN
    -- Obtener un "handle" para el bloqueo con nombre
    DBMS_LOCK.ALLOCATE_UNIQUE(c_LOCK_AGENDA, v_handle);

    -- Solicitar bloqueo exclusivo (modo 6), timeout de 10 segundos
    v_resultado := DBMS_LOCK.REQUEST(v_handle, DBMS_LOCK.X_MODE, 10);

    CASE v_resultado
        WHEN 0 THEN
            DBMS_OUTPUT.PUT_LINE('✅ Bloqueo adquirido. Modificando agenda del médico...');
            -- ... modificaciones en la agenda ...
            v_resultado := DBMS_LOCK.RELEASE(v_handle);
            DBMS_OUTPUT.PUT_LINE('Bloqueo liberado.');
        WHEN 1 THEN
            DBMS_OUTPUT.PUT_LINE('⏳ Timeout: Otro proceso está modificando la agenda.');
        WHEN 4 THEN
            DBMS_OUTPUT.PUT_LINE('⚠️ Deadlock detectado.');
        ELSE
            DBMS_OUTPUT.PUT_LINE('Error al solicitar bloqueo: código ' || v_resultado);
    END CASE;
END;
/
```

### 9.2 Librerías de funciones

#### Funciones de cadena de texto

```sql
DECLARE
    v_nombre    VARCHAR2(50) := '  maría josé  ';
    v_historial VARCHAR2(20) := 'H-2024-00123';
BEGIN
    -- UPPER / LOWER / INITCAP: cambiar mayúsculas/minúsculas
    DBMS_OUTPUT.PUT_LINE(UPPER(v_nombre));       -- '  MARÍA JOSÉ  '
    DBMS_OUTPUT.PUT_LINE(LOWER(v_nombre));       -- '  maría josé  '
    DBMS_OUTPUT.PUT_LINE(INITCAP(v_nombre));     -- '  María José  '

    -- TRIM / LTRIM / RTRIM: eliminar espacios
    DBMS_OUTPUT.PUT_LINE(TRIM(v_nombre));        -- 'maría josé'
    DBMS_OUTPUT.PUT_LINE(LTRIM(v_nombre));       -- 'maría josé  '
    DBMS_OUTPUT.PUT_LINE(RTRIM(v_nombre));       -- '  maría josé'

    -- LENGTH: longitud de la cadena
    DBMS_OUTPUT.PUT_LINE(LENGTH(v_historial));   -- 12

    -- SUBSTR: subcadena (posición inicial, longitud)
    DBMS_OUTPUT.PUT_LINE(SUBSTR(v_historial, 3, 4));  -- '2024'

    -- INSTR: posición de una subcadena dentro de otra
    DBMS_OUTPUT.PUT_LINE(INSTR(v_historial, '-'));     -- 2 (primera ocurrencia)

    -- REPLACE: reemplazar texto
    DBMS_OUTPUT.PUT_LINE(REPLACE(v_historial, '-', '/'));  -- 'H/2024/00123'

    -- LPAD / RPAD: rellenar con caracteres hasta una longitud
    DBMS_OUTPUT.PUT_LINE(LPAD('42', 6, '0'));     -- '000042'
    DBMS_OUTPUT.PUT_LINE(RPAD('Cita', 10, '.'));  -- 'Cita......'
END;
/
```

#### Funciones matemáticas

```sql
DECLARE
    v_peso      NUMBER := -72.6;
    v_altura    NUMBER := 1.75;
    v_imc       NUMBER;
BEGIN
    -- ABS: valor absoluto
    DBMS_OUTPUT.PUT_LINE(ABS(v_peso));           -- 72.6

    -- ROUND: redondeo (segundo parámetro = decimales)
    v_imc := (ABS(v_peso)) / (v_altura ** 2);
    DBMS_OUTPUT.PUT_LINE(ROUND(v_imc, 2));       -- 23.71

    -- TRUNC: truncar decimales sin redondear
    DBMS_OUTPUT.PUT_LINE(TRUNC(v_imc, 1));       -- 23.7

    -- CEIL / FLOOR: redondeo hacia arriba / abajo
    DBMS_OUTPUT.PUT_LINE(CEIL(v_imc));           -- 24
    DBMS_OUTPUT.PUT_LINE(FLOOR(v_imc));          -- 23

    -- MOD: resto de la división (útil para saber si es par/impar)
    DBMS_OUTPUT.PUT_LINE(MOD(10, 3));            -- 1

    -- POWER: potencia (equivalente a **)
    DBMS_OUTPUT.PUT_LINE(POWER(v_altura, 2));    -- 3.0625

    -- SQRT: raíz cuadrada
    DBMS_OUTPUT.PUT_LINE(SQRT(16));              -- 4

    -- SIGN: signo del número (-1, 0, 1)
    DBMS_OUTPUT.PUT_LINE(SIGN(-5));              -- -1
END;
/
```

#### Funciones de fecha y hora

```sql
DECLARE
    v_fecha_nac     DATE := TO_DATE('1985-06-20', 'YYYY-MM-DD');
    v_fecha_cita    DATE := TO_DATE('2024-12-15', 'YYYY-MM-DD');
BEGIN
    -- SYSDATE: fecha y hora actuales del servidor
    DBMS_OUTPUT.PUT_LINE('Hoy: ' || TO_CHAR(SYSDATE, 'DD/MM/YYYY HH24:MI:SS'));

    -- SYSTIMESTAMP: como SYSDATE pero con fracciones de segundo y zona horaria
    DBMS_OUTPUT.PUT_LINE('Ahora: ' || TO_CHAR(SYSTIMESTAMP, 'DD/MM/YYYY HH24:MI:SS.FF3 TZH:TZM'));

    -- TRUNC: elimina la parte horaria de una fecha
    DBMS_OUTPUT.PUT_LINE(TO_CHAR(TRUNC(SYSDATE), 'DD/MM/YYYY'));  -- Solo fecha

    -- MONTHS_BETWEEN: meses entre dos fechas
    DBMS_OUTPUT.PUT_LINE('Meses entre fechas: ' ||
        ROUND(MONTHS_BETWEEN(SYSDATE, v_fecha_nac), 1));

    -- ADD_MONTHS: añadir meses a una fecha
    DBMS_OUTPUT.PUT_LINE('Revisión en 6 meses: ' ||
        TO_CHAR(ADD_MONTHS(SYSDATE, 6), 'DD/MM/YYYY'));

    -- LAST_DAY: último día del mes de la fecha dada
    DBMS_OUTPUT.PUT_LINE('Fin de mes de la cita: ' ||
        TO_CHAR(LAST_DAY(v_fecha_cita), 'DD/MM/YYYY'));

    -- NEXT_DAY: próxima ocurrencia de un día de la semana
    DBMS_OUTPUT.PUT_LINE('Próximo lunes: ' ||
        TO_CHAR(NEXT_DAY(SYSDATE, 'LUNES'), 'DD/MM/YYYY'));

    -- Aritmética de fechas: restar fechas da los días de diferencia
    DBMS_OUTPUT.PUT_LINE('Días hasta la cita: ' ||
        TRUNC(v_fecha_cita - SYSDATE) || ' días');
END;
/
```

#### Funciones de conversión

```sql
DECLARE
    v_num_texto VARCHAR2(20) := '1234.56';
    v_num       NUMBER;
    v_fecha_txt VARCHAR2(20) := '25/12/2024';
    v_fecha     DATE;
BEGIN
    -- TO_NUMBER: texto a número
    v_num := TO_NUMBER(v_num_texto, '9999.99');
    DBMS_OUTPUT.PUT_LINE('Número: ' || v_num);

    -- TO_DATE: texto a fecha (indicando el formato)
    v_fecha := TO_DATE(v_fecha_txt, 'DD/MM/YYYY');
    DBMS_OUTPUT.PUT_LINE('Fecha: ' || TO_CHAR(v_fecha, 'Day DD de Month de YYYY'));

    -- TO_CHAR: número/fecha a texto con formato
    DBMS_OUTPUT.PUT_LINE(TO_CHAR(3500.75, 'L99G999D99'));   -- €3.500,75 (según NLS)
    DBMS_OUTPUT.PUT_LINE(TO_CHAR(SYSDATE, 'YYYY-MM-DD'));

    -- NVL: sustituir NULL por un valor por defecto
    DECLARE v_telefono VARCHAR2(15) := NULL;
    BEGIN
        DBMS_OUTPUT.PUT_LINE('Teléfono: ' || NVL(v_telefono, 'Sin teléfono'));
    END;

    -- NVL2: si no es NULL devuelve A, si es NULL devuelve B
    DECLARE v_email VARCHAR2(100) := 'paciente@hospital.es';
    BEGIN
        DBMS_OUTPUT.PUT_LINE(NVL2(v_email, 'Tiene email', 'Sin email'));
    END;

    -- COALESCE: devuelve el primer valor no nulo de la lista
    DECLARE
        v_tel1 VARCHAR2(15) := NULL;
        v_tel2 VARCHAR2(15) := '666123456';
        v_tel3 VARCHAR2(15) := '91 234 56 78';
    BEGIN
        DBMS_OUTPUT.PUT_LINE('Contacto: ' || COALESCE(v_tel1, v_tel2, v_tel3));
        -- Devuelve '666123456' (primer no nulo)
    END;
END;
/
```

#### Funciones de agregación (en contexto SQL dentro de PL/SQL)

```sql
DECLARE
    v_total_citas   NUMBER;
    v_media_edad    NUMBER(5,1);
    v_max_salario   MEDICOS.salario%TYPE;
    v_min_salario   MEDICOS.salario%TYPE;
BEGIN
    -- COUNT: contar filas
    SELECT COUNT(*) INTO v_total_citas
    FROM CITAS WHERE estado = 'PENDIENTE';

    -- AVG: media
    SELECT ROUND(AVG(TRUNC(MONTHS_BETWEEN(SYSDATE, fecha_nac)/12)), 1)
    INTO v_media_edad
    FROM PACIENTES;

    -- MAX y MIN
    SELECT MAX(salario), MIN(salario)
    INTO v_max_salario, v_min_salario
    FROM MEDICOS;

    DBMS_OUTPUT.PUT_LINE('Citas pendientes: ' || v_total_citas);
    DBMS_OUTPUT.PUT_LINE('Edad media pacientes: ' || v_media_edad || ' años');
    DBMS_OUTPUT.PUT_LINE('Salario máximo médico: ' || v_max_salario || ' €');
    DBMS_OUTPUT.PUT_LINE('Salario mínimo médico: ' || v_min_salario || ' €');
END;
/
```

---

## 10. Entornos de desarrollo, depuración y control de código

### Oracle SQL Developer (recomendado para esta asignatura)

**Oracle SQL Developer** es la herramienta oficial y gratuita de Oracle para trabajar con PL/SQL. Es la que usaremos en clase.

**Características principales:**
- Editor de código con resaltado de sintaxis y autocompletado.
- Ejecutar bloques PL/SQL anónimos, procedimientos y funciones.
- Depurador visual: permite establecer **puntos de ruptura** (*breakpoints*), ejecutar paso a paso y inspeccionar el valor de las variables en cada momento.
- Explorador de objetos: navega por tablas, vistas, procedimientos, funciones, etc.
- Panel DBMS Output integrado para ver la salida de `DBMS_OUTPUT.PUT_LINE`.
- Exportación de resultados a CSV, Excel, HTML.

**Para activar DBMS Output en SQL Developer:**
1. Menú *Ver* → *DBMS Output*.
2. En el panel que aparece, haz clic en el botón verde `+` y selecciona la conexión activa.
3. Ahora los mensajes de `DBMS_OUTPUT.PUT_LINE` aparecerán en este panel al ejecutar el bloque.

### Otros entornos

| Herramienta | Descripción | Cuándo usarla |
|---|---|---|
| **SQL*Plus** | Cliente de línea de comandos de Oracle | Automatización de scripts, entornos de servidor sin interfaz gráfica |
| **Oracle LiveSQL** | IDE web oficial de Oracle (livesql.oracle.com) | Pruebas rápidas sin instalación local |
| **VS Code** + extensión Oracle Dev Tools | Editor moderno con soporte para Oracle | Desarrolladores que prefieren VS Code; integración con Git muy sencilla |
| **Toad for Oracle** | IDE comercial muy completo | Entornos profesionales con necesidades avanzadas de DBA |

### Control de versiones del código PL/SQL

El código PL/SQL (procedimientos, funciones, paquetes, triggers) es código fuente como cualquier otro y debe gestionarse con un sistema de control de versiones. La práctica recomendada es:

1. Guardar cada objeto PL/SQL en un fichero `.sql` independiente.
2. Nombrar los ficheros de forma descriptiva: `pkg_gestion_citas.sql`, `prc_calcular_edad.sql`, etc.
3. Usar **Git** para versionar estos ficheros.
4. En SQL Developer: *Team* → *Git* → permite clonar repositorios, hacer commits, push/pull directamente desde el IDE.

---

## 11. Tipos de scripts y scripts simples

### 11.1 Tipos de scripts en PL/SQL

PL/SQL permite crear distintos tipos de objetos programáticos que se almacenan en la base de datos:

| Tipo | Se almacena en BD | Devuelve valor | Tiene parámetros | Descripción |
|---|---|---|---|---|
| **Script anónimo** | ❌ No | ❌ No | ❌ No | Bloque de código temporal para ejecución inmediata |
| **Procedimiento** | ✅ Sí | ❌ No (usa OUT) | ✅ Sí | Bloque reutilizable sin valor de retorno directo |
| **Función** | ✅ Sí | ✅ Sí | ✅ Sí | Bloque reutilizable que devuelve un valor |
| **Paquete** | ✅ Sí | Variable | ✅ Sí | Agrupa procedimientos y funciones relacionados |
| **Trigger** | ✅ Sí | ❌ No | ❌ No | Se ejecuta automáticamente ante eventos en la BD |

> Los procedimientos, funciones, paquetes y triggers se estudiarán en detalle en próximas unidades.

### 11.2 Scripts simples o anónimos

Un script anónimo es un bloque PL/SQL que **no se almacena** en la base de datos. Se escribe, se ejecuta y desaparece. Son ideales para:

- Realizar tareas de mantenimiento puntuales (actualizar datos masivamente).
- Probar fragmentos de código durante el desarrollo.
- Ejecutar lógica que solo se necesita una vez (migraciones de datos).
- Generar informes ad-hoc.

**Características del script anónimo:**
- No tiene nombre asignado.
- No puede ser llamado desde otro script.
- No acepta parámetros de entrada.
- Se ejecuta en el contexto de sesión actual.

**Ejemplo práctico — Actualización masiva de datos:**

Supongamos que el hospital ha decidido que todos los pacientes mayores de 65 años deben tener un número de historial con el prefijo `GER-` (geriatría). Necesitamos actualizar el campo `num_historial` para todos ellos:

```sql
DECLARE
    v_contFila     PLS_INTEGER := 0;
    v_contError    PLS_INTEGER := 0;
    v_nuevo_hist   VARCHAR2(20);

    CURSOR cur_mayores IS
        SELECT id_paciente, num_historial,
               TRUNC(MONTHS_BETWEEN(SYSDATE, fecha_nac)/12) AS edad
        FROM PACIENTES
        WHERE TRUNC(MONTHS_BETWEEN(SYSDATE, fecha_nac)/12) >= 65
          AND num_historial NOT LIKE 'GER-%';
BEGIN
    DBMS_OUTPUT.PUT_LINE('=== Actualización de historiales geriátricos ===');
    DBMS_OUTPUT.PUT_LINE('Inicio: ' || TO_CHAR(SYSDATE, 'DD/MM/YYYY HH24:MI:SS'));
    DBMS_OUTPUT.PUT_LINE(RPAD('-', 50, '-'));

    FOR rec IN cur_mayores LOOP
        BEGIN
            v_nuevo_hist := 'GER-' || SUBSTR(rec.num_historial, 1, 16);

            UPDATE PACIENTES
            SET num_historial = v_nuevo_hist
            WHERE id_paciente = rec.id_paciente;

            v_contFila := v_contFila + 1;
            DBMS_OUTPUT.PUT_LINE('Actualizado paciente ' || rec.id_paciente
                                || ' (' || rec.edad || ' años): '
                                || rec.num_historial || ' → ' || v_nuevo_hist);
        EXCEPTION
            WHEN OTHERS THEN
                v_contError := v_contError + 1;
                DBMS_OUTPUT.PUT_LINE('⚠️ Error en paciente ' || rec.id_paciente
                                    || ': ' || SQLERRM);
        END;
    END LOOP;

    DBMS_OUTPUT.PUT_LINE(RPAD('-', 50, '-'));
    DBMS_OUTPUT.PUT_LINE('Pacientes actualizados: ' || v_contFila);
    DBMS_OUTPUT.PUT_LINE('Errores encontrados:   ' || v_contError);

    IF v_contError = 0 THEN
        COMMIT;
        DBMS_OUTPUT.PUT_LINE('✅ COMMIT realizado. Cambios guardados.');
    ELSE
        ROLLBACK;
        DBMS_OUTPUT.PUT_LINE('❌ ROLLBACK realizado. Se detectaron errores.');
    END IF;

    DBMS_OUTPUT.PUT_LINE('Fin: ' || TO_CHAR(SYSDATE, 'DD/MM/YYYY HH24:MI:SS'));
END;
/
```

> 💡 Nota el uso de un **cursor explícito** (`CURSOR cur_mayores IS ...`) y un bucle `FOR` sobre él. Los cursores se estudiarán en profundidad en la próxima unidad; por ahora, interpreta el `FOR rec IN cur_mayores LOOP` como "para cada fila que devuelve la consulta, haz lo siguiente".

---

## 12. Casos prácticos resueltos

### Caso 1: Sistema de alertas de stock de medicamentos

**Enunciado:** Escribir un bloque PL/SQL que recorra todos los medicamentos del hospital y genere alertas en tres niveles según el stock disponible: **crítico** (< 5 unidades), **bajo** (5-20 unidades) y **normal** (> 20 unidades).

```sql
DECLARE
    v_total_criticos    PLS_INTEGER := 0;
    v_total_bajos       PLS_INTEGER := 0;
    v_total_normales    PLS_INTEGER := 0;
    v_nivel_alerta      VARCHAR2(10);
BEGIN
    DBMS_OUTPUT.PUT_LINE('╔══════════════════════════════════════════════╗');
    DBMS_OUTPUT.PUT_LINE('║     INFORME DE STOCK DE MEDICAMENTOS         ║');
    DBMS_OUTPUT.PUT_LINE('║     ' || TO_CHAR(SYSDATE, 'DD/MM/YYYY HH24:MI') || '                       ║');
    DBMS_OUTPUT.PUT_LINE('╠══════════════════════════════════════════════╣');

    FOR rec IN (SELECT id_medicamento, nombre, stock, precio_unidad
                FROM MEDICAMENTOS
                ORDER BY stock ASC)
    LOOP
        IF rec.stock < 5 THEN
            v_nivel_alerta   := '🔴 CRITICO';
            v_total_criticos := v_total_criticos + 1;
        ELSIF rec.stock <= 20 THEN
            v_nivel_alerta   := '🟡 BAJO   ';
            v_total_bajos    := v_total_bajos + 1;
        ELSE
            v_nivel_alerta   := '🟢 NORMAL ';
            v_total_normales := v_total_normales + 1;
        END IF;

        DBMS_OUTPUT.PUT_LINE('║ ' || v_nivel_alerta || ' | '
            || RPAD(rec.nombre, 20) || ' | '
            || LPAD(rec.stock, 5) || ' uds ║');
    END LOOP;

    DBMS_OUTPUT.PUT_LINE('╠══════════════════════════════════════════════╣');
    DBMS_OUTPUT.PUT_LINE('║ Críticos: ' || LPAD(v_total_criticos, 3)
                        || '  Bajos: ' || LPAD(v_total_bajos, 3)
                        || '  Normales: ' || LPAD(v_total_normales, 4) || '         ║');
    DBMS_OUTPUT.PUT_LINE('╚══════════════════════════════════════════════╝');
END;
/
```

---

### Caso 2: Cálculo de antigüedad y categoría salarial de médicos

**Enunciado:** Para cada médico, calcular su antigüedad en el hospital y determinar su categoría salarial según la siguiente tabla: Residente (<3 años), Adjunto (3-10 años), Senior (>10 años). Mostrar también el incremento salarial que le correspondería.

```sql
DECLARE
    v_anios_antig   NUMBER;
    v_categoria     VARCHAR2(20);
    v_incremento    NUMBER(5,2);  -- Porcentaje de incremento
    v_nuevo_salario MEDICOS.salario%TYPE;
BEGIN
    DBMS_OUTPUT.PUT_LINE(RPAD('MÉDICO', 30) || RPAD('ANTIGÜEDAD', 12)
                        || RPAD('CATEGORÍA', 12) || RPAD('SALARIO', 12) || 'INCREMENTO');
    DBMS_OUTPUT.PUT_LINE(RPAD('=', 80, '='));

    FOR rec IN (SELECT id_medico, nombre, apellidos, especialidad, salario, fecha_alta
                FROM MEDICOS
                ORDER BY fecha_alta ASC)
    LOOP
        v_anios_antig := TRUNC(MONTHS_BETWEEN(SYSDATE, rec.fecha_alta) / 12);

        CASE
            WHEN v_anios_antig < 3 THEN
                v_categoria  := 'Residente';
                v_incremento := 0;
            WHEN v_anios_antig <= 10 THEN
                v_categoria  := 'Adjunto';
                v_incremento := 5;    -- 5% de incremento
            ELSE
                v_categoria  := 'Senior';
                v_incremento := 12;   -- 12% de incremento
        END CASE;

        v_nuevo_salario := rec.salario * (1 + v_incremento / 100);

        DBMS_OUTPUT.PUT_LINE(
            RPAD(rec.nombre || ' ' || rec.apellidos, 30)
            || RPAD(v_anios_antig || ' años', 12)
            || RPAD(v_categoria, 12)
            || RPAD(TO_CHAR(rec.salario, '9999.99') || ' €', 12)
            || TO_CHAR(v_incremento, '90.00') || '%'
            || CASE WHEN v_incremento > 0
                    THEN ' → ' || TO_CHAR(v_nuevo_salario, '9999.99') || ' €'
                    ELSE ''
               END
        );
    END LOOP;
END;
/
```

---

### Caso 3: Validación completa al registrar una cita

**Enunciado:** Escribir un bloque PL/SQL que registre una nueva cita, realizando las siguientes validaciones previas:
1. Que el paciente exista.
2. Que el médico exista y tenga la especialidad requerida.
3. Que el médico no tenga ya una cita en esa franja horaria (+/- 30 minutos).
4. Que la fecha de la cita sea futura.

```sql
DECLARE
    -- Datos de la nueva cita (en un procedimiento real, vendrían como parámetros)
    v_id_paciente   NUMBER := 15;
    v_id_medico     NUMBER := 3;
    v_fecha_cita    DATE   := TO_DATE('2025-06-20 10:30', 'YYYY-MM-DD HH24:MI');
    v_motivo        VARCHAR2(200) := 'Revisión postoperatoria';
    v_especialidad  VARCHAR2(60) := 'TRAUMA';

    -- Variables auxiliares
    v_existe_pac    NUMBER;
    v_existe_med    NUMBER;
    v_esp_medico    MEDICOS.especialidad%TYPE;
    v_conflicto     NUMBER;
    v_nuevo_id      NUMBER;

    -- Excepciones de negocio
    e_paciente_no_existe    EXCEPTION;
    e_medico_no_existe      EXCEPTION;
    e_especialidad_erronea  EXCEPTION;
    e_fecha_pasada          EXCEPTION;
    e_conflicto_horario     EXCEPTION;
BEGIN
    -- Validación 1: Fecha futura
    IF v_fecha_cita <= SYSDATE THEN
        RAISE e_fecha_pasada;
    END IF;

    -- Validación 2: Paciente existe
    SELECT COUNT(*) INTO v_existe_pac
    FROM PACIENTES WHERE id_paciente = v_id_paciente;

    IF v_existe_pac = 0 THEN
        RAISE e_paciente_no_existe;
    END IF;

    -- Validación 3: Médico existe y tiene la especialidad correcta
    SELECT COUNT(*), MAX(especialidad)
    INTO v_existe_med, v_esp_medico
    FROM MEDICOS
    WHERE id_medico = v_id_medico;

    IF v_existe_med = 0 THEN
        RAISE e_medico_no_existe;
    END IF;

    IF v_esp_medico <> v_especialidad THEN
        RAISE e_especialidad_erronea;
    END IF;

    -- Validación 4: Sin conflicto de horario (ventana de ±30 minutos)
    SELECT COUNT(*) INTO v_conflicto
    FROM CITAS
    WHERE id_medico = v_id_medico
      AND estado = 'PENDIENTE'
      AND ABS(fecha_cita - v_fecha_cita) * 24 * 60 < 30;  -- Diferencia en minutos

    IF v_conflicto > 0 THEN
        RAISE e_conflicto_horario;
    END IF;

    -- Todas las validaciones superadas: insertar la cita
    SELECT NVL(MAX(id_cita), 0) + 1 INTO v_nuevo_id FROM CITAS;  -- Secuencia simplificada

    INSERT INTO CITAS (id_cita, id_paciente, id_medico, fecha_cita, motivo, estado)
    VALUES (v_nuevo_id, v_id_paciente, v_id_medico, v_fecha_cita, v_motivo, 'PENDIENTE');

    COMMIT;
    DBMS_OUTPUT.PUT_LINE('✅ Cita registrada con ID ' || v_nuevo_id
                        || ' para el ' || TO_CHAR(v_fecha_cita, 'DD/MM/YYYY HH24:MI'));

EXCEPTION
    WHEN e_fecha_pasada THEN
        DBMS_OUTPUT.PUT_LINE('❌ Error: La fecha de la cita debe ser futura.');
    WHEN e_paciente_no_existe THEN
        DBMS_OUTPUT.PUT_LINE('❌ Error: No existe el paciente con ID ' || v_id_paciente);
    WHEN e_medico_no_existe THEN
        DBMS_OUTPUT.PUT_LINE('❌ Error: No existe el médico con ID ' || v_id_medico);
    WHEN e_especialidad_erronea THEN
        DBMS_OUTPUT.PUT_LINE('❌ Error: El médico ' || v_id_medico
                            || ' no tiene la especialidad ' || v_especialidad
                            || ' (tiene: ' || v_esp_medico || ')');
    WHEN e_conflicto_horario THEN
        DBMS_OUTPUT.PUT_LINE('❌ Error: El médico ya tiene una cita próxima a las '
                            || TO_CHAR(v_fecha_cita, 'HH24:MI')
                            || '. Seleccione otro horario.');
    WHEN OTHERS THEN
        ROLLBACK;
        DBMS_OUTPUT.PUT_LINE('❌ Error inesperado [' || SQLCODE || ']: ' || SQLERRM);
END;
/
```

---

## 13. Resumen del tema

```
EL LENGUAJE PL/SQL
│
├── DEFINICIÓN Y ESTRUCTURA
│   ├── Lenguaje procedimental propietario de Oracle
│   ├── Estructura de bloque: DECLARE → BEGIN → EXCEPTION → END
│   └── Reglas: insensible a mayúsculas, ; obligatorio, / para ejecutar
│
├── UNIDADES LÉXICAS
│   ├── Delimitadores: ; := || ** < > = ...
│   ├── Identificadores: nombres de variables, funciones, cursores
│   ├── Literales: valores fijos (numéricos, texto, booleanos, fechas)
│   └── Comentarios: -- (línea) y /* ... */ (bloque)
│
├── TIPOS DE DATOS
│   ├── Numérico: NUMBER, PLS_INTEGER, BINARY_FLOAT/DOUBLE
│   ├── Booleano: BOOLEAN (TRUE/FALSE/NULL) — solo en PL/SQL
│   ├── Carácter: CHAR, VARCHAR2, NCHAR, NVARCHAR2
│   ├── Fecha: DATE, TIMESTAMP, INTERVAL
│   ├── LOB: CLOB, BLOB
│   └── Atributos especiales: %TYPE, %ROWTYPE
│
├── VARIABLES Y OPERADORES
│   ├── Declaración: nombre [CONSTANT] tipo [:= valor]
│   ├── SELECT INTO para asignar desde SQL
│   ├── Operadores: aritméticos (+,-,*,/,**), comparación, lógicos, ||
│   └── Precedencia y uso de paréntesis
│
├── ESTRUCTURAS DE CONTROL
│   ├── Condicionales
│   │   ├── IF / ELSIF / ELSE / END IF
│   │   └── CASE simple / CASE buscado / CASE expresión
│   └── Bucles
│       ├── LOOP (infinito + EXIT WHEN)
│       ├── WHILE condición LOOP
│       ├── FOR variable IN [REVERSE] inicio..fin LOOP
│       └── Bucles anidados con etiquetas (<<nombre>>)
│
├── EXCEPCIONES
│   ├── Predefinidas: NO_DATA_FOUND, TOO_MANY_ROWS, ZERO_DIVIDE...
│   ├── Oracle sin nombre: PRAGMA EXCEPTION_INIT(nombre, código)
│   └── Usuario: EXCEPTION + RAISE + WHEN
│
├── LIBRERÍAS
│   ├── Básicas: DBMS_OUTPUT, UTL_FILE, DBMS_SCHEDULER, DBMS_LOCK
│   └── Funciones: texto (UPPER, SUBSTR...), número (ABS, ROUND...),
│                  fecha (SYSDATE, ADD_MONTHS...), conversión (TO_CHAR...)
│
├── ENTORNOS
│   ├── Oracle SQL Developer (recomendado)
│   ├── SQL*Plus (línea de comandos)
│   └── VS Code + extensión Oracle, Oracle LiveSQL
│
└── TIPOS DE SCRIPTS
    ├── Anónimos (esta unidad): sin nombre, sin almacenamiento
    ├── Procedimientos (próxima unidad): almacenados, sin retorno
    ├── Funciones (próxima unidad): almacenadas, con retorno
    ├── Paquetes (próxima unidad): agrupan procedimientos/funciones
    └── Triggers (próxima unidad): se disparan ante eventos en la BD
```

---

## 14. Bibliografía y recursos

### Bibliografía principal

- Connolly, T., y Begg, C. (2005). *Sistemas de Bases de Datos* (5ª ed.). Addison Wesley.
- Feuerstein, S., y Pribyl, B. (2014). *Oracle PL/SQL Programming* (6ª ed.). O'Reilly Media.
- Pérez Marqués, M. (2016). *Administración básica de bases de datos con Oracle 12c SQL*. Alfaomega.

### Documentación oficial de Oracle

- [PL/SQL Language Reference — Oracle 21c](https://docs.oracle.com/en/database/oracle/oracle-database/21/lnpls/)
- [PL/SQL Packages and Types Reference](https://docs.oracle.com/en/database/oracle/oracle-database/21/arpls/)
- [Oracle Built-in Functions](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/Functions.html)
- [Predefined PL/SQL Exceptions](https://docs.oracle.com/en/database/oracle/oracle-database/21/lnpls/plsql-error-handling.html#GUID-8C327B4A-71FA-4CFB-8BC9-4550A23734D6)

### Recursos online para practicar

- [Oracle LiveSQL](https://livesql.oracle.com/) — Entorno PL/SQL en el navegador, sin instalación.
- [Oracle Dev Gym](https://devgym.oracle.com/) — Ejercicios y quizzes de SQL y PL/SQL.
- [Ask TOM (Oracle)](https://asktom.oracle.com/) — Preguntas y respuestas sobre Oracle con respuestas del equipo de Oracle.

## 15. ANEXO: Errores del PDF

> Este anexo documenta los errores de sintaxis detectados en el material original de la unidad. En todos los casos la causa raíz es la misma: el PDF mezcla sintaxis de **MySQL** (u otros dialectos SQL) con la de **Oracle PL/SQL**. Aunque los conceptos que explica son correctos, el código proporcionado **no compilaría ni ejecutaría en Oracle 21c**.
>
> Se muestra en cada caso el código erróneo tal y como aparece en el PDF, la explicación del error y la versión corregida para Oracle PL/SQL.

---

### Error 1 — Declaración de variables con `DECLARE` por variable y asignación con `SET` (Caso práctico 1, pág. 15-16)

#### ¿Qué muestra el PDF?

```sql
DECLARE varContador INT;
SET varContador = 10;

DECLARE varTextosVARCHAR(100);
SET varTextos = "Leyenda";

DECLARE varFecha DATE;
SET varFecha = '1999-01-01';
```

#### ¿Por qué es incorrecto en Oracle PL/SQL?

Se han mezclado tres características propias de **MySQL** que no existen en Oracle PL/SQL:

| Elemento incorrecto | Comportamiento en MySQL | Comportamiento en Oracle PL/SQL |
|---|---|---|
| `DECLARE var tipo;` repetido | Declara una variable suelta fuera de bloque | `DECLARE` es la **cabecera de la sección** de declaraciones del bloque; no se repite por variable |
| `SET variable = valor` | Asigna un valor a una variable | En PL/SQL la asignación es `:=`; la palabra `SET` **no existe** como instrucción de asignación |
| `"Leyenda"` (comillas dobles) | Literal de cadena | En Oracle, las comillas dobles identifican **objetos** (`"nombre_tabla"`); los literales de texto usan **comillas simples** |

Además, `VARCHAR` sin el `2` (es decir, sin ser `VARCHAR2`) está técnicamente permitido en Oracle como alias, pero su uso está desaconsejado: Oracle reserva `VARCHAR` para un posible cambio de semántica futuro, y la convención es usar siempre `VARCHAR2`.

#### Código correcto en Oracle PL/SQL

```sql
DECLARE
    varContador  PLS_INTEGER  := 10;
    varTextos    VARCHAR2(100) := 'Leyenda';
    varFecha     DATE          := DATE '1999-01-01';
BEGIN
    DBMS_OUTPUT.PUT_LINE(
        varContador || ' - ' || varTextos || ' - ' || TO_CHAR(varFecha, 'YYYY-MM-DD')
    );
END;
/
-- Salida: 10 - Leyenda - 1999-01-01
```

> 💡 **Nota sobre literales de fecha:** `DATE '1999-01-01'` es la notación ANSI estándar, válida en Oracle. La alternativa Oracle clásica es `TO_DATE('1999-01-01', 'YYYY-MM-DD')`, que es más explícita y recomendada cuando el formato puede variar según la configuración NLS del servidor.

---

### Error 2 — Asignación dentro de `CASE` con `SET variable = valor` y comillas dobles (Caso práctico 2, pág. 16)

#### ¿Qué muestra el PDF?

```sql
CASE varPais
    WHEN "España" THEN
    SET varCod = "ESP";

    WHEN "Francia" THEN
    SET varCod = "FRA";

    WHEN "Inglaterra" THEN
    SET varcod = "ING";

    ELSE
    SET varcod = "ERR";
END CASE;
```

#### ¿Por qué es incorrecto en Oracle PL/SQL?

Son tres errores combinados, todos de sintaxis MySQL:

| Elemento incorrecto | MySQL | Oracle PL/SQL |
|---|---|---|
| `SET varCod = "ESP"` dentro de CASE | Sintaxis válida | `SET` no existe; la asignación es `varCod := 'ESP';` |
| `"España"`, `"ESP"` (comillas dobles) | Literal de cadena | Las comillas dobles son para identificadores; los literales van con comillas simples |
| `varcod` (inconsistencia de mayúsculas) | MySQL es insensible | PL/SQL también es insensible al caso, pero la inconsistencia es mala práctica |

#### Código correcto en Oracle PL/SQL

```sql
DECLARE
    varPais  VARCHAR2(20);
    varCod   VARCHAR2(3);
BEGIN
    SELECT DISTINCT p.nombre
    INTO varPais
    FROM paises p
    INNER JOIN equipos e ON p.cod_pais = e.cod_pais
    WHERE e.cod_equipo = 5;

    CASE varPais
        WHEN 'España'     THEN varCod := 'ESP';
        WHEN 'Francia'    THEN varCod := 'FRA';
        WHEN 'Inglaterra' THEN varCod := 'ING';
        ELSE                   varCod := 'ERR';
    END CASE;

    DBMS_OUTPUT.PUT_LINE('Código de país: ' || varCod);
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('No se encontró el país para el equipo indicado.');
END;
/
```

> 💡 Observa también que el `SELECT` que carga `varPais` se ha movido **dentro del bloque `BEGIN`**, usando la sintaxis Oracle `SELECT ... INTO ...`. En el PDF aparecía como `SET varPais = (SELECT ...)`, que es sintaxis MySQL (ver Error 3 a continuación).

---

### Error 3 — Inicialización de variable con `SET variable = (SELECT ...)` (Caso práctico 2, pág. 16)

#### ¿Qué muestra el PDF?

```sql
DECLARE varPais VARCHAR(20);
DECLARE varCod  VARCHAR(3);
SET varPais = (SELECT DISTINCT País.Nombre
               FROM País
               INNER JOIN Equipo
                   ON País.CodPais = Equipo.CodPais
               WHERE Equipo.CodEquipo = 5);
```

#### ¿Por qué es incorrecto en Oracle PL/SQL?

| Elemento incorrecto | MySQL | Oracle PL/SQL |
|---|---|---|
| `SET var = (SELECT ...)` | Asigna el resultado de una subconsulta a una variable | No existe. En PL/SQL se usa `SELECT ... INTO var FROM ...` dentro del `BEGIN` |
| `DECLARE var tipo;` repetido | Cada variable en su propio `DECLARE` | `DECLARE` es la sección; todas las variables se declaran en ella sin repetir la palabra |
| `VARCHAR(20)` | Tipo de cadena estándar MySQL | En Oracle debe ser `VARCHAR2(20)` |

#### Código correcto en Oracle PL/SQL

```sql
DECLARE
    varPais  VARCHAR2(20);   -- VARCHAR2, no VARCHAR
    varCod   VARCHAR2(3);
BEGIN
    -- SELECT ... INTO es la forma correcta en Oracle PL/SQL
    SELECT DISTINCT p.nombre
    INTO varPais
    FROM paises p
    INNER JOIN equipos e ON p.cod_pais = e.cod_pais
    WHERE e.cod_equipo = 5;

    -- A partir de aquí, varPais tiene el valor recuperado
    DBMS_OUTPUT.PUT_LINE('País del equipo: ' || varPais);
EXCEPTION
    WHEN NO_DATA_FOUND  THEN DBMS_OUTPUT.PUT_LINE('Equipo no encontrado.');
    WHEN TOO_MANY_ROWS  THEN DBMS_OUTPUT.PUT_LINE('La consulta devolvió más de una fila.');
END;
/
```

> ⚠️ **Recuerda:** `SELECT INTO` en Oracle lanza `NO_DATA_FOUND` si no hay resultado y `TOO_MANY_ROWS` si hay más de uno. Siempre hay que contemplar estas excepciones (el PDF no lo menciona en este contexto).

---

### Error 4 — Sintaxis del bucle `WHILE` con `DO` y `END WHILE` (pág. 10)

#### ¿Qué muestra el PDF?

```sql
WHILE <condición> DO
    <sentencias_bucle>
END WHILE;
```

#### ¿Por qué es incorrecto en Oracle PL/SQL?

Las palabras clave `DO` y `END WHILE` pertenecen a la sintaxis de **MySQL**. Oracle PL/SQL usa `LOOP` para abrir el cuerpo del bucle y `END LOOP` para cerrarlo, independientemente del tipo de bucle.

| Elemento | MySQL | Oracle PL/SQL |
|---|---|---|
| Apertura del cuerpo | `DO` | `LOOP` |
| Cierre del bucle | `END WHILE` | `END LOOP` |

#### Código correcto en Oracle PL/SQL

```sql
-- Sintaxis correcta del WHILE en Oracle PL/SQL
WHILE <condición> LOOP
    <sentencias_bucle>
END LOOP;
```

**Ejemplo aplicado** — Reducir el stock de un medicamento en lotes de 10 mientras haya suficiente:

```sql
DECLARE
    v_stock_actual  NUMBER := 85;
    v_lote          CONSTANT NUMBER := 10;
    v_num_envios    PLS_INTEGER := 0;
BEGIN
    WHILE v_stock_actual >= v_lote LOOP
        v_stock_actual := v_stock_actual - v_lote;
        v_num_envios   := v_num_envios + 1;
        DBMS_OUTPUT.PUT_LINE('Envío #' || v_num_envios
                            || ' procesado. Stock restante: ' || v_stock_actual);
    END LOOP;

    DBMS_OUTPUT.PUT_LINE('Proceso completado. Envíos: ' || v_num_envios
                        || '. Stock final: ' || v_stock_actual);
END;
/
```

---

### Error 5 — El bucle `REPEAT...UNTIL...END REPEAT` no existe en Oracle PL/SQL (pág. 10)

#### ¿Qué muestra el PDF?

```sql
REPEAT
    <sentencias_bucle>
UNTIL <condición_salida>
END REPEAT;
```

El PDF lo presenta como una construcción válida de PL/SQL.

#### ¿Por qué es incorrecto en Oracle PL/SQL?

`REPEAT...UNTIL...END REPEAT` es una estructura de **MySQL** (y de otros lenguajes como Pascal o Ada). **Oracle PL/SQL no tiene esta construcción**. Ejecutar ese código en Oracle 21c producirá un error de compilación:

```
PLS-00103: Encountered the symbol "REPEAT" when expecting one of the following: ...
```

La semántica de `REPEAT...UNTIL` (ejecutar el cuerpo **al menos una vez** y salir cuando la condición sea verdadera) se replica en Oracle PL/SQL usando un `LOOP` básico con `EXIT WHEN` **al final** del cuerpo:

| Característica | MySQL `REPEAT...UNTIL` | Oracle `LOOP...EXIT WHEN` al final |
|---|---|---|
| ¿Ejecuta el cuerpo al menos una vez? | ✅ Sí | ✅ Sí (la condición se evalúa después) |
| Condición de salida | Cuando la condición es **verdadera** | Cuando la condición del `EXIT WHEN` es **verdadera** |
| Disponible en Oracle 21c | ❌ No | ✅ Sí |

#### Código correcto en Oracle PL/SQL

```sql
-- Equivalente Oracle al REPEAT...UNTIL de MySQL
LOOP
    <sentencias_bucle>
    EXIT WHEN <condición_salida>;
END LOOP;
```

**Ejemplo aplicado** — Solicitar confirmación de un proceso crítico (simula reintento hasta éxito):

```sql
DECLARE
    v_intentos      PLS_INTEGER := 0;
    v_max_intentos  CONSTANT PLS_INTEGER := 3;
    v_exito         BOOLEAN := FALSE;
    v_cod_error     NUMBER;
BEGIN
    LOOP
        v_intentos  := v_intentos + 1;
        v_cod_error := TRUNC(DBMS_RANDOM.VALUE(0, 3));  -- Simula éxito/fallo aleatorio

        IF v_cod_error = 0 THEN
            v_exito := TRUE;
            DBMS_OUTPUT.PUT_LINE('✅ Intento #' || v_intentos || ': operación exitosa.');
        ELSE
            DBMS_OUTPUT.PUT_LINE('⚠️ Intento #' || v_intentos || ': fallo (código ' || v_cod_error || '). Reintentando...');
        END IF;

        -- La condición de salida va AL FINAL, garantizando al menos una ejecución
        EXIT WHEN v_exito OR v_intentos >= v_max_intentos;
    END LOOP;

    IF NOT v_exito THEN
        DBMS_OUTPUT.PUT_LINE('❌ Se agotaron los ' || v_max_intentos || ' intentos sin éxito.');
    END IF;
END;
/
```

---

### Error 6 — Vista con `LIMIT` en lugar de `FETCH FIRST n ROWS ONLY` (pág. 15)

#### ¿Qué muestra el PDF?

```sql
CREATE VIEW ComentariosRecientes AS
SELECT id_comentario, id_publicacion,
       autor_comentario, fecha_comentario, contenido_comentario
FROM comentarios_wp
ORDER BY fecha_comentario DESC
LIMIT 20;

GRANT SELECT ON ComentariosRecientes TO usuarioWeb;
```

#### ¿Por qué es incorrecto en Oracle PL/SQL?

`LIMIT n` es la cláusula de **MySQL** (y PostgreSQL) para restringir el número de filas devueltas. En **Oracle no existe `LIMIT`**. Ejecutar ese `CREATE VIEW` en Oracle 21c producirá:

```
ORA-00933: SQL command not properly ended
```

Oracle ofrece **tres formas** de limitar filas, dependiendo de la versión:

| Método | Versión Oracle | Observaciones |
|---|---|---|
| `FETCH FIRST n ROWS ONLY` | Oracle 12c+ ✅ | Sintaxis SQL estándar (ANSI). **Preferida** |
| `FETCH FIRST n ROWS WITH TIES` | Oracle 12c+ ✅ | Incluye filas empatadas en el último valor |
| `WHERE ROWNUM <= n` | Todas ⚠️ | Técnica clásica; interactúa con `ORDER BY` de forma confusa si no se anida |

> ⚠️ **Trampa clásica con `ROWNUM`:** `WHERE ROWNUM <= 20` se aplica **antes** del `ORDER BY`. Si los escribes en el mismo nivel, obtienes 20 filas aleatorias ordenadas, no las 20 primeras del orden deseado. Siempre hay que anidar la consulta.

#### Código correcto en Oracle PL/SQL / Oracle 21c

```sql
-- ✅ Forma recomendada en Oracle 12c+ (incluido Oracle 21c)
CREATE OR REPLACE VIEW comentarios_recientes AS
SELECT id_comentario, id_publicacion,
       autor_comentario, fecha_comentario, contenido_comentario
FROM comentarios_wp
ORDER BY fecha_comentario DESC
FETCH FIRST 20 ROWS ONLY;

GRANT SELECT ON comentarios_recientes TO usuario_web;
```

```sql
-- ✅ Forma clásica con ROWNUM (compatible con todas las versiones Oracle)
-- Requiere anidar la consulta para que ORDER BY se aplique antes del filtro
CREATE OR REPLACE VIEW comentarios_recientes AS
SELECT *
FROM (
    SELECT id_comentario, id_publicacion,
           autor_comentario, fecha_comentario, contenido_comentario
    FROM comentarios_wp
    ORDER BY fecha_comentario DESC
)
WHERE ROWNUM <= 20;
```

```sql
-- ❌ NO válido en Oracle — solo MySQL/PostgreSQL
CREATE VIEW comentarios_recientes AS
SELECT ...
FROM comentarios_wp
ORDER BY fecha_comentario DESC
LIMIT 20;  -- ORA-00933: SQL command not properly ended
```

---

### Tabla resumen de todos los errores

| # | Ubicación en el PDF | Error detectado | Dialecto origen | Corrección en Oracle 21c |
|---|---|---|---|---|
| 1 | Caso práctico 1 (pág. 15-16) | `DECLARE var tipo;` repetido + `SET var = valor` | MySQL | Sección `DECLARE` única + operador `:=` |
| 2 | Caso práctico 2 (pág. 16) | `SET varCod = "ESP"` dentro de `CASE` + comillas dobles | MySQL | `varCod := 'ESP';` con comillas simples |
| 3 | Caso práctico 2 (pág. 16) | `SET varPais = (SELECT ...)` para inicializar con consulta | MySQL | `SELECT ... INTO varPais FROM ...` en `BEGIN` |
| 4 | Apartado 7 — Bucles (pág. 10) | Sintaxis `WHILE ... DO ... END WHILE` | MySQL | `WHILE ... LOOP ... END LOOP` |
| 5 | Apartado 7 — Bucles (pág. 10) | Estructura `REPEAT ... UNTIL ... END REPEAT` | MySQL | `LOOP ... EXIT WHEN ... END LOOP` |
| 6 | Apartado 11.2 — Scripts (pág. 15) | Cláusula `LIMIT n` en una vista | MySQL | `FETCH FIRST n ROWS ONLY` (Oracle 12c+) |

> 📌 **Consejo para el alumno:** cuando busques ejemplos de PL/SQL en internet, presta atención al SGBD al que hace referencia el código. MySQL, PostgreSQL, SQL Server y Oracle comparten muchos conceptos pero tienen sintaxis propietarias que **no son intercambiables**. Una buena señal de alerta: si ves `SET variable = valor`, `LIMIT`, `AUTO_INCREMENT` o `IF ... THEN ... END IF;` sin el `END IF`, probablemente es MySQL, no Oracle.
---

*Material elaborado para el módulo de Bases de Datos del CFGS DAM — Oracle 21c*

