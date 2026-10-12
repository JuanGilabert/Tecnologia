# Clean Architecture (Arquitectura Limpia)

## 1. Introducción

La **Clean Architecture**, o **Arquitectura Limpia**, es un enfoque de diseño de software popularizado por Robert C. Martin (Uncle Bob) que busca organizar una aplicación de manera que la lógica de negocio y las reglas fundamentales sean independientes de los detalles técnicos de implementación.

Su objetivo principal es separar las responsabilidades del sistema y establecer límites claros entre el dominio, los casos de uso, las interfaces y la infraestructura.

La idea fundamental de esta arquitectura es la **regla de dependencia**: las dependencias del código deben apuntar hacia el interior, es decir, hacia las políticas y reglas de negocio. El dominio no debe depender directamente de frameworks, bases de datos, interfaces gráficas ni servicios externos.

Gracias a este enfoque, una aplicación puede evolucionar tecnológicamente sin que cada cambio en la infraestructura obligue a modificar su lógica de negocio.

**Fuentes:**
- [Robert C. Martin: The Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [Microsoft: Arquitectura de aplicaciones web modernas](https://learn.microsoft.com/es-es/dotnet/architecture/modern-web-apps-azure/common-web-application-architectures)

## 2. Características principales

### 2.1. Regla de dependencia

La regla de dependencia establece que las dependencias del código fuente deben apuntar hacia las capas internas de la arquitectura.

Las capas externas pueden conocer y utilizar las interfaces definidas por las capas internas, pero las capas internas no deben depender directamente de los detalles de implementación de las externas.

Por ejemplo, un caso de uso puede depender de una interfaz para guardar pedidos, pero no necesita conocer si los datos se almacenan en PostgreSQL, MySQL o MongoDB.

Esta regla es uno de los principios fundamentales de Clean Architecture.

### 2.2. Separación de responsabilidades

La arquitectura divide la aplicación en componentes con responsabilidades bien definidas.

Habitualmente se distinguen cuatro áreas principales:

- **Entidades:** representan las reglas fundamentales del dominio.
- **Casos de uso:** implementan las operaciones específicas que puede realizar la aplicación.
- **Adaptadores de interfaz:** convierten los datos entre los formatos externos y los modelos internos.
- **Frameworks e infraestructura:** contienen los detalles técnicos, como bases de datos, frameworks web y servicios externos.

Esta separación facilita el mantenimiento y reduce el impacto de los cambios entre componentes.

### 2.3. Independencia del dominio

El dominio contiene las reglas de negocio esenciales y debe permanecer aislado de las tecnologías utilizadas para ejecutar la aplicación.

Por ejemplo, las reglas que determinan si un pedido puede confirmarse no deberían depender de un framework web ni de una biblioteca concreta de persistencia.

Esto permite probar y reutilizar la lógica de negocio sin tener que ejecutar toda la infraestructura.

### 2.4. Independencia de la infraestructura

La aplicación no debe depender directamente de una tecnología específica de almacenamiento, comunicación o presentación.

Para conseguirlo, las capas internas definen contratos que las implementaciones externas satisfacen.

Por ejemplo, un caso de uso puede depender de una interfaz `PedidoRepository`, mientras que una implementación externa utiliza PostgreSQL para persistir los pedidos.

Si cambia la tecnología de almacenamiento, puede sustituirse la implementación sin modificar necesariamente el caso de uso.

### 2.5. Facilidad para realizar pruebas

Al mantener la lógica de negocio aislada de la infraestructura, los casos de uso pueden probarse mediante dependencias simuladas (*mocks* o *fakes*).

Esto permite verificar las reglas de negocio sin necesidad de levantar una base de datos real, iniciar un servidor web o conectarse a servicios externos.

No obstante, las pruebas de integración siguen siendo necesarias para comprobar que las implementaciones concretas funcionan correctamente.

### 2.6. Inversión de dependencias

Clean Architecture aplica habitualmente el **principio de inversión de dependencias** (*Dependency Inversion Principle*).

En lugar de que la lógica de negocio dependa directamente de las implementaciones técnicas, las dependencias se organizan alrededor de abstracciones definidas en las capas internas.

De esta manera, los detalles de infraestructura se adaptan a las necesidades de la aplicación y no al contrario.

**Fuentes:**
- [Robert C. Martin: The Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [Microsoft: Arquitecturas de aplicaciones web](https://learn.microsoft.com/es-es/dotnet/architecture/modern-web-apps-azure/common-web-application-architectures)

## 3. Capas de Clean Architecture

Clean Architecture suele representarse mediante círculos concéntricos. Cada círculo representa un nivel de responsabilidad, y las dependencias del código apuntan hacia el centro.

### 3.1. Entidades (*Entities*)

Las entidades contienen las reglas de negocio más generales y estables del dominio.

Representan conceptos importantes del negocio y encapsulan comportamientos o invariantes que deben cumplirse independientemente de los detalles de la aplicación.

Por ejemplo, en un sistema de pedidos, una entidad `Pedido` puede comprobar que no se confirme un pedido vacío o que sus operaciones respeten determinadas reglas de negocio.

Las entidades no deberían depender de frameworks, controladores ni mecanismos concretos de persistencia.

### 3.2. Casos de uso (*Use Cases*)

Los casos de uso contienen las reglas específicas de la aplicación y coordinan las operaciones necesarias para cumplir una funcionalidad.

Por ejemplo:

- Crear un pedido.
- Confirmar un pedido.
- Cancelar una compra.
- Registrar un usuario.
- Consultar el historial de pedidos.

Un caso de uso puede utilizar entidades del dominio y contratos de salida para guardar información o comunicarse con sistemas externos.

Su responsabilidad es coordinar el flujo de la operación, no implementar directamente los detalles técnicos de la infraestructura.

### 3.3. Adaptadores de interfaz (*Interface Adapters*)

Esta capa transforma los datos entre los formatos externos y los modelos utilizados por los casos de uso.

Entre sus componentes habituales se encuentran:

- **Controladores:** reciben solicitudes y llaman a los casos de uso.
- **Presentadores:** preparan los resultados para su presentación.
- **Repositorios:** implementan contratos de acceso a datos.
- **Mapeadores:** convierten DTO, entidades y modelos de persistencia cuando es necesario.

Por ejemplo, un controlador REST puede recibir una petición HTTP, convertir sus datos en una entrada adecuada para un caso de uso y devolver una respuesta HTTP con el resultado.

### 3.4. Frameworks e infraestructura (*Frameworks & Drivers*)

Es la capa más externa y contiene los detalles técnicos que conectan la aplicación con el entorno.

Puede incluir:

- Frameworks web, como Spring Boot o ASP.NET Core.
- Bases de datos, como PostgreSQL o MySQL.
- Sistemas de mensajería, como Apache Kafka o RabbitMQ.
- Clientes HTTP para consumir APIs externas.
- Configuración, mecanismos de arranque e inyección de dependencias.

Estos componentes pueden cambiar con el tiempo sin obligar a modificar las reglas fundamentales del dominio, siempre que se mantengan correctamente los límites de la arquitectura.

**Importante:** los nombres de las capas y su distribución física pueden variar entre proyectos. Lo esencial es respetar la dirección de las dependencias.

**Fuente:**
- [Robert C. Martin: The Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)

## 4. Ventajas

### 4.1. Mantenibilidad

La separación de responsabilidades facilita localizar la lógica de negocio y comprender cómo se relacionan los componentes.

Los cambios en la infraestructura pueden realizarse sin afectar necesariamente a los casos de uso o a las entidades.

### 4.2. Facilidad para realizar pruebas unitarias

Los casos de uso y las entidades pueden probarse de forma aislada mediante dependencias controladas.

Esto permite verificar las reglas de negocio con pruebas rápidas y reproducibles, sin depender de servicios externos.

### 4.3. Independencia tecnológica

La aplicación puede cambiar de framework, base de datos o proveedor externo con un impacto limitado sobre el núcleo, siempre que las nuevas implementaciones respeten los contratos definidos.

### 4.4. Mayor claridad en las responsabilidades

Cada capa tiene un propósito definido. Esto reduce la tendencia a mezclar reglas de negocio con consultas SQL, controladores HTTP o detalles de presentación.

### 4.5. Evolución del sistema

La arquitectura facilita introducir nuevas funcionalidades manteniendo los límites entre el dominio, los casos de uso y la infraestructura.

También puede ayudar a que varios desarrolladores trabajen sobre componentes distintos con menos interferencias.

### 4.6. Reutilización de los casos de uso

Un mismo caso de uso puede invocarse desde diferentes puntos de entrada, como una API REST, una aplicación de escritorio, una tarea programada o un consumidor de mensajes.

Así se evita duplicar las reglas de negocio en cada interfaz.

**Fuentes:**
- [Robert C. Martin: The Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [Microsoft: Arquitecturas de aplicaciones web](https://learn.microsoft.com/es-es/dotnet/architecture/modern-web-apps-azure/common-web-application-architectures)

## 5. Desafíos

### 5.1. Complejidad inicial

La separación en capas, interfaces, casos de uso y adaptadores puede generar más archivos y estructuras que una aplicación sencilla con una organización más directa.

Para proyectos pequeños, esta estructura adicional puede no aportar suficientes beneficios.

### 5.2. Mayor cantidad de abstracciones

La arquitectura puede requerir interfaces, DTO, modelos de dominio, modelos de persistencia y mecanismos de conversión.

Si estas abstracciones no responden a necesidades reales, pueden aumentar el código repetitivo y dificultar la comprensión del proyecto.

### 5.3. Disciplina arquitectónica

Es necesario mantener las dependencias en la dirección correcta.

Por ejemplo, si una entidad de dominio importa clases de un framework de persistencia, se introduce una dependencia que contradice el objetivo de independencia del dominio.

La estructura de carpetas, por sí sola, no garantiza que se cumpla la regla de dependencia.

### 5.4. Configuración e inyección de dependencias

La aplicación debe conectar las interfaces internas con sus implementaciones concretas.

Esto suele realizarse mediante un contenedor de inyección de dependencias o una configuración explícita durante el arranque.

Una configuración deficiente puede dificultar el inicio de la aplicación y aumentar la complejidad del mantenimiento.

### 5.5. Riesgo de sobreingeniería

No todos los componentes necesitan varias interfaces o capas de abstracción.

Una arquitectura excesivamente elaborada puede ralentizar el desarrollo sin mejorar de forma significativa la calidad del sistema.

La clave consiste en aplicar los principios de Clean Architecture de forma proporcional a la complejidad y a las necesidades del proyecto.

**Fuentes:**
- [Robert C. Martin: The Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [Microsoft: Arquitecturas de aplicaciones web](https://learn.microsoft.com/es-es/dotnet/architecture/modern-web-apps-azure/common-web-application-architectures)

## 6. Ejemplo práctico: sistema de pedidos

Imaginemos una aplicación de comercio electrónico que utiliza Clean Architecture para gestionar los pedidos de sus clientes.

### 6.1. Flujo de funcionamiento

1. Un cliente envía una solicitud HTTP para crear un pedido.
2. Un controlador recibe la petición y valida su formato.
3. El controlador invoca el caso de uso `CrearPedido`.
4. El caso de uso aplica las reglas de negocio y utiliza las entidades correspondientes.
5. Para guardar el pedido, el caso de uso utiliza una interfaz de repositorio definida en una capa interna.
6. Una implementación externa del repositorio guarda los datos en PostgreSQL.
7. El caso de uso devuelve el resultado y el controlador construye la respuesta HTTP.

La lógica de negocio no necesita conocer cómo funciona HTTP ni cómo se ejecutan las consultas SQL.

### 6.2. Ejemplo simplificado en pseudocódigo

**Entidad de dominio:**

```typescript
class Pedido {
  constructor(
    public readonly id: string,
    public readonly importe: number
  ) {
    if (importe <= 0) {
      throw new Error("El importe debe ser positivo");
    }
  }
}
```

**Puerto de salida:**

```typescript
interface PedidoRepository {
  guardar(pedido: Pedido): Promise<void>;
}
```

**Caso de uso:**

```typescript
class CrearPedido {
  constructor(
    private readonly repositorio: PedidoRepository
  ) {}

  async ejecutar(
    id: string,
    importe: number
  ): Promise<void> {
    const pedido = new Pedido(id, importe);

    await this.repositorio.guardar(pedido);
  }
}
```

**Adaptador de persistencia:**

```typescript
class PedidoRepositoryPostgres
  implements PedidoRepository {

  async guardar(pedido: Pedido): Promise<void> {
    // Implementación de persistencia en PostgreSQL.
  }
}
```

Este ejemplo es ilustrativo y omite aspectos como las transacciones, la validación completa, la gestión de errores y la configuración de dependencias.

Lo importante es que `CrearPedido` depende de la interfaz `PedidoRepository`, no directamente de PostgreSQL.

## 7. Ejemplo de organización de un proyecto

Una aplicación basada en Clean Architecture podría organizarse de la siguiente manera:

```text
src/
├── domain/
│   ├── entities/
│   ├── value-objects/
│   └── exceptions/
├── application/
│   ├── use-cases/
│   ├── ports/
│   │   ├── in/
│   │   └── out/
│   └── dto/
├── infrastructure/
│   ├── persistence/
│   │   ├── repositories/
│   │   └── models/
│   ├── web/
│   │   ├── controllers/
│   │   └── routes/
│   ├── messaging/
│   └── external-services/
├── config/
└── main
```

### Descripción de los directorios

- **`domain/`:** contiene las entidades, los objetos de valor y las reglas fundamentales del negocio.
- **`application/`:** implementa los casos de uso y define los contratos necesarios para ejecutarlos.
- **`infrastructure/`:** contiene los adaptadores y las implementaciones técnicas, como los controladores web, los repositorios y los clientes de servicios externos.
- **`config/`:** agrupa la configuración de la aplicación y la conexión entre interfaces e implementaciones.
- **`main`:** representa el punto de entrada y el arranque de la aplicación.

Esta estructura es orientativa y puede adaptarse al lenguaje, al framework y al tamaño del proyecto.

Lo fundamental no es la ubicación exacta de los archivos, sino garantizar que las capas externas dependan de las internas y no al contrario.

## 8. Comparación con otras arquitecturas

| Aspecto | Monolítica | Microservicios | Hexagonal | Clean Architecture |
|---|---|---|---|---|
| Enfoque principal | Unidad de despliegue | Servicios independientes | Puertos y adaptadores | Separación de responsabilidades y dependencias |
| Objetivo principal | Simplificar la construcción y operación de una aplicación integrada | Favorecer la autonomía de los servicios | Aislar el núcleo mediante contratos | Mantener las reglas de negocio independientes de los detalles externos |
| Organización | Puede organizarse por capas o módulos | Cada servicio puede tener su propia organización interna | Núcleo y adaptadores conectados mediante puertos | Entidades, casos de uso, adaptadores e infraestructura |
| Dependencias | Dependen de la organización del código | Se distribuyen entre servicios y contratos de comunicación | Se dirigen hacia las abstracciones del núcleo | Se dirigen hacia las políticas y reglas internas |
| Despliegue | Habitualmente, una unidad | Múltiples unidades independientes | No lo determina | No lo determina |
| Pruebas | Dependen de la modularidad | Requieren pruebas locales y de integración entre servicios | Facilita probar mediante puertos | Facilita probar entidades y casos de uso de forma aislada |

Estas arquitecturas no son necesariamente excluyentes.

Una aplicación puede ser monolítica y aplicar Clean Architecture. También puede utilizar microservicios en los que cada servicio esté organizado siguiendo principios de arquitectura hexagonal o Clean Architecture.

La arquitectura hexagonal y Clean Architecture comparten el objetivo de aislar la lógica de negocio y controlar las dependencias. Sin embargo, Clean Architecture suele presentar una organización más explícita en entidades, casos de uso, adaptadores e infraestructura.

## 9. Conclusión

Clean Architecture permite construir aplicaciones mantenibles, testeables y menos dependientes de tecnologías concretas. Su principal aportación consiste en mantener las reglas de negocio protegidas frente a los cambios de infraestructura, presentación y comunicación.

Gracias a la separación entre entidades, casos de uso, adaptadores e infraestructura, resulta más sencillo probar la lógica central y sustituir implementaciones externas cuando sea necesario.

Sin embargo, este enfoque también puede incrementar la cantidad de abstracciones y el esfuerzo inicial de desarrollo. Por ello, conviene aplicarlo de forma proporcional a la complejidad del sistema.

Es especialmente útil en aplicaciones con reglas de negocio importantes, una vida útil prolongada o una necesidad frecuente de modificar tecnologías e integraciones. Para proyectos muy pequeños, una estructura más sencilla puede ser suficiente.

En definitiva, **Clean Architecture no busca añadir capas por sí mismas, sino proteger la lógica de negocio y mantener las dependencias bajo control**.