# Django Quick-Start

## Resources

* [Official Documentations](https://docs.djangoproject.com/en/3.1/)
* [Django by Example YouTube playlist by Noah Rubin](https://www.youtube.com/playlist?list=PLAF3anQEEkzS-mjdX7s-D63bjLWRdhuFM)

## Loose-ends

* Setting up `mod_wsgi` on the Apache installation as opposed to just using `mod_wsgi-standalone` and the express tool ([docs](https://docs.djangoproject.com/en/3.1/topics/install/#install-apache-and-mod-wsgi)).
* Setting up a proper database with the required python bindings as opposed to just using SQLite ([docs](https://docs.djangoproject.com/en/3.1/topics/install/#get-your-database-running)).
* Intricacies around the database API ([Database API ref](https://docs.djangoproject.com/en/3.1/topics/db/queries/), [`QuerySet` ref](https://docs.djangoproject.com/en/3.1/ref/models/querysets/), [Lookup API ref](https://docs.djangoproject.com/en/3.1/ref/models/lookups/), etc.).
  * Mainly just Django's decisions on the translations from python models to tables/records and from query language to python db API.

## Installation

### Django

Treat `django` as just another python library, e.g.
```shell
# After something like source venv/bin/activate
pip install django

# Or as part of requirements.txt from an existing repo
pip install -r requirements.txt
```

Includes a lightweight web server for testing purposes.  
Otherwise, we also need Apache + `mod_wsgi` for deployment in an actual web server.

Verify with:
```python
import django

print(django.get_version())
```

### Apache

See for example: [DigitalOcean's tutorial on how to install apache on Debian 10](https://www.digitalocean.com/community/tutorials/how-to-install-the-apache-web-server-on-debian-10)

E.g. not just `apt-get install apache2`, but also how to setup firewalls, basic workflows involved in working with an Apache process, etc.

```shell
# Apache itself
apt-get install apache2

# Allowing traffics through the firewall, e.g.
ufw allow WWW
# Check with
ufw status

# Check running apache service
systemctl status apache2
# or check the landing page on the server's IP (e.g. hostname -I)
```

#### Working with the Apache Process

```shell
systemctl [verb] apache2
# Verbs:
 - status
 - start
 - stop
 - restart
 - reload (for configuration changes without dropping connections)
 - enable (start up at boot) (default)
 - disable
```

See [section 5](https://www.digitalocean.com/community/tutorials/how-to-install-the-apache-web-server-on-debian-10#step-5-%E2%80%94-setting-up-virtual-hosts-(recommended)) and [6](https://www.digitalocean.com/community/tutorials/how-to-install-the-apache-web-server-on-debian-10#step-6-%E2%80%93-getting-familiar-with-important-apache-files-and-directories) for virtual hosts and important Apache files and directories respectively.

### `mod_wsgi`

See [documentations](https://modwsgi.readthedocs.io/en/develop/) for details.

Install into Python/virtual environment:
```shell
pip install mod_wsgi
# or
pip install mod_wsgi-standalone (long)
```

The standalone have the benefit of being separated from the system's Apache installation (suitable for docker-like settings, etc.).

For errors with `bdist_wheel`, try `pip install wheel`.

Verify with:
```shell
mod_wsgi-express start-server
# Visit localhost:8000
```

See the `mod_wsgi` docs and [django's guide](https://docs.djangoproject.com/en/3.1/howto/deployment/wsgi/modwsgi/) if not using the stand-alone with the express tool.

## Django Project

Initialise a Django project with:
```shell
django-admin startproject [project-name]
```

Verify with the development server:
```shell
# In project directory:
python manage.py runserver
# Check localhost:8000 to see the rocket
```

### Basic Structure

```
project-name/          Just a container, can be renamed
    manage.py          For administrative tasks
    project-name/      Python package corresponding to the project
        __init__.py
        settings.py
        urls.py        "Table of content", maps URLs to django views (python functions)
        asgi.py        Entry points for web servers to serve the project
        wsgi.py        See above
```

## Django App

* Is a unit of application (e.g. blog system, database of some records, polling app).
* Many-to-many relationship with django projects.
* Can be anywhere in python path, or in some project container as another top-level module, or in a submodule of existing project, etc.

Initialise with:
```shell
python manage.py startapp [app-name]
```

## Models and Databases

* Django model represents database layout with additional metadata.
* Model is meant to be the single, definitive source of truth about the data.
* SQL commands specific to the choice of backends are generated from the models (migrations).
* Models also provide API to the corresponding database in python (e.g. CRUD) ([docs](https://docs.djangoproject.com/en/3.1/topics/db/queries/)).

### Migrations

```
models --makemigrations--> migrations --migrate--> database tables
```

* Default apps don't require `makemigrations` since there are already migrations for them.
* New models/changes need to be `makemigrations`-ed first before they can be migrated.
* Inspect the SQL commands (dependent on the backends specified in the `settings.py`) with:
  ```
  python manage.py sqlmigrate app-name migration-#
  ```
  
* Migrations allow database/scheme changes over time without having to delete or make new tables.

#### Workflow for Changing Models

1. Make the changes in `models.py`.
2. Create a new migrations for those changes (`makemigrations`).
3. Apply the migration to the database (`migrate`).

Migrations (artifacts from step 2 above) can be put into version control and ship with the app.

### Database API

Available from:
```shell
python manage.py shell
```

* Perform CRUD in python.
* Define `__str__` method since the representation will be used throughout the ecosystem (e.g. interactive shell, admin interface).
* Any custom methods will also be available in the API (e.g. don't have to come up with the API equivalent of the desired query on the fly).

## Django Admin

Register admin user with:
```shell
python manage.py createsuperuser
```

* Admin interface is provided by default (see `admin/` routing in the project's `urls.py`).
* Models can be registered to the interface to automatically get access to administrative tasks for free:
  ```python
  # Inside app/admin.py
  from .models import ModelName
  
  admin.site.register(ModelName)
  ```
* Essentially CRUD in the actual website (subset of what's available in python through the database API).
