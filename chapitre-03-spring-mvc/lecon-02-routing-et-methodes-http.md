# Chapitre 3 - Leçon 2 : Gestion des URLs (Routing) et des méthodes HTTP

Une API REST bien conçue utilise à la fois les URLs et les verbes HTTP pour exprimer clairement les actions sur les ressources. Spring MVC fournit un ensemble riche d'annotations pour mapper les méthodes de votre contrôleur à des requêtes HTTP spécifiques.

## Les Annotations de Mapping par Méthode

Nous avons déjà vu `@GetMapping`. Spring fournit une annotation pour chaque verbe HTTP majeur. Elles sont toutes des raccourcis pour `@RequestMapping(method = ...)`.

-   `@GetMapping` : Pour les requêtes `GET`.
-   `@PostMapping` : Pour les requêtes `POST`.
-   `@PutMapping` : Pour les requêtes `PUT`.
-   `@PatchMapping` : Pour les requêtes `PATCH`.
-   `@DeleteMapping` : Pour les requêtes `DELETE`.

## Construire une API CRUD

Appliquons ces annotations pour construire une API CRUD (Create, Read, Update, Delete) complète pour notre ressource `Book`.

Pour l'instant, nous allons simuler une base de données avec une simple liste en mémoire pour nous concentrer sur la partie contrôleur.

```java
package com.example.demo.controller;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;

import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

@RestController
@RequestMapping("/api/v1/books")
public class BookController {

    // Simule une table de base de données
    private final List<String> books = new ArrayList<>(List.of("The Lord of the Rings", "The Hobbit"));

    // --- READ ---
    @GetMapping
    public List<String> getAllBooks() {
        return books;
    }

    @GetMapping("/{id}") // e.g., /api/v1/books/0
    public String getBookById(@PathVariable int id) {
        return books.get(id); // Attention : gestion d'erreur simplifiée
    }

    // --- CREATE ---
    @PostMapping
    @ResponseStatus(HttpStatus.CREATED) // Retourne un code 201 Created
    public String createBook(@RequestBody String newBook) {
        books.add(newBook);
        return newBook;
    }

    // --- UPDATE ---
    @PutMapping("/{id}")
    public String updateBook(@PathVariable int id, @RequestBody String updatedBook) {
        books.set(id, updatedBook); // Attention : gestion d'erreur simplifiée
        return updatedBook;
    }

    // --- DELETE ---
    @DeleteMapping("/{id}")
    @ResponseStatus(HttpStatus.NO_CONTENT) // Retourne un code 204 No Content
    public void deleteBook(@PathVariable int id) {
        books.remove(id); // Attention : gestion d'erreur simplifiée
    }
}
```

### Analyse des Nouveautés

#### 1. Variables de Chemin (`@PathVariable`)

-   **Syntaxe :** L'URL contient une partie variable dénotée par des accolades : `/{id}`.
-   **Annotation :** L'annotation `@PathVariable` sur un paramètre de méthode lie la valeur de la variable dans l'URL à ce paramètre.
-   **Exemple :** Une requête `GET /api/v1/books/1` appellera la méthode `getBookById`, et la variable `id` aura la valeur `1`. Spring s'occupe de la conversion de type (ici, de `String` à `int`).

#### 2. Corps de la Requête (`@RequestBody`)

-   **Annotation :** L'annotation `@RequestBody` indique à Spring de désérialiser le corps de la requête HTTP (qui est généralement en JSON) en un objet Java.
-   **Exemple (Création) :** Pour créer un livre, le client enverra une requête `POST` avec un corps JSON comme `"Dune"`. Spring va prendre cette chaîne et la passer au paramètre `newBook` de la méthode `createBook`. Nous verrons dans une leçon future comment gérer des objets JSON plus complexes (DTOs).

#### 3. Codes de Statut HTTP (`@ResponseStatus`)

-   Par défaut, une requête réussie retourne un code de statut `200 OK`.
-   Cependant, les conventions REST suggèrent d'utiliser des codes plus spécifiques :
    -   `201 Created` après une création réussie (`POST`).
    -   `204 No Content` après une suppression réussie (`DELETE`), car il n'y a pas de contenu à retourner.
-   L'annotation `@ResponseStatus` permet de changer le code de statut HTTP retourné par la méthode.

## Tester l'API CRUD

Vous pouvez maintenant tester chaque endpoint avec un client API comme Postman ou `curl`.

-   **`GET /api/v1/books`** : Récupère tous les livres.
-   **`GET /api/v1/books/0`** : Récupère le premier livre.
-   **`POST /api/v1/books`** avec le corps `"Dune"` : Ajoute "Dune" à la liste.
-   **`PUT /api/v1/books/1`** avec le corps `"The Hobbit: An Unexpected Journey"` : Met à jour le deuxième livre.
-   **`DELETE /api/v1/books/0`** : Supprime le premier livre.

**Note importante sur la gestion des erreurs :** Le code ci-dessus est volontairement simplifié. Si vous demandez un livre avec un ID qui n'existe pas, l'application lèvera une `IndexOutOfBoundsException`, ce qui résultera en une réponse `500 Internal Server Error`. Une API robuste doit gérer ces cas et retourner des codes d'erreur appropriés (comme `404 Not Found`). Nous aborderons la gestion des erreurs dans un chapitre ultérieur.
