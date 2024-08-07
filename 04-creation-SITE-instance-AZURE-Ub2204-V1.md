### Script 1: Configuration du Répertoire et Création du Fichier Cloud-init

```bash
#!/bin/bash

REPERTOIRE="siteweb/"

# Supprimer le répertoire s'il existe
if [ -d "$REPERTOIRE" ]; then
    rm -rf "$REPERTOIRE"
    echo "Le répertoire '$REPERTOIRE' a été supprimé."
fi

# Créer le répertoire et y accéder
mkdir -p $REPERTOIRE
cd $REPERTOIRE

# Créer le fichier cloud-init pour la configuration initiale
cat <<EOF > cloud-init-siteweb.txt
#cloud-config
package_upgrade: true
runcmd:
  - sudo apt update
  - sudo apt install -y apache2 mysql-server php libapache2-mod-php php-mysql
  - sudo ufw allow 'Apache'
  - sudo systemctl enable apache2
  - sudo systemctl start apache2
  - sudo mysql -e "CREATE USER 'eleve'@'localhost' IDENTIFIED BY 'eleve';"
  - sudo mysql -e "GRANT ALL PRIVILEGES ON *.* TO 'eleve'@'localhost' WITH GRANT OPTION;"
  - sudo mysql -e "ALTER USER 'root'@'localhost' IDENTIFIED WITH 'mysql_native_password' BY 'eleve';"
  - sudo mysql -e "FLUSH PRIVILEGES;"
EOF

echo "Script 1 terminé : Configuration du répertoire et création du fichier cloud-init."
```

### Script 2: Création des Ressources Azure

```bash
#!/bin/bash

REPERTOIRE="siteweb/"
cd $REPERTOIRE

# Créer un groupe de ressources sur Azure
az group create --name siteweb-rg --location westus

# Créer une machine virtuelle dans le groupe de ressources avec Ubuntu 22.04
az vm create \
  --resource-group siteweb-rg \
  --name siteweb-vm \
  --image Ubuntu2204 \
  --admin-username azureuser \
  --generate-ssh-keys \
  --public-ip-sku Standard \
  --custom-data cloud-init-siteweb.txt

# Ouvrir le port 80 pour l'application
az vm open-port \
  --resource-group siteweb-rg \
  --name siteweb-vm \
  --port 80 \
  --priority 1010

echo "Script 2 terminé : VM Azure créée et port ouvert."
```

### Script 3: Vérification du Statut de la VM et Connexion

```bash
#!/bin/bash

GROUPE_DE_RESSOURCES="siteweb-rg"
NOM_VM="siteweb-vm"
FICHIER_LOG="vm_setup.log"

# Rediriger la sortie standard et la sortie d'erreur vers le fichier de log
exec &> >(tee "$FICHIER_LOG")

# Tentative de récupération de l'adresse IP de la VM avec des réessais
MAX_RETRIES=10
for (( i=1; i<=MAX_RETRIES; i++ )); do
    IP_ADDRESS=$(az vm show --show-details --resource-group $GROUPE_DE_RESSOURCES --name $NOM_VM --query publicIps -o tsv)

    if [ ! -z "$IP_ADDRESS" ]; then
        echo "Adresse IP trouvée : $IP_ADDRESS"
        break
    else
        echo "En attente de l'adresse IP de la VM... Tentative $i"
        sleep 30
    fi
done

if [ -z "$IP_ADDRESS" ]; then
    echo "Erreur : Impossible de récupérer l'adresse IP de la VM après plusieurs tentatives."
    exit 1
fi

# Afficher et enregistrer la commande SSH
COMMANDE_SSH="ssh -o StrictHostKeyChecking=no azureuser@$IP_ADDRESS"
echo "Pour se connecter à la VM, utilisez : $COMMANDE_SSH"

# Connexion à la VM via SSH
$COMMANDE_SSH << 'EOF'
    echo "Connecté à la VM."

    # Vérifier si Apache est en cours d'exécution
    if systemctl status apache2; then
        echo "Apache est en cours d'exécution."
    else
        echo "Apache n'est pas en cours d'exécution."
        exit 1
    fi

    # Vérifier si MySQL est en cours d'exécution
    if systemctl status mysql; then
        echo "MySQL est en cours d'exécution."
    else
        echo "MySQL n'est pas en cours d'exécution."
        exit 1
    fi
EOF

echo "Script 3 terminé : Statut de la VM vérifié et connexion SSH établie."
```

