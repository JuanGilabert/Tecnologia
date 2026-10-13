# KISS — Keep It Simple, Stupid
## Principio de simplicidad en el diseño y la programación de software

## 1. Introducción

**KISS** significa *Keep It Simple, Stupid*, una expresión que puede traducirse como «mantén las cosas simples».

Es un principio de diseño de software que recomienda **buscar la solución más sencilla que resuelva correctamente el problema, cumpla los requisitos y permita mantener y evolucionar el código con facilidad**.

La idea fundamental es evitar la complejidad innecesaria: abstracciones que no aportan valor, jerarquías excesivas, configuraciones complicadas, patrones de diseño aplicados sin necesidad o código difícil de entender cuando existe una alternativa más directa.

KISS no significa escribir menos líneas a cualquier precio. Significa reducir la complejidad accidental y facilitar que otras personas —incluido nuestro yo del futuro— puedan comprender, probar y modificar el software.

Mi recomendación es utilizar KISS como un criterio transversal de diseño, junto con SOLID, DRY, YAGNI y Clean Code:

- KISS: evita la complejidad innecesaria.
- YAGNI: evita construir funcionalidades hipotéticas.
- DRY: evita duplicar reglas y conocimientos que deberían mantenerse coherentes.
- SOLID: estructura las responsabilidades y dependencias para facilitar la evolución.
- Clean Code: expresa las soluciones de forma legible y mantenible.
La idea más importante es esta: no apliques un principio de manera aislada si eso hace que el diseño global empeore. El objetivo final es que el código sea correcto, comprensible, comprobable y razonablemente fácil de modificar.

### 1.1. Objetivos principales

- Facilitar la comprensión del código.
- Reducir la cantidad de conceptos que un desarrollador debe conocer para modificar una funcionalidad.
- Disminuir la probabilidad de errores.
- Simplificar las pruebas y la depuración.
- Facilitar el mantenimiento y la incorporación de nuevas funcionalidades.
- Evitar abstracciones prematuras y sobreingeniería.
- Reducir el coste de evolución del software.

### 1.2. Idea fundamental

> No diseñes una solución más compleja de lo que el problema necesita.

Por ejemplo, si una aplicación solo necesita calcular el precio final de un producto, probablemente no necesite un motor de reglas configurable, una jerarquía de diez clases y un sistema de plugins. Una función clara puede ser suficiente.

Sin embargo, si las reglas de precios cambian frecuentemente, dependen de múltiples estrategias comerciales o deben ser configurables por usuarios, una arquitectura más elaborada puede estar justificada.

**La simplicidad depende del problema, del contexto y de los requisitos reales.**

---

## 2. ¿Qué problema resuelve KISS?

A medida que un sistema crece, es fácil introducir complejidad que no aporta beneficios proporcionales.

Algunos ejemplos habituales son:

- Crear interfaces para implementaciones que nunca van a variar.
- Aplicar patrones de diseño porque se conocen, no porque resuelvan un problema concreto.
- Introducir capas de abstracción que únicamente delegan llamadas.
- Diseñar mecanismos de configuración para necesidades hipotéticas.
- Construir soluciones genéricas para un único caso de uso.
- Dividir una operación sencilla en demasiados métodos o clases.
- Usar expresiones compactas y sofisticadas que dificultan la lectura.
- Introducir dependencias externas para resolver problemas triviales.

Estas decisiones aumentan el esfuerzo necesario para comprender el sistema y modifican la relación entre el valor que aporta una solución y su coste de mantenimiento.

### 2.1. Ejemplo conceptual

Imaginemos que necesitamos obtener el precio final de un producto aplicando un descuento.

Una solución sencilla:

```java
public class PriceCalculator {

    public double calculateFinalPrice(
            double price,
            double discountPercentage) {

        return price * (1 - discountPercentage / 100);
    }
}
```

Una solución excesivamente compleja para ese requisito aislado podría introducir:

- Una fábrica de calculadoras.
- Una jerarquía de políticas de precios.
- Un registro de estrategias.
- Un motor de reglas.
- Un proveedor de configuraciones.
- Un sistema de plugins.

