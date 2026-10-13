# SDD — Spec-Driven Development
## Desarrollo guiado por especificaciones en ingeniería del software

## 1. Introducción

**SDD (*Spec-Driven Development*)** es un enfoque de desarrollo de software en el que las especificaciones explícitas de un sistema o funcionalidad guían las decisiones de diseño, implementación y validación.

En lugar de comenzar directamente a programar a partir de una idea general, se define primero qué debe hacer el software, qué restricciones debe cumplir, cómo se comprobará su comportamiento y qué condiciones permitirán considerar terminado el trabajo.

A partir de esa definición, se prepara un plan técnico, se descompone el trabajo en tareas y se implementa una solución verificable.

La especificación no es simplemente un documento que se escribe al principio y después se olvida. En un proceso SDD bien aplicado, se mantiene alineada con el código y sirve como referencia para comprobar que la implementación continúa satisfaciendo los requisitos.

SDD cobra especial relevancia en el desarrollo asistido por inteligencia artificial, porque una especificación estructurada proporciona contexto y reduce la ambigüedad de las instrucciones que recibe un agente de programación.

Una implementación conocida de este enfoque es **GitHub Spec Kit**, que estructura el proceso mediante fases como especificar, planificar, descomponer en tareas, implementar y comprobar la convergencia entre el resultado y los requisitos. [Documentación oficial de Spec Kit](https://github.com/github/spec-kit)

### 1.1. Objetivos principales

- Convertir ideas de negocio en requisitos claros y verificables.
- Reducir ambigüedades antes de comenzar a programar.
- Separar las necesidades funcionales de las decisiones técnicas.
- Facilitar la comunicación entre desarrolladores, responsables de producto, QA y otras partes interesadas.
- Relacionar requisitos, decisiones de diseño, tareas y pruebas.
- Detectar contradicciones antes de que se conviertan en errores de implementación.
- Mejorar la planificación y la estimación del trabajo.
- Facilitar el uso controlado de herramientas de desarrollo asistidas por IA.
- Mantener la evolución del software alineada con la intención original.

### 1.2. Idea fundamental

> Primero define qué debe hacer el sistema y cómo comprobarás que lo hace correctamente; después diseña e implementa la solución.

Esto no significa que debamos conocer todos los detalles antes de escribir código. Las especificaciones pueden evolucionar y descubrirse nuevos requisitos durante el desarrollo.

El objetivo es que las decisiones importantes sean explícitas y que los cambios se incorporen de forma controlada.

### 1.3. ¿SDD es una metodología formal única?

No existe una única secuencia universal que todos los equipos deban seguir para aplicar SDD.

Es más preciso considerarlo un **enfoque de desarrollo guiado por especificaciones**, que puede adaptarse a diferentes ciclos de vida, arquitecturas y herramientas.

Por ejemplo, un equipo puede aplicar SDD con documentos Markdown, historias de usuario y pruebas automatizadas, mientras que otro puede utilizar herramientas especializadas para generar planes, tareas y validaciones a partir de las especificaciones.

Lo esencial es que la especificación sea una referencia útil y mantenida durante el desarrollo, no la herramienta concreta elegida.

---

## 2. ¿Qué problema resuelve SDD?

En muchos proyectos, el desarrollo comienza con una petición poco detallada:

> «Necesitamos que los usuarios puedan recuperar su contraseña».

Aunque la petición parece clara, deja numerosas preguntas abiertas:

- ¿Cómo se identifica al usuario?
- ¿Se envía un enlace o un código?
- ¿Cuánto tiempo puede utilizarse?
- ¿Qué ocurre si se solicita varias veces?
- ¿Cómo se impiden los ataques de fuerza bruta?
- ¿Qué mensaje se muestra si el correo no existe?
- ¿Qué pruebas deben superarse?
- ¿Qué requisitos de seguridad deben cumplirse?

Si estas decisiones se toman de manera improvisada durante la implementación, diferentes desarrolladores pueden construir comportamientos incompatibles con las expectativas del producto.

### 2.1. Problemas habituales

**Ambigüedad en los requisitos.** Una descripción general permite múltiples interpretaciones.

**Cambios costosos.** Un requisito descubierto tarde puede obligar a modificar el diseño, el código, las pruebas y la documentación.

**Falta de trazabilidad.** Puede ser difícil determinar qué requisito justifica una funcionalidad o qué pruebas demuestran que está implementada.

**Desalineación entre equipos.** Producto, desarrollo y QA pueden tener interpretaciones distintas del comportamiento esperado.

**Implementaciones incompletas.** El código puede funcionar en el caso principal y fallar en los casos límite que nunca se especificaron.

**Problemas con la IA.** Un agente puede completar los detalles que faltan mediante suposiciones y producir código técnicamente correcto, pero contrario a las necesidades reales.

### 2.2. Cómo ayuda SDD

SDD introduce una referencia explícita antes de implementar:

1. Define la necesidad y el comportamiento esperado.
2. Aclara las reglas, restricciones y casos límite relevantes.
3. Diseña una solución técnica coherente con esos requisitos.
4. Divide la implementación en tareas comprobables.
5. Valida el código frente a los criterios de aceptación.
6. Actualiza la especificación cuando cambian los requisitos.

El resultado esperado es reducir las interpretaciones contradictorias y mejorar la capacidad de verificar el software.

---

## 3. Principios fundamentales de SDD

### 3.1. La intención debe estar explícita

Los requisitos importantes no deberían depender exclusivamente de conversaciones informales, memoria individual o suposiciones.

Conviene registrar las decisiones que afectan al comportamiento, la seguridad, las integraciones o las restricciones del sistema.

### 3.2. Separar el qué del cómo

La especificación funcional describe qué necesita el usuario y cómo debe comportarse el sistema.

El plan técnico describe cómo se implementará ese comportamiento.

Por ejemplo:

- **Especificación:** un usuario con una cuenta activa puede solicitar un enlace para restablecer su contraseña.
- **Plan técnico:** generar un token aleatorio, almacenarlo de forma segura y enviar un correo mediante un proveedor configurado.

La primera afirmación describe el comportamiento esperado. La segunda es una decisión de implementación.

Esta separación permite cambiar la tecnología sin modificar innecesariamente el requisito funcional.

### 3.3. Los requisitos deben ser verificables

Una especificación útil permite comprobar si el software cumple lo solicitado.

Por ejemplo, «el sistema debe ser rápido» es demasiado ambiguo.

Una especificación más verificable podría establecer que, bajo una carga y un entorno definidos, el percentil 95 de las respuestas de una operación debe ser inferior a 300 milisegundos.

Los valores concretos deben proceder de las necesidades del proyecto, no inventarse como objetivos universales.

### 3.4. La especificación evoluciona

Los requisitos pueden cambiar cuando aparecen nuevos datos, limitaciones técnicas o necesidades de negocio.

SDD no exige congelar los requisitos para siempre. Exige que los cambios relevantes se reflejen en los artefactos afectados y que se comprueben sus consecuencias.

### 3.5. La implementación debe demostrar conformidad

No basta con afirmar que una funcionalidad está terminada porque existe código para ella.

Es necesario comprobar, según el riesgo y el alcance del proyecto, que se satisfacen los requisitos mediante pruebas, análisis, revisión y otras evidencias apropiadas.

### 3.6. La especificación necesita revisión humana

Una especificación puede contener errores, contradicciones o requisitos imposibles de cumplir.

Si se utiliza IA para generarla, su contenido debe revisarse antes de convertirlo en un plan de implementación.

Automatizar el proceso no elimina la responsabilidad de validar las decisiones.

---

## 4. Ciclo de vida de SDD

Un proceso SDD puede representarse mediante las siguientes fases:

```text
             Idea o necesidad
                    |
                    v
           1. Especificación
                    |
                    v
         2. Aclaración y revisión
                    |
                    v
           3. Plan técnico
                    |
                    v
          4. Descomposición
              en tareas
                    |
                    v
          5. Implementación
                    |
                    v
          6. Verificación
                    |
                    v
       ¿Cumple la especificación?
              /           \
            No             Sí
            |               |
            v               v
     Corregir código,    Revisar y
     plan o requisitos   aceptar
            |               |
            v               v
       Volver a validar   Entregar
```

Las fases pueden repetirse y adaptarse al contexto. En proyectos pequeños, algunas se pueden combinar; en sistemas críticos, pueden requerirse revisiones y evidencias más rigurosas.

### 4.1. Fase 1: especificación

Se define el problema, los usuarios afectados, el comportamiento esperado y las restricciones.

**Entregable habitual:** `spec.md`.

### 4.2. Fase 2: aclaración y revisión

Se identifican ambigüedades, contradicciones, casos límite y requisitos ausentes.

**Entregables posibles:** una especificación revisada y una lista de preguntas resueltas.

### 4.3. Fase 3: planificación técnica

Se decide la arquitectura, las tecnologías, las integraciones, los modelos de datos y los contratos necesarios.

**Entregables habituales:** `plan.md`, diagramas y contratos técnicos.

### 4.4. Fase 4: descomposición en tareas

Se convierte el plan en tareas pequeñas, ordenadas por dependencias y asociadas a requisitos o historias de usuario.

**Entregable habitual:** `tasks.md`.

### 4.5. Fase 5: implementación

Se desarrolla el código siguiendo el plan y las tareas. Las pruebas se ejecutan durante la implementación, no únicamente al final.

### 4.6. Fase 6: verificación y convergencia

Se comprueba que el código satisface los requisitos, que las tareas están completadas y que no quedan diferencias relevantes entre lo especificado y lo implementado.

Si aparecen discrepancias, se determina si hay que corregir el código, ajustar el plan o revisar formalmente el requisito.

---

## 5. Artefactos principales de SDD

Los nombres y formatos pueden variar, pero un proyecto suele beneficiarse de los siguientes artefactos.

| Artefacto | Propósito | Contenido habitual |
|---|---|---|
| `spec.md` | Definir el comportamiento esperado | Objetivos, usuarios, reglas y criterios de aceptación |
| `plan.md` | Explicar la solución técnica | Arquitectura, tecnologías, dependencias y decisiones |
| `tasks.md` | Organizar el trabajo | Tareas, prioridades, dependencias y validaciones |
| `data-model.md` | Describir los datos relevantes | Entidades, atributos, relaciones y restricciones |
| `contracts/` | Definir contratos entre componentes | APIs, eventos, esquemas y formatos |
| Pruebas automatizadas | Verificar comportamientos | Pruebas unitarias, de integración y aceptación |
| Registro de decisiones | Conservar decisiones importantes | Contexto, alternativas y justificación |

No es necesario crear todos estos archivos en cada proyecto. La documentación debe ser proporcional a la complejidad y al riesgo.

### 5.1. Relación entre los artefactos

```text
spec.md
   |
   | Define los requisitos
   v
plan.md
   |
   | Define la solución
   v
tasks.md
   |
   | Organiza la implementación
   v
Código + pruebas
   |
   | Producen evidencias
   v
Validación frente a spec.md
```

Los artefactos no deben convertirse en documentos aislados que evolucionan de manera independiente. Sus relaciones deben poder revisarse.

---

## 6. Ejemplo completo de SDD: recuperación de contraseña

Vamos a desarrollar un ejemplo sencillo para comprender cómo se pasa de una petición general a una especificación verificable.

### 6.1. Petición inicial

> Permitir que los usuarios recuperen su contraseña cuando no puedan acceder a su cuenta.

Esta petición describe una necesidad, pero todavía no define un comportamiento completo.

### 6.2. Especificación funcional

Un archivo `spec.md` podría contener lo siguiente:

```markdown
# Especificación: recuperación de contraseña

## 1. Objetivo

Permitir que un usuario recupere el acceso a su cuenta
sin conocer su contraseña actual.

## 2. Actores

- Usuario registrado.
- Servicio de autenticación.
- Servicio de correo electrónico.

## 3. Alcance

- Solicitar un enlace de recuperación.
- Validar el token recibido.
- Establecer una nueva contraseña.
- Invalidar el token después de utilizarlo.

## 4. Requisitos funcionales

RF-01:
El sistema debe permitir solicitar la recuperación
mediante una dirección de correo electrónico.

RF-02:
La respuesta pública no debe revelar si la dirección
está registrada.

RF-03:
El enlace debe utilizar un token aleatorio, de un solo
uso y con caducidad definida.

RF-04:
El sistema debe permitir establecer una nueva contraseña
cuando el token sea válido.

RF-05:
El token debe invalidarse después de una recuperación
correcta.

RF-06:
El sistema debe rechazar tokens caducados, inválidos
o ya utilizados.

## 5. Requisitos no funcionales

RNF-01:
Los tokens deben generarse de forma criptográficamente
segura.

RNF-02:
Los tokens no deben almacenarse en texto plano si
pueden almacenarse de forma segura mediante un resumen.

RNF-03:
Las solicitudes deben estar protegidas frente a abusos
mediante controles de frecuencia apropiados.

RNF-04:
Las contraseñas deben almacenarse mediante un algoritmo
de hash adecuado para contraseñas.

## 6. Criterios de aceptación

CA-01:
Dada una solicitud de recuperación,
cuando se proporciona una dirección válida,
el sistema devuelve la respuesta pública definida.

CA-02:
Cuando el token es válido y no ha caducado,
el usuario puede establecer una nueva contraseña.

CA-03:
Cuando el token ha caducado,
el sistema rechaza la operación.

CA-04:
Cuando el token ya se ha utilizado,
el sistema rechaza un segundo uso.

CA-05:
La respuesta pública no revela si existe una cuenta
asociada a la dirección solicitada.

## 7. Fuera de alcance

- Inicio de sesión mediante proveedores externos.
- Recuperación mediante SMS.
- Autenticación multifactor adicional.
```

Esta especificación es un ejemplo didáctico. Antes de utilizarla en producción, habría que concretar aspectos como la duración del token, las reglas de contraseña, los límites de solicitudes, la respuesta HTTP y el comportamiento frente a fallos de correo.

### 6.3. ¿Por qué es mejor que la petición inicial?

Porque convierte una necesidad general en comportamientos que se pueden diseñar, implementar y verificar.

Además, los requisitos funcionales están separados de las restricciones de seguridad y de los aspectos que quedan fuera del alcance.

Esto facilita detectar qué decisiones siguen pendientes antes de programar.

---

## 7. De la especificación al plan técnico

Una vez revisados los requisitos, se prepara el plan técnico.

Un ejemplo de `plan.md` sería:

```markdown
# Plan técnico: recuperación de contraseña

## 1. Arquitectura

El flujo se implementará en el backend existente,
manteniendo la lógica de negocio separada de los
detalles de persistencia y del proveedor de correo.

## 2. Componentes

- PasswordRecoveryController
- PasswordRecoveryService
- PasswordResetTokenRepository
- PasswordHasher
- EmailSender

Los nombres son orientativos y deberán adaptarse
a la estructura real del proyecto.

## 3. Modelo de datos

PasswordResetToken:
- id
- userId
- tokenHash
- expiresAt
- usedAt
- createdAt

## 4. Flujo de recuperación

1. Recibir la solicitud.
2. Aplicar los controles de frecuencia.
3. Buscar la cuenta sin revelar su existencia
   en la respuesta pública.
4. Generar un token seguro.
5. Almacenar su resumen y fecha de caducidad.
6. Enviar el enlace de recuperación.
7. Validar el token cuando el usuario lo utilice.
8. Actualizar la contraseña de forma segura.
9. Invalidar el token en una operación consistente.

## 5. Seguridad

- Utilizar un generador criptográfico seguro.
- Limitar la vigencia del token.
- Impedir su reutilización.
- Evitar registrar tokens o contraseñas en logs.
- Aplicar controles de frecuencia.
- Evitar respuestas que permitan enumerar usuarios.

## 6. Validación

- Pruebas unitarias del servicio.
- Pruebas de integración con la persistencia.
- Pruebas del flujo completo.
- Pruebas de expiración y reutilización.
- Pruebas de los controles de seguridad.
```

### 7.1. Diferencia entre especificación y plan

La especificación dice qué comportamiento debe cumplir el sistema.

El plan explica cómo se implementará ese comportamiento en la arquitectura elegida.

Esta separación permite revisar la solución técnica sin perder de vista el objetivo funcional.

También permite descubrir si el plan no cubre algún requisito de la especificación.

---

## 8. Descomposición del trabajo en tareas

El siguiente paso es transformar el plan en tareas ejecutables.

Ejemplo de `tasks.md`:

```markdown
# Tareas: recuperación de contraseña

## Fase 1: modelo y persistencia

- T001 Crear el modelo PasswordResetToken.
- T002 Crear la migración de la tabla.
- T003 Implementar el repositorio de tokens.
- T004 Añadir restricciones de persistencia necesarias.

## Fase 2: lógica de negocio

- T005 Implementar la generación de tokens seguros.
- T006 Implementar el almacenamiento de su resumen.
- T007 Implementar la validación de expiración.
- T008 Implementar la invalidación tras su uso.

## Fase 3: integración

- T009 Implementar el endpoint de solicitud.
- T010 Integrar el proveedor de correo.
- T011 Implementar el endpoint de restablecimiento.
- T012 Integrar el algoritmo de hash de contraseñas.

## Fase 4: validación

- T013 Probar tokens válidos.
- T014 Probar tokens caducados.
- T015 Probar tokens reutilizados.
- T016 Verificar que la respuesta pública no revela la existencia de una cuenta.
- T017 Verificar los controles de frecuencia.
```

Esta lista es orientativa. En un proyecto real habría que ajustar el orden, las dependencias y el nivel de detalle.

Las tareas pueden relacionarse con los requisitos mediante identificadores, por ejemplo `RF-03`, `RF-05` o `CA-04`. De este modo, es más fácil identificar qué debe cambiar cuando se modifica un requisito.

---

## 9. Trazabilidad entre requisitos, código y pruebas

Una de las prácticas más útiles en SDD es la trazabilidad.

La trazabilidad permite relacionar cada requisito con las decisiones de diseño, los componentes que lo implementan y las pruebas que aportan evidencia de su cumplimiento.

### 9.1. Ejemplo de matriz de trazabilidad

| Requisito | Componente relacionado | Prueba de verificación |
|---|---|---|
| RF-01: solicitar recuperación | Controlador y servicio | Solicitud de recuperación |
| RF-03: token de un solo uso y con caducidad | Servicio y repositorio | Token caducado y reutilizado |
| RF-04: establecer nueva contraseña | Servicio de recuperación | Cambio correcto de contraseña |
| RF-05: invalidar el token | Servicio y persistencia | Segundo intento rechazado |
| RF-02: no revelar existencia de la cuenta | Controlador | Comparación de respuestas públicas |

La relación no siempre es uno a uno. Un requisito puede necesitar varios componentes y varias pruebas.

### 9.2. ¿Por qué importa?

La trazabilidad ayuda a responder preguntas como:

- ¿Qué parte del sistema implementa este requisito?
- ¿Qué pruebas verifican su cumplimiento?
- ¿Qué requisitos podrían verse afectados por este cambio?
- ¿Qué comportamiento está implementado, pero no tiene una justificación clara?
- ¿Qué requisitos todavía no tienen evidencia suficiente?

En sistemas regulados, críticos o con contratos formales, esta capacidad puede ser especialmente importante.

En proyectos pequeños, una matriz sencilla o referencias dentro de las pruebas pueden ser suficientes.

---

## 10. Criterios de aceptación y especificaciones verificables

Los criterios de aceptación describen las condiciones que debe satisfacer una funcionalidad para considerarse aceptable.

Deben ser suficientemente concretos para que diferentes personas puedan evaluar el resultado de manera coherente.

### 10.1. Ejemplo ambiguo

> El sistema debe permitir restablecer contraseñas de forma segura y rápida.

Esta descripción deja sin definir qué significa «segura» y qué significa «rápida».

### 10.2. Ejemplo más verificable

```gherkin
Feature: Recuperación de contraseña

  Scenario: Token válido
    Given que existe una solicitud de recuperación válida
    And el token no ha caducado
    When el usuario establece una nueva contraseña válida
    Then la contraseña debe actualizarse
    And el token debe quedar invalidado

  Scenario: Token caducado
    Given que el token ha superado su fecha de caducidad
    When el usuario intenta cambiar la contraseña
    Then la operación debe rechazarse

  Scenario: Token reutilizado
    Given que el token ya se ha utilizado correctamente
    When el usuario vuelve a enviar el mismo token
    Then la operación debe rechazarse
```

Gherkin es un formato habitual para expresar escenarios de comportamiento mediante `Given`, `When` y `Then`. Puede utilizarse con herramientas de pruebas de aceptación, aunque escribir escenarios en Gherkin no implica que sean automáticamente ejecutables.

### 10.3. Qué hace bueno a un criterio de aceptación

Un criterio útil debe ser:

- Claro.
- Relevante para un requisito.
- Verificable.
- Lo suficientemente específico para evitar interpretaciones contradictorias.
- Independiente de detalles técnicos innecesarios, cuando sea posible.

La especificación debe evitar tanto la vaguedad como el exceso de detalle irrelevante.

---

## 11. Requisitos funcionales y no funcionales

SDD debe contemplar tanto el comportamiento funcional como las propiedades que condicionan su implementación.

### 11.1. Requisitos funcionales

Describen qué debe hacer el sistema.

Ejemplos:

- Permitir crear una cuenta.
- Permitir consultar un pedido.
- Rechazar una operación no autorizada.
- Enviar una notificación tras completar una compra.

### 11.2. Requisitos no funcionales

Describen propiedades, restricciones o niveles de calidad que debe cumplir el sistema.

Ejemplos:

- Rendimiento.
- Seguridad.
- Disponibilidad.
- Accesibilidad.
- Escalabilidad.
- Auditabilidad.
- Compatibilidad.
- Privacidad.

### 11.3. Requisitos medibles

Cuando una propiedad debe medirse, conviene especificar las condiciones de evaluación.

Por ejemplo:

- Percentil de latencia que se medirá.
- Volumen de solicitudes esperado.
- Entorno de ejecución.
- Porcentaje de disponibilidad requerido.
- Condiciones de carga.
- Método de medición.

No todas las propiedades pueden resumirse en una única métrica, pero deben describirse de manera que puedan revisarse razonablemente.

---

## 12. Relación con TDD, BDD y desarrollo ágil

SDD se puede combinar con otras prácticas de ingeniería del software. No es necesario elegir exclusivamente una de ellas.

### 12.1. SDD frente a TDD

TDD significa *Test-Driven Development*, o desarrollo guiado por pruebas.

En TDD, el desarrollador sigue habitualmente el ciclo:

1. Escribir una prueba que falla.
2. Implementar el código mínimo para que pase.
3. Refactorizar.
4. Repetir.

SDD se centra en que las especificaciones guíen el desarrollo y la validación.

| Aspecto | SDD | TDD |
|---|---|---|
| Foco | Requisitos y especificaciones como guía del desarrollo | Pruebas automatizadas como motor del ciclo de implementación |
| Artefacto central | Especificación y artefactos derivados | Pruebas automatizadas |
| Pregunta principal | ¿Qué debe hacer el sistema? | ¿Qué comportamiento debe pasar ahora y cómo lo implementamos? |
| Relación | Puede definir los requisitos que deben probarse | Puede convertir esos requisitos en ciclos concretos de implementación |

**SDD y TDD son complementarios.** Una especificación puede definir el comportamiento esperado, y TDD puede ayudar a implementarlo de manera incremental.

### 12.2. SDD frente a BDD

BDD significa *Behavior-Driven Development*, o desarrollo guiado por el comportamiento.

BDD utiliza ejemplos de comportamiento para facilitar el entendimiento compartido entre negocio, desarrollo y pruebas.

SDD es más amplio: puede incluir comportamiento, arquitectura, contratos, restricciones técnicas, requisitos no funcionales y planes de implementación.

Los escenarios BDD pueden ser parte de una especificación SDD.

### 12.3. SDD y metodologías ágiles

SDD no obliga a utilizar un proceso secuencial rígido ni sustituye necesariamente Scrum, Kanban o XP.

Puede aplicarse de forma incremental:

- Se especifica una funcionalidad.
- Se revisa y planifica.
- Se implementa.
- Se valida.
- Se incorpora la retroalimentación.
- Se actualiza la especificación si cambian los requisitos.

La documentación debe tener el nivel de detalle necesario para facilitar el trabajo, no convertirse en un fin en sí misma.

---

## 13. SDD y los principios SOLID, KISS, DRY y YAGNI

Los principios de diseño y SDD se complementan porque operan en niveles diferentes.

| Principio o enfoque | Qué aporta |
|---|---|
| SDD | Define y mantiene explícito qué debe cumplir el sistema |
| SOLID | Ayuda a organizar responsabilidades y dependencias |
| KISS | Evita complejidad innecesaria en las soluciones |
| DRY | Evita duplicar conocimiento que debe mantenerse coherente |
| YAGNI | Evita implementar funcionalidades hipotéticas |
| Clean Code | Mejora la legibilidad y el mantenimiento |
| TDD | Permite desarrollar mediante ciclos guiados por pruebas |

### 13.1. SDD y KISS

La especificación define las necesidades reales; KISS ayuda a encontrar una implementación comprensible que las satisfaga.

Una especificación clara no debe traducirse automáticamente en una arquitectura compleja.

### 13.2. SDD y YAGNI

SDD ayuda a distinguir entre requisitos confirmados y funcionalidades fuera de alcance.

YAGNI invita a no implementar estas últimas sin una justificación posterior.

Sin embargo, una especificación también debe incluir las restricciones necesarias de seguridad, rendimiento y operación, aunque no sean funcionalidades visibles para el usuario.

### 13.3. SDD y SOLID

La especificación establece el comportamiento y las restricciones. SOLID puede ayudar a decidir cómo distribuir responsabilidades y dependencias en el diseño.

No es necesario aplicar todos los mecanismos posibles de SOLID a cada módulo. El diseño debe responder a los requisitos y al contexto del proyecto.

### 13.4. SDD y DRY

La documentación y las pruebas no deberían duplicar reglas de forma que puedan contradecirse.

Conviene mantener referencias y una fuente de verdad clara para cada tipo de información.

Por ejemplo, una regla de negocio puede describirse en la especificación y verificarse mediante pruebas, pero los documentos deben revisarse cuando esa regla cambie.

---

## 14. SDD aplicado al desarrollo asistido por IA

SDD resulta especialmente útil cuando se utilizan agentes de programación que pueden generar o modificar múltiples archivos.

Una petición breve como:

> «Crea un sistema de reservas para restaurantes».

deja abiertas numerosas decisiones sobre usuarios, disponibilidad, horarios, cancelaciones, concurrencia, pagos y políticas de negocio.

Un agente puede asumir valores por su cuenta y producir una implementación coherente internamente, pero incorrecta respecto a las necesidades reales.

### 14.1. Flujo recomendado con IA

```text
Petición del usuario
        |
        v
Generación de borrador de especificación
        |
        v
Revisión y aclaración humana
        |
        v
Plan técnico revisado
        |
        v
Tareas de implementación
        |
        v
Generación o modificación del código
        |
        v
Ejecución de pruebas y análisis
        |
        v
Comparación con la especificación
        |
        v
Correcciones y nueva validación
```

### 14.2. Ventajas

- Proporciona contexto más estable entre sesiones.
- Reduce la necesidad de repetir instrucciones extensas.
- Facilita detectar requisitos olvidados.
- Ayuda a dividir el trabajo en unidades verificables.
- Permite revisar si el código implementa todos los requisitos.
- Facilita mantener coherencia en proyectos con muchos archivos.

### 14.3. Riesgos

SDD no garantiza que una IA genere código correcto.

Entre los riesgos se encuentran:

- Requisitos incompletos.
- Especificaciones contradictorias.
- Decisiones técnicas inventadas.
- Pruebas que no cubren los requisitos.
- Cambios de código que no se reflejan en la documentación.
- Interpretaciones incorrectas de las restricciones de seguridad.

Por eso, las especificaciones y los resultados deben revisarse, y las pruebas deben ejecutarse en un entorno apropiado.

### 14.4. La IA no sustituye el criterio de ingeniería

La IA puede ayudar a redactar especificaciones, proponer alternativas y generar código.

La responsabilidad de confirmar los requisitos, aceptar las decisiones arquitectónicas y validar el resultado sigue correspondiendo al equipo responsable del software.

---

## 15. Herramientas para aplicar SDD

SDD puede practicarse sin una herramienta especializada, aunque existen herramientas que automatizan parte del proceso.

### 15.1. Documentación en Markdown

Una estructura inicial podría ser:

```text
project/
├── docs/
│   ├── architecture.md
│   ├── decisions/
│   └── glossary.md
├── specs/
│   └── password-recovery/
│       ├── spec.md
│       ├── plan.md
│       ├── tasks.md
│       └── contracts/
├── src/
└── tests/
```

Esta estructura es orientativa. Conviene adaptarla al repositorio y evitar duplicar documentos que ya tienen una ubicación establecida.

### 15.2. Control de versiones

Guardar las especificaciones en Git permite revisar cambios, conocer su autoría y relacionarlos con tareas o solicitudes de modificación.

También facilita revisar qué requisitos cambiaron junto con una implementación.

### 15.3. GitHub Spec Kit

[GitHub Spec Kit](https://github.com/github/spec-kit) es una herramienta de código abierto que ayuda a estructurar flujos SDD y a trabajar con agentes de programación.

Su flujo principal incluye fases como:

- `specify`: definir qué se va a construir.
- `plan`: elaborar el plan técnico.
- `tasks`: descomponer el trabajo.
- `implement`: ejecutar la implementación.
- `converge`: comprobar que el resultado converge con la especificación y el plan.

El flujo completo puede incorporar pasos adicionales de aclaración, listas de comprobación y análisis de consistencia. Los nombres exactos y las opciones dependen de la versión y de la integración utilizada. [Guía de inicio de Spec Kit](https://github.com/github/spec-kit/blob/main/docs/quickstart.md)

Una instalación documentada en su guía de inicio utiliza:

```bash
uv tool install specify-cli
specify init mi-proyecto --integration copilot
```

Después se entra en el proyecto y se utiliza el agente seleccionado siguiendo los pasos documentados para esa integración. La instalación y los comandos disponibles pueden cambiar entre versiones, por lo que conviene consultar la guía oficial antes de aplicarlos a un proyecto real.

### 15.4. ¿Es obligatorio utilizar Spec Kit?

No. Spec Kit es una herramienta, no la definición completa de SDD.

También se puede aplicar SDD con Markdown, Git, pruebas automatizadas y un proceso de revisión bien definido.

La herramienta debe adaptarse al proceso del equipo, no convertirse en un requisito para aplicar el enfoque.

---

## 16. Ventajas de SDD

### 16.1. Menos ambigüedad

Las decisiones importantes se registran y revisan antes de que se traduzcan en código.

### 16.2. Mayor trazabilidad

Es más fácil relacionar requisitos, componentes, tareas y pruebas.

### 16.3. Mejor comunicación

Los diferentes participantes disponen de una referencia común para discutir qué debe hacer el sistema.

### 16.4. Validación más estructurada

Los criterios de aceptación y los requisitos no funcionales proporcionan una base para comprobar la solución.

### 16.5. Cambios más controlados

Al conocer qué requisitos y componentes están relacionados, resulta más sencillo evaluar el impacto de una modificación.

### 16.6. Mejor contexto para la IA

Las especificaciones proporcionan información más persistente y estructurada que una secuencia de instrucciones improvisadas.

### 16.7. Mayor claridad sobre el alcance

Los requisitos y las exclusiones ayudan a evitar que el proyecto crezca sin control.

---

## 17. Desventajas y riesgos de SDD

### 17.1. Coste inicial de documentación

Preparar una especificación requiere tiempo.

**Mitigación:** ajustar su detalle al riesgo y la complejidad. Una modificación pequeña no necesita un expediente extenso.

### 17.2. Especificaciones obsoletas

Si cambia el código y no se actualiza la especificación, esta puede dejar de representar el comportamiento real.

**Mitigación:** incluir la actualización de los artefactos afectados dentro del proceso de cambio y revisión.

### 17.3. Exceso de burocracia

Un equipo puede convertir SDD en una sucesión de documentos y aprobaciones que ralentizan el desarrollo sin aportar suficiente valor.

**Mitigación:** utilizar únicamente los artefactos y controles que ayudan a reducir riesgos o mejorar la comprensión.

### 17.4. Falsa sensación de seguridad

Una especificación extensa puede parecer completa aunque omita casos importantes.

**Mitigación:** revisar las reglas, los casos límite, las contradicciones y los riesgos mediante análisis y pruebas.

### 17.5. Resistencia al cambio

Si los documentos se consideran contratos inmutables, el equipo puede resistirse a modificar requisitos incluso cuando aparecen nuevos datos.

**Mitigación:** tratar la especificación como un artefacto vivo, versionado y revisable.

### 17.6. Automatización incorrecta

Un agente puede generar una implementación aparentemente completa sin cubrir todos los criterios.

**Mitigación:** verificar resultados con pruebas, análisis estático, revisión de código y controles adecuados al riesgo.

---

## 18. Buenas prácticas para aplicar SDD

1. **Empieza por el problema.** Describe la necesidad antes de elegir tecnologías.
2. **Define el alcance.** Explica qué incluye y qué queda fuera.
3. **Haz explícitas las reglas de negocio.** Evita que queden escondidas en conversaciones o suposiciones.
4. **Incluye casos límite.** Considera errores, permisos, estados inválidos y condiciones excepcionales relevantes.
5. **Define criterios de aceptación.** Establece cómo se comprobará cada comportamiento importante.
6. **Separa requisitos y decisiones técnicas.** Así puedes evolucionar la implementación sin confundirla con la necesidad original.
7. **Registra las restricciones no funcionales.** Incluye seguridad, rendimiento, privacidad y disponibilidad cuando correspondan.
8. **Relaciona requisitos y pruebas.** Cada requisito importante debería contar con una estrategia de verificación adecuada.
9. **Descompón el trabajo en tareas pequeñas.** Facilita la revisión, la ejecución y el seguimiento.
10. **Revisa la consistencia entre artefactos.** Busca contradicciones entre especificación, plan, tareas y código.
11. **Mantén los documentos actualizados.** La especificación debe representar el comportamiento que se pretende mantener.
12. **Usa la IA de forma controlada.** Revisa las decisiones generadas y no des por válido el código solo porque se haya producido automáticamente.
13. **Aplica KISS y YAGNI.** Documenta lo necesario sin diseñar un sistema documental desproporcionado.
14. **Adapta el proceso al riesgo.** Los sistemas críticos necesitan controles más rigurosos que una funcionalidad interna de bajo impacto.

---

## 19. Checklist para revisar una especificación

### Comprensión del problema

- El objetivo está claramente definido.
- Se identifican los usuarios o actores afectados.
- El alcance y las exclusiones están documentados.
- Las reglas de negocio relevantes están identificadas.

### Calidad de los requisitos

- Los requisitos son claros y verificables.
- Se han identificado los casos límite relevantes.
- Se han revisado las ambigüedades y contradicciones.
- Se contemplan los requisitos no funcionales necesarios.
- Los criterios de aceptación permiten comprobar el resultado.

### Diseño y planificación

- El plan técnico cubre los requisitos.
- Las decisiones importantes están justificadas.
- Las dependencias y contratos están definidos cuando corresponde.
- Las tareas tienen un alcance claro.
- Las dependencias entre tareas son comprensibles.

### Implementación y validación

- Los requisitos importantes se relacionan con pruebas o evidencias.
- El código respeta los contratos definidos.
- Se han ejecutado las pruebas relevantes.
- Los errores encontrados se han corregido o registrado.
- Los artefactos están actualizados tras los cambios.
- No existen requisitos importantes sin resolver.

---

## 20. Estrategia para introducir SDD en un proyecto existente

No es necesario reorganizar todo un repositorio para comenzar a aplicar SDD.

Una adopción incremental puede ser más eficaz.

### Paso 1. Seleccionar una funcionalidad

Elige una funcionalidad nueva o una mejora cuyo comportamiento pueda definirse con claridad.

### Paso 2. Escribir una especificación pequeña

Documenta el objetivo, el alcance, las reglas principales, los casos límite y los criterios de aceptación.

### Paso 3. Revisar las dependencias existentes

Antes de proponer una arquitectura nueva, estudia cómo funciona el sistema actual y qué contratos debe conservar.

### Paso 4. Elaborar un plan técnico proporcional

Identifica los componentes que hay que modificar, las dependencias afectadas y las decisiones que deben revisarse.

### Paso 5. Crear tareas verificables

Divide la implementación en cambios pequeños y relaciona las tareas con los requisitos correspondientes.

### Paso 6. Implementar y probar

Desarrolla la funcionalidad y ejecuta las pruebas necesarias para comprobar los comportamientos especificados.

### Paso 7. Comparar el resultado con los requisitos

Comprueba que no existan requisitos omitidos, comportamientos no deseados ni diferencias relevantes entre la especificación y el código.

### Paso 8. Incorporar la experiencia al proceso

Identifica qué documentos o controles fueron útiles y cuáles generaron trabajo innecesario. Ajusta el proceso antes de extenderlo al resto del proyecto.

---

## 21. Conclusión

SDD es un enfoque de ingeniería del software que sitúa las especificaciones explícitas en el centro del desarrollo.

Su principal aportación consiste en establecer una conexión clara entre la necesidad inicial, el comportamiento esperado, el diseño técnico, las tareas de implementación y las pruebas que permiten verificar el resultado.

Aplicarlo significa:

- Definir qué debe hacer el sistema antes de comprometerse con una implementación.
- Aclarar los requisitos y sus casos límite.
- Separar el comportamiento esperado de las decisiones técnicas.
- Crear un plan y tareas que respondan a los requisitos.
- Verificar el código mediante pruebas y revisiones.
- Mantener la especificación alineada con la evolución del producto.
- Evitar tanto la ambigüedad como la documentación innecesaria.
- Utilizar la IA como apoyo, sin renunciar al criterio de ingeniería.

SDD no garantiza por sí solo un software correcto, ni elimina la necesidad de experiencia técnica, pruebas o revisión humana. Su valor reside en hacer explícitas las expectativas y proporcionar una base común para tomar decisiones y comprobar resultados.

**En definitiva, SDD ayuda a que el equipo no se limite a producir código, sino que construya una solución verificable para un problema definido.**

---

## 22. Referencias y lecturas recomendadas

- [GitHub Spec Kit — repositorio y documentación](https://github.com/github/spec-kit): herramienta para estructurar flujos de desarrollo guiados por especificaciones.
- [Spec Kit — guía de inicio](https://github.com/github/spec-kit/blob/main/docs/quickstart.md): ejemplo de un flujo de especificación, planificación, tareas e implementación.
- [The Pragmatic Programmer](https://pragprog.com/titles/tpp20/the-pragmatic-programmer-20th-anniversary-edition/), de David Thomas y Andrew Hunt: prácticas de desarrollo, decisiones técnicas y mantenimiento.
- [Refactoring](https://martinfowler.com/books/refactoring.html), de Martin Fowler: técnicas para mejorar el diseño del código existente.
- [Agile Manifesto](https://agilemanifesto.org/): valores y principios para desarrollar software de forma iterativa y responder a los cambios.
- [Cucumber — documentación de Gherkin](https://cucumber.io/docs/gherkin/): formato para expresar escenarios de comportamiento verificables.

Estas referencias complementan la metodología y ayudan a adaptar el nivel de especificación al contexto real de cada proyecto.