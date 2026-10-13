# Patrón MVP (Model-View-Presenter)

## 1. Introducción

**MVP (Model-View-Presenter)** es un patrón de arquitectura de presentación que separa una aplicación en tres componentes principales: **Model**, **View** y **Presenter**.

Su objetivo es reducir el acoplamiento entre la interfaz gráfica y la lógica que determina cómo debe comportarse esa interfaz.

En MVP, la vista delega las acciones del usuario al Presenter, y este coordina las operaciones necesarias para actualizar la interfaz. El Model representa los datos y las reglas de negocio, normalmente mediante servicios, repositorios y entidades.

MVP se utiliza especialmente en aplicaciones con interfaces gráficas, aplicaciones de escritorio, aplicaciones móviles y sistemas donde se desea probar la lógica de presentación sin depender de una interfaz real.

### Objetivos principales

- Separar la lógica de presentación de la interfaz gráfica.
- Facilitar las pruebas unitarias.
- Reducir las responsabilidades de las vistas.
- Mejorar la mantenibilidad y la legibilidad del código.
- Permitir cambiar la tecnología de interfaz sin reescribir toda la lógica de presentación.
- Facilitar el trabajo paralelo entre desarrolladores de interfaz y desarrolladores de lógica de aplicación.

### Idea fundamental

La **View** muestra información y comunica las acciones del usuario. El **Presenter** interpreta esas acciones, consulta o modifica el Model y ordena a la View qué debe mostrar.

A diferencia de MVVM, MVP suele utilizar una colaboración explícita entre la View y el Presenter, normalmente mediante una interfaz implementada por la vista.

---

## 2. Problema que resuelve

En una aplicación sencilla, es habitual que la propia vista contenga toda la lógica:

```java
public class LoginView {

    public void onLoginClicked(String username, String password) {
        if (username.isBlank() || password.isBlank()) {
            showError("Los campos son obligatorios");
            return;
        }

        // Consultar usuario, validar credenciales,
        // gestionar errores y navegar a otra pantalla.
    }

    private void showError(String message) {
        System.out.println(message);
    }
}
```

Este enfoque puede funcionar inicialmente, pero a medida que crece la aplicación aparecen varios problemas.

### 2.1. Mezcla de responsabilidades

La vista se encarga de mostrar elementos, validar datos, coordinar servicios y decidir qué ocurre después de una acción.

### 2.2. Dificultad para realizar pruebas

Para probar la lógica de presentación puede ser necesario inicializar componentes gráficos, simular eventos de interfaz o arrancar parte de la aplicación.

### 2.3. Alto acoplamiento

La lógica depende de una clase concreta de interfaz. Cambiar la tecnología gráfica puede obligar a modificar numerosas clases.

### 2.4. Dificultad para mantener el código

Cuando una pantalla incorpora muchas reglas y operaciones, resulta complicado entender qué parte corresponde a la interfaz y cuál a la lógica de presentación.

**MVP aborda estos problemas trasladando la lógica de presentación al Presenter y definiendo un contrato explícito para la View.**

---

## 3. Solución propuesta

MVP divide la presentación en tres componentes.

### 3.1. Model

Representa los datos y las operaciones que necesita la aplicación.

Puede incluir:

- Entidades y objetos de dominio.
- Servicios de aplicación.
- Repositorios.
- Acceso a bases de datos o APIs.
- Reglas de negocio.

En una arquitectura bien organizada, el Model no depende de la View ni del Presenter.

### 3.2. View

Representa la interfaz con la que interactúa el usuario.

Sus responsabilidades principales son:

- Mostrar datos.
- Capturar acciones del usuario.
- Delegar esas acciones al Presenter.
- Implementar el contrato definido para la vista.
- Mostrar estados de carga, errores y resultados.

La View debería contener la menor cantidad posible de lógica de presentación.

### 3.3. Presenter

Actúa como intermediario entre la View y el Model.

Sus responsabilidades incluyen:

- Recibir las acciones de la View.
- Validar los datos de entrada relacionados con la presentación.
- Invocar servicios o repositorios.
- Transformar resultados en información apropiada para mostrar.
- Decidir qué estado visual debe presentarse.
- Ordenar a la View que muestre resultados, errores o estados de carga.

El Presenter no debería manipular directamente componentes gráficos concretos, como botones, etiquetas o campos de texto. Para ello utiliza el contrato de la View.

