# Pruebas de rendimiento (Performance Testing)

## 1. Introducción

Las **pruebas de rendimiento** son un conjunto de pruebas de software cuyo objetivo es evaluar la velocidad, estabilidad, capacidad y consumo de recursos de una aplicación bajo unas condiciones determinadas de uso.

No se limitan a comprobar si una operación funciona correctamente, sino que analizan **cómo se comporta el sistema cuando recibe solicitudes, procesa datos y utiliza recursos**.

Por ejemplo, una aplicación de comercio electrónico puede superar todas sus pruebas funcionales y, sin embargo, presentar problemas cuando cientos o miles de usuarios intentan comprar simultáneamente:

- Las páginas tardan demasiado en cargar.
- Las consultas a la base de datos se vuelven lentas.
- Aumenta el número de errores HTTP.
- Se agota la memoria disponible.
- Las conexiones a la base de datos quedan saturadas.
- El sistema deja de responder cuando aumenta la demanda.

Las pruebas de rendimiento permiten descubrir estos problemas antes de que afecten a los usuarios en producción.

### Objetivos principales

1. Medir los tiempos de respuesta.
2. Determinar cuántas solicitudes puede procesar un sistema.
3. Identificar cuellos de botella.
4. Evaluar el comportamiento bajo diferentes niveles de carga.
5. Detectar degradaciones progresivas del rendimiento.
6. Comprobar el consumo de CPU, memoria, red y otros recursos.
7. Verificar que se cumplen los objetivos de rendimiento acordados.
8. Evitar regresiones de rendimiento durante la evolución del software.

---

## 2. ¿Qué problema resuelven?

Una aplicación puede estar correctamente implementada desde el punto de vista funcional y seguir siendo inadecuada para su uso real si no responde con suficiente rapidez o no soporta el volumen esperado de operaciones.

Imaginemos una API que permite consultar productos.

En condiciones normales, la consulta tarda 100 milisegundos. Sin embargo, cuando aumenta la carga:

- El tiempo de respuesta supera varios segundos.
- Las consultas concurrentes bloquean recursos.
- El pool de conexiones a la base de datos se agota.
- Las peticiones empiezan a expirar.
- Los usuarios repiten las operaciones y generan todavía más carga.

Sin pruebas de rendimiento, estos problemas pueden descubrirse cuando el sistema ya está en producción.

### Sin pruebas de rendimiento

1. Se desarrolla la aplicación.
2. Se ejecutan pruebas unitarias, de integración y funcionales.
3. Se valida que las operaciones funcionan.
4. Se despliega el sistema.
5. La carga real revela limitaciones de capacidad.

### Con pruebas de rendimiento

1. Se definen los objetivos de rendimiento.
2. Se modelan los patrones de uso esperados.
3. Se prepara un entorno representativo.
4. Se genera carga controlada.
5. Se miden latencia, errores y consumo de recursos.
6. Se identifican los cuellos de botella.
7. Se optimiza el sistema y se repiten las pruebas.

La finalidad no es conseguir que todas las operaciones sean instantáneas, sino comprobar que el sistema satisface unos objetivos realistas bajo condiciones conocidas.

---

## 3. ¿Qué se considera una prueba de rendimiento?

Una prueba de rendimiento ejecuta operaciones sobre un sistema mientras se mide cómo responde ante unas condiciones definidas.

Estas condiciones pueden incluir:

- Número de usuarios concurrentes.
- Frecuencia de las solicitudes.
- Duración de la prueba.
- Volumen de datos.
- Tamaño de las peticiones y respuestas.
- Distribución de las operaciones.
- Recursos disponibles para la aplicación.
- Comportamiento de las dependencias.

Por ejemplo, una prueba puede simular 200 usuarios virtuales que consultan productos, inician sesión y añaden artículos al carrito durante diez minutos.

Durante la ejecución se recopilan métricas como:

- Tiempo de respuesta.
- Percentiles de latencia.
- Solicitudes por segundo.
- Porcentaje de errores.
- CPU y memoria.
- Conexiones activas.
- Saturación de la base de datos.

Los resultados permiten comprobar si el sistema cumple los objetivos definidos.

**Importante:** una prueba de rendimiento no garantiza que la aplicación vaya a comportarse igual en cualquier entorno. Los resultados dependen de la infraestructura, la configuración, los datos, las dependencias y el patrón de carga utilizado.

