---
published: true
layout: post
title: Restless for building Python RESTful API
---

After trying to build RESTful API in Django with [Django Rest Framework][0], my friend [Pabluk][1] allow me to discover [Restless][2]. A very small code base Python module to build RESTful API.

I'm totally not a huge fan of [Django Rest Framework][0]. It's just to restricting if you don't make a 100% REST API, and this is often the case. First of all, you'll have a long progression period when you'll have a bad use of this framework. I think it's to complex while we try a build something simple. A RESTful API.

But, this is [Restless][2].
It has so small code base that it can fit to many web frameworks, like [Flask][3], [Pyramid][4], [Django][5], etc...

Let's try a [Django][5] integration.

#### Example

This is your existing `models.py` :

```
class Country(models.Model):
	name = models.CharField(max_length=2)
    
class Pizza(models.Model):
    name = models.CharField(max_length=255)
    country = models.ForeignKey(Country)
    
```

You have to understand that your API will expose *Resources*, this is how many framework call it. Let's create a `resources.py`.

```
from restless.dj import DjangoResource
from restless.preparers import FieldsPreparer

class CountryResource(DjangoResource):
    preparer = FieldsPreparer(fields={
        'id': 'id',
        'name': 'name'})

class PizzaResource(DjangoResource):
	preparer = FieldsPreparer(fields={
    	'id': 'id',
        'name': 'name',
        'country': 'country.id'})
```

And now just expose it through your `urls.py` :

```
[...]
url(r'^api/countries/', include(CountryResource.urls())),
url(r'^api/pizzas/', include(PizzaResource.urls())),
[...]
```

This is quite simple, I won't explain you how to use `POST`, `PATCH`, etc... Just read [documentation][6].

But just leave me show you a simple mixin for pagination. Let's create a `mixins.py`, just as Django *Class Based View*.

```
from django.core.paginator import Paginator

class APIPaginatorMixin:
    per_page = 25
    
    def get_queryset(self):
        raise NotImplementedError()
    
    def wrap_list_response(self, data):
        return {
            "objects": data,
            "per_page": self.paginator.per_page,
            "count": self.paginator.count,
            "num_page": self.paginator.num_pages,
            "page": self.page}

	def paginate(self, queryset, per_page=None):
        if per_page is None:
            per_page = self.per_page

        per_page_arg = int(self.request.GET.get('per_page'))
        self.paginator = Paginator(queryset, per_page)

        self.page = int(self.request.GET.get('page', 1))
    
    def list(self):
        qs = self.get_queryset()
        self.paginate(qs)
        return self.paginator.page(self.page)
```

You just have to update your `resources.py` :

```
from restless.dj import DjangoResource
from restless.preparers import FieldsPreparer
from .mixins import APIPaginatorMixin

class CountryResource(APIPaginatorMixin, DjangoResource):
    preparer = FieldsPreparer(fields={
        'id': 'id',
        'name': 'name'})

	def get_queryset(self):
    	return Pizza.objects.all()

class PizzaResource(APIPaginatorMixin, DjangoResource):
	preparer = FieldsPreparer(fields={
    	'id': 'id',
        'name': 'name',
        'country': 'country.id'})
        
    def get_queryset(self):
    	return Country.objects.all()
```

And just enjoy your paginated Restful API.
This is why I love [Restless][2]. I agree to say that you'll write probably more code than [Django Rest Framework][0] but you'll get more control of all the magic your Restless API do.

[Restless][2] is very simple, modular and not restrictive. It's very close to KISS.

Another thing that I like, it's the easiness to read [Restless][2] source code like Django *Class Based Views*. Simple, re-usable.

> Simple is better than complex.

[0]: http://www.django-rest-framework.org/
[1]: http://seminar.io
[2]: https://github.com/toastdriven/restless
[3]: http://flask.pocoo.org/
[4]: http://www.pylonsproject.org/
[5]: https://www.djangoproject.com/
[6]: http://restless.readthedocs.org/en/latest/index.html
