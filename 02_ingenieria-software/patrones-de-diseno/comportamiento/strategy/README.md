# Patrón de Diseño Strategy (Comportamiento)

## 1. Introducción

El patrón de diseño **Strategy (Estrategia)** pertenece a la categoría de los patrones de comportamiento. Su objetivo es definir una familia de algoritmos, encapsular cada uno de ellos en una clase independiente y hacer que puedan intercambiarse sin modificar el código cliente que los utiliza.

Este patrón permite separar la lógica de una operación de la implementación concreta del algoritmo que debe ejecutarla.

En lugar de concentrar diferentes variantes de un comportamiento en una única clase mediante estructuras condicionales, Strategy delega esa responsabilidad en implementaciones independientes que comparten una misma interfaz.

Algunos ejemplos habituales son:

- Diferentes métodos de pago.
- Algoritmos de ordenación y búsqueda.
- Sistemas de cálculo de descuentos.
- Estrategias de envío de pedidos.
- Métodos de autenticación.
- Algoritmos de compresión.
- Sistemas de navegación y cálculo de rutas.

La idea principal es **encapsular los comportamientos variables para poder seleccionarlos, sustituirlos y ampliarlos sin modificar innecesariamente el código que los utiliza**.

## 2. Problema que resuelve

Imaginemos una tienda en línea que permite calcular los gastos de envío mediante diferentes métodos:

- Envío estándar.
- Envío urgente.
- Envío gratuito para determinados clientes.

Una implementación tradicional podría concentrar todos los algoritmos en una misma clase:

```java
public class CalculadoraEnvio {

    public double calcular(String tipo, double peso) {

        if (tipo.equals("ESTANDAR")) {
            return peso * 2.0;

        } else if (tipo.equals("URGENTE")) {
            return peso * 4.5;

        } else if (tipo.equals("GRATUITO")) {
            return 0.0;

        } else {
            throw new IllegalArgumentException(
                "Tipo de envío no soportado"
            );
        }
    }
}
```

Este enfoque presenta varios inconvenientes:

- **Acoplamiento:** la clase conoce todos los algoritmos de cálculo.
- **Condicionales crecientes:** cada nueva estrategia añade más ramas al código.
- **Dificultad de mantenimiento:** los algoritmos se encuentran mezclados.
- **Extensibilidad limitada:** incorporar una nueva modalidad requiere modificar la clase existente.
- **Pruebas menos aisladas:** probar un algoritmo puede implicar recorrer una estructura condicional compartida.
- **Reutilización limitada:** resulta más difícil utilizar un algoritmo de forma independiente en otros contextos.

Strategy permite solucionar estos problemas separando cada algoritmo en una implementación independiente y ofreciendo una interfaz común para utilizarlos.

## 3. Solución propuesta

El patrón Strategy introduce una abstracción que define el contrato común de los algoritmos y diferentes implementaciones que encapsulan cada variante.

Los elementos principales son:

- **Strategy:** interfaz que define el comportamiento común.
- **Concrete Strategy:** implementación concreta de un algoritmo.
- **Context:** clase que utiliza una estrategia mediante la interfaz.
- **Cliente:** componente que selecciona o configura la estrategia que desea utilizar.

Por ejemplo:

```java
CalculadoraEnvio calculadora =
    new CalculadoraEnvio(new EnvioEstandar());

double coste = calculadora.calcular(5.0);
```

Si el cliente necesita otro comportamiento, puede sustituir la estrategia:

```java
calculadora.setEstrategia(new EnvioUrgente());

double coste = calculadora.calcular(5.0);
```

La clase `CalculadoraEnvio` no necesita conocer los detalles internos de cada algoritmo. Solo utiliza el contrato definido por `EstrategiaEnvio`.

## 4. Estructura del patrón

Los participantes habituales de Strategy son:

- **Strategy:** define la interfaz común de los algoritmos.
- **Concrete Strategy:** implementa una variante específica del algoritmo.
- **Context:** mantiene una referencia a una estrategia y delega en ella la operación correspondiente.
- **Cliente:** selecciona o proporciona la estrategia adecuada según las necesidades de la aplicación.

### Diagrama conceptual

