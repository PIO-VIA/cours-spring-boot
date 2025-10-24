# Leçon 04 : Sécurité de Base avec Spring Security

Assurer la sécurité de votre application est une priorité absolue, surtout en environnement de production. Spring Boot s'intègre parfaitement avec Spring Security, un framework puissant et hautement personnalisable pour gérer l'authentification et les autorisations.

## 1. Ajouter Spring Security

Pour commencer, il suffit d'ajouter la dépendance `spring-boot-starter-security` à votre `pom.xml` (pour Maven) ou `build.gradle` (pour Gradle).

**Maven (`pom.xml`)**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

Dès que cette dépendance est présente dans le classpath, Spring Boot applique une configuration de sécurité automatique par défaut.

## 2. Comportement par Défaut

Par défaut, Spring Security :
- Sécurise tous les points de terminaison (endpoints) de votre application.
- Génère un utilisateur `user` avec un mot de passe aléatoire affiché dans la console au démarrage.
- Crée une page de connexion (`/login`) pour l'authentification.
- Protège contre les attaques CSRF (Cross-Site Request Forgery).

Cette configuration est un excellent point de départ mais doit être personnalisée pour les besoins réels de votre application.

## 3. Personnaliser la Configuration de Sécurité

Pour personnaliser la sécurité, on crée généralement une classe de configuration annotée avec `@Configuration` et `@EnableWebSecurity`.

Voici un exemple de configuration de base qui sécurise tous les endpoints sauf la page d'accueil (`/`).

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(authorizeRequests ->
                authorizeRequests
                    .requestMatchers("/").permitAll() // Autorise l'accès à la racine sans authentification
                    .anyRequest().authenticated()     // Exige une authentification pour toutes les autres requêtes
            )
            .formLogin(); // Active la page de connexion par formulaire

        return http.build();
    }
}
```

### Explication :
- `@EnableWebSecurity`: Active le support de la sécurité web de Spring.
- `SecurityFilterChain`: C'est le bean principal qui définit la chaîne de filtres de sécurité.
- `authorizeHttpRequests`: C'est ici que l'on définit les règles d'autorisation.
- `requestMatchers("/").permitAll()`: Autorise toutes les requêtes HTTP vers l'URL `/`.
- `anyRequest().authenticated()`: Pour toutes les autres requêtes, l'utilisateur doit être authentifié.
- `formLogin()`: Indique à Spring Security d'utiliser une page de connexion basée sur un formulaire HTML.

## 4. Gérer les Utilisateurs

La configuration par défaut utilise un utilisateur en mémoire. Pour des cas réels, vous voudrez vous connecter à une base de données. C'est un sujet avancé qui sera couvert plus tard, mais cela implique généralement d'implémenter l'interface `UserDetailsService`.

---

Cette leçon couvre les bases pour mettre en place Spring Security. C'est un outil très vaste, mais cette première configuration est la porte d'entrée pour sécuriser efficacement vos applications Spring Boot.
