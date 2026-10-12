# Patrón de Diseño Memento (Recuerdo)

## 1. Introducción

El patrón de diseño **Memento** pertenece a la categoría de los patrones de comportamiento (*Behavioral Design Patterns*) definidos por GoF (*Gang of Four*).

Su objetivo es **capturar y almacenar el estado interno de un objeto en un momento determinado para poder restaurarlo posteriormente, sin exponer los detalles de su implementación**.

Este patrón resulta especialmente útil cuando una aplicación necesita permitir que un usuario deshaga acciones, recupere versiones anteriores de un documento o restaure un objeto a un estado previo.

Por ejemplo, un editor de texto puede guardar el contenido actual de un documento antes de realizar una modificación. Si el usuario comete un error, el sistema puede restaurar el contenido anterior utilizando el recuerdo almacenado.

### Objetivos principales

- Guardar el estado de un objeto en un momento concreto.
- Restaurar un objeto a un estado anterior.
- Encapsular el estado guardado sin exponer su representación interna.
- Implementar mecanismos de deshacer y restaurar.
- Mantener separados el objeto original y la gestión de su historial.
- Facilitar la recuperación de versiones anteriores de un objeto.

---

## 2. Problema que resuelve

Imaginemos que estamos desarrollando un editor de texto. El usuario puede escribir, borrar, modificar y reemplazar el contenido de un documento.

Supongamos que el documento contiene inicialmente:

```text
Hola, mundo
```

Después de varias modificaciones, el contenido podría ser:

```text
Hola, mundo. Este es mi documento.
```

Si el usuario desea recuperar el contenido original, la aplicación necesita disponer de una copia del estado anterior.

Una solución sencilla consistiría en exponer el contenido interno del documento para que otra clase pudiera almacenarlo y restaurarlo.

```java
String contenidoAnterior = documento.getContenido();

// Modificar el documento.

documento.setContenido(contenidoAnterior);
```

Aunque este enfoque puede funcionar, presenta algunos inconvenientes si se utiliza de forma generalizada.

### Problemas principales

1. **Exposición del estado interno:** otras clases pueden necesitar acceder a detalles que deberían permanecer encapsulados.
2. **Acoplamiento:** el componente que administra el historial depende de la representación interna del objeto.
3. **Dificultad para mantener estados complejos:** un objeto puede tener múltiples atributos que deben guardarse y restaurarse conjuntamente.
4. **Duplicación de lógica:** el código encargado de guardar estados puede terminar repartido por distintas partes de la aplicación.
5. **Riesgo de restauraciones incompletas:** si se olvida guardar algún atributo relevante, el objeto puede no recuperar su estado correctamente.

El patrón Memento resuelve estos problemas proporcionando un objeto especializado que representa una instantánea del estado.

---

## 3. Solución propuesta

Memento introduce un objeto que contiene una representación del estado de otro objeto en un instante determinado.

El objeto cuyo estado se desea guardar se denomina **Originator**. Es el responsable de crear los recuerdos y restaurar su estado a partir de ellos.

El objeto que almacena y administra esos recuerdos se denomina **Caretaker**. Se encarga de conservarlos, por ejemplo, en una pila o una lista, sin modificar su contenido interno.

El tercer participante es el **Memento**, que representa el estado guardado.

El flujo habitual es el siguiente:

1. El objeto original se encuentra en un estado determinado.
2. El cliente solicita crear un recuerdo.
3. El objeto original crea un Memento con la información necesaria.
4. El administrador del historial almacena ese Memento.
5. El objeto original continúa modificándose.
6. Cuando es necesario restaurar un estado, se recupera el Memento y se entrega al objeto original.
7. El objeto original restaura su estado a partir de la información guardada.

### Idea fundamental

**Memento permite guardar y restaurar el estado de un objeto sin obligar a otros objetos a conocer cómo está representado internamente.**

---

## 4. Estructura del patrón

### 4.1. Originator

Es el objeto cuyo estado queremos guardar y restaurar.

Sus responsabilidades son:

- Mantener el estado actual.
- Crear Mementos que representen su estado.
- Restaurar su estado a partir de un Memento válido.

### 4.2. Memento

Representa una instantánea del estado del Originator.

Contiene la información necesaria para restaurarlo, pero debe evitar que otros objetos puedan modificar arbitrariamente ese estado.

En Java, esto puede implementarse mediante una clase con campos privados y acceso restringido.

### 4.3. Caretaker

Es el objeto que administra los Mementos.

Puede almacenarlos en una pila, una lista o cualquier estructura de datos adecuada.

El Caretaker no necesita conocer los detalles internos del estado almacenado.

