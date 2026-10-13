# Patrón de diseño estructural: Bridge (Puente)

## 1. Introducción

El patrón de diseño **Bridge (Puente)** es un patrón estructural que permite separar una abstracción de su implementación, de manera que ambas puedan evolucionar de forma independiente.

Su objetivo principal es **evitar que una abstracción y sus distintas implementaciones queden fuertemente acopladas mediante la herencia**.

Este patrón resulta especialmente útil cuando una clase tiene varias dimensiones de variación. En lugar de crear una subclase para cada combinación posible, Bridge separa esas dimensiones y las conecta mediante composición.

### Ejemplo conceptual

Imaginemos una aplicación que permite controlar distintos dispositivos, como televisores y radios, utilizando diferentes tipos de control remoto, como uno básico y otro avanzado.

Una solución basada exclusivamente en herencia podría terminar generando clases como:

- `ControlRemotoBasicoTelevision`
- `ControlRemotoAvanzadoTelevision`
- `ControlRemotoBasicoRadio`
- `ControlRemotoAvanzadoRadio`

Si posteriormente añadimos más dispositivos y controles remotos, el número de clases crecerá rápidamente.

Con Bridge podemos separar ambos conceptos:

- **Abstracción:** el tipo de control remoto.
- **Implementación:** el dispositivo que recibe las operaciones.

Cada control remoto trabaja con una implementación de dispositivo a través de una interfaz común. Así podemos combinar los controles y los dispositivos sin crear una clase para cada combinación.

---

## 2. Problema que resuelve

Bridge resuelve el problema de la **explosión de subclases cuando existen varias dimensiones de variación independientes**.

Este problema aparece cuando utilizamos herencia para representar simultáneamente diferentes características de un objeto.

### Ejemplo sin utilizar Bridge

Supongamos que tenemos dos tipos de notificación:

- Notificación normal.
- Notificación urgente.

Y dos canales de envío:

- Correo electrónico.
- SMS.

Podríamos crear una jerarquía como esta:

```text
Notificacion
├── NotificacionNormalEmail
├── NotificacionUrgenteEmail
├── NotificacionNormalSMS
└── NotificacionUrgenteSMS
```

Cada clase combina un tipo de notificación con un canal de envío.

El problema se agrava cuando añadimos más tipos de notificación o más canales. Si existen \(n\) variantes de abstracción y \(m\) implementaciones, una solución basada en clases para cada combinación podría requerir hasta \(n \times m\) clases.

Además, cada nueva combinación puede obligarnos a duplicar lógica o modificar una jerarquía existente.

**Bridge soluciona este problema separando las dos dimensiones en jerarquías independientes y conectándolas mediante composición.**

---

## 3. Solución propuesta

La solución consiste en dividir el diseño en dos partes:

1. **Abstracción:** define las operaciones de alto nivel que necesita el cliente.
2. **Implementación:** define cómo se ejecutan esas operaciones en una plataforma, dispositivo o servicio concreto.

La abstracción mantiene una referencia a una implementación mediante una interfaz. De este modo, ambas partes pueden evolucionar sin depender de clases concretas de la otra jerarquía.

### Diagrama conceptual

```text
                 Cliente
                    |
                    v
             Abstraccion
                    |
                    | referencia
                    v
             Implementacion
                    ^
                    |
          +---------+---------+
          |                   |
 ImplementacionA       ImplementacionB

Abstraccion
    ^
    |
+---+----------------+
|                    |
RefinamientoA   RefinamientoB
```

### Participantes del patrón

- **Abstraction:** define la interfaz de alto nivel y mantiene una referencia a una implementación.
- **RefinedAbstraction:** amplía o especializa el comportamiento de la abstracción.
- **Implementor:** define la interfaz de las operaciones que realizan las implementaciones concretas.
- **ConcreteImplementor:** proporciona una implementación concreta de esa interfaz.
- **Client:** utiliza la abstracción sin necesitar conocer los detalles de implementación.

Es importante destacar que la abstracción y la implementación son dos jerarquías distintas. La abstracción no hereda de la implementación: **las conecta mediante composición**.

---

## 4. Implementación completa en Java

