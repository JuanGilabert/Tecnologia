# Pruebas funcionales (Functional Testing)

## 1. Introducción

Las **pruebas funcionales** son un tipo de prueba de software cuyo objetivo es verificar que una aplicación cumple los requisitos funcionales definidos para ella.

Comprueban qué hace el sistema ante determinadas entradas, acciones o situaciones y si los resultados observables son los esperados.

Por ejemplo, en una aplicación de comercio electrónico, las pruebas funcionales pueden verificar que:

- Un usuario puede iniciar sesión con credenciales válidas.
- Un usuario recibe un mensaje de error si introduce una contraseña incorrecta.
- Un producto puede añadirse al carrito.
- El precio total se calcula correctamente.
- Un pedido puede completarse siguiendo el proceso de compra.
- Un usuario no puede acceder a una funcionalidad para la que carece de permisos.
- Una contraseña puede recuperarse mediante el flujo previsto.

Estas pruebas se centran principalmente en el **comportamiento observable del sistema**, no en cómo está implementado internamente.

### Objetivos principales

1. Verificar que los requisitos funcionales se cumplen.
2. Validar los flujos de trabajo de los usuarios.
3. Detectar errores en las reglas de negocio.
4. Comprobar que los distintos componentes colaboran correctamente desde la perspectiva funcional.
5. Reducir las regresiones al modificar la aplicación.
6. Validar los criterios de aceptación definidos por negocio.
7. Proporcionar confianza antes de publicar una nueva versión.

---

## 2. ¿Qué problema resuelven?

Una aplicación puede contener métodos correctamente implementados y pruebas unitarias satisfactorias y, aun así, no funcionar correctamente como producto.

Imaginemos una aplicación bancaria en la que el cálculo de una transferencia funciona correctamente de forma aislada. Sin embargo, al ejecutar el flujo completo:

- El usuario no puede seleccionar la cuenta de origen.
- El formulario permite introducir un importe no válido.
- La operación se procesa, pero el saldo no se actualiza.
- Se muestra un mensaje de éxito aunque la operación haya fallado.

Las pruebas unitarias de los métodos individuales podrían no detectar todos estos problemas.

Las pruebas funcionales verifican que la aplicación ofrece el comportamiento esperado al ejecutar una funcionalidad completa o una parte significativa de ella.

### Sin pruebas funcionales

1. Se desarrollan los componentes.
2. Se comprueba cada unidad de forma aislada.
3. Se integran los componentes.
4. Aparecen errores en los flujos completos.
5. Los errores se descubren durante las pruebas manuales o, en el peor caso, en producción.

### Con pruebas funcionales

1. Se identifican los requisitos.
2. Se definen los escenarios de uso.
3. Se preparan los datos y el entorno.
4. Se ejecutan las acciones previstas.
5. Se comparan los resultados reales con los esperados.
6. Se registran y corrigen los defectos detectados.

---

## 3. ¿Qué se considera una prueba funcional?

Una prueba funcional valida una capacidad del sistema desde el punto de vista de los requisitos.

Por ejemplo, para una funcionalidad de inicio de sesión, podemos definir los siguientes escenarios:

| Escenario | Entrada o condición | Resultado esperado |
|---|---|---|
| Inicio de sesión correcto | Credenciales válidas | Acceso autorizado |
| Contraseña incorrecta | Usuario existente y contraseña errónea | Acceso denegado |
| Usuario inexistente | Email no registrado | Acceso denegado con respuesta apropiada |
| Campos vacíos | Sin email ni contraseña | Se informa de los campos obligatorios |
| Sesión expirada | Token o sesión caducados | Se solicita autenticación de nuevo |
| Acceso no autorizado | Usuario sin permisos | Se deniega la operación protegida |

Estos escenarios pueden ejecutarse manualmente o automatizarse.

Una prueba funcional no tiene por qué utilizar una interfaz gráfica. También puede probarse mediante una API, una aplicación de línea de comandos o cualquier otra interfaz que permita observar el comportamiento funcional.

