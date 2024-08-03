# Script shell pour créer une instance AWS EC2 avec Amazon Linux 2023. 
- Ce script effectue toutes les étapes nécessaires, de la configuration du groupe de sécurité à l'attribution de la paire de clés et au lancement de l'instance.

```sh
#!/bin/bash

# Variables
AMI_ID="ami-01ba8fe702263d044"  # Amazon Linux 2023 AMI ID
INSTANCE_TYPE="t2.micro"
KEY_NAME="aws-linux-2023-key"
SECURITY_GROUP_NAME="aws-linux-2023-sg"
SECURITY_GROUP_DESC="Security group for Amazon Linux 2023 instance"
REGION="us-east-1"  # Change to your preferred region

# Step 1: Create a key pair
echo "Creating a key pair..."
aws ec2 create-key-pair --key-name $KEY_NAME --query 'KeyMaterial' --output text > ${KEY_NAME}.pem
chmod 400 ${KEY_NAME}.pem
echo "Key pair created and saved as ${KEY_NAME}.pem"

# Step 2: Create a security group
echo "Creating a security group..."
SECURITY_GROUP_ID=$(aws ec2 create-security-group --group-name $SECURITY_GROUP_NAME --description "$SECURITY_GROUP_DESC" --output text)
echo "Security group created with ID: $SECURITY_GROUP_ID"

# Step 3: Authorize security group ingress rules
echo "Authorizing security group ingress rules..."
aws ec2 authorize-security-group-ingress --group-id $SECURITY_GROUP_ID --protocol tcp --port 22 --cidr 0.0.0.0/0
aws ec2 authorize-security-group-ingress --group-id $SECURITY_GROUP_ID --protocol tcp --port 80 --cidr 0.0.0.0/0
aws ec2 authorize-security-group-ingress --group-id $SECURITY_GROUP_ID --protocol tcp --port 443 --cidr 0.0.0.0/0
echo "Ingress rules authorized for SSH, HTTP, and HTTPS"

# Step 4: Run an EC2 instance
echo "Running an EC2 instance..."
INSTANCE_ID=$(aws ec2 run-instances --image-id $AMI_ID --count 1 --instance-type $INSTANCE_TYPE --key-name $KEY_NAME --security-group-ids $SECURITY_GROUP_ID --query 'Instances[0].InstanceId' --output text)
echo "Instance launched with ID: $INSTANCE_ID"

# Step 5: Wait for the instance to be in running state
echo "Waiting for the instance to be in running state..."
aws ec2 wait instance-running --instance-ids $INSTANCE_ID
echo "Instance is now running."

# Step 6: Get the public IP address of the instance
PUBLIC_IP=$(aws ec2 describe-instances --instance-ids $INSTANCE_ID --query 'Reservations[0].Instances[0].PublicIpAddress' --output text)
echo "Instance public IP address: $PUBLIC_IP"

# Step 7: Output connection command
echo "To connect to your instance, use the following command:"
echo "ssh -i ${KEY_NAME}.pem ec2-user@${PUBLIC_IP}"
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
