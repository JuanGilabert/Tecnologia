# Patrón de diseño estructural: Composite (Composición)

## 1. Introducción

El patrón de diseño **Composite (Composición)** es un patrón estructural que permite organizar objetos individuales y grupos de objetos dentro de una estructura jerárquica, normalmente en forma de árbol, y tratarlos de manera uniforme.

Su objetivo principal es **permitir que el cliente utilice objetos individuales y composiciones de objetos a través de una misma interfaz**, sin necesidad de conocer si está trabajando con un elemento simple o con un grupo de elementos.

Este patrón resulta especialmente útil cuando un sistema contiene estructuras jerárquicas en las que algunos objetos representan elementos individuales y otros representan conjuntos de esos elementos.

### Ejemplo conceptual

Imaginemos que estamos desarrollando un explorador de archivos.

En un sistema de archivos podemos encontrar:

- Archivos individuales.
- Carpetas que contienen archivos.
- Carpetas que contienen otras carpetas.
- Estructuras con varios niveles de anidamiento.

Una solución sencilla podría utilizar una clase para los archivos y otra para las carpetas. Sin embargo, si cada operación tuviera que distinguir entre ambos tipos, el código cliente terminaría lleno de condiciones.

Composite permite definir una interfaz común para archivos y carpetas, de forma que ambos puedan tratarse mediante las mismas operaciones.

Por ejemplo:

```text
Sistema de archivos
└── Documentos
    ├── informe.pdf
    ├── notas.txt
    └── Proyectos
        ├── proyectoA.java
        └── proyectoB.java
```

El cliente puede solicitar información sobre una carpeta y obtener un resultado que incluya sus archivos y subcarpetas, sin tener que gestionar manualmente cada nivel de la estructura.

---

## 2. Problema que resuelve

Composite resuelve el problema de **tratar de forma uniforme objetos individuales y grupos de objetos que forman una jerarquía**.

Este problema aparece cuando tenemos una estructura de árbol en la que los elementos pueden contener otros elementos del mismo tipo general.

### Ejemplo sin utilizar Composite

Imaginemos una aplicación que gestiona elementos gráficos:

- Un círculo.
- Un rectángulo.
- Un grupo de figuras.

Cada figura puede tener una operación para dibujarse, pero un grupo debe recorrer todos sus elementos y dibujarlos individualmente.

Sin una abstracción común, el cliente podría necesitar comprobar el tipo de cada elemento:

```java
if (elemento instanceof Circulo) {
    // Dibujar círculo
} else if (elemento instanceof Rectangulo) {
    // Dibujar rectángulo
} else if (elemento instanceof GrupoFiguras) {
    // Recorrer y dibujar cada figura del grupo
}
```

Este enfoque presenta varios inconvenientes:

- El cliente conoce las clases concretas.
- Cada nuevo tipo de figura puede obligar a modificar las condiciones.
- Las estructuras anidadas requieren lógica adicional.
- Las operaciones sobre grupos se repiten en distintos lugares.

**Composite permite solucionar este problema mediante una interfaz común para los objetos simples y los objetos compuestos.**

---

## 3. Solución propuesta

La solución consiste en definir una interfaz común que puedan implementar tanto los objetos individuales como los grupos.

El patrón suele incluir los siguientes elementos:

1. **Component:** define las operaciones comunes para los objetos individuales y compuestos.
2. **Leaf:** representa un objeto individual que no contiene otros componentes.
3. **Composite:** representa un objeto que contiene otros componentes y delega las operaciones en ellos.
4. **Client:** trabaja con la interfaz común sin necesitar conocer la estructura interna de cada elemento.

### Diagrama conceptual

```text
                   Cliente
                      |
                      v
                  Component
                      ^
                      |
             +--------+--------+
             |                 |
            Leaf            Composite
                               |
                               | contiene
                               v
                          Component
                               ^
                               |
                     +---------+---------+
                     |                   |
                    Leaf              Composite
```

El aspecto fundamental es que un objeto compuesto puede contener otros objetos que implementan la misma interfaz, incluidos otros objetos compuestos.

Esto permite construir estructuras jerárquicas con tantos niveles como necesite la aplicación.

---

## 4. Implementación completa en Java

Para entender el patrón, utilizaremos el ejemplo de un sistema de archivos con archivos y carpetas.

### 4.1. Interfaz común: Component

