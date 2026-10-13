# Pruebas unitarias (Unit Testing)

## 1. Introducción

Las **pruebas unitarias** son pruebas automatizadas que verifican que una unidad pequeña y aislada de código funciona según lo esperado.

Una unidad puede ser:

- Un método.
- Una función.
- Una clase.
- Un componente con una responsabilidad concreta.

El objetivo es comprobar que, dadas unas condiciones de entrada, el código produce el resultado esperado o responde correctamente ante una situación determinada.

Por ejemplo, si tenemos una clase que calcula descuentos, podemos crear pruebas que verifiquen que:

- Un cliente obtiene el descuento correcto.
- Un importe negativo es rechazado.
- Un importe igual a cero se gestiona correctamente.
- Los límites de los descuentos se respetan.

Las pruebas unitarias forman parte de una estrategia de calidad del software y permiten detectar errores antes de que lleguen a producción.

### Objetivos principales

1. Verificar el comportamiento del código.
2. Detectar errores durante el desarrollo.
3. Evitar regresiones cuando se modifica una funcionalidad.
4. Facilitar la refactorización.
5. Documentar el comportamiento esperado.
6. Mejorar el diseño y la mantenibilidad del software.
7. Proporcionar confianza durante la evolución de una aplicación.

---

## 2. ¿Qué problema resuelven?

A medida que una aplicación crece, las modificaciones pueden introducir errores en funcionalidades que anteriormente funcionaban correctamente.

Sin pruebas automatizadas, el desarrollador debe comprobar manualmente cada comportamiento afectado. Esto consume tiempo y hace más probable que ciertos casos pasen inadvertidos.

Las pruebas unitarias permiten automatizar esas comprobaciones.

### Sin pruebas unitarias

1. Se desarrolla una funcionalidad.
2. Se comprueba manualmente que funciona.
3. Se modifica el código semanas después.
4. Un cambio rompe una funcionalidad anterior.
5. El error puede llegar a producción si no se detecta a tiempo.

### Con pruebas unitarias

1. Se define el comportamiento esperado.
2. Se implementa el código.
3. Se automatizan las comprobaciones.
4. Se modifica el código.
5. Se ejecutan de nuevo las pruebas.
6. Se detectan posibles regresiones inmediatamente.

El objetivo no es eliminar todos los errores, sino reducir la probabilidad de introducirlos y acelerar su detección.

---

## 3. ¿Qué es una unidad de código?

Una unidad es la parte de la aplicación cuyo comportamiento queremos comprobar de manera independiente.

Por ejemplo, imaginemos una aplicación de comercio electrónico con las siguientes clases:

- `PriceCalculator`: calcula precios.
- `DiscountService`: aplica descuentos.
- `OrderService`: gestiona pedidos.
- `PaymentService`: procesa pagos.
- `EmailService`: envía correos electrónicos.

Podríamos probar individualmente el cálculo de precios, la aplicación de descuentos o las reglas de validación de pedidos.

Una prueba unitaria de `DiscountService` no debería necesitar conectarse a una base de datos real, realizar un pago o enviar un correo electrónico.

Las dependencias externas se pueden sustituir por objetos controlados, como mocks o stubs, cuando sea necesario.

**Importante:** una prueba que verifica una clase puede utilizar otras clases reales si son sencillas y deterministas. Lo esencial es mantener la prueba rápida, controlada y centrada en el comportamiento que se quiere comprobar.

---

## 4. Anatomía de una prueba unitaria

Una prueba unitaria suele organizarse siguiendo el patrón **Arrange – Act – Assert (AAA)**.

### Arrange: preparar

Se crean los objetos y las condiciones necesarias para ejecutar la prueba.

### Act: ejecutar

Se invoca el método o comportamiento que se quiere comprobar.

### Assert: verificar

Se comprueba que el resultado coincide con lo esperado.

Ejemplo en Java:

```java
import static org.junit.jupiter.api.Assertions.assertEquals;

import org.junit.jupiter.api.Test;

class CalculatorTest {

    @Test
    void shouldSumTwoNumbers() {
        // Arrange
        Calculator calculator = new Calculator();

        // Act
        int result = calculator.sum(2, 3);

        // Assert
        assertEquals(5, result);
    }
}
```

La clase que se está probando:

```java
class Calculator {

    int sum(int a, int b) {
        return a + b;
    }
}
```

La prueba comprueba que sumar `2` y `3` produce `5`.