---

## 4. Diagrama conceptual

```text
                 Usuario
                    |
                    v
             +-------------+
             |    View     |
             |  Interfaz   |
             +-------------+
                    |
        Acciones    |    Métodos del contrato
                    v
             +-------------+
             |  Presenter  |
             | Lógica de   |
             | presentación|
             +-------------+
                    |
         Consulta / ejecución
                    v
             +-------------+
             |    Model    |
             | Servicios,  |
             | repositorios|
             | y entidades |
             +-------------+
                    |
             Resultado
                    v
             +-------------+
             |  Presenter  |
             +-------------+
                    |
          Actualización de View
                    v
             +-------------+
             |    View     |
             +-------------+
```

El Presenter recibe las acciones de la View, coordina el trabajo con el Model y devuelve a la View instrucciones para actualizar la interfaz.

### Flujo típico

1. El usuario interactúa con la interfaz.
2. La View captura la acción.
3. La View invoca un método del Presenter.
4. El Presenter valida la entrada y consulta el Model.
5. El Model devuelve un resultado.
6. El Presenter interpreta el resultado.
7. El Presenter llama a los métodos del contrato de la View.
8. La View presenta la información al usuario.

---

## 5. Ejemplo completo en Java: gestión de tareas

Implementaremos una pequeña aplicación que permite consultar tareas y marcar una tarea como completada.

El ejemplo estará dividido en capas:

```text
src/
├── domain/
│   └── Task.java
├── application/
│   ├── TaskService.java
│   └── InMemoryTaskService.java
└── presentation/
    ├── TaskView.java
    ├── TaskPresenter.java
    └── ConsoleTaskView.java
```

Para mantener el ejemplo autocontenido, utilizaremos una implementación en memoria del servicio.

### 5.1. Model: entidad Task

```java
package domain;

public class Task {

    private final long id;
    private final String title;
    private boolean completed;

    public Task(long id, String title) {
        this.id = id;
        this.title = title;
        this.completed = false;
    }

    public long getId() {
        return id;
    }

    public String getTitle() {
        return title;
    }

    public boolean isCompleted() {
        return completed;
    }

    public void markAsCompleted() {
        this.completed = true;
    }
}
```

La clase `Task` representa una tarea y su estado. No necesita conocer la interfaz gráfica ni el Presenter.

### 5.2. Servicio de aplicación

Definimos un contrato para las operaciones que necesita la presentación.

```java
package application;

import domain.Task;
import java.util.List;
import java.util.Optional;

public interface TaskService {

    List<Task> findAll();

    Optional<Task> findById(long id);

    void completeTask(long id);
}
```

Esta interfaz permite sustituir la implementación en memoria por otra basada en una base de datos, una API o cualquier otro mecanismo.

### 5.3. Implementación del servicio

```java
package application;

import domain.Task;

import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

public class InMemoryTaskService implements TaskService {

    private final List<Task> tasks = new ArrayList<>();

    public InMemoryTaskService() {
        tasks.add(new Task(1, "Estudiar MVP"));
        tasks.add(new Task(2, "Escribir pruebas unitarias"));
        tasks.add(new Task(3, "Revisar arquitectura"));
    }

    @Override
    public List<Task> findAll() {
        return List.copyOf(tasks);
    }

    @Override
    public Optional<Task> findById(long id) {
        return tasks.stream()
                .filter(task -> task.getId() == id)
                .findFirst();
    }

    @Override
    public void completeTask(long id) {
        Task task = findById(id)
                .orElseThrow(() ->
                        new IllegalArgumentException(
                                "No existe la tarea con id " + id
                        )
                );

        task.markAsCompleted();
    }
}
```

La implementación mantiene los datos en memoria. En un proyecto real, podría delegar en un repositorio persistente.

### 5.4. Contrato de la View

La interfaz de la View define las operaciones que el Presenter puede solicitar.

```java
package presentation;

import domain.Task;
import java.util.List;

public interface TaskView {

    void showTasks(List<Task> tasks);

    void showLoading();

    void hideLoading();

    void showMessage(String message);

    void showError(String message);
}
```

El Presenter depende de esta abstracción, no de una clase concreta de interfaz.

Por ejemplo, una vista de consola, una ventana de escritorio o una pantalla móvil podrían implementar el mismo contrato, siempre que ofrezcan las operaciones necesarias.

