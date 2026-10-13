# Arquitectura de microservicios

## 1. Introducción

La **arquitectura de microservicios** es un estilo de diseño de software en el que una aplicación grande se divide en un conjunto de pequeños servicios autónomos, independientes y débilmente acoplados. Cada microservicio se encarga de una función o de un dominio de negocio específico.

Este enfoque permite desarrollar, desplegar y escalar cada servicio de forma independiente, favoreciendo la flexibilidad y la evolución de las aplicaciones.

**Fuentes:**
- [Google Cloud: ¿Qué es la arquitectura de microservicios?](https://cloud.google.com/learn/what-is-microservices-architecture?hl=es)
- [Microsoft Azure: Estilo de arquitectura de microservicios](https://learn.microsoft.com/es-es/azure/architecture/guide/architecture-styles/microservices)
- [IBM: Microservicios](https://www.ibm.com/es-es/think/topics/microservices)
- [Wikipedia: Arquitectura de microservicios](https://es.wikipedia.org/wiki/Arquitectura_de_microservicios)

## 2. Características principales

### 2.1. Autonomía

Cada microservicio puede desplegarse, actualizarse y escalarse de forma independiente, sin necesidad de modificar o desplegar toda la aplicación.

**Fuentes:**
- [Decide Soluciones: Arquitectura de microservicios](https://decidesoluciones.es/arquitectura-de-microservicios/)
- [AWS: Microservicios](https://aws.amazon.com/es/microservices/)
- [Microsoft Azure: Microservicios](https://learn.microsoft.com/es-es/azure/architecture/guide/architecture-styles/microservices)

### 2.2. Bases de datos descentralizadas

Cada servicio suele gestionar su propio almacenamiento y persistencia de datos. Esto permite que cada microservicio utilice la tecnología de almacenamiento más adecuada para sus necesidades.

**Fuentes:**
- [Microsoft Azure: Microservicios](https://learn.microsoft.com/es-es/azure/architecture/guide/architecture-styles/microservices)
- [Decide Soluciones: Arquitectura de microservicios](https://decidesoluciones.es/arquitectura-de-microservicios/)

### 2.3. Comunicación mediante APIs

Los microservicios intercambian información mediante interfaces y protocolos de comunicación ligeros. Entre los más habituales se encuentran:

- **HTTP/REST:** permite la comunicación mediante peticiones y respuestas.
- **gRPC:** facilita la comunicación remota entre servicios de forma eficiente.
- **Sistemas de mensajería:** como Apache Kafka o RabbitMQ, que permiten implementar comunicación asíncrona y basada en eventos.

**Fuentes:**
- [AWS: Microservicios](https://aws.amazon.com/es/microservices/)
- [Microsoft Azure: Microservicios](https://learn.microsoft.com/es-es/azure/architecture/guide/architecture-styles/microservices)
- [Wikipedia: Arquitectura de microservicios](https://es.wikipedia.org/wiki/Arquitectura_de_microservicios)

### 2.4. Libertad tecnológica

Cada microservicio puede utilizar diferentes lenguajes de programación, bases de datos y tecnologías según sus necesidades. Este enfoque se conoce como **arquitectura políglota**.

**Fuentes:**
- [Microsoft Azure: Microservicios](https://learn.microsoft.com/es-es/azure/architecture/guide/architecture-styles/microservices)
- [AWS: Microservicios](https://aws.amazon.com/es/microservices/)

## 3. Ventajas

### 3.1. Escalabilidad individual

Permite escalar únicamente los microservicios que reciben una mayor carga de trabajo, sin necesidad de aumentar los recursos de toda la aplicación.

### 3.2. Mayor resiliencia

El fallo de un microservicio no tiene por qué provocar la caída de toda la aplicación. Un diseño adecuado permite aislar los errores y reducir su impacto sobre el resto del sistema.

### 3.3. Agilidad en los equipos de desarrollo

Facilita que equipos pequeños trabajen en paralelo sobre diferentes servicios, con mayor autonomía en el desarrollo, las pruebas y los despliegues.

**Fuentes:**
- [Atlassian: Arquitectura de microservicios](https://www.atlassian.com/es/microservices/microservices-architecture)
- [Google Cloud: ¿Qué es la arquitectura de microservicios?](https://cloud.google.com/learn/what-is-microservices-architecture?hl=es)
- [Microsoft Azure: Microservicios](https://learn.microsoft.com/es-es/azure/architecture/guide/architecture-styles/microservices)

## 4. Desafíos

### 4.1. Complejidad de los sistemas distribuidos

La comunicación entre múltiples servicios introduce dificultades relacionadas con la latencia de red, los fallos de comunicación, la trazabilidad y la coherencia de los datos.

En determinados sistemas se emplean modelos como **BASE** (*Basically Available, Soft state, Eventually consistent*), que priorizan la disponibilidad y admiten la consistencia eventual. Esto requiere diseñar cuidadosamente los mecanismos de comunicación y sincronización de datos.

**Fuentes:**
- [Wikipedia: Arquitectura de microservicios](https://es.wikipedia.org/wiki/Arquitectura_de_microservicios)
- [Microsoft Azure: Microservicios](https://learn.microsoft.com/es-es/azure/architecture/guide/architecture-styles/microservices)

### 4.2. Curva de aprendizaje y costes operativos

La arquitectura de microservicios requiere infraestructura automatizada y herramientas especializadas para gestionar el ciclo de vida de los servicios. Entre las tecnologías y prácticas habituales se encuentran:

- **Docker:** para crear y ejecutar contenedores.
- **Kubernetes:** para orquestar contenedores y gestionar despliegues.
- **CI/CD:** para automatizar la integración, las pruebas y la entrega continua.
- **Observabilidad:** para recopilar registros (*logs*), métricas y trazas distribuidas.

Aunque estas herramientas facilitan la gestión del sistema, su implantación y mantenimiento pueden aumentar los costes operativos y la complejidad técnica.

**Fuentes:**
- [Atlassian: Arquitectura de microservicios](https://www.atlassian.com/es/microservices/microservices-architecture)
- [Microsoft Azure: Microservicios](https://learn.microsoft.com/es-es/azure/architecture/guide/architecture-styles/microservices)
- [Decide Soluciones: Arquitectura de microservicios](https://decidesoluciones.es/arquitectura-de-microservicios/)

## 5. Conclusión

La arquitectura de microservicios permite construir aplicaciones modulares, escalables y flexibles, facilitando el desarrollo independiente de sus componentes y el trabajo paralelo de los equipos.

Sin embargo, también introduce desafíos relacionados con la comunicación distribuida, la coherencia de los datos, la observabilidad y los costes de infraestructura.

Por tanto, su adopción debe basarse en las necesidades del proyecto, la complejidad del dominio de negocio y la capacidad del equipo para gestionar sistemas distribuidos. En aplicaciones pequeñas o con requisitos sencillos, una arquitectura monolítica bien estructurada puede resultar más simple y económica.