# UNIVERSIDAD NACIONAL MAYOR DE SAN MARCOS
## FACULTAD DE INGENIERÍA DE SISTEMAS E INFORMÁTICA
### ESCUELA PROFESIONAL DE INGENIERÍA DE SISTEMAS
**Asignatura:** Diseño de Sistemas de Información (Plan 2018, Semestre 2026-2)  
**Marco Metodológico:** Agile Unified Process (AUP)  
**Proyecto:** Sistema de Mesa de Ayuda de TI (Service Desk) - Caso B  
**Documento base:** Fase de Elaboración / Diseño Lógico y Arquitectura (Consolidado PE1)

---

## 3. Análisis (Continuación)

### 3.3 Modelo de Casos de Uso

El modelo funcional formaliza las interacciones entre los actores del sistema y los servicios provistos, estructurado bajo el estándar OMG UML 2.5.1 mediante relaciones estereotipadas `<<include>>` y `<<extend>>`.

#### Catálogo General de Casos de Uso
- **CU-01:** Registrar Incidencia de TI.
- **CU-02 (Crítico / Vertical Slice):** Escalar Incidencia por Vencimiento de SLA.
- **CU-03:** Calcular Fecha Límite de SLA (`<<include>>` de CU-01).
- **CU-04:** Monitorear Vencimiento de Tickets (Iniciado por el actor sistema *Motor de Monitoreo SLA*).
- **CU-05:** Emitir Notificación de Alerta de SLA (`<<include>>` de CU-02).
- **CU-06:** Atender y Resolver Incidencia.
- **CU-07:** Consultar Estado y Trazabilidad.

*(Diagrama de Casos de Uso modelado en: `casos_de_uso.puml`)*

---

#### Especificación del Caso de Uso Crítico (Vertical Slice)

| Campo | Detalle Técnico |
| :--- | :--- |
| **Identificador** | **CU-02** |
| **Nombre** | Registrar Incidencia y Escalarla Automáticamente al Vencer SLA |
| **Actores Primarios** | Agente N1 (Mesa de Ayuda) / Servidor Público Solicitante |
| **Actores Secundarios** | Motor de Monitoreo de SLA (Sistema), Observadores de Alerta, Agente N2 / N3 |
| **Propósito** | Capturar una falla de TI, calcular su SLA contractual e iniciar atención; si el tiempo límite expira sin resolución, alertar a los observadores y transferir el ticket al siguiente escalafón de soporte de manera desatendida. |
| **Precondiciones** | 1. El servicio REST de Service Desk se encuentra activo y disponible.<br>2. Existen canales observadores de notificación registrados en el sistema.<br>3. La cadena de escalamiento jerárquica (N1 $\rightarrow$ N2 $\rightarrow$ N3) está configurada. |
| **Postcondiciones** | 1. La incidencia queda persistida con identificador único y marca temporal `dueAt` calculada.<br>2. Al verificarse el vencimiento, el estado transiciona a `ESCALADO`.<br>3. Se emiten eventos desacoplados hacia los observadores (log y correo).<br>4. El nivel asignado se incrementa a `NIVEL_2` (o `NIVEL_3`), registrando el motivo en su historial inmutable. |

##### Flujo Básico (Camino Feliz - Vencimiento y Escalamiento)
1. El Agente N1 o Solicitante envía una petición de registro (`POST /tickets`) suministrando: título, descripción, categoría (`Hardware`, `Software`, `Redes`, `Accesos`), impacto y urgencia.
2. El sistema valida la integridad de los datos de entrada según las reglas de formato.
3. El sistema resuelve la prioridad ITIL (`P1`, `P2`, `P3`, `P4`) mediante la matriz de cruce `Impacto x Urgencia`.
4. El sistema invoca la factoría especializada (`TicketFactory`) correspondiente a la categoría del ticket.
5. La factoría calcula la fecha y hora exacta de vencimiento contractual:
   $$\text{dueAt} = \text{fechaRegistro} + \text{SLA\_Hours}(\text{categoría}, \text{prioridad})$$
