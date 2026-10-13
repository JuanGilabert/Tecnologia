# YAGNI — You Aren’t Gonna Need It
## Principio de no implementar funcionalidades que todavía no se necesitan

## 1. Introducción

**YAGNI** significa *You Aren’t Gonna Need It*, que puede traducirse como «no vas a necesitarlo».

Es un principio de desarrollo de software que recomienda **no implementar funcionalidades, abstracciones o mecanismos adicionales hasta que exista una necesidad real que los justifique**.

Su objetivo es evitar invertir tiempo y esfuerzo en resolver problemas hipotéticos que quizá nunca lleguen a producirse.

Por ejemplo, si una aplicación solo necesita enviar notificaciones por correo electrónico, puede no ser necesario implementar desde el primer día un sistema genérico de plugins que permita enviar notificaciones por correo, SMS, mensajería instantánea, notificaciones push y proveedores futuros.

Si esas necesidades aparecen posteriormente, el diseño puede evolucionar para satisfacerlas.

YAGNI está estrechamente relacionado con el desarrollo ágil, la simplicidad del diseño y la reducción de la deuda técnica.

Cómo aplicar YAGNI junto con los principios que ya estás estudiando
Una forma práctica de recordar la relación entre ellos es:
- YAGNI: no implementes lo que todavía no necesitas.
- KISS: no compliques lo que puedes resolver de forma clara.
- DRY: no mantengas duplicado el mismo conocimiento cuando deba permanecer coherente.
- SOLID: organiza el código para que las responsabilidades y las dependencias faciliten su evolución.
- Clean Code: escribe el código de manera que otras personas puedan comprenderlo y modificarlo.
Mi recomendación: cuando diseñes una funcionalidad, empieza por los requisitos reales, implementa la solución más sencilla que los satisfaga y utiliza pruebas y refactorización para evolucionarla cuando aparezcan nuevas necesidades. Así evitas tanto la sobreingeniería como el código improvisado difícil de mantener.

### 1.1. Objetivos principales

- Evitar funcionalidades que no responden a requisitos actuales.
- Reducir la complejidad accidental.
- Evitar abstracciones prematuras.
- Disminuir el coste de desarrollo y mantenimiento.
- Facilitar la entrega de valor de forma incremental.
- Reducir el tiempo invertido en hipótesis sobre el futuro.
- Mantener el código alineado con las necesidades reales del producto.

### 1.2. Idea fundamental

> Implementa lo que necesitas, no todo lo que imaginas que podrías necesitar.

Esto no significa ignorar el futuro ni diseñar aplicaciones incapaces de evolucionar. Significa no pagar hoy el coste de implementar funcionalidades especulativas sin suficiente justificación.

**YAGNI no prohíbe preparar un diseño razonablemente extensible; desaconseja implementar anticipadamente capacidades que todavía no son necesarias.**

---

## 2. ¿Qué problema resuelve YAGNI?

Durante el desarrollo es habitual pensar en posibles requisitos futuros:

- «Quizá algún día necesitemos varios proveedores de pago».
- «Tal vez tengamos que admitir cinco bases de datos diferentes».
- «Podríamos necesitar cambiar todas las reglas desde un panel de administración».
- «Conviene implementar desde ahora un sistema de plugins».
- «Es posible que más adelante haya que soportar varios tipos de usuario».
- «Quizá el producto se internacionalice y utilice diferentes monedas».

Estas posibilidades pueden ser legítimas, pero no todas tienen la misma probabilidad ni el mismo impacto.

Si implementamos todas esas capacidades desde el principio, podemos terminar con un sistema que:

- Contiene más clases y configuraciones de las necesarias.
- Tiene más puntos de fallo.
- Requiere más pruebas.
- Resulta más difícil de comprender.
- Necesita más documentación.
- Retrasa la entrega de funcionalidades reales.
- Obliga a mantener código que nunca llega a utilizarse.

YAGNI ayuda a distinguir entre **una necesidad actual o suficientemente demostrada y una posibilidad meramente hipotética**.

