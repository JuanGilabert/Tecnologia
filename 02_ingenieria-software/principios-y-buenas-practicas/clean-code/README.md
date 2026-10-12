# Clean Code: principios y buenas prácticas de programación

## 1. Introducción

**Clean Code**, o código limpio, es una filosofía de desarrollo de software que busca que el código sea claro, comprensible, mantenible, verificable y fácil de modificar.

No es un patrón de diseño, una arquitectura concreta ni una metodología de desarrollo. Es un conjunto de principios, convenciones y prácticas que ayudan a mejorar la calidad del código fuente.

El concepto se popularizó especialmente a través del libro *Clean Code*, de Robert C. Martin, aunque muchas de sus ideas se relacionan con principios de ingeniería del software anteriores y con prácticas de desarrollo ampliamente utilizadas.

El objetivo no consiste simplemente en escribir menos código, sino en escribir código que comunique correctamente su intención y que permita realizar cambios con un riesgo razonable.

La técnica Clean Code (Código Limpio) es fundamental para complementar todos los patrones de diseño y arquitecturas que estás documentando. Mientras que patrones como MVP, MVC o Strategy ayudan a organizar la estructura de una solución, Clean Code se centra en cómo escribir código que sea fácil de leer, comprender, probar, mantener y modificar.

### Objetivos principales

- Mejorar la legibilidad del código.
- Reducir la complejidad accidental.
- Facilitar el mantenimiento y la evolución del software.
- Evitar duplicaciones innecesarias.
- Mantener las responsabilidades bien delimitadas.
- Facilitar las pruebas automatizadas.
- Reducir errores derivados de suposiciones implícitas.
- Favorecer una colaboración más eficiente entre desarrolladores.
- Simplificar la incorporación de nuevas funcionalidades.
- Disminuir el coste de comprender código existente.

### Idea fundamental

> El código debe estar escrito para que otras personas —incluido quien lo escribió meses atrás— puedan comprenderlo y modificarlo con confianza.

El código limpio no implica que todas las soluciones deban ser extremadamente abstractas o sofisticadas. En muchas ocasiones, la solución más limpia es la más sencilla que satisface los requisitos sin introducir complejidad innecesaria.

---

## 2. ¿Qué problemas resuelve Clean Code?

A medida que una aplicación evoluciona, puede acumular problemas de diseño y mantenimiento.

### 2.1. Código difícil de entender

```java
public double c(double a, double b, int t) {
    if (t == 1) {
        return a * b * 0.21;
    }

    if (t == 2) {
        return a * b * 0.10;
    }

    return a * b;
}
```

Aunque este método funciona para determinados valores, es difícil entender qué representan `a`, `b` y `t`, así como el significado de cada porcentaje.

Una versión más expresiva sería:

```java
public double calculateTax(
        double price,
        double quantity,
        TaxType taxType
) {
    double subtotal = price * quantity;

    return switch (taxType) {
        case STANDARD -> subtotal * 0.21;
        case REDUCED -> subtotal * 0.10;
        case EXEMPT -> 0.0;
    };
}
```

El ejemplo utiliza nombres y tipos más descriptivos. En un sistema real, además, sería conveniente representar los importes monetarios mediante un tipo decimal apropiado, como `BigDecimal`, en lugar de `double`.

### 2.2. Métodos demasiado grandes

Un método que valida datos, consulta una base de datos, calcula importes, envía correos y genera un informe tiene demasiadas responsabilidades.

Esto dificulta comprender su comportamiento y probarlo de manera aislada.

### 2.3. Duplicación de código

Cuando una misma regla de negocio aparece en varios lugares, cualquier cambio debe reproducirse correctamente en todos ellos.

Si se olvida una implementación, pueden aparecer comportamientos inconsistentes.

### 2.4. Alto acoplamiento

Cuando una clase depende directamente de muchas implementaciones concretas, cualquier cambio puede propagarse a otras partes del sistema.

### 2.5. Pruebas difíciles

El código que mezcla lógica de negocio, acceso a datos, estado global y operaciones externas suele ser difícil de probar sin configurar un entorno complejo.

### 2.6. Miedo a modificar el software

Cuando nadie entiende con claridad qué hace una clase o qué efectos secundarios produce un método, los cambios se vuelven arriesgados.

Clean Code ayuda a reducir estos problemas mediante decisiones de diseño que favorecen la claridad y el aislamiento de responsabilidades.

---

## 3. Principios fundamentales de Clean Code

## 3.1. Legibilidad antes que ingenio

El código debe expresar su intención de manera clara.

Una solución ingeniosa, compacta o especialmente sofisticada no es necesariamente mejor que una solución sencilla.

**Menos recomendable:**

```java
boolean v = u != null && u.a() && !u.b();
```

**Más recomendable:**

```java
boolean canAccessResource(User user) {
    return user != null
            && user.isActive()
            && !user.isBlocked();
}
```

El segundo ejemplo permite entender mejor la condición.

Si la regla aparece en muchos lugares, puede ser útil darle un nombre de dominio como `canAccessResource`.

**Regla práctica:** optimiza primero la claridad y mide el rendimiento antes de introducir complejidad para mejorar la velocidad.

## 3.2. Simplicidad

Una solución sencilla suele ser más fácil de comprender, probar y mantener.

Esto no significa eliminar todas las abstracciones. Significa evitar las que no aportan valor suficiente.

Por ejemplo, crear cinco interfaces y seis clases para transformar una cadena en mayúsculas probablemente sea innecesario. Sin embargo, abstraer un proveedor externo que puede sustituirse o simularse en las pruebas sí puede estar justificado.

## 3.3. Separación de responsabilidades

Cada componente debe tener un propósito reconocible.

Una clase de facturación no debería asumir automáticamente la responsabilidad de enviar correos, gestionar conexiones HTTP y generar documentos PDF.

Una organización posible sería:

```text
InvoiceService
├── InvoiceCalculator
├── InvoiceRepository
├── InvoicePdfGenerator
└── NotificationService
```

Esta estructura es conceptual. No es obligatorio crear una clase por cada operación: la separación debe responder a responsabilidades diferenciadas y necesidades reales.

## 3.4. Bajo acoplamiento y alta cohesión

**Bajo acoplamiento:** una clase depende de la menor cantidad razonable de detalles de otras clases.

**Alta cohesión:** los elementos de una clase están relacionados con una responsabilidad común.

Una clase que calcula precios, administra usuarios y procesa imágenes tiene baja cohesión.

Una clase que concentra las reglas para calcular el precio final de un pedido tiene una responsabilidad más coherente.

## 3.5. Código explícito

Evita que el comportamiento dependa de suposiciones ocultas.

Los nombres, tipos, contratos y errores deben ayudar a comprender qué puede ocurrir.

Por ejemplo, un método que devuelve `null` cuando no encuentra un usuario obliga a quien lo utiliza a conocer esa convención.

Una alternativa explícita en Java es:

```java
public Optional<User> findUserById(long id) {
    // Consultar el repositorio.
    return repository.findById(id);
}
```

El uso de `Optional` es apropiado para expresar que un resultado puede estar ausente, aunque no es necesario utilizarlo indiscriminadamente en todos los tipos de retorno.

