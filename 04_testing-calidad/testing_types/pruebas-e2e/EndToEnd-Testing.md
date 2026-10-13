# Pruebas E2E (End-to-End Testing)

## 1. Introducción

Las **pruebas End-to-End (E2E)** son pruebas de software que verifican que un flujo completo de una aplicación funciona correctamente desde el punto de entrada hasta el resultado final esperado.

Su objetivo es comprobar que los diferentes componentes de un sistema colaboran correctamente en un escenario realista, incluyendo, cuando corresponda, la interfaz de usuario, el backend, las APIs, las bases de datos, los sistemas de autenticación y los servicios externos.

A diferencia de una prueba unitaria, que comprueba una unidad aislada, una prueba E2E evalúa una funcionalidad de extremo a extremo.

Por ejemplo, en una aplicación de comercio electrónico, una prueba E2E podría verificar el siguiente flujo:

1. El usuario accede a la aplicación.
2. Inicia sesión.
3. Busca un producto.
4. Añade el producto al carrito.
5. Introduce los datos de envío.
6. Completa una compra de prueba.
7. Comprueba que el pedido se ha registrado correctamente.

La prueba no se limita a comprobar que cada pantalla aparece correctamente. También verifica que los distintos componentes funcionan juntos y que el proceso completo produce el resultado esperado.

### Objetivos principales

- Verificar los flujos críticos del negocio.
- Comprobar la integración real entre componentes.
- Validar el comportamiento desde la perspectiva del usuario.
- Detectar errores que no aparecen en pruebas unitarias o de integración.
- Verificar la comunicación entre frontend, backend y bases de datos.
- Comprobar que las principales funcionalidades siguen funcionando después de un cambio.
- Reducir el riesgo de introducir regresiones en producción.
- Automatizar escenarios repetitivos de validación.

---

## 2. ¿Qué problemas resuelven las pruebas E2E?

Una aplicación puede tener pruebas unitarias y de integración satisfactorias y, aun así, presentar problemas cuando un usuario intenta realizar una operación completa.

Por ejemplo:

- El formulario de inicio de sesión funciona visualmente, pero envía mal las credenciales.
- El backend autentica correctamente, pero el frontend no guarda la sesión.
- El carrito muestra un producto, pero el pedido se crea con un importe incorrecto.
- La API devuelve los datos esperados, pero la interfaz no los representa.
- Una modificación en el sistema de navegación impide completar una compra.
- La aplicación procesa correctamente una petición aislada, pero falla cuando el usuario cambia de pantalla durante el proceso.

Las pruebas E2E permiten detectar estos defectos porque validan el recorrido completo y no únicamente componentes individuales.

### Sin pruebas E2E

1. Se implementan las funcionalidades.
2. Las pruebas unitarias verifican la lógica aislada.
3. Las pruebas de integración verifican algunas dependencias.
4. Se despliega la aplicación.
5. Un usuario descubre que el flujo completo falla.

### Con pruebas E2E

1. Se define un escenario de negocio.
2. Se automatizan las acciones del usuario.
3. Se ejecuta el flujo contra una aplicación desplegada.
4. Se comprueban las respuestas y el estado final.
5. Se detectan errores antes de publicar.
6. Se corrigen y se repite la prueba.

**Idea clave:** las pruebas E2E verifican que el sistema completo satisface un escenario de uso, pero no sustituyen a las pruebas unitarias, de integración ni a las pruebas especializadas de rendimiento o seguridad.

---

## 3. ¿Qué se considera una prueba E2E?

Una prueba E2E ejecuta un escenario que atraviesa los límites relevantes de un sistema y verifica el resultado final.

En una aplicación web, puede utilizar un navegador real o automatizado para interactuar con la interfaz, mientras el sistema se comunica con sus APIs, bases de datos y otros servicios.

En otros contextos, una prueba E2E puede validar una operación completa mediante APIs, aplicaciones móviles, sistemas de mensajería o procesos de backend, aunque no exista una interfaz gráfica.

Lo que define una prueba E2E no es necesariamente utilizar un navegador, sino **validar un flujo completo a través de los componentes relevantes del sistema**.

### Ejemplo de arquitectura

Una compra online podría seguir este recorrido:

```text
Usuario
   |
   v
Frontend web
   |
   v
API REST
   |
   v
Servicio de pedidos
   |
   +------> Base de datos
   |
   +------> Servicio de pagos de prueba
   |
   v
Pedido confirmado
   |
   v
Confirmación visible para el usuario
```

