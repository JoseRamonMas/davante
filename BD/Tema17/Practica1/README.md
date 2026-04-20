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

1. Crea un procedimiento `prc_info_reparacion(p_id_rep IN NUMBER, p_coste_actual OUT NUMBER)` que obtenga con `SELECT INTO` los campos `cliente`, `dispositivo` y `coste` de la reparación indicada en el parámetro de entrada. 
   - Imprime por pantalla una línea exacta a esta: `Reparación de [dispositivo] para el cliente [cliente].`
   - Asigna el coste obtenido al parámetro de salida `p_coste_actual`.
   - Captura la excepción `NO_DATA_FOUND` e imprime un mensaje indicando: `Error: La reparación no existe.`
   - Escribe un bloque anónimo que llame al procedimiento para la reparación 5002 (imprimiendo luego en el bloque el coste devuelto) y para la reparación 9999 (debe mostrar el mensaje de error).

2. Crea un procedimiento `prc_aplicar_descuento(p_id_rep IN NUMBER, p_descuento IN NUMBER)` que actualice el coste de una reparación restándole el descuento indicado en el parámetro.
   - **Antes** de ejecutar la actualización, evalúa el parámetro de descuento. Si `p_descuento < 0`, lanza una excepción usando `RAISE_APPLICATION_ERROR(-20101, 'El descuento no puede ser negativo.')`.
   - Si la validación es correcta, actualiza la tabla restando el descuento al coste actual y haz `COMMIT` al terminar.
   - Invoca el procedimiento en dos bloques anónimos distintos: el primero intentará aplicar un descuento de `-10` a la reparación 5001 (provocando la parada de ejecución por el error); el segundo aplicará un descuento de `15` a la reparación 5005 (operación válida).

---

## 4. Bloque II — Funciones almacenadas

*Recuerda ejecutar el script de inicialización antes de comenzar este bloque.*

1. Crea una función `fnc_nivel_reparacion(p_coste IN NUMBER) RETURN VARCHAR2` que evalúe el coste recibido por parámetro y devuelva una cadena de texto según las siguientes reglas:
   - `'BÁSICA'` si el coste es menor o igual a 50 €.
   - `'ESTÁNDAR'` si está estrictamente entre 51 € y 100 €.
   - `'COMPLEJA'` si supera los 100 €.
   - `'SIN COSTE'` si `p_coste IS NULL`.
   
   Esta función no necesita acceder a ninguna tabla, únicamente evalúa la variable de entrada.

2. Escribe una consulta `SELECT` sobre `U17_REPARACIONES` que muestre las columnas `id_rep`, `cliente`, `coste` y una cuarta columna calculada denominada `NIVEL_REPARACION`. El valor de esta última columna se obtendrá invocando directamente a tu función `fnc_nivel_reparacion` y pasándole la columna `coste`. Filtra la consulta mediante la cláusula `WHERE` para que solo aparezcan las reparaciones cuyo estado sea `'PENDIENTE'`.

---

## 5. Bloque III — Paquetes

*Recuerda ejecutar el script de inicialización antes de comenzar este bloque.*

1. Crea la **especificación** y el **cuerpo** del paquete `pkg_taller` con los siguientes elementos:
   - **Especificación:**
     - Una constante `C_ESTADO_FILTRO CONSTANT VARCHAR2(15) := 'EN_PROCESO'`.
     - La cabecera del procedimiento público `prc_reparaciones_tecnico(p_id_emp NUMBER)`.
   - **Cuerpo:**
     - La implementación de `prc_reparaciones_tecnico`. Este procedimiento debe utilizar un cursor `FOR` loop explícito o implícito para recorrer todas las reparaciones asignadas al empleado recibido por parámetro (`p_id_emp`) cuyo estado coincida exactamente con la constante `C_ESTADO_FILTRO`.
     - Dentro del bucle, imprime mediante `DBMS_OUTPUT` el ID de la reparación y el dispositivo en el formato: `[ID] - Dispositivo`.
   - **Prueba:** Escribe un bloque anónimo que invoque la ejecución de `pkg_taller.prc_reparaciones_tecnico(102);`.

---

## 6. Bloque IV — Disparadores DML

*Recuerda ejecutar el script de inicialización antes de comenzar este bloque.*

> Crea siempre los disparadores con `CREATE OR REPLACE TRIGGER`. Tras cada ejercicio, verifica el resultado con las sentencias de DML indicadas.

1. Crea un disparador de **fila** `trg_auditar_estado` que se active `AFTER UPDATE OF estado` sobre la tabla `U17_REPARACIONES` `FOR EACH ROW`. 
   - Por cada fila modificada, debe insertar un nuevo registro en `U17_AUDITORIA` con los siguientes valores: el ID de auditoría con `U17_SEQ_AUDIT.NEXTVAL`, `tabla_afectada = 'U17_REPARACIONES'`, `accion = 'UPDATE'`, `id_registro` con el valor de la reparación afectada, `campo = 'ESTADO'`, `valor_anterior` con el valor de `:OLD.estado` y `valor_nuevo` con el valor de `:NEW.estado`.
   - Pruébalo ejecutando: `UPDATE U17_REPARACIONES SET estado = 'TERMINADO' WHERE id_rep = 5006;` y comprueba mediante una consulta `SELECT` que la tabla de auditoría ha registrado correctamente el cambio.

2. Crea un disparador de fila `trg_proteger_borrado` que se active `BEFORE DELETE` sobre la tabla `U17_PRESUPUESTOS` `FOR EACH ROW`. 
   - Si el estado del presupuesto que se intenta eliminar (usando el pseudo-registro `:OLD`) es `'ACEPTADO'`, impide el borrado lanzando la excepción: `RAISE_APPLICATION_ERROR(-20200, 'No se puede borrar un presupuesto ya aceptado.')`.
   - Prueba el disparador en dos escenarios: (a) intenta ejecutar `DELETE FROM U17_PRESUPUESTOS WHERE id_presupuesto = 9001` (estado `'ACEPTADO'`, debe fallar); (b) ejecuta `DELETE FROM U17_PRESUPUESTOS WHERE id_presupuesto = 9006` (estado `'RECHAZADO'`, el borrado debe tener éxito). 
   - Haz `ROLLBACK` al terminar las pruebas para restaurar los datos.

---

## Criterios de entrega

1. Entrega un único fichero `.sql`.
2. Añade al principio del fichero: `-- Autor: Nombre Apellido`.
3. Numera cada ejercicio con un comentario antes de cada sentencia o bloque: `-- Ejercicio I.1`, `-- Ejercicio IV.2`, etc.
4. El fichero debe poder ejecutarse en Oracle 21c **sin errores de compilación**. Los errores de ejecución controlados (excepciones capturadas o `RAISE_APPLICATION_ERROR` esperados) son correctos y forman parte del enunciado.
5. Los ejercicios marcados como *«Solo escritura»* deben incluirse en el fichero como comentarios de bloque `/* ... */` precedidos de su etiqueta (en esta versión simplificada no hay, pero mantén el formato para futuros).
6. Añade una línea en blanco entre ejercicios consecutivos y usa comentarios para separar visualmente las secciones lógicas dentro de cada bloque PL/SQL.