### 5.5. Presenter

```java
package presentation;

import application.TaskService;
import domain.Task;

import java.util.List;

public class TaskPresenter {

    private final TaskView view;
    private final TaskService taskService;

    public TaskPresenter(
            TaskView view,
            TaskService taskService
    ) {
        this.view = view;
        this.taskService = taskService;
    }

    public void loadTasks() {
        view.showLoading();

        try {
            List<Task> tasks = taskService.findAll();
            view.showTasks(tasks);
        } catch (RuntimeException exception) {
            view.showError("No se pudieron cargar las tareas");
        } finally {
            view.hideLoading();
        }
    }

    public void completeTask(long id) {
        try {
            taskService.completeTask(id);

            view.showMessage("Tarea completada correctamente");

            loadTasks();
        } catch (IllegalArgumentException exception) {
            view.showError(exception.getMessage());
        } catch (RuntimeException exception) {
            view.showError("No se pudo completar la tarea");
        }
    }
}
```

El Presenter coordina la interacción entre la View y el servicio.

Observa que:

- No conoce los componentes gráficos concretos.
- No accede directamente a una base de datos.
- Decide qué debe mostrar la View.
- Puede probarse mediante una implementación simulada de `TaskView`.

**Nota:** este ejemplo es síncrono. En una aplicación real con operaciones de red o base de datos, conviene utilizar callbacks, futuros o mecanismos asíncronos y actualizar la interfaz desde el hilo apropiado.

### 5.6. Implementación de la View

Para mostrar el funcionamiento, utilizaremos una vista de consola.

```java
package presentation;

import domain.Task;

import java.util.List;

public class ConsoleTaskView implements TaskView {

    @Override
    public void showTasks(List<Task> tasks) {
        System.out.println("\n--- LISTA DE TAREAS ---");

        if (tasks.isEmpty()) {
            System.out.println("No hay tareas disponibles");
            return;
        }

        for (Task task : tasks) {
            String status = task.isCompleted()
                    ? "COMPLETADA"
                    : "PENDIENTE";

            System.out.printf(
                    "%d - %s [%s]%n",
                    task.getId(),
                    task.getTitle(),
                    status
            );
        }
    }

    @Override
    public void showLoading() {
        System.out.println("Cargando tareas...");
    }

    @Override
    public void hideLoading() {
        System.out.println("Carga finalizada");
    }

    @Override
    public void showMessage(String message) {
        System.out.println("INFO: " + message);
    }

    @Override
    public void showError(String message) {
        System.err.println("ERROR: " + message);
    }
}
```

Esta clase se limita a presentar información. No conoce las reglas de coordinación que implementa el Presenter.

### 5.7. Clase principal

```java
import application.InMemoryTaskService;
import application.TaskService;
import presentation.ConsoleTaskView;
import presentation.TaskPresenter;
import presentation.TaskView;

public class Main {

    public static void main(String[] args) {
        TaskView view = new ConsoleTaskView();
        TaskService service = new InMemoryTaskService();

        TaskPresenter presenter =
                new TaskPresenter(view, service);

        presenter.loadTasks();
        presenter.completeTask(2);
    }
}
```

### 5.8. Resultado aproximado

```text
Cargando tareas...

--- LISTA DE TAREAS ---
1 - Estudiar MVP [PENDIENTE]
2 - Escribir pruebas unitarias [PENDIENTE]
3 - Revisar arquitectura [PENDIENTE]
Carga finalizada

INFO: Tarea completada correctamente
Cargando tareas...

--- LISTA DE TAREAS ---
1 - Estudiar MVP [PENDIENTE]
2 - Escribir pruebas unitarias [COMPLETADA]
3 - Revisar arquitectura [PENDIENTE]
Carga finalizada
```

El ejemplo muestra cómo una acción puede pasar por la View y el Presenter hasta el servicio, y cómo el Presenter solicita después una actualización de la interfaz.

---

## 6. Variantes de MVP

MVP tiene varias variantes. La terminología no es completamente uniforme entre autores y frameworks, pero las siguientes distinciones son habituales.

### 6.1. MVP clásico

La View conoce al Presenter y delega en él las acciones del usuario.

El Presenter mantiene una referencia a la View, normalmente mediante una interfaz, y actualiza la interfaz invocando sus métodos.

```text
Usuario
   |
   v
 View -----> Presenter -----> Model
   ^              |
   |______________|
```

