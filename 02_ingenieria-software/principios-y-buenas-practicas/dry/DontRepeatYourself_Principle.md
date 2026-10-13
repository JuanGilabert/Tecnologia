# DRY (Don't Repeat Yourself): principios y buenas prácticas de programación

## 1. Introducción

**DRY (Don't Repeat Yourself)** significa «No te repitas».

Es un principio de diseño de software que propone evitar la duplicación innecesaria de conocimiento dentro de un sistema.

Su formulación clásica procede del libro *The Pragmatic Programmer*, de Andrew Hunt y David Thomas. La idea fundamental es que cada pieza de conocimiento importante debería tener una representación autoritativa dentro del sistema, de modo que no sea necesario actualizar varias implementaciones independientes cuando ese conocimiento cambia.

DRY no significa que nunca debamos repetir código literalmente. Significa que debemos evitar mantener varias versiones independientes de una misma regla, decisión o conocimiento.

Por ejemplo, si una regla de negocio establece que un pedido tiene un descuento determinado a partir de cierto importe, no conviene implementar esa regla de forma independiente en el servicio de pedidos, en el proceso de facturación y en el generador de informes si todos representan la misma política.

La técnica DRY (Don't Repeat Yourself) es uno de los principios fundamentales de la programación mantenible. Está muy relacionada con Clean Code, SOLID, KISS y YAGNI, pero se centra en un aspecto específico: evitar que un mismo conocimiento o una misma regla tenga que mantenerse en varios lugares independientes.

### Objetivos principales

- Evitar duplicaciones de reglas de negocio.
- Reducir el coste de mantenimiento.
- Disminuir el riesgo de inconsistencias.
- Facilitar la modificación de requisitos.
- Centralizar el conocimiento que debe evolucionar de manera conjunta.
- Mejorar la legibilidad y la cohesión del código.
- Reducir errores derivados de actualizar una implementación y olvidar otras.
- Facilitar las pruebas y la evolución del software.

### Idea fundamental

> Una regla que representa un mismo conocimiento no debería tener varias implementaciones independientes que deban mantenerse sincronizadas manualmente.

La clave está en identificar cuándo dos fragmentos de código representan realmente el mismo conocimiento y cuándo son simplemente parecidos.

---

## 2. ¿Qué problema resuelve DRY?

Imaginemos que una aplicación calcula el impuesto de un pedido utilizando una tasa del 21 %.

La regla aparece en tres lugares diferentes.

### 2.1. Ejemplo con duplicación

```java
public class OrderService {

    public BigDecimal calculateTax(BigDecimal amount) {
        return amount.multiply(new BigDecimal("0.21"));
    }
}
```

```java
public class InvoiceService {

    public BigDecimal calculateTax(BigDecimal amount) {
        return amount.multiply(new BigDecimal("0.21"));
    }
}
```

```java
public class ReportService {

    public BigDecimal calculateTax(BigDecimal amount) {
        return amount.multiply(new BigDecimal("0.21"));
    }
}
```

Los tres métodos contienen la misma regla de cálculo.

Supongamos que el requisito cambia y la tasa pasa a ser del 23 % para el escenario al que se aplica esta regla.

Un desarrollador podría actualizar `OrderService` y `InvoiceService`, pero olvidar `ReportService`.

El sistema terminaría mostrando resultados diferentes para un mismo concepto.

### 2.2. Consecuencias de la duplicación

- Inconsistencias entre componentes.
- Mayor esfuerzo para modificar el código.
- Más posibilidades de cometer errores.
- Necesidad de repetir pruebas y revisiones.
- Dificultad para saber cuál es la implementación correcta.
- Riesgo de que los cambios futuros se apliquen solo parcialmente.

DRY busca reducir estos problemas haciendo que el conocimiento compartido tenga una representación centralizada.

**Importante:** en un sistema real pueden existir tasas fiscales diferentes por país, producto, fecha o tipo de operación. No se deben unificar reglas que sean conceptualmente distintas solo porque sus valores coincidan actualmente.

---

## 3. ¿Qué significa realmente «no repetirse»?

Es importante diferenciar entre dos tipos de duplicación.

### 3.1. Duplicación de código

Consiste en repetir las mismas instrucciones o estructuras en varios lugares.

Por ejemplo:

```java
if (user == null) {
    throw new IllegalArgumentException("Usuario obligatorio");
}
```

Si esta comprobación aparece repetida en numerosos métodos y representa exactamente la misma regla, podría ser conveniente centralizarla.

Sin embargo, que dos bloques de código sean textualmente idénticos no demuestra por sí solo que deban convertirse en una abstracción común.

### 3.2. Duplicación de conocimiento

Es la que DRY pretende evitar especialmente.

Existe cuando una misma regla, decisión o política se define de manera independiente en varios lugares.

Por ejemplo:

- La misma regla de autorización implementada en distintos servicios.
- Una tasa de comisión repetida en varios cálculos.
- La misma política de caducidad duplicada en diferentes procesos.
- Una fórmula de cálculo mantenida en varios módulos.
- Una misma restricción de negocio replicada en múltiples componentes.

La duplicación de conocimiento es peligrosa porque obliga a recordar todos los lugares que deben modificarse cuando cambia una regla.

### 3.3. ¿Qué duplicaciones pueden ser aceptables?

Hay ocasiones en las que repetir código es más sencillo y seguro que introducir una abstracción.

Por ejemplo, dos validaciones pueden parecer iguales hoy, pero responder a políticas diferentes que evolucionarán de forma independiente.

Si se crea una única función compartida, cualquier modificación futura podría afectar a ambos comportamientos cuando solo debería afectar a uno.

Por tanto:

**DRY no consiste en eliminar toda repetición textual, sino en evitar la duplicación innecesaria de conocimiento que debe mantenerse coherente.**

---

## 4. Principios fundamentales de DRY

### 4.1. Una única fuente de verdad

Cuando una regla debe ser consistente en todo el sistema, debería existir una fuente autoritativa para esa regla.

Por ejemplo, si el tiempo máximo de espera de una operación es una política compartida, no conviene definir valores independientes en cada componente sin una razón clara.

```java
public final class ApplicationLimits {

    public static final Duration REQUEST_TIMEOUT =
            Duration.ofSeconds(10);

    private ApplicationLimits() {
    }
}
```

Los componentes que deban utilizar esa misma política pueden depender de una configuración común.

En aplicaciones reales, los límites pueden necesitar configuración externa o políticas específicas por operación. Una constante global no es automáticamente la mejor solución.

### 4.2. Centralizar las reglas de negocio compartidas

Si varias operaciones deben aplicar exactamente la misma regla de dominio, esa regla debería residir en un componente adecuado.

Por ejemplo:

```java
public class ShippingCostCalculator {

    public BigDecimal calculate(
            BigDecimal weight,
            BigDecimal distance
    ) {
        // Aplicar la política de cálculo de envío.
        return weight.multiply(distance);
    }
}
```

Los consumidores pueden utilizar el mismo componente en lugar de implementar la fórmula por separado.

La fórmula es ilustrativa: un cálculo real de envío dependería de tarifas, unidades, zonas y otras reglas del negocio.

### 4.3. Separar conocimiento de implementación

No toda repetición de instrucciones implica una regla común.

Por ejemplo, dos métodos pueden utilizar un bucle parecido para procesar datos diferentes. Si sus responsabilidades y evolución son distintas, mantenerlos separados puede ser más claro.

La abstracción debería representar un concepto significativo, no simplemente una coincidencia en la forma del código.

### 4.4. Reducir el número de lugares que deben cambiar

Una buena aplicación de DRY permite que un cambio de política se realice en el componente que es responsable de esa política.

No elimina todos los cambios coordinados: si una API modifica su contrato, puede ser necesario actualizar sus consumidores. El objetivo es evitar tener que modificar manualmente múltiples implementaciones independientes de la misma regla.

---

## 5. Ejemplo práctico en Java: centralización de una regla de negocio

Supongamos que una aplicación calcula descuentos para pedidos.

### 5.1. Implementación con duplicación

```java
public class OnlineOrderService {

    public BigDecimal calculateDiscount(BigDecimal subtotal) {
        if (subtotal.compareTo(new BigDecimal("100.00")) >= 0) {
            return subtotal.multiply(new BigDecimal("0.10"));
        }

        return BigDecimal.ZERO;
    }
}
```

```java
public class StoreOrderService {

    public BigDecimal calculateDiscount(BigDecimal subtotal) {
        if (subtotal.compareTo(new BigDecimal("100.00")) >= 0) {
            return subtotal.multiply(new BigDecimal("0.10"));
        }

        return BigDecimal.ZERO;
    }
}
```

Ambas clases implementan la misma regla de descuento.

Si cambia el umbral o el porcentaje, hay que modificar y verificar ambas implementaciones.

### 5.2. Crear una abstracción compartida

```java
public interface DiscountPolicy {

    BigDecimal calculateDiscount(BigDecimal subtotal);
}
```

Implementación:

```java
public class ThresholdDiscountPolicy implements DiscountPolicy {

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
    public BigDecimal calculateDiscount(BigDecimal subtotal) {
        if (subtotal.compareTo(minimumSubtotal) >= 0) {
            return subtotal.multiply(discountRate);
        }

        return BigDecimal.ZERO;
    }
}
```

Uso desde los servicios:

```java
public class OnlineOrderService {

    private final DiscountPolicy discountPolicy;

    public OnlineOrderService(DiscountPolicy discountPolicy) {
        this.discountPolicy = discountPolicy;
    }

    public BigDecimal calculateDiscount(BigDecimal subtotal) {
        return discountPolicy.calculateDiscount(subtotal);
    }
}
```

```java
public class StoreOrderService {

    private final DiscountPolicy discountPolicy;

    public StoreOrderService(DiscountPolicy discountPolicy) {
        this.discountPolicy = discountPolicy;
    }

    public BigDecimal calculateDiscount(BigDecimal subtotal) {
        return discountPolicy.calculateDiscount(subtotal);
    }
}
```

La configuración podría crear una única instancia de la política y compartirla entre ambos servicios:

```java
DiscountPolicy policy = new ThresholdDiscountPolicy(
        new BigDecimal("100.00"),
        new BigDecimal("0.10")
);

OnlineOrderService onlineService =
        new OnlineOrderService(policy);

StoreOrderService storeService =
        new StoreOrderService(policy);
```

### Ventajas obtenidas

- La regla se implementa una sola vez.
- Los servicios no necesitan conocer los detalles del cálculo.
- La política puede cambiar sin duplicar modificaciones.
- Es más sencillo probar el comportamiento compartido.
- Se puede introducir otra política si el negocio lo requiere.

### Precaución

No conviene introducir `DiscountPolicy` solo porque dos métodos sean similares. Esta abstracción está justificada porque representa una regla de negocio identificable y compartida.

Además, si el canal online y la tienda física tienen políticas distintas, deberían existir implementaciones diferentes o configuraciones explícitas, en lugar de forzar una política común incorrecta.

---

## 6. DRY aplicado a constantes y configuración

Los valores repetidos también pueden representar conocimiento duplicado.

### 6.1. Ejemplo con números mágicos

```java
public class PasswordValidator {

    public boolean isValid(String password) {
        return password != null && password.length() >= 12;
    }
}
```

```java
public class RegistrationService {

    public boolean canRegister(String password) {
        return password != null && password.length() >= 12;
    }
}
```

Si ambas clases representan exactamente la misma política de longitud mínima, podría centralizarse.

```java
public final class PasswordPolicy {

    public static final int MIN_LENGTH = 12;

    private PasswordPolicy() {
    }
}
```

Sin embargo, es preferible que las reglas de contraseña pertenezcan a una política coherente, especialmente si también incluyen complejidad, listas de contraseñas comprometidas, restricciones de cuenta o configuración de seguridad.

### 6.2. Configuración centralizada

En lugar de repartir parámetros de configuración por el código:

```java
Duration timeout = Duration.ofSeconds(10);
```

```java
Duration timeout = Duration.ofSeconds(10);
```

puede utilizarse una configuración compartida cuando ambas operaciones deban seguir la misma política.

En sistemas modernos, los valores operativos suelen gestionarse mediante archivos de configuración, variables de entorno o mecanismos específicos del framework.

### 6.3. No centralizar valores que deben ser diferentes

Una tasa fiscal, un límite de peticiones o un tiempo de espera no tiene por qué ser idéntico para todo el sistema.

Centraliza el conocimiento que realmente debe ser común. Mantén explícitas las diferencias que representan requisitos legítimos.

---

## 7. DRY aplicado a validaciones

Las validaciones suelen ser una fuente frecuente de duplicación.

### 7.1. Validación repetida

```java
if (email == null || email.isBlank()) {
    throw new IllegalArgumentException("Email obligatorio");
}
```

Esta comprobación podría aparecer en servicios de registro, actualización y creación de clientes.

Si todos deben aplicar exactamente la misma regla, conviene evitar implementaciones independientes.

### 7.2. Centralización en un objeto de valor

```java
public record EmailAddress(String value) {

    public EmailAddress {
        if (value == null || value.isBlank()) {
            throw new IllegalArgumentException(
                    "El email es obligatorio"
            );
        }

        value = value.trim();
    }
}
```

Los componentes que necesitan una dirección de correo pueden utilizar `EmailAddress` en lugar de validar repetidamente una cadena.

Este ejemplo solo valida que el valor no esté vacío. No comprueba completamente la validez de una dirección de correo; una validación real depende de los requisitos del sistema.

### Beneficios

- La regla queda asociada al concepto que valida.
- Los consumidores reciben un objeto que cumple el contrato definido.
- Se reduce la duplicación de comprobaciones.
- El tipo expresa mejor la intención que una cadena genérica.

### Precaución

No todas las validaciones deben centralizarse en un único método universal. Las reglas pueden pertenecer a distintos límites:

- Validación de formato en la entrada.
- Validación de invariantes en el dominio.
- Validación de permisos en la autorización.
- Validación de integridad en la persistencia.

Estas comprobaciones pueden parecer similares, pero tienen propósitos diferentes.

---

## 8. DRY aplicado a consultas y acceso a datos

La duplicación de consultas puede generar diferencias de comportamiento entre componentes.

### Ejemplo

```java
public List<User> findActiveUsers() {
    return jdbcTemplate.query(
            "SELECT * FROM users WHERE active = true",
            userRowMapper
    );
}
```

Si varios componentes implementan por separado la misma consulta, pueden acabar usando filtros diferentes.

Una solución es encapsular la operación en un repositorio:

```java
public interface UserRepository {

    List<User> findActiveUsers();
}
```

Los servicios utilizan el contrato:

```java
public class UserService {

    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public List<User> getActiveUsers() {
        return userRepository.findActiveUsers();
    }
}
```

La implementación concreta se ocupa de la consulta y de mapear los resultados.

### Precaución

No todas las consultas deben fusionarse en un único método genérico. Una consulta para generar un informe puede tener requisitos de rendimiento, proyección de datos o consistencia diferentes de una consulta para mostrar una pantalla.

La centralización debe respetar las necesidades de cada operación.

---

## 9. DRY aplicado a la lógica de presentación

La duplicación no aparece únicamente en el dominio o en el acceso a datos. También puede producirse en la interfaz.

Por ejemplo, diferentes pantallas pueden implementar independientemente la misma política de formato para fechas, cantidades o mensajes.

Una solución consiste en utilizar componentes o servicios de presentación compartidos.

### Ejemplo de formateo

```java
public class CurrencyFormatter {

    private final NumberFormat formatter;

    public CurrencyFormatter(Locale locale) {
        this.formatter = NumberFormat.getCurrencyInstance(locale);
    }

    public String format(BigDecimal amount) {
        return formatter.format(amount);
    }
}
```

Las vistas que deban mostrar importes según la misma política pueden utilizar el mismo formateador.

En una aplicación concurrente, conviene recordar que muchas implementaciones de `NumberFormat` no son seguras para uso simultáneo desde varios hilos. La estrategia de creación y uso debe adaptarse al contexto.

### Qué evitar

No es recomendable crear un módulo universal de utilidades que concentre cualquier función repetida sin relación conceptual.

Un componente compartido debe tener una responsabilidad coherente, no convertirse en un contenedor genérico de métodos.

---

## 10. DRY aplicado a APIs y contratos

La duplicación de conocimiento también aparece cuando una misma estructura o contrato se define independientemente en varios lugares.

Ejemplos:

- Definiciones de esquemas mantenidas manualmente en varios servicios.
- Modelos de mensajes duplicados entre productores y consumidores.
- Reglas de compatibilidad repetidas en clientes diferentes.
- Contratos de una API documentados de manera distinta en varias fuentes.

Una solución puede ser utilizar un contrato compartido, un esquema formal o generación de código cuando resulte apropiado.

Por ejemplo, una especificación OpenAPI puede actuar como fuente autoritativa para documentar una API y generar clientes.

### Precaución en sistemas distribuidos

Compartir código entre microservicios puede introducir acoplamiento entre equipos y despliegues.

En ocasiones es preferible compartir un contrato estable y permitir que cada servicio mantenga su propia implementación.

DRY no exige que todos los servicios utilicen una misma biblioteca para cada concepto parecido. Los límites de autonomía y la evolución independiente también son importantes.

---

## 11. DRY y las pruebas automatizadas

Las pruebas también pueden sufrir duplicación.

Si varios tests reproducen la misma configuración compleja, los cambios pueden requerir modificaciones repetitivas.

### Ejemplo

```java
User user = new User(
        1L,
        "ana@example.com",
        true
);
```

Si esta preparación aparece en muchos tests, puede resultar útil crear un método de apoyo:

```java
private User createActiveUser() {
    return new User(
            1L,
            "ana@example.com",
            true
    );
}
```

No obstante, conviene mantener cada prueba comprensible.

Un método auxiliar demasiado genérico que oculte los datos relevantes puede dificultar saber qué escenario está verificando el test.

### Buenas prácticas

- Reutilizar preparación común cuando realmente se repita el mismo escenario.
- Mantener visibles los datos importantes de cada prueba.
- Evitar constructores de fixtures excesivamente complejos.
- No crear una jerarquía de clases de prueba innecesaria.
- Compartir helpers que expresen conceptos claros.

**Una prueba legible es más importante que eliminar toda repetición de sus líneas.**

---

## 12. DRY y la automatización de tareas

El principio también se aplica fuera del código de negocio.

Por ejemplo, una organización puede repetir manualmente los mismos pasos para compilar, ejecutar pruebas, analizar el código y empaquetar una aplicación.

Esos pasos pueden centralizarse en un sistema de construcción o en una canalización de integración continua.

### Ejemplo conceptual

```text
Pipeline CI
├── Compilar
├── Ejecutar pruebas
├── Analizar código
├── Construir artefacto
└── Publicar resultado
```

En lugar de mantener scripts independientes con secuencias diferentes, se pueden reutilizar tareas y configuraciones comunes.

Sin embargo, las etapas que tienen requisitos diferentes deben mantenerse diferenciadas. No conviene construir una abstracción excesivamente genérica que haga difícil entender qué se ejecuta en cada entorno.

---

## 13. Cuándo conviene aplicar DRY

DRY suele aportar valor cuando se cumplen una o varias de estas condiciones:

- Existe una regla de negocio idéntica en varios componentes.
- Un cambio debería aplicarse de forma consistente en varios lugares.
- Hay una fórmula o política cuyo conocimiento debe tener un responsable claro.
- La duplicación está provocando errores o inconsistencias.
- Varios componentes deben respetar un mismo contrato.
- La repetición hace más costosa la revisión y las pruebas.
- Se ha identificado una abstracción con significado propio.

### Ejemplos típicos

| Situación | Aplicación de DRY |
|---|---|
| Misma regla de descuento en varios servicios | Centralizar la política de descuentos. |
| Mismo formato de identificadores | Crear un tipo o componente común si representa una regla compartida. |
| Misma consulta reutilizada | Encapsular la operación en el repositorio adecuado. |
| Mismo contrato de API | Mantener una especificación autoritativa cuando sea viable. |
| Mismo proceso de compilación | Reutilizar tareas de construcción y automatización. |
| Misma política de autorización | Centralizar la decisión en el límite responsable de autorizar. |
| Misma preparación repetida en pruebas | Crear helpers expresivos si mejoran la legibilidad. |

---

## 14. Cuándo NO conviene aplicar DRY

Este es uno de los puntos más importantes del principio.

La aplicación excesiva de DRY puede provocar abstracciones prematuras, dependencias innecesarias y componentes difíciles de modificar.

### 14.1. Código parecido con significados diferentes

Dos métodos pueden tener la misma estructura, pero responder a reglas distintas.

Por ejemplo, la validación de un límite de crédito y la validación de un límite de almacenamiento podrían comparar números de manera idéntica.

Eso no significa que deban compartir una abstracción: representan conceptos diferentes y podrían evolucionar de forma independiente.

### 14.2. Abstracciones prematuras

No conviene crear una infraestructura genérica solo porque dos fragmentos de código coinciden actualmente.

A veces es mejor mantener dos implementaciones sencillas hasta comprender si existe un concepto compartido estable.

### 14.3. Acoplamiento excesivo

Si varios módulos dependen de una biblioteca común que cambia con frecuencia, la centralización puede hacer que todos evolucionen al mismo tiempo.

En ese caso, puede ser preferible mantener implementaciones locales independientes.

### 14.4. Funciones genéricas difíciles de entender

Un método que recibe numerosos parámetros, banderas y callbacks para cubrir todos los casos posibles puede ser más complejo que las implementaciones originales.

### 14.5. Duplicación útil en límites arquitectónicos

En determinados sistemas, duplicar una pequeña cantidad de lógica puede reducir el acoplamiento entre módulos o servicios.

Por ejemplo, un servicio puede mantener una validación local sencilla para proteger su frontera, aunque exista una validación similar en otro servicio.

La decisión depende de si ambas comprobaciones representan una única política o si protegen contratos y responsabilidades diferentes.

### Regla práctica

Antes de eliminar una duplicación, pregúntate:

1. ¿Los fragmentos representan realmente el mismo conocimiento?
2. ¿Cambiarán normalmente por el mismo motivo?
3. ¿La abstracción será más fácil de comprender que el código actual?
4. ¿Reducirá el mantenimiento o añadirá acoplamiento?
5. ¿Se mantendrán claros los límites de cada módulo?

---

## 15. Relación entre DRY, KISS y YAGNI

DRY, KISS y YAGNI se complementan, pero no significan lo mismo.

| Principio | Objetivo | Riesgo de aplicarlo mal |
|---|---|---|
| DRY | Evitar duplicar conocimiento | Crear abstracciones excesivas para cualquier coincidencia. |
| KISS | Mantener las soluciones sencillas | Simplificar en exceso un problema que sí requiere estructura. |
| YAGNI | No construir funcionalidades innecesarias | Ignorar requisitos futuros que ya son conocidos y relevantes. |

### Ejemplo combinado

Supongamos que dos servicios aplican la misma regla de cálculo de una comisión.

- DRY sugiere evitar implementaciones independientes de la misma regla.
- KISS sugiere elegir una abstracción sencilla.
- YAGNI sugiere no construir un motor de reglas configurable si solo existe una política y no hay necesidad de variación.

La solución puede ser una clase pequeña que represente la política, sin crear un framework genérico.

---

## 16. Relación entre DRY y SOLID

DRY puede ayudar a aplicar SOLID, pero no lo sustituye.

### 16.1. Single Responsibility Principle (SRP)

Al centralizar una regla de negocio en un componente, puede quedar más clara su responsabilidad.

Sin embargo, no se debe concentrar todo el código repetido en una única clase de utilidades.

### 16.2. Open/Closed Principle (OCP)

Si una política compartida está representada mediante una abstracción adecuada, pueden añadirse implementaciones alternativas sin duplicar la lógica de coordinación.

### 16.3. Liskov Substitution Principle (LSP)

Las implementaciones que sustituyen una abstracción compartida deben respetar su contrato.

Una interfaz común no es útil si las implementaciones tienen comportamientos incompatibles.

### 16.4. Interface Segregation Principle (ISP)

Una abstracción creada para evitar duplicación debe ser específica y coherente. No conviene obligar a todos los consumidores a depender de operaciones que no necesitan.

### 16.5. Dependency Inversion Principle (DIP)

Una regla compartida puede exponerse mediante una interfaz para que los componentes de alto nivel dependan de un contrato y no de una implementación concreta.

**Conclusión:** DRY reduce duplicación de conocimiento; SOLID ayuda a organizar responsabilidades, contratos y dependencias. Aplicarlos conjuntamente puede mejorar la mantenibilidad.

---

## 17. DRY y los patrones de diseño

Los patrones de diseño pueden facilitar la aplicación de DRY, pero no son necesarios en todos los casos.

| Patrón | Relación con DRY |
|---|---|
| Strategy | Centraliza la forma de utilizar una política y permite intercambiar algoritmos. |
| Factory | Puede concentrar decisiones de creación de objetos repetidas. |
| Builder | Encapsula la construcción de objetos complejos. |
| Template Method | Comparte una estructura de algoritmo cuando sus pasos comunes son estables. |
| Facade | Proporciona un punto de entrada común para operaciones de un subsistema. |
| Decorator | Permite componer comportamientos sin copiar su implementación. |
| Adapter | Centraliza la adaptación de una interfaz externa a un contrato interno. |
| Command | Representa una operación como un objeto reutilizable o ejecutable. |

### Ejemplo con Strategy

Si existen varias políticas de descuento, puede utilizarse una interfaz común:

```java
public interface DiscountPolicy {
    BigDecimal calculate(BigDecimal subtotal);
}
```

Las implementaciones representan reglas diferentes:

```java
public class NoDiscountPolicy implements DiscountPolicy {

    @Override
    public BigDecimal calculate(BigDecimal subtotal) {
        return BigDecimal.ZERO;
    }
}
```

```java
public class PercentageDiscountPolicy
        implements DiscountPolicy {

    private final BigDecimal percentage;

    public PercentageDiscountPolicy(BigDecimal percentage) {
        this.percentage = percentage;
    }

    @Override
    public BigDecimal calculate(BigDecimal subtotal) {
        return subtotal.multiply(percentage);
    }
}
```

El código que utiliza la política puede permanecer estable mientras se incorporan nuevas estrategias.

No obstante, Strategy no debe introducirse únicamente para eliminar dos líneas duplicadas. Está especialmente justificado cuando hay algoritmos alternativos que representan comportamientos distintos y deben poder intercambiarse.

---

## 18. DRY en arquitecturas de software

El principio se aplica a diferentes niveles, desde métodos hasta sistemas distribuidos.

### 18.1. Arquitectura monolítica

Dentro de un monolito, DRY puede ayudar a centralizar reglas de dominio compartidas entre módulos.

Conviene mantener límites modulares para evitar que la reutilización cree dependencias circulares o un módulo común que conozca demasiado sobre toda la aplicación.

### 18.2. Arquitectura hexagonal

Una regla de dominio puede residir en el dominio o en un caso de uso, mientras que los adaptadores reutilizan los contratos adecuados.

No conviene trasladar las reglas del dominio a un adaptador de infraestructura solo porque allí se utilicen desde varios lugares.

### 18.3. Clean Architecture

DRY ayuda a reducir implementaciones duplicadas de reglas de negocio y contratos.

Sin embargo, compartir código no debe invertir la dirección de dependencias ni obligar al dominio a depender de la infraestructura.

### 18.4. Microservicios

En microservicios, DRY requiere especial cuidado.

Compartir una biblioteca con lógica de negocio entre numerosos servicios puede provocar:

- Dependencias de versiones.
- Despliegues coordinados.
- Acoplamiento entre equipos.
- Dificultad para evolucionar los modelos de dominio independientemente.

En ocasiones, es mejor compartir un contrato estable que una implementación completa.

### 18.5. MVC, MVP y MVVM

Puede centralizarse la lógica de presentación que realmente es común, por ejemplo, un formato de fecha compartido o una política de validación visual.

No conviene trasladar todas las operaciones de todas las vistas a un controlador o Presenter universal solo para evitar duplicación.

### Principio arquitectónico

**La reutilización debe respetar los límites de responsabilidad y autonomía del sistema.**

---

## 19. Cómo identificar duplicación de conocimiento

La duplicación no siempre es evidente. Puede aparecer con nombres diferentes o en capas distintas.

Una revisión puede seguir estos pasos:

### Paso 1. Identificar la regla

Busca fórmulas, validaciones, decisiones, valores y contratos que se repitan.

### Paso 2. Comprender su significado

Determina si los fragmentos representan el mismo concepto de dominio o si solo tienen una estructura similar.

### Paso 3. Identificar quién es responsable

Busca el componente que debería ser responsable de esa regla: una entidad, una política, un servicio de aplicación, un repositorio o una configuración.

### Paso 4. Evaluar la evolución

Pregúntate si los fragmentos deberían cambiar siempre juntos.

Si sus motivos de cambio son independientes, compartir la implementación podría no ser apropiado.

### Paso 5. Diseñar la abstracción mínima

Si existe una regla común, elige una solución sencilla:

- Un método.
- Una constante con significado.
- Una clase de dominio.
- Una política.
- Un servicio.
- Un contrato.
- Una configuración compartida.

No todos los casos necesitan una interfaz o una jerarquía de clases.

### Paso 6. Refactorizar y probar

Introduce el cambio en pasos pequeños y comprueba que el comportamiento se mantiene.

### Paso 7. Eliminar implementaciones obsoletas

Una vez que los consumidores utilizan la regla compartida, elimina las copias antiguas y comprueba que no queden caminos alternativos que mantengan la duplicación.

---

## 20. Errores comunes al aplicar DRY

### Error 1. Eliminar cualquier repetición textual

Dos bloques similares no siempre representan la misma regla.

**Mejora:** identifica primero el significado y los motivos de cambio.

### Error 2. Crear una clase `Utils` para todo

Una clase de utilidades que acumula operaciones no relacionadas puede convertirse en una fuente de acoplamiento y desorden.

**Mejora:** agrupa las operaciones por responsabilidad y significado.

### Error 3. Crear una abstracción demasiado genérica

Un componente diseñado para cubrir todas las variaciones imaginables puede ser más complejo que el código original.

**Mejora:** crea la abstracción mínima que resuelva una necesidad real.

### Error 4. Compartir código entre módulos que deberían ser independientes

La reutilización puede introducir dependencias difíciles de gestionar.

**Mejora:** revisa los límites arquitectónicos antes de extraer una biblioteca compartida.

### Error 5. Centralizar reglas que realmente son distintas

Dos políticas pueden utilizar actualmente el mismo valor y tener requisitos diferentes.

**Mejora:** representa las diferencias de negocio de manera explícita.

### Error 6. Ocultar el comportamiento tras demasiadas capas

Una llamada sencilla puede terminar atravesando numerosos componentes genéricos.

**Mejora:** mantén el flujo de ejecución comprensible y evita la indirección innecesaria.

### Error 7. Crear un método compartido con demasiados parámetros

Intentar cubrir múltiples casos en una sola función puede generar un contrato difícil de usar.

**Mejora:** separa políticas distintas o utiliza objetos de configuración cuando representen un concepto coherente.

### Error 8. Olvidar la documentación y las pruebas

Centralizar una regla sin definir sus invariantes puede trasladar el problema a un único lugar.

**Mejora:** prueba los casos normales, límites, errores y variaciones importantes.

---

## 21. Pruebas automatizadas de una regla compartida

Cuando una regla se centraliza, conviene probar su comportamiento directamente.

Por ejemplo, para la política de descuento anterior:

```java
import static org.junit.jupiter.api.Assertions.assertEquals;

import java.math.BigDecimal;
import org.junit.jupiter.api.Test;

class ThresholdDiscountPolicyTest {

    private final ThresholdDiscountPolicy policy =
            new ThresholdDiscountPolicy(
                    new BigDecimal("100.00"),
                    new BigDecimal("0.10")
            );

    @Test
    void shouldApplyDiscountAtMinimumSubtotal() {
        BigDecimal discount = policy.calculateDiscount(
                new BigDecimal("100.00")
        );

        assertEquals(
                0,
                new BigDecimal("10.0000").compareTo(discount)
        );
    }

    @Test
    void shouldNotApplyDiscountBelowMinimumSubtotal() {
        BigDecimal discount = policy.calculateDiscount(
                new BigDecimal("99.99")
        );

        assertEquals(
                0,
                BigDecimal.ZERO.compareTo(discount)
        );
    }
}
```

El ejemplo presupone JUnit 5 y la implementación de `ThresholdDiscountPolicy` presentada anteriormente.

### ¿Qué se consigue?

- La regla compartida tiene pruebas específicas.
- Los límites de la política quedan documentados mediante ejemplos.
- Los cambios futuros pueden verificarse sin repetir toda la configuración de cada consumidor.
- Los servicios que utilizan la política pueden probar su coordinación por separado.

Centralizar una regla no elimina la necesidad de pruebas de integración. También es necesario comprobar que los consumidores la utilizan correctamente.

---

## 22. Refactorización paso a paso para aplicar DRY

Supongamos que una aplicación tiene tres métodos con el mismo cálculo.

### Paso 1. Detectar la duplicación

```java
return subtotal.multiply(new BigDecimal("0.10"));
```

La misma regla aparece en varios servicios.

### Paso 2. Comprobar el significado

Verifica que todos calculan realmente el mismo descuento y que no existen diferencias por canal, cliente o producto.

### Paso 3. Elegir la ubicación

Si el descuento es una regla de dominio, sitúalo en una política o componente de dominio adecuado, no necesariamente en una clase genérica de utilidades.

### Paso 4. Extraer la regla

```java
public class TenPercentDiscountPolicy {

    public BigDecimal calculate(BigDecimal subtotal) {
        return subtotal.multiply(new BigDecimal("0.10"));
    }
}
```

La clase es deliberadamente sencilla. Si la tasa debe configurarse o existen varias políticas, podría utilizarse una abstracción como `DiscountPolicy`.

### Paso 5. Actualizar los consumidores

Los servicios delegan el cálculo en la política compartida.

### Paso 6. Ejecutar las pruebas

Comprueba los resultados de la política y los flujos de negocio que dependen de ella.

### Paso 7. Eliminar las copias

Una vez comprobado el comportamiento, elimina las implementaciones duplicadas.

### Paso 8. Revisar el diseño

Asegúrate de que la extracción ha reducido el mantenimiento sin crear dependencias innecesarias.

---

## 23. Checklist de DRY para revisar una clase

### Reglas de negocio

- [ ] ¿Existen reglas de negocio duplicadas?
- [ ] ¿Los mismos cálculos se implementan de manera independiente?
- [ ] ¿Hay validaciones compartidas que deberían tener una única definición?
- [ ] ¿Los valores de configuración representan una política común o políticas distintas?

### Diseño

- [ ] ¿La abstracción propuesta tiene un significado claro?
- [ ] ¿La clase compartida tiene una responsabilidad coherente?
- [ ] ¿La reutilización respeta los límites de los módulos?
- [ ] ¿Se evitan dependencias innecesarias?
- [ ] ¿La solución sigue siendo fácil de comprender?

### Mantenimiento

- [ ] ¿Un cambio de la regla puede realizarse en su lugar responsable?
- [ ] ¿Se han eliminado las implementaciones antiguas?
- [ ] ¿Se evitan fuentes de verdad contradictorias?
- [ ] ¿Se han considerado las variaciones legítimas del negocio?

### Pruebas

- [ ] ¿La regla compartida tiene pruebas específicas?
- [ ] ¿Se comprueban sus límites y errores?
- [ ] ¿Se verifica que los consumidores la utilizan correctamente?
- [ ] ¿Las pruebas siguen siendo legibles?

---

## 24. Checklist de DRY para una Pull Request

Antes de aprobar un cambio, puedes comprobar:

- [ ] ¿Se ha introducido una nueva implementación de una regla que ya existe?
- [ ] ¿Se han repetido valores importantes sin una razón clara?
- [ ] ¿Se ha copiado una consulta o una validación existente?
- [ ] ¿La nueva abstracción representa un concepto real?
- [ ] ¿Se están unificando fragmentos que deberían evolucionar de manera independiente?
- [ ] ¿La reutilización introduce acoplamiento entre módulos?
- [ ] ¿Se han añadido dependencias innecesarias?
- [ ] ¿Se mantienen claros los contratos públicos?
- [ ] ¿Existen pruebas para el comportamiento centralizado?
- [ ] ¿El cambio reduce realmente el coste de mantenimiento?

Una buena revisión debe priorizar la coherencia conceptual y el mantenimiento. No debería rechazar automáticamente un cambio porque contenga unas pocas líneas similares a otras del proyecto.

---

## 25. Plan de adopción de DRY en un proyecto existente

No es recomendable intentar eliminar toda duplicación de una aplicación en una única refactorización masiva.

Una estrategia progresiva reduce el riesgo.

### Fase 1. Identificar duplicaciones importantes

Busca reglas de negocio, cálculos, validaciones y configuraciones repetidas que puedan producir errores o inconsistencias.

### Fase 2. Priorizar por impacto

Da prioridad a las duplicaciones que:

- Afectan a reglas críticas.
- Cambian con frecuencia.
- Han producido errores.
- Aparecen en numerosos componentes.
- Deben ser consistentes por razones de negocio o seguridad.

### Fase 3. Crear pruebas de caracterización

Cuando sea necesario, documenta mediante pruebas el comportamiento actual antes de modificarlo.

Esto ayuda a detectar cambios accidentales durante la refactorización.

### Fase 4. Extraer reglas coherentes

Crea componentes pequeños con responsabilidades claras y contratos explícitos.

### Fase 5. Actualizar consumidores

Sustituye las implementaciones duplicadas por el componente compartido.

### Fase 6. Verificar los límites arquitectónicos

Comprueba que la reutilización no introduce dependencias circulares ni obliga a módulos independientes a compartir detalles innecesarios.

### Fase 7. Automatizar las comprobaciones

Utiliza pruebas, análisis estático y revisiones de código para evitar que reaparezcan duplicaciones problemáticas.

El objetivo no es conseguir un código sin ninguna repetición, sino reducir las duplicaciones que encarecen la evolución del sistema.

---

## 26. Conclusión

**DRY (Don't Repeat Yourself) es un principio de diseño que busca evitar la duplicación innecesaria de conocimiento en el software.**

Su principal beneficio es reducir el número de lugares que deben modificarse cuando una regla compartida cambia.

Los puntos más importantes son:

1. **Una única fuente de verdad:** mantén una definición autoritativa para las reglas que deben ser comunes.
2. **Centralización consciente:** agrupa el conocimiento compartido en componentes con responsabilidades coherentes.
3. **No confundir similitud con identidad:** dos fragmentos parecidos pueden representar reglas distintas.
4. **Abstracciones con propósito:** reutiliza código cuando la abstracción mejore la claridad y el mantenimiento.
5. **Respeto por los límites:** no introduzcas acoplamiento innecesario entre módulos o servicios.
6. **Pruebas automatizadas:** verifica el comportamiento de la regla compartida y su uso por los consumidores.
7. **Refactorización progresiva:** elimina duplicaciones de manera incremental y segura.
8. **Pragmatismo:** unas pocas líneas repetidas pueden ser preferibles a una abstracción compleja.

La idea más importante es que **DRY no pretende minimizar el número de líneas de código, sino minimizar la cantidad de conocimiento que debe mantenerse de manera independiente**.

Una aplicación bien diseñada no es necesariamente aquella en la que todo se reutiliza, sino aquella en la que cada regla tiene una ubicación coherente y los componentes pueden evolucionar sin crear inconsistencias ni dependencias innecesarias.

---

## 27. Referencias y lecturas recomendadas

### Libros

- Andrew Hunt y David Thomas — *The Pragmatic Programmer: Your Journey to Mastery*.
- Robert C. Martin — *Clean Code: A Handbook of Agile Software Craftsmanship*.
- Robert C. Martin — *Agile Software Development: Principles, Patterns, and Practices*.
- Martin Fowler — *Refactoring: Improving the Design of Existing Code*.
- Joshua Bloch — *Effective Java*.

### Recursos técnicos

- Martin Fowler — Refactoring: https://refactoring.com/
- Documentación oficial de Java: https://docs.oracle.com/en/java/
- JUnit 5: https://junit.org/junit5/
- OWASP Cheat Sheet Series: https://cheatsheetseries.owasp.org/

Estas referencias permiten profundizar en la refactorización, la cohesión, el acoplamiento, el diseño orientado a objetos y las prácticas que ayudan a mantener un sistema coherente.