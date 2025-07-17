#  Lab 2 : Gérer les Ressources (Requests/Limits) et la QoS
Objectif : Comprendre comment déclarer les besoins en ressources d'un Pod et observer l'impact des limites sur son comportement.

## Étape 1 : Créer un Pod avec une Limite de Mémoire
Créez un fichier pod-oom-lab.yaml. Ce Pod va tenter d'utiliser plus de mémoire que ce que sa limite autorise.

```YAML

# pod-oom-lab.yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-oom
spec:
  containers:
  - name: app
    image: debian
    # Cette commande essaie d'allouer 200Mo de mémoire
    command: ["/bin/```Bash", "-c", "dd if=/dev/zero of=/dev/null bs=200M"]
    resources:
      limits:
        # On lui donne une limite de seulement 100Mi
        memory: "100Mi"
```

## Étape 2 : Observer le OOMKilled
Appliquez le manifeste :

```Bash

kubectl apply -f pod-oom-lab.yaml
```
Observez le statut du Pod :

```Bash

kubectl get pods -w
```
Le Pod va démarrer, puis passer rapidement en état Error ou OOMKilled. Le compteur RESTARTS va augmenter car Kubernetes essaie de le relancer en boucle.

Inspectez les détails pour confirmer la cause :

```Bash

kubectl describe pod pod-oom
```
Dans la description, sous Last State, vous verrez que la Reason est OOMKilled (Out Of Memory Killed).

## Étape 3 : Vérifier la Classe de QoS
Créez un fichier pod-resources.yaml avec des requests et limits différents :

```YAML

# pod-resources.yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-resources
spec:
  containers:
  - name: app
    image: nginx
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```
Déployez-le et vérifiez sa classe de QoS :

```Bash

kubectl apply -f pod-resources.yaml
kubectl describe pod pod-resources | grep "QoS Class"
```
Résultat attendu : QoS Class: Burstable

## Étape 4 : Nettoyage
```Bash

kubectl delete pod pod-oom
kubectl delete pod pod-resources
```
