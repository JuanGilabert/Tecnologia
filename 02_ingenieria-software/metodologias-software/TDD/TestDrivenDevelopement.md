# TDD (Test-Driven Development)
## Desarrollo guiado por pruebas en ingeniería del software

## 1. Introducción

**TDD (Test-Driven Development)**, o **Desarrollo Guiado por Pruebas**, es una metodología de desarrollo de software en la que las pruebas automatizadas se escriben antes que el código de producción.

Su objetivo es construir software de manera incremental, verificando continuamente que cada funcionalidad cumple los requisitos definidos y que los cambios no rompen comportamientos existentes.

TDD no consiste simplemente en escribir muchas pruebas, sino en utilizar las pruebas como guía para diseñar e implementar el software.

La metodología se popularizó gracias a las prácticas de Extreme Programming (XP) y está estrechamente relacionada con el desarrollo ágil, la refactorización continua y el diseño de software mantenible.

Su ciclo fundamental es:

**Red → Green → Refactor**

1. **Red:** escribir una prueba que falle porque la funcionalidad todavía no existe o no está implementada correctamente.
2. **Green:** escribir el código mínimo necesario para que la prueba pase.
3. **Refactor:** mejorar el diseño y la estructura del código sin modificar su comportamiento observable.

Este ciclo se repite durante todo el desarrollo.

---

## 2. ¿Qué problema resuelve TDD?

En un enfoque tradicional, un desarrollador suele implementar una funcionalidad y después escribe pruebas para comprobar si funciona.

Este proceso puede presentar varios problemas:

- El código se diseña sin una definición suficientemente precisa de su comportamiento esperado.
- Las pruebas se escriben demasiado tarde o no llegan a escribirse.
- Los errores se descubren cuando la funcionalidad ya está integrada con otros componentes.
- El código puede acumular dependencias difíciles de sustituir o simular durante las pruebas.
- Los cambios posteriores pueden romper funcionalidades que anteriormente funcionaban.
- Las pruebas pueden terminar adaptándose a la implementación existente en lugar de verificar los requisitos reales.

TDD invierte el orden habitual: primero define un comportamiento verificable, después lo implementa y finalmente mejora su diseño.

### Ejemplo conceptual

Supongamos que necesitamos implementar un método que calcule el descuento aplicable a una compra.

En un desarrollo sin TDD, podríamos escribir directamente el método y probarlo al final.

Con TDD, primero definimos los comportamientos esperados:

- Una compra de 100 € sin descuento debe mantener su importe.
- Una compra de 100 € con un descuento del 10 % debe costar 90 €.
- Un porcentaje de descuento negativo debe rechazarse.
- Un descuento superior al 100 % debe rechazarse.

Estas expectativas se convierten en pruebas ejecutables que guían la implementación.

---

## 3. Principios fundamentales de TDD

### 3.1. Escribir primero una prueba que falle

La prueba debe representar una necesidad concreta y todavía no satisfecha.

Esto permite comprobar que la prueba detecta realmente la ausencia de la funcionalidad.

Una prueba que pasa desde el primer momento podría no estar verificando el comportamiento que se pretende implementar.

### 3.2. Implementar únicamente lo necesario

Una vez que la prueba falla por el motivo esperado, se desarrolla la solución mínima que permite superarla.

No se recomienda anticipar funcionalidades futuras que todavía no están justificadas por los requisitos o por las pruebas actuales.

### 3.3. Refactorizar continuamente

Cuando las pruebas pasan, se puede mejorar el código:

- Eliminar duplicaciones.
- Simplificar condiciones.
- Mejorar nombres.
- Extraer responsabilidades.
- Reducir dependencias innecesarias.
- Aplicar patrones de diseño cuando aporten valor.

La suite de pruebas proporciona una red de seguridad para comprobar que estas mejoras no alteran el comportamiento esperado.

### 3.4. Mantener ciclos pequeños

Cada iteración debe resolver un comportamiento concreto y verificable.

Los ciclos pequeños facilitan la identificación de errores, reducen el coste de depuración y permiten avanzar con mayor seguridad.

### 3.5. Considerar las pruebas como especificaciones ejecutables

Una prueba bien escrita documenta qué debe hacer el sistema ante determinadas entradas y condiciones.

Por ejemplo:

`deberiaAplicarDescuentoDelDiezPorCientoCuandoLaCompraEsValida()`

Este nombre comunica el comportamiento esperado sin necesidad de leer toda la implementación.

---

## 4. El ciclo Red-Green-Refactor

<diagram-placeholder>
[Red] Escribir una prueba que falla
   ↓
[Green] Implementar el código mínimo
   ↓
[Refactor] Mejorar el diseño
   ↓
[Red] Añadir la siguiente prueba
</diagram-placeholder>

### Fase 1. Red: prueba fallida

Se escribe una prueba automatizada que describa el comportamiento esperado.

La prueba debe fallar por una razón válida: la funcionalidad no existe, devuelve un resultado incorrecto o no satisface la condición establecida.

