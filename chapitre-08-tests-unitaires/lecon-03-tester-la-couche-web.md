# Leçon 03 : Tester la Couche Web (Contrôleurs)

Pour tester les contrôleurs (`@RestController`), on veut vérifier que :
- Les endpoints sont correctement mappés (ex: `/api/produits`).
- Les paramètres de requête sont bien gérés.
- Les codes de statut HTTP (200 OK, 404 Not Found, etc.) sont corrects.
- Le corps de la réponse (JSON) est celui attendu.

Pour cela, on utilise un test d'intégration "tranché" (slice test) qui ne charge que la couche web.

### 1. L'Annotation `@WebMvcTest`

`@WebMvcTest` est une annotation qui ne charge que les composants nécessaires pour tester la couche Spring MVC (`@Controller`, `@RestController`, `@ControllerAdvice`, etc.). Elle ne charge ni les services, ni les repositories. Les dépendances du contrôleur doivent donc être mockées.

### 2. Le Scénario

Imaginons un `ProduitController` qui dépend de notre `ProduitService`.

```java
@RestController
@RequestMapping("/api/produits")
public class ProduitController {

    private final ProduitService produitService;

    public ProduitController(ProduitService produitService) {
        this.produitService = produitService;
    }

    @GetMapping("/{name}")
    public ResponseEntity<Produit> getProduitByName(@PathVariable String name) {
        Produit produit = produitService.getProduitByName(name);
        return ResponseEntity.ok(produit);
    }
}
```

### 3. Créer la Classe de Test

Nous allons utiliser `@WebMvcTest` et injecter un `MockMvc`. `MockMvc` nous permet de simuler des requêtes HTTP.

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.web.servlet.MockMvc;

import static org.mockito.BDDMockito.given;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

// On spécifie le contrôleur à tester pour ne charger que lui
@WebMvcTest(ProduitController.class)
class ProduitControllerTest {

    // MockMvc est auto-configuré par @WebMvcTest et permet de lancer des requêtes
    @Autowired
    private MockMvc mockMvc;

    // @MockBean crée un mock du service et l'ajoute au contexte Spring du test
    // Le vrai bean service n'est pas utilisé
    @MockBean
    private ProduitService produitService;

    @Test
    void quandGetProduit_alorsRetournerJsonEtStatut200() throws Exception {
        // Arrange
        Produit produit = new Produit(1L, "Livre", 25.0);
        
        // On configure le comportement du service mocké
        // BDDMockito.given() est une alternative à Mockito.when()
        given(produitService.getProduitByName("Livre")).willReturn(produit);

        // Act & Assert
        mockMvc.perform(get("/api/produits/{name}", "Livre")) // Simule un GET
                .andExpect(status().isOk()) // Vérifie que le statut HTTP est 200 OK
                .andExpect(jsonPath("$.name").value("Livre")) // Vérifie le contenu du JSON de la réponse
                .andExpect(jsonPath("$.price").value(25.0));
    }

    @Test
    void quandGetProduitInexistant_alorsRetournerStatut404() throws Exception {
        // Arrange
        given(produitService.getProduitByName("Inexistant"))
            .willThrow(new ProduitNotFoundException("Produit non trouvé"));

        // Act & Assert
        mockMvc.perform(get("/api/produits/{name}", "Inexistant"))
                .andExpect(status().isNotFound()); // Vérifie que le statut HTTP est 404 Not Found
    }
}
```

### Explication :
- `@WebMvcTest(ProduitController.class)` : On cible le test sur `ProduitController`. Spring Boot ne configure que cette partie de l'application.
- `@MockBean` : C'est une annotation de Spring Boot qui remplace le bean `ProduitService` réel dans le contexte par un mock Mockito. C'est la clé pour isoler le contrôleur.
- `mockMvc.perform(...)` : Exécute une requête (ici, un `GET`).
- `.andExpect(...)` : Permet de faire des assertions sur la réponse (statut, contenu JSON, headers, etc.).
- `jsonPath("$.name")` : Utilise la syntaxe JsonPath pour inspecter le corps de la réponse JSON. `$` représente la racine du document.

Cette méthode est très efficace pour valider toute la couche web sans dépendre de la logique métier ou de la base de données.
