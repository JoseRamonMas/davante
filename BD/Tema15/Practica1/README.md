# Unidad 15: Práctica 1 — El lenguaje PL/SQL

## 1. Contexto del esquema de trabajo

Para esta práctica seguimos trabajando con el universo **TechFix Solutions**, pero ahora toda la lógica reside en bloques **PL/SQL anónimos** en lugar de en sentencias SQL puras. El esquema incluye las tablas habituales de empleados, componentes y pedidos, más una nueva tabla `U15_LOG_ACTIVIDAD` que usaremos como destino de escritura desde nuestros scripts (práctica habitual en sistemas reales para auditar operaciones).

| Tabla | Descripción |
|---|---|
| `U15_EMPLEADOS` | Personal de TechFix: técnicos, vendedores y gerentes |
| `U15_COMPONENTES` | Catálogo de componentes informáticos con stock |
| `U15_PEDIDOS` | Pedidos realizados por clientes |
| `U15_LOG_ACTIVIDAD` | Registro de eventos generados por nuestros scripts PL/SQL |

> **Nota:** Ejecuta el script de inicialización antes de cada bloque de ejercicios para partir de un estado limpio y predecible.

---

## 2. Script de inicialización

```sql
/* ==========================================================
   ESQUEMA: TECHFIX SOLUTIONS — Unidad 15 PL/SQL
   Tablas: U15_EMPLEADOS, U15_COMPONENTES,
           U15_PEDIDOS,   U15_LOG_ACTIVIDAD
   ========================================================== */

-- Borrado de tablas y secuencia (en orden para respetar FKs)
DROP SEQUENCE U15_SEQ_LOG;
DROP TABLE U15_LOG_ACTIVIDAD CASCADE CONSTRAINTS;
DROP TABLE U15_PEDIDOS       CASCADE CONSTRAINTS;
DROP TABLE U15_COMPONENTES   CASCADE CONSTRAINTS;
DROP TABLE U15_EMPLEADOS     CASCADE CONSTRAINTS;

-- 1. EMPLEADOS
CREATE TABLE U15_EMPLEADOS (
    id_emp         NUMBER(4)    PRIMARY KEY,
    nombre         VARCHAR2(30) NOT NULL,
    apellido       VARCHAR2(30) NOT NULL,
    puesto         VARCHAR2(20) CHECK (puesto IN ('TECNICO','VENDEDOR','GERENTE')),
    salario        NUMBER(7,2)  NOT NULL,
    comision       NUMBER(7,2),          -- Puede ser NULL
    fecha_contrato DATE         NOT NULL
);

-- 2. COMPONENTES
CREATE TABLE U15_COMPONENTES (
    cod_comp   VARCHAR2(10)  PRIMARY KEY,
    nombre     VARCHAR2(50)  NOT NULL,
    categoria  VARCHAR2(20)  CHECK (categoria IN ('GPU','CPU','RAM','ALMACENAMIENTO','PERIFERICO')),
    precio     NUMBER(8,2)   NOT NULL,
    stock      NUMBER(4)     DEFAULT 0 NOT NULL
);

-- 3. PEDIDOS
CREATE TABLE U15_PEDIDOS (
    id_pedido    NUMBER(5)    PRIMARY KEY,
    id_emp       NUMBER(4)    REFERENCES U15_EMPLEADOS(id_emp),
    cod_comp     VARCHAR2(10) REFERENCES U15_COMPONENTES(cod_comp),
    cantidad     NUMBER(3)    NOT NULL,
    fecha_pedido DATE         DEFAULT SYSDATE,
    estado       VARCHAR2(15) CHECK (estado IN ('PENDIENTE','ENVIADO','ENTREGADO','CANCELADO'))
);

-- 4. LOG DE ACTIVIDAD (destino de escritura desde PL/SQL)
CREATE TABLE U15_LOG_ACTIVIDAD (
    id_log     NUMBER(6)     PRIMARY KEY,
    fecha_hora TIMESTAMP     DEFAULT SYSTIMESTAMP NOT NULL,
    bloque     VARCHAR2(50),
    mensaje    VARCHAR2(500) NOT NULL
);

-- Secuencia para la PK del log
CREATE SEQUENCE U15_SEQ_LOG START WITH 1 INCREMENT BY 1 NOCACHE;

-- ============================================================
-- INSERCIONES
-- ============================================================

INSERT INTO U15_EMPLEADOS VALUES (101,'Carlos', 'Aranda', 'GERENTE', 3500.00,   NULL, TO_DATE('15/01/2010','DD/MM/YYYY'));
INSERT INTO U15_EMPLEADOS VALUES (102,'Elena',  'Vázquez','TECNICO', 1800.00, 200.00, TO_DATE('20/03/2015','DD/MM/YYYY'));
INSERT INTO U15_EMPLEADOS VALUES (103,'Marcos', 'Ruiz',   'VENDEDOR',1400.00, 600.00, TO_DATE('10/07/2018','DD/MM/YYYY'));
INSERT INTO U15_EMPLEADOS VALUES (104,'Lucía',  'Gómez',  'VENDEDOR',1450.00,   NULL, TO_DATE('05/11/2021','DD/MM/YYYY'));
INSERT INTO U15_EMPLEADOS VALUES (105,'David',  'Sanz',   'TECNICO', 1750.00, 150.00, TO_DATE('12/02/2019','DD/MM/YYYY'));
INSERT INTO U15_EMPLEADOS VALUES (106,'Sofía',  'Méndez', 'VENDEDOR',1300.00, 300.00, SYSDATE - 50);
INSERT INTO U15_EMPLEADOS VALUES (107,'Javier', 'Trillo', 'TECNICO', 1600.00,   NULL, SYSDATE - 10);

INSERT INTO U15_COMPONENTES VALUES ('CPU-INT-09','Intel Core i9 12900K',  'CPU',           650.50, 10);
INSERT INTO U15_COMPONENTES VALUES ('CPU-AMD-07','AMD Ryzen 7 5800X',     'CPU',           420.00,  5);
INSERT INTO U15_COMPONENTES VALUES ('GPU-NV-306','Nvidia RTX 3060',       'GPU',           380.99,  2);
INSERT INTO U15_COMPONENTES VALUES ('GPU-NV-409','Nvidia RTX 4090',       'GPU',          1850.00,  0);
INSERT INTO U15_COMPONENTES VALUES ('RAM-COR-16','Corsair Vengeance 16GB','RAM',            85.50, 50);
INSERT INTO U15_COMPONENTES VALUES ('RAM-KIN-32','Kingston Fury 32GB',    'RAM',           139.99, 20);
INSERT INTO U15_COMPONENTES VALUES ('SSD-SAM-1T','Samsung 980 Pro 1TB',   'ALMACENAMIENTO',120.00, 25);
INSERT INTO U15_COMPONENTES VALUES ('MOU-LOG-MX','Logitech MX Master 3',  'PERIFERICO',    99.99, 15);
INSERT INTO U15_COMPONENTES VALUES ('KEY-RAZ-BL','Razer BlackWidow',      'PERIFERICO',   140.50,  8);

INSERT INTO U15_PEDIDOS VALUES (2001, 103, 'GPU-NV-306', 1, TO_DATE('15/01/2024','DD/MM/YYYY'), 'ENTREGADO');
INSERT INTO U15_PEDIDOS VALUES (2002, 103, 'CPU-INT-09', 1, TO_DATE('20/02/2024','DD/MM/YYYY'), 'ENTREGADO');
INSERT INTO U15_PEDIDOS VALUES (2003, 104, 'RAM-COR-16', 2, TO_DATE('05/03/2024','DD/MM/YYYY'), 'ENVIADO');
INSERT INTO U15_PEDIDOS VALUES (2004, 105, 'SSD-SAM-1T', 3, TO_DATE('10/04/2024','DD/MM/YYYY'), 'ENVIADO');
INSERT INTO U15_PEDIDOS VALUES (2005, 103, 'MOU-LOG-MX', 1, TO_DATE('02/05/2024','DD/MM/YYYY'), 'PENDIENTE');
INSERT INTO U15_PEDIDOS VALUES (2006, 104, 'GPU-NV-409', 1, TO_DATE('18/06/2024','DD/MM/YYYY'), 'CANCELADO');
INSERT INTO U15_PEDIDOS VALUES (2007, 106, 'KEY-RAZ-BL', 2, SYSDATE - 5,                       'PENDIENTE');

COMMIT;
```