Una prueba E2E de compra comprueba que el recorrido completo produce un pedido correcto y una confirmación coherente.

No es necesario que todos los sistemas externos sean reales. En un entorno de pruebas pueden utilizarse servicios de pago simulados, siempre que se documente qué partes del flujo se están validando y cuáles quedan fuera del alcance.

---

## 4. Diferencias entre pruebas unitarias, integración, funcionales y E2E

Estas categorías pueden solaparse: las pruebas unitarias, de integración y E2E describen principalmente el alcance o nivel de comprobación, mientras que las pruebas funcionales describen el objetivo de validar un comportamiento. Una prueba E2E puede ser también una prueba funcional.

| Tipo de prueba | Alcance | Objetivo principal |
|---|---|---|
| Unitaria | Una función, clase o unidad aislada | Verificar una regla de lógica |
| Integración | Dos o más componentes colaborando | Verificar su interacción |
| Funcional | Una funcionalidad o requisito | Comprobar el comportamiento esperado |
| E2E | Un flujo completo a través del sistema | Verificar que el proceso funciona de principio a fin |
| Rendimiento | Sistema o componente bajo carga | Medir latencia, capacidad y estabilidad |

### Ejemplo: inicio de sesión

**Prueba unitaria**

Verifica que un servicio rechaza credenciales inválidas.

**Prueba de integración**

Verifica que el servicio de autenticación consulta correctamente el repositorio de usuarios.

**Prueba funcional**

Verifica que un usuario con credenciales incorrectas recibe un mensaje de error apropiado.

**Prueba E2E**

Abre la aplicación, introduce credenciales, envía el formulario y verifica que el usuario autenticado accede a su área privada.

Cada nivel aporta información diferente. La prueba E2E confirma el flujo completo, mientras que las pruebas más pequeñas ayudan a localizar la causa de un fallo.

---

## 5. Características de las pruebas E2E

### 5.1. Validan flujos completos

Comprueban una secuencia de acciones y su resultado final, no solo una llamada aislada.

### 5.2. Utilizan componentes reales cuando es necesario

Pueden ejecutarse contra un frontend desplegado, una API real y una base de datos de pruebas.

El alcance debe ser explícito: no todas las pruebas E2E necesitan utilizar todas las dependencias externas reales.

### 5.3. Simulan comportamientos de usuario

En aplicaciones web pueden incluir navegación, clics, introducción de datos, envío de formularios y validación de mensajes.

### 5.4. Comprueban resultados observables

Verifican que el usuario recibe la respuesta esperada y, cuando sea necesario, que el estado persistido también es correcto.

### 5.5. Son más costosas que las pruebas unitarias

Suelen requerir más tiempo de ejecución, preparación de datos, infraestructura y mantenimiento.

### 5.6. Pueden ser sensibles al entorno

Una aplicación lenta, una dependencia inestable o una configuración incorrecta puede hacer fallar una prueba aunque el código no haya cambiado.

Por este motivo, la estabilidad del entorno y la calidad de la automatización son fundamentales.

---

## 6. Tipos de pruebas E2E

### 6.1. E2E de interfaz de usuario

Validan los flujos mediante la interfaz que utiliza el usuario.

Ejemplos:

- Iniciar sesión.
- Crear una cuenta.
- Buscar un producto.
- Completar un formulario.
- Realizar una compra.
- Descargar un informe.

Son especialmente útiles para comprobar que la navegación, los formularios y las interacciones principales funcionan correctamente.

### 6.2. E2E de API

Verifican un proceso completo a través de endpoints y servicios, sin necesidad de utilizar un navegador.

Ejemplo:

1. Crear un cliente.
2. Crear un pedido asociado.
3. Consultar el pedido.
4. Verificar sus líneas y su importe.
5. Comprobar su estado final.

Estas pruebas pueden ser más rápidas y estables que las pruebas de interfaz, aunque no verifican el comportamiento visual del frontend.

### 6.3. E2E de aplicaciones móviles

Validan flujos completos en aplicaciones Android o iOS.

Ejemplos:

- Inicio de sesión.
- Registro.
- Navegación entre pantallas.
- Compra mediante la aplicación.
- Recepción de una notificación.
- Recuperación de una sesión.

Herramientas como Appium permiten automatizar interacciones en aplicaciones móviles.

### 6.4. E2E de procesos de negocio

Verifican procesos que atraviesan varios servicios o sistemas.

