# Unidad 17: Práctica 1 — Subprogramas y disparadores

## 1. Contexto del esquema de trabajo

Seguimos en el universo **TechFix Solutions**. En esta práctica toda la lógica de negocio se implementa mediante los recursos propios del tema: procedimientos, funciones, paquetes y disparadores almacenados en la base de datos. El esquema incorpora dos tablas nuevas: `U17_PRESUPUESTOS`, que registra los presupuestos emitidos antes de iniciar cada reparación, y `U17_AUDITORIA`, donde los disparadores anotarán automáticamente los cambios producidos en las tablas principales.

| Tabla                  | Descripción                                                           |
|------------------------|-----------------------------------------------------------------------|
| `U17_EMPLEADOS`        | Personal de TechFix: técnicos, vendedores y gerente                   |
| `U17_REPARACIONES`     | Órdenes de reparación con estado, coste y fecha de cierre             |
| `U17_PRESUPUESTOS`     | Presupuestos emitidos para cada reparación antes de comenzarla        |
| `U17_AUDITORIA`        | Registro automático de cambios generado por los disparadores          |
| `U17_LOG_SUBPROGRAMAS` | Trazas de ejecución escritas por procedimientos y paquetes            |

> **Nota:** Ejecuta el script de inicialización antes de cada bloque de ejercicios para partir de un estado limpio y predecible.

---

## 2. Script de inicialización

