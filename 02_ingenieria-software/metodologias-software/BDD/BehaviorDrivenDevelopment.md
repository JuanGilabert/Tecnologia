# BDD (Behavior-Driven Development)
## Desarrollo guiado por el comportamiento en ingeniería del software

## 1. Introducción

**BDD (Behavior-Driven Development)**, o **Desarrollo Guiado por el Comportamiento**, es una metodología de desarrollo de software que busca mejorar la colaboración entre negocio, producto, QA y desarrollo mediante la definición compartida del comportamiento esperado de una aplicación.

Su objetivo es que todas las personas involucradas comprendan qué debe hacer el sistema, en qué condiciones debe hacerlo y qué resultados se consideran correctos.

BDD combina prácticas de desarrollo ágil, ejemplos concretos de negocio, criterios de aceptación y automatización de pruebas.

En lugar de limitarse a describir funcionalidades de forma abstracta, BDD promueve la definición de escenarios comprensibles que expresen el comportamiento observable del sistema.

Por ejemplo, en lugar de indicar:

> El sistema debe gestionar los descuentos de los pedidos.

Podemos definir un comportamiento más preciso:

> Cuando un cliente aplica un cupón válido a un pedido, el sistema debe recalcular el importe total y mostrar el descuento correspondiente.

Este comportamiento puede describirse mediante ejemplos concretos y, si resulta conveniente, automatizarse como una prueba de aceptación.

BDD se asocia habitualmente con el lenguaje **Gherkin**, que permite expresar escenarios mediante palabras clave como `Feature`, `Scenario`, `Given`, `When` y `Then`.

---

## 2. ¿Qué problema resuelve BDD?

En muchos proyectos, los requisitos se describen utilizando expresiones ambiguas como:

- El sistema debe ser rápido.
- El usuario debe poder gestionar sus pedidos.
- El proceso de pago debe ser seguro.
- El sistema debe validar los datos correctamente.

Estas descripciones pueden ser interpretadas de manera diferente por las personas involucradas.

Por ejemplo, para un desarrollador, «validar un pedido» podría significar comprobar que tiene productos. Para negocio, podría implicar también verificar el stock, la dirección de entrega, los límites de compra y las condiciones de pago.

Esta diferencia de interpretación puede provocar:

- Funcionalidades que no cumplen las expectativas del negocio.
- Requisitos incompletos o ambiguos.
- Discusiones tardías sobre qué comportamiento era el correcto.
- Pruebas que verifican una interpretación diferente de los requisitos.
- Criterios de aceptación difíciles de comprobar.
- Retrabajo y retrasos en la entrega.

BDD intenta reducir estos problemas mediante la colaboración temprana y el uso de ejemplos concretos.

### Ejemplo

Requisito ambiguo:

«El sistema debe bloquear a los usuarios que introduzcan una contraseña incorrecta demasiadas veces».

Preguntas que deberían aclararse:

- ¿Cuántos intentos están permitidos?
- ¿Durante cuánto tiempo se bloquea la cuenta?
- ¿Qué ocurre si el usuario recupera su contraseña?
- ¿Se aplica el bloqueo por usuario, por dispositivo o por dirección IP?
- ¿Qué mensaje se muestra?
- ¿Cómo se evita revelar información sensible?

BDD ayuda a transformar estas preguntas en reglas de negocio y escenarios verificables.

---

## 3. Principios fundamentales de BDD

### 3.1. Colaboración entre negocio, desarrollo y QA

BDD fomenta la colaboración entre las personas que conocen las necesidades del negocio y las que implementan y verifican el sistema.

El objetivo es construir un entendimiento compartido antes de que las decisiones importantes queden reflejadas en el código.

### 3.2. Comportamientos expresados mediante ejemplos

Los requisitos generales suelen ser insuficientes para aclarar todas las condiciones relevantes.

BDD utiliza ejemplos concretos para mostrar cómo debe comportarse el sistema ante diferentes situaciones.

Por ejemplo:

- Un cliente con saldo suficiente puede realizar una compra.
- Un cliente con saldo insuficiente recibe un rechazo.
- Un pedido sin productos no puede confirmarse.

Los ejemplos hacen visibles las reglas y sus límites.

### 3.3. Lenguaje comprensible para todas las partes

Los escenarios deben describir comportamientos de negocio sin depender innecesariamente de detalles técnicos.

Es preferible hablar de «confirmar un pedido» que de «invocar el método `setStatus(CONFIRMED)`».

### 3.4. Especificaciones ejecutables cuando aportan valor

Los escenarios pueden vincularse con código de automatización para comprobar que la aplicación cumple los comportamientos definidos.

Sin embargo, **BDD no exige que todos los escenarios estén automatizados**. La automatización debe centrarse en comportamientos importantes, estables y repetibles.

### 3.5. Feedback temprano

Al definir los escenarios antes de implementar una funcionalidad, el equipo puede descubrir discrepancias en los requisitos cuando todavía son relativamente fáciles de resolver.

### 3.6. Enfoque en el comportamiento observable

Los escenarios deben describir qué puede observar un usuario, un consumidor del servicio o una parte interesada.

No deberían depender innecesariamente de la estructura interna del código.

---

## 4. El ciclo de trabajo de BDD

Un proceso habitual de BDD puede representarse así:

```text
Necesidad del negocio
        |
        v
Conversación entre negocio, QA y desarrollo
        |
        v
Ejemplos concretos de comportamiento
        |
        v
Escenarios y criterios de aceptación
        |
        v
Automatización de escenarios seleccionados
        |
        v
Implementación de la funcionalidad
        |
        v
Ejecución de las pruebas
        |
        v
Revisión de resultados y aprendizaje
        |
        +------> Refinar ejemplos o requisitos
```

Este flujo es iterativo. No es necesario definir todos los escenarios del producto antes de empezar a implementar.

### Fase 1. Descubrir el comportamiento

El equipo conversa sobre la necesidad que debe resolver la funcionalidad.

Se identifican las reglas del negocio, las condiciones de entrada, los resultados esperados y los casos excepcionales.

### Fase 2. Definir ejemplos

Se seleccionan ejemplos que representen los comportamientos más importantes.

Pueden incluir el camino habitual, errores previsibles, límites y reglas especiales.

### Fase 3. Expresar escenarios

Los ejemplos se documentan utilizando lenguaje natural estructurado, frecuentemente mediante Gherkin.

### Fase 4. Automatizar cuando sea útil

Los escenarios seleccionados se conectan con código de pruebas que interactúa con la aplicación.

### Fase 5. Implementar

El equipo desarrolla la funcionalidad para satisfacer los comportamientos acordados.

Puede utilizar TDD dentro de esta fase para implementar las reglas de negocio con pruebas unitarias más pequeñas.

### Fase 6. Verificar y revisar

Se ejecutan las pruebas, se analizan los fallos y se revisa con las partes interesadas si los comportamientos representan correctamente las necesidades del negocio.

---

## 5. Gherkin: lenguaje habitual de BDD

**Gherkin** es un lenguaje de especificación estructurado que permite describir comportamientos de manera legible.

Es utilizado por herramientas como Cucumber y puede adaptarse a distintos lenguajes de programación.

Sus palabras clave más conocidas son:

| Palabra clave | Propósito |
|---|---|
| `Feature` | Agrupa una funcionalidad o capacidad del sistema. |
| `Rule` | Describe una regla de negocio relacionada con la funcionalidad. |
| `Scenario` | Define un ejemplo concreto de comportamiento. |
| `Given` | Establece el contexto o las condiciones iniciales. |
| `When` | Describe la acción o el acontecimiento. |
| `Then` | Expresa el resultado esperado. |
| `And` | Añade una condición o paso relacionado. |
| `But` | Añade una condición contrastante. |
| `Background` | Define contexto compartido por los escenarios de una misma sección. |
| `Scenario Outline` | Permite ejecutar un escenario con varios conjuntos de datos. |
| `Examples` | Proporciona los datos que utiliza un `Scenario Outline`. |

Gherkin admite otros elementos y reglas sintácticas, pero estos son suficientes para comenzar.

### 5.1. Estructura Given-When-Then

El patrón central de muchos escenarios BDD es:

- **Given (Dado):** contexto inicial.
- **When (Cuando):** acción que desencadena el comportamiento.
- **Then (Entonces):** resultado observable esperado.

Ejemplo:

```gherkin
Feature: Aplicación de descuentos

  Scenario: Aplicar un cupón válido
    Given que el cliente tiene un pedido de 100 euros
    And dispone de un cupón de descuento del 10 por ciento
    When aplica el cupón al pedido
    Then el descuento aplicado debe ser de 10 euros
    And el total del pedido debe ser de 90 euros
```

Este escenario es comprensible para negocio, QA y desarrollo.

Describe qué debe suceder, sin imponer si el cálculo se realiza en un servicio, una clase o una función concreta.

### 5.2. Qué debe evitarse en Given-When-Then

Un escenario excesivamente técnico podría ser:

```gherkin
Scenario: Aplicar descuento
  Given que se instancia la clase PedidoService
  And se crea un mock de CuponRepository
  When se invoca calcularTotal()
  Then debe llamarse a save() una vez
```

Este escenario puede ser apropiado para una prueba unitaria de interacción, pero normalmente no es una buena especificación BDD de negocio.

Describe detalles de implementación en lugar del comportamiento que interesa a las partes interesadas.

---

## 6. Ejemplo completo: compra con descuento

Vamos a desarrollar un ejemplo de BDD con Java, Cucumber y Maven.

### 6.1. Requisito funcional

Una tienda permite aplicar cupones de descuento a los pedidos.

Reglas:

1. Un pedido válido tiene un importe igual o superior a cero.
2. El cupón contiene un porcentaje de descuento entre 0 y 100.
3. El descuento se calcula sobre el importe original.
4. Un cupón válido actualiza el total.
5. Un porcentaje fuera de rango debe rechazarse.
6. El comportamiento esperado debe poder comprobarse de forma automatizada.

En una aplicación real también habría que especificar caducidad, uso único, elegibilidad del cliente, impuestos, moneda y reglas de acumulación de cupones cuando correspondiera.

### 6.2. Escenario principal

Archivo: `src/test/resources/features/descuentos.feature`

```gherkin
Feature: Aplicación de descuentos
  Para reducir el importe de mis compras
  Como cliente de la tienda
  Quiero aplicar un cupón válido a mi pedido

  Rule: El descuento se calcula sobre el importe original

    Scenario: Aplicar un cupón del 10 por ciento
      Given que el pedido tiene un importe de 100 euros
      And el cupón ofrece un descuento del 10 por ciento
      When el cliente aplica el cupón
      Then el descuento aplicado debe ser de 10 euros
      And el importe final debe ser de 90 euros
```

