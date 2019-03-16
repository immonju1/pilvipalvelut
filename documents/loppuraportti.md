# Sisällyluettelo

1. [Johdanto](#Johdanto)
2. [Mikropalvelut](#mikropalvelut)
3. [Mikä on Docker](#mika-on-docker) 
    1. [Arkkitehtuuri](#arkkitehtuuri1)
    2. [Komentokieli, käytetyimmät komennot](#komennot)
    3. [Imaget, mistä saadaan](#imaget)
    4. [Konttien ajaminen](#kontit)
4. [Mikä on Kuberbetes](#mika-on-kubernetes)
    1. Minikube
    2. AWS asennus
    3. Arkkitehtuuri
5. [Yhteenveto](#Yhteenveto)

# Johdanto  <a name="Johdanto"></a>

Johdanto

# Mikropalvelut (draft) <a name="mikropalvelut"></a>

## Yleistä

DevOpsista jotain.

Kubernetesin avulla on helppo viedä tuotantoon hyvin erilaisia sovelluksia. Sovellukset voivat olla monoliitteja, joilla ei ole mitään tekemistä toistensa kanssa. Tai mikroserviceitä, pieniä palveluita, jotka yhdessä muodostavat sovelluksen.

Mikropalveluarkkkitehtuurin suosio kasvaa jatkuvasti. Tämä lähestymistapa antaa kehittäjille mahdolisuuden pilkkoa sovellus pieniin itsenäisiin osiin. Lukuisten mikropalveluiden hallinta voi aiheuttaa palveluiden hallinnalle ongelmia. Kuinka hallita ja operoida kymmeniä, mahdollisesti satoja eri mikropalveluita, voi olla ongelmallista.

Tähän lisää ja paremmin. Hyödyt jne.

Samaan aikaan tuotannossa eri versioita palveluista, liikenne voidaan ohjata eri versioille ja verrata vaikka toimintaa.

## Mikropalveluiden periaate 

Microservices-arkkitehtuurilla tarkoitetaan yksittäistä itsenäistä sovellusta, joka tarjoaa integraatiorajapinnan muille sovelluksille. Isompi ohjelmisto koostuu useista eri mikroserviceistä, päinvastoin kuin perinteinen ohjelmisto, joka koostuu yhdestä isosta sovelluksesta, joka tekee kaiken. (DevOps kurssi 2017.) Mikropalveluarkkitehtuuri on vaihtoehto monoliittiselle sovellukselle.

Sujuva ohjelmiston jatkokehitys edellyttää usein uusien teknologioiden liittämisen vanhaan järjestelmään. Mikäli järjestelmän osia voi vaihtaa pikkuhiljaa uusiin ja tarpeita vastaaviin, helpottuu ohjelmiston kehitys ja ylläpito. (DevOps kurssi 2017.) Tämä on mahdollista mikropalveluilla.

Lisäksi yksinkertainen palvelu on helppo kirjoittaa kokonaan uusiksi eikä muutaman palvelun uudelleenkirjoittaminen vie paljoa aikaa. Mikropalveuiden avulla voidaan myös korvata olemassaolevaa vanhaa teknologiaa tai korvata iso monoliitti pikkuhiljaa. (DevOps kurssi 2017.)

## Mikropalveluiden haasteita

Mikropalveluarkkitehtuuri voi johtaa toisaalta hyvin monimutkaiseen arkkiehtuuriin. Lukuisa joukko mikropalveluita kommunikoi keskenään, eikä ole selkeää kokonaiskuvaa siitä, mikä palvelu käyttää mitäkin toista palvelua. Tuloksena on melkoinen sekasotku, joka ei ole kenenkään ylläpidettävissä. (Viane 2018.)

Mikropalveluarkkitehtuurin tai toteutusten ongelmana on myös se, että ei ole salausta tietoliikenteessä mikropalveluiden välillä. Jos kommunikointi toisen mikropalvelun kanssa epäonnistuu, ei ole vikasietoisuutta, yhteyttä ei yritetä uudestaan. Vikasietoisuus pitää olla manuaalisesti koodattuna kuhunkin mikropalveluun. Sovelluksella tai mikropalveluilla ei ole älykästä kuormanjakoa, eli ei seurata mikropalveluiden latenssia, käytetään vain yksinkertaista algoritmia kuormanjakoon.  (Viane 2018.)

Mikropalveluiden välillä ei voida tehdä reitityspäätöksiä. Mikropalveluiden välisestä liikenteestä ei jää välttämättä lokeja, eikä liikennettä monitorointia. Mikropalveluiden välillä ei ole myöskään  pääsynvalvontaa, ellei sitä tehdä itse mikropalveluihin.  (Viane 2018.)

## Ratkaisu mikropalveluiden haasteisiin

Ratkaisuna aiemmin mainittuihin haasteisiin on toki tehdä mikropalveluihin itseensä kaikki tarvittavat asiat, mutta se vaatii jokaiselta mikropalveluiden kehittäjältä oman toteutuksen. Ratkaisuna voidaan käyttää proxyn lisäämistä kaikkiin mikropalveluihin. Kommunikointi tapahtuu proxyn avulla mikropalveluiden välillä. Proxy hoitaa salauksen ja vikasietoisuuden. Proxyissa voi olla myös kuormanjako-ominaisuuksia. (Viane 2018.)

Reititys mikropalveluiden välillä, siten että eri versioita mikropalveluista voi olla olemassa, voidaan hoitaa hallintakerroksen avulla (management interface). Samoin lokien keruu ja monitorointi tapahtuu hallintakerroksen avulla. (Viane 2018.)

# Lähteet

DevOps Fundamentals-koulutus 28.8.2017 – 30.8.2017. Kouluttaja Kai Jokiniemi, Eficode.

Viane, Edward 2018. Learn DevOps: The complete Kubernetes course. Udemy.


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

# Testisovellus

Testisovelluksemme on yksikertainen node.js sovellus, joka tulostaa Hello World!

??? Pitää muokata ???

## Docker file



## Testi Docker containerin luonti

Docker Hubiin pitää viedä ensin image, jota voidaan ajaa AWSssä tai Minikubessa.

Tähän tarkoitukseen on aiemmin luotu Hello World image, joka /scrpits hakemistossa.

Rakennetaan image
 ```
docker build -t helloworld .
 ```
Testataan

 ```
 docker run -d -p 8080:8080 helloworld
 ```
 
## Julkaistaan Docker Hub image 
 
 Luotu tunnus osoitteessa:   https://hub.docker.com/ 
 
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

# Mikä on Kubernetes  <a name="mika-on-kubernetes"></a>

??? Järjestys, millaiset kappaleet ???

Seuraavassa käydään läpi miten Kubernetes asennetaan käyttäen Minikubea.

Tämän jälkeen asennetaan Kubernetes klusteri ja ohjelma AWSään.

Viimeisessä kappaleessa käydään läpi Kubernetesin arkkitehtuuri.

## Minikube

Minikube on työkalu, jolla voi asentaa itselleen yksinkertaisen Kubernetes ympäristön. Toinen vaihtoehto on Docker Client.

Minikuben asentaminen vaatii virtuaalikoneen esim. Virtualbox.

Linkki Minikuben dokumentaatioon [](https://github.com/kubernetes/minikube/)

Raporteissamme on tarkemmin Minikuben asentamisesta ja testaamisesta. Linkki.

### Asentaminen

 ```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && sudo install minikube-linux-amd64 /usr/local/bin/minikube
 ```

Asennuksen jälkeen toiminnan voi tarkistaa.

 ```
minikube start

minikube status
 ```

### Kubectl

Kubectl on komentorivityökalu sovellusten hallintaan Kubernetesissä.

Lisää dokumentaatiota löytyy osoitteeesta [](https://kubernetes.io/docs/tasks/tools/install-kubectl/) ja raporteistamme. Linkki.

#### Kubectl asennus

Kubectl tarvitaan Kubernetes klusterin ohjaamiseen. Sen voi asentaa itselleen suraavilla komennoilla.

```
sudo apt-get update && sudo apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
 ```

Kubectl komentoja, joilla voi tarkistaa Minikuben asennuksen onnistumisen.

```
kubectl 

kubecluster-info
```

### Kubernetesin testaaminen Minikuben avulla

??? koko setti pitää testata ja tekstiä parantaa ???

#### Podin luominen

Luodaan ensin Pod, ja sitä varten tiedosto pod-helloworld.yml

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

#### Luodaan Service Minikubeen

Podin luomisen jälkeen voidaan luoda service.

    kubectl expose pod nodehelloworld.example.com --type=NodePort --name nodehelloworld-service

Palvelun URL saadaan selville komennolla, jonka jälkeen Web selaimella voi tarkistaa sovelluksen toiminnan.

    minikube service nodehelloworld-service --url

Komennolla

    kubectl get service

Näkee päällä olevat palvelut

#### Sovelluksen käyttäminen

Pääsy sovellukseen tapahtuu normaalisti servicen kautta. Toinen vaihtoehto servicelle on NodePort (suoraan noden porttiin). NodePort:ia voi käyttää jos ei ole LoadBalanceria. Loadbalancer tarvitaan jos on useampi node varattuna sovellukselle.

Jos ei ole vielä serviceä olemassa, voidaan käyttää port-forward komentoa, jolloin sovellusta käytetään suoraan Podin kautta.

    kubectl port-forward nodehelloworld.example.com 8081:8080

local port 8081 ohjautuu porttiin 8080.

Toinen vaihtoehto on käyttää servicen URL, joka mainittu edellisessä kappaleessa.

### Hyödyllisiä komentoja

???Näitä liikaa??? Vai taulukkoon ???

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


## Kubernetesin asennus AWS:ään

Kubernetes klusterin asentaminen AWS-palvelunn osoittautui melko haastavaksi, mutta usean yrityksen jälkeen saimme sen sinne toimimaan.



## Kubernetes arkkitehtuuri




# Yhteenveto  <a name="Yhteenveto"></a>



