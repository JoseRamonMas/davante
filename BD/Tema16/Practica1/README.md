# Unidad 16: Práctica 1 — Tipos de datos compuestos y cursores

## 1. Contexto del esquema de trabajo

Seguimos en el universo **TechFix Solutions**, pero ahora toda la lógica del negocio se resuelve con los recursos aprendidos en este tema: registros, colecciones y cursores PL/SQL. El esquema amplía las tablas habituales con una tabla de **valoraciones de reparaciones** y un **log de operaciones** donde nuestros scripts dejarán trazas de su actividad.

| Tabla | Descripción |
|---|---|
| `U16_EMPLEADOS` | Personal de TechFix: técnicos, vendedores y gerentes |
| `U16_COMPONENTES` | Catálogo de componentes informáticos con stock y precio |
| `U16_REPARACIONES` | Órdenes de reparación con estado y coste |
| `U16_VALORACIONES` | Puntuaciones (1-5) que los clientes dejan por cada reparación |
| `U16_LOG_OPERACIONES` | Registro de eventos generados por nuestros scripts PL/SQL |

> **Nota:** Ejecuta el script de inicialización antes de cada bloque de ejercicios para partir de un estado limpio y predecible.

---

## 2. Script de inicialización

```sql
/* ==========================================================
   ESQUEMA: TECHFIX SOLUTIONS — Unidad 16
   Tablas: U16_EMPLEADOS,    U16_COMPONENTES,
           U16_REPARACIONES, U16_VALORACIONES,
           U16_LOG_OPERACIONES
   ========================================================== */

-- Borrado de tablas y secuencia (orden de hijas a padres)
DROP SEQUENCE U16_SEQ_LOG;
DROP TABLE U16_LOG_OPERACIONES CASCADE CONSTRAINTS;
DROP TABLE U16_VALORACIONES    CASCADE CONSTRAINTS;
DROP TABLE U16_REPARACIONES    CASCADE CONSTRAINTS;
DROP TABLE U16_COMPONENTES     CASCADE CONSTRAINTS;
DROP TABLE U16_EMPLEADOS       CASCADE CONSTRAINTS;

-- 1. EMPLEADOS
CREATE TABLE U16_EMPLEADOS (
    id_emp         NUMBER(4)    PRIMARY KEY,
    nombre         VARCHAR2(30) NOT NULL,
    apellido       VARCHAR2(30) NOT NULL,
    puesto         VARCHAR2(20) CHECK (puesto IN ('TECNICO','VENDEDOR','GERENTE')),
    salario        NUMBER(7,2)  NOT NULL,
    comision       NUMBER(7,2),
    fecha_contrato DATE         NOT NULL
);

-- 2. COMPONENTES
CREATE TABLE U16_COMPONENTES (
    cod_comp  VARCHAR2(10)  PRIMARY KEY,
    nombre    VARCHAR2(50)  NOT NULL,
    categoria VARCHAR2(20)  CHECK (categoria IN ('GPU','CPU','RAM','ALMACENAMIENTO','PERIFERICO')),
    precio    NUMBER(8,2)   NOT NULL,
    stock     NUMBER(4)     DEFAULT 0 NOT NULL
);

-- 3. REPARACIONES
CREATE TABLE U16_REPARACIONES (
    id_rep     NUMBER(5)    PRIMARY KEY,
    id_emp     NUMBER(4)    REFERENCES U16_EMPLEADOS(id_emp),
    cliente    VARCHAR2(50) NOT NULL,
    dispositivo VARCHAR2(50),
    estado     VARCHAR2(15) CHECK (estado IN ('PENDIENTE','EN_PROCESO','TERMINADO','ENTREGADO')),
    coste      NUMBER(6,2),
    fecha_entrada DATE      DEFAULT SYSDATE
);

-- 4. VALORACIONES
CREATE TABLE U16_VALORACIONES (
    id_val     NUMBER(6)   PRIMARY KEY,
    id_rep     NUMBER(5)   REFERENCES U16_REPARACIONES(id_rep),
    puntuacion NUMBER(1)   CHECK (puntuacion BETWEEN 1 AND 5),
    comentario VARCHAR2(200),
    fecha_val  DATE        DEFAULT SYSDATE
);

-- 5. LOG DE OPERACIONES (destino de escritura desde PL/SQL)
CREATE TABLE U16_LOG_OPERACIONES (
    id_log     NUMBER(6)     PRIMARY KEY,
    fecha_hora TIMESTAMP     DEFAULT SYSTIMESTAMP NOT NULL,
    bloque     VARCHAR2(50),
    mensaje    VARCHAR2(500) NOT NULL
);

CREATE SEQUENCE U16_SEQ_LOG START WITH 1 INCREMENT BY 1 NOCACHE;

-- ============================================================
-- INSERCIONES
-- ============================================================

INSERT INTO U16_EMPLEADOS VALUES (101,'Carlos', 'Aranda', 'GERENTE', 3500.00,   NULL, TO_DATE('15/01/2010','DD/MM/YYYY'));
INSERT INTO U16_EMPLEADOS VALUES (102,'Elena',  'Vázquez','TECNICO', 1800.00, 200.00, TO_DATE('20/03/2015','DD/MM/YYYY'));
INSERT INTO U16_EMPLEADOS VALUES (103,'Marcos', 'Ruiz',   'VENDEDOR',1400.00, 600.00, TO_DATE('10/07/2018','DD/MM/YYYY'));
INSERT INTO U16_EMPLEADOS VALUES (104,'Lucía',  'Gómez',  'VENDEDOR',1450.00,   NULL, TO_DATE('05/11/2021','DD/MM/YYYY'));
INSERT INTO U16_EMPLEADOS VALUES (105,'David',  'Sanz',   'TECNICO', 1750.00, 150.00, TO_DATE('12/02/2019','DD/MM/YYYY'));
INSERT INTO U16_EMPLEADOS VALUES (106,'Sofía',  'Méndez', 'VENDEDOR',1300.00, 300.00, SYSDATE - 50);
INSERT INTO U16_EMPLEADOS VALUES (107,'Javier', 'Trillo', 'TECNICO', 1600.00,   NULL, SYSDATE - 10);

INSERT INTO U16_COMPONENTES VALUES ('CPU-INT-09','Intel Core i9 12900K',  'CPU',           650.50, 10);
INSERT INTO U16_COMPONENTES VALUES ('CPU-AMD-07','AMD Ryzen 7 5800X',     'CPU',           420.00,  5);
INSERT INTO U16_COMPONENTES VALUES ('GPU-NV-306','Nvidia RTX 3060',       'GPU',           380.99,  2);
INSERT INTO U16_COMPONENTES VALUES ('GPU-NV-409','Nvidia RTX 4090',       'GPU',          1850.00,  0);
INSERT INTO U16_COMPONENTES VALUES ('RAM-COR-16','Corsair Vengeance 16GB','RAM',            85.50, 50);
INSERT INTO U16_COMPONENTES VALUES ('RAM-KIN-32','Kingston Fury 32GB',    'RAM',           139.99, 20);
INSERT INTO U16_COMPONENTES VALUES ('SSD-SAM-1T','Samsung 980 Pro 1TB',   'ALMACENAMIENTO',120.00, 25);
INSERT INTO U16_COMPONENTES VALUES ('MOU-LOG-MX','Logitech MX Master 3',  'PERIFERICO',    99.99, 15);
INSERT INTO U16_COMPONENTES VALUES ('KEY-RAZ-BL','Razer BlackWidow',      'PERIFERICO',   140.50,  8);

INSERT INTO U16_REPARACIONES VALUES (5001, 102, 'Ana Torres',    'Portátil HP Pavilion',  'PENDIENTE',   80.00, SYSDATE - 2);
INSERT INTO U16_REPARACIONES VALUES (5002, 105, 'Luis Gil',      'Torre Custom',           'EN_PROCESO',  45.50, SYSDATE - 5);
INSERT INTO U16_REPARACIONES VALUES (5003, 102, 'Pedro Mola',    'Consola PS5',            'TERMINADO',   30.00, SYSDATE - 10);
INSERT INTO U16_REPARACIONES VALUES (5004, 107, 'Marta Ríos',    'Portátil Dell XPS',      'ENTREGADO',   25.00, TO_DATE('10/01/2024','DD/MM/YYYY'));
INSERT INTO U16_REPARACIONES VALUES (5005, 105, 'Roberto Cano',  'Servidor Dell',          'PENDIENTE',  150.00, SYSDATE - 1);
INSERT INTO U16_REPARACIONES VALUES (5006, 102, 'Carmen Vidal',  'MacBook Pro',            'EN_PROCESO',  95.00, SYSDATE - 3);
INSERT INTO U16_REPARACIONES VALUES (5007, 107, 'Ana Torres',    'Tablet Samsung',         'TERMINADO',   40.00, SYSDATE - 7);
INSERT INTO U16_REPARACIONES VALUES (5008, 105, 'Luis Gil',      'Impresora HP',           'ENTREGADO',   20.00, TO_DATE('05/02/2024','DD/MM/YYYY'));

INSERT INTO U16_VALORACIONES VALUES (1, 5003, 5, 'Reparación excelente, muy rápido', SYSDATE - 8);
INSERT INTO U16_VALORACIONES VALUES (2, 5004, 4, 'Bien, aunque tardó un poco más', SYSDATE - 5);
INSERT INTO U16_VALORACIONES VALUES (3, 5007, 5, 'Perfecto, muy profesional', SYSDATE - 6);
INSERT INTO U16_VALORACIONES VALUES (4, 5008, 3, 'Correcto pero sin más', SYSDATE - 3);

COMMIT;
```

