<<<<<<< HEAD
# 📚 Batería de Ejercicios: Normalización de Bases de Datos
=======
# Batería de Ejercicios: Normalización de Bases de Datos
>>>>>>> 0c7581282b5a58fbd975f93246e891f3325a8fc8

<<<<<<< HEAD
## Ejercicio 1: Sistema de Gestión de Biblioteca

### 🎯 Situación inicial

Se tiene la siguiente tabla que almacena información sobre préstamos de libros en una biblioteca:


| ID_Prestamo | DNI_Usuario | Nombre_Usuario | Teléfonos | ISBN_Libros | Títulos_Libros | Autores_Libros | Fecha_Prestamo |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| 1 | 12345678A | Ana García | 666111222, 666333444 | 978-84-376-0494-7, 978-84-206-8186-8 | El Quijote, Cien años de soledad | Miguel de Cervantes, Gabriel García Márquez | 2025-01-15 |
| 2 | 23456789B | Luis Martín | 677555666 | 978-84-376-0494-7 | El Quijote | Miguel de Cervantes | 2025-01-20 |
| 3 | 12345678A | Ana García | 666111222, 666333444 | 978-84-204-8238-9 | La sombra del viento | Carlos Ruiz Zafón | 2025-02-01 |

**Clave primaria:** ID_Prestamo

***

### 📝 Paso 1: Análisis y normalización a Primera Forma Normal (1FN)

**¿Qué incumple la 1FN?**

La tabla NO cumple con la 1FN porque:

- El atributo **Teléfonos** contiene valores múltiples (varios teléfonos separados por comas)
- Los atributos **ISBN_Libros**, **Títulos_Libros** y **Autores_Libros** contienen valores múltiples cuando se prestan varios libros

**Regla de 1FN:** Todos los atributos deben contener valores atómicos (indivisibles), no puede haber grupos repetitivos ni atributos multivaluados.

**Solución:** Crear filas separadas para cada combinación de préstamo-libro y eliminar atributos multivaluados.

**Tabla normalizada a 1FN:**

**PRESTAMOS_1FN**


| ID_Prestamo | DNI_Usuario | Nombre_Usuario | ISBN_Libro | Título_Libro | Autor_Libro | Fecha_Prestamo |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| 1 | 12345678A | Ana García | 978-84-376-0494-7 | El Quijote | Miguel de Cervantes | 2025-01-15 |
| 1 | 12345678A | Ana García | 978-84-206-8186-8 | Cien años de soledad | Gabriel García Márquez | 2025-01-15 |
| 2 | 23456789B | Luis Martín | 978-84-376-0494-7 | El Quijote | Miguel de Cervantes | 2025-01-20 |
| 3 | 12345678A | Ana García | 978-84-204-8238-9 | La sombra del viento | Carlos Ruiz Zafón | 2025-02-01 |

**TELEFONOS_USUARIOS**


| DNI_Usuario | Teléfono |
| :-- | :-- |
| 12345678A | 666111222 |
| 12345678A | 666333444 |
| 23456789B | 677555666 |

**Nueva clave primaria:** (ID_Prestamo, ISBN_Libro)

***

### 📝 Paso 2: Análisis y normalización a Segunda Forma Normal (2FN)

**¿Qué incumple la 2FN?**

La tabla PRESTAMOS_1FN NO cumple con la 2FN porque existen **dependencias funcionales parciales**:

- **DNI_Usuario** → Nombre_Usuario (depende solo de parte de la clave)
- **ISBN_Libro** → Título_Libro, Autor_Libro (depende solo de parte de la clave)
- **ID_Prestamo** → DNI_Usuario, Fecha_Prestamo (depende solo de parte de la clave)

**Regla de 2FN:** No debe haber dependencias funcionales parciales. Todos los atributos no clave deben depender de la clave primaria completa.

**Solución:** Separar en tablas donde cada atributo dependa completamente de su clave.

**Tablas normalizadas a 2FN:**

**PRESTAMOS**


| ID_Prestamo | DNI_Usuario | Fecha_Prestamo |
| :-- | :-- | :-- |
| 1 | 12345678A | 2025-01-15 |
| 2 | 23456789B | 2025-01-20 |
| 3 | 12345678A | 2025-02-01 |

**DETALLE_PRESTAMOS**


| ID_Prestamo | ISBN_Libro |
| :-- | :-- |
| 1 | 978-84-376-0494-7 |
| 1 | 978-84-206-8186-8 |
| 2 | 978-84-376-0494-7 |
| 3 | 978-84-204-8238-9 |

**USUARIOS**


| DNI_Usuario | Nombre_Usuario |
| :-- | :-- |
| 12345678A | Ana García |
| 23456789B | Luis Martín |

**LIBROS**


| ISBN_Libro | Título_Libro | Autor_Libro |
| :-- | :-- | :-- |
| 978-84-376-0494-7 | El Quijote | Miguel de Cervantes |
| 978-84-206-8186-8 | Cien años de soledad | Gabriel García Márquez |
| 978-84-204-8238-9 | La sombra del viento | Carlos Ruiz Zafón |


***

### 📝 Paso 3: Análisis y normalización a Tercera Forma Normal (3FN)

**¿Qué incumple la 3FN?**

Las tablas ya cumplen con la 3FN porque NO existen **dependencias funcionales transitivas**. Todos los atributos no clave dependen únicamente de la clave primaria y no de otros atributos no clave.

✅ Las tablas están en 3FN.

***

### 📝 Paso 4: Análisis y normalización a Forma Normal de Boyce-Codd (FNBC)

**¿Qué incumple la FNBC?**

Las tablas ya cumplen con la FNBC porque en todas las dependencias funcionales, el determinante es una superclave (clave candidata).

✅ Las tablas están en FNBC.

**Resultado final:** 5 tablas normalizadas hasta FNBC.

***

## Ejercicio 2: Sistema de Gestión de Clínica Médica

### 🎯 Situación inicial

Se tiene la siguiente tabla que almacena información sobre consultas médicas:


| ID_Consulta | DNI_Paciente | Nombre_Paciente | Edad_Paciente | ID_Médicos | Nombres_Médicos | Especialidades | Diagnósticos | Medicamentos_Recetados | Fecha_Consulta |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| 101 | 11111111A | María López | 45 | M01, M02 | Dr. Pérez, Dra. Sánchez | Cardiología, Medicina General | Hipertensión | Enalapril 10mg, Aspirina 100mg | 2025-03-10 |
| 102 | 22222222B | Juan Torres | 32 | M02 | Dra. Sánchez | Medicina General | Gripe | Paracetamol 500mg | 2025-03-11 |
| 103 | 11111111A | María López | 45 | M01 | Dr. Pérez | Cardiología | Control rutinario | - | 2025-04-15 |

**Clave primaria:** ID_Consulta

***

### 📝 Paso 1: Análisis y normalización a Primera Forma Normal (1FN)

**¿Qué incumple la 1FN?**

La tabla NO cumple con la 1FN porque:

- **ID_Médicos**, **Nombres_Médicos** y **Especialidades** contienen valores múltiples
- **Diagnósticos** y **Medicamentos_Recetados** contienen valores múltiples

**Solución:** Separar los valores múltiples en filas individuales.

**Tablas normalizadas a 1FN:**

**CONSULTAS_1FN**


| ID_Consulta | DNI_Paciente | Nombre_Paciente | Edad_Paciente | ID_Médico | Nombre_Médico | Especialidad | Diagnóstico | Medicamento_Recetado | Fecha_Consulta |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| 101 | 11111111A | María López | 45 | M01 | Dr. Pérez | Cardiología | Hipertensión | Enalapril 10mg | 2025-03-10 |
| 101 | 11111111A | María López | 45 | M01 | Dr. Pérez | Cardiología | Hipertensión | Aspirina 100mg | 2025-03-10 |
| 101 | 11111111A | María López | 45 | M02 | Dra. Sánchez | Medicina General | Hipertensión | Enalapril 10mg | 2025-03-10 |
| 101 | 11111111A | María López | 45 | M02 | Dra. Sánchez | Medicina General | Hipertensión | Aspirina 100mg | 2025-03-10 |
| 102 | 22222222B | Juan Torres | 32 | M02 | Dra. Sánchez | Medicina General | Gripe | Paracetamol 500mg | 2025-03-11 |
| 103 | 11111111A | María López | 45 | M01 | Dr. Pérez | Cardiología | Control rutinario | NULL | 2025-04-15 |

**Nueva clave primaria:** (ID_Consulta, ID_Médico, Medicamento_Recetado)

***

### 📝 Paso 2: Análisis y normalización a Segunda Forma Normal (2FN)

**¿Qué incumple la 2FN?**

Existen dependencias funcionales parciales:

- **DNI_Paciente** → Nombre_Paciente, Edad_Paciente
- **ID_Médico** → Nombre_Médico, Especialidad
- **ID_Consulta** → DNI_Paciente, Fecha_Consulta

**Solución:** Crear tablas separadas para eliminar dependencias parciales.

**Tablas normalizadas a 2FN:**

**CONSULTAS**


| ID_Consulta | DNI_Paciente | Fecha_Consulta |
| :-- | :-- | :-- |
| 101 | 11111111A | 2025-03-10 |
| 102 | 22222222B | 2025-03-11 |
| 103 | 11111111A | 2025-04-15 |

**PACIENTES**


| DNI_Paciente | Nombre_Paciente | Edad_Paciente |
| :-- | :-- | :-- |
| 11111111A | María López | 45 |
| 22222222B | Juan Torres | 32 |

**MÉDICOS**


| ID_Médico | Nombre_Médico | Especialidad |
| :-- | :-- | :-- |
| M01 | Dr. Pérez | Cardiología |
| M02 | Dra. Sánchez | Medicina General |

**CONSULTAS_MÉDICOS**


| ID_Consulta | ID_Médico |
| :-- | :-- |
| 101 | M01 |
| 101 | M02 |
| 102 | M02 |
| 103 | M01 |

**DIAGNÓSTICOS**


| ID_Consulta | Diagnóstico |
| :-- | :-- |
| 101 | Hipertensión |
| 102 | Gripe |
| 103 | Control rutinario |

**MEDICAMENTOS_RECETADOS**


| ID_Consulta | Medicamento |
| :-- | :-- |
| 101 | Enalapril 10mg |
| 101 | Aspirina 100mg |
| 102 | Paracetamol 500mg |


***

### 📝 Paso 3: Análisis y normalización a Tercera Forma Normal (3FN)

**¿Qué incumple la 3FN?**

En la tabla PACIENTES existe una dependencia transitiva:

- DNI_Paciente → Nombre_Paciente (correcta)
- Sin embargo, la **Edad_Paciente** debería calcularse dinámicamente a partir de la fecha de nacimiento, ya que cambia con el tiempo

**Mejora sugerida:** Sustituir Edad_Paciente por Fecha_Nacimiento.

**PACIENTES (mejorado para 3FN)**


| DNI_Paciente | Nombre_Paciente | Fecha_Nacimiento |
| :-- | :-- | :-- |
| 11111111A | María López | 1980-05-12 |
| 22222222B | Juan Torres | 1993-08-23 |

✅ Ahora todas las tablas están en 3FN.

***

### 📝 Paso 4: Análisis y normalización a Forma Normal de Boyce-Codd (FNBC)

**¿Qué incumple la FNBC?**

Todas las tablas cumplen con la FNBC porque en cada dependencia funcional, el determinante es una superclave.

✅ Las tablas están en FNBC.

**Resultado final:** 6 tablas normalizadas hasta FNBC.

***

¿Te gustan estos ejercicios? Si es así, puedo generarte más con diferentes contextos y niveles de complejidad. 🎯



## Ejercicio 3: Sistema de Cursos Universitarios

### 🎯 Situación inicial

Se tiene la siguiente tabla que almacena información sobre inscripciones en cursos universitarios:


| ID_Inscripción | ID_Estudiante | Nombre_Estudiante | Cursos_Inscritos | Profesores | Códigos_Postales_Profesores | Ciudades_Profesores | Provincias | Departamento_Prof | Fecha_Inscripción |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| 1 | E001 | Pedro Ruiz | MAT101, FIS201 | Prof. García, Prof. López | 28001, 28002 | Madrid, Madrid | Madrid, Madrid | Matemáticas, Física | 2025-09-01 |
| 2 | E002 | Laura Vega | MAT101 | Prof. García | 28001 | Madrid | Madrid | Matemáticas | 2025-09-01 |
| 3 | E001 | Pedro Ruiz | QUIM301 | Prof. Martínez | 41001 | Sevilla | Sevilla | Química | 2025-09-05 |

**Clave primaria:** ID_Inscripción

***

### 📝 Paso 1: Análisis y normalización a Primera Forma Normal (1FN)

**¿Qué incumple la 1FN?**

- **Cursos_Inscritos**, **Profesores**, **Códigos_Postales_Profesores**, **Ciudades_Profesores**, **Provincias** y **Departamento_Prof** contienen valores múltiples

**Solución:** Crear filas separadas por cada curso.

**INSCRIPCIONES_1FN**


