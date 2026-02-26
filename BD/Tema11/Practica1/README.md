# Unidad 11/13/14: Práctica 1

## 1. Contexto del esquema de trabajo

Para esta práctica, ampliamos el entorno de **TechFix Solutions** con un esquema
relacional completo de 7 tablas interconectadas. Esto nos permitirá practicar
consultas multitabla, subconsultas, operaciones de conjuntos, vistas y
transacciones sobre datos realistas y relacionados entre sí.

**Tablas del esquema:**

| Tabla                | Descripción                                      |
|----------------------|--------------------------------------------------|
| `U11_DEPARTAMENTOS`  | Departamentos de la empresa                      |
| `U11_EMPLEADOS`      | Empleados, con FK a departamento                 |
| `U11_CLIENTES`       | Clientes de la tienda                            |
| `U11_CATEGORIAS`     | Categorías de componentes                        |
| `U11_COMPONENTES`    | Componentes en stock, con FK a categoría         |
| `U11_PEDIDOS`        | Pedidos realizados, con FK a cliente y empleado  |
| `U11_LINEAS_PEDIDO`  | Líneas de cada pedido, con FK a pedido y componente |

> **Nota:** Ejecuta el script de inicialización antes de cada bloque de
> ejercicios para partir de un estado limpio.

---

## 2. Script de inicialización

