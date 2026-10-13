# Pytest: testing y calidad del código en Python

## 1. Introducción

**Pytest** es un framework de testing para Python que permite crear, organizar y ejecutar pruebas automatizadas de manera sencilla y flexible.

Se utiliza para comprobar que las funciones, clases, módulos y componentes de una aplicación se comportan como se espera.

Pytest permite escribir pruebas utilizando funciones normales de Python, realizar aserciones con `assert`, preparar datos reutilizables mediante fixtures y ejecutar escenarios con diferentes entradas mediante pruebas parametrizadas.

También dispone de un ecosistema de plugins que permite ampliar sus funcionalidades e integrarlo con herramientas de cobertura, automatización y análisis de calidad.

### Objetivos principales

- Verificar el comportamiento del código.
- Detectar defectos de forma temprana.
- Prevenir regresiones durante la evolución del software.
- Comprobar reglas de negocio y casos límite.
- Facilitar la refactorización segura.
- Reutilizar la preparación de datos de prueba.
- Integrarse con herramientas como Coverage.py, pytest-cov, Ruff, mypy y SonarQube.
- Automatizar las pruebas dentro de pipelines CI/CD.

**Idea clave:** Pytest permite convertir los comportamientos esperados de una aplicación en comprobaciones automáticas, repetibles y fáciles de mantener.

Documentación oficial: https://docs.pytest.org/

---

## 2. ¿Qué problema resuelve Pytest?

Sin pruebas automatizadas, un desarrollador puede comprobar manualmente una funcionalidad ejecutando el programa y revisando los resultados.

Este enfoque presenta varios problemas:

- Consume tiempo.
- Es difícil repetir todos los escenarios.
- Puede dejar sin comprobar casos límite.
- Los cambios pueden introducir regresiones.
- Las comprobaciones manuales pueden ser inconsistentes.
- Resulta complicado verificar muchas combinaciones de entradas.

Pytest permite automatizar estas comprobaciones para ejecutarlas cada vez que cambia el código.

Por ejemplo, una función que calcula el precio final de un producto puede probarse con distintos escenarios:

- Precio sin descuento.
- Descuento del 10 %.
- Descuento del 50 %.
- Precio igual a cero.
- Descuento negativo.
- Porcentaje superior al permitido.
- Valores con decimales.

Una suite de pruebas puede ejecutar estos escenarios en segundos y señalar cuáles no producen el resultado esperado.

### Beneficios principales

1. **Rapidez:** ejecutar múltiples pruebas de forma automatizada.
2. **Repetibilidad:** comprobar el mismo comportamiento en diferentes ejecuciones.
3. **Detección temprana:** localizar defectos antes de desplegar.
4. **Mantenibilidad:** conservar pruebas junto al código.
5. **Integración:** utilizar los resultados como parte de un proceso CI/CD.

---

## 3. Instalación y configuración

Pytest se instala habitualmente mediante `pip`.

```bash
python -m pip install pytest
```

Para verificar la instalación:

```bash
pytest --version
```

Es recomendable trabajar dentro de un entorno virtual para aislar las dependencias del proyecto.

```bash
python -m venv .venv
```

En Linux o macOS:

```bash
source .venv/bin/activate
```

En Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

Después, se pueden instalar las herramientas de desarrollo que necesite el proyecto.

```bash
python -m pip install pytest pytest-cov ruff
```

Estas herramientas tienen responsabilidades diferentes:

| Herramienta | Función |
|---|---|
| Pytest | Ejecutar las pruebas |
| pytest-cov | Medir y presentar la cobertura de código |
| Coverage.py | Recopilar datos de cobertura; pytest-cov se apoya en esta herramienta |
| Ruff | Analizar y aplicar reglas de estilo y calidad estática |
| mypy | Comprobar tipos estáticos en Python |
| SonarQube | Analizar distintos aspectos de la calidad del código |
| Jenkins | Orquestar la ejecución dentro de CI/CD |

Es recomendable fijar y gestionar las versiones de las dependencias de desarrollo para mantener ejecuciones reproducibles.

---

## 4. Estructura recomendada del proyecto

Pytest descubre automáticamente las pruebas que siguen sus convenciones de nombres.

Una estructura habitual es:

```text
mi_proyecto/
├── src/
│   └── mi_app/
│       ├── __init__.py
│       ├── calculadora.py
│       └── servicios.py
├── tests/
│   ├── unit/
│   │   ├── test_calculadora.py
│   │   └── test_servicios.py
│   ├── integration/
│   │   └── test_repositorio.py
│   └── conftest.py
├── pyproject.toml
└── requirements-dev.txt
```

Esta estructura es orientativa. En proyectos pequeños puede bastar con un único directorio `tests/`.

### Convenciones de nombres

Pytest descubre normalmente archivos con nombres como:

- `test_modulo.py`
- `modulo_test.py`

Dentro de esos archivos, detecta funciones que comienzan por `test_` y métodos de prueba con nombres equivalentes en clases de prueba compatibles con sus convenciones.

Las clases de pruebas suelen comenzar por `Test` y no necesitan heredar de una clase base especial.

Ejemplo:

```python
def test_sumar_dos_numeros():
    assert 2 + 3 == 5
```

La prueba se puede ejecutar desde la raíz del proyecto:

```bash
pytest
```

---

## 5. Primera prueba con Pytest

Supongamos que tenemos una función para sumar dos números.

Archivo `src/mi_app/calculadora.py`:

```python
def sumar(a: int, b: int) -> int:
    return a + b
```

Archivo `tests/unit/test_calculadora.py`:

```python
from mi_app.calculadora import sumar


def test_sumar_dos_numeros():
    resultado = sumar(2, 3)

    assert resultado == 5
```

La prueba ejecuta la función y comprueba que el resultado sea `5`.

Si el resultado es distinto, Pytest muestra un error que incluye los valores comparados.

### ¿Qué ocurre durante la ejecución?

1. Pytest descubre el archivo de prueba.
2. Identifica la función cuyo nombre comienza por `test_`.
3. Ejecuta la función.
4. Evalúa la expresión `assert`.
5. Marca la prueba como satisfactoria o fallida.
6. Presenta un resumen de los resultados.

A diferencia de otros frameworks, Pytest permite utilizar las aserciones normales de Python. Cuando una aserción falla, realiza una introspección que ayuda a entender qué valores provocaron el error.

---

## 6. El patrón Arrange, Act, Assert

Una práctica útil consiste en estructurar las pruebas mediante el patrón AAA:

- **Arrange:** preparar los datos y las condiciones.
- **Act:** ejecutar la operación.
- **Assert:** comprobar el resultado.

Ejemplo:

```python
from mi_app.calculadora import sumar


def test_sumar_dos_numeros():
    # Arrange
    a = 10
    b = 20

    # Act
    resultado = sumar(a, b)

    # Assert
    assert resultado == 30
```

Esta organización ayuda a identificar rápidamente qué se está preparando, qué comportamiento se ejecuta y qué resultado se espera.

No es obligatorio incluir los tres comentarios en todas las pruebas. Si el test es suficientemente claro, la estructura puede expresarse directamente mediante el código.

---

## 7. Aserciones en Pytest

Las aserciones permiten comprobar los resultados de las operaciones.

### 7.1. Igualdad

```python
assert resultado == 10
```

### 7.2. Condiciones booleanas

```python
assert usuario.esta_activo()
assert not usuario.esta_bloqueado()
```

### 7.3. Comprobación de pertenencia

```python
assert "admin" in roles
```

### 7.4. Comprobación de valores nulos

En Python se utiliza `None`:

```python
assert resultado is None
assert usuario is not None
```

### 7.5. Comparación de colecciones

```python
assert resultado == ["A", "B", "C"]
```

### 7.6. Comparación de números decimales

Cuando se trabaja con cálculos de punto flotante, es posible que la representación binaria produzca pequeñas diferencias.

Para comparar resultados aproximados se puede utilizar `pytest.approx`:

```python
import pytest


def test_calculo_decimal():
    resultado = 0.1 + 0.2

    assert resultado == pytest.approx(0.3)
```

Para operaciones monetarias, conviene definir explícitamente la precisión y la política de redondeo. En muchos casos es preferible utilizar `Decimal` en lugar de `float`.

---

## 8. Pruebas de excepciones

También es necesario comprobar que una función rechaza las entradas inválidas de la forma prevista.

Pytest proporciona `pytest.raises` para verificar excepciones.

### Ejemplo de código de producción

