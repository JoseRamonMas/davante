# Unidad 9/10/12: Práctica 1

## 1. Script de inicialización

Ejecuta el siguiente script para generar el entorno de trabajo. Ejecútalo en tu base de datos Oracle antes de comenzar cada bloque de ejercicios.

```sql
/* ==========================================================
   ESQUEMA: TECHFIX SOLUTIONS
   Tablas: U9_EMPLEADOS, U9_COMPONENTES, U9_REPARACIONES
   ========================================================== */

-- Borrado de tablas por si existen
DROP TABLE U9_REPARACIONES CASCADE CONSTRAINTS;
DROP TABLE U9_COMPONENTES CASCADE CONSTRAINTS;
DROP TABLE U9_EMPLEADOS CASCADE CONSTRAINTS;

-- 1. Tabla EMPLEADOS
CREATE TABLE U9_EMPLEADOS (
    id_emp NUMBER(4) PRIMARY KEY,
    nombre VARCHAR2(30) NOT NULL,
    apellido VARCHAR2(30) NOT NULL,
    puesto VARCHAR2(20) NOT NULL, -- 'TECNICO', 'VENDEDOR', 'GERENTE'
    salario NUMBER(7,2) NOT NULL,
    comision NUMBER(7,2), -- Puede ser NULL
    fecha_contrato DATE NOT NULL,
    email VARCHAR2(50) NOT NULL
);

-- 2. Tabla COMPONENTES (Stock de tienda)
CREATE TABLE U9_COMPONENTES (
    cod_comp VARCHAR2(10) PRIMARY KEY,
    nombre VARCHAR2(50) NOT NULL,
    categoria VARCHAR2(20) NOT NULL, -- 'GPU', 'CPU', 'RAM', 'ALMACENAMIENTO', 'PERIFERICO'
    precio NUMBER(6,2) NOT NULL,
    stock NUMBER(4) NOT NULL,
    peso_gr NUMBER(5) NOT NULL,
    fecha_alta DATE NOT NULL
);

-- 3. Tabla REPARACIONES (Servicio técnico)
CREATE TABLE U9_REPARACIONES (
    id_rep NUMBER(5) PRIMARY KEY,
    cliente VARCHAR2(50) NOT NULL,
    dispositivo VARCHAR2(50) NOT NULL,
    descripcion_averia VARCHAR2(100) NOT NULL,
    estado VARCHAR2(15) CHECK (estado IN ('PENDIENTE', 'REVISAR', 'EN_PROCESO', 'TERMINADO', 'ENTREGADO')),
    coste_mano_obra NUMBER(6,2) NOT NULL,
    fecha_entrada DATE NOT NULL
);

-- INSERCIONES
-- Empleados
INSERT INTO U9_EMPLEADOS VALUES (101, 'Carlos', 'Aranda', 'GERENTE', 3500, NULL, TO_DATE('15/01/2010','DD/MM/YYYY'), 'c.aranda@techfix.com');
INSERT INTO U9_EMPLEADOS VALUES (102, 'Elena', 'Vázquez', 'TECNICO', 1800, 200, TO_DATE('20/03/2015','DD/MM/YYYY'), 'elena.v@techfix.com');
INSERT INTO U9_EMPLEADOS VALUES (103, 'Marcos', 'Ruiz', 'VENDEDOR', 1400, 600, TO_DATE('10/07/2018','DD/MM/YYYY'), 'marcos.r@techfix.com');
INSERT INTO U9_EMPLEADOS VALUES (104, 'Lucía', 'Gómez', 'VENDEDOR', 1450, NULL, TO_DATE('05/11/2021','DD/MM/YYYY'), 'lucia.gomez@techfix.com');
INSERT INTO U9_EMPLEADOS VALUES (105, 'David', 'Sanz', 'TECNICO', 1750, 150, TO_DATE('12/02/2019','DD/MM/YYYY'), 'd.sanz@techfix.com');
INSERT INTO U9_EMPLEADOS VALUES (106, 'Sofía', 'Méndez', 'VENDEDOR', 1300, 300, SYSDATE-50, 'sofia.m@techfix.com'); -- Contratada hace poco

-- Componentes
INSERT INTO U9_COMPONENTES VALUES ('CPU-INT-09', 'Intel Core i9 12900K', 'CPU', 650.50, 10, 250, TO_DATE('01/01/2022','DD/MM/YYYY'));
INSERT INTO U9_COMPONENTES VALUES ('CPU-AMD-07', 'AMD Ryzen 7 5800X', 'CPU', 420.00, 5, 200, TO_DATE('15/02/2022','DD/MM/YYYY'));
INSERT INTO U9_COMPONENTES VALUES ('GPU-NV-306', 'Nvidia RTX 3060', 'GPU', 380.99, 2, 800, TO_DATE('10/03/2021','DD/MM/YYYY'));
INSERT INTO U9_COMPONENTES VALUES ('GPU-NV-409', 'Nvidia RTX 4090', 'GPU', 1850.00, 0, 1200, TO_DATE('20/12/2022','DD/MM/YYYY'));
INSERT INTO U9_COMPONENTES VALUES ('RAM-COR-16', 'Corsair Vengeance 16GB', 'RAM', 85.50, 50, 50, TO_DATE('05/05/2020','DD/MM/YYYY'));
INSERT INTO U9_COMPONENTES VALUES ('SSD-SAM-1T', 'Samsung 980 Pro 1TB', 'ALMACENAMIENTO', 120.00, 25, 30, TO_DATE('01/06/2021','DD/MM/YYYY'));
INSERT INTO U9_COMPONENTES VALUES ('MOU-LOG-MX', 'Logitech MX Master 3', 'PERIFERICO', 99.99, 15, 140, TO_DATE('15/08/2023','DD/MM/YYYY'));
INSERT INTO U9_COMPONENTES VALUES ('KEY-RAZ-BL', 'Razer BlackWidow', 'PERIFERICO', 140.50, 8, 900, TO_DATE('10/09/2019','DD/MM/YYYY'));

-- Reparaciones
INSERT INTO U9_REPARACIONES VALUES (5001, 'Ana Torres', 'Portátil HP Pavilion', 'Pantalla rota', 'PENDIENTE', 80.00, SYSDATE-2);
INSERT INTO U9_REPARACIONES VALUES (5002, 'Luis Gil', 'Torre Custom', 'No enciende', 'EN_PROCESO', 45.50, SYSDATE-5);
INSERT INTO U9_REPARACIONES VALUES (5003, 'Pedro Mola', 'Consola PS5', 'Limpieza y pasta térmica', 'TERMINADO', 30.00, SYSDATE-10);
INSERT INTO U9_REPARACIONES VALUES (5004, 'Marta Ríos', 'Portátil Dell XPS', 'Cambio batería', 'ENTREGADO', 25.00, TO_DATE('01/01/2023','DD/MM/YYYY'));

COMMIT;

```