```sql
/* ==========================================================
   ESQUEMA: TECHFIX SOLUTIONS — AMPLIADO (Práctica 2)
   Tablas: U11_DEPARTAMENTOS, U11_EMPLEADOS, U11_CLIENTES,
           U11_CATEGORIAS,    U11_COMPONENTES,
           U11_PEDIDOS,       U11_LINEAS_PEDIDO
   ========================================================== */

-- Borrado de tablas (de hijas a padres para respetar FK)
DROP TABLE U11_LINEAS_PEDIDO  CASCADE CONSTRAINTS;
DROP TABLE U11_PEDIDOS        CASCADE CONSTRAINTS;
DROP TABLE U11_COMPONENTES    CASCADE CONSTRAINTS;
DROP TABLE U11_CATEGORIAS     CASCADE CONSTRAINTS;
DROP TABLE U11_CLIENTES       CASCADE CONSTRAINTS;
DROP TABLE U11_EMPLEADOS      CASCADE CONSTRAINTS;
DROP TABLE U11_DEPARTAMENTOS  CASCADE CONSTRAINTS;

-- 1. DEPARTAMENTOS
CREATE TABLE U11_DEPARTAMENTOS (
    id_dept     NUMBER(3)    PRIMARY KEY,
    nombre_dept VARCHAR2(30) NOT NULL,
    presupuesto NUMBER(8,2),
    ciudad      VARCHAR2(30)
);

-- 2. EMPLEADOS
CREATE TABLE U11_EMPLEADOS (
    id_emp         NUMBER(4)    PRIMARY KEY,
    nombre         VARCHAR2(30),
    apellido       VARCHAR2(30),
    puesto         VARCHAR2(20), -- 'TECNICO', 'VENDEDOR', 'GERENTE'
    salario        NUMBER(7,2),
    comision       NUMBER(7,2),  -- Puede ser NULL
    fecha_contrato DATE,
    id_dept        NUMBER(3) REFERENCES U11_DEPARTAMENTOS(id_dept)
);

-- 3. CLIENTES
CREATE TABLE U11_CLIENTES (
    id_cliente NUMBER(4)    PRIMARY KEY,
    nombre     VARCHAR2(30),
    apellido   VARCHAR2(30),
    email      VARCHAR2(50),
    ciudad     VARCHAR2(30),
    telefono   VARCHAR2(15)
);

-- 4. CATEGORIAS
CREATE TABLE U11_CATEGORIAS (
    id_cat      NUMBER(3)     PRIMARY KEY,
    nombre_cat  VARCHAR2(30)  NOT NULL,
    descripcion VARCHAR2(100)
);

-- 5. COMPONENTES
CREATE TABLE U11_COMPONENTES (
    cod_comp VARCHAR2(10) PRIMARY KEY,
    nombre   VARCHAR2(50),
    id_cat   NUMBER(3)   REFERENCES U11_CATEGORIAS(id_cat),
    precio   NUMBER(7,2),
    stock    NUMBER(4)
);

-- 6. PEDIDOS
CREATE TABLE U11_PEDIDOS (
    id_pedido    NUMBER(5)    PRIMARY KEY,
    id_cliente   NUMBER(4)    REFERENCES U11_CLIENTES(id_cliente),
    id_emp       NUMBER(4)    REFERENCES U11_EMPLEADOS(id_emp),
    fecha_pedido DATE,
    estado       VARCHAR2(15) CHECK (estado IN ('PENDIENTE','ENVIADO','ENTREGADO','CANCELADO'))
);

-- 7. LINEAS_PEDIDO
CREATE TABLE U11_LINEAS_PEDIDO (
    id_linea        NUMBER(6)    PRIMARY KEY,
    id_pedido       NUMBER(5)    REFERENCES U11_PEDIDOS(id_pedido),
    cod_comp        VARCHAR2(10) REFERENCES U11_COMPONENTES(cod_comp),
    cantidad        NUMBER(3),
    precio_unitario NUMBER(7,2)
);

-- ============================================================
-- INSERCIONES
-- ============================================================

-- Departamentos (el 4 'Almacén' no tiene empleados → útil para RIGHT JOIN)
INSERT INTO U11_DEPARTAMENTOS VALUES (1, 'Ventas',    15000.00, 'Madrid');
INSERT INTO U11_DEPARTAMENTOS VALUES (2, 'Técnico',   12000.00, 'Barcelona');
INSERT INTO U11_DEPARTAMENTOS VALUES (3, 'Gerencia',  20000.00, 'Madrid');
INSERT INTO U11_DEPARTAMENTOS VALUES (4, 'Almacén',    8000.00, 'Valencia');

-- Empleados (el 107 tiene id_dept NULL → útil para LEFT JOIN)
INSERT INTO U11_EMPLEADOS VALUES (101,'Carlos','Aranda', 'GERENTE', 2500.00,   NULL, TO_DATE('15/01/2010','DD/MM/YYYY'), 3);
INSERT INTO U11_EMPLEADOS VALUES (102,'Elena', 'Vázquez','TECNICO', 2000.00, 200.00, TO_DATE('20/03/2015','DD/MM/YYYY'), 2);
INSERT INTO U11_EMPLEADOS VALUES (103,'Marcos','Ruiz',   'VENDEDOR',1400.00, 600.00, TO_DATE('10/07/2018','DD/MM/YYYY'), 1);
INSERT INTO U11_EMPLEADOS VALUES (104,'Lucía', 'Gómez',  'VENDEDOR',1450.00,   NULL, TO_DATE('05/11/2021','DD/MM/YYYY'), 1);
INSERT INTO U11_EMPLEADOS VALUES (105,'David', 'Sanz',   'TECNICO', 1950.00, 150.00, TO_DATE('12/02/2019','DD/MM/YYYY'), 2);
INSERT INTO U11_EMPLEADOS VALUES (106,'Sofía', 'Méndez', 'VENDEDOR',1300.00, 300.00, SYSDATE-50, 1);
INSERT INTO U11_EMPLEADOS VALUES (107,'Javier','Trillo', 'TECNICO', 1600.00,   NULL, SYSDATE-10, NULL);

-- Clientes
INSERT INTO U11_CLIENTES VALUES (1,'Ana',    'Torres','ana.t@mail.com',    'Madrid',   '600111222');
INSERT INTO U11_CLIENTES VALUES (2,'Luis',   'Gil',   'luis.g@mail.com',   'Barcelona','600222333');
INSERT INTO U11_CLIENTES VALUES (3,'Pedro',  'Mola',  'pedro.m@mail.com',  'Valencia', '600333444');
INSERT INTO U11_CLIENTES VALUES (4,'Marta',  'Ríos',  'marta.r@mail.com',  'Madrid',   '600444555');
INSERT INTO U11_CLIENTES VALUES (5,'Roberto','Cano',  'roberto.c@mail.com','Sevilla',  '600555666');
INSERT INTO U11_CLIENTES VALUES (6,'Carmen', 'Vidal', 'carmen.v@mail.com', 'Valencia', '600666777');

-- Categorías (la 6 'Refrigeración' no tiene componentes → útil para RIGHT JOIN)
INSERT INTO U11_CATEGORIAS VALUES (1,'GPU',           'Tarjetas gráficas');
INSERT INTO U11_CATEGORIAS VALUES (2,'CPU',           'Procesadores');
INSERT INTO U11_CATEGORIAS VALUES (3,'RAM',           'Memoria RAM');
INSERT INTO U11_CATEGORIAS VALUES (4,'ALMACENAMIENTO','Discos y SSDs');
INSERT INTO U11_CATEGORIAS VALUES (5,'PERIFERICO',    'Periféricos varios');
INSERT INTO U11_CATEGORIAS VALUES (6,'REFRIGERACION', 'Sistemas de refrigeración');

-- Componentes
INSERT INTO U11_COMPONENTES VALUES ('GPU-NV-306','Nvidia RTX 3060',        1,  380.99,  2);
INSERT INTO U11_COMPONENTES VALUES ('GPU-NV-409','Nvidia RTX 4090',        1, 1850.00,  0);
INSERT INTO U11_COMPONENTES VALUES ('CPU-INT-09','Intel Core i9 12900K',   2,  650.50, 10);
INSERT INTO U11_COMPONENTES VALUES ('CPU-AMD-07','AMD Ryzen 7 5800X',      2,  420.00,  5);
INSERT INTO U11_COMPONENTES VALUES ('RAM-COR-16','Corsair Vengeance 16GB', 3,   85.50, 50);
INSERT INTO U11_COMPONENTES VALUES ('RAM-KIN-32','Kingston Fury 32GB',     3,  139.99, 20);
INSERT INTO U11_COMPONENTES VALUES ('SSD-SAM-1T','Samsung 980 Pro 1TB',    4,  120.00, 25);
INSERT INTO U11_COMPONENTES VALUES ('MOU-LOG-MX','Logitech MX Master 3',   5,   99.99, 15);
INSERT INTO U11_COMPONENTES VALUES ('KEY-RAZ-BL','Razer BlackWidow',       5,  140.50,  8);

-- Pedidos
INSERT INTO U11_PEDIDOS VALUES (1001, 1, 103, TO_DATE('15/01/2024','DD/MM/YYYY'),'ENTREGADO');
INSERT INTO U11_PEDIDOS VALUES (1002, 2, 103, TO_DATE('20/02/2024','DD/MM/YYYY'),'ENTREGADO');
INSERT INTO U11_PEDIDOS VALUES (1003, 1, 104, TO_DATE('05/03/2024','DD/MM/YYYY'),'ENVIADO');
INSERT INTO U11_PEDIDOS VALUES (1004, 3, 105, TO_DATE('10/04/2024','DD/MM/YYYY'),'ENVIADO');
INSERT INTO U11_PEDIDOS VALUES (1005, 4, 103, TO_DATE('02/05/2024','DD/MM/YYYY'),'PENDIENTE');
INSERT INTO U11_PEDIDOS VALUES (1006, 5, 104, TO_DATE('18/06/2024','DD/MM/YYYY'),'CANCELADO');
INSERT INTO U11_PEDIDOS VALUES (1007, 6, 106, SYSDATE-5,                         'PENDIENTE');

-- Líneas de pedido
INSERT INTO U11_LINEAS_PEDIDO VALUES ( 1, 1001,'GPU-NV-306', 1,  380.99);
INSERT INTO U11_LINEAS_PEDIDO VALUES ( 2, 1001,'RAM-COR-16', 2,   85.50);
INSERT INTO U11_LINEAS_PEDIDO VALUES ( 3, 1002,'CPU-INT-09', 1,  650.50);
INSERT INTO U11_LINEAS_PEDIDO VALUES ( 4, 1003,'GPU-NV-409', 1, 1850.00);
INSERT INTO U11_LINEAS_PEDIDO VALUES ( 5, 1003,'SSD-SAM-1T', 2,  120.00);
INSERT INTO U11_LINEAS_PEDIDO VALUES ( 6, 1004,'CPU-AMD-07', 1,  420.00);
INSERT INTO U11_LINEAS_PEDIDO VALUES ( 7, 1004,'KEY-RAZ-BL', 1,  140.50);
INSERT INTO U11_LINEAS_PEDIDO VALUES ( 8, 1005,'MOU-LOG-MX', 3,   99.99);
INSERT INTO U11_LINEAS_PEDIDO VALUES ( 9, 1006,'GPU-NV-306', 2,  380.99);
INSERT INTO U11_LINEAS_PEDIDO VALUES (10, 1007,'RAM-COR-16', 1,   85.50);
INSERT INTO U11_LINEAS_PEDIDO VALUES (11, 1007,'SSD-SAM-1T', 1,  120.00);

COMMIT;
```


