🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
##### 1 - Script shell pour créer une instance AWS EC2 + *BD* + *LANCER LE SITE WEB* avec Amazon Linux 2023. 
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇

- Ce script effectue toutes les étapes nécessaires, de la configuration du groupe de sécurité à l'attribution de la paire de clés et au lancement de l'instance.

```sh
#!/bin/bash

# Variables
AMI_ID="ami-0ba9883b710b05ac6"  # Amazon Linux 2023 AMI ID
INSTANCE_TYPE=""
KEY_NAME=""
SECURITY_GROUP_NAME=""
SECURITY_GROUP_DESC="Security group for Amazon Linux 2023 instance"
REGION="us-east-1"  # Change to your preferred region
LOCAL_AUTH_PHP="Auth.php"
LOCAL_INDEX_PHP="index.php"

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
    EXISTING_KEYS=$(aws ec2 describe-key-pairs --query 'KeyPairs[*].KeyName' --output text)
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
    
    echo "Saving public IP address to instance_ip.txt"
    echo $PUBLIC_IP > instance_ip.txt
}

# Main execution starts here
choose_action

if [ "$choice" == "create" ] || [ "$create_new" == "yes" ]; then
    choose_instance_type
    create_key_pair
    create_security_group
    run_instance
fi

# Fetch public IP address
INSTANCE_IP=$(cat instance_ip.txt)

# Copy files using scp
scp -o StrictHostKeyChecking=no -i "${KEY_NAME}.pem" "$LOCAL_AUTH_PHP" ec2-user@${INSTANCE_IP}:/tmp
scp -o StrictHostKeyChecking=no -i "${KEY_NAME}.pem" "$LOCAL_INDEX_PHP" ec2-user@${INSTANCE_IP}:/tmp

# Script to configure the instance
echo "Fetching public IP address from instance_ip.txt..."
INSTANCE_IP=$(cat instance_ip.txt)

read -p "Enter the username for MySQL (default: eleve): " mysql_user
mysql_user=${mysql_user:-eleve}

read -p "Enter the password for MySQL (default: eleve): " mysql_pass
mysql_pass=${mysql_pass:-eleve}

echo "Connecting to the instance and setting up the environment..."
ssh -o StrictHostKeyChecking=no -i ${KEY_NAME}.pem ec2-user@${INSTANCE_IP} << EOF
sudo yum update -y
sudo yum install httpd* -y
sudo yum install php -y
sudo yum install mariadb* -y

sudo systemctl start httpd
sudo systemctl enable httpd

sudo systemctl start mariadb
sudo systemctl enable mariadb

latest_php_mysqlnd=\$(sudo yum list available php\* | grep 'php[0-9]\.[0-9]-mysqlnd.x86_64' | sort -r | head -n 1 | awk '{print \$1}')
echo "Installing PHP MySQL extension: \$latest_php_mysqlnd"
sudo yum install -y \$latest_php_mysqlnd


sudo mysql -u root -p'$mysql_pass' -e "CREATE USER '$mysql_user'@'localhost' IDENTIFIED BY '$mysql_pass';"
sudo mysql -u root -p'$mysql_pass' -e "GRANT ALL PRIVILEGES ON *.* TO '$mysql_user'@'localhost' WITH GRANT OPTION;"
sudo mysql -u root -p'$mysql_pass' -e "ALTER USER 'root'@'localhost' IDENTIFIED BY '$mysql_pass';"
sudo mysql -u root -p'$mysql_pass' -e "FLUSH PRIVILEGES;"
sudo mysql -u root -p'$mysql_pass' -e "CREATE DATABASE employee;"
sudo mysql -u root -p'$mysql_pass' -e "USE employee; CREATE TABLE emp_info(emp_id int(11),emp_name varchar(50),emp_username varchar(50),emp_password varchar(50),emp_email varchar(50),emp_phone bigint(20));"

# NE FONCTIONNE PAS SANS MOT DE PASSE
# sudo mysql -u root -e "FLUSH PRIVILEGES;"
# sudo mysql -u root -e "CREATE DATABASE employee;"
# sudo mysql -u root -e "USE employee; CREATE TABLE emp_info(emp_id int(11),emp_name varchar(50),emp_username varchar(50),emp_password varchar(50),emp_email varchar(50),emp_phone bigint(20));"

# Move files to the web server directory
sudo mv /tmp/Auth.php /var/www/html/
sudo mv /tmp/index.php /var/www/html/
sudo chown ec2-user:ec2-user /var/www/html/Auth.php /var/www/html/index.php
sudo systemctl restart httpd
EOF

echo "Setup complete. You can now access your instance's web server and PHP files."

```

