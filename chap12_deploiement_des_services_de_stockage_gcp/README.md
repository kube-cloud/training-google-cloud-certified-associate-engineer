# Déploiement de services de stockage GCP

## Déploiement et gestion de Cloud SQL

Cette section vous permettra d'apprendre à :

* Créer des instances de services de bases de données
* Se connecter aux instances de services de bases de données
* Créer des bases de données dans les instances
* Charger des données dans les bases de données
* Requêter des données depuis les bases de données
* Sauvegarder les données des base de données

Nous travaillerons avec `MySQL`, sachant que toutes ces opérations sont valables sur `Postgres` et `SQL Server`, mis à part les instructions spécifiques à ces serveurs et qui ne sont pas couvert par Google. (Pour apprendre spécifiquement MySQL, Postgres ou SQL Server, il faudra obtenir un livre traitant spécialement de ces sujets)

1. Création et connexion à une instance MySQL

    * Création d'une instance `Cloud SQL - MySQL`
    ```
    gcloud sql instances create demo-cloudsql-mysql-chap12 --assign-ip --authorized-networks=82.64.160.93 --availability-type=zonal --database-version=MYSQL_8_0 --root-password=P@ssW0rd --storage-type=SSD --zone=us-central1-a --tier=db-n1-standard-1
    ```
    NB : Notez le paramètre `--authorized-networks=82.64.160.93` qui permet d'autoriser l'adresse distance `82.64.160.93 ` à se connecter à l'instance `Cloud SQL` ainsi que le paramètre `--assign-ip` qui permet d'exposer l'instance `Cloud SQL` pour des appels internet. Grâce à ces deux paramètres, il est donc possible d'accéder à votre instance depuis un laptop ayant cette adresse.

    * Accès à l'instance depuis Ckoud SDK
    ```
    gcloud sql connect --project=kubecloud-gke-training demo-cloudsql-mysql-chap12 -u=root
    ```

    * Accès à l'instance depuis un client MySQL
    ```
    mysql -u root -h <INSTANCE_IP> -p
    ```
2. création d'une base de données, chargement et requêtage de données

    * Créez et utilisez une base de données dans l'instance
    ```
    CREATE DATABASE ace_exam_book;
    USE ace_exam_book;
    ```

    * Créez une table dans la base de données et insérer deux lignes
    ```
    CREATE TABLE books (title VARCHAR(255), num_chapters INT, entity_id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY (entity_id));
    INSERT INTO books (title,num_chapters) VALUES ('ACE Exam Study Guide', 18);
    INSERT INTO books (title,num_chapters) VALUES ('Architecture Exam Study Guide', 18);
    ```

    * Sélectionnez les données de la table
    ```
    SELECT * from books;
    ```
    
3. Sauvegarde de l'instance MySQL dans Cloud SQL

    Vous pouvez créer une sauvegarde via la console

    * Étant dans la page de votre instance `Cloud SQL`, allez sur le menu `Sauvegardes` et cliquez sur `Créer une Sauvegarde`

    ![`Cloud SQL Instance main page`](./cloud-sql-instance-main-page.png)

    * Dans la fenêtre, saissez la description facultative, ainsi que le chois de l'emplacement et validez

    ![`Cloud SQL Instance backup page`](./cloud-sql-instance-backup-page.png)

    * Dans la fenêtre principale de l'instance, la sauvegarde est créée avec un bouton de restauration.

    ![`Cloud SQL Instance main page backup`](./cloud-sql-instance-main-page-with-backup.png)

    * Dans l'image précédente, remarquez aussi la zone (blueu) de paramétrage des sauvegardes automatiques. Avec le bouton modifier, vous pouvez ouvrir la fenêtre de configuration es sauvegardes automatiques.

    ![`Cloud SQL Instance automatic backup form`](./cloud-sql-instance-automatic-backup-form.png)

    Vous pouvez aussi créer des sauvegardes en ligne de commande

    ```
    gcloud sql backups create --instance=INSTANCE[, -i INSTANCE] [--async]
        [--description=DESCRIPTION] [--location=LOCATION] [GCLOUD_WIDE_FLAG ...]
    ```

    Vous pouvez activer la sauvegarde automatique en ligne de commande en utilisant la commande de modification d'une instance (`patch`) pour modifier les paramètres liés à la sauvegarde
    
    ```
    gcloud sql instances patch demo-cloudsql-mysql-chap12 --backup-start-time=01:00 --backup-location=us-east1
    ```

    Vous pouvez suppromer une instance via la commande
    ```
    gcloud sql instances delete INSTANCE_NAME --project=PROJECT_ID
    ```
    
