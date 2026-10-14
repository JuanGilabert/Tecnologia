# 📐 Arquitecturas del software

ESTE README DE NIVEL 2 CONTENDRA BREVES DESCRIPCIONES E IMAGENES.
ENLAZARA CON LOS README DE NIVEL 3 LOS CUALES DETALLARAN CON EXACTITUD LAS TECNOLOGIAS.

Los estilos arquitectónicos definen la estructura general de una aplicación, sus componentes, sus relaciones y la forma en que se despliega o comunica.

- [**Arquitectura monolítica**](monolitica/README.md): organiza la aplicación como una única unidad desplegable, con sus componentes integrados en un mismo sistema.
![Diagrama de la arquitectura monolítica](assets/monolitica-pattern_diagram.png)
- [**Microservicios**](microservicios/README.md): divide la aplicación en servicios pequeños e independientes, con responsabilidades específicas y comunicación mediante interfaces bien definidas.
![Diagrama de la arquitectura de microservicios](assets/microservices-pattern_diagram.png)
- [**Arquitectura hexagonal**](hexagonal/README.md): aísla la lógica de negocio de las tecnologías externas mediante puertos y adaptadores.
![Diagrama de la arquitectura hexagonal](assets/hexagonal-pattern_diagram.png)
- [**Clean Architecture**](clean-architecture/README.md): organiza el sistema en capas de responsabilidad y establece que las dependencias del código deben dirigirse hacia las reglas de negocio.
![Diagrama de la arquitectura Clean Architecture](assets/clean-architecture-pattern_diagram.png)
- [**Arquitectura dirigida por eventos**](event-driven/README.md): estructura la comunicación entre componentes alrededor de la publicación, emisión y consumo de eventos.
![Diagrama de la arquitectura dirigida por eventos](assets/event-driven-pattern_diagram.png)
- [**Arquitectura en capas**](arquitectura-en-capas/README.md): divide el sistema en niveles con responsabilidades diferenciadas, como presentación, lógica de negocio y acceso a datos.
![Diagrama de la arquitectura en capas](assets/arquitectura-en-capas-pattern_diagram.png)


| Tema | Descripción |
|---|---|
| [Arquitectura monolítica](arquitecturas-software/monolitica/README.md) | Organización de una aplicación como una unidad desplegable. |
| [Microservicios](arquitectura/microservicios/README.md) | Descomposición del sistema en servicios independientes que colaboran entre sí. |
| [Arquitectura hexagonal](arquitectura/hexagonal/README.md) | Aislamiento del dominio mediante puertos y adaptadores. |
| [Clean Architecture](arquitectura/clean-architecture/README.md) | Organización del software mediante capas y dependencias orientadas hacia el núcleo de la aplicación. |
| [Event-Driven Architecture](arquitectura/event-driven/README.md) | Comunicación y coordinación mediante eventos. |
| [Arquitectura en capas](arquitectura/arquitectura-en-capas/README.md) | Separación del sistema en capas con responsabilidades diferenciadas. |
