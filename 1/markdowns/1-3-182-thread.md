
# Post \#73251012 [Link](https://stackoverflow.com/questions/73251012/)

## Put logo and title above/on top of page navigation in sidebar of streamlit multipage app

**Vote**: 3 (575/702) **Views**: 3713 (553/702) 

**Internal ID** \#1-3-182

Created at 2022-08-05 14:03:57

Tags: `python` `streamlit`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I am using the new [multipage feature](https://docs.streamlit.io/library/get-started/multipage-apps) and would like to style my multipage app and put a logo with a title on top of/before the page navigation.
Here's a small example tested on `Python 3.9` with `streamlit==1.11.1` in the following directory structure:
```
/Home.py
/pages/Page_1.py
/pages/Page_2.py
```

`Home.py`:
```
import streamlit as st


st.sidebar.markdown(
    "My Logo (sidebar) should be on top of the Navigation within the sidebar"
)

st.markdown("# Home")
```

`Page_1.py`:
```
import streamlit as st

st.markdown("Page 1")
```

`Page_2.py`:
```
import streamlit as st

st.markdown("Page 2")
```

which I can run using:
```
$ streamlit run Home.py
```

But this leads to the Text printed  and not  the navigation:
[](https://i.stack.imgur.com/ZUjGl.png)
Is there any way to do this? Any hints are welcome!
Best wishes,
Cord


----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2022-08-05 14:32:33

------------

One option is to do it via CSS, with a function like  this:
```
def add_logo():
    st.markdown(
        """
        <style>
            [data-testid="stSidebarNav"] {
                background-image: url(http://placekitten.com/200/200);
                background-repeat: no-repeat;
                padding-top: 120px;
                background-position: 20px 20px;
            }
            [data-testid="stSidebarNav"]::before {
                content: "My Company Name";
                margin-left: 20px;
                margin-top: 20px;
                font-size: 30px;
                position: relative;
                top: 100px;
            }
        </style>
        """,
        unsafe_allow_html=True,
    )
```

And then just call that function at the top of each page. That produces an effect like this: [](https://i.stack.imgur.com/JReur.png)


------------
    
    
## Answer \#1

 Vote: 4

Created at 2022-08-05 15:03:32

------------

You can also achieve this result with `PIL`:
This function will enable you to take control of the logo size as well.
```
from PIL import Image
import streamlit as st

# You can always call this function where ever you want

def add_logo(logo_path, width, height):
    """Read and return a resized logo"""
    logo = Image.open(logo_path)
    modified_logo = logo.resize((width, height))
    return modified_logo

my_logo = add_logo(logo_path="your/logo/path", width=50, height=60)
st.sidebar.image(my_logo)

# OR

st.sidebar.image(add_logo(logo_path="your/logo/path", width=50, height=60))
```

You can `call` the function in your home page to display your `logo`, and should in case you have  images to display in any of your pages.


------------
    
    
## Answer \#2

 Vote: 3

Created at 2022-08-08 13:39:08

------------

Based on Zachary Blackwoods answer and an [answer from the streamlit forum](https://discuss.streamlit.io/t/how-do-i-use-a-background-image-on-streamlit/5067/6?u=ckaldemeyer) to also deliver local files encoded in a string, I came up with this solution in my `Home.py`:
```
import base64
import streamlit as st


@st.cache(allow_output_mutation=True)
def get_base64_of_bin_file(png_file):
    with open(png_file, "rb") as f:
        data = f.read()
    return base64.b64encode(data).decode()


def build_markup_for_logo(
    png_file,
    background_position="50% 10%",
    margin_top="10%",
    image_width="60%",
    image_height="",
):
    binary_string = get_base64_of_bin_file(png_file)
    return """
            <style>
                [data-testid="stSidebarNav"] {
                    background-image: url("data:image/png;base64,%s");
                    background-repeat: no-repeat;
                    background-position: %s;
                    margin-top: %s;
                    background-size: %s %s;
                }
            </style>
            """ % (
        binary_string,
        background_position,
        margin_top,
        image_width,
        image_height,
    )


def add_logo(png_file):
    logo_markup = build_markup_for_logo(png_file)
    st.markdown(
        logo_markup,
        unsafe_allow_html=True,
    )

add_logo("img/my_logo.png")

st.markdown("# Home")
```

@Zachary Blackwood: Feel free to put this in your answer and I will delete my one.
Hope it helps someone!


------------
    
    