| ID_Inscripción | ID_Estudiante | Nombre_Estudiante | Curso | Profesor | Código_Postal_Prof | Ciudad_Prof | Provincia_Prof | Departamento_Prof | Fecha_Inscripción |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| 1 | E001 | Pedro Ruiz | MAT101 | Prof. García | 28001 | Madrid | Madrid | Matemáticas | 2025-09-01 |
| 1 | E001 | Pedro Ruiz | FIS201 | Prof. López | 28002 | Madrid | Madrid | Física | 2025-09-01 |
| 2 | E002 | Laura Vega | MAT101 | Prof. García | 28001 | Madrid | Madrid | Matemáticas | 2025-09-01 |
| 3 | E001 | Pedro Ruiz | QUIM301 | Prof. Martínez | 41001 | Sevilla | Sevilla | Química | 2025-09-05 |

**Nueva clave primaria:** (ID_Inscripción, Curso)

***

### 📝 Paso 2: Análisis y normalización a Segunda Forma Normal (2FN)

**¿Qué incumple la 2FN?**

Dependencias funcionales parciales:

- **ID_Estudiante** → Nombre_Estudiante
- **ID_Inscripción** → ID_Estudiante, Fecha_Inscripción
- **Curso** → Profesor, Código_Postal_Prof, Ciudad_Prof, Provincia_Prof, Departamento_Prof

**Solución:** Separar en tablas independientes.

**INSCRIPCIONES**


| ID_Inscripción | ID_Estudiante | Fecha_Inscripción |
| :-- | :-- | :-- |
| 1 | E001 | 2025-09-01 |
| 2 | E002 | 2025-09-01 |
| 3 | E001 | 2025-09-05 |

**DETALLE_INSCRIPCIONES**


| ID_Inscripción | Curso |
| :-- | :-- |
| 1 | MAT101 |
| 1 | FIS201 |
| 2 | MAT101 |
| 3 | QUIM301 |

**ESTUDIANTES**


| ID_Estudiante | Nombre_Estudiante |
| :-- | :-- |
| E001 | Pedro Ruiz |
| E002 | Laura Vega |

**CURSOS**


| Curso | Profesor | Código_Postal_Prof | Ciudad_Prof | Provincia_Prof | Departamento_Prof |
| :-- | :-- | :-- | :-- | :-- | :-- |
| MAT101 | Prof. García | 28001 | Madrid | Madrid | Matemáticas |
| FIS201 | Prof. López | 28002 | Madrid | Madrid | Física |
| QUIM301 | Prof. Martínez | 41001 | Sevilla | Sevilla | Química |


***

### 📝 Paso 3: Análisis y normalización a Tercera Forma Normal (3FN)

**¿Qué incumple la 3FN?**

En la tabla CURSOS existen **dependencias transitivas**:

- Curso → Código_Postal_Prof → Ciudad_Prof, Provincia_Prof
- Curso → Profesor → Código_Postal_Prof, Ciudad_Prof, Provincia_Prof, Departamento_Prof

**Solución:** Extraer la información de los profesores a una tabla separada.

**CURSOS (normalizado a 3FN)**


| Curso | Profesor |
| :-- | :-- |
| MAT101 | Prof. García |
| FIS201 | Prof. López |
| QUIM301 | Prof. Martínez |

**PROFESORES**


| Profesor | Código_Postal | Ciudad | Provincia | Departamento |
| :-- | :-- | :-- | :-- | :-- |
| Prof. García | 28001 | Madrid | Madrid | Matemáticas |
| Prof. López | 28002 | Madrid | Madrid | Física |
| Prof. Martínez | 41001 | Sevilla | Sevilla | Química |

Pero aún hay dependencia transitiva:

- Profesor → Código_Postal → Ciudad, Provincia

**PROFESORES (corregido)**


| Profesor | Código_Postal | Departamento |
| :-- | :-- | :-- |
| Prof. García | 28001 | Matemáticas |
| Prof. López | 28002 | Física |
| Prof. Martínez | 41001 | Química |

**CÓDIGOS_POSTALES**


| Código_Postal | Ciudad | Provincia |
| :-- | :-- | :-- |
| 28001 | Madrid | Madrid |
| 28002 | Madrid | Madrid |
| 41001 | Sevilla | Sevilla |

✅ Ahora está en 3FN.

***

### 📝 Paso 4: Análisis y normalización a Forma Normal de Boyce-Codd (FNBC)

**¿Qué incumple la FNBC?**

En la tabla CURSOS tenemos un problema: si un profesor puede impartir múltiples cursos, pero queremos registrar qué profesor imparte qué curso en qué período, podríamos tener:

- (Curso, Período) → Profesor
- Profesor → Departamento

Suponiendo que la tabla CURSOS debería incluir información de períodos académicos:

**Situación problemática para FNBC:**


| Curso | Período | Profesor |
| :-- | :-- | :-- |
| MAT101 | 2025-1 | Prof. García |
| FIS201 | 2025-1 | Prof. López |
| MAT101 | 2025-2 | Prof. García |

Aquí la clave candidata es (Curso, Período), pero si agregamos que cada profesor solo puede impartir un curso específico (restricción del dominio), tendríamos:

- Profesor → Curso

Esto viola FNBC porque Profesor no es superclave.

**Solución para FNBC:** Mantener las tablas separadas como están actualmente cumple FNBC.

✅ Las tablas están en FNBC.

**Resultado final:** 6 tablas normalizadas hasta FNBC.

***

## Ejercicio 4: Sistema de Gestión Hospitalaria

### 🎯 Situación inicial

Se tiene la siguiente tabla que registra las cirugías realizadas en un hospital:


| ID_Cirugía | Paciente_DNI | Nombre_Paciente | Códigos_Diagnóstico | Descripciones_Diagnóstico | ID_Quirófanos | Pisos | ID_Cirujano_Principal | Nombre_Cirujano | Especialidad_Cirujano | Hospital_Cirujano | Director_Hospital | Fecha_Cirugía |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| C001 | 33333333C | Ana Ruiz | D01, D02 | Apendicitis, Peritonitis | Q1, Q2 | 3, 3 | CIR001 | Dr. Fernández | Cirugía General | Hospital Central | Dr. Gómez | 2025-05-10 |
| C002 | 44444444D | Carlos Díaz | D03 | Fractura fémur | Q3 | 4 | CIR002 | Dra. Morales | Traumatología | Hospital Norte | Dra. Silva | 2025-05-12 |
| C003 | 33333333C | Ana Ruiz | D04 | Revisión post-operatoria | Q1 | 3 | CIR001 | Dr. Fernández | Cirugía General | Hospital Central | Dr. Gómez | 2025-05-20 |

**Clave primaria:** ID_Cirugía

***

### 📝 Paso 1: Análisis y normalización a Primera Forma Normal (1FN)

**¿Qué incumple la 1FN?**

- **Códigos_Diagnóstico**, **Descripciones_Diagnóstico**, **ID_Quirófanos** y **Pisos** contienen valores múltiples

**Solución:** Separar en filas individuales.

**CIRUGÍAS_1FN**


| ID_Cirugía | Paciente_DNI | Nombre_Paciente | Código_Diagnóstico | Descripción_Diagnóstico | ID_Quirófano | Piso | ID_Cirujano_Principal | Nombre_Cirujano | Especialidad_Cirujano | Hospital_Cirujano | Director_Hospital | Fecha_Cirugía |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| C001 | 33333333C | Ana Ruiz | D01 | Apendicitis | Q1 | 3 | CIR001 | Dr. Fernández | Cirugía General | Hospital Central | Dr. Gómez | 2025-05-10 |
| C001 | 33333333C | Ana Ruiz | D01 | Apendicitis | Q2 | 3 | CIR001 | Dr. Fernández | Cirugía General | Hospital Central | Dr. Gómez | 2025-05-10 |
| C001 | 33333333C | Ana Ruiz | D02 | Peritonitis | Q1 | 3 | CIR001 | Dr. Fernández | Cirugía General | Hospital Central | Dr. Gómez | 2025-05-10 |
| C001 | 33333333C | Ana Ruiz | D02 | Peritonitis | Q2 | 3 | CIR001 | Dr. Fernández | Cirugía General | Hospital Central | Dr. Gómez | 2025-05-10 |
| C002 | 44444444D | Carlos Díaz | D03 | Fractura fémur | Q3 | 4 | CIR002 | Dra. Morales | Traumatología | Hospital Norte | Dra. Silva | 2025-05-12 |
| C003 | 33333333C | Ana Ruiz | D04 | Revisión post-operatoria | Q1 | 3 | CIR001 | Dr. Fernández | Cirugía General | Hospital Central | Dr. Gómez | 2025-05-20 |

**Nueva clave primaria:** (ID_Cirugía, Código_Diagnóstico, ID_Quirófano)

***

### 📝 Paso 2: Análisis y normalización a Segunda Forma Normal (2FN)

**¿Qué incumple la 2FN?**

Dependencias parciales:

- **Paciente_DNI** → Nombre_Paciente
- **Código_Diagnóstico** → Descripción_Diagnóstico
- **ID_Quirófano** → Piso
- **ID_Cirujano_Principal** → Nombre_Cirujano, Especialidad_Cirujano, Hospital_Cirujano, Director_Hospital
- **ID_Cirugía** → Paciente_DNI, ID_Cirujano_Principal, Fecha_Cirugía

**Solución:** Dividir en tablas separadas.

**CIRUGÍAS**


| ID_Cirugía | Paciente_DNI | ID_Cirujano_Principal | Fecha_Cirugía |
| :-- | :-- | :-- | :-- |
| C001 | 33333333C | CIR001 | 2025-05-10 |
| C002 | 44444444D | CIR002 | 2025-05-12 |
| C003 | 33333333C | CIR001 | 2025-05-20 |

**CIRUGÍAS_DIAGNÓSTICOS**


| ID_Cirugía | Código_Diagnóstico |
| :-- | :-- |
| C001 | D01 |
| C001 | D02 |
| C002 | D03 |
| C003 | D04 |

**CIRUGÍAS_QUIRÓFANOS**


| ID_Cirugía | ID_Quirófano |
| :-- | :-- |
| C001 | Q1 |
| C001 | Q2 |
| C002 | Q3 |
| C003 | Q1 |

**PACIENTES**


| Paciente_DNI | Nombre_Paciente |
| :-- | :-- |
| 33333333C | Ana Ruiz |
| 44444444D | Carlos Díaz |

**DIAGNÓSTICOS**


| Código_Diagnóstico | Descripción_Diagnóstico |
| :-- | :-- |
| D01 | Apendicitis |
| D02 | Peritonitis |
| D03 | Fractura fémur |
| D04 | Revisión post-operatoria |

**QUIRÓFANOS**


| ID_Quirófano | Piso |
| :-- | :-- |
| Q1 | 3 |
| Q2 | 3 |
| Q3 | 4 |

**CIRUJANOS**


| ID_Cirujano | Nombre_Cirujano | Especialidad_Cirujano | Hospital_Cirujano | Director_Hospital |
| :-- | :-- | :-- | :-- | :-- |
| CIR001 | Dr. Fernández | Cirugía General | Hospital Central | Dr. Gómez |
| CIR002 | Dra. Morales | Traumatología | Hospital Norte | Dra. Silva |


***

### 📝 Paso 3: Análisis y normalización a Tercera Forma Normal (3FN)

**¿Qué incumple la 3FN?**

En la tabla CIRUJANOS existe una **dependencia transitiva**:

- ID_Cirujano → Hospital_Cirujano → Director_Hospital

**Solución:** Separar la información del hospital.

**CIRUJANOS (normalizado a 3FN)**


| ID_Cirujano | Nombre_Cirujano | Especialidad_Cirujano | Hospital_Cirujano |
| :-- | :-- | :-- | :-- |
| CIR001 | Dr. Fernández | Cirugía General | Hospital Central |
| CIR002 | Dra. Morales | Traumatología | Hospital Norte |

**HOSPITALES**


| Hospital | Director |
| :-- | :-- |
| Hospital Central | Dr. Gómez |
| Hospital Norte | Dra. Silva |

✅ Ahora está en 3FN.

***

### 📝 Paso 4: Análisis y normalización a Forma Normal de Boyce-Codd (FNBC)

**¿Qué incumple la FNBC?**

Supongamos que en la tabla CIRUJANOS existe la siguiente restricción del dominio: **cada especialidad solo puede ejercerse en un hospital específico** (por políticas del sistema de salud regional). Esto crea:

- ID_Cirujano → Especialidad_Cirujano, Hospital_Cirujano
- Especialidad_Cirujano → Hospital_Cirujano

Aquí **Especialidad_Cirujano** determina **Hospital_Cirujano**, pero Especialidad_Cirujano no es superclave, violando FNBC.

**Solución para FNBC:** Dividir la tabla.

**CIRUJANOS (normalizado a FNBC)**


| ID_Cirujano | Nombre_Cirujano | Especialidad_Cirujano |
| :-- | :-- | :-- |
| CIR001 | Dr. Fernández | Cirugía General |
| CIR002 | Dra. Morales | Traumatología |

**ESPECIALIDADES_HOSPITALES**


| Especialidad | Hospital |
| :-- | :-- |
| Cirugía General | Hospital Central |
| Traumatología | Hospital Norte |

✅ Ahora está en FNBC.

**Resultado final:** 10 tablas normalizadas hasta FNBC.

***

## Ejercicio 5: Sistema de Concesionario de Vehículos

### 🎯 Situación inicial

Se tiene la siguiente tabla que registra ventas de vehículos:


| ID_Venta | Cliente_DNI | Nombre_Cliente | Teléfonos_Cliente | VINs_Vehículos | Modelos | Marcas | Colores | Vendedor_ID | Nombre_Vendedor | Sucursal_Vendedor | Ciudad_Sucursal | Provincia_Sucursal | Gerente_Sucursal | Fecha_Venta |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| V001 | 55555555E | Miguel Soto | 611222333, 622444555 | VIN001, VIN002 | Ibiza, León | SEAT, SEAT | Rojo, Blanco | VEN01 | Lucía Ramos | SUC01 | Barcelona | Barcelona | Juan Pérez | 2025-06-15 |
| V002 | 66666666F | Sara Blanco | 633666777 | VIN003 | Corolla | Toyota | Negro | VEN02 | Roberto Díaz | SUC02 | Valencia | Valencia | María López | 2025-06-18 |
| V003 | 55555555E | Miguel Soto | 611222333, 622444555 | VIN004 | Qashqai | Nissan | Azul | VEN01 | Lucía Ramos | SUC01 | Barcelona | Barcelona | Juan Pérez | 2025-07-10 |

**Clave primaria:** ID_Venta

***

### 📝 Paso 1: Análisis y normalización a Primera Forma Normal (1FN)

**¿Qué incumple la 1FN?**

- **Teléfonos_Cliente**, **VINs_Vehículos**, **Modelos**, **Marcas** y **Colores** contienen valores múltiples

**Solución:** Atomizar valores múltiples.

**VENTAS_1FN**


| ID_Venta | Cliente_DNI | Nombre_Cliente | VIN_Vehículo | Modelo | Marca | Color | Vendedor_ID | Nombre_Vendedor | Sucursal_Vendedor | Ciudad_Sucursal | Provincia_Sucursal | Gerente_Sucursal | Fecha_Venta |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| V001 | 55555555E | Miguel Soto | VIN001 | Ibiza | SEAT | Rojo | VEN01 | Lucía Ramos | SUC01 | Barcelona | Barcelona | Juan Pérez | 2025-06-15 |
| V001 | 55555555E | Miguel Soto | VIN002 | León | SEAT | Blanco | VEN01 | Lucía Ramos | SUC01 | Barcelona | Barcelona | Juan Pérez | 2025-06-15 |
| V002 | 66666666F | Sara Blanco | VIN003 | Corolla | Toyota | Negro | VEN02 | Roberto Díaz | SUC02 | Valencia | Valencia | María López | 2025-06-18 |
| V003 | 55555555E | Miguel Soto | VIN004 | Qashqai | Nissan | Azul | VEN01 | Lucía Ramos | SUC01 | Barcelona | Barcelona | Juan Pérez | 2025-07-10 |

**TELÉFONOS_CLIENTES**


| Cliente_DNI | Teléfono |
| :-- | :-- |
| 55555555E | 611222333 |
| 55555555E | 622444555 |
| 66666666F | 633666777 |

**Nueva clave primaria:** (ID_Venta, VIN_Vehículo)

***

### 📝 Paso 2: Análisis y normalización a Segunda Forma Normal (2FN)

**¿Qué incumple la 2FN?**

Dependencias parciales:

- **Cliente_DNI** → Nombre_Cliente
- **VIN_Vehículo** → Modelo, Marca, Color
- **Vendedor_ID** → Nombre_Vendedor, Sucursal_Vendedor, Ciudad_Sucursal, Provincia_Sucursal, Gerente_Sucursal
- **ID_Venta** → Cliente_DNI, Vendedor_ID, Fecha_Venta

**Solución:** Crear tablas separadas.

**VENTAS**


| ID_Venta | Cliente_DNI | Vendedor_ID | Fecha_Venta |
| :-- | :-- | :-- | :-- |
| V001 | 55555555E | VEN01 | 2025-06-15 |
| V002 | 66666666F | VEN02 | 2025-06-18 |
| V003 | 55555555E | VEN01 | 2025-07-10 |

**DETALLE_VENTAS**


| ID_Venta | VIN_Vehículo |
| :-- | :-- |
| V001 | VIN001 |
| V001 | VIN002 |
| V002 | VIN003 |
| V003 | VIN004 |

**CLIENTES**


| Cliente_DNI | Nombre_Cliente |
| :-- | :-- |
| 55555555E | Miguel Soto |
| 66666666F | Sara Blanco |

**VEHÍCULOS**


| VIN_Vehículo | Modelo | Marca | Color |
| :-- | :-- | :-- | :-- |
| VIN001 | Ibiza | SEAT | Rojo |
| VIN002 | León | SEAT | Blanco |
| VIN003 | Corolla | Toyota | Negro |
| VIN004 | Qashqai | Nissan | Azul |

**VENDEDORES**


| Vendedor_ID | Nombre_Vendedor | Sucursal_Vendedor | Ciudad_Sucursal | Provincia_Sucursal | Gerente_Sucursal |
| :-- | :-- | :-- | :-- | :-- | :-- |
| VEN01 | Lucía Ramos | SUC01 | Barcelona | Barcelona | Juan Pérez |
| VEN02 | Roberto Díaz | SUC02 | Valencia | Valencia | María López |


***

### 📝 Paso 3: Análisis y normalización a Tercera Forma Normal (3FN)

**¿Qué incumple la 3FN?**

En la tabla VENDEDORES existen **dependencias transitivas**:

- Vendedor_ID → Sucursal_Vendedor → Ciudad_Sucursal, Provincia_Sucursal, Gerente_Sucursal

**Solución:** Extraer información de sucursales.

**VENDEDORES (normalizado a 3FN)**


| Vendedor_ID | Nombre_Vendedor | Sucursal_Vendedor |
| :-- | :-- | :-- |
| VEN01 | Lucía Ramos | SUC01 |
| VEN02 | Roberto Díaz | SUC02 |

**SUCURSALES**


| Sucursal | Ciudad | Provincia | Gerente |
| :-- | :-- | :-- | :-- |
| SUC01 | Barcelona | Barcelona | Juan Pérez |
| SUC02 | Valencia | Valencia | María López |

✅ Ahora está en 3FN.

***

### 📝 Paso 4: Análisis y normalización a Forma Normal de Boyce-Codd (FNBC)

**¿Qué incumple la FNBC?**

Supongamos que existe la restricción: **cada gerente solo puede gestionar sucursales en una provincia específica** (política empresarial de asignación territorial). Esto crea:

- Sucursal → Ciudad, Provincia, Gerente
- Gerente → Provincia

Aquí **Gerente** determina **Provincia**, pero Gerente no es superclave, violando FNBC.

**Solución para FNBC:**

**SUCURSALES (normalizado a FNBC)**


| Sucursal | Ciudad | Gerente |
| :-- | :-- | :-- |
| SUC01 | Barcelona | Juan Pérez |
| SUC02 | Valencia | María López |

**GERENTES_PROVINCIAS**


| Gerente | Provincia |
| :-- | :-- |
| Juan Pérez | Barcelona |
| María López | Valencia |

**CIUDADES_PROVINCIAS**


| Ciudad | Provincia |
| :-- | :-- |
| Barcelona | Barcelona |
| Valencia | Valencia |

✅ Ahora está en FNBC.

**Resultado final:** 9 tablas normalizadas hasta FNBC.

***

## Ejercicio 6: Sistema de Gestión de Proyectos de Construcción

### 🎯 Situación inicial

Se tiene la siguiente tabla que registra asignaciones de empleados a proyectos de construcción:


| ID_Asignación | ID_Proyecto | Nombre_Proyecto | Ubicación_Proyecto | Código_Postal_Proyecto | Municipio | Comarca | ID_Empleados | Nombres_Empleados | Categorías | Departamentos | Jefes_Departamento | Salarios_Base_Departamento | Horas_Asignadas | Fecha_Asignación |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| A001 | P001 | Edificio Rosales | Calle Mayor 15 | 50001 | Zaragoza | Zaragoza | EMP01, EMP02 | Luis García, Ana Martín | Ingeniero, Arquitecto | Ingeniería, Arquitectura | Ing. Pérez, Arq. López | 45000, 48000 | 120, 80 | 2025-08-01 |
| A002 | P002 | Centro Comercial Sur | Avenida Sur 200 | 29001 | Málaga | Málaga | EMP03 | Pedro Ruiz | Jefe de Obra | Construcción | Const. Ramírez | 42000 | 160 | 2025-08-05 |
| A003 | P001 | Edificio Rosales | Calle Mayor 15 | 50001 | Zaragoza | Zaragoza | EMP03 | Pedro Ruiz | Jefe de Obra | Construcción | Const. Ramírez | 42000 | 100 | 2025-08-10 |

**Clave primaria:** ID_Asignación

***

### 📝 Paso 1: Análisis y normalización a Primera Forma Normal (1FN)

**¿Qué incumple la 1FN?**

- **ID_Empleados**, **Nombres_Empleados**, **Categorías**, **Departamentos**, **Jefes_Departamento**, **Salarios_Base_Departamento** y **Horas_Asignadas** contienen valores múltiples

**Solución:** Separar en filas individuales.

**ASIGNACIONES_1FN**


| ID_Asignación | ID_Proyecto | Nombre_Proyecto | Ubicación_Proyecto | Código_Postal_Proyecto | Municipio | Comarca | ID_Empleado | Nombre_Empleado | Categoría | Departamento | Jefe_Departamento | Salario_Base_Departamento | Horas_Asignadas | Fecha_Asignación |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| A001 | P001 | Edificio Rosales | Calle Mayor 15 | 50001 | Zaragoza | Zaragoza | EMP01 | Luis García | Ingeniero | Ingeniería | Ing. Pérez | 45000 | 120 | 2025-08-01 |
| A001 | P001 | Edificio Rosales | Calle Mayor 15 | 50001 | Zaragoza | Zaragoza | EMP02 | Ana Martín | Arquitecto | Arquitectura | Arq. López | 48000 | 80 | 2025-08-01 |
| A002 | P002 | Centro Comercial Sur | Avenida Sur 200 | 29001 | Málaga | Málaga | EMP03 | Pedro Ruiz | Jefe de Obra | Construcción | Const. Ramírez | 42000 | 160 | 2025-08-05 |
| A003 | P001 | Edificio Rosales | Calle Mayor 15 | 50001 | Zaragoza | Zaragoza | EMP03 | Pedro Ruiz | Jefe de Obra | Construcción | Const. Ramírez | 42000 | 100 | 2025-08-10 |

**Nueva clave primaria:** (ID_Asignación, ID_Empleado)

***

### 📝 Paso 2: Análisis y normalización a Segunda Forma Normal (2FN)

**¿Qué incumple la 2FN?**

Dependencias parciales:

- **ID_Proyecto** → Nombre_Proyecto, Ubicación_Proyecto, Código_Postal_Proyecto, Municipio, Comarca
- **ID_Empleado** → Nombre_Empleado, Categoría, Departamento, Jefe_Departamento, Salario_Base_Departamento
- **ID_Asignación** → ID_Proyecto, Fecha_Asignación
- **(ID_Asignación, ID_Empleado)** → Horas_Asignadas

**Solución:** Dividir en tablas separadas.

**ASIGNACIONES**


| ID_Asignación | ID_Proyecto | Fecha_Asignación |
| :-- | :-- | :-- |
| A001 | P001 | 2025-08-01 |
| A002 | P002 | 2025-08-05 |
| A003 | P001 | 2025-08-10 |

**DETALLE_ASIGNACIONES**


| ID_Asignación | ID_Empleado | Horas_Asignadas |
| :-- | :-- | :-- |
| A001 | EMP01 | 120 |
| A001 | EMP02 | 80 |
| A002 | EMP03 | 160 |
| A003 | EMP03 | 100 |

**PROYECTOS**


| ID_Proyecto | Nombre_Proyecto | Ubicación_Proyecto | Código_Postal_Proyecto | Municipio | Comarca |
| :-- | :-- | :-- | :-- | :-- | :-- |
| P001 | Edificio Rosales | Calle Mayor 15 | 50001 | Zaragoza | Zaragoza |
| P002 | Centro Comercial Sur | Avenida Sur 200 | 29001 | Málaga | Málaga |

**EMPLEADOS**


| ID_Empleado | Nombre_Empleado | Categoría | Departamento | Jefe_Departamento | Salario_Base_Departamento |
| :-- | :-- | :-- | :-- | :-- | :-- |
| EMP01 | Luis García | Ingeniero | Ingeniería | Ing. Pérez | 45000 |
| EMP02 | Ana Martín | Arquitecto | Arquitectura | Arq. López | 48000 |
| EMP03 | Pedro Ruiz | Jefe de Obra | Construcción | Const. Ramírez | 42000 |


***

### 📝 Paso 3: Análisis y normalización a Tercera Forma Normal (3FN)

**¿Qué incumple la 3FN?**

En PROYECTOS hay **dependencia transitiva**:

- ID_Proyecto → Código_Postal_Proyecto → Municipio, Comarca

En EMPLEADOS hay **dependencia transitiva**:

- ID_Empleado → Departamento → Jefe_Departamento, Salario_Base_Departamento

**Solución:** Separar información dependiente.

**PROYECTOS (normalizado a 3FN)**


| ID_Proyecto | Nombre_Proyecto | Ubicación_Proyecto | Código_Postal_Proyecto |
| :-- | :-- | :-- | :-- |
| P001 | Edificio Rosales | Calle Mayor 15 | 50001 |
| P002 | Centro Comercial Sur | Avenida Sur 200 | 29001 |

