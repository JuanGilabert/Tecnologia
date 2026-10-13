# Patrón de diseño estructural: Flyweight

## 1. Introducción

El patrón de diseño **Flyweight** es un patrón estructural cuyo objetivo es **reducir el consumo de memoria compartiendo los datos comunes entre múltiples objetos**, en lugar de almacenar repetidamente la misma información en cada instancia.

Este patrón resulta especialmente útil cuando una aplicación necesita crear una gran cantidad de objetos similares y muchos de ellos contienen datos que se repiten.

En lugar de que cada objeto almacene todos sus atributos de manera independiente, Flyweight separa los datos compartidos de los datos específicos de cada instancia.

### Ejemplo conceptual

Imaginemos un videojuego que representa un bosque con miles de árboles.

Cada árbol tiene información como:

- Tipo de árbol.
- Textura.
- Color.
- Modelo gráfico.

Además, cada árbol tiene información particular:

- Posición X.
- Posición Y.
- Altura o escala individual.

Si cada árbol almacena su propia copia del modelo, la textura y el resto de los datos comunes, el consumo de memoria puede crecer considerablemente.

Con Flyweight, los datos compartidos se almacenan una sola vez por tipo de árbol, mientras que cada árbol conserva sus propias coordenadas y otros atributos específicos.

<box>
  <grid columns={2} gap={3}>
    <grid-item>
      <box border radius="lg" padding={3} gap={2}>
        **Sin Flyweight**
        <AsyncImage query="single realistic oak tree isolated on plain light background 3d game asset" aspectRatio="4:3" width="100%" />
        <text size="sm">Cada instancia mantiene sus propios datos, incluidos los que podrían compartirse.</text>
      </box>
    </grid-item>
    <grid-item>
      <box border radius="lg" padding={3} gap={2}>
        **Con Flyweight**
        <AsyncImage query="multiple identical low poly oak trees 3d game assets grouped together" aspectRatio="4:3" width="100%" />
        <text size="sm">Muchas instancias reutilizan el mismo tipo de árbol y mantienen por separado sus posiciones.</text>
      </box>
    </grid-item>
  </grid>
</box>

La idea no consiste en eliminar los objetos individuales, sino en **evitar duplicar innecesariamente la información que tienen en común**.

---

## 2. Problema que resuelve

Flyweight resuelve el problema del elevado consumo de memoria cuando una aplicación crea un número muy grande de objetos similares que contienen datos repetidos.

Este problema aparece, por ejemplo, en:

- Videojuegos con miles de árboles, partículas o elementos del escenario.
- Editores de texto con muchos caracteres.
- Sistemas de mapas con numerosos iconos.
- Aplicaciones de dibujo con figuras repetidas.
- Interfaces gráficas con componentes similares.
- Sistemas que representan grandes cantidades de entidades con atributos comunes.

### Ejemplo sin Flyweight

Imaginemos una clase que representa un árbol dentro de un videojuego.

```java
public class Arbol {

    private final String tipo;
    private final String textura;
    private final String color;

    private final int posicionX;
    private final int posicionY;

    public Arbol(
            String tipo,
            String textura,
            String color,
            int posicionX,
            int posicionY) {

        this.tipo = tipo;
        this.textura = textura;
        this.color = color;
        this.posicionX = posicionX;
        this.posicionY = posicionY;
    }

    public void dibujar() {
        System.out.println(
            "Dibujando " + tipo
            + " en (" + posicionX
            + ", " + posicionY + ")"
        );
    }
}
```

Podemos crear miles de árboles:

```java
Arbol arbol1 = new Arbol(
    "Roble", "roble.png", "Verde", 10, 20
);

Arbol arbol2 = new Arbol(
    "Roble", "roble.png", "Verde", 30, 40
);

Arbol arbol3 = new Arbol(
    "Roble", "roble.png", "Verde", 50, 60
);
```

Los tres árboles comparten el tipo, la textura y el color, pero cada instancia almacena sus propios atributos.

En un escenario con cientos de miles de árboles, la duplicación de datos y objetos auxiliares puede convertirse en un problema de rendimiento y memoria.

