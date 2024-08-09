# Introduction à AWS Identity and Access Management (IAM)

# 1. **Qu'est-ce qu'AWS IAM ?**
   - **AWS Identity and Access Management (IAM)** est un service web qui vous permet de contrôler de manière sécurisée l'accès aux services et ressources AWS pour vos utilisateurs. Grâce à IAM, vous pouvez créer et gérer des utilisateurs et des groupes AWS, et utiliser des autorisations pour autoriser ou refuser l'accès à vos ressources AWS.

# 2. **Pourquoi IAM est-il important ?**
   - **Sécurité** : IAM vous permet de définir qui peut accéder à vos ressources AWS et ce qu'ils peuvent faire avec ces ressources.
   - **Gestion centralisée des accès** : IAM vous aide à gérer les utilisateurs et les groupes dans un seul endroit.
   - **Accès détaillé** : IAM permet d'appliquer des autorisations granulaires, ce qui signifie que vous pouvez autoriser ou restreindre des actions spécifiques sur des ressources spécifiques.

# 3. **Concepts clés d'IAM**

   - **Utilisateurs IAM** : Représentent des personnes ou des services qui interagissent avec AWS. Chaque utilisateur a une identité unique et des informations d'identification.
   - **Groupes IAM** : Ce sont des ensembles d'utilisateurs ayant des permissions similaires. Les autorisations sont attribuées à un groupe, et tous les utilisateurs du groupe héritent de ces autorisations.
   - **Rôles IAM** : Un rôle est une entité IAM que vous pouvez créer dans votre compte pour accorder des autorisations aux services AWS ou aux utilisateurs.
   - **Politiques IAM** : Les politiques sont des documents JSON qui définissent les autorisations. Elles spécifient quelles actions sont autorisées ou refusées pour quel service et quelles ressources.

# 4. **Comment fonctionne IAM ?**
   - **Création d'utilisateurs et de groupes** : Vous pouvez créer des utilisateurs et les regrouper pour attribuer des autorisations spécifiques.
   - **Application des politiques** : Des politiques sont attachées aux utilisateurs, groupes, ou rôles pour contrôler l'accès aux ressources.
   - **Authentification et autorisation** : Les utilisateurs se connectent avec des informations d'identification uniques et les politiques définissent les actions qu'ils peuvent effectuer.

# 5. **Pratiques recommandées pour l'utilisation d'IAM**

   - **Utiliser le principe du moindre privilège** : Accorder uniquement les autorisations nécessaires aux utilisateurs pour accomplir leur travail.
   - **Activer l'authentification multi-facteurs (MFA)** : Ajouter une couche de sécurité supplémentaire pour protéger vos comptes IAM.
   - **Utiliser des rôles IAM pour les applications** : Les rôles permettent de gérer les autorisations de manière dynamique pour les services AWS ou les instances EC2.
   - **Surveiller et auditer les activités IAM** : Utiliser AWS CloudTrail pour surveiller les actions effectuées avec IAM afin de garantir la sécurité et la conformité.

# 6. **Exemple pratique : Création d'un utilisateur IAM et d'une politique**

   **Étape 1 : Créer un utilisateur**
   - Accédez à la console IAM.
   - Sélectionnez "Users" et cliquez sur "Add user".
   - Entrez un nom d'utilisateur et sélectionnez le type d'accès (AWS Management Console ou programmatique).
   - Configurez les autorisations pour l'utilisateur.

   **Étape 2 : Créer une politique**
   - Sélectionnez "Policies" et cliquez sur "Create policy".
   - Choisissez un service (par exemple, S3), une action (par exemple, ListBucket), et spécifiez les ressources.
   - Attachez cette politique à l'utilisateur ou au groupe.

# 7. **Conclusion**
   - IAM est un service essentiel pour gérer de manière sécurisée l'accès aux ressources AWS. Il offre une gestion fine des autorisations, facilitant ainsi la protection de vos données et services. En suivant les bonnes pratiques et en configurant IAM correctement, vous pouvez garantir que seuls les utilisateurs autorisés peuvent accéder aux ressources critiques de votre infrastructure AWS. 

# Annexe 1 - Résumé des Objectifs du Lab : Introduction à AWS IAM

### Résumé détaillé des Objectifs du Lab : Introduction à AWS IAM

