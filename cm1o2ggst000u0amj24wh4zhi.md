---
title: "Recon"
seoTitle: "Démonstration de quelques techniques de reconnaissance"
seoDescription: "Apprenez sur la reconnaissance lors d’un pentest avec les outils Check_mdi, ASN, DNSCHEKER, ASRank, BGP.HE.NET, ASNmap, Naabu, et Rustscan"
datePublished: Sun Sep 29 2024 21:01:48 GMT+0000 (Coordinated Universal Time)
cuid: cm1o2ggst000u0amj24wh4zhi
slug: recon
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/CpPF4W5PB1c/upload/aef83ceccacd78fe1c53289278204b00.jpeg
tags: pentesting, penetration-testing, reconnaissance, recon, rustscan, asnmap, naabu, naabu-scan

---

La reconnaissance (Recon) est une étape du test de pénétration qui consiste à collecter le plus d’informations sur un système, afin d’exploiter de potentielles vulnérabilités.  
Dans cet article, nous montrerons quelques outils et moyens utilisés pour la reconnaissance. Ces outils m’ont été présentés lors d’un Workshop de Bsides Montreal 2024 tenu par [Jason Haddix](https://www.linkedin.com/in/jhaddix/).

## Check\_mdi

Check\_mdi un script pyhton qui interroge l’API Microsoft pour énumérer les domaines Microsoft 365 valides à un domaine donné. Il trouve le locataire du domaine et vérifie la présence d'une instance de Microsoft Defender for Identity (MDI). MDI est une solution de sécurité dévéloppée par Micosoft pour les environnements en Active Directory. Vous pouvez trouver le code source sur [Github](https://github.com/expl0itabl3/check_mdi).  
Pour utiliser Check\_mdi, il faut exécuter la commande suivante:

```python
git clone https://github.com/expl0itabl3/check_mdi.git
cd check_mdi
python3 ./check_mdi.py -d domain.com
```

Cette commande va cloner le dépôt, naviguer dans le répertoire cloné, et ensuite exécuter le script pour le domaine spécifié.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727111274904/2baf50e1-6601-48b0-883d-55fa7dbbbee5.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727112257409/9a4873f5-727c-4627-964d-ed399d2a7eb0.png align="center")

On voit donc une liste de sous-domaines et de domaines associés aux domaines recherchés, le nom du locataire, et la présence ou non de MDI.

## ASN

Un ASN(Autonomous System Number) est un identifiant unique, écrit sous le format ASXXXXX, attribué à un groupe d’adresses IP géré par une même organisation, avec une politique de routage commune. Les ASN permettent aux réseaux de s’identifier de manière unique sur Internet.

Il existe plusieurs moyens et outils pour trouver les ASN associés à un domaine ou une adresse IP.

## DNSCHEKER

