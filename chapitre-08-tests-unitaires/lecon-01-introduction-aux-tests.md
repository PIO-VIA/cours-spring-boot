# Chapitre 08 : Tests Unitaires et d'Intégration

## Leçon 01 : Introduction aux Tests dans Spring Boot

Écrire des tests est une pratique fondamentale pour garantir la qualité, la robustesse et la maintenabilité de votre code. Spring Boot simplifie grandement la mise en place de tests grâce à son module `spring-boot-starter-test`.

### 1. Le Starter de Test

Lorsque vous créez un projet Spring Boot via `start.spring.io`, la dépendance `spring-boot-starter-test` est incluse par défaut. Elle regroupe les bibliothèques les plus courantes et utiles pour les tests :

- **JUnit 5** : La nouvelle génération de JUnit, le standard de facto pour les tests en Java.
- **Spring Test & Spring Boot Test** : Des utilitaires et annotations pour charger un contexte Spring et tester votre application de manière fine.
- **AssertJ** : Une bibliothèque d'assertions fluides qui rend vos tests plus lisibles (ex: `assertThat(monObjet).isNotNull();`).
- **Mockito** : Un framework de "mocking" (simulation) très populaire pour isoler les classes que vous testez de leurs dépendances.
- **Hamcrest** : Une bibliothèque pour écrire des "matchers" (règles de correspondance).
- **JsonPath** : Pour les assertions sur des documents JSON.

### 2. Types de Tests

Dans une application Spring, on distingue principalement deux types de tests :

- **Tests Unitaires (Unit Tests)** : Ils testent une seule classe (une "unité") de manière isolée. Les dépendances de cette classe sont généralement "mockées" (simulées) pour ne tester que la logique métier de la classe elle-même. Ces tests sont très rapides.

- **Tests d'Intégration (Integration Tests)** : Ils testent l'interaction entre plusieurs composants. Par exemple, tester un contrôleur qui appelle un service, qui lui-même interagit avec une base de données. Ces tests sont plus lents car ils nécessitent de démarrer une partie ou la totalité du contexte Spring.

### 3. Structure d'une Classe de Test

Les tests se trouvent dans le répertoire `src/test/java`. Une classe de test ressemble à ceci :

```java
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;
import static org.assertj.core.api.Assertions.assertThat;

// Indique que c'est un test Spring Boot qui doit charger le contexte de l'application
@SpringBootTest
class MonApplicationTests {

    // Chaque méthode annotée avec @Test est un cas de test
    @Test
    void contextLoads() {
        // Un test simple qui vérifie que le contexte de l'application se charge sans erreur
    }

    @Test
    void unTestExemple() {
        String nom = "Spring Boot";
        assertThat(nom).isEqualTo("Spring Boot");
        assertThat(nom).isNotNull();
    }
}
```

- `@SpringBootTest` : C'est l'annotation principale. Elle charge le contexte complet de l'application Spring, ce qui est utile pour les tests d'intégration.
- `@Test` : Marque une méthode comme étant une méthode de test.

Dans les leçons suivantes, nous verrons comment écrire des tests unitaires et d'intégration pour chaque couche de notre application (Contrôleur, Service, Repository).
