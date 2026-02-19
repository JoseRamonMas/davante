# Ejercicios de clase: Consultas multitabla, vistas y transacciones

Batería de ejercicios prácticos para los temas **11 (Consultas multitabla y
subconsultas)**, **13 (Vistas y DCL)** y **14 (Transacciones)**.

Trabajaremos sobre el mismo entorno de la empresa de software de temas
anteriores, ampliado con una nueva tabla `SOFT_ASIGNACIONES` que vincula
empleados con proyectos.

## Parte 1: Preparación del entorno

> **Importante:** Vuelve a ejecutar este script completo para reiniciar los
> datos antes de cada bloque.

```sql
-- BORRADO (de tabla hija a padre para respetar las FK)
DROP TABLE SOFT_ASIGNACIONES  CASCADE CONSTRAINTS;
DROP TABLE SOFT_EMPLEADOS     CASCADE CONSTRAINTS;
DROP TABLE SOFT_DEPARTAMENTOS CASCADE CONSTRAINTS;
DROP TABLE SOFT_PROYECTOS     CASCADE CONSTRAINTS;
DROP TABLE SOFT_CLIENTES      CASCADE CONSTRAINTS;

-- 1. DEPARTAMENTOS
CREATE TABLE SOFT_DEPARTAMENTOS (
    ID_DEP NUMBER(2)    PRIMARY KEY,
    NOMBRE VARCHAR2(30) NOT NULL,
    CIUDAD VARCHAR2(30)
);

-- 2. EMPLEADOS
CREATE TABLE SOFT_EMPLEADOS (
    ID_EMP     NUMBER(4)    PRIMARY KEY,
    NOMBRE     VARCHAR2(30),
    APELLIDO   VARCHAR2(30),
    OFICIO     VARCHAR2(20),
    FECHA_ALTA DATE,
    SALARIO    NUMBER(7,2),
    COMISION   NUMBER(7,2),
    ID_DEP     NUMBER(2),
    CONSTRAINT FK_DEP FOREIGN KEY (ID_DEP) REFERENCES SOFT_DEPARTAMENTOS(ID_DEP)
);

-- 3. PROYECTOS
CREATE TABLE SOFT_PROYECTOS (
    COD_PROY     VARCHAR2(5)  PRIMARY KEY,
    NOMBRE_PROY  VARCHAR2(40),
    PRESUPUESTO  NUMBER(10,2),
    FECHA_INICIO DATE,
    FECHA_FIN    DATE,
    CIUDAD_PROY  VARCHAR2(30)
);

-- 4. CLIENTES
CREATE TABLE SOFT_CLIENTES (
    ID_CLIENTE     NUMBER(3)    PRIMARY KEY,
    NOMBRE_EMPRESA VARCHAR2(50),
    CONTACTO       VARCHAR2(50),
    TELEFONO       VARCHAR2(15),
    REGION         VARCHAR2(20)
);

-- 5. ASIGNACIONES (NUEVA — vincula empleados con proyectos)
CREATE TABLE SOFT_ASIGNACIONES (
    ID_EMP   NUMBER(4)   REFERENCES SOFT_EMPLEADOS(ID_EMP),
    COD_PROY VARCHAR2(5) REFERENCES SOFT_PROYECTOS(COD_PROY),
    HORAS    NUMBER(4),
    CONSTRAINT PK_ASIG PRIMARY KEY (ID_EMP, COD_PROY)
);

-- ============================================================
-- INSERCIONES
-- ============================================================

-- Departamentos
-- El dpto 40 (RH) no tiene empleados -> útil para RIGHT JOIN
INSERT INTO SOFT_DEPARTAMENTOS VALUES (10, 'DESARROLLO', 'MADRID');
INSERT INTO SOFT_DEPARTAMENTOS VALUES (20, 'SISTEMAS',   'BARCELONA');
INSERT INTO SOFT_DEPARTAMENTOS VALUES (30, 'VENTAS',     'SEVILLA');
INSERT INTO SOFT_DEPARTAMENTOS VALUES (40, 'RH',         'MADRID');

-- Empleados
-- El empleado 1009 no tiene departamento (id_dep NULL) -> útil para LEFT JOIN
INSERT INTO SOFT_EMPLEADOS VALUES (1001,'JUAN',  'PEREZ',   'PROGRAMADOR',  TO_DATE('15/01/2020','DD/MM/YYYY'), 2200, NULL, 10);
INSERT INTO SOFT_EMPLEADOS VALUES (1002,'ANA',   'GARCIA',  'ANALISTA',     TO_DATE('01/03/2018','DD/MM/YYYY'), 3100, NULL, 10);
INSERT INTO SOFT_EMPLEADOS VALUES (1003,'LUIS',  'LOPEZ',   'VENDEDOR',     TO_DATE('10/02/2021','DD/MM/YYYY'), 1500,  400, 30);
INSERT INTO SOFT_EMPLEADOS VALUES (1004,'MARTA', 'SANCHEZ', 'DIRECTOR',     TO_DATE('05/09/2015','DD/MM/YYYY'), 4500, NULL, 10);
INSERT INTO SOFT_EMPLEADOS VALUES (1005,'PEDRO', 'RUIZ',    'VENDEDOR',     TO_DATE('20/11/2022','DD/MM/YYYY'), 1400,  200, 30);
INSERT INTO SOFT_EMPLEADOS VALUES (1006,'SOFIA', 'ALVAREZ', 'ADM_SISTEMAS', TO_DATE('12/12/2019','DD/MM/YYYY'), 2100, NULL, 20);
INSERT INTO SOFT_EMPLEADOS VALUES (1007,'CARLOS','MUNOZ',   'PROGRAMADOR',  TO_DATE('25/06/2023','DD/MM/YYYY'), 1900, NULL, 10);
INSERT INTO SOFT_EMPLEADOS VALUES (1008,'LAURA', 'GIL',     'BECARIO',      TO_DATE('01/01/2024','DD/MM/YYYY'),  800, NULL, 20);
INSERT INTO SOFT_EMPLEADOS VALUES (1009,'DAVID', 'MORA',    'CONSULTOR',    TO_DATE('10/10/2023','DD/MM/YYYY'), 2800, NULL, NULL);

-- Proyectos
-- El proyecto P05 no tiene asignaciones -> útil para RIGHT JOIN con asignaciones
INSERT INTO SOFT_PROYECTOS VALUES ('P01','MIGRACION CLOUD',     50000, TO_DATE('01/01/2023','DD/MM/YYYY'), TO_DATE('30/06/2023','DD/MM/YYYY'), 'MADRID');
INSERT INTO SOFT_PROYECTOS VALUES ('P02','APP MOVIL VENTAS',    25000, TO_DATE('15/03/2023','DD/MM/YYYY'), NULL,                               'SEVILLA');
INSERT INTO SOFT_PROYECTOS VALUES ('P03','WEB CORPORATIVA',     12000, TO_DATE('01/09/2023','DD/MM/YYYY'), TO_DATE('01/12/2023','DD/MM/YYYY'), 'BILBAO');
INSERT INTO SOFT_PROYECTOS VALUES ('P04','ERP INTERNO',        150000, TO_DATE('01/02/2020','DD/MM/YYYY'), NULL,                               'BARCELONA');
INSERT INTO SOFT_PROYECTOS VALUES ('P05','AUDITORIA SEGURIDAD',  8000, TO_DATE('10/01/2024','DD/MM/YYYY'), TO_DATE('20/01/2024','DD/MM/YYYY'), 'MADRID');

-- Clientes
INSERT INTO SOFT_CLIENTES VALUES (1,'TECH SOLUTIONS SL','Juan M.',   '600111222','NORTE');
INSERT INTO SOFT_CLIENTES VALUES (2,'ALIMENTOS SUR',    'Maria T.',  '600333444','SUR');
INSERT INTO SOFT_CLIENTES VALUES (3,'BANCO CENTRAL',    'Roberto F.','911222333','CENTRO');

-- Asignaciones
-- Empleados 1005, 1008 y 1009 NO tienen asignaciones -> útil para LEFT JOIN
-- Proyecto P05 NO tiene asignaciones -> útil para RIGHT JOIN
INSERT INTO SOFT_ASIGNACIONES VALUES (1001,'P01',120);
INSERT INTO SOFT_ASIGNACIONES VALUES (1001,'P04', 80);
INSERT INTO SOFT_ASIGNACIONES VALUES (1002,'P01',200);
INSERT INTO SOFT_ASIGNACIONES VALUES (1002,'P04',160);
INSERT INTO SOFT_ASIGNACIONES VALUES (1004,'P04', 40);
INSERT INTO SOFT_ASIGNACIONES VALUES (1006,'P04', 90);
INSERT INTO SOFT_ASIGNACIONES VALUES (1007,'P02',110);
INSERT INTO SOFT_ASIGNACIONES VALUES (1007,'P03', 75);
INSERT INTO SOFT_ASIGNACIONES VALUES (1003,'P02', 50);

COMMIT;
```


