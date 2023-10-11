
# Post \#62055988 [Link](https://stackoverflow.com/questions/62055988/)

## Golang: A map Interface, how to print key and value

**Vote**: 9 (409/702) **Views**: 46999 (172/702) 

**Internal ID** \#2-8-460

Created at 2020-05-28 02:29:14

Tags: `go` `interface`

----------

#### Metadata:

Area: `Back-end`

Language: `go` (full parsed tag list: `go`)

----------

**Notepad**


----------

I get a map interface, like :

```
getUsersAppInfo := usersAppInfo.GetUsersAppInfo(getUserId)
```


then I print :

```
fmt.Println(getUsersAppInfo)
```


get this, like :

```
[map[appId:2 fcmServerKey:keyTestTest name:com.app]]
```


Ask : How to just print the value, like

```
appId value is 2 
name value is com.app 
fcmServerKey:keyTestTest value is keyTestTest
```



----------
        
## Answer \#0

**Accepted** Vote: 16

Created at 2020-05-28 03:15:02

------------

The OP's comment on the question states that type of `getUsersAppInfo` is `[]map[string]interface{}`.

Loop over the slice of maps.  For each map, loop over the keys and values and print.

```
// loop over elements of slice
for _, m := range getUsersAppInfo {

    // m is a map[string]interface.
    // loop over keys and values in the map.
    for k, v := range m {
        fmt.Println(k, "value is", v)
    }
}
```


[Run this on the GoLang PlayGround](https://play.golang.org/p/H25g2nCsPQ-).


------------
    
    
## Answer \#1

 Vote: 10

Created at 2020-05-29 06:10:10

------------

I don't recommend to do this in production setup. But when I want to print out a map without too much code on my dev box, i print the JSON serialised version. This will be a crime to do in production. 

```
package main

import (
    "encoding/json"
    "fmt"
)

func main() {
    a := map[string]interface{}{"appId": 2, "fcmServerKey": "keyTestTest", "name": "com.app", "version": []int{1, 2, 3}, "xyz": 3}
    bs, _ := json.Marshal(a)
    fmt.Println(string(bs))
}
```


Output:

`{"appId":2,"fcmServerKey":"keyTestTest","name":"com.app","version":[1,2,3],"xyz":3}`


------------
    
    
## Answer \#2

 Vote: 4

Created at 2020-05-28 02:38:51

------------

I'm afraid the only option is to iterate through the map:

```
getUsersAppInfo := map[string]interface{}{"foo": 3, "bar": "baz"}
for key, value := range getUsersAppInfo {
    fmt.Printf("%s value is %v\n", key, value)
}
```



------------
    
    