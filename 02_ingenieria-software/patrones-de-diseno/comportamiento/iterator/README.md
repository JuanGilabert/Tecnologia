# Patrón de Diseño Iterator (Iterador)

## 1. Introducción

El patrón de diseño **Iterator** pertenece a la categoría de los patrones de comportamiento (*Behavioral Design Patterns*) de GoF (*Gang of Four*).

Su objetivo es proporcionar una forma de recorrer los elementos de una colección de manera secuencial **sin exponer su estructura interna**.

Gracias a este patrón, el código cliente puede acceder a los elementos de una colección sin necesidad de conocer cómo están almacenados internamente, ya sea en una lista, un árbol, una estructura de datos personalizada o cualquier otra representación.

En Java, este patrón está ampliamente integrado en el lenguaje y en su biblioteca estándar mediante las interfaces `Iterator`, `Iterable` y las estructuras de datos del Java Collections Framework.

### Objetivos principales

- Encapsular la lógica de recorrido de una colección.
- Separar la estructura de datos del algoritmo que la recorre.
- Permitir diferentes formas de recorrer una misma colección.
- Facilitar la reutilización del código cliente.
- Evitar que el cliente dependa de la implementación interna de una colección.

---

## 2. Problema que resuelve

Imaginemos que estamos desarrollando una aplicación que gestiona una biblioteca. Necesitamos recorrer los libros almacenados en diferentes estructuras de datos.

Podríamos tener:

- Una lista de libros.
- Un árbol de categorías.
- Una colección personalizada.
- Una estructura que obtiene los libros desde una base de datos.

Sin el patrón Iterator, el código cliente podría tener que conocer la estructura interna de cada colección para recorrer sus elementos.

Por ejemplo:

```java
for (int i = 0; i < libros.size(); i++) {
    System.out.println(libros.get(i));
}
```

Este código funciona con estructuras que permiten acceder a los elementos mediante índices, pero no es aplicable directamente a cualquier estructura, como un conjunto o un árbol.

Otra alternativa sería que cada colección ofreciera un método de recorrido diferente:

```java
lista.recorrerLibros();
arbol.recorrerLibros();
coleccionPersonalizada.recorrerLibros();
```

Esto obligaría al cliente a conocer las particularidades de cada implementación.

### Problemas principales

1. **Acoplamiento:** el código cliente depende de la estructura interna de la colección.
2. **Duplicación:** cada estructura puede terminar implementando su propia lógica de recorrido.
3. **Falta de flexibilidad:** cambiar la estructura de almacenamiento puede obligar a modificar el código cliente.
4. **Dificultad para ofrecer distintos recorridos:** implementar recorridos alternativos puede complicar la colección original.

El patrón Iterator resuelve estos problemas proporcionando una interfaz común para recorrer los elementos.

---

## 3. Solución propuesta

El patrón Iterator introduce un objeto iterador que se encarga de recorrer una colección y controlar la posición actual durante el recorrido.

El cliente utiliza el iterador para obtener los elementos uno a uno, sin conocer cómo están almacenados.

Las operaciones habituales de un iterador son:

- `hasNext()`: comprueba si quedan elementos por recorrer.
- `next()`: devuelve el siguiente elemento y avanza la posición.
- `remove()`: en determinadas implementaciones, permite eliminar el último elemento devuelto, aunque es una operación opcional.

La interfaz exacta depende del lenguaje y de la implementación. En Java, `Iterator<E>` define principalmente `hasNext()` y `next()`, además de `remove()` como operación opcional.

### Idea fundamental

El cliente deja de recorrer directamente la estructura de datos y delega el recorrido en un objeto especializado.

De esta manera, la colección y la lógica que la recorre quedan separadas.

---

## 4. Estructura del patrón

El patrón Iterator clásico está formado por los siguientes participantes:

### 4.1. Iterator

Define la interfaz común para recorrer los elementos.

En Java, esta responsabilidad corresponde habitualmente a `Iterator<E>`.

### 4.2. ConcreteIterator