---

## 3. Bloque I — Variables, constantes y tipos de datos

*Recuerda ejecutar el script de inicialización antes de comenzar este bloque.*

> En todos los ejercicios de este bloque, muestra los resultados usando `DBMS_OUTPUT.PUT_LINE`. Para verlos en SQL Developer activa la pestaña *DBMS Output* (menú *Ver*) y pulsa el botón `+` para vincularla a tu conexión.

1. Escribe un bloque PL/SQL que declare **al menos una variable de cada uno de estos tipos**: `NUMBER`, `VARCHAR2`, `DATE`, `BOOLEAN` y `PLS_INTEGER`. Asígnales valores directamente en la sección `DECLARE` (sin `SELECT INTO`) e imprime cada una por pantalla con una etiqueta descriptiva. Para el `BOOLEAN`, usa `CASE` como expresión para convertirlo a texto `'TRUE'` / `'FALSE'` / `'NULL'` antes de imprimirlo, ya que `DBMS_OUTPUT` no acepta `BOOLEAN` directamente.

2. Usa `SELECT INTO` para cargar en variables el `nombre`, `apellido`, `salario` y `comision` del empleado con `id_emp = 102`. Imprime en una sola línea el nombre completo y sus ingresos totales (salario + comisión), usando `NVL` para tratar la comisión nula como 0. Formato de salida esperado:
   ```
   Elena Vázquez — Ingresos totales: 2000 €
   ```

