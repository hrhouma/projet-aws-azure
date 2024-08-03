Pour accomplir toutes les tâches spécifiées, nous allons créer un script shell qui :

1. Récupère l'adresse IP publique de l'instance EC2 nouvellement créée.
2. Se connecte à l'instance via SSH.
3. Installe Apache, PHP, et MariaDB.
4. Configure MariaDB selon les instructions fournies.
5. Crée les fichiers PHP `Auth.php` et `index.php` dans `/var/www/html`.

Voici le script :

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

sudo systemctl status httpd
sudo systemctl start httpd
sudo systemctl enable httpd

sudo systemctl status mariadb
sudo systemctl start mariadb
sudo systemctl enable mariadb

sudo yum list available php\*
latest_php=\$(sudo yum list available php\* | grep 'php[0-9]\.\[0-9\]-mysqlnd.x86_64' | sort -r | head -n 1 | awk '{print \$1}')
sudo yum install -y \$latest_php

sudo mysql -e "CREATE USER '$mysql_user'@'localhost' IDENTIFIED BY '$mysql_pass';"
sudo mysql -e "GRANT ALL PRIVILEGES ON *.* TO '$mysql_user'@'localhost' WITH GRANT OPTION;"
sudo mysql -e "ALTER USER 'root'@'localhost' IDENTIFIED BY '$mysql_pass';"
sudo mysql -e "FLUSH PRIVILEGES;"
sudo mysql -e "CREATE DATABASE employee;"
sudo mysql -e "USE employee; CREATE TABLE emp_info(emp_id int(11),emp_name varchar(50),emp_username varchar(50),emp_password varchar(

50),emp_email varchar(50),emp_phone bigint(20));"

sudo tee /var/www/html/Auth.php > /dev/null <<EOT
<?php
if(isset(\$_GET['id']))
{
    \$link = mysqli_connect("localhost", "$mysql_user", "$mysql_pass", "employee");
    mysqli_query(\$link, "select * from emp_info where emp_email='\$_GET[id]'");
    if(mysqli_affected_rows(\$link) > 0)
    {
        echo "Email-Id Already Exist";
    }
    mysqli_close(\$link);
}
if(isset(\$_GET['id1']))
{
    \$link = mysqli_connect("localhost", "$mysql_user", "$mysql_pass", "employee");
    mysqli_query(\$link, "select * from emp_info where emp_username='\$_GET[id1]'");
    if(mysqli_affected_rows(\$link) > 0)
    {
        echo "Username Already Exist";
    }
    mysqli_close(\$link);
}
?>
EOT

sudo tee /var/www/html/index.php > /dev/null <<EOT
<?php
\$msg = "";
if(isset(\$_GET['reg']))
{
    \$link = mysqli_connect("localhost", "$mysql_user", "$mysql_pass", "employee");
    \$qry = "insert into emp_info values(\$_GET[txt_id],'\$_GET[txt_name]','\$_GET[txt_uname]','\$_GET[txt_pwd]','\$_GET[txt_email]',\$_GET[txt_no])";
    mysqli_query(\$link, \$qry);
    if(mysqli_affected_rows(\$link) > 0)
    {
        \$msg = "<font color='green' size='5px'>Registration Done.....</font>";
    }
    else
    {
        \$msg = "<font color='red' size='5px'>Registration Fail.....</font>";
    }
    mysqli_close(\$link);
}
?>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title></title>
    <script>
        temp = 0;
        temp1 = 0;
        function CheckEmail()
        {
            temp = 0;
            email = document.getElementById('email').value;
            ht = new XMLHttpRequest();
            ht.open("get", "Auth.php?id=" + email, true);
            ht.send();
            ht.onreadystatechange = function() {
                if(ht.readyState == 4 && ht.status == 200)
                {
                    document.getElementById("d1").innerHTML = ht.responseText;
                    if(ht.responseText == "Email-Id Already Exist")
                        temp = 1;
                }
            }
        }
        function CheckUsername()
        {
            temp1 = 0;
            uname = document.getElementById('uname').value;
            ht1 = new XMLHttpRequest();
            ht1.open("get", "Auth.php?id1=" + uname, true);
            ht1.send();
            ht1.onreadystatechange = function() {
                if(ht1.readyState == 4 && ht1.status == 200)
                {
                    document.getElementById("d2").innerHTML = ht1.responseText;
                    if(document.getElementById("d2").innerHTML == "Username Already Exist")
                    { temp1 = 1; }
                }
            }
        }
        function validate()
        {
            if(temp == 1)
                return false;
            if(temp1 == 1)
                return false;
            else
                return true;
        }
    </script>
    <style>
        .mystyle {
            border-radius: 10px;
            padding-left: 5px;
            height: 25px;
            width: 200px;
            font-size: 15px;
        }
        .style1 {
            width: 120px;
            height: 30px;
            border-radius: 10px;
            padding-left: 5px;
            font-family: Time In Roman;
            font-size: 20px;
        }
        label {
            font-family: Time In Roman;
            font-size: 20px;
        }
    </style>