Para entender Bridge, utilizaremos el ejemplo de las notificaciones con distintos niveles de prioridad y diferentes canales de envío.

### 4.1. Interfaz de implementación: Implementor

Esta interfaz define las operaciones que deben ofrecer los distintos canales de notificación.

```java
public interface CanalEnvio {

    void enviarMensaje(String destinatario, String mensaje);
}
```

La interfaz `CanalEnvio` no define si el mensaje es urgente o normal. Solo establece cómo se realiza el envío a través de un canal.

### 4.2. Implementación concreta: correo electrónico

```java
public class CanalEmail implements CanalEnvio {

    @Override
    public void enviarMensaje(
            String destinatario,
            String mensaje) {

        System.out.println(
            "Enviando EMAIL a "
            + destinatario
            + ": "
            + mensaje
        );
    }
}
```

Esta clase representa una de las implementaciones concretas del canal de envío.

### 4.3. Implementación concreta: SMS

```java
public class CanalSMS implements CanalEnvio {

    @Override
    public void enviarMensaje(
            String destinatario,
            String mensaje) {

        System.out.println(
            "Enviando SMS a "
            + destinatario
            + ": "
            + mensaje
        );
    }
}
```

Esta implementación utiliza un canal diferente, pero respeta el mismo contrato.

En una aplicación real, ambas clases podrían comunicarse con proveedores externos y gestionar errores de red, autenticación o entrega.

### 4.4. Abstracción: Notificacion

La clase `Notificacion` define el comportamiento común de alto nivel y mantiene una referencia a `CanalEnvio`.

```java
public abstract class Notificacion {

    protected final CanalEnvio canalEnvio;

    protected Notificacion(CanalEnvio canalEnvio) {
        this.canalEnvio = canalEnvio;
    }

    public abstract void enviar(
        String destinatario,
        String mensaje
    );
}
```

La abstracción no necesita conocer las clases `CanalEmail` o `CanalSMS`. Solo conoce la interfaz `CanalEnvio`.

Este es uno de los puntos fundamentales del patrón.

### 4.5. Abstracción refinada: notificación normal

```java
public class NotificacionNormal extends Notificacion {

    public NotificacionNormal(CanalEnvio canalEnvio) {
        super(canalEnvio);
    }

    @Override
    public void enviar(
            String destinatario,
            String mensaje) {

        canalEnvio.enviarMensaje(
            destinatario,
            "[NORMAL] " + mensaje
        );
    }
}
```

La notificación normal utiliza el canal configurado para enviar el mensaje con una etiqueta de prioridad.

### 4.6. Abstracción refinada: notificación urgente

```java
public class NotificacionUrgente extends Notificacion {

    public NotificacionUrgente(CanalEnvio canalEnvio) {
        super(canalEnvio);
    }

    @Override
    public void enviar(
            String destinatario,
            String mensaje) {

        canalEnvio.enviarMensaje(
            destinatario,
            "[URGENTE] " + mensaje
        );
    }
}
```

La notificación urgente utiliza el mismo contrato de canal, pero modifica el contenido enviado.

En una implementación más avanzada, podría añadir reglas de reintento, prioridad de entrega o mecanismos de escalado.

### 4.7. Clase principal

```java
public class Main {

    public static void main(String[] args) {

        CanalEnvio email = new CanalEmail();
        CanalEnvio sms = new CanalSMS();

        Notificacion normalPorEmail =
            new NotificacionNormal(email);

        Notificacion urgentePorSMS =
            new NotificacionUrgente(sms);

        normalPorEmail.enviar(
            "usuario@ejemplo.com",
            "Tu pedido ha sido recibido"
        );

        urgentePorSMS.enviar(
            "+34123456789",
            "Se ha detectado una incidencia"
        );
    }
}
```

Salida aproximada:

```text
Enviando EMAIL a usuario@ejemplo.com: [NORMAL] Tu pedido ha sido recibido
Enviando SMS a +34123456789: [URGENTE] Se ha detectado una incidencia
```

### 4.8. ¿Qué hemos conseguido?

Hemos separado dos dimensiones independientes:

- El tipo de notificación: normal o urgente.
- El canal de envío: correo electrónico o SMS.

