
# Post \#53292263 [Link](https://stackoverflow.com/questions/53292263/)

## Send PathVariable @PostMapping in postman

**Vote**: 14 (336/702) **Views**: 61280 (138/702) 

**Internal ID** \#2-5-349

Created at 2018-11-14 02:19:02

Tags: `java` `spring` `spring-mvc`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I want send path variable in post mapping, in postman software.I select post mapping body and then how to do? I checked with [@RequestParam vs @PathVariable](https://stackoverflow.com/questions/13715811/requestparam-vs-pathvariable) example,all answers for get method, But I need answer for post method.

```
@RestController
@RequestMapping("api/v1/customers") 
public class CustomerController {

@PostMapping("/{code}")
    public String postRequest(@PathVariable String code,@RequestBody CustomerDTO dto){
         System.out.println(dto);
         System.out.println(code);
         return "Something";
    }
}
```



----------
        
## Answer \#0

**Accepted** Vote: 12

Created at 2018-11-14 02:50:48

------------

[](https://i.stack.imgur.com/L7Hly.png)

select post -> add the url -> select body -> choose raw -> select JSON(application/json) -> add your json data -> click send 


------------
    
    
## Answer \#1

 Vote: 34

Created at 2020-03-15 09:26:10

------------

the easy way to put a path variable in Postman is your case is "/:code"

![check link to see how to add path variables in Postman](https://i.stack.imgur.com/1GI9y.png)


------------
    
    
## Answer \#2

 Vote: 2

Created at 2018-11-14 02:27:39

------------

You can refer official documentation :

[https://www.getpostman.com/docs/v6/postman/sending_api_requests/requests](https://www.getpostman.com/docs/v6/postman/sending_api_requests/requests)

Please go to charpter URL .

Some API endpoints use path variables. You can work with those in Postman. Below is an example of a URL with a path variable:

[https://api.library.com/:entity/](https://api.library.com/:entity/)

To edit the path variable, click on Params to see it already entered as the key. Update the value as needed. For example, :entity can be “user” in this specific case. Postman also gives you suggestions to autocomplete the URL.


------------
    
    