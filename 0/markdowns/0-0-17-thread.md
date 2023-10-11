
# Post \#68814136 [Link](https://stackoverflow.com/questions/68814136/)

## TipTap/VueJS - How to detect a keypress

**Vote**: 2 (631/702) **Views**: 2816 (577/702) 

**Internal ID** \#0-0-17

Created at 2021-08-17 08:33:58

Tags: `vue.js` `tiptap` `prose-mirror`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I have a collaborative chat application that uses [tiptap](https://www.tiptap.dev/) for it's messaging area. I found it useful as it can support multiple formats already and can add some flexibility. However, I found myself stuck when looking for an event listener that listens for "enter" key. When the user hit the enter, I want to submit their chat and clear the editor.
I found this  event listener but I couldn't find exactly the place where it detects what key is pressed.
Sample code is below:
```
mounted() {
  let editor = new Editor({
    extensions: [StarterKit],
    content: this.value
  });

  editor.on("update", e => {
    console.log(e);
    this.$emit("input", this.editor.getHTML());
  });

  this.editor = editor;
}
```

I'm using Vue2, btw.
Thanks


----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2021-08-19 01:31:25

------------

In the editor props, pass in a callback
```
handleDOMEvents: { 
        keydown: (view, event) => {
          if (event.key === "Enter") {
          }
          return false;
        }
      },
```

[https://www.tiptap.dev/api/editor/#editor-props](https://www.tiptap.dev/api/editor/#editor-props)
[https://prosemirror.net/docs/ref/#view.EditorProps](https://prosemirror.net/docs/ref/#view.EditorProps)


------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-08-18 01:00:30

------------

For reference, I managed to do something similar. I use VueJS native keydown event to detect which key is pressed.
```
<EditorContent class="editor" :editor="editor" @keydown.native="onKeydown" />
```

```
methods: {
  onKeydown(e) {
    if (!e.shiftKey && e.which == 13) {
      this.editor.commands.undo(); // i just "undo" the enter event to remove the space it made
      this.$emit("onEnter");
    }
  }
}
```

Reference:
[https://www.tiptap.dev/installation/vue2#5-use-v-model-optional](https://www.tiptap.dev/installation/vue2#5-use-v-model-optional)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-02-01 09:21:43

------------

```
editorProps: {
    handleDOMEvents: {
      keypress: (view, event) => {
        if (event.key === 'Enter') {
          console.log('Heyyyy')
        }
      },
    },
  },
```

You can pass it as props to new Editor()


------------
    
    