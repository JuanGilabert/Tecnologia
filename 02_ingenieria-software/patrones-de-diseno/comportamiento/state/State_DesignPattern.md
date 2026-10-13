# Patrón de Diseño State (Comportamiento)

## 1. Introducción

El patrón de diseño **State (Estado)** pertenece a la categoría de los patrones de comportamiento. Su objetivo es permitir que un objeto modifique su comportamiento cuando cambia su estado interno, dando la impresión de que su clase ha cambiado.

En lugar de concentrar toda la lógica de comportamiento en estructuras condicionales como `if-else` o `switch`, State delega las operaciones en objetos que representan los diferentes estados posibles.

Este patrón resulta especialmente útil cuando un objeto puede encontrarse en varios estados y las operaciones disponibles o su comportamiento dependen del estado actual.

Algunos ejemplos habituales son:

- El ciclo de vida de un pedido.
- Las máquinas expendedoras.
- Los reproductores multimedia.
- Los flujos de autenticación.
- Los procesos de pago.
- Los sistemas de conexión y desconexión.
- Los flujos de trabajo con diferentes fases.

La idea principal es **encapsular el comportamiento asociado a cada estado en clases independientes**, reduciendo la complejidad del objeto que mantiene el estado.

## 2. Problema que resuelve

Imaginemos una aplicación que gestiona pedidos. Un pedido puede encontrarse en diferentes estados:

- Pendiente.
- Confirmado.
- Enviado.
- Entregado.
- Cancelado.

El comportamiento de una operación depende del estado actual del pedido. Por ejemplo, un pedido pendiente puede confirmarse o cancelarse, mientras que un pedido enviado no debería poder cancelarse mediante la misma operación habitual.

Una implementación tradicional podría utilizar múltiples condiciones:

```java
public class Pedido {

    private String estado = "PENDIENTE";

    public void procesar() {

        if (estado.equals("PENDIENTE")) {
            System.out.println("Confirmando pedido");

        } else if (estado.equals("CONFIRMADO")) {
            System.out.println("Preparando envío");

        } else if (estado.equals("ENVIADO")) {
            System.out.println("Pedido en tránsito");

        } else if (estado.equals("ENTREGADO")) {
            System.out.println("Pedido completado");

        } else if (estado.equals("CANCELADO")) {
            System.out.println("Pedido cancelado");
        }
    }
}
```

Este enfoque presenta varios inconvenientes:

- **Condicionales crecientes:** cada nuevo estado añade más ramas a la lógica.
- **Dificultad de mantenimiento:** las reglas de cada estado se encuentran mezcladas en una misma clase.
- **Riesgo de inconsistencias:** distintas operaciones pueden implementar de forma diferente las mismas reglas.
- **Extensibilidad limitada:** añadir un estado obliga a revisar varios métodos.
- **Responsabilidades mezcladas:** la clase principal termina controlando tanto el estado como todas las variantes de comportamiento.

A medida que aumenta el número de estados y operaciones, la complejidad de las condiciones puede crecer considerablemente.

State permite solucionar este problema delegando las operaciones en el objeto que representa el estado actual.

## 3. Solución propuesta

El patrón introduce una interfaz común para representar los estados y una clase concreta para cada uno de ellos.

El objeto principal, denominado habitualmente **Context**, mantiene una referencia al estado actual y delega en él las operaciones que dependen del estado.

Por ejemplo:

```java
Pedido pedido = new Pedido();

pedido.confirmar();
pedido.enviar();
pedido.entregar();
```

Cada operación se comporta de forma diferente según el estado actual del pedido.

La clase `Pedido` no necesita contener todas las reglas de comportamiento en grandes bloques condicionales. En su lugar, delega las operaciones en el estado correspondiente.

## 4. Estructura del patrón

Los participantes habituales de State son:

- **Context:** objeto cuyo comportamiento depende de su estado interno.
- **State:** interfaz que define las operaciones que pueden realizar los estados.
- **Concrete State:** implementación concreta que define el comportamiento correspondiente a un estado.
- **Cliente:** utiliza el contexto sin tener que gestionar directamente todos los detalles internos de los estados.

### Diagrama conceptual

```text
                  Cliente
                     |
                     v
                  Context
                     |
              estado actual
                     |
                     v
                   State
                     |
          +----------+----------+
          |          |          |
          v          v          v
       Estado A   Estado B   Estado C
```

El contexto mantiene una referencia a la abstracción `State`, por lo que puede trabajar con diferentes implementaciones sin depender directamente de todas las clases concretas.

Cuando cambia el estado actual, también cambia el comportamiento delegado por el contexto.

## 5. Ejemplo de implementación en Java

Imaginemos un sistema de pedidos en el que cada pedido atraviesa distintas fases de su ciclo de vida.

Para simplificar el ejemplo, utilizaremos los siguientes estados:

- Pendiente.
- Confirmado.
- Enviado.
- Entregado.
- Cancelado.

Las transiciones estarán controladas por los propios estados.

### 5.1. Definición de la interfaz State

Primero se define una interfaz que establece las operaciones disponibles.

```java
public interface EstadoPedido {

    void confirmar(Pedido pedido);

    void enviar(Pedido pedido);

    void entregar(Pedido pedido);

    void cancelar(Pedido pedido);

    String obtenerNombre();
}
```

Cada implementación deberá definir cómo se comporta el pedido cuando se invoca una operación desde ese estado.

Aunque todas las clases comparten el mismo contrato, cada una podrá permitir, rechazar o gestionar las operaciones de forma diferente.

### 5.2. Implementación del estado Pendiente

Un pedido pendiente puede confirmarse o cancelarse, pero no enviarse ni entregarse directamente.

```java
public class EstadoPendiente implements EstadoPedido {

    @Override
    public void confirmar(Pedido pedido) {
        System.out.println("Pedido confirmado");
        pedido.setEstado(new EstadoConfirmado());
    }

    @Override
    public void enviar(Pedido pedido) {
        System.out.println(
            "No se puede enviar un pedido pendiente"
        );
    }

    @Override
    public void entregar(Pedido pedido) {
        System.out.println(
            "No se puede entregar un pedido pendiente"
        );
    }

    @Override
    public void cancelar(Pedido pedido) {
        System.out.println("Pedido cancelado");
        pedido.setEstado(new EstadoCancelado());
    }

    @Override
    public String obtenerNombre() {
        return "PENDIENTE";
    }
}
```

La clase encapsula las reglas aplicables al estado pendiente. Cuando el pedido se confirma, sustituye su estado por una instancia de `EstadoConfirmado`.

### 5.3. Implementación del estado Confirmado

Un pedido confirmado puede enviarse o cancelarse.

```java
public class EstadoConfirmado implements EstadoPedido {

    @Override
    public void confirmar(Pedido pedido) {
        System.out.println("El pedido ya está confirmado");
    }

    @Override
    public void enviar(Pedido pedido) {
        System.out.println("Pedido enviado");
        pedido.setEstado(new EstadoEnviado());
    }

    @Override
    public void entregar(Pedido pedido) {
        System.out.println(
            "El pedido debe enviarse antes de entregarse"
        );
    }

    @Override
    public void cancelar(Pedido pedido) {
        System.out.println("Pedido cancelado");
        pedido.setEstado(new EstadoCancelado());
    }

    @Override
    public String obtenerNombre() {
        return "CONFIRMADO";
    }
}
```

El estado confirmado define sus propias reglas sin modificar la clase principal `Pedido`.

### 5.4. Implementación del estado Enviado

Un pedido enviado puede entregarse, pero en este ejemplo no puede cancelarse mediante la operación habitual.

