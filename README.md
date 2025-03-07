# Securelim - API

![](./docs/images/securelim_banner.jpg)



# Sommaire :

- I - Introduction
    
    1 - Contexte 
    
    2 - A quoi sert une API
    
- II - Installation
    
    1 - Prérequis
    
    2 - Mise en place de l’environnement
    
- Configuration & Démarrage
    
    1 - Configuration
    
    2 - Démarrage
    
- Explication, structure du code, et Fonctionnalité
- Recette & Amélioration

# Introduction :

### 1 - Contexte

Dans le cadre de la sécurisation du site de Limoges, une amélioration était nécessaire. Pour avoir plus de contexte sur l’existant, et des problèmes que ce dernier pose et posera, rendez-vous sur le rapport d’étude de l’équipe. 

Cependant, pour répondre au principal problème, qui est l’externalisation du traitement des données et de la sécurisation, et pour s’adapter à la nouvelle infrastructure sécurisée. Nous avons fait le choix logique de partir sur une API. 

Elle servira de point centrale aux échanges de données entre les Raspberry, l’application web et la base de données. 

### 2 - A quoi sert une API ?

Une API ( pour Application Programming Interface) est un ensemble de règles et fonctions qui permettent à différentes applications de communiquer entre elles. Elle définit les méthodes et les formats de données acceptés. 

Dans notre cas le format de données est en JSON. Il permet une exploitation beaucoup plus simple au niveau de l’application web par exemple.

En résumé, une API sert à faciliter la communication et l'échange de données entre différentes applications, systèmes ou services, ce qui permet d'ajouter des fonctionnalités, d'intégrer des services tiers et d'automatiser des tâches.

# Installation  :

### 1 - Prérequis

Pour l’API, vous aurez besoin de :

- Python en version 3.11.6
- Base de donnée MariaDB hébergée en local ou distante (voir la documentation pour la mettre en place)

### 2 - Mise en place de l’environnement

Pour installer cette API, vous aurez besoin de Python en **version 3.11.6.**

Après avoir cette installation, vous devez trouver votre interpréteur python. Si tout se passe bien et que vous avez ajouter python à votre `path` (variable d’environnement), alors en écrivant `python —version` ou `python 3.11 —version` il devrait apparaitre dans votre terminal.

Cependant, si il n’est pas dans votre `path`, voici comment faire : 

- Trouver votre interpreteur :

`where python` (Windows) 

`which python` (Unix)

- `where python` (Windows)
- `which python` (Unix)
- Généralement il est situé dans :
    
    `C:\Users\User\AppData\Local\Programs\Python\Python311\python.exe`


Maintenant que nous avons notre interpreteur, nous allons créer un environnement virtuel. Ce dernier permettra d’isoler notre application de notre machine (par exemple si jamais des packages sont mis à jour).

```bash
python -m venv venv
# ou
python3 -m venv venv
# ou 
`C:\Users\User\AppData\Local\Programs\Python\Python311\python.exe` -m venv venv
```

Nous allons maintenant activé notre environnement virtuel, pour utiliser son interpreteur plutot que celui installé précédemment. Il faudra l’activer à chaque fois que vous voulez démarrer l’API, ou installer des nouvelles dépendances. 

- Activé son environnement virtuel (sur Windows) :

```bash
venv\Scripts\activate
```

- Activé son environnement virtuel (sur Unix : Linux,Macos…)

```bash
source venv/bin/activate
```