El patrón AAA mejora la legibilidad porque separa claramente la preparación, la ejecución y la verificación.

No es obligatorio escribir comentarios `Arrange`, `Act` y `Assert` en todas las pruebas, especialmente si la estructura ya es evidente.

---

## 5. Principios de una buena prueba unitaria

Una buena prueba no se limita a ejecutarse correctamente. También debe ser comprensible, fiable y fácil de mantener.

### 5.1. Independencia

Cada prueba debe poder ejecutarse sin depender del resultado de otra.

Las pruebas no deberían depender del orden de ejecución ni compartir un estado mutable que pueda provocar resultados inconsistentes.

### 5.2. Determinismo

Una prueba debe producir el mismo resultado cuando se ejecuta repetidamente bajo las mismas condiciones.

Se deben controlar dependencias como:

- La hora actual.
- La generación de números aleatorios.
- Los servicios externos.
- El estado compartido.
- Las variables de entorno.
- Los datos que cambian entre ejecuciones.

Cuando una regla depende de la fecha, por ejemplo, es preferible inyectar un `Clock` o una abstracción temporal en lugar de consultar directamente la hora del sistema.

### 5.3. Rapidez

Las pruebas unitarias deberían ser suficientemente rápidas como para ejecutarse con frecuencia, incluso después de cada pequeño cambio.

### 5.4. Legibilidad

El nombre de una prueba debe explicar qué comportamiento se está comprobando.

Ejemplos:

- `shouldCalculateDiscountForPremiumCustomer`
- `shouldRejectNegativeAmount`
- `shouldReturnZeroWhenCartIsEmpty`

### 5.5. Una razón clara para fallar

Cada prueba debe centrarse en un comportamiento concreto. Si falla, el desarrollador debería poder identificar rápidamente qué regla ha dejado de cumplirse.

Una prueba puede contener varias aserciones relacionadas cuando todas verifican el mismo comportamiento. No es necesario imponer una única aserción por prueba.

### 5.6. Repetibilidad

Las pruebas deben poder ejecutarse tantas veces como sea necesario sin dejar el entorno en un estado diferente o depender de ejecuciones anteriores.

---

## 6. Frameworks y herramientas

Existen distintas herramientas para escribir y ejecutar pruebas unitarias.

| Herramienta | Lenguaje o entorno | Finalidad |
|---|---|---|
| JUnit 5 | Java | Framework de pruebas |
| Mockito | Java | Creación y verificación de dobles de prueba |
| AssertJ | Java | Aserciones expresivas |
| pytest | Python | Framework de pruebas |
| unittest | Python | Framework incluido en la biblioteca estándar |
| Jest | JavaScript / TypeScript | Pruebas unitarias y de componentes |
| Vitest | JavaScript / TypeScript | Pruebas para proyectos modernos de frontend |
| NUnit | C# | Framework de pruebas para .NET |
| xUnit.net | .NET | Framework de pruebas |

En los ejemplos siguientes se utilizará **Java con JUnit 5 y Mockito**, una combinación habitual en aplicaciones Java empresariales.

---

## 7. Ejemplo práctico: calculadora de descuentos

Supongamos que necesitamos implementar una regla de negocio para calcular descuentos.

La regla es:

- Si el importe es inferior o igual a cero, se rechaza.
- Los clientes premium obtienen un 20 % de descuento.
- Los clientes estándar obtienen un 5 % de descuento.
- El resultado se expresa con dos decimales.

### 7.1. Implementación

```java
import java.math.BigDecimal;
import java.math.RoundingMode;

public class DiscountService {

    public BigDecimal calculateDiscount(
            BigDecimal amount,
            boolean premiumCustomer) {

        if (amount == null || amount.signum() <= 0) {
            throw new IllegalArgumentException(
                "Amount must be greater than zero"
            );
        }

        BigDecimal rate = premiumCustomer
                ? new BigDecimal("0.20")
                : new BigDecimal("0.05");

        return amount.multiply(rate)
                .setScale(2, RoundingMode.HALF_UP);
    }
}
```

Se utiliza `BigDecimal` para evitar los problemas de precisión binaria que pueden aparecer con `double` al realizar cálculos monetarios.

### 7.2. Pruebas unitarias

