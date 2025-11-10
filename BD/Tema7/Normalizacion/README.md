# Normalización de BD

> 🎯 **Objetivo**: Comprender el proceso de normalización y aplicarlo correctamente en el diseño de bases de datos relacionales.

## ¿Qué es la Normalización?

La **normalización** es un proceso fundamental en el diseño de bases de datos que tiene como objetivo organizar los datos de forma eficiente, eliminando redundancias y garantizando la integridad de la información.

Imagina que tienes un cajón desordenado donde guardas de todo sin ningún criterio. Sería difícil encontrar algo específico, ¿verdad? La normalización es como organizar ese cajón en compartimentos lógicos, donde cada cosa tiene su lugar específico.

## ¿Por qué normalizar?

Imagina que tienes una tabla donde guardas información de estudiantes y sus cursos, y cada vez que un estudiante se inscribe en un curso nuevo, tienes que repetir su nombre, dirección y teléfono. ¿Qué pasa si ese estudiante cambia de dirección? Tendrías que actualizar múltiples filas, y si olvidas alguna, tendrás **datos inconsistentes**. La normalización resuelve estos problemas mediante tres objetivos fundamentales.


### Objetivos principales

1. **Eliminar la redundancia de datos**: Evitar que la misma información se almacene en múltiples lugares, lo que ahorra espacio y previene inconsistencias.
2. **Evitar anomalías en las operaciones**: Garantizar que las inserciones, actualizaciones y eliminaciones se realicen correctamente sin perder información ni generar inconsistencias.
3. **Facilitar el mantenimiento**: Una base de datos bien normalizada es más fácil de mantener y modificar.

## Dependencias funcionales

Antes de entrar en las formas normales, es imprescindible entender el concepto de **dependencias funcionales**, ya que son la piedra angular de la normalización.

### ¿Qué es una Dependencia Funcional?

Una dependencia funcional es una relación entre atributos. Describe cómo un atributo (o conjunto de atributos) determina de manera única el valor de otro atributo dentro de una tabla. Si decimos que un atributo `B` depende funcionalmente de un atributo `A` (lo que se escribe como `A -> B`), significa que si conocemos el valor de `A`, podemos saber **de forma única** el valor de `B`.

**Notación**: Si el atributo A determina al atributo B, lo escribimos como: **A → B**

**Ejemplo sencillo**: En una tabla de estudiantes:

```
DNI → Nombre
```

Esto significa que conociendo el DNI de un estudiante, podemos determinar de manera única su nombre. En cambio, conociendo el nombre no puedo determinar de manera única su DNI.


### Tipos de dependencias funcionales

#### 1️⃣ Dependencia funcional completa

Se produce cuando un atributo (o conjunto de atributos) depende **completamente** de toda la clave primaria y no solo de una parte de ella. *Este concepto es importante cuando tenemos claves primarias compuestas (formadas por más de un atributo).*


**Ejemplo práctico 1**:

```
EMPLEADOS
┌────────┬─────────────────┬────────────┐
│ DNI    │ Nombre          │ Teléfono   │
├────────┼─────────────────┼────────────┤
│ 12345A │ Ana García      │ 600111222  │
│ 67890B │ Carlos Ruiz     │ 600333444  │
└────────┴─────────────────┴────────────┘
```

Aquí: `DNI → Nombre` y `DNI → Teléfono` son dependencias funcionales completas, porque el DNI determina completamente estos atributos.

**Ejemplo práctico 2**:

Imagina una tabla de matrículas universitarias:

| ID_Estudiante | ID_Asignatura | Nombre_Estudiante | Nota | Fecha_Matricula |
| :-- | :-- | :-- | :-- | :-- |
| 1001 | BD101 | Ana García | 8.5 | 2024-09-15 |
| 1001 | PRG102 | Ana García | 7.0 | 2024-09-15 |
| 1002 | BD101 | Carlos López | 9.0 | 2024-09-16 |

Aquí la clave primaria es **(ID_Estudiante, ID_Asignatura)**.

- **Dependencia completa**: `(ID_Estudiante, ID_Asignatura) → Nota` (necesitas ambos datos para saber la nota)
- **Dependencia completa**: `(ID_Estudiante, ID_Asignatura) → Fecha_Matricula`


#### 2️⃣ Dependencia funcional parcial

