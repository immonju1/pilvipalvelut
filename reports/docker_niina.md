# Docker

Lähdin asentamaan ja keräämään tietoa Dockerista noudattamalla kurssia
https://docker-hy.github.io/part1/   


 
- Mikä on Docker?
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



