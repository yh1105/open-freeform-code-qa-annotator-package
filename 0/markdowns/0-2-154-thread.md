
# Post \#72546090 [Link](https://stackoverflow.com/questions/72546090/)

## How can I convert HTML to Object structure with text and formatting?

**Vote**: 1 (672/702) **Views**: 611 (686/702) 

**Internal ID** \#0-2-154

Created at 2022-06-08 12:56:12

Tags: `javascript` `html` `recursion` `data-conversion` `domparser`

----------

#### Metadata:

Area: `Front-end`

Language: `html` (full parsed tag list: `html`)

----------

**Notepad**


----------

I need to convert a HTML String with nested Tags like this one:
```
const strHTML = "<p>Hello World</p><p>I am a text with <strong>bold</strong> word</p><p><strong>I am bold text with nested <em>italic</em> Word.</strong></p>"
```

Into the following Array of objects with this Structure:
```
const result = [{
  text: "Hello World",
  format: null
}, {
  text: "I am a text with",
  format: null
}, {
  text: "bold",
  format: ["strong"]
}, {
  text: " word",
  format: null
}, {
  text: "I am a text with nested",
  format: ["strong"]
}, {
  text: "italic",
  format: ["strong", "em"]
}, {
  text: "Word.",
  format: ["strong"]
}];
```

I managed the conversion with the DOMParser() as long as there are no nested Tags. I am not able to get it running with nested Tags, like in the last paragraph, so my whole paragraph is bold, but the word "italic" should be both bold and italic. I cannot get it running as a recursion.
Any help would be appreciated.
So the code I wrote so far is this one:
```
export interface Phrase {
    text: string;
    format: string | string[];
}

export class HTMLParser {

    public parse(text: string): void {
        const parser = new DOMParser();
        const sourceDocument = parser.parseFromString(text, "text/html");
        this.parseChildren(sourceDocument.body.childNodes);

        // HERE SHOULD BE the result
        console.log("RESULT of CONVERSION", this.phrasesProcessed);
    }

    public phrasesProcessed: Phrase[] = [];

    private parseChildren(toParse: NodeListOf<ChildNode>) {
        this.phrasesProcessed = [];
        try {
            Array.from(toParse)
                .map(item => {
                    if (item.nodeType === Node.ELEMENT_NODE && item instanceof HTMLElement) {
                        return Array.from(item.childNodes).map(child => ({ text: child.textContent, format: (child.nodeType === Node.ELEMENT_NODE && child instanceof HTMLElement) ? child.tagName : null }));
                    } else {
                        return Array.from(item.childNodes).map(child => ({ text: child.textContent, format: null }));
                    }
                })
                .filter(line => line.length) // only non emtpy arrays
                .map(element => ([...element, { text: "\n", format: null }])) // add linebreak after each P
                .reduce((acc: (Phrase)[], val) => acc.concat(val), []) // flatten
                .forEach(
                    element => {
                        // console.log("ELEMENT", element);
                        this.phrasesProcessed.push(element);
                    }
                );
        } catch (e) {
            console.warn(e);
        }
    }

}
```



----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2022-06-08 13:32:07

------------

You can use recursion. And this seems a good case for a generator function. As it was not clear which tags should be retained in `format` (apparently, not `p`), I left this as a configuration to provide:
```
const formatTags = new Set(["b", "big", "code", "del", "em", "i", "pre", "s", "small", "strike", "strong", "sub", "sup", "u"]);

function* iterLeafNodes(nodes, format=[]) {
    for (let node of nodes) {
        if (node.nodeType == 3) {
            yield ({text: node.nodeValue, format: format.length ? [...format] : null});
        } else {
            const tag = node.tagName.toLowerCase();
            yield* iterLeafNodes(node.childNodes, 
                                 formatTags.has(tag) ? format.concat(tag) : format);
        }
    }
}

// Example input
const strHTML = "<p>Hello World</p><p>I am a text with <strong>bold</strong> word</p><p><strong>I am bold text with nested <em>italic</em> Word.</strong></p>"
const nodes = new DOMParser().parseFromString(strHTML, 'text/html').body.childNodes;
let result = [...iterLeafNodes(nodes)];

console.log(result);
```


Note that this will still split the text when it is spread over multiple tags, which are considered non-formatting tags, like `span`.
Secondly, I'm not convinced that having `null` as a possible value for `format` is more useful then just an empty array `[]`, but anyway, the above produces `null` in that case.

