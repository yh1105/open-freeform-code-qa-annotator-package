
# Post \#54989513 [Link](https://stackoverflow.com/questions/54989513/)

## React: Prevent scroll when modal is open

**Vote**: 48 (127/702) **Views**: 87696 (96/702) 

**Internal ID** \#0-0-28

Created at 2019-03-04 18:38:58

Tags: `javascript` `reactjs`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I have a custom modal component. When it's open, there is no scrolling whatsoever in the background.

I tried this code below:

```
componentDidMount() {
    document.body.style.overflow = 'hidden';
}

componentWillUnmount() {
    document.body.style.overflow = 'unset';
}
```


Which seems to work at first, but when I use the modal component, in another page, there is no scroll even when the modal is closed.

Is there a better solution for this?

My modal component:

```
export class Modal extends React.Component {

constructor(props) {
    super(props);
}

componentDidMount() {
    document.body.style.overflow = 'hidden';
}

componentWillUnmount() {
    document.body.style.overflow = 'unset';
}

render() {
    return (
        <React.Fragment>
            {this.props.showAt ?
                this.props.show ?
                    <div style={style} className={`${this.props.sectionName} ${modalTypeStyle ? modalTypeStyle : styles.modalWhite} ${modalTypeSize ? modalTypeSize : styles.modalSmall} ${!this.props.showAt ? styles.modalWhiteFixed : ""}`}>
                        {this.props.arrowShape ? <div className={arrowTypeStyle ? arrowTypeStyle : styles.triangleToprightWhite} /> : null}
                        {this.props.children}
                    </div>
                    : null
                :
                this.props.show ?
                    <div className={`${this.props.className} ${styles.modal}`}>
                        <div style={style} className={`${this.props.sectionName} ${modalTypeStyle ? modalTypeStyle : styles.modalWhite} ${modalTypeSize ? modalTypeSize : styles.modalSmall} ${!this.props.showAt ? styles.modalWhiteFixed : ""}`}>
                            {this.props.arrowShape ? <div className={arrowTypeStyle ? arrowTypeStyle : styles.triangleToprightWhite} /> : null}
                            {this.props.children}
                        </div>
                    </div> :
                    null}
        </React.Fragment>
    )
  }
}
```



----------
        
## Answer \#0

**Accepted** Vote: 58

Created at 2019-03-04 18:50:37

------------

Use state to track if the Modal is open and only hide scroll if it's true. Since you're using `document.body.style.overflow = 'hidden'` in `componentDidMount`, the component still gets mounted which calls the lifecycle method that hides the scroll on body.

```
export class Modal extends React.Component {

constructor(props) {
    super(props);
    this.state = {
      open:false
    }
}

componentDidMount() {    
  if(this.state.open){
    document.body.style.overflow = 'hidden';
  }    
}

componentWillUnmount() {
    document.body.style.overflow = 'unset';
}

render() {
    return (
        <React.Fragment>
            {this.props.showAt ?
                this.props.show ?
                    <div style={style} className={`${this.props.sectionName} ${modalTypeStyle ? modalTypeStyle : styles.modalWhite} ${modalTypeSize ? modalTypeSize : styles.modalSmall} ${!this.props.showAt ? styles.modalWhiteFixed : ""}`}>
                        {this.props.arrowShape ? <div className={arrowTypeStyle ? arrowTypeStyle : styles.triangleToprightWhite} /> : null}
                        {this.props.children}
                    </div>
                    : null
                :
                this.props.show ?
                    <div className={`${this.props.className} ${styles.modal}`}>
                        <div style={style} className={`${this.props.sectionName} ${modalTypeStyle ? modalTypeStyle : styles.modalWhite} ${modalTypeSize ? modalTypeSize : styles.modalSmall} ${!this.props.showAt ? styles.modalWhiteFixed : ""}`}>
                            {this.props.arrowShape ? <div className={arrowTypeStyle ? arrowTypeStyle : styles.triangleToprightWhite} /> : null}
                            {this.props.children}
                        </div>
                    </div> :
                    null}
        </React.Fragment>
    )
  }
}
```



------------
    
    
## Answer \#1

 Vote: 45