3. Declara una variable de tipo `U15_COMPONENTES%ROWTYPE` y cárgala con la fila completa del componente `'SSD-SAM-1T'`. A continuación imprime su nombre, precio y el precio con IVA (21%), redondeado a 2 decimales. Usa una **constante** para el porcentaje de IVA en lugar de escribir `0.21` directamente.

4. Declara variables usando **`%TYPE`** ancladas a las columnas `U15_EMPLEADOS.salario` y `U15_EMPLEADOS.puesto`. Usa `SELECT INTO` con funciones de agregación para cargar en ellas: el **salario máximo** y el **puesto** al que pertenece ese salario máximo. Imprime el resultado indicando también cuántos empleados hay en total (tercera variable con `COUNT(*)`).

5. La empresa aplica estas bonificaciones anuales sobre el salario bruto: técnicos 8%, vendedores 5%, gerentes 3%. Declara las tres como **constantes**. Carga el salario y puesto del empleado `id_emp = 105` con `SELECT INTO` y calcula e imprime su bonificación anual en euros, indicando el porcentaje aplicado.

6. Escribe un bloque que cargue mediante `SELECT INTO` el nombre y la `fecha_contrato` del empleado con `id_emp = 101`. Imprime una ficha con las siguientes líneas (usa `TO_CHAR` para todos los formatos de fecha):
   - Nombre completo en mayúsculas (`UPPER`).
   - Fecha de contrato con formato `'DD "de" Month "de" YYYY'`.
   - Años completos en la empresa (pista: `TRUNC(MONTHS_BETWEEN(...) / 12)`).
   - Fecha estimada de jubilación (asumiendo 67 años de edad y que la fecha de nacimiento es ficticia: 20 años antes de la fecha de contrato; usa `ADD_MONTHS`).

