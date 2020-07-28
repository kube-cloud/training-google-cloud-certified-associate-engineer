# Projets, Services et Facturation

Dans ce chapitre, nous allons tenter de comprendre comment GCP organise les ressources mises à disposition des utilisateurs. Nous présenterons aussi le concept de comptes de service (Service Account) ainsi que le modèle de facturation GCP.

## Organisation des projets et des comptes par Google Cloud Platform (GCP)

Google utilise un modèle hiérarchique basé sur les notions d'Organisation, de répertoires et de projets afin d'organiser les ressources mise à disposition aux utilisateurs. Il utilise aussi la notion de `Compte de Service (Service Account)`, de `Roles` et de `Permissions`, permettant aux clients de contrôler finement les droits d'accès à leurs ressources et services.

En effet, lorsque vous exécuter plusieurs groupes d'applications et de services destinés à plusieurs départements de votre organisation, vous pouvez très vite avoir besoin de gérer de manière indépendantes les services adressés à chacun de vos département et même d'avoir des responsables IT par départements qui se chargerons de gérer les politiques d'accès grâce à l'attribution de privilèges.

GCP proprose donc la notion de `Hiérarchie de Ressources (Resources Hierarchy)` permettant de grouper des ressources et de les gérer comme une seule unité en y appliquant des politiques d'accès.

1. Hiérarchie des ressources GCP

Le concept central autour duquel est bâtis l'rganisation des ressources est `Resource Hierarchy`.
Google propose 3 niveaux de hiérarchie de ressources

* Organisations

  * L'`Organisation` est la racine de la hiérarchie des ressources GCP pour une entreprise
  * L'`Organisation` représente une entreprise
  * Un client sans `Organisation` se voit attribué une organisation auto-générée
  * Les services `G-Suite Domains` et `Google Cloud Identity` sont associés à une organisation, ces deux services permettent en pratique de définir des information d'identités pour une Organisation chez GCP.
  * `G Suite` est une suite d'outils de productivité proposée par `Google Cloud Platform`. La suite d'outils propose
    * `Google Docs`
    * `Google Sheet`
    * `Google Drive`
    * `Google Calendar`
    * `GMail`
    * etc...
  * Si une entreprise utilise déjà `G Suite`, elle peut créer une `organisation` dans sa hiérarchie de ressource `GCP`
  * Une entreprise n'est toutefois pas obligée d'utiliser `G Suite` pour être ientifiée chez `GCP`, elle peut tout simplement utiliser le service `Google Cloud Identity (iDaaS)`
  * Une identité `Cloud Identity` est associée au plus à une entreprise
  * Une identité `Cloud Identity` dispose d'un `Super Administrateur` qui distribue le rôles d'administrateurs IAM (`Identity and Access Management`) au niveau Organisation, aux utilisateurs afin de décentraliser la gestion de l'organisation et de l'accès aux ressources
  * De plus, GCP attribue automatiquement à tous les utilisateurs du domaine les rôles de :
    * Créateur de projet (Project Creator)
    * Créateur de compte de facturation (Billing Account Creator)
  * Ces deux rôles attribués automatiquement permettent donc à tout utilisateur de créer des projets et d'activer la facturation pour les ressources utilisées dans sont projet.
  * L'`Administrateur IAM` a pour rôle de:
    * Définir la structure de la hiérarchie des ressources
    * Définir les politiques d'accès aux ressources de la hiérarchie
    * Déléguer certains rôles de gestion de ressources à d'autres utilisateurs.
  * Lorsqu'un membre d'une organisation G-Suite ou un utilisateur Cloud Identity crée un compte de facturation ou un projet, GCP va automatiquement générer une ressource Organisation
  * Tout projet et tout compte de facturation doit être créee dans une Organisation (Celle-ci peut être générée automatiquemen par GCP)
  * Lorsqu'un projet est crée, tous les utilisateurs importés dans ce projet ont par défaut les rôles
    * `Créateur de projets (Project Creator)`
    * `Créateur de compte de facturation (Billing Account Creator)`
  * Conséquence du points précédent : TOUS LES UTILISATEURS G-SUITE ONT ACCES AUX RESSOURCES GCP du fait qu'il font partit d'une Organisation GCP et que par défaut ils ont des droits de créateur de projets et de compte de facturation GCP

* Répertoires

  * Les répertoires représentent des Blocks organisationnels dans une Organisation : Exemple, le département des Finances, RH
  * Les répertoires peuvent donc être utilisés afin d¡organiser et cloisonner les projets de plusieurs unités. Quelaue exemples
    * Une entreprise ayant 4 départements 
      * Finance, qui va stocker les données des comptes de réception et de paiement séparés. L'administrateur pourra donc créer un répertoire `Finance` avec deux sous-répertoires `Account Receivable` et `Account Payable`
      * Développement logiciel, dans lequel chaque logiciel dispose d'un environnement de déploiement de type DEV, TEST, STAGING et PROD avec des politiques de contrôle d'accès spécifique à chaque environnement. L'administrateur pourra donc organiser les ressources de chaque environnement dans un répertoire dédié
      * Marketing et legal partagent toutes leurs ressources. L'administrateur pourra créer un seul répertoire pour les ressources de ces deux départements

    ![GCP Organization Hierarchy : Folder](./gcp_organization_hierarchy_01.png)

