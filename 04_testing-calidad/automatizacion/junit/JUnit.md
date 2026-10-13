# JUnit: testing y calidad del código en Java

## 1. Introducción

**JUnit** es un framework de testing para Java que permite escribir, organizar y ejecutar pruebas automatizadas.

Se utiliza principalmente para verificar que las clases, métodos y componentes de una aplicación funcionan según lo esperado.

JUnit permite definir casos de prueba, preparar datos, ejecutar el código que se quiere comprobar, comparar los resultados obtenidos con los esperados y detectar automáticamente comportamientos incorrectos.

Es una herramienta habitual en proyectos Java que utilizan Maven o Gradle y se integra con entornos de desarrollo, sistemas de integración continua y herramientas de análisis de calidad.

### Objetivos principales

- Verificar el comportamiento del código.
- Detectar defectos de forma temprana.
- Prevenir regresiones cuando se modifica una funcionalidad.
- Facilitar la refactorización segura.
- Documentar mediante ejemplos el comportamiento esperado.
- Comprobar reglas de negocio y casos límite.
- Integrarse con herramientas como Mockito, Spring Boot, JaCoCo y Jenkins.
- Mejorar la mantenibilidad y la confianza en el código.

**Idea clave:** JUnit permite automatizar la verificación del comportamiento del software. No garantiza por sí solo que el código esté libre de errores, pero ayuda a detectar defectos de manera sistemática y repetible.

Documentación oficial: https://junit.org/junit5/

---

## 2. ¿Qué problema resuelve JUnit?

Sin pruebas automatizadas, un desarrollador puede tener que ejecutar manualmente la aplicación para comprobar que una funcionalidad funciona correctamente.

Este proceso presenta varios inconvenientes:

- Consume tiempo.
- Es difícil repetir todos los escenarios.
- Puede dejar sin comprobar casos límite.
- Los errores pueden aparecer después de realizar una refactorización.
- Las comprobaciones manuales pueden variar entre desarrolladores.
- Resulta complicado validar muchas combinaciones de entradas.

JUnit permite transformar estas comprobaciones en pruebas ejecutables.

Por ejemplo, imaginemos un método que calcula el precio final de un producto después de aplicar un descuento.

Sin JUnit, el desarrollador podría probar algunos valores manualmente.

Con JUnit, puede automatizar escenarios como:

- Precio sin descuento.
- Descuento del 10 %.
- Descuento del 50 %.
- Precio igual a cero.
- Descuento inválido.
- Valores que no deberían ser aceptados.

Las pruebas pueden ejecutarse repetidamente cada vez que cambia el código, lo que permite detectar rápidamente si el comportamiento esperado se ha alterado.

---

## 3. ¿Qué es JUnit 5?

JUnit 5 es una generación de JUnit formada por tres componentes principales.

| Componente | Función |
|---|---|
| JUnit Platform | Proporciona la infraestructura para descubrir y ejecutar pruebas |
| JUnit Jupiter | Incluye el modelo de programación y las funcionalidades modernas para escribir pruebas |
| JUnit Vintage | Permite ejecutar pruebas antiguas basadas en JUnit 3 y JUnit 4 dentro de la plataforma, cuando se configura el motor correspondiente |

En proyectos nuevos suele utilizarse JUnit Jupiter.

Aunque habitualmente se habla de «JUnit 5», la arquitectura de JUnit 5 se distribuye mediante módulos diferenciados. Las versiones actuales de JUnit pueden tener números de versión principales posteriores, por lo que conviene consultar la documentación correspondiente a la versión elegida.

### ¿Qué aporta JUnit Jupiter?

- Anotaciones para identificar las pruebas.
- Aserciones para verificar resultados.
- Preparación y limpieza de escenarios.
- Pruebas parametrizadas.
- Pruebas anidadas.
- Etiquetas para clasificar pruebas.
- Extensiones para ampliar el comportamiento.
- Integración con herramientas de construcción.

---

## 4. Conceptos fundamentales

### 4.1. Test case