### 4.4. Cliente

Es el código que coordina las operaciones, como solicitar que se guarde un estado, modificar el objeto o solicitar una restauración.

Dependiendo de la implementación, el cliente puede trabajar directamente con el Caretaker.

### Diagrama conceptual

```text
             Cliente
                |
                v
          +-------------+
          |  Caretaker  |
          +-------------+
          | historial   |
          +-------------+
                |
                | almacena
                v
          +-------------+
          |   Memento   |
          +-------------+
          | estado      |
          +-------------+
                ^
                | crea / restaura
                |
          +-------------+
          |  Originator |
          +-------------+
          | estadoActual|
          +-------------+
```

El Originator es el único responsable de interpretar el contenido del recuerdo. El Caretaker administra su ciclo de vida, pero no necesita conocer sus detalles internos.

---

## 5. Implementación en Java

Vamos a implementar un editor de texto sencillo que permita guardar el contenido de un documento y restaurarlo posteriormente.

### 5.1. Crear la clase Memento

El Memento almacena una copia del contenido del documento.

```java
public final class Memento {

    private final String contenido;

    Memento(String contenido) {
        this.contenido = contenido;
    }

    String getContenido() {
        return contenido;
    }
}
```

En este ejemplo:

- La clase es `final` para impedir que se herede y se altere su comportamiento.
- El contenido es `private` y `final`.
- El constructor y el método de lectura tienen visibilidad de paquete, por lo que el ejemplo debe mantener `Memento` y `Documento` en el mismo paquete.

Esta solución es sencilla porque `String` es inmutable.

En un diseño más estricto, podríamos utilizar una clase interna privada del Originator o una interfaz de Memento que el Caretaker almacene sin tener acceso a los datos internos.

### 5.2. Crear el Originator

La clase `Documento` representa el objeto cuyo estado queremos conservar.

```java
public class Documento {

    private String contenido = "";

    public void escribir(String texto) {
        contenido += texto;
    }

    public void reemplazarContenido(String nuevoContenido) {
        contenido = nuevoContenido;
    }

    public String getContenido() {
        return contenido;
    }

    public Memento guardarEstado() {
        return new Memento(contenido);
    }

    public void restaurarEstado(Memento memento) {
        if (memento == null) {
            throw new IllegalArgumentException(
                "El Memento no puede ser null"
            );
        }

        this.contenido = memento.getContenido();
    }
}
```

La clase `Documento` es el Originator porque controla tanto el estado actual como la creación y restauración de los recuerdos.

El Caretaker no necesita modificar directamente el atributo `contenido`.

### 5.3. Crear el Caretaker

La clase `HistorialDocumento` administra los estados guardados.

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class HistorialDocumento {

    private final Deque<Memento> historial =
        new ArrayDeque<>();

    public void guardar(Memento memento) {
        historial.push(memento);
    }

    public Memento recuperarUltimoEstado() {
        if (historial.isEmpty()) {
            throw new IllegalStateException(
                "No hay estados guardados"
            );
        }

        return historial.pop();
    }

    public boolean hayEstadosGuardados() {
        return !historial.isEmpty();
    }
}
```

El Caretaker utiliza una pila para almacenar los recuerdos.

- `push()` guarda un nuevo estado.
- `pop()` recupera y elimina el estado más reciente.
- `hayEstadosGuardados()` permite comprobar si existen estados previos.

Este comportamiento es adecuado para un mecanismo de deshacer en el que cada restauración consume el último estado guardado.

### 5.4. Utilizar el patrón desde el cliente

```java
public class Main {

    public static void main(String[] args) {

        Documento documento = new Documento();
        HistorialDocumento historial =
            new HistorialDocumento();

        documento.escribir("Hola");
        historial.guardar(documento.guardarEstado());

        documento.escribir(", mundo");
        historial.guardar(documento.guardarEstado());

        documento.escribir(" desde Java");

        System.out.println(
            "Estado actual: " + documento.getContenido()
        );

        documento.restaurarEstado(
            historial.recuperarUltimoEstado()
        );

        System.out.println(
            "Primer deshacer: " + documento.getContenido()
        );

        documento.restaurarEstado(
            historial.recuperarUltimoEstado()
        );

        System.out.println(
            "Segundo deshacer: " + documento.getContenido()
        );
    }
}
```

Salida aproximada:

```text
Estado actual: Hola, mundo desde Java
Primer deshacer: Hola, mundo
Segundo deshacer: Hola
```

### ¿Qué hemos conseguido?

- El documento puede recuperar estados anteriores.
- El historial no necesita conocer cómo se representa internamente el documento.
- La creación y restauración de recuerdos están encapsuladas en el Originator.
- Podemos gestionar el historial sin exponer directamente los atributos internos.

**Nota:** este ejemplo guarda una instantánea después de escribir cada fragmento. En un editor real, el momento de crear los recuerdos dependería de las reglas de la aplicación, por ejemplo, guardar antes de cada modificación o agrupar varias modificaciones en una única operación.

---

## 6. Implementación de un historial completo

La implementación anterior utiliza una pila que elimina el recuerdo al restaurarlo. Es suficiente para ilustrar el mecanismo de deshacer, pero no conserva un historial completo para navegar por las versiones.

Si necesitamos mantener todas las versiones, podemos utilizar una lista y un índice que indique el estado actual.

```java
import java.util.ArrayList;
import java.util.List;

