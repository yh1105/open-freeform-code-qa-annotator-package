
# Post \#70864286 [Link](https://stackoverflow.com/questions/70864286/)

## Should static files be included in a Docker Image?

**Vote**: 0 (696/702) **Views**: 1163 (649/702) 

**Internal ID** \#2-6-391

Created at 2022-01-26 13:36:29

Tags: `c#` `.net` `docker` `image` `dockerfile`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

I have a .NET microservice running as a docker container that is responsible for sending emails to customers. These emails contains additional banner which is a png file.
Should that file be included in my Docker Image during build process? Is it a good practise at all?
Is it a better approach to upload the file to an already built container or linked the file stored in different place?
Dockerfile
```
FROM mcr.microsoft.com/dotnet/aspnet:5.0-buster-slim AS base

WORKDIR /app

EXPOSE 80

FROM mcr.microsoft.com/dotnet/sdk:5.0-buster-slim AS build

WORKDIR /src

COPY src/Email.Service/Email.Service.csproj .
COPY nuget.config .

RUN dotnet restore Email.Service.csproj --configfile nuget.config

COPY src/Email.Service .

RUN dotnet build Email.Service.csproj -c Release -o /app

FROM build AS publish

RUN dotnet publish Email.Service.csproj -c Release -o /app

FROM base AS final

WORKDIR /app

COPY --from=publish /app .

COPY src/Email.Service/Assets Assets

ENTRYPOINT [ "dotnet" ]
CMD [ "Email.Service.dll" ]
```

banner.png lives in Assets directory in the project.


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2022-01-26 14:49:19

------------

> Should that file be included in my Docker Image during build process?
Yes.  This makes the image be self-contained, and you can just run it, without needing the source tree or any external artifacts.
(...but also see the "file stored in a different place" option.)
> Is it a better approach to upload the file to an already built container
No, this is not a best practice.  It's very routine to delete and recreate containers (and in environments like Kubernetes sometimes it happens outside your control) and needing additional manual setup steps to make a container functional risks things breaking because you forgot the step.
> or linked the file stored in different place?
For production-oriented deployments, this can actually be a good strategy.  Consider a Javascript application built with Webpack or a similar tool, and compiled to static files.  The "normal Docker way" would be to use a multi-stage build that first built the application and then `COPY` those files into a minimal Nginx image.  Webpack has some features that allow serving old versions of an application, so the previous build still works even if the end user hasn't reloaded their page with a link to newer assets; this "normal Docker way" doesn't actually work with that.
So there's actually a very valid path to take static assets like this and upload them to a normal HTTP file-serving service.  (I'm used to Amazon's offerings and I'd use AWS S3 for this but I believe other public clouds have similar offerings, and really anything where you can upload files and HTTP GET them will work.)  You'd use a totally normal not-necessarily-Docker frontend build pipeline for this.  In your application code, you'd either proxy to the file-serving service yourself or directly send a URL to it.
As I say, this is a little more production-oriented, where you are thinking about concerns like "is the script URL from yesterday's build still valid today"; it's harder to work with in a development environment.  If you already have a Web application publishing pipeline and are trying to adapt the backend to Docker, this can be a good approach.  Whether it's "better" depends on how important URL stability is to you, how much content is involved, how frequently it changes, and how important it is that the developer setup is more self-contained.


------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-01-26 13:43:50

------------

The primary goal of microservices is, in general, . Microservices should be independent of each other.
If you store the image in another container, you introduce coupling. You can only send mails when the other container is available.
So in general, the best way, in a microservice architecture, is to include the image in your mailing image.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-01-26 14:21:37

------------

Not directly related to your usecase but related to the title of the question:
Assume to have an image with a webserver (httpd). Should the static files in htdocs be part of the container image or should they come through a filesystem mount?
My answer to such a question would depend a bit on responsibility and lifecycle of webserver installation/patching and the documents making up the content. If you want to handover responsibility on the content to some other team, you likely want to use a volume mount and give the other team access to modify that volume.
This is no contradiction to the answer Hans gave. For one png that likely stays longer than you run a container I see no issue on baking it into the image.


------------
    
    