```sql
/* ==========================================================
   ESQUEMA: TECHFIX SOLUTIONS — Unidad 17
   Tablas: U17_EMPLEADOS,    U17_REPARACIONES,
           U17_PRESUPUESTOS, U17_AUDITORIA,
           U17_LOG_SUBPROGRAMAS
   ========================================================== */

-- Borrado de objetos (de hijas a padres)
DROP SEQUENCE U17_SEQ_LOG;
DROP SEQUENCE U17_SEQ_AUDIT;
DROP TABLE U17_LOG_SUBPROGRAMAS CASCADE CONSTRAINTS;
DROP TABLE U17_AUDITORIA        CASCADE CONSTRAINTS;
DROP TABLE U17_PRESUPUESTOS     CASCADE CONSTRAINTS;
DROP TABLE U17_REPARACIONES     CASCADE CONSTRAINTS;
DROP TABLE U17_EMPLEADOS        CASCADE CONSTRAINTS;

-- 1. EMPLEADOS
CREATE TABLE U17_EMPLEADOS (
    id_emp         NUMBER(4)    PRIMARY KEY,
    nombre         VARCHAR2(30) NOT NULL,
    apellido       VARCHAR2(30) NOT NULL,
    puesto         VARCHAR2(20) CHECK (puesto IN ('TECNICO','VENDEDOR','GERENTE')),
    salario        NUMBER(7,2)  NOT NULL,
    comision       NUMBER(7,2),
    fecha_contrato DATE         NOT NULL
);

-- 2. REPARACIONES
CREATE TABLE U17_REPARACIONES (
    id_rep        NUMBER(5)    PRIMARY KEY,
    id_emp        NUMBER(4)    REFERENCES U17_EMPLEADOS(id_emp),
    cliente       VARCHAR2(50) NOT NULL,
    dispositivo   VARCHAR2(50),
    estado        VARCHAR2(15) CHECK (estado IN ('PENDIENTE','EN_PROCESO','TERMINADO','ENTREGADO')),
    coste         NUMBER(6,2),
    fecha_entrada DATE         DEFAULT SYSDATE,
    fecha_cierre  DATE                         -- NULL hasta la entrega
);

-- 3. PRESUPUESTOS
CREATE TABLE U17_PRESUPUESTOS (
    id_presupuesto NUMBER(5)    PRIMARY KEY,
    id_rep         NUMBER(5)    REFERENCES U17_REPARACIONES(id_rep),
    descripcion    VARCHAR2(200),
    importe        NUMBER(7,2)  NOT NULL,
    estado         VARCHAR2(15) CHECK (estado IN ('PENDIENTE','ACEPTADO','RECHAZADO')),
    fecha_emision  DATE         DEFAULT SYSDATE
);

-- 4. AUDITORIA (destino de los disparadores)
CREATE TABLE U17_AUDITORIA (
    id_auditoria   NUMBER(6)    PRIMARY KEY,
    tabla_afectada VARCHAR2(30) NOT NULL,
    accion         VARCHAR2(10) NOT NULL,       -- 'INSERT', 'UPDATE', 'DELETE'
    id_registro    NUMBER(6),
    campo          VARCHAR2(30),
    valor_anterior VARCHAR2(200),
    valor_nuevo    VARCHAR2(200),
    usuario_bd     VARCHAR2(30) DEFAULT USER,
    fecha_hora     TIMESTAMP    DEFAULT SYSTIMESTAMP
);

-- 5. LOG DE SUBPROGRAMAS (destino de procedimientos y paquetes)
CREATE TABLE U17_LOG_SUBPROGRAMAS (
    id_log      NUMBER(6)     PRIMARY KEY,
    subprograma VARCHAR2(60),
    mensaje     VARCHAR2(500) NOT NULL,
    fecha_hora  TIMESTAMP     DEFAULT SYSTIMESTAMP
);

CREATE SEQUENCE U17_SEQ_AUDIT START WITH 1 INCREMENT BY 1 NOCACHE;
CREATE SEQUENCE U17_SEQ_LOG   START WITH 1 INCREMENT BY 1 NOCACHE;

-- ============================================================
-- INSERCIONES
-- ============================================================

INSERT INTO U17_EMPLEADOS VALUES (101,'Carlos', 'Aranda', 'GERENTE', 3500.00,   NULL, TO_DATE('15/01/2010','DD/MM/YYYY'));
INSERT INTO U17_EMPLEADOS VALUES (102,'Elena',  'Vázquez','TECNICO', 1800.00, 200.00, TO_DATE('20/03/2015','DD/MM/YYYY'));
INSERT INTO U17_EMPLEADOS VALUES (103,'Marcos', 'Ruiz',   'VENDEDOR',1400.00, 600.00, TO_DATE('10/07/2018','DD/MM/YYYY'));
INSERT INTO U17_EMPLEADOS VALUES (104,'Lucía',  'Gómez',  'VENDEDOR',1450.00,   NULL, TO_DATE('05/11/2021','DD/MM/YYYY'));
INSERT INTO U17_EMPLEADOS VALUES (105,'David',  'Sanz',   'TECNICO', 1750.00, 150.00, TO_DATE('12/02/2019','DD/MM/YYYY'));
INSERT INTO U17_EMPLEADOS VALUES (106,'Sofía',  'Méndez', 'VENDEDOR',1300.00, 300.00, SYSDATE - 50);
INSERT INTO U17_EMPLEADOS VALUES (107,'Javier', 'Trillo', 'TECNICO', 1600.00,   NULL, SYSDATE - 10);

INSERT INTO U17_REPARACIONES VALUES (5001,102,'Ana Torres',   'Portátil HP Pavilion', 'PENDIENTE',  80.00, SYSDATE-2,  NULL);
INSERT INTO U17_REPARACIONES VALUES (5002,105,'Luis Gil',     'Torre Custom',         'EN_PROCESO', 45.50, SYSDATE-5,  NULL);
INSERT INTO U17_REPARACIONES VALUES (5003,102,'Pedro Mola',   'Consola PS5',          'TERMINADO',  30.00, SYSDATE-10, NULL);
INSERT INTO U17_REPARACIONES VALUES (5004,107,'Marta Ríos',   'Portátil Dell XPS',    'ENTREGADO',  25.00, TO_DATE('10/01/2024','DD/MM/YYYY'), TO_DATE('15/01/2024','DD/MM/YYYY'));
INSERT INTO U17_REPARACIONES VALUES (5005,105,'Roberto Cano', 'Servidor Dell',        'PENDIENTE', 150.00, SYSDATE-1,  NULL);
INSERT INTO U17_REPARACIONES VALUES (5006,102,'Carmen Vidal', 'MacBook Pro',          'EN_PROCESO', 95.00, SYSDATE-3,  NULL);
INSERT INTO U17_REPARACIONES VALUES (5007,107,'Ana Torres',   'Tablet Samsung',       'TERMINADO',  40.00, SYSDATE-7,  NULL);

INSERT INTO U17_PRESUPUESTOS VALUES (9001,5001,'Revisión y cambio de teclado',      80.00,'ACEPTADO', SYSDATE-3);
INSERT INTO U17_PRESUPUESTOS VALUES (9002,5002,'Limpieza y sustitución de fuente',  45.50,'ACEPTADO', SYSDATE-6);
INSERT INTO U17_PRESUPUESTOS VALUES (9003,5003,'Reparación lector Blu-ray',         30.00,'ACEPTADO', SYSDATE-11);
INSERT INTO U17_PRESUPUESTOS VALUES (9004,5004,'Cambio de pantalla',                25.00,'ACEPTADO', TO_DATE('08/01/2024','DD/MM/YYYY'));
INSERT INTO U17_PRESUPUESTOS VALUES (9005,5005,'Diagnóstico y sustitución de RAM', 150.00,'PENDIENTE',SYSDATE-2);
INSERT INTO U17_PRESUPUESTOS VALUES (9006,5005,'Cambio de placa base completo',    320.00,'RECHAZADO', SYSDATE-3);

COMMIT;
```