---

## 3. Bloque I — Registros (`RECORD`)

*Recuerda ejecutar el script de inicialización antes de comenzar este bloque.*

1. Declara un tipo registro `t_ficha_emp` con los campos `id_emp`, `nombre`, `apellido`, `puesto` y `salario`, usando `%TYPE` sobre las columnas de `U16_EMPLEADOS` para cada campo. Declara una variable de ese tipo, cárgala con los datos del empleado `id_emp = 102` mediante `SELECT INTO`, e imprime una ficha con el formato:
   ```
   ID: 102 | Nombre: Elena Vázquez | Puesto: TECNICO | Salario: 1800 €
   ```

2. Declara una variable de tipo `U16_REPARACIONES%ROWTYPE` y cárgala con los datos de la reparación `id_rep = 5005`. Imprime el nombre del cliente, el dispositivo y el coste con el formato:
   ```
   Cliente: Roberto Cano — Dispositivo: Servidor Dell — Coste: 150 €
   ```

3. Declara dos registros del mismo tipo `t_comp` (definido con campos `cod_comp`, `nombre` y `precio` usando `%TYPE`). Carga el primero con los datos del componente `'GPU-NV-306'` mediante `SELECT INTO`. Asigna el primer registro al segundo directamente (asignación entre registros del mismo tipo). Modifica en el segundo registro el precio al doble. Imprime los precios de ambos registros para demostrar que la asignación fue una copia independiente.