**Ventajas:**

- Flujo de control explícito.
- Separación clara entre presentación e interfaz.
- Fácil de probar con dobles de prueba.

**Inconvenientes:**

- El Presenter puede acumular demasiadas responsabilidades.
- Una interfaz de View extensa puede ser difícil de mantener.

### 6.2. Passive View

Es una variante en la que la View contiene muy poca lógica y evita tomar decisiones sobre la presentación.

El Presenter dirige prácticamente todo el comportamiento de la pantalla.

Por ejemplo, el Presenter determina si debe mostrarse un error, si un botón debe habilitarse o qué texto debe aparecer.

**Ventajas:**

- Muy buena capacidad de prueba.
- La lógica de presentación se concentra en un lugar.
- Menor dependencia de los componentes gráficos.

**Inconvenientes:**

- Puede generar Presenters grandes.
- Las interfaces de vista pueden incluir muchos métodos pequeños.

Esta variante es especialmente interesante cuando la lógica de presentación es compleja o necesita pruebas unitarias exhaustivas.

### 6.3. Supervising Controller

En esta variante, la View puede encargarse de algunas tareas sencillas de presentación, mientras el Presenter gestiona las decisiones más complejas.

Por ejemplo, la View puede mostrar una propiedad sencilla del Model, mientras el Presenter coordina una operación que requiere validación, varias llamadas a servicios o tratamiento de errores.

**Ventajas:**

- Menos código en el Presenter para interfaces sencillas.
- Puede resultar más natural en determinadas tecnologías gráficas.

**Inconvenientes:**

- La separación de responsabilidades es menos estricta.
- Si se añaden demasiadas reglas a la View, puede perderse la ventaja de MVP.

---

## 7. Relación con MVC y MVVM

MVP, MVC y MVVM intentan separar la interfaz de usuario de la lógica de presentación, pero organizan la colaboración entre sus componentes de manera diferente.

| Aspecto | MVC | MVP | MVVM |
|---|---|---|---|
| Componente principal de presentación | Controller | Presenter | ViewModel |
| Papel de la View | Muestra información y recibe interacción | Muestra información y delega acciones | Se vincula al estado del ViewModel |
| Comunicación con la View | Depende de la implementación | Habitualmente explícita mediante una interfaz | Habitualmente mediante data binding u observación |
| Actualización de la interfaz | Coordinada por el flujo de MVC | El Presenter invoca métodos de la View | La View reacciona a cambios de estado |
| Dependencia de la interfaz | Variable | El Presenter depende del contrato de la View | El ViewModel intenta evitar depender de la View |
| Pruebas de presentación | Buenas con separación adecuada | Muy buenas mediante una View simulada | Muy buenas mediante un ViewModel aislado |
| Complejidad inicial | Moderada | Moderada | Variable; puede requerir binding y gestión de estado |

### ¿Cuándo elegir MVP?

MVP es una buena opción cuando:

- Se desea un flujo de control explícito.
- La tecnología gráfica facilita definir interfaces de vista.
- Se necesita probar la lógica de presentación sin inicializar la interfaz.
- La aplicación tiene pantallas con interacciones y validaciones complejas.
- El equipo prefiere que el Presenter controle de manera directa las actualizaciones de la View.

### ¿Cuándo elegir MVVM?

MVVM puede resultar más conveniente cuando el framework ofrece mecanismos sólidos de data binding, propiedades observables o flujos reactivos.

### ¿Cuándo elegir MVC?

MVC puede ser una buena opción cuando el framework ya estructura la aplicación alrededor de controladores, rutas y vistas, como sucede en muchas aplicaciones web.

No existe un ganador universal: la elección depende de la tecnología, el equipo y la complejidad de la presentación.

---

## 8. Relación con los principios SOLID

MVP no garantiza por sí solo el cumplimiento de SOLID, pero puede ayudar a aplicarlo.

### 8.1. Single Responsibility Principle (SRP)

Cada componente tiene una responsabilidad principal:

- `TaskView`: representar la interfaz.
- `TaskPresenter`: coordinar la lógica de presentación.
- `TaskService`: ofrecer operaciones de aplicación.
- `Task`: representar el concepto de dominio.

Si el Presenter acumula validaciones complejas, coordinación de múltiples procesos y reglas de negocio, conviene extraer esas responsabilidades.

