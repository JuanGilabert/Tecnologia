# Patrón de Diseño Observer (Comportamiento)

## 1. Introducción

El patrón de diseño **Observer (Observador)** pertenece a la categoría de los patrones de comportamiento. Su objetivo es establecer una relación de dependencia entre un objeto, denominado *Subject* o sujeto, y varios objetos observadores, de manera que cuando el sujeto cambia de estado, sus observadores reciben una notificación.

Este patrón permite implementar un mecanismo de comunicación entre componentes sin que el sujeto tenga que conocer los detalles internos de cada observador.

Observer resulta especialmente útil cuando un cambio producido en un componente debe propagarse a otros componentes interesados en ese cambio.

Algunos ejemplos habituales son:

- Sistemas de notificaciones.
- Interfaces gráficas que reaccionan a cambios de estado.
- Sistemas de publicación y suscripción de eventos.
- Actualización de vistas cuando cambia un modelo.
- Monitorización de métricas y procesos.
- Aplicaciones que reaccionan a cambios en inventarios, pedidos o cuentas de usuario.

La idea principal es **desacoplar al componente que produce un cambio de los componentes que necesitan reaccionar ante él**.

## 2. Problema que resuelve

Imaginemos una aplicación de comercio electrónico en la que, cuando cambia el estado de un pedido, es necesario realizar varias acciones:

- Enviar un correo electrónico al cliente.
- Actualizar el panel de administración.
- Registrar el cambio en un sistema de auditoría.
- Enviar una notificación a una aplicación móvil.

Una implementación directa podría incluir todas estas operaciones dentro de la clase que gestiona el pedido.

```java
public class PedidoService {

    public void confirmarPedido() {
        // Confirmar pedido

        // Enviar correo electrónico
        // Actualizar panel de administración
        // Registrar auditoría
        // Enviar notificación móvil
    }
}
```

Este enfoque presenta varios inconvenientes:

- **Acoplamiento elevado:** el servicio conoce todos los componentes que reaccionan al cambio.
- **Dificultad de extensión:** incorporar una nueva reacción obliga a modificar la lógica existente.
- **Responsabilidades mezcladas:** la gestión del pedido se combina con el envío de notificaciones y otras tareas.
- **Reutilización limitada:** las reacciones están vinculadas a una implementación concreta.
- **Pruebas más complejas:** es necesario controlar varias dependencias para probar una operación aparentemente sencilla.

Observer permite resolver estos problemas mediante una relación de suscripción: los componentes interesados se registran como observadores y reciben notificaciones cuando ocurre el evento correspondiente.

## 3. Solución propuesta

El patrón introduce dos roles principales:

- **Subject (sujeto):** mantiene una colección de observadores y les notifica los cambios relevantes.
- **Observer (observador):** define el contrato que deben implementar los componentes interesados en recibir notificaciones.

Cuando cambia el estado del sujeto, este recorre sus observadores registrados y ejecuta el método de notificación definido por la abstracción.

Por ejemplo:

```java
pedido.registrarObservador(new EmailObserver());
pedido.registrarObservador(new AuditoriaObserver());

pedido.confirmar();
```

Cuando se confirma el pedido, los observadores registrados reciben la notificación correspondiente.

El sujeto no necesita conocer las clases concretas de los observadores. Solo necesita conocer el contrato que deben cumplir.

## 4. Estructura del patrón

Los participantes habituales de Observer son:

- **Subject:** mantiene la lista de observadores y proporciona operaciones para registrarlos, eliminarlos y notificarlos.
- **Concrete Subject:** representa el objeto cuyo estado o eventos interesan a otros componentes.
- **Observer:** define la interfaz de notificación.
- **Concrete Observer:** implementa la reacción ante los cambios del sujeto.
- **Cliente:** registra los observadores y configura la relación entre los componentes.

### Diagrama conceptual

```text
                  Cliente
                     |
             registra observadores
                     |
                     v
              Concrete Subject
                     |
             +-------+-------+
             |       |       |
             v       v       v
         Observer Observer Observer
             |       |       |
             v       v       v
          Email   Auditoría  Panel
```

