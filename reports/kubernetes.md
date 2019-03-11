
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

Tarvitaan AWS tunnus. Luodaan sellainen AWS:ssä. Tämän jälkeen pitää luoda IAM tunnus.

![AWS services](https://github.com/immonju1/pilvipalvelut/blob/master/pics/AWS_services.png)

Kuva. AWS palvelut näkymä

Kirjaudu sisään ja mene:

- Identity and access management (IAM) -> users -> create new user.

Luodaan käyttäjä nimellä kops. Käyttäjän luomisen jälkeen saadaan access key ja secret access key käyttäjälle. Ole huolellinen näiden kanssa, ne voi tallentaa omalle koneelle .cvs formaatissa.

![IAM user](https://github.com/immonju1/pilvipalvelut/blob/master/pics/IAM_user.png)

Kuva. IAM käyttäjä.

Konfiguraoidaan komentoriviltä tämän jälkeen avaimet:

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

Tarvitaan konfiguraation ja tilan tallentamiseen.

![S3 bucket](https://github.com/immonju1/pilvipalvelut/blob/master/pics/S3_bucket.png)

Kuva. S3 bucket.

### Seuraavaksi DNS

AWS: Route 53 -> DNS management -> create hosted zone

Domain name
- kubernetes.juhaimmonen.com

![DNS](https://github.com/immonju1/pilvipalvelut/blob/master/pics/dns.png)

Kuva. DNS zone

Tämän jälkeen pitää asettaa nimipalveluun recordit, jotta osoite ohjautuu Route 53 nimipalvelimille.

### Luodaan SSH avaimet

Tarvitaan loginia varten, kun loggaudutaan klusterin nodeille.

Yksityinen avain

```
$ ssh-keygen -f .ssh/id_rsa
```

### Luodaan klusteri

```
kops create cluster --name=kubernetes.juhaimmonen.com --state=s3://kops-state-a1703033 --zones=eu-central-1a --node-count=2 --node-size=t2.micro --master-size=t2.micro --dns-zone=kubernetes.juhaimmonen.com
```

Jos zonen kanssa ongelmia, oman saa haettua
```
aws ec2 describe-availability-zones --region eu-central-1
```

Komennon jälkeen klusteri pitää vielä julkaista.

```
$ kops update cluster kubernetes.juhaimmonen.com --yes --state=s3://kops-state-a1703033
```
### Validointi

Status saadaan selville
```
kubectl get node
```

Pitäisi näkyä master ja kaksi nodea. Luonti voi kestää jonkin aikaa.

```
 * validate cluster: kops validate cluster
 * list nodes: kubectl get nodes --show-labels
 * ssh to the master: ssh -i ~/.ssh/id_rsa admin@api.kubernetes.juhaimmonen.com
```
kops luo konfiguraatiot tiedostoon

```
- cat ~.kube/config
```

### Testaus

Deployment
```
$ kubectl run hello-minikube --image=k8s.gcr.io/echoserver:1.4 --port=8080
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

IP osoite AWSstä. Tänne asti pääsin, mutta sitten ei toiminut selaimesta...

## AWS Kubernetes klusterin poistaminen

Maksullinen, joten poista kun ei tarvita. Tein poiston.

```
$ kops delete cluster kubernetes.juhaimmonen.com --state=s3://kops-state-a1703033
```
Klusterin voi luoda uudestaan: ``kops create cluster-komennolla``


# Podin luominen

Harjoitus tehdään **Minikukubessa**.

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

## Luodaan pod kubectl:n avulla

    kubectl create -f pod-helloworld.yml

Tulos

    pod/nodehelloworld.example.com created

## Tarkistus

    kubectl describe pod nodehelloworld.example.com

Näkee mistä hakee imagen.

```
Name:               nodehelloworld.example.com
Namespace:          default
Priority:           0
PriorityClassName:  <none>
Node:               minikube/10.0.2.15
Start Time:         Tue, 05 Mar 2019 18:14:47 +0200
Labels:             app=helloworld
Annotations:        <none>
Status:             Pending
IP:                 
Containers:
  kubernetes-demo:
    Container ID:   
    Image:          immonju/helloworld
    Image ID:       
    Port:           8080/TCP
    Host Port:      0/TCP
    State:          Waiting
      Reason:       ContainerCreating
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-2gck9 (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             False 
  ContainersReady   False 
  PodScheduled      True 
Volumes:
  default-token-2gck9:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-2gck9
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  43s   default-scheduler  Successfully assigned default/nodehelloworld.example.com to minikube
  Normal  Pulling    42s   kubelet, minikube  pulling image "immonju/helloworld"
  Normal  Pulled     25s   kubelet, minikube  Successfully pulled image "immonju/helloworld"
  Normal  Created    25s   kubelet, minikube  Created container
  Normal  Started    24s   kubelet, minikube  Started container
```

## Kuinka päädtä Podiin palveluun kiinni

Pääsy sovellukseen tapahtuu normaalisti servicen kautta. Toinen vaihtoehto servicelle on NodePort (suoraan noden porttiin). NodePort:ia voi käyttää jos ei ole LoadBalanceria. LB tarvitaan jos on useampi node.

Jos ei ole vielä serviceä olemassa, voidaan käyttää port-forward komentoa. 

    kubectl port-forward nodehelloworld.example.com 8081:8080

local port 8081 ohjautuu porttiin 8080.

Tarkista curl localhost:8081 toisessa shellissä.

toimii, tulos

```
$ curl localhost:8081

Hello world

```

## Luodaan Service Minikubeen

Podin luomisen jälkeen voidaan luoda service. Tarvitaan AWS kanssa, jos halutaan LB.

    kubectl expose pod nodehelloworld.example.com --type=NodePort --name nodehelloworld-service

Tulos
    service/nodehelloworld-service exposed

Palvelun URL

    minikube service nodehelloworld-service --url

```
$ minikube service nodehelloworld-service --url
http://192.168.99.100:32038
```

Saa URLin palveluun, AWS pitää katsoa instanssit (EC2), mikä on IP.

Komennolla

    kubectl get service

Näkee päällä olevat palvelut.

## Hyödyllisiä komentoja

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

Tulostus:

```
$ kubectl describe service nodehelloworld-service
Name:                     nodehelloworld-service
Namespace:                default
Labels:                   app=helloworld
Annotations:              <none>
Selector:                 app=helloworld
Type:                     NodePort
IP:                       10.97.112.60
Port:                     <unset>  3000/TCP
TargetPort:               3000/TCP
NodePort:                 <unset>  32038/TCP
Endpoints:                172.17.0.5:3000
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

Toisesta Pod:sta voi kutsua Endpoint: "172.17.0.5:3000"

## Luodaan toinen pod, toisella kontainerilla

    kubectl run -i --tty busybox --image=busybox --restart=Never -- sh

Pääsee suoraan konttiin sisälle.

Voi testata yhteyttä toiseen podiin. Koska budycubessa ei curlia, voi käyttää telnet 172.17.0.5:3000 (Endpoint)

# Loadbalancerin lisääminen AWSään (TODO)

Käytetään **AWS** tästä eteenpäin.

Kaikki mitä voit tehdä kubectl:llä voidaan laittaa .yml tiedostoon.

Luodaan pod ja palvelu.

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

## Luodaan pod AWSään

### Ensin pitää luoda klusteri.

```
kops create cluster --name=kubernetes.juhaimmonen.com --state=s3://kops-state-a1703033 --zones=eu-central-1a --node-count=2 --node-size=t2.micro --master-size=t2.micro --dns-zone=kubernetes.juhaimmonen.com
```

Komennon jälkeen klusteri pitää vielä julkaista.

```
$ kops update cluster kubernetes.juhaimmonen.com --yes --state=s3://kops-state-a1703033
```

### Luodaan Pod

    kubectl create -f helloworld.yml

### Luodaan Service

    kubectl create -f helloworld-service.yml

Nyt EC2:ssa pitäisi näkyä klusterin lisäksi loadbalancer.

### Oikeuksien lisääminen ELB luomiseen

LB luonti ei onnistunut Servicelle, joten loin lisää oikeuksia IAM käyttäjälle
- Puuttuva rooli oli elasticloadbalancing

Tämän jälkeen LB luonti onnistui Servicen luonnilla.


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

## Domain nimen testaaminen

domain nimi oli kubernetes.juhaimmonen.com

Route 53:ssa voi luoda alias-nimen loadbalancerille. Saadaan sovellus näkyviin domain nimellä internettiin.

Klikkaa Create Record Set ja syötä domain

    helloworld.kubernetes.juhaimmonen.com

Määritä tämä aliakseksi LB:lle. Surffasin osoitteeseen ja ohjelma toimi.

![helloworld form AWS with LB](pics/aws_lb_helloworld.png)

## Poista palvelu, podit ja klusteri

Pelkkä klusterin poisto riittää.

Poista service

    kubectl delete service helloworld-service

Poista pod

    kubectl delete pod nodehelloworld-pod

Klusterin poisto

```
$ kops delete cluster kubernetes.juhaimmonen.com --state=s3://kops-state-a1703033
```

