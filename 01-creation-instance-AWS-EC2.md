# Script shell pour créer une instance AWS EC2 avec Amazon Linux 2023. 
- Ce script effectue toutes les étapes nécessaires, de la configuration du groupe de sécurité à l'attribution de la paire de clés et au lancement de l'instance.

```sh
#!/bin/bash

# Variables
AMI_ID="ami-0ba9883b710b05ac6"  # Amazon Linux 2023 AMI ID correct
INSTANCE_TYPE=""
KEY_NAME=""
SECURITY_GROUP_NAME=""
SECURITY_GROUP_DESC="Security group for Amazon Linux 2023 instance"
REGION="us-east-1"  # Change to your preferred region

function choose_action() {
    echo "Checking for existing running instances..."
    EXISTING_INSTANCES=$(aws ec2 describe-instances --filters "Name=instance-state-name,Values=running" --query 'Reservations[*].Instances[*].InstanceId' --output text)

    if [ ! -z "$EXISTING_INSTANCES" ]; then
        echo "Found existing running instances:"
        echo "$EXISTING_INSTANCES"
        read -p "Do you want to stop, terminate, or create a new instance? (stop/terminate/create): " choice

        case "$choice" in
            stop)
                read -p "Enter the Instance ID to stop: " INSTANCE_ID
                echo "Stopping instance $INSTANCE_ID..."
                aws ec2 stop-instances --instance-ids $INSTANCE_ID
                aws ec2 wait instance-stopped --instance-ids $INSTANCE_ID
                echo "Instance stopped."
                ;;
            terminate)
                read -p "Enter the Instance ID to terminate: " INSTANCE_ID
                echo "Terminating instance $INSTANCE_ID..."
                aws ec2 terminate-instances --instance-ids $INSTANCE_ID
                aws ec2 wait instance-terminated --instance-ids $INSTANCE_ID
                echo "Instance terminated."
                ;;
            create)
                echo "Creating a new instance."
                return 0
                ;;
            *)
                echo "Invalid choice. Exiting."
                exit 1
                ;;
        esac
    else
        echo "No running instances found."
        read -p "Do you want to create a new instance? (yes/no): " create_new
        if [ "$create_new" == "yes" ]; then
            echo "Creating a new instance."
            return 0
        else
            echo "Exiting script."
            exit 0
        fi
    fi
}

function choose_instance_type() {
    echo "Choose the instance type:"
    echo "1) t2.micro"
    echo "2) t2.small"
    echo "3) t2.medium"
    read -p "Enter the number of your choice: " instance_choice

    case "$instance_choice" in
        1)
            INSTANCE_TYPE="t2.micro"
            ;;
        2)
            INSTANCE_TYPE="t2.small"
            ;;
        3)
            INSTANCE_TYPE="t2.medium"
            ;;
        *)
            echo "Invalid choice. Defaulting to t2.micro."
            INSTANCE_TYPE="t2.micro"
            ;;
    esac
    echo "Instance type set to $INSTANCE_TYPE"
}

function create_key_pair() {
    echo "Fetching existing key pairs..."
    aws ec2 describe-key-pairs --query 'KeyPairs[*].KeyName' --output text

    echo "Available key pairs:"
    EXISTING_KEYS=$(aws ec2.describe-key-pairs --query 'KeyPairs[*].KeyName' --output text)
    echo "$EXISTING_KEYS"

    read -p "Do you want to use an existing key pair? (yes/no): " use_existing_key

    if [ "$use_existing_key" == "yes" ]; then
        read -p "Enter the name of the existing key pair: " KEY_NAME
    else
        read -p "Enter a name for the new key pair: " KEY_NAME
        echo "Creating a new key pair..."
        aws ec2 create-key-pair --key-name $KEY_NAME --query 'KeyMaterial' --output text > ${KEY_NAME}.pem
        chmod 400 ${KEY_NAME}.pem
        echo "Key pair created and saved as ${KEY_NAME}.pem"
    fi
}

function create_security_group() {
    echo "Fetching existing security groups..."
    EXISTING_SGS=$(aws ec2 describe-security-groups --query 'SecurityGroups[*].GroupName' --output text)
    echo "Available security groups:"
    echo "$EXISTING_SGS"

    read -p "Do you want to use an existing security group? (yes/no): " use_existing_sg

    if [ "$use_existing_sg" == "yes" ]; then
        read -p "Enter the name of the existing security group: " SECURITY_GROUP_NAME
        SECURITY_GROUP_ID=$(aws ec2 describe-security-groups --group-names $SECURITY_GROUP_NAME --query 'SecurityGroups[*].GroupId' --output text)
    else
        read -p "Enter a name for the new security group: " SECURITY_GROUP_NAME
        echo "Creating a new security group..."
        SECURITY_GROUP_ID=$(aws ec2 create-security-group --group-name $SECURITY_GROUP_NAME --description "$SECURITY_GROUP_DESC" --output text)
        echo "Security group created with ID: $SECURITY_GROUP_ID"

        echo "Authorize traffic:"
        read -p "Authorize SSH traffic? (yes/no): " auth_ssh
        if [ "$auth_ssh" == "yes" ]; then
            aws ec2 authorize-security-group-ingress --group-id $SECURITY_GROUP_ID --protocol tcp --port 22 --cidr 0.0.0.0/0
        fi

        read -p "Authorize HTTP traffic? (yes/no): " auth_http
        if [ "$auth_http" == "yes" ]; then
            aws ec2 authorize-security-group-ingress --group-id $SECURITY_GROUP_ID --protocol tcp --port 80 --cidr 0.0.0.0/0
        fi

        read -p "Authorize All traffic? (yes/no): " auth_all
        if [ "$auth_all" == "yes" ]; then
            aws ec2 authorize-security-group-ingress --group-id $SECURITY_GROUP_ID --protocol all --cidr 0.0.0.0/0
        fi
    fi
}

function run_instance() {
    echo "Running an EC2 instance..."
    INSTANCE_ID=$(aws ec2 run-instances --image-id $AMI_ID --count 1 --instance-type $INSTANCE_TYPE --key-name $KEY_NAME --security-group-ids $SECURITY_GROUP_ID --query 'Instances[0].InstanceId' --output text)
    echo "Instance launched with ID: $INSTANCE_ID"

    echo "Waiting for the instance to be in running state..."
    aws ec2 wait instance-running --instance-ids $INSTANCE_ID
    echo "Instance is now running."

    PUBLIC_IP=$(aws ec2 describe-instances --instance-ids $INSTANCE_ID --query 'Reservations[0].Instances[0].PublicIpAddress' --output text)
    echo "Instance public IP address: $PUBLIC_IP"

    echo "To connect to your instance, use the following command:"
    echo "ssh -i ${KEY_NAME}.pem ec2-user@${PUBLIC_IP}"
}

# Main execution starts here
choose_action

if [ "$choice" == "create" ] || [ "$create_new" == "yes" ]; then
    choose_instance_type
    create_key_pair
    create_security_group
    run_instance
fi
```

