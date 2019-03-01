
# Mikä on Kubernetes (kesken)

https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/

- Open source orkestraattori Docker kontainereille
- Useita kontainereita voi ajaa samalla koneella
- Kontainereissa voi olla esim. Web sovelluksia

Kubernetesin avulla hallinoidaan näitä kontainereita

- Kontainerin voi käynnistää tietylle nodelle
- Kontainereita voi uudelleenkäynnistää
- Kontainereita voi siirtää toiselle nodelle

# Minikube

https://github.com/kubernetes/minikube/

Minikube on työkalu, jolla voi asentaa itselleen yksinkertaisen Kubernetes ympäristön 

Toinen vaihtoehto on Docker Client.

## Asennus

https://kubernetes.io/docs/tasks/tools/install-minikube/

- Vaatii VMn koneelle, esim. Virtualbox

Asennus
 ```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && sudo install minikube-linux-amd64 /usr/local/bin/minikube
 ```

Asennuksen jälkeen 
 ```
minikube start

minikube status
 ```
Konfiguraatiotiedosto syntyy ~/.kube hakemistoon

## Testaus

todo

# Kubectl

## asennus

https://kubernetes.io/docs/tasks/tools/install-kubectl/

Kubectl on komentorivityökalu sovellusten hallintaan Kubernetesissä.

Asennus
 ```

sudo apt-get update && sudo apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
 ```

## Komentoja
 ```
kubectl 

kubectl cluster-info
 ```
 
## Testaus

todo
 
# Kubernetesin asennus AWS:ään

Asennetaan Kubernetes clusteri kops:in avulla AWSään. Kops tarkoitta Kubernetes Operations ohjelmaa.

Kubernetes clusterin asennus kops:lla pitäisi olla melko helppoa. muut DNS asetusten kanssa voi tulla haasteita. Tätä varten on olemassa mahdollisuus asentaa Kubernetes "gossib based", jolloin ei tarvita DNS nimeä.

Tarvitaan image, joten ensin luodaan Docker image Docker Hubiin. TODO: parempi testi-image?

## Testi Docker containerin luonti

Docker Hubiin pitää viedä ensin image, jota voidaan ajaa AWSssä.

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

## AWS asennuksen esiehdot

- AWS tunnus
- kubectl
- kops binaryt omalle koneelle
- awscli tool
- AWS tunnukselle seuraavat oikeudet: AmazonEC2FullAccess, AmazonRoute53FullAccess, AmazonS3FullAccess, IAMFullAccess, AmazonVPCFullAccess -> tässä asennuksessa laitetaan administrator.
- DNS domain nimi, jos halutaan käyttää
- Asennetaan bento/ubuntu-16.04 Vagrant koneelle

## Asenna kubectl

Asenna kubectl aiemmin olleen ohjeen mukaan.

## Asenna kops

Kops on työkalu, jolla voi tehdä tuotantotason Kubernetes asennuksia, päivityksiä ja ylläpitoa.

https://github.com/kubernetes/kops

```
curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
chmod +x kops-linux-amd64
sudo mv kops-linux-amd64 /usr/local/bin/kops

```

Testaus
```
$ kops version
```
Version 1.11.0 (git-2c2042465)


## Asenna ja konfiguroi awscli

### Asennus

Uusimman version saa näillä ohjeilla
https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html

#### Asenna pip3

Tarkista ensin Python versio
- python3 --version

Asenna pip3
```
$ curl -O https://bootstrap.pypa.io/get-pip.py
$ python3 get-pip.py --user
```
Testaus
```
$ pip3 --version
```

#### AWS asennus
```
$ pip3 install awscli --upgrade --user
```

testaus
```
$ aws --version
```

### Konfigurointi

Tarvitaan AWS tunnus. Luodaan sellainen.

Kirjaudu sisään ja mene:

- Identity and access management (IAM) -> users -> create new user.

Luodaan käyttäjä nimellä kops. Käyttäjän luomisen jälkeen saadaan access key ja secret access key käyttäjälle. Ole huolellinen näiden kanssa.

Komentoriviltä tämän jälkeen:

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

- administration access

### Luodaan S3 bucket

Käyttöliittymän kautta create new bucket. bucket-name: kops-state-a1703033

Tarvitaan konfiguraation ja tilan tallentamiseen

### Seuraavaksi DNS

AWS: Route 53 -> DNS management -> create hosted zone

Domain name
- kubernetes.juhaimmonen.com

Tämän jälkeen pitää asettaa nimipalveluun recordit, jotta osoite ohjautuu Route 53 nimipalvelimille.

### Luodaan SSH avaimet

Tarvitaan loginia varten, kun loggaudutaan klusteriin. 

Yksityinen avain

```
$ ssh-keygen -f .ssh/id_rsa
```

### Luodaan klusteri

```
kops create cluster --name=kubernetes.juhaimmonen.com --state=s3://kops-state-a1703033 --zones=eu-central-1 --node-count=2 --node-size=t2.micro --master-size=t2.micro --dns-zone=kubernetes.juhaimmonen.com
```

Komennon jälkeen klusteri pitää vielä julkaista.

```
$ kops update cluster kubernetes.juhaimmonen.com --yes --state=s3://kops-state-a1703033
```

status saadaan selville

```
kunectl get node
```

Pitäisi näkyä master ja kaksi nodea.

### Mistä kops löytää konfiguraatiot

kops luo konfiguraatio tiedostoon

```
- cat ~.kube/config
```

Onko tarpeen??:
```
$ export KOPS_STATE_STORE=s3://kops-state-a1703033
```

### Testaus

Deployment

```
$ kubectl run hello-minikube --image=k8s.grc.io/echoserver:1.4 --port=8080
```
Kytketään päälle nodet ja portit

```
$ kubectl expose deployment hello-minikube --type=NodePort
```
Testaus, ja nähdään portti

```
$ kubectl get services
```
AWS konsolista tarkistus

- Palvelu EC2
- instances
- autoscaling groups

### Palomuurit

Nodeilla on palomuurit, exposed portti pitää sallia

### Testaaminen WEb selaimella

IP osoite AWSstä

## AWS Kubernetes klusterin poistaminen

Maksullinen, joten poista kun ei tarvita

```
$ kops delete kluster kubernetes.juhaimmonen.com --state=S3://kops-state-a1703033
```






 
