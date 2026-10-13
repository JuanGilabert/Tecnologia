# Patrón de Diseño Singleton (Creacional)

## 1. Introducción

El patrón de diseño **Singleton** pertenece a la categoría de los patrones creacionales. Su objetivo es garantizar que una clase tenga una única instancia y proporcionar un punto de acceso global a ella.

Este patrón resulta útil cuando una aplicación necesita coordinar el acceso a un recurso o mantener una única instancia compartida de un componente.

Algunos ejemplos habituales son:

- Gestores de configuración.
- Registradores de eventos (*loggers*).
- Gestores de caché compartida.
- Componentes de acceso a recursos comunes.
- Servicios que coordinan una única instancia dentro de un ámbito determinado.

Sin embargo, Singleton debe utilizarse con precaución. Aunque puede simplificar el acceso a una instancia compartida, también puede introducir estado global, acoplamiento y dificultades para realizar pruebas automatizadas.

Es importante distinguir entre garantizar una única instancia dentro de una aplicación y garantizar una única instancia en todo un sistema distribuido. Singleton, por sí solo, no ofrece esta última garantía.

## 2. Problema que resuelve

En determinadas situaciones, crear múltiples instancias de una misma clase puede provocar inconsistencias o un consumo innecesario de recursos.

Por ejemplo, imaginemos una clase encargada de gestionar la configuración de una aplicación:

```java
public class Configuracion {

    private String idioma = "es";

    public String getIdioma() {
        return idioma;
    }

    public void setIdioma(String idioma) {
        this.idioma = idioma;
    }
}
```

Si cada componente crea su propia instancia, los cambios realizados en una instancia no se reflejan necesariamente en las demás.

```java
Configuracion config1 = new Configuracion();
Configuracion config2 = new Configuracion();

config1.setIdioma("en");

System.out.println(config1.getIdioma()); // en
System.out.println(config2.getIdioma()); // es
```

Esto puede ser problemático cuando se pretende que todos los componentes compartan la misma configuración.

Además, si la clase gestiona un recurso costoso, la creación repetida de instancias puede incrementar el consumo de memoria o la complejidad de la coordinación.

Singleton aborda este problema controlando la creación de instancias y proporcionando un punto de acceso común.

No obstante, compartir una instancia no garantiza por sí solo que sus operaciones sean seguras ante accesos concurrentes. La sincronización del estado compartido es una cuestión adicional que debe diseñarse explícitamente.

## 3. Solución propuesta

Singleton restringe la creación de objetos mediante tres elementos principales:

1. Un constructor privado que impide crear instancias directamente desde otras clases.
2. Una referencia estática a la instancia única.
3. Un método o mecanismo de acceso que permite obtener esa instancia.

La utilización desde el cliente podría ser la siguiente:

```java
Configuracion config =
    Configuracion.getInstancia();

System.out.println(config.getIdioma());
```

Cada llamada a `getInstancia()` devuelve la misma instancia administrada por la clase.

## 4. Estructura del patrón

Los elementos principales de Singleton son:

- **Singleton:** clase que controla su propia instanciación.
- **Instancia única:** objeto compartido al que se accede mediante un método o campo definido por la clase.
- **Cliente:** componente que solicita y utiliza la instancia.

### Diagrama conceptual

```text
              Cliente A
                  |
                  v
          getInstancia()
                  |
                  v
             Singleton
                  |
          instancia compartida
                  |
          +-------+-------+
          |               |
          v               v
      Cliente B        Cliente C
```

La característica esencial es que todos los clientes obtienen la misma instancia dentro del ámbito en el que se aplica el patrón.

## 5. Implementación en Java

Existen distintas formas de implementar Singleton en Java. La elección depende de los requisitos de concurrencia, inicialización y gestión del ciclo de vida.

### 5.1. Implementación básica con inicialización anticipada

Una de las implementaciones más sencillas consiste en crear la instancia cuando la clase se inicializa.

```java
public class Configuracion {

    private static final Configuracion INSTANCIA =
        new Configuracion();

    private String idioma = "es";

    private Configuracion() {
    }

    public static Configuracion getInstancia() {
        return INSTANCIA;
    }

    public String getIdioma() {
        return idioma;
    }

    public void setIdioma(String idioma) {
        this.idioma = idioma;
    }
}
```

#### Explicación