public class HistorialVersiones {

    private final List<Memento> versiones =
        new ArrayList<>();

    private int indiceActual = -1;

    public void guardar(Memento memento) {
        // Si se ha restaurado una versión anterior,
        // descartamos las versiones posteriores.
        while (versiones.size() > indiceActual + 1) {
            versiones.remove(versiones.size() - 1);
        }

        versiones.add(memento);
        indiceActual = versiones.size() - 1;
    }

    public Memento deshacer() {
        if (indiceActual <= 0) {
            throw new IllegalStateException(
                "No hay una versión anterior"
            );
        }

        indiceActual--;
        return versiones.get(indiceActual);
    }

    public Memento rehacer() {
        if (indiceActual >= versiones.size() - 1) {
            throw new IllegalStateException(
                "No hay una versión posterior"
            );
        }

        indiceActual++;
        return versiones.get(indiceActual);
    }
}
```

Este historial permite:

- Guardar nuevas versiones.
- Volver a una versión anterior mediante `deshacer()`.
- Avanzar hacia una versión posterior mediante `rehacer()`.
- Descartar las versiones posteriores si se realiza una modificación nueva después de deshacer.

La clase administra los recuerdos, pero sigue sin interpretar su contenido interno.

Para utilizarla, el cliente debe entregar al documento el Memento que devuelve cada operación:

```java
documento.restaurarEstado(historial.deshacer());
documento.restaurarEstado(historial.rehacer());
```

Antes de usar `deshacer()`, debe existir al menos una versión anterior; para `rehacer()`, debe existir una versión posterior.

---

## 7. Memento con estados más complejos

El patrón no se limita a guardar una única cadena de texto.

Un objeto puede contener varios atributos que deban restaurarse conjuntamente.

Por ejemplo, un editor gráfico podría tener:

- La posición de un elemento.
- Su tamaño.
- Su color.
- Su rotación.
- Su visibilidad.

Una instantánea debería guardar todos los atributos necesarios para recuperar el estado lógico anterior.

```java
public final class PosicionMemento {

    private final int x;
    private final int y;
    private final int ancho;
    private final int alto;

    public PosicionMemento(
        int x,
        int y,
        int ancho,
        int alto
    ) {
        this.x = x;
        this.y = y;
        this.ancho = ancho;
        this.alto = alto;
    }

    public int getX() {
        return x;
    }

    public int getY() {
        return y;
    }

    public int getAncho() {
        return ancho;
    }

