# SOLID: principios y buenas prácticas de diseño de software

## 1. Introducción

**SOLID** es un acrónimo que agrupa cinco principios de diseño de software orientado a objetos. Su objetivo es ayudar a construir sistemas fáciles de entender, probar, modificar y ampliar sin introducir cambios innecesarios en otras partes de la aplicación.

Los cinco principios son:

| Letra | Principio | Significado |
|---|---|---|
| S | Single Responsibility Principle (SRP) | Principio de responsabilidad única. |
| O | Open/Closed Principle (OCP) | Principio abierto/cerrado. |
| L | Liskov Substitution Principle (LSP) | Principio de sustitución de Liskov. |
| I | Interface Segregation Principle (ISP) | Principio de segregación de interfaces. |
| D | Dependency Inversion Principle (DIP) | Principio de inversión de dependencias. |

Estos principios están relacionados con el diseño de clases, interfaces, módulos y dependencias.

No son reglas absolutas que deban aplicarse mecánicamente a cada clase. Son criterios para evaluar si una solución está bien estructurada y si los cambios previsibles podrán realizarse sin provocar efectos secundarios innecesarios.

Los principios SOLID son cinco principios fundamentales del diseño orientado a objetos que ayudan a construir software mantenible, extensible, comprobable y con un acoplamiento controlado. Se complementan muy bien con los principios como Clean Code, DRY, KISS y YAGNI.

### Objetivos principales

- Reducir el acoplamiento entre componentes.
- Mejorar la cohesión de las clases.
- Facilitar las pruebas unitarias.
- Permitir la extensión del comportamiento con cambios localizados.
- Evitar jerarquías de herencia incorrectas.
- Definir contratos pequeños y coherentes.
- Facilitar el mantenimiento y la evolución del software.
- Mejorar la separación de responsabilidades.
- Hacer que las dependencias sean explícitas y controlables.

### Idea fundamental

> El software debería organizarse de manera que los cambios en un requisito tengan un impacto limitado y predecible sobre el resto del sistema.

---

## 2. ¿Qué problema resuelven los principios SOLID?

Imaginemos una aplicación de comercio electrónico con una clase que calcula precios, aplica descuentos, guarda pedidos en una base de datos, envía correos y genera facturas.

```java
public class OrderService {

    public BigDecimal calculateTotal(Order order) {
        // Calcular total.
        return BigDecimal.ZERO;
    }

    public void saveToDatabase(Order order) {
        // Guardar el pedido.
    }

    public void sendConfirmationEmail(Order order) {
        // Enviar correo.
    }

    public void generateInvoice(Order order) {
        // Generar factura.
    }
}
```

Aunque el ejemplo es sencillo, la clase acumula responsabilidades que evolucionan por motivos diferentes.

Si cambia la base de datos, podría ser necesario modificarla. Si cambia el sistema de correo, también. Si cambia la política de facturación, volvería a modificarse.

Además, las pruebas de una operación pueden depender de recursos externos que no son necesarios para comprobar su lógica.

SOLID ayuda a detectar y reducir problemas como estos:

- Clases que concentran demasiadas responsabilidades.
- Condicionales que crecen cada vez que aparece un nuevo comportamiento.
- Herencias que no respetan el contrato de la clase base.
- Interfaces demasiado grandes.
- Componentes de alto nivel que dependen directamente de detalles de infraestructura.
- Código difícil de probar sin conectarse a servicios reales.
- Cambios que se propagan por demasiados módulos.

Una posible estructura más organizada sería:

```text
OrderService
    |
    +--> OrderRepository
    |
    +--> NotificationService
    |
    +--> InvoiceGenerator
```

Cada componente tiene un contrato definido y una responsabilidad más clara. El diseño concreto dependerá del tamaño y los requisitos del sistema.

---

# 3. S — Single Responsibility Principle (SRP)

## 3.1. Definición

El **Principio de Responsabilidad Única** establece que un módulo debe tener una única razón para cambiar.

En la práctica, esto significa que una clase debería concentrarse en una responsabilidad coherente y no mezclar funciones que evolucionan por motivos independientes.

No significa que una clase solo pueda tener un método. Una clase puede tener muchos métodos y seguir respetando SRP si todos contribuyen a una misma responsabilidad.

### Ejemplo conceptual

Una clase `Invoice` puede representar una factura y calcular su importe.

Sin embargo, guardar la factura en una base de datos, enviarla por correo y generar un PDF son responsabilidades diferentes que pueden evolucionar independientemente de las reglas propias de la factura.

## 3.2. Implementación incorrecta

```java
public class Invoice {

    public BigDecimal calculateTotal() {
        // Calcular el importe de la factura.
        return BigDecimal.ZERO;
    }

    public void saveToDatabase() {
        // Persistir la factura.
    }

    public void sendEmail() {
        // Enviar la factura por correo.
    }

    public void generatePdf() {
        // Generar un documento PDF.
    }
}
```

Esta clase combina:

- Reglas de cálculo.
- Persistencia.
- Comunicación por correo.
- Generación de documentos.

Un cambio en cualquiera de estas áreas puede obligar a modificar la misma clase.

## 3.3. Implementación mejorada

Separamos las responsabilidades.

### Entidad de factura

```java
import java.math.BigDecimal;
import java.util.List;

public class Invoice {

    private final List<InvoiceLine> lines;

    public Invoice(List<InvoiceLine> lines) {
        this.lines = List.copyOf(lines);
    }

    public BigDecimal calculateTotal() {
        return lines.stream()
                .map(InvoiceLine::total)
                .reduce(BigDecimal.ZERO, BigDecimal::add);
    }
}
```

### Línea de factura