Un *test case* es un caso de prueba que verifica un comportamiento concreto.

Por ejemplo, comprobar que un servicio devuelve el precio esperado al aplicar un descuento.

### 4.2. Test suite

Una *test suite* es un conjunto de pruebas que se ejecutan como una unidad lógica.

Puede agrupar pruebas de una clase, un módulo o una categoría funcional.

### 4.3. Assertion

Una aserción compara el resultado obtenido con una condición esperada.

Si la condición no se cumple, la prueba falla.

Ejemplo:

```java
assertEquals(90.0, resultado);
```

Esta aserción comprueba que el resultado sea igual a `90.0`. Para comparar valores monetarios reales, es preferible utilizar `BigDecimal` y definir explícitamente la política de redondeo.

### 4.4. Fixture

Un *fixture* es el conjunto de datos y condiciones que necesita una prueba para ejecutarse.

Puede incluir objetos, configuraciones, usuarios de prueba o dependencias simuladas.

### 4.5. Test runner

Es el componente que descubre y ejecuta las pruebas.

En los proyectos Java, Maven, Gradle y los IDE suelen integrarse con JUnit para lanzar las pruebas y presentar sus resultados.

---

## 5. Estructura de una prueba: Arrange, Act, Assert

Una práctica común es organizar cada prueba mediante el patrón **AAA: Arrange, Act, Assert**.

- **Arrange:** preparar los datos y las condiciones.
- **Act:** ejecutar la operación que se quiere comprobar.
- **Assert:** verificar el resultado esperado.

### Ejemplo

```java
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertEquals;

class CalculadoraTest {

    @Test
    void debeSumarDosNumeros() {
        // Arrange
        Calculadora calculadora = new Calculadora();

        // Act
        int resultado = calculadora.sumar(2, 3);

        // Assert
        assertEquals(5, resultado);
    }
}
```

La clase que se está probando podría ser:

```java
public class Calculadora {

    public int sumar(int a, int b) {
        return a + b;
    }
}
```

Esta prueba verifica una única regla sencilla: la suma de `2` y `3` debe producir `5`.

### ¿Por qué es importante AAA?

Facilita la lectura y permite entender rápidamente:

1. Qué condiciones se han preparado.
2. Qué comportamiento se está ejecutando.
3. Qué resultado se espera.

No es obligatorio utilizar comentarios en todos los casos. Cuando la estructura del test ya es evidente, los comentarios pueden ser innecesarios.

---

## 6. Anotaciones principales de JUnit 5

JUnit utiliza anotaciones para definir el comportamiento de las pruebas.

| Anotación | Función |
|---|---|
| `@Test` | Identifica un método de prueba |
| `@BeforeEach` | Ejecuta una preparación antes de cada prueba |
| `@AfterEach` | Ejecuta una acción después de cada prueba |
| `@BeforeAll` | Ejecuta una preparación común antes de todas las pruebas de la clase |
| `@AfterAll` | Ejecuta una acción común después de todas las pruebas de la clase |
| `@DisplayName` | Asigna un nombre descriptivo a una prueba o clase |
| `@Disabled` | Desactiva temporalmente una prueba o clase |
| `@Nested` | Agrupa pruebas relacionadas dentro de una clase anidada |
| `@Tag` | Clasifica pruebas mediante etiquetas |
| `@ParameterizedTest` | Ejecuta un mismo test con diferentes conjuntos de datos |
| `@RepeatedTest` | Repite una prueba un número determinado de veces |
| `@TestFactory` | Permite generar pruebas dinámicas |

### Ejemplo de preparación y limpieza

```java
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

class ServicioTest {

    @BeforeEach
    void preparar() {
        // Preparar los datos de cada prueba
    }

    @Test
    void debeEjecutarLaOperacion() {
        // Ejecutar y verificar el comportamiento
    }

    @AfterEach
    void limpiar() {
        // Liberar los recursos utilizados
    }
}
```

JUnit crea normalmente una nueva instancia de la clase de prueba para cada método de prueba, salvo que se configure otro ciclo de vida.

