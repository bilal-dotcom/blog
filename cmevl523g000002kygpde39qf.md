---
title: "Déployer une application Python via Docker sur Kubernetes"
seoTitle: "Déployer App Python avec Docker & Kubernetes"
seoDescription: "Déployez une application Python Flask avec Docker et Kubernetes, en utilisant Minikube pour un test de cluster local dans ce guide étape par étap"
datePublished: Thu Aug 28 2025 15:55:22 GMT+0000 (Coordinated Universal Time)
cuid: cmevl523g000002kygpde39qf
slug: deployer-une-application-python-via-docker-sur-kubernetes
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/GSiEeoHcNTQ/upload/e3f7f3d4e8c954bc191f773266e03128.jpeg
tags: docker, kubernetes, k8s, dockerfile, kubectl, docker-images, minikube, kubernetes-container, minikube-setup, k8scluster

---

Dans cet article, on va déployer une application Python Flask sur Kubernetes. Un premier article montrant les premiers pas avec Kubernetes et Minikube est disponible ici [Kubernetes: Simplifying Container Orchestration](https://blog.fikara.io/kubernetes)

## Pré-requis

* **Docker**: pour créer l’image conteneurisée de l’application avec Docker Desktop
    
* **Minikube**: pour créer un cluster local de test
    
* **kubectl**: pour interagir avec Kubernetes
    

## Étape 1: Créer l’application Python

1. Créer un fichier app.py
    
    Ce code crée une application via Flask qui écoute sur le port 5000 et affiche un message
    
    ```python
    from flask import Flask
    
    app = Flask(__name__)
    
    @app.route('/')
    def hello_world():
        return 'Hello, K8s!'
    
    if __name__ == '__main__':
        app.run(host="0.0.0.0", port=5000)
    ```
    
2. Créer un requirements.txt pour y lister les dépendances.
    
    ```python
    Flask==3.0.3
    ```
    
3. Installer les dépendances
    
    ```bash
    pip install -r requirements.txt
    ```
    
    Vous pouvez ensuite tester l’application avec la commande `python app.py`
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756235749902/604c3214-91d3-447b-b71a-e9ab264552a4.png align="center")

## Étape 2: Conteneuriser l’application via Docker

Pour déployer l’application sur Kubernetes, il faut la conteneuriser avec Docker. Cela signifie de créer une image Docker qui contient l’application et tout ce qu’il faut pour l’exécuter (dépendances et autres).

1. Créez un fichier Dockerfile
    
    ```dockerfile
    # Utiliser une image de base Python
    FROM python:3.9-slim
    
    # Définir le réepertoire de travail
    WORKDIR /app
    
    # Copier les fichiers de l’application
    COPY requirements.txt .
    COPY app.py .
    
    # Installer les dépendances
    RUN pip install -r requirements.txt --no-cache-dir 
    
    # Exposer le port de l’application
    EXPOSE 5000
    
    # Démarrer l’application
    CMD ["python", "app.py"]
    ```
    
2. Construire l’image Docker
    
    Docker doit d’abord être installé pour que cela fonctionne. Vous pouvez télécharger et installer [Docker Desktop](https://www.docker.com/products/docker-desktop/). Après l’installation, exécutez la commande suivante:
    
    ```bash
    docker build -t flask-k8s:1.0
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756307460750/e5b1706d-6a6f-43f5-a5bc-a04987201e48.png align="center")
    
    L’image Docker a bien été construite et est disponible localement. Vous pouvez la vérifier avec la commande `docker images`.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756307766644/7d000da8-ab90-43b3-857c-bfa1df8ceeb7.png align="center")
    
3. Tester l’image en local
    
    Pour s’assurer que l’application fonctionne, testez le conteneur avec la commande suivante:
    
    ```bash
    docker run -p 5000:5000 flask-k8s:1.0
    ```
    
    On accède ensuite à l’application via `http://localhost:5000`
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756309072517/cdb04f14-01aa-43bf-9121-a0ac92fed511.png align="center")
    

## Étape 3 : Pousser l’image vers Docker Hub

La prochaine étape sera de pousser l’image sur Docker Hub afin de la rendre accessible partout, pas seulement localement. Tout autre cluster a besoin d’Accéder à l’image pour créer les conteneurs. Si l’image reste uniquement sur le Docker local, seul le PC peut la lancer. Il faut la pousser sur Docker Hub afin de la rendre disponible pour notre cluster Kubernetes ou à d’autres personnes.