El sujeto mantiene referencias a las abstracciones de los observadores, no necesariamente a sus implementaciones concretas.

De esta forma, es posible incorporar nuevos observadores sin modificar el sujeto, siempre que respeten el contrato establecido.

## 5. Ejemplo de implementación en Java

Imaginemos un sistema de pedidos que debe notificar diferentes componentes cuando cambia su estado.

### 5.1. Definición de la interfaz Observer

Primero se define una interfaz común para todos los observadores.

```java
public interface PedidoObserver {

    void actualizar(String estado);
}
```

Cualquier clase que implemente `PedidoObserver` podrá recibir notificaciones sobre el estado del pedido.

### 5.2. Implementación de observadores concretos

Cada observador define qué acción debe realizar cuando recibe una notificación.

**Observador de correo electrónico:**

```java
public class EmailObserver implements PedidoObserver {

    @Override
    public void actualizar(String estado) {
        System.out.println(
            "Email enviado. Nuevo estado del pedido: "
                + estado
        );
    }
}
```

**Observador de auditoría:**

```java
public class AuditoriaObserver implements PedidoObserver {

    @Override
    public void actualizar(String estado) {
        System.out.println(
            "Auditoría registrada. Estado: " + estado
        );
    }
}
```

**Observador del panel de administración:**

```java
public class PanelObserver implements PedidoObserver {

    @Override
    public void actualizar(String estado) {
        System.out.println(
            "Panel actualizado. Estado: " + estado
        );
    }
}
```

Cada observador implementa el mismo contrato, pero reacciona de manera diferente.

### 5.3. Implementación del Subject

La clase `Pedido` mantiene una lista de observadores y los notifica cuando cambia su estado.

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Objects;

public class Pedido {

    private final List<PedidoObserver> observadores =
        new ArrayList<>();

    private String estado = "PENDIENTE";

    public void registrarObservador(PedidoObserver observer) {
        observadores.add(
            Objects.requireNonNull(observer)
        );
    }

    public void eliminarObservador(PedidoObserver observer) {
        observadores.remove(observer);
    }

    public void cambiarEstado(String nuevoEstado) {
        this.estado = Objects.requireNonNull(nuevoEstado);
        notificarObservadores();
    }

    public void confirmar() {
        cambiarEstado("CONFIRMADO");
    }

    private void notificarObservadores() {
        for (PedidoObserver observer :
                List.copyOf(observadores)) {
            observer.actualizar(estado);
        }
    }

    public String getEstado() {
        return estado;
    }
}
```

En esta implementación:

- `registrarObservador()` incorpora un observador.
- `eliminarObservador()` permite dejar de recibir notificaciones.
- `cambiarEstado()` modifica el estado del pedido y notifica el cambio.
- `List.copyOf()` crea una instantánea de la lista para recorrerla sin que las modificaciones a la colección original durante una notificación provoquen una modificación estructural de la iteración.

Esta solución es adecuada como ejemplo básico, pero no hace que toda la clase sea automáticamente segura para concurrencia. Si varios hilos modifican el pedido o registran observadores al mismo tiempo, será necesario definir las garantías de sincronización.

### 5.4. Uso desde el cliente

El cliente registra los observadores que deben reaccionar a los cambios.

```java
public class Main {

