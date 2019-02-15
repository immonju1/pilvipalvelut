# Docker Compose

[Source:](https://docker-hy.github.io/part2/)

Docker compose on työkalu, jonka avulla voidaan määritellä ja ajaa applikaatioita, jotka käyttävät useita Docker kontteja.

Composen avulla voidaan skaalata palveluita.

Composen networking avulla voidaan yhdistää eri palveluita.

## docker-compose.yml

Yaml-file, jonka avulla voi hallinnoida Docker imageja. Samaan hakemistoon kuin Docker-file.

```
docker-compose.yml
version: '3' 

services: 

    youtube-dl-ubuntu:  
      image: <username>/<repositoryname>
      build: . 
 ```
 
 Versio > 2
 
 
Docker image voidaan rakentaa ja viedä repositorioon seuraavasti:
```
$ docker-compose build
$ docker-compose push
```
```
$ docker-compose up -d 
```

Voluumeita voidaan sitoa Docker Composeen myös.
```
location-in-host:location-in-container
```
services:
```
    compose_nimi:
      image:
      build: . (minne)
      volumes:
        - .:/mydir
      container_name: kontainerin nimi
```

Skaalaus
 docker-compose up --scale whoami=3 
 
 Miät porttia skaalattu kontti käyttää?
 
 docker-compose port
 
 docker networking

      

