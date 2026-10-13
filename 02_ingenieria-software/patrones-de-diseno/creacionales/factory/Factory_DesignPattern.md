# Patrón de Diseño Factory (Creacional)

## 1. Introducción

El patrón de diseño **Factory** pertenece a la categoría de los patrones creacionales, cuyo objetivo es abstraer y organizar el proceso de creación de objetos.

El término *Factory* se utiliza habitualmente para describir varias soluciones relacionadas con la creación de objetos. Entre las más conocidas se encuentran:

- **Simple Factory:** encapsula la lógica de creación en una clase o método que decide qué objeto instanciar.
- **Factory Method:** define un método de creación que permite a las subclases decidir qué tipo concreto de producto instanciar.
- **Abstract Factory:** proporciona una interfaz para crear familias de objetos relacionados sin especificar sus clases concretas.

Aunque estas soluciones están relacionadas, no son exactamente el mismo patrón. En esta documentación se estudiará principalmente la **Simple Factory**, una forma sencilla y habitual de centralizar la creación de objetos, y se explicarán sus diferencias con Factory Method y Abstract Factory.

El propósito principal de Factory es **separar la lógica de creación de objetos del código que los utiliza**, reduciendo el acoplamiento entre el cliente y las implementaciones concretas.

## 2. Problema que resuelve

En una aplicación, es habitual que una clase necesite crear instancias de distintos tipos según una condición, una configuración o una entrada del usuario.

Sin un mecanismo de creación centralizado, la lógica de instanciación puede acabar distribuida por numerosas partes del código.

Por ejemplo:

```java
public class NotificacionService {

    public void enviar(String tipo, String mensaje) {

        if (tipo.equals("EMAIL")) {
            EmailNotificacion notificacion =
                new EmailNotificacion();

            notificacion.enviar(mensaje);

        } else if (tipo.equals("SMS")) {
            SmsNotificacion notificacion =
                new SmsNotificacion();

            notificacion.enviar(mensaje);
        }
    }
}
```

Este enfoque presenta varios inconvenientes:

- **Acoplamiento:** el servicio conoce las clases concretas que necesita crear.
- **Duplicación:** la misma lógica de creación puede repetirse en diferentes clases.
- **Dificultad de mantenimiento:** incorporar un nuevo tipo de notificación obliga a modificar el código que contiene las condiciones.
- **Complejidad creciente:** las estructuras `if-else` o `switch` pueden crecer a medida que aparecen nuevas variantes.
- **Dificultad para probar:** la creación directa de dependencias concretas puede complicar el aislamiento de componentes durante las pruebas.

Factory permite centralizar la selección y creación de los objetos para que el código cliente no tenga que encargarse directamente de estos detalles.

## 3. Solución propuesta

El patrón Factory introduce un componente responsable de crear objetos. El cliente solicita una instancia sin tener que conocer necesariamente los detalles de su construcción.

Por ejemplo:

```java
Notificacion notificacion =
    NotificacionFactory.crear("EMAIL");

notificacion.enviar("Tu pedido ha sido confirmado");
```

En este caso, el cliente trabaja con la abstracción `Notificacion`, mientras que `NotificacionFactory` se encarga de decidir qué implementación concreta debe crear.

La solución permite separar dos responsabilidades:

- **Creación:** decidir qué objeto instanciar y cómo obtenerlo.
- **Uso:** ejecutar las operaciones disponibles en el objeto creado.

## 4. Estructura del patrón

En una implementación habitual de Simple Factory intervienen los siguientes elementos:

- **Producto (Product):** interfaz o clase base que define las operaciones comunes.
- **Productos concretos (Concrete Products):** implementaciones específicas del producto.
- **Factory:** clase o método responsable de crear la implementación adecuada.
- **Cliente (Client):** componente que solicita el objeto y utiliza el producto a través de su abstracción.

### Diagrama conceptual

```text
                Cliente
                   |
                   v
                 Factory
                   |
             selecciona tipo
                   |
          +--------+--------+
          |                 |
          v                 v
   EmailNotificacion   SmsNotificacion
          |                 |
          +--------+--------+
                   |
                   v
             Notificacion
              (interfaz)
```

La idea fundamental es que el cliente dependa, siempre que sea posible, de una interfaz común y no de las clases concretas.

## 5. Ejemplo de implementación en Java

Imaginemos una aplicación que necesita enviar notificaciones por distintos canales: correo electrónico, SMS y notificaciones push.

### 5.1. Definición del producto

Primero se crea una interfaz que define el comportamiento común.

```java
public interface Notificacion {

    void enviar(String mensaje);
}
```

Esta interfaz permite que el código cliente utilice cualquier implementación sin necesitar conocer su clase concreta.

### 5.2. Productos concretos

Se implementa la interfaz para cada tipo de notificación.

