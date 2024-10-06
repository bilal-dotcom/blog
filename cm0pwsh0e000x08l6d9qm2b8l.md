---
title: "Penetration Testing avec Metasploit"
seoTitle: "Metasploit Penetration Testing Guide"
seoDescription: "Guide étape par étape de penetration testing avec Metasploit, utilisant des outils comme Nmap, Nikto, Burp."
datePublished: Thu Sep 05 2024 23:19:00 GMT+0000 (Coordinated Universal Time)
cuid: cm0pwsh0e000x08l6d9qm2b8l
slug: penetration-testing-metasploit
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1725466545818/ff7f0ce2-a083-4fc1-a554-c1ea75de76da.png
tags: kali-linux, nmap, pentesting, pentest, metasploit, pentesting-tools, metasploit-framework, nikto

---

## Pré-requis

* [Installation et Configuration de Metasploit sur Kali Linux](https://blog.fikara.io/metasploit-sur-kali-linux)
    

Le pentesting, ou test d'intrusion est une méthode utilisée pour évaluer la sécurité d'un système ou réseau informatique. Le but est d'identifer des vulnérabilités et surtout de les corriger pour éviter qu'elles soient exploitées par des acteurs malveillants.

Il existe plusieurs outils couramment utilisés pour le pentesting:

* [Metasploit](https://blog.fikara.io/metasploit-sur-kali-linux), un environnement de test d'intrusion sur Kali Linux, utilisé pour tester et exploiter la vulnérabilité des systèmes en utilisant divers exploits (failles connues) et payloads(scripts permettant de prendre le contrôle d'une machine).
    
* Nmap, un outil qui permet de scan le réseau et obtenir des informations sur une machine cible (services, versions des logiciels, ports ouverts, etc)
    
* Nikto, pour détecter des failles sur des serveurs. Il les analyse afin de détecter des fichiers et bien d'autres. On peut trouver avec Nikto des pages accessibles comme `/index` ou des pages de logs `/phpMyadmin`.
    
* Searchploit, pour rechercher localement des exploits en utilisant des bases de données de vulnérabilités présentes sur le système. Il est en quelque sorte l'équivalent de [exploit-db](https://www.exploit-db.com/) qui est un outil open-source de recherche des exploits.
    
* Burp, pour analyser les requêtes http et les réponses entre un navigateur et un serveur. Il peut manipuler le traffic pour trouver des failles.
    
* Medusa ou Hydra pour des attaques par force brute. Ils sont souvent utilisés pour tester le conformité des mots de passe.
    

## Nmap

On utilise ici, une machine virtuelle metaspoitable (cible) vulnérable. La première étape est l'analyse de la cible dans l'éventualité de trouver des informations utiles. On pourrait utliser `nmap` ou `nikto` pour cette phase.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725488931286/776adacd-e058-4f8f-b77c-bc6ba6d98004.png align="center")

On voit par exemple que le service FTP est ouvert sur le port 21 utilisant vsftpd 2.3.4, SSH sur le port 22 avec OpenSSH 4.7p1, telnet est ouvert et donne un accès à des communication non chiffrées facilitant des interceptions, HTTP à travers un serveur web apache avec la version 2.2.8, le service Samba actif sur les ports 139 et 445 mais sans la version exacte.

Chacun de ces ports ouverts peut être l'objet d'une potentielle exploitation.

## Searchsploit

Searchsploit est un outil pour rechercher localement des exploits et des vulnérabilités sur un système. Cet outil utilise la base de données [Exploit Database](https://www.exploit-db.com/), qui est une archive publique de failles de sécurité et d'exploits.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1728228596434/9604243f-6596-4eca-be0f-2c3357e593dc.png align="center")

La commande `searchsploit vsftpd 2.3.4` retourne deux exploits disponibles pour vsftpd 2.3.4. Plus d’informations sur ces exploits sont répertoriées sur Exploit Database.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725493797060/6fbc6a57-ea69-45d4-9b0b-e51cdfe3a28f.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1728228922462/812d922f-2092-46ac-81e1-75146735b1d9.png align="center")

* **vsftpd 2.3.4 - Backdoor Command Execution** : Cet exploit est disponible sous la forme d'un script Python. Il permet d’exécuter des commandes à distance sur un serveur vulnérable.
    
* **vsftpd 2.3.4 - Backdoor Command Execution (Metasploit)** : Celui-ci est intégré directement dans Metasploit et est disponible sous la forme d'un script Ruby. Vous pouvez le visualiser directement dans le répertoire `/usr/share/metasploit-framework/modules/exploits/unix/ftp/` écrit en langage Ruby
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1728229479115/67555c14-8d09-4171-97d9-3bfd27c84ebf.png align="center")
    

## Vsftpd 2.34

On veut trouver ici, une vulnérabilité liée à vstfpd, pour réussir notre attaque. En utilisant la commande `search vsftpd 2.3.4` dans msfconole, on trouve un exploit pour la version 2.3.4 avec un rang excellent, ce qui indique qu'il peut être fiable.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725489388211/dfae501e-b585-417b-b9aa-2d9766dea633.png align="center")

On commence par sélectionner l'exploit trouvé en faisant la commande `use 0` ou `use exploit/unix/ftp/vsftpd_234_backdoor`. Dans Metasploit la commandes `info` fournit des informations détaillées sur l'exploit sélectionné et la commande `options` , les paramètres configurables pour l'exploit sélectionné comme l’adresse IP, le port et d’autres paramètres nécessaires pour exécuter l’exploit.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725489928150/88ac51a9-623e-47f9-b8e8-351b766dafd8.png align="center")