```java
import java.math.BigDecimal;

public record InvoiceLine(
        String description,
        int quantity,
        BigDecimal unitPrice
) {
    public InvoiceLine {
        if (quantity < 0) {
            throw new IllegalArgumentException(
                    "La cantidad no puede ser negativa"
            );
        }

        if (unitPrice == null || unitPrice.signum() < 0) {
            throw new IllegalArgumentException(
                    "El precio no puede ser negativo"
            );
        }
    }

    public BigDecimal total() {
        return unitPrice.multiply(BigDecimal.valueOf(quantity));
    }
}
```

### Persistencia

```java
public interface InvoiceRepository {

    void save(Invoice invoice);
}
```

### Generación de documentos

```java
public interface InvoiceDocumentGenerator {

    byte[] generate(Invoice invoice);
}
```

### Notificaciones

```java
public interface InvoiceNotifier {

    void send(Invoice invoice);
}
```

Ahora cada componente tiene una responsabilidad más específica.

`Invoice` mantiene el comportamiento propio de la factura, mientras que los contratos de persistencia, generación de documentos y notificaciones permiten separar otras preocupaciones.

## 3.4. Diagrama conceptual

```text
                    +------------------+
                    |     Invoice      |
                    |------------------|
                    | Calcular total   |
                    | Mantener reglas  |
                    +------------------+
                             |
             +---------------+---------------+
             |               |               |
             v               v               v
    +----------------+ +-------------+ +----------------+
    | Invoice        | | Invoice     | | Invoice        |
    | Repository     | | Document    | | Notifier       |
    |                | | Generator   | |                |
    +----------------+ +-------------+ +----------------+
       Persistencia       Documento        Notificación
```

El diagrama representa responsabilidades separadas; no implica que la entidad deba invocar directamente todos esos componentes.

## 3.5. Beneficios de SRP

- Las clases son más fáciles de comprender.
- Los cambios quedan mejor localizados.
- Las pruebas pueden centrarse en una responsabilidad.
- Se reduce el acoplamiento entre funciones diferentes.
- Los componentes pueden reutilizarse con mayor facilidad.
- Los errores son más fáciles de localizar.

## 3.6. Errores frecuentes

**Error: crear una clase por cada método.**

Separar responsabilidades no significa fragmentar el código hasta que cada operación tenga su propia clase sin necesidad.

**Error: interpretar «una responsabilidad» como «una única función».**

Una clase puede tener múltiples métodos relacionados con un mismo concepto.

**Error: crear una clase universal de servicios.**

Una clase como `ApplicationManager` que coordina persistencia, correo, seguridad, cálculos y reportes puede terminar concentrando demasiados motivos de cambio.

## 3.7. Cuándo aplicar SRP

- Cuando una clase tiene demasiadas responsabilidades.
- Cuando cambios independientes obligan a modificar el mismo componente.
- Cuando las pruebas requieren configurar muchos recursos no relacionados.
- Cuando una clase combina lógica de negocio con detalles de infraestructura.
- Cuando el nombre de una clase no describe claramente su función.

---

# 4. O — Open/Closed Principle (OCP)

## 4.1. Definición

El **Principio Abierto/Cerrado** establece que las entidades de software deberían estar abiertas a la extensión, pero cerradas a la modificación.

Esto significa que, cuando aparece una nueva variante de comportamiento, deberíamos poder incorporarla sin reescribir continuamente el código estable que ya funciona.

No significa que el código nunca deba modificarse. Los errores y los cambios reales de requisitos pueden exigir modificaciones. El objetivo es evitar que cada nueva variante obligue a alterar numerosos condicionales existentes.

## 4.2. Implementación incorrecta

Supongamos que una aplicación calcula descuentos según el tipo de cliente.

```java
public class DiscountCalculator {

    public BigDecimal calculate(
            BigDecimal amount,
            String customerType
    ) {
        if ("REGULAR".equals(customerType)) {
            return amount.multiply(new BigDecimal("0.05"));
        }

        if ("PREMIUM".equals(customerType)) {
            return amount.multiply(new BigDecimal("0.10"));
        }

        if ("VIP".equals(customerType)) {
            return amount.multiply(new BigDecimal("0.20"));
        }

        return BigDecimal.ZERO;
    }
}
```

Cada vez que aparece un nuevo tipo de cliente, hay que modificar `DiscountCalculator`.

A medida que se agregan reglas, el método puede convertirse en una estructura de condicionales extensa y difícil de probar.

## 4.3. Implementación mejorada con Strategy

Creamos un contrato común para las políticas de descuento.

```java
public interface DiscountPolicy {

    BigDecimal calculate(BigDecimal amount);
}
```

Implementación para clientes regulares:

```java
public class RegularDiscountPolicy implements DiscountPolicy {

    @Override
    public BigDecimal calculate(BigDecimal amount) {
        return amount.multiply(new BigDecimal("0.05"));
    }
}
```

Implementación para clientes premium:

```java
public class PremiumDiscountPolicy implements DiscountPolicy {

    @Override
    public BigDecimal calculate(BigDecimal amount) {
        return amount.multiply(new BigDecimal("0.10"));
    }
}
```

Implementación para clientes VIP:

```java
public class VipDiscountPolicy implements DiscountPolicy {

    @Override
    public BigDecimal calculate(BigDecimal amount) {
        return amount.multiply(new BigDecimal("0.20"));
    }
}
```

El servicio utiliza la abstracción:

```java
public class DiscountCalculator {

    public BigDecimal calculate(
            BigDecimal amount,
            DiscountPolicy policy
    ) {
        return policy.calculate(amount);
    }
}
```

Uso:

```java
DiscountCalculator calculator = new DiscountCalculator();

DiscountPolicy policy = new PremiumDiscountPolicy();

BigDecimal discount = calculator.calculate(
        new BigDecimal("200.00"),
        policy
);
```

Cuando aparece una nueva política, se añade otra implementación de `DiscountPolicy` sin modificar el método `calculate` del servicio.

## 4.4. Diagrama conceptual