Ejemplo de gestión de pedidos:

1. Se crea un pedido.
2. Se valida el pago.
3. Se actualiza el inventario.
4. Se genera la confirmación.
5. Se verifica el estado final.

En arquitecturas distribuidas, el proceso puede ser asíncrono. La prueba debe contemplar los tiempos de procesamiento y los estados intermedios válidos.

### 6.5. E2E de sistemas distribuidos

Comprueban la colaboración de servicios a través de APIs, colas de mensajes y otros mecanismos de comunicación.

Son relevantes en arquitecturas de microservicios y orientadas a eventos.

Por ejemplo, una prueba puede verificar que crear un pedido provoca la actualización del estado del pedido y la publicación de un evento que posteriormente procesa otro servicio.

Es importante evitar depender de tiempos fijos arbitrarios cuando el sistema es asíncrono. Es preferible esperar por una condición observable con un límite de tiempo razonable.

---

## 7. Herramientas para pruebas E2E

La elección depende del tipo de aplicación, el ecosistema tecnológico y el nivel de integración requerido.

### 7.1. Playwright

Playwright permite automatizar navegadores y validar aplicaciones web. Incluye herramientas para interactuar con elementos, realizar comprobaciones y gestionar esperas.

Ventajas:

- Automatización de flujos completos en navegador.
- Compatibilidad con varios motores de navegador.
- Soporte para diferentes lenguajes, incluido TypeScript y Java.
- Aserciones y mecanismos de espera integrados.
- Capturas, trazas y herramientas de diagnóstico.

Documentación oficial: https://playwright.dev/

### 7.2. Cypress

Cypress es una herramienta orientada a la automatización de aplicaciones web.

Ventajas:

- Experiencia de desarrollo centrada en JavaScript y TypeScript.
- Ejecución y depuración visual.
- Comprobaciones integradas.
- Buen soporte para pruebas de aplicaciones frontend.

Documentación oficial: https://docs.cypress.io/

### 7.3. Selenium

Selenium permite automatizar navegadores y cuenta con soporte para distintos lenguajes y ecosistemas.

Es una alternativa habitual en proyectos con infraestructura de automatización ya establecida.

Documentación oficial: https://www.selenium.dev/documentation/

### 7.4. Appium

Appium permite automatizar aplicaciones móviles y otros escenarios de automatización de interfaces.

Documentación oficial: https://appium.io/docs/en/latest/

### 7.5. REST Assured

REST Assured facilita la automatización de pruebas de APIs REST en proyectos Java.

Puede utilizarse para verificar escenarios de negocio completos a través de endpoints, incluyendo códigos HTTP, cabeceras y contenido de las respuestas.

Documentación oficial: https://rest-assured.io/

### 7.6. ¿Qué herramienta elegir?

| Contexto | Herramienta que conviene evaluar |
|---|---|
| Aplicación web con TypeScript | Playwright o Cypress |
| Aplicación web con Java y Selenium ya establecido | Selenium |
| Flujos de APIs en Java | REST Assured |
| Aplicación móvil nativa o híbrida | Appium |
| Aplicación web con necesidad de trazas y diagnóstico detallado | Playwright |

La mejor herramienta no es necesariamente la que tiene más funcionalidades, sino la que se adapta al proyecto, al equipo y al tipo de flujo que se necesita validar.

---

## 8. Ejemplo práctico con Playwright y TypeScript

Supongamos que tenemos una aplicación web con una pantalla de inicio de sesión.

Queremos verificar que:

1. La aplicación se abre.
2. El usuario introduce sus credenciales.
3. Se envía el formulario.
4. La aplicación permite el acceso.
5. Aparece el área privada.

### Estructura del proyecto

```text
project/
├── src/
├── tests/
│   └── login.spec.ts
├── playwright.config.ts
└── package.json
```

### Ejemplo de prueba

```typescript
import { test, expect } from '@playwright/test';

test('un usuario puede iniciar sesión', async ({ page }) => {
  await page.goto('/login');

  await page.getByLabel('Correo electrónico').fill(
    'usuario@example.com'
  );

  await page.getByLabel('Contraseña').fill(
    'PasswordDePrueba123!'
  );

  await page.getByRole('button', {
    name: 'Iniciar sesión',
  }).click();

  await expect(page).toHaveURL(/\/dashboard/);

  await expect(
    page.getByRole('heading', {
      name: 'Mi área personal',
    })
  ).toBeVisible();
});
```

