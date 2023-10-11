
# Post \#62102453 [Link](https://stackoverflow.com/questions/62102453/)

## How to define callbacks in separate files? (plotly dash)

**Vote**: 25 (228/702) **Views**: 12770 (385/702) 

**Internal ID** \#1-3-186

Created at 2020-05-30 12:28:59

Tags: `python` `plotly-dash`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------


### Background



Dash web applications have a dash application instance, usually named `app`, and initiated like this:

```
app = dash.Dash(__name__)
```


Then, callbacks are added to the application using a `callback` decorator:

```
@app.callback(...)
def my_function(...):
    # do stuff.
```


In most of the tutorials you find, the callbacks are defined with all of the application layout in the `app.py`. This of course is just the MWE way of doing things. In a real application, separating code to modules and packages would greatly improve readability and maintainability, but naively separating the callbacks to and layouts just results into circular imports. 


### Question



What would be the correct way to separate callbacks and layouts from the `app.py` in a single page app? 


## MWE



Here is a minimal (non-)working example with the problem


### File structure



```
.
├── my_dash_app
│   ├── app.py
│   └── views
│       ├── first_view.py
│       └── __init__.py
└── setup.py
```



### setup.py



```
import setuptools

setuptools.setup(
    name='dash-minimal-realworld',
    version='1.0.0',
    install_requires=['dash>=1.12.0'],
    packages=setuptools.find_packages(),
)
```



### app.py



```
import dash

from my_dash_app.views.first_view import make_layout

app = dash.Dash(__name__)
app.layout = make_layout()


if __name__ == '__main__':
    app.run_server(debug=True)
```



### first_view.py



```
from dash.dependencies import Input, Output

import dash_core_components as dcc
import dash_html_components as html

from my_dash_app.app import app 

def make_layout():
    return html.Div([
        dcc.Input(id='my-id', value='initial value', type='text'),
        html.Div(id='my-div')
    ])

@app.callback(Output(component_id='my-div', component_property='children'),
              [Input(component_id='my-id', component_property='value')])
def update_output_div(input_value):
    return 'You\'ve entered "{}"'.format(input_value)
```


Running `python ./my_dash_app/app.py` results into circular dependency:

```
ImportError: cannot import name 'make_layout' from 'my_dash_app.views.first_view' (c:\tmp\dash_minimal_realworld\my_dash_app\views\first_view.py)
```



----------
        
## Answer \#0

**Accepted** Vote: 18

Created at 2020-05-30 12:41:05

------------

I don't think  that there's a correct way of doing it per se, but what you could do it have a central module (`maindash.py`) around your startup code `app = dash.Dash(__name__)`, and have different  simply import `app` from `my_dash_app.maindash`. This would set up the callbacks in their own separate modules but re-use that one central module for the `app` instance.

