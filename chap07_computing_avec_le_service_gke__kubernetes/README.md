# Computing avec le service Google kubernetes Engine (GKE)

Dans ce chapitre, nous allons apprendre :

* Ce qu'est Kubernetes
* Quel est son architecture générale
* Comment gérér et monitorer les ressources Kubernetes via
  * La console GCE
  * Les lignes de commandes Cloud Shell et Cloud SDK
* Comment déployer des PODS

## Introduction au moteur d'orchestration Kubernetes (Kubernetes Engine)

0. Overview

    * GKE (Google Kubernetes Engine) est un service fournissant des clusters Kubernetes Managés
    * Les clients GKE peuvent créer et maintenir leurs clusters Kubernetes sans avoir à manager eux-même une plateforme Kubernetes
    * Kubernetes est un cluster de Machines (Virtuelles), permettant
        * d'orchestrer le déploiement des conteneurs sur l'ensemble des ressources (VMs ou Barre metal) du cluster
        * d'exécuter des containers en mettant à leur disposition un ensemble de ressources
        * de monitorer l'état de vie des conteneurs déployés sur le cluster
        * Gérer le cycle de vie des Machines (Virtuelles) du cluster.
    * Un clusters de VM Kubernetes est comparable à un groupe de VMs managés, l'une des différences est que les groupes de VMs partagent généralement la même configuration (Dans le cas de groupe managés [via un template de VM]) contrairement aux VMs d'un cluster Kubernetes qui auront très souvent des configurations différentes, en fonction de ce qu'on veut en faire. De plus, les Groupes d'instances de VMs ne disposent d'aucun mécanisme d'orchestration de déploiement de conteneurs.

1. Architecture d'un cluster Kubernetes

    Un cluster Kubernetes est constitué :

    * D'un noeud `Master`, dont le rôle est de gérer le cluster ainsi que les interactions avec les administrateurs. Ce noeud `Master` peut être répliqué pour des besoin de haute disponibilité
    * D'un ou de plusieurs noeuds `Worker`, dont le rôle est d'exécuter les charges de travail (conteneurs) qui lui sont fournie.