**Objetivo:** demostrar que la prueba detecta la carencia que se quiere resolver.

### Fase 2. Green: prueba satisfactoria

Se implementa la menor cantidad de código necesaria para que la prueba pase.

En esta fase se prioriza satisfacer el comportamiento especificado, sin intentar resolver de antemano todos los casos futuros.

**Objetivo:** disponer de una implementación funcional para el comportamiento probado.

### Fase 3. Refactor: mejora del diseño

Se revisa el código de producción y, cuando corresponda, también la estructura de las pruebas.

Se pueden extraer métodos, reducir duplicaciones, mejorar la legibilidad o eliminar complejidad accidental.

Después de cada cambio, se ejecutan nuevamente las pruebas.

**Objetivo:** mejorar la calidad interna manteniendo el comportamiento externo.

### Importante

No es necesario escribir toda la suite de pruebas antes de empezar la implementación. En TDD se avanza mediante pequeños ciclos, añadiendo una prueba cada vez o un pequeño conjunto de pruebas relacionadas cuando resulte razonable.

---

## 5. Ejemplo completo de TDD en Java

Para los ejemplos se utilizará Java con JUnit 5.

### 5.1. Requisito funcional

Desarrollar una clase `CalculadoraDescuentos` que permita calcular el precio final de una compra aplicando un porcentaje de descuento.

Reglas:

1. El precio original debe ser mayor o igual que cero.
2. El porcentaje de descuento debe estar entre 0 y 100, ambos incluidos.
3. El descuento se calcula sobre el precio original.
4. El resultado representa el precio después de aplicar el descuento.

### 5.2. Configuración de Maven

En el archivo `pom.xml` se puede incluir JUnit 5 como dependencia de pruebas:

```xml
<dependencies>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>5.11.4</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

Para ejecutar las pruebas con Maven, configura una versión compatible de `maven-surefire-plugin` si la versión de Maven o del proyecto lo requiere.

### 5.3. Primera prueba: sin descuento

Primero definimos el comportamiento más sencillo.

Archivo: `CalculadoraDescuentosTest.java`

```java
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertEquals;

class CalculadoraDescuentosTest {

    @Test
    void deberiaMantenerElPrecioCuandoElDescuentoEsCero() {
        CalculadoraDescuentos calculadora =
                new CalculadoraDescuentos();

        double resultado =
                calculadora.calcularPrecioFinal(100.0, 0.0);

        assertEquals(100.0, resultado, 0.001);
    }
}
```

En este momento, la clase `CalculadoraDescuentos` todavía no existe.

La prueba no compila. En el sentido práctico de TDD, todavía no tenemos una prueba ejecutable que falle en tiempo de ejecución; primero debemos crear el esqueleto mínimo para poder ejecutarla.

### 5.4. Implementación mínima

Creamos la clase de producción:

Archivo: `CalculadoraDescuentos.java`

```java
public class CalculadoraDescuentos {

    public double calcularPrecioFinal(
            double precioOriginal,
            double porcentajeDescuento) {

        return precioOriginal;
    }
}
```

Ahora la prueba compila y pasa.

Sin embargo, la implementación todavía no aplica ningún descuento. La siguiente prueba permitirá detectar esta carencia.

### 5.5. Segunda prueba: aplicar un descuento

Añadimos:

```java
@Test
void deberiaAplicarUnDescuentoDelDiezPorCiento() {
    CalculadoraDescuentos calculadora =
            new CalculadoraDescuentos();

    double resultado =
            calculadora.calcularPrecioFinal(100.0, 10.0);

    assertEquals(90.0, resultado, 0.001);
}
```

Esta prueba falla con la implementación anterior, porque devuelve 100 en lugar de 90.

Ahora tenemos una prueba ejecutable que falla por el comportamiento esperado.

### 5.6. Implementación necesaria para pasar la prueba

Modificamos el método:

```java
public class CalculadoraDescuentos {

    public double calcularPrecioFinal(
            double precioOriginal,
            double porcentajeDescuento) {

        double descuento =
                precioOriginal * porcentajeDescuento / 100.0;

        return precioOriginal - descuento;
    }
}
```

Las dos pruebas pasan.

El comportamiento ya está implementado, pero todavía faltan las reglas de validación.

### 5.7. Tercera prueba: precio negativo

Añadimos una prueba para establecer cómo debe comportarse el método cuando el precio no es válido:

```java
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertThrows;

@Test
void deberiaRechazarUnPrecioNegativo() {
    CalculadoraDescuentos calculadora =
            new CalculadoraDescuentos();

    assertThrows(
            IllegalArgumentException.class,
            () -> calculadora.calcularPrecioFinal(-10.0, 10.0)
    );
}
```

Esta prueba falla inicialmente porque el método no valida el precio.

Implementamos la validación:

```java
public class CalculadoraDescuentos {

