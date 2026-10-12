# Patrón arquitectónico: MVVM (Model-View-ViewModel)

## 1. Introducción

MVVM (*Model-View-ViewModel*) es un patrón arquitectónico que separa una aplicación en tres componentes principales: **Modelo, Vista y ViewModel**.

Su objetivo es desacoplar la interfaz de usuario de la lógica de presentación y de los datos de la aplicación. Para conseguirlo, introduce un componente intermedio, el ViewModel, que expone el estado y las operaciones que la vista necesita.

Este patrón resulta especialmente útil en aplicaciones que utilizan interfaces reactivas, enlaces de datos (*data binding*) y mecanismos de observación de cambios.

Se utiliza habitualmente en aplicaciones de escritorio, aplicaciones móviles y aplicaciones web con frameworks que favorecen la gestión declarativa del estado.

### Los tres componentes principales

**Modelo (Model)**

Representa los datos y las reglas de negocio de la aplicación.

Puede incluir entidades del dominio, servicios, casos de uso y mecanismos de acceso a datos. El modelo no debería depender de los detalles visuales de la interfaz.

**Vista (View)**

Es la interfaz que ve y utiliza el usuario.

Puede ser una ventana, una pantalla móvil, una página web o un conjunto de componentes gráficos.

Su responsabilidad principal es presentar el estado recibido y transmitir las interacciones del usuario.

**ViewModel (View-Model)**

Es el componente que prepara y expone los datos que necesita la vista, además de proporcionar operaciones que la vista puede invocar.

Puede transformar datos del modelo en información adecuada para la presentación, mantener el estado de la interfaz y coordinar operaciones mediante servicios o casos de uso.

El ViewModel no debería depender directamente de controles gráficos concretos ni de detalles específicos de la tecnología visual.

### Ejemplo conceptual

Imaginemos una aplicación de tareas.

La pantalla muestra una lista de tareas y un botón para marcar una de ellas como completada.

Con MVVM:

1. La vista muestra las tareas expuestas por el ViewModel.
2. El usuario pulsa el botón de completar.
3. La vista invoca una operación del ViewModel.
4. El ViewModel solicita al modelo o al servicio correspondiente que complete la tarea.
5. El ViewModel actualiza el estado que expone a la vista.
6. La vista refleja el nuevo estado mediante *data binding* o un mecanismo de observación.

La vista no necesita conocer cómo se almacenan las tareas ni cómo se ejecutan las reglas de negocio.

---

## 2. Problema que resuelve

MVVM resuelve el problema de mezclar en la interfaz de usuario la presentación, el estado visual y la lógica de aplicación.

Sin una separación clara, una pantalla puede terminar realizando consultas, validaciones, transformaciones de datos y operaciones de negocio directamente desde los eventos de los controles gráficos.

Esto provoca varios problemas:

- **Acoplamiento entre interfaz y lógica:** los cambios visuales pueden afectar a la funcionalidad.
- **Pruebas difíciles:** la lógica depende de ventanas, botones u otros controles gráficos.
- **Duplicación de código:** varias pantallas repiten las mismas transformaciones.
- **Gestión compleja del estado:** resulta difícil mantener sincronizados los datos y la interfaz.
- **Dificultad de mantenimiento:** los controladores de eventos o las pantallas acumulan demasiadas responsabilidades.
- **Reutilización limitada:** la lógica de presentación queda vinculada a una pantalla concreta.

### Ejemplo sin MVVM

Imaginemos una pantalla que carga tareas y actualiza directamente los controles visuales.

```java
public class PantallaTareas {

    public void completarTarea(int id) {

        // Acceso directo a los datos
        Tarea tarea = buscarTarea(id);

        // Regla de negocio
        tarea.completar();

        // Actualización visual
        actualizarEtiqueta(tarea);

        // Mostrar notificación
        mostrarMensaje("Tarea completada");
    }

    private Tarea buscarTarea(int id) {
        // Búsqueda de la tarea
        return null;
    }

    private void actualizarEtiqueta(Tarea tarea) {
        // Actualización de la interfaz
    }

    private void mostrarMensaje(String mensaje) {
        // Presentación del mensaje
    }
}
```

Este código es ilustrativo, pero muestra el problema: una misma clase mezcla acceso a datos, lógica de negocio y presentación.

