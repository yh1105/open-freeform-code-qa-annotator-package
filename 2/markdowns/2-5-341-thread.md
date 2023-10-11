
# Post \#63832966 [Link](https://stackoverflow.com/questions/63832966/)

## HttpMessageNotWritableException: No converter for [...] with preset Content-Type 'null'] with OpenApi Spring generator

**Vote**: 7 (449/702) **Views**: 31745 (224/702) 

**Internal ID** \#2-5-341

Created at 2020-09-10 15:28:21

Tags: `spring` `spring-boot` `openapi-generator`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I am implementing the petstore API with openAPI in a (gradle) spring boot project. I generate a server with the openapi generator plugin and implement a simple request:
```
@Service
@RestController
public class PetApiController implements PetApi {

    @Override
    public ResponseEntity<Pet> getPetById(Long petId) {
        Pet p = new Pet();
        p.setId(0L);
        p.setName("fido");
        p.setPhotoUrls(new ArrayList<String>());
        p.setStatus(StatusEnum.AVAILABLE);
        p.setTags(new ArrayList<Tag>());
        Category c = new Category();
        c.setId(3L);
        c.setName("dummy category");
        p.setCategory(c);
        
        ResponseEntity<Pet> r = new ResponseEntity<Pet>(p, HttpStatus.OK);
        
        return r;
    }
}
```

The swagger-ui that I generate offers both xml and json queries for a request, but xml doesn't seem to work:
```
$ curl -X GET "http://localhost:8080/pet/1" -H  "accept: application/xml"; echo ""

$ curl -X GET "http://localhost:8080/pet/1" -H  "accept: application/json"; echo ""
{"id":0,"name":"fido","category":{"id":3,"name":"dummy category"},"photoUrls":[],"tags":[],"status":"available"}
```

I even get an error message:
```
2020-09-10 09:04:34.213  WARN 23958 --- [nio-8080-exec-1] .w.s.m.s.DefaultHandlerExceptionResolver : Resolved [org.springframework.http.converter.HttpMessageNotWritableException: No converter for [class com.petstore.server.model.Pet] with preset Content-Type 'null']
```

However I cannot catch this exception and print the whole context, it only happens after `return r`
There is already a question with my exact error message:
[Springboot HttpMessageNotWritableException: No converter for [...] with preset Content-Type 'null']](https://stackoverflow.com/q/60502811/3014199) But I still think my context is different since I generate my POJOs so I have no controll over them and I shouldn't have this problem in the first place since the plugin should generate everything fittingly.


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2020-09-10 15:28:21

------------

The spring generator discriminates against xml by default, see the documentation: [https://openapi-generator.tech/docs/generators/spring](https://openapi-generator.tech/docs/generators/spring) under
```
Option  | Description                                       | Default
--------+---------------------------------------------------+---------
withXml | whether to include support for application/xml    | false
        | content type and include XML annotations in the   |
        | model (works with libraries that provide support  |
        | for JSON and XML)                                 |
```



------------
    
    
## Answer \#1

 Vote: 6

Created at 2020-11-09 16:43:49

------------

for me a problem was in lines:
```
responses:
        "200":
          description: default response
          content:
            '*/*' # <---- here
              schema:
```

instead of `'*/*'` it should be `'application/json'`


------------
    
    
## Answer \#2

 Vote: 3

Created at 2021-05-23 11:28:54

------------

You need to add support for xml format using the below dependency in your pom file:
```
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-xml</artifactId>
    <version>2.1.1</version>
</dependency>
```

Note : here version is the latest as per today.


------------
    
    
## Answer \#3

 Vote: 2

Created at 2022-04-19 11:07:24

------------

In my case, my class `Pet` didn't have a getter at all.
So I fixed the problem just by adding get methods for fields.


------------
    
    
## Answer \#4

 Vote: 0

Created at 2021-06-20 04:56:03

------------

According awnser by Peer, i have resolve my issue but it is different way. You need add "text/xml" to tell spring. Even though this limits your API to being JSON-based (which is fine for your needs), you’re welcome to set produces to an array of String for multiple content types. For example, to allow for XML output, you could add “text/html” to the produces attribute:



------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-09-26 05:24:44

------------

People with similar errors and most things correct, try to add this header in the API request! I faced this too
`Accept : application/json` (Or `application/xml` whichever media your response is producing)


------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-10-04 05:26:23

------------

For my case, I was getting 
This was in file download api while converting file object into Java Object class.
Issue I fixed by converting java.io.File object into byte array like below:
```
@GetMapping(value="/download/{migrationType}",produces=MediaType.APPLICATION_OCTET_STREAM_VALUE)

    return ResponseEntity.ok()
                        .body(Files.readAllBytes(Paths.get(filePath)));
```

There is no need to put any external jar in spring boot based project. HttpMessageConverter is a part of spring-boot-starter-web dependency. org.springframework.http.converter.ByteArrayHttpMessageConverter is responsible to convert Byte array to APPLICATION_OCTET_STREAM_VALUE media type.


------------
    
    
## Answer \#7

 Vote: 0

Created at 2022-11-14 18:14:30

------------

I got the same error for the below piece of code.
```
@GetMapping(value = "/sample", produces = MediaType.APPLICATION_JSON_VALUE)
public ResponseEntity<Object> sample() {

    return ResponseEntity.ok(new Object());
}
```


> No converter for [class java.lang.Object] with preset Content-Type
'null'
To fix it, I replaced the `new Object()` with `new ArrayList()`


------------
    
    