# Jenkins: testing, calidad del código e integración continua (CI/CD)

## 1. Introducción

**Jenkins** es una herramienta de automatización de código abierto que permite construir, probar, analizar y desplegar aplicaciones mediante procesos automatizados.

Se utiliza habitualmente para implementar prácticas de integración continua (*Continuous Integration*, CI) y entrega o despliegue continuo (*Continuous Delivery/Deployment*, CD).

En el contexto del testing y la calidad del código, Jenkins permite definir un proceso reproducible que ejecuta las comprobaciones necesarias cada vez que se incorpora un cambio al repositorio.

Por ejemplo, cuando un desarrollador crea una rama o abre una *pull request*, Jenkins puede:

1. Obtener el código del repositorio.
2. Compilar la aplicación.
3. Ejecutar las pruebas unitarias.
4. Ejecutar las pruebas de integración.
5. Calcular métricas de cobertura.
6. Analizar el código con SonarQube.
7. Ejecutar pruebas E2E.
8. Generar informes de resultados.
9. Bloquear la integración si se incumplen los criterios definidos.
10. Desplegar la aplicación si todas las validaciones obligatorias son satisfactorias.

El proceso se define normalmente mediante un archivo llamado `Jenkinsfile`, que se almacena junto al código del proyecto y permite versionar el pipeline como cualquier otro artefacto de software.