Podemos combinar cualquiera de las abstracciones con cualquiera de las implementaciones compatibles, sin crear una clase específica para cada combinación.

Por ejemplo:

```java
Notificacion urgentePorEmail =
    new NotificacionUrgente(new CanalEmail());

Notificacion normalPorSMS =
    new NotificacionNormal(new CanalSMS());
```

Si añadimos un canal nuevo, como una notificación push, podemos crear `CanalPush` sin modificar las clases de notificación existentes.

Del mismo modo, podemos añadir otro tipo de notificación sin tener que implementar de nuevo cada canal.

---

## 5. Estructura general del patrón

La estructura habitual de Bridge puede representarse de forma genérica:

```java
// Implementor
public interface Implementacion {
    void ejecutarOperacion();
}

// ConcreteImplementor
public class ImplementacionA implements Implementacion {

    @Override
    public void ejecutarOperacion() {
        System.out.println("Implementación A");
    }
}

// ConcreteImplementor
public class ImplementacionB implements Implementacion {

    @Override
    public void ejecutarOperacion() {
        System.out.println("Implementación B");
    }
}

// Abstraction
public abstract class Abstraccion {

    protected final Implementacion implementacion;

    protected Abstraccion(Implementacion implementacion) {
        this.implementacion = implementacion;
    }

    public abstract void operacion();
}

// RefinedAbstraction
public class AbstraccionRefinada extends Abstraccion {

    public AbstraccionRefinada(
            Implementacion implementacion) {
        super(implementacion);
    }

    @Override
    public void operacion() {
        System.out.println("Operación de alto nivel");
        implementacion.ejecutarOperacion();
    }
}

// Client
public class Main {

    public static void main(String[] args) {

        Abstraccion objeto = new AbstraccionRefinada(
            new ImplementacionA()
        );

        objeto.operacion();
    }
}
```

El elemento esencial es que `Abstraccion` recibe una implementación mediante su constructor y la utiliza por composición.

Esto permite cambiar la implementación sin cambiar necesariamente la abstracción que utiliza el cliente.

---

## 6. Ventajas y desventajas

### Ventajas

1. **Separación de responsabilidades:** la abstracción y la implementación evolucionan de forma independiente.
2. **Reducción de subclases:** evita crear una clase para cada combinación de características.
3. **Mayor flexibilidad:** permite combinar diferentes abstracciones con distintas implementaciones.
4. **Menor acoplamiento:** la abstracción depende de una interfaz, no de una implementación concreta.
5. **Facilidad de extensión:** pueden añadirse nuevas implementaciones sin alterar la jerarquía de abstracción.
6. **Mejor testabilidad:** las implementaciones pueden sustituirse por dobles de prueba.
7. **Reutilización:** una implementación puede utilizarse desde varias abstracciones compatibles.

### Desventajas

1. **Mayor complejidad inicial:** requiere identificar correctamente las dos dimensiones de variación.
2. **Más interfaces y clases:** puede resultar excesivo para problemas sencillos.
3. **Diseño menos intuitivo:** la separación entre abstracción e implementación puede dificultar la comprensión inicial.
4. **No elimina todas las dependencias:** ambas jerarquías siguen relacionadas mediante un contrato común.
5. **Puede ser innecesario:** si no hay dimensiones independientes que deban evolucionar por separado, una solución más simple puede ser suficiente.

Bridge aporta más valor cuando las dos partes del diseño tienen motivos diferentes para cambiar.

---

## 7. Relación con los principios SOLID

### Single Responsibility Principle (SRP)

La abstracción y la implementación tienen responsabilidades distintas.

En el ejemplo:

- `NotificacionUrgente` define el comportamiento asociado a la prioridad.
- `CanalEmail` define cómo enviar un mensaje mediante correo electrónico.

Cada clase se centra en una dimensión del problema.

### Open/Closed Principle (OCP)

Podemos añadir nuevas implementaciones, como `CanalPush`, sin modificar las abstracciones existentes.

También podemos incorporar nuevos refinamientos de `Notificacion` sin tener que crear una implementación específica para cada canal.