2. Concepts Kubernetes

    * `API Server`, qui est l'API principale exposée par un noeud `MASTER` et permettant l'interaction administrative avec `Kubernetes`.
    * `Controllers`, qui sont des services s'exécutant sur un noeud `MASTER` et permettent de gérer l'état des ressources déployées dans le cluster. Par exemple, un `Job Controller` est un exemple de Contrôleur Kubernetes dont le rôle est de s'assurer que l'état d'un JOB correspond à l'état attendu pat d'administrateur.
    * `Control Loop`, c'est un pattern de boucle infinie permettant de réguler l'état d'un système ou d'un composant du système. L'idée étant que à chaque itération de la boucle, l'état du éel du système sera comparé à l'état attendu et en cas de différence, des actions seront prise afin d'aligner l'état réel à l'état attendu.
    * `Scheduler`, qui permet de déterminer la VM sur laquelle les PODs seront déployés
    * `Kubelet` est un agent déployé sur un noeud `Worker`, permettant les échanges entre `MASTER` et `WORKER`. Il permet au `MASTER` de communiquer des commandes au `WORKERS`.
    * `kubectl`est un programme en ligne de commandes permettant d'interacgir avec le noeud MASTER d'un cluster Kubernetes.
    * `POD` est une instance d'un processus applicatif s'éxécutant sur le cluster Kubernetes. 
        * Un POD permet de faire tourner au minimum un conteneur
        * Les conteneurs tournant dans le même POD partagent plusieurs ressources
            * Adresses IP
            * Ports
            * Network
            * Stockage
            * VM (Tous les conteneurs d'un même POD sont schédulés sur la même VM)
    * `REPLICASET`, C'est un controleur dont le but est de s'assurer que le nombre d'instance d'un POD correspond bien au nombre attendu. Si le controleur de POD se rend compte (via la `sonde liveness`) qu'un POD ne répond plus, le controleur de POD le KILL. Le contrôleur de réplication va détecter (via la `Control Loop`) que le nombre d'instance attendue n'est plus respecté et va prendre des actions dans le but de re-créer le POD.
    * `POD Autoscaling`, c'est une propriété supportée par les PODS et permettant, sur la base de l'analyse de la consommation de ressources du POD (traduisant en principe la charge supportée), peut déclencher le provisionning d'une nouvelle instance du POD afin de partager la charge
    * `Service` représente un endpoint fournissant une API avec une IP stable, représentant des instances de PODs. En effet, un POD est essentiellement éphémère, cela dit, il peut être killé à tout moment pour être reschédulé sur un autre noeud, avec un adresse différente. Dans ce cas, tous les objets qui utilisaient les services de l'instance supprimée via son adresse directe ne seront plus désservis. Kubernetes met donc à dsposition un objet `Service` qui dispose d'une IP fixe et qui est associée à un ensemble de règles de routage permettant de rediriger le traffic vers les instances du POD tout en se mettant à jour en fonction des éventuels re-schéduling du POD
    * `Deployment` est un concept représentant une application déployée. Elle ressemble fortement à un `ReplicaSet`, à la différence que le `Deployment` intègre les problématiques de Stratégie de déploiement, de versionning, en plus de la réplication
    * `StatefulSet` représente une construction dans laquelle les PODs disposent d'un identifiant unique et persistent. En effet, un POD est stateless, ce qui signifie qu'il n'a pas d'état et que chacune de ses instances se valent, c'est le cas d'un microservice web qui exécute le calcul d'une fonction mathématique. Par contre, dans le cas d'une base de donnée ou d'un cache, nous avons par exemple besoin que chaque instance d'un POD soit toujours associé au même disque persistent et au même client. Pour cela Il faut utiliser des StatefulSets
    * `Job` est un objet permettant de créer des PODs qui vont s'exécuter jusqu'à ce que le travail se termine.
    * `DaemonSets` permet de créer des POD qui s'exécutent en une seule instance sur chacun des noeuds du cluster (en fonction de la strategie d'affinité de noeuds et de POD)

## Déploiement de clusters Kubernetes

Pour utiliser GKE, il faut toutd'abord activer l'API `Kubernetes Engine API`. Une fois activée, vous pouvez désormais accéder à la page principale du service `Kubernetes Engine` et créer un cluster. La première fois qu'on accède à l'interface principale GKE, il nous est demandé de créer des credentials afin d'accéder à cette API (En effet, certianes API GCP utilisent une plateforme authentification différente, du coup on aura besoin des credentials nécessaires pour s'authentifier sur ces plateformes [Ce mécanisme est certainement temporaire et pourrait êrte remplacé par une authentification unifiée])

![`Page Principale GKE`](./gke_main_page.png)