Estos componentes pueden tener sentido en un sistema comercial avanzado, pero no necesariamente en una operación aislada.

El problema no es utilizar clases, interfaces o patrones. El problema es **introducir mecanismos cuyo coste y complejidad superan el beneficio que aportan al requisito actual**.

> Nota: para un sistema financiero o comercial real, conviene utilizar `BigDecimal` en Java en lugar de `double` cuando se necesita precisión decimal y reglas de redondeo explícitas.

---

## 3. Principios fundamentales de KISS

KISS no es una norma formal con una lista universal de reglas. Puede aplicarse mediante varias pautas prácticas.

### 3.1. Preferir soluciones directas

Si una solución sencilla satisface los requisitos actuales, no añadas complejidad sin una razón concreta.

```java
public boolean isAdult(int age) {
    return age >= 18;
}
```

No es necesario crear una clase, una interfaz y una fábrica para una condición tan simple.

### 3.2. Evitar abstracciones prematuras

No introduzcas una abstracción solo porque algún día podría resultar útil.

Por ejemplo, si una aplicación utiliza una única implementación de un servicio y no existe una necesidad real de sustituirla, una interfaz adicional puede ser innecesaria.

Eso no significa que debamos evitar las interfaces. Son valiosas cuando delimitan contratos, aíslan dependencias externas, permiten sustituir implementaciones o facilitan las pruebas.

### 3.3. Hacer que el código sea fácil de leer

La simplicidad también afecta a la expresión del código.

Preferible:

```java
if (user.isActive()) {
    sendNotification(user);
}
```

Frente a:

```java
if (Boolean.TRUE.equals(
        Optional.ofNullable(user)
                .map(User::isActive)
                .orElse(false))) {
    sendNotification(user);
}
```

La segunda alternativa podría estar justificada si `user` puede ser `null` y esa circunstancia debe tratarse de una forma específica. Pero si `user` está garantizado como no nulo, añade complejidad innecesaria.

### 3.4. Resolver el problema real, no todos los problemas imaginables

No debemos diseñar cada funcionalidad pensando en todas las posibles necesidades futuras.

Es preferible implementar una solución correcta y extensible cuando sea necesario, en lugar de anticipar requisitos hipotéticos que quizá nunca aparezcan.

Esto no significa ignorar riesgos previsibles, requisitos de seguridad o restricciones de arquitectura. Significa diferenciar entre necesidades demostrables y posibilidades especulativas.

### 3.5. Mantener las responsabilidades claras

Cuando una clase, método o módulo realiza demasiadas tareas, comprenderlo y modificarlo resulta más difícil.

Por ejemplo, una clase que valida datos, accede a una base de datos, envía correos y genera informes tiene demasiadas responsabilidades para muchos contextos.

Dividirla puede simplificar el sistema, aunque aumente el número de archivos.

**KISS no significa tener pocas clases: significa tener una estructura que sea fácil de comprender.**

---

## 4. Diagrama conceptual de KISS

```text
             Requisito o problema
                      |
                      v
          Comprender qué se necesita
                      |
                      v
          Identificar restricciones
                      |
                      v
          Diseñar una solución directa
                      |
                      v
       ¿Cumple todos los requisitos reales?
                 /            \
               No              Sí
               |                |
               v                v
       Ajustar la solución   Revisar su
       y volver a probar     complejidad
                                  |
                                  v
                     ¿Hay complejidad innecesaria?
                           /             \
                         Sí               No
                         |                 |
                         v                 v
                 Simplificar          Validar y
                 y volver a probar    mantener
```

El proceso no consiste en eliminar componentes indiscriminadamente. Consiste en cuestionar si cada elemento aporta un valor suficiente para justificar su existencia.

---

## 5. Ejemplo práctico en Java: validar un usuario

Supongamos que necesitamos comprobar si un usuario puede iniciar sesión.

### 5.1. Implementación innecesariamente compleja

```java
public class LoginEligibilityEvaluator {

    public boolean evaluate(User user) {
        return new EligibilityRuleExecutor()
                .execute(
                    new ActiveUserRule(),
                    user
                );
    }
}
```

