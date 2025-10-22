# Leçon 01 : Les profils Spring pour gérer différents environnements

## 1. Introduction

Lors du développement d'une application, il est courant de devoir gérer différentes configurations pour différents environnements (développement, test, production, etc.). Par exemple, la base de données utilisée en développement n'est généralement pas la même que celle utilisée en production.

Spring Boot simplifie cette gestion grâce au concept de **profils**. Un profil est un ensemble de configurations qui ne sont activées que lorsque ce profil est actif.

## 2. Définir des configurations par profil

La manière la plus simple de définir des configurations spécifiques à un profil est d'utiliser des fichiers `application-{profil}.properties` ou `application-{profil}.yml`.

Par exemple, dans le répertoire `src/main/resources`, on peut avoir :

**`application.properties` (configuration par défaut)**
```properties
# Port par défaut
server.port=8080
```

**`application-dev.properties` (pour l'environnement de développement)**
```properties
# Port pour le développement
server.port=9090
# Configuration d'une base de données en mémoire
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=password
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
```

**`application-prod.properties` (pour l'environnement de production)**
```properties
# Port pour la production
server.port=80
# Configuration de la base de données de production
spring.datasource.url=jdbc:postgresql://prod-db-host:5432/mydatabase
spring.datasource.username=prod_user
spring.datasource.password=secret_password
spring.jpa.hibernate.ddl-auto=validate
```

## 3. Activer un profil

Il existe plusieurs manières d'activer un ou plusieurs profils :

**1. Dans `application.properties` :**
```properties
# Active le profil 'dev' par défaut si aucun autre n'est spécifié
spring.profiles.active=dev
```

**2. En ligne de commande lors du lancement de l'application :**
```bash
java -jar my-application.jar --spring.profiles.active=prod
```

**3. Avec une variable d'environnement :**
```bash
export SPRING_PROFILES_ACTIVE=prod
java -jar my-application.jar
```

Si aucun profil n'est activé, seules les propriétés du fichier `application.properties` seront chargées. Si un profil est actif (par exemple, `dev`), Spring chargera d'abord `application.properties`, puis `application-dev.properties`. Les propriétés définies dans `application-dev.properties` écraseront celles du fichier par défaut.

## 4. Utiliser les profils dans le code

On peut également activer ou désactiver des beans en fonction du profil actif à l'aide de l'annotation `@Profile`.

```java
package com.example.cours.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;

@Configuration
public class AppConfig {

    @Bean
    @Profile("dev")
    public void devBean() {
        System.out.println("Ce bean est créé uniquement pour le profil 'dev'.");
    }

    @Bean
    @Profile("prod")
    public void prodBean() {
        System.out.println("Ce bean est créé uniquement pour le profil 'prod'.");
    }

    @Bean
    @Profile("!prod") // Tous les profils sauf 'prod'
    public void notProdBean() {
        System.out.println("Ce bean est créé pour tous les profils sauf 'prod'.");
    }
}
```

## 5. Conclusion

Les profils Spring sont un mécanisme puissant et essentiel pour construire des applications flexibles et facilement configurables pour différents environnements. Ils permettent de séparer clairement les configurations et d'éviter les erreurs liées à un mauvais paramétrage.
