# Patrón de Diseño Chain of Responsibility (Cadena de Responsabilidad)

## 1. Introducción

El patrón de diseño **Chain of Responsibility** pertenece a la categoría de los patrones de comportamiento (*Behavioral Design Patterns*) definidos por GoF (*Gang of Four*).

Su objetivo es **pasar una petición a través de una cadena de posibles receptores, de manera que cada uno pueda procesarla o delegarla en el siguiente**.

El emisor de la petición no necesita conocer qué objeto terminará procesándola. Solo tiene que enviarla al primer elemento de la cadena.

Cada elemento evalúa si puede gestionar la petición. Dependiendo del diseño, puede:

- Procesarla y detener la cadena.
- Rechazarla.
- Delegarla en el siguiente elemento.
- Ejecutar su propia lógica y continuar el procesamiento.

Este patrón es especialmente útil cuando existen varios posibles responsables de una operación y no queremos acoplar el código cliente a un receptor concreto.

### Objetivos principales

- Desacoplar al emisor de la petición de su receptor final.
- Separar las responsabilidades de procesamiento.
- Permitir incorporar o eliminar manejadores con facilidad.
- Organizar validaciones y reglas en una secuencia.
- Evitar estructuras condicionales demasiado extensas.
- Permitir que el procesamiento se distribuya entre varios objetos.

---

## 2. Problema que resuelve

Imaginemos que estamos desarrollando una aplicación web que recibe peticiones HTTP.

Antes de procesar una petición, el sistema debe comprobar diferentes condiciones:

1. Verificar que el usuario está autenticado.
2. Comprobar que tiene permisos suficientes.
3. Validar los datos recibidos.
4. Aplicar límites de uso.
5. Ejecutar la lógica de negocio.

Una implementación sencilla podría concentrar todas estas comprobaciones en un único método.

```java
public void procesarPeticion(Peticion peticion) {

    if (!estaAutenticado(peticion)) {
        throw new RuntimeException("No autenticado");
    }

    if (!tienePermisos(peticion)) {
        throw new RuntimeException("Sin permisos");
    }

    if (!datosValidos(peticion)) {
        throw new RuntimeException("Datos inválidos");
    }

    if (!dentroDelLimite(peticion)) {
        throw new RuntimeException("Límite superado");
    }

    ejecutarOperacion(peticion);
}
```

Aunque esta solución puede ser válida en aplicaciones pequeñas, el método puede crecer rápidamente cuando aparecen nuevas reglas o cambian las condiciones de procesamiento.

### Problemas principales

1. **Acumulación de responsabilidades:** un único método concentra demasiadas comprobaciones.
2. **Dificultad de mantenimiento:** añadir una validación implica modificar el método principal.
3. **Escasa reutilización:** las reglas están ligadas a un flujo de procesamiento concreto.
4. **Orden rígido:** reorganizar las comprobaciones puede resultar complicado.
5. **Acoplamiento:** el código que coordina la petición conoce todos los pasos que debe ejecutar.

Chain of Responsibility permite dividir estas responsabilidades en manejadores independientes conectados entre sí.

---

## 3. Solución propuesta

El patrón introduce una cadena de objetos denominados **handlers** o manejadores.

Cada manejador implementa una responsabilidad concreta y mantiene, normalmente, una referencia al siguiente manejador de la cadena.

Cuando llega una petición:

1. El emisor la entrega al primer manejador.
2. El manejador evalúa si debe procesarla.
3. Si no puede o no debe resolverla, la pasa al siguiente.
4. El proceso continúa hasta que se resuelve la petición o se alcanza el final de la cadena.

El comportamiento exacto depende de la implementación. Algunos manejadores pueden detener la cadena, mientras que otros pueden realizar una comprobación y continuar.

### Idea fundamental

**Una petición puede recorrer varios objetos sin que el emisor tenga que conocer cuál de ellos será responsable de procesarla.**