Si queremos reutilizar la operación desde otra pantalla, una API o una prueba automatizada, tendremos que extraer parte de esa lógica.

**MVVM propone introducir un ViewModel que concentre el estado y las operaciones necesarias para la presentación, manteniendo la vista lo más desacoplada posible.**

---

## 3. Solución propuesta

MVVM distribuye las responsabilidades entre tres componentes.

### 3.1. Modelo (Model)

Representa los datos y las reglas del dominio.

Por ejemplo, en una aplicación de tareas puede incluir:

- La entidad `Tarea`.
- Las reglas para completar una tarea.
- Los servicios de aplicación.
- Los repositorios.
- La persistencia de los datos.

El modelo no necesita conocer qué botones existen ni cómo se muestran los mensajes en pantalla.

### 3.2. Vista (View)

Presenta el estado que expone el ViewModel.

Sus responsabilidades pueden incluir:

- Mostrar listas, formularios y etiquetas.
- Recoger las interacciones del usuario.
- Enlazar controles con propiedades del ViewModel.
- Representar estados visuales como carga, error o éxito.

La vista debería contener la mínima lógica posible relacionada con la presentación.

### 3.3. ViewModel

Actúa como intermediario entre la vista y el modelo.

Sus responsabilidades habituales incluyen:

- Exponer propiedades observables.
- Preparar los datos para la presentación.
- Proporcionar comandos u operaciones invocables desde la vista.
- Mantener el estado de la interfaz.
- Gestionar estados como carga, error o éxito.
- Coordinar llamadas a servicios o casos de uso.

El ViewModel no debería manipular directamente componentes gráficos concretos, como botones, ventanas o etiquetas.

### Diagrama conceptual

```text
                Usuario
                   |
                   v
                 Vista
                   |
                   | Acciones / Data Binding
                   v
               ViewModel
                   |
                   | Servicios / Casos de uso
                   v
                 Modelo
                   |
                   v
          Repositorio / Datos
```

Los datos y los cambios de estado pueden volver a la vista mediante propiedades observables o mecanismos de enlace de datos.

```text
+-----------------------+
|         Vista         |
|-----------------------|
| Presentación          |
| Enlaces de datos      |
+-----------------------+
            |
            | Lee estado
            | Invoca acciones
            v
+-----------------------+
|       ViewModel       |
|-----------------------|
| Estado de interfaz    |
| Propiedades           |
| Comandos / Operaciones|
+-----------------------+
            |
            | Coordina
            v
+-----------------------+
|         Modelo        |
|-----------------------|
| Datos                 |
| Reglas de negocio     |
| Servicios / Casos uso |
+-----------------------+
```

### ¿Qué es el Data Binding?

El *data binding* es un mecanismo que conecta propiedades de un componente con propiedades de otro.

En MVVM, permite que la vista muestre el valor de una propiedad del ViewModel y, cuando el framework lo permite, que determinados cambios se propaguen automáticamente.

Por ejemplo, una etiqueta puede mostrar el valor de `nombreUsuario`. Si el ViewModel cambia esa propiedad y notifica el cambio correctamente, la interfaz puede actualizarse sin que el ViewModel tenga que buscar la etiqueta y modificarla directamente.

El enlace de datos no es obligatorio en todas las implementaciones de MVVM. También pueden utilizarse observables, flujos de estado o mecanismos equivalentes.

---

## 4. Implementación completa en Java

Vamos a crear una aplicación sencilla de tareas con MVVM.

Para que el ejemplo sea independiente de un framework gráfico, utilizaremos las clases de Java y un mecanismo de observación sencillo.

La vista será una simulación de consola, pero las responsabilidades se podrán adaptar posteriormente a JavaFX, Android o una interfaz web.

### 4.1. Modelo: clase `Tarea`

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

La clase `Tarea` representa una entidad del dominio.

No contiene referencias a la vista ni al ViewModel, por lo que puede utilizarse desde distintos componentes de la aplicación.

### 4.2. Modelo: servicio de tareas

El servicio proporciona las operaciones disponibles sobre las tareas.