```java
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertThrows;

import java.math.BigDecimal;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

class DiscountServiceTest {

    private DiscountService discountService;

    @BeforeEach
    void setUp() {
        discountService = new DiscountService();
    }

    @Test
    void shouldApplyTwentyPercentForPremiumCustomer() {
        BigDecimal amount = new BigDecimal("100.00");

        BigDecimal result =
                discountService.calculateDiscount(amount, true);

        assertEquals(new BigDecimal("20.00"), result);
    }

    @Test
    void shouldApplyFivePercentForStandardCustomer() {
        BigDecimal amount = new BigDecimal("100.00");

        BigDecimal result =
                discountService.calculateDiscount(amount, false);

        assertEquals(new BigDecimal("5.00"), result);
    }

    @Test
    void shouldRejectZeroAmount() {
        assertThrows(
            IllegalArgumentException.class,
            () -> discountService.calculateDiscount(
                BigDecimal.ZERO, true
            )
        );
    }

    @Test
    void shouldRejectNegativeAmount() {
        assertThrows(
            IllegalArgumentException.class,
            () -> discountService.calculateDiscount(
                new BigDecimal("-10.00"), false
            )
        );
    }

    @Test
    void shouldRejectNullAmount() {
        assertThrows(
            IllegalArgumentException.class,
            () -> discountService.calculateDiscount(null, true)
        );
    }
}
```

### 7.3. ¿Qué estamos comprobando?

Las pruebas verifican:

- El comportamiento para clientes premium.
- El comportamiento para clientes estándar.
- El rechazo de importes nulos.
- El rechazo de importes negativos.
- El rechazo de importes iguales a cero.

La implementación contiene pocas líneas, pero las pruebas cubren varias reglas de negocio importantes.

**Una buena prueba se centra en el comportamiento, no en los detalles internos de implementación.** Si cambiamos la estructura interna del servicio sin modificar sus reglas, las pruebas deberían seguir pasando.

---

## 8. Pruebas de casos límite

Uno de los errores habituales consiste en probar únicamente el caso más sencillo.

Por ejemplo, comprobar que el 20 % de 100 es 20 no garantiza que todos los escenarios estén bien resueltos.

También conviene probar:

- Valores mínimos y máximos permitidos.
- Valores nulos cuando sean posibles.
- Cadenas vacías.
- Colecciones vacías.
- Valores negativos.
- Límites entre categorías.
- Resultados redondeados.
- Datos incorrectos.
- Excepciones esperadas.

En el caso del descuento, sería interesante probar importes como `0.01`, porque obligan a comprobar el comportamiento del redondeo.

### Tabla de casos de prueba

| Entrada | Cliente | Resultado esperado |
|---|---|---|
| `100.00` | Premium | `20.00` |
| `100.00` | Estándar | `5.00` |
| `0.01` | Premium | `0.00` con `HALF_UP` |
| `0.00` | Premium | Excepción |
| `-10.00` | Estándar | Excepción |
| `null` | Premium | Excepción |

Los casos límite deben derivarse de las reglas de negocio. No es necesario probar todas las combinaciones imaginables, sino aquellas que aporten confianza real.

---

## 9. Pruebas parametrizadas

Cuando varios escenarios comprueban la misma regla, podemos utilizar pruebas parametrizadas para reducir duplicaciones.

JUnit 5 permite utilizar `@ParameterizedTest` y `@CsvSource`.

Ejemplo:

```java
import static org.junit.jupiter.api.Assertions.assertEquals;

import java.math.BigDecimal;

import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;

class DiscountServiceParameterizedTest {

    private final DiscountService service = new DiscountService();

    @ParameterizedTest
    @CsvSource({
        "100.00, true, 20.00",
        "100.00, false, 5.00",
        "200.00, true, 40.00",
        "200.00, false, 10.00",
        "0.01, true, 0.00"
    })
    void shouldCalculateDiscount(
            String amount,
            boolean premium,
            String expected) {

        BigDecimal result = service.calculateDiscount(
                new BigDecimal(amount),
                premium
        );

        assertEquals(new BigDecimal(expected), result);
    }
}
```

Cada fila representa un escenario independiente.

Las pruebas parametrizadas resultan útiles cuando:

- Se valida una regla con diferentes entradas.
- Se prueban muchos valores límite.
- Se comprueba una función matemática.
- Se valida un conjunto de reglas de negocio similares.

No conviene convertir todos los escenarios en una única prueba parametrizada si cada uno representa un comportamiento conceptualmente distinto.

---

## 10. Dobles de prueba: Dummy, Stub, Fake, Mock y Spy