Además, imaginemos que `EligibilityRuleExecutor` coordina reglas, `ActiveUserRule` contiene una única comparación y cada objeto tiene una interfaz propia.

Si la única regla existente es comprobar si el usuario está activo, esta arquitectura puede ser desproporcionada.

No es que las reglas independientes sean incorrectas. Son útiles cuando existen varias reglas intercambiables, configurables o reutilizables.

### 5.2. Implementación sencilla

```java
public class User {

    private final String username;
    private final boolean active;

    public User(String username, boolean active) {
        this.username = username;
        this.active = active;
    }

    public String getUsername() {
        return username;
    }

    public boolean isActive() {
        return active;
    }
}
```

```java
public class LoginService {

    public boolean canLogin(User user) {
        return user != null && user.isActive();
    }
}
```

La intención del método es explícita: un usuario puede iniciar sesión si existe y está activo.

La solución es fácil de leer, probar y mantener.

### 5.3. ¿Cuándo dejaría de ser suficiente?

Imaginemos que las condiciones pasan a ser:

- La cuenta debe estar activa.
- No debe estar bloqueada.
- Debe haber completado la verificación de identidad.
- No debe haber superado el límite de intentos.
- Debe cumplir políticas diferentes según el tipo de cuenta.
- Las reglas pueden cambiar según la organización.

En ese contexto, convendría modelar las reglas con métodos específicos, componentes independientes o políticas configurables.

La arquitectura adecuada debe evolucionar con la complejidad real del dominio.

---

## 6. Ejemplo práctico: evitar complejidad accidental en un método

Supongamos que necesitamos clasificar un pedido según su importe.

### 6.1. Código difícil de entender

```java
public String classifyOrder(double amount) {
    return amount < 100
            ? "BASIC"
            : amount < 500
                ? "STANDARD"
                : amount < 1000
                    ? "PREMIUM"
                    : "ENTERPRISE";
}
```

Este código puede funcionar, pero los operadores ternarios anidados hacen más difícil identificar los límites y las reglas.

### 6.2. Versión más sencilla de leer

```java
public String classifyOrder(double amount) {
    if (amount < 100) {
        return "BASIC";
    }

    if (amount < 500) {
        return "STANDARD";
    }

    if (amount < 1000) {
        return "PREMIUM";
    }

    return "ENTERPRISE";
}
```

La segunda versión ocupa más líneas, pero cada condición puede identificarse de manera independiente.

Esta es una idea importante de KISS:

**La menor cantidad de líneas no siempre produce el código más sencillo.**

### 6.3. Añadir validaciones cuando el dominio las requiera

Si el importe no puede ser negativo o debe tener un valor finito, esas restricciones deben expresarse explícitamente.

```java
public String classifyOrder(double amount) {
    if (!Double.isFinite(amount) || amount < 0) {
        throw new IllegalArgumentException(
                "El importe debe ser finito y no negativo"
        );
    }

    if (amount < 100) {
        return "BASIC";
    }

    if (amount < 500) {
        return "STANDARD";
    }

    if (amount < 1000) {
        return "PREMIUM";
    }

    return "ENTERPRISE";
}
```

La simplicidad no consiste en eliminar validaciones importantes. Consiste en expresar las reglas necesarias de forma clara.

---

## 7. KISS y el diseño orientado a objetos

KISS es compatible con la programación orientada a objetos, pero nos invita a utilizar sus mecanismos cuando aportan valor.

### 7.1. No crear interfaces sin una razón

Una interfaz puede resultar útil cuando:

- Existen varias implementaciones.
- Se necesita sustituir una dependencia externa.
- Hay un contrato importante entre módulos.
- Se requiere aislar la lógica de negocio de la infraestructura.
- La variabilidad es una parte real del dominio.
- Se necesita un punto de sustitución estable para pruebas o integraciones.

Puede resultar innecesaria cuando simplemente duplica una clase sin aportar un contrato significativo ni resolver un problema concreto.

Ejemplo sencillo:

```java
public class TaxCalculator {

    public double calculate(double amount) {
        return amount * 0.21;
    }
}
```