4. Declara dos tipos de registro distintos `t_emp_a` y `t_emp_b`, ambos con los campos `id_emp NUMBER` y `nombre VARCHAR2(30)`. Carga un registro de tipo `t_emp_a` con datos del empleado `id_emp = 103`. Intenta asignarlo a un registro de tipo `t_emp_b` —que fallará—. Comenta esa línea y realiza la asignación **campo por campo** correctamente. Imprime el nombre del registro de tipo `t_emp_b` para verificar que la copia fue correcta.
   > *Este ejercicio ilustra la restricción de asignación entre tipos distintos aunque sean estructuralmente iguales.*

5. Declara una variable `v_rep` de tipo `U16_REPARACIONES%ROWTYPE`. Rellena sus campos manualmente (sin `SELECT INTO`) con los siguientes valores: `id_rep = 5099`, `id_emp = 102`, `cliente = 'Cliente Prueba'`, `dispositivo = 'PC Sobremesa'`, `estado = 'PENDIENTE'`, `coste = 55.00`, `fecha_entrada = SYSDATE`. Inserta ese registro en `U16_REPARACIONES` usando `INSERT INTO U16_REPARACIONES VALUES v_rep` y confirma la inserción con `COMMIT`. Verifica el resultado con un `SELECT` final dentro del mismo bloque.

6. Carga con `SELECT INTO` en una variable `U16_EMPLEADOS%ROWTYPE` los datos del empleado con mayor salario. Aplica al registro una subida salarial del 12% y actualiza la tabla con ese nuevo valor usando `UPDATE ... WHERE id_emp = v_emp.id_emp`. Imprime el nombre del empleado y su salario antes y después de la subida. Haz `COMMIT`.

---

## 4. Bloque II — Colecciones

*Recuerda ejecutar el script de inicialización antes de comenzar este bloque.*

### VARRAY

