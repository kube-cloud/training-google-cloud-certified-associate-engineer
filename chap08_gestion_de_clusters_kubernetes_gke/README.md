# Gestion d'un cluster Kubernetes

L'objectif de ce chapitre est d'apprendre à gérer les ressources GKE

## Accès au Statut du cluster Kubernetes

1. Accès au Statut du cluster Kubernetes via la Console Web
2. Accès au Statut du cluster Kubernetes via Cloud SDK / Cloud Shell

    * `gcloud container clusters list`
    * `gcloud container clusters describe <CLUSTER_NAME> [--zone=ZONE] [--project=PROJECT_ID]`

## Ajout, modification et suppression de noeuds du Cluster Kubernetes

1. Ajout, modification et suppression de noeuds du Cluster Kubernetes via la Console Web
    
    * Ajout de noeud :  Il suffira de mettre à jour le nombre de noeud du pool souhaité
    * Modifier un cluster : Il suffira de cliquer sur le bouton de modification du cluster afin d'afficher le formulaire de mise à jour

2. Ajout, modification et suppression de noeuds du Cluster Kubernetes via Cloud SDK / Cloud Shell

    * Ajout de noeud : `gcloud container clusters resize <CLUSTE_NAME> --node-pool <NODE_POOL_TO_UPDATE> --size=<NEW_SIZE> [--zone=ZONE] [--project=PROJECT_ID]`
    * Modification cluster : `gcloud container clusters update <CLUSTE_NAME> [--zone=ZONE] [--project=PROJECT_ID] <ALL_AUTHORIZED_CLUSTER_OPTIONS>`
        * Exemple : `gcloud container cluster cusler-demo --enable-autoscaling --min-nodes 1 --max-nodes 10`

## Ajout, modification et suppression de PODS du Clueter Kubernetes

1. Ajout, modification et suppression de PODS du Cluster Kubernetes via la Console Web
2. Ajout, modification et suppression de PODS du Cluster Kubernetes via Cloud SDK / Cloud Shell

## Ajout, modification et suppression de Services du Clueter Kubernetes

1. Ajout, modification et suppression de Services du Cluster Kubernetes via la Console Web
2. Ajout, modification et suppression de Services du Cluster Kubernetes via Cloud SDK / Cloud Shell

## Accès au dépôt et au détails d'images disponibles

1. Accès au dépôt et au détails d'images disponibles via la Console Web
2. Accès au dépôt et au détails d'images disponibles via Cloud SDK / Cloud Shell

## Résumé

## Quelques questions d'examen

## Revue des questions
