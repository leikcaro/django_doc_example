
# django_doc_example

Este repositorio contiene el ejemplo oficial de la documentación de Django (versión 5.1), siguiendo el tutorial hasta la sección de administración (Tutorial 2).

---

## Comandos principales utilizados

### Crear el proyecto Django y la app raíz `mysite`

```bash
django-admin startproject mysite djangotutorial
```

La estructura del proyecto y la app raíz `mysite` es la siguiente:

```
djangotutorial/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
```

- **`settings.py`**: Archivo de configuración principal.
- **`urls.py`**: Ruteador principal de la aplicación.

---

### Ejecutar el servidor de desarrollo

```bash
python manage.py runserver
```

---

### Crear la aplicación `polls`

```bash
python manage.py startapp polls
```

La estructura de la aplicación `polls` es:

```
polls/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py
```

---

## Base de datos

Por defecto, Django utiliza **SQLite**, por lo que no se requieren cambios en la sección `DATABASES` del archivo `mysite/settings.py`. Sin embargo, se puede cambiar a PostgreSQL u otra base de datos si el proyecto lo requiere.

Para inicializar las bases de datos de los modelos predeterminados, ejecuta:

```bash
python manage.py migrate
```

Este comando **crea las tablas requeridas en SQLite** (en este caso) y no se ejecuta automáticamente para permitir:

1. Configurar qué elementos dejar por defecto (apps, middlewares, etc.).
2. Sustituir SQLite por otra base de datos si es necesario.

---

## Creación de modelos

En `polls/models.py`, definimos los siguientes modelos:

```python
from django.db import models

class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField("date published")

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

Para activar la app `polls`, debemos agregarla a `INSTALLED_APPS` en `mysite/settings.py`:

```python
INSTALLED_APPS = [
    ...
    'polls',
]
```

---

### Preparar migraciones

El siguiente comando prepara las migraciones necesarias para los modelos creados, permitiéndonos identificar errores antes de aplicarlas:

```bash
python manage.py makemigrations polls
```

Salida esperada:

```
Migrations for 'polls':
  polls/migrations/0001_initial.py
    - Create model Question
    - Create model Choice
```

---

### Revisar el SQL generado por una migración

Para inspeccionar el código SQL que se generará para una migración específica (por ejemplo, `0001_initial`):

```bash
python manage.py sqlmigrate polls 0001
```

---

### Aplicar las migraciones

Una vez revisado el SQL, aplicamos las migraciones para vincular los modelos con la base de datos:

```bash
python manage.py migrate
```

Esto crea las tablas, atributos, relaciones y otros elementos necesarios.

---

## Interactuar con los modelos desde la consola

Podemos utilizar la shell interactiva para revisar y manipular los datos directamente:

```bash
python manage.py shell
```

Ejemplo de uso dentro de la shell:

```python
from polls.models import Choice, Question

# Verificar objetos creados
Question.objects.all()
# Resultado esperado (vacío inicialmente): <QuerySet []>

# Crear un nuevo objeto
```python
q = Question(question_text="¿Cuál es tu lenguaje de programación favorito?", pub_date=timezone.now())
q.save()
```
---
Se puede añadir rutas que permitan capturar elementos desde las urls para ser pasadas a traves de request a una funcion de views y asi poder interactuar entre el usuario y la BBDD, permitiendo hacer una respuesta específica para cualquier elemento de la BBDD.

```python
# ex: /polls/5/results/
    path("<int:question_id>/results/", views.results, name="results")
```


podemos añadir un fragmento de código a un archivo html para poder usar este archivo en vez de HttpResponse, usando generalmente la funcion render:

fragmento de codigo para recorrer con un ciclo for los elementos:
```python
{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
        <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}
```


en views de polls vamos a crear un renderizado usando la funcion render:

```python
from django.shortcuts import render

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by("-pub_date")[:5]
    context = {"latest_question_list": latest_question_list}
    return render(request, "polls/index.html", context)
```
La funcion render recibe el request, la ubicacion del html a renderizar y un diccionario que trae elementos como objetos de la BBDD u otros elementos que sirvan para complementar el HTML al HTML.
---

Este archivo se actualizará a medida que avancemos en el tutorial.
