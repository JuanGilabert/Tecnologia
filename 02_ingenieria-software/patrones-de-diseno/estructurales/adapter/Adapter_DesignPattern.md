# Patrón de diseño estructural: Adapter (Adaptador)

## 1. Introducción

El patrón de diseño **Adapter** es un patrón estructural que permite que dos interfaces incompatibles trabajen juntas. Para conseguirlo, introduce una clase adaptadora que transforma la interfaz de un objeto existente en otra que el cliente espera utilizar.

Su objetivo principal es **adaptar una interfaz sin necesidad de modificar el código original de la clase que queremos reutilizar**.

Este patrón resulta especialmente útil cuando necesitamos integrar sistemas externos, bibliotecas de terceros, servicios antiguos o componentes que ofrecen funcionalidades válidas, pero cuya interfaz no coincide con la que necesita nuestra aplicación.

### Ejemplo conceptual

Imaginemos que nuestra aplicación espera trabajar con un servicio de pago que utiliza el método:

```java
procesarPago(double cantidad)
```

Sin embargo, una biblioteca externa ofrece un método diferente:

```java
realizarTransaccion(int importeEnCentimos)
```

En lugar de modificar la biblioteca externa o cambiar todo nuestro código, podemos crear un adaptador que convierta la cantidad y delegue la operación en el servicio externo.

De esta forma, nuestra aplicación continúa utilizando la interfaz que conoce.

---

## 2. Problema que resuelve

El patrón Adapter resuelve el problema de la **incompatibilidad entre interfaces**.

Este problema aparece cuando una clase ofrece la funcionalidad que necesitamos, pero sus métodos, parámetros o convenciones de uso no coinciden con los que espera nuestro sistema.

### Ejemplo sin utilizar Adapter

Supongamos que tenemos una aplicación que espera una interfaz común para realizar pagos:

```java
public interface PasarelaPago {
    void procesarPago(double cantidad);
}
```

Una implementación propia podría utilizarla directamente:

```java
public class PagoLocal implements PasarelaPago {

    @Override
    public void procesarPago(double cantidad) {
        System.out.println(
            "Procesando pago de " + cantidad + " euros"
        );
    }
}
```

Sin embargo, una biblioteca externa ofrece una interfaz incompatible:

```java
public class ServicioPagoExterno {

    public void realizarTransaccion(int importeEnCentimos) {
        System.out.println(
            "Transacción externa de "
            + importeEnCentimos
            + " céntimos"
        );
    }
}
```

No podemos utilizar directamente `ServicioPagoExterno` donde se espera un objeto `PasarelaPago`, porque la clase no implementa esa interfaz y sus métodos tienen una firma diferente.

Una solución poco recomendable sería modificar el código cliente para conocer cada implementación externa y sus particularidades. Esto incrementaría el acoplamiento y dificultaría la incorporación de nuevos proveedores.

**Adapter permite solucionar este problema mediante una clase intermedia que traduce una interfaz a otra.**

---

## 3. Solución propuesta

La solución consiste en introducir un objeto adaptador que:

1. Implementa la interfaz que necesita el cliente.
2. Mantiene una referencia al objeto incompatible que queremos reutilizar.
3. Traduce los parámetros o las llamadas cuando sea necesario.
4. Delega la operación en el objeto adaptado.

El cliente trabaja exclusivamente con la interfaz esperada, sin necesitar conocer los detalles del servicio externo.

### Diagrama conceptual

```text
                 Cliente
                    |
                    v
              PasarelaPago
               (Target)
                    ^
                    |
             PagoExternoAdapter
                (Adapter)
                    |
                    v
           ServicioPagoExterno
               (Adaptee)
```

Los participantes son:

- **Client:** el código que necesita realizar una operación.
- **Target:** la interfaz que el cliente espera utilizar.
- **Adapter:** la clase que implementa la interfaz esperada y traduce las llamadas.
- **Adaptee:** la clase existente cuya interfaz es incompatible.

En Java, la solución más habitual es utilizar un adaptador basado en composición: el adaptador implementa la interfaz esperada y contiene una referencia al objeto que adapta.

---

## 4. Implementación completa en Java

### 4.1. Interfaz objetivo: Target

Esta es la interfaz que espera utilizar nuestra aplicación.

```java
public interface PasarelaPago {

    void procesarPago(double cantidad);
}
```