Este ejemplo es ilustrativo: los textos de los campos, las rutas y el título deben coincidir con los elementos reales de la aplicación.

La prueba no debe utilizar credenciales reales de producción. En un entorno automatizado se recomienda crear usuarios de prueba y gestionar las credenciales mediante variables de entorno o un mecanismo seguro equivalente.

### ¿Qué se está validando?

- Que la ruta de inicio de sesión está disponible.
- Que los campos permiten introducir información.
- Que el botón envía el formulario.
- Que el flujo termina en la ruta esperada.
- Que el contenido del área privada aparece.

La prueba verifica el resultado observable desde el navegador. Si además se necesita comprobar un estado persistido, como un registro en base de datos, puede añadirse una comprobación específica a través de una API de pruebas o de un mecanismo de acceso controlado al estado.

### Configuración de ejemplo

```typescript
import { defineConfig } from '@playwright/test';

export default defineConfig({
  testDir: './tests',

  use: {
    baseURL: process.env.BASE_URL ?? 'http://localhost:3000',
    trace: 'retain-on-failure',
  },

  retries: 1,
});
```

`baseURL` permite cambiar el entorno sin modificar la prueba. La configuración de trazas facilita el diagnóstico de fallos.

El número de reintentos debe utilizarse con cuidado: puede ayudar a diagnosticar fallos transitorios, pero no debería ocultar pruebas inestables.

### Ejecución

Con Playwright configurado en el proyecto:

```bash
npx playwright test
```

Para abrir el informe HTML generado:

```bash
npx playwright show-report
```

Consulta la documentación oficial para la instalación y la configuración del proyecto: https://playwright.dev/docs/intro

---

## 9. Ejemplo de un flujo E2E de negocio

Consideremos una tienda online.

El objetivo es verificar que un usuario puede completar una compra y que el sistema registra el pedido correctamente.

### Escenario

**Dado** que existe un usuario de prueba autenticable y hay un producto disponible.

**Cuando** el usuario añade el producto al carrito, completa los datos necesarios y confirma la compra.

**Entonces** el sistema crea un pedido con el producto esperado, el importe correcto y el estado inicial previsto.

### Pasos automatizados

1. Preparar un usuario y un producto de prueba.
2. Abrir la aplicación.
3. Autenticarse.
4. Buscar el producto.
5. Añadirlo al carrito.
6. Comprobar la cantidad y el precio.
7. Completar el proceso de compra.
8. Utilizar una integración de pagos de prueba.
9. Esperar la confirmación.
10. Verificar el número de pedido y su contenido.
11. Limpiar los datos generados si corresponde.

### Qué debe comprobarse

No basta con que aparezca el mensaje «Compra realizada».

También puede ser necesario verificar:

- Que el pedido existe.
- Que contiene las líneas correctas.
- Que el importe es correcto.
- Que no se han creado pedidos duplicados.
- Que el estado coincide con el flujo previsto.
- Que la confirmación corresponde al pedido creado.

Las comprobaciones deben centrarse en los resultados relevantes para el negocio. No es necesario verificar cada detalle interno de implementación.

---

## 10. Pruebas E2E con BDD

BDD (*Behavior-Driven Development*) es un enfoque de desarrollo que ayuda a describir el comportamiento esperado del sistema mediante ejemplos comprensibles para negocio, desarrollo y testing.

Puede combinarse con pruebas E2E para convertir escenarios de negocio en pruebas automatizadas.

Un formato habitual es Gherkin.

### Ejemplo de escenario

```gherkin
Feature: Inicio de sesión

  Scenario: Acceso con credenciales válidas
    Given que existe un usuario registrado
    And que el usuario se encuentra en la página de acceso
    When introduce credenciales válidas
    And confirma el formulario
    Then accede a su área personal
```

El escenario describe el comportamiento esperado, pero no constituye por sí mismo una prueba ejecutable. Es necesario conectarlo a implementaciones que realicen las acciones y comprobaciones correspondientes.

Herramientas como Cucumber permiten automatizar escenarios escritos en Gherkin.

Documentación oficial: https://cucumber.io/docs/

### Ventajas de combinar BDD y E2E

- Los escenarios se expresan en términos de negocio.
- Los requisitos pueden vincularse con ejemplos ejecutables.
- Facilita la colaboración entre roles.
- Permite revisar los flujos críticos antes de automatizarlos.

### Precaución

