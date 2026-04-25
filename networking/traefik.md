Traefik
=======

  * https://github.com/containous/traefik

Usage
-----
```
$ ./traefik --configFile=traefik.toml
```

or using Docker

```
$ docker run -d -p 8080:8080 -p 80:80 -v $PWD/traefik.toml:/etc/traefik/traefik.toml traefik
```

[Configuration](https://raw.githubusercontent.com/containous/traefik/master/traefik.sample.toml) sample