**CÓDIGOS_POSTALES_UBICACIÓN**


| Código_Postal | Municipio | Comarca |
| :-- | :-- | :-- |
| 50001 | Zaragoza | Zaragoza |
| 29001 | Málaga | Málaga |

**EMPLEADOS (normalizado a 3FN)**


| ID_Empleado | Nombre_Empleado | Categoría | Departamento |
| :-- | :-- | :-- | :-- |
| EMP01 | Luis García | Ingeniero | Ingeniería |
| EMP02 | Ana Martín | Arquitecto | Arquitectura |
| EMP03 | Pedro Ruiz | Jefe de Obra | Construcción |

**DEPARTAMENTOS**


| Departamento | Jefe_Departamento | Salario_Base |
| :-- | :-- | :-- |
| Ingeniería | Ing. Pérez | 45000 |
| Arquitectura | Arq. López | 48000 |
| Construcción | Const. Ramírez | 42000 |

✅ Ahora está en 3FN.

***

### 📝 Paso 4: Análisis y normalización a Forma Normal de Boyce-Codd (FNBC)

**¿Qué incumple la FNBC?**

En la tabla DEPARTAMENTOS, supongamos que existe la restricción: **cada jefe solo puede dirigir un departamento, pero un departamento puede tener múltiples jefes en diferentes turnos**. Si añadimos turno:


| Departamento | Turno | Jefe_Departamento | Salario_Base |
| :-- | :-- | :-- | :-- |
| Ingeniería | Mañana | Ing. Pérez | 45000 |
| Ingeniería | Tarde | Ing. Gómez | 45000 |

Clave candidata: (Departamento, Turno)

Pero si existe: **Jefe_Departamento → Turno** (cada jefe trabaja en un turno fijo)

Aquí Jefe_Departamento determina Turno, pero no es superclave, violando FNBC.

**Solución para FNBC:**

**DEPARTAMENTOS_TURNOS**


| Departamento | Turno | Jefe_Departamento |
| :-- | :-- | :-- |
| Ingeniería | Mañana | Ing. Pérez |
| Arquitectura | Mañana | Arq. López |
| Construcción | Mañana | Const. Ramírez |

**JEFES_TURNOS**


| Jefe_Departamento | Turno |
| :-- | :-- |
| Ing. Pérez | Mañana |
| Arq. López | Mañana |
| Const. Ramírez | Mañana |

**DEPARTAMENTOS_SALARIOS**


| Departamento | Salario_Base |
| :-- | :-- |
| Ingeniería | 45000 |
| Arquitectura | 48000 |
| Construcción | 42000 |

✅ Ahora está en FNBC.

**Resultado final:** 10 tablas normalizadas hasta FNBC.

***

Estos 4 ejercicios incluyen violaciones específicas en 3FN y FNBC para que los alumnos practiquen todos los niveles de normalización. Cada ejercicio presenta dependencias transitivas claras en 3FN y situaciones de FNBC donde determinantes que no son superclaves generan problemas de redundancia.

---

# 📚 Ejercicios Avanzados: Normalización hasta 3FN

## Ejercicio 7: Sistema de Gestión de Eventos y Conferencias

### 🎯 Situación inicial

Se tiene la siguiente tabla que registra información sobre ponencias en conferencias académicas:


| ID_Ponencia | Título_Ponencia | IDs_Conferencia | Nombres_Conferencia | Fechas_Conferencia | Ciudades_Conferencia | Países | Códigos_País | Continentes | DNIs_Ponentes | Nombres_Ponentes | Emails_Ponentes | Universidades_Ponentes | Códigos_Postales_Unis | Ciudades_Unis | IDs_Sala | Nombres_Sala | Capacidades_Sala | Edificios_Sala | Plantas_Edificio | Coordinador_Edificio | Duración_Minutos | Categorías_Ponencia |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| PON001 | IA en Medicina | CONF01, CONF02 | TechSummit 2025, MedAI Forum | 2025-09-15, 2025-10-20 | Madrid, Barcelona | España, España | ES, ES | Europa, Europa | 11111111A, 22222222B | Dr. Martínez, Dra. López | martinez@uni.es, lopez@uni.es | Universidad Complutense, Universidad Barcelona | 28040, 08007 | Madrid, Barcelona | S101, S202 | Auditorio A, Sala Magna | 200, 150 | Edificio Norte, Edificio Central | 3, 2 | Coord. García, Coord. Ruiz | 90, 60 | Inteligencia Artificial, Machine Learning |
| PON002 | Blockchain y Finanzas | CONF01 | TechSummit 2025 | 2025-09-15 | Madrid | España | ES | Europa | 33333333C | Dr. Fernández | fernandez@uni.es | Universidad Politécnica | 28006 | Madrid | S103 | Sala Innovación | 80 | Edificio Sur | 1 | Coord. Sánchez | 120 | Blockchain, Fintech |
| PON003 | Robótica Educativa | CONF03 | EduTech Congress | 2025-11-10 | Valencia | España | ES | Europa | 11111111A, 44444444D | Dr. Martínez, Dr. Ramírez | martinez@uni.es, ramirez@uni.es | Universidad Complutense, Universidad Valencia | 28040, 46010 | Madrid, Valencia | S301 | Sala Polivalente | 100 | Edificio Este | 2 | Coord. Torres | 75 | Robótica, Educación |

**Clave primaria:** ID_Ponencia

***

### 📝 Paso 1: Análisis y normalización a Primera Forma Normal (1FN)

**¿Qué incumple la 1FN?**

Múltiples atributos contienen valores multivaluados:

- **IDs_Conferencia**, **Nombres_Conferencia**, **Fechas_Conferencia**, **Ciudades_Conferencia**, **Países**, **Códigos_País**, **Continentes**
- **DNIs_Ponentes**, **Nombres_Ponentes**, **Emails_Ponentes**, **Universidades_Ponentes**, **Códigos_Postales_Unis**, **Ciudades_Unis**
- **IDs_Sala**, **Nombres_Sala**, **Capacidades_Sala**, **Edificios_Sala**, **Plantas_Edificio**, **Coordinador_Edificio**, **Duración_Minutos**
- **Categorías_Ponencia**

**Solución:** Crear combinaciones únicas de ponencia-conferencia-ponente-sala-categoría.

**PONENCIAS_1FN**


| ID_Ponencia | Título_Ponencia | ID_Conferencia | Nombre_Conferencia | Fecha_Conferencia | Ciudad_Conferencia | País | Código_País | Continente | DNI_Ponente | Nombre_Ponente | Email_Ponente | Universidad_Ponente | Código_Postal_Uni | Ciudad_Uni | ID_Sala | Nombre_Sala | Capacidad_Sala | Edificio_Sala | Planta_Edificio | Coordinador_Edificio | Duración_Minutos | Categoría_Ponencia |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| PON001 | IA en Medicina | CONF01 | TechSummit 2025 | 2025-09-15 | Madrid | España | ES | Europa | 11111111A | Dr. Martínez | martinez@uni.es | Universidad Complutense | 28040 | Madrid | S101 | Auditorio A | 200 | Edificio Norte | 3 | Coord. García | 90 | Inteligencia Artificial |
| PON001 | IA en Medicina | CONF01 | TechSummit 2025 | 2025-09-15 | Madrid | España | ES | Europa | 11111111A | Dr. Martínez | martinez@uni.es | Universidad Complutense | 28040 | Madrid | S101 | Auditorio A | 200 | Edificio Norte | 3 | Coord. García | 90 | Machine Learning |
| PON001 | IA en Medicina | CONF01 | TechSummit 2025 | 2025-09-15 | Madrid | España | ES | Europa | 22222222B | Dra. López | lopez@uni.es | Universidad Barcelona | 08007 | Barcelona | S101 | Auditorio A | 200 | Edificio Norte | 3 | Coord. García | 90 | Inteligencia Artificial |
| PON001 | IA en Medicina | CONF01 | TechSummit 2025 | 2025-09-15 | Madrid | España | ES | Europa | 22222222B | Dra. López | lopez@uni.es | Universidad Barcelona | 08007 | Barcelona | S101 | Auditorio A | 200 | Edificio Norte | 3 | Coord. García | 90 | Machine Learning |
| PON001 | IA en Medicina | CONF02 | MedAI Forum | 2025-10-20 | Barcelona | España | ES | Europa | 11111111A | Dr. Martínez | martinez@uni.es | Universidad Complutense | 28040 | Madrid | S202 | Sala Magna | 150 | Edificio Central | 2 | Coord. Ruiz | 60 | Inteligencia Artificial |
| PON001 | IA en Medicina | CONF02 | MedAI Forum | 2025-10-20 | Barcelona | España | ES | Europa | 11111111A | Dr. Martínez | martinez@uni.es | Universidad Complutense | 28040 | Madrid | S202 | Sala Magna | 150 | Edificio Central | 2 | Coord. Ruiz | 60 | Machine Learning |
| PON001 | IA en Medicina | CONF02 | MedAI Forum | 2025-10-20 | Barcelona | España | ES | Europa | 22222222B | Dra. López | lopez@uni.es | Universidad Barcelona | 08007 | Barcelona | S202 | Sala Magna | 150 | Edificio Central | 2 | Coord. Ruiz | 60 | Inteligencia Artificial |
| PON001 | IA en Medicina | CONF02 | MedAI Forum | 2025-10-20 | Barcelona | España | ES | Europa | 22222222B | Dra. López | lopez@uni.es | Universidad Barcelona | 08007 | Barcelona | S202 | Sala Magna | 150 | Edificio Central | 2 | Coord. Ruiz | 60 | Machine Learning |
| PON002 | Blockchain y Finanzas | CONF01 | TechSummit 2025 | 2025-09-15 | Madrid | España | ES | Europa | 33333333C | Dr. Fernández | fernandez@uni.es | Universidad Politécnica | 28006 | Madrid | S103 | Sala Innovación | 80 | Edificio Sur | 1 | Coord. Sánchez | 120 | Blockchain |
| PON002 | Blockchain y Finanzas | CONF01 | TechSummit 2025 | 2025-09-15 | Madrid | España | ES | Europa | 33333333C | Dr. Fernández | fernandez@uni.es | Universidad Politécnica | 28006 | Madrid | S103 | Sala Innovación | 80 | Edificio Sur | 1 | Coord. Sánchez | 120 | Fintech |
| PON003 | Robótica Educativa | CONF03 | EduTech Congress | 2025-11-10 | Valencia | España | ES | Europa | 11111111A | Dr. Martínez | martinez@uni.es | Universidad Complutense | 28040 | Madrid | S301 | Sala Polivalente | 100 | Edificio Este | 2 | Coord. Torres | 75 | Robótica |
| PON003 | Robótica Educativa | CONF03 | EduTech Congress | 2025-11-10 | Valencia | España | ES | Europa | 11111111A | Dr. Martínez | martinez@uni.es | Universidad Complutense | 28040 | Madrid | S301 | Sala Polivalente | 100 | Edificio Este | 2 | Coord. Torres | 75 | Educación |
| PON003 | Robótica Educativa | CONF03 | EduTech Congress | 2025-11-10 | Valencia | España | ES | Europa | 44444444D | Dr. Ramírez | ramirez@uni.es | Universidad Valencia | 46010 | Valencia | S301 | Sala Polivalente | 100 | Edificio Este | 2 | Coord. Torres | 75 | Robótica |
| PON003 | Robótica Educativa | CONF03 | EduTech Congress | 2025-11-10 | Valencia | España | ES | Europa | 44444444D | Dr. Ramírez | ramirez@uni.es | Universidad Valencia | 46010 | Valencia | S301 | Sala Polivalente | 100 | Edificio Este | 2 | Coord. Torres | 75 | Educación |

**Nueva clave primaria:** (ID_Ponencia, ID_Conferencia, DNI_Ponente, ID_Sala, Categoría_Ponencia)

***

### 📝 Paso 2: Análisis y normalización a Segunda Forma Normal (2FN)

**¿Qué incumple la 2FN?**

Existen múltiples dependencias funcionales parciales:

- **ID_Ponencia** → Título_Ponencia
- **ID_Conferencia** → Nombre_Conferencia, Fecha_Conferencia, Ciudad_Conferencia, País, Código_País, Continente
- **DNI_Ponente** → Nombre_Ponente, Email_Ponente, Universidad_Ponente, Código_Postal_Uni, Ciudad_Uni
- **ID_Sala** → Nombre_Sala, Capacidad_Sala, Edificio_Sala, Planta_Edificio, Coordinador_Edificio
- **(ID_Ponencia, ID_Conferencia, ID_Sala)** → Duración_Minutos

**Solución:** Separar en tablas según las dependencias.

**PONENCIAS**


| ID_Ponencia | Título_Ponencia |
| :-- | :-- |
| PON001 | IA en Medicina |
| PON002 | Blockchain y Finanzas |
| PON003 | Robótica Educativa |

**CONFERENCIAS**


| ID_Conferencia | Nombre_Conferencia | Fecha_Conferencia | Ciudad_Conferencia | País | Código_País | Continente |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| CONF01 | TechSummit 2025 | 2025-09-15 | Madrid | España | ES | Europa |
| CONF02 | MedAI Forum | 2025-10-20 | Barcelona | España | ES | Europa |
| CONF03 | EduTech Congress | 2025-11-10 | Valencia | España | ES | Europa |

