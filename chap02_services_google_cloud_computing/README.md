# Services de la Plateforme Google Cloud Platform

[`Google Cloud Platform`](https://cloud.google.com/) est une plateforme offrant une suite de services cloud (Calcul, Stockage et réseau) à ses clients. Les petites organisations se contentent très souvent de machines virtuelles (VMs) et de stockage, tandis que les grandes entreprises consomment en plus des Clusters, des bases de données BigData ou encore des Services Spécialisés.

l'objectif de ce chapitre est de présenter et de décrire les cas d'usage des services importants proposés par [`Google Cloud Platform`](https://cloud.google.com/).

À la fin du chapitre, le lecteur devra être familier avec chacun des services des différentes catégories offertes par GCP

1. __***`Composants de calcul`***__
    * Google Compute Engine
    * Google Kubernetes Engine
    * Google AppEngine
    * Google Cloud Functions
2. __***`Composants de stockage`***__
    * Google Cloud Storage
    * Google Persistent Disk
    * Google Cloud Storage for Firebase
    * Google Cloud Filestore
    * Google Databases
    * Google Vloud SQL
    * Google Cloud bigTable
    * Google Cloud Spanner
    * Google Cloud Datastore
    * Google Cloud Meorystore
    * Google Firestore
3. __***`Composants réseau`***__
    * Google Virtual private Cloud
    * Google Cloud Load Balacing
    * Google Cloud Armor
    * Coogle Cloud CDN
    * Google Cloud Interconnect
    * Google Cloud DNS
    * Google Identity Management (IAM)
    * Google Development Tools
4. __***`Composants additionnels`***__
    * Google Management Tools
    * Google Specialized Services
        * Google Apigee API Platform
        * Google Data Analytics
        * Google Artificial Intelligence and Machine Learning

## ***Composants de calcul de la plateforme Google Cloud (GCP)***

### ***Ressources de calcul (Compute Resources)***

Les ressource de calcul de la plateforme GCP offrent au client des services suivant différents modèles d'utilisation

* Le modèle `IaaS (Infrastructure As A Service)`, dénommé `Google Compute Engine`, c'est modèle dans lequel le client peut créer, configurer et administrer des VMs; Il peut notament:
  * Choisir le système d'exploitation à installer
  * Configurer la mémoire nécessaire
  * Configurer les CPUs (nombre et technologies)
  * Provisionner et associer des disques
  * Effectuer toutes sorte de tâches d'administration, de sauvegarde et restauration sur les VMs crées
  * Associer les VMs à un Network
  * etc..

  __***En gros c'est un modèle dans lequel le client assure lui-même la gestion de ses VMs.***__
  __***Les services IaaS de GCP sont :***__
  * `Google Compute Engine (GCE)`

* Le modèle `PaaS (Platform As A Service)`, c'est une alternative au modèle `IaaS`, fournissant au client un environnement de déploiement et d'exécution d'application, sans les contraintes de création et d'administration de VMs, de stockage ou de réseau. Le client peut ainsi se concentrer sur son application en laissant à la plateforme la gestion de l'infrastructure serveur et réseau.

  __***En gros c'est un modèle dans lequel la plateforme Cloud assure la gestion de l'infrastructure et le client se concentre sur la gestion de son service applicatif.***__

  __***Les services PaaS de GCP sont :***__
  * `Google AppEngine`
  * `Google Cloud Functions`

* Le modèle `PaaS géré par le Client`, c'est un modèle dans lequel le client lui-même instancie et administre lui-même son environnement de gestion de conteneurs applicatifs (Docker/RKT).
  __***Les services PaaS Client de GCP sont :***__
  * `Google Kubernetes Engine (GKE)`

#### Présentation des services de calcul

1. `Google Compute Engine (GCE)`

    * Service permettant aux client :
        * Créer des VMs
        * Attacher du stockage persistent au VMs
        * Exploiter d'autre services (`Cloud Storage (Stockage Objet)`)
        * Effectuer des tâches d'administrtaions diverses
        * etc...
    * Les VMs sont des abstraction des machines physiques, des programmes qui émulent les machine physiques et fournissent CPU, Mémoire, Stockage et d'autres services
    * Les VMs sont crées et exécutées par des hyperviseurs, qui sont des programmes d'exploitation des ressources physiques (comme des OS), et dont le seul objectif est de permettre de créer et gérer des VMs
    * Pour mettre en oeuvre le service `GCE`, `GCP` utilise un hyerviseur de type 1, basé sur `KVM (Kernel Virtual Machine)`
    * `KVM (Kernel Virtual Machine)` est une technologie de virtualisation intégrée au noyau Linux depuis la version `2.6.20`. `KVM` permet de transformer `Linux` en hyperviseur permettant ainsi à la machine hôte d'exécuter plusieurs environnements isolés (les machine virtuelles)
    * Un hyperviseur (comme `KVM`) permet d'exécuter plusieurs instances de systèmes d'exploitation appelé `Système invité`.
    * Dans le cas de `GCE`, `KVM` joue le rôle de `Système d'exploitation`, et met à disposition un programme d'`Hypervision` permettant de gérer des VMs.

    ![Instances de VMs dans un Hyperviseur](./vm_instances_in_hypervisor.png)

    * Lorsqu'on crée une VM, nous avons la possibilité de configurer plusieurs aspects parmis lesquels :
        * Le modèle de VM, choisi parmis des modèles existant ou personalisés (choix implicite du CPU et de la RAM)
        * Le CPU
        * La RAM
        * La taiile et le nombre de disques persistants
        * Le système d'exploitation
        * Les unités de calcul Graphiques (CPU graphiques) si la machine est destinée à des calculs intensif comme le Machine Learning
        * Les Tags réseau
        * L'interface réseau
        * Les règles de parefeu de bases (http/https)
        * Les cñlés SSH autorisés à se connecter à la VM
        * Les scripts à exécuter au démarrage de la VM
        * La préemptibilité de la machine
            * Une machine préemptible coûtera beaucoup moins cher (80% moins cher) qu'une machine normale, mais la contrepartie étant qu'il n'ya aucune garantie de disponibilité. Elle peut être arrêtée à tout moment par GCP dépendament des besoins de la plateforme. En général, elle sera arrêtée après 24H d'exécution continue

2. `Google Kubernetes Engine (GKE)`
3. `Google AppEngine`
4. `Google Cloud Functions`


## ***Composants de stockage de la plateforme Google Cloud (GCP)***

1. Ressources de stockage (Storage Resources)
2. Base de données (Databases)

## ***Composants de networking de la plateforme Google Cloud (GCP)***

1. Service réseau (Networking Services)
2. Gestion des indentités (Identity Management)
3. Outils de développement (Development Tools)

## ***Composants additionnels de la plateforme Google Cloud (GCP)***

1. Services de gestion (Management Services)
2. Services spécialisés (Specialized Services)