```text
               +----------------------+
               |    DiscountPolicy    |
               |----------------------|
               | calculate(amount)    |
               +----------------------+
                         ^
             +-----------+-----------+
             |           |           |
             v           v           v
       +-----------+ +-----------+ +-----------+
       | Regular   | | Premium   | | VIP       |
       | Discount  | | Discount  | | Discount  |
       +-----------+ +-----------+ +-----------+
```

## 4.5. Beneficios de OCP

- Facilita añadir nuevos comportamientos.
- Reduce los cambios en componentes estables.
- Mejora la extensibilidad.
- Favorece las pruebas aisladas de cada variante.
- Puede reducir el crecimiento de condicionales.
- Permite utilizar polimorfismo para representar diferentes políticas.

## 4.6. Errores frecuentes

**Crear interfaces para todas las clases sin necesidad.**

No toda clase necesita una abstracción. Si no existe una variación razonablemente prevista, una implementación sencilla puede ser suficiente.

**Predecir todas las extensiones futuras.**

Diseñar para posibilidades hipotéticas puede producir una arquitectura demasiado compleja.

**Creer que OCP prohíbe cualquier modificación.**

El principio busca proteger las partes estables frente a extensiones previsibles; no impide corregir errores ni adaptar el diseño cuando cambian los requisitos.

## 4.7. Cuándo aplicar OCP

- Cuando una funcionalidad tiene variantes bien identificadas.
- Cuando aparecen nuevos tipos de reglas de negocio con frecuencia.
- Cuando se añaden nuevos formatos, proveedores o estrategias.
- Cuando los cambios repetidos en condicionales provocan regresiones.
- Cuando un contrato estable permite añadir implementaciones intercambiables.

---

# 5. L — Liskov Substitution Principle (LSP)

## 5.1. Definición

El **Principio de Sustitución de Liskov** establece que los objetos de una clase derivada deben poder utilizarse donde se espera un objeto de la clase base sin romper las propiedades y expectativas del programa.

En otras palabras, una subclase debe respetar el contrato de comportamiento de su tipo base.

No basta con que una clase herede de otra para que la relación sea correcta. La herencia debe tener sentido desde el punto de vista del comportamiento.

## 5.2. Ejemplo clásico: rectángulos y cuadrados

Supongamos que tenemos una clase `Rectangle` con operaciones para modificar ancho y alto.

```java
public class Rectangle {

    protected int width;
    protected int height;

    public void setWidth(int width) {
        this.width = width;
    }

    public void setHeight(int height) {
        this.height = height;
    }

    public int area() {
        return width * height;
    }
}
```

Podríamos crear una clase `Square` que herede de `Rectangle` y mantenga ambos lados iguales.

```java
public class Square extends Rectangle {

    @Override
    public void setWidth(int width) {
        this.width = width;
        this.height = width;
    }

    @Override
    public void setHeight(int height) {
        this.width = height;
        this.height = height;
    }
}
```

Desde la geometría, un cuadrado es un tipo de rectángulo. Sin embargo, esta implementación puede romper las expectativas de un programa que presupone que ancho y alto pueden modificarse de forma independiente.

Por ejemplo:

```java
public static void configureRectangle(Rectangle rectangle) {
    rectangle.setWidth(5);
    rectangle.setHeight(4);

    if (rectangle.area() != 20) {
        throw new IllegalStateException(
                "El área no coincide con el contrato esperado"
        );
    }
}
```

Con un `Rectangle` normal, el área es 20.

Con `Square`, la última operación puede dejar ambos lados en 4, por lo que el área será 16.

La subclase no respeta el comportamiento que espera el consumidor.

## 5.3. Diseño alternativo

Si el sistema solo necesita calcular áreas, una abstracción más adecuada puede ser:

```java
public interface Shape {

    int area();
}
```

Rectángulo:

```java
public class Rectangle implements Shape {

    private final int width;
    private final int height;

    public Rectangle(int width, int height) {
        if (width < 0 || height < 0) {
            throw new IllegalArgumentException(
                    "Las dimensiones no pueden ser negativas"
            );
        }

        this.width = width;
        this.height = height;
    }

    @Override
    public int area() {
        return width * height;
    }
}
```

Cuadrado:

```java
public class Square implements Shape {

    private final int side;

    public Square(int side) {
        if (side < 0) {
            throw new IllegalArgumentException(
                    "El lado no puede ser negativo"
            );
        }

        this.side = side;
    }

    @Override
    public int area() {
        return side * side;
    }
}
```

Ambas clases cumplen el contrato `Shape` sin prometer operaciones de modificación que no puedan implementar de forma coherente.

## 5.4. Reglas prácticas para respetar LSP

- Una subclase debe respetar las precondiciones y postcondiciones del contrato base.
- No debe exigir condiciones más restrictivas que las que el consumidor espera.
- No debe debilitar las garantías que ofrece el tipo base.
- Debe respetar los invariantes del objeto.
- No debe introducir comportamientos incompatibles con las expectativas de los clientes.
- Las excepciones y los efectos secundarios deben ser coherentes con el contrato.

Estas reglas son una guía de diseño contractual, no una lista que pueda verificarse únicamente observando la estructura de la clase.

## 5.5. Otro ejemplo: almacenamiento de archivos

Supongamos que una interfaz define:

```java
public interface FileStorage {

    void save(String fileName, byte[] content);

    byte[] read(String fileName);
}
```

Una implementación que permite guardar y leer archivos debería respetar ambas operaciones.

Si una clase solo puede guardar archivos y lanza siempre `UnsupportedOperationException` al leerlos, quizá no debería implementar ese contrato completo.

Una mejor opción puede ser separar los contratos:

```java
public interface FileWriter {

    void save(String fileName, byte[] content);
}
```

```java
public interface FileReader {

    byte[] read(String fileName);
}
```

Esta separación también se relaciona con ISP.

## 5.6. Beneficios de LSP