```java
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class TareaService {

    private final Map<Integer, Tarea> tareas =
        new HashMap<>();

    public TareaService() {
        tareas.put(
            1,
            new Tarea(1, "Estudiar MVVM")
        );

        tareas.put(
            2,
            new Tarea(2, "Practicar Java")
        );
    }

    public List<Tarea> obtenerTareas() {
        return List.copyOf(tareas.values());
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

El servicio contiene la lógica de acceso y coordinación de las operaciones.

En una aplicación real, podría delegar en un repositorio persistente. Para mantener sencillo el ejemplo, los datos se guardan en memoria.

### 4.3. ViewModel

El ViewModel mantiene el estado que necesita la vista y expone operaciones que esta puede invocar.

En este ejemplo utilizaremos `PropertyChangeSupport`, disponible en Java, para notificar cambios de propiedades.

```java
import java.beans.PropertyChangeListener;
import java.beans.PropertyChangeSupport;
import java.util.List;

public class TareaViewModel {

    private final TareaService tareaService;

    private final PropertyChangeSupport cambios =
        new PropertyChangeSupport(this);

    private List<Tarea> tareas;
    private String mensaje;
    private boolean cargando;

    public TareaViewModel(TareaService tareaService) {
        this.tareaService = tareaService;
        this.tareas = List.of();
        this.mensaje = "";
        this.cargando = false;
    }

    public List<Tarea> getTareas() {
        return tareas;
    }

    public String getMensaje() {
        return mensaje;
    }

    public boolean isCargando() {
        return cargando;
    }

    public void agregarObservador(
            PropertyChangeListener listener) {

        cambios.addPropertyChangeListener(listener);
    }

    public void quitarObservador(
            PropertyChangeListener listener) {

        cambios.removePropertyChangeListener(listener);
    }

    public void cargarTareas() {
        cambiarCargando(true);

        try {
            List<Tarea> anteriores = this.tareas;

            this.tareas = tareaService.obtenerTareas();

            cambios.firePropertyChange(
                "tareas",
                anteriores,
                this.tareas
            );

            cambiarMensaje("Tareas cargadas");

        } catch (RuntimeException e) {
            cambiarMensaje(
                "No se pudieron cargar las tareas"
            );

        } finally {
            cambiarCargando(false);
        }
    }

    public void completarTarea(int id) {
        try {
            tareaService.completarTarea(id);

            cargarTareas();

            cambiarMensaje(
                "Tarea completada correctamente"
            );

        } catch (IllegalArgumentException e) {
            cambiarMensaje(e.getMessage());
        }
    }

    private void cambiarMensaje(String nuevoMensaje) {
        String anterior = this.mensaje;
        this.mensaje = nuevoMensaje;

        cambios.firePropertyChange(
            "mensaje",
            anterior,
            nuevoMensaje
        );
    }

    private void cambiarCargando(boolean nuevoEstado) {
        boolean anterior = this.cargando;
        this.cargando = nuevoEstado;

        cambios.firePropertyChange(
            "cargando",
            anterior,
            nuevoEstado
        );
    }
}
```

El ViewModel no conoce etiquetas, botones ni ventanas.

Expone el estado de las tareas, un mensaje y un indicador de carga. Además, proporciona la operación `completarTarea`, que puede ser invocada desde la vista.

El ejemplo utiliza notificaciones de propiedades para ilustrar el mecanismo. En una aplicación de producción convendría definir DTO o modelos de presentación inmutables, especialmente si el estado del dominio se comparte con otros componentes. También habría que adaptar el mecanismo de notificación al framework elegido y a su modelo de concurrencia.

### 4.4. Vista

La vista se suscribe a los cambios del ViewModel y presenta su estado.

```java
import java.beans.PropertyChangeEvent;
import java.beans.PropertyChangeListener;

public class TareaView implements PropertyChangeListener {

    private final TareaViewModel viewModel;

    public TareaView(TareaViewModel viewModel) {
        this.viewModel = viewModel;

        this.viewModel.agregarObservador(this);
    }

    public void mostrar() {
        System.out.println("=== LISTA DE TAREAS ===");

        if (viewModel.isCargando()) {
            System.out.println("Cargando tareas...");
        }

        for (Tarea tarea : viewModel.getTareas()) {
            System.out.println(
                tarea.getId()
                + " - "
                + tarea.getNombre()
                + " - "
                + (tarea.estaCompletada()
                    ? "Completada"
                    : "Pendiente")
            );
        }

        if (!viewModel.getMensaje().isBlank()) {
            System.out.println(
                "Mensaje: " + viewModel.getMensaje()
            );
        }
    }

