# Kubernetes (Draft)

Kubernetes on Open source orkestraattori Docker kontainereille. Kontainereissa voi olla esim. Web sovelluksia. Kubernetesin avulla hallinnoidaan näitä kontainereita. (What is Kubernetes)

Docker ja Kubernetes täydentävät toisiaan, tyypillisesti sovellukset kontitetaan Dockerilla, ja Kubernetes orkestroi niitä. (Poulton 6) Kontainerina voi olla muukin kuin Docker. Kubernetesin avulla useita kontainereita voidaan ajaa samalla koneella, kontainerin voi käynnistää tietylle nodelle, kontainereita voi uudelleenkäynnistää ja kontainereita voi siirtää toiselle nodelle. (lähde ?)

Kubernetes tekee perinteiset ohjelmistojen tuotantoonviennit ja päivitykset helpoksi. Sovelluksille voi tehdä deploymentteja (tuotantoon vientejä), sovelluksen versiota voi päivittää, vikatilanteessa voidaan palata edelliseen sovellusversioon (roll back) ja päivityksiä on mahdollista tehdä katkottomasti, eli päivitys ei näy loppukäyttäjille. (Poulton 5) Sovellusta voidaan myös skaalata joustavasti.

Käyttäjän ei tarvitse välittää missä ja miten ohjelma pyörii, Kubernetes hoitaa sovelluksen ajoon haluttuun ympäristöön. (Poulton 10)

## Idea / teoria

vrt. Salt.

Määritykset tekstinä (declarative model)

Single source of truth

Idempotentti
Klusteri (nodet) pidetään halutussa tilassa, master hoitaa sen.

## Kubernetes käsitteet

Pod, Replica Set, Deployment, Service, Secret, Label, Voluumi, Nimialue

Vai vasta arkkitehtuurin jälkeen?

# Kubernetes Arkkitehtuuri

## Cluster

Kubernetesin cluster eli rykelmällä tarkoitetaan joukkoa tietokoneita, jotka ovat yhteydessä toisiinsa ja muodostaen yhden yksikön. Kuberenetes valjastaa kontitetut sovellukset rykelmälle ilman, että tarvitsee säätää jokaista konetta erikseen.

Kuva jostain kokonaisarkkitehtuurista.

## Master ja Nodet

Kubernetes klusteri rakentuu mastereista ja nodeista. Nämä voivat olla Linux servereitä, jotka voivat olla virtuaalikoneita tai fyysisiä palvelimia.

## Master

Master on vastuussa klusterin hallinnoinnista. Se myös monitoroi klusteria, ajaa muutokset sinne ja reagoi tapahtumiin klusterissa. (Poulton 15) HA ratkaisuissa mastereita voi olla useita.

Seuraavassa on esitelty Masterin eri komponentit.

#### API server (aivot)

API server tarjoaa REST rajapinnan klusterin käyttämiseen ja ohjaamiseen. Rajapintaan välitetään manifesteiksi kutsuttuja yaml-fomraatissa olevia konfiguraatiotiedostoja. (Poulton 16) Näiden avulla voidaan esimerkiksi viedä sovellus tuotantoon (deployment). 

#### Klusterin tietovarasto (muisti)

Klusterin hallinnollinen data ja konfiguraatiot säilötään etcd:n, joka on hajautettu tietokanta. etcd on "yksi totuus" klusterista.

#### Control Manager

Valvoo klusteria ja reagoi tapahtumiin. Tavoitteena on varmistaa, että klusteri on tilassa jossa sen halutaan olevan. (Poulton 17)

#### Scheduler

Scheduler jakaa sovellukset klusteriin, siten että sovelluksella on riittävästi resursseja käytössään. (Poulton 17)

## Node

Nodet ovat Kubernetes klusterin työyksiköitä. Niiden tehtävä on odottaa uusia tehtäviä API serveriltä, toteuttaa ne ja raportoida tilastaan takaisin Masterille. (Poulton 18)

Node rakentuu kolmesta eri komponentista.

### Kubelet

Kubelet ohjaa Noden toimintaa, sen tehtävän on kommunikoida masterin kanssa (API server) ja toteuttaa saadut tehtävät. Master päättää mitä tehdään, jos tarvitan esim. uusi Node Podeille. (Poulton 19) Esimerkiksi jos Pod terminoituu Nodella, Master päättää missä sitä yritetään ajaa seuraavaksi.

### Container runtime (CR)

Container Runtime tarvitaan konttien ajamiseen Nodella. CR hakee imaget ja käynnistää sekä pysäyttää ne. (Poulton 19) 

### Network / Kube proxy

Network Proxy huolehtii lokaalista verkosta Nodejen välillä. Esimerkiksi siitä, että jokaisella nodella on oma IP, sekä IPtables asetukset kohdallaan. (Poulton 20)

