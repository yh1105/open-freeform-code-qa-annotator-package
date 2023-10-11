
# Post \#70262174 [Link](https://stackoverflow.com/questions/70262174/)

## How to make the positions of buttons in navbar fixed even when the screen size changes in Dash-plotly with python

**Vote**: 1 (672/702) **Views**: 2751 (580/702) 

**Internal ID** \#1-3-206

Created at 2021-12-07 14:45:24

Tags: `python` `html` `plotly` `plotly-dash` `dash-bootstrap-components`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `html` `python`)

----------

**Notepad**


----------

I am trying to keep the position of the buttons fixed in the Nanbar of my Dash app even when we zoom in the browser or if the screen size changes. I used dash bootstrap components to make the layout but the buttons disorient when I zoom in or if I use a smaller display. I am new to this so any help would be appreciated.
At 100% zoom
[](https://i.stack.imgur.com/8c4Fr.png)
At 110% zoom
[](https://i.stack.imgur.com/GbIcC.png)
this is my code
```
import random
import time
import webbrowser
from collections import deque

import dash
import dash_core_components as dcc
import dash_html_components as html
import plotly.graph_objs as go
from dash.dependencies import Output, Input

BS = "https://codepen.io/chriddyp/pen/bWLwgP.css"
app = dash.Dash('vehicle-data', external_stylesheets=[BS])

button_group = html.Div(
    [
        dbc.NavbarBrand(dcc.Link(
            dbc.Button(children='HOME',
                       style={
                           'display': 'inline-block',
                           'align': 'center',
                           'color': 'white', 'marginLeft': '100px',
                           'fontSize': '15px ',
                           'backgroundColor': '#101820',
                           'width': '150px',
                           'height': '50px',
                           'marginRight': '100px'
                       }, className='lg'),

            href='http://127.0.0.1:5050/', refresh=True), className='lg'),

        dbc.NavbarBrand(dcc.Link(
            dbc.Button(children='OVERVIEW',
                       style={'color': 'white',
                              'backgroundColor': '#101820',
                              'marginLeft': '10px',
                              'fontSize': '15px ',
                              'width': '150px',
                              'marginRight': '100px',
                              'height': '50px'
                              }),
            href='/pages/overview', refresh=True)),
        dbc.NavbarBrand(dcc.Link(
            dbc.Button(children='GRAPH',
                       style={'color': 'white',
                              'backgroundColor': '#101820',
                              'fontSize': '15px ',
                              'marginLeft': '10px',
                              'marginRight': '100px',
                              'width': '150px',
                              'height': '50px'
                              }),
            href='/pages/graph_page', refresh=True)),
        dbc.NavbarBrand(dcc.Link(
            dbc.Button(children='CONSOLE',
                       style={'color': 'white',
                              'backgroundColor': '#101820',
                              'fontSize': '15px ',
                              'marginLeft': '10px',
                              'marginRight': '100px',
                              'width': '150px',
                              'height': '50px'
                              }),
            href='/log_stream', refresh=True)),
        dbc.NavbarBrand(dcc.Link(
            dbc.Button(children='DIAGNOSTIC',
                       style={'color': 'white',
                              'backgroundColor': '#101820',
                              'marginLeft': '2px',
                              'fontSize': '15px ',
                              'width': '170px',
                              'marginRight': '100px',
                              'height': '50px'
                              }),
            href='/pages/diag', refresh=True))
    ],

)

app.layout = html.Div([
    html.Div([
        dbc.Row(
            [
                dbc.Col([button_group]),
            ],
            style={
                'textAlign': 'center',
                'position': 'sticky',
                'backgroundColor': '#101820',
                'display': 'flex',
                'marginRight': '0px',
            },
        ),
        dcc.Location(id='url', refresh=False),
        html.Div(id='page-content', children=[])
    ]),
])


if __name__ == '__main__':
    webbrowser.open('http://127.0.0.1:8050/')
    app.run_server(debug=True)
```

[](https://i.stack.imgur.com/CQytJ.png)


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2022-09-02 14:58:25

------------

Simply add `brand="Fixed Navbar"` to your code just like shown below:
```
nav = dbc.NavbarSimple(
    children=[
        dbc.NavItem(dbc.NavLink("Page 1", href="#")),
        dbc.DropdownMenu(
            children=[
                dbc.DropdownMenuItem("More pages", header=True),
                dbc.DropdownMenuItem("Page 2", href="#"),
                dbc.DropdownMenuItem("Page 3", href="#"),
            ],
            nav=True,
            in_navbar=True,
            label="More",
        ),
    ],
    brand="Fixed Navbar",
    brand_href="#",
    color="lightgreen",
    dark=True,
)
```

now above assigned `nav` can be added to UI by:
```
app.layout = html.Div(children=[

    nav,
])

if __name__ == '__main__':
    app.run_server(debug=True)
```

Also,
make sure to add `import dash_bootstrap_components as dbc` to your code.


------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-12-09 07:16:35

------------

```
import random
import time
import webbrowser
from collections import deque

import dash
import dash_core_components as dcc
import dash_html_components as html
import dash_bootstrap_components as dbc
import plotly.graph_objs as go
from dash.dependencies import Output, Input

BS = "https://codepen.io/chriddyp/pen/bWLwgP.css"
app = dash.Dash('vehicle-data', external_stylesheets=[BS])

button_group = html.Div(
    [
        dbc.NavbarBrand(dcc.Link(
            dbc.Button(children='HOME',
                       style={
                           'display': 'inline-block',
                           'align': 'center',
                           'color': 'white', 'marginLeft': '100px',
                           'fontSize': '15px ',
                           'backgroundColor': '#101820',
                           'width': '150px',
                           'height': '50px',
                           'marginRight': '100px'
                       }, className='lg'),

            href='http://127.0.0.1:5050/', refresh=True), className='lg'),

        dbc.NavbarBrand(dcc.Link(
            dbc.Button(children='OVERVIEW',
                       style={'color': 'white',
                              'backgroundColor': '#101820',
                              'marginLeft': '10px',
                              'fontSize': '15px ',
                              'width': '150px',
                              'marginRight': '100px',
                              'height': '50px'
                              }),
            href='/pages/overview', refresh=True)),
        dbc.NavbarBrand(dcc.Link(
            dbc.Button(children='GRAPH',
                       style={'color': 'white',
                              'backgroundColor': '#101820',
                              'fontSize': '15px ',
                              'marginLeft': '10px',
                              'marginRight': '100px',
                              'width': '150px',
                              'height': '50px'
                              }),
            href='/pages/graph_page', refresh=True)),
        dbc.NavbarBrand(dcc.Link(
            dbc.Button(children='CONSOLE',
                       style={'color': 'white',
                              'backgroundColor': '#101820',
                              'fontSize': '15px ',
                              'marginLeft': '10px',
                              'marginRight': '100px',
                              'width': '150px',
                              'height': '50px'
                              }),
            href='/log_stream', refresh=True)),
        dbc.NavbarBrand(dcc.Link(
            dbc.Button(children='DIAGNOSTIC',
                       style={'color': 'white',
                              'backgroundColor': '#101820',
                              'marginLeft': '2px',
                              'fontSize': '15px ',
                              'width': '170px',
                              'marginRight': '100px',
                              'height': '50px'
                              }),
            href='/pages/diag', refresh=True))
    ],

)

app.layout = html.Div([
    html.Div([
        dbc.Row(
            [
                dbc.Col([button_group]),
            ],
            style={
                'textAlign': 'center',
                'position': 'sticky',
                'backgroundColor': '#101820',
                'display': 'flex',
                'marginRight': '0px',
                'maxWidth':'1500px',
                'width':'1500px'
            },
        ),
        dcc.Location(id='url', refresh=False),
        html.Div(id='page-content', children=[])
    ]),
])


if __name__ == '__main__':
    webbrowser.open('http://127.0.0.1:8050/')
    app.run_server(debug=True)
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-12-07 21:08:54

------------

To prevent the contents of the block from being wrapped to another line, you need to add the `white-space: nowrap;` to this block CSS style.
So, for Dash it is `'whiteSpace': 'nowrap'`:
```
app.layout = html.Div([
    html.Div([
        dbc.Row(
            [
                dbc.Col(
                    [button_group],
                    style={'whiteSpace': 'nowrap'}
                ),
            ],
            style={
                'textAlign': 'center',
                'position': 'sticky',
                'backgroundColor': '#101820',
                'display': 'flex',
                'marginRight': '0px',
            },
        ),
        dcc.Location(id='url', refresh=False),
        html.Div(id='page-content', children=[])
    ]),
])
```

The only way to display the navigation bar correctly when the zoom changes, which I know is a fixed width layout.
This is a sample code with a little refactoring. The style of the buttons has been moved to `nav_btn_style`, unnecessary nested elements have been removed.
```
import dash
import dash_core_components as dcc
import dash_html_components as html
import plotly.graph_objs as go
from dash.dependencies import Output, Input
import dash_bootstrap_components as dbc

# BS = "https://codepen.io/chriddyp/pen/bWLwgP.css"
app = dash.Dash('vehicle-data', external_stylesheets=[dbc.themes.BOOTSTRAP])

# style for navigation buttons
nav_btn_style = {
    'align': 'center',
    'color': 'white', 
    'backgroundColor': '#101820',
    'fontSize': '1rem',
    'width': '10rem',
    'height': '3.2rem',
    'margin': '0rem 1rem',
}

button_group = dbc.Nav(
    [
        dbc.NavItem(dcc.Link(
            dbc.Button(
                children='HOME',
                style=nav_btn_style,
                ),
            href='/',
            refresh=True),
            ),
        dbc.NavItem(dcc.Link(
            dbc.Button(
                children='OVERVIEW',
                style=nav_btn_style,
                ),
            href='/pages/overview',
            refresh=True),
            ),
        dbc.NavItem(dcc.Link(
            dbc.Button(
                children='GRAPH',
                style=nav_btn_style,
                ),
            href='/pages/graph_page',
            refresh=True),
            ),
        dbc.NavItem(dcc.Link(
            dbc.Button(
                children='CONSOLE',
                style=nav_btn_style
                ),
            href='/log_stream',
            refresh=True),
            ),
        dbc.NavItem(dcc.Link(
            dbc.Button(
                children='DIAGNOSTIC',
                style=nav_btn_style,
                ),
            href='/pages/diag',
            refresh=True),
            )
    ],
    horizontal='around',
    style={
        'flexWrap': 'nowrap',  # no wrap buttons 
        'padding': '0.5rem 3rem',
        'border': '3px dotted crimson',  # nav bar border
        'position': 'sticky',
        'backgroundColor': '#101820',
        }
)

app.layout = html.Div([
    button_group,
    dcc.Location(id='url', refresh=False),
    html.Div(
        id='page-content', children=['TEST'],
        style={
            'height': '20rem',
            'border': '3px dotted royalblue',
            'display': 'flex',
            'justifyContent': 'center',
            'alignItems': 'center'
            }
        ),
    ],
    style={'margin': '0px auto', 'width': '80rem'}  # fixed width for outer div
)


if __name__ == '__main__':
    webbrowser.open('http://127.0.0.1:8050/')
    app.run_server(debug=True)
```

and how it look
[](https://i.stack.imgur.com/OgzSI.png)


------------
    
    