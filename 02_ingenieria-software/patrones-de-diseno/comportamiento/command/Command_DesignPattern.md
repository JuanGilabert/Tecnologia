# Patrón de Diseño Command (Comando)

## 1. Introducción

El patrón de diseño **Command** pertenece a la categoría de los patrones de comportamiento (*Behavioral Design Patterns*) definidos por GoF (*Gang of Four*).

Su objetivo es **encapsular una petición como un objeto**, convirtiendo una operación en una entidad independiente que puede almacenarse, pasarse como parámetro, ejecutarse posteriormente o incluso deshacerse.

En lugar de que un objeto invoque directamente una operación de otro objeto, el patrón introduce un objeto comando que representa esa acción.

Esto permite separar al objeto que solicita una operación del objeto que realmente la ejecuta.

### Objetivos principales

- Desacoplar al emisor de una petición del receptor que la ejecuta.
- Encapsular las operaciones en objetos independientes.
- Permitir almacenar, encolar o programar operaciones.
- Facilitar la implementación de operaciones reversibles.
- Permitir registrar un historial de acciones.
- Facilitar la incorporación de nuevas operaciones sin modificar necesariamente el código del emisor.

---

## 2. Problema que resuelve

Imaginemos que estamos desarrollando una aplicación que controla diferentes dispositivos de una casa inteligente: luces, persianas, calefacción y televisión.

Una solución sencilla podría consistir en que el controlador conociera todos los dispositivos y ejecutara directamente sus métodos.

```java
public class ControlRemoto {

    private Luz luz;

    public ControlRemoto(Luz luz) {
        this.luz = luz;
    }

    public void pulsarBotonEncendido() {
        luz.encender();
    }

    public void pulsarBotonApagado() {
        luz.apagar();
    }
}
```

Este enfoque funciona, pero el controlador está acoplado directamente a la clase `Luz`.

Si queremos añadir una televisión, una persiana o cualquier otro dispositivo, tendremos que modificar el controlador para incorporar nuevas dependencias y operaciones.

Además, implementar funcionalidades como deshacer una acción, almacenar un historial o programar operaciones puede complicar todavía más la clase.

### Problemas principales

1. **Acoplamiento:** el emisor conoce directamente al receptor que ejecuta la operación.
2. **Escalabilidad limitada:** añadir nuevas acciones puede obligar a modificar el controlador.
3. **Dificultad para reutilizar operaciones:** las acciones están ligadas al código que las invoca.
4. **Historial complicado:** no existe una representación independiente de cada acción que pueda almacenarse.
5. **Dificultad para deshacer operaciones:** el controlador debe conocer cómo revertir cada operación.
6. **Poca flexibilidad:** ejecutar acciones de forma diferida o encolada requiere introducir lógica adicional.

El patrón Command resuelve estos problemas encapsulando cada operación en un objeto comando.

---

## 3. Solución propuesta

El patrón Command introduce una abstracción que representa una acción.

El emisor (*Invoker*) no necesita conocer los detalles de la operación. Simplemente solicita la ejecución del comando.

El comando, por su parte, conoce el receptor (*Receiver*) que debe realizar el trabajo.

La estructura habitual está formada por:

- **Command:** interfaz que define la operación de ejecución.
- **ConcreteCommand:** implementación concreta de una acción.
- **Receiver:** objeto que contiene la lógica real de negocio o de infraestructura.
- **Invoker:** objeto que solicita la ejecución del comando.
- **Client:** objeto que configura los comandos y los conecta con sus receptores.

La operación suele exponerse mediante un método como `execute()`.

Por ejemplo, podemos tener comandos independientes para encender y apagar una luz:

```java
encenderLuz.execute();
apagarLuz.execute();
```

El controlador únicamente necesita conocer la interfaz `Command`, sin depender directamente de la clase `Luz`.

### Idea fundamental

**Una petición deja de ser una llamada directa y pasa a estar representada por un objeto.**

Esto permite tratar las operaciones como elementos independientes que pueden ejecutarse, almacenarse, combinarse o procesarse posteriormente.

---

## 4. Estructura del patrón

### 4.1. Command

Es la interfaz común que deben implementar los comandos concretos.

Normalmente define un método como `execute()`.

```java
public interface Command {
    void execute();
}
```

### 4.2. ConcreteCommand

Implementa una operación concreta y mantiene una referencia al receptor que debe ejecutarla.

Por ejemplo:

- `EncenderLuzCommand`
- `ApagarLuzCommand`
- `EncenderTelevisionCommand`

