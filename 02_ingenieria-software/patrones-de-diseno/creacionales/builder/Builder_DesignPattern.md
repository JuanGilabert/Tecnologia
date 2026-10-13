# Patrón de Diseño Builder (Creacional)

## 1. Introducción

El patrón de diseño **Builder** es un patrón creacional que permite construir objetos complejos paso a paso, separando el proceso de construcción de la representación final del objeto.

Su objetivo principal es simplificar la creación de objetos que requieren múltiples parámetros, configuraciones opcionales o una secuencia específica de operaciones para su inicialización.

En lugar de utilizar constructores con numerosos argumentos, Builder ofrece una API más clara y flexible para configurar un objeto antes de construirlo.

Este patrón es especialmente útil cuando un objeto tiene muchas propiedades opcionales, diferentes configuraciones posibles o reglas de construcción que conviene encapsular.

## 2. Problema que resuelve

Cuando una clase necesita muchos parámetros para inicializarse, su constructor puede volverse difícil de utilizar y mantener.

Por ejemplo:

```java
Usuario usuario = new Usuario(
    "Carlos",
    "carlos@example.com",
    30,
    true,
    false,
    "Madrid"
);
```

Esta forma de construcción presenta varios inconvenientes:

- **Falta de legibilidad:** resulta difícil recordar qué representa cada argumento.
- **Parámetros opcionales:** obliga a proporcionar valores que quizá no sean necesarios.
- **Constructores sobrecargados:** pueden aparecer múltiples constructores para cubrir distintas combinaciones.
- **Mantenimiento complicado:** añadir nuevas propiedades puede aumentar la complejidad de la clase.
- **Validaciones dispersas:** las reglas de construcción pueden acabar repartidas entre distintos constructores.

Builder aborda estos problemas proporcionando una forma explícita y gradual de configurar el objeto.

## 3. Solución propuesta

El patrón Builder encapsula el proceso de construcción en un objeto constructor que permite establecer las propiedades de manera individual y, finalmente, crear la instancia definitiva.

Por ejemplo:

```java
Usuario usuario = new Usuario.Builder("Carlos")
    .email("carlos@example.com")
    .edad(30)
    .activo(true)
    .ciudad("Madrid")
    .build();
```

Esta alternativa mejora la legibilidad, permite configurar únicamente las propiedades necesarias y centraliza el proceso de construcción.

## 4. Estructura del patrón

Los participantes habituales del patrón Builder son:

- **Producto (Product):** objeto complejo que se desea construir.
- **Builder:** define las operaciones necesarias para configurar el producto.
- **Concrete Builder:** implementa las operaciones de construcción y mantiene el estado necesario.
- **Director (opcional):** coordina los pasos de construcción para obtener una configuración concreta.
- **Cliente:** utiliza el Builder para construir el objeto.

No todas las implementaciones necesitan una clase Director. En muchos lenguajes y frameworks modernos, el cliente configura directamente el Builder y llama a `build()`.

### Diagrama conceptual

```text
                Cliente
                   |
                   v
                Builder
                   |
        +----------+----------+
        |          |          |
        v          v          v
    propiedad  propiedad   build()
                               |
                               v
                            Producto
```

## 5. Ejemplo de implementación en Java

A continuación, se muestra una implementación sencilla en la que un usuario se construye mediante un Builder estático.

### 5.1. Clase Usuario