- `private static final` mantiene una referencia estática a la instancia.
- El constructor privado impide la creación directa desde otras clases.
- `getInstancia()` proporciona el punto de acceso.
- La inicialización de las clases en Java garantiza que la instancia se publique de forma segura durante la inicialización normal de la clase.

**Ventajas:**

- Implementación sencilla.
- Seguridad frente a accesos concurrentes durante la inicialización.
- No necesita sincronización explícita en el método de acceso.

**Desventaja principal:**

La instancia se crea al inicializar la clase, aunque no llegue a utilizarse. Esto puede ser inconveniente si su construcción es costosa o depende de recursos que solo están disponibles más adelante.

### 5.2. Implementación con inicialización diferida (*Lazy Initialization*)

En esta variante, la instancia se crea cuando se solicita por primera vez.

```java
public class Configuracion {

    private static Configuracion instancia;

    private Configuracion() {
    }

    public static Configuracion getInstancia() {

        if (instancia == null) {
            instancia = new Configuracion();
        }

        return instancia;
    }
}
```

Esta implementación ilustra el concepto de inicialización diferida, pero tiene un problema importante: **no es segura en entornos multihilo**.

Dos hilos podrían comprobar simultáneamente que la instancia es `null` y crear objetos diferentes.

Por tanto, esta versión no debería utilizarse en aplicaciones concurrentes sin mecanismos adicionales de sincronización.

### 5.3. Implementación sincronizada

Una forma sencilla de garantizar la seguridad entre hilos consiste en sincronizar el método de acceso.

```java
public class Configuracion {

    private static Configuracion instancia;

    private Configuracion() {
    }

    public static synchronized Configuracion getInstancia() {

        if (instancia == null) {
            instancia = new Configuracion();
        }

        return instancia;
    }
}
```

La palabra clave `synchronized` garantiza que solo un hilo pueda ejecutar simultáneamente el método para una misma clase.

Una vez creada la instancia, el método sigue adquiriendo el bloqueo en cada llamada. Dependiendo del rendimiento requerido, esto puede introducir una sobrecarga innecesaria.

Esta implementación es sencilla y puede ser suficiente en muchas aplicaciones, aunque existen alternativas con una inicialización diferida segura y menos sincronización en el acceso habitual.

### 5.4. Implementación con *Double-Checked Locking*

Esta variante comprueba dos veces si la instancia existe: primero sin adquirir el bloqueo y después dentro de una sección sincronizada.

```java
public class Configuracion {

    private static volatile Configuracion instancia;

    private Configuracion() {
    }

    public static Configuracion getInstancia() {

        if (instancia == null) {
            synchronized (Configuracion.class) {
                if (instancia == null) {
                    instancia = new Configuracion();
                }
            }
        }

        return instancia;
    }
}
```

El uso de `volatile` es esencial en esta implementación: garantiza la visibilidad de la referencia y las garantías de orden de memoria necesarias para publicar correctamente la instancia.

**Ventajas:**

- La instancia se crea solo cuando se necesita.
- El bloqueo se utiliza únicamente durante la inicialización.
- Las llamadas posteriores evitan la sincronización explícita del método.

**Desventajas:**

- Es más compleja que otras variantes.
- Resulta más fácil cometer errores al implementarla.
- Puede ser innecesaria si la inicialización anticipada o el patrón *Holder* resuelven el problema con mayor claridad.

### 5.5. Implementación recomendada en Java: *Initialization-on-demand Holder*

Para muchos casos de uso en Java, el patrón *Holder* ofrece una alternativa sencilla, segura y con inicialización diferida.

```java
public class Configuracion {

    private Configuracion() {
    }

    private static class Holder {

        private static final Configuracion INSTANCIA =
            new Configuracion();
    }

    public static Configuracion getInstancia() {
        return Holder.INSTANCIA;
    }
}
```

#### ¿Cómo funciona?

1. La clase `Configuracion` puede cargarse sin crear inmediatamente la instancia.
2. Cuando se llama por primera vez a `getInstancia()`, se inicializa la clase anidada `Holder`.
3. Java garantiza la inicialización segura de esa clase.
4. La instancia se crea una sola vez durante la inicialización normal de `Holder`.

Esta solución combina inicialización diferida, seguridad entre hilos y una implementación relativamente sencilla.

Es una opción habitual cuando se necesita un Singleton clásico en Java y no se requiere un ciclo de vida gestionado por un contenedor externo.

## 6. Uso desde el cliente

