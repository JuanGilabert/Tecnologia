# Selenium: testing y calidad del código

## 1. Introducción

**Selenium** es un conjunto de herramientas de código abierto para automatizar navegadores web. Su componente principal, Selenium WebDriver, permite interactuar con una aplicación web de forma similar a como lo haría una persona.

Selenium puede utilizarse para automatizar operaciones como:

- Abrir páginas y navegar entre ellas.
- Introducir datos en formularios.
- Pulsar botones y enlaces.
- Comprobar mensajes y estados de la interfaz.
- Validar procesos de autenticación y autorización.
- Ejecutar pruebas en diferentes navegadores.
- Automatizar pruebas de regresión.
- Ejecutar pruebas en máquinas remotas mediante Selenium Grid.

Selenium no es, por sí solo, un framework de pruebas completo con todas las convenciones de organización y ejecución. Normalmente se combina con herramientas como JUnit o TestNG en Java, o Pytest en Python.

### Objetivos principales

- Automatizar pruebas funcionales y E2E.
- Detectar regresiones en aplicaciones web.
- Reducir las comprobaciones manuales repetitivas.
- Validar flujos críticos de negocio.
- Comprobar la compatibilidad entre navegadores.
- Integrar las pruebas en pipelines CI/CD.
- Facilitar la detección y el diagnóstico de defectos.

Documentación oficial: https://www.selenium.dev/documentation/

---

## 2. ¿Qué problema resuelve Selenium?

Una aplicación puede superar las pruebas unitarias y seguir presentando errores en la interfaz o en la interacción entre sus componentes.

Por ejemplo:

- Un botón deja de responder después de una actualización.
- Un formulario permite enviar información inválida.
- El proceso de autenticación falla.
- Una tabla no muestra los datos esperados.
- Un menú funciona en un navegador, pero no en otro.
- Un cambio de CSS rompe un flujo funcional.
- Una operación se completa en el backend, pero la interfaz no refleja el resultado.

Selenium permite automatizar estos escenarios mediante scripts que controlan el navegador y verifican los resultados observables.

### Beneficios

1. **Repetibilidad:** ejecutar las mismas comprobaciones en cada cambio.
2. **Detección temprana:** descubrir defectos antes del despliegue.
3. **Compatibilidad:** comprobar distintos navegadores y entornos.
4. **Integración:** ejecutar pruebas automáticamente desde CI/CD.
5. **Trazabilidad:** registrar qué escenarios pasan y cuáles fallan.
6. **Escalabilidad:** distribuir pruebas mediante Selenium Grid.

### ¿Cuándo utilizarlo?

Es especialmente útil cuando se necesita comprobar aplicaciones web en navegadores reales, automatizar procesos complejos o mantener una suite de regresión funcional.

---

## 3. Componentes principales del ecosistema Selenium

| Componente | Responsabilidad |
|---|---|
| Selenium WebDriver | Controlar el navegador mediante una API |
| Selenium Manager | Facilitar la gestión de drivers y navegadores compatibles |
| Selenium Grid | Distribuir pruebas entre máquinas, navegadores y entornos |
| JUnit / TestNG | Organizar y ejecutar tests en Java |
| Pytest | Organizar y ejecutar tests en Python |
| Jenkins | Automatizar la ejecución de pruebas en CI/CD |
| SonarQube | Analizar la calidad del código según la configuración del proyecto |

### Selenium WebDriver

Es el componente central. Permite localizar elementos, ejecutar interacciones y consultar el estado de la página.

### Selenium Manager

En versiones modernas de Selenium, Selenium Manager facilita la gestión de los drivers necesarios. En muchos entornos evita tener que descargar y configurar manualmente un driver, aunque pueden existir requisitos adicionales según el sistema y la configuración.

### Selenium Grid

Permite ejecutar pruebas en máquinas remotas y distribuir la carga entre diferentes navegadores y plataformas.

Documentación:

- WebDriver: https://www.selenium.dev/documentation/webdriver/
- Selenium Grid: https://www.selenium.dev/documentation/grid/

---

## 4. Instalación y configuración con Java

Para los ejemplos utilizaremos **Java, Maven, Selenium 4 y JUnit 5**.

Esta combinación es habitual en proyectos empresariales y permite integrar Selenium con herramientas de análisis estático, cobertura y CI/CD.

