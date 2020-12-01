# Networking : Virtual Private Clouds et Virtual Private Networks

L'objectif de ce chqpitre est de vous qpprendre `créer des résequx VPC avec des sous-réseaux par défaut ou personalisés et ensuite de définir des politiaues de pare feu et créer des réseaux d'interconnexion VPN.

## Création d'un cloud privé virtuel via les sous-réseaux (Subnets)

* Le service VPC est une version logicielle d'un réseau physique permettant de relier des ressources d'un projet.
* Lorsque vous créer un projet, VPC va automatiquement créer un réseau VPC pour ce projet là. Vous pourrez par la suite créer des réseaux VPC additionnels associés à ce projet.
* Un réseau VPC est une ressource globale qui n'est rattachée à aucune résgion ou zone.
* Un réseau VPC contient des sous-réseaux, aui sont des ressources régionales (un sous-réseau permet de faire communiquer les ressources d'une région donnée)
* Un sous réseau est associée à une plage d'adresses IP et fourni une plage d'adresses privées
* Les ressources (telles que des VM, des noeuds GKE, etc...) utilisent les adresses IP du sous réseau pour communiquer entre elles.
* En plus des réseaux VPC associés à un projet, vous pouvez créer des réseaux VPC partagés pour une organisation.
* Un réseau VPC partagé est hébergé dans un projet commun et tout utilisateur d'un autre projet, disposant des droits adéquats pourra créer des ressources et les associer à ce réseau partagé.

1. Création d'un cloud privé virtuel via Cloud Concole

    * Allez dans le menu VPC

    ![`Cloud VPC : Menu`](./cloud-vpc-menu.png)

    * Dans l'interface principale, cliquez sur le bouton de création d'un nouveau réseau VPC
    * Dans le formulaire renseignez le nom du réseau, ainsi que sa description

    ![`Cloud VPC : Form 01`](./cloud-vpc-form-01.png)

    * Lorsque vous créez un nouveau réseau VPC, vous devez lui associez un ou plusieurs sous-réseau. Pour le faire
        * vous pouvez choisir l'option `Automatique` dans la section `Sous Réseaux`. Cette option permet à `GCP` de générer pour nous un sous-réseau par région. Dans ce cas, vous pourrez aussi choisir les règles de pare feu parñis celle qui vous seront automatiquement proposées.

        ![`Cloud VPC : Form 02 Subnets Auto`](./cloud-vpc-form-02-subnets-auto.png)

        * Vous pouvez aussi choisir l'option `Personalisée`, qui vous permettra de décider de comment vous souhaitez créer les sous-réseau de votre VPC. Vous devrez donc créer manuellement l'ensemble des sous-réseaux que vous souhaitez rattacher à votre réseau VPC. Dans ce cas, vous devrez vous rendre dans la zone `Pare feu` afin de définir les règles de pare feu de applicables à vos sous réseau personalisés. Par défaut aucun accès autorisé.

        ![`Cloud VPC : Form 02 Subnets Manuel`](./cloud-vpc-form-02-subnets-manuel.png)

        Notez que ans la configuration manuelle des sous réseaux d'un VPC, vous avez la possibilité de rajouter à un sous réseau une plage d'adresse IP secondaire (ou d'alias).

        En effet, cette plage permet d'attribuer des places d'adresse IP secondaire à une ressource VPC (VM GCP). Ces plages d'adresses IP sera principalement utilisée dans le cadre de l'affectation d'adresse IP pour les conteneurs tournant sur les VMs.

        En attribuant aux coneneurs une plage d'adresse IP distincte de celle de la VM principale, nous avons la possibilité de router le traffic des conteneurs indépendament de celui de la VM principale. Pour plus d'informations sur l'utilisation des adresses secondaires : [`Cloud VPC : Subnet secondaire`](https://cloud.google.com/vpc/docs/alias-ip?hl=fr)

        Vous avez aussi la possibilité de spécifier les accès privé ou non aux services GCP. En effet, cette option vous permettra de décider si les VM de votre sous réseau pourront accéder aux services GCP sans avoir besoin qu'une adresse IP publique soit affectée à la VM

    * Vous avez enfin la possibilité de configurer le mode de rourage dynamique du traffic de votre réseau VPC.
        * L'option `Mondiale` vous permettra d'annoncer vos sous-réseaux toutes régions confondues sur votre routeur sur site (dans e cadre d'un VPN site-to-site). Avec cette option, vous n'avez besoin que d'un VPN avec un routeur cloud pour apprendre dynamiquement les routes vers et depuis les régions GCP.
        * L'option `Regionale` va restreindre l'apprentissage des routes uniquement à celle de la région.

2. Création d'un cloud privé virtuel via GCloud

    Vous pouvez aussi créer des réseaux VPC via la ligne de commande grâce à la commande :

    ```
    gcloud compute networks create NAME
        [--bgp-routing-mode=MODE; default="regional"]
        [--description=DESCRIPTION] [--mtu=MTU] [--range=RANGE]
        [--subnet-mode=MODE] [GCLOUD_WIDE_FLAG ...]
    ```
    * `NAME` représente le nom du réseau VPC
    * `--bgp-routing-mode=MODE` permet de spécifier le mode de routage BGP (`Régional` ou `Mondial`) avec comme valeur par défaut `Regional`
    * `--mtu=MTU` permet de spécifier la taille maximale des packets IP pouvant circuler sur ce réseau
    * `--range=RANGE` permet de spécifier la plage d'adresse IPV4 à attribuer aux ressources de ce réseau. Elle est spécifier sous forme de CIDR (`Classless Inter-Domain Routing`)
    * `--subnet-mode=MODE` permet de préciser le mode de création des sous réseaux de ce VPC.
        * `auto` pour la génération automatique d'un sous réseau par région (recommandée)
        * `custom` pour la génération manuelle des sous réseaux

    `Exemple : gcloud compute networks create demo-vpc-02 --bgp-routing-mode=global --subnet-mode=custom`

    * Dans le cas de la création manuelle d'un réseau VPC avec l'option de sous réseau `custom`, il faudra alors créer manuellement des sous réseau pour ce réseau VPC. via la commande :

    ```
    gcloud compute networks subnets create NAME --network=NETWORK --range=RANGE
        [--description=DESCRIPTION] [--enable-flow-logs]
        [--enable-private-ip-google-access]
        [--logging-aggregation-interval=LOGGING_AGGREGATION_INTERVAL]
        [--logging-filter-expr=LOGGING_FILTER_EXPR]
        [--logging-flow-sampling=LOGGING_FLOW_SAMPLING]
        [--logging-metadata=LOGGING_METADATA]
        [--logging-metadata-fields=[METADATA_FIELD,...]]
        [--private-ipv6-google-access-type=PRIVATE_IPV6_GOOGLE_ACCESS_TYPE]
        [--purpose=PURPOSE] [--region=REGION] [--role=ROLE]
        [--secondary-range=PROPERTY=VALUE,[...]] [GCLOUD_WIDE_FLAG ...]
    ```
    * `NAME` représente le nom du sous réseau
    * `--network=NETWORK` permet de précisier l'identifiant (nom) du réseau VPC parent
    * `--range=RANGE`, Permet de préciser la plage d'adresse principale du sous réseau
    * `--enable-private-ip-google-access`, permet d'activer l'accès privé aux services GCP
    * `--region=REGION` permet de préciser la région associée au sous réseau. Si vous n'en précisez pas une dans la commande, celle par défaut de votre `Cloud SDK` sera choisie (`gcloud config set region REGION`)
    * `--purpose=PURPOSE` permet de spécifier l'objectif du sous réseau
        * `INTERNAL_HTTPS_LOAD_BALANCER`, Sous réseau réservé à la répartition de charges
        * `PRIVATE` réseau crée par un utilisateur régulier ou automatique
    * `--role=ROLE` permet de spécifier le rôle du sous-réseau (requis uniquement lorsque `--purpose=INTERNAL_HTTPS_LOAD_BALANCER`)
        * `ACTIVE` permet d'identifier le sous réseau comme celui en cours d'utilisation pour le Load Balancing HTTP(s)
        * `BACKUP` permet d'identifier le sous réseau comme dormant, en attendant son élection en ACTIF en cas de défaillance du sous réseau ACTIF courant.
    * `--secondary-range=PROPERTY=VALUE,[...]` permet de rajouter des plages d'adresses privées secondaire au sous réseaux. Utilisées principalement pour affecter des sous-réseau d'adresses auc conteneurs qui tourneront sur les noeuds de ce sous réseau. `--secondary-range range1=192.168.64.0/24 range1=192.168.78.0/24`

    `Exemple : gcloud beta compute networks subnets create demo-vpc-02-subnet1 --network=demo-vpc-02 --region=us-west2 --range=10.20.0.0/16 --enable-private-ip-google-access --enable-flow-logs`

3. Création d'un cloud privé virtuel partagé via GCloud

    * Cette fonctionnalité n'est disponible que pour des comptes GCP de type `Organisation`
    * Avant de pouvoir créer des réseaux VPC partagés, il faut tout d'abord
        * Assigner à l'utilisateur le rôle `Shared VPC Admin` pour l'organisation cible. Il pourra alors créer des réseaux partagés pour cette organisation.
        ```
        gcloud organizations add-iam-policy-binding [ORG_ID] 
            --member='user:[EMAIL_ADDRESS]'
            --role="roles/compute.xpnAdmin"
        ```
        `ORG_ID` est l'identifiant de l'organisation cible obtenu via la commande :`gcloud organizations list`.
        Vous pouvez aussi opter pour affecter le rôle à l'utilisateur, mais sur un `Dossier` spécifique. Il ne pourra alors créer des réseaux VPC que pour les projets de ce dossier de l'organisation.
        ```
        gcloud organizations add-iam-policy-binding [FOLDER_ID] 
            --member='user:[EMAIL_ADDRESS]'
            --role="roles/compute.xpnAdmin"
        ```
        `FOLDER_ID` est l'identifiant du dossier cible de l'organisation obtenu via la commande : `gcloud beta resource-manager folders list --organization=[ORG_ID]`.
    * Une fois les rôles affectés, vous pouvez maintenant activer le partage un projet comme hébergeant de réseau VPC via la commande : `gcloud compute shared-vpc enable [HOST_PROJECT_ID]`. Cette commande aura pour effet de créer un réseau VPC partagé hébergé sur le projet cible.
    * Une fois le réseau VPC partagé crée, vous pouvez associer des projets à ce partage
        * `gcloud compute shared-vpc associated-projects add [PRJ] --host-project [HOST_PRJ]`, pour des autorisations au niveau Organisation
        * `gcloud beta compute shared-vpc associated-projects add [PRJ] --host-project [HOST_PRJ]`, pour des autorisations au niveau Projet

4. Appairage de réseaux VPC

    * Lorsque vous ne disposez pas d'une organisation, il vous est impossible d'utiliser les fonctionnalités de réseau partagés
    * Alternativement, vous pourrez créer un appairage de deux réseaux VPC pour aboutir à un résultats similaire grâce à la commande
        ```
        gcloud compute networks peerings create NAME --network=NETWORK
            --peer-network=PEER_NETWORK [--async] [--auto-create-routes]
            [--export-custom-routes] [--export-subnet-routes-with-public-ip]
            [--import-custom-routes] [--import-subnet-routes-with-public-ip]
            [--peer-project=PEER_PROJECT] [GCLOUD_WIDE_FLAG ...]
        ```
        * `NAME` représente le nom de l'appairage
        * `--network=NETWORK` permet de spécifier le réseau source du projet en cours qui sera appairé
        * `--peer-network=PEER_NETWORK` permet de spécifier le réseau d'appairage
        * `--peer-project=PEER_PROJECT` permet de spécifier le projet hébergeant le réseau cible de l'appairage
        * `--export-custom-routes` permettra d'exporter les routes du réseau source vers le réseau d'appairage
        * `--no-export-custom-routes` annule les effet de `--export-custom-routes`
        * `--export-subnet-routes-with-public-ip` permettra d'exporter les routes des sous réseau sources dans le domaine d'adresses IP Publique vers le réseau d'appairage
        * `--no-export-subnet-routes-with-public-ip` annule les effets de `--export-subnet-routes-with-public-ip`
        * `--import-custom-routes` permet d'importer les routes du réseau d'appairage vers le réseau source
        * `--import-subnet-routes-with-public-ip` permet d'importer les routes des sous réseau sources dans le domaine d'adresses IP Publique vers le réseau source

        Par exemple, afin de faire communiquer les VMs des réseaux `demo-vpc-01` et `demo-vpc-02` il faut :
        * Créer un appairage de `demo-vpc-01` vers `demo-vpc-02`

        `gcloud compute networks peerings create demo-vpc-peering-01 --network=demo-vpc-01 --peer-network=demo-vpc-02 --export-custom-routes --import-custom-routes --export-subnet-routes-with-public-ip --import-subnet-routes-with-public-ip`

        * Créer Un appairage inverse de `demo-vpc-02` vers `demo-vpc-01`

        `gcloud compute networks peerings create demo-vpc-peering-02 --network=demo-vpc-02 --peer-network=demo-vpc-01 --export-custom-routes --import-custom-routes --export-subnet-routes-with-public-ip --import-subnet-routes-with-public-ip`

        * Ensuite pour effectuer un test de `PING` depuis une VM du réseau `demo-vpc-01`, vous devez ouvrir le protocole `icmp` sur le réseau cible `demo-vpc-02`.

## Déploiement des service Compute Engine avec un réseau personalisé

1. Déployez une VM compute engine dans un réseau VPC via la console web

    Sans vouloir revenir sur le process de création de VM, nous alons juste porter notre attention sur le zone de mise en réseau.
    * Choisissez votre réseau (`demo-vpc-01`) dans la liste des réseaux disponibles. Ensuite GCP vous présentera la liste des sous-réseaux de ce réseau compatibles avec la région de vitre VM (Aucun sous-réseau ne vous sera présenté si vous n'avez pas de sous-réseau dans la même région que celle de votre VM)

    ![`Cloud VPC : Create VM`](./cloud-vpc-create-vm.png)

2. Déployez une VM compute engine dans un réseau VPC via Cloud SDK

    Via la ligne de commande, il faudra juste préciser :
    * Le réseau dans lequel votre vm devra ètre créee : `--network=NETWORK`
    * Le sous-réseau dans lequel votre vm devra ètre créee : `--subnet=SUBNET`

## Création de règles de pare-feu pour un cloud privé virtuel

* Les règles de pare feu vont vous permettre de contrôler le traffic sur des ports de vos ressources: Par exemple, une règle permettant d'accepter le traffic TCP sur le port 22.
* Les règes permettent aussin de spécifier la direction du traffic (entrant ou sortant)
* Une connection est considérée comme active s'il y a au moins un paquet toutes les 10mn

1. Structuration des règles de pare-feu

    * `Direction` : Entrée (Ingress) ou Sortie (Egress)
    * `Priorité` : Entier compris dans l'intervalle [0, 65535].
        * La valeur `0` est la priorité la plus forte
        * La valeur `65535` est la priorité la plus faible
        * Lorsque deux règles matches une transaction, celle avec la priorité la plus élevée est exécutée et les autres non
    * `Action` : Allow (Permettre) ou Deny (Interdire)
    * `Cible` : Instance sur laquelle la règle s'applique. Les cibles peuvent être toutes les instances ou des instances ayant certains `TAGS` ou encore des instances associé à un compte spécifique
    * `Source` Permet de d'appliquer des règles de filtre aux entités sources de traffic entrant (`Ingress`). On peut filtrer suivant :
        * La plage d'adresses
        * Les TAGS de l'entité
        * Le compte de service
    * `Destination` Permet d'appliquer des filtre aux entités cibles du traffic sortant (`Egress`). On peut filtrer suivant :
        * La plage d'adresse cible
    * `Protocoles et Ports` : Permet de préciser les protocoles (TCP, UDP, ICMP, etc...) et les ports à prendre en compte dans la règle de pare feu.
    * `Status` permetde préciser si la règle de pare feu est active ou inactive

    Tous les VPC disposent de deux règles implicites ayant la priorité la plus basse, vous permettant ainsi de les override.
    * Une permettant d'autoriser tous le traffic sortant
    * Une interdisant tous traffic entrant.

    Un réseau VPC crée automatiquement, génère 4 règles de pare feu de basse priorité (65534)
    * Autorisation de traffic entrant à partir de n'importe quelle VM du même réseau
    * Autorisation de traffic entrant sur le port SSH (22)
    * Autorisation de traffic entrant sur le port RDP (3389)
    * Autorisation de traffic entrant sur le port ICMP depuis n'importe quelle source du réseau

2. Création de règles de pare-feu pour via Cloud Console

    * Sur la page principale de gestion des réseaux VPC, allez sur le lien `Pare feu`

    ![`Cloud VPC : Firewall Rule Menu`](./cloud-vpc-firewall-rule-menu.png)

    * Dans la page des `Pare feu`, cliquer sur le lien d'ajout d'une nouvelle règle

    ![`Cloud VPC : Firewall Rule Main`](./cloud-vpc-firewall-rule-main.png)

    * Dans le formulaire de `Pare feu`, saisissez les informations attendues

    ![`Cloud VPC : Firewall Rule Form 01`](./cloud-vpc-firewall-rule-form-01.png)

    ![`Cloud VPC : Firewall Rule Form 02`](./cloud-vpc-firewall-rule-form-02.png)

3. Création de règles de pare-feu pour via GCloud

La commande est 
```
gcloud compute firewall-rules create NAME
    (--action=ACTION | --allow=PROTOCOL[:PORT[-PORT]],[...])
    [--description=DESCRIPTION]
    [--destination-ranges=CIDR_RANGE,[CIDR_RANGE,...]]
    [--direction=DIRECTION] [--disabled] [--[no-]enable-logging]
    [--logging-metadata=LOGGING_METADATA]
    [--network=NETWORK; default="default"] [--priority=PRIORITY]
    [--rules=PROTOCOL[:PORT[-PORT]],[...]]
    [--source-ranges=CIDR_RANGE,[CIDR_RANGE,...]]
    [--source-service-accounts=EMAIL,[EMAIL,...]]
    [--source-tags=TAG,[TAG,...]]
    [--target-service-accounts=EMAIL,[EMAIL,...]]
    [--target-tags=TAG,[TAG,...]] [GCLOUD_WIDE_FLAG ...]
```
* `NAME` représente le nom de la règle
* `--action=ACTION` permet de préciser l'action à exécuter (ALLOW ou DENY)
* `--allow=PROTOCOL[:PORT[-PORT]],[...])` permettra de préciser la liste des protocoles et port autorisés par la règle de pare feu (Alias de `--action=ALLOW`). 
    Les protocoles autorisés :
    * `tcp`
    * `udp`
    * `icmp`
    * `esp`
    * `ah`
    * `sctp`

    Exemples :
    * Port TCP de 20000 à 25000 : `--allow tcp:20000-25000`
    * Port TCP 80 et ICMP : `--allow tcp:80,icmp`
    * Tout traffic TCP : `--allow tcp`
* `--description=DESCRIPTION` permet de préciser la description de la règle
* `--direction=DIRECTION` permet de spécifier la direction du traffic
    * `INGRESS` ou `IN`
    * `EGRESS` ou `OUT`
* `--destination-ranges=CIDR_RANGE,[CIDR_RANGE,...]` permet de préciser les CIDR d'adresses IP de destination dans le cas de règles de `DIRECTION=EGRESS ou OUT`. La valeur par défaut de ce champ est `0.0.0.0/0`
* `--cource-ranges=CIDR_RANGE,[CIDR_RANGE,...]` permet de préciser les CIDR d'adresses IP de source dans le cas de règles de `DIRECTION=INGRESS ou IN`. La valeur par défaut de ce champ est `0.0.0.0/0`
* `--rules=PROTOCOL[:PORT[-PORT]],[...]` permet de spécifier les protocoles et ports sur lesquels s'appliquera l'action de la règle.
* `--priority=PRIORITY` permet de spécifier le priorité de la règle. Valeur entre [0 - 65534], 0 étant la plus haute priorité.
* `--network=NETWORK` permet de spécifier le réseau VPC sur lequel la règle s'applique
* `--source-service-accounts=EMAIL,[EMAIL,...]` permet de spécifier les comptes des instances sur lesquels la règle de traffic entran s'appliquera
* `--source-tags=TAG,[TAG,...]` permet de spécifier les TAGS des instances sur lesquels la règle de traffic entrant s'appliquera
* `--target-service-accounts=EMAIL,[EMAIL,...]` permet de spécifier les comptes des instances sur lesquels la règle de traffic sortant s'appliquera
* `--target-tags=TAG,[TAG,...]` permet de spécifier les TAGS des instances sur lesquels la règle de traffic sortant s'appliquera

Quelques exemples:
* `gcloud compute firewall-rules create rule01_allow_8080 --allow=tcp:8080 --description="Allow incoming traffic on TCP port 8080" --direction=INGRESS`
* `gcloud compute firewall-rules create rule01_allow_80 --allow=tcp:80 --source-ranges="10.0.0.0/22,10.0.0.0/14" --description="Narrowing TCP traffic"`

## Création d'un réseau privé virtuel

Avant de commencer il faut noter les points suivants :

* GCP ne supporte que des VPN Site To Site
* Nous configurerons un VPN Site 2 Site via un tunnel IPSec IKV2
* Nous présenterons la configuration d'un VPN GCP Haute disponibilité (2 adresse IP distantes pour supporter le failover), mais toutes les configurations ici sont valables pour un VPN Site 2 Site classique
* Nous configurerons un Tunnel Cloud VPN, exploitant le protocole BGP pour les annonces dynamiques de routes
* Nous présenterons aussi rapidement comment accéder à un service Cloud SQL privé (crée sans adresse publique) sur notre VPN

1. Création d'un réseau privé virtuel via Cloud Concole

    * Création d'un routeur Cloud (Cloud Router) qui sera le Peer côté GCP (il hébergera les sessions BGP).

        * Rendez vous dans `Connectivité Hybride` et allez sur le menu `Routeur Cloud`

        ![`Cloud VPN : Menu Hybrid Connectivity`](./cloud-vpn-menu-hybrid-connectivity.png)

        ![`Cloud VPN : Cloud Router`](./cloud-vpn-cloud-routers.png)


        * 

    * Création d'une passerelle VPN Cloud
    * Création d'une passerelle VPN Peer (représente la passerelle VPN côté Site On-Premise)
    * Création d'un Tunnel Cloud VPN



2. Création d'un réseau privé virtuel via GCloud

## Résumé

## Quelques questions d'examen

## Revue des questions