```python
def dividir(dividendo: float, divisor: float) -> float:
    if divisor == 0:
        raise ValueError("El divisor no puede ser cero")

    return dividendo / divisor
```

### Prueba

```python
import pytest

from mi_app.calculadora import dividir


def test_dividir_debe_rechazar_divisor_cero():
    with pytest.raises(
        ValueError,
        match="El divisor no puede ser cero",
    ):
        dividir(10, 0)
```

La prueba verifica que se lanza una excepción `ValueError` y que el mensaje coincide con el patrón indicado.

### Buenas prácticas

- Comprobar el tipo de excepción esperado.
- Verificar el mensaje cuando forme parte del contrato.
- Evitar capturar excepciones demasiado generales.
- No envolver en `pytest.raises` más código del necesario.

De lo contrario, una excepción producida accidentalmente en otra operación podría hacer que la prueba pasara por un motivo incorrecto.

---

## 9. Pruebas parametrizadas

La parametrización permite ejecutar una misma prueba con múltiples conjuntos de datos.

En Pytest se utiliza `@pytest.mark.parametrize`.

### Ejemplo

```python
import pytest

from mi_app.validaciones import es_mayor_de_edad


@pytest.mark.parametrize(
    "edad, esperado",
    [
        (17, False),
        (18, True),
        (19, True),
        (0, False),
    ],
)
def test_es_mayor_de_edad(edad, esperado):
    assert es_mayor_de_edad(edad) is esperado
```

Para este ejemplo, la función podría ser:

```python
def es_mayor_de_edad(edad: int) -> bool:
    return edad >= 18
```

Pytest ejecuta la prueba una vez por cada conjunto de datos.

### Ventajas

- Reduce la duplicación.
- Facilita añadir escenarios.
- Mejora la claridad de las reglas verificadas.
- Permite comprobar límites y valores inválidos.
- Ayuda a mantener una suite compacta.

### Cuándo utilizarla

La parametrización es especialmente útil cuando se quiere verificar el mismo comportamiento con diferentes entradas y resultados esperados.

No conviene agrupar en una misma prueba parametrizada escenarios que tienen objetivos muy distintos y necesitan preparaciones o comprobaciones completamente diferentes.

---

## 10. Fixtures: preparación reutilizable de pruebas

Las *fixtures* son una de las funcionalidades más importantes de Pytest.

Permiten preparar datos, crear objetos, iniciar dependencias y gestionar recursos que necesitan las pruebas.

Se definen mediante `@pytest.fixture` y se solicitan como argumentos de las funciones de prueba.

### Ejemplo básico

```python
import pytest


@pytest.fixture
def usuario():
    return {
        "nombre": "Ana",
        "email": "ana@example.com",
        "activo": True,
    }


def test_usuario_activo(usuario):
    assert usuario["activo"] is True
    assert usuario["nombre"] == "Ana"
```

Pytest detecta que la prueba necesita la fixture `usuario`, la ejecuta y pasa el resultado a la función.

### Ventajas

- Evitan repetir la preparación de datos.
- Separan la configuración de las comprobaciones.
- Permiten reutilizar recursos.
- Facilitan la limpieza después de cada prueba.
- Mejoran la legibilidad.

### Fixtures con `yield`

Cuando una prueba necesita preparar un recurso y liberarlo después, se puede utilizar `yield`.

```python
import pytest


@pytest.fixture
def recurso_temporal():
    recurso = crear_recurso_temporal()

    yield recurso

    eliminar_recurso_temporal(recurso)
```

En este ejemplo, `crear_recurso_temporal` y `eliminar_recurso_temporal` representan funciones del proyecto que deben existir.

El código anterior a `yield` prepara el recurso; el posterior se ejecuta durante la limpieza cuando Pytest finaliza el uso de la fixture.

### Alcance de las fixtures

Una fixture puede utilizar diferentes ámbitos de ejecución:

- `function`: una instancia por función de prueba; es el ámbito predeterminado.
- `class`: una instancia por clase de pruebas.
- `module`: una instancia por módulo.
- `package`: una instancia por paquete de pruebas.
- `session`: una instancia durante la sesión de ejecución.

Ejemplo:

```python
@pytest.fixture(scope="session")
def configuracion_compartida():
    return cargar_configuracion()
```

