# ESPECIFICACIÓN DE PROYECTO DE SOFTWARE: SISTEMA DE MESA DE AYUDA DE TI (SERVICE DESK)

## 1. METADATOS Y MARCO DEL PROYECTO

- Institución: Universidad Nacional Mayor de San Marcos (UNMSM) - Facultad de Ingeniería de Sistemas e Informática.
- Asignatura: Diseño de Sistemas de Información (Plan 2018, Ciclo VI, Semestre 2026-2).
- Marco Metodológico: Agile Unified Process (AUP) — Fusión de RUP (Inicio, Elaboración, Construcción, Transición) con prácticas e iteraciones ágiles cortas.
- Enfoque de Evaluación:
  - Análisis (20%): Elicitación, RF/RNF (ISO/IEC 25010), BPMN, Casos de Uso, Modelo de Dominio, Trazabilidad.
  - Diseño (50%): Arquitectura 4+1 (Kruchten), Clases de diseño (UML 2.5.1), Diagramas de secuencia con fragmentos combinados, Patrones GRASP/SOLID, Componentes y Despliegue.
  - Programación (30%): Prototipo vertical (_vertical slice_) de 1 a 2 CU críticos con trazabilidad end-to-end y pruebas de caja blanca/negra.

---

## 2. DEFINICIÓN DEL DOMINIO DE NEGOCIO (CASO B)

- Organización / Contexto: Oficina de Tecnologías de la Información de una entidad pública.
- Problema Central: Registro, priorización, escalamiento y cierre de incidencias de TI dentro de acuerdos de nivel de servicio (SLA) formalizados bajo buenas prácticas ITIL.
- Caso de Uso Crítico (Vertical Slice): Registrar una incidencia y escalarla automáticamente al nivel siguiente si se vence el SLA.
- Estilo Arquitectónico Base: MVC + API REST para un microservicio de gestión de tickets.
- Atributos de Calidad Prioritarios (ISO/IEC 25010):
  1. Rendimiento (Tiempo de respuesta de la API).
  2. Disponibilidad (Continuidad operativa del servicio de registro).
  3. Mantenibilidad (Bajo acoplamiento y alta cohesión mediante patrones de diseño).
- Riesgo de Dominio Identificado: Falta de alineación en conceptos ITIL/SLA. Requiere modelar formalmente la matriz de escalamiento (Nivel 1, Nivel 2, Nivel 3) y tiempos de resolución contractuales.

---

## 3. ARQUITECTURA DE SOFTWARE Y PATRONES DE DISEÑO MANDATORIOS

### 3.1. Estilo Arquitectónico

- Microservicio REST estructurado bajo el patrón Modelo-Vista-Controlador (MVC) o Arquitectura en Capas Limpia (Controller, Service/Domain, Repository).

### 3.2. Catálogo de Patrones GoF Obligatorios

1. Chain of Responsibility (Comportamiento):
   - Propósito: Escalamiento jerárquico de tickets por niveles de soporte (Soporte N1 -> Soporte N2 -> Soporte Especializado N3) activado cuando se vence el SLA o la complejidad lo amerita.
2. Factory Method (Creacional):
   - Propósito: Creación e inicialización de tickets según el tipo de incidencia (Hardware, Software, Redes, Accesos), asignando dinámicamente sus tiempos SLA y prioridades por defecto.
3. Observer (Comportamiento):
   - Propósito: Suscripción y emisión de eventos de alerta cuando un SLA está próximo a vencer o ha expirado, desacoplando el motor de monitoreo de los canales de notificación (Email, Webhook, Log).

### 3.3. Principios de Soporte

- Aplicación estricta de principios SOLID y patrones GRASP (Experto en Información, Controlador, Bajo Acoplamiento, Alta Cohesión).

---

## 4. CRONOGRAMA, HITOS Y ENTREGABLES POR FASE AUP

| Sesión        | Hito Sílabo                                | Fase AUP                    | Entregable y Alcance Específico                                                                                                                          |
| :------------ | :----------------------------------------- | :-------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------- |
| S2 - S4       | Kickoff y Análisis Preliminar              | Inicio (_Inception_)        | Elicitación de RF/RNF (ISO/IEC 25010), BPMN 2.0 del proceso actual de atención de incidencias en la entidad pública.                                     |
| S4 (Entrega)  | Informe de la Organización (PL - Unidad I) | Inicio (_Inception_)        | Documento de contexto, definición de problemática SLA, límites del alcance, supuestos y restricciones.                                                   |
| S5 - S7       | Modelado de Dominio y CU                   | Elaboración                 | Modelo Conceptual de Dominio, Modelo de Casos de Uso (UML 2.5.1), especificación detallada del CU crítico (Registrar y Escalar Incidencia).              |
| S7 - S9       | Documento Arquitectura 4+1                 | Elaboración                 | Vistas Lógica, de Procesos, de Desarrollo y Física según Kruchten y estándar ISO/IEC/IEEE 42010.                                                         |
| S9 (Entrega)  | PE1: Diseño Lógico del Sistema             | Elaboración                 | Sustentación técnica del Documento 4+1 y Diagrama de Clases de Diseño.                                                                                   |
| S10 - S12     | Catálogo de Patrones                       | Elaboración -> Construcción | Especificación de Factory Method, Chain of Responsibility y Observer aplicados al Service Desk, justificados con GRASP/SOLID y evidencia de refactoring. |
| S12 (Entrega) | Modelo de Requisitos (PL - Unidad III)     | Elaboración -> Construcción | Catálogo formal de patrones de diseño y refactoring antes/después documentado.                                                                           |
| S13 - S14     | Arquitectura Web y Pruebas                 | Construcción                | Diagramas de secuencia con fragmentos combinados (alt, opt, loop, par) y diseño de casos de prueba.                                                      |
| S15 (Entrega) | PE2: Modelo de Interacciones y Prototipo   | Construcción -> Transición  | Prototipo vertical funcional end-to-end del CU crítico + reporte de pruebas de caja blanca y caja negra.                                                 |
| S16 (Entrega) | Informe Final Integrador                   | Transición                  | Documento consolidado unificado y sustentación final del proyecto.                                                                                       |