```text
                 Cliente
                    |
                    v
                  Context
                    |
               Strategy
                    |
          +---------+---------+
          |         |         |
          v         v         v
       Strategy A Strategy B Strategy C
```

El contexto depende de una abstracción y no necesita implementar directamente cada variante del comportamiento.

Esto permite incorporar nuevas estrategias sin tener que modificar la lógica interna del contexto, siempre que se mantenga el contrato definido por la interfaz.

## 5. Ejemplo de implementación en Java

Imaginemos un sistema que calcula los gastos de envío según la modalidad seleccionada por el cliente.

Para simplificar el ejemplo, utilizaremos tres estrategias:

- Envío estándar.
- Envío urgente.
- Envío gratuito.

Los importes son ilustrativos y no representan tarifas reales.

### 5.1. Definición de la interfaz Strategy

Primero se define una interfaz que establece la operación común a todos los algoritmos.

```java
public interface EstrategiaEnvio {

    double calcularCoste(double peso);
}
```

Cada estrategia deberá implementar `calcularCoste()`, pero podrá aplicar sus propias reglas.

### 5.2. Implementación de las estrategias concretas

**Envío estándar:**

```java
public class EnvioEstandar implements EstrategiaEnvio {

    @Override
    public double calcularCoste(double peso) {
        validarPeso(peso);
        return peso * 2.0;
    }

    private void validarPeso(double peso) {
        if (peso < 0) {
            throw new IllegalArgumentException(
                "El peso no puede ser negativo"
            );
        }
    }
}
```

**Envío urgente:**

```java
public class EnvioUrgente implements EstrategiaEnvio {

    @Override
    public double calcularCoste(double peso) {
        validarPeso(peso);
        return peso * 4.5;
    }

    private void validarPeso(double peso) {
        if (peso < 0) {
            throw new IllegalArgumentException(
                "El peso no puede ser negativo"
            );
        }
    }
}
```

**Envío gratuito:**

```java
public class EnvioGratuito implements EstrategiaEnvio {

    @Override
    public double calcularCoste(double peso) {
        if (peso < 0) {
            throw new IllegalArgumentException(
                "El peso no puede ser negativo"
            );
        }

        return 0.0;
    }
}
```

Cada clase encapsula una variante del algoritmo. En una aplicación real, las reglas podrían considerar también el destino, las dimensiones del paquete, el importe del pedido o las condiciones de una promoción.

Para evitar duplicaciones, las validaciones comunes podrían extraerse a un componente compartido si la complejidad lo justificara.

### 5.3. Implementación del Context

La clase `CalculadoraEnvio` utiliza una estrategia para calcular el coste.

```java
import java.util.Objects;

public class CalculadoraEnvio {

    private EstrategiaEnvio estrategia;

    public CalculadoraEnvio(EstrategiaEnvio estrategia) {
        this.estrategia = Objects.requireNonNull(estrategia);
    }

    public void setEstrategia(EstrategiaEnvio estrategia) {
        this.estrategia = Objects.requireNonNull(estrategia);
    }

    public double calcular(double peso) {
        return estrategia.calcularCoste(peso);
    }
}
```

La clase no necesita saber si el cálculo utiliza una tarifa estándar, urgente o gratuita. Simplemente delega la operación en la estrategia actual.

El método `setEstrategia()` permite sustituir el algoritmo durante la ejecución.

Si no se necesita cambiar la estrategia una vez creado el contexto, puede utilizarse una referencia `final` y omitir el método modificador.

### 5.4. Uso desde el cliente

El cliente decide qué estrategia utilizar.

```java
public class Main {

    public static void main(String[] args) {

        double peso = 5.0;

        CalculadoraEnvio calculadora =
            new CalculadoraEnvio(new EnvioEstandar());

        System.out.println(
            "Envío estándar: "
                + calculadora.calcular(peso)
        );

        calculadora.setEstrategia(new EnvioUrgente());

        System.out.println(
            "Envío urgente: "
                + calculadora.calcular(peso)
        );

        calculadora.setEstrategia(new EnvioGratuito());

        System.out.println(
            "Envío gratuito: "
                + calculadora.calcular(peso)
        );
    }
}
```

Salida esperada:

```text
Envío estándar: 10.0
Envío urgente: 22.5
Envío gratuito: 0.0
```

