# Patrón arquitectónico: MVC (Model-View-Controller)

## 1. Introducción

MVC (*Model-View-Controller*) es un patrón arquitectónico que divide una aplicación en tres componentes principales: **Modelo, Vista y Controlador**.

Su objetivo es separar la gestión de los datos y las reglas de negocio de la presentación de la información y del tratamiento de las interacciones del usuario.

Esta separación facilita el mantenimiento, las pruebas y la evolución de la aplicación, ya que cada componente tiene una responsabilidad diferenciada.

MVC se utiliza en aplicaciones web, aplicaciones de escritorio, interfaces gráficas y otros sistemas en los que resulta conveniente separar la lógica de la aplicación de su presentación.

### Los tres componentes principales

**Modelo (Model)**

Representa los datos, el estado y las reglas de negocio de la aplicación.

Puede incluir entidades del dominio, validaciones, servicios y mecanismos de acceso a datos, aunque en una arquitectura bien organizada conviene diferenciar esas responsabilidades en clases o capas específicas.

**Vista (View)**

Se encarga de presentar la información al usuario.

Puede ser una página HTML, una plantilla, una ventana de escritorio, una pantalla móvil o un componente de interfaz gráfica.

**Controlador (Controller)**

Recibe las acciones del usuario, interpreta las peticiones y coordina la operación correspondiente.

Normalmente delega el trabajo en servicios o componentes del modelo y determina qué vista debe mostrarse o qué respuesta debe devolverse.

### Ejemplo conceptual

Imaginemos una aplicación de gestión de tareas.

El usuario pulsa un botón para marcar una tarea como completada.

1. La vista recoge la interacción del usuario.
2. El controlador recibe la acción.
3. El controlador solicita al modelo que actualice la tarea.
4. El modelo modifica el estado y aplica las reglas correspondientes.
5. La vista muestra la información actualizada.

De esta forma, la interfaz no necesita contener toda la lógica de negocio y el modelo no necesita conocer los detalles visuales de la aplicación.

---

## 2. Problema que resuelve

MVC resuelve el problema de mezclar en un mismo componente la presentación, la gestión de los datos y el tratamiento de las interacciones del usuario.

Sin una separación clara, una aplicación puede terminar con clases que generan HTML, ejecutan consultas a bases de datos, validan reglas de negocio y procesan peticiones al mismo tiempo.

Esto provoca varios problemas:

- **Alto acoplamiento:** un cambio visual puede afectar a la lógica de negocio.
- **Dificultad de mantenimiento:** las responsabilidades están mezcladas.
- **Pruebas más complicadas:** probar una regla de negocio puede exigir construir componentes visuales.
- **Duplicación de código:** distintas pantallas pueden repetir las mismas operaciones.
- **Menor reutilización:** la lógica de negocio queda ligada a una interfaz concreta.
- **Dificultad para evolucionar:** cambiar de tecnología de presentación puede exigir modificar muchas clases.

### Ejemplo sin MVC

Imaginemos una clase que gestiona tareas y genera directamente la respuesta visual.

```java
public class GestorTareas {

    public String completarTarea(
            int id,
            String nombre) {

        // Lógica de negocio
        boolean completada = true;

        // Presentación mezclada con la lógica
        if (completada) {
            return "<html><h1>"
                + nombre
                + " completada</h1></html>";
        }

        return "<html><h1>Error</h1></html>";
    }
}
```

Esta clase mezcla la operación de completar una tarea con la generación de HTML.

Si en el futuro queremos crear una aplicación móvil o una API REST, parte de la lógica tendría que modificarse o duplicarse.

MVC propone separar esas responsabilidades.

---

## 3. Solución propuesta

MVC divide la aplicación en tres componentes con responsabilidades específicas.

### 3.1. Modelo (Model)

El modelo contiene los datos y el comportamiento relacionado con el dominio de la aplicación.

Por ejemplo, en una aplicación de tareas puede incluir:

- La entidad `Tarea`.
- Las reglas para completar una tarea.
- La validación de las operaciones.
- Los servicios que coordinan casos de uso.
- Los repositorios que permiten persistir los datos.

El modelo no debería depender de detalles concretos de presentación, como etiquetas HTML o componentes visuales.

### 3.2. Vista (View)

La vista presenta los datos al usuario.

Sus responsabilidades pueden incluir:

- Mostrar información.
- Representar estados visuales.
- Recoger entradas de usuario.
- Presentar mensajes de validación.
- Renderizar listas, tablas o formularios.

La vista no debería convertirse en el lugar principal donde se implementan las reglas de negocio.

### 3.3. Controlador (Controller)

El controlador coordina las interacciones.

Sus responsabilidades habituales incluyen:

- Recibir una acción o petición.
- Validar los datos de entrada a nivel de petición.
- Invocar el servicio o la operación correspondiente.
- Gestionar el resultado de la operación.
- Seleccionar una vista o devolver una respuesta.

El controlador debería mantenerse relativamente ligero y evitar concentrar toda la lógica del dominio.

### Diagrama conceptual

```text
              Usuario
                 |
                 v
               Vista
                 |
                 v
            Controlador
                 |
                 v
               Modelo
                 |
                 v
        Datos / Repositorio
```

Este esquema representa un flujo habitual de interacción. Sin embargo, no significa que todas las implementaciones MVC tengan exactamente las mismas dependencias.

En algunas variantes, la vista consulta el modelo para obtener información; en otras, el controlador prepara los datos que la vista necesita. El detalle depende del framework y de la variante de MVC utilizada.

### Diagrama de responsabilidades

```text
+------------------+
|       Vista      |
|------------------|
| Presentación     |
| Interacción      |
+------------------+
          |
          v
+------------------+
|   Controlador    |
|------------------|
| Recibir acciones |
| Coordinar flujo  |
+------------------+
          |
          v
+------------------+
|      Modelo      |
|------------------|
| Estado           |
| Reglas de negocio|
| Operaciones      |
+------------------+
          |
          v
+------------------+
| Repositorio      |
|------------------|
| Persistencia     |
+------------------+
```

---

## 4. Implementación completa en Java

Vamos a desarrollar una aplicación sencilla para gestionar tareas.

El usuario podrá consultar una tarea, completarla y mostrar su estado actualizado.

Para mantener el ejemplo sencillo, utilizaremos una vista de consola. La misma separación conceptual puede aplicarse a una aplicación web o de escritorio.

### 4.1. Modelo: clase `Tarea`

Representa una tarea de la aplicación.

```java
public class Tarea {

    private final int id;
    private final String nombre;
    private boolean completada;

    public Tarea(int id, String nombre) {
        if (nombre == null || nombre.isBlank()) {
            throw new IllegalArgumentException(
                "El nombre de la tarea es obligatorio"
            );
        }

        this.id = id;
        this.nombre = nombre;
        this.completada = false;
    }

    public int getId() {
        return id;
    }

    public String getNombre() {
        return nombre;
    }

    public boolean estaCompletada() {
        return completada;
    }

    public void completar() {
        this.completada = true;
    }
}
```

Esta clase representa el estado de una tarea y proporciona la operación necesaria para completarla.

Las reglas de negocio más complejas pueden trasladarse a servicios del dominio o de aplicación, según las necesidades del sistema.

### 4.2. Modelo: servicio de tareas

El servicio contiene las operaciones que la aplicación ofrece sobre las tareas.

```java
import java.util.HashMap;
import java.util.Map;

public class TareaService {

    private final Map<Integer, Tarea> tareas =
        new HashMap<>();

    public TareaService() {
        tareas.put(
            1,
            new Tarea(1, "Estudiar MVC")
        );

        tareas.put(
            2,
            new Tarea(2, "Practicar Java")
        );
    }

    public Tarea obtenerPorId(int id) {
        Tarea tarea = tareas.get(id);

        if (tarea == null) {
            throw new IllegalArgumentException(
                "La tarea no existe: " + id
            );
        }

        return tarea;
    }

    public void completarTarea(int id) {
        Tarea tarea = obtenerPorId(id);
        tarea.completar();
    }
}
```

