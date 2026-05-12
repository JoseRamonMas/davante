# Ejercicios de repaso UT17: Subprogramas y Disparadores

> **Nota introductoria para el alumno:** Para realizar estos ejercicios, asume que partimos del esquema de la empresa **TechCorp** visto en la teoría (tablas `DEPARTAMENTOS`, `EMPLEADOS`, `PROYECTOS`, `EMPLEADOS_PROYECTOS` y `AUDITORIA_EMPLEADOS` con sus datos iniciales). Recuerda ejecutar `SET SERVEROUTPUT ON` en tu entorno para poder visualizar los mensajes.

---

### Ejercicio 1: Procedimientos almacenados con parámetros IN y OUT

**Objetivo:** Crear un bloque reutilizable que reciba datos, consulte la base de datos y devuelva información a través de parámetros de salida.

**Enunciado:**
Crea un procedimiento almacenado llamado `prc_info_proyecto` que reciba como parámetro de entrada (`IN`) el ID de un proyecto (`p_id_proyecto`).

1. El procedimiento debe tener dos parámetros de salida (`OUT`): `p_nombre_proyecto` (tipo VARCHAR2) y `p_presupuesto` (tipo NUMBER).
2. Utiliza un `SELECT INTO` para buscar el proyecto en la tabla `PROYECTOS` y volcar los datos en los parámetros de salida.
3. Captura la excepción `NO_DATA_FOUND` y, en caso de ocurrir, asigna el texto `'No existe'` al nombre y un `0` al presupuesto.
4. Escribe un bloque anónimo que invoque a este procedimiento para el proyecto con ID `101` y muestre los resultados devueltos por pantalla.

<details>
<summary>Ver solución</summary>

```sql
-- 1. Creación del procedimiento
CREATE OR REPLACE PROCEDURE prc_info_proyecto (
    p_id_proyecto     IN  PROYECTOS.id_proyecto%TYPE,
    p_nombre_proyecto OUT VARCHAR2,
    p_presupuesto     OUT NUMBER
)
IS
BEGIN
    SELECT nombre, presupuesto
    INTO p_nombre_proyecto, p_presupuesto
    FROM PROYECTOS
    WHERE id_proyecto = p_id_proyecto;
    
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        p_nombre_proyecto := 'No existe';
        p_presupuesto := 0;
END prc_info_proyecto;
/

-- 2. Bloque anónimo para invocarlo
DECLARE
    v_nombre VARCHAR2(100);
    v_presup NUMBER(12,2);
BEGIN
    prc_info_proyecto(101, v_nombre, v_presup);
    DBMS_OUTPUT.PUT_LINE('Proyecto: ' || v_nombre || ' - Presupuesto: ' || v_presup || ' €');
END;
/

```

</details>

---

### Ejercicio 2: Funciones almacenadas y su uso en SQL

**Objetivo:** Diferenciar funciones de procedimientos (uso de `RETURN`) e integrarlas directamente en consultas SQL.

**Enunciado:**
Las funciones devuelven siempre un valor y, si no realizan operaciones DML, pueden usarse en instrucciones `SELECT`.

1. Crea una función llamada `fnc_clasificar_salario` que reciba un salario (`p_salario IN NUMBER`) y devuelva un `VARCHAR2`.
2. La función debe evaluar el salario:
* Si es mayor a 3000, devuelve `'ALTO'`.
* Si está entre 2000 y 3000 (ambos inclusive), devuelve `'MEDIO'`.
* Si es menor a 2000, devuelve `'BAJO'`.


3. Una vez compilada la función, escribe una consulta `SELECT` sobre la tabla `EMPLEADOS` que muestre el nombre, el salario y una tercera columna llamada `CATEGORIA` que se calcule invocando a tu función `fnc_clasificar_salario`.

<details>
<summary>Ver solución</summary>

```sql
-- 1. Creación de la función
CREATE OR REPLACE FUNCTION fnc_clasificar_salario (
    p_salario IN NUMBER
) RETURN VARCHAR2
IS
BEGIN
    IF p_salario > 3000 THEN
        RETURN 'ALTO';
    ELSIF p_salario >= 2000 THEN
        RETURN 'MEDIO';
    ELSE
        RETURN 'BAJO';
    END IF;
END fnc_clasificar_salario;
/

-- 2. Uso de la función en una consulta SQL
SELECT nombre, 
       salario, 
       fnc_clasificar_salario(salario) AS categoria
FROM EMPLEADOS
ORDER BY salario DESC;

```

