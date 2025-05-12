---
title: "TryHackMe Room Expose"
seoTitle: "Expose: TryHackMe Room"
datePublished: Sun May 11 2025 02:05:00 GMT+0000 (Coordinated Universal Time)
cuid: cmaj0hcwk000r08kwd36vhvcc
slug: tryhackme-room-expose
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1746929181732/7a6510f3-b3d1-4692-92b0-efb91b91f99f.png
tags: tryhackme, thm, thm-presecurity-path, thm-writeup, tryhackme-walkthrough, tryhackmewalkthrough, thm-room

---

Lien de la room: [https://tryhackme.com/room/expose](https://tryhackme.com/room/expose)

# User Flag

On débute par un scan Nmap. On voit 4 ports ouverts dont la 1337.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746918082078/fb26e2c2-1ba6-4fd7-9f31-4e9c6c87155d.png align="center")

En examinant cette page et le code source, rien qui pourrait nous servir.

Je tente alors une commande avec `gobuster` pour énumérer des répertoires ou fichiers cachés. On trouve ici deux répertoires: `admin` et `admin_101`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746918274932/fab535a2-9aeb-4b39-bb1a-0b98a94d5733.png align="center")

La page `http://10.10.194.184:1337/admin/` est un formulaire de connexion.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746918399680/802c909f-1e30-4c57-ba1c-2352767493f8.png align="center")

La page `http://10.10.194.184:1337/admin_101/` héberge le même formulaire, mais ici la valeur de l’email est défini par défaut à `hacker@root.thm`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746918465796/0eb76aae-1116-4ae4-9ab2-03421d1c894e.png align="center")

J’ai tenté de voir avec Burp. Après avoir envoyé la requête dans le Repeater, on a une requête SQL mais qui n’inclue pas le mot de passe.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746921893931/a82ed4af-ed7c-4d19-b420-fc3ca394e52f.png align="center")

On va donc tenter une injection SQL ici. On voit aussi que larequête est envoyée par `POST` à `includes/user_login.php`.

On teste avec `ffuf` avec une liste de payloads d’injection SQL. Mais on a aucun résultat.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746920786294/b821aba5-951b-4d67-9b27-69dea32836f5.png align="center")

Un autre moyen est de le faire avec sqlmap, un outil d’exploitation par injections SQL. On exécute la commande suivante: `sqlmap -u "http://10.10.230.197:1337/admin_101/includes/user_login.php" --data="email=hacker@root.thm&password=test" --batch --dbs --dump` . Cette commande va permettre de lister toutes les bases de données disponibles et de récupérer le contenu des bases de données identifiées.

On voit donc trois base de données, dont une du nom de `expose` qui contient 2 tables: `config` et `user`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746924564109/35aaf430-4f7b-43bd-b58c-cb8a44e01d2d.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746925223418/5c04ac2f-be4c-4cb0-b4d3-03d8603ff1ad.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746925089013/af88a29f-7265-413a-b01d-7fb8205408a7.png align="center")

On a maintenant le nom d’utilisateur `hacker@root.thm` et son mot de passe. On arrive ensuite à se connecter, mais la page n’est pas tant utile et on a rien dans le code source.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746925188084/224fc1f5-1631-400c-a30a-45f321cae231.png align="center")

En visitant `/file1010111/index.php` , trouvé dans la table config, on tombe sur un formulaire qui demande un mot de passe.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746925325428/e272ea5c-1bd4-478d-9fdb-3da781d4b445.png align="center")

Le mot de passe fourni dans la table est peut-être codé. En allant sur dcode, on l’obtient en clair.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746925628778/c24ed192-42e6-4ea6-95dd-00169edf013e.png align="center")

On tombe à nouveau sur une autre page.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746925715534/4b0d4833-0167-4add-901f-021619cd0a42.png align="center")

Dans le code source , on a une sorte d’indice qui nous dit d’essayer avec des paramètres.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746926125658/ffcf0b11-6fab-46a5-beef-f7551ca57e02.png align="center")

On va donc manipuler l’URL. J’ai essayé `http://10.10.230.197:1337/file1010111/index.php?file=test` ou encore `http://10.10.230.197:1337/file1010111/index.php?file=1`, mais rien.

Dans la deuxième entrée de la table config, il y avait une autre URL qui indiquait ONLY ACCESSIBLE THROUGH USERNAME STARTING WITH Z. Il faut donc voir pour des username, faisant allusion à `/etc/passwd`. J’ai donc tenté `http://10.10.230.197:1337/file1010111/index.php?file=../../../../../etc/passwd`.

On a bel et bien un nom d’utilisateur débutant par z.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746926726627/a46430a2-a29d-468c-afa6-bada92ebc163.png align="center")

On tombe sur une page qui permet d’upload un fichie, via le répertoire `/upload-cv00101011`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746926747852/3ede118a-4733-4f55-a5ea-283b651948e8.png align="center")

En regardant le code source, on voit que le site accepte juste un fichier `jpg` ou `png`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746926885871/255e83de-3717-4da6-902b-5ce741719e53.png align="center")