1. Déploiement d'un cluster Kubernetes via la Console Web

    Vous devez cliquer sur le bouton de création d'un nouveau cluster, contenu dans la page principale GKE. Vous devrez ensuite renseigner les informations suivantes

    * Paramètres généraux du cluster
        * Nom
        * Type d'emplacement
            * Zonal, dans ce cas il faudra choisir la zone (emplacement) dans laquelle seront créés les noeuds du cluster
            * Regional, dans ce cas il faudra choisir la region (emplacement) dans laquelle seront créés les noeuds du cluster
        * L'emplacement par défaut du cluster
            * Fixé à la valeur de la zone choisie en cas de type d'emplacement ZONAL
            * Possibilité de choisir entre les zones d'une région en cas de type d'emplacement REGIONAL
        * Version du setup GKE à utiliser pour l'installation des noeuds MASTER du cluster
            * `Stable`, représente les version STABLE du setup. Lorsque vous choisissez cette option, vous ferez les mises à jour manuellement
            * `version disponible`, vous permet d'exploiter même des version non stable. Lorsque vous choisissez cette option, vous bénéficiez de mise à jour automatiques lorsque de nouvelles versions sont disponibles.

    ![`GKE Parametre Generaux`](./gke_general_parameters.png)

    * La définition des pools de noeuds (`SLAVE`) de VM à utiliser pour créer le cluster (une configuration de pool de noeuds par défaut est proposé par GCP). Pour chaque pool vous renseignerez les informations :
        * Générale du pool
            * Le nom du Pool
            * Le nombre de noeuds du pool
            * L'activation ou non de l'autoscaling des noeuds de ce pool
            * L'emplacement des noeuds de ce pool
            * L'activation ou non de l'automatisation des mises à jours des noeuds du pool
                * Cette option est forcée à TRUE dans la cas où vous avez choisi `VERSION DISPONIBLE` comme option de setup GKE au niveau `MASTER`
        * sur le sous-groupe de noeuds `SLAVE` du cluster créés à partir de ce pool
            * Type d'image
                * `COS` (Container Optimized OS) par défaut
                * `COS avec Containerd`, c'est une variante de COS avec `containerd` comme environnement d'exécution des conteneurs au lieu de Docker.
                * `Ubuntu`, compatible avec NFS, Glusterfs, XFS, Sysdig, Debian
                * `Ubuntu avec Containerd` c'est une variante de `Ubuntu` avec containerd comme environnement d'exécution des conteneurs.
                * `Cloud ML` pour des conteneurs exécutant des algorithmes de Machine Learning
            * Le type de machine
            * Le type de disque de démarrage
            * Taille du disque de démarrage
            * Le Flag de noeud préemptif
            * Le nombre maximum de POD par noeud
            * Les TAGS réseau permettant de sélectionner les r`gles de pare-feu à utiliser pour l'accès aux noeuds créés dans ce pool
        * sur la sécurité du sous-groupe de noeuds du cluster créés à partir de ce pool
            * `Le compte de service` qui sera utilisé par les applications s'exécutant sur les noeuds de ce pool afin d'invoquer les API GCP
            * `Le niveau d'accès` des noeuds aux API GCP.
                * Accès par défaut
                * Accès complet à l'ensemble des API GCP
                * Définition personalisée des accès par API, qui permettra pour chacune des API de définir le niveau d'accès
        * sur les métadonnées à appliquer au sous-groupe de noeuds du cluster créés à partir de ce pool
            * Les libellés de noeud Kubernetes, qui peuvent être utilisés dans les expression de selection de noeuds (Node Affinity)
            * Les métadonnées d'instance GCE, qui permettront de définir des métadonnées qui apparaitront au niveau des instance de VM GCE et ne seront pas utilisables au niveau Kubernetes

    ![`GKE Parametre de Pool`](./gke_pool_parameters.png)

    * Les paramètres spécifiques du cluster
        * Automatisation, qui permet :
            * D'activer l'auto-scaling de POD
            * D'activer et configurer l'auto-scaling de noeuds en fonction de la charge CPU et de la consomation mémoire
            * D'activer et définir les jours et heures de maintenance (en cas de maintenance automatique)

        ![`GKE Parametre d'automatisation`](./gke_automatisation_parameters.png)

        * Mise en réseau, permettra de préciser les options suivantes :
            * La visibilité du Cluster
                * Cluster public, les noeuds ont une adresse IP publique par laquelle ils sont accessibles
                * Cluster Privé, les noeuds du cluster n'ont pas d'adresse IP publique et le noeud MASTER est par défaut inaccessible depuis l'extérieur. Dans ce cas vous pouvez préciser :
                    * Si le MASTER est accessible via une IP Externe ou uniquement par une VM se trouvant dans son sous-reseau
                    * Si le MASTER peut être accessible depuis toutes les regions GCP (Accès Mondial du MASTER)
            * Le réseau dans lequel les noeuds du cluster se trouveront. Ce paramètre permettra de déterminer les ressources GCE avec lesquelles les noeuds du cluster pourront communiquer
            * Le sous-réseau dans lequel les neouds du cluster se trouveront (Ce sous-réseau doit contenir au moins deux plages secondaires libres et inutilisées par un autre cluster afin d'éviter des intersections d'adresse)
            * La plage d'adresses des POD
            * Le nombre max de POD par noeud
            * La plage d'adresse des services
            * Activer ou non la visibilité intra-noeud, ce qui permet d'avoir accès aux traffics entre les noeuds (qui normalement est privé), et de le monitorer au niveau GCP grace aux outils de journalisation des flux VPC
            * Activation de l'équilibrage de charge HTTP, qui permettra d'utiliser l'équilibreur de charge GCP avec les Ingress Kubernetes (Ce qui permettra de bypasser l'utilisation de l'Ingress Controleur NGINX par exemple).
            * Activation des règles réseau, permettant à l'administrateur Kubernetes de définir les règles de communication inter-POD

        ![`GKE Parametre de mise en reseau`](./gke_network_parameters.png)

        * Sécurité, permettra de définir les options suivantes
            * Activation des noeudd protégés, permettant
                * de contrôler la provenance du système d'exploitation du noeud
                * de s'assurer que le système d'exploitation du noeud s'exécute sur une VM dans un centre de données Google
                * de lutter contre les rootkits et les bootkits
            * Activation du chiffrement des secrets au niveau du Datastore Kubernetes (ETCD, ou autres)
            * Activate Google Group pour RBAC, permettant ansi de construire une politique d'autorisation basé sur des utilisateurs G-Suite

        ![`GKE Parametre de securite`](./gke_security_parameters.png)

        * Métadonnées permettront de définir des labels CLE/VALEUR qui seront appliqués au Cluster dans GCE, vous permettant ainsi de'organiser vos cluster ainsi que leur ressources.

        ![`GKE Parametre de metadonnees`](./gke_metadatas_parameters.png)

        * Fonctionnalités, permettant de préciser des fonctionalités additionnelles à installer dans le cluster
            * ISTIO pour e Service Mesh
            * Kubernetes Engine Monitoring
            * Cloud TPU
            * Gestionnaire d'applications

    Une fois le cluster créé, on peut s'y connecter via gcloud

    * Récupérez la configuration d'accès (`kubectl`) au point de termisaison du Cluster et définir cette configuration comme celle à utiliser par défaut par `kubectl`
    ```
    gcloud container clusters get-credentials <CLUSTER_NAME> --zone <CLUSTER_ZONE> --project <PROJECT_NAME>
    ```
    * On peut maintenant exécuter des commandes `kubectl` qui attaqueront notre cluster
    ```
    kubectl get pods -n kube-system
    ```

