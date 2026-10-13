# Arquitectura monolítica

## 1. Introducción

La **arquitectura monolítica** es un estilo de diseño de software en el que una aplicación se desarrolla y despliega como una única unidad. Sus principales componentes funcionales forman parte de un mismo sistema y, habitualmente, comparten un proceso de ejecución y un ciclo de despliegue.

En una aplicación monolítica, los diferentes módulos pueden encargarse de funciones específicas, como la gestión de usuarios, los pedidos, los pagos o el inventario, pero todos forman parte de una misma aplicación desplegable.

Este enfoque permite centralizar el desarrollo y simplificar determinados aspectos de la ejecución, las pruebas y el despliegue. Sin embargo, a medida que la aplicación crece, puede resultar más difícil mantenerla, escalar partes concretas o modificar componentes sin afectar al conjunto.

**Fuentes:**
- [Martin Fowler: Monolith First](https://martinfowler.com/bliki/MonolithFirst.html)
- [Microsoft Azure: Estilos de arquitectura](https://learn.microsoft.com/es-es/azure/architecture/guide/architecture-styles/)
- [AWS: ¿Qué es una arquitectura monolítica?](https://aws.amazon.com/what-is/monolithic-architecture/)

## 2. Características principales

### 2.1. Despliegue unificado

La aplicación se empaqueta y despliega como una única unidad. Los cambios realizados en uno de sus módulos suelen formar parte de una nueva versión del conjunto, aunque el proceso exacto depende de las herramientas y del diseño del proyecto.

Este modelo simplifica la publicación inicial, ya que no es necesario coordinar el despliegue de múltiples servicios independientes.

### 2.2. Componentes integrados

Los distintos módulos de la aplicación se encuentran dentro de una misma unidad de software. Por ejemplo, una aplicación de comercio electrónico puede incluir módulos para:

- **Gestión de usuarios:** registro, autenticación y perfiles.
- **Catálogo de productos:** consulta y administración de productos.
- **Pedidos:** creación, seguimiento y gestión de compras.
- **Pagos:** coordinación de las operaciones de pago.
- **Inventario:** control de existencias.

Aunque estos módulos pueden estar separados mediante paquetes, clases o capas, forman parte de la misma aplicación desplegable.

### 2.3. Comunicación interna directa

Los componentes suelen comunicarse mediante llamadas a métodos, funciones o interfaces internas, sin necesidad de utilizar obligatoriamente protocolos de comunicación entre procesos.

Esto puede reducir la latencia y simplificar la interacción entre módulos frente a una arquitectura distribuida, siempre que las operaciones no dependan de servicios externos.

### 2.4. Gestión centralizada de datos

Es habitual que los módulos compartan una misma base de datos, aunque no es un requisito obligatorio de la arquitectura monolítica.

Compartir el almacenamiento puede facilitar las transacciones que afectan a varias partes de la aplicación. Sin embargo, también puede generar dependencias entre módulos si estos acceden directamente a las tablas o modifican los datos sin respetar límites bien definidos.

### 2.5. Tecnología y entorno compartidos

Los módulos suelen desarrollarse utilizando un conjunto común de tecnologías, dependencias y herramientas. Esto facilita la estandarización del proyecto, aunque no impide que existan componentes internos con tecnologías diferentes.

**Fuentes:**
- [AWS: ¿Qué es una arquitectura monolítica?](https://aws.amazon.com/what-is/monolithic-architecture/)
- [Martin Fowler: Monolith First](https://martinfowler.com/bliki/MonolithFirst.html)
- [Microsoft Azure: Estilos de arquitectura](https://learn.microsoft.com/es-es/azure/architecture/guide/architecture-styles/)

## 3. Tipos de arquitectura monolítica

Aunque el término monolito suele referirse a una aplicación desplegada como una única unidad, existen diferentes formas de organizar su código.

### 3.1. Monolito tradicional

Todos los componentes de la aplicación forman parte de una misma unidad, con una separación interna que puede ser limitada.

Cuando no existen límites claros entre las responsabilidades, los módulos pueden depender excesivamente unos de otros, haciendo que el mantenimiento sea más difícil a medida que crece el sistema.

### 3.2. Monolito por capas

La aplicación se organiza en capas con responsabilidades diferenciadas. Una estructura habitual incluye:

- **Capa de presentación:** gestiona las solicitudes y la interacción con el usuario.
- **Capa de aplicación o servicios:** coordina los casos de uso.
- **Capa de dominio o negocio:** contiene las reglas de negocio.
- **Capa de persistencia:** gestiona el acceso a los datos.

Esta organización ayuda a separar responsabilidades y facilita el mantenimiento. No obstante, es importante controlar las dependencias para evitar que las capas superiores e inferiores queden demasiado acopladas.

### 3.3. Monolito modular

La aplicación continúa desplegándose como una única unidad, pero se divide en módulos con responsabilidades y límites claramente definidos.

Cada módulo encapsula su lógica y expone interfaces controladas para interactuar con otros módulos. Este enfoque permite conservar la sencillez operativa del monolito y reducir el acoplamiento interno.

Un monolito modular también puede facilitar una futura extracción de determinados módulos como servicios independientes, si las necesidades del negocio lo justifican.

## 4. Ventajas

### 4.1. Simplicidad de desarrollo inicial

En proyectos pequeños o con requisitos bien delimitados, una arquitectura monolítica permite comenzar con una estructura relativamente sencilla, sin tener que configurar desde el principio la comunicación entre múltiples servicios.

### 4.2. Despliegue y operación más sencillos

Al existir una única unidad desplegable, se reduce la necesidad de coordinar versiones, configuraciones y despliegues independientes.

Esto puede simplificar la infraestructura, especialmente en aplicaciones con pocos requisitos de escalabilidad.

### 4.3. Comunicación interna eficiente

Las llamadas entre componentes pueden realizarse directamente dentro del proceso de la aplicación. Esto evita parte de la latencia y de los fallos de comunicación asociados a las llamadas de red entre servicios distribuidos.

### 4.4. Transacciones y consistencia de datos

Cuando los módulos comparten una base de datos transaccional, puede resultar más sencillo realizar operaciones que necesitan modificar varios conjuntos de datos de forma atómica.

La consistencia dependerá, en todo caso, del diseño de las transacciones y de las características del sistema de almacenamiento.

### 4.5. Depuración y pruebas simplificadas

El hecho de ejecutar la aplicación como una única unidad puede facilitar la depuración y las pruebas de integración, ya que hay menos componentes desplegados de forma independiente que coordinar.

**Fuentes:**
- [Martin Fowler: Monolith First](https://martinfowler.com/bliki/MonolithFirst.html)
- [AWS: ¿Qué es una arquitectura monolítica?](https://aws.amazon.com/what-is/monolithic-architecture/)

## 5. Desafíos

### 5.1. Escalabilidad de toda la aplicación

En una arquitectura monolítica tradicional, escalar la aplicación suele implicar desplegar más instancias del conjunto, incluso cuando solo un módulo necesita recursos adicionales.

Esto puede generar un consumo de recursos menos eficiente que en una arquitectura donde cada servicio se escala de forma independiente.

### 5.2. Acoplamiento entre componentes

Si los módulos no están bien separados, un cambio en una parte de la aplicación puede afectar a otras. Con el tiempo, las dependencias internas pueden dificultar la comprensión del sistema y aumentar el riesgo de introducir errores.

Un diseño modular ayuda a reducir este problema, aunque no elimina todos los riesgos.

### 5.3. Despliegues con mayor alcance

Una modificación localizada puede requerir construir, probar y desplegar una nueva versión de toda la aplicación.

A medida que el sistema crece, los procesos de integración, pruebas y publicación pueden volverse más lentos y exigir una mayor coordinación.

### 5.4. Impacto de los fallos

Un error grave, un consumo excesivo de recursos o un fallo en un componente crítico puede afectar a toda la aplicación si no existen mecanismos adecuados de aislamiento y recuperación.

La arquitectura monolítica no implica necesariamente que cualquier fallo derribe el sistema completo, pero el aislamiento de componentes puede ser más limitado que en un sistema distribuido bien diseñado.

### 5.5. Evolución tecnológica más compleja

Los módulos comparten habitualmente el mismo entorno de ejecución y buena parte de las dependencias. Por ello, introducir tecnologías muy diferentes para una funcionalidad concreta puede requerir cambios adicionales o procesos de integración más complejos.

**Fuentes:**
- [AWS: ¿Qué es una arquitectura monolítica?](https://aws.amazon.com/what-is/monolithic-architecture/)
- [Microsoft Azure: Estilos de arquitectura](https://learn.microsoft.com/es-es/azure/architecture/guide/architecture-styles/)

## 6. Ejemplo de organización de un proyecto

Una aplicación monolítica organizada por capas podría presentar la siguiente estructura:

```text
src/
├── presentation/
│   ├── controllers/
│   ├── routes/
│   └── dto/
├── application/
│   ├── services/
│   └── use-cases/
├── domain/
│   ├── entities/
│   ├── value-objects/
│   └── business-rules/
├── infrastructure/
│   ├── persistence/
│   ├── repositories/
│   └── configuration/
└── main
```

### Descripción de los directorios

- **`presentation/`:** recibe las solicitudes externas y presenta los resultados.
- **`application/`:** coordina las operaciones y los casos de uso.
- **`domain/`:** contiene las entidades y las reglas de negocio.
- **`infrastructure/`:** implementa el acceso a bases de datos y otros detalles técnicos.
- **`main`:** representa el punto de entrada de la aplicación; su nombre y formato dependen del lenguaje utilizado.

Esta estructura es orientativa. Los directorios y las capas pueden variar según el lenguaje, el framework y las necesidades del proyecto.

Aunque el código esté organizado en varias capas, la aplicación puede seguir siendo monolítica si se construye y despliega como una única unidad.

## 7. Comparación con otras arquitecturas

| Aspecto | Monolítica | Microservicios |
|---|---|---|
| Unidad de despliegue | Una aplicación | Múltiples servicios independientes |
| Escalabilidad | Habitualmente, de toda la aplicación | Independiente por servicio |
| Comunicación interna | Normalmente, llamadas directas | APIs, mensajería o llamadas de red |
| Complejidad operativa | Generalmente menor al inicio | Generalmente mayor |
| Gestión de datos | Puede compartir una base de datos | Cada servicio suele gestionar sus propios datos |
| Autonomía de equipos | Depende de la modularidad interna | Mayor autonomía potencial |
| Consistencia de datos | Puede simplificarse con transacciones compartidas | Puede requerir consistencia eventual y coordinación distribuida |

La comparación es orientativa: un monolito modular puede ofrecer una separación interna sólida, mientras que los microservicios también pueden presentar problemas de acoplamiento si sus límites están mal definidos.

Por otra parte, la arquitectura monolítica y la arquitectura hexagonal no son alternativas equivalentes: la primera describe principalmente cómo se empaqueta y despliega la aplicación, mientras que la segunda define cómo se organizan las dependencias entre la lógica de negocio y los elementos externos. Es posible construir un monolito siguiendo principios de arquitectura hexagonal.

## 8. Conclusión

La arquitectura monolítica es una opción adecuada para muchas aplicaciones, especialmente cuando los requisitos son claros, el dominio de negocio no presenta una complejidad excesiva y se busca reducir la complejidad operativa inicial.

Sus principales ventajas son la sencillez de despliegue, la comunicación interna eficiente y la posibilidad de gestionar transacciones dentro de un mismo sistema. Sin embargo, una mala separación de responsabilidades puede provocar un acoplamiento elevado, dificultar la evolución del código y limitar la escalabilidad individual de los componentes.

Por ello, una estrategia recomendable es comenzar con una arquitectura monolítica bien estructurada cuando las necesidades del proyecto lo permitan y adoptar una organización modular desde el principio. Si aparecen requisitos que justifiquen distribuir partes del sistema, se podrá evaluar una transición gradual hacia microservicios.

La elección de una arquitectura debe basarse en los requisitos funcionales y no funcionales, el tamaño del equipo, los costes operativos y las necesidades reales de escalabilidad, en lugar de adoptar una arquitectura distribuida por defecto.