Cuando una clase depende de otros componentes, puede ser necesario sustituir esas dependencias por objetos controlados.

Estos objetos suelen denominarse *test doubles* o dobles de prueba.

### Dummy

Objeto que se proporciona porque una llamada necesita un argumento, aunque ese objeto no intervenga realmente en el escenario.

### Stub

Devuelve respuestas previamente configuradas.

### Fake

Implementación simplificada que funciona, pero no utiliza el mecanismo de producción.

Por ejemplo, un repositorio en memoria puede sustituir una base de datos en una prueba controlada.

### Mock

Objeto que permite configurar y verificar interacciones esperadas.

Por ejemplo, comprobar que se solicita el envío de un correo cuando se registra correctamente un pedido.

### Spy

Permite observar las llamadas a un objeto real o parcialmente sustituido. En Mockito, un spy envuelve normalmente un objeto real y puede conservar el comportamiento original de sus métodos.

No todos los dobles tienen que ser mocks. Para probar una regla de negocio, muchas veces basta con utilizar objetos reales sencillos.

### ¿Cuándo utilizar Mockito?

Mockito es útil cuando queremos aislar una unidad de dependencias como:

- Repositorios.
- Clientes HTTP.
- Servicios de terceros.
- Publicadores de eventos.
- Servicios de correo.
- Sistemas de pago.

No debería utilizarse para simular indiscriminadamente cada objeto del sistema. Un exceso de mocks puede producir pruebas acopladas a la implementación y poco representativas del comportamiento real.

---

## 11. Ejemplo con Mockito

Imaginemos un servicio que registra usuarios y consulta un repositorio.

### 11.1. Contrato del repositorio

```java
import java.util.Optional;

public interface UserRepository {

    Optional<User> findByEmail(String email);

    User save(User user);
}
```

### 11.2. Modelo

```java
public record User(String email) {
}
```

### 11.3. Servicio

```java
public class UserService {

    private final UserRepository repository;

    public UserService(UserRepository repository) {
        this.repository = repository;
    }

    public User register(String email) {
        if (email == null || email.isBlank()) {
            throw new IllegalArgumentException(
                "Email is required"
            );
        }

        if (repository.findByEmail(email).isPresent()) {
            throw new IllegalStateException(
                "User already exists"
            );
        }

        return repository.save(new User(email));
    }
}
```

### 11.4. Prueba con Mockito

```java
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertThrows;
import static org.mockito.Mockito.*;

import java.util.Optional;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    private UserRepository repository;

    private UserService userService;

    @BeforeEach
    void setUp() {
        userService = new UserService(repository);
    }

    @Test
    void shouldRegisterNewUser() {
        String email = "user@example.com";
        User expected = new User(email);

        when(repository.findByEmail(email))
                .thenReturn(Optional.empty());

        when(repository.save(any(User.class)))
                .thenReturn(expected);

        User result = userService.register(email);

        assertEquals(expected, result);

        verify(repository).findByEmail(email);
        verify(repository).save(any(User.class));
    }

    @Test
    void shouldRejectExistingUser() {
        String email = "user@example.com";

        when(repository.findByEmail(email))
                .thenReturn(Optional.of(new User(email)));

        assertThrows(
            IllegalStateException.class,
            () -> userService.register(email)
        );

        verify(repository, never()).save(any(User.class));
    }
}
```

### 11.5. ¿Qué demuestra esta prueba?

La base de datos no se utiliza. El mock del repositorio permite controlar el resultado de la búsqueda y verificar que el servicio se comporta correctamente.

La primera prueba comprueba que un usuario nuevo se registra.

La segunda comprueba que no se intenta guardar un usuario duplicado.

Este enfoque es apropiado cuando queremos verificar la lógica del servicio sin probar la implementación real del repositorio.

Sin embargo, estas pruebas **no demuestran que la consulta a la base de datos funcione correctamente**. Para ello hacen falta pruebas de integración.

---

## 12. Pruebas unitarias frente a pruebas de integración

Ambos tipos de pruebas son importantes, pero responden a preguntas diferentes.

| Característica | Prueba unitaria | Prueba de integración |
|---|---|---|
| Objetivo | Comprobar una unidad de comportamiento | Comprobar la colaboración entre componentes |
| Dependencias | Normalmente aisladas o controladas | Se utilizan dependencias reales o representativas |
| Base de datos | Habitualmente no | Puede utilizarse una base de datos de prueba |
| Velocidad | Generalmente muy rápida | Generalmente más lenta |
| Diagnóstico | Suele ser sencillo | Puede requerir investigar varias capas |
| Ejemplo | Validar una regla de descuentos | Verificar que el repositorio persiste correctamente |