Cada comando encapsula una petición diferente.

### 4.3. Receiver

Es el objeto que realiza el trabajo real.

Puede ser un dispositivo, un servicio de negocio, un repositorio o cualquier objeto con la lógica necesaria para ejecutar la operación.

### 4.4. Invoker

Es el objeto que solicita la ejecución del comando.

Puede ser un botón, un controlador, una interfaz gráfica, una cola de tareas o un planificador.

El emisor no necesita conocer la implementación concreta del comando.

### 4.5. Client

Es el responsable de crear y configurar los comandos, asociándolos con los receptores correspondientes y entregándolos al emisor.

### Diagrama conceptual

```text
              Client
                 |
                 | configura
                 v
          +--------------+
          | ConcreteCommand |
          +--------------+
          | receiver      |
          | execute()     |
          +--------------+
                 |
                 | invoca
                 v
          +--------------+
          |   Receiver   |
          +--------------+
          | operacion()  |
          +--------------+

              Invoker
                 |
                 | solicita
                 v
          +--------------+
          |   Command    |
          +--------------+
          | execute()    |
          +--------------+
                 ^
                 |
          ConcreteCommand
```

La relación importante es que el `Invoker` depende de la abstracción `Command`, mientras que cada comando concreto conoce al receptor necesario para ejecutar su operación.

---

## 5. Implementación en Java

Vamos a implementar un control remoto que permita encender y apagar una luz utilizando el patrón Command.

### 5.1. Crear la interfaz Command

Definimos el contrato común de todos los comandos.

```java
public interface Command {

    void execute();
}
```

Todos los comandos deberán implementar el método `execute()`.

### 5.2. Crear el receptor

La clase `Luz` contiene la lógica real para encender y apagar el dispositivo.

```java
public class Luz {

    public void encender() {
        System.out.println("La luz está encendida");
    }

    public void apagar() {
        System.out.println("La luz está apagada");
    }
}
```

Esta clase no necesita conocer el patrón Command. Simplemente ofrece las operaciones que puede realizar.

### 5.3. Crear el comando para encender la luz

```java
public class EncenderLuzCommand implements Command {

    private final Luz luz;

    public EncenderLuzCommand(Luz luz) {
        this.luz = luz;
    }

    @Override
    public void execute() {
        luz.encender();
    }
}
```

El comando encapsula la petición de encender la luz.

Mantiene una referencia al receptor y delega en él la ejecución real.

### 5.4. Crear el comando para apagar la luz

```java
public class ApagarLuzCommand implements Command {

    private final Luz luz;

    public ApagarLuzCommand(Luz luz) {
        this.luz = luz;
    }

    @Override
    public void execute() {
        luz.apagar();
    }
}
```

Este segundo comando representa una operación diferente, pero implementa la misma interfaz.

Gracias a ello, ambos comandos pueden utilizarse de manera intercambiable desde el emisor.

### 5.5. Crear el emisor

El controlador remoto recibe un comando y solicita su ejecución.

```java
public class ControlRemoto {

    private Command comando;

    public void configurarComando(Command comando) {
        this.comando = comando;
    }

    public void pulsarBoton() {
        if (comando == null) {
            throw new IllegalStateException(
                "No hay ningún comando configurado"
            );
        }

        comando.execute();
    }
}
```

Observa que `ControlRemoto` no tiene ninguna referencia directa a `Luz`.

Tampoco necesita saber si el comando enciende una luz, apaga una televisión o abre una persiana.

Su única responsabilidad es solicitar la ejecución de un objeto que implemente `Command`.

### 5.6. Utilizar el patrón desde el cliente

```java
public class Main {

    public static void main(String[] args) {

        Luz luz = new Luz();

        Command encender =
            new EncenderLuzCommand(luz);

        Command apagar =
            new ApagarLuzCommand(luz);

        ControlRemoto control = new ControlRemoto();

        control.configurarComando(encender);
        control.pulsarBoton();

        control.configurarComando(apagar);
        control.pulsarBoton();
    }
}
```

Salida aproximada:

```text
La luz está encendida
La luz está apagada
```

### ¿Qué hemos conseguido?

- El controlador no conoce la clase `Luz`.
- Cada acción está encapsulada en su propio comando.
- Podemos incorporar nuevos comandos sin cambiar la lógica interna del controlador.
- El cliente puede decidir qué operación se ejecutará al pulsar el botón.

Este desacoplamiento es la principal ventaja del patrón.

---

## 6. Añadir nuevos comandos

