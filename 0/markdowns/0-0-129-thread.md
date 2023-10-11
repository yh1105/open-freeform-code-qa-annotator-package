
# Post \#62792477 [Link](https://stackoverflow.com/questions/62792477/)

## Can't connect to Elasticsearch with Node.Js on Kubernetes (self signed certificate in certificate chain)

**Vote**: 13 (343/702) **Views**: 8023 (465/702) 

**Internal ID** \#0-0-129

Created at 2020-07-08 10:14:52

Tags: `node.js` `typescript` `elasticsearch` `kubernetes` `microk8s`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I have a NodeJs application running inside a Kubernetes cluster (I am using [microk8s](https://microk8s.io/)). I've also followed the [official steps](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-quickstart.html) to setup Elasticsearch on Kubernetes.

# Issue


But I am unable to connect to the Elasticsearch cluster. I get this error:
```
ConnectionError: self signed certificate in certificate chain
```

This is a [code snippet](https://github.com/flolu/elasticsearch-k8s-connection/blob/e5c321d7727dc026ec1af14a7dcea44a1874d14b/search/index.ts#L4) of my connection:
```
const client = new elasticsearch.Client({
  node: process.env.elasticsearch_node,
  // https://elasticsearch-es-http.default.svc.cluster.local:9200
});
```


# Minimal reproduction


I've created a minimal reproduction of this issue here: [https://github.com/flolu/elasticsearch-k8s-connection](https://github.com/flolu/elasticsearch-k8s-connection). (Setup instructions are in the README)
Basically, everything works fine when running Elasticsearch inside Docker compose, but I can't connect when running inside Kubernetes.
The reason for this is probably because I didn't setup TLS certificates correctly, but I haven't found any information about it. 


----------
        
## Answer \#0

**Accepted** Vote: 8

Created at 2020-07-14 07:24:00

------------

The solution is to configure SSL and the Elastic user when creating the `Client`
```
const client = new elasticsearch.Client({
  node: process.env.elasticsearch_node,
  auth: {
    username: "elastic",
    password: process.env.elasticsearch_password || "changeme",
  },
  ssl: {
    ca: process.env.elasticsearch_certificate,
    rejectUnauthorized: false,
  },
});
```

The password and the certificate are provided by Elastic. They are stored inside Kubernetes secrets.
So I've just passed the password and the certificate into my NodeJs service via environment variables like this:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: search-deployment
spec:
  selector:
    matchLabels:
      app: search
  replicas: 1
  template:
    metadata:
      labels:
        app: search
    spec:
      containers:
        - name: search
          image: search:placeholder_name
          imagePullPolicy: Always
          env:
            - name: elasticsearch_node
              value: https://elasticsearch-es-http.default.svc.cluster.local:9200
            - name: elasticsearch_certificate
              valueFrom:
                secretKeyRef:
                  name: elasticsearch-es-http-ca-internal
                  key: tls.crt
            - name: elasticsearch_password
              valueFrom:
                secretKeyRef:
                  name: elasticsearch-es-elastic-user
                  key: elastic
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2021-03-25 15:44:21

------------

I'd like to build on top of @Florian Ludewig's answer on 2 points, since I struggled myself to have it work on my side.

## 1. Don't turn off rejectUnauthorized


```
const client = new elasticsearch.Client({
  node: 'node httpS url here',
  ssl: {
    ca: process.env.elasticsearch_certificate,
    rejectUnauthorized: true, // <-- this is important
  },
});
```

If you set `rejectUnauthorized` to false, the underlying nodejs https agent will bypass the certificate check. Of course if you are confident in the security of your cluster, you could disable it, but it renders the idea of providing the CA cert useless in the first place.

## 2. Make sure you provide a PEM CA cert - not the base64 encoded version


Perhaps you are providing the CA cert from your own config file without using Kubernetes' secret injection - possibly because the ES client application is in a different namespace and thus cannot access the CA secret.
In this case you may find it useful to store the CA cert as a base64 string, in a config file, but you should not forget to provide a decoded string to your client:
```
const config = loadConfigFromFile('config.yml');
const caCertificate = Buffer.from(config.base64CaCertificate, 'base64').toString();

const client = new elasticsearch.Client({
  node: 'node httpS url here',
  ssl: {
    ca: caCertificate,
    rejectUnauthorized: true
  },
});
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2020-07-14 05:33:09

------------

This is an SSL problem
Please try to disable verifying SSL or apply for an SSL from CA.
Cloudflare SSL is also a good choice .
Here's how to disable verifying SSL for NodeJs
```
process.env.NODE_TLS_REJECT_UNAUTHORIZED = '0';
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2020-07-08 12:42:10

------------

In order to resolve your issue you will need to trust the CA, you should be able to do so using the [following](https://nodejs.org/api/cli.html#cli_node_extra_ca_certs_file). Also found the following [Question here](https://stackoverflow.com/questions/29283040/how-to-add-custom-certificate-authority-ca-to-nodejs).
If you wish to import the CA as a env variable as discussed you might be able to do something like:
```
- name: NODE_EXTRA_CA_CERTS
    valueFrom:
      secretKeyRef:
        name: elasticsearch-ca
        key: tls.crt
```

Note: I've not tried the above, an alternative would be to mound the secret as a volume and import it that way :)
Please note that If you wish to disable TLS on your Elasticsearch deployment you can do so as follows:
```
spec:
  http:
    tls:
      selfSignedCertificate:
        disabled: true
```

Please note that disabling TLS isn't recommended.


------------
    
    
## Answer \#4

 Vote: 0

Created at 2021-05-06 14:33:28

------------

I ran into this error with Minikube using the elasticsearch JS client:
```
getaddrinfo ENOTFOUND <CUSTOM_NAME>-es-default-0.<CUSTOM_NAME>-es-default.default.svc {"name":"ConnectionError","meta":{"body":null,"statusCode":null,"headers":null,"meta":{"context":null,"request":{"params":{"method":"HEAD","path":"/xxx","body":null,"querystring":"","headers":{"user-agent":"elasticsearch-js/7.12.0 (linux 5.11.16-arch1-1-x64; Node.js v16.1.0)","x-elastic-client-meta":"es=7.12.0,js=16.1.0,t=7.12.0,hc=16.1.0"},"timeout":30000},"options":{},"id":2}
```

The following solution doesn't require disabling TLS verification on the client side using a self signed cert.
es-stack yaml:
```
apiVersion: elasticsearch.k8s.elastic.co/v1
kind: Elasticsearch
metadata:
  name: <CUSTOM_NAME>
  namespace: default
spec:
  version: 7.12.1
  auth:
    roles:
      - secretName: roles
    fileRealm:
      - secretName: filerealm
  http:
    tls:
      selfSignedCertificate:
        subjectAltNames:
          - ip: 127.0.0.1
```


1. Port forwarding to access the ES cluster:


```
kubectl port-forward service/<CUSTOM_NAME>-es-http 9200:9200
```


1. Settings in ES JS client:


```
const client = new Client(
     {
        node: `https://127.0.0.1:9200`,
        sniffOnStart: true,
        ConnectionPool: MyConnectionPool,
        auth: {
          username,
          password,
        },
        ssl: {
          ca: fs.readFileSync("/tmp/ca.pem"),
          rejectUnauthorized: true,
        },
      };
    )
```

I use the `ca.crt` key in `<CUSTOM_NAME>-es-http-certs-public` for `/tmp/ca.pem`. You can also get rid of this line if you set the system environment `NODE_EXTRA_CA_CERTS` to `/tmp/ca.pem` before you start node.js, like this:
```
NODE_EXTRA_CA_CERTS=/tmp/ca.pem node index.js
```

If you aren't sure what CA cert you need to refer to, just ssh into the ES container, and check the ca.crt file in`config/http-certs/`.

1. This is the most important step. Append this line to /etc/hosts on your local machine.


```
127.0.0.1   <CUSTOM_NAME>-es-default-0.<CUSTOM_NAME>-es-default.default.svc
```

You should be good to go. I came up with the idea after reading this tutorial about Ingress:
[https://kubernetes.io/docs/tasks/access-application-cluster/ingress-minikube/](https://kubernetes.io/docs/tasks/access-application-cluster/ingress-minikube/)


------------
    
    