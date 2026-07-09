# Rapport d'Ingénierie : Initialisation & Fondations Web
**Titre de la phase :** Phase 0 & 1 - Initialisation & Fondations Web
**Préparé par :** `Eya Soyed`

---

## 1. Comptes & Environnements (Phase 0)

| Plateforme | Utilité dans le Workshop | Nom d'utilisateur (Username) | URL du Profil |
| :--- | :--- | :--- | :--- |
| **GitHub** | Hébergement du code & Automatisation CI/CD | `eya568` | `https://github.com/eya568` |
| **DockerHub** | Registre public pour nos conteneurs | `eyasoyed` | `https://app.docker.com/accounts/eyasoyed` |
| **Microsoft Azure** | Infrastructure Cloud de Production | `swayedaya@gmail.com` | Eya Soyed |

![Mon profil GitHub](images/profilGitHub.png)

---

## 2. Rapport de Concepts Théoriques (The "Why")

### Module A : Architecture Web & Communication

**Q2.1 — Frontend, Backend et base de données**

Le Frontend est la partie visible et interactive de l'application, celle qui s'exécute dans le navigateur de l'utilisateur : il gère l'affichage, l'interface et les interactions (clics, formulaires, navigation). Le Backend, lui, tourne côté serveur et ne se voit pas directement : c'est là que se trouve la logique métier, c'est lui qui reçoit les requêtes envoyées par le frontend, applique les règles de l'application (calculs, vérifications, authentification) et décide quoi faire avec les données. La base de données complète ce trio en assurant le stockage persistant des informations : utilisateurs, produits, transactions, etc. Elle n'est jamais consultée directement par le frontend, mais uniquement via le backend, qui lit et écrit dedans selon les besoins de l'application.

**Q2.2 — Client-Serveur vs 3-Tiers**

Dans une architecture Client-Serveur classique (2-tiers), on sépare simplement le client, qui affiche l'interface, du serveur, qui gère à la fois la logique métier et les données. Ça fonctionne, mais le serveur cumule trop de responsabilités : il devient difficile à sécuriser et à faire évoluer. L'architecture 3-tiers va plus loin en séparant clairement trois couches indépendantes : le frontend pour l'interface, le backend pour la logique métier, et la base de données pour le stockage. C'est devenu le standard du web moderne pour plusieurs raisons : la base de données n'est jamais exposée directement, ce qui réduit la surface d'attaque ; chaque couche a un rôle unique, ce qui rend le code plus facile à maintenir ; et on peut faire évoluer une couche sans toucher aux autres, par exemple changer de base de données sans rien modifier côté frontend.

**Q2.3 — API et analogie du restaurant**

Une API (Application Programming Interface) est un ensemble de règles qui permet à deux logiciels de communiquer entre eux sans que l'un ait besoin de connaître le fonctionnement interne de l'autre. Elle définit précisément quelles requêtes on peut envoyer et quelles réponses on peut recevoir en retour.

L'analogie du restaurant aide à comprendre son rôle : le client assis à table, qui a faim, représente le frontend ; la cuisine, où le plat est réellement préparé, représente le backend ; et le serveur du restaurant, qui fait le lien entre les deux, représente l'API. Le client ne va jamais en cuisine préparer lui-même son plat : il donne sa commande au serveur, qui la transmet à la cuisine dans un format compris de tous, puis ramène le plat une fois prêt. Le client n'a pas besoin de savoir comment le plat est cuisiné, il communique juste via un menu standard, qui correspond au contrat de l'API.

Concrètement en informatique, le frontend envoie une requête à l'API, par exemple pour demander la liste des produits ; l'API transmet cette demande au backend, qui va chercher l'information, souvent en interrogeant la base de données ; l'API renvoie enfin la réponse au frontend dans un format standard, généralement JSON. En résumé, l'API est le messager standardisé qui permet au frontend de demander des choses au backend sans jamais connaître les détails internes de son fonctionnement.

**Q2.4 — Sécuriser la communication Backend/Base de données**

Plusieurs mécanismes empêchent un accès non autorisé aux données. D'abord, le frontend n'accède jamais directement à la base de données : tout passe obligatoirement par le backend. Ensuite, les identifiants de connexion à la base (utilisateur, mot de passe, hôte) sont stockés dans un fichier `.env`, séparé du code source, et jamais publiés. Les connexions peuvent en plus être chiffrées via TLS/SSL pour éviter qu'elles soient interceptées sur le réseau. Enfin, c'est le backend qui contrôle les autorisations avant toute opération sur les données, en vérifiant par exemple qu'un utilisateur a bien le droit de lire ou modifier telle ressource avant d'exécuter la requête.

### Module B : Configuration, Frameworks & Écosystème