Se necesita un JDK compatible con las versiones seleccionadas de las dependencias.

### Dependencias de Maven

En el archivo `pom.xml`:

```xml
<dependencies>
    <dependency>
        <groupId>org.seleniumhq.selenium</groupId>
        <artifactId>selenium-java</artifactId>
        <version>${selenium.version}</version>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>${junit.version}</version>
        <scope>test</scope>
    </dependency>
</dependencies>

<properties>
    <maven.compiler.release>17</maven.compiler.release>
    <selenium.version>VERSION_VALIDADA</selenium.version>
    <junit.version>VERSION_VALIDADA</junit.version>
</properties>
```

`VERSION_VALIDADA` es un marcador que debes sustituir por versiones concretas, compatibles y aprobadas para tu proyecto. No debe permanecer como texto literal en el `pom.xml` definitivo.

En un proyecto real, conviene fijar las versiones de Selenium, JUnit, Maven y el JDK para que las ejecuciones sean reproducibles.

### Estructura típica de Maven

```text
mi-proyecto/
├── src/
│   └── test/
│       └── java/
│           ├── tests/
│           │   └── LoginTest.java
│           ├── pages/
│           │   └── LoginPage.java
│           └── support/
│               └── WebDriverFactory.java
├── pom.xml
└── Jenkinsfile
```

- `tests/`: escenarios de prueba.
- `pages/`: objetos de página reutilizables.
- `support/`: creación y configuración del navegador.
- `pom.xml`: dependencias y configuración de Maven.

---

## 5. Primera prueba con Selenium y JUnit 5

Ejemplo de una prueba sencilla:

```java
package tests;

import org.junit.jupiter.api.Test;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

import static org.junit.jupiter.api.Assertions.assertTrue;

class HomePageTest {

    @Test
    void shouldDisplayExpectedTitle() {
        WebDriver driver = new ChromeDriver();

        try {
            driver.get("https://www.selenium.dev/");

            assertTrue(
                driver.getTitle().contains("Selenium"),
                "El título debería contener Selenium"
            );
        } finally {
            driver.quit();
        }
    }
}
```

### ¿Qué ocurre?

1. JUnit identifica el método anotado con `@Test`.
2. Selenium crea una sesión de navegador.
3. `driver.get()` abre la página.
4. JUnit comprueba el título.
5. `driver.quit()` cierra la sesión incluso si falla la aserción.

En versiones modernas, Selenium Manager puede encargarse de resolver el driver compatible automáticamente.

### Ejecutar la prueba

```bash
mvn test
```

Esta prueba es ilustrativa. Para una suite de producción, conviene centralizar la creación del navegador, configurar tiempos de espera y evitar depender de servicios externos innecesariamente.

---

## 6. Ciclo de vida del navegador

Cada prueba debe gestionar correctamente los recursos que utiliza.

En JUnit 5 se pueden utilizar `@BeforeEach` y `@AfterEach` para preparar y liberar el navegador.

```java
package tests;

import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

abstract class BaseUiTest {

    protected WebDriver driver;

    @BeforeEach
    void setUp() {
        driver = new ChromeDriver();
    }

    @AfterEach
    void tearDown() {
        if (driver != null) {
            driver.quit();
        }
    }
}
```

Una prueba que herede de `BaseUiTest` puede reutilizar esta configuración.

### Buenas prácticas

- Crear una sesión de navegador por prueba, salvo que exista una razón clara para hacerlo de otra forma.
- Cerrar el navegador en todos los escenarios.
- Evitar compartir estado mutable entre tests.
- Configurar la ejecución sin interfaz gráfica cuando el entorno de CI lo requiera.
- No reutilizar sesiones de forma indiscriminada para ahorrar tiempo.

En ejecución paralela, cada prueba debe disponer de su propia sesión y de datos de prueba aislados. También es necesario configurar correctamente el modelo de concurrencia de JUnit y los recursos compartidos.

---

## 7. Localizadores de elementos

Selenium utiliza localizadores para encontrar elementos del DOM.

Los más habituales son:

- `By.id()`
- `By.name()`
- `By.cssSelector()`
- `By.xpath()`
- `By.className()`
- `By.linkText()`

### Ejemplo

```java
driver.findElement(By.id("email")).sendKeys("qa@example.com");

driver.findElement(By.cssSelector("[data-testid='submit-login']")).click();
```

### ¿Qué localizador conviene utilizar?