Las fixtures compartidas pueden mejorar el rendimiento, pero deben diseñarse con cuidado para no introducir estado mutable compartido ni dependencias entre pruebas.

---

## 11. El archivo `conftest.py`

Pytest utiliza `conftest.py` para definir fixtures y otras configuraciones compartidas dentro de un árbol de pruebas.

Por ejemplo:

```text
tests/
├── conftest.py
├── unit/
│   ├── test_calculadora.py
│   └── test_validaciones.py
└── integration/
    └── test_repositorio.py
```

El archivo `tests/conftest.py` podría contener:

```python
import pytest


@pytest.fixture
def usuario_de_prueba():
    return {
        "id": 1,
        "nombre": "Ana",
        "activo": True,
    }
```

Las pruebas ubicadas en los directorios correspondientes pueden utilizar esta fixture sin importarla explícitamente.

### Buenas prácticas

- Colocar en `conftest.py` las fixtures que realmente se reutilizan.
- Mantener las fixtures específicas cerca de los tests que las necesitan.
- Evitar que el archivo se convierta en un lugar donde se acumule toda la lógica de preparación.
- Documentar las fixtures que gestionan recursos complejos.
- Mantener el estado compartido bajo control.

---

## 12. Mocking y aislamiento de dependencias

Las pruebas unitarias suelen centrarse en una unidad de código. Para evitar que dependan de servicios externos, pueden utilizar dobles de prueba.

Python incluye `unittest.mock`, que se puede utilizar con Pytest.

### Ejemplo

Supongamos que tenemos un servicio que consulta el precio de un producto mediante un cliente externo.

```python
class ServicioPrecios:
    def __init__(self, cliente):
        self.cliente = cliente

    def obtener_precio(self, producto_id: str) -> float:
        return self.cliente.consultar_precio(producto_id)
```

Podemos comprobar el comportamiento sin realizar una llamada de red real:

```python
from unittest.mock import Mock

from mi_app.servicios import ServicioPrecios


def test_obtener_precio_consulta_al_cliente():
    cliente = Mock()
    cliente.consultar_precio.return_value = 19.99

    servicio = ServicioPrecios(cliente)

    resultado = servicio.obtener_precio("P-123")

    assert resultado == 19.99
    cliente.consultar_precio.assert_called_once_with("P-123")
```

### ¿Qué se comprueba?

- Que el servicio devuelve el precio esperado.
- Que utiliza el identificador correcto al consultar la dependencia.
- Que la prueba no necesita acceder a un servicio real.

### Uso de `monkeypatch`

Pytest incluye la fixture `monkeypatch`, que permite sustituir atributos, variables de entorno o comportamientos durante una prueba y restaurarlos automáticamente al finalizar.

Es útil para aislar dependencias, aunque conviene utilizarla con criterio y evitar modificar demasiados elementos globales.

### Buenas prácticas

- Simular dependencias externas cuando el objetivo es comprobar la lógica local.
- Evitar simular la misma función que se pretende probar.
- No verificar todas las llamadas internas sin una necesidad concreta.
- Mantener pruebas de integración que comprueben el comportamiento real entre componentes.

Demasiados mocks pueden producir una falsa sensación de confianza si las dependencias reales se comportan de manera diferente.

---

## 13. Pruebas de integración con Pytest

Las pruebas de integración comprueban que varios componentes funcionan correctamente cuando colaboran entre sí.

En Python pueden utilizarse para verificar:

- Acceso a bases de datos.
- Persistencia de información.
- Comunicación con APIs.
- Lectura y escritura de archivos.
- Integración entre servicios.
- Interacción con colas de mensajes.

### Ejemplo conceptual

Supongamos que un repositorio guarda y recupera usuarios.

Una prueba de integración puede:

1. Preparar una base de datos de pruebas.
2. Crear un usuario.
3. Consultar el usuario mediante el repositorio.
4. Verificar los datos recuperados.
5. Limpiar los datos creados.

A diferencia de una prueba unitaria, aquí interesa comprobar el comportamiento real de la integración con la base de datos.

### Buenas prácticas

- Utilizar bases de datos y servicios aislados.
- Evitar datos reales de producción.
- Preparar y limpiar los registros de forma reproducible.
- Controlar las transacciones y los efectos secundarios.
- No depender del orden de ejecución.
- Separar las pruebas de integración de las unitarias cuando tengan costes y requisitos distintos.