### 8.2. Open/Closed Principle (OCP)

Es posible incorporar nuevas implementaciones de `TaskView` sin modificar el Presenter, siempre que respeten el contrato.

Por ejemplo:

```java
TaskView consoleView = new ConsoleTaskView();
```

Otra implementación podría representar una ventana de escritorio o una vista de pruebas.

### 8.3. Liskov Substitution Principle (LSP)

Cualquier implementación válida de `TaskView` debe cumplir el comportamiento esperado por el Presenter.

Una implementación no debería, por ejemplo, lanzar excepciones inesperadas en operaciones que el contrato considera válidas ni ignorar sistemáticamente las actualizaciones solicitadas.

### 8.4. Interface Segregation Principle (ISP)

Las interfaces de vista deben ser coherentes y no incluir operaciones innecesarias.

Si una pantalla se vuelve demasiado compleja, puede ser útil dividir el contrato en interfaces más pequeñas. Sin embargo, no conviene fragmentarlas artificialmente.

### 8.5. Dependency Inversion Principle (DIP)

El Presenter depende de abstracciones:

```java
private final TaskView view;
private final TaskService taskService;
```

No depende de `ConsoleTaskView` ni de una implementación concreta del servicio.

Este diseño facilita sustituir dependencias y realizar pruebas.

---

## 9. Ventajas

### 9.1. Alta capacidad de prueba

La lógica de presentación puede probarse sin abrir ventanas, crear pantallas reales o simular componentes gráficos complejos.

### 9.2. Separación de responsabilidades

La View se centra en presentar información; el Presenter coordina el comportamiento de la pantalla.

### 9.3. Menor acoplamiento con la tecnología gráfica

El Presenter depende de una interfaz de vista y no de sus detalles internos.

### 9.4. Flujo de control explícito

Las llamadas desde la View al Presenter y desde el Presenter a la View hacen que el comportamiento sea relativamente fácil de seguir.

### 9.5. Sustitución de dependencias

Se pueden sustituir servicios, repositorios y vistas por implementaciones alternativas.

### 9.6. Adecuado para interfaces con lógica compleja

Cuando una pantalla tiene múltiples estados, validaciones, errores y operaciones, centralizar la coordinación en el Presenter puede simplificar la View.

---

## 10. Desventajas

### 10.1. Mayor cantidad de clases e interfaces

Una pantalla puede necesitar una interfaz de View, una implementación de View y un Presenter, además de sus servicios.

En aplicaciones pequeñas, esta estructura puede parecer excesiva.

### 10.2. Presenters demasiado grandes

Si el Presenter se encarga de toda la lógica de la pantalla, puede convertirse en una clase difícil de mantener.

**Solución:** extraer casos de uso, validadores, coordinadores o servicios cuando exista una responsabilidad diferenciada.

### 10.3. Interfaces de vista extensas

En la variante Passive View, cada estado visual puede necesitar un método. Esto puede provocar contratos con muchas operaciones.

**Solución:** agrupar operaciones relacionadas de forma coherente o utilizar modelos de estado de presentación cuando la tecnología y el diseño lo permitan.

### 10.4. Gestión del ciclo de vida

En aplicaciones móviles, una referencia del Presenter a una View puede mantener una pantalla en memoria después de que debería haberse liberado.

**Solución:** desconectar la View cuando corresponda, cancelar trabajos en curso y definir claramente el ciclo de vida del Presenter.

### 10.5. Operaciones asíncronas

Las peticiones de red, los procesos en segundo plano y las respuestas tardías requieren una estrategia explícita de concurrencia y cancelación.

**Solución:** utilizar abstracciones asíncronas apropiadas para la plataforma y proteger las actualizaciones de una View que ya no esté activa.

---

## 11. Buenas prácticas

### 11.1. Mantener la View lo más sencilla posible

La View debería capturar eventos y delegarlos al Presenter.

```java
// Ejemplo conceptual de una View
public void onCompleteButtonClicked(long taskId) {
    presenter.completeTask(taskId);
}
```

La View no debería decidir cómo se ejecuta la operación ni acceder directamente a la base de datos.

### 11.2. No introducir reglas de negocio en el Presenter

El Presenter puede validar entradas desde el punto de vista de la presentación, pero las reglas de negocio importantes deben residir en el dominio o en los servicios de aplicación correspondientes.

