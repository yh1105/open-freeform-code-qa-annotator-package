
# Post \#72828521 [Link](https://stackoverflow.com/questions/72828521/)

## Implementing both IDisposable and IAsyncDisposable

**Vote**: 4 (532/702) **Views**: 849 (670/702) 

**Internal ID** \#2-6-411

Created at 2022-07-01 11:22:46

Tags: `c#` `dispose` `idisposable` `iasyncdisposable`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

Say I have a non-sealed class that does not deal with any unmanaged resources. I need to make a single async call during its disposing stage to do some clean up. There are no other managed resources to deal with.
From what I understand, in order to make the async clean up call, I must implement IAsyncDisposable and use the DisposeAsync() and DisposeAsyncCore() methods. But the guidance says that you should also implement the dispose pattern when you implement the async dispose pattern. This is all fine but there's nothing really I need to do in the Dispose().
So my question is, should the Dispose() logic be empty or do I need something to do the async cleanup in a synchronous way? (see comment in code about "What if anything should go here").
```
public class MyClass : IDisposable, IAsyncDisposable
{
    private bool disposed;

    public void Dispose()
    {
        Dispose(true);
        GC.SuppressFinalize(this);
    }

    public async ValueTask DisposeAsync()
    {
        await DisposeAsyncCore().ConfigureAwait(false);

        Dispose(false);
        GC.SuppressFinalize(this);
    }

    protected virtual void Dispose(bool disposing)
    {
        if (!disposed)
        {
            if (disposing)
            {
                // What if anything should go here?
            }

            disposed = true;
        }
    }

    protected virtual async ValueTask DisposeAsyncCore()
    {
        // Make async cleanup call here e.g. Database.CleanupAsync();
    }
}
```



----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2022-07-04 15:20:47

------------


```
internal class Program
{
    static void Main(string[] args)
    {
        foreach (var a in new B()){}
        //IAsyncDisposable is not called - you leaking resources. 
        //No deadlocks in UI, no warning in compilation, nothing.
        //So it is better to be on safe side and implement both
        //because you never know how one will manage lifetime of your class.
    }

    public class B : IEnumerable, IAsyncEnumerable<object>
    {
        public IEnumerator GetEnumerator() => new A();
        public IAsyncEnumerator<object> GetAsyncEnumerator(CancellationToken ct) => new A();
    }

    public class A : IAsyncEnumerator<object>, IEnumerator
    {
        public ValueTask DisposeAsync()
        {
            Console.WriteLine("Async Disposed");
            return ValueTask.CompletedTask;
        }

        public bool MoveNext() => false;
        public void Reset(){}
        public ValueTask<bool> MoveNextAsync() => ValueTask.FromResult(false);

        public object Current => null;
    }
}
```


You can freely add support for async version only, but beware: some wraps, like `foreach` or older versions of DI containers (, , etc), code generators like , or proxy generators like  might not support `IAsyncDisposable`. Failing to cast object to `IDisposable` will present hard to catch bugs in your app. Whereas if you do implement it, the worst thing that could happen is deadlock in finally block (if you do it through sync-over-async).
In general,  if you plan to make it public API or you don't have control over your class lifetime (like in DI containers or other widely known wrappers).

There is full Microsoft example on how to implement both of them in inheritable class (non-sealed, like in your example) - [https://learn.microsoft.com/en-us/dotnet/standard/garbage-collection/implementing-disposeasync#implement-both-dispose-and-async-dispose-patterns](https://learn.microsoft.com/en-us/dotnet/standard/garbage-collection/implementing-disposeasync#implement-both-dispose-and-async-dispose-patterns)
```
class ExampleConjunctiveDisposableusing : IDisposable, IAsyncDisposable
{
    IDisposable? _disposableResource = new MemoryStream();
    IAsyncDisposable? _asyncDisposableResource = new MemoryStream();

    public void Dispose()
    {
        Dispose(disposing: true);
        GC.SuppressFinalize(this);
    }

    public async ValueTask DisposeAsync()
    {
        await DisposeAsyncCore().ConfigureAwait(false);

        Dispose(disposing: false);
#pragma warning disable CA1816 // Dispose methods should call SuppressFinalize
        GC.SuppressFinalize(this);
#pragma warning restore CA1816 // Dispose methods should call SuppressFinalize
    }

    protected virtual void Dispose(bool disposing)
    {
        if (disposing)
        {
            _disposableResource?.Dispose();
            (_asyncDisposableResource as IDisposable)?.Dispose();
            _disposableResource = null;
            _asyncDisposableResource = null;
        }
    }
    protected virtual async ValueTask DisposeAsyncCore()
    {
        if (_asyncDisposableResource is not null)
        {
            await _asyncDisposableResource.DisposeAsync().ConfigureAwait(false);
        }

        if (_disposableResource is IAsyncDisposable disposable)
        {
            await disposable.DisposeAsync().ConfigureAwait(false);
        }
        else
        {
            _disposableResource?.Dispose();
        }

        _asyncDisposableResource = null;
        _disposableResource = null;
    }
}
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2022-07-04 09:51:19

------------

Both implementations of dispose feature is from the callers point of view. Your class would then offer both mechanisms to dispose off any managed and unmanaged resources and the caller application decides what to choose. This also ensures that any consumer which is unable to make use of asynchronous patterns are not lost.
You do not really need to implement synchronous dispose if you are sure about or want to force asynchronous consumption of your class.
So depending on your vision of class usage, you can choose how to dispose objects. If you choose to keep both mechanisms, you can dispose all resources both ways.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-07-06 09:02:18

------------

As you have said, the class is non-sealed. For sealed classes, it's enough to implement `I(Async)Disposable` interface. The `Disposable` pattern exists because the derived class may want to add cleanup logic that can be either sync or async. You can't know. That's why you need to implement the whole pattern for sync and async cases.
For your question. Never block async call in sync `Dispose` method. It's a caller's responsibility to use your class correctly. If he decides to call `Dispose` instead of `DisposeAsync` and clear only sync resources it's his decision/mistake. If this async call in `DisposeAsync` is absolutely necessary for proper cleanup and it is controlled by you, consider adding sync equivalent to be used in `Dispose` method.


------------
    
    