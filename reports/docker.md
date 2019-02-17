# docker

Nämä ovat muistiinpanoni kurssista https://docker-hy.github.io/part1/

Use cases for Docker
https://nodesource.com/blog/containerizing-node-js-applications-with-docker/

## Docker arkkitehtuuri

### Tietoturva

- Kuka on luonut imagen? Tietoturva

### Optimointi

- Dockerfile komennot loogiseen järjestykseen
- Hakemistojen nimet
- RUN komennot yhteen -> yksi layer
$ docker history -> imagen koko

### Koon pienentäminen
- rm tai apt-get purge

### Ajaminen ei rootinta
Luo käyttäjä
USER app 

### Alpine Linux variant



## ympäristö

Asennus Vagrant boxiin, Ubuntu 16.04
```
$ vagrant up
$ vagrant ssh
```

Päivitetään paketit.

```
vagrant:~$ sudo apt-get update
vagrant:~$ sudo apt-get upgrade
```

## Docker asennus

```
vagrant:~$ wget -qO- https://get.docker.com/ | sh

# Executing docker install script, commit: 4957679
+ sudo -E sh -c apt-get update -qq >/dev/null
+ sudo -E sh -c apt-get install -y -qq apt-transport-https ca-certificates curl >/dev/null
```

Luodaan käyttäjä, jolla Dockeria ajetaan. Ei haluta ajaa roottina.

```
vagrant:~$ sudo adduser m
vagrant:~$ sudo usermod -aG docker mono
```

Ajetaan ensimmäinen docker kontti

```
mono@vagrant:$ docker run hello-world

Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
1b930d010525: Pull complete 
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

Kontti haettiin Docker Hubista, koska sitä ei ollut vielä lokaalina.

```
vagrant:~$ docker ps -a | grep hello-world
```

Komennolla nähdään Docker prosessit, jotta voidaan poistaa ID:llä

```
65b14c92ecfa        hello-world         "/hello"            5 seconds ago       Exited (0) 3 seconds ago                       amazing_ellis
```

Poistaminen, voidaan nyt poistaa docker rm id

```
mono@vagrant:/home/vagrant$ docker rm 65
65
```
Poistaa kaikki kontainerit
```
docker container prune
```

## Nginx

Käynnistyy terminaalissa
```
docker run nginx
```
Taustalla, pitää stopata ensin, jotta voi poistaa

```
docker run -d nginx
docker stop <container id or name>
```



# Docker komennot

Komento|Selite
--------|-------------
docker ps -a|Listaa kontainerit
docker images|Listaa Imaget
docker pull <image>|Hakee imagen Docker Hubista
docker rm <container-id>|poistaa kontainerin (id tai nimi)
docker rmi <image-id>|poistaa imagen
docker stop <container-id>|pysäyttää kontainerin
docker run <image>|ajaa imagen ja luo kontainerin
 
# Mistä imaget saadaan?
(docker hub)https://hub.docker.com/
```
docker search
```
(quai)https://quay.io/
 
# Imaget
```
docker pull ubuntu

docker pull ubuntu:16.04
```
Voi tagata lokaalisti
```
docker tag ubuntu:16.04 ubuntu:xenial

docker run fav_distro:xenial uptime
```
# kontainerien ajaminen ja pysäyttäminen
```
docker run -d --name looper ubuntu:16.04 sh -c 'while true; do date; sleep 1; done'
```
Lokitus kontista
```
docker logs -f looper
```
Tauolle
```
docker pause looper
docker unpause looper
```
Toisessa terminaalissa
```
docker attach looper, jos klikkaa ctrl-C kontin suoritus lakkaa
```
Estetään tämä
```
$ docker start looper 

$ docker attach --sig-proxy=false looper
```
--> terminaali katkeaa, prosesi jää taustallle

Kontiin pääsy, saadaan komentorivikehoite konttiin
```
docker exec -it looper bash
```
-i is “interactive, connect STDIN” and -t “allocate a pseudo-TTY”
```
$ docker kill looper 
$ docker rm looper 
```
ovat sama kuin ```docker rm --force looper```
```
docker run -d --rm -it --name looper-it ubuntu:16.04 sh -c 'while true; do date; sleep 1; done'
```
- rm siivoaa pois automaattiseti exitin jälkeen, tai ctrl-C terminaalissa
```
docker attach looper-it 
```
control+p, control+q -> pois STDOUT

# Luodaan oma image
```
Dockerfile
FROM ubuntu:16.04 

