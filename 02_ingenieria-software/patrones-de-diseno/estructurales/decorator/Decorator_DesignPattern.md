# Patrón de diseño estructural: Decorator (Decorador)

## 1. Introducción

El patrón de diseño **Decorator (Decorador)** es un patrón estructural que permite añadir nuevas responsabilidades o comportamientos a un objeto de forma dinámica, sin modificar su clase original.

Su objetivo principal es **extender el comportamiento de un objeto mediante composición, evitando tener que crear una subclase para cada combinación posible de funcionalidades**.

En lugar de modificar la clase original o utilizar una jerarquía de herencia cada vez más compleja, Decorator envuelve un objeto con otro objeto que implementa la misma interfaz y añade comportamiento antes, después o alrededor de la operación original.

### Ejemplo conceptual

Imaginemos que estamos desarrollando una aplicación que permite preparar diferentes tipos de café.

Inicialmente, podemos tener un café básico. Más adelante, queremos añadir funcionalidades como:

- Leche.
- Azúcar.
- Chocolate.
- Nata.

Una solución basada exclusivamente en herencia podría generar clases como:

- `CafeConLeche`
- `CafeConAzucar`
- `CafeConLecheYAzucar`
- `CafeConChocolateYLeche`
- `CafeConNataYChocolate`

A medida que aumenta el número de ingredientes, el número de combinaciones posibles también crece.

Con Decorator podemos partir de un café básico y envolverlo con los decoradores que necesitemos. Cada decorador añade una funcionalidad o un coste sin modificar el objeto original.

Por ejemplo:

```text
CafeSimple
    |
    v
CafeConLeche
    |
    v
CafeConChocolate
```

Cada elemento mantiene la misma interfaz, pero añade una responsabilidad.

---

## 2. Problema que resuelve

Decorator resuelve el problema de **añadir funcionalidades a objetos sin modificar sus clases y sin multiplicar las subclases para representar todas las combinaciones posibles**.

Este problema aparece cuando una clase necesita diferentes características opcionales que pueden combinarse entre sí.

### Ejemplo sin utilizar Decorator

Supongamos que tenemos una interfaz para representar cafés:

```java
public interface Cafe {

    String descripcion();

    double coste();
}
```

Una implementación básica sería:

```java
public class CafeSimple implements Cafe {

    @Override
    public String descripcion() {
        return "Café simple";
    }

    @Override
    public double coste() {
        return 1.50;
    }
}
```

Ahora queremos ofrecer café con leche, café con chocolate y café con ambos ingredientes.

Una alternativa sería crear una clase para cada combinación:

```text
Cafe
├── CafeSimple
├── CafeConLeche
├── CafeConChocolate
└── CafeConLecheYChocolate
```

Si incorporamos más ingredientes, la cantidad de combinaciones aumenta rápidamente.

Además, tendríamos que modificar o ampliar la jerarquía cada vez que apareciera una nueva combinación.

**Decorator permite resolver este problema utilizando objetos envolventes que añaden funcionalidades de forma independiente y combinable.**

---

## 3. Solución propuesta

La solución consiste en crear una estructura donde los decoradores implementan la misma interfaz que el objeto que envuelven.

El patrón se compone de los siguientes elementos:

1. **Component:** define la interfaz común.
2. **ConcreteComponent:** proporciona el comportamiento original.
3. **Decorator:** mantiene una referencia a otro componente y delega en él.
4. **ConcreteDecorator:** añade una funcionalidad específica antes o después de delegar.

El cliente puede utilizar tanto el componente original como cualquiera de sus decoradores sin cambiar la forma de invocarlos.

### Diagrama conceptual

```text
                  Cliente
                     |
                     v
                 Component
                     ^
                     |
          +----------+----------+
          |                     |
 ConcreteComponent          Decorator
                               |
                               | contiene
                               v
                           Component
                               ^
                               |
                    +----------+----------+
                    |                     |
             DecoratorA             DecoratorB
```

El aspecto fundamental es que el decorador implementa la misma interfaz que el componente y, además, contiene una referencia a otro componente.

Esto permite encadenar varios decoradores.

---

## 4. Implementación completa en Java

Para entender el patrón, utilizaremos el ejemplo de los cafés y sus ingredientes adicionales.

### 4.1. Interfaz común: Component

Esta interfaz define las operaciones que estarán disponibles tanto en el café básico como en los decoradores.

```java
public interface Cafe {

    String descripcion();

    double coste();
}
```

El cliente trabajará con esta interfaz sin necesitar conocer la clase concreta que se encuentra detrás.

### 4.2. Componente concreto: ConcreteComponent