It's easiest to show an overview of it like this:
[](https://i.stack.imgur.com/7SJQ6.png)

`app.py` being the main script called to start everything up. `maindash.py` is in charge of creating the main app instance. `first_view.py` is where the decorators are defined to set up all the callbacks.

Here's the result:
[](https://i.stack.imgur.com/qfWIK.png)

```
.
├── my_dash_app
│   ├── app.py
│   ├── maindash.py
│   └── views
│       ├── first_view.py
│       └── __init__.py
└── setup.py
```


Since imports are re-used in Python, there's no real harm in doing `from my_dash_app.maindash import app` several times from different other modules, such as event handlers and the main script. They'll share the same import instance - thus re-using the `dash.Dash()` instance as well.

Just make sure you import the central module before setting up the handlers, and you should be good to go.

Here's the code snippets separated for testing:


## app.py



```
from my_dash_app.maindash import app
from my_dash_app.views.first_view import make_layout

if __name__ == '__main__':
    app.layout = make_layout()
    app.run_server(debug=True)
```



## maindash.py



```
import dash
app = dash.Dash(__name__)
```



## first_view.py



```
from my_dash_app.maindash import app
from dash.dependencies import Input, Output

import dash_core_components as dcc
import dash_html_components as html

def make_layout():
    return html.Div([
        dcc.Input(id='my-id', value='initial value', type='text'),
        html.Div(id='my-div')
    ])

@app.callback(Output(component_id='my-div', component_property='children'),
              [Input(component_id='my-id', component_property='value')])
def update_output_div(input_value):
    return 'You\'ve entered "{}"'.format(input_value)
```



------------
    
    
## Answer \#1

 Vote: 16

Created at 2021-06-30 14:19:19

------------

A little late to the party but I have found this to be a straightforward way of doing it:

1. You create a separate script called 'callbacks.py' (for example)
2. Define a function within callbacks.py which takes a dash.Dash object (i.e. the app) as a parameter (you can of course pass more arguments if necessary) and within which you define all your callbacks as you normally would in the main script:


```
def get_callbacks(app):
    @app.callback([Output("figure1", "figure")],
                  [Input("child1", "value")])
    def callback1(figure):
        return

    @app.callback([Output("figure2", "figure")],
                  [Input("child2", "value")])
    def callback2(figure):
        return
```


1. Within the main script, simply import the function and call it after instantiating the dash.Dash object passing the same object into it:


```
import dash
from callbacks import get_callbacks
import layout

app = dash.Dash(__name__)
app.layout = layout.layout

get_callbacks(app)
```



------------
    
    
## Answer \#2

 Vote: 10

Created at 2020-11-25 22:15:12

------------

I know it is too late to answer your question here, but maybe someone else will find it useful.
I wanted to be able to create callbacks in separate files, however I think that although importing an app from main dash module works well, it may be unclear for other people who read the code.
I created a callback manager used to initialize callbacks. This manager is attached to an app in the main app module.

## callbacks_manager.py


```
from dataclasses import dataclass, field
from typing import Callable, List, Union
from dash.dependencies import handle_callback_args
from dash.dependencies import Input, Output, State


@dataclass
class Callback:
    func: Callable
    outputs: Union[Output, List[Output]]
    inputs: Union[Input, List[Input]]
    states: Union[State, List[State]] = field(default_factory=list)
    kwargs: dict = field(default_factory=lambda: {"prevent_initial_call": False})


class CallbackManager:
    def __init__(self):
        self._callbacks = []

    def callback(self, *args, **kwargs):
        output, inputs, state, prevent_initial_call = handle_callback_args(
            args, kwargs
        )

        def wrapper(func):
            self._callbacks.append(Callback(func,
                                            output,
                                            inputs,
                                            state,
                                            {"prevent_initial_callback": prevent_initial_call}))

        return wrapper

    def attach_to_app(self, app):
        for callback in self._callbacks:
            app.callback(
                callback.outputs, callback.inputs, callback.states, **callback.kwargs
            )(callback.func)
```


## callbacks.py


```
import dash

from callback_manager import CallbackManager

callback_manager = CallbackManager()


@callback_manager.callback(
    dash.dependencies.Output('label', 'children'),
    [dash.dependencies.Input('call_btn', 'n_clicks')])
def update_label(n_clicks):
    if n_clicks > 0:
        return "Callback called!"
```


## app.py


```
import dash
import dash_html_components as html

from callbacks import callback_manager

app = dash.Dash(__name__)
callback_manager.attach_to_app(app)

app.layout = html.Div([
    html.Div(id="label"),
    html.Button('Call callback', id='call_btn', n_clicks=0),
])
if __name__ == '__main__':
    app.run_server(debug=True)
```

Note that you can have multiple files with callbacks and import them with `as` keyword:
```
from callbacks1 import callback_manager as callback_manager1
from callbacks2 import callback_manager as callback_manager2

app = dash.Dash(__name__)
callback_manager1.attach_to_app(app)
callback_manager2.attach_to_app(app)
```

I believe doing it this way is more explicit.


------------
    
    
## Answer \#3

 Vote: 5

Created at 2022-06-02 15:07:53

------------

You can just use the decorator `@dash.callback` instead of `@app.callback`. Then remove the line `from my_dash_app.app import app` in `first_view.py` and you'll get rid of the circular dependency.
From the documentation: `@dash.callback` is an alternative to `@app.callback (where app = dash.Dash())` introduced in Dash 2.0. It allows you to register callbacks without defining or importing the app object. The call signature is identical and it can be used instead of `app.callback` in all cases.


------------
    
    
## Answer \#4

 Vote: 2

Created at 2023-02-02 18:08:09

------------

This is a rather late response to an older post, but here's a very simple way to completely separate layout, callbacks, and app, without introducing additional complexity.

```
from dash import Dash

from layouts import my_layout
from callbacks import my_callback, my_callback_inputs, my_callback_outputs

if __name__ == "__main__":
    app = Dash(__name__)

    app.layout = my_layout
    app.callback(my_callback_outputs, my_callback_inputs)(my_callback)

    app.run_server()
```

It works by  using the decorator syntax, which is supposed to be "syntactic sugar" to make things simpler.  However, for this specific problem, many of the proposed solutions actually increase coupling and complexity while retaining the decorator syntax.  In this case, it's simpler just not to use the decorator.
In `callbacks.py`, `my_callback` is defined without any decorator:

```
my_callback_inputs = []
my_callback_outputs = []


def my_callback():
    return
```



------------
    
    