Implementa el recorrido concreto de una colección y mantiene el estado necesario para saber cuál es el siguiente elemento.

Por ejemplo, puede almacenar una referencia a la colección y un índice que indique la posición actual.

### 4.3. Aggregate

Define una interfaz para crear u obtener iteradores.

En Java, esta función suele estar representada por `Iterable<E>`, que declara el método `iterator()`.

### 4.4. ConcreteAggregate

Es la colección concreta que contiene los elementos y proporciona el iterador apropiado.

Puede ser una lista, un conjunto o una colección personalizada.

### Diagrama de clases conceptual

```text
             Cliente
                |
                v
       +----------------+
       |   Iterator<E>  |
       +----------------+
       | hasNext()      |
       | next()         |
       +----------------+
                ^
                |
       +-------------------+
       | ConcreteIterator  |
       +-------------------+
       | posicionActual    |
       +-------------------+
                |
                | recorre
                v
       +-------------------+
       | ConcreteAggregate|
       +-------------------+
       | elementos         |
       | iterator()        |
       +-------------------+
```

En Java, `Iterable` representa el contrato de la colección que permite obtener un iterador, mientras que `Iterator` representa el contrato para recorrer sus elementos.

---

## 5. Implementación en Java

Vamos a implementar el patrón Iterator con una colección personalizada de libros.

### 5.1. Crear la clase Libro

Representa un libro de la biblioteca.

```java
public class Libro {

    private final String titulo;
    private final String autor;

    public Libro(String titulo, String autor) {
        this.titulo = titulo;
        this.autor = autor;
    }

    public String getTitulo() {
        return titulo;
    }

    public String getAutor() {
        return autor;
    }

    @Override
    public String toString() {
        return titulo + " - " + autor;
    }
}
```

La clase `Libro` representa los datos que almacenará nuestra colección.

### 5.2. Crear la colección personalizada

Implementaremos una colección de libros que utiliza internamente un array.

La colección ofrecerá un método para añadir libros y otro para obtener un iterador.

```java
import java.util.Iterator;
import java.util.NoSuchElementException;

public class Biblioteca implements Iterable<Libro> {

    private final Libro[] libros;
    private int cantidad;

    public Biblioteca(int capacidad) {
        if (capacidad < 0) {
            throw new IllegalArgumentException(
                "La capacidad no puede ser negativa"
            );
        }

        this.libros = new Libro[capacidad];
        this.cantidad = 0;
    }

    public void agregarLibro(Libro libro) {
        if (libro == null) {
            throw new IllegalArgumentException(
                "El libro no puede ser null"
            );
        }

        if (cantidad >= libros.length) {
            throw new IllegalStateException(
                "La biblioteca está llena"
            );
        }

        libros[cantidad] = libro;
        cantidad++;
    }

    @Override
    public Iterator<Libro> iterator() {
        return new Iterator<Libro>() {

            private int posicion = 0;

            @Override
            public boolean hasNext() {
                return posicion < cantidad;
            }

            @Override
            public Libro next() {
                if (!hasNext()) {
                    throw new NoSuchElementException(
                        "No quedan libros por recorrer"
                    );
                }

                return libros[posicion++];
            }
        };
    }
}
```

**¿Qué está ocurriendo?**

- `Biblioteca` implementa `Iterable<Libro>`, por lo que permite obtener un iterador.
- El método `iterator()` devuelve una implementación de `Iterator<Libro>`.
- La variable `posicion` mantiene el estado del recorrido.
- `hasNext()` indica si existen más libros.
- `next()` devuelve el siguiente libro e incrementa la posición.
- Si se intenta obtener un elemento cuando ya no quedan, se lanza `NoSuchElementException`, como establece el contrato de `Iterator`.

La implementación utiliza una clase anónima para simplificar el ejemplo. También podríamos crear una clase independiente llamada `BibliotecaIterator`.

### 5.3. Utilizar la colección desde el cliente

El cliente no necesita conocer que la biblioteca almacena los libros en un array.