    public double calcularPrecioFinal(
            double precioOriginal,
            double porcentajeDescuento) {

        if (!Double.isFinite(precioOriginal)
                || precioOriginal < 0) {
            throw new IllegalArgumentException(
                    "El precio debe ser finito y no negativo"
            );
        }

        double descuento =
                precioOriginal * porcentajeDescuento / 100.0;

        return precioOriginal - descuento;
    }
}
```

La nueva prueba pasa. Sin embargo, la implementación todavía no valida el porcentaje de descuento.

### 5.8. Cuarta prueba: porcentaje no válido

```java
@Test
void deberiaRechazarUnDescuentoSuperiorAlCienPorCien() {
    CalculadoraDescuentos calculadora =
            new CalculadoraDescuentos();

    assertThrows(
            IllegalArgumentException.class,
            () -> calculadora.calcularPrecioFinal(100.0, 120.0)
    );
}
```

Para que esta prueba pase, añadimos la validación correspondiente.

### 5.9. Implementación final después de los ciclos

```java
public class CalculadoraDescuentos {

    public double calcularPrecioFinal(
            double precioOriginal,
            double porcentajeDescuento) {

        if (!Double.isFinite(precioOriginal)
                || precioOriginal < 0) {
            throw new IllegalArgumentException(
                    "El precio debe ser finito y no negativo"
            );
        }

        if (!Double.isFinite(porcentajeDescuento)
                || porcentajeDescuento < 0
                || porcentajeDescuento > 100) {
            throw new IllegalArgumentException(
                    "El descuento debe estar entre 0 y 100"
            );
        }

        double descuento =
                precioOriginal * porcentajeDescuento / 100.0;

        return precioOriginal - descuento;
    }
}
```

### 5.10. Suite de pruebas completa

En el archivo de pruebas, reunimos los comportamientos relevantes:

```java
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertThrows;

class CalculadoraDescuentosTest {

    private final CalculadoraDescuentos calculadora =
            new CalculadoraDescuentos();

    @Test
    void deberiaMantenerElPrecioCuandoElDescuentoEsCero() {
        assertEquals(
                100.0,
                calculadora.calcularPrecioFinal(100.0, 0.0),
                0.001
        );
    }

    @Test
    void deberiaAplicarUnDescuentoDelDiezPorCiento() {
        assertEquals(
                90.0,
                calculadora.calcularPrecioFinal(100.0, 10.0),
                0.001
        );
    }

    @Test
    void deberiaPermitirUnDescuentoDelCienPorCien() {
        assertEquals(
                0.0,
                calculadora.calcularPrecioFinal(100.0, 100.0),
                0.001
        );
    }

    @Test
    void deberiaRechazarUnPrecioNegativo() {
        assertThrows(
                IllegalArgumentException.class,
                () -> calculadora.calcularPrecioFinal(-10.0, 10.0)
        );
    }

    @Test
    void deberiaRechazarUnDescuentoSuperiorAlCienPorCien() {
        assertThrows(
                IllegalArgumentException.class,
                () -> calculadora.calcularPrecioFinal(100.0, 120.0)
        );
    }

    @Test
    void deberiaRechazarUnDescuentoNegativo() {
        assertThrows(
                IllegalArgumentException.class,
                () -> calculadora.calcularPrecioFinal(100.0, -5.0)
        );
    }
}
```

Para una aplicación financiera real, conviene utilizar `BigDecimal` con una política explícita de redondeo en lugar de `double`, ya que los números de punto flotante pueden introducir pequeñas imprecisiones decimales.

Este ejemplo ilustra el ciclo TDD; no pretende ser una implementación monetaria completa.

---

## 6. ¿Qué ocurre durante la refactorización?

Una vez que las pruebas pasan, revisamos la calidad del código.

Por ejemplo, podemos extraer las validaciones a métodos privados si la clase crece y esa separación mejora su legibilidad.

```java
public class CalculadoraDescuentos {

    public double calcularPrecioFinal(
            double precioOriginal,
            double porcentajeDescuento) {

        validarPrecio(precioOriginal);
        validarDescuento(porcentajeDescuento);

        return precioOriginal
                * (1 - porcentajeDescuento / 100.0);
    }

    private void validarPrecio(double precio) {
        if (!Double.isFinite(precio) || precio < 0) {
            throw new IllegalArgumentException(
                    "Precio no válido"
            );
        }
    }

    private void validarDescuento(double descuento) {
        if (!Double.isFinite(descuento)
                || descuento < 0
                || descuento > 100) {
            throw new IllegalArgumentException(
                    "Descuento no válido"
            );
        }
    }
}
```

Esta refactorización mantiene el contrato observable: los casos válidos producen los mismos resultados y los casos no válidos siguen provocando una excepción.

Después de refactorizar, ejecutamos de nuevo toda la suite de pruebas.

**Regla fundamental:** la refactorización no debe cambiar el comportamiento observable. Si se necesita cambiarlo, primero debe definirse y probarse el nuevo comportamiento.

---

## 7. Estructura de un proyecto Java con TDD

Una organización habitual para un proyecto Maven es:

```text
mi-proyecto/
├── pom.xml
└── src/
    ├── main/
    │   └── java/
    │       └── com/ejemplo/
    │           └── CalculadoraDescuentos.java
    └── test/
        └── java/
            └── com/ejemplo/
                └── CalculadoraDescuentosTest.java