Esta interfaz define las operaciones que estarán disponibles tanto para los archivos individuales como para las carpetas.

```java
public interface SistemaArchivo {

    String obtenerNombre();

    long obtenerTamano();
}
```

El cliente puede solicitar el nombre y el tamaño de cualquier elemento sin conocer si se trata de un archivo o una carpeta.

En este ejemplo, el tamaño de una carpeta será la suma de los tamaños de sus elementos descendientes.

### 4.2. Objeto individual: Leaf

La clase `Archivo` representa un archivo que no contiene otros elementos.

```java
public class Archivo implements SistemaArchivo {

    private final String nombre;
    private final long tamano;

    public Archivo(String nombre, long tamano) {
        if (nombre == null || nombre.isBlank()) {
            throw new IllegalArgumentException(
                "El nombre no puede estar vacío"
            );
        }

        if (tamano < 0) {
            throw new IllegalArgumentException(
                "El tamaño no puede ser negativo"
            );
        }

        this.nombre = nombre;
        this.tamano = tamano;
    }

    @Override
    public String obtenerNombre() {
        return nombre;
    }

    @Override
    public long obtenerTamano() {
        return tamano;
    }
}
```

La clase `Archivo` es una hoja del árbol, ya que no contiene otros objetos `SistemaArchivo`.

Su tamaño se obtiene directamente del valor almacenado.

### 4.3. Objeto compuesto: Composite

La clase `Carpeta` puede contener archivos y otras carpetas.

```java
import java.util.ArrayList;
import java.util.List;

public class Carpeta implements SistemaArchivo {

    private final String nombre;
    private final List<SistemaArchivo> elementos =
        new ArrayList<>();

    public Carpeta(String nombre) {
        if (nombre == null || nombre.isBlank()) {
            throw new IllegalArgumentException(
                "El nombre no puede estar vacío"
            );
        }

        this.nombre = nombre;
    }

    public void agregar(SistemaArchivo elemento) {
        if (elemento == null) {
            throw new IllegalArgumentException(
                "El elemento no puede ser null"
            );
        }

        elementos.add(elemento);
    }

    public boolean eliminar(SistemaArchivo elemento) {
        return elementos.remove(elemento);
    }

    @Override
    public String obtenerNombre() {
        return nombre;
    }

    @Override
    public long obtenerTamano() {
        long total = 0;

        for (SistemaArchivo elemento : elementos) {
            total = Math.addExact(
                total,
                elemento.obtenerTamano()
            );
        }

        return total;
    }
}
```

La clase `Carpeta` contiene una lista de objetos `SistemaArchivo`.

Esto significa que puede almacenar tanto archivos como otras carpetas, ya que todos implementan la misma interfaz.

El método `obtenerTamano()` recorre los elementos contenidos y suma sus tamaños. Si uno de esos elementos es otra carpeta, su propia implementación calcula recursivamente el tamaño de su contenido.

El uso de `Math.addExact()` permite detectar un posible desbordamiento de un entero de tipo `long`.

### 4.4. Cliente

El cliente utiliza la interfaz `SistemaArchivo` sin distinguir entre hojas y compuestos.

```java
public class ExploradorArchivos {

    public void mostrarInformacion(SistemaArchivo elemento) {
        System.out.println(
            "Nombre: " + elemento.obtenerNombre()
        );

        System.out.println(
            "Tamaño: " + elemento.obtenerTamano() + " bytes"
        );
    }
}
```

La clase `ExploradorArchivos` no necesita comprobar si recibe un `Archivo` o una `Carpeta`.

### 4.5. Clase principal

```java
public class Main {

    public static void main(String[] args) {

        Archivo informe = new Archivo("informe.pdf", 1200);
        Archivo notas = new Archivo("notas.txt", 300);

        Archivo proyectoA =
            new Archivo("proyectoA.java", 800);

        Archivo proyectoB =
            new Archivo("proyectoB.java", 700);

        Carpeta proyectos = new Carpeta("Proyectos");
        proyectos.agregar(proyectoA);
        proyectos.agregar(proyectoB);

        Carpeta documentos = new Carpeta("Documentos");
        documentos.agregar(informe);
        documentos.agregar(notas);
        documentos.agregar(proyectos);

        ExploradorArchivos explorador =
            new ExploradorArchivos();

        explorador.mostrarInformacion(informe);
        explorador.mostrarInformacion(proyectos);
        explorador.mostrarInformacion(documentos);
    }
}
```

