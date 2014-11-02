---
layout: post
title: Django SQL Debug - HTML reports of your HTTP requests
---

[Django Debug Toolbar][1] is an awesome tool but it has some limitations. It's an `HTML` panel that is injected in your web pages that allow you to debug and profile your application at different layers. From `HTTP` to `SQL` requests.

But when you do applications with a [REST][2] pattern for example, with a full javascript client for example, it quickly become pain in the ass to debug your app.
You can't reproduce easily `POST`, `PUT`, etc... requests.

Let's introduce [Django SQL Debug][3]. It's a tool that works with [Django Debug Toolbar][1] to generate an `HTML` report that you can read offline or later for each `HTTP` requests.

![Django SQL Debug screenshot](https://raw.githubusercontent.com/socketubs/django-sql-debug/master/screenshots/01.png)

It's goal is to let you see all your `SQL` queries (*and more*) when you don't have a direct access to [Django Debug Toolbar][1].
That's why it's generate an `HTML` report that looks like the toolbar.

You can install it like any other Django application:

```
pip install django-sql-debug
```

Just add `django_sql_debug` before `debug_toolbar` in `INSTALLED_APPS`. Configure your `HTML` reports directory, let's use your app and see your first report.

Development is at the very beginning and it's more a proof of concept for now but it's usable and it can save you in special cases.

You can find source at [Github][3]. Contribution and any feedbacks are welcome.

Let's discuss [here][4].

[1]: https://github.com/django-debug-toolbar/django-debug-toolbar
[2]: http://en.wikipedia.org/wiki/Representational_state_transfer
[3]: https://github.com/socketubs/django-sql-debug
[4]: https://news.ycombinator.com/item?id=8268848