Por ello, `@BeforeEach` resulta útil para preparar datos independientes en cada ejecución.

### ¿Cuándo utilizar `@BeforeAll`?

Se utiliza para preparar recursos compartidos que deben existir antes de comenzar la ejecución de todas las pruebas de una clase.

En condiciones normales, el método anotado con `@BeforeAll` debe ser estático. Puede evitarse esa restricción utilizando un ciclo de vida `PER_CLASS`, aunque conviene comprender cómo afecta al estado compartido.

Las operaciones de preparación compartida no deberían introducir dependencias entre pruebas.

---

## 7. Aserciones: cómo comprobar resultados

Las aserciones son uno de los elementos más importantes de JUnit.

Permiten expresar de forma explícita qué comportamiento se considera correcto.

### 7.1. `assertEquals`

Comprueba que dos valores son iguales.

```java
assertEquals(10, resultado);
```

Puede incluir un mensaje explicativo:

```java
assertEquals(
    10,
    resultado,
    "El resultado debe ser diez"
);
```

### 7.2. `assertTrue` y `assertFalse`

Comprueban condiciones booleanas.

```java
assertTrue(usuario.estaActivo());
assertFalse(usuario.estaBloqueado());
```

### 7.3. `assertNull` y `assertNotNull`

Comprueban si un resultado es nulo o no.

```java
assertNotNull(usuario);
assertNull(resultadoOpcional);
```

### 7.4. `assertThrows`

Comprueba que una operación lanza una excepción del tipo esperado.

```java
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertThrows;

class UsuarioServiceTest {

    @Test
    void debeRechazarUnCorreoInvalido() {
        UsuarioService service = new UsuarioService();

        assertThrows(
            IllegalArgumentException.class,
            () -> service.registrar("correo-invalido")
        );
    }
}
```

El ejemplo presupone que `registrar` valida el formato del correo y lanza esa excepción cuando los datos son incorrectos.

Es importante que el bloque pasado a `assertThrows` contenga únicamente la operación que se espera que lance la excepción, para evitar que otro error haga pasar la prueba por accidente.

### 7.5. `assertAll`

Permite ejecutar varias aserciones relacionadas y recopilar los fallos producidos.

```java
import static org.junit.jupiter.api.Assertions.assertAll;
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertTrue;

assertAll(
    () -> assertEquals("Ana", usuario.getNombre()),
    () -> assertEquals("ana@example.com", usuario.getEmail()),
    () -> assertTrue(usuario.isActivo())
);
```

Es útil cuando se quiere verificar un conjunto de propiedades independientes de un mismo resultado.

### 7.6. `assertIterableEquals`

Comprueba que dos iterables contienen elementos iguales en el mismo orden.

```java
assertIterableEquals(
    List.of("A", "B", "C"),
    resultado
);
```

### 7.7. `assertTimeout`

Permite comprobar que una operación termina dentro de un tiempo determinado.

```java
import java.time.Duration;

import static org.junit.jupiter.api.Assertions.assertTimeout;

assertTimeout(
    Duration.ofSeconds(1),
    () -> servicio.ejecutar()
);
```

Esta aserción no sustituye a una prueba de rendimiento. Sirve para comprobar una restricción temporal concreta en un escenario de prueba, pero los resultados pueden depender del entorno de ejecución.

---

## 8. Pruebas parametrizadas

Las pruebas parametrizadas permiten ejecutar el mismo escenario con diferentes entradas sin duplicar el método de prueba.

Son especialmente útiles para verificar reglas de negocio y casos límite.

### Ejemplo

Supongamos que tenemos una función que determina si una persona es mayor de edad.

```java
public class ValidadorEdad {

    public boolean esMayorDeEdad(int edad) {
        return edad >= 18;
    }
}
```

Podemos probar varios valores con `@ParameterizedTest` y `@CsvSource`.