---

## 3. Bloque I — Composiciones (JOINs)

*Recuerda ejecutar el script de inicialización antes de comenzar este bloque.*

### Composiciones internas

1. Muestra el nombre, apellido y puesto de cada empleado junto con el nombre de su
departamento (`nombre_dept`). Usa `INNER JOIN` entre `U11_EMPLEADOS` y
`U11_DEPARTAMENTOS`.
2. A partir de la consulta anterior, añade una condición `WHERE` para obtener
únicamente los empleados del departamento `'Técnico'`, mostrando también
su salario.
3. Usa `NATURAL JOIN` entre `U11_PEDIDOS` y `U11_CLIENTES` para obtener, de cada
pedido, el `id_pedido`, el estado y el nombre y apellido del cliente que lo
realizó. *Observa qué columna comparten ambas tablas y cómo la usa
`NATURAL JOIN` automáticamente.*
4. Muestra el `id_pedido`, el nombre del componente y la cantidad pedida de
cada línea de pedido. Relaciona `U11_LINEAS_PEDIDO` y `U11_COMPONENTES`
con `INNER JOIN`.
5. (**JOIN de 4 tablas**) Lista el nombre del cliente, el nombre y apellido del
empleado que gestionó el pedido, y el nombre de cada componente incluido en
él. Relaciona `U11_CLIENTES`, `U11_PEDIDOS`, `U11_LINEAS_PEDIDO` y
`U11_COMPONENTES`.