Pour exécuter ce script :

1. Enregistrez le script dans un fichier, par exemple `launch-ec2-instance.sh`.
   
   ```sh
   cmd
   powershell
   ```

   ```sh
   nano launch-ec2-instance-website-v1.sh
   ```
   
3. Rendez le script exécutable :

   ```sh
   chmod +x launch-ec2-instance-website-v1.sh
   ```

4. Exécutez le script :

   ```sh
   sh launch-ec2-instance-website-v1.sh
   ```

Assurez-vous que l'AWS CLI est configurée correctement avec vos identifiants AWS avant d'exécuter ce script. Remplacez la région par la vôtre si nécessaire.


🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇
#### 2 - Annexe : Guide d'utilisation
🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇🥇


#### 1 - Script shell pour créer une instance AWS EC2 et lancer un site web avec Amazon Linux 2023

Ce guide vous accompagne pas à pas dans l'utilisation du script shell pour créer une instance EC2 sur AWS et déployer un site web basique. Vous apprendrez à configurer les groupes de sécurité, générer des paires de clés, et installer les services nécessaires sur l'instance.

---

##### Prérequis :
1. **AWS CLI** : Assurez-vous que l'interface en ligne de commande AWS est installée et configurée sur votre machine.
2. **Permissions AWS** : Vous devez avoir les permissions nécessaires pour créer des instances EC2, des groupes de sécurité, et des paires de clés.
3. **Scripts PHP** : Préparez les fichiers `Auth.php` et `index.php` sur votre machine locale.

##### Étapes détaillées :

---

##### 1. Enregistrez le script dans un fichier.

Créez un fichier nommé `launch-ec2-instance-website-v1.sh` et copiez le script suivant dans ce fichier :