Ocurre cuando un atributo depende **solo de una parte** de la clave primaria compuesta, no de toda ella.

Siguiendo el ejemplo anterior:

- **Dependencia parcial**: `ID_Estudiante → Nombre_Estudiante`

¿Por qué es parcial? Porque el nombre del estudiante depende **únicamente** del ID_Estudiante, no necesitas conocer también el ID_Asignatura. 

**Otro ejemplo clarificador**:

Tenemos una discográfica con la siguiente tabla:


| Álbum | Autor | Num_Canciones | Nacionalidad_Autor |
| :-- | :-- | :-- | :-- |
| Thriller | Michael Jackson | 9 | Estadounidense |
| Bad | Michael Jackson | 11 | Estadounidense |

La clave primaria es **(Álbum, Autor)**.

- `Álbum → Num_Canciones` (solo depende del álbum, no del autor)
- `Autor → Nacionalidad_Autor` (solo depende del autor, no del álbum)

Ninguno de estos atributos depende de la clave completa, sino solo de una parte. **Esto es una dependencia parcial**.

#### 3️⃣ Dependencia funcional transitiva

Se produce cuando un atributo depende de otro atributo que **no es clave**, creando una cadena de dependencias.

**Estructura**: `A → B` y `B → C`, entonces `A → C` (transitiva)

**Ejemplo práctico**:

Tabla de empleados:


| DNI | Nombre | Cod_Provincia | Nombre_Provincia |
| :-- | :-- | :-- | :-- |
| 11111111X | Serafín | 28 | Madrid |
| 22222222H | Pablo | 13 | Ciudad Real |

Aquí:

- `DNI → Cod_Provincia`
- `Cod_Provincia → Nombre_Provincia`
- Por lo tanto: `DNI → Nombre_Provincia` (transitiva)

El problema es que `Nombre_Provincia` no depende directamente de la clave primaria (DNI), sino que depende de `Cod_Provincia`.

**Ejemplo práctico 2**:


```
ESTUDIANTES
┌──────────────┬─────────────┬──────────────┬─────────────────────┐
│ NumMatricula │ Nombre      │ CodGrupo     │ AulaGrupo           │
├──────────────┼─────────────┼──────────────┼─────────────────────┤
│ 2024001      │ Laura López │ DAM1A        │ Aula 101            │
│ 2024002      │ Pedro Sanz  │ DAM1A        │ Aula 101            │
│ 2024003      │ Marta Gil   │ DAM1B        │ Aula 102            │
└──────────────┴─────────────┴──────────────┴─────────────────────┘
```

Problema: `AulaGrupo` depende de `CodGrupo`, no directamente de `NumMatricula`:

- `NumMatricula → CodGrupo` 
- `CodGrupo → AulaGrupo` 
- Por tanto: `NumMatricula → AulaGrupo` (transitiva)

> ⚠️ Si el grupo DAM1A cambia al Aula 103, tendríamos que actualizar múltiples filas de estudiantes.

## Las Formas Normales: Paso a Paso

Las formas normales son niveles progresivos de normalización. Cada forma normal **requiere** que se cumplan todas las anteriores.

> ⚠️ **Importante**: Para que una tabla cumpla una forma normal, debe cumplir primero todas las anteriores.

### 1️⃣ Primera Forma Normal (1FN)

> Una tabla está en 1FN si todos sus atributos son **atómicos**.

La 1FN requiere que todos los atributos sean **atómicos** (sin listas o multivalores) y cada fila sea única. No se permiten atributos multivaluados ni grupos repetitivos.

#### ❌ Tabla que NO cumple 1FN:

| ID_Estudiante | Nombre | Teléfonos | Cursos |
| :-- | :-- | :-- | :-- |
| 1001 | Ana García | 600111222, 910333444 | Matemáticas, Física |
| 1002 | Carlos López | 655777888 | Historia |

**Problemas**:

- El campo `Teléfonos` contiene múltiples valores separados por comas
- El campo `Cursos` también contiene múltiples valores


#### ✅ Tabla que SÍ cumple 1FN:

**Tabla ESTUDIANTES**:


| ID_Estudiante | Nombre | Teléfono | Curso |
| :-- | :-- | :-- | :-- |
| 1001 | Ana García | 600111222 | Matemáticas |
| 1001 | Ana García | 910333444 | Matemáticas |
| 1001 | Ana García | 600111222 | Física |
| 1002 | Carlos López | 655777888 | Historia |

