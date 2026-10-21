# UNIVERSIDAD NACIONAL MAYOR DE SAN MARCOS
## Facultad de Ingeniería de Sistemas e Informática
### DISEÑO DE SISTEMAS DE INFORMACIÓN — PROYECTO INTEGRADOR 2026-2

---

## Metadatos del Proyecto

| Campo | Valor |
| :--- | :--- |
| **Caso asignado (A/B/C)** | [completar] |
| **Organización / sistema** | [completar] |
| **Integrantes del equipo** | [completar nombre y código] |
| **Docente** | [completar] |
| **Semestre académico** | 2026-2 |
| **Versión del documento** | [v1.0 - fecha] |

---

## Pautas Generales y Estándares Obligatorios

- **Consolidación:** Integra los hitos del semestre (Informe de la organización, PE1 Diseño lógico/Requisitos y PE2 Modelo de interacciones).
- **Estándares técnicos requeridos:**
  - Diagramas UML: **UML 2.5.1** (OMG, 2017).
  - Procesos de negocio: **BPMN 2.0** (OMG, 2013).
  - Arquitectura de software: **ISO/IEC/IEEE 42010:2011** (Vistas 4+1 de Kruchten).
  - Calidad y requisitos no funcionales: **ISO/IEC 25010:2011**.
  - Citas y bibliografía: **Normas APA (7.ª ed.)**.
- *Nota para el agente/usuario:* Los bloques `> **Instrucción:**` guían el llenado; los bloques `[...]` representan campos a reemplazar.

---

## 1. Resumen ejecutivo

> **Instrucción:** Máximo 1 página. Sintetice el problema, la solución diseñada, la arquitectura elegida y los resultados del prototipo. Redactar al finalizar el proyecto.

[Resumen ejecutivo del proyecto]

---

## 2. Introducción

### 2.1 Contexto organizacional
> **Instrucción:** Describa la organización (real o simulada) asignada: tamaño, sector y proceso afectado. Depurar del hito de organización.

[Descripción de la organización]

### 2.2 Problema y justificación
> **Instrucción:** Formule el problema como una brecha entre el estado actual y el deseado. Justifique la necesidad de la solución de software.

[Planteamiento del problema y justificación]

### 2.3 Objetivos
> **Instrucción:** 1 objetivo general y 3 a 5 específicos con verbos en infinitivo, alineados al ciclo de vida del curso (analizar, diseñar, implementar, probar).

- **Objetivo General:** [completar]
- **Objetivos Específicos:**
  - [Objetivo específico 1]
  - [Objetivo específico 2]
  - [Objetivo específico 3]
  - [Objetivo específico 4]

### 2.4 Alcance, supuestos y restricciones
> **Instrucción:** Delimite taxativamente qué incluye y qué NO incluye el prototipo funcional (vertical slice). Detalle supuestos y restricciones operativas/tecnológicas.

[Alcance, supuestos y restricciones]

---

## 3. Análisis (Fase de Inicio)

