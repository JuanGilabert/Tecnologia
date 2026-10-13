# Patrón de diseño estructural: Facade (Fachada)

## 1. Introducción

El patrón de diseño **Facade (Fachada)** es un patrón estructural que proporciona una interfaz simplificada para interactuar con un sistema complejo compuesto por múltiples clases, subsistemas o componentes.

Su objetivo principal es **reducir la complejidad que percibe el cliente, centralizando en una interfaz de alto nivel las operaciones necesarias para realizar una tarea**.

En lugar de obligar al cliente a conocer todos los componentes internos, sus dependencias y el orden en que deben ejecutarse, Facade ofrece un punto de entrada sencillo que coordina las operaciones necesarias.

Este patrón no pretende sustituir ni eliminar los subsistemas existentes, sino proporcionar una forma más cómoda y controlada de utilizarlos.

### Ejemplo conceptual

Imaginemos una tienda online que necesita procesar un pedido.

Para completar la compra, el sistema debe realizar varias operaciones:

- Validar el pedido.
- Comprobar el inventario.
- Procesar el pago.
- Preparar el envío.
- Enviar una confirmación al cliente.

Sin Facade, la clase que procesa el pedido podría tener que conocer todos esos componentes y coordinar directamente sus operaciones.

Con Facade, el cliente puede utilizar una operación de alto nivel, como:

```java
procesarPedido(pedido);
```

La fachada se encarga de coordinar los subsistemas internos.

```text
                 Cliente
                    |
                    v
             PedidoFacade
                    |
          +---------+---------+
          |         |         |
          v         v         v
      Inventario   Pago     Envíos
          |
          v
     Notificaciones
```

El cliente no necesita conocer todos los detalles internos para iniciar el proceso.

---

## 2. Problema que resuelve

Facade resuelve el problema de la **complejidad que aparece cuando un cliente debe interactuar directamente con varios componentes de un sistema**.

Este problema suele aparecer cuando:

- Un caso de uso requiere múltiples operaciones.
- Existen dependencias entre subsistemas.
- El orden de ejecución es importante.
- Los detalles de implementación cambian con frecuencia.
- Varias partes de la aplicación repiten la misma secuencia de llamadas.

### Ejemplo sin utilizar Facade

Imaginemos que tenemos una aplicación para reproducir contenido multimedia.

Para iniciar una película, el cliente debe interactuar con varias clases:

```java
public class Main {

    public static void main(String[] args) {

        Reproductor reproductor = new Reproductor();
        Proyector proyector = new Proyector();
        SistemaSonido sonido = new SistemaSonido();
        BibliotecaMultimedia biblioteca =
            new BibliotecaMultimedia();

        biblioteca.cargar("pelicula.mp4");
        proyector.encender();
        proyector.seleccionarEntrada("HDMI");
        sonido.encender();
        sonido.seleccionarModoCine();
        reproductor.iniciar("pelicula.mp4");
    }
}
```

Este enfoque funciona, pero el cliente conoce demasiados detalles del sistema.

Si cambia el proceso de inicio, habría que modificar potencialmente todos los lugares donde se repite esa secuencia.

Además, el cliente es responsable de coordinar componentes que quizá deberían permanecer encapsulados dentro de un subsistema.

**Facade permite resolver este problema proporcionando una operación de alto nivel que centraliza la coordinación.**

---

## 3. Solución propuesta

La solución consiste en crear una clase fachada que actúe como punto de entrada para las operaciones más habituales del sistema.

Esta clase conoce los subsistemas que necesita utilizar y coordina sus operaciones, mientras que el cliente depende de una interfaz más sencilla.

### Participantes del patrón

- **Client:** el código que necesita realizar una operación.
- **Facade:** proporciona una interfaz simplificada y coordina los subsistemas.
- **Subsystem classes:** implementan las funcionalidades específicas del sistema.

### Diagrama conceptual

```text
                   Cliente
                      |
                      v
                   Facade
                      |
          +-----------+-----------+
          |           |           |
          v           v           v
      Subsistema A Subsistema B Subsistema C
          |           |           |
          v           v           v
       Operación    Operación    Operación
```

El cliente conoce la fachada, mientras que esta conoce y utiliza los subsistemas necesarios.