---

## Parte 2: Batería de ejercicios

### Bloque 1: JOINs (Consultas multitabla)

*Objetivo: Combinar datos de varias tablas con distintos tipos de composición.*

**1. INNER JOIN básico**
Muestra el nombre y apellido de cada empleado junto con el nombre de su
departamento. Usa `INNER JOIN`.

<details>
<summary>Ver solución</summary>

```sql
SELECT e.NOMBRE, e.APELLIDO, d.NOMBRE AS DEPARTAMENTO
FROM SOFT_EMPLEADOS e
INNER JOIN SOFT_DEPARTAMENTOS d ON e.ID_DEP = d.ID_DEP;
```

</details>

---

**2. INNER JOIN con filtro**
Repite la consulta anterior, pero muestra solo los empleados cuyo departamento
sea `'DESARROLLO'`.

<details>
<summary>Ver solución</summary>

```sql
SELECT e.NOMBRE, e.APELLIDO, d.NOMBRE AS DEPARTAMENTO
FROM SOFT_EMPLEADOS e
INNER JOIN SOFT_DEPARTAMENTOS d ON e.ID_DEP = d.ID_DEP
WHERE d.NOMBRE = 'DESARROLLO';
```

</details>

---

**3. INNER JOIN con 3 tablas**
Muestra el nombre del empleado, el nombre del proyecto al que está asignado y
las horas dedicadas. Relaciona `SOFT_EMPLEADOS`, `SOFT_ASIGNACIONES` y
`SOFT_PROYECTOS`.

