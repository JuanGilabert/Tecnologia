# Patrón de Diseño Mediator (Mediador)

## 1. Introducción

El patrón de diseño **Mediator** pertenece a la categoría de los patrones de comportamiento (*Behavioral Design Patterns*) definidos por GoF (*Gang of Four*).

Su objetivo es **centralizar la comunicación entre un conjunto de objetos mediante un mediador**, evitando que estos tengan que conocerse y comunicarse directamente entre sí.

En sistemas con muchos componentes relacionados, las dependencias entre objetos pueden crecer rápidamente. Cada componente puede necesitar conocer a varios de los demás para coordinar sus acciones, lo que aumenta el acoplamiento y dificulta el mantenimiento.

Mediator introduce un objeto intermediario que coordina las interacciones entre los componentes.

### Objetivos principales

- Reducir las dependencias directas entre objetos.
- Centralizar la lógica de coordinación.
- Facilitar la incorporación y modificación de componentes.
- Evitar que las reglas de comunicación estén distribuidas por todo el sistema.
- Mejorar la mantenibilidad y la legibilidad del código.
- Permitir que los componentes se comuniquen sin conocer los detalles internos de los demás.

---

## 2. Problema que resuelve

Imaginemos que estamos desarrollando una aplicación de videoconferencias. En una reunión participan varios usuarios y necesitamos gestionar distintas interacciones:

- Cuando un usuario envía un mensaje, los demás deben recibirlo.
- Cuando un usuario desactiva su micrófono, la interfaz debe reflejarlo.
- Cuando un usuario abandona la reunión, hay que actualizar la lista de participantes.
- Cuando el moderador bloquea el chat, los mensajes deben dejar de enviarse.

Una solución inicial podría hacer que cada participante mantuviera referencias a todos los demás participantes.

```java
public class Usuario {

    private Usuario usuario1;
    private Usuario usuario2;
    private Usuario usuario3;

    public void enviarMensaje(String mensaje) {
        usuario1.recibirMensaje(mensaje);
        usuario2.recibirMensaje(mensaje);
        usuario3.recibirMensaje(mensaje);
    }
}
```

Este ejemplo está simplificado, pero ilustra el problema: el usuario conoce directamente a los demás participantes.

Si añadimos usuarios, nuevas funcionalidades o reglas de coordinación, las dependencias aumentan.

### Problemas principales

1. **Acoplamiento elevado:** los componentes necesitan conocer a otros componentes concretos.
2. **Complejidad creciente:** añadir participantes o funcionalidades obliga a modificar varias clases.
3. **Lógica duplicada:** diferentes objetos pueden implementar las mismas reglas de comunicación.
4. **Difícil mantenimiento:** cambiar una interacción puede afectar a múltiples componentes.
5. **Pruebas más complejas:** las clases dependen unas de otras, lo que dificulta probarlas de forma aislada.

El patrón Mediator resuelve estos problemas centralizando la coordinación en un objeto especializado.

---

## 3. Solución propuesta

Mediator introduce un objeto denominado **mediador**, que conoce a los componentes que participan en una interacción y decide cómo deben comunicarse.

En lugar de que los componentes se invoquen directamente, notifican al mediador cuando ocurre una acción relevante.

El mediador interpreta esa acción y coordina las respuestas necesarias.

Por ejemplo, en una videoconferencia:

1. Un usuario envía un mensaje al mediador.
2. El mediador comprueba las reglas de comunicación.
3. El mediador identifica a los participantes que deben recibirlo.
4. El mediador distribuye el mensaje.

Los usuarios no necesitan conocer directamente a los demás participantes.

### Idea fundamental

**Los componentes colaboran a través del mediador, en lugar de mantener dependencias directas entre ellos.**

Esto no significa que todos los objetos deban comunicarse siempre mediante un mediador. El patrón es útil cuando existe una coordinación compleja entre varios componentes y centralizarla reduce realmente el acoplamiento.