**Importante:** las pruebas funcionales no son necesariamente pruebas de extremo a extremo (E2E). Pueden comprobar una funcionalidad a través de una API o de un componente integrado sin recorrer toda la aplicación.

---

## 4. Diferencias entre pruebas unitarias, funcionales y de integración

Aunque están relacionadas, estas pruebas tienen objetivos diferentes.

| Característica | Unitarias | Integración | Funcionales |
|---|---|---|---|
| Objetivo principal | Verificar una unidad de comportamiento | Verificar la colaboración entre componentes | Verificar requisitos funcionales |
| Alcance habitual | Método, clase o unidad aislada | Dos o más componentes integrados | Una capacidad o flujo funcional |
| Dependencias | Normalmente aisladas o controladas | Se prueban interacciones reales o representativas | Depende del nivel; puede incluir varios componentes |
| Interfaz gráfica | Normalmente no | No necesariamente | Opcional |
| Base de datos real | Normalmente no | Frecuente, según el escenario | Puede utilizarse |
| Ejemplo | Calcular un descuento | Verificar un servicio y su repositorio | Completar una compra |
| Velocidad | Generalmente muy alta | Intermedia | Variable; los E2E suelen ser más lentos |

La distinción no depende exclusivamente del número de clases utilizadas. Depende del objetivo de la prueba, del contrato que verifica y de las dependencias que participan.

Una prueba funcional de una API, por ejemplo, puede comprobar una regla de negocio sin utilizar un navegador. Una prueba E2E de compra puede validar el recorrido completo desde la interfaz hasta la confirmación del pedido.

---

## 5. Tipos y niveles de pruebas funcionales

Las pruebas funcionales pueden organizarse de distintas maneras, según la perspectiva y el alcance.

### 5.1. Pruebas de funcionalidades aisladas

Verifican una capacidad concreta del sistema.

Ejemplos:

- Registrar un usuario.
- Crear un producto.
- Actualizar un perfil.
- Calcular el precio de un pedido.
- Recuperar una contraseña.

### 5.2. Pruebas de flujos de usuario

Comprueban una secuencia de acciones que el usuario realiza para alcanzar un objetivo.

Ejemplo de compra:

1. Acceder a la aplicación.
2. Buscar un producto.
3. Añadirlo al carrito.
4. Introducir los datos de envío.
5. Seleccionar el método de pago.
6. Confirmar el pedido.
7. Verificar la confirmación.

Estas pruebas son especialmente útiles para detectar problemas de integración y errores en la coordinación de componentes.

### 5.3. Pruebas funcionales de API

Verifican que los endpoints responden de acuerdo con su contrato funcional.

Pueden comprobar:

- Códigos HTTP.
- Estructura de las respuestas.
- Validaciones de entrada.
- Reglas de negocio.
- Autorización.
- Efectos observables de las operaciones.

Por ejemplo, crear un pedido mediante `POST /orders` y comprobar que se devuelve una respuesta apropiada y que el pedido queda registrado.

### 5.4. Pruebas de aceptación

Verifican si una funcionalidad cumple los criterios de aceptación acordados con negocio o con el cliente.

Pueden ejecutarse manualmente o automatizarse. No son necesariamente E2E: una prueba de aceptación puede realizarse en una API, una interfaz o cualquier nivel que permita comprobar el requisito.

### 5.5. Pruebas de regresión funcional

Comprueban que una modificación no ha roto funcionalidades que anteriormente funcionaban.

Por ejemplo, después de cambiar el sistema de descuentos, se vuelven a comprobar la creación de pedidos, el cálculo del precio y la confirmación de la compra.

### 5.6. Pruebas basadas en reglas de negocio

Verifican políticas y condiciones funcionales.

Ejemplos:

- Un pedido superior a cierto importe obtiene envío gratuito.
- Solo los administradores pueden eliminar productos.
- Un usuario no puede realizar una transferencia superior a su límite.
- Un descuento no puede superar el máximo permitido.

