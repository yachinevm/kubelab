# Lab 3.3 : Manipuler le Stockage Persistant
Objectif : Comprendre le cycle de vie du provisionnement dynamique et prouver que les données persistent indépendamment du Pod.

## Étape 1 : Inspecter la StorageClass et Créer une PVC
Inspectez la StorageClass par défaut de Minikube :

```Bash

kubectl get sc
```
Notez le nom de la StorageClass par défaut, qui est standard.

Créez un fichier pvc-dynamique-lab.yaml pour demander du stockage :

```YAML

# pvc-dynamique-lab.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mon-volume-dynamique
spec:
  storageClassName: standard
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
Appliquez le manifeste :
```

```Bash

kubectl apply -f pvc-dynamique-lab.yaml
```
## Étape 2 : Observer le Provisionnement Dynamique
Suivez le statut de la PVC en temps réel :

```Bash

kubectl get pvc -w
```
L'état passera de Pending à Bound en quelques secondes.

Vérifiez que le PersistentVolume (PV) a été créé automatiquement :

```Bash

kubectl get pv
```
Vous verrez un PV avec un nom aléatoire, lié à votre PVC.

## Étape 3 : Utiliser la PVC dans un Pod
Créez le fichier pod-storage-lab.yaml pour monter le volume :

```YAML

# pod-storage-lab.yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pod-storage
spec:
  containers:
  - name: app
    image: busybox
    command: ["sleep", "3600"]
    volumeMounts:
    - name: mon-stockage
      mountPath: "/data"
  volumes:
  - name: mon-stockage
    persistentVolumeClaim:
      claimName: mon-volume-dynamique
```
Appliquez le manifeste :

```Bash

kubectl apply -f pod-storage-lab.yaml
```
## Étape 4 : Écrire des Données, Détruire le Pod et Prouver la Persistance
Écrivez un fichier sur le volume persistant :

```Bash

kubectl exec test-pod-storage -- sh -c 'echo "Mes données sont en sécurité" > /data/mon_fichier.txt'
```
Simulez une panne en supprimant le Pod :

```Bash

kubectl delete pod test-pod-storage
```
Vérifiez que la PVC et le PV sont toujours là :

```Bash

kubectl get pvc,pv
```
Recréez le Pod et lisez les données :

```Bash

kubectl apply -f pod-storage-lab.yaml
```
Attendez que le nouveau Pod soit 'Running'
```
kubectl exec test-pod-storage -- cat /data/mon_fichier.txt
```
Résultat Magique : Le message "Mes données sont en sécurité" s'affiche, prouvant que les données ont persisté.