6. El sistema inicializa el ticket en estado `ABIERTO`, nivel `NIVEL_1` y lo persiste en el repositorio.
7. El sistema retorna el ticket creado con código HTTP `201 Created` y su carga útil estructurada.
8. El proceso concurrente de monitoreo evalúa periódicamente el estado temporal de los tickets activos.
9. El sistema ejecuta `isSlaBreached(currentTime)` sobre el ticket y determina que `currentTime > dueAt`.
10. El sistema instancia un evento `SlaBreachEvent` y lo publica a través de `SlaBreachNotifier` (Sujeto del patrón *Observer*).
11. Los observadores suscritos (`ConsoleLogObserver`, `EmailAlertObserver`) reciben el evento y despachan las alertas correspondientes de forma desacoplada.
12. El sistema somete el ticket a la cadena de escalamiento (`SupportLevelHandler`).
13. El manejador actual (`L1SupportHandler`) evalúa que la incidencia no fue resuelta dentro del SLA y delega la responsabilidad a `L2SupportHandler`.
14. El manejador `L2SupportHandler` acepta la delegación, actualiza el estado a `ESCALADO`, asigna `currentLevel = NIVEL_2` e inserta un nuevo `EscalationRecord` con motivo `"SLA_BREACH_TIMEOUT"`.
15. El sistema persiste la actualización del ticket en el repositorio.
16. El caso de uso finaliza exitosamente garantizando la continuidad de atención.

##### Flujos Alternos
- **A1: Resolución exitosa en Nivel 1 antes del vencimiento del SLA:**
  - En el paso 8, antes de que el reloj del sistema alcance `dueAt`, el Agente N1 ejecuta la solución y solicita el cierre (`PATCH /tickets/:id/resolve`).
  - El sistema actualiza el estado a `RESUELTO`.
  - El motor de monitoreo omite el ticket en evaluaciones posteriores. El caso de uso finaliza.
- **A2: Escalamiento por complejidad técnica declarada (sin vencimiento previo):**
  - En el paso 8, el analista de N1 diagnostica que la incidencia requiere reemplazo físico de componentes de red troncal.
  - El analista solicita escalamiento manual justificado.
  - La cadena de responsabilidad evalúa la regla de competencia y eleva el ticket a N2 o N3 de forma inmediata, manteniendo el SLA original.

##### Flujos de Excepción
- **E1: Payload de creación inválido:**
  - En el paso 2, si falta algún atributo obligatorio o la categoría es desconocida, el sistema rechaza la solicitud retornando HTTP `400 Bad Request` con el detalle del error. El ticket no se persiste.
- **E2: Falla en canal de notificación de observador:**
  - En el paso 11, si el canal de correo presenta timeout o error de red, el observador captura la excepción internamente y registra la traza en el log sin interrumpir ni revertir la transacción de escalamiento del ticket.

---

### 3.4 Modelo de Dominio

El Modelo Conceptual de Dominio representa las abstracciones del negocio y sus interrelaciones estructurales conforme a las directrices de Larman (2004), sin incluir métodos ni detalles de implementación tecnológica.

#### Diccionario de Clases Conceptuales
- **`Usuario` (Solicitante):** Servidor público que reporta una interrupción o degradación en sus servicios tecnológicos ante la Mesa de Ayuda.
- **`Incidencia` (Ticket):** Entidad central que encapsula el requerimiento correctivo. Mantiene su ciclo de vida (`ABIERTO`, `EN_PROCESO`, `ESCALADO`, `RESUELTO`, `CERRADO`), su categoría técnica (`HARDWARE`, `SOFTWARE`, `REDES`, `ACCESOS`) y su historial cronológico.
- **`Prioridad`:** Entidad resultante del cruce bidimensional ITIL v4 entre **Impacto** y **Urgencia**.
- **`SLA` (Acuerdo de Nivel de Servicio):** Parámetros contractuales que definen la fecha límite de resolución (`dueAt`), tiempo límite de primer contacto y umbrales de alerta temprana.
- **`NivelSoporte`:** Escalafón organizativo de atención (`NIVEL_1`, `NIVEL_2`, `NIVEL_3`) con competencias asignadas.
- **`RegistroEscalamiento`:** Instancia inmutable que documenta la transferencia entre niveles (marca de tiempo, motivo y actor/mecanismo que autoriza).
- **`AlertaSLA`:** Evento de notificación emitido ante la proximidad o infracción del tiempo contractual de resolución.