Esto permite incorporar nuevas responsabilidades sin modificar necesariamente los manejadores existentes.

---

## 4. Estructura del patrón

### 4.1. Handler

Define la interfaz común de los manejadores.

Puede incluir un método para procesar una petición y mecanismos para establecer el siguiente elemento de la cadena.

### 4.2. ConcreteHandler

Implementa una responsabilidad concreta.

Por ejemplo:

- `AutenticacionHandler`
- `PermisosHandler`
- `ValidacionHandler`
- `LimiteUsoHandler`

Cada manejador aplica sus propias reglas y decide si la petición puede continuar.

### 4.3. Cliente

Es el componente que construye la cadena y envía la petición al primer manejador.

El cliente no necesita conocer todos los detalles de procesamiento, aunque normalmente sí configura el orden de los manejadores.

### Diagrama conceptual

```text
             Cliente
                |
                v
       +----------------+
       |   Handler A    |
       +----------------+
                |
                | siguiente
                v
       +----------------+
       |   Handler B    |
       +----------------+
                |
                | siguiente
                v
       +----------------+
       |   Handler C    |
       +----------------+
                |
                v
             Fin de
             cadena
```

Cada manejador puede procesar la petición o delegarla al siguiente, según las reglas definidas.

---

## 5. Implementación en Java

Vamos a implementar una cadena de validación para procesar peticiones de acceso a una aplicación.

El proceso tendrá tres manejadores:

- Autenticación.
- Comprobación de permisos.
- Validación de los datos de la petición.

Si cualquiera de ellos rechaza la petición, el procesamiento se detendrá.

### 5.1. Crear la clase Peticion

Representa los datos necesarios para evaluar una petición.

```java
public class Peticion {

    private final boolean autenticado;
    private final boolean tienePermisos;
    private final String contenido;

    public Peticion(
        boolean autenticado,
        boolean tienePermisos,
        String contenido
    ) {
        this.autenticado = autenticado;
        this.tienePermisos = tienePermisos;
        this.contenido = contenido;
    }

    public boolean isAutenticado() {
        return autenticado;
    }

    public boolean tienePermisos() {
        return tienePermisos;
    }

    public String getContenido() {
        return contenido;
    }
}
```

La clase `Peticion` contiene los datos que necesitan los manejadores.

En una aplicación real, podría incluir información de identidad, parámetros, metadatos o el contexto de la operación.

### 5.2. Crear la clase Handler

Definimos una clase abstracta que permite conectar los manejadores.

```java
public abstract class Handler {

    private Handler siguiente;

    public Handler establecerSiguiente(Handler siguiente) {
        this.siguiente = siguiente;
        return siguiente;
    }

    public final boolean manejar(Peticion peticion) {
        if (!procesar(peticion)) {
            return false;
        }

        if (siguiente == null) {
            return true;
        }

        return siguiente.manejar(peticion);
    }

    protected abstract boolean procesar(Peticion peticion);
}
```

Esta clase implementa el mecanismo general de la cadena.

- `establecerSiguiente()` configura el siguiente manejador.
- `manejar()` ejecuta la lógica del manejador actual y continúa si la petición es aceptada.
- `procesar()` delega en cada subclase la implementación de su responsabilidad concreta.

En este diseño, devolver `false` detiene la cadena, mientras que devolver `true` permite continuar.

### 5.3. Crear el manejador de autenticación

```java
public class AutenticacionHandler extends Handler {

    @Override
    protected boolean procesar(Peticion peticion) {

        if (!peticion.isAutenticado()) {
            System.out.println(
                "Petición rechazada: usuario no autenticado"
            );

            return false;
        }

        System.out.println("Autenticación correcta");
        return true;
    }
}
```

Este manejador comprueba si el usuario está autenticado.

Si no lo está, devuelve `false` y la cadena se detiene.

### 5.4. Crear el manejador de permisos