**Objectif principal :**
Ce laboratoire a pour objectif de vous initier aux concepts fondamentaux et aux opérations de base d'AWS Identity and Access Management (IAM). À travers une série d'exercices pratiques, vous apprendrez à explorer, manipuler et tester les utilisateurs, groupes, et politiques IAM pour gérer l'accès aux ressources AWS de manière sécurisée.

**Objectifs spécifiques :**

1. **Exploration des utilisateurs et groupes IAM pré-créés :**
   - **Découverte des utilisateurs IAM :** Vous explorerez les utilisateurs préexistants dans votre compte AWS, tels que `user-1`, `user-2`, et `user-3`.
   - **Analyse des détails des utilisateurs :** Vous inspecterez les autorisations, l'appartenance à des groupes, et les informations d'identification de sécurité de chaque utilisateur.

2. **Inspection des politiques IAM appliquées aux groupes :**
   - **Identification des groupes IAM pré-créés :** Vous découvrirez des groupes comme `EC2-Admin`, `EC2-Support`, et `S3-Support`.
   - **Analyse des politiques gérées :** Vous examinerez les politiques gérées, comme `AmazonEC2ReadOnlyAccess` et `AmazonS3ReadOnlyAccess`, associées à ces groupes.
   - **Exploration des politiques en ligne :** Vous verrez comment des politiques spécifiques peuvent être créées et appliquées directement à des utilisateurs ou groupes particuliers pour des cas d'usage uniques.

3. **Ajout d'utilisateurs à des groupes :**
   - **Scénario métier :** Vous suivrez un scénario où vous devez attribuer des rôles spécifiques à chaque utilisateur en les ajoutant aux groupes adéquats, par exemple :
     - `user-1` sera ajouté au groupe `S3-Support` pour avoir un accès en lecture seule à Amazon S3.
     - `user-2` sera ajouté au groupe `EC2-Support` pour avoir un accès en lecture seule à Amazon EC2.
     - `user-3` sera ajouté au groupe `EC2-Admin` pour pouvoir gérer les instances EC2 (afficher, démarrer et arrêter les instances).
   - **Héritage des autorisations :** En ajoutant des utilisateurs à des groupes, ils hériteront des autorisations définies par les politiques associées à ces groupes.

4. **Localisation et utilisation de l'URL de connexion IAM :**
   - **URL de connexion IAM :** Vous apprendrez à localiser l'URL de connexion spécifique pour les utilisateurs IAM dans votre compte AWS.
   - **Connexion en tant qu'utilisateur IAM :** Vous utiliserez cette URL pour vous connecter à la Console de gestion AWS en tant que différents utilisateurs, testant ainsi les permissions assignées.

5. **Test des effets des politiques sur l'accès aux services :**
   - **Connexion en tant que `user-1` :** Vous testerez l'accès de `user-1` aux services AWS comme S3 et EC2 pour vérifier que les permissions sont correctement appliquées (lecture seule sur S3, accès refusé sur EC2).
   - **Connexion en tant que `user-2` :** Vous vérifierez que `user-2` peut voir les instances EC2 mais ne peut pas les modifier, et que l'accès à S3 est refusé.
   - **Connexion en tant que `user-3` :** Vous testerez les capacités d'administration de `user-3` sur EC2, y compris l'arrêt des instances, tout en vérifiant que les permissions sont correctement restreintes.

**Schéma ASCII pour visualiser les relations entre utilisateurs, groupes, et politiques :**

```plaintext
+-------------------------+
|      AWS IAM            |
+-------------------------+
            |
            v
+--------------------------+         +----------------------------+
|   Utilisateurs IAM       |         |    Groupes IAM              |
|                          |         |                             |
| +--------+  +--------+   |         | +------------------------+  |
| | user-1 |  | user-2 |   |<------->| |     S3-Support          |  |
| +--------+  +--------+   |         | | (AmazonS3ReadOnlyAccess)|  |
|     |            |       |         | +------------------------+  |
|     v            v       |         |                             |
| +--------------------+   |         | +------------------------+  |
| |   user-3           |   |<------->| |    EC2-Support          |  |
| +--------------------+   |         | | (AmazonEC2ReadOnlyAccess)| |
|     |                    |         | +------------------------+  |
|     v                    |         |                             |
| +--------------------+   |<------->| +------------------------+  |
| | EC2-Admin (inline  |   |         | |    EC2-Admin            |  |
| | policy)            |   |         | | (Custom inline policy)  |  |
| +--------------------+   |         | +------------------------+  |
+--------------------------+         +----------------------------+
```

