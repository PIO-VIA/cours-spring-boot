# Chapitre 3 - Leçon 1 : Création d'un contrôleur REST (`@RestController`)

Nous avons déjà effleuré ce sujet dans notre "Hello, World", mais il est temps de plonger en profondeur dans la création de contrôleurs pour construire des API RESTful. La pierre angulaire de cette fonctionnalité dans Spring Boot est l'annotation `@RestController`.

## Qu'est-ce qu'une API RESTful ?

Avant de coder, un bref rappel. Une API RESTful (Representational State Transfer) est un style d'architecture pour la communication entre systèmes informatiques, généralement via le protocole HTTP. Elle repose sur plusieurs principes :

-   **Communication Client-Serveur :** Le client et le serveur sont séparés.
-   **Sans état (Stateless) :** Chaque requête du client vers le serveur doit contenir toutes les informations nécessaires pour que le serveur la comprenne. Le serveur ne stocke aucun état de session du client.
-   **Ressources :** Les données sont exposées sous forme de "ressources" (par exemple, un utilisateur, un produit, une commande). Chaque ressource est identifiée par une URI unique (par exemple, `/users/123`).
-   **Verbes HTTP :** On utilise les méthodes HTTP standard pour manipuler ces ressources :
    -   `GET` : Pour récupérer une ressource.
    -   `POST` : Pour créer une nouvelle ressource.
    -   `PUT` : Pour mettre à jour une ressource existante (remplacement complet).
    -   `PATCH` : Pour mettre à jour partiellement une ressource.
    -   `DELETE` : Pour supprimer une ressource.

## Le rôle de Spring MVC

Spring MVC est le module du framework Spring qui gère les requêtes HTTP et implémente le pattern de conception *Modèle-Vue-Contrôleur*. Dans le contexte des API REST, nous nous concentrons principalement sur le **Contrôleur**.

Lorsque vous ajoutez le starter `spring-boot-starter-web`, Spring Boot configure automatiquement Spring MVC pour vous.

## `@Controller` vs `@RestController`

Spring propose deux annotations principales pour les contrôleurs :

1.  **`@Controller` :** C'est l'annotation de base. Historiquement, elle était utilisée pour les applications web traditionnelles où le contrôleur retourne le nom d'une *vue* (par exemple, une page HTML rendue par Thymeleaf). Le corps de la réponse n'est pas la valeur de retour de la méthode directement.
2.  **`@RestController` :** C'est une annotation de commodité introduite pour simplifier la création d'API REST. Elle est elle-même annotée avec `@Controller` et `@ResponseBody`.

    -   `@Controller` : Indique que la classe est un contrôleur web.
    -   `@ResponseBody` : Appliquée à toutes les méthodes du contrôleur, cette annotation indique à Spring que la valeur retournée par la méthode doit être sérialisée (généralement en JSON) et écrite directement dans le corps de la réponse HTTP.

**Conclusion : Pour créer des API REST qui retournent du JSON, utilisez toujours `@RestController`.**

## Exemple Pratique : Un `BookController`

Créons un contrôleur simple pour gérer une ressource "Livre".

1.  **Créez un package `controller`** si ce n'est pas déjà fait.
2.  **Créez une classe `BookController`** dans ce package.

```java
package com.example.demo.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

// 1. Déclarer la classe comme un contrôleur REST
@RestController
// 2. Définir un préfixe de chemin pour toutes les méthodes de ce contrôleur
@RequestMapping("/api/v1/books")
public class BookController {

    // 3. Mapper une méthode à une requête HTTP GET
    @GetMapping
    public List<String> getAllBooks() {
        // 4. Retourner des données
        return List.of("The Lord of the Rings", "The Hobbit", "Silmarillion");
    }
}
```

### Analyse du code

1.  **`@RestController` :** Indique à Spring que cette classe gérera les requêtes HTTP et que les retours de méthode seront sérialisés en JSON.
2.  **`@RequestMapping("/api/v1/books")` :** Cette annotation au niveau de la classe définit un préfixe commun pour toutes les routes définies à l'intérieur. C'est une excellente pratique pour versionner (`/v1`) et organiser vos API. Toutes les routes de ce contrôleur commenceront donc par `/api/v1/books`.
3.  **`@GetMapping` :** Cette annotation mappe la méthode `getAllBooks()` aux requêtes HTTP `GET`. Comme elle n'a pas de chemin spécifié, elle hérite du chemin de la classe. L'URL complète pour appeler cette méthode est donc `GET http://localhost:8080/api/v1/books`.
4.  **Retourner des données :** La méthode retourne un `List<String>`. Grâce à la magie de Spring Boot et de la bibliothèque **Jackson** (incluse par défaut avec le starter web), cette liste Java sera automatiquement transformée en un tableau JSON dans la réponse HTTP.

## Test de notre Contrôleur

1.  Lancez votre application Spring Boot.
2.  Ouvrez un navigateur ou un client API et faites une requête `GET` à l'URL : `http://localhost:8080/api/v1/books`.

Vous devriez recevoir la réponse suivante, avec le `Content-Type` `application/json` :

```json
[
    "The Lord of the Rings",
    "The Hobbit",
    "Silmarillion"
]
```

Félicitations ! Vous avez créé un véritable endpoint REST. Dans la prochaine leçon, nous verrons comment gérer les différentes méthodes HTTP et comment rendre nos URLs plus dynamiques.
