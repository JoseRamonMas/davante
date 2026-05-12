# Ejercicios de repaso UT16: Tipos de datos compuestos y cursores

> **Nota introductoria para el alumno:** Para realizar estos ejercicios, asumimos que partimos del esquema de la **Empresa de desarrollo de software** visto en la teoría (tablas `DEPARTAMENTOS`, `EMPLEADOS`, `PROYECTOS` y `ASIGNACIONES` con sus datos iniciales). Recuerda activar la salida por consola en tu entorno (`SET SERVEROUTPUT ON` o activando *DBMS Output* en SQL Developer).

---

### Ejercicio 1: Registros (`%ROWTYPE`) y Cursor Implícito (`SQL%`)

**Objetivo:** Trabajar con registros que calcan la estructura de una tabla y verificar el resultado de las operaciones DML mediante los atributos de los cursores implícitos.

**Enunciado:**
Escribe un bloque PL/SQL anónimo que declare una variable de tipo registro anclada a la estructura de la tabla `PROYECTOS` mediante `%ROWTYPE`.

1. Utiliza `SELECT INTO` para cargar en ese registro los datos del proyecto con `id_proyecto = 101`.
2. Modifica en memoria el campo presupuesto de tu registro, aumentándolo en 10.000 €.
3. Realiza un `UPDATE` en la tabla `PROYECTOS` para volcar este nuevo presupuesto, filtrando por el ID del registro.
4. Utiliza el atributo `SQL%FOUND` del cursor implícito para comprobar si la actualización tuvo éxito. Si es así, imprime: `'Proyecto [Nombre] actualizado. Filas afectadas: [SQL%ROWCOUNT]'`. Haz un `COMMIT`.

<details>
<summary>Ver solución</summary>

```sql
DECLARE
    v_proyecto PROYECTOS%ROWTYPE;
BEGIN
    -- 1. Cargamos el registro
    SELECT * INTO v_proyecto
    FROM PROYECTOS
    WHERE id_proyecto = 101;
    
    -- 2. Modificamos el valor en el registro (en memoria)
    v_proyecto.presupuesto := v_proyecto.presupuesto + 10000;
    
    -- 3. Actualizamos la base de datos usando el ID del registro
    UPDATE PROYECTOS
    SET presupuesto = v_proyecto.presupuesto
    WHERE id_proyecto = v_proyecto.id_proyecto;
    
    -- 4. Comprobamos el cursor implícito SQL
    IF SQL%FOUND THEN
        DBMS_OUTPUT.PUT_LINE('Proyecto ' || v_proyecto.nombre || ' actualizado.');
        DBMS_OUTPUT.PUT_LINE('Filas afectadas: ' || SQL%ROWCOUNT);
        COMMIT;
    ELSE
        DBMS_OUTPUT.PUT_LINE('No se pudo actualizar el proyecto.');
        ROLLBACK;
    END IF;
END;
/

```

</details>

---

### Ejercicio 2: Colecciones (`VARRAY`) y Cursor `FOR LOOP`

**Objetivo:** Llenar una colección de tamaño máximo fijo (`VARRAY`) de forma dinámica utilizando la sintaxis abreviada del cursor `FOR LOOP`.

**Enunciado:**
Los arrays (`VARRAY`) son colecciones ordenadas con un tamaño máximo fijo.

1. Declara un tipo `VARRAY` de un máximo de **3** elementos que almacene cadenas de texto `VARCHAR2(100)`. Declara e inicializa una variable de este tipo vacía.
2. Utiliza un **cursor FOR LOOP inline** (la consulta directamente en el `FOR`) para obtener el nombre y apellidos de los 3 empleados con mayor salario de la empresa (`ORDER BY salario DESC FETCH FIRST 3 ROWS ONLY`).
3. Dentro del bucle, usa el método `EXTEND` para hacer hueco en el array y guarda el nombre completo del empleado en la última posición disponible (`COUNT`).
4. Al salir del bucle, recorre el array utilizando los métodos `FIRST` y `LAST` para imprimir el "Top 3 Empleados mejor pagados".