### 5.7. Pruebas positivas y negativas

Las pruebas positivas comprueban que el sistema funciona con entradas válidas.

Las pruebas negativas verifican que el sistema gestiona correctamente entradas inválidas, restricciones y errores previsibles.

Ambas son necesarias para una cobertura funcional adecuada.

---

## 6. Enfoques de prueba: caja negra y caja blanca

Las pruebas funcionales suelen diseñarse desde una perspectiva de **caja negra**.

### Caja negra

Se conoce el comportamiento esperado, pero no es necesario conocer la implementación interna.

Se proporcionan entradas, se ejecutan acciones y se verifican resultados observables.

Por ejemplo, para probar una API de registro no es necesario conocer cómo se organizan internamente las clases de persistencia.

### Caja blanca

El diseño de las pruebas utiliza información sobre la estructura interna del código, como ramas, condiciones o caminos de ejecución.

Este enfoque es frecuente en pruebas unitarias y análisis estructural, aunque también puede ayudar a complementar las pruebas funcionales.

Los dos enfoques no son incompatibles. Se pueden utilizar de manera complementaria para identificar escenarios relevantes y verificar tanto el comportamiento como aspectos estructurales.

---

## 7. Técnicas de diseño de pruebas funcionales

Diseñar buenas pruebas requiere seleccionar casos representativos. No basta con probar entradas al azar.

### 7.1. Particiones de equivalencia

Consiste en agrupar las entradas que deberían producir un comportamiento equivalente.

Supongamos que un descuento se aplica a pedidos cuyo importe está entre 50 y 500 euros, ambos incluidos.

Podemos identificar estas particiones:

- Menos de 50 euros: descuento no aplicable.
- Entre 50 y 500 euros: descuento aplicable.
- Más de 500 euros: descuento no aplicable, si así lo define la regla.

En lugar de probar todos los importes, seleccionamos valores representativos de cada partición.

### 7.2. Análisis de valores límite

Los errores aparecen con frecuencia en los límites de las condiciones.

Para un intervalo permitido de 50 a 500 euros, conviene probar:

- 49,99.
- 50,00.
- 50,01.
- 499,99.
- 500,00.
- 500,01.

Esto permite detectar errores relacionados con operadores como `<`, `<=`, `>` y `>=`.

### 7.3. Tablas de decisión

Resultan útiles cuando el comportamiento depende de varias condiciones.

Ejemplo de una política de envío:

| Cliente premium | Pedido superior a 100 € | Envío gratuito |
|---|---|---|
| No | No | No |
| No | Sí | Sí |
| Sí | No | Sí |
| Sí | Sí | Sí |

La tabla permite identificar las combinaciones que deben verificarse y descubrir reglas ambiguas.

### 7.4. Transición de estados

Se utilizan cuando el sistema tiene estados y transiciones permitidas.

Ejemplo para un pedido:

`CREATED → PAID → SHIPPED → DELIVERED`

Las pruebas deben verificar tanto las transiciones válidas como las no permitidas, por ejemplo, impedir que un pedido cancelado se marque como enviado.

### 7.5. Casos de uso y escenarios

Se parte de los objetivos del usuario y se describen los pasos necesarios para alcanzarlos.

Esta técnica es útil para funcionalidades con varios pasos, como registrarse, reservar una cita o completar una compra.

---

## 8. Ejemplo práctico: requisitos funcionales de una tienda online

Imaginemos una tienda online con la siguiente regla:

> Un cliente puede comprar un producto si hay unidades suficientes en el inventario. Si no hay stock, la compra debe rechazarse y no debe generarse un pedido confirmado.

### Requisitos

- El producto debe existir.
- La cantidad solicitada debe ser positiva.
- Debe haber unidades suficientes.
- Una compra válida debe generar un pedido.
- Una compra inválida no debe generar un pedido confirmado.

### Escenarios