**Flyweight permite separar los atributos comunes de los atributos particulares de cada árbol.**

---

## 3. Solución propuesta

La solución consiste en dividir la información del objeto en dos categorías.

### Estado intrínseco

Es la información compartida que puede reutilizarse entre múltiples instancias.

Por ejemplo:

- Tipo de árbol.
- Textura.
- Modelo gráfico.
- Color predeterminado.
- Características comunes de una partícula.

Este estado forma parte del objeto Flyweight y, una vez creado, debería tratarse como inmutable para que compartirlo sea seguro.

### Estado extrínseco

Es la información particular de cada uso del objeto compartido.

Por ejemplo:

- Posición X e Y.
- Orientación.
- Escala individual.
- Nivel de transparencia específico.
- Datos de una operación concreta.

Este estado se mantiene fuera del objeto compartido y se proporciona cuando se necesita utilizarlo.

### Diagrama conceptual

```text
                 Cliente
                    |
                    v
             FlyweightFactory
                    |
                    v
             Objeto compartido
              (Roble / Roble)
                    |
           +--------+--------+
           |                 |
           v                 v
       Árbol 1            Árbol 2
       Estado             Estado
       externo            externo
       (10, 20)           (30, 40)
           |                 |
           +--------+--------+
                    |
                    v
           Datos intrínsecos
           compartidos
```

La fábrica se encarga de proporcionar objetos Flyweight reutilizables, normalmente identificados mediante una clave.

### Participantes del patrón

- **Flyweight:** define la interfaz de los objetos compartidos.
- **ConcreteFlyweight:** implementa los datos intrínsecos que pueden reutilizarse.
- **FlyweightFactory:** administra los objetos compartidos y evita crearlos repetidamente.
- **Client:** mantiene o proporciona el estado extrínseco y utiliza los objetos compartidos.

### Flujo de funcionamiento

1. El cliente solicita un objeto Flyweight para una determinada clave.
2. La fábrica comprueba si ya existe un objeto con esa clave.
3. Si existe, devuelve la instancia compartida.
4. Si no existe, crea una nueva instancia y la almacena.
5. El cliente utiliza el objeto compartido proporcionando los datos particulares necesarios.

---

## 4. Implementación completa en Java: bosque de árboles

Vamos a desarrollar un ejemplo en el que un videojuego representa miles de árboles.

Los datos comunes de cada tipo de árbol se compartirán, mientras que cada árbol conservará su propia posición.

### 4.1. Interfaz Flyweight

Definimos una interfaz que permite dibujar un tipo de árbol utilizando las coordenadas específicas de cada instancia.

```java
public interface TipoArbol {

    void dibujar(int posicionX, int posicionY);
}
```

El método recibe las coordenadas como argumentos porque estas pertenecen al estado extrínseco y no deben almacenarse en el objeto compartido.

### 4.2. ConcreteFlyweight

La clase `TipoArbolConcreto` almacena los datos intrínsecos.

```java
public final class TipoArbolConcreto
        implements TipoArbol {

    private final String nombre;
    private final String textura;
    private final String color;

    public TipoArbolConcreto(
            String nombre,
            String textura,
            String color) {

        this.nombre = nombre;
        this.textura = textura;
        this.color = color;
    }

    @Override
    public void dibujar(
            int posicionX,
            int posicionY) {

        System.out.println(
            "Dibujando " + nombre
            + " con textura " + textura
            + " y color " + color
            + " en (" + posicionX
            + ", " + posicionY + ")"
        );
    }
}
```

Los atributos son `final` para evitar que cambien después de construir el objeto. Esto favorece la reutilización segura de la instancia.

### 4.3. FlyweightFactory

La fábrica almacena y reutiliza los objetos según su tipo.

```java
import java.util.HashMap;
import java.util.Map;

public class ArbolFactory {

    private final Map<String, TipoArbol> tipos =
        new HashMap<>();

    public TipoArbol obtenerTipoArbol(
            String nombre,
            String textura,
            String color) {

        String clave = nombre;

        TipoArbol tipoArbol = tipos.get(clave);

        if (tipoArbol == null) {
            tipoArbol = new TipoArbolConcreto(
                nombre,
                textura,
                color
            );

            tipos.put(clave, tipoArbol);

            System.out.println(
                "Creando tipo de árbol: " + nombre
            );
        }

        return tipoArbol;
    }

    public int obtenerNumeroTipos() {
        return tipos.size();
    }
}
```

