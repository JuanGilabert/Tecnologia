# Pruebas de integración (Integration Testing)

## 1. Introducción

Las **pruebas de integración** son un tipo de prueba de software cuyo objetivo es verificar que dos o más componentes funcionan correctamente cuando interactúan entre sí.

Mientras que las pruebas unitarias suelen comprobar una unidad de código de forma aislada, las pruebas de integración se centran en las relaciones y los contratos entre componentes.

Una aplicación puede contener clases individuales que funcionan correctamente y, aun así, presentar errores cuando se conectan.

Por ejemplo:

- Un servicio genera correctamente una consulta, pero el repositorio no la ejecuta bien contra la base de datos.
- Un controlador recibe una petición HTTP, pero la validación de entrada no funciona como se esperaba.
- Un servicio envía un mensaje a un sistema de mensajería con un formato incorrecto.
- Una aplicación invoca una API externa y no interpreta correctamente su respuesta.
- Una transacción guarda algunos cambios, pero no todos los que deberían confirmarse conjuntamente.

Las pruebas de integración permiten detectar este tipo de problemas.

### Objetivos principales

1. Verificar la comunicación entre componentes.
2. Comprobar que los contratos entre módulos se respetan.
3. Detectar errores de configuración e integración.
4. Validar la persistencia y recuperación de datos.
5. Comprobar transacciones y gestión de errores.
6. Verificar la comunicación con APIs, colas y otros sistemas.
7. Reducir los defectos que aparecen al desplegar o ejecutar la aplicación.
8. Aumentar la confianza en que los componentes funcionan correctamente cuando colaboran.

---

## 2. ¿Qué problema resuelven?

Las pruebas unitarias permiten verificar el comportamiento de una clase de forma controlada. Sin embargo, normalmente no comprueban todos los aspectos de su interacción con sistemas reales.

Imaginemos una aplicación que permite registrar pedidos.

El sistema está compuesto por:

- `OrderController`: recibe las peticiones HTTP.
- `OrderService`: aplica las reglas de negocio.
- `OrderRepository`: almacena los pedidos.
- Una base de datos PostgreSQL.

Cada clase puede superar sus pruebas unitarias y, aun así, existir errores como:

- Una entidad está mal mapeada a una columna de la base de datos.
- Una consulta devuelve datos incorrectos.
- Una restricción de integridad impide guardar un pedido válido.
- Una transacción no se revierte cuando ocurre una excepción.
- El controlador devuelve un código HTTP distinto del definido en el contrato.
- La configuración de producción no coincide con la configuración de pruebas.

Las pruebas de integración verifican estas interacciones utilizando los componentes reales que sean relevantes para el escenario.

### Sin pruebas de integración

1. Se desarrollan los componentes.
2. Se prueban de manera aislada.
3. Se conectan los módulos.
4. Aparecen errores de comunicación, configuración o persistencia.
5. Los errores pueden descubrirse tarde, durante el despliegue o la ejecución real.

### Con pruebas de integración

1. Se identifican los componentes que deben colaborar.
2. Se prepara un entorno de prueba controlado.
3. Se ejecuta una operación a través de los componentes relevantes.
4. Se verifican los resultados y los efectos observables.
5. Se detectan los problemas antes de publicar la aplicación.

---

## 3. ¿Qué se considera una prueba de integración?

Una prueba de integración comprueba una interacción entre componentes o sistemas.

No existe un único tamaño obligatorio para una prueba de integración. Puede verificar, por ejemplo:

- Un repositorio contra una base de datos real.
- Un servicio de aplicación contra un repositorio real.
- Un controlador HTTP contra los servicios y la persistencia.
- Un productor y un consumidor de mensajes.
- Un cliente HTTP contra un servidor de prueba.
- Dos servicios que intercambian información mediante una API.

Lo importante es que el objetivo sea validar una integración real o representativa, en lugar de sustituir todas las dependencias por mocks.

### Ejemplo conceptual

Supongamos que queremos registrar un pedido.

```text
Cliente HTTP
     |
     v
OrderController
     |
     v
OrderService
     |
     v
OrderRepository
     |
     v
Base de datos
```

Una prueba unitaria podría comprobar que `OrderService` valida correctamente una cantidad.

Una prueba de integración podría ejecutar `OrderRepository` contra PostgreSQL y comprobar que el pedido se guarda y se recupera correctamente.