Salida aproximada:

```text
Nombre: informe.pdf
Tamaño: 1200 bytes

Nombre: Proyectos
Tamaño: 1500 bytes

Nombre: Documentos
Tamaño: 3000 bytes
```

### 4.6. ¿Qué hemos conseguido?

Hemos construido una jerarquía de objetos donde los archivos y las carpetas comparten la interfaz `SistemaArchivo`.

El cliente puede solicitar el tamaño de un archivo o de una carpeta utilizando exactamente la misma operación.

La diferencia es que:

- Un archivo devuelve su tamaño almacenado.
- Una carpeta calcula el tamaño de los elementos que contiene.
- Una carpeta puede contener otras carpetas, permitiendo el cálculo recursivo.

De esta manera, la estructura puede crecer sin obligar al cliente a conocer todos los tipos concretos de elementos.

**Nota:** este ejemplo es una representación simplificada de un sistema de archivos. Un sistema real también tendría que considerar enlaces, permisos, archivos dispersos, unidades de almacenamiento y posibles ciclos o referencias repetidas.

---

## 5. Estructura general del patrón

La estructura clásica de Composite puede representarse mediante el siguiente ejemplo genérico:

```java
import java.util.ArrayList;
import java.util.List;

// Component
public interface Componente {

    void ejecutar();
}

// Leaf
public class Hoja implements Componente {

    @Override
    public void ejecutar() {
        System.out.println("Ejecutando hoja");
    }
}

// Composite
public class Compuesto implements Componente {

    private final List<Componente> hijos =
        new ArrayList<>();

    public void agregar(Componente componente) {
        if (componente == null) {
            throw new IllegalArgumentException(
                "El componente no puede ser null"
            );
        }

        hijos.add(componente);
    }

    public void eliminar(Componente componente) {
        hijos.remove(componente);
    }

    @Override
    public void ejecutar() {
        for (Componente hijo : hijos) {
            hijo.ejecutar();
        }
    }
}

// Client
public class Main {

    public static void main(String[] args) {

        Componente hoja1 = new Hoja();
        Componente hoja2 = new Hoja();

        Compuesto grupo = new Compuesto();
        grupo.agregar(hoja1);
        grupo.agregar(hoja2);

        grupo.ejecutar();
    }
}
```

La clase `Compuesto` implementa la misma interfaz que `Hoja`, pero delega la operación en todos sus componentes hijos.

Así, el cliente puede invocar `ejecutar()` sobre una hoja o sobre un compuesto sin modificar su código.

En un proyecto Java, las clases públicas de este ejemplo deberían estar en archivos independientes.

---

## 6. Tipos de implementación de Composite

Existen dos enfoques habituales para diseñar la interfaz del componente.

### 6.1. Interfaz común con operaciones de gestión

En este enfoque, la interfaz común incluye tanto las operaciones propias del componente como operaciones para gestionar hijos.

Conceptualmente:

```java
public interface Componente {

    void ejecutar();

    void agregar(Componente componente);

    void eliminar(Componente componente);
}
```

Las hojas no pueden gestionar hijos, por lo que tendrían que rechazar estas operaciones o implementar un comportamiento no aplicable.

**Ventaja:** el cliente puede gestionar todos los elementos utilizando la misma interfaz.

**Desventaja:** las hojas pueden verse obligadas a ofrecer operaciones que no tienen sentido para ellas.

### 6.2. Interfaz común y gestión exclusiva en Composite

En este enfoque, la interfaz común solo define las operaciones que realmente comparten hojas y compuestos.

```java
public interface Componente {

    void ejecutar();
}
```

La clase compuesta ofrece los métodos `agregar()` y `eliminar()` directamente.

**Ventaja:** la interfaz es más pequeña y cada clase expone únicamente las operaciones que puede realizar.

**Desventaja:** el código que necesita modificar la estructura debe conocer que está trabajando con un compuesto o utilizar una interfaz adicional de gestión.

Para muchos diseños Java, el segundo enfoque suele ser más claro porque evita que una hoja implemente operaciones que no puede soportar.

---

## 7. Ejemplo práctico: estructura de una organización

Composite también puede utilizarse para representar jerarquías organizativas.

Imaginemos una empresa formada por empleados individuales y departamentos que contienen otros empleados o departamentos.

### 7.1. Interfaz común