---

## 4. Estructura del patrón

### 4.1. Mediator

Define el contrato de comunicación entre los componentes.

Puede declarar operaciones para registrar componentes y notificar acciones.

### 4.2. ConcreteMediator

Implementa las reglas de coordinación.

Conoce a los componentes participantes y decide qué debe ocurrir cuando uno de ellos genera una interacción.

### 4.3. Colleague o componente participante

Es un objeto que se comunica a través del mediador.

Puede enviar notificaciones al mediador y recibir instrucciones o mensajes procedentes de él.

### 4.4. ConcreteColleague

Es una implementación concreta de un componente participante.

En nuestro ejemplo, cada usuario de la videoconferencia será un componente de este tipo.

### Diagrama conceptual

```text
             +-------------------+
             |      Cliente      |
             +-------------------+
                       |
                       v
             +-------------------+
             | ConcreteMediator  |
             +-------------------+
               /       |       \
              /        |        \
             v         v         v
         +--------+ +--------+ +--------+
         |UsuarioA| |UsuarioB| |UsuarioC|
         +--------+ +--------+ +--------+
              \        |        /
               \       |       /
                Notifican al
                 mediador
```

Las interacciones se coordinan mediante el mediador. Los componentes no necesitan mantener referencias directas entre sí.

---

## 5. Implementación en Java

Vamos a implementar un sistema de chat sencillo en el que varios usuarios se comunican a través de una sala de chat.

### 5.1. Crear la interfaz Mediator

Definimos el contrato del mediador.

```java
public interface MediadorChat {

    void registrarUsuario(Usuario usuario);

    void enviarMensaje(String mensaje, Usuario emisor);
}
```

La interfaz permite registrar usuarios y enviar mensajes indicando quién los ha enviado.

### 5.2. Crear la clase Usuario

Cada usuario conoce al mediador, pero no necesita conocer a los demás usuarios.

```java
public class Usuario {

    private final String nombre;
    private final MediadorChat mediador;

    public Usuario(String nombre, MediadorChat mediador) {
        this.nombre = nombre;
        this.mediador = mediador;
    }

    public String getNombre() {
        return nombre;
    }

    public void enviarMensaje(String mensaje) {
        mediador.enviarMensaje(mensaje, this);
    }

    public void recibirMensaje(String mensaje, String emisor) {
        System.out.println(
            "[" + nombre + "] recibe de "
            + emisor + ": " + mensaje
        );
    }
}
```

La clase `Usuario` delega el envío de mensajes al mediador.

No necesita almacenar una lista de los demás usuarios ni decidir quién debe recibir cada mensaje.

### 5.3. Crear el mediador concreto

La clase `SalaChat` implementa la lógica de coordinación.

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Objects;

public class SalaChat implements MediadorChat {

    private final List<Usuario> usuarios = new ArrayList<>();

    @Override
    public void registrarUsuario(Usuario usuario) {
        Objects.requireNonNull(
            usuario,
            "El usuario no puede ser null"
        );

        if (!usuarios.contains(usuario)) {
            usuarios.add(usuario);
        }
    }

    @Override
    public void enviarMensaje(
        String mensaje,
        Usuario emisor
    ) {
        Objects.requireNonNull(
            mensaje,
            "El mensaje no puede ser null"
        );

        if (!usuarios.contains(emisor)) {
            throw new IllegalArgumentException(
                "El emisor no está registrado en la sala"
            );
        }

        for (Usuario usuario : usuarios) {
            if (usuario != emisor) {
                usuario.recibirMensaje(
                    mensaje,
                    emisor.getNombre()
                );
            }
        }
    }
}
```

El mediador es responsable de distribuir los mensajes.

En este ejemplo:

- Mantiene la lista de participantes.
- Comprueba que el emisor esté registrado.
- Distribuye el mensaje a todos los usuarios excepto al emisor.
- Centraliza las reglas de comunicación.

En una aplicación real, esta clase podría incorporar permisos, moderación, canales o filtros de mensajes.

### 5.4. Utilizar el patrón desde el cliente

```java
public class Main {

