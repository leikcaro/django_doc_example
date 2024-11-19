# django_doc_example
Este es el ejemplo oficial desde la documentacion de Django 

# Los comandos principales que hemos usado son:

Crear el proyecto django tutorial con la app raiz mysite
__django-admin startproject mysite djangotutorial__

La estructura del proyecto y la app raiz mysite es:

djangotutorial/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py

Destacar settings.py como el archivo de configuracion y urls.py como ruteador principal de la aplicación.

Correr el servidor de desarrollo
__python manage.py runserver__

Crear la aplicacion polls:
__python manage.py startapp polls__

La estructura de archivos de polls es diferente de la de la app raiz

polls/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py

Por defecto se trabaja en SQLite, lo que no requiere cambios en la seccion DATABASES de mysite/settings.py, se puede cambiar a PostgreSQL si asi lo requiere el proyecto.

El siguiente comando permite crear las bases de datos de los modelos que vienen por defecto en el proyecto, no se ejecutan automáticamente por dos razones
1) Permite cambiar qué dejar por defecto, agregando o sacando apps o middlewares, entre otros.
2) Sustituir SQLite por otra base de datos.

__python manage.py migrate__ __crea las tablas requeridas en SQLite__ en este caso, ya que no se modifica la base de datos a otra.

Creación de modelos:

from django.db import models


class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField("date published")


class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)

Estos dos modelos se crean en models.py de la app polls, pero para que la app de __polls se active, debemos agregarla a INSTALLED_APPS de mysite/settings.py__

El siguiente comando "prepara" las migraciones necesarias de los modelos que creamos, identificando errores antes de crear las tablas u otros codigo SQL:

__python manage.py makemigrations polls__

Migrations for 'polls':
  polls/migrations/0001_initial.py
    + Create model Question
    + Create model Choice

Con el siguiente coóigo, podemos imprimir y revisar el códgo SQL que se escribirá para realizar una migración en particular, en este caso la 0001_initial:

__python manage.py sqlmigrate polls 0001__

Una vez revisado y aceptado, se procede  a migrar, es decir, se vinculan los modelos con la base de datos, se crean las tablas atributos, relaciones y todo lo necesario para el funcionamiento de los mismos.

Acceder a una shell para revisar los datos y ejecutar comandos directamente:

__python manage.py shell__

Dentro de la shell podemos importar librerías, modelos y ejecutar pruebas. Crear elementos

from polls.models import Choice, Question  # Importar las clases/modelos anteriormente agregadas

Question.objects.all() #Revisar los objetos creados a partir de questions

<QuerySet []> Lo anterior retorna vacío, ya que aun no se han credado

q.save(): comando que nos permitiría guardar, si queremos crear un objeto de la clase Question, por ejemplo.



