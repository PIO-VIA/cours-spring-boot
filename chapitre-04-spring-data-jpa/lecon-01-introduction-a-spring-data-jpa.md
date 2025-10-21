# Chapitre 4 : Spring Data JPA

## Leçon 1 : Introduction à Spring Data JPA

Après avoir appris à créer des API REST avec Spring MVC, il est temps de connecter notre application à une base de données pour persister les données. Spring Data JPA est un module de Spring qui simplifie considérablement l'accès aux données.

### 1. Qu'est-ce que JPA et un ORM ?

- **JPA (Java Persistence API)** : C'est une spécification (un ensemble de règles et d'interfaces) de Java qui décrit comment gérer les données relationnelles dans les applications Java. Elle fournit une approche standard pour la persistance.

- **ORM (Object-Relational Mapping)** : C'est une technique de programmation qui permet de convertir les données entre un modèle objet (comme les classes Java) et un modèle relationnel (comme les tables d'une base de données). Hibernate est l'implémentation de JPA la plus populaire et celle utilisée par défaut par Spring Boot.

L'ORM nous permet de manipuler la base de données en utilisant des objets Java, sans avoir à écrire de code SQL répétitif.

### 2. Le rôle de Spring Data JPA

Spring Data JPA n'est pas une implémentation de JPA. C'est une surcouche qui ajoute un niveau d'abstraction supplémentaire au-dessus de l'ORM (comme Hibernate). Son objectif principal est de réduire la quantité de code nécessaire pour implémenter la couche d'accès aux données.

Avec Spring Data JPA, vous n'avez souvent plus besoin d'écrire les implémentations de vos classes DAO (Data Access Object). Il vous suffit de définir des interfaces.

### 3. Dépendances et Configuration

Pour utiliser Spring Data JPA, vous avez besoin de deux dépendances principales dans votre `pom.xml` :

1.  `spring-boot-starter-data-jpa` : Inclut Spring Data, Hibernate et tout le nécessaire pour la persistance.
2.  Un driver de base de données : Par exemple, `h2`, `postgresql`, ou `mysql-connector-java`.

Pour commencer, nous utiliserons H2, une base de données en mémoire très pratique pour le développement.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```

### 4. Configuration de la source de données

Ensuite, vous devez configurer la connexion à la base de données dans votre fichier `src/main/resources/application.properties`.

Pour une base de données H2 en mémoire :

```properties
# URL de connexion à la base de données H2
spring.datasource.url=jdbc:h2:mem:testdb
# Nom d'utilisateur de la base de données
spring.datasource.username=sa
# Mot de passe (vide pour H2 par défaut)
spring.datasource.password=
# Driver JDBC
spring.datasource.driver-class-name=org.h2.Driver

# Activer la console web de H2 (optionnel mais utile)
spring.h2.console.enabled=true
```

Avec cette configuration, Spring Boot créera et configurera automatiquement une source de données connectée à une base H2.

---

Dans la prochaine leçon, nous allons créer notre première entité JPA et définir un `Repository` pour interagir avec la base de données.
