---
title: "Wazuh Intégration VirusTotal"
seoTitle: "Wazuh and VirusTotal Integration Guide"
seoDescription: "Renforcez la sécurité Wazuh avec l'intégration VirusTotal pour des détections et alertes avancées sur fichiers et URL suspects"
datePublished: Mon Sep 09 2024 01:16:52 GMT+0000 (Coordinated Universal Time)
cuid: cm0ubbljn000009lccjac5h2h
slug: wazuh-integration-virustotal
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1725840448744/bebbfcb1-a502-4971-8c89-d77a9042e429.jpeg
tags: virus, cybersecurity-1, wazuh, cybersec, virustotal

---

## Pré-requis:

* [Installation de Wazuh](https://blog.fikara.io/wazuh-installation)
    
* [Déploiement d'un agent wazuh sur une machine](https://blog.fikara.io/deploiement-des-agents-wazuh)
    
* [Configuration des alertes](https://blog.fikara.io/configuration-des-alertes-fim-sur-wazuh) -FIM et Brute Force Attack - (facultatif)
    

Wazuh est en mesure de renforcer les détections, avec une intégration VirusTotal, qui génère des alertes sur les fichiers et URL suspects. Il est possible de faire l'intégration soit avec une API public ou privée. Même si l'API publique a quelques limitations, elle est largement suffisante ici. Pour cela, il faut d'abord créer un compte [VirusTotal](https://www.virustotal.com/gui/home/upload) afin d'obtenir la clé de l'API. La clé API privée requiert un compte premium pour des configurations plus avancées.

La documentation complète se trouve sur le site officiel de [Wazuh](https://documentation.wazuh.com/current/user-manual/capabilities/malware-detection/virus-total-integration.html). Une fois le compte crée, vous pouvez récupérer la clé.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725628431931/0b39fe63-e800-4ff2-b195-17ab736d135c.png align="center")

Alles dans le fichier `/var/ossec/etc/ossec.conf` du serveur et ajouter le code suivant:

```bash
<integration>
  <name>virustotal</name>
  <api_key>API_KEY</api_key>
  <group>syscheck</group>
  <alert_format>json</alert_format>
</integration>
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725628776664/70015b78-f4fc-4f03-bc30-275b47b1ddd2.png align="center")

Remplacez `API_KEY` par votre clé récupérée au niveau de votre compte Virus Total.

Il faut maintenant configurer les machines où sont installées l'agent wazuh, en modifiant le fichier `ossec.conf`.

**Pour les systèmes Windows**, il se trouve dans le répertoire `C:\Program Files (x86)\ossec-agent\` et **pour les systèmes Windows** il se trouve dans `/var/ossec/etc/`

Ajoutez le code suivant au fichier:

```bash
<syscheck>
  <directories check_all="yes" realtime="yes">C:\PATH</directories>
</syscheck>
```

Redémarrez le wazuh manager

```bash
systemctl restart wazuh-manager
```

Vous pouvez maintenant tester la configuration en téléchargeant un fichier potentiellement malicieux. Vous pouvez essayer le fichier `eicar`, qui est un fichier non vulnérable pour vos sytèmes, mais concu pour être détecté.

```bash
curl -Lo /home/kali/Desktop/suspicious-file.exe https://secure.eicar.org/eicar.com
```

Une alerte est créée, accessible dans la section VirusTotal sur le dashboard.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725632871963/99f0788d-1df3-4b32-ad8b-ee2b544211d3.png align="center")

Il offre un [lien](https://www.virustotal.com/gui/file/275a021bbfb6489e54d471899f7db9d1663fc695ec2fe2a2c4538aabf651fd0f/detection/f-275a021bbfb6489e54d471899f7db9d1663fc695ec2fe2a2c4538aabf651fd0f-1725629780) associé vers l'interface Virus Total pour avoir beaucoup plus d'informations. À chaque fois qu'un nouveau fichier malveillant sera téléchargé, il sera détecté par le module.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725631514182/75b9f1b9-bea4-4170-931c-469a979dec08.png align="center")

Il est aussi possible d'automatiser la suppression de ces fichiers.

L'intégration de Wazuh avec VirusTotal améliore la détection des menaces et renforce la sécurité des sytèmes en fournissant des alertes détaillées et en facilitant une réponse rapide aux fichiers malveillants.

Vous pouvez aussi ajouter des configurations d'alertes FIM ou la détection des attaques par force brute. Un article est disponible sur ce sujet [ici](https://blog.fikara.io/configuration-des-alertes-fim-sur-wazuh).

## Références

La photo de couverture provient de [Aravind Raja](https://www.linkedin.com/in/aravindsraja/) sur LinkedIn