---

## 3. Bloque I — Procedimientos almacenados

*Recuerda ejecutar el script de inicialización antes de comenzar este bloque.*

> En todos los bloques de este tema, activa `SET SERVEROUTPUT ON` al inicio de tu fichero para poder ver la salida de `DBMS_OUTPUT`.

1. Crea un procedimiento `prc_ficha_reparacion(p_id_rep IN NUMBER)` que obtenga con `SELECT INTO` los campos `cliente`, `dispositivo`, `estado`, `coste` y `fecha_entrada` de la reparación, así como el nombre y apellido del técnico asignado mediante `JOIN`. Imprime una ficha por `DBMS_OUTPUT` similar a esta:
   ```
   === Reparación #5002 ===
   Cliente     : Luis Gil
   Dispositivo : Torre Custom
   Estado      : EN_PROCESO
   Coste       : 45,5 €
   Entrada     : 15/03/2024
   Técnico     : David Sanz
   ```
   Captura `NO_DATA_FOUND` e imprime un mensaje de error si la reparación no existe. Invoca el procedimiento para las reparaciones 5001, 5004 y 9999 (esta última debe mostrar el mensaje de error).

2. Crea un procedimiento `prc_resumen_tecnico` con los siguientes parámetros:
   - `p_id_emp IN NUMBER`
   - `p_num_reps OUT NUMBER` — número total de reparaciones asignadas
   - `p_coste_total OUT NUMBER` — suma de costes (con `NVL` para costes nulos)
   
   El procedimiento obtiene los datos con `SELECT ... INTO` usando funciones de agregación. Escribe un bloque anónimo que llame al procedimiento para cada uno de los tres técnicos (IDs 102, 105, 107) e imprima una línea por técnico con su nombre (cárgalo con otro `SELECT INTO`) y los valores devueltos.

3. Crea un procedimiento `prc_actualizar_coste(p_id_rep IN NUMBER, p_porcentaje IN NUMBER, p_coste_final IN OUT NUMBER)` que:
   - Lea el coste actual de la reparación con `SELECT INTO`.
   - Calcule el nuevo coste aplicando el porcentaje sobre el valor actual: `nuevo = actual * (1 + porcentaje/100)` y lo redondee a 2 decimales.
   - Actualice la tabla con el nuevo valor.
   - Asigne ese valor al parámetro `IN OUT` para devolverlo al llamador.
   - Haga `COMMIT` al terminar.
   
   Invócalo para la reparación 5005 con un incremento del 10%. Imprime el coste final recibido por el bloque llamador.