### 2.1. Ejemplo conceptual

Imaginemos que debemos implementar el envío de correos electrónicos.

La necesidad actual es enviar una confirmación de registro utilizando un proveedor concreto.

Una solución inicial podría ser:

```java
public class RegistrationService {

    private final EmailSender emailSender;

    public RegistrationService(EmailSender emailSender) {
        this.emailSender = emailSender;
    }

    public void register(User user) {
        // Lógica de registro...
        emailSender.send(
                user.getEmail(),
                "Registro completado",
                "Tu cuenta se ha creado correctamente."
        );
    }
}
```

Esta solución permite enviar el correo mediante una dependencia definida.

En cambio, quizá no necesitemos todavía:

- Un catálogo de proveedores.
- Un sistema de descubrimiento automático de plugins.
- Una interfaz administrativa para cambiar proveedores.
- Un lenguaje de configuración propio.
- Un motor de políticas de selección.
- Un sistema de enrutamiento entre proveedores.

Esas capacidades podrían resultar útiles en el futuro, pero su implementación debe responder a necesidades concretas.

### 2.2. Importante: YAGNI no significa eliminar toda abstracción

En el ejemplo anterior, `EmailSender` puede estar justificado si representa una dependencia externa, permite probar el servicio de registro o facilita sustituir el proveedor.

La cuestión no es si debemos utilizar interfaces o no. La cuestión es si **la abstracción resuelve un problema real o solamente prepara una funcionalidad imaginaria**.

---

## 3. Principios fundamentales de YAGNI

### 3.1. Implementar requisitos reales

Cada funcionalidad debería responder a una necesidad identificable.

Antes de implementarla, conviene preguntar:

- ¿Quién necesita esta funcionalidad?
- ¿Qué problema resuelve?
- ¿Es un requisito confirmado?
- ¿Qué sucede si no la implementamos ahora?
- ¿Existe una alternativa más sencilla?

Si no existe una justificación clara, puede ser mejor posponerla.

### 3.2. Evitar anticipar demasiadas variantes

No es necesario implementar todas las posibles alternativas desde el primer momento.

Por ejemplo, si una aplicación utiliza un único proveedor de almacenamiento y no hay requisitos de portabilidad, puede no ser necesario construir un sistema genérico que soporte múltiples proveedores con configuraciones intercambiables.

Sin embargo, si el almacenamiento externo es una frontera importante de la arquitectura, aislarlo mediante una interfaz puede estar perfectamente justificado.

### 3.3. Evolucionar el diseño cuando aparezcan nuevas necesidades

YAGNI favorece un desarrollo incremental.

Primero se implementa la solución necesaria. Cuando aparecen nuevos requisitos, se modifica el diseño de forma controlada.

Este enfoque funciona especialmente bien si el código está bien probado y se refactoriza regularmente.

### 3.4. Diferenciar entre extensibilidad y funcionalidad anticipada

Conviene distinguir dos conceptos:

- **Diseño extensible:** permite incorporar cambios con un coste razonable.
- **Funcionalidad anticipada:** implementa capacidades que todavía no se necesitan.

Un diseño puede estar preparado para evolucionar sin incluir todas las funciones futuras.

Por ejemplo, separar el dominio de la infraestructura puede facilitar cambios posteriores. Pero implementar desde el inicio diez integraciones hipotéticas no suele ser necesario.

### 3.5. No confundir una posibilidad con un requisito

Que una funcionalidad pueda resultar útil no significa que deba desarrollarse ahora.

Las decisiones deberían basarse en requisitos, riesgos, restricciones y necesidades del producto, no únicamente en posibilidades técnicas.

---

## 4. Diagrama conceptual de YAGNI

