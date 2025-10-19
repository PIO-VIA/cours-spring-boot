# Chapitre 1 - Leçon 4 : Comprendre le build avec Maven ou Gradle

Le fichier de build est le pilier de votre projet. Il définit non seulement les dépendances, mais aussi comment compiler, tester et empaqueter votre application. Nous allons décortiquer le fichier `pom.xml` généré par Spring Initializr.

## Anatomie du `pom.xml`

Voici les sections les plus importantes d'un `pom.xml` pour un projet Spring Boot.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" ...>
    <modelVersion>4.0.0</modelVersion>

    <!-- 1. Le Parent Starter -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.x.x</version> <!-- Version actuelle de Spring Boot -->
        <relativePath/>
    </parent>

    <!-- 2. Métadonnées du Projet -->
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>
    <description>Demo project for Spring Boot</description>

    <!-- 3. Propriétés -->
    <properties>
        <java.version>17</java.version>
    </properties>

    <!-- 4. Dépendances -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <!-- 5. Le Plugin Spring Boot -->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

### 1. Le Parent Starter (`spring-boot-starter-parent`)

C'est la pièce maîtresse. En héritant de ce `parent`, notre projet obtient immédiatement :
- **Une gestion centralisée des versions :** Le `parent` définit les versions de dizaines de dépendances communes (Spring, Jackson, Logback, etc.). Cela garantit leur compatibilité et nous évite d'avoir à spécifier une version pour chaque dépendance.
- **Une configuration par défaut sensée :** Il configure des plugins Maven, comme ceux pour la compilation du code source Java, en utilisant les bonnes pratiques.
- **La version de Java :** Il définit une version de Java par défaut (que nous pouvons surcharger dans la section `<properties>`).

### 2. Métadonnées du Projet

Cette section (`groupId`, `artifactId`, `version`) définit les coordonnées uniques de notre projet dans l'écosystème Maven. C'est ce qui permet d'identifier notre application.

### 3. Propriétés (`<properties>`)

Ici, nous pouvons définir des variables réutilisables dans le `pom.xml`. La plus commune est `<java.version>`, qui permet de spécifier la version de Java à utiliser pour la compilation, surchargeant ainsi celle définie dans le `parent`.

### 4. Dépendances (`<dependencies>`)

C'est ici que nous déclarons les "starters" et autres bibliothèques dont notre projet a besoin.
- **`spring-boot-starter-web`** : Comme nous l'avons vu, il importe tout le nécessaire pour une application web. Notez que nous n'avons pas besoin de spécifier de version, car elle est gérée par le `parent`.
- **`spring-boot-starter-test`** : Inclut des bibliothèques de test comme JUnit 5, Mockito et AssertJ.
- **La portée (`<scope>`) :**
    - `test` : La dépendance n'est disponible que pour le code de test, elle ne sera pas incluse dans le JAR final.
    - `runtime` : La dépendance n'est pas nécessaire à la compilation, mais elle l'est à l'exécution. C'est le cas de `devtools`.
    - `provided` : La dépendance est nécessaire à la compilation, mais elle sera fournie par l'environnement d'exécution (par exemple, un serveur d'applications). Moins courant avec Spring Boot.
    - `compile` (par défaut) : La dépendance est disponible partout.

### 5. Le Plugin Spring Boot (`spring-boot-maven-plugin`)

Ce plugin est essentiel. Il accomplit deux tâches principales :
1.  **Empaquetage :** Il crée un "fat JAR" (ou JAR exécutable). Ce JAR contient non seulement notre code compilé, mais aussi toutes ses dépendances et le serveur d'application embarqué (Tomcat). C'est ce qui rend notre application autonome.
2.  **Exécution :** Il fournit l'objectif (goal) `spring-boot:run` que nous avons utilisé pour lancer l'application depuis la ligne de commande.

## Le Maven Wrapper (`mvnw`)

Vous avez peut-être remarqué les fichiers `mvnw` et `mvnw.cmd` à la racine du projet. C'est le **Maven Wrapper**.

**Objectif :** Il permet à n'importe qui de builder le projet avec la version exacte de Maven avec laquelle il a été configuré, sans avoir à installer Maven manuellement sur sa machine.

Lorsque vous exécutez `./mvnw ...` pour la première fois, le wrapper télécharge automatiquement la version de Maven spécifiée dans le projet (dans `.mvn/wrapper/maven-wrapper.properties`) et l'utilise.

**Bonne pratique :** Utilisez toujours `./mvnw` (ou `mvnw.cmd` sur Windows) plutôt que la commande `mvn` de votre système. Cela garantit des builds reproductibles pour tous les développeurs de l'équipe.

---

Ceci conclut notre premier chapitre. Vous avez maintenant une compréhension solide des bases de Spring Boot : sa philosophie, comment mettre en place un environnement, créer une application simple et comprendre son processus de build. Dans le prochain chapitre, nous plongerons dans le cœur de Spring : l'inversion de contrôle et l'injection de dépendances.
