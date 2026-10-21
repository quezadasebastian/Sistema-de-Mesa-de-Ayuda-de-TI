# UNIVERSIDAD NACIONAL MAYOR DE SAN MARCOS

## FACULTAD DE INGENIERÍA DE SISTEMAS E INFORMÁTICA

### ESCUELA PROFESIONAL DE INGENIERÍA DE SISTEMAS

**Asignatura:** Diseño de Sistemas de Información (Plan 2018, Semestre 2026-2)  
**Marco Metodológico:** Agile Unified Process (AUP)  
**Proyecto:** Sistema de Mesa de Ayuda de TI (Service Desk) - Caso B  
**Documento base:** Fase de Inicio / Informe de la Organización (Consolidado para Plantilla Final)

---

## 2. Introducción

### 2.1 Contexto organizacional

El presente proyecto se desarrolla en el marco de la **Oficina General de Tecnologías de la Información (OGTI)** de una entidad pública del sector gubernamental peruano (Ministerio / Organismo Público Descentralizado). La OGTI tiene a su cargo la gobernanza digital, la administración de la infraestructura tecnológica, las redes de comunicaciones, las bases de datos y la provisión de soporte técnico a más de 1,200 usuarios internos (servidores públicos) distribuidos en sedes centrales y desconcentradas.

Operativamente, la OGTI estructura la atención de incidencias en tres niveles técnicos:

- **Unidad de Mesa de Ayuda (Soporte Nivel 1 - N1):** Punto único de contacto (SPOC) encargado del triaje, recepción, registro inicial, diagnóstico preliminar y resolución de incidencias de baja complejidad (restablecimiento de contraseñas de correo institucional, soporte ofimático y configuración inicial de periféricos).
- **Unidad de Infraestructura y Soporte de Campo (Soporte Nivel 2 - N2):** Equipo técnico especializado en mantenimiento correctivo y configuración _in situ_ de hardware, sistemas operativos, servidores de impresión, conectividad cableada/WiFi y software de gestión documental.
- **Unidad de Arquitectura de Sistemas y Seguridad Digital (Soporte Especializado Nivel 3 - N3):** Especialistas en bases de datos (DBA), ingenieros de redes/seguridad perimetral, desarrolladores de software y enlace con proveedores externos para incidencias de alta criticidad, bugs del core del negocio y caídas de infraestructura central.

### 2.2 Problema y justificación

Actualmente, la OGTI opera bajo un modelo de atención artesanal sustentado en hojas de cálculo compartidas, correos electrónicos y mensajería instantánea. Dicho modelo genera severas deficiencias operativas analizadas bajo el marco de **ITIL v4 (Práctica de Gestión de Incidentes y Gestión de Niveles de Servicio)**:

1. **Ausencia de Cálculo Formal y Monitoreo de SLAs:** No existe un mecanismo sistematizado que determine y controle la fecha/hora límite contractual de atención según la criticidad del incidente. Se pierde visibilidad del vencimiento de plazos.
2. **Escalamiento Discrecional y Cuellos de Botella:** El paso de un incidente de Nivel 1 a Nivel 2 o Nivel 3 se ejecuta manualmente a criterio personal del analista de turno, generando acumulación de tickets sin resolver en N1 o sobrecarga innecesaria en N3 por mala categorización.
3. **Pérdida de Trazabilidad e Incumplimiento Normativo:** Los incidentes críticos (ej. fallo en el Sistema Integrado de Gestión Administrativa o caída del enlace principal) exceden los tiempos máximos permitidos por los Acuerdos de Nivel de Servicio (SLA) sin que se emita ninguna alerta temprana a la jefatura de la OGTI.
4. **Falta de Desacoplamiento entre Tipos de Incidencia y Reglas de Negocio:** Cada categoría de incidente (`Hardware`, `Software`, `Redes`, `Accesos`) requiere parámetros de SLA, prioridades base y protocolos de atención diferenciados; sin embargo, hoy se procesan con una plantilla genérica sin polimorfismo.

#### Matriz de Criticidad ITIL v4: Impacto, Urgencia y Prioridad

En concordancia con ITIL v4, la prioridad de una incidencia resulta de la intersección entre el **Impacto** (magnitud del perjuicio sobre la continuidad de los servicios o volumen de usuarios afectados) y la **Urgencia** (rapidez requerida para resolver el incidente antes de causar daño mayor).