1. Declara un tipo `t_estados IS VARRAY(4) OF VARCHAR2(15)` e inicialízalo con los cuatro estados posibles de una reparación: `'PENDIENTE'`, `'EN_PROCESO'`, `'TERMINADO'`, `'ENTREGADO'`. Recorre el array con un bucle `FOR` numérico e imprime cada estado junto a su número de orden.

2. Declara un `VARRAY` de hasta 9 elementos de tipo `VARCHAR2(50)` llamado `t_nombres_comp`. Inícialo vacío (`t_nombres_comp()`). Usa un cursor `FOR` implícito para recorrer `U16_COMPONENTES` ordenado por precio descendente y en cada iteración llama a `EXTEND` para añadir el nombre al array. Al terminar el bucle, imprime todos los nombres del array, el número de elementos que contiene (`COUNT`) y su capacidad máxima (`LIMIT`).

3. Partiendo del array del ejercicio anterior (o declarando uno nuevo del mismo tipo con los mismos nombres cargados), imprime los elementos en **orden inverso** usando `FOR i IN REVERSE`. Después usa los métodos `FIRST`, `LAST`, `PRIOR` y `NEXT` para imprimir, en líneas separadas, el primer elemento, el último, el anterior al último y el siguiente al primero.

4. Declara un `VARRAY` de hasta 5 elementos `NUMBER(7,2)` llamado `t_salarios`. Cárgalo con los salarios de los 5 empleados con mayor salario (usa un cursor `FOR` implícito con `ROWNUM <= 5` o `FETCH FIRST 5 ROWS ONLY` en la subconsulta). Calcula e imprime la media aritmética de esos salarios usando `COUNT` para el divisor. *(Pista: acumula la suma en una variable `NUMBER` dentro del bucle de carga.)*

### Tablas anidadas

5. Declara un tipo `t_lista_clientes IS TABLE OF VARCHAR2(50)` e inicialízalo con los nombres de los clientes de las reparaciones en estado `'TERMINADO'` o `'ENTREGADO'` (cárgalos con un cursor `FOR` implícito + `EXTEND`). A continuación:
   - Imprime cuántos hay con `COUNT`.
   - Elimina el **primer** elemento con `DELETE(1)` (simula que ese cliente ya recogió su dispositivo y se cierra el registro).
   - Recorre la lista con un bucle `WHILE` usando `NEXT` y comprueba `EXISTS(i)` en cada paso para esquivar el hueco creado.
   - Imprime cada elemento que aún exista.

6. Declara una tabla anidada de tipo `U16_REPARACIONES%ROWTYPE` llamada `t_lista_reps`. Cárgala con todas las reparaciones asignadas al técnico `id_emp = 102` usando un cursor `FOR` implícito + `EXTEND`. Después:
   - Imprime cuántas reparaciones hay.
   - Elimina las que tengan `estado = 'ENTREGADO'` iterando con un bucle y usando `DELETE(i)` cuando se cumpla la condición.
   - Imprime los `id_rep` y `estado` de las reparaciones que queden, comprobando `EXISTS(i)` en cada paso.

### Arrays asociativos

7. Declara un array asociativo `t_stock IS TABLE OF NUMBER INDEX BY VARCHAR2(10)` y cárgalo con todos los pares `cod_comp → stock` de `U16_COMPONENTES` usando un cursor `FOR` implícito. Luego:
   - Consulta e imprime el stock de `'RAM-COR-16'` y `'GPU-NV-409'` accediendo directamente por clave.
   - Comprueba con `EXISTS` si existe la clave `'CPU-XXX-00'` e imprime un mensaje apropiado.
   - Imprime el número total de pares almacenados con `COUNT`.

8. Declara un array asociativo `t_media_coste IS TABLE OF NUMBER INDEX BY VARCHAR2(15)` donde la clave sea el `estado` de la reparación y el valor sea el coste medio de las reparaciones de ese estado. Cárgalo consultando `U16_REPARACIONES` con un cursor `FOR` implícito que agrupe por estado y calcule la media con `AVG`. Recorre el array completo con `FIRST`/`NEXT` e imprime cada par estado → coste medio (redondeado a 2 decimales).

---

## 5. Bloque III — Cursores implícitos

*Recuerda ejecutar el script de inicialización antes de comenzar este bloque.*