<details>
<summary>Ver solución</summary>

```sql
SELECT e.NOMBRE, e.APELLIDO, p.NOMBRE_PROY, a.HORAS
FROM SOFT_EMPLEADOS e
INNER JOIN SOFT_ASIGNACIONES a ON e.ID_EMP   = a.ID_EMP
INNER JOIN SOFT_PROYECTOS    p ON a.COD_PROY = p.COD_PROY;
```

</details>

---

**4. NATURAL JOIN**
Usa `NATURAL JOIN` entre `SOFT_ASIGNACIONES` y `SOFT_PROYECTOS` para mostrar
el nombre del proyecto y las horas de cada asignación.

> *Nota: Estas dos tablas solo comparten la columna `COD_PROY`, por lo que
> `NATURAL JOIN` funciona correctamente. Fíjate en que entre `SOFT_EMPLEADOS`
> y `SOFT_DEPARTAMENTOS` no podría usarse directamente porque ambas tienen una
> columna llamada `NOMBRE`, lo que provocaría resultados incorrectos.*

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE_PROY, HORAS
FROM SOFT_ASIGNACIONES
NATURAL JOIN SOFT_PROYECTOS;
```

</details>

---

**5. LEFT JOIN — empleados sin departamento**
Muestra TODOS los empleados, incluido el que no tiene departamento asignado,
junto con el nombre de su departamento. Los que no tienen departamento deben
aparecer con `NULL` en ese campo.

<details>
<summary>Ver solución</summary>

```sql
SELECT e.NOMBRE, e.APELLIDO, d.NOMBRE AS DEPARTAMENTO
FROM SOFT_EMPLEADOS e
LEFT JOIN SOFT_DEPARTAMENTOS d ON e.ID_DEP = d.ID_DEP;

