# Leçon 05 : Dockeriser une Application Spring Boot

La conteneurisation est devenue une norme pour le déploiement d'applications modernes. Docker est l'outil le plus populaire pour cela. Il permet d'empaqueter une application avec toutes ses dépendances dans une image portable et de l'exécuter de manière isolée dans un conteneur.

## 1. Prérequis : Empaqueter l'Application

Avant de créer une image Docker, votre application Spring Boot doit être empaquetée en tant que fichier JAR exécutable. Vous pouvez le faire avec Maven ou Gradle.

**Avec Maven :**
```bash
./mvnw package
```

**Avec Gradle :**
```bash
./gradlew build
```

Cette commande génère un fichier JAR dans le répertoire `target` (pour Maven) ou `build/libs` (pour Gradle).

## 2. Créer un Dockerfile

Le `Dockerfile` est un fichier texte qui contient les instructions pour construire une image Docker. Créez un fichier nommé `Dockerfile` à la racine de votre projet.

Voici un `Dockerfile` simple et optimisé pour une application Spring Boot :

```dockerfile
# Étape 1: Utiliser une image de base avec Java
FROM openjdk:17-jdk-slim

# Étape 2: Définir des arguments pour le nom du JAR
ARG JAR_FILE=target/*.jar

# Étape 3: Copier le fichier JAR dans l'image
COPY ${JAR_FILE} app.jar

# Étape 4: Définir le point d'entrée pour exécuter l'application
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

### Explication des instructions :
- `FROM openjdk:17-jdk-slim`: Utilise une image de base officielle contenant Java 17. La version `slim` est plus légère.
- `ARG JAR_FILE=target/*.jar`: Définit une variable `JAR_FILE` qui pointe vers le JAR généré par Maven. Si vous utilisez Gradle, changez le chemin pour `build/libs/*.jar`.
- `COPY ${JAR_FILE} app.jar`: Copie le fichier JAR de votre système de fichiers local (là où la construction Docker est exécutée) vers le système de fichiers de l'image, en le renommant `app.jar`.
- `ENTRYPOINT ["java", "-jar", "/app.jar"]`: Spécifie la commande à exécuter lorsque le conteneur démarre. C'est l'équivalent de lancer `java -jar app.jar` dans votre terminal.

## 3. Construire l'Image Docker

Ouvrez un terminal à la racine de votre projet (là où se trouve le `Dockerfile`) et exécutez la commande suivante :

```bash
docker build -t mon-app-spring .
```

- `docker build`: La commande pour construire une image.
- `-t mon-app-spring`: Donne un nom (tag) à votre image, ici `mon-app-spring`.
- `.`: Indique que le contexte de build est le répertoire courant.

## 4. Lancer le Conteneur

Une fois l'image construite, vous pouvez lancer un conteneur à partir de celle-ci :

```bash
docker run -p 8080:8080 mon-app-spring
```

- `docker run`: La commande pour démarrer un conteneur.
- `-p 8080:8080`: Mappe le port 8080 du conteneur au port 8080 de votre machine hôte. Cela vous permet d'accéder à l'application via `http://localhost:8080`.
- `mon-app-spring`: Le nom de l'image à utiliser.

Votre application Spring Boot est maintenant en cours d'exécution à l'intérieur d'un conteneur Docker !
