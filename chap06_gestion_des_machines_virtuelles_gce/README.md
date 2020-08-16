# Gestion des machines virtuelles

Après avoir créée les VMs, il est désormais temps de travailler avec elles de manière individuelle ou groupée (Instances Group).

Dans ce chapitre nous allons :

1. Présenter quelques une des tâches habituelle de management
2. Exécuter ces tâches via `Cloud Console`
3. Exécuter ces tâches via `Cloud SDK` et `Cloud Shell`

## Gestion d'une instance unique

1. Gestion d'une instance unique de machine virtuelle via la Console Web

    * (Re)Démarrage, Reprise, Suspension, Arrêt et Suppression d'une VM

        Ces opérations se font en cliquant sur l'item de menu adéquat apparaissant lors du clic sur les 3 boutons en fin de la ligne représentant la VM que vous souhaitez manipuler. En image :

        * Démarrage

        ![`Demarrage VM`](./gcp_demarrage_vm_console_web.png)

        * Redémarrage : La VM est redémarrée et son état mémoire est perdu

        ![`Redemarrage VM`](./gcp_redemarrage_vm_console_web.png)

        * Suspension : Correspond à l'envoi d'un signal ACPI (Advanced Configuration and Power Management) S3 au système d'exploitation de la VM, causant ainsi son passage à l'état SUSPENDED. La VM reste en cours d'exécution mais n'est plus accessible. Son système ne traite plus de requêtes.
        À l'état SUSPENDU, uniquement les ressources Mémoire, disque et adresses IP vous sont facturées.

        ![`Suspension VM`](./gcp_suspension_vm_console_web.png)

        * Reprise : Cette opération concerne les VM à l'état suspendue. Elle permet de réactiver le système précédemment suspendu et réouvrir les accès vers la VM. Dans le cas où la VM utilise une clé de chiffrement pour ses disques, il faudra le préciser lors de la réactivation

        * Arrêt : La VM est totalement arrêtée. Lorsqu'une instance de VM est arrêtée elle ne consomme plus de ressource et de ce fait vous n'êtes plus facturé. L'instance continue à exister et peut être redémarrée à tout moment. Une fois que la VM est stoppée, l'icône verte indiquant l'état de demarrage passe à gris et les opérations `Stop` et `Reset` de la liste d'opérations de l'instance sont grisées.

        ![`Arrêt VM`](./gcp_arret_vm_console_web.png)

        * Suppression : La VM est complètement supprimée de GCP et toutes les ressources réservées sont libérées. Lors de la suppression, GCP vous demandera de confirmer votre souhait de supprimer l'instance

    * Recherche et affichage de l'inventaire de VMs

        * La page des VM présente par défaut la liste des VMs du projet indépendament de leur état.
        * Si la liste est trop longue, la barre de recherche au-dessus (avec la mention `Filtrer les instances VM`), permet de préciser des critères de recherche de VM. Vous pouvez effectuer des recherche multicritères simple et composées en utilisant des opérateurs logiques (`OR` explicitement et `AND` implicitement). Vous pouvez donc exploiter les critères de recherches suivants :
            * Nom
            * Zone
            * Type de machine
            * Réseau
            * Tags réseau
            * Libellés
            * Protection contre la suppression (TRUE|FALSE)
            * État (ARRET|EN COURS|SUSPENDU)
            * Adresse IP Interne
            * Adresse IP Externe
            * Membre de groupe d'instances gérées (TRUE|FALSE)
            * Membre de groupe d'instances non gérées (TRUE|FALSE)

        ![`Filtre VM`](./gcp_filtre_vm_console_web.png)

    * Attachement d'une unité de calcul graphique à une VM

        * Les unités `GPU` sont utilisées pour des VMs dédiées à des calculs intensifs (Visualisation de données, Machile learning, etc...).
        * Les unités `GPU` exécutent des calculs intensifs mathématiques et permettent donc de déplacer des charges de travail du CPU noral vers les unités `GPU`
        * Pour rajouter une unité GPU à une instance de VM il faut :
            * S'assurer que les librairies GPU sont bien installées dans l'OS de l'instance de la VM. pour cela vous pouvez utiliser pour les disque de boot, des images GCP dans lesquelles ces librairies sont déjà disponibles comme par exemple
                * Les images dédiées au Deep Learning
                    * `Intel Optimized Deep learning Image : Tensor flow`
                    * `GPU Optimized debian m32`
            * Rajouter, dans la zone de choix des types de machine, des instances de processeurs GPU à l'instance lors de la création de la VM. Vos pouvez spécifier
                * Le type d'unité GPU
                * Le nombre d'unité GPU

            ![`Add GPU VM`](./gcp_add_gpu_console_web.png)

        * L'ajout d'unité GPU est soumise à contraintes
            * Les unités GPUs ne peuvent êtres rattachées à des instances de VM à mémoire partagées
            * Les unités GPU doivent être compatibles aux unités CPU de la VM. Par exemple, si vous choisissez des CPU Intel Skylake ou plus récents, vous ne pouvez pas y rattacher d'unité GPU Tesla K80 GPU.

        * Quelques recommendations sont à prendre en compte lorsqu'on utilisenités GPU
            * Ne pas utiliser des unités GPUs sur des instances préemptibles
            * Configurer le redémarrage automatique des instances VM
            * Activer l'arrêt de la VM lors de la maintenance

    * Travailler avec des Snapshots de VMs

        * Les `Snapshots` sont des copies des données contenues dans les disques persistents.
        * Les `Snapshots` sont utiles pour sauegarder les données d'un disque persistent afin de les restaurer plus tard. Vous pourrez donc de cette manière créer des VMs ou des disque persistent disposant exactement des mêmes données.
        * GCP supporte les concepts de `Snapshots` incrémentaux. En effet, lorsque vous créez un `Snapshot` pour le première fois, GCP va créer une compie complètes des données de disque. La prochaine fois que vous allez créer un `Snapshot` GCP ne copiera que les données qui ont changé. Ce qui permet d'optimiser le stockage. Il faut cependant prendre des précautions lorsque vous utilisez des logiciels exploitant du Buffuring (MySQL, etc...). Il faut vous assurez que les données ont bien été flushées sur disque avant d'exécuter le `Snapshot`.

        * Pour créer avec les Snapshots il faut :
            * Vous assurer que l'utilisateur dispose du rôle `Compute Storage Admin`

            ![`Role Compute Storage Admin`](./gcp_role_compute_storage_admin_console_web.png)

            * Vous rendre ensuite dans le menu de création des `Snapshots` : `(Compute Engine -> Snapshots)`. Vous avez ainsi la possibilité de créer manuellement des instantannés ou de programmer leur génération

            ![`Snaphot Menu`](./gcp_snaphots_menu_admin_console_web.png)

            * Cliquez sur le bouton de création d'un instantané. Le formulaire suivant s'affichera.

                ![`Snaphot Form`](./gcp_snaphot_form_console_web.png)

                Vous pourrez donc renseigner
                * Le nom de l'instantanné
                * La description de l'instantanné
                * Le disque source (L'un des disque créé manuellement ou automatiquement lors de l création d'une VMs)
                * Le type d'emplacement de voyre instantanné,
                    * `Régional` pour un stockage dans une seule région
                    * `Multirégional` pour un stockage avec redondance dans plusieurs régions
                * L'emplacement de la Snapshot
                    * Dans le cas du stockage régional, vous choisissez la région qui vous convient por le stockage (ex: `us-central1`, `asia-east1`)
                        * Attention, si vous choisissez une région différente de celle du disque source, il vous sera facturé des frais de transfert réseau
                    * Dans le cas du stockage multi-régional, vous devez choisir l'espace géographique (contenant les régions) dans lequel vous souhaitez stocker (Ex: `us`, `asia`, `eu`)
                * Les étiquettes (Labels), que vous pourrez utiliser plus tard dans des recherches.
                * À noter que l'option VSS, permettant de snapshoter un disque sans arrêter la VM associée est disponible pour des disques rattachés à des VMs Windows Server.

            * Dans le cas de la création d'une programmation d'instatanné, qui vous permettra de générer automatiquement des sauvegardes de vos disques, en plus des informations du formulaire de l'instantanné, vous renseignerez.
                * La fréquence de génération des instantannés
                * L'heure de début du backup
                * Le nombre de jours de conservation d'un instantanné (par defaut 14 Jours)
                * La règle de conservation de l'instantanné si le disque source est supprimé. Vous avez le chois entre :
                    * Conserver les instantannés
                    * Supprimer les instantannées en fonction du nombre de jours de conservation précédent
                * Activer VSS, qui vous permet de créer un instantanné sans avoir à arreter la VM

                ![`Snaphot Schedule Form`](./gcp_snaphot_scheduled_form_added_options_console_web.png)

                * Une fois la programmation d'instantannée créée, vous pouvez l'appliquer à un disque, soit lors de sa création dans le cadre d'un ajout de disque à une VM ou directement dans le formulaire de création de disque, soit dans son panneau de détails, en sélectionnant dans le formulaire, le scheduler à utiliser.

                Paramétrage de la programmation lors de la création de la VM

                ![`Snaphot Schedule Apply On VM Creation Form`](./gcp_snaphot_scheduled_form_apply_vm_creation_console_web.png)

                Paramétrage de la programmation lors de la modification d'un disque

                ![`Snaphot Schedule Apply Form`](./gcp_snaphot_scheduled_form_apply_console_web.png)

        * Pour supprimer un instantanné, il faut :
            * Cliquer sur le lien représentant le nom de l'instantanné, afin d'afficher ses détails
            * Dans son panneau de détail, cliquer sur le lien `Supprimer` en haut à droite des autre boutons d'action.

    * Travailler avec des images de VMs

        * Une image est similaire un `Snapshot (Instantané)`. Ce sont tous deux des copies des contenue de disque.
        * Les différences entre Images et Snapshots sont les suivantes
            * Une image peut être utilisée pour booter une VM, ce qui n'est pas le cas d'un Snapshot
            * Une image contient un `boot loader`
            * Une image contient un `système d'exploitation`
            * Un Snapshot est utilisé pour la sauvegarde de données
            * Un Snapshot supporte les mécanismes de sauvegarde incrémental, ce qui n'est pas le cas d'une image
        * Une image peut être créée à partir :
            * D'un disque
            * D'un Snapshot
            * D'un fichier Cloud Storage
            * D'une autre image
            * D'un disque virtuel (VMDK ou VHD) Stocké sur Cloud Storage
        * Vous pouvez Créer une image à partir du formulaire de création des images
            * Allez sur le menu de list des images (Compute Engine -> Images)

            ![`Images List`](./gcp_images_list_console_web.png)

            * Cliquez sur le lien `Créer une image`
            * Une fois le formulaire affiché, saisissez les informations requises et valisez. Vous pouvez renseigner

                ![`Images Form`](./gcp_images_creation_form_console_web.png)

                * Le nom de l'image
                * Le type de source de l'image
                    * Disque
                    * Snapshot
                    * Fichier Cloud Storage
                    * Image
                    * Disque virtuel (VMDK, VHD)
                * La source de l'image
                    * Le disque cible au cas où le type de source est `Disque`
                    * Le Snapshot cible au cas où le type de source est `Snapshot`
                    * L'image source (créée par vous dans un projet) au cas où le type de source est `Image`
                    * Le chemin du fichier `Cloud Storage` au cas où le type de source est `Fichier Cloud Storage`
                    * Le chemin `Cloud Storage` vers le disque virtuel au cas où le type de source est `Disque Viruel`
                * L'emplacement de stockage (Même description que dans les Snapshots)
                * Les libellés ou Labels, permettant de faciliter la recherche le moment venu

2. Gestion d'une instance unique de machine virtuelle via Cloud Shell
3. Gestion d'une instance unique de machine virtuelle via Command Line

## Introduction à la notion de groupes d'instances

1. Création et suppression des groupes d'instances
2. Création et suppression des templates d'instances de groupes Load-Balancés et Autoscalés
3. Allocation de ressources élastique (Elastic Resources Allocation)

## Recommadantions de gestion de VMs

## Résumé

## Quelques questions d'examen

## Revue des questions