    public static void main(String[] args) {

        SalaChat sala = new SalaChat();

        Usuario ana = new Usuario("Ana", sala);
        Usuario carlos = new Usuario("Carlos", sala);
        Usuario lucia = new Usuario("Lucía", sala);

        sala.registrarUsuario(ana);
        sala.registrarUsuario(carlos);
        sala.registrarUsuario(lucia);

        ana.enviarMensaje("¡Hola a todos!");

        carlos.enviarMensaje("¡Hola, Ana!");
    }
}
```

Salida aproximada:

```text
[Carlos] recibe de Ana: ¡Hola a todos!
[Lucía] recibe de Ana: ¡Hola a todos!
[Ana] recibe de Carlos: ¡Hola, Ana!
[Lucía] recibe de Carlos: ¡Hola, Ana!
```

### ¿Qué hemos conseguido?

- Los usuarios no mantienen referencias a otros usuarios.
- La comunicación está centralizada en `SalaChat`.
- Podemos cambiar las reglas de distribución sin modificar la clase `Usuario`.
- Los participantes pueden añadirse a la sala sin modificar las clases de los demás.

Esta separación es la principal ventaja del patrón Mediator.

---

## 6. Añadir nuevas reglas de comunicación

Una ventaja de Mediator es que permite incorporar reglas de coordinación en un punto central.

Por ejemplo, podríamos impedir que los mensajes vacíos se distribuyan.

Podríamos modificar el método `enviarMensaje()` de `SalaChat`:

```java
@Override
public void enviarMensaje(
    String mensaje,
    Usuario emisor
) {
    Objects.requireNonNull(mensaje);
    Objects.requireNonNull(emisor);

    if (!usuarios.contains(emisor)) {
        throw new IllegalArgumentException(
            "El emisor no está registrado"
        );
    }

    if (mensaje.isBlank()) {
        return;
    }

    for (Usuario usuario : usuarios) {
        if (usuario != emisor) {
            usuario.recibirMensaje(
                mensaje,
                emisor.getNombre()
            );
        }
    }
}
```

Ahora la sala rechaza los mensajes vacíos sin introducir esa regla en cada usuario.

También podríamos añadir:

- Bloqueo temporal del chat.
- Mensajes privados.
- Roles de administrador y moderador.
- Restricciones de envío.
- Registro de mensajes.
- Notificaciones de entrada y salida.

A medida que crece la complejidad, conviene mantener el mediador organizado y delegar en servicios especializados aquellas responsabilidades que no formen parte de la coordinación.

---

## 7. Ejemplo con componentes de una interfaz gráfica

Mediator también es útil en interfaces gráficas donde varios controles dependen entre sí.

Imaginemos un formulario con:

- Un campo para introducir el nombre.
- Una casilla para aceptar las condiciones.
- Un botón para registrarse.

El botón solo debería habilitarse cuando el nombre sea válido y las condiciones estén aceptadas.

Sin un mediador, cada control podría necesitar referencias a los demás.

Con Mediator, los controles notifican los cambios y el mediador decide cómo actualizar la interfaz.

### 7.1. Definir el mediador

```java
public interface MediadorFormulario {

    void nombreCambiado(String nombre);

    void condicionesCambiadas(boolean aceptadas);