-- Observa: DAVID MORA aparece con NULL en DEPARTAMENTO
```

</details>

---

**6. LEFT JOIN — filtrar solo los que no tienen departamento**
Usando `LEFT JOIN`, muestra únicamente los empleados que NO tienen ningún
departamento asignado. *Pista: tras un LEFT JOIN, las filas "huérfanas" tienen
NULL en las columnas de la tabla derecha.*

<details>
<summary>Ver solución</summary>

```sql
SELECT e.NOMBRE, e.APELLIDO
FROM SOFT_EMPLEADOS e
LEFT JOIN SOFT_DEPARTAMENTOS d ON e.ID_DEP = d.ID_DEP
WHERE d.ID_DEP IS NULL;
```

</details>

---

**7. RIGHT JOIN — departamentos sin empleados**
Muestra TODOS los departamentos aunque no tengan empleados. Los departamentos
sin empleados deben aparecer con `NULL` en nombre y apellido del empleado.

<details>
<summary>Ver solución</summary>

```sql
SELECT e.NOMBRE, e.APELLIDO, d.NOMBRE AS DEPARTAMENTO
FROM SOFT_EMPLEADOS e
RIGHT JOIN SOFT_DEPARTAMENTOS d ON e.ID_DEP = d.ID_DEP;

-- Observa: el departamento RH (40) aparece con NULL en NOMBRE y APELLIDO
```

</details>

---

**8. FULL OUTER JOIN**
Muestra todos los empleados y todos los departamentos en un solo resultado,
aunque no estén relacionados entre sí.

<details>
<summary>Ver solución</summary>

```sql
SELECT e.NOMBRE, e.APELLIDO, d.NOMBRE AS DEPARTAMENTO
FROM SOFT_EMPLEADOS e
FULL OUTER JOIN SOFT_DEPARTAMENTOS d ON e.ID_DEP = d.ID_DEP;

-- Verás tanto a DAVID MORA (sin dpto) como al dpto RH (sin empleados)
```

</details>

---

**9. LEFT JOIN con agrupación — proyectos sin asignaciones**
Muestra todos los proyectos y cuántas asignaciones tiene cada uno. Los
proyectos sin asignaciones deben mostrar `0`.

<details>
<summary>Ver solución</summary>

```sql
SELECT p.NOMBRE_PROY, COUNT(a.ID_EMP) AS NUM_ASIGNACIONES
FROM SOFT_PROYECTOS p
LEFT JOIN SOFT_ASIGNACIONES a ON p.COD_PROY = a.COD_PROY
GROUP BY p.NOMBRE_PROY;

-- COUNT(a.ID_EMP) ignora los NULL, devolviendo 0 para P05
```

</details>

---

**10. JOIN con agrupación y ORDER BY**
Muestra el nombre de cada proyecto y la suma total de horas asignadas en él.
Ordena de mayor a menor. Incluye solo los proyectos que tengan al menos
una asignación.

<details>
<summary>Ver solución</summary>

```sql
SELECT p.NOMBRE_PROY, SUM(a.HORAS) AS TOTAL_HORAS
FROM SOFT_PROYECTOS p
INNER JOIN SOFT_ASIGNACIONES a ON p.COD_PROY = a.COD_PROY
GROUP BY p.NOMBRE_PROY
ORDER BY TOTAL_HORAS DESC;
```

</details>

---

### Bloque 2: Subconsultas

*Objetivo: Anidar consultas para filtrar o calcular datos de forma dinámica.*

**1. Subconsulta escalar — comparar con un valor concreto**
Muestra el nombre, apellido y salario de los empleados que ganan MÁS que
`'JUAN'` `'PEREZ'`.

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE, APELLIDO, SALARIO
FROM SOFT_EMPLEADOS
WHERE SALARIO > (
    SELECT SALARIO FROM SOFT_EMPLEADOS
    WHERE NOMBRE = 'JUAN' AND APELLIDO = 'PEREZ'
);
```