Representa el café básico, que proporciona el comportamiento original.

```java
public class CafeSimple implements Cafe {

    @Override
    public String descripcion() {
        return "Café simple";
    }

    @Override
    public double coste() {
        return 1.50;
    }
}
```

Esta clase no necesita conocer los ingredientes opcionales que podrán añadirse posteriormente.

### 4.3. Decorador abstracto: Decorator

El decorador base implementa la interfaz `Cafe` y mantiene una referencia a otro objeto que también implementa esa interfaz.

```java
public abstract class CafeDecorator implements Cafe {

    protected final Cafe cafe;

    protected CafeDecorator(Cafe cafe) {
        this.cafe = cafe;
    }
}
```

Esta clase proporciona la estructura común para todos los decoradores.

Cada decorador concreto decidirá cómo ampliar la descripción o el coste del café que recibe.

### 4.4. Decorador concreto: leche

```java
public class LecheDecorator extends CafeDecorator {

    public LecheDecorator(Cafe cafe) {
        super(cafe);
    }

    @Override
    public String descripcion() {
        return cafe.descripcion() + ", con leche";
    }

    @Override
    public double coste() {
        return cafe.coste() + 0.40;
    }
}
```

Este decorador añade leche al café existente.

No necesita conocer si el objeto recibido es un `CafeSimple` o un café que ya contiene otros decoradores.

### 4.5. Decorador concreto: chocolate

```java
public class ChocolateDecorator extends CafeDecorator {

    public ChocolateDecorator(Cafe cafe) {
        super(cafe);
    }

    @Override
    public String descripcion() {
        return cafe.descripcion() + ", con chocolate";
    }

    @Override
    public double coste() {
        return cafe.coste() + 0.60;
    }
}
```

Este decorador añade chocolate y su coste correspondiente.

Al igual que el decorador de leche, delega en el objeto que envuelve y amplía su comportamiento.

### 4.6. Decorador concreto: nata

```java
public class NataDecorator extends CafeDecorator {

    public NataDecorator(Cafe cafe) {
        super(cafe);
    }

    @Override
    public String descripcion() {
        return cafe.descripcion() + ", con nata";
    }

    @Override
    public double coste() {
        return cafe.coste() + 0.50;
    }
}
```

Podemos seguir añadiendo decoradores sin modificar las clases anteriores.

### 4.7. Clase principal

```java
public class Main {

    public static void main(String[] args) {

        Cafe cafe = new CafeSimple();

        System.out.println(cafe.descripcion());
        System.out.println("Coste: " + cafe.coste() + " €");

        Cafe cafeConLeche =
            new LecheDecorator(new CafeSimple());

        System.out.println(cafeConLeche.descripcion());
        System.out.println(
            "Coste: " + cafeConLeche.coste() + " €"
        );

        Cafe cafeCompleto =
            new NataDecorator(
                new ChocolateDecorator(
                    new LecheDecorator(
                        new CafeSimple()
                    )
                )
            );

        System.out.println(cafeCompleto.descripcion());
        System.out.println(
            "Coste: " + cafeCompleto.coste() + " €"
        );
    }
}
```

Salida aproximada:

```text
Café simple
Coste: 1.5 €

Café simple, con leche
Coste: 1.9 €

Café simple, con leche, con chocolate, con nata
Coste: 3.0 €
```

### 4.8. ¿Qué hemos conseguido?

Hemos creado distintas configuraciones combinando los decoradores sin tener que crear una clase para cada combinación.

Por ejemplo:

```java
Cafe cafe1 =
    new LecheDecorator(new CafeSimple());

Cafe cafe2 =
    new ChocolateDecorator(new CafeSimple());

Cafe cafe3 =
    new ChocolateDecorator(
        new LecheDecorator(new CafeSimple())
    );
```

Cada objeto conserva la interfaz `Cafe`, pero el comportamiento y el coste cambian según los decoradores que lo envuelven.

En este ejemplo, el orden de los decoradores cambia el orden de las operaciones y de la descripción. Como los costes son aditivos, el coste total no depende del orden.

En otros casos, los decoradores pueden interactuar y el orden sí puede afectar al resultado.

**Nota para aplicaciones reales:** para gestionar dinero es preferible utilizar `BigDecimal` o una representación monetaria adecuada, en lugar de `double`, para evitar problemas de precisión.

---

## 5. Estructura general del patrón

La estructura habitual de Decorator puede representarse mediante el siguiente ejemplo genérico:

```java
// Component
public interface Componente {

    void ejecutar();
}

// ConcreteComponent
public class ComponenteConcreto implements Componente {

    @Override
    public void ejecutar() {
        System.out.println("Operación original");
    }
}

// Decorator
public abstract class Decorador implements Componente {

    protected final Componente componente;

    protected Decorador(Componente componente) {
        this.componente = componente;
    }
}

// ConcreteDecorator
public class DecoradorConcreto extends Decorador {

    public DecoradorConcreto(Componente componente) {
        super(componente);
    }

    @Override
    public void ejecutar() {
        System.out.println("Comportamiento adicional antes");
        componente.ejecutar();
        System.out.println("Comportamiento adicional después");
    }
}

// Client
public class Main {

    public static void main(String[] args) {

        Componente componente =
            new DecoradorConcreto(
                new ComponenteConcreto()
            );

        componente.ejecutar();
    }
}
```

La salida sería:

```text
Comportamiento adicional antes
Operación original
Comportamiento adicional después
```

Este ejemplo muestra que un decorador puede añadir comportamiento antes y después de la operación original, no solo modificar un resultado.

---

## 6. Encadenamiento de decoradores

Una de las características más importantes de Decorator es que permite envolver un objeto con múltiples decoradores.

Por ejemplo:

```java
Componente componente =
    new DecoradorA(
        new DecoradorB(
            new DecoradorC(
                new ComponenteConcreto()
            )
        )
    );
```

La llamada al método comienza en el decorador más externo y se propaga hacia el componente interior mediante delegación.

Cuando la llamada vuelve, los decoradores pueden realizar operaciones adicionales.

### ¿Por qué es útil?

Porque permite construir comportamientos combinables en tiempo de ejecución, sin crear una clase específica para cada combinación.

Por ejemplo, en una aplicación web podríamos envolver un servicio con decoradores para añadir:

- Registro de operaciones.
- Medición del tiempo de ejecución.
- Caché.
- Métricas.
- Validaciones adicionales.

No todos estos comportamientos tienen que implementarse necesariamente con Decorator, pero son ejemplos habituales de responsabilidades que pueden componerse de esta forma.

---

## 7. Ejemplo práctico: registro y medición de un servicio

Decorator también puede utilizarse para añadir funcionalidades transversales a un servicio sin modificar su implementación original.

### 7.1. Interfaz del servicio

```java
public interface Servicio {

    void ejecutar();
}
```

### 7.2. Implementación original

```java
public class ServicioReal implements Servicio {

    @Override
    public void ejecutar() {
        System.out.println("Ejecutando operación principal");
    }
}
```

### 7.3. Decorador de registro

```java
public class ServicioConLog implements Servicio {

    private final Servicio servicio;

    public ServicioConLog(Servicio servicio) {
        this.servicio = servicio;
    }

    @Override
    public void ejecutar() {
        System.out.println("Inicio de la operación");

        try {
            servicio.ejecutar();
            System.out.println("Operación completada");
        } catch (RuntimeException e) {
            System.err.println(
                "Error durante la operación: " + e.getMessage()
            );
            throw e;
        }
    }
}
```

Este decorador añade mensajes de registro antes y después de ejecutar el servicio. Si se produce una excepción de tipo `RuntimeException`, registra el error y lo propaga.

En un sistema real, el registro debería realizarse mediante una biblioteca de logging y evitar incluir datos sensibles.

### 7.4. Decorador de medición del tiempo

```java
public class ServicioConTiempo implements Servicio {

    private final Servicio servicio;

    public ServicioConTiempo(Servicio servicio) {
        this.servicio = servicio;
    }

    @Override
    public void ejecutar() {
        long inicio = System.nanoTime();

        try {
            servicio.ejecutar();
        } finally {
            long duracion = System.nanoTime() - inicio;

            System.out.println(
                "Duración: " + duracion + " ns"
            );
        }
    }
}
```

El bloque `finally` permite medir la duración incluso si la operación termina con una excepción.

### 7.5. Uso combinado

```java
public class Main {

    public static void main(String[] args) {

        Servicio servicio =
            new ServicioConLog(
                new ServicioConTiempo(
                    new ServicioReal()
                )
            );

        servicio.ejecutar();
    }
}
```

La estructura es la siguiente:

```text
ServicioConLog
       |
       v
ServicioConTiempo
       |
       v
ServicioReal
```

El servicio original no necesita conocer los mecanismos de registro ni de medición.

Este enfoque permite añadir o quitar funcionalidades sin modificar `ServicioReal`.

---

## 8. Ventajas y desventajas

### Ventajas