Created at 2019-10-08 16:49:26

------------

in functional component
for when mount or unmount

```
useEffect(() => {
     document.body.style.overflow = 'hidden';
     return ()=> document.body.style.overflow = 'unset';
  }, []);
```


or when `show` is change or etc

```
useEffect(() => {
     show && document.body.style.overflow = 'hidden';
     !show && document.body.style.overflow = 'unset';
  }, [show ]);
```



------------
    
    
## Answer \#2

 Vote: 26

Created at 2021-01-06 15:44:28

------------

Here is a custom hook you can use for functional components.
```
import { useEffect } from 'react';

export const useDisableBodyScroll = (open) => {
  useEffect(() => {
    if (open) {
      document.body.style.overflow = 'hidden';
    } else {
      document.body.style.overflow = 'unset';
    }
  }, [open]);
};
```

```
export const Modal = (props) => {
    useDisableBodyScroll(props.show);

    return (
        <React.Fragment>
            {props.showAt ?
                props.show ?
                    <div style={style} className={`${props.sectionName} ${modalTypeStyle ? modalTypeStyle : styles.modalWhite} ${modalTypeSize ? modalTypeSize : styles.modalSmall} ${!props.showAt ? styles.modalWhiteFixed : ""}`}>
                        {props.arrowShape ? <div className={arrowTypeStyle ? arrowTypeStyle : styles.triangleToprightWhite} /> : null}
                        {props.children}
                    </div>
                    : null
                :
                props.show ?
                    <div className={`${props.className} ${styles.modal}`}>
                        <div style={style} className={`${props.sectionName} ${modalTypeStyle ? modalTypeStyle : styles.modalWhite} ${modalTypeSize ? modalTypeSize : styles.modalSmall} ${!props.showAt ? styles.modalWhiteFixed : ""}`}>
                            {props.arrowShape ? <div className={arrowTypeStyle ? arrowTypeStyle : styles.triangleToprightWhite} /> : null}
                            {props.children}
                        </div>
                    </div> :
                    null}
        </React.Fragment>
    )
  }
}
```



------------
    
    
## Answer \#3

 Vote: 7

Created at 2021-09-10 12:06:37

------------

I have 2 functions to handle this, when open modal and when close modal
```
openModal(){
        this.setState({
            isModalOpen: true
        })
        document.body.style.overflow = 'hidden';
    }

closeModal(){
        this.setState({
            isModalOpen: false
        })
        document.body.style.overflow = 'unset';
    }
```



------------
    
    
## Answer \#4

 Vote: 5

Created at 2020-10-05 14:34:56

------------

In functional components if the browser move due to hiding/showing scroll.
```
useEffect(() => {
    if (show) {
      document.body.style.overflow = 'hidden';
      document.body.style.paddingRight = '15px';
    }
    return () => {
      document.body.style.overflow = 'unset';
      document.body.style.paddingRight = '0px';
    };
  }, [show]);
```



------------
    
    
## Answer \#5

 Vote: 5

Created at 2021-08-25 13:40:39

------------

For me it works when I check the show of the show.
Example in functional component
```
useEffect(() => {
    if (show) document.body.style.overflow = 'hidden';
    else document.body.style.overflow = 'visible';
  }, [show]);
```



------------
    
    
## Answer \#6

 Vote: 0

Created at 2021-06-12 07:44:10

------------

I tried according to Mehran Motiee's but every time I close a cookie consent, it clears the body style. So I tried adding class list instead and it works.
```
React.useEffect(() => {
    if (show) document.body.classList.add('overflow-hidden');
    else document.body.classList.remove('overflow-hidden');
  }, [show]);
```



------------
    
    
## Answer \#7

 Vote: -1

Created at 2020-12-27 22:30:04

------------

You can also use add the following class to your div containing the Modal:
```
.modal {
    position: fixed;
}
```

[Considerations for styling a modal](https://css-tricks.com/considerations-styling-modal/#fixed)
> The point of this is that a user could be scrolled down the page, trigger a modal, and have the modal be just as centered-and-visible as it would be if they weren’t scrolled.


------------
    
    