# Overview de la plateforme de service Google Cloud Platform

Le but de ce chapitre est de présenter à l'apprenant l'ensemble des services disponibles sur la plateforme [`Google Cloud Platform`](https://cloud.google.com/certification/guides/cloud-engineer/), leur utilité, ainsi que quelques cas d'usage de ces services.

À noter que les clients des plateformes Cloud ([`Google Cloud`](https://cloud.google.com/), [`AWS`](https://aws.amazon.com), [`Microsoft Azure`](https://azure.microsoft.com), etc...) sont de deux types :

1. Les clients qui démarrent directement leurs activités dans le Cloud

    Ce type de clients peut démarrer de manière graduelle en ciblant au fur et à mesure, les services cloud dont il a besoin à un moment de son développement (Ex: IAM, VM, STORAGE)

2. Les clients qui disposent déjà d'une infrastructure `On-Premise` et veulent l'étendre via le Cloud

    Ce type de client par contre a le challenge impératif de pouvoir intégrer son infrastructure privée avec celle acquise sur le Cloud, ce qui pourrait engendrer des contraintes de coûts supplémentaires. Par exemple :

    * Dans le cas où le client dispose déjà d'une infrastructure d'authentification basée sur `Active Directory`, il souhaiterais avoir la possibilité de l'intégrer et la synchroniser avec l'infrastructure d'authentification du fournisseur de Cloud.

    * le client pourrait aussi avoir besoin de maintenir une infrastructure réseau sécurisée et dédiée (en cas de gros volumes d'échange) entre ses sites privés (`On-Premise`) et ses site public (`On-Cloud`)

## Types de services cloud offerts

Les fournisseurs de cloud offrent en général des services qui peuvent être organisés en 4 catégories :

1. Ressources de calcul (Compute Resources)

    On distingue plusieurs variétés de ressources de calcul

    * `Machine virtuelles (Google Compute Engine)`

        * `Unité de base` des ressources de calcul
        * Point de départ lorsque l'on souhaite expérimenter le cloud
        * Pour créer des VMs, il faut :
            * Créer un compte GCP (Google offre 300$ aux utilisateurs pour la première activation GCP)
            * Fournir des information permettant la facturation (carte de crédit/debit, etc...)
            * Se servir de la console de Self-Service ou de la ligne de commande afin de créer des VMs
        * GCP propose un ensemble de pré-configuration de VMs qui varient principalement en fonction
            * du nombre de CPUs
            * de la quantité de mémoire
        * Les pré-configurations de VMs proposées par GCP sont catégorisées en familles suivant des besoins spécifiques et des séries de génération de processeurs proposées par GCP
            * Famille de VMs à usage général, pour des travaux courants
                * Série N1, fourni sur des processeurs Intel Skylake ou un de ses prédécesseurs
                * Série E2, technologie processeur déterminée automatiquement en fonction de sa disponibilité
                * Série N2, fourni sur des processeurs Cascade Lake
                * Série N2D, fourni sur des processeurs AMD EPYC Rome
            * Famille de VMs à mémoire optimisée, pour des travaux exigeant en mémoire
                * Série M1, fourni sur des processeurs Intel Skylake ou un de ses prédécesseurs
            * Famille de VMs à processeur optimisé, pour des travaux exigeant en performance de calcul
                * Série N2, fourni sur des processeurs Cascade Lake
        * Il est aussi possible de créer des configurations personalisées de VMs
        * Une fois la VM créee vous pouvez l'administrer
            * Configurer le système de fichier
            * Installer des packages addistionnels
            * Ajouter du stoakage
            * Patcher le Système d'exploitation
            * etc...
        * GCP propose aussi d'autres ressources liées aux VMs
            * `Load Balancers`, utiles pour la haute disponibilité, ils fournissent un point d'accès unique sur des services `Back-End` distribués. Si l'une des VMs faisant tourner un de nos services tombe, le traffic pourra être redirigé vers une autre VM en fonction de la configuration du `Load Balancer`
            * `Autoscalers`, qui permettent de rajouter ou de supprimer des VMs en fonction de la charge, permettant ainsi de controller les coûts en executant le nombre se VMs strictement n´cessaire à notre besoin. On parle de `Horizontal Autoscaling`.

    * `Clusters Kubernetes Managés (Google Kubernetes Engine)`

        GCP nous fourni les outils permettant de créer, manager et monitorer un `Cluster Kubernetes`.

        * Un `Cluster Kubernetes` est un `Orchestrateur` assurant entre autres la gestion de l'infrastructure de calcul ainsi que des déploiements et de la disponibilité de vos services applicatifs dans des conteneurs, crées sur des virtuelles provisionnées manuellement (via des scripts) ou automatiquement (par un stratégie d'autoscaling)
        * L'utilisateur peut donc se concentrer sur la production et la distribution de ses services plutôts que sur le maintien des serveur en conditions opérationnelle.
        * Les `Clusters Kubernetes` font usage de conteneur afin de déployer les services.
        * Les conteneurs sont en quelque sorte des VMs ultra légères, qui permettent de cloisonner les capacités de calcul et de stockage (CPU, Mémoire, Disque, Réseau, etc..) afin de garantir à chaque conteneur un ensemble de ressources dédié à son fonctionnement.

    * `Serverless`

        Contrairement au deux premiers types de ressources de calcul (`Machines Virtuelles` et `Cluster Kubernetes`) qui nécessitent la création d'une ou de plusieurs VMs, l'approche `Serverless` permet d'exécuter du code sans avoir à créer une VM ou un Cluster de VM. GCP offre deux type de déploiement `Serverless`

        * `AppEngine`, utilisé pour l'exécution d'application et/ou de conteneur en continue (site de e-commerce, une application métier, etc...)
        * `Cloud Function`, utilisé pour déployer des application callback permettant de réagir à un évènement donné (arrivé d'un message dans une file, upload d'un fichier, réception d'un mail, etc...)

2. Ressources de stockage (Storage Resources)

    GCP offre un ensemble de types de stockage qui sont adaptés à des besoins spécifiques :

    * `Stockage Objet (Object Storage)`

        Dénomé `Cloud Storage`, ce type de stockage est principalement utilisé pour l'archivage et le stockage de fichiers sous forme d'objets/BLOBS (Binary Large OBjectS)

        * Permet le stockage de données sous forme d'objet ou Blobs (Binary Large OBjectS).
        * Les fichiers ne sont pas stockés dans un système de fichiers conventionnel (hiérarchie de répertoires, etc...)
        * Les objets à stocker sont regroupés dans des `BUCKETS`
        * Chaque objet est identifiable de manière précise via son URL
        * Les `BUCKETS` sont `Serververless` et de ce fait, le stockage d'objet n'est pas limité par la taille d'un quelconque disque (qui serait rattaché à un serveur)
        * Les objets stockés peuvent disposer de plusieurs copies dans des zones géographiques différentes afin d'assurer la haute disponibilité en cas de panne dans une région.
        * Le contrôle d'accès peut être appliqué à chaque objet stocké, permettant alors au propriétaire de définir qui peut y avoir accès en Lecture/Modification/Suppression.
        * Les objets stockés dans les `BUCKETS` sont accessible depuis les VMs crées sur GCP mais aussi depuis n'importe quel équipement disposant d'un accès internet et des autorisation nécessaires.

    * `Stockage Fichier (File Storage)`

        Dénommé `Cloud Filestore`, ce type de stockage fourni un système de fichier hiérarchique (comme celui des Systèmes d'exploitation)

        * `Cloud filestore` est basé sur le protocole `NFS (Network File System)`
        * Les fichier stockés existe indépendemment des VMs/Containers qui vont les consommer (via un montage)

    * `Block Storage`

        * Organise le stockage des données sous forme de `Blocks` de taille fixe (4 KB pour les systèmes de fichiers sous linux)
        * Principalement utiisé pour fournir des disques persistants (qui stockent les données au-delà de l'existence d'une VM) ou ephemères (Qui stockent des données uniquement lorsque la VM est active) rattachés à des VMs.
        * Un disque éphémère a un cycle de vie est lié à celui de la VM à laquelle ils est rattaché. Il sera donc principalement utilisé dans pour stocker des données comme des systèmes d'explitation, ou autres données inutiles après l'arrêt d'un VM
        * Un disque persistant quant existe au indépendemment d'un VM à laquelle il serait rattaché.
        * On peut y installer un système de fichier (par formattage en général)
        * On peut aussi mettre en place des application qui accèdent directement aux Blocks, sans passer par un système de fichier
        * La différence entre `Block Storage` et `Object Storage` vient :
            * du fait que `Object Storage` supporte uniquement des accès HTTP(s) aux fichier qu'il stockent.
            * de la lenteur d'accès aux données des supports `Object Storage` relativement aux `Block Storage`
        * Très souvent, on pourra utiliser `Block Storage` et `Object Storage` ensemble pour les besoins de nos services applicatifs, `Object Storage` permettant de stocker de grands volumes de données, qui pourront être copiés sur des disque `Block Storage` au besoin (`cas de l'exploitation d'une archive stockée sur un Bucket par une Application tournant sur une VM`)

    * `Cache`

        * Structure de stockage de données en mémoire (`In-Memory data stores`) qui permettent de maintenir un accès rapide aux données.
        * Le temps d'accès aux données d'un support est appelé `Latence (Latency)` et voici une petite comparaison
            * Lecture aléatoire de 4KB sur un disque SSD prend 150 micro secondes
            * Lecture séquentielle de 1MB en mémoire prend 250 micro secondes
            * Lecture séquentielle de 1MB sur un disque SSD prend 1 000 micro secondes (1 milliseconde)
            * Lecture séquentielle de 1MB sur un disque HDD prend 20 000 micro secondes (20 millisecondes)
        * Toutefois, il n'est pas conseiller d'utiliser les caches comme unique mécanisme de stockage
            * La mémoire (RAM) utilisée pour mettre en euvre les caches coûte plus cher que des disques SSD, qui eux-même coutent plus cher que des HDD
            * La mémoire cache est volatile et de ce fait, toutes les données qui s'y trouvent se perdent en cas d'arrêt ou de redémarrage du système qui héberge le cache
            * Le cache peut être désynchronisé par rapport au support de stockage permanent duquel les données sont chargées.
        * Lorsqu'on utilise un cache, il faut s'assurer impérativement que la stratégie de mise à jour du cache est supporte les contraintes de synchronicité avec le support de stockage permanent des données.
        * Un petit exemple d'utilisation réel de cache,

        ```accesslog
        Cas d'une application web, dans laquelle une réponse de plus de 3 secondes n'est pas acceptable en terme de SLA.
        Dans ce cas, au fur et à mesure que les usagers font des recherches par exemple afin d'obtenir des informations sur un produit depuis la base de données, cette dernière va mettre les requêtes dans une FIFO et les exécuter progressivement, ce qui aura pour conséquence une lenteur du point de vue de certains utilisateurs dont l'exécution de la requête sera en bout de file.

        Une solution est de s'assurer que le stockage utilisé pour la base de données est du SSD.
        Mais au cas où cette solution serait encore insiffisante, il serait bien de mettre en place un cache, afin de stocker le résultats d'aciennes requêtes en mémoire de manière à ne pas surcharger la base de données par des requêtes strictement redondantes.
        ```

3. Ressources réseau (Networking)

    Lorsqu'on travaille sur le cloud, il est très fréquent de vouloir gérer des contraintes liées au réseau d'accès aux équipement et services, mais aussi éventuellement au d'interconnexion entre les ressources de notre cloud public et notre système interne (On-Premise).

    GCP met à disposition un ensemble de services permettant de gérer les aspects réseau : `Virtual Private Cloud (VPC - Réseau privé)`, `Firewall`, `Adresses IP Publiques`, `PEERING (Liaison de réseaux VPC distincts)`, `Cloud VPN (Liaison VPC <-> On-Premise Infra)`

    * Chaque utilisateur `GCP` dispose de deux réseaux
        * Un réseau privé (`Virtual Private Cloud (VPC)`) dont les ressources ne sont accessibles que de l'intérieur du réseau.
        * Un réseau publique dont les ressources sont accessibles depuis internet. Les adresses IP de ce réseau sont de deux types
            * Adresses IP `Éphémères`
                * Créees automatiquement par la plateforme GCP lors du démarrage d'une machine virtuelle créee sans AdresseIP
                * Attribuées de manière aléatoire à une machine virtuelle au démarrage et libérée à l'arrêt.
                * Aucune garantie qu'une VM disposera de la même adresse IP `Éphémère` entre deux démarrages
            * Adresses IP `Statiques`
                * Créees par l'utilisateur indépendament de l'équipement qui y sera associé
                * Attribuée aux ressources de manière permanente.
                * Garantie que l'équipement associé conservera la même adresse entre deux démarrages
    * Tout équipement et service GCP dispose d'une adresse IP

    * En plus de la gestion des adresses IP, nous avons aussi besoin de gérer les règles de pare-feu (firewall), permettant de contrôller l'accès à nos ressources via nos sous-réseaux internes (VPC). Ceci est particulièrement utile lrsque par exemple
        * Nous disposons d'une base de données dont nous souhaitons restreindre l'accès uniquement à un serveur d'application donné.
    * Les règles de pare-feu vont donc nous permettre de limiter le traffic (entrée/sortie) vers des adresses IP ou encore vers des `LoadBalancers` frontaux de nos services, VMs ou Clusters.
    * Différents type sde `Peering` vont nous permettre de liér entre eux nos réseaux privés VPC disticts
    * Le service `Cloud VPN` nous permet de connecter notre réseau VPC et notre infrastructure On-Premise afin de partager des données.

4. Services spécialisés (Specialized Services)

    Google Cloud Platform (tout comme d'autres fournisseurs cloud), propose un ensemble de services utilitaires, qui pourront être consommés par les ressources et services d'un utilisateurs (organisation) comme brique de base. Ces services spécialisés ont les caractéristiques suivantes :

    * `Serverless`, pas besoin de déployer une VM ou un Cluster pour les activer
    * Fournissent un traitement spécifique (par exemple : Traduction de textes, analyse d'images)
    * Fournissent une API pour l'accès aux fonctionnalités du service offert
    * Ils sont facturés à l'utilisation

    Voici quelques service spécialisés offerts par GCP

    * `AutoML`, un service de `Machine Learning`
    * `Cloud Natural Language`, pour la traduction de texte
    * `Cloud Vision`, pour l'analyse d'images
    * `Cloud Inference`, pour la gestion des correlation des données de series temporelles (time-series)

    Les services spécialisés rendent disponibles, sous forme d'API simple et réutilisable, des traitement qui sont en réalité complexes, permettant ainsi de développer des systèmes complexes en s'appuyant sur l'expérience Google.

## Différence entre Cloud Computing et Datacenter Computing

Il existe de grandes différences entre un Datacenter privé On-Premise et sur un Cloud public.

1. Location plutot que possession de ressources (Rent instead of Owned resources)

    Derrière cet indicateur, se cache un certain nombre d'avantages et de flexibilité

    * La `Flexibilité`, qui permet aux organisation d'adresser convenablement la problématique lié au besoin de ressources supplémentaires dans des périodes de pic d'activité.

    En effet, dans un modèle de Datacenter On-Premise, il est difficile d'acquerir, d'installer et de configurer des ressources à la demande. par conséquent, si une organisation a besoin de 20 serveurs pour couvrir son activité en période creuse et 80 serveurs en période de vaccances, alors elle sera dans l'obligation d'acquerir 80 serveurs dont 60 seront inutiles la grande partie du temps.

    Dans un modèle `Full Cloud`, l'organisation peut démarrer avec un minimum de serveur et en acuqrir d'autres pour une période spécifique de manière manuelle, ou automatique (Autoscalers)

    Dans un modèle `Hybrid Cloud`, l'organisation peut acuqrir 20 serveurs pour couvrir son activité nominale et provisionner 60 serveurs sur le cloud uniquement durant les périodes de pic d'activité

2. Modèle  de facturation `Payer au fur et à mesure de votre consommation` (Pay as you go for what you use)

    Le principe de facturation chez les fournisseur de Cloud est de payer ce qu'on a consommé.

    * Généralement, lorsqu'on demarre une VM, nous somme facturé pour une période initiale (par exemple 10mn), après laquelle nous commeçon une facturation par minute (ou par heure) en fonction des fournisseur
    * Le coôut de la ressource (par exemple une VM) par unité dépends de la ressource, ainsi que de ses caractéristiques (une VM de 96 Coeurs n'aura pas le même coût qu'un de 3 coeurs)
    * Il es très important de comprendre le modèle de facturation du fournisseur de Cloud en vue d'optimiser le coût de déploiement des services sur ce Cloud

3. Allocation de ressources élastique (Elastic Resources Allocation)

    Dans un cloud, il est possible d'aquerir un nombre très élevé de serveur à la demande. En effet, le fournisseur de Cloud met à disposition de chaque client une capacité maximale de ressources qui est largement suffisante pour des besoins d'une organisation.
    De plus, chez GCP, les quotas de ressources peuvent être élargis sur demande explicite du client à l'équipe Google Cloud.

4. Services spécialisés (Specialized Services)

    Les services spécialisés sont par nature des services qui ne sont pas à la portée de tous. En effet, tous les développeurs ne connaissent pas forcément le domaine de `L'analyse du langage naturel`, du `Machine Learning` ou encore de la `Data science`.
    Un fournisseur de Cloud va rendre disponible ce type de service afin de mutualiser ce savoir faire pour tout ses utilisateurs.