```text
              Nuevo requisito o idea
                       |
                       v
             ¿Resuelve una necesidad?
                  /           \
                No             Sí
                |               |
                v               v
       Posponer y registrar   Evaluar alcance,
       si tiene interés       riesgos y coste
                                |
                                v
                     ¿Es necesario ahora?
                         /          \
                       No            Sí
                       |              |
                       v              v
                Posponer la idea   Implementar la
                y revisar después  solución necesaria
                                        |
                                        v
                               Probar y validar
                                        |
                                        v
                                Revisar el diseño
                                cuando cambien
                                los requisitos
```

Este flujo es una guía de decisión, no una regla mecánica.

Una capacidad puede no ser una funcionalidad solicitada directamente, pero sí resultar necesaria para cumplir requisitos de seguridad, rendimiento, accesibilidad, fiabilidad o cumplimiento normativo.

---

## 5. Ejemplo práctico en Java: sistema de descuentos

Supongamos que una tienda necesita aplicar un descuento fijo del 10 % a todos sus pedidos.

### 5.1. Solución inicial innecesariamente anticipada

Podríamos construir una arquitectura que incluyera:

- Una interfaz para cada tipo de descuento.
- Un registro de estrategias.
- Un motor de reglas.
- Un sistema de prioridades.
- Configuración dinámica desde una base de datos.
- Un editor de reglas para usuarios.
- Un sistema de plugins.
- Un mecanismo de versionado de políticas.

Esta arquitectura podría ser adecuada para una plataforma comercial con reglas complejas y administrables, pero puede resultar desproporcionada para un único descuento fijo.

### 5.2. Implementación sencilla

```java
public class DiscountCalculator {

    private static final double DISCOUNT_RATE = 0.10;

    public double calculateFinalPrice(double price) {
        return price * (1 - DISCOUNT_RATE);
    }
}
```

Esta implementación cubre la necesidad inicial.

Si se trata de un sistema financiero real, conviene utilizar `BigDecimal` y definir explícitamente las reglas de redondeo.

### 5.3. ¿Qué sucede cuando aparecen nuevos requisitos?

Supongamos que posteriormente el negocio solicita tres modalidades:

- Descuento estándar.
- Descuento para clientes prémium.
- Descuento promocional.

Ahora existe una variabilidad real.

Podemos introducir una estrategia:

```java
public interface DiscountPolicy {

    double applyDiscount(double price);
}
```

```java
public class StandardDiscount implements DiscountPolicy {

    @Override
    public double applyDiscount(double price) {
        return price * 0.90;
    }
}
```

```java
public class PremiumDiscount implements DiscountPolicy {

    @Override
    public double applyDiscount(double price) {
        return price * 0.80;
    }
}
```

```java
public class DiscountService {

    public double calculateFinalPrice(
            double price,
            DiscountPolicy policy) {

        return policy.applyDiscount(price);
    }
}
```

Ahora el patrón Strategy tiene una utilidad concreta: seleccionar diferentes políticas de descuento.

### 5.4. Aprendizaje

La primera solución no tenía que anticipar todas las modalidades posibles.

El diseño pudo evolucionar cuando la variabilidad pasó a formar parte de los requisitos.

Esto es especialmente efectivo cuando existe una buena cobertura de pruebas y el código puede refactorizarse con seguridad.

---

## 6. Ejemplo práctico: evitar configuraciones innecesarias

Supongamos que una aplicación necesita mostrar diez productos destacados en la página principal.

### 6.1. Solución excesivamente genérica

Podríamos crear un sistema configurable con:

- Una interfaz para cada criterio de selección.
- Un motor de reglas.
- Una tabla de configuración.
- Un editor visual de prioridades.
- Un sistema de plugins.
- Un lenguaje de consultas propio.

Todo ello puede tener sentido en un catálogo complejo que cambia continuamente por decisiones comerciales.

Pero si el requisito es simplemente mostrar los diez productos con mayor número de ventas, puede ser suficiente una consulta clara.

### 6.2. Solución directa

```sql
SELECT id, name, sales_count
FROM products
ORDER BY sales_count DESC
LIMIT 10;
```

La consulta expresa directamente el criterio solicitado.

### 6.3. ¿Cuándo estaría justificada una solución más elaborada?