Los subsistemas no tienen por qué conocer la existencia de la fachada y pueden seguir utilizándose directamente cuando exista una necesidad justificada.

**Importante:** Facade simplifica el acceso al sistema, pero no implica necesariamente que toda la lógica de negocio deba concentrarse en una única clase. La fachada debe coordinar las operaciones, no convertirse en una clase monolítica que asuma todas las responsabilidades.

---

## 4. Implementación completa en Java

Para entender el patrón, utilizaremos un sistema de compra online.

El objetivo será procesar un pedido mediante una única llamada, ocultando los detalles de inventario, pago, envío y notificación.

### 4.1. Subsistema: inventario

Esta clase comprueba si existe suficiente stock y permite reservar los productos.

```java
public class InventarioService {

    public boolean hayStock(String producto, int cantidad) {
        System.out.println(
            "Comprobando stock de " + producto
        );

        return cantidad > 0;
    }

    public void reservar(String producto, int cantidad) {
        System.out.println(
            "Reservando " + cantidad + " unidades de "
            + producto
        );
    }
}
```

En este ejemplo, la comprobación es simplificada: considera que hay stock cuando la cantidad es positiva. En un sistema real, tendría que consultar las existencias reales y reservarlas de forma atómica.

### 4.2. Subsistema: pagos

```java
public class PagoService {

    public boolean procesarPago(
            String cliente,
            double importe) {

        System.out.println(
            "Procesando pago de "
            + importe
            + " euros para "
            + cliente
        );

        return importe > 0;
    }
}
```

El subsistema de pagos representa la operación de cobro.

En una aplicación real, esta clase debería comunicarse con el proveedor de pagos y gestionar estados como autorización, rechazo, confirmación y posibles reintentos. El ejemplo utiliza `double` solo por simplicidad; para importes monetarios es preferible una representación decimal adecuada.

### 4.3. Subsistema: envío

```java
public class EnvioService {

    public void prepararEnvio(
            String producto,
            int cantidad) {

        System.out.println(
            "Preparando envío de "
            + cantidad
            + " unidades de "
            + producto
        );
    }
}
```

Esta clase encapsula la preparación del envío.

### 4.4. Subsistema: notificaciones

```java
public class NotificacionService {

    public void enviarConfirmacion(String cliente) {
        System.out.println(
            "Enviando confirmación al cliente: "
            + cliente
        );
    }
}
```

Este subsistema se encarga de comunicar al cliente que el pedido se ha procesado.

### 4.5. Fachada: PedidoFacade

La fachada centraliza la coordinación de los subsistemas.

```java
public class PedidoFacade {

    private final InventarioService inventarioService;
    private final PagoService pagoService;
    private final EnvioService envioService;
    private final NotificacionService notificacionService;

    public PedidoFacade(
            InventarioService inventarioService,
            PagoService pagoService,
            EnvioService envioService,
            NotificacionService notificacionService) {

        this.inventarioService = inventarioService;
        this.pagoService = pagoService;
        this.envioService = envioService;
        this.notificacionService = notificacionService;
    }

    public boolean procesarPedido(
            String cliente,
            String producto,
            int cantidad,
            double importe) {

        if (!inventarioService.hayStock(producto, cantidad)) {
            System.out.println("Stock insuficiente");
            return false;
        }

        boolean pagoCorrecto =
            pagoService.procesarPago(cliente, importe);

        if (!pagoCorrecto) {
            System.out.println("El pago ha sido rechazado");
            return false;
        }

        inventarioService.reservar(producto, cantidad);

        envioService.prepararEnvio(producto, cantidad);

        notificacionService.enviarConfirmacion(cliente);

        return true;
    }
}
```

La fachada proporciona un método de alto nivel y coordina las llamadas a los subsistemas.

En este ejemplo, el flujo se simplifica para ilustrar el patrón. Un sistema de producción debe resolver cuidadosamente las condiciones de concurrencia, la reserva de inventario, los fallos parciales y la compensación de operaciones. Por ejemplo, reservar el inventario después del cobro puede generar inconsistencias si la reserva falla.

### 4.6. Cliente

El cliente utiliza la fachada sin tener que instanciar ni coordinar cada subsistema.