## Déploiement et gestion de Datastore

1. Ajout de données dans une base de données Datastore

    * Créer une entité (Structure de données équivalente à une table relationnelle). Vous pouvez créer une instance de l'entité en même temps que la structure elle-même.

    ![`Cloud Datastore Entity form`](./cloud-datastore-entity-form.png)

    * Une fois la structure et l'instance créées, vous pouvez requêter les documents (instances), en utilisant le langage GQL (Google Query Language)

    ![`Cloud Datastore Query form`](./cloud-datastore-query-form.png)

2. Sauvegarde d'une base de données Datastore

    Pour sauvegarder les données `Cloud Datastore`, vous devez :

    * Créer un `BUCKET` dans lequel les données `Cloud Datastore` seront stockées
        * `gsutil mb -c STORAGE_CLASS -p PROJECT_ID -l REGION gs://BUCKET_NAME`
        * `gsutil mb -c standard -p kubecloud-gke-training -l europe-west3 gs://demo-cloud-storage-01`
    * Donner les droits spécifiques aux utilisateurs qui vont exécuter les sauvegardes
        * Les utilisateurs auront besoin de la permission : `datastore.databases.export`
    * Les utilisateurs ayant les droit d'export peuvent désormais effectuer l'export via la commande :
        * `gcloud datastore export --project=SOURCE_PROJECT_ID -namespaces='NS_TO_EXPORT1[,NS_TO_EXPORT2,...]' [--async] [--kind=KIND_TO_EXPORT1[,KIND_TO_EXPORT2,...]] gs://TARGET_BUCKET`
        * `gcloud datastore export --project=kubecloud-gke-training --namespaces='default' gs://demo-cloud-storage-01`
        * Attention, assurez-vous que la localisation du projet et celle du bucket soit cohérente. Pour cela fixez votre projet soit par `--project` ou par `gcloud config set project`
    * Pour importer les données depuis `Cloud Storage`
        * `gcloud datastore import --project=kubecloud-gke-training gs://demo-cloud-storage-01/2020-10-26T20:09:49_61480/2020-10-26T20:09:49_61480.overall_export_metadata`

## Déploiement et gestion de BigQuery

`BigQuery` est un service de base de données managé et de ce fait, GCP s'occupe des tâches comme la sauvegarde et autres tâches administratives. En tant qu'ingenieur Cloud, vous devrez donc vous concentrer sur deux tâches essentielles.

1. Estimation du coût (en data) des requêtes BigQuery

    * Saisissez par exemple cette requête, qui charge les nom, prenom et decompte depuis une table publique de google. On verra que `BigQuery` nous fournira une estimation de la quantité de données qui sera ramenée par cette requête
    ```
    select name, gender, sum(number) as total
    from bigquery-public-data.usa_names.usa_1910_2013
    group by name, gender
    order by total desc
    limit 10
    ```
    * Il est aussi possible d'estimer le coût d'une requête via la commande 
    ```
    bq --location=[LOCATION] query --use_legacy_sql=false --dry_run [SQL_QUERY]
    ```
    * `--location` permet de préciser la localisation de l'ensemble de données requêté. Dans notre cas, nous exploitons l'ensemble de données d'un projet publique de GCP nommé `bigquery-public-data` et visible via l'URL `https://console.cloud.google.com/bigquery?project=bigquery-public-data&p=bigquery-public-data&d=usa_names&page=dataset`. En visualisant son Dataset `usa_names`, on voit que la localisation du stockage est `US`. Nous pouvons donc utiliser la requête :
    ```
    bq --location=US --project_id=kubecloud-gke-training query --use_legacy_sql=false --dry_run 'select name, gender, sum(number) as total from bigquery-public-data.usa_names.usa_1910_2013 group by name, gender order by total desc limit 10'
    ```
    * Disposant de cette estimation de volume, vous pouvez donc utiliser le simulateur de prix pour estimer le coût de cette requète. L'adresse de ce simulateur : `https://cloud.google.com/products/calculator/`

    ![`Cloud Bigquery pricing estimation`](./cloud-pricing-estimation.png)