En general, se recomienda priorizar identificadores estables y específicos.

Orden orientativo:

1. `id` estable.
2. Atributos semánticos o de prueba como `data-testid`.
3. Selectores CSS sencillos y específicos.
4. XPath cuando la estructura o relación entre elementos lo justifique.

### Evitar selectores frágiles

Un selector como:

```java
By.xpath("/html/body/div[2]/main/div[1]/form/button")
```

depende demasiado de la estructura del documento.

Es preferible:

```java
By.cssSelector("[data-testid='submit-login']")
```

Los atributos de prueba deben definirse de manera consistente con el equipo de desarrollo.

---

## 8. Esperas explícitas y sincronización

Uno de los problemas más frecuentes en Selenium es intentar interactuar con elementos antes de que estén listos.

Una página puede haber terminado de cargar su HTML y seguir ejecutando JavaScript que actualiza el contenido.

### Evitar las pausas fijas

No es recomendable utilizar `Thread.sleep()` como solución habitual:

```java
Thread.sleep(5000);
```

Esta instrucción espera siempre cinco segundos, incluso si el elemento aparece antes. Si aparece después, la prueba puede seguir fallando.

### Utilizar WebDriverWait

```java
import java.time.Duration;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;

WebDriverWait wait = new WebDriverWait(
    driver,
    Duration.ofSeconds(10)
);

WebElement boton = wait.until(
    ExpectedConditions.elementToBeClickable(
        By.id("submit")
    )
);

boton.click();
```

La prueba espera hasta que se cumpla la condición o se alcance el límite de tiempo.

### Condiciones habituales

- Elemento visible.
- Elemento clicable.
- Texto presente.
- Elemento desaparecido.
- URL esperada.
- Presencia de un elemento en el DOM.

### Recomendación importante

Evitar mezclar esperas implícitas y explícitas, ya que pueden producir tiempos de espera difíciles de predecir. Para la mayoría de las suites, resulta más claro utilizar esperas explícitas ligadas a condiciones concretas.

Documentación oficial: https://www.selenium.dev/documentation/webdriver/waits/

---

## 9. Patrón Page Object Model (POM)

El **Page Object Model** consiste en encapsular las interacciones y los localizadores de una página en una clase.

Su objetivo es reducir la duplicación y separar el comportamiento de la interfaz de los escenarios de prueba.

### Ejemplo: LoginPage.java

```java
package pages;

import java.time.Duration;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;

public class LoginPage {

    private final WebDriver driver;
    private final WebDriverWait wait;

    private final By emailInput = By.id("email");
    private final By passwordInput = By.id("password");
    private final By loginButton = By.id("login");
    private final By successMessage = By.id("welcome-message");

    public LoginPage(WebDriver driver) {
        this.driver = driver;
        this.wait = new WebDriverWait(
            driver,
            Duration.ofSeconds(10)
        );
    }

    public void open(String baseUrl) {
        driver.get(baseUrl + "/login");
    }

    public void login(String email, String password) {
        wait.until(
            ExpectedConditions.visibilityOfElementLocated(emailInput)
        ).sendKeys(email);

        driver.findElement(passwordInput).sendKeys(password);

        wait.until(
            ExpectedConditions.elementToBeClickable(loginButton)
        ).click();
    }

    public String getSuccessMessage() {
        return wait.until(
            ExpectedConditions.visibilityOfElementLocated(successMessage)
        ).getText();
    }
}
```

### Ejemplo de prueba

```java
package tests;

import org.junit.jupiter.api.Test;
import pages.LoginPage;

import static org.junit.jupiter.api.Assertions.assertEquals;

class LoginTest extends BaseUiTest {

    @Test
    void validUserCanLogIn() {
        LoginPage loginPage = new LoginPage(driver);

        loginPage.open("http://localhost:8080");
        loginPage.login("qa@example.com", "test-password");

        assertEquals(
            "Bienvenido",
            loginPage.getSuccessMessage()
        );
    }
}
```

Los selectores, URL y mensajes son ilustrativos y deben adaptarse a la aplicación.

### Ventajas del POM

- Centraliza los localizadores.
- Facilita la reutilización de acciones.
- Reduce duplicación.
- Simplifica el mantenimiento cuando cambia la interfaz.
- Mejora la legibilidad de los tests.

### Riesgos