</details>

---

**2. Subconsulta escalar con función de grupo**
Muestra los empleados cuyo salario supera la media salarial de toda la empresa.

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE, APELLIDO, SALARIO
FROM SOFT_EMPLEADOS
WHERE SALARIO > (SELECT AVG(SALARIO) FROM SOFT_EMPLEADOS);
```

</details>

---

**3. Subconsulta con IN**
Muestra el nombre y apellido de los empleados que están asignados a algún
proyecto (que aparecen en `SOFT_ASIGNACIONES`).

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE, APELLIDO
FROM SOFT_EMPLEADOS
WHERE ID_EMP IN (SELECT ID_EMP FROM SOFT_ASIGNACIONES);
```

</details>

---

**4. Subconsulta con NOT IN**
Muestra los empleados que NO están asignados a ningún proyecto.

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE, APELLIDO
FROM SOFT_EMPLEADOS
WHERE ID_EMP NOT IN (SELECT ID_EMP FROM SOFT_ASIGNACIONES);
```

</details>

---

**5. Subconsulta con ANY**
Muestra los empleados cuyo salario es mayor que el salario de AL MENOS UNO
de los empleados del departamento 30 (VENTAS).
*Recuerda: `> ANY` equivale a "mayor que el mínimo del conjunto".*

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE, APELLIDO, SALARIO
FROM SOFT_EMPLEADOS
WHERE SALARIO > ANY (
    SELECT SALARIO FROM SOFT_EMPLEADOS WHERE ID_DEP = 30
);
```

</details>

---

**6. Subconsulta con ALL**
Muestra los empleados cuyo salario supera al salario de TODOS los empleados
del departamento 30 (VENTAS).
*Recuerda: `> ALL` equivale a "mayor que el máximo del conjunto".*

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE, APELLIDO, SALARIO
FROM SOFT_EMPLEADOS
WHERE SALARIO > ALL (
    SELECT SALARIO FROM SOFT_EMPLEADOS WHERE ID_DEP = 30
);
```

</details>

---

**7. Subconsulta con EXISTS**
Muestra los proyectos que tienen al menos una asignación registrada. Usa
`EXISTS` con una subconsulta correlacionada.

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE_PROY
FROM SOFT_PROYECTOS p
WHERE EXISTS (
    SELECT 1 FROM SOFT_ASIGNACIONES a
    WHERE a.COD_PROY = p.COD_PROY
);
```

</details>

---

