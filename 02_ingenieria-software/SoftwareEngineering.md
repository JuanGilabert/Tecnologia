# 🏗️ Ingeniería del Software

Guía de referencia sobre **arquitectura, diseño, patrones de diseño, principios y buenas prácticas de desarrollo de software**.

Esta sección reúne conceptos, enfoques y soluciones utilizados para construir sistemas de software comprensibles, mantenibles, extensibles y adaptables a los cambios.

La documentación se organiza en cinco áreas principales: arquitectura del sistema, diseño de software, patrones de diseño, principios y buenas prácticas, y documentación y modelado.

---

## 📚 Índice de contenidos

- [📐 Arquitectura](#-arquitectura)
- [🧩 Diseño de software](#-diseño-de-software)
- [🧱 Patrones de diseño](#-patrones-de-diseño)
  - [Creacionales](#-patrones-creacionales)
  - [Estructurales](#-patrones-estructurales)
  - [De comportamiento](#-patrones-de-comportamiento)
- [✅ Principios y buenas prácticas](#-principios-y-buenas-prácticas)
- [📝 Documentación y modelado](#-documentación-y-modelado)
- [🧭 Cómo navegar por la documentación](#-cómo-navegar-por-la-documentación)
- [🎯 Objetivos](#-objetivos)

---

## 📐 Arquitectura

La arquitectura de software define la estructura fundamental de un sistema, sus componentes, sus relaciones y las decisiones técnicas que condicionan su evolución, despliegue y mantenimiento.

En esta sección se recogen diferentes estilos y enfoques arquitectónicos, cada uno con sus propias características, ventajas, limitaciones y contextos de aplicación.


| Tema | Descripción |
|---|---|
| [Arquitectura monolítica](arquitectura/monolitica/README.md) | Organización de una aplicación como una unidad desplegable. |
| [Microservicios](arquitectura/microservicios/README.md) | Descomposición del sistema en servicios independientes que colaboran entre sí. |
| [Arquitectura hexagonal](arquitectura/hexagonal/README.md) | Aislamiento del dominio mediante puertos y adaptadores. |
| [Clean Architecture](arquitectura/clean-architecture/README.md) | Organización del software mediante capas y dependencias orientadas hacia el núcleo de la aplicación. |
| [Event-Driven Architecture](arquitectura/event-driven/README.md) | Comunicación y coordinación mediante eventos. |
| [Arquitectura en capas](arquitectura/arquitectura-en-capas/README.md) | Separación del sistema en capas con responsabilidades diferenciadas. |

---

## 🧩 Diseño de software

El diseño de software se centra en cómo se organizan los componentes de una aplicación, cómo colaboran entre sí y cómo se distribuyen sus responsabilidades.

Los enfoques incluidos en esta sección ayudan a estructurar aplicaciones y separar la lógica de presentación, la lógica de negocio y otras responsabilidades.

| Tema | Descripción |
|---|---|
| [MVC](diseno-de-software/mvc/) | Modelo-Vista-Controlador: separación del modelo, la interfaz y el control de las interacciones. |
| [MVVM](diseno-de-software/mvvm/) | Model-View-ViewModel: separación de la interfaz y su estado o lógica de presentación. |
| [MVP](diseno-de-software/mvp/) | Model-View-Presenter: coordinación entre la vista y la lógica de presentación mediante un presentador. |
| [Separación de responsabilidades](diseno-de-software/separacion-de-responsabilidades/) | División del software en componentes con funciones y responsabilidades bien delimitadas. |

---

## 🧱 Patrones de diseño

Los patrones de diseño son soluciones generales y reutilizables para problemas recurrentes en el diseño de software. No son implementaciones cerradas, sino modelos que pueden adaptarse a las necesidades de cada proyecto.

Se clasifican según el tipo de problema que ayudan a resolver.

### 🏭 Patrones creacionales

Se centran en los mecanismos de creación de objetos, buscando reducir el acoplamiento y facilitar la construcción de instancias.

| Patrón | Descripción |
|---|---|
| [Factory](patrones-de-diseno/creacionales/factory/) | Encapsula o centraliza la lógica de creación de objetos. |
| [Builder](patrones-de-diseno/creacionales/builder/) | Permite construir objetos complejos paso a paso. |
| [Singleton](patrones-de-diseno/creacionales/singleton/) | Restringe una clase a una única instancia accesible mediante un punto común. |

### 🧩 Patrones estructurales

Describen cómo combinar clases y objetos para formar estructuras flexibles y simplificar sus relaciones.

| Patrón | Descripción |
|---|---|
| [Facade](patrones-de-diseno/estructurales/facade/) | Proporciona una interfaz simplificada para interactuar con un subsistema complejo. |

### 🔄 Patrones de comportamiento

Abordan la comunicación y la distribución de responsabilidades entre objetos, definiendo cómo colaboran para realizar determinadas operaciones.

[Consultar los patrones de comportamiento](patrones-de-diseno/comportamiento/).

---

## ✅ Principios y buenas prácticas

Los principios y las buenas prácticas orientan el desarrollo hacia un código legible, mantenible, reutilizable cuando corresponde y preparado para evolucionar sin introducir complejidad innecesaria.

| Tema | Descripción |
|---|---|
| [Clean Code](principios-y-buenas-practicas/clean-code/) | Prácticas para escribir código claro, expresivo y fácil de mantener. |
| [SOLID](principios-y-buenas-practicas/solid/) | Cinco principios de diseño orientado a objetos que favorecen la separación de responsabilidades y la flexibilidad. |
| [DRY](principios-y-buenas-practicas/dry/) | Evita duplicaciones innecesarias de conocimiento y lógica. |
| [KISS](principios-y-buenas-practicas/kiss/) | Favorece soluciones sencillas frente a complejidad innecesaria. |
| [YAGNI](principios-y-buenas-practicas/yagni/) | Evita implementar funcionalidades que todavía no son necesarias. |

Estos principios deben aplicarse con criterio y según el contexto: no constituyen reglas absolutas ni sustituyen el análisis de los requisitos del sistema.

---

## 📝 Documentación y modelado

La documentación y el modelado permiten representar sistemas, comunicar decisiones técnicas y comprender la estructura y el comportamiento de una solución desde diferentes niveles de abstracción.

| Tema | Descripción |
|---|---|
| [UML — Unified Modeling Language](documentacion-y-modelado/uml/) | Lenguaje de modelado visual para representar la estructura, el comportamiento y las interacciones de un sistema. |
| [Diagramas C4](documentacion-y-modelado/diagramas-c4/) | Modelo de representación arquitectónica mediante diagramas de contexto, contenedores, componentes y código. |

---

## 🧭 Cómo navegar por la documentación

Cada directorio contiene el espacio destinado a desarrollar su temática específica. Desde este índice puedes acceder directamente a los conceptos y recursos organizados por categoría.

La estructura sigue una separación conceptual:

- **Arquitectura:** cómo se estructura el sistema y cómo se relacionan sus partes.
- **Diseño de software:** cómo se organizan los componentes y sus responsabilidades.
- **Patrones de diseño:** qué soluciones reutilizables pueden aplicarse a problemas recurrentes.
- **Principios y buenas prácticas:** cómo mejorar la calidad y mantenibilidad del código.
- **Documentación y modelado:** cómo representar y comunicar la estructura del sistema.

Los distintos enfoques pueden complementarse entre sí. Por ejemplo, una aplicación puede utilizar microservicios como estilo arquitectónico, aplicar Clean Architecture internamente y recurrir a patrones de diseño para resolver problemas concretos.

---

## 🎯 Objetivos

- Comprender los fundamentos de la Ingeniería del Software.
- Identificar las diferencias entre arquitectura, diseño y patrones.
- Conocer los principales estilos arquitectónicos y sus compromisos.
- Aplicar principios y buenas prácticas de forma razonada.
- Mejorar la calidad, legibilidad y mantenibilidad del código.
- Documentar sistemas mediante técnicas de modelado.
- Disponer de una referencia técnica organizada para el aprendizaje y el desarrollo de proyectos.

---

## 🔗 Navegación del repositorio

- [Volver al índice principal de Tecnología](../README.md)
- [Consultar las demás áreas del repositorio](../)

---

*El objetivo de la Ingeniería del Software es construir soluciones que no solo funcionen, sino que también puedan comprenderse, mantenerse y evolucionar con el tiempo.*