El servicio centraliza la búsqueda y la operación de completar una tarea.

En una aplicación real, el almacenamiento en memoria podría sustituirse por un repositorio conectado a una base de datos.

### 4.3. Vista: `TareaView`

La vista presenta la información y los mensajes al usuario.

```java
public class TareaView {

    public void mostrarTarea(Tarea tarea) {
        System.out.println("ID: " + tarea.getId());

        System.out.println(
            "Nombre: " + tarea.getNombre()
        );

        System.out.println(
            "Estado: "
            + (tarea.estaCompletada()
                ? "Completada"
                : "Pendiente")
        );
    }

    public void mostrarMensaje(String mensaje) {
        System.out.println(mensaje);
    }
}
```

La vista no decide cómo se completa una tarea. Únicamente representa la información que recibe.

### 4.4. Controlador: `TareaController`

El controlador conecta las acciones del usuario con las operaciones del servicio y la presentación.

```java
public class TareaController {

    private final TareaService tareaService;
    private final TareaView tareaView;

    public TareaController(
            TareaService tareaService,
            TareaView tareaView) {

        this.tareaService = tareaService;
        this.tareaView = tareaView;
    }

    public void mostrarTarea(int id) {
        try {
            Tarea tarea =
                tareaService.obtenerPorId(id);

            tareaView.mostrarTarea(tarea);

        } catch (IllegalArgumentException e) {
            tareaView.mostrarMensaje(
                "Error: " + e.getMessage()
            );
        }
    }

    public void completarTarea(int id) {
        try {
            tareaService.completarTarea(id);

            tareaView.mostrarMensaje(
                "Tarea completada correctamente"
            );

            mostrarTarea(id);

        } catch (IllegalArgumentException e) {
            tareaView.mostrarMensaje(
                "Error: " + e.getMessage()
            );
        }
    }
}
```

El controlador coordina la operación, pero no implementa directamente la regla de completar una tarea.

Para una aplicación de producción convendría distinguir entre errores de validación, recursos inexistentes y fallos internos, evitando convertir todas las excepciones en mensajes genéricos.

### 4.5. Cliente: clase `Main`

La clase principal crea los componentes y los conecta.

```java
public class Main {

    public static void main(String[] args) {

        TareaService tareaService =
            new TareaService();

        TareaView tareaView =
            new TareaView();

        TareaController tareaController =
            new TareaController(
                tareaService,
                tareaView
            );

        tareaController.mostrarTarea(1);

        System.out.println("--------------");

        tareaController.completarTarea(1);
    }
}
```

Salida aproximada:

```text
ID: 1
Nombre: Estudiar MVC
Estado: Pendiente
--------------
Tarea completada correctamente
ID: 1
Nombre: Estudiar MVC
Estado: Completada
```

### 4.6. ¿Qué hemos conseguido?

Hemos separado tres responsabilidades:

- El modelo representa las tareas y ejecuta las operaciones relacionadas con ellas.
- La vista muestra la información.
- El controlador coordina las acciones del usuario.

Además, las dependencias se pasan mediante los constructores. Esto facilita sustituir componentes y realizar pruebas unitarias.

**Importante:** este ejemplo ilustra la separación de responsabilidades, pero no representa una implementación única y universal de MVC. En una aplicación web, el controlador podría recibir una petición HTTP y devolver una plantilla o una respuesta JSON.

---

## 5. Flujo de ejecución en MVC

El flujo habitual de una operación puede representarse así:

```text
Usuario
   |
   | 1. Solicita completar una tarea
   v
Controlador
   |
   | 2. Invoca la operación
   v
Servicio / Modelo
   |
   | 3. Actualiza el estado
   v
Repositorio
   |
   | 4. Persiste los cambios, si procede
   v
Servicio / Modelo
   |
   | 5. Devuelve el resultado
   v
Controlador
   |
   | 6. Selecciona la respuesta
   v
Vista
   |
   | 7. Presenta el estado actualizado
   v
Usuario
```

