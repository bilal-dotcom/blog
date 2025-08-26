---
title: "Kubernetes"
seoTitle: "Kubernetes: Simplifying Container Orchestration"
seoDescription: "Découvrez Kubernetes, une plateforme open-source pour gérer le déploiement et l'orchestration d'applications conteneurisées efficacement"
datePublished: Tue Aug 26 2025 16:58:47 GMT+0000 (Coordinated Universal Time)
cuid: cmessiwzr000702jv5w7y6j5i
slug: kubernetes
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/9cXMJHaViTM/upload/f83a764b75524ecf51f73ce1a7df9231.jpeg
tags: kubernetes, k8s, kubernetes-container, k8scluster, k8s-commands

---

Kubernetes ou K8s est une plateforme open-source utilisée pour automatiser le déploiement et la gestion des applications conteneurisées. Une application conteneurisée est une application qui fonctionne à l’intérieur d’un conteneur, un environnement léger et isolé contenant tout ce dont l’application a besoin pour être exécuter (le code, les bibliothèques, les dépendances, els configurations et autres). Les conteneurs garantissent que que l’application tourne de la même manière, que ce soit sur un PC, un serveur ou dans le cloud. Kubernetees aide donc à orchestrer le tout: il déploie ces conteneurs, en ajoute on en retire selon la demande, les met à jour, et s’assure qu’ils restent opérationnels.

# Pré-requis

* **Minikube**: pour créer un cluster local de test
    
* **kubectl**: pour interagir avec Kubernetes
    

## Étape 1: Installer et démarrer Minikube

Minikube permet de créer un cluster Kubernetes local. Allez sur le site [minikube start | minikube](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download) pour télécharger et installer minikube. À noter que Minikube ne doit pas être utilisé en prod car il est principalement concu pour des environnements de tests sur une machine. Il faudrait envisager Amazon EKS ou Google Kubernetes Engine en prod.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756216793624/f379471b-fe0d-4bb1-994c-df12534d7711.png align="center")

Exécutez ensuite la commande suivante pour lancer le cluster.

```bash
minikube start
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756220166010/0cec0400-07f4-47bc-813b-b2accb7912bd.png align="center")

Le cluster minikube a démarré correctement mais il est indiqué que **kubectl** est introuvable. Cela signifie que kubectl n’est pas bien installé sur le système windows. On peut l’installer avec la commande suivante:

```bash
minikube kubectl -- get pods -A
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756224800190/c6587ddd-897e-4620-8a82-df881f31cbff.png align="center")

La commande a été exécutée et affiche la liste des pods en cours d’exécution. Un pod est l’unité de base de déploiement dans Kubernetes. Il représente un ou plusieurs conteneurs qui partagent le même réseau et le même espace de stockage. Chaque pod a sa propre adresse IP, ce qui permet aux conteneurs à l'intérieur de communiquer entre eux.

Le cluster Kubernetes est maintenant fonctionnel et tous les composants ( etcd, kube-apiserver, kube-controller-manager, kube-scheduler, etc) sont en status Running.

## Étape 2 : Déployer une application de test conteneurisée

On va déployer ici une application conteneurisée assez simple, un serveur web NGINX.

1. Crééz le déploiement
    
    ```bash
    minikube kubectl -- create deployment hello-nginx --image=nginx
    ```
    
    Cette commande crée un pod(un conteneur) avec l’image NGINX, qui contient tout ce qu’il faut pour exécuter le serveur web.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756226197064/f7c820c2-6292-4e2f-84e2-3a109dc1a6c5.png align="center")
    
2. Exposez l’application
    
    ```bash
    minikube kubectl -- expose deployment hello-nginx --type=NodePort --port=80
    ```
    
    Cette commande expose le déploiement kubernetes et crée un service de type NodePort pour accéder à l’application depuis le navigateur
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756226208691/0a633889-1928-4b4f-93a8-2edf01adc3b8.png align="center")
    
3. Accédez à l’application
    
    ```bash
    minikube service hello-nginx
    ```
    
    Cette commande permet à Minikube d’ouvrir une URL dans le navigateur pour voir la page NGINX.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756226217263/ff36a26f-110e-4204-a8c8-408b19c9ceee.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756226177257/6d410943-6020-431a-bde1-6f60e64a98f3.png align="center")

Le cluster fonctionne donc bien. La prochaine étape serait de déployer une réelle application.