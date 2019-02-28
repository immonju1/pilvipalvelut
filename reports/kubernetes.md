
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

Toinen vaihtoehto on Docker Client?

## Asennus

https://kubernetes.io/docs/tasks/tools/install-minikube/

- Vaatii VMn koneelle

Asennuksen jälkeen 

minikube start

minikube status

Konfiguraatiotiedosto syntyy ~/.kube hakemistoon

## Asenna kubectl

https://kubernetes.io/docs/tasks/tools/install-kubectl/

Komentorivi työkalu sovellusten hallintaan Kubernetesissä.

## Komentoja

kubectl 

kubectl cluster-info

# Kubernetesin asennus AWS:ään

Docker Hubiin pitää viedä image, jota voidaan ajaa AWSssä.

Tähän tarkoitukseen on aiemmin luoto Hello World image, joka /scrpits hakemistossa.