WORKDIR /mydir 
RUN apt-get update && apt-get install -y wget # komennot liittyvät toisiinsa
RUN touch hello.txt 
COPY local.txt . # lopioidan lokaali tiedosto konttiin
RUN wget http://example.com/index.html 
CMD ["/bin/bash"]
```
nimetään kontti:
```
docker build -t myfirst .
```
Ajetaan 
```
$ docker run -it myfirst 
```
Muokataan kontin sisäsltöä kontista käsin ja haetaan erot:
```
$ docker diff id
```
Luodaan uusi image kontin sisällöllä
```
$ docker commit id myfirst-pluschanges
```
Ajetaan ja testataan uusi image
```
docker run myfirst-pluschanges ls -l
```

# Isomman / monimutkaisemman kontainerin luonti

Toinen lähestymistapa Dockerin luontii, tehdään se interaktiivisesti ja testataan, että toimii. Jatkuvan buildauksen sijaan.
```
docker run -it myfirst
```
Kontissa voi testata toimiiko ohjelma. Tässä asennetaan youtube-dl ohjelmaa esimerkin mukaan.

Lopullinen Dockerfile
```
FROM ubuntu:16.04 

WORKDIR /mydir 
RUN apt-get update && apt-get install -y wget
RUN touch hello.txt 
COPY local.txt . 
RUN wget http://example.com/index.html 
CMD ["/bin/bash"]

RUN apt-get install -y curl python # python ja curl tarvitaan

RUN curl -L https://yt-dl.org/downloads/latest/youtube-dl -o /usr/local/bin/yout
ube-dl # haetaan sovellus

RUN chmod a+x /usr/local/bin/youtube-dl 

ENV LC_ALL=C.UTF-8 # merkistö kuntoon

CMD ["/usr/local/bin/youtube-dl"] 

ENTRYPOINT ["/usr/local/bin/youtube-dl"] # konttia ajettaessa annetaan ajokomennossa oleva parametri tälle komennole. muutoin menee CMD:lle ja ei toimi
```

Kääntäminen

```
docker build -t youtube-dl .
```
ENTRYPOINT ja CMD erot, "-merkkien käyttö parametrien välittämiseen. ENTRYPOINTia käyttämällä saadaan parametri ohjelmalle, ei komentoriville.

Ohjelman lataamat tiedostot latautuvat konttiin.
```
$ docker diff id 

  C /mydir 
  A /mydir/Short introduction to Docker (Scribe)-UFLCdmfGs7E.mp4 
```
Kopiointi kontista lokaaliin hakemistoon:

```
$ docker cp "determined_elion://mydir/Short introduction to Docker (Scribe)-UFLCdmfGs7E.mp4" . 
```

## Lokaalien hakemistojen liittäminen konttiin

1. Portti jota kontti kuuntelee
2. Isäntäkoneen portti, joka liitetty kontin porttiin

To expose a port, add line EXPOSE <port> in your Dockerfile

To publish a port, run the container with -p <host-port>:<container-port>
 
 esim.:
 ```
 expose 4567 Dockerfilessa
 ```
 
 Sidotaan hostin portti 1234 porttiin 4567
 ```
 docker run -p 1234:4567 app-in-port
 ```
 Seuraava komento näyttää mappaykset kontille
 ```
 docker port id
 ```
 
 # konttien julkaisu Docker hubissa
 
 Luo tunnus:   https://hub.docker.com/ 
 
 Dashboard: Create Repository
 
 Muuta imagen nimi
 ```
 docker tag youtube-dl <username>/<repositoryname>
 ```
 Login
 ```
 docker login
 
 docker push <username>/<repositoryname>
 ```
 
# Frontend

```
 docker exec -it looper-it /bin/sh -c 'read website; sleep 3; curl http://$website';
 ```
 
 ```
 Esimerkki node.js Docker imagesta (Dockerfile)
 
 FROM ubuntu:16.04 

RUN apt-get update && apt-get install -y wget 

RUN touch hello.txt

RUN apt-get install -y curl sudo

RUN curl -sL https://deb.nodesource.com/setup_10.x | bash

RUN sudo apt install -y nodejs

RUN sudo apt-get install -y git

RUN git clone https://github.com/docker-hy/frontend-example-docker.git

WORKDIR frontend-example-docker/

ENV API_URL=http://localhost:8000

RUN npm install

EXPOSE 5000

CMD [ "npm", "start" ]
```

# Back with volume file

Dockerfile
```
FROM ubuntu:16.04 

RUN apt-get update && apt-get install -y wget 

RUN touch hello.txt

RUN apt-get install -y curl sudo

RUN curl -sL https://deb.nodesource.com/setup_10.x | bash

RUN sudo apt install -y nodejs

RUN sudo apt-get install -y git

RUN git clone https://github.com/docker-hy/backend-example-docker.git

WORKDIR backend-example-docker/

ENV FRONT_URL=http://localhost:5000

RUN npm install

EXPOSE 8000

CMD [ "npm", "start" ]

```

Command
```
$ docker run -v $(pwd)/logs.txt:/backend-example-docker/logs.txt -d -p 8000:8000 back
```