En este ejemplo, la clave es el nombre del árbol. Esto es correcto únicamente si cada nombre identifica de forma inequívoca una combinación de textura, color y demás atributos intrínsecos.

En una aplicación real, la clave debería representar todos los atributos que determinan la identidad del objeto compartido. Si dos robles tienen texturas o colores diferentes, no deben reutilizar la misma instancia por el simple hecho de llamarse igual.

### 4.4. Contexto de cada árbol

Cada árbol concreto del bosque mantiene sus propias coordenadas y una referencia al tipo compartido.

```java
public class Arbol {

    private final int posicionX;
    private final int posicionY;
    private final TipoArbol tipoArbol;

    public Arbol(
            int posicionX,
            int posicionY,
            TipoArbol tipoArbol) {

        this.posicionX = posicionX;
        this.posicionY = posicionY;
        this.tipoArbol = tipoArbol;
    }

    public void dibujar() {
        tipoArbol.dibujar(
            posicionX,
            posicionY
        );
    }
}
```

La clase `Arbol` no duplica los datos de textura, color o nombre. Solo conserva el estado específico de cada árbol y la referencia al objeto compartido.

### 4.5. Cliente

Ahora podemos crear un bosque con numerosos árboles.

```java
import java.util.ArrayList;
import java.util.List;

public class Main {

    public static void main(String[] args) {

        ArbolFactory factory = new ArbolFactory();

        List<Arbol> bosque = new ArrayList<>();

        TipoArbol roble = factory.obtenerTipoArbol(
            "Roble",
            "roble.png",
            "Verde"
        );

        TipoArbol pino = factory.obtenerTipoArbol(
            "Pino",
            "pino.png",
            "Verde oscuro"
        );

        bosque.add(new Arbol(10, 20, roble));
        bosque.add(new Arbol(30, 40, roble));
        bosque.add(new Arbol(50, 60, roble));

        bosque.add(new Arbol(70, 80, pino));
        bosque.add(new Arbol(90, 100, pino));

        for (Arbol arbol : bosque) {
            arbol.dibujar();
        }

        System.out.println(
            "Número de árboles: " + bosque.size()
        );

        System.out.println(
            "Número de tipos compartidos: "
            + factory.obtenerNumeroTipos()
        );
    }
}
```

Salida aproximada:

```text
Creando tipo de árbol: Roble
Creando tipo de árbol: Pino
Dibujando Roble con textura roble.png y color Verde en (10, 20)
Dibujando Roble con textura roble.png y color Verde en (30, 40)
Dibujando Roble con textura roble.png y color Verde en (50, 60)
Dibujando Pino con textura pino.png y color Verde oscuro en (70, 80)
Dibujando Pino con textura pino.png y color Verde oscuro en (90, 100)
Número de árboles: 5
Número de tipos compartidos: 2
```

Aunque existen cinco árboles en el bosque, solo se crean dos objetos compartidos para representar sus tipos.

Los cinco árboles siguen siendo objetos independientes porque cada uno tiene su propia posición.

### 4.6. ¿Qué hemos conseguido?

Hemos separado los datos en dos grupos:

| Estado | Ejemplo | Dónde se almacena |
|---|---|---|
| Intrínseco | Textura, color, tipo | Objeto Flyweight compartido |
| Extrínseco | Posición X, posición Y | Objeto `Arbol` |
| Referencia compartida | Tipo de árbol | Objeto `Arbol` mantiene una referencia al Flyweight |

El patrón no elimina el coste de mantener cada árbol ni garantiza por sí mismo una reducción determinada de memoria. La mejora depende del número de objetos, del tamaño de los datos compartidos y del coste de las referencias y estructuras adicionales.

---

## 5. Estructura general del patrón

Una versión simplificada puede representarse así:

```java
import java.util.HashMap;
import java.util.Map;

// Flyweight
public interface Caracter {

    void dibujar(int posicion);
}

// ConcreteFlyweight
public final class CaracterConcreto
        implements Caracter {

    private final char simbolo;
    private final String fuente;

    public CaracterConcreto(
            char simbolo,
            String fuente) {

        this.simbolo = simbolo;
        this.fuente = fuente;
    }

    @Override
    public void dibujar(int posicion) {
        System.out.println(
            "Símbolo: " + simbolo
            + ", fuente: " + fuente
            + ", posición: " + posicion
        );
    }
}

// FlyweightFactory
public class CaracterFactory {

    private final Map<String, Caracter> caracteres =
        new HashMap<>();

    public Caracter obtener(
            char simbolo,
            String fuente) {

        String clave = simbolo + "|" + fuente;

        return caracteres.computeIfAbsent(
            clave,
            k -> new CaracterConcreto(
                simbolo,
                fuente
            )
        );
    }
}

// Cliente
public class Main {

    public static void main(String[] args) {

        CaracterFactory factory = new CaracterFactory();

        Caracter a1 = factory.obtener('A', "Arial");
        Caracter a2 = factory.obtener('A', "Arial");
        Caracter b = factory.obtener('B', "Arial");

        System.out.println(a1 == a2); // true
        System.out.println(a1 == b);  // false

        a1.dibujar(0);
        a2.dibujar(10);
        b.dibujar(20);
    }
}
```

Este ejemplo muestra la idea central: las dos solicitudes de `A` con fuente `Arial` devuelven el mismo objeto, mientras que `B` necesita otro.

La clave debe construirse sin ambigüedades. Para sistemas más complejos, es preferible definir una clase de clave con igualdad y `hashCode` correctos, en lugar de concatenar cadenas.

---

## 6. Flyweight intrínseco y extrínseco

Conviene distinguir entre los dos tipos de estado porque de ello depende que el patrón funcione correctamente.

### Estado intrínseco

Es estable, compartido y propio del objeto Flyweight.

Ejemplos:

- El símbolo de un carácter.
- La familia tipográfica.
- El modelo de una partícula.
- La textura de un árbol.
- La geometría de una figura reutilizable.

El estado intrínseco no debe depender de la posición concreta o del contexto de una invocación particular.

### Estado extrínseco

Es específico de cada uso.

Ejemplos:

- La posición de un carácter en una línea.
- La ubicación de un árbol en el mapa.
- La posición de una partícula en una animación.
- La orientación de una figura dentro de un dibujo.

El estado extrínseco se pasa a la operación o se mantiene en el objeto que representa cada uso.

### ¿Por qué es importante esta separación?

Si guardamos la posición de un árbol dentro del Flyweight compartido, todos los árboles que reutilicen esa instancia acabarían compartiendo la misma posición.

Eso sería un error de diseño.

Por tanto, **un objeto Flyweight no debería almacenar como estado mutable compartido los datos que cambian entre sus distintos usos**.

---

## 7. Ventajas y desventajas

### Ventajas

1. **Reducción del consumo de memoria:** evita almacenar repetidamente datos idénticos en muchas instancias.
2. **Reutilización de objetos:** una fábrica centraliza la creación y recuperación de instancias compartidas.
3. **Mejor escalabilidad en ciertos escenarios:** puede facilitar la representación de grandes cantidades de entidades.
4. **Separación del estado:** diferencia los atributos compartidos de los atributos particulares.
5. **Centralización de la creación:** la fábrica permite controlar cómo se identifican y reutilizan los objetos.
6. **Posible mejora del rendimiento:** reducir asignaciones y presión sobre el recolector de basura puede ser beneficioso en aplicaciones con muchos objetos.

### Desventajas