## Sovellusten pakkaaminen Kubernetesissa

Jotta sovellusta voidaan ajaa Kubernetes klusterissa, pitää sovelluksesta tehdä Docker image, tälle imagelle voidaan sitten tehdä deployment manifest-tiedoston avulla. (Poulton 20) Deployment tehdään Podin sisällä, joka on "kääre" kontainerille.

Kubernetes tarjoaa objekteja sovellusten hallintaan. Niiden avulla sovellus kapseloidaan. Kukin kapselointi tarjoaa omat abstraktiokerroksensa ja palvelunsa sovellukselle. Sovellus kapseloidaan kontin sisälle, kontti kapseloidaan Podin sisälle, Deployment kapseloi Podit nodella. Service kapseloi Deploymentit.

Deployment avulla sovellus voidaan skaalata (luoda tai vähentää podeja). Sen avulla myös Podeja voidaan käynnistää uudelleen, ja valvoa niiden toimintaa. (Poulton 20)

![Kubernetes Node](­kubernetes_node.png)
Kuva kapseloinnista (Poulton 20)

## Manifest ja tavoitetila

Sovelluksen tila kuvataan manifesteissa, jotka ovat yaml-formaatissa oelvia tekstitiedostoja. 

Nämä välitetään API serverille, ja tallennetaan halutuksi tilaksi etcd tietovarastoon. 

API Server ohjaa sovelluksen oikeaan tilaan klusterissa.

Valvonta asetetaan päälle, eli verrataan sovelluksen nykyistä tilaa haluttuun  tilaan. (Poulton 20 koko kappale)

## Pods

Pod on Kubernetes maailmassa pienin yksikkö, joka voidaan viedä klusterin Nodelle. Docker maailmassa vastaava on kontti. 

### Arkkitehtuuri

Docker kontti kapseloidaan Podin sisälle. Pod on ajoympäristö konteille. Jos kontissa ajetaan useampaa konttia, ne jakavat saman IP-osoitteen. Kontit voivat kommunikoida Podin sisällä locahost verkossa. (Poulton 24)

Skaalautuvuuden kannalta on parempi, että yhdessä Podissa on ajossa vain yksi kontti. Tällöin voidaan tarvittaessa luoda uusia Podeja, ja sovellusta voidaan skaalta. Pod voi sijaita vain yhdellä nodella. (Poulton 27)

### Elinkaari

Podit luodaan, ne palvelevat aikansa ja jossain vaiheessa niiden toiminta lakkaa tai ne terminoidaan. Jos Podissa oleva sovellus lakkaa toimimasta, luodaan uusi Pod, johon ladataan sovelluksen image. (Poulton 27)

## Deployments

Deployment on ylemmän tason kapselointi Podeille, joka tarjoaa lisäpalveluita, kuten skaalautumisen, loppukäyttäjille näkymättömän versionvaihon (zero-downtime update) ja rollbackin aiempaan versioon. (Poulton 27)

## Services

Podit itsessään ovat epäluotettavia, sovellukset niissä saattavat vikaantua. Uusia podeja käynnistyy ja terminoituu. Service edustaa pysyvyyttä sovellukselle. Service kapseloi joukon Podeja (eri deploymenteissa) ja tarjoaa niille front-endin ulkomaailmaan. Nämä palvelut pitävät sisällään domain nimen, IP-osoitteet, portit ja kuormanjaon sovellukeselle. (Poulton 28)

Service pitää yllä kirjapitoa, missä podit ovat, tarjoten luotettavan endpointin sovellukselle. Vastaavasti vaikka Podeja skaalattaisiin ylös tai alaspäin, tietää Service käytössä olevat Podit ja sovelluksen käyttäjille skaalaus on näkymätön. (Poulton 28-29) 

Serice käyttää Labeleita kuorman jakamiseen Podeille, liikenne ohjataan niille Podeille, joilla on oikeat labelit. Servicet ohjaavat liikennettä vain toimiville Podeille. (Poulton 30)

# Lähteet

Poulton, Nigel 2018. The Kubernetes Book: version 3. eBook. 

Viane, Edward 2018. Learn DevOps: The complete Kubernetes course. 

What is Kubernetes. https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/. Luettu 7.3.2019.

Kube-scheduler. https://kubernetes.io/docs/reference/command-line-tools-reference/kube-scheduler/

Peltola, Reko 2017. Kubernetes-klusterin asennus ja käytöönotto. https://www.theseus.fi/bitstream/handle/10024/138954/Peltola_Reko.pdf?sequence=1&isAllowed=y

Sarakkala Jyri 2016. Kubernetes ja klusteroitava verkkosovellus. https://www.theseus.fi/bitstream/handle/10024/120976/Opinnayte_jyri_sakkara_final.pdf?sequence=1

Ellingwood 2016. 

