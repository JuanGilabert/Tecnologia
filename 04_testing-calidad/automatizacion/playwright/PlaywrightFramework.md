# Playwright: testing y calidad del código

## 1. Introducción

**Playwright** es un framework de automatización desarrollado por Microsoft que permite controlar navegadores y verificar el comportamiento de aplicaciones web.

Su ecosistema incluye **Playwright Test**, un ejecutor de pruebas que proporciona funcionalidades como:

- Definición y ejecución de tests.
- Aserciones con reintentos automáticos.
- Fixtures para gestionar el contexto de ejecución.
- Ejecución paralela.
- Pruebas en diferentes navegadores.
- Capturas de pantalla, vídeos y trazas.
- Informes HTML.
- Integración con sistemas CI/CD.
- Automatización de APIs HTTP.
- Pruebas de regresión visual.

Playwright permite automatizar Chromium, Firefox y WebKit, y dispone de mecanismos para simular dispositivos, tamaños de pantalla y determinadas condiciones de red.

Documentación oficial: https://playwright.dev/

### Objetivos principales

- Automatizar pruebas funcionales.
- Validar flujos completos de usuario.
- Detectar regresiones antes de desplegar.
- Comprobar la compatibilidad entre navegadores.
- Validar interacciones con formularios, botones, tablas y otros componentes.
- Comprobar contratos y respuestas de APIs.
- Facilitar el diagnóstico de fallos.
- Integrar las pruebas en los procesos de desarrollo y despliegue.

**Idea clave:** Playwright permite verificar que una aplicación web funciona como se espera desde la perspectiva del usuario, mientras que otras herramientas complementarias pueden analizar la calidad interna del código.

---

## 2. ¿Qué problema resuelve Playwright?

Una aplicación web puede compilar correctamente y superar las pruebas unitarias, pero seguir presentando errores funcionales.

Por ejemplo:

- El botón de inicio de sesión no responde.
- El formulario permite enviar datos inválidos.
- Una petición HTTP devuelve un error inesperado.
- Una actualización rompe el proceso de compra.
- Una interfaz funciona en Chromium, pero falla en WebKit.
- Una ventana modal no se cierra correctamente.
- Una operación se completa en el backend, pero la interfaz no actualiza su estado.

Playwright permite automatizar estos escenarios y ejecutarlos repetidamente.

### Beneficios

1. **Automatización:** reduce la necesidad de repetir comprobaciones manuales.
2. **Detección temprana:** ayuda a detectar regresiones antes del despliegue.
3. **Reproducibilidad:** permite ejecutar escenarios controlados de forma consistente.
4. **Compatibilidad:** facilita probar diferentes navegadores.
5. **Diagnóstico:** proporciona informes, capturas y trazas.
6. **Integración:** puede ejecutarse desde Jenkins, GitHub Actions y otros sistemas CI/CD.
7. **Mantenibilidad:** ofrece herramientas para crear tests reutilizables y estructurados.

### ¿Cuándo utilizarlo?

Es especialmente recomendable para aplicaciones web con flujos críticos, como autenticación, gestión de usuarios, formularios complejos, operaciones comerciales o procesos de compra.

---

## 3. Instalación y configuración

Esta guía utiliza principalmente **TypeScript con Node.js**, una combinación habitual para Playwright Test. También existe una API para Python y otros lenguajes.

Se recomienda disponer de una versión de Node.js compatible con la versión de Playwright que se vaya a instalar.

### Inicializar el proyecto

```bash
mkdir mi-proyecto-e2e
cd mi-proyecto-e2e
npm init playwright@latest
```

El asistente permite seleccionar opciones como el lenguaje, el directorio de pruebas y la configuración de CI.

También puede instalarse en un proyecto Node.js existente:

```bash
npm install --save-dev @playwright/test
npx playwright install
```

Para instalar únicamente Chromium en un entorno que no necesita los demás navegadores:

```bash
npx playwright install chromium
```

Comprobar la versión:

```bash
npx playwright --version
```

Es importante mantener alineadas las versiones de `@playwright/test` y los navegadores instalados, especialmente en CI.

### Scripts de `package.json`

Una configuración habitual es:

```json
{
  "scripts": {
    "test:e2e": "playwright test",
    "test:e2e:ui": "playwright test --ui",
    "test:e2e:report": "playwright show-report"
  }
}
```