**8. Subconsulta con NOT EXISTS**
Muestra los proyectos que NO tienen ninguna asignación de empleados.

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE_PROY
FROM SOFT_PROYECTOS p
WHERE NOT EXISTS (
    SELECT 1 FROM SOFT_ASIGNACIONES a
    WHERE a.COD_PROY = p.COD_PROY
);
```

</details>

---

**9. Subconsulta en FROM**
Calcula el salario máximo de cada departamento. Crea una subconsulta en el
`FROM` que agrupe por `ID_DEP` y luego únela con `SOFT_DEPARTAMENTOS` para
mostrar el nombre del departamento.

<details>
<summary>Ver solución</summary>

```sql
SELECT d.NOMBRE AS DEPARTAMENTO, maximos.SAL_MAX
FROM SOFT_DEPARTAMENTOS d
INNER JOIN (
    SELECT ID_DEP, MAX(SALARIO) AS SAL_MAX
    FROM SOFT_EMPLEADOS
    GROUP BY ID_DEP
) maximos ON d.ID_DEP = maximos.ID_DEP;
```

</details>

---

**10. Subconsulta en HAVING**
Muestra los departamentos cuya media salarial es superior a la media
salarial global de toda la empresa.
*Pista: la subconsulta escalar va dentro de la cláusula `HAVING`.*

<details>
<summary>Ver solución</summary>

```sql
SELECT ID_DEP, AVG(SALARIO) AS MEDIA_DEPTO
FROM SOFT_EMPLEADOS
GROUP BY ID_DEP
HAVING AVG(SALARIO) > (SELECT AVG(SALARIO) FROM SOFT_EMPLEADOS);
```

</details>

---

### Bloque 3: Operaciones de conjuntos

*Objetivo: Combinar o comparar los resultados de varias consultas SELECT.*

**1. UNION**
Obtén un listado de todas las ciudades donde hay departamentos O proyectos,
sin duplicados. Combina la columna `CIUDAD` de `SOFT_DEPARTAMENTOS` con
`CIUDAD_PROY` de `SOFT_PROYECTOS`.

<details>
<summary>Ver solución</summary>

```sql
SELECT CIUDAD
FROM SOFT_DEPARTAMENTOS
UNION
SELECT CIUDAD_PROY
FROM SOFT_PROYECTOS;
```

</details>

---

**2. UNION ALL**
Repite la consulta anterior con `UNION ALL`. Observa cuántas filas devuelve
ahora y explica por escrito la diferencia respecto a `UNION`.

<details>
<summary>Ver solución</summary>

```sql
SELECT CIUDAD      FROM SOFT_DEPARTAMENTOS
UNION ALL
SELECT CIUDAD_PROY FROM SOFT_PROYECTOS;

-- UNION elimina duplicados; UNION ALL los conserva.
-- Por eso UNION ALL devuelve más filas (MADRID aparece varias veces).
```

</details>

---

**3. INTERSECT**
¿En qué ciudades hay tanto un departamento de la empresa como un proyecto?
Usa `INTERSECT`.

<details>
<summary>Ver solución</summary>

```sql
SELECT CIUDAD      FROM SOFT_DEPARTAMENTOS
INTERSECT
SELECT CIUDAD_PROY FROM SOFT_PROYECTOS;
```

</details>

---

**4. MINUS**
¿En qué ciudades hay un departamento pero ningún proyecto? Usa `MINUS`.

<details>
<summary>Ver solución</summary>

```sql
SELECT CIUDAD      FROM SOFT_DEPARTAMENTOS
MINUS
SELECT CIUDAD_PROY FROM SOFT_PROYECTOS;
```

</details>

---

**5. UNION con columnas adicionales y ORDER BY**
Genera un listado unificado de "personas" que incluya:

- Los empleados con `OFICIO = 'PROGRAMADOR'`
(columnas: `NOMBRE`, `APELLIDO`, literal `'EMPLEADO'`).
- Los clientes de la región `'NORTE'`
(columnas: `NOMBRE_EMPRESA`, `CONTACTO`, literal `'CLIENTE'`).

Combínalos con `UNION` y ordena el resultado por la primera columna
ascendente. *Pista: el `ORDER BY` va solo al final y referencia columnas
del primer `SELECT`.*

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE, APELLIDO, 'EMPLEADO' AS TIPO
FROM SOFT_EMPLEADOS
WHERE OFICIO = 'PROGRAMADOR'
UNION
SELECT NOMBRE_EMPRESA, CONTACTO, 'CLIENTE'
FROM SOFT_CLIENTES
WHERE REGION = 'NORTE'
ORDER BY NOMBRE ASC;
```

</details>

---

### Bloque 4: Vistas (Tema 13)

*Objetivo: Crear, consultar, modificar y eliminar vistas.*

**1. CREATE VIEW básica**
Crea una vista llamada `V_EMP_DPTO` que muestre el `ID_EMP`, nombre, apellido,
oficio y nombre del departamento de cada empleado. Usa `LEFT JOIN` para que
aparezcan también los empleados sin departamento.

<details>
<summary>Ver solución</summary>

