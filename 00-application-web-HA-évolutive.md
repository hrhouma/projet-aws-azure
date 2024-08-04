# Application Web Hautement Disponible et Évolutive

- Ce projet nécessite une compréhension des services principaux d'AWS, tels que les services de calcul, de stockage, de réseau et de base de données.
- Le projet exige également une connaissance des meilleures pratiques architecturales, telles que la haute disponibilité, l'évolutivité et la sécurité.
- Les étudiants doivent avoir terminé le cours AWS Academy Cloud Architecting pour acquérir les connaissances nécessaires.
- Les étudiants ayant terminé le cours AWS Academy Cloud Foundations et étant inscrits au cours AWS Academy Cloud Architecting peuvent également essayer de réaliser ce projet avec l'aide des matériaux de cours, des laboratoires pratiques des cours et des conseils des éducateurs.
- La connaissance de n'importe quel langage de programmation, tel que Python ou JavaScript, est un avantage, mais n'est pas obligatoire.
- Votre scénario consiste à planifier, concevoir, construire et déployer l'application web sur le Cloud AWS d'une manière conforme aux meilleures pratiques du cadre AWS Well-Architected. Pendant la période de pointe des admissions, l'application doit prendre en charge des milliers d'utilisateurs et être hautement disponible, évolutive, équilibrée en charge, sécurisée et performante.

# Étapes

1. Créez un diagramme architectural pour illustrer les différents services AWS et leurs interactions entre eux.

2. Estimez le coût de l'utilisation des services en utilisant l'AWS Pricing Calculator.

3. Déployez une application web fonctionnelle qui fonctionne sur une seule machine virtuelle et qui est soutenue par une base de données relationnelle.

4. Concevez une application web pour séparer les couches de l'application, telles que le serveur web et la base de données.

5. Créez un réseau virtuel configuré de manière appropriée pour héberger une application web accessible publiquement et sécurisée.

6. Déployez une application web avec la charge répartie sur plusieurs serveurs web.

7. Configurez les paramètres de sécurité réseau appropriés pour les serveurs web et la base de données.

8. Implémentez la haute disponibilité et l'évolutivité dans la solution déployée.

9. Configurez les autorisations d'accès entre les services AWS.