---

## 2. Bloque I: Lenguaje de manipulación de Datos (DML)

1. Inserta un nuevo empleado en la tabla `U9_EMPLEADOS`: Se llama "Javier Trillo", puesto 'TECNICO', salario 1600, sin comisión, contratado hoy mismo, id 107 y email `javier.t@techfix.com`.
2. Inserta en `U9_COMPONENTES` un nuevo disco duro: Código 'SSD-KIN-2T', nombre 'Kingston NV2 2TB'. El resto de campos déjalos vacíos (null) usando la lista de columnas en el `INSERT`.
3. Ha llegado una reparación urgente a `U9_REPARACIONES`. ID 5005, cliente "Empresa S.L.", dispositivo "Servidor Dell", problema "Fallo RAID", estado 'PENDIENTE', coste 150. La fecha de entrada fue el 1 de Febrero de 2024 a las 9 de la mañana (usa `TO_DATE` con formato de hora).
4. Debido a la inflación, sube el precio de todos los componentes de la categoría 'GPU' un 5%.
5. Los técnicos (puesto 'TECNICO') han trabajado horas extra. Aumenta su salario en 120€, pero solo a aquellos que fueron contratados antes del año 2020.
6. Hemos notado que algunos vendedores tienen la comisión a NULL. Actualiza dichos registros para que su comisión sea de 50€.
7. Borra de la tabla `U9_REPARACIONES` aquellas reparaciones antiguas que ya fueron 'ENTREGADO' y cuya fecha de entrada sea anterior al año 2024.
8. Borra de la tabla `U9_COMPONENTES` todos los productos de la categoría 'CPU'que contengan la palabra "Ryzen" en su nombre.
9. Modifica la fecha de alta de los componentes de tipo 'RAM'. Ponles como fecha de alta el próximo lunes respecto a la fecha actual del sistema.
10. (No ejecutar, solo escribir la sentencia). Escribe cómo insertarías en la tabla `U9_REPARACIONES` una copia de todos los datos de las reparaciones actuales que están en estado 'PENDIENTE', pero cambiándoles el ID (sumándole 1000 al actual) y poniendo el estado a 'REVISAR'. *Pista: INSERT INTO ... SELECT ...*