Los scripts permiten ejecutar las pruebas de forma consistente desde el terminal y desde el pipeline.

---

## 4. Estructura recomendada del proyecto

Una estructura sencilla puede ser:

```text
mi-proyecto/
├── tests/
│   ├── login.spec.ts
│   ├── pedidos.spec.ts
│   └── api.spec.ts
├── pages/
│   ├── login.page.ts
│   └── pedidos.page.ts
├── fixtures/
│   └── test-fixtures.ts
├── utils/
│   └── test-data.ts
├── playwright.config.ts
├── package.json
├── package-lock.json
└── .gitignore
```

### Responsabilidades

- `tests/`: escenarios de prueba.
- `pages/`: objetos de página cuando ayudan a encapsular interacciones reutilizables.
- `fixtures/`: preparación de contextos, usuarios y recursos.
- `utils/`: utilidades auxiliares de testing.
- `playwright.config.ts`: configuración de ejecución.
- `package.json`: dependencias y scripts.
- `package-lock.json`: versiones resueltas de las dependencias.

No es necesario crear todas estas carpetas desde el principio. Para un proyecto pequeño, puede bastar con `tests/` y el archivo de configuración.

### Convención de nombres

Los archivos de prueba suelen terminar en `.spec.ts` o `.test.ts`, según el patrón configurado.

Por ejemplo:

- `login.spec.ts`
- `registro.spec.ts`
- `checkout.spec.ts`

Utilizar una convención uniforme facilita la localización y ejecución selectiva de las pruebas.

---

## 5. Anatomía de una prueba Playwright

Ejemplo básico:

```typescript
import { test, expect } from '@playwright/test';

test('la página principal muestra el título esperado', async ({ page }) => {
  await page.goto('https://playwright.dev/');

  await expect(page).toHaveTitle(/Playwright/);
});
```

### ¿Qué ocurre en el ejemplo?

1. `test` define un caso de prueba.
2. `page` es una fixture que proporciona una página del navegador.
3. `page.goto()` navega a una URL.
4. `expect()` comprueba una condición.
5. `toHaveTitle()` verifica el título de la página y reintenta la comprobación hasta que se cumpla o se agote el tiempo disponible.

Las operaciones asíncronas deben esperarse mediante `await`.

Documentación de aserciones: https://playwright.dev/docs/test-assertions

### Principio AAA

Una estructura útil para diseñar pruebas es **Arrange, Act, Assert**:

- **Arrange:** preparar el contexto y los datos.
- **Act:** ejecutar la acción que se quiere probar.
- **Assert:** comprobar el resultado.

Ejemplo:

```typescript
import { test, expect } from '@playwright/test';

test('el usuario puede abrir la documentación', async ({ page }) => {
  // Arrange
  const url = 'https://playwright.dev/';

  // Act
  await page.goto(url);

  // Assert
  await expect(
    page.getByRole('heading', { name: /playwright/i }).first()
  ).toBeVisible();
});
```

El patrón AAA ayuda a entender qué se prepara, qué se ejecuta y qué resultado se espera.

---

## 6. Localizadores y selección de elementos

Los localizadores determinan cómo encuentra Playwright los elementos de la interfaz.

Los más recomendables suelen representar cómo el usuario percibe o identifica el contenido.

### Prioridad habitual

1. Roles accesibles mediante `getByRole()`.
2. Etiquetas mediante `getByLabel()`.
3. Texto mediante `getByText()`.
4. Atributos de prueba mediante `getByTestId()`.
5. Selectores CSS o XPath cuando exista una razón concreta.

### Ejemplo

```typescript
import { test, expect } from '@playwright/test';

test('el usuario puede enviar un formulario', async ({ page }) => {
  await page.goto('https://mi-aplicacion.example/login');

  await page.getByLabel('Correo electrónico').fill('qa@example.com');
  await page.getByLabel('Contraseña').fill('clave-de-prueba');
  await page.getByRole('button', { name: 'Iniciar sesión' }).click();

  await expect(
    page.getByRole('heading', { name: 'Mi cuenta' })
  ).toBeVisible();
});
```

La URL y los selectores son ilustrativos y deben adaptarse a la aplicación real.

### ¿Por qué evitar selectores frágiles?

Un selector como:

```typescript
page.locator('div:nth-child(3) > div > button');
```

puede dejar de funcionar si cambia la estructura HTML, aunque la funcionalidad siga siendo correcta.

Un selector semántico como:

```typescript
page.getByRole('button', { name: 'Iniciar sesión' });
```

suele expresar mejor la intención.

### Cuándo utilizar `data-testid`

Cuando un elemento no tiene un nombre accesible estable o se necesita un identificador específico para las pruebas, puede utilizarse un atributo como `data-testid`.

```typescript
await page.getByTestId('submit-order').click();
```

Los atributos de prueba deben mantenerse estables y utilizarse de forma coherente.

---

## 7. Aserciones y sincronización

Una causa habitual de pruebas inestables es comprobar una condición antes de que la aplicación termine de actualizarse.

Playwright proporciona aserciones que vuelven a comprobar la condición hasta que se cumple o se alcanza el tiempo límite.

### Ejemplo recomendado

```typescript
await expect(
  page.getByTestId('order-status')
).toHaveText('Confirmado');
```

### Ejemplo menos recomendable

```typescript
await page.waitForTimeout(5000);

const status = await page.getByTestId('order-status').textContent();

expect(status).toBe('Confirmado');
```

La segunda opción espera siempre cinco segundos y puede fallar si el sistema tarda más o perder tiempo cuando responde antes.

### Aserciones habituales

```typescript
await expect(page.getByRole('button', { name: 'Guardar' }))
  .toBeEnabled();

await expect(page.getByText('Operación completada'))
  .toBeVisible();

await expect(page.getByTestId('total'))
  .toHaveText('100,00 €');

await expect(page)
  .toHaveURL(/\/dashboard/);
```

**Buena práctica:** preferir las aserciones específicas de Playwright frente a lecturas puntuales del DOM seguidas de comprobaciones que no vuelven a intentarse automáticamente.

Los tiempos de espera deben ajustarse con criterio; aumentar todos los timeouts no resuelve una mala sincronización ni una aplicación defectuosa.

---

## 8. Fixtures y preparación del entorno

Las fixtures permiten preparar los recursos que necesitan las pruebas y gestionar su ciclo de vida.

Playwright incluye fixtures como:

- `page`: página del navegador.
- `context`: contexto aislado del navegador.
- `browser`: instancia del navegador.
- `request`: contexto para solicitudes HTTP.

Ejemplo:

```typescript
import { test, expect } from '@playwright/test';

test('la aplicación carga correctamente', async ({ page }) => {
  await page.goto('https://playwright.dev/');

  await expect(page.locator('body')).toBeVisible();
});
```

Playwright se encarga de proporcionar y gestionar la fixture `page` para la prueba.

### ¿Por qué son importantes?

Las fixtures ayudan a:

- Evitar la duplicación de preparación.
- Crear recursos específicos para cada prueba.
- Gestionar autenticación y datos.
- Encapsular configuraciones comunes.
- Reducir dependencias entre escenarios.

### Fixtures personalizadas

Si varias pruebas necesitan una preparación común, puede crearse una fixture propia.

```typescript
import { test as base, expect } from '@playwright/test';

type Fixtures = {
  baseUrl: string;
};

export const test = base.extend<Fixtures>({
  baseUrl: async ({}, use) => {
    const url = process.env.BASE_URL ?? 'http://127.0.0.1:3000';

    await use(url);
  },
});

export { expect };
```

Después se puede utilizar:

```typescript
import { test, expect } from '../fixtures/test-fixtures';

test('la página principal responde', async ({ page, baseUrl }) => {
  await page.goto(baseUrl);

  await expect(page.locator('body')).toBeVisible();
});
```

La fixture centraliza la URL del entorno y permite modificarla mediante una variable de entorno.

---

## 9. Page Object Model

El patrón **Page Object Model (POM)** encapsula los elementos e interacciones de una página en una clase o módulo.

Es útil cuando hay múltiples pruebas que comparten los mismos flujos de interacción.

### Ejemplo de Page Object

Archivo `pages/login.page.ts`:

```typescript
import { type Page, expect } from '@playwright/test';

export class LoginPage {
  constructor(private readonly page: Page) {}

  async open() {
    await this.page.goto('/login');
  }

  async login(email: string, password: string) {
    await this.page.getByLabel('Correo electrónico').fill(email);
    await this.page.getByLabel('Contraseña').fill(password);
    await this.page.getByRole('button', { name: 'Iniciar sesión' }).click();
  }

  async expectLoginSuccess() {
    await expect(
      this.page.getByRole('heading', { name: 'Mi cuenta' })
    ).toBeVisible();
  }
}
```

Prueba:

```typescript
import { test } from '@playwright/test';
import { LoginPage } from '../pages/login.page';

test('un usuario válido puede iniciar sesión', async ({ page }) => {
  const loginPage = new LoginPage(page);

  await loginPage.open();
  await loginPage.login('qa@example.com', 'clave-de-prueba');
  await loginPage.expectLoginSuccess();
});
```

Para utilizar `page.goto('/login')` con una URL relativa, hay que configurar `baseURL` en `playwright.config.ts`.

### Ventajas del POM

- Centraliza selectores.
- Reutiliza interacciones.
- Reduce duplicación.
- Facilita cambios en la interfaz.
- Separa los detalles de interacción de los escenarios de negocio.

### Riesgos

- Crear clases enormes que representen toda la aplicación.
- Encapsular lógica de negocio que debería comprobarse en otro nivel.
- Crear abstracciones innecesarias para páginas sencillas.
- Ocultar demasiados detalles y dificultar la comprensión de las pruebas.

El POM es una herramienta de organización, no una obligación. Para escenarios simples, los localizadores directos pueden ser suficientes.

---

## 10. Pruebas de APIs

Playwright Test incorpora capacidades para enviar peticiones HTTP mediante un contexto de API.

Esto permite validar endpoints sin necesidad de interactuar siempre con la interfaz.

### Ejemplo

```typescript
import { test, expect } from '@playwright/test';

test('la API devuelve un recurso existente', async ({ request }) => {
  const response = await request.get(
    'https://jsonplaceholder.typicode.com/posts/1'
  );

  expect(response.status()).toBe(200);

  const body = await response.json();

  expect(body.id).toBe(1);
  expect(body.title).toBeTruthy();
});
```

El ejemplo utiliza un servicio público de demostración. Para pipelines fiables, es preferible utilizar un entorno controlado o una API propia.

### Qué validar

- Códigos de estado HTTP.
- Estructura y tipos del JSON.
- Valores importantes.
- Autenticación y autorización.
- Validación de entradas.
- Respuestas ante errores.
- Contratos entre servicios.
- Comportamiento ante datos inexistentes.

### Pruebas funcionales combinadas

Una prueba también puede crear un recurso mediante la API y comprobarlo después en la interfaz. Esto puede acelerar la preparación del escenario.

Sin embargo, hay que elegir cuidadosamente dónde validar el comportamiento: si el objetivo es probar el formulario de creación, la prueba debe crear el recurso desde la interfaz y no saltarse precisamente esa funcionalidad.

---

## 11. Pruebas de regresión visual

Playwright permite capturar capturas de pantalla y comparar imágenes para detectar cambios visuales.

Ejemplo:

```typescript
import { test, expect } from '@playwright/test';

test('la página mantiene su apariencia esperada', async ({ page }) => {
  await page.goto('/');

  await expect(page).toHaveScreenshot('home-page.png');
});
```

Esta prueba requiere una configuración adecuada de `baseURL` y una captura de referencia inicial.

### Casos de uso

- Detectar cambios inesperados de estilos.
- Comprobar componentes visuales.
- Validar páginas críticas.
- Revisar diferencias entre versiones.
- Proteger diseños de regresiones accidentales.

### Precauciones

Las imágenes pueden variar según el sistema operativo, las fuentes, el navegador y el entorno de ejecución.

Para reducir falsos positivos:

- Utilizar entornos reproducibles.
- Esperar a que la interfaz esté en un estado estable.
- Controlar animaciones y contenido dinámico cuando corresponda.
- Evitar incluir datos que cambien en cada ejecución.
- Revisar las diferencias antes de actualizar las capturas de referencia.

No se deben aceptar automáticamente todas las nuevas capturas sin revisar si el cambio visual es correcto.

---

## 12. Pruebas de accesibilidad

Playwright permite comprobar aspectos de accesibilidad mediante aserciones y herramientas complementarias.