1. **Extensión dinámica:** permite añadir responsabilidades a un objeto en tiempo de ejecución mediante composición.
2. **Menor dependencia de la herencia:** evita crear una subclase por cada combinación de funcionalidades.
3. **Principio Open/Closed:** facilita añadir decoradores nuevos sin modificar el componente original.
4. **Composición flexible:** permite combinar distintos decoradores según las necesidades.
5. **Responsabilidades aisladas:** cada decorador puede centrarse en una funcionalidad concreta.
6. **Reutilización:** un mismo decorador puede utilizarse con distintos componentes que implementen la interfaz común.
7. **Facilidad de pruebas:** los decoradores pueden probarse de manera aislada y combinarse en pruebas de integración.

### Desventajas

1. **Aumento del número de clases:** cada responsabilidad adicional puede requerir un decorador concreto.
2. **Mayor complejidad de depuración:** una operación puede atravesar varias capas de envoltorios.
3. **Dificultad para inspeccionar el objeto real:** el cliente puede desconocer cuántos decoradores se han aplicado.
4. **Orden relevante:** algunos decoradores dependen del orden en que se encadenan.
5. **Compatibilidad de interfaces:** los decoradores clásicos suelen mantener la misma interfaz, por lo que no son apropiados si se necesita cambiarla.
6. **Gestión de errores más compleja:** un decorador debe decidir cómo tratar las excepciones sin alterar inesperadamente el contrato del componente.

Decorator resulta especialmente útil cuando existen comportamientos opcionales y combinables. No es necesario aplicarlo si una simple implementación satisface los requisitos.

---

## 9. Relación con los principios SOLID

### Single Responsibility Principle (SRP)

Cada decorador debería añadir una responsabilidad concreta.

Por ejemplo:

- `ServicioConLog` se encarga del registro.
- `ServicioConTiempo` se encarga de medir la duración.
- `ServicioReal` ejecuta la operación principal.

Esto permite cambiar una responsabilidad sin tener que modificar una clase que concentre todas las funcionalidades.

### Open/Closed Principle (OCP)

Podemos crear nuevos decoradores sin modificar la implementación original ni los decoradores ya existentes.

Por ejemplo, podríamos añadir un decorador de métricas o caché que implemente la misma interfaz.

### Liskov Substitution Principle (LSP)

El decorador debe respetar el contrato del componente que envuelve.

Si una implementación original devuelve un resultado, lanza determinadas excepciones o garantiza cierto comportamiento, el decorador debe conservar esas expectativas, salvo que la interfaz permita explícitamente otra semántica.

Implementar la misma interfaz no garantiza por sí solo que se cumpla este principio.

### Interface Segregation Principle (ISP)

Los componentes y decoradores deberían depender de interfaces específicas y manejables.

Una interfaz pequeña permite que los decoradores implementen únicamente las operaciones necesarias y facilita su reutilización.

### Dependency Inversion Principle (DIP)

Los decoradores dependen de una abstracción común, no necesariamente de una implementación concreta.

Por ejemplo, `ServicioConLog` depende de `Servicio`, lo que permite envolver distintas implementaciones compatibles.

---

## 10. Diferencias con otros patrones estructurales

### Decorator vs. Adapter

Ambos patrones pueden envolver un objeto, pero persiguen objetivos distintos.

- **Decorator:** mantiene la interfaz y añade responsabilidades.
- **Adapter:** transforma una interfaz para hacerla compatible con la que espera el cliente.

Por ejemplo, añadir registro a un servicio corresponde a Decorator; adaptar una biblioteca externa a la interfaz de nuestra aplicación corresponde a Adapter.

### Decorator vs. Proxy

Ambos pueden mantener una referencia a otro objeto y delegar operaciones.

- **Decorator:** añade responsabilidades al objeto.
- **Proxy:** controla el acceso al objeto, por ejemplo, mediante autorización, carga diferida o acceso remoto.

Las implementaciones pueden parecer similares, pero la intención del diseño es diferente.

### Decorator vs. Composite

- **Decorator:** envuelve un componente individual para ampliar su comportamiento.
- **Composite:** organiza objetos individuales y grupos de objetos en una estructura de árbol para tratarlos de manera uniforme.

Ambos pueden implementar la misma interfaz y utilizar composición, pero resuelven problemas diferentes.

### Decorator vs. Bridge

- **Decorator:** añade responsabilidades de manera combinable.
- **Bridge:** separa una abstracción de su implementación para que ambas evolucionen independientemente.

Bridge se centra en separar dos dimensiones del diseño; Decorator, en añadir comportamiento alrededor de un componente.

---

## 11. Casos de uso habituales

Decorator es especialmente útil en los siguientes escenarios:

- Añadir registro a servicios existentes.
- Medir tiempos de ejecución y recopilar métricas.
- Aplicar transformaciones a flujos de entrada y salida.
- Añadir compresión o cifrado a operaciones compatibles.
- Envolver componentes con validaciones adicionales.
- Añadir comportamientos opcionales a componentes gráficos.
- Ampliar servicios de negocio sin modificar su implementación original.

### Ejemplo en Java: flujos de entrada y salida

La biblioteca estándar de Java utiliza ampliamente una estructura de composición similar a Decorator.

Por ejemplo:

```java
import java.io.BufferedInputStream;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;

public class Main {

    public static void main(String[] args) throws IOException {

        try (InputStream entrada =
                 new BufferedInputStream(
                     new FileInputStream("datos.txt")
                 )) {

            int primerByte = entrada.read();

            System.out.println(
                "Primer byte: " + primerByte
            );
        }
    }
}
```

En este ejemplo:

- `FileInputStream` permite leer datos de un archivo.
- `BufferedInputStream` añade almacenamiento intermedio para mejorar la eficiencia de muchas operaciones de lectura.

Ambos se utilizan mediante la abstracción `InputStream`.

Es un ejemplo representativo del uso de envoltorios que añaden comportamiento sin cambiar la interfaz general de lectura.

---

## 12. Buenas prácticas

1. **Mantén la misma interfaz:** en la variante clásica, el decorador y el componente deben poder utilizarse de forma intercambiable.
2. **Añade una responsabilidad clara:** evita que un decorador se convierta en una clase que gestione demasiadas funcionalidades.
3. **Prefiere composición a herencia:** el comportamiento adicional debería delegarse en el objeto envuelto.
4. **Controla el orden de los decoradores:** documenta las combinaciones en las que el orden cambia el resultado.
5. **Respeta los contratos:** no alteres silenciosamente los resultados, las excepciones o las garantías del componente.
6. **Evita el exceso de envoltorios:** una cadena demasiado larga puede complicar el mantenimiento.
7. **Prueba cada decorador de forma aislada:** verifica tanto su comportamiento adicional como la delegación.
8. **Considera la gestión de errores:** si un decorador realiza acciones antes y después de delegar, determina qué debe ocurrir si el componente falla.
9. **Utiliza nombres descriptivos:** el nombre de cada decorador debería expresar claramente su responsabilidad.
10. **Evalúa alternativas:** interceptores, filtros, middleware o mecanismos de programación orientada a aspectos pueden ser más adecuados en algunos sistemas.

---

## 13. Pruebas recomendadas

Las pruebas deberían comprobar que cada decorador añade su comportamiento sin romper el contrato del componente original.

En el ejemplo del café, conviene verificar que:

- El café simple tiene la descripción y el coste esperados.
- El decorador de leche añade su descripción y coste.
- El decorador de chocolate funciona de forma independiente.
- La combinación de varios decoradores produce el resultado esperado.
- El orden de los decoradores se comporta correctamente cuando existen interacciones entre ellos.

Para los servicios, conviene comprobar que:

- El decorador delega en el servicio envuelto.
- Los registros se generan en los momentos esperados.
- La medición se realiza incluso cuando el servicio falla.
- Las excepciones se propagan de acuerdo con el contrato.
- La combinación de decoradores no altera accidentalmente el comportamiento funcional.

Se pueden utilizar pruebas unitarias con JUnit y dobles de prueba para aislar cada responsabilidad.

---

## 14. Conclusión

El patrón **Decorator (Decorador)** permite ampliar las responsabilidades de un objeto de forma flexible, sin modificar su clase original ni crear una subclase para cada combinación de funcionalidades.

Su principal beneficio es que permite componer comportamientos mediante objetos envolventes que mantienen una interfaz común.

En Java, se implementa habitualmente creando una interfaz de componente, una implementación concreta y una serie de decoradores que contienen una referencia a otro componente compatible.

Es especialmente útil cuando necesitamos añadir responsabilidades opcionales y combinables, como registro, medición, caché o funcionalidades adicionales en objetos existentes.

La idea fundamental es: **envolver un objeto con otro que mantiene su interfaz y añade comportamiento, permitiendo combinar funcionalidades mediante composición en lugar de depender exclusivamente de la herencia.**

---

## 15. Referencias

- **Design Patterns: Elements of Reusable Object-Oriented Software**, de Erich Gamma, Richard Helm, Ralph Johnson y John Vlissides (Gang of Four).
- Refactoring.Guru — Decorator: https://refactoring.guru/design-patterns/decorator
- Java Documentation: https://docs.oracle.com/en/java/
- SourceMaking — Decorator Design Pattern: https://sourcemaking.com/design_patterns/decorator