En aplicaciones web con formularios, la vista puede generar una petición HTTP y el controlador puede devolver una nueva página o redirigir al usuario.

En aplicaciones basadas en interfaces reactivas, la actualización visual puede producirse mediante cambios de estado, sin volver a renderizar una página completa.

---

## 6. Variantes de MVC

MVC ha evolucionado y existen diferentes formas de implementarlo.

### 6.1. MVC clásico

En el modelo clásico, el modelo representa el estado y las reglas de la aplicación, mientras que la vista y el controlador gestionan la presentación y las interacciones.

Según la implementación, la vista puede observar los cambios del modelo o recibir los datos preparados por el controlador.

Se utiliza como referencia conceptual para entender la separación de responsabilidades.

### 6.2. MVC web

En aplicaciones web tradicionales, el navegador realiza una petición HTTP y el controlador la procesa.

Un flujo habitual es:

1. El usuario envía un formulario.
2. El controlador recibe la petición.
3. El controlador invoca un servicio.
4. El servicio obtiene o modifica los datos.
5. El controlador selecciona una vista.
6. La aplicación devuelve HTML al navegador.

Este modelo es habitual en aplicaciones que generan páginas en el servidor.

### 6.3. MVC con API REST

En una API REST, el controlador recibe peticiones HTTP y normalmente devuelve datos en formato JSON en lugar de una vista HTML.

El modelo y los servicios siguen gestionando las operaciones, pero la representación de la respuesta cambia.

Por ejemplo:

```text
Cliente HTTP
     |
     v
Controlador REST
     |
     v
Servicio de aplicación
     |
     v
Repositorio
     |
     v
Respuesta JSON
```

Aunque se hable de MVC en este contexto, la respuesta JSON no es necesariamente una vista en el sentido tradicional de una plantilla visual. La terminología depende de la arquitectura concreta.

### 6.4. MVC con frameworks

Frameworks como Spring MVC facilitan la implementación de este patrón mediante controladores, resolución de vistas, validación y mecanismos de tratamiento de peticiones.

El framework proporciona infraestructura, pero la separación efectiva de responsabilidades sigue dependiendo del diseño de la aplicación.

---

## 7. Ejemplo de MVC web con Spring Boot

El siguiente ejemplo muestra cómo se puede aplicar MVC en una aplicación web Java.

### 7.1. Modelo

```java
public class Producto {

    private final Long id;
    private final String nombre;
    private final double precio;

    public Producto(
            Long id,
            String nombre,
            double precio) {

        this.id = id;
        this.nombre = nombre;
        this.precio = precio;
    }

    public Long getId() {
        return id;
    }

    public String getNombre() {
        return nombre;
    }

    public double getPrecio() {
        return precio;
    }
}
```

### 7.2. Servicio

```java
import java.util.List;

import org.springframework.stereotype.Service;

@Service
public class ProductoService {

    public List<Producto> obtenerProductos() {
        return List.of(
            new Producto(1L, "Teclado", 49.99),
            new Producto(2L, "Ratón", 24.99),
            new Producto(3L, "Monitor", 199.99)
        );
    }
}
```

Para simplificar, los productos se crean en memoria. En una aplicación real, el servicio normalmente consultaría un repositorio.

### 7.3. Controlador

```java
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class ProductoController {

    private final ProductoService productoService;

    public ProductoController(
            ProductoService productoService) {

        this.productoService = productoService;
    }

    @GetMapping("/productos")
    public String listarProductos(Model model) {

        model.addAttribute(
            "productos",
            productoService.obtenerProductos()
        );

        return "productos";
    }
}
```

La anotación `@Controller` identifica un controlador web. El método recibe una petición GET, prepara los datos para la vista y devuelve el nombre lógico de una plantilla.

