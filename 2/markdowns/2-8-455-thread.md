
# Post \#68543604 [Link](https://stackoverflow.com/questions/68543604/)

## Best way to parse a string to an enum

**Vote**: 4 (532/702) **Views**: 9498 (423/702) 

**Internal ID** \#2-8-455

Created at 2021-07-27 10:55:34

Tags: `go` `enums`

----------

#### Metadata:

Area: `Back-end`

Language: `go` (full parsed tag list: `go`)

----------

**Notepad**


----------

I have an enum as follows
```
type Capability int

const (
    Read   Capability = iota // Read   = 0
    Create                   // Create = 1
    Update                   // Update = 2
    Delete                   // Delete = 3
    List                     // List   = 4
)
```

I want to be able to get the string representation from the enum  parse a string to get the enum.
I get the string from the enum as follows.
```
capabilityStrs := []string{"read", "create", "update", "delete", "list"}
func (c Capability) String() string {
    return capabilityStrs[c]
}
```

How do I parse a string into an enum such that a call to `ParseSTring("read") gives me`Read`. What is the Best way to go about this?


----------
        
## Answer \#0

**Accepted** Vote: 8

Created at 2021-07-27 12:32:52

------------

Answer based on the comment by @super
```
var (
    capabilitiesMap = map[string]Capability{
        "read":   Read,
        "create": Create,
        "update": Update,
        "delete": Delete,
        "list":   List,
    }
)
func ParseString(str string) (Capability, bool) {
    c, ok := capabilitiesMap[strings.ToLower(str)]
    return c, ok
}
```



------------
    
    
## Answer \#1

 Vote: 1

Created at 2021-07-27 11:21:20

------------

Whereas what defines 'best way' is dependable like @inian says, following is one approach
```
type Capability string

const (
    Read   Capability = "READ"
    Create Capability = "CREATE"
    Update Capability = "UPDATE"
    Delete Capability = "DELETE"
    List   Capability = "LIST"
)

func (c Capability)String()string{
    return string(c)
}

func ParseCapability(s string)(c Capability,err error){
    capabilities := map[Capability]struct{}{
        Read: {},
        Create: {},
        Update: {},
        Delete: {},
        List: {},
    }

    cap := Capability(s)
    _,ok := capabilities[cap]
    if !ok{
        return c,fmt.Errorf(`cannot parse:[%s] as capability`,s)
    }
    return cap,nil
}
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2023-01-23 01:55:25

------------

This is better than the top answer since if you have a ton of enums, you don't have to write out the list twice (unlike top answer):
```
package usstates

type USState int

//go:generate stringer -type=USState
const (
    Alabama USState = iota
    Alaska
    Arizona
    Arkansas
    California
    Colorado
    Connecticut
    Delaware
    Florida
    Georgia
    Hawaii
    Idaho
    Illinois
    Indiana
    Iowa
    Kansas
    Kentucky
    Louisiana
    Maine
    Maryland
    Massachusetts
    Michigan
    Minnesota
    Mississippi
    Missouri
    Montana
    Nebraska
    Nevada
    NewHampshire
    NewJersey
    NewMexico
    NewYork
    NorthCarolina
    NorthDakota
    Ohio
    Oklahoma
    Oregon
    Pennsylvania
    RhodeIsland
    SouthCarolina
    SouthDakota
    Tennessee
    Texas
    Utah
    Vermont
    Virginia
    Washington
    WestVirginia
    Wisconsin
    Wyoming
)

var MapEnumStringToUSState = func() map[string]USState {
    m := make(map[string]USState)
    for i := Alabama; i <= Wyoming; i++ {
        m[i.String()] = i
    }
    return m
}()
```



------------
    
    