    public void pulsarCompletar(int id) {
        viewModel.completarTarea(id);
    }

    @Override
    public void propertyChange(PropertyChangeEvent evento) {
        System.out.println(
            "Cambio detectado en: "
            + evento.getPropertyName()
        );

        mostrar();
    }

    public void cerrar() {
        viewModel.quitarObservador(this);
    }
}
```

Esta vista simula una interfaz gráfica mediante la consola.

En una aplicación real, `pulsarCompletar` podría corresponder al comando asociado a un botón y `mostrar` podría sustituirse por enlaces de datos declarativos.

### 4.5. Cliente: clase `Main`

```java
public class Main {

    public static void main(String[] args) {

        TareaService tareaService =
            new TareaService();

        TareaViewModel viewModel =
            new TareaViewModel(tareaService);

        TareaView vista =
            new TareaView(viewModel);

        viewModel.cargarTareas();

        System.out.println("----------------");

        vista.pulsarCompletar(1);

        vista.cerrar();
    }
}
```

### 4.6. Flujo de ejecución

```text
Usuario
   |
   | Pulsa completar
   v
Vista
   |
   | Invoca completarTarea(1)
   v
ViewModel
   |
   | Solicita completar la tarea
   v
TareaService
   |
   | Actualiza la entidad
   v
Modelo
   |
   | Devuelve el resultado
   v
ViewModel
   |
   | Notifica cambios de estado
   v
Vista
   |
   | Presenta la información actualizada
   v
Usuario
```

### 4.7. ¿Qué hemos conseguido?

- La vista no ejecuta directamente las reglas de negocio.
- El modelo no depende de componentes gráficos.
- El ViewModel centraliza el estado y las operaciones de presentación.
- La lógica del ViewModel puede probarse sin crear una ventana real.
- La vista puede sustituirse por otra implementación que utilice el mismo ViewModel, siempre que se mantenga el contrato necesario.

**Nota técnica:** este ejemplo es una demostración educativa. `PropertyChangeSupport` notifica cambios, pero no implementa por sí solo un sistema completo de *data binding*. Además, la lista contiene referencias a objetos `Tarea` mutables; para aplicaciones con actualizaciones reactivas más complejas, es preferible exponer modelos de presentación inmutables y notificar explícitamente los cambios de estado.

---

## 5. MVVM y el Data Binding

El *data binding* es uno de los mecanismos más característicos de muchas implementaciones MVVM.

Permite vincular propiedades de la vista con propiedades del ViewModel para que los cambios se reflejen de forma automática o mediante un mecanismo gestionado por el framework.

### Ejemplo conceptual

Supongamos que el ViewModel expone estas propiedades:

```java
public class UsuarioViewModel {

    private String nombre = "";
    private boolean guardando;

    public String getNombre() {
        return nombre;
    }

    public boolean isGuardando() {
        return guardando;
    }

    public void cambiarNombre(String nombre) {
        this.nombre = nombre;
    }

    public void guardar() {
        // Ejecutar operación de guardado
    }
}
```

Una vista podría mostrar el nombre y permitir modificarlo mediante un campo de texto.

Conceptualmente:

```text
Campo de texto
      |
      | Enlace con nombre
      v
UsuarioViewModel
      |
      | Estado actualizado
      v