Si la aplicación siempre aplica el mismo tipo de impuesto y no hay requisitos de sustitución, esta clase puede ser suficiente.

Pero si existen impuestos distintos por país, categoría de producto y tipo de cliente, una abstracción puede ser adecuada:

```java
public interface TaxPolicy {

    double calculateTax(double amount);
}
```

```java
public class SpanishTaxPolicy implements TaxPolicy {

    @Override
    public double calculateTax(double amount) {
        return amount * 0.21;
    }
}
```

```java
public class ReducedTaxPolicy implements TaxPolicy {

    @Override
    public double calculateTax(double amount) {
        return amount * 0.10;
    }
}
```

Ahora la interfaz expresa una variación real del dominio. No es complejidad gratuita: permite seleccionar políticas diferentes.

### 7.2. No confundir sencillez con ausencia de diseño

Una aplicación con toda su lógica dentro de una única clase puede parecer sencilla al principio, pero volverse difícil de mantener con el tiempo.

KISS no recomienda concentrarlo todo en un archivo o método. Recomienda diseñar la estructura mínima que permita mantener las responsabilidades y las dependencias bajo control.

---

## 8. Relación con SOLID

KISS y SOLID persiguen objetivos compatibles, aunque se centran en aspectos distintos.

| Principio | Objetivo | Relación con KISS |
|---|---|---|
| SRP — Single Responsibility Principle | Una responsabilidad principal por módulo o clase | Ayuda a simplificar la comprensión y los cambios |
| OCP — Open/Closed Principle | Facilitar extensiones sin modificar continuamente el código estable | Evita modificaciones repetidas, pero no justifica crear abstracciones hipotéticas |
| LSP — Liskov Substitution Principle | Mantener la corrección al sustituir implementaciones | Permite jerarquías coherentes, sin herencias innecesarias |
| ISP — Interface Segregation Principle | Contratos pequeños y específicos | Evita interfaces excesivamente grandes |
| DIP — Dependency Inversion Principle | Depender de abstracciones apropiadas | Reduce el acoplamiento cuando existe una frontera arquitectónica útil |

### 8.1. KISS y SRP

SRP ayuda a que cada módulo tenga un propósito comprensible.

No obstante, dividir cada operación en una clase independiente puede aumentar la complejidad si las clases resultantes no representan responsabilidades significativas.

La pregunta adecuada no es «¿puedo dividir esta clase?», sino «¿esta división hace más fácil comprender y modificar el sistema?».

### 8.2. KISS y OCP

OCP favorece diseños extensibles.

Pero no es necesario crear una jerarquía de estrategias para todas las posibles variantes futuras.

Una buena solución puede comenzar con una implementación directa y extraer una abstracción cuando la variabilidad se convierta en un requisito real.

### 8.3. KISS y DIP

DIP es especialmente útil cuando la lógica de negocio necesita aislarse de una base de datos, una API externa o un servicio sustituible.

Sin embargo, añadir interfaces a todos los objetos de una aplicación no garantiza una arquitectura mejor.

La abstracción debe proteger una frontera importante, no convertirse en un trámite mecánico.

---

## 9. Relación con otros principios y buenas prácticas

### 9.1. KISS y DRY

DRY significa *Don't Repeat Yourself*: evitar duplicar conocimiento o reglas de negocio.

KISS recomienda soluciones sencillas. DRY recomienda mantener cada regla o conocimiento en un lugar apropiado.

A veces se complementan:

```java
public class DiscountCalculator {

    private static final double DISCOUNT_RATE = 0.10;

    public double calculateDiscount(double amount) {
        return amount * DISCOUNT_RATE;
    }
}
```

La regla del descuento se define una sola vez.

Sin embargo, no toda repetición visual es una duplicación de conocimiento. Dos procesos que se parecen hoy pueden tener reglas de negocio diferentes.

Crear una abstracción común demasiado pronto puede acoplarlos y complicar futuras modificaciones.

**Regla práctica:** no dupliques reglas que deben mantenerse coherentes, pero tampoco unifiques comportamientos distintos solo porque se parecen.