```java
public class EstadoEnviado implements EstadoPedido {

    @Override
    public void confirmar(Pedido pedido) {
        System.out.println("El pedido ya fue confirmado");
    }

    @Override
    public void enviar(Pedido pedido) {
        System.out.println("El pedido ya fue enviado");
    }

    @Override
    public void entregar(Pedido pedido) {
        System.out.println("Pedido entregado");
        pedido.setEstado(new EstadoEntregado());
    }

    @Override
    public void cancelar(Pedido pedido) {
        System.out.println(
            "No se puede cancelar un pedido enviado"
        );
    }

    @Override
    public String obtenerNombre() {
        return "ENVIADO";
    }
}
```

Las reglas de cancelación pueden variar según el dominio real. En un sistema de comercio electrónico, por ejemplo, podría existir un proceso específico para solicitar una devolución o detener un envío.

### 5.5. Implementación del estado Entregado

El estado entregado representa un estado final en el que las operaciones habituales de transición dejan de estar disponibles.

```java
public class EstadoEntregado implements EstadoPedido {

    @Override
    public void confirmar(Pedido pedido) {
        System.out.println("El pedido ya está entregado");
    }

    @Override
    public void enviar(Pedido pedido) {
        System.out.println("El pedido ya está entregado");
    }

    @Override
    public void entregar(Pedido pedido) {
        System.out.println("El pedido ya está entregado");
    }

    @Override
    public void cancelar(Pedido pedido) {
        System.out.println(
            "No se puede cancelar un pedido entregado"
        );
    }

    @Override
    public String obtenerNombre() {
        return "ENTREGADO";
    }
}
```

### 5.6. Implementación del estado Cancelado

El estado cancelado también se considera final para las operaciones incluidas en este ejemplo.

```java
public class EstadoCancelado implements EstadoPedido {

    @Override
    public void confirmar(Pedido pedido) {
        System.out.println("El pedido está cancelado");
    }

    @Override
    public void enviar(Pedido pedido) {
        System.out.println(
            "No se puede enviar un pedido cancelado"
        );
    }

    @Override
    public void entregar(Pedido pedido) {
        System.out.println(
            "No se puede entregar un pedido cancelado"
        );
    }

    @Override
    public void cancelar(Pedido pedido) {
        System.out.println("El pedido ya está cancelado");
    }

    @Override
    public String obtenerNombre() {
        return "CANCELADO";
    }
}
```

### 5.7. Implementación del Context

La clase `Pedido` mantiene el estado actual y delega las operaciones en él.

```java
public class Pedido {

    private EstadoPedido estado;

    public Pedido() {
        this.estado = new EstadoPendiente();
    }

    public void confirmar() {
        estado.confirmar(this);
    }

    public void enviar() {
        estado.enviar(this);
    }

    public void entregar() {
        estado.entregar(this);
    }

    public void cancelar() {
        estado.cancelar(this);
    }

    public void setEstado(EstadoPedido estado) {
        this.estado = estado;
    }

    public String obtenerEstado() {
        return estado.obtenerNombre();
    }
}
```

La clase `Pedido` no necesita conocer todas las reglas de transición. Su responsabilidad principal es mantener el estado actual y delegar las operaciones.

En una implementación de producción, convendría controlar también cómo se cambia el estado para impedir modificaciones arbitrarias desde componentes externos.

### 5.8. Uso desde el cliente

```java
public class Main {

    public static void main(String[] args) {

        Pedido pedido = new Pedido();

        System.out.println(pedido.obtenerEstado());

        pedido.confirmar();
        System.out.println(pedido.obtenerEstado());

        pedido.enviar();
        System.out.println(pedido.obtenerEstado());

        pedido.entregar();
        System.out.println(pedido.obtenerEstado());

        pedido.cancelar();
        System.out.println(pedido.obtenerEstado());
    }
}
```

Una posible salida sería:

```text
PENDIENTE
Pedido confirmado
CONFIRMADO
Pedido enviado
ENVIADO
Pedido entregado
ENTREGADO
No se puede cancelar un pedido entregado
ENTREGADO
```

