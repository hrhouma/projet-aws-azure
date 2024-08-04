🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

##### 1 - Script shell pour supprimer toutes les ressources AWS créées

🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

- Ce script supprime toutes les ressources AWS créées, y compris les instances, les volumes, les groupes de sécurité et les paires de clés. Il demande une confirmation avant chaque suppression et permet à l'utilisateur de choisir les ressources à supprimer ou de tout supprimer d'un coup.

```sh
#!/bin/bash

# Variables
REGION="us-east-1"  # Change to your preferred region

function delete_instances() {
    echo "Fetching all running instances..."
    INSTANCE_IDS=$(aws ec2 describe-instances --filters "Name=instance-state-name,Values=running" --query 'Reservations[*].Instances[*].InstanceId' --output text)
    
    if [ ! -z "$INSTANCE_IDS" ]; then
        echo "Running instances found: $INSTANCE_IDS"
        read -p "Do you want to terminate all instances, or specify which ones to terminate? (all/specify): " choice
        if [ "$choice" == "all" ]; then
            aws ec2 terminate-instances --instance-ids $INSTANCE_IDS
            aws ec2 wait instance-terminated --instance-ids $INSTANCE_IDS
            echo "All instances terminated."
        elif [ "$choice" == "specify" ]; then
            read -p "Enter the Instance IDs to terminate (separated by spaces): " instance_ids
            aws ec2 terminate-instances --instance-ids $instance_ids
            aws ec2 wait instance-terminated --instance-ids $instance_ids
            echo "Specified instances terminated."
        fi
    else
        echo "No running instances found."
    fi
}

function delete_volumes() {
    echo "Fetching all available volumes..."
    VOLUME_IDS=$(aws ec2 describe-volumes --filters "Name=status,Values=available" --query 'Volumes[*].VolumeId' --output text)
    
    if [ ! -z "$VOLUME_IDS" ]; then
        echo "Available volumes found: $VOLUME_IDS"
        read -p "Do you want to delete all volumes, or specify which ones to delete? (all/specify): " choice
        if [ "$choice" == "all" ]; then
            for volume_id in $VOLUME_IDS; do
                aws ec2 delete-volume --volume-id $volume_id
            done
            echo "All volumes deleted."
        elif [ "$choice" == "specify" ]; then
            read -p "Enter the Volume IDs to delete (separated by spaces): " volume_ids
            for volume_id in $volume_ids; do
                aws ec2 delete-volume --volume-id $volume_id
            done
            echo "Specified volumes deleted."
        fi
    else
        echo "No available volumes found."
    fi
}

function delete_security_groups() {
    echo "Fetching all security groups (excluding default)..."
    SECURITY_GROUP_IDS=$(aws ec2 describe-security-groups --query "SecurityGroups[?GroupName!='default'].GroupId" --output text)
    
    if [ ! -z "$SECURITY_GROUP_IDS" ]; then
        echo "Security groups found: $SECURITY_GROUP_IDS"
        read -p "Do you want to delete all security groups, or specify which ones to delete? (all/specify): " choice
        if [ "$choice" == "all" ]; then
            for sg_id in $SECURITY_GROUP_IDS; do
                aws ec2 delete-security-group --group-id $sg_id
            done
            echo "All security groups deleted."
        elif [ "$choice" == "specify" ]; then
            read -p "Enter the Security Group IDs to delete (separated by spaces): " sg_ids
            for sg_id in $sg_ids; do
                aws ec2 delete-security-group --group-id $sg_id
            done
            echo "Specified security groups deleted."
        fi
    else
        echo "No security groups found."
    fi
}

function delete_key_pairs() {
    echo "Fetching all key pairs..."
    KEY_NAMES=$(aws ec2 describe-key-pairs --query 'KeyPairs[*].KeyName' --output text)
    
    if [ ! -z "$KEY_NAMES" ]; then
        echo "Key pairs found: $KEY_NAMES"
        read -p "Do you want to delete all key pairs, or specify which ones to delete? (all/specify): " choice
        if [ "$choice" == "all" ]; then
            for key_name in $KEY_NAMES; do
                aws ec2 delete-key-pair --key-name $key_name
                rm -f ${key_name}.pem
            done
            echo "All key pairs deleted."
        elif [ "$choice" == "specify" ]; then
            read -p "Enter the Key Names to delete (separated by spaces): " key_names
            for key_name in $key_names; do
                aws ec2 delete-key-pair --key-name $key_name
                rm -f ${key_name}.pem
            done
            echo "Specified key pairs deleted."
        fi
    else
        echo "No key pairs found."
    fi
}

function recursive_cleanup() {
    delete_instances
    delete_volumes
    delete_security_groups
    delete_key_pairs

    echo "Fetching all remaining resources..."
    remaining_instances=$(aws ec2 describe-instances --query 'Reservations[*].Instances[*].InstanceId' --output text)
    remaining_volumes=$(aws ec2 describe-volumes --query 'Volumes[*].VolumeId' --output text)
    remaining_security_groups=$(aws ec2 describe-security-groups --query 'SecurityGroups[*].GroupId' --output text)
    remaining_key_pairs=$(aws ec2 describe-key-pairs --query 'KeyPairs[*].KeyName' --output text)

    echo "Remaining instances: $remaining_instances"
    echo "Remaining volumes: $remaining_volumes"
    echo "Remaining security groups: $remaining_security_groups"
    echo "Remaining key pairs: $remaining_key_pairs"

    read -p "Do you want to continue cleanup? (yes/no): " continue_cleanup
    if [ "$continue_cleanup" == "yes" ]; then
        recursive_cleanup
    else
        echo "Cleanup stopped by user."
        exit 0
    fi
}

# Main execution starts here
echo "Starting recursive cleanup of AWS resources..."
recursive_cleanup

```