Las líneas descriptivas que comienzan con `Para`, `Como` y `Quiero` suelen llamarse formato de historia de usuario. Son texto descriptivo; no constituyen una regla ejecutable especial de Gherkin.

### 6.3. Añadir casos adicionales

Es importante no comprobar solamente el camino feliz.

```gherkin
Feature: Aplicación de descuentos

  Scenario: Aplicar un descuento del 10 por ciento
    Given que el pedido tiene un importe de 100 euros
    And el cupón ofrece un descuento del 10 por ciento
    When el cliente aplica el cupón
    Then el importe final debe ser de 90 euros

  Scenario: Aplicar un descuento del 100 por ciento
    Given que el pedido tiene un importe de 100 euros
    And el cupón ofrece un descuento del 100 por ciento
    When el cliente aplica el cupón
    Then el importe final debe ser de 0 euros

  Scenario: Rechazar un descuento superior al 100 por ciento
    Given que el pedido tiene un importe de 100 euros
    And el cupón ofrece un descuento del 120 por ciento
    When el cliente intenta aplicar el cupón
    Then el sistema debe rechazar el cupón
    And debe informar de que el porcentaje no es válido
```

Los escenarios expresan resultados observables y pueden convertirse en criterios de aceptación.

---

## 7. Scenario Outline y Examples

Cuando varios ejemplos comparten la misma estructura, Gherkin permite utilizar `Scenario Outline` con una tabla `Examples`.

Esto evita repetir escenarios casi idénticos.

```gherkin
Feature: Aplicación de descuentos

  Scenario Outline: Calcular el precio final
    Given que el pedido tiene un importe de <importe> euros
    And el cupón ofrece un descuento del <descuento> por ciento
    When el cliente aplica el cupón
    Then el importe final debe ser de <total> euros

    Examples:
      | importe | descuento | total |
      | 100     | 0         | 100   |
      | 100     | 10        | 90    |
      | 100     | 25        | 75    |
      | 100     | 100       | 0     |
```

Cada fila representa una ejecución del escenario.

### ¿Cuándo conviene utilizarlo?

Es apropiado cuando:

- La estructura del comportamiento es la misma.
- Solo cambian los datos de entrada y los resultados esperados.
- Los ejemplos se comprenden fácilmente en una tabla.

No conviene convertir en una tabla enorme un conjunto de escenarios que representa reglas de negocio diferentes. En esos casos, es mejor mantener escenarios separados.

---

## 8. Implementación con Java y Cucumber

Gherkin describe los escenarios, pero necesita una herramienta que los vincule con el código de pruebas.

**Cucumber** es una de las opciones habituales para automatizar escenarios Gherkin.

El siguiente ejemplo utiliza Java y Cucumber con JUnit Platform.

### 8.1. Dependencias Maven

Un `pom.xml` puede incluir dependencias como las siguientes:

```xml
<properties>
    <maven.compiler.release>17</maven.compiler.release>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <cucumber.version>7.20.1</cucumber.version>
    <junit.platform.version>1.11.4</junit.platform.version>
</properties>

<dependencies>
    <dependency>
        <groupId>io.cucumber</groupId>
        <artifactId>cucumber-java</artifactId>
        <version>${cucumber.version}</version>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>io.cucumber</groupId>
        <artifactId>cucumber-junit-platform-engine</artifactId>
        <version>${cucumber.version}</version>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>org.junit.platform</groupId>
        <artifactId>junit-platform-suite</artifactId>
        <version>${junit.platform.version}</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

Las versiones son un ejemplo reproducible de referencia, no una afirmación de que sean las últimas disponibles. En proyectos nuevos conviene verificar la compatibilidad entre las versiones de Java, Cucumber, JUnit Platform y Maven.

### 8.2. Código de producción

Archivo: `src/main/java/com/ejemplo/CalculadoraDescuentos.java`

```java
package com.ejemplo;

public class CalculadoraDescuentos {

    public double calcularPrecioFinal(
            double importe,
            double porcentajeDescuento) {

        if (!Double.isFinite(importe) || importe < 0) {
            throw new IllegalArgumentException(
                    "El importe no puede ser negativo ni no finito"
            );
        }

        if (!Double.isFinite(porcentajeDescuento)
                || porcentajeDescuento < 0
                || porcentajeDescuento > 100) {
            throw new IllegalArgumentException(
                    "El porcentaje debe estar entre 0 y 100"
            );
        }

        return importe * (1 - porcentajeDescuento / 100.0);
    }
}
```

Esta clase implementa la regla de cálculo y validación.

Para importes monetarios reales, se recomienda utilizar `BigDecimal` y establecer explícitamente las reglas de precisión y redondeo.

### 8.3. Definiciones de pasos

Las definiciones de pasos conectan las frases de Gherkin con el código Java.

Archivo: `src/test/java/com/ejemplo/DescuentosSteps.java`

```java
package com.ejemplo;

import io.cucumber.java.es.Dado;
import io.cucumber.java.es.Cuando;
import io.cucumber.java.es.Entonces;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertThrows;

public class DescuentosSteps {

    private final CalculadoraDescuentos calculadora =
            new CalculadoraDescuentos();

    private double importe;
    private double descuento;
    private double resultado;
    private Exception error;

    @Dado("que el pedido tiene un importe de {double} euros")
    public void elPedidoTieneUnImporte(double importe) {
        this.importe = importe;
    }