Otra prueba podría enviar una petición HTTP al controlador, atravesar el servicio y el repositorio, y comprobar que la operación completa produce la respuesta esperada.

**Una prueba que atraviesa más capas no es automáticamente mejor.** El alcance debe elegirse en función de los riesgos que se quieren comprobar y del coste de mantener la prueba.

---

## 4. Diferencias entre pruebas unitarias, de integración y funcionales

Estos tipos de pruebas son complementarios, aunque sus límites pueden variar según el equipo y la arquitectura.

| Característica | Unitarias | Integración | Funcionales |
|---|---|---|---|
| Objetivo | Verificar una unidad de comportamiento | Verificar interacciones entre componentes | Verificar requisitos funcionales |
| Alcance habitual | Método, clase o unidad aislada | Componentes conectados | Una funcionalidad o flujo |
| Dependencias reales | Normalmente limitadas | Se utilizan cuando son relevantes | Depende del nivel de prueba |
| Base de datos real | Normalmente no | Frecuente | Puede utilizarse |
| API o interfaz | Generalmente no | Opcional | Frecuente, pero no obligatoria |
| Velocidad | Muy alta, normalmente | Intermedia | Variable |
| Diagnóstico | Suele ser sencillo | Puede requerir revisar varios componentes | Depende del alcance |
| Ejemplo | Calcular un descuento | Guardar y recuperar un pedido en PostgreSQL | Completar una compra |

Una prueba puede ser funcional y, al mismo tiempo, tener características de integración. Por ejemplo, una prueba que envía una petición HTTP y utiliza una base de datos real puede comprobar tanto el comportamiento funcional como la integración entre capas.

Las categorías describen objetivos distintos y no siempre son mutuamente excluyentes.

---

## 5. Tipos de pruebas de integración

### 5.1. Integración entre clases o módulos

Comprueba que varios componentes de la aplicación colaboran correctamente.

Ejemplo:

- Un servicio invoca un repositorio.
- El repositorio devuelve una entidad.
- El servicio transforma el resultado.
- El consumidor recibe el resultado esperado.

### 5.2. Integración con bases de datos

Verifica la interacción entre la aplicación y el sistema de persistencia.

Permite detectar problemas relacionados con:

- Consultas SQL.
- Mapeo objeto-relacional.
- Restricciones de integridad.
- Tipos de datos.
- Transacciones.
- Claves primarias y foráneas.
- Migraciones de esquema.
- Persistencia y recuperación de información.

### 5.3. Integración entre API y aplicación

Comprueba que una petición HTTP atraviesa las capas correspondientes y que la respuesta respeta el contrato definido.

Puede incluir:

- Serialización y deserialización JSON.
- Validaciones.
- Códigos HTTP.
- Gestión de excepciones.
- Autenticación y autorización.
- Acceso a la persistencia.

### 5.4. Integración con servicios externos

Verifica la interacción con sistemas externos, como:

- Pasarelas de pago.
- Servicios de correo.
- Proveedores de identidad.
- APIs de terceros.
- Sistemas de facturación.

Cuando el objetivo es comprobar el contrato y el comportamiento del cliente, se puede utilizar un servidor simulado controlado. Para validar la compatibilidad real con el proveedor, también puede ser necesario un entorno de pruebas del propio proveedor.

### 5.5. Integración con sistemas de mensajería

Comprueba el envío y consumo de mensajes mediante herramientas como RabbitMQ, Kafka o sistemas equivalentes.

Puede verificar:

- El formato de los mensajes.
- La publicación y recepción.
- El procesamiento correcto.
- La gestión de errores.
- Los reintentos.
- La idempotencia.
- El comportamiento ante mensajes duplicados.

### 5.6. Integración entre microservicios

Comprueba que dos o más servicios se comunican mediante APIs, eventos o mensajería.

Estas pruebas pueden descubrir incompatibilidades de contratos, errores de serialización y diferencias entre versiones.

No es necesario levantar todo el ecosistema de microservicios para cada prueba. Se debe seleccionar el número mínimo de componentes reales que permita verificar la integración relevante.

---

## 6. Estrategias de integración

Existen diferentes estrategias para organizar las pruebas de integración.

### 6.1. Integración incremental

Los componentes se conectan y prueban progresivamente.

Por ejemplo:

1. Integrar el servicio con el repositorio.
2. Verificar la persistencia.
3. Integrar el controlador HTTP.
4. Verificar el flujo completo.

