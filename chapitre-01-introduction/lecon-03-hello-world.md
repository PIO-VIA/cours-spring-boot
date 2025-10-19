# Chapitre 1 - Leçon 3 : Votre première application "Hello, World"

Maintenant que notre environnement est prêt et que le projet est généré, il est temps de lancer l'application et de créer notre premier point de terminaison (endpoint) web.

## Exploration de la Structure du Projet

Après avoir importé le projet généré par Spring Initializr, vous devriez voir une structure de fichiers similaire à celle-ci (pour un projet Maven) :

```
demo/
├── .mvn/
│   └── wrapper/
│       └── ...
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/
│   │   │       └── example/
│   │   │           └── demo/
│   │   │               └── DemoApplication.java  <-- Point d'entrée
│   │   └── resources/
│   │       ├── static/
│   │       └── templates/
│   │       └── application.properties          <-- Fichier de configuration
│   └── test/
│       └── java/
│           └── com/
│               └── example/
│                   └── demo/
│                       └── DemoApplicationTests.java
├── .gitignore
├── mvnw
├── mvnw.cmd
└── pom.xml                                     <-- Fichier de build Maven
```

### Fichiers et Dossiers Clés

- **`DemoApplication.java`** : C'est le cœur de votre application. Ce fichier contient la méthode `main`, qui est le point de départ pour lancer l'application. Il est annoté avec `@SpringBootApplication`.
- **`pom.xml`** : Le fichier de configuration de Maven. Il définit les métadonnées du projet, les dépendances (comme `spring-boot-starter-web`) et les plugins.
- **`src/main/resources`** : Ce répertoire contient les fichiers de configuration et les ressources statiques.
    - **`application.properties`** : Le fichier principal pour configurer votre application (port du serveur, connexion à la base de données, etc.). Il est vide par défaut.
    - **`static`** : Pour les ressources web statiques (CSS, JavaScript, images).
    - **`templates`** : Pour les templates de vues (par exemple, avec Thymeleaf ou FreeMarker).
- **`src/test`** : Le répertoire racine pour vos tests.

## La Classe Principale : `DemoApplication.java`

Ouvrez ce fichier. Son contenu est remarquablement simple :

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}
```

- **`@SpringBootApplication`** : C'est une annotation de commodité qui regroupe trois autres annotations :
    1.  **`@Configuration`** : Marque la classe comme une source de définitions de beans pour le contexte de l'application.
    2.  **`@EnableAutoConfiguration`** : Demande à Spring Boot de commencer à ajouter des beans en se basant sur les paramètres du classpath, d'autres beans et divers paramètres de propriété (c'est la "magie" de l'auto-configuration).
    3.  **`@ComponentScan`** : Demande à Spring de scanner le package `com.example.demo` (et ses sous-packages) à la recherche d'autres composants (comme les contrôleurs, services, etc.).
- **`main(String[] args)`** : La méthode standard pour démarrer une application Java.
- **`SpringApplication.run(...)`** : Cette ligne délègue le démarrage de l'application à Spring Boot, qui va créer le contexte de l'application et lancer le serveur web embarqué.

## Lancement de l'Application

Vous pouvez lancer l'application de plusieurs manières :

1.  **Depuis l'IDE (Recommandé) :**
    - Faites un clic droit sur le fichier `DemoApplication.java`.
    - Sélectionnez "Run 'DemoApplication.main()'”.
2.  **Avec Maven en ligne de commande :**
    - Ouvrez un terminal à la racine du projet.
    - Exécutez la commande : `./mvnw spring-boot:run`

Vous devriez voir le journal de démarrage de Spring Boot dans la console, se terminant par des lignes indiquant que Tomcat a démarré sur un port (par défaut, le port `8080`).

## Création de notre premier Contrôleur REST

Pour que notre application fasse quelque chose, nous devons créer un contrôleur. Un contrôleur est une classe qui gère les requêtes web entrantes.

1.  **Créez un nouveau package** `controller` à côté de `DemoApplication.java` (dans `com.example.demo.controller`). C'est une bonne pratique pour organiser votre code.
2.  **Créez une nouvelle classe Java** nommée `GreetingController` dans ce package.
3.  **Ajoutez le code suivant :**

```java
package com.example.demo.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class GreetingController {

    @GetMapping("/hello")
    public String sayHello() {
        return "Hello, World!";
    }
}
```

### Explication du Code

- **`@RestController`** : C'est un stéréotype qui indique à Spring que cette classe est un contrôleur et que les valeurs retournées par ses méthodes doivent être écrites directement dans le corps de la réponse (par défaut, en format JSON ou texte brut). C'est une combinaison de `@Controller` et `@ResponseBody`.
- **`@GetMapping("/hello")`** : Cette annotation mappe les requêtes HTTP `GET` pour l'URL `/hello` à la méthode `sayHello()`.
- **`sayHello()`** : Lorsque vous accédez à `/hello`, cette méthode est exécutée. La chaîne de caractères `"Hello, World!"` qu'elle retourne sera la réponse HTTP.

## Test de l'Application

Si vous avez activé `spring-boot-devtools`, l'application devrait avoir redémarré automatiquement après vos modifications. Sinon, relancez-la.

Ouvrez votre navigateur web ou un client API (comme Postman ou `curl`) et accédez à l'URL :

[http://localhost:8080/hello](http://localhost:8080/hello)

Vous devriez voir le message **"Hello, World!"** s'afficher.

Félicitations ! Vous avez créé et exécuté votre première application web avec Spring Boot. Dans la prochaine leçon, nous examinerons de plus près le fichier de build.