</details>

---

### Ejercicio 3: Paquetes (Especificación y Cuerpo)

**Objetivo:** Agrupar lógica de negocio relacionada, declarando una interfaz pública y ocultando la implementación en el cuerpo del paquete.

**Enunciado:**
Vamos a crear un módulo para gestionar Recursos Humanos mediante un paquete llamado `pkg_rrhh`.

1. Crea la **especificación** del paquete (`CREATE PACKAGE`). Debe declarar una constante pública `C_TOPE_SALARIO CONSTANT NUMBER := 5000` y la cabecera de un procedimiento público llamado `prc_actualizar_email(p_id_emp NUMBER, p_nuevo_email VARCHAR2)`.
2. Crea el **cuerpo** del paquete (`CREATE PACKAGE BODY`). Implementa el procedimiento `prc_actualizar_email` para que actualice el email del empleado indicado en la tabla `EMPLEADOS`.
3. Invoca el procedimiento del paquete desde un bloque anónimo para cambiar el email del empleado con ID `1` a `'nuevo.email@techcorp.es'`.

<details>
<summary>Ver solución</summary>

```sql
-- 1. Especificación del paquete
CREATE OR REPLACE PACKAGE pkg_rrhh AS
    C_TOPE_SALARIO CONSTANT NUMBER := 5000;
    PROCEDURE prc_actualizar_email(p_id_emp NUMBER, p_nuevo_email VARCHAR2);
END pkg_rrhh;
/

-- 2. Cuerpo del paquete
CREATE OR REPLACE PACKAGE BODY pkg_rrhh AS

    PROCEDURE prc_actualizar_email(p_id_emp NUMBER, p_nuevo_email VARCHAR2) IS
    BEGIN
        UPDATE EMPLEADOS
        SET email = p_nuevo_email
        WHERE id_empleado = p_id_emp;
        
        IF SQL%NOTFOUND THEN
            DBMS_OUTPUT.PUT_LINE('No se encontró al empleado.');
        ELSE
            DBMS_OUTPUT.PUT_LINE('Email actualizado con éxito.');
            COMMIT;
        END IF;
    END prc_actualizar_email;

END pkg_rrhh;
/

-- 3. Invocación
BEGIN
    pkg_rrhh.prc_actualizar_email(1, 'nuevo.email@techcorp.es');
END;
/

```

</details>

---

### Ejercicio 4: Disparador (Trigger) de Fila para Auditoría

**Objetivo:** Automatizar la creación de un histórico de cambios accediendo a los valores `:NEW` y `:OLD`.

**Enunciado:**
La empresa necesita registrar cualquier cambio que se produzca en los salarios de los empleados.

1. Crea un trigger de fila llamado `trg_auditoria_salario` que se dispare `AFTER UPDATE OF salario ON EMPLEADOS FOR EACH ROW`.
2. Utiliza la cláusula `WHEN (OLD.salario != NEW.salario)` para asegurarte de que solo se audita si hay un cambio real.
3. El trigger debe hacer un `INSERT` en la tabla `AUDITORIA_EMPLEADOS` guardando: el `id_empleado` (`:OLD.id_empleado`), la acción (`'UPDATE'`), el campo (`'SALARIO'`), el valor anterior (con `:OLD.salario`) y el valor nuevo (con `:NEW.salario`). Recuerda usar `TO_CHAR` para los números.
4. Ejecuta un `UPDATE` manual sumando 100 € al empleado con ID `2` y comprueba la tabla de auditoría con un `SELECT`.

<details>
<summary>Ver solución</summary>