Esta estrategia facilita localizar problemas a medida que se incorporan componentes.

### 6.2. Top-down

Se comienza por los componentes de nivel superior y se incorporan progresivamente los componentes inferiores.

Si los componentes inferiores todavía no están disponibles, pueden utilizarse sustitutos temporales.

### 6.3. Bottom-up

Se prueban primero los componentes inferiores, como repositorios o adaptadores, y después se incorporan los servicios y controladores.

Este enfoque resulta útil cuando la persistencia o los adaptadores contienen una parte importante del riesgo técnico.

### 6.4. Big Bang

Se conectan varios componentes al mismo tiempo y se prueba el conjunto.

Puede ser sencillo de organizar en sistemas pequeños, pero resulta más difícil identificar el origen de un fallo cuando intervienen muchos componentes.

### ¿Qué estrategia conviene?

En aplicaciones modernas suele resultar práctico combinar pruebas de integración específicas con pruebas más amplias de los flujos críticos.

No es necesario elegir una sola estrategia para todo el proyecto.

---

## 7. Ejemplo práctico: integración con una base de datos

Supongamos que una aplicación Java utiliza Spring Boot, Spring Data JPA y PostgreSQL.

Queremos verificar que un pedido puede guardarse y recuperarse correctamente.

### Estructura conceptual

```text
OrderIntegrationTest
        |
        v
OrderRepository
        |
        v
Spring Data JPA
        |
        v
PostgreSQL
```

Esta prueba no se limita a simular el repositorio. Utiliza el mecanismo real de persistencia para comprobar que la entidad y la configuración funcionan correctamente.

### Entidad de ejemplo

```java
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.Table;

import java.math.BigDecimal;

@Entity
@Table(name = "orders")
public class Order {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String customerEmail;

    private BigDecimal total;

    protected Order() {
    }

    public Order(String customerEmail, BigDecimal total) {
        this.customerEmail = customerEmail;
        this.total = total;
    }

    public Long getId() {
        return id;
    }

    public String getCustomerEmail() {
        return customerEmail;
    }

    public BigDecimal getTotal() {
        return total;
    }
}
```

### Repositorio

```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface OrderRepository
        extends JpaRepository<Order, Long> {
}
```

### Prueba de integración

```java
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertTrue;

import java.math.BigDecimal;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;

@DataJpaTest
class OrderRepositoryIntegrationTest {

    @Autowired
    private OrderRepository orderRepository;

    @Test
    void shouldSaveAndRetrieveOrder() {
        Order order = new Order(
            "customer@example.com",
            new BigDecimal("149.90")
        );

        Order saved = orderRepository.saveAndFlush(order);

        assertTrue(saved.getId() != null);

        Order retrieved = orderRepository.findById(saved.getId())
            .orElseThrow();

        assertEquals(
            "customer@example.com",
            retrieved.getCustomerEmail()
        );

        assertEquals(
            new BigDecimal("149.90"),
            retrieved.getTotal()
        );
    }
}
```

### ¿Qué comprueba?

La prueba verifica que:

1. Spring puede configurar el repositorio.
2. La entidad puede persistirse.
3. Se genera un identificador.
4. El registro puede recuperarse.
5. Los datos recuperados son los esperados.

`@DataJpaTest` configura un contexto orientado a la persistencia. Por defecto puede utilizar una base de datos embebida si existe una disponible; por tanto, **este ejemplo no garantiza por sí solo que se esté utilizando PostgreSQL**.

Si se quiere comprobar la compatibilidad real con PostgreSQL, conviene utilizar una instancia de PostgreSQL de prueba, por ejemplo mediante Testcontainers.

---

## 8. Uso de Testcontainers para probar con dependencias reales

