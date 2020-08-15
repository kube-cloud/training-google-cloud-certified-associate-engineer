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

GCP offre 3 possibilité permettant d'interagir avec les ressources :

* Ligne de commande
* Interface RestFull
* Cloud Shell

Avant de pouvoir utiliser la ligne de commande ou l'interface de service Restfull, il faudra tout d'abord installer `Cloud SDK`, qui apporte un ensemble de bibliothèques et d'outils nécessaire aux interaction avec GCP.

1. Installation de Cloud SDK

    * La documentation liée à `Cloud SDK` peut être trouvée à l'adresse suivante : [`https://cloud.google.com/sdk/docs/`](https://cloud.google.com/sdk/docs/)
    * `Cloud SDK` peut être installé à partir d'archives versionnées et téléchargeables et spécifiques au système d'exploitation
        * Pour linux x64 par exemple : [`https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-302.0.0-linux-x86_64.tar.gz`](https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-302.0.0-linux-x86_64.tar.gz)
        * Pour OSX x64 : [`https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-302.0.0-darwin-x86_64.tar.gz`](https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-302.0.0-darwin-x86_64.tar.gz)
    * Une fois l'archive obtenue, il suffit de la décompresser dans un répertoire de votre système, de préférence le répertoire HOME de votre utilisateur.
    * Si vous souhaitez rajouter les commandes et outils du `Cloud SDK` dans votre PATH, il faudra donc exécuter le script `<CLOUD_SDK_HOME>/google-cloud-sdk/install.sh`
        * le script `install.sh` vous permet de décider si vous souhaitez communiquer vos données d'utilisation pour contribuer à l'amélioration du service. Cette fonctionnalité peut être activée ou désactivée grace à la commande : `gcloud config set disable_usage_reporting true|false`
        * Le script `install.sh` va aussi détecter les composants du SDK qui sont manquants et vous les lister dans un tableau avec les version compatible au SDK ainsi que les ID de composants permettant leur installation ou suppression via la commande : `gcloud components install|remove COMPONENT_ID`
        * La liste des composants `Cloud SDK` peut être obtenue grâce à la commande `gcloud components list`, une fois que vous avez installé le SDK
        * La mise à jour des composants du SDK se fait via la commande : `gcloud components update`
        * Le script vous demandera ensuite si vous souhaitez modifier votre PATH afin d'y intégrer les outils du SDK. Si oui, vous devrez donner le chemin du fichier de déclaration des variables d'environnement (le script en proposera un par defaut).
        * Dans le cas de OSX, voici les lignes rajoutée :

            ```
            # The next line updates PATH for the Google Cloud SDK.
            if [ -f '<SDK_HOME>/path.bash.inc' ]; then . '<SDK_HOME>/path.bash.inc'; fi

            # The next line enables shell command completion for gcloud.
            if [ -f '<SDK_HOME>/completion.bash.inc' ]; then . '<SDK_HOME>/completion.bash.inc'; fi
            ```
        * Une fois exécuté, vous devez relancer le terminal et vérifier l'installation via la commande `gcloud version`
    * `Cloud SDK` peut aussi être installée via un gestionnaire de paquet, pour Ubuntu et Debian. La documentation officielle détaille trè bien ces cas.
    * Une fois installée, `Cloud SDK` vous donne accès entre aters à un outils nommé `GCLOUD` qui est un utilitaire en ligne de commande permettant d'interacgir avec un ensemble de rservices et ressources GCP
        * `Compute Engine`
        * `Kubernetes Engine`
        * `Cloud SQL`
        * `Cloud Dataproc`
        * `Cloud DNS`
        * `Cloud Deployment`
        * `Déploiement AppEngine`
        * `Authentification`
        * etc...
    * Les commandes `GCLOUD` sont organisées en groupes qui eux-même peuvent avoir des sous-groupes, en foncion des opération offertes par chacunes des ressources qu'il permet de manipuler. Par exemple:
        * le groupe de commandes `gcloud compute` permet de gérer toutes les opérations liées à `Compute Engine`
            * `images`, pour la gestion des images de VMs
            * `instances`, pour la gestion des instances de VMs
            * `addresses`, pour la gestion des adresses IP de VMs
            * `disks`, pour la gestion des disques persistent pour VMs
            * `disk-types`, pour la gestion des types de disques
            * `commitments`, pour la gestion des engagements longue durée (engagement, 1 an, 3 ans, etc...)
            * `instance-groups`, pour la gestion des groupes d'instances
            * `instance-templates`, pour la gestion des modèles de VMs (Modèle de configuration de VM qui peut être utilisé pour la création automatique de VMs dans le cadre de groupe de VMs ou de Cluster de VMs)
            * `machine-types`, pour la gestion des types de VVMs
            * `networks`, pour la gestion des réseaux pour les VMs
            * `zones`, pour la gestion des zones
            * `snapshots`, pour la gestion des snapshots de VMs
            * `sole-tenancy` (Noeud à locataire unique)
            * La liste exhaustive à l'adresse [`https://cloud.google.com/sdk/gcloud/reference/compute#GROUP`](https://cloud.google.com/sdk/gcloud/reference/compute#GROUP)
        * le groupe de commandes `gcloud container` permet de gérer toutes les opérations liées à `Kubernetes Engine`
        * le groupe de commandes `gcloud datastore` permet de gérer toutes les opérations liées à `Cloud Datastore`
        * le groupe de commandes `gcloud dataproc` permet de gérer toutes les opérations liées à `Cloud Dataproc` (Cluster et Jobs)
        * le groupe de commandes `gcloud datastore` permet de gérer toutes les opérations liées à `Cloud Datastore`
        * le groupe de commandes `gcloud filestore` permet de gérer toutes les opérations liées à `Cloud Filestore`
        * le groupe de commandes `gcloud firebase` permet de gérer toutes les opérations liées à `Cloud Firebase`
        * le groupe de commandes `gcloud firebase` permet de gérer toutes les opérations liées à `Cloud Firebase`
        * le groupe de commandes `gcloud bigtable` permet de gérer toutes les opérations liées à `Cloud Bigtable`
        * le groupe de commandes `gcloud function` permet de gérer toutes les opérations liées à `Cloud Functions`
        * le groupe de commandes `gcloud app` permet de gérer toutes les opérations liées à `Cloud AppEngine`
        * le groupe de commandes `gcloud iam` permet de gérer toutes les opérations liées à `Cloud IAM`
        * le groupe de commandes `gcloud organizations` permet de gérer toutes les opérations liées à `Cloud Organizations`
        * La liste exhaustive à l'adresse [`https://cloud.google.com/sdk/gcloud/reference#GROUP`](https://cloud.google.com/sdk/gcloud/reference#GROUP)
    * Une commande `gcloud` commence généralement par le `groupe (listé précédemment)`
    * La forme générale est la suivante:
    
        ```
        gcloud GROUP | COMMAND [--account=ACCOUNT] [--billing-project=BILLING_PROJECT] [--configuration=CONFIGURATION] [--flags-file=YAML_FILE] [--flatten=[KEY,…]] [--format=FORMAT] [--help] [--project=PROJECT_ID] [--quiet, -q] [--verbosity=VERBOSITY; default="warning"] [--version, -v] [-h] [--impersonate-service-account=SERVICE_ACCOUNT_EMAIL] [--log-http] [--trace-token=TRACE_TOKEN] [--no-user-output-enabled]
        ```
    * Parmis les options les plus utilisées nous avons :
        * `--boot-disk-type` qui permet de spécifier le type de disque de démarrage souhaité. La liste des types de disques peut être obtenue via la commande `gcloud compute disk-types list`
        * `--boot-disk-size` qui permet de fixer la taille du disque de démarrage. Chaque type de disque définit la plage de tailles autorisées. On peut la consulter lors du listage des types de disques
        * `--machine-type` qui permet de définir le type de machine pour notre VM. La liste des types de machines peut être obtenue grâce à la commande `gcloud compute machine-types list`
        * `--preemptible` qui permet (si présent) de spécifier que la VM est préemptible
        * `--labels` qui permet de rajouter des labels aux VMs
    
    * Avant toute utilisation de `GCLOUD`, il faut générer une configuration :
        * `gcloud init` permet d'initialiser une configuration permettant de se connecter à un projet, qui va de manière interactive vous accompagner dans la configuration d'un contexte GCP pour votre lgne de commandes
        * Il est aussi possible de le faire de zero :
            * Se connecter : `gcloud auth login` : Ouvrira le navigateur pour vous authentifier
            * [Optionnel] Créer in projet : `gcloud projects create <PROJECT_ID>`
            * Sélectionner le projet courant : `gcloud config set project PROJECT_ID`
        * On peut aussi initialiser des valeurs par défaut qui seront utilisées dans des commandes `gcloud` au cas où on ne les précise pas
            * `gcloud config set [SECTION/]PROPERTY VALUE`
            * Les valeurs possible de `[SECTION/]PROPERTY`
                * `core/project` : Projet par défaut à utiliser lorsque non spécifié dans la commande
                * `core/account` : Compte utilisateur à utiliser par défaut (On peut les lister avec `gcloud auth list`)
                * `compute/region` : La région par défaut  pour les commandes GCE
                * `compute/zone` : La zone par défaut pour les commandes GCE
                * `container/cluster` : Le cluster par défaut à utiliser pour les commades GKE
                * `functions/region` : region par défaut à utiliser pour les commades Cloud Functions

