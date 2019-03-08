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

-----------------
# AWS ja Kubernetes

Toteutin testin VirtualBoxissa Xubuntu 18.04. 
Noudatin ohjeita täältä:  https://medium.com/containermind/how-to-create-a-kubernetes-cluster-on-aws-in-few-minutes-89dda10354f4 

Sekä sain muutaman vinkin Juhan raportista. Minulla ei ilmeisesti ole mahdollisuutta subdomainiin domain nimeni palveluntarjoalta.   Yritän myöhemmin uudestaan, jos kokeilen hankkia uuden domain nimen testi käyttöön. Onnistuin kuitenkin luomaan klusterin Kubernetesiin käyttämällä S3 bucketia. 

### Asenna kubectl
https://kubernetes.io/docs/tasks/tools/install-kubectl/
```
sudo apt-get update && sudo apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
```

### Asenna Kops

https://github.com/kubernetes/kops

```
curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
chmod +x kops-linux-amd64
sudo mv kops-linux-amd64 /usr/local/bin/kops
```

### Asenna AWS CLI
https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html

Asensin Python3 noudattamalla oheita alla näkyvästä linkistä. 
https://docs.aws.amazon.com/cli/latest/userguide/install-linux-python.html

```
$ sudo apt-get install python3
```

Asensin AWS CLI ja pip alla näkyvillä komennoilla:
```
$ curl -O https://bootstrap.pypa.io/get-pip.py
```
```
$ python3 get-pip.py --user
```
https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html


### Luo IAM user

Loin AWS palvelussa IAM käyttäjän, jonka lisäsin  ryhmään jossa on alla olevat oikeudet: 

```
AmazonEC2FullAccess
AmazonRoute53FullAccess
AmazonS3FullAccess
AmazonVPCFullAccess
IAMFullAccess
```

Suoritin konfiguraatio komennon
```
$ aws coniguration
```

Käytin security credentials (access key ID ja secret access key), jotka löytyvät AWS:sta IAM käyttäjän tiedoista.

```
AWS Access Key ID [None]: AccessKeyValue
AWS Secret Access Key [None]: SecretAccessKeyValue
Default region name [None]: eu-central-1
Default output format [None]:
```


## S3 Bucket

Loin S3 Bucketin AWS sivustolla.


8. Provide a name for the Kubernetes cluster and set the S3 bucket URL in the following environment variables:

export KOPS_CLUSTER_NAME=imesh.k8s.local
export KOPS_STATE_STORE=s3://${kops-test2-hh}

## Klusterin luonti

### SSH avain
Loin SSH avaimet alla näkyvällä komennolla, koska yhteys tarvitsee ne.
```
ssh-keygen -f .ssh/id_rsa
```

Alkuun sain erroria "unable to infer CloudProvider from Zones (is there a typo in --zones?) ". Joten etsin saatavilla olevia zoneja komennolla:

```
$ aws ec2 describe-regions
```

Komennon avulla huomasin, että zonesta puuttui a-kirjain. Komentoon lisäsin myös --state -kohdan, jotta sain sen toimintaan.


```
$ kops create cluster --state=s3://kops-test2-hh  --node-count=2 --node-size=t2.micro --zones=eu-central-1a --name=${KOPS_CLUSTER_NAME}

```

Clusterin konfiguraatio onnistui ja seuraavaksi ajoin komennon: 
```
$ kops update cluster --name ${KOPS_CLUSTER_NAME} --yes
```

Komento antoi kuitenkin erroria:  
" error reading cluster configuration: error reading cluster configuration "imesh.k8s.local": error reading s3://test2-hh/imesh.k8s.local/config: Could not retrieve location for AWS bucket test2-hh" 

Jouduin siis lisäämään lopppuun --state -kohdan.
kops update cluster --name ${KOPS_CLUSTER_NAME} --yes --state=s3://kops-test2-hh


Tämän komennon jälkeen sain tuloksia.
```
kops has set your kubectl context to imesh.k8s.local

Cluster changes have been applied to the cloud.


Changes may require instances to restart: kops rolling-update cluster
```

### Get nodes
```
$ kubectl get node
```

```
NAME                                             STATUS   ROLES    AGE   VERSION
ip-172-20-46-97.eu-central-1.compute.internal    Ready    node     4m    v1.11.7
ip-172-20-59-185.eu-central-1.compute.internal   Ready    master   5m    v1.11.7
ip-172-20-60-142.eu-central-1.compute.internal   Ready    node     4m    v1.11.7

```

Asensin Kuberenetes dashboardin.
```
$ kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
```

Käytin allaolevaa komentoa saadakseni adminin salasanan.

```
$ kops get secrets kube --type secret -oplaintext
```

Allaolevalla komennolla löysin Kubernetesin masterin hostnamen.
```
$ kubectl cluster-info
```

Kubernetesin dashboardiin pääsee seuraavalla:

```
$ https://<kubernetes-master-hostname>/ui
```
Käytin kirjautumiseen käyttäjätunnuksena admin ja salasanana yllä hankkimaani.

Pääsin kirjautumaan ja sivulle latautui jonkin näköistä JSONIa (?)..


Seurasin lähteitä:

https://medium.com/containermind/how-to-create-a-kubernetes-cluster-on-aws-in-few-minutes-89dda10354f4