Pytest no obliga a utilizar una infraestructura determinada. La base de datos, el cliente HTTP o el sistema de mensajería se seleccionan según la arquitectura de la aplicación.

---

## 14. Pruebas E2E con Pytest

Pytest también puede utilizarse para organizar y ejecutar pruebas E2E.

En este nivel se verifica un flujo completo de la aplicación, por ejemplo:

1. Abrir una aplicación web.
2. Iniciar sesión.
3. Crear un pedido.
4. Comprobar el resultado.
5. Verificar el estado final.

Para automatizar navegadores se pueden utilizar herramientas como Playwright o Selenium, que se integran con Python.

### Ejemplo ilustrativo con Playwright para Python

```python
from playwright.sync_api import Page, expect


def test_usuario_puede_acceder(page: Page):
    page.goto("http://localhost:8000/login")

    page.get_by_label("Correo electrónico").fill(
        "usuario@example.com"
    )

    page.get_by_label("Contraseña").fill(
        "PasswordDePrueba123!"
    )

    page.get_by_role(
        "button",
        name="Iniciar sesión",
    ).click()

    expect(page).to_have_url(
        "http://localhost:8000/dashboard"
    )

    expect(
        page.get_by_role(
            "heading",
            name="Mi área personal",
        )
    ).to_be_visible()
```

El ejemplo presupone que la aplicación está disponible en la dirección indicada y que los campos y textos coinciden con su interfaz real.

Para utilizar este estilo de pruebas, hay que instalar y configurar Playwright para Python y sus navegadores.

Documentación oficial: https://playwright.dev/python/

### Consideraciones

- Las pruebas E2E son más costosas que las unitarias.
- Necesitan una aplicación y un entorno disponibles.
- Deben utilizar usuarios y datos de prueba controlados.
- Conviene guardar capturas y trazas para investigar fallos.
- Deben centrarse en flujos de negocio importantes.

No es necesario comprobar todas las reglas de negocio mediante el navegador. Las pruebas unitarias e integración suelen ser más adecuadas para muchos casos detallados.

---

## 15. Organización y ejecución selectiva de las pruebas

Pytest ofrece varias opciones para ejecutar subconjuntos de la suite.

### Ejecutar todas las pruebas

```bash
pytest
```

### Ejecutar un archivo

```bash
pytest tests/unit/test_calculadora.py
```

### Ejecutar una función concreta

```bash
pytest tests/unit/test_calculadora.py::test_sumar_dos_numeros
```

### Ejecutar con más información

```bash
pytest -v
```

### Mostrar la salida de las pruebas

Por defecto, Pytest captura parte de la salida estándar. Para mostrarla durante la ejecución:

```bash
pytest -s
```

### Detenerse en el primer fallo

```bash
pytest -x
```

### Ejecutar pruebas relacionadas con un nombre

```bash
pytest -k "usuario"
```

### Ejecutar por marcadores

```bash
pytest -m integration
```

Este último comando presupone que se han definido y aplicado marcadores de integración.

---

## 16. Marcadores para clasificar las pruebas

Los marcadores permiten clasificar pruebas y seleccionar grupos de ejecución.

Por ejemplo:

```python
import pytest


@pytest.mark.integration
def test_repositorio_guarda_usuario():
    ...
```

Se pueden utilizar marcadores como `unit`, `integration` o `e2e`, siempre que se configuren y documenten adecuadamente.

Ejemplo de configuración en `pyproject.toml`:

```toml
[tool.pytest.ini_options]
testpaths = ["tests"]
addopts = "-ra"
markers = [
    "unit: pruebas unitarias",
    "integration: pruebas de integración",
    "e2e: pruebas end-to-end",
]
```

Después se puede ejecutar:

```bash
pytest -m unit
```

O ejecutar las pruebas de integración:

```bash
pytest -m integration
```

### Beneficios

- Separar las pruebas rápidas de las más costosas.
- Ejecutar distintas categorías en CI/CD.
- Facilitar el mantenimiento de suites grandes.
- Adaptar la estrategia de testing al riesgo del cambio.

La clasificación debe ser coherente: un marcador no cambia la naturaleza de una prueba, sino que permite identificarla y seleccionarla.

