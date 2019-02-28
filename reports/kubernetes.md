
# Mikä on Kubernetes

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

## Asenna kubectl

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
 
# Kubernetesin asennus AWS:ään

Docker Hubiin pitää viedä image, jota voidaan ajaa AWSssä.

Tähän tarkoitukseen on aiemmin luotu Hello World image, joka /scrpits hakemistossa.

Rakennetaan image
 ```
docker build -t helloworld .
 ```
Testataan

 ```
 docker run -d -p 8080:8080 helloworld
 ```

# konttien julkaisu Docker hubissa
 
 Luotu tunnus:   https://hub.docker.com/ 
 
 Luotu Dashboard: Create Repository
 
 Muutettu imagen nimi
 ```
 docker tag helloworld immonju/helloworld
 ```
 Login ja vienti repoon.
 ```
 docker login
 
 docker push immonju/helloworld
```


 