### 7.4. Vista con Thymeleaf

Archivo: `src/main/resources/templates/productos.html`

```html
<!DOCTYPE html>
<html lang="es"
      xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Listado de productos</title>
</head>
<body>
    <h1>Productos</h1>

    <ul>
        <li th:each="producto : ${productos}">
            <span th:text="${producto.nombre}"></span>
            -
            <span th:text="${producto.precio}"></span>
            €
        </li>
    </ul>
</body>
</html>
```

Cuando el navegador solicita `/productos`, Spring MVC ejecuta el controlador, obtiene la lista de productos y entrega los datos a la plantilla.

Thymeleaf genera el HTML que recibe el navegador.

Para ejecutar el ejemplo se necesita un proyecto Spring Boot con las dependencias web y Thymeleaf configuradas.

---

## 8. Ventajas y desventajas

### Ventajas

1. **Separación de responsabilidades:** cada componente tiene una función claramente definida.
2. **Mantenibilidad:** los cambios en la presentación pueden realizarse sin modificar necesariamente las reglas de negocio.
3. **Testabilidad:** el modelo y los servicios pueden probarse sin construir una interfaz gráfica.
4. **Reutilización:** la misma lógica puede servir a diferentes vistas o interfaces.
5. **Organización del código:** facilita distribuir las responsabilidades entre clases y equipos.
6. **Evolución de la interfaz:** permite sustituir o ampliar la presentación con menor impacto en el modelo.
7. **Integración con frameworks:** muchos frameworks proporcionan herramientas para implementar MVC de forma organizada.

### Desventajas

1. **Mayor número de componentes:** una aplicación pequeña puede requerir más clases y configuración.
2. **Complejidad inicial:** es necesario decidir dónde debe vivir cada responsabilidad.
3. **Controladores sobrecargados:** si acumulan lógica de negocio, pierden su función de coordinación.
4. **Modelos mal definidos:** un modelo que depende de HTML, plantillas o detalles de presentación dificulta la reutilización.
5. **Duplicación accidental:** distintas vistas o controladores pueden repetir lógica si no existe una separación adecuada.
6. **Complejidad de integración:** en aplicaciones grandes hay que coordinar servicios, persistencia, validación y tratamiento de errores.
7. **Variantes diferentes:** la interpretación del papel de la vista y del controlador cambia según el framework.

MVC es especialmente útil cuando una aplicación tiene varias responsabilidades de presentación y necesita evolucionar de forma mantenible. Para una herramienta muy pequeña, su estructura puede resultar innecesaria.

---

## 9. Relación con los principios SOLID

### Single Responsibility Principle (SRP)

MVC favorece la separación de responsabilidades:

- La vista presenta información.
- El controlador coordina las peticiones.
- El modelo representa datos y comportamiento del dominio.

Esta separación ayuda a evitar clases que gestionen simultáneamente la interfaz, la persistencia y las reglas de negocio.

### Open/Closed Principle (OCP)

Una vista puede sustituirse o ampliarse sin modificar necesariamente el modelo.

Por ejemplo, podemos crear una nueva vista para mostrar las mismas tareas en una tabla en lugar de una lista.

No obstante, esto requiere que la lógica de negocio no dependa de detalles específicos de la vista original.

### Liskov Substitution Principle (LSP)

Cuando utilizamos interfaces para representar servicios, repositorios o componentes intercambiables, sus implementaciones deben respetar los contratos establecidos.

MVC no garantiza automáticamente LSP, pero su separación facilita diseñar componentes sustituibles.

### Interface Segregation Principle (ISP)

Definir interfaces pequeñas para los servicios que utilizan los controladores evita que estos dependan de operaciones que no necesitan.

Por ejemplo, un controlador de consulta puede depender de una interfaz de lectura en lugar de una interfaz que incluya todas las operaciones de modificación.

### Dependency Inversion Principle (DIP)

Los controladores deberían depender de abstracciones cuando esto aporte valor, y no necesitar crear directamente todas sus dependencias.

