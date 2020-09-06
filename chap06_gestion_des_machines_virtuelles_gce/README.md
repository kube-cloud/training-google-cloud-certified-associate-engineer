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
                * La famile de l'image, qui vous permet de créer des groupes d'images personalisés
                * Les libellés ou Labels, permettant de faciliter la recherche le moment venu
        * Vous pouvez supprimer une image il suffit
            * De se rendre dans le formulaire de liste d'images
            * De rechercher votre image grâce à la barre de critère
            * Sélectionner votre image
            * Cliquer sur le lien `Supprimer (icone de poubelle)` en haut à droite
        * Vous pouvez aussi déprecier une image de la même manière mais au lieu du lien `Supprimer (icone de poubelle)`, utiliser plutôt le lien `Déprécier (icone de -)`

2. Gestion d'une instance unique de machine virtuelle via Cloud SDK (gcloud) ou Cloud Shell

    * `Cloud SDK` et `Cloud Shell` supportent tous deux le même jeu de commandes.
    * `Cloud Shell` est en réalité un environnement `Cloud SDK` disponible via un navigateur à partir de `Cloud Console`
    * Toutes les commandes `gcloud` supportent un sous-ensemble communs de paramètres et flags (`gcloud-wide flags`) parmis lesquels :
        * --account, qui permet de spécifier le compte GCP à utiliser pour la commande (écrase le compte par défaut)
        * --configuration, qui permet de spécifier un fichier de configuration contenant des paires clé-valeur
        * --flattern, qui permet de générer plusieur enregistrement cle-valeur lorsque la clé a plusieurs valeurs
        * --format, permet de préciser le format de sortie de la commande (`config, csv, default, diff, disable, flattened, get, json, list, multi, none, object, table, text, value, yaml`)
        * --help, permet d'obtenir de l'aide sur la commande en cours
        * --project, permet de spécifier le projet GCP (ID) sur lequel la commande sera exécutée (écrase le projet par défaut)
        * --quiet ou -q, permet de désactiver le mode interactif et utilise les valeurs par défaut à la place de celles attendues de l'utilisateur
        * --verbosity, permet de définir le niveau de détails des retour de commandes (info, warning, error)
    * Comme vu dans le chapitre précédent, l'initialisation des paramètres par défaut se fait via la commande `gcloud init`. Cette commande va générer un fichier de configuration à l'emplacement `<USER_HOME>/.config/gcloud/configurations/<config_your-config-name>`
    * Démarrage des Instances :
        ```
        gcloud compute instances start <instance_name_1> ... <instance_name_n> [--zone=<ZONE_NAME>] [--async]
        ```
        * Vous pouvez overrider la zone utilisée pour la commande, sinon la zone par défaut (gcloud init, ou gcloud config) sera utilisée
        * La liste des zones possibles peut être objtenue via la commande `gcloud compute zones list`
        * `--async` va permettre d'exécuter la commande de manière asynchrone, sans attendre la fin de son exécution
    * Arrêter des Instances :
        ```
        gcloud compute instances stop <instance_name_1> ... <instance_name_n> [--zone=<ZONE_NAME> ou --zone <ZONE_NAME>] [--async]
        ```
    * Supprimer des Instances :
        ```
        gcloud compute instances delete <instance_name_1> ... <instance_name_n> [--zone=<ZONE_NAME> ou --zone <ZONE_NAME>] [--async] [--keep-disks=all] [--quiet] [--limit=N] [--sort-by]
        ```
        * `--keep-disks` permet de spécifier la stratégie de conservation de disque associée à la VM en cours de suppression
            * `all` permet de préciser de conserver tous les disque (boot et données)
            * `boot` permet de ne conserver que le disque de boot
            * `data` permet de ne conserver que les disques de données (non boot disks)
        * `--quiet` permettra d'éxécuter la commande en mode non interactif et utiliser les valeurs par défaut (notament la valeur par défaut de la confirmation de suppression qui est YES)
        * `--sorted-by` permet de préciser les champs de tri
    * Lister les instances de VM :
        ```
        gcloud compute instances list [NAME ...] [--regexp=REGEXP, -r REGEXP] [--zones=ZONE,[ZONE,...]] [--filter=EXPRESSION] [--limit=LIMIT] [--page-size=PAGE_SIZE] [--sort-by=[FIELD,...]] [--uri] [GCLOUD_WIDE_FLAG ...]
        ```
        * `NAME1, ..., NAMEN`, déprécié et à ne plus utiliser
        * `EXPRESSION` permet de préciser l'expression de filtre de résultats. Cette expression prend la forme `property1:value1 [AND|OR] property2:value2 [AND|OR] ... [AND|OR] propertyN:valueN`.
        * Une clé `propertyX` représente une des propriétés de la VM sur laquelle on défini la contrainte. Les valeurs possibles sont :
            * name
            * zone
            * machineType
            * network
            * tags.items
            * labels.<label-name>
            * createTime
        * Il est possible de créer des critères de filtre sur tous les champs d'un objet filtré. notament sur les objets VM, vous pouvez obtenir la liste de ses champs en exécutant la commande permettant de lister les VMs avec une limite de 1 et en affichant le résultat au format JSON : `gcloud compute instances list --format=json --limit=1`. Vous obtiendrez ainsi les champs de l'objet VM et vous pourrez construire vos filtre et vo formattage de type TABLE ou autres.
        * Exemples :
            * Recherche de toutes les VMs dont le nom commence par `instance-group-` et affichage en frmat table avec les colonnes classiques :
            ```
            gcloud compute instances list --filter="name:(instance-group-*)" --format="table(name,zone,machineType,networkInterfaces[0].networkIP:label=INTERNAL_IP,networkInterfaces[0].accessConfigs[0].natIP:label=EXTERNAL_IP, scheduling.preemptible)"
            ```
            * Recherche de toutes les VMs dont le nom commence par `instance-group-` et qui sont dans la zone `us-central1-a` et affichage en frmat table avec les colonnes classiques :
            ```
            gcloud compute instances list --filter="name:instance-group-* and zone:central1-a" --format="table(name,zone,machineType,networkInterfaces[0].networkIP:label=INTERNAL_IP,networkInterfaces[0].accessConfigs[0].natIP:label=EXTERNAL_IP, scheduling.preemptible)"
            ```
            * recherche des VM dont le nom commence par `instance-group-` et qui sont dans la zone `us-central1-a` ou `us-central1-b` :
            ```
            gcloud compute instances list --filter="name ~ instance-group- AND (zone:us-central1-a OR zone:us-central1-b)"
            ```
            L'opérateur `AND` étant implicite, on peut aussi l'écrire :
            ```
            gcloud compute instances list --filter="name ~ instance-group- (zone:us-central1-a OR zone:us-central1-b)"
            ```
            * Recherche des VMs ayant le tags `tag-1` OU `tag-2` tags :
            ```
            gcloud compute instances list --filter="tags.items=(tag-1, tag-2)"
            ```
            * Recherche des VMs ayant le tags `tag-1` ET `tag-2` :
            ```
            gcloud compute instances list --filter="tags.items=tag-1 AND tags.items=tag-2"
            ```
            * Recherche des VMs ayant soit le tag `tag-1` Sans le tag `tag-2` ou ayant le tag `tag-3` :
            ```
            gcloud compute instances list --filter="(tags.items=tag-1 AND -tags.items=tag-2) OR tags.items=tag-3"
            ```
            * Recherche des VMs ayant le label `my-label:` avec n'importe quelle valeur :
            ```
            gcloud compute instances list --filter="(labels.my-label:*"
            ```
    * Obtenir les détails d'une VM :
        ```
        gcloud compute instances describe INSTANCE_NAME [--zone=ZONE] [GCLOUD_WIDE_FLAG ...]
        ```
        * `--zone`, permet de préciser la zone de stockage. Si vous ne la renseignez pas, la valeur par défaut sera utilisée. Vous pouvez configurer la valeur par défaut via la commande : `gcloud config set|unset compute/zone ZONE`
        * `CLOUD_WIDE_FLAG`, son les flags classique de toute les commande gcloud
            * `--account`, qui permet de spécifier le compte GCP à utiliser pour la commande (écrase le compte par défaut)
            * `--configuration`, qui permet de spécifier un fichier de configuration contenant des paires clé-valeur
            * `--flattern`, qui permet de générer plusieur enregistrement cle-valeur lorsque la clé a plusieurs valeurs
            * `--format`, permet de préciser le format de sortie de la commande (`config, csv, default, diff, disable, flattened, get, json, list, multi, none, object, table, text, value, yaml`)
            * `--help`, permet d'obtenir de l'aide sur la commande en cours
            * `--project`, permet de spécifier le projet GCP (ID) sur lequel la commande sera exécutée (écrase le projet par défaut)
            * `--quiet ou -q`, permet de désactiver le mode interactif et utilise les valeurs par défaut à la place de celles attendues de l'utilisateur
            * `--verbosity`, permet de définir le niveau de détails des retour de commandes (info, warning, error)

    * Créer des Snapshots de disque :
        ```
        gcloud compute disks snapshot DISK_NAME [DISK_NAME ...] [--async] [--csek-key-file=FILE] [--description=DESCRIPTION] [--guest-flush] [--labels=[KEY=VALUE,...]] [--snapshot-names=SNAPSHOT_NAME,[...]] [--storage-location=LOCATION]  [--region=REGION | --zone=ZONE] [GCLOUD_WIDE_FLAG ...]
        ```
        * `DISK_NAME1 [DISK_NAME2 ... DISK_NAMEN]`, représentent les disques pour lesquels on veut créer des Snapshots
        * `--async`, permet d'exécuter la commande sans attendre son retour
        * `--description`, permet de spécifier une description du snapshot
        * `--labels`, permet de rattacher des labels au(x) Snapshot(s) créé(s) pour des besoin ultérieus de recherche
        * `--snapshot-names`, permet de préciser les noms de chaque Snapshot, au cas où la commande concerne plusieurs disques
        * `--storage-location`, permet de préciser le type d'emplacement `regional` ou `multi-regional`
        * `--region`, permet de préciser la région de stockage. Si vous ne la renseignez pas, la valeur par défaut sera utilisée. Vous pouvez configurer la valeur par défaut via la commande : `gcloud config set|unset compute/region REGION`
        * `--zone`, permet de préciser la zone de stockage. Si vous ne la renseignez pas, la valeur par défaut sera utilisée. Vous pouvez configurer la valeur par défaut via la commande : `gcloud config set|unset compute/zone ZONE`
        * `--guest-flush`, permet d'informer l'OS de la VM hébergeant le disque de se préparer à un Snapshot, question de s'assurer que les cahes sont flushés.
        * `CLOUD_WIDE_FLAG`, son les flags classique de toute les commande gcloud
            * `--account`, qui permet de spécifier le compte GCP à utiliser pour la commande (écrase le compte par défaut)
            * `--configuration`, qui permet de spécifier un fichier de configuration contenant des paires clé-valeur
            * `--flattern`, qui permet de générer plusieur enregistrement cle-valeur lorsque la clé a plusieurs valeurs
            * `--format`, permet de préciser le format de sortie de la commande (`config, csv, default, diff, disable, flattened, get, json, list, multi, none, object, table, text, value, yaml`)
            * `--help`, permet d'obtenir de l'aide sur la commande en cours
            * `--project`, permet de spécifier le projet GCP (ID) sur lequel la commande sera exécutée (écrase le projet par défaut)
            * `--quiet ou -q`, permet de désactiver le mode interactif et utilise les valeurs par défaut à la place de celles attendues de l'utilisateur
            * `--verbosity`, permet de définir le niveau de détails des retour de commandes (info, warning, error)
    * Lister les Snapshots de disque :
        ```
        gcloud compute snapshots list [NAME ...] [--regexp=REGEXP, -r REGEXP] [--filter=EXPRESSION] [--limit=LIMIT] [--page-size=PAGE_SIZE] [--sort-by=[FIELD,...]] [--uri] [GCLOUD_WIDE_FLAG ...]
        ```
        * `NAME1, ..., NAMEN`, déprécié et à ne plus utiliser
        * `--filter=EXPRESSION`, pareil que dans le cas des filtres de VM. Vous pouvez consulter les champs de filtre en affichant un Snapshot au format JSON via la commande : `gcloud compute snapshots --limit=1 --format=json`. Vous verrez donc les propriétés et pourrez construire vos critères de filtre
        * `--sorted-by` permet de préciser les champs de tri
        * `CLOUD_WIDE_FLAG`, son les flags classique de toute les commande gcloud
            * `--account`, qui permet de spécifier le compte GCP à utiliser pour la commande (écrase le compte par défaut)
            * `--configuration`, qui permet de spécifier un fichier de configuration contenant des paires clé-valeur
            * `--flattern`, qui permet de générer plusieur enregistrement cle-valeur lorsque la clé a plusieurs valeurs
            * `--format`, permet de préciser le format de sortie de la commande (`config, csv, default, diff, disable, flattened, get, json, list, multi, none, object, table, text, value, yaml`)
            * `--help`, permet d'obtenir de l'aide sur la commande en cours
            * `--project`, permet de spécifier le projet GCP (ID) sur lequel la commande sera exécutée (écrase le projet par défaut)
            * `--quiet ou -q`, permet de désactiver le mode interactif et utilise les valeurs par défaut à la place de celles attendues de l'utilisateur
            * `--verbosity`, permet de définir le niveau de détails des retour de commandes (info, warning, error)
    * Obtenir les détails d'un Snapshot :
        ```
        gcloud compute snapshots describe SNAPSHOT_NAME [GCLOUD_WIDE_FLAG ...]
        ```
        * `SNAPSHOT_NAME`, représente le nom du Snapshot
        * `CLOUD_WIDE_FLAG`, son les flags classique de toute les commande gcloud
            * `--account`, qui permet de spécifier le compte GCP à utiliser pour la commande (écrase le compte par défaut)
            * `--configuration`, qui permet de spécifier un fichier de configuration contenant des paires clé-valeur
            * `--flattern`, qui permet de générer plusieur enregistrement cle-valeur lorsque la clé a plusieurs valeurs
            * `--format`, permet de préciser le format de sortie de la commande (`config, csv, default, diff, disable, flattened, get, json, list, multi, none, object, table, text, value, yaml`)
            * `--help`, permet d'obtenir de l'aide sur la commande en cours
            * `--project`, permet de spécifier le projet GCP (ID) sur lequel la commande sera exécutée (écrase le projet par défaut)
            * `--quiet ou -q`, permet de désactiver le mode interactif et utilise les valeurs par défaut à la place de celles attendues de l'utilisateur
            * `--verbosity`, permet de définir le niveau de détails des retour de commandes (info, warning, error)
    * Créer un disque de 100GB standard à partir d'un Snapshot (Juste un exemple d'utilisation, on arrivera sur le stockage bientôt)
        ```
        gcloud compute disks create DISK_NAME --source-snapshot=snapshot-01 --size=100 --type=pd-standard
        ```
    * Supprimer des Snapshots
        ```
        gcloud compute snapshots delete SNAPSHOT_NAME1 [SNAPSHOT_NAME2 ... SNAPSHOT_NAMEN] [GCLOUD_WIDE_FLAG ...]
        ```
        * `SNAPSHOT_NAMEX`, représente les noms des Snapshots à supprimer
        * `CLOUD_WIDE_FLAG`, son les flags classique de toute les commande gcloud
            * `--account`, qui permet de spécifier le compte GCP à utiliser pour la commande (écrase le compte par défaut)
            * `--configuration`, qui permet de spécifier un fichier de configuration contenant des paires clé-valeur
            * `--flattern`, qui permet de générer plusieur enregistrement cle-valeur lorsque la clé a plusieurs valeurs
            * `--format`, permet de préciser le format de sortie de la commande (`config, csv, default, diff, disable, flattened, get, json, list, multi, none, object, table, text, value, yaml`)
            * `--help`, permet d'obtenir de l'aide sur la commande en cours
            * `--project`, permet de spécifier le projet GCP (ID) sur lequel la commande sera exécutée (écrase le projet par défaut)
            * `--quiet ou -q`, permet de désactiver le mode interactif et utilise les valeurs par défaut à la place de celles attendues de l'utilisateur
            * `--verbosity`, permet de définir le niveau de détails des retour de commandes (info, warning, error)
    * Créer des images
        ```
        gcloud compute images create IMAGE_NAME (--source-disk=SOURCE_DISK | --source-image=SOURCE_IMAGE | --source-image-family=SOURCE_IMAGE_FAMILY | --source-snapshot=SOURCE_SNAPSHOT | --source-uri=SOURCE_URI) [--csek-key-file=FILE] [--description=DESCRIPTION] [--family=FAMILY] [--forbidden-database-file=[DBX_VALUE,...]] [--force] [--guest-os-features=[GUEST_OS_FEATURE,...]] [--key-exchange-key-file=[KEK_VALUE,...]] [--labels=[KEY=VALUE,...]] [--licenses=[LICENSES,...]] [--platform-key-file=PLATFORM_KEY_FILE] [--no-require-csek-key-create] [--signature-database-file=[DB_VALUE,...]] [--source-disk-zone=SOURCE_DISK_ZONE] [--source-image-project=SOURCE_IMAGE_PROJECT] [--storage-location=LOCATION] [--kms-key=KMS_KEY : --kms-keyring=KMS_KEYRING --kms-location=KMS_LOCATION --kms-project=KMS_PROJECT] [GCLOUD_WIDE_FLAG ...]
        ```
        * `IMAGE_NAME`, représente le nom de l'image à céer
        * `--source-disk`, permet de spécifier le disque source à partir duquel on veut créer l'image (`à utiliser lorsqu'on veut créer une image à partir d'un disque`)
        * `--source-disk-zone`, permet de préciser la zone du disque source lorsque l'image est créer à partir d'un disque (`--source-disk`). Si aucune zone n'est précisée, la zone par défaut configurée sur `Cloud SDK` sera utilisée
        * `--source-image`, permet de spécifier l'image source à partir de laquelle on veut créer l'image (`à utiliser lorsqu'on veut créer une image à partir d'une autre image`)
        * `--source-image-family`, permet de spécifier la famille de l'image source à partir de laquelle on veut créer l'image. L'image à utiliser sera alors la dernière image non dépréciée de cette famille.
        * `--source-image-project`, permet de référencer le projet dans lequel (la famille de) l'image se trouve.
        * `--source-snapshot`, permet de spécifier le snapshot source à partir duquel on veut créer l'image (`à utiliser lorsqu'on veut créer une image à partir d'un snapshot`)
        * `--source-uri`, permet de préciser le chemin complet du bucket Cloud Storage dans lequel l'image disque source se trouve. `Le fichier image doit être une archive compressée TAR+GZIP avec un nom se terminant par tar.gz`
        * `--description`, permet de préciser la description de l'image
        * `--family`, permet de spécifier la famille de l'image (Lorsqu'on créé une VM ou un disque en précisant la famille de l'image, alors la dernière image non dépréciée de cette famille sera sélectionnée)
        * `--labels`, permet de préciser les labels pour cette image. Ils pourront être utilisés ultérieurement pour des recherches
        * `--force`, permet de forcer la création d'une image à partir d'un disque même si celui-ci est en cours d'utilisation. En effet, par défaut la création d'une image depuis un disque en cours d'utilisation génère une erreur
        * `--storage-location`, permet de spécifier l'emplacement de stockage de l'image générée (regional ou multi-regional).
        * `CLOUD_WIDE_FLAG`, son les flags classique de toute les commande gcloud
            * `--account`, qui permet de spécifier le compte GCP à utiliser pour la commande (écrase le compte par défaut)
            * `--configuration`, qui permet de spécifier un fichier de configuration contenant des paires clé-valeur
            * `--flattern`, qui permet de générer plusieur enregistrement cle-valeur lorsque la clé a plusieurs valeurs
            * `--format`, permet de préciser le format de sortie de la commande (`config, csv, default, diff, disable, flattened, get, json, list, multi, none, object, table, text, value, yaml`)
            * `--help`, permet d'obtenir de l'aide sur la commande en cours
            * `--project`, permet de spécifier le projet GCP (ID) sur lequel la commande sera exécutée (écrase le projet par défaut)
            * `--quiet ou -q`, permet de désactiver le mode interactif et utilise les valeurs par défaut à la place de celles attendues de l'utilisateur
            * `--verbosity`, permet de définir le niveau de détails des retour de commandes (info, warning, error)
        * NB : Les flags `--source-disk`, `--source-image`, `--source-image-family` et `--source-snapshot` sont à utiliser de manière exclusive. Chacun de ces flags permet de définir la source de l'image.
    * Supprimer une image
        ```
        gcloud compute images delete IMAGE_NAME1 [IMAGE_NAME2 ... IMAGE_NAMEN] [GCLOUD_WIDE_FLAG ...]
        ```
        * `IMAGE_NAMEX`, représente les noms des images à supprimer
        * `CLOUD_WIDE_FLAG`, son les flags classique de toute les commande gcloud
            * `--account`, qui permet de spécifier le compte GCP à utiliser pour la commande (écrase le compte par défaut)
            * `--configuration`, qui permet de spécifier un fichier de configuration contenant des paires clé-valeur
            * `--flattern`, qui permet de générer plusieur enregistrement cle-valeur lorsque la clé a plusieurs valeurs
            * `--format`, permet de préciser le format de sortie de la commande (`config, csv, default, diff, disable, flattened, get, json, list, multi, none, object, table, text, value, yaml`)
            * `--help`, permet d'obtenir de l'aide sur la commande en cours
            * `--project`, permet de spécifier le projet GCP (ID) sur lequel la commande sera exécutée (écrase le projet par défaut)
            * `--quiet ou -q`, permet de désactiver le mode interactif et utilise les valeurs par défaut à la place de celles attendues de l'utilisateur
            * `--verbosity`, permet de définir le niveau de détails des retour de commandes (info, warning, error)
    * Exporter une image vers un bucket Cloud Storage
        ```
        gcloud compute images export --destination-uri=DESTINATION_URI (--image=IMAGE | --image-family=IMAGE_FAMILY) [--async] [--export-format=EXPORT_FORMAT] [--image-project=IMAGE_PROJECT] [--log-location=LOG_LOCATION] [--network=NETWORK] [--subnet=SUBNET] [--timeout=TIMEOUT; default="2h"] [--zone=ZONE] [GCLOUD_WIDE_FLAG ...]
        ```
        * `--destination-uri`, permet de préciser le chemin vers le bucket Cloud Storage dans lequel l'image sera exportée
        * `--image`, permet de spécifier l'image à exporter
        * `--image-family`, permet de préciser la famille de l'image à exporter. Dans ce cas, l'image à exporter sera la dernière image non dépréciée de cette famille.
        * `--async`, permet d'exécuter la commande sans attendre son retour
        * `--export-format`, permet de préciser le format d'export de l'image 
            * `vmdk`
            * `vhdx`
            * `vpc`
            * `qcow2`
        
