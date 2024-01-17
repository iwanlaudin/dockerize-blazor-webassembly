# Dockerize Blazor Webassembly

## Adding NGINX Configuration
In the root of the project add a new file called and add in the following code. nginx.conf
```nginx
# nginx.conf

events { }
http {
  include mime.types;

  server {
    listen 80;

    location / {
      root /usr/share/nginx/html;
      try_files $uri $uri/ /index.html =404;
    }
  }
}
```

## Adding a Dockerfile

```Dockerfile
# Dockerfile

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY BlazorWasmDocker.csproj .
RUN dotnet restore BlazorWasmDocker.csproj
COPY . .
RUN dotnet build BlazorWasmDocker.csproj -c Release -o /app/build

FROM build AS publish
RUN dotnet publish BlazorWasmDocker.csproj -c Release -o /app/publish

FROM nginx:alpine AS final
WORKDIR /usr/share/nginx/html
COPY --from=publish /app/publish/wwwroot .
COPY nginx.conf /etc/nginx/nginx.conf
```
Using this Dockerfile, you can generate a Docker container containing a compiled Blazor Webassembly application ready to run with Nginx as a web server.

## Building the image
```bash
docker build -t blazorwasmdemo .
```

## Starting a container
```bash
docker run -d -p 8080:80 blazorwasmdemo
```
Once you run the command, open a browser and navigate to and you should be able to load the application. http://localhost:8080