> Los cursores implícitos (`SQL%...`) se crean automáticamente por Oracle al ejecutar `SELECT INTO`, `INSERT`, `UPDATE` o `DELETE`. Sus atributos — `SQL%FOUND`, `SQL%NOTFOUND`, `SQL%ROWCOUNT` — reflejan el resultado de la **última** sentencia DML o `SELECT INTO` ejecutada.

1. Escribe un bloque que actualice el estado de la reparación `id_rep = 5001` de `'PENDIENTE'` a `'EN_PROCESO'`. Tras el `UPDATE`, usa `SQL%FOUND` para imprimir `'Reparación actualizada correctamente'` o `'No se encontró la reparación'` según corresponda. Haz `COMMIT`.

2. Escribe un bloque que borre de `U16_REPARACIONES` todas las reparaciones con estado `'EN PROCESO'`. Después del `DELETE`, usa `SQL%ROWCOUNT` para imprimir cuántas filas se eliminaron con el mensaje: `'Reparaciones archivadas: N'`. Haz `ROLLBACK` al final para no alterar el estado del esquema para los bloques siguientes.

3. Escribe un bloque que intente cargar con `SELECT INTO` el `coste` de la reparación `id_rep = 9999` (inexistente). Maneja la excepción `NO_DATA_FOUND` e imprime un mensaje de error. Si la reparación sí existiera, ¿cuál sería el valor de `SQL%FOUND` justo después del `SELECT INTO`? Responde mediante un comentario en el código.

4. Escribe un bloque que realice las tres operaciones siguientes en secuencia, imprimiendo el valor de `SQL%ROWCOUNT` **después de cada una**:
   - `INSERT` de una nueva valoración (`id_val = 99, id_rep = 5003, puntuacion = 5, comentario = 'Test'`).
   - `UPDATE` de la comisión de todos los `'VENDEDOR'` sumándoles 50 €.
   - `DELETE` de todos los componentes con `stock = 0`.
   
   Haz `ROLLBACK` al final para no alterar el esquema.

---

## 6. Bloque IV — Cursores explícitos

*Recuerda ejecutar el script de inicialización antes de comenzar este bloque.*

### Ciclo de vida: OPEN / FETCH / CLOSE

1. Declara un cursor `cur_tecnicos` que seleccione `id_emp`, `nombre`, `apellido` y `salario` de los empleados con `puesto = 'TECNICO'`, ordenados por salario descendente. Gestiona su ciclo de vida completo: `OPEN`, bucle `LOOP` con `FETCH ... INTO` variables individuales, `EXIT WHEN cur_tecnicos%NOTFOUND`, y `CLOSE`. Imprime una línea por cada técnico. Tras cerrar el cursor, imprime el número total de filas procesadas usando `%ROWCOUNT`.
   > *(Nota: `%ROWCOUNT` solo es válido mientras el cursor está abierto o justo antes de cerrarlo; guárdalo en una variable auxiliar antes del `CLOSE` si lo necesitas después.)*

2. Declara un cursor `cur_reps_pendientes` que seleccione todas las columnas de `U16_REPARACIONES` donde el estado sea `'PENDIENTE'` o `'EN_PROCESO'`. Usa `cur_reps_pendientes%ROWTYPE` como tipo de la variable de recepción del `FETCH`. Antes de abrir el cursor, comprueba con `%ISOPEN` que no está ya abierto e imprime el resultado de la comprobación. Recorre el cursor e imprime el `id_rep`, cliente y estado de cada reparación. Al terminar, vuelve a comprobar e imprimir el estado de `%ISOPEN` tras el `CLOSE`.

3. Declara dos cursores: `cur_reps` (todas las reparaciones) y `cur_vals` (todas las valoraciones de un `id_rep` dado, usando parámetro). Recorre `cur_reps` con el ciclo completo y, para cada reparación, abre `cur_vals` pasando su `id_rep` como parámetro. Si la reparación tiene valoraciones (`cur_vals%ROWCOUNT > 0` tras recorrerla), imprime el `id_rep`, el cliente y la puntuación de cada valoración. Si no tiene ninguna, imprime `'— Sin valoración'`. Cierra ambos cursores correctamente.
   > *Pista: Este es el patrón de cursores anidados. Recuerda abrir y cerrar `cur_vals` dentro del bucle externo, en cada iteración.*

### Cursor FOR LOOP

