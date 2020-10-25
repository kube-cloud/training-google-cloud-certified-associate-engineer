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

## Déploiement et gestion de Datastore

1. Ajout de données dans une base de données Datastore
2. Sauvegarde d'une base de données Datastore

## Déploiement et gestion de BigQuery

1. Estimation du coût des requêtes BigQuery
2. Visualisation des Jobs BigQuery

## Déploiement et gestion de Cloud Spanner

## Déploiement et gestion de Cloud Pub/Sub

## Déploiement et gestion de Cloud BigTable

## Déploiement et gestion de Cloud Dataproc

## Gestion de Cloud Storage

## Résumé

## Quelques questions d'examen

## Revue des questions