Por ejemplo, la View puede comprobar que se ha introducido un identificador válido, pero la regla que determina si una tarea puede completarse debería pertenecer a la lógica de negocio.

### 11.3. Depender de interfaces

Es preferible inyectar servicios y contratos en lugar de crear dependencias concretas dentro del Presenter.

```java
public TaskPresenter(
        TaskView view,
        TaskService taskService
) {
    this.view = view;
    this.taskService = taskService;
}
```

Esto facilita las pruebas y reduce el acoplamiento.

### 11.4. Definir estados visuales claros

Una pantalla puede necesitar representar estados como:

- Inicial.
- Cargando.
- Con datos.
- Vacío.
- Con error.

El Presenter debe coordinar estas transiciones para evitar que la interfaz muestre estados contradictorios.

### 11.5. Gestionar correctamente los errores

Evita capturar excepciones indiscriminadamente sin registrar o interpretar su causa.

En aplicaciones reales, es preferible traducir errores técnicos a mensajes comprensibles para el usuario y conservar la información técnica necesaria para diagnosticar el problema.

### 11.6. Gestionar el ciclo de vida

En aplicaciones con pantallas que se crean y destruyen, debe existir un mecanismo para conectar y desconectar la View del Presenter.

También es importante cancelar peticiones o ignorar resultados obsoletos cuando el usuario abandona una pantalla.

### 11.7. Evitar el exceso de abstracciones

MVP ya introduce una separación estructural. No es necesario añadir interfaces para todas las clases sin una razón concreta.

Las abstracciones deben facilitar las pruebas, permitir sustituir implementaciones o proteger límites importantes del sistema.

---

## 12. Pruebas unitarias del Presenter

Una de las principales ventajas de MVP es que el Presenter puede probarse de manera aislada.

Para ello, se puede implementar una View de prueba que registre las operaciones que recibe.

El siguiente ejemplo utiliza JUnit 5.

### 12.1. View de prueba

```java
import domain.Task;
import presentation.TaskView;

import java.util.List;

public class FakeTaskView implements TaskView {

    public List<Task> displayedTasks;
    public String lastMessage;
    public String lastError;
    public boolean loading;
    public boolean loadingWasHidden;

    @Override
    public void showTasks(List<Task> tasks) {
        this.displayedTasks = tasks;
    }

    @Override
    public void showLoading() {
        loading = true;
    }

    @Override
    public void hideLoading() {
        loading = false;
        loadingWasHidden = true;
    }

    @Override
    public void showMessage(String message) {
        lastMessage = message;
    }

    @Override
    public void showError(String message) {
        lastError = message;
    }
}
```

### 12.2. Prueba de carga de tareas

```java
import application.InMemoryTaskService;
import org.junit.jupiter.api.Test;
import presentation.TaskPresenter;

import static org.junit.jupiter.api.Assertions.*;

class TaskPresenterTest {

    @Test
    void shouldLoadTasksAndHideLoadingIndicator() {
        FakeTaskView view = new FakeTaskView();

        TaskPresenter presenter = new TaskPresenter(
                view,
                new InMemoryTaskService()
        );

        presenter.loadTasks();

        assertNotNull(view.displayedTasks);
        assertEquals(3, view.displayedTasks.size());
        assertFalse(view.loading);
        assertTrue(view.loadingWasHidden);
        assertNull(view.lastError);
    }
}
```

Esta prueba verifica el comportamiento del Presenter sin utilizar una interfaz gráfica real.

### 12.3. Prueba de finalización de una tarea

```java
import application.InMemoryTaskService;
import domain.Task;
import org.junit.jupiter.api.Test;
import presentation.TaskPresenter;

import java.util.List;

import static org.junit.jupiter.api.Assertions.*;

class TaskCompletionPresenterTest {

    @Test
    void shouldCompleteTaskAndRefreshView() {
        FakeTaskView view = new FakeTaskView();
        InMemoryTaskService service = new InMemoryTaskService();

        TaskPresenter presenter = new TaskPresenter(view, service);

        presenter.completeTask(2);

        assertEquals(
                "Tarea completada correctamente",
                view.lastMessage
        );

        List<Task> tasks = service.findAll();

        Task completedTask = tasks.stream()
                .filter(task -> task.getId() == 2)
                .findFirst()
                .orElseThrow();

        assertTrue(completedTask.isCompleted());
        assertNull(view.lastError);
    }
}
```

