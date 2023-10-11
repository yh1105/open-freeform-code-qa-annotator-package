
# Post \#67993633 [Link](https://stackoverflow.com/questions/67993633/)

## How to fix this in error rails "warning: 299 Elasticsearch built-in security features are not enabled. Without authentication, your cluster could..."

**Vote**: 28 (207/702) **Views**: 18516 (315/702) 

**Internal ID** \#2-9-489

Created at 2021-06-15 21:19:36

Tags: `ruby-on-rails` `ruby` `elasticsearch` `homebrew` `searchkick`

----------

#### Metadata:

Area: `Back-end`

Language: `ruby` (full parsed tag list: `ruby`)

----------

**Notepad**


----------

I have a Rails app that uses Searchkick and after updating my gems and yarn, I'm getting this Elasticsearch warning"
```
warning: 299 Elasticsearch-7.13.1-9a7758028e4ea59bcab41c12004603c5a7dd84a9 "Elasticsearch built-in security features are not enabled. Without authentication, your cluster could be accessible to anyone. See https://www.elastic.co/guide/en/elasticsearch/reference/7.13/security-minimal-setup.html to enable security."
```

I tried following the instructions at the [webpage mentioned](https://www.elastic.co/guide/en/elasticsearch/reference/7.13/security-minimal-setup.html), but I'm getting stuck at the `run the elasticsearch-setup-passwords utility` step. I can't find this script anywhere.
I'm currently on an M1 Mac, but running terminal with Rosetta, so homebrew was installed with Rosetta.
This is just for my development machine, so it doesn't seem like security is that important and previous versions of Elasticsearch installed with Homebrew did not display this warning.
Here's what I'm running now:
- - - `brew install Elasticsearch``brew services start elasticsearch`- 
Is there a way to ignore this message? Or how do I fix this? It's so large in my console that I can't read my tests.


----------
        
## Answer \#0

**Accepted** Vote: 51

Created at 2021-06-19 21:18:37

------------

This warning is shown because you have the security plugin enabled but not configurated.
If you don't want security you can disable xpack and it should do the trick
simply add this line in your configuration `elasticsearch.yml`  :
```
xpack.security.enabled: false
```

UPDATE: to check the location of the `elasticsearch.yml` file in your Mac, run `brew info elasticsearch` and it will be located inside the config folder displayed.


------------
    
    
## Answer \#1

 Vote: 9

Created at 2021-12-20 12:28:15

------------

Adding to @luc-e answer. If you have Elastic installed as Docker container, then you should do the following:
```
docker exec -it <container_id> bash
cd /usr/share/elasticsearch/config
echo "xpack.security.enabled: false" >> elasticsearch.yml
```

and restart your container


------------
    
    
## Answer \#2

 Vote: 5

Created at 2022-06-28 15:14:55

------------

For development with docker where you don't care about security, you can disable this warning via environment variables:
`docker-compose.yml`
```
version: '3'

services:
  elasticsearch:
    image: blacktop/elasticsearch:7.17
    environment:
      - xpack.security.enabled=false
```



------------
    
    