---

## 4. Tipos de pruebas de rendimiento

Existen distintos tipos de pruebas, cada uno orientado a responder una pregunta diferente.

### 4.1. Pruebas de carga (*Load Testing*)

Evalúan el comportamiento del sistema bajo una carga esperada o cercana a la habitual.

**Pregunta principal:** ¿puede la aplicación soportar el volumen de trabajo previsto?

Ejemplo:

- 200 usuarios virtuales.
- Un flujo realista de consultas y operaciones.
- Una duración de 20 minutos.
- Un objetivo de latencia definido.

Permiten comprobar si el sistema mantiene sus niveles de servicio en condiciones normales o de máxima actividad prevista.

### 4.2. Pruebas de estrés (*Stress Testing*)

Incrementan la carga por encima de las condiciones habituales para identificar los límites del sistema y observar cómo se degrada.

**Pregunta principal:** ¿qué ocurre cuando se supera la capacidad prevista?

Se puede aumentar progresivamente la concurrencia hasta observar:

- Incrementos pronunciados de latencia.
- Aumento de errores.
- Saturación de CPU o memoria.
- Agotamiento de conexiones.
- Incapacidad para procesar nuevas solicitudes.

También es importante comprobar si el sistema se recupera cuando la carga vuelve a niveles normales.

### 4.3. Pruebas de picos (*Spike Testing*)

Evalúan la respuesta ante incrementos repentinos de carga.

**Pregunta principal:** ¿cómo reacciona la aplicación cuando la demanda aumenta bruscamente?

Ejemplo:

Una plataforma recibe de repente un volumen elevado de peticiones debido al lanzamiento de una promoción.

Estas pruebas pueden revelar problemas relacionados con:

- Escalado automático.
- Arranque de nuevas instancias.
- Cachés.
- Pools de conexiones.
- Colas de trabajo.
- Límites de concurrencia.

### 4.4. Pruebas de resistencia (*Soak Testing* o *Endurance Testing*)

Mantienen una carga relativamente estable durante un periodo prolongado.

**Pregunta principal:** ¿puede el sistema mantener un comportamiento estable durante mucho tiempo?

Permiten detectar:

- Fugas de memoria.
- Acumulación de conexiones.
- Crecimiento de colas.
- Degradación progresiva.
- Problemas de limpieza de recursos.
- Acumulación de tareas pendientes.

Una aplicación puede funcionar correctamente durante cinco minutos y degradarse después de varias horas.

### 4.5. Pruebas de capacidad (*Capacity Testing*)

Buscan determinar la capacidad de procesamiento que puede ofrecer el sistema cumpliendo unos objetivos de servicio.

**Pregunta principal:** ¿cuánta carga puede soportar la aplicación sin incumplir sus objetivos?

Los resultados ayudan a planificar infraestructura, escalado y límites operativos.

### 4.6. Pruebas de escalabilidad (*Scalability Testing*)

Evalúan cómo cambia el rendimiento cuando se incrementan los recursos disponibles o se modifica la arquitectura.

**Pregunta principal:** ¿el sistema aprovecha correctamente los recursos adicionales?

Por ejemplo, se puede comparar el comportamiento con dos, cuatro y ocho instancias de una aplicación.

Duplicar las instancias no implica necesariamente duplicar la capacidad: puede existir un cuello de botella compartido en la base de datos, una dependencia externa o un recurso centralizado.

### 4.7. Pruebas de rendimiento de frontend

Evalúan el comportamiento percibido por el usuario en una interfaz web o móvil.

Pueden estudiar:

- Tiempo de carga.
- Renderizado.
- Ejecución de JavaScript.
- Carga de recursos.
- Interactividad.
- Estabilidad visual.

Las métricas del navegador no son equivalentes a la latencia de una API. Para evaluar una experiencia completa conviene analizar tanto el frontend como el backend.

---

## 5. Diferencias entre los tipos de pruebas

| Tipo | Qué evalúa | Pregunta principal |
|---|---|---|
| Carga | Demanda esperada | ¿Funciona bien bajo la carga prevista? |
| Estrés | Demanda por encima de lo habitual | ¿Dónde están los límites? |
| Picos | Cambios bruscos de demanda | ¿Tolera aumentos repentinos? |
| Resistencia | Carga sostenida | ¿Se mantiene estable con el tiempo? |
| Capacidad | Volumen máximo dentro de los objetivos | ¿Cuánto trabajo puede procesar? |
| Escalabilidad | Rendimiento al añadir recursos | ¿Cómo mejora al escalar? |
| Rendimiento de frontend | Experiencia y respuesta del navegador | ¿La interfaz responde con fluidez? |