2. Création d'une machine virtuelle avec Cloud SDK

    * Exemple de création : `gcloud compute instances create demo-instance-01 demo-instance-02 --machine-type n1-standard-16 --preemptible --boot-disk-size 10G --boot-disk-type pd-ssd --zone us-central1-a`
    * Listage des VM : `gcloud compute instances list`
    * Exemple de suppression : `gcloud compute instances delete demo-instance-01 demo-instance-02`

3. Création d'une machine virtuelle avec Cloud Shell

    * `Cloud Shell` est un service en ligne de commande disponible via le portail GCP sur le navigateur web.
    * `Cloud Shell` permet d'exécuter des commandes `gcloud` sans avoir à installer `Cloud SDK` sur le poste client.
    * Pour utiliser `Cloud Shell` il suffit de le lancer via la console web GCP sur l'icone en forme de ligne de commande.

    ![`Cloud Shell Icon`](./gcp_cloud_shell_icon.png)

    ![`Cloud Shell`](./gcp_cloud_shell.png)
    * Une fois `Cloud Shell` démarrée, vous pouvez vous servir des mêmes commandes `gcloud` que vous auriez utilisé avec une installation `Cloud SDK` sur votre poste.

## Gestion basique des machines virtuelles

Une fois qu'une machine virtuelle est créée, vous pouvez effectuer sur elle certaines opérations, via `Cloud Console (interface web GCP)` ou en ligne de commande via `Cloud SDK (gcloud)` ou encore `Cloud Shell`. Les plus basiques des commandes sont les suivantes :