*(Diagrama de Modelo de Dominio conceptual disponible en: `modelo_dominio.puml`)*

---

## 4. Diseño (Fase de Elaboración)

### 4.1 Arquitectura de software - Vista 4+1

En observancia del estándar ISO/IEC/IEEE 42010:2011 y el modelo de Kruchten (1995), la arquitectura se documenta a través de cuatro vistas fundamentales integradas por el escenario de uso crítico.

#### Vista Lógica (Logical View)
Descompone el sistema en capas cohesivas y paquetes desacoplados:
- **`Presentation`:** Controladores REST (`TicketController`), adaptadores HTTP y objetos de transferencia de datos (`CreateTicketDTO`).
- **`Application`:** Servicios de aplicación (`TicketService`) que coordinan casos de uso sin acoplamiento a infraestructura.
- **`Domain`:** Núcleo puro del negocio que encapsula entidades (`Ticket`), factorías polimórficas (`TicketFactory`), cadena de soporte (`SupportLevelHandler`), notificador de SLAs (`SlaBreachNotifier`) e interfaces de persistencia (`ITicketRepository`).
- **`Infrastructure`:** Adaptadores tecnológicos (`InMemoryTicketRepository`, `ConsoleLogObserver`, `EmailAlertObserver`, `SlaSchedulerWorker`).

#### Vista de Procesos (Process View)
Aborda la concurrencia, sincronización de flujos y tiempo de ejecución:
- **Event Loop Principal (Node.js):** Atiende peticiones HTTP de forma síncrona/no bloqueante, asegurando tiempos de respuesta menores a 20 ms para la creación de tickets.
- **Worker Concurrente (`SlaSchedulerWorker`):** Temporizador desacoplado que evalúa en segundo plano los tickets activos y despacha eventos ante infracciones de SLA sin bloquear las peticiones REST.
- **Despacho Concurrente a Observadores:** Notificación paralela a los observadores registrados, aislando cualquier latencia de I/O de la cadena de escalamiento.

*(Diagrama de Vista de Procesos disponible en: `vista_procesos.puml`)*

#### Vista de Desarrollo (Development View)
Define la estructura modular del código bajo principios de Arquitectura Limpia:
- **Regla de Dependencia:** La capa de Dominio no posee dependencias externas. Aplicación depende únicamente de Dominio. Presentación e Infraestructura dependen de las capas internas mediante Inversión de Dependencias (DIP).
- **Organización de Directorios:**
  - `src/presentation/`: Controladores y definición de servidor HTTP.
  - `src/application/`: Servicios coordinadores y DTOs de salida.
  - `src/domain/`: Entidades, factorías GoF, cadena GoF, observadores GoF y contratos de repositorio.
  - `src/infrastructure/`: Repositorios concretos, observadores de notificación y workers.
  - `tests/`: Pruebas de caja negra y caja blanca.

*(Diagrama de Vista de Desarrollo disponible en: `vista_desarrollo.puml`)*

#### Vista Física / Despliegue (Physical View)
Modela la topología de ejecución del prototipo:
- **Estación Cliente:** Navegador / Postman / Consola emitiendo peticiones HTTP/JSON hacia la API.
- **Servidor de Aplicación (Host / Contenedor):** Proceso Node.js v24 LTS en el puerto `TCP 3000` ejecutando la API REST y el worker temporal de SLAs en el mismo espacio de memoria seguro.
- **Subsistema de Persistencia:** Acceso desacoplado a través de `ITicketRepository`, resuelto en memoria concurrente (`InMemoryTicketRepository`) para garantizar mínima latencia en el prototipo vertical.