[Testcontainers](https://testcontainers.com/) permite levantar dependencias reales en contenedores durante las pruebas.

Es especialmente útil para verificar integraciones con:

- PostgreSQL.
- MySQL.
- MongoDB.
- Kafka.
- RabbitMQ.
- Redis.
- Otros servicios compatibles con su ecosistema.

### ¿Por qué utilizar una base de datos real?

Una base de datos embebida puede comportarse de forma diferente a la base de datos de producción.

Las diferencias pueden afectar a:

- Sintaxis SQL.
- Tipos de datos.
- Restricciones.
- Índices.
- Consultas específicas del motor.
- Comportamiento transaccional.
- Migraciones.

Testcontainers permite ejecutar las pruebas contra una instancia real del motor elegido y reduce el riesgo de detectar estos problemas demasiado tarde.

### Ejemplo conceptual con PostgreSQL

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.DynamicPropertyRegistry;
import org.springframework.test.context.DynamicPropertySource;
import org.testcontainers.containers.PostgreSQLContainer;
import org.testcontainers.junit.jupiter.Container;
import org.testcontainers.junit.jupiter.Testcontainers;

import static org.junit.jupiter.api.Assertions.assertNotNull;

@Testcontainers
@SpringBootTest
class OrderPostgresIntegrationTest {

    @Container
    static final PostgreSQLContainer<?> postgres =
        new PostgreSQLContainer<>("postgres:16-alpine")
            .withDatabaseName("orders_test")
            .withUsername("test")
            .withPassword("test");

    @DynamicPropertySource
    static void configureDatabase(
            DynamicPropertyRegistry registry) {

        registry.add(
            "spring.datasource.url",
            postgres::getJdbcUrl
        );

        registry.add(
            "spring.datasource.username",
            postgres::getUsername
        );

        registry.add(
            "spring.datasource.password",
            postgres::getPassword
        );

        registry.add(
            "spring.datasource.driver-class-name",
            postgres::getDriverClassName
        );
    }

    @Autowired
    private OrderRepository orderRepository;

    @Test
    void shouldPersistOrderInPostgres() {
        Order saved = orderRepository.saveAndFlush(
            new Order(
                "customer@example.com",
                new java.math.BigDecimal("149.90")
            )
        );

        assertNotNull(saved.getId());
    }
}
```

Este ejemplo requiere las dependencias de Testcontainers, el módulo de PostgreSQL y la configuración de Spring Boot correspondiente. También necesita un entorno donde Docker o el runtime compatible pueda iniciar contenedores.

El contenedor utiliza una versión concreta de PostgreSQL para que el entorno de prueba sea reproducible. En un proyecto real conviene alinear la versión con la utilizada en producción.

Para proyectos con migraciones de base de datos, es preferible probar el esquema mediante la misma herramienta de migración que se usa en producción, en lugar de depender únicamente de la generación automática del esquema a partir de las entidades.

---

## 9. Pruebas de integración de servicios en Spring Boot

Además de comprobar repositorios, se puede verificar la colaboración entre servicios y persistencia.

Supongamos que el servicio crea un pedido.

```java
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.math.BigDecimal;

@Service
public class OrderService {

    private final OrderRepository repository;

    public OrderService(OrderRepository repository) {
        this.repository = repository;
    }

    @Transactional
    public Order createOrder(
            String customerEmail,
            BigDecimal total) {

        if (customerEmail == null || customerEmail.isBlank()) {
            throw new IllegalArgumentException(
                "Customer email is required"
            );
        }

        if (total == null || total.signum() <= 0) {
            throw new IllegalArgumentException(
                "Total must be greater than zero"
            );
        }

        return repository.save(
            new Order(customerEmail, total)
        );
    }
}
```

Una prueba de integración puede cargar el contexto de Spring y comprobar que el servicio y el repositorio funcionan juntos.

```java
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNotNull;

import java.math.BigDecimal;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.transaction.annotation.Transactional;

@SpringBootTest
@Transactional
class OrderServiceIntegrationTest {

    @Autowired
    private OrderService orderService;

    @Autowired
    private OrderRepository orderRepository;

    @Test
    void shouldCreateAndPersistOrder() {
        Order created = orderService.createOrder(
            "customer@example.com",
            new BigDecimal("149.90")
        );

        assertNotNull(created.getId());

        Order stored = orderRepository.findById(created.getId())
            .orElseThrow();

        assertEquals(
            "customer@example.com",
            stored.getCustomerEmail()
        );

        assertEquals(
            new BigDecimal("149.90"),
            stored.getTotal()
        );
    }
}
```

### ¿Qué valida?

- Que el contexto de Spring puede construir los componentes.
- Que el servicio puede invocar el repositorio.
- Que la transacción se gestiona en el contexto de la aplicación.
- Que el pedido se persiste y se puede recuperar.

`@Transactional` en una prueba de Spring suele provocar que la transacción de prueba se revierta al finalizar. Esto ayuda a aislar los datos entre pruebas, pero también significa que esta prueba no verifica necesariamente el comportamiento de una confirmación transaccional independiente en condiciones reales.

Cuando se prueban confirmaciones, eventos posteriores a la transacción o procesos asíncronos, puede ser necesario diseñar el escenario con una gestión explícita de las transacciones y del estado.

---

## 10. Pruebas de integración de APIs REST

Una prueba de integración de API puede verificar que el controlador, los servicios, la validación y la persistencia funcionan conjuntamente.

En Spring Boot, `MockMvc` permite enviar peticiones al entorno MVC sin levantar un servidor HTTP real. Es útil para probar el procesamiento web y las capas integradas dentro del contexto de la aplicación.

### Ejemplo con MockMvc

```java
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;

@SpringBootTest
@AutoConfigureMockMvc
class OrderControllerIntegrationTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void shouldCreateOrderThroughHttpLayer() throws Exception {
        mockMvc.perform(
                post("/api/orders")
                    .contentType(MediaType.APPLICATION_JSON)
                    .content("""
                        {
                          "customerEmail": "customer@example.com",
                          "total": 149.90
                        }
                        """)
            )
            .andExpect(status().isCreated())
            .andExpect(jsonPath("$.id").exists())
            .andExpect(
                jsonPath("$.customerEmail")
                    .value("customer@example.com")
            );
    }
}
```

El ejemplo presupone que existe un controlador para `POST /api/orders`, que acepta ese JSON y que devuelve `201 Created`.

El código de respuesta y la estructura JSON deben corresponder al contrato real de la aplicación.

### ¿MockMvc es una prueba E2E?

No necesariamente. `MockMvc` prueba la capa MVC dentro del entorno de Spring, pero no inicia por sí solo un servidor HTTP real ni verifica toda la infraestructura de red.

Si se necesita comprobar también el arranque de un servidor y la comunicación HTTP real, se puede levantar la aplicación en un puerto aleatorio y enviar peticiones mediante un cliente HTTP.

### ¿Qué escenarios conviene probar?

- Creación correcta.
- Entrada inválida.
- Recurso inexistente.
- Errores de persistencia.
- Respuestas de error.
- Validación de campos.
- Autorización y permisos, cuando correspondan.

---

## 11. Integración con APIs externas

Muchas aplicaciones dependen de servicios externos, como proveedores de pagos, servicios de identidad o APIs de terceros.

Hay dos objetivos distintos que conviene separar.

### Verificar el comportamiento del cliente

Podemos utilizar un servidor HTTP simulado que devuelva respuestas controladas.

Esto permite comprobar que el cliente:

- Construye correctamente las peticiones.
- Envía las cabeceras necesarias.
- Interpreta las respuestas.
- Gestiona los códigos de error.
- Aplica la política de reintentos esperada.

### Verificar la compatibilidad con el proveedor real

Para comprobar la integración real, se puede utilizar un entorno sandbox proporcionado por el proveedor, si existe.

Esto permite descubrir diferencias que un servidor simulado no puede detectar.

### Buenas prácticas

- No realizar pagos reales durante las pruebas.
- Utilizar credenciales de prueba.
- No almacenar secretos en el código.
- Controlar tiempos de espera.
- Simular respuestas de error cuando corresponda.
- Verificar que los reintentos no duplican operaciones.
- Utilizar identificadores de idempotencia si el contrato del proveedor los admite.
- Mantener separadas las pruebas contra dobles controlados y las pruebas contra el sandbox real.

Un mock es útil para controlar escenarios, pero no demuestra que el proveedor externo esté disponible ni que su implementación real sea compatible con la aplicación.

---

## 12. Integración entre microservicios

En una arquitectura de microservicios, cada servicio puede desplegarse y evolucionar de forma independiente. Esto aumenta la importancia de los contratos entre componentes.

Una integración puede producirse mediante:

- REST o HTTP.
- gRPC.
- Eventos.
- Colas de mensajes.
- Streams.

### Ejemplo: pedidos y pagos

```text
Order Service
     |
     | Solicitud de pago
     v