<details>
<summary>Ver solución</summary>

```sql
DECLARE
    -- 1. Declaración del tipo VARRAY e inicialización vacía
    TYPE t_top_empleados IS VARRAY(3) OF VARCHAR2(100);
    v_top_emp t_top_empleados := t_top_empleados();
BEGIN
    -- 2. Cursor FOR LOOP inline
    FOR rec IN (
        SELECT nombre || ' ' || apellidos AS nombre_completo 
        FROM EMPLEADOS 
        ORDER BY salario DESC 
        FETCH FIRST 3 ROWS ONLY
    ) LOOP
        -- 3. Hacemos hueco y añadimos al final
        v_top_emp.EXTEND;
        v_top_emp(v_top_emp.COUNT) := rec.nombre_completo;
    END LOOP;
    
    -- 4. Recorrido del array para imprimir
    DBMS_OUTPUT.PUT_LINE('--- TOP 3 EMPLEADOS MEJOR PAGADOS ---');
    FOR i IN v_top_emp.FIRST .. v_top_emp.LAST LOOP
        DBMS_OUTPUT.PUT_LINE(i || '. ' || v_top_emp(i));
    END LOOP;
END;
/

```

</details>

---

### Ejercicio 3: Arrays Asociativos (`INDEX BY TABLE`)

**Objetivo:** Trabajar con diccionarios clave-valor en memoria usando claves alfanuméricas (`VARCHAR2`).

**Enunciado:**
Los arrays asociativos permiten realizar búsquedas rápidas usando claves personalizadas.

1. Declara un tipo de array asociativo donde la **clave** sea el nombre del departamento (`VARCHAR2(50)`) y el **valor** sea su presupuesto (`NUMBER(10,2)`). Declara una variable de este tipo.
2. Llena este array recorriendo la tabla `DEPARTAMENTOS` con un bucle `FOR`. La asignación directa (`array(clave) := valor`) es suficiente.
3. Modifica directamente en el array el presupuesto de `'Frontend'`, sumándole 5.000 €.
4. Recorre el array asociativo completo utilizando los métodos `FIRST` y `NEXT` junto con un bucle `WHILE`, imprimiendo el nombre de cada departamento y su presupuesto.

<details>
<summary>Ver solución</summary>

```sql
DECLARE
    -- 1. Declaración del array asociativo
    TYPE t_presupuestos IS TABLE OF NUMBER(10,2) INDEX BY VARCHAR2(50);
    v_presup_cache t_presupuestos;
    
    v_clave VARCHAR2(50);
BEGIN
    -- 2. Llenado del array
    FOR rec IN (SELECT nombre, presupuesto FROM DEPARTAMENTOS) LOOP
        v_presup_cache(rec.nombre) := rec.presupuesto;
    END LOOP;
    
    -- 3. Modificamos un valor directamente
    IF v_presup_cache.EXISTS('Frontend') THEN
        v_presup_cache('Frontend') := v_presup_cache('Frontend') + 5000;
    END IF;
    
    -- 4. Recorrido con FIRST y NEXT
    DBMS_OUTPUT.PUT_LINE('--- CACHÉ DE PRESUPUESTOS ---');
    v_clave := v_presup_cache.FIRST;
    
    WHILE v_clave IS NOT NULL LOOP
        DBMS_OUTPUT.PUT_LINE('Dpto: ' || RPAD(v_clave, 15) || ' -> ' || v_presup_cache(v_clave) || ' €');
        v_clave := v_presup_cache.NEXT(v_clave);
    END LOOP;
END;
/

```

</details>

---

### Ejercicio 4: Tablas Anidadas (`NESTED TABLE`) y Huecos

**Objetivo:** Gestionar colecciones de tamaño dinámico y manejar los huecos producidos al eliminar elementos mediante `DELETE(i)`.

**Enunciado:**
A diferencia de un `VARRAY`, las tablas anidadas permiten borrar elementos intermedios.