**Q3.1 — Variables d'environnement et hardcoding**

Les variables d'environnement, généralement stockées dans un fichier `.env`, sont des paramètres de configuration externes au code de l'application : mots de passe, clés d'API, URLs de base de données, etc. Elles permettent de séparer le code de son contexte d'exécution. Écrire ces informations directement dans le code source, ce qu'on appelle le hardcoding, est fortement déconseillé pour deux raisons principales : si le code est partagé ou publié, par exemple sur GitHub, ces données sensibles se retrouveraient exposées à tout le monde ; et surtout, ça empêche de changer facilement de configuration entre les différents environnements (développement, test, production) sans devoir modifier le code lui-même.

**Q3.2 — Protéger `.env` avec `.gitignore`**

Il suffit d'ajouter la ligne `*.env` dans le fichier `.gitignore` du projet. Git ignorera alors automatiquement ce fichier et ne le proposera jamais au suivi de version, ce qui évite qu'il soit accidentellement commité puis poussé sur GitHub.

**Q3.3 — Framework, dépendance et librairie open source**

Un framework est une base de développement qui fournit une architecture complète et un ensemble d'outils pour construire une application. Il impose généralement une certaine façon d'organiser le projet et de développer les fonctionnalités, contrairement à une simple librairie qu'on utilise ponctuellement. Une dépendance, ou package, est un composant externe qu'on ajoute au projet pour répondre à un besoin précis sans avoir à le redévelopper soi-même ; elle est gérée par un gestionnaire de paquets comme npm, pip ou Maven. Une librairie open source, enfin, est du code mis à disposition de la communauté, que chacun peut librement utiliser, étudier et parfois modifier ; elle peut être intégrée directement dans un projet ou distribuée sous forme de package.

### Module C : Anatomie d'un Projet & Risques (Dev vs Prod)

**Q4.1 — Arborescence d'un projet full-stack**
my-app/
├── frontend/         # Code exécuté dans le navigateur de l'utilisateur
│   ├── src/          # Composants, pages, styles, logique de l'interface
│   └── package.json  # Gestion des dépendances du Frontend
├── backend/          # Code exécuté sur le serveur (logique métier)
│   ├── src/
│   ├── .env          # Variables de configuration confidentielles
│   └── package.json  # Gestion des dépendances du Backend
└── .gitignore        # Fichiers exclus du suivi Git

Le dossier `frontend/` regroupe tout ce qui concerne l'interface utilisateur, avec son propre `package.json` pour gérer ses dépendances indépendamment du reste. Le dossier `backend/` contient la logique métier côté serveur, ainsi que le fichier `.env`, qui doit impérativement rester hors du dépôt Git puisqu'il contient des informations confidentielles. Enfin, le `.gitignore` à la racine liste tous les fichiers et dossiers à exclure du versioning, comme `.env` ou `node_modules/`.

**Q4.2 — Environnement de développement local vs production**

L'environnement de développement local, sur son propre PC, sert à écrire et tester le code dans des conditions contrôlées, souvent avec peu de données et un seul utilisateur. L'environnement de production, hébergé dans le cloud, est celui où l'application tourne réellement pour de vrais utilisateurs, avec un vrai volume de trafic et de données, et des exigences de disponibilité et de sécurité bien plus strictes.

**Q4.3 — Risques du passage local → production**

Deux risques majeurs reviennent souvent. Le premier concerne les différences d'environnement : une version de langage, une variable d'environnement ou une dépendance différente entre le poste local et le serveur de production peut faire planter une application qui fonctionnait pourtant parfaitement en local. Le second concerne le volume de données et de trafic : en local, on teste généralement avec une petite base de données et un seul utilisateur, alors qu'en production l'application doit encaisser un trafic réel et des volumes de données bien plus importants, ce qui peut révéler des problèmes de performance invisibles jusque-là. Le DevOps aide à prévenir ces risques en automatisant les tests et les déploiements via des pipelines CI/CD, et en standardisant les environnements grâce à des outils comme Docker, pour que ce qui tourne en local corresponde le plus fidèlement possible à ce qui tournera en production.

### Module D : Maîtrise de Git & Préparation à l'Automatisation

**Q5.1 — Git vs GitHub**

Git est un outil de gestion de versions installé localement sur l'ordinateur, qui permet de suivre l'historique des modifications d'un projet. GitHub est une plateforme en ligne qui héberge des dépôts Git et ajoute des fonctionnalités de collaboration, comme les Pull Requests ou les Issues. Git peut être utilisé sans GitHub, mais GitHub repose sur Git.

**Q5.2 — Contrôle de version**

