# Quelques questions d'examen

1. Quelle est le composant de base du cloud computing ?
    1. Serveur Physique
    2. __***`Machine Virtuelle`***__
    3. Block
    4. Sous réseau

2. Dans un cluster managé, qu'est ce qui est géré pour vous par le fournisseur de Cloud ?
    1. Monitoring
    2. Réseau
    3. Certaines tâches de sécurité
    4. __***`Tous ce qui précède`***__

3. De quel type de resources GCP avez-vous besoin pour traiter des fichier et exposer des backends en `serverless`
    1. Google Kubernetes Engine et Google Compute Engine
    2. __***`Google AppEngine et Google Cloud Functions`***__
    3. Google Cloud Functions et Google Compute Engine
    4. Google Cloud Functions et Google Kubernetes Engine

4. Quel type de stockage choisiriez vous dans le cadre de la conception d'un système permettant de stocker de larges fichiers pouvant être traité par des services tiers ? Sachant que des fonctionalités de Systèmes de fichier ne sont pas requises.
    1. Stockage Block
    2. __***`Stockage Objet`***__
    3. Stockage Fichier
    4. Cache

5. Quelle est la taille des blocks des systèmes de stoakeges Block
    1. 4KB
    2. 8KB
    3. 16KB
    4. __***`Taille de block variable`***__

6. Quel outil de sécurité utiliseriez-vous pour contrôler le traffic entre vos sous-réseau VPC ?
    1. Gestionnaire des identtés et des accès
    2. Routeurs
    3. __***`Parefeu`***__
    4. Table des adresses IP

7. Quel type de servers choisiriez-vous pour gérer les ressources nécessaire à la mise en place du service `Machine Learning` pour analyser un texte contenu dans une image ?
    1. Machine Virtuelles
    2. Clusters de Machine Virtuelles
    3. __***`Aucun serveur (les services spécialisés sont Serverless)`***__
    4. machines virtuelle Linux uniquement

8. l'investissement dans l'acquisition de serveur physique peut marcher lorsqu'une entreprise ?
    1. Démarre
    2. __***`Peut prédire exactement le nombre de serveur nécessaire à la fourniture de son service`***__
    3. A un budget informatique fixé
    4. A un budget informatique variable

9. Quel est le facteur déterminant du coût d'un serveur par minute ?
    1. La période de la journée dans laquelle le serveur est en exécution
    2. __***`Les caractéristiques du serveur`***__
    3. Le type d'application que vous exécutez dans le serveur
    4. Aucune des propositions précédentes

10. De combien de serveur auriez-vous besoin afin d'utiliser le service `Google Cloud Vision` pour analyser 5,000 images par heure ?
    1. 1
    2. 10
    3. 20
    4. __***`Aucune (Cloud Vision est un service spécialisé et donc Serverless)`***__

11. Quel modèle de déploiement choisiriez-vous pour exécuter un grand nombre de services support à votre application ?
    1. Très grande Machine virtuelle
    2. __***`Conteneurs dans un Cluster Managé`***__
    3. 2 grandes Machines Virtuelle, avec une en Lecture-seule
    4. Démarrage avec une petite machine virtuelle et augmentation de ses ressources en fonction de la charge

12. Quel type d'op´tation d'administration pouvez-vous effectuer sur une machine virtuelle que vous avez créee ?
    1. Configuration du système de fichier
    2. Patch du système d'exploitation
    3. Modification des permissions sur les fichiers et les répertoires
    4. __***`Tout ce qui précède`***__

13. `Google Cloud Filestore` est un service basé sur
    1. __***`Network File System (NFS)`***__
    2. XFS
    3. EXT4
    4. ReiserFS

14. Comment sont traitées les ressources réseau, lorsque vous configurez un réseau GCP ?
    1. __***`Virtual Private Cloud`***__
    2. Subdomain
    3. Cluster
    4. Aucune des propositions précédentes

15. Comment les caches affectent-ils le chargement des données ?
    1. Ils améliorent l'exécution du code Javascript client
    2. Ils stockent les données de manière permanente, même en cas d'arrêt du serveur
    3. Ils peuvent être désynchronisés par rapport à la source de données originale
    4. __***`Ils réduisent la latence du fait de meilleures performaces d'accès que les disque SSD ou HDD`***__

16. Pourquoi les fournisseurs de cloud offrent-ils l'allocation élastique de ressources ?
    1. Pour pouvoir retirer des ressources aux utilisateur moins prioritaires et les affecter aux plus prioritaires
    2. Les ressour_______________________
    3. Pour facturer plus
    4. Ils n'offrent pas d'allocation elastiques de ressources

17. Quelle est la caractéristique qui ne peut être attribuée à un service spécialisé GCP
    1. Ils sont `Serverless`
    2. Ils fournissent des fonctions spécifiques
    3. __***`Ils nécessitent d'être monitorés par l'utilisateur`***__
    4. Ils exposent une API permettant d'accéder au fonctionnalités du service offert

18. Quel est le type de stockage fournit par un lecteur attaché à une machine virtuelle et permettant un accès aléatoire à une portion d'un fichier ?
    1. Stockage Objet
    2. __***`Stockage Block`***__
    3. Stockage NoSQL
    4. Stockage SSD

19. Queltype de stockage utiliseriez-vous pour le stockage des fichiers d'une base de données relationnelle ?
    1. Stockage Objet
    2. Stockage Data
    3. __***`Stockage Block`***__
    4. Cache

20. Pour quelle raison recommenderiez-vous l'association `Cloud Storage`, `AppEngine` et `Cloud Function` à un utilisateur qui veut démarrer des services avec un minimum de configuration ?
    1. Ils sont facturés en fonction du temps d'usage
    2. __***`Ils sont Serverless`***__
    3. Ils nécessitent une configuration minimale de 3 VMs
    4. Ils peuvent exécuter des applications écrites en `Go`
