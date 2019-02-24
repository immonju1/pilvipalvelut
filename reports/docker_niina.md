# Docker

Lähdin asentamaan ja keräämään tietoa Dockerista noudattamalla kurssia
https://docker-hy.github.io/part1/   


 
Mikä on Docker?
 - Dockerin avulla pystyy kontittamaan sovelluksia, jolloin käsitteleminen ja siirtäminen eri alustalle on helpompaa
 - Docker onkin luotu erityisesti ottaen huomioon kehittäjien yhteensopivuus ongelmia. Eli sovellus toimii omalla koneella, mutta jos se siirretään toiselle koneelle, saattaa tulla yhteensopivuus ongelmia eri versioiden kesken.
 


## Docker CE (Community Edition) asentaminen Ubuntuun

- Asensin Dockersin suositeltua tapaa käyttäen, eli asentamalla Dockersin repositoryn

```
$ sudo apt-get update
```

- Asenna paketit HTTPS yli:
```
sudo apt-get install apt-transport-https
sudo apt-get install ca-certificates
sudo apt-get install curl
sudo apt-get install gnupg-agent
sudo apt-get install software-properties-common
```

- Lisää Dockersin GPG avain
```
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

- Avaimen voi varmistaa etsimällä 8 viimeistä merkkiä sormenjäljestä  
https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-using-the-repository 

- Dockersilla on 3 erilaista "update channels", joita ovat stable, test ja nighty.


- Asenna "stable" repositori
```
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

- Asenna Docker CE
```
$ sudo apt-get update
```
```
$ sudo apt-get install docker-ce docker-ce-cli containerd.io
```

- Varmista, että Docker CE toimii oikein. Tämä tehdään luomalla uusi kontti. Komento tarkistaa onko valmiiksi luotuna "hello-world" kuvaa, mutta jos ei ole niin se hakee sen Docker Hubista. 
```
$ sudo docker run hello-world
```

## Asenna Docker Compose
Docker Compose on työkalu pyörittämään multi-kontti Docker sovelluksia. Konfiguroiminen tehdään YAML tiedostossa.


- Asenna viimeisin Docker Compose versio
```
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

- Muuta oikeuksia
```
$ sudo chmod +x /usr/local/bin/docker-compose
```

- Varmista, että asennus onnistui katsomalla 
```
$ docker-compose --version
```

## Lisää käyttäjäsi Docker , jolloin ei tarvitse käyttää pääkäyttäjän oikeuksia komennoissa
```
$ sudo groupadd docker
```
```
$ sudo usermod -aG docker $USER
```
Tämän jälkeen kannattaa uudelleenkäynnistää kone.


## Hyödyllisiä komentoja

Komento | kuvaus
--------|--------
$ docker images | Listaa "kuvan", kontainerin, nimet
$ docker ps -a | Listaa kaikkien prosessi-id:t
$ docker rm <containers id> | Poistaa prosessi id:llä
$ docker stop <container id> | Pysäyttää prosessi id:llä



## Docker "image" 

- "Image", kuva, pitää sisällään sovelluksen ja sen "dependencies". Se pitää sisällään layereita, joista jokainen edustaa ohjeita.   

Kuvat haetaan Docker Hubista, jos niitä ei löydy paikallisesti.  

 "Docker Hub is the world's largest library and community for contaner images"  
 https://hub.docker.com/
 
---------------------------------------------------------------------------
Päivitetty 24.2.2019


## Ubuntun hakeminen Docker Hubista

Pullasin Ubuntun Dockeriin. 

```
$ docker pull ubuntu
$ docker pull ubuntu:16.04
```
Docker asensi oletuksena uusimman, ellei tarkenna versiota.

## Konttien starttaus ja stoppaus

```
$ docker run -d --name looper ubuntu:16.04 sh -c 'while true; do date; sleep 1; done'
```

- --name looper: viitataan konttiin
- ubuntu:16.04 on image
- heittomerkeissä on komento kirjoitettuna

Voit tarkastaa, että kontti on ajossa seuraavalla komennolla:
```
$ docker ps
```
Lokien dataa voit tarkastella komennolla:
```
$ docker logs -f looper
```
```
Fri Feb 22 20:59:00 UTC 2019
Fri Feb 22 20:59:01 UTC 2019
Fri Feb 22 20:59:02 UTC 2019
...
```
Docker voidaan laittaa paussille ja jatkaa seuraavilla komennoilla toisessa terminaali ikkunassa:
```
$ docker pause looper
$ docker unpause looper
```
Logit voidaan liittää toiseen toiseen terminaaliin komenmolla
```
$ docker attach looper
```
Painamalla CTRL+C kontti pysähtyy
```
$ docker start looper
$ docker attach --sig-proxy=false looper
```
Näiden komentojen jälkeen kontti on edelleen käynnissä mutta lopettaa tulostamisen kyseisessä terminaali-ikkunassa.


Allaolevalla komennolla pääsee konttiin "sisälle" ja käyttämään sitä kuten komentoriviä. Eli pystyt suorittamaan komentoja kuten Ubuntussa yleensäkin, eli jos allaolevan komennon jälkeen syötät "ps aux" saat tulostettua prosessit ja pystyt lopettamaan ne tutulla kill -komennolla ja prosessi-id:llä.
```
$ docker exec -it looper bash
```


## Luodaan oma projekti

Loin Dockerfile -tiedoston kotihakemistooni
```
$ touch Dockerfile
$ nano Dockerfile
```

Lisäsin seuraavan tekstin tiedostoon.
```
FROM ubuntu:16.04 