---

## 17. Cobertura de código con pytest-cov

La cobertura mide qué parte del código se ejecuta durante las pruebas.

Pytest puede integrarse con Coverage.py mediante el plugin `pytest-cov`.

Instalación:

```bash
python -m pip install pytest-cov
```

### Ejecutar pruebas y medir cobertura

```bash
pytest --cov=mi_app
```

### Generar un informe detallado por terminal

```bash
pytest --cov=mi_app --cov-report=term-missing
```

### Generar un informe HTML

```bash
pytest --cov=mi_app --cov-report=html
```

El informe suele generarse en el directorio `htmlcov/`.

### Limitar la ejecución por cobertura

Se puede establecer un umbral mínimo:

```bash
pytest --cov=mi_app --cov-fail-under=80
```

Este comando hace fallar la ejecución si la cobertura total calculada queda por debajo del umbral configurado.

El valor del 80 % es solo un ejemplo, no una recomendación universal.

### Buenas prácticas

- Medir cobertura sobre el código de producción relevante.
- Identificar las ramas y reglas de negocio críticas sin cubrir.
- No añadir tests sin aserciones significativas solo para subir el porcentaje.
- Distinguir el código nuevo del heredado cuando sea útil.
- Revisar las exclusiones de cobertura.
- Interpretar el resultado junto con la calidad de las pruebas.

**Una cobertura elevada no garantiza que el comportamiento sea correcto.** Solo indica que una parte del código se ejecutó durante la suite medida.

---

## 18. Calidad estática: Ruff, mypy y SonarQube

Pytest se centra en ejecutar pruebas. La calidad del código puede reforzarse mediante otras herramientas complementarias.

### Ruff

Ruff permite aplicar reglas de estilo y detectar diversos problemas estáticos en Python.

Ejemplo:

```bash
ruff check .
```

Puede utilizarse para detectar problemas antes de ejecutar las pruebas.

### mypy

mypy realiza comprobaciones estáticas de tipos.

Ejemplo:

```bash
mypy src/
```

Su efectividad depende de las anotaciones, la configuración y el código analizado.

### SonarQube

SonarQube puede analizar código Python y presentar métricas y hallazgos de calidad según la configuración y las funcionalidades disponibles en la instalación.

También puede evaluar condiciones de calidad configuradas para el proyecto.

Para que los resultados de cobertura aparezcan en el análisis, hay que generar el informe compatible y configurar su importación en SonarQube.

### Diferencia entre herramientas

| Herramienta | Responsabilidad |
|---|---|
| Pytest | Ejecutar pruebas |
| pytest-cov | Recoger y presentar cobertura a través de Coverage.py |
| Ruff | Aplicar reglas de estilo y análisis estático |
| mypy | Comprobar tipos estáticos |
| SonarQube | Analizar diferentes dimensiones de calidad |
| Jenkins | Orquestar las validaciones y el pipeline |

La combinación permite comprobar tanto el comportamiento como determinados aspectos estructurales del código.

---

## 19. Integración con Jenkins y CI/CD

Pytest se puede ejecutar desde cualquier sistema CI/CD que disponga de Python y de las dependencias necesarias.

Con Jenkins, el pipeline puede:

1. Obtener el código.
2. Crear un entorno virtual.
3. Instalar las dependencias.
4. Ejecutar Ruff.
5. Ejecutar mypy, si está configurado.
6. Ejecutar las pruebas.
7. Generar cobertura.
8. Publicar informes.
9. Analizar el código con SonarQube.
10. Impedir la promoción si fallan las comprobaciones obligatorias.