Si los responsables comerciales necesitan cambiar frecuentemente los criterios, combinar reglas, programar campañas o personalizar los resultados por usuario, una solución configurable podría aportar valor real.

La decisión depende de los requisitos y de los costes operativos, no de una preferencia automática por el código más corto.

---

## 7. YAGNI y el diseño orientado a objetos

YAGNI es especialmente relevante cuando se diseñan clases, interfaces, jerarquías y abstracciones.

### 7.1. No crear jerarquías para variantes hipotéticas

Si solo existe un tipo de notificación y no hay requisitos de sustitución, quizá no sea necesario crear una jerarquía de cinco clases abstractas.

Una implementación directa puede ser suficiente.

### 7.2. No eliminar abstracciones que ya resuelven problemas reales

YAGNI no obliga a introducir dependencias concretas por todas partes.

Por ejemplo, una interfaz que separa el dominio de una API externa puede ser útil incluso si solo existe una implementación.

Esa frontera puede facilitar:

- Las pruebas unitarias.
- La sustitución del proveedor.
- El aislamiento de errores externos.
- La protección del dominio frente a detalles de infraestructura.

La pregunta relevante es si la abstracción aporta un beneficio actual y significativo.

### 7.3. Evitar métodos y parámetros especulativos

No conviene añadir parámetros, métodos o configuraciones que nadie utiliza y cuyo propósito solo es atender posibles escenarios futuros.

Sin embargo, no deben eliminarse parámetros que formen parte de un contrato estable o de una necesidad confirmada.

---

## 8. Relación con KISS, DRY, SOLID y Clean Code

YAGNI funciona mejor cuando se combina con otros principios de diseño.

| Principio | Objetivo principal | Relación con YAGNI |
|---|---|---|
| KISS | Mantener las soluciones comprensibles y sin complejidad innecesaria | Evita mecanismos excesivos |
| DRY | Evitar duplicar conocimiento o reglas que deben mantenerse coherentes | Evita que las reglas evolucionen de forma inconsistente |
| SOLID | Organizar responsabilidades, contratos y dependencias | Facilita evolucionar el diseño cuando hay necesidades reales |
| Clean Code | Mejorar la legibilidad y el mantenimiento | Ayuda a implementar solo lo necesario de forma clara |
| YAGNI | No implementar capacidades que todavía no se necesitan | Evita anticipar funcionalidades especulativas |

### 8.1. YAGNI y KISS

KISS pregunta:

«¿Existe una forma más sencilla de resolver este problema?».

YAGNI pregunta:

«¿Necesitamos realmente implementar esta funcionalidad ahora?».

Son principios complementarios. Una funcionalidad innecesaria puede complicar incluso una implementación bien organizada.

### 8.2. YAGNI y DRY

DRY no significa que debamos crear una abstracción cada vez que vemos dos fragmentos de código parecidos.

Supongamos que dos módulos calculan importes de forma similar, pero las reglas pertenecen a dominios diferentes. Unificarlos prematuramente puede introducir dependencias y dificultar su evolución independiente.

YAGNI ayuda a evitar una abstracción común que todavía no está justificada.

Por otro lado, si ambos módulos deben aplicar exactamente la misma regla de negocio, puede ser conveniente centralizarla para evitar inconsistencias.

### 8.3. YAGNI y SOLID

SOLID no exige crear abstracciones para todas las posibles extensiones.

Por ejemplo, OCP favorece diseños extensibles, pero no implica que debamos preparar un sistema para todos los cambios imaginables.

DIP recomienda invertir las dependencias cuando ello mejora la arquitectura, especialmente en fronteras relevantes. No exige crear una interfaz para cada clase.

La aplicación equilibrada de ambos principios permite mantener un diseño que evolucione sin anticipar funcionalidades innecesarias.

### 8.4. YAGNI y Clean Code

El código limpio suele ser más fácil de modificar, por lo que favorece la evolución incremental.