```java
public class PermisosHandler extends Handler {

    @Override
    protected boolean procesar(Peticion peticion) {

        if (!peticion.tienePermisos()) {
            System.out.println(
                "Petición rechazada: permisos insuficientes"
            );

            return false;
        }

        System.out.println("Permisos correctos");
        return true;
    }
}
```

Este manejador se ejecuta únicamente si la autenticación ha sido satisfactoria.

### 5.5. Crear el manejador de validación

```java
public class ValidacionHandler extends Handler {

    @Override
    protected boolean procesar(Peticion peticion) {

        String contenido = peticion.getContenido();

        if (contenido == null || contenido.isBlank()) {
            System.out.println(
                "Petición rechazada: contenido vacío"
            );

            return false;
        }

        System.out.println("Validación correcta");
        return true;
    }
}
```

Este manejador comprueba que el contenido de la petición sea válido.

### 5.6. Construir y ejecutar la cadena

```java
public class Main {

    public static void main(String[] args) {

        Handler autenticacion =
            new AutenticacionHandler();

        Handler permisos =
            new PermisosHandler();

        Handler validacion =
            new ValidacionHandler();

        autenticacion
            .establecerSiguiente(permisos)
            .establecerSiguiente(validacion);

        Peticion peticion = new Peticion(
            true,
            true,
            "Crear un nuevo pedido"
        );

        boolean aceptada = autenticacion.manejar(peticion);

        if (aceptada) {
            System.out.println(
                "La petición puede continuar al servicio"
            );
        } else {
            System.out.println(
                "La petición no puede procesarse"
            );
        }
    }
}
```

Salida aproximada:

```text
Autenticación correcta
Permisos correctos
Validación correcta
La petición puede continuar al servicio
```

### ¿Qué hemos conseguido?

- Cada manejador tiene una responsabilidad concreta.
- La lógica de validación está distribuida en clases independientes.
- El cliente no necesita ejecutar manualmente cada comprobación.
- Podemos cambiar el orden o añadir manejadores al construir la cadena.
- Si un manejador rechaza la petición, los siguientes no se ejecutan.

En este ejemplo, todos los manejadores utilizan la misma abstracción y el resultado de cada comprobación determina si la petición puede continuar.

---

## 6. Añadir nuevos manejadores

Una ventaja importante del patrón es que podemos incorporar nuevas comprobaciones sin reescribir las existentes.

Por ejemplo, podemos añadir un manejador que limite el tamaño del contenido.

```java
public class LimiteContenidoHandler extends Handler {

    private final int maximoCaracteres;

    public LimiteContenidoHandler(int maximoCaracteres) {
        if (maximoCaracteres < 0) {
            throw new IllegalArgumentException(
                "El límite no puede ser negativo"
            );
        }

        this.maximoCaracteres = maximoCaracteres;
    }

    @Override
    protected boolean procesar(Peticion peticion) {

        String contenido = peticion.getContenido();

        if (contenido != null
                && contenido.length() > maximoCaracteres) {
            System.out.println(
                "Petición rechazada: contenido demasiado largo"
            );

            return false;
        }

        System.out.println("Tamaño del contenido correcto");
        return true;
    }
}
```

Podemos incorporarlo a la cadena:

```java
Handler autenticacion = new AutenticacionHandler();
Handler permisos = new PermisosHandler();
Handler validacion = new ValidacionHandler();

Handler limite = new LimiteContenidoHandler(200);

autenticacion
    .establecerSiguiente(permisos)
    .establecerSiguiente(validacion)
    .establecerSiguiente(limite);
```

La petición solo continuará si supera todas las comprobaciones.

El nuevo manejador no necesita conocer los detalles internos de los demás.

**Importante:** el orden de la cadena puede afectar al comportamiento. En este ejemplo se comprueba la autenticación antes de los permisos y de la validación del contenido. En una aplicación real, el orden debe elegirse según las reglas de seguridad y de negocio.

---

## 7. Variante: manejadores que procesan y continúan

