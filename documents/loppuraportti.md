# DevOps - Docker ja Kubernetes mikropalveluarkkitehtuurin mahdollistajina
***Pilvipalvelut kurssin projektityö kevät 2019***   
 
 [Juha Immonen](https://github.com/immonju1) 
 
 [Niina Villman](https://github.com/niinavi)
 
 
# Sisällysluettelo

1. [Johdanto](#Johdanto)
2. [Mikropalvelut](#mikropalvelut)
3. [Mikä on Docker](#mika-on-docker) 
    1. [Arkkitehtuuri](#arkkitehtuuri)
    2. [Imaget](#imaget)
    3. [Konttien ajaminen](#kontit)
    4. [Komentokieli](#komennot)
5. [Testisovellus](#testisovellus)
6. [Mikä on Kuberbetes](#mika-on-kubernetes)
    1. [Kubernetes Arkkitehtuuri](#arkki-kubernetes)
    2. [Sovellusten pakkaaminen Kubernetesissa](#pakkaaminen)
    3. [Manifest](#manifest)
    3. [Kubernetes objektit](#objektit)
7. [Minikube](#minikube)
    1. [Asentaminen](#m-asennus)
    2. [Kubectl](#kubectl)
    3. [Testaus](#k-testaus)
    4. [Hyödyllisiä komentoja](#komentoja)
8. [Kubernetesin asennus AWS:ään](#aws)
    1. [AWS asennuksen esiehdot](#esiehdot)
    2. [Kubectl](#aws-kubectl)
    3. [Asenna kops](#kops)
    4. [Asenna ja konfiguroi awscli](#awscli)
    5. [Klusterin luominen](#klusterin_luonti)
    6. [Pod, Service ja Loadbalancer](#lb)
    7. [Domain nimen testaaminen](#domain)
    8. [Poista klusteri](#poista)
9. [Yhteenveto](#Yhteenveto)

<a name="Johdanto"></a>

# Johdanto  

Projekti on harjoitustyö Haaga-Helian kurssille pilviteknologiat. Kurssin laajuus oli 5 opintopistettä. Valitsimme aiheen projektin aiheeksi, koska haluamme oppia enemmän pilvipalveluista ja miten niitä toteutetaan. Keskeisenä idologiana pilvipalveluissa on DevOps.

DevOps on, sekä tapa organisoida töitä ohjelmistokehityksessä, että joukko uusia teknologioita, joiden avulla sovellusten hallintaan liittyviä tehtäviä automatisoidaan. DevOpsin tavoitteena on nopeuttaa sovelluskehityssykliä (Dev), eli viedä tuotantoon mahdollisimman nopeasti. Samalla sen tavoitteena on automatisoida tuotantoonvientiin liittyvät (Ops) taskit. Teknologian ohessa DevOpsiin liitty myös siilojen madaltaminen, kehitys- ja tuotantopuolen ihmiset työskentelevät mielellään samoissa tiimeissä.

Palveluiden nopeampi tuotantoonvienti ja kehittäminen vaatii myös uudenlaista arkkitehtuuria. Isojen monoliittisten sovellusten sijaan tarvitaan arkkitehtuuri, joka mahdollistaa pienten itsenäisten palveluiden kehittämisen ja tuotantoonviennin. Arkkitehtuuria kutsutaan mikropalveluarkkitehtuuriksi. 

DevOpsiin liittyy lukuisia teknisistä ratkaisuita. Projektin aikana opettelimme DevOps teknologioista Dockerin ja Kubernetesin. Teimme kevyen testisovelluksen simuloimaan mikropalvelua (Hello world -sovellus), kontitimme sen Dockerin avulla ja veimme sen tuotantoon Kubernetes klusteriin, jossa oli loadbalancer. Palvelu oli saavutettavissa domain nimen avulla.

Seuraavassa käymme läpi asennuksen eri vaiheet ja teknologiat, joita käytimme projektin aikana.

<a name="mikropalvelut"></a>

# Mikropalvelut 

## Yleistä

Kubernetesin avulla on helppo viedä tuotantoon hyvin erilaisia sovelluksia. Sovellukset voivat olla monoliitteja, joilla ei ole mitään tekemistä toistensa kanssa. Tai mikropalveluita, pieniä sovelluksia, jotka yhdessä muodostavat isommman sovelluksen.

Mikropalveluarkkkitehtuurin suosio kasvaa jatkuvasti. Tämä lähestymistapa antaa kehittäjille mahdolisuuden pilkkoa sovellus pieniin itsenäisiin osiin. Lukuisten mikropalveluiden hallinta voi aiheuttaa palveluiden hallinnalle ongelmia. Kuinka hallita ja operoida kymmeniä, mahdollisesti satoja eri mikropalveluita.

## Mikropalveluiden periaate ja hyödyt

Microservices-arkkitehtuurilla tarkoitetaan yksittäistä itsenäistä sovellusta, joka tarjoaa integraatiorajapinnan muille sovelluksille. Isompi ohjelmisto koostuu useista eri mikroserviceistä, päinvastoin kuin perinteinen ohjelmisto, joka koostuu yhdestä isosta sovelluksesta, joka tekee kaiken. (DevOps Fundamentals kurssi 2017.) Mikropalveluarkkitehtuuri on vaihtoehto monoliittiselle sovellukselle.

Sujuva ohjelmiston jatkokehitys edellyttää usein uusien teknologioiden liittämisen vanhaan järjestelmään. Mikäli järjestelmän osia voi vaihtaa pikkuhiljaa uusiin ja tarpeita vastaaviin, helpottuu ohjelmiston kehitys ja ylläpito. (DevOps Fundamentals kurssi 2017.) Tämä on mahdollista mikropalveluilla.

Lisäksi yksinkertainen palvelu on helppo kirjoittaa kokonaan uusiksi eikä muutaman palvelun uudelleenkirjoittaminen vie paljoa aikaa. Mikropalveuiden avulla voidaan myös korvata olemassaolevaa vanhaa teknologiaa tai korvata iso monoliitti pikkuhiljaa. (DevOps Fundamentals kurssi 2017.)

Mikropalveluiden avulla samaan aikaan tuotannossa voi olla eri versioita palveluista, liikenne voidaan ohjata eri versioille ja verrata niiden toimintaa (Viane 2018, luku 1).

## Mikropalveluiden haasteita

Mikropalveluarkkitehtuuri voi johtaa toisaalta hyvin monimutkaiseen arkkiehtuuriin. Lukuisa joukko mikropalveluita kommunikoi keskenään, eikä ole selkeää kokonaiskuvaa siitä, mikä palvelu käyttää mitäkin toista palvelua. Tuloksena on melkoinen sekasotku, joka ei ole kenenkään ylläpidettävissä. (Viane 2018, luku 7.)

Mikropalveluarkkitehtuurin tai toteutusten ongelmana on myös se, että ei ole salausta tietoliikenteessä mikropalveluiden välillä. Jos kommunikointi toisen mikropalvelun kanssa epäonnistuu, ei ole vikasietoisuutta, yhteyttä ei yritetä uudestaan. Vikasietoisuus pitää olla manuaalisesti koodattuna kuhunkin mikropalveluun. Sovelluksella tai mikropalveluilla ei ole älykästä kuormanjakoa, eli ei seurata mikropalveluiden latenssia, käytetään vain yksinkertaista algoritmia kuormanjakoon. (Viane 2018, luku 7.)

Mikropalveluiden välillä ei voida tehdä reitityspäätöksiä. Mikropalveluiden välisestä liikenteestä ei jää välttämättä lokeja, eikä liikennettä monitorointia. Mikropalveluiden välillä ei ole myöskään  pääsynvalvontaa, ellei sitä tehdä itse mikropalveluihin. (Viane 2018, luku 7.)

## Ratkaisu mikropalveluiden haasteisiin

Ratkaisuna aiemmin mainittuihin haasteisiin on toki tehdä mikropalveluihin itseensä kaikki tarvittavat asiat, mutta se vaatii jokaiselta mikropalveluiden kehittäjältä oman toteutuksen. Ratkaisuna voidaan käyttää proxyn lisäämistä kaikkiin mikropalveluihin. Kommunikointi tapahtuu proxyn avulla mikropalveluiden välillä. Proxy hoitaa salauksen ja vikasietoisuuden. Proxyissa voi olla myös kuormanjako-ominaisuuksia. (Viane 2018, luku 7.)

Reititys mikropalveluiden välillä, siten että eri versioita mikropalveluista voi olla olemassa, voidaan hoitaa hallintakerroksen avulla (management interface). Samoin lokien keruu ja monitorointi tapahtuu hallintakerroksen avulla. (Viane 2018, luku 7.)

<a name="mika-on-docker"></a>

## Mikä on Docker 

Docker on avoimeen lähdekoodiin perustuva teknologia kontitukseen.	
Dockerin avulla pystyy kontittamaan sovelluksia, mikä mahdollistaa käsittelemisen ja siirtämisen eri alustoille helpommin.
Docker onkin luotu erityisesti ottaen huomioon kehittäjien ongelmat yhteensopivuuden kanssa. Yhteensopivuus ongelmissa törmätään siihen, että sovellus toimii omalla koneella, mutta siirrettäessä toiselle koneelle ilmenee ongelmia esimerkiksi eri versioiden kesken.
(Kousa 2019, luku 1.)

Dockeria voidaankin verrata ihan perinteisiin rahtikontteihin, joita kuljetaan johdonmukaisesti ja järjestelmällisesti pinottuina.  Kontittaminen tarjoaa paljon etuja, kuten nopea sovellusten käyttöönotto, siirrettävyys ja uudelleenkäytettävyys. Isot yritykset kuten Google, Microsoft ja VMware ovatkin ottaneet Dockerin käyttöön infrastruktuurissaan.
(Kohli, Dua & Wooten 2017, luku 1.)

<a name="arkkitehtuuri"></a>
### Arkkitehtuuri
		
Kontteja voidaan hyödyntää monella eri tapaa ja yleisimpiä tapoja ovat OS (operating system) kontit ja sovelluskontit. OS kontit voidaan helposti mieltää virtuaalikoneiksi (VM), mutta ne poikkeavat virtuaalikoneista (VM) sillä, että ne jakavat isäntäkoneella pyörivän käyttöjärjestelmän ytimen mutta tarjoaa käyttäjätilan eristämisen (user space isolation).


![Docker and Virtualization](https://github.com/immonju1/pilvipalvelut/blob/master/pics/docker_virtualmachine.png)
Kuva 1. Docker vs. virtuaalikone. Mukaillen Kohli ym. 2017, luku 1.


Sovelluskontit ovat tarkoitettu yhden prosessin ajamiseen. Se pitää siis sisällään sovelluksen prosessin. OS kontit puolestaan ajavat monia prosesseja. Sovelluskontissa paketoidaan sovelluksen komponentit eri kontteihin ja ne kommunikoivat keskenään API:n ja palveluiden avulla. Tällainen multi-komponentti järjestelmä sivuuttaa jo hiukan mikropalveluita, joista kerrotaan myöhemmin tässä raportissa.
(Kohli ym. 2017, luku 1.)

<a name="imaget"></a>
#### Image

Image on suoritettava paketti. Se sisältää kaiken tarvittavan sovelluksen suorittamiseen muun muassa koodin, kirjastot, ympäristö nja konfiguraatio tiedostot. Kontit launchataan imagen avulla. (Docker inc 2019a.)

Jokainen muutos originaliin imageen säilötään erillisellä layerilla. Joka kerta kun kommitoit imageen, luodaan uusi kerros (layer) Docker imageen. Alkuperäinen image ja sen olemassaolevat kerrokset pysyvät muuttumattomina. (Raj, Chelladhurai, Singh 2015, luku 2.)

Imagen layereita voi tarkastella komennolla.
```
docker image history
```
(McKendrick, Chelladhurai, Raj, & Singh 2017. Luku 2.)

Image on pohjana konteille, mikä tarkoittaa sitä että yhdestä imagesta voidaan luoda monia eri kontteja, joissa on erilaisia ominaisuuksia. Imagella on kerroksia (layers) ja kontilla on myös oma kerroksensa. Jokaisella kontilla on kirjoitettava kerros (writeable layer) mihin tallennetaan muutokset ja data, mutta ne käyttävät samaa imagea pohjana. Tämä image pysyy muuttumattomana ja kontin poistaminen ei vaikuta imageen. Kontti voi kirjoittaa kirjoittaa dataa kontissa mutta kontin data ei säily samallalailla kuten imagen.
(Docker inc 2019a.)


![Docker Layer](https://github.com/immonju1/pilvipalvelut/blob/master/pics/docker_layer.png)
Kuva mukaillen Docker inc 2019a. Container and layers.

Imagen rakentamiseen käytetään Dockerfilea. Dockerfile -tiedosto pitää sisällään komennot. Image rakennetaan komennolla ```docker build``` käyttäen Dockerfilea.  (Docker inc 2019b.)

Docker Hub on kirjasto ja yhteisö konttien imageille. Image saadaan lataamalla ne Docker Hubista. Se on maailman suurin repositori konttien imageille. Docker hubia käytetään imagien tallentamiseen ja hallinnointiin. Käyttäjät saavat pääsyn tallentaa image julkisiin tai yksityisiin repositoreihin. Se on suunnattu niin kehittäjille että yrityksille. (Docker inc 2019c.)

<a name="kontit"></a>
### Konttien ajaminen

Kontit ajetaan komennolla ```docker run```. Tämä komento luo kirjoittettavan kerroksen imagen päälle ja käynnistää sen. Pysäytetty kontti voidaan käynnistää uudelleen komennolla ``` docker start```. (Docker inc 2019d.)
Kontissa pyörivät palvelut voivat pyöriä yhdellä isäntäkoneella tai useammalla isännällä. Tästä syystä konttien tuleekin pystyä keskustelemaan toistensta kanssa. Konteissa voidaankin käyttää IP-tauluja kontrolloimaan liikennetta konttien välillä. Kahden isäntäkoneen välillä käytetään tunnelointia. Ip osoite voidaan löytää komennolla  ``` docker inspect ``` . (Goasguen 2015a, luku 3.)

Yhteys konttiin avataan paljastamalla (expose) portti. Docker kontti kuuntelee määrittelemääsi porttia. Portti täytyy julkaista, eli kontin portti ohjataan isäntäkoneen porttiin. Portin määrittelemään tapahtuu lisäämällä rivi ``` EXPOSE <port> ``` Dockerfileen. Portin julkaiseminen tapahtuu ajamalla kontti ja lisäämällä portit komentoon ``` docker run -p <host-port>:<container-port> app-in-port ```. Ensimmäiseksi otetaan siis yhteys isäntäkoneen porttiin ja joka ohjautuu sitten sovelluksen porttiin. Jos isäntäkoneen porttia ei määritellä Docker valitsee automaattiseksi vapaan portin. (Kousa 2019, luku 1.)


<a name="komennot"></a>
### Komentokieli, käytetyimmät komennot 
    
Komento|Selite
--------|-------------
docker ps -a|Listaa kontainerit
docker images|Listaa Imaget
docker pull <image>|Hakee imagen Docker Hubista
docker rm <container-id>|poistaa kontainerin (id tai nimi)
docker rmi <image-id>|poistaa imagen
docker stop <container-id>|pysäyttää kontainerin
docker run <image>|ajaa imagen ja luo kontainerin
(Kousa 2019, luku 1.)


<a name="testisovellus"></a>
# Testisovellus

Testisovelluksemme on yksikertainen node.js sovellus, joka tulostaa Hello World. Testisovelluksessa hyödynsimme Node.js Foundation sivuilta löytyvää materiaalia. (Node.js Foundation.)

## Docker file

```
FROM node:8

WORKDIR /usr/src/app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 8080
CMD ["npm", "start"]
```

## Docker containerin luonti

Docker Hubiin pitää viedä ensin image, jota voidaan ajaa AWSssä tai Minikubessa.

Rakennetaan image
 ```
docker build -t helloworld .
 ```
Testataan lokaalisti

 ```
 docker run -d -p 8080:8080 helloworld
 ```
 (Kousa 2019, luku 1.)
 
## Julkaistaan Docker Hubiin image 
 
 Luotu tunnus osoitteessa:  https://hub.docker.com/ 
 
 Luotu Dashboard: Create Repository-toiminnolla.
 
 Muutettu imagen nimi omalla koneella
 ```
 docker tag helloworld immonju/helloworld
 ```
 Login ja vienti repoon.
 ```
 docker login
 
 docker push immonju/helloworld
```
(Kousa 2019, luku 1.)

 <a name="mika-on-kubernetes"></a>

# Mikä on Kubernetes 

Kubernetes on Open source sovellus, jolla voidaan viedä tuotantoon, skaalata ja hallinnoida kontitettuja sovelluksia. Kontainereissa voi olla esim. Web sovelluksia. (Goasguen 2017b, luku "What is Kubernetes".) Kubernetes on orkestraattori, joka mahdollistaa mikropalveluarkkitehtuurin. Ilman orkestraattoria konttien hallinnointi isossa sovelluksessa, joka koostuu mikropalveluista, on hyvin hankalaa.

Docker ja Kubernetes täydentävät toisiaan, tyypillisesti sovellukset kontitetaan Dockerilla, ja Kubernetes orkestroi niitä (Poulton 2018, 6). Kontainerina voi olla muukin kuin Docker. Kubernetesin avulla useita kontainereita voidaan ajaa samalla koneella, kontainerin voi käynnistää tietylle nodelle, kontainereita voi uudelleenkäynnistää ja kontainereita voi siirtää toiselle nodelle. (Viane 2018, luku 1.)

Kubernetes tekee perinteiset ohjelmistojen tuotantoonviennit ja päivitykset helpoksi. Sovelluksille voi tehdä deploymentteja (tuotantoon vientejä), sovelluksen versiota voi päivittää, vikatilanteessa voidaan palata edelliseen sovellusversioon (roll back) ja päivityksiä on mahdollista tehdä katkottomasti, eli päivitys ei näy loppukäyttäjille. (Poulton 2018, 5.) Sovellusta voidaan myös skaalata joustavasti.

Käyttäjän ei tarvitse välittää missä ja miten ohjelma pyörii, Kubernetes hoitaa sovelluksen ajoon haluttuun ympäristöön (Poulton 2018, 10).

Seuraavassa kappaleessa käydään läpi miten Kubernetes asennetaan käyttäen Minikubea. Tämän jälkeen asennetaan Kubernetes klusteri ja ohjelma AWS:ään. Lopuksi käydään läpi Kubernetesin arkkitehtuuri.

 <a name="arkki-kubernetes"></a>

## Kubernetes Arkkitehtuuri

![](https://github.com/immonju1/pilvipalvelut/blob/master/pics/kubernetes_arkkitehtuuri.png)

Kuva 2. Kubernetes arkkitehtuuri (Kuva mukaillen Aqua Security 2018).

Seuraavissa kappaleissa käymme läpi Kubernetesin arkkitehtuurin, sovellusten pakkaamisen ja Kubernetesin objektit.

### Klusteri

Kubernetesin klusterilla tarkoitetaan joukkoa tietokoneita, jotka ovat yhteydessä toisiinsa ja muodostaen näin yhden yksikön. Koneet voivat olla virtuaalikoneita tai fyysisiä servereitä (Goasguen 2017b, luku "What is Kubernetes"). Klusterissa on kahdenlaisia koneita, joita voidaan kutsua useilla nimillä. Kutsumme tässä projektissa toisia Master koneiksi (Head Node), ja loppuja koneita Nodeiksi (Worker Node). Master koneita on yleensä yksi.

Kuva jostain kokonaisarkkitehtuurista.

### Master ja Nodet

Kubernetes klusteri rakentuu mastereista ja nodeista. Nämä voivat olla Linux servereitä, jotka voivat olla virtuaalikoneita tai fyysisiä palvelimia.

#### Master

Master on vastuussa klusterin hallinnoinnista. Se myös monitoroi klusteria, ajaa muutokset sinne ja reagoi tapahtumiin klusterissa. (Poulton 2018, 15.) HA ratkaisuissa mastereita voi olla useita.

Seuraavassa on esitelty Masterin eri komponentit.

API server
- API server tarjoaa REST rajapinnan klusterin käyttämiseen ja ohjaamiseen. Rajapintaan välitetään manifesteiksi kutsuttuja yaml-fomraatissa olevia konfiguraatiotiedostoja. (Poulton 2018, 16.) Näiden avulla voidaan esimerkiksi viedä sovellus tuotantoon (deployment). 

Klusterin tietovarasto
- Klusterin hallinnollinen data ja konfiguraatiot säilötään etcd:n, joka on hajautettu tietokanta. etcd on "yksi totuus" klusterista.

Control Manager
- Valvoo klusteria ja reagoi tapahtumiin. Tavoitteena on varmistaa, että klusteri on tilassa jossa sen halutaan olevan. (Poulton 2018, 17.)

Scheduler
- Scheduler jakaa sovellukset klusteriin, siten että sovelluksella on riittävästi resursseja käytössään (Poulton 2018, 17).

#### Node

Nodet ovat Kubernetes klusterin työyksiköitä. Niiden tehtävä on odottaa uusia tehtäviä API serveriltä, toteuttaa ne ja raportoida tilastaan takaisin Masterille. (Poulton 2018, 18.)

Node rakentuu kolmesta eri komponentista.

Kubelet
- Kubelet ohjaa Noden toimintaa ja huolehtii, että tarvittavat Podit ovat ajossa. Sen tehtävänä on kommunikoida masterin kanssa (API server) ja toteuttaa saadut tehtävät. Master päättää mitä tehdään, jos tarvitaan esim. uusi Node Podeille. (Poulton 2018, 19.) Esimerkiksi jos Pod terminoituu Nodella, Master päättää missä sitä yritetään ajaa seuraavaksi.

Container runtime (CR)
- Container Runtime tarvitaan konttien ajamiseen Nodella. CR hakee imaget ja käynnistää sekä pysäyttää ne. (Poulton 2018, 19.) 

Kube-proxy
- Network Proxy huolehtii lokaalista verkosta Nodejen välillä. Esimerkiksi siitä, että jokaisella Nodella on oma IP, sekä IPtables asetukset kohdallaan liikenteen reitittämiseksi oikealle Podille. (Poulton 2018, 20.)

 <a name="pakkaaminen"></a>

## Sovellusten pakkaaminen Kubernetesissa

Jotta sovellusta voidaan ajaa Kubernetes klusterissa, pitää sovelluksesta tehdä Docker image, tälle imagelle voidaan sitten tehdä deployment manifest-tiedoston avulla (Poulton 20). Deployment tehdään Podin sisällä, joka on "kääre" kontainerille.

Kubernetes tarjoaa objekteja sovellusten hallintaan. Niiden avulla sovellus kapseloidaan. Kukin kapselointi tarjoaa omat abstraktiokerroksensa ja palvelunsa sovellukselle. Sovellus kapseloidaan kontin sisälle, kontti kapseloidaan Podin sisälle, Deployment kapseloi Podit nodella. Service kapseloi Deploymentit.

Deployment avulla sovellus voidaan skaalata (luoda tai vähentää podeja). Sen avulla myös Podeja voidaan käynnistää uudelleen, ja valvoa niiden toimintaa. (Poulton 2018, 20.)

![Kubernetes Node](https://github.com/immonju1/pilvipalvelut/blob/master/pics/kubernetes_node.png)
Kuva 3. Kubernetes node. (Kuva mukaillen Poulton 2018, 20)

 <a name="manifest"></a>

## Manifest

Sovelluksen tila kuvataan manifesteissa, jotka ovat yaml-formaatissa olevia tekstitiedostoja. 

Nämä välitetään API serverille, ja tallennetaan halutuksi tilaksi etcd tietovarastoon. 

API Server ohjaa sovelluksen oikeaan tilaan klusterissa.

Valvonta asetetaan päälle, eli verrataan sovelluksen nykyistä tilaa haluttuun  tilaan. (Poulton 2018, 20.)

 <a name="objektit"></a>

## Kubernetes objektit

### Pods

Pod on Kubernetes maailmassa pienin yksikkö, joka voidaan viedä klusterin Nodelle. Docker maailmassa vastaava on kontti. Pod sisältää yleensä yhden sovelluskontin.

Arkkitehtuuri
- Docker kontti kapseloidaan Podin sisälle. Pod on ajoympäristö konteille. Jos kontissa ajetaan useampaa konttia, ne jakavat saman IP-osoitteen. Kontit voivat kommunikoida Podin sisällä locahost verkossa. (Poulton 2018, 24.) Podin voi ajatella olevan kontti, jonka sisällä on kontteja.
- Skaalautuvuuden kannalta on parempi, että yhdessä Podissa on ajossa vain yksi kontti. Tällöin voidaan tarvittaessa luoda uusia Podeja, ja sovellusta voidaan skaalta. Yksi Pod voi kuitenkin sijaita vain yhdellä nodella. (Poulton 2018, 27.)

Elinkaari
- Podit luodaan, ne palvelevat aikansa ja jossain vaiheessa niiden toiminta lakkaa tai ne terminoidaan. Jos Podissa oleva sovellus lakkaa toimimasta, luodaan uusi Pod, johon ladataan sovelluksen image. (Poulton 2018, 27.) Podeja ei siis yritetä käynnistää uudelleen, jos Pod tuhoutuu tai lakkaa toimista, tilalle vain luodaan uusi.

### Deployments

Deployment on ylemmän tason kapselointi Podeille, joka tarjoaa lisäpalveluita, kuten skaalautumisen, loppukäyttäjille näkymättömän versionvaihon (zero-downtime update) ja rollbackin aiempaan versioon. (Poulton 2018, 27.) Podeja hallinnoidaan Deployment objektin avulla. Deployment määrittelee haluntun tilan Podille, ja se ylläpitää tilaa. (Warnock 2018, luku "Kubernetes architecture and design".)

### ReplicaSet

ReplicaSet huolehtii, että Podeja on oikea määrä ajossa tiettynä aikana. ReplicaSet on hyvä määritellä Deploymentin osana. Se määrittelee kuinka monta sovellusinstanssai klsuterissa on ajossa. (Warnock 2018, luku "Kubernetes architecture and design".) Ohjelman skaalaminen tapahtuu ReplicaSetin avulla.

### Services

Podit itsessään ovat epäluotettavia, sovellukset niissä saattavat vikaantua. Uusia podeja käynnistyy ja terminoituu. Service edustaa pysyvyyttä sovellukselle. Service kapseloi joukon Podeja (eri deploymenteissa) ja tarjoaa niille front-endin ulkomaailmaan. Nämä palvelut pitävät sisällään domain nimen, IP-osoitteet, portit ja kuormanjaon sovellukeselle. (Poulton 2018, 28.)

Service pitää yllä kirjapitoa, missä podit ovat, tarjoten luotettavan endpointin sovellukselle. Vastaavasti vaikka Podeja skaalattaisiin ylös tai alaspäin, tietää Service käytössä olevat Podit ja sovelluksen käyttäjille skaalaus on näkymätön. (Poulton 2018, 28-29.) 

Serice käyttää Labeleita kuorman jakamiseen Podeille, liikenne ohjataan niille Podeille, joilla on oikeat labelit. Servicet ohjaavat liikennettä vain toimiville Podeille. (Poulton 2018, 30.)

 <a name="minikube"></a>

# Minikube

Minikube on työkalu, jolla voi asentaa itselleen yksinkertaisen Kubernetes klusterin. Toinen vaihtoehto on Docker Client. Minikuben voi asentaa linuxiin, Windowsiin tai Macille. Asensimme Minikuben noudattamalla Kubernetes sivun ohjeita. (The Kubernetes Authors 2019a.)

Minikuben asentaminen vaatii virtuaalikoneen esim. Virtualbox.

Linkki Minikuben dokumentaatioon [](https://kubernetes.io/docs/tasks/tools/install-minikube/)

<a name="m-asennus"></a>

## Asentaminen

Asensimme Minikuben koneelle, jossa Ubuntu 18.04 LTS.

VirtualBoxin asentaminen onnistuu alla olevalla komennolla, jos sitä ei ole asennettuna.

```
sudo apt-get install virtualbox
```

Minikuben asennetaan seuraavalla komennolla.

```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && sudo install minikube-linux-amd64 /usr/local/bin/minikube
 ```

Minikuben käynnistäminen:

```
minikube start
```

Asennuksen jälkeen minikuben statuksen voi tarkistaa.

```
minikube status
 ```

Minikuben voi pysäyttää komennolla

```
 minikube stop
```

<a name="kubectl"></a>

## Kubectl

Kubectl on komentorivityökalu sovellusten hallintaan, kun käytössä on Kubernetes. Kubectl avulla voidaan selvittää klusterin resursseja, voidaan luoda, poistaa tai muuttaa komponentteja, sekä viedä sovelluksia klusteriin. Kubectl tarvitaan siis  Kubernetes klusterin ohjaamiseen.  Seuraavat kappaleet käsittelevät Kubectl asennusta ja noudatimme ohjeita Kubernetesin sivuilta. (The Kubernetes Authors 2019b.)

Lisää dokumentaatiota löytyy osoitteesta [](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

### Kubectl asennus

Kubectl:n voi asentaa itselleen seuraavilla komennoilla.

```
sudo apt-get update && sudo apt-get install -y apt-transport-https

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update

sudo apt-get install -y kubectl
 ```

Kubectl testaus, onko asentunut. Tulostuu kubectl komentovaihtoehdot.

```
kubectl
```

Jos Minikube on päällä, Minikube klusterin tiedot saadaan komennolla

```
kubectl cluster-info
```
<a name="k-testaus"></a>

## Testaus

Käynnistä Minikube ensin.

```
minikube start
```

Tämän jälkeen luodaan Pod ja Sercvice. Ajettava image on "immonju/helloworld"

### Podin luominen

Podin luomiseen käytettävä tiedosto pod-helloworld.yml

```
apiVersion: v1
kind: Pod
metadata:
  name: nodehelloworld.example.com
  labels:
    app: helloworld
spec:
  containers:
  - name: kubernetes-demo
    image: immonju/helloworld
    ports:
    - containerPort: 8080

```

Luodaan pod kubectl:n avulla

    kubectl create -f pod-helloworld.yml

Tarkistus, että asentunut oikein.

    kubectl describe pod nodehelloworld.example.com

### Luodaan Service Minikubeen

Podin luomisen jälkeen voidaan luoda service.

    kubectl expose pod nodehelloworld.example.com --type=NodePort --name nodehelloworld-service

Komennolla

    kubectl get service

Nähdään päällä olevat palvelut.

### Sovelluksen käyttäminen

Pääsy sovellukseen tapahtuu normaalisti servicen kautta. 

Palvelun URL saadaan selville alla olevalla komennolla, jonka jälkeen Web selaimella voi tarkistaa sovelluksen toiminnan.

    minikube service nodehelloworld-service --url

Toinen vaihtoehto on NodePort, jolloin käytetään sovellusta suoraan Noden portin kautta. NodePort:ia ei kannata käyttää, jos sovellus on asennettu usealle Nodelle. Tällöin tarvitaan Loadbalancer, joka ohjaa liikennettä Nodeille.

Porttiohjaukseen voidaan käyttää port-forward komentoa, jolloin sovellusta käytetään suoraan Podin kautta.

    kubectl port-forward nodehelloworld.example.com 8081:8080

local port 8081 ohjautuu porttiin 8080, jota kuuntelee sovellus Podissa.

```
curl localhost:8081
```
<a name="komentoja"></a>

## Hyödyllisiä komentoja

Laita taulukkoon:

Poista pod

    kubectl delete pod nodehelloworld.example.com

Poista service

    kubectl delete service nodehelloworld-service

Mene podiin

    kubectl attach nodehelloworld.example.com -i

Kontainerin hakemiston sisältö

    kubectl exec nodehelloworld.example.com -- ls

Komentoriville pod:iin

    kubectl exec -it nodehelloworld.example.com -- bash

Lokit

    kubectl logs nodehelloworld.example.com

Palvelun tila

    kubectl describe service nodehelloworld-service

(Viane 2018, luku 2.)

<a name="aws"></a>

# Kubernetesin asennus AWS:ään

Asennuksessa on noudatettu Udemy kurssin Learn DevOps-materiaalia lähdeviitteellä (Viane 2018). Ilman kyseistä materiaalia asennus olisi ollut huomattavasti vaikeampi.

Kubernetes klusterin asentaminen AWS-palveluun osoittautui silti melko haastavaksi, mutta usean yrityksen jälkeen saimme sen sinne toimimaan.

Asensimme Kubernetes klusterin kops:in avulla AWSään. Kops tarkoitta Kubernetes Operations ohjelmaa, ja käytimme sitä klusterin hallintaan AWSssä.

Docker Hubissa meillä oli käytössä aiemmin luomamme image: "immonju/helloworld".

<a name="esiehdot"></a>

## AWS asennuksen esiehdot

Seuraavassa käymme läpi vaiheet, jotka asentamiseen tarvitaan. 

### Tarvittavat tunnukset ja asennukset

AWS tunnus
- Palvelun hallintaan AWS alustalla

kubectl omalle koneelle
- Kubernetes hallintatyökalu

kops asennus omalle koneelle
- Kubernetes hallintatyökalu

Awscli tool omalle koneelle
- AWS hallintaan

- AWS tunnukselle seuraavat oikeudet: 
  - AmazonEC2FullAccess
  - AmazonRoute53FullAccess
  - AmazonS3FullAccess
  - IAMFullAccess
  - AmazonVPCFullAccess
  - Asentamisen helpottamiseksi päädyimme käyttämään administrator oikeustasoa.

DNS domain nimi
- Halusimme tehdä asennuksen loppuun asti, ja tarjota testisovelluksellemme myös domain nimen.

### Käytetty ympäristö

Asensimme palvelut bento/ubuntu-16.04 Vagrant koneelle.
- kubectl
- kops
- Awscli

Nimipalvelu meilä oli NameCheap palvelusta.

<a name="aws-kubectl"></a>

## Asenna kubectl

Asenna kubectl aiemmin olleen ohjeen mukaan.

<a name="kops"></a>

## Asenna kops

Kops on työkalu, jolla voi tehdä tuotantotason Kubernetes asennuksia, päivityksiä ja ylläpitoa.

https://github.com/kubernetes/kops

Asennus 

```
curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
chmod +x kops-linux-amd64
sudo mv kops-linux-amd64 /usr/local/bin/kops

```

Testaus

```
$ kops version
```
<a name="awscli"></a>

## Asenna ja konfiguroi awscli

### Asennus

AWSCLI asennuksessa noudatimme seuraavia ohjeita:
https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html  
Lähdeviite (Amazon Web Services 2019)

Vaiheet on käyty seuraavissa kappaleissa läpi.

#### Asenna pip3

Tarkista ensin Python versio
- python3 --version

Tarvittaessa tarvitset python3-disutils, jonka voit asentaa alla olevalla komennolla.

```
sudo apt-get install python3-distutils
```

Asenna pip3

```
curl -O https://bootstrap.pypa.io/get-pip.py
python3 get-pip.py --user
```

Tarkista ympäristömuuttuja, jotta siellä on polku pip3:een. Saatat mahdollisesti tarvita komentoa:

```
export PATH=~/.local/bin:$PATH
```

Testaus
```
pip3 --version
```

#### Awscli asennus

```
$ pip3 install awscli --upgrade --user
```

testaus
```
$ aws --version
```


### Konfigurointi

Konfiguroimiseen tarvitaan AWS tunnus. Loimme sen AWS:ssä. Tämän jälkeen piti luoda IAM tunnus, jota varsinaisesti käytetään palveluiden deploymentissa AWSään. Lähteenä ja apuna on ollut Viane 2018.

![AWS services](https://github.com/immonju1/pilvipalvelut/blob/master/pics/AWS_services.png)

Kuva 4. AWS palvelut näkymä.

#### IAM käyttäjän luominen

Kirjaudu sisään AWS:ään ja mene:

- Identity and access management (IAM) -> users -> create new user.

Loimme käyttäjän nimellä kops. Käyttäjän luomisen jälkeen saadaan access key ja secret access key käyttäjälle. 

![IAM user](https://github.com/immonju1/pilvipalvelut/blob/master/pics/IAM_user.png)

Kuva 5. IAM käyttäjä.

#### Avainten konfigurointi

Konfiguroidaan komentoriviltä tämän jälkeen avaimet, jotka saadaan AWS:stä.

```
$ aws configure
AWS Access Key ID [None]: **************
AWS Secret Access Key [None]: **************
Default region name [None]: 
Default output format [None]:
```

Tarkistus

ls -ahl ~/.aws/

Käyttäjälle pitää antaa tämän jälkeen lisää oikeuksia AWS konsolissa.

Annoimme seuraavat oikeudet
- administration access

### Luodaan S3 bucket

S3 buckettia tarvitaan konfiguraation ja tilan tallentamiseen.

Käyttöliittymän kautta create new bucket. 

bucket-name: kops-state-a1703033

![S3 bucket](https://github.com/immonju1/pilvipalvelut/blob/master/pics/S3_bucket.png)

Kuva 6. S3 bucket.

### Seuraavaksi domain nimen luominen

Domain nimi luodaan WAS:n route 53 palvelussa.

Route 53 -> DNS management -> create hosted zone

Domain name
- kubernetes.juhaimmonen.com

![DNS](https://github.com/immonju1/pilvipalvelut/blob/master/pics/dns.png)

Kuva 7. DNS zone.

Tämän jälkeen asensimme NameCheapissa nimipalveluun AWSstä saadut recordit, jotta domain nimemme ohjautuu Route 53 nimipalvelimille.

### Luodaan SSH avaimet

Seuravaksi loimme SSH avaimet , joita tarvitaan loginia varten, kun loggaudutaan klusterin nodeille.

Yksityinen avain

```
ssh-keygen -f .ssh/id_rsa
```
<a name="klusterin_luonti"></a>

## Klusterin luominen

Kubernetes klusterin luominen AWS:ään

```
kops create cluster --name=kubernetes.juhaimmonen.com --state=s3://kops-state-a1703033 --zones=eu-central-1a --node-count=2 --node-size=t2.micro --master-size=t2.micro --dns-zone=kubernetes.juhaimmonen.com
```

Jos zonen kanssa ongelmia, oman saa haettua
```
aws ec2 describe-availability-zones --region eu-central-1
```

Komennon jälkeen klusteri pitää vielä julkaista.

```
kops update cluster kubernetes.juhaimmonen.com --yes --state=s3://kops-state-a1703033
```

Klusterin validointi
```
kubectl get node
```
```
NAME                                             STATUS   ROLES    AGE   VERSION
ip-172-20-56-105.eu-central-1.compute.internal   Ready    node     2m    v1.11.7
ip-172-20-60-110.eu-central-1.compute.internal   Ready    node     3m    v1.11.7
ip-172-20-63-171.eu-central-1.compute.internal   Ready    master   4m    v1.11.7

```

Voi tarkistaa myös kommennolla

```
kops validate cluster --state=s3://kops-state-a1703033 
```
```
vagrant@vagrant:~$ kops validate cluster --state=s3://kops-state-a1703033 
Using cluster from kubectl context: kubernetes.juhaimmonen.com

Validating cluster kubernetes.juhaimmonen.com

INSTANCE GROUPS
NAME			ROLE	MACHINETYPE	MIN	MAX	SUBNETS
master-eu-central-1a	Master	t2.micro	1	1	eu-central-1a
nodes			Node	t2.micro	2	2	eu-central-1a

NODE STATUS
NAME						ROLE	READY
ip-172-20-56-105.eu-central-1.compute.internal	node	True
ip-172-20-60-110.eu-central-1.compute.internal	node	True
ip-172-20-63-171.eu-central-1.compute.internal	master	True

Your cluster kubernetes.juhaimmonen.com is ready
```

SSH yhteys masteriin aiemmin luotuja SSH-avaimia käyttäen.
```
ssh -i ~/.ssh/id_rsa admin@api.kubernetes.juhaimmonen.com
```

<a name="lb"></a>

## Pod, Service ja Loadbalancer

Kaikki mitä voidaan tehdä kubectl:llä, voidaan laittaa myös .yml tiedostoon. Kokeilimme Podin ja Servicen luomista konfiguraatiotiedostojen (manifest) avulla.

Loadbalancerin jouduimme luomaan, koska halusimme testisovellukselle domain nimen käyttöön. Ilman loadbalaceria se ei ole mahdollista.

### Luodaan Pod ja Service

Tarvittavat manifestit Podin ja Servicen luomiseen. Lähteenä Viane 2018, luku 1.

pod: helloworld.yml

```
apiVersion: v1
kind: Pod
metadata:
  name: nodehelloworld-pod
  labels:
    app: helloworld
spec:
  containers:
  - name: kubernetes-demo
    image: immonju/helloworld
    ports:
    - name: nodejs-port
      containerPort: 8080
```

service: helloworld-service.yml
- Lisää loadbalancerin

```
apiVersion: v1
kind: Service
metadata:
  name: helloworld-service
spec:
  ports:
  - port: 80
    targetPort: nodejs-port
    protocol: TCP
  selector:
    app: helloworld
  type: LoadBalancer
```

### Luodaan Pod AWS

Klusteri luotiin jo aikaisemmin Kopsin avulla. Luodaan Pod klusteriin.

    kubectl create -f helloworld.yml

Tarkista podin luominen seuraavalla komennolla.

```
kubectl get pod
```

### Oikeuksien lisääminen Loadbalancerin luomiseen

Loadbalancer luonti ei onnistunut Servicelle, joten jouduimme luomaan lisää oikeuksia IAM käyttäjälle.
- Puuttuva rooli oli elasticloadbalancing

Tämä ei selvinnyt muuten kuin virheilmoitusta tutkimalla, ja AWS dokumentaatioon perehtymällä.

Oikeuksien lisäämisen jälkeen LB luonti onnistui Servicen luonnilla.

### Luodaan Service

Palvelun luominen sovellukselle.

    kubectl create -f helloworld-service.yml

Nyt AWS:n EC2:ssa pitäisi näkyä Masterin ja Nodejen lisäksi loadbalancer.


Seuraavalla komennolla voi hakea servicen tiedot. Komennolla pystyt varmistamaan, että load balancer on luotu palveluun.

```
$ kubectl describe service helloworld-service

Name:                     helloworld-service
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=helloworld
Type:                     LoadBalancer
IP:                       100.65.161.183
LoadBalancer Ingress:     a94279ba2442911e98e700288b3b3c66-38697140.eu-central-1.elb.amazonaws.com
Port:                     <unset>  80/TCP
TargetPort:               nodejs-port/TCP
NodePort:                 <unset>  32396/TCP
Endpoints:                100.96.1.4:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type    Reason                Age   From                Message
  ----    ------                ----  ----                -------
  Normal  EnsuringLoadBalancer  20m   service-controller  Ensuring load balancer
  Normal  EnsuredLoadBalancer   20m   service-controller  Ensured load balancer
```

<a name="domain"></a>

## Domain nimen testaaminen

domain nimi oli kubernetes.juhaimmonen.com

Route 53:ssa voi luoda alias-nimen loadbalancerille. Tämä avulla saadaan liikenne ohjattua domain nimellä sovellukselle.

Klikkaa Create Record Set ja syötä domain

    helloworld.kubernetes.juhaimmonen.com

Määritä tämä vielä aliakseksi Loadbalancerille.

Tämä jälkeen ohjelma vastaa osoitteessa: helloworld.kubernetes.juhaimmonen.com

![helloworld form AWS with LB](https://raw.githubusercontent.com/immonju1/pilvipalvelut/master/pics/aws_lb_helloworld.png)
Kuva 8. Hello World.

<a name="poista"></a>

## Poista klusteri

Pelkkä klusterin poisto riittää, samalla poistuvat Pod:it ja Service, sekä Loadbalancer.

Klusterin poisto

```
kops delete cluster kubernetes.juhaimmonen.com --state=s3://kops-state-a1703033
```

 <a name="Yhteenveto"></a>

# Yhteenveto 

Projektimme oli hyvin laaja, joten saimme vain pintaraapaisun Dockeriin ja Kubernetesiin. Projektin aikana meille hahmottui paremmin niiden toiminta sekä kokonaisuudessaan mikropalveluiden hyödyntäminen sovelluskehittämisessä.

Projektin aikana opimme miten Dockerin, Minikuben ja Kubernetesin asennus onnistuu.  Saimme myös tuntumaa Amazon Web Services pilvipalveluiden tarjontaan.  Projektin laajan aiheen seurauksena meille tuli tutuksi tapamme oppia ja etsiä tietoa sekä miten pilkkoa isoa aihe-aluetta pienempiin osiin oppimisen helpottamiseksi.

Projektissa onnistuimme tavoitteissamme. Projektia voisimme jatkaa pidemmälle tutustumalla syvemmin tekniikoihin esimerkiksi kontittamalla isompia sovelluksia Dockerilla ja hallinnoimalla niitä Kubernetesillä. Amazon Web Services -palvelut myös tarjoavat paljon eri palveluita joihin voisi syvemmin tutustua. 


# Lähteet
Amazon Web Services 2019. Install the AWS CLI on Linux. Luettavissa: https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html. Luettu: 

Aqua Security 2018. Kubernetes Architecture 101. Luettavissa: https://www.aquasec.com/wiki/display/containers/Kubernetes+Architecture+101. Luettu: 25.3.2019.

DevOps Fundamentals-koulutus 28.8.2017 – 30.8.2017. Kouluttaja Kai Jokiniemi, Eficode.

Docker inc 2019a. About images, containers, and storage drivers. Luettavissa: https://docs.docker.com/v17.09/engine/userguide/storagedriver/imagesandcontainers/. Luettu: 31.3.2019.

Docker inc 2019b. Dockerfile reference. Luettavissa: https://docs.docker.com/engine/reference/builder/. Luettu: 31.3.2019.  

Docker inc 2019c. Luettavissa: https://www.docker.com/products/docker-hub. Luettu: 31.3.2019.  

Docker inc 2019d. Docker run. Luettavissa: https://docs.docker.com/engine/reference/commandline/run/. Luettu: 31.3.2019.

Goasguen, Sébastien 2015a. Docker Cookbook. O'Reilly Media. eBook.

Goasguen, Sébastien 2017b. Kubernetes Fundamentals. O'Reilly. ebook.

Kohli, Vaibhav, Dua, Rajdeep & Wooten, John 2017. Troubleshooting Docker. Packt Publishing. eBook.

Kousa, Jami. Devops with Docker. Luettavissa: https://docker-hy.github.io/part1/. Luettu: 31.3.2019.

McKendrick, Russ, Chelladhurai, Jeeva S., Raj, Pethuru & Singh, Vinod 2017. Docker Bootcamp. Packt Publishing. eBook.

Node.js Foundation. Dockerizing a Node.js web app. Luettavissa: https://nodejs.org/de/docs/guides/nodejs-docker-webapp/. Luettu: 4.3.2019

Poulton, Nigel 2018. The Kubernetes Book: version 3. eBook. 

Viane, Edward 2018. Learn DevOps: The complete Kubernetes course. Udemy.

Warnock, Braine E.E. 2018. Learning Kubernetes. Packt Publishing. eBook.

What is Kubernetes. https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/. Luettu 7.3.2019.

Raj, Pethuru, Chelladhurai, Jeeva S. & Singh, Vinod 2015. Learning Docker. Packt Publishing. eBook.

The Kubernetes Authors 2019a. Install Minikube. Luettavissa: https://kubernetes.io/docs/tasks/tools/install-minikube/ Luettu: 21.2.2019

The Kubernetes Authors 2019b. Install and Set Up kubectl. Luettavissa: https://kubernetes.io/docs/tasks/tools/install-kubectl/ Luettu: 28.2.2019


