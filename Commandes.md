# 🧾 Fiche Pratique Kubernetes – Commandes Essentielles (sans alias + shell)

## 1. 💡 Travaille avec des alias intelligents

Facilite la vie avec des alias puissants dans ton fichier `.bashrc` ou `.zshrc` :

```bash
alias k=kubectl
alias kget='kubectl get all'
alias kexec='kubectl exec -it'
alias kctx='kubectl config use-context'
alias kns='kubectl config set-context --current --namespace'
```

---

## 2. 🔄 Active la complétion automatique

Active l’autocomplétion dans ton shell pour `kubectl` et son alias `k` :

```bash
source <(kubectl completion bash)    # Remplace "bash" par "zsh" si nécessaire
complete -F __start_kubectl k
```

➡️ À ajouter dans ton `.bashrc` ou `.zshrc` pour rendre ça permanent.

---

## 3. 🔍 Voir les ressources
```bash
kubectl get pods
kubectl get services
kubectl get deployments
kubectl get all
kubectl get nodes
```
➡️ Ajouter `-n <namespace>` pour spécifier un namespace.

---

## 4. 📋 Inspecter une ressource
```bash
kubectl describe pod <nom-du-pod>
kubectl describe service <nom-du-service>
kubectl describe deployment <nom-du-deployment>
```

---

## 5. 📦 Consulter les logs
```bash
kubectl logs <nom-du-pod>
kubectl logs -f <nom-du-pod>       # suivre en direct (stream)
kubectl logs -c <container> <pod>  # si plusieurs containers
```

---

## 6. 💻 Accéder à un Pod en ligne de commande
```bash
kubectl exec -it <nom-du-pod> -- bash
kubectl exec -it <nom-du-pod> -- sh    # si bash indisponible
```

---

## 7. 🔄 Appliquer des fichiers YAML
```bash
kubectl apply -f fichier.yaml
```

✔️ Tester sans appliquer :
```bash
kubectl apply -f fichier.yaml --dry-run=client -o yaml
```

---

## 8. 🧪 Supprimer des ressources
```bash
kubectl delete pod <nom>
kubectl delete -f fichier.yaml
```

---

## 9. 📊 Surveiller l’utilisation des ressources
```bash
kubectl top pods
kubectl top nodes
```

📌 *Nécessite l’installation de Metrics Server.*

---

## 10. 🧭 Contexte et namespace
Afficher le contexte et le namespace actifs :
```bash
kubectl config current-context
kubectl config view --minify | grep namespace
```

Changer le namespace par défaut :
```bash
kubectl config set-context --current --namespace=<nom-namespace>
```

---

## 11. 🔐 Lire un Secret (décodé)
```bash
kubectl get secret <nom> -o jsonpath="{.data.<clé>}" | base64 -d
```

---

## 12. 🚨 Voir les événements récents du cluster
```bash
kubectl get events --sort-by=.metadata.creationTimestamp
```

---

## 13. ⚙️ Créer des objets Kubernetes depuis la ligne de commande

### ▶️ Créer un Pod simple
```bash
kubectl run nginx-pod --image=nginx
```
➡️ Crée un Pod nommé `nginx-pod` basé sur l’image Docker officielle `nginx`.

---

### 🛠️ Créer un Deployment
```bash
kubectl create deployment nginx-deployment --image=nginx
```

Ajouter des replicas :
```bash
kubectl scale deployment nginx-deployment --replicas=3
```

---

### 🌐 Exposer un Deployment via un Service (type ClusterIP par défaut)
```bash
kubectl expose deployment nginx-deployment --port=80 --target-port=80
```

Pour un Service de type **NodePort** :
```bash
kubectl expose deployment nginx-deployment --type=NodePort --port=80
```

---

### 📁 Créer un Namespace
```bash
kubectl create namespace mon-namespace
```

Changer de namespace actif :
```bash
kubectl config set-context --current --namespace=mon-namespace
```

---

### 🔐 Créer un Secret (clé simple en ligne)
```bash
kubectl create secret generic mon-secret --from-literal=password=SuperSecret123
```

---

### ⚙️ Créer un ConfigMap
```bash
kubectl create configmap mon-config --from-literal=ENV=prod
```

---

### 📄 Voir les ressources créées
```bash
kubectl get all
kubectl get svc
kubectl get deployment
kubectl get pod
```

---

📌 Pour convertir une ressource CLI en YAML sans l’appliquer :
```bash
kubectl create deployment nginx-deployment --image=nginx --dry-run=client -o yaml
```