Una prueba que ejecuta una clase de servicio y un repositorio real puede ser una prueba de integración, aunque la ejecución se limite a una parte pequeña de la aplicación.

La clasificación depende del objetivo y de las dependencias reales utilizadas, no solo del número de clases involucradas.

---

## 13. Pruebas unitarias y TDD

**Test-Driven Development (TDD)** es una metodología en la que las pruebas guían el desarrollo del código.

Su ciclo clásico es:

1. **Red:** escribir una prueba que falla porque la funcionalidad todavía no está implementada.
2. **Green:** escribir la implementación mínima para que la prueba pase.
3. **Refactor:** mejorar el diseño manteniendo las pruebas en verde.

Ejemplo:

Queremos implementar un método que valide un importe.

### Paso 1: Red

Escribimos una prueba que exige rechazar importes negativos.

```java
@Test
void shouldRejectNegativeAmount() {
    DiscountService service = new DiscountService();

    assertThrows(
        IllegalArgumentException.class,
        () -> service.calculateDiscount(
            new BigDecimal("-1.00"), true
        )
    );
}
```

### Paso 2: Green

Implementamos la validación necesaria para que la prueba pase.

### Paso 3: Refactor

Mejoramos el código, eliminamos duplicaciones y aclaramos nombres sin cambiar el comportamiento esperado.

TDD no consiste simplemente en escribir muchas pruebas después de terminar la implementación. Consiste en utilizar pruebas pequeñas para guiar incrementalmente el diseño y el desarrollo.

Las pruebas unitarias pueden escribirse sin seguir TDD, pero TDD depende de un ciclo continuo de pruebas y cambios pequeños.

---

## 14. Relación con BDD y SDD

Las pruebas unitarias también encajan dentro de metodologías más amplias.

### BDD: Behavior-Driven Development

BDD define y comunica comportamientos esperados utilizando ejemplos comprensibles para el negocio.

Por ejemplo:

```gherkin
Feature: Registro de usuarios

  Scenario: Registrar un usuario nuevo
    Given que el email no está registrado
    When se registra el usuario
    Then el usuario debe guardarse correctamente
```

Este escenario puede orientar la creación de pruebas automatizadas en diferentes niveles. No tiene por qué convertirse directamente en una prueba unitaria: puede ser una prueba de servicio, integración o aceptación, según cómo se implemente.

### SDD: Spec-Driven Development

SDD parte de una especificación explícita que describe requisitos, restricciones y criterios de aceptación.

Una especificación puede definir:

- El comportamiento esperado.
- Las entradas válidas e inválidas.
- Las reglas de negocio.
- Los criterios de aceptación.
- Los casos límite.

A partir de ella, el equipo puede diseñar pruebas unitarias que verifiquen las reglas concretas.

Una secuencia de trabajo posible es:

**SDD define qué construir → BDD aclara el comportamiento → TDD guía la implementación → las pruebas unitarias verifican reglas concretas → las pruebas de integración validan colaboraciones.**

Estas prácticas se complementan, pero no son equivalentes ni obligan a utilizarse todas juntas.

---

## 15. Cobertura de código y calidad de las pruebas

La cobertura mide qué parte del código se ejecuta durante las pruebas.

Existen distintas métricas.

### Cobertura de líneas

Mide qué proporción de líneas ejecutables se ha ejecutado.

### Cobertura de instrucciones

Mide qué proporción de instrucciones ejecutables se ha cubierto.

### Cobertura de ramas

Mide qué proporción de las alternativas de decisión se ha recorrido.

Por ejemplo:

```java
if (amount.signum() > 0) {
    return "VALID";
}

return "INVALID";
```

Para cubrir ambas ramas hay que probar un importe positivo y otro que no lo sea.

### Cobertura de métodos

Indica qué métodos se han ejecutado durante las pruebas.

### ¿Es suficiente alcanzar un 80 % de cobertura?

No necesariamente.

Un proyecto puede tener una cobertura elevada y, aun así, contener pruebas que no detectan errores importantes.

Ejemplo de una prueba débil:

```java
@Test
void shouldCalculateDiscount() {
    DiscountService service = new DiscountService();

    service.calculateDiscount(
        new BigDecimal("100.00"), true
    );
}
```

