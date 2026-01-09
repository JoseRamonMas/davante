# Ejercicios Prácticos de SQL: Gestión de Proyectos de Software

Batería de ejercicios prácticos para los temas de **Tratamiento de datos (DML)**, **Consultas (SELECT)** y **Funciones**.

Para realizar estos ejercicios, trabajaremos sobre un supuesto real: una empresa de desarrollo de software.

## Parte 1: Preparación del entorno

Antes de comenzar, debéis ejecutar el siguiente script en vuestra base de datos Oracle. Este script crea las tablas necesarias (`SOFT_DEPARTAMENTOS`, `SOFT_EMPLEADOS`, `SOFT_PROYECTOS`, `SOFT_CLIENTES`) y carga los datos de prueba.

> **Importante:** Si queréis reiniciar los datos para volver al estado original (por ejemplo, al cambiar de tema), volved a ejecutar este script completo.

```sql
-- BORRADO DE TABLAS PREVIAS (Si existen)
DROP TABLE SOFT_EMPLEADOS CASCADE CONSTRAINTS;
DROP TABLE SOFT_DEPARTAMENTOS CASCADE CONSTRAINTS;
DROP TABLE SOFT_PROYECTOS CASCADE CONSTRAINTS;
DROP TABLE SOFT_CLIENTES CASCADE CONSTRAINTS;

-- 1. TABLA DEPARTAMENTOS
CREATE TABLE SOFT_DEPARTAMENTOS (
    ID_DEP NUMBER(2) PRIMARY KEY,
    NOMBRE VARCHAR2(30) NOT NULL,
    CIUDAD VARCHAR2(30)
);

-- 2. TABLA EMPLEADOS
CREATE TABLE SOFT_EMPLEADOS (
    ID_EMP NUMBER(4) PRIMARY KEY,
    NOMBRE VARCHAR2(30),
    APELLIDO VARCHAR2(30),
    OFICIO VARCHAR2(20),
    FECHA_ALTA DATE,
    SALARIO NUMBER(7,2),
    COMISION NUMBER(7,2),
    ID_DEP NUMBER(2), 
    CONSTRAINT FK_DEP FOREIGN KEY (ID_DEP) REFERENCES SOFT_DEPARTAMENTOS(ID_DEP)
);

-- 3. TABLA PROYECTOS
CREATE TABLE SOFT_PROYECTOS (
    COD_PROY VARCHAR2(5) PRIMARY KEY,
    NOMBRE_PROY VARCHAR2(40),
    PRESUPUESTO NUMBER(10,2),
    FECHA_INICIO DATE,
    FECHA_FIN DATE, -- Puede ser NULL si está en curso
    CIUDAD_PROY VARCHAR2(30)
);

-- 4. TABLA CLIENTES
CREATE TABLE SOFT_CLIENTES (
    ID_CLIENTE NUMBER(3) PRIMARY KEY,
    NOMBRE_EMPRESA VARCHAR2(50),
    CONTACTO VARCHAR2(50),
    TELEFONO VARCHAR2(15),
    REGION VARCHAR2(20)
);

-- INSERCIONES DE DATOS

-- Departamentos
INSERT INTO SOFT_DEPARTAMENTOS VALUES (10, 'DESARROLLO', 'MADRID');
INSERT INTO SOFT_DEPARTAMENTOS VALUES (20, 'SISTEMAS', 'BARCELONA');
INSERT INTO SOFT_DEPARTAMENTOS VALUES (30, 'VENTAS', 'SEVILLA');
INSERT INTO SOFT_DEPARTAMENTOS VALUES (40, 'RH', 'MADRID');

-- Empleados 
INSERT INTO SOFT_EMPLEADOS VALUES (1001, 'JUAN', 'PEREZ', 'PROGRAMADOR', TO_DATE('15/01/2020','DD/MM/YYYY'), 2200, NULL, 10);
INSERT INTO SOFT_EMPLEADOS VALUES (1002, 'Ana', 'GARCIA', 'ANALISTA', TO_DATE('01/03/2018','DD/MM/YYYY'), 3100, NULL, 10);
INSERT INTO SOFT_EMPLEADOS VALUES (1003, 'LUIS', 'Lopez', 'VENDEDOR', TO_DATE('10/02/2021','DD/MM/YYYY'), 1500, 400, 30);
INSERT INTO SOFT_EMPLEADOS VALUES (1004, 'Marta', 'SANCHEZ', 'DIRECTOR', TO_DATE('05/09/2015','DD/MM/YYYY'), 4500, NULL, 10);
INSERT INTO SOFT_EMPLEADOS VALUES (1005, 'PEDRO', 'Ruiz', 'VENDEDOR', TO_DATE('20/11/2022','DD/MM/YYYY'), 1400, 200, 30);
INSERT INTO SOFT_EMPLEADOS VALUES (1006, 'Sofia', 'ALVAREZ', 'ADM_SISTEMAS', TO_DATE('12/12/2019','DD/MM/YYYY'), 2100, NULL, 20);
INSERT INTO SOFT_EMPLEADOS VALUES (1007, 'Carlos', 'MUNOZ', 'PROGRAMADOR', TO_DATE('25/06/2023','DD/MM/YYYY'), 1900, NULL, 10);
INSERT INTO SOFT_EMPLEADOS VALUES (1008, 'Laura', 'GIL', 'BECARIO', TO_DATE('01/01/2024','DD/MM/YYYY'), 800, NULL, 20);

-- Proyectos
INSERT INTO SOFT_PROYECTOS VALUES ('P01', 'MIGRACION CLOUD', 50000, TO_DATE('01/01/2023','DD/MM/YYYY'), TO_DATE('30/06/2023','DD/MM/YYYY'), 'MADRID');
INSERT INTO SOFT_PROYECTOS VALUES ('P02', 'APP MOVIL VENTAS', 25000, TO_DATE('15/03/2023','DD/MM/YYYY'), NULL, 'SEVILLA'); 
INSERT INTO SOFT_PROYECTOS VALUES ('P03', 'WEB CORPORATIVA', 12000, TO_DATE('01/09/2023','DD/MM/YYYY'), TO_DATE('01/12/2023','DD/MM/YYYY'), 'BILBAO');
INSERT INTO SOFT_PROYECTOS VALUES ('P04', 'ERP INTERNO', 150000, TO_DATE('01/02/2020','DD/MM/YYYY'), NULL, 'BARCELONA'); 
INSERT INTO SOFT_PROYECTOS VALUES ('P05', 'AUDITORIA SEGURIDAD', 8000, TO_DATE('10/01/2024','DD/MM/YYYY'), TO_DATE('20/01/2024','DD/MM/YYYY'), 'MADRID');

-- Clientes
INSERT INTO SOFT_CLIENTES VALUES (1, 'TECH SOLUTIONS SL', 'Juan M.', '600111222', 'NORTE');
INSERT INTO SOFT_CLIENTES VALUES (2, 'ALIMENTOS SUR', 'Maria T.', '600333444', 'SUR');
INSERT INTO SOFT_CLIENTES VALUES (3, 'BANCO CENTRAL', 'Roberto F.', '911222333', 'CENTRO');

COMMIT;

```