### 3.1 Requisitos funcionales y no funcionales
> **Instrucción:** Clasifique RNF según características ISO/IEC 25010 (adecuación funcional, rendimiento/eficiencia, compatibilidad, usabilidad, fiabilidad, seguridad, mantenibilidad, portabilidad). Asigne ID único y prioridad MoSCoW (Must, Should, Could, Won't).

| Código | Descripción | Tipo / Característica ISO 25010 | Prioridad (MoSCoW) |
| :--- | :--- | :--- | :--- |
| **RF01** | [completar] | Funcional | [Must / Should / Could / Won't] |
| **RF02** | [completar] | Funcional | [Must / Should / Could / Won't] |
| **RNF01**| [completar] | [Ej. Seguridad / Fiabilidad / Rendimiento] | [Must / Should / Could / Won't] |
| **RNF02**| [completar] | [Ej. Usabilidad / Mantenibilidad] | [Must / Should / Could / Won't] |

### 3.2 Modelo de procesos de negocio
> **Instrucción:** Diagrama BPMN 2.0 del proceso actual (*as-is*) y propuesto (*to-be*). Validar pools, lanes, eventos, actividades y compuertas tipadas.

[Diagrama BPMN as-is / to-be]

### 3.3 Modelo de Casos de Uso
> **Instrucción:** Diagrama de Casos de Uso en UML 2.5.1 indicando actores y relaciones (`<<include>>`, `<<extend>>`, generalización).

[Diagrama de Casos de Uso]

#### Especificación del Caso de Uso Crítico

| Elemento | Detalle |
| :--- | :--- |
| **Nombre del CU** | [completar] |
| **Actor(es)** | [completar] |
| **Precondición** | [completar] |
| **Flujo básico** | 1. [Paso 1]<br>2. [Paso 2]<br>3. [Paso 3] |
| **Flujos alternos** | [Flujo alterno A: condición y pasos] |
| **Flujos de excepción** | [Flujo de excepción E: error y recuperación] |
| **Postcondición** | [completar] |

### 3.4 Modelo de Dominio
> **Instrucción:** Diagrama de clases conceptuales (Larman, 2004). Incluye clases del dominio, atributos esenciales, asociaciones rotuladas y multiplicidades. **Sin métodos ni tipos de datos de implementación.**

[Diagrama del Modelo de Dominio]

---

## 4. Diseño (Fase de Elaboración)

### 4.1 Arquitectura de software — Modelo de Vistas 4+1
> **Instrucción:** Conforme a Kruchten (1995) e ISO/IEC/IEEE 42010:2011. Especifique preocupaciones (concerns) y stakeholders para cada vista.

- **Vista Lógica:** Diagrama de paquetes y clases de diseño que soportan los RF.
- **Vista de Procesos:** Concurrencia, hilos, sincronía/asincronía y comunicación en runtime.
- **Vista de Desarrollo:** Organización modular, capas del código y librerías desde la perspectiva de desarrollo.
- **Vista Física (Despliegue):** Nodos físicos/virtuales, contenedores, artefactos y protocolos de red.
- **Escenarios (Casos de Uso integradores):** 1-2 escenarios críticos que trazan y validan la interoperabilidad de las cuatro vistas.

[Diagramas y justificaciones de la arquitectura 4+1]

### 4.2 Diagrama de clases de diseño
> **Instrucción:** Diagrama formal UML 2.5.1. Debe contener métodos, visibilidad (`+`, `-`, `#`), tipos de datos de retorno/parámetros, agregación (rombo vacío) y composición (rombo relleno).

[Diagrama de Clases de Diseño]

### 4.3 Diagramas de secuencia
> **Instrucción:** Mínimo uno por cada CU crítico. Detalle líneas de vida, llamadas sincrónicas/asincrónicas y fragmentos combinados (`alt`, `opt`, `loop`, `par`) coherentes con los flujos de la sección 3.3.

[Diagramas de Secuencia]

### 4.4 Diagrama de estados (si aplica)
> **Instrucción:** Requerido para entidades con ciclo de vida complejo (estados, transiciones, eventos desencadenantes y condiciones de guarda). Si no aplica, incluir justificación técnica explícita.

[Diagrama de Estados o Justificación de no aplicabilidad]

### 4.5 Patrones de diseño aplicados
> **Instrucción:** Detalle los patrones GoF/GRASP aplicados, los problemas arquitectónicos/estructurales resueltos y los principios SOLID/GRASP satisfechos.

| Patrón | Problema que resuelve | Principio GRASP / SOLID | Clase(s) donde se aplica |
| :--- | :--- | :--- | :--- |
| [Ej. Factory Method / Strategy / Observer] | [completar] | [Ej. OCP, Polimorfismo, Bajo Acoplamiento] | [completar] |
| [completar] | [completar] | [completar] | [completar] |

[Diagrama(s) de clases instanciando los patrones en el dominio]

### 4.6 Diagrama de componentes y despliegue
> **Instrucción:** Diagrama de componentes con interfaces provistas/requeridas (`lollipops`/`sockets`) y topología de despliegue final (puede referenciar la vista física de 4.1).

[Diagrama de Componentes y Despliegue]

---

## 5. Arquitectura web y prototipo (Fase de Construcción)

### 5.1 Arquitectura web seleccionada
> **Instrucción:** Justifique el estilo y patrón adoptado (cliente delgado/grueso/SPA, MVC, API RESTful, microservicios/monolito modular) en función de los RNF priorizados.

[Justificación de la arquitectura web]

### 5.2 Alcance del prototipo implementado
> **Instrucción:** Defina los casos de uso cubiertos por el *vertical slice*, stack tecnológico (frontend, backend, base de datos) y matriz de trazabilidad entre clases implementadas y clases de diseño (sección 4.2).

[Alcance, stack tecnológico y trazabilidad del prototipo]

### 5.3 Evidencia de implementación
> **Instrucción:** Enlace al repositorio, estructura del árbol de directorios del proyecto y capturas de pantalla/consola de la ejecución.

- **Repositorio:** [URL de Git / GitHub / GitLab]
- **Estructura del proyecto:**
```text
[Insertar árbol de directorios del código fuente]
```
- **Evidencias visuales:** [Capturas de pantalla o logs de ejecución]

---

## 6. Pruebas de software

### 6.1 Plan de pruebas
> **Instrucción:** Alcance de pruebas, herramientas utilizadas (Jest, JUnit, Postman, etc.) y criterios de aceptación.

[Plan de pruebas]

### 6.2 Casos de prueba — Caja negra
> **Instrucción:** Diseñados mediante partición de equivalencia y análisis de valores límite (BVA).

| ID | Entrada | Resultado esperado | Resultado obtenido | Estado (Pasó / Falló) |
| :--- | :--- | :--- | :--- | :--- |
| **CN01** | [completar] | [completar] | [completar] | [Pasó / Falló] |
| **CN02** | [completar] | [completar] | [completar] | [Pasó / Falló] |
| **CN03** | [completar] | [completar] | [completar] | [Pasó / Falló] |

### 6.3 Casos de prueba — Caja blanca
> **Instrucción:** Detalle el método bajo prueba, flujo de control evaluado y métricas de cobertura (sentencias, ramas o caminos).

```text
[Fragmento de código analizado / Caso de prueba unitario]
```
[Evidencia de cobertura de caminos/decisión]

### 6.4 Resultados y defectos encontrados
> **Instrucción:** Métrica resumen (ejecutados, aprobados, fallidos), catálogo de bugs detectados y estado de remediación.

[Resumen métrico de pruebas y registro de defectos]

---

## 7. Conclusiones y recomendaciones
> **Instrucción:** Cada conclusión debe mapearse directamente a un objetivo específico planteado en 2.3. Enfocarse en resultados empíricos y no en intenciones.

- **Conclusiones:**
  - [Conclusión 1 - asociada a Objetivo Específico 1]
  - [Conclusión 2 - asociada a Objetivo Específico 2]
  - [Conclusión 3 - asociada a Objetivo Específico 3]
- **Recomendaciones:**
  - [Recomendación técnica/metodológica 1]
  - [Recomendación técnica/metodológica 2]

---

## 8. Lecciones aprendidas (Retrospectiva ágil)
> **Instrucción:** Retrospectiva basada en el proceso AUP/Ágil seguido por el equipo.

- **¿Qué funcionó bien?:** [completar]
- **¿Qué haríamos de forma diferente?:** [completar]
- **Ajustes al proceso de trabajo del equipo:** [completar]

---

## 9. Referencias bibliográficas
> **Instrucción:** Formato APA (7.ª edición). Debe incluir obligatoriamente los estándares citados (OMG UML 2.5.1, BPMN 2.0, ISO 25010, ISO 42010, Larman, Kruchten, etc.).

[Referencias bibliográficas en formato APA 7]

---

## Anexos
> **Instrucción:** Fragmentos de código complementarios, scripts de migración de BD, actas de reuniones o evidencias de despliegue.

[Anexos]