7. *(Solo escritura — no es necesario ejecutar este ejercicio.)* Escribe el bloque `DECLARE...BEGIN...END` que declararía las variables necesarias para almacenar todos los campos de la tabla `U15_PEDIDOS` de un único pedido. Usa `%TYPE` para cada campo individual. Añade un comentario explicando en 2-3 líneas qué ventaja tiene usar `%TYPE` frente a escribir el tipo explícitamente (p.ej. `NUMBER(5)`).

---

## 4. Bloque II — Estructuras condicionales: IF y CASE

*Recuerda ejecutar el script de inicialización antes de comenzar este bloque.*

1. Carga el `stock` del componente `'GPU-NV-409'` en una variable. Usando un `IF` simple, imprime `'⚠️ SIN STOCK — Componente no disponible'` si el stock es 0, o `'✅ Disponible: X unidades'` en caso contrario.

2. Carga el `salario` del empleado `id_emp = 104` y el salario medio de todos los empleados (`AVG`), ambos con `SELECT INTO`. Usando `IF / ELSE`, imprime si el empleado cobra **por encima**, **exactamente igual** o **por debajo** de la media, indicando también la diferencia en euros. Redondea la media a 2 decimales.

3. Escribe un bloque que cargue el `salario` de cada uno de los siguientes empleados —`id_emp` 101, 103 y 107— uno a uno (tres bloques anidados o tres variables separadas, como prefieras) y clasifique su salario usando `IF / ELSIF / ELSE` en cuatro niveles: `'BÁSICO'` (< 1.400 €), `'ESTÁNDAR'` (1.400 – 1.799 €), `'SENIOR'` (1.800 – 2.999 €) y `'DIRECTIVO'` (≥ 3.000 €). Imprime nombre y nivel para cada uno.

4. Carga la `categoria` del componente `'KEY-RAZ-BL'` con `SELECT INTO`. Usando una sentencia `CASE` **simple**, imprime una descripción extendida de la categoría:
   - `'GPU'` → `'Tarjeta gráfica — Alto rendimiento gráfico'`
   - `'CPU'` → `'Procesador — Unidad central de proceso'`
   - `'RAM'` → `'Memoria RAM — Almacenamiento volátil'`
   - `'ALMACENAMIENTO'` → `'Almacenamiento — SSD o HDD'`
   - `'PERIFERICO'` → `'Periférico — Dispositivo de entrada/salida'`

5. Carga el `precio` del componente `'CPU-INT-09'`. Usando un `CASE` **buscado**, determina e imprime el descuento aplicable según la siguiente política de la empresa:
   - Precio ≥ 1.500 €: descuento del 15 %
   - Precio entre 500 € y 1.499,99 €: descuento del 10 %
   - Precio entre 100 € y 499,99 €: descuento del 5 %
   - Precio < 100 €: sin descuento
   
   Imprime el precio original, el porcentaje de descuento y el precio final tras aplicarlo.

6. Carga el `estado` del pedido `id_pedido = 2006`. Usando `CASE` como **expresión** (no como sentencia de control), asigna a una variable `VARCHAR2` un icono y etiqueta según el estado: `'PENDIENTE'` → `'⏳ Pendiente'`, `'ENVIADO'` → `'🚚 En camino'`, `'ENTREGADO'` → `'✅ Entregado'`, `'CANCELADO'` → `'❌ Cancelado'`. Imprime el resultado.

7. Escribe un bloque que cargue el `puesto` y la `comision` (con `NVL`) de los empleados `id_emp = 102`, `104` y `106`. Para cada uno, usando **`IF` con condiciones compuestas** (`AND` / `OR`), determina si le corresponde un bono extraordinario de fin de año:
   - Técnico con comisión > 100 €: bono de 300 €
   - Técnico sin comisión (o ≤ 100 €): bono de 150 €
   - Vendedor con comisión > 0: bono de 200 €
   - Vendedor sin comisión: bono de 100 €
   - Gerente: sin bono (0 €)
   
   Imprime nombre, puesto, comisión y bono asignado.

