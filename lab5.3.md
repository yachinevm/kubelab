#  Lab 3 : Contrôler le Placement des Pods
Objectif : Apprendre à influencer le planificateur Kubernetes en utilisant nodeSelector et les Taints/Tolerations.

##  Partie A : nodeSelector
Étiquetez votre nœud Minikube pour lui donner une caractéristique spéciale :

```Bash

kubectl label nodes minikube disktype=ssd
```
Créez un fichier pod-ssd.yaml qui demande à tourner sur un nœud avec ce label :

```YAML

# pod-ssd.yaml
apiVersion: v1
kind: Pod
metadata:
  name: database-pod
spec:
  containers:
  - name: postgres-db
    image: postgres:13
    env:
    - name: POSTGRES_PASSWORD
      value: "password"
  nodeSelector:
    disktype: ssd
```
Déployez et vérifiez. Le Pod doit être planifié avec succès sur le nœud minikube.

```Bash

kubectl apply -f pod-ssd.yaml
kubectl get pods -o wide
```
Nettoyez le label et le pod :

```Bash

kubectl delete -f pod-ssd.yaml
kubectl label nodes minikube disktype-
```


##  Partie B : Taints et Tolerations
"Contaminez" votre nœud Minikube pour qu'il repousse les Pods normaux :

```Bash

kubectl taint nodes minikube special-workload=true:NoSchedule
```
Essayez de déployer un Pod normal. Il restera bloqué à l'état Pending.

```Bash

kubectl run nginx-test --image=nginx
kubectl get pods
```
Vous verrez que nginx-test ne peut pas être planifié.

Créez un fichier pod-toleration.yaml pour un Pod qui peut "tolérer" cette contamination :

```YAML

# pod-toleration.yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-special
spec:
  containers:
  - name: app
    image: busybox
    command: ["sleep", "3600"]
  tolerations:
  - key: "special-workload"
    operator: "Equal"
    value: "true"
    effect: "NoSchedule"
```
Déployez ce Pod. Il sera planifié avec succès.

```Bash

kubectl apply -f pod-toleration.yaml
kubectl get pods
```
Nettoyage final :

```Bash

kubectl delete pod nginx-test
kubectl delete -f pod-toleration.yaml
kubectl taint nodes minikube special-workload:NoSchedule
```