### Composiciones externas

6. Muestra **todos** los empleados aunque no tengan departamento asignado.
Incluye las columnas nombre, apellido y `nombre_dept` (que aparecerá como
`NULL` para los empleados sin departamento). Usa `LEFT JOIN`.
7. Muestra **todos** los departamentos aunque no tengan empleados. Incluye
`nombre_dept` y el nombre y apellido del empleado (que aparecerán como
`NULL` donde no haya empleados). Usa `RIGHT JOIN` entre `U11_EMPLEADOS`
y `U11_DEPARTAMENTOS`.
8. Usa `FULL OUTER JOIN` entre `U11_COMPONENTES` y `U11_CATEGORIAS` para
obtener un listado completo que muestre el nombre del componente y el nombre
de su categoría. Deben aparecer tanto los componentes cuya categoría existe
como las categorías que no tienen ningún componente asignado.

---

## 4. Bloque II — Subconsultas

*Recuerda ejecutar el script de inicialización antes de comenzar este bloque.*

### Subconsultas en WHERE

1. Muestra el nombre, apellido y salario de los empleados
cuyo salario es superior a la media de todos los empleados de la empresa.
2. Muestra el nombre y apellido de los empleados que
han gestionado al menos un pedido (que aparezcan en `U11_PEDIDOS`).
3. Muestra los empleados que **no** han gestionado
ningún pedido.
4. Muestra el nombre y precio de los componentes cuyo
precio es inferior al precio de **alguna** CPU (categoría `id_cat = 2`).
*Recuerda qué significa `< ANY`: basta con que sea menor que uno de los valores
del conjunto.*
5. Muestra el nombre y precio de los componentes cuyo
precio es superior al precio de **todos** los módulos de RAM
(categoría `id_cat = 3`). Es decir, que supere el precio de la RAM
más cara.

### Subconsultas con EXISTS / NOT EXISTS

6. Muestra el nombre y apellido de los clientes que tienen al menos
un pedido que **no** está en estado `'CANCELADO'`. Usa una subconsulta
correlacionada con `EXISTS`.
7. Muestra los clientes que **solo** tienen pedidos cancelados
(o ninguno no cancelado). Usa `NOT EXISTS`.

### Subconsultas en FROM y con GROUP BY / HAVING

8. Obtén, por cada departamento, el salario máximo
de sus empleados. Para ello crea una subconsulta en el `FROM` que agrupe
los empleados por `id_dept` y calcule el máximo salario; luego únela
con `U11_DEPARTAMENTOS` para mostrar el `nombre_dept` junto al salario
máximo.
9. Muestra el nombre de los departamentos cuya media salarial supera la media salarial global de
toda la empresa. Usa una subconsulta escalar en la cláusula `HAVING`.
10. Muestra el nombre y precio de los componentes
cuyo precio es superior al precio medio de su **propia categoría**.
*Pista: la subconsulta correlacionada en el `WHERE` debe filtrar
`U11_COMPONENTES` por el mismo `id_cat` que el registro externo.*

---

## 5. Bloque III — Operaciones de conjuntos