```java
public class Main {

    public static void main(String[] args) {

        PedidoFacade pedidoFacade = new PedidoFacade(
            new InventarioService(),
            new PagoService(),
            new EnvioService(),
            new NotificacionService()
        );

        boolean procesado = pedidoFacade.procesarPedido(
            "Cliente123",
            "Portátil",
            1,
            1200.00
        );

        if (procesado) {
            System.out.println("Pedido procesado correctamente");
        } else {
            System.out.println("No se ha podido procesar el pedido");
        }
    }
}
```

Salida aproximada:

```text
Comprobando stock de Portátil
Procesando pago de 1200.0 euros para Cliente123
Reservando 1 unidades de Portátil
Preparando envío de 1 unidades de Portátil
Enviando confirmación al cliente: Cliente123
Pedido procesado correctamente
```

### 4.7. ¿Qué hemos conseguido?

La clase `Main` no necesita conocer el orden de ejecución ni las dependencias internas de los servicios.

Solo utiliza:

```java
pedidoFacade.procesarPedido(
    "Cliente123",
    "Portátil",
    1,
    1200.00
);
```

Si cambia la forma de coordinar los subsistemas, podemos modificar la fachada sin tener que actualizar cada cliente que utiliza ese proceso.

Además, los subsistemas siguen siendo independientes y pueden utilizarse directamente cuando sea necesario.

---

## 5. Estructura general del patrón

La estructura habitual de Facade puede representarse mediante un ejemplo genérico:

```java
// Subsistema A
public class SubsistemaA {

    public void operacionA() {
        System.out.println("Operación A");
    }
}

// Subsistema B
public class SubsistemaB {

    public void operacionB() {
        System.out.println("Operación B");
    }
}

// Subsistema C
public class SubsistemaC {

    public void operacionC() {
        System.out.println("Operación C");
    }
}

// Facade
public class Facade {

    private final SubsistemaA subsistemaA;
    private final SubsistemaB subsistemaB;
    private final SubsistemaC subsistemaC;

    public Facade() {
        this.subsistemaA = new SubsistemaA();
        this.subsistemaB = new SubsistemaB();
        this.subsistemaC = new SubsistemaC();
    }

    public void ejecutarProcesoCompleto() {
        subsistemaA.operacionA();
        subsistemaB.operacionB();
        subsistemaC.operacionC();
    }
}

// Client
public class Main {

    public static void main(String[] args) {
        Facade facade = new Facade();
        facade.ejecutarProcesoCompleto();
    }
}
```

Este ejemplo muestra cómo una sola operación de la fachada puede coordinar varias operaciones internas.

En aplicaciones reales, suele ser preferible inyectar las dependencias en la fachada, en lugar de crearlas directamente dentro de ella. Esto facilita las pruebas, la configuración y la sustitución de implementaciones.

---

## 6. Variantes y formas de utilizar Facade

Facade no requiere una única forma de implementación. Puede adaptarse a diferentes necesidades.

### 6.1. Fachada simple

Proporciona una única interfaz de alto nivel para operaciones habituales.

Por ejemplo, `PedidoFacade` puede coordinar el proceso de compra.

### 6.2. Fachadas específicas por caso de uso

En sistemas grandes, puede resultar más claro crear varias fachadas pequeñas, cada una orientada a un proceso.

Por ejemplo:

- `PedidoFacade`
- `AutenticacionFacade`
- `GeneracionInformeFacade`

Esto evita concentrar demasiadas responsabilidades en una única clase.

### 6.3. Fachada con dependencias inyectadas

La fachada recibe sus subsistemas mediante el constructor.

```java
public class InformeFacade {

    private final DatosService datosService;
    private final FormatoService formatoService;
    private final ExportacionService exportacionService;

    public InformeFacade(
            DatosService datosService,
            FormatoService formatoService,
            ExportacionService exportacionService) {

        this.datosService = datosService;
        this.formatoService = formatoService;
        this.exportacionService = exportacionService;
    }

    // Los métodos de alto nivel coordinan los servicios.
}
```

Este enfoque favorece el desacoplamiento y facilita la sustitución de dependencias en pruebas unitarias.

### 6.4. Fachada como punto de entrada, no como barrera obligatoria

La fachada puede ofrecer la forma recomendada de utilizar un subsistema, pero no siempre necesita prohibir el acceso directo a las clases internas.