---

## Parte 2: Batería de Ejercicios

### Bloque 1: Tratamiento de datos (DML)

*Objetivo: Insertar, modificar, borrar y controlar transacciones.*

**1. Insertar departamento**
Inserta un nuevo departamento en la tabla `SOFT_DEPARTAMENTOS` con ID 50, nombre 'MARKETING' y ciudad 'VALENCIA'.

<details>
<summary>Ver solución</summary>

```sql
INSERT INTO SOFT_DEPARTAMENTOS VALUES (50, 'MARKETING', 'VALENCIA');

-- Alternativa: Es la "buena práctica" para evitar errores

INSERT INTO SOFT_DEPARTAMENTOS (ID_DEP, NOMBRE, CIUDAD)
VALUES (50, 'MARKETING', 'VALENCIA');

```

</details>

**2. Insertar empleado simple**
Inserta un empleado en `SOFT_EMPLEADOS` con ID 1009, nombre 'DAVID', puesto 'BECARIO' y salario 900. Deja el resto de campos nulos.

<details>
<summary>Ver solución</summary>

```sql
INSERT INTO SOFT_EMPLEADOS
VALUES (1009, 'DAVID', NULL, 'BECARIO', NULL, 900, NULL, NULL);

-- Opción recomendada: especificando columnas

INSERT INTO SOFT_EMPLEADOS (ID_EMP, NOMBRE, OFICIO, SALARIO)
VALUES (1009, 'DAVID', 'BECARIO', 900);

```