| Impacto \ Urgencia                                             |     Alta (A)     |   Media (M)    |    Baja (B)    |
| :------------------------------------------------------------- | :--------------: | :------------: | :------------: |
| **Alto (A)** (Afecta servicios críticos o > 50 usuarios)       | **P1 - Crítica** | **P2 - Alta**  | **P3 - Media** |
| **Medio (M)** (Afecta una oficina completa o 10 a 50 usuarios) |  **P2 - Alta**   | **P3 - Media** | **P4 - Baja**  |
| **Bajo (B)** (Afecta a un solo usuario o función no esencial)  |  **P3 - Media**  | **P4 - Baja**  | **P4 - Baja**  |

#### Matriz de Acuerdos de Nivel de Servicio (SLA) y Política de Escalamiento Jerárquico

Los SLAs formalizan los tiempos máximos tolerables para el **Tiempo de Respuesta (SLA-R)** y el **Tiempo de Resolución (SLA-T)**.

|    Prioridad     | Nivel Inicial Asignado | SLA Tiempo de Respuesta (SLA-R) | SLA Tiempo de Resolución (SLA-T) | Umbral de Alerta Temprana (Observer)  |                                Regla de Escalamiento Automático (Chain of Responsibility)                                |
| :--------------: | :--------------------: | :-----------------------------: | :------------------------------: | :-----------------------------------: | :----------------------------------------------------------------------------------------------------------------------: |
| **P1 (Crítica)** |   Nivel 1 / Nivel 2    |          $\le$ 15 min           |            $\le$ 2 h             | Al consumir el 75% del SLA-T (1.5 h)  |              Si expira SLA-T o no hay resolución en N1/N2 $\rightarrow$ Escalamiento forzoso a **Nivel 3**               |
|  **P2 (Alta)**   |        Nivel 1         |          $\le$ 30 min           |            $\le$ 4 h             | Al consumir el 75% del SLA-T (3.0 h)  | Si expira SLA-T en N1 $\rightarrow$ Escalamiento a **Nivel 2**; si expira en N2 $\rightarrow$ Escalamiento a **Nivel 3** |
|  **P3 (Media)**  |        Nivel 1         |            $\le$ 1 h            |            $\le$ 8 h             | Al consumir el 80% del SLA-T (6.4 h)  |                              Si expira SLA-T en N1 $\rightarrow$ Escalamiento a **Nivel 2**                              |
|  **P4 (Baja)**   |        Nivel 1         |            $\le$ 2 h            |            $\le$ 24 h            | Al consumir el 85% del SLA-T (20.4 h) |                              Si expira SLA-T en N1 $\rightarrow$ Escalamiento a **Nivel 2**                              |

---

### 2.3 Objetivos

#### Objetivo General

Diseñar e implementar un prototipo vertical de un Sistema de Mesa de Ayuda de TI (Service Desk) bajo las buenas prácticas de ITIL v4, que automatice la recepción, cálculo y monitoreo de SLAs, así como el escalamiento jerárquico de incidentes mediante patrones de diseño de software.

#### Objetivos Específicos

1. **Analizar** los procesos operativos de soporte de la OGTI y formalizar los requisitos funcionales y no funcionales bajo el estándar internacional ISO/IEC 25010:2011.
2. **Diseñar** la arquitectura lógica del sistema mediante el modelo de vistas 4+1 (Kruchten) y artefactos UML 2.5.1, aplicando los patrones GoF _Factory Method_, _Chain of Responsibility_ y _Observer_.
3. **Implementar** un prototipo vertical (_vertical slice_) en Node.js/TypeScript que cubra de punta a punta el caso de uso crítico de registro y escalamiento automatizado de incidencias por vencimiento de SLA.
4. **Probar** la solución mediante pruebas unitarias de caja blanca (cobertura de ramas y sentencias) y pruebas funcionales de caja negra (particiones de equivalencia y análisis de valores límite temporales).

---

### 2.4 Alcance, supuestos y restricciones

#### 2.4.1 Alcance Incluido (In-Scope - Vertical Slice)

El proyecto se enfoca en un prototipo vertical de alta fidelidad que garantice trazabilidad punta a punta:

- **Flujo E2E Crítico:** Implementación integral del CU _"Registrar incidencia y escalarla automáticamente al vencer SLA"_.
- **Pila Arquitectónica Completa:**
  - _Capa de Presentación / API REST:_ Endpoints para creación de incidencias, verificación de vencimiento y consulta de estado.
  - _Capa de Aplicación / Servicios:_ Coordinación de casos de uso y orquestación de patrones de software.
  - _Capa de Dominio:_ Entidades ricas, reglas ITIL v4 y tres patrones GoF obligatorios (_Factory Method_, _Chain of Responsibility_, _Observer_).
  - _Capa de Persistencia:_ Abstracción desacoplada mediante patrón Repository e implementación en memoria/persistencia ligera.