Por ejemplo, utilizar `getByRole()` y `getByLabel()` fomenta la creación de interfaces con roles y etiquetas accesibles.

No obstante, usar localizadores accesibles no demuestra por sí solo que toda la aplicación cumpla los requisitos de accesibilidad.

Se pueden incorporar herramientas especializadas, como axe-core, para realizar comprobaciones automáticas complementarias.

Las pruebas automatizadas pueden detectar determinados problemas, pero no sustituyen la evaluación manual ni las pruebas con tecnologías de asistencia cuando estas sean necesarias.

---

## 13. Configuración central: `playwright.config.ts`

El archivo de configuración permite establecer las opciones comunes para toda la suite.

Ejemplo:

```typescript
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  testDir: './tests',

  fullyParallel: true,

  forbidOnly: Boolean(process.env.CI),

  retries: process.env.CI ? 1 : 0,

  workers: process.env.CI ? 1 : undefined,

  reporter: [
    ['list'],
    ['html', { open: 'never' }],
  ],

  use: {
    baseURL: process.env.BASE_URL ?? 'http://127.0.0.1:3000',

    trace: 'on-first-retry',

    screenshot: 'only-on-failure',

    video: 'retain-on-failure',
  },

  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] },
    },
    {
      name: 'webkit',
      use: { ...devices['Desktop Safari'] },
    },
  ],
});
```

### Consideraciones

- `testDir` define el directorio de pruebas.
- `forbidOnly` evita que se suban accidentalmente tests marcados con `test.only` en CI.
- `retries` configura reintentos.
- `workers` limita la concurrencia.
- `reporter` define los informes generados.
- `baseURL` centraliza la dirección de la aplicación.
- `trace`, `screenshot` y `video` ayudan a investigar fallos.
- `projects` permite ejecutar las pruebas con diferentes navegadores.

Los reintentos sirven para recopilar información sobre fallos intermitentes, pero no deben ocultar pruebas inestables.

La ejecución paralela puede reducir el tiempo total, aunque conviene comprobar que los tests son independientes antes de aumentar el número de workers.

Documentación sobre proyectos: https://playwright.dev/docs/test-projects

---

## 14. Ejecución y depuración

### Ejecutar todas las pruebas

```bash
npx playwright test
```

### Ejecutar un archivo

```bash
npx playwright test tests/login.spec.ts
```

### Ejecutar por nombre

```bash
npx playwright test -g "iniciar sesión"
```

### Abrir el informe HTML

```bash
npx playwright show-report
```

### Ejecutar con navegador visible

```bash
npx playwright test --headed
```

### Abrir el modo interactivo

```bash
npx playwright test --ui
```

### Depurar

```bash
npx playwright test --debug
```

### Grabar una traza

```bash
npx playwright test --trace on
```

El modo interactivo y el inspector permiten avanzar paso a paso, observar las interacciones y examinar los localizadores.

### Trace Viewer

Las trazas pueden incluir:

- Acciones ejecutadas.
- Capturas y estados del DOM.
- Errores.
- Información de red.
- Duraciones y secuencia de ejecución.

Son especialmente útiles para investigar diferencias entre una ejecución local y una ejecución en CI.

Documentación: https://playwright.dev/docs/trace-viewer-intro

---

## 15. Testing y calidad del código

Playwright comprueba el comportamiento observable de la aplicación, pero no reemplaza las herramientas de análisis estático.

Para mejorar la calidad del código conviene combinar varios mecanismos.

| Herramienta | Responsabilidad |
|---|---|
| Playwright Test | Pruebas funcionales y E2E |
| TypeScript | Tipado estático, si se utiliza |
| ESLint | Análisis estático y reglas de calidad para JavaScript/TypeScript |
| Prettier | Formato consistente |
| SonarQube | Análisis de calidad según los lenguajes y la configuración disponibles |
| Jenkins | Orquestación de las validaciones CI/CD |

### Aspectos que se deben revisar

- Nombres claros para las pruebas.
- Evitar duplicación.
- Localizadores estables.
- Uso correcto de `await`.
- Aserciones relevantes.
- Separación de responsabilidades.
- Gestión segura de datos y credenciales.
- Control de pruebas inestables.
- Configuración reproducible.
- Dependencias actualizadas.

### Calidad y cobertura

La cantidad de pruebas o su porcentaje de éxito no representa por sí sola la calidad global.