1. **Mayor complejidad:** separar estado intrínseco y extrínseco exige analizar cuidadosamente el modelo de datos.
2. **Más indirección:** para conocer el estado completo de una entidad puede ser necesario consultar varios objetos.
3. **Coste de gestión:** la fábrica y su mapa de objetos consumen memoria y requieren mantenimiento.
4. **Concurrencia:** una fábrica compartida puede necesitar mecanismos seguros para gestionar solicitudes simultáneas.
5. **Gestión del ciclo de vida:** si se almacenan indefinidamente todos los Flyweights, la propia fábrica puede convertirse en una fuente de consumo de memoria.
6. **Beneficio no garantizado:** en modelos pequeños o con pocos datos repetidos, el patrón puede aportar complejidad sin ahorrar recursos.
7. **Riesgo de estado mutable compartido:** modificar un objeto compartido puede afectar a todos sus consumidores.

Flyweight debe utilizarse cuando exista un volumen significativo de objetos y datos repetidos, preferiblemente después de identificar el problema mediante mediciones.

---

## 8. Relación con los principios SOLID

### Single Responsibility Principle (SRP)

Cada clase puede tener una responsabilidad diferenciada:

- `TipoArbolConcreto`: representa los atributos intrínsecos de un tipo de árbol.
- `Arbol`: mantiene la información particular de una instancia.
- `ArbolFactory`: administra los objetos compartidos.

Esta separación evita que una sola clase tenga que representar todos los datos, gestionar las posiciones y administrar simultáneamente la reutilización de instancias.

### Open/Closed Principle (OCP)

La estructura permite incorporar nuevos tipos de Flyweight sin cambiar necesariamente el cliente.

Por ejemplo, podemos añadir nuevos tipos de árboles o nuevas implementaciones de la interfaz `TipoArbol`.

Sin embargo, si añadimos nuevos atributos intrínsecos, puede ser necesario actualizar la clave de la fábrica y su política de creación.

### Liskov Substitution Principle (LSP)

Las distintas implementaciones de `TipoArbol` deben respetar el contrato definido por la interfaz.

El cliente debería poder utilizarlas mediante `TipoArbol` sin depender de detalles específicos de una implementación concreta.

### Interface Segregation Principle (ISP)

Las interfaces de Flyweight deberían ser pequeñas y contener las operaciones que realmente necesitan sus consumidores.

Una interfaz reducida facilita compartir las implementaciones y evita obligar a los clientes a depender de métodos que no utilizan.

### Dependency Inversion Principle (DIP)

El cliente puede depender de la abstracción `TipoArbol` en lugar de la implementación concreta `TipoArbolConcreto`.

Esto facilita sustituir implementaciones y probar el comportamiento de las clases que utilizan los objetos compartidos.

---

## 9. Diferencias con otros patrones de diseño

### Flyweight vs. Singleton

Ambos pueden implicar reutilización de instancias, pero tienen objetivos distintos.

- **Flyweight:** comparte múltiples objetos equivalentes según sus datos intrínsecos.
- **Singleton:** restringe una clase a una única instancia accesible mediante un mecanismo definido.

Una fábrica Flyweight puede administrar cientos de tipos de objetos diferentes. No es necesario que toda la fábrica sea un Singleton.

### Flyweight vs. Factory Method

- **Flyweight:** busca reutilizar objetos para reducir la duplicación de estado.
- **Factory Method:** delega la decisión de qué objeto concreto crear a un método de creación.

Flyweight suele necesitar una fábrica para gestionar las instancias compartidas, pero el objetivo principal de ambos patrones no es el mismo.

### Flyweight vs. Abstract Factory

- **Flyweight:** reutiliza objetos que comparten datos intrínsecos.
- **Abstract Factory:** crea familias de objetos relacionados mediante una interfaz común.

Pueden utilizarse juntos, aunque resuelven problemas diferentes.

### Flyweight vs. Prototype

- **Flyweight:** evita duplicar objetos compartiendo una misma instancia cuando es apropiado.
- **Prototype:** crea nuevos objetos a partir de la clonación de un prototipo.

Si necesitamos una instancia independiente con su propio estado mutable, Prototype puede ser más adecuado. Flyweight se centra en compartir los datos comunes.

### Flyweight vs. Composite

- **Flyweight:** reduce la duplicación de datos compartidos entre muchos objetos.
- **Composite:** representa jerarquías de objetos individuales y compuestos mediante una interfaz común.

En un editor gráfico, por ejemplo, Composite podría representar un grupo de figuras y Flyweight podría compartir los estilos o las texturas utilizados por esas figuras.