El resto del sistema trabajará con esta abstracción, independientemente del proveedor de pagos utilizado.

### 4.2. Clase incompatible: Adaptee

Se trata de una clase externa o preexistente cuya interfaz no coincide con la esperada.

```java
public class ServicioPagoExterno {

    public void realizarTransaccion(int importeEnCentimos) {
        System.out.println(
            "Procesando transacción externa por "
            + importeEnCentimos
            + " céntimos"
        );
    }
}
```

Su método recibe un entero que representa céntimos, mientras que nuestra aplicación utiliza cantidades expresadas en euros mediante un `double`.

En un sistema real, también deberíamos considerar las reglas de redondeo y la precisión monetaria.

### 4.3. Adaptador: Adapter

Esta clase implementa `PasarelaPago` y utiliza internamente `ServicioPagoExterno`.

```java
public class PagoExternoAdapter implements PasarelaPago {

    private final ServicioPagoExterno servicioExterno;

    public PagoExternoAdapter(
            ServicioPagoExterno servicioExterno) {
        this.servicioExterno = servicioExterno;
    }

    @Override
    public void procesarPago(double cantidad) {
        if (!Double.isFinite(cantidad) || cantidad < 0) {
            throw new IllegalArgumentException(
                "La cantidad debe ser finita y no negativa"
            );
        }

        int importeEnCentimos =
            Math.toIntExact(Math.round(cantidad * 100));

        servicioExterno.realizarTransaccion(
            importeEnCentimos
        );
    }
}
```

**¿Qué responsabilidades tiene el adaptador?**

- Implementar la interfaz que espera el cliente.
- Convertir euros a céntimos.
- Invocar el método equivalente del servicio externo.
- Mantener aislados los detalles específicos del proveedor.

La conversión de este ejemplo es ilustrativa. En sistemas financieros reales suele ser preferible utilizar `BigDecimal` o una representación monetaria en unidades menores con validaciones explícitas para evitar problemas de precisión y desbordamiento.

### 4.4. Cliente

El cliente depende únicamente de la interfaz `PasarelaPago`.

```java
public class Tienda {

    private final PasarelaPago pasarelaPago;

    public Tienda(PasarelaPago pasarelaPago) {
        this.pasarelaPago = pasarelaPago;
    }

    public void comprar(double precio) {
        System.out.println("Iniciando compra...");
        pasarelaPago.procesarPago(precio);
        System.out.println("Solicitud de pago enviada");
    }
}
```

La clase `Tienda` no necesita saber si el pago se realiza mediante una implementación local o un servicio externo.

### 4.5. Clase principal

```java
public class Main {

    public static void main(String[] args) {

        ServicioPagoExterno servicioExterno =
            new ServicioPagoExterno();

        PasarelaPago adaptador =
            new PagoExternoAdapter(servicioExterno);

        Tienda tienda = new Tienda(adaptador);

        tienda.comprar(25.50);
    }
}
```

Salida aproximada:

```text
Iniciando compra...
Procesando transacción externa por 2550 céntimos
Solicitud de pago enviada
```

### 4.6. ¿Qué hemos conseguido?

La clase `Tienda` no se ha modificado para incorporar el servicio externo. Solo hemos creado un adaptador que hace compatible la interfaz existente con la que espera la aplicación.

Si mañana queremos incorporar otro proveedor de pagos, podemos crear una nueva implementación de `PasarelaPago` o un adaptador diferente, sin cambiar necesariamente la lógica de compra.

---

## 5. Estructura general del patrón

La estructura habitual puede expresarse mediante las siguientes clases:

```java
// Target: interfaz esperada por el cliente
public interface Target {
    void ejecutar();
}

// Adaptee: clase existente con otra interfaz
public class Adaptee {

    public void ejecutarOperacionEspecial() {
        System.out.println("Operación especial");
    }
}

// Adapter: traduce una interfaz a otra
public class Adapter implements Target {

    private final Adaptee adaptee;

    public Adapter(Adaptee adaptee) {
        this.adaptee = adaptee;
    }

    @Override
    public void ejecutar() {
        adaptee.ejecutarOperacionEspecial();
    }
}

// Client: trabaja con Target
public class Client {

    private final Target target;

    public Client(Target target) {
        this.target = target;
    }

    public void realizarOperacion() {
        target.ejecutar();
    }
}
```