---

## 4. Nombres significativos

Elegir buenos nombres es una de las prácticas con mayor impacto en la legibilidad.

Los nombres deben expresar intención y significado de dominio.

### 4.1. Variables

**Evitar:**

```java
int d;
double x;
List<String> l;
```

**Preferir:**

```java
int daysUntilExpiration;
double monthlyRevenue;
List<String> customerEmails;
```

Un buen nombre reduce la necesidad de comentarios explicativos.

### 4.2. Métodos

Los métodos deben utilizar nombres que expresen acciones.

```java
calculateTotalPrice();
findActiveCustomers();
sendConfirmationEmail();
validatePaymentDetails();
```

Evita nombres vagos como:

```java
process();
handle();
execute();
doWork();
```

Estos nombres pueden ser adecuados si el contexto ya define claramente la operación, pero suelen resultar insuficientes por sí solos.

### 4.3. Clases

Los nombres de las clases deberían representar conceptos, responsabilidades o roles.

```java
OrderValidator
PaymentProcessor
CustomerRepository
InvoiceGenerator
```

Evita nombres genéricos como `Manager`, `Helper` o `Utils` cuando no describen una responsabilidad concreta.

No son palabras prohibidas, pero pueden ocultar clases con demasiadas funciones.

### 4.4. Evitar abreviaturas ambiguas

Las abreviaturas habituales del dominio pueden ser útiles, pero no conviene inventar abreviaturas difíciles de interpretar.

Por ejemplo, `customerAccount` suele ser más claro que `custAcc`.

### 4.5. Utilizar nombres que expresen el significado de los booleanos

```java
boolean isActive;
boolean hasPermission;
boolean canBeCancelled;
boolean shouldRetry;
```

Estos nombres permiten leer las condiciones de manera natural.

### 4.6. Evitar información engañosa

Un método llamado `getCustomer` no debería modificar al cliente ni realizar operaciones inesperadas.

Los nombres deben reflejar el comportamiento real, incluidos los posibles efectos secundarios importantes.

---

## 5. Métodos pequeños y con una sola responsabilidad

Un método debería representar una operación comprensible y mantener una cohesión interna razonable.

No existe un número universal de líneas que determine si un método está bien diseñado. Lo importante es que su propósito sea claro y que no combine tareas independientes.

### 5.1. Ejemplo de método con demasiadas responsabilidades

```java
public void registerUser(User user) {
    if (user.getEmail() == null
            || !user.getEmail().contains("@")) {
        throw new IllegalArgumentException("Email inválido");
    }

    String normalizedEmail =
            user.getEmail().trim().toLowerCase();

    user.setEmail(normalizedEmail);

    repository.save(user);

    System.out.println("Usuario guardado");

    emailService.sendWelcomeEmail(user.getEmail());
}
```

Este método valida, normaliza, persiste, escribe en consola y envía un correo.

Una separación más clara sería:

```java
public void registerUser(User user) {
    validateUser(user);

    User normalizedUser = normalizeUser(user);

    userRepository.save(normalizedUser);

    notificationService.sendWelcomeEmail(
            normalizedUser.getEmail()
    );
}

private void validateUser(User user) {
    if (user == null) {
        throw new IllegalArgumentException(
                "El usuario es obligatorio"
        );
    }

    if (user.getEmail() == null
            || user.getEmail().isBlank()
            || !user.getEmail().contains("@")) {
        throw new IllegalArgumentException(
                "El email no es válido"
        );
    }
}

private User normalizeUser(User user) {
    String normalizedEmail = user.getEmail()
            .trim()
            .toLowerCase(java.util.Locale.ROOT);

    return new User(user.getId(), normalizedEmail);
}
```

Este ejemplo presupone que `User` dispone de un constructor compatible con el código mostrado. En una aplicación real, la normalización y validación deben ajustarse a las reglas del dominio y a la política de direcciones de correo.

La idea importante es separar las tareas que tienen significado propio.

### 5.2. Evitar la abstracción innecesaria

No es necesario crear un método privado para cada línea.

Extraer métodos resulta útil cuando:

- Se identifica una operación con significado propio.
- Una parte del código puede reutilizarse.
- El método principal se vuelve más fácil de leer.
- Se desea aislar una decisión compleja.
- La extracción facilita las pruebas o el mantenimiento.

Si el método extraído solo cambia el nombre de una expresión trivial sin mejorar la comprensión, quizá no aporte valor.

---

## 6. Evitar la duplicación: principio DRY

DRY significa **Don't Repeat Yourself**: no repetir conocimiento de manera innecesaria.

El objetivo no es eliminar cualquier coincidencia textual, sino evitar que una misma regla de negocio tenga varias implementaciones independientes que puedan divergir.

### Ejemplo con duplicación

```java
public double calculateStandardTax(double amount) {
    return amount * 0.21;
}

public double calculateOrderTax(double amount) {
    return amount * 0.21;
}
```

Si ambas funciones representan exactamente la misma regla, mantenerlas por separado crea un riesgo de inconsistencia.

Una alternativa:

```java
public double calculateTax(double amount) {
    return amount * STANDARD_TAX_RATE;
}
```

```java
private static final double STANDARD_TAX_RATE = 0.21;
```

Para cálculos monetarios reales, se recomienda utilizar `BigDecimal` y definir explícitamente la escala y el redondeo aplicables.

### Cuándo no conviene eliminar una duplicación

Dos fragmentos de código pueden parecer iguales y representar reglas diferentes.

Por ejemplo, dos cálculos que hoy utilizan el mismo porcentaje podrían responder a políticas de negocio independientes que evolucionarán de forma distinta.

Antes de extraer una abstracción, pregunta:

- ¿Representan el mismo concepto?
- ¿Cambiarán por los mismos motivos?
- ¿Comparten una regla o solo una estructura superficial?
- ¿La abstracción resultante será más fácil de entender?

**DRY trata de evitar la duplicación de conocimiento, no de convertir cualquier similitud en una abstracción compartida.**

---

## 7. Comentarios: cuándo utilizarlos

Los comentarios deben aportar información que el código no expresa adecuadamente.

### 7.1. Comentarios poco útiles

```java
// Incrementar el contador
counter++;

// Comprobar si el usuario está activo
if (user.isActive()) {
    // ...
}
```

Estos comentarios repiten literalmente lo que hace el código.

### 7.2. Comentarios que explican una decisión

```java
// Usamos Locale.ROOT para evitar que la normalización
// dependa de la configuración regional del sistema.
String normalized = email.toLowerCase(Locale.ROOT);
```

Este comentario aporta el motivo de una decisión técnica.

### 7.3. Documentación de contratos

En APIs públicas, puede ser útil explicar las condiciones, los efectos y las excepciones de un método.

```java
/**
 * Busca un pedido por su identificador.
 *
 * @param orderId identificador del pedido
 * @return el pedido encontrado
 * @throws OrderNotFoundException si no existe
 */
public Order findOrder(long orderId) {
    // ...
}
```

### 7.4. Comentarios que pueden ser perjudiciales

Evita:

- Comentarios que describen código obvio.
- Comentarios desactualizados.
- Bloques de código comentados que ya no se utilizan.
- Comentarios que ocultan una mala estructura.
- Comentarios que explican una regla que podría expresarse mediante nombres más claros.

**Regla práctica:** primero intenta que el código se explique por sí mismo; utiliza comentarios para explicar el contexto, las restricciones y el porqué.

---

## 8. Manejo de errores y excepciones

Un código limpio no solo debe funcionar cuando todo sale bien. También debe definir qué ocurre cuando aparecen situaciones inesperadas.

### 8.1. No ocultar errores

**Evitar:**

```java
try {
    repository.save(user);
} catch (Exception exception) {
    // Ignorar
}
```

Esta implementación puede provocar que la aplicación continúe como si la operación hubiera tenido éxito.

**Preferir:**

```java
try {
    repository.save(user);
} catch (RepositoryException exception) {
    logger.error(
            "No se pudo guardar el usuario",
            exception
    );

    throw new UserPersistenceException(
            "No se pudo registrar el usuario",
            exception
    );
}
```

El ejemplo presupone que existen `RepositoryException`, `UserPersistenceException` y un `logger` configurado.

### 8.2. Utilizar excepciones específicas

Las excepciones específicas permiten distinguir situaciones diferentes.

Por ejemplo:

```java
public class UserNotFoundException
        extends RuntimeException {

    public UserNotFoundException(long userId) {
        super("No existe el usuario con id " + userId);
    }
}
```

No conviene crear una clase de excepción para cada error trivial. Las excepciones personalizadas son útiles cuando representan una condición significativa que necesita un tratamiento diferenciado.

### 8.3. No utilizar excepciones como flujo de control habitual

Las excepciones deberían representar condiciones excepcionales o errores según el contrato de la operación, no sustituir indiscriminadamente las estructuras de control normales.

### 8.4. No devolver valores ambiguos

Un método que devuelve `0`, `null` o una cadena vacía para indicar cualquier error mezcla resultados válidos con situaciones de fallo.

Utiliza un contrato explícito: excepciones, `Optional`, tipos de resultado o códigos de error bien definidos, según el contexto.

### 8.5. Preservar la causa original

Cuando una excepción se transforma en otra, conserva la causa original si es relevante para el diagnóstico.

```java
throw new PaymentException(
        "No se pudo procesar el pago",
        exception
);
```

### 8.6. Separar errores técnicos y mensajes de usuario

Los detalles técnicos pueden ser útiles para los registros, pero no siempre deben exponerse al usuario final.

Una aplicación debería registrar información suficiente para diagnosticar el problema y mostrar mensajes comprensibles que no revelen datos sensibles.

---

## 9. Principio KISS

KISS significa **Keep It Simple, Stupid**, una expresión que suele interpretarse como «mantén las cosas sencillas».

Su idea es evitar complejidad innecesaria.

### Ejemplo

Una implementación excesivamente compleja para una condición sencilla puede introducir interfaces, clases y estrategias sin aportar flexibilidad real.

```java
public boolean isAdult(int age) {
    return age >= 18;
}
```

Este método es suficientemente claro.

No sería necesario crear una jerarquía de clases de estrategias únicamente para representar esta condición, salvo que el problema real requiera políticas de edad intercambiables.

### Aplicación práctica

- Prefiere estructuras claras a soluciones ingeniosas difíciles de comprender.
- Añade abstracciones cuando resuelvan un problema real.
- No anticipes todas las funcionalidades imaginables.
- Evita generalizar antes de comprender los requisitos.
- Elimina código muerto y dependencias innecesarias.

Simplicidad no significa falta de diseño. Significa diseñar sin complejidad que no aporte valor.

---

## 10. Principio YAGNI

YAGNI significa **You Aren't Gonna Need It**: no implementes funcionalidades que todavía no necesitas.

Se relaciona con KISS, pero pone el énfasis en evitar funcionalidades especulativas.

### Ejemplo

Si la aplicación solo necesita enviar notificaciones por correo y no existe un requisito de admitir varios canales, quizá no sea necesario implementar desde el principio una infraestructura completa de plugins para correo, SMS, notificaciones push, fax y otros medios.

Una abstracción como `NotificationService` puede seguir siendo razonable si representa un límite real. Lo que debe evitarse es implementar complejidad futura sin evidencia de que será necesaria.

### Buenas prácticas

- Implementa los requisitos actuales con una estructura extensible cuando esté justificado.
- Evita funcionalidades hipotéticas.
- No confundas anticipar una evolución probable con construir un sistema para cualquier escenario imaginable.
- Refactoriza cuando los nuevos requisitos lo justifiquen.

YAGNI no prohíbe diseñar pensando en el futuro; recomienda no pagar hoy el coste de funcionalidades que aún no se necesitan.

---

## 11. Principios SOLID y su relación con Clean Code

SOLID es un conjunto de cinco principios de diseño orientado a objetos que complementan las prácticas de Clean Code.

### 11.1. S — Single Responsibility Principle

**Principio de responsabilidad única.**

Una clase debería tener una responsabilidad bien definida y, en la formulación habitual, un único motivo principal para cambiar.

Una clase que valida pedidos, calcula impuestos, guarda datos y envía correos puede tener demasiados motivos de cambio.

Una alternativa es separar esas responsabilidades cuando representen conceptos diferenciados.

### 11.2. O — Open/Closed Principle

**Principio abierto/cerrado.**

Las entidades de software deberían poder extenderse para admitir comportamientos nuevos sin tener que modificar continuamente código estable.

No significa que nunca pueda modificarse una clase existente. Significa que las abstracciones adecuadas pueden reducir el impacto de determinadas ampliaciones.

### 11.3. L — Liskov Substitution Principle

**Principio de sustitución de Liskov.**

Una implementación derivada debe poder sustituir a su tipo base sin incumplir las expectativas del contrato.

Por ejemplo, una implementación de repositorio no debería devolver resultados incompatibles con las garantías que establece la interfaz.

### 11.4. I — Interface Segregation Principle

**Principio de segregación de interfaces.**

Es preferible que los clientes dependan de interfaces pequeñas y relevantes, en lugar de depender de contratos amplios con operaciones que no necesitan.

### 11.5. D — Dependency Inversion Principle

**Principio de inversión de dependencias.**

Los componentes de alto nivel deberían depender de abstracciones, en lugar de depender directamente de detalles de bajo nivel.

Por ejemplo, un servicio de aplicación puede depender de una interfaz de repositorio y dejar que la infraestructura proporcione su implementación.

### Ejemplo

```java
public interface PaymentGateway {
    PaymentResult charge(PaymentRequest request);
}
```

```java
public class CheckoutService {

    private final PaymentGateway paymentGateway;

    public CheckoutService(PaymentGateway paymentGateway) {
        this.paymentGateway = paymentGateway;
    }

    public PaymentResult checkout(PaymentRequest request) {
        return paymentGateway.charge(request);
    }
}
```

`CheckoutService` no necesita conocer los detalles internos de un proveedor de pagos concreto.

**Importante:** SOLID no es una receta para crear interfaces en todas partes. Su propósito es reducir acoplamiento, clarificar responsabilidades y facilitar la evolución cuando el problema lo justifica.