### Flyweight vs. Decorator

- **Flyweight:** comparte el estado intrínseco para ahorrar recursos.
- **Decorator:** añade responsabilidades o comportamientos a un objeto envolviéndolo con otra implementación compatible.

Un objeto compartido puede utilizarse junto con decorators en algunos diseños, pero hay que evitar que la envoltura introduzca estado mutable que debería ser específico de cada uso.

---

## 10. Casos de uso habituales

### Videojuegos y gráficos

- Árboles, rocas y vegetación.
- Partículas y elementos de escenarios.
- Modelos y texturas reutilizables.
- Elementos gráficos repetidos.

### Editores de texto

Un documento con millones de caracteres puede beneficiarse de compartir información como:

- Símbolo del carácter.
- Familia tipográfica.
- Estilo compartido.

La posición de cada carácter y la información específica del documento seguirían gestionándose por separado.

En implementaciones reales, las estructuras de texto y los sistemas de renderizado pueden utilizar optimizaciones más especializadas, pero el principio de reutilización es similar.

### Interfaces gráficas

- Iconos compartidos.
- Recursos gráficos.
- Estilos comunes.
- Elementos visuales reutilizables.

### Mapas y sistemas geográficos

- Símbolos de puntos de interés.
- Iconos de categorías.
- Representaciones comunes de entidades.
- Recursos visuales reutilizados en grandes cantidades.

### Aplicaciones de dibujo

- Estilos de líneas.
- Texturas.
- Materiales.
- Configuraciones visuales compartidas por múltiples figuras.

### Sistemas de procesamiento masivo

Puede ser útil cuando hay numerosas entidades que comparten configuraciones, descripciones o recursos costosos, siempre que la separación entre estado compartido y particular sea natural para el dominio.

---

## 11. Flyweight en arquitecturas modernas

Flyweight es un patrón de objetos y no una arquitectura completa. Puede aplicarse dentro de distintos estilos arquitectónicos.

### 11.1. En una aplicación por capas

```text
Presentación
     |
     v
Servicio de aplicación
     |
     v
FlyweightFactory
     |
     v
Objetos compartidos
     |
     v
Procesamiento o renderizado
```

Una fábrica puede centralizar la reutilización de estilos, configuraciones o recursos dentro de un módulo.

No es necesario que todas las aplicaciones tengan una fábrica global. El ámbito de reutilización debe definirse según el ciclo de vida de los recursos.

### 11.2. En un sistema de renderizado

```text
Escena
  |
  +--> Entidad 1 ----+
  |                  |
  +--> Entidad 2 ----+----> Modelo compartido
  |                  |
  +--> Entidad 3 ----+
```

Cada entidad conserva su posición, orientación y otros atributos particulares, mientras que todas pueden referenciar el mismo modelo o recurso gráfico.

### 11.3. En aplicaciones distribuidas

El patrón puede utilizarse dentro de cada proceso para reutilizar objetos inmutables.

Sin embargo, **compartir un objeto Flyweight en memoria no equivale a compartirlo automáticamente entre servidores**. Cada proceso tiene su propia memoria y necesitará sus propios mecanismos de distribución o almacenamiento si se desea reutilizar recursos entre máquinas.

---

## 12. Buenas prácticas

1. **Mide antes de aplicar el patrón:** comprueba que el número de objetos y la duplicación de datos justifican la optimización.
2. **Identifica correctamente el estado intrínseco:** comparte únicamente los datos que realmente sean comunes.
3. **Mantén el estado compartido inmutable:** reduce el riesgo de que una modificación afecte a múltiples consumidores.
4. **Diseña claves completas y estables:** todos los atributos relevantes para identificar un Flyweight deben formar parte de la clave.
5. **Separa el estado extrínseco:** mantenlo en el contexto o pásalo como argumento.
6. **Controla el crecimiento de la fábrica:** considera límites, expiración o estrategias de eliminación si pueden generarse muchas claves.
7. **Ten en cuenta la concurrencia:** usa estructuras concurrentes o sincronización cuando la fábrica se comparta entre varios hilos.
8. **Evita compartir objetos con estado mutable específico de una operación:** puede provocar errores difíciles de detectar.
9. **Utiliza tipos y claves bien definidos:** una clase de clave explícita suele ser más robusta que una cadena concatenada.
10. **Documenta la política de reutilización:** deja claro qué atributos determinan la identidad de un objeto compartido.