8. Carga el `nombre` y `apellido` del empleado `id_emp = 103` con `SELECT INTO`. Usando la función `DECODE`, imprime su puesto traducido al inglés (`'TECNICO'` → `'Technician'`, `'VENDEDOR'` → `'Salesman'`, `'GERENTE'` → `'Manager'`, cualquier otro valor → `'Unknown'`).

---

## 5. Bloque III — Estructuras de control: Bucles

*Recuerda ejecutar el script de inicialización antes de comenzar este bloque.*

1. Usando un bucle `LOOP` con `EXIT WHEN`, imprime la **tabla de multiplicar del 7** (del 7×1 al 7×10). Cada línea debe tener el formato `7 x 3 = 21`.

2. Usando un bucle `WHILE`, simula el **vaciado progresivo del stock** del componente `'RAM-COR-16'`: empieza cargando su stock actual con `SELECT INTO` y en cada iteración descuenta un lote de 8 unidades. El bucle termina cuando el stock restante sea menor que 8 (es decir, ya no cabe un lote completo). Imprime en cada iteración el número de lote y el stock tras el descuento. Al salir del bucle, imprime el stock final no vendido.

3. Usando un bucle `FOR` numérico, imprime la **previsión de los próximos 7 días** mostrando para cada uno: la fecha (formato `DD/MM/YYYY DY`) y el número de pedidos en estado `'PENDIENTE'` que vencen ese día. Usa `SELECT COUNT(*) INTO` dentro del bucle filtrando por `TRUNC(fecha_pedido) = TRUNC(SYSDATE) + i`. Si el conteo es 0, imprime `'— Sin pedidos pendientes'`.

4. Usando un bucle `FOR` con `REVERSE`, genera un **contador de cuenta atrás** del 5 al 1 y después imprime `'🚀 Sistema iniciado'`. Aunque el resultado sea sencillo, el objetivo es practicar la sintaxis `IN REVERSE`.

5. Usando **bucles anidados**, genera e imprime una **matriz de disponibilidad ficticia** para los 3 técnicos de la empresa (IDs 102, 105, 107) durante los próximos 3 días. El bucle externo itera sobre los días (1, 2, 3) y el bucle interno sobre los técnicos. Para cada combinación imprime una línea del tipo:
   ```
   Día +1 | Técnico 102 | DISPONIBLE
   ```
   Usa `MOD(id_tecnico + dia, 3)` para simular si el técnico está disponible (`= 0`) o no (`!= 0`), de forma que el resultado no sea siempre el mismo. Etiqueta la columna de disponibilidad con `'DISPONIBLE'` o `'OCUPADO'` usando `CASE` como expresión.

6. Escribe un bucle `LOOP` que acumule el **valor total del inventario** (suma de `precio * stock`) componente a componente, usando un cursor `FOR` implícito (`FOR rec IN (SELECT ...) LOOP`). Para cada componente cuyo valor individual supere los 2.000 €, imprime una línea de alerta con su nombre y valor. Al salir del bucle, imprime el valor total de todo el inventario. *(Pista: `FOR rec IN (SELECT cod_comp, nombre, precio, stock FROM U15_COMPONENTES) LOOP` — no es necesario haber estudiado cursores explícitos para usar esta forma.)*

7. *(Solo escritura — no ejecutar.)* Explica mediante comentarios en el código por qué el siguiente bloque produce un **bucle infinito** y reescríbelo correctamente usando `EXIT WHEN` para que se detenga cuando `v_contador` llegue a 5:
   ```sql
   DECLARE
       v_contador NUMBER := 1;
   BEGIN
       LOOP
           DBMS_OUTPUT.PUT_LINE('Iteración: ' || v_contador);
       END LOOP;
   END;
   /
   ```