Independientemente de la implementación elegida, el código cliente utiliza el método de acceso definido por la clase.

```java
public class Main {

    public static void main(String[] args) {

        Configuracion config1 =
            Configuracion.getInstancia();

        Configuracion config2 =
            Configuracion.getInstancia();

        config1.setIdioma("en");

        System.out.println(config1.getIdioma());
        System.out.println(config2.getIdioma());

        System.out.println(config1 == config2);
    }
}
```

Salida esperada:

```text
en
en
true
```

La comparación `config1 == config2` devuelve `true` porque ambas referencias apuntan al mismo objeto.

Además, el cambio de idioma realizado a través de `config1` es visible al acceder al mismo objeto mediante `config2`.

Este ejemplo demuestra la identidad compartida, pero no convierte automáticamente las operaciones sobre `idioma` en operaciones seguras entre hilos. Si varios hilos modifican el estado simultáneamente, será necesario definir la sincronización o las garantías de concurrencia adecuadas.

## 7. Singleton mediante un `enum` en Java

Java ofrece otra alternativa especialmente robusta para implementar un Singleton: utilizar un tipo `enum` con una única constante.

```java
public enum Configuracion {

    INSTANCIA;

    private String idioma = "es";

    public String getIdioma() {
        return idioma;
    }

    public void setIdioma(String idioma) {
        this.idioma = idioma;
    }
}
```

Uso:

```java
Configuracion config =
    Configuracion.INSTANCIA;

config.setIdioma("en");

System.out.println(config.getIdioma());
```

### Ventajas de esta variante

- La creación de la constante está gestionada por Java.
- Su inicialización es segura frente a concurrencia.
- La serialización estándar de los `enum` preserva la identidad de la constante.
- Ofrece protección sólida frente a la creación de instancias adicionales mediante los mecanismos habituales de reflexión.

Esta alternativa está recomendada por Joshua Bloch en *Effective Java* para implementar Singletons cuando el modelo basado en `enum` encaja con los requisitos.

No obstante, puede resultar menos apropiada si la clase necesita heredar de otra clase, extenderse mediante herencia o adaptarse a un ciclo de vida administrado externamente.

## 8. Ventajas

- **Instancia controlada:** centraliza la creación de una única instancia dentro del ámbito previsto.
- **Acceso compartido:** permite que diferentes componentes utilicen el mismo objeto.
- **Inicialización controlada:** puede crearse al inicio o de forma diferida.
- **Coordinación:** facilita compartir un estado cuando este realmente debe ser común.
- **Reducción de instancias duplicadas:** puede evitar construcciones repetidas de componentes costosos.
- **Simplicidad de acceso:** ofrece un punto de acceso conocido para obtener la instancia.

Estas ventajas solo son relevantes cuando existe una necesidad real de compartir una instancia. Singleton no es necesariamente la mejor solución para toda clase que tenga que utilizarse en varios lugares.

## 9. Desventajas

- **Estado global:** cualquier componente con acceso a la instancia puede modificar su estado compartido si la API lo permite.
- **Acoplamiento:** los consumidores dependen de una forma concreta de obtener la instancia.
- **Pruebas más difíciles:** puede ser complicado sustituir el Singleton por una implementación simulada (*mock*) durante las pruebas.
- **Concurrencia:** compartir una instancia mutable exige gestionar correctamente las operaciones concurrentes.
- **Ciclo de vida rígido:** el patrón dificulta la creación de varias instancias independientes en distintos contextos.
- **Dependencias ocultas:** una clase puede acceder directamente al Singleton sin declarar esa dependencia en su constructor.
- **Problemas en sistemas distribuidos:** una instancia única en una JVM no garantiza una instancia única entre múltiples procesos, contenedores o servidores.
- **Riesgo de abuso:** puede convertirse en una alternativa improvisada a una arquitectura de dependencias bien definida.

## 10. Cuándo utilizar Singleton

Puede ser apropiado cuando:

- Debe existir una única instancia dentro de un ámbito bien definido.
- El componente mantiene un estado realmente compartido.
- Se necesita un punto de coordinación común.
- El ciclo de vida de la instancia debe ser controlado de manera explícita.
- La solución no introduce un acoplamiento global perjudicial.

### Ejemplos potenciales