*(Diagrama de Vista de Despliegue disponible en: `vista_despliegue.puml`)*

#### Escenarios (+1)
El escenario central que articula y conecta las cuatro vistas es el **CU-02 (Escalamiento automático por vencimiento de SLA)**:
1. Una petición HTTP entra por la **Vista Física**, es recibida por el Event Loop de la **Vista de Procesos** y canalizada por los controladores de la **Vista de Desarrollo**.
2. Las clases del dominio en la **Vista Lógica** calculan el SLA y persisten el ticket.
3. El worker en segundo plano (**Vista de Procesos**) detecta la expiración temporal e interactúa con el patrón *Observer* y la *Chain of Responsibility* (**Vista Lógica**), propagando el cambio de estado a través de los módulos definidos (**Vista de Desarrollo**).

---

#### Justificación de Decisiones Arquitectónicas y Tácticas (SEI / ADD)
Conforme a la metodología de Bass, Clements y Kazman (SEI):
- **Tácticas para Rendimiento (RNF-01: $P_{95} \le 200\text{ ms}$):**
  - *Cálculo estático anticipado:* La factoría calcula la fecha absoluta `dueAt` al crear el ticket, permitiendo verificaciones de vencimiento en $\mathcal{O}(1)$ (`now > dueAt`).
  - *Persistencia indexada en memoria:* Uso de `Map<string, Ticket>` con costo de acceso y actualización en $\mathcal{O}(1)$.
- **Tácticas para Disponibilidad (RNF-02: Uptime $\ge 99.5\%$):**
  - *Aislamiento de excepciones en observadores:* Cada observador captura fallos externos (ej. caída del servidor SMTP) evitando que se interrumpa la transacción de escalamiento.
  - *Degradación elegante:* El fallo de un canal de notificación no detiene la transferencia de soporte en la cadena de responsabilidad.
- **Tácticas para Mantenibilidad y Testeabilidad (RNF-03 / RNF-04):**
  - *Encapsulamiento de variaciones:* Nuevas categorías de tickets o niveles de soporte se agregan extendiendo clases sin modificar el código cliente (principio Abierto/Cerrado).
  - *Inyección de dependencias:* Los repositorios y manejadores se inyectan en el constructor de los servicios, facilitando el aislamiento para pruebas unitarias.

---

### 4.2 Diagrama de clases de diseño

A diferencia del modelo conceptual de dominio, el Diagrama de Clases de Diseño formaliza la implementación orientada a objetos en UML 2.5.1, detallando métodos, tipos de datos, visibilidad y distinguiendo agregaciones de composiciones.

#### Estructura y Patrones Implementados
1. **Jerarquía Polimórfica (Factory Method):**
   - Clase abstracta `Ticket` con atributos privados (`id`, `title`, `description`, `status`, `priority`, `currentLevel`, `dueAt`, `createdAt`).
   - Especializaciones: `HardwareTicket`, `SoftwareTicket`, `NetworkTicket`, `AccessTicket`.
   - Creadores: `TicketFactory` como creador base y factorías concretas por categoría que encapsulan el cálculo del SLA.
2. **Cadena de Escalamiento (Chain of Responsibility):**
   - Clase base abstracta `SupportLevelHandler` con referencia `nextHandler: SupportLevelHandler` y método `handle(ticket: Ticket): EscalationResult`.
   - Eslabones concretos: `L1SupportHandler`, `L2SupportHandler` y `L3SupportHandler`.
3. **Mecanismo de Alertas (Observer):**
   - Interfaz `ISlaSubject` implementada por `SlaBreachNotifier`.
   - Interfaz `ISlaObserver` con método `update(event: SlaBreachEvent): void`, implementada por `ConsoleLogObserver` y `EmailAlertObserver`.
4. **Capa de Persistencia:**
   - Interfaz `ITicketRepository` (`save`, `findById`, `findAll`, `findActiveTickets`).
   - Implementación concreta `InMemoryTicketRepository`.

*(Diagrama de Clases de Diseño detallado disponible en: `clases_diseno.puml`)*