Este esquema representa la idea esencial del patrón: el cliente utiliza `Target`, mientras que `Adapter` traduce las llamadas para aprovechar el comportamiento de `Adaptee`.

---

## 6. Tipos de Adapter

Existen dos variantes clásicas del patrón.

### 6.1. Object Adapter (adaptador de objetos)

Utiliza composición: el adaptador contiene una referencia al objeto que adapta y delega en él las operaciones.

```java
public class Adapter implements Target {

    private final Adaptee adaptee;

    public Adapter(Adaptee adaptee) {
        this.adaptee = adaptee;
    }

    @Override
    public void ejecutar() {
        adaptee.ejecutarOperacionEspecial();
    }
}
```

**Ventajas:**

- No necesita heredar de la clase adaptada.
- Puede adaptar objetos de clases finales.
- Favorece la flexibilidad y el bajo acoplamiento.
- Encaja bien con las prácticas habituales de Java.

Esta suele ser la opción preferida en aplicaciones Java modernas.

### 6.2. Class Adapter (adaptador de clases)

Utiliza herencia para adaptar una clase existente a una interfaz objetivo.

En Java, una versión posible requiere extender la clase adaptada e implementar la interfaz objetivo:

```java
public class Adaptee {

    public void operacionEspecial() {
        System.out.println("Operación especial");
    }
}

public interface Target {
    void ejecutar();
}

public class ClassAdapter
        extends Adaptee
        implements Target {

    @Override
    public void ejecutar() {
        operacionEspecial();
    }
}
```

En este caso, `ClassAdapter` hereda el comportamiento de `Adaptee` y expone la interfaz `Target`.

**Limitaciones:**

- Java solo permite extender una clase.
- El adaptador queda más ligado a la implementación de la clase base.
- No es posible adaptar una instancia arbitraria mediante composición.

Por estas razones, el adaptador de objetos suele ser más flexible en Java. El adaptador de clases es una variante conceptual clásica cuya viabilidad depende de las restricciones del lenguaje y de las clases implicadas.

---

## 7. Ejemplo práctico: integración de sistemas de notificaciones

Imaginemos una aplicación que necesita enviar notificaciones mediante una interfaz común:

```java
public interface Notificador {

    void enviar(String destinatario, String mensaje);
}
```

Un proveedor externo ofrece una API con nombres de métodos diferentes:

```java
public class ApiSmsExterna {

    public void mandarSms(
            String numeroTelefono,
            String contenido) {

        System.out.println(
            "SMS enviado a "
            + numeroTelefono
            + ": "
            + contenido
        );
    }
}
```

Creamos el adaptador:

```java
public class SmsAdapter implements Notificador {

    private final ApiSmsExterna apiSms;

    public SmsAdapter(ApiSmsExterna apiSms) {
        this.apiSms = apiSms;
    }

    @Override
    public void enviar(
            String destinatario,
            String mensaje) {

        apiSms.mandarSms(destinatario, mensaje);
    }
}
```

El cliente puede utilizar cualquier implementación de `Notificador`:

```java
public class ServicioNotificaciones {

    private final Notificador notificador;

    public ServicioNotificaciones(Notificador notificador) {
        this.notificador = notificador;
    }

    public void notificarUsuario(
            String telefono,
            String mensaje) {

        notificador.enviar(telefono, mensaje);
    }
}
```

Uso:

```java
public class Main {

    public static void main(String[] args) {

        Notificador notificador =
            new SmsAdapter(new ApiSmsExterna());

        ServicioNotificaciones servicio =
            new ServicioNotificaciones(notificador);

        servicio.notificarUsuario(
            "+34123456789",
            "Tu pedido ha sido enviado"
        );
    }
}
```

El servicio de notificaciones no conoce la API externa. Si incorporamos otro proveedor, podremos crear un adaptador nuevo que implemente `Notificador`.

En producción, los datos de contacto deben validarse y las credenciales del proveedor deben gestionarse de forma segura.

---

## 8. Ventajas y desventajas

### Ventajas