- Crear clases demasiado grandes.
- Mezclar reglas de negocio con detalles de presentación.
- Ocultar demasiada lógica dentro de métodos genéricos.
- Introducir abstracciones innecesarias en proyectos pequeños.

El patrón debe utilizarse cuando mejora realmente la mantenibilidad.

---

## 10. Diseño de pruebas con AAA

Una forma útil de estructurar los tests es el patrón **Arrange, Act, Assert**.

- **Arrange:** preparar el estado y los datos.
- **Act:** realizar la acción.
- **Assert:** verificar el resultado.

Ejemplo:

```java
@Test
void shouldShowValidationErrorForEmptyEmail() {
    LoginPage loginPage = new LoginPage(driver);

    // Arrange
    loginPage.open("http://localhost:8080");

    // Act
    loginPage.submitEmptyForm();

    // Assert
    assertEquals(
        "El correo es obligatorio",
        loginPage.getEmailError()
    );
}
```

El ejemplo presupone que se han implementado los métodos correspondientes en `LoginPage`.

### Buenas prácticas

- Comprobar un comportamiento principal por test.
- Utilizar nombres que describan el resultado esperado.
- Evitar aserciones irrelevantes.
- Mantener la preparación de datos simple.
- Separar la preparación compleja en métodos auxiliares cuando aporte claridad.

Una prueba debería fallar por una razón comprensible y facilitar el diagnóstico del defecto.

---

## 11. Pruebas funcionales, E2E e integración

Selenium es especialmente apropiado para automatizar pruebas funcionales que pasan por la interfaz del navegador.

| Tipo de prueba | Uso de Selenium |
|---|---|
| Unitaria | No suele ser la herramienta adecuada |
| Integración | Útil si la interacción requiere una interfaz real |
| Funcional | Muy apropiado para flujos web |
| Aceptación | Útil para comprobar criterios de negocio |
| E2E | Apropiado para flujos completos de usuario |
| Regresión | Muy útil para volver a ejecutar escenarios críticos |
| Compatibilidad | Permite ejecutar escenarios en diferentes navegadores |

### ¿Por qué no utilizar Selenium para todo?

Las pruebas de interfaz suelen ser más lentas y pueden ser más sensibles a los cambios visuales o de estructura.

Para una estrategia equilibrada:

- Utilizar pruebas unitarias para lógica aislada.
- Utilizar pruebas de integración para comprobar colaboraciones entre componentes.
- Utilizar pruebas de API para validar contratos.
- Utilizar Selenium para los flujos críticos que requieren interacción real con el navegador.

Esta distribución reduce el coste de mantenimiento y acelera la detección de defectos.

---

## 12. Ejecución en diferentes navegadores

Selenium permite trabajar con diferentes implementaciones de WebDriver.

Por ejemplo, para Chrome:

```java
WebDriver driver = new ChromeDriver();
```

Para Firefox:

```java
WebDriver driver = new FirefoxDriver();
```

Para Edge:

```java
WebDriver driver = new EdgeDriver();
```

Las clases y los drivers necesarios dependen de las dependencias instaladas y del entorno.

### Estrategia recomendada

No siempre es necesario ejecutar todos los tests en todos los navegadores en cada cambio.

Una estrategia práctica consiste en:

- Ejecutar la suite rápida en el navegador principal durante cada cambio.
- Ejecutar una matriz de navegadores para flujos críticos.
- Ampliar las pruebas de compatibilidad antes de las entregas importantes.
- Investigar las diferencias específicas de cada navegador.

La selección de navegadores debe responder a los requisitos reales de soporte de la aplicación.

---

## 13. Selenium Grid y ejecución distribuida

Selenium Grid permite ejecutar pruebas de WebDriver en máquinas remotas y distribuir la carga entre diferentes navegadores y sistemas operativos.

Es útil cuando:

- Hay muchas pruebas.
- Se necesita ejecutar una matriz de navegadores.
- El entorno de CI tiene varios agentes.
- Se requiere separar los clientes de prueba de las máquinas que ejecutan los navegadores.

### Beneficios

- Distribución de ejecuciones.
- Compatibilidad con diferentes entornos.
- Mayor capacidad de paralelización.
- Centralización de la infraestructura de navegadores.

### Precauciones

- Configurar correctamente la capacidad de los nodos.
- Evitar saturar los recursos.
- Aislar los datos de las pruebas.
- Gestionar la limpieza de sesiones.
- Controlar versiones de navegadores y drivers.
- Recoger logs y evidencias en caso de fallo.