</details>

**3. Insertar con columnas específicas**
Inserta un cliente en `SOFT_CLIENTES` especificando solo el ID (4) y el Nombre ('LOGISTICA RAPIDA').

<details>
<summary>Ver solución</summary>

```sql
INSERT INTO SOFT_CLIENTES (ID_CLIENTE, NOMBRE_EMPRESA)
VALUES (4, 'LOGISTICA RAPIDA');

```

</details>

**4. Insertar fecha actual**
Inserta un nuevo proyecto en `SOFT_PROYECTOS` con código 'P06', nombre 'API REST', presupuesto 5000 y usa la función `SYSDATE` para la fecha de inicio.

<details>
<summary>Ver solución</summary>

```sql
INSERT INTO SOFT_PROYECTOS (COD_PROY, NOMBRE_PROY, PRESUPUESTO, FECHA_INICIO, CIUDAD_PROY)
VALUES ('P06', 'API REST', 5000, SYSDATE, 'MADRID');

```

</details>

**5. Insertar violando PK (Error esperado)**
Intenta insertar un departamento con ID 10 (ya existente). Observa el error.

<details>
<summary>Ver solución</summary>

```sql
INSERT INTO SOFT_DEPARTAMENTOS VALUES (10, 'REPE', 'REPE');
-- Resultado: Error ORA-00001 (Restricción única violada)

```

</details>

**6. Update simple**
Modifica la ciudad del departamento 'VENTAS' (ID 30) para que ahora sea 'MALAGA'.

<details>
<summary>Ver solución</summary>

```sql
UPDATE SOFT_DEPARTAMENTOS
SET CIUDAD = 'MALAGA'
WHERE ID_DEP = 30;

```

</details>

**7. Update masivo con cálculo**
Debido a la inflación, sube el salario de **todos** los empleados un 5% (multiplica salario por 1.05).

<details>
<summary>Ver solución</summary>

```sql
UPDATE SOFT_EMPLEADOS
SET SALARIO = SALARIO * 1.05;

-- Alternativamente otras formas de calcularlo.

UPDATE SOFT_EMPLEADOS
SET SALARIO = SALARIO + (SALARIO * 0.05);

```

</details>

**8. Update condicional**
Asigna una comisión de 100 euros a todos los empleados cuyo oficio sea 'PROGRAMADOR'.

<details>
<summary>Ver solución</summary>

```sql
UPDATE SOFT_EMPLEADOS
SET COMISION = 100
WHERE OFICIO = 'PROGRAMADOR';

```

</details>

**9. Update con nulos**
El proyecto 'P03' se ha reactivado. Pon su `FECHA_FIN` a `NULL` para indicar que no ha terminado.

<details>
<summary>Ver solución</summary>

```sql
UPDATE SOFT_PROYECTOS
SET FECHA_FIN = NULL
WHERE COD_PROY = 'P03';

```

</details>

**10. Delete condicional**
Borra al empleado con ID 1008 (Laura GIL).

<details>
<summary>Ver solución</summary>

```sql
DELETE FROM SOFT_EMPLEADOS
WHERE ID_EMP = 1008;

```

</details>

**11. Delete por rango**
Borra todos los proyectos cuyo presupuesto sea inferior a 10.000 euros.

<details>
<summary>Ver solución</summary>

```sql
DELETE FROM SOFT_PROYECTOS
WHERE PRESUPUESTO < 10000;

```

</details>

**12. Delete masivo (Peligro)**
Borra todos los empleados del departamento 40 (RH).

<details>
<summary>Ver solución</summary>

