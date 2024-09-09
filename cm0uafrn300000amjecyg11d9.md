---
title: "Configuration des alertes FIM sur Wazuh"
seoTitle: "Alertes Wazuh: Guide de Configuration"
seoDescription: "Guide complet pour configurer les alertes sur Wazuh et surveiller l'intégrité des systèmes Windows et Linux"
datePublished: Mon Sep 09 2024 00:52:07 GMT+0000 (Coordinated Universal Time)
cuid: cm0uafrn300000amjecyg11d9
slug: configuration-des-alertes-fim-sur-wazuh
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1725633287903/4fd42032-f394-4a55-9964-718411a5b6e4.png
tags: wazuh, bruteforceattack, wazuh-dashboard, wazuh-fim, whodata

---

Après avoir installé l'agent Wazuh sur les terminaux, la prochaine étape est de configurer des règles de détection. Il en existe plusieurs expliqueés sur le site de officiel de [Wazuh](https://documentation.wazuh.com/current/proof-of-concept-guide/index.html).

Dans cet article, on expliquera comment mettre en place les alertes FIM et celles sur les attaques par force brute.

## File Integrity Monitoring / Surveillance de l’intégrité des fichiers

Les alertes FIM sont des alertes générées lorsque des modifications sont apportées aux fichiers ou répertoires surveillées. Il peut s'agir de la création, la modification ou la suppression d'un fichier. Le FIM détecte aussi les changements de permissions de lecture et d'écriture.

### **Pour les systèmes Windows:**

Se déplacer dans le répertoire `C:\Program Files (x86)\ossec-agent\` et modifier le fichier `ossec.conf` avec le le blocnote en ajoutant la commande suivante dans le block `<syscheck>`.

Mettre le chemin du répertoire que vous souhaitez monitorez à la place de `C:\PATH` dans le bout de code ci-dessous:

```bash
<directories check_all="yes" report_changes="yes" realtime="yes">C:\PATH</directories>
```

Dans l'image qui suit, l'analyse se fera au niveau des utilisateurs "Public" et "Administrateur" (le répertoire Desktop/Bureau pour l'Administrateur).

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725059235328/cfcfd3ce-ad53-4189-acbe-c56fba0c1f9c.png align="center")

Plusieurs paramètres de base sont spécifiés : la surveillance en temps réel avec `"realtime"`, le signalement de toutes les modifications apportées aux fichiers avec `"report_changes"` et la surveillance des métadonnées des fichiers avec `"check_all"`.  
Il est possible d'être plus précis sur les paramètres à monitorer et créer des règles personnalisées. Plus de détails sont disponibles [ici](https://documentation.wazuh.com/current/user-manual/capabilities/file-integrity/creating-custom-fim-rules.html) .

Ouvrir le Gestionnaire de services via Win+R et redémarrer le service wazuh dans la liste.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725056970915/c92c3cd5-ce4c-4407-bfd8-b02a47da2c29.png align="center")

Wazuh répertorie tous changements apportés au répertoire `Desktop` de l'utilisateur Administrateur et à tous les réportoires de l'utilisateur `Public`, en spécifiant la date, l'évènement, l'agent concerné et d'autres informations.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725060796638/fcb92448-94bb-4bee-82c9-81c685ed0d7c.png align="center")

### **Pour les systèmes Linux:**

Accéder au fichier de configuration avec root avec la commande suivante:

```bash
nano /var/ossec/etc/ossec.conf
```

Ajouter la ligne suivante à l'intérieur du bloc `<syscheck>` et remplacer `C:\PATH` par le chemin du repertoire que vous souhaitez monitorez.

```bash
<directories check_all="yes" report_changes="yes" realtime="yes">C:\PATH</directories>
```

Ici, on monitore tous les changements apportés aux fichiers sur le Bureau

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725067953457/3dadc1dd-3b08-4b1b-a5a6-f72052b7027b.png align="center")

Rédemarrer l'agent wazuh

```bash
sudo systemctl restart wazuh-agent
```

On voit aussi que toutes les actions sont repertoriés.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725068782964/0e9580dc-1ca7-4eb0-9a9b-995ee8078219.png align="center")

## Whodata

La règle `"who-data"` est une amélioration de la règle `"realtime"` car elle inclut la surveillance en temps réel ainsi que des informations supplémentaires sur l'identité de la personne ayant apporté des modifications aux fichiers surveillés, le programme et les processus impliqués.

### **Pour les systèmes Windows:**

Modifier le fichier `ossec.conf` dans le répertoire `C:\Program Files (x86)\ossec-agent\` en placant le lien vers le répertoire que vous souhaitez monitorer. Remplacez C:\\PATH par le chemin du repertoire que vous souhaitez monitorez.

```bash
  <directories check_all="yes" whodata="yes">C:\PATH</directories>
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725063632449/81774b31-4dfa-4297-8818-81a920da88a5.png align="left")