4. Crea un procedimiento `prc_nuevo_presupuesto(p_id_rep IN NUMBER, p_descripcion IN VARCHAR2, p_importe IN NUMBER)` que valide las siguientes condiciones **antes** de insertar en `U17_PRESUPUESTOS`:
   - Si `p_importe <= 0`: lanza `RAISE_APPLICATION_ERROR(-20101, 'El importe del presupuesto debe ser positivo.')`.
   - Si la reparación `p_id_rep` no existe: lanza `RAISE_APPLICATION_ERROR(-20102, 'La reparación indicada no existe.')`.
   - Si ya hay un presupuesto en estado `'PENDIENTE'` para esa reparación: lanza `RAISE_APPLICATION_ERROR(-20103, 'Ya existe un presupuesto pendiente para esta reparación.')`.
   
   Si pasa todas las validaciones, inserta el presupuesto con `estado = 'PENDIENTE'` y el `id_presupuesto` calculado como `(SELECT NVL(MAX(id_presupuesto),9000)+1 FROM U17_PRESUPUESTOS)`. Haz `COMMIT`. Prueba el procedimiento con tres llamadas: una que provoque el error -20101, otra que provoque el -20102 y una última válida (usa la reparación 5003, que no tiene presupuesto pendiente).

5. *(Solo escritura — no es necesario ejecutar este ejercicio.)* Escribe la sentencia SQL que consulta `USER_SOURCE` para mostrar las líneas de código del procedimiento `PRC_FICHA_REPARACION` ordenadas por número de línea. A continuación escribe la sentencia `DROP` para eliminarlo. Explica en un comentario qué ocurriría si otro procedimiento o paquete llamase a `prc_ficha_reparacion` después de haberlo eliminado.

---

## 4. Bloque II — Funciones almacenadas

*Recuerda ejecutar el script de inicialización antes de comenzar este bloque.*

1. Crea una función `fnc_dias_en_taller(p_id_rep IN NUMBER) RETURN NUMBER` que devuelva el número de días completos transcurridos desde `fecha_entrada` hasta hoy (`TRUNC(SYSDATE - fecha_entrada)`). Si la reparación no existe, devuelve `NULL` (captura `NO_DATA_FOUND`). Pruébala en un bloque anónimo para las reparaciones 5001, 5004 y 9999.

2. Crea una función `fnc_nombre_tecnico(p_id_emp IN NUMBER) RETURN VARCHAR2` que devuelva el nombre completo del empleado (`nombre || ' ' || apellido`) en formato `INITCAP`. Si no existe, devuelve `'Técnico desconocido'`. Pruébala para los IDs 105 y 999.

3. Crea una función `fnc_clasificar_reparacion(p_coste IN NUMBER) RETURN VARCHAR2` que devuelva:
   - `'BÁSICA'` si el coste es ≤ 50 €
   - `'ESTÁNDAR'` si está entre 51 € y 150 €
   - `'COMPLEJA'` si supera los 150 €
   - `'SIN COSTE'` si `p_coste IS NULL`
   
   No necesita acceder a ninguna tabla. Pruébala para los valores 30, 95, 200 y `NULL`.

4. Escribe una consulta `SELECT` sobre `U17_REPARACIONES` que, para cada reparación cuyo estado **no sea** `'ENTREGADO'`, muestre: `id_rep`, `cliente`, el nombre completo del técnico (usando `fnc_nombre_tecnico`), los días en taller (usando `fnc_dias_en_taller`) y la clasificación del coste (usando `fnc_clasificar_reparacion`). Ordena por días en taller de mayor a menor. *Observa que las tres funciones se invocan directamente en el `SELECT`, como si fueran funciones SQL nativas.*

5. Crea una función `fnc_esta_disponible(p_id_emp IN NUMBER) RETURN BOOLEAN` que devuelva `TRUE` si el técnico tiene menos de 2 reparaciones con estado `'PENDIENTE'` o `'EN_PROCESO'`, y `FALSE` en caso contrario. Escribe un bloque anónimo que la use para imprimir `'DISPONIBLE'` u `'OCUPADO'` para cada uno de los tres técnicos (102, 105, 107). A continuación, añade como comentario la respuesta a estas dos preguntas: ¿qué error devuelve Oracle si intentas usar `fnc_esta_disponible(102)` directamente dentro de un `SELECT … FROM DUAL`? ¿Qué tipo de retorno usarías para hacer la función compatible con SQL?

