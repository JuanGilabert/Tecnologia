# Arquitectura hexagonal

## 1. Introducción

La **arquitectura hexagonal**, también conocida como **Ports and Adapters (Puertos y Adaptadores)**, es un patrón de diseño de software propuesto por Alistair Cockburn que busca aislar la lógica de negocio de los detalles técnicos y de los sistemas externos.

Su objetivo principal es que el núcleo de la aplicación pueda funcionar y probarse independientemente de tecnologías concretas, como bases de datos, interfaces de usuario, servicios externos o frameworks.

Para conseguirlo, la arquitectura organiza el sistema alrededor de un núcleo central que contiene la lógica de negocio y define interfaces, denominadas *puertos*, mediante las cuales se comunica con el exterior. Los *adaptadores* implementan esas interfaces o conectan los sistemas externos con ellas.

**Fuentes:**
- [Alistair Cockburn: Hexagonal Architecture](https://alistair.cockburn.us/hexagonal-architecture/)
- [AWS: Hexagonal Architecture](https://docs.aws.amazon.com/prescriptive-guidance/latest/hexagonal-architectures/welcome.html)

## 2. Características principales

### 2.1. Separación de responsabilidades

La arquitectura separa la lógica de negocio de los detalles de infraestructura y presentación. De esta forma, las reglas de negocio no dependen directamente de una base de datos, un framework web o una interfaz gráfica.

Esta separación facilita el mantenimiento, la evolución y la comprensión del código.

### 2.2. Puertos (*Ports*)

Los puertos son interfaces o contratos que definen cómo puede interactuar la aplicación con el exterior.

Se suelen distinguir dos tipos principales:

- **Puertos de entrada:** permiten que actores externos invoquen las funcionalidades de la aplicación. Por ejemplo, una API REST puede utilizar un puerto de entrada para ejecutar un caso de uso de creación de pedidos.
- **Puertos de salida:** definen las capacidades externas que necesita la aplicación. Por ejemplo, una interfaz para guardar pedidos, enviar notificaciones o consultar un servicio externo.

Los puertos representan las necesidades y capacidades de la aplicación sin imponer una tecnología concreta.

### 2.3. Adaptadores (*Adapters*)

Los adaptadores conectan el núcleo de la aplicación con los sistemas externos mediante los puertos definidos.

Entre los ejemplos más habituales se encuentran:

- **Adaptadores de entrada:** controladores REST, interfaces gráficas, consumidores de mensajes o comandos de consola.
- **Adaptadores de salida:** implementaciones de repositorios, clientes HTTP, integraciones con servicios externos o sistemas de mensajería.

Gracias a los adaptadores, es posible cambiar una tecnología sin tener que modificar necesariamente la lógica de negocio.

### 2.4. Independencia tecnológica

La lógica de negocio no debe depender directamente de tecnologías concretas de infraestructura.

Por ejemplo, una aplicación puede cambiar una base de datos relacional por otra tecnología de persistencia sustituyendo el adaptador correspondiente, siempre que se mantenga el contrato definido por el puerto.

Esta independencia también facilita la sustitución de frameworks y la integración con diferentes sistemas externos.

### 2.5. Inversión de dependencias

La arquitectura hexagonal suele apoyarse en el **principio de inversión de dependencias** (*Dependency Inversion Principle*).

En lugar de que la lógica de negocio dependa directamente de las implementaciones técnicas, define abstracciones que esas implementaciones satisfacen.

Así, las dependencias del código apuntan hacia el núcleo de la aplicación y no hacia los detalles externos de infraestructura.

**Fuentes:**
- [Alistair Cockburn: Hexagonal Architecture](https://alistair.cockburn.us/hexagonal-architecture/)
- [AWS: Hexagonal Architecture](https://docs.aws.amazon.com/prescriptive-guidance/latest/hexagonal-architectures/welcome.html)

## 3. Estructura de la arquitectura

La arquitectura hexagonal puede entenderse mediante tres elementos principales:

### 3.1. Núcleo de la aplicación

Contiene la lógica de negocio, las reglas del dominio y los casos de uso que representan las operaciones principales del sistema.

Debe permanecer aislado de los detalles técnicos externos.

### 3.2. Puertos

Definen los contratos de comunicación entre el núcleo y el exterior. Permiten especificar qué operaciones ofrece la aplicación y qué servicios externos necesita.

### 3.3. Adaptadores

Implementan la comunicación entre los puertos y las tecnologías concretas, como bases de datos, APIs, interfaces de usuario o sistemas de mensajería.

### 3.4. Ejemplo de funcionamiento

Imaginemos una aplicación de gestión de pedidos:

1. Un cliente envía una petición a una API REST para crear un pedido.
2. Un controlador actúa como adaptador de entrada y utiliza el puerto correspondiente para invocar el caso de uso.
3. El núcleo ejecuta las reglas de negocio y valida la operación.
4. Cuando necesita persistir el pedido, utiliza un puerto de salida, por ejemplo, una interfaz de repositorio.
5. Un adaptador de salida implementa ese contrato y guarda el pedido en la base de datos.
6. El resultado vuelve al cliente a través del adaptador de entrada.

De esta manera, el caso de uso no necesita conocer los detalles de HTTP ni cómo se almacenan físicamente los datos.

## 4. Ventajas

### 4.1. Facilidad de mantenimiento

Al separar la lógica de negocio de la infraestructura, los cambios tecnológicos suelen tener un impacto más limitado sobre el núcleo de la aplicación.

### 4.2. Mayor facilidad para realizar pruebas

Los puertos permiten sustituir dependencias externas por implementaciones simuladas (*mocks* o *fakes*). Esto facilita probar los casos de uso sin necesidad de levantar una base de datos real o desplegar todos los servicios externos.

### 4.3. Flexibilidad tecnológica

Es posible cambiar adaptadores para incorporar nuevas tecnologías de persistencia, comunicación o presentación sin modificar necesariamente las reglas de negocio.

### 4.4. Reutilización de la lógica de negocio

El mismo núcleo puede utilizarse desde distintos canales de entrada, como una API REST, una aplicación web, una interfaz de consola o un consumidor de mensajes.

### 4.5. Menor acoplamiento

La aplicación depende de contratos bien definidos en lugar de depender directamente de implementaciones concretas. Esto reduce el impacto de determinados cambios y favorece una evolución más controlada del sistema.

**Fuentes:**
- [Alistair Cockburn: Hexagonal Architecture](https://alistair.cockburn.us/hexagonal-architecture/)
- [AWS: Hexagonal Architecture](https://docs.aws.amazon.com/prescriptive-guidance/latest/hexagonal-architectures/welcome.html)

## 5. Desafíos

### 5.1. Mayor complejidad inicial

La introducción de puertos, adaptadores e interfaces puede generar más estructuras y archivos que una implementación sencilla en la que toda la lógica se encuentra concentrada en unas pocas clases.

Si el proyecto es pequeño o tiene requisitos simples, esta estructura adicional puede no aportar suficientes beneficios.

### 5.2. Diseño adecuado de los puertos

Definir interfaces mal diseñadas puede generar abstracciones innecesarias o contratos demasiado ligados a una tecnología concreta.

Los puertos deben representar necesidades reales de la aplicación y mantener un nivel de abstracción coherente con el dominio.

### 5.3. Riesgo de sobreingeniería

No todas las dependencias necesitan una abstracción específica. Introducir interfaces y adaptadores para cada componente sin una justificación clara puede aumentar la complejidad y dificultar la lectura del código.

### 5.4. Disciplina arquitectónica

Para mantener la independencia del núcleo, es necesario respetar los límites entre capas y evitar que la lógica de negocio dependa directamente de controladores, frameworks o detalles de infraestructura.

Esta separación debe reflejarse tanto en el diseño como en las dependencias reales del código.

**Fuentes:**
- [Alistair Cockburn: Hexagonal Architecture](https://alistair.cockburn.us/hexagonal-architecture/)
- [AWS: Hexagonal Architecture](https://docs.aws.amazon.com/prescriptive-guidance/latest/hexagonal-architectures/welcome.html)

## 6. Ejemplo de organización de un proyecto

Una aplicación basada en arquitectura hexagonal podría organizarse de la siguiente manera:

```text
src/
├── domain/
│   ├── entities/
│   └── value-objects/
├── application/
│   ├── ports/
│   │   ├── in/
│   │   └── out/
│   └── use-cases/
├── adapters/
│   ├── in/
│   │   ├── rest/
│   │   └── messaging/
│   └── out/
│       ├── persistence/
│       └── external-services/
└── infrastructure/
    ├── configuration/
    └── dependency-injection/
```

Esta estructura es orientativa, no una convención obligatoria. Los nombres y la distribución de directorios pueden variar según el lenguaje de programación, el framework y las necesidades del proyecto.

## 7. Conclusión

La arquitectura hexagonal permite construir aplicaciones en las que la lógica de negocio permanece aislada de los detalles técnicos. Mediante puertos y adaptadores, facilita las pruebas, reduce el acoplamiento y permite sustituir tecnologías externas con un impacto limitado sobre el núcleo de la aplicación.

Sin embargo, su adopción requiere diseñar adecuadamente las interfaces y mantener los límites arquitectónicos. Una abstracción excesiva puede añadir complejidad innecesaria.

Por tanto, resulta especialmente útil cuando la lógica de negocio es importante, se prevén cambios tecnológicos o se necesita probar la aplicación de forma independiente de la infraestructura. En proyectos pequeños y sencillos, una solución más directa puede ser suficiente.