No conviene convertir cada requisito en un escenario E2E. Los escenarios de bajo nivel y las reglas detalladas suelen ser más fáciles de mantener mediante pruebas unitarias o de integración.

---

## 11. Pruebas E2E en arquitecturas de microservicios

En una arquitectura de microservicios, un flujo de negocio puede atravesar varios servicios independientes.

Por ejemplo:

```text
Frontend
   |
   v
API Gateway
   |
   v
Servicio de pedidos
   |
   +------> Servicio de inventario
   |
   +------> Servicio de pagos
   |
   +------> Servicio de notificaciones
```

Una prueba E2E puede validar que una operación iniciada por el usuario produce el resultado esperado a través de todos los servicios relevantes.

### Problemas habituales

- Dependencias externas inestables.
- Procesamiento asíncrono.
- Orden de llegada de eventos.
- Datos compartidos entre pruebas.
- Tiempos de espera difíciles de predecir.
- Dificultad para identificar qué servicio ha provocado el fallo.

### Buenas prácticas

1. Definir el alcance de cada prueba.
2. Utilizar entornos de prueba aislados cuando sea posible.
3. Preparar datos controlados.
4. Esperar condiciones observables en los procesos asíncronos.
5. Correlacionar solicitudes y eventos con identificadores.
6. Recopilar logs y trazas.
7. Evitar depender de servicios externos reales cuando no sean necesarios.
8. Mantener pruebas de integración por servicio para localizar errores más rápidamente.

En un sistema distribuido, una prueba E2E no debería ser la única forma de verificar las interacciones. Las pruebas de contrato y las pruebas de integración por servicio ayudan a detectar incompatibilidades con un coste menor.

---

## 12. Pruebas E2E y calidad del código

Las pruebas E2E contribuyen a la calidad del software porque comprueban que los cambios no rompen flujos relevantes para los usuarios.

Sin embargo, no demuestran por sí solas que el código esté bien diseñado, sea seguro o tenga un rendimiento adecuado.

### Relación con Clean Code

Un código claro facilita mantener la aplicación y comprender el comportamiento que las pruebas deben verificar.

Las pruebas también deberían ser legibles: un escenario debe comunicar qué comportamiento se está comprobando sin obligar a entender todos los detalles internos.

### Relación con SOLID

- **SRP:** separar responsabilidades facilita localizar defectos cuando falla un flujo.
- **OCP:** permite ampliar comportamientos sin introducir modificaciones indiscriminadas en componentes existentes.
- **LSP:** ayuda a mantener contratos coherentes entre implementaciones sustituibles.
- **ISP:** evita que los clientes dependan de interfaces innecesariamente amplias.
- **DIP:** facilita sustituir dependencias y crear entornos de prueba controlados.

Estos principios mejoran la estructura y mantenibilidad, pero no garantizan automáticamente que una prueba E2E sea estable. También hacen falta buenos escenarios, datos controlados y una infraestructura adecuada.

### Relación con DRY, KISS y YAGNI

- **DRY:** evita duplicar lógica de negocio y puede reducir la duplicación de utilidades de prueba.
- **KISS:** favorece escenarios sencillos, explícitos y fáciles de diagnosticar.
- **YAGNI:** evita crear infraestructuras de automatización innecesarias antes de que exista una necesidad real.

La reutilización de código de pruebas debe aplicarse con criterio. Una abstracción excesiva puede ocultar lo que hace una prueba y dificultar su mantenimiento.

---

## 13. Pruebas E2E y SonarQube

SonarQube puede ayudar a analizar la calidad estática del código de producción y del código de pruebas compatible con los analizadores configurados.

Puede contribuir a detectar problemas de mantenibilidad y determinados defectos de código, pero no ejecuta por sí mismo una validación completa de los flujos E2E.

Conviene separar las responsabilidades:

| Herramienta o técnica | Responsabilidad |
|---|---|
| SonarQube | Analizar aspectos estáticos de calidad |
| JUnit | Ejecutar pruebas automatizadas en Java |
| Playwright, Cypress o Selenium | Automatizar flujos de interfaz web |
| REST Assured | Automatizar pruebas de APIs en Java |
| JaCoCo | Medir cobertura de ejecución en proyectos Java |
| Allure u otros informes compatibles | Facilitar la consulta de resultados de pruebas |
| Logs y trazas | Ayudar a diagnosticar fallos durante la ejecución |

Una buena cobertura de código no garantiza que todos los flujos de negocio funcionen correctamente.

