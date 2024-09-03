---
title: "Wazuh: Installation"
seoTitle: "Wazuh: Guide Complet pour l'installation"
seoDescription: "Wazuh est une plateforme open-source pour la détection des menaces, la réponse aux incidents, et la surveillance de l'infrastructure"
datePublished: Thu Aug 29 2024 04:13:27 GMT+0000 (Coordinated Universal Time)
cuid: cm0ersbot000s09l5dofm7223
slug: wazuh-installation
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1724880795424/81d71514-ebd9-4ec2-ab4d-3766f536b7b6.png
tags: web-development, security, cybersecurity, cybersecurity-1, wazuh, cybersec, edr, siem, xdr, siem-as-a-service, open-xdr, wazuh-dashboard

---

Wazuh est une plateforme open-source qui offre des moyens de détecter des menaces, de répondre aux incidents, de surveiller l'intégrité des fichiers et de fournir une visibilité sur l'ensemble de l'infrastructure. Wazuh est essentiellement un SIEM, utilisé par de nombreuses organisations en matière de sécurité.

### Composants

Tout d'abord, voici les 4 principaux composants de Wazuh :

* Le serveur Wazuh analyse les données collectées par les différents agents. Il détecte les menaces, génère des alertes et propose des solutions aux incidents. Les informations sont centralisées au niveau du serveur Wazuh pour fournir une vue d'ensemble de la sécurité de l'infrastructure.
    
* L'indexeur Wazuh agit comme une base de données en répertoriant les alertes et incidents générés par le serveur Wazuh. Il permet de stocker et d'analyser les données de sécurité collectées, facilitant ainsi la détection des menaces et la réponse aux incidents.
    
* L'agent Wazuh est installé directement sur les terminaux à surveiller (poste de travail, serveur, machine virtuelle, etc.). L'agent collecte tous les événements de sécurité, les données du système, les logs et bien plus encore, puis envoie les informations au serveur Wazuh.
    
* Le dashboard (tableau de bord) fournit une interface utilisateur graphique pour analyser et gérer les alertes.
    