- Hace que el polimorfismo sea más fiable.
- Evita herencias conceptualmente incorrectas.
- Reduce comportamientos inesperados.
- Facilita sustituir implementaciones.
- Mejora la estabilidad de los contratos.
- Ayuda a detectar abstracciones mal diseñadas.

## 5.7. Errores frecuentes

- Heredar solo para reutilizar código.
- Crear subclases que no pueden cumplir todos los métodos heredados.
- Lanzar excepciones para operaciones que el contrato promete soportar.
- Introducir restricciones incompatibles con la clase base.
- Suponer que una relación del mundo real implica necesariamente una relación de herencia adecuada en el software.

## 5.8. Cuándo aplicar LSP

- Al diseñar jerarquías de herencia.
- Al introducir nuevas implementaciones de una interfaz.
- Cuando una sustitución produce errores inesperados.
- Cuando una subclase necesita desactivar operaciones heredadas.
- Cuando los consumidores dependen de garantías específicas del tipo base.

---

# 6. I — Interface Segregation Principle (ISP)

## 6.1. Definición

El **Principio de Segregación de Interfaces** establece que ningún cliente debería verse obligado a depender de métodos que no utiliza.

En lugar de diseñar interfaces grandes que agrupan operaciones poco relacionadas, conviene definir contratos más pequeños y específicos.

Esto no significa que todas las interfaces deban contener un único método. Deben agruparse las operaciones que pertenecen a una misma responsabilidad y son relevantes para sus consumidores.

## 6.2. Implementación incorrecta

Imaginemos una interfaz para dispositivos de oficina:

```java
public interface OfficeMachine {

    void print(Document document);

    void scan(Document document);

    void fax(Document document);
}
```

Una impresora básica que solo imprime tendría que implementar operaciones que no puede realizar.

```java
public class BasicPrinter implements OfficeMachine {

    @Override
    public void print(Document document) {
        // Imprimir documento.
    }

    @Override
    public void scan(Document document) {
        throw new UnsupportedOperationException(
                "No se admite escaneo"
        );
    }

    @Override
    public void fax(Document document) {
        throw new UnsupportedOperationException(
                "No se admite fax"
        );
    }
}
```

El diseño obliga a la clase a implementar un contrato demasiado amplio.

## 6.3. Implementación mejorada

Dividimos la interfaz por capacidades.

```java
public interface Printer {

    void print(Document document);
}
```

```java
public interface Scanner {

    void scan(Document document);
}
```

```java
public interface FaxMachine {

    void fax(Document document);
}
```

Una impresora básica implementa solo la capacidad que ofrece:

```java
public class BasicPrinter implements Printer {

    @Override
    public void print(Document document) {
        // Imprimir documento.
    }
}
```

Una máquina multifunción puede implementar varios contratos:

```java
public class MultiFunctionPrinter
        implements Printer, Scanner, FaxMachine {

    @Override
    public void print(Document document) {
        // Imprimir documento.
    }

    @Override
    public void scan(Document document) {
        // Escanear documento.
    }

    @Override
    public void fax(Document document) {
        // Enviar fax.
    }
}
```

## 6.4. Diagrama conceptual

```text
    +----------------+   +----------------+   +----------------+
    |    Printer     |   |    Scanner     |   |   FaxMachine   |
    +----------------+   +----------------+   +----------------+
            ^                    ^                    ^
            |                    |                    |
            +--------------------+--------------------+
                                 |
                       +-------------------+
                       | MultiFunction     |
                       | Printer           |
                       +-------------------+

    +----------------+
    |  BasicPrinter  |
    +----------------+
            ^
            |
         Printer
```

El diagrama muestra que una implementación puede adoptar solo los contratos que realmente soporta.

## 6.5. Beneficios de ISP

- Interfaces más fáciles de comprender.
- Menor acoplamiento entre consumidores e implementaciones.
- Menos métodos vacíos o no soportados.
- Mejor capacidad para sustituir implementaciones.
- Pruebas más específicas.
- Contratos más precisos y expresivos.

## 6.6. Errores frecuentes

**Fragmentar las interfaces en exceso.**

Una interfaz por cada método puede generar demasiadas abstracciones y dificultar la comprensión.

**Crear contratos sin cohesión.**

Una interfaz pequeña también puede estar mal diseñada si combina conceptos que no pertenecen juntos.

**Confundir ISP con la regla de un método por interfaz.**

El principio se refiere a las necesidades de los clientes, no a un número fijo de métodos.

## 6.7. Cuándo aplicar ISP

- Cuando las implementaciones necesitan lanzar excepciones para métodos no soportados.
- Cuando los clientes utilizan solo una pequeña parte de una interfaz grande.
- Cuando una interfaz combina capacidades independientes.
- Cuando cambios en métodos no relacionados obligan a modificar implementaciones que no los necesitan.

---

# 7. D — Dependency Inversion Principle (DIP)

## 7.1. Definición

El **Principio de Inversión de Dependencias** establece dos ideas fundamentales:

1. Los módulos de alto nivel no deberían depender directamente de los módulos de bajo nivel. Ambos deberían depender de abstracciones.
2. Las abstracciones no deberían depender de detalles concretos; los detalles concretos deberían depender de las abstracciones.

Un módulo de alto nivel contiene políticas o reglas de aplicación. Un módulo de bajo nivel suele encargarse de detalles técnicos, como acceder a una base de datos, enviar un correo o comunicarse con un proveedor externo.

DIP permite que la lógica importante no quede atada a una implementación concreta de infraestructura.

## 7.2. Implementación incorrecta

```java
public class MySqlOrderRepository {

    public void save(Order order) {
        // Guardar el pedido en MySQL.
    }
}
```

```java
public class OrderService {

    private final MySqlOrderRepository repository =
            new MySqlOrderRepository();

    public void createOrder(Order order) {
        repository.save(order);
    }
}
```

`OrderService` depende directamente de `MySqlOrderRepository`.