2. Visualisation des Jobs BigQuery

    * Un job `BigQuery` est un processus permettant de charger, exporter, copier ou requêter les données d'un Dataset `BigQuery`
    * C'est une action que `BigQuery` exécute en votre nom pour charger, exporter, interoger ou copier les données.
    * Chaque fois que nous exécutons une requête via la console web ou l'utilitaire `bq`, une ressource de tâche est automatiquement crée, plannifiée et exécutée.

## Déploiement et gestion de Cloud Spanner

1. Création d'une instance `Cloud Spanner`

La requête de création est sous la forme suivante :
```
gcloud spanner instances create INSTANCE_ID --description='INSTANCE DESCRIPTION' --config=INSTANCE_CONFIG_LOCATION --nodes=INSTANCE_NODES_NUMBER
```

* INSTANCE_CONFIG_LOCATION, peut être obtenu grâce à la commande : `gcloud spanner instance-configs list`

Exemple concret :

```
gcloud spanner instances create demo-cloud-spanner-instance-01 --description='Demo Cloud Spanner 01' --config=regional-asia-east1 --nodes=3
```

2. Création d'une base de données et des tables `Cloud Spanner`

La commande suivante permet de créer une base de données dans une instance et d'exécuter des instructions DDL pour créer des tables
```
gcloud spanner databases create (DATABASE : --instance=INSTANCE) [--async] [--ddl=DDL] [--ddl-file=DDL_FILE] [GCLOUD_WIDE_FLAG ...]
```

En concret
```
gcloud spanner databases create demo-cloud-spanner-db-01 --instance=demo-cloud-spanner-instance-01 --ddl="$(cat ./spanner-demo-database-schema-01.spql)"
```