**Notificación por correo electrónico:**

```java
public class EmailNotificacion implements Notificacion {

    @Override
    public void enviar(String mensaje) {
        System.out.println(
            "Enviando email: " + mensaje
        );
    }
}
```

**Notificación por SMS:**

```java
public class SmsNotificacion implements Notificacion {

    @Override
    public void enviar(String mensaje) {
        System.out.println(
            "Enviando SMS: " + mensaje
        );
    }
}
```

**Notificación push:**

```java
public class PushNotificacion implements Notificacion {

    @Override
    public void enviar(String mensaje) {
        System.out.println(
            "Enviando notificación push: " + mensaje
        );
    }
}
```

Cada clase implementa el mismo contrato, pero ofrece su propia forma de realizar la operación.

### 5.3. Implementación de la Factory

La Factory centraliza la creación de las distintas implementaciones.

```java
public class NotificacionFactory {

    public static Notificacion crear(String tipo) {

        if (tipo == null) {
            throw new IllegalArgumentException(
                "El tipo de notificación no puede ser nulo"
            );
        }

        return switch (tipo.toUpperCase()) {
            case "EMAIL" -> new EmailNotificacion();
            case "SMS" -> new SmsNotificacion();
            case "PUSH" -> new PushNotificacion();

            default -> throw new IllegalArgumentException(
                "Tipo de notificación no soportado: " + tipo
            );
        };
    }
}
```

La Factory recibe un identificador y devuelve una instancia que implementa `Notificacion`.

Si se solicita un tipo no soportado, se lanza una excepción explícita en lugar de devolver un objeto nulo o una implementación inesperada.

### 5.4. Uso desde el cliente

El cliente solicita la implementación que necesita y utiliza el objeto a través de la interfaz.

```java
public class Main {

    public static void main(String[] args) {

        Notificacion email =
            NotificacionFactory.crear("EMAIL");

        email.enviar("Tu pedido ha sido confirmado");

        Notificacion sms =
            NotificacionFactory.crear("SMS");

        sms.enviar("Tu código de verificación es 123456");
    }
}
```

Una posible salida sería:

```text
Enviando email: Tu pedido ha sido confirmado
Enviando SMS: Tu código de verificación es 123456
```

### 5.5. Explicación del ejemplo

1. El cliente solicita una notificación a la Factory.
2. La Factory interpreta el tipo solicitado.
3. Se crea la implementación concreta correspondiente.
4. La instancia se devuelve mediante la interfaz `Notificacion`.
5. El cliente ejecuta `enviar()` sin tener que conocer los detalles de creación.

Si en el futuro se incorpora un nuevo canal, se puede crear una nueva implementación de `Notificacion` y ampliar la lógica de creación centralizada.

No obstante, en esta versión de Simple Factory, la clase Factory debe modificarse para reconocer el nuevo tipo. Si se necesita una arquitectura que facilite extender las variantes sin modificar una factoría central, puede ser más adecuado utilizar Factory Method, un registro de proveedores o un mecanismo de inyección de dependencias.

## 6. Variante: Factory Method

**Factory Method** es un patrón creacional formal que define un método para crear un producto, delegando en las subclases la decisión sobre qué implementación concreta instanciar.

A diferencia de Simple Factory, no se limita a centralizar una estructura de condiciones: utiliza la herencia y el polimorfismo para delegar la creación.

### 6.1. Estructura

Sus participantes habituales son:

- **Producto:** interfaz común de los objetos creados.
- **Producto concreto:** implementación específica del producto.
- **Creador (Creator):** declara el método de creación y puede utilizarlo en su lógica.
- **Creador concreto:** implementa el método para devolver un producto concreto.

### 6.2. Ejemplo de implementación

Se reutiliza la interfaz `Notificacion` y sus implementaciones anteriores.

Primero, se define el creador abstracto:

```java
public abstract class NotificacionCreator {

    public abstract Notificacion crearNotificacion();

    public void enviarNotificacion(String mensaje) {
        Notificacion notificacion = crearNotificacion();
        notificacion.enviar(mensaje);
    }
}
```

Después, se crean los creadores concretos:

```java
public class EmailNotificacionCreator
        extends NotificacionCreator {

    @Override
    public Notificacion crearNotificacion() {
        return new EmailNotificacion();
    }
}
```

```java
public class SmsNotificacionCreator
        extends NotificacionCreator {

    @Override
    public Notificacion crearNotificacion() {
        return new SmsNotificacion();
    }
}
```

Finalmente, el cliente selecciona el creador apropiado:

```java
public class Main {

    public static void main(String[] args) {

        NotificacionCreator creator =
            new EmailNotificacionCreator();

        creator.enviarNotificacion(
            "Tu factura está disponible"
        );
    }
}
```

### 6.3. ¿Qué aporta esta variante?