Estas pruebas pueden combinarse. Por ejemplo, una campaña de pruebas de carga puede incluir una fase estable, un pico repentino y una fase de recuperación.

---

## 6. Métricas fundamentales

Para interpretar correctamente los resultados es necesario medir más de una variable.

### 6.1. Tiempo de respuesta (*Response Time*)

Es el tiempo que transcurre desde que se inicia una operación hasta que se recibe la respuesta definida como final para esa medición.

Ejemplo:

Una petición tarda 250 ms en completarse.

Es importante definir qué se mide exactamente. La duración de una petición HTTP, el tiempo total de una operación de negocio y el tiempo percibido en el navegador pueden ser diferentes.

### 6.2. Latencia y percentiles

La latencia indica cuánto tarda una operación en responder.

Además de la media, conviene analizar los percentiles:

- **p50:** la mitad de las operaciones tarda ese tiempo o menos.
- **p90:** el 90 % tarda ese tiempo o menos.
- **p95:** el 95 % tarda ese tiempo o menos.
- **p99:** el 99 % tarda ese tiempo o menos.

Ejemplo ilustrativo:

| Métrica | Resultado |
|---|---:|
| p50 | 120 ms |
| p90 | 250 ms |
| p95 | 400 ms |
| p99 | 1.200 ms |

En este ejemplo, aunque muchas solicitudes responden rápidamente, una parte de ellas tarda bastante más.

Los percentiles altos ayudan a descubrir problemas que la media puede ocultar, especialmente cuando hay colas, bloqueos o dependencias lentas.

### 6.3. Throughput

Es la cantidad de trabajo completado por unidad de tiempo.

En una API puede expresarse como solicitudes por segundo (*requests per second*, RPS).

Ejemplo:

- 500 solicitudes por segundo.
- 2.000 operaciones por minuto.

El throughput debe interpretarse junto con la latencia y la tasa de errores. Procesar más solicitudes no significa necesariamente ofrecer un mejor servicio si una proporción importante falla.

### 6.4. Concurrencia

Representa cuántas operaciones o usuarios están activos simultáneamente, según la definición utilizada por la herramienta.

Es importante distinguir entre usuarios concurrentes y solicitudes por segundo. Un mismo usuario puede realizar muchas peticiones, y el número de peticiones completadas depende también de la latencia y del patrón de actividad.

### 6.5. Tasa de errores

Mide la proporción de operaciones que fallan según los criterios definidos.

Los errores pueden incluir:

- Respuestas HTTP de error.
- Timeouts.
- Fallos de conexión.
- Respuestas funcionalmente incorrectas.
- Operaciones incompletas.

Una respuesta HTTP `200` no demuestra necesariamente que la operación haya sido correcta. También deben validarse las respuestas funcionales relevantes.

### 6.6. Consumo de CPU

Permite detectar si el procesamiento está limitado por la capacidad de cálculo.

Una CPU saturada puede deberse a algoritmos costosos, serialización, consultas ineficientes, exceso de trabajo o concurrencia mal gestionada.

### 6.7. Memoria

Es importante observar tanto el consumo absoluto como su evolución.

Un crecimiento constante de memoria durante una prueba prolongada puede indicar una fuga o una acumulación de objetos, aunque es necesario analizar el comportamiento del recolector de basura y las características del runtime antes de concluir que existe un problema.

### 6.8. Recursos y dependencias

También conviene observar:

- Conexiones activas y disponibles.
- Latencia de consultas SQL.
- Bloqueos en la base de datos.
- Tamaño de colas.
- Tiempo de espera por recursos.
- Uso de red y almacenamiento.
- Errores de servicios externos.

Las métricas de la aplicación deben correlacionarse con las de su infraestructura para localizar la causa de una degradación.

---

## 7. SLA, SLO y criterios de aceptación

Las pruebas de rendimiento necesitan criterios objetivos para determinar si un resultado es aceptable.

### SLA: Service Level Agreement