0. Opérations via la console web

    * Démarrage d'une instance de VM ou reprise d'une instance suspendue
    * Arrêt d'une instance
    * Suspension d'une instance
    * Réinitialisation d'une instance
    * Suppression d'une instance
    * Création d'une nouvelle image de VM à partir de cette instance
    * Affichage des détails de configuration réseau de la VM
    * Affichage du journal d'opérations de l'instance

    ![`VM Cloud Console Operations`](./gcp_vm_operations_console_web.png)

1. Démarrage, arrêt, réinitialisation d'instances

`gcloud compute instances <start|stop|reset> <INSTANCE_NAME>`

2. Accès réseau aux machines virtuelles

    * Il est possible d'accéder aux VM que vous avez créer via le protocole SSH
    * GCP offre plusieur options d'accès SSH à vos machines virtuelles

        ![`VM SSH Options`](./gcp_vm_connexion_ssh_options.png)

    * On peut ouvrir une console SSH directement disponible via la console web GCP
    * On peut aussi se connecter en SSH depuis un client SSH externe sur notre poste en passant par la commande 
        * `gcloud beta compute ssh <INSTANCE_NAME> [--project "<PROJECT_NAME>"] [--zone "<ZONE>"]` de `gloud`.
        * Cette commande étant encore en version Beta, va vous demander de confirmer l'installation du groupe de commande BETA
    * On peut enfin se connecter en SSH depuis n'importe qclient SSH sans passer par `gcloud`. Pour cela, il faudra enregistrer sa clé publique sur la VM ou l'autoriser sur le projet et ensuite utiliser la commande SS classique afin de se connecter via l'adresse IP externe de la VM. À noter que le port SSH de la VM est ouvert par défaut vers l'extérieur.
        * `ssh [-i <PATH_TO_PRIVATE_KEY>] key_username@vm_external_ip`