4. Reescribe el ejercicio 1 de este bloque usando un **cursor `FOR` loop** con el cursor declarado en la sección `DECLARE`. El resultado impreso debe ser idéntico. Reflexiona (como comentario en el código) sobre qué instrucciones del ciclo de vida ya no son necesarias con esta sintaxis.

5. Usa un **cursor `FOR` loop inline** (sin declarar el cursor en `DECLARE`) para generar un informe de componentes con bajo stock. El informe debe mostrar, para cada componente con `stock < 10`, su código, nombre, stock actual y una columna calculada `'CRÍTICO'` si `stock = 0` o `'BAJO'` si `stock < 10`. Ordena por stock ascendente. Imprime también, fuera del bucle, el total de componentes procesados usando una variable acumuladora de tipo `PLS_INTEGER`.

6. Usa un cursor `FOR` loop con un cursor **parametrizado** `cur_reps_emp` que reciba un `p_id_emp NUMBER` y devuelva las reparaciones asignadas a ese empleado. Ejecuta el cursor dos veces seguidas (sin redeclararlos: basta con dos bucles `FOR` en el bloque `BEGIN`): primero para el empleado `102` y después para el `105`. Para cada reparación imprime el id, cliente, estado y coste. Entre los dos grupos imprime una línea separadora.

---

## 7. Bloque V — Actualización con cursores (`FOR UPDATE`)

*Recuerda ejecutar el script de inicialización antes de comenzar este bloque.*

> En este bloque modificamos datos reales de las tablas. Incluye siempre un bloque `EXCEPTION` con `ROLLBACK` + `CLOSE` del cursor para liberar los bloqueos en caso de error. Al terminar cada ejercicio, ejecuta el script de inicialización si quieres volver al estado original.

1. Declara un cursor `cur_revision_salarial` que seleccione `id_emp`, `nombre`, `apellido`, `puesto` y `salario` de `U16_EMPLEADOS`, bloqueando la columna `salario` con `FOR UPDATE OF salario NOWAIT`. Recorre el cursor y aplica las siguientes reglas con `WHERE CURRENT OF`:
   - `'TECNICO'` con salario < 1700 €: +8%
   - `'TECNICO'` con salario >= 1700 €: +5%
   - `'VENDEDOR'`: +3%
   - `'GERENTE'`: sin cambio

   Imprime una línea por empleado con el salario antiguo y el nuevo (o `'Sin cambio'`). Al terminar el bucle, imprime el coste total de la revisión (suma de todos los incrementos). Haz `COMMIT`.

2. Declara un cursor `cur_cerrar_reps` que seleccione las reparaciones con estado `'TERMINADO'` bloqueándolas con `FOR UPDATE NOWAIT`. Recórrelo y para cada fila:
   - Cambia el estado a `'ENTREGADO'` con `WHERE CURRENT OF`.
   - Inserta una fila en `U16_LOG_OPERACIONES` con `U16_SEQ_LOG.NEXTVAL` como `id_log`, el bloque `'V.2'` y el mensaje `'Reparación [id_rep] entregada al cliente [cliente]'` (sustituye los valores reales). Usa `SYSTIMESTAMP` como `fecha_hora`.
   
   Tras el bucle, imprime cuántas reparaciones se han cerrado. Haz `COMMIT`. Verifica el resultado con un `SELECT` final sobre `U16_REPARACIONES` y otro sobre `U16_LOG_OPERACIONES`.

---


## Criterios de entrega

1. Entrega un único fichero `.sql`.
2. Añade al principio del fichero: `-- Autor: Nombre Apellido`.
3. Numera cada ejercicio con un comentario antes de cada bloque PL/SQL: `-- Ejercicio I.1`, `-- Ejercicio IV.3`, etc.
4. El fichero debe poder ejecutarse en Oracle 21c **sin errores de compilación**. Los errores de ejecución controlados (excepciones capturadas) son correctos y esperados.
5. Activa **DBMS Output** en SQL Developer antes de ejecutar (menú *Ver → DBMS Output → botón +*) para ver la salida de todos los bloques.
6. En el Bloque V (**FOR UPDATE**), incluye siempre un bloque `EXCEPTION` mínimo con `ROLLBACK` y cierre del cursor. No dejes bloqueos sin liberar.
7. Añade una línea en blanco entre ejercicios consecutivos y usa comentarios para separar visualmente las secciones lógicas dentro de un mismo bloque PL/SQL.