```sql
DELETE FROM SOFT_EMPLEADOS
WHERE ID_DEP = 40;

```

</details>

**13. Integridad referencial (Error esperado)**
Intenta borrar el departamento 10 (Desarrollo). ¿Qué ocurre y por qué?

<details>
<summary>Ver solución</summary>

```sql
DELETE FROM SOFT_DEPARTAMENTOS WHERE ID_DEP = 10;
-- Error ORA-02292: No se puede borrar el padre si tiene hijos (empleados) asociados.

```

</details>

**14. Integridad referencial en inserción (Error esperado)**
Intenta insertar un nuevo empleado con ID 2000 y nombre 'ERROR', asignándole el departamento número 99. ¿Qué ocurre?

<details>
<summary>Ver solución</summary>

```sql
INSERT INTO SOFT_EMPLEADOS (ID_EMP, NOMBRE, ID_DEP) 
VALUES (2000, 'ERROR', 99);
-- Resultado: Error ORA-02291 (Restricción de integridad violada - no se encontró la clave padre).
-- El departamento 99 no existe en la tabla SOFT_DEPARTAMENTOS.

```

</details>

**15. Restricción NOT NULL (Error esperado)**
Intenta insertar un nuevo departamento con ID 60 y ciudad 'LUGO', pero sin especificar un nombre.

<details>
<summary>Ver solución</summary>

```sql
INSERT INTO SOFT_DEPARTAMENTOS (ID_DEP, CIUDAD) 
VALUES (60, 'LUGO');
-- Resultado: Error ORA-01400 (No se puede realizar una inserción NULL).
-- La columna NOMBRE en SOFT_DEPARTAMENTOS tiene la restricción NOT NULL.

```

</details>

**16. Update con lógica booleana compleja**
La empresa aplica una política de retención de talento: Sube el salario un **10%** a los empleados que cumplan **una** de estas dos condiciones:

1. Son 'PROGRAMADOR' y ganan menos de 2000.
2. Son 'ANALISTA' del departamento 10.
*Pista: Cuidado con los paréntesis en el `WHERE`.*

<details>
<summary>Ver solución</summary>

```sql
UPDATE SOFT_EMPLEADOS
SET SALARIO = SALARIO * 1.10
WHERE (OFICIO = 'PROGRAMADOR' AND SALARIO < 2000)
   OR (OFICIO = 'ANALISTA' AND ID_DEP = 10);

```

</details>

**17. Delete por patrón de texto**
Borra a los clientes cuyo nombre de contacto **no** incluya un apellido (es decir, que no contenga ningún espacio en blanco en la columna `CONTACTO`) y que además sean de la región 'CENTRO'.

<details>
<summary>Ver solución</summary>

```sql
DELETE FROM SOFT_CLIENTES
WHERE CONTACTO NOT LIKE '% %' 
  AND REGION = 'CENTRO';

```

</details>

**18. Insert con formato de fecha y hora**
Inserta un proyecto urgente con código 'P99', nombre 'HOTFIX', presupuesto 0. Lo importante es que la fecha de inicio debe registrarse exactamente como '01/01/2024' a las '08:00:00' de la mañana.
*Pista: Usa `TO_DATE` con máscara de hora.*

<details>
<summary>Ver solución</summary>

```sql
INSERT INTO SOFT_PROYECTOS (COD_PROY, NOMBRE_PROY, PRESUPUESTO, FECHA_INICIO, CIUDAD_PROY)
VALUES ('P99', 'HOTFIX', 0, TO_DATE('01/01/2024 08:00:00', 'DD/MM/YYYY HH24:MI:SS'), 'MADRID');

```

</details>

---

### Bloque 2: La sentencia SELECT

*Objetivo: Consultas básicas, filtrado, ordenación y agrupación.*

**1. Select total**
Muestra toda la información de la tabla `SOFT_EMPLEADOS`.

<details>
<summary>Ver solución</summary>

```sql
SELECT * FROM SOFT_EMPLEADOS;

```

</details>

**2. Proyección**
Muestra solo el nombre, apellido y salario de los empleados.

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE, APELLIDO, SALARIO
FROM SOFT_EMPLEADOS;