Pour exécuter ce script :

1. Enregistrez le script dans un fichier, par exemple `cleanup-aws-resources.sh`.

   ```sh
   cmd
   powershell
   nano cleanup-aws-resources.sh
   ```

2. Rendez le script exécutable :

   ```sh
   chmod +x cleanup-aws-resources.sh
   ```

3. Exécutez le script :

   ```sh
   sh cleanup-aws-resources.sh
   ```

Assurez-vous que l'AWS CLI est configurée correctement avec vos identifiants AWS avant d'exécuter ce script. Le script va demander confirmation avant de supprimer les ressources et continuera à demander s'il y a des ressources restantes jusqu'à ce que l'utilisateur décide d'arrêter.

---

🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
#### 2 - Annexe : Guide d'utilisation
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇


#### 1 - Script shell pour supprimer toutes les ressources AWS créées

Ce guide vous accompagne pas à pas dans l'utilisation du script shell pour supprimer toutes les ressources AWS créées par vos précédents scripts ou manuellement. Il couvre la suppression des instances EC2, des volumes, des groupes de sécurité et des paires de clés.

---

##### Prérequis :
1. **AWS CLI** : Assurez-vous que l'interface en ligne de commande AWS est installée et configurée sur votre machine.
2. **Permissions AWS** : Vous devez avoir les permissions nécessaires pour supprimer des instances EC2, des groupes de sécurité, des volumes et des paires de clés.

##### Étapes détaillées :

---

##### 1. Enregistrez le script dans un fichier.

Créez un fichier nommé `cleanup-aws-resources.sh` et copiez le script suivant dans ce fichier :

