# Overview de la formation

## Introduction

Le but de cette formation est de préparer les apprenants à la certification [`Google Cloud Engineer Certified Associate`](https://cloud.google.com/certification/guides/cloud-engineer/).

En effet, ce type de certifié est capable d'identifier et comprendre l'ensemble des services fournis par [`Google Cloud Platform (GCP)`](https://cloud.google.com/) et est capable, via les outils `Cloud Console`, `Cloud Shell` ou encore `Cloud SDK`, de créer, déployer et administrer, pour son compte personnel ou pour celui d'une  organisation, des applications, solutions ou infrastructures sur la plateforme [`Google Cloud Platform (GCP)`](https://cloud.google.com/).

L'examen de certification aura pour objectif de tester les connaissances de l'apprenant concernant :

* La planification de solution en se basant sur un ou plusieurs services GCP
* La création d'environnements cloud pour une organisation
* Le déploiement d'applications et d'infrastructures
* L'exploitation du monitoring et de la journalisation afin d'assurer la disponibilités des solutions mises en place
* La mise en place de la gestion d'identité, la gestion des accès et autres mesures de sécurité

Les objectifs détaiilés des attentes d'un certifié `Google Cloud Engineer Associate` sont disponible à l'adresse suivante : [`Gooogle Cloud Engineer Certified Goals`](https://cloud.google.com/certification/guides/cloud-engineer/)

## Objectifs de la certification [`Google Cloud Engineer Associate`](https://cloud.google.com/certification/guides/cloud-engineer/)

1. Configurer un environnement de solution cloud

    * Configurer des projets cloud et des comptes. Les tâches suivantes sont incluses :

        * Créer des projets
        * Attribuer des utilisateurs à des rôles IAM prédéfinis au sein d'un projet
        * Gérer des utilisateurs dans Cloud Identity (de façon manuelle et automatisée)
        * Activer des API au sein des projets
        * Provisionner un ou plusieurs espaces de travail Stackdriver

    * Gérer la configuration de la facturation. Les tâches suivantes sont incluses :

        * Créer un ou plusieurs comptes de facturation
        * Associer des projets à un compte de facturation
        * Établir des budgets de facturation et des alertes
        * Configurer des exportations de la facturation pour estimer les frais quotidiens/mensuels

    * Installer et configurer l'interface de ligne de commande (CLI), le SDK Cloud

2. Planifier et configurer une solution cloud

    * Planifier et estimer l'utilisation d'un produit GCP à l'aide du simulateur de coût

    * Planifier et configurer les ressources de calcul. Points à prendre en compte :

        * Choix de la solution de calcul appropriée pour une charge de travail donnée (ex. : Compute Engine, Google Kubernetes Engine, App Engine, Cloud Run, Cloud Functions)
        * Utilisation de VM préemptives et de types de machines personnalisés, selon les cas

    * Planifier et configurer les options de stockage de données. Points à prendre en compte :

        * Choix du produit (par exemple, Cloud SQL, BigQuery, Cloud Spanner, Cloud Bigtable)
        * Choix des options de stockage (ex. : Standard, Nearline, Coldline, Archive)

    * Planifier et configurer des ressources réseau. Les tâches suivantes sont incluses :

        * Faire la différence entre les différentes options d'équilibrage de charge
        * Identifier les emplacements des ressources dans un réseau pour la disponibilité
        * Configurer Cloud DNS

3. Déployer et mettre en œuvre une solution cloud

    * Déployer et mettre en œuvre des ressources Compute Engine. Les tâches suivantes sont incluses :

        * Lancer une instance de calcul à l'aide de Cloud Console et du SDK Cloud (gcloud) (ex. : attribution de disques, règles de disponibilité, clés SSH)
        * Créer un groupe d'instances géré avec autoscaling à l'aide d'un modèle d'instance
        * Générer/importer une clé SSH personnalisée pour les instances
        * Configurer une VM pour Stackdriver Monitoring et Stackdriver Logging
        * Évaluer les quotas de calcul et demander des augmentations
        * Installer l'agent Stackdriver pour la surveillance et la journalisation

    * Déployer et mettre en œuvre des ressources Google Kubernetes Engine. Les tâches suivantes sont incluses :

        * Déployer un cluster Google Kubernetes Engine
        * Déployer une application de conteneur sur Google Kubernetes Engine à l'aide de pods
        * Configurer la surveillance et la journalisation des applications dans Google Kubernetes Engine

    * Déployer et mettre en œuvre des ressources App Engine, Cloud Run et Cloud Functions. Les tâches suivantes sont incluses, le cas échéant :

        * Déployer une application, et mettre à jour la configuration du scaling, les versions et la répartition du trafic
        * Déployer une application qui reçoit les événements Google Cloud (ex. : événements Cloud Pub/Sub, événements de notification de modification des objets Cloud Storage)

    * Déployer et mettre en œuvre des solutions de données. Les tâches suivantes sont incluses :

        * Initialiser des systèmes de données avec des produits (ex. : Cloud SQL, Cloud Datastore, BigQuery, Cloud Spanner, Cloud Pub/Sub, Cloud Bigtable, Cloud Dataproc, Cloud Dataflow, Cloud Storage)
        * Charger des données (ex. : importer des lignes de commande, transférer des API, importer/exporter, charger des données cloud à partir de Cloud Storage, diffuser des données dans Cloud Pub/Sub)

    * Déployer et mettre en œuvre des ressources réseau. Les tâches suivantes sont incluses :

        * Créer un VPC avec des sous-réseaux (ex. : un VPC en mode personnalisé, un VPC partagé)
        * Lancer une instance Compute Engine à l'aide d'une configuration réseau personnalisée (ex. : adresse IP interne uniquement, accès privé Google, adresse IP privée, externe et statique, tags réseau)
        * Créer des règles de pare-feu d'entrée et de sortie pour un VPC (ex. : sous-réseaux IP, tags, comptes de service)
        * Créer un VPN entre un VPC Google et un réseau externe à l'aide de Cloud VPN
        * Créer un équilibreur de charge pour distribuer le trafic réseau d'application à une application (ex. : équilibreur de charge HTTP(S) global, équilibreur de charge proxy SSL global, équilibreur de charge proxy TCP global, équilibreur de charge de réseau régional, équilibreur de charge interne régional)

    * Déployer une solution à l'aide de Cloud Marketplace. Les tâches suivantes sont incluses :

        * Parcourir le catalogue de Cloud Marketplace pour consulter les détails de la solution
        * Déployer une solution Cloud Marketplace

    * Déployer une infrastructure d'application à l'aide de Cloud Deployment Manager. Les tâches suivantes sont incluses :

        * Développer des modèles Deployment Manager
        * Lancer un modèle Deployment Manager

4. Garantir le bon fonctionnement d'une solution cloud

    * Gérer les ressources Compute Engine. Les tâches suivantes sont incluses :

        * Gérer une instance de VM unique (ex. : démarrer, arrêter, modifier la configuration ou supprimer une instance)
        * Établir une connexion SSH/RDP à l'instance
        * Associer un GPU à une nouvelle instance et installer des bibliothèques CUDA
        * Afficher l'inventaire des VM en cours d'exécution (ID des instances, détails)
        * Travailler avec les instantanés (ex. : créer un instantané à partir d'une VM, afficher les instantanés, supprimer un instantané)
        * Travailler avec les images (ex. : créer une image à partir d'une VM ou d'un instantané, afficher les images, supprimer une image)
        * Travailler avec les groupes d'instances (ex. : définir les paramètres d'autoscaling, attribuer un modèle d'instance, créer un modèle d'instance, supprimer un groupe d'instances)
        * Travailler avec les interfaces de gestion (ex. : Cloud Console, Cloud Shell, SDK gcloud)

    * Gérer les ressources Google Kubernetes Engine. Les tâches suivantes sont incluses :

        * Afficher l'inventaire des clusters en cours d'exécution (nœuds, pods, services)
        * Parcourir le dépôt d'images de conteneurs et afficher les détails des images de conteneurs
        * Travailler avec des pools de nœuds (ex. : ajouter, modifier ou supprimer un pool de nœuds)
        * Travailler avec les pods (ex. : ajouter, modifier ou supprimer des pods)
        * Travailler avec les services (ex. : ajouter, modifier ou supprimer un service)
        * Travailler avec des applications avec état (ex. : volumes persistants, ensembles avec état)
        * Travailler avec les interfaces de gestion (ex. : Cloud Console, Cloud Shell, SDK Cloud)

    * Gérer les ressources App Engine et Cloud Run. Les tâches suivantes sont incluses :

        * Ajuster les paramètres de répartition du trafic des applications
        * Définir les paramètres de scaling pour l'autoscaling des instances
        * Travailler avec les interfaces de gestion (ex. : Cloud Console, Cloud Shell, SDK Cloud)

    * Gérer les solutions de stockage et de base de données. Les tâches suivantes sont incluses :

        * Déplacer des objets entre des buckets Cloud Storage
        * Convertir les classes de stockage des buckets Cloud Storage
        * Définir les règles de gestion du cycle de vie des objets pour les buckets Cloud Storage
        * Exécuter des requêtes pour récupérer des données à partir des instances de données (ex. : Cloud SQL, BigQuery, Cloud Spanner, Cloud Datastore, Cloud Bigtable)
        * Estimer les coûts d'une requête BigQuery
        * Sauvegarder et restaurer les instances de données (ex. : Cloud SQL, Cloud Datastore)
        * Consulter l'état de la tâche dans Cloud Dataproc, Cloud Dataflow ou BigQuery
        * Travailler avec les interfaces de gestion (ex. : Cloud Console, Cloud Shell, SDK Cloud)

    * Gérer les ressources réseau. Les tâches suivantes sont incluses :

        * Ajouter un sous-réseau à un VPC existant
        * Développer un sous-réseau pour obtenir plus d'adresses IP
        * Réserver des adresses IP statiques externes ou internes
        * Travailler avec les interfaces de gestion (ex. : Cloud Console, Cloud Shell, SDK Cloud)

    * Gérer la surveillance et la journalisation. Les tâches suivantes sont incluses :

        * Créer des alertes Stackdriver basées sur les métriques liées aux ressources
        * Créer des métriques personnalisées Stackdriver
        * Configurer des récepteurs de journaux pour exporter ces ressources vers les systèmes externes (ex. : sur site ou BigQuery)
        * Afficher et filtrer les journaux dans Stackdriver
        * Afficher les détails d'un message de journal spécifique dans Stackdriver
        * Utiliser les diagnostics cloud pour identifier le problème d'une application (ex. : afficher les données Cloud Trace, ou utiliser le débogage Cloud pour afficher un moment précis de l'application)
        * Afficher l'état de Google Cloud Platform
        * Travailler avec les interfaces de gestion (ex. : Cloud Console, Cloud Shell, SDK Cloud)

5. Configurer les accès et la sécurité

    * Utiliser la gestion de l'authentification et des accès (IAM). Les tâches suivantes sont incluses :

        * Afficher les attributions de rôles IAM
        * Attribuer des rôles IAM à des comptes ou à des Google Groupes
        * Définir des rôles IAM personnalisés

    * Gérer les comptes de service. Les tâches suivantes sont incluses :

        * Gérer les comptes de service avec des privilèges limités
        * Attribuer un compte de service à des instances de VM
        * Octroyer l'accès à un compte de service dans un autre projet

    * Afficher les journaux d'audit pour les services de projet et gérés