| Identificador | Escenario | Resultado esperado |
|---|---|---|
| FT-01 | Producto con stock suficiente | Compra aceptada |
| FT-02 | Producto sin stock | Compra rechazada |
| FT-03 | Cantidad igual a cero | Solicitud rechazada |
| FT-04 | Cantidad negativa | Solicitud rechazada |
| FT-05 | Producto inexistente | Respuesta de recurso no encontrado o equivalente |
| FT-06 | Cantidad superior al stock | Compra rechazada |
| FT-07 | Error al guardar el pedido | No se informa de una compra exitosa |

Los códigos HTTP exactos y los mensajes deben establecerse en el contrato de la API o en los requisitos del proyecto. No conviene elegirlos arbitrariamente durante la automatización.

---

## 9. Ejemplo de pruebas funcionales de API con Java

Para probar funcionalmente una API REST se pueden utilizar herramientas como REST Assured, junto con JUnit.

Supongamos que existe un endpoint:

`POST /api/orders`

Y que el contrato especifica que una solicitud válida crea un pedido y devuelve `201 Created`.

### Ejemplo con REST Assured

```java
import static io.restassured.RestAssured.given;
import static org.hamcrest.Matchers.equalTo;
import static org.hamcrest.Matchers.notNullValue;

import org.junit.jupiter.api.Test;

class OrderFunctionalTest {

    @Test
    void shouldCreateOrderWhenStockIsAvailable() {
        given()
            .contentType("application/json")
            .body("""
                {
                  "productId": "PRODUCT-123",
                  "quantity": 2
                }
                """)
        .when()
            .post("/api/orders")
        .then()
            .statusCode(201)
            .body("id", notNullValue())
            .body("status", equalTo("CREATED"));
    }
}
```

Este ejemplo presupone que la API está disponible en la configuración de pruebas y que el producto tiene stock suficiente.

La URL base, los datos iniciales y las dependencias deben configurarse de acuerdo con el proyecto. La prueba debe ejecutarse contra un entorno controlado, no contra producción.

### ¿Qué valida?

- Que el endpoint acepta una solicitud válida.
- Que devuelve el código de respuesta definido.
- Que la respuesta contiene un identificador.
- Que el estado inicial del pedido es el esperado.

Para que la prueba sea realmente útil, hay que comprobar también que el estado persistido y las reglas de negocio son correctos cuando corresponda. Un código `201` por sí solo no demuestra que toda la operación se haya realizado correctamente.

### Prueba de solicitud inválida

Si el contrato define que una cantidad no positiva debe rechazarse con `400 Bad Request`, podemos escribir:

```java
@Test
void shouldRejectOrderWithInvalidQuantity() {
    given()
        .contentType("application/json")
        .body("""
            {
              "productId": "PRODUCT-123",
              "quantity": 0
            }
            """)
    .when()
        .post("/api/orders")
    .then()
        .statusCode(400);
}
```

El código HTTP del ejemplo debe coincidir con el contrato real de la API. En algunos sistemas se utiliza otra respuesta según las convenciones establecidas.

### Dependencias Maven ilustrativas

```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>rest-assured</artifactId>
    <version>5.5.0</version>
    <scope>test</scope>
</dependency>

<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.11.4</version>
    <scope>test</scope>
</dependency>
```

Las versiones son ilustrativas y deben ajustarse a las versiones compatibles con el proyecto.

---

## 10. Ejemplo de pruebas funcionales de interfaz con Playwright

Las pruebas de interfaz verifican el comportamiento visible de una aplicación web mediante acciones como pulsar botones, rellenar formularios y comprobar mensajes.

Playwright permite automatizar navegadores y comprobar el comportamiento de la interfaz.

Ejemplo con TypeScript:

```typescript
import { test, expect } from '@playwright/test';

test('un usuario puede iniciar sesión', async ({ page }) => {
  await page.goto('/login');

  await page.getByLabel('Email').fill('user@example.com');
  await page.getByLabel('Contraseña').fill('correct-password');

  await page.getByRole('button', { name: 'Iniciar sesión' }).click();

  await expect(page).toHaveURL(/dashboard/);
  await expect(
    page.getByRole('heading', { name: 'Panel principal' })
  ).toBeVisible();
});
```

El ejemplo presupone que:

- Existe una página `/login`.
- Los campos tienen etiquetas accesibles.
- El botón utiliza el texto indicado.
- Las credenciales de prueba son válidas.
- Tras iniciar sesión se accede al panel principal.

Los nombres de los elementos y las rutas deben adaptarse a la aplicación real.

### ¿Qué comprueba esta prueba?

1. Que la página de acceso puede abrirse.
2. Que los campos permiten introducir datos.
3. Que se puede enviar el formulario.
4. Que el flujo conduce al panel principal.
5. Que el usuario observa el resultado esperado.

No demuestra por sí sola que el sistema de autenticación sea seguro ni que todas las operaciones de backend funcionen correctamente. Esas propiedades requieren pruebas adicionales.

### Buenas prácticas para pruebas de interfaz

- Utilizar selectores accesibles, como roles y etiquetas.
- Evitar selectores CSS ligados a detalles visuales que cambian con frecuencia.
- Esperar las condiciones necesarias en lugar de introducir pausas fijas.
- Preparar datos de prueba de forma reproducible.
- Evitar depender de servicios externos inestables.
- Verificar resultados visibles y efectos funcionales importantes.
- Capturar trazas, capturas de pantalla o registros cuando una prueba falla.

---

## 11. BDD y Gherkin en pruebas funcionales

BDD (*Behavior-Driven Development*) es un enfoque que facilita definir y comunicar el comportamiento esperado del sistema mediante ejemplos.

Gherkin es un lenguaje estructurado utilizado por herramientas como Cucumber.

Un escenario habitual utiliza:

- `Given`: condición inicial.
- `When`: acción.
- `Then`: resultado esperado.

Ejemplo:

```gherkin
Feature: Compra de productos

  Scenario: Comprar un producto con stock suficiente
    Given que el producto "PRODUCT-123" tiene 5 unidades disponibles
    And que el cliente tiene una sesión válida
    When el cliente compra 2 unidades
    Then el pedido debe crearse correctamente
    And el stock debe reducirse a 3 unidades
```

Este escenario describe el comportamiento esperado sin imponer cómo debe implementarse internamente.

Otro escenario podría comprobar que una compra con stock insuficiente se rechaza y no crea un pedido confirmado.

### Relación entre BDD y pruebas funcionales

BDD ayuda a definir ejemplos comprensibles y compartidos entre desarrollo, pruebas y negocio. Las pruebas funcionales pueden automatizar esos escenarios mediante API, interfaz gráfica u otras interfaces.

No todos los escenarios Gherkin tienen que convertirse en pruebas E2E. La automatización debe realizarse en el nivel que permita verificar el comportamiento con suficiente confianza y un coste razonable.

---

## 12. Pruebas funcionales y regresión

Una prueba funcional también puede formar parte de la suite de regresión.

La regresión consiste en volver a ejecutar pruebas después de un cambio para detectar comportamientos que han dejado de funcionar.

Ejemplo:

1. Se modifica el cálculo del precio de los pedidos.
2. Se ejecutan las pruebas del cálculo.
3. Se comprueba el flujo de creación del pedido.
4. Se verifica el proceso de pago.
5. Se confirma que la factura sigue mostrando el importe correcto.

La suite de regresión debería priorizar las funcionalidades críticas y las áreas afectadas por el cambio.

No es necesario ejecutar siempre todas las pruebas E2E después de cada modificación. Una estrategia habitual es ejecutar pruebas unitarias rápidas durante el desarrollo y reservar suites funcionales más amplias para la integración continua o las etapas previas a una publicación.

---

## 13. Automatización de pruebas funcionales