```java
public interface UnidadOrganizativa {

    String obtenerNombre();

    double obtenerCosteSalarial();
}
```

### 7.2. Hoja: empleado

```java
public class Empleado implements UnidadOrganizativa {

    private final String nombre;
    private final double salario;

    public Empleado(String nombre, double salario) {
        if (nombre == null || nombre.isBlank()) {
            throw new IllegalArgumentException(
                "El nombre no puede estar vacío"
            );
        }

        if (!Double.isFinite(salario) || salario < 0) {
            throw new IllegalArgumentException(
                "El salario debe ser finito y no negativo"
            );
        }

        this.nombre = nombre;
        this.salario = salario;
    }

    @Override
    public String obtenerNombre() {
        return nombre;
    }

    @Override
    public double obtenerCosteSalarial() {
        return salario;
    }
}
```

### 7.3. Composite: departamento

```java
import java.util.ArrayList;
import java.util.List;

public class Departamento
        implements UnidadOrganizativa {

    private final String nombre;
    private final List<UnidadOrganizativa> unidades =
        new ArrayList<>();

    public Departamento(String nombre) {
        if (nombre == null || nombre.isBlank()) {
            throw new IllegalArgumentException(
                "El nombre no puede estar vacío"
            );
        }

        this.nombre = nombre;
    }

    public void agregar(UnidadOrganizativa unidad) {
        if (unidad == null) {
            throw new IllegalArgumentException(
                "La unidad no puede ser null"
            );
        }

        unidades.add(unidad);
    }

    @Override
    public String obtenerNombre() {
        return nombre;
    }

    @Override
    public double obtenerCosteSalarial() {
        double total = 0;

        for (UnidadOrganizativa unidad : unidades) {
            total += unidad.obtenerCosteSalarial();
        }

        return total;
    }
}
```

### 7.4. Uso

```java
public class Main {

    public static void main(String[] args) {

        Empleado ana = new Empleado("Ana", 3000);
        Empleado luis = new Empleado("Luis", 2500);
        Empleado marta = new Empleado("Marta", 3200);

        Departamento desarrollo =
            new Departamento("Desarrollo");

        desarrollo.agregar(ana);
        desarrollo.agregar(luis);

        Departamento tecnologia =
            new Departamento("Tecnología");

        tecnologia.agregar(desarrollo);
        tecnologia.agregar(marta);

        System.out.println(
            "Coste de Desarrollo: "
            + desarrollo.obtenerCosteSalarial()
        );

        System.out.println(
            "Coste de Tecnología: "
            + tecnologia.obtenerCosteSalarial()
        );
    }
}
```

Salida aproximada:

```text
Coste de Desarrollo: 5500.0
Coste de Tecnología: 8700.0
```

El departamento de Tecnología contiene otro departamento y un empleado. Gracias a la interfaz común, puede calcular el coste salarial total de toda la estructura sin conocer los detalles internos de cada unidad.

En una aplicación real, los cálculos monetarios deberían utilizar una representación decimal apropiada, como `BigDecimal`, y contemplar la moneda correspondiente.

---

## 8. Ventajas y desventajas

### Ventajas

1. **Tratamiento uniforme:** el cliente puede trabajar con objetos individuales y compuestos mediante la misma interfaz.
2. **Estructuras recursivas:** permite representar árboles con múltiples niveles de anidamiento.
3. **Extensibilidad:** pueden añadirse nuevos tipos de hojas y compuestos sin cambiar necesariamente el cliente.
4. **Menos condiciones por tipo:** reduce la necesidad de comprobar continuamente si un objeto es una hoja o un compuesto.
5. **Reutilización:** las operaciones comunes pueden definirse en una abstracción compartida.
6. **Organización clara:** representa de forma natural jerarquías como carpetas, departamentos y grupos gráficos.
7. **Recorrido simplificado:** las operaciones pueden delegarse recursivamente en los elementos hijos.

### Desventajas

1. **Restricciones de tipos:** una interfaz común puede resultar demasiado general si las hojas y los compuestos tienen capacidades muy diferentes.
2. **Validación de estructuras:** hay que evitar relaciones inválidas, como ciclos en estructuras que deberían ser árboles.
3. **Gestión de identidad y pertenencia:** si un elemento puede pertenecer a varios grupos, es necesario definir claramente la semántica de esa relación.
4. **Operaciones con comportamientos distintos:** algunas operaciones pueden tener sentido para los compuestos, pero no para las hojas.
5. **Coste de recorrido:** recorrer estructuras grandes puede resultar costoso, especialmente si se realizan cálculos repetidos.
6. **Complejidad innecesaria:** para una colección plana de elementos, una lista convencional puede ser suficiente.