![](https://www.linode.com/docs/guides/monitoring-and-securing-cloud-workloads-with-wazuh/wazuh-system-diagram_hu80f5247a9b43a7649d456272ccd288a6_150952_1388x0_resize_q71_bgfafafc_catmullrom.jpg align="center")

Voici une image provenant de [Linode](https://www.linode.com/docs/guides/monitoring-and-securing-cloud-workloads-with-wazuh/) qui illustre le fonctionnement de Wazuh.

Il est important de noter que l'agent Wazuh peut être installé sur macOS, Windows et Linux. Pour les systèmes Linux, il est recommandé d'utiliser une version 64 bits pour une meilleure compatibilité.

### Installation

La documentation détaillée est disponible sur le site officiel de [Wazuh](https://documentation.wazuh.com/current/installation-guide/index.html).

Dans notre article, l'installation se fera sur Debian du système d'exploitation Linux.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724851154491/9a14fc08-7648-4bcd-8f9f-022f0bc004e7.png align="center")

À partir de là, il suffit de suivre les étapes décrites dans la documentation.

\*\*\*Si les pacakges curl ne sont pas encore installés, faire la commande `sudo apt install curl gpg` si la commande curl est introuvable.

1. Ajouter la clé GPG
    
    ```bash
    curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg
    ```
    
2. Ajouter le dépot Wazuh
    
    ```bash
    echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list
    ```
    
3. Mettre à jour les informations des paquets
    
    ```bash
    sudo apt update && sudo apt upgrade && sudo apt-get install debconf adduser procps curl gnupg apt-transport-https filebeat debhelper libcap2-bin
    ```
    
4. Télécharger le script `wazuh-certs-tool.sh` et le fichier `config.yml`. Cela permettra d'ajouter les addresses IP du/des serveur/s, de l'indexeur et du dashboard.
    
    ```bash
    curl -sO https://packages.wazuh.com/4.8/wazuh-certs-tool.sh && curl -sO https://packages.wazuh.com/4.8/config.yml
    ```
    
5. Vérifier que les deux fichiers ont bien été créés avec la commande `dir`
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724853975862/0b514f56-3012-4d21-886c-bea602f6d094.png align="center")
    
6. Modifier le fichier config.yml avec la commande `nano /config.yml`
    
    Ajouter l'adresse IP de la machine Debian pour l'indexeur, le serveur et le tableau de bord. Cette approche utilise le mode "single node" où un seul serveur gère toutes les fonctions. Une autre approche serait la configuration en mode "multinode", où plusieurs serveurs sont impliqués, avec une machine dédiée au tableau de bord et une autre servant d'indexeur. La configuration "single node" simplifie la gestion et les déploiements.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724854197204/60a70527-debd-49b1-b3e7-4780078ecde4.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724879271683/c4ef3b21-19d4-4311-bc54-25dbc24efd54.png align="center")
    
    Notez qu'il est important à ce niveau de conserver les noms des indexer , server et dashboard
    
7. Exécuter le script suivant pour générer les certificats et compresser les fichiers
    
    ```bash
    bash ./wazuh-certs-tool.sh -A
    tar -cvf ./wazuh-certificates.tar -C ./wazuh-certificates/ .
    rm -rf ./wazuh-certificates
    ```
    
8. Installer le paquet wazuh-indexer , wazuh-manager et wazuh-dashboard
    
    "Wazuh-indexer" installe <mark>Elasticsearch</mark>, qui est utilisé pour l'indexation et la recherche des logs collectés par Wazuh.
    
    "wazuh-manager" installe le serveur Wazuh
    
    "wazuh-dashboard" installe <mark>Kibana</mark>, qui est l'interface web utilisée pour visualiser les alertes et les données dans Wazuh
    
    ```bash
    sudo apt install wazuh-indexer wazuh-manager wazuh-dashboard -y
    ```
    
    À noter que cette installation peut durer quelques minutes 😉
    
9. Configurer le "wazuh-indexer"
    
    ```bash
    nano /etc/wazuh-indexer/opensearch.yml
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724857648437/d9b4ab63-5043-4358-8423-38b10bf1cc1d.png align="center")
    
    À ce niveau il faut mettre l'adresse IP du serveur au "network.host".
    
10. Déployer les certificats de l'indexer
    
    Exécuter les commandes suivantes en remplacant le nom du nœud d’indexeur Wazuh si il a été modifé à l'étape 6 et 9. Si vous ne l'avez pas modifié, il devrait être "<mark>node-1</mark>".
    
    ```bash
    NODE_NAME=node-1
    ```
    
    Les commandes pour déployer les certificats
    
    ```bash
    mkdir /etc/wazuh-indexer/certs
    tar -xf ./wazuh-certificates.tar -C /etc/wazuh-indexer/certs/ ./$NODE_NAME.pem ./$NODE_NAME-key.pem ./admin.pem ./admin-key.pem ./root-ca.pem
    mv -n /etc/wazuh-indexer/certs/$NODE_NAME.pem /etc/wazuh-indexer/certs/indexer.pem
    mv -n /etc/wazuh-indexer/certs/$NODE_NAME-key.pem /etc/wazuh-indexer/certs/indexer-key.pem
    chmod 500 /etc/wazuh-indexer/certs
    chmod 400 /etc/wazuh-indexer/certs/*
    chown -R wazuh-indexer:wazuh-indexer /etc/wazuh-indexer/certs
    ```
    
11. Activer et démarrer le service wazuh-indexer
    
    ```bash
    sudo systemctl daemon-reload
    sudo systemctl enable wazuh-indexer
    sudo systemctl start wazuh-indexer
    ```
    
12. Initialisation du cluster
    
    ```bash
    /usr/share/wazuh-indexer/bin/indexer-security-init.sh
    ```
    
13. Tester l'installation du cluster
    
    Remplacer &lt;WAZUH\_INDEXER\_IP\_ADDRESS&gt; par l'adresse IP de l'indexer
    
    ```bash
    sudo curl -k -u admin:admin https://<WAZUH_INDEXER_IP_ADRESS>:9200
    ```
    
    La sortie devrait ressembler à ceci, indiquant que l'installation a été réussie.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724859394268/1fa57e8b-682b-420e-8089-3a9ca2358cfd.png align="center")
    
    ```bash
    curl -k -u admin:admin https://<WAZUH_INDEXER_IP_ADRESS>:9200/_cat/nodes?v
    ```
    
14. Installation du Wazuh manager
    
    ```bash
    sudo systemctl daemon-reload
    sudo systemctl enable wazuh-manager
    sudo systemctl start wazuh-manager
    sudo systemctl status wazuh-manager
    ```
    
    Après quelques minutes, le service wazuh-manager devrait être installé.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724860269336/dbacacba-6070-47b9-b399-873d521b4138.png align="center")
    
15. Démarrer le service filebeat
    
    ```bash
    apt install filebeat
    ```
    
16. Télécharger et configurer le fichier de confuguration filebeat
    
    ```bash
    curl -so /etc/filebeat/filebeat.yml https://packages.wazuh.com/4.8/tpl/wazuh/filebeat/filebeat.yml
    ```
    
    ```bash
    nano /etc/filebeat/filebeat.yml
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724860603396/42b4c332-2309-4613-a05a-c7e83b37cfea.png align="center")
    
    Modifier l'adresse IP localhost par celui de l'indexer (la même adresse IP depuis l'étape 6)
    
17. Créer le fichier filebeat keystore pour stocker en toute sécurité les informations d’authentification.
    
    ```bash
    filebeat keystore create
    ```
    
18. Ajouter le nom d'utilisateur et le mot de passe par défaut au keystore
    
    ```bash
    echo admin | filebeat keystore add username --stdin --force
    echo admin | filebeat keystore add password --stdin --force
    ```
    
    Le nom d'utilisateur et le mot de passe sont tous deux spécifiés à "admin".
    
19. Télécharhger le template d'alertes pour le wazuh indexer
    
    ```bash
    curl -so /etc/filebeat/wazuh-template.json https://raw.githubusercontent.com/wazuh/wazuh/v4.8.2/extensions/elasticsearch/7.x/wazuh-template.json
    chmod go+r /etc/filebeat/wazuh-template.json
    ```
    
20. Installer le module wazuh pour filebeat
    
    ```bash
    curl -s https://packages.wazuh.com/4.x/filebeat/wazuh-filebeat-0.4.tar.gz | tar -xvz -C /usr/share/filebeat/module
    ```
    
21. Déployer les certificats
    
    Ici encore une fois, remplacer le nom du certificat &lt;SERVER\_NODE\_NAME&gt; par le nom utilisé lors de la création des certificats. Si vous n'avez rien modifié, il restera "<mark>wazuh-1</mark>"
    
    ```plaintext
    NODE_NAME=wazuh-1
    ```
    
    ```bash
    mkdir /etc/filebeat/certs
    tar -xf ./wazuh-certificates.tar -C /etc/filebeat/certs/ ./$NODE_NAME.pem ./$NODE_NAME-key.pem ./root-ca.pem
    mv -n /etc/filebeat/certs/$NODE_NAME.pem /etc/filebeat/certs/filebeat.pem
    mv -n /etc/filebeat/certs/$NODE_NAME-key.pem /etc/filebeat/certs/filebeat-key.pem
    chmod 500 /etc/filebeat/certs
    chmod 400 /etc/filebeat/certs/*
    chown -R root:root /etc/filebeat/certs
    ```
    
22. Redémarrer le service filebeat
    
    ```bash
    sudo systemctl daemon-reload
    sudo systemctl enable filebeat
    sudo systemctl start filebeat
    ```
    
23. Vérifier que filebeat est installé avec succès
    
    ```bash
    filebeat test output
    ```
    
    Vous devriez obtenir la sortie suivante, avec l'adresse IP de votre serveur.
    
24. ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724862155207/6c856672-9372-414c-bfb2-09dcb69ef9ab.png align="center")
    
    Configurer le wazuh dashboard
    
    ```bash
    nano /etc/wazuh-dashboard/opensearch_dashboards.yml
    ```
    
    Modifier l'adresse IP "opensearch.hosts" et mettre celui de l'indexer
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724862451853/27880fa2-5a16-44db-b1a4-084f064e4dbb.png align="center")
    
25. Déployer les certificats pour le wazuh-dashboard
    
    Remplacez le &lt;DASHBOARD\_NODE\_NAME&gt; par le nom utilisé lors de la configuration du fichier config.yml à l'étape 6 au niveau du tableau de bord. Si vous n'avez rien modifié, il restera "<mark>dashboard</mark>".
    
    ```plaintext
    NODE_NAME=dashboard
    ```
    
    ```bash
    mkdir /etc/wazuh-dashboard/certs
    tar -xf ./wazuh-certificates.tar -C /etc/wazuh-dashboard/certs/ ./$NODE_NAME.pem ./$NODE_NAME-key.pem ./root-ca.pem
    mv -n /etc/wazuh-dashboard/certs/$NODE_NAME.pem /etc/wazuh-dashboard/certs/dashboard.pem
    mv -n /etc/wazuh-dashboard/certs/$NODE_NAME-key.pem /etc/wazuh-dashboard/certs/dashboard-key.pem
    chmod 500 /etc/wazuh-dashboard/certs
    chmod 400 /etc/wazuh-dashboard/certs/*
    chown -R wazuh-dashboard:wazuh-dashboard /etc/wazuh-dashboard/certs
    ```
    
    Pour vérifier le nom du nœud de votre tableau de bord Wazuh, faire la commande `cat config.yml` ou `nano config.yml`
    
26. Démarrer le service wazuh-dashboard
    
    ```bash
    sudo systemctl daemon-reload
    sudo systemctl enable wazuh-dashboard
    sudo systemctl start wazuh-dashboard
    ```
    
    Vous pouvez vérifier l'état des services avec les commandes suivantes:
    
    ```plaintext
    sudo systemctl status wazuh-dashboard
    sudo systemctl status wazuh-manager
    sudo systemctl status wazuh-indexer
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724864034142/e2778e17-386b-4914-b173-360a6eb76c3f.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724864064383/c2809129-566d-42b3-bce8-fd95785b8f1d.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724874719163/e68a72a1-c516-490f-a2e5-baf31e88405c.png align="center")
    
    On voit que les services sont actifs.
    

### Interface Wazuh

Une fois l'installation terminée, il faut se connecter avec `https://<adresseIP_server>` .

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724880745319/f8f17b65-caab-4181-a1bd-88140af739d2.png align="center")

### Conclusion

Pour conclure, Wazuh est une solution open-source solide et flexible de gestion des informations et des événements de sécurité (SIEM). Grâce à ses composants principaux — le serveur, l'indexeur, l'agent et le tableau de bord — Wazuh offre une surveillance complète de l'infrastructure, la détection des menaces, la réponse aux incidents et la gestion de l'intégrité des fichiers. Son installation, bien que détaillée, est facilitée par une documentation exhaustive sur le site officiel. La prochaine étape est le [déploiement des agents sur les terminaux](https://bilaldotcom.hashnode.dev/deploiement-des-agents-wazuh).