```java
public class Main {

    public static void main(String[] args) {

        Biblioteca biblioteca = new Biblioteca(3);

        biblioteca.agregarLibro(
            new Libro("Clean Code", "Robert C. Martin")
        );

        biblioteca.agregarLibro(
            new Libro("Effective Java", "Joshua Bloch")
        );

        biblioteca.agregarLibro(
            new Libro("Design Patterns", "GoF")
        );

        for (Libro libro : biblioteca) {
            System.out.println(libro);
        }
    }
}
```

Salida aproximada:

```text
Clean Code - Robert C. Martin
Effective Java - Joshua Bloch
Design Patterns - GoF
```

La instrucción `for (Libro libro : biblioteca)` utiliza internamente el método `iterator()` y las operaciones del iterador para recorrer los elementos.

El cliente no necesita acceder al array ni controlar manualmente la posición.

---

## 6. Implementación explícita con Iterator

También podemos recorrer la colección utilizando directamente el iterador, sin recurrir al bucle `for-each`.

```java
import java.util.Iterator;

public class Main {

    public static void main(String[] args) {

        Biblioteca biblioteca = new Biblioteca(2);

        biblioteca.agregarLibro(
            new Libro("Clean Code", "Robert C. Martin")
        );

        biblioteca.agregarLibro(
            new Libro("Refactoring", "Martin Fowler")
        );

        Iterator<Libro> iterator = biblioteca.iterator();

        while (iterator.hasNext()) {
            Libro libro = iterator.next();
            System.out.println(libro.getTitulo());
        }
    }
}
```

Este enfoque permite controlar explícitamente el recorrido.

Es útil cuando necesitamos detenernos en determinadas condiciones, ejecutar operaciones adicionales entre elementos o consumir una colección paso a paso.

---

## 7. Diferentes iteradores para una misma colección

Una ventaja importante del patrón es que una colección puede proporcionar distintas formas de recorrido.

Por ejemplo, una biblioteca podría permitir:

- Recorrido en orden de inserción.
- Recorrido inverso.
- Recorrido filtrado por categoría.
- Recorrido de los libros más recientes.

Cada iterador encapsularía su propia lógica.

### Ejemplo conceptual: iterador inverso

Si los libros están almacenados en un array, un iterador inverso podría empezar por el último elemento:

```java
Iterator<Libro> iteradorInverso = crearIteradorInverso();
```

La implementación de `crearIteradorInverso()` mantendría una posición inicial igual al último índice válido y la reduciría en cada llamada a `next()`.

La colección no necesitaría modificar su estructura interna para ofrecer ese recorrido.

**Importante:** el método `crearIteradorInverso()` es ilustrativo; no forma parte de la clase `Biblioteca` implementada anteriormente. Habría que añadir su implementación para poder utilizarlo.

---

## 8. Uso del Iterator integrado en Java

En aplicaciones reales, muchas veces no es necesario implementar el patrón manualmente.

El Java Collections Framework ya proporciona iteradores para colecciones como `ArrayList`, `HashSet` y `LinkedList`.

Ejemplo:

```java
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

public class Main {

    public static void main(String[] args) {

        List<String> nombres = new ArrayList<>();

        nombres.add("Ana");
        nombres.add("Carlos");
        nombres.add("Lucía");

        Iterator<String> iterator = nombres.iterator();

        while (iterator.hasNext()) {
            String nombre = iterator.next();
            System.out.println(nombre);
        }
    }
}
```

También podemos utilizar el bucle `for-each`:

```java
for (String nombre : nombres) {
    System.out.println(nombre);
}
```

Ambas alternativas permiten recorrer la colección sin depender de su implementación concreta.

### ¿Cuándo implementar un iterador propio?

Tiene sentido cuando:

- Estamos desarrollando una estructura de datos personalizada.
- Queremos ofrecer un orden de recorrido diferente al predeterminado.
- Necesitamos recorrer estructuras como árboles o grafos.
- Queremos aplicar filtros o reglas específicas durante el recorrido.
- Necesitamos abstraer una fuente de datos que no ofrece directamente un iterador adecuado.