```

- `src/main/java`: contiene el código de producción.
- `src/test/java`: contiene las pruebas automatizadas.
- `pom.xml`: declara dependencias y configuración del proyecto.

Para ejecutar las pruebas:

```bash
mvn test
```

En un proyecto real, la estructura de paquetes de las pruebas suele reflejar la de producción, aunque pueden existir diferencias justificadas por la arquitectura.

---

## 8. Tipos de pruebas relacionadas con TDD

TDD no es un tipo concreto de prueba, sino una disciplina de desarrollo que puede aplicarse a diferentes niveles.

### 8.1. Pruebas unitarias

Comprueban el comportamiento de una unidad pequeña de software, normalmente una clase o función, aislando las dependencias cuando resulta apropiado.

Son especialmente frecuentes en TDD porque permiten ciclos rápidos de ejecución.

Ejemplo: comprobar el cálculo de un descuento sin acceder a una base de datos.

### 8.2. Pruebas de integración

Verifican la colaboración entre componentes reales, como un repositorio y una base de datos.

También pueden escribirse primero, aunque suelen ser más lentas y requieren más configuración.

Ejemplo: comprobar que un repositorio persiste y recupera correctamente un pedido en una base de datos de pruebas.

### 8.3. Pruebas de aceptación

Verifican comportamientos expresados desde la perspectiva del usuario o del negocio.

Ejemplo: un cliente aplica un cupón válido y el total de su pedido se actualiza correctamente.

### 8.4. Pruebas de contrato

Verifican que dos componentes o servicios cumplen un contrato de interacción acordado.

Son útiles en sistemas distribuidos y arquitecturas de microservicios.

### 8.5. Relación entre los niveles

No existe una proporción universal obligatoria, pero suele resultar práctico disponer de muchas pruebas unitarias rápidas, pruebas de integración selectivas y pruebas de aceptación que cubran los flujos críticos del negocio.

No todas las pruebas deben ejecutarse con la misma frecuencia ni tener el mismo coste.

---

## 9. TDD y el enfoque AAA

Un patrón habitual para organizar las pruebas es **AAA (Arrange, Act, Assert)**.

- **Arrange:** preparar los datos y las dependencias.
- **Act:** ejecutar la operación que se quiere verificar.
- **Assert:** comprobar el resultado esperado.

Ejemplo:

```java
@Test
void deberiaAplicarUnDescuentoDelDiezPorCiento() {

    // Arrange
    CalculadoraDescuentos calculadora =
            new CalculadoraDescuentos();

    // Act
    double resultado =
            calculadora.calcularPrecioFinal(100.0, 10.0);

    // Assert
    assertEquals(90.0, resultado, 0.001);
}
```

AAA es una convención para estructurar pruebas, no una fase adicional del ciclo Red-Green-Refactor.

Su ventaja principal es que hace explícitos el contexto, la acción y el resultado esperado.

---

## 10. TDD, BDD y SDD: diferencias y complementariedad

Estas tres prácticas pueden combinarse, pero resuelven problemas distintos.

| Aspecto | TDD | BDD | SDD |
|---|---|---|---|
| Significado | Test-Driven Development | Behavior-Driven Development | Spec-Driven Development |
| Enfoque | Desarrollo guiado por pruebas | Comportamiento compartido entre negocio y tecnología | Desarrollo guiado por especificaciones |
| Pregunta principal | ¿Qué comportamiento debe verificar esta prueba? | ¿Cómo debe comportarse el sistema desde la perspectiva del negocio? | ¿Qué debe construir el sistema según la especificación? |
| Artefacto habitual | Pruebas automatizadas | Escenarios de comportamiento y criterios de aceptación | Especificaciones, requisitos y criterios verificables |
| Participantes | Principalmente desarrollo y QA | Negocio, producto, QA y desarrollo | Producto, negocio, arquitectura y desarrollo |
| Alcance | Frecuentemente código y componentes | Flujos de negocio y comportamiento | Puede abarcar requisitos, arquitectura, implementación y validación |

### Cómo se complementan

Un flujo posible sería:

1. **SDD:** definir la especificación de la funcionalidad.
2. **BDD:** expresar ejemplos de comportamiento y criterios de aceptación.
3. **TDD:** implementar cada comportamiento mediante ciclos Red-Green-Refactor.
4. **Integración continua:** ejecutar las pruebas y verificar que los cambios cumplen los requisitos.
5. **Revisión de aceptación:** comprobar que la solución satisface las necesidades del negocio.

No es obligatorio utilizar las tres metodologías. La combinación depende del tamaño del proyecto, el equipo y el nivel de formalidad requerido.

---

## 11. Relación entre TDD y SOLID

TDD y SOLID son complementarios.

**TDD ayuda a descubrir problemas de diseño a través del proceso de implementación; SOLID aporta principios para organizar las responsabilidades y dependencias.**

### 11.1. S — Single Responsibility Principle

Una clase con demasiadas responsabilidades suele ser difícil de probar.

TDD puede hacer visibles estas dificultades cuando una prueba necesita configurar numerosos elementos no relacionados con el comportamiento que se desea verificar.

La respuesta no siempre es dividir la clase. Primero hay que comprobar si las responsabilidades realmente son independientes.

### 11.2. O — Open/Closed Principle

Cuando una nueva regla de negocio exige modificar muchos condicionales, las pruebas existentes ayudan a proteger los comportamientos anteriores.

Si aparecen variaciones frecuentes, puede resultar apropiado utilizar estrategias intercambiables o políticas independientes.

### 11.3. L — Liskov Substitution Principle

Las pruebas pueden verificar que las implementaciones de una abstracción cumplen su contrato.

Por ejemplo, distintas implementaciones de un servicio de almacenamiento deberían respetar las mismas reglas funcionales acordadas.

### 11.4. I — Interface Segregation Principle

Las pruebas difíciles de preparar pueden revelar que una clase depende de una interfaz demasiado amplia.

Dividir interfaces puede reducir el acoplamiento, siempre que la separación represente responsabilidades reales.

### 11.5. D — Dependency Inversion Principle

TDD suele beneficiarse de dependencias sustituibles.

Por ejemplo, una aplicación puede depender de una interfaz de repositorio y utilizar una implementación en memoria durante las pruebas unitarias, mientras que producción utiliza una base de datos real.

Esto no significa que cada clase deba tener una interfaz propia. Las abstracciones se justifican cuando aportan una frontera útil, una variación real o un aislamiento necesario.

---

## 12. TDD, Clean Code, DRY, KISS y YAGNI

### 12.1. TDD y Clean Code

TDD ayuda a construir una suite de pruebas que documenta los comportamientos del sistema. Clean Code orienta la legibilidad y mantenibilidad del código de producción y de las pruebas.

Una prueba debería ser comprensible sin exigir que el lector reconstruya mentalmente demasiados pasos.

Buenas prácticas:

- Nombres descriptivos.
- Una intención principal por prueba.
- Preparación sencilla.
- Aserciones relacionadas con el comportamiento probado.
- Evitar lógica innecesaria dentro de las pruebas.

### 12.2. TDD y DRY

DRY busca evitar duplicaciones de conocimiento o reglas de negocio.

En las pruebas puede resultar útil compartir datos de preparación, constructores de objetos de prueba o métodos auxiliares.

Sin embargo, no conviene abstraer cada repetición de inmediato. Dos pruebas que parecen similares pueden representar reglas de negocio diferentes.

La claridad de las pruebas es más importante que eliminar toda repetición textual.

### 12.3. TDD y KISS

KISS recomienda soluciones simples.

En TDD, la fase Green invita a implementar lo mínimo necesario para satisfacer la prueba actual. La fase Refactor permite mejorar esa solución sin introducir complejidad injustificada.

### 12.4. TDD y YAGNI

YAGNI recomienda no construir funcionalidades anticipadas que no estén justificadas.

TDD ayuda a aplicar este principio porque cada incremento de código responde a un comportamiento que debe verificarse.

No obstante, esto no implica ignorar requisitos no funcionales, seguridad, accesibilidad o necesidades arquitectónicas conocidas.

---

## 13. Diseño de buenas pruebas

Una suite útil debe ser fiable, legible y suficientemente sensible a los errores importantes.

### 13.1. Verificar comportamientos, no detalles internos

Es preferible comprobar resultados observables y contratos públicos en lugar de exigir que el código utilice una estructura interna concreta.

Las pruebas excesivamente ligadas a la implementación pueden fallar tras una refactorización que no cambia el comportamiento.

### 13.2. Evitar pruebas frágiles

Una prueba frágil falla por cambios irrelevantes.

Ejemplos:

- Depender de la hora real del sistema.
- Utilizar servicios externos sin control.
- Asumir un orden de ejecución que no está garantizado.
- Compartir estado mutable entre pruebas.
- Comparar representaciones internas que no forman parte del contrato.

### 13.3. Comprobar límites y casos extremos

Además del caso habitual, conviene probar:

- Valores mínimos y máximos.
- Entradas vacías.
- Datos nulos cuando el contrato los permite o exige rechazarlos.
- Errores previsibles.
- Estados no válidos.
- Valores fuera de rango.

La selección de casos debe derivarse de los requisitos, no de una búsqueda indiscriminada de combinaciones.

### 13.4. Evitar pruebas que solo comprueban que no hay errores

Una prueba que invoca un método y no verifica ningún resultado puede pasar incluso cuando el método produce un resultado incorrecto.

Cuando sea pertinente, deben comprobarse el resultado, el estado final, la excepción o la interacción contractual esperada.

### 13.5. No utilizar mocks indiscriminadamente

Los mocks y stubs permiten sustituir dependencias, pero su uso excesivo puede generar pruebas acopladas a detalles internos.

Una guía práctica:

- Utilizar objetos reales cuando sean sencillos y seguros.
- Usar stubs para proporcionar respuestas controladas.
- Usar mocks cuando sea importante verificar una interacción contractual.
- Reservar las pruebas de integración para validar la colaboración real entre componentes.

---

## 14. TDD y refactorización segura

Refactorizar significa cambiar la estructura interna del software sin modificar su comportamiento observable.

Ejemplos:

- Extraer un método.
- Cambiar el nombre de una variable.
- Eliminar duplicaciones.
- Separar responsabilidades.
- Introducir una abstracción justificada.
- Simplificar un algoritmo.

El proceso recomendado es:

1. Ejecutar la suite y comprobar que está verde.
2. Identificar una mejora concreta.
3. Realizar un cambio pequeño.
4. Ejecutar las pruebas relevantes.
5. Ejecutar la suite completa.
6. Revisar que la mejora ha simplificado el diseño.

Las pruebas no garantizan la ausencia de defectos. Su capacidad de protección depende de la calidad de los casos, las aserciones y los requisitos que cubren.

Por eso, la refactorización debe combinar pruebas, revisión de código y criterio técnico.

---

## 15. TDD en arquitecturas modernas

### 15.1. Arquitectura hexagonal

En arquitectura hexagonal, el dominio puede probarse sin iniciar una base de datos, un servidor web o un sistema de mensajería.

Los puertos definen contratos y los adaptadores conectan el sistema con tecnologías externas.

TDD resulta especialmente útil para implementar primero las reglas de dominio y después verificar los adaptadores mediante pruebas de integración.

### 15.2. Clean Architecture

Las reglas de negocio pueden probarse de forma aislada de la infraestructura.

Por ejemplo, un caso de uso que registra un pedido puede depender de una abstracción de repositorio y recibirla mediante su constructor.

Las pruebas unitarias pueden verificar las reglas de aplicación sin iniciar toda la infraestructura.

### 15.3. Microservicios

TDD puede ayudar a implementar la lógica de cada servicio, mientras que las pruebas de integración, contrato y extremo a extremo comprueban las interacciones distribuidas.

No conviene intentar cubrir todo un sistema de microservicios exclusivamente con pruebas unitarias: estas no verifican por sí solas la compatibilidad real entre servicios.

### 15.4. Aplicaciones orientadas a eventos

Es posible desarrollar primero pruebas para los eventos que se publican, los cambios de estado que se producen y las condiciones que deben cumplirse al procesarlos.

También deben contemplarse, según el sistema, los duplicados, el orden de los mensajes, los reintentos y la idempotencia.

---

## 16. TDD en integración continua y entrega continua

TDD funciona especialmente bien cuando las pruebas se ejecutan de forma automatizada en un pipeline de integración continua (CI).

Un flujo típico es:

```text
Desarrollador
     |
     v
