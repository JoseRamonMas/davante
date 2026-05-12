# Ejercicios de repaso UT15: El lenguaje PL/SQL

> **Nota introductoria:** Para realizar estos ejercicios, asumimos que estamos trabajando sobre el esquema del **Sistema Hospitalario** visto en la teoría (tablas `PACIENTES`, `MEDICOS`, `CITAS`, `MEDICAMENTOS` y `PRESCRIPCIONES` con datos ya insertados). Activa siempre la salida de consola en tu entorno de desarrollo (con `SET SERVEROUTPUT ON` en SQL*Plus/SQLcl o activando la ventana *DBMS Output* en SQL Developer).

---

### Ejercicio 1: Variables, atributos `%ROWTYPE` y cálculos básicos

**Objetivo:** Repasar la declaración de variables referenciadas a la estructura de la base de datos y la realización de cálculos aritméticos sencillos con funciones integradas.

**Enunciado:**
Escribe un bloque PL/SQL anónimo que recupere la información completa del medicamento con `id_medicamento = 1` utilizando el atributo `%ROWTYPE` y la sentencia `SELECT INTO`. Además, declara una constante para aplicar un IVA sanitario del 4%.

El bloque debe calcular el precio final del medicamento sumando el IVA al precio base y redondear el resultado a 2 decimales. Muestra por pantalla el nombre del medicamento, su precio base y su precio final con IVA.

<details>
</summary>Ver solución</summary>

```sql
DECLARE
    r_medicamento MEDICAMENTOS%ROWTYPE;
    c_iva         CONSTANT NUMBER(4,2) := 0.04;
    v_precio_iva  NUMBER(6,2);
BEGIN
    -- Recuperamos toda la fila del medicamento 1
    SELECT * INTO r_medicamento
    FROM MEDICAMENTOS
    WHERE id_medicamento = 1;
    
    -- Calculamos el precio con IVA redondeado a 2 decimales
    v_precio_iva := ROUND(r_medicamento.precio_unidad * (1 + c_iva), 2);
    
    -- Mostramos los resultados
    DBMS_OUTPUT.PUT_LINE('--- DATOS DEL MEDICAMENTO ---');
    DBMS_OUTPUT.PUT_LINE('Nombre: ' || r_medicamento.nombre);
    DBMS_OUTPUT.PUT_LINE('Precio Base: ' || r_medicamento.precio_unidad || ' €');
    DBMS_OUTPUT.PUT_LINE('Precio Final (IVA 4%): ' || v_precio_iva || ' €');
END;
/

```

</details>


---

### Ejercicio 2: Estructuras condicionales (IF / CASE)

**Objetivo:** Afianzar el control de flujo y la toma de decisiones basada en datos recuperados de la base de datos.

**Enunciado:**
Vamos a categorizar a un médico según su especialidad para asignarle una etiqueta de "Prioridad de Guardia". Desarrolla un bloque PL/SQL que recupere el `nombre`, `apellidos` y `especialidad` del médico con `id_medico = 2` utilizando anclaje de tipos (`%TYPE`).

Utiliza una estructura `CASE` (como sentencia o como expresión) para evaluar su especialidad:

* Si es `'URGENCIAS'` o `'TRAUMA'`, la prioridad será `'ALTA'`.
* Si es `'CARDIO'` o `'NEURO'`, la prioridad será `'MEDIA'`.
* Para cualquier otra especialidad, la prioridad será `'BAJA'`.

Imprime por pantalla el nombre completo del médico y la prioridad de guardia asignada.

<details>
</summary>Ver solución</summary>

```sql
DECLARE
    v_nombre       MEDICOS.nombre%TYPE;
    v_apellidos    MEDICOS.apellidos%TYPE;
    v_especialidad MEDICOS.especialidad%TYPE;
    v_prioridad    VARCHAR2(20);
BEGIN
    -- Recuperamos los datos del médico
    SELECT nombre, apellidos, especialidad
    INTO v_nombre, v_apellidos, v_especialidad
    FROM MEDICOS
    WHERE id_medico = 2;
    
    -- Evaluamos la especialidad con CASE
    CASE v_especialidad
        WHEN 'URGENCIAS' THEN v_prioridad := 'ALTA';
        WHEN 'TRAUMA'    THEN v_prioridad := 'ALTA';
        WHEN 'CARDIO'    THEN v_prioridad := 'MEDIA';
        WHEN 'NEURO'     THEN v_prioridad := 'MEDIA';
        ELSE                  v_prioridad := 'BAJA';
    END CASE;
    
    DBMS_OUTPUT.PUT_LINE('Dr/Dra. ' || v_nombre || ' ' || v_apellidos);
    DBMS_OUTPUT.PUT_LINE('Especialidad: ' || v_especialidad);
    DBMS_OUTPUT.PUT_LINE('Prioridad de Guardia: ' || v_prioridad);
END;
/

```

</details>

---

### Ejercicio 3: Bucles básicos (`WHILE` o `LOOP`) sin consultas a BD

**Objetivo:** Entender cómo controlar la ejecución repetitiva basándose en variables y condiciones que cambian dinámicamente en memoria.