Composite aporta más valor cuando existe una jerarquía real de objetos que deben tratarse de manera uniforme.

---

## 9. Relación con los principios SOLID

### Single Responsibility Principle (SRP)

Cada clase puede centrarse en su responsabilidad:

- Una hoja representa un elemento individual.
- Un compuesto gestiona sus hijos y delega operaciones.
- El cliente utiliza la abstracción común.

Esto evita que el cliente tenga que conocer y gestionar toda la estructura interna.

### Open/Closed Principle (OCP)

Podemos añadir nuevas clases que implementen la interfaz común sin modificar necesariamente el cliente.

Por ejemplo, podríamos incorporar un nuevo tipo de archivo o una nueva unidad organizativa.

No obstante, si añadimos operaciones nuevas a la interfaz común, puede ser necesario modificar todas las implementaciones existentes.

### Liskov Substitution Principle (LSP)

Las hojas y los compuestos deben respetar el contrato de la interfaz común.

Si el cliente solicita el tamaño de un componente, ambas clases deben proporcionar un resultado que respete las expectativas establecidas para esa operación.

Una hoja no debería verse obligada a implementar operaciones de gestión de hijos que no puede soportar. Por eso conviene diseñar cuidadosamente la interfaz compartida.

### Interface Segregation Principle (ISP)

La interfaz común debe contener las operaciones que realmente necesitan tanto las hojas como los compuestos.

Las operaciones exclusivas de los compuestos, como agregar o eliminar hijos, pueden mantenerse en una interfaz o clase específica.

Este enfoque reduce la necesidad de proporcionar métodos que no tienen sentido para ciertos tipos de componentes.

### Dependency Inversion Principle (DIP)

El cliente depende de la abstracción `SistemaArchivo`, no directamente de las clases concretas `Archivo` o `Carpeta`.

Esto permite añadir nuevas implementaciones compatibles sin acoplar el cliente a una clase específica.

---

## 10. Diferencias con otros patrones estructurales

### Composite vs. Decorator

Ambos patrones utilizan composición y pueden implementar una interfaz común, pero persiguen objetivos diferentes.

- **Composite:** organiza objetos individuales y grupos de objetos en una estructura jerárquica.
- **Decorator:** envuelve un objeto para añadirle responsabilidades.

Por ejemplo, una carpeta que contiene archivos es un caso de Composite. Un servicio envuelto por un objeto que añade registro es un caso de Decorator.

### Composite vs. Iterator

- **Composite:** define cómo se organizan los objetos y cómo pueden tratarse de manera uniforme.
- **Iterator:** proporciona una forma de recorrer una colección sin exponer directamente su representación interna.

Ambos pueden utilizarse juntos: Composite representa el árbol y un Iterator permite recorrer sus elementos.

### Composite vs. Chain of Responsibility

- **Composite:** permite ejecutar operaciones sobre una estructura de objetos que contienen otros objetos.
- **Chain of Responsibility:** encadena objetos para que una petición pase de un manejador a otro hasta que se gestione o se alcance el final de la cadena.

Aunque ambos pueden implicar relaciones entre varios objetos, la estructura y la intención son diferentes.

### Composite vs. Bridge

- **Composite:** permite tratar de forma uniforme objetos individuales y grupos.
- **Bridge:** separa una abstracción de su implementación para que ambas evolucionen de forma independiente.

Composite se centra en la composición jerárquica; Bridge, en separar dimensiones de diseño.

---

## 11. Casos de uso habituales

Composite resulta especialmente útil en los siguientes escenarios:

- Sistemas de archivos con carpetas y archivos.
- Interfaces gráficas con controles y contenedores.
- Estructuras organizativas con departamentos y empleados.
- Documentos con secciones, párrafos y elementos anidados.
- Árboles sintácticos en compiladores.
- Estructuras de menús y submenús.
- Sistemas de categorías y subcategorías.
- Modelos de escenas en motores gráficos.
- Estructuras de productos compuestos en aplicaciones comerciales.

### Ejemplo en interfaces gráficas