- Un componente inmutable que mantiene metadatos compartidos.
- Un registro de configuración de solo lectura, si no existe un mecanismo de configuración más adecuado.
- Un gestor interno que debe ser único dentro de una aplicación pequeña.
- Un componente de coordinación cuyo ámbito de unicidad esté claramente definido.

### Cuándo evitarlo

Conviene evitar Singleton cuando:

- La clase solo necesita ser compartida, pero no requiere una identidad única.
- Se necesitan instancias independientes para pruebas, usuarios, peticiones o entornos.
- El estado compartido puede provocar condiciones de carrera.
- El framework ya ofrece un contenedor de dependencias con gestión del ciclo de vida.
- La unicidad debe mantenerse entre varios procesos o servidores.
- Se está utilizando únicamente para evitar pasar dependencias de manera explícita.

## 11. Singleton y concurrencia

La concurrencia es uno de los aspectos más importantes de este patrón.

Que una clase tenga una única instancia no implica que sus métodos sean seguros para ejecutarse desde varios hilos.

Por ejemplo:

```java
public class ContadorSingleton {

    private static final ContadorSingleton INSTANCIA =
        new ContadorSingleton();

    private int contador = 0;

    private ContadorSingleton() {
    }

    public static ContadorSingleton getInstancia() {
        return INSTANCIA;
    }

    public void incrementar() {
        contador++;
    }

    public int getContador() {
        return contador;
    }
}
```

Aunque la instancia es única, la operación `contador++` no es atómica. Si varios hilos la ejecutan simultáneamente, pueden perderse incrementos.

Para un contador concurrente, se podría utilizar `AtomicInteger`:

```java
import java.util.concurrent.atomic.AtomicInteger;

public class ContadorSingleton {

    private static final ContadorSingleton INSTANCIA =
        new ContadorSingleton();

    private final AtomicInteger contador =
        new AtomicInteger(0);

    private ContadorSingleton() {
    }

    public static ContadorSingleton getInstancia() {
        return INSTANCIA;
    }

    public void incrementar() {
        contador.incrementAndGet();
    }

    public int getContador() {
        return contador.get();
    }
}
```

Esta versión hace atómica la operación de incremento. Para estados más complejos, puede ser necesaria una estrategia de sincronización distinta.

**Idea clave:** la unicidad de la instancia y la seguridad de sus operaciones son dos propiedades diferentes.

## 12. Singleton y los contenedores de inyección de dependencias

En aplicaciones modernas, especialmente en sistemas basados en frameworks, no siempre es necesario implementar Singleton manualmente.

Un contenedor de inyección de dependencias puede gestionar el ciclo de vida de un componente y reutilizar una misma instancia dentro de un ámbito determinado.

Por ejemplo, en Spring:

```java
import org.springframework.stereotype.Service;

@Service
public class ConfiguracionService {

    public String obtenerIdiomaPredeterminado() {
        return "es";
    }
}
```

Por defecto, Spring utiliza el ámbito `singleton` para sus beans: mantiene una instancia compartida por cada bean dentro de un `ApplicationContext`.

Este comportamiento no equivale a garantizar una única instancia en toda la JVM. Diferentes contextos de Spring pueden administrar instancias diferentes.

Además, el ámbito singleton de Spring no convierte automáticamente todos los campos o métodos del bean en seguros para concurrencia. Los servicios compartidos suelen diseñarse sin estado mutable por petición.

La inyección de dependencias ofrece ventajas como:

- Declaración explícita de dependencias.
- Sustitución de implementaciones en pruebas.
- Gestión centralizada del ciclo de vida.
- Mayor flexibilidad para utilizar distintos ámbitos.

Por este motivo, en aplicaciones empresariales suele ser preferible dejar la gestión de instancias al contenedor cuando ya se utiliza uno.

## 13. Relación con los principios SOLID

Singleton puede entrar en tensión con algunos principios SOLID si se utiliza indiscriminadamente.

### Single Responsibility Principle (SRP)

Una clase debería tener una responsabilidad bien definida. Si, además de su función principal, controla su propia creación y actúa como repositorio de estado global, puede acumular responsabilidades.

### Open/Closed Principle (OCP)

El uso directo de una clase Singleton concreta puede dificultar la sustitución o ampliación de su comportamiento. Una abstracción y una dependencia inyectada pueden ofrecer más flexibilidad.

### Dependency Inversion Principle (DIP)

Si los consumidores acceden directamente a `Configuracion.getInstancia()`, quedan vinculados a una implementación concreta y a su mecanismo global de acceso.