### 5.5. Explicación del ejemplo

1. El cliente crea una estrategia concreta.
2. La estrategia se proporciona a `CalculadoraEnvio`.
3. Cuando se llama a `calcular()`, el contexto delega la operación en la estrategia actual.
4. El cliente puede sustituir la estrategia por otra implementación.
5. La clase `CalculadoraEnvio` permanece sin cambios aunque se añadan nuevos algoritmos.

Si se incorpora una estrategia para envíos internacionales, basta con implementar `EstrategiaEnvio` y proporcionarla al contexto. No es necesario añadir una nueva condición dentro de `CalculadoraEnvio`.

## 6. Variante: Strategy con expresiones lambda

En Java, cuando una estrategia puede representarse mediante una interfaz funcional, es posible utilizar expresiones lambda en lugar de crear una clase para cada algoritmo.

La interfaz puede mantenerse igual:

```java
@FunctionalInterface
public interface EstrategiaEnvio {

    double calcularCoste(double peso);
}
```

El cliente puede proporcionar las estrategias directamente:

```java
public class Main {

    public static void main(String[] args) {

        EstrategiaEnvio estandar =
            peso -> peso * 2.0;

        EstrategiaEnvio urgente =
            peso -> peso * 4.5;

        EstrategiaEnvio gratuito =
            peso -> 0.0;

        CalculadoraEnvio calculadora =
            new CalculadoraEnvio(estandar);

        System.out.println(calculadora.calcular(5.0));

        calculadora.setEstrategia(urgente);

        System.out.println(calculadora.calcular(5.0));

        calculadora.setEstrategia(gratuito);

        System.out.println(calculadora.calcular(5.0));
    }
}
```

### Ventajas de esta variante

- Reduce el número de clases.
- Facilita definir algoritmos pequeños.
- Permite aprovechar las características funcionales de Java.
- Hace más conciso el código cuando la lógica es sencilla.

### Cuándo no utilizarla

Las clases concretas pueden resultar más apropiadas cuando cada estrategia contiene múltiples operaciones, dependencias, configuración o reglas de negocio complejas.

Además, las validaciones deben seguir formando parte del diseño: una lambda que no comprueba entradas inválidas puede incumplir las expectativas del contrato.

## 7. Selección de estrategias en tiempo de ejecución

Una de las principales ventajas de Strategy es que la implementación puede seleccionarse según la configuración o el contexto de la aplicación.

Por ejemplo, la modalidad de envío podría recibirse desde una petición:

```java
public enum TipoEnvio {
    ESTANDAR,
    URGENTE,
    GRATUITO
}
```

Un componente de selección podría resolver el tipo:

```java
public class SelectorEstrategiaEnvio {

    public EstrategiaEnvio seleccionar(TipoEnvio tipo) {

        return switch (tipo) {
            case ESTANDAR -> new EnvioEstandar();
            case URGENTE -> new EnvioUrgente();
            case GRATUITO -> new EnvioGratuito();
        };
    }
}
```

El cliente utilizaría el selector para obtener el algoritmo correspondiente:

```java
SelectorEstrategiaEnvio selector =
    new SelectorEstrategiaEnvio();

EstrategiaEnvio estrategia =
    selector.seleccionar(TipoEnvio.URGENTE);

CalculadoraEnvio calculadora =
    new CalculadoraEnvio(estrategia);
```

Esta solución separa la selección de la estrategia de su ejecución.

No obstante, el selector debe modificarse cuando se incorpora una nueva modalidad. En sistemas más extensibles, puede utilizarse un registro de estrategias, configuración externa o inyección de dependencias para evitar una selección centralizada demasiado rígida.

## 8. Ventajas

- **Desacoplamiento:** el contexto no depende de las implementaciones concretas de los algoritmos.
- **Extensibilidad:** pueden añadirse nuevas estrategias sin modificar la clase que las utiliza.
- **Mantenibilidad:** cada algoritmo está encapsulado en una implementación independiente.
- **Reutilización:** las estrategias pueden utilizarse desde diferentes contextos compatibles.
- **Facilidad de pruebas:** cada algoritmo puede probarse de manera aislada.
- **Flexibilidad:** permite cambiar el comportamiento en tiempo de ejecución.
- **Reducción de condicionales:** evita concentrar numerosas variantes en una única clase.
- **Claridad:** cada estrategia tiene una responsabilidad bien delimitada.