WORKDIR /mydir 
RUN touch hello.txt 
COPY local.txt . 
RUN wget http://example.com/index.html 
CMD ["/bin/bash"]
```

Aja se komennolla samassa hakemistossa
```
$ docker build .
```

Tämän jälkeen tulee error, koska meillä ei ole olemassa local.txt tiedostoa jota se yrittää kopioida. Tämä kannattaa siis luoda samaan hakemistoon. Tämän jälkeen kokeile uudestaan, jolloin tulee uusi error. Täytyy lisätä wget:n asennnus

```
FROM ubuntu:16.04 

WORKDIR /mydir 
RUN touch hello.txt 
COPY local.txt . 
RUN apt-get update && apt-get install -y wget
RUN wget http://example.com/index.html 
CMD ["/bin/bash"]
```
 Tämän jälkeen saat tiedon "Succesfully built 333n54232f". Kirjain ja numerosarja on random nimi imagellemme, joten se kannattaa vaihtaa.
 
Tagaa helpompi nimi imagelle.
```
$ docker build -t myfirst .
```
Ja aja imagemme sekä katso /mydir sisältö.
```
$ docker run -it myfirst 
  root@accf99660aeb:/mydir# ls 
  hello.txt  index.html  local.txt
```
"root@accf99660aeb" kirjain- ja numeroyhdistelmä vastaa kontin id:tä.
Muokkaa kontin sisältöä lisäämällä txt -tiedosto ja poistu exit -komennolla.

```
$ docker diff containerid
  C /root
  A /root/.bash_history
  C /mydir
  A /mydir/manually.txt
 
```

Myfirst ja tiedostosta voi luoda uuden imagen.
```
$ docker commit containerid myfirst-pluschanges 
```

Nyt tästä uudesta imagesta voidaan luodaan uusi kontti.

```
$ docker run myfirst-pluschanges ls -l 
  total 8 
  -rw-r--r-- 1 root root    0 Feb  23 07:17 hello.txt 
  -rw------- 1 root root 1270 Aug  9  2013 index.html 
  -rw-r--r-- 1 root root    0 Feb  23 07:19 local.txt
  -rw-r--r-- 1 root root	0 Feb  23 07:35 manually.txt