La lógica de envío se encuentra en el creador base, pero la decisión sobre qué objeto crear corresponde a las subclases.

Esto permite incorporar nuevos creadores sin modificar el método `enviarNotificacion()`.

Sin embargo, añade clases y puede introducir una jerarquía de herencia innecesaria si el problema de creación es sencillo.

## 7. Diferencias entre Simple Factory, Factory Method y Abstract Factory

Aunque sus nombres son similares, resuelven necesidades distintas.

| Característica | Simple Factory | Factory Method | Abstract Factory |
|---|---|---|---|
| Objetivo | Centralizar la creación. | Delegar la creación en subclases. | Crear familias de objetos relacionados. |
| Mecanismo habitual | Método con condiciones o selección por tipo. | Herencia y redefinición de un método. | Interfaz de factoría con varios métodos de creación. |
| Número de productos | Normalmente una categoría de producto. | Normalmente una categoría de producto. | Varias categorías de productos relacionados. |
| Extensión | Puede requerir modificar la Factory. | Puede añadirse un creador concreto. | Puede añadirse una nueva familia de factorías. |
| Complejidad | Baja. | Media. | Media o alta. |
| Uso típico | Selección sencilla de una implementación. | Variantes de creación asociadas a diferentes creadores. | Familias compatibles de componentes. |

### ¿Cuándo elegir cada uno?

- Utiliza **Simple Factory** cuando quieras centralizar una lógica de creación sencilla.
- Utiliza **Factory Method** cuando diferentes creadores deban decidir qué producto concreto construir.
- Utiliza **Abstract Factory** cuando necesites crear varios tipos de objetos que deban pertenecer a una misma familia compatible.

## 8. Ventajas

- **Desacoplamiento:** el cliente puede depender de una interfaz en lugar de instanciar directamente clases concretas.
- **Centralización:** la lógica de creación se encuentra en un lugar definido.
- **Mantenibilidad:** se evita repetir la misma lógica de selección en múltiples componentes.
- **Reutilización:** distintos clientes pueden utilizar la misma Factory.
- **Facilidad de pruebas:** las abstracciones facilitan sustituir implementaciones cuando el diseño permite inyectar dependencias.
- **Control de creación:** se pueden aplicar validaciones, configuraciones o reglas antes de devolver una instancia.
- **Claridad:** el código cliente expresa qué tipo de producto necesita sin incluir todos los detalles de instanciación.

## 9. Desventajas

- **Complejidad adicional:** introduce clases o métodos que pueden ser innecesarios en escenarios simples.
- **Factory central demasiado grande:** una Simple Factory puede convertirse en una clase con demasiadas condiciones y responsabilidades.
- **Modificaciones al añadir productos:** una Factory basada en `if-else` o `switch` suele necesitar cambios para reconocer nuevos tipos.
- **Acoplamiento interno:** la Factory sigue conociendo las implementaciones concretas que crea.
- **Abstracción excesiva:** utilizar jerarquías de factorías cuando solo existe una implementación sencilla puede dificultar la comprensión del código.
- **Gestión de errores:** es necesario definir qué ocurre cuando se solicita un tipo desconocido o una configuración no válida.

## 10. Cuándo utilizar Factory

Este patrón resulta especialmente útil cuando:

- La aplicación debe seleccionar entre varias implementaciones de una misma interfaz.
- La lógica de creación se repite en distintos componentes.
- El proceso de instanciación requiere configuración adicional.
- Se desea evitar que el cliente conozca todas las clases concretas.
- La selección depende de una configuración, un tipo de operación o un contexto.
- Se necesita centralizar las reglas que determinan qué objeto debe crearse.

### Cuándo no utilizarlo

Puede ser innecesario cuando:

- Solo existe una implementación y no se prevén variantes relevantes.
- La construcción de objetos es trivial.
- Un constructor directo proporciona suficiente claridad.
- La Factory añade una capa de indirección sin reducir realmente el acoplamiento ni simplificar el código.

## 11. Ejemplos de uso en aplicaciones reales

### 11.1. Sistemas de notificaciones

Una aplicación puede crear implementaciones de correo electrónico, SMS o notificaciones push en función de las preferencias del usuario o de la configuración del sistema.

### 11.2. Sistemas de almacenamiento

Una aplicación puede seleccionar un proveedor de almacenamiento local, en la nube o remoto según su configuración.

### 11.3. Procesamiento de documentos

Una aplicación puede crear procesadores para PDF, CSV, XML o JSON, utilizando una interfaz común para las operaciones compartidas.

### 11.4. Integraciones externas

Una plataforma puede crear clientes para diferentes servicios externos, manteniendo separada la lógica que selecciona el proveedor.

### 11.5. Interfaces gráficas