---

## 12. Principios complementarios: DRY, KISS, YAGNI y separación de responsabilidades

Estos principios suelen combinarse con Clean Code.

| Principio | Idea principal | Pregunta útil |
|---|---|---|
| DRY | Evitar duplicar conocimiento | ¿Esta regla se implementa de manera independiente en varios lugares? |
| KISS | Reducir la complejidad innecesaria | ¿Existe una solución más sencilla y clara? |
| YAGNI | Evitar funcionalidades especulativas | ¿Necesitamos realmente esta funcionalidad ahora? |
| SRP | Mantener responsabilidades coherentes | ¿Esta clase tiene demasiados motivos para cambiar? |
| SoC | Separar aspectos diferentes del sistema | ¿Se están mezclando responsabilidades que evolucionan por motivos distintos? |
| DIP | Depender de abstracciones apropiadas | ¿Este componente está acoplado a detalles que debería poder sustituir? |

Estos principios pueden entrar en tensión. Por ejemplo, eliminar toda duplicación mediante una abstracción excesivamente genérica puede empeorar la claridad.

La decisión correcta depende de la cohesión conceptual, la evolución esperada y el coste de mantenimiento.

---

## 13. Evitar números mágicos y cadenas mágicas

Un valor mágico es un literal cuyo significado no resulta evidente en el contexto.

### Ejemplo poco claro

```java
if (user.getFailedAttempts() >= 5) {
    user.lock();
}
```

¿Qué representa el número `5`? ¿Es un límite de seguridad, una regla temporal o una condición arbitraria?

### Versión más expresiva

```java
private static final int MAX_FAILED_LOGIN_ATTEMPTS = 5;

if (user.getFailedAttempts() >= MAX_FAILED_LOGIN_ATTEMPTS) {
    user.lock();
}
```

El nombre documenta la intención y facilita modificar el valor.

En un proyecto real, un límite de seguridad puede necesitar configuración centralizada o una política de dominio, en lugar de una constante local.

### Cadenas mágicas

**Evitar:**

```java
if ("PAID".equals(order.getStatus())) {
    // ...
}
```

**Preferir:**

```java
if (order.getStatus() == OrderStatus.PAID) {
    // ...
}
```

Un `enum` reduce errores tipográficos y representa de forma más segura un conjunto finito de estados.

No todos los literales necesitan convertirse en constantes: un `0` en un cálculo matemático sencillo puede ser perfectamente claro. El objetivo es dar nombre a los valores cuyo significado no sea obvio o cuya modificación deba controlarse.

---

## 14. Inmutabilidad y gestión del estado

La inmutabilidad consiste en que un objeto no pueda modificar su estado observable después de construirse.

Puede simplificar el razonamiento sobre el programa, reducir efectos secundarios y facilitar la concurrencia.

### Ejemplo de objeto inmutable

```java
public final class Money {

    private final BigDecimal amount;
    private final Currency currency;

    public Money(BigDecimal amount, Currency currency) {
        this.amount = Objects.requireNonNull(amount);
        this.currency = Objects.requireNonNull(currency);

        if (amount.scale() < 0) {
            throw new IllegalArgumentException(
                    "La escala no puede ser negativa"
            );
        }
    }

    public BigDecimal amount() {
        return amount;
    }

    public Currency currency() {
        return currency;
    }
}
```

El ejemplo ilustra la estructura básica, pero la regla sobre la escala es solo demostrativa: no constituye una política monetaria universal. Las reglas de precisión, redondeo y escala deben definirse de acuerdo con la moneda y el dominio.

En Java moderno, un `record` también puede resultar adecuado para representar datos inmutables:

```java
public record CustomerId(long value) {
}
```

### Ventajas

- Menos cambios de estado inesperados.
- Más facilidad para razonar sobre los datos.
- Menos riesgo de efectos secundarios.
- Mejor comportamiento en determinados escenarios concurrentes.

### Precauciones

La inmutabilidad superficial no garantiza que todo el estado sea inmutable. Si un objeto contiene una lista mutable, debe evitarse exponerla directamente cuando se requiera una garantía de inmutabilidad.

No todos los objetos tienen que ser inmutables: las entidades de dominio pueden necesitar transiciones de estado explícitas.

---

## 15. Evitar efectos secundarios inesperados

Un método produce un efecto secundario cuando modifica estado, escribe datos, realiza operaciones externas o altera el entorno, además de calcular un resultado.

Los efectos secundarios no son malos por sí mismos; son necesarios para que el software haga cosas. El problema aparece cuando no están claros o se mezclan con operaciones que parecen puramente informativas.

### Ejemplo ambiguo

```java
public double getTotal(Order order) {
    order.setLastCalculatedAt(Instant.now());

    return order.calculateTotal();
}
```

El nombre `getTotal` sugiere una consulta, pero el método modifica el pedido.

Una alternativa sería separar ambas operaciones:

```java
public double calculateTotal(Order order) {
    return order.calculateTotal();
}
```

Y, si la aplicación necesita registrar cuándo se calculó el importe, hacerlo explícitamente en el componente responsable de esa operación.

**Regla práctica:** los métodos deberían hacer lo que su nombre y contrato prometen, sin cambios ocultos de estado.

---

## 16. Evitar booleanos que controlan múltiples comportamientos

Un método con muchos parámetros booleanos suele ocultar decisiones difíciles de interpretar.

### Ejemplo

```java
generateReport(report, true, false, true);
```

¿Qué significa cada valor? ¿Incluir detalles, exportar PDF o enviar el informe?

### Alternativa con opciones explícitas

```java
public record ReportOptions(
        boolean includeDetails,
        boolean exportPdf,
        boolean sendByEmail
) {
}
```

```java
generateReport(
        report,
        new ReportOptions(true, false, true)
);
```

El significado de cada opción queda más claro en el lugar donde se construye el objeto.

Otra alternativa puede ser utilizar métodos especializados o un patrón Builder cuando existan muchas opciones configurables.

No es necesario reemplazar cualquier booleano por una clase. El problema surge cuando la combinación de parámetros dificulta entender la llamada o permite estados inválidos.

---

## 17. Evitar estructuras de control excesivamente complejas

Las condiciones anidadas dificultan comprender las rutas de ejecución.

### Ejemplo difícil de leer

```java
if (user != null) {
    if (user.isActive()) {
        if (user.hasPermission()) {
            executeOperation();
        }
    }
}
```

### Alternativa con cláusulas de guarda

```java
public void executeIfAllowed(User user) {
    if (user == null) {
        return;
    }

    if (!user.isActive()) {
        return;
    }

    if (!user.hasPermission()) {
        return;
    }

    executeOperation();
}
```

Las cláusulas de guarda permiten descartar rápidamente los casos que impiden ejecutar la operación.

Otra opción es encapsular la condición en un método con nombre significativo:

```java
if (canExecuteOperation(user)) {
    executeOperation();
}
```

### Precaución

No todas las condiciones anidadas son malas. En ocasiones, expresan de manera natural una jerarquía de decisiones. El objetivo es que el flujo de control sea fácil de seguir y no introduzca complejidad innecesaria.