## 9. Desventajas

- **Mayor número de componentes:** cada algoritmo puede requerir una clase adicional.
- **Complejidad innecesaria:** para una operación sencilla con una única variante, Strategy puede ser excesivo.
- **Selección adicional:** el sistema necesita decidir qué estrategia utilizar.
- **Conocimiento por parte del cliente:** en algunas implementaciones, el cliente debe conocer las estrategias disponibles.
- **Posible duplicación:** varias estrategias pueden repetir validaciones o lógica común.
- **Gestión de dependencias:** las estrategias pueden necesitar configuración o servicios externos.
- **Sobrecarga de abstracción:** una interfaz por cada operación variable no siempre aporta un beneficio significativo.

## 10. Cuándo utilizar Strategy

Es recomendable utilizar este patrón cuando:

- Existen varias formas de realizar una misma operación.
- Los algoritmos cambian con frecuencia o pueden ampliarse.
- Se necesita seleccionar un comportamiento según la configuración.
- Una clase contiene demasiados condicionales para elegir algoritmos.
- Se desea probar cada variante de forma independiente.
- Diferentes contextos necesitan reutilizar los mismos algoritmos.

### Cuándo no utilizarlo

Puede ser innecesario cuando:

- Solo existe una implementación estable.
- La lógica alternativa es muy pequeña y poco probable que cambie.
- El número de clases añade más complejidad que claridad.
- La variación se limita a unos pocos parámetros y no a algoritmos distintos.
- Una función sencilla o una expresión lambda es suficiente para resolver el problema.

## 11. Ejemplos de uso en aplicaciones reales

### 11.1. Métodos de pago

Una aplicación puede ofrecer pagos mediante tarjeta, transferencia bancaria o una pasarela digital. Cada estrategia encapsula las reglas necesarias para ejecutar el pago.

### 11.2. Sistemas de descuentos

Una tienda puede aplicar descuentos para clientes habituales, promociones temporales o campañas especiales. Cada estrategia implementa su propio cálculo.

### 11.3. Algoritmos de búsqueda

Una aplicación puede utilizar búsqueda lineal, búsqueda binaria o un algoritmo especializado dependiendo de los datos y de los requisitos de rendimiento.

### 11.4. Compresión de archivos

Una aplicación puede seleccionar distintos algoritmos de compresión según el tipo de archivo, el tamaño o las necesidades de velocidad.

### 11.5. Autenticación

Un sistema puede utilizar diferentes mecanismos de autenticación, como credenciales, certificados o proveedores de identidad. Cada implementación encapsula el proceso correspondiente.

### 11.6. Cálculo de rutas

Una aplicación de navegación puede utilizar algoritmos diferentes para calcular rutas en coche, bicicleta o a pie, compartiendo una interfaz común.

En todos estos escenarios, la utilidad de Strategy aumenta cuando las variantes representan algoritmos claramente diferenciados y se necesita poder sustituirlos con facilidad.

## 12. Relación con los principios SOLID

Strategy contribuye especialmente a la modularidad y al desacoplamiento.

### Single Responsibility Principle (SRP)

Cada estrategia encapsula una variante concreta de un algoritmo, evitando que una única clase concentre todas las implementaciones.

### Open/Closed Principle (OCP)

Se pueden añadir nuevas estrategias sin modificar el contexto, siempre que respeten la interfaz y no sea necesario cambiar la lógica de selección.

### Liskov Substitution Principle (LSP)

Todas las implementaciones deben respetar el contrato de la estrategia. Por ejemplo, si el contrato define cómo se tratan los pesos negativos, cada implementación debe cumplir esa regla.

### Dependency Inversion Principle (DIP)

El contexto depende de la abstracción `EstrategiaEnvio`, no de una implementación concreta como `EnvioEstandar`.

Esto facilita sustituir los algoritmos y realizar pruebas con implementaciones alternativas.

## 13. Buenas prácticas