Esto dificulta sustituir la persistencia por otra implementación y puede complicar las pruebas unitarias.

## 7.3. Implementación mejorada

Definimos una abstracción:

```java
public interface OrderRepository {

    void save(Order order);
}
```

El servicio depende del contrato:

```java
public class OrderService {

    private final OrderRepository repository;

    public OrderService(OrderRepository repository) {
        this.repository = repository;
    }

    public void createOrder(Order order) {
        repository.save(order);
    }
}
```

La implementación concreta satisface la abstracción:

```java
public class MySqlOrderRepository implements OrderRepository {

    @Override
    public void save(Order order) {
        // Persistencia real en MySQL.
    }
}
```

Podemos crear otra implementación para pruebas:

```java
import java.util.ArrayList;
import java.util.List;

public class InMemoryOrderRepository implements OrderRepository {

    private final List<Order> orders = new ArrayList<>();

    @Override
    public void save(Order order) {
        orders.add(order);
    }

    public List<Order> findAll() {
        return List.copyOf(orders);
    }
}
```

Uso:

```java
OrderRepository repository = new InMemoryOrderRepository();

OrderService service = new OrderService(repository);

service.createOrder(new Order());
```

El servicio no necesita saber qué tecnología almacena los pedidos.

En una aplicación real, la implementación concreta puede ser seleccionada por el contenedor de inyección de dependencias o por la configuración de la aplicación.

## 7.4. Diagrama conceptual

```text
Antes:

+----------------+  depende directamente de  +----------------------+
| OrderService   | -------------------------> | MySqlOrderRepository |
+----------------+                            +----------------------+


Después:

+----------------+       +-------------------+
| OrderService   | ----> | OrderRepository   |
+----------------+       +-------------------+
                                  ^
                                  |
                      +-----------+-----------+
                      |                       |
                      v                       v
             +----------------+     +-----------------------+
             | MySqlOrder     |     | InMemoryOrder         |
             | Repository     |     | Repository            |
             +----------------+     +-----------------------+
```

## 7.5. Inyección de dependencias y DIP

Ambos conceptos están relacionados, pero no son lo mismo.

**DIP** es un principio de diseño que orienta cómo deben depender los módulos.

**Inyección de dependencias (DI)** es una técnica para proporcionar a un objeto las dependencias que necesita, en lugar de obligarlo a crearlas internamente.

Por ejemplo:

```java
public OrderService(OrderRepository repository) {
    this.repository = repository;
}
```

La dependencia se proporciona desde fuera mediante el constructor.

DI puede ayudar a implementar DIP, pero inyectar una clase concreta no garantiza por sí solo que el diseño respete DIP.

## 7.6. Beneficios de DIP

- Reduce el acoplamiento con la infraestructura.
- Facilita las pruebas unitarias.
- Permite sustituir implementaciones.
- Hace explícitas las dependencias.
- Facilita trabajar con adaptadores.
- Mejora la separación entre políticas de negocio y detalles técnicos.

## 7.7. Errores frecuentes

**Crear interfaces para todas las clases.**

Las abstracciones deben aportar valor. No es necesario crear una interfaz para cada clase sencilla y estable.

**Depender de una abstracción que pertenece al lugar equivocado.**

Los contratos deberían definirse en el módulo que necesita esa abstracción o en un módulo de contratos apropiado, respetando las dependencias de la arquitectura.

**Confundir DIP con DI.**

Un contenedor de inyección de dependencias no garantiza un buen diseño si los módulos siguen acoplados conceptualmente.

**Crear una abstracción que expone detalles de infraestructura.**

Una interfaz que obliga a todos los consumidores a conocer tipos específicos de una base de datos puede no ofrecer una separación real.

## 7.8. Cuándo aplicar DIP

- Cuando la lógica de negocio depende directamente de una base de datos.
- Cuando los servicios crean internamente sus dependencias.
- Cuando se necesita sustituir proveedores externos.
- Cuando las pruebas son difíciles porque requieren infraestructura real.
- Cuando se quiere mantener la dirección de dependencias de una arquitectura limpia.

---

# 8. Cómo se complementan los cinco principios

Los principios SOLID no deben entenderse como reglas aisladas. A menudo, una mejora de diseño involucra varios principios simultáneamente.

| Principio | Pregunta clave | Resultado esperado |
|---|---|---|
| SRP | ¿Esta clase tiene motivos de cambio independientes? | Responsabilidades coherentes. |
| OCP | ¿Puedo añadir una variante sin modificar continuamente el código estable? | Extensibilidad. |
| LSP | ¿Esta implementación puede sustituir a la abstracción sin romper su contrato? | Polimorfismo fiable. |
| ISP | ¿Los clientes dependen solo de las operaciones que necesitan? | Interfaces específicas. |
| DIP | ¿La lógica importante depende de detalles concretos innecesariamente? | Dependencias desacopladas. |

### Ejemplo integrado

Imaginemos un sistema de notificaciones que permite enviar mensajes por correo electrónico y SMS.

Una estructura posible sería:

```text
                  +----------------------+
                  | NotificationChannel  |
                  +----------------------+
                            ^
                  +---------+---------+
                  |                   |
                  v                   v
          +---------------+   +---------------+
          | EmailChannel  |   | SmsChannel    |
          +---------------+   +---------------+
                  ^                   ^
                  +---------+---------+
                            |
                  +----------------------+
                  | NotificationService  |
                  +----------------------+
```

Este diseño puede reflejar varios principios:

- **SRP:** el servicio de aplicación coordina la notificación; cada canal se ocupa de su mecanismo de envío.
- **OCP:** se puede añadir un nuevo canal compatible sin reescribir la coordinación principal.
- **LSP:** todos los canales deben respetar el contrato de `NotificationChannel`.
- **ISP:** el contrato no debería exigir capacidades que todos los canales no puedan ofrecer.
- **DIP:** el servicio depende de una abstracción y no de una implementación concreta.

