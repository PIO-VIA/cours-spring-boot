# Chapitre 1 - Leçon 2 : Mise en place de l'environnement de développement

Pour développer efficacement avec Spring Boot, il est essentiel d'avoir un environnement de développement bien configuré. Voici les outils indispensables et les étapes pour être rapidement opérationnel.

## Prérequis Indispensables

### 1. Java Development Kit (JDK)

Spring Boot 3 (la version actuelle majeure) requiert **Java 17** au minimum. Il est compatible avec les versions plus récentes (Java 21, etc.). Je vous recommande d'utiliser une distribution OpenJDK.

**Action :**
- **Vérifiez votre version de Java :** Ouvrez un terminal et tapez `java -version`.
- **Installez le JDK si nécessaire :**
    - **SDKMAN! (Recommandé pour Linux/macOS) :** C'est un gestionnaire de versions pour de nombreux SDKs, y compris Java.
      ```bash
      # Pour installer le JDK 17 d'Adoptium (Temurin)
      sdk install java 17.0.11-tem
      ```
    - **Homebrew (macOS) :**
      ```bash
      brew install openjdk@17
      ```
    - **Téléchargement manuel :** Rendez-vous sur des sites comme [Adoptium (Eclipse Temurin)](https://adoptium.net/) ou [Azul Zulu](https://www.azul.com/downloads/?package=jdk) pour télécharger l'installeur correspondant à votre système d'exploitation.

### 2. Outil de Build : Maven ou Gradle

Spring Boot est compatible avec les deux principaux outils de build de l'écosystème Java. Le choix dépend souvent des préférences de l'équipe.

- **Maven :** Plus ancien, basé sur XML, très répandu en entreprise.
- **Gradle :** Plus moderne, utilise un DSL (Domain Specific Language) basé sur Groovy ou Kotlin pour ses scripts de build, souvent plus rapide et flexible.

**Action :**
- La plupart des IDEs modernes intègrent Maven et Gradle. Vous n'avez donc généralement pas besoin de les installer manuellement.
- Si vous souhaitez les utiliser en ligne de commande, vous pouvez les installer via SDKMAN!, Homebrew, ou en les téléchargeant depuis leurs sites officiels.

### 3. Un IDE (Environnement de Développement Intégré)

Un bon IDE est crucial pour la productivité. Voici les plus populaires pour le développement Spring Boot :

- **IntelliJ IDEA (Community ou Ultimate) :** C'est la référence. La version *Community* (gratuite) est largement suffisante pour commencer. La version *Ultimate* (payante) offre une intégration encore plus poussée avec le framework Spring.
- **Visual Studio Code (VS Code) :** Avec l'extension "Extension Pack for Java" de Microsoft, VS Code est devenu une alternative très crédible, légère et performante.
- **Eclipse IDE for Java EE Developers :** Un classique qui reste une option viable, bien que souvent considéré comme moins moderne qu'IntelliJ.

**Action :** Téléchargez et installez l'IDE de votre choix.

## Création de votre premier projet avec Spring Initializr

Le moyen le plus simple et le plus fiable pour démarrer un nouveau projet Spring Boot est d'utiliser le **Spring Initializr**. C'est un générateur de projet web qui vous permet de choisir tous les paramètres de base.

Vous pouvez y accéder de deux manières :

1.  **Via le site web : [start.spring.io](https://start.spring.io/)**
2.  **Directement depuis votre IDE** (IntelliJ, VS Code et Eclipse l'intègrent).

### Configuration du projet sur start.spring.io

Rendez-vous sur [start.spring.io](https://start.spring.io/) et configurez votre projet comme suit :

- **Project :** `Maven` (ou Gradle si vous préférez).
- **Language :** `Java`.
- **Spring Boot :** Laissez la version par défaut (non-SNAPSHOT).
- **Project Metadata :**
    - **Group :** `com.example` (souvent le nom de domaine inversé de votre entreprise).
    - **Artifact :** `demo` (le nom de votre projet).
    - **Name :** `demo`.
    - **Description :** `Demo project for Spring Boot`.
    - **Package name :** `com.example.demo`.
- **Packaging :** `Jar` (le plus courant).
- **Java :** `17` (ou la version que vous avez installée).

### Ajout des Dépendances Essentielles

Sur la partie droite, cliquez sur **"ADD DEPENDENCIES..."**. Pour une première application web simple, ajoutez :

1.  **Spring Web :** C'est le starter `spring-boot-starter-web`. Il inclut tout le nécessaire pour créer des applications web et des API REST, y compris un serveur Tomcat embarqué.
2.  **Spring Boot DevTools :** Cette dépendance est extrêmement utile en développement. Elle active des fonctionnalités comme le redémarrage automatique de l'application lorsque vous modifiez un fichier.

### Génération et Importation

1.  Cliquez sur le bouton **"GENERATE"**. Cela va télécharger une archive `.zip` contenant la structure de votre projet.
2.  Décompressez cette archive sur votre machine.
3.  Ouvrez votre IDE et importez le projet (par exemple, dans IntelliJ : `File > Open...` et sélectionnez le fichier `pom.xml` ou le répertoire du projet).

Votre environnement est maintenant prêt. Dans la prochaine leçon, nous allons explorer la structure de ce projet et écrire notre première ligne de code.