```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;

import static org.junit.jupiter.api.Assertions.assertEquals;

class ValidadorEdadTest {

    private final ValidadorEdad validador = new ValidadorEdad();

    @ParameterizedTest
    @CsvSource({
        "17, false",
        "18, true",
        "19, true",
        "0, false"
    })
    void debeDeterminarLaMayoriaDeEdad(
        int edad,
        boolean esperado
    ) {
        assertEquals(
            esperado,
            validador.esMayorDeEdad(edad)
        );
    }
}
```

JUnit ejecuta el test para cada fila de datos.

### Ventajas

- Menos duplicación.
- Mayor claridad.
- Cobertura de varios casos.
- Facilidad para añadir escenarios límite.

Para utilizar `@ParameterizedTest` es necesario disponer de la dependencia `junit-jupiter-params`, que normalmente se incorpora mediante `junit-jupiter`.

---

## 9. Pruebas de excepciones y casos límite

Las pruebas no deben limitarse a los escenarios en los que todo funciona correctamente.

También deben verificar cómo responde el sistema ante entradas inválidas, condiciones excepcionales y situaciones límite.

Ejemplos:

- Una cantidad negativa.
- Un identificador inexistente.
- Un usuario sin permisos.
- Una operación duplicada.
- Una dependencia que falla.
- Un recurso que no existe.

### Ejemplo de validación

```java
public class Calculadora {

    public int dividir(int dividendo, int divisor) {
        if (divisor == 0) {
            throw new IllegalArgumentException(
                "El divisor no puede ser cero"
            );
        }

        return dividendo / divisor;
    }
}
```

Prueba:

```java
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertThrows;

class CalculadoraTest {

    @Test
    void debeLanzarExcepcionCuandoElDivisorEsCero() {
        Calculadora calculadora = new Calculadora();

        assertThrows(
            IllegalArgumentException.class,
            () -> calculadora.dividir(10, 0)
        );
    }
}
```

La prueba documenta que dividir entre cero no se acepta en esta implementación y verifica que se produce el tipo de excepción esperado.

---

## 10. JUnit y Mockito

JUnit permite ejecutar las pruebas y comprobar resultados. Mockito facilita la creación de dobles de prueba, como mocks, para simular dependencias.

Ambas herramientas se utilizan frecuentemente juntas en pruebas unitarias.

### ¿Qué problema resuelve Mockito?

Supongamos que un servicio depende de un repositorio de base de datos.

En una prueba unitaria, no siempre es necesario acceder a una base de datos real. Podemos simular el comportamiento del repositorio y verificar la lógica del servicio de forma aislada.

### Ejemplo de producción

```java
public interface UsuarioRepository {
    Optional<Usuario> findById(Long id);
}
```

```java
public class UsuarioService {

    private final UsuarioRepository repository;

    public UsuarioService(UsuarioRepository repository) {
        this.repository = repository;
    }

    public Usuario obtenerUsuario(Long id) {
        return repository.findById(id)
            .orElseThrow(
                () -> new IllegalArgumentException(
                    "Usuario no encontrado"
                )
            );
    }
}
```

### Ejemplo de prueba con Mockito

```java
import org.junit.jupiter.api.Test;

import java.util.Optional;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertThrows;
import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.verify;
import static org.mockito.Mockito.when;

class UsuarioServiceTest {

    @Test
    void debeObtenerElUsuarioPorId() {
        UsuarioRepository repository =
            mock(UsuarioRepository.class);

        Usuario usuario = new Usuario(1L, "Ana");

        when(repository.findById(1L))
            .thenReturn(Optional.of(usuario));

        UsuarioService service = new UsuarioService(repository);

        Usuario resultado = service.obtenerUsuario(1L);

        assertEquals("Ana", resultado.getNombre());
        verify(repository).findById(1L);
    }

    @Test
    void debeLanzarExcepcionSiNoExisteElUsuario() {
        UsuarioRepository repository =
            mock(UsuarioRepository.class);

        when(repository.findById(99L))
            .thenReturn(Optional.empty());

        UsuarioService service = new UsuarioService(repository);

        assertThrows(
            IllegalArgumentException.class,
            () -> service.obtenerUsuario(99L)
        );
    }
}
```