Ahora cada campo contiene un único valor atómico.

#### ❌ Tabla que NO cumple 1FN:

| ID_Cliente | Nombre | Telefonos |
| :-- | :-- | :-- |
| C01 | Pedro | 666111222, 915554433 |
| C02 | Laura | 677888999 |

El campo `Telefonos` no es atómico, ya que puede contener varios números

#### ✅ Tabla que SÍ cumple 1FN:

| ID_Cliente | Nombre | Telefonos |
| :-- | :-- | :-- |
| C01 | Pedro | 666111222 |
| C01 | Pedro | 915554433 |
| C02 | Laura | 677888999 |

### 2️⃣ Segunda Forma Normal (2FN)

Debe estar en 1FN **Y** eliminar todas las **dependencias funcionales parciales**.

Esto significa que cada atributo que no sea clave debe depender de **toda** la clave primaria, no solo de una parte.

#### ❌ Tabla que NO cumple 2FN:

```
PEDIDOS
┌──────────────┬──────────────┬──────────────────┬──────────┬──────────┐
│ NumPedido    │ CodProducto  │ NombreProducto   │ Precio   │ Cantidad │
├──────────────┼──────────────┼──────────────────┼──────────┼──────────┤
│ P001         │ PR100        │ Teclado RGB      │ 45.00    │ 2        │
│ P001         │ PR200        │ Ratón óptico     │ 15.00    │ 5        │
│ P002         │ PR100        │ Teclado RGB      │ 45.00    │ 1        │
└──────────────┴──────────────┴──────────────────┴──────────┴──────────┘

```

**Clave primaria**: (NumPedido, CodProducto)

**Problemas detectados**:

- `CodProducto → NombreProducto` (dependencia parcial)
- `CodProducto → Precio` (dependencia parcial)

#### ✅ Tablas que SÍ cumplen 2FN:

```
PRODUCTOS
┌──────────────┬──────────────────┬──────────┐
│ CodProducto  │ NombreProducto   │ Precio   │
├──────────────┼──────────────────┼──────────┤
│ PR100        │ Teclado RGB      │ 45.00    │
│ PR200        │ Ratón óptico     │ 15.00    │
└──────────────┴──────────────────┴──────────┘

LINEAS_PEDIDO
┌──────────────┬──────────────┬──────────┐
│ NumPedido    │ CodProducto  │ Cantidad │
├──────────────┼──────────────┼──────────┤
│ P001         │ PR100        │ 2        │
│ P001         │ PR200        │ 5        │
│ P002         │ PR100        │ 1        │
└──────────────┴──────────────┴──────────┘
```

Ahora `NombreProducto` y `Precio` están solo en la tabla PRODUCTOS, eliminando la redundancia

#### ❌ Tabla que NO cumple 2FN:

| ID_Estudiante | ID_Asignatura | Nombre_Estudiante | Nombre_Asignatura | Nota |
| :-- | :-- | :-- | :-- | :-- |
| 1001 | BD101 | Ana García | Bases de Datos | 8.5 |
| 1001 | PRG102 | Ana García | Programación | 7.0 |
| 1002 | BD101 | Carlos López | Bases de Datos | 9.0 |

**Clave primaria**: (ID_Estudiante, ID_Asignatura)

**Problemas detectados**:

- `ID_Estudiante → Nombre_Estudiante` (dependencia parcial)
- `ID_Asignatura → Nombre_Asignatura` (dependencia parcial)

Estos atributos no necesitan conocer la clave completa, solo una parte.

#### ✅ Tablas que SÍ cumplen 2FN:

**Tabla ESTUDIANTES**:


| ID_Estudiante | Nombre_Estudiante |
| :-- | :-- |
| 1001 | Ana García |
| 1002 | Carlos López |

**Tabla ASIGNATURAS**:


| ID_Asignatura | Nombre_Asignatura |
| :-- | :-- |
| BD101 | Bases de Datos |
| PRG102 | Programación |

**Tabla MATRICULAS**:


| ID_Estudiante | ID_Asignatura | Nota |
| :-- | :-- | :-- |
| 1001 | BD101 | 8.5 |
| 1001 | PRG102 | 7.0 |
| 1002 | BD101 | 9.0 |