### Ejemplo de Jenkinsfile

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Instalar dependencias') {
            steps {
                sh '''
                    python -m venv .venv
                    . .venv/bin/activate
                    python -m pip install --upgrade pip
                    python -m pip install -r requirements-dev.txt
                '''
            }
        }

        stage('Análisis estático') {
            steps {
                sh '''
                    . .venv/bin/activate
                    ruff check .
                '''
            }
        }

        stage('Testing y cobertura') {
            steps {
                sh '''
                    . .venv/bin/activate
                    pytest \
                      --junitxml=test-results.xml \
                      --cov=mi_app \
                      --cov-report=xml:coverage.xml \
                      --cov-report=term-missing
                '''
            }
        }
    }

    post {
        always {
            junit(
                testResults: 'test-results.xml',
                allowEmptyResults: false
            )

            archiveArtifacts(
                artifacts: 'coverage.xml',
                allowEmptyArchive: true
            )
        }
    }
}
```

Este ejemplo supone que:

- El agente ejecuta Linux y dispone de Python.
- Existe el archivo `requirements-dev.txt`.
- El paquete `mi_app` puede importarse correctamente.
- El proyecto tiene instalados Pytest, pytest-cov y Ruff.
- Jenkins dispone del plugin JUnit para publicar los resultados XML.

El informe `test-results.xml` permite a Jenkins presentar los resultados de las pruebas. El archivo `coverage.xml` contiene datos de cobertura que pueden utilizarse en herramientas compatibles, incluida SonarQube cuando está configurado para importarlos.

El ejemplo no integra todavía el análisis de SonarQube: esa etapa debe añadirse con la configuración, credenciales y parámetros propios de la instalación.

### Buenas prácticas en CI/CD

- Fijar versiones de dependencias.
- Ejecutar las pruebas en un entorno reproducible.
- Publicar informes incluso cuando falla una etapa.
- Separar pruebas rápidas y costosas cuando resulte útil.
- No almacenar credenciales en el código.
- Mantener los resultados accesibles para el equipo.
- Evitar que los reintentos oculten fallos reales.

---

## 20. Buenas prácticas para escribir pruebas mantenibles

### Una prueba, un comportamiento principal

Cada test debe tener un objetivo comprensible. No es necesario limitarlo a una sola aserción, pero sí evitar mezclar comportamientos independientes sin justificación.

### Nombres descriptivos

Preferir:

```python
def test_rechaza_un_precio_negativo():
    ...
```

en lugar de:

```python
def test_03():
    ...