El ejemplo es ilustrativo y presupone que la clase `Usuario` dispone del constructor y los métodos mostrados.

### ¿Qué se está comprobando?

- Que el servicio devuelve el usuario encontrado.
- Que el servicio gestiona el caso en el que el usuario no existe.
- Que la consulta se realiza con el identificador esperado.

### Buenas prácticas con Mockito

- Simular dependencias externas, no el objeto que se está probando.
- No crear mocks innecesarios.
- No verificar cada llamada interna sin una razón funcional.
- Evitar que el test reproduzca exactamente la implementación.
- Utilizar pruebas de integración para comprobar el comportamiento real del repositorio.

Un exceso de mocks puede generar pruebas que pasan aunque la aplicación falle al conectar los componentes reales.

---

## 11. JUnit en aplicaciones Spring Boot

En aplicaciones Spring Boot se utiliza JUnit para probar tanto la lógica aislada como los componentes integrados con el framework.

La estrategia depende de qué se quiera verificar.

### 11.1. Pruebas unitarias de servicios

Se instancia el servicio directamente y se simulan sus dependencias cuando es necesario.

Ventajas:

- Ejecución rápida.
- Poca infraestructura.
- Diagnóstico sencillo.

### 11.2. Pruebas de integración del contexto

La anotación `@SpringBootTest` permite cargar el contexto de Spring Boot para comprobar cómo colaboran los componentes configurados.

Estas pruebas son más costosas porque pueden inicializar una parte importante de la aplicación.

```java
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class AplicacionIntegrationTest {

    @Test
    void debeIniciarElContexto() {
        // Si la prueba llega a ejecutarse, el contexto se ha cargado.
    }
}
```

Esta prueba verifica principalmente que el contexto puede inicializarse. No demuestra por sí sola que los flujos de negocio funcionen correctamente.

### 11.3. Pruebas de la capa web

`@WebMvcTest` permite probar determinados componentes de Spring MVC sin levantar toda la aplicación.

Es útil para validar controladores y respuestas HTTP, normalmente aislando las dependencias de negocio.

### 11.4. Pruebas de persistencia

`@DataJpaTest` permite comprobar componentes relacionados con JPA y repositorios mediante una configuración de prueba específica.

Según la configuración, puede utilizar una base de datos embebida. Si se necesita validar el comportamiento frente a una base de datos real del mismo tipo que producción, puede utilizarse una solución como Testcontainers.

### Recomendación

No utilices `@SpringBootTest` para todas las pruebas por defecto. Reserva la carga completa del contexto para los escenarios que realmente necesitan comprobar la integración de los componentes de Spring.

Documentación oficial: https://docs.spring.io/spring-boot/reference/testing/

---

## 12. JUnit y los diferentes niveles de testing

JUnit puede utilizarse como motor de ejecución para pruebas de distintos niveles. No determina por sí solo si una prueba es unitaria, de integración o funcional: eso depende de su alcance y de las dependencias que utiliza.

| Nivel | Ejemplo | Dependencias |
|---|---|---|
| Unitaria | Calcular el precio con descuento | Objetos aislados |
| Integración | Verificar una consulta de repositorio | Base de datos de prueba |
| Funcional | Verificar el resultado de una operación de negocio | Componentes necesarios para el requisito |
| E2E | Completar una operación desde la interfaz | Sistema completo o recorrido representativo |
| Rendimiento | Medir la respuesta bajo carga | Entorno controlado y herramienta de carga |

### Ejemplo de estrategia

Para una aplicación de pedidos:

- Las reglas de cálculo se verifican mediante pruebas unitarias.
- La persistencia se verifica mediante pruebas de integración.
- El flujo completo de creación de pedidos se verifica con pruebas funcionales o E2E.
- La capacidad para procesar muchas solicitudes se evalúa mediante pruebas de rendimiento.

No es necesario utilizar JUnit para todas las herramientas de testing. Por ejemplo, Playwright y k6 tienen sus propios mecanismos de ejecución y se pueden orquestar desde el mismo pipeline.

