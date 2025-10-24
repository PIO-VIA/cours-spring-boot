# Leçon 04 : Tester la Couche de Données (Repositories)

Tester la couche de persistance est crucial pour s'assurer que vos entités JPA sont correctement mappées et que vos requêtes fonctionnent comme prévu. Pour cela, Spring Boot propose l'annotation `@DataJpaTest`.

### 1. L'Annotation `@DataJpaTest`

`@DataJpaTest` est un autre "slice test" qui se concentre uniquement sur les composants JPA. Il va :
- Configurer une base de données en mémoire (H2 par défaut).
- Scanner vos entités (`@Entity`) et vos repositories Spring Data (`@Repository`).
- Ne pas charger les autres beans (`@Service`, `@Controller`, etc.).
- Rendre chaque test transactionnel et effectuer un rollback à la fin. Cela garantit que chaque test est isolé des autres.

### 2. Le Scénario

Nous voulons tester notre `ProduitRepository` pour nous assurer que la méthode `findByName` que nous avons définie fonctionne.

**L'Entité**
```java
@Entity
public class Produit {
    @Id @GeneratedValue
    private Long id;
    private String name;
    private double price;
    // Constructeurs, getters, setters...
}
```

**Le Repository**
```java
public interface ProduitRepository extends JpaRepository<Produit, Long> {
    Optional<Produit> findByName(String name);
}
```

### 3. Créer la Classe de Test

Pour tester le repository, nous allons utiliser `@DataJpaTest` et un `TestEntityManager`, un utilitaire fourni par Spring Boot pour manipuler les entités dans les tests.

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.boot.test.autoconfigure.orm.jpa.TestEntityManager;

import java.util.Optional;

import static org.assertj.core.api.Assertions.assertThat;

@DataJpaTest
class ProduitRepositoryTest {

    // TestEntityManager est une alternative à l'EntityManager standard,
    // spécialement conçue pour les tests.
    @Autowired
    private TestEntityManager entityManager;

    @Autowired
    private ProduitRepository produitRepository;

    @Test
    void quandFindByName_alorsRetournerProduit() {
        // Arrange
        Produit produit = new Produit("Livre", 25.0);
        entityManager.persist(produit); // Sauvegarde l'entité dans la BDD de test
        entityManager.flush(); // Force la synchronisation avec la BDD

        // Act
        Optional<Produit> produitTrouve = produitRepository.findByName("Livre");

        // Assert
        assertThat(produitTrouve).isPresent();
        assertThat(produitTrouve.get().getName()).isEqualTo(produit.getName());
    }

    @Test
    void quandFindByNameInexistant_alorsRetournerEmpty() {
        // Act
        Optional<Produit> produitTrouve = produitRepository.findByName("Inexistant");

        // Assert
        assertThat(produitTrouve).isNotPresent();
    }
}
```

### Explication :
- `@DataJpaTest` : Prépare tout l'environnement de test pour la couche JPA.
- `TestEntityManager` : Nous l'utilisons pour préparer les données nécessaires à notre test (`Arrange`). `persist()` met l'objet dans le contexte de persistance, et `flush()` écrit les changements en base.
- `@Autowired ProduitRepository` : Même si c'est une interface, Spring Data JPA crée une implémentation concrète à notre place, que nous pouvons injecter et utiliser.

Avec `@DataJpaTest`, vous pouvez tester toutes vos méthodes de repository, y compris les requêtes personnalisées (`@Query`), avec la certitude qu'elles fonctionnent au niveau de la base de données.

Ce chapitre sur les tests vous a donné les outils pour tester efficacement chaque couche de votre application Spring Boot, une compétence indispensable pour tout développeur professionnel.
