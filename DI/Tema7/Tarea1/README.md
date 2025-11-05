# **TEMA 7: Usabilidad**

## **Tarea 1: Evaluación heurística de usabilidad en flujo de reserva hotelera**

**Modalidad:** Parejas o individual

***

## **Objetivo y alcance**

Realizar una evaluación heurística completa aplicando métricas de usabilidad sobre el flujo de reserva de una web de hotel (ficticia o pública real). El análisis cubrirá el proceso completo: búsqueda, selección de habitación, introducción de datos del cliente y confirmación final. Se recogerán métricas de **efectividad, eficiencia y satisfacción** correspondientes al primer intento del usuario.

***

## **Flujo de tareas obligatorias**

El usuario objetivo debe completar las siguientes tareas en secuencia:

**T1 - Búsqueda con filtros:** Buscar hotel en Sevilla para entrada el día 15 del mes actual y salida dos días después, aplicando el filtro de desayuno incluido.

**T2 - Consulta de disponibilidad:** Abrir la ficha detallada de un hotel del listado obtenido y verificar disponibilidad para las fechas indicadas.

**T3 - Selección y extras:** Seleccionar una habitación doble con desayuno y continuar al proceso de checkout.

**T4 - Datos del cliente:** Completar los campos obligatorios (nombre completo, correo electrónico...) y seleccionar un método de pago, sin completar el pago real.

**T5 - Revisión final:** Localizar y revisar el resumen de reserva, identificando claramente el precio total con impuestos incluidos y la política de cancelación.

***

## **Criterios de éxito por tarea**

Cada tarea se considera completada con éxito si cumple los siguientes requisitos:

- **Éxito T1:** Se muestran resultados correctamente filtrados por fechas y desayuno incluido en menos de 90 segundos, sin ayuda externa.
- **Éxito T2:** Se accede a la ficha del hotel y se visualiza el estado "Disponible" para las fechas seleccionadas en menos de 60 segundos.
- **Éxito T3:** Se añade una habitación doble con desayuno y se accede al checkout en menos de 120 segundos.
- **Éxito T4:** Se completan todos los campos obligatorios sin mensajes de error persistentes en menos de 180 segundos.
- **Éxito T5:** Se identifica el precio total final y la política de cancelación en menos de 60 segundos.

***

## **Métricas a registrar**

Para cada usuario evaluado, registrar las siguientes métricas cuantitativas y cualitativas:

### **Efectividad**

Porcentaje de tareas completadas con éxito:

$$
\text{Efectividad} = \frac{\text{Tareas completadas}}{5} \times 100
$$

### **Eficiencia**

Tiempo en segundos del primer intento por tarea y tiempo total acumulado:

$$
\text{Eficiencia (tiempo total)} = \sum_{i=1}^{5} \text{tiempo}_i
$$

### **Errores**

- Número de errores cometidos por tarea
- Si el usuario requirió ayuda externa
- Si fue necesario deshacer acciones


### **Satisfacción**

Valoración del usuario en escala de 1 (muy insatisfecho) a 5 (muy satisfecho):

- Satisfacción por tarea individual
- Satisfacción global tras completar el flujo completo

***

## **Umbrales de aceptación**

Los resultados obtenidos se compararán con los siguientes umbrales mínimos de aceptabilidad:

### **Efectividad aceptable**

≥ 80% de tareas completadas sin ayuda en el primer intento.

### **Eficiencia orientativa**

- T1 ≤ 90 segundos
- T2 ≤ 60 segundos
- T3 ≤ 120 segundos
- T4 ≤ 180 segundos
- T5 ≤ 60 segundos
- **Tiempo total ≤ 8 minutos**


### **Satisfacción aceptable**

- Promedio global ≥ 4,0
- Sin ítems individuales ≤ 2 en las tareas críticas T3 (selección) y T4 (datos cliente)

***

## **Escala de satisfacción y severidad**

### **Escala de satisfacción por tarea**

1. **Muy insatisfecho** - Experiencia frustrante, no lograría completar sin ayuda
2. **Insatisfecho** - Dificultades significativas, proceso confuso
3. **Neutro** - Aceptable pero mejorable
4. **Satisfecho** - Experiencia fluida y clara
5. **Muy satisfecho** - Experiencia excelente, intuitiva y eficiente

### **Clasificación de severidad de hallazgos**

- **Alta:** Bloquea la tarea o genera error frecuente que impide continuar
- **Media:** Ralentiza significativamente o confunde al usuario, pero permite continuar
- **Baja:** Problema estético o de inconsistencia sin impacto funcional

***

## **Checklist ISO 9241 - Criterios de validación**

Validar el cumplimiento de los siguientes principios de la norma ISO 9241 aplicables al flujo:

### **ISO 9241-110: Principios de diálogo**

**Auto-descriptividad:** Cada paso del flujo presenta etiquetas claras y acciones comprensibles inmediatamente.

**Conformidad con expectativas:** Consistencia en la terminología utilizada (ej: "Reservar", "Continuar", "Pagar") y ubicación coherente de botones principales.

**Control del usuario:** El sistema permite volver atrás sin pérdida de datos introducidos durante el proceso de checkout.

### **ISO 9241-11: Usabilidad y medición**

**Claridad del objetivo:** Cada pantalla comunica claramente su propósito.

**Soporte a métricas:** El diseño facilita la efectividad, eficiencia y satisfacción del usuario.

### **ISO 9241-112: Presentación de información**

**Visibilidad del precio:** El precio total con impuestos y tasas está claramente visible.

**Legibilidad:** Contraste adecuado entre texto y fondo en listados de resultados y pantallas de checkout.

### **ISO 9241-14: Diseño de menús**

**Etiquetas comprensibles:** Los filtros y opciones de menú utilizan terminología clara y no ambigua.

**Estructura apropiada:** La profundidad de navegación no es excesiva.

### **ISO 9241-17: Formularios**

**Ejemplos y formato:** Los campos de email y nombre muestran ejemplos o placeholders orientativos.

**Validaciones comprensibles:** Los mensajes de error no culpabilizan al usuario y explican claramente el problema.

### **Retroalimentación y mensajes**

**Errores específicos:** Los mensajes indican exactamente qué campo tiene problema y proponen una solución.

**Mantenimiento de datos válidos:** Cuando hay un error, el sistema mantiene los datos ya validados.

### **Prevención de errores**

**Deshabilitación de continuar:** El botón de continuar permanece deshabilitado hasta completar los campos mínimos obligatorios.

**Formateo guiado:** El campo de email proporciona formato guiado o validación en tiempo real.

***

## **Entregable**

Documento que incluya:

1. Identificación de la web evaluada (nombre, URL si es pública)
2. Tabla de métricas por usuario y tarea
3. Análisis de cumplimiento del checklist ISO 9241
4. Lista priorizada de hallazgos con severidad asignada
5. Propuestas de mejora concretas y justificadas

**Formato:** PDF
