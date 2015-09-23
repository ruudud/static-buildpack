# DEPRECATION WARNING
Since dokku now supports using a Dockerfile placed at the git root, you should instead use that.
Example contents that will basically replace the functionality of this build pack and also make it easier to use locally:
```
FROM debian

RUN apt-get update && \
    apt-get install -y nginx-light && \
    rm -rf /var/lib/apt/lists/*

RUN mkdir /app
ADD . /app

EXPOSE 5000

CMD ["nginx", "-c", "/app/.nginx.conf"]
```

..and create a file `.nginx.conf` in the same place:
```
daemon off;
error_log stderr;
worker_processes 1;

events {
    worker_connections 1024;
}
http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;
    access_log /dev/stdout;

    server {
        listen 5000;
        port_in_redirect off;

        location / {
            root "/app/";
            autoindex
                on;
            index
                index.html
                index.htm;
        }
    }
}

```

# Dokku Static Buildpack using Nginx

Two things to make this work:

1. Add an empty `.servestatic` file in your project root
2. Create an `.env` file in your project containing this:

```
BUILDPACK_URL="https://github.com/ruudud/static-buildpack.git"
```