Esto favorece la extensión del sistema sin modificar continuamente el código ya existente.

### Liskov Substitution Principle (LSP)

Las implementaciones concretas de `CanalEnvio` deben cumplir el contrato definido por la interfaz.

Por ejemplo, sustituir `CanalEmail` por `CanalSMS` no debería provocar comportamientos incompatibles con las expectativas que establece la abstracción.

El cumplimiento depende tanto de la firma de los métodos como de su comportamiento.

### Interface Segregation Principle (ISP)

La interfaz `CanalEnvio` define una operación concreta y no obliga a todas las implementaciones a proporcionar funcionalidades ajenas a su propósito.

Las interfaces pequeñas facilitan la incorporación de nuevos canales y simplifican las pruebas.

### Dependency Inversion Principle (DIP)

La abstracción `Notificacion` depende de la interfaz `CanalEnvio`, no de clases concretas como `CanalEmail` o `CanalSMS`.

Las implementaciones concretas también cumplen esa interfaz.

Esta separación facilita cambiar los detalles técnicos sin tener que reescribir la lógica de alto nivel.

---

## 8. Diferencias con otros patrones estructurales

### Bridge vs. Adapter

Aunque ambos patrones pueden utilizar composición y delegación, su intención es distinta.

- **Bridge:** separa dos dimensiones de diseño para que puedan evolucionar independientemente.
- **Adapter:** adapta una interfaz existente a otra que necesita el cliente.

Bridge suele formar parte del diseño de la solución desde el principio. Adapter se utiliza frecuentemente para integrar componentes cuyas interfaces ya son incompatibles.

### Bridge vs. Strategy

Ambos patrones delegan comportamiento a través de una interfaz, pero resuelven problemas diferentes.

- **Bridge:** separa una abstracción de su implementación para permitir que ambas jerarquías evolucionen independientemente.
- **Strategy:** permite intercambiar algoritmos o comportamientos que cumplen un mismo contrato.

En algunos diseños, la interfaz de implementación de Bridge puede desempeñar un papel similar al de una estrategia. Sin embargo, la intención global del patrón es distinta.

### Bridge vs. Decorator

- **Bridge:** separa dos dimensiones independientes de un diseño.
- **Decorator:** añade responsabilidades a un objeto sin modificar su interfaz pública.

Bridge conecta una abstracción con una implementación; Decorator envuelve un objeto para ampliar su comportamiento.

### Bridge vs. Abstract Factory

- **Bridge:** define cómo se relacionan la abstracción y su implementación.
- **Abstract Factory:** proporciona una interfaz para crear familias de objetos relacionados sin especificar sus clases concretas.

Ambos pueden utilizarse juntos. Por ejemplo, una fábrica puede crear los componentes de una plataforma y Bridge puede conectar esos componentes con las abstracciones de alto nivel.

---

## 9. Casos de uso habituales

Bridge resulta útil en los siguientes escenarios:

- Aplicaciones que deben funcionar en distintas plataformas.
- Sistemas que admiten múltiples proveedores de servicios.
- Interfaces gráficas con diferentes sistemas de representación.
- Controladores que gestionan distintos tipos de dispositivos.
- Sistemas de notificaciones con múltiples canales.
- Motores de persistencia con varios proveedores de bases de datos.
- Aplicaciones que separan las operaciones de alto nivel de los detalles de infraestructura.

### Ejemplo de una aplicación multiplataforma

Imaginemos una aplicación gráfica con diferentes tipos de ventanas y distintos sistemas de representación.

Las abstracciones podrían ser:

- `VentanaBasica`
- `VentanaAvanzada`

Las implementaciones podrían ser:

- `RenderizadorWindows`
- `RenderizadorLinux`
- `RenderizadorWeb`

Bridge permitiría combinar cada tipo de ventana con los renderizadores compatibles, evitando crear una clase distinta para cada combinación.

### Ejemplo en una arquitectura por capas

En una aplicación empresarial, una abstracción de alto nivel puede delegar operaciones en implementaciones diferentes para acceder a distintos servicios externos.

No obstante, Bridge no es una arquitectura de software completa ni sustituye por sí mismo a una arquitectura hexagonal o a Clean Architecture. Es un patrón de diseño que puede emplearse dentro de ellas cuando existe una separación real entre dos dimensiones de variación.