</head>
<body>
    <hr size="4" color="green"/>
    <h2 style="font-size: 30px; color: maroon; font-family: Time In Roman; text-align: center"><br>Employee Registration Form<br></h2>
    <hr size="4" color="green"/><br>
    <div align="center"><form method="get" onsubmit="return validate()">
        <table width="50%">
        <tr>
            <td><label>Employee ID</label></td>
            <td><input class="mystyle" type="text" name="txt_id" value="" required=""/></td>
        </tr>
        <tr>
            <td><label>Employee Name</label></td>
            <td><input class="mystyle" type="text" name="txt_name" value="" required=""/></td>
        </tr>
        <tr>
            <td><label>Username</label></td>
            <td><input class="mystyle" id="uname" type="text" name="txt_uname" value="" onchange="CheckUsername()" required=""/><br><div id="d2" style="color:red"></div></td>
        </tr>
        <tr>
            <td><label>Password</label></td>
            <td><input class="mystyle" type="password" name="txt_pwd" value="" required=""/></td>
        </tr>
        <tr>
            <td><label>Email ID</label></td>
            <td><input class="mystyle" id="email" type="email" name="txt_email" onchange="CheckEmail()" value="" required=""/><br><div id="d1" style="color:red"></div></td>
        </tr>
        <tr>
            <td><label>Phone Number</label></td>
            <td><input class="mystyle" type="text" name="txt_no" value="" required=""/></td>
        </tr>
        <tr>
            <td></td>
            <td><input class="style1" type="submit" value="Register" name="reg"/></td>
        </tr>
        <tr>
            <td colspan="2"><?php echo \$msg; ?></td>
        </tr>
        </table>
    </form></div>
</body>
</html>
EOT
EOF

echo "Setup complete. You can now access your instance's web server and PHP files."
```

### Explication du script

1. **Lancement de l'instance EC2** :
    - Le script principal crée une instance EC2, une paire de clés, et un groupe de sécurité selon les choix de l'utilisateur.
    - L'adresse IP publique de l'instance est enregistrée dans un fichier `instance_ip.txt`.

2. **Configuration de l'instance** :
    - Le script récupère l'adresse IP publique de l'instance à partir du fichier `instance_ip.txt`.
    - Demande les informations d'identification MySQL (nom d'utilisateur et mot de passe), avec des valeurs par défaut (`eleve`).
    - Se connecte à l'instance via SSH et exécute les commandes pour installer Apache, PHP, et MariaDB.
    - Configure MariaDB avec les instructions fournies.
    - Crée les fichiers PHP `Auth.php` et `index.php` dans `/var/www/html`, en utilisant les informations d'identification MySQL fournies.

Pour exécuter ce script, suivez les étapes précédentes pour enregistrer et rendre le script exécutable, puis exécutez-le avec :

```sh
./launch-ec2-instance.sh
```

### Note

Assurez-vous que l'AWS CLI est configurée correctement avec vos identifiants AWS et que vous avez les autorisations nécessaires pour gérer les instances EC2, les paires de clés, et les groupes de sécurité.