No todas las cadenas tienen que detenerse cuando un manejador procesa una petición.

En algunos escenarios, un manejador puede ejecutar su responsabilidad y después delegar la petición al siguiente.

Por ejemplo, una cadena de procesamiento de registros podría:

1. Añadir una marca temporal.
2. Registrar el mensaje en un archivo.
3. Enviar una copia a un sistema de monitorización.

En este caso, cada manejador realiza una operación y permite que la petición continúe.

El flujo conceptual sería:

```text
Petición
   |
   v
Añadir marca temporal
   |
   v
Registrar en archivo
   |
   v
Enviar a monitorización
   |
   v
Fin
```

La diferencia con la implementación anterior es que los manejadores no compiten necesariamente por ser el único responsable. Todos pueden contribuir al procesamiento.

Por tanto, existen dos variantes habituales:

- **Procesamiento selectivo:** un manejador procesa la petición y puede detener la cadena.
- **Procesamiento acumulativo:** varios manejadores ejecutan sus tareas y continúan la cadena.

La elección depende del problema que se quiera resolver.

---

## 8. Ejemplo con lambdas y funciones en Java

Para cadenas sencillas, Java permite representar operaciones de procesamiento mediante interfaces funcionales.

Podemos definir una interfaz que reciba una petición y devuelva el resultado de su validación.

```java
import java.util.function.Predicate;

Predicate<Peticion> autenticacion =
    Peticion::isAutenticado;

Predicate<Peticion> permisos =
    Peticion::tienePermisos;

Predicate<Peticion> contenidoValido =
    peticion -> peticion.getContenido() != null
        && !peticion.getContenido().isBlank();
```

Después, podemos combinar las validaciones:

```java
Predicate<Peticion> cadena =
    autenticacion
        .and(permisos)
        .and(contenidoValido);

boolean aceptada = cadena.test(peticion);
```

Esta alternativa permite expresar una secuencia de comprobaciones de forma compacta.

Sin embargo, no es una implementación idéntica al patrón clásico GoF: utiliza composición de predicados, no una cadena explícita de objetos manejadores con delegación al siguiente.

Es apropiada cuando las operaciones son simples y se necesita combinar condiciones. Si cada paso tiene lógica compleja, requiere dependencias propias o debe controlar explícitamente la delegación, las clases de manejadores suelen resultar más claras.

---

## 9. Ventajas del patrón Chain of Responsibility

### 9.1. Bajo acoplamiento

El emisor no necesita conocer qué manejador procesará finalmente la petición.

Solo debe enviarla al primer elemento de la cadena.

### 9.2. Separación de responsabilidades

Cada manejador implementa una responsabilidad concreta.

Esto permite que las clases sean más pequeñas y fáciles de probar.

### 9.3. Extensibilidad

Podemos añadir nuevos manejadores sin modificar necesariamente los existentes.

### 9.4. Flexibilidad en el orden

La cadena puede configurarse en diferentes órdenes según las necesidades de la aplicación.

### 9.5. Reutilización

Un manejador puede reutilizarse en distintas cadenas cuando sus dependencias y su responsabilidad lo permitan.

### 9.6. Reducción de condicionales

Las comprobaciones pueden distribuirse entre objetos independientes, evitando que un único método acumule numerosas ramas condicionales.

---

## 10. Desventajas del patrón Chain of Responsibility

### 10.1. La petición puede quedar sin procesar

Si ningún manejador acepta la petición, esta puede llegar al final de la cadena sin ser atendida.

Es importante definir qué debe ocurrir en ese caso.

### 10.2. Dificultad para seguir el flujo

Cuando hay muchos manejadores, puede resultar más difícil entender el recorrido completo de una petición.

### 10.3. Dependencia del orden

Cambiar el orden de los manejadores puede modificar el comportamiento del sistema.

Esto es especialmente importante en validaciones de seguridad o procesos de negocio.

### 10.4. Coste de procesamiento