---

## 18. Colecciones y tratamiento de valores nulos

Los contratos explícitos reducen errores y simplifican el uso de las APIs.

### 18.1. Evitar devolver `null` para colecciones vacías

En muchos contratos es más sencillo devolver una colección vacía:

```java
public List<Order> findOrdersByCustomer(long customerId) {
    return repository.findByCustomerId(customerId);
}
```

Si el repositorio no encuentra pedidos, puede devolver una lista vacía en lugar de `null`.

Así, el código cliente puede iterar sin comprobar siempre si la colección existe.

### 18.2. Comprobar las entradas obligatorias

```java
public OrderService(OrderRepository repository) {
    this.repository = Objects.requireNonNull(repository);
}
```

Esta comprobación hace explícita una condición del constructor.

### 18.3. Evitar comprobaciones de nulidad indiscriminadas

Si un contrato garantiza que un método nunca devuelve `null`, añadir comprobaciones redundantes por todas partes puede dificultar la lectura.

La mejor estrategia es definir contratos consistentes y validar los límites del sistema, donde los datos externos pueden ser impredecibles.

### 18.4. Usar `Optional` con criterio

`Optional<T>` es útil para expresar que un resultado puede estar ausente, especialmente como tipo de retorno.

No es necesario usarlo como sustituto universal de todos los campos, parámetros o estructuras de datos.

---

## 19. Diseño de clases y encapsulación

La encapsulación consiste en controlar cómo se accede al estado y cómo se modifican los datos de un objeto.

### Ejemplo de diseño débil

```java
public class BankAccount {
    public double balance;
}
```

Cualquier componente puede modificar el saldo directamente, incluso asignando valores inválidos.

### Alternativa

```java
public class BankAccount {

    private BigDecimal balance;

    public BankAccount(BigDecimal initialBalance) {
        if (initialBalance.signum() < 0) {
            throw new IllegalArgumentException(
                    "El saldo inicial no puede ser negativo"
            );
        }

        this.balance = initialBalance;
    }

    public BigDecimal getBalance() {
        return balance;
    }

    public void deposit(BigDecimal amount) {
        requirePositive(amount);
        balance = balance.add(amount);
    }

    private void requirePositive(BigDecimal amount) {
        Objects.requireNonNull(amount);

        if (amount.signum() <= 0) {
            throw new IllegalArgumentException(
                    "El importe debe ser positivo"
            );
        }
    }
}
```

Este ejemplo representa una cuenta con una política simplificada que prohíbe saldos iniciales negativos. En un sistema bancario real, las reglas sobre descubiertos, divisas, precisión, concurrencia y operaciones autorizadas tendrían que modelarse explícitamente.

La idea principal es que la clase controla sus propias invariantes y no permite que otros componentes las vulneren mediante modificaciones arbitrarias.

---

## 20. Composición frente a herencia

La herencia puede ser útil cuando existe una relación de sustitución válida entre tipos.

Sin embargo, utilizarla únicamente para reutilizar código puede crear jerarquías rígidas y difíciles de mantener.

### Ejemplo de composición

```java
public interface DiscountPolicy {
    BigDecimal apply(BigDecimal subtotal);
}
```

```java
public class CheckoutService {

    private final DiscountPolicy discountPolicy;

    public CheckoutService(DiscountPolicy discountPolicy) {
        this.discountPolicy = discountPolicy;
    }

    public BigDecimal calculateTotal(BigDecimal subtotal) {
        return discountPolicy.apply(subtotal);
    }
}
```

`CheckoutService` delega el cálculo del descuento a una política configurable.

Puede existir una implementación para clientes habituales y otra para campañas promocionales.

### Ventajas de la composición

- Permite cambiar comportamientos sin crear numerosas subclases.
- Facilita sustituir dependencias.
- Reduce algunas formas de acoplamiento rígido.
- Favorece la combinación de comportamientos.

### Precaución

La composición también puede generar demasiados componentes y niveles de delegación.

Debe utilizarse cuando los comportamientos sean realmente variables o representen responsabilidades independientes, no como una prohibición absoluta de la herencia.

---

## 21. Programar contra interfaces

Depender de interfaces permite separar el contrato de una operación de su implementación concreta.

### Ejemplo

```java
public interface UserRepository {
    Optional<User> findById(long id);

    void save(User user);
}
```

```java
public class UserService {

    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public User getUser(long id) {
        return userRepository.findById(id)
                .orElseThrow(() ->
                        new UserNotFoundException(id)
                );
    }
}
```

`UserService` no necesita conocer si los usuarios se guardan en una base de datos relacional, en un sistema remoto o en una implementación en memoria.

### Beneficios

- Facilita las pruebas con dobles de prueba.
- Reduce la dependencia de detalles de infraestructura.
- Permite sustituir implementaciones.
- Favorece la inversión de dependencias.

### Cuándo no es necesario

Si una clase tiene una única implementación estable y no existe una razón de diseño para introducir una abstracción, añadir una interfaz puede aportar poco valor.

La abstracción debería responder a una necesidad, no a una regla mecánica.

---

## 22. Pruebas automatizadas como parte del código limpio

El código limpio no se limita al código de producción. Las pruebas también deben ser legibles, fiables y mantenibles.

### 22.1. Una prueba, una intención principal

Una prueba debe verificar un comportamiento claramente identificable.

Esto no significa que solo pueda contener una aserción, sino que debería resultar fácil comprender qué comportamiento se está comprobando y por qué ha fallado.

### 22.2. Nombres descriptivos

```java
@Test
void shouldRejectPaymentWhenAmountIsNegative() {
    // ...
}
```

El nombre explica el escenario y el resultado esperado.

### 22.3. Estructura Arrange-Act-Assert

Una estructura habitual es:

- **Arrange:** preparar los datos y las dependencias.
- **Act:** ejecutar la operación.
- **Assert:** verificar el resultado.

```java
@Test
void shouldApplyTenPercentDiscount() {
    // Arrange
    DiscountPolicy policy = new TenPercentDiscount();
    BigDecimal subtotal = new BigDecimal("100.00");

    // Act
    BigDecimal total = policy.apply(subtotal);

    // Assert
    assertEquals(
            new BigDecimal("90.0000"),
            total
    );
}
```

Este ejemplo depende de cómo esté implementada la política y de su escala de salida. En un sistema monetario real, es preferible definir expresamente la escala y el redondeo para que el resultado esperado sea consistente con el contrato.

### 22.4. Evitar pruebas frágiles

Una prueba no debería fallar por detalles internos que no forman parte del comportamiento contractual.

Por ejemplo, comprobar el orden exacto de llamadas internas puede hacer que una prueba deje de ser útil después de una refactorización que conserva el mismo comportamiento externo.

### 22.5. Pirámide de pruebas

Una estrategia equilibrada suele combinar:

- **Pruebas unitarias:** verifican unidades de comportamiento de manera aislada.
- **Pruebas de integración:** verifican la colaboración entre componentes reales.
- **Pruebas de extremo a extremo:** comprueban flujos completos a través del sistema.

No existe una proporción universal. La combinación depende del coste de ejecución, los riesgos y las características de la aplicación.

---