Etiqueta / Campo de texto
```

El mecanismo exacto de enlace depende de la tecnología utilizada. El código anterior, por sí solo, no implementa notificaciones automáticas; para ello habría que añadir observabilidad o utilizar el sistema de propiedades del framework.

### Tipos de enlace

**One-way binding**

Los datos fluyen desde el ViewModel hacia la vista.

Ejemplo: una etiqueta muestra el nombre del usuario.

**Two-way binding**

Los cambios pueden fluir en ambas direcciones.

Ejemplo: el usuario escribe en un campo de texto y el ViewModel recibe el nuevo valor; cuando el ViewModel cambia el valor, el campo también se actualiza.

**Command binding**

La vista vincula una acción, como pulsar un botón, con una operación expuesta por el ViewModel.

Ejemplo: el botón Guardar ejecuta el comando `guardar`.

No todos los frameworks implementan estos mecanismos de la misma forma, por lo que conviene consultar la tecnología concreta utilizada.

---

## 6. Variantes y tecnologías que utilizan MVVM

### 6.1. MVVM con JavaFX

JavaFX dispone de propiedades observables y mecanismos de enlace que permiten construir interfaces siguiendo una separación similar a MVVM.

Por ejemplo, un ViewModel puede exponer una `StringProperty` y una vista puede enlazar una etiqueta a dicha propiedad.

Esto facilita que la interfaz reaccione cuando cambia el estado del ViewModel.

### 6.2. MVVM en Android

En Android, MVVM se utiliza habitualmente junto con componentes de arquitectura como `ViewModel`, `LiveData`, `StateFlow` o bibliotecas de observación.

El ViewModel conserva y expone el estado de la interfaz, mientras que la vista observa los cambios.

Es importante no confundir el patrón arquitectónico MVVM con la clase `ViewModel` concreta de Android: la clase del framework proporciona infraestructura, mientras que MVVM es el enfoque de organización del código.

### 6.3. MVVM en aplicaciones web

En aplicaciones web, un enfoque similar puede construirse con frameworks que gestionan el estado reactivo de la interfaz.

El ViewModel puede representarse mediante un objeto de estado y operaciones que la vista consume.

La implementación concreta dependerá del framework. No todos los frameworks web denominan ViewModel a ese componente, aunque utilicen ideas similares.

### 6.4. MVVM y programación reactiva

Los sistemas reactivos permiten representar cambios de estado como flujos de datos o señales observables.

El ViewModel puede exponer estos flujos para que la vista reaccione cuando se producen cambios.

Este enfoque resulta útil cuando existen operaciones asíncronas, como consultas de red, carga de datos o búsquedas.

---

## 7. Ventajas y desventajas

### Ventajas

1. **Separación de responsabilidades:** la vista se centra en presentar información y el ViewModel en gestionar el estado de presentación.
2. **Testabilidad:** el ViewModel puede probarse sin necesidad de construir controles gráficos.
3. **Menor acoplamiento:** la lógica de presentación no necesita manipular directamente los componentes visuales.
4. **Gestión del estado:** permite centralizar propiedades como datos, errores, indicadores de carga y mensajes.
5. **Reactividad:** los mecanismos de observación y enlace de datos facilitan actualizar la interfaz cuando cambia el estado.
6. **Reutilización:** un ViewModel puede ser consumido por distintas vistas compatibles.
7. **Compatibilidad con operaciones asíncronas:** facilita representar estados como cargando, correcto o fallido.
8. **Mantenibilidad:** permite cambiar parte de la presentación sin tener que modificar necesariamente la lógica del ViewModel.

### Desventajas

1. **Mayor complejidad inicial:** puede introducir más clases y mecanismos de observación.
2. **Curva de aprendizaje:** el enlace de datos y la programación reactiva requieren comprender conceptos adicionales.
3. **ViewModels demasiado grandes:** pueden acumular lógica de presentación, coordinación y estado de demasiadas pantallas.
4. **Gestión del ciclo de vida:** las suscripciones y los observadores deben liberarse cuando corresponda.
5. **Depuración más compleja:** los cambios de estado pueden propagarse automáticamente y dificultar la localización de algunos errores.
6. **Dependencia de la tecnología:** determinadas características de MVVM dependen del framework elegido.
7. **Duplicación de estado:** si se mantienen copias innecesarias de los mismos datos, pueden surgir inconsistencias.
8. **Notificaciones incorrectas:** si los cambios no se notifican adecuadamente, la vista puede mostrar información desactualizada.

MVVM suele resultar especialmente útil en interfaces con un estado considerable, enlaces de datos y operaciones asíncronas. Para una pantalla muy sencilla, una solución más directa puede ser suficiente.

---

## 8. Relación con los principios SOLID

### Single Responsibility Principle (SRP)

MVVM facilita separar las responsabilidades:

- El modelo representa datos y reglas del dominio.
- La vista presenta información y recoge interacciones.
- El ViewModel mantiene y prepara el estado de presentación.

Un ViewModel que gestione demasiadas pantallas o concentre reglas complejas del dominio debería dividirse o delegar operaciones en servicios de aplicación.

### Open/Closed Principle (OCP)

Podemos modificar o sustituir la vista sin cambiar necesariamente el ViewModel, siempre que ambas mantengan el contrato necesario.

También podemos incorporar nuevas vistas que reutilicen el mismo estado y las operaciones existentes.

No obstante, añadir nuevos requisitos puede exigir ampliar el ViewModel o introducir nuevos componentes.

### Liskov Substitution Principle (LSP)

Cuando se definen abstracciones para servicios o dependencias del ViewModel, las distintas implementaciones deben respetar sus contratos.

Esto permite sustituir un servicio real por una implementación de prueba sin cambiar el comportamiento esperado del ViewModel.

### Interface Segregation Principle (ISP)

Es conveniente que el ViewModel dependa de interfaces específicas para las operaciones que necesita.

Por ejemplo, una pantalla de consulta puede utilizar una interfaz de lectura sin depender de todas las operaciones de modificación de una entidad.

### Dependency Inversion Principle (DIP)

El ViewModel debería recibir sus dependencias mediante el constructor u otro mecanismo de inyección, en lugar de crear directamente todos los servicios que necesita.

Ejemplo:

```java
public class UsuarioViewModel {

