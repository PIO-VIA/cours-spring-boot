# Leçon 02 : Monitoring de l'application avec Spring Boot Actuator

## 1. Introduction

Une fois une application déployée en production, il est crucial de pouvoir la surveiller (monitoring) pour s'assurer de son bon fonctionnement, vérifier son état de santé et accéder à des métriques de performance.

**Spring Boot Actuator** est un sous-projet de Spring Boot qui fournit des fonctionnalités prêtes à l'emploi pour le monitoring et la gestion d'une application en production.

## 2. Mise en place

Pour utiliser Actuator, il suffit d'ajouter sa dépendance dans le `pom.xml` (pour Maven) ou `build.gradle` (pour Gradle).

**Dépendance Maven :**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Une fois la dépendance ajoutée, Spring Boot expose automatiquement plusieurs **endpoints** (points d'accès) via HTTP ou JMX.

## 3. Endpoints principaux

Par défaut, la plupart des endpoints sont exposés via JMX mais seulement `/health` et `/info` sont exposés via HTTP pour des raisons de sécurité.

Voici quelques-uns des endpoints les plus utiles :

-   `GET /actuator/health` : Affiche l'état de santé de l'application. Le statut peut être `UP`, `DOWN`, etc. Il peut agréger l'état de plusieurs composants (base de données, service de messagerie, etc.).
-   `GET /actuator/info` : Affiche des informations générales sur l'application, que vous pouvez personnaliser.
-   `GET /actuator/metrics` : Fournit des métriques détaillées sur l'application (utilisation de la mémoire, charge du CPU, requêtes HTTP, etc.).
-   `GET /actuator/env` : Affiche les variables d'environnement et les propriétés de configuration de l'application.
-   `GET /actuator/loggers` : Permet de visualiser et de modifier les niveaux de log à la volée.
-   `GET /actuator/mappings` : Affiche la liste de tous les endpoints `@RequestMapping` de l'application.

## 4. Configuration

Pour exposer plus d'endpoints via HTTP, vous pouvez le configurer dans votre fichier `application.properties`.

```properties
# Exposer tous les endpoints (à utiliser avec prudence en production)
management.endpoints.web.exposure.include=*

# N'exposer que certains endpoints
# management.endpoints.web.exposure.include=health,info,metrics

# Exclure certains endpoints
# management.endpoints.web.exposure.exclude=env
```

Il est également possible de changer le port sur lequel les endpoints d'Actuator sont exposés pour les séparer du port principal de l'application :
```properties
# Les endpoints d'actuator seront accessibles sur le port 9001
management.server.port=9001
```

## 5. Sécurisation des endpoints

Exposer des informations sensibles comme la configuration (`/env`) ou les logs (`/loggers`) peut représenter un risque de sécurité. Il est **fortement recommandé** de sécuriser les endpoints d'Actuator en production.

Si Spring Security est présent dans le projet, les endpoints d'Actuator sont automatiquement sécurisés par défaut. Vous devrez alors configurer les accès comme pour le reste de votre application.

## 6. Conclusion

Spring Boot Actuator est un outil indispensable pour toute application Spring Boot destinée à la production. Il offre une visibilité essentielle sur l'état interne de l'application avec un minimum de configuration, facilitant ainsi la maintenance, le débogage et le monitoring.