```

</details>

**3. Alias**
Muestra el nombre del proyecto y su presupuesto. La columna del presupuesto debe aparecer con el encabezado "Inversión en Euros".

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE_PROY, PRESUPUESTO "Inversión en Euros"
FROM SOFT_PROYECTOS;

-- Alternativa: la palabra clave "AS" es opcional en Oracle pero recomendada

SELECT NOMBRE_PROY, PRESUPUESTO AS "Inversión en Euros"
FROM SOFT_PROYECTOS;

```

</details>

**4. Distinct**
Muestra los distintos oficios que existen en la empresa (sin duplicados).

<details>
<summary>Ver solución</summary>

```sql
SELECT DISTINCT OFICIO
FROM SOFT_EMPLEADOS;

```

</details>

**5. Where (igualdad)**
Lista los empleados que trabajan en el departamento 20.

<details>
<summary>Ver solución</summary>

```sql
SELECT * FROM SOFT_EMPLEADOS
WHERE ID_DEP = 20;

```

</details>

**6. Where (rango)**
Muestra los proyectos cuyo presupuesto esté entre 10.000 y 40.000 euros.

<details>
<summary>Ver solución</summary>

```sql
SELECT * FROM SOFT_PROYECTOS
WHERE PRESUPUESTO BETWEEN 10000 AND 40000;

```

</details>

**7. Where (lista)**
Muestra los empleados cuyo oficio sea 'VENDEDOR' o 'ANALISTA' (Usa `IN`).

<details>
<summary>Ver solución</summary>

```sql
SELECT * FROM SOFT_EMPLEADOS
WHERE OFICIO IN ('VENDEDOR', 'ANALISTA');

```

</details>

**8. Where (patrones)**
Muestra los empleados cuyo nombre empiece por la letra 'M'.

<details>
<summary>Ver solución</summary>

```sql
SELECT * FROM SOFT_EMPLEADOS
WHERE NOMBRE LIKE 'M%';

```

</details>

**9. Order By (ascendente)**
Lista los empleados ordenados alfabéticamente por apellido.

<details>
<summary>Ver solución</summary>

```sql
SELECT * FROM SOFT_EMPLEADOS
ORDER BY APELLIDO ASC;

```

</details>

**10. Order By (descendente/múltiple)**
Muestra los empleados ordenados por departamento, y dentro de cada departamento, por salario de mayor a menor.

<details>
<summary>Ver solución</summary>

```sql
SELECT * FROM SOFT_EMPLEADOS
ORDER BY ID_DEP ASC, SALARIO DESC;

```

</details>

**11. Is Null**
Muestra los proyectos que están actualmente en curso (aquellos donde `FECHA_FIN` es nula).

<details>
<summary>Ver solución</summary>

```sql
SELECT * FROM SOFT_PROYECTOS
WHERE FECHA_FIN IS NULL; -- FECHA_FIN = NULL -> INCORRECTO

```

</details>

**12. Group By (count)**
Muestra cuántos empleados hay en cada departamento.

<details>
<summary>Ver solución</summary>

```sql
SELECT ID_DEP, COUNT(*)
FROM SOFT_EMPLEADOS
GROUP BY ID_DEP;

```

</details>

**13. Group By (avg)**
Calcula el salario medio por cada oficio.

<details>
<summary>Ver solución</summary>

```sql
SELECT OFICIO, AVG(SALARIO)
FROM SOFT_EMPLEADOS
GROUP BY OFICIO;

```

</details>

**14. Having**
Muestra los departamentos (ID) que tienen más de 2 empleados.

<details>
<summary>Ver solución</summary>

```sql
SELECT ID_DEP
FROM SOFT_EMPLEADOS
GROUP BY ID_DEP
HAVING COUNT(*) > 2;

```

</details>

**15. Combinada (filtro previo)**
Muestra la suma total de salarios pagados por departamento, pero solo para el departamento 10.

<details>
<summary>Ver solución</summary>

```sql
SELECT SUM(SALARIO)
FROM SOFT_EMPLEADOS
WHERE ID_DEP = 10;

```