```


## Isompia ja monimutkaisempia kontteja

Lisätään imageen youtube-dl -ohjelma, joka lataa youtube videoita. Lisätään se avaamalla interaktiivinen sessio, jossa voidaan testata toimivuutta ennen Dockerfile -tiedostoon tallettamista.


```
$ docker run -it myfirst 
```

Täytyy asentaa curl komento. 

```
$ apt-get install -y curl
$ curl -L https://yt-dl.org/downloads/latest/youtube-dl -o /usr/local/bin/youtube-dl
```

Seuraavaksi täytyy vaihtaa oikeuksia sekä tarvitaan python youtube-dl varten.
```
$ chmod a+rx /usr/local/bin/youtube-dl 
$ apt-get install -y python
$ youtube-dl
```

Youtube-dl ajettaessa se pyytää URL sekä antaa varoituksen, että LC_ALL ei ole asetettu. Voidaan testata sen toimivuutta komennolla.
```
$ LC_ALL=C.UTF-8 youtube-dl
```

Tämän toimiessa voimme exportata sen sessioomme.
```
$ export LC_ALL=C.UTF-8
$ youtube-dl youtubevideoURL
```

Kun tämä on todettu toimivaksi, voimme lisätä tämän DOckerfile -tiedoston loppuun.

```
FROM ubuntu:16.04 

WORKDIR /mydir 
RUN touch hello.txt 
COPY local.txt . 
RUN apt-get update && apt-get install -y wget
RUN wget http://example.com/index.html 
CMD ["/bin/bash"]

RUN apt-get install -y curl python 
RUN curl -L https://yt-dl.org/downloads/latest/youtube-dl -o /usr/local/bin/youtube-dl 
RUN chmod a+x /usr/local/bin/youtube-dl 
ENV LC_ALL=C.UTF-8 
ENTRYPOINT ["/usr/local/bin/youtube-dl"] 
```

Käytämme ENViä (environmental variables), jolloin meidän ei tarvitse exportata vaan se on suoraan jo lisätty Dockerfileen. Meidän tulee muuttaa CMD --> ENTRYPOINT määrittelyksi, jotta URL ei toimi komentona vaan se on suoritettavana kun kontti starttaa. Aikaisemmissa esimerkeissä on käytetty Exec muotoa, joka on JSON muodossa.

ENTRYPOINT ja CMD voidaan asettaa kahdella eri tavalla, exec ja shell muodossa.
https://medium.freecodecamp.org/docker-entrypoint-cmd-dockerfile-best-practices-abc591c30e21

Tutkitaan kontissa tehtyjä muutoksia komennolla
```
$ docker diff containername
```

Kopioidaan tiedosto.
```
$ docker cp "determined_elion://mydir/Short introduction to Docker (Scribe)-UFLCdmfGs7E.mp4" .
```

Tiedosto on mahdollista saada isäntäkoneen ja kontin jaettuun kansioon tai tiedostoon, jota kutsutaan "volume". Nämä "volumes" myös muuttuvat kontin ohjelman ajamana ja ovat siten myös tallennettuna jos kontti sulkeutuu ja jotakin tapahtuu.

```
$ docker run -v $(pwd):/mydir youtube-dl https://www.youtube.com/watch?v=420UIn01VVc
```


## Yhteyksien salliminen konttiin

Konttiin voidaan sallia yhteyksiä. 

Portti täytyy paljastaa (expose) Dockerfilessä, eli siis lisätä rivi tekstiä, ja jonka jälkeen suorittaa komento (publish "-p").

```
EXPOSE 4567
```

```
$ docker run -p 1234:4567 app-in-port
```
Eli yllä olevalla komennolla voidaan ottaa yhteys porttiin 1234 ja se ohjataan sovelluksen porttiin 4567. Jos portin 1234 (isäntä portti) jättää pois, Docker luo automaattisesti portin vapaana olevasta portista.

## Imagen julkistaminen Docker Hubissa

Docker Hubiin täytyy kirjautua ja luoda uusi repository, joka asetetaan julkiseksi.

Nimeä uudelleen image:
```
$ docker tag youtube-dl <username>/<repositoryname>
```

Kirjaudu sisään ja pushaa image.

```
$ docker login
$ docker push <username>/<repositoryname>
```



------------------------------------------------------------------
Lähteitä, joita olen käyttänyt tässä raportissa:    

Seurasin tämän kurssin ohjeita
https://docker-hy.github.io/part1/

More information images  https://docs.docker.com/v17.09/engine/userguide/storagedriver/imagesandcontainers/#images-and-layers

Asennus Docker CE
https://docs.docker.com/install/linux/docker-ce/ubuntu/

Asennus Docker Compose
https://docs.docker.com/compose/overview/
 
Docker ryhmä
https://docs.docker.com/install/linux/linux-postinstall/ 
 
Update Channels
https://docs.docker.com/install/