( Pour stopper l’environnement virtuel, tapez `deactivate`

Puis, nous allons installer les packages de notre application. L’ensemble de ces derniers sont situés dans le fichier requirements.txt, avec la version spécifique qui correspond à notre environnement.

- Installer avec le Nexus La Poste (recommandé) :

```bash
python -m pip install --index https://repositories.net-courrier.extra.laposte.fr/repository/pypi-public/simple -r requirements.txt
```

- Installer avec le proxy La Poste :

```bash
pip install -r requirements.txt --proxy
http://user:password@surf-sccc.pasi.log.intra.laposte.fr:8080
```

Pour info, Nexus est un gestionnaire de référentiel, il permet de stocker, gérer et déployer divers types de paquets (npm, pip etc..) ou images (Docker…) ou autres.

# Configuration & Démarrage

### 1 - Configuration

Avant de démarrer notre API, nous avons besoin de configurer quelques variables. 

Rendez-vous dans app/config et dupliquez le fichier config_template.yml en config.yml. Puis vous devez remplir les variables de configuration du fichier. 

```yaml
CONFIG:
  API_PORT: 3000 # Port de machine hote de votre BDD
  BDD_HOST: localhost # Machine hote de votre BDD
  BDD_USER: "" # Utilisateur de la BDD que vous voulez utiliser
  BDD_PASS: "" # Le mot de passe de l'utilisateur
  BDD_NAME: "" # Le nom de la base de donnée utilisée
  LOG_FOLDER_PATH: app/logs # Chemin du dossier où sont stockés les logs d'entrée de l'API

DEVICES:
  IP_LOCATION_NAME: 8.8.8.8
  IP_LOCATION_NAME: XXX.XXX.XXX.XXX
  IP_LOCATION_NAME: 8.8.8.8
  # Vous devez remplacer les noms et IP en fonction de vos machines : 
  # Si jamais la machine est une raspberry dans un escalier alors 
  #IP_RASP_STAIRS : 192.168.19.3 (IP fictive) 
```

A noter que ce fichier config.yml sera ignorer si jamais il y a un push sur git, la configuration restera donc uniquement sur votre machine et sera à refaire si jamais le dossier est supprimé ou qu’il faut procéder à une réinstallation.

### 2 - Démarrage

Maintenant que notre environnement est mis en place et que notre configuration est faite, nous allons démarrer notre API avec cette commande : 

```bash
uvicorn app.main:app --reload --port 3000 
```

Pour cette commande shell, vous avez plusieurs arguments : 

- `—-reload` : Permet de relancer automatiquement l’application si jamais elle crash en cas d’erreur
- `—-port` : le port sur lequel notre API écoutera les requêtes entrantes
- `—-host` : vous avez la possibilité de spécifier l’host 0.0.0.0 celui permettra de lancer l’API, et qu’elle soit écoutable sur tout son réseau

# Explication, structure du code, et Fonctionnalité

La construction de cette API se base sur la structure **MVC** pour **Model Vue Controlleur**. 

Dans notre cas, les models sont les données de sortie de notre API, la Vue sont le résultat de notre API (au format JSON), et les controlleurs sont les classes qui vont requeter notre BDD.

```bash
securelim_api/
├── app/
│   ├── config/                    # Fichiers de configurations (BDD, Machines etcc..) 
│   ├── controllers/               # Dossier des controlleurs                      
│   │   ├── base.py                # Controlleur de base, s'occupuant des fonctions de connexion et déconnexion à la BDD (classe mère des autres controllers de la BDD)
│   │   ├── status.py              # Controlleur qui s'occupe des status
│   │   ├── employee.py            # Controller qui regroupe les requetes sur les employees (CRUD) en héritant du controller base
│   │   ├── ...                                
│   ├── logs/                      # Dossier regroupant les fichiers de logs d'accès à l'étage et d'action sur le site web 
│   │   ├── access.json            # Fichier de logs d'accès à l'étage
│   │   └── webapp.json            # Fichier de logs d'action sur le site web
│   ├── middleware/                # Dossier regroupant les middlewares comme le CORS (et prochainement le token manager) 
│   ├── models/                    # Dossier regroupant les models, pour mapper les sorties des fonctions de l'API
│   ├── utils/                     # Fonctions d'utilité générique, comme des calculs de date, formattage etc...
│   ├── main.py                    # Point d'entrée de l'API (endpoints, instanciation du client, des middlewares et du FastAPI) 
│   └── securelim_client.py        # Fichier du client de l'API, qui va initier les controllers, mapper les sorties           
├── .gitignore                     # Fichier pour ignorer certains fichiers git
├── Dockerfile                     # Fichier pour démarrer une image ou démarrer un container de l'API grâce à Docker.         
├── README.md                      # ...           
└── requirements.txt               # Fichier listant les dépendances                  
```

# Recette & Amélioration

 - Fonctionnel : 
    - [x] CRUD : Employés, Roles
    - [x] Les Status des machines
    - [x] La lecture et l'écriture de log
    - [x] Une route de vérification qui semble ok : check_access_floor (avec la logique)

- A faire : 
    - [ ] Verification access avec le NFC (en se basant sur l'existant) - Priorité 5/5
    - [ ] Mettre des vérifications avec condition à des endroits clés - Priorité 3/5
    - [ ] Commenter au max le code - Priorité 3/5
    - [ ] Cestion des cas erreurs au max - Priorité 4/5
    - [ ] Mettre en place le token sur toutes les routes sauf une (celle d'init de l'appli rasp) - Priorité 4/5
    - [ ] Faire le CRUD / Route pour l'appli web - Priorité 4/5
    - [ ] Test unitaires (avec un mock d'une BDD) - Priorité 1/5

--- 
 Roche Sébastien - Noa Duchiron - Axel Vignaud | Equipe Middleware | 2023-2024# securelim
# securelim