Ahora todos los atributos no clave dependen **completamente** de su respectiva clave primaria.

**Ventajas obtenidas**:

- ✅ Eliminamos redundancia (los nombres no se repiten)
- ✅ Podemos tener estudiantes sin matrículas
- ✅ Podemos tener asignaturas sin estudiantes matriculados
- ✅ Actualizar el nombre de un estudiante es más sencillo


### 3️⃣ Tercera Forma Normal (3FN)

Debe estar en 2FN **Y** eliminar todas las **dependencias transitivas**.

Ningún atributo que no sea clave puede depender de otro atributo que no sea clave.

#### ❌ Tabla que NO cumple 3FN:

```
EMPLEADOS
┌──────────┬─────────────────┬──────────────┬───────────────────┐
│ DNI      │ Nombre          │ CodProvincia │ NombreProvincia   │
├──────────┼─────────────────┼──────────────┼───────────────────┤
│ 11111X   │ Serafín Moreno  │ 28           │ Madrid            │
│ 22222H   │ Pablo Jiménez   │ 13           │ Ciudad Real       │
│ 33333M   │ Ana Ruiz        │ 28           │ Madrid            │
└──────────┴─────────────────┴──────────────┴───────────────────┘
```

**Problema**: `NombreProvincia` depende de `CodProvincia`, no directamente de `DNI`

#### ✅ Tablas que SÍ cumplen 3FN:

```
EMPLEADOS
┌──────────┬─────────────────┬──────────────┐
│ DNI      │ Nombre          │ CodProvincia │
├──────────┼─────────────────┼──────────────┤
│ 11111X   │ Serafín Moreno  │ 28           │
│ 22222H   │ Pablo Jiménez   │ 13           │
│ 33333M   │ Ana Ruiz        │ 28           │
└──────────┴─────────────────┴──────────────┘

PROVINCIAS
┌──────────────┬───────────────────┐
│ CodProvincia │ NombreProvincia   │
├──────────────┼───────────────────┤
│ 28           │ Madrid            │
│ 13           │ Ciudad Real       │
└──────────────┴───────────────────┘
```

Ahora si cambia el nombre de una provincia, solo actualizamos un registro en la tabla PROVINCIAS

#### ❌ Tabla que NO cumple 3FN:

| ID_Cliente | Nombre_Cliente | Cod_Ciudad | Nombre_Ciudad | Cod_Postal |
| :-- | :-- | :-- | :-- | :-- |
| 1 | Juan Pérez | 28 | Madrid | 28001 |
| 2 | María López | 08 | Barcelona | 08001 |
| 3 | Ana Ruiz | 28 | Madrid | 28015 |

**Clave primaria**: ID_Cliente

**Problemas**:

- `Nombre_Ciudad` depende de `Cod_Ciudad`, no directamente de la clave primaria.
- `Cod_Postal` depende de `Cod_Ciudad`, no directamente de la clave primaria.

#### ✅ Tablas que SÍ cumplen 3FN:

**Tabla CLIENTES**:


| ID_Cliente | Nombre_Cliente | Cod_Ciudad |
| :-- | :-- | :-- |
| 1 | Juan Pérez | 28 |
| 2 | María López | 08 |
| 3 | Ana Ruiz | 28 |

**Tabla CIUDADES**:


| Cod_Ciudad | Nombre_Ciudad | Cod_Postal_Base |
| :-- | :-- | :-- |
| 28 | Madrid | 28000 |
| 08 | Barcelona | 08000 |

Ahora todos los atributos no clave dependen **directamente** de su clave primaria, sin dependencias intermedias.

### 🔸 Forma Normal de Boyce-Codd (FNBC)

La FNBC es una versión **más estricta** de la 3FN. Para cumplirla, en **toda** dependencia funcional, el lado izquierdo (determinante) debe ser una **clave candidata**.

**Regla**: Para toda dependencia funcional `X → Y`, X debe ser una superclave.

> Según la **definición de Zaniolo de 3FN**: Una tabla está en 3FN si para cada dependencia funcional X → A, se cumple al menos una de estas condiciones:
>
> 1. X es una superclave, O
> 2. A es un atributo primo (parte de alguna clave candidata)
>
> **FNBC elimina la segunda opción**. En FNBC, para TODA dependencia funcional X → Y, X **debe ser una superclave**. No hay excepciones.