```

### Independencia

Cada prueba debe preparar los datos que necesita y no depender de la ejecución previa de otras.

### Casos positivos y negativos

Comprobar tanto el comportamiento normal como las entradas inválidas y las situaciones excepcionales.

### Fixtures con responsabilidad clara

Reutilizar la preparación de datos cuando aporta claridad, evitando fixtures demasiado genéricas o difíciles de comprender.

### Evitar efectos secundarios

Las pruebas no deberían modificar servicios reales ni datos de producción.

### Comprobar contratos y resultados

Priorizar las salidas y comportamientos relevantes en lugar de verificar indiscriminadamente todos los detalles internos.

### Mantener una ejecución rápida

Las pruebas unitarias deberían ser rápidas siempre que sea posible. Las dependencias externas y los procesos costosos pueden trasladarse a suites de integración específicas.

---

## 21. Errores frecuentes al utilizar Pytest

### Escribir pruebas sin aserciones significativas

Una función puede ejecutarse sin verificar el resultado.

**Solución:** comprobar explícitamente el comportamiento esperado.

### Compartir estado entre pruebas

Una prueba puede alterar datos que necesita otra.

**Solución:** utilizar fixtures con un ciclo de vida adecuado y limpiar los recursos.

### Utilizar mocks en exceso

La suite puede comprobar un comportamiento simulado sin verificar la integración real.

**Solución:** combinar pruebas unitarias con pruebas de integración.

### Buscar cobertura elevada a cualquier precio

El porcentaje puede aumentar sin que las pruebas detecten más defectos.

**Solución:** priorizar reglas importantes, casos límite y aserciones útiles.

### Ignorar fallos en CI/CD

Si el pipeline continúa aunque fallen pruebas obligatorias, disminuye el valor de la automatización.

**Solución:** definir claramente qué fallos impiden integrar o desplegar.

### No gestionar dependencias

Una prueba puede pasar en un ordenador y fallar en otro por diferencias de paquetes o versiones.

**Solución:** utilizar entornos virtuales y dependencias versionadas.

### Crear fixtures demasiado complejas

La preparación de un test puede resultar más difícil de entender que la propia prueba.

**Solución:** mantener las fixtures pequeñas, explícitas y específicas cuando sea posible.

---

## 22. Estrategia recomendada para implantar Pytest

### Fase 1. Configurar el entorno

- Crear un entorno virtual.
- Instalar Pytest.
- Establecer la estructura de pruebas.
- Definir convenciones de nombres.

### Fase 2. Crear pruebas unitarias

- Identificar reglas de negocio.
- Añadir casos normales.
- Incorporar casos límite.
- Verificar excepciones.

### Fase 3. Reutilizar preparación de datos

- Crear fixtures.
- Gestionar los recursos.
- Evitar estado compartido innecesario.

### Fase 4. Añadir integración

- Probar repositorios y APIs.
- Utilizar servicios de prueba.
- Aislar datos y dependencias externas.

### Fase 5. Medir cobertura

- Instalar pytest-cov.
- Generar informes.
- Identificar zonas relevantes no cubiertas.
- Definir umbrales razonables.

### Fase 6. Incorporar análisis estático

- Añadir Ruff.
- Configurar mypy cuando aporte valor.
- Integrar SonarQube si el proyecto lo requiere.

### Fase 7. Automatizar en CI/CD

- Ejecutar las pruebas en Jenkins u otra plataforma.
- Publicar resultados y cobertura.
- Definir criterios de aceptación.
- Revisar la estabilidad de la suite.

### Fase 8. Mantener la suite

- Eliminar pruebas obsoletas.
- Corregir las inestables.
- Revisar escenarios críticos.
- Actualizar dependencias de forma controlada.

---

## 23. Checklist de calidad para Pytest

- Las pruebas están organizadas de forma coherente.
- Los nombres describen el comportamiento esperado.
- Cada prueba tiene un objetivo claro.
- Se comprueban resultados significativos.
- Se cubren escenarios positivos y negativos.
- Se contemplan casos límite relevantes.
- Las excepciones se verifican correctamente.
- Las fixtures tienen un ciclo de vida apropiado.
- No existen dependencias entre pruebas.
- Los mocks se utilizan con criterio.
- Las pruebas de integración utilizan entornos controlados.
- La cobertura se genera y se interpreta correctamente.
- Las herramientas de análisis estático están configuradas.
- Las pruebas se ejecutan en CI/CD.
- Los resultados quedan publicados.
- Las credenciales y los datos sensibles están protegidos.
- Los fallos críticos impiden avanzar cuando corresponde.

---

## 24. Conclusión

Pytest es una herramienta esencial para automatizar pruebas en Python. Su sintaxis sencilla y su ecosistema permiten construir suites de testing que crecen junto con el proyecto.

Sus funcionalidades principales incluyen:

- Pruebas automatizadas mediante funciones.
- Aserciones claras.
- Parametrización de escenarios.
- Fixtures reutilizables.
- Gestión de excepciones.
- Integración con mocks.
- Soporte para pruebas unitarias, de integración y E2E.
- Integración con herramientas de cobertura y CI/CD.

Para una estrategia completa de calidad del código, Pytest debe combinarse con herramientas complementarias:

- **pytest-cov y Coverage.py:** cobertura.
- **Ruff:** análisis estático y estilo.
- **mypy:** comprobación estática de tipos.
- **SonarQube:** análisis de calidad.
- **Jenkins:** automatización del pipeline.
- **Playwright o Selenium:** automatización de navegadores para pruebas E2E.

La recomendación es comenzar con pruebas unitarias pequeñas, independientes y significativas. A continuación, incorporar integración, cobertura y análisis estático, y finalmente automatizar el proceso en CI/CD.

**Una buena suite Pytest no se mide solo por la cantidad de tests o por su cobertura, sino por su capacidad para detectar defectos relevantes de forma fiable, reproducible y mantenible.**

---

## 25. Referencias oficiales

- Pytest: https://docs.pytest.org/
- Guía de uso de Pytest: https://docs.pytest.org/en/stable/how-to/index.html
- Fixtures: https://docs.pytest.org/en/stable/how-to/fixtures.html
- Parametrización: https://docs.pytest.org/en/stable/how-to/parametrize.html
- Pytest-cov: https://pytest-cov.readthedocs.io/
- Coverage.py: https://coverage.readthedocs.io/
- Ruff: https://docs.astral.sh/ruff/
- mypy: https://mypy.readthedocs.io/
- SonarQube: https://docs.sonarsource.com/sonarqube-server/
- Jenkins Pipeline: https://www.jenkins.io/doc/book/pipeline/
- Playwright para Python: https://playwright.dev/python/
