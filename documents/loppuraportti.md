# Sisällyluettelo

1. [Johdanto](#Johdanto)
2. [Mitä on DevOps](#mita-on-devops)
3. [Mikä on Docker](#mika-on-docker) 
    1. [Arkkitehtuuri](#arkkitehtuuri1)
    2. [Komentokieli, käytetyimmät komennot](#komennot)
    3. [Imaget, mistä saadaan](#imaget)
    4. [Konttien ajaminen](#kontit)


4. [Mikä on Kuberbetes](#mika-on-kubernetes)
5. [Mikropalvelut (?)](#mikropalvelut)
6. [Yhteenveto](#Yhteenveto)

## Johdanto  <a name="Johdanto"></a>
## Mitä on Devops <a name="mita-on-devops"></a>


## Mikä on Docker <a name="mika-on-docker"></a>

Docker on avoimeen lähdekoodiin perustuva teknologia kontitukseen.	
Dockerin avulla pystyy kontittamaan sovelluksia, mikä mahdollistaa käsittelemisen ja siirtämisen eri alustoille helpommin.
Docker onkin luotu erityisesti ottaen huomioon kehittäjien ongelmat yhteensopivuuden kanssa. Yhteensopivuus ongelmissa törmätään siihen, että sovellus toimii omalla koneella, mutta siirrettäessä toiselle koneelle ilmenee ongelmia esimerkiksi eri versioiden kesken.
(https://docker-hy.github.io/part1/)

Dockeria voidaankin verrata ihan perinteisiin rahtikontteihin, joita kuljetaan johdonmukaisesti ja järjestelmällisesti pinottuina.  Kontittaminen tarjoaa paljon etuja, kuten nopea sovellusten käyttöönotto, siirrettävyys ja uudelleenkäytettävyys. Isot yritykset kuten Google, Microsoft ja VMware ovatkin ottaneet Dockerin käyttöön infrastruktuurissaan.
(Troubleshooting Docker, Chapter 1. Understanding Container Scenarios and an Overview of Docker)


### Arkkitehtuuri
Kuva havainnollistamaan
		
Kontteja voidaan hyödyntää monella eri tapaa ja yleisimpiä tapoja ovat OS (operating system) kontit ja sovelluskontit. OS kontit voidaan helposti mieltää virtuaalikoneiksi (VM), mutta ne poikkeavat virtuaalikoneista (VM) sillä, että ne jakavat isäntäkoneella pyörivän käyttöjärjestelmän ytimen mutta tarjoaa käyttäjätilan eristämisen (user space isolation).

Sovelluskontit ovat tarkoitettu yhden prosessin ajamiseen. Se pitää siis sisällään sovelluksen prosessin. OS kontit puolestaan ajavat monia prosesseja. Sovelluskontissa paketoidaan sovelluksen komponentit eri kontteihin ja ne kommunikoivat keskenään API:n ja palveluiden avulla. Tällainen multi-komponentti järjestelmä sivuuttaa jo hiukan mikropalveluita, joista kerrotaan myöhemmin tässä raportissa.
(Troubleshooting Docker, Chapter 1. Understanding Container Scenarios and an Overview of Docker)

#### Komentokieli, käytetyimmät komennot  <a name="komennot">
    
Komento|Selite
--------|-------------
docker ps -a|Listaa kontainerit
docker images|Listaa Imaget
docker pull <image>|Hakee imagen Docker Hubista
docker rm <container-id>|poistaa kontainerin (id tai nimi)
docker rmi <image-id>|poistaa imagen
docker stop <container-id>|pysäyttää kontainerin
docker run <image>|ajaa imagen ja luo kontainerin

#### Image

Image on suoritettava paketti. Se sisältää kaiken tarvittavan sovelluksen suorittamiseen muun muassa koodin, kirjastot, ympäristö nja konfiguraatio tiedostot. Kontit launchataan imagen avulla. (https://docs.docker.com/get-started/)

Jokainen muutos originaliin imageen säilötään erillisellä layerilla. Joka kerta kun kommitoit imageen, luodaan uusi kerros (layer) Docker imageen. Alkuperäinen image ja sen olemassaolevat kerrokset pysyvät muuttumattomina. (Learning Docker, chapter 2 clarifying the docker terms)
Imagen layereita voi tarkastella komennolla.
```
docker image history
```
(Docker Bootcamp, Chapter 2. Launching application, Docker terminology)

Image on pohjana konteille, mikä tarkoittaa sitä että yhdestä imagesta voidaan luoda monia eri kontteja, joissa on erilaisia ominaisuuksia. Jokaisella kontilla on kirjoitettava kerros (writeable layer) mihin tallennetaan muutokset ja data, mutta ne käyttävät samaa imagea pohjana. Tämä image pysyy muuttumattomana ja kontin poistaminen ei vaikuta imageen.
(https://docs.docker.com/v17.09/engine/userguide/storagedriver/imagesandcontainers/#container-and-layers)

Imagen rakentamiseen käytetään Dockerfilea. Dockerfile -tiedosto pitää sisällään komennot. Image rakennetaan komennolla ```docker build``` käyttäen Dockerfilea.  (https://docs.docker.com/engine/reference/builder/)

Tähän esimerkki DockerFilesta?


Docker Hub on kirjasto ja yhteisö konttien imageille. Image saadaan lataamalla ne Docker Hubista. Se on maailman suurin repositori konttien imageille. Docker hubia käytetään imagien tallentamiseen ja hallinnointiin. Käyttäjät saavat pääsyn tallentaa image julkisiin tai yksityisiin repositoreihin. Se on suunnattu niin kehittäjille että yrityksille. (https://www.docker.com/products/docker-hub)


#### Konttien ajaminen

Kontit ajetaan komennolla ```docker run```. Tämä komento luo kirjoittettavan kerroksen imagen päälle ja käynnistää sen. Pysäytetty kontti voidaan käynnistää uudelleen komennolla ``` docker start```. (https://docs.docker.com/engine/reference/commandline/run/)
Kontissa pyörivät palvelut voivat pyöriä yhdellä isäntäkoneella tai useammalla isännällä. Tästä syystä konttien tuleekin pystyä keskustelemaan toistensta kanssa. Konteissa voidaankin käyttää IP-tauluja kontrolloimaan liikennetta konttien välillä. Kahden isäntäkoneen välillä käytetään tunnelointia. Ip osoite voidaan löytää komennolla  ``` docker inspect ``` .

jotain lisää FIND IP,  Expose port, linking containers ??


#### Testisovellus


-------------------------------
Kirja lähteet Dockeria varten:

Learning Docker
Raj, Pethuru ; Chelladhurai, Jeeva S. ; Singh, Vinod
Packt Publishing, E-kirja

Docker Bootcamp
McKendrick, Russ ; Chelladhurai, Jeeva S. ; Raj, Pethuru ; Singh, Vinod
Packt Publishing, E-kirja

Troubleshooting Docker
Kohli, Vaibhav; Dua, Rajdeep; Wooten, John
Packt Publishing, E-kirja

------------------------------

## Mikä on Kubernetes  <a name="mika-on-kubernetes"></a>
## Mikropalvelut (?)  <a name="mikropalvelut"></a>
## Yhteenveto  <a name="Yhteenveto"></a>