YAGNI complementa esta idea al evitar que el código acumule funciones, parámetros, clases y configuraciones sin utilidad demostrada.

---

## 9. YAGNI y los patrones de diseño

Los patrones de diseño resuelven problemas de diseño recurrentes, pero no todos los proyectos necesitan todos los patrones.

### 9.1. Cuándo aplicar un patrón

Puede estar justificado cuando:

- Existen varias implementaciones reales.
- Los algoritmos deben ser intercambiables.
- Se necesita desacoplar un módulo de una dependencia externa.
- La lógica de creación de objetos es realmente compleja.
- Las reglas de negocio requieren composición o variación.
- La solución mejora claramente la capacidad de prueba o mantenimiento.

### 9.2. Cuándo posponerlo

Puede ser razonable esperar cuando:

- Solo existe una variante sencilla.
- No hay requisitos que justifiquen la abstracción.
- La estructura adicional no mejora la comprensión.
- La solución anticipa necesidades sin evidencia.
- Es probable que el diseño cambie cuando se conozcan mejor los requisitos.

### 9.3. Regla práctica

No apliques un patrón porque podría resultar útil algún día.

Aplícalo cuando resuelva un problema de diseño presente o una necesidad futura suficientemente justificada.

---

## 10. YAGNI en arquitecturas de software

El principio también se aplica a las decisiones arquitectónicas.

### 10.1. Microservicios

Los microservicios pueden ser apropiados cuando se necesitan despliegues independientes, escalado diferenciado, autonomía de equipos o límites de dominio bien definidos.

Pero no es necesario dividir una aplicación en microservicios solo porque la aplicación pueda crecer en el futuro.

Una arquitectura distribuida introduce costes adicionales de comunicación, observabilidad, despliegue, seguridad y consistencia.

### 10.2. Arquitectura hexagonal

La arquitectura hexagonal permite aislar el dominio de la infraestructura y facilita sustituir adaptadores externos.

Puede ser adecuada desde etapas tempranas cuando el dominio y sus dependencias requieren esa separación.

Sin embargo, no siempre es necesario crear numerosos puertos y adaptadores para una aplicación pequeña sin fronteras complejas.

### 10.3. Clean Architecture

Clean Architecture ayuda a controlar las dependencias y a proteger la lógica de negocio.

YAGNI invita a adaptar la implementación a la escala y los requisitos del sistema, evitando capas que únicamente delegan llamadas sin aportar una frontera o responsabilidad útil.

### 10.4. Regla arquitectónica

**No adoptes una arquitectura compleja solo por una posible necesidad futura; elige una estructura que satisfaga los requisitos reales y permita evolucionar con un coste razonable.**

---

## 11. YAGNI en APIs y funcionalidades de producto

### 11.1. APIs

Si una API necesita crear, consultar y actualizar usuarios, no es necesario implementar desde el inicio operaciones para todas las posibles variantes futuras.

Cada endpoint añade contratos que deben probarse, documentarse, protegerse y mantenerse.

Sin embargo, no conviene omitir operaciones necesarias para cumplir los requisitos de integración o los contratos ya comprometidos.

### 11.2. Configuración

No conviertas todos los valores del código en opciones configurables.

Por ejemplo, una regla estable del dominio puede expresarse mediante una constante.

Si un valor debe cambiar por entorno, cliente, país o política comercial, puede estar justificada la configuración externa.

### 11.3. Internacionalización

Si la aplicación debe operar en varios idiomas desde su primera versión, la internacionalización es un requisito actual, no una funcionalidad especulativa.

Si el producto está limitado deliberadamente a un idioma y no existen necesidades adicionales, puede ser razonable posponer una infraestructura de localización completa.

La decisión debe basarse en requisitos, compromisos y costes previsibles.

---

## 12. YAGNI en pruebas automatizadas

YAGNI también se aplica a la estrategia de pruebas.

No es necesario construir un framework de pruebas propio cuando JUnit y las herramientas existentes cubren las necesidades del proyecto.

