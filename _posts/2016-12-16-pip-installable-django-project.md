---
published: true
layout: post
title: Pip installable Django project
---

I'll present you a [Django][1] layout which allow to ship your project (not app) as a [Python][5] package. Your users will be able to install it with [pip][2] for example.

This template is available as a [Cookiecutter][3] template. But I'll remove every references, because this paper is mainly written to present you a Django project layout. Maybe I'll do another post about Cookiecutter later.

My first need is to be able to install my Django project via `pip install my-awesome-app` and access it via `my-awesome-app` entry point
without loosing `manage.py` flexibility.
The first reference for this template is [Sentry][4] project. I'm really appreciative of how this project is nicely built by nice people.

Let's describe how it works!

## Layout

This is a little description of my typical layout for every new Django project I build.

{% highlight bash linenos %}
$ tree .
.
├── conftest.py
├── Makefile
├── MANIFEST.in
├── README.rst
├── setup.cfg
├── setup.py
├── src
│   └── my_awesome_project
│       ├── __about__.py
│       ├── apps
│       │   ├── app_one
│       │   └── app_two
│       ├── core
│       │   ├── apps.py
│       │   ├── views.py
│       │   └── [...]
│       ├── runner
│       │   ├── commands
│       │   │   ├── django.py
│       │   │   └── help.py
│       │   └── decorators.py
│       ├── settings
│       │   ├── base.py
│       │   └── local.dist.py
│       ├── urls.py
│       └── wsgi.py
└── tests
    └── core
        └── test_core.py
{% endhighlight %}

In order to have a [pip][2] installable project we need to have a `setup.py` like every [Python][5] package.
You'll notive that we don't need `requirements.txt` at all. Hooray! All your dependencies will be organized in `setup.py`.
No more `requirements-[dev|test].txt` files, [setuptools][6] `extras_require` option will do the trick.

You'll probably need a `core` app (*line 15*) and many other apps (*line 12*). Another special directory is `runner` where your
command line entry points will be stored.

Django community have various ways to organize settings, feel free to use your own but I prefer to have a `base.py` (*line 25*) settings file and another `local.py` which will be imported in `settings/__init__.py`.

It's always a good thing to give users `local.dist.py` (*line 26*) or `production.dist.py` example files, as long as Django settings number always tend to grow.
Always try to put a little description below every settings that can be complex. But don't try to describe all them all if it seems easy to understand.
This kind of documentation can be difficult to keep up-to-date.

{% highlight python %}
LOGIN_URL = 'https://localhost:8000/login'
# Set these to drop to an unprivileged user after
# binding the SMTP daemon (Default: False)
DROP_PRIVILEGES_USER = False
# Product name appear in email templates and optouts pages
PRODUCT_NAME = 'My Awesome App'
{% endhighlight %}

## Install process

A developper install process will be something like that:

* Cloning repository
* Build [virtualenv][7]
* `pip install -e .[dev,test]`
* Coding.

A production deployment for a [PyPI][8] published project will be:

* `pip install my-awesome-project`
* Run command line: `my-awesome-project --help`

Let's take a quick look at `setup.py`:


{% highlight python %}
[...]
	entry_points={
        'console_scripts': [
            'my-awesome-project = my_awesome_project.runner:main']
    },
    extras_require={
        'dev': [
            'bumpversion==0.5.3',
            'docker-compose==1.9.0'
        ],
        'tests': [
            'pytest==3.0.5',
            'flake8==3.2.1',
            'libfaketime==0.4.2',
            'factory-boy==2.7.0',
            'fake-factory==0.7.2'
        ],
    },
[...]
{% endhighlight %}

This `extra_require` option is pretty useful. You can have a `production`
one which contains for example [Gunicorn][9] or [Psycopg][10], etc...

## Where is my `manage.py`?

First of all, don't panic! You still have `django-admin` command line tool which is very similar to `manage.py`.

Instead of `python manage.py shell`, you can do:

{% highlight bash %}
DJANGO_SETTINGS_MODULE=my_awesome_project.settings django-admin shell
{% endhighlight %}

Ok, it's very verbose. This is why this template give you another approach with `runner` which use [click][11] library.

Main goal is to be able to do something like: 

{% highlight bash %}
my-awesome-project django shell
{% endhighlight %}

Then every Django commands will be accessible. You can take a look at how it works [here][12].

This is a dead simple example of a custom command which return Django version.

{% highlight python %}
$ cat src/my_awesome_project/runner/commands/version.py
import click
import django


@click.command()
def version():
    "Show Django version." 
    click.echo('Django {}'.format(django.__version__))
{% endhighlight %}

Then you'll be able to do:

{% highlight bash %}
$ my-awesome-project version
Django 1.10.1
{% endhighlight %}

## End

I know every Django or project layouts can be personnal but here, I want 
to show you how to ship a Django project in a professional way.

You can retrieve this template [here][16].

Feel free to contact [me][13] for any questions, leave issue on [Github][12]. 

In further posts I'll speak about: "How to store package metadata", "How to trash your unmaintainable fixtures" and more tools like [bumpversion][14] and [docker-compose][15].

Another article about how to organize Python package metadata.
Another article about factory-boy and fake-factory.
Another article about docker-compose.
Another article about bumpversion.

[1]: https://www.djangoproject.com/
[2]: https://pip.pypa.io/en/stable/
[3]: https://cookiecutter.readthedocs.io/en/latest/
[4]: https://github.com/getsentry/sentry
[5]: https://www.python.org/
[6]: https://setuptools.readthedocs.io/en/latest/
[7]: https://virtualenv.pypa.io/en/stable/
[8]: https://pypi.org/
[9]: http://gunicorn.org/
[10]: http://initd.org/psycopg/docs/
[11]: http://click.pocoo.org/
[12]: https://github.com/toxinu/cookiecutter-django/blob/59daaaebe9c1828dfab1beb7f7c8a22c4376ce7b/%7B%7B%20cookiecutter.package_name%20%7D%7D/src/%7B%7B%20cookiecutter.module_name%20%7D%7D/runner/commands/django.py
[13]: mailto:contact@toxi.nu
[14]: https://github.com/peritus/bumpversion
[15]: https://www.docker.com/products/docker-compose
[16]: https://github.com/toxinu/cookiecutter-django/
