
# Post \#53090699 [Link](https://stackoverflow.com/questions/53090699/)

## How to run an alert on button click React.js

**Vote**: 17 (290/702) **Views**: 128972 (66/702) 

**Internal ID** \#0-0-53

Created at 2018-10-31 19:30:20

Tags: `javascript` `node.js` `reactjs` `frontend`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I've been running through a react file upload tutorial and want to add on to it. I'm trying to make it so when a user clicks the upload message the browser will prompt them saying "Your file is being uploaded" I'm not a frontend dev by any means so please forgive me if this question is super nooby. For some reason when I use this code, if you navigate to the web page the code in the  function runs once, and then again on click. My intended use is to only run on click, any idea what I am doing wrong?

```
import React, { Component } from 'react'
import { Alert } from 'react-alert'

class Main extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      imageURL: '',
    };

    this.handleUploadImage = this.handleUploadImage.bind(this);
  }

  handleUploadImage(ev) {
    ev.preventDefault();

    const data = new FormData();
    data.append('file', this.uploadInput.files[0]);
    data.append('filename', this.fileName.value);

    fetch('http://localhost:8000/upload', {
      method: 'POST',
      body: data,
    }).then((response) => {
      response.json().then((body) => {
        this.setState({ imageURL: `http://localhost:8000/${body.file}` });
      });
    });
  }

  render() {
    return (
      <form onSubmit={this.handleUploadImage}>
        <div>
          <input ref={(ref) => { this.uploadInput = ref; }} type="file" />
        </div>
        <div>
          <input ref={(ref) => { this.fileName = ref; }} type="text" placeholder="Enter the desired name of file" />
        </div>
        <br />
        <div>
          <button onclick="myFunction()">Upload</button>
              <script>
              function myFunction() {
                  alert("Your file is being uploaded!")
              }
              </script>

        </div>
        <img src={this.state.imageURL} alt="img" />
      </form>
    );
  }
}

export default Main;
```



----------
        
## Answer \#0

**Accepted** Vote: 20

Created at 2018-10-31 20:25:31

------------

Why don't you move the alert at the begining of handleUploadImage function?

```
handleUploadImage(ev) {
    alert("Your file is being uploaded!")
    ....
}
```


and instead of

```
<div>
  <button onclick="myFunction()">Upload</button>
      <script>
      function myFunction() {
          alert("Your file is being uploaded!")
      }
      </script>

</div>
```


you will have

```
<div>
  <button type="submit">Upload</button>
</div>
```



------------
    
    
## Answer \#1

 Vote: 7

Created at 2018-10-31 20:47:04

------------

for anyone curious, here is the final code following the help I received. 

```
import React, { Component } from 'react'
import { Alert } from 'react-alert'

class Main extends React.Component {


  constructor(props) {
    super(props);

    this.state = {
      imageURL: '',
    };

    this.handleUploadImage = this.handleUploadImage.bind(this);
  }

  handleUploadImage(ev) {
    alert("Your file is being uploaded!")
    ev.preventDefault();

    const data = new FormData();
    data.append('file', this.uploadInput.files[0]);
    data.append('filename', this.fileName.value);

    fetch('http://localhost:8000/upload', {
      method: 'POST',
      body: data,
    }).then((response) => {
      response.json().then((body) => {
        this.setState({ imageURL: `http://localhost:8000/${body.file}` });
      });
    });
  }

  render() {
    return (
      <form onSubmit={this.handleUploadImage}>
        <div>
          <input ref={(ref) => { this.uploadInput = ref; }} type="file" />
        </div>
        <div>
          <input ref={(ref) => { this.fileName = ref; }} type="text" placeholder="Enter the desired name of file" />
        </div>
        <br />
        <div>
          <button type="submit">Upload</button>
        </div>
        <img src={this.state.imageURL} alt="img" />
      </form>
    );
  }
}

export default Main;
```



------------
    
    
## Answer \#2

 Vote: 3

Created at 2018-10-31 19:33:54

------------

Change

```
<form onSubmit={this.handleUploadImage}>
```


To

```
<form onSubmit={e => this.handleUploadImage(e)}>
```


This will call handleUploadImage only when you click


------------
    
    