Documentación oficial: https://www.selenium.dev/documentation/grid/

---

## 14. Calidad del código de pruebas

Los tests automatizados son código de software y también necesitan prácticas de calidad.

### Aspectos que deben revisarse

- Nombres descriptivos.
- Responsabilidades claras.
- Evitar duplicación.
- Uso correcto de esperas.
- Gestión del ciclo de vida del navegador.
- Localizadores robustos.
- Aserciones significativas.
- Independencia entre pruebas.
- Gestión segura de credenciales.
- Configuración reproducible.

### Aplicación de principios de programación

**Clean Code:** los nombres deben explicar la intención del escenario.

**DRY:** reutilizar código cuando exista una duplicación real, sin introducir abstracciones excesivas.

**KISS:** mantener cada prueba lo más simple posible.

**SOLID:** diseñar clases de apoyo y Page Objects con responsabilidades claras.

**YAGNI:** evitar crear un framework interno complejo antes de necesitarlo.

### Ejemplo de mala práctica

```java
@Test
void test1() {
    // Muchas interacciones y verificaciones sin una intención clara
}
```

### Ejemplo de mejor práctica

```java
@Test
void userCannotSubmitOrderWithoutRequiredAddress() {
    // Escenario enfocado en una regla concreta
}
```

Los nombres no garantizan calidad por sí solos, pero ayudan a comprender la finalidad de cada prueba.

---

## 15. Análisis estático con SonarQube

Selenium no analiza la calidad estática del código. Para ello se pueden utilizar herramientas como SonarQube, junto con las comprobaciones del lenguaje.

En proyectos Java es habitual combinar:

- Selenium WebDriver.
- JUnit 5.
- Maven.
- JaCoCo para cobertura de código Java.
- SonarQube para análisis de calidad.
- Jenkins para automatizar el proceso.

### ¿Qué puede aportar SonarQube?

Según los analizadores y la configuración del proyecto, puede ayudar a identificar:

- Problemas potenciales.
- Código duplicado.
- Complejidad excesiva.
- Problemas de mantenibilidad.
- Vulnerabilidades y riesgos de seguridad detectables por análisis estático.
- Incumplimientos de reglas de calidad.

### ¿Cómo se complementa con Selenium?

Selenium comprueba que el flujo de usuario funciona. SonarQube analiza aspectos internos del código.

Por ejemplo, una prueba E2E puede confirmar que un usuario inicia sesión, mientras que el análisis estático ayuda a detectar problemas de mantenibilidad en las clases de soporte o en el código de la aplicación.

### Cobertura de código

La cobertura de código no es lo mismo que la cobertura funcional.

JaCoCo puede generar informes de cobertura para código Java instrumentado. Si se quiere medir cobertura de código de producción ejecutado por pruebas E2E, es necesario configurar la instrumentación y la recogida de datos correspondientes.

No se debe interpretar la cobertura de las clases de prueba como una medida directa de la cobertura del código de negocio.

---

## 16. Pruebas inestables (flaky tests)

Una prueba inestable puede pasar en una ejecución y fallar en otra sin que haya cambiado el comportamiento que se intenta comprobar.

### Causas habituales

- Esperas insuficientes.
- Selectores frágiles.
- Datos compartidos.
- Dependencia del orden.
- Servicios externos.
- Animaciones y actualizaciones asíncronas.
- Saturación del navegador o de la infraestructura.
- Diferencias de configuración entre entornos.

### Cómo reducirlas

1. Utilizar esperas explícitas.
2. Evitar pausas fijas como solución general.
3. Crear datos aislados para cada prueba.
4. No depender del resultado de otra prueba.
5. Usar selectores estables.
6. Registrar logs y capturas cuando falle un escenario.
7. Ejecutar las pruebas en entornos reproducibles.
8. Analizar las causas de los reintentos.

Los reintentos pueden ayudar a diagnosticar problemas transitorios, pero no deben ocultar la inestabilidad.

Documentación oficial de esperas: https://www.selenium.dev/documentation/webdriver/waits/

---

## 17. Seguridad y datos de prueba

Las suites automatizadas pueden acceder a cuentas, tokens, información personal y servicios externos.

### Recomendaciones