**PONENTES**


| DNI_Ponente | Nombre_Ponente | Email_Ponente | Universidad_Ponente | Código_Postal_Uni | Ciudad_Uni |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 11111111A | Dr. Martínez | martinez@uni.es | Universidad Complutense | 28040 | Madrid |
| 22222222B | Dra. López | lopez@uni.es | Universidad Barcelona | 08007 | Barcelona |
| 33333333C | Dr. Fernández | fernandez@uni.es | Universidad Politécnica | 28006 | Madrid |
| 44444444D | Dr. Ramírez | ramirez@uni.es | Universidad Valencia | 46010 | Valencia |

**SALAS**


| ID_Sala | Nombre_Sala | Capacidad_Sala | Edificio_Sala | Planta_Edificio | Coordinador_Edificio |
| :-- | :-- | :-- | :-- | :-- | :-- |
| S101 | Auditorio A | 200 | Edificio Norte | 3 | Coord. García |
| S103 | Sala Innovación | 80 | Edificio Sur | 1 | Coord. Sánchez |
| S202 | Sala Magna | 150 | Edificio Central | 2 | Coord. Ruiz |
| S301 | Sala Polivalente | 100 | Edificio Este | 2 | Coord. Torres |

**PONENCIAS_CONFERENCIAS_SALAS**


| ID_Ponencia | ID_Conferencia | ID_Sala | Duración_Minutos |
| :-- | :-- | :-- | :-- |
| PON001 | CONF01 | S101 | 90 |
| PON001 | CONF02 | S202 | 60 |
| PON002 | CONF01 | S103 | 120 |
| PON003 | CONF03 | S301 | 75 |

**PONENCIAS_PONENTES**


| ID_Ponencia | DNI_Ponente |
| :-- | :-- |
| PON001 | 11111111A |
| PON001 | 22222222B |
| PON002 | 33333333C |
| PON003 | 11111111A |
| PON003 | 44444444D |

**PONENCIAS_CATEGORÍAS**


| ID_Ponencia | Categoría |
| :-- | :-- |
| PON001 | Inteligencia Artificial |
| PON001 | Machine Learning |
| PON002 | Blockchain |
| PON002 | Fintech |
| PON003 | Robótica |
| PON003 | Educación |


***

### 📝 Paso 3: Análisis y normalización a Tercera Forma Normal (3FN)

**¿Qué incumple la 3FN?**

Existen **dependencias transitivas** en varias tablas:

En **CONFERENCIAS**:

- ID_Conferencia → Ciudad_Conferencia → País
- ID_Conferencia → País → Código_País, Continente

En **PONENTES**:

- DNI_Ponente → Universidad_Ponente → Código_Postal_Uni, Ciudad_Uni

En **SALAS**:

- ID_Sala → Edificio_Sala → Planta_Edificio, Coordinador_Edificio

**Solución:** Eliminar dependencias transitivas creando tablas adicionales.

**CONFERENCIAS (normalizado a 3FN)**


| ID_Conferencia | Nombre_Conferencia | Fecha_Conferencia | Ciudad_Conferencia |
| :-- | :-- | :-- | :-- |
| CONF01 | TechSummit 2025 | 2025-09-15 | Madrid |
| CONF02 | MedAI Forum | 2025-10-20 | Barcelona |
| CONF03 | EduTech Congress | 2025-11-10 | Valencia |

**CIUDADES**


| Ciudad | País |
| :-- | :-- |
| Madrid | España |
| Barcelona | España |
| Valencia | España |

**PAÍSES**


| País | Código_País | Continente |
| :-- | :-- | :-- |
| España | ES | Europa |

**PONENTES (normalizado a 3FN)**


| DNI_Ponente | Nombre_Ponente | Email_Ponente | Universidad_Ponente |
| :-- | :-- | :-- | :-- |
| 11111111A | Dr. Martínez | martinez@uni.es | Universidad Complutense |
| 22222222B | Dra. López | lopez@uni.es | Universidad Barcelona |
| 33333333C | Dr. Fernández | fernandez@uni.es | Universidad Politécnica |
| 44444444D | Dr. Ramírez | ramirez@uni.es | Universidad Valencia |

**UNIVERSIDADES**


| Universidad | Código_Postal | Ciudad |
| :-- | :-- | :-- |
| Universidad Complutense | 28040 | Madrid |
| Universidad Barcelona | 08007 | Barcelona |
| Universidad Politécnica | 28006 | Madrid |
| Universidad Valencia | 46010 | Valencia |

**SALAS (normalizado a 3FN)**


| ID_Sala | Nombre_Sala | Capacidad_Sala | Edificio_Sala |
| :-- | :-- | :-- | :-- |
| S101 | Auditorio A | 200 | Edificio Norte |
| S103 | Sala Innovación | 80 | Edificio Sur |
| S202 | Sala Magna | 150 | Edificio Central |
| S301 | Sala Polivalente | 100 | Edificio Este |

**EDIFICIOS**


| Edificio | Planta | Coordinador |
| :-- | :-- | :-- |
| Edificio Norte | 3 | Coord. García |
| Edificio Sur | 1 | Coord. Sánchez |
| Edificio Central | 2 | Coord. Ruiz |
| Edificio Este | 2 | Coord. Torres |

✅ **Todas las tablas están ahora en 3FN.**

**Resultado final:** 11 tablas normalizadas hasta 3FN.

***
=======
## Ejercicio 1: Sistema de Gestión de Biblioteca

### 🎯 Situación inicial

Se tiene la siguiente tabla que almacena información sobre préstamos de libros en una biblioteca:


| ID_Prestamo | DNI_Usuario | Nombre_Usuario | Teléfonos | ISBN_Libros | Títulos_Libros | Autores_Libros | Fecha_Prestamo |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| 1 | 12345678A | Ana García | 666111222, 666333444 | 978-84-376-0494-7, 978-84-206-8186-8 | El Quijote, Cien años de soledad | Miguel de Cervantes, Gabriel García Márquez | 2025-01-15 |
| 2 | 23456789B | Luis Martín | 677555666 | 978-84-376-0494-7 | El Quijote | Miguel de Cervantes | 2025-01-20 |
| 3 | 12345678A | Ana García | 666111222, 666333444 | 978-84-204-8238-9 | La sombra del viento | Carlos Ruiz Zafón | 2025-02-01 |

**Clave primaria:** ID_Prestamo

***

### 📝 Paso 1: Análisis y normalización a Primera Forma Normal (1FN)

**¿Qué incumple la 1FN?**

La tabla NO cumple con la 1FN porque:

- El atributo **Teléfonos** contiene valores múltiples (varios teléfonos separados por comas)
- Los atributos **ISBN_Libros**, **Títulos_Libros** y **Autores_Libros** contienen valores múltiples cuando se prestan varios libros

**Regla de 1FN:** Todos los atributos deben contener valores atómicos (indivisibles), no puede haber grupos repetitivos ni atributos multivaluados.

**Solución:** Crear filas separadas para cada combinación de préstamo-libro y eliminar atributos multivaluados.

**Tabla normalizada a 1FN:**

**PRESTAMOS_1FN**


| ID_Prestamo | DNI_Usuario | Nombre_Usuario | ISBN_Libro | Título_Libro | Autor_Libro | Fecha_Prestamo |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| 1 | 12345678A | Ana García | 978-84-376-0494-7 | El Quijote | Miguel de Cervantes | 2025-01-15 |
| 1 | 12345678A | Ana García | 978-84-206-8186-8 | Cien años de soledad | Gabriel García Márquez | 2025-01-15 |
| 2 | 23456789B | Luis Martín | 978-84-376-0494-7 | El Quijote | Miguel de Cervantes | 2025-01-20 |
| 3 | 12345678A | Ana García | 978-84-204-8238-9 | La sombra del viento | Carlos Ruiz Zafón | 2025-02-01 |

**TELEFONOS_USUARIOS**


| DNI_Usuario | Teléfono |
| :-- | :-- |
| 12345678A | 666111222 |
| 12345678A | 666333444 |
| 23456789B | 677555666 |

**Nueva clave primaria:** (ID_Prestamo, ISBN_Libro)

***

### 📝 Paso 2: Análisis y normalización a Segunda Forma Normal (2FN)

**¿Qué incumple la 2FN?**

La tabla PRESTAMOS_1FN NO cumple con la 2FN porque existen **dependencias funcionales parciales**:

- **DNI_Usuario** → Nombre_Usuario (depende solo de parte de la clave)
- **ISBN_Libro** → Título_Libro, Autor_Libro (depende solo de parte de la clave)
- **ID_Prestamo** → DNI_Usuario, Fecha_Prestamo (depende solo de parte de la clave)

**Regla de 2FN:** No debe haber dependencias funcionales parciales. Todos los atributos no clave deben depender de la clave primaria completa.

**Solución:** Separar en tablas donde cada atributo dependa completamente de su clave.

**Tablas normalizadas a 2FN:**

**PRESTAMOS**


| ID_Prestamo | DNI_Usuario | Fecha_Prestamo |
| :-- | :-- | :-- |
| 1 | 12345678A | 2025-01-15 |
| 2 | 23456789B | 2025-01-20 |
| 3 | 12345678A | 2025-02-01 |

**DETALLE_PRESTAMOS**


| ID_Prestamo | ISBN_Libro |
| :-- | :-- |
| 1 | 978-84-376-0494-7 |
| 1 | 978-84-206-8186-8 |
| 2 | 978-84-376-0494-7 |
| 3 | 978-84-204-8238-9 |

**USUARIOS**


| DNI_Usuario | Nombre_Usuario |
| :-- | :-- |
| 12345678A | Ana García |
| 23456789B | Luis Martín |

**LIBROS**


| ISBN_Libro | Título_Libro | Autor_Libro |
| :-- | :-- | :-- |
| 978-84-376-0494-7 | El Quijote | Miguel de Cervantes |
| 978-84-206-8186-8 | Cien años de soledad | Gabriel García Márquez |
| 978-84-204-8238-9 | La sombra del viento | Carlos Ruiz Zafón |


***

### 📝 Paso 3: Análisis y normalización a Tercera Forma Normal (3FN)

**¿Qué incumple la 3FN?**

Las tablas ya cumplen con la 3FN porque NO existen **dependencias funcionales transitivas**. Todos los atributos no clave dependen únicamente de la clave primaria y no de otros atributos no clave.

✅ Las tablas están en 3FN.

***

### 📝 Paso 4: Análisis y normalización a Forma Normal de Boyce-Codd (FNBC)

**¿Qué incumple la FNBC?**

Las tablas ya cumplen con la FNBC porque en todas las dependencias funcionales, el determinante es una superclave (clave candidata).

✅ Las tablas están en FNBC.

***

## Ejercicio 2: Sistema de Gestión de Clínica Médica

### 🎯 Situación inicial

Se tiene la siguiente tabla que almacena información sobre consultas médicas:


| ID_Consulta | DNI_Paciente | Nombre_Paciente | Edad_Paciente | ID_Médicos | Nombres_Médicos | Especialidades | Diagnósticos | Medicamentos_Recetados | Fecha_Consulta |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| 101 | 11111111A | María López | 45 | M01, M02 | Dr. Pérez, Dra. Sánchez | Cardiología, Medicina General | Hipertensión | Enalapril 10mg, Aspirina 100mg | 2025-03-10 |
| 102 | 22222222B | Juan Torres | 32 | M02 | Dra. Sánchez | Medicina General | Gripe | Paracetamol 500mg | 2025-03-11 |
| 103 | 11111111A | María López | 45 | M01 | Dr. Pérez | Cardiología | Control rutinario | - | 2025-04-15 |

**Clave primaria:** ID_Consulta

***

### 📝 Paso 1: Análisis y normalización a Primera Forma Normal (1FN)

**¿Qué incumple la 1FN?**

La tabla NO cumple con la 1FN porque:

- **ID_Médicos**, **Nombres_Médicos** y **Especialidades** contienen valores múltiples
- **Diagnósticos** y **Medicamentos_Recetados** contienen valores múltiples

**Solución:** Separar los valores múltiples en filas individuales.

**Tablas normalizadas a 1FN:**

**CONSULTAS_1FN**


| ID_Consulta | DNI_Paciente | Nombre_Paciente | Edad_Paciente | ID_Médico | Nombre_Médico | Especialidad | Diagnóstico | Medicamento_Recetado | Fecha_Consulta |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| 101 | 11111111A | María López | 45 | M01 | Dr. Pérez | Cardiología | Hipertensión | Enalapril 10mg | 2025-03-10 |
| 101 | 11111111A | María López | 45 | M01 | Dr. Pérez | Cardiología | Hipertensión | Aspirina 100mg | 2025-03-10 |
| 101 | 11111111A | María López | 45 | M02 | Dra. Sánchez | Medicina General | Hipertensión | Enalapril 10mg | 2025-03-10 |
| 101 | 11111111A | María López | 45 | M02 | Dra. Sánchez | Medicina General | Hipertensión | Aspirina 100mg | 2025-03-10 |
| 102 | 22222222B | Juan Torres | 32 | M02 | Dra. Sánchez | Medicina General | Gripe | Paracetamol 500mg | 2025-03-11 |
| 103 | 11111111A | María López | 45 | M01 | Dr. Pérez | Cardiología | Control rutinario | NULL | 2025-04-15 |