La decisión depende del diseño y de las necesidades de encapsulación. Si se necesita imponer una política de acceso estricta, habrá que complementarla con mecanismos de visibilidad, módulos o controles de arquitectura.

---

## 7. Ejemplo práctico: generación de informes

Imaginemos una aplicación que permite generar informes en PDF a partir de datos almacenados.

Para completar el proceso, se necesita:

1. Obtener los datos.
2. Transformarlos al formato esperado.
3. Generar el documento.
4. Guardar el archivo.

### 7.1. Subsistema de datos

```java
public class DatosService {

    public String obtenerDatos() {
        return "Ventas: 15000; Pedidos: 240";
    }
}
```

### 7.2. Subsistema de formato

```java
public class FormatoService {

    public String prepararDatos(String datos) {
        return "INFORME\n" + datos;
    }
}
```

### 7.3. Subsistema de generación

```java
public class PdfService {

    public byte[] generarPdf(String contenido) {
        System.out.println(
            "Generando PDF con contenido: " + contenido
        );

        // Resultado ficticio para ilustrar el patrón.
        return contenido.getBytes(
            java.nio.charset.StandardCharsets.UTF_8
        );
    }
}
```

Esta implementación no genera un PDF real; devuelve los bytes del texto para mantener el ejemplo centrado en Facade.

### 7.4. Subsistema de almacenamiento

```java
public class AlmacenamientoService {

    public void guardar(String nombre, byte[] contenido) {
        System.out.println(
            "Guardando archivo: " + nombre
            + " (" + contenido.length + " bytes)"
        );
    }
}
```

### 7.5. Fachada

```java
public class InformeFacade {

    private final DatosService datosService;
    private final FormatoService formatoService;
    private final PdfService pdfService;
    private final AlmacenamientoService almacenamientoService;

    public InformeFacade(
            DatosService datosService,
            FormatoService formatoService,
            PdfService pdfService,
            AlmacenamientoService almacenamientoService) {

        this.datosService = datosService;
        this.formatoService = formatoService;
        this.pdfService = pdfService;
        this.almacenamientoService = almacenamientoService;
    }

    public void generarInformeMensual() {

        String datos = datosService.obtenerDatos();

        String contenido =
            formatoService.prepararDatos(datos);

        byte[] pdf = pdfService.generarPdf(contenido);

        almacenamientoService.guardar(
            "informe-mensual.pdf",
            pdf
        );
    }
}
```

### 7.6. Uso desde el cliente

```java
public class Main {

    public static void main(String[] args) {

        InformeFacade informeFacade = new InformeFacade(
            new DatosService(),
            new FormatoService(),
            new PdfService(),
            new AlmacenamientoService()
        );

        informeFacade.generarInformeMensual();
    }
}
```

El cliente solo necesita invocar `generarInformeMensual()`.

No necesita conocer la secuencia de pasos ni coordinar directamente los subsistemas.

---

## 8. Ventajas y desventajas

### Ventajas

1. **Simplificación de la interfaz:** reduce la cantidad de operaciones que el cliente necesita conocer.
2. **Menor acoplamiento:** los clientes dependen de una interfaz de alto nivel en lugar de múltiples subsistemas.
3. **Centralización de la coordinación:** agrupa en un punto los pasos necesarios para realizar una operación.
4. **Mejor mantenibilidad:** permite cambiar la secuencia interna sin modificar todos los clientes.
5. **Facilidad de uso:** facilita la utilización de bibliotecas o sistemas complejos.
6. **Mejor organización:** permite definir puntos de entrada claros para los casos de uso más frecuentes.
7. **Compatibilidad con sistemas existentes:** puede proporcionar una interfaz sencilla sobre un conjunto de clases que no fueron diseñadas para trabajar juntas de forma simple.

### Desventajas

1. **Riesgo de una fachada demasiado grande:** si concentra demasiados casos de uso, puede convertirse en una clase difícil de mantener.
2. **Posible duplicación de abstracciones:** una fachada innecesaria puede añadir una capa que aporta poco valor.
3. **No elimina la complejidad interna:** los subsistemas siguen existiendo y necesitan un diseño adecuado.
4. **Puede ocultar detalles importantes:** una interfaz excesivamente simplificada puede impedir que el cliente gestione casos que requieren más control.
5. **No garantiza transacciones:** coordinar varias operaciones no significa que todas se ejecuten de manera atómica.
6. **Puede convertirse en un punto de acoplamiento:** si muchos clientes dependen de una fachada con demasiadas responsabilidades, sus cambios pueden afectar a gran parte del sistema.

