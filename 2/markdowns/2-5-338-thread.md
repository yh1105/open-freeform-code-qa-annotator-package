
# Post \#61076443 [Link](https://stackoverflow.com/questions/61076443/)

## How to solve the error "No serializer found for class java.io.ByteArrayInputStream " when passing MultipartFile using RestTemplate?

**Vote**: 9 (409/702) **Views**: 23104 (273/702) 

**Internal ID** \#2-5-338

Created at 2020-04-07 09:06:58

Tags: `java` `post` `microservices` `multipartform-data` `resttemplate`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

I am trying to pass a spring MultipartFile from my application to a microservice, and using RestTemplate, as the following,

```
HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.MULTIPART_FORM_DATA);

    MultiValueMap<String, Object> body= new LinkedMultiValueMap<>(); 
    body.add("circularAttachment", souqBean.getCircularAttachment()); //MultipartFile 
    body.add("circularEntryId", souqBean.getCircularEntryId());
    body.add("circularTitle", souqBean.getCircularTitle());


    HttpEntity<?> entity = new HttpEntity<MultiValueMap<String, Object>>(body,headers); 


     ResponseEntity<Boolean> response =    restTemplate.postForEntity("http://localhost:8081/circular-save", entity, Boolean.class);
     status=response.getBody();
```


For more information, below is `getCircularAttachment()` method

```
public MultipartFile getCircularAttachment() {
        return circularAttachment;
    }
```


where MultipartFile is a class in Spring: `org.springframework.web.multipart.MultipartFile`

But on this line,

```
ResponseEntity<Boolean> response =    restTemplate.postForEntity("http://localhost:8081/circular-save", entity, Boolean.class);
 status=response.getBody();
```


I get the following error:

```
org.springframework.http.converter.HttpMessageNotWritableException: Could not write JSON: No serializer found for class java.io.ByteArrayInputStream and no properties discovered to create BeanSerializer (to avoid exception, disable SerializationFeature.FAIL_ON_EMPTY_BEANS) ); nested exception is com.fasterxml.jackson.databind.JsonMappingException: No serializer found for class java.io.ByteArrayInputStream and no properties discovered to create BeanSerializer (to avoid exception, disable SerializationFeature.FAIL_ON_EMPTY_BEANS) ) (through reference chain: org.springframework.web.multipart.commons.CommonsMultipartFile["fileItem"]->org.apache.commons.fileupload.disk.DiskFileItem["inputStream"])


    at org.springframework.http.converter.json.AbstractJackson2HttpMessageConverter.writeInternal(AbstractJackson2HttpMessageConverter.java:293)
    at org.springframework.http.converter.AbstractGenericHttpMessageConverter.writeInternal(AbstractGenericHttpMessageConverter.java:115)
    at org.springframework.http.converter.AbstractHttpMessageConverter.write(AbstractHttpMessageConverter.java:227)
    at org.springframework.http.converter.FormHttpMessageConverter.writePart(FormHttpMessageConverter.java:373)
    at org.springframework.http.converter.FormHttpMessageConverter.writeParts(FormHttpMessageConverter.java:353)
    at org.springframework.http.converter.FormHttpMessageConverter.writeMultipart(FormHttpMessageConverter.java:342)
    at org.springframework.http.converter.FormHttpMessageConverter.write(FormHttpMessageConverter.java:257)
    at org.springframework.http.converter.FormHttpMessageConverter.write(FormHttpMessageConverter.java:89)
    at org.springframework.web.client.RestTemplate$HttpEntityRequestCallback.doWithRequest(RestTemplate.java:897)
    at org.springframework.web.client.RestTemplate.doExecute(RestTemplate.java:658)
    at org.springframework.web.client.RestTemplate.execute(RestTemplate.java:621)
    at org.springframework.web.client.RestTemplate.postForEntity(RestTemplate.java:415)
    at ae.gov.adm.saeed.web.controller.util.CircularsControllerUtil.saveCircularView(CircularsControllerUtil.java:390)
    at ae.gov.adm.saeed.web.controller.CircularsController.saveCircular(CircularsController.java:87)
    at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    at java.lang.reflect.Method.invoke(Method.java:498)
    at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:205)
    at org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:133)
    at org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:97)
    at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:827)
    at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:738)
    at org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:85)
    at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:967)
    at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:901)
    at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:970)
    at org.springframework.web.servlet.FrameworkServlet.doPost(FrameworkServlet.java:872)
    at javax.servlet.http.HttpServlet.service(HttpServlet.java:660)
    at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:846)
    at javax.servlet.http.HttpServlet.service(HttpServlet.java:741)
    at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:231)
    at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
    at org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:53)
    at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
    at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
    at ae.gov.adm.saeed.web.security.AuthFilter.doFilter(AuthFilter.java:335)
    at ae.gov.adm.saeed.web.security.AuthFilter.doFilter(AuthFilter.java:610)
    at ae.gov.adm.common.web.filter.AbstractFilter.doFilter(AbstractFilter.java:47)
    at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
    at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
    at org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:197)
    at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107)
    at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
    at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
    at org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:199)
    at org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:96)
    at org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:502)
    at org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:140)
    at org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:81)
    at org.apache.catalina.valves.AbstractAccessLogValve.invoke(AbstractAccessLogValve.java:651)
    at org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:87)
    at org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:342)
    at org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:417)
    at org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:66)
    at org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:754)
    at org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1376)
    at org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
    at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)
    at java.lang.Thread.run(Thread.java:748)
Caused by: com.fasterxml.jackson.databind.JsonMappingException: No serializer found for class java.io.ByteArrayInputStream and no properties discovered to create BeanSerializer (to avoid exception, disable SerializationFeature.FAIL_ON_EMPTY_BEANS) ) (through reference chain: org.springframework.web.multipart.commons.CommonsMultipartFile["fileItem"]->org.apache.commons.fileupload.disk.DiskFileItem["inputStream"])
    at com.fasterxml.jackson.databind.ser.impl.UnknownSerializer.failForEmpty(UnknownSerializer.java:59)
    at com.fasterxml.jackson.databind.ser.impl.UnknownSerializer.serialize(UnknownSerializer.java:26)
    at com.fasterxml.jackson.databind.ser.BeanPropertyWriter.serializeAsField(BeanPropertyWriter.java:575)
    at com.fasterxml.jackson.databind.ser.std.BeanSerializerBase.serializeFields(BeanSerializerBase.java:663)
    at com.fasterxml.jackson.databind.ser.BeanSerializer.serialize(BeanSerializer.java:156)
    at com.fasterxml.jackson.databind.ser.BeanPropertyWriter.serializeAsField(BeanPropertyWriter.java:575)
    at com.fasterxml.jackson.databind.ser.std.BeanSerializerBase.serializeFields(BeanSerializerBase.java:663)
    at com.fasterxml.jackson.databind.ser.BeanSerializer.serialize(BeanSerializer.java:156)
    at com.fasterxml.jackson.databind.ser.DefaultSerializerProvider.serializeValue(DefaultSerializerProvider.java:129)
    at com.fasterxml.jackson.databind.ObjectWriter.writeValue(ObjectWriter.java:851)
    at org.springframework.http.converter.json.AbstractJackson2HttpMessageConverter.writeInternal(AbstractJackson2HttpMessageConverter.java:286)
    ... 61 more
```


How to solve? Thanks.


----------
        
## Answer \#0

**Accepted** Vote: 6

Created at 2020-04-07 09:31:15

------------

The signature of the service is expecting `byte[]`'s, so even if a `ByteInputStream` was serializable it would still not be the correct thing to send.
Just send those `byte[]`'s.
```
body.add("circularAttachment", 
                   new ByteArrayResource(souqBean.getCircularAttachment().getBytes()));
```



------------
    
    
## Answer \#1

 Vote: 1

Created at 2020-04-07 09:27:57

------------

Instead of adding direct multipart file, could you try adding

```
MultipartFile file  = souqBean.getCircularAttachment();


body.add("circularAttachment", new ByteArrayResource(file.getBytes()));
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-06-08 16:17:25

------------

According to above answers(in Eklavya's and Datta Diware's) it's not allowed to send our file in  type format. But it worked for me when I send the file in  type not in  type ;) It was like in  `body.add("circularAttachment", file.getBytes());` I should mention, I'm using spring boot (v2.4.5)


------------
    
    