    private final UsuarioService usuarioService;

    public UsuarioViewModel(
            UsuarioService usuarioService) {

        this.usuarioService = usuarioService;
    }
}
```

Esta estructura facilita sustituir el servicio por una implementación simulada durante las pruebas.

**Importante:** MVVM no garantiza automáticamente el cumplimiento de SOLID. El resultado depende de cómo se diseñen los componentes, sus responsabilidades y sus dependencias.

---

## 9. Diferencias con otros patrones y arquitecturas

### MVVM vs. MVC

MVC significa *Model-View-Controller*.

- **MVC:** el controlador recibe las interacciones y coordina las operaciones.
- **MVVM:** el ViewModel expone el estado y las operaciones que necesita la vista.

En MVVM, la vista suele conectarse al ViewModel mediante *data binding*, propiedades observables o mecanismos equivalentes.

En MVC, el controlador suele desempeñar un papel más explícito en la gestión de las peticiones y la selección de las respuestas.

Ambos patrones buscan separar responsabilidades, pero organizan la interacción con la presentación de forma diferente.

### MVVM vs. MVP

MVP significa *Model-View-Presenter*.

- **MVP:** el Presenter suele comunicarse con una interfaz de vista explícita y coordinar directamente su actualización.
- **MVVM:** el ViewModel expone estado y operaciones que la vista consume, normalmente mediante observación o enlace de datos.

MVP puede encajar bien cuando se necesita controlar explícitamente la comunicación entre presentador y vista.

MVVM suele resultar especialmente cómodo cuando la tecnología permite enlazar propiedades y observar cambios de estado.

### MVVM vs. Clean Architecture

- **MVVM:** organiza la presentación y el estado de la interfaz.
- **Clean Architecture:** organiza las dependencias y las responsabilidades de la aplicación en torno al dominio y los casos de uso.

Pueden combinarse.

Por ejemplo, el ViewModel puede invocar un caso de uso definido en la capa de aplicación, mientras que la lógica de negocio permanece independiente de la interfaz.

### MVVM vs. arquitectura en capas

La arquitectura en capas organiza el sistema en niveles, como presentación, aplicación, dominio e infraestructura.

MVVM organiza los componentes de presentación.

Por tanto, una aplicación puede utilizar MVVM en la capa de presentación y mantener debajo una arquitectura en capas para los servicios, el dominio y la persistencia.

### MVVM vs. Observer

Observer es un patrón de comportamiento que permite notificar a varios observadores cuando cambia el estado de un objeto.

MVVM puede utilizar Observer, propiedades observables o flujos reactivos para mantener actualizada la vista.

Sin embargo, Observer por sí solo no define la separación entre Modelo, Vista y ViewModel.

---

## 10. Casos de uso habituales

### Aplicaciones móviles

- Formularios de registro.
- Pantallas de inicio de sesión.
- Listas de elementos.
- Pantallas de detalle.
- Gestión de estados de carga y error.
- Interfaces con actualizaciones asíncronas.

### Aplicaciones de escritorio

- Herramientas de administración.
- Editores de documentos.
- Aplicaciones de gestión.
- Paneles de configuración.
- Interfaces basadas en JavaFX u otras tecnologías con propiedades observables.

### Aplicaciones web

- Interfaces con estado reactivo.
- Formularios dinámicos.
- Paneles de control.
- Interfaces con filtros y búsquedas.
- Aplicaciones de una sola página cuando la estructura elegida sigue un enfoque similar a MVVM.

### Sistemas empresariales

- Gestión de clientes.
- Inventarios.
- Facturación.
- Gestión de pedidos.
- Paneles de operaciones.
- Aplicaciones con múltiples estados de presentación.

---

## 11. MVVM en arquitecturas modernas

MVVM se ocupa principalmente de la presentación. Puede integrarse con arquitecturas más amplias.

### 11.1. MVVM con arquitectura en capas

```text
+------------------------+
|          Vista         |
+------------------------+
             |
             v