#### ❌ Tabla que NO cumple FNBC:

**Tabla: TUTORÍAS**


| Estudiante | Asignatura | Profesor |
| :-- | :-- | :-- |
| Juan | Matemáticas | López |
| Ana | Física | García |

**Claves candidatas**: {Estudiante, Asignatura} y {Estudiante, Profesor} (asumiendo que cada estudiante tiene un solo profesor por asignatura y cada profesor enseña una sola asignatura)

**Dependencia problemática**: Profesor → Asignatura

Aquí, `Asignatura` es un atributo primo (parte de una clave candidata), por lo que **cumple 3FN**. Pero `Profesor` no es superclave, por lo que **viola FNBC**.

#### ✅ Tablas que SÍ cumplen FNBC:

**1. Profesores:**


| Profesor | Asignatura |
| :-- | :-- |
| López | Matemáticas |
| García | Física |

Ahora, la clave primaria es `Profesor`, y la dependencia Profesor → Asignatura es válida. *Asignatura sería clave alternativa*

**2. Matriculas:**


| Estudiante | Asignatura |
| :-- | :-- |
| Juan | Matemáticas |
| Ana | Física |
| Juan | Física |
| Ana | Matemáticas |

La clave primaria es `(Estudiante, Asignatura)`. Aquí, no quedan dependencias funcionales problemáticas: solo se almacena la matriculación de los estudiantes en cada asignatura.


## 🎯 Resumen de las formas normales

| Forma Normal | Requisito Principal | Qué Elimina |
| :-- | :-- | :-- |
| **1FN** | Valores atómicos (no listas) | Grupos repetitivos y atributos multivaluados |
| **2FN** | 1FN + Sin dependencias parciales | Dependencias de parte de la clave |
| **3FN** | 2FN + Sin dependencias transitivas | Dependencias entre atributos no clave |
| **FNBC** | 3FN + Todo determinante es clave candidata | Anomalías residuales de 3FN |

## 💡 Consejos prácticos para normalizar

1. **Identifica la clave primaria primero**: Es fundamental saber qué atributos identifican de forma única cada fila.
2. **Dibuja las dependencias funcionales**: Crea un diagrama con flechas mostrando qué determina qué. Esto te ayudará a visualizar las dependencias parciales y transitivas.
3. **Pregúntate siempre**: "¿Este dato depende de toda la clave o solo de una parte?" y "¿Este dato depende directamente de la clave o a través de otro atributo?"
4. **No normalices en exceso**: En el mundo real, raramente se va más allá de 3FN o FNBC. Las formas normales superiores (4FN, 5FN) son más teóricas.
5. **Piensa en las operaciones**: Una base bien normalizada facilita las actualizaciones y evita inconsistencias.

## ¿Hasta dónde normalizar?

En la práctica, **lo recomendable es normalizar hasta 3FN o FNBC**. Las formas normales superiores (4FN, 5FN, FNDC) tienen aplicación principalmente teórica y rara vez se usan en aplicaciones reales.

## 🔄 Desnormalización: Cuando Romper las Reglas

Aunque parezca contradictorio, a veces **deliberadamente** se viola la normalización para mejorar el **rendimiento** de las consultas.

### ¿Cuándo desnormalizar?

- Cuando las consultas sean muy lentas debido a múltiples JOINs
- En sistemas de reporting o análisis donde se priorizan las lecturas sobre las escrituras
- Cuando la redundancia controlada mejora significativamente la velocidad

> ⚠️ **Advertencia**: La desnormalización requiere un control riguroso de la integridad de datos y solo debe aplicarse tras un análisis de rendimiento.


## Resumen visual del proceso

```
┌─────────────────────────────────────────────────────┐
│  TABLA NO NORMALIZADA                               │
│  (múltiples valores, redundancia, dependencias)     │
└────────────────┬────────────────────────────────────┘
                 │
                 ▼
         ┌───────────────┐
         │      1FN      │  Eliminar valores múltiples
         │  (atómicos)   │  → cada celda un valor
         └───────┬───────┘
                 │
                 ▼
         ┌───────────────┐
         │      2FN      │  Eliminar dependencias parciales
         │ (sin parcial) │  → todo depende de toda la clave
         └───────┬───────┘
                 │
                 ▼
         ┌───────────────┐
         │      3FN      │  Eliminar dependencias transitivas
         │(sin transitiv)│  → atributos no clave independientes
         └───────┬───────┘
                 │
                 ▼
         ┌───────────────┐
         │     FNBC      │  Determinantes son claves candidatas
         │ (más estricta)│  → máxima normalización práctica
         └───────────────┘
```