Una ventana puede contener botones, etiquetas y paneles. Un panel puede contener otros paneles y controles.

Si todos implementan una interfaz común, como `ComponenteVisual`, la ventana puede solicitar que se dibuje toda la estructura sin tener que gestionar manualmente cada nivel.

El panel compuesto delega la operación de dibujado en sus hijos, mientras que los controles individuales realizan su propia representación.

### Ejemplo en árboles sintácticos

Un compilador puede representar una expresión mediante nodos simples y nodos compuestos.

Por ejemplo:

```text
          Multiplicacion
           /          \
       Suma           Numero
       /   \             |
   Numero Numero         3
      |      |
      1      2
```

Los nodos que representan números son hojas. Los nodos de suma y multiplicación son compuestos porque contienen otras expresiones.

Una operación como evaluar la expresión puede delegarse recursivamente a cada nodo.

---

## 12. Buenas prácticas

1. **Define una interfaz común coherente:** incluye las operaciones que tengan sentido para hojas y compuestos.
2. **Evita métodos innecesarios en las hojas:** la gestión de hijos puede exponerse únicamente en los compuestos.
3. **Controla los ciclos:** si la estructura debe ser un árbol, evita que un nodo termine conteniéndose a sí mismo, directa o indirectamente.
4. **Define las reglas de pertenencia:** especifica si un elemento puede pertenecer a varios compuestos o solamente a uno.
5. **Gestiona estructuras grandes:** evalúa el coste de los recorridos recursivos y considera iteraciones explícitas si la profundidad puede ser elevada.
6. **Mantén el cliente desacoplado:** utiliza la interfaz común siempre que no necesites operaciones exclusivas del compuesto.
7. **Valida los elementos añadidos:** evita referencias nulas y relaciones que rompan las reglas de la estructura.
8. **Considera la inmutabilidad:** si la estructura no necesita cambiar después de construirse, puede ser conveniente limitar las operaciones de modificación.
9. **Diseña correctamente las operaciones agregadas:** define cómo se calculan tamaños, costes o resultados cuando existen elementos anidados.
10. **No utilices Composite para todo:** si los elementos forman una lista plana sin relaciones jerárquicas, una colección convencional puede ser más sencilla.

---

## 13. Pruebas recomendadas

Las pruebas deberían verificar tanto el comportamiento de las hojas como el de los compuestos y las estructuras anidadas.

En el ejemplo del sistema de archivos, conviene comprobar que:

- Un archivo devuelve su tamaño almacenado.
- Una carpeta vacía tiene un tamaño total de cero.
- Una carpeta suma correctamente los tamaños de sus archivos.
- Una carpeta con subcarpetas calcula el total de toda la jerarquía.
- Los métodos de modificación añaden y eliminan los elementos esperados.
- Se rechazan los elementos nulos y los valores inválidos.
- Se detectan los desbordamientos si se utiliza `Math.addExact()`.

También conviene probar estructuras profundas y definir un comportamiento adecuado si la aplicación permite jerarquías grandes.

Se pueden utilizar JUnit y dobles de prueba para comprobar las operaciones individuales y el comportamiento recursivo.

---

## 14. Conclusión

El patrón **Composite (Composición)** permite construir estructuras jerárquicas de objetos y tratar los elementos individuales y los grupos mediante una interfaz común.

Su principal beneficio consiste en simplificar el código cliente y facilitar la representación de estructuras recursivas, como sistemas de archivos, organizaciones, documentos y componentes gráficos.

En Java, suele implementarse mediante una interfaz común, clases hoja que representan objetos individuales y clases compuestas que contienen una colección de componentes y delegan las operaciones en ellos.

Es especialmente útil cuando los objetos forman una jerarquía real y las operaciones deben aplicarse de manera uniforme a elementos individuales o a grupos completos.

La idea fundamental es: **tratar un objeto individual y un grupo de objetos de la misma manera mediante una abstracción común, permitiendo construir estructuras jerárquicas de forma recursiva.**

---

## 15. Referencias

- **Design Patterns: Elements of Reusable Object-Oriented Software**, de Erich Gamma, Richard Helm, Ralph Johnson y John Vlissides (Gang of Four).
- Refactoring.Guru — Composite: https://refactoring.guru/design-patterns/composite
- Java Documentation: https://docs.oracle.com/en/java/
- SourceMaking — Composite Design Pattern: https://sourcemaking.com/design_patterns/composite
