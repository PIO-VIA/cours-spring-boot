# Chapitre 2 - Leçon 1 : Les Beans Spring et le Contexte d'Application

Nous entrons maintenant dans le concept le plus fondamental du framework Spring : l'**Inversion de Contrôle (Inversion of Control - IoC)**. Pour comprendre l'IoC, il faut d'abord maîtriser ses deux éléments centraux : les **Beans** et le **Contexte d'Application**.

## Le Problème : La Gestion Traditionnelle des Objets

Imaginez une application sans Spring. Vous avez des objets qui dépendent les uns des autres.

```java
// Un service pour envoyer des notifications
public class NotificationService {
    private final EmailGateway emailGateway;

    public NotificationService() {
        // Le service est responsable de la création de sa dépendance
        this.emailGateway = new EmailGateway();
    }

    public void sendNotification(String message) {
        emailGateway.send(message);
    }
}

// La classe qui utilise le service
public class UserService {
    private final NotificationService notificationService;

    public UserService() {
        // UserService est responsable de la création de NotificationService
        this.notificationService = new NotificationService();
    }

    public void registerUser(String username) {
        // ... logique d'enregistrement ...
        notificationService.sendNotification("Welcome " + username);
    }
}
```

Ce code présente plusieurs problèmes :
- **Fort Couplage :** `UserService` est directement lié à l'implémentation concrète de `NotificationService`. Si nous voulons changer pour un `SmsService`, nous devons modifier le code de `UserService`.
- **Difficulté à Tester :** Il est très difficile de tester `UserService` de manière isolée. Nous ne pouvons pas facilement remplacer `NotificationService` par un *mock* (un objet factice) pour nos tests unitaires.
- **Gestion du Cycle de Vie :** Chaque objet est responsable de la création et de la gestion de ses propres dépendances. C'est complexe et source d'erreurs.

## La Solution Spring : L'Inversion de Contrôle

L'IoC inverse ce modèle. Au lieu que vos objets créent leurs dépendances, vous déléguez cette responsabilité à un conteneur externe. Ce conteneur est responsable de l'instanciation, de la configuration et de l'assemblage des objets.

**Le contrôle de la création des objets est inversé : il passe du développeur (votre code) au conteneur (Spring).**

### Qu'est-ce qu'un Bean Spring ?

Dans le jargon Spring, un **Bean** est simplement un objet qui est instancié, assemblé et géré par le conteneur IoC de Spring.

Au lieu de faire `new MyObject()`, vous demandez à Spring de vous fournir une instance de `MyObject`. Spring se charge de la créer (si elle n'existe pas déjà) et de lui fournir toutes les dépendances dont elle a besoin.

### Le Conteneur IoC : `ApplicationContext`

Le conteneur qui gère les beans s'appelle l'**`ApplicationContext`** (le Contexte d'Application). C'est le cœur de votre application Spring.

Lorsque votre application Spring Boot démarre (via `SpringApplication.run()`), elle crée une instance de l'`ApplicationContext`. Ce contexte va :
1.  **Scanner votre projet :** Il cherche des classes que vous avez marquées comme étant des candidats pour devenir des beans (par exemple, avec des annotations comme `@Component`, `@Service`, `@RestController`).
2.  **Instancier les Beans :** Il crée des instances de ces classes.
3.  **Gérer le Cycle de Vie :** Il gère le cycle de vie complet des beans, de leur création à leur destruction.
4.  **Injecter les Dépendances :** Si un bean A a besoin d'un bean B, le conteneur va "injecter" une instance du bean B dans le bean A. C'est ce qu'on appelle l'**Injection de Dépendances (Dependency Injection - DI)**, qui est la principale manière de mettre en œuvre l'IoC.

## Comment définir un Bean ?

La manière la plus simple de dire à Spring "cette classe doit être un bean" est d'utiliser des annotations de stéréotype. La plus générique est `@Component`.

```java
package com.example.demo.service;

import org.springframework.stereotype.Component;

@Component // Indique à Spring de gérer cette classe comme un bean
public class NotificationService {
    
    public void sendNotification(String message) {
        System.out.println("Sending notification: " + message);
    }
}
```

Grâce à l'annotation `@ComponentScan` (incluse dans `@SpringBootApplication`), Spring va détecter cette classe lors du démarrage, créer une instance de `NotificationService` et la placer dans l'`ApplicationContext`. Cette instance unique (par défaut) est appelée un **singleton**.

---

Dans la prochaine leçon, nous verrons comment un bean peut en obtenir un autre grâce à l'Injection de Dépendances. C'est là que la puissance de l'IoC se révèle pleinement.