---

## 10. Buenas prácticas

1. **Identifica dos dimensiones independientes:** Bridge es más útil cuando ambas pueden cambiar por separado.
2. **Utiliza composición:** permite conectar la abstracción con distintas implementaciones sin depender de una jerarquía rígida.
3. **Define interfaces coherentes:** el contrato de implementación debe representar las operaciones que necesita la abstracción.
4. **Evita interfaces excesivamente generales:** una interfaz clara facilita el mantenimiento y las pruebas.
5. **Mantén las responsabilidades separadas:** la abstracción debe centrarse en el comportamiento de alto nivel y la implementación en los detalles correspondientes.
6. **Inyecta las implementaciones:** los constructores o un contenedor de dependencias pueden facilitar la sustitución de implementaciones.
7. **Prueba ambas dimensiones:** verifica tanto los refinamientos de la abstracción como las implementaciones concretas.
8. **No lo introduzcas por anticipado sin necesidad:** si el diseño solo tiene una implementación y no se prevé una variación independiente, Bridge puede añadir complejidad innecesaria.
9. **Documenta las combinaciones compatibles:** no todas las abstracciones tienen por qué funcionar correctamente con todas las implementaciones.
10. **Evalúa los errores y las garantías:** las implementaciones deben mantener las expectativas del contrato, incluso cuando utilizan tecnologías distintas.

---

## 11. Pruebas recomendadas

Las pruebas de Bridge deberían comprobar que la abstracción delega correctamente en la implementación elegida y que ambas partes pueden cambiar de forma independiente.

Para el ejemplo de las notificaciones, conviene verificar que:

- `NotificacionNormal` utiliza el canal configurado.
- `NotificacionUrgente` añade el comportamiento correspondiente a su prioridad.
- Cada canal recibe el destinatario y el mensaje esperados.
- Las nuevas implementaciones respetan el contrato de `CanalEnvio`.
- Las abstracciones no necesitan conocer las clases concretas de los canales.

Un ejemplo de doble de prueba sería:

```java
public class CanalEnvioFake implements CanalEnvio {

    private String ultimoDestinatario;
    private String ultimoMensaje;

    @Override
    public void enviarMensaje(
            String destinatario,
            String mensaje) {

        this.ultimoDestinatario = destinatario;
        this.ultimoMensaje = mensaje;
    }

    public String getUltimoDestinatario() {
        return ultimoDestinatario;
    }

    public String getUltimoMensaje() {
        return ultimoMensaje;
    }
}
```

Este objeto permite comprobar el comportamiento de las abstracciones sin enviar mensajes reales.

En un proyecto real, se pueden utilizar bibliotecas de pruebas como JUnit y Mockito para verificar las interacciones, así como pruebas de integración para validar los canales reales.

---

## 12. Conclusión

El patrón **Bridge (Puente)** permite separar una abstracción de su implementación para que ambas puedan evolucionar de manera independiente.

Su principal beneficio consiste en reducir el acoplamiento entre dos dimensiones de un diseño, evitando una proliferación de subclases que representan todas las combinaciones posibles.

En Java, se implementa habitualmente mediante una interfaz de implementación, una abstracción que mantiene una referencia a esa interfaz y distintas clases concretas para cada dimensión.

Bridge resulta especialmente útil cuando un sistema debe admitir diferentes plataformas, proveedores, canales o mecanismos de ejecución sin multiplicar innecesariamente las clases.

La idea fundamental es: **separar lo que el sistema quiere hacer de cómo se realiza, conectando ambas partes mediante composición y una abstracción común.**

---

## 13. Referencias

- **Design Patterns: Elements of Reusable Object-Oriented Software**, de Erich Gamma, Richard Helm, Ralph Johnson y John Vlissides (Gang of Four).
- Refactoring.Guru — Bridge: https://refactoring.guru/design-patterns/bridge
- Java Documentation: https://docs.oracle.com/en/java/
- SourceMaking — Bridge Design Pattern: https://sourcemaking.com/design_patterns/bridge

[//]: # (end)