Del mismo modo, que todas las pruebas E2E pasen no demuestra que no existan defectos en escenarios no cubiertos.

La calidad se obtiene combinando pruebas de distintos niveles, análisis estático y observabilidad.

---

## 14. Automatización E2E en CI/CD

La automatización permite ejecutar pruebas E2E de forma repetible durante el ciclo de desarrollo.

Un pipeline puede seguir este flujo:

1. Obtener el código.
2. Instalar dependencias.
3. Compilar la aplicación.
4. Ejecutar pruebas unitarias.
5. Ejecutar pruebas de integración.
6. Construir y desplegar la aplicación en un entorno de pruebas.
7. Preparar los datos necesarios.
8. Ejecutar las pruebas E2E.
9. Publicar los informes y evidencias.
10. Bloquear la promoción si falla un criterio obligatorio.

### Ejemplo conceptual con GitHub Actions y Playwright

```yaml
name: E2E Tests

on:
  pull_request:
  workflow_dispatch:

jobs:
  e2e:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm

      - name: Install dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps

      - name: Run E2E tests
        run: npx playwright test
        env:
          BASE_URL: ${{ vars.TEST_BASE_URL }}

      - name: Upload test report
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: playwright-report
          path: playwright-report/
          if-no-files-found: ignore
```

Este ejemplo supone que la aplicación ya está desplegada en una URL accesible y que el proyecto tiene configurado Playwright. Si no es así, el pipeline debe incluir los pasos necesarios para iniciar la aplicación y preparar sus dependencias.

En un proyecto real, conviene revisar las versiones de las acciones, gestionar secretos de forma segura y configurar la retención de los informes.

### Buenas prácticas para CI/CD

- Ejecutar primero las pruebas rápidas.
- Reservar las pruebas E2E para los flujos de mayor valor.
- Preparar datos de prueba de manera reproducible.
- Evitar compartir usuarios y registros mutables entre pruebas paralelas.
- Conservar capturas, trazas e informes cuando se produzcan fallos.
- Ejecutar las pruebas contra una versión conocida de la aplicación.
- No ocultar defectos mediante reintentos excesivos.
- Evitar que las pruebas dependan de servicios externos inestables.
- Bloquear despliegues cuando fallen comprobaciones críticas definidas por el equipo.

---

## 15. El problema de las pruebas E2E inestables (*Flaky Tests*)

Una prueba *flaky* es aquella que, ejecutada repetidamente bajo condiciones aparentemente equivalentes, unas veces pasa y otras falla sin que exista necesariamente un cambio relevante en el código.

Las pruebas E2E son especialmente susceptibles a este problema porque interactúan con más componentes y dependen de tiempos, redes, datos y estado del sistema.

### Causas frecuentes

- Esperas fijas demasiado cortas.
- Selectores frágiles.
- Datos de prueba compartidos.
- Animaciones o actualizaciones asíncronas.
- Dependencias externas inestables.
- Orden de ejecución implícito.
- Entornos sobrecargados.
- Limpieza incompleta de datos.

### Ejemplo de mala práctica

```typescript
await page.click('#submit');

await page.waitForTimeout(3000);

await expect(
  page.locator('.success-message')
).toBeVisible();
```

La espera fija obliga a dormir tres segundos, independientemente de cuándo termine realmente la operación.

Puede ser insuficiente en un entorno lento y excesiva en uno rápido.

### Enfoque preferible

```typescript
await page.getByRole('button', {
  name: 'Enviar',
}).click();

await expect(
  page.getByRole('status')
).toContainText('Operación completada');
```

Las aserciones de Playwright pueden esperar automáticamente a que se cumpla la condición dentro del tiempo configurado.

La condición y el selector deben corresponder a la aplicación real.

### Cómo reducir la inestabilidad

1. Utilizar selectores accesibles y estables.
2. Esperar condiciones observables, no tiempos arbitrarios.
3. Aislar los datos de cada prueba.
4. Evitar dependencias innecesarias de servicios externos.
5. Recopilar trazas y capturas cuando se produzcan fallos.
6. Investigar las causas de las pruebas inestables.
7. Usar los reintentos como herramienta de diagnóstico, no como sustituto de una corrección.

---

## 16. Datos de prueba y aislamiento

Los datos son una parte esencial de la estabilidad de las pruebas E2E.

Por ejemplo, una prueba que crea un usuario con un correo electrónico fijo puede fallar cuando se ejecuta por segunda vez porque el usuario ya existe.