*Recuerda ejecutar el script de inicialización antes de comenzar este bloque.*

1. Obtén un listado de todas las ciudades donde hay **clientes
o departamentos**, sin duplicados. Combina la columna `ciudad` de
`U11_CLIENTES` con la columna `ciudad` de `U11_DEPARTAMENTOS`.
2. Repite la consulta anterior usando `UNION ALL`. ¿Cuántas
filas devuelve ahora y por qué difiere del resultado con `UNION`?
3. ¿En qué ciudades hay **tanto** clientes **como** algún
departamento de TechFix?
4. ¿En qué ciudades hay clientes pero **ningún departamento**
de la empresa? Usa `MINUS` (primera consulta `ciudad` de `U11_CLIENTES`,
segunda `ciudad` de `U11_DEPARTAMENTOS`).
5.  Obtén un listado unificado de
personas que incluya:
    - Los empleados de puesto `'TECNICO'` (columnas: nombre, apellido, origen
literal `'EMPLEADO'`).
    - Los clientes que viven en `'Valencia'` (columnas: nombre, apellido, origen
literal `'CLIENTE'`).

Combínalos con `UNION` y ordena el resultado por apellido ascendente.
*Recuerda que `ORDER BY` solo puede usarse una vez, al final, y debe
referenciar las columnas del primer `SELECT`.*

---

## 6. Bloque IV — Vistas

*Recuerda ejecutar el script de inicialización antes de comenzar este bloque.*

1. Crea una vista llamada `V_EMP_DPTO` que muestre, de
cada empleado, su `id_emp`, nombre, apellido, puesto y el `nombre_dept`
de su departamento. Usa `LEFT JOIN` para que aparezcan también los
empleados sin departamento asignado (con `NULL` en `nombre_dept`).
2. Consulta la vista `V_EMP_DPTO` para obtener
únicamente los empleados que pertenecen al departamento `'Técnico'`.
*Observa que la consulta sobre la vista es igual que sobre una tabla.*
3. Modifica la vista `V_EMP_DPTO` con
`CREATE OR REPLACE VIEW` para que incluya también el `salario` del
empleado.
4. Crea una vista llamada `V_PEDIDOS_ACTIVOS`
que muestre los pedidos que **no** están en estado `'CANCELADO'`.
La vista debe incluir: `id_pedido`, `fecha_pedido`, `estado`, el nombre
y apellido del cliente y el nombre y apellido del empleado que gestionó
el pedido. Usa los `JOIN` necesarios.
5. Sobre la vista `V_PEDIDOS_ACTIVOS`,
muestra únicamente los pedidos en estado `'PENDIENTE'` ordenados por
`fecha_pedido` ascendente.
6. Elimina las vistas `V_EMP_DPTO` y `V_PEDIDOS_ACTIVOS`.
Comprueba que ya no existen intentando consultarlas.

---

## 7. Bloque V — DCL básico (Tema 13)

> ⚠️ Los ejercicios de este bloque son de **redacción**: escribe las
> sentencias correctas pero **no es necesario ejecutarlas** si no dispones
> de permisos de administrador en tu instancia de Oracle.

1. Escribe la sentencia que concede el privilegio `SELECT`
sobre la vista `V_PEDIDOS_ACTIVOS` al usuario `CONSULTOR_VENTAS`.
Además, debe permitirle que él a su vez pueda conceder ese privilegio
a otros usuarios (*pista: `WITH GRANT OPTION`*).
2. Escribe la sentencia que revoca el privilegio `SELECT`
sobre `V_PEDIDOS_ACTIVOS` al usuario `CONSULTOR_VENTAS`.

---

<details><summary>El bloque VI no hay que hacerlo</summary>
## 8. Bloque VI — Transacciones (Tema 14)

> ⚠️ **Importante — Desactiva el AUTOCOMMIT antes de empezar este bloque.**
>
> - **SQL Developer:** Herramientas → Preferencias → Base de Datos → Avanzado
>   → desmarca la casilla *Autocommit*.
> - **SQL\*Plus / SQLcl:** ejecuta `SET AUTOCOMMIT OFF;`
>
> En Oracle, las transacciones **se inician automáticamente** con la primera
> sentencia DML; no es necesario escribir `START TRANSACTION` o `BEGIN`.
> La transacción finaliza con `COMMIT` (confirmar) o `ROLLBACK` (deshacer).
>
> Recuerda también ejecutar el script de inicialización antes de comenzar.

