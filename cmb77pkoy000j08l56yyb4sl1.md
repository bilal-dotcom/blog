---
title: "TryHackMe Room Dreaming"
seoTitle: "TryHackMe: Dreaming Room"
seoDescription: "Explore TryHackMe's Dreaming room, practicing network scanning, directory traversal, and privilege escalation to capture various flags"
datePublished: Wed May 28 2025 00:33:49 GMT+0000 (Coordinated Universal Time)
cuid: cmb77pkoy000j08l56yyb4sl1
slug: tryhackme-room-dreaming
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1748356721282/48eb8401-4d69-4ecb-bc22-aa7174d676d7.png
tags: cybersecurity-1, tryhackme, thm, thm-writeup, tryhackme-walkthrough, tryhackmewalkthrough, thm-room, thm-dreamin-room

---

Lien de la room: [https://tryhackme.com/room/dreaming](https://tryhackme.com/room/dreaming)

On débute par un scan nmap, mais rien d’intéressant.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748356975449/375bc3ee-080f-48c7-8301-02a5287bbd1e.png align="center")

On cherche ensuite des répertoires ou fichiers sur le serveur avec gobuster, via la commande gobuster dir -u 10.10.56.225 -w /usr/share/wordlists/dirb/big.txt

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748357891451/75260661-238b-45b9-8027-5b28de7c3b9f.png align="center")

On y trouve un répertoire `/app`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748357918625/31742175-51d3-4215-b67f-2fc3ee95cf44.png align="center")

On tombe ensuite sur une page qui est celle du CMS

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748358276269/52c490c7-d30e-4fe6-8702-91f691cdebc1.png align="center")

En cliquant sur le lien admin, on a une page de ocnnexion juste avec un mot de passe.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748358366104/97eb5ddd-9114-4b66-9c7a-ec9fbe16b8e6.png align="center")

Après une recherche google et sur metasploit, il existe bien un exploit disponible de la version `4.7.13` de pluck, qui permet un upload de fichiers dans `Pluck CMS` pour permettre un code d’exécution à distance. On peut donc essayer d’y téléverser un fichier qui va permettre une connexion en reverse shell sur notre machine.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748358870549/bc823105-24a1-46a8-982f-63e7da349188.png align="center")

J’ai d’abord essayé de manipuler le lien en tentant un LFI, mais on a un message d’erreur. Il semble y avoir un filtre qui bloque les injections LFI.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748358704452/582dc12e-ce9d-4eb7-8e3e-94713a252d9e.png align="center")

On essaie de bypasser la page de login trouvé un peu plus haut.

En examinant le code source de la page de login.php, on a plus d’infos sur les paramètres à utiliser dans la commande avec hydra

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748360245364/1bcca930-8071-453d-a723-7222be45e748.png align="center")

On essaie la commande `hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.56.225 http-post-form "/app/pluck-4.7.13/login.php:cont1=^PASS^&bogus=’’ : Password incorrect"`

Cette commande envouie une requête POST vers `/app/pluck-4.7.13/login.php`, la page du formulaire. `cont1` et `bogus` sont les noms des champs pour le mot de passe et le nom d’utilisateur. Mais celuis du nom d’utilisateur n’intervient pas dans la connexion donc on le met à vide.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748360372344/26cc211e-2957-4b4f-869a-3690f3120957.png align="center")

Après avoir testé les mots de passe un à un, le mot de passe valide est `password`. On tombe sur page `admin.php`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748360646082/0ec60ee9-254e-40e1-b677-33a3c1be41e9.png align="center")

En parcourant un peu le site, on a une section qui nous permet de téléverser un fichier. On va donc créer un reverse shell en PHP.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748384331497/ac41edbf-82e0-4465-b27e-a711c66f470e.png align="center")

Une fois le fichier téléversé, il a été modifié en `rev_shell.php.txt`, ce qui empêche l’exécution directe du code vu que le sevreur le considère juste comme un fichier texte.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748384498958/dc1ac176-3dfd-4639-9654-57534a1b0794.png align="center")

On va donc renommer le fichier en ajoutant une des extensions `.phtml`, `.php5`, `.phar` car celles-ci ne sont pas toujours filtrées.