    @Dado("el cupón ofrece un descuento del {double} por ciento")
    public void elCuponOfreceUnDescuento(double descuento) {
        this.descuento = descuento;
    }

    @Cuando("el cliente aplica el cupón")
    public void elClienteAplicaElCupon() {
        resultado = calculadora.calcularPrecioFinal(
                importe,
                descuento
        );
    }

    @Cuando("el cliente intenta aplicar el cupón")
    public void elClienteIntentaAplicarElCupon() {
        try {
            resultado = calculadora.calcularPrecioFinal(
                    importe,
                    descuento
            );
        } catch (IllegalArgumentException exception) {
            error = exception;
        }
    }

    @Entonces("el importe final debe ser de {double} euros")
    public void elImporteFinalDebeSer(double esperado) {
        assertEquals(esperado, resultado, 0.001);
    }

    @Entonces("el sistema debe rechazar el cupón")
    public void elSistemaDebeRechazarElCupon() {
        assertEquals(
                IllegalArgumentException.class,
                error == null ? null : error.getClass()
        );
    }

    @Entonces("debe informar de que el porcentaje no es válido")
    public void debeInformarDePorcentajeNoValido() {
        assertEquals(
                "El porcentaje debe estar entre 0 y 100",
                error.getMessage()
        );
    }
}
```

Este ejemplo ilustra el mecanismo de enlace, pero tiene una limitación importante: si la operación del camino feliz falla, Cucumber marcará ese paso como fallido y no ejecutará los pasos posteriores de ese escenario.

En un proyecto real, también conviene modelar explícitamente el estado del resultado y evitar que los escenarios compartan estado accidentalmente.

### 8.4. Runner de Cucumber

Archivo: `src/test/java/com/ejemplo/RunCucumberTest.java`

```java
package com.ejemplo;

import org.junit.platform.suite.api.ConfigurationParameter;
import org.junit.platform.suite.api.IncludeEngines;
import org.junit.platform.suite.api.SelectClasspathResource;
import org.junit.platform.suite.api.Suite;

import static io.cucumber.junit.platform.engine.Constants.GLUE_PROPERTY_NAME;
import static io.cucumber.junit.platform.engine.Constants.PLUGIN_PROPERTY_NAME;

@Suite
@IncludeEngines("cucumber")
@SelectClasspathResource("features")
@ConfigurationParameter(
        key = GLUE_PROPERTY_NAME,
        value = "com.ejemplo"
)
@ConfigurationParameter(
        key = PLUGIN_PROPERTY_NAME,
        value = "pretty"
)
public class RunCucumberTest {
}
```

Para utilizar las anotaciones en español, hay que configurar Cucumber para reconocer el idioma de los archivos Gherkin. Se puede añadir la propiedad:

```properties
# src/test/resources/junit-platform.properties
cucumber.glue=com.ejemplo
cucumber.plugin=pretty
cucumber.execution.dry-run=false
cucumber.filter.tags=not @ignore
cucumber.ansi-colors.disabled=true
```

Y establecer el idioma español al principio del archivo `.feature`:

```gherkin
# language: es
Característica: Aplicación de descuentos
```

Las definiciones de pasos anteriores utilizan anotaciones españolas como `@Dado`, `@Cuando` y `@Entonces`. La configuración de idioma y las anotaciones deben ser coherentes con la versión de Cucumber utilizada.

### 8.5. Organización del proyecto

```text
mi-proyecto/
├── pom.xml
└── src/
    ├── main/
    │   └── java/
    │       └── com/ejemplo/
    │           └── CalculadoraDescuentos.java
    └── test/
        ├── java/
        │   └── com/ejemplo/
        │       ├── DescuentosSteps.java
        │       └── RunCucumberTest.java
        └── resources/
            ├── junit-platform.properties
            └── features/
                └── descuentos.feature
```

Para ejecutar las pruebas:

```bash
mvn test
```

En proyectos reales, la configuración exacta puede variar según las versiones de Cucumber, JUnit Platform y Maven Surefire.

---

## 9. Diferencia entre BDD y TDD

BDD y TDD están relacionados, pero no son equivalentes.

| Aspecto | BDD | TDD |
|---|---|---|
| Nombre completo | Behavior-Driven Development | Test-Driven Development |
| Enfoque principal | Comportamiento y colaboración | Desarrollo guiado por pruebas |
| Pregunta central | ¿Qué debe hacer el sistema para satisfacer la necesidad? | ¿Qué prueba debe guiar la siguiente implementación? |
| Nivel frecuente | Negocio, aceptación y flujos funcionales | Unidad, clase, función o componente |
| Lenguaje habitual | Lenguaje de negocio estructurado | Código de pruebas |
| Artefactos frecuentes | Escenarios Gherkin, ejemplos y criterios de aceptación | Pruebas unitarias y ciclos Red-Green-Refactor |
| Participantes | Negocio, producto, QA y desarrollo | Principalmente desarrollo, aunque no exclusivamente |
| Automatización | Opcional para cada escenario; frecuente en los críticos | Habitualmente automatizada |

### Ejemplo de complementariedad

Un escenario BDD establece:

```gherkin
Scenario: Rechazar una compra sin stock
  Given que el producto no tiene unidades disponibles
  When el cliente intenta confirmar la compra
  Then el pedido no debe confirmarse
  And debe informarse de la falta de stock