Es un acuerdo de nivel de servicio que puede establecer compromisos entre un proveedor y un cliente.

### SLO: Service Level Objective

Es un objetivo de nivel de servicio medible, como una latencia máxima para un porcentaje de solicitudes o una tasa de éxito.

### Criterios de aceptación de rendimiento

Son las condiciones que debe cumplir una prueba para considerarse satisfactoria.

Por ejemplo, un equipo podría establecer para una API de consulta de productos:

- El p95 debe ser inferior a 300 ms.
- El p99 debe ser inferior a 800 ms.
- La tasa de errores debe ser inferior al 1 %.
- El sistema debe soportar 200 solicitudes por segundo.
- La prueba debe durar 15 minutos bajo una carga estable.

Estos valores son **ejemplos ilustrativos**, no estándares universales. Los objetivos deben basarse en las necesidades del producto, el presupuesto de latencia, el tráfico esperado y las capacidades de la infraestructura.

También es importante definir el entorno y el patrón de carga con los que se evaluarán los criterios.

---

## 8. Diseño de una prueba de rendimiento

Antes de generar carga, hay que definir qué se quiere comprobar.

### Paso 1. Identificar la operación crítica

Ejemplos:

- Inicio de sesión.
- Búsqueda de productos.
- Consulta de una cuenta.
- Creación de pedidos.
- Generación de informes.

### Paso 2. Definir la carga esperada

Determinar:

- Usuarios concurrentes.
- Solicitudes por segundo.
- Duración.
- Distribución de operaciones.
- Tamaño de datos.
- Patrón de llegada de solicitudes.

### Paso 3. Establecer objetivos

Definir límites de latencia, errores y throughput.

### Paso 4. Preparar el entorno

Utilizar una infraestructura representativa y controlar las dependencias relevantes.

### Paso 5. Ejecutar una prueba pequeña

Validar que el script funciona, que los datos son correctos y que la herramienta mide lo esperado.

### Paso 6. Aumentar la carga

Incrementar progresivamente la demanda para estudiar cómo evoluciona el sistema.

### Paso 7. Analizar los resultados

Relacionar latencia, errores, throughput y métricas de infraestructura.

### Paso 8. Optimizar y repetir

Introducir una mejora, repetir el mismo escenario y comparar los resultados bajo condiciones equivalentes.

---

## 9. Ejemplo práctico con Grafana k6