1. Declara una tabla anidada que almacene nombres de proyectos (`VARCHAR2(100)`).
2. Llena la colección con todos los nombres de la tabla `PROYECTOS` utilizando `EXTEND` dentro de un bucle.
3. Recorre tu colección con un bucle `FOR` (del 1 al `COUNT`) y, si el índice es par (`MOD(i, 2) = 0`), elimina ese elemento usando el método `DELETE(i)`.
4. Imprime la lista de proyectos resultantes. **Precaución:** Como has generado huecos, debes usar el método `EXISTS(i)` antes de intentar imprimir cada elemento para evitar errores.

<details>
<summary>Ver solución</summary>

```sql
DECLARE
    -- 1. Declaración e inicialización
    TYPE t_proyectos IS TABLE OF VARCHAR2(100);
    v_lista_proy t_proyectos := t_proyectos();
BEGIN
    -- 2. Llenado de la tabla anidada
    FOR rec IN (SELECT nombre FROM PROYECTOS) LOOP
        v_lista_proy.EXTEND;
        v_lista_proy(v_lista_proy.LAST) := rec.nombre;
    END LOOP;
    
    -- 3. Borramos los índices pares para generar "huecos"
    FOR i IN 1 .. v_lista_proy.COUNT LOOP
        IF MOD(i, 2) = 0 THEN
            v_lista_proy.DELETE(i);
        END IF;
    END LOOP;
    
    -- 4. Impresión segura comprobando EXISTS(i)
    DBMS_OUTPUT.PUT_LINE('--- PROYECTOS ACTIVOS (SIN ÍNDICES PARES) ---');
    FOR i IN 1 .. v_lista_proy.LAST LOOP
        IF v_lista_proy.EXISTS(i) THEN
            DBMS_OUTPUT.PUT_LINE('Índice ' || i || ': ' || v_lista_proy(i));
        END IF;
    END LOOP;
END;
/

```

</details>

---

### Ejercicio 5: Ciclo de vida del Cursor Explícito (Simple)

**Objetivo:** Practicar la gestión manual completa de un cursor: declaración, apertura, extracción y cierre.

**Enunciado:**
Escribe un bloque PL/SQL que recorra todos los departamentos de la empresa utilizando un cursor explícito sin parámetros.

1. Declara el cursor en la sección `DECLARE` para seleccionar el `nombre` y `presupuesto` de `DEPARTAMENTOS`.
2. En la sección `BEGIN`, abre el cursor (`OPEN`).
3. Utiliza un bucle `LOOP` básico para extraer los datos fila a fila (`FETCH`) en variables locales o en un registro.
4. Establece la condición de salida del bucle cuando el cursor no encuentre más datos (`%NOTFOUND`).
5. Imprime el nombre y presupuesto de cada departamento.
6. Cierra el cursor (`CLOSE`) al finalizar el bucle.

<details>
<summary>Ver solución</summary>

```sql
DECLARE
    -- 1. Declaración del cursor
    CURSOR cur_dptos IS
        SELECT nombre, presupuesto FROM DEPARTAMENTOS;
    
    -- Variables para el FETCH
    v_nom  DEPARTAMENTOS.nombre%TYPE;
    v_pres DEPARTAMENTOS.presupuesto%TYPE;
BEGIN
    -- 2. Apertura
    OPEN cur_dptos;
    
    DBMS_OUTPUT.PUT_LINE('--- LISTADO DE DEPARTAMENTOS ---');
    LOOP
        -- 3. Extracción manual
        FETCH cur_dptos INTO v_nom, v_pres;
        
        -- 4. Condición de salida
        EXIT WHEN cur_dptos%NOTFOUND;
        
        -- 5. Procesamiento
        DBMS_OUTPUT.PUT_LINE('Dpto: ' || v_nom || ' | Presupuesto: ' || v_pres || ' €');
    END LOOP;
    
    -- 6. Cierre
    CLOSE cur_dptos;
END;
/

```

</details>

---

### Ejercicio 6: Cursores Explícitos con Parámetros

**Objetivo:** Flexibilizar las consultas mediante el paso de argumentos al cursor al momento de su apertura.