### 5.9. Explicación del ejemplo

1. Se crea un pedido cuyo estado inicial es `EstadoPendiente`.
2. El cliente invoca `confirmar()` sobre el contexto.
3. El contexto delega la operación en el estado actual.
4. El estado pendiente permite la confirmación y cambia el estado del pedido.
5. Las siguientes operaciones se ejecutan utilizando la nueva implementación de `EstadoPedido`.
6. Cuando se intenta cancelar un pedido entregado, el estado actual rechaza la operación.

Este enfoque permite añadir nuevos estados con sus propias reglas, reduciendo la necesidad de modificar una gran estructura condicional centralizada.

## 6. Variante: State con enumeraciones y condicionales

No siempre es necesario crear una clase para cada estado.

Para flujos sencillos, puede ser suficiente utilizar un `enum`:

```java
public enum EstadoPedido {
    PENDIENTE,
    CONFIRMADO,
    ENVIADO,
    ENTREGADO,
    CANCELADO
}
```

La clase principal podría almacenar el estado así:

```java
private EstadoPedido estado = EstadoPedido.PENDIENTE;
```

Esta solución permite representar los estados de manera segura y evitar cadenas de texto arbitrarias. Sin embargo, si el comportamiento continúa dependiendo de grandes estructuras `switch`, no se está aplicando plenamente el patrón State.

### ¿Cuándo utilizar cada alternativa?

- **Enum:** cuando los estados son simples y no presentan demasiadas diferencias de comportamiento.
- **State con clases:** cuando cada estado tiene reglas, operaciones o transiciones significativamente diferentes.

El objetivo no es eliminar todas las condiciones del código, sino encapsular el comportamiento variable cuando ello mejora el diseño.

## 7. State frente a una máquina de estados finitos

State está relacionado con las máquinas de estados finitos (*Finite State Machines*, FSM), pero ambos conceptos no son equivalentes.

Una máquina de estados finitos es un modelo que define:

- Un conjunto de estados.
- Un estado inicial.
- Un conjunto de eventos o entradas.
- Las transiciones permitidas.
- Opcionalmente, acciones asociadas a las transiciones.

El patrón State es una técnica de diseño orientado a objetos que permite encapsular el comportamiento de cada estado en clases independientes.

Una aplicación puede utilizar State para implementar una máquina de estados, pero también puede representar sus transiciones mediante tablas, configuraciones o bibliotecas especializadas.

Cuando existen numerosos estados, transiciones complejas, estados jerárquicos o reglas temporales, una máquina de estados explícita puede ser más apropiada que una colección de clases State creadas manualmente.

## 8. Ventajas

- **Separación de responsabilidades:** cada estado concentra el comportamiento que le corresponde.
- **Reducción de condicionales:** evita que la clase principal acumule grandes bloques `if-else` o `switch`.
- **Extensibilidad:** permite incorporar estados nuevos con implementaciones independientes.
- **Mantenimiento:** las reglas específicas de cada estado se encuentran localizadas.
- **Claridad del comportamiento:** resulta más sencillo identificar qué operaciones están permitidas en cada estado.
- **Encapsulación de transiciones:** las reglas para cambiar de estado pueden definirse junto al comportamiento correspondiente.
- **Mejor organización:** facilita representar ciclos de vida complejos dentro de una estructura orientada a objetos.

## 9. Desventajas

- **Aumento del número de clases:** cada estado puede requerir una implementación independiente.
- **Complejidad adicional:** para unos pocos estados con comportamientos simples, puede ser excesivo.
- **Transiciones distribuidas:** si las reglas no se documentan bien, puede resultar difícil entender todas las transiciones posibles.
- **Gestión del estado compartido:** las clases State necesitan colaborar correctamente con el contexto.
- **Cambios transversales:** modificar una operación común a todos los estados puede requerir revisar varias implementaciones.
- **Pruebas más amplias:** conviene verificar cada estado y cada transición relevante.
- **Concurrencia:** si varias operaciones cambian el estado simultáneamente, es necesario definir cómo se sincronizan las transiciones.