On y upload une photo.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746927186945/cbdde2b1-fe3d-4313-a432-cf4c4eab6531.png align="center")

On regarde à nouveau le code source, on voit que les fichiers sont téléversés dans le répertoire `/upload_thm_1001`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746927238097/38023f3b-b690-4539-b342-244dcf7d9bbd.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746927543316/f90e958a-83c8-42da-ad2e-7e23f6f80c54.png align="center")

Les photos sont sous le répertoire `/upload_thm_1001`.

Ce qu’on va donc faire cest téléverser un fichier qui sera notre reverse shell. Vu que le serveur utilise php, on utilisera un reverse shell en php. On peut créer un revshell sur [Online - Reverse Shell Generator](https://www.revshells.com/).

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746927780753/38e2b314-c71f-404c-84e4-4bbefad3c6d9.png align="center")

J’ai créé un fichier `rev_shell.php` mais que jai renommé `rev_shell.png`, pour que l’upload réussisse.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746928195307/23ef5adf-deb4-421f-a1c5-28c69d12676d.png align="center")

Il faut donc renommer le fichier une fois dans Burp en `rev_shell.php` puis forward la requête

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746928376921/96531464-8652-4050-8404-c296e90db17a.png align="center")

On retourne dans le répertoire `/upload_thm_1001` *et on voit* que le fichier `rev_shell.png` que j’ai renommé dans la requête en `rev_shell.php` a bien été téléversé comme le montre la photo ci-dessous.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746928308535/3204c963-3b04-44bf-815d-65ab642fedb1.png align="center")

Il suffit donc de cliquer sur la photo, et on a le shell.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746928635360/a59c5ad5-4401-4d86-9ca8-f4afa0537c75.png align="center")

On est connecté en tant que www-data. Mais une fois connecté, je ne peux pas lire le flag de l’utilisateur. Mais le fichier ssh\_creds.txt contient ses identifiants avec lesquels on peut juste se connecter.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746928738230/81a19a3e-6b03-4ee4-85a6-5b85da035add.png align="center")

J’ai donc changé d’utilisateur, avec la commande `su zeamkish` pour passer de `www-data` à `zeamkish`, entré son mot de passe, et j’ai accès à `flag.txt`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746928971279/046b807a-b87b-417c-b81d-641e9ddd7734.png align="center")

# Root Flag

La commande `sudo -l` ne nous sort rien. Donc zeamkish n’a aucun privilège root.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747012509027/b3a50133-d9d7-4ef1-b51e-5bab110fb9e8.png align="center")

On essaie aussi de voir un fichier potentiel de flag contenant le terme root, mais rien.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747014225385/acacd5e4-f2cf-4885-9527-9e2cc97d49b5.png align="center")

Finalement, on regarde les fichiers SUID, qui sont des fichiers ou programmes qui s’exécutent avec les droits de leur propriétaire, souvent root. Avec la commande `find / -type f -perm -u=s 2>/dev/null` ou `find / -type f -perm 4000 2>/dev/null`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747014551899/e78aa8b2-63d7-4a7e-948b-39ff91fb4c02.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747075197669/343beb99-de49-46f6-aef7-16954161accf.png align="center")

On a une liste de binaires SUID avec le bit s, et qui appartiennetnt à root. Il est possible ici d’utiliser plus d’un.

## /usr/bin/nano

Le binaire `/usr/bin/nano` permet de modifier des fichiers avec des privilèges root. On peut donc modifier le fichier `/etc/shadow` qui contient les mots de passes des utilisateurs. Pour cela, on génère d’abord le hash du mot de passe avec `openssl`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747015985184/383a5d86-56a9-44e7-bda9-a4fe74a35bac.png align="center")

On copie ensuite ce hash, au niveau de root, juste après `root:` avec la commande `/usr/bin/nano /etc/shadow`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747016118148/3b237ccb-c853-43a4-a788-00425d22b42b.png align="center")

On passe maintenant à root, et on a le flag.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747016158821/508e2a65-5138-4fe4-8388-54672bd22933.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747016198415/a69b43c4-8bd4-4de9-848a-4029b583f032.png align="center")

## /usr/bin/find

On peut aussi utiliser le binaire find pour directement trouver le flag de root. On peut utiliser la commande `/usr/bin/find . -exec /bin/sh -p \; -quit`. Cette commande utilise le binaire find pour exécuter un shell avec les privilèges SUID.

* `/usr/bin/find` est le chemin du binaire trouvé un peu plus haut.
    
* `-exec /bin/sh -p \;` exec est une option de find qui permet d’exécuter des commandes. L’option `-p` permet de conserver les userid effectif (celui de root), ce qui me donne un shell en étant root. On peut le voir dans la capture ci dessous. Sans l’option -p, le shell détecte une différence entre l’UID réel et l’UID effectif et redescend à l’UID réel (celui de l’utilisateur zeamkish).
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747076245158/7f0da240-d495-4420-bebe-6b0a36bf34bd.png align="center")

On peut ensuite lire le flag.txt dans le fichier `flag.txt` de `/root`.