**Enunciado:**
Un paciente necesita recibir dosis de un medicamento cada 8 horas. Crea un bloque PL/SQL que simule la cuenta atrás de un frasco de jarabe. Partiendo de una variable que simule un frasco con `100` ml iniciales, utiliza un bucle `WHILE` o un `LOOP` con `EXIT WHEN` para restar `15` ml por cada dosis administrada.

El bucle debe detenerse cuando quede menos cantidad que la dosis necesaria (< 15 ml). En cada iteración imprime el número de dosis administrada y el líquido restante. Al salir, muestra el sobrante final del frasco.

<details>
</summary>Ver solución</summary>

```sql
DECLARE
    v_frasco_ml    NUMBER := 100;
    c_dosis_ml     CONSTANT NUMBER := 15;
    v_num_dosis    PLS_INTEGER := 0;
BEGIN
    DBMS_OUTPUT.PUT_LINE('Comenzando tratamiento (Frasco: 100ml)...');
    
    -- Mientras haya suficiente líquido para al menos una dosis más
    WHILE v_frasco_ml >= c_dosis_ml LOOP
        v_frasco_ml := v_frasco_ml - c_dosis_ml;
        v_num_dosis := v_num_dosis + 1;
        
        DBMS_OUTPUT.PUT_LINE('Dosis #' || v_num_dosis || ' administrada. Quedan: ' || v_frasco_ml || ' ml.');
    END LOOP;
    
    DBMS_OUTPUT.PUT_LINE('-----------------------------------');
    DBMS_OUTPUT.PUT_LINE('Tratamiento finalizado.');
    DBMS_OUTPUT.PUT_LINE('Total dosis administradas: ' || v_num_dosis);
    DBMS_OUTPUT.PUT_LINE('Sobrante en el frasco: ' || v_frasco_ml || ' ml (insuficiente para otra dosis).');
END;
/

```

</details>

---

### Ejercicio 4: Bucle numérico (`FOR`) e integración de consultas (`SELECT INTO`)

**Objetivo:** Utilizar un bucle numérico para generar identificadores secuenciales y realizar múltiples consultas individuales a la base de datos, combinándolo con lógicas condicionales.

**Enunciado:**
El hospital quiere clasificar a un pequeño grupo de prueba para una campaña de vacunación. Utiliza un bucle `FOR` **numérico** que itere del `1` al `5` para simular la búsqueda de los pacientes con `id_paciente` del 1 al 5.

Dentro del bucle, utiliza un `SELECT INTO` para obtener el `nombre`, `apellidos` y la `fecha_nac` del paciente cuyo ID coincida con el contador del bucle. Calcula su edad truncando los meses entre la fecha actual y su fecha de nacimiento.

* Si el paciente tiene 65 años o más, imprime: `[Nombre completo] - Edad: [X] - GRUPO DE RIESGO`.
* Si tiene menos, imprime: `[Nombre completo] - Edad: [X] - ESTÁNDAR`.

*Nota importante:* Es posible que en esa secuencia numérica algún ID haya sido borrado y no exista. Envuelve tu `SELECT INTO` en un sub-bloque `BEGIN ... EXCEPTION ... END;` dentro del bucle para capturar el error `NO_DATA_FOUND` e imprimir un mensaje indicando que ese ID no existe, permitiendo que el bucle continúe con el siguiente número.

<details>
</summary>Ver solución</summary>

```sql
DECLARE
    v_nombre    PACIENTES.nombre%TYPE;
    v_apellidos PACIENTES.apellidos%TYPE;
    v_fecha_nac PACIENTES.fecha_nac%TYPE;
    v_edad      NUMBER;
BEGIN
    DBMS_OUTPUT.PUT_LINE('--- CAMPAÑA DE VACUNACIÓN (Muestra 1 al 5) ---');
    
    FOR i IN 1..5 LOOP
        BEGIN
            -- Intentamos recuperar el paciente con el ID del contador actual
            SELECT nombre, apellidos, fecha_nac
            INTO v_nombre, v_apellidos, v_fecha_nac
            FROM PACIENTES
            WHERE id_paciente = i;
            
            -- Calculamos la edad truncando los decimales
            v_edad := TRUNC(MONTHS_BETWEEN(SYSDATE, v_fecha_nac) / 12);
            
            IF v_edad >= 65 THEN
                DBMS_OUTPUT.PUT_LINE('ID ' || i || ': ' || v_nombre || ' ' || v_apellidos || 
                                     ' - Edad: ' || v_edad || ' - GRUPO DE RIESGO');
            ELSE
                DBMS_OUTPUT.PUT_LINE('ID ' || i || ': ' || v_nombre || ' ' || v_apellidos || 
                                     ' - Edad: ' || v_edad || ' - ESTÁNDAR');
            END IF;
            
        EXCEPTION
            WHEN NO_DATA_FOUND THEN
                -- Si el ID no existe, informamos y el bucle FOR pasa al siguiente número
                DBMS_OUTPUT.PUT_LINE('ID ' || i || ': [No asignado o paciente borrado]');
        END;
    END LOOP;
END;
/

```