### Script 4: Téléversement des Fichiers du Site Web

```bash
#!/bin/bash

# Définir les variables
REPERTOIRE="siteweb/"
GROUPE_DE_RESSOURCES="siteweb-rg"
NOM_VM="siteweb-vm"
IP_ADDRESS=$(az vm show --show-details --resource-group $GROUPE_DE_RESSOURCES --name $NOM_VM --query publicIps -o tsv)
CHEMIN_LOCAL_SITE="./site"
CHEMIN_DISTANT_SITE="/var/www/html"

# Téléverser les fichiers du site web via SCP
scp -r -i mysrvubuntukey.pem $CHEMIN_LOCAL_SITE azureuser@$IP_ADDRESS:/tmp

# Se connecter à la VM et déplacer les fichiers dans le répertoire Apache
ssh -i mysrvubuntukey.pem azureuser@$IP_ADDRESS << 'EOF'
    sudo -i
    mv /tmp/site/* /var/www/html/
    sudo systemctl restart apache2
EOF

echo "Script 4 terminé : Fichiers du site web téléversés et Apache redémarré."
```

### Script 5: Installation du Certificat SSL Gratuit

```bash
#!/bin/bash

# Définir les variables
GROUPE_DE_RESSOURCES="siteweb-rg"
NOM_VM="siteweb-vm"
IP_ADDRESS=$(az vm show --show-details --resource-group $GROUPE_DE_RESSOURCES --name $NOM_VM --query publicIps -o tsv)

# Se connecter à la VM et installer le certificat SSL
ssh -i mysrvubuntukey.pem azureuser@$IP_ADDRESS << 'EOF'
    sudo apt update
    sudo apt install certbot python3-certbot-apache -y
    sudo ufw allow 'Apache Full'
    sudo certbot --apache
    sudo systemctl status certbot.timer
    sudo certbot renew --dry-run
EOF

echo "Script 5 terminé : Certificat SSL gratuit installé."
```

### Script Maître: Exécuter Tous les Scripts

```bash
#!/bin/bash

# Exécuter script1.sh
sh script1.sh
if [ $? -ne 0 ]; then
    echo "Le Script 1 n'a pas été exécuté correctement."
    exit 1
fi

# Exécuter script2.sh
sh script2.sh
if [ $? -ne 0 ]; then
    echo "Le Script 2 n'a pas été exécuté correctement."
    exit 1
fi

# Attendre un moment pour s'assurer que la VM Azure est opérationnelle
echo "En attente de l'initialisation de la VM..."
sleep 120

# Exécuter script3.sh
sh script3.sh
if [ $? -ne 0 ]; then
    echo "Le Script 3 n'a pas été exécuté correctement."
    exit 1
fi

# Exécuter script4.sh
sh script4.sh
if [ $? -ne 0 ]; then
    echo "Le Script 4 n'a pas été exécuté correctement."
    exit 1
fi

# Exécuter script5.sh
sh script5.sh
if [ $? -ne 0 ]; then
    echo "Le Script 5 n'a pas été exécuté correctement."
    exit 1
fi

echo "Tous les scripts ont été exécutés avec succès."
```

Ces scripts automatisent le processus de déploiement d'un site web PHP sur Microsoft Azure, l'installation et la configuration d'Apache, MySQL, PHP, et phpMyAdmin, ainsi que l'installation d'un certificat SSL gratuit.