## 23. Refactorización continua

Refactorizar significa modificar la estructura interna del código sin cambiar su comportamiento observable.

La refactorización es una práctica fundamental para mantener la calidad a medida que aparecen nuevos requisitos.

### Ejemplos de refactorización

- Cambiar nombres ambiguos.
- Extraer métodos con significado propio.
- Dividir clases con responsabilidades mezcladas.
- Eliminar duplicación de conocimiento.
- Reducir dependencias innecesarias.
- Sustituir números mágicos por nombres expresivos.
- Simplificar condiciones complejas.
- Introducir una abstracción cuando varias implementaciones realmente la necesitan.

### Flujo recomendado

1. Asegurar que existe una forma razonable de verificar el comportamiento.
2. Realizar un cambio pequeño.
3. Ejecutar las pruebas.
4. Revisar el resultado.
5. Repetir.

La refactorización no debería utilizarse como excusa para cambiar simultáneamente el comportamiento, añadir funcionalidades y reorganizar grandes partes del sistema sin una estrategia clara.

---

## 24. Code smells: señales de un diseño problemático

Un *code smell* es una señal de que el código podría tener un problema de diseño. No implica necesariamente que exista un error funcional ni que haya que refactorizar inmediatamente.

### 24.1. Long Method

Un método realiza demasiadas operaciones o combina varias responsabilidades.

**Posible solución:** extraer operaciones con significado propio y separar responsabilidades.

### 24.2. Large Class

Una clase concentra demasiados datos, comportamientos o motivos de cambio.

**Posible solución:** dividirla por responsabilidades coherentes.

### 24.3. Duplicated Code

Una regla se implementa en varios lugares independientes.

**Posible solución:** identificar si existe conocimiento compartido y extraerlo cuando la abstracción sea adecuada.

### 24.4. Long Parameter List

Un método requiere tantos parámetros que resulta difícil entender la llamada.

**Posible solución:** agrupar datos relacionados en un objeto cuando representen un concepto real, o dividir la operación.

### 24.5. Primitive Obsession

Se utilizan tipos primitivos para representar conceptos de dominio complejos.

Por ejemplo, usar una cadena para un identificador de cliente puede permitir mezclarlo accidentalmente con un identificador de pedido.

**Posible solución:** introducir tipos de dominio como `CustomerId` y `OrderId` cuando aporten seguridad y claridad.

### 24.6. Feature Envy

Un método utiliza principalmente los datos de otra clase en lugar de los datos o comportamientos de la clase en la que reside.

**Posible solución:** revisar si el comportamiento debería pertenecer a la clase cuyos datos utiliza.

### 24.7. Shotgun Surgery

Un cambio pequeño obliga a modificar muchas clases sin una razón clara.

**Posible solución:** revisar la distribución de responsabilidades y las dependencias.

### 24.8. God Object

Una clase centraliza gran parte de la lógica del sistema.

**Posible solución:** extraer responsabilidades y definir límites más claros.

### 24.9. Premature Abstraction

Se construyen jerarquías y generalizaciones antes de comprender si existen variaciones reales.

**Posible solución:** empezar con una solución clara y refactorizar cuando los requisitos justifiquen una abstracción.

**Importante:** los code smells son indicios, no reglas absolutas. La decisión de refactorizar debe considerar el contexto, el riesgo y el coste del cambio.

---

## 25. Clean Code y los patrones de diseño

Clean Code y los patrones de diseño se complementan, pero no son lo mismo.

Un patrón de diseño ofrece una estructura conocida para resolver un problema recurrente. Clean Code ayuda a implementar esa solución de forma clara y mantenible.

### Ejemplo: Strategy

El patrón Strategy permite intercambiar algoritmos mediante una abstracción.

Una implementación poco clara podría utilizar nombres genéricos, condiciones ambiguas y clases con múltiples responsabilidades.

Una implementación más expresiva sería:

```java
public interface ShippingCostPolicy {
    BigDecimal calculateCost(Order order);
}
```

```java
public class StandardShippingPolicy
        implements ShippingCostPolicy {

    @Override
    public BigDecimal calculateCost(Order order) {
        return new BigDecimal("5.00");
    }
}
```

El nombre de la interfaz comunica el concepto de dominio, y la implementación expresa una política concreta.

### Relación con otros patrones

| Patrón | Cómo puede ayudar Clean Code |
|---|---|
| Strategy | Facilita nombrar políticas y separar algoritmos intercambiables. |
| Factory | Permite encapsular la creación de objetos cuando existen decisiones reales de construcción. |
| Builder | Hace más comprensible la creación de objetos con muchos parámetros opcionales. |
| Observer | Puede separar emisores de eventos y suscriptores, siempre que el flujo de eventos sea claro. |
| Facade | Simplifica el uso de subsistemas complejos mediante una interfaz expresiva. |
| Adapter | Separa el modelo interno de una interfaz externa incompatible. |
| Decorator | Añade comportamientos de manera compositiva cuando existe una necesidad real. |
| Command | Representa operaciones como objetos y puede facilitar su registro, ejecución o composición. |

Los patrones no garantizan un buen diseño por sí mismos. Una implementación excesivamente compleja de un patrón puede ser menos limpia que una solución directa.

---

## 26. Clean Code en arquitecturas de software

Clean Code puede aplicarse independientemente de la arquitectura elegida.

### 26.1. Arquitectura monolítica

Permite organizar módulos y responsabilidades dentro de una misma aplicación desplegable.

La separación entre clases, módulos y capas sigue siendo importante aunque no existan microservicios.

### 26.2. Arquitectura de microservicios

Ayuda a mantener legibles los servicios y sus límites, pero no sustituye el diseño de contratos, la observabilidad ni la gestión de fallos distribuidos.

### 26.3. Arquitectura hexagonal

Favorece contratos claros entre el dominio y los adaptadores externos.

### 26.4. Clean Architecture

Complementa la organización de las dependencias con nombres significativos, responsabilidades claras y pruebas mantenibles.

### 26.5. MVC, MVP y MVVM

Ayuda a mantener controladores, Presenters y ViewModels centrados en la lógica de presentación, evitando que se conviertan en clases con demasiadas responsabilidades.

La arquitectura define principalmente la organización y los límites del sistema; Clean Code se aplica también a los detalles internos de cada componente.

---

## 27. Buenas prácticas adicionales

### 27.1. Mantener un estilo consistente

Utiliza convenciones de nombres, formato y organización coherentes en todo el proyecto.

La consistencia reduce el esfuerzo necesario para interpretar el código.

### 27.2. Utilizar herramientas automáticas

Los analizadores estáticos, formateadores y linters pueden detectar problemas antes de que lleguen a producción.

En Java, pueden considerarse herramientas como:

- Checkstyle.
- PMD.
- SpotBugs.
- SonarQube o SonarLint.
- El formateador del IDE.
- Las comprobaciones de compilación y pruebas del sistema de integración continua.

Estas herramientas ayudan, pero no sustituyen la revisión del diseño ni el criterio del equipo.

### 27.3. Mantener dependencias actualizadas

Las dependencias deben gestionarse de forma consciente, atendiendo a la compatibilidad, las vulnerabilidades y el impacto de las actualizaciones.