### Special case - insertion of \n


In comments you ask for the insertion of a line break after each `p` element.
The code below will generate that extra element. Here I also used `[]` instead of `null` for `format`:
```
const formatTags = new Set(["b", "big", "code", "del", "em", "i", "pre", "s", "small", "strike", "strong", "sub", "sup", "u"]);

function* iterLeafNodes(nodes, format=[]) {
    for (let node of nodes) {
        if (node.nodeType == 3) {
            yield ({text: node.nodeValue, format: [...format]});
        } else {
            const tag = node.tagName.toLowerCase();
            yield* iterLeafNodes(node.childNodes, 
                                 formatTags.has(tag) ? format.concat(tag) : format);
            if (tag === "p") yield ({text: "\n", format: [...format]});
        }
    }
}

// Example input
const strHTML = "<p>Hello World</p><p>I am a text with <strong>bold</strong> word</p><p><strong>I am bold text with nested <em>italic</em> Word.</strong></p>"
const nodes = new DOMParser().parseFromString(strHTML, 'text/html').body.childNodes;
let result = [...iterLeafNodes(nodes)];

console.log(result);
```




------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-06-08 13:16:17

------------

You can recursively loop over the child nodes and construct the desired array using an array like `FORMAT_NODES`.
```
const FORMAT_NODES = ["strong", "em"];

function getText(node, parents = [], res = []) {
  if (node.nodeName === "#text") {
    const text = node.textContent.trim();
    if (text) {
      const format = parents.filter((p) => FORMAT_NODES.includes(p));
      res.push({ text, format: format.length ? format : null });
    }
  } else {
    node.childNodes.forEach((node) =>
      getText(node, parents.concat(node.nodeName.toLowerCase()), res)
    );
  }
  return res;
}

const container = document.querySelector("#container");
const result = getText(container);
console.log(result);
```
```
<div id="container">
  <p>Hello World</p>
  <p>I am a text with <strong>bold</strong> word</p>
  <p><strong>I am bold text with nested <em>italic</em> Word.</strong></p>
</div>
```



- [Node.childNodes](https://developer.mozilla.org/en-US/docs/Web/API/Node/childNodes)- [Node.parentNode](https://developer.mozilla.org/en-US/docs/Web/API/Node/parentNode)- [Array.prototype.concat](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/concat)- [Array.prototype.includes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-06-08 16:16:38

------------

A version not all that different from the other two posted here, but with a different breakdown in responsibilities.
```
const getTextNodes = (node, path = []) =>
  node .nodeType === 3
    ? {text: node .nodeValue, path}
    : [... node .childNodes] .flatMap ((child) => getTextNodes (child, [... path, node .tagName .toLowerCase()]))

const extract = (keep) => (html) =>
  [...new DOMParser () .parseFromString (html, 'text/html') .body .childNodes] 
    .flatMap (node => getTextNodes (node))
    .map (({text, path = []}) => ({text, format: [...new Set (path .filter (p => keep .includes (p)))]}))

const reformat = extract (["em", "strong"])

const strHTML = "<p>Hello World</p><p>I am a text with <strong>bold</strong> word</p><p><strong>I am bold text with nested <em>italic</em> Word.</strong></p>"

console .log (reformat (strHTML))
```
```
.as-console-wrapper {max-height: 100% !important; top: 0}
```


This goes through an intermediate format, which might be useful for other purposes:
```
[
  {text: "Hello World", path: ["p"]},
  {text: "I am a text with ", path: ["p"]},
  {text: "bold", path: ["p", "strong"]},
  {text: " word", path: ["p"]},
  {text: "I am bold text with nested ", path: ["p", "strong"]},
  {text: "italic", path: ["p", "strong", "em"]},
  {text: " Word.", path: ["p", "strong"]}
]
```

While this looks similar to your final format, the `path` includes the entire tag history to the text node, and could be used for various purposes.  `getTextNodes` extracts this format from a given node.  Thus a `path` might look like `["div", "div", "div", "nav", "ol", "li", "a", "div", "div", "strong"]`, with repeated elements, and many non-formatting tags.
The final `map` call in `extract` simply filters this path into your configured collection of formatting tags.
While we can easily do this in a single pass, `getTextNodes` is itself a useful function we might use elsewhere in our system.


------------
    
    