---
title: "TryHackMe Room techsupp0rt1"
seoTitle: "TechSupp0rt1 Room Guide | TryHackMe"
seoDescription: "Use Nmap, smbclient, and CyberChef in TryHackMe's TechSupp0rt1 room to uncover hidden pages, crack passwords, and access secure interfaces"
datePublished: Thu May 08 2025 00:53:08 GMT+0000 (Coordinated Universal Time)
cuid: cmaenldid00010al2dp4l662b
slug: tryhackme-room-techsupp0rt1
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1746666074405/6635c279-e915-4a47-a067-46b62a14daa5.png
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

La page `http://10.10.74.44/test/` semble être une page malveillante

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

Après avoir fouillé un peu