Una alternativa consiste en declarar una interfaz y proporcionar la implementación mediante inyección de dependencias.

### Testabilidad

Aunque la testabilidad no es un principio SOLID independiente, es una consecuencia práctica importante del diseño desacoplado. Las dependencias explícitas suelen ser más fáciles de sustituir y controlar durante las pruebas.

Singleton no viola necesariamente SOLID por definición, pero su uso como estado global puede dificultar su aplicación.

## 14. Comparación con otros patrones creacionales

| Patrón | Objetivo principal | Diferencia respecto a Singleton |
|---|---|---|
| Factory | Encapsular la creación y selección de objetos. | Se centra en decidir qué objeto crear, no en imponer una instancia única. |
| Builder | Construir objetos paso a paso. | Se centra en configurar objetos complejos. |
| Prototype | Crear objetos mediante la copia de una instancia existente. | Busca facilitar la clonación, no limitar el número de instancias. |
| Abstract Factory | Crear familias de objetos relacionados. | Se centra en la creación de productos compatibles. |
| Singleton | Controlar la creación de una única instancia dentro de un ámbito. | Se centra en la unicidad y el acceso compartido. |

Singleton puede combinarse con otros patrones, aunque es importante evitar introducir una instancia global cuando la creación y gestión de dependencias ya están resueltas por otro mecanismo.

## 15. Buenas prácticas

1. **Justificar la unicidad:** definir por qué debe existir una sola instancia y cuál es exactamente su ámbito.
2. **Preferir implementaciones seguras frente a concurrencia:** en Java, considerar la inicialización anticipada, el patrón *Holder* o un `enum`, según las necesidades.
3. **Evitar el estado mutable global:** siempre que sea posible, preferir instancias inmutables o componentes sin estado.
4. **Separar unicidad y sincronización:** proteger explícitamente las operaciones que necesiten garantías de concurrencia.
5. **No ocultar dependencias innecesariamente:** utilizar inyección de dependencias cuando aporte flexibilidad y claridad.
6. **No convertirlo en una solución universal:** una clase compartida no siempre necesita ser Singleton.
7. **Definir el ámbito de la instancia:** distinguir entre una JVM, un contenedor, un contexto de aplicación y un sistema distribuido.
8. **Considerar el ciclo de vida:** evaluar si la inicialización diferida o anticipada es más apropiada.
9. **Facilitar las pruebas:** mantener las dependencias sustituibles cuando se necesite aislar componentes.
10. **Evitar la complejidad prematura:** utilizar la implementación más sencilla que cumpla los requisitos reales.

## 16. Conclusión

Singleton es un patrón creacional cuyo propósito es controlar la creación de instancias y proporcionar acceso compartido a un único objeto dentro de un ámbito determinado.

Puede resultar útil para componentes que realmente requieren una identidad compartida y un ciclo de vida único. En Java existen varias implementaciones, entre ellas la inicialización anticipada, la inicialización diferida, el patrón *Holder* y el uso de `enum`.

Sin embargo, Singleton también introduce riesgos relacionados con el estado global, el acoplamiento, la concurrencia y la testabilidad. Por ello, debe utilizarse cuando la unicidad sea un requisito real, no simplemente como mecanismo para acceder cómodamente a un objeto.

En aplicaciones modernas, los contenedores de inyección de dependencias pueden proporcionar una alternativa más flexible para gestionar el ciclo de vida de los componentes.

Singleton es especialmente interesante para comparar con Factory y Builder, porque los tres son creacionales, pero resuelven problemas distintos: Factory decide qué objeto crear, Builder define cómo construirlo y Singleton controla cuántas instancias deben existir.

En definitiva, **Singleton permite controlar la existencia de una instancia compartida, pero su uso correcto depende de definir claramente su ámbito, su estado y sus requisitos de concurrencia**.

## 17. Referencias

- [Refactoring Guru — Singleton](https://refactoring.guru/design-patterns/singleton)
- [SourceMaking — Singleton Design Pattern](https://sourcemaking.com/design_patterns/singleton)
- [Oracle Java Documentation — Class Initialization](https://docs.oracle.com/javase/specs/jls/se21/html/jls-12.html)
- [Spring Framework — Bean Scopes](https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html)
- Joshua Bloch, *Effective Java*, tercera edición. Capítulo sobre creación y gestión de objetos.