En proyectos más grandes, es conveniente utilizar dobles de prueba o mocks para verificar las interacciones con los servicios sin depender de su implementación real.

**Importante:** las pruebas unitarias del Presenter no sustituyen a las pruebas de integración de los servicios ni a las pruebas de interfaz. Cada nivel verifica un tipo diferente de comportamiento.

---

## 13. MVP en aplicaciones reales

### 13.1. Aplicaciones de escritorio

MVP puede funcionar bien en interfaces creadas con Swing o JavaFX, especialmente cuando se desea evitar que los controladores de eventos acumulen lógica de presentación.

### 13.2. Aplicaciones móviles

En Android, MVP se ha utilizado para separar las actividades o fragmentos de la lógica de presentación. Es importante gestionar las referencias a la View, las operaciones asíncronas y los cambios de configuración para evitar fugas de memoria.

### 13.3. Aplicaciones empresariales

Puede utilizarse en formularios de gestión, paneles administrativos, procesos de validación y pantallas con reglas de interacción complejas.

### 13.4. Aplicaciones web

Aunque muchas aplicaciones web modernas utilizan MVC, MVVM o componentes reactivos, las ideas de MVP también pueden aplicarse cuando una interfaz delega sus acciones a un objeto coordinador independiente.

La conveniencia depende de la arquitectura y de las convenciones del framework utilizado.

---

## 14. Errores comunes

### Error 1: convertir el Presenter en un servicio de negocio

El Presenter debería coordinar la presentación, no convertirse en el lugar donde se implementan todas las reglas de negocio.

**Mejora:** delegar las operaciones de negocio a servicios o casos de uso.

### Error 2: permitir que la View consulte directamente el Model

Si la View accede directamente a los repositorios, el flujo de responsabilidades se vuelve confuso.

**Mejora:** hacer que las acciones pasen por el Presenter y que este coordine el acceso a los servicios necesarios.

### Error 3: hacer que el Presenter dependa de una View concreta

Depender de una implementación concreta reduce la flexibilidad y dificulta las pruebas.

**Mejora:** definir una interfaz de View y utilizar inyección de dependencias.

### Error 4: olvidar la gestión del ciclo de vida

Un Presenter que conserva una referencia a una pantalla destruida puede provocar fugas de memoria o actualizaciones inválidas.

**Mejora:** definir métodos o mecanismos para conectar y desconectar la View, y gestionar los trabajos en curso.

### Error 5: añadir capas sin necesidad

MVP no obliga a crear numerosas interfaces y clases auxiliares si no aportan una separación útil.

**Mejora:** aplicar el patrón de forma proporcional a la complejidad real de la interfaz.

### Error 6: confundir el Model con la base de datos

El Model no es necesariamente una tabla, una entidad ORM o una conexión a base de datos. Puede incluir el dominio, los servicios y las abstracciones de persistencia que necesita la aplicación.

---

## 15. MVP y Clean Architecture

MVP es un patrón de presentación, mientras que Clean Architecture organiza las dependencias de una aplicación en capas o límites arquitectónicos.

Por tanto, no son alternativas excluyentes: pueden utilizarse conjuntamente.

Una posible organización sería:

```text
┌───────────────────────────────────┐
│ Presentación                      │
│ View + Presenter                  │
└─────────────────┬─────────────────┘
                  │
                  v
┌───────────────────────────────────┐
│ Aplicación                        │
│ Casos de uso                      │
└─────────────────┬─────────────────┘
                  │
                  v
┌───────────────────────────────────┐
│ Dominio                           │
│ Entidades y reglas de negocio     │
└───────────────────────────────────┘

Infraestructura:
Adaptadores para bases de datos,
APIs externas y otros sistemas.
```

El diagrama es conceptual: en una implementación real, las dependencias deben orientarse de acuerdo con las reglas de la arquitectura elegida.

Por ejemplo, un Presenter podría invocar un caso de uso `CompleteTaskUseCase`, en lugar de depender directamente de un servicio de infraestructura. Así, la presentación se mantiene separada de los detalles técnicos.

### Diferencia fundamental

- **MVP** organiza la colaboración entre la interfaz y la lógica de presentación.
- **Clean Architecture** organiza los límites, las responsabilidades y la dirección de las dependencias de toda la aplicación.

---

## 16. MVP y otros patrones relacionados