```sql
CREATE VIEW V_EMP_DPTO AS
SELECT e.ID_EMP, e.NOMBRE, e.APELLIDO, e.OFICIO,
       d.NOMBRE AS DEPARTAMENTO
FROM SOFT_EMPLEADOS e
LEFT JOIN SOFT_DEPARTAMENTOS d ON e.ID_DEP = d.ID_DEP;
```

</details>

---

**2. Consultar una vista**
Consulta la vista `V_EMP_DPTO` para ver únicamente los empleados con oficio
`'PROGRAMADOR'`. Observa que se usa exactamente igual que una tabla.

<details>
<summary>Ver solución</summary>

```sql
SELECT * FROM V_EMP_DPTO
WHERE OFICIO = 'PROGRAMADOR';
```

</details>

---

**3. CREATE OR REPLACE VIEW**
Modifica la vista `V_EMP_DPTO` usando `CREATE OR REPLACE VIEW` para incluir
también el `SALARIO` del empleado.

<details>
<summary>Ver solución</summary>

```sql
CREATE OR REPLACE VIEW V_EMP_DPTO AS
SELECT e.ID_EMP, e.NOMBRE, e.APELLIDO, e.OFICIO, e.SALARIO,
       d.NOMBRE AS DEPARTAMENTO
FROM SOFT_EMPLEADOS e
LEFT JOIN SOFT_DEPARTAMENTOS d ON e.ID_DEP = d.ID_DEP;
```

</details>

---

**4. Vista con subconsulta**
Crea una vista llamada `V_PROYECTOS_ACTIVOS` que muestre solo los proyectos
actualmente en curso (aquellos cuya `FECHA_FIN` es `NULL`). Incluye todas
las columnas del proyecto.

<details>
<summary>Ver solución</summary>

```sql
CREATE VIEW V_PROYECTOS_ACTIVOS AS
SELECT *
FROM SOFT_PROYECTOS
WHERE FECHA_FIN IS NULL;
```

</details>

---

**5. DROP VIEW**
Elimina ambas vistas: `V_EMP_DPTO` y `V_PROYECTOS_ACTIVOS`. Luego intenta
consultarlas para comprobar que ya no existen y anota qué error devuelve Oracle.

<details>
<summary>Ver solución</summary>

```sql
DROP VIEW V_EMP_DPTO;
DROP VIEW V_PROYECTOS_ACTIVOS;

-- Al intentar: SELECT * FROM V_EMP_DPTO;
-- Oracle devuelve ORA-00942: la tabla o vista no existe
```

</details>

---

### Bloque 5: DCL básico (Tema 13)

*Objetivo: Conocer los comandos GRANT y REVOKE para gestionar permisos.*

> ⚠️ Estos ejercicios son de **redacción**. Escribe las sentencias correctas
> pero no es necesario ejecutarlas si no dispones de permisos de administrador.

**1. GRANT sin propagación**
Escribe la sentencia que concede el privilegio `SELECT` sobre la tabla
`SOFT_EMPLEADOS` al usuario `BECARIO_01`, sin que este pueda re-conceder
ese permiso a otros.

<details>
<summary>Ver solución</summary>

```sql
GRANT SELECT ON SOFT_EMPLEADOS TO BECARIO_01;
-- Sin WITH GRANT OPTION: no puede propagar el permiso a otros usuarios.
```

</details>

---

**2. GRANT con propagación y REVOKE**
a) Escribe la sentencia que concede permisos de `SELECT` e `INSERT` sobre
`SOFT_PROYECTOS` al usuario `JEFE_PROYECTO`, permitiéndole además conceder
esos permisos a otros usuarios.

b) Escribe la sentencia para revocarle después esos privilegios.

<details>
<summary>Ver solución</summary>

```sql
-- a) Conceder con posibilidad de propagación
GRANT SELECT, INSERT ON SOFT_PROYECTOS TO JEFE_PROYECTO WITH GRANT OPTION;

-- b) Revocar
REVOKE SELECT, INSERT ON SOFT_PROYECTOS FROM JEFE_PROYECTO;
```

</details>

---