Una de las ventajas de Command es que podemos ampliar el comportamiento de la aplicación creando nuevos comandos.

Por ejemplo, podemos añadir un comando para encender una televisión.

### 6.1. Crear el receptor Televisión

```java
public class Television {

    public void encender() {
        System.out.println("La televisión está encendida");
    }

    public void apagar() {
        System.out.println("La televisión está apagada");
    }
}
```

### 6.2. Crear el comando para encender la televisión

```java
public class EncenderTelevisionCommand implements Command {

    private final Television television;

    public EncenderTelevisionCommand(Television television) {
        this.television = television;
    }

    @Override
    public void execute() {
        television.encender();
    }
}
```

Ahora podemos utilizar el mismo controlador:

```java
Television television = new Television();

Command encenderTelevision =
    new EncenderTelevisionCommand(television);

control.configurarComando(encenderTelevision);
control.pulsarBoton();
```

No ha sido necesario modificar `ControlRemoto`.

La ampliación se ha realizado mediante una nueva implementación de la interfaz `Command`.

Este ejemplo ilustra el principio Open/Closed: podemos extender las funcionalidades sin modificar el emisor existente.

---

## 7. Implementar la operación deshacer (Undo)

Una de las aplicaciones más conocidas del patrón Command es permitir deshacer operaciones.

Para ello, podemos ampliar la interfaz y definir un método `undo()`.

```java
public interface Command {

    void execute();

    void undo();
}
```

Cada comando deberá implementar tanto la acción original como su operación inversa.

### 7.1. Comando reversible para encender la luz

```java
public class EncenderLuzCommand implements Command {

    private final Luz luz;

    public EncenderLuzCommand(Luz luz) {
        this.luz = luz;
    }

    @Override
    public void execute() {
        luz.encender();
    }

    @Override
    public void undo() {
        luz.apagar();
    }
}
```

### 7.2. Comando reversible para apagar la luz

```java
public class ApagarLuzCommand implements Command {

    private final Luz luz;

    public ApagarLuzCommand(Luz luz) {
        this.luz = luz;
    }

    @Override
    public void execute() {
        luz.apagar();
    }

    @Override
    public void undo() {
        luz.encender();
    }
}
```

### 7.3. Crear un historial de acciones

El emisor puede guardar el último comando ejecutado para permitir deshacerlo.

```java
public class ControlRemoto {

    private Command comando;
    private Command ultimoComando;

    public void configurarComando(Command comando) {
        this.comando = comando;
    }

    public void pulsarBoton() {
        if (comando == null) {
            throw new IllegalStateException(
                "No hay ningún comando configurado"
            );
        }

        comando.execute();
        ultimoComando = comando;
    }

    public void deshacer() {
        if (ultimoComando != null) {
            ultimoComando.undo();
            ultimoComando = null;
        }
    }
}
```

En este ejemplo se almacena únicamente el último comando ejecutado. Por tanto, permite deshacer una sola acción.

Si quisiéramos implementar un historial completo, podríamos utilizar una pila de comandos ejecutados.

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class HistorialComandos {

    private final Deque<Command> historial =
        new ArrayDeque<>();

    public void ejecutar(Command comando) {
        comando.execute();
        historial.push(comando);
    }

    public void deshacer() {
        if (!historial.isEmpty()) {
            Command comando = historial.pop();
            comando.undo();
        }
    }
}
```

Esta implementación permite deshacer las acciones en orden inverso al de ejecución, siempre que los comandos implementen correctamente sus operaciones inversas.

**Importante:** deshacer una operación no siempre consiste en ejecutar su opuesta. En aplicaciones reales, puede ser necesario guardar el estado anterior, gestionar errores o compensar operaciones que afectan a sistemas externos.

---

## 8. Ejecutar comandos encolados o diferidos

Otra ventaja de Command es que las operaciones pueden representarse como objetos y ejecutarse posteriormente.

Esto resulta útil en:

- Colas de tareas.
- Sistemas de procesamiento en segundo plano.
- Planificadores de operaciones.
- Automatizaciones.
- Sistemas de mensajería.
- Procesos que deben ejecutarse por lotes.

Por ejemplo, podemos almacenar varios comandos y ejecutarlos más adelante.

```java
import java.util.ArrayDeque;
import java.util.Queue;

public class ColaComandos {

    private final Queue<Command> comandos =
        new ArrayDeque<>();

    public void agregar(Command comando) {
        comandos.offer(comando);
    }