Una aplicación puede crear componentes gráficos específicos para distintas plataformas o temas, especialmente cuando se necesita construir familias de componentes compatibles.

En estos casos, el patrón exacto dependerá del problema: una selección sencilla puede resolverse con Simple Factory, mientras que la creación de familias completas puede justificar Abstract Factory.

## 12. Relación con los principios SOLID

Factory puede ayudar a aplicar varios principios SOLID, aunque su uso no garantiza automáticamente un diseño correcto.

### Single Responsibility Principle (SRP)

Una Factory puede separar la responsabilidad de crear objetos de la responsabilidad de utilizarlos.

### Open/Closed Principle (OCP)

Una Simple Factory basada en condiciones puede necesitar modificaciones cada vez que aparece un nuevo tipo.

Factory Method y otras soluciones basadas en proveedores pueden facilitar la extensión mediante nuevas implementaciones, aunque esto depende del diseño concreto.

### Dependency Inversion Principle (DIP)

El cliente puede depender de una abstracción, como `Notificacion`, en lugar de depender directamente de `EmailNotificacion` o `SmsNotificacion`.

Es importante recordar que una Factory puede reducir el acoplamiento del cliente, pero la propia Factory normalmente necesita conocer las implementaciones concretas que crea.

## 13. Buenas prácticas

1. **Trabajar con interfaces o abstracciones:** siempre que tenga sentido, devolver un tipo común en lugar de exponer innecesariamente las clases concretas.
2. **Mantener la Factory sencilla:** evitar concentrar reglas de negocio ajenas a la creación de objetos.
3. **Definir el tratamiento de tipos desconocidos:** lanzar excepciones claras o devolver un resultado explícito, según el contrato de la aplicación.
4. **Evitar condiciones duplicadas:** centralizar la selección cuando varios componentes necesiten la misma lógica.
5. **Elegir la variante adecuada:** no utilizar Factory Method o Abstract Factory si una Simple Factory resuelve el problema con menos complejidad.
6. **Considerar la inyección de dependencias:** en aplicaciones grandes, un contenedor de dependencias puede encargarse de gestionar la creación y configuración de objetos.
7. **Evitar el uso indiscriminado de cadenas:** cuando los tipos posibles son conocidos de antemano, un `enum` puede ser más seguro que utilizar identificadores de texto arbitrarios.
8. **No confundir creación con ciclo de vida:** crear un objeto y gestionar su ámbito, reutilización o destrucción son responsabilidades distintas.
9. **Probar las distintas variantes:** comprobar tanto los tipos soportados como el comportamiento ante entradas inválidas.

## 14. Comparación con Builder

Factory y Builder son patrones creacionales, pero se centran en aspectos diferentes del proceso de creación.

| Característica | Factory | Builder |
|---|---|---|
| Objetivo principal | Seleccionar y crear una implementación. | Construir y configurar un objeto paso a paso. |
| Pregunta que responde | ¿Qué tipo de objeto debo crear? | ¿Cómo configuro este objeto? |
| Complejidad típica | Selección entre implementaciones. | Configuración de objetos con múltiples propiedades. |
| Ejemplo | Crear una notificación por email o SMS. | Construir una petición HTTP con cabeceras, URI y método. |

Ambos patrones pueden combinarse. Por ejemplo, una Factory podría seleccionar el tipo de cliente HTTP y un Builder podría configurar las opciones de la petición que ese cliente debe enviar.

## 15. Conclusión

Factory es una familia de soluciones creacionales que permite separar la creación de objetos de su utilización, facilitando el desacoplamiento y la organización del código.

La Simple Factory resulta útil para centralizar una lógica de selección sencilla. Factory Method permite delegar la decisión de creación en subclases, mientras que Abstract Factory facilita la creación de familias de objetos relacionados.

Su aplicación debe responder a una necesidad real de diseño. Una Factory bien planteada reduce la duplicación y oculta detalles de instanciación; una Factory innecesariamente compleja puede añadir abstracciones sin aportar beneficios suficientes.

Factory se centra en encapsular la decisión de qué objeto crear, a diferencia de Builder, que se centra en configurar un objeto paso a paso. Esta distinción te ayudará a identificar cuándo conviene aplicar cada patrón.

En definitiva, **Factory permite controlar y encapsular la creación de objetos para que el código cliente dependa menos de sus implementaciones concretas**.

## 16. Referencias

- [Refactoring Guru — Factory Method](https://refactoring.guru/design-patterns/factory-method)
- [Refactoring Guru — Abstract Factory](https://refactoring.guru/design-patterns/abstract-factory)
- [SourceMaking — Factory Method](https://sourcemaking.com/design_patterns/factory_method)
- [SourceMaking — Abstract Factory](https://sourcemaking.com/design_patterns/abstract_factory)
- [Oracle Java Documentation](https://docs.oracle.com/en/java/)