---

## 3. Bloque II: Consultas básicas

*Recuerda ejecutar el script de inicialización antes de empezar este bloque.*

1. Muestra el nombre, categoría y precio de los componentes que contengan la cadena "RTX" o "Pro" en su nombre. Ordena el resultado por precio descendente.
2. Lista los empleados que son 'VENDEDOR' o 'TECNICO' y cuyo salario oscila entre 1300 y 1750 euros (ambos inclusive).
3. Muestra el nombre y el email de los empleados que **no** tienen comisión asignada (es nula).
4. Queremos saber qué categorías de productos distintas tenemos actualmente en la base de datos `U9_COMPONENTES`.
5. Muestra el nombre del componente, su precio actual, y una columna calculada llamada "Precio_con_IVA" (21%). Redondea el resultado a 2 decimales.
6. Obtén los datos de las reparaciones que entraron en el último trimestre de cualquier año (meses 10, 11 o 12).
7. Muestra los empleados ordenados por puesto alfabéticamente. Dentro del mismo puesto, ordénalos por antigüedad (fecha de contrato) de más recientes a más antiguos.
8. Muestra los componentes que **no** sean ni 'GPU' ni 'CPU' y cuyo stock sea superior a 10 unidades.
9. Queremos analizar qué tipos de productos suelen tener modelos de alto rendimiento o "Gaming". Muestra las **categorías distintas*** de la tabla `U9_COMPONENTES` que contengan algún producto cuyo nombre incluya la letra 'X' (en mayúscula, indicativo de series X/XT/RTX) o la subcadena 'Pro' en cualquier posición.
10. Muestra las reparaciones que están 'PENDIENTE' y tienen un coste superior a 50€; O aquellas que están 'TERMINADO' sin importar el coste.

---

## 4. Bloque III: Funciones, agrupación y CASE

*Recuerda ejecutar el script de inicialización antes de empezar este bloque.*

1. Muestra el nombre, salario, comisión y los "Ingresos Totales" (salario + comisión) de todos los empleados. Si la comisión es nula, debe sumar 0.
2. Genera un listado de "Códigos de Usuario" para los empleados. Este código se forma concatenando: Las 2 primeras letras del puesto, un guion, y las 3 primeras letras del apellido (todo en mayúsculas). Ejemplo: TEC-VAZ.
3. La empresa hace una revisión de sueldo a los 6 meses de contratar a alguien. Muestra el nombre del empleado, su fecha de contrato y la fecha de su "Primera Revisión" (fecha contrato + 6 meses).
4. Muestra el nombre del componente, su precio y una columna nueva llamada "Gama".
    * Si el precio es < 100: 'Baja'
    * Si el precio está entre 100 y 400: 'Media'
    * Si el precio es > 400: 'Alta'
5. Muestra el nombre del empleado y su puesto traducido al inglés usando `DECODE` (TECNICO -> 'Technician', VENDEDOR -> 'Salesman', GERENTE -> 'Manager').
6. Calcula cuántos componentes hay de cada `categoria` y cuál es el precio medio de los productos de dicha categoría.
7. ¿Cuál es el salario más alto y el más bajo dentro de cada puesto de trabajo en `U9_EMPLEADOS`?
8. Muestra las categorías de componentes (`U9_COMPONENTES`) que tienen un stock total (la suma del stock de sus productos) inferior a 20 unidades.
9. Queremos identificar las categorías "estratégicas" de nuestro inventario. Muestra la `categoria`, el número de productos distintos que tiene y el "Valor Total del Stock" (suma del `precio * stock` de todos sus productos).

    * **Filtrado:** Solo deben aparecer aquellas categorías que cumplan **ambas** condiciones:
        1. Tener **más de 1** producto distinto registrado.
        2. Que el valor total de su stock supere los **2000€**.
10. Queremos un informe de reparaciones (`U9_REPARACIONES`). Muestra el `estado` y la suma total de dinero (`coste_mano_obra`) que vamos a facturar por cada estado, pero **excluyendo** las reparaciones cuyo coste sea inferior a 30€. Ordena el resultado por la facturación total de mayor a menor.

---

### Criterios de Entrega

1. Debes entregar un único archivo `.sql`.
2. Añade un comentario (`--`) al principio del fichero que contenga tu nombre (autor).
3. Añade comentarios (`--`) antes de cada ejercicio para numerarlos (`-- Ejercicio 1.1`)
4. Asegúrate de entregar el fichero sin errores de sintaxis.

