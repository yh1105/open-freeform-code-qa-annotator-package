
# Post \#62479274 [Link](https://stackoverflow.com/questions/62479274/)

## Best way to create this JSON object in Golang

**Vote**: 3 (575/702) **Views**: 11778 (398/702) 

**Internal ID** \#2-8-450

Created at 2020-06-19 22:15:08

Tags: `arrays` `json` `go` `struct`

----------

#### Metadata:

Area: `Back-end`

Language: `go` (full parsed tag list: `go` `json`)

----------

**Notepad**


----------

I'm trying to find the best way to create this JSON object using Go: 

```
{
  "Clients" : [
    {
      "Hostname" : "example.com",
      "IP" : "127.0.0.1",
      "MacAddr" : "mactonight"
    },
    {
      "Hostname" : "foo.biz",
      "IP" : "0.0.0.0",
      "MacAddr" : "12:34:56:78"
    }
  ]
}
```


In my existing code, I'm currently slicing multiple string lines and then splitting each line into 3 separate variables (host, ip, mac). For example `hostname 192.168.1.0 F0:F0:F0:F0:F0` gets converted consectively.

This is done by the following code:

```
func parselines(line string){
    for _, line := range strings.Split(line, "\n") {
        if line != "" {
            s := strings.Split(line, " ")
            host, ip, mac := s[0], s[1], s[2]
            fmt.Println("Hostname: " + host + " IP: " + ip + " MAC: " + mac)
        }
    }
}
```


So within this for loop I'd be looking to build the JSON object mentioned above. I've tried with structs but I'm really confused as to how to use them. I've done this with Ruby which took a few lines of code, but Go seems to be quite challenging (for me that is!). In ruby it's done like this:

```
require 'json'

clients = []

STDIN.each do |line|
  fields = line.split(/\s+/)
  clients << {
    Hostname: fields[0],
    IP: fields[1],
    MacAddr: fields[2]
  }
end

connections = {}
connections[:Clients] = clients
puts connections.to_json
```



----------
        
## Answer \#0

**Accepted** Vote: 7

Created at 2020-06-19 23:04:08

------------

Declare types that match the structure of the JSON document.

```
type client struct {
    Hostname string `json:"Hostname"`
    IP       string `json:"IP"`
    MacAddr  string `json:"MacAddr"`
}

type connection struct {
    Clients []*client `json:"Clients"`
}
```


Initialize values using those types and encode to JSON.

```
var clients []*client
for _, line := range strings.Split(line, "\n") {
    if line != "" {
        s := strings.Split(line, " ")
        clients = append(clients, &client{Hostname: s[0], IP: s[1], MacAddr: s[2]})
    }
}

p, _ := json.Marshal(connection{Clients: clients})
fmt.Printf("%s\n", p)
```


The JSON field tags (`json:"Hostname"`) are not needed in this example because the JSON object keys are valid [exported identifiers](https://golang.org/ref/spec#Exported_identifiers).  I include the tags here because they are often needed.

[Run the code on the Go Playground](https://play.golang.org/p/EODlt0dqgOZ).


------------
    
    
## Answer \#1

 Vote: 3

Created at 2020-06-20 03:36:54

------------

You need Initialize 2 structs

```
type Client struct {
    Hostname string
    IP string
    MacAddr string
}

type Connection struct {
    Clients []Client
}
```


And use Marshal to convert struct to Json

```
var clients []Client
clients = append(clients, Client{
    Hostname: "localhost",
    IP: "127.0.0.1",
    MacAddr: "1123:22512:25632",
})

// add more if you want ...

myJson, _ := json.Marshal(Connection{Clients:clients})
fmt.Println(string(myJson))
```


Dont forget import this

```
import "encoding/json"
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-06-19 22:27:41

------------

Create slices and maps to match the structure of the data you want.

```
var clients []interface{}
for _, line := range strings.Split(line, "\n") {
    if line != "" {
        s := strings.Split(line, " ")
        clients = append(clients, map[string]string{"Hostname": s[0], "IP": s[1], "MAC": s[2]})
    }
}
connections := map[string]interface{}{"Clients": clients}
p, _ := json.Marshal(connections)
fmt.Printf("%s\n", p)
```



------------
    
    