Pour exécuter ce script :

1. Enregistrez le script dans un fichier, par exemple `launch-ec2-instance.sh`.
2. Rendez le script exécutable :

   ```sh
   chmod +x launch-ec2-instance.sh
   ```

3. Exécutez le script :

   ```sh
   ./launch-ec2-instance.sh
   ```

Assurez-vous que l'AWS CLI est configurée correctement avec vos identifiants AWS avant d'exécuter ce script. Remplacez la région par la vôtre si nécessaire.


# Annexe : Guide d'utilisation


## Guide d'utilisation du script de création d'une instance EC2 sur AWS

### Description

Ce script shell vous guide à travers le processus de gestion et de création d'une instance EC2 sur AWS. Il vous permet de :

1. Vérifier et gérer les instances EC2 en cours d'exécution.
2. Choisir la taille de l'instance.
3. Utiliser ou créer une nouvelle paire de clés pour accéder à l'instance.
4. Utiliser ou créer un nouveau groupe de sécurité avec des règles de trafic personnalisées.
5. Lancer une nouvelle instance EC2.

### Prérequis

- AWS CLI doit être installé et configuré sur votre machine. Pour configurer AWS CLI, exécutez `aws configure` et suivez les instructions.
- Les autorisations nécessaires pour gérer les instances EC2, les paires de clés et les groupes de sécurité.

### Utilisation du script

1. **Enregistrer le script** :
   Enregistrez le script dans un fichier, par exemple `launch-ec2-instance.sh`.