    public int getAlto() {
        return alto;
    }
}
```

El Originator podría crear una instancia con los valores actuales y utilizarlos posteriormente para restaurar su estado.

Cuando el estado incluye objetos mutables, colecciones o referencias a otros objetos, hay que decidir si es necesaria una copia profunda. Guardar únicamente referencias puede provocar que el supuesto recuerdo cambie cuando los objetos referenciados se modifiquen.

---

## 8. Encapsulación y visibilidad del Memento

La protección del estado es uno de los aspectos más importantes del patrón.

El Caretaker debe poder almacenar y devolver un recuerdo, pero no debería modificar arbitrariamente la información que contiene.

En Java existen varias formas de conseguirlo.

### 8.1. Clase Memento con campos privados

Como en los ejemplos anteriores, el estado se mantiene en campos privados y se expone únicamente la información necesaria.

Es una solución sencilla cuando el estado es inmutable y el diseño no requiere ocultar completamente su representación.

### 8.2. Memento como clase interna privada

El Originator puede definir el Memento como una clase interna privada. De esta forma, el Caretaker puede almacenar recuerdos a través de un tipo abstracto o una interfaz sin conocer su contenido.

Es una opción especialmente útil cuando se necesita preservar con más rigor la encapsulación.

### 8.3. Memento inmutable

Un Memento inmutable evita que su estado cambie después de ser creado.

Esto resulta recomendable para historiales, versiones y operaciones de restauración.

**Importante:** la inmutabilidad de la referencia no garantiza por sí sola que todo el estado sea inmutable. Si el recuerdo contiene objetos mutables, puede ser necesario copiarlos o utilizar representaciones inmutables.

---

## 9. Ventajas del patrón Memento

### 9.1. Encapsulación del estado

El objeto original mantiene el control sobre cómo se guarda y restaura su estado.

El administrador del historial no necesita acceder a los atributos internos.

### 9.2. Recuperación de estados anteriores

Permite implementar operaciones de deshacer, restaurar versiones y recuperar estados previos.

### 9.3. Separación de responsabilidades

El Originator administra su estado y crea recuerdos, mientras que el Caretaker administra su almacenamiento y recuperación.

### 9.4. Historial de cambios

Los Mementos pueden almacenarse en pilas o listas para mantener un historial de versiones.

### 9.5. Facilidad para probar restauraciones

Es posible verificar que un objeto recupera correctamente un estado guardado después de determinadas modificaciones.

### 9.6. Flexibilidad

El patrón puede aplicarse a documentos, configuraciones, editores, procesos de juego y otros objetos cuyo estado deba conservarse.

---

## 10. Desventajas del patrón Memento

### 10.1. Consumo de memoria

Guardar numerosas instantáneas completas puede consumir mucha memoria, especialmente si el objeto contiene grandes cantidades de datos.

### 10.2. Coste de creación de recuerdos

Crear una copia del estado puede resultar costoso si la estructura es compleja o contiene muchos objetos relacionados.

### 10.3. Complejidad en estados mutables

Es necesario determinar qué datos deben copiarse y cuáles pueden compartirse de forma segura.

### 10.4. Restauración incompleta

Si no se guarda toda la información relevante, el objeto puede no recuperar correctamente su estado anterior.

### 10.5. Gestión del historial

La aplicación debe decidir cuándo guardar recuerdos, cuántos conservar y cuándo descartarlos.

### 10.6. Posibles limitaciones con sistemas externos

Restaurar el estado de un objeto en memoria no revierte automáticamente operaciones que ya se hayan ejecutado en sistemas externos, como enviar un correo, realizar un pago o publicar un mensaje.

En estos casos pueden ser necesarios mecanismos de compensación, transacciones u otros patrones.

---

## 11. Casos de uso reales

### 11.1. Editores de texto

Guardar el contenido del documento antes de modificarlo para permitir deshacer o rehacer cambios.

### 11.2. Editores gráficos

Conservar la posición, el tamaño, el color y otras propiedades de elementos visuales.

### 11.3. Videojuegos

Guardar el estado de un personaje, su posición, inventario, puntuación o progreso en una partida.

### 11.4. Sistemas de configuración

Conservar una configuración anterior para poder restaurarla si una modificación produce resultados inesperados.

### 11.5. Herramientas de modelado

Guardar versiones de una escena o modelo para recuperar una edición anterior.

### 11.6. Flujos de trabajo

Conservar el estado de un proceso cuando sea necesario regresar a una etapa previa, siempre que la restauración sea compatible con las operaciones realizadas.

### 11.7. Aplicaciones empresariales

Guardar versiones de documentos, borradores o formularios para recuperar una edición previa.

---

## 12. Relación con los principios SOLID

### Single Responsibility Principle (SRP)

Cada participante tiene una responsabilidad delimitada:

- El Originator administra su estado.
- El Memento representa una instantánea.
- El Caretaker administra los recuerdos.

Esta separación ayuda a evitar que el objeto original tenga que gestionar también todo el historial.

### Open/Closed Principle (OCP)

La gestión de los recuerdos puede ampliarse con nuevas políticas de almacenamiento o recuperación sin modificar necesariamente la lógica interna del Originator.

Sin embargo, si cambia la estructura del estado que debe guardarse, puede ser necesario modificar la implementación del Memento.

### Liskov Substitution Principle (LSP)

Cuando se utilizan abstracciones o implementaciones alternativas de Memento, deben respetar el contrato esperado para guardar y restaurar estados.

No se debe asumir que cualquier objeto puede restaurarse correctamente a partir de cualquier recuerdo.

### Interface Segregation Principle (ISP)

El Caretaker puede trabajar con una interfaz mínima para almacenar y devolver recuerdos, sin necesitar métodos que permitan modificar el estado interno del Originator.

### Dependency Inversion Principle (DIP)

El historial puede depender de una abstracción del recuerdo en lugar de depender de los detalles de su representación.

En Java, este enfoque puede implementarse mediante una interfaz de Memento o un tipo abstracto que el Caretaker almacene sin interpretar.

---

## 13. Comparación con otros patrones de diseño

### 13.1. Memento vs. Command

| Memento | Command |
|---|---|
| Guarda una instantánea del estado de un objeto. | Encapsula una petición como un objeto. |
| Se centra en restaurar un estado anterior. | Se centra en representar y ejecutar una acción. |
| Conserva información necesaria para recuperar el estado. | Puede contener la información necesaria para ejecutar una operación. |
| Se utiliza para versiones y restauraciones. | Se utiliza para acciones, colas, macros e historiales de comandos. |

Ambos patrones pueden combinarse para implementar operaciones de deshacer. Command almacena la acción ejecutada y Memento conserva el estado anterior necesario para restaurarla.

### 13.2. Memento vs. State

| Memento | State |
|---|---|
| Conserva un estado para poder restaurarlo posteriormente. | Cambia el comportamiento de un objeto según su estado actual. |
| Se centra en la recuperación de estados anteriores. | Se centra en representar comportamientos asociados a estados. |
| Es útil para deshacer y versionar. | Es útil cuando un objeto tiene comportamientos diferentes según su situación. |

La diferencia fundamental es que Memento guarda estados; State organiza el comportamiento según el estado vigente.

### 13.3. Memento vs. Prototype

| Memento | Prototype |
|---|---|
| Guarda el estado de un objeto para restaurarlo. | Permite crear objetos nuevos copiando un prototipo existente. |
| Se centra en recuperar un estado anterior. | Se centra en la creación de objetos mediante clonación. |
| Normalmente se utiliza con el mismo Originator. | Se utiliza para crear nuevas instancias a partir de un objeto existente. |

### 13.4. Memento vs. Observer

| Memento | Observer |
|---|---|
| Conserva una instantánea del estado. | Notifica cambios a los observadores registrados. |
| Se centra en guardar y restaurar. | Se centra en la comunicación y propagación de eventos. |
| Permite recuperar versiones anteriores. | Permite que otros componentes reaccionen a modificaciones. |

Una aplicación puede utilizar Observer para detectar cambios en un documento y Memento para conservar versiones anteriores.

---

## 14. Buenas prácticas

- Guardar únicamente el estado necesario para restaurar el objeto correctamente.
- Mantener los recuerdos inmutables siempre que sea posible.
- Evitar exponer innecesariamente la representación interna del Memento.
- Decidir cuándo crear instantáneas para evitar guardar estados redundantes.
- Establecer un límite de recuerdos o una política de retención si el historial puede crecer indefinidamente.
- Utilizar copias adecuadas cuando el estado incluya objetos mutables.
- Definir con claridad la diferencia entre restaurar, deshacer y rehacer.
- Probar que la restauración recupera todos los atributos relevantes.
- No asumir que restaurar un objeto revierte automáticamente operaciones externas.
- Considerar técnicas como instantáneas incrementales o *copy-on-write* cuando el estado sea grande y el coste de copiarlo resulte elevado.

---

## 15. Conclusión

El patrón Memento es un patrón de comportamiento que permite capturar y restaurar el estado de un objeto sin exponer innecesariamente su estructura interna.

Su principal ventaja es que separa la gestión del estado del objeto de la administración de su historial, facilitando la implementación de operaciones de deshacer, restauración de versiones y recuperación de estados anteriores.

Es especialmente útil en editores de texto, aplicaciones gráficas, videojuegos y sistemas en los que el estado de un objeto debe conservarse durante un periodo de tiempo.

En Java, puede implementarse mediante un Originator que crea y restaura recuerdos, una clase Memento que representa el estado y un Caretaker que administra las instantáneas.

Memento permite guardar una instantánea del estado de un objeto para recuperarla más adelante. A diferencia de Command, que representa una acción, Memento conserva la información necesaria para restaurar un estado anterior.

En definitiva, **Memento permite conservar una instantánea del estado de un objeto para poder recuperarlo posteriormente sin que el administrador del historial necesite conocer sus detalles internos**.

---

## 16. Referencias

- *Design Patterns: Elements of Reusable Object-Oriented Software*, Erich Gamma, Richard Helm, Ralph Johnson y John Vlissides.
- Refactoring.Guru — Memento: https://refactoring.guru/design-patterns/memento
- Documentación oficial de Java — Clases y objetos: https://docs.oracle.com/javase/tutorial/java/javaOO/index.html
- Documentación oficial de Java — `Deque`: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Deque.html
- Documentación oficial de Java — `List`: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/List.html