Le contrôle de version est un système qui enregistre l'historique complet des modifications apportées au code d'un projet. Il est indispensable en équipe car plusieurs ingénieurs peuvent travailler simultanément sur les mêmes fichiers sans écraser le travail des uns et des autres. Il facilite la collaboration, la gestion des conflits de code, et assure une meilleure organisation du projet tout en réduisant le risque d'erreurs irréversibles, puisqu'on peut toujours revenir à une version antérieure.

**Q5.3 — Commit, Push, Pull**

Le commit est une action locale qui enregistre un ensemble de modifications dans l'historique Git du projet ; chaque commit représente une version stable ou cohérente du code, accompagnée d'un message descriptif. Le push envoie ensuite ces commits locaux vers un dépôt distant comme GitHub, ce qui permet de partager les modifications avec le reste de l'équipe. Le pull fait l'inverse : il récupère les dernières modifications présentes sur le dépôt distant pour les intégrer dans la copie locale, afin de rester synchronisé avec le travail des autres.

**Q5.4 — Branches Git**

Une branche Git est une ligne de développement indépendante, créée à partir d'un point donné de l'historique du projet. Elle permet à deux développeurs de coder en même temps sans se marcher dessus : chacun crée sa propre branche, développe sa fonctionnalité de son côté, puis fusionne ses changements dans la branche principale via un merge ou, plus couramment sur GitHub, via une Pull Request qui permet en plus une relecture avant fusion.

**Q5.5 — Dépôt public vs privé**

Un dépôt public est visible et accessible par n'importe qui sur GitHub, ce qui convient bien à des projets open source où l'on cherche du partage et de la visibilité. Un dépôt privé n'est accessible qu'aux personnes explicitement autorisées, et se choisit plutôt pour des projets nécessitant sécurité et confidentialité, comme un usage interne à une entreprise ou un projet encore non finalisé.

**Q5.6 — Pipeline CI/CD et GitHub Actions**

Un pipeline CI/CD automatise le cycle de vie du code entre son écriture et son déploiement. La partie CI, intégration continue, consiste à tester automatiquement le code à chaque push, que ce soit via des tests unitaires, une vérification de compilation ou du linting, afin de détecter les erreurs le plus tôt possible, avant qu'elles n'atteignent la branche principale. La partie CD, déploiement continu, prend le relais si les tests passent avec succès : le code est alors automatiquement déployé, que ce soit vers un serveur de staging ou directement en production. GitHub Actions est l'outil qui exécute concrètement ce pipeline directement dans GitHub, en définissant des workflows sous forme de fichiers YAML qui automatisent ces tâches de test, build et déploiement à chaque push ou Pull Request.

---

## 3. Journal d'Implémentation Technique (The "How")

Pour initialiser le premier dépôt Git du projet, j'ai utilisé la séquence de commandes suivante depuis le dossier local du projet :

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/eya568/sourceToCloud
git push -u origin main
```

La commande `git init` crée le dépôt Git local, `git add .` prépare tous les fichiers du dossier pour le prochain commit, `git commit` enregistre cet état dans l'historique avec un message descriptif, `git remote add origin` relie le dépôt local au dépôt distant sur GitHub, et enfin `git push -u origin main` envoie ce premier commit vers GitHub tout en définissant la branche `main` distante comme branche de suivi par défaut.

Pour tester l'isolation de mon fichier `.env`, je l'ai d'abord ajouté dans mon fichier `.gitignore`, puis j'ai lancé `git status` pour vérifier qu'il n'apparaissait pas dans la liste des fichiers suivis ou proposés au commit. J'ai ensuite confirmé le résultat directement sur GitHub après un push : le fichier `.env` n'était effectivement pas visible dans le dépôt distant, ce qui valide que la protection fonctionne correctement.

---

## 4. Tableau Post-Mortem (Gestion des Erreurs)

| Erreur / Message de Log | Cause de l'erreur | Comment l'avez-vous résolue ? |
| :--- | :--- | :--- |
| *Ex: fatal: pathspec '.env' did not match...* | *Le fichier n'était pas dans le bon dossier* | *Déplacement du fichier et réexécution de git add* |

---

## 5. Conclusion & Prochaine Étape

Cette phase m'a permis de mieux comprendre comment le DevOps rapproche développement et exploitation, et pourquoi la séparation en couches, avec l'architecture 3-tiers, ainsi que la sécurisation des données dès le début d'un projet, via les variables d'environnement et le `.gitignore`, sont des réflexes essentiels plutôt que des détails techniques secondaires. J'ai aussi consolidé ma compréhension du cycle Git commit/push/pull et de son rôle dans le travail en équipe.

Je me sens prêt à passer à l'étape suivante, "Infrastructure & Linux", puisque les bases de la collaboration via Git et de la séparation frontend/backend/base de données sont désormais claires et appliquées concrètement sur ce projet.