[k6](https://k6.io/docs/) es una herramienta de pruebas de rendimiento que permite definir escenarios mediante código y ejecutarlos localmente o en pipelines de automatización. Sus métricas y umbrales permiten convertir los objetivos de rendimiento en criterios de aceptación automatizables.

### Escenario

Queremos comprobar una API de consulta de productos.

Objetivos ilustrativos:

- Ejecutar una carga progresiva de hasta 20 usuarios virtuales.
- Mantener la carga durante un periodo breve.
- Comprobar que las respuestas son correctas.
- Mantener la tasa de errores por debajo del 1 %.
- Conseguir que el p95 de las solicitudes sea inferior a 500 ms.

### Script de ejemplo

Guarda el siguiente código como `performance-test.js`.

```javascript
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '15s', target: 10 },
    { duration: '30s', target: 20 },
    { duration: '15s', target: 0 },
  ],

  thresholds: {
    http_req_failed: ['rate<0.01'],
    http_req_duration: ['p(95)<500'],
  },
};

export default function () {
  const response = http.get(
    `${__ENV.BASE_URL}/api/products`
  );

  check(response, {
    'status is 200': (res) => res.status === 200,
  });

  sleep(1);
}
```

El script realiza peticiones a un endpoint de consulta y aplica criterios de aceptación a la tasa de errores y al percentil 95 de la duración de las peticiones.

La URL base se obtiene de la variable de entorno `BASE_URL`.

### Cómo ejecutarlo

Con k6 instalado y una API de prueba disponible:

```bash
BASE_URL=http://localhost:8080 k6 run performance-test.js
```

En Windows, la sintaxis para definir variables de entorno depende de la consola utilizada.

### ¿Qué hace cada parte?

- `stages`: incrementa y reduce gradualmente el número de usuarios virtuales.
- `http.get`: realiza una solicitud HTTP.
- `check`: comprueba el código de respuesta.
- `sleep(1)`: introduce una pausa entre iteraciones.
- `thresholds`: define los límites de rendimiento aceptables.

La configuración de etapas simula un perfil de carga con usuarios virtuales que repiten el escenario. No equivale a garantizar una tasa fija de solicitudes por segundo: esta depende de la latencia, la pausa y el trabajo ejecutado por cada usuario virtual.

Los umbrales de rendimiento pueden hacer que la ejecución termine con un estado de error si no se cumplen los criterios configurados. <https://grafana.com/docs/k6/latest/using-k6/thresholds/>

### Consideraciones importantes

- El endpoint debe existir y estar disponible.
- El entorno debe ser de pruebas o estar expresamente autorizado.
- La infraestructura de generación de carga debe tener recursos suficientes.
- Conviene utilizar datos y escenarios representativos.
- La prueba debe incluir comprobaciones funcionales relevantes.
- Los límites deben ajustarse a los objetivos reales del sistema.

Este script es un punto de partida. Para una prueba de carga representativa, normalmente habrá que incorporar autenticación, varias operaciones, datos de prueba y una distribución realista del tráfico.

---

## 10. Ejemplo de pruebas de rendimiento con Apache JMeter

Apache JMeter es una herramienta de código abierto para probar el rendimiento de aplicaciones y servicios. Puede utilizarse con HTTP, servicios REST, JDBC y otros protocolos. <https://jmeter.apache.org/>

### Elementos principales

Un plan de prueba típico contiene:

- **Test Plan:** configura el conjunto de la prueba.
- **Thread Group:** define el comportamiento de los usuarios virtuales.
- **Samplers:** realizan operaciones, como solicitudes HTTP.
- **Assertions:** verifican condiciones sobre las respuestas.
- **Listeners y reportes:** permiten observar y analizar los resultados.

### Ejemplo de escenario

Supongamos que queremos comprobar una API de consulta de productos.

1. Crear un Test Plan.
2. Añadir un Thread Group.
3. Definir un número de usuarios virtuales.
4. Configurar el periodo de incremento de usuarios.
5. Añadir una solicitud HTTP `GET /api/products`.
6. Configurar comprobaciones del código de respuesta.
7. Ejecutar una prueba pequeña para validar el plan.
8. Aumentar la carga de forma gradual.
9. Generar un informe HTML.
10. Analizar la latencia, los errores y el throughput.

Para pruebas de carga, JMeter recomienda ejecutar las pruebas desde la línea de comandos y no desde la interfaz gráfica, ya que la propia interfaz puede consumir recursos y afectar a los resultados. <https://jmeter.apache.org/usermanual/get-started.html>

JMeter es una buena opción cuando se necesita diseñar planes visualmente, utilizar protocolos variados o aprovechar escenarios existentes. k6 resulta atractivo cuando se prefiere mantener las pruebas como código.

---

## 11. Pruebas de rendimiento en aplicaciones Java y Spring Boot

En una aplicación Java, las pruebas de rendimiento pueden dirigirse a diferentes niveles.

### Nivel de API

Se mide el comportamiento de los endpoints HTTP bajo carga.

Puede revelar problemas relacionados con:

- Serialización JSON.
- Controladores.
- Filtros y middleware.
- Autenticación.
- Validación.
- Acceso a servicios.

### Nivel de base de datos

Se estudian las consultas, los índices, los bloqueos y las conexiones.

Conviene analizar las consultas lentas y observar cómo cambian la latencia y la saturación cuando aumenta la concurrencia.

### Nivel de JVM

Se observan métricas como:

- Uso de heap.
- Pausas del recolector de basura.
- Número de hilos.
- Consumo de CPU.
- Bloqueos.
- Tiempo de ejecución.

### Herramientas complementarias

- **Java Flight Recorder (JFR):** recopila información de diagnóstico y rendimiento de la JVM.
- **VisualVM:** ayuda a inspeccionar procesos Java y su consumo de recursos.
- **Micrometer:** permite instrumentar métricas en aplicaciones compatibles.
- **Prometheus y Grafana:** permiten almacenar, consultar y visualizar métricas.
- **OpenTelemetry:** facilita la instrumentación y correlación de trazas y métricas compatibles.

La elección depende de la infraestructura y del problema que se esté investigando.

### Ejemplo de diagnóstico

Si el p95 de una API aumenta de 200 ms a 1.500 ms durante una prueba, no basta con aumentar el número de instancias de forma automática.

Primero conviene comprobar:

1. Si la CPU está saturada.
2. Si aumenta el tiempo de las consultas SQL.
3. Si se agotan las conexiones disponibles.
4. Si se acumulan solicitudes en colas.
5. Si aparecen pausas prolongadas de la JVM.
6. Si una dependencia externa introduce latencia.
7. Si el generador de carga puede producir el tráfico previsto.

La optimización debe dirigirse a la causa demostrada, no a una suposición.

---

## 12. Cuellos de botella y optimización

Un cuello de botella es un recurso o componente que limita la capacidad del sistema.

### 12.1. CPU

Posibles causas:

- Algoritmos costosos.
- Cálculos redundantes.
- Serialización excesiva.
- Compresión intensiva.
- Trabajo innecesario por solicitud.

Posibles acciones:

- Perfilar el código.
- Mejorar algoritmos.
- Reducir cálculos repetidos.
- Revisar el procesamiento de datos.

### 12.2. Memoria

Posibles causas:

- Objetos retenidos innecesariamente.
- Cachés sin límites.
- Acumulación de resultados.
- Fugas de memoria.
- Estructuras de datos demasiado grandes.

Posibles acciones:

- Analizar perfiles de memoria.
- Revisar el ciclo de vida de los objetos.
- Limitar cachés y colas.
- Procesar grandes volúmenes de forma incremental.

### 12.3. Base de datos

Posibles causas:

- Consultas sin índices adecuados.
- Consultas N+1.
- Transacciones demasiado largas.
- Bloqueos.
- Pool de conexiones insuficiente o mal dimensionado.

Posibles acciones:

- Analizar los planes de ejecución.
- Revisar índices.
- Reducir consultas innecesarias.
- Ajustar las transacciones.
- Medir y dimensionar el pool de conexiones.

### 12.4. Red y servicios externos

Posibles causas:

- Latencia de proveedores.
- Respuestas muy grandes.
- Demasiadas llamadas secuenciales.
- Timeouts inadecuados.
- Reintentos que amplifican la carga.

Posibles acciones:

- Medir cada dependencia.
- Aplicar límites de tiempo.
- Utilizar concurrencia cuando sea segura.
- Considerar caché cuando los datos lo permitan.
- Evitar reintentos ilimitados.

### Regla fundamental

**Mide antes de optimizar.** Una mejora local no garantiza una mejora global. Por ejemplo, una caché puede reducir consultas a la base de datos, pero también incrementar el uso de memoria y añadir problemas de consistencia.

---

## 13. Pruebas de rendimiento y calidad del código

Las pruebas de rendimiento no sustituyen a las pruebas unitarias, funcionales ni al análisis estático. Complementan estas prácticas verificando el comportamiento del sistema bajo carga.

### Relación con Clean Code

Un código claro y con responsabilidades bien definidas facilita identificar las operaciones costosas y comprender su comportamiento.

### Relación con SOLID

- **SRP:** permite localizar las responsabilidades que consumen más recursos.
- **OCP:** facilita introducir estrategias de optimización sin modificar indiscriminadamente módulos existentes.
- **LSP:** ayuda a mantener contratos consistentes entre implementaciones intercambiables, que también deben medirse bajo condiciones comparables.
- **ISP:** evita contratos innecesariamente amplios, aunque su impacto real en rendimiento debe medirse.
- **DIP:** permite sustituir dependencias y diseñar pruebas controladas; para medir la latencia real de una dependencia, se necesita incluirla en un escenario apropiado.

### Relación con DRY, KISS y YAGNI

- **DRY:** reduce duplicación de lógica, pero no garantiza que el código sea más rápido.
- **KISS:** evita complejidad innecesaria que puede dificultar el diagnóstico y mantenimiento.
- **YAGNI:** evita introducir optimizaciones o infraestructuras complejas sin una necesidad demostrada.

No conviene aplicar estas prácticas como reglas mecánicas de rendimiento. Una abstracción bien diseñada puede mejorar el mantenimiento sin afectar de forma relevante al rendimiento, y una duplicación controlada no siempre representa un problema medible.

La decisión de optimizar debe apoyarse en perfiles, métricas y escenarios reproducibles.

---

## 14. Relación con SonarQube, cobertura y análisis estático

SonarQube ayuda a identificar problemas de calidad estática, como determinados defectos, vulnerabilidades, problemas de mantenibilidad y patrones de código no deseados.

Sin embargo, **SonarQube no sustituye a una herramienta de pruebas de rendimiento**.

Conviene distinguir:

| Herramienta o práctica | Qué aporta |
|---|---|
| SonarQube | Análisis estático y métricas de calidad configuradas |
| JUnit | Pruebas automatizadas de unidades de código |
| JaCoCo | Métricas de cobertura de ejecución |
| k6 o JMeter | Generación de carga y medición del comportamiento |
| JFR o un profiler | Información para diagnosticar el coste de ejecución |
| Prometheus y Grafana | Observabilidad y visualización de métricas |

Una cobertura elevada no demuestra que el sistema sea rápido. Del mismo modo, un buen resultado en SonarQube no garantiza que la aplicación pueda soportar el volumen de solicitudes esperado.

La calidad del software se evalúa combinando diferentes técnicas.

---

## 15. Automatización de pruebas de rendimiento en CI/CD

Las pruebas de rendimiento pueden integrarse en pipelines para detectar regresiones antes de publicar una nueva versión.

Un flujo habitual es:

1. Compilar el código.
2. Ejecutar las pruebas unitarias.
3. Ejecutar las pruebas de integración.
4. Ejecutar las pruebas funcionales prioritarias.
5. Desplegar la aplicación en un entorno de pruebas.
6. Ejecutar un escenario de rendimiento controlado.
7. Evaluar los umbrales de latencia y errores.
8. Publicar los informes.
9. Aplicar los criterios de calidad establecidos.

### Ejemplo conceptual con GitHub Actions y k6

```yaml
name: Performance Smoke Test

on:
  workflow_dispatch:

jobs:
  performance:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Install k6
        uses: grafana/setup-k6-action@v1

      - name: Run performance test
        uses: grafana/run-k6-action@v1
        with:
          path: performance-test.js
        env:
          BASE_URL: ${{ vars.TEST_BASE_URL }}
```

Este ejemplo es orientativo. Antes de incorporarlo a un repositorio real, hay que verificar las versiones y la configuración de las acciones utilizadas, además de garantizar que la aplicación de prueba está desplegada y accesible.

Para una prueba controlada de tipo *smoke*, puede utilizarse una carga pequeña que compruebe que el escenario funciona y que no existe una regresión evidente. Las pruebas de estrés y de resistencia suelen requerir una planificación y unos recursos mayores, por lo que no siempre es adecuado ejecutarlas en cada pull request.

### Buenas prácticas en CI/CD

- Empezar con pruebas breves y reproducibles.
- Definir umbrales explícitos.
- Mantener el entorno de prueba estable.
- Registrar la configuración y el perfil de carga.
- Comparar resultados entre versiones.
- Evitar que el generador de carga sea el cuello de botella.
- Separar las pruebas de regresión rápida de las campañas de rendimiento extensas.
- Guardar los informes necesarios para diagnosticar fallos.

---

## 16. Errores frecuentes

### Medir solo el tiempo medio

La media puede ocultar solicitudes muy lentas.

**Solución:** revisar percentiles como p95 y p99, además de la media.

### Generar una carga poco realista

Una prueba que repite una sola petición no representa necesariamente el comportamiento de usuarios reales.

**Solución:** modelar los flujos, las pausas, la mezcla de operaciones y los volúmenes de datos relevantes.

### Ejecutar las pruebas contra un entorno inadecuado

Una infraestructura diferente puede producir resultados que no se corresponden con producción.

**Solución:** documentar las diferencias y utilizar un entorno representativo cuando sea posible.

### No comprobar los errores funcionales

Una API podría responder rápidamente con errores y parecer eficiente.

**Solución:** medir simultáneamente latencia, errores y corrección funcional.

### Saturar el generador de carga

La herramienta que genera solicitudes puede alcanzar su límite antes que la aplicación.

**Solución:** vigilar también los recursos de los generadores y distribuir la carga cuando sea necesario.

### Optimizar sin identificar la causa

Cambiar código o infraestructura sin diagnóstico puede añadir complejidad sin mejorar el resultado.

**Solución:** utilizar métricas y perfiles para localizar el cuello de botella.

### Comparar resultados de pruebas incompatibles

Cambios en el entorno, datos o perfil de carga pueden explicar las diferencias.

**Solución:** mantener condiciones comparables y registrar los cambios realizados.

### Ejecutar pruebas intensivas sin autorización

Una prueba de estrés puede degradar un servicio o afectar a otros usuarios.

**Solución:** utilizar entornos autorizados, acordar límites de carga y definir criterios de parada.

---

## 17. Estrategia para introducir pruebas de rendimiento

### Fase 1: identificar operaciones críticas

Seleccionar las operaciones que tienen mayor impacto en la experiencia del usuario o en el negocio.

### Fase 2: definir objetivos medibles

Establecer límites de latencia, throughput y errores, además de las condiciones bajo las que deben cumplirse.

### Fase 3: construir escenarios representativos

Modelar los patrones de uso más importantes, incluyendo autenticación, consultas y operaciones de escritura cuando corresponda.

### Fase 4: establecer una línea base

Ejecutar una prueba inicial y guardar sus métricas para poder comparar resultados futuros.

### Fase 5: automatizar pruebas pequeñas

Incorporar comprobaciones de rendimiento rápidas en el proceso de desarrollo o integración.

### Fase 6: programar campañas extensas

Ejecutar pruebas de carga, estrés, picos y resistencia en entornos adecuados, con la frecuencia que requiera el riesgo del sistema.

### Fase 7: analizar y optimizar

Relacionar las métricas de respuesta con las de aplicación e infraestructura.

### Fase 8: repetir y comparar

Después de cada optimización, repetir el escenario con condiciones equivalentes para comprobar si la mejora es real.

---

## 18. Checklist de revisión

Antes de dar por válida una campaña de rendimiento, comprueba:

- Se ha definido el objetivo de la prueba.
- Existe un perfil de carga documentado.
- Los datos son representativos.
- El entorno y sus diferencias están documentados.
- Se miden latencia y percentiles.
- Se mide el throughput.
- Se controla la tasa de errores.
- Se monitorizan los recursos relevantes.
- El generador de carga tiene capacidad suficiente.
- Existen umbrales de aceptación.
- La prueba puede repetirse de forma comparable.
- Se conservan los resultados.
- Se han revisado los cuellos de botella antes de optimizar.
- Las pruebas intensivas se ejecutan en entornos autorizados.
- Se ha comprobado la recuperación del sistema cuando el escenario lo requiere.

---

## 19. Conclusión

Las pruebas de rendimiento permiten determinar si una aplicación satisface sus objetivos de velocidad, capacidad y estabilidad bajo unas condiciones de carga concretas.

Son especialmente importantes en aplicaciones con un volumen elevado de solicitudes, procesos de negocio críticos, APIs, sistemas distribuidos y servicios que deben mantener tiempos de respuesta previsibles.

Una estrategia eficaz combina diferentes tipos de prueba:

- Pruebas de carga para validar la demanda esperada.
- Pruebas de estrés para identificar límites.
- Pruebas de picos para comprobar aumentos repentinos.
- Pruebas de resistencia para detectar degradaciones prolongadas.
- Pruebas de capacidad y escalabilidad para planificar el crecimiento.
- Monitorización y profiling para diagnosticar los problemas encontrados.

La clave es definir objetivos medibles, utilizar escenarios representativos y tomar decisiones basadas en datos.

Las pruebas de rendimiento no consisten únicamente en enviar muchas peticiones: consisten en comprobar que el sistema puede ofrecer el nivel de servicio esperado de forma estable, eficiente y reproducible.

---

## 20. Referencias oficiales

- Grafana k6: https://k6.io/docs/
- k6, métricas y umbrales: https://grafana.com/docs/k6/latest/using-k6/metrics/
- k6, pruebas de carga de API: https://grafana.com/docs/k6/latest/testing-guides/api-load-testing/
- Apache JMeter: https://jmeter.apache.org/
- Manual de Apache JMeter: https://jmeter.apache.org/usermanual/
- Gatling: https://docs.gatling.io/
- Java Flight Recorder: https://docs.oracle.com/en/java/javase/
- Spring Boot Actuator: https://docs.spring.io/spring-boot/reference/actuator/
- OpenTelemetry: https://opentelemetry.io/docs/
- Prometheus: https://prometheus.io/docs/
- Grafana: https://grafana.com/docs/
- SonarQube: https://docs.sonarsource.com/sonarqube-server/