    public void ejecutarPendientes() {
        while (!comandos.isEmpty()) {
            Command comando = comandos.poll();
            comando.execute();
        }
    }
}
```

Uso:

```java
ColaComandos cola = new ColaComandos();

cola.agregar(new EncenderLuzCommand(luz));
cola.agregar(new ApagarLuzCommand(luz));

cola.ejecutarPendientes();
```

Las acciones se guardan primero y se ejecutan cuando se llama a `ejecutarPendientes()`.

Esta versión es síncrona: los comandos se ejecutan secuencialmente en el mismo hilo. Para convertirla en una cola asíncrona o distribuida sería necesario incorporar mecanismos adicionales de concurrencia o mensajería.

---

## 9. Comandos compuestos (MacroCommand)

Command también permite combinar varios comandos en una única operación.

Por ejemplo, una escena de domótica podría encender la luz, encender la televisión y bajar las persianas.

Podemos crear un comando compuesto que contenga una lista de comandos.

```java
import java.util.List;

public class MacroCommand implements Command {

    private final List<Command> comandos;

    public MacroCommand(List<Command> comandos) {
        this.comandos = List.copyOf(comandos);
    }

    @Override
    public void execute() {
        for (Command comando : comandos) {
            comando.execute();
        }
    }

    @Override
    public void undo() {
        for (int i = comandos.size() - 1; i >= 0; i--) {
            comandos.get(i).undo();
        }
    }
}
```

Ejemplo de uso:

```java
Command escenaNoche = new MacroCommand(
    List.of(
        new EncenderLuzCommand(luz),
        new EncenderTelevisionCommand(television)
    )
);

escenaNoche.execute();
```

En este ejemplo, la macro ejecuta los comandos en el orden definido y los deshace en orden inverso.

Este enfoque es apropiado cuando las operaciones deben tratarse como una acción lógica compuesta.

Hay que tener en cuenta que, si un comando falla a mitad de la ejecución, esta implementación no revierte automáticamente los comandos anteriores. Para conseguirlo habría que definir una política de errores y compensación.

---

## 10. Command en aplicaciones empresariales

El patrón no está limitado a controles remotos o interfaces gráficas. También puede ser útil en arquitecturas de aplicaciones empresariales.

Por ejemplo, una aplicación de pedidos podría representar la creación de un pedido mediante un comando:

```java
public record CrearPedidoCommand(
    Long clienteId,
    java.util.List<String> productos
) {
}
```

Este objeto representa una petición de creación de pedido y transporta los datos necesarios para procesarla.

Un servicio de aplicación podría recibirlo:

```java
public class CrearPedidoHandler {