```

Dentro de la implementación, el equipo puede aplicar TDD para desarrollar una regla de dominio:

```java
@Test
void noDebePermitirComprarMasUnidadesQueLasDisponibles() {
    // Arrange
    // Act
    // Assert
}
```

BDD define el comportamiento de negocio; TDD ayuda a construir la implementación de manera incremental y verificable.

---

## 10. Relación entre BDD y SDD

**SDD (Spec-Driven Development)** se centra en utilizar una especificación como guía para el desarrollo.

BDD puede formar parte de ese enfoque al convertir partes de la especificación en ejemplos de comportamiento y criterios de aceptación.

Un flujo posible es:

1. **SDD:** definir el objetivo, las reglas, las restricciones y los requisitos.
2. **BDD:** aclarar los comportamientos mediante ejemplos y escenarios.
3. **TDD:** implementar las reglas internas mediante pruebas pequeñas.
4. **CI:** ejecutar las pruebas automáticamente.
5. **Validación:** comprobar que el resultado satisface los criterios acordados.

No existe una única secuencia obligatoria. En la práctica, la especificación y los escenarios se refinan a medida que el equipo aprende.

### Diferencia importante

Una especificación puede incluir requisitos no funcionales, restricciones arquitectónicas, seguridad, rendimiento y decisiones de integración.

Los escenarios BDD no sustituyen necesariamente a esa documentación. Son especialmente útiles para describir comportamientos verificables.

---

## 11. BDD y principios SOLID

BDD puede ayudar a descubrir responsabilidades y contratos poco claros, aunque no sustituye los principios de diseño.

### S — Single Responsibility Principle

Los escenarios permiten identificar distintas responsabilidades funcionales.

Si una funcionalidad combina cálculo de precios, envío de correos y persistencia de datos, los ejemplos pueden ayudar a aclarar qué comportamientos pertenecen a cada componente.

### O — Open/Closed Principle

Los escenarios existentes protegen los comportamientos anteriores cuando se incorporan nuevas reglas.

Si aparecen nuevas variantes de una política de negocio, el diseño puede evolucionar para admitirlas sin acumular condicionales difíciles de mantener.

### L — Liskov Substitution Principle

Los contratos de comportamiento pueden aplicarse a distintas implementaciones de una abstracción.

Por ejemplo, diferentes medios de pago deberían respetar las reglas funcionales acordadas para autorizar o rechazar una operación.

### I — Interface Segregation Principle

Los escenarios ayudan a reconocer capacidades diferenciadas del sistema.

Esto puede orientar la definición de interfaces más específicas, siempre que exista una separación real de responsabilidades.

### D — Dependency Inversion Principle

Las pruebas automatizadas suelen beneficiarse de límites claros entre el dominio y la infraestructura.

Los escenarios BDD pueden verificar el comportamiento de alto nivel mientras las pruebas unitarias y de integración comprueban las implementaciones internas y sus dependencias.

---

## 12. BDD, Clean Code, DRY, KISS y YAGNI

### BDD y Clean Code

Los escenarios deben ser legibles y expresar una intención clara.

Un escenario no debería obligar a entender el código de implementación para descubrir qué comportamiento está verificando.

### BDD y DRY

`Background` y `Scenario Outline` ayudan a evitar repetición en Gherkin.

No obstante, la prioridad debe ser que cada escenario siga siendo fácil de entender. Eliminar texto repetido no justifica crear una abstracción confusa.

### BDD y KISS

Los escenarios deben centrarse en un comportamiento principal y evitar pasos innecesariamente complejos.

Un escenario breve y específico suele ser más útil que otro que intenta describir todo un proceso de negocio de una sola vez.

### BDD y YAGNI

Los escenarios deben derivarse de necesidades y reglas justificadas, no de funcionalidades hipotéticas sin una razón concreta.

Eso no significa ignorar riesgos conocidos o requisitos obligatorios; significa evitar desarrollar comportamientos sin una necesidad identificada.

---

## 13. Cómo escribir buenos escenarios BDD

### 13.1. Utilizar lenguaje de negocio

Preferir:

```gherkin
Then el pedido debe quedar confirmado
```

En lugar de:

```gherkin
Then el campo status debe tener el valor CONFIRMED
```

La segunda forma puede ser adecuada para una prueba técnica, pero normalmente expresa un detalle interno.

### 13.2. Un escenario, una intención principal

Un escenario debe describir un comportamiento reconocible.

Si mezcla registro de usuario, pago, envío, notificaciones y devolución, será más difícil identificar qué regla ha fallado.

### 13.3. Incluir resultados observables

Los resultados pueden ser:

- Un pedido confirmado.
- Un importe calculado.
- Un mensaje de validación.
- Una operación rechazada.
- Un cambio de estado.
- Una respuesta de una API.

Deben corresponder al contrato de la funcionalidad.

### 13.4. Cubrir el camino feliz y los casos relevantes

No basta con verificar que todo funciona con datos válidos.

También deben considerarse los rechazos esperados, los límites y las condiciones excepcionales que tengan relevancia para el negocio.

### 13.5. Evitar escenarios excesivamente largos

Si un escenario contiene demasiados pasos, conviene revisar si realmente describe un único comportamiento.

`Background` puede compartir un contexto sencillo. No debería ocultar dependencias importantes ni convertir los escenarios en secuencias difíciles de entender.

### 13.6. Evitar detalles de infraestructura

Los escenarios de negocio no deberían depender directamente de nombres de clases, consultas SQL, estructuras internas o llamadas a métodos privados.

Estos detalles corresponden a las pruebas técnicas, salvo que formen parte explícita del contrato que se está verificando.

### 13.7. Revisar periódicamente los escenarios

Cuando cambian las reglas de negocio, los escenarios deben actualizarse.

Los escenarios obsoletos pueden inducir a error y dificultar la evolución del sistema.

---

## 14. BDD en diferentes tipos de pruebas

BDD puede aplicarse a diferentes niveles, pero no todos los escenarios necesitan automatizarse de la misma manera.

### 14.1. Pruebas de aceptación

Verifican si el sistema satisface las necesidades del usuario o del negocio.

Ejemplo: un cliente puede finalizar una compra cuando cumple las condiciones necesarias.

### 14.2. Pruebas de API

Verifican comportamientos expuestos por una interfaz HTTP u otro contrato de servicio.

Ejemplo: una solicitud válida crea un pedido y devuelve la respuesta acordada.

### 14.3. Pruebas de interfaz de usuario

Verifican los comportamientos observables de una aplicación a través de su interfaz.

Ejemplo: un usuario introduce datos válidos y recibe confirmación.

Estas pruebas suelen ser más lentas y sensibles a cambios en la interfaz, por lo que conviene reservarlas para flujos importantes.

### 14.4. Pruebas de integración

Comprueban la colaboración entre componentes reales, como una API y una base de datos.

### 14.5. Pruebas unitarias

Las pruebas unitarias suelen escribirse directamente en el lenguaje de programación y se relacionan especialmente con TDD.

No es necesario que cada prueba unitaria esté expresada en Gherkin. BDD se centra en el descubrimiento y la especificación del comportamiento, no en imponer un formato único para todas las pruebas.

---

## 15. Herramientas habituales para BDD

### Cucumber

Permite ejecutar escenarios Gherkin y vincularlos con definiciones de pasos en diferentes lenguajes.

Es una opción conocida en proyectos Java, JavaScript, Ruby y otros entornos.

Documentación: https://cucumber.io/docs/

### SpecFlow y Reqnroll

Reqnroll es una alternativa del ecosistema .NET para automatizar especificaciones Gherkin. Los equipos que mantienen proyectos históricos de SpecFlow deben revisar las opciones de migración y compatibilidad.

Documentación: https://reqnroll.net/

### Behave

Framework de BDD para Python que permite definir escenarios Gherkin y asociarlos a código Python.

Documentación: https://behave.readthedocs.io/

### pytest-bdd

Integra escenarios de estilo Gherkin con pytest, aprovechando el ecosistema de pruebas de Python.

Documentación: https://pytest-bdd.readthedocs.io/

### Playwright

Es una herramienta de automatización de navegadores, no un framework BDD por sí mismo. Puede combinarse con una capa de escenarios y definiciones de pasos cuando el proyecto lo necesita.

Documentación: https://playwright.dev/

### Cómo elegir

La elección depende del lenguaje, la arquitectura, las herramientas existentes y el nivel de automatización necesario.

No conviene incorporar una herramienta BDD solo para escribir archivos `.feature` si el equipo no va a utilizar esos escenarios como medio real de colaboración o validación.

---

## 16. Ventajas de BDD

### 16.1. Mejor entendimiento compartido

Los ejemplos permiten que negocio, QA y desarrollo discutan sobre el mismo comportamiento.

### 16.2. Requisitos más concretos

BDD ayuda a transformar descripciones generales en ejemplos que pueden evaluarse.

### 16.3. Detección temprana de ambigüedades

Las preguntas sobre casos límite y resultados esperados aparecen antes de que la implementación esté terminada.

### 16.4. Criterios de aceptación claros

Los escenarios pueden utilizarse para decidir cuándo una funcionalidad satisface los comportamientos acordados.

### 16.5. Documentación viva

Cuando se mantienen actualizados y automatizados, los escenarios muestran ejemplos del comportamiento actual del sistema.

### 16.6. Regresión funcional

Las pruebas automatizadas ayudan a detectar cambios que rompen flujos importantes.

### 16.7. Mejor colaboración

BDD crea un espacio común para hablar sobre reglas de negocio sin obligar a todas las partes a dominar los detalles técnicos.

---

## 17. Desventajas y limitaciones de BDD

### 17.1. Coste de mantenimiento

Los escenarios y su automatización deben mantenerse alineados con el producto.

Una suite extensa y obsoleta puede convertirse en una carga.

### 17.2. Riesgo de exceso de formalismo

Si se escriben escenarios para cada detalle trivial, la documentación puede resultar repetitiva y difícil de mantener.

### 17.3. Automatización frágil

Los escenarios que dependen excesivamente de interfaces gráficas o de infraestructura inestable pueden fallar por razones ajenas al comportamiento de negocio.

### 17.4. Colaboración insuficiente

Escribir escenarios únicamente desde el equipo técnico, sin participación real de negocio, puede reducir BDD a una sintaxis diferente para las pruebas automatizadas.

### 17.5. Falsa sensación de cobertura

Tener muchos escenarios no significa que se hayan definido todos los comportamientos relevantes.

La calidad depende de los ejemplos elegidos y de la exactitud de las expectativas.

### 17.6. Dificultad para requisitos no funcionales

BDD es especialmente útil para comportamientos observables. Otros requisitos, como objetivos de latencia, resiliencia, seguridad o capacidad, pueden necesitar métricas, análisis y pruebas especializadas adicionales.

---

## 18. Errores habituales al aplicar BDD

| Error | Consecuencia | Alternativa |
|---|---|---|
| Escribir escenarios sin conversar con negocio | Se automatizan interpretaciones incorrectas | Revisar ejemplos con las partes interesadas |
| Utilizar Gherkin como lenguaje de programación | Los escenarios se vuelven difíciles de leer | Mantenerlos orientados al comportamiento |
| Automatizar todos los escenarios sin priorizar | Aumenta el coste de mantenimiento | Priorizar flujos críticos y repetibles |
| Describir detalles internos | Las pruebas se rompen con refactorizaciones | Verificar contratos observables |
| Crear escenarios enormes | Resulta difícil identificar la causa de un fallo | Separar comportamientos distintos |
| Repetir pasos de forma innecesaria | La suite pierde claridad | Utilizar abstracciones simples y justificadas |
| Usar `Background` para ocultar contexto complejo | Los escenarios resultan difíciles de comprender | Mantener explícitas las condiciones importantes |
| Ignorar los casos negativos | Quedan reglas sin verificar | Incluir rechazos y límites relevantes |
| Tratar BDD como sustituto de TDD | Se descuida el diseño y la verificación interna | Combinar BDD con pruebas unitarias y TDD |
| Mantener escenarios obsoletos | La documentación pierde credibilidad | Revisar los escenarios junto con los requisitos |

---

## 19. BDD en arquitecturas modernas

### 19.1. Arquitectura hexagonal

BDD puede describir los comportamientos visibles de los casos de uso, mientras que las pruebas unitarias comprueban las reglas internas del dominio.

Los adaptadores se verifican mediante pruebas de integración específicas.

### 19.2. Clean Architecture

Los escenarios de aceptación pueden describir los flujos del usuario y los resultados esperados.

Las pruebas de unidad comprueban los casos de uso y las reglas del dominio sin necesidad de levantar toda la infraestructura.

### 19.3. Microservicios

BDD puede ayudar a definir los comportamientos de las APIs y los flujos de negocio que atraviesan varios servicios.

Para comprobar la compatibilidad entre servicios, conviene añadir pruebas de contrato. Los escenarios de extremo a extremo deben reservarse para flujos importantes porque suelen tener un coste de ejecución y mantenimiento mayor.

### 19.4. Arquitectura orientada a eventos

Los escenarios pueden describir qué ocurre cuando se publica o recibe un evento.

Por ejemplo:

- Un pedido confirmado genera un evento de pedido confirmado.
- Una reserva cancelada libera la disponibilidad.
- Un pago rechazado impide finalizar la compra.

También es necesario verificar aspectos técnicos como duplicados, reintentos, orden de mensajes e idempotencia cuando corresponda.

---

## 20. Integración de BDD en CI/CD

BDD puede integrarse en un pipeline de integración continua y entrega continua.

```text
Cambio de código
       |
       v