    void registrar();
}
```

### 7.2. Implementar el formulario como mediador

```java
public class FormularioRegistro
        implements MediadorFormulario {

    private String nombre = "";
    private boolean condicionesAceptadas;

    @Override
    public void nombreCambiado(String nombre) {
        this.nombre = nombre;
        actualizarEstado();
    }

    @Override
    public void condicionesCambiadas(
        boolean aceptadas
    ) {
        this.condicionesAceptadas = aceptadas;
        actualizarEstado();
    }

    private void actualizarEstado() {
        boolean formularioValido =
            nombre != null
            && !nombre.isBlank()
            && condicionesAceptadas;

        System.out.println(
            "Botón registrar habilitado: "
            + formularioValido
        );
    }

    @Override
    public void registrar() {
        if (nombre == null
                || nombre.isBlank()
                || !condicionesAceptadas) {
            throw new IllegalStateException(
                "El formulario no es válido"
            );
        }

        System.out.println(
            "Registrando usuario: " + nombre
        );
    }
}
```

En este ejemplo, el mediador centraliza la coordinación entre los campos y la acción de registro.

La interfaz es ilustrativa: no utiliza una biblioteca gráfica concreta. En una aplicación real, el mediador actualizaría el estado del botón y coordinaría los controles correspondientes.

---

## 8. Mediator frente a la comunicación directa

Para comprender mejor el patrón, comparemos ambos enfoques.

### Sin Mediator

```text
Usuario A <------> Usuario B
    ^                  ^
    |                  |
    v                  v
Usuario C <------> Usuario D
```

Cada componente puede necesitar referencias a varios de los demás.

Cuando aumenta el número de participantes, también aumenta la complejidad de las dependencias.

### Con Mediator

```text
       Usuario A
           |
       Usuario B
           |
           v
      Mediador
       /  |  \
      v   v   v
 Usuario C Usuario D Usuario E