---

## 6. Bloque IV — Manejo de errores: Excepciones

*Recuerda ejecutar el script de inicialización antes de comenzar este bloque.*

> En este bloque es especialmente importante que el código **compile y ejecute sin errores no controlados**. Las excepciones deben quedar capturadas por la sección `EXCEPTION`, nunca propagarse al cliente como errores de Oracle.

1. Escribe un bloque que intente cargar con `SELECT INTO` el nombre y salario del empleado con `id_emp = 999` (que no existe). Captura la excepción `NO_DATA_FOUND` e imprime: `'No se encontró ningún empleado con ID 999'`. Sin el bloque `EXCEPTION`, ¿qué mensaje de error mostraría Oracle?

2. Escribe un bloque que intente cargar con `SELECT INTO` un único apellido de `U15_EMPLEADOS` **sin cláusula `WHERE`** (con lo que la consulta devuelve 7 filas). Captura `TOO_MANY_ROWS` e imprime un mensaje indicando que la consulta devolvió más filas de las esperadas. Añade también la captura de `NO_DATA_FOUND` aunque en este caso no se produzca, para practicar el bloque `EXCEPTION` con múltiples `WHEN`.

3. Carga con `SELECT INTO` el número de componentes con `stock = 0` y el número total de componentes. Calcula el **porcentaje de componentes sin stock** dividiendo el primero entre el segundo. Captura la excepción `ZERO_DIVIDE` para el caso en que la tabla estuviera vacía (denominador = 0) e imprime un mensaje apropiado. Para probar que el `EXCEPTION` funciona, añade al final del script —comentado— la versión que forzaría el error (`... / 0`).

4. Intenta insertar en `U15_COMPONENTES` un componente con el código `'RAM-COR-16'`, que ya existe en la tabla. Captura la excepción `DUP_VAL_ON_INDEX` e imprime: `'Error: ya existe un componente con ese código en el catálogo.'`. Incluye un `ROLLBACK` dentro del bloque de la excepción.

5. Define una **excepción de usuario** llamada `e_stock_insuficiente`. Escribe un bloque que: (a) cargue el stock del componente `'GPU-NV-409'`, (b) compruebe si el stock es menor que una cantidad solicitada (usa el literal `3` como cantidad pedida), y (c) lance `RAISE e_stock_insuficiente` si no hay suficiente. Captura la excepción e imprime: `'Stock insuficiente: se solicitaron 3 unidades pero solo hay X disponibles.'` (sustituye X por el valor real cargado).

6. Define mediante `PRAGMA EXCEPTION_INIT` una excepción con nombre para el error Oracle **ORA-02291** (violación de FK — registro padre no encontrado). Llámala `e_fk_violation`. Intenta insertar en `U15_PEDIDOS` un pedido cuyo `id_emp` sea `999` (empleado inexistente) y captura tu excepción nombrada imprimiendo: `'Error de integridad: el empleado o componente referenciado no existe.'`. Incluye `ROLLBACK`.

7. Escribe un bloque que contenga un **bloque interno anidado**. El bloque interno debe intentar una división por cero. Si la excepción no se captura en el bloque interno, se propagará al externo. Captura en el bloque **externo** con `WHEN OTHERS` e imprime el código de error (`SQLCODE`) y el mensaje (`SQLERRM`). Además, inserta una fila en `U15_LOG_ACTIVIDAD` con el mensaje de error usando la secuencia `U15_SEQ_LOG.NEXTVAL`, el bloque `'IV.7'` y el texto del error. Finaliza con `COMMIT` para que el log quede guardado.

---

## 7. Bloque V — Integración

*Recuerda ejecutar el script de inicialización antes de comenzar este bloque.*

> Estos ejercicios combinan todos los elementos del tema. Se valora especialmente que el código esté bien estructurado, comentado y sea robusto ante errores.