```sql
-- 1, 2 y 3. Creación del Trigger
CREATE OR REPLACE TRIGGER trg_auditoria_salario
AFTER UPDATE OF salario ON EMPLEADOS
FOR EACH ROW
WHEN (OLD.salario != NEW.salario)
BEGIN
    INSERT INTO AUDITORIA_EMPLEADOS (
        id_empleado, accion, campo_modificado, valor_anterior, valor_nuevo
    ) VALUES (
        :OLD.id_empleado, 
        'UPDATE', 
        'SALARIO', 
        TO_CHAR(:OLD.salario), 
        TO_CHAR(:NEW.salario)
    );
    -- Los triggers no llevan COMMIT, comparten transacción con el UPDATE
END trg_auditoria_salario;
/

-- 4. Prueba del disparador
UPDATE EMPLEADOS SET salario = salario + 100 WHERE id_empleado = 2;
COMMIT;

SELECT id_empleado, accion, valor_anterior, valor_nuevo 
FROM AUDITORIA_EMPLEADOS;

```

</details>

---

### Ejercicio 5: Disparador Multi-evento con Excepciones

**Objetivo:** Centralizar reglas de negocio validando datos antes de que se guarden y utilizando condicionales de evento (`INSERTING`, `DELETING`).

**Enunciado:**
Vamos a proteger la tabla `DEPARTAMENTOS` creando un trigger llamado `trg_control_deptos` que se active `BEFORE INSERT OR DELETE ON DEPARTAMENTOS FOR EACH ROW`.

1. Usando los predicados de evento, si la acción es un `INSERT` (`IF INSERTING THEN`), comprueba que el presupuesto que se intenta insertar no sea negativo (`:NEW.presupuesto < 0`). Si lo es, lanza un error con `RAISE_APPLICATION_ERROR(-20010, 'El presupuesto inicial no puede ser negativo')`.
2. Si la acción es un `DELETE` (`ELSIF DELETING THEN`), impide que se borre el departamento de `'Desarrollo'` (`:OLD.nombre = 'Desarrollo'`) lanzando el error `-20020` con un mensaje apropiado.
3. Intenta borrar el departamento de Desarrollo y verifica que el trigger te lo impide.

<details>
<summary>Ver solución</summary>

```sql
-- 1 y 2. Creación del Trigger multi-evento
CREATE OR REPLACE TRIGGER trg_control_deptos
BEFORE INSERT OR DELETE ON DEPARTAMENTOS
FOR EACH ROW
BEGIN
    IF INSERTING THEN
        IF :NEW.presupuesto < 0 THEN
            RAISE_APPLICATION_ERROR(-20010, 'El presupuesto inicial no puede ser negativo');
        END IF;
        
    ELSIF DELETING THEN
        IF :OLD.nombre = 'Desarrollo' THEN
            RAISE_APPLICATION_ERROR(-20020, 'Prohibido eliminar el departamento de Desarrollo');
        END IF;
    END IF;
END trg_control_deptos;
/

-- 3. Prueba del borrado (Generará un error ORA-20020)
DELETE FROM DEPARTAMENTOS WHERE nombre = 'Desarrollo';

```

</details>

---

### Ejercicio 6: Normalización Automática (`BEFORE INSERT`)

**Objetivo:** Usar triggers para limpiar y formatear los datos de entrada antes de que se almacenen definitivamente en la tabla.

**Enunciado:**
Crea un disparador `trg_formatear_proyecto` que actúe `BEFORE INSERT OR UPDATE ON PROYECTOS FOR EACH ROW`.

1. Su única función es garantizar que el nombre del proyecto siempre se guarde con la primera letra de cada palabra en mayúscula, independientemente de cómo lo introduzca el usuario.
2. Modifica en el trigger el valor que se va a guardar (`:NEW.nombre`) aplicando la función `INITCAP` sobre él.
3. Inserta un nuevo proyecto llamado `'portal web corporativo'` en el departamento `3` e inmediatamente haz un `SELECT` para comprobar que se guardó como `'Portal Web Corporativo'`.

<details>
<summary>Ver solución</summary>

```sql
-- 1 y 2. Trigger para modificar :NEW
CREATE OR REPLACE TRIGGER trg_formatear_proyecto
BEFORE INSERT OR UPDATE ON PROYECTOS
FOR EACH ROW
BEGIN
    -- Forzamos el formato Título usando INITCAP
    :NEW.nombre := INITCAP(:NEW.nombre);
END trg_formatear_proyecto;
/

-- 3. Prueba
INSERT INTO PROYECTOS (nombre, presupuesto, id_departamento)
VALUES ('portal web corporativo', 20000, 3);
COMMIT;

SELECT nombre FROM PROYECTOS WHERE nombre = 'Portal Web Corporativo';

```

</details>
