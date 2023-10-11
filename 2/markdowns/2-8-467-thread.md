
# Post \#72799988 [Link](https://stackoverflow.com/questions/72799988/)

## Struct field tag `name` not compatible with reflect.StructTag.Get: bad syntax for struct tag pair

**Vote**: 1 (672/702) **Views**: 3184 (562/702) 

**Internal ID** \#2-8-467

Created at 2022-06-29 10:38:37

Tags: `go` `struct` `reflection` `compiler-warnings`

----------

#### Metadata:

Area: `Back-end`

Language: `go` (full parsed tag list: `go`)

----------

**Notepad**


----------

I read [this](https://stackoverflow.com/q/59959839/2441637), but it is different than my case, I've the below code:
```
package main

import (
    "bytes"
    "fmt"
    "reflect"
    "strconv"
    "strings"
)

type User struct {
    Name string `name`
    Age  int64  `age`
}

func main() {
    var u User = User{"bob", 10}

    res, err := JSONEncode(u)
    if err != nil {
        panic(err)
    }
    fmt.Println(string(res))

}

func JSONEncode(v interface{}) ([]byte, error) {
    refObjVal := reflect.ValueOf(v)
    refObjTyp := reflect.TypeOf(v)
    buf := bytes.Buffer{}
    if refObjVal.Kind() != reflect.Struct {
        return buf.Bytes(), fmt.Errorf(
            "val of kind %s is not supported",
            refObjVal.Kind(),
        )
    }
    buf.WriteString("{")
    pairs := []string{}
    for i := 0; i < refObjVal.NumField(); i++ {
        structFieldRefObj := refObjVal.Field(i)
        structFieldRefObjTyp := refObjTyp.Field(i)

        switch structFieldRefObj.Kind() {
        case reflect.String:
            strVal := structFieldRefObj.Interface().(string)
            pairs = append(pairs, `"`+string(structFieldRefObjTyp.Tag)+`":"`+strVal+`"`)
        case reflect.Int64:
            intVal := structFieldRefObj.Interface().(int64)
            pairs = append(pairs, `"`+string(structFieldRefObjTyp.Tag)+`":`+strconv.FormatInt(intVal, 10))
        default:
            return buf.Bytes(), fmt.Errorf(
                "struct field with name %s and kind %s is not supprted",
                structFieldRefObjTyp.Name,
                structFieldRefObj.Kind(),
            )
        }
    }

    buf.WriteString(strings.Join(pairs, ","))
    buf.WriteString("}")

    return buf.Bytes(), nil
}
```

It works perfectly, and give output as:
```
{"name":"bob","age":10}
```

But as VS code, it gives me the below problems:
[](https://i.stack.imgur.com/G8okK.png)
What could be the issue?


----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2022-06-29 14:17:43

------------

Note that that's just a warning telling you that you're not following convention. The code, as you already know, compiles and runs and outputs the result you want: [https://go.dev/play/p/gxcv8qPVZ6z](https://go.dev/play/p/gxcv8qPVZ6z).
To avoid the warning, disable your linter, or, better yet, follow the convention by using `key:"value"` in the struct tags and then extract the value by using the [Get](https://pkg.go.dev/reflect@go1.18.3#StructTag.Get) method: [https://go.dev/play/p/u0VTGL48TjO](https://go.dev/play/p/u0VTGL48TjO).

---


[https://pkg.go.dev/reflect@go1.18.3#StructTag](https://pkg.go.dev/reflect@go1.18.3#StructTag)
> A StructTag is the tag string in a struct field. Each key is a non-empty string
consisting of non-control characters other than space (U+0020 ' '),
quote (U+0022 '"'), and colon (U+003A ':'). Each value is quoted using
U+0022 '"' characters and Go string literal syntax.


------------
    
    
## Answer \#1

 Vote: 3

Created at 2022-06-29 10:40:56

------------

Struct tag supposed to be a `key:"value"`, `field:"name"` for example.
```
type User struct {
    Name string `field:"name"`
    Age  int64  `field:"age"`
}
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2023-02-10 07:30:09

------------

instead of field as in another answer you can use `json:"keyname"`
```
type User struct {
    Name string `json:"name"`
    Age  int64  `json:"age"`
}
```



------------
    
    