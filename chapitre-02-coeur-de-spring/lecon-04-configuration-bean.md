# Chapitre 2 - Leçon 4 : La configuration avec `@Configuration` et `@Bean`

Jusqu'à présent, nous avons laissé Spring détecter automatiquement nos beans grâce au *component scanning* (`@ComponentScan`) et aux stéréotypes (`@Service`, `@Repository`, etc.). C'est ce qu'on appelle la **configuration par stéréotypes**.

Cependant, il y a des cas où cette méthode n'est pas suffisante ou souhaitable :
-   Quand on veut créer un bean à partir d'une classe qui ne fait pas partie de notre projet (une classe d'une bibliothèque externe). On ne peut pas ajouter `@Component` à son code source.
-   Quand la création d'un objet est complexe et nécessite une logique de configuration particulière.

Pour ces cas, Spring propose une méthode de configuration explicite et centralisée : la **configuration Java** avec les annotations `@Configuration` et `@Bean`.

## `@Configuration` : La Classe de Configuration

Une classe annotée avec `@Configuration` indique à Spring qu'elle contient des définitions de beans. C'est l'équivalent moderne des anciens fichiers de configuration XML.

```java
package com.example.demo.config;

import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {
    // ... les définitions de beans viendront ici
}
```

Spring va automatiquement détecter cette classe (car elle est elle-même un `@Component`) et traiter les méthodes annotées avec `@Bean` qu'elle contient.

## `@Bean` : La Méthode "Usine" de Bean

Une méthode annotée avec `@Bean` dans une classe `@Configuration` est une sorte d'**usine à beans**. Elle dit à Spring : "Quand quelqu'un a besoin d'un bean de ce type, exécute cette méthode et utilise l'objet qu'elle retourne".

**Exemple :** Imaginons que nous utilisions une bibliothèque externe qui fournit une classe `ExternalApiClient`. Nous ne pouvons pas modifier son code pour y ajouter `@Component`. Voici comment nous pouvons la déclarer comme un bean :

```java
package com.example.demo.config;

import com.example.external.ExternalApiClient; // Classe d'une lib externe
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean // Cette méthode produit un bean qui sera placé dans le contexte Spring
    public ExternalApiClient externalApiClient() {
        // Ici, nous avons le contrôle total sur la création de l'objet
        ExternalApiClient client = new ExternalApiClient();
        client.setApiKey("VOTRE_API_KEY_PROVIENT_D_UN_FICHIER_DE_PROPRIETES");
        client.setTimeout(5000); // 5 secondes
        return client;
    }
}
```

### Comment ça marche ?

1.  Au démarrage, Spring trouve la classe `AppConfig` annotée avec `@Configuration`.
2.  Il trouve la méthode `externalApiClient()` annotée avec `@Bean`.
3.  Il exécute cette méthode.
4.  L'objet `ExternalApiClient` retourné par la méthode est enregistré comme un bean dans l'`ApplicationContext`.
5.  Par défaut, le nom du bean est le nom de la méthode (`externalApiClient`). Le type du bean est le type de retour (`ExternalApiClient`).

Maintenant, n'importe quel autre bean de notre application peut recevoir ce client via l'injection de dépendances :

```java
@Service
public class MyService {

    private final ExternalApiClient apiClient;

    // Spring va trouver le bean créé par notre méthode @Bean et l'injecter ici
    public MyService(ExternalApiClient apiClient) {
        this.apiClient = apiClient;
    }
}
```

## Combiner les Deux Approches

Dans une application Spring Boot typique, les deux approches de configuration cohabitent :

1.  **Configuration par stéréotypes (Component Scanning) :** C'est l'approche par défaut pour **vos propres classes** (`services`, `repositories`, `controllers`...). C'est simple, rapide et décentralisé.
2.  **Configuration Java (`@Configuration`/`@Bean`) :** C'est l'approche utilisée pour :
    -   Intégrer des **bibliothèques tierces**.
    -   Définir des beans dont la **configuration est complexe** ou conditionnelle.
    -   Centraliser la configuration d'un aspect particulier de l'application (par exemple, `SecurityConfig`, `CacheConfig`).

## Un exemple plus complexe : les dépendances entre Beans

Que se passe-t-il si un bean défini avec `@Bean` dépend d'un autre bean ? C'est très simple.

```java
@Configuration
public class AppConfig {

    @Bean
    public DataSource dataSource() {
        // Configuration de la source de données (ex: PostgreSQL)
        // ...
        return new PostgreSQLDataSource(...);
    }

    @Bean
    public JdbcTemplate jdbcTemplate(DataSource dataSource) { // On déclare la dépendance
        // Spring voit qu'il a besoin d'un bean DataSource.
        // Il va trouver celui défini par la méthode dataSource() ci-dessus et l'injecter.
        return new JdbcTemplate(dataSource);
    }
}
```

Spring est assez intelligent pour gérer l'ordre de création des beans en fonction de leurs dépendances.

---

Ceci conclut notre exploration du cœur de Spring. Vous comprenez maintenant comment Spring gère les objets (Beans), comment il les connecte entre eux (Injection de Dépendances) et les différentes manières de les déclarer (Stéréotypes vs Configuration Java). Ces concepts sont la base de tout ce que nous allons construire par la suite.