# Annexe 2 - Glossaire des services AWS les plus utilisés


| **Service**             | **Description**                                                                                                                                   | **Exemples**                                                                                                                                                                |
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Utilisateurs IAM**     | Représentent des personnes ou des services qui interagissent avec AWS. Chaque utilisateur a une identité unique et des informations d'identification. | Exemple : Vous pouvez créer un utilisateur IAM pour un développeur dans votre entreprise afin qu'il puisse accéder à certaines ressources AWS nécessaires à son travail.     |
| **Groupes IAM**          | Ce sont des ensembles d'utilisateurs ayant des permissions similaires. Les autorisations sont attribuées à un groupe, et tous les utilisateurs du groupe héritent de ces autorisations. | Exemple : Vous pouvez créer un groupe IAM appelé "Développeurs" avec des autorisations d'accès en lecture aux compartiments S3, et ajouter tous les développeurs à ce groupe. |
| **Rôles IAM**            | Un rôle est une entité IAM que vous pouvez créer dans votre compte pour accorder des autorisations aux services AWS ou aux utilisateurs.              | Exemple : Vous pouvez créer un rôle IAM pour une instance EC2 qui lui permet d'accéder à un compartiment S3 sans avoir besoin de stocker des clés d'accès sur l'instance.     |
| **Politiques IAM**       | Les politiques sont des documents JSON qui définissent les autorisations. Elles spécifient quelles actions sont autorisées ou refusées pour quel service et quelles ressources. | Exemple : Une politique IAM peut autoriser un utilisateur à lister les objets dans un compartiment S3, mais lui refuser la possibilité de supprimer des objets.               |
| **Amazon EC2 (Elastic Compute Cloud)** | Service de calcul en nuage qui permet de lancer et gérer des instances de serveurs virtuels (VMs) dans le cloud.                                                             | Exemple : Vous pouvez utiliser EC2 pour héberger un site web sur un serveur Linux, en lançant une instance EC2 avec Apache installé.                                         |
| **Amazon S3 (Simple Storage Service)** | Service de stockage d'objets évolutif permettant de stocker et récupérer des données à tout moment, depuis n'importe où sur le web.                                           | Exemple : Vous pouvez stocker des fichiers de sauvegarde, des images ou des vidéos dans un compartiment S3 et y accéder depuis votre application ou site web.                |
| **AWS CloudTrail**       | Service qui enregistre les appels d'API effectués sur votre compte AWS, permettant l'audit des activités et la surveillance de la sécurité.                                                | Exemple : Vous pouvez utiliser CloudTrail pour suivre les actions de modification effectuées sur les ressources AWS, comme la suppression d'une instance EC2.                |
| **AWS CloudWatch**       | Service de surveillance et d'observation pour AWS, qui collecte et suit des mesures, enregistre des fichiers journaux, et configure des alarmes.                                        | Exemple : CloudWatch peut être utilisé pour surveiller la santé d'une instance EC2 en envoyant une alerte si l'utilisation du CPU dépasse un certain seuil.                  |
| **Amazon RDS (Relational Database Service)** | Service de base de données managé qui permet de configurer, d'exploiter et de mettre à l'échelle des bases de données relationnelles dans le cloud.                        | Exemple : Utilisez Amazon RDS pour déployer une base de données MySQL pour votre application sans avoir à gérer le matériel sous-jacent ou la mise à jour logicielle.         |
| **AWS Lambda**           | Service de calcul sans serveur qui exécute du code en réponse à des événements et gère automatiquement les ressources de calcul nécessaires.                                               | Exemple : Vous pouvez utiliser AWS Lambda pour exécuter une fonction qui redimensionne les images téléchargées sur S3, sans avoir à gérer un serveur.                       |
| **Amazon VPC (Virtual Private Cloud)** | Service qui vous permet de lancer des ressources AWS dans un réseau virtuel que vous avez défini, offrant un contrôle total sur votre environnement réseau.                    | Exemple : Vous pouvez configurer un VPC pour isoler une application web dans un sous-réseau privé tout en contrôlant le trafic entrant et sortant à l'aide de groupes de sécurité. |

# Annexe 3 -  Utilisateurs IAM, groupes, Rôles et politiques définis 


### Exemples Pratiques des Concepts Clés d'AWS IAM

#### 1. **Utilisateurs IAM**

   **Exemple Pratique :**
   - **Scénario** : Une entreprise de développement logiciel a plusieurs développeurs qui travaillent sur différents projets AWS. Chaque développeur doit avoir accès à certaines ressources AWS pour effectuer son travail, mais les accès doivent être limités en fonction de leur rôle spécifique.
   - **Action** : Créez un utilisateur IAM pour chaque développeur, par exemple `dev-julie` et `dev-paul`. Chaque utilisateur aura ses propres informations d'identification (mot de passe pour accéder à la Console de gestion AWS et/ou clés d'accès pour accéder aux services via API ou CLI).
   - **Résultat** : `dev-julie` et `dev-paul` peuvent se connecter à AWS avec leurs identifiants respectifs. Leur accès est tracé individuellement, ce qui permet un audit détaillé des actions effectuées par chaque développeur.

#### 2. **Groupes IAM**

   **Exemple Pratique :**
   - **Scénario** : L'entreprise souhaite faciliter la gestion des permissions pour ses développeurs. Les développeurs front-end n'ont besoin que d'un accès en lecture à certains services, tandis que les développeurs back-end nécessitent des permissions supplémentaires pour gérer les instances EC2.
   - **Action** : Créez deux groupes IAM, `FrontEnd-Devs` et `BackEnd-Devs`. Attribuez des permissions spécifiques à chaque groupe :
     - `FrontEnd-Devs` : Accès en lecture seule à Amazon S3 pour consulter les ressources statiques comme les fichiers CSS et JS.
     - `BackEnd-Devs` : Permissions pour lancer, arrêter et gérer des instances EC2.
   - **Résultat** : Tous les développeurs front-end ajoutés au groupe `FrontEnd-Devs` hériteront automatiquement des permissions de lecture seule sur S3. De même, les développeurs back-end ajoutés à `BackEnd-Devs` hériteront des permissions nécessaires pour gérer EC2, simplifiant ainsi la gestion des accès.

#### 3. **Rôles IAM**

   **Exemple Pratique :**
   - **Scénario** : L'entreprise déploie une application web sur Amazon EC2 qui doit accéder aux objets stockés dans Amazon S3 pour servir des images aux utilisateurs. Pour des raisons de sécurité, il est déconseillé de stocker des clés d'accès AWS directement sur l'instance EC2.
   - **Action** : Créez un rôle IAM, `S3AccessRole`, qui accorde l'accès en lecture à un compartiment S3 spécifique. Attachez ce rôle à l'instance EC2 lors de son lancement.
   - **Résultat** : L'application web sur l'instance EC2 peut accéder aux objets dans S3 sans qu'aucune clé d'accès ne soit stockée ou gérée manuellement. Le rôle IAM gère automatiquement l'accès sécurisé.

#### 4. **Politiques IAM**

   **Exemple Pratique :**
   - **Scénario** : L'entreprise a un utilisateur IAM, `data-analyst`, qui doit pouvoir extraire des données de DynamoDB, mais uniquement de certaines tables, et il ne doit pas pouvoir modifier ces données.
   - **Action** : Créez une politique IAM JSON personnalisée comme celle-ci :
     ```json
     {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Effect": "Allow",
           "Action": [
             "dynamodb:GetItem",
             "dynamodb:Scan",
             "dynamodb:Query"
           ],
           "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/MyTable"
         }
       ]
     }
     ```
   - **Résultat** : La politique IAM accorde à `data-analyst` la permission de lire (GetItem, Scan, Query) uniquement sur la table DynamoDB spécifiée, mais lui interdit toute action de modification (comme PutItem ou DeleteItem). Cette politique peut être attachée directement à l'utilisateur `data-analyst` ou à un groupe auquel il appartient.


**Conclusion :**
- À travers cet atelier, vous aurez maîtrisé l'exploration, la gestion et le test des utilisateurs, groupes, et politiques IAM. 
- Cela vous permettra de gérer efficacement les identités et les accès dans AWS, tout en assurant une sécurité robuste pour vos ressources cloud. Vous aurez également acquis les compétences nécessaires pour appliquer ces concepts dans des scénarios réels, adaptés aux besoins de votre organisation.