Una posible interfaz sería:

```java
public interface NotificationChannel {

    void send(String recipient, String message);
}
```

Y el servicio:

```java
public class NotificationService {

    private final NotificationChannel channel;

    public NotificationService(NotificationChannel channel) {
        this.channel = channel;
    }

    public void notify(
            String recipient,
            String message
    ) {
        channel.send(recipient, message);
    }
}
```

El ejemplo es simplificado. En una aplicación real, podrían necesitarse selección de canal, plantillas, reintentos, gestión de errores y políticas de entrega. Esas necesidades deberían modelarse cuando existan, sin convertir una abstracción sencilla en un sistema excesivamente genérico.

---

# 9. Relación entre SOLID y otros principios de programación

SOLID forma parte de un conjunto más amplio de principios de diseño.

| Principio | Enfoque principal | Relación con SOLID |
|---|---|---|
| Clean Code | Legibilidad y mantenibilidad. | Facilita comprender responsabilidades y contratos. |
| DRY | Evitar duplicar conocimiento. | Puede reducir implementaciones independientes de una misma regla. |
| KISS | Mantener la sencillez. | Evita aplicar SOLID con abstracciones innecesarias. |
| YAGNI | No implementar necesidades hipotéticas. | Limita la sobrearquitectura. |
| Separation of Concerns | Separar preocupaciones. | Está estrechamente relacionado con SRP. |
| Composition over Inheritance | Preferir composición cuando resulte adecuada. | Puede evitar jerarquías que violen LSP. |
| Encapsulation | Ocultar detalles internos. | Ayuda a proteger invariantes y contratos. |
| Law of Demeter | Limitar el conocimiento de otros objetos. | Puede reducir el acoplamiento entre componentes. |

### DRY y SOLID

DRY pregunta si el mismo conocimiento se mantiene de manera independiente en varios lugares.

SOLID ayuda a decidir dónde colocar ese conocimiento y cómo organizar los contratos y dependencias.

No toda duplicación debe eliminarse. Si dos módulos necesitan evolucionar de forma autónoma, compartir una implementación puede crear más problemas de los que resuelve.

### KISS y SOLID

SOLID no exige crear interfaces, fábricas, estrategias o jerarquías para cada clase.

Una solución sencilla que cumple los requisitos actuales puede ser mejor que una arquitectura preparada para decenas de variantes hipotéticas.

---

# 10. Relación entre SOLID y los patrones de diseño

Los patrones de diseño ofrecen soluciones conocidas a problemas recurrentes. Algunos pueden ayudar a aplicar SOLID.

| Patrón | Principios relacionados | Uso típico |
|---|---|---|
| Strategy | OCP, DIP | Intercambiar algoritmos o políticas. |
| Factory Method | OCP, DIP | Delegar la creación de objetos a implementaciones especializadas. |
| Abstract Factory | OCP, DIP | Crear familias de objetos compatibles. |
| Adapter | LSP, DIP | Adaptar una implementación externa a un contrato interno. |
| Decorator | OCP, SRP | Añadir comportamiento mediante composición. |
| Facade | SRP, ISP | Ofrecer una interfaz de entrada más sencilla a un subsistema. |
| Repository | SRP, DIP | Separar el acceso a datos del resto de la aplicación. |
| Observer | OCP, DIP | Desacoplar emisores de eventos y sus suscriptores. |
| Command | SRP, OCP | Encapsular operaciones como objetos. |
| Composite | LSP | Tratar elementos individuales y grupos mediante un contrato común, cuando sus comportamientos sean compatibles. |

Estos vínculos no son automáticos. Un patrón puede implementarse mal y violar los principios que pretende favorecer.

Por ejemplo, Strategy ayuda con OCP cuando existen algoritmos alternativos reales, pero crear una interfaz y diez clases para una única operación estable puede empeorar la simplicidad del sistema.

---

# 11. SOLID en arquitecturas de software

## 11.1. Arquitectura monolítica

SOLID ayuda a organizar un monolito en clases y módulos con responsabilidades claras.

Un monolito bien diseñado puede tener un alto grado de modularidad interna, aunque se despliegue como una única aplicación.

No es necesario adoptar microservicios para beneficiarse de SOLID.

## 11.2. Arquitectura hexagonal

La arquitectura hexagonal separa el núcleo de negocio de los sistemas externos mediante puertos y adaptadores.

DIP es especialmente relevante: el núcleo define los contratos que necesita y los adaptadores concretos implementan esos contratos.

```text
         +-----------------------------+
         |       Aplicación            |
         |                             |
         |  Caso de uso                 |
         |       |                     |
         |       v                     |
         |  Puerto de persistencia     |
         +-------------^---------------+
                       |
                 implementa
                       |
         +-------------+---------------+
         |     Adaptador de base de     |
         |     datos                    |
         +-----------------------------+
```

El núcleo no necesita depender directamente de un framework de persistencia.

## 11.3. Clean Architecture

Clean Architecture organiza el código en capas o círculos con una dirección de dependencias controlada.

DIP permite que los casos de uso dependan de abstracciones, mientras que los detalles externos se adaptan a ellas.

SRP también ayuda a mantener separadas las reglas de negocio, los casos de uso, la presentación y la infraestructura.

## 11.4. Arquitectura de microservicios

SOLID puede aplicarse dentro de cada servicio para mantener sus módulos internos bien diseñados.

Sin embargo, no determina por sí solo los límites de los microservicios. Para decidir esos límites también hay que considerar el dominio, la autonomía de despliegue, la propiedad de los datos y las necesidades operativas.

Compartir interfaces o bibliotecas entre servicios debe evaluarse cuidadosamente para no introducir acoplamiento entre despliegues.

## 11.5. MVC, MVP y MVVM

En los patrones de presentación, SOLID ayuda a separar:

- La representación visual.
- La coordinación de interacciones.
- Las reglas de negocio.
- Los servicios externos.