**Enunciado:**
Declara un cursor explícito parametrizado que reciba un número de departamento (`p_id_dpto NUMBER`) y devuelva el `nombre`, `apellidos` y `puesto` de los empleados de dicho departamento.

1. Declara una variable basada en el `%ROWTYPE` del propio cursor.
2. Abre el cursor para el departamento `30` pasándole el valor como parámetro.
3. Utiliza un bucle `LOOP` y `FETCH` para mostrar los datos de los empleados.
4. Antes de cerrar el cursor, imprime cuántas filas se han procesado utilizando el atributo `%ROWCOUNT`.

<details>
<summary>Ver solución</summary>

```sql
DECLARE
    -- Declaración con parámetro
    CURSOR cur_emp_dpto(p_id_dpto NUMBER) IS
        SELECT nombre, apellidos, puesto
        FROM EMPLEADOS
        WHERE id_dpto = p_id_dpto;
        
    -- Registro basado en el cursor
    v_fila cur_emp_dpto%ROWTYPE;
BEGIN
    DBMS_OUTPUT.PUT_LINE('--- EMPLEADOS DEL DEPARTAMENTO 30 ---');
    
    -- Apertura con argumento
    OPEN cur_emp_dpto(30);
    
    LOOP
        FETCH cur_emp_dpto INTO v_fila;
        EXIT WHEN cur_emp_dpto%NOTFOUND;
        
        DBMS_OUTPUT.PUT_LINE(v_fila.nombre || ' ' || v_fila.apellidos || ' (' || v_fila.puesto || ')');
    END LOOP;
    
    -- Uso de %ROWCOUNT
    DBMS_OUTPUT.PUT_LINE('Total de empleados en el dpto: ' || cur_emp_dpto%ROWCOUNT);
    
    CLOSE cur_emp_dpto;
END;
/

```

</details>

---

### Ejercicio 7: Cursores de Actualización (`FOR UPDATE`) y Bloques Seguros

**Objetivo:** Realizar actualizaciones transaccionales seguras bloqueando las filas y gestionando excepciones para evitar bloqueos infinitos.

**Enunciado:**
Para actualizar datos de forma segura usamos `FOR UPDATE`.

1. Declara un cursor que seleccione los empleados del departamento `10` y bloquéalos añadiendo `FOR UPDATE NOWAIT`.
2. Recorre el cursor y, para cada empleado, aumenta su salario en `1.000` € mediante un `UPDATE` que utilice `WHERE CURRENT OF nombre_cursor`.
3. Al finalizar, realiza un `COMMIT` y cierra el cursor.
4. **Importante:** Añade un bloque `EXCEPTION`. Si ocurre un error, haz un `ROLLBACK`, comprueba si el cursor sigue abierto con `%ISOPEN` para cerrarlo, e informa del error con `SQLERRM`.

<details>
<summary>Ver solución</summary>

```sql
DECLARE
    -- Cursor de bloqueo
    CURSOR cur_revision IS
        SELECT nombre, salario
        FROM EMPLEADOS
        WHERE id_dpto = 10
        FOR UPDATE NOWAIT;
        
    v_emp cur_revision%ROWTYPE;
BEGIN
    OPEN cur_revision;
    
    LOOP
        FETCH cur_revision INTO v_emp;
        EXIT WHEN cur_revision%NOTFOUND;
        
        -- Actualización dirigida
        UPDATE EMPLEADOS
        SET salario = salario + 1000
        WHERE CURRENT OF cur_revision;
        
        DBMS_OUTPUT.PUT_LINE('Salario de ' || v_emp.nombre || ' actualizado.');
    END LOOP;
    
    COMMIT;
    CLOSE cur_revision;

EXCEPTION
    WHEN OTHERS THEN
        ROLLBACK;
        DBMS_OUTPUT.PUT_LINE('❌ Error en la transacción: ' || SQLERRM);
        
        -- Limpieza de seguridad
        IF cur_revision%ISOPEN THEN
            CLOSE cur_revision;
        END IF;
END;
/

```

</details>