| Patrón | Objetivo principal | Diferencia respecto a MVP |
|---|---|---|
| MVC | Separar Model, View y Controller | El flujo de interacción y actualización depende de la implementación de MVC. |
| MVVM | Separar la presentación mediante un ViewModel | Suele apoyarse en binding, observación o estado reactivo. |
| Observer | Notificar cambios a múltiples suscriptores | Es un patrón de comunicación que puede utilizarse dentro de MVP. |
| Strategy | Encapsular algoritmos intercambiables | Puede emplearse en un Presenter para variar una política de presentación o validación. |
| Command | Representar acciones como objetos | Puede ayudar a desacoplar operaciones solicitadas por la interfaz. |
| Facade | Simplificar el acceso a un subsistema | Puede utilizarse para ofrecer al Presenter una API simplificada de servicios. |

Estos patrones no se excluyen entre sí. MVP puede combinarse con ellos cuando existe una necesidad concreta.

---

## 17. Cuándo utilizar MVP

MVP es especialmente adecuado cuando:

- La interfaz contiene interacciones y validaciones complejas.
- Se desea realizar pruebas unitarias de la lógica de presentación.
- Se necesita separar claramente la interfaz de los servicios de aplicación.
- El framework facilita la implementación de interfaces de View.
- Se desea que el flujo de control sea explícito y fácil de seguir.
- El equipo quiere reducir la lógica contenida en actividades, controladores gráficos o componentes de interfaz.

Puede resultar menos conveniente cuando:

- La aplicación es muy pequeña y no tiene lógica de presentación relevante.
- El framework ofrece un enfoque declarativo y reactivo mucho más natural.
- La interfaz se puede implementar de forma sencilla con componentes y estado local.
- El coste de mantener contratos y clases adicionales supera el beneficio de la separación.

---

## 18. Lista de comprobación para una buena implementación

Antes de considerar terminado un diseño basado en MVP, conviene verificar lo siguiente:

- [ ] La View se limita principalmente a mostrar información y capturar eventos.
- [ ] La View delega las acciones al Presenter.
- [ ] El Presenter depende de una interfaz de View, no de una implementación concreta.
- [ ] Los servicios se inyectan como dependencias.
- [ ] Las reglas de negocio están fuera de la View y no se concentran innecesariamente en el Presenter.
- [ ] Los estados de carga, error, vacío y éxito se gestionan de manera coherente.
- [ ] Las operaciones asíncronas respetan el ciclo de vida de la interfaz.
- [ ] Las pruebas del Presenter no necesitan inicializar componentes gráficos reales.
- [ ] Los contratos de View no contienen operaciones innecesarias.
- [ ] Las abstracciones añadidas resuelven una necesidad real.

---

## 19. Conclusión

El patrón **MVP (Model-View-Presenter)** permite separar la interfaz de usuario de la lógica que coordina su comportamiento.

Su principal fortaleza es que el Presenter concentra la lógica de presentación y se comunica con la View mediante un contrato explícito. Esto facilita las pruebas, reduce el acoplamiento con la tecnología gráfica y mejora la organización del código.

Sin embargo, MVP no elimina automáticamente todos los problemas de diseño. Un Presenter demasiado grande, una View con lógica de negocio o una gestión incorrecta del ciclo de vida pueden degradar la arquitectura.

La clave consiste en mantener responsabilidades claras, depender de abstracciones, extraer las reglas de negocio a las capas adecuadas y utilizar el patrón cuando la complejidad de la presentación justifique su estructura.

**En resumen:** MVP es una buena elección cuando se busca una presentación desacoplada, explícita y fácil de probar, especialmente en interfaces con lógica de interacción significativa.

---

## 20. Referencias y lecturas recomendadas

- Martin Fowler — *GUI Architectures*: https://martinfowler.com/eaaDev/uiArchs.html
- Martin Fowler — *Passive View*: https://martinfowler.com/eaaDev/PassiveScreen.html
- Martin Fowler — *Supervising Controller*: https://martinfowler.com/eaaDev/SupervisingPresenter.html
- Robert C. Martin — *Clean Architecture: A Craftsman's Guide to Software Structure and Design*.
- Robert C. Martin — *Agile Software Development: Principles, Patterns, and Practices*.

Estas referencias ayudan a profundizar en la separación entre interfaz y lógica de presentación, así como en los principios de diseño que favorecen una arquitectura mantenible.