Documentación oficial: [Jenkins Pipeline](https://www.jenkins.io/doc/book/pipeline/).

---

## 2. ¿Qué problema resuelve Jenkins?

Sin automatización, las validaciones suelen depender de que cada desarrollador recuerde ejecutar los comandos adecuados y de que todos utilicen procedimientos equivalentes.

Esto puede provocar situaciones como:

- Código que se integra sin haber ejecutado las pruebas.
- Diferencias entre los entornos locales y los de integración.
- Defectos que se descubren demasiado tarde.
- Falta de trazabilidad sobre qué pruebas se ejecutaron.
- Publicaciones con errores conocidos.
- Análisis de calidad inconsistentes.
- Procesos manuales repetitivos y propensos a errores.

Jenkins permite centralizar estas tareas en un proceso automatizado, repetible y visible para el equipo.

### Objetivos principales

- Automatizar la compilación y las pruebas.
- Detectar defectos cuanto antes.
- Estandarizar las validaciones de calidad.
- Integrar herramientas como SonarQube.
- Facilitar la trazabilidad de cada cambio.
- Reducir errores en los procesos de entrega.
- Aplicar políticas de calidad antes de integrar o desplegar código.
- Generar evidencias y resultados consultables.

**Idea clave:** Jenkins no garantiza por sí mismo la calidad del software. Su valor consiste en automatizar y hacer cumplir un proceso de calidad definido por el equipo.

---

## 3. Conceptos fundamentales

### 3.1. Integración continua (CI)

La integración continua consiste en integrar cambios pequeños y frecuentes en un repositorio compartido y validar automáticamente cada cambio.

Un proceso CI habitual ejecuta:

- Compilación.
- Pruebas unitarias.
- Pruebas de integración.
- Análisis estático.
- Comprobaciones de cobertura.
- Validaciones de dependencias o seguridad, cuando estén configuradas.

El objetivo es detectar los problemas cerca del momento en que se introducen.

### 3.2. Entrega continua (Continuous Delivery)

La entrega continua automatiza el proceso necesario para producir una versión potencialmente desplegable.

El pipeline puede construir el artefacto, validarlo, empaquetarlo y publicarlo en un repositorio o entorno de pruebas.

El despliegue a producción puede seguir requiriendo una aprobación explícita.

### 3.3. Despliegue continuo (Continuous Deployment)

El despliegue continuo va un paso más allá: los cambios que superan todas las validaciones y políticas configuradas se despliegan automáticamente en producción.

No todos los equipos necesitan este nivel de automatización. Depende del riesgo, la arquitectura, la capacidad de observabilidad y los mecanismos de recuperación disponibles.

### Diferencia resumida

| Concepto | Objetivo |
|---|---|
| CI | Validar los cambios continuamente |
| Continuous Delivery | Mantener versiones listas para desplegar |
| Continuous Deployment | Desplegar automáticamente los cambios aprobados por el pipeline |

---

## 4. ¿Cómo funciona Jenkins?

Jenkins organiza el trabajo en procesos llamados *jobs* o *pipelines*. Estos procesos pueden iniciarse manualmente o mediante eventos y mecanismos programados.

Una arquitectura habitual incluye los siguientes componentes:

### Controller

Es el componente central que coordina los trabajos, gestiona la configuración y presenta los resultados.

### Agents

Son las máquinas o entornos de ejecución que realizan tareas del pipeline. Pueden utilizar sistemas operativos, herramientas o recursos diferentes.

### Jobs y Pipelines

Definen las tareas que deben ejecutarse y sus dependencias.

### Plugins

Amplían las funcionalidades de Jenkins e integran herramientas de control de versiones, testing, análisis, publicación de artefactos y despliegue.

### Repositorio de código

Almacena el código fuente y, habitualmente, el `Jenkinsfile`.

### Flujo general

```text
Desarrollador
      |
      v
Repositorio Git
      |
      v
Jenkins Controller
      |
      v
Jenkins Agent
      |
      v
Compilación
      |
      v
Pruebas unitarias
      |
      v
Pruebas de integración
      |
      v
Análisis con SonarQube
      |
      v
Pruebas E2E
      |
      v
Publicación del artefacto
      |
      v
Despliegue autorizado
```

El orden exacto depende del proyecto. Por ejemplo, puede ejecutarse el análisis estático antes de las pruebas E2E, mientras que las pruebas de rendimiento más costosas se reservan para ejecuciones específicas o entornos de preproducción.

---

## 5. Jenkins y las herramientas de testing

Jenkins no sustituye a los frameworks de testing. Los ejecuta, coordina sus resultados y puede detener el proceso cuando se produce un fallo.

| Tipo de prueba o análisis | Herramientas posibles | Función de Jenkins |
|---|---|---|
| Pruebas unitarias | JUnit, pytest, Jest | Ejecutar y publicar resultados |
| Pruebas de integración | JUnit, Testcontainers, REST Assured | Preparar dependencias y ejecutar escenarios |
| Pruebas funcionales | Frameworks del proyecto | Automatizar su ejecución |
| Pruebas E2E | Playwright, Cypress, Selenium | Ejecutar flujos completos y conservar evidencias |
| Pruebas de rendimiento | k6, JMeter | Lanzar pruebas y recopilar resultados |
| Cobertura | JaCoCo, cobertura de otros lenguajes | Generar y publicar métricas |
| Análisis estático | SonarQube, linters | Coordinar el análisis y aplicar los criterios de calidad |
| Análisis de dependencias | Herramientas especializadas | Ejecutar comprobaciones y aplicar políticas |

La integración concreta depende del lenguaje, las herramientas y los plugins instalados.

---

## 6. Jenkins Pipeline y Jenkinsfile

Un *Pipeline* define las etapas del proceso de integración y entrega.

El `Jenkinsfile` permite declarar ese proceso como código y guardarlo en el repositorio.

Jenkins admite dos estilos principales:

- **Declarative Pipeline:** estructura más guiada y fácil de leer.
- **Scripted Pipeline:** mayor flexibilidad mediante Groovy.

Para la mayoría de los pipelines de un proyecto de aplicación, Declarative Pipeline es un buen punto de partida.

### Estructura básica

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Obteniendo el código'
            }
        }

        stage('Build') {
            steps {
                echo 'Compilando la aplicación'
            }
        }

        stage('Test') {
            steps {
                echo 'Ejecutando las pruebas'
            }
        }
    }

    post {
        always {
            echo 'Finalizando el pipeline'
        }
    }
}
```

Este ejemplo solo ilustra la estructura: los mensajes `echo` no compilan ni prueban realmente una aplicación.

En un proyecto real, las etapas deben ejecutar los comandos del sistema de construcción correspondiente.

### Elementos importantes

- `pipeline`: define el pipeline.
- `agent`: indica dónde se ejecutan las tareas.
- `stages`: agrupa el proceso en etapas.
- `stage`: define una etapa concreta.
- `steps`: contiene las acciones de la etapa.
- `post`: permite ejecutar acciones al finalizar, por ejemplo, publicar informes.

Referencia oficial: [Pipeline Syntax](https://www.jenkins.io/doc/book/pipeline/syntax/).

---

## 7. Ejemplo práctico: Jenkins con Java, Maven y JUnit

Supongamos que trabajamos con una aplicación Java construida con Maven y que utiliza JUnit 5 para las pruebas.

Queremos que Jenkins:

1. Obtenga el código.
2. Compile la aplicación.
3. Ejecute las pruebas.
4. Publique los resultados.
5. Marque la ejecución como fallida si las comprobaciones no se superan.

### Estructura del proyecto

```text
mi-aplicacion/
├── src/
│   ├── main/
│   │   └── java/
│   └── test/
│       └── java/
├── pom.xml
└── Jenkinsfile
```

### Ejemplo de Jenkinsfile

```groovy
pipeline {
    agent any

    options {
        timestamps()
        timeout(time: 20, unit: 'MINUTES')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Compilar') {
            steps {
                sh 'mvn -B -DskipTests compile'
            }
        }

        stage('Pruebas unitarias') {
            steps {
                sh 'mvn -B test'
            }
        }

        stage('Empaquetar') {
            steps {
                sh 'mvn -B -DskipTests package'
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

Este ejemplo está pensado para un agente Linux que tenga Java y Maven instalados y configurados. En agentes Windows se utilizarían los pasos apropiados para ese entorno.

### ¿Qué hace cada etapa?

**Checkout**

Obtiene la revisión del repositorio asociada al trabajo.

**Compilar**

Comprueba que el código de producción se puede compilar. El comando omite las pruebas en esta etapa porque se ejecutan por separado.

**Pruebas unitarias**

Ejecuta las pruebas configuradas en Maven. Si Maven devuelve un código de error por pruebas fallidas, la etapa falla.

**Empaquetar**

Genera el artefacto de la aplicación, por ejemplo, un JAR.

**Publicación de resultados**

El paso `junit` recoge los informes XML generados por Maven y permite consultar los resultados en Jenkins, incluyendo fallos y tendencias históricas.

Documentación oficial: [JUnit Plugin](https://www.jenkins.io/doc/pipeline/steps/junit/).

### Consideración importante

El pipeline puede detenerse al fallar una etapa. El bloque `post { always { ... } }` permite intentar publicar los informes incluso cuando una etapa anterior falla.

Si la compilación falla antes de generar informes, el patrón de resultados puede no encontrar ningún archivo. Por eso conviene ajustar los patrones a la estructura real del proyecto y mantener un comportamiento explícito ante informes ausentes.

---

## 8. Integración de SonarQube con Jenkins

SonarQube analiza el código para detectar problemas de calidad estática, defectos potenciales y problemas de mantenibilidad, entre otros aspectos.

Jenkins puede lanzar el análisis y esperar a que SonarQube evalúe el *Quality Gate* configurado.

El Quality Gate es un conjunto de condiciones que determina si el análisis cumple los criterios establecidos.

Por ejemplo, un equipo podría decidir que los cambios nuevos deben cumplir condiciones relacionadas con:

- Defectos nuevos.
- Vulnerabilidades nuevas.
- Mantenibilidad.
- Cobertura del código nuevo.
- Código duplicado.

Los criterios exactos deben configurarse en SonarQube según las necesidades del proyecto.

### Flujo de integración

```text
Jenkins
   |
   v
Compilar y ejecutar pruebas
   |
   v
Generar datos de análisis y cobertura
   |
   v
Enviar análisis a SonarQube
   |
   v
Evaluar Quality Gate
   |
   +------> Cumple: continuar
   |
   +------> No cumple: bloquear la promoción
```

### Configuración necesaria

Habitualmente se necesita:

1. Una instancia de SonarQube accesible desde Jenkins.
2. El plugin de integración de SonarQube para Jenkins.
3. Una configuración del servidor en Jenkins.
4. Credenciales gestionadas de forma segura.
5. La configuración del proyecto y de su análisis.
6. Un webhook de SonarQube a Jenkins para que `waitForQualityGate` reciba el resultado de la evaluación.

La configuración exacta depende de la versión y del tipo de instalación.

### Ejemplo de Jenkinsfile

El siguiente ejemplo presupone que SonarQube está configurado en Jenkins con el nombre `SonarQubeServer`, que el agente dispone de Java y Maven y que se ha configurado el webhook necesario.

```groovy
pipeline {
    agent any

    stages {
        stage('Build y tests') {
            steps {
                sh 'mvn -B clean verify'
            }
        }

        stage('Análisis SonarQube') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {
                    sh '''
                        mvn -B sonar:sonar \
                          -Dsonar.projectKey=mi-aplicacion
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    script {
                        def qualityGate = waitForQualityGate()
                        if (qualityGate.status != 'OK') {
                            error(
                                "Quality Gate no superado: " +
                                qualityGate.status
                            )
                        }
                    }
                }
            }
        }
    }
}
```

El ejemplo utiliza un identificador de proyecto ilustrativo. Debe sustituirse por la clave real del proyecto y por la configuración correspondiente a la instalación.

Además, la ejecución de `mvn clean verify` debe generar los informes y la cobertura que se espera utilizar. La cobertura no aparece automáticamente en SonarQube si no se genera y configura el informe correspondiente.

Documentación oficial:

- [Jenkins Pipeline](https://www.jenkins.io/doc/book/pipeline/)
- [SonarQube Server Documentation](https://docs.sonarsource.com/sonarqube-server/)

### ¿Por qué es importante el Quality Gate?

Sin un criterio de aceptación automatizado, el equipo puede ejecutar análisis de calidad y continuar el despliegue aunque el resultado sea insatisfactorio.

Con un Quality Gate integrado en el pipeline, el análisis puede convertirse en una condición explícita para avanzar.

No obstante, hay que definir bien las reglas: un umbral inadecuado o un análisis mal configurado puede bloquear cambios válidos o permitir que pasen problemas importantes.

---

## 9. Cobertura de código y Jenkins

La cobertura mide qué parte del código se ejecuta durante las pruebas, utilizando métricas como líneas, instrucciones o ramas cubiertas.

En proyectos Java, una herramienta habitual es JaCoCo.

Jenkins puede ejecutar el proceso que genera el informe y publicar los resultados mediante el plugin apropiado.

### Ejemplo con Maven y JaCoCo

En el `pom.xml`, el proyecto debe configurar JaCoCo para generar el informe de cobertura. Una vez configurado, el pipeline puede ejecutar:

```bash
mvn -B clean verify
```

Después, se puede publicar el informe mediante un plugin de cobertura compatible con la configuración del proyecto.

### Buenas prácticas

- Medir la cobertura de forma consistente.
- Distinguir el código nuevo del código heredado.
- Utilizar umbrales que tengan sentido para el proyecto.
- Analizar también la calidad de los casos de prueba.
- Comprobar las ramas importantes de la lógica de negocio.

**Una cobertura elevada no garantiza que las pruebas sean correctas.** Es posible ejecutar una línea de código sin comprobar que su comportamiento sea el esperado.

Por eso, la cobertura debe interpretarse junto con los resultados de las pruebas, el análisis estático y la revisión de código.

---

## 10. Jenkins y los diferentes tipos de testing

### 10.1. Pruebas unitarias

Son un buen candidato para ejecutarse en cada cambio, ya que suelen ser rápidas.

El pipeline debería:

- Ejecutarlas automáticamente.
- Detectar fallos.
- Publicar los informes.
- Impedir la promoción si fallan pruebas obligatorias.

### 10.2. Pruebas de integración

Pueden necesitar bases de datos, contenedores, colas de mensajes u otros servicios.

Jenkins puede ejecutar estos tests en agentes con los requisitos necesarios, utilizando servicios de prueba aislados y datos reproducibles.

En proyectos Java, Testcontainers puede ayudar a levantar dependencias reales en contenedores durante las pruebas.

### 10.3. Pruebas funcionales

Verifican que el sistema satisface los requisitos funcionales definidos.

Pueden ejecutarse en una etapa específica del pipeline, según su duración y sus dependencias.

### 10.4. Pruebas E2E

Herramientas como Playwright, Cypress y Selenium pueden automatizar flujos completos de usuario.

Estas pruebas pueden ejecutarse después de desplegar la aplicación en un entorno de pruebas.

Es recomendable conservar capturas, trazas y resultados para investigar los fallos.

### 10.5. Pruebas de rendimiento

Jenkins puede lanzar pruebas con k6 o JMeter en entornos controlados.

El pipeline puede recoger métricas como latencia, tasa de errores y rendimiento, y compararlas con umbrales definidos.

Las pruebas de carga intensiva deben ejecutarse con límites y autorización adecuados para evitar afectar a otros entornos.

### Estrategia recomendada

No es obligatorio ejecutar todas las categorías de pruebas en cada cambio. El pipeline puede dividirse en varios niveles:

- Validaciones rápidas en cada *commit* o *pull request*.
- Pruebas de integración en cada cambio relevante.
- Pruebas E2E en cambios que afecten a flujos críticos o en etapas seleccionadas.
- Pruebas de rendimiento extensas en ejecuciones programadas o antes de versiones importantes.

---

## 11. Ejemplo de pipeline completo

Este ejemplo muestra cómo organizar las etapas de un proyecto Java con Maven, SonarQube y pruebas E2E.

Es una plantilla conceptual: presupone que las herramientas están instaladas y configuradas, que los comandos del proyecto existen y que la aplicación se puede iniciar en el entorno de pruebas.

```groovy
pipeline {
    agent any

    options {
        timestamps()
        timeout(time: 40, unit: 'MINUTES')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Compilación y pruebas') {
            steps {
                sh 'mvn -B clean verify'
            }
        }

        stage('Análisis estático') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {
                    sh '''
                        mvn -B sonar:sonar \
                          -Dsonar.projectKey=mi-aplicacion
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    script {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error("Quality Gate: ${qg.status}")
                        }
                    }
                }
            }
        }

        stage('Pruebas E2E') {
            steps {
                sh './scripts/run-e2e-tests.sh'
            }
        }

        stage('Publicar artefacto') {
            steps {
                archiveArtifacts(
                    artifacts: 'target/*.jar',
                    fingerprint: true
                )
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

        success {
            echo 'Pipeline completado correctamente'
        }

        failure {
            echo 'El pipeline ha fallado'
        }
    }
}
```

### Aspectos que deben adaptarse

- El nombre `SonarQubeServer` debe coincidir con la configuración real.
- El webhook de SonarQube debe estar operativo para esperar el Quality Gate.
- El script de pruebas E2E debe existir y devolver un código de salida correcto.
- La aplicación debe estar disponible en el entorno que utilizan las pruebas E2E.
- Los informes de pruebas deben coincidir con los patrones de publicación.
- La ruta de los artefactos debe ajustarse al empaquetado real.
- Si existen varios módulos Maven, puede ser necesario adaptar los patrones de informes y artefactos.

El ejemplo publica el JAR solo si se alcanza la etapa correspondiente. En un proyecto real también puede ser conveniente separar la construcción del artefacto de su publicación, para garantizar que se promueve exactamente el mismo artefacto que ha superado las validaciones.

---

## 12. Jenkins y Git: ejecución automática

Jenkins puede integrarse con GitHub, GitLab, Bitbucket y otros sistemas de control de versiones.

El pipeline puede iniciarse mediante:

- Un webhook cuando se crea o actualiza una *pull request*.
- Un nuevo *commit*.
- Un cambio en una rama.
- Una ejecución manual.
- Una programación periódica.

### Flujo habitual con pull requests

1. El desarrollador crea una rama.
2. Publica los cambios.
3. Se abre una *pull request*.
4. Jenkins ejecuta las comprobaciones.
5. Se publican los resultados.
6. El equipo revisa el código.
7. Se permite la integración cuando se cumplen las políticas del repositorio.

La política para permitir o bloquear la integración suele requerir también configuración en el proveedor Git, por ejemplo, reglas de protección de ramas o comprobaciones obligatorias.

No basta con que Jenkins ejecute el pipeline: hay que conectar correctamente sus resultados con la política de integración del repositorio.

---

## 13. Seguridad y gestión de credenciales

Un pipeline puede necesitar credenciales para acceder a repositorios, SonarQube, registros de contenedores o entornos de despliegue.

Las credenciales no deben guardarse directamente en el `Jenkinsfile`, ni imprimirse en los logs.

Jenkins dispone de un sistema de credenciales que permite almacenarlas y referenciarlas mediante identificadores.

### Buenas prácticas

- Utilizar credenciales administradas por Jenkins.
- Limitar los permisos a lo estrictamente necesario.
- Separar las credenciales de pruebas y producción.
- No mostrar tokens ni contraseñas en la salida del pipeline.
- Evitar que código no confiable de una *pull request* acceda a secretos privilegiados.
- Limitar los permisos de los agentes.
- Mantener Jenkins y sus plugins actualizados.
- Proteger el acceso administrativo.

Documentación oficial: [Using Credentials](https://www.jenkins.io/doc/book/using/using-credentials/).

---

## 14. Agentes, contenedores y reproducibilidad

El entorno de ejecución afecta directamente a la fiabilidad de un pipeline.

Un trabajo puede pasar en una máquina y fallar en otra si existen diferencias en versiones de Java, Node.js, Maven, navegadores o dependencias del sistema.

Para reducir estos problemas se pueden utilizar agentes dedicados, contenedores y versiones de herramientas controladas.

### Beneficios

- Entornos de ejecución más reproducibles.
- Separación de dependencias entre proyectos.
- Mayor facilidad para escalar el número de trabajos.
- Menos diferencias entre ejecuciones.
- Posibilidad de utilizar herramientas específicas por etapa.

### Precauciones

Los contenedores no garantizan por sí solos la reproducibilidad. También es necesario controlar las versiones de las imágenes, dependencias y herramientas, además de gestionar correctamente los datos y servicios externos.

Los agentes que ejecutan código de ramas no confiables deben tener permisos y acceso a secretos cuidadosamente limitados.

---

## 15. Errores frecuentes al utilizar Jenkins

### Ejecutar pruebas, pero no publicar sus resultados

El pipeline puede fallar, pero el equipo pierde visibilidad sobre las causas y las tendencias.

**Solución:** publicar los informes XML y conservar evidencias relevantes.

### Confundir ejecución con calidad

Que el pipeline termine correctamente no significa que el código cumpla cualquier criterio de calidad imaginable.

**Solución:** definir condiciones explícitas para pruebas, cobertura, análisis y despliegue.

### No controlar las versiones de las herramientas

Cambios de versión pueden alterar los resultados o provocar incompatibilidades.

**Solución:** fijar versiones y actualizarlas de forma controlada.

### Ejecutar todo en cada cambio

Un pipeline excesivamente largo ralentiza el feedback del equipo.

**Solución:** priorizar las comprobaciones rápidas y reservar las pruebas más costosas para momentos adecuados.

### Utilizar credenciales dentro del código

Puede exponer secretos en el repositorio o los logs.

**Solución:** usar el sistema de credenciales de Jenkins y limitar los permisos.

### Ignorar pruebas inestables

Las pruebas que fallan de forma intermitente reducen la confianza en el pipeline.

**Solución:** identificar su causa, aislar los datos y mejorar los mecanismos de espera.

### Permitir despliegues sin validaciones obligatorias

La automatización no protege el sistema si sus fallos se ignoran.

**Solución:** configurar políticas de promoción y reglas de protección de ramas coherentes con el riesgo del proyecto.

---

## 16. Jenkins frente a GitHub Actions y GitLab CI/CD

Jenkins no es la única solución para automatizar pipelines.

| Aspecto | Jenkins | GitHub Actions | GitLab CI/CD |
|---|---|---|---|
| Modelo | Servidor de automatización autogestionado habitualmente | Servicio integrado en GitHub o runners autogestionados | Integrado en GitLab o runners autogestionados |
| Configuración | Jenkinsfile y plugins | Workflows YAML | `.gitlab-ci.yml` |
| Flexibilidad | Alta, con ecosistema de plugins | Alta, con acciones reutilizables | Alta, con componentes y jobs |
| Operación | El equipo suele gestionar el controller y su mantenimiento | Depende de la modalidad utilizada | Depende de la modalidad utilizada |
| Integración con repositorio | Requiere configurar las integraciones | Directa en GitHub | Directa en GitLab |

### ¿Cuándo elegir Jenkins?

Puede ser una buena opción cuando:

- Se necesita un alto grado de personalización.
- Existe infraestructura propia de CI/CD.
- Hay agentes especializados o requisitos de ejecución particulares.
- El equipo ya tiene experiencia con Jenkins.
- Es necesario integrar herramientas y sistemas heterogéneos.

La decisión también debe considerar el coste operativo, las actualizaciones, la seguridad, el mantenimiento de plugins y las necesidades de escalado.

---

## 17. Métricas para evaluar el pipeline

Además de contar pruebas y fallos, es útil medir la eficacia del proceso de integración.

### Métricas relevantes

- **Duración del pipeline:** tiempo desde el inicio hasta el resultado.
- **Tiempo de feedback:** cuánto tarda el desarrollador en conocer el resultado.
- **Tasa de éxito:** proporción de ejecuciones satisfactorias.
- **Tasa de fallos de pruebas:** porcentaje de ejecuciones con tests fallidos.
- **Inestabilidad de pruebas:** frecuencia con la que un test falla de manera no reproducible.
- **Cobertura:** parte del código ejecutada por las pruebas.
- **Defectos detectados antes de producción:** indicador de la capacidad preventiva.
- **Frecuencia de despliegue:** con qué frecuencia se publican cambios.
- **Tiempo de recuperación:** cuánto se tarda en recuperar el servicio tras un fallo.

Estas métricas deben interpretarse en conjunto. Por ejemplo, una tasa de éxito elevada no significa necesariamente que las pruebas cubran los riesgos más importantes.

### Objetivo práctico

Un buen pipeline debe proporcionar feedback rápido, detectar defectos relevantes y permitir investigar los fallos sin introducir una carga de mantenimiento excesiva.

---

## 18. Relación con Clean Code, SOLID y la calidad del software

Jenkins automatiza comprobaciones, pero la calidad también depende de cómo se diseña y mantiene el código.

### Clean Code

Un código legible facilita la revisión y el diagnóstico de defectos.

El pipeline puede ejecutar linters y análisis estáticos para aplicar parte de las convenciones del equipo.

### SOLID

Los principios SOLID ayudan a organizar responsabilidades y reducir acoplamientos innecesarios.

Las pruebas automatizadas pueden verificar que el comportamiento esperado se conserva cuando el diseño evoluciona.

### DRY

Evitar duplicación innecesaria simplifica el mantenimiento. También puede aplicarse a pipelines mediante bibliotecas compartidas, siempre que la abstracción sea útil y comprensible.

### KISS

Un pipeline sencillo es más fácil de entender, mantener y depurar.

Conviene evitar etapas y plugins que no aporten un beneficio claro.

### YAGNI

La infraestructura de CI/CD debe evolucionar según las necesidades reales. No es necesario implantar desde el primer día una cadena de herramientas excesivamente compleja.

**Importante:** Jenkins puede hacer cumplir ciertas reglas, pero no reemplaza el diseño adecuado, las revisiones de código ni el criterio técnico del equipo.

---

## 19. Estrategia recomendada de implantación

### Fase 1. Pipeline básico

- Obtener el código.
- Compilar.
- Ejecutar pruebas unitarias.
- Publicar resultados.

### Fase 2. Calidad estática

- Integrar SonarQube.
- Generar informes de cobertura.
- Definir un Quality Gate.
- Revisar las condiciones que bloquean la integración.

### Fase 3. Pruebas de integración

- Preparar bases de datos y dependencias de prueba.
- Automatizar los escenarios prioritarios.
- Publicar los informes.

### Fase 4. Pruebas E2E

- Seleccionar los flujos críticos.
- Preparar el entorno de ejecución.
- Recopilar capturas, trazas e informes.
- Controlar la duración y la estabilidad.

### Fase 5. Publicación y despliegue

- Generar artefactos versionados.
- Publicarlos en un repositorio adecuado.
- Desplegar en un entorno de pruebas.
- Aplicar aprobaciones y controles antes de producción.

### Fase 6. Mejora continua

- Medir la duración del pipeline.
- Eliminar cuellos de botella.
- Corregir pruebas inestables.
- Revisar periódicamente plugins, agentes y permisos.

---

## 20. Checklist de calidad para Jenkins

- El pipeline está versionado en el repositorio.
- Las etapas tienen responsabilidades claras.
- Las pruebas unitarias se ejecutan automáticamente.
- Las pruebas de integración se ejecutan en un entorno controlado.
- Los resultados de las pruebas se publican.
- La cobertura se genera y se interpreta correctamente.
- SonarQube se integra cuando aporta valor al proyecto.
- El Quality Gate tiene criterios explícitos.
- Los flujos E2E críticos están automatizados.
- Los artefactos generados se identifican y conservan.
- Los secretos se gestionan mediante credenciales seguras.
- Las ramas protegidas exigen las comprobaciones relevantes.
- Los agentes tienen permisos limitados.
- Las herramientas y dependencias se mantienen actualizadas.
- Se investigan los fallos intermitentes.
- Se controla la duración de cada etapa.
- Los despliegues se realizan con controles apropiados.
- Existen mecanismos para diagnosticar y recuperar fallos.

---

## 21. Conclusión

Jenkins es una herramienta de automatización que permite integrar testing, análisis de calidad y despliegues en un proceso repetible.

Su principal aportación no consiste en sustituir a las herramientas de calidad, sino en coordinarlas y convertir sus resultados en decisiones automatizadas.

Una estrategia madura puede combinar:

- Jenkins para orquestar el pipeline.
- JUnit y otros frameworks para ejecutar pruebas.
- JaCoCo para medir cobertura en Java.
- SonarQube para analizar calidad estática y aplicar un Quality Gate.
- Playwright, Cypress o Selenium para pruebas E2E.
- k6 o JMeter para pruebas de rendimiento.
- Repositorios de artefactos para conservar las versiones construidas.
- Sistemas de control de versiones para gestionar cambios y reglas de integración.

La recomendación es empezar con un pipeline pequeño que compile, ejecute las pruebas y publique sus resultados. Después, incorporar progresivamente análisis estático, cobertura, pruebas de integración, E2E y despliegue.

**Un pipeline de calidad eficaz no se limita a ejecutar comandos: convierte las expectativas de calidad del equipo en comprobaciones reproducibles, visibles y verificables.**

---

## 22. Referencias oficiales

- Jenkins Pipeline: https://www.jenkins.io/doc/book/pipeline/
- Introducción a Pipeline: https://www.jenkins.io/doc/book/pipeline/getting-started/
- Sintaxis de Jenkinsfile: https://www.jenkins.io/doc/book/pipeline/syntax/
- Plugin JUnit: https://www.jenkins.io/doc/pipeline/steps/junit/
- Gestión de credenciales: https://www.jenkins.io/doc/book/using/using-credentials/
- Documentación de SonarQube: https://docs.sonarsource.com/sonarqube-server/
- JaCoCo: https://www.jacoco.org/jacoco/trunk/doc/
- Playwright: https://playwright.dev/
- k6: https://grafana.com/docs/k6/latest/
- GitHub Actions: https://docs.github.com/actions