## 📝 Ejercicio práctico

### Contexto

Una tienda online de videojuegos quiere organizar su información de pedidos. Actualmente tienen toda la información en una única tabla desordenada.

### Tabla inicial

| ID_Pedido | Cliente | Email_Cliente | Ciudad_Cliente | Cod_Postal | Provincia | Productos | Precios | Cantidades | Fecha_Pedido |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| 1001 | Ana García | ana@mail.com | Toledo | 45001 | Toledo | FIFA24, Zelda | 59.99, 69.99 | 1, 2 | 2025-10-15 |
| 1002 | Luis Ruiz | luis@mail.com | Madrid | 28001 | Madrid | Mario Kart | 49.99 | 1 | 2025-10-16 |
| 1003 | Ana García | ana@mail.com | Toledo | 45001 | Toledo | Zelda, FIFA24, Minecraft | 69.99, 59.99, 29.99 | 1, 1, 2 | 2025-10-17 |

**¿Qué problemas tiene?** Viola 1FN porque los campos `Productos`, `Precios` y `Cantidades` contienen múltiples valores separados por comas (no son atómicos).

***

### Tarea 1: Normalizar a primera forma normal (1FN)

**Objetivo:** Eliminar los valores multivaluados. Cada celda debe contener UN SOLO valor.

**Pista:** Crea una fila por cada producto en el pedido.

**Solución esperada:**


| ID_Pedido | Cliente | Email_Cliente | Ciudad_Cliente | Cod_Postal | Provincia | Producto | Precio | Cantidad | Fecha_Pedido |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| 1001 | Ana García | ana@mail.com | Toledo | 45001 | Toledo | FIFA24 | 59.99 | 1 | 2025-10-15 |
| 1001 | Ana García | ana@mail.com | Toledo | 45001 | Toledo | Zelda | 69.99 | 2 | 2025-10-15 |
| 1002 | Luis Ruiz | luis@mail.com | Madrid | 28001 | Madrid | Mario Kart | 49.99 | 1 | 2025-10-16 |
| 1003 | Ana García | ana@mail.com | Toledo | 45001 | Toledo | Zelda | 69.99 | 1 | 2025-10-17 |
| 1003 | Ana García | ana@mail.com | Toledo | 45001 | Toledo | FIFA24 | 59.99 | 1 | 2025-10-17 |
| 1003 | Ana García | ana@mail.com | Toledo | 45001 | Toledo | Minecraft | 29.99 | 2 | 2025-10-17 |

**Clave primaria:** `{ID_Pedido, Producto}` (porque cada pedido puede tener múltiples productos)

**¿Cumple 2FN?** **NO**. Existen dependencias parciales.

***

### Tarea 2: Identificar dependencias parciales

**Pregunta para los alumnos:**
Observa la tabla en 1FN. La clave primaria es `{ID_Pedido, Producto}`. ¿Qué atributos dependen solo de `ID_Pedido` y no necesitan conocer el `Producto`?

**Respuesta:**

- `Cliente`, `Email_Cliente`, `Ciudad_Cliente`, `Cod_Postal`, `Provincia` y `Fecha_Pedido` dependen SOLO de `ID_Pedido`.
- `Precio` depende SOLO de `Producto` (el precio es del producto, no del pedido).
- `Cantidad` depende de AMBOS (cada producto tiene su cantidad específica en cada pedido).

Estas son **dependencias parciales** porque atributos no-clave dependen solo de PARTE de la clave primaria.

***

### Tarea 3: Normalizar a segunda forma normal (2FN)

**Objetivo:** Eliminar las dependencias parciales. Crear tablas separadas para cada conjunto de dependencias.

**Solución esperada:**

**Tabla PEDIDOS:**