Para colecciones estándar, lo habitual es reutilizar las implementaciones de Java.

---

## 9. Ventajas del patrón Iterator

### 9.1. Encapsulación

El cliente no necesita conocer cómo están almacenados los elementos.

La colección puede cambiar internamente sin obligar a modificar el código que la recorre, siempre que mantenga su contrato público.

### 9.2. Separación de responsabilidades

La colección se encarga de almacenar los elementos, mientras que el iterador controla el recorrido.

Esto permite mantener cada responsabilidad más localizada.

### 9.3. Reutilización

Diferentes clientes pueden utilizar el mismo iterador sin duplicar la lógica de recorrido.

### 9.4. Flexibilidad

Una misma colección puede proporcionar distintos iteradores para diferentes necesidades.

### 9.5. Interoperabilidad

En Java, implementar `Iterable<E>` permite utilizar la colección con el bucle `for-each` y con numerosas API que esperan objetos iterables.

### 9.6. Código más limpio

El cliente evita gestionar manualmente índices, posiciones y detalles internos de la colección.

---

## 10. Desventajas del patrón Iterator

### 10.1. Complejidad adicional

Para una colección muy sencilla, implementar un iterador propio puede añadir clases y abstracciones innecesarias.

### 10.2. Gestión del estado

Cada iterador mantiene un estado de recorrido. Una implementación incorrecta puede provocar errores al avanzar, repetir elementos o terminar el recorrido.

### 10.3. Consideraciones sobre modificaciones

Modificar una colección mientras se recorre puede tener consecuencias diferentes según la implementación.

Los iteradores de las colecciones estándar de Java suelen ser *fail-fast* cuando detectan ciertas modificaciones estructurales externas, pero este comportamiento es de mejor esfuerzo y no constituye una garantía general de sincronización.

### 10.4. Rendimiento y memoria

Un iterador personalizado puede introducir objetos o lógica adicional. Normalmente el coste es pequeño, pero debe evaluarse cuando se recorren estructuras grandes o se utilizan algoritmos exigentes.

---

## 11. Casos de uso reales

### 11.1. Sistemas de gestión de bibliotecas

Recorrer libros sin conocer si están almacenados en una lista, un array o una estructura más compleja.

### 11.2. Interfaces gráficas

Recorrer componentes, controles o elementos visibles de una interfaz.

### 11.3. Árboles y grafos

Implementar recorridos como profundidad primero (DFS), anchura primero (BFS) o recorrido en orden de un árbol binario.

Cada estrategia de recorrido puede encapsularse en un iterador específico.

### 11.4. Procesamiento de datos

Procesar registros secuencialmente sin cargar necesariamente todos los resultados en una estructura convencional, siempre que el iterador esté diseñado para trabajar con una fuente de datos incremental.

### 11.5. Sistemas de archivos

Recorrer archivos y directorios, encapsulando la lógica necesaria para visitar elementos de una estructura jerárquica.

### 11.6. Aplicaciones empresariales

Recorrer pedidos, clientes, facturas o productos sin acoplar la lógica de negocio a la estructura interna utilizada para almacenarlos.

---

## 12. Relación con los principios SOLID

### Single Responsibility Principle (SRP)

La colección almacena y administra los elementos, mientras que el iterador controla cómo se recorren.

Separar estas responsabilidades facilita el mantenimiento y las pruebas.

### Open/Closed Principle (OCP)

Podemos añadir nuevas formas de recorrido mediante nuevos iteradores sin tener que reescribir necesariamente la colección o los clientes existentes.

### Liskov Substitution Principle (LSP)

Las implementaciones de `Iterator<E>` deben respetar el contrato de la interfaz. Por ejemplo, `next()` debe devolver el siguiente elemento disponible y señalar que no quedan elementos mediante la excepción correspondiente.

### Interface Segregation Principle (ISP)

El cliente depende de una interfaz pequeña y especializada para recorrer elementos, en lugar de necesitar conocer todos los métodos internos de la colección.

### Dependency Inversion Principle (DIP)

El código cliente puede trabajar con abstracciones como `Iterable<E>` e `Iterator<E>` en lugar de depender de una implementación concreta de la colección.

---

## 13. Comparación con otros patrones de diseño

### Iterator vs. Composite

| Iterator | Composite |
|---|---|
| Permite recorrer elementos de una colección o estructura. | Permite tratar objetos individuales y composiciones de objetos de forma uniforme. |
| Se centra en el recorrido. | Se centra en representar jerarquías parte-todo. |
| Puede utilizarse para recorrer un árbol Composite. | Puede beneficiarse de un Iterator para recorrer sus componentes. |

Ambos patrones pueden utilizarse juntos, especialmente cuando es necesario recorrer estructuras jerárquicas.

### Iterator vs. Strategy

| Iterator | Strategy |
|---|---|
| Encapsula la lógica de recorrido. | Encapsula un algoritmo o una estrategia intercambiable. |
| Su interfaz suele exponer operaciones para obtener elementos. | Su interfaz expone la operación que ejecuta el algoritmo. |
| Controla el avance y el estado del recorrido. | Permite cambiar el algoritmo utilizado por un contexto. |

Un iterador puede encapsular un algoritmo de recorrido, pero su finalidad principal es ofrecer acceso secuencial a los elementos.

### Iterator vs. Factory Method

| Iterator | Factory Method |
|---|---|
| Se centra en recorrer elementos. | Se centra en delegar la creación de objetos a métodos que pueden redefinirse. |
| Su resultado permite acceder a los elementos de una colección. | Su resultado es un producto creado por una fábrica. |
| Es un patrón de comportamiento. | Es un patrón creacional. |

Ambos pueden colaborar: una colección puede crear un iterador a través de su método `iterator()`.

---

## 14. Buenas prácticas

- Utilizar `Iterable<E>` cuando una clase deba permitir recorridos mediante `for-each`.
- Utilizar `Iterator<E>` para recorrer elementos sin exponer la estructura interna.
- Mantener la lógica de recorrido dentro del iterador y no duplicarla en el cliente.
- Lanzar `NoSuchElementException` cuando se invoque `next()` y no queden elementos.
- No asumir que todos los iteradores permiten eliminar elementos.
- Evitar modificar estructuralmente una colección mientras se recorre, salvo que su contrato permita expresamente esa operación.
- Preferir las colecciones e iteradores estándar de Java cuando satisfagan las necesidades.
- Crear iteradores propios cuando aporten una capacidad concreta, como recorrer un árbol o aplicar un filtro.
- Documentar el orden del recorrido cuando sea relevante para el comportamiento de la aplicación.

---

## 15. Conclusión

El patrón Iterator es un patrón de comportamiento que permite recorrer una colección sin exponer su representación interna.

Su principal beneficio es separar la lógica de recorrido de la estructura de datos, haciendo que el código cliente sea más flexible, reutilizable y fácil de mantener.

En Java, este patrón está integrado en las interfaces `Iterator` e `Iterable`, por lo que se utiliza de manera habitual incluso cuando no se implementa explícitamente.

Es especialmente útil cuando trabajamos con estructuras de datos personalizadas, recorridos alternativos o estructuras complejas como árboles y grafos.

Iterator se encarga de cómo recorrer una colección, mientras que la colección se encarga de cómo almacenar sus elementos. Esa separación es la esencia del patrón.

En definitiva, **Iterator permite recorrer una colección sin necesidad de conocer cómo está construida internamente**.

---

## 16. Referencias

- *Design Patterns: Elements of Reusable Object-Oriented Software*, Erich Gamma, Richard Helm, Ralph Johnson y John Vlissides.
- Documentación oficial de Java: `Iterator`: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Iterator.html
- Documentación oficial de Java: `Iterable`: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Iterable.html
- Tutorial oficial de Java sobre el bucle `for-each`: https://docs.oracle.com/javase/tutorial/java/nutsandbolts/for.html