## 10. Cuándo utilizar State

Es recomendable utilizar este patrón cuando:

- El comportamiento de un objeto cambia según su estado interno.
- Existen muchas condiciones que dependen de ese estado.
- Algunas operaciones solo están permitidas en determinados estados.
- Las reglas de transición son suficientemente complejas como para justificar su encapsulación.
- Se necesita incorporar nuevos estados sin modificar constantemente una clase central.
- El objeto representa un ciclo de vida claramente definido.

### Cuándo no utilizarlo

Puede ser innecesario cuando:

- Solo existen dos o tres estados con comportamientos muy sencillos.
- Las operaciones no cambian realmente su comportamiento según el estado.
- Una enumeración y unas pocas condiciones son suficientes.
- La introducción de clases independientes hace más difícil comprender el flujo.
- Las transiciones son tan complejas que conviene utilizar un motor de flujos de trabajo o una máquina de estados explícita.

## 11. Ejemplos de uso en aplicaciones reales

### 11.1. Ciclo de vida de pedidos

Un pedido puede pasar por los estados pendiente, confirmado, enviado y entregado. Cada estado define qué operaciones son válidas.

### 11.2. Reproductores multimedia

Un reproductor puede tener estados como detenido, reproduciendo y pausado. La operación `play()` puede producir resultados distintos según el estado actual.

### 11.3. Sistemas de autenticación

Una sesión puede estar sin autenticar, autenticada o expirada. Las operaciones permitidas dependen de la situación de la sesión.

### 11.4. Procesos de pago

Un pago puede estar pendiente, autorizado, capturado, rechazado o reembolsado. Cada transición debe cumplir reglas de negocio concretas.

### 11.5. Conexiones de red

Una conexión puede estar desconectada, conectando, conectada o cerrándose. El patrón ayuda a organizar las operaciones válidas en cada fase.

### 11.6. Flujos de aprobación

Una solicitud puede estar pendiente de revisión, aprobada, rechazada o cancelada. Cada estado determina las acciones disponibles para los usuarios y los siguientes pasos del proceso.

## 12. Relación con los principios SOLID

State puede contribuir a un diseño modular cuando las responsabilidades están bien distribuidas.

### Single Responsibility Principle (SRP)

Cada clase concreta representa un estado y concentra el comportamiento correspondiente a ese estado, mientras que el contexto mantiene la referencia al estado actual y delega las operaciones.

### Open/Closed Principle (OCP)

Es posible añadir nuevas implementaciones de `EstadoPedido` sin reescribir necesariamente el contexto.

Sin embargo, si añadir un estado exige modificar muchas clases o transiciones, será necesario revisar el diseño para evitar acoplamientos innecesarios.

### Liskov Substitution Principle (LSP)

Todas las implementaciones de la interfaz `State` deben respetar el contrato definido por ella. Las operaciones pueden producir resultados diferentes, pero sus comportamientos deben ser coherentes con las reglas y expectativas del sistema.

### Dependency Inversion Principle (DIP)

El contexto depende de la abstracción `EstadoPedido`, no directamente de una única implementación concreta para todas sus operaciones.

No obstante, el contexto todavía puede crear el estado inicial de manera directa. Si se necesita una inicialización más flexible, esta responsabilidad puede delegarse en una Factory u otro mecanismo de configuración.

## 13. Buenas prácticas