| ID_Pedido | Cliente | Email_Cliente | Ciudad_Cliente | Cod_Postal | Provincia | Fecha_Pedido |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| 1001 | Ana García | ana@mail.com | Toledo | 45001 | Toledo | 2025-10-15 |
| 1002 | Luis Ruiz | luis@mail.com | Madrid | 28001 | Madrid | 2025-10-16 |
| 1003 | Ana García | ana@mail.com | Toledo | 45001 | Toledo | 2025-10-17 |

Clave primaria: `ID_Pedido`

**Tabla PRODUCTOS:**


| Producto | Precio |
| :-- | :-- |
| FIFA24 | 59.99 |
| Zelda | 69.99 |
| Mario Kart | 49.99 |
| Minecraft | 29.99 |

Clave primaria: `Producto`

**Tabla LINEAS_PEDIDO:**


| ID_Pedido | Producto | Cantidad |
| :-- | :-- | :-- |
| 1001 | FIFA24 | 1 |
| 1001 | Zelda | 2 |
| 1002 | Mario Kart | 1 |
| 1003 | Zelda | 1 |
| 1003 | FIFA24 | 1 |
| 1003 | Minecraft | 2 |

Clave primaria: `{ID_Pedido, Producto}`

**¿Cumple 3FN?** **NO**. En la tabla `PEDIDOS` existe una dependencia transitiva.

***

### Tarea 4: Identificar dependencias transitivas

**Pregunta para los alumnos:**
En la tabla `PEDIDOS`, ¿qué atributo depende de otro atributo que NO es la clave primaria?

**Respuesta:**
`Cod_Postal` → `Provincia` (el código postal determina la provincia).

Por tanto: `ID_Pedido` → `Cod_Postal` → `Provincia` (dependencia transitiva).

Además: `ID_Pedido` → `Cliente` → `Email_Cliente`, `Ciudad_Cliente` (el cliente determina su email y ciudad).

***

### Tarea 5: Normalizar a tercera forma normal (3FN)

**Objetivo:** Eliminar las dependencias transitivas. Los atributos no-clave solo deben depender de la clave primaria.

**Solución esperada:**

**Tabla PEDIDOS:**


| ID_Pedido | Cliente | Fecha_Pedido |
| :-- | :-- | :-- |
| 1001 | ana@mail.com | 2025-10-15 |
| 1002 | luis@mail.com | 2025-10-16 |
| 1003 | ana@mail.com | 2025-10-17 |

Clave primaria: `ID_Pedido`
Nota: Usamos el email como identificador del cliente (clave foránea).

**Tabla CLIENTES:**


| Email_Cliente | Cliente | Ciudad_Cliente | Cod_Postal |
| :-- | :-- | :-- | :-- |
| ana@mail.com | Ana García | Toledo | 45001 |
| luis@mail.com | Luis Ruiz | Madrid | 28001 |

Clave primaria: `Email_Cliente`

**Tabla CODIGOS_POSTALES:**


| Cod_Postal | Provincia |
| :-- | :-- |
| 45001 | Toledo |
| 28001 | Madrid |

Clave primaria: `Cod_Postal`

**Tabla PRODUCTOS:** (sin cambios)


| Producto | Precio |
| :-- | :-- |
| FIFA24 | 59.99 |
| Zelda | 69.99 |
| Mario Kart | 49.99 |
| Minecraft | 29.99 |

**Tabla LINEAS_PEDIDO:** (sin cambios)


| ID_Pedido | Producto | Cantidad |
| :-- | :-- | :-- |
| 1001 | FIFA24 | 1 |
| 1001 | Zelda | 2 |
| 1002 | Mario Kart | 1 |
| 1003 | Zelda | 1 |
| 1003 | FIFA24 | 1 |
| 1003 | Minecraft | 2 |


***

### Resumen del ejercicio

**Ventajas de la normalización:**

- No se repite la información del cliente en cada pedido.
- Actualizar el precio de un producto se hace en un solo lugar.
- Agregar un nuevo código postal no requiere un pedido existente.
- Se eliminan anomalías de inserción, actualización y borrado.

**Preguntas de reflexión:**

1. ¿Qué pasaría si el cliente "Ana García" cambia de dirección? ¿En cuántas tablas tendríamos que actualizar?
2. ¿Cómo consultar todos los pedidos con su información completa ahora que están en múltiples tablas? (Pista: JOINs)
3. ¿Esta estructura cumple FNBC? ¿Por qué?