![image](https://github.com/user-attachments/assets/e1bd4b16-bbf7-42bd-93cb-64205befb26c)

# Caractéristiques 

- Fonctionnel : La solution répond aux exigences fonctionnelles, telles que la possibilité de visualiser, ajouter, supprimer ou modifier les dossiers des étudiants, sans aucun délai perceptible.

- Équilibré en charge : La solution peut correctement équilibrer le trafic des utilisateurs pour éviter les ressources surchargées ou sous-utilisées.

- Évolutif : La solution est conçue pour évoluer afin de répondre aux exigences de l'application.

- Hautement disponible : La solution est conçue pour avoir un temps d'arrêt limité lorsqu'un serveur web devient indisponible.

- Sécurisé : La base de données est sécurisée et ne peut pas être directement accessible depuis les réseaux publics.

- Les serveurs web et la base de données ne peuvent être accessibles que sur les ports appropriés.

- L'application web est accessible sur internet.

- Les identifiants de la base de données ne sont pas codés en dur dans l'application web.

- Optimisé pour les coûts : La solution est conçue pour maintenir les coûts bas.

- Performant : Les opérations courantes (visualisation, ajout, suppression ou modification de dossiers) sont effectuées sans délai perceptible sous des charges normales, variables et maximales.

# Hypothèses

Ce projet sera construit dans un environnement de laboratoire contrôlé qui impose des restrictions sur les services, les fonctionnalités et le budget. Prenez en compte les hypothèses suivantes pour le projet :

L'application est déployée dans une seule région AWS (la solution n'a pas besoin d'être multi-régionale).

Le site web n'a pas besoin d'être disponible en HTTPS ou sur un domaine personnalisé (nous n'utiliserons pas Amazon CloudFront, Route 53).

La solution est déployée sur des machines Ubuntu en utilisant le code JavaScript fourni.

Utilisez le code JavaScript tel qu'il est écrit, sauf si les instructions vous demandent spécifiquement de modifier le code.

La solution utilise des services et des fonctionnalités dans les limites de l'environnement de laboratoire.

La base de données est hébergée dans une seule zone de disponibilité.

Le site web est accessible publiquement sans authentification.

L'estimation du coût est approximative.

**Avertissement :** Une bonne pratique de sécurité consiste à permettre l'accès au site web via le réseau de l'université et l'authentification. Cependant, comme vous construisez cette application en tant que preuve de concept (POC).



# Planification de la conception et estimation des coûts

### Planification de la conception

Vous pouvez utiliser plusieurs outils pour créer des conceptions architecturales. L'un des outils recommandés est Lucidchart. Vous pouvez télécharger des icônes architecturales officielles d'AWS et consulter les diagrammes de référence de l'architecture AWS pour trouver des références.

### Estimation des coûts

Pour estimer les coûts sur AWS, vous pouvez utiliser l'AWS Pricing Calculator. Il est également conseillé de vérifier les coûts des ressources AWS, comme les prix d'Amazon Route 53, pour être certain des coûts, car parfois le calculateur de prix n'est pas mis à jour avec les dernières informations.

**Important :** L'AWS Pricing Calculator fournit uniquement une estimation de vos frais AWS et n'inclut pas les taxes éventuelles. Vos frais réels dépendent de divers facteurs, y compris votre utilisation réelle des services AWS.

### Exemple de planification d'architecture et estimation des coûts

J'ai planifié une partie de l'architecture ainsi qu'un calculateur de prix ci-dessous ; n'hésitez pas à proposer vos propres conceptions :

1. Téléchargez les icônes officielles de l'architecture AWS pour une représentation précise.
2. Utilisez Lucidchart pour créer vos diagrammes architecturaux.
3. Consultez les diagrammes de référence AWS pour vous inspirer et valider vos conceptions.
4. Utilisez l'AWS Pricing Calculator pour estimer les coûts et vérifier les prix des services spécifiques comme Amazon Route 53.

Planifiez soigneusement votre architecture pour garantir qu'elle répond à vos besoins tout en restant dans les limites budgétaires prévues.

![image](https://github.com/user-attachments/assets/af42ee59-ec9a-47a6-81b0-a4974dd63d8a)

![image](https://github.com/user-attachments/assets/81ed7966-9854-4751-9b4b-1bc1e0854857)


## Créer un VPC

Un Virtual Private Cloud (VPC) est un réseau virtuel sur le cloud qui vous permet de connecter et de gérer en toute sécurité des ressources au sein de votre environnement cloud. Il offre une isolation et un contrôle sur votre infrastructure réseau, vous permettant de définir et de configurer les paramètres réseau selon vos besoins spécifiques.

### Étapes pour créer un VPC

1. En haut de la console de gestion AWS, dans la barre de recherche, recherchez et sélectionnez "VPC".
   
2. Cliquez sur "Créer un VPC" et configurez les éléments suivants :

   - **Ressources à créer** : Choisissez "VPC uniquement".
   - **Tag de nom** : Entrez le nom que vous souhaitez définir.
   - **CIDR IPv4** : Entrez `10.0.0.0/16` 
     - **Remarque** : La plage CIDR fournie pour la configuration du VPC est uniquement un exemple. Vous pouvez utiliser une plage différente selon les autorisations de votre environnement de laboratoire.
   
3. Cliquez sur "Créer un VPC".

![image](https://github.com/user-attachments/assets/2c1bade1-e21b-4001-8c21-cbb7a81e03e2)

# Explication du VPC 

Le bloc CIDR VPC dans AWS est un élément crucial pour définir la plage d'adresses IP de votre VPC. Il détermine le nombre d'adresses IP disponibles pour vos ressources et sous-réseaux. Lors du choix d'un bloc CIDR, vous devez prendre en compte la taille, le chevauchement potentiel, les besoins en échelle et les exigences de sous-réseautage de votre VPC.

La notation “10.0.0.0/16” représente un bloc d'adresses IP allant de 10.0.0.0 à 10.0.255.255. Le “/16” indique le masque de sous-réseau, ce qui signifie que les 16 premiers bits de l'adresse IP sont fixes et que les 16 bits restants peuvent être utilisés pour assigner des adresses spécifiques au sein de cette plage. Cela permet d'avoir un total de 65 536 adresses IP possibles dans le sous-réseau 10.0.0.0/16.

En revanche, “10.0.0.0/24” représente un bloc plus petit d'adresses IP allant de 10.0.0.0 à 10.0.0.255. Le masque de sous-réseau “/24” signifie que les 24 premiers bits sont fixes, ne laissant que 8 bits disponibles pour l'adressage. Cela donne un total de 256 adresses IP possibles dans le sous-réseau 10.0.0.0/24.


# Informations Importantes sur le VPC

Le VPC réserve 5 adresses IP, donc vous devez toujours soustraire ces adresses du total d'adresses IP disponibles. Par exemple, pour un bloc de 256 adresses IP, soustrayez 5 adresses, ce qui vous donne 251 adresses IP disponibles.

**Remarque :** Ajoutez plus d'adresses IP pour les ressources privées que pour les ressources publiques.

### Paramètres IPv4 et IPv6

- L'IPv4 est toujours activé par défaut. Vous pouvez activer IPv6, mais vous ne pouvez pas supprimer IPv4.

### Mise à Jour des Paramètres du VPC

1. **Choisissez Actions > Modifier les paramètres du VPC.**
2. **Dans la section des paramètres DNS, sélectionnez Activer les noms d'hôtes DNS.**
3. **Cliquez sur Enregistrer.**

![image](https://github.com/user-attachments/assets/75d89fc1-02d2-43ae-bb11-953ea17e832d)

![image](https://github.com/user-attachments/assets/092adcd7-06db-4fe8-a119-17ea40b42847)


### Création des Composants d'un VPC

Un VPC se compose des éléments suivants :

1. **Sous-réseaux :** Les sous-réseaux sont des subdivisions de la plage d'adresses IP d'un VPC. Ils vous permettent d'isoler logiquement les ressources au sein de votre VPC et de contrôler leur accès. Vous pouvez configurer des tables de routage au niveau du sous-réseau pour contrôler le flux de trafic.

2. **Passerelle Internet :** Une passerelle Internet (IGW) est un composant horizontalement évolutif et redondant qui fournit une connexion entre votre VPC et l'Internet. Elle permet aux ressources de votre VPC de communiquer avec Internet et vice versa.

3. **Tables de routage :** Les tables de routage définissent les règles de routage du trafic au sein de votre VPC. Chaque sous-réseau est associé à une table de routage, qui détermine comment le trafic est dirigé entre les sous-réseaux, l'Internet et d'autres réseaux connectés.

### Configuration d'une Passerelle Internet

1. Dans le volet de navigation, choisissez **Passerelles Internet**.
2. Configurez les éléments suivants :
   - **Choisissez : Créer une passerelle Internet**
   - **Tag de nom :** NomPourVotreIGW
   - **Choisissez : Créer une passerelle Internet**

![image](https://github.com/user-attachments/assets/11d547ee-688f-4098-801b-200ace0043ad)

### Passerelle Internet

Une passerelle Internet (IGW) est un composant horizontalement évolutif et redondant qui fournit une connexion entre votre VPC et l'Internet. Elle permet aux ressources de votre VPC de communiquer avec Internet et vice versa.

### Attacher la Passerelle Internet au VPC

1. **Choisissez Actions > Attacher au VPC.**
2. **VPC disponibles :** Choisissez le VPC que nous avons créé.
3. **Choisissez Attacher la passerelle Internet.**

![image](https://github.com/user-attachments/assets/902ddf7d-81f5-44cf-a69a-bca5bdccbfa0)

![image](https://github.com/user-attachments/assets/a5c20443-a8e4-4de7-8e54-b69ac1c87dd4)

### Création d'un Sous-réseau

1. Dans le volet de navigation, choisissez **Sous-réseaux** et configurez les éléments suivants :
   - **Choisissez : Créer un sous-réseau**
   - **ID du VPC :** Choisissez le VPC que nous avons créé
   - **Nom du sous-réseau :** Entrez `Public Subnet 1`
   - **Zone de disponibilité :** Choisissez la première zone de disponibilité dans la liste déroulante
   - **Bloc CIDR du VPC IPv4 :** Entrez `10.0.0.0/16`
   - **Bloc CIDR du sous-réseau IPv4 :** Entrez `10.0.1.0/24`
   - **Choisissez : Créer un sous-réseau**

![image](https://github.com/user-attachments/assets/b8ed5ac5-4e48-44fc-863b-60fac4273790)

### Sous-réseaux

Les sous-réseaux sont des subdivisions de la plage d'adresses IP d'un VPC. Ils vous permettent d'isoler logiquement les ressources au sein de votre VPC et de contrôler leur accès. Vous pouvez configurer des tables de routage au niveau du sous-réseau pour contrôler le flux de trafic.

### Création d'un Deuxième Sous-réseau

Créez un autre sous-réseau en vous assurant que le bloc CIDR du sous-réseau IPv4 est différent et ne se chevauche pas :

1. Dans le volet de navigation, choisissez **Sous-réseaux** et configurez les éléments suivants :
   - **Choisissez : Créer un sous-réseau**
   - **ID du VPC :** Choisissez le VPC que nous avons créé
   - **Nom du sous-réseau :** Entrez `Public Subnet 2`
   - **Zone de disponibilité :** Choisissez une autre zone de disponibilité dans la liste déroulante pour garantir une haute disponibilité
   - **Bloc CIDR du VPC IPv4 :** Entrez `10.0.0.0/16`
   - **Bloc CIDR du sous-réseau IPv4 :** Entrez `10.0.2.0/24`
   - **Choisissez : Créer un sous-réseau**

### Activer l'Attribution Automatique d'IP Publiques

Nous devons attribuer automatiquement des adresses IPv4 publiques, car l'adresse IP publique n'est pas activée par défaut. Vous devez donc activer l'attribution automatique d'IP publiques :

1. Sélectionnez le sous-réseau que vous venez de créer.
2. Choisissez **Actions > Modifier les paramètres de l'attribution automatique d'IP**.
3. Cochez la case **Activer l'attribution automatique d'IP publiques**.
4. Cliquez sur **Enregistrer**.

![image](https://github.com/user-attachments/assets/d0d774a2-09b5-4bd6-bd11-7ca146415625)

![image](https://github.com/user-attachments/assets/94072f4d-eb43-48cd-9454-9428f50aee28)


### Activation de l'Attribution Automatique d'IP

Assurez-vous d'activer le paramètre d'attribution automatique d'IP, ce qui permet l'allocation automatique d'adresses IPv4 aux instances lancées dans le sous-réseau public. Cela signifie que chaque fois que vous lancez une nouvelle instance dans le sous-réseau, elle se verra automatiquement attribuer une adresse IPv4 publique unique. Cela élimine le besoin de configuration manuelle et garantit que chaque instance dispose d'une adresse distincte pour les communications.

Fait amusant : Lorsque vous arrêtez et redémarrez votre instance, l'adresse IP publique change, mais l'adresse IP privée reste la même.

### Table de Routage

Donnez à votre table de routage le nom `PublicRouteTable` et sélectionnez le VPC que nous avons créé.

![image](https://github.com/user-attachments/assets/19e24357-4865-4d3d-a1df-4cfc7fc45201)

Cliquez sur le bouton "Créer" pour terminer le processus.

Une table de routage publique dans AWS est une ressource réseau qui contrôle le flux de trafic entre les sous-réseaux au sein d'un cloud privé virtuel (VPC) et l'internet. Elle contient un ensemble de règles, appelées routes, qui déterminent comment le trafic est dirigé. Dans cette section, nous explorerons les caractéristiques clés et les composants d'une table de routage publique AWS.

1. Route → Modifier les routes → (destination `0.0.0.0/0` signifie n'importe où) (cible : passerelle Internet)

![image](https://github.com/user-attachments/assets/259ea0a2-9aec-4c86-8ec6-0655be411ce3)

![image](https://github.com/user-attachments/assets/e177dc21-c360-47fc-a82a-cf60e3555ff4)

En ce qui concerne le routage du trafic, les tables de routage publiques AWS fonctionnent de manière similaire aux tables de routage traditionnelles. Elles utilisent un modèle de routage basé sur la destination, où chaque route dans la table spécifie un bloc CIDR de destination et une cible. La cible peut être une passerelle Internet, un sous-réseau ayant une attribution automatique d'IPv4 publique.

Nous devons maintenant modifier les associations de sous-réseaux explicites (attacher le sous-réseau à notre table de routage).

1. **Route → Modifier les routes** : 
   - **Destination** : `0.0.0.0/0` (signifie n'importe où)
   - **Cible** : Passerelle Internet (Internet Gateway)

2. **Modifier les associations de sous-réseaux** :
   - Attachez le sous-réseau à notre table de routage nouvellement créée.

![image](https://github.com/user-attachments/assets/89a52c55-1b8d-47bf-aa01-af056d8ce8f7)

![image](https://github.com/user-attachments/assets/4f92b9c0-8901-4225-8279-219d2e00eb2e)

### Associations Explicites de Sous-réseaux

1. **Modifier les associations de sous-réseaux.**

2. **Sélectionner et ajouter un sous-réseau public** pour l'attacher à la table de routage.


![image](https://github.com/user-attachments/assets/2ccd2126-c831-4b46-b013-810c799c18c4)

Une fois que vous avez créé une table de routage publique, vous pouvez configurer ses routes et l'associer à des sous-réseaux au sein de votre VPC. En associant un sous-réseau à une table de routage, vous permettez aux sous-réseaux d'utiliser les routes définies dans la table pour le routage du trafic.

Il est important de noter qu'un VPC peut avoir plusieurs tables de routage, mais chaque sous-réseau ne peut être associé qu'à une seule table de routage à la fois. Cela vous permet d'avoir des configurations de routage différentes pour différents sous-réseaux au sein de votre VPC.


### Création d'un Sous-réseau Privé Associé à une Table de Routage Privée

1. Suivez la même procédure que pour la création d'un sous-réseau public, mais pour le sous-réseau privé.
   
2. Assurez-vous que le bloc CIDR soit différent ainsi que la zone de disponibilité. Exemple :
   - **Sous-réseau privé 1** : `10.0.3.0/24`, AZ `1a`
   - **Sous-réseau privé 2** : `10.0.4.0/24`, AZ `1b`

**Remarque importante :** Pas besoin d'attribuer automatiquement des adresses IPv4 publiques, car il s'agit d'un sous-réseau privé et il n'est donc pas nécessaire d'avoir une adresse IPv4 publique.

### Création d'une Table de Routage Privée

1. Dans le tableau de bord Amazon VPC, cliquez sur **Tables de routage** dans le volet de navigation à gauche.

![image](https://github.com/user-attachments/assets/b1d3c2f7-2b79-455e-b505-662a635b0113)


### Création et Configuration d'une Table de Routage Privée

1. Cliquez sur le bouton **Créer une table de routage**.
2. Dans le champ **Tag de nom**, entrez un nom descriptif pour votre table de routage (par exemple, "Table de routage privée").
3. Dans le menu déroulant **VPC**, sélectionnez le VPC que nous avons créé.
4. Cliquez sur le bouton **Créer** pour créer la table de routage.

![image](https://github.com/user-attachments/assets/7f6edcdb-3076-40c5-99ab-d0952b9e4786)

![image](https://github.com/user-attachments/assets/28ae0652-f2d3-4343-82a7-372265151c43)




### Étape 4 : Associer des Sous-réseaux à la Table de Routage

1. Dans le tableau de bord **Tables de routage**, localisez la table de routage privée nouvellement créée.
2. Cliquez sur le bouton **Actions** et sélectionnez **Modifier les associations de sous-réseaux**.
3. Dans la section **Associations de sous-réseaux**, cliquez sur le bouton **Ajouter un sous-réseau**.
4. Sélectionnez les sous-réseaux privés que vous souhaitez associer à la table de routage privée.
5. Cliquez sur le bouton **Enregistrer** pour associer les sous-réseaux à la table de routage.

![image](https://github.com/user-attachments/assets/0401f723-65f9-42ff-83cb-317e98eea555)

![image](https://github.com/user-attachments/assets/c964f3a6-8d20-4720-9636-dd969d797287)

![image](https://github.com/user-attachments/assets/1312eb73-b6f5-4a54-b0e2-23b5cf5c0459)



### Remarques Importantes pour les Sous-réseaux Privés

- Les sous-réseaux privés n'ont pas d'attribution automatique d'IPv4 publique.
- La table de routage ne doit pas inclure de route vers la passerelle Internet, ce qui signifie que l'accès à Internet n'est pas possible. Seul le trafic local dans le réseau VPC, qui est isolé logiquement, est autorisé.

Dans le champ **Destination**, entrez le bloc CIDR pour le trafic réseau que vous souhaitez router (par exemple, `10.0.0.0/16`). Le réseau se déplacera uniquement en interne dans le bloc CIDR du réseau VPC.


### Création d'une instance EC2 pour notre application web

1. Cliquez sur **"Launch Instance"** pour démarrer le processus de création d'instance.

![image](https://github.com/user-attachments/assets/4fd1249d-b80b-4784-bd0e-1b50adf7f422)

2. Dans la section **Application and OS Images**, sous **Quick Start**, choisissez **Ubuntu**.

![image](https://github.com/user-attachments/assets/64834f85-9c9f-4d14-bf32-231d0299b834)

3. Sélectionnez le type d'instance souhaité et cliquez sur **"Next: Configure Instance Details"**. Utilisez `t2.micro` pour le niveau gratuit (juste pour la preuve de concept, pour un site de production, le type d'instance sera augmenté en fonction des spécifications des besoins de l'entreprise).
4. Dans la section **Key pair**, pour **Key pair name**, choisissez `vockey`.
5. Dans la section **Network settings**, configurez les éléments suivants :
   - Choisissez **Edit**.
   - **VPC** : Choisissez le VPC que nous avons créé.
   - **Auto-assign public IP** : Choisissez **Enable**.
   
   **Remarque :** Cela permet à l'instance d'avoir une adresse IP publique attribuée automatiquement. Cela permettra à l'instance de communiquer sur Internet.

6. **Firewall (security groups)** : Choisissez **Create security group**.
   - **Nom du groupe de sécurité** : Entrez le nom du groupe de sécurité souhaité.
   - Choisissez **Add security group rule**.
   - Gardez la règle SSH existante et ajoutez deux nouvelles règles avec les paramètres suivants :
     - **Nouvelle règle 1** : Pour **Type**, choisissez **HTTP**. Pour **Source type**, choisissez **Anywhere**. 
       **Remarque :** Cette règle permet le trafic depuis un navigateur web.
     - **Nouvelle règle 2** : Pour **Type**, choisissez **MYSQL/Aurora**. Pour **Source**, entrez `10.0.0.0/16`.
       **Remarque :** Cette règle permet d'exporter des données depuis la base de données lors d'une tâche ultérieure.

7. Dans la page **Advanced details**, vous pouvez également spécifier un profil d'instance IAM si vous souhaitez que l'instance communique avec AWS Secrets Manager ou tout autre service AWS. Cela permet à l'instance d'avoir les autorisations nécessaires pour accéder aux secrets de manière sécurisée.

8. Enfin, vous pouvez fournir des **données utilisateur** sur la page **Configure Instance Details**. Les données utilisateur sont un script qui s'exécute sur l'instance pendant le processus de démarrage. Vous pouvez spécifier les étapes nécessaires à effectuer après le lancement de l'instance.

```bash
#!/bin/bash -xe
apt update -y
apt install nodejs unzip wget npm mysql-client -y
#wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACCAP1-1-DEV/code.zip -P /home/ubuntu
wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACCAP1-1-79581/1-lab-capstone-project-1/code.zip -P /home/ubuntu
cd /home/ubuntu
unzip code.zip -x "resources/codebase_partner/node_modules/*"
cd resources/codebase_partner
npm install aws aws-sdk
export APP_PORT=80
npm start &
echo '#!/bin/bash -xe
cd /home/ubuntu/resources/codebase_partner
export APP_PORT=80
npm start' > /etc/rc.local
chmod +x /etc/rc.local
```

9. Vérifiez toutes les configurations et cliquez sur **"Launch"** pour créer l'instance EC2.

**Important :** Vérifiez la mise en forme du script après l'avoir copié dans le champ des données utilisateur. Si les lignes de code semblent être coupées.

**Remarque :** Ce script installe Node.js, l'application de gestion des dossiers étudiants (site web, JavaScript, CSS et autres fichiers), et la base de données MySQL sur l'instance EC2.

**Important :** Avant de passer à la tâche suivante, confirmez que l'instance est en état **Running** et que la colonne **Status check** indique "2/2 checks passed." Cela prendra quelques minutes.