```sh
#!/bin/bash

# Variables
REGION="us

-east-1"  # Change to your preferred region

function delete_instances() {
    echo "Fetching all running instances..."
    INSTANCE_IDS=$(aws ec2 describe-instances --filters "Name=instance-state-name,Values=running" --query 'Reservations[*].Instances[*].InstanceId' --output text)
    
    if [ ! -z "$INSTANCE_IDS" ]; then
        echo "Running instances found: $INSTANCE_IDS"
        read -p "Do you want to terminate all instances, or specify which ones to terminate? (all/specify): " choice
        if [ "$choice" == "all" ]; then
            aws ec2 terminate-instances --instance-ids $INSTANCE_IDS
            aws ec2 wait instance-terminated --instance-ids $INSTANCE_IDS
            echo "All instances terminated."
        elif [ "$choice" == "specify" ]; then
            read -p "Enter the Instance IDs to terminate (separated by spaces): " instance_ids
            aws ec2 terminate-instances --instance-ids $instance_ids
            aws ec2 wait instance-terminated --instance-ids $instance_ids
            echo "Specified instances terminated."
        fi
    else
        echo "No running instances found."
    fi
}

function delete_volumes() {
    echo "Fetching all available volumes..."
    VOLUME_IDS=$(aws ec2 describe-volumes --filters "Name=status,Values=available" --query 'Volumes[*].VolumeId' --output text)
    
    if [ ! -z "$VOLUME_IDS" ]; then
        echo "Available volumes found: $VOLUME_IDS"
        read -p "Do you want to delete all volumes, or specify which ones to delete? (all/specify): " choice
        if [ "$choice" == "all" ]; then
            for volume_id in $VOLUME_IDS; do
                aws ec2 delete-volume --volume-id $volume_id
            done
            echo "All volumes deleted."
        elif [ "$choice" == "specify" ]; then
            read -p "Enter the Volume IDs to delete (separated by spaces): " volume_ids
            for volume_id in $volume_ids; do
                aws ec2 delete-volume --volume-id $volume_id
            done
            echo "Specified volumes deleted."
        fi
    else
        echo "No available volumes found."
    fi
}

function delete_security_groups() {
    echo "Fetching all security groups (excluding default)..."
    SECURITY_GROUP_IDS=$(aws ec2 describe-security-groups --query "SecurityGroups[?GroupName!='default'].GroupId" --output text)
    
    if [ ! -z "$SECURITY_GROUP_IDS" ]; then
        echo "Security groups found: $SECURITY_GROUP_IDS"
        read -p "Do you want to delete all security groups, or specify which ones to delete? (all/specify): " choice
        if [ "$choice" == "all" ]; then
            for sg_id in $SECURITY_GROUP_IDS; do
                aws ec2 delete-security-group --group-id $sg_id
            done
            echo "All security groups deleted."
        elif [ "$choice" == "specify" ]; then
            read -p "Enter the Security Group IDs to delete (separated by spaces): " sg_ids
            for sg_id in $sg_ids; do
                aws ec2 delete-security-group --group-id $sg_id
            done
            echo "Specified security groups deleted."
        fi
    else
        echo "No security groups found."
    fi
}

function delete_key_pairs() {
    echo "Fetching all key pairs..."
    KEY_NAMES=$(aws ec2 describe-key-pairs --query 'KeyPairs[*].KeyName' --output text)
    
    if [ ! -z "$KEY_NAMES" ]; then
        echo "Key pairs found: $KEY_NAMES"
        read -p "Do you want to delete all key pairs, ou specify which ones to delete? (all/specify): " choice
        if [ "$choice" == "all" ];then
            for key_name in $KEY_NAMES; do
                aws ec2 delete-key-pair --key-name $key_name
                rm -f ${key_name}.pem
            done
            echo "All key pairs deleted."
        elif [ "$choice" == "specify" ]; then
            read -p "Enter the Key Names to delete (separated by spaces): " key_names
            for key_name in $key_names; do
                aws ec2 delete-key-pair --key-name $key_name
                rm -f ${key_name}.pem
            done
            echo "Specified key pairs deleted."
        fi
    else
        echo "No key pairs found."
    fi
}

function recursive_cleanup() {
    delete_instances
    delete_volumes
    delete_security_groups
    delete_key_pairs

    echo "Fetching all remaining resources..."
    remaining_instances=$(aws ec2 describe-instances --query 'Reservations[*].Instances[*].InstanceId' --output text)
    remaining_volumes=$(aws ec2 describe-volumes --query 'Volumes[*].VolumeId' --output text)
    remaining_security_groups=$(aws ec2 describe-security-groups --query 'SecurityGroups[*].GroupId' --output text)
    remaining_key_pairs=$(aws ec2 describe-key-pairs --query 'KeyPairs[*].KeyName' --output text)

    echo "Remaining instances: $remaining_instances"
    echo "Remaining volumes: $remaining_volumes"
    echo "Remaining security groups: $remaining_security_groups"
    echo "Remaining key pairs: $remaining_key_pairs"

    read -p "Do you want to continue cleanup? (yes/no): " continue_cleanup
    if [ "$continue_cleanup" == "yes" ]; then
        recursive_cleanup
    else
        echo "Cleanup stopped by user."
        exit 0
    fi
}

# Main execution starts here
echo "Starting recursive cleanup of AWS resources..."
recursive_cleanup
```

---

##### 2. Rendez le script exécutable

Utilisez la commande suivante pour rendre le script exécutable :

```sh
chmod +x cleanup-aws-resources.sh
```

---

##### 3. Exécutez le script

Lancez le script en utilisant la commande suivante :

```sh
sh cleanup-aws-resources.sh
```

---

### Détails des fonctions du script

1. **delete_instances** : Récupère toutes les instances EC2 en cours d'exécution et propose de les terminer, soit toutes, soit en spécifiant les IDs des instances.
2. **delete_volumes** : Récupère tous les volumes disponibles et propose de les supprimer, soit tous, soit en spécifiant les IDs des volumes.
3. **delete_security_groups** : Récupère tous les groupes de sécurité (hors défaut) et propose de les supprimer, soit tous, soit en spécifiant les IDs des groupes.
4. **delete_key_pairs** : Récupère toutes les paires de clés et propose de les supprimer, soit toutes, soit en spécifiant les noms des clés.
5. **recursive_cleanup** : Appelle les fonctions de suppression pour les instances, les volumes, les groupes de sécurité et les paires de clés. Répète le processus tant que des ressources restent à supprimer et que l'utilisateur souhaite continuer.

---

### Fonctionnement du script

Le script commence par vérifier et lister les ressources existantes. Pour chaque type de ressource (instances, volumes, groupes de sécurité, paires de clés), il demande à l'utilisateur s'il souhaite tout supprimer ou spécifier les ressources à supprimer. Après chaque cycle de suppression, il affiche les ressources restantes et demande si l'utilisateur souhaite continuer la suppression. Le processus se répète jusqu'à ce que l'utilisateur choisisse d'arrêter.

---

Ce guide fournit toutes les informations nécessaires pour utiliser le script de suppression de ressources AWS. Suivez les instructions attentivement et assurez-vous que toutes les dépendances et configurations AWS sont correctement mises en place avant de commencer.

---

#### Remarque : Ce guide est conçu pour être un point de départ et peut nécessiter des ajustements en fonction des spécificités de votre environnement AWS et de vos besoins.