Es importante diferenciar:

- **Cobertura funcional:** qué requisitos y flujos están comprobados.
- **Cobertura de código:** qué partes del código se ejecutan durante las pruebas.
- **Calidad estática:** problemas potenciales de mantenibilidad, errores o estilo.
- **Estabilidad:** consistencia de los resultados entre ejecuciones.

Playwright no genera automáticamente una medida completa de cobertura de código de la aplicación. Si se necesita esa métrica, hay que configurar instrumentación y herramientas compatibles con el stack tecnológico.

---

## 16. Buenas prácticas de diseño

### 16.1. Aplicar Clean Code

Los tests deben expresar con claridad qué comportamiento se verifica.

Evitar:

```typescript
test('test 1', async ({ page }) => {
  // ...
});
```

Preferir:

```typescript
test('un usuario autenticado puede consultar sus pedidos', async ({ page }) => {
  // ...
});
```

### 16.2. Aplicar DRY con criterio

Reutilizar fixtures y funciones cuando varias pruebas compartan preparación o acciones.

Sin embargo, no conviene ocultar cada interacción en una función genérica que haga más difícil comprender el escenario.

### 16.3. Aplicar KISS

Mantener los tests cortos, enfocados y con un propósito claro.

Una prueba debería fallar por una razón fácil de entender.

### 16.4. Aplicar SOLID

Cuando existan Page Objects, fixtures y bibliotecas propias, conviene diseñarlos con responsabilidades claras.

Por ejemplo, un objeto de página debería centrarse en las interacciones con la interfaz, mientras que las reglas de negocio complejas pueden pertenecer a otros componentes de la aplicación.

### 16.5. Aplicar YAGNI

No crear una arquitectura de testing compleja antes de que el proyecto lo necesite.

Comenzar con una estructura sencilla y extraer abstracciones cuando haya duplicación o problemas reales de mantenimiento.

---

## 17. Pruebas inestables (flaky tests)

Una prueba inestable puede pasar en unas ejecuciones y fallar en otras sin que haya cambiado el código relevante.

Causas frecuentes:

- Esperas insuficientes.
- Selectores ambiguos.
- Datos compartidos entre pruebas.
- Dependencias de orden.
- Servicios externos poco fiables.
- Animaciones y contenido dinámico.
- Competencia por recursos compartidos.
- Diferencias entre entornos.

### Cómo reducir la inestabilidad

- Preferir aserciones con reintentos automáticos.
- Evitar pausas fijas.
- Crear datos independientes por prueba.
- No depender del estado que deja otra prueba.
- Mantener versiones de dependencias controladas.
- Utilizar entornos reproducibles.
- Analizar las trazas de los fallos.
- Revisar los reintentos para identificar problemas recurrentes.

Un test que falla de forma intermitente no debe considerarse correcto únicamente porque pase después de un reintento. El objetivo es identificar y resolver la causa del fallo.

---

## 18. Seguridad en las pruebas

Las pruebas pueden interactuar con cuentas, tokens, datos personales y sistemas externos.

### Recomendaciones

- No guardar contraseñas reales en el repositorio.
- Gestionar secretos mediante variables protegidas o un gestor de secretos.
- Evitar imprimir tokens en los logs.
- Utilizar usuarios específicos para testing.
- Limitar los permisos de las cuentas automatizadas.
- Evitar que las pruebas modifiquen datos de producción.
- Controlar el acceso a capturas, vídeos y trazas.
- Revisar los datos sensibles antes de conservar artefactos.

Las capturas y trazas pueden incluir información que normalmente no aparece en los logs de texto. Deben tratarse como artefactos potencialmente sensibles.

---

## 19. Integración con Jenkins y CI/CD

Playwright puede ejecutarse desde un pipeline de Jenkins mediante comandos de Node.js.

Un flujo típico es:

1. Obtener el código.
2. Instalar dependencias.
3. Instalar los navegadores necesarios.
4. Ejecutar análisis estático.
5. Ejecutar pruebas E2E.
6. Publicar informes y evidencias.
7. Bloquear el avance si fallan las comprobaciones obligatorias.

### Ejemplo de `Jenkinsfile`