On définit ensuite la machine cible avec `set RHOSTS 192.168.2.20` et ensuite on exécute avec la commande `run` ou `exploit`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725490346145/04e6a334-d5ca-4fa7-8360-b93680dcee87.png align="center")

Une session a été établie entre notre machine 192.168.2.98 et la machine cible 192.168.2.20 via les ports 33599 et 6200. On est maintenant directement connecté sur la machine cible.

Il est aussi possible de définir le payload après avoir sélectionné l'exploit et configuré les options. On rappelle que payload est le code qui sera exécuté sur la machine cible une fois l'exploit réussi. Après avoir sélectionné un exploit avec la commande `use`, vous pouvez utiliser `show payloads` pour afficher une liste de tous les payloads <mark>compatibles </mark> avec cet exploit. Il faut maintenant configurer le payload et ses options, LHOST pour l’adresse IP et LPORT pour le port, et ensuite vous exécutez l’exploit.

## Openssh

Il est aussi possible d'exploiter la vulnérabilité en utilisant le service SSH.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725490675503/877802e1-a842-42bb-9b85-daf8ac845a03.png align="center")

Avec la commande `search openssh 4.7`, on recherche des exploits liés à openssh et `searchsploit openssh 4.7` pour des exploits avec des CVE disponibles sur openssh.

Il se peut, comme dans ce cas, que la recherche ne nous retourne pas directement des informations plus ou moins utiles.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725492404939/ed892143-71b4-46b6-9fc1-df136ab559ec.png align="center")

Aussi, sur le site de base de données des exploit, on a pas plus d'informations.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725493978208/5250697d-af36-470b-91bd-fa20dce9ce36.png align="center")

## Samba

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725458109702/c72042f3-c27d-4bbc-91db-1d6d84ba7164.png align="center")

On va essayer de trouver ici, une faille en exploitant le service Samba qui est généralement utilisé pour le partage de fichiers et des imprimantes. Le résultat du nmap n'indique pas exactement la version du service samba. On ne peut donc pas directement savoir quel exploit utiliser mais elle doit être autour d'une version 3.X à 4.X.

On peut donc essayer avec `searchsploit`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725458184967/b1b41f9a-2658-4c51-a20e-ba9b342fc171.png align="center")

Il se peut qu'il ne nous retourne pas directement des informations qui nous seront utiles ou qu'on pourrait directement exploiter.

Une autre option ici est le module `auxiliary` dans les modules du `metasploit-framework` qui fournit des informations supplémentaires.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725465474723/9735cd74-70b0-4c17-983a-50f6c5874375.png align="center")

On a un résultat en lien avec la version. On peut donc faire la commande `options` pour voir les configurations possibles. Ici j'utilise la commande `use 4` afin d'utiliser directement le résultat de ma recherche après la commande search. La commande est pareille que `use auxiliary/scanner/smb/smb_version`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725465736200/21ce79ba-e12f-47a1-bc04-98eccab5e394.png align="center")

On spécifie ensuite le RHOST et on peut faire la commande `exploit` ou `run`.

On a pu trouver la version exacte sur la machine (Samba 3.0.20-Debian)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725458323647/44784d3e-59e5-4ee4-8ba4-4db6d3e29b55.png align="center")

Cette info sur la version peut être utilisée pour trouver une ou plusieures autres vulnérablités. Il est donc possible que cette version ait des vulnérabilités connues.

Avec la commande `searchsploit`, on peut ainsi trouver des exploits potentiels pour cette version de Samba détectée.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725577665489/118b7025-bcbc-4019-8213-8e499f386976.png align="center")

On a donc touvé quatre vulnérabilités différentes. L'étape suivante serait de savoir à quels exploits de `msfconsole`, correspond le résultat du searchsploit. Pour cela, il est possible de voir le chemin indiqué, faire une recherche avec le nom ou encore une recherche simple sur google.

En faisant un search avec un des termes retournés par le searchsploit, on peut donc trouver un exploit lié à samba.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725459769627/47c1eee6-06fc-4bdc-bb56-e0a911c5e42c.png align="center")

Pour avoir plus d'info sur l'exploit, il est aussi possible d'aller dans le répertoire, ici , `/usr/share/metasploit-framework/modules/exploits/multi/samba/` et voir le code de l'exploit, qui est en language Ruby.

Une fois, dans l'exploit, avec la commande `use 3` ou `use exploit/multi/samba/usermap_script`, on peut donc voir sa description et ensuite faire la commande `info` pour des informations sur l'exploi.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725460352499/d2348c75-6db6-4c2d-92f2-6b56b88fc1fb.png align="center")

Il nous donne des détails supplémentaires sur les options à configurer et la descripton de l'exploit.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725460458656/91f754c5-ad87-499f-8ba9-3e8b35a25f40.png align="center")

L'exploit ici est lié une vulnérabilité par injection de commandes sur la version de Samba 3.0.20 à 3.0.25rc3. Elle permet donc d'exécuter des commandes arbitraires spécifiant un nom d'utilisateur contenant des caractères méta de shell, sans authentification

On spécifie donc ici l'hôte avec `SET RHOST 192.168.2.20` et ensuite exécuter l'exploit avec la commande `exploit` ou la commande `use`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725461216964/47a0ecfb-af50-450b-a556-cd304cfb0805.png align="center")

On peut voir qu'une session a été ouverte. On est maintenant connecté sur la machine cible en tant que root.

<mark>Il est important de rappeler que l'utilisation de ces outils doit toujours se faire dans un cadre légal.</mark>