1. **Definir una interfaz clara:** establecer qué operación deben ofrecer todas las estrategias.
2. **Mantener cada algoritmo independiente:** evitar que una estrategia dependa innecesariamente de otra implementación concreta.
3. **Validar las entradas:** definir reglas consistentes para datos inválidos.
4. **Evitar estrategias triviales innecesarias:** no crear clases adicionales cuando una función sencilla resuelve el problema.
5. **Separar selección y ejecución:** cuando sea útil, utilizar un componente específico para elegir la estrategia.
6. **Evitar condicionales centrales excesivos:** valorar un registro de estrategias o la inyección de dependencias cuando el número de variantes crezca.
7. **Compartir lógica común con criterio:** extraer validaciones o cálculos comunes cuando realmente reduzcan duplicación.
8. **Probar cada estrategia de forma independiente:** verificar los casos normales, límites y errores.
9. **Controlar las dependencias externas:** las estrategias que utilizan bases de datos, APIs o servicios deben recibir esas dependencias de forma explícita cuando sea posible.
10. **Preferir estrategias inmutables cuando sea viable:** facilita su reutilización y reduce los riesgos de modificaciones accidentales.
11. **Documentar las diferencias entre algoritmos:** explicar qué condiciones determinan cuándo conviene utilizar cada estrategia.

## 14. Comparación con otros patrones de comportamiento

| Patrón | Objetivo principal | Diferencia respecto a Strategy |
|---|---|---|
| State | Modificar el comportamiento según el estado interno de un objeto. | Las estrategias representan algoritmos intercambiables; State representa estados y sus transiciones. |
| Template Method | Definir la estructura de un algoritmo y permitir redefinir determinados pasos. | Utiliza principalmente herencia; Strategy favorece la composición. |
| Command | Encapsular una solicitud como un objeto. | Representa una operación que puede ejecutarse, encolarse o registrarse. |
| Factory Method | Delegar la creación de objetos en subclases. | Resuelve cómo crear un objeto, no cómo intercambiar algoritmos. |
| Chain of Responsibility | Pasar una solicitud entre diferentes manejadores. | La solicitud recorre una cadena de posibles procesadores. |
| Observer | Notificar cambios a varios componentes interesados. | Propaga eventos; Strategy selecciona cómo realizar una operación. |

### Diferencia entre Strategy y State

Strategy y State pueden tener una estructura similar porque ambos utilizan una interfaz y varias implementaciones concretas.

La diferencia se encuentra en su intención:

- **Strategy:** encapsula algoritmos alternativos que pueden seleccionarse según las necesidades del cliente.
- **State:** representa los estados de un objeto y el comportamiento asociado a cada uno, incluyendo las posibles transiciones.

Por ejemplo, elegir entre diferentes algoritmos de cálculo de rutas corresponde a Strategy. Gestionar las fases de un pedido, donde cada fase permite determinadas operaciones, encaja mejor con State.

## 15. Conclusión

Strategy es un patrón de comportamiento que permite definir una familia de algoritmos, encapsularlos en implementaciones independientes y hacer que puedan intercambiarse sin modificar el código que los utiliza.

Su principal aportación consiste en separar las variantes de un comportamiento, reducir la complejidad de las estructuras condicionales y facilitar la incorporación de nuevos algoritmos.

Resulta especialmente útil en sistemas de pagos, descuentos, envíos, autenticación y cálculo de rutas. Sin embargo, debe aplicarse cuando existe una variación real del comportamiento que justifique la abstracción.

Strategy y State comparten una estructura basada en interfaces y clases concretas, pero tienen objetivos diferentes: Strategy permite elegir entre algoritmos, mientras que State permite que el comportamiento evolucione según el estado interno de un objeto.

En definitiva, **Strategy permite cambiar cómo se realiza una operación sin tener que modificar la clase que la utiliza**, favoreciendo un diseño más flexible, modular y mantenible.

## 16. Referencias

- [Refactoring Guru — Strategy](https://refactoring.guru/design-patterns/strategy)
- [SourceMaking — Strategy Design Pattern](https://sourcemaking.com/design_patterns/strategy)
- [Oracle Java Documentation](https://docs.oracle.com/en/java/)
- [Java Language Specification — Functional Interfaces](https://docs.oracle.com/javase/specs/jls/se21/html/jls-9.html)