    public void handle(CrearPedidoCommand command) {
        // Validar los datos.
        // Crear el pedido.
        // Persistirlo mediante un repositorio.
        // Publicar los eventos necesarios.
    }
}
```

En este contexto, `CrearPedidoCommand` representa una intención de negocio y `CrearPedidoHandler` es el componente que la procesa.

Esta organización es frecuente en arquitecturas basadas en comandos y en implementaciones de CQRS (*Command Query Responsibility Segregation*).

Sin embargo, hay una distinción importante: **utilizar objetos que representan comandos no implica implementar automáticamente CQRS**. CQRS requiere separar conceptualmente las operaciones que modifican el estado de las consultas que devuelven información.

Además, una aplicación puede utilizar Command sin necesidad de adoptar CQRS.

---

## 11. Ventajas del patrón Command

### 11.1. Bajo acoplamiento

El emisor depende de la interfaz `Command`, no de las clases concretas que realizan las operaciones.

### 11.2. Extensibilidad

Es posible añadir nuevos comandos sin modificar la lógica del emisor, siempre que la nueva acción pueda representarse mediante la interfaz existente.

### 11.3. Historial de operaciones

Los comandos pueden almacenarse para registrar las acciones ejecutadas o construir un historial.

### 11.4. Operaciones reversibles

Si una acción dispone de una operación inversa o de información suficiente para restaurar el estado anterior, se puede implementar `undo()`.

### 11.5. Ejecución diferida

Los comandos pueden guardarse y ejecutarse posteriormente, por ejemplo, desde una cola de tareas.

### 11.6. Composición

Es posible agrupar varios comandos para construir operaciones más complejas.

### 11.7. Facilidad de pruebas

Los comandos pueden probarse individualmente y los emisores pueden verificarse utilizando implementaciones simuladas de `Command`.

---

## 12. Desventajas del patrón Command

### 12.1. Mayor número de clases

Cada operación puede requerir una clase de comando concreta. En sistemas con muchas operaciones sencillas, esto puede aumentar la cantidad de código.

### 12.2. Complejidad adicional

Introducir comandos para todas las llamadas puede ser innecesario cuando no existe una necesidad real de desacoplamiento, historial, ejecución diferida o composición.

### 12.3. Gestión del estado

Para implementar correctamente `undo()`, puede ser necesario almacenar el estado previo a la ejecución de cada operación.

### 12.4. Tratamiento de errores

Si los comandos se ejecutan en cola o como parte de una macro, es necesario definir qué ocurre cuando una operación falla.

### 12.5. Persistencia y serialización

Guardar comandos para ejecutarlos después puede exigir persistir sus parámetros y definir cómo reconstruirlos. No siempre es apropiado serializar directamente objetos Java arbitrarios.

### 12.6. Reintentos y efectos secundarios

Repetir un comando puede producir efectos duplicados si la operación no es idempotente. En sistemas distribuidos, los reintentos deben diseñarse teniendo en cuenta esta posibilidad.

---

## 13. Casos de uso reales

### 13.1. Interfaces gráficas

Cada botón o elemento de menú puede estar asociado a un comando que representa la acción correspondiente.

Ejemplos:

- Guardar un documento.
- Copiar y pegar texto.
- Eliminar un registro.
- Exportar información.

### 13.2. Editores de texto

Command permite representar las operaciones realizadas por el usuario y mantener un historial para implementar deshacer y rehacer.

### 13.3. Sistemas de domótica

Los comandos pueden controlar luces, persianas, calefacción y otros dispositivos mediante una interfaz común.

### 13.4. Procesamiento en segundo plano

Las tareas pueden encapsularse y enviarse a una cola para ejecutarse posteriormente.

### 13.5. Sistemas de pedidos

Las operaciones de crear, cancelar o modificar pedidos pueden representarse mediante comandos de aplicación.

### 13.6. Sistemas administrativos

Acciones como aprobar facturas, cerrar incidencias o generar informes pueden encapsularse como operaciones independientes.

### 13.7. Automatización

Es posible construir flujos de trabajo compuestos por una secuencia de comandos configurables.

---

## 14. Relación con los principios SOLID

### Single Responsibility Principle (SRP)

Cada comando representa una operación concreta y encapsula la lógica necesaria para solicitarla.

El emisor se limita a invocar el comando, mientras que el receptor realiza el trabajo correspondiente.

Esto ayuda a separar responsabilidades.

### Open/Closed Principle (OCP)

Podemos incorporar nuevas operaciones creando nuevas implementaciones de `Command` sin modificar necesariamente el emisor.

Por ejemplo, añadir `GuardarDocumentoCommand` no exige cambiar un controlador que ya trabaja con la interfaz `Command`.

### Liskov Substitution Principle (LSP)

Todas las implementaciones de `Command` deben respetar el contrato definido por la interfaz.

El emisor debe poder utilizar cualquier comando válido sin depender de detalles particulares de su implementación.

### Interface Segregation Principle (ISP)

El emisor depende de una interfaz pequeña, con la operación que necesita para ejecutar una petición.

No necesita conocer todos los métodos del receptor.

Si algunas operaciones requieren capacidades especiales, como deshacer, puede ser preferible definir una abstracción adicional en lugar de obligar a todos los comandos a implementarlas artificialmente.

### Dependency Inversion Principle (DIP)

El emisor depende de la abstracción `Command`, no de clases concretas como `EncenderLuzCommand`.

Esto permite cambiar la acción configurada sin modificar la lógica interna del emisor.

---

## 15. Comparación con otros patrones de diseño

### 15.1. Command vs. Strategy

| Command | Strategy |
|---|---|
| Encapsula una petición como un objeto. | Encapsula un algoritmo o una forma de realizar una tarea. |
| Se centra en representar y solicitar una acción. | Se centra en seleccionar entre algoritmos alternativos. |
| Puede facilitar el historial, la ejecución diferida y `undo()`. | Facilita intercambiar comportamientos algorítmicos. |
| Puede almacenar una referencia al receptor y los datos necesarios para la operación. | Normalmente proporciona una implementación intercambiable de un algoritmo. |

**Ejemplo:** `EncenderLuzCommand` representa la acción de encender una luz. En cambio, una estrategia de cálculo de impuestos representa una forma alternativa de calcular un impuesto.

### 15.2. Command vs. Observer

| Command | Observer |
|---|---|
| Representa una acción que se solicita ejecutar. | Define un mecanismo para notificar cambios a varios observadores. |
| Normalmente existe una petición explícita de ejecución. | Las notificaciones se producen cuando ocurre un evento o cambio relevante. |
| Es útil para acciones, colas e historiales. | Es útil para notificaciones y sistemas reactivos. |

**Ejemplo:** pulsar un botón para guardar un documento puede ejecutar un comando. Notificar a varias vistas que el documento ha cambiado puede resolverse mediante Observer.

### 15.3. Command vs. State

| Command | State |
|---|---|
| Encapsula una acción o petición. | Encapsula el comportamiento asociado al estado actual de un objeto. |
| Se centra en qué operación debe ejecutarse. | Se centra en cómo cambia el comportamiento según el estado. |
| Puede ejecutarse independientemente del estado del emisor, según las reglas de la aplicación. | El comportamiento depende del estado representado por el objeto. |

**Ejemplo:** `CerrarSesionCommand` representa una petición de cierre de sesión. Un objeto que utiliza State podría comportarse de forma distinta si la sesión está activa, cerrada o bloqueada.

### 15.4. Command vs. Chain of Responsibility

| Command | Chain of Responsibility |
|---|---|
| Encapsula una petición en un objeto. | Permite pasar una petición a través de una cadena de posibles manejadores. |
| El emisor solicita ejecutar el comando configurado. | La petición puede ser procesada por un manejador de la cadena. |
| No requiere una cadena de decisión entre manejadores. | Los manejadores pueden procesar o reenviar la petición. |

Ambos patrones pueden combinarse: un comando puede iniciar un proceso que después se gestione mediante una cadena de validadores o manejadores.

### 15.5. Command vs. Factory Method

| Command | Factory Method |
|---|---|
| Es un patrón de comportamiento. | Es un patrón creacional. |
| Representa una operación que puede ejecutarse. | Define un mecanismo para delegar la creación de objetos. |
| Se centra en encapsular peticiones. | Se centra en la creación de productos. |

Una fábrica puede utilizarse para crear comandos concretos, pero sus responsabilidades son distintas.

---

## 16. Buenas prácticas

- Mantener cada comando enfocado en una operación concreta y coherente.
- Hacer que el emisor dependa de la interfaz `Command`.
- Evitar introducir clases de comando innecesarias para operaciones triviales que no se benefician de esta abstracción.
- Separar el comando que representa la petición del servicio que ejecuta la lógica de negocio cuando resulte conveniente.
- Implementar `undo()` solo cuando la operación pueda revertirse de forma definida.
- Almacenar suficiente información para restaurar el estado anterior cuando sea necesario.
- Definir una política clara de errores para las macros y las colas de comandos.
- Diseñar los reintentos teniendo en cuenta la idempotencia y los efectos secundarios.
- Si los comandos se persisten, definir un formato estable para sus datos y una estrategia de versionado.
- Probar individualmente los comandos y verificar que delegan correctamente en sus receptores.
- No confundir el patrón Command con CQRS: pueden utilizarse juntos, pero son conceptos diferentes.

---

## 17. Conclusión

El patrón Command es un patrón de comportamiento que permite encapsular una petición dentro de un objeto independiente.

Su principal ventaja es desacoplar al objeto que solicita una operación del objeto que la ejecuta. Esto permite añadir nuevas acciones, almacenarlas, ejecutarlas posteriormente, combinarlas y, cuando es posible, deshacerlas.

Es especialmente útil en interfaces gráficas, sistemas de automatización, colas de tareas, historiales de operaciones y aplicaciones empresariales.

En Java, puede implementarse mediante una interfaz sencilla como `Command`, clases concretas que representan las acciones y un emisor que invoca el método `execute()`.

Command encapsula qué acción se quiere ejecutar en un objeto independiente. Esto permite desacoplar al emisor del receptor y facilita funcionalidades como el historial, la ejecución diferida, las macros y el deshacer.

En definitiva, **Command convierte una operación en un objeto que puede gestionarse de forma independiente a quien la solicita y a quien la ejecuta**.

---

## 18. Referencias

- *Design Patterns: Elements of Reusable Object-Oriented Software*, Erich Gamma, Richard Helm, Ralph Johnson y John Vlissides.
- Refactoring.Guru — Command: https://refactoring.guru/design-patterns/command
- Documentación oficial de Java — Interfaces: https://docs.oracle.com/javase/tutorial/java/IandI/createinterface.html
- Documentación oficial de Java — `Deque`: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Deque.html
- Documentación oficial de Java — `Queue`: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Queue.html
