
# Post \#70306703 [Link](https://stackoverflow.com/questions/70306703/)

## MediatR Multiple Pipeline Behaviors

**Vote**: 3 (575/702) **Views**: 1670 (616/702) 

**Internal ID** \#2-6-392

Created at 2021-12-10 15:19:56

Tags: `c#` `.net` `mediatr`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

Helllo I already use this pattern of Mediator CQRS with Pipeline Behavior for a while, but just now i'm facing a issue where the generic implementation of TResponse and TRequest are not enough. So i'm trying to understand if having two separate pipelines for two very specific request is a bad practice or a bad ideia.
So the main idea is, having a PipelineBehavior<RequestOne, ResponseOne> to do a specific logic for the requestOne, and another PipelineBehavior<RequestTwo, ResponseTwo> to do another logic that doesn't apply to request one.


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2021-12-10 17:08:45

------------

I don't think its a bad idea at all, you can create an interface for mark all the requests you want to implement the PipelineX, and other for PipelineY.
Ex:
You have the RequestOne and RequestTwo classes that need to go through PipelineX, so you create and interface like IPipelineOne and mark the classes with it, then use reflection to process inside the pripeline middleware. The PipelineY will still run through these two classes and the rest of the program as well with no problems


------------
    
    
## Answer \#1

 Vote: 3

Created at 2022-04-05 13:57:11

------------

You can construct multiple pipelines with different behaviors.
Register pipeline behaviors:
```
// pipeline 1's behaviors
services.AddScoped(typeof(IPipelineBehavior<,>), typeof(FooBehavior<,>));
services.AddScoped(typeof(IPipelineBehavior<,>), typeof(BarBehavior<,>));
services.AddScoped(typeof(IPipelineBehavior<,>), typeof(BazBehavior<,>));

// pipeline 2's behaviors
services.AddScoped(typeof(IPipelineBehavior<,>), typeof(CatBehavior<,>));
services.AddScoped(typeof(IPipelineBehavior<,>), typeof(DogBehavior<,>));
services.AddScoped(typeof(IPipelineBehavior<,>), typeof(PigBehavior<,>));
```

Define pipelines:
```
// pipeline 1
public interface IPipeline1 { }
public class FooBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse> where TRequest : IPipeline1 { }
public class BarBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse> where TRequest : IPipeline1 { }
public class BazBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse> where TRequest : IPipeline1 { }

// pipeline 2
public interface IPipeline2 { }
public class CatBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse> where TRequest : IPipeline2 { }
public class DogBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse> where TRequest : IPipeline2 { }
public class PigBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse> where TRequest : IPipeline2 { }
```

Define requests:
```
// requests to be processed by pipeline 1
public class ARequest : IRequest, IPipeline1 { }
public class BRequest : IRequest, IPipeline1 { }
public class CRequest : IRequest, IPipeline1 { }

// requests to be processed by pipeline 2
public class XRequest : IRequest, IPipeline2 { }
public class YRequest : IRequest, IPipeline2 { }
public class ZRequest : IRequest, IPipeline2 { }
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-01-02 23:03:27

------------

Not exactly response to your question, bit if you find yourself confused with the behavior registrations as their number grows, you can consider specifying them using the library I created for that purpose [https://github.com/ITIXO/MediatR.Extensions.AttributedBehaviors](https://github.com/ITIXO/MediatR.Extensions.AttributedBehaviors)


------------
    
    