Esta prueba ejecuta el método, pero no comprueba el resultado. Si el método devuelve un valor incorrecto, la prueba podría seguir pasando.

Una prueba más útil verifica el comportamiento:

```java
@Test
void shouldCalculateDiscount() {
    DiscountService service = new DiscountService();

    BigDecimal result = service.calculateDiscount(
        new BigDecimal("100.00"), true
    );

    assertEquals(new BigDecimal("20.00"), result);
}
```

**La cobertura mide ejecución, no calidad ni capacidad de detectar defectos.** Conviene combinarla con revisión de escenarios, aserciones significativas y análisis de mutaciones cuando el contexto lo justifique.

---

## 16. Mutation Testing

El *mutation testing* evalúa si las pruebas son capaces de detectar pequeños cambios artificiales en el código.

Por ejemplo, un mutante podría cambiar:

```java
return amount.multiply(rate);
```

por:

```java
return amount.multiply(BigDecimal.ZERO);
```

Si todas las pruebas siguen pasando, es posible que no estén verificando adecuadamente el resultado.

Herramientas como PIT pueden introducir mutaciones en proyectos Java y comprobar si las pruebas las detectan.

La métrica de mutaciones detectadas ofrece información adicional sobre la capacidad de las pruebas, pero no es una garantía absoluta de calidad.

El mutation testing resulta especialmente útil en módulos críticos, algoritmos complejos y reglas de negocio importantes.

---

## 17. Integración con SonarQube y JaCoCo

Las pruebas unitarias y el análisis estático de código cumplen funciones diferentes y complementarias.

- **JUnit:** ejecuta las pruebas y verifica el comportamiento.
- **Mockito:** permite aislar dependencias cuando resulta útil.
- **JaCoCo:** mide la cobertura de código durante la ejecución de pruebas.
- **SonarQube:** analiza la calidad del código y puede incorporar los informes de cobertura para mostrar métricas y aplicar condiciones de calidad.

SonarQube no sustituye la ejecución de las pruebas unitarias. Tampoco puede demostrar por sí solo que la lógica de negocio es correcta.

### Ejemplo de configuración Maven

Una configuración simplificada puede incluir JUnit 5, Mockito y JaCoCo:

```xml
<dependencies>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>5.11.4</version>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>org.mockito</groupId>
        <artifactId>mockito-junit-jupiter</artifactId>
        <version>5.14.2</version>
        <scope>test</scope>
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>3.5.2</version>
        </plugin>

        <plugin>
            <groupId>org.jacoco</groupId>
            <artifactId>jacoco-maven-plugin</artifactId>
            <version>0.8.12</version>
            <executions>
                <execution>
                    <goals>
                        <goal>prepare-agent</goal>
                    </goals>
                </execution>

                <execution>
                    <id>report</id>
                    <phase>test</phase>
                    <goals>
                        <goal>report</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

Las versiones son ilustrativas y deben ajustarse a las versiones compatibles con el proyecto y su entorno Java.

Para ejecutar las pruebas:

```bash
mvn clean test
```

Con la configuración anterior, JaCoCo genera normalmente el informe HTML en:

```text
target/site/jacoco/index.html
```

El informe XML suele encontrarse en:

```text
target/site/jacoco/jacoco.xml
```

El XML puede utilizarse para integrar los datos de cobertura con SonarQube, configurando la ruta correspondiente en el análisis.

Una pipeline de CI/CD puede ejecutar las pruebas y analizar el proyecto antes de permitir la integración de cambios.

---

## 18. Buenas prácticas para escribir pruebas unitarias

### 18.1. Utilizar nombres descriptivos

Evitar nombres genéricos como:

```java
@Test
void test1() {
}
```

Preferir:

```java
@Test
void shouldRejectInvalidEmail() {
}
```

### 18.2. Probar comportamiento, no implementación

Una prueba debería verificar resultados, excepciones o efectos observables relevantes, en lugar de comprobar detalles internos que pueden cambiar durante una refactorización.

### 18.3. Evitar mocks innecesarios

No es preciso simular todos los objetos. Las clases pequeñas y deterministas suelen poder utilizarse directamente.

### 18.4. No duplicar la lógica de producción

Una prueba no debería reproducir exactamente el algoritmo que está verificando para calcular el resultado esperado. De lo contrario, el mismo error podría aparecer tanto en la implementación como en la prueba.

### 18.5. Controlar el tiempo y la aleatoriedad

Cuando una funcionalidad depende de fechas o valores aleatorios, conviene inyectar una fuente controlable para obtener pruebas reproducibles.

### 18.6. Probar las excepciones de forma específica

Es preferible comprobar que se produce el tipo de excepción esperado y, cuando sea relevante, verificar su mensaje o contexto.

### 18.7. Mantener las pruebas junto al código

Una estructura habitual en Maven es:

```text
src/
├── main/
│   └── java/
│       └── com/example/
│           ├── DiscountService.java
│           └── UserService.java
└── test/
    └── java/
        └── com/example/
            ├── DiscountServiceTest.java
            └── UserServiceTest.java