Tampoco conviene crear capas de utilidades excesivamente genéricas para casos de prueba que aparecen una sola vez y son fáciles de expresar directamente.

### 12.1. Ejemplo con JUnit 5

Supongamos que tenemos un servicio sencillo de cálculo:

```java
public class PriceCalculator {

    public double calculateTotal(double price, double taxRate) {
        return price * (1 + taxRate);
    }
}
```

Podemos probar su comportamiento directamente:

```java
import static org.junit.jupiter.api.Assertions.assertEquals;

import org.junit.jupiter.api.Test;

class PriceCalculatorTest {

    private final PriceCalculator calculator =
            new PriceCalculator();

    @Test
    void calculatesTotalWithTax() {
        double result = calculator.calculateTotal(100.0, 0.21);

        assertEquals(121.0, result, 0.0001);
    }
}
```

No hace falta crear un motor de escenarios o un framework propio para una operación tan sencilla.

### 12.2. No confundir YAGNI con falta de pruebas

YAGNI no justifica omitir pruebas importantes.

Las pruebas deben cubrir los comportamientos requeridos, los casos límite relevantes y los riesgos del sistema.

Tampoco se debe posponer automáticamente una prueba de seguridad, una prueba de integración crítica o una validación necesaria para cumplir un requisito.

---

## 13. Ventajas de aplicar YAGNI

### 13.1. Entrega de valor más rápida

Se priorizan las funcionalidades que realmente necesita el producto, reduciendo el trabajo que no aporta valor inmediato.

### 13.2. Menor complejidad

El código contiene menos mecanismos especulativos y resulta más fácil de comprender.

### 13.3. Menor coste de mantenimiento

Hay menos funcionalidades sin uso, menos configuraciones y menos código que mantener.

### 13.4. Menos decisiones prematuras

Las decisiones se toman con información más precisa a medida que se conocen los requisitos.

### 13.5. Mayor capacidad de aprendizaje

Un equipo puede validar una solución con usuarios reales y ajustar el producto a partir de la experiencia, en lugar de invertir demasiado tiempo en suposiciones.

### 13.6. Menor riesgo de sobreingeniería

Se reduce la tendencia a crear soluciones genéricas y complejas antes de que exista una necesidad demostrada.

---

## 14. Riesgos de interpretar YAGNI incorrectamente

YAGNI no debe utilizarse como excusa para ignorar requisitos relevantes o tomar atajos que aumenten el coste futuro.

### 14.1. Ignorar requisitos no funcionales

Seguridad, accesibilidad, rendimiento, disponibilidad y cumplimiento normativo pueden ser requisitos desde el principio.

**Corrección:** considera estos requisitos parte del problema actual cuando correspondan al sistema.

### 14.2. Evitar toda planificación

YAGNI no implica desarrollar sin arquitectura ni tomar decisiones sin considerar sus consecuencias.

**Corrección:** diseña para las necesidades conocidas y conserva una capacidad razonable de evolución.

### 14.3. Acoplar todo a una implementación concreta

Eliminar una abstracción puede reducir código, pero también crear un acoplamiento costoso.

**Corrección:** conserva las fronteras que aportan valor actual, especialmente alrededor de infraestructura, integraciones y dominio.

### 14.4. Posponer requisitos confirmados

Una funcionalidad puede parecer futura, pero ser necesaria por contrato, regulación, seguridad o compromisos de producto.

**Corrección:** distingue entre una hipótesis y un requisito con una fecha o una obligación concreta.

### 14.5. Generar deuda técnica innecesaria

Una solución provisional puede convertirse en un problema si se implementa sin pruebas, sin límites claros o con duplicaciones difíciles de corregir.

**Corrección:** la solución inicial debe ser sencilla, pero correcta y mantenible.

### 14.6. Esperar demasiado para refactorizar

Si una segunda o tercera variante ya demuestra que existe una necesidad real de generalización, seguir acumulando condiciones y duplicaciones puede empeorar el diseño.