### Estrategias habituales

- Crear datos nuevos para cada ejecución.
- Utilizar identificadores únicos cuando el sistema lo permita.
- Preparar datos mediante APIs de prueba.
- Utilizar una base de datos aislada.
- Limpiar los registros generados al terminar.
- Restablecer el estado del entorno entre escenarios cuando sea viable.

### Consideraciones de seguridad

- No utilizar credenciales reales de usuarios.
- No almacenar secretos directamente en el código.
- Evitar utilizar datos personales reales innecesarios.
- Limitar los permisos de las cuentas de prueba.
- No ejecutar pruebas destructivas contra producción.
- Utilizar proveedores de pago y servicios externos de prueba cuando corresponda.

Una prueba E2E fiable debe poder repetirse sin depender del resultado de una ejecución anterior.

---

## 17. Qué debe probarse con E2E y qué no

Las pruebas E2E suelen ser más costosas que las pruebas unitarias, por lo que conviene seleccionar cuidadosamente los escenarios.

### Buenos candidatos para E2E

- Inicio y cierre de sesión.
- Registro y recuperación de cuenta.
- Flujos principales de compra.
- Operaciones críticas del negocio.
- Permisos y acceso a áreas protegidas.
- Procesos que atraviesan varios servicios.
- Flujos de pago mediante proveedores de prueba.
- Operaciones de alto riesgo para el usuario.

### Menos adecuados como única estrategia E2E

- Todas las combinaciones posibles de una función matemática.
- Cada regla de validación de bajo nivel.
- Cada rama de lógica de negocio.
- Cada variación de una consulta interna.
- Cada caso límite de una clase o método.

Estos escenarios suelen ser más fáciles de verificar con pruebas unitarias o de integración.

### Ejemplo

Si un formulario tiene diez reglas de validación, no es necesario crear diez flujos completos de navegador si esas reglas pueden comprobarse de forma fiable mediante pruebas unitarias y funcionales más pequeñas.

Puede bastar con:

- Pruebas unitarias para las reglas individuales.
- Pruebas de integración para validar la persistencia.
- Una prueba E2E para comprobar que el usuario completa correctamente el flujo principal.

---

## 18. Pirámide de testing y E2E

Una estrategia habitual consiste en mantener muchas pruebas unitarias, un conjunto intermedio de pruebas de integración y un conjunto más pequeño de pruebas E2E.

```text
              /\
             /  \
            / E2E\
           /------\
          /Integración\
         /------------\
        /  Unitarias   \
       /----------------\
```

### Pruebas unitarias

- Rápidas.
- Aisladas.
- Útiles para cubrir reglas y casos límite.
- Más sencillas de diagnosticar.

### Pruebas de integración

- Verifican la colaboración entre componentes.
- Detectan incompatibilidades de configuración o persistencia.
- Tienen un coste intermedio.

### Pruebas E2E

- Verifican flujos completos.
- Detectan problemas de integración global y comportamiento observable.
- Suelen tener mayor coste y más dependencias del entorno.

La pirámide es una orientación, no una ley universal. La distribución adecuada depende del tipo de aplicación, los riesgos, la arquitectura y las herramientas disponibles.

El objetivo no es maximizar el número de pruebas E2E, sino obtener una buena cobertura de riesgos con una suite estable, rápida y mantenible.

---

## 19. Errores frecuentes en pruebas E2E

### Probar demasiados escenarios con E2E

Aumenta el tiempo de ejecución y el coste de mantenimiento.

**Solución:** trasladar las reglas de bajo nivel a pruebas unitarias o de integración.

### Comprobar únicamente que una página aparece

Una página visible no demuestra que la operación haya terminado correctamente.

**Solución:** verificar el resultado de negocio, el estado relevante y los mensajes esperados.

### Utilizar selectores frágiles

Los selectores basados en la estructura interna del HTML pueden romperse tras cambios visuales.

**Solución:** preferir roles accesibles, etiquetas y atributos de prueba estables cuando sea necesario.

### Depender del orden de ejecución

Una prueba puede pasar únicamente porque otra ha preparado datos previamente.

**Solución:** hacer que cada escenario gestione sus propios datos y estado.

### Usar esperas fijas

Añaden tiempo innecesario y no garantizan que una operación haya terminado.

**Solución:** esperar condiciones observables mediante los mecanismos de la herramienta.

### Reintentar hasta conseguir un resultado satisfactorio