Por ejemplo, un Presenter no debería concentrar necesariamente la persistencia, las reglas del dominio y la construcción visual. Las responsabilidades deben distribuirse de acuerdo con el papel de cada componente.

---

# 12. Cómo refactorizar código para aplicar SOLID

La refactorización debe hacerse progresivamente, sin cambiar accidentalmente el comportamiento funcional.

### Paso 1. Detectar síntomas

Busca clases con muchas responsabilidades, condicionales extensos, interfaces demasiado grandes, herencias problemáticas y dependencias concretas innecesarias.

### Paso 2. Comprender los requisitos

Antes de cambiar el diseño, identifica qué comportamientos existen y cuáles podrían variar razonablemente.

### Paso 3. Elegir el principio relevante

- Responsabilidades mezcladas: SRP.
- Variantes que obligan a modificar código estable: OCP.
- Sustituciones que rompen el comportamiento: LSP.
- Contratos demasiado amplios: ISP.
- Dependencias directas de detalles técnicos: DIP.

### Paso 4. Diseñar una solución mínima

No introduzcas todos los patrones posibles. Selecciona la modificación más pequeña que resuelva el problema identificado.

### Paso 5. Añadir o revisar pruebas

Las pruebas deben proteger el comportamiento existente y ayudar a detectar regresiones.

### Paso 6. Refactorizar en pasos pequeños

Extrae responsabilidades, define contratos cuando sean útiles, actualiza los consumidores y elimina el código obsoleto.

### Paso 7. Revisar la complejidad resultante

Comprueba que el nuevo diseño es más claro, no solo que tiene más interfaces o clases.

---

# 13. Pruebas automatizadas y SOLID

Los principios SOLID suelen facilitar las pruebas porque promueven responsabilidades más acotadas, contratos explícitos y dependencias sustituibles.

No obstante, respetarlos no garantiza automáticamente que una aplicación sea fácil de probar.

## 13.1. Ejemplo de prueba unitaria con DIP

Supongamos que queremos probar `OrderService` sin conectarnos a una base de datos real.

Podemos utilizar una implementación de prueba del repositorio.

```java
import static org.junit.jupiter.api.Assertions.assertEquals;

import java.util.ArrayList;
import java.util.List;
import org.junit.jupiter.api.Test;

class OrderServiceTest {

    @Test
    void shouldSaveCreatedOrder() {
        InMemoryOrderRepository repository =
                new InMemoryOrderRepository();

        OrderService service = new OrderService(repository);

        Order order = new Order();

        service.createOrder(order);

        assertEquals(1, repository.findAll().size());
    }
}
```

El ejemplo utiliza las clases `OrderService`, `OrderRepository` e `InMemoryOrderRepository` definidas anteriormente.

Esta prueba verifica la interacción con la abstracción de persistencia sin necesitar MySQL.

## 13.2. Qué conviene probar

- El comportamiento de cada responsabilidad.
- Los contratos públicos de las abstracciones.
- Los casos límite y los errores.
- Las implementaciones alternativas que deban ser sustituibles.
- La integración entre los componentes que colaboran.
- Las decisiones de configuración que seleccionan implementaciones concretas.

## 13.3. Precaución

No es necesario probar cada método privado de manera aislada ni construir mocks para todos los objetos.

Las pruebas deben centrarse en el comportamiento relevante y en los contratos. Un exceso de pruebas ligadas a detalles internos puede hacer que una refactorización sencilla requiera modificar numerosos tests.

---

# 14. Errores comunes al aplicar SOLID

## Error 1. Crear interfaces para todo

Las interfaces son útiles cuando representan contratos significativos, puntos de variación o límites entre módulos.

No es necesario crear una interfaz para cada clase solo por cumplir DIP u OCP.

## Error 2. Confundir SRP con clases diminutas

Una clase no está mal diseñada simplemente por tener muchos métodos. Lo importante es que sus operaciones formen una responsabilidad coherente.

## Error 3. Abusar de la herencia

La herencia no debería utilizarse únicamente para reutilizar código. La relación debe respetar el comportamiento y el contrato del tipo base.

## Error 4. Diseñar extensiones que todavía no se necesitan

OCP no obliga a construir un framework de plugins para una funcionalidad que solo tiene una implementación estable.

## Error 5. Crear interfaces demasiado genéricas

Una abstracción que no representa un concepto claro puede complicar el código en lugar de mejorarlo.

## Error 6. Confundir DIP con usar un framework de inyección

Un contenedor DI puede gestionar dependencias, pero no corrige automáticamente una arquitectura mal acoplada.

## Error 7. Fragmentar una clase sin mejorar la cohesión

Extraer métodos en clases diferentes no garantiza SRP. Las nuevas clases deben tener responsabilidades y límites claros.

## Error 8. Aplicar SOLID sin considerar el contexto

El tamaño del proyecto, la frecuencia de cambios, las necesidades de prueba y la complejidad del dominio influyen en qué abstracciones merecen la pena.

---

# 15. Checklist de SOLID para revisar una clase

## SRP — Responsabilidad única

- [ ] ¿La clase tiene una responsabilidad coherente?
- [ ] ¿Sus métodos pertenecen al mismo concepto?
- [ ] ¿Existen motivos de cambio independientes mezclados?
- [ ] ¿Se puede separar alguna responsabilidad sin introducir complejidad innecesaria?

## OCP — Abierto/cerrado

- [ ] ¿Añadir una variante obliga a modificar muchos condicionales?
- [ ] ¿Existe un punto de extensión estable que tenga sentido?
- [ ] ¿La nueva abstracción responde a una necesidad real?
- [ ] ¿Se evita diseñar para extensiones puramente hipotéticas?

## LSP — Sustitución de Liskov