---

### Ejercicio 1 — ROLLBACK básico

-  Inserta el siguiente componente en `U11_COMPONENTES`:
código `'FAN-COR-H1'`, nombre `'Corsair H150i Elite'`, `id_cat = 6`
(Refrigeración), precio `189.99`, stock `3`.

- Ejecuta un `SELECT * FROM U11_COMPONENTES` y confirma que el registro
aparece.

- Ejecuta `ROLLBACK`.

- Ejecuta de nuevo `SELECT * FROM U11_COMPONENTES`. ¿Sigue existiendo
el componente? Explica brevemente qué ha ocurrido.

---

### Ejercicio 2 — COMMIT y persistencia

- Inserta un nuevo cliente en `U11_CLIENTES`:
`id_cliente = 7`, nombre `'Raúl'`, apellido `'Navarro'`,
email `'raul.n@mail.com'`, ciudad `'Bilbao'`, teléfono `'600777888'`.

- Ejecuta `COMMIT` para confirmar los cambios.

- Ahora intenta deshacer la inserción ejecutando `ROLLBACK`. ¿Qué
observas? ¿Se ha podido deshacer el registro? Explica por qué.

---

### Ejercicio 3 — SAVEPOINT y ROLLBACK TO SAVEPOINT

- Actualiza el precio de todos los componentes de categoría `'GPU'`
(`id_cat = 1`) incrementándolo un **10%**.

- Crea un punto de guardado: `SAVEPOINT SP_GPU_OK;`

- Actualiza ahora el precio de todos los componentes de categoría
`'CPU'` (`id_cat = 2`) incrementándolo también un **10%**.

- Comprueba los precios actuales con un `SELECT` sobre `U11_COMPONENTES`
filtrando por `id_cat IN (1, 2)`.

- Decide que la subida de CPU fue un error: ejecuta
`ROLLBACK TO SP_GPU_OK;`

- Vuelve a consultar los precios de GPU y CPU. ¿Cuáles mantienen el
incremento y cuáles han vuelto a su valor original? Explica el
comportamiento de `ROLLBACK TO SAVEPOINT`.

- Confirma definitivamente los cambios pendientes con `COMMIT`.

---

### Ejercicio 4 — Transacción compleja con múltiples SAVEPOINTs

Simula el registro de un nuevo pedido paso a paso:

- Inserta un nuevo pedido en `U11_PEDIDOS`:
`id_pedido = 1008`, `id_cliente = 1`, `id_emp = 102`,
`fecha_pedido = SYSDATE`, `estado = 'PENDIENTE'`.

- Crea un punto de guardado: `SAVEPOINT SP_PEDIDO;`

- Inserta la primera línea de pedido en `U11_LINEAS_PEDIDO`:
`id_linea = 12`, `id_pedido = 1008`, `cod_comp = 'CPU-INT-09'`,
`cantidad = 1`, `precio_unitario = 650.50`.

- Crea un segundo punto de guardado: `SAVEPOINT SP_LINEA1;`

- Inserta una segunda línea (el cliente añade por error una GPU):
`id_linea = 13`, `id_pedido = 1008`, `cod_comp = 'GPU-NV-409'`,
`cantidad = 1`, `precio_unitario = 1850.00`.

- El cliente se arrepiente de la GPU. Deshaz **solo** esa última línea
ejecutando `ROLLBACK TO SP_LINEA1;`

- Verifica con sendos `SELECT` que el pedido 1008 y la línea 12
(CPU) siguen existiendo, pero la línea 13 (GPU) ha desaparecido.

- Confirma la transacción con `COMMIT`.

---

> ✅ **Al finalizar este bloque, vuelve a activar el AUTOCOMMIT:**
>
> - **SQL Developer:** Herramientas → Preferencias → Base de Datos → Avanzado
>   → marca de nuevo la casilla *Autocommit*.
> - **SQL\*Plus / SQLcl:** ejecuta `SET AUTOCOMMIT ON;`

---
</details>

### Criterios de Entrega

1. Entrega un único fichero `.sql`.
2. Añade un comentario al principio con tu nombre: `-- Autor: Nombre Apellido`.
3. Numera cada ejercicio con comentarios: `-- Ejercicio 3.1`, `-- Ejercicio 4.5`, etc.
4. Asegúrate de que el fichero no contiene errores de sintaxis.
5. En los ejercicios del **Bloque V (DCL)**, añade únicamente la sentencia
escrita como comentario.