### 9.2. KISS y YAGNI

YAGNI significa *You Aren't Gonna Need It*: no implementes funcionalidades que no necesitas.

Es uno de los principios más relacionados con KISS.

Ejemplo: no construir un sistema de plugins cuando solo existe un caso de uso y no hay requisitos que indiquen que otros plugins serán necesarios.

YAGNI no significa ignorar necesidades previsibles y demostrables, como seguridad, observabilidad, rendimiento requerido o cumplimiento normativo.

### 9.3. KISS y Clean Code

Clean Code busca que el código sea legible, expresivo y mantenible.

KISS refuerza este objetivo al recomendar:

- Nombres descriptivos.
- Métodos con una intención clara.
- Condiciones comprensibles.
- Flujo de control sencillo.
- Pocas abstracciones innecesarias.
- Manejo explícito de errores relevantes.

### 9.4. KISS y el principio de menor sorpresa

Una API o componente debería comportarse de una manera coherente con lo que otros desarrolladores esperan.

Un método llamado `calculateTotal()` no debería enviar correos, modificar usuarios y escribir en una base de datos de forma inesperada.

Reducir las sorpresas hace que el comportamiento sea más sencillo de entender y probar.

### 9.5. KISS y separación de responsabilidades

Separar responsabilidades puede aumentar el número de clases, pero disminuir la complejidad mental de cada una.

La simplicidad se mide por la facilidad para comprender el conjunto, no solo por el número de elementos que lo componen.

---

## 10. KISS y los patrones de diseño

Los patrones de diseño son herramientas, no objetivos en sí mismos.

Un patrón puede simplificar un sistema si resuelve una dificultad concreta, pero también puede introducir complejidad si se aplica sin necesidad.

### 10.1. Cuándo un patrón ayuda

El patrón Strategy puede ser útil cuando una aplicación necesita seleccionar diferentes algoritmos de forma intercambiable.

```java
public interface ShippingStrategy {

    double calculateCost(double weight);
}
```

```java
public class StandardShipping implements ShippingStrategy {

    @Override
    public double calculateCost(double weight) {
        return 5.0 + weight * 0.5;
    }
}
```

```java
public class ExpressShipping implements ShippingStrategy {

    @Override
    public double calculateCost(double weight) {
        return 12.0 + weight * 1.2;
    }
}
```

```java
public class ShippingService {

    public double calculateCost(
            double weight,
            ShippingStrategy strategy) {

        return strategy.calculateCost(weight);
    }
}
```

Esta estructura es útil si existen varias modalidades de envío y deben poder intercambiarse.

### 10.2. Cuándo un patrón puede sobrar

Si el programa solo calcula una tarifa fija y no existe una necesidad real de cambiar el algoritmo, una clase con un método directo podría ser más clara.

No se debe preguntar únicamente «¿qué patrón puedo aplicar?».

Es preferible preguntarse:

1. ¿Qué problema de diseño estoy intentando resolver?
2. ¿Qué dificultad tiene la implementación actual?
3. ¿Qué complejidad añade el patrón?
4. ¿Qué beneficio concreto proporciona?
5. ¿El beneficio justifica esa complejidad?

### 10.3. Regla práctica

Utiliza un patrón cuando reduzca la complejidad total del diseño o permita satisfacer requisitos importantes, no simplemente porque sea conocido.

---

## 11. KISS en la arquitectura de software

KISS también se aplica a la arquitectura.

### 11.1. Arquitectura monolítica

Una aplicación monolítica modular puede ser la solución adecuada cuando:

- El equipo es pequeño.
- El dominio está relativamente bien delimitado.
- No existen necesidades independientes de escalado.
- La distribución entre servicios no aporta suficiente valor.
- La operación y el despliegue deben mantenerse sencillos.

### 11.2. Microservicios

Los microservicios pueden estar justificados cuando existen necesidades como despliegue independiente, escalado diferenciado, autonomía de equipos o límites de dominio bien definidos.

Sin esas necesidades, pueden introducir complejidad adicional:

- Comunicación de red.
- Fallos parciales.
- Consistencia distribuida.
- Observabilidad distribuida.
- Gestión de credenciales y secretos.
- Versionado de contratos.
- Automatización de despliegues.
- Mayor carga operativa.

KISS no afirma que un monolito sea siempre mejor que los microservicios. Recomienda elegir la arquitectura menos compleja que satisfaga los requisitos presentes y las restricciones previsibles.

### 11.3. Arquitectura hexagonal y Clean Architecture

Estas arquitecturas pueden reducir el acoplamiento y facilitar las pruebas, especialmente cuando existen fronteras importantes entre el dominio y la infraestructura.

Pero una implementación puede volverse innecesariamente compleja si cada operación requiere demasiadas interfaces, adaptadores, fábricas y clases de delegación.

Aplicar KISS implica adaptar la profundidad de la arquitectura a la escala y las necesidades del sistema.

---

## 12. KISS en bases de datos y APIs

### 12.1. Consultas SQL

Una consulta clara suele ser preferible a una consulta con numerosas subconsultas y transformaciones difíciles de entender, siempre que ambas resuelvan correctamente el problema y tengan un rendimiento adecuado.

No obstante, la consulta más corta no siempre es la más eficiente. Deben considerarse los planes de ejecución, los índices, el volumen de datos y las necesidades de mantenimiento.

### 12.2. APIs

Una API sencilla debería exponer operaciones coherentes y contratos fáciles de comprender.

Por ejemplo:

```http
GET /users/123
```

es una forma directa de obtener un usuario identificado por `123`.

No conviene introducir múltiples capas de envoltorios, parámetros o formatos especiales si no resuelven una necesidad real.

Por otro lado, la validación, la autorización y los contratos explícitos son complejidad necesaria cuando el sistema los requiere.

### 12.3. Configuración

No todas las constantes deben convertirse en opciones configurables.

Si un valor forma parte de una regla estable del dominio, puede ser más claro modelarlo como una constante o una política definida en el código.

Si debe cambiarse entre entornos, clientes o periodos operativos, la configuración externa puede ser la opción correcta.

---

## 13. KISS en pruebas automatizadas

Las pruebas deben comprobar comportamientos importantes con una estructura fácil de entender.

Ejemplo con JUnit 5:

```java
import static org.junit.jupiter.api.Assertions.assertTrue;
import static org.junit.jupiter.api.Assertions.assertFalse;

import org.junit.jupiter.api.Test;

class LoginServiceTest {

    private final LoginService loginService = new LoginService();

    @Test
    void activeUserCanLogin() {
        User user = new User("ana", true);

        assertTrue(loginService.canLogin(user));
    }

    @Test
    void inactiveUserCannotLogin() {
        User user = new User("ana", false);

        assertFalse(loginService.canLogin(user));
    }

    @Test
    void nullUserCannotLogin() {
        assertFalse(loginService.canLogin(null));
    }
}
```

Las pruebas son pequeñas, independientes y expresan claramente cada comportamiento.

### 13.1. Buenas prácticas

- Probar comportamientos observables.
- Utilizar nombres descriptivos.
- Evitar configuraciones de prueba innecesariamente complejas.
- No simular dependencias que no es necesario simular.
- Preferir pruebas deterministas.
- Evitar que las pruebas dependan de detalles internos irrelevantes.

### 13.2. No simplificar eliminando cobertura importante

Una prueba que omite casos límite puede ser más corta, pero no necesariamente mejor.

KISS favorece pruebas fáciles de comprender que cubran adecuadamente los riesgos y requisitos relevantes.

---

## 14. Ventajas de aplicar KISS

### 14.1. Mayor legibilidad

El equipo necesita menos esfuerzo para comprender el código y localizar el origen de un problema.

### 14.2. Menor coste de mantenimiento

Cuando la estructura es clara, los cambios suelen requerir menos modificaciones y existe menor riesgo de afectar a comportamientos no relacionados.

### 14.3. Depuración más sencilla

Un flujo de ejecución directo y unas responsabilidades claras facilitan localizar errores.

### 14.4. Pruebas más fáciles

Las funciones con responsabilidades definidas y pocas dependencias suelen ser más fáciles de probar.

