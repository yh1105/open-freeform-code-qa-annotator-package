
# Post \#56503954 [Link](https://stackoverflow.com/questions/56503954/)

## Bootstrap toast does not show up

**Vote**: 17 (290/702) **Views**: 54674 (153/702) 

**Internal ID** \#0-2-152

Created at 2019-06-08 06:09:38

Tags: `javascript` `jquery` `html` `css` `bootstrap-4`

----------

#### Metadata:

Area: `Front-end`

Language: `html` (full parsed tag list: `html`)

----------

**Notepad**


----------

I am developing a website in HTML using bootstrap and javascript. I am trying to add a toast using the following code from the bootstrap website:

```
<div class="toast" role="alert" aria-live="assertive" aria-atomic="true">
  <div class="toast-header">
    <img src="..." class="rounded mr-2" alt="...">
    <strong class="mr-auto">Bootstrap</strong>
    <small class="text-muted">11 mins ago</small>
    <button type="button" class="ml-2 mb-1 close" data-dismiss="toast" aria-label="Close">
      <span aria-hidden="true">&times;</span>
    </button>
  </div>
  <div class="toast-body">
    Hello, world! This is a toast message.
  </div>
</div>
```



But the toast does not show up as shown in the image. It just leaves a gap between the nav and the carousel.[](https://i.stack.imgur.com/rTRk0.png)

I have imported all of these scripts from the bootstrap website:

```
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>
```



And have put the following into my javascript document:

```
$('.toast').toast(option)
```



But still, nothing happens. I hope someone can help me out :)


----------
        
## Answer \#0

**Accepted** Vote: 26

Created at 2019-06-08 06:34:17

------------

You need to put the valid option. i:e `show, hide or a callback function`. See - [https://getbootstrap.com/docs/4.2/components/toasts/](https://getbootstrap.com/docs/4.2/components/toasts/).

```
$('.toast').toast('show');
```
```
<link href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" rel="stylesheet"/>

<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js"></script>
<div class="toast" role="alert" aria-live="assertive" aria-atomic="true">
  <div class="toast-header">
    <img height="200px" width="200px" src="https://upload.wikimedia.org/wikipedia/commons/f/f9/Phoenicopterus_ruber_in_S%C3%A3o_Paulo_Zoo.jpg" class="rounded mr-2" alt="...">
    <strong class="mr-auto">Bootstrap</strong>
    <small class="text-muted">11 mins ago</small>
    <button type="button" class="ml-2 mb-1 close" data-dismiss="toast" aria-label="Close">
                <span aria-hidden="true">&times;</span>
                </button>
  </div>
  <div class="toast-body">
    Hello, world! This is a toast message.
  </div>
</div>
```



If you do not want to close the toast automatically, add `data-autohide="false"`.

```
$('.toast').toast('show')
```
```
<link href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" rel="stylesheet"/>

<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js"></script>
<div class="toast" data-autohide="false">
  <div class="toast-header">
    <svg class=" rounded mr-2" width="20" height="20" xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="xMidYMid slice" focusable="false" role="img">
                    <rect fill="#007aff" width="100%" height="100%" /></svg>
    <strong class="mr-auto">Bootstrap</strong>
    <small class="text-muted">11 mins ago</small>
    <button type="button" class="ml-2 mb-1 close" data-dismiss="toast" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                  </button>
  </div>
  <div class="toast-body">
    Hello, world! This is a toast message.
  </div>
</div>
```




------------
    
    
## Answer \#1

 Vote: 5

Created at 2020-08-09 21:38:33

------------

```
<link href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" rel="stylesheet"/>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js"></script>


<div class="toast" data-autohide="false">
    <div class="toast-header">
        <svg class=" rounded mr-2" width="20" height="20" xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="xMidYMid slice" focusable="false" role="img">
        <rect fill="#007aff" width="100%" height="100%" /></svg>
        <strong class="mr-auto">Bootstrap</strong>
        <small class="text-muted">11 mins ago</small>
        <button type="button" class="ml-2 mb-1 close" data-dismiss="toast" aria-label="Close">
            <span aria-hidden="true">&times;</span>
        </button>
    </div>
    <div class="toast-body">
    Hello, world! This is a toast message.
    </div>
</div>
```

You need to start " $(".toast").toast('show'); " when document is ready.
```
<script>
    $(document).ready(function() {
        $(".toast").toast('show');
    });
</script>
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-07-24 23:47:53

------------

Firstly, you need to make the toast fixed to top or bottom, depending on where you want it.
```
<div role="alert" aria-live="assertive" aria-atomic="true" class="toast fixed-bottom m-5 ms-auto"
    data-bs-autohide="false">
    <div class="toast-header">
        <img src="..." class="rounded me-2" alt="...">
        <strong class="me-auto">Bootstrap</strong>
        <small>11 mins ago</small>
        <button type="button" class="btn-close" data-bs-dismiss="toast" aria-label="Close"></button>
    </div>
    <div class="toast-body">
        Hello, world! This is a toast message.
    </div>
</div>
<script>
    window.onload = (event) => {
        let myAlert = document.querySelector('.toast');
        let bsAlert = new bootstrap.Toast(myAlert);
        
        setTimeout(function () {
            bsAlert.show();
        }, 5000);
    };
</script>
```



------------
    
    