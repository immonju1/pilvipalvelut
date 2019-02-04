# DevOps - Docker ja Kubernetes mikropalveluarkkitehtuurin mahdollistajina
***Pilvipalvelut kurssin projektityö kevät 2019***   
* [Juha Immonen](https://github.com/immonju1) [Niina Villman](https://github.com/niinavi)

### Projektin tausta

Projekti on harjoitustyö Haaga-Helian kurssille pilvipalvelut. Valitsin aiheen oman projektin aiheeksi, koska haluan oppia enemmän DevOps:sta. DevOps on, sekä tapa organisoida töitä ohjelmistokehityksessä, että joukko uusia teknologioita, joiden avulla sovellusten hallintaan liittyviä tehtäviä automatisoidaan. DevOpsin tavoitteena on nopeuttaa sovelluskehityssykliä (Dev), eli viedä tuotantoon mahdollisimman nopeasti. Samalla sen tavoitteena on automatisoida tuotantoonvientiin liittyvät (Ops) taskit. Teknologian ohessa DevOpsiin liitty myös siilojen madaltaminen, kehitys- ja tuotantopuolen ihmiset työskentelevät mielellään samoissa tiimeissä.

Palveluiden nopeampi tuotantoonvienti ja kehittäminen vaatii myös uudenlaista arkkitehtuuria. Isojen monoliittisten sovellusten sijaan tarvitaan arkkiehtuuri, joka mahdollistaa pienten itsenäisten palveluiden kehittämisen ja tuotantoonviennin. Arkkitehtuuria kutsutaan mikropalveluarkkitehtuuriksi. Jokainen toiminnallisuus / palvelu on itsenäinen sovellus, joka tarjoaa rajapinnan muille palveluille. Kukin mikropalvelu voidaan viedä tuotantoon riippumattaa muista mikropalevluista. Yhdessä mikropalvelut muodostavat varsinaisen sovelluksen. Mikropalvelut lisäävät tarvetta automaatiolle, helpolle tavalle viedä mikropalvelut tuotantoon. Samalla palveluiden skaalautuminen tarpeen mukaan helpottuu.

DevOpsiin liittyy lukuisia teknisistä ratkaisuista. Projektin aikana tulen opettelemaan DevOps teknologioista Dockerin ja Kubernetesin. Näiden toiminnan testaamista varten tulen tekemään pienen testisovelluksen LAMP pinon päälle. Kokonaisuus on minulle täysin uutta asiaa, joten testisovelluksesta pyrin tekemään mikropalveluarkkitehtuurin mukaisen, jos aikataulu sen sallii. Ensijainen tavoite on saada testisovellus kontitettua Dockerin avulla ja vietyä se tuotantoon Kubernetesin avulla. Tähän pyrin tekemään oman alustan, mutta vaihtoehtona on käyttää olemassaolevia pilvipalveluita.

### Projektin tavoitteet

Projektin tavoitteena on oppia 
1. Docker-teknologia
- Asennus
- Arkkitehtuuri
- Docker kontin tekeminen
- Deployment 
2. Kubernetes
- Asennus
- Arkkitehtuuri
- Deployment
3. Tehdä testisovellus komponenttien testaamiseen
- Yksinkertainen LAMP pinon päällä toimiva ohjelma, joka hakee tietokannasta Web-sivulle sisältöä
4. Tutkia eri pilvipalveluita, jotka tarjoavat Dockerin ja Kubernetesin sovelluskehityseen 
- Google
- Docker

### Projektin eteneminen ja välitavoitteet

Projekti etenee seuraavasti
1. Esiselvitys
- Teknologioiden opiskelua (Docker, Kubernetes, mikropalvelut)
2. Docker kontitus LAMPlle
- Asennusohjeet Githubissa
3. Kubernetes asennus / pilvipalveluna
-  Asennusohjeet Githubissa
4. Lopullinen testisovellus, ja koko pinon testaaminen
- Kontitus, tuotantoonvienti Kubernetesin avulla
 
### Projektiorganisaatio ja sidosryhmät

Projektin omistaja on projektiryhmä, jonka jäseniä ovat Juha Immonen ja Niina Villman.

Projektin ohjaajana toimii Olavi korhonen.

### Alustava aikataulu

**Viikko**|**Taski**
--------|-------------
Viikko 5-6|Projektin aloitus
Viikko 7|Projektisuunnitelman palautus
Viikko 10|Esiselvitys valmis ja projektin edistymisraportti
Viikot 11-15|Projektin tekemistä
Viikko 15|Edistymisraportti 2
Viikko 17|Loppuraportti valmis
Viikko 18|Projektin tuotoksen esittely

### Projektisuunnitelma

[Tarkempi suunnitelma](https://github.com/immonju1/pilvipalvelut/blob/master/documents/projektisuunnitelma.md)

### Riskit ja vaatimukset etenemiselle

Riskit
- Tekniikoiden opiskelu vaatii enemmän aikaa kuin suunniteltu
- Asennuksissa tulee viiveitä / haasteita
- Ylipääsemätön tekninen ongelma

### Kommunikaatio

Projektilla on käytössä Github, jonne eteneminen, konfiguraatiot, koodit ja loppuraportti tullaan kirjaamaan.