### 14.5. Incorporación más rápida de desarrolladores

Un sistema comprensible reduce el tiempo necesario para aprender cómo está organizada una aplicación.

### 14.6. Menor sobreingeniería

Se reduce la tendencia a crear mecanismos genéricos, configurables o extensibles antes de que exista una necesidad concreta.

---

## 15. Riesgos de interpretar KISS incorrectamente

KISS también puede utilizarse como justificación de decisiones de diseño deficientes si se interpreta de forma demasiado superficial.

### 15.1. Confundir simplicidad con menos líneas

Una expresión compacta puede resultar más difícil de leer que varias instrucciones claras.

**Corrección:** optimiza la comprensión, no el número de caracteres.

### 15.2. Crear una única clase para toda la aplicación

Concentrar demasiadas responsabilidades puede producir una clase difícil de probar, reutilizar y modificar.

**Corrección:** separa las responsabilidades cuando esa separación facilite la comprensión y reduzca el acoplamiento.

### 15.3. Evitar todas las abstracciones

Sin abstracciones apropiadas, la lógica puede quedar acoplada a bases de datos, proveedores externos o implementaciones concretas.

**Corrección:** introduce abstracciones cuando protejan una frontera importante o resuelvan una variabilidad real.

### 15.4. Ignorar requisitos futuros demostrables

No toda planificación futura es sobreingeniería. Algunos sistemas necesitan prever compatibilidad, seguridad, escalabilidad o regulación desde el inicio.

**Corrección:** distingue entre requisitos previsibles y escenarios hipotéticos sin evidencia.

### 15.5. Eliminar validaciones para simplificar

Reducir las comprobaciones puede dejar pasar datos incorrectos o generar fallos más difíciles de diagnosticar.

**Corrección:** conserva las validaciones necesarias y haz que expresen claramente las reglas del dominio.

### 15.6. Confundir simplicidad inicial con simplicidad a largo plazo

Una solución rápida puede crear duplicaciones, acoplamiento o deuda técnica.

**Corrección:** evalúa la complejidad de todo el ciclo de vida, no solo la velocidad de la primera implementación.

---

## 16. Cómo aplicar KISS durante una revisión de código

Antes de aceptar una solución, podemos formular las siguientes preguntas:

1. ¿Se entiende rápidamente qué hace este código?
2. ¿Cada clase, método o interfaz aporta una responsabilidad o contrato claro?
3. ¿Hay abstracciones que no resuelven ningún problema actual?
4. ¿Se están contemplando requisitos hipotéticos sin una justificación concreta?
5. ¿Existe una alternativa más directa con la misma corrección y seguridad?
6. ¿Las condiciones y los flujos de control son fáciles de seguir?
7. ¿Se puede reducir el acoplamiento sin añadir una arquitectura innecesaria?
8. ¿Las dependencias externas están justificadas?
9. ¿Las pruebas verifican los requisitos importantes?
10. ¿El diseño puede evolucionar razonablemente si cambian los requisitos?

Estas preguntas no deben convertirse en una lista rígida. Su finalidad es facilitar decisiones conscientes.

---

## 17. Checklist práctico de KISS

### Diseño

- [ ] La solución aborda un requisito real.
- [ ] No existen abstracciones sin una utilidad clara.
- [ ] Los patrones de diseño resuelven problemas concretos.
- [ ] La arquitectura es proporcional a las necesidades del sistema.
- [ ] Las responsabilidades están bien delimitadas.

### Código

- [ ] Los nombres expresan la intención.
- [ ] Los métodos son fáciles de comprender.
- [ ] Las condiciones y los bucles son claros.
- [ ] No existen capas de delegación innecesarias.
- [ ] La solución no sacrifica corrección por brevedad.

### Mantenimiento

- [ ] El comportamiento importante está cubierto por pruebas.
- [ ] Los errores se manejan explícitamente.
- [ ] La configuración responde a una necesidad real.
- [ ] La solución puede evolucionar sin complejidad desproporcionada.
- [ ] Se han considerado los riesgos de seguridad y operación.

---

## 18. Diferencias entre KISS, DRY, YAGNI y SOLID

