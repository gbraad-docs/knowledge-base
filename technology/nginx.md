NGINX
=====


Usage
-----

### Standard container
```
$ docker run --name some-nginx -v /some/content:/usr/share/nginx/html:ro -d nginx
```

### nginx-proxy
```
$ docker run -d -p 80:80 -p 443:443 \
   --name nginx-proxy \
   -v /path/to/certs:/etc/nginx/certs:ro \
   -v /etc/nginx/vhost.d \
   -v /usr/share/nginx/html \
   -v /var/run/docker.sock:/tmp/docker.sock:ro \
   jwilder/nginx-proxy
```

[Source](https://hub.docker.com/r/jwilder/nginx-proxy/)


### Let's Encrypt companion for nginx-proxy
```
$ docker run -d \
   --name nginx-proxy-letsencrypt \
   -v /path/to/certs:/etc/nginx/certs:rw \
   --volumes-from nginx-proxy \
   -v /var/run/docker.sock:/var/run/docker.sock:ro \
   jrcs/letsencrypt-nginx-proxy-companion
```