Escribe una prueba
     |
     v
Implementa la funcionalidad
     |
     v
Ejecuta las pruebas localmente
     |
     v
Commit / Pull Request
     |
     v
Pipeline de CI
     |
     +--> Compilación
     |
     +--> Pruebas unitarias
     |
     +--> Pruebas de integración
     |
     +--> Análisis estático
     |
     v
Revisión y posible integración
```

La configuración concreta depende del proyecto. Las pruebas lentas, las pruebas con infraestructura externa y las comprobaciones de extremo a extremo pueden ejecutarse en etapas distintas.

La integración continua no sustituye a TDD, pero ayuda a mantener la disciplina de verificación durante el trabajo colaborativo.

---

## 17. Ventajas de TDD

### 17.1. Detección temprana de errores

Los errores se identifican cerca del momento en que se introduce el comportamiento defectuoso, lo que facilita su diagnóstico.

### 17.2. Mayor confianza en los cambios

Una suite de pruebas útil permite comprobar con rapidez que los comportamientos anteriores siguen funcionando.

### 17.3. Diseño más verificable

Las dificultades para probar una clase pueden revelar responsabilidades excesivas, dependencias rígidas o interfaces poco adecuadas.

No todas las dificultades implican un mal diseño, pero sí constituyen una señal que merece revisión.

### 17.4. Documentación ejecutable

Las pruebas muestran ejemplos concretos de cómo debe comportarse el sistema.

### 17.5. Refactorización con menor riesgo

Las pruebas reducen el riesgo de modificar accidentalmente un comportamiento que ya estaba implementado.

### 17.6. Feedback frecuente

Los ciclos cortos permiten validar decisiones técnicas sin esperar a que una funcionalidad completa esté terminada.

---

## 18. Desventajas y limitaciones de TDD

### 18.1. Curva de aprendizaje

El equipo necesita aprender a formular pruebas útiles, elegir buenos límites de aislamiento y diseñar incrementos pequeños.

### 18.2. Inversión inicial de tiempo

Es necesario escribir y mantener las pruebas, lo que puede ralentizar determinadas tareas al principio.

El balance depende de la complejidad del sistema, la estabilidad de los requisitos y la experiencia del equipo.

### 18.3. Falsa sensación de seguridad

Una cobertura elevada no garantiza que se estén verificando los comportamientos importantes.

Es posible ejecutar muchas pruebas que comprueban poco o que repiten la misma expectativa.

### 18.4. Pruebas acopladas a la implementación

Si las pruebas inspeccionan demasiados detalles internos, pueden dificultar cambios legítimos en el diseño.

### 18.5. Dificultades con sistemas externos

Las bases de datos, las interfaces gráficas, los servicios de terceros y los sistemas distribuidos pueden requerir pruebas adicionales y entornos controlados.

### 18.6. No es una solución universal

TDD no sustituye el análisis de requisitos, las revisiones de código, las pruebas exploratorias, las pruebas de seguridad ni la validación con usuarios.

Puede resultar menos rentable para prototipos desechables o exploraciones cuya finalidad sea aprender y que todavía no tengan un comportamiento estable.

---

## 19. Errores habituales al aplicar TDD

| Error | Consecuencia | Alternativa |
|---|---|---|
| Escribir primero toda la implementación | Se pierde el feedback incremental | Trabajar en pequeños ciclos |
| Escribir pruebas que siempre pasan | No se demuestra que detectan la carencia | Comprobar que fallan por la razón esperada |
| Implementar demasiado en Green | Se introduce complejidad innecesaria | Resolver el comportamiento actual |
| Omitir la refactorización | Se acumula deuda técnica | Revisar el diseño cuando las pruebas pasan |
| Probar detalles internos | Las pruebas se vuelven frágiles | Verificar comportamientos y contratos |
| Utilizar mocks para todo | Se puede simular un sistema que en realidad no funciona | Combinar pruebas aisladas y de integración |
| Buscar cobertura del 100 % a cualquier precio | Se invierte esfuerzo en pruebas de poco valor | Priorizar riesgos y comportamientos importantes |
| Confundir pruebas con requisitos | Se pueden codificar expectativas equivocadas | Validar primero la especificación |
| No ejecutar la suite completa | Pueden quedar regresiones fuera del alcance de la prueba actual | Automatizar la verificación en CI |

---

## 20. Buenas prácticas profesionales

1. **Empezar por un comportamiento concreto.** Evitar pruebas demasiado grandes que mezclen muchas reglas.
2. **Nombrar las pruebas por su intención.** El nombre debería comunicar la condición y el resultado esperado.
3. **Mantener cada prueba independiente.** Evitar que el resultado dependa del orden de ejecución.
4. **Controlar las dependencias externas.** Utilizar dobles de prueba o infraestructura de pruebas cuando sea apropiado.
5. **Probar los límites del dominio.** Los casos extremos suelen descubrir errores importantes.
6. **Refactorizar con frecuencia.** No esperar a que el código sea difícil de mantener.
7. **Mantener una suite rápida.** Una prueba lenta reduce la frecuencia con la que el equipo obtiene feedback.
8. **Automatizar la ejecución.** Integrar las pruebas en el proceso de desarrollo y en CI.
9. **Revisar la calidad de las aserciones.** Una prueba debe fallar cuando el comportamiento relevante es incorrecto.
10. **No confundir cobertura con calidad.** Evaluar también el valor de las pruebas y los riesgos que cubren.
11. **Separar pruebas unitarias y de integración.** Cada nivel debe verificar el tipo de garantía para el que está diseñado.
12. **Evitar sobrearquitectura.** Las abstracciones deben responder a necesidades reales del diseño.
13. **Mantener las pruebas como código de producción.** Aplicarles legibilidad, estructura y mantenimiento.
14. **Revisar las especificaciones.** Una prueba puede ser correcta técnicamente y aun así verificar una regla de negocio equivocada.

---

## 21. Lista de comprobación para aplicar TDD

### Antes de implementar

- [ ] ¿El comportamiento esperado está definido?
- [ ] ¿Existe una prueba que representa una necesidad concreta?
- [ ] ¿La prueba falla por el motivo esperado?
- [ ] ¿El caso probado es suficientemente pequeño?

### Durante la implementación

- [ ] ¿Se está escribiendo el código mínimo necesario?
- [ ] ¿Se evita anticipar requisitos no justificados?
- [ ] ¿Las pruebas proporcionan feedback rápido?
- [ ] ¿Se respetan los contratos y las reglas de negocio?

### Durante la refactorización

- [ ] ¿Las pruebas pasan antes de empezar?
- [ ] ¿La mejora simplifica el diseño?
- [ ] ¿Se ha evitado modificar accidentalmente el comportamiento?
- [ ] ¿Se han eliminado duplicaciones injustificadas?
- [ ] ¿La suite completa sigue pasando?

### Antes de integrar los cambios

- [ ] ¿Se ejecutan las pruebas relevantes?
- [ ] ¿Se han considerado los casos límite?
- [ ] ¿Las pruebas de integración necesarias también pasan?
- [ ] ¿El pipeline de CI está configurado correctamente?
- [ ] ¿Los comportamientos nuevos y modificados están documentados mediante pruebas adecuadas?

---

## 22. Estrategia para introducir TDD en un equipo

No es necesario transformar todo un proyecto de una vez.

Una adopción gradual puede seguir este plan:

### Etapa 1. Elegir un área adecuada

Seleccionar una funcionalidad de negocio con reglas claras y un riesgo controlado.

Por ejemplo, validaciones, cálculos, políticas de precios o transiciones de estado.

### Etapa 2. Acordar convenciones

Definir cómo se nombran las pruebas, dónde se ubican, cómo se organizan y qué framework se utiliza.

### Etapa 3. Practicar Red-Green-Refactor

Realizar sesiones de programación en pareja o mob programming para aprender a trabajar con ciclos pequeños.

### Etapa 4. Automatizar la ejecución

Configurar el proyecto para que las pruebas se ejecuten localmente y en el pipeline de integración continua.

### Etapa 5. Revisar resultados

Observar si las pruebas detectan errores relevantes, si son mantenibles y si proporcionan feedback suficientemente rápido.

### Etapa 6. Ampliar progresivamente

Aplicar TDD a nuevas funcionalidades y utilizar pruebas de caracterización cuando se necesite modificar código heredado cuyo comportamiento no está suficientemente documentado.

**Importante:** en código heredado, puede ser necesario comenzar por pruebas de caracterización que registren el comportamiento actual antes de introducir nuevas funcionalidades mediante TDD.

---

## 23. TDD y herramientas de inteligencia artificial

Las herramientas de IA pueden ayudar a generar casos de prueba, sugerir valores límite, proponer datos de prueba o detectar escenarios que no se han considerado.

Sin embargo, conviene mantener la disciplina de TDD:

1. Definir y revisar el comportamiento esperado.
2. Escribir o validar una prueba que falle por una causa concreta.
3. Implementar el cambio mínimo necesario.
4. Ejecutar las pruebas.
5. Revisar la solución y refactorizar.
6. Comprobar que las pruebas no reproducen simplemente los mismos errores de razonamiento que la implementación.

La IA puede generar pruebas que parecen correctas pero contienen expectativas equivocadas, no detectan errores o están demasiado acopladas al código generado.

Por ello, la revisión humana de los requisitos y de las aserciones sigue siendo fundamental.

---

## 24. Resumen conceptual

TDD establece un ciclo de desarrollo incremental en el que las pruebas guían la implementación y permiten comprobar continuamente el comportamiento del sistema.

Su valor no se limita a detectar errores: también contribuye a hacer explícitos los contratos, facilita la refactorización y proporciona feedback temprano sobre el diseño.

El ciclo central es:

**Red → Green → Refactor**

- **Red:** expresar un comportamiento esperado mediante una prueba que falla.
- **Green:** implementar lo mínimo necesario para satisfacerlo.
- **Refactor:** mejorar la estructura sin alterar el comportamiento observable.

TDD no sustituye al diseño de software ni garantiza por sí solo la ausencia de defectos. Funciona mejor cuando se combina con requisitos claros, pruebas de diferentes niveles, revisión de código, integración continua y principios de diseño como SOLID, KISS y DRY.

## 25. Conclusión

TDD es una metodología especialmente útil para construir software evolutivo, mantenible y verificable.

Al convertir los requisitos en pruebas antes de implementar cada comportamiento, el equipo obtiene feedback frecuente y puede detectar errores de manera temprana. Además, una suite de pruebas bien diseñada permite refactorizar con mayor confianza y sirve como documentación ejecutable del comportamiento esperado.

La clave no es escribir el mayor número posible de pruebas, sino **escribir pruebas valiosas, implementar en incrementos pequeños y mantener un diseño sencillo mediante refactorización continua**.

TDD debe entenderse como una disciplina de ingeniería, no como una garantía automática de calidad. Sus resultados dependen de la precisión de los requisitos, la calidad de las pruebas y el criterio técnico del equipo.

---

## 26. Referencias y recursos para profundizar

- **Kent Beck — *Test-Driven Development: By Example*.** Obra de referencia para comprender el ciclo Red-Green-Refactor.
- **JUnit 5 — User Guide.** Documentación oficial para escribir y ejecutar pruebas en Java: https://junit.org/junit5/docs/current/user-guide/
- **Martin Fowler — Test-Driven Development.** Artículos y explicaciones sobre TDD y prácticas de diseño: https://martinfowler.com/
- **Extreme Programming (XP).** Contexto metodológico en el que TDD se popularizó: https://www.agilealliance.org/
- **Google Testing Blog.** Artículos sobre estrategia de pruebas, mantenibilidad y automatización: https://testing.googleblog.com/

La documentación oficial de las herramientas y las publicaciones de referencia son un buen punto de partida para profundizar en la práctica.