    public static void main(String[] args) {

        Pedido pedido = new Pedido();

        pedido.registrarObservador(new EmailObserver());
        pedido.registrarObservador(new AuditoriaObserver());
        pedido.registrarObservador(new PanelObserver());

        pedido.confirmar();

        System.out.println(
            "Estado final: " + pedido.getEstado()
        );
    }
}
```

Una posible salida sería:

```text
Email enviado. Nuevo estado del pedido: CONFIRMADO
Auditoría registrada. Estado: CONFIRMADO
Panel actualizado. Estado: CONFIRMADO
Estado final: CONFIRMADO
```

### 5.5. Explicación del ejemplo

1. El cliente crea un pedido.
2. Registra los observadores que están interesados en los cambios de estado.
3. El pedido cambia de estado mediante `confirmar()`.
4. El sujeto notifica a los observadores registrados.
5. Cada observador ejecuta su propia lógica.
6. El sujeto no necesita conocer los detalles de las operaciones realizadas por los observadores.

Si en el futuro se añade un observador para enviar notificaciones móviles, puede incorporarse una nueva clase que implemente `PedidoObserver` sin modificar la lógica de notificación de `Pedido`.

## 6. Variante: notificaciones mediante eventos

En algunas aplicaciones resulta útil que los observadores reciban algo más que el nuevo estado. Por ejemplo, pueden necesitar el estado anterior, el estado nuevo, la fecha del cambio o el identificador del pedido.

Para ello, se puede utilizar un objeto de evento.

### 6.1. Definición del evento

```java
public record PedidoEvento(
    String estadoAnterior,
    String estadoNuevo
) {
}
```

El evento representa la información asociada a un cambio de estado.

### 6.2. Interfaz del observador

```java
public interface PedidoObserver {

