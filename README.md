{% comment %}
# Django Project Package Template

This is a template for a [Django](https://www.djangoproject.com/) 2.x project.

The project has a layout so that it can be build as a [wheel](https://github.com/pypa/wheel), one type of [Python packages](https://pypi.org/help/#packages).

## Features

*   `setup()` is configured using a [`setup.cfg` file](https://setuptools.readthedocs.io/en/latest/setuptools.html#configuring-setup-using-setup-cfg-files).
*   The version is defined in `{{ project_name }}.__version__`.
*   Source code is located in a `src` directory to prevent side effects.
*   All apps use the `{{ project_name }}.apps` namespace.
*   All configuration modules use the `{{ project_name }}.conf` namespace.
*   `{{ project_name }}.conf.settings` uses [`pathlib`](https://docs.python.org/3.7/library/pathlib.html) instead of `os` and `os.path`.
*   Many settings can be defined using environment variables and parsed with [envparse](https://github.com/rconradharris/envparse).
*   All templates, static files and locales will be included in the wheel.
*   All code follows the [Black](https://github.com/ambv/black) code style.
*   All docstrings follow [PEP 257](https://www.python.org/dev/peps/pep-0257/) conventions.
*   [Django Debug Toolbar](https://github.com/jazzband/django-debug-toolbar), [IPython](https://ipython.org/) and [check-manifest](https://github.com/mgedmin/check-manifest) are already added to the development dependencies.
*   A Sublime Text project configuration is already included.

## Usage

Use the following [startproject](https://docs.djangoproject.com/en/stable/ref/django-admin/#django-admin-startproject) command to create a new project using this template:

```console
python3 -m django startproject --extension=cfg,gitignore,gitkeep,in,md,sublime-project \
    --template=https://github.com/keimlink/django-project-package-template/archive/master.zip \
    name [directory]
```

_Tip: If you want to create the project in your current working directory use `.` as directory argument._

## Testbed

A testbed to quickly test the project template can be found in the separate [django-project-package-template-testbed](https://github.com/keimlink/django-project-package-template-testbed) repository.

## License

Distributed under the [3-Clause BSD License](https://opensource.org/licenses/BSD-3-Clause).

Copyright 2018-2019 Markus Zapke-Gründemann

_All text below the horizontal line is the template for the new project's README._

---
{% endcomment %}# {{ project_name|title }}

Describe your project in one sentence.

## Quickstart

Install the project and the development dependencies into a [virtual environment](https://docs.python.org/3.7/tutorial/venv.html):

```console
python3 -m venv .venv
source .venv/bin/activate
python3 -m pip install --upgrade pip setuptools wheel
python3 -m pip install --editable .[dev]
./manage.py migrate
./manage.py runserver
```

## Starting a New App

First create a new directory in the `apps` directory:

```console
mkdir src/{{ project_name }}/apps/name
```

Then pass the path to the new directory to the [startapp](https://docs.djangoproject.com/en/{{ docs_version }}/ref/django-admin/#django-admin-startapp) command:

```console
./manage.py startapp name src/{{ project_name }}/apps/name
```

## Deployment

The following list describes only the absolute necessary steps to outline a deployment for a Django project wheel. For example a component to serve static files is missing - you could use [WhiteNoise](https://github.com/evansd/whitenoise/) to do this.

Also see [How to use Django with Gunicorn](https://docs.djangoproject.com/en/{{ docs_version }}/howto/deployment/wsgi/gunicorn/) and [Deployment Checklist](https://docs.djangoproject.com/en/{{ docs_version }}/howto/deployment/checklist/) for more information.

1.  Add your favorite WSGI HTTP server, e.g.  [Gunicorn](https://gunicorn.org/), to `install_requires` in `setup.cfg`.
2.  [Check](https://github.com/mgedmin/check-manifest) if all files are included in the package:
    ```console
    check-manifest
    ```
3.  [Build](https://packaging.python.org/tutorials/packaging-projects/#generating-distribution-archives) a [wheel](https://github.com/pypa/wheel) of the project.
    ```console
    ./setup.py bdist_wheel
    ```
4.  Copy the wheel file from the `dist` directory to the server to be deployed.
5.  Create a minimal configuration on the server using environment variables.
    ```bash
    export DJANGO_SETTINGS_MODULE={{ project_name }}.conf.settings
    export DJANGO_ALLOWED_HOSTS=www.example.com
    export DJANGO_DEBUG=False
    ```
6.  Install the wheel and [collect the static files](https://docs.djangoproject.com/en/{{ docs_version }}/ref/contrib/staticfiles/#django-admin-collectstatic):
    ```console
    python3 -m pip install --find-links=/path/to/wheel_dir {{ project_name }}
    django-project collectstatic --no-input
    ```
7.  Start Gunicorn like this:
    ```console
    gunicorn {{ project_name }}.wsgi
    ```