Puede ocultar defectos reales.

**Solución:** analizar la inestabilidad y corregir su causa.

### No conservar evidencias

Un fallo sin contexto puede ser difícil de reproducir.

**Solución:** conservar informes, capturas, trazas y logs pertinentes.

### Depender de servicios externos reales

Los cambios de disponibilidad, latencia o configuración pueden provocar fallos ajenos al código evaluado.

**Solución:** simular las dependencias que no forman parte del objetivo de la prueba y mantener verificaciones separadas para las integraciones reales que sí sean críticas.

---

## 20. Estrategia recomendada para implantar pruebas E2E

### Fase 1. Identificar los flujos críticos

Seleccionar los escenarios de mayor impacto para usuarios y negocio.

### Fase 2. Definir resultados verificables

Cada escenario debe especificar el estado inicial, las acciones relevantes y el resultado esperado.

### Fase 3. Elegir la herramienta

Valorar el stack tecnológico, la facilidad de mantenimiento, la integración con CI/CD y las necesidades de diagnóstico.

### Fase 4. Preparar el entorno

Asegurar que la aplicación, los servicios y los datos necesarios están disponibles.

### Fase 5. Automatizar pocos escenarios prioritarios

Empezar por los flujos más importantes y estabilizar la infraestructura de pruebas.

### Fase 6. Integrar las pruebas en CI/CD

Ejecutarlas en los momentos apropiados y conservar sus resultados.

### Fase 7. Monitorizar estabilidad y duración

Medir cuánto tarda la suite y cuántas veces se producen fallos transitorios o no reproducibles.

### Fase 8. Ampliar progresivamente

Añadir escenarios cuando aporten cobertura de riesgos que no esté bien cubierta por niveles inferiores.

### Fase 9. Revisar y mantener

Eliminar pruebas obsoletas, corregir las inestables y actualizar los escenarios cuando cambien los requisitos.

---

## 21. Checklist de revisión

Antes de considerar lista una suite E2E, comprueba:

- Los flujos críticos del negocio están identificados.
- Cada escenario tiene un resultado esperado explícito.
- Las pruebas cubren el recorrido relevante de extremo a extremo.
- El entorno de pruebas está disponible y documentado.
- Los datos se preparan de forma reproducible.
- Las pruebas no dependen de la ejecución previa de otros escenarios.
- Los selectores son estables y comprensibles.
- Las esperas se basan en condiciones observables.
- Se verifican resultados funcionales, no solo elementos visibles.
- Las dependencias externas están controladas.
- Los secretos y credenciales se gestionan de forma segura.
- Se generan informes y evidencias de fallo.
- Las pruebas se ejecutan en CI/CD.
- Los reintentos no ocultan defectos.
- Se revisan la duración y la estabilidad de la suite.
- Las reglas de bajo nivel se prueban en niveles más pequeños cuando resulta adecuado.

---

## 22. Conclusión

Las pruebas E2E permiten verificar que los flujos completos de una aplicación funcionan correctamente desde la perspectiva del usuario o del proceso de negocio.

Son especialmente valiosas cuando una operación atraviesa frontend, backend, bases de datos, APIs y servicios externos.

Una estrategia de calidad eficaz combina:

- Pruebas unitarias para verificar reglas aisladas.
- Pruebas de integración para comprobar la colaboración entre componentes.
- Pruebas funcionales para validar los requisitos.
- Pruebas E2E para verificar flujos completos.
- Pruebas de rendimiento para analizar latencia y capacidad.
- Análisis estático y revisión de código para mejorar mantenibilidad y prevenir determinados defectos.

La clave es seleccionar los escenarios que aportan más valor, preparar los datos de forma controlada, evitar dependencias frágiles y automatizar los flujos críticos.

**Una buena suite E2E no es la que tiene más pruebas, sino la que detecta errores relevantes de forma fiable, reproducible y mantenible.**

---

## 23. Referencias oficiales

- Playwright: https://playwright.dev/
- Playwright, buenas prácticas: https://playwright.dev/docs/best-practices
- Cypress: https://docs.cypress.io/
- Selenium: https://www.selenium.dev/documentation/
- Appium: https://appium.io/docs/en/latest/
- REST Assured: https://rest-assured.io/
- Cucumber y Gherkin: https://cucumber.io/docs/
- SonarQube: https://docs.sonarsource.com/sonarqube-server/
- GitHub Actions: https://docs.github.com/actions
