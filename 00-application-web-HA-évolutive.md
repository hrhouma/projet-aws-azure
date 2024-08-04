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




