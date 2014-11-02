---
layout: post
title: Websocket with Flask and Gevent
---

## Ubik, package manager for Unix

If you follow me on [Github][7] you have maybe already seen that I currently develop a package manager for Unix, [Ubik][1].

In order to test my API, I create a _WebUI_ for _Ubik_ and the websockets comes to me.  
My two prefered web framework are __Django__ and __Flask__, but I need a _lightweight_ web app, so welcome to Flask.

## Node.js, blah, blah

Ok, I have many many times hear good things about __Node.js__ and __[Socket.io][4]__, no doubt.  
But my focus is to do a lightweight and __full Python__ implemented application.

So, tools are:

* [flask][2]
* [gevent][3]
* [gevent-websocket][4]

By the way, [gevent-socketio][6] seems to be a solid __socket.io__ Python implementation.

## App presentation

This is my Flask app structure:

{% highlight bash %}
.
├── runserver.py
├── requirements.txt
└── app
    ├── __init__.py
    ├── views.py
    ├── websocket.py
    ├── static
    │   └── ...
    └── templates
        └── index.html
{% endhighlight %}

### requirements.txt

{% highlight bash %}
flask
gevent
gevent-websocket
{% endhighlight %}

### \_\_init\_\_.py

This is the _app core_, create your app, import every shared object you want to use into your app, like databases, etc.

And don't forget to import views at the end to avoid circular imports.

{% highlight python %}
# coding: utf-8
import os

from flask import Flask
from websocket import handle_websocket

app = Flask(__name__)
app.secret_key = os.urandom(24)
app.debug = True

def my_app(environ, start_response):  
    path = environ["PATH_INFO"]  
    if path == "/":  
        return app(environ, start_response)  
    elif path == "/websocket":  
        handle_websocket(environ["wsgi.websocket"])   
    else:  
        return app(environ, start_response)  

import views
{% endhighlight %}

### websocket.py

All your websocket stuff could be here, this is the _websocket handler_.  
Messages from browser arrived here, and you can easily send message throught websocket with ``send`` method from ``ws`` object.  

Use ``json``, please.

{% highlight python %}
# coding: utf-8
import json

def handle_websocket(ws):
    while True:
        message = ws.receive()
        if message is None:
            break
        else:
            message = json.loads(message)

            r  = "I have received this message from you : %s" % message
            r += "<br>Glad to be your webserver."
            ws.send(json.dumps({'output': r}))
{% endhighlight %}

### views.py

Simple ``flask`` view to ``index.html`` template.

{% highlight python %}
# coding: utf-8
from flask import render_template

from app import app

@app.route('/')
def index():
    return render_template('index.html')
{% endhighlight %}

### runserver.py

Run your app with this script. It import your app and feed ``WSGIServer`` with it.

{% highlight python %}
#!/usr/bin/env python
# coding: utf-8
from gevent.pywsgi import WSGIServer
from geventwebsocket.handler import WebSocketHandler

from app import my_app

if __name__ == '__main__':
    http_server = WSGIServer(('',5000), my_app, handler_class=WebSocketHandler)
    http_server.serve_forever()
{% endhighlight %}

### index.html

And a quick _Javascript_ websocket example.

{% highlight html %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
</head>
<p>Websocket with Flask, Gevent and Gevent-websocket</p>
<p id="log"></p>
<button id="send" type="button">Send!</button>
<body>
    <script src="http://code.jquery.com/jquery-1.8.2.min.js"></script>
    <script>
        $(document).ready(function(){
            if ("WebSocket" in window) {
                ws = new WebSocket("ws://" + document.domain + ":5000/websocket");
                ws.onmessage = function (msg) {
                    var message = JSON.parse(msg.data);
                    $("p#log").html(message.output);
                };
            };

            // Bind send button to websocket
            $("button#send").live("click", function() {
                ws.send(JSON.stringify({'output': 'Sent from my browser!'}));
            });

            // Cleanly close websocket when unload window
            window.onbeforeunload = function() {
                ws.onclose = function () {}; // disable onclose handler first
                ws.close()
            };
        });
    </script>
</body>
</html>
{% endhighlight %}

All can find all this stuff in this [repository][8].
Thanks to [tzangms][9].

## What now ?

Play websockets with Flask and Python is fun !  

I will continue to improve [__Ubik__][1] _webui_ with _websocket_ and you have to take a look at the _0.2_ branch.  
Bunch of new features is coming !

[1]: https://github.com/Socketubs/Ubik
[2]: http://flask.pocoo.org/ 
[3]: http://www.gevent.org/
[4]: http://www.gelens.org/code/gevent-websocket/
[5]: http://socket.io/
[6]: https://github.com/abourget/gevent-socketio
[7]: https://github.com/Socketubs
[8]: https://github.com/tzangms/python-websocket-example
[9]: https://github.com/tzangms