</details>

---

### Ejercicio 5: Manejo de Excepciones Predefinidas

**Objetivo:** Capturar y gestionar errores para evitar que un script aborte inesperadamente, ofreciendo mensajes de diagnóstico.

**Enunciado:**
Escribe un bloque PL/SQL que intente recuperar el `estado` de la cita cuyo `id_cita = 8888` (asume que este ID no existe en la base de datos).

Añade un bloque de excepciones que capture específicamente el error que ocurre cuando una instrucción `SELECT INTO` no devuelve resultados e imprime el mensaje: `⚠️ ALERTA: La cita solicitada no consta en nuestros registros.`. Añade también una captura general (`WHEN OTHERS`) para cubrir errores imprevistos.

<details>
</summary>Ver solución</summary>

```sql
DECLARE
    v_estado_cita CITAS.estado%TYPE;
    v_id_buscado  NUMBER := 8888;
BEGIN
    -- Intentamos buscar una cita que no existe
    SELECT estado 
    INTO v_estado_cita
    FROM CITAS
    WHERE id_cita = v_id_buscado;
    
    -- Si la encontrase, lo imprimiría
    DBMS_OUTPUT.PUT_LINE('El estado de la cita es: ' || v_estado_cita);
    
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('⚠️ ALERTA: La cita solicitada (ID: ' || v_id_buscado || ') no consta en nuestros registros.');
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('❌ Error imprevisto: ' || SQLCODE || ' - ' || SQLERRM);
END;
/

```

</details>

---

### Ejercicio 6: Integración Final (Variables, DML, Condicionales y Excepciones de Usuario)

**Objetivo:** Consolidar todos los conceptos del tema simulando una operación transaccional completa.

**Enunciado:**
Vamos a simular la dispensación de **3 unidades** del medicamento con `id_medicamento = 5`. Desarrolla un bloque PL/SQL que cumpla los siguientes pasos:

1. Declara una excepción de usuario llamada `e_stock_insuficiente`.
2. Utiliza `SELECT INTO` para obtener el `nombre` y el `stock` actual de dicho medicamento.
3. Utiliza un `IF` para comprobar el stock. Si es menor a las 3 unidades que queremos retirar, lanza tu excepción `e_stock_insuficiente`.
4. Si hay stock suficiente, realiza un `UPDATE` restando esas 3 unidades al stock actual en la tabla `MEDICAMENTOS`. Imprime un mensaje indicando que la dispensación ha sido exitosa y el stock restante.
5. En la sección principal de `EXCEPTION`, captura tu `e_stock_insuficiente` informando del problema (Ej: *Stock insuficiente para dispensar*). Captura también `NO_DATA_FOUND` por si el medicamento 5 no existiera en la base de datos. Haz un `ROLLBACK` en caso de error y un `COMMIT` en caso de éxito.

<details>
</summary>Ver solución</summary>

```sql
DECLARE
    v_id_med           NUMBER := 5;
    v_nombre_med       MEDICAMENTOS.nombre%TYPE;
    v_stock_actual     MEDICAMENTOS.stock%TYPE;
    c_cantidad_solicit CONSTANT NUMBER := 3;
    
    -- 1. Declaración de la excepción de usuario
    e_stock_insuficiente EXCEPTION;
BEGIN
    -- 2. Recuperamos los datos del medicamento
    SELECT nombre, stock 
    INTO v_nombre_med, v_stock_actual
    FROM MEDICAMENTOS
    WHERE id_medicamento = v_id_med;
    
    -- 3. Comprobación de negocio
    IF v_stock_actual < c_cantidad_solicit THEN
        RAISE e_stock_insuficiente;
    END IF;
    
    -- 4. Si llegamos aquí, hay stock suficiente. Hacemos el UPDATE.
    UPDATE MEDICAMENTOS
    SET stock = stock - c_cantidad_solicit
    WHERE id_medicamento = v_id_med;
    
    DBMS_OUTPUT.PUT_LINE('✅ Dispensación exitosa: ' || c_cantidad_solicit || ' unidades de ' || v_nombre_med);
    DBMS_OUTPUT.PUT_LINE('Stock restante: ' || (v_stock_actual - c_cantidad_solicit) || ' unidades.');
    
    COMMIT; -- Confirmamos los cambios

EXCEPTION
    -- 5. Manejo de excepciones
    WHEN e_stock_insuficiente THEN
        ROLLBACK;
        DBMS_OUTPUT.PUT_LINE('❌ ERROR: Stock insuficiente. Se intentaron retirar ' || c_cantidad_solicit || 
                             ' unidades de ' || v_nombre_med || ' pero solo hay ' || v_stock_actual || ' disponibles.');
                             
    WHEN NO_DATA_FOUND THEN
        ROLLBACK;
        DBMS_OUTPUT.PUT_LINE('❌ ERROR: El medicamento con ID ' || v_id_med || ' no existe en la base de datos.');
        
    WHEN OTHERS THEN
        ROLLBACK;
        DBMS_OUTPUT.PUT_LINE('❌ Error inesperado: ' || SQLERRM);
END;
/

```

</details>

