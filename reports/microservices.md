# Mikropalvelut (draft)

## Yleistä

DevOpsista jotain.

Kubernetesin avulla on helppo viedä tuotantoon hyvin erilaisia sovelluksia. Sovellukset voivat olla monoliitteja, joilla ei ole mitään tekemistä toistensa kanssa. Tai mikroserviceitä, pieniä palveluita, jotka yhdessä muodostavat sovelluksen.

Mikropalveluarkkkitehtuurin suosio kasvaa jatkuvasti. Tämä lähestymistapa antaa kehittäjille mahdolisuuden pilkkoa sovellus pieniin itsenäisiin osiin. Lukuisten mikropalveluiden hallinta voi aiheuttaa palveluiden hallinnalle ongelmia. Kuinka hallita ja operoida kymmeniä, mahdollisesti satoja eri mikropalveluita, voi olla ongelmallista.

Tähän lisää ja paremmin. Hyödyt jne.

Samaan aikaan tuotannossa eri versioita palveluista, liikenne voidaan ohjata eri versioille ja verrata vaikka toimintaa.

****

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