La syntaxe DDL complete se trouve ici : [`Spanner DDL`](https://cloud.google.com/spanner/docs/data-definition-language)

3. Insertion d'une ligne dans une table `Cloud Spanner`

la commande suivante permet d'exécuter du SQL sur une base de donnée `Cloud Spanner`

```
gcloud spanner databases execute-sql (DATABASE : --instance=INSTANCE) --sql=SQL [--enable-partitioned-dml]
    [--query-mode=QUERY_MODE; default="NORMAL"] [--timeout=TIMEOUT; default="10m"] [GCLOUD_WIDE_FLAG ...]
```

En réel

```
gcloud spanner databases execute-sql demo-cloud-spanner-db-01 --instance=demo-cloud-spanner-instance-01 --sql="INSERT Singers (SingerId,  BirthDate, FirstName, LastName,  LastUpdated) VALUES (1, '1981-03-28', 'Marc', 'Richards', '2020-10-27')"
```

4. Requêtage des données d'une table `Cloud Spanner`

Les requêtes de select classiques ont applicables.

## Déploiement et gestion de Cloud Pub/Sub

Afin de pouvoir poster des messages dans des files `Cloud Pub/Sub`, il y a deux étapes à respecter :

1. Créer des topics, qui sont des structure dans lesquelles les messages sont postés. `Cloud Pub/Sub` va recevoir des messages via les topics et les stocker jusqu'à ce qu'ils soient consommés par les applications.

La comande permettant de créer des topics est la suivante :
```
gcloud pubsub topics create TOPIC [TOPIC ...] [--labels=[KEY=VALUE,...]]
    [--message-storage-policy-allowed-regions=[REGION,...]]
    [--topic-encryption-key=TOPIC_ENCRYPTION_KEY
    : --topic-encryption-key-keyring=TOPIC_ENCRYPTION_KEY_KEYRING
    --topic-encryption-key-location=TOPIC_ENCRYPTION_KEY_LOCATION
    --topic-encryption-key-project=TOPIC_ENCRYPTION_KEY_PROJECT]
    [GCLOUD_WIDE_FLAG ...]
```

Pour notre exemple : `gcloud pubsub topics create demo-topic-01 demo-topic-02`

2. Créer des souscriptions, qui permettent de consommer les messages stocgés dans les files

Les souscriptions représentent les abonnement d'un  consommateur sur les messages arrivant dans un topic donné. Pour les créer il faudra préciser un certain nombre d'informations, entre autres :

* L'identifiant de la souscription
* L'identifiant du topic cible
* L'identifiant du projet contenant le topic
* La durée de rétention des messages dans la souscription
* La période d'expiration des messages

La commande est la suivante :

```
gcloud pubsub subscriptions create SUBSCRIPTION [SUBSCRIPTION ...]
        (--topic=TOPIC : --topic-project=TOPIC_PROJECT)
        [--ack-deadline=ACK_DEADLINE] [--enable-message-ordering]
        [--expiration-period=EXPIRATION_PERIOD] [--labels=[KEY=VALUE,...]]
        [--message-retention-duration=MESSAGE_RETENTION_DURATION]
        [--push-auth-service-account=SERVICE_ACCOUNT_EMAIL]
        [--push-auth-token-audience=OPTIONAL_AUDIENCE_OVERRIDE]
        [--push-endpoint=PUSH_ENDPOINT] [--retain-acked-messages]
        [--max-delivery-attempts=MAX_DELIVERY_ATTEMPTS
          [--dead-letter-topic=DEAD_LETTER_TOPIC
          : --dead-letter-topic-project=DEAD_LETTER_TOPIC_PROJECT]]
        [--max-retry-delay=MAX_RETRY_DELAY --min-retry-delay=MIN_RETRY_DELAY]
        [GCLOUD_WIDE_FLAG ...]
```

* `SUBSCRIPTION1 [SUBSCRIPTION2...SUBSCRIPTIONX]` sont les noms des souscriptions à créer
* `--topic=TOPIC` est l'identifiant du topic cible
* `--topic-project=TOPIC_PROJECT` est l'identifiant du projet contenant le topic cible
* `--ack-deadline=ACK_DEADLINE` est le temps pendant lequel le broker de message attendra l'accusé de réception du message par le consommateur avant de procéder à un renvoi
* `--enable-message-ordering` permettra de s'assurer que les message ayant la même `CLÉ D'ORDRE` seront envoyés à la souscription dans le même ordre que celui dans lequel ils ont été reçu par le broker
* `--expiration-period=EXPIRATION_PERIOD` permet de préciser la durée d'inactivité après laquelle la souscription expire. La valeur est un entier suivi d'une unité (`s`, `m`, `h`, `d`)
* `--message-retention-duration=MESSAGE_RETENTION_DURATION` permet de préciser la durée de rétention des messages non consommés dans la souscription (durée relative à l'instant de publication). Par défaut 7 jours. La valeur est un entier suivi d'une unité (`s`, `m`, `h`, `d`)
* `--retain-acked-messages` permet d'activer la rétention des messages déjà consommés
* `--push-endpoint=PUSH_ENDPOINT` permet de préciser un endpoint vers lequel les message de cette souscription seront postés
* `--max-delivery-attempts=MAX_DELIVERY_ATTEMPTS` permet de préciser le nombre maximum de tentatives de renvoi du message en cas de non réception, avant que celui-ci ne soit envoyé dans la DLT (Dead Letter Topic) spécifiée par `--dead-letter-topic`. Par défaut 5, la valeur devra être entre 5 et 100
* `--dead-letter-topic=DEAD_LETTER_TOPIC` permet de préciser le topic qui recevra les message n'ayant pas pu être délivré après un certain nombre de tentatives
* `--dead-letter-topic-project=DEAD_LETTER_TOPIC_PROJECT` permett de préciser le projet contenant le topic de lettre mortes
* `--max-retry-delay=MAX_RETRY_DELAY` permet de préciser la durée max entre deux tentatives de renvoi. La valeur est un entier suivi d'une unité (`s`, `m`, `h`, `d`)
* `--min-retry-delay=MIN_RETRY_DELAY` permet de préciser la durée min entre deux tentatives de renvoi. La valeur est un entier suivi d'une unité (`s`, `m`, `h`, `d`)

Pour notre projet

```
gcloud pubsub subscriptions create subscription-01 --topic=demo-topic-01 --topic-project=kubecloud-gke-training
```

## Déploiement et gestion de Cloud BigTable

En tant que ingénieur Cloud, votre rôle est de pouvoir :

* Créer une instance `Bigtable` pouvant contenir plusieurs clusters

    ```
    gcloud bigtable instances create INSTANCE --cluster=CLUSTER
        --cluster-zone=CLUSTER_ZONE --display-name=DISPLAY_NAME [--async]
        [--cluster-num-nodes=CLUSTER_NUM_NODES]
        [--cluster-storage-type=CLUSTER_STORAGE_TYPE; default="ssd"]
        [--instance-type=INSTANCE_TYPE; default="PRODUCTION"]
        [GCLOUD_WIDE_FLAG ...]
    ```
    * `INSTANCE` représente l'identifiant unique de l'instance
    * `CLUSTER` (obligatoire) représente l'identifiant du cluster à créer dans l'instance
    * `CLUSTER_ZONE` (Obligatoire) représente la zone dans laquelle seront crées les noeud du cluster
    * `DISPLAY_NAME` (Obligatoire) représente le nom publique de l'instance `Bigtable`
    * `CLUSTER_NUM_NODES` permet de préciser le nombre de noeuds du cluster à créer (`à ne pas utiliser pour des instances de DEVELOPPEMENT`)
    * `CLUSTER_STORAGE_TYPE` permet de préciser le type de stockage à utiliser pour les noeuds du cluster (par défaut : `ssd`)
    * `INSTANCE_TYPE` permet de préciser si c'est une instance de PROD ou de DEV. Les valeurs possibles
        * `PRODUCTION` ce type d'instance dispose de cluster avec au minimum 3 noeuds et supporte la haute disponibilité.
        * `DEVELOPMENT` ce type d'instances sont moins cher du fait qu'elles sont utilisées pour le développement et les tests. Elle ne proposent pas de haute disponibilité ni de SLA

    ### Pour notre exemple
    ```
    gcloud bigtable instances create demo-bigtable-instance-dev-01 --display-name="Demo BigTable DEV Instance 01" --instance-type=DEVELOPMENT --cluster=demo-bigtable-cluster-dev-01 --cluster-zone=us-east1-c --cluster-storage-type=ssd
    ```
    ```
    gcloud bigtable instances create demo-bigtable-instance-prod-01 --display-name="Demo BigTable PROD Instance 01" --instance-type=PRODUCTION --cluster=demo-bigtable-cluster-prod-01 --cluster-zone=us-east1-c --cluster-storage-type=ssd --cluster-num-nodes=3
    ```

* Lister les instances `Bigtable`
    ```
    gcloud bigtable instances list [--filter=EXPRESSION] [--limit=LIMIT]
        [--page-size=PAGE_SIZE] [--sort-by=[FIELD,...]] [--uri]
        [GCLOUD_WIDE_FLAG ...]
    ```
    * `--filter=EXPRESSION` permet de spécifier une expression de filtre de recherche sur les champs des clusters. des exemples peuvent être obtenu via la commande `gcloud topic filters`
    * `--limit=LIMIT` permet de préciser le nombre maximal de ressource à lister
    * `--sort-by=[FIELD,...]` permet de spécifier des colonne de tri (parmis les champs de description d'un cluster. On peut obtenir ces champs via l'affichage au format JSON `--format=json`)

* Supprimer des instances `Bigtable`

    ```
    gcloud bigtable instances delete INSTANCE [INSTANCE ...] [GCLOUD_WIDE_FLAG ...]
    ```
    * `INSTANCE [INSTANCE ...]` représente la liste identifiants d'instances à supprimer

* Rajouter de nouveaux clusters pour une instance `Bigtable`

    ```
    gcloud bigtable clusters create (CLUSTER : --instance=INSTANCE) --zone=ZONE [--async]
        [--num-nodes=NUM_NODES; default=3]
    ```
    * `CLUSTER` représente l'identifiant du cluster à créer
    * `--instance=INSTANCE` permet de préciser l'instance à laquelle rattacher ce cluster
    * `--zone=ZONE` permet de spécifier la zone du cluster
    * `--num-nodes=NUM_NODES` permet de spécifier le nombre de noeuds du cluster (3 par défaut)

    À ne pas utiliser sur les instances de DEVELOPPEMENT qui ne supportent pas la haute disponibilité. De plus, il est impossible de créer plusieurs cluster dans la même zone.
    Pour notre exemple :
    ```
    gcloud bigtable clusters create demo-bigtable-cluster-prod-02 --instance=demo-bigtable-instance-prod-01 --zone=us-west1-a --num-nodes=3
    ```
    
* Lister les clusters `Bigtable`

    ```
    gcloud bigtable clusters list --instances=[INSTANCES,...]
        [--filter=EXPRESSION] [--limit=LIMIT] [--page-size=PAGE_SIZE]
        [--sort-by=[FIELD,...]] [--uri] [GCLOUD_WIDE_FLAG ...]
    ```
    * `--instances=[INSTANCES,...]` permettra de spécifier la liste des instances dont on veut afficher les clusters (On peut obtenir ces champs via l'affichage au format JSON `--format=json`)
    * `--filter=EXPRESSION` permet de spécifier une expression de filtre de recherche sur les champs des clusters. des exemples peuvent être obtenu via la commande `gcloud topic filters`
    * `--limit=LIMIT` permet de préciser le nombre maximal de ressource à lister
    * `--sort-by=[FIELD,...]` permet de spécifier des colonne de tri (parmis les champs de description d'un cluster. On peut obtenir ces champs via l'affichage au format JSON `--format=json`)
    

* Supprimer des clusters `Bigtable`

    ```
    gcloud bigtable clusters delete CLUSTER --instance=INSTANCE [GCLOUD_WIDE_FLAG ...]
    ```
    * `CLUSTER` représent l'identifiant du cluster à supprimer
    * `--instance=INSTANCE` permet de spécifier l'instance parete du cluster
    
* Modifier le nombre de noeuds d'un cluster `Bigtable`

    ```
    gcloud bigtable clusters update CLUSTER --instance=INSTANCE --num-nodes=NUM_NODES [--async]
    [GCLOUD_WIDE_FLAG ...]
    ```
    * `CLUSTER` représent l'identifiant du cluster à supprimer
    * `--instance=INSTANCE` permet de spécifier l'instance parete du cluster
    * `--num-nodes=NUM_NODES` permet de spécifier le nombre de noeud du cluster


### NB : Google Cloud fournit un outils [`cbt`] en ligne de commande permettant d'effectuer des actions sur les tables d'une instance `Bigtable`.

### Pour l'installer vous avez la commande : `gcloud components update && gcloud components install cbt`

### L'outil `cbt` nécessite une variable d'environnement nommée [`instance`] permettant de préciser l'instance sur laquelle vous travaillez. Cette variable d'environnement devra être définie dans un fichier à la racine de l'utilisateur système : `~/.cbtrc`. Si cette instance n'est pas précisée dans le fichier .cbtrc, elle devra être renseignée via le paramètre `-instance` de la commande `cbt`

### L'outil cbt nécessite aussi une variable nommée [`project`] permettant de préciser le projet sur lequel vous travaillez. Cette variable pourra être définie dans le fichier ~/.cbtrc comme la première variable ou via la ligne de commande [`-project`]

### L'outil `cbt` nécessite aussi une variable nommée `creds` qui représente le chemin vers le fichier de clé du compte utilisateur google JSON. Cette variable pourra être définie dans le fichier ~/.cbtrc comme la première variable ou via la ligne de commande [`-creds`]

### Pour notre exemple nous initialiserons comme suit : `echo instance = demo-bigtable-instance-dev-01 >> ~/.cbtrc`

### La liste des commandes `cbt` peuvent être trouvées [`Documentation CBT`](https://cloud.google.com/bigtable/docs/cbt-reference?hl=fr)

* Créer et lister des tables
    * `cbt -instance demo-bigtable-instance-dev-01 -project kubecloud-gke-training createtable demo-bigtable-table-dev-01`
    * `cbt ls`
* Créer une famille de colonnes
    * `cbt createfamily demo-bigtable-table-dev-01 colfam01`
* Insérer des données dans les tables
    * Insérer une valeur dans une cellule ligne `row1`, colonne nommée `col1` de la famille de colonnes `colfam01` : `cbt set demo-bigtable-table-dev-01 row1 colfam01:col1=MyFirstValue`
* Requêter ces données

    * Lire toute la table : `cbt read demo-bigtable-table-dev-01`

## Déploiement et gestion de Cloud Dataproc

* `Cloud Dataproc` est un service managé Apache Spark et Apache Hadoop proposé par GCP.
* `Apache Spark` est une base de données NoSQL qui supporte l'analyse de données
* `Apache Hadoop` est un Framework de stockage et de traiment de gros volume de données

Votre rôle en tant que ingénieur Cloud sera de pouvoir créer des Cluster Dataproc et leur soumettre des travaux de calcul à exécuter.

* Créer un cluster `Dataproc`
    * Allez sur la barre de menu et clisquez sur le menu `Dataproc`

    ![`Cloud Dataproc menu`](./cloud-dataproc-menu.png)

    * Sur la page principale de `Dataproc`, cliquez sur le bouton de création d'un cluster

    ![`Cloud Dataproc main page`](./cloud-dataproc-main-page.png)

    * Remplissez le formulaire de création de cluster : configuration du cluster
        * Le nom du cluster
        * La zone de stockage des noeuds du cluster
        * Le type de cluster
            * `Standard`, constitué d'un noeud maître et plusieurs Slaves
            * `One Node`, constitué d'un noeud maître et pas de Slave
            * `Haute disponibilité`, constitué de 3 noeuds maître et plusieurs Slaves
        * Les composants additionnels à installer

    ![`Cloud Dataproc form 01`](./cloud-dataproc-form-01.png)
    ![`Cloud Dataproc form 02`](./cloud-dataproc-form-02.png)

    * Remplissez le formulaire de création de cluster : configuration du noeuds
        * Le type de machine pour les noeuds maître
        * Le type de machine pour les noeuds Slave
        * La configuration des noeuds secondaires

    ![`Cloud Dataproc form 03`](./cloud-dataproc-form-03.png)
    ![`Cloud Dataproc form 04`](./cloud-dataproc-form-04.png)
    ![`Cloud Dataproc form 05`](./cloud-dataproc-form-05.png)

* Soumettre des tâches au cluster
    * Remplissez le formulaire de configuration de la tâche
        * Nom de la tâche
        * Le type de tâche (Hadoop, Spark, Hive, Pig, SparkR, etc..)
        * Soit le nom de la classe principale soit le fichier d'archive JAVA à utiliser (et contenant la classe principale)
        * La liste de fichier s'archive JAR à rajouter dans le Classpath
        * La liste d'argument de la ligne de commandes JAVA
        * Le nombre maximal de redémarrage par heure en cas d'echec
        * Si vous utilisez PySpark, vous devez fournir une archive Python. Si vous utilisez SparkR, vous devrez fournir des archive R. Si vous utilisez Hive ou SparkSQL, vous devrez fournir des fichiers de requêtes.

    ![`Cloud Dataproc task form 01`](./cloud-dataproc-task-form-01.png)

    * Voici un fichier python exemple stocké et partagé par Google pour les tests
        * `gsutil cat gs://dataproc-examples/pyspark/hello-world/hello-world.py`
        * Démarrez un Job PySpark pour ce fichier python
        ```
        gcloud dataproc jobs submit pyspark gs://dataproc-examples/pyspark/hello-world/hello-world.py --cluster=demo-dataproc-cluster-01 --region=us-central1
        ```

    * Vous pouvez ensuite supprimer le cluster `Dataproc`
    ```
    gcloud dataproc clusters delete CLUSTER --region=REGION
    ```
    
## Gestion de Cloud Storage

* Lorsque vous utilisez `Cloud Storage`, vous avez la possibilité de définir une stratégie de cycle de vie permettant de modifier automatiquement la classe ddes objets d'un bucket en fonction du temps de stockage

Vous pouvez aussi le faire à la demande soit par la console web, soit via la commande
```
gsutil rewrite -s NEW_STORAGE_CLASS gs://PATH_TO_THE_OBJECT
```

* Vous pouvez aussi avoir besoin de déplacer un objet d'un Bucket à un autre
```
gsutil mv gs://SRC_BUCKET/OLD_PATH_TO_OBJECT gs://DST_BUCKET/NEW_PATH_TO_OBJECT
```

## Résumé

## Quelques questions d'examen

## Revue des questions
