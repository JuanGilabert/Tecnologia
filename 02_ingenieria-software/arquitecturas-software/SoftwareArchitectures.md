# 📐 Arquitecturas del software

Los estilos arquitectónicos definen la estructura general de una aplicación, sus componentes, sus relaciones y la forma en que se despliega o comunica.

- [**Arquitectura monolítica**](monolitica/README.md): organiza la aplicación como una única unidad desplegable, con sus componentes integrados en un mismo sistema.
![Diagrama UML de la arquitectura monolítica](assets/monolitica-pattern_diagram.png)
- [**Microservicios**](microservicios/README.md): divide la aplicación en servicios pequeños e independientes, con responsabilidades específicas y comunicación mediante interfaces bien definidas.
![Diagrama UML de la arquitectura de microservicios](assets/microservices-pattern_diagram.png)
- [**Arquitectura hexagonal**](hexagonal/README.md): aísla la lógica de negocio de las tecnologías externas mediante puertos y adaptadores.
![Diagrama UML de la arquitectura hexagonal](assets/hexagonal-pattern_diagram.png)
- [**Clean Architecture**](clean-architecture/README.md): organiza el sistema en capas de responsabilidad y establece que las dependencias del código deben dirigirse hacia las reglas de negocio.
![Diagrama UML de la arquitectura Clean Architecture](assets/clean-architecture-pattern_diagram.png)
- [**Arquitectura dirigida por eventos**](event-driven/README.md): estructura la comunicación entre componentes alrededor de la publicación, emisión y consumo de eventos.
![Diagrama UML de la arquitectura dirigida por eventos](assets/event-driven-pattern_diagram.png)
- [**Arquitectura en capas**](arquitectura-en-capas/README.md): divide el sistema en niveles con responsabilidades diferenciadas, como presentación, lógica de negocio y acceso a datos.
![Diagrama UML de la arquitectura en capas](assets/arquitectura-en-capas-pattern_diagram.png)