Après avoir renommé puis téléversé, on appuie sur la loupe, ce qui permet d’accéder à `http://10.10.2.49/app/pluck-4.7.13/files/rev_shell.phar`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748385394684/4adc4c9d-bb54-493e-bba8-c52b4819b22d.png align="center")

On a ensuite le reverse shell sur notre machine.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748385501627/a31252b8-894f-4dc4-8c9c-61ff45d00ce0.png align="center")

# Lucien Flag

Une fois connecté sur la machine cible, on tentera de lire le flag de lucien.

`www-data` n’a pas les permssions. On peut juste le lire en étant connecté à lucien, le propriétaire du fichier lui-même.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748385923587/4c575033-709b-4b69-a003-6d09528219ea.png align="center")

Pareil pour le flag de death et celui de morpheus.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748386083402/57330649-e78c-4657-994d-5b8043a96fe0.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748386065318/619e0f81-15d6-4dbf-81bd-554cf36cc186.png align="center")

On essaie un `sudo -l` pour voir les commandes privilèges que l’utilisateur peut exécuter via sudo.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748387828478/9a11231b-2746-419a-8786-8f73fff3fbcd.png align="center")

On obtient une erreur car sudo demande un mot de passe. On doit donc stabiliser le shell pour pouvoir utiliser sudo -l. Pour stabiliser le shell, utiliser les commandes suivantes:

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm
CTRL+Z
stty raw -echo;fg
ENTRÉE
ENTRÉE
```

Après ca on a un shell interactif, mais on retente sudo -l et on a une demande de mot de passe.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748388259359/974a1f1a-e2f2-4b93-a05c-fbc05e804614.png align="center")

Le user `www-data` n’est pas dans le fichier sudoers avec les droits nécéssaires, on ne peut donc pas passer par cette technique.

On va essayer la commande `find / -type f -perm 4000 2>/dev/null` pour trouver les fichiers SUID, qui sont des fichiers ou programmes qui s’exécutent avec les droits de leur propriétaire.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748390336475/11c3da31-125e-440f-8dc8-6c3019f5dc6d.png align="center")

Mais on a rien 😕.

On essaie la commande `find / -type f -group lucien 2>/dev/null`, qui cherche les fichiers appartenant au groupe `lucien`. Vu qu’on avait trouvé plus haut que le fichier `lucien_flag.txt` appartenait au groupe `lucien`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748391206731/74e4e161-8433-42ae-beb1-2fa1feafaecc.png align="center")

On y voit un fichier `/opt/test.py` qu’on peut lire.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748391580847/ed81ab7a-d4d3-456c-ac07-8f28a1b1dcbb.png align="center")

On y voit une URL qui pointe vers localhost, et un mot de passe. Il s’agit peut-etre du mot de passe de lucien. On tente donc `su lucien` pour se connecter avec le mot de passe trouvé et bingo, on se connecte et on a le flag de l’utilisateur lucien 😁

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748391807442/bdbb59cc-89b8-4cd0-9dd6-b5fa3f499a05.png align="center")

# Death Flag

Ici aussi le fichier death\_flag.txt ne peut-être lu que par son propriétaire. On essaie `sudo -l`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748393162102/27908f4b-86f8-44d2-b78d-385bcc265251.png align="center")

La dernière ligne indique que lucien peut exécuter le script getDreams.py, même si celui-ci appartient à l’utilisateur death.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748394131296/6d313193-4928-4264-b52b-96b2b98b1b85.png align="center")

On essaie donc de voir le contenu de `getDreams.py`, mais on ne peut pas le lire directement dans le répertoire de `death`. Je fais donc la commande `find / -type f -name “getDreams.py“ -readable 2">/dev/null`. On peut lire le fichier `getDreams.py` depuis le répertoire `/opt`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748394446400/4f069151-8285-4a17-85a0-459e84e84412.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748394617015/8a9aff4a-fe40-4937-8333-3b7d3bbe54bb.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748394692493/1fd1019c-4452-4a5f-8015-e8eed53fc735.png align="center")

En analysant le contenu, il s’agit d’opérations effectuées sur une base de données.

# Morpheus Flag