1. **Definir los estados y sus transiciones:** documentar qué cambios están permitidos.
2. **Mantener coherencia en las reglas:** evitar que distintas clases implementen de forma contradictoria la misma transición.
3. **Limitar el acceso al cambio de estado:** impedir que cualquier componente modifique arbitrariamente el estado interno.
4. **Evitar estados innecesarios:** cada estado debería representar una diferencia significativa de comportamiento o de ciclo de vida.
5. **Separar estado y datos del dominio:** no convertir todas las propiedades del objeto en clases de estado.
6. **Probar las transiciones:** verificar tanto las operaciones permitidas como las rechazadas.
7. **Definir el tratamiento de operaciones inválidas:** decidir si deben ignorarse, registrarse o provocar una excepción de dominio.
8. **Evaluar el uso de enumeraciones:** para flujos simples, un `enum` puede ser más claro que muchas clases.
9. **Controlar la concurrencia:** si el contexto se utiliza desde varios hilos, garantizar que las transiciones se realicen de forma consistente.
10. **Considerar máquinas de estados explícitas:** cuando existen numerosas transiciones, estados jerárquicos o reglas complejas.
11. **Evitar efectos secundarios inesperados:** una transición de estado debería tener un comportamiento claro y documentado.

## 14. Comparación con otros patrones de comportamiento

| Patrón | Objetivo principal | Diferencia respecto a State |
|---|---|---|
| Strategy | Encapsular algoritmos intercambiables. | El algoritmo suele seleccionarse para variar cómo se realiza una tarea; State representa comportamientos vinculados al estado interno y a sus transiciones. |
| Observer | Notificar a otros objetos cuando ocurre un cambio. | State modifica el comportamiento del contexto; Observer comunica los cambios a otros componentes. |
| Command | Encapsular una operación como un objeto. | Representa solicitudes o acciones independientes de quien las invoca. |
| Chain of Responsibility | Pasar una solicitud entre distintos manejadores. | La solicitud recorre una cadena de posibles procesadores. |
| Template Method | Definir la estructura de un algoritmo y delegar algunos pasos. | Mantiene una secuencia de ejecución con pasos redefinibles. |
| State | Encapsular el comportamiento dependiente del estado. | Se centra en cambiar el comportamiento según el estado actual del objeto. |

### Diferencia entre State y Strategy

State y Strategy pueden tener estructuras muy similares: ambos utilizan una abstracción y diferentes implementaciones concretas.

La diferencia principal se encuentra en la intención:

- **Strategy:** permite seleccionar o intercambiar un algoritmo, normalmente desde el cliente o la configuración.
- **State:** representa el estado de un objeto y permite que su comportamiento varíe con él, a menudo mediante transiciones.

En State, las transiciones pueden estar coordinadas por los propios estados o por el contexto. En Strategy, el algoritmo suele seleccionarse de forma explícita según la necesidad del cliente.

## 15. Conclusión

State es un patrón de comportamiento que permite representar los distintos estados de un objeto mediante implementaciones independientes y delegar en ellas las operaciones cuyo comportamiento depende del estado actual.

Su principal aportación consiste en reducir la complejidad de las estructuras condicionales, encapsular las reglas de cada estado y facilitar el mantenimiento de ciclos de vida con múltiples transiciones.

Resulta especialmente útil en pedidos, pagos, reproductores multimedia, conexiones y flujos de aprobación. Sin embargo, no siempre es necesario: cuando los estados son pocos y simples, una enumeración con condiciones puede ser suficiente.

State se parece estructuralmente a Strategy, pero su intención es diferente: Strategy permite intercambiar algoritmos, mientras que State organiza los comportamientos y las transiciones de un objeto según su estado actual. Esta distinción es especialmente útil en entrevistas técnicas y en el diseño de flujos de negocio.

En definitiva, **State permite organizar el comportamiento de un objeto en función de su estado interno, haciendo más explícitas sus reglas y transiciones**.

## 16. Referencias

- [Refactoring Guru — State](https://refactoring.guru/design-patterns/state)
- [SourceMaking — State Design Pattern](https://sourcemaking.com/design_patterns/state)
- [Microsoft — State pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/)
- [Java Documentation](https://docs.oracle.com/en/java/)