### 27.4. No ignorar las advertencias sin motivo

Una advertencia puede indicar un problema real. Si se decide ignorarla, conviene comprenderla y documentar la razón cuando sea relevante.

### 27.5. Revisar el código entre compañeros

Una revisión de código debería centrarse en aspectos como:

- Correctitud.
- Claridad.
- Responsabilidades.
- Manejo de errores.
- Seguridad.
- Pruebas.
- Coherencia con la arquitectura.
- Complejidad innecesaria.

### 27.6. Medir antes de optimizar

La legibilidad es importante, pero el software también debe cumplir sus requisitos de rendimiento.

Utiliza mediciones y perfiles cuando existan problemas reales de rendimiento. No introduzcas complejidad basándote únicamente en suposiciones.

### 27.7. Documentar las decisiones importantes

El código explica qué hace una implementación, pero puede no explicar por qué se eligió una alternativa frente a otra.

Para decisiones arquitectónicas relevantes, los documentos de decisión arquitectónica (ADR) pueden registrar el contexto, las alternativas y las consecuencias.

---

## 28. Ejemplo integral: refactorización de un servicio de pedidos

Veamos cómo se aplican varios principios de Clean Code en un ejemplo.

### 28.1. Implementación problemática

```java
public class OrderProcessor {

    public void process(Order order, boolean sendEmail) {
        if (order == null) {
            throw new RuntimeException("error");
        }

        double total = 0;

        for (OrderItem item : order.getItems()) {
            total += item.getPrice() * item.getQuantity();
        }

        if (total > 100) {
            total = total * 0.9;
        }

        order.setTotal(total);
        Database.save(order);

        if (sendEmail) {
            Email.send(
                    order.getCustomerEmail(),
                    "Pedido procesado"
            );
        }

        System.out.println("OK");
    }
}
```

### Problemas detectados

- La excepción es demasiado genérica.
- Los nombres no expresan todas las reglas del negocio.
- Se utiliza `double` para importes monetarios.
- La clase calcula, persiste y notifica.
- Las dependencias son estáticas y difíciles de sustituir.
- El parámetro booleano hace menos expresiva la llamada.
- El descuento está codificado directamente en el método.
- La salida a consola mezcla responsabilidades de negocio y presentación.
- No queda claro cómo deben tratarse pedidos vacíos, artículos inválidos o errores de persistencia.

### 28.2. Propuesta de diseño

```text
OrderApplicationService
├── OrderTotalCalculator
├── DiscountPolicy
├── OrderRepository
└── OrderNotificationService
```

Cada componente representa una responsabilidad diferenciada. La organización exacta dependerá de los requisitos y del tamaño real del sistema.

### 28.3. Calculadora de totales

```java
public class OrderTotalCalculator {

    public BigDecimal calculateSubtotal(Order order) {
        return order.getItems().stream()
                .map(item -> item.getUnitPrice()
                        .multiply(
                                BigDecimal.valueOf(
                                        item.getQuantity()
                                )
                        )
                )
                .reduce(BigDecimal.ZERO, BigDecimal::add);
    }
}
```

Esta clase se centra en el subtotal.

En una aplicación real, el dominio debería definir qué cantidades son válidas y cómo se gestionan las escalas y el redondeo monetario.

### 28.4. Política de descuento

```java
public interface DiscountPolicy {
    BigDecimal apply(BigDecimal subtotal);
}
```

```java
public class ThresholdDiscountPolicy
        implements DiscountPolicy {

    private final BigDecimal minimumSubtotal;
    private final BigDecimal discountRate;

    public ThresholdDiscountPolicy(
            BigDecimal minimumSubtotal,
            BigDecimal discountRate
    ) {
        this.minimumSubtotal = minimumSubtotal;
        this.discountRate = discountRate;
    }

    @Override
    public BigDecimal apply(BigDecimal subtotal) {
        if (subtotal.compareTo(minimumSubtotal) > 0) {
            return subtotal.multiply(
                    BigDecimal.ONE.subtract(discountRate)
            );
        }

        return subtotal;
    }
}
```

Esta política permite configurar el umbral y el descuento sin modificar la calculadora.

En producción, el constructor debería validar los parámetros y el sistema debería definir la escala y la política de redondeo.

### 28.5. Servicio de aplicación

```java
public class OrderApplicationService {

    private final OrderTotalCalculator calculator;
    private final DiscountPolicy discountPolicy;
    private final OrderRepository repository;
    private final OrderNotificationService notificationService;

    public OrderApplicationService(
            OrderTotalCalculator calculator,
            DiscountPolicy discountPolicy,
            OrderRepository repository,
            OrderNotificationService notificationService
    ) {
        this.calculator = calculator;
        this.discountPolicy = discountPolicy;
        this.repository = repository;
        this.notificationService = notificationService;
    }

    public void processOrder(Order order) {
        Objects.requireNonNull(order, "El pedido es obligatorio");

        BigDecimal subtotal = calculator.calculateSubtotal(order);
        BigDecimal total = discountPolicy.apply(subtotal);

        order.setTotal(total);
        repository.save(order);

        notificationService.notifyOrderProcessed(order);
    }
}
```

Este ejemplo separa responsabilidades y utiliza dependencias explícitas. No pretende ser una implementación de producción completa: todavía habría que definir las invariantes de `Order`, el tratamiento de fallos, la consistencia de la persistencia y el comportamiento de las notificaciones.

**Observación importante:** dividir código en más clases no garantiza automáticamente una mejora. La calidad proviene de que las responsabilidades sean coherentes y los límites tengan sentido.

---

## 29. Seguridad como parte de la calidad del código

Un código legible y mantenible también debe evitar errores de seguridad.

Algunas prácticas importantes son:

- Validar los datos en los límites de confianza.
- No construir consultas SQL concatenando entradas del usuario.
- Utilizar consultas parametrizadas.
- No registrar contraseñas, tokens o datos sensibles.
- No devolver trazas internas o detalles confidenciales al usuario.
- Comprobar la autorización en el servidor, no solo en la interfaz.
- Gestionar correctamente los secretos y las credenciales.
- Mantener dependencias con vulnerabilidades conocidas bajo control.
- Definir límites de tamaño y formatos para datos externos.
- Evitar que los errores revelen información innecesaria.

### Ejemplo de consulta parametrizada

En lugar de concatenar una entrada:

```java
String sql =
        "SELECT * FROM users WHERE email = '" + email + "'";
```

Utiliza parámetros mediante la API de acceso a datos:

```java
String sql = "SELECT * FROM users WHERE email = ?";

try (PreparedStatement statement =
             connection.prepareStatement(sql)) {

    statement.setString(1, email);

    try (ResultSet resultSet = statement.executeQuery()) {
        // Procesar el resultado.
    }
}
```

Las consultas parametrizadas reducen el riesgo de inyección SQL para los valores suministrados como parámetros. No sustituyen otras medidas de seguridad, como la autorización, la gestión de privilegios y la validación apropiada de entradas.

---

## 30. Checklist de Clean Code para revisar una clase

Antes de considerar terminada una clase, puedes utilizar esta lista como guía.