</details>

**16. Agrupación múltiple y filtro de grupo**
Queremos analizar la estructura salarial. Muestra el **ID de departamento**, el **Oficio** y el **Salario Medio** de ese oficio en ese departamento. Pero **solo** muestra aquellos grupos cuya media salarial supere los 2000 euros. Ordena el resultado por el salario medio de mayor a menor.

<details>
<summary>Ver solución</summary>

```sql
SELECT ID_DEP, OFICIO, AVG(SALARIO) AS "Salario Medio"
FROM SOFT_EMPLEADOS
GROUP BY ID_DEP, OFICIO
HAVING AVG(SALARIO) > 2000
ORDER BY 3 DESC; -- O ORDER BY "Salario Medio" DESC

```

</details>

**17. Lógica de filtrado combinada**
Selecciona a los empleados que cumplen **todas** estas condiciones simultáneamente:

1. Su apellido contiene la letra 'A'.
2. Ganan más de 1000 euros.
3. NO trabajan en el departamento 30.

<details>
<summary>Ver solución</summary>

```sql
SELECT * FROM SOFT_EMPLEADOS
WHERE APELLIDO LIKE '%A%' 
  AND SALARIO > 1000 
  AND ID_DEP != 30; -- O ID_DEP <> 30

-- Alternativa: con lógica negada

SELECT * FROM SOFT_EMPLEADOS
WHERE APELLIDO LIKE '%A%' 
  AND SALARIO > 1000 
  AND NOT (ID_DEP = 30);

```

</details>

**18. Count Distinct (La pregunta trampa)**
El director pregunta: "¿Cuántos oficios distintos tenemos cubiertos en el departamento 10?". No quiere saber cuántos empleados hay, sino cuántos tipos de trabajo diferentes (roles) existen allí.

<details>
<summary>Ver solución</summary>

```sql
SELECT COUNT(DISTINCT OFICIO)
FROM SOFT_EMPLEADOS
WHERE ID_DEP = 10;

```

</details>

---

### Bloque 3: Operadores y funciones

*Objetivo: Manipular datos dentro del SELECT.*

**1. Concatenación**
Muestra una columna llamada "Nombre Completo" que una el APELLIDO, una coma, y el NOMBRE de cada empleado.

<details>
<summary>Ver solución</summary>

```sql
SELECT APELLIDO || ', ' || NOMBRE AS "Nombre Completo"
FROM SOFT_EMPLEADOS;

-- Alternativa: con la función CONCAT aunque se lee menos claro
SELECT CONCAT(CONCAT(APELLIDO, ', '), NOMBRE) AS "Nombre Completo"
FROM SOFT_EMPLEADOS;

```

</details>

**2. Aritmética básica**
Calcula el salario anual de cada empleado (Salario * 12).

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE, SALARIO * 12 AS "Salario Anual"
FROM SOFT_EMPLEADOS;

```

</details>

**3. Funciones de caracteres (UPPER)**
Muestra el nombre de todos los proyectos en MAYÚSCULAS.

<details>
<summary>Ver solución</summary>

```sql
SELECT UPPER(NOMBRE_PROY)
FROM SOFT_PROYECTOS;

```

</details>

**4. Funciones de caracteres (SUBSTR)**
Muestra las 3 primeras letras del nombre de cada empleado.

<details>
<summary>Ver solución</summary>

```sql
SELECT SUBSTR(NOMBRE, 1, 3)
FROM SOFT_EMPLEADOS;

```

</details>

**5. Funciones de caracteres (LENGTH)**
Lista los empleados cuyo apellido tenga más de 5 letras.

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE, APELLIDO
FROM SOFT_EMPLEADOS
WHERE LENGTH(APELLIDO) > 5;

```

</details>

**6. Funciones numéricas (ROUND)**
Supón que el salario diario es el mensual dividido entre 30. Muestra el salario diario redondeado a 2 decimales.

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE, ROUND(SALARIO / 30, 2) AS "Salario Diario"
FROM SOFT_EMPLEADOS;