2. Déploiement d'un cluster Kubernetes via Cloud Shell ou Cloud SDK

    * Le groupe de commande permettant d'interagir avec le sous-système GKE est `container`.
    * Le sous-groupe de commandes permettant de gérer le cycle de vie des cluster Kubernetes est `clusters`
    * Pour créer un cluster la commande est
    ```
    gcloud container clusters create NAME
            [--accelerator=[type=TYPE,[count=COUNT],...]]
            [--additional-zones=ZONE,[ZONE,...]] [--addons=[ADDON,...]] [--async]
            [--boot-disk-kms-key=BOOT_DISK_KMS_KEY]
            [--cluster-ipv4-cidr=CLUSTER_IPV4_CIDR]
            [--cluster-secondary-range-name=NAME]
            [--cluster-version=CLUSTER_VERSION]
            [--create-subnetwork=[KEY=VALUE,...]]
            [--database-encryption-key=DATABASE_ENCRYPTION_KEY]
            [--default-max-pods-per-node=DEFAULT_MAX_PODS_PER_NODE]
            [--disable-default-snat] [--disk-size=DISK_SIZE]
            [--disk-type=DISK_TYPE] [--enable-autorepair] [--no-enable-autoupgrade]
            [--enable-binauthz] [--enable-cloud-logging]
            [--enable-cloud-monitoring] [--enable-cloud-run-alpha]
            [--enable-intra-node-visibility] [--enable-ip-alias]
            [--enable-kubernetes-alpha] [--enable-legacy-authorization]
            [--enable-master-global-access] [--enable-network-policy]
            [--enable-shielded-nodes] [--enable-stackdriver-kubernetes]
            [--enable-vertical-pod-autoscaling] [--image-type=IMAGE_TYPE]
            [--issue-client-certificate] [--labels=[KEY=VALUE,...]]
            [--local-ssd-count=LOCAL_SSD_COUNT]
            [--machine-type=MACHINE_TYPE, -m MACHINE_TYPE]
            [--max-nodes-per-pool=MAX_NODES_PER_POOL]
            [--max-pods-per-node=MAX_PODS_PER_NODE]
            [--max-surge-upgrade=MAX_SURGE_UPGRADE]
            [--max-unavailable-upgrade=MAX_UNAVAILABLE_UPGRADE]
            [--metadata=KEY=VALUE,[KEY=VALUE,...]]
            [--metadata-from-file=KEY=LOCAL_FILE_PATH,[...]]
            [--min-cpu-platform=PLATFORM] [--network=NETWORK]
            [--node-labels=[NODE_LABEL,...]] [--node-locations=ZONE,[ZONE,...]]
            [--node-taints=[NODE_TAINT,...]] [--node-version=NODE_VERSION]
            [--num-nodes=NUM_NODES; default=3] [--preemptible]
            [--release-channel=CHANNEL] [--services-ipv4-cidr=CIDR]
            [--services-secondary-range-name=NAME]
            [--shielded-integrity-monitoring] [--shielded-secure-boot]
            [--subnetwork=SUBNETWORK] [--tags=TAG,[TAG,...]]
            [--workload-metadata=WORKLOAD_METADATA] [--workload-pool=WORKLOAD_POOL]
            [[--enable-autoprovisioning
              : --autoprovisioning-config-file=AUTOPROVISIONING_CONFIG_FILE
              | [--max-cpu=MAX_CPU --max-memory=MAX_MEMORY
              : --autoprovisioning-locations=ZONE,[ZONE,...] --min-cpu=MIN_CPU
              --min-memory=MIN_MEMORY
              --autoprovisioning-max-surge-upgrade=AUTOPROVISIONING_MAX_SURGE_UPGRADE --autoprovisioning-max-unavailable-upgrade=AUTOPROVISIONING_MAX_UNAVAILABLE_UPGRADE --autoprovisioning-scopes=[SCOPE,
              ...]
              --autoprovisioning-service-account=AUTOPROVISIONING_SERVICE_ACCOUNT
              --enable-autoprovisioning-autorepair
              --enable-autoprovisioning-autoupgrade
              [--max-accelerator=[type=TYPE,count=COUNT,...]
              : --min-accelerator=[type=TYPE,count=COUNT,...]]]]]
            [--enable-autoscaling --max-nodes=MAX_NODES --min-nodes=MIN_NODES]
            [--enable-master-authorized-networks
              --master-authorized-networks=NETWORK,[NETWORK,...]]
            [--enable-network-egress-metering
              --enable-resource-consumption-metering
              --resource-usage-bigquery-dataset=RESOURCE_USAGE_BIGQUERY_DATASET]
            [--enable-private-endpoint
              --enable-private-nodes --master-ipv4-cidr=MASTER_IPV4_CIDR]
            [--enable-tpu --tpu-ipv4-cidr=CIDR]
            [--maintenance-window=START_TIME | --maintenance-window-end=TIME_STAMP
              --maintenance-window-recurrence=RRULE
              --maintenance-window-start=TIME_STAMP]
            [--password=PASSWORD --enable-basic-auth | --username=USERNAME, -u USERNAME]
            [--region=REGION | --zone=ZONE, -z ZONE]
            [--reservation=RESERVATION --reservation-affinity=RESERVATION_AFFINITY]
            [--scopes=[SCOPE,...];
              default="gke-default" --service-account=SERVICE_ACCOUNT]
            [GCLOUD_WIDE_FLAG ...]
    ```

    * Une astuce afin de faciliter l'obtention de la bonne ligne de commande est de :
        * Se rendre sur Cloud Console et paramétrer notre Cluster via l'interface
        * Cliquer sur le lien `Ligne de commande` qui se trouve à côté du bouton `CREER`. Ce lien génèrera pour vous la ligne de commande correspondant à votre aramétrage. Exemple
    ```
    gcloud beta container --project "kubecloud-gke-training" clusters create "cluster-demo-01" --zone "southamerica-east1-c" --no-enable-basic-auth --cluster-version "1.15.12-gke.2" --machine-type "n1-standard-4" --image-type "UBUNTU" --disk-type "pd-ssd" --disk-size "100" --node-labels kubecloud.demo=true,kubecloud.training=true --metadata disable-legacy-endpoints=true --scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" --max-pods-per-node "110" --preemptible --num-nodes "4" --enable-stackdriver-kubernetes --enable-private-nodes --master-ipv4-cidr "172.16.0.0/28" --enable-ip-alias --network "projects/kubecloud-gke-training/global/networks/default" --subnetwork "projects/kubecloud-gke-training/regions/southamerica-east1/subnetworks/default" --default-max-pods-per-node "110" --enable-autoscaling --min-nodes "0" --max-nodes "4" --no-enable-master-authorized-networks --addons HorizontalPodAutoscaling,HttpLoadBalancing --enable-autoupgrade --enable-autorepair --max-surge-upgrade 1 --max-unavailable-upgrade 0 --enable-vertical-pod-autoscaling
    ```

