# Documentation – WAMP Docker pour les élèves

## 🎯 Objectif
Ce guide permet d’installer un environnement équivalent à **WampServer** à l’aide de **Docker**.  
Vous obtiendrez :
- un serveur **Apache + PHP**,
- une base de données **MariaDB**,
- une interface de gestion **phpMyAdmin**.

---

## 🧱 1. Prérequis

### 1.1 Installer Docker Desktop
Téléchargez et installez **Docker Desktop** depuis :  
👉 [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/)

Pendant l’installation :
- Acceptez **l’intégration WSL2** si elle est proposée (Windows).
- Redémarrez l’ordinateur après l’installation.

Pour vérifier que tout fonctionne :
```bash
docker --version
docker compose version
```

---

## 📁 2. Structure du projet

Créez un dossier nommé `wampserver-docker` contenant les éléments suivants :

```
wampserver-docker/
├── docker-compose.yml
├── apache-php/
│   └── Dockerfile
└── src/
    └── index.php
```

---

## 🧩 3. Contenu des fichiers

### 3.1 `apache-php/Dockerfile`
```dockerfile
FROM php:8.2-apache

# Installer les extensions PHP nécessaires
RUN docker-php-ext-install mysqli pdo pdo_mysql

# Activer mod_rewrite (utile pour frameworks PHP)
RUN a2enmod rewrite

# Définir le répertoire de travail
WORKDIR /var/www/html
```

---

### 3.2 `docker-compose.yml`
```yaml
services:
  web:
    build: ./apache-php
    container_name: wamp_web
    ports:
      - "8081:80"   # Accès au site sur http://localhost:8081
    volumes:
      - ./src:/var/www/html
    depends_on:
      - db

  db:
    image: mariadb:11
    container_name: wamp_db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: testdb
      MYSQL_USER: user
      MYSQL_PASSWORD: userpass
    volumes:
      - db_data:/var/lib/mysql

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: wamp_phpmyadmin
    restart: always
    environment:
      PMA_HOST: db
      PMA_USER: root
      PMA_PASSWORD: root
    ports:
      - "8080:80"  # Accès à phpMyAdmin sur http://localhost:8080
    depends_on:
      - db

volumes:
  db_data:
```

---

### 3.3 `src/index.php`
```php
<?php
phpinfo();
```

---

## ⚙️ 4. Commandes de base

### Démarrer les services
```bash
docker compose up -d
```

### Arrêter les services
```bash
docker compose down
```

### Recréer après une modification du Dockerfile
```bash
docker compose build
docker compose up -d
```

### Voir les conteneurs en cours
```bash
docker ps
```

---

## 🌐 5. Accès

| Service       | URL                          | Identifiants par défaut         |
|----------------|------------------------------|---------------------------------|
| Site Web (PHP) | [http://localhost:8081](http://localhost:80) | — |
| phpMyAdmin     | [http://localhost:8080](http://localhost:8080) | utilisateur : `root` / mot de passe : `root` |

---

## 🧠 6. Dépannage

| Problème | Solution |
|-----------|-----------|
| Page charge à l’infini | Vérifier que le port `8081` n’est pas déjà utilisé, ou changer `8081:80` dans le fichier `docker-compose.yml`. |
| Le conteneur web ne démarre pas | Exécuter `docker logs wamp_web` pour voir les erreurs. |
| phpMyAdmin ne se connecte pas | Vérifier que le conteneur `wamp_db` est bien lancé (`docker ps`). |
| Modifier le mot de passe root | Modifier la variable `MYSQL_ROOT_PASSWORD` dans `docker-compose.yml`, puis exécuter `docker compose down -v && docker compose up -d`. |

---

## 💡 7. (Optionnel) Lancement simplifié sous Windows

Créer un fichier `start.bat` :
```bat
@echo off
echo Démarrage du WAMP Docker...
docker compose up -d
pause
```

Créer un fichier `stop.bat` :
```bat
@echo off
echo Arrêt du WAMP Docker...
docker compose down
pause
```

Les élèves peuvent simplement **double-cliquer sur `start.bat`** pour tout lancer.

---

## ✅ 8. Résumé

| Élément | Description |
|----------|--------------|
| Apache + PHP | Serveur web pour exécuter le code PHP |
| MariaDB | Base de données MySQL |
| phpMyAdmin | Interface graphique pour gérer les bases |
| `src/` | Dossier contenant le code du projet |
| `8081` | Port pour accéder au site |
| `8080` | Port pour phpMyAdmin |