**Nueva clave primaria:** (ID_Consulta, ID_Médico, Medicamento_Recetado)

***

### 📝 Paso 2: Análisis y normalización a Segunda Forma Normal (2FN)

**¿Qué incumple la 2FN?**

Existen dependencias funcionales parciales:

- **DNI_Paciente** → Nombre_Paciente, Edad_Paciente
- **ID_Médico** → Nombre_Médico, Especialidad
- **ID_Consulta** → DNI_Paciente, Fecha_Consulta

**Solución:** Crear tablas separadas para eliminar dependencias parciales.

**Tablas normalizadas a 2FN:**

**CONSULTAS**


| ID_Consulta | DNI_Paciente | Fecha_Consulta |
| :-- | :-- | :-- |
| 101 | 11111111A | 2025-03-10 |
| 102 | 22222222B | 2025-03-11 |
| 103 | 11111111A | 2025-04-15 |

**PACIENTES**


| DNI_Paciente | Nombre_Paciente | Edad_Paciente |
| :-- | :-- | :-- |
| 11111111A | María López | 45 |
| 22222222B | Juan Torres | 32 |

**MÉDICOS**


| ID_Médico | Nombre_Médico | Especialidad |
| :-- | :-- | :-- |
| M01 | Dr. Pérez | Cardiología |
| M02 | Dra. Sánchez | Medicina General |

**CONSULTAS_MÉDICOS**


| ID_Consulta | ID_Médico |
| :-- | :-- |
| 101 | M01 |
| 101 | M02 |
| 102 | M02 |
| 103 | M01 |

**DIAGNÓSTICOS**


| ID_Consulta | Diagnóstico |
| :-- | :-- |
| 101 | Hipertensión |
| 102 | Gripe |
| 103 | Control rutinario |

**MEDICAMENTOS_RECETADOS**


| ID_Consulta | Medicamento |
| :-- | :-- |
| 101 | Enalapril 10mg |
| 101 | Aspirina 100mg |
| 102 | Paracetamol 500mg |


***

### 📝 Paso 3: Análisis y normalización a Tercera Forma Normal (3FN)

**¿Qué incumple la 3FN?**

En la tabla PACIENTES existe una dependencia transitiva:

- DNI_Paciente → Nombre_Paciente (correcta)
- Sin embargo, la **Edad_Paciente** debería calcularse dinámicamente a partir de la fecha de nacimiento, ya que cambia con el tiempo

**Mejora sugerida:** Sustituir Edad_Paciente por Fecha_Nacimiento.

**PACIENTES (mejorado para 3FN)**


| DNI_Paciente | Nombre_Paciente | Fecha_Nacimiento |
| :-- | :-- | :-- |
| 11111111A | María López | 1980-05-12 |
| 22222222B | Juan Torres | 1993-08-23 |

✅ Ahora todas las tablas están en 3FN.

***

### 📝 Paso 4: Análisis y normalización a Forma Normal de Boyce-Codd (FNBC)

**¿Qué incumple la FNBC?**

Todas las tablas cumplen con la FNBC porque en cada dependencia funcional, el determinante es una superclave.

✅ Las tablas están en FNBC.

***

## Ejercicio 3: Sistema de Cursos Universitarios

### 🎯 Situación inicial

Se tiene la siguiente tabla que almacena información sobre inscripciones en cursos universitarios:


| ID_Inscripción | ID_Estudiante | Nombre_Estudiante | Cursos_Inscritos | Profesores | Códigos_Postales_Profesores | Ciudades_Profesores | Provincias | Departamento_Prof | Fecha_Inscripción |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| 1 | E001 | Pedro Ruiz | MAT101, FIS201 | Prof. García, Prof. López | 28001, 28002 | Madrid, Madrid | Madrid, Madrid | Matemáticas, Física | 2025-09-01 |
| 2 | E002 | Laura Vega | MAT101 | Prof. García | 28001 | Madrid | Madrid | Matemáticas | 2025-09-01 |
| 3 | E001 | Pedro Ruiz | QUIM301 | Prof. Martínez | 41001 | Sevilla | Sevilla | Química | 2025-09-05 |

**Clave primaria:** ID_Inscripción

***

### 📝 Paso 1: Análisis y normalización a Primera Forma Normal (1FN)

**¿Qué incumple la 1FN?**

- **Cursos_Inscritos**, **Profesores**, **Códigos_Postales_Profesores**, **Ciudades_Profesores**, **Provincias** y **Departamento_Prof** contienen valores múltiples

**Solución:** Crear filas separadas por cada curso.

**INSCRIPCIONES_1FN**


| ID_Inscripción | ID_Estudiante | Nombre_Estudiante | Curso | Profesor | Código_Postal_Prof | Ciudad_Prof | Provincia_Prof | Departamento_Prof | Fecha_Inscripción |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| 1 | E001 | Pedro Ruiz | MAT101 | Prof. García | 28001 | Madrid | Madrid | Matemáticas | 2025-09-01 |
| 1 | E001 | Pedro Ruiz | FIS201 | Prof. López | 28002 | Madrid | Madrid | Física | 2025-09-01 |
| 2 | E002 | Laura Vega | MAT101 | Prof. García | 28001 | Madrid | Madrid | Matemáticas | 2025-09-01 |
| 3 | E001 | Pedro Ruiz | QUIM301 | Prof. Martínez | 41001 | Sevilla | Sevilla | Química | 2025-09-05 |

**Nueva clave primaria:** (ID_Inscripción, Curso)

***

### 📝 Paso 2: Análisis y normalización a Segunda Forma Normal (2FN)

**¿Qué incumple la 2FN?**

Dependencias funcionales parciales:

- **ID_Estudiante** → Nombre_Estudiante
- **ID_Inscripción** → ID_Estudiante, Fecha_Inscripción
- **Curso** → Profesor, Código_Postal_Prof, Ciudad_Prof, Provincia_Prof, Departamento_Prof

**Solución:** Separar en tablas independientes.

**INSCRIPCIONES**


| ID_Inscripción | ID_Estudiante | Fecha_Inscripción |
| :-- | :-- | :-- |
| 1 | E001 | 2025-09-01 |
| 2 | E002 | 2025-09-01 |
| 3 | E001 | 2025-09-05 |

**DETALLE_INSCRIPCIONES**


| ID_Inscripción | Curso |
| :-- | :-- |
| 1 | MAT101 |
| 1 | FIS201 |
| 2 | MAT101 |
| 3 | QUIM301 |

**ESTUDIANTES**


| ID_Estudiante | Nombre_Estudiante |
| :-- | :-- |
| E001 | Pedro Ruiz |
| E002 | Laura Vega |

**CURSOS**


| Curso | Profesor | Código_Postal_Prof | Ciudad_Prof | Provincia_Prof | Departamento_Prof |
| :-- | :-- | :-- | :-- | :-- | :-- |
| MAT101 | Prof. García | 28001 | Madrid | Madrid | Matemáticas |
| FIS201 | Prof. López | 28002 | Madrid | Madrid | Física |
| QUIM301 | Prof. Martínez | 41001 | Sevilla | Sevilla | Química |


***

### 📝 Paso 3: Análisis y normalización a Tercera Forma Normal (3FN)

**¿Qué incumple la 3FN?**

En la tabla CURSOS existen **dependencias transitivas**:

- Curso → Código_Postal_Prof → Ciudad_Prof, Provincia_Prof
- Curso → Profesor → Código_Postal_Prof, Ciudad_Prof, Provincia_Prof, Departamento_Prof

**Solución:** Extraer la información de los profesores a una tabla separada.

**CURSOS (normalizado a 3FN)**


| Curso | Profesor |
| :-- | :-- |
| MAT101 | Prof. García |
| FIS201 | Prof. López |
| QUIM301 | Prof. Martínez |

**PROFESORES**


| Profesor | Código_Postal | Ciudad | Provincia | Departamento |
| :-- | :-- | :-- | :-- | :-- |
| Prof. García | 28001 | Madrid | Madrid | Matemáticas |
| Prof. López | 28002 | Madrid | Madrid | Física |
| Prof. Martínez | 41001 | Sevilla | Sevilla | Química |

Pero aún hay dependencia transitiva:

- Profesor → Código_Postal → Ciudad, Provincia

**PROFESORES (corregido)**


| Profesor | Código_Postal | Departamento |
| :-- | :-- | :-- |
| Prof. García | 28001 | Matemáticas |
| Prof. López | 28002 | Física |
| Prof. Martínez | 41001 | Química |

**CÓDIGOS_POSTALES**


| Código_Postal | Ciudad | Provincia |
| :-- | :-- | :-- |
| 28001 | Madrid | Madrid |
| 28002 | Madrid | Madrid |
| 41001 | Sevilla | Sevilla |

✅ Ahora está en 3FN.

***

### 📝 Paso 4: Análisis y normalización a Forma Normal de Boyce-Codd (FNBC)

**¿Qué incumple la FNBC?**

En la tabla CURSOS tenemos un problema: si un profesor puede impartir múltiples cursos, pero queremos registrar qué profesor imparte qué curso en qué período, podríamos tener:

- (Curso, Período) → Profesor
- Profesor → Departamento

Suponiendo que la tabla CURSOS debería incluir información de períodos académicos:

**Situación problemática para FNBC:**


| Curso | Período | Profesor |
| :-- | :-- | :-- |
| MAT101 | 2025-1 | Prof. García |
| FIS201 | 2025-1 | Prof. López |
| MAT101 | 2025-2 | Prof. García |

Aquí la clave candidata es (Curso, Período), pero si agregamos que cada profesor solo puede impartir un curso específico (restricción del dominio), tendríamos:

- Profesor → Curso

Esto viola FNBC porque Profesor no es superclave.

**Solución para FNBC:** Mantener las tablas separadas como están actualmente cumple FNBC.

✅ Las tablas están en FNBC.

***

## Ejercicio 4: Sistema de Gestión Hospitalaria

### 🎯 Situación inicial

Se tiene la siguiente tabla que registra las cirugías realizadas en un hospital:


| ID_Cirugía | Paciente_DNI | Nombre_Paciente | Códigos_Diagnóstico | Descripciones_Diagnóstico | ID_Quirófanos | Pisos | ID_Cirujano_Principal | Nombre_Cirujano | Especialidad_Cirujano | Hospital_Cirujano | Director_Hospital | Fecha_Cirugía |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| C001 | 33333333C | Ana Ruiz | D01, D02 | Apendicitis, Peritonitis | Q1, Q2 | 3, 3 | CIR001 | Dr. Fernández | Cirugía General | Hospital Central | Dr. Gómez | 2025-05-10 |
| C002 | 44444444D | Carlos Díaz | D03 | Fractura fémur | Q3 | 4 | CIR002 | Dra. Morales | Traumatología | Hospital Norte | Dra. Silva | 2025-05-12 |
| C003 | 33333333C | Ana Ruiz | D04 | Revisión post-operatoria | Q1 | 3 | CIR001 | Dr. Fernández | Cirugía General | Hospital Central | Dr. Gómez | 2025-05-20 |

**Clave primaria:** ID_Cirugía

***

### 📝 Paso 1: Análisis y normalización a Primera Forma Normal (1FN)

**¿Qué incumple la 1FN?**

- **Códigos_Diagnóstico**, **Descripciones_Diagnóstico**, **ID_Quirófanos** y **Pisos** contienen valores múltiples

**Solución:** Separar en filas individuales.

**CIRUGÍAS_1FN**


| ID_Cirugía | Paciente_DNI | Nombre_Paciente | Código_Diagnóstico | Descripción_Diagnóstico | ID_Quirófano | Piso | ID_Cirujano_Principal | Nombre_Cirujano | Especialidad_Cirujano | Hospital_Cirujano | Director_Hospital | Fecha_Cirugía |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| C001 | 33333333C | Ana Ruiz | D01 | Apendicitis | Q1 | 3 | CIR001 | Dr. Fernández | Cirugía General | Hospital Central | Dr. Gómez | 2025-05-10 |
| C001 | 33333333C | Ana Ruiz | D01 | Apendicitis | Q2 | 3 | CIR001 | Dr. Fernández | Cirugía General | Hospital Central | Dr. Gómez | 2025-05-10 |
| C001 | 33333333C | Ana Ruiz | D02 | Peritonitis | Q1 | 3 | CIR001 | Dr. Fernández | Cirugía General | Hospital Central | Dr. Gómez | 2025-05-10 |
| C001 | 33333333C | Ana Ruiz | D02 | Peritonitis | Q2 | 3 | CIR001 | Dr. Fernández | Cirugía General | Hospital Central | Dr. Gómez | 2025-05-10 |
| C002 | 44444444D | Carlos Díaz | D03 | Fractura fémur | Q3 | 4 | CIR002 | Dra. Morales | Traumatología | Hospital Norte | Dra. Silva | 2025-05-12 |
| C003 | 33333333C | Ana Ruiz | D04 | Revisión post-operatoria | Q1 | 3 | CIR001 | Dr. Fernández | Cirugía General | Hospital Central | Dr. Gómez | 2025-05-20 |

**Nueva clave primaria:** (ID_Cirugía, Código_Diagnóstico, ID_Quirófano)

***

### 📝 Paso 2: Análisis y normalización a Segunda Forma Normal (2FN)

**¿Qué incumple la 2FN?**

Dependencias parciales:

- **Paciente_DNI** → Nombre_Paciente
- **Código_Diagnóstico** → Descripción_Diagnóstico
- **ID_Quirófano** → Piso
- **ID_Cirujano_Principal** → Nombre_Cirujano, Especialidad_Cirujano, Hospital_Cirujano, Director_Hospital
- **ID_Cirugía** → Paciente_DNI, ID_Cirujano_Principal, Fecha_Cirugía

**Solución:** Dividir en tablas separadas.

**CIRUGÍAS**


| ID_Cirugía | Paciente_DNI | ID_Cirujano_Principal | Fecha_Cirugía |
| :-- | :-- | :-- | :-- |
| C001 | 33333333C | CIR001 | 2025-05-10 |
| C002 | 44444444D | CIR002 | 2025-05-12 |
| C003 | 33333333C | CIR001 | 2025-05-20 |

**CIRUGÍAS_DIAGNÓSTICOS**


| ID_Cirugía | Código_Diagnóstico |
| :-- | :-- |
| C001 | D01 |
| C001 | D02 |
| C002 | D03 |
| C003 | D04 |

**CIRUGÍAS_QUIRÓFANOS**


| ID_Cirugía | ID_Quirófano |
| :-- | :-- |
| C001 | Q1 |
| C001 | Q2 |
| C002 | Q3 |
| C003 | Q1 |

**PACIENTES**


| Paciente_DNI | Nombre_Paciente |
| :-- | :-- |
| 33333333C | Ana Ruiz |
| 44444444D | Carlos Díaz |

**DIAGNÓSTICOS**


| Código_Diagnóstico | Descripción_Diagnóstico |
| :-- | :-- |
| D01 | Apendicitis |
| D02 | Peritonitis |
| D03 | Fractura fémur |
| D04 | Revisión post-operatoria |

**QUIRÓFANOS**


| ID_Quirófano | Piso |
| :-- | :-- |
| Q1 | 3 |
| Q2 | 3 |
| Q3 | 4 |

**CIRUJANOS**


| ID_Cirujano | Nombre_Cirujano | Especialidad_Cirujano | Hospital_Cirujano | Director_Hospital |
| :-- | :-- | :-- | :-- | :-- |
| CIR001 | Dr. Fernández | Cirugía General | Hospital Central | Dr. Gómez |
| CIR002 | Dra. Morales | Traumatología | Hospital Norte | Dra. Silva |


***

### 📝 Paso 3: Análisis y normalización a Tercera Forma Normal (3FN)

**¿Qué incumple la 3FN?**

En la tabla CIRUJANOS existe una **dependencia transitiva**:

- ID_Cirujano → Hospital_Cirujano → Director_Hospital

**Solución:** Separar la información del hospital.

**CIRUJANOS (normalizado a 3FN)**


| ID_Cirujano | Nombre_Cirujano | Especialidad_Cirujano | Hospital_Cirujano |
| :-- | :-- | :-- | :-- |
| CIR001 | Dr. Fernández | Cirugía General | Hospital Central |
| CIR002 | Dra. Morales | Traumatología | Hospital Norte |

**HOSPITALES**


| Hospital | Director |
| :-- | :-- |
| Hospital Central | Dr. Gómez |
| Hospital Norte | Dra. Silva |

✅ Ahora está en 3FN.

***

### 📝 Paso 4: Análisis y normalización a Forma Normal de Boyce-Codd (FNBC)

**¿Qué incumple la FNBC?**

Supongamos que en la tabla CIRUJANOS existe la siguiente restricción del dominio: **cada especialidad solo puede ejercerse en un hospital específico** (por políticas del sistema de salud regional). Esto crea:

- ID_Cirujano → Especialidad_Cirujano, Hospital_Cirujano
- Especialidad_Cirujano → Hospital_Cirujano

Aquí **Especialidad_Cirujano** determina **Hospital_Cirujano**, pero Especialidad_Cirujano no es superclave, violando FNBC.

**Solución para FNBC:** Dividir la tabla.

**CIRUJANOS (normalizado a FNBC)**


| ID_Cirujano | Nombre_Cirujano | Especialidad_Cirujano |
| :-- | :-- | :-- |
| CIR001 | Dr. Fernández | Cirugía General |
| CIR002 | Dra. Morales | Traumatología |

**ESPECIALIDADES_HOSPITALES**


| Especialidad | Hospital |
| :-- | :-- |
| Cirugía General | Hospital Central |
| Traumatología | Hospital Norte |

✅ Ahora está en FNBC.

***

## Ejercicio 5: Sistema de Concesionario de Vehículos

### 🎯 Situación inicial

Se tiene la siguiente tabla que registra ventas de vehículos:


| ID_Venta | Cliente_DNI | Nombre_Cliente | Teléfonos_Cliente | VINs_Vehículos | Modelos | Marcas | Colores | Vendedor_ID | Nombre_Vendedor | Sucursal_Vendedor | Ciudad_Sucursal | Provincia_Sucursal | Gerente_Sucursal | Fecha_Venta |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| V001 | 55555555E | Miguel Soto | 611222333, 622444555 | VIN001, VIN002 | Ibiza, León | SEAT, SEAT | Rojo, Blanco | VEN01 | Lucía Ramos | SUC01 | Barcelona | Barcelona | Juan Pérez | 2025-06-15 |
| V002 | 66666666F | Sara Blanco | 633666777 | VIN003 | Corolla | Toyota | Negro | VEN02 | Roberto Díaz | SUC02 | Valencia | Valencia | María López | 2025-06-18 |
| V003 | 55555555E | Miguel Soto | 611222333, 622444555 | VIN004 | Qashqai | Nissan | Azul | VEN01 | Lucía Ramos | SUC01 | Barcelona | Barcelona | Juan Pérez | 2025-07-10 |

**Clave primaria:** ID_Venta

***

### 📝 Paso 1: Análisis y normalización a Primera Forma Normal (1FN)

**¿Qué incumple la 1FN?**

- **Teléfonos_Cliente**, **VINs_Vehículos**, **Modelos**, **Marcas** y **Colores** contienen valores múltiples

**Solución:** Atomizar valores múltiples.

**VENTAS_1FN**


| ID_Venta | Cliente_DNI | Nombre_Cliente | VIN_Vehículo | Modelo | Marca | Color | Vendedor_ID | Nombre_Vendedor | Sucursal_Vendedor | Ciudad_Sucursal | Provincia_Sucursal | Gerente_Sucursal | Fecha_Venta |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| V001 | 55555555E | Miguel Soto | VIN001 | Ibiza | SEAT | Rojo | VEN01 | Lucía Ramos | SUC01 | Barcelona | Barcelona | Juan Pérez | 2025-06-15 |
| V001 | 55555555E | Miguel Soto | VIN002 | León | SEAT | Blanco | VEN01 | Lucía Ramos | SUC01 | Barcelona | Barcelona | Juan Pérez | 2025-06-15 |
| V002 | 66666666F | Sara Blanco | VIN003 | Corolla | Toyota | Negro | VEN02 | Roberto Díaz | SUC02 | Valencia | Valencia | María López | 2025-06-18 |
| V003 | 55555555E | Miguel Soto | VIN004 | Qashqai | Nissan | Azul | VEN01 | Lucía Ramos | SUC01 | Barcelona | Barcelona | Juan Pérez | 2025-07-10 |

**TELÉFONOS_CLIENTES**


| Cliente_DNI | Teléfono |
| :-- | :-- |
| 55555555E | 611222333 |
| 55555555E | 622444555 |
| 66666666F | 633666777 |

**Nueva clave primaria:** (ID_Venta, VIN_Vehículo)

***

### 📝 Paso 2: Análisis y normalización a Segunda Forma Normal (2FN)

**¿Qué incumple la 2FN?**

Dependencias parciales:

- **Cliente_DNI** → Nombre_Cliente
- **VIN_Vehículo** → Modelo, Marca, Color
- **Vendedor_ID** → Nombre_Vendedor, Sucursal_Vendedor, Ciudad_Sucursal, Provincia_Sucursal, Gerente_Sucursal
- **ID_Venta** → Cliente_DNI, Vendedor_ID, Fecha_Venta

**Solución:** Crear tablas separadas.

**VENTAS**


| ID_Venta | Cliente_DNI | Vendedor_ID | Fecha_Venta |
| :-- | :-- | :-- | :-- |
| V001 | 55555555E | VEN01 | 2025-06-15 |
| V002 | 66666666F | VEN02 | 2025-06-18 |
| V003 | 55555555E | VEN01 | 2025-07-10 |

**DETALLE_VENTAS**


| ID_Venta | VIN_Vehículo |
| :-- | :-- |
| V001 | VIN001 |
| V001 | VIN002 |
| V002 | VIN003 |
| V003 | VIN004 |

**CLIENTES**


| Cliente_DNI | Nombre_Cliente |
| :-- | :-- |
| 55555555E | Miguel Soto |
| 66666666F | Sara Blanco |

**VEHÍCULOS**


| VIN_Vehículo | Modelo | Marca | Color |
| :-- | :-- | :-- | :-- |
| VIN001 | Ibiza | SEAT | Rojo |
| VIN002 | León | SEAT | Blanco |
| VIN003 | Corolla | Toyota | Negro |
| VIN004 | Qashqai | Nissan | Azul |

**VENDEDORES**


| Vendedor_ID | Nombre_Vendedor | Sucursal_Vendedor | Ciudad_Sucursal | Provincia_Sucursal | Gerente_Sucursal |
| :-- | :-- | :-- | :-- | :-- | :-- |
| VEN01 | Lucía Ramos | SUC01 | Barcelona | Barcelona | Juan Pérez |
| VEN02 | Roberto Díaz | SUC02 | Valencia | Valencia | María López |


***

### 📝 Paso 3: Análisis y normalización a Tercera Forma Normal (3FN)

**¿Qué incumple la 3FN?**

En la tabla VENDEDORES existen **dependencias transitivas**:

- Vendedor_ID → Sucursal_Vendedor → Ciudad_Sucursal, Provincia_Sucursal, Gerente_Sucursal

**Solución:** Extraer información de sucursales.

**VENDEDORES (normalizado a 3FN)**


| Vendedor_ID | Nombre_Vendedor | Sucursal_Vendedor |
| :-- | :-- | :-- |
| VEN01 | Lucía Ramos | SUC01 |
| VEN02 | Roberto Díaz | SUC02 |

**SUCURSALES**


| Sucursal | Ciudad | Provincia | Gerente |
| :-- | :-- | :-- | :-- |
| SUC01 | Barcelona | Barcelona | Juan Pérez |
| SUC02 | Valencia | Valencia | María López |

✅ Ahora está en 3FN.

***

### 📝 Paso 4: Análisis y normalización a Forma Normal de Boyce-Codd (FNBC)

**¿Qué incumple la FNBC?**

Supongamos que existe la restricción: **cada gerente solo puede gestionar sucursales en una provincia específica** (política empresarial de asignación territorial). Esto crea:

- Sucursal → Ciudad, Provincia, Gerente
- Gerente → Provincia

Aquí **Gerente** determina **Provincia**, pero Gerente no es superclave, violando FNBC.

**Solución para FNBC:**

**SUCURSALES (normalizado a FNBC)**


| Sucursal | Ciudad | Gerente |
| :-- | :-- | :-- |
| SUC01 | Barcelona | Juan Pérez |
| SUC02 | Valencia | María López |

**GERENTES_PROVINCIAS**


| Gerente | Provincia |
| :-- | :-- |
| Juan Pérez | Barcelona |
| María López | Valencia |

**CIUDADES_PROVINCIAS**


| Ciudad | Provincia |
| :-- | :-- |
| Barcelona | Barcelona |
| Valencia | Valencia |

✅ Ahora está en FNBC.

***

## Ejercicio 6: Sistema de Gestión de Proyectos de Construcción

### 🎯 Situación inicial

Se tiene la siguiente tabla que registra asignaciones de empleados a proyectos de construcción:


| ID_Asignación | ID_Proyecto | Nombre_Proyecto | Ubicación_Proyecto | Código_Postal_Proyecto | Municipio | Comarca | ID_Empleados | Nombres_Empleados | Categorías | Departamentos | Jefes_Departamento | Salarios_Base_Departamento | Horas_Asignadas | Fecha_Asignación |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| A001 | P001 | Edificio Rosales | Calle Mayor 15 | 50001 | Zaragoza | Zaragoza | EMP01, EMP02 | Luis García, Ana Martín | Ingeniero, Arquitecto | Ingeniería, Arquitectura | Ing. Pérez, Arq. López | 45000, 48000 | 120, 80 | 2025-08-01 |
| A002 | P002 | Centro Comercial Sur | Avenida Sur 200 | 29001 | Málaga | Málaga | EMP03 | Pedro Ruiz | Jefe de Obra | Construcción | Const. Ramírez | 42000 | 160 | 2025-08-05 |
| A003 | P001 | Edificio Rosales | Calle Mayor 15 | 50001 | Zaragoza | Zaragoza | EMP03 | Pedro Ruiz | Jefe de Obra | Construcción | Const. Ramírez | 42000 | 100 | 2025-08-10 |

**Clave primaria:** ID_Asignación

***

### 📝 Paso 1: Análisis y normalización a Primera Forma Normal (1FN)

**¿Qué incumple la 1FN?**