Compilación
       |
       v
Pruebas unitarias
       |
       v
Pruebas de integración
       |
       v
Escenarios BDD seleccionados
       |
       v
Análisis de resultados
       |
       +----> Fallo: investigar y corregir
       |
       v
Revisión y posible despliegue
```

El orden y la selección de pruebas dependen de la duración, la infraestructura y el riesgo.

Las pruebas rápidas suelen ejecutarse con más frecuencia, mientras que los escenarios que requieren servicios externos o una interfaz completa pueden ejecutarse en etapas posteriores.

La automatización no elimina la necesidad de revisar los escenarios ni de validar los requisitos con negocio.

---

## 21. Lista de comprobación para aplicar BDD

### Antes de definir escenarios

- [ ] ¿La necesidad de negocio está clara?
- [ ] ¿Participan las personas necesarias para aclarar las reglas?
- [ ] ¿Se han identificado las condiciones iniciales?
- [ ] ¿Se conocen los resultados esperados?
- [ ] ¿Se han discutido los casos límite relevantes?

### Al escribir escenarios

- [ ] ¿El lenguaje es comprensible para negocio y desarrollo?
- [ ] ¿Cada escenario expresa un comportamiento principal?
- [ ] ¿Los resultados son observables y verificables?
- [ ] ¿Se han evitado detalles internos innecesarios?
- [ ] ¿Se cubren los casos positivos y negativos importantes?
- [ ] ¿Los ejemplos son concretos y no ambiguos?

### Al automatizar

- [ ] ¿La automatización aporta valor?
- [ ] ¿Las definiciones de pasos son reutilizables sin resultar genéricas en exceso?
- [ ] ¿Se controla el estado de las pruebas?
- [ ] ¿Las dependencias externas están gestionadas correctamente?
- [ ] ¿Se ha evitado que las pruebas dependan de detalles de interfaz irrelevantes?

### Durante el mantenimiento

- [ ] ¿Los escenarios siguen reflejando las reglas vigentes?
- [ ] ¿Se eliminan los escenarios obsoletos?
- [ ] ¿Los fallos permiten identificar el comportamiento afectado?
- [ ] ¿Los escenarios críticos se ejecutan en CI?
- [ ] ¿Se revisa periódicamente el coste de mantenimiento de la suite?

---

## 22. Estrategia para introducir BDD en un equipo

### Etapa 1. Seleccionar una funcionalidad relevante

Escoger un flujo de negocio que tenga reglas claras y valor para el usuario.

Ejemplos: registro de clientes, recuperación de contraseña, compra de productos o gestión de reservas.

### Etapa 2. Organizar una conversación de descubrimiento

Reunir a negocio, QA y desarrollo para identificar reglas, excepciones y ejemplos.

### Etapa 3. Definir los escenarios

Escribir escenarios pequeños y legibles, priorizando el camino principal y las excepciones relevantes.

### Etapa 4. Validar el lenguaje

Comprobar que las personas de negocio comprenden los escenarios sin necesitar explicaciones sobre las clases o la arquitectura interna.

### Etapa 5. Elegir qué automatizar

Automatizar los comportamientos estables y de alto valor. Mantener los ejemplos exploratorios o todavía ambiguos fuera de la suite automatizada hasta que resulte conveniente.

### Etapa 6. Integrar las pruebas en CI

Ejecutar los escenarios seleccionados automáticamente y hacer visibles sus resultados.

### Etapa 7. Evaluar y mejorar

Revisar si los escenarios reducen las ambigüedades, facilitan la aceptación de las funcionalidades y permiten detectar regresiones importantes.

La adopción debe medirse por el valor que aporta al proceso, no por el número de archivos `.feature` creados.

---

## 23. BDD y herramientas de inteligencia artificial

Las herramientas de IA pueden ayudar a proponer escenarios, generar tablas de ejemplos, detectar ambigüedades o sugerir casos límite.

Un proceso responsable puede seguir estos pasos:

1. Proporcionar la especificación y el contexto de negocio.
2. Solicitar ejemplos de comportamientos positivos, negativos y extremos.
3. Revisar los escenarios con las personas responsables del producto.
4. Seleccionar los comportamientos que conviene automatizar.
5. Generar o mantener las definiciones de pasos.
6. Ejecutar las pruebas y revisar los resultados.
7. Actualizar la especificación cuando cambien las reglas.

La IA puede producir escenarios sintácticamente válidos que representen una regla incorrecta o incompleta. Por ello, los escenarios deben revisarse contra las necesidades reales del negocio.

BDD no consiste en generar muchos archivos Gherkin, sino en mejorar el entendimiento compartido del comportamiento del sistema.

---

## 24. BDD, TDD y SDD: flujo de trabajo conjunto

Las tres metodologías pueden combinarse dentro de un mismo proceso de ingeniería.

```text
SDD
Definir requisitos y restricciones
       |
       v
