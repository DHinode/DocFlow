# Kubernetes

| **Commande** | **Effet** |
|-------------|----------|
| `kubectl version` | Affiche la version du client et du serveur Kubernetes |
| `kubectl cluster-info` | Affiche les informations du cluster en cours |
| `kubectl get nodes` | Liste tous les nœuds du cluster |
| `kubectl get pods` | Liste tous les pods dans l’espace de noms actuel |
| `kubectl get services` | Liste tous les services |
| `kubectl get deployments` | Liste tous les déploiements |

## Gestion des pods 

| **Commande** | **Effet** |
|-------------|----------|
| `kubectl run mypod --image=nginx` | Crée un pod nommé `mypod` avec l’image `nginx` |
| `kubectl delete pod mypod` | Supprime un pod nommé `mypod` |
| `kubectl describe pod mypod` | Affiche les détails du pod `mypod` |
| `kubectl logs mypod` | Affiche les logs du pod `mypod` |
| `kubectl exec -it mypod -- /bin/sh` | Ouvre un shell interactif dans le pod `mypod` |

## Gestion des services 

| **Commande** | **Effet** |
|-------------|----------|
| `kubectl expose pod mypod --type=NodePort --port=80` | Expose un pod en tant que service de type `NodePort` |
| `kubectl get svc` | Liste tous les services |
| `kubectl delete svc myservice` | Supprime le service `myservice` |


## Gestion des déploiements

| **Commande** | **Effet** |
|-------------|----------|
| `kubectl create deployment myapp --image=nginx` | Crée un déploiement avec l’image `nginx` |
| `kubectl get deployments` | Liste tous les déploiements |
| `kubectl describe deployment myapp` | Affiche les détails du déploiement `myapp` |
| `kubectl scale deployment myapp --replicas=3` | Met à l’échelle le déploiement `myapp` avec 3 réplicas |
| `kubectl delete deployment myapp` | Supprime le déploiement `myapp` |

## Gestion des fichiers YAML 

| **Commande** | **Effet** |
|-------------|----------|
| `kubectl apply -f deployment.yaml` | Applique une configuration depuis un fichier YAML |
| `kubectl create -f service.yaml` | Crée un objet Kubernetes depuis un fichier YAML |
| `kubectl delete -f deployment.yaml` | Supprime un objet défini dans un fichier YAML |
| `kubectl get -f pod.yaml` | Liste les objets définis dans un fichier YAML |

### Exemple de fichier YAML 

```yaml=
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myserver
  labels:
    app: myserver
spec:
  replicas: 5
  selector:
    matchLabels:
      app: myserver
  template:
    metadata:
      labels:
        app: myserver
    spec:
      containers:
        - name: myserver
          image: docker/getting-started:latest
          ports:
            - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: myfirstservice
spec:
  selector:
    app.kubernetes.io/name: MyApp
  ports:
    - protocol: TCP
      port: 12345
      targetPort: 80
```
