---
title: "Metasploit sur Kali Linux"
seoTitle: "Metasploit Installation et Configuration sur Kali Linux"
seoDescription: "Apprenez à utiliser Metasploit sur Kali Linux pour tester et exploiter les vulnérabilités des systèmes en toute légalité"
datePublished: Tue Sep 03 2024 22:51:39 GMT+0000 (Coordinated Universal Time)
cuid: cm0n0xlhl000608jp0c2j4qaw
slug: metasploit-sur-kali-linux
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1725457803153/1e9828ef-5dba-4481-80ca-34d319165e02.png
tags: kali-linux, kali, pentesting, cybersecurity-1, metasploit, pentesting-tools, metasploit-framework

---

**Metasploit** est un environnement de test d'intrusion sur Kali Linux. Il est utilisé pour tester et exploiter la vulnérabilité des systèmes en utilisant divers exploits et payloads.

* Un exploit est un module de codes qui abuse des vulnérabilités d'un système pour exécuter des actions malveillantes ou compromettre un système.
    
* Un payload est un programme injecté dans la cible après exploitation de la vulnérabilité. Il exécute une action spécifique sur le système compromis après que l'exploit ait réussi. Il peut exécuter diveres actions comme l'ouverture d'une session, l'installation de logiciel malveillant et bien d'autres.
    

### Installation et Configuration de Metasploit

Metasploit est préinstallé sur Kali Linux. Tapez la commande suivante sur votre machine Kali pour accéder à la console Metasploit

```plaintext
msfconsole
```

Cette commande lance la console Metasploit qui affiche l'invite de commande metasploit indiquant que vous êtes à l'intérieur du framework.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725376437221/ac1eec5f-711b-444e-a34e-16f98c721939.png align="center")

La racine de Metasploit se trouve dans le répertoire `/usr/share/metasploit-framework/` accessible depuis le système de fichiers.  
L'architecture de la console Metasploit est directement liée au répertoire `/usr/share/metasploit-framework/modules/`. Par défaut, en lancant la console (msfconsole), Metasploit se réfère directement au dossier `modules`, à l'intérieur duquel il y a tous les modules, tels que les exploits, les payloads, les auxiliaires et bien d'autres.

* le dossier `auxiliary` contient des modules auxiliaires pouvant fournir des informations supplémentaires lors d'une phase de renseignement ou d'une attaque. Il peut être utilisé pour extraire des informations sur les systèmes cibles, comme les versions, les configurations réseau, ou les infos de sécurité. Il contient des sous-dossiers,chacun avec un module pour différentes tâches: `admin` pour des cas d'administration, `dos` pour des cas d'attaque Dos, `scanner` pour des scans de réseaux pour trouver des version, ports et bien d'autres.
    
* le dossier `encoder` contient des modules pour encoder les payloads afin que ceux-ci ne soient pas détectés par les antivirus ou tout autre système de sécurité.
    
* le dossier `exploit` contient les modules d'exploitation de vulnérabilités pour obtenir un accès ou exécuter des commandes non autorisées.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725373144878/371fadae-7b06-4da9-9a13-8a2818c31000.png align="center")

À l'intérieur du dossier exploits, se trouvent tous les exploits correspondants aux vulnérabilités potentielles sur plusieurs sytèmes d'exploitation comme Android, iOS, Linux etc.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725374678609/061847b6-97ff-4fa2-9eb6-1b7afdc3295c.png align="center")

Il est possible d'accéder à ces tous ces différents modules. Vous pouvez sélectionner et utiliser un exploit avec la commande suivante:

```bash
use exploits/linux/telnet/netgear_telnetenable
```

Vous pouvez remplacer ce module par celui que vous souhaitez en modifiant le chemin après `use`.

Pour sortir d'un module et revenir à la console principale de msfconsole, utilisez la commande suivante :

```plaintext
back
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725373666474/fa845090-7b69-496e-87cf-1bb46637a306.png align="center")

Il est possible de rechercher directement un exploit ou un payload, ou un terme dans le chemin d'un exploit ou d'un payload, depuis msfconsole avec la commande suivante:

```bash
search <EXPLOIT/PAYLOAD>
```

Cela permet de trouver les répertoires où se trouve l'exploit ou le terme recherché

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725374027355/59fea05e-37bd-4b77-a655-afc83e3302d9.png align="center")

La commande `info <PATH_EXPLOIT>` affiche des informations détaillées sur un exploit ou un payload spécifique, telles que le nom, la description, l'auteur et surtout les options de configuration. Cette commande permet de comprendre les paramètres d'un exploit ou d'un payload avant de l'utiliser.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725377236948/315e4840-8a79-49e2-8f63-22ef0d8495d3.png align="center")

Vous pouvez accéder à la liste des payloads deuis msfconsole, avec la commande

```bash
show payloads
```

### **Exploitation des Vulnérabilités avec Metasploit**

Une fois à l'intérieur de la configuration de l'exploit avec la commande `use`, il est possible de lister toutes les options avec la commande suivante:

```bash
show options
```

Elle affiche les options configurables de l'exploit avec leur description. Elle peut inclure l'adresse IP de la machine cible, les ports, et d'autres configurations spécifiques à l'exploit.

Pour la suite, il est préférable d'avoir une machine à votre disposition sur laquelle vous souhaitez accéder. <mark>Notez qu'il est interdit d'effectuer ces opérations sur des machines qui ne vous appartiennent pas afin d'éviter tout problème juridique.</mark>

Pour définir une option, il faut entrer `set` suivi du nom de l'option qui peut varier en fonction de l'exploit. Remplacer l'IP par celle de la machine à attaquer.

```bash
set RHOST 192.168.2.20
```

Pour annuler un paramètre spécifique après l'avoir configuré avec la commande `set`, vous pouvez utiliser la commande suivante:

```bash
unset RHOST
```

Vous pouvez également choisir un payload compatible avec l'exploit en utilisant la commande:

```bash
set PAYLOAD cmd/windows/tftp/x64/shell_bind_tcp
```

Une fois toutes les options configurées, vous lancez lancez l'exploit avec la commande suivante:

```bash
exploit
```

Le payload s'exécute par la suite si l'exploit est réussi.

En configurant correctement les exploits et les payloads, et en utilisant les commandes appropriées, vous pouvez tester la sécurité de vos systèmes de manière efficace. Il est important de rappeler que l'utilisation de Metasploit doit toujours se faire dans un cadre légal.