Payment Service
     |
     | Resultado
     v
Order Service
```

Las pruebas pueden verificar que:

1. El servicio de pedidos envía una solicitud con el contrato correcto.
2. El servicio de pagos interpreta la solicitud.
3. El resultado se representa correctamente.
4. Un pago rechazado no produce un pedido marcado como pagado.
5. Los errores de comunicación se gestionan de acuerdo con la política definida.

### Pruebas de contrato

Las pruebas de contrato verifican que un consumidor y un proveedor respetan un contrato de comunicación acordado.

Pueden reducir la necesidad de ejecutar siempre todos los microservicios juntos, ya que permiten comprobar la compatibilidad de las interfaces de forma más aislada.

Herramientas como Pact facilitan la definición y verificación de contratos entre consumidores y proveedores.

Las pruebas de contrato complementan las pruebas de integración: no sustituyen todos los escenarios que requieren comprobar el comportamiento de varios servicios en funcionamiento.

### Consideraciones especiales

En sistemas distribuidos es importante contemplar:

- Latencia.
- Fallos parciales.
- Reintentos.
- Duplicación de mensajes.
- Consistencia eventual.
- Cambios de versión en los contratos.
- Idempotencia.
- Orden de procesamiento, cuando sea relevante.

Una prueba de integración distribuida debe controlar estos factores cuando formen parte de los riesgos funcionales del sistema.

---

## 13. Pruebas de integración y transacciones

Las transacciones son una fuente frecuente de errores en aplicaciones que manejan datos persistentes.

Una prueba de integración puede comprobar que varias operaciones se confirman conjuntamente o que los cambios se revierten cuando una operación falla.

Imaginemos una transferencia entre cuentas.

La operación debe:

1. Verificar el saldo disponible.
2. Restar el importe de la cuenta de origen.
3. Añadir el importe a la cuenta de destino.
4. Confirmar ambas modificaciones conjuntamente.

Si falla la actualización de la cuenta de destino, no debería quedar la primera modificación confirmada de forma aislada cuando el requisito exige atomicidad.

### Escenarios de prueba

- Transferencia válida.
- Saldo insuficiente.
- Cuenta de destino inexistente.
- Error durante la segunda actualización.
- Dos operaciones concurrentes.
- Repetición de una misma solicitud.

Las pruebas de transacciones requieren especial atención al entorno utilizado. Una prueba envuelta en una transacción que se revierte al finalizar no necesariamente reproduce una confirmación real en el sistema.

Para escenarios de concurrencia, aislamiento o recuperación, puede ser necesario utilizar varias transacciones independientes y una base de datos del mismo tipo que la de producción.

---

## 14. Preparación y aislamiento de datos

La calidad de las pruebas de integración depende en gran medida del estado del entorno.

Si una prueba necesita que exista un cliente concreto, debe preparar ese dato o utilizar un mecanismo controlado para garantizar su existencia.

### Estrategias habituales

**Preparar datos al inicio:** crear las entidades necesarias antes de ejecutar el escenario.

**Limpiar después de cada prueba:** eliminar los registros generados cuando sea seguro hacerlo.

**Utilizar una base de datos exclusiva:** asignar una instancia o esquema aislado al conjunto de pruebas.

**Revertir transacciones:** cuando sea compatible con el comportamiento que se quiere verificar.

**Usar contenedores efímeros:** iniciar dependencias específicas para la suite de pruebas y destruirlas al terminar.

### Buenas prácticas

- No depender de los datos creados por otra prueba.
- Evitar identificadores fijos que puedan colisionar en ejecuciones paralelas.
- No compartir datos mutables entre escenarios.
- Preparar datos mínimos y explícitos.
- Controlar el estado de los servicios externos.
- Evitar utilizar datos personales reales.
- Comprobar que las pruebas pueden ejecutarse de forma repetida.

---

## 15. Pruebas de integración y calidad del código

Las pruebas de integración no miden directamente la legibilidad ni el diseño del código, pero aportan información valiosa sobre su comportamiento en condiciones más próximas a la ejecución real.

### Relación con SOLID

- **Single Responsibility Principle (SRP):** facilita identificar qué componente provoca un fallo.
- **Open/Closed Principle (OCP):** permite extender comportamientos sin alterar innecesariamente módulos estables.
- **Liskov Substitution Principle (LSP):** los componentes intercambiables deben respetar los contratos que las pruebas verifican.
- **Interface Segregation Principle (ISP):** contratos pequeños hacen más claras las dependencias.
- **Dependency Inversion Principle (DIP):** permite controlar dependencias y sustituir proveedores cuando la prueba no necesita ejecutarlos realmente.

### Relación con Clean Code

Los nombres, contratos y responsabilidades claros ayudan a comprender qué está comprobando cada escenario y a diagnosticar los fallos.

### Relación con DRY, KISS y YAGNI

- **DRY:** compartir utilidades de preparación cuando reduce duplicación sin ocultar los datos relevantes.
- **KISS:** mantener los escenarios y el entorno tan sencillos como sea posible.
- **YAGNI:** evitar construir una infraestructura compleja de integración si unas pocas herramientas existentes resuelven la necesidad.

Las pruebas deben proteger el comportamiento sin quedar excesivamente acopladas a la estructura interna de la implementación.

---

## 16. Relación con SonarQube y la cobertura

Las pruebas de integración y SonarQube cumplen funciones diferentes.

- **JUnit:** permite definir y ejecutar pruebas automatizadas.
- **Spring Boot Test:** facilita probar componentes y contextos de Spring.
- **Testcontainers:** permite ejecutar dependencias reales en contenedores.
- **JaCoCo:** mide la cobertura durante la ejecución de las pruebas.
- **SonarQube:** analiza el código y puede incorporar informes de cobertura y resultados compatibles.

SonarQube no demuestra por sí solo que dos componentes se integren correctamente. Esa comprobación depende de las pruebas que se ejecuten y de los escenarios que cubran.

### Cobertura frente a calidad de integración

Una prueba puede ejecutar muchas líneas y no comprobar correctamente la persistencia, la respuesta de una API o la gestión de errores.

Por ello, es importante verificar explícitamente:

- Los resultados de las operaciones.
- Los efectos sobre los datos.
- Los contratos entre componentes.
- Los errores previsibles.
- La consistencia del estado.
- Las condiciones de recuperación.

La cobertura es un indicador complementario, no una garantía de calidad.

---

## 17. Integración en CI/CD

Las pruebas de integración deberían formar parte del proceso automatizado de construcción y validación del software.

Un flujo habitual es:

1. Descargar el código.
2. Instalar las dependencias.
3. Compilar.
4. Ejecutar las pruebas unitarias.
5. Iniciar los servicios necesarios.
6. Ejecutar las pruebas de integración.
7. Ejecutar las pruebas funcionales seleccionadas.
8. Generar informes.
9. Ejecutar el análisis estático.
10. Evaluar los criterios de calidad.
11. Publicar los artefactos si se cumplen las condiciones definidas.

### Ejemplo conceptual con Maven

```bash
mvn clean verify
```

En Maven, la fase `verify` ejecuta las fases anteriores del ciclo de construcción, incluidas las pruebas que estén configuradas para esas fases.

La separación entre pruebas unitarias y de integración depende de la configuración del proyecto. Por ejemplo, Maven Surefire se utiliza habitualmente para las pruebas unitarias, mientras que Maven Failsafe se puede configurar para ejecutar pruebas de integración en las fases `integration-test` y `verify`.

No basta con nombrar una clase `*IT.java`: hay que configurar el plugin correspondiente para que se ejecute en la fase prevista.

### Buenas prácticas

- Ejecutar pruebas unitarias antes de las suites más costosas.
- Mantener un entorno de integración reproducible.
- Utilizar contenedores cuando aporten aislamiento y fidelidad.
- Publicar informes de fallos.
- Guardar logs relevantes.
- Evitar que las pruebas dependan de servicios inestables.
- Separar las pruebas rápidas de las que requieren infraestructura.
- Ejecutar los escenarios críticos antes de una publicación.
- Controlar la duración y estabilidad de la suite.

---

## 18. Errores frecuentes

### Utilizar mocks para todas las dependencias

Si todos los componentes están simulados, se comprueba principalmente el comportamiento de los mocks y no la integración real.

**Solución:** utilizar dependencias reales cuando sean relevantes para el objetivo de la prueba.

### Probar siempre toda la aplicación

Levantar todo el sistema para verificar cada pequeña interacción aumenta el coste y dificulta localizar errores.

**Solución:** elegir el menor alcance que permita comprobar la integración necesaria.

### Depender de una base de datos distinta de producción

Una base de datos embebida puede ocultar incompatibilidades con el motor real.

**Solución:** utilizar una instancia del motor de producción para las pruebas que necesiten validar esa compatibilidad.

### Compartir datos entre pruebas

Las pruebas fallan según el orden de ejecución.

**Solución:** aislar el estado y preparar datos explícitos.

### Ignorar las transacciones

Una prueba parece correcta porque su transacción se revierte automáticamente, aunque el flujo real tenga un problema de confirmación.

**Solución:** diseñar pruebas específicas para el comportamiento transaccional que se necesita verificar.

### No probar los errores

Solo se verifica que los componentes funcionan cuando todo sale bien.

**Solución:** incluir fallos de persistencia, entradas inválidas, indisponibilidad y errores de comunicación cuando sean relevantes.

### Confundir integración con pruebas funcionales completas

Una prueba de repositorio puede demostrar que se guardan los datos, pero no que un usuario puede completar correctamente todo el flujo.

**Solución:** complementar las pruebas de integración con pruebas funcionales y de aceptación.

---

## 19. Estrategia para introducir pruebas de integración

### Fase 1: identificar las dependencias críticas

Determinar qué interacciones presentan más riesgo:

- Base de datos.
- APIs externas.
- Mensajería.
- Autenticación.
- Transacciones.
- Microservicios.

### Fase 2: comenzar por los adaptadores

Probar repositorios, clientes HTTP y productores o consumidores de mensajes con dependencias controladas o reales según el objetivo.

### Fase 3: validar las operaciones principales

Crear pruebas que atraviesen los componentes necesarios para ejecutar operaciones críticas.

### Fase 4: incorporar datos y entornos reproducibles

Utilizar datos de prueba controlados, contenedores o entornos aislados.

### Fase 5: automatizar

Integrar la ejecución en Maven, Gradle o el sistema de construcción correspondiente.

### Fase 6: añadir casos de error

Verificar fallos de persistencia, errores de red, respuestas inesperadas y situaciones transaccionales.

### Fase 7: revisar el coste de mantenimiento

Identificar pruebas lentas, inestables o redundantes y ajustar su alcance.

El objetivo es obtener confianza en las interacciones críticas sin convertir la suite en una réplica innecesariamente compleja de producción.

---

## 20. Checklist de revisión

Antes de considerar terminada una prueba de integración, comprueba:

- Está claro qué componentes se están integrando.
- La prueba verifica una interacción real o representativa.
- Las dependencias relevantes son las adecuadas para el objetivo.
- Los datos iniciales están controlados.
- Los resultados y efectos secundarios importantes se verifican.
- Se contemplan errores previsibles.
- Las transacciones se prueban de acuerdo con el comportamiento requerido.
- El entorno es reproducible.
- Las pruebas pueden ejecutarse de forma independiente.
- Los fallos producen información útil.
- La suite se ejecuta automáticamente en CI/CD.
- El tiempo de ejecución es razonable para el valor que aporta.
- Se han separado los escenarios de integración de los flujos E2E cuando corresponda.

---

## 21. Conclusión

Las pruebas de integración permiten verificar que los componentes de una aplicación funcionan correctamente cuando colaboran entre sí.

Son especialmente importantes en sistemas que dependen de bases de datos, servicios externos, APIs, colas de mensajes y arquitecturas distribuidas.

Su principal valor consiste en detectar errores que no suelen aparecer al probar componentes de forma aislada: incompatibilidades, problemas de configuración, fallos de persistencia, contratos incorrectos y errores en la coordinación de operaciones.

Una estrategia de calidad equilibrada combina:

- Pruebas unitarias para reglas y unidades pequeñas.
- Pruebas de integración para interacciones reales.
- Pruebas funcionales para verificar requisitos.
- Pruebas de aceptación para validar objetivos de negocio.
- Análisis estático con herramientas como SonarQube.
- Cobertura con herramientas como JaCoCo.
- Automatización mediante CI/CD.

El objetivo no es levantar toda la infraestructura para cada prueba, sino seleccionar el alcance adecuado para comprobar las integraciones importantes con un coste razonable y resultados reproducibles.

---

## 22. Referencias oficiales

- JUnit 5: https://junit.org/junit5/
- Spring Boot Testing: https://docs.spring.io/spring-boot/reference/testing/
- Spring Framework Testing: https://docs.spring.io/spring-framework/reference/testing.html
- Testcontainers: https://testcontainers.com/
- Testcontainers para Java: https://java.testcontainers.org/
- REST Assured: https://rest-assured.io/
- Pact: https://docs.pact.io/
- JaCoCo: https://www.jacoco.org/jacoco/
- SonarQube: https://docs.sonarsource.com/sonarqube-server/
- Apache Maven Failsafe Plugin: https://maven.apache.org/surefire/maven-failsafe-plugin/
- Martin Fowler, The Practical Test Pyramid: https://martinfowler.com/articles/practical-test-pyramid.html
