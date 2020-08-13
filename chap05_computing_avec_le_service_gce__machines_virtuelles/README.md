# Machines virtuelles Google Compute Engine

Dans ce chapitre, nous allons

* Installer et configurer l'outil de ligne de commande `Cloud SDK`
* Planifier et configurer les ressources `Compute Engine`
    * avec la console web `Google Cloud Console`
    * avec la ligne de commande disponible dans la console `Google Cloud Shell`
    * avec la ligne de commande disponible sur notre poste client `Google Cloud SDK`

## Création et configuration des machines virtuelle via la Console Web

La console GCP se trouve à l'adresse [https://console.cloud.google.com](https://console.cloud.google.com)

1. Configurations principales d'une machines virtuelles

    * Naviguez vers l'adresse de la console GCP [`Console GCP`](https://console.cloud.google.com)
    * Sélectionnez le projet parent dans lequel vous souhaitez créer la machine virtuelle (Dans la grande barre de menu en haut)

    ![`Selection projet`](./gcp_selection_projet.png)

    * Selection du menu de gestion des instances de machines virtuelles

    ![`Menu instances VM`](./gcp_menu_instances_vm.png)

    * À noter que si aucun compte de facturation n'est associé à votre projet, vous verrez une option vous permettant d'activer la facturation ou vous inscrire à l'essai gratuit avant de pouvoir créer une VM.

   ![`Dialogue activation facturation`](./gcp_dialogue_activation_facturation.png)

    * Une fois un compte de facturation associé, vous avez désormais une boîte de dialogue disposant d'un bouton permettant de créer une VM

    ![`Dialogue création VM`](./gcp_dialogue_creation_instances_vm.png)

    * Saisissez les informations principales de création d'une VM
        * Nom
        * Libellés (représentent des étiquettes permettent de tagguer des VMs afin de les organiser par exemple par environnement, par équipes, ou encore par domaine d'utilisation [Base de données, Serveur d'applications, etc..])
        * Région et Zone

        ![`Region VM`](./gcp_regions_vm.png)

        ![`Zone VM`](./gcp_zone_vm.png)

        * Type de machines (impliquant la puissance de calcul et la mémoire). À noter que les types de machines sont déterminés en fonction de la région et de la zone par GCP. En effet, toutes les zones n'offrent pas forcément les mêmes types de machines.

        ![`Type VM`](./gcp_type_vm.png)

        * Optionnellement l'ajout ou non d'un processeur GPU (par exemple pour les calcul nécessitant de la puissance)
        * La taille, le type et l'image du système du disque de Boot

        ![`OS Disque Demarrage VM`](./gcp_disque_demarrage_vm.png)

        ![`Taille Type Disque Demarrage VM`](./gcp_type_taille_disque_demarrage_vm.png)

        * Le compte de service qui sera utilisé par les application s'exécutant dans la VM lors de l'invocation des services GCP

        ![`Compte de service VM`](./gcp_identite_acces_api_vm.png)

        * Les contraintes d'ouverture de port http(s)

        ![`Ouverture ports Http(s) VM`](./gcp_ouverture_port_http.png)

    * Informations avancées de configurations d'une machine virtuelle

        * Les informations de management
            * Description de la VM
            * Activation de confirmation lors de la suppression
            * Métadonnées (tags) personalisées, associées à la VM et qui seront stockées dans un serveur de méta-données utilisées par l'API Compute Engine. Ces métadonnées permettent par exemple de cibler des groupes de VM sur lesquelles on veut appliquer des opérations (Scripts, etc...)
            * Script de demarrage personalisés qui s'exécuteront lors du (re)demarrage [bash ou python]
            * Règles de disponibilité
                * Activation de la préemption qui implique des coûts très réduits mais une disponibilité non assurée (GCP peut arêter votre BM à tout moment en vous notifiant 30 secondes à l'avance)
                * Redémarrage automatique, en cas d'arrêt non initié par l'utilisateur. Par exemple, si la VM s'arrète à cause d'un évènement de maintenance ou une panne physique
                * Transfert sans interruption lors des maintenances d'infra par GCE

        ![`Management 01 VM`](./gcp_config_avancee_management_vm_01.png)

        ![`Management 02 VM`](./gcp_config_avancee_management_vm_02.png)

        * Les aspects de sécurité
            * Activation de la protection VM, permettant d'utiliser un micrologiciel de confiance UEFI et des options de démarrage sécurisées. Disponibles pour des OS qui le permettent (CentOS, RedHat, Ubuntu, etc...). Les options possibles sont :
                * Démarrage sécurisé, qui permet de s'assurer que uniquement un OS authorisé peut s'exécuter sur cette VM
                * vTPM (Virtual Trusted Platform Module), qui permet de contrôler l'intégrité pendant le démarrage de votre VM, ainsi que de générer et protéger des clés
                * Monitoring de l'intégrité, qui permet de suivre, via StackDriver, l'analyse de l'intégroté vTPM de votre VM
            * Clé SSH publique autorisées
                * GCP supporte le concept de `Project-Wide SSH Key`, permettant l'accès à des clés SSH autorisées au niveau projet. Il est possible de revoquer cet accès en cochant l'option `Block Project-Wide SSH Keys`
                * Il est aussi possible de rajouter des clés SSH qui seront autorisées sur cette VM (indépendemment de la déactivation des clés au niveau projet)

        ![`Cles SSH VM`](./gcp_cles_ssh_vm.png)

        * Les informations de disque
            * Règle de cascade de suppression du disque de demarrage en cas de suppression de la VM
            * Choix de la clé de chiffrement des données des disques
                * Clé gérée par GCP
                * Clés venant du service GCP de stockage de clés du clieng (Cloud Key Management)
                * Clés fournies manuellement par le client. Dans ce cas, Google ne pourra rien pour vous si votre clé est perdue vu qu'il ne la gère pas directement et qu'elle n'est pas référencée dans votre abonnement Cloud Key Management
            * Disque supplémentaires, qui permet de rajouter des disques à votre VM. Vous pouvez soit créer un nouveau disque, soit rattacher votre VM à un ou plusieurs disques existants. Dans la cas de la création d'un nouveau disque, vous allez remplir les informations suivantes
                * Nom du disque
                * Type de disque (SSD, HDD)
                * Taille du disque
                * Image/Snapshot du contenu du disque (au cas où le disque doit être initialisé avec un contenu)
                * Indication sur la cascade de suppression (le disque doit-il être supprimé lors de la suppression de la VM parente)
                * La gestion du chiffrement des données du disque (comme vu pour le disque de démarrage)

        ![`Disque VM`](./gcp_config_avancee_disque_vm.png)

        * Les Tags réseau, nom d'hôte, interface réseau associée à la VM (IP, sous-réseau)

        ![`Interface reseau 01 VM`](./gcp_interface_reseau_01.png)

        ![`Interface reseau 02 VM`](./gcp_interface_reseau_02.png)

        * Location unique (Sole Location), permet de demander la creation de VMs dans des noeuds la locataire unique. Les noeuds à locataire unique permettent d'isoler les VMs de celles des autres clients, Ils représentent en réalité des serveurs physiques dédiés. Il est donc possible de créer des groupes de noeuds à locataire unique et de demander à GCP de créer vos VM dans ces groupes.

            * Le champ `Libellé d'affinité de noeud` représente des contraintes sous la form `CLÉ:OPERATION:VALEUR` permettant d'exprimer le lieu de création. Par exemple :
                * `compute.googleapis.com/node-group-name:IN:my-node-group-name` permet de demander la création de la VM dans le groupe de noeuds `my-node-group-name`
                * `compute.googleapis.com/node-name:IN:my-node-name` permet de demander la création de la VM dans le noeud `my-node-name`

        ![`Location Unique VM`](./gcp_sole_location_vm.png)

        ![`Group VM`](./gcp_sole_tenant_node_group_vm.png)

## Création et configuration des machines virtuelle via le Cloud SDK

1. Installation de Cloud SDK
2. Cloud SDK sur MacOS
3. Exemple d'installation de Cloud SDK sous Linux
4. Création d'une machine virtuelle avec Cloud SDK
5. Création d'une machine virtuelle avec Cloud Shell

## Gestion basique des machines virtuelles

1. Démarrage et arrêt d'instances
2. Accès réseau auc machines virtuelles
3. Monitorer une machine virtuelle
4. Coût d'une machine virtuelle

## Recommandantions pour la plannification, le déploiement et la gestion de VMs

## Résumé

## Quelques questions d'examen

## Revue des questions