La automatización permite ejecutar las mismas comprobaciones repetidamente y detectar regresiones sin depender de verificaciones manuales constantes.

### Herramientas habituales

| Herramienta | Uso principal |
|---|---|
| Playwright | Automatización de navegadores y pruebas web |
| Selenium | Automatización de navegadores |
| Cypress | Pruebas de aplicaciones web |
| REST Assured | Pruebas de API REST en Java |
| Postman | Diseño y ejecución de pruebas de API |
| Cucumber | Escenarios BDD y su vinculación con código de automatización |
| Karate | Pruebas de API y otros flujos de automatización |
| Appium | Automatización de aplicaciones móviles |

La elección depende del lenguaje del proyecto, el tipo de aplicación, las capacidades del equipo y la integración con CI/CD.

No es necesario incorporar todas estas herramientas. Conviene seleccionar las que cubran los requisitos reales del proyecto.

### Pruebas manuales y automatizadas

Las pruebas manuales siguen siendo útiles para explorar funcionalidades, evaluar experiencias de usuario, investigar problemas nuevos y comprobar escenarios difíciles de automatizar.

La automatización resulta especialmente valiosa para flujos repetitivos, reglas estables y escenarios críticos que deben ejecutarse en cada entrega.

---

## 14. Pirámide de pruebas

Una estrategia equilibrada suele contener muchos tests unitarios rápidos, un conjunto de pruebas de integración y un conjunto más selectivo de pruebas funcionales amplias o E2E.

```text
                 /\
                /  \
               / E2E\
              /------\
             /Funcio- \
            / nales/API\
           /------------\
          / Integración  \
         /----------------\
        /  Unitarias       \
       /____________________\
```

La representación es conceptual, no una proporción obligatoria. La organización real depende del sistema.

### Pruebas unitarias

Son rápidas y permiten localizar fallos en reglas pequeñas.

### Pruebas de integración

Comprueban colaboraciones entre componentes y dependencias reales.

### Pruebas funcionales de API

Verifican contratos y capacidades de la aplicación con menos dependencia de la interfaz gráfica.

### Pruebas E2E

Verifican flujos completos a través de las principales capas del sistema.

Una buena estrategia evita depender exclusivamente de E2E para verificar cada regla de negocio, ya que suelen ser más lentas y sensibles a cambios del entorno.

---

## 15. Pruebas funcionales y calidad del código

Las pruebas funcionales aportan información sobre la calidad del comportamiento del sistema, pero no sustituyen a otras prácticas.

Conviene distinguir entre:

- **Correctitud funcional:** la aplicación realiza las operaciones esperadas.
- **Calidad estructural:** el código es legible, mantenible y está bien organizado.
- **Seguridad:** la aplicación protege datos, operaciones y permisos.
- **Rendimiento:** las operaciones responden dentro de los objetivos establecidos.
- **Usabilidad:** la aplicación puede utilizarse de forma comprensible y eficaz.
- **Fiabilidad:** el sistema se comporta de forma consistente en condiciones previstas.

Las pruebas funcionales pueden descubrir errores de lógica y flujos incompletos, pero no garantizan automáticamente seguridad, rendimiento o mantenibilidad.

### Relación con Clean Code y SOLID

Un código bien organizado facilita crear pruebas funcionales estables porque las responsabilidades y los contratos resultan más claros.

- **SRP:** permite separar reglas de negocio de detalles de presentación.
- **DIP:** facilita sustituir dependencias externas durante las pruebas.
- **Clean Code:** promueve contratos comprensibles y comportamientos explícitos.
- **DRY:** reduce la duplicación en la implementación, aunque los escenarios de prueba deben seguir siendo claros.
- **KISS:** favorece flujos y pruebas simples.
- **YAGNI:** evita crear infraestructura de automatización que no aporta valor actual.

### Relación con SonarQube

SonarQube puede ayudar a identificar problemas estáticos de código y, cuando está configurado, mostrar métricas de cobertura.

