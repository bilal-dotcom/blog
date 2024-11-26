---
title: "Recherche de sous-domaines"
seoTitle: "Sous-domaines : Méthodes de Recherche Efficaces"
seoDescription: "Guide pour énumération de sous-domaines: Amass, OneForAll, Chaos (ProjectDiscovery), Subfinder, Assetfinder,Sublist3r. "
datePublished: Tue Nov 26 2024 05:00:20 GMT+0000 (Coordinated Universal Time)
cuid: cm3xznfef000609mjfyoy2eqk
slug: recherche-de-sous-domaines
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/4vBN9dMCip0/upload/8cd10929b076bb06310adf408ec01b0b.jpeg
tags: subfinder, cybersecurity-1, cybersec, bugbounty, subdomains, bugbountytips, subdomain-enumeration, subdomain-discovery, assetfinder, amass, sublist3r

---

La recherche de sous-domaines permet d’identifier les sous-domaines rattachés à un domaine principal qui pourraient être vulnérables, et donc des points d’entrée pour des services mal configurés. Le fait de découvrir les sous-domaines permet donc recueillir des informations sur les technologies et configurations utilisées.

## Crt.sh

[**Crt.sh**](http://Crt.sh) est un outil en ligne qui permet de rechercher et de consulter des certificats SSL/TLS pour divers domaines. Il peut aussi bien être utilisé pour trouver des sous-domaines. En recherchant un domaine principal, on peut voir les certificats associés, mais aussi ceux émis pour ses sous-domaines. Les recherches peuvent donc se poursuivre avec ces sous-domaines avec d’autres outils.

## Virus Total

[VirusTotal](https://www.virustotal.com/gui/) est un outil en ligne qui permet d’analyser des fichiers, des URLs afin de détecter de potentiels virus. Il effectue une analyse complète en utilisant plusieurs moteurs antivirus et outils de détections d’URLs. Il peut aussi être utilisé pour rechercher des sous-domaines et d’autres informations.

Les sous-domaines associés à la recherche seront listés dans l’onglet Relations. La photo ci-dessous montre une liste de sous domaines associés à Amazon.ca.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730480512189/389d5154-f775-4c5b-bbdc-36b533f0b940.png align="center")

## Amass

Amass est un outil développé par l'OWASP (Open Web Application Security Project) qui est aussi utilisé pour l’identification de sous-domaines et des adresses IP associées en utilisant des sources publiques commes des APIs, des certificats SSL, des DNS etc.

La documentation officielle se retrouve sur [**GitHub**](https://github.com/owasp-amass/amass).

Pour installer Amass, utilisez la commande suivante:

```python
go install -v github.com/owasp-amass/amass/v4/...@master
```

### Options

* `amass enum` : ce mode est utilisé pour l’énumération des sous-domaines en utilisant des sources publiques et des techniques de reconnaissance active.
    
* `amass intel`: ce mode est utilisé pour découvrir des domaines et d'autres actifs qui peuvent être explorés plus en détail lors du processus d'énumération
    
* `amass enum -d website.com -active -brute -aw /PATH -bl “word“`
    
    * l’option `-d` spécifie le domaine dont on veut énumérer les sous-domaines
        
    * l’option `-active` permet de découvrir des sous-domaines actifs
        
    * l’option `-aw` permet de spécifier un fichier de liste de mots qui sera utilisé pour les altérations des noms des sous-domaines. Cela permet de modifier dynamiquement les sous-domaines en ajoutant des termes dans les noms des sous-domaines non visibles dans les résultats de DNS standards.
        
    * l’option `-bl` permet de spécifier des domaines à exclure de la recherche
        

L’image ci-dessous montre la commande amass utilisée, qui a découvert plusieurs informations dont des enregistrements MX, A, AAAA et plusieurs autres sous-domaines. On y retrouve aussi des informations ASN de CloudFlare, qui gère probablement les adresses IP de ces sous-domaines là.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731637967194/67052a86-6ebd-4ee1-a971-5c194d1e4753.png align="center")

## Chaos projectdiscovery

[Chaos](https://chaos.projectdiscovery.io/) est un projet de ProjectDiscovery qui fournit des informations sur les sous-domaines. Il dispose d’une base données avec un grand nombre de sous-domaines pour les domaines publics, mis à jour régulièrement.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730732080367/c021ae5b-7ed5-4b7e-9faf-541409009056.png align="center")

Il suffit donc de télécharger le fichier .zip et on obtient une liste de sous-domaines associés au terme ou au domaine dans la recherche.

Chaos ProjectDiscovery est un projet open-source et peut être aussi utilisé via [GitHub](https://github.com/projectdiscovery/chaos-client). L’utilisation de cet outil avec l’API en ligne de commande est beaucoup plus flexible et puissante en terme de recherches. En utilisant l’outil en ligne de commande, on obtient une liste plus complète, contrairement à la version web où l’utilisation peut ne retourner aucun résultat pour certains domaines

Pour l’installer, utilisez la commande suivante:

```python
go install -v github.com/projectdiscovery/chaos-client/cmd/chaos@latest
```

### Options

* `chaos -h` : cette option liste toutes les options de Chaos ProjectDiscovery
    
* `export PDCP_API_KEY=[API_ KEY]`
    
    * cette option permet de spécifier la clé API qui peut être obtenue simplement en s’inscrivant sur le site
        
    * la clé API doit être spécifiée obligatoirement pour l’utilisation en ligne de commande
        
* `chaos -d [site.com] -count -o list.txt`
    
    * l’option `-d` spécifie le domaine à analyser
        
    * l’option `-count` affiche le nombre de sous-domaines sans les lister
        
    * l’option `-o` spécifie un fichier (ici list.txt) pour stocker la sortie
        
* `chaos -dL [list_domains.txt] -json | grep [STRING] > list.txt`
    
    * l’option `-dL` spécifie un fichier contenant une liste de domaines à analyser
        
    * l’option `-json` affiche la sortie en format JSON
        
    * l’option `grep` filtre les résultats de la recherche selon le string défini et les enregistre dans le fichier list.txt
        

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730734621204/84dc8d3e-161e-4467-a504-ea6e0b66b14b.png align="center")

## Subfinder

Subfinder est outil d’énumération de sous-domaines écrit en langage Go, développé par [ProjectDiscovery](https://projectdiscovery.io/). Il est utilisé pour effectuer une énumération passive des sous-domaines d’un domaine cible, c’est-à-dire, qu'il utilise des sources en ligne publiques pour identifier les sous-domaines sans interagir directement avec les serveurs cibles. Il utilise des sources comme Censys, Chaos, Recon.dev, Shodan, Spyse, Virustotal etc.

La documentation officielle se retrouve sur [GitHub](https://github.com/projectdiscovery/subfinder).

Pour installer Subfinder, utilisez la commande suivante:

```python
go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest
```

### Options

* `subfinder -h` : l’option `-h` permet de lister toutes les options de subfinder
    
* `subfinder -ls`: l'option `-ls` permet de lister toutes les sources de données possibles
    
* `subfinder -d [site.com] -all -json -cs -o [list.txt]`
    
    * l’option `-d` spécifie le domaine à analyser
        
    * l’option `-all` permet d’utiliser toutes les sources pour l’énumération.
        
    * l’option `-json` affiche la sortie en format JSON
        
    * l’option `-cs` permet d’afficher la source dans le résultat de la commande
        
    * l’option `-o` enregistre les résultats dans un fichier
        

Ici, on analyse le domaine hackerone.com en affichant les sources de chaque sous-domaine avec l’option `-cs`, et en excluant les sources du serveur google 8.8.8.8 avec l’option `-es`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730743484088/7cbc99d4-ff88-444c-81e0-26a35f294ebf.png align="center")

* `subfinder -dL [list_domains.txt] -s/-es [SOURCES] -rl [INT]`
    
    * l’option `-dL` spécifie un fichier contenant une liste de domaines à analyser
        
    * l’option `-s` spécifie les sources à utiliser lors de la recherche
        
    * l’option `-es` exclut les sources lors de la recherche
        
    * l’option `-rl` détermine le nombre de requêtes https par secondes. Cela limite et évite la surcharge
        
* `subfinder -d hackerone.com --active -oI`
    
    * l’option `--active` ou `-nW` vérifie les sous-domaines en temps réel. Elle vérifie si les sous-domaines sont actifs et répondent aux requêtes, sinon, ils ne sont pas listées.
        
    * l’option `-oI` ou `-ip` permet d’inclure les adresses IP des sous-domaines dans le résultat. Cette option ne s’utilise qu’en combinaison avec l’option `--active` ou `-nW`
        
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730746300133/9a589fef-a0de-48d6-a6ba-6811d5ab9b89.png align="center")
    

## Assetfinder

Assetfinder est aussi un outil développé en langage Go, utilisé pour énumérer des sous-domaines associé à un domaine principal. Il utilise des sources comme, certspotter, hackertarget, facebook, virustotal etc.

La documentation officielle se retrouve sur [GitHub](https://github.com/tomnomnom/assetfinder).

Pour installer Assetfinder, utilisez la commande suivante:

```python
go install github.com/tomnomnom/assetfinder
```

### Options

* `assetfinder -h` : l’option `-h` permet de lister toutes les options de assetfinder
    
* `assetfinder -subs-only [SITE.COM] > list.txt`
    
    * l’option `-subs-only` limite la sortie aux sous-domaines du domaine cible. Cette option est la seule disponible pour assetfinder.
        

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730748576730/b64bfe0d-7523-47d7-97ed-7fbf40eed732.png align="center")

## **Sublist3r**

Sublist3r est un outil d’énumération de sous domaines en utilisant des moteurs de recherche et d'autres sources en ligne. Il intègre l’outil [Subbrute](https://github.com/TheRook/subbrute), un outil de brute force pour l'énumération de sous-domaines, afin d'augmenter la capacité de trouver plus de sous-domaines. Subbrute utilise une liste de mots améliorée pour tester de manière systématique des sous-domaines potentiels.

La documentation officielle se retrouve sur [GitHub](https://github.com/aboul3la/Sublist3r).

Pour installer Sublist3r, utilisez la commande suivante:

```python
git clone https://github.com/aboul3la/Sublist3r.git
cd Sublist3r
pip install -r requirements.txt
```

### Options

* `python sublist3r.py -h` : l’option `-h` permet de list[er toute](https://github.com/TheRook/subbrute)s les options de sublist3r
    
* `python sublist3r.py -b -d [site.com] -p 80,21 -o result.txt`
    
    * l’option `-d` permet de spécifier le domaine cible
        
    * l’option `-b` active le mode brute force; ceci permet de découvrir les sous-domaines
        
    * l’option `-p` spécifie les ports à tester pour vérifier si un sous-domaine est actif sur ces ports.
        
    * l’option `-o` enregistre les résultats dans un fichier
        
* `python sublist3r.py -e google,virustotal -v -d [site.com]`
    
    * l’option `-e` permet de spécifier le moteur de recherche à utiliser
        
    * l’option `-v` active le mode verbeux, ce qui permet d'afficher plus de détails pendant l'exécution du script et les sous-domaines en temps réel.
        

La commande ci-dessous exécutée, enumère les sous-domaines de google.com et active le mode verbeux, ce qui permet d’afficher les sous-domaines en temps réel.

La commande recherche les sous-domaines sur plusieurs sources dont Baidu, Yahoo,Google,Bing, SSL Certificates etc.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731947680161/b9251afd-4e84-4196-bdd1-99ab4f68058b.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731947705826/e38a8163-0c8e-48a8-b14e-10d69196c913.png align="center")

## OneForAll

OneForAll est un outil d’énumération de sous-domaines utilisé pour lister les sous-domaines d’un domaine principal. Cet outil utilse plusieurs techniques comme la résolution de DNS, le brute-forcing des sous-domaines et collecte aussi des données à partir de sources publiques que d’autres outils n’interrogent pas généralement. OneForAll règle le problème de puissance ou encore d’efficacité qu’on peut voir avec d’autres outils.

La documentation officielle se retrouve sur [GitHub](https://github.com/shmilylty/OneForAll).

Pour installer OneForAll, utilisez les commandes suivantes:

Vérifiez d’abord les versions de Python et pip3 . La version de Python3 doit être supérieure à 3.6.0 et la version de pip3, supérieure à 19.2.2.

```python
python -V
pip3 -V
```

Clonez le dépôt du projet.

```python
git clone https://github.com/shmilylty/OneForAll.git
```

Créez un environnement virtuel pour séparer l’installation de votre environnement python.

```python
virtualenv -p python3 .venv
source .venv/bin/activate
```

Installez les dépendances

```python
cd OneForAll
python3 -m pip install -U pip setuptools wheel
pip3 install -r requirements.txt
```

### Options

* `python3 oneforall.py -h` : cette option permet de lister toutes les options de OneForAll
    
* `python oneforall.py --target [site.com] --brute FALSE --dns FALSE --valid TRUE run`
    
    * l’option `--target` permet de spécifier l’URL ou le domaine à analyser
        
    * l’option `--brute` permet d’activer/désactiver le module de bruteforce pour rechercher les sous-domaines. Par défaut, l’option est activée
        
    * l’option `--dns` permet d’activer/désactiver la résolution DNS lors de l’analyse des sous-domaines. Cette option est aussi activée par défaut.
        
    * l’option `--valid` permet de spécifier que seuls les sous-domaines actifs et valides seront pris en compte. L’outil va donc interroger chaque sous-domaine afin de vérifier leur validité.
        
* `python oneforall.py --targets list_urls.txt --takeover TRUE --path /PATH/TO/FILE run`
    
    * l’option `--targets` permet de spécifier une liste de domaines contenue dans un fichier
        
    * l’option `--takeover` permet de vérifier si un sous-domaine trouvé est vulnérable à une prise de contrôle de sous-domaine (subdomain takeover). Cette option est cruciale dans l’identification des sous-domaines mal configurés. Elle est par défaut désactivée.
        
    * l’option `--path` spécifie le chemin du fichier où seront stockés les résultats de la collecte de sous-domaines. Lorsqu’un fichier n’est pas spécifié, OneForAll générera automatiquement un fichier de résultats.
        
    * Comme le montre l’image ci-dessous, l'outil génère automatiquement un fichier `.csv` qui répertorie les résultats, dans le dossier `/results`
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731290129863/dd8e94e3-1729-4a26-91eb-92005077424d.png align="center")
        

### Interprétation des résultats

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731279673339/8f583716-d6db-4520-8505-a765df582355.png align="center")

Les résultats de la commande OneForAll sur `https://hackerone.com` montrent les modules exécutés ainsi que le nombre de sous-domaines trouvés. Par exemple, avec les résultats de cette requête, on voit que le module `CertInfo` a trouvé 4 sous-domaines en 0,1 seconde, l'accès au site `dnsgrep.cn` a été refusé (statut403) et d’autres informations.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1731290099020/204d1d6d-7444-43a0-8db8-b01979d7aaee.png align="center")

Les serveurs `a.ns.hackerone.com` et `b.ns.hackerone.com` ont aussi été trouvés, ce qui a permis de retourner les adresses IP 162.159.0.31 et 162.159.1.31.

Les sous-domaines trouvés seront répertoriés dans le dossier `/results` par défaut dans un fichier csv qui peut être compliqué à lire. Vous pouvez formater les colonnes à l’affichage avec les commandes column et less:

```python
column -s, -t < hackerone.com.csv | less -#2 -N -S
```

* L’option `-s,` (-s suivi de la virgule) spécifie que la virgule ( **,** ) est le délimiteur qui sépare les colonnes dans le fichier csv
    
* L’option `-t` indique à column de créer un tableau avec des colonnes alignées
    
* `< hackerone.com.csv` permet une redirection du contenu du fichier hackerone.com.csv comme entrée pour la commande column
    
* `less -#2 -N -S` permet de paginer la sortie avec `less`, affiche les numéros de ligne avec `-N`, permet un défilement horizontal avec `-S`, et définit l'espacement des tabulations à 2 avec `-#2`. Vous pouvez quitter l’affichage avec la touche `q` .
    

Le fichier avec les résultats avec un affiche standard se présente comme suit:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732226018213/a9a55b8d-66c6-4fa6-a787-9901d650b261.png align="center")

On passe donc de l’affichage de l’image au dessus à ceux en dessous avec la commande `column -s, -t < hackerone.com.csv | less -#2 -N -S` .

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732241237226/1f2caa58-61dd-482e-a049-ceb066949a9c.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732241262762/c63c29e9-ff72-40e2-bc88-12c698f62fec.png align="center")

L’énumération de sous-domaines est une étape cruciale dans la collecte d’informations, permettant d’identifier des parties d’un domaine qui pourraient être mal configurées ou vulnérables. Avec des outils comme OneForAll, Subfinder pour une recherche passive, Assetfinder pour sa rapidité, et Sublist3r pour combiner recherche passive et brute force, on a donc des informations précieuses sur l'infrastructure d'un domaine

<mark>À noter, qu’il est important de rappeler que l'utilisation de ces outils doit toujours se faire dans un cadre légal.</mark>