# Docker Compose

[Source:](https://docker-hy.github.io/part2/)

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
 
 
$ docker-compose build
$ docker-compose push