## Déploiement de PODS sur un cluster Kubernetes

Pour déployer une Application sur un cluster GKE, on peut

1. Passer par la console web`Cloud Console`
    
    * Allez sur la page principale des charges de travail

    ![`GKE Deploy Main Page`](./gke_deploy_app_main_page.png)

    * Cliquez sur le boutin de création (ou d'ajout) d'un déploiement qui qffichera la page des paramètrage de conteneurs et remplissez les champs attendus
        * Image à déployer
        * Variable d'environnement
        * Commande d'entry point

    ![`GKE Deploy Container Page`](./gke_deploy_app_container_params.png)

    * Ensuite cliquez sur le bouton "`continuer`" afin de passer à la page de configuration du déploiement et remplissez les informations attendues
        * Nom du déploiement
        * l'espace de nommage cible du déploiement
        * Les labels à associer au déploiement
        * Le cluster sur lequel déployer

    ![`GKE Deploy Configuration Page`](./gke_deploy_app_configuration_params.png)

2. Passer par la ligne de comandes `kubectl` permettant de déployer et scaler un POD

    * kubectl run nginx-02 --image=nginx:latest --port=8080 (Qui permet de créer un POD exécutant nginx)
    * kubectl apply -f nginx.yml (nginx.yml) représentant le fichier de description du déploiement de l'image nginx

## Monitoring d'un cluster Kubernetes

StackDriver est le service GCP permettant le motitoring, le logging et l'alerting. Il peut être utilisé afin de monitorer un cluster GKE. Si vous souhaitez monitorer vos cluster avec StackDriver, assurez-vous d'activer cette option lors de la création du cluster

## Résumé

## Quelques questions d'examen

## Revue des questions