- No guardar contraseñas reales en el repositorio.
- Utilizar un gestor de secretos o credenciales protegidas en CI/CD.
- Evitar imprimir tokens en los logs.
- Utilizar usuarios exclusivos para pruebas.
- Limitar los permisos de las cuentas automatizadas.
- Evitar ejecutar pruebas destructivas en producción.
- Aislar los datos de cada escenario.
- Controlar el acceso a capturas y registros.

Las evidencias de las pruebas pueden contener información sensible. Deben almacenarse y conservarse de acuerdo con las políticas de seguridad del proyecto.

---

## 18. Integración con Jenkins

Jenkins puede ejecutar las pruebas Selenium mediante Maven.

Un pipeline habitual incluye:

1. Obtener el código.
2. Preparar el JDK.
3. Descargar las dependencias.
4. Ejecutar comprobaciones estáticas.
5. Ejecutar las pruebas.
6. Publicar los resultados.
7. Archivar evidencias.
8. Evaluar los criterios de aceptación.

### Ejemplo de Jenkinsfile

```groovy
pipeline {
    agent any

    tools {
        jdk 'JDK17'
        maven 'Maven3'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Compilar y ejecutar pruebas') {
            steps {
                sh 'mvn clean verify'
            }
        }
    }

    post {
        always {
            junit(
                testResults: 'target/surefire-reports/*.xml',
                allowEmptyResults: true
            )

            archiveArtifacts(
                artifacts: 'target/screenshots/**,target/logs/**',
                allowEmptyArchive: true
            )
        }
    }
}
```

### Consideraciones importantes

- `JDK17` y `Maven3` son nombres ilustrativos que deben existir en la configuración de herramientas de Jenkins.
- El agente necesita un navegador compatible y las dependencias del sistema operativo.
- Si se utiliza ejecución headless, debe configurarse en las opciones del navegador.
- El proyecto debe tener configurado Maven Surefire para ejecutar JUnit 5.
- Los directorios de capturas y logs solo se publicarán si el proyecto los genera.
- Si las pruebas requieren una aplicación levantada, el pipeline debe iniciarla antes de ejecutar la suite.
- Si se necesita análisis SonarQube, hay que añadir un paso específico para ejecutarlo y configurar su Quality Gate.

### Ejemplo de opciones headless en Chrome

```java
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;

ChromeOptions options = new ChromeOptions();
options.addArguments("--headless");

WebDriver driver = new ChromeDriver(options);
```

Las opciones exactas deben adaptarse al navegador y al entorno de CI. En contenedores Linux también hay que comprobar que las dependencias del sistema están disponibles.

---

## 19. Métricas para evaluar la calidad

Para evaluar la suite Selenium no basta con contar los tests.

### Métricas útiles

- Porcentaje de pruebas satisfactorias.
- Número de fallos por versión.
- Frecuencia de pruebas inestables.
- Duración total de la suite.
- Duración por escenario.
- Tiempo medio de diagnóstico.
- Cobertura de requisitos críticos.
- Defectos detectados antes del despliegue.
- Defectos encontrados después del despliegue.
- Coste de mantenimiento de la automatización.

### Precaución

Una suite con cientos de pruebas no es necesariamente mejor que otra más pequeña. La calidad depende de que los escenarios sean relevantes, detecten defectos y puedan mantenerse.

Un 100 % de éxito solo significa que las pruebas ejecutadas han pasado; no demuestra que todos los comportamientos de la aplicación estén cubiertos.

---

## 20. Errores frecuentes

### Utilizar Selenium para pruebas unitarias

**Problema:** las pruebas se vuelven lentas y dependen innecesariamente del navegador.

**Solución:** utilizar JUnit o herramientas unitarias para la lógica aislada y reservar Selenium para interacciones web.

### Utilizar pausas fijas

**Problema:** las pruebas se vuelven lentas y continúan siendo inestables.

**Solución:** utilizar esperas explícitas basadas en condiciones.

### Crear selectores frágiles

**Problema:** cambios menores del HTML rompen las pruebas.

**Solución:** utilizar identificadores estables y selectores específicos.

### Mezclar lógica de negocio y automatización

**Problema:** las pruebas resultan difíciles de mantener.

**Solución:** separar los escenarios de las interacciones mediante Page Objects o abstracciones sencillas cuando sea apropiado.

### Compartir el navegador entre pruebas paralelas