---

## 5. Bloque III — Paquetes

*Recuerda ejecutar el script de inicialización antes de comenzar este bloque. Crea también las funciones `fnc_dias_en_taller` y `fnc_nombre_tecnico` del Bloque II si aún no las tienes en tu sesión, ya que el cuerpo del paquete las referenciará.*

1. Crea la **especificación** del paquete `pkg_taller` con los siguientes elementos públicos:
   - Constante `C_REPS_MAX CONSTANT PLS_INTEGER := 3` (número máximo de reparaciones activas por técnico).
   - Tipo registro `t_resumen_rep` con los campos: `id_rep NUMBER`, `cliente VARCHAR2(50)`, `estado VARCHAR2(15)`, `coste NUMBER`, `dias NUMBER`.
   - Cabecera del procedimiento `prc_listado_activas` (sin parámetros).
   - Cabecera de la función `fnc_carga_tecnico(p_id_emp NUMBER) RETURN NUMBER`.

2. Crea el **cuerpo** del paquete `pkg_taller` con:
   - Un procedimiento **privado** `prc_log(p_subprog VARCHAR2, p_msg VARCHAR2)` que inserte una fila en `U17_LOG_SUBPROGRAMAS` usando `U17_SEQ_LOG.NEXTVAL`. *Al ser privado, no aparece en la especificación.*
   - La implementación de `prc_listado_activas`: recorre las reparaciones con estado `'PENDIENTE'` o `'EN_PROCESO'` usando un cursor `FOR` loop, imprime una línea por reparación con `id_rep`, cliente, estado, coste y días en taller (llamando a `fnc_dias_en_taller`), y al finalizar llama a `prc_log` para registrar la ejecución.
   - La implementación de `fnc_carga_tecnico`: devuelve el número de reparaciones activas (`'PENDIENTE'` o `'EN_PROCESO'`) del empleado indicado.

3. Escribe un bloque anónimo que invoque `pkg_taller.prc_listado_activas`. A continuación, escribe una consulta `SELECT` que muestre el `id_emp`, nombre completo (con `fnc_nombre_tecnico`) y carga actual (`pkg_taller.fnc_carga_tecnico`) de todos los empleados con `puesto = 'TECNICO'`. Añade una columna calculada `ALERTA` que valga `'⚠ SATURADO'` si la carga supera `pkg_taller.C_REPS_MAX` y `'OK'` en caso contrario. Verifica también que el log se ha rellenado con un `SELECT * FROM U17_LOG_SUBPROGRAMAS`.

4. *(Solo escritura — no es necesario ejecutar.)* Escribe en un comentario qué error lanzaría Oracle al ejecutar `BEGIN pkg_taller.prc_log('test','test'); END;` desde fuera del paquete, e indica en qué cláusula de la sintaxis del paquete reside la causa. A continuación escribe la consulta sobre `USER_SOURCE` que muestra el código completo del cuerpo del paquete `PKG_TALLER`.

---

## 6. Bloque IV — Disparadores DML

*Recuerda ejecutar el script de inicialización antes de comenzar este bloque.*

> Crea siempre los disparadores con `CREATE OR REPLACE TRIGGER`. Tras cada ejercicio, verifica el resultado con las sentencias `SELECT` indicadas o con una consulta sobre la tabla afectada.

1. Crea un disparador de **instrucción** `trg_log_insert_rep` que se active `AFTER INSERT` sobre `U17_REPARACIONES`. Cada vez que se ejecute una sentencia `INSERT` (independientemente de cuántas filas inserte), debe insertar **una sola fila** en `U17_LOG_SUBPROGRAMAS` con `subprograma = 'TRIGGER'` y el mensaje `'INSERT en REPARACIONES por: ' || USER`. Pruébalo insertando la reparación `(5099, 102, 'Cliente Test', 'Dispositivo Test', 'PENDIENTE', 50.00, SYSDATE, NULL)` y verifica que aparece una fila en el log. Haz `ROLLBACK` para no alterar los datos.