BDD
Aclarar comportamientos con ejemplos
       |
       v
TDD
Implementar reglas mediante pruebas
       |
       v
SOLID + Clean Code
Mantener una estructura comprensible
       |
       v
CI/CD
Ejecutar pruebas y verificar cambios
       |
       v
Validación funcional
Confirmar que se cumplen las necesidades
```

Este diagrama representa una posible organización del trabajo, no una secuencia universal obligatoria.

En equipos reales, los requisitos, los escenarios, las pruebas y el diseño evolucionan conjuntamente.

- **SDD** ayuda a especificar qué debe construirse.
- **BDD** ayuda a concretar cómo debe comportarse el sistema.
- **TDD** guía la implementación incremental mediante pruebas.
- **SOLID y Clean Code** ayudan a mantener el diseño.
- **CI/CD** automatiza la verificación y facilita la entrega.

---

## 25. Conclusión

BDD es una metodología que mejora la definición y validación de requisitos mediante ejemplos concretos y colaboración entre negocio, QA y desarrollo.

Su principal aportación consiste en transformar expectativas ambiguas en comportamientos comprensibles y verificables. Gherkin permite expresar esos comportamientos de forma estructurada, mientras que herramientas como Cucumber posibilitan automatizar los escenarios que aportan valor.

BDD no consiste simplemente en escribir pruebas de aceptación ni en utilizar una sintaxis especial. Su eficacia depende de la calidad de las conversaciones, la precisión de los ejemplos y el mantenimiento de las especificaciones.

Combinado con TDD, SDD, SOLID, Clean Code e integración continua, BDD puede formar parte de un proceso de ingeniería que reduzca ambigüedades, facilite la colaboración y aumente la confianza en la evolución del software.

La idea central es:

**Primero comprender el comportamiento esperado, después expresarlo mediante ejemplos y finalmente verificar que el software cumple esos comportamientos.**

---

## 26. Referencias y recursos

- **Cucumber Documentation:** documentación oficial sobre Gherkin, escenarios, definiciones de pasos y automatización.  
  https://cucumber.io/docs/

- **Gherkin Reference:** referencia del lenguaje Gherkin y sus palabras clave.  
  https://cucumber.io/docs/gherkin/

- **Behaviour-Driven Development:** recursos sobre BDD y prácticas de descubrimiento colaborativo.  
  https://cucumber.io/docs/bdd/

- **Martin Fowler:** artículos sobre pruebas, diseño, automatización y desarrollo ágil.  
  https://martinfowler.com/

- **Agile Alliance:** recursos sobre desarrollo ágil, colaboración y prácticas de ingeniería.  
  https://www.agilealliance.org/

- **Reqnroll:** documentación de la alternativa BDD para .NET.  
  https://reqnroll.net/

- **Behave:** documentación del framework BDD para Python.  
  https://behave.readthedocs.io/

Estas referencias permiten profundizar tanto en los fundamentos de BDD como en su aplicación práctica con diferentes lenguajes y herramientas.