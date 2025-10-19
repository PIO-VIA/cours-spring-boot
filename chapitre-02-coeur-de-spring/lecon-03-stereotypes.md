# Chapitre 2 - Leçon 3 : Stéréotypes : `@Component`, `@Service`, `@Repository`

Nous avons vu que l'annotation `@Component` est une manière générique de déclarer un bean. Cependant, Spring fournit des annotations plus spécifiques, appelées **stéréotypes**, qui non seulement font la même chose, mais ajoutent aussi une signification sémantique à vos beans.

Les trois stéréotypes principaux sont `@Service`, `@Repository`, et `@Controller` (ou `@RestController` que nous avons déjà vu).

## `@Component` : Le Stéréotype de Base

`@Component` est l'annotation générique pour n'importe quel bean géré par Spring. Si un bean ne rentre dans aucune des catégories plus spécifiques ci-dessous, vous pouvez utiliser `@Component`.

**Exemple :** Un parser de fichier, un client pour une API externe, un composant utilitaire.

```java
package com.example.demo.util;

import org.springframework.stereotype.Component;

@Component
public class JsonParser {
    // ... logique pour parser du JSON
}
```

## `@Service` : Pour la Logique Métier

L'annotation `@Service` est utilisée pour marquer les beans qui contiennent la **logique métier** de votre application (les "business rules"). C'est la couche qui orchestre les opérations, traite les données et met en œuvre les cas d'utilisation.

Sémantiquement, un service est souvent appelé par un contrôleur et peut faire appel à des repositories ou à d'autres services.

**Exemple :** `UserService`, `OrderService`, `ProductService`.

```java
package com.example.demo.service;

import com.example.demo.repository.UserRepository;
import org.springframework.stereotype.Service;

@Service
public class UserService {

    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public void registerUser(String username) {
        if (userRepository.findByUsername(username).isPresent()) {
            throw new IllegalStateException("Username already exists");
        }
        // ... logique de création de l'utilisateur
    }
}
```

Techniquement, `@Service` ne fait rien de plus que `@Component` à l'heure actuelle. Cependant, son utilisation est une **bonne pratique** car :
1.  **Clarté :** Elle indique clairement le rôle de la classe dans l'architecture.
2.  **Évolutivité :** Dans le futur, Spring pourrait ajouter des fonctionnalités spécifiques à cette annotation.

## `@Repository` : Pour la Persistance des Données

L'annotation `@Repository` est utilisée pour marquer les beans qui sont responsables de l'accès aux données. Ces classes, souvent appelées *Data Access Objects (DAO)*, encapsulent la logique pour communiquer avec une source de données (base de données SQL, NoSQL, etc.).

**Exemple :** `UserRepository`, `ProductRepository`.

```java
package com.example.demo.repository;

import org.springframework.stereotype.Repository;

@Repository
public class UserRepository {
    // ... logique pour interagir avec la table des utilisateurs en base de données
    public Optional<User> findByUsername(String username) {
        // ...
    }
}
```

Contrairement à `@Service`, `@Repository` a un avantage technique important : elle active la **traduction d'exceptions**.

### La Traduction d'Exceptions

Les frameworks de persistance (comme JPA/Hibernate) lèvent souvent leurs propres exceptions techniques (par exemple, `PersistenceException`). Ces exceptions sont spécifiques à la technologie utilisée.

Si un bean est annoté avec `@Repository`, Spring active un post-processeur qui va intercepter ces exceptions spécifiques à la plateforme et les retraduire en exceptions de la hiérarchie de Spring, plus génériques et non-cochées (`DataAccessException`).

**Avantage :** Votre couche de service n'a pas besoin de connaître les détails de l'implémentation de la persistance. Elle peut attraper une `DataAccessException` sans se soucier de savoir si la couche de persistance utilise JPA, JDBC, ou autre chose. Cela réduit le couplage.

## `@Controller` / `@RestController` : Pour la Couche Web

Comme nous l'avons vu, ces annotations sont utilisées pour la couche de présentation (web). Elles marquent les beans qui gèrent les requêtes HTTP. Nous les explorerons en détail dans le chapitre sur Spring MVC.

## Pourquoi utiliser les Stéréotypes ?

1.  **Lisibilité et Intention :** En un coup d'œil, un développeur peut comprendre le rôle d'une classe dans l'architecture de l'application. C'est un `Service`, donc il contient de la logique métier. C'est un `Repository`, donc il parle à la base de données.
2.  **Détection Automatique (`ComponentScan`) :** Ces annotations permettent à Spring de trouver et d'enregistrer automatiquement vos beans.
3.  **Fonctionnalités Additionnelles :** Comme vu avec `@Repository`, les stéréotypes peuvent activer des fonctionnalités spécifiques au framework.

En résumé, bien que vous puissiez tout déclarer avec `@Component`, l'utilisation des stéréotypes appropriés est une convention essentielle pour construire des applications Spring claires, maintenables et robustes.