```groovy
pipeline {
    agent any

    environment {
        CI = 'true'
        BASE_URL = 'http://127.0.0.1:3000'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Instalar dependencias') {
            steps {
                sh 'npm ci'
            }
        }

        stage('Instalar navegadores') {
            steps {
                sh 'npx playwright install --with-deps chromium'
            }
        }

        stage('Análisis estático') {
            steps {
                sh 'npm run lint'
            }
        }

        stage('Pruebas E2E') {
            steps {
                sh 'npx playwright test'
            }
        }
    }

    post {
        always {
            archiveArtifacts(
                artifacts: 'playwright-report/**,test-results/**',
                allowEmptyArchive: true
            )
        }
    }
}
```

### Requisitos del ejemplo

- Debe existir un script `lint` en `package.json`.
- El agente debe disponer de Node.js y de las dependencias necesarias.
- La aplicación debe estar levantada y accesible en `BASE_URL`.
- La configuración debe generar el informe en `playwright-report/`.
- El pipeline debe disponer de permisos adecuados para conservar artefactos.

Si la aplicación se ejecuta en otro contenedor o servicio, habrá que ajustar `BASE_URL` y la configuración de red.

El ejemplo instala Chromium porque utiliza un solo navegador en CI. Si la suite utiliza Firefox o WebKit, también habrá que instalarlos.

### Publicación de informes

Playwright genera un informe HTML que se puede conservar como artefacto. Jenkins puede publicar estos artefactos o configurarse con un plugin compatible para mostrar los informes de forma más integrada.

También es recomendable conservar los resultados de las pruebas fallidas y las trazas necesarias para diagnosticar problemas.

Documentación oficial de CI: https://playwright.dev/docs/ci

---

## 20. SonarQube y Playwright

SonarQube puede complementar las pruebas automatizadas mediante análisis estático de los lenguajes y archivos compatibles con la configuración del proyecto.

En un pipeline, una estrategia habitual es:

1. Ejecutar ESLint y otras comprobaciones estáticas.
2. Ejecutar pruebas unitarias.
3. Ejecutar Playwright.
4. Generar los informes de cobertura si el stack dispone de ellos.
5. Ejecutar o completar el análisis de SonarQube.
6. Evaluar el Quality Gate.
7. Permitir o bloquear el despliegue según los criterios definidos.

### Distinción importante

Playwright no sustituye a SonarQube y SonarQube no sustituye a Playwright.

- Playwright comprueba comportamientos.
- ESLint identifica problemas según sus reglas.
- TypeScript detecta determinadas incompatibilidades de tipos.
- SonarQube analiza aspectos de calidad dentro de sus capacidades y configuración.
- Jenkins coordina el proceso.

Para que SonarQube evalúe cobertura o resultados de pruebas, hay que configurar los informes correspondientes y su importación. Ejecutar Playwright no implica que la cobertura de código se calcule automáticamente.

---

## 21. Errores frecuentes

### Utilizar selectores frágiles

**Problema:** las pruebas fallan cuando cambia la estructura del HTML.

**Solución:** priorizar roles accesibles, etiquetas y atributos de prueba estables.

### Utilizar esperas fijas

**Problema:** las pruebas son lentas y continúan siendo inestables.

**Solución:** usar aserciones con reintentos y condiciones explícitas.

### Crear pruebas demasiado grandes

**Problema:** resulta difícil identificar qué comportamiento ha fallado.

**Solución:** dividir los escenarios según el comportamiento que se desea verificar.

### Compartir estado entre pruebas

**Problema:** el resultado depende del orden de ejecución.

**Solución:** preparar y limpiar los datos de forma independiente.

### Depender de servicios externos

**Problema:** una interrupción ajena a la aplicación provoca fallos.

**Solución:** utilizar entornos controlados y simulaciones cuando proceda.

### Abusar del POM

**Problema:** la capa de abstracción oculta el comportamiento de los tests.

**Solución:** crear objetos de página solo cuando aporten reutilización y claridad.

### Reintentar sin investigar

**Problema:** los fallos intermitentes se convierten en algo habitual.

**Solución:** analizar trazas, errores y causas raíz.

### Ejecutar todos los tests en todos los navegadores sin criterio

**Problema:** el pipeline se vuelve innecesariamente lento.

**Solución:** ejecutar una suite rápida en cada cambio y reservar la matriz completa de navegadores para los flujos y momentos adecuados.

---

