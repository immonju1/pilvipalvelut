
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

Tarvitaan image, jota voidaan, joten ensin luodaan Docker image Docker Hubiin.

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
- AWS tunnukselle seuraavat oikeudet: AmazonEC2FullAccess, AmazonRoute53FullAccess, AmazonS3FullAccess, IAMFullAccess, AmazonVPCFullAccess
- DNS domain nimi, jos halutaan käyttää

## Asenna kubectl

Asenna kubectl aiemmin olleen ohjeen mukaan

## Asenna kops
```
$ curl -LO https://github.com/kubernetes/kops/releases/download/1.10.0/kops-linux-amd64
$ chmod +x kops-linux-amd64
$ mv kops-linux-amd64 /usr/local/bin/kops
```

Testaus
```
$ kops version
```

## Asenna ja konfiguroi awscli

### Asennus

```
 $ sudo apt-get install awscli -y
```

### Konfigurointi

Tarvitaan AWS tunnus yllä mainituilla oikeuksilla.

```
$ aws configure
AWS Access Key ID [None]: **************
AWS Secret Access Key [None]: **************
Default region name [None]: enter-your-region-here
Default output format [None]:

```
### Luodaan S3 bucket

```
$ aws s3 mb s3://cluster1.k8s.juhaimmonen.com
```

Tarvitaan konfiguraation ja tilan tallentamiseen

Tänne syötetään klusterin DNS nimi, olkoon tässä vaiheessa:

k8s.juhaimmonen.com

Alidomain pitää asettaa AWSn Route 53:ssa 

Alidomainilla voi olla sitten klustereita omilla nimillään

cluster1.k8s.juhaimmonen.com

### Tarkista S3 bucket

```
$ aws s3 ls | grep k8s
```

### Mistä kops löytää konfiguraatiot

```
$ export KOPS_STATE_STORE=s3://cluster1.k8s.juhaimmonen.com
```

### Luodaan klusteri

```
 $ kops create cluster \
  --cloud=aws \
  --zones=eu-west-1b \
  --dns-zone=k8s.tf1.com \
  --name cluster1.k8s.tf1.com  \
--ssh-public-key ~/np-k8s.pub \
  --yes
```

### Testaus

```
 $ kops validate cluster
 ```

## AWS Kubernetes klusterin poistaminen

Maksullinen, joten poista kun ei tarvita






 