```java
public class Usuario {

    private final String nombre;
    private final String email;
    private final Integer edad;
    private final boolean activo;
    private final String ciudad;

    private Usuario(Builder builder) {
        this.nombre = builder.nombre;
        this.email = builder.email;
        this.edad = builder.edad;
        this.activo = builder.activo;
        this.ciudad = builder.ciudad;
    }

    public String getNombre() {
        return nombre;
    }

    public String getEmail() {
        return email;
    }

    public Integer getEdad() {
        return edad;
    }

    public boolean isActivo() {
        return activo;
    }

    public String getCiudad() {
        return ciudad;
    }

    public static class Builder {

        private final String nombre;
        private String email;
        private Integer edad;
        private boolean activo;
        private String ciudad;

        public Builder(String nombre) {
            if (nombre == null || nombre.isBlank()) {
                throw new IllegalArgumentException(
                    "El nombre es obligatorio"
                );
            }

            this.nombre = nombre;
        }

        public Builder email(String email) {
            this.email = email;
            return this;
        }

        public Builder edad(Integer edad) {
            if (edad != null && edad < 0) {
                throw new IllegalArgumentException(
                    "La edad no puede ser negativa"
                );
            }

            this.edad = edad;
            return this;
        }

        public Builder activo(boolean activo) {
            this.activo = activo;
            return this;
        }

        public Builder ciudad(String ciudad) {
            this.ciudad = ciudad;
            return this;
        }

        public Usuario build() {
            return new Usuario(this);
        }
    }
}
```

### 5.2. Uso del Builder

```java
public class Main {

    public static void main(String[] args) {

        Usuario usuario = new Usuario.Builder("Carlos")
            .email("carlos@example.com")
            .edad(30)
            .activo(true)
            .ciudad("Madrid")
            .build();

        System.out.println(usuario.getNombre());
        System.out.println(usuario.getEmail());
        System.out.println(usuario.getCiudad());
    }
}
```

### 5.3. Explicación del ejemplo

1. El cliente crea un `Builder` indicando el nombre, que es obligatorio.
2. Cada método permite configurar una propiedad opcional.
3. Los métodos devuelven `this`, lo que permite encadenar llamadas (*method chaining*).
4. El método `build()` crea la instancia definitiva de `Usuario`.
5. El producto utiliza campos `final` y no expone métodos modificadores, por lo que su estado no puede cambiar a través de su API pública después de construirse.

Esta implementación también centraliza determinadas validaciones en el Builder. Si existen reglas que dependen de varias propiedades, conviene validarlas en `build()` antes de crear el producto.

## 6. Variante con Director

En la implementación anterior, el cliente decide directamente qué propiedades configurar. Sin embargo, algunas aplicaciones necesitan construir objetos siguiendo secuencias predefinidas.

Para esos casos puede utilizarse un **Director**, responsable de coordinar la construcción.

Por ejemplo, imaginemos la creación de una configuración de conexión a una base de datos.

```java
public class ConfiguracionBD {

    private final String host;
    private final int puerto;
    private final String baseDatos;
    private final boolean ssl;

    public ConfiguracionBD(
        String host,
        int puerto,
        String baseDatos,
        boolean ssl
    ) {
        this.host = host;
        this.puerto = puerto;
        this.baseDatos = baseDatos;
        this.ssl = ssl;
    }

    public static class Builder {

        private String host;
        private int puerto = 5432;
        private String baseDatos;
        private boolean ssl = true;

        public Builder host(String host) {
            this.host = host;
            return this;
        }

        public Builder puerto(int puerto) {
            this.puerto = puerto;
            return this;
        }

        public Builder baseDatos(String baseDatos) {
            this.baseDatos = baseDatos;
            return this;
        }

        public Builder ssl(boolean ssl) {
            this.ssl = ssl;
            return this;
        }

        public ConfiguracionBD build() {
            if (host == null || host.isBlank()) {
                throw new IllegalStateException(
                    "El host es obligatorio"
                );
            }

            if (baseDatos == null || baseDatos.isBlank()) {
                throw new IllegalStateException(
                    "La base de datos es obligatoria"
                );
            }

            if (puerto < 1 || puerto > 65535) {
                throw new IllegalStateException(
                    "El puerto no es válido"
                );
            }

            return new ConfiguracionBD(
                host,
                puerto,
                baseDatos,
                ssl
            );
        }
    }
}
```

Un Director podría definir configuraciones preestablecidas:

```java
public class ConfiguracionBDDirector {

    public ConfiguracionBD crearConfiguracionLocal() {
        return new ConfiguracionBD.Builder()
            .host("localhost")
            .puerto(5432)
            .baseDatos("aplicacion")
            .ssl(false)
            .build();
    }

    public ConfiguracionBD crearConfiguracionProduccion() {
        return new ConfiguracionBD.Builder()
            .host("db.example.com")
            .puerto(5432)
            .baseDatos("aplicacion")
            .ssl(true)
            .build();
    }
}
```

El Director es útil cuando varias partes de la aplicación necesitan repetir configuraciones conocidas. No es obligatorio y puede resultar innecesario si solo existe una forma de construir el objeto.

## 7. Ventajas

- **Legibilidad:** las llamadas describen qué propiedad se está configurando.
- **Flexibilidad:** permite construir distintas variantes de un mismo tipo de objeto.
- **Gestión de parámetros opcionales:** evita constructores con numerosos argumentos.
- **Validación centralizada:** facilita comprobar las reglas antes de crear el producto.
- **Inmutabilidad:** encaja especialmente bien con objetos cuyos campos no deben modificarse después de su construcción.
- **Separación de responsabilidades:** el cliente configura el objeto sin necesitar conocer todos los detalles de su inicialización.
- **Extensibilidad:** incorporar nuevas propiedades opcionales suele requerir menos cambios en el código cliente.

## 8. Desventajas

- **Mayor cantidad de código:** puede requerir clases y métodos adicionales.
- **Complejidad innecesaria:** para objetos sencillos con pocos parámetros, un constructor convencional puede ser suficiente.
- **Mantenimiento del Builder:** cuando cambia el producto, es necesario mantener coherente su constructor.
- **Posibles errores de configuración:** si las reglas no se validan adecuadamente, pueden construirse objetos con estados inconsistentes.
- **Duplicación en implementaciones manuales:** escribir un Builder para cada clase puede resultar repetitivo. Herramientas como Lombok pueden reducir ese código, aunque introducen una dependencia y convenciones adicionales.

## 9. Cuándo utilizar Builder

Es recomendable utilizar este patrón cuando:

- Un objeto tiene muchos parámetros, especialmente si varios son opcionales.
- Existen distintas combinaciones válidas de propiedades.
- Se necesita construir un objeto complejo en varias etapas.
- Se quiere exponer una API de creación legible.
- Se necesita centralizar las validaciones de construcción.
- Se desea crear objetos inmutables de forma cómoda.
- Existen configuraciones predefinidas que pueden encapsularse mediante un Director.

### Cuándo no utilizarlo

Puede ser innecesario cuando:

- El objeto tiene pocos atributos y una construcción sencilla.
- Casi todas las propiedades son obligatorias y no hay variaciones relevantes.
- La complejidad adicional no aporta una mejora real de legibilidad o mantenimiento.

## 10. Comparación con otros patrones creacionales

| Patrón | Objetivo principal | Diferencia respecto a Builder |
|---|---|---|
| Factory Method | Delegar en subclases la decisión sobre qué producto crear. | Se centra en elegir el tipo de producto, no en configurarlo paso a paso. |
| Abstract Factory | Crear familias de objetos relacionados. | Se centra en la compatibilidad entre productos de una familia. |
| Prototype | Crear objetos copiando una instancia existente. | Evita reconstruir manualmente el estado desde cero. |
| Singleton | Garantizar una única instancia accesible de una clase. | Controla la cantidad de instancias, no el proceso de construcción. |
| Builder | Construir objetos complejos mediante pasos explícitos. | Se centra en el proceso de construcción y la configuración del producto. |

## 11. Buenas prácticas

1. **Priorizar la claridad:** utilizar nombres de métodos que indiquen qué propiedad se está configurando.
2. **Definir los campos obligatorios:** exigirlos en el constructor del Builder cuando corresponda.
3. **Validar antes de construir:** comprobar en `build()` las reglas que afectan al estado completo del objeto.
4. **Considerar la inmutabilidad:** utilizar campos `final` y evitar exponer métodos que permitan modificar el producto si debe permanecer inmutable.
5. **Establecer valores predeterminados razonables:** documentarlos y evitar valores implícitos que puedan producir configuraciones inseguras.
6. **Evitar Builders innecesarios:** no introducir el patrón si un constructor simple es suficientemente claro.
7. **No confundir construcción con lógica de negocio:** el Builder debe encargarse de configurar y validar el objeto, no de ejecutar procesos de negocio ajenos a su creación.
8. **Documentar las reglas de configuración:** explicar qué parámetros son obligatorios y qué combinaciones están permitidas.

## 12. Ejemplos de uso en aplicaciones reales

Builder aparece con frecuencia en contextos donde los objetos admiten numerosas opciones:

- **Configuración de conexiones:** host, puerto, credenciales, tiempos de espera y opciones de seguridad.
- **Construcción de peticiones HTTP:** método, cabeceras, parámetros, cuerpo y autenticación.
- **Generación de consultas:** condiciones, ordenación, paginación y proyecciones.
- **Creación de documentos:** secciones, estilos, metadatos y elementos opcionales.
- **Configuración de servicios:** parámetros de inicialización y opciones de ejecución.
- **Pruebas automatizadas:** construcción de objetos de prueba con distintos estados.

Un ejemplo habitual en Java es `HttpRequest.Builder`, de la API estándar:

```java
import java.net.URI;
import java.net.http.HttpRequest;

HttpRequest request = HttpRequest.newBuilder()
    .uri(URI.create("https://example.com/api/usuarios"))
    .header("Accept", "application/json")
    .GET()
    .build();
```

En este caso, el Builder permite configurar la petición de forma progresiva antes de obtener el objeto `HttpRequest`.

## 13. Relación con los principios SOLID

Builder no garantiza por sí solo el cumplimiento de SOLID, pero puede contribuir a un diseño más mantenible:

- **Single Responsibility Principle (SRP):** permite separar la lógica de construcción de otras responsabilidades del cliente.
- **Open/Closed Principle (OCP):** una API de construcción bien diseñada puede facilitar nuevas opciones sin obligar a modificar todos los lugares donde se crea el objeto.
- **Dependency Inversion Principle (DIP):** puede utilizarse junto con abstracciones de construcción cuando el cliente necesita desacoplarse de implementaciones concretas.

La aplicación de estos principios depende de la implementación. Un Builder demasiado complejo también puede convertirse en una fuente de acoplamiento o responsabilidades excesivas.

## 14. Conclusión

El patrón Builder es una solución creacional que permite construir objetos complejos de forma gradual, explícita y controlada.

Su principal aportación consiste en separar la configuración de un objeto de su representación final, mejorando la legibilidad y reduciendo la complejidad asociada a constructores con muchos parámetros.

Es especialmente apropiado para objetos con propiedades opcionales, configuraciones múltiples o requisitos de validación. No obstante, debe aplicarse con criterio: para objetos simples, un constructor convencional puede ser más adecuado.

En definitiva, **Builder ayuda a que la creación de objetos sea más clara, flexible y fácil de mantener**, especialmente cuando su configuración empieza a crecer.

## 15. Referencias

- [Refactoring Guru — Builder](https://refactoring.guru/design-patterns/builder)
- [SourceMaking — Builder Design Pattern](https://sourcemaking.com/design_patterns/builder)
- [Java SE — HttpRequest.Builder](https://docs.oracle.com/en/java/javase/21/docs/api/java.net.http/java/net/http/HttpRequest.Builder.html)
- [Project Lombok — @Builder](https://projectlombok.org/features/Builder)