## Introduction à la notion de groupes d'instances

1. Définitions et Caract2ristiques

    * Un groupe d'instance est un ensemble de VMs qui peuvent être gérées de manière unitaire
    * Une commande `gcloud`, `cloud console` ou `cloud shell` exécutée sur un groupe d'instances sera appliquée à toutes les instances du groupe.
    * `GCP` propose 2 type de groupes d'instances
        * Les `Groupes D'instances Managés`
            * Groupes d'instances de VM rigoureusement identiques
            * Les instances de ce groupe sont créées à partir d'un modèle (`template`) de VM standardisant la configuration de chaque VM créée.
            * Les `Templates` de VMs permettent de paramétrer
                * Le type de machine
                * Le disque de Boot
                * Les Regions/Zones
                * Les Labels
                * Le nombre d'instances du groupe
                * etc...
            * Les `Groupes d'instances managés` peuvent être utilisées avec un `Load Balancer` afin de répartir la charge des requêtes sur les instances du groupe.
            * Si une des instances du groupe tombe, elle est automatiquement recrées afin de s'assurer que le nombre d'instances voulues par l'utilisateur soit respecté
            * De même, si l'utilisateur r2duit le nombre d'instance du groupe, un certain nombre d'instances vont être supprimées afin de garantir le respect de la taille de pool souhaitée par l'utilisateur.
        * Les `Groupes D'instances non Managés`
            * Ce sont des groupes dans lesquelles les VM peuvent être créées manuellement avec des configuration strictement différetes