+------------------------+
|       ViewModel        |
|------------------------|
| Estado de presentación |
| Acciones               |
+------------------------+
             |
             v
+------------------------+
| Servicio / Caso de uso |
+------------------------+
             |
             v
+------------------------+
|         Dominio        |
+------------------------+
             |
             v
+------------------------+
|    Infraestructura     |
| Repositorios / BD      |
+------------------------+
```

El ViewModel no necesita acceder directamente a la base de datos. Normalmente delega las operaciones en servicios o casos de uso.

Esta separación ayuda a evitar que la presentación dependa de detalles de persistencia.

### 11.2. MVVM y operaciones asíncronas

Una pantalla puede necesitar consultar un servicio remoto.

El ViewModel puede exponer un estado con tres posibilidades:

```text
       Inicio
          |
          v
       Cargando
       /      \
      v        v
   Éxito      Error
```

La vista representa cada estado:

- **Cargando:** muestra un indicador.
- **Éxito:** presenta los datos.
- **Error:** muestra un mensaje y, si procede, una opción para reintentar.

En aplicaciones reales, las operaciones asíncronas deben tener en cuenta la cancelación, la concurrencia, el ciclo de vida de la pantalla y la posibilidad de que una respuesta antigua llegue después de una petición más reciente.

### 11.3. MVVM y gestión del ciclo de vida

En aplicaciones móviles y de escritorio, los ViewModels pueden sobrevivir a determinados cambios de configuración o mantenerse mientras una pantalla conserva su estado.

Sin embargo, su ciclo de vida depende de la tecnología utilizada.

Es importante evitar que un ViewModel mantenga referencias fuertes a ventanas o vistas que deberían poder liberarse, y cancelar las tareas que ya no resulten necesarias.

---

## 12. Buenas prácticas

1. **Mantén la vista ligera:** evita incluir reglas de negocio en los controladores de eventos.
2. **Evita ViewModels gigantes:** divide la responsabilidad por pantalla, flujo o caso de uso cuando sea necesario.
3. **Expón estado claro:** utiliza propiedades que representen datos, errores, carga y resultados.
4. **Utiliza modelos de presentación:** transforma las entidades del dominio cuando la vista necesite una representación diferente.
5. **Inyecta las dependencias:** facilita las pruebas y reduce el acoplamiento.
6. **Gestiona las suscripciones:** elimina observadores y libera recursos cuando corresponda.
7. **Controla las operaciones asíncronas:** contempla errores, cancelación y resultados obsoletos.
8. **Evita duplicar estado sin necesidad:** define qué componente es responsable de cada dato.
9. **Mantén el dominio independiente:** no introduzcas controles gráficos ni dependencias de la interfaz en las entidades de negocio.
10. **No expongas objetos mutables sin control:** utiliza propiedades observables, colecciones adecuadas o modelos inmutables.
11. **Prueba los cambios de estado:** verifica tanto los resultados correctos como los errores.
12. **Adapta el patrón al framework:** aprovecha sus mecanismos de observación y enlace de datos sin perder la separación de responsabilidades.

---

## 13. Pruebas recomendadas

Una aplicación MVVM puede probarse por componentes.

### Pruebas del modelo

Verifican las reglas de negocio.

Por ejemplo:

- Una tarea se completa correctamente.
- Una entrada inválida se rechaza.
- Una entidad inexistente produce el error esperado.

### Pruebas del ViewModel

Son especialmente importantes porque el ViewModel contiene parte de la lógica de presentación.

Se puede comprobar que:

- Al cargar los datos, expone la lista esperada.
- Al completar una tarea, invoca el servicio correspondiente.
- Actualiza los mensajes de estado.
- Representa correctamente los estados de carga y error.
- Notifica los cambios relevantes.
- No modifica la vista directamente.

### Ejemplo de prueba del ViewModel

Con JUnit y Mockito, una prueba simplificada podría verificar que la operación se delega al servicio.

```java
import static org.mockito.Mockito.verify;