Facade es especialmente útil cuando existe una secuencia de operaciones repetida o una complejidad que conviene ocultar. No debería utilizarse solo para envolver cada clase con otra clase adicional.

---

## 9. Relación con los principios SOLID

### Single Responsibility Principle (SRP)

Una fachada debería tener una responsabilidad clara, como coordinar el proceso de compra o la generación de informes.

Si una fachada gestiona procesos completamente independientes, conviene dividirla en varias fachadas más pequeñas.

### Open/Closed Principle (OCP)

Podemos modificar la implementación interna de los subsistemas sin cambiar necesariamente la interfaz pública de la fachada.

Sin embargo, si incorporamos nuevos requisitos que cambian el contrato de alto nivel, puede ser necesario ampliar o modificar esa interfaz.

### Liskov Substitution Principle (LSP)

LSP no es el principio más directamente relacionado con Facade, ya que el patrón no requiere una jerarquía de herencia.

Aun así, si existen varias implementaciones de una interfaz de fachada, todas deberían respetar el mismo contrato para poder sustituirse correctamente.

### Interface Segregation Principle (ISP)

Las fachadas deberían ofrecer operaciones de alto nivel que respondan a necesidades concretas del cliente, sin exponer una interfaz excesivamente grande.

En sistemas complejos, varias fachadas específicas pueden resultar más adecuadas que una única interfaz con multitud de métodos no relacionados.

### Dependency Inversion Principle (DIP)

Una fachada puede depender de abstracciones de los subsistemas en lugar de depender directamente de implementaciones concretas.

La inyección de dependencias facilita este enfoque y permite sustituir los componentes en pruebas o en diferentes configuraciones de la aplicación.

---

## 10. Diferencias con otros patrones estructurales

### Facade vs. Adapter

Aunque ambos patrones pueden envolver componentes existentes, su intención es diferente.

- **Facade:** simplifica el acceso a un sistema proporcionando una interfaz de alto nivel.
- **Adapter:** convierte una interfaz en otra compatible con lo que necesita el cliente.

Por ejemplo, `PedidoFacade` coordina varios servicios para procesar una compra. Un adaptador de pagos traduce la interfaz de un proveedor externo a la interfaz esperada por la aplicación.

### Facade vs. Proxy

- **Facade:** simplifica el uso de un conjunto de componentes.
- **Proxy:** controla el acceso a un objeto concreto, por ejemplo, mediante autorización, acceso remoto o carga diferida.

Una fachada suele coordinar múltiples subsistemas; un proxy suele representar o controlar el acceso a un objeto o servicio.

### Facade vs. Decorator

- **Facade:** proporciona una interfaz simplificada para un sistema.
- **Decorator:** añade responsabilidades a un objeto manteniendo una interfaz compatible.

Facade reduce la complejidad de uso; Decorator amplía el comportamiento de un componente.

### Facade vs. Mediator

- **Facade:** ofrece una interfaz simplificada para que el cliente utilice un sistema.
- **Mediator:** centraliza la comunicación entre objetos que necesitan interactuar entre sí.

Facade organiza el acceso desde el exterior. Mediator coordina las interacciones entre colegas dentro de un conjunto de componentes.

### Facade vs. Abstract Factory

- **Facade:** simplifica la utilización de un sistema.
- **Abstract Factory:** proporciona una interfaz para crear familias de objetos relacionados sin depender de sus clases concretas.

Ambos pueden utilizarse juntos: una fachada puede emplear una fábrica abstracta para crear los componentes que necesita.

---

## 11. Casos de uso habituales

Facade resulta especialmente útil en los siguientes escenarios:

- Simplificación de bibliotecas con muchas clases.
- Coordinación de procesos empresariales.
- Integración de sistemas externos.
- Inicialización de subsistemas complejos.
- Generación de informes.
- Procesos de compra y gestión de pedidos.
- Operaciones que combinan acceso a datos, transformación y almacenamiento.
- Creación de puntos de entrada sencillos para módulos internos.
- Migraciones progresivas de sistemas heredados.