---

## 13. JUnit y Clean Code

Las pruebas son código y deben seguir principios de legibilidad y mantenibilidad.

Una prueba difícil de entender puede ser casi tan problemática como un código de producción difícil de mantener.

### Principios recomendados

**Una prueba debe tener un objetivo claro.**

Evita comprobar muchos comportamientos no relacionados en un único método.

**Los nombres deben describir el comportamiento esperado.**

Por ejemplo:

```java
debeRechazarUnDescuentoNegativo()
```

es más informativo que:

```java
test3()
```

**Evita duplicar la lógica de producción en el test.**

Si el test reproduce exactamente el mismo algoritmo, puede repetir también el mismo error.

**Prepara datos relevantes.**

Utiliza valores que permitan comprender el escenario sin tener que analizar una gran cantidad de configuración.

**Evita dependencias entre pruebas.**

Cada prueba debe preparar el estado que necesita y no depender de que otra haya sido ejecutada previamente.

**Utiliza aserciones significativas.**

Comprueba el resultado importante para el comportamiento, no únicamente que la ejecución haya terminado sin lanzar una excepción.

---

## 14. Relación con SOLID, DRY, KISS y YAGNI

### SOLID

- **SRP:** las clases con responsabilidades claras son más fáciles de probar.
- **OCP:** las extensiones del comportamiento pueden validarse sin reescribir todas las pruebas existentes.
- **LSP:** las implementaciones intercambiables deben respetar los contratos que las pruebas verifican.
- **ISP:** las interfaces pequeñas reducen dependencias innecesarias.
- **DIP:** depender de abstracciones facilita sustituir dependencias en pruebas unitarias.

### DRY

Evita duplicar innecesariamente la preparación de datos y las utilidades de prueba.

Sin embargo, no conviene abstraer todos los tests en métodos genéricos que oculten qué comportamiento se está verificando.

### KISS

Las pruebas deben ser simples, explícitas y fáciles de diagnosticar.

### YAGNI

Evita crear infraestructura de testing excesiva antes de que exista una necesidad real.

**Importante:** aplicar estos principios no garantiza automáticamente una buena suite de pruebas. También es necesario seleccionar escenarios adecuados y comprobar resultados significativos.

---

## 15. JUnit y cobertura de código con JaCoCo

JaCoCo es una herramienta que mide la cobertura de código en proyectos Java.

Puede informar sobre líneas, instrucciones y ramas ejecutadas durante las pruebas.

JUnit ejecuta los tests y JaCoCo mide qué partes del código se han recorrido durante esa ejecución.

### Flujo

1. Ejecutar las pruebas JUnit.
2. Recoger datos de ejecución.
3. Generar el informe de JaCoCo.
4. Analizar las partes del código que no se han cubierto.
5. Añadir pruebas para comportamientos relevantes que no estén verificados.

### Ejemplo de Maven

La configuración de JaCoCo puede incluir el agente de cobertura y el objetivo de generación de informes. Después, se ejecuta:

```bash
mvn clean verify
```

Si el proyecto está configurado correctamente, el informe HTML suele generarse bajo:

```text
target/site/jacoco/index.html
```

La ruta exacta depende de la configuración de Maven.

### ¿Qué porcentaje de cobertura es adecuado?

No existe un porcentaje universal que garantice calidad.

Una cobertura elevada puede ser útil, pero no garantiza que las aserciones sean correctas ni que se hayan comprobado todos los comportamientos relevantes.

Conviene priorizar:

- Reglas de negocio críticas.
- Casos límite.
- Manejo de errores.
- Ramas condicionales relevantes.
- Comportamientos que podrían provocar daños si fallan.

La cobertura es una métrica de apoyo, no un sustituto del diseño de pruebas.

Documentación oficial: https://www.jacoco.org/jacoco/trunk/doc/

---

## 16. Ejecución de JUnit con Maven