La inyección de dependencias facilita sustituir servicios por implementaciones de prueba y reduce el acoplamiento con clases concretas.

**Importante:** MVC y SOLID son conceptos distintos. MVC organiza responsabilidades a nivel arquitectónico; SOLID orienta el diseño de clases y sus dependencias.

---

## 10. Diferencias con otros patrones y arquitecturas

### MVC vs. MVP

MVP significa *Model-View-Presenter*.

- **MVC:** el controlador coordina las acciones y el flujo de la aplicación.
- **MVP:** el Presenter concentra la lógica de presentación y suele comunicarse con la vista mediante una interfaz explícita.

MVP se utiliza en determinados entornos donde se quiere probar la lógica de presentación de forma aislada.

### MVC vs. MVVM

MVVM significa *Model-View-ViewModel*.

- **MVC:** utiliza un controlador para gestionar las interacciones.
- **MVVM:** introduce un ViewModel que expone el estado y las operaciones que necesita la vista.

MVVM encaja especialmente bien con interfaces que utilizan data binding y mecanismos reactivos.

### MVC vs. Clean Architecture

- **MVC:** organiza principalmente la interacción y la presentación.
- **Clean Architecture:** organiza las dependencias de toda la aplicación alrededor del dominio y de sus reglas.

Ambos pueden combinarse. Una aplicación puede utilizar MVC en su capa de entrada y presentación, mientras que los controladores delegan los casos de uso en una arquitectura interna más independiente.

### MVC vs. arquitectura en capas

- **MVC:** separa Modelo, Vista y Controlador.
- **Arquitectura en capas:** organiza el sistema en capas como presentación, aplicación, dominio e infraestructura.

Una aplicación MVC puede utilizar además una arquitectura en capas. De hecho, es habitual que el controlador forme parte de la capa de presentación y delegue las operaciones en servicios de aplicación.

### MVC vs. Observer

- **MVC:** estructura la interacción entre presentación, coordinación y modelo.
- **Observer:** define una relación de notificación entre un sujeto y sus observadores.

Algunas implementaciones MVC utilizan mecanismos similares a Observer para propagar cambios del modelo a la vista, pero no todas lo hacen.

---

## 11. Casos de uso habituales

### Aplicaciones web

- Formularios de registro.
- Paneles administrativos.
- Listados y búsquedas.
- Gestión de usuarios.
- Aplicaciones de comercio electrónico.

### Aplicaciones de escritorio

- Sistemas de gestión.
- Herramientas de edición.
- Aplicaciones con múltiples ventanas.
- Programas de administración de datos.

### Aplicaciones empresariales

- Sistemas de inventario.
- Gestión de pedidos.
- Facturación.
- Gestión de empleados.
- Aplicaciones de atención al cliente.

### APIs y servicios HTTP

Puede utilizarse una estructura similar para separar el controlador HTTP, los servicios de aplicación y el acceso a los datos. Sin embargo, una API que devuelve JSON no necesariamente utiliza una vista visual tradicional.

---

## 12. Buenas prácticas

1. **Mantén los controladores ligeros:** deberían coordinar operaciones, no implementar todas las reglas del negocio.
2. **Evita introducir HTML en el modelo:** la lógica del dominio debe permanecer independiente de la presentación.
3. **Centraliza las reglas de negocio:** utiliza servicios o componentes de dominio cuando la lógica lo requiera.
4. **Utiliza inyección de dependencias:** facilita las pruebas y reduce el acoplamiento.
5. **Valida en las fronteras del sistema:** comprueba las entradas recibidas antes de ejecutar las operaciones correspondientes.
6. **Distingue validación de entrada y reglas del dominio:** no toda validación debe residir en el controlador.
7. **No expongas directamente entidades internas sin valorar sus implicaciones:** utiliza DTO cuando ayuden a definir contratos de entrada y salida.
8. **Gestiona los errores de forma coherente:** evita repetir el mismo tratamiento en todos los controladores.
9. **Mantén las vistas enfocadas en la presentación:** evita que ejecuten operaciones complejas de negocio.
10. **Prueba cada componente por separado:** combina pruebas unitarias y de integración.
11. **Evita controladores gigantes:** si gestionan demasiados casos de uso, divide las responsabilidades.
12. **Adapta MVC al framework:** sigue sus convenciones sin perder de vista los límites de las responsabilidades.