### Bloque 6: Transacciones (Tema 14)

*Objetivo: Controlar los cambios en la base de datos con COMMIT, ROLLBACK y
SAVEPOINT.*

> ⚠️ **Desactiva el AUTOCOMMIT antes de empezar este bloque.**
> - SQL Developer: Herramientas → Preferencias → Base de Datos → Avanzado
>   → desmarcar la casilla *Autocommit*.
> - SQL\*Plus / SQLcl: ejecuta `SET AUTOCOMMIT OFF;`
>
> Recuerda también ejecutar el script de inicialización.

---

**1. ROLLBACK básico**
a) Sube el salario de todos los empleados del departamento 30 (VENTAS) en
200 euros.

b) Comprueba el resultado con un `SELECT`.

c) Decide que fue un error y deshaz el cambio con `ROLLBACK`.

d) Verifica con otro `SELECT` que los salarios han vuelto a su valor original.

<details>
<summary>Ver solución</summary>

```sql
-- a) Actualización
UPDATE SOFT_EMPLEADOS
SET SALARIO = SALARIO + 200
WHERE ID_DEP = 30;

-- b) Comprobación
SELECT NOMBRE, SALARIO FROM SOFT_EMPLEADOS WHERE ID_DEP = 30;

-- c) Deshacer
ROLLBACK;

-- d) Verificación: LUIS vuelve a 1500 y PEDRO a 1400
SELECT NOMBRE, SALARIO FROM SOFT_EMPLEADOS WHERE ID_DEP = 30;
```

</details>

---

**2. COMMIT — confirmar y comprobar que no se puede deshacer**
a) Inserta un nuevo empleado: ID 2000, nombre `'TEST'`, oficio `'BECARIO'`,
salario 500, sin departamento.

b) Confirma el cambio con `COMMIT`.

c) Intenta deshacerlo con `ROLLBACK`. ¿Funciona? Explica por qué.

<details>
<summary>Ver solución</summary>

```sql
-- a) Inserción
INSERT INTO SOFT_EMPLEADOS (ID_EMP, NOMBRE, OFICIO, SALARIO)
VALUES (2000, 'TEST', 'BECARIO', 500);

-- b) Confirmar permanentemente
COMMIT;

-- c) Intentar deshacer
ROLLBACK;
-- No tiene efecto: el COMMIT ha hecho los cambios permanentes.
-- Un ROLLBACK posterior a un COMMIT no puede revertir esa transacción.
```

</details>

---

**3. SAVEPOINT — punto de guardado intermedio**
a) Inserta un departamento: ID 50, nombre `'MARKETING'`, ciudad `'VALENCIA'`.

b) Crea un punto de guardado: `SAVEPOINT SP1`.

c) Inserta otro departamento: ID 60, nombre `'LOGISTICA'`, ciudad `'ZARAGOZA'`.

d) Deshaz solo el segundo con `ROLLBACK TO SP1`.

e) Comprueba que el departamento 50 existe pero el 60 no.

f) Confirma definitivamente con `COMMIT`.

<details>
<summary>Ver solución</summary>

```sql
-- a)
INSERT INTO SOFT_DEPARTAMENTOS VALUES (50, 'MARKETING', 'VALENCIA');

-- b)
SAVEPOINT SP1;

-- c)
INSERT INTO SOFT_DEPARTAMENTOS VALUES (60, 'LOGISTICA', 'ZARAGOZA');

-- d)
ROLLBACK TO SP1;

-- e) Solo debe aparecer el ID 50
SELECT * FROM SOFT_DEPARTAMENTOS WHERE ID_DEP IN (50, 60);

-- f)
COMMIT;
```

</details>

---

> ✅ **Al terminar el bloque, vuelve a activar el AUTOCOMMIT:**
> - SQL Developer: Herramientas → Preferencias → Base de Datos → Avanzado
>   → marcar de nuevo la casilla *Autocommit*.
> - SQL\*Plus / SQLcl: ejecuta `SET AUTOCOMMIT ON;`
