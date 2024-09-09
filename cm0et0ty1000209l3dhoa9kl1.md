---
title: "Déploiement des agents Wazuh"
seoTitle: "Déploiement des Agents Wazuh Guide"
seoDescription: "Guide étape par étape pour déployer et configurer les agents Wazuh sur Linux, Windows et macOS depuis le dashboard Wazuh"
datePublished: Thu Aug 29 2024 04:48:04 GMT+0000 (Coordinated Universal Time)
cuid: cm0et0ty1000209l3dhoa9kl1
slug: deploiement-des-agents-wazuh
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1724906971710/18724497-e5c4-422c-99db-7e91e840a7d7.png
tags: security, cybersecurity-1, wazuh, edr, open-xdr

---

## Pré-requis

* Installation de Wazuh
    

Il existe plusieurs moyens de déployer l'agent Wazuh sur les terminaux. Après avoir installé correctement le wazuh-dashboard, le wazuh-manager et le wazuh-indexer, les agents peuvent maintenant être installés depuis le dashboard. Suivez le lien [ici](https://bilaldotcom.hashnode.dev/wazuh-installation) pour l'installation de Wazuh.

Une fois connecté au dashboard, appuyer sur "Add agent" pour ajouter un nouvel agent.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724899435248/65ba7688-df35-4fbc-a5d9-02379ffb29a2.png align="center")

Il existe trois types de systèmes supportés par Wazuh : Linux, Windows et macOS. Notez que pour installer Wazuh, le système Linux doit être une version 64 bits, conformément à la documentation [Wazuh](https://documentation.wazuh.com/current/installation-guide/packages-list.html).

Choisir le type de système.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724899563765/00ee0e7b-4ce1-4ebb-b11e-f772929accb8.png align="center")

Entrer l'adresse IP du serveur

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724906219179/09daecaf-4f3e-4ad3-a0d7-f9e83b7c863f.png align="center")

Copier, coller et ensuite exécuter le script généré dans l'invite de commandes en tant qu'administrateur.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724907171380/d9d50ae8-a16b-4086-97b2-a06bfbee5d82.png align="center")

Démarrer ensuite l'agent avec la commande :

`NET START` WazuhSvc si votre système est un Windows ou ,

`sudo systemctl daemon-reload sudo systemctl enable wazuh-agent sudo systemctl start wazuh-agent` si il s'agit d'un système Linux.

Voici un apercu du tableau de bord avec des agents installés.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724907276643/3b76e2dd-af6c-49fe-a974-386f5618bef8.png align="center")

Avec quelques agents installés, vous pouvez désormais surveiller les événements de sécurité sur vos terminaux.

Wazuh peut réagir face à des situations comme le blocage d’un acteur malveillant connu, la surveillance de l’intégrité des fichiers, la détection de répétées de connexion échouées, l’analyse des comportements anormaux sur le réseau, et la réponse automatisée aux incidents de sécurité.

La prochaine étape est la mise en place des règles et des scripts pour détecter les alertes et incidents avec Wazuh. Un article concernant la configurations des alertes est disponible [ici](https://blog.fikara.io/configuration-des-alertes-fim-sur-wazuh).