* Projets

  * Le projet est le concept opérationnel GCP, c'est dans un projet que l'on peut finalement créer des ressources, activer et exploiter des services, gérer les permissions et la facturation
  * Tout utilisateur disposant de la permission `resourcemanager.projects.create` peut créer un projet. Par défaut, lorsqu'une organisation est créee, tous les utilisateurs du domaine ont cette permission
  * Une organisation dispose d'un quota de projet qu'il peut créer. orsque vous atteignez la limite de projets que vous pouvez créer et que vous essayez d'en créer un autre, il vous sera demandé d'adresser une requête d'augmentation de quotas dans laquelle vous préciserez le nombre de projets supplémentaires que vous souhaitez créer ainsi que leur utilité.
  * Une fois votre hiérarchie de ressources complète, vous pouvez commencer à définir les polices de gouvernance de cette hiérarchie.

2. Politiques d'une Organisation

GCP fournit un service de gestion des Politiques d'Organisation. Ce service gère le contrôle d'accès aux ressources de l'organisation. Il complète IAM dans ce sens que IAM permet `d'affecter des permissions à un utilisateurs ou à un rôle afin de contrôler quelle opération il peut effectuer` tandis que le Service de gestion des Politiques d'Organisation permet de `Définir les limites d'utilisation des ressources d'une organisation (indépendament de qui a le droit de le faire ou pas)`

En gros, `IAM` permet de définir qui peut effectuer une opération sur une ressource, tandis que le `Service de gestion des Politique d'Organisation` permet de définir quelle opération peut être effectué sur une ressources.

Les politiques d'organisation s'exprime sous forme de contraintes sur les ressources.

* Contraintes sur les ressources

  * Les contraintes sont en réalité des restrictions sur les services
  * GCP dispose de deux types de contraintes
    * Les liste de contraintes, qui représentent des liste de valeurs autorisées pour une ressources. Quelques contraintes de ce type
      * Permettre un ensemble de valeurs spécifiques
      * Interdire un ensemble de valeurs spécifiques
      * Interdire un valeur ainsi que ses valeurs filles
      * Interdire toutes les valeurs
    * Les contraintes booléennes, qui sont évaluées à `TRUE` ou `FALSE` permettent de déterminer si la contrainte doit être appliquée ou pas. Quelques contraintes de ce type
      * constraints/compute.disableSerialPortAccess (TRUS/FALSE), permet d'autoriser ou non l'accès au port série

* Évaluation des politiques

  * L'évaluation des politiques d'accès se font sur les ressources des projets
  * Un projet va hériter des politiques de son parent (répertoire/organisation).
  * Lorsqu'on définit une politique, on peut l'appliquer à :
    * Une Organisation, ainsi tous les répertoires et projets de cette organisation hériteront de cette politique 
    * Un répertoire, ainsi tous les sous-répertoires et projets de cette organisation hériteront de cette politique
    * Un projet va cumuler les permissions de ses parents directs et indirects
  * Les politiques d'organisation sont accessible dans le panneau `IAM and Admin` > `Organisation Rules`


3. Gestion des projets GCP

* La première chose à faire lorsqu'on engage un initiative Cloud est de créer une hirarchie et un projet. Pour cela, il faudra 
  * Naviguer vers `https://console.google.com`
  * S'authentifier
  * Activer google
  * Allez sur `IAM and Admin` > `Manage Resources`
  * Vous pouvez à ce niveau créer des répertoires et des projets pour votre Organisation. Attention toutefois a disposer des droits adéquats (`Administrateur de dossier (resourcemanager.folders.*)`)

## Rôles et Identités (Roles and Identity)

En plus de créer et gérer des hiérarchies de ressources et des ressources, vous aurez aussi besoin de contrôler l'accès à ces ressources. Pour cela vous devrez savoir manipuler les rôles et identités

1. Les rôles GCP

* Un rôle est une collection de permissions
* Un rôle peut être associé à un utilisateurs ou à un groupe d'utilisateurs
* Une identité est le concept qui représente un utilisateur humain ou un service
  * Ex : `Jean-Jacques ÉTUNÈ NGI` est un Architecte Cloud (Utulusateur humain), son identité est `jetune@kube-cloud.com` chez GCP et il peut `créer`, `modifier` et `supprimer` des ressources.
* GCP Propose 3 types de rôles
  * Les `Roles Primitifs`, qui sont des rôles anciens qui existaient bien avant le service `IAM`. Il comprennent les rôles
    * `Propriétaire (Owner)`
    * `lecteur (Viewer)`
    * `Éditeur (Editor)`
  * Les `Rôles Prédéfinis`, permettent de gérer ssleions de manière beaucoup plus précise
  * Les `Rôles Personalisés`


2. Attribution de rôles à des identités (utilisateurs)
3. Allocation de ressources élastique (Elastic Resources Allocation)
4. Services spécialisés (Specialized Services)

## Comptes de services (Account Services)

## Facturation (Billing)

1. Compte de facturation
2. Budgets et Alertes
3. Exportation des données de facturation

## Activation des APIs

## Provisionning de Workspace Stackdriver

## Quelques questions d'examen

## Revue des questions