**Problema:** aparecen interferencias y resultados impredecibles.

**Solución:** aislar sesiones y datos.

### Ignorar los informes de fallos

**Problema:** se invierte demasiado tiempo en reproducir los errores.

**Solución:** guardar logs, capturas y suficiente información contextual.

### Automatizar todos los escenarios con E2E

**Problema:** la suite se vuelve lenta y costosa.

**Solución:** combinar pruebas unitarias, integración, API y E2E.

---

## 21. Estrategia recomendada de implantación

### Fase 1. Identificar los flujos críticos

Elegir procesos con un impacto importante en el negocio, como autenticación, creación de pedidos o gestión de usuarios.

### Fase 2. Crear una base de pruebas

Configurar Selenium, JUnit y Maven con un ejemplo sencillo y reproducible.

### Fase 3. Definir convenciones

Establecer nombres, localizadores, esperas y gestión de recursos.

### Fase 4. Introducir Page Objects cuando aporten valor

Evitar duplicaciones sin crear capas de abstracción innecesarias.

### Fase 5. Incorporar CI/CD

Ejecutar automáticamente las pruebas y publicar los resultados en Jenkins.

### Fase 6. Añadir análisis estático

Integrar SonarQube y las comprobaciones de calidad del lenguaje.

### Fase 7. Mejorar estabilidad y rendimiento

Identificar las pruebas inestables, aislar datos y revisar la concurrencia.

### Fase 8. Ampliar la compatibilidad

Ejecutar las pruebas en los navegadores requeridos y utilizar Selenium Grid si la escala lo justifica.

---

## 22. Checklist de calidad

- Los tests tienen nombres descriptivos.
- Cada prueba verifica un comportamiento concreto.
- Los localizadores son estables.
- Se utilizan esperas explícitas.
- No se abusa de `Thread.sleep()`.
- El navegador se cierra correctamente.
- Las pruebas son independientes.
- Los datos de prueba están aislados.
- Las sesiones se aíslan durante la ejecución paralela.
- Los Page Objects tienen responsabilidades claras.
- Las credenciales se gestionan de forma segura.
- Los fallos generan evidencias útiles.
- Las pruebas se ejecutan automáticamente en CI/CD.
- Se publican los resultados de JUnit.
- El análisis estático está integrado en el pipeline.
- La cobertura de código se interpreta correctamente.
- Las pruebas inestables se investigan.
- Los navegadores y drivers se gestionan de forma reproducible.
- La duración de la suite es compatible con el flujo de desarrollo.
- Los escenarios automatizados cubren requisitos relevantes.

---

## 23. Conclusión

Selenium es una herramienta madura y flexible para automatizar pruebas web. Combinado con JUnit y Maven, permite crear suites de pruebas funcionales mantenibles e integrarlas en pipelines CI/CD.

Para una estrategia de calidad completa, conviene combinar:

- **Selenium WebDriver:** automatización de navegadores.
- **JUnit 5:** organización y ejecución de tests Java.
- **Maven:** gestión de dependencias y ciclo de construcción.
- **Selenium Grid:** ejecución distribuida cuando sea necesaria.
- **JaCoCo:** cobertura de código Java instrumentado.
- **SonarQube:** análisis estático y seguimiento de calidad.
- **Jenkins:** ejecución automatizada y publicación de resultados.

La recomendación es comenzar por los flujos críticos, utilizar esperas explícitas, mantener los tests independientes y automatizar su ejecución dentro del pipeline.

**La calidad de una suite Selenium se mide por su capacidad para detectar defectos reales, producir resultados reproducibles y facilitar el mantenimiento de las pruebas a medida que evoluciona la aplicación.**

---

## 24. Referencias oficiales

- Documentación Selenium: https://www.selenium.dev/documentation/
- Selenium WebDriver: https://www.selenium.dev/documentation/webdriver/
- Esperas y sincronización: https://www.selenium.dev/documentation/webdriver/waits/
- Selenium Grid: https://www.selenium.dev/documentation/grid/
- Prácticas recomendadas: https://www.selenium.dev/documentation/test_practices/
- JUnit 5: https://junit.org/junit5/docs/current/user-guide/
- Maven: https://maven.apache.org/guides/
- SonarQube: https://docs.sonarsource.com/sonarqube-server/
- Jenkins Pipeline: https://www.jenkins.io/doc/book/pipeline/