---

## 13. Pruebas recomendadas

Las pruebas deberían verificar tanto la reutilización como la separación del estado.

### Pruebas de reutilización

- Dos solicitudes con la misma clave devuelven la misma instancia cuando esa es la política definida.
- Dos solicitudes con claves diferentes producen instancias diferentes.
- La fábrica crea una instancia nueva solo cuando no existe una coincidente.

Ejemplo con JUnit:

```java
import static org.junit.jupiter.api.Assertions.*;

import org.junit.jupiter.api.Test;

class CaracterFactoryTest {

    @Test
    void reutilizaCaracteresConLaMismaClave() {

        CaracterFactory factory = new CaracterFactory();

        Caracter primero =
            factory.obtener('A', "Arial");

        Caracter segundo =
            factory.obtener('A', "Arial");

        assertSame(primero, segundo);
    }

    @Test
    void creaInstanciasParaClavesDiferentes() {

        CaracterFactory factory = new CaracterFactory();

        Caracter primero =
            factory.obtener('A', "Arial");

        Caracter segundo =
            factory.obtener('B', "Arial");

        assertNotSame(primero, segundo);
    }
}
```

### Pruebas del estado extrínseco

- Dos árboles que comparten un tipo pueden dibujarse en posiciones diferentes.
- La operación realizada para una entidad no altera la información de otra.
- Las modificaciones particulares no afectan al objeto compartido.

### Pruebas de concurrencia

Si la fábrica se utiliza desde múltiples hilos, conviene verificar que las solicitudes concurrentes no crean duplicados inesperados cuando la política exige una única instancia por clave.

La estrategia concreta dependerá de la implementación utilizada y de si se permite o no crear instancias duplicadas temporalmente.

---

## 14. Cuándo utilizar Flyweight y cuándo evitarlo

### Utiliza Flyweight cuando:

- Existen muchos objetos similares.
- Una parte importante de sus datos se repite.
- La memoria es un recurso relevante.
- El estado compartido puede separarse del estado individual.
- La reutilización no compromete el comportamiento de las entidades.

### Evítalo cuando:

- Existen pocos objetos.
- Los atributos son principalmente específicos de cada instancia.
- El coste de gestionar la fábrica supera el posible ahorro.
- El diseño se vuelve considerablemente más complejo sin una mejora medible.
- El estado compartido necesita mutar de manera independiente para cada consumidor.

La mejor decisión es comparar el consumo de memoria, el tiempo de creación y el coste de acceso antes y después de aplicar el patrón.

---

## 15. Conclusión

El patrón **Flyweight** permite reducir la duplicación de datos mediante la reutilización de objetos que contienen estado intrínseco compartido.

Su funcionamiento se basa en separar los atributos comunes de los atributos particulares de cada uso, normalmente con ayuda de una fábrica que administra las instancias reutilizables.

Es especialmente útil en aplicaciones con grandes cantidades de elementos similares, como videojuegos, editores de texto, sistemas gráficos y mapas.

Su aplicación requiere analizar cuidadosamente qué datos pueden compartirse, mantener bajo control el ciclo de vida de los objetos y evitar que el estado mutable de una instancia afecte accidentalmente a las demás.

La idea fundamental es: **compartir los datos comunes y mantener separado el estado específico de cada objeto para reducir el consumo de recursos sin perder la identidad de cada entidad.**

---

## 16. Referencias

- **Design Patterns: Elements of Reusable Object-Oriented Software**, de Erich Gamma, Richard Helm, Ralph Johnson y John Vlissides (Gang of Four).
- Refactoring.Guru — Flyweight: https://refactoring.guru/design-patterns/flyweight
- Documentación oficial de Java: https://docs.oracle.com/en/java/
- Java Collections Framework: https://docs.oracle.com/en/java/javase/