- **Suite de Pruebas:**
  - Pruebas de Caja Negra: Particiones de equivalencia y valores límite sobre las fronteras temporales del SLA ($T - 1$ s, $T$, $T + 1$ s).
  - Pruebas de Caja Blanca: Cobertura exhaustiva de caminos independientes y bifurcaciones en los manejadores de soporte (N1, N2 y N3).

#### 2.4.2 Alcance Excluido (Out-of-Scope)

- **Autenticación Compleja y Roles Distribuidos:** No se implementará SSO, OAuth2/OIDC ni federación con Active Directory institucional (la identidad del operador/usuario se inyecta por cabeceras de contexto).
- **Módulos de Reportería y BI Extensos:** No se construirán dashboards analíticos ni motores de exportación documental (PDF/Excel).
- **Interfaces Web Cosméticas:** Se prescinde de frontends de múltiples vistas; la interacción se valida mediante API REST, OpenAPI y clientes HTTP automatizados.
- **Gestión de Problemas y Cambios ITIL:** El sistema cubre exclusivamente la Gestión de Incidentes; no incluye Change Management ni Problem Management.

#### 2.4.3 Restricciones Técnicas y Metodológicas

- **Restricción R-01 (Trazabilidad 1:1):** Cada clase, método e interfaz implementada en código debe corresponderse estrictamente con el Diagrama de Clases de Diseño (PE1) y el Diagrama de Secuencia (PE2).
- **Restricción R-02 (Estándar Notacional):** Todo artefacto visual debe adherirse a OMG UML 2.5.1 y OMG BPMN 2.0 en código PlantUML compilable.
- **Restricción R-03 (Microservicio Autónomo):** La solución debe ser autosuficiente y ejecutable en entorno local con dependencias declaradas en `package.json`, sin requerir servicios privativos de nube.

#### 2.4.4 Supuestos Operativos

- **Supuesto S-01:** La jornada laboral para el cómputo de SLAs se modela inicialmente en tiempo continuo o lineal parametrizable dentro de la factoría de tickets.
- **Supuesto S-02:** Los niveles de soporte (N1, N2, N3) cuentan con agentes activos en el pool de atención al momento en que el motor transfiere la incidencia.

---

## 3. Análisis (Fase de Inicio)

### 3.1 Requisitos funcionales y no funcionales

#### Requisitos Funcionales (RF)