Una petición puede tener que pasar por varios manejadores antes de ser procesada o rechazada.

En cadenas grandes, conviene revisar el coste de cada paso.

### 10.5. Complejidad innecesaria

Para una secuencia fija y muy sencilla de dos o tres comprobaciones, una implementación directa puede resultar más fácil de entender.

El patrón aporta valor cuando existe una necesidad real de desacoplamiento, flexibilidad o composición de responsabilidades.

---

## 11. Casos de uso reales

### 11.1. Middleware web

Procesar peticiones HTTP mediante una secuencia de componentes de autenticación, autorización, registro y otras comprobaciones.

### 11.2. Validación de formularios

Comprobar campos obligatorios, formatos, límites y reglas de negocio.

### 11.3. Sistemas de autorización

Evaluar si una petición cumple diferentes requisitos antes de permitir una operación.

### 11.4. Registro y monitorización

Procesar mensajes mediante diferentes manejadores que añaden información, filtran eventos o los envían a distintos destinos.

### 11.5. Procesamiento de incidencias

Derivar una incidencia a distintos responsables según su tipo, prioridad o nivel de complejidad.

### 11.6. Sistemas de soporte técnico

Escalar una solicitud desde un primer nivel de atención hasta especialistas cuando no puede resolverse inicialmente.

### 11.7. Procesamiento de eventos

Aplicar una secuencia de filtros, transformaciones y validaciones a los eventos recibidos.

### 11.8. Flujos de aprobación

Pasar una solicitud por diferentes responsables o niveles de autorización hasta que se apruebe, rechace o escale.

---

## 12. Relación con los principios SOLID

### Single Responsibility Principle (SRP)

Cada manejador se encarga de una responsabilidad concreta.

Por ejemplo, el manejador de autenticación verifica la identidad, mientras que el de permisos evalúa la autorización.

### Open/Closed Principle (OCP)

Podemos incorporar nuevos manejadores sin modificar necesariamente los ya existentes.

Esto facilita extender la cadena mediante nuevas validaciones o transformaciones.

### Liskov Substitution Principle (LSP)

Las implementaciones de `Handler` deben respetar el contrato definido por la clase base.

Cualquier manejador válido debe poder incorporarse a la cadena sin romper el flujo esperado.

### Interface Segregation Principle (ISP)

Los componentes dependen de una interfaz común y especializada para el procesamiento de peticiones, en lugar de depender de una clase que reúna todas las validaciones.

### Dependency Inversion Principle (DIP)

El cliente puede trabajar con la abstracción `Handler`, sin conocer las implementaciones concretas de cada validación.

Esto permite sustituir o reorganizar los manejadores con menor acoplamiento.

---

## 13. Comparación con otros patrones de diseño

### 13.1. Chain of Responsibility vs. Decorator

| Chain of Responsibility | Decorator |
|---|---|
| Pasa una petición entre posibles manejadores. | Añade responsabilidades a un objeto envolviéndolo con otros objetos. |
| Un manejador puede procesar, delegar o detener la petición. | Los decoradores suelen delegar en el objeto envuelto y añadir comportamiento. |
| Se centra en decidir cómo se procesa una petición a través de la cadena. | Se centra en ampliar el comportamiento de un objeto. |

Ambos patrones pueden utilizar objetos enlazados, pero su intención es distinta.

### 13.2. Chain of Responsibility vs. Mediator

| Chain of Responsibility | Mediator |
|---|---|
| Una petición recorre una secuencia de manejadores. | Un mediador coordina las interacciones entre varios componentes. |
| El flujo suele seguir una cadena. | El mediador decide qué participantes intervienen y cómo colaboran. |
| Es útil para filtros, validaciones y escalado. | Es útil para coordinar objetos que interactúan entre sí. |

### 13.3. Chain of Responsibility vs. Command