```sh
#!/bin/bash

# Variables
AMI_ID="ami-0ba9883b710b05ac6"  # Amazon Linux 2023 AMI ID
INSTANCE_TYPE=""
KEY_NAME=""
SECURITY_GROUP_NAME=""
SECURITY_GROUP_DESC="Security group for Amazon Linux 2023 instance"
REGION="us-east-1"  # Change to your preferred region
LOCAL_AUTH_PHP="Auth.php"
LOCAL_INDEX_PHP="index.php"

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
    EXISTING_KEYS=$(aws ec2 describe-key-pairs --query 'KeyPairs[*].KeyName' --output text)
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
    
    echo "Saving public IP address to instance_ip.txt"
    echo $PUBLIC_IP > instance_ip.txt
}

# Main execution starts here
choose_action

if [ "$choice" == "create" ] || [ "$create_new" == "yes" ]; then
    choose_instance_type
    create_key_pair
    create_security_group
    run_instance
fi

# Fetch public IP address
INSTANCE_IP=$(cat instance_ip.txt)

# Copy files using scp
scp -o StrictHostKeyChecking=no -i "${KEY_NAME}.pem" "$LOCAL_AUTH_PHP" ec2-user@${INSTANCE_IP}:/tmp
scp -o StrictHostKeyChecking=no -i "${KEY_NAME}.pem" "$LOCAL_INDEX_PHP" ec2-user@${INSTANCE_IP}:/tmp

# Script to configure the instance
echo "Fetching public IP address from instance_ip.txt..."
INSTANCE_IP=$(cat instance_ip.txt)

read -p "Enter the username for MySQL (default: eleve): " mysql_user
mysql_user=${mysql_user:-eleve}

read -p "Enter the password for MySQL (default: eleve): " mysql_pass
mysql_pass=${mysql_pass:-eleve}

echo "Connecting to the instance and setting up the environment..."
ssh -o StrictHostKeyChecking=no -i ${KEY_NAME}.pem ec2-user@${INSTANCE_IP} << EOF
sudo yum update -y
sudo yum install httpd* -y
sudo yum install php -y
sudo yum install mari

adb* -y

sudo systemctl start httpd
sudo systemctl enable httpd

sudo systemctl start mariadb
sudo systemctl enable mariadb

latest_php_mysqlnd=\$(sudo yum list available php\* | grep 'php[0-9]\.[0-9]-mysqlnd.x86_64' | sort -r | head -n 1 | awk '{print \$1}')
echo "Installing PHP MySQL extension: \$latest_php_mysqlnd"
sudo yum install -y \$latest_php_mysqlnd


sudo mysql -u root -p'$mysql_pass' -e "CREATE USER '$mysql_user'@'localhost' IDENTIFIED BY '$mysql_pass';"
sudo mysql -u root -p'$mysql_pass' -e "GRANT ALL PRIVILEGES ON *.* TO '$mysql_user'@'localhost' WITH GRANT OPTION;"
sudo mysql -u root -p'$mysql_pass' -e "ALTER USER 'root'@'localhost' IDENTIFIED BY '$mysql_pass';"
sudo mysql -u root -p'$mysql_pass' -e "FLUSH PRIVILEGES;"
sudo mysql -u root -p'$mysql_pass' -e "CREATE DATABASE employee;"
sudo mysql -u root -p'$mysql_pass' -e "USE employee; CREATE TABLE emp_info(emp_id int(11),emp_name varchar(50),emp_username varchar(50),emp_password varchar(50),emp_email varchar(50),emp_phone bigint(20));"

# Move files to the web server directory
sudo mv /tmp/Auth.php /var/www/html/
sudo mv /tmp/index.php /var/www/html/
sudo chown ec2-user:ec2-user /var/www/html/Auth.php /var/www/html/index.php
sudo systemctl restart httpd
EOF

echo "Setup complete. You can now access your instance's web server and PHP files."
```

---

##### 2. Rendez le script exécutable

Utilisez la commande suivante pour rendre le script exécutable :

```sh
chmod +x launch-ec2-instance-website-v1.sh
```

---

##### 3. Exécutez le script

Lancez le script en utilisant la commande suivante :

```sh
sh launch-ec2-instance-website-v1.sh
```

---

### Détails des fonctions du script

1. **choose_action** : Vérifie les instances EC2 en cours d'exécution et propose de les arrêter, les terminer, ou créer une nouvelle instance.
2. **choose_instance_type** : Permet de choisir le type d'instance parmi trois options : t2.micro, t2.small, t2.medium.
3. **create_key_pair** : Gère les paires de clés, soit en utilisant une paire existante, soit en en créant une nouvelle.
4. **create_security_group** : Crée un nouveau groupe de sécurité ou utilise un groupe existant et configure les règles de trafic.
5. **run_instance** : Lance une nouvelle instance EC2 et affiche son adresse IP publique pour la connexion.

---

### Connectez-vous à votre instance

Après avoir lancé l'instance EC2, vous pouvez vous y connecter en utilisant la commande SSH fournie à la fin du script. Utilisez l'adresse IP publique et la clé privée générée pour établir la connexion.

---

### Configuration des fichiers PHP

Le script copie les fichiers `Auth.php` et `index.php` dans le répertoire `/var/www/html/` sur l'instance EC2 et configure le serveur web Apache pour les servir.

Vous pouvez maintenant accéder à votre site web en utilisant l'adresse IP publique de l'instance EC2 dans votre navigateur.

---

Ce guide fournit toutes les informations nécessaires pour lancer et configurer une instance EC2 avec un site web de base sur Amazon Linux 2023. Suivez les instructions attentivement et assurez-vous que toutes les dépendances et configurations AWS sont correctement mises en place avant de commencer.

---

#### Remarque : Ce guide est conçu pour être un point de départ et peut nécessiter des ajustements en fonction des spécificités de votre environnement AWS et de vos besoins.