2. Crea un disparador de **fila** `trg_audit_coste` que se active `AFTER UPDATE OF coste` sobre `U17_REPARACIONES` `FOR EACH ROW`. Para cada fila modificada, debe insertar en `U17_AUDITORIA` los valores: `U17_SEQ_AUDIT.NEXTVAL`, `'U17_REPARACIONES'`, `'UPDATE'`, `:OLD.id_rep`, `'COSTE'`, `TO_CHAR(:OLD.coste)`, `TO_CHAR(:NEW.coste)`. Pruébalo ejecutando `UPDATE U17_REPARACIONES SET coste = coste * 1.05 WHERE estado IN ('PENDIENTE','EN_PROCESO')` y comprueba que la tabla `U17_AUDITORIA` contiene exactamente una fila por cada reparación actualizada. Haz `ROLLBACK`.

3. Crea un disparador de fila `trg_valida_importe` que se active `BEFORE INSERT` sobre `U17_PRESUPUESTOS` `FOR EACH ROW`. Si `:NEW.importe <= 0`, lanza `RAISE_APPLICATION_ERROR(-20101, 'El importe del presupuesto debe ser un valor positivo.')`. Prueba dos casos: un `INSERT` con importe `-50` (debe fallar con el error personalizado) y un `INSERT` válido con importe `60.00` para la reparación 5007 (debe funcionar). Haz `ROLLBACK` tras la prueba.

4. Crea un disparador de fila `trg_proteger_rep` que se active `BEFORE DELETE` sobre `U17_REPARACIONES` `FOR EACH ROW`. Si `:OLD.estado != 'ENTREGADO'`, lanza `RAISE_APPLICATION_ERROR(-20110, 'Solo se pueden eliminar reparaciones entregadas. Estado actual: ' || :OLD.estado || '.')`. Prueba: (a) intenta borrar la reparación 5001 (estado `'PENDIENTE'`) — debe fallar; (b) borra la reparación 5004 (estado `'ENTREGADO'`) — debe funcionar. Haz `ROLLBACK` tras la segunda prueba para conservar los datos.

5. Crea dos disparadores sobre `U17_REPARACIONES`:
   - `trg_orden_instruccion`: `AFTER UPDATE`, **disparador de instrucción** (sin `FOR EACH ROW`), que imprime `'[A] INSTRUCCIÓN — trigger de instrucción ejecutado.'`.
   - `trg_orden_fila`: `AFTER UPDATE FOR EACH ROW`, que imprime `'[B] FILA — fila procesada: ' || :OLD.id_rep`.
   
   Ejecuta `UPDATE U17_REPARACIONES SET coste = coste WHERE estado = 'PENDIENTE'` (afecta a las reparaciones 5001 y 5005). Observa en la salida de `DBMS_OUTPUT` cuántas veces aparece el mensaje `[A]` y cuántas el mensaje `[B]` y en qué orden. Explica el resultado en un comentario. A continuación, consulta `USER_TRIGGERS` filtrando por `table_name = 'U17_REPARACIONES'` para ver el estado de todos los disparadores creados hasta ahora.


---


## Criterios de entrega

1. Entrega un único fichero `.sql`.
2. Añade al principio del fichero: `-- Autor: Nombre Apellido`.
3. Numera cada ejercicio con un comentario antes de cada sentencia o bloque: `-- Ejercicio I.1`, `-- Ejercicio IV.3`, etc.
4. El fichero debe poder ejecutarse en Oracle 21c **sin errores de compilación**. Los errores de ejecución controlados (excepciones capturadas o `RAISE_APPLICATION_ERROR` esperados) son correctos y forman parte del enunciado.
5. Los ejercicios marcados como *«Solo escritura»* deben incluirse en el fichero como comentarios de bloque `/* ... */` precedidos de su etiqueta.
6. Añade una línea en blanco entre ejercicios consecutivos y usa comentarios para separar visualmente las secciones lógicas dentro de cada bloque PL/SQL.
