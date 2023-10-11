
# Post \#65686318 [Link](https://stackoverflow.com/questions/65686318/)

## Sharing python objects across multiple workers

**Vote**: 27 (213/702) **Views**: 17597 (325/702) 

**Internal ID** \#1-3-258

Created at 2021-01-12 14:51:10

Tags: `python` `asynchronous` `python-asyncio` `fastapi`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

We have created a service using FastAPI. When our service starts it creates a few python objects that the endpoints then use to store or retrieve data from.
FastAPI in production starts with multiple workers. Our problem is that .
The script below shows a (simplified) example of what we are doing, though in our case the usage of Meta() is considerably more complex.
```
from fastapi import FastAPI, status

class Meta:
   def __init__(self):
      self.count = 0  

app = FastAPI()

meta = Meta()

# increases the count variable in the meta object by 1
@app.get("/increment")
async def increment():
    meta.count += 1
    return status.HTTP_200_OK

# returns a json containing the current count from the meta object
@app.get("/report")
async def report():
    return {'count':meta.count}

# resets the count in the meta object to 0
@app.get("/reset")
async def reset():
    meta.count = 0
    return status.HTTP_200_OK
```

As mentioned above, the problem with multiple workers is that each one will have its own `meta` object. Please be aware that the issue is not visible when running the api with a single worker.
More explicitly, when we hit the `/increment` endpoint for the first time we will see only one of the two workers responding to the call (this is correct, we don't want both workers doing the same thing). However, because there are two separate `meta` objects, only one of the two will be incremented.  When hitting the `/report` endpoint, depending on which worker responds to the request, either 1 or 0 will be returned.
The question then is, how do we get the workers to share and operate on the same object?
As a side question, the problem above affects the `/reset` endpoint too. If this endpoint is called then only one of the workers will reset its object. Is there a way to force all workers to respond to a single call on an endpoint?
Thanks!
Edit: I forgot to mention that we have tried (with no success) to store the `meta` object in the `app.state` instead. Essentially:
```
app.state.meta = Meta()
...
@app.get("/report")
async def report():
    return {'count':app.state.meta.count}
```



----------
        
## Answer \#0

**Accepted** Vote: 28

Created at 2021-01-13 09:49:23

------------

It is not possible to share a python object between different processes straightforwardly.
The facilities included in the `multiprocessing` module (like  or ) are not suitable for sharing resources between workers, since they require a master process creating the resources and do not have the durability property. Also server processes can be run on different machines.
The  means for sharing resources between workers:
- `PostgreSQL``MariaDB``MongoDB`- `Redis``Memcached`
Below I will present two very simple examples of how one could use both approaches to share data in `FastAPI` application between workers. As an example, I took the [aiocache](https://github.com/aio-libs/aiocache) library with [Redis](https://redis.io/) as backend and [Tortoise ORM](https://tortoise-orm.readthedocs.io/en/latest/) library with [PostgreSQL](https://www.postgresql.org/docs/) as backend. Since `FastAPI` is the asynchronous framework I chose `asyncio`-based libraries.

### The structure of the test project is as follows:


```
.
├── app_cache.py
├── app_db.py
├── docker-compose.yml
├── __init__.py
```


### Docker-compose file:


For experiments, you can use the following docker-compose file exposing `5432` (Postgres) and `6379` (Redis) ports to `localhost`.
```
version: '3'

services:
  database:
    image: postgres:12-alpine
    ports:
      - "5432:5432"
    environment:
      POSTGRES_PASSWORD: test_pass
      POSTGRES_USER: test_user
      POSTGRES_DB: test_db
  redis:
    image: redis:6-alpine
    ports:
      - "6379:6379"
```

Starting:
```
docker-compose up -d
```


## Cache (aiocache)


> Aiocache provides 3 main entities:- `SimpleMemoryCache``RedisCache``aioredis``MemCache``aiomcache`- `serializers``StringSerializer``PickleSerializer``JsonSerializer``MsgPackSerializer`- 
Starting:
```
uvicorn app_cache:app --host localhost --port 8000 --workers 5
```

```
# app_cache.py
import os
from aiocache import Cache
from fastapi import FastAPI, status


app = FastAPI()
cache = Cache(Cache.REDIS, endpoint="localhost", port=6379, namespace="main")


class Meta:
    def __init__(self):
        pass

    async def get_count(self) -> int:
        return await cache.get("count", default=0)

    async def set_count(self, value: int) -> None:
        await cache.set("count", value)

    async def increment_count(self) -> None:
        await cache.increment("count", 1)


meta = Meta()


# increases the count variable in the meta object by 1
@app.post("/increment")
async def increment():
    await meta.increment_count()
    return status.HTTP_200_OK


# returns a json containing the current count from the meta object
@app.get("/report")
async def report():
    count = await meta.get_count()
    return {'count': count, "current_process_id": os.getpid()}


# resets the count in the meta object to 0
@app.post("/reset")
async def reset():
    await meta.set_count(0)
    return status.HTTP_200_OK
```


### Database (Tortoise ORM + PostgreSQL)


Starting:
For the sake of simplicity, we first run one worker to create a schema in the database:
```
uvicorn app_db:app --host localhost --port 8000 --workers 1
[Ctrl-C] 
uvicorn app_db:app --host localhost --port 8000 --workers 5
```

```
# app_db.py
from fastapi import FastAPI, status
from tortoise import Model, fields
from tortoise.contrib.fastapi import register_tortoise


class MetaModel(Model):
    count = fields.IntField(default=0)


app = FastAPI()


# increases the count variable in the meta object by 1
@app.post("/increment")
async def increment():
    meta, is_created = await MetaModel.get_or_create(id=1)
    meta.count += 1  # it's better do it in transaction
    await meta.save()
    return status.HTTP_200_OK


# returns a json containing the current count from the meta object
@app.get("/report")
async def report():
    meta, is_created = await MetaModel.get_or_create(id=1)
    return {'count': meta.count}


# resets the count in the meta object to 0
@app.post("/reset")
async def reset():
    meta, is_created = await MetaModel.get_or_create(id=1)
    meta.count = 0
    await meta.save()
    return status.HTTP_200_OK

register_tortoise(
    app,
    db_url="postgres://test_user:test_pass@localhost:5432/test_db",  # Don't expose login/pass in src, use environment variables
    modules={"models": ["app_db"]},
    generate_schemas=True,
    add_exception_handlers=True,
)
```



------------
    
    
## Answer \#1

 Vote: 12

Created at 2021-02-10 10:57:36

------------

If you run your FastAPI service using a setup with gunicorn and uvicorn as is described in the [docs](https://fastapi.tiangolo.com/deployment/docker/#tiangolouvicorn-gunicorn-fastapi) you can employ the method [described here by Yagiz Degimenci](https://stackoverflow.com/a/65708232/15182133) in a simpler way. You can use gunicorn's `--preload` setting in combination with multiprocessing.Manager in order to avoid the necessity to start another server. In particular the following does need no extra setup to make it work in a single Docker Container.
```
import logging
from multiprocessing import Manager

manager = Manager()

store = manager.dict()

store["count"] = 0

from fastapi import FastAPI

app = FastAPI()


@app.post("/increment")
async def increment():
    store["count"] = store["count"] + 1


@app.get("/count")
async def get_count():
    return store["count"]


@app.on_event("startup")
async def startup_event():
    uv_logger = logging.getLogger("uvicorn.access")
    handler = logging.StreamHandler()
    handler.setFormatter(
        logging.Formatter(
            "%(process)d - %(processName)s - %(asctime)s - %(levelname)s - %(message)s"
        )
    )
    uv_logger.addHandler(handler)
```

Save this is `demo.py` and run via (you need fastapi, guvicorn and uvicorn libraries):
```
GUNICORN_CMD_ARGS="--bind=127.0.0.1 --workers=3 --preload --access-logfile=-" gunicorn -k uvicorn.workers.UvicornWorker demo:app
```

(the `--preload` is essential here!)
Try incrementing via the OpenApi UI at http://localhost:8000/docs and compare multiple calls to the /count endpoint with the process ids in access log output to see that it returns the incremented value regardless of which worker process is responding.
 I do not make any claims about thread / async safety here and this method should probably not be employed in production services. In case of any doubt you should always rely on a proper database / caching / memory store solution for production setups. I myself only use this in demo code!


------------
    
    
## Answer \#2

 Vote: 10

Created at 2021-01-13 18:58:16

------------

You can create architecture without needing any external library or adding any extra complexity with databases etc.
This will be our server for sharing objects across different processes.
```
from multiprocessing.managers import SyncManager


class MyManager(SyncManager):
    pass

syncdict = {}

def get_dict():
    return syncdict

if __name__ == "__main__":
    MyManager.register("syncdict", get_dict)
    manager = MyManager(("127.0.0.1", 5000), authkey=b"password")
    manager.start()
    input()
    manager.shutdown()
```

Name this file as `server.py` and run this on a different process. Just `python server.py` should do good.

## Let's skip to our client implementation.


This will be our client implementation.
```
from multiprocessing.managers import SyncManager
from typing import Optional, Dict, Any, Union


class MyManager(SyncManager):
    ...


class Meta:
    def __init__(self, *, port: int) -> None:
        self.manager = MyManager(("127.0.0.1", port), authkey=b"password")
        self.manager.connect()
        MyManager.register("syncdict")

        self.syndict = self.manager.syncdict()

    def update(self, kwargs: Dict[Any, Any]) -> None:
        self.syndict.update(kwargs)

    def increase_one(self, key: str) -> None:
        self.syndict.update([(key, self.syndict.get(key) + 1)])

    def report(self, item: Union[str, int]) -> int:
        return self.syndict.get(item)


meta = Meta(port=5000)
```


## Let's merge it with our API.


```
from fastapi import FastAPI, status

from multiprocessing.managers import SyncManager
from typing import Optional, Dict, Any, Union


class MyManager(SyncManager):
    ...


class Meta:
    def __init__(self, *, port: int, **kwargs: Dict[Any, Any]):
        self.manager = MyManager(("127.0.0.1", port), authkey=b"password")
        self.manager.connect()
        MyManager.register("syncdict")

        self.syndict = self.manager.syncdict()
        self.syndict.update(**kwargs)

    def increase_one(self, key: str):
        self.syndict.update([(key, self.syndict.get(key) + 1)])

    def reset(self, key: str):
        self.syndict.update([(key, 0)])

    def report(self, item: Union[str, int]):
        return self.syndict.get(item)


app = FastAPI()

meta = Meta(port=5000, cnt=0)

# increases the count variable in the meta object by 1
@app.get("/increment")
async def increment(key: str):
    meta.increase_one(key)
    return status.HTTP_200_OK


# returns a json containing the current count from the meta object
@app.get("/report")
async def report(key: str):
    return {"count": meta.report(key)}


# resets the count in the meta object to 0
@app.get("/reset")
async def reset(key: str):
    meta.reset(key)
    return status.HTTP_200_OK
```

I'm gonna start two instances of our API, one will be on 8000, the other on 8001.
```
In: curl -X GET "http://127.0.0.1:8000/report?key=cnt"
Out: {"count": 0}

In: curl -X GET "http://127.0.0.1:8001/report?key=cnt"
Out: {"count": 0}
```

Both started with 0 value. Now let's increment it
```
for _ in {1..10}; do curl -X GET "http://127.0.0.1:8000/increment?key=cnt" &; done
```

I ran the curl on port `8000`, 10 times which means `cnt` should be 10.
Let's check it out from port `8001`:
```
In: curl -X GET "http://127.0.0.1:8001/report?key=cnt" 
Out: {"cnt": 10}
```

Working like a charm.
There are two things the consider.

1. You should start your app in a different process. More specifically, the uvicorn my_app:app and your server shouldn't be parent processes.
2. You might want to add something like graceful shutdowns. Since this is a very simple but highly extensible example.




------------
    
    
## Answer \#3

 Vote: 4

Created at 2021-01-13 08:24:08

------------


# Question 1


> The question then is, how do we get the workers to share and operate on the same object?

## TL;DR


While you could share objects via something like `multiprocessing`, in your use case you're probably , like Redis.

## Explanation


I'm no expert at all in parallel/concurrent applications, but I do know that unless you need to speed up  (i.e. very complex and/or long runnning calculations), you  want to share objects between processes.
You CAN do that, via dedicated libraries and modules, however it will make your app , having to handle all the possible race conditions and edge cases intrinsic with parallelism. If you do want to go that route, I'm sure there are plenty of libraries and tools, but you should first take a look at [multiprocessing](https://docs.python.org/3/library/multiprocessing.html), the standard python library for handling parallelism. Check also [this](https://stackoverflow.com/questions/18213619/sharing-a-lock-between-gunicorn-workers) and [this](https://stackoverflow.com/questions/27240278/sharing-memory-in-gunicorn) about using it to share resources among workers with `gunicorn`.
On the other hand, your use case doesn't look like it requires very complex calculations, so I would suggest to use a simple cache to act as the "data hub" for your workers, instead of a class. It will give you the desired result of having a single source of truth for your processes without the complexities of shared memory.
If you want to give this approach a try, I suggest to give a look at [Redis](https://redis.io/) which is a very popular and well supported solution for a cache and can even persist the data if you want.
Here's a list of [Redis clients for python](https://redis.io/clients#python). [redis-py](https://github.com/andymccurdy/redis-py) is the recommended one.

---



# Question 2


> As a side question, the problem above affects the /reset endpoint too. If this endpoint is called then only one of the workers will reset its object. Is there a way to force all workers to respond to a single call on an endpoint?
If you use a cache the problem disappears. You only have a single source of truth and you just erase the data there, with whichever worker responds to the request. Then every worker will see the data has been reset.


------------
    
    
## Answer \#4

 Vote: 2

Created at 2022-06-25 09:02:38

------------

There is a lib called [UltraDict](https://github.com/ronny-rentner/UltraDict):
> UltraDict uses multiprocessing.shared_memory to synchronize a dict between multiple processes.It does so by using a stream of updates in a shared memory buffer. This is efficient because only changes have to be serialized and transferred.
You could just `pip install UltraDict`
Main advantage is that you don't have to run two scripts and have less code overwall than in answers with `multiprocessing.Manager`
Example solution could look like this:
```
from multiprocessing.managers import SyncManager
from typing import Any, Dict, Optional, Union

import uvicorn

from fastapi import FastAPI, status
from UltraDict import UltraDict

class Meta:
    def __init__(self, **kwargs: Dict[Any, Any]):
        self.ultradict = UltraDict(name='fastapi_dict')
        self.ultradict.update(**kwargs)

    def increase_one(self, key: str):
        self.ultradict.update([(key, self.ultradict.get(key) + 1)])

    def reset(self, key: str):
        self.ultradict.update([(key, 0)])

    def report(self, item: Union[str, int]):
        return self.ultradict.get(item)


app = FastAPI()
meta = Meta(cnt=0)


# increases the count variable in the meta object by 1
@app.get('/increment')
async def increment(key: str):
    meta.increase_one(key)
    return status.HTTP_200_OK

# returns a json containing the current count from the meta object
@app.get('/report')
async def report(key: str):
    return {'count': meta.report(key)}

# resets the count in the meta object to 0
@app.get('/reset')
async def reset(key: str):
    meta.reset(key)
    return status.HTTP_200_OK
```

Let's run it and verify if it's working:
```
uvicorn main:app --workers 5

curl -X GET "http://127.0.0.1:8000/report?key=cnt"
#> {"count":0}

$ for _ in {1..10} ; do curl -X GET "http://127.0.0.1:8000/increment?key=cnt" & done
#> Big bash stuff

$ curl -X GET "http://127.0.0.1:8000/report?key=cnt"
#> {"count":10}
```

Also let's check fastapi logs:
```
INFO:     Application startup complete.
INFO:     127.0.0.1:19122 - "GET /report?key=cnt HTTP/1.1" 200 OK
INFO:     127.0.0.1:19123 - "GET /increment?key=cnt HTTP/1.1" 200 OK
INFO:     127.0.0.1:19124 - "GET /increment?key=cnt HTTP/1.1" 200 OK
INFO:     127.0.0.1:19125 - "GET /increment?key=cnt HTTP/1.1" 200 OK
INFO:     127.0.0.1:19126 - "GET /increment?key=cnt HTTP/1.1" 200 OK
INFO:     127.0.0.1:19127 - "GET /increment?key=cnt HTTP/1.1" 200 OK
INFO:     127.0.0.1:19128 - "GET /increment?key=cnt HTTP/1.1" 200 OK
INFO:     127.0.0.1:19129 - "GET /increment?key=cnt HTTP/1.1" 200 OK
INFO:     127.0.0.1:19130 - "GET /increment?key=cnt HTTP/1.1" 200 OK
INFO:     127.0.0.1:19131 - "GET /increment?key=cnt HTTP/1.1" 200 OK
INFO:     127.0.0.1:19132 - "GET /increment?key=cnt HTTP/1.1" 200 OK
INFO:     127.0.0.1:19144 - "GET /report?key=cnt HTTP/1.1" 200 OK
```

You could see that diffirent workers are used (look at ports), and everything works as expected


------------
    
    
## Answer \#5

 Vote: 1

Created at 2022-08-11 01:33:20

------------

I had this exact problem and I was able to share memory across workers by using sqlite. I created a KeyValue store that uses the same api as a `dict`. People say that this is simpler than installing and running a Redis process.
`pip install keyvalue-sqlite`
You can use it like so:
```
from keyvalue_sqlite import KeyValueSqlite

DB_PATH = '/path/to/db.sqlite'

db = KeyValueSqlite(DB_PATH, 'table-name')
# Now use standard dictionary operators
db.set_default('0', '1')
actual_value = db.get('0')
assert '1' == actual_value
db.set_default('0', '2')
assert '1' == db.get('0')
```

You can store any kind of json structure in the database.
More api docs can be found here:
[https://github.com/zackees/keyvalue_sqlite](https://github.com/zackees/keyvalue_sqlite)


------------
    
    