**Corrección:** revisa las decisiones cuando aparezcan nuevos requisitos y refactoriza de forma incremental.

---

## 15. Cómo aplicar YAGNI durante el desarrollo

Antes de implementar una nueva capacidad, puedes seguir este proceso.

### Paso 1. Identificar la necesidad

Describe el problema que debe resolver la funcionalidad.

### Paso 2. Verificar el requisito

Comprueba si procede de un requisito confirmado, un usuario, una restricción técnica, un riesgo relevante o una obligación externa.

### Paso 3. Evaluar el coste de posponerla

Determina qué consecuencias tendría no implementarla ahora.

### Paso 4. Diseñar la solución mínima suficiente

Busca una implementación que cumpla los requisitos sin incorporar mecanismos adicionales injustificados.

### Paso 5. Mantener la calidad

No elimines validaciones, pruebas, controles de seguridad ni requisitos de fiabilidad para reducir el alcance.

### Paso 6. Entregar y obtener información

Cuando sea posible, valida la solución con usuarios, métricas o resultados observables.

### Paso 7. Evolucionar cuando aparezca una necesidad real

Añade la funcionalidad o abstracción cuando el nuevo requisito justifique el coste.

---

## 16. Checklist práctico de YAGNI

### Requisitos

- [ ] La funcionalidad responde a una necesidad identificable.
- [ ] Se distingue entre requisitos confirmados e hipótesis.
- [ ] Se ha evaluado el coste de posponerla.
- [ ] Se han considerado las restricciones legales, técnicas y operativas pertinentes.

### Diseño

- [ ] No se han creado abstracciones para variantes puramente hipotéticas.
- [ ] Las interfaces existentes aportan un beneficio claro.
- [ ] Los patrones de diseño resuelven problemas concretos.
- [ ] La arquitectura es proporcional a las necesidades actuales.
- [ ] La solución permite evolucionar sin complejidad innecesaria.

### Implementación

- [ ] No existen parámetros o configuraciones sin uso ni justificación.
- [ ] No se han implementado funcionalidades especulativas.
- [ ] El código mantiene responsabilidades comprensibles.
- [ ] Se han conservado las validaciones y controles necesarios.
- [ ] Los comportamientos relevantes están cubiertos por pruebas.

### Mantenimiento

- [ ] Se puede refactorizar cuando cambien los requisitos.
- [ ] Se revisan periódicamente las decisiones de diseño.
- [ ] Se evita tanto la sobreingeniería como la improvisación.
- [ ] Se documentan las decisiones cuya razón no sea evidente.

---

## 17. Diferencias entre YAGNI y KISS

Aunque están estrechamente relacionados, no son exactamente lo mismo.

| Aspecto | YAGNI | KISS |
|---|---|---|
| Pregunta principal | ¿Necesitamos esta funcionalidad ahora? | ¿Podemos resolver el problema de forma más sencilla? |
| Foco | Alcance de lo que se implementa | Complejidad de la solución |
| Riesgo que reduce | Funcionalidades especulativas | Complejidad innecesaria |
| Ejemplo | No crear un sistema de plugins que nadie necesita | Evitar un sistema de plugins excesivamente complejo si basta una implementación directa |
| Aplicación típica | Planificación y priorización | Diseño e implementación |

Una funcionalidad puede ser necesaria y, aun así, estar implementada de forma demasiado compleja. En ese caso, KISS ayuda a simplificarla.

Por otra parte, una funcionalidad hipotética puede estar implementada de forma elegante, pero seguir siendo innecesaria. En ese caso, YAGNI invita a posponerla.

---

## 18. Estrategia para refactorizar aplicando YAGNI

Si una base de código contiene funcionalidades que no se utilizan, no conviene eliminarlas indiscriminadamente. Primero hay que comprobar si forman parte de contratos, integraciones o requisitos existentes.

Un proceso prudente sería el siguiente:

### Paso 1. Inventariar las capacidades

Identifica clases, métodos, configuraciones y componentes que aparentemente no tienen uso.