En un proyecto Maven, el plugin Maven Surefire ejecuta habitualmente las pruebas unitarias durante la fase `test`.

### Comandos habituales

Ejecutar las pruebas:

```bash
mvn test
```

Limpiar y ejecutar las fases de verificación:

```bash
mvn clean verify
```

Ejecutar una clase de prueba concreta:

```bash
mvn -Dtest=CalculadoraTest test
```

Ejecutar una prueba concreta dentro de una clase, si el proveedor de tests configurado lo admite:

```bash
mvn -Dtest=CalculadoraTest#debeSumarDosNumeros test
```

### Dependencias

Un proyecto que utiliza JUnit Jupiter puede declarar la dependencia `junit-jupiter` con alcance de pruebas en el `pom.xml`.

Ejemplo ilustrativo:

```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>${junit.version}</version>
    <scope>test</scope>
</dependency>
```

La propiedad `junit.version` debe definirse en el proyecto con una versión compatible. En aplicaciones Spring Boot, normalmente conviene aprovechar la gestión de dependencias proporcionada por el parent o el BOM de Spring Boot, evitando fijar versiones incompatibles de forma independiente.

Las versiones actuales de Maven Surefire también deben ser compatibles con JUnit y con la versión de Java utilizada.

---

## 17. Integración con Jenkins y CI/CD

JUnit se integra habitualmente con Jenkins para ejecutar pruebas automáticamente y publicar los resultados.

### Flujo

1. Jenkins obtiene el código.
2. Ejecuta `mvn clean verify` o los comandos definidos.
3. Maven compila el proyecto y ejecuta las pruebas.
4. JUnit produce los resultados de las pruebas.
5. Maven Surefire genera informes XML.
6. Jenkins publica los informes mediante su paso `junit`.
7. El pipeline marca el resultado según el estado de la ejecución.