---

## 13. Pruebas recomendadas

Una aplicación MVC puede probarse en distintos niveles.

### Pruebas del modelo

Comprueban que las entidades y servicios respetan las reglas del dominio.

Por ejemplo:

- Una tarea se marca como completada.
- No se puede obtener una tarea inexistente.
- Las entradas inválidas producen el error esperado.

### Pruebas del controlador

Comprueban que el controlador coordina correctamente la petición.

Por ejemplo:

- Invoca el servicio adecuado.
- Gestiona los resultados.
- Devuelve la vista o respuesta esperada.
- Trata correctamente los errores.

En Spring Boot, pueden utilizarse herramientas como `MockMvc` para probar controladores web sin iniciar necesariamente un servidor HTTP completo.

### Pruebas de vista

Comprueban que la plantilla o el componente visual presenta correctamente los datos.

Por ejemplo:

- Los nombres se muestran correctamente.
- Los estados se representan de forma adecuada.
- Los mensajes de validación aparecen cuando corresponde.

### Pruebas de integración

Comprueban que las distintas partes funcionan juntas.

Por ejemplo:

1. El usuario envía una petición.
2. El controlador invoca el servicio.
3. El servicio obtiene o modifica los datos.
4. La respuesta contiene la información esperada.

---

## 14. Cuándo utilizar MVC y cuándo evitarlo

### Utiliza MVC cuando:

- La aplicación tiene una interfaz de usuario con distintas interacciones.
- Necesitas separar presentación y lógica de negocio.
- Quieres facilitar las pruebas de los componentes.
- Distintas vistas pueden reutilizar las mismas operaciones.
- La aplicación debe evolucionar sin mezclar todas las responsabilidades.

### Valora alternativas o una implementación más sencilla cuando:

- El programa es un script muy pequeño.
- No existe una interfaz de usuario significativa.
- La separación añade más complejidad que beneficio.
- Otro patrón de presentación se adapta mejor al framework o al tipo de interfaz.
- La aplicación necesita una arquitectura más amplia para organizar el dominio y la infraestructura.

MVC no obliga a organizar toda la aplicación únicamente en tres paquetes. En sistemas grandes, puede complementarse con servicios de aplicación, repositorios, DTO, módulos de dominio y otras estructuras.

---

## 15. Conclusión

El patrón arquitectónico **MVC (Model-View-Controller)** permite separar una aplicación en tres componentes fundamentales: Modelo, Vista y Controlador.

El modelo representa los datos y el comportamiento del dominio; la vista presenta la información; y el controlador coordina las interacciones del usuario.

Esta separación facilita la mantenibilidad, las pruebas y la evolución de la aplicación, especialmente en sistemas con interfaces complejas o múltiples formas de presentación.

MVC puede utilizarse junto con una arquitectura en capas, Clean Architecture, servicios de aplicación y otros patrones, siempre que las responsabilidades y las dependencias estén bien definidas.

La idea fundamental es: **separar los datos y el comportamiento de la aplicación, la presentación visual y la coordinación de las interacciones para conseguir un sistema más organizado, mantenible y fácil de probar.**

---

## 16. Referencias

- Oracle Java Documentation: https://docs.oracle.com/en/java/
- Spring Framework — Web MVC: https://docs.spring.io/spring-framework/reference/web/webmvc.html
- Martin Fowler — GUI Architectures: https://martinfowler.com/eaaDev/uiArchs.html
- MDN Web Docs — Conceptos de arquitectura web: https://developer.mozilla.org/
- *Patterns of Enterprise Application Architecture*, de Martin Fowler.