### Paso 2. Comprobar dependencias y contratos

Revisa si están siendo utilizados indirectamente, expuestos como API pública o requeridos por otros sistemas.

### Paso 3. Determinar el valor que aportan

Establece si resuelven un requisito actual, una obligación o un riesgo relevante.

### Paso 4. Eliminar lo que realmente sobra

Retira código y configuraciones innecesarias de forma incremental.

### Paso 5. Ejecutar las pruebas

Comprueba que la eliminación no altera los comportamientos esperados.

### Paso 6. Refactorizar las abstracciones prematuras

Cuando una estructura genérica solo atiende un caso de uso, considera simplificarla, siempre que no elimines una frontera arquitectónica importante.

### Paso 7. Registrar las decisiones relevantes

Si se ha decidido posponer una funcionalidad, puede ser útil registrarla en el backlog con su motivación, en lugar de implementarla anticipadamente.

---

## 19. Ejemplos de decisiones cotidianas

| Situación | Decisión alineada con YAGNI |
|---|---|
| Solo se necesita un proveedor de correo y no hay requisitos de sustitución complejos | Evitar un sistema genérico de plugins sin necesidad |
| El sistema debe soportar varios proveedores de pago desde la primera versión | Diseñar la abstracción necesaria desde el principio |
| Se supone que algún día habrá varios idiomas, pero no existe una necesidad confirmada | Evaluar si puede posponerse la infraestructura completa de localización |
| El producto debe lanzarse en tres idiomas | Tratar la internacionalización como requisito actual |
| Una clase tiene una responsabilidad clara y estable | No añadir una interfaz solo por uniformidad |
| La lógica de negocio depende directamente de un proveedor externo difícil de sustituir | Valorar una frontera de abstracción adecuada |
| Una regla de negocio se repite y debe mantenerse coherente | Centralizarla cuando la abstracción sea clara |
| Dos fragmentos parecen similares, pero representan reglas distintas | Evitar unificarlos prematuramente |

Estos ejemplos ilustran una regla general: la decisión depende de las necesidades reales y de las consecuencias de cada alternativa.

---

## 20. Conclusión

YAGNI es un principio fundamental para evitar la sobreingeniería y mantener el software alineado con los problemas que realmente necesita resolver.

Aplicarlo significa:

- Priorizar requisitos reales.
- Evitar funcionalidades especulativas.
- Posponer abstracciones prematuras.
- Desarrollar de forma incremental.
- Mantener una solución inicial sencilla, correcta y comprobable.
- Refactorizar cuando los requisitos evolucionen.
- No sacrificar seguridad, fiabilidad ni calidad por reducir el alcance.
- Distinguir entre preparar una solución para evolucionar e implementar anticipadamente todas sus posibles variantes.

La clave no es ignorar el futuro, sino **evitar pagar hoy el coste de resolver problemas que todavía no existen, salvo que haya razones concretas para anticiparlos**.

El software evoluciona, y su diseño también debe hacerlo. Una buena solución no necesita incluir desde el primer día todas las posibilidades imaginables; necesita resolver bien el problema actual y permitir cambios razonables cuando aparezcan nuevas necesidades.

---

## 21. Referencias y lecturas recomendadas

- **Extreme Programming Explained**, de Kent Beck: prácticas de desarrollo incremental, retroalimentación y simplicidad.
- **The Pragmatic Programmer**, de Andrew Hunt y David Thomas: decisiones de diseño, mantenimiento y evolución del software.
- **Refactoring**, de Martin Fowler: técnicas para mejorar el diseño de una base de código existente.
- **Clean Code**, de Robert C. Martin: legibilidad, responsabilidades y mantenibilidad.
- **Agile Manifesto**: principios de desarrollo iterativo, colaboración y entrega frecuente de software útil.

Estas referencias complementan YAGNI y ayudan a decidir cuándo una funcionalidad debe implementarse ahora y cuándo es preferible esperar hasta disponer de una necesidad mejor definida.