```

</details>

**7. Funciones numéricas (MOD)**
Muestra el resto de dividir el ID del empleado entre 2 (para saber si es par o impar).

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE, ID_EMP, MOD(ID_EMP, 2)
FROM SOFT_EMPLEADOS;

```

</details>

**8. Funciones de fecha (SYSDATE)**
Muestra el nombre del empleado y la fecha de hoy.

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE, SYSDATE
FROM SOFT_EMPLEADOS;

```

</details>

**9. Funciones de fecha (MONTHS_BETWEEN)**
Calcula cuántos meses de antigüedad tiene cada empleado (diferencia entre hoy y `FECHA_ALTA`).

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE, MONTHS_BETWEEN(SYSDATE, FECHA_ALTA)
FROM SOFT_EMPLEADOS;

```

</details>

**10. Funciones de fecha (ADD_MONTHS)**
Calcula la fecha de revisión salarial, que será 6 meses después de la `FECHA_ALTA`.

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE, ADD_MONTHS(FECHA_ALTA, 6) AS "Revision Salarial"
FROM SOFT_EMPLEADOS;

```

</details>

**11. Funciones de conversión (TO_CHAR fecha)**
Muestra la fecha de inicio de los proyectos con el formato "Día/Mes/Año" (ej: '01/Enero/2023').

<details>
<summary>Ver solución</summary>

```sql
SELECT TO_CHAR(FECHA_INICIO, 'DD/MM/YYYY')
FROM SOFT_PROYECTOS;

```

</details>

**12. Funciones de conversión (TO_CHAR moneda)**
Muestra el salario de los empleados con el símbolo de euro y separadores de miles (ej: '2.200 €').

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE, TO_CHAR(SALARIO, '999G999D99') || ' €'
FROM SOFT_EMPLEADOS;

```

</details>

**13. Funciones de nulos (NVL)**
Muestra el nombre y los ingresos totales (Salario + Comisión). *Nota: Si la comisión es NULL, debe sumar 0, no devolver NULL.*

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE, SALARIO + NVL(COMISION, 0) AS "Ingresos Totales"
FROM SOFT_EMPLEADOS;

-- Alternativa: Para que funciona en otras BD (SQL Server, PostgreSQL...)

SELECT NOMBRE, SALARIO + COALESCE(COMISION, 0) AS "Ingresos Totales"
FROM SOFT_EMPLEADOS;


```

</details>

**14. Expresión CASE (simple)**
Muestra el nombre del proyecto y una columna "Tamaño": si el presupuesto es > 40000 es 'GRANDE', si no, 'NORMAL'.

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE_PROY,
       CASE
           WHEN PRESUPUESTO > 40000 THEN 'GRANDE'
           ELSE 'NORMAL'
       END AS "Tamaño"
FROM SOFT_PROYECTOS;

```

</details>

**15. DECODE (específica Oracle)**
Muestra el nombre del empleado y su departamento. Usa DECODE para que en vez de salir '10' salga 'OFICINA', y si es '30' salga 'TIENDA'.

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE, DECODE(ID_DEP, 10, 'OFICINA', 30, 'TIENDA') AS "Ubicación"
FROM SOFT_EMPLEADOS;

-- Alternativa: También se podría hacer sin DECODE

SELECT NOMBRE, 
       CASE ID_DEP 
           WHEN 10 THEN 'OFICINA'
           WHEN 30 THEN 'TIENDA'
       END AS "Ubicación"
FROM SOFT_EMPLEADOS;
```

</details>

**16. Generador de códigos (anidación)**
Genera un "Código de Usuario" para cada empleado concatenando:

1. Las 3 primeras letras del nombre (en mayúsculas).
2. La longitud de su apellido.
3. El ID de su departamento.
Ejemplo: Para 'Juan' 'Perez' del dep 10 -> 'JUA510'.

<details>
<summary>Ver solución</summary>

```sql
SELECT NOMBRE, APELLIDO, 
       UPPER(SUBSTR(NOMBRE, 1, 3)) || LENGTH(APELLIDO) || ID_DEP AS "Código"
FROM SOFT_EMPLEADOS;

```

</details>