1. **Reutilización de código:** permite aprovechar clases existentes sin modificar su implementación.
2. **Compatibilidad entre interfaces:** conecta componentes que originalmente no podían trabajar juntos.
3. **Menor acoplamiento:** el cliente depende de una interfaz común y no de los detalles del proveedor.
4. **Facilidad de integración:** simplifica la incorporación de bibliotecas externas, API y sistemas heredados.
5. **Mejor mantenibilidad:** concentra las conversiones y particularidades de una integración en un punto concreto.
6. **Facilidad para sustituir proveedores:** permite cambiar la implementación sin modificar la lógica principal del cliente.

### Desventajas

1. **Aumento del número de clases:** cada integración puede requerir uno o varios adaptadores.
2. **Complejidad adicional:** una adaptación excesiva puede dificultar el seguimiento de las llamadas.
3. **Posibles pérdidas de información:** las conversiones de tipos o formatos pueden perder precisión o significado.
4. **No resuelve incompatibilidades semánticas por sí solo:** dos métodos pueden tener firmas adaptables, pero comportamientos diferentes.
5. **Dependencia del proveedor dentro del adaptador:** cuando la API externa cambia, el adaptador puede necesitar modificaciones.

Adapter es útil cuando existe una incompatibilidad concreta. No conviene introducirlo si dos componentes ya tienen interfaces compatibles y no existe una necesidad real de aislamiento.

---

## 9. Relación con los principios SOLID

### Single Responsibility Principle (SRP)

El adaptador tiene una responsabilidad específica: traducir la interfaz del cliente a la interfaz del servicio adaptado.

Así, la lógica de negocio no necesita incluir conversiones particulares de cada proveedor.

### Open/Closed Principle (OCP)

Podemos incorporar nuevos proveedores mediante nuevas implementaciones o adaptadores, procurando no modificar el código cliente existente.

Esto funciona especialmente bien cuando el cliente depende de una interfaz estable.

### Liskov Substitution Principle (LSP)

Un adaptador debe respetar el contrato de la interfaz objetivo.

Por ejemplo, si `PasarelaPago` establece que los importes negativos deben rechazarse, todas las implementaciones y adaptadores deben respetar ese comportamiento.

Implementar la interfaz no basta: también deben mantenerse sus precondiciones, postcondiciones y expectativas semánticas.

### Interface Segregation Principle (ISP)

Las interfaces objetivo deben ser suficientemente pequeñas y específicas para que los adaptadores no se vean obligados a implementar operaciones que no pueden soportar.

Una interfaz pequeña suele facilitar la integración con servicios externos.

### Dependency Inversion Principle (DIP)

El cliente depende de la abstracción `PasarelaPago`, no de la clase concreta `ServicioPagoExterno`.

El adaptador conecta esa abstracción con la implementación concreta.

Esto facilita las pruebas y permite sustituir proveedores con menos cambios en el sistema.

---

## 10. Diferencias con otros patrones estructurales

### Adapter vs. Decorator

Ambos patrones envuelven un objeto, pero su propósito es diferente.

- **Adapter:** cambia la interfaz disponible para hacer compatible un objeto con el cliente.
- **Decorator:** mantiene una interfaz compatible y añade responsabilidades o comportamiento.

Ejemplo: un adaptador convierte la interfaz de una API externa; un decorador añade registros, métricas o caché a un servicio existente.

### Adapter vs. Facade

- **Adapter:** adapta una interfaz existente a otra que el cliente necesita.
- **Facade:** proporciona una interfaz simplificada para un sistema complejo compuesto por varios componentes.

Ejemplo: un adaptador hace compatible un proveedor de pagos; una fachada coordina varios subsistemas para ejecutar un proceso de compra.

### Adapter vs. Proxy

- **Adapter:** traduce o transforma la interfaz.
- **Proxy:** controla el acceso a otro objeto, por ejemplo, mediante acceso remoto, carga diferida o comprobaciones de permisos.

Un proxy suele conservar la interfaz del objeto que representa, mientras que un adaptador busca satisfacer una interfaz distinta.

### Adapter vs. Bridge

- **Adapter:** se utiliza normalmente para conectar interfaces que ya existen y son incompatibles.
- **Bridge:** separa dos dimensiones de diseño para que puedan evolucionar independientemente desde el principio.

La intención y el momento de aplicación son diferentes: Adapter suele resolver una incompatibilidad existente; Bridge ayuda a estructurar una solución extensible.

---

## 11. Casos de uso habituales

Adapter resulta especialmente útil en los siguientes escenarios:

- Integración de API de terceros con convenciones diferentes.
- Migración gradual de sistemas heredados.
- Sustitución de proveedores externos sin alterar la lógica de negocio.
- Adaptación de formatos de datos, unidades de medida o modelos de dominio.
- Integración de bibliotecas con interfaces que no controlamos.
- Unificación de distintos sistemas de notificaciones, pagos, almacenamiento o autenticación.
- Compatibilidad entre una interfaz antigua y una nueva durante una transición tecnológica.

### Ejemplo en una arquitectura por capas

En una arquitectura hexagonal, un adaptador de salida puede implementar un puerto definido por el dominio o la aplicación y delegar en una API externa.

Por ejemplo:

```text
Aplicación
    |
    v
Puerto de salida: PasarelaPago
    ^
    |
Adaptador de proveedor
    |
    v
API externa
```

Esta estructura permite mantener las reglas de negocio separadas de las particularidades de los proveedores.

**Importante:** Adapter es un patrón de diseño estructural; un adaptador de infraestructura dentro de una arquitectura hexagonal cumple una función relacionada, pero ambos conceptos no son exactamente lo mismo.

---

## 12. Buenas prácticas

1. **Adapta lo necesario:** evita crear capas de adaptación que no aporten compatibilidad, aislamiento o una traducción útil.
2. **Mantén el adaptador enfocado:** concentra en él las conversiones de tipos, nombres de métodos y particularidades del proveedor.
3. **Define una interfaz objetivo estable:** facilita sustituir la implementación sin afectar al cliente.
4. **Gestiona correctamente los errores:** traduce las excepciones del proveedor cuando sea necesario y conserva información útil para diagnosticar problemas.
5. **No ocultes diferencias importantes:** si dos proveedores tienen garantías o comportamientos diferentes, documenta esas diferencias.
6. **Valida las conversiones:** presta atención a unidades, formatos, zonas horarias, monedas, precisión y límites numéricos.
7. **Prueba el contrato del adaptador:** verifica que cada implementación cumple las expectativas de la interfaz objetivo.
8. **Evita filtrar tipos externos:** procura que los modelos específicos del proveedor no se propaguen por toda la aplicación.
9. **Prefiere composición en Java:** suele permitir adaptadores más flexibles que la herencia.
10. **No confundas adaptación con lógica de negocio:** el adaptador traduce interfaces; las decisiones de negocio deben permanecer en la capa correspondiente.

---

## 13. Pruebas recomendadas

Un adaptador debe comprobarse tanto por la traducción que realiza como por el comportamiento que presenta al cliente.

Por ejemplo, para `PagoExternoAdapter`, conviene verificar que:

- Un importe de `25.50` euros se convierte en `2550` céntimos.
- Se invoca el método correcto del proveedor.
- Los importes inválidos se rechazan según el contrato definido.
- Los errores del proveedor se gestionan o propagan según la política establecida.
- La aplicación puede utilizar una implementación alternativa de `PasarelaPago`.

Es recomendable utilizar pruebas unitarias con dobles de prueba para verificar las llamadas y pruebas de integración cuando sea necesario comprobar la comunicación real con el proveedor.

---

## 14. Conclusión

El patrón **Adapter** permite integrar componentes con interfaces incompatibles sin tener que modificar sus implementaciones originales.

Su principal beneficio es que aísla las diferencias entre sistemas, favoreciendo la reutilización, el mantenimiento y la evolución independiente de los componentes.

En Java, la variante basada en composición suele ser la opción más flexible, ya que permite implementar una interfaz objetivo y delegar las operaciones en un objeto existente.

Conviene utilizarlo cuando exista una incompatibilidad real entre interfaces, especialmente al integrar servicios externos, bibliotecas de terceros o sistemas heredados.

La idea fundamental es sencilla: **el cliente utiliza la interfaz que necesita y el adaptador se encarga de traducir las llamadas a la interfaz que ofrece el componente existente.**

---

## 15. Referencias

- **Design Patterns: Elements of Reusable Object-Oriented Software**, de Erich Gamma, Richard Helm, Ralph Johnson y John Vlissides (Gang of Four).
- Refactoring.Guru — Adapter: https://refactoring.guru/design-patterns/adapter
- Java Documentation: https://docs.oracle.com/en/java/
- SourceMaking — Adapter Design Pattern: https://sourcemaking.com/design_patterns/adapter