No ejecuta por sí solo todas las pruebas funcionales ni puede demostrar que una funcionalidad satisface los requisitos de negocio.

La calidad se obtiene combinando pruebas, análisis estático, revisión de código y criterios de aceptación.

---

## 16. Integración con CI/CD

Las pruebas funcionales pueden ejecutarse automáticamente dentro de una pipeline.

Un flujo habitual es:

1. Descargar el código.
2. Instalar dependencias.
3. Compilar la aplicación.
4. Ejecutar las pruebas unitarias.
5. Ejecutar las pruebas de integración.
6. Levantar el entorno necesario.
7. Ejecutar un conjunto seleccionado de pruebas funcionales.
8. Generar informes.
9. Analizar la calidad del código.
10. Publicar los artefactos si se cumplen los criterios definidos.

### Ejemplo conceptual

```yaml
name: Functional Tests

on:
  pull_request:
  push:
    branches: [main]

jobs:
  functional-tests:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: 22

      - name: Install dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      - name: Run functional tests
        run: npx playwright test
```

Este ejemplo presupone que el repositorio contiene una configuración válida de Playwright y que la aplicación de prueba está disponible o se inicia mediante la configuración del proyecto. En un proyecto real también hay que definir los secretos, los datos de prueba, el entorno y los servicios necesarios.

Las versiones de las acciones y de Node.js deben ajustarse a las políticas de mantenimiento y compatibilidad del repositorio.

### Buenas prácticas en CI/CD

- Ejecutar primero las pruebas rápidas.
- Reservar las pruebas más costosas para las etapas adecuadas.
- Mantener entornos de prueba reproducibles.
- Gestionar los secretos de forma segura.
- Evitar utilizar datos personales reales.
- Publicar informes y evidencias cuando una prueba falle.
- Investigar y reducir las pruebas inestables.
- Impedir la publicación cuando fallen verificaciones críticas definidas como obligatorias.

---

## 17. Datos y entornos de prueba

La calidad de una prueba funcional depende también de su entorno.

Un escenario puede fallar por una regresión real o porque el producto de prueba no estaba preparado correctamente.

Para reducir esta ambigüedad:

- Preparar datos conocidos antes de cada escenario.
- Evitar depender de pruebas ejecutadas previamente.
- Limpiar los datos creados cuando corresponda.
- Utilizar entornos separados de producción.
- Controlar las versiones de los servicios y las configuraciones.
- Evitar que dos pruebas modifiquen simultáneamente el mismo dato compartido.
- Utilizar dobles de prueba para servicios externos inestables cuando el objetivo no sea probar su integración real.

Cuando se necesita verificar una integración real, se deben utilizar pruebas específicas que incluyan el componente correspondiente y que puedan ejecutarse de forma controlada.

---

## 18. Errores frecuentes en las pruebas funcionales

### Probar únicamente el camino feliz

Se verifica el comportamiento correcto, pero no los errores ni los límites.

**Solución:** definir escenarios positivos, negativos y de valores límite.

### Automatizar toda la aplicación mediante el navegador

Se generan muchas pruebas lentas y frágiles.

**Solución:** comprobar las reglas en niveles más bajos cuando sea apropiado y reservar E2E para flujos realmente importantes.

### Acoplar las pruebas a detalles visuales

Cambios de maquetación provocan fallos aunque el comportamiento funcional siga siendo correcto.

**Solución:** utilizar selectores accesibles y verificar resultados funcionales.

### Utilizar datos compartidos sin control

Las pruebas interfieren entre sí o dependen del orden de ejecución.

**Solución:** aislar datos y preparar el estado de cada escenario.

### Confundir una respuesta HTTP correcta con una operación correcta

La API devuelve el código esperado, pero no actualiza el estado real.

**Solución:** verificar los efectos observables relevantes para el requisito.

### Ignorar los requisitos no funcionales

Las pruebas funcionales pasan y se asume que el sistema es seguro o rápido.