| Chain of Responsibility | Command |
|---|---|
| Se centra en pasar una petición entre posibles responsables. | Encapsula una petición como un objeto ejecutable. |
| Puede permitir que varios manejadores evalúen la petición. | Representa una operación que puede ejecutarse independientemente. |
| La petición puede ser procesada por un elemento de la cadena o por varios, según la implementación. | El comando representa una acción concreta. |

Ambos patrones pueden combinarse. Por ejemplo, una cadena de manejadores puede recibir un objeto `Command` y decidir cómo procesarlo.

### 13.4. Chain of Responsibility vs. Strategy

| Chain of Responsibility | Strategy |
|---|---|
| Permite pasar una petición por varios manejadores. | Permite seleccionar un algoritmo entre varias alternativas. |
| El flujo puede incluir varios pasos consecutivos. | Normalmente se selecciona una estrategia para realizar una operación. |
| Es útil cuando el procesamiento depende de diferentes filtros o responsables. | Es útil cuando una operación tiene distintas implementaciones intercambiables. |

### 13.5. Chain of Responsibility vs. Observer

| Chain of Responsibility | Observer |
|---|---|
| La petición se transmite siguiendo una cadena de manejadores. | Un emisor notifica a los observadores registrados. |
| El procesamiento puede detenerse o continuar según cada manejador. | Varios observadores pueden reaccionar a una misma notificación. |
| Se centra en el procesamiento de una petición. | Se centra en comunicar que ha ocurrido un cambio o evento. |

---

## 14. Buenas prácticas

- Mantener cada manejador centrado en una responsabilidad concreta.
- Definir claramente qué significa aceptar, rechazar o delegar una petición.
- Establecer qué ocurre cuando ningún manejador procesa la petición.
- Configurar explícitamente el orden de la cadena cuando afecte al comportamiento.
- Evitar que los manejadores dependan directamente de las implementaciones de los demás.
- Diseñar los manejadores para que puedan probarse individualmente.
- Evitar efectos secundarios inesperados en manejadores que solo deberían validar.
- Registrar información útil para diagnosticar qué manejador ha rechazado una petición, evitando exponer datos sensibles.
- No confundir la validación de autorización con una simple comprobación de datos: las decisiones de seguridad deben aplicarse de forma consistente en todos los puntos de entrada relevantes.
- Evitar cadenas excesivamente largas o difíciles de seguir.
- Utilizar una secuencia directa de llamadas cuando el problema sea demasiado sencillo para justificar el patrón.

---

## 15. Conclusión

El patrón Chain of Responsibility es un patrón de comportamiento que permite pasar una petición a través de una secuencia de manejadores independientes.

Su principal ventaja es desacoplar al emisor de la petición de los objetos que pueden procesarla, distribuyendo las responsabilidades y facilitando la incorporación de nuevos pasos.

Es especialmente útil en middleware web, validaciones, autorización, registro de eventos, procesamiento de incidencias y flujos de aprobación.

En Java, puede implementarse mediante una interfaz o clase base `Handler`, varias implementaciones concretas y una referencia al siguiente manejador.

No obstante, conviene definir cuidadosamente el orden de la cadena y el comportamiento cuando ningún manejador procesa la petición.

Chain of Responsibility permite que una petición pase por distintos manejadores sin que el emisor tenga que conocer quién la procesará. Cada manejador puede resolverla, rechazarla o delegarla en el siguiente.

En definitiva, **Chain of Responsibility permite organizar el procesamiento de una petición mediante una cadena de objetos, cada uno responsable de decidir si la procesa o la delega en el siguiente**.

---

## 16. Referencias

- *Design Patterns: Elements of Reusable Object-Oriented Software*, Erich Gamma, Richard Helm, Ralph Johnson y John Vlissides.
- Refactoring.Guru — Chain of Responsibility: https://refactoring.guru/design-patterns/chain-of-responsibility
- Documentación oficial de Java — Interfaces: https://docs.oracle.com/javase/tutorial/java/IandI/createinterface.html
- Documentación oficial de Java — `Predicate`: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/function/Predicate.html