3. Monitorer une machine virtuelle

    * Lors de l'exécution de votre VM, vous avez la possibilité de monitorer la consomation CPU, Disque, réseau, etc.
    * Pour le faire,vous devez :
        * Cliquez sur le lien portant le nom de la VM dont vous souhaitez visualiser les informations de monitoring, afin d'entrer dans la page de détails de cette instance
        * Une fois dans la page de détails, vous allez sur l'onglet `Monitoring (ou surveillance)`
        * Vous pourrez visualiser un ensemble de graphique présentant
            * La consommation processeur
            * Le traffic réseau (octets, paquets, etc...)
            * Les E/S disque (en octets et opétations)

4. Coût d'une machine virtuelle

    * Une grande partie de la gestion basique des VMs est le tracking des coûts.
    * Les VMs sont facturées par tranches de secondes, avec un minimum d'une minute. Ce qui veut dire que si vous avez utilisé une VM 30 secondes, vous serez facturé 1 minute.
    * Une VM est facturé sur toute la durée de son activité
    * Le temps d'activité d'une VM est le temps d'écoulant entre son démarrage et son arrêt (RUNNING -> TERMINATED)
    * Le coût d'une VM dépend du type de machine (nombre de coeurs, mémoire, disque, etc...)
    * Google offre une réduction sur utilisation prolongée pour des VM standard hors engagement (engagement d'utilisation annuelle ou trimestrielle)
    * Google offre des réductions pouvant aller à 57% si vous prenez un engagement d'utilisation sur 1 an ou 3 ans
    * Les VM préemptive sont facturée 80% moins cher que les VM normales, mais ne garantissent aucune SLA
    * GCP vous permet d'exporter vos facture de manière automatique sur une instance `BigQuery` ou sous forme de fichier `Cloud Datastore`. Vous pouvez donc par exemple en ce moment positionner uns fonction `Cloud Functions` qui pourra réagir à l'arrivée d'une nouvelle facture et l'intégrer à votre outil de comptabilité

    ![`VM Facturation Export`](./gcp_vm_facturation_export.png)

## Recommandantions pour la plannification, le déploiement et la gestion de VMs

1. Pensez à choisir pour vos VM le type de machine ayant le plus faible excédent de ressources par rapport à votre besoin.
2. Utilisez la console web pour les tâches ponctuelle, mais les scripts exploitant `gcloud` pour les tâches répétitives
3. Utilisez des scripts de démarrage (Startup Scripts) lors de la création de vos VMs pour effectuer toutes les tâches d'initialisation dont vous avez besoin
4. Pensez à créer une image personalisée lorsque vous apportez des modifications à l'image de base de votre VMs pour pouvoir la réutiliser plus tard sur d'autres VMs
5. Si certains de vos services supporte des interruptions non plannifiées (Ci/Cd, etc...), privilégiez des VMs préemptives pour les déployer
6. Utilisez SSH/GCLOUD SSH/RDP/ pour accéder à vos VMs pour efectuer des tâches système.
7. Utilisez Cloud Console, Cloud SDK ou Cloud Shell pour des tâches niveau VM (non pas des tâches OS)

## Résumé

## Quelques questions d'examen

## Revue des questions