2. **Rendre le script exécutable** :
   ```sh
   chmod +x launch-ec2-instance.sh
   ```

3. **Exécuter le script** :
   ```sh
   ./launch-ec2-instance.sh
   ```

### Fonctionnalités du script

1. **Vérification des instances en cours d'exécution** :
   - Le script vérifie s'il y a des instances EC2 en cours d'exécution et vous demande si vous souhaitez arrêter, résilier ou créer une nouvelle instance.

2. **Choix de la taille de l'instance** :
   - Le script vous propose trois options pour la taille de l'instance : `t2.micro`, `t2.small`, et `t2.medium`.

3. **Gestion des paires de clés** :
   - Le script liste les paires de clés existantes et vous demande si vous souhaitez en utiliser une existante ou en créer une nouvelle.

4. **Gestion des groupes de sécurité** :
   - Le script liste les groupes de sécurité existants et vous demande si vous souhaitez en utiliser un existant ou en créer un nouveau.
   - Si vous créez un nouveau groupe de sécurité, le script vous demande quelles règles de trafic autoriser (SSH, HTTP, tout trafic).

5. **Lancement de l'instance EC2** :
   - Le script lance une nouvelle instance EC2 avec les paramètres spécifiés et attend qu'elle soit en cours d'exécution.
   - Une fois l'instance lancée, le script affiche l'adresse IP publique de l'instance et la commande SSH pour s'y connecter.

### Détails du script