1. Créer un compte Docker Hub [https://hub.docker.com/](https://hub.docker.com/) puis se connecter avec la commande `docker login -u USERNAME`. Vous serez invités à entrer votre mot de passe.
    
2. Tagger l’image Docker
    
    Avant de pousser l’image, il faut s’assurer qu’elle est correctmeent taggée. Cela ne crée pas une nouvelle image , mais ajoute un tag à l’image existante.
    
    ```bash
    docker tag flask-k8s:1.0 bildotcom/flask-k8s:1.0
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756311572140/1b62cea4-0699-4e04-8e2c-d620b8ceeefd.png align="center")
    
3. Pousser l’image
    
    Assurez vous d’avoir confirmer votre adresse courriel sinon l’image ne pourra être poussée vers le hub
    
    ```bash
    docker push bildotcom/flask-k8s:1.0
    ```
    
    Après cela, votre image sera disponible sur Docker Hub et pourra être utilisée pour des déploiements sur d’autres machines.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756312506728/eb5b66db-e1dd-4f9c-b361-f517f11b9246.png align="center")

## Étape 4 : **Déployer l'image sur Kubernetes**

Une fois que l’Image est sur Docker Hub, on peut maintenant déployer l’application sur un cluster Kubernetes. Pour cela, il faut créer des fichiers de configurations Kubernetes qui sont des fichiers YAML pour définir les objets nécessaires, tels que Deployment et Service.

1. Créer le fichier deployment.yaml
    
    Deployment définit comment l’application doit être déployée, le nombre de répliques et l’image à utiliser.
    
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: flask-k8s
    spec:
      replicas: 2 # ici on spécifie le nombre de pods (répliques)
      selector: 
        matchLabels:
          app: flask-k8s 
      template:
        metadata:
          labels:
            app: flask-k8s
        spec:
          containers:
          - name: flask-k8s
            image: bildotcom/flask-k8s:1.0
            ports:
            - containerPort: 5000 # port exposé par le conteneur
    ```
    
2. Créer le fichier service.yaml
    
    Service expose l’application afin qu’elle soit accessible à d’autres services à l’extérieur du cluster.
    
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: flask-k8s
    spec:
      selector:
        app: flask-k8s
      ports:
      - protocol: TCP
        port: 80 # port exposé par le service
        targetPort: 5000 # port interne du conteneur
      type: NodePort
    ```
    
    Dans Kubernetes, il existe plusieurs types de services qui déterminent comment un service est exposé. On le définit via le champ `type` du fichier service.yaml.
    
    * **clusterIP**: type par défaut. Il expose le service uniquement à l’intérieur du cluster sur une IP interne. L’accès externe ne sera pas possible sans un tunnel.
        
    * **NodePort**: expose le service sur un port fixe de chaque noeud du cluster. Cela permet d’accéder au service depuis l’extérieur du cluster avec l’IP du noeud et le port spécifié.
        
    * **LoadBalancer:** crée un équilibreur de trafic vers les pods du service.
        
    * **ExternalName**: redirige le service vers un nom DNS externe en utilisant un CNAME.
        
3. Déployer sur le cluster
    
    Pour cette étape, il faut s’assurer que le cluster Minikube est en cours d’exécution avec la commande `minikube start`. Déployez ensuite le déploiement et le service avec les commandes suivantes:
    
    ```bash
    kubectl apply -f deployment.yaml
    kubectl apply -f service.yaml
    ```
    
    Vous pouvez vérifier si les pods démarrent avec la commande `kubectl get pods`.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756392239026/06087d24-ae12-4fe0-816b-857d0b2ced07.png align="center")
    
    On voit bien les deux pods spécifiés dans le fichier deployment.yaml sont en status `Running`.
    
4. Vérifier que le service est exposé
    
    La commande `kubectl get svc` permet de lister tous les services dans le cluster. On voit que le service est bien exposé via NodePort sur le port 32101.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756392704257/166e65a6-003b-4c4d-9ab7-af7c95dc0fa3.png align="center")
    
5. Accéder à l’application
    
    Ouvrez l’application dans votre navigateur avec la commande suivante:
    
    ```bash
    minikube service flask-k8s
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756393999041/b4fb4f13-72c7-4fd4-848a-6dc049d86452.png align="center")
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756394471596/1226fe22-5e4f-4570-8e70-ccb819ddd865.png align="center")

Si l’adresse 192.168.49.2:32107 ne fonctionne pas directement (car le réseau Minikube n’est pas routé vers l’hôte du poste Windows), on peut utiliser la commande minikube service flask-k8s pour ouvrir l’URL automatiquement ou utiliser un LoadBalancer au lieu du NodePort

Voici comment créer une application Pyhton Flask, la conteneurisée avec Docker, la publier sur DockerHub puis la déployer sur un cluster Kubernetes avec Minikube.