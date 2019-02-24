# Kubernetes

## Mikä on Kubernetes?

- Kubernetes on avoimen lähdekoodin alusta konttien hallinnointiin


### Cluster 

Kubernetesin cluster eli rykelmällä tarkoitetaan joukkoa tietokoneita, jotka ovat yhteydessä toisiinsa ja muodostaen yhden yksikön. Kuberenetes valjastaa kontitetut sovellukset rykelmälle ilman, että tarvitsee säätää jokaista konetta erikseen.

Master hallinnoi "clusteria" (rykelmää) ja "nodes" ovat sovellusta ajavia fyysisiä tai virtuaalisia koneita.


Minikube on hyvä tapa aloittaa Kubenernetesillä kehittäminen. Se on kevyt ja sillä pystyy luomaan yksinkertaisen clusterin, joka pitää sisällään yhden noden.
Täältä asentamisesta lisää tietoa:
https://kubernetes.io/docs/tasks/tools/install-minikube/

### Deploy an App

Kun on cluster valmiina voidaan ottaa käyttöön kontitetut sovellukset. Tarvitaan Deployment konfiguraatio, joka määrittää miten luodaan ja päivitetään sovelluksen instansseja ("instances of your application"). Master aikatauluttaa sovellusinstanssit jokaiseen clusterin nodeen.

Käyttöönotto (deployment) tapahtuu Kubenetesin komentorivikäyttöliittymän Kubectl avulla.



------
https://kubernetes.io/docs/tutorials/kubernetes-basics/
https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/