Redémarrez le service Wazuh

On a donc plus d'informations à ce niveau comme le nom de l'utilisateur, le processus.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725066540607/9a880259-925c-4988-8e02-2100635dc111.png align="left")

### **Pour les systèmes Linux (Debian):**

Installez l'audit et avec la commande suivante :

```bash
apt-get install auditd
```

Installez le plugin `audispd af_unix` et redémarrez le service d'Audit

```bash
apt-get install audispd-plugins
systemctl restart auditd
```

Modifiez le fichier `/var/ossec/etc/ossec.conf` et ajouter la ligne suivante à l'intérieur du bloc &lt;syscheck&gt;.

Remplacez `C:\PATH` par le chemin du repertoire que vous souhaitez monitorez.

```bash
 <directories check_all="yes" report_changes="yes" whodata="yes">C:\PATH</directories>
```

Redémarrez l'agent wazuh

```bash
systemctl restart wazuh-agent
```

On voit aussi beaucoup plus de détails notamment grâce aux règles personnalisées.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725070748789/fbf00b55-f357-4dbb-b0a9-89543ec3b5c4.png align="center")

## Detecting a brute force attack/Détection d’une attaque par force brute

Wazuh est aussi en mesure d'identifier des attaques par forces brutes suite à des tentatives d'authentification multiples échouées.

D'abord, au niveau de votre serveur, vérifier que le bloc suivant est présent dans le fichier `/var/ossec/etc/ossec.conf` dans le block `<ossec_config>`

```bash
<command>
  <name>firewall-drop</name>
  <executable>firewall-drop</executable>
  <timeout_allowed>yes</timeout_allowed>
</command>
```

Si il n'y est pas, vous pouvez le rajouter.

Ajoutez ensuite la réponse active dans le même fichier avec le code suivant:

```bash
<active-response>
    <command>firewall-drop</command>
    <location>local</location>
    <rules_id>5763</rules_id>
    <timeout>180</timeout>
  </active-response>
```

Redémarrez le wazuh manager pour exécuter le changement

```bash
systemctl restart wazuh-manager
```

Installer Hydra sur la machine attaquante

```bash
sudo apt update
sudo apt install -y hydra
```

Créer un fichier texte avec 10 ou 15 mots de passes aléatoires. Vous pouvez utiliser la commande suivante qui crée 10 mots de passes aléatoires de 8 caractères.

```bash
pwdgen 8 10 > password.txt
```

Lancer l'attaque avec Hydra avec la commande suivante en remplacant `<RHEL_IP>` par l'IP de la machine à attaquer et `<user>` par l'utilisateur de la même machine.

```bash
sudo hydra -l <user> -P password.txt <RHEL_IP> ssh
```

Pour l'exécuter sur des systèmes windows, la commande est la suivante:

```bash
sudo hydra -l <user>-P password.txt rdp://<WINDOWS_IP>
```

Vous pouvez maintenant visualiser l'incident dans le dashboard Wazuh, dans la section Threat Hunting au niveau des évènements.

Mettre en place des règles de détection est cruciale pour renforcer la sécurité des systèmes. En surveillant les potentielles menaces, votre infrastructure devient donc plus résiliente aux attaques.