### Ejemplo en una arquitectura por capas

Una aplicación puede exponer un servicio de aplicación que coordine las operaciones necesarias para completar un caso de uso.

Por ejemplo:

```text
Controlador
    |
    v
Servicio de aplicación / Fachada
    |
    +----> Servicio de inventario
    |
    +----> Servicio de pagos
    |
    +----> Servicio de envíos
```

Este diseño permite que el controlador no conozca todos los detalles internos del proceso.

No obstante, un servicio de aplicación y una fachada no son conceptos idénticos: un servicio de aplicación suele implementar un caso de uso y sus reglas de coordinación, mientras que Facade es un patrón estructural para simplificar el acceso a uno o varios subsistemas. Un mismo componente puede desempeñar ambas funciones si el diseño lo justifica.

---

## 12. Buenas prácticas

1. **Mantén una interfaz de alto nivel clara:** cada operación debería representar una tarea comprensible para el cliente.
2. **Evita fachadas gigantes:** separa procesos que tengan responsabilidades diferentes.
3. **Inyecta las dependencias:** facilita las pruebas y la sustitución de subsistemas.
4. **No dupliques la lógica de negocio:** la fachada debería coordinar servicios, no convertirse en el lugar donde se concentra toda la lógica.
5. **Define la gestión de errores:** especifica qué sucede si falla un subsistema durante una operación.
6. **Ten en cuenta los fallos parciales:** varias llamadas coordinadas no son automáticamente una transacción atómica.
7. **Mantén los subsistemas cohesivos:** la fachada simplifica su uso, pero no sustituye un diseño interno correcto.
8. **Evita ocultar información necesaria:** si un cliente requiere operaciones avanzadas, considera proporcionar otra interfaz adecuada.
9. **Documenta el contrato:** explica qué garantiza cada operación y qué errores puede producir.
10. **Evalúa si aporta valor:** no crees una fachada únicamente para añadir una capa más al sistema.

---

## 13. Pruebas recomendadas

Las pruebas deberían comprobar que la fachada coordina los subsistemas en el orden adecuado y responde correctamente a los distintos resultados.

En el ejemplo del pedido, conviene verificar que:

- Si no hay stock, el proceso termina sin continuar con las operaciones posteriores.
- Si el pago falla, el pedido no continúa con el envío.
- Si todas las operaciones tienen éxito, se ejecutan los pasos esperados.
- Los errores de los subsistemas se gestionan de acuerdo con el contrato definido.
- La fachada no realiza directamente tareas que corresponden a los subsistemas.

Para ello, pueden utilizarse pruebas unitarias con JUnit y Mockito, sustituyendo los servicios reales por dobles de prueba.

En procesos críticos, también conviene realizar pruebas de integración para verificar la interacción con las bases de datos, los proveedores de pago y los servicios externos.

---

## 14. Conclusión

El patrón **Facade (Fachada)** permite simplificar la utilización de un sistema complejo mediante una interfaz de alto nivel que coordina las operaciones de sus subsistemas.

Su principal beneficio consiste en reducir el acoplamiento entre los clientes y los detalles internos de la implementación, haciendo que los casos de uso habituales sean más fáciles de ejecutar y mantener.

En Java, suele implementarse mediante una clase que recibe o utiliza los subsistemas necesarios y expone métodos que coordinan las operaciones requeridas.

Es especialmente útil cuando varios componentes deben colaborar para completar una tarea y no queremos que todos los clientes tengan que conocer la secuencia de llamadas.

La idea fundamental es: **proporcionar una interfaz sencilla para utilizar un sistema complejo, ocultando los detalles de coordinación sin necesidad de eliminar ni sustituir sus subsistemas internos.**

---

## 15. Referencias

- **Design Patterns: Elements of Reusable Object-Oriented Software**, de Erich Gamma, Richard Helm, Ralph Johnson y John Vlissides (Gang of Four).
- Refactoring.Guru — Facade: https://refactoring.guru/design-patterns/facade
- Java Documentation: https://docs.oracle.com/en/java/
- SourceMaking — Facade Design Pattern: https://sourcemaking.com/design_patterns/facade