C’est un outil en ligne ([dnschecker.org](https://dnschecker.org/all-dns-records-of-domain.php)) qui permet de vérifier les DNS pour un domaine. Il permet aussi de voir l’ASN associé au domaine.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727270514327/f35f5da7-0e02-4227-8f07-27f13bf5fae8.png align="center")

## ASRANKCAIDA

ASRank ([asrank.caida.org](https://asrank.caida.org/)) est un service qui permet d’explorer et d’analyser les relations entre différents ASN. Il fournit des informations détaillées sur l’organisation, les AS voisins et bien d’autres.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727635305505/a992e9f0-0cb3-484c-927e-b19554c14385.png align="center")

En essayant avec l’ASN de CloudFlare, on a des informations comme le positionnemnt de l’AS, le nombre de clients ou de réseaux qui dépendent de CloudFlare. Il y a aussi la liste des ASNs voisins qui montre les autres organisations avec lesquelles CloudFlare interagit. Toutes ces informations peuvent aider à mieux comprendre la topologie d’un réseau.

## BGP.HE.NET

BGP.HE.NET est un service internet de [Hurricane Eletric](https://bgp.he.net/) qui fournit des informations sur le DNS, l’ASN associé au domaine et les routes BGP utilisées pour atteindre ce domaine.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727270240784/efc73d40-2cd3-4f96-af98-616a4dc4b38c.png align="center")

## ASNmap

ASNmap est un outil de reconnaissance qui permet de retrouver les adresses IP associées à un domaine. Il est surtout utilisé pour avoir des infos sur les réseaux ou les serveurs. Le guide d’installation est disponible sur [Github](https://github.com/projectdiscovery/asnmap).|

Installez d’abord Go sur votre machine avec la commande suivante:

```python
sudo apt install golang-go
```

Clônez ensuite le dépôt depuis GitHub:

```python
go install github.com/projectdiscovery/asnmap/cmd/asnmap@latest
```

Lors de la première utilisation de ASNmap, il vous faudra créer un compte sur [Project Discovery Platform](https://cloud.projectdiscovery.io/), pour avoir une clé gratuite pour l’utilisation de ASNmap.

### Options

ASNmap supporte plusieurs options:

* `asnmap -a ASXXXX` pour la recherche d’un ASN spécifique,
    
* `asnmap --ip 192.168.2.0` pour une adresse IP,
    
* `asnmap -d domain.com` pour interroger un nom de domaine,
    
* `asnmap -org ORGANIZATION` pour une organisation.
    

Il est possible d’avoir plusieurs options dans une même commande. Toute la documentation est disponible sur [Project Discovey Blog](https://blog.projectdiscovery.io/asnmap/).

En faisant une recherche sur le domaine facebook.com avec l’option `-j` (affichage en format JSON), on obtient des informations comme l’ASN, le nom et le pays associé à l’AS, et aussi les plages d’adresses IP.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727272562026/b22acae9-563c-4735-9af1-a84537ecfbe3.png align="center")

L’option `jq` ci-dessus, permet de formater et manipuler les données au format JSON

Pour les scans, l’outil le plus connu est peut être Nmap. Mais il en existe d’autres qui sont beaucoup plus rapides et efficaces.

## Naabu

Naabu est un scanner de ports devéloppé par [Project Discorvery](https://projectdiscovery.io/) et écrit en lagange Go, pour identifier les services ouverts sur une machine ou un réseau. Il est connu pour être plus rapides avec beaucoup d’options, comme le fait de pouvoir exclure des ports, scanner toute une liste de ports ou encore le combiner avec Nmap pour avoir des résultats plus détaillés. Vous pouvez l’installer directement via [Github](https://github.com/projectdiscovery/naabu).

Installez d’abord Go sur votre machine avec la commande suivante:

```python
sudo apt install golang-go
```

Installez ensuite Naabu

```python
go install -v github.com/projectdiscovery/naabu/v2/cmd/naabu@latest
```

### Options

* L’option `-p` permet de scanner un port spécifique, et l’option `-exclude-ports`, exclut ici, les ports 22 et 80.
    
    ```python
    naabu -host [DOMAIN or IP] -exclude-ports 22,80
    ```
    
* L’option `-list` ou `-l` effectue un scan avec un fichier contenant une liste de domaines ou d’adresses IP.
    
    ```python
    naabu -list domains.txt -p 22,80
    ```
    
    Cette commande va scanner les hôtes à l’intérieur du fichier domains.txt, mais juste les ports 22 et 80
    
* L’option `-json` permet d’avoir les résultats en format JSON. On peut ajouter l’option `-o` pour sauvegarder les résultats dans un fichier.
    
    ```python
    naabu -host [DOMAIN] -o output.json -json
    ```
    
* L’option `-nmap-cli` permet d’exécuter la commande nmap directement sur les résultats obtenus. Nmap doit être déjà installé pour que ca fonctionne.|
    
    ```python
    naabu -host [DOMAIN] -p [PORTS] -nmap-cli 'nmap -sV -Pn'
    ```
    
    Cette commande exécute Naabu pour scanner les ports spécifiés sur le domaine puis lance le service nmap sur chaque port.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727275114325/257b5e06-9e0e-41f8-9ec8-2c8f78d320ae.png align="center")
    
* Il est aussi possible de combiner Naabu avec ASNmap;
    
    ```python
    asnmap -a ASXXXX -silent | naabu -p 22,80 -silent
    ```
    
    Cette commande va d’abord récupérer les plages d’adresses IP associées à l’ASN en mode silent et ensuite scanner les ports 22 et 80 avec Naabu
    
    <mark>Il est important de rappeler que l'utilisation de ces outils doit toujours se faire dans un cadre légal.</mark>
    

## Rustscan

RustScan est aussi un scanner de ports, écrit en langage Rust, beaucoup plus rapide et capable d’intégrer des outils comme Nmap pour des analyses plus détaillées.

Vous pouvez le télécharger depuis la biliothèque officiel sur [Github](https://github.com/RustScan/RustScan). Vous pouvez suivre un article de Gokul sur Medium qui détaile l’installation [ici](https://gokulelango.medium.com/how-install-rustscan-in-kalilinux-5da3663d33b7).

### Options

* L’option `-a` spécifie l’adresse ou le domine cible et l’option -p, pour un ou plusieurs ports spécifiques
    
    ```python
    rust -a [DOMAINE/IP] -p 22,80
    ```
    
* RustScan redirige directement ses résultats vers Nmap pour d’autres anlayses. Il est aussi possible de les modifier à votre guise.
    
* L’option -- -sV passe les résultats à Nmap pour la détection des versions des services
    
* L’option `-b` limite la bande passante et éviter de surcharger le réseau pendant le scan.
    

Ici on scanne les ports 22,80 et 445 de l’adresse 192.168.2.96. Le double-tiret indique les options sont passées à Nmap, avec l’option -sV, pour détecter la version des services

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727278498585/6361b167-60f4-4f3d-a954-9a44ed375139.png align="center")

Pour finir, ces différents outils offrent des fonctionnalitées utiles lors de la reconnaissance dans un test de pénétration. Cependant, il est important de toujours les utiliser dans un cadre légal avec les autorisations appropriées.