```

### 18.8. Ejecutar las pruebas automáticamente

Las pruebas deberían ejecutarse durante el desarrollo y en la integración continua, no únicamente antes de una entrega.

### 18.9. Revisar las pruebas que no detectan cambios

Si una implementación puede cambiar de forma incorrecta y todas las pruebas siguen pasando, conviene revisar si faltan aserciones o escenarios importantes.

### 18.10. Evitar pruebas frágiles

Una prueba frágil falla por cambios internos irrelevantes, formatos variables o dependencias ambientales, aunque el comportamiento principal siga siendo correcto.

---

## 19. Errores frecuentes

### Probar solo el camino feliz

Se valida el caso correcto, pero no los errores, límites ni condiciones excepcionales.

**Solución:** diseñar los escenarios a partir de las reglas de negocio y sus límites.

### Confundir cobertura con calidad

Se busca aumentar el porcentaje ejecutando métodos sin comprobar sus resultados.

**Solución:** priorizar aserciones significativas y escenarios de riesgo.

### Utilizar demasiados mocks

Las pruebas terminan verificando interacciones internas en lugar del comportamiento de la unidad.

**Solución:** simular únicamente dependencias cuya colaboración sea relevante para el escenario.

### Hacer pruebas dependientes del orden

Una prueba modifica datos que otra necesita, por lo que la suite falla de forma intermitente.

**Solución:** preparar el estado de cada prueba de manera independiente.

### Probar detalles de implementación

Cambios internos inocuos provocan numerosas modificaciones en las pruebas.

**Solución:** probar contratos y comportamientos observables.

### Ignorar las pruebas después de refactorizar

Se modifica el código sin ejecutar la suite completa.

**Solución:** integrar la ejecución de pruebas en el flujo normal de trabajo y en CI/CD.

### Escribir pruebas difíciles de mantener

La preparación de datos es excesiva y el motivo de la prueba no está claro.

**Solución:** utilizar datos pequeños, nombres descriptivos y una estructura AAA legible.

---

## 20. Relación con SOLID, Clean Code y otros principios

Las pruebas unitarias no son únicamente un mecanismo de detección de errores. También pueden ayudar a descubrir problemas de diseño.

### SOLID

- **Single Responsibility Principle (SRP):** las clases con una responsabilidad clara suelen ser más sencillas de probar.
- **Open/Closed Principle (OCP):** una estructura extensible permite añadir comportamientos sin modificar innecesariamente código estable.
- **Liskov Substitution Principle (LSP):** las implementaciones deben respetar los contratos esperados; las pruebas pueden verificar ese comportamiento.
- **Interface Segregation Principle (ISP):** interfaces pequeñas facilitan la creación de colaboradores de prueba.
- **Dependency Inversion Principle (DIP):** depender de abstracciones facilita sustituir servicios externos por dobles de prueba.

### Clean Code

Las pruebas también deben ser legibles, tener nombres claros, evitar duplicaciones innecesarias y expresar una intención concreta.

### DRY

Se puede reutilizar preparación común cuando aporta claridad. Sin embargo, una abstracción de pruebas demasiado compleja puede dificultar la comprensión de cada escenario.

### KISS

Es preferible una prueba sencilla y explícita a una infraestructura sofisticada que esconda lo que realmente se está comprobando.

### YAGNI

No es necesario construir un framework de pruebas propio si JUnit, Mockito y unas pocas utilidades cubren las necesidades actuales.

**Una arquitectura fácil de probar suele beneficiarse de una buena separación de responsabilidades y de dependencias explícitas.** Sin embargo, la testabilidad no debe conseguirse a costa de convertir cada detalle interno en una interfaz o un mock.

---

## 21. ¿Qué merece una prueba unitaria?

No todas las líneas de código tienen el mismo riesgo ni necesitan el mismo esfuerzo de pruebas.

Es especialmente importante probar:

- Reglas de negocio.
- Cálculos financieros.
- Validaciones.
- Algoritmos.
- Transiciones de estado.
- Tratamiento de errores.
- Permisos y decisiones de autorización.
- Transformaciones de datos.
- Comportamientos con múltiples casos límite.

Puede tener menos valor crear pruebas específicas para métodos triviales que simplemente delegan en otra llamada, salvo que exista un motivo relevante.

La prioridad debería depender de la complejidad, el impacto de un fallo, la frecuencia de cambio y el coste de verificar el comportamiento.

---

## 22. Estrategia de adopción en un proyecto real

Una forma práctica de introducir pruebas unitarias en un proyecto existente es hacerlo gradualmente.

### Fase 1: establecer una base

- Configurar JUnit y el sistema de construcción.
- Ejecutar las pruebas desde el entorno local.
- Incorporar una ejecución automática en CI/CD.

### Fase 2: comenzar por las reglas críticas

Seleccionar los servicios de negocio que tengan mayor riesgo o impacto.

### Fase 3: definir una convención

Acordar nombres, estructura de pruebas, uso de mocks, organización de datos y tratamiento de excepciones.

### Fase 4: añadir cobertura de forma progresiva

Medir la cobertura inicial y establecer objetivos razonables, evitando perseguir porcentajes sin evaluar la utilidad de las pruebas.

### Fase 5: proteger el código nuevo

Solicitar pruebas para nuevas reglas de negocio y correcciones de errores. Una prueba de regresión debería reproducir el defecto antes de corregirlo y comprobar que la corrección lo resuelve.

### Fase 6: mejorar continuamente

Revisar fallos intermitentes, pruebas lentas, escenarios insuficientes y oportunidades de simplificación.

En proyectos heredados, una estrategia eficaz es **probar primero el comportamiento que se va a modificar**, en lugar de intentar cubrir toda la aplicación de una sola vez.

---

## 23. Checklist de revisión de pruebas unitarias

Antes de considerar terminada una prueba, conviene comprobar:

- El nombre explica el comportamiento esperado.
- El escenario es independiente.
- Los datos de entrada son claros.
- El resultado se verifica mediante aserciones.
- Se contemplan los límites relevantes.
- Las excepciones esperadas se verifican correctamente.
- Las dependencias externas están controladas cuando es necesario.
- La prueba no depende de la hora real, del azar o del orden de ejecución.
- La prueba no está excesivamente acoplada a la implementación.
- Se ejecuta automáticamente en CI/CD.
- Un fallo proporciona información útil para diagnosticar el problema.

---

## 24. Conclusión

Las pruebas unitarias son una de las herramientas fundamentales para construir software mantenible y fiable.

Permiten verificar reglas de negocio de forma rápida, detectar regresiones, facilitar la refactorización y documentar comportamientos esperados mediante ejemplos ejecutables.

Su eficacia no depende únicamente de la cantidad de pruebas ni del porcentaje de cobertura. Depende de que comprueben los comportamientos correctos, contemplen los escenarios relevantes y sean suficientemente sencillas para mantenerse a lo largo del tiempo.

Una estrategia de calidad equilibrada combina:

- Pruebas unitarias para verificar unidades de comportamiento.
- Pruebas de integración para comprobar colaboraciones reales.
- Pruebas de aceptación para validar requisitos.
- Análisis estático mediante herramientas como SonarQube.
- Cobertura con herramientas como JaCoCo.
- Ejecución automática en CI/CD.
- Revisión continua de diseño y mantenibilidad.

El objetivo final no es tener muchas pruebas, sino disponer de una red de seguridad fiable que permita desarrollar y modificar el software con confianza.

---

## 25. Referencias oficiales

- JUnit 5: https://junit.org/junit5/
- Guía de usuario de JUnit: https://junit.org/junit5/docs/current/user-guide/
- Mockito: https://site.mockito.org/
- Documentación de Mockito: https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html
- JaCoCo: https://www.jacoco.org/jacoco/
- PIT Mutation Testing: https://pitest.org/
- SonarQube: https://docs.sonarsource.com/sonarqube-server/
- Martin Fowler, Test Pyramid: https://martinfowler.com/articles/practical-test-pyramid.html
- Martin Fowler, Test-Driven Development: https://martinfowler.com/bliki/TestDrivenDevelopment.html