**Solución:** complementar la suite con pruebas de seguridad, rendimiento, accesibilidad y otros atributos según el riesgo.

### No mantener las pruebas

La aplicación evoluciona y las pruebas dejan de representar los requisitos actuales.

**Solución:** revisar y actualizar los escenarios junto con los cambios funcionales.

---

## 19. Estrategia para introducir pruebas funcionales en un proyecto

### Fase 1: identificar funcionalidades críticas

Seleccionar las operaciones de mayor impacto: autenticación, pagos, pedidos, permisos o gestión de datos importantes.

### Fase 2: definir criterios de aceptación

Documentar qué debe ocurrir en los escenarios correctos y qué debe suceder ante errores.

### Fase 3: diseñar los escenarios

Utilizar casos de uso, particiones de equivalencia, valores límite y tablas de decisión.

### Fase 4: seleccionar las herramientas

Elegir entre pruebas de API, interfaz o una combinación según el comportamiento que se quiere verificar.

### Fase 5: automatizar los flujos prioritarios

Comenzar con un conjunto reducido de pruebas fiables y valiosas, en lugar de automatizar toda la aplicación desde el principio.

### Fase 6: integrar las pruebas en CI/CD

Ejecutar las comprobaciones en los momentos adecuados y publicar informes útiles.

### Fase 7: medir y mejorar

Evaluar los defectos detectados, la duración de las pruebas, su estabilidad y el mantenimiento necesario.

El objetivo es que la suite proteja los comportamientos importantes sin ralentizar innecesariamente el desarrollo.

---

## 20. Checklist de revisión

Antes de considerar terminada una prueba funcional, comprueba:

- Existe un requisito o comportamiento esperado claramente definido.
- El escenario contiene datos y condiciones iniciales conocidos.
- Se verifica el resultado funcional esperado.
- Se han considerado errores y valores límite relevantes.
- Los datos y el entorno están controlados.
- La prueba no depende del orden de ejecución.
- La automatización utiliza selectores y mecanismos de espera estables.
- Se verifican los efectos importantes, no solo el mensaje de éxito.
- La prueba es reproducible.
- Los fallos generan información útil para el diagnóstico.
- La prueba se ejecuta en el momento adecuado dentro de CI/CD.
- El escenario sigue correspondiéndose con los requisitos actuales.

---

## 21. Conclusión

Las pruebas funcionales permiten verificar que una aplicación cumple los requisitos desde la perspectiva de su comportamiento observable.

Complementan las pruebas unitarias porque validan capacidades y flujos funcionales, y ayudan a descubrir errores que aparecen al integrar componentes o al ejecutar procesos completos.

Su eficacia depende de seleccionar buenos escenarios, definir resultados esperados, preparar entornos reproducibles y elegir el nivel de automatización adecuado.

Una estrategia equilibrada combina:

- Pruebas unitarias para reglas y unidades pequeñas.
- Pruebas de integración para colaboraciones entre componentes.
- Pruebas funcionales de API para contratos y capacidades.
- Pruebas E2E para flujos críticos de usuario.
- Pruebas de aceptación para validar requisitos.
- Análisis estático con herramientas como SonarQube.
- Automatización mediante CI/CD.

El objetivo no es automatizar cada interacción, sino obtener confianza suficiente en que las funcionalidades importantes siguen funcionando cuando el software evoluciona.

---

## 22. Referencias oficiales

- Playwright: https://playwright.dev/
- Selenium: https://www.selenium.dev/documentation/
- Cypress: https://docs.cypress.io/
- REST Assured: https://rest-assured.io/
- Cucumber: https://cucumber.io/docs/
- Postman: https://learning.postman.com/
- Appium: https://appium.io/docs/en/latest/
- SonarQube: https://docs.sonarsource.com/sonarqube-server/
- Martin Fowler, The Practical Test Pyramid: https://martinfowler.com/articles/practical-test-pyramid.html
- ISTQB: https://www.istqb.org/