## 22. Estrategia de implantación

### Fase 1. Identificar los flujos críticos

Seleccionar las operaciones con mayor impacto para usuarios y negocio.

### Fase 2. Automatizar los escenarios principales

Empezar con unos pocos flujos, como autenticación, navegación, formularios y operaciones críticas.

### Fase 3. Establecer convenciones

Definir nombres de tests, criterios de localización, gestión de datos y estructura de directorios.

### Fase 4. Crear abstracciones cuando aporten valor

Introducir fixtures o Page Objects para evitar duplicación y mejorar la legibilidad.

### Fase 5. Añadir pruebas de APIs

Comprobar contratos y errores sin utilizar un navegador cuando no sea necesario.

### Fase 6. Integrar el análisis estático

Añadir TypeScript, ESLint, Prettier y SonarQube según las necesidades del proyecto.

### Fase 7. Incorporar CI/CD

Ejecutar automáticamente las comprobaciones y publicar informes.

### Fase 8. Controlar estabilidad y tiempos

Medir duración, revisar pruebas inestables y ajustar el paralelismo.

### Fase 9. Ampliar la cobertura de forma selectiva

Incorporar más navegadores, pruebas visuales y escenarios de accesibilidad cuando aporten valor.

---

## 23. Checklist de calidad

- Los tests tienen nombres descriptivos.
- Cada test verifica un comportamiento comprensible.
- Se utiliza `await` correctamente.
- Los localizadores son estables y específicos.
- Se priorizan roles y etiquetas accesibles cuando corresponda.
- Se utilizan aserciones de Playwright con reintentos automáticos.
- Se evitan pausas fijas innecesarias.
- Los tests son independientes.
- Los datos se preparan de forma controlada.
- Se utiliza POM o fixtures cuando aportan valor.
- Los secretos se gestionan de forma segura.
- Se conservan informes y trazas para los fallos.
- Los tests críticos se ejecutan en CI/CD.
- El análisis estático se ejecuta automáticamente.
- Las pruebas inestables se investigan.
- La ejecución paralela no introduce conflictos.
- Se comprueba la compatibilidad con los navegadores relevantes.
- La cobertura de requisitos se revisa más allá del número de tests.
- Las capturas y trazas se gestionan según las políticas de seguridad.
- El pipeline tiene límites de tiempo y criterios claros de aceptación.

---

## 24. Conclusión

Playwright es una herramienta potente para automatizar pruebas funcionales, de regresión y E2E de aplicaciones web modernas.

Sus principales fortalezas son la automatización de navegadores, las aserciones con reintentos, la compatibilidad entre motores, las fixtures y las herramientas de diagnóstico.

Para construir una estrategia de calidad completa, se recomienda combinarlo con:

- **Playwright Test:** pruebas funcionales y E2E.
- **TypeScript:** comprobación estática de tipos, cuando se utilice.
- **ESLint:** reglas de calidad y detección de problemas en JavaScript/TypeScript.
- **Prettier:** formato consistente.
- **SonarQube:** análisis estático y seguimiento de indicadores de calidad.
- **Jenkins:** ejecución automatizada, publicación de resultados y control del pipeline.
- **Pruebas unitarias:** validación rápida de la lógica interna.
- **Pruebas de API:** comprobación de contratos y reglas del backend.

La recomendación es empezar por los flujos funcionales críticos, escribir pruebas legibles y estables y ejecutarlas automáticamente en CI/CD.

**La calidad de una suite Playwright no se mide solo por cuántos tests existen, sino por su capacidad para detectar regresiones reales, ofrecer resultados reproducibles y facilitar el mantenimiento del proyecto.**

---

## 25. Referencias oficiales

- Playwright: https://playwright.dev/
- Guía de buenas prácticas: https://playwright.dev/docs/best-practices
- Aserciones: https://playwright.dev/docs/test-assertions
- Fixtures: https://playwright.dev/docs/test-fixtures
- Page Object Model: https://playwright.dev/docs/pom
- Proyectos y navegadores: https://playwright.dev/docs/test-projects
- Pruebas de API: https://playwright.dev/docs/api-testing
- Trace Viewer: https://playwright.dev/docs/trace-viewer-intro
- Integración continua: https://playwright.dev/docs/ci
- SonarQube: https://docs.sonarsource.com/sonarqube-server/