Aunque están relacionados, cada uno aborda un aspecto diferente del diseño.

| Principio | Pregunta principal |
|---|---|
| KISS | ¿Podemos resolver el problema de una forma más sencilla y comprensible? |
| DRY | ¿Estamos duplicando una regla o conocimiento que debería mantenerse en un único lugar? |
| YAGNI | ¿Estamos implementando algo que realmente no necesitamos todavía? |
| SOLID | ¿La estructura del código favorece responsabilidades claras, dependencias apropiadas y evolución segura? |
| Clean Code | ¿El código es legible, expresivo y fácil de mantener? |

Estos principios deben utilizarse conjuntamente, evitando aplicarlos como reglas absolutas.

Por ejemplo, eliminar una abstracción puede simplificar el código y cumplir KISS, pero no siempre es correcto si esa abstracción aísla una dependencia externa importante. Del mismo modo, eliminar una duplicación puede cumplir DRY, pero resultar contraproducente si une comportamientos que deben evolucionar por separado.

---

## 19. Estrategia para refactorizar aplicando KISS

Cuando una implementación parece demasiado compleja, es conveniente simplificarla de forma incremental.

### Paso 1. Comprender el comportamiento existente

Identifica los requisitos, las restricciones y los casos límite. No elimines componentes sin entender su finalidad.

### Paso 2. Crear o revisar las pruebas

Las pruebas permiten comprobar que la simplificación conserva el comportamiento esperado.

### Paso 3. Identificar complejidad accidental

Busca abstracciones redundantes, métodos difíciles de leer, dependencias innecesarias y mecanismos de configuración sin una necesidad demostrable.

### Paso 4. Simplificar un elemento cada vez

Por ejemplo, sustituye una condición excesivamente compleja por varias condiciones explícitas, o elimina una capa que solo delega una llamada y no aporta una frontera útil.

### Paso 5. Ejecutar las pruebas

Comprueba que los cambios no alteran los comportamientos requeridos.

### Paso 6. Revisar el resultado

Verifica que el código final sea más comprensible y no simplemente más corto.

### Paso 7. Documentar las decisiones no evidentes

Si una solución necesita una abstracción o una restricción aparentemente compleja, explica brevemente la razón cuando no resulte evidente a partir del código.

---

## 20. Conclusión

KISS es uno de los principios fundamentales para construir software mantenible porque pone el foco en algo esencial: **la complejidad tiene un coste y debe estar justificada**.

Aplicarlo no significa evitar la orientación a objetos, eliminar las interfaces, renunciar a los patrones de diseño o escribir todas las funcionalidades en una única clase.

Significa:

- Resolver el problema real con una solución proporcional.
- Evitar abstracciones prematuras y mecanismos innecesarios.
- Priorizar la claridad frente a la brevedad artificial.
- Mantener las responsabilidades comprensibles.
- Introducir complejidad cuando los requisitos la justifiquen.
- Conservar las validaciones, las pruebas y las medidas de seguridad necesarias.
- Revisar periódicamente si el diseño sigue siendo apropiado para el problema actual.

La mejor solución no es necesariamente la más corta ni la más sofisticada. Es la que satisface correctamente los requisitos con una complejidad razonable y permite que el software continúe evolucionando.

---

## 21. Referencias y lecturas recomendadas

- **The Pragmatic Programmer**, de Andrew Hunt y David Thomas: principios prácticos para desarrollar y mantener software.
- **Clean Code**, de Robert C. Martin: legibilidad, nombres, funciones y organización del código.
- **Refactoring**, de Martin Fowler: técnicas para mejorar el diseño de una base de código existente.
- **Design Patterns: Elements of Reusable Object-Oriented Software**, de Erich Gamma, Richard Helm, Ralph Johnson y John Vlissides: catálogo clásico de patrones de diseño.
- **SOLID Principles**: principios para organizar responsabilidades, contratos y dependencias en el diseño orientado a objetos.

Estas lecturas complementan KISS y ayudan a decidir cuándo una solución sencilla es suficiente y cuándo una abstracción o estructura adicional está justificada.