---

## 5. LINEAMIENTOS Y RESTRICCIONES DE PROGRAMACIÓN (_VERTICAL SLICE_)

- Alcance Funcional: 1 a 2 CU críticos implementados de punta a punta (Capa de Presentación/API -> Capa de Dominio/Lógica con Patrones -> Capa de Persistencia/BD).
- Flujo Mandatorio a Ejecutar:
  1. `POST /tickets`: Creación del ticket vía `Factory Method` calculando la fecha/hora máxima de resolución del SLA.
  2. Disparo de vencimiento / verificación de SLA: Mecanismo que detecta la expiración y notifica a observadores vía `Observer`.
  3. Escalamiento automático: Delegación a través de `Chain of Responsibility` cambiando el nivel asignado (N1 -> N2).
- Trazabilidad Obligatoria: Toda clase e interfaz del código debe tener correspondencia directa 1:1 con el Diagrama de Clases de Diseño y el Diagrama de Componentes de PE1.
- Criterios de Exclusión (No evaluado / Penalizado):
  - No desarrollar pantallas secundarias no asociadas al CU crítico.
  - No implementar autenticación compleja ni módulos de reportería que no pertenezcan al vertical slice.
  - No agregar código fuera del alcance del documento de arquitectura (resta puntaje por incoherencia).
- Estrategia de Pruebas Requerida:
  - Caja Negra: Particiones de equivalencia y análisis de valores límite sobre los tiempos de vencimiento de SLA.
  - Caja Blanca: Cobertura de caminos (ramas principales y de excepción) sobre la lógica de la cadena de responsabilidad y factoría.

---

## 6. MATRIZ DE RÚBRICAS DE EVALUACIÓN

### 6.1. Informe de la Organización (PL - Unidad I)

- Contexto y Problema (30%): Datos específicos del área TI de la entidad pública, proceso actual de mesa de ayuda.
- Requisitos Iniciales (30%): RF y RNF categorizados según norma ISO/IEC 25010.
- Alcance y Supuestos (20%): Fronteras claras del sistema, exclusiones explícitas y dependencias.
- Redacción Técnica (20%): Vocabulario técnico riguroso (terminología ITIL) y citación formal.

### 6.2. PE1: Diseño Lógico del Sistema (Unidad II)

- Modelo de Casos de Uso (20%): Sintaxis UML 2.5.1 correcta (`<<include>>`, `<<extend>>`), especificación completa del CU crítico.
- Modelo de Dominio (15%): Clases conceptuales coherentes con la gestión de incidencias y SLAs.
- Arquitectura 4+1 (40%): Vistas Lógica, de Procesos, de Desarrollo y Física completas y alineadas a los RNF de rendimiento/disponibilidad.
- Diagrama de Clases de Diseño (15%): Sintaxis UML 2.5.1 estricta (tipos de datos, visibilidad, multiplicidad, agregación vs. composición).
- Sustentación Oral (10%): Justificación técnica mediante tácticas arquitectónicas SEI/ADD.

### 6.3. Modelo de Requisitos: Patrones de Diseño (PL - Unidad III)

- Identificación del Problema (25%): Problema real de diseño en el Service Desk (no ejemplos genéricos).
- Justificación GRASP/SOLID (35%): Explicación formal de por qué la solución con el patrón es superior a la alternativa naive.
- Notación del Patrón (25%): Diagramas de clases UML con las clases concretas del dominio de tickets.
- Refactoring Evidenciado (15%): Diseño antes/después ilustrando la erradicación del code smell.

### 6.4. PE2: Modelo de Interacciones y Prototipo (Unidad IV)[cite: 8, 9]

- Arquitectura Web (15%): Justificación de la API REST / MVC / Microservicio.
- Diagramas de Secuencia (15%): Uso riguroso de fragmentos combinados (`alt`, `opt`, `loop`, `par`) en el CU crítico.
- Prototipo Funcional (35%): Ejecución end-to-end demostrable del vertical slice.
- Plan y Casos de Prueba (25%): Diseño riguroso de partición de equivalencia, valores límite y cobertura de código.
- Evidencia de Ejecución (10%): Registro de resultados de pruebas y gestión de defectos.

---

## 7. ESTÁNDARES FORMALES DE REFERENCIA

- Notación de Modelado: OMG UML 2.5.1 y OMG BPMN 2.0.2.
- Modelo de Calidad: ISO/IEC 25010:2011 (SQuaRE).
- Descripción Arquitectónica: ISO/IEC/IEEE 42010:2011 y Kruchten (1995) 4+1 View Model.
- Buenas Prácticas de Ingeniería: Principios SOLID, GRASP (Larman, 2004) y Tácticas Arquitectónicas (Bass et al., 2021).
