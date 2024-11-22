---
title: "OneForAll"
seoTitle: "OneForAll : Outil d'énumération de Sous-domaines"
seoDescription: "Découvrez OneForAll, l'outil Python open-source pour l'énumération de sous-domaines qui combine résolution DNS, brute-force et détection de vulnérabilités"
datePublished: Fri Nov 22 2024 02:50:07 GMT+0000 (Coordinated Universal Time)
cuid: cm3s58kgw001j08kv85ujho7e
slug: oneforall
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1732225910900/3f857e38-5663-43b6-994d-2df37c6b94d4.png
tags: cybersecurity, cybersecurity-1, cybersec, subdomains, map-subdomain, oneforall, subdomain-enumeration

---

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

OneForAll est un outil essnetiel en matière d’énumération de sous-domaines de part sa capacité à collecter le sous-domaines de manière rapide et précise.

<mark>À noter, qu’il est important de rappeler que l'utilisation de cet outil doit toujours se faire dans un cadre légal.</mark>