### Ejemplo de Jenkinsfile

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Pruebas') {
            steps {
                sh 'mvn -B clean verify'
            }
        }
    }

    post {
        always {
            junit(
                testResults: '**/target/surefire-reports/TEST-*.xml',
                allowEmptyResults: false
            )
        }
    }
}
```

Este ejemplo presupone un agente Linux con Java y Maven configurados.

En proyectos con varios módulos o pruebas de integración ejecutadas mediante otros plugins, puede ser necesario recoger también los informes de Failsafe u otras ubicaciones.

### Beneficios

- Ejecución consistente de las pruebas.
- Resultados visibles para el equipo.
- Detección temprana de regresiones.
- Historial de fallos.
- Posibilidad de bloquear la integración o el despliegue.

Jenkins no convierte automáticamente todas las pruebas en un criterio obligatorio de aceptación. Es necesario configurar correctamente el pipeline y las políticas de integración del repositorio.

---

## 18. Errores frecuentes al utilizar JUnit

### Escribir pruebas que solo verifican que no hay excepciones

Una prueba puede pasar sin verificar que el resultado sea correcto.

**Solución:** añadir aserciones relacionadas con el comportamiento esperado.

### Probar únicamente el camino correcto

Los errores suelen aparecer en valores límite, entradas inválidas o dependencias que fallan.

**Solución:** incluir escenarios negativos y casos límite relevantes.

### Crear tests dependientes entre sí

El resultado de una prueba depende de datos creados por otra.

**Solución:** preparar y limpiar los datos de cada prueba de manera independiente.

### Utilizar demasiados mocks

Las pruebas pueden verificar una implementación simulada sin comprobar las interacciones reales.

**Solución:** reservar los mocks para aislar dependencias y utilizar pruebas de integración cuando sea necesario.

### Comprobar detalles internos

Un test puede fallar por una refactorización que no cambia el comportamiento observable.

**Solución:** comprobar contratos y resultados relevantes, no cada llamada interna sin necesidad.

### Buscar cobertura elevada a cualquier precio

Se pueden crear tests con muchas líneas ejecutadas pero pocas aserciones significativas.

**Solución:** priorizar la calidad de los escenarios y las condiciones verificadas.

### Ignorar pruebas fallidas

Si el pipeline permite continuar aunque fallen pruebas críticas, se pierde buena parte del valor de la automatización.

**Solución:** definir reglas claras sobre qué fallos impiden integrar o desplegar.

---

## 19. Estrategia recomendada para implantar JUnit

### Fase 1. Identificar la lógica importante

Seleccionar reglas de negocio y comportamientos que puedan producir defectos relevantes.

### Fase 2. Crear pruebas unitarias sencillas

Comenzar con métodos o servicios aislados.

### Fase 3. Añadir casos límite

Incluir entradas inválidas, límites y escenarios excepcionales.

### Fase 4. Introducir Mockito cuando sea necesario

Aislar dependencias externas sin simular innecesariamente el comportamiento que se quiere comprobar.

### Fase 5. Añadir pruebas de integración

Comprobar el comportamiento real de repositorios, configuración y comunicación entre componentes.

### Fase 6. Medir la cobertura

Utilizar JaCoCo para identificar zonas no cubiertas y priorizar las que tengan mayor riesgo.

### Fase 7. Integrar los tests en CI/CD

Ejecutar las pruebas en Jenkins o en la plataforma de integración elegida.

### Fase 8. Revisar y mantener

Eliminar tests redundantes, mejorar nombres, corregir pruebas inestables y adaptar los escenarios cuando cambien los requisitos.

---

## 20. Checklist de calidad para pruebas JUnit

- Cada prueba tiene un objetivo claro.
- Los nombres describen el comportamiento esperado.
- Las pruebas verifican resultados significativos.
- Se cubren escenarios positivos y negativos.
- Se contemplan los casos límite relevantes.
- Los datos de prueba son comprensibles.
- Las pruebas no dependen del orden de ejecución.
- Los recursos se preparan y limpian correctamente.
- Los mocks se utilizan con criterio.
- Las pruebas unitarias no necesitan infraestructura innecesaria.
- Las integraciones importantes se verifican con pruebas específicas.
- La cobertura se interpreta junto con las aserciones.
- Las pruebas se ejecutan automáticamente en CI/CD.
- Los resultados quedan registrados.
- Los fallos críticos impiden avanzar cuando corresponde.

---

## 21. Conclusión

JUnit es una herramienta esencial para automatizar pruebas en Java y constituye una base sólida para una estrategia de calidad del software.

Permite definir el comportamiento esperado mediante pruebas ejecutables, detectar regresiones y facilitar refactorizaciones seguras.

Su valor aumenta cuando se combina con herramientas complementarias:

- **JUnit:** define y ejecuta pruebas automatizadas.
- **Mockito:** simula dependencias para aislar componentes.
- **Spring Boot Test:** facilita probar componentes y contextos de Spring.
- **Testcontainers:** permite probar integraciones con dependencias reales en contenedores.
- **JaCoCo:** mide la cobertura de código.
- **SonarQube:** analiza la calidad estática y puede aplicar criterios de aceptación.
- **Jenkins:** automatiza la ejecución y publicación de resultados en CI/CD.

La recomendación es empezar con pruebas unitarias pequeñas, legibles y centradas en reglas de negocio. Después, añadir pruebas de integración y conectar la suite a un pipeline automatizado.

**Una buena prueba JUnit no se limita a ejecutar código: comprueba de forma clara, repetible y significativa que el comportamiento esperado se mantiene.**

---

## 22. Referencias oficiales

- JUnit: https://junit.org/junit5/
- Guía de usuario de JUnit Jupiter: https://junit.org/junit5/docs/current/user-guide/
- Mockito: https://site.mockito.org/
- Spring Boot Testing: https://docs.spring.io/spring-boot/reference/testing/
- JaCoCo: https://www.jacoco.org/jacoco/trunk/doc/
- Maven Surefire: https://maven.apache.org/surefire/maven-surefire-plugin/
- Jenkins Pipeline: https://www.jenkins.io/doc/book/pipeline/
- SonarQube: https://docs.sonarsource.com/sonarqube-server/
