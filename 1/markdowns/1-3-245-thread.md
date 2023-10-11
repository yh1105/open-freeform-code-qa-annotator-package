
# Post \#71311507 [Link](https://stackoverflow.com/questions/71311507/)

## ModuleNotFoundError: No module named 'app' fastapi docker

**Vote**: 2 (631/702) **Views**: 3853 (547/702) 

**Internal ID** \#1-3-245

Created at 2022-03-01 16:15:40

Tags: `python` `docker` `fastapi`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

```
FROM python:3.8
WORKDIR /app 

COPY requirements.txt /
RUN pip install --requirement /requirements.txt

COPY ./app /app

EXPOSE 8000
CMD ["uvicorn", "app.main:app", "--host=0.0.0.0" , "--reload" , "--port", "8000"]
```

when i used
> 
ModuleNotFoundError: No module named 'app'
- the folders in Fastapi framework:- fastapi- app-main.py- ```
language_detector.py
```
- Dockerfile- docker-compose


----------
        
## Answer \#0

**Accepted** Vote: 7

Created at 2022-03-01 17:39:56

------------

```
CMD ["uvicorn", "main:app", "--host=0.0.0.0" , "--reload" , "--port", "8000"]
```

Your work directory is /app and the main.py file is already there. So you don't need to call app.main module. Just call main.py script directly in CMD.


------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-03-01 16:23:08

------------

Try creating the /app folder before
```
FROM python:3.8
RUN mkdir -p /app
WORKDIR /app 

COPY requirements.txt /
RUN pip install --requirement /requirements.txt

COPY ./app /app

EXPOSE 8000
CMD ["uvicorn", "app.main:app", "--host=0.0.0.0" , "--reload" , "--port", "8000"]
```

And launching it:
docker-compose up --build


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-07-14 20:51:05

------------

The issue could likely be caused by how you define the volume mounts on docker compose:
More on mounts [here](https://docs.docker.com/storage/volumes/)


------------
    
    