### Legibilidad

- [ ] Los nombres expresan la intención.
- [ ] Las condiciones son comprensibles.
- [ ] Los métodos tienen un propósito claro.
- [ ] Los comentarios aportan contexto o explican decisiones.
- [ ] Los valores mágicos están identificados cuando su significado no es evidente.

### Responsabilidades

- [ ] La clase tiene una responsabilidad coherente.
- [ ] No mezcla innecesariamente lógica de negocio, persistencia y presentación.
- [ ] Las dependencias son razonables.
- [ ] La clase mantiene la cohesión.
- [ ] Las abstracciones tienen una justificación concreta.

### Robustez

- [ ] Las entradas se validan en los límites adecuados.
- [ ] Los errores se gestionan explícitamente.
- [ ] No se ocultan excepciones importantes.
- [ ] Los efectos secundarios son previsibles.
- [ ] Los contratos de retorno son claros.
- [ ] Las reglas de seguridad relevantes están cubiertas.

### Mantenibilidad

- [ ] No hay duplicación innecesaria de conocimiento.
- [ ] No hay funcionalidades especulativas sin justificar.
- [ ] La solución no tiene complejidad innecesaria.
- [ ] Las dependencias pueden sustituirse cuando es útil.
- [ ] Las reglas de dominio están ubicadas en el componente apropiado.

### Pruebas

- [ ] Existe una estrategia de pruebas apropiada para el riesgo.
- [ ] Las pruebas tienen nombres descriptivos.
- [ ] Los escenarios importantes están cubiertos.
- [ ] Los errores relevantes están probados.
- [ ] Las pruebas no dependen innecesariamente de detalles internos.

---

## 31. Checklist de Clean Code para una revisión de Pull Request

Esta lista es útil para revisar cambios realizados por otros desarrolladores.

- [ ] ¿El cambio cumple el requisito solicitado?
- [ ] ¿El código comunica claramente su intención?
- [ ] ¿Los nombres son coherentes con el dominio?
- [ ] ¿Se han introducido dependencias innecesarias?
- [ ] ¿Se están mezclando responsabilidades?
- [ ] ¿Hay duplicación de una regla de negocio?
- [ ] ¿Se han añadido abstracciones sin una necesidad clara?
- [ ] ¿Los errores se gestionan correctamente?
- [ ] ¿Se han considerado entradas nulas, inválidas o inesperadas?
- [ ] ¿Existen pruebas para el comportamiento nuevo?
- [ ] ¿Las pruebas cubren los casos límite relevantes?
- [ ] ¿El cambio respeta la arquitectura del proyecto?
- [ ] ¿Hay riesgos de seguridad?
- [ ] ¿Se han eliminado código muerto y comentarios obsoletos?
- [ ] ¿Se ha comprobado el impacto sobre rendimiento y compatibilidad cuando corresponde?

Una revisión de calidad no debería convertirse en una búsqueda de preferencias estilísticas personales. Es preferible priorizar correctitud, claridad, seguridad, consistencia y coste de mantenimiento.

---

## 32. Plan de adopción progresiva de Clean Code

Intentar aplicar todas las prácticas simultáneamente a un proyecto grande puede resultar costoso.

Una estrategia progresiva suele ser más eficaz.

### Fase 1. Convenciones de estilo

- Definir convenciones de nombres.
- Configurar el formateador.
- Activar comprobaciones estáticas básicas.
- Acordar criterios de revisión.

### Fase 2. Mejorar la legibilidad

- Renombrar variables y métodos ambiguos.
- Eliminar comentarios obsoletos.
- Simplificar condiciones difíciles de comprender.
- Reducir métodos que mezclan tareas independientes.

### Fase 3. Reducir riesgos

- Añadir pruebas a las funcionalidades críticas.
- Mejorar el tratamiento de errores.
- Revisar entradas externas y límites de confianza.
- Eliminar estados globales innecesarios.

### Fase 4. Mejorar el diseño

- Separar responsabilidades.
- Reducir acoplamiento.
- Introducir abstracciones cuando aporten valor.
- Aplicar SOLID de forma pragmática.

### Fase 5. Integrar la calidad en el flujo diario

- Ejecutar pruebas automáticamente.
- Integrar análisis estático en CI.
- Revisar los cambios mediante Pull Requests.
- Refactorizar pequeñas áreas de riesgo de manera continua.

La calidad del código debe mantenerse durante el desarrollo, no reservarse únicamente para una gran refactorización al final del proyecto.

---

## 33. Conclusión

**Clean Code es una práctica continua de diseño, implementación y mantenimiento del software.**

Su objetivo principal es que el código exprese claramente su intención, limite la complejidad innecesaria, mantenga responsabilidades coherentes y pueda evolucionar con un riesgo razonable.

Los principios más importantes que conviene recordar son:

1. **Legibilidad:** el código debe ser fácil de comprender.
2. **Simplicidad:** evita complejidad que no aporta valor.
3. **Responsabilidad única:** mantén las clases y métodos centrados en propósitos coherentes.
4. **Nombres significativos:** expresa la intención mediante el código.
5. **DRY:** evita duplicar conocimiento.
6. **KISS:** prefiere soluciones sencillas y claras.
7. **YAGNI:** no construyas funcionalidades especulativas.
8. **SOLID:** utiliza sus principios para mejorar el diseño orientado a objetos.
9. **Manejo explícito de errores:** define cómo se comporta el sistema ante los fallos.
10. **Pruebas automatizadas:** verifica el comportamiento y permite refactorizar con confianza.
11. **Refactorización continua:** mejora la estructura de forma incremental.
12. **Seguridad:** protege los límites de confianza y evita exponer información sensible.

La idea más importante es que Clean Code no consiste en cumplir reglas mecánicamente ni en crear la mayor cantidad posible de clases, interfaces o abstracciones.

Consiste en tomar decisiones que hagan que el software sea **más claro, más seguro, más fácil de probar y menos costoso de modificar**.

---

## 34. Referencias y lecturas recomendadas

### Libros

- Robert C. Martin — *Clean Code: A Handbook of Agile Software Craftsmanship*.
- Robert C. Martin — *Agile Software Development: Principles, Patterns, and Practices*.
- Martin Fowler — *Refactoring: Improving the Design of Existing Code*.
- Joshua Bloch — *Effective Java*.
- Andrew Hunt y David Thomas — *The Pragmatic Programmer*.
- Erich Gamma, Richard Helm, Ralph Johnson y John Vlissides — *Design Patterns: Elements of Reusable Object-Oriented Software*.

### Recursos técnicos

- Martin Fowler — Refactoring: https://refactoring.com/
- Principios de diseño de Java: https://docs.oracle.com/javase/tutorial/java/concepts/
- Documentación oficial de Java: https://docs.oracle.com/en/java/
- JUnit 5: https://junit.org/junit5/
- OWASP Cheat Sheet Series: https://cheatsheetseries.owasp.org/
- SonarQube: https://www.sonarsource.com/products/sonarqube/

Estas referencias permiten profundizar en la legibilidad, la refactorización, los principios de diseño orientado a objetos, las pruebas automatizadas y las prácticas de seguridad que complementan Clean Code.