```sh
#!/bin/bash

# Variables
AMI_ID="ami-0ba9883b710b05ac6"  # Amazon Linux 2023 AMI ID correct
INSTANCE_TYPE=""
KEY_NAME=""
SECURITY_GROUP_NAME=""
SECURITY_GROUP_DESC="Security group for Amazon Linux 2023 instance"
REGION="us-east-1"  # Change to your preferred region

function choose_action() {
    echo "Checking for existing running instances..."
    EXISTING_INSTANCES=$(aws ec2 describe-instances --filters "Name=instance-state-name,Values=running" --query 'Reservations[*].Instances[*].InstanceId' --output text)

    if [ ! -z "$EXISTING_INSTANCES" ]; then
        echo "Found existing running instances:"
        echo "$EXISTING_INSTANCES"
        read -p "Do you want to stop, terminate, or create a new instance? (stop/terminate/create): " choice

        case "$choice" in
            stop)
                read -p "Enter the Instance ID to stop: " INSTANCE_ID
                echo "Stopping instance $INSTANCE_ID..."
                aws ec2 stop-instances --instance-ids $INSTANCE_ID
                aws ec2 wait instance-stopped --instance-ids $INSTANCE_ID
                echo "Instance stopped."
                ;;
            terminate)
                read -p "Enter the Instance ID to terminate: " INSTANCE_ID
                echo "Terminating instance $INSTANCE_ID..."
                aws ec2 terminate-instances --instance-ids $INSTANCE_ID
                aws ec2 wait instance-terminated --instance-ids $INSTANCE_ID
                echo "Instance terminated."
                ;;
            create)
                echo "Creating a new instance."
                return 0
                ;;
            *)
                echo "Invalid choice. Exiting."
                exit 1
                ;;
        esac
    else
        echo "No running instances found."
        read -p "Do you want to create a new instance? (yes/no): " create_new
        if [ "$create_new" == "yes" ]; then
            echo "Creating a new instance."
            return 0
        else
            echo "Exiting script."
            exit 0
        fi
    fi
}

function choose_instance_type() {
    echo "Choose the instance type:"
    echo "1) t2.micro"
    echo "2) t2.small"
    echo "3) t2.medium"
    read -p "Enter the number of your choice: " instance_choice

    case "$instance_choice" in
        1)
            INSTANCE_TYPE="t2.micro"
            ;;
        2)
            INSTANCE_TYPE="t2.small"
            ;;
        3)
            INSTANCE_TYPE="t2.medium"
            ;;
        *)
            echo "Invalid choice. Defaulting to t2.micro."
            INSTANCE_TYPE="t2.micro"
            ;;
    esac
    echo "Instance type set to $INSTANCE_TYPE"
}

function create_key_pair() {
    echo "Fetching existing key pairs..."
    aws ec2 describe-key-pairs --query 'KeyPairs[*].KeyName' --output text

    echo "Available key pairs:"
    EXISTING_KEYS=$(aws ec2.describe-key-pairs --query 'KeyPairs[*].KeyName' --output text)
    echo "$EXISTING_KEYS"

    read -p "Do you want to use an existing key pair? (yes/no): " use_existing_key

    if [ "$use_existing_key" == "yes" ]; then
        read -p "Enter the name of the existing key pair: " KEY_NAME
    else
        read -p "Enter a name for the new key pair: " KEY_NAME
        echo "Creating a new key pair..."
        aws ec2 create-key-pair --key-name $KEY_NAME --query 'KeyMaterial' --output text > ${KEY_NAME}.pem
        chmod 400 ${KEY_NAME}.pem
        echo "Key pair created and saved as ${KEY_NAME}.pem"
    fi
}

function create_security_group() {
    echo "Fetching existing security groups..."
    EXISTING_SGS=$(aws ec2 describe-security-groups --query 'SecurityGroups[*].GroupName' --output text)
    echo "Available security groups:"
    echo "$EXISTING_SGS"

    read -p "Do you want to use an existing security group? (yes/no): " use_existing_sg

    if [ "$use_existing_sg" == "yes" ]; then
        read -p "Enter the name of the existing security group: " SECURITY_GROUP_NAME
        SECURITY_GROUP_ID=$(aws ec2 describe-security-groups --group-names $SECURITY_GROUP_NAME --query 'SecurityGroups[*].GroupId' --output text)
    else
        read -p "Enter a name for the new security group: " SECURITY_GROUP_NAME
        echo "Creating a new security group..."
        SECURITY_GROUP_ID=$(aws ec2 create-security-group --group-name $SECURITY_GROUP_NAME --description "$SECURITY_GROUP_DESC" --output text)
        echo "Security group created with ID: $SECURITY_GROUP_ID"

        echo "Authorize traffic:"
        read -p "Authorize SSH traffic? (yes/no): " auth_ssh
        if [ "$auth_ssh" == "yes" ]; then
            aws ec2 authorize-security-group-ingress --group-id $SECURITY_GROUP_ID --protocol tcp --port 22 --cidr 0.0.0.0/0
        fi

        read -p "Authorize HTTP traffic? (yes/no): " auth_http
        if [ "$auth_http" == "yes" ]; then
            aws ec2 authorize-security-group-ingress --group-id $SECURITY_GROUP_ID --protocol tcp --port 80 --cidr 0.0.0.0/0
        fi

        read -p "Authorize All traffic? (yes/no): " auth_all
        if [ "$auth_all" == "yes" ]; then
            aws ec2 authorize-security-group-ingress --group-id $SECURITY_GROUP_ID --protocol all --cidr 0.0.0.0/0
        fi
    fi
}

function run_instance() {
    echo "Running an EC2 instance..."
    INSTANCE_ID=$(aws ec2 run-instances --image-id $AMI_ID --count 1 --instance-type $INSTANCE_TYPE --key-name $KEY_NAME --security-group-ids $SECURITY_GROUP_ID --query 'Instances[0].InstanceId' --output text)
    echo "Instance launched with ID: $INSTANCE_ID"

    echo "Waiting for the instance to be in running state..."
    aws ec2 wait instance-running --instance-ids $INSTANCE_ID
    echo "Instance is now running."

    PUBLIC_IP=$(aws ec2 describe-instances --instance-ids $INSTANCE_ID --query 'Reservations[0].Instances[0].PublicIpAddress' --output text)
    echo "Instance public IP address: $PUBLIC_IP"

    echo "To connect to your instance, use the following command:"
    echo "ssh -i ${KEY_NAME}.pem ec2-user@${PUBLIC_IP}"
}

# Main execution starts

 here
choose_action

if [ "$choice" == "create" ] || [ "$create_new" == "yes" ]; then
    choose_instance_type
    create_key_pair
    create_security_group
    run_instance
fi
```

### Conclusion

Ce script est un outil complet pour gérer et créer des instances EC2 sur AWS. Il offre une flexibilité en vous permettant de choisir les paramètres de l'instance, les paires de clés et les groupes de sécurité. Assurez-vous d'avoir les autorisations nécessaires et AWS CLI correctement configuré avant d'exécuter ce script.
