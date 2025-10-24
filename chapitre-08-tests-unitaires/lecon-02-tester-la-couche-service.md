# Leçon 02 : Tester la Couche Service

Tester la couche service est un excellent exemple de test unitaire. L'objectif est de vérifier la logique métier d'une classe de service sans dépendre des couches adjacentes (comme la base de données). Pour cela, nous allons utiliser Mockito pour simuler les dépendances.

### 1. Le Scénario

Imaginons un `ProduitService` qui dépend d'un `ProduitRepository` pour récupérer des produits.

**Le Repository (interface)**
```java
public interface ProduitRepository extends JpaRepository<Produit, Long> {
    Optional<Produit> findByName(String name);
}
```

**Le Service à tester**
```java
@Service
public class ProduitService {

    private final ProduitRepository produitRepository;

    public ProduitService(ProduitRepository produitRepository) {
        this.produitRepository = produitRepository;
    }

    public Produit getProduitByName(String name) {
        return produitRepository.findByName(name)
                .orElseThrow(() -> new ProduitNotFoundException("Produit non trouvé"));
    }
}
```

### 2. Créer la Classe de Test

Nous allons créer une classe de test pour `ProduitService`. Nous n'avons pas besoin de charger le contexte Spring (`@SpringBootTest`), car c'est un test unitaire pur.

```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import java.util.Optional;

import static org.assertj.core.api.Assertions.assertThat;
import static org.mockito.Mockito.when;

// Active l'extension Mockito pour JUnit 5
@ExtendWith(MockitoExtension.class)
class ProduitServiceTest {

    // Crée un mock (une simulation) du ProduitRepository
    @Mock
    private ProduitRepository produitRepository;

    // Injecte les mocks (ici, produitRepository) dans cette instance de ProduitService
    @InjectMocks
    private ProduitService produitService;

    @Test
    void quandGetProduitByNameEstTrouve_alorsRetournerProduit() {
        // 1. Arrange (Préparation)
        Produit produitAttendu = new Produit(1L, "Livre", 25.0);
        
        // On définit le comportement du mock :
        // quand la méthode findByName("Livre") est appelée sur le repository,
        // alors retourner un Optional contenant notre produit.
        when(produitRepository.findByName("Livre")).thenReturn(Optional.of(produitAttendu));

        // 2. Act (Action)
        Produit produitTrouve = produitService.getProduitByName("Livre");

        // 3. Assert (Vérification)
        assertThat(produitTrouve).isNotNull();
        assertThat(produitTrouve.getName()).isEqualTo("Livre");
    }

    @Test
    void quandGetProduitByNameEstNonTrouve_alorsLancerException() {
        // 1. Arrange
        // On définit le comportement du mock :
        // quand findByName est appelé avec n'importe quel string, retourner un Optional vide.
        when(produitRepository.findByName(anyString())).thenReturn(Optional.empty());

        // 2. Act & 3. Assert
        // On vérifie qu'une exception de type ProduitNotFoundException est bien lancée
        assertThrows(ProduitNotFoundException.class, () -> {
            produitService.getProduitByName("un nom inconnu");
        });
    }
}
```

### Explication des Annotations Mockito :
- `@ExtendWith(MockitoExtension.class)` : Intègre Mockito avec le cycle de vie des tests JUnit 5.
- `@Mock` : Crée une version simulée (un "mock") de la classe. Les appels à cet objet ne sont pas réels, mais peuvent être configurés.
- `@InjectMocks` : Crée une instance de la classe à tester et y injecte les dépendances annotées avec `@Mock`.

Cette approche permet de tester la logique du service (`orElseThrow`) de manière complètement isolée de la base de données.