- [ ] ¿Las implementaciones respetan el contrato del tipo base?
- [ ] ¿Se evitan excepciones para operaciones que el contrato promete soportar?
- [ ] ¿Las precondiciones y postcondiciones son compatibles?
- [ ] ¿El polimorfismo produce el comportamiento que esperan los consumidores?

## ISP — Segregación de interfaces

- [ ] ¿Los clientes dependen solo de las operaciones que necesitan?
- [ ] ¿Existen métodos no soportados o implementaciones vacías?
- [ ] ¿La interfaz agrupa operaciones relacionadas?
- [ ] ¿La fragmentación propuesta mejora realmente la claridad?

## DIP — Inversión de dependencias

- [ ] ¿La lógica de alto nivel depende directamente de detalles de infraestructura?
- [ ] ¿Las dependencias importantes están expresadas mediante contratos adecuados?
- [ ] ¿Las dependencias se proporcionan de forma explícita cuando resulta útil?
- [ ] ¿Se pueden sustituir implementaciones para realizar pruebas?
- [ ] ¿Las abstracciones evitan exponer detalles técnicos innecesarios?

---

# 16. Checklist de SOLID para una Pull Request

Antes de aprobar un cambio, puedes revisar los siguientes puntos:

- [ ] Las clases nuevas tienen responsabilidades claras.
- [ ] No se han mezclado reglas de negocio y detalles de infraestructura sin una razón justificada.
- [ ] Las nuevas variantes no obligan a modificar repetidamente componentes estables.
- [ ] Las implementaciones respetan los contratos que declaran.
- [ ] Las interfaces no fuerzan a los consumidores a depender de métodos irrelevantes.
- [ ] Las dependencias de los componentes importantes están bien definidas.
- [ ] Las abstracciones introducidas aportan valor real.
- [ ] No se han creado capas o interfaces innecesarias.
- [ ] Las pruebas verifican los comportamientos importantes.
- [ ] El diseño final es más comprensible y mantenible que el anterior.

Esta checklist debe utilizarse como guía, no como un requisito para introducir cinco cambios en cada Pull Request.

---

# 17. Plan para adoptar SOLID en un proyecto existente

La mejor forma de aplicar SOLID suele ser mediante mejoras incrementales.

### Fase 1. Evaluar el diseño actual

Identifica las clases con más responsabilidades, las dependencias concretas difíciles de sustituir y los contratos que no se respetan.

### Fase 2. Priorizar problemas reales

Empieza por los componentes que cambian con frecuencia, generan errores o son difíciles de probar.

### Fase 3. Proteger el comportamiento

Añade pruebas de caracterización cuando necesites conocer y preservar el comportamiento actual.

### Fase 4. Refactorizar responsabilidades

Aplica SRP a las clases que mezclan motivos de cambio claramente independientes.

### Fase 5. Introducir abstracciones justificadas

Utiliza OCP y DIP cuando existan puntos de variación o límites técnicos importantes.

### Fase 6. Revisar los contratos

Aplica LSP e ISP al diseñar jerarquías e interfaces.

### Fase 7. Comprobar el resultado

Ejecuta las pruebas y revisa si el nuevo diseño reduce el acoplamiento sin crear complejidad innecesaria.

### Fase 8. Mantener la mejora

Incorpora revisiones de diseño y análisis de código al proceso habitual de desarrollo.

---

# 18. Conclusión

**SOLID es un conjunto de cinco principios que ayuda a diseñar software orientado a objetos con responsabilidades claras, contratos fiables y dependencias controladas.**

Sus ideas principales son:

1. **SRP:** una clase o módulo debería tener una responsabilidad coherente y una razón principal para cambiar.
2. **OCP:** las nuevas variantes deberían poder incorporarse sin modificar continuamente el código estable.
3. **LSP:** las implementaciones deben poder sustituirse respetando el contrato del tipo base.
4. **ISP:** los consumidores no deberían depender de operaciones que no necesitan.
5. **DIP:** los módulos de alto nivel deberían depender de abstracciones apropiadas, no de detalles concretos innecesarios.

La aplicación conjunta de estos principios puede facilitar las pruebas, mejorar la mantenibilidad y reducir el impacto de los cambios.

Sin embargo, **SOLID no consiste en maximizar el número de interfaces, clases o patrones de diseño**. El objetivo es conseguir un diseño que represente bien el dominio y permita evolucionar el sistema con un coste razonable.

Una aplicación sencilla no necesita una arquitectura compleja para ser correcta. Conviene introducir abstracciones cuando resuelven un problema real y mantenerlas tan claras como sea posible.

Para consolidar los conceptos, recomiendo estudiar los principios en este orden:

1. SRP, para aprender a separar responsabilidades.
2. DIP, para aprender a desacoplar los componentes mediante contratos.
3. ISP, para diseñar interfaces adecuadas.
4. OCP, para permitir la evolución del comportamiento sin modificar innecesariamente el código estable.
5. LSP, para asegurar que las implementaciones respeten los contratos y puedan sustituirse correctamente.

---

# 19. Referencias y lecturas recomendadas

## Libros

- Robert C. Martin — *Agile Software Development: Principles, Patterns, and Practices*.
- Robert C. Martin — *Clean Code: A Handbook of Agile Software Craftsmanship*.
- Martin Fowler — *Refactoring: Improving the Design of Existing Code*.
- Joshua Bloch — *Effective Java*.
- Erich Gamma, Richard Helm, Ralph Johnson y John Vlissides — *Design Patterns: Elements of Reusable Object-Oriented Software*.

## Recursos técnicos

- Documentación oficial de Java: https://docs.oracle.com/en/java/
- JUnit 5: https://junit.org/junit5/
- Martin Fowler — Refactoring: https://refactoring.com/
- OWASP Cheat Sheet Series: https://cheatsheetseries.owasp.org/

Estas referencias permiten profundizar en el diseño orientado a objetos, la refactorización, los patrones de diseño, los contratos y las prácticas de pruebas que complementan SOLID.