    void actualizar(PedidoEvento evento);
}
```

### 6.3. Notificación del cambio

Dentro del sujeto, el cambio puede notificarse de la siguiente manera:

```java
public void cambiarEstado(String nuevoEstado) {

    String estadoAnterior = this.estado;
    this.estado = nuevoEstado;

    PedidoEvento evento = new PedidoEvento(
        estadoAnterior,
        nuevoEstado
    );

    for (PedidoObserver observer :
            List.copyOf(observadores)) {
        observer.actualizar(evento);
    }
}
```

Ahora los observadores pueden reaccionar en función del cambio concreto, en lugar de recibir únicamente el estado final.

Por ejemplo, un observador podría enviar una notificación solo cuando el pedido pase de `PENDIENTE` a `CONFIRMADO`.

Esta variante resulta especialmente útil cuando los eventos tienen significado propio dentro del dominio de la aplicación.

## 7. Observer y el mecanismo de publicación-suscripción

Observer y el patrón *Publish-Subscribe* están relacionados, pero no son exactamente equivalentes.

### Observer

En su forma clásica:

- El sujeto mantiene referencias a sus observadores.
- Los observadores se registran directamente en el sujeto.
- Las notificaciones suelen realizarse dentro del mismo proceso.
- La relación entre productor y observadores es relativamente directa.

### Publish-Subscribe

En un sistema de publicación-suscripción:

- El productor publica eventos en un canal o intermediario.
- Los consumidores se suscriben a temas o tipos de eventos.
- El productor normalmente no necesita conocer a los consumidores.
- El sistema puede utilizar un intermediario como un *message broker*.
- Es posible distribuir eventos entre procesos o servicios diferentes.

| Característica | Observer | Publish-Subscribe |
|---|---|---|
| Comunicación | Directa entre sujeto y observadores. | Mediada habitualmente por un canal o intermediario. |
| Referencias | El sujeto suele mantener referencias a los observadores. | El productor puede desconocer a los suscriptores. |
| Distribución | Normalmente dentro de una aplicación o proceso. | Puede extenderse a múltiples procesos y servicios. |
| Complejidad | Generalmente baja o moderada. | Depende del mecanismo de mensajería y su infraestructura. |
| Ejemplo | Actualizar varias vistas cuando cambia un modelo. | Publicar un evento de pedido para que varios microservicios lo consuman. |

La elección depende de las necesidades de comunicación, distribución, persistencia y tolerancia a fallos de la aplicación.

## 8. Ventajas

- **Desacoplamiento:** el sujeto no necesita conocer la implementación concreta de sus observadores.
- **Extensibilidad:** pueden añadirse nuevos observadores sin modificar el sujeto.
- **Separación de responsabilidades:** cada observador concentra una reacción específica.
- **Reutilización:** los observadores pueden reutilizarse en otros contextos compatibles.
- **Comunicación dinámica:** es posible registrar o eliminar observadores durante la ejecución.
- **Facilidad de mantenimiento:** la lógica de cada reacción está separada de la lógica del sujeto.
- **Compatibilidad con arquitecturas orientadas a eventos:** puede servir como base para mecanismos de notificación más avanzados.

## 9. Desventajas

- **Orden de ejecución:** en una implementación síncrona, las notificaciones suelen ejecutarse según el orden de recorrido de la colección, lo que puede crear dependencias no deseadas.
- **Errores en cascada:** una excepción en un observador puede impedir que se notifique a los siguientes si no se gestiona adecuadamente.
- **Problemas de rendimiento:** muchos observadores o tareas costosas pueden ralentizar la operación que produce el evento.
- **Gestión de suscripciones:** olvidar eliminar observadores puede mantener referencias innecesarias y provocar fugas de memoria.
- **Dificultad de depuración:** el flujo de ejecución puede resultar menos evidente porque una acción desencadena otras de forma indirecta.
- **Concurrencia:** los cambios de estado y las suscripciones simultáneas requieren una estrategia de sincronización.
- **Notificaciones repetidas:** si no se define cuándo se emite un evento, un mismo cambio puede generar notificaciones innecesarias.

Además, Observer no garantiza entrega duradera, reintentos, orden global ni comunicación entre procesos. Si esos requisitos son necesarios, puede ser apropiado utilizar una infraestructura de mensajería especializada.

## 10. Cuándo utilizar Observer

Es recomendable utilizar este patrón cuando:

- Un objeto debe notificar cambios a varios componentes.
- No se desea acoplar el productor a todas las reacciones posibles.
- Los componentes interesados pueden variar durante la ejecución.
- Una modificación de estado debe actualizar diferentes partes de una aplicación.
- Se quiere separar la lógica principal de los efectos secundarios.
- Los componentes necesitan reaccionar a eventos sin formar una cadena rígida de dependencias.

### Cuándo no utilizarlo

Puede ser innecesario cuando:

- Solo existe un consumidor y la llamada directa es suficientemente clara.
- Las dependencias entre componentes son sencillas y estables.
- El flujo de ejecución debe ser estrictamente secuencial y explícito.
- Se necesitan garantías de entrega, persistencia o reintentos que una implementación básica no ofrece.
- El número de observadores y los efectos secundarios hacen difícil controlar el comportamiento del sistema.

## 11. Ejemplos de uso en aplicaciones reales

### 11.1. Interfaces gráficas

Cuando cambia un modelo, diferentes componentes visuales pueden actualizarse para reflejar el nuevo estado.

Por ejemplo, un contador, una tabla y un gráfico pueden reaccionar al mismo evento.

### 11.2. Sistemas de pedidos

Cuando un pedido cambia de estado, pueden activarse notificaciones, registros de auditoría y actualizaciones de interfaz.

### 11.3. Monitorización

Un componente que detecta cambios en una métrica puede notificar a paneles, registradores o mecanismos de alerta.

### 11.4. Sistemas financieros

Cuando cambia el saldo de una cuenta, diferentes componentes pueden reaccionar para actualizar una interfaz o recalcular indicadores, siempre que se definan correctamente las garantías de consistencia.

### 11.5. Aplicaciones reactivas

Los mecanismos de suscripción permiten que los consumidores reaccionen ante valores o eventos emitidos por otros componentes.

En Java, bibliotecas reactivas y mecanismos de eventos proporcionan abstracciones más avanzadas que pueden cubrir necesidades similares, especialmente cuando intervienen asincronía, contrapresión o flujos continuos de datos.

## 12. Relación con los principios SOLID

Observer puede contribuir a un diseño modular cuando se utiliza correctamente.

### Single Responsibility Principle (SRP)

Cada observador puede concentrarse en una única reacción, como registrar una auditoría o actualizar una vista.

### Open/Closed Principle (OCP)

Es posible incorporar nuevos observadores sin modificar el sujeto, siempre que estos implementen el contrato existente y no sea necesario cambiar la semántica de los eventos.

### Dependency Inversion Principle (DIP)

El sujeto puede depender de una abstracción como `PedidoObserver`, en lugar de depender de clases concretas como `EmailObserver` o `AuditoriaObserver`.

### Interface Segregation Principle (ISP)

La interfaz del observador debería ser lo suficientemente específica para que sus implementaciones no se vean obligadas a definir operaciones que no necesitan.

Por ejemplo, si existen grupos de eventos con propósitos muy diferentes, puede ser preferible definir contratos separados en lugar de crear una interfaz excesivamente amplia.

## 13. Buenas prácticas

1. **Definir claramente los eventos:** documentar qué significa cada notificación y cuándo se emite.
2. **Mantener pequeño el contrato del observador:** exponer únicamente los métodos y datos necesarios.
3. **Gestionar las suscripciones:** ofrecer mecanismos claros para registrar y eliminar observadores.
4. **Evitar dependencias circulares:** no permitir que el sujeto y los observadores dependan mutuamente de forma innecesaria.
5. **Controlar las excepciones:** decidir si un fallo debe detener la notificación, registrarse o gestionarse de manera aislada.
6. **Definir la estrategia de concurrencia:** especificar si las notificaciones son síncronas o asíncronas y cómo se protegen los datos compartidos.
7. **Evitar trabajo excesivo dentro del sujeto:** las operaciones costosas pueden delegarse a mecanismos asíncronos si el caso de uso lo permite.
8. **Evitar fugas de memoria:** eliminar las suscripciones cuando el observador ya no deba recibir eventos.
9. **Evitar notificaciones innecesarias:** definir si deben emitirse eventos cuando el estado realmente cambia.
10. **No asumir garantías que no existen:** una lista de observadores en memoria no proporciona persistencia ni entrega garantizada.
11. **Utilizar eventos con significado de dominio:** cuando corresponda, representar los cambios mediante objetos explícitos, en lugar de utilizar cadenas genéricas.

## 14. Comparación con otros patrones de comportamiento

| Patrón | Objetivo principal | Diferencia respecto a Observer |
|---|---|---|
| Strategy | Encapsular algoritmos intercambiables. | Permite seleccionar cómo ejecutar una operación; Observer propaga notificaciones de cambios. |
| Command | Encapsular una solicitud como un objeto. | Representa una acción que puede ejecutarse, registrarse o encolarse. |
| Mediator | Centralizar la comunicación entre varios componentes. | Introduce un intermediario que coordina interacciones. |
| Chain of Responsibility | Pasar una solicitud entre posibles manejadores. | Los manejadores procesan o reenvían una solicitud en una cadena. |
| State | Cambiar el comportamiento de un objeto según su estado interno. | Organiza el comportamiento dependiente del estado; Observer notifica a otros componentes. |
| Observer | Notificar cambios a múltiples suscriptores. | Se centra en propagar cambios sin acoplar directamente al productor con todos los consumidores. |

## 15. Conclusión

Observer es un patrón de comportamiento que permite establecer una relación de suscripción entre un objeto y varios componentes interesados en sus cambios.

Su principal aportación consiste en separar la lógica que produce un evento de las reacciones que se desencadenan a partir de él. Esto mejora el desacoplamiento y facilita incorporar nuevos comportamientos sin modificar constantemente el componente principal.

Resulta especialmente útil en interfaces gráficas, sistemas de notificaciones y aplicaciones orientadas a eventos. Sin embargo, es importante definir adecuadamente la gestión de suscripciones, las excepciones, el rendimiento y la concurrencia.

Observer encaja especialmente bien con Factory y Singleton como contraste: Factory controla cómo se crean los objetos, Singleton controla la unicidad de una instancia y Observer define cómo se notifican los cambios entre componentes.

En definitiva, **Observer permite que distintos componentes reaccionen a los cambios de un objeto de forma flexible y desacoplada**, siempre que las reglas de notificación y el ciclo de vida de los observadores estén bien definidos.

## 16. Referencias

- [Refactoring Guru — Observer](https://refactoring.guru/design-patterns/observer)
- [SourceMaking — Observer Design Pattern](https://sourcemaking.com/design_patterns/observer)
- [Java Documentation — Observer Pattern Overview](https://docs.oracle.com/javase/tutorial/uiswing/events/)
- [Reactive Streams](https://www.reactive-streams.org/)