Clasificados con prioridad MoSCoW (_Must have_, _Should have_, _Could have_, _Won't have_) conforme al alcance del prototipo.

| Código RF | Descripción                                                                                                                                                                                                                  | Característica ISO/IEC 25010 |   Prioridad (MoSCoW)   |    Caso de Uso Vinculado    |
| :-------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------- | :--------------------: | :-------------------------: |
| **RF-01** | El sistema debe permitir registrar una incidencia capturando título, descripción, categoría (`Hardware`, `Software`, `Redes`, `Accesos`), impacto y urgencia, generando un identificador correlativo único.                  | Adecuación Funcional         | **Must (Obligatorio)** | CU-01: Registrar Incidencia |
| **RF-02** | El sistema debe inicializar polimórficamente la entidad de ticket según su categoría, calculando la prioridad ITIL y la marca temporal exacta de vencimiento del SLA (`dueAt`).                                              | Adecuación Funcional         | **Must (Obligatorio)** | CU-01: Registrar Incidencia |
| **RF-03** | El sistema debe proveer un mecanismo de verificación temporal que evalúe periódicamente o bajo demanda si un ticket ha sobrepasado su fecha límite de resolución (`dueAt`).                                                  | Adecuación Funcional         | **Must (Obligatorio)** |  CU-02: Escalar Incidencia  |
| **RF-04** | Al detectar la expiración de un SLA, el sistema debe disparar un evento de alerta hacia los observadores suscritos (notificador en consola/log y canal de eventos).                                                          | Adecuación Funcional         | **Must (Obligatorio)** |  CU-02: Escalar Incidencia  |
| **RF-05** | El sistema debe delegar el ticket vencido o de alta complejidad a través de una cadena secuencial de manejadores (N1 $\rightarrow$ N2 $\rightarrow$ N3), actualizando su estado a `ESCALADO` y reasignando el nivel técnico. | Adecuación Funcional         | **Must (Obligatorio)** |  CU-02: Escalar Incidencia  |
| **RF-06** | El sistema debe permitir consultar un ticket por su ID, exponiendo el nivel de soporte asignado, historial de escalamiento, marcas temporales de SLA y estado actual.                                                        | Adecuación Funcional         | **Should (Deseable)**  |   CU-03: Consultar Ticket   |

#### Requisitos No Funcionales (RNF) estructurados bajo ISO/IEC 25010:2011

| Código RNF | Característica / Subcaracterística ISO/IEC 25010   | Descripción Técnica y Restricción                                                                                                                                | Métrica / Criterio de Aceptación Cuantitativo                                                                                       | Método de Verificación                                                      |
| :--------- | :------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------- |
| **RNF-01** | **Rendimiento** / Comportamiento temporal          | El tiempo de respuesta de la API REST para el endpoint de registro (`POST /tickets`) debe ser óptimo bajo condiciones normales de carga de la red institucional. | Tiempo de respuesta en percentil 95 ($P_{95}$) $\le$ 200 ms con carga concurrente de 50 peticiones/seg.                             | Prueba de carga con herramienta automatizada (k6 / Artillery / autocannon). |
| **RNF-02** | **Fiabilidad** / Disponibilidad                    | El servicio central de gestión y monitoreo de tickets debe operar continuamente durante la jornada laboral de la entidad (08:00 a 18:00 hrs).                    | Disponibilidad mensual $\ge$ 99.5% en horario laboral. Tiempo medio de recuperación ($MTTR$) $\le$ 15 min ante fallos no fatales.   | Monitoreo de uptime y logs de disponibilidad de servicio.                   |
| **RNF-03** | **Mantenibilidad** / Modularidad                   | El código debe estructurarse en capas limpias y aplicar patrones GoF para incorporar nuevas categorías o manejadores sin modificar el código cliente (OCP).      | Cumplimiento del principio Abierto/Cerrado. Ausencia de referencias circulares en el grafo de módulos.                              | Análisis estático de dependencias y revisión de código.                     |
| **RNF-04** | **Mantenibilidad** / Testeabilidad (_Testability_) | La lógica de negocio nuclear (cálculo de SLA, factoría y cadena de escalamiento) debe validarse con pruebas unitarias automatizadas.                             | Cobertura de ramas (_branch coverage_) $\ge$ 90% en la cadena de escalamiento y factoría. Cobertura de líneas $\ge$ 85% en dominio. | Reporte automatizado de cobertura con test runner (Vitest / Jest).          |
| **RNF-05** | **Seguridad** / Integridad                         | El sistema debe validar estrictamente los payloads de entrada en los endpoints REST, rechazando esquemas malformados o categorías no soportadas.                 | 100% de peticiones con datos inválidos deben responder con código HTTP 400 Bad Request estructurado, sin fugar trazas internas.     | Pruebas de caja negra con valores límite y particiones inválidas.           |

---

### 3.2 Modelo de procesos de negocio (BPMN 2.0)

El proceso modelado corresponde a la **Gestión y Escalamiento de Incidencias de TI**, formalizando la interacción entre el Usuario Solicitante, la Mesa de Ayuda (Nivel 1), el Soporte de Campo (Nivel 2), el Soporte Especializado (Nivel 3) y el Motor de Monitoreo Automatizado de SLAs.

#### Descripción del Flujo Operativo BPMN 2.0

1. **Inicio:** Un usuario de la entidad pública reporta una anomalía tecnológica a través del canal unificado.
2. **Recepción y Triaje (N1):** El operador N1 ingresa la incidencia. El sistema clasifica el tipo (`Hardware`, `Software`, `Redes`, `Accesos`) y calcula de forma automática la fecha/hora de vencimiento del SLA (`dueAt`).
3. **Diagnóstico Preliminar:** Si la incidencia corresponde al primer nivel y puede resolverse en el acto (ej. reseteo de contraseñas), N1 ejecuta la solución y cierra el ticket.
4. **Monitoreo Concurrente de SLA:** En paralelo, el motor de monitoreo evalúa periódicamente el tiempo remanente. Si se detecta un vencimiento o infracción de umbral crítico, se genera un evento de alerta emitido a los observadores suscritos (notificación / log).
5. **Cadena de Escalamiento:** Si el SLA expira o la complejidad técnica supera las competencias de N1, el ticket se transfiere automáticamente a N2. Si N2 no lo resuelve dentro del margen o requiere intervención estructural, se transfiere a N3.
6. **Resolución y Cierre:** El especialista asignado registra la solución técnica y confirma el restablecimiento del servicio al usuario.

#### Diagrama BPMN 2.0 (Código PlantUML Embebido)

_(El diagrama BPMN compilable en PlantUML se ubica formalmente en `docs/diagrams/bpmn_gestion_incidencias.puml` y se renderiza en la Sección de Entregables)._
