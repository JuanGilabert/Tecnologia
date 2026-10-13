# Arquitectura dirigida por eventos (Event-Driven Architecture)

## 1. Introducción

La **arquitectura dirigida por eventos** (*Event-Driven Architecture*, EDA) es un estilo arquitectónico en el que los diferentes componentes de un sistema se comunican mediante la producción, publicación, detección y consumo de eventos.

Un **evento** representa un hecho significativo que ha ocurrido en el sistema, como la creación de un pedido, la confirmación de un pago, la actualización del inventario o el registro de un usuario.

En lugar de depender exclusivamente de llamadas directas entre componentes, los productores publican eventos que pueden ser procesados por uno o varios consumidores. De esta forma, los componentes pueden interactuar de manera asíncrona y mantener un acoplamiento reducido.

Este enfoque resulta especialmente útil en sistemas que necesitan procesar grandes volúmenes de información, reaccionar a cambios en tiempo real o integrar múltiples servicios y aplicaciones.

**Fuentes:**
- [Microsoft Azure: Estilo de arquitectura basada en eventos](https://learn.microsoft.com/es-es/azure/architecture/guide/architecture-styles/event-driven)
- [AWS: ¿Qué es una arquitectura dirigida por eventos?](https://aws.amazon.com/event-driven-architecture/)
- [Confluent: Event-Driven Architecture](https://www.confluent.io/learn/event-driven-architecture/)

## 2. Características principales

### 2.1. Comunicación basada en eventos

Los componentes se comunican mediante eventos que describen hechos que ya han ocurrido.

Por ejemplo, cuando un cliente realiza una compra, el sistema puede publicar un evento llamado `PedidoCreado`. Otros componentes pueden reaccionar a ese evento para reservar productos, iniciar el proceso de pago o enviar una notificación.

El productor no necesita conocer necesariamente qué componentes consumirán el evento ni cómo lo procesarán.

### 2.2. Desacoplamiento entre productores y consumidores

Los productores generan eventos sin depender directamente de la implementación de los consumidores.

Esta separación permite incorporar nuevos consumidores sin tener que modificar necesariamente el componente que produce los eventos, siempre que se mantenga el contrato del evento y la infraestructura de comunicación.

Por ejemplo, un evento de pago confirmado podría ser utilizado por los módulos de facturación, notificaciones y análisis de datos.

### 2.3. Procesamiento asíncrono

Los eventos pueden procesarse de manera asíncrona, de modo que el productor no tenga que esperar a que todos los consumidores terminen su trabajo.

Esto puede mejorar la capacidad de respuesta de una aplicación y permitir que las tareas secundarias se ejecuten en segundo plano.

Sin embargo, el procesamiento asíncrono también introduce retos relacionados con la consistencia de los datos, el seguimiento de las operaciones y el tratamiento de errores.

### 2.4. Productores, consumidores y canales de eventos

Una arquitectura dirigida por eventos suele incluir los siguientes elementos:

- **Productores:** componentes que generan y publican eventos.
- **Consumidores:** componentes que reciben y procesan los eventos.
- **Canales o intermediarios de eventos:** mecanismos que transportan, almacenan o distribuyen los eventos.
- **Eventos:** mensajes que describen hechos ocurridos en el sistema.

Entre las tecnologías que pueden utilizarse se encuentran Apache Kafka, RabbitMQ, Amazon EventBridge y Azure Event Grid. Cada una ofrece capacidades diferentes, por lo que su elección depende de los requisitos del sistema.

### 2.5. Escalabilidad independiente

Los consumidores pueden escalarse de acuerdo con el volumen de eventos que necesitan procesar.

Por ejemplo, si aumenta el número de pedidos, se pueden incrementar los recursos destinados al procesamiento de pedidos sin necesidad de escalar todos los consumidores del sistema.

La escalabilidad efectiva dependerá de la tecnología utilizada, la estrategia de particionado, el orden de los eventos y la capacidad de procesamiento de cada consumidor.

### 2.6. Comunicación mediante eventos de dominio

En sistemas orientados al dominio, los **eventos de dominio** representan hechos relevantes para el negocio, como `PedidoConfirmado`, `PagoRechazado` o `ProductoReservado`.

Estos eventos permiten expresar los cambios del dominio de manera explícita y facilitar que otros componentes reaccionen a ellos.

Es importante distinguir los eventos de dominio de los mensajes puramente técnicos, como una señal de monitorización o una notificación interna de infraestructura.

**Fuentes:**
- [Microsoft Azure: Arquitectura basada en eventos](https://learn.microsoft.com/es-es/azure/architecture/guide/architecture-styles/event-driven)
- [AWS: Arquitectura dirigida por eventos](https://aws.amazon.com/event-driven-architecture/)
- [Confluent: Event-Driven Architecture](https://www.confluent.io/learn/event-driven-architecture/)

## 3. Modelos de comunicación

### 3.1. Modelo de publicación y suscripción (*Publish/Subscribe*)

En este modelo, un productor publica un evento en un canal o intermediario y varios consumidores interesados pueden recibirlo.

Por ejemplo, cuando se confirma un pedido, los sistemas de facturación, notificaciones y análisis pueden reaccionar al mismo evento de forma independiente.

Este modelo facilita la incorporación de nuevos consumidores y reduce las dependencias directas entre los componentes.

### 3.2. Modelo basado en colas (*Message Queue*)

En un modelo basado en colas, los mensajes se almacenan hasta que un consumidor puede procesarlos.

Es habitual utilizarlo para distribuir tareas entre trabajadores y gestionar cargas de trabajo asíncronas.

Por ejemplo, una aplicación puede enviar solicitudes de generación de informes a una cola, donde varios trabajadores las procesan a medida que disponen de recursos.

La semántica concreta depende de la tecnología: una cola de trabajo con consumidores competidores no funciona necesariamente igual que un sistema de publicación y suscripción con varios grupos de consumidores.

### 3.3. Modelo de transmisión de eventos (*Event Streaming*)

En este modelo, los eventos se registran en un flujo que puede consumirse de manera continua y, según la plataforma y su configuración, conservarse durante un periodo determinado.

Esto permite procesar información en tiempo real, reconstruir determinados estados y volver a consumir eventos históricos.

**Apache Kafka** es una tecnología habitual en este tipo de escenarios.

### 3.4. Procesamiento en tiempo real

La arquitectura dirigida por eventos permite reaccionar a los cambios a medida que se producen.

Entre sus aplicaciones se encuentran la detección de fraude, la monitorización de sistemas, el seguimiento logístico, el análisis de actividad y las actualizaciones de inventario.

No obstante, la velocidad de procesamiento dependerá de la infraestructura, la carga de trabajo y las garantías de entrega configuradas.

**Fuentes:**
- [Microsoft Azure: Arquitectura basada en eventos](https://learn.microsoft.com/es-es/azure/architecture/guide/architecture-styles/event-driven)
- [Apache Kafka: Documentación oficial](https://kafka.apache.org/documentation/)
- [RabbitMQ: Documentación oficial](https://www.rabbitmq.com/docs)

## 4. Ventajas

### 4.1. Bajo acoplamiento

Los productores no necesitan conocer directamente a todos los consumidores. Esto facilita modificar o ampliar el sistema sin introducir dependencias innecesarias entre sus componentes.

### 4.2. Escalabilidad

Los consumidores pueden aumentar o reducir su capacidad de procesamiento de forma independiente, de acuerdo con el volumen de eventos recibidos.

Además, los intermediarios de mensajería pueden ayudar a distribuir las cargas de trabajo y absorber aumentos temporales de actividad.

### 4.3. Mayor capacidad de respuesta

Las operaciones que no necesitan ejecutarse inmediatamente pueden procesarse en segundo plano.

Por ejemplo, una aplicación puede confirmar la recepción de un pedido y ejecutar posteriormente tareas como el envío de notificaciones o la actualización de sistemas analíticos.

### 4.4. Extensibilidad

Se pueden incorporar nuevos consumidores para añadir funcionalidades sin modificar necesariamente los productores existentes.

Un evento de pedido creado podría activar en el futuro un sistema de recomendaciones, un servicio de estadísticas o un mecanismo de fidelización.

### 4.5. Integración entre sistemas

Los eventos facilitan la comunicación entre aplicaciones, servicios y plataformas diferentes, incluso cuando utilizan tecnologías distintas.

Esto resulta especialmente útil en sistemas empresariales que necesitan integrar servicios internos con aplicaciones externas.

### 4.6. Tolerancia a interrupciones temporales

Cuando se utiliza un intermediario que almacena los mensajes de forma duradera, los consumidores pueden recuperar el trabajo pendiente después de una interrupción, siempre que se mantengan las garantías de persistencia y retención necesarias.

Esta capacidad no elimina los fallos, pero puede ayudar a evitar la pérdida de mensajes y mejorar la recuperación.

**Fuentes:**
- [AWS: Arquitectura dirigida por eventos](https://aws.amazon.com/event-driven-architecture/)
- [Microsoft Azure: Arquitectura basada en eventos](https://learn.microsoft.com/es-es/azure/architecture/guide/architecture-styles/event-driven)
- [Confluent: Event-Driven Architecture](https://www.confluent.io/learn/event-driven-architecture/)

## 5. Desafíos

### 5.1. Complejidad de los sistemas distribuidos

La comunicación asíncrona dificulta conocer de forma inmediata el estado global de una operación.

Un evento puede publicarse correctamente mientras alguno de sus consumidores permanece temporalmente desconectado o experimenta errores.

Por ello, es necesario diseñar mecanismos de seguimiento, gestión de errores y recuperación.

### 5.2. Consistencia eventual

Los distintos componentes pueden actualizar sus datos en momentos diferentes.

Por ejemplo, un pedido puede figurar como confirmado antes de que el sistema de análisis haya recibido el evento correspondiente.

Esta situación se conoce como **consistencia eventual** cuando los datos convergen finalmente si se cumplen las condiciones de procesamiento.

Es importante determinar qué operaciones necesitan consistencia inmediata y cuáles pueden admitir cierto retraso.

### 5.3. Duplicación de eventos e idempotencia

Dependiendo de la tecnología y de las garantías de entrega, un consumidor puede recibir un mismo evento más de una vez.

Para evitar que esto provoque operaciones duplicadas, los consumidores deben diseñarse, cuando sea necesario, de forma **idempotente**: procesar repetidamente el mismo evento no debe producir efectos adicionales no deseados.

También pueden utilizarse identificadores únicos, registros de procesamiento y mecanismos de deduplicación.

### 5.4. Orden de los eventos

En determinados procesos, el orden de los eventos es importante.

Por ejemplo, un evento de pedido cancelado no debería procesarse antes que el evento de creación correspondiente si eso provoca un estado incorrecto.

Sin embargo, garantizar el orden global puede resultar costoso o no estar disponible en todas las plataformas. Por ello, conviene definir si el orden debe mantenerse por entidad, partición, canal o flujo de negocio.

### 5.5. Gestión de errores y reintentos

Los consumidores pueden fallar al procesar un evento por problemas temporales, datos inválidos o errores en sistemas externos.

Es necesario definir políticas de reintentos, tiempos de espera, límites de procesamiento y mecanismos de aislamiento.

Los mensajes que no pueden procesarse después de los intentos establecidos pueden enviarse a una **cola de mensajes no procesables** (*Dead Letter Queue*, DLQ), si la tecnología utilizada lo permite.

### 5.6. Observabilidad y trazabilidad

Una operación puede recorrer varios productores, intermediarios y consumidores antes de completarse.

Por ello, resulta fundamental utilizar identificadores de correlación, registros estructurados, métricas y trazas distribuidas para reconstruir el recorrido de un evento y detectar problemas.

### 5.7. Evolución de los contratos de eventos

Los productores y consumidores deben interpretar los eventos de forma compatible.

Modificar el nombre, el formato o el significado de un campo puede afectar a consumidores que no se actualicen al mismo tiempo.

Para reducir este riesgo, es recomendable definir contratos claros, documentar los eventos y aplicar estrategias de compatibilidad y versionado.

**Fuentes:**
- [Microsoft Azure: Arquitectura basada en eventos](https://learn.microsoft.com/es-es/azure/architecture/guide/architecture-styles/event-driven)
- [Apache Kafka: Documentación oficial](https://kafka.apache.org/documentation/)
- [RabbitMQ: Documentación oficial](https://www.rabbitmq.com/docs)

## 6. Ejemplo práctico: sistema de pedidos

Imaginemos una aplicación de comercio electrónico que utiliza una arquitectura dirigida por eventos para gestionar las compras.

### 6.1. Flujo de funcionamiento

1. Un cliente realiza una compra a través de la aplicación.
2. El sistema de pedidos valida la información y registra el pedido.
3. Se publica un evento llamado `PedidoCreado`.
4. El servicio de inventario consume el evento y gestiona la reserva de los productos.
5. El servicio de pagos procesa el cobro según las reglas del negocio.
6. Cuando el pago se confirma, se publica el evento `PagoConfirmado`.
7. El servicio de notificaciones consume ese evento y envía una confirmación al cliente.
8. El sistema de análisis recibe los eventos relevantes para actualizar sus indicadores de negocio.

Los pasos exactos y sus dependencias dependerán de las reglas de negocio. Por ejemplo, en algunos sistemas el pedido no se considera confirmado hasta que el pago y la reserva del inventario han finalizado correctamente.

### 6.2. Representación simplificada

```text
                   ┌───────────────────┐
                   │ Cliente / API     │
                   └─────────┬─────────┘
                             │
                             ▼
                   ┌───────────────────┐
                   │ Servicio de       │
                   │ pedidos            │
                   └─────────┬─────────┘
                             │
                             ▼
                   ┌───────────────────┐
                   │ Intermediario de  │
                   │ eventos            │
                   └─────────┬─────────┘
                             │
                ┌────────────┼────────────┐
                ▼            ▼            ▼
        ┌─────────────┐ ┌────────────┐ ┌─────────────┐
        │ Inventario  │ │ Pagos      │ │ Analítica   │
        └─────────────┘ └─────┬──────┘ └─────────────┘
                              │
                              ▼
                    ┌───────────────────┐
                    │ Pago confirmado   │
                    └─────────┬─────────┘
                              │
                              ▼
                    ┌───────────────────┐
                    │ Notificaciones    │
                    └───────────────────┘
```

El diagrama representa una simplificación conceptual. En una implementación real, los eventos de pago, inventario y pedidos pueden seguir flujos separados y coordinarse mediante una máquina de estados o un proceso de negocio.

### 6.3. Ejemplo de evento en formato JSON

Un evento de creación de pedido podría tener la siguiente estructura:

```json
{
  "eventId": "evt-10001",
  "eventType": "PedidoCreado",
  "eventVersion": 1,
  "occurredAt": "2026-10-12T10:30:00Z",
  "data": {
    "pedidoId": "PED-12345",
    "clienteId": "CLI-789",
    "importe": 149.99,
    "moneda": "EUR"
  }
}
```

Este ejemplo es ilustrativo. En un sistema real, conviene definir un contrato formal para los eventos, utilizar identificadores adecuados y evitar incluir información personal o sensible que los consumidores no necesiten.

## 7. Tecnologías habituales

| Tecnología | Uso principal |
|---|---|
| Apache Kafka | Plataforma distribuida de transmisión de eventos. |
| RabbitMQ | Intermediario de mensajería con colas y diferentes patrones de distribución. |
| Amazon EventBridge | Servicio gestionado de enrutamiento de eventos en AWS. |
| Azure Event Grid | Servicio de distribución y enrutamiento de eventos en Azure. |
| Apache Pulsar | Plataforma de mensajería y transmisión de eventos. |

La elección debe basarse en factores como el volumen de mensajes, la necesidad de conservar eventos, las garantías de entrega, los requisitos de orden, la infraestructura existente y los costes operativos.

**Fuentes oficiales:**
- [Apache Kafka](https://kafka.apache.org/documentation/)
- [RabbitMQ](https://www.rabbitmq.com/docs)
- [Amazon EventBridge](https://aws.amazon.com/eventbridge/)
- [Azure Event Grid](https://learn.microsoft.com/es-es/azure/event-grid/overview)
- [Apache Pulsar](https://pulsar.apache.org/docs/)

## 8. Comparación con otras arquitecturas

| Aspecto | Monolítica | Microservicios | Dirigida por eventos |
|---|---|---|---|
| Enfoque principal | Aplicación desplegada como una unidad | Servicios desplegables de forma independiente | Comunicación y procesamiento mediante eventos |
| Comunicación habitual | Llamadas internas | APIs, mensajería o llamadas de red | Publicación y consumo de eventos |
| Acoplamiento | Depende de la modularidad interna | Depende de los límites entre servicios | Puede reducir el acoplamiento entre productores y consumidores |
| Procesamiento asíncrono | Posible, pero no obligatorio | Posible, pero no obligatorio | Es un patrón habitual |
| Escalabilidad | Habitualmente, del conjunto de la aplicación | Independiente por servicio | Según los productores, consumidores e infraestructura de eventos |
| Consistencia de datos | Puede apoyarse en transacciones compartidas | Puede requerir coordinación distribuida | Suele requerir gestionar estados y consistencia eventual |
| Complejidad operativa | Generalmente menor al inicio | Generalmente mayor | Depende de la infraestructura y de los flujos de eventos |

Estas arquitecturas no son mutuamente excluyentes. Una aplicación monolítica puede utilizar eventos internamente y una arquitectura de microservicios puede comunicarse mediante eventos para determinadas operaciones.

La arquitectura dirigida por eventos describe principalmente cómo se producen y consumen los cambios del sistema, mientras que la arquitectura monolítica y la de microservicios describen principalmente cómo se organiza y despliega el software.

## 9. Conclusión

La arquitectura dirigida por eventos permite construir sistemas reactivos, extensibles y capaces de procesar información de manera asíncrona. Sus principales ventajas son el desacoplamiento entre componentes, la escalabilidad de los consumidores y la facilidad para integrar nuevos procesos.

Sin embargo, requiere prestar especial atención a la consistencia de los datos, la duplicación de mensajes, el orden de los eventos, la gestión de errores y la observabilidad.

Por ello, resulta especialmente adecuada para sistemas con numerosos procesos asíncronos, integraciones entre servicios, procesamiento de datos en tiempo real o flujos de negocio que necesitan reaccionar a cambios.

No obstante, no siempre es la solución más sencilla. Si una operación necesita una respuesta inmediata y directa, una llamada síncrona puede ser más apropiada. En muchos sistemas, la mejor solución consiste en combinar la comunicación síncrona y la dirigida por eventos según las necesidades de cada caso de uso.