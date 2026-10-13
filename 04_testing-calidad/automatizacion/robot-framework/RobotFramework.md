# Robot Framework: testing y calidad del código

## 1. Introducción

**Robot Framework** es un framework de automatización de pruebas de código abierto que permite definir y ejecutar pruebas utilizando una sintaxis tabular y palabras clave legibles.

Su objetivo es facilitar la automatización de escenarios de prueba mediante una separación entre:

- La definición del comportamiento que se quiere comprobar.
- Las acciones que se ejecutan durante la prueba.
- Las bibliotecas que interactúan con los sistemas.
- Los resultados y evidencias de la ejecución.

Robot Framework se utiliza especialmente en pruebas de aceptación y pruebas funcionales, aunque su arquitectura extensible permite automatizar otros escenarios, como pruebas de APIs, procesos de integración y determinadas tareas de automatización.

Una de sus principales ventajas es que las pruebas pueden expresarse de manera comprensible para perfiles técnicos y funcionales.

### Objetivos principales

- Automatizar pruebas funcionales y de aceptación.
- Validar requisitos y reglas de negocio.
- Reducir las comprobaciones manuales repetitivas.
- Comprobar flujos completos de usuario.
- Reutilizar acciones y lógica mediante keywords.
- Integrarse con herramientas de automatización y CI/CD.
- Generar informes y evidencias de ejecución.
- Facilitar la colaboración entre desarrollo, QA y negocio.

**Idea clave:** Robot Framework permite describir comportamientos esperados mediante pruebas legibles y reutilizables, apoyándose en bibliotecas que ejecutan las acciones necesarias sobre la aplicación.

Documentación oficial: https://robotframework.org/

---

## 2. ¿Qué problema resuelve Robot Framework?

En muchos proyectos, los requisitos se describen mediante escenarios como:

- Un usuario debe poder iniciar sesión con credenciales válidas.
- Un cliente debe poder realizar un pedido.
- Una API debe devolver un código HTTP determinado.
- Un usuario sin permisos no debe poder acceder a una funcionalidad.
- Un proceso debe actualizar correctamente el estado de una operación.

Si estas comprobaciones se realizan manualmente, consumen tiempo y pueden ejecutarse de forma inconsistente.

Robot Framework permite convertir estos escenarios en pruebas automatizadas que pueden ejecutarse repetidamente durante el desarrollo y el despliegue.

### Beneficios principales

1. **Legibilidad:** las pruebas se expresan mediante keywords con nombres descriptivos.
2. **Reutilización:** las acciones comunes pueden encapsularse en keywords.
3. **Automatización:** las pruebas pueden ejecutarse localmente o en CI/CD.
4. **Trazabilidad:** los resultados permiten identificar pruebas fallidas y consultar evidencias.
5. **Extensibilidad:** puede integrarse con bibliotecas y código Python.
6. **Colaboración:** los escenarios pueden ser comprendidos por perfiles de QA, desarrollo y negocio.

### ¿Cuándo utilizarlo?

Robot Framework es especialmente útil cuando se necesita automatizar flujos funcionales completos, validar criterios de aceptación o mantener una suite de pruebas que pueda entenderse sin analizar grandes cantidades de código de programación.

---

## 3. Instalación y configuración

Robot Framework se instala mediante `pip`.

```bash
python -m pip install robotframework
```

Para comprobar la instalación:

```bash
robot --version
```

Es recomendable utilizar un entorno virtual para aislar las dependencias del proyecto.

```bash
python -m venv .venv
```

Activación en Linux o macOS:

```bash
source .venv/bin/activate
```

Activación en Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

Después se puede instalar Robot Framework dentro del entorno virtual.

```bash
python -m pip install robotframework
```

### Bibliotecas adicionales

Robot Framework proporciona el núcleo del framework, pero las acciones concretas dependen de las bibliotecas que se instalen.

| Biblioteca o herramienta | Utilidad |
|---|---|
| Robot Framework | Motor de ejecución de pruebas |
| SeleniumLibrary | Automatización de navegadores mediante Selenium |
| Browser | Automatización web basada en Playwright |
| RequestsLibrary | Automatización de pruebas HTTP y APIs |
| DatabaseLibrary | Interacción con bases de datos compatibles |
| pabot | Ejecución paralela de suites |
| Jenkins | Orquestación de CI/CD |
| SonarQube | Análisis de calidad del código compatible con la configuración del proyecto |

Por ejemplo, para automatizar una aplicación web mediante Playwright se puede instalar la biblioteca Browser:

```bash
python -m pip install robotframework-browser
```

Después hay que instalar los navegadores y dependencias requeridos por la versión de Browser instalada. El procedimiento concreto se encuentra en su documentación oficial.

Documentación: https://marketsquare.github.io/robotframework-browser/

Para automatizar APIs HTTP:

```bash
python -m pip install robotframework-requests
```

La selección de bibliotecas debe basarse en las tecnologías y necesidades del proyecto.

---

## 4. Estructura recomendada del proyecto

Una estructura sencilla para una aplicación web podría ser:

```text
mi_proyecto/
├── tests/
│   ├── acceptance/
│   │   ├── login.robot
│   │   └── pedidos.robot
│   ├── api/
│   │   └── usuarios_api.robot
│   └── resources/
│       ├── common.resource
│       ├── login.resource
│       └── pedidos.resource
├── libraries/
│   └── utilidades.py
├── results/
├── requirements.txt
└── README.md
```

### Responsabilidad de cada directorio

- `tests/acceptance/`: pruebas de aceptación y flujos funcionales.
- `tests/api/`: pruebas de APIs.
- `tests/resources/`: keywords, variables y configuraciones reutilizables.
- `libraries/`: bibliotecas personalizadas, por ejemplo, implementadas en Python.
- `results/`: ubicación habitual para los informes de ejecución, que normalmente conviene excluir del control de versiones.

La estructura puede adaptarse a la complejidad del proyecto. No es necesario crear todos los directorios desde el principio.

### Tipos de archivos habituales

Robot Framework utiliza principalmente archivos con estas extensiones:

- `.robot`: suites de pruebas y definiciones de keywords.
- `.resource`: recursos reutilizables.
- `.py`: bibliotecas personalizadas o código auxiliar en Python.
- Archivos de configuración y dependencias: por ejemplo, `requirements.txt` o archivos `pyproject.toml`.

---

## 5. Anatomía de un archivo Robot Framework

Los archivos `.robot` suelen organizarse mediante secciones.

| Sección | Finalidad |
|---|---|
| `Settings` | Importar bibliotecas y recursos, definir configuración y hooks |
| `Variables` | Declarar variables reutilizables |
| `Test Cases` | Definir las pruebas |
| `Tasks` | Definir tareas automatizadas, en usos orientados a tareas |
| `Keywords` | Definir keywords personalizados |

No todos los archivos necesitan todas las secciones.

### Ejemplo básico

Archivo `tests/acceptance/calculadora.robot`:

```robotframework
*** Test Cases ***
Sumar dos números
    ${resultado}=    Evaluate    2 + 3
    Should Be Equal As Integers    ${resultado}    5
```

Esta prueba ejecuta una operación y comprueba el resultado.

`Evaluate` permite evaluar una expresión Python en el contexto de Robot Framework. En un proyecto real, conviene utilizarlo con criterio y evitar evaluar expresiones dinámicas no confiables.

La prueba se ejecuta mediante:

```bash
robot tests/acceptance/calculadora.robot
```

Robot Framework descubre la prueba, ejecuta sus keywords y genera informes de la ejecución.

---

## 6. El enfoque Keyword-Driven Testing

El enfoque basado en keywords consiste en describir las acciones de una prueba mediante palabras clave que representan operaciones.

Por ejemplo:

```robotframework
*** Test Cases ***
Usuario inicia sesión correctamente
    Abrir página de inicio de sesión
    Introducir credenciales válidas
    Enviar formulario de acceso
    Verificar que el usuario ha iniciado sesión
```

Los nombres anteriores representan keywords que deben estar definidos en el proyecto o disponibles en alguna biblioteca importada.

Este estilo permite que el escenario describa el comportamiento esperado sin exponer todos los detalles técnicos.

### Ventajas

- Mejora la legibilidad.
- Permite reutilizar acciones comunes.
- Separa el flujo funcional de los detalles de implementación.
- Reduce la duplicación.
- Facilita que QA y desarrollo colaboren sobre los mismos escenarios.

### Riesgo: crear demasiadas abstracciones

Si cada línea se convierte en una keyword genérica sin un propósito claro, el proyecto puede resultar difícil de mantener.

Por ejemplo, keywords como `Ejecutar Acción 1` o `Procesar Elemento` no expresan adecuadamente la intención.

Es preferible utilizar nombres orientados al negocio, como `Crear pedido` o `Verificar estado del pedido`.

---

## 7. Primer ejemplo funcional de prueba web

Para automatizar una página web necesitamos una biblioteca de navegador.

Una alternativa es SeleniumLibrary:

```bash
python -m pip install robotframework-seleniumlibrary
```

Además, hay que disponer de un navegador y de un controlador compatible, o de una configuración de Selenium que gestione ese requisito.

### Ejemplo de prueba

```robotframework
*** Settings ***
Library    SeleniumLibrary

*** Test Cases ***
Usuario puede abrir la página de inicio
    Open Browser    https://example.com    chrome
    Title Should Be    Example Domain
    [Teardown]    Close Browser
```

La prueba:

1. Abre el navegador.
2. Navega a la página.
3. Comprueba el título.
4. Cierra el navegador al terminar, tanto si la prueba pasa como si falla.

Este es un ejemplo ilustrativo. La disponibilidad del navegador y la configuración de su controlador dependen del entorno de ejecución.

### Buenas prácticas

- Gestionar la limpieza mediante `Test Teardown` o `[Teardown]`.
- Evitar pausas fijas innecesarias.
- Utilizar esperas explícitas o mecanismos de espera de la biblioteca.
- Elegir selectores estables.
- No depender del orden de ejecución de otras pruebas.

Para aplicaciones web modernas, también puede considerarse Browser, basada en Playwright, según las necesidades del equipo.

---

## 8. Variables y reutilización de datos

Las variables permiten evitar la repetición de valores.

Ejemplo:

```robotframework
*** Variables ***
${BASE_URL}       https://example.com
${TIMEOUT}        10 seconds
${USUARIO_TEST}   usuario@example.com
```

Las variables pueden utilizarse en diferentes pruebas y keywords.

```robotframework
*** Settings ***
Library    SeleniumLibrary

*** Variables ***
${BASE_URL}    https://example.com

*** Test Cases ***
Abrir aplicación
    Open Browser    ${BASE_URL}    chrome
    [Teardown]    Close Browser
```

### Buenas prácticas

- Evitar repetir URLs, nombres de entorno y valores comunes.
- No guardar contraseñas reales directamente en los archivos.
- Mantener separadas las variables de entorno y los datos sensibles.
- Utilizar nombres descriptivos.
- Evitar variables globales que representen estados mutables difíciles de controlar.

Para credenciales, utilizar variables de entorno o mecanismos seguros de gestión de secretos del sistema de CI/CD.

---

## 9. Keywords personalizados

Los keywords personalizados permiten encapsular acciones que se repiten y darles un nombre significativo.

Ejemplo:

```robotframework
*** Settings ***
Library    SeleniumLibrary

*** Keywords ***
Abrir aplicación
    Open Browser    https://example.com    chrome

Verificar página principal
    Title Should Be    Example Domain

Cerrar aplicación
    Close Browser

*** Test Cases ***
Comprobar página principal
    Abrir aplicación
    Verificar página principal
    [Teardown]    Cerrar aplicación
```

En este ejemplo, las acciones del navegador se agrupan en keywords con nombres comprensibles.

### Cuándo crear un keyword

Es útil cuando:

- Representa una acción de negocio.
- Encapsula una secuencia repetida.
- Reduce la complejidad de una prueba.
- Aporta un nombre más expresivo que las operaciones de bajo nivel.

No conviene crear keywords únicamente para ocultar una sola instrucción trivial si eso dificulta la navegación por el código.

---

## 10. Recursos reutilizables mediante archivos `.resource`

Cuando los keywords se utilizan en diferentes suites, pueden trasladarse a archivos `.resource`.

Archivo `tests/resources/web.resource`:

```robotframework
*** Settings ***
Library    SeleniumLibrary

*** Variables ***
${BASE_URL}    https://example.com

*** Keywords ***
Abrir aplicación
    Open Browser    ${BASE_URL}    chrome

Cerrar aplicación
    Close Browser
```

Archivo `tests/acceptance/home.robot`:

```robotframework
*** Settings ***
Resource    ../resources/web.resource

*** Test Cases ***
La página principal se muestra correctamente
    Abrir aplicación
    Title Should Be    Example Domain
    [Teardown]    Cerrar aplicación
```

### Ventajas

- Reduce la duplicación.
- Centraliza operaciones comunes.
- Separa las pruebas de los detalles reutilizables.
- Facilita el mantenimiento de suites grandes.

### Recomendaciones

- Agrupar los recursos por responsabilidad.
- Evitar archivos de recursos gigantes.
- No introducir dependencias circulares entre recursos.
- Mantener los nombres de keywords consistentes.
- Evitar que las pruebas dependan de un estado oculto creado por otra suite.

---

## 11. Pruebas de APIs con Robot Framework

Robot Framework puede automatizar pruebas HTTP mediante RequestsLibrary.

Instalación:

```bash
python -m pip install robotframework-requests
```

### Ejemplo de prueba de una API

```robotframework
*** Settings ***
Library    RequestsLibrary

*** Test Cases ***
Consultar recurso existente
    ${respuesta}=    GET    https://jsonplaceholder.typicode.com/posts/1
    Should Be Equal As Integers    ${respuesta.status_code}    200
    Should Be Equal As Integers    ${respuesta.json()}[id]    1
```

Este ejemplo utiliza una API pública de demostración. Para una suite estable de producción, es preferible controlar el entorno y los datos de prueba.

La sintaxis concreta de las keywords HTTP puede variar entre versiones de RequestsLibrary. Se debe consultar la documentación correspondiente a la versión instalada.

### Qué se puede comprobar

- Código de estado HTTP.
- Estructura del JSON.
- Valores de los campos.
- Cabeceras.
- Reglas de validación.
- Autenticación y autorización.
- Comportamiento ante entradas inválidas.
- Contratos de API.

### Buenas prácticas

- Verificar más que el código HTTP.
- Comprobar los campos importantes del contrato.
- Evitar depender de servicios públicos externos en las pruebas obligatorias de CI.
- Utilizar datos controlados.
- No registrar tokens ni información sensible en los informes.
- Comprobar escenarios de error y permisos insuficientes.

---

## 12. Pruebas de aceptación y BDD

Robot Framework es especialmente útil para automatizar criterios de aceptación.

BDD, o *Behavior-Driven Development*, es una práctica que permite expresar el comportamiento esperado desde la perspectiva del negocio.

Aunque Robot Framework no obliga a utilizar BDD, su sintaxis puede ayudar a describir escenarios funcionales legibles.

Ejemplo:

```robotframework
*** Test Cases ***
Un cliente puede consultar su pedido
    Dado que existe un cliente autenticado
    Cuando consulta un pedido existente
    Entonces se muestran los detalles del pedido
```

Los pasos anteriores son keywords personalizados y necesitan una implementación.

### Relación con Given, When, Then

- **Given / Dado:** establece las condiciones iniciales.
- **When / Cuando:** ejecuta la acción.
- **Then / Entonces:** verifica el resultado.

### Ventajas

- Los escenarios describen requisitos.
- Las pruebas pueden revisarse con perfiles funcionales.
- Se facilita la trazabilidad entre requisitos y automatización.
- Se documenta el comportamiento esperado.

### Precaución

Utilizar palabras como Dado, Cuando y Entonces no convierte automáticamente una prueba en BDD.

La utilidad de BDD depende de que los escenarios representen comportamientos relevantes y formen parte de una colaboración real entre negocio, QA y desarrollo.

---

## 13. Pruebas unitarias, integración, funcionales y E2E

Robot Framework puede organizar diferentes niveles de pruebas, aunque no siempre es la mejor herramienta para comprobar directamente pequeñas unidades de código.

| Tipo | Objetivo | Uso habitual |
|---|---|---|
| Unitarias | Verificar funciones o componentes aislados | Pytest o unittest en Python |
| Integración | Comprobar la interacción entre componentes | Pytest, Robot Framework u otras herramientas |
| APIs | Verificar contratos y respuestas HTTP | Robot Framework con RequestsLibrary |
| Funcionales | Comprobar comportamientos de la aplicación | Robot Framework |
| Aceptación | Validar criterios de negocio | Robot Framework |
| E2E | Comprobar flujos completos | Robot Framework con bibliotecas de navegador |

### ¿Conviene utilizarlo para pruebas unitarias?

Puede utilizarse para ciertos casos, pero si se necesita probar directamente funciones Python, normalmente Pytest o unittest proporcionan un enfoque más natural.

Una estrategia equilibrada puede combinar:

- Pytest para pruebas unitarias de código Python.
- Robot Framework para pruebas funcionales y de aceptación.
- Bibliotecas HTTP para automatizar APIs.
- Browser o SeleniumLibrary para automatizar aplicaciones web.
- Jenkins para ejecutar y publicar los resultados.

---

## 14. Integración con CI/CD y Jenkins

Robot Framework puede ejecutarse desde un pipeline de Jenkins como cualquier otro comando disponible en el entorno.

Un pipeline puede:

1. Obtener el código.
2. Preparar Python y el entorno virtual.
3. Instalar las dependencias.
4. Ejecutar las pruebas.
5. Generar informes.
6. Publicar los resultados.
7. Aplicar criterios de aceptación.
8. Continuar con el proceso de despliegue si las validaciones obligatorias pasan.

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

        stage('Instalar dependencias') {
            steps {
                sh '''
                    python -m venv .venv
                    . .venv/bin/activate
                    python -m pip install --upgrade pip
                    python -m pip install -r requirements.txt
                '''
            }
        }

        stage('Ejecutar pruebas Robot Framework') {
            steps {
                sh '''
                    . .venv/bin/activate
                    robot \
                      --outputdir results \
                      tests/
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts(
                artifacts: 'results/**',
                allowEmptyArchive: true
            )
        }
    }
}
```

Este ejemplo presupone un agente Linux con Python y un archivo `requirements.txt` adecuado. Para ejecutar pruebas de navegador, el entorno debe contar además con los navegadores y dependencias necesarios.

### Informes generados

Robot Framework genera normalmente tres archivos principales:

- `output.xml`: resultados estructurados de la ejecución.
- `log.html`: registro detallado de las pruebas y keywords.
- `report.html`: resumen de los resultados.

Estos archivos se pueden conservar como artefactos del pipeline para facilitar el análisis de fallos.

### Mejoras recomendadas para producción

- Configurar la publicación de resultados y el acceso a los informes.
- Separar suites rápidas de pruebas E2E más costosas.
- Guardar las evidencias de forma controlada.
- Configurar alertas para fallos recurrentes.
- Gestionar credenciales mediante mecanismos seguros.
- Ejecutar las pruebas en entornos reproducibles.
- Hacer que los fallos críticos bloqueen el avance del pipeline.

La publicación de artefactos del ejemplo no configura por sí sola una integración de Jenkins con SonarQube ni establece un Quality Gate. Estas capacidades deben añadirse explícitamente.

---

## 15. Ejecución paralela

Cuando una suite crece, la duración de la ejecución puede convertirse en un problema.

Pabot es una herramienta del ecosistema Robot Framework que permite ejecutar suites en paralelo.

Instalación:

```bash
python -m pip install robotframework-pabot
```

Ejemplo:

```bash
pabot --processes 4 --outputdir results tests/
```

La ejecución paralela puede reducir el tiempo total, pero requiere que las pruebas sean suficientemente independientes.

### Riesgos que deben controlarse

- Dos pruebas modifican el mismo registro.
- Varias suites utilizan la misma cuenta de usuario.
- Las pruebas comparten un archivo temporal.
- Los escenarios compiten por un recurso exclusivo.
- El entorno no soporta la concurrencia esperada.

Antes de activar paralelismo, conviene eliminar las dependencias entre pruebas y aislar los datos y recursos compartidos.

---

## 16. Robot Framework y la calidad del código

Robot Framework es una herramienta de testing, no un sustituto de un analizador estático general.

Una estrategia de calidad debe considerar tanto las pruebas como el mantenimiento de los archivos `.robot`, las bibliotecas Python y la configuración del proyecto.

### Aspectos de calidad que conviene revisar

- Nombres de pruebas y keywords.
- Duplicación de pasos.
- Tamaño y complejidad de los keywords.
- Uso de variables.
- Manejo de errores y recursos.
- Independencia de las pruebas.
- Selectores web estables.
- Gestión de credenciales.
- Dependencias y versiones.
- Tiempo de ejecución y estabilidad.
- Facilidad para investigar fallos.

### Herramientas complementarias

| Herramienta | Finalidad |
|---|---|
| Robot Framework | Automatización de pruebas |
| Robocop | Análisis estático y reglas de estilo para archivos Robot Framework |
| Ruff | Calidad estática del código Python |
| Pytest | Pruebas unitarias y de integración de Python |
| SonarQube | Análisis de calidad según las capacidades y configuración disponibles |
| Jenkins | Automatización y orquestación del proceso |

Robocop puede ayudar a detectar problemas de estilo, convenciones y mantenibilidad en los archivos de Robot Framework.

Instalación:

```bash
python -m pip install robotframework-robocop
```

Ejecución:

```bash
robocop check tests/
```

Los comandos y reglas concretos dependen de la versión instalada. Conviene fijar una versión y consultar su documentación.

Documentación: https://robocop.readthedocs.io/

---

## 17. Buenas prácticas de diseño

### 17.1. Aplicar Clean Code

Las pruebas también son código y deben poder leerse, comprenderse y mantenerse.

Un escenario debería explicar qué comportamiento se comprueba y por qué.

Evitar nombres genéricos como:

```robotframework
Ejecutar operación
Comprobar resultado
```

Cuando no expresan la intención real.

Preferir nombres específicos, por ejemplo:

```robotframework
Crear pedido con productos disponibles
Verificar que el pedido queda confirmado
```

### 17.2. Evitar duplicación con DRY

Si varias pruebas repiten los mismos pasos, se pueden encapsular en keywords reutilizables.

No obstante, DRY no significa crear una abstracción para cada línea. La reutilización debe reducir la complejidad, no aumentarla.

### 17.3. Aplicar KISS

Mantener los escenarios simples.

Una prueba funcional no debería necesitar una cadena de decenas de keywords genéricos para realizar una comprobación sencilla.

### 17.4. Aplicar SOLID a bibliotecas personalizadas

Si el proyecto utiliza bibliotecas Python propias, los principios SOLID ayudan a diseñar componentes mantenibles:

- **Responsabilidad única:** cada biblioteca debe tener una finalidad coherente.
- **Abierto/cerrado:** permitir ampliar comportamientos sin modificar indiscriminadamente componentes estables.
- **Sustitución de Liskov:** respetar los contratos de las abstracciones.
- **Segregación de interfaces:** evitar interfaces innecesariamente grandes.
- **Inversión de dependencias:** depender de abstracciones cuando aporte valor.

Estos principios son especialmente relevantes para las bibliotecas Python que implementan operaciones complejas o integraciones externas.

### 17.5. Aplicar YAGNI

No crear frameworks internos, capas de abstracción o bibliotecas genéricas hasta que exista una necesidad real.

La arquitectura de automatización debe crecer con los requisitos del proyecto.

---

## 18. Pruebas inestables o flaky tests

Una prueba inestable puede pasar en una ejecución y fallar en otra sin que exista necesariamente un defecto en la funcionalidad probada.

En Robot Framework, las causas habituales incluyen:

- Esperas insuficientes.
- Selectores inestables.
- Datos compartidos.
- Dependencia del orden.
- Servicios externos poco fiables.
- Condiciones de carrera.
- Diferencias entre navegadores o entornos.

### Cómo reducirlas

- Utilizar esperas basadas en condiciones.
- Evitar pausas fijas como solución general.
- Aislar los datos de cada escenario.
- Asegurar la limpieza de recursos.
- Utilizar selectores robustos.
- Analizar logs y evidencias.
- Repetir una prueba fallida como diagnóstico, sin ocultar el fallo original.

Los reintentos pueden ayudar a diagnosticar fallos transitorios, pero no deben sustituir la corrección de la causa raíz.

---

## 19. Informes y métricas de calidad

Los informes de Robot Framework permiten consultar el resultado de cada prueba y analizar la secuencia de keywords ejecutados.

Para evaluar la calidad de una suite es recomendable observar más indicadores que el porcentaje de pruebas satisfactorias.

### Métricas útiles

- Porcentaje de pruebas satisfactorias.
- Número de pruebas fallidas.
- Número de pruebas omitidas.
- Duración total de la suite.
- Duración por suite o caso.
- Frecuencia de pruebas inestables.
- Tiempo medio de diagnóstico.
- Cobertura de requisitos críticos.
- Frecuencia de defectos detectados después del despliegue.

### Precaución con las métricas

Un 100 % de pruebas satisfactorias no demuestra que una aplicación esté libre de defectos. Puede significar que los escenarios automatizados pasan, mientras otros comportamientos no están cubiertos.

Del mismo modo, un número elevado de pruebas no garantiza una buena estrategia de testing si existen duplicaciones, escenarios poco relevantes o verificaciones débiles.

---

## 20. Seguridad y gestión de datos de prueba

Los sistemas de automatización pueden acceder a información sensible si no se controlan adecuadamente.

### Recomendaciones

- No incluir contraseñas reales en archivos `.robot`.
- Utilizar secretos gestionados por CI/CD.
- Evitar imprimir tokens en logs.
- No almacenar datos personales reales sin una justificación y controles adecuados.
- Utilizar cuentas específicas para pruebas.
- Limitar los permisos de las credenciales.
- Revisar qué información se conserva en capturas e informes.
- Asegurar que los entornos de prueba no afectan a producción.

Los informes HTML y las capturas de pantalla pueden contener información sensible. Deben tratarse como artefactos que necesitan controles de acceso y políticas de conservación.

---

## 21. Errores frecuentes

### Crear keywords demasiado genéricos

Dificultan comprender el propósito del escenario.

**Solución:** utilizar nombres que expresen acciones de negocio y responsabilidades claras.

### Repetir la misma lógica en múltiples suites

Aumenta el coste de mantenimiento.

**Solución:** trasladar acciones realmente compartidas a recursos reutilizables.

### Usar esperas fijas en pruebas web

Las pruebas se vuelven lentas o inestables.

**Solución:** utilizar las esperas de la biblioteca de automatización y comprobar condiciones concretas.

### Depender de servicios públicos externos

Las interrupciones externas pueden bloquear el pipeline.

**Solución:** utilizar entornos controlados o servicios simulados cuando el objetivo de la prueba no sea comprobar esa dependencia real.

### Automatizar todos los escenarios mediante E2E

Aumenta el tiempo de ejecución y el coste de diagnóstico.

**Solución:** distribuir las comprobaciones entre pruebas unitarias, integración, APIs y E2E según el objetivo.

### Ignorar la mantenibilidad de las pruebas

La suite puede convertirse en una fuente de trabajo manual.

**Solución:** revisar periódicamente duplicaciones, estabilidad, estructura y utilidad de cada escenario.

---

## 22. Estrategia recomendada de implantación

### Fase 1. Identificar los flujos críticos

Seleccionar las funcionalidades que más impacto tienen para el usuario o el negocio.

### Fase 2. Automatizar escenarios funcionales prioritarios

Empezar por unos pocos flujos que aporten valor, como autenticación, creación de pedidos o consulta de información.

### Fase 3. Crear keywords reutilizables

Extraer acciones comunes cuando mejoren la legibilidad y reduzcan duplicaciones.

### Fase 4. Añadir pruebas de API

Comprobar contratos y reglas de negocio sin necesidad de utilizar un navegador en todos los escenarios.

### Fase 5. Incorporar análisis estático

Utilizar Robocop para los archivos Robot Framework y herramientas adecuadas para las bibliotecas Python.

### Fase 6. Integrar en CI/CD

Ejecutar las suites y publicar los informes mediante Jenkins u otra plataforma.

### Fase 7. Controlar estabilidad y rendimiento

Investigar pruebas inestables, reducir dependencias externas y optimizar las suites lentas.

### Fase 8. Mantener la automatización

Revisar los escenarios junto con la evolución de los requisitos y retirar los que hayan dejado de aportar valor.

---

## 23. Checklist de calidad para Robot Framework

- Las pruebas están organizadas por funcionalidad o nivel.
- Los nombres describen comportamientos de negocio.
- Los keywords tienen responsabilidades claras.
- La lógica repetida se reutiliza cuando aporta valor.
- Las pruebas son independientes.
- Los recursos se preparan y liberan correctamente.
- Los datos de prueba están controlados.
- Las credenciales se gestionan de forma segura.
- Los selectores web son robustos.
- Las esperas se basan en condiciones cuando corresponde.
- Los fallos pueden investigarse mediante logs e informes.
- Se ejecuta análisis estático con herramientas adecuadas.
- Las pruebas críticas se ejecutan en CI/CD.
- Los resultados se publican y conservan según la política del proyecto.
- Las pruebas inestables se investigan.
- Las suites tienen una duración compatible con el flujo de desarrollo.
- Los criterios de aceptación están relacionados con los requisitos.
- Las pruebas E2E se reservan para los flujos donde aportan más valor.

---

## 24. Conclusión

Robot Framework es una herramienta útil para automatizar pruebas funcionales, de aceptación, APIs y flujos E2E.

Sus principales ventajas son la legibilidad de los escenarios, la reutilización mediante keywords, la extensibilidad mediante bibliotecas y la generación de informes de ejecución.

Para construir una estrategia de calidad completa, conviene combinarlo con herramientas complementarias:

- **Pytest:** pruebas unitarias e integración de código Python.
- **Robot Framework:** pruebas funcionales y de aceptación.
- **Browser o SeleniumLibrary:** automatización de navegadores.
- **RequestsLibrary:** pruebas HTTP y APIs.
- **Robocop:** análisis estático de archivos Robot Framework.
- **Ruff:** análisis estático de bibliotecas Python.
- **SonarQube:** análisis de calidad según la configuración del proyecto.
- **Jenkins:** automatización y publicación de resultados.

La recomendación es empezar con los flujos de negocio más importantes, mantener los keywords simples y expresivos y ejecutar las pruebas automáticamente dentro del proceso CI/CD.

**La calidad de una suite Robot Framework depende de que sus escenarios sean relevantes, estables, comprensibles y capaces de detectar defectos reales, no únicamente de la cantidad de pruebas automatizadas.**

---

## 25. Referencias oficiales

- Robot Framework: https://robotframework.org/
- Documentación oficial: https://robotframework.org/robotframework/
- Guía de usuario: https://robotframework.org/robotframework/latest/RobotFrameworkUserGuide.html
- SeleniumLibrary: https://robotframework.org/SeleniumLibrary/
- Browser Library: https://marketsquare.github.io/robotframework-browser/
- RequestsLibrary: https://github.com/MarketSquare/robotframework-requests
- Robocop: https://robocop.readthedocs.io/
- Pabot: https://github.com/mkorpela/pabot
- Jenkins Pipeline: https://www.jenkins.io/doc/book/pipeline/
- SonarQube: https://docs.sonarsource.com/sonarqube-server/