2. Création et suppression des groupes d'instances

    * Pour créer une instance de groupe managée, il faut tout d'abord créer un modèle de VM, qui sera utilisé pour générer les VM du groupe. La commande est la suivante
        ```
        gcloud compute instance-templates create NAME
                [--accelerator=[count=COUNT],[type=TYPE]] [--no-boot-disk-auto-delete]
                [--boot-disk-device-name=BOOT_DISK_DEVICE_NAME]
                [--boot-disk-size=BOOT_DISK_SIZE] [--boot-disk-type=BOOT_DISK_TYPE]
                [--can-ip-forward] [--configure-disk=[PROPERTY=VALUE,...]]
                [--create-disk=[PROPERTY=VALUE,...]] [--description=DESCRIPTION]
                [--disk=[auto-delete=AUTO-DELETE],
                  [boot=BOOT],[device-name=DEVICE-NAME],[mode=MODE],[name=NAME]]
                [--labels=[KEY=VALUE,...]]
                [--local-ssd=[device-name=DEVICE-NAME],[interface=INTERFACE]]
                [--machine-type=MACHINE_TYPE] [--maintenance-policy=MAINTENANCE_POLICY]
                [--metadata=KEY=VALUE,[KEY=VALUE,...]]
                [--metadata-from-file=KEY=LOCAL_FILE_PATH,[...]]
                [--min-cpu-platform=PLATFORM] [--min-node-cpu=MIN_NODE_CPU]
                [--network=NETWORK] [--network-interface=[PROPERTY=VALUE,...]]
                [--network-tier=NETWORK_TIER] [--preemptible]
                [--private-ipv6-google-access-type=PRIVATE_IPV6_GOOGLE_ACCESS_TYPE]
                [--private-network-ip=PRIVATE_NETWORK_IP] [--region=REGION]
                [--no-restart-on-failure] [--shielded-integrity-monitoring]
                [--shielded-secure-boot] [--shielded-vtpm]
                [--source-instance=SOURCE_INSTANCE]
                [--source-instance-zone=SOURCE_INSTANCE_ZONE] [--subnet=SUBNET]
                [--tags=TAG,[TAG,...]] [--address=ADDRESS | --no-address]
                [--boot-disk-kms-key=BOOT_DISK_KMS_KEY
                  : --boot-disk-kms-keyring=BOOT_DISK_KMS_KEYRING
                  --boot-disk-kms-location=BOOT_DISK_KMS_LOCATION
                  --boot-disk-kms-project=BOOT_DISK_KMS_PROJECT]
                [--custom-cpu=CUSTOM_CPU --custom-memory=CUSTOM_MEMORY
                  : --custom-extensions --custom-vm-type=CUSTOM_VM_TYPE]
                [--image-project=IMAGE_PROJECT --image=IMAGE
                  | --image-family=IMAGE_FAMILY]
                [--node=NODE | --node-affinity-file=NODE_AFFINITY_FILE
                  | --node-group=NODE_GROUP]
                [--reservation=RESERVATION
                  --reservation-affinity=RESERVATION_AFFINITY; default="any"]
                [--scopes=[SCOPE,...] | --no-scopes]
                [--service-account=SERVICE_ACCOUNT | --no-service-account]
                [GCLOUD_WIDE_FLAG ...]
        ```
        * `--no-boot-disk-auto-delete`, active la suppression automatique du disque de boot lorsque l'instance parente est supprimée
        * `--boot-disk-device-name`, permet de préciser le nom avec lequel le système d'exploitation invité identifiera le disque de boot. Utilisé uniquement dans le cadre de la création d'un nouveau disque de boot. À ne jamais utiliser si l'on souhaite rattacher un disque existant.
        * `--boot-disk-size`, permet de préciser la taille du disque de boot.
            * La taille du disque de boot est exprimée en KB, MB ou GB
            * S' aucune unité n'est précisée, l'unité par défaut est le GB
            * La taille d'un disque de Boot est entre 10GB et 2TB
        * `--boot-disk-type` permet de préciser le type de disque de Boot
            * Cette option est utilisée dans lorsqu'un nouveau disque est créé (à ne pas utiliser dans le cadre du rattachement d'un disque existant).
            * On peut obtenir la liste des types de disque avec la commande `gcloud compute disk-types list`
        * `--can-ip-forward`, permet à une VM de pouvoir recevoir des pqquets dont l'adresse de destination n'est pas l'une de ses adresses interne et de pouvoir diffuser des paquets dont l'adresse source ne correspond à aucune des ses adresses internes.
        `En effet par défaut, GCP effectue une vérification de paquets afin de s'assurer qu'une VM ne puisse pas recevoir (Diffuser) de paquet dont l'adresse IP de destination (source) ne correspond à aucune de ses adresse IP internes`. Ce comportement empêche à une VM de jouer le rôle de passerelle, en tranferant les paquêts en provenance ou à destination d'une autre VM. Le Flag `--can-ip-forward` permettra donc de faire sauter ce contrôle, permettant ainsi d'utiliser une VM comme passerelle.
        * `--create-disk=`[PROPERTY_NAME=PROPERTY_VALUE, ...]` permet de créer et qttqcher un disque à la VM
            * `name`, propriété permettant de préciser le nom du disque (à ne pas utiliser lorsque plusieurs VM sont créées)
            * `description`, propriété permettant de décrire le disque à créer
            * `mode`, propriété permettant de d´2finir le mode d'accès au disque (`ro` ou `rw`)
            * `image`, propriété permettant de préciser le nom de l'image avec laquelle le disque sera initialisé
            * `image-family`, propriété permettant de préciser la famille de l'image avec laquelle le disque sera initialisé. L'image choisie sera alors la dernière image non dépréciée de cette famille (elle s'utilise à la place de `image`).
            * `image-project`, propriété permettant de préciser le projet à partir duquel toutes les images et familles d'images seront sélectionnées.
            * `size`, propriété permettant de préciser la taille du disque (en KB, MB, GB ou TB)
            * `type`, permet de préciser le type de disque (les types de disques peuvent être obtenus via la commande `gcloud compute disk-types list`)
            * `device-name` permet de spécifier le nom du disque à l'intérieur du système d'exploitation invité. Si aucun n'est donné, le modèle suivant sera utilisé : `persistent-disk-N`
            * `auto-delete`, permet de préciser la stragtégie de suppression automatique du disque lorsque l'instance parente est supprimée. Lq vqleur par défaut est `yes`
            * `boot` permet d'indiquer que le disque est un disque de boot (`yes`) ou pas (`no`)
            * `kms-key` permet de préciser le nom complet de la clé de cryptographie Cloud KMS qui sera utilisée pour chiffrer et protéger le disque. le format de ce chemin est de la forme : `projects/<kms-project>/locations/<kms-location>/keyRings/<kms-keyring>/
            cryptoKeys/<key-name>`
            * `kms-project` permet de préciser le projet KMS dans lequel se trouve la clé de chiffrage du disque (Si aucun projet n'est précisé alors le projet qui utilise le disque sera utilisé). Lorsque ce Flag est positionné, alors les Flags `kms-location`, `kms-keyring` et `kms-key` deviennent obligatoire.
            * `kms-location` permet de préciser le chemin de la cé de cryptographie dans un projet donné. En effet, chaque clé de cryptographie est localisée via un chemin.
            * `kms-keyring` permet de préciser le porte-clé (keyring en anglais) dans lequel se trouve la clé. Si ce paramètre est spécifié, alors `kms-location` et `kms-key` deviennent obligatoire
        * `--description` permet de donner une petite description du template de VM
        * `--disk=[auto-delete=AUTO-DELETE],[boot=BOOT],[device-name=DEVICE-NAME],[mode=MODE],[name=NAME]` permet de rattacher un disque à l'instance créée.Le disque à rattacher doit exister.
            * `name` permet de préciser le nom du disque à rattacher
            * `mode` permet de préciser le mode d'acc`s au disque rattaché (ro, rw)
            * `boot` permet de préciser si le disque sera utilisé comme disque de boot ou pas
            * `device-name` permet de préciser le nom qui sera utilisé en interne (OS) pour référencer le disque (/dev/<device-name>). Si aucun nom n'est précisé, le nom `persistent-disk-N` sera utilisé
            * `auto-delete` permet de préciser si le disque rattaché sera supprimé au même moment que la VM qui l'exploite (les valeurs sont `yes` et `no`)
        * `--labels=[KEY=VALUE,...]` permet de rattacher des labels qui pourront être utilisés dans le cadre des recherches
        * `--machine-type` permet de spécifier le type de VM qui sera créé par cet template. La liste des types de VM peut être obtenue via la commande : `gcloud compute machine-types list`
        * `--maintenance-policy=[MIGRATE|TERMINATE]` permet de préciser la politique de maintenance des VM générées par ce template. En effet, cette politique précise comment traiter laVM en cas de migration de la machine hôte sous-jacente
            * `MIGRATE`, demande la migration de l'instance de VM sur un autre hôte
            * `TERMINATE`, demande l'arrêt pure et simple de l'instance de VM.
        * `--metadata=KEY=VALUE,[KEY=VALUE,...]` permet de spécifier des métadonnées qui seront accessible depuis le système d'exploitation invité.
            * `startup-script` est une méta-donnée qui permet de spécifier le chemin d'un script à exécuter au démarrage de la VM
            * `startup-script-url` pareil que `startup-script`, sauf que le script se trouve sur un serveur sur un web
        * `--metadata-from-file=KEY=LOCAL_FILE_PATH,[...]` permet de chqrger les métadonnées depuis un ensemble de fichier
        * `--preemptible` permet de préciser que la VM sera préemptible ou pas
        * `--network` permet de spécifier le réseau dans lequel les VMs seront créées
        * `--network-interface=[PROPERTY=VALUE,...]` permet de rajouter une interface Network aux VM créés. Lorsque vous utilisez cette option, vous ne pouvez pas utiliser `--address`, `--network`, `--network-tier`, `--subnet`, `--private-network-ip`
            * `address` permet de préciser une adresse externe à assigner à la VM
            * `network` permet de préciser un réseau dans lequel la VM se trouvera
            * `no-address` permet de préciser que l'interface réseau ne disposera d'aucune adresse externe
            * `private-network-ip` permet de préciser une adresse IP privée pour l'interface
            * `--restart-on-failure` permet de redémarrer la VM en cas d'erreur matérielle ou autre.
        * `--source-instance=SOURCE_INSTANCE` permet de créer un template à partir d'une instance de VM
    * On peut supprimer un modèle de VM avec la commande `gcloud compute instance-templates delete NAME`
    * On peut lister un modèle de VM avec la commande `gcloud compute instance-templates list`

    * Une fois le modèle de VM crée on peut créer un groupe d'instances de VMs `managé` grace à la commande suivante
        ```
        gcloud compute instance-groups managed create NAME --size=SIZE
                --template=TEMPLATE [--base-instance-name=BASE_INSTANCE_NAME]
                [--description=DESCRIPTION] [--initial-delay=INITIAL_DELAY]
                [--instance-redistribution-type=TYPE]
                [--stateful-disk=[auto-delete=AUTO-DELETE],[device-name=DEVICE-NAME]]
                [--target-pool=[TARGET_POOL,...]] [--zones=ZONE,[ZONE,...]]
                [--health-check=HEALTH_CHECK | --http-health-check=HTTP_HEALTH_CHECK
                  | --https-health-check=HTTPS_HEALTH_CHECK]
                [--region=REGION | --zone=ZONE] [GCLOUD_WIDE_FLAG ...]
        ```
        * `NAME` représente le nom du groupe d'instances de VMs Managés
        * `-size=SIZE` permet de préciser le nombre d'instances voulu dans le Pool (groupe)
        * `--template=TEMPLATE` permet de préciser le template avec lequel les instances de VMs managées seront générées
        * `--base-instance-name=BASE_INSTANCE_NAME` permet de préciser le nom de base utilisé pour générer les instances du groupe. Si aucun n'est fourni, le nom du groupe sera utilisé.
        * `--description=DESCRIPTION` permet de fournir une petite description du groupe
        * `--initial-delay=INITIAL_DELAY` permet de préciser le temps avant lequel les instances de VM ne devornt pas être HealCheckées, elle sont supposée ête encore en cours d'initialisation. Ce délai ne peut être supérieur à 1H
        * `--zones=ZONE,[ZONE,...]` permet de créer un groupe d'instances managé et régionalisé. Les instances de ce groupe seront crées dans les zones spécifiées
        * `--target-pool=[TARGET_POOL,...]` permet de préciser dans quels pools seront référencées les VM créés par ce groupes d'instance managés (à noter que les `pools cibles` s'utilisent dans le cadre de l'équilibrage de charges)
        * `--health-check=HEALTH_CHECK` permet de spécifier le nom du HealthCheck à utiliser pour tester l'état de vie des instances du groupe.
    * Lister les groupes d'instances managées : `gcloud compute instance-groups managed list`
    * Lister les instances d'un groupe managé : `gcloud compute instance-groups managed list-instances GROUP_NAME`
    * Supprimer un groupe d'instances managées: `gcloud compute instance-groups managed delete GROUP_NAME [GROUP_NAME...]`

3. Création et suppression des templates d'instances de groupes Load-Balancés et Autoscalés
4. Allocation de ressources élastique (Elastic Resources Allocation)

## Recommadantions de gestion de VMs

## Résumé

## Quelques questions d'examen

## Revue des questions