import org.junit.jupiter.api.Test;
import org.mockito.Mockito;

class TareaViewModelTest {

    @Test
    void completarTareaInvocaElServicio() {

        TareaService servicio =
            Mockito.mock(TareaService.class);

        TareaViewModel viewModel =
            new TareaViewModel(servicio);

        viewModel.completarTarea(1);

        verify(servicio).completarTarea(1);
    }
}
```

Esta prueba es ilustrativa y presupone que las dependencias de prueba están configuradas. La implementación actual del ViewModel también intenta recargar las tareas después de completar una operación, por lo que conviene configurar el mock y añadir aserciones sobre el estado resultante para realizar una prueba más completa.

### Pruebas de la vista

Comprueban que los enlaces y los componentes visuales muestran el estado correctamente.

En una aplicación con *data binding*, se puede verificar que un cambio en el ViewModel actualiza el control correspondiente.

### Pruebas de integración

Verifican que la vista, el ViewModel y los servicios trabajan correctamente en conjunto.

Estas pruebas son especialmente útiles para detectar problemas de enlace de datos, ciclo de vida y operaciones asíncronas.

---

## 14. Cuándo utilizar MVVM y cuándo evitarlo

### Utiliza MVVM cuando:

- La interfaz tiene un estado considerable.
- Necesitas separar la lógica de presentación de los controles visuales.
- El framework ofrece mecanismos de observación o enlace de datos.
- Existen operaciones asíncronas y varios estados de pantalla.
- Quieres probar la lógica de presentación de forma independiente.
- La aplicación tiene varias pantallas o flujos de interacción.

### Considera una alternativa cuando:

- La interfaz es muy sencilla.
- No existe un estado de presentación significativo.
- El framework no facilita la separación y el patrón introduce una complejidad innecesaria.
- La lógica de presentación puede resolverse de forma más clara con otro patrón.
- El ViewModel termina duplicando innecesariamente los datos y comportamientos del modelo.

MVVM no obliga a implementar toda la aplicación en un único ViewModel. Es habitual tener un ViewModel por pantalla o flujo de presentación y delegar la lógica de negocio en servicios o casos de uso.

---

## 15. Conclusión

El patrón arquitectónico **MVVM (Model-View-ViewModel)** permite separar los datos y las reglas de negocio de la presentación mediante un componente intermedio que expone el estado y las operaciones necesarias para la interfaz.

El modelo representa el dominio, la vista presenta la información y el ViewModel prepara y mantiene el estado de presentación.

Sus principales ventajas son la testabilidad, el desacoplamiento de los componentes visuales y la facilidad para gestionar interfaces reactivas mediante mecanismos de observación o *data binding*.

MVVM resulta especialmente útil en aplicaciones móviles, de escritorio y web donde la interfaz tiene múltiples estados o interacciones. También puede combinarse con arquitecturas en capas y Clean Architecture para mantener el dominio independiente de la tecnología de presentación.

La idea fundamental es: **separar la interfaz visual del estado y de la lógica de presentación mediante un ViewModel que expone datos y operaciones sin depender directamente de los controles gráficos.**

---

## 16. Referencias

- Microsoft Learn — Patrón Model-View-ViewModel: https://learn.microsoft.com/en-us/dotnet/architecture/maui/mvvm
- Android Developers — ViewModel: https://developer.android.com/topic/libraries/architecture/viewmodel
- JavaFX Documentation: https://openjfx.io/
- Oracle Java Documentation: https://docs.oracle.com/en/java/
- Martin Fowler — GUI Architectures: https://martinfowler.com/eaaDev/uiArchs.html