```

Todos los participantes colaboran mediante el mediador.

El objetivo no es eliminar toda comunicación entre objetos, sino **reducir las dependencias directas y centralizar las reglas de coordinación que lo necesitan**.

---

## 9. Ventajas del patrón Mediator

### 9.1. Reducción del acoplamiento

Los componentes no necesitan conocer directamente a todos los demás participantes.

Solo necesitan conocer el contrato del mediador.

### 9.2. Centralización de la coordinación

Las reglas que gobiernan las interacciones se concentran en un punto identificable.

Esto facilita comprender cómo colaboran los componentes.

### 9.3. Mayor facilidad para modificar interacciones

Podemos cambiar la forma en que se comunican los componentes sin modificar cada clase participante.

### 9.4. Reutilización de componentes

Un componente puede utilizarse en otros contextos si depende de una abstracción del mediador y no de implementaciones específicas de otros participantes.

### 9.5. Pruebas más sencillas

Podemos probar un componente con un mediador simulado y verificar qué notificaciones envía.

También podemos probar las reglas de coordinación de forma centralizada.

### 9.6. Menos dependencias entre participantes

Los componentes no necesitan mantener listas de referencias a otros objetos ni conocer sus detalles internos.

---

## 10. Desventajas del patrón Mediator

### 10.1. Mediador demasiado complejo

Si toda la lógica de coordinación termina en una única clase, esta puede convertirse en un objeto excesivamente grande y difícil de mantener.

Este problema suele conocerse como *God Object*.

### 10.2. Punto central de cambio

Muchas interacciones pasan por el mediador. Por tanto, modificar sus reglas puede afectar a varios componentes.

### 10.3. Posible cuello de botella

En sistemas con muchas interacciones, el mediador puede concentrar demasiada carga si no está diseñado adecuadamente.

Esto no implica necesariamente un problema de rendimiento: depende del volumen de operaciones y de la implementación.

### 10.4. Complejidad adicional

Si los objetos apenas interactúan entre sí, introducir un mediador puede añadir abstracciones innecesarias.

### 10.5. Riesgo de acoplamiento al mediador concreto

Aunque el objetivo es reducir las dependencias entre componentes, un mediador que conoce demasiados detalles de cada participante puede terminar acoplando toda la aplicación a su implementación.

Por ello, es importante mantener responsabilidades claras y definir interfaces adecuadas.

---

## 11. Casos de uso reales

### 11.1. Interfaces gráficas

Coordinar formularios, botones, campos de texto, menús y ventanas cuyos comportamientos dependen entre sí.

### 11.2. Aplicaciones de mensajería

Gestionar la distribución de mensajes, la pertenencia a salas y las reglas de comunicación entre participantes.

### 11.3. Sistemas de videoconferencia

Coordinar usuarios, permisos de micrófono, cámaras, salas y eventos de moderación.

### 11.4. Sistemas de domótica

Coordinar luces, persianas, sensores y climatización mediante reglas comunes.

### 11.5. Flujos de trabajo empresariales

Coordinar componentes que participan en procesos de aprobación, validación y notificación.

### 11.6. Sistemas de videojuegos

Coordinar interacciones entre personajes, objetos, inventarios, interfaces y eventos del juego.

### 11.7. Aplicaciones con componentes desacoplados

Centralizar la coordinación cuando varios módulos necesitan reaccionar a acciones de otros sin depender directamente de sus implementaciones.

En sistemas distribuidos, una función similar puede realizarse mediante servicios de coordinación, eventos o mensajería, aunque esas soluciones no son necesariamente implementaciones directas del patrón GoF Mediator.

---

## 12. Relación con los principios SOLID

### Single Responsibility Principle (SRP)

Los componentes se centran en sus responsabilidades individuales, mientras que el mediador gestiona las reglas de coordinación.

Sin embargo, el mediador también debe tener una responsabilidad bien delimitada para evitar convertirse en una clase que lo hace todo.

### Open/Closed Principle (OCP)

Podemos incorporar nuevas interacciones centralizando sus reglas en el mediador y añadiendo nuevos componentes cuando el diseño lo permita.

No obstante, si cada nueva interacción obliga a modificar continuamente un mediador monolítico, será necesario revisar su diseño.

### Liskov Substitution Principle (LSP)

Las implementaciones de la interfaz del mediador deben respetar el contrato que esperan los componentes participantes.

Así, un mediador alternativo puede sustituir a otro cuando mantiene el comportamiento esperado.

### Interface Segregation Principle (ISP)

Es conveniente que los componentes dependan únicamente de las operaciones del mediador que necesitan.

En sistemas más complejos pueden definirse interfaces específicas para distintos tipos de interacción, evitando crear una interfaz enorme con métodos que muchos participantes no utilizan.

### Dependency Inversion Principle (DIP)

Los componentes pueden depender de una abstracción del mediador en lugar de una implementación concreta.

Esto permite probarlos de forma aislada y cambiar la implementación de coordinación con menos impacto.

---

## 13. Comparación con otros patrones de diseño

### 13.1. Mediator vs. Observer

| Mediator | Observer |
|---|---|
| Centraliza la coordinación entre componentes. | Define una relación de publicación y suscripción a cambios o eventos. |
| El mediador conoce las reglas de interacción. | El emisor de un evento notifica a sus observadores registrados. |
| Decide cómo reaccionan los participantes a una interacción. | Permite que varios observadores reaccionen a una notificación. |
| Reduce las dependencias directas entre componentes que colaboran. | Reduce el acoplamiento entre quien publica y quienes reciben las notificaciones. |

**Ejemplo:** en un chat, un mediador puede decidir quién recibe un mensaje. Observer puede utilizarse para notificar a distintas vistas que ha llegado un mensaje.

Los dos patrones pueden combinarse, pero no son equivalentes.

### 13.2. Mediator vs. Facade

| Mediator | Facade |
|---|---|
| Coordina la interacción entre varios objetos colaboradores. | Proporciona una interfaz simplificada para acceder a un subsistema. |
| Los participantes pueden comunicarse a través del mediador. | Los clientes utilizan la fachada para acceder a funcionalidades del subsistema. |
| Se centra en las relaciones entre los objetos. | Se centra en simplificar el acceso a un conjunto de funcionalidades. |

**Ejemplo:** un mediador coordina los controles de un formulario. Una fachada podría ofrecer un método sencillo para iniciar un proceso de registro que utiliza varios servicios internos.

### 13.3. Mediator vs. Chain of Responsibility

| Mediator | Chain of Responsibility |
|---|---|
| Centraliza la coordinación entre componentes. | Pasa una petición por una cadena de posibles manejadores. |
| El mediador decide qué participantes intervienen. | Cada manejador puede procesar o reenviar la petición. |
| Resulta útil para interacciones entre varios componentes. | Resulta útil cuando una petición debe encontrar uno o varios manejadores apropiados. |

### 13.4. Mediator vs. Command

| Mediator | Command |
|---|---|
| Coordina la comunicación entre varios objetos. | Encapsula una petición como un objeto. |
| Decide cómo deben colaborar los participantes. | Representa una operación que puede ejecutarse de manera independiente. |
| Se centra en las relaciones y la coordinación. | Se centra en la representación y ejecución de acciones. |

Un mediador puede recibir un comando y decidir qué componentes deben participar en su ejecución.

### 13.5. Mediator vs. Strategy

| Mediator | Strategy |
|---|---|
| Organiza las interacciones entre objetos. | Permite intercambiar algoritmos o comportamientos. |
| Se centra en la coordinación. | Se centra en la elección de una forma de realizar una operación. |
| Gestiona relaciones entre participantes. | Define implementaciones alternativas de una misma estrategia. |

---

## 14. Buenas prácticas

- Utilizar Mediator cuando existan múltiples interacciones entre componentes que resulte conveniente centralizar.
- Mantener el mediador enfocado en la coordinación, delegando la lógica de negocio especializada en servicios apropiados.
- Definir interfaces claras para que los componentes no dependan innecesariamente de implementaciones concretas.
- Evitar que el mediador acumule todas las responsabilidades del sistema.
- Mantener las reglas de comunicación explícitas y fáciles de probar.
- Considerar mediadores más pequeños o especializados cuando existan dominios de interacción independientes.
- Utilizar Observer o mecanismos de eventos cuando el problema sea principalmente notificar cambios a múltiples suscriptores.
- No introducir el patrón si la comunicación entre los objetos es sencilla y el acoplamiento existente no supone un problema real.
- Evitar que el mediador se convierta en un punto de sincronización innecesario en aplicaciones concurrentes.
- En sistemas distribuidos, evaluar si un servicio de coordinación, un intermediario de mensajes o una arquitectura basada en eventos se adapta mejor al problema.

---

## 15. Conclusión

El patrón Mediator es un patrón de comportamiento que permite centralizar la comunicación y coordinación entre un conjunto de objetos.

Su principal ventaja consiste en reducir las dependencias directas entre los componentes, evitando que tengan que conocer los detalles de los demás para colaborar.

Es especialmente útil en interfaces gráficas, sistemas de mensajería, aplicaciones de domótica y procesos empresariales donde existen múltiples reglas de interacción.

En Java, puede implementarse mediante una interfaz de mediador, una clase concreta que coordina las interacciones y componentes que delegan en ella sus solicitudes.

No obstante, el mediador debe diseñarse con cuidado: centralizar la coordinación es beneficioso, pero centralizar toda la lógica del sistema en una única clase puede crear un nuevo problema de mantenimiento.

Mediator se centra en cómo colaboran los objetos entre sí. En lugar de que cada componente conozca a los demás, delega la coordinación en un mediador común.

En definitiva, **Mediator permite que varios objetos colaboren sin depender directamente unos de otros, delegando la coordinación de sus interacciones en un objeto especializado**.

---

## 16. Referencias

- *Design Patterns: Elements of Reusable Object-Oriented Software*, Erich Gamma, Richard Helm, Ralph Johnson y John Vlissides.
- Refactoring.Guru — Mediator: https://refactoring.guru/design-patterns/mediator
- Documentación oficial de Java — Interfaces: https://docs.oracle.com/javase/tutorial/java/IandI/createinterface.html
- Documentación oficial de Java — `List`: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/List.html