- **ID_Empleados**, **Nombres_Empleados**, **Categorías**, **Departamentos**, **Jefes_Departamento**, **Salarios_Base_Departamento** y **Horas_Asignadas** contienen valores múltiples

**Solución:** Separar en filas individuales.

**ASIGNACIONES_1FN**


| ID_Asignación | ID_Proyecto | Nombre_Proyecto | Ubicación_Proyecto | Código_Postal_Proyecto | Municipio | Comarca | ID_Empleado | Nombre_Empleado | Categoría | Departamento | Jefe_Departamento | Salario_Base_Departamento | Horas_Asignadas | Fecha_Asignación |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| A001 | P001 | Edificio Rosales | Calle Mayor 15 | 50001 | Zaragoza | Zaragoza | EMP01 | Luis García | Ingeniero | Ingeniería | Ing. Pérez | 45000 | 120 | 2025-08-01 |
| A001 | P001 | Edificio Rosales | Calle Mayor 15 | 50001 | Zaragoza | Zaragoza | EMP02 | Ana Martín | Arquitecto | Arquitectura | Arq. López | 48000 | 80 | 2025-08-01 |
| A002 | P002 | Centro Comercial Sur | Avenida Sur 200 | 29001 | Málaga | Málaga | EMP03 | Pedro Ruiz | Jefe de Obra | Construcción | Const. Ramírez | 42000 | 160 | 2025-08-05 |
| A003 | P001 | Edificio Rosales | Calle Mayor 15 | 50001 | Zaragoza | Zaragoza | EMP03 | Pedro Ruiz | Jefe de Obra | Construcción | Const. Ramírez | 42000 | 100 | 2025-08-10 |

**Nueva clave primaria:** (ID_Asignación, ID_Empleado)

***

### 📝 Paso 2: Análisis y normalización a Segunda Forma Normal (2FN)

**¿Qué incumple la 2FN?**

Dependencias parciales:

- **ID_Proyecto** → Nombre_Proyecto, Ubicación_Proyecto, Código_Postal_Proyecto, Municipio, Comarca
- **ID_Empleado** → Nombre_Empleado, Categoría, Departamento, Jefe_Departamento, Salario_Base_Departamento
- **ID_Asignación** → ID_Proyecto, Fecha_Asignación
- **(ID_Asignación, ID_Empleado)** → Horas_Asignadas

**Solución:** Dividir en tablas separadas.

**ASIGNACIONES**


| ID_Asignación | ID_Proyecto | Fecha_Asignación |
| :-- | :-- | :-- |
| A001 | P001 | 2025-08-01 |
| A002 | P002 | 2025-08-05 |
| A003 | P001 | 2025-08-10 |

**DETALLE_ASIGNACIONES**


| ID_Asignación | ID_Empleado | Horas_Asignadas |
| :-- | :-- | :-- |
| A001 | EMP01 | 120 |
| A001 | EMP02 | 80 |
| A002 | EMP03 | 160 |
| A003 | EMP03 | 100 |

**PROYECTOS**


| ID_Proyecto | Nombre_Proyecto | Ubicación_Proyecto | Código_Postal_Proyecto | Municipio | Comarca |
| :-- | :-- | :-- | :-- | :-- | :-- |
| P001 | Edificio Rosales | Calle Mayor 15 | 50001 | Zaragoza | Zaragoza |
| P002 | Centro Comercial Sur | Avenida Sur 200 | 29001 | Málaga | Málaga |

**EMPLEADOS**


| ID_Empleado | Nombre_Empleado | Categoría | Departamento | Jefe_Departamento | Salario_Base_Departamento |
| :-- | :-- | :-- | :-- | :-- | :-- |
| EMP01 | Luis García | Ingeniero | Ingeniería | Ing. Pérez | 45000 |
| EMP02 | Ana Martín | Arquitecto | Arquitectura | Arq. López | 48000 |
| EMP03 | Pedro Ruiz | Jefe de Obra | Construcción | Const. Ramírez | 42000 |


***

### 📝 Paso 3: Análisis y normalización a Tercera Forma Normal (3FN)

**¿Qué incumple la 3FN?**

En PROYECTOS hay **dependencia transitiva**:

- ID_Proyecto → Código_Postal_Proyecto → Municipio, Comarca

En EMPLEADOS hay **dependencia transitiva**:

- ID_Empleado → Departamento → Jefe_Departamento, Salario_Base_Departamento

**Solución:** Separar información dependiente.

**PROYECTOS (normalizado a 3FN)**


| ID_Proyecto | Nombre_Proyecto | Ubicación_Proyecto | Código_Postal_Proyecto |
| :-- | :-- | :-- | :-- |
| P001 | Edificio Rosales | Calle Mayor 15 | 50001 |
| P002 | Centro Comercial Sur | Avenida Sur 200 | 29001 |

**CÓDIGOS_POSTALES_UBICACIÓN**


| Código_Postal | Municipio | Comarca |
| :-- | :-- | :-- |
| 50001 | Zaragoza | Zaragoza |
| 29001 | Málaga | Málaga |

**EMPLEADOS (normalizado a 3FN)**


| ID_Empleado | Nombre_Empleado | Categoría | Departamento |
| :-- | :-- | :-- | :-- |
| EMP01 | Luis García | Ingeniero | Ingeniería |
| EMP02 | Ana Martín | Arquitecto | Arquitectura |
| EMP03 | Pedro Ruiz | Jefe de Obra | Construcción |

**DEPARTAMENTOS**


| Departamento | Jefe_Departamento | Salario_Base |
| :-- | :-- | :-- |
| Ingeniería | Ing. Pérez | 45000 |
| Arquitectura | Arq. López | 48000 |
| Construcción | Const. Ramírez | 42000 |

✅ Ahora está en 3FN.

***

### 📝 Paso 4: Análisis y normalización a Forma Normal de Boyce-Codd (FNBC)

**¿Qué incumple la FNBC?**

En la tabla DEPARTAMENTOS, supongamos que existe la restricción: **cada jefe solo puede dirigir un departamento, pero un departamento puede tener múltiples jefes en diferentes turnos**. Si añadimos turno:


| Departamento | Turno | Jefe_Departamento | Salario_Base |
| :-- | :-- | :-- | :-- |
| Ingeniería | Mañana | Ing. Pérez | 45000 |
| Ingeniería | Tarde | Ing. Gómez | 45000 |

Clave candidata: (Departamento, Turno)

Pero si existe: **Jefe_Departamento → Turno** (cada jefe trabaja en un turno fijo)

Aquí Jefe_Departamento determina Turno, pero no es superclave, violando FNBC.

**Solución para FNBC:**

**DEPARTAMENTOS_TURNOS**


| Departamento | Turno | Jefe_Departamento |
| :-- | :-- | :-- |
| Ingeniería | Mañana | Ing. Pérez |
| Arquitectura | Mañana | Arq. López |
| Construcción | Mañana | Const. Ramírez |

**JEFES_TURNOS**


| Jefe_Departamento | Turno |
| :-- | :-- |
| Ing. Pérez | Mañana |
| Arq. López | Mañana |
| Const. Ramírez | Mañana |

**DEPARTAMENTOS_SALARIOS**


| Departamento | Salario_Base |
| :-- | :-- |
| Ingeniería | 45000 |
| Arquitectura | 48000 |
| Construcción | 42000 |

✅ Ahora está en FNBC.

## Ejercicio 7: Sistema de Envíos de E‑commerce

### 🎯 Situación inicial

Se tiene la siguiente tabla que registra envíos con un identificador único, el cliente y el código postal, junto a ciudad y provincia derivables del código postal según el dominio de datos.

Clave primaria: ID_Envio.


| ID_Envio | Cliente_ID | CP | Ciudad | Provincia |
| :-- | :-- | :-- | :-- | :-- |
| E001  | C101  | 28001  | Madrid  | Madrid  |
| E002  | C102  | 08001  | Barcelona  | Barcelona  |
| E003  | C101  | 28001  | Madrid  | Madrid  |

**¿En qué forma normal está la tabla?, razona si existe dependencia parcial o transitiva y qué implicaciones tiene sobre 2FN y 3FN.**

Repuesta: *La tabla está en 2FN pero no en 3FN, porque con clave simple no puede haber dependencias parciales (cumple 2FN), pero hay dependencia transitiva ID_Envio → CP y CP → Ciudad, Provincia que viola 3FN según la definición que prohíbe atributos no clave dependientes transitivamente de la clave.​*

Justificación breve: *2FN requiere 1FN y ausencia de dependencias parciales respecto a claves compuestas, lo que se cumple con clave simple, mientras que 3FN exige además eliminar dependencias transitivas como CP → Ciudad, Provincia, por lo que la forma normal alcanzada es 2FN.*

***

## Ejercicio 8: Sistema de Matrícula Universitaria

### 🎯 Situación inicial

Tabla inicial: la tabla MATRICULA recoge quién cursa qué asignatura y con qué profesor, asumiendo la restricción de dominio “cada profesor imparte exactamente una asignatura” (Profesor → Asignatura).​

Clave primaria: (Alumno_ID, Asignatura).


| Alumno_ID | Asignatura | Profesor |
| :-- | :-- | :-- |
| A01  | BD1  | Prof. Ruiz  |
| A02  | BD1  | Prof. Ruiz  |
| A01  | PRG1  | Prof. Vega  |

**¿En qué forma normal está la tabla?, analiza si hay dependencias transitivas y si se viola BCNF dadas las dependencias Profesor → Asignatura y la clave (Alumno_ID, Asignatura).**

Dependencias funcionales del dominio:

- (Alumno_ID, Asignatura) → Profesor, porque para cada alumno en una asignatura concreta existe un profesor asignado a ese grupo.
- Profesor → Asignatura, por la política “cada profesor imparte una única asignatura” definida para este ejemplo didáctico.

Respuesta: *la tabla está en 3FN pero no en BCNF, porque la dependencia Profesor → Asignatura tiene determinante que no es superclave, pero el atributo del lado derecho (Asignatura) es atributo primo al ser parte de la clave, cumpliendo la condición de Zaniolo para 3FN y fallando la exigencia más estricta de BCNF.​*

Justificación breve: *3FN se satisface si para cada X → A, X es superclave o A es atributo primo; aquí A es primo, por lo que 3FN se cumple, mientras que BCNF requeriría que Profesor fuese superclave, lo cual no sucede, luego no es BCNF.*

***

## Ejercicio 9: Detalle de Facturación

### 🎯 Situación inicial

Se registra el detalle de líneas de factura con una clave compuesta y descriptores de producto y cliente incluidos en la misma tabla, típicamente generando dependencias de parte de la clave.
Clave primaria: (Factura_ID, Producto_ID).


| Factura_ID | Producto_ID | Cliente_Nombre | Producto_Nombre | Precio_Unitario | Cantidad |
| :-- | :-- | :-- | :-- | :-- | :-- |
| F001  | P10  | Ana Ruiz  | Teclado  | 25.00  | 2  |
| F001  | P20  | Ana Ruiz  | Ratón  | 15.00  | 1  |
| F002  | P10  | Luis Gil  | Teclado  | 25.00  | 1  |

**¿En qué forma normal está la tabla?, identifica si hay valores atómicos, dependencias parciales como Producto_ID → Producto_Nombre, Precio_Unitario y Factura_ID → Cliente_Nombre, y razona qué implica sobre 2FN.**

Respuesta: *La tabla está en 1FN pero no en 2FN, porque aunque todos los atributos son atómicos y no hay grupos repetidos (cumple 1FN), existen dependencias parciales de atributos no clave respecto a una parte de la clave compuesta, lo que infringe 2FN (p. ej., Producto_ID → Producto_Nombre, Precio_Unitario y Factura_ID → Cliente_Nombre).*
​
Justificación breve: *2FN exige que cada atributo no primo dependa de toda la clave y no solo de una parte, requisito que se incumple por las dependencias señaladas, por lo que la forma normal alcanzada es 1FN.*

***

## Ejercicio 10: Empleados y Departamentos

### 🎯 Situación inicial

Se mantiene una tabla de empleados con su departamento y datos derivados del departamento, con clave simple por DNI, lo que evita dependencias parciales pero mantiene una dependencia transitiva vía Departamento_ID.
Clave primaria: DNI.


| DNI | Nombre | Departamento_ID | Departamento_Nombre | Jefe_Departamento |
| :-- | :-- | :-- | :-- | :-- |
| 11111111A  | María López  | D10  | Ventas  | Sr. Pérez  |
| 22222222B  | Juan Torres  | D20  | Soporte  | Sra. Vega  |
| 33333333C  | Ana Ruiz  | D10  | Ventas  | Sr. Pérez  |

**¿En qué forma normal está la tabla?, analiza si hay dependencias parciales o transitivas con clave simple y valora su impacto en 3FN.**

Solución: *La tabla está en 2FN pero no en 3FN, porque con clave simple no hay dependencias parciales (cumple 2FN), pero existe dependencia transitiva DNI → Departamento_ID y Departamento_ID → Departamento_Nombre, Jefe_Departamento, lo que viola 3FN al no permitir que atributos no clave dependan indirectamente de la clave.​*

Justificación breve: *3FN requiere estar en 2FN y eliminar dependencias transitivas, de modo que al persistir Departamento_ID → (Departamento_Nombre, Jefe_Departamento) la forma normal alcanzada es exactamente 2FN.​*

>>>>>>> 0c7581282b5a58fbd975f93246e891f3325a8fc8