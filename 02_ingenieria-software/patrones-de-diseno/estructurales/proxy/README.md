# Patrón de diseño estructural: Proxy

## 1. Introducción

El patrón de diseño **Proxy** es un patrón estructural que proporciona un objeto intermediario que controla el acceso a otro objeto, denominado objeto real o *Real Subject*.

El cliente interactúa con el proxy como si estuviera utilizando directamente el objeto real. Sin embargo, el proxy puede ejecutar operaciones adicionales antes de delegar la petición, después de obtener el resultado o incluso decidir si permite que la operación se realice.

Su objetivo principal es **controlar el acceso a un objeto sin cambiar la interfaz que utilizan sus clientes**.

Este patrón resulta especialmente útil cuando necesitamos añadir mecanismos de seguridad, optimizar el rendimiento, retrasar la creación de objetos costosos o acceder a recursos remotos.

### Ejemplo conceptual

Imaginemos una aplicación que permite consultar documentos confidenciales.

Antes de mostrar un documento, necesitamos comprobar si el usuario tiene permisos suficientes.

Sin Proxy, cada cliente podría tener que implementar esa comprobación.

Con Proxy, el cliente solicita el documento a través de un intermediario que verifica los permisos y, si corresponde, delega la operación en el objeto real.

```text
             Cliente
                |
                v
          DocumentoProxy
                |
         Comprobación de
            permisos
                |
                v
        DocumentoReal
                |
                v
       Contenido del documento
```

El cliente no necesita conocer los detalles de la comprobación de permisos. Solo necesita utilizar la interfaz prevista para acceder al documento.

---

## 2. Problema que resuelve

Proxy resuelve el problema de **controlar o ampliar el acceso a un objeto sin modificar directamente su implementación ni obligar a cambiar el código cliente**.

Puede ser necesario cuando:

- Un objeto es costoso de crear y queremos retrasar su inicialización.
- Necesitamos comprobar permisos antes de permitir una operación.
- Queremos registrar las llamadas realizadas a un objeto.
- El objeto real se encuentra en otro proceso o en otra máquina.
- Necesitamos controlar el acceso a un recurso compartido.
- Queremos introducir una capa de caché delante de un servicio.
- Necesitamos limitar o regular determinadas operaciones.

### Ejemplo sin utilizar Proxy

Imaginemos que una aplicación carga imágenes de alta resolución desde el disco.

```java
public class ImagenReal {

    private final String ruta;

    public ImagenReal(String ruta) {
        this.ruta = ruta;
        cargarImagen();
    }

    private void cargarImagen() {
        System.out.println(
            "Cargando imagen desde disco: " + ruta
        );
    }

    public void mostrar() {
        System.out.println(
            "Mostrando imagen: " + ruta
        );
    }
}
```

Si creamos una imagen directamente:

```java
ImagenReal imagen =
    new ImagenReal("fotografia.jpg");

imagen.mostrar();
```

La imagen se carga durante la construcción del objeto, aunque quizá el usuario nunca llegue a visualizarla.

Si la carga es costosa y existen muchas imágenes, esto puede perjudicar el rendimiento.

Además, si queremos retrasar la carga, tendremos que modificar la clase original o introducir lógica adicional en los clientes.

**Proxy permite controlar cuándo se crea y se utiliza el objeto real, manteniendo una interfaz común para el cliente.**

---

## 3. Solución propuesta

La solución consiste en introducir un objeto intermediario que implemente la misma interfaz que el objeto real.

El cliente utiliza esa interfaz sin necesitar conocer si está trabajando con el objeto real o con un proxy.

### Participantes del patrón

- **Subject:** interfaz común que define las operaciones disponibles.
- **RealSubject:** implementación real que realiza el trabajo.
- **Proxy:** implementa la misma interfaz y controla el acceso al objeto real.
- **Client:** utiliza el objeto a través de la interfaz común.

### Diagrama UML conceptual

```text
             +------------------+
             |     Subject      |
             +------------------+
             | + operacion()    |
             +------------------+
                      ^
                      |
             +--------+--------+
             |                 |
             |                 |
   +----------------+  +----------------+
   |     Proxy      |  |   RealSubject  |
   +----------------+  +----------------+
   | - realSubject  |  |                |
   +----------------+  +----------------+
   | + operacion()  |  | + operacion()  |
   +----------------+  +----------------+
             |
             v
       RealSubject
```

El proxy mantiene una referencia al objeto real, aunque en algunos escenarios puede crearlo bajo demanda o resolver su ubicación cuando se necesita.

### Flujo de ejecución

1. El cliente invoca una operación del proxy.
2. El proxy realiza las comprobaciones o tareas adicionales que correspondan.
3. Si la operación está permitida, el proxy invoca al objeto real.
4. El objeto real ejecuta la operación.
5. El proxy devuelve el resultado al cliente o gestiona posibles errores.

La operación adicional dependerá del tipo de proxy que se implemente.

---

## 4. Implementación completa en Java: proxy de protección

Vamos a desarrollar un ejemplo en el que una aplicación permite consultar documentos confidenciales.

El acceso estará condicionado por los permisos del usuario.

### 4.1. Interfaz Subject

Definimos la interfaz común que utilizarán tanto el proxy como el objeto real.

```java
public interface Documento {

    void mostrar(String usuario);
}
```

Esta interfaz declara la operación que el cliente puede solicitar.

### 4.2. Implementación real: RealSubject

La clase real representa el documento que contiene la información protegida.

```java
public class DocumentoReal implements Documento {

    private final String nombre;
    private final String contenido;

    public DocumentoReal(
            String nombre,
            String contenido) {

        this.nombre = nombre;
        this.contenido = contenido;

        cargarDocumento();
    }

    private void cargarDocumento() {
        System.out.println(
            "Cargando documento: " + nombre
        );
    }

    @Override
    public void mostrar(String usuario) {
        System.out.println(
            "Usuario: " + usuario
        );

        System.out.println(
            "Documento: " + nombre
        );

        System.out.println(
            "Contenido: " + contenido
        );
    }
}
```

Esta clase se encarga de la funcionalidad principal: cargar y mostrar el documento.

No contiene la lógica de autorización, porque esa responsabilidad se delegará al proxy en este ejemplo.

### 4.3. Proxy de protección

El proxy comprueba si el usuario tiene permiso para acceder al documento.

```java
public class DocumentoProxy implements Documento {

    private final String nombre;
    private final String contenido;
    private final String usuarioAutorizado;

    private DocumentoReal documentoReal;

    public DocumentoProxy(
            String nombre,
            String contenido,
            String usuarioAutorizado) {

        this.nombre = nombre;
        this.contenido = contenido;
        this.usuarioAutorizado = usuarioAutorizado;
    }

    @Override
    public void mostrar(String usuario) {

        if (!usuarioAutorizado.equals(usuario)) {
            System.out.println(
                "Acceso denegado para: " + usuario
            );

            return;
        }

        if (documentoReal == null) {
            documentoReal = new DocumentoReal(
                nombre,
                contenido
            );
        }

        documentoReal.mostrar(usuario);
    }
}
```

Observa que el objeto real se crea únicamente cuando un usuario autorizado intenta mostrar el documento por primera vez.

El proxy introduce dos comportamientos adicionales:

- Comprueba los permisos.
- Retrasa la creación del objeto real hasta que resulta necesario.

Esta implementación es didáctica. En una aplicación real, la autorización debe basarse en una identidad autenticada y en una política de permisos fiable; no debería confiarse únicamente en un nombre de usuario proporcionado por el cliente.

### 4.4. Cliente

El cliente trabaja con la interfaz `Documento`, sin necesitar conocer la implementación concreta.

```java
public class Main {

    public static void main(String[] args) {

        Documento documento = new DocumentoProxy(
            "informe-confidencial.pdf",
            "Información confidencial",
            "admin"
        );

        documento.mostrar("invitado");

        System.out.println("----------------");

        documento.mostrar("admin");

        System.out.println("----------------");

        documento.mostrar("admin");
    }
}
```

Salida aproximada:

```text
Acceso denegado para: invitado
----------------
Cargando documento: informe-confidencial.pdf
Usuario: admin
Documento: informe-confidencial.pdf
Contenido: Información confidencial
----------------
Usuario: admin
Documento: informe-confidencial.pdf
Contenido: Información confidencial
```

La primera llamada se rechaza y no carga el documento.

La segunda llamada autoriza el acceso y crea el objeto real.

La tercera llamada reutiliza el mismo objeto, evitando una nueva carga.

### 4.5. ¿Qué hemos conseguido?

El cliente utiliza una única interfaz:

```java
Documento documento = new DocumentoProxy(
    "informe-confidencial.pdf",
    "Información confidencial",
    "admin"
);

documento.mostrar("admin");
```

No necesita conocer cómo se realiza la comprobación de permisos ni cuándo se carga el documento.

Además, la clase `DocumentoReal` no ha tenido que modificarse para incorporar esas tareas adicionales.

---

## 5. Estructura general del patrón

La estructura mínima puede implementarse de la siguiente manera:

```java
// Interfaz común
public interface Servicio {

    void ejecutar();
}

// Objeto real
public class ServicioReal implements Servicio {

    @Override
    public void ejecutar() {
        System.out.println(
            "Ejecutando operación real"
        );
    }
}

// Proxy
public class ServicioProxy implements Servicio {

    private ServicioReal servicioReal;

    @Override
    public void ejecutar() {

        System.out.println(
            "Realizando comprobaciones previas"
        );

        if (servicioReal == null) {
            servicioReal = new ServicioReal();
        }

        servicioReal.ejecutar();

        System.out.println(
            "Operación finalizada"
        );
    }
}

// Cliente
public class Main {

    public static void main(String[] args) {

        Servicio servicio = new ServicioProxy();

        servicio.ejecutar();
    }
}
```

El ejemplo muestra la estructura esencial: el proxy implementa la misma interfaz, realiza tareas adicionales y delega la operación al objeto real.

En una aplicación real, conviene valorar la inyección de dependencias, la gestión de errores y la concurrencia. Si varias peticiones pueden llegar simultáneamente, la creación diferida del objeto real deberá sincronizarse cuando sea necesario.

---

## 6. Tipos de Proxy

El patrón Proxy tiene distintas variantes según el objetivo que se quiera conseguir.

### 6.1. Virtual Proxy

**Objetivo:** retrasar la creación o carga de un objeto costoso hasta que realmente se necesita.

Ejemplos:

- Carga diferida de imágenes.
- Inicialización de componentes pesados.
- Lectura de documentos grandes.
- Carga bajo demanda de información.

Es la variante utilizada cuando queremos evitar inicializar objetos que quizá nunca se utilicen.

### 6.2. Protection Proxy

**Objetivo:** controlar el acceso al objeto real mediante permisos o reglas de autorización.

Ejemplos:

- Acceso a documentos confidenciales.
- Operaciones administrativas.
- Recursos con permisos específicos.
- Servicios que requieren autorización previa.

El proxy decide si permite la operación, pero los controles de seguridad importantes deben aplicarse también en la frontera de confianza correspondiente, especialmente si existen accesos directos al recurso real.

### 6.3. Remote Proxy

**Objetivo:** representar localmente un objeto que se encuentra en otro proceso o en otra máquina.

El cliente invoca operaciones sobre una interfaz local, mientras que el proxy se encarga de comunicarse con el servicio remoto.

Ejemplos:

- Clientes de servicios remotos.
- Comunicación mediante RPC.
- Acceso a servicios distribuidos.
- Integraciones con sistemas externos.

El proxy remoto puede ocultar detalles de comunicación, pero no elimina la latencia, los errores de red ni la posibilidad de que una operación remota falle después de haberse ejecutado.

### 6.4. Caching Proxy

**Objetivo:** almacenar resultados para evitar operaciones repetidas y costosas.

Ejemplos:

- Consultas frecuentes.
- Lecturas de datos que cambian poco.
- Acceso a APIs externas.
- Recuperación de información de un servicio remoto.

La caché debe definir una política de expiración e invalidación coherente con los requisitos del sistema.

### 6.5. Logging Proxy

**Objetivo:** registrar las operaciones realizadas sobre el objeto real.

Puede registrar:

- Nombre de la operación.
- Fecha y hora.
- Duración.
- Resultado.
- Errores producidos.

Debe evitar registrar credenciales, datos personales u otra información sensible sin una justificación y unas medidas adecuadas.

### 6.6. Smart Reference Proxy

**Objetivo:** realizar tareas adicionales cada vez que se accede a un objeto.

Por ejemplo:

- Contar referencias activas.
- Controlar el acceso concurrente.
- Comprobar el estado del objeto.
- Gestionar determinadas operaciones de ciclo de vida.

Esta variante resulta útil cuando el acceso al objeto debe estar sujeto a controles adicionales.

---

## 7. Ejemplo práctico: proxy de caché

Supongamos que una aplicación consulta información de productos mediante un servicio costoso.

Queremos evitar que varias consultas consecutivas al mismo producto provoquen llamadas repetidas al servicio real.

### 7.1. Interfaz del servicio

```java
public interface ProductoService {

    String obtenerProducto(String id);
}
```

### 7.2. Servicio real

```java
public class ProductoServiceReal
        implements ProductoService {

    @Override
    public String obtenerProducto(String id) {

        System.out.println(
            "Consultando producto en el servicio real: " + id
        );

        // Simulación de una consulta costosa.
        return "Producto " + id;
    }
}
```

### 7.3. Proxy con caché

```java
import java.util.HashMap;
import java.util.Map;

public class ProductoServiceProxy
        implements ProductoService {

    private final ProductoService servicioReal;
    private final Map<String, String> cache =
        new HashMap<>();

    public ProductoServiceProxy(
            ProductoService servicioReal) {

        this.servicioReal = servicioReal;
    }

    @Override
    public String obtenerProducto(String id) {

        if (cache.containsKey(id)) {
            System.out.println(
                "Resultado obtenido desde caché"
            );

            return cache.get(id);
        }

        String producto =
            servicioReal.obtenerProducto(id);

        cache.put(id, producto);

        return producto;
    }
}
```

### 7.4. Cliente

```java
public class Main {

    public static void main(String[] args) {

        ProductoService servicio =
            new ProductoServiceProxy(
                new ProductoServiceReal()
            );

        System.out.println(
            servicio.obtenerProducto("123")
        );

        System.out.println(
            servicio.obtenerProducto("123")
        );

        System.out.println(
            servicio.obtenerProducto("456")
        );
    }
}
```

Salida aproximada:

```text
Consultando producto en el servicio real: 123
Producto 123
Resultado obtenido desde caché
Producto 123
Consultando producto en el servicio real: 456
Producto 456
```

La segunda consulta del producto `123` reutiliza el resultado almacenado.

Este ejemplo utiliza un `HashMap` sencillo y es adecuado para ilustrar el patrón. En una aplicación concurrente o distribuida, podrían ser necesarias una caché segura para concurrencia, una política de expiración, una estrategia de invalidación y límites de memoria.

---

## 8. Ventajas y desventajas

### Ventajas

1. **Control de acceso:** permite comprobar permisos o condiciones antes de acceder al objeto real.
2. **Carga diferida:** evita crear objetos costosos hasta que son necesarios.
3. **Optimización del rendimiento:** una caché puede reducir llamadas repetidas.
4. **Separación de responsabilidades:** permite incorporar tareas de control sin introducirlas directamente en la lógica principal del objeto real.
5. **Transparencia para el cliente:** si ambos implementan la misma interfaz, el cliente puede utilizar el proxy sin cambiar el contrato que conoce.
6. **Acceso remoto:** puede ocultar parte de los detalles de comunicación con servicios externos.
7. **Instrumentación:** facilita añadir registros, métricas y controles de acceso alrededor de una operación.

### Desventajas

1. **Mayor complejidad:** introduce una clase y una capa de indirección adicionales.
2. **Posible latencia:** las comprobaciones o comunicaciones añadidas pueden aumentar el tiempo de respuesta.
3. **Gestión de errores:** el proxy debe definir cómo propagar o transformar los errores del objeto real.
4. **Problemas de caché:** los datos almacenados pueden quedar obsoletos si no existe una política de invalidación adecuada.
5. **Complejidad concurrente:** la inicialización diferida y el almacenamiento compartido pueden requerir sincronización.
6. **Falsa sensación de seguridad:** un proxy no protege un recurso si los clientes pueden saltárselo y acceder directamente al objeto real.
7. **Dificultad de depuración:** puede ser necesario recorrer varias capas para identificar el origen de una operación o un error.

Proxy resulta apropiado cuando el control de acceso, la carga diferida, la caché o la comunicación remota justifican la capa adicional.

---

## 9. Relación con los principios SOLID

### Single Responsibility Principle (SRP)

El proxy debería tener una responsabilidad concreta relacionada con el acceso al objeto real.

Por ejemplo, un proxy de protección debería encargarse de comprobar permisos, no de gestionar simultáneamente cachés, notificaciones, informes y lógica de negocio no relacionada.

Si necesitamos varios comportamientos, podemos utilizar componentes separados o combinar proxies especializados cuando el diseño lo permita.

### Open/Closed Principle (OCP)

Podemos añadir comportamientos de acceso mediante un proxy sin modificar la implementación del objeto real.

Por ejemplo, podemos introducir un proxy de logging alrededor de un servicio existente.

No obstante, el proxy y el objeto real deben seguir respetando el contrato común.

### Liskov Substitution Principle (LSP)

Este principio es especialmente relevante para Proxy.

Como el proxy y el objeto real implementan la misma interfaz, el cliente debería poder utilizar el proxy en los contextos donde espera esa interfaz sin encontrarse con incumplimientos inesperados del contrato.

Esto no significa que ambos tengan que presentar idénticos tiempos de respuesta o detalles internos, sino que deben respetar las garantías y los comportamientos definidos por la interfaz.

### Interface Segregation Principle (ISP)

Las interfaces pequeñas facilitan la creación de proxies.

Si una interfaz contiene numerosas operaciones no relacionadas, el proxy puede terminar gestionando una complejidad innecesaria.

Definir contratos específicos para cada responsabilidad ayuda a mantener implementaciones más claras.

### Dependency Inversion Principle (DIP)

El cliente debería depender de una abstracción común, no de una implementación concreta.

Por ejemplo:

```java
Servicio servicio = new ServicioProxy();
```

El cliente utiliza la interfaz `Servicio`.

En aplicaciones con inyección de dependencias, el proxy y el servicio real pueden configurarse para que la aplicación utilice el intermediario sin tener que modificar el código cliente.

---

## 10. Diferencias con otros patrones estructurales

### Proxy vs. Decorator

Ambos patrones suelen implementar la misma interfaz que el objeto que envuelven, pero sus intenciones son diferentes.

- **Proxy:** controla el acceso al objeto real.
- **Decorator:** añade responsabilidades o comportamientos al objeto.

Ejemplo:

Un proxy puede comprobar si un usuario tiene permiso para consultar un documento.

Un decorator puede añadir compresión o cifrado a una operación de almacenamiento.

La estructura puede ser similar, pero el propósito determina qué patrón estamos aplicando.

### Proxy vs. Adapter

- **Proxy:** conserva la interfaz del objeto y controla el acceso.
- **Adapter:** transforma una interfaz en otra que el cliente necesita.

Un proxy de imágenes mantiene la interfaz de visualización de la imagen.

Un adaptador puede convertir la interfaz de una biblioteca multimedia externa en la interfaz utilizada por nuestra aplicación.

### Proxy vs. Facade

- **Proxy:** representa un objeto y controla el acceso a él.
- **Facade:** simplifica el acceso a un conjunto de componentes o a un subsistema.

Un proxy de documentos puede autorizar o retrasar la carga de un documento.

Una fachada de documentos puede ofrecer una operación de alto nivel que coordine la búsqueda, validación, carga y exportación de documentos.

### Proxy vs. Bridge

- **Proxy:** controla el acceso a un objeto.
- **Bridge:** separa una abstracción de su implementación para que ambas puedan evolucionar de forma independiente.

Bridge se centra en separar dos dimensiones de diseño; Proxy se centra en introducir un intermediario para gestionar el acceso.

### Proxy vs. Chain of Responsibility

- **Proxy:** representa un objeto y controla cómo se accede a él.
- **Chain of Responsibility:** pasa una petición por una cadena de posibles manejadores hasta que alguno la procesa o la cadena termina.

Aunque un proxy puede realizar comprobaciones antes de delegar, no necesita recorrer una cadena de manejadores.

---

## 11. Casos de uso habituales

Proxy aparece en numerosos escenarios de desarrollo de software.

### Seguridad

- Protección de documentos.
- Control de acceso a servicios.
- Autorización de operaciones administrativas.
- Aplicación de políticas de acceso.

### Rendimiento

- Carga diferida de objetos.
- Caché de consultas.
- Reutilización de recursos costosos.
- Evitar inicializaciones innecesarias.

### Sistemas distribuidos

- Clientes de servicios remotos.
- Comunicación mediante RPC.
- Acceso a recursos alojados en otros servidores.
- Intermediación con APIs externas.

### Observabilidad

- Registro de operaciones.
- Medición de tiempos de respuesta.
- Recopilación de métricas.
- Trazabilidad de llamadas.

### Gestión de recursos

- Control de acceso a conexiones.
- Protección de operaciones costosas.
- Gestión de objetos compartidos.
- Limitación de determinadas operaciones.

En Java, los proxies también aparecen en mecanismos de frameworks y bibliotecas para implementar comportamientos transversales. Por ejemplo, Spring puede utilizar proxies para aplicar aspectos como transacciones y determinados tipos de interceptación. El comportamiento concreto depende de la configuración y del mecanismo utilizado.

---

## 12. Proxy en arquitecturas modernas

El patrón puede utilizarse tanto dentro de una aplicación como en sistemas distribuidos.

### 12.1. En una aplicación por capas

```text
Controlador
    |
    v
Interfaz del servicio
    |
    v
Proxy de logging / caché / seguridad
    |
    v
Servicio real
    |
    v
Repositorio
```

El proxy puede añadir funcionalidades técnicas alrededor del servicio real, mientras que este conserva la lógica que le corresponde.

El orden de las capas y la ubicación de la seguridad deben elegirse según las necesidades de la aplicación. No basta con introducir un proxy para considerar protegido todo el sistema.

### 12.2. En una arquitectura distribuida

```text
Aplicación cliente
        |
        v
Proxy de acceso remoto
        |
        v
Red / protocolo de comunicación
        |
        v
Servicio remoto
```

El proxy puede ofrecer una interfaz de programación local para realizar llamadas remotas.

Sin embargo, una llamada remota no tiene las mismas garantías que una llamada local: puede sufrir tiempos de espera, fallos de red, respuestas perdidas o resultados ambiguos.

Por ello, un proxy remoto debe contemplar errores, límites de tiempo y políticas de reintento apropiadas. Los reintentos requieren especial cuidado cuando la operación puede producir efectos secundarios, como realizar un cobro.

### 12.3. Proxy y programación orientada a aspectos

En algunos frameworks, un proxy intercepta llamadas para aplicar funcionalidades técnicas como:

- Transacciones.
- Logging.
- Medición de tiempos.
- Seguridad.
- Caché.

Este mecanismo permite separar esas funcionalidades de la lógica de negocio.

No obstante, hay que tener en cuenta las particularidades del framework. Por ejemplo, según el tipo de proxy y la configuración de Spring, ciertas llamadas internas entre métodos de una misma instancia pueden no pasar por el intermediario y, por tanto, no activar el comportamiento esperado.

---

## 13. Buenas prácticas

1. **Define una interfaz común:** facilita que el cliente utilice el proxy y el objeto real mediante el mismo contrato.
2. **Mantén clara la intención del proxy:** determina si su responsabilidad es seguridad, caché, carga diferida, acceso remoto u otra función.
3. **Evita acumular responsabilidades:** considera separar los comportamientos en componentes especializados.
4. **Gestiona correctamente los errores:** define qué excepciones se propagan y cuáles se transforman.
5. **Considera la concurrencia:** especialmente en la inicialización diferida y en las cachés compartidas.
6. **No confíes exclusivamente en el proxy para proteger recursos críticos:** aplica los controles necesarios en el punto de acceso efectivo al recurso.
7. **Define la política de caché:** contempla expiración, invalidación, coherencia y límites de almacenamiento.
8. **No ocultes costes importantes:** una operación que realiza llamadas remotas debe documentar sus posibles latencias y fallos.
9. **Utiliza inyección de dependencias cuando aporte valor:** facilita las pruebas y la sustitución de implementaciones.
10. **Evita añadir proxies innecesarios:** la indirección adicional debe resolver un problema concreto.

---

## 14. Pruebas recomendadas

Las pruebas deberían verificar tanto el comportamiento del proxy como su interacción con el objeto real.

Para el proxy de protección, conviene comprobar que:

- Un usuario no autorizado no puede ejecutar la operación.
- Un usuario autorizado sí puede acceder al documento.
- El objeto real no se crea cuando no es necesario.
- El objeto real se reutiliza cuando corresponde.
- Los errores del objeto real se propagan o gestionan de acuerdo con el contrato.

Para el proxy de caché, conviene comprobar que:

- La primera consulta delega en el servicio real.
- Una consulta repetida devuelve el resultado almacenado.
- Los resultados correspondientes a claves diferentes no se mezclan.
- Los errores no se almacenan accidentalmente como resultados válidos.
- La política de expiración e invalidación funciona correctamente, si está implementada.

Se pueden utilizar JUnit y Mockito para verificar las interacciones con el objeto real, simular errores y comprobar cuántas veces se ejecuta una operación.

En proxies que controlan seguridad, también son recomendables pruebas de integración que confirmen que no existen vías alternativas para acceder al recurso protegido sin autorización.

---

## 15. Conclusión

El patrón **Proxy** permite controlar el acceso a un objeto mediante un intermediario que mantiene una interfaz compatible con la del objeto real.

Su principal ventaja es que posibilita incorporar comportamientos como autorización, carga diferida, caché, registro o comunicación remota sin modificar necesariamente la implementación principal del objeto.

En Java, suele implementarse mediante una interfaz compartida por el proxy y el objeto real, de manera que el cliente pueda utilizar ambos a través del mismo contrato.

Es especialmente útil cuando necesitamos gestionar cómo y cuándo se accede a un recurso, en lugar de cambiar la funcionalidad principal que ese recurso proporciona.

La idea fundamental es: **introducir un intermediario que controle el acceso a un objeto, manteniendo una interfaz compatible con la que utiliza el cliente.**

---

## 16. Referencias

- **Design Patterns: Elements of Reusable Object-Oriented Software**, de Erich Gamma, Richard Helm, Ralph Johnson y John Vlissides (Gang of Four).
- Refactoring.Guru — Proxy: https://refactoring.guru/design-patterns/proxy
- Documentación oficial de Java: https://docs.oracle.com/en/java/
- Documentación oficial de Spring Framework: https://docs.spring.io/spring-framework/reference/