1. **Ficha completa de componente.** Escribe un script que, dado el código `'CPU-INT-09'`, genere la siguiente ficha impresa por `DBMS_OUTPUT`. Todos los valores deben obtenerse de la base de datos (no hardcodeados), excepto la constante de IVA (21%):
   ```
   ╔══════════════════════════════════════╗
   ║  FICHA DE COMPONENTE                 ║
   ╠══════════════════════════════════════╣
   ║  Código    : CPU-INT-09              ║
   ║  Nombre    : Intel Core i9 12900K    ║
   ║  Categoría : Procesador              ║  ← descripción, no código
   ║  Precio    : 650,50 €                ║
   ║  IVA (21%) : 136,61 €               ║
   ║  Total     : 787,11 €               ║
   ║  Stock     : 10 uds — Nivel: NORMAL  ║  ← CRITICO<5 / BAJO 5-15 / NORMAL>15
   ╚══════════════════════════════════════╝
   ```
   Usa una **constante** para el IVA, `CASE` para la descripción de categoría y el nivel de stock, e `IF` para imprimir el símbolo de alerta si el nivel es `'CRITICO'`. Gestiona el caso `NO_DATA_FOUND` si el código no existe.

2. **Informe de nómina.** Escribe un script que recorra **todos los empleados** de `U15_EMPLEADOS` usando un bucle `FOR` con cursor implícito y calcule para cada uno:
   - Sus ingresos totales: salario + comisión (con `NVL`).
   - Su tramo salarial: `'BÁSICO'` / `'ESTÁNDAR'` / `'SENIOR'` / `'DIRECTIVO'` (mismos rangos del Bloque II, ejercicio 3).
   - Su bonificación de fin de año (mismas reglas del Bloque II, ejercicio 7).
   
   Imprime una línea por empleado con todos estos datos. Al terminar el bucle, imprime una línea de totales con: número de empleados procesados, suma total de ingresos y suma total de bonificaciones. Usa variables acumuladoras (`PLS_INTEGER` para contadores, `NUMBER` para sumas).

3. **Simulación de venta con log de auditoría.** Escribe un script que simule el proceso de venta de **2 unidades** del componente `'RAM-COR-16'` gestionado por el empleado `id_emp = 103`. El script debe:
   - Verificar que el empleado existe (excepción `NO_DATA_FOUND` si no).
   - Verificar que el componente existe y tiene suficiente stock (excepción de usuario `e_stock_insuficiente` si no).
   - Si todo es correcto: decrementar el stock en 2 unidades (`UPDATE`) e insertar un nuevo pedido en `U15_PEDIDOS` (usa `NVL(MAX(id_pedido),2000)+1` para generar el ID) con estado `'PENDIENTE'`.
   - En cualquier caso (éxito o error), insertar una fila en `U15_LOG_ACTIVIDAD` con el resultado de la operación (mensaje de éxito o descripción del error).
   - Si la operación fue exitosa: `COMMIT`. Si hubo error: `ROLLBACK` (pero el log debe confirmarse con `COMMIT` igualmente).
   - Imprime al final si la operación fue exitosa o falló, y el ID del nuevo pedido generado (en caso de éxito).

---

## Criterios de entrega

1. Entrega un único fichero `.sql`.
2. Añade al principio del fichero: `-- Autor: Nombre Apellido`.
3. Numera cada ejercicio con un comentario antes de cada bloque: `-- Ejercicio I.1`, `-- Ejercicio III.5`, etc.
4. El fichero debe poder ejecutarse en Oracle 21c **sin errores de compilación**. Los errores de ejecución controlados (excepciones capturadas) son correctos y esperados.
5. En los ejercicios marcados como *"solo escritura"*, incluye el código como comentario de bloque (`/* ... */`) precedido de la etiqueta del ejercicio.
6. Activa **DBMS Output** en SQL Developer antes de ejecutar (menú *Ver → DBMS Output → botón +*) para poder ver la salida de todos los bloques.
