
# Post \#63826138 [Link](https://stackoverflow.com/questions/63826138/)

## Does C# perform short circuit evaluation of if statements with await?

**Vote**: 76 (82/702) **Views**: 5161 (518/702) 

**Internal ID** \#2-6-418

Created at 2020-09-10 08:51:54

Tags: `c#` `if-statement` `async-await` `short-circuiting`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

I believe that C# stops evaluating an if statement condition as soon as it is able to tell the outcome. So for example:
```
if ( (1 < 0) && check_something_else() )
    // this will not be called
```

Since the condition `(1 < 0)` evaluates as `false`, the `&&` condition cannot be met, and `check_something_else()` will not be called.
How does C# evaluate an if statement with asynchronous functions? Does it wait for both to return? So for example:
```
if( await first_check() && await second_check() )
    // ???
```

Will this ever be short-circuited?


----------
        
## Answer \#0

**Accepted** Vote: 68

Created at 2020-09-10 08:57:04

------------

This is super simple to check.
Try this code:
```
async Task Main()
{
    if (await first_check() && await second_check())
    {
        Console.WriteLine("Here?");
    }
    Console.WriteLine("Tested");
}

Task<bool> first_check() => Task.FromResult(false);
Task<bool> second_check() { Console.WriteLine("second_check"); return Task.FromResult(true); }
```

It outputs "Tested" and nothing else.


------------
    
    
## Answer \#1

 Vote: 92

Created at 2020-09-10 08:56:56

------------

Yes, it will be short-circuited. Your code is equivalent to:
```
bool first = await first_check();
if (first)
{
    bool second = await second_check();
    if (second)
    {
        ...
    }
}
```

Note how it won't even  `second_check` until the awaitable returned by `first_check` has completed. So note that this won't execute the two checks in parallel. If you wanted to do that, you could use:
```
var t1 = first_check();
var t2 = second_check();

if (await t1 && await t2)
{
}
```

At that point:
- - - - 
If you want to execute checks in parallel, finishing as soon as  of them returns false, you'd probably want to write some general purpose code for that, collecting the tasks to start with and then using `Task.WhenAny` repeatedly. (You should also consider what you want to happen to any exceptions thrown by tasks that are effectively irrelevant to the end result due to another task returning false.)


------------
    
    
## Answer \#2

 Vote: 11

Created at 2020-09-10 08:57:16

------------

Yes it does. You can check it yourself using [sharplab.io](https://sharplab.io/#v2:EYLgxg9gTgpgtADwGwBYA+ABATARgLABQGADAAQY4CsA3IRgMzlakDCpA3oad+YxgBzkkpALIAKAJQcuPWQEsAZmIwBOIQDoAYlAgBbAEowAzgFcANgBcxFqCZhSAZA/JqMSLToPHzVhQEMzI3sJGVkw8hwVSVoCWQBfQjigA===), the following:
```
public async Task M() {
    if(await Task.FromResult(true) && await Task.FromResult(false))
        Console.WriteLine();
}
```

Is effectively transformed by the compiler into something like:
```
TaskAwaiter<bool> awaiter;

... compiler-generated state machine for first task...

bool result = awaiter.GetResult();

// second operation started and awaited only if first one returned true    
if (result)
{
     awaiter = Task.FromResult(false).GetAwaiter();
...
```

Or as a simple program:
```
Task<bool> first_check() => Task.FromResult(false);
Task<bool> second_check() => throw new Exception("Will Not Happen");

if (await first_check() && await second_check()) {}
```

Second example on [sharplab.io](https://sharplab.io/#v2:EYLgxg9gTgpgtADwGwBYA+ABATARgLABQGADAAQY4CsA3IRgMzlakDCpA3oad+YxgBzkkpALIAKAJQceM0l1ncAlgDNSYjAE5SyxVADOAFwD6YABYwwAa0mkAZLfJa9FiADsAJifNXJE+QpkKDUlaAhkAX385MJ4MJAAeYAgIABsAPm1dQy8LaykAXgy4gDoAMSgIAFsAJRg9AFcUgzFlAEMU5wlQwISk1IznSA8cnwKMg1MKgHdSVxgZgFEEMBgABwNFNzEAIgB1RRSU0gA5CANSAAlW1dWYV22uwnCgA==).


------------
    
    
## Answer \#3

 Vote: 3

Created at 2020-09-15 20:21:18

------------

Since I've been writing compilers myself, I feel qualified to offer a more logic opinion that is not merely based on some tests.
Today, most compilers turn source code into an AST (Abstract Syntax Tree), which is used to represent source code a language–independent way.
AST usually consists of syntax nodes. A syntax node that produces a value is called an expression, while one that doesn't produce anything is a statement.
Given the code in the question,
> ```
if (await first_check() && await second_check())
```

let's consider the test condition expression, that is
```
await first_check() && await second_check()
```

The produced AST for such code will be something like:
```
AndExpression:
    firstOperand = (
        AwaitExpression:
            operand = (
                MethodInvocationExpression:
                    name = "first_check"
                    parameterTypes = []
                    arguments = []
            )
    )
    secondOperand = (
        AwaitExpression:
            operand = (
                MethodInvocationExpression:
                    name = "second_check"
                    parameterTypes = []
                    arguments = []
            )
    )
```


At this point, what is to be figured out is the way that'll be interpreted. Well, I can tell most interpreters just evaluate expressions hierarchically. Therefore it will be done pretty much this way:

1. Evaluate the expression await first_check() && await second_check() Evaluate the expression await first_check() Evaluate the expression first_check() Resolve the symbol first_check Is it a reference? No (otherwise check whether it references a delegate.) Is it a method name? Yes (I don't include things like resolving nested scopes, checking if it's static or not, etc. as it's off–topic and not enough information is provided in the question to dig deeper in these details.) Evaluate arguments. There's no one. So, a parameterless method with the name first_check is to be called. Invoke a parameterless method named first_check and its result will be the value the expression first_check(). The value is expected to be a Task<T> or ValueTask<T>, since this is an await expression. The await expression is being waited for to get the value it will eventually produce. Does the first operand of the and expression produce false? Yes. Needless to evaluate the second operand. At this point, we know the value of await first_check() && await second_check() will necessarily be false as well.



The nitty–gritty of this whole thing is that C# won't care whether the expression is awaited or not — it's still the first operand of an and expression, and as such it will be evaluated first. Then, only if it will produce `true` the second operand is going to be evaluated. Otherwise, the whole and expression is assumed to be `false`, as it can't be otherwise.
This is mostly the way the vast majority of compilers, including Roslyn (the actual C# compiler, entirely written using C#), and interpreters will work, though I've hidden some implementation details that don't matter, like the way await expression are really waited for, which you can understand yourself by looking at the generated bytecode (you may use a website like [this](https://sharplab.io/). .)
Just to clarify, the way await expressions work is rather complicated and it doesn't fit in the topic of this question. It would deserve a whole, separated answer to be correctly explained, but I don't consider it as important because it's purely an implementation detail and won't make awaited expression behave anyhow differently from normal expressions.


------------
    
    