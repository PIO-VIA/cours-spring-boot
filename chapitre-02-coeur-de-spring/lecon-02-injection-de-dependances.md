# Chapitre 2 - Leçon 2 : L'Injection de Dépendances (DI)

L'Injection de Dépendances (Dependency Injection - DI) est le mécanisme par lequel le conteneur Spring fournit à un bean les instances des autres beans dont il a besoin. C'est la mise en pratique du principe d'Inversion de Contrôle.

Reprenons notre exemple `UserService` qui a besoin de `NotificationService`. Avec Spring, `UserService` ne créera plus lui-même l'instance de `NotificationService`. Il va simplement la *déclarer* comme une dépendance, et Spring se chargera de la lui *fournir* (l'injecter).

Il existe trois manières principales de réaliser l'injection de dépendances.

## 1. Injection par Constructeur (Recommandée)

C'est la méthode **privilégiée et recommandée** par l'équipe de Spring et la communauté.

```java
package com.example.demo.service;

import org.springframework.stereotype.Service;

@Service // @Service est une spécialisation de @Component pour la logique métier
public class UserService {

    private final NotificationService notificationService;

    // Le constructeur déclare la dépendance
    public UserService(NotificationService notificationService) {
        this.notificationService = notificationService;
    }

    public void registerUser(String username) {
        // ... logique d'enregistrement ...
        notificationService.sendNotification("Welcome " + username);
    }
}
```

### Comment ça marche ?

1.  `UserService` est déclaré comme un bean (`@Service`).
2.  Il possède un constructeur qui accepte un `NotificationService` en paramètre.
3.  Au démarrage, lorsque Spring doit créer le bean `UserService`, il inspecte son constructeur.
4.  Il voit qu'il a besoin d'un bean de type `NotificationService`.
5.  Il cherche alors dans son contexte (`ApplicationContext`) une instance de `NotificationService` (que nous avons définie avec `@Component` dans la leçon précédente).
6.  Il la trouve et la passe en argument au constructeur de `UserService` pour créer l'instance.

### Pourquoi est-ce la meilleure méthode ?

- **Immutabilité :** En déclarant le champ `notificationService` comme `final`, on garantit que la dépendance ne pourra pas être modifiée après la création de l'objet. Cela rend nos objets plus robustes et *thread-safe*.
- **Dépendances Explicites :** Le contrat de la classe est clair. Le constructeur liste tout ce dont la classe a besoin pour fonctionner. On ne peut pas créer une instance de `UserService` sans lui fournir toutes ses dépendances.
- **Facilité de Test :** C'est extrêmement simple à tester. En test unitaire, pas besoin de Spring. On peut simplement faire `new UserService(mockNotificationService)` en passant un mock de `NotificationService`.
- **Pas d'ambiguïté :** Depuis Spring 4.3, si une classe n'a qu'un seul constructeur, l'annotation `@Autowired` est implicite et facultative. Le code est donc plus propre.

## 2. Injection par Setter (Moins courante)

Cette méthode utilise une méthode `set` (un mutateur) pour injecter la dépendance.

```java
@Service
public class UserService {

    private NotificationService notificationService;

    @Autowired // L'annotation est ici obligatoire
    public void setNotificationService(NotificationService notificationService) {
        this.notificationService = notificationService;
    }
    
    // ...
}
```

### Inconvénients :

- **Dépendances Optionnelles :** Cette méthode permet de créer le bean `UserService` sans sa dépendance, qui sera injectée plus tard. Cela peut mener à des `NullPointerException` si la dépendance n'est pas correctement injectée.
- **Mutabilité :** Le champ ne peut pas être `final`. La dépendance peut être changée à n'importe quel moment, ce qui peut être une source de bugs.
- **Verbosité :** Le code est plus verbeux que l'injection par constructeur.

## 3. Injection par Champ (À Éviter)

Cette méthode injecte la dépendance directement dans le champ de la classe.

```java
@Service
public class UserService {

    @Autowired // L'annotation est ici obligatoire
    private NotificationService notificationService;
    
    // ...
}
```

Bien que ce soit la syntaxe la plus concise, elle est **fortement déconseillée** pour plusieurs raisons :

- **Violation des Principes de l'OOP :** Elle cache les dépendances de la classe. Rien dans le contrat public de la classe (ses méthodes et constructeurs) n'indique qu'elle a besoin d'un `NotificationService`.
- **Difficulté de Test Extrême :** Il est très difficile de tester cette classe en isolation. Pour injecter un mock, il faut utiliser des techniques de réflexion (reflection), ce qui est complexe et fragile.
- **Couplage au Conteneur :** La classe ne peut être instanciée qu'au sein d'un conteneur Spring qui comprend l'annotation `@Autowired`. Vous ne pouvez plus faire un simple `new UserService()` en dehors de Spring (même pour un test).
- **Immutabilité Impossible :** Le champ ne peut pas être `final`.

---

**Conclusion :** Utilisez **toujours l'injection par constructeur**. C'est la méthode la plus propre, la plus sûre et la plus facile à tester. Elle vous force à écrire du code mieux conçu et moins couplé.
