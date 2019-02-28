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
Lähteet:
https://kubernetes.io/docs/tutorials/kubernetes-basics/
https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/

------

# Docker Desktop For Windows and Kubernetes
- Kubernetes on saatavilla Docker Desktop 18.06.0-ce-win70 CE ja uudemmissa

- Minikube on mahdollista asentaa Windowsille Chocolateyn kautta. Docker Desktop on Windowsille ja siinä on valmiina Kubernetes sisällytetty.

- Kubernetes palvelin pyörii lokaalisesti Docker instanssissa ja on single-node kluster. Kubernetes ei ole konfiguroitavissa.

## Mitä kannattaa tietää ennen asentamista
- Docker Desktop tarvitsee Microsoftin Hyper-V:n
- Kun Hyper-V on sallitty, VirtualBox ei toimi 


# Minikuben asentaminen Macille

- You can see if your CPU supports Hypervisor.framework by checking the value of the sysctl kern.hv_support key
```
$ sysctl kern.hv_support
	kern.hv_support: 1
```
Jos tulos on 1, CPU on tuettu ja jos tulos on 0 Hypervisor.frameworkia ei pysty käyttämään CPU:n kanssa.


- Install homebrew. Homebrew on paketinhallinta ohjelma, joka asentaa paketit omaan hakemistoon.
https://brew.sh/

- Install Kubectl
```
$ brew install kubernetes-cli
```
```
$ brew cask install minikube
@ start minikube
```
Minulla oli VirtualBox asennettuna. Minikube käyttää oletuksena VirtualBoxia.


## Deploy Hello World Node.js sovellus

- Pod = kaikki tarvittava mitä sovellus tarvitsee pyöriäkseen
- Tässä emme luo pod:ia vaan deployment, jolloin pod luodaan taka-alalla. 

Avasin Kubernetes dashboardin:
```
$ minikube dashboard
```

Deployasin hello world node.js sovelluksen komennolla:
```
$ kubectl run hello-minikube --image=k8s.gcr.io/echoserver:1.10 --port=8080
```

hello-minikube ilmaantuu Kubernetesin dashboardille.

Expose the deployment as a service:
```
$ kubectl expose deployment hello-minikube --type=NodePort
```

Näytä hello-minikube services tiedot tai katsele dashboardin kautta:
```
$ kubectl get services hello-minikube
```

Katsele Pods 
```
$ kubectl get pod
```
Kun pod on running -tilassa voidaan käyttää curl -komentoa.
```
$ curl $(minikube service hello-minikube --url)
```
Tai ip ja portti selainta varten.
```
$ minikube service webserver --url
```

## Sulje klusteri
Poista service:
```
$ kubectl delete service hello-minikube
```
Poista deployment:
```
$ kubectl delete deployment hello-minikube
```
Pysäytä minikube:
```
$ minikube stop
```

## Käytä Minikuben sisäänrakennettua Docker daemon (Built-in docker daemon)
https://gist.github.com/kevin-smets/b91a34cea662d0c523968472a81788f7

--------
Lähteet:
http://www.openkb.info/2018/10/how-to-get-started-with-minikube-on-mac.html

https://kubernetes.io/docs/setup/minikube/#quickstart
