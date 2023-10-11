
# Post \#70012970 [Link](https://stackoverflow.com/questions/70012970/)

## running a vite dev server inside a docker container

**Vote**: 14 (336/702) **Views**: 17457 (327/702) 

**Internal ID** \#0-0-56

Created at 2021-11-17 23:52:37

Tags: `docker` `vue.js` `vite`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I have a Vue-cli app that I'm trying to convert to vite. I am using Docker to run the server. I looked at a couple tutorials and got vite to run in development mode without errors. However, the browser can't access the port. That is, when I'm on my macbook's command line (outside of Docker) I can't `curl` it:
```
$ curl localhost:8080
curl: (52) Empty reply from server
```

If I try localhost:8081 I get `Failed to connect`. In addition, if I run the webpack dev server it works normally so I know that my container's port is exposed.
Also, if I run curl in the same virtual machine that is running the vite server it works, so I know that vite is working.
Here are the details:
In package.json:
```
...
"dev": "vue-cli-service serve",
"vite": "vite",
...
```

The entire vite.config.ts file:
```
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

// https://vitejs.dev/config/
export default defineConfig({
    resolve: { alias: { '@': '/src' } },
    plugins: [vue()],
    server: {
        port: 8080
    }
})
```

The command that starts the container:
```
docker-compose run --publish 8080:8080 --rm app bash
```

The docker-compose.yml file:
```
version: '3.7'

services:
  app:
    image: myapp
    build: .
    container_name: myapp
    ports:
      - "8080:8080"
```

The Dockerfile:
```
FROM node:16.10.0

RUN npm install -g npm@8.1.3
RUN npm install -g @vue/cli@4.5.15

RUN mkdir /srv/app && chown node:node /srv/app

USER node

WORKDIR /srv/app
```

The command that I run inside the docker container for vite:
```
npm run vite
```

The command that I run inside the docker container for vue-cli:
```
npm run dev
```

So, to summarize: my setup works when running the vue-cli dev server but doesn't work when using the vite dev server.


----------
        
## Answer \#0

**Accepted** Vote: 23

Created at 2021-11-22 15:54:10

------------

I figured it out. I needed to add a "host" attribute in the config, so now my vite.config.ts file is:
```
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

// https://vitejs.dev/config/
export default defineConfig({
    resolve: { alias: { '@': '/src' } },
    plugins: [vue()],
    server: {
        host: true,
        port: 8080
    }
})
```



------------
    
    
## Answer \#1

 Vote: 10

Created at 2021-12-28 23:42:44

------------

You can also start your vite server with:
```
$ npm run dev -- --host
```

This passes the --host flag to the vite command line.
You will see output like:
```
vite v2.7.9 dev server running at:

  > Local:    http://localhost:3000/
  > Network:  http://192.168.4.68:3000/

  ready in 237ms.
```

(I'm running a VirtualBox VM - but I think this applies here as well.)


------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-04-05 15:47:32

------------

You need to add host `0.0.0.0` to allow any external access:
```
export default defineConfig({
server: {
    host: '0.0.0.0',
    watch: {
        usePolling: true
    }
},})
```



------------
    
    