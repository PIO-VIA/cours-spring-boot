# Leçon 4 : Types de Réponses et JSON

Par défaut, lorsque vous utilisez l'annotation `@RestController`, Spring MVC configure automatiquement la sérialisation des objets Java en JSON. Cela est rendu possible grâce à la bibliothèque Jackson, qui est incluse par défaut dans le starter `spring-boot-starter-web`.

## 1. Sérialisation JSON Automatique

Tout objet POJO (Plain Old Java Object) retourné par une méthode d'un `@RestController` sera automatiquement transformé en une chaîne de caractères JSON dans le corps de la réponse HTTP.

### Exemple :

Reprenons notre classe `Produit` :

```java
public class Produit {
    private Long id;
    private String nom;
    private double prix;

    public Produit(Long id, String nom, double prix) {
        this.id = id;
        this.nom = nom;
        this.prix = prix;
    }

    // Getters et Setters
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public String getNom() { return nom; }
    public void setNom(String nom) { this.nom = nom; }
    public double getPrix() { return prix; }
    public void setPrix(double prix) { this.prix = prix; }
}
```

Et un contrôleur qui retourne un objet `Produit` :

```java
@RestController
@RequestMapping("/api/produits")
public class ProduitApiController {

    @GetMapping("/{id}")
    public Produit getProduit(@PathVariable Long id) {
        // Simule la récupération d'un produit depuis une base de données
        return new Produit(id, "Livre Spring Boot", 25.99);
    }

    @GetMapping
    public List<Produit> getAllProduits() {
        // Simule la récupération d'une liste de produits
        return Arrays.asList(
            new Produit(1L, "Livre Spring Boot", 25.99),
            new Produit(2L, "Livre Java", 20.00)
        );
    }
}
```

- Un appel à `GET http://localhost:8080/api/produits/1` retournera la réponse JSON :
  ```json
  {
      "id": 1,
      "nom": "Livre Spring Boot",
      "prix": 25.99
  }
  ```
- Un appel à `GET http://localhost:8080/api/produits` retournera :
  ```json
  [
      {
          "id": 1,
          "nom": "Livre Spring Boot",
          "prix": 25.99
      },
      {
          "id": 2,
          "nom": "Livre Java",
          "prix": 20.00
      }
  ]
  ```

## 2. `ResponseEntity`

Parfois, vous avez besoin de plus de contrôle sur la réponse HTTP. Vous pourriez vouloir spécifier le code de statut (par exemple, `201 Created` ou `404 Not Found`) ou ajouter des en-têtes HTTP spécifiques.

La classe `ResponseEntity` est un wrapper qui vous permet d'encapsuler le corps de la réponse, les en-têtes et le code de statut.

### Exemple :

```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

@RestController
@RequestMapping("/api/v2/produits")
public class ProduitApiV2Controller {

    // Simule une base de données en mémoire
    private final Map<Long, Produit> produits = new ConcurrentHashMap<>();

    @PostMapping
    public ResponseEntity<Produit> creerProduit(@RequestBody Produit produit) {
        // Simule la sauvegarde et l'attribution d'un ID
        Long nouveauId = produits.size() + 1L;
        produit.setId(nouveauId);
        produits.put(nouveauId, produit);
        
        return new ResponseEntity<>(produit, HttpStatus.CREATED); // Status 201
    }

    @GetMapping("/{id}")
    public ResponseEntity<Produit> getProduit(@PathVariable Long id) {
        Produit produit = produits.get(id);
        if (produit != null) {
            return ResponseEntity.ok(produit); // Status 200 OK
        } else {
            return ResponseEntity.notFound().build(); // Status 404 Not Found
        }
    }
}
```

`ResponseEntity` offre des méthodes statiques très pratiques pour construire des réponses communes :
- `ResponseEntity.ok(body)`: Crée une réponse avec le statut 200 OK.
- `ResponseEntity.status(HttpStatus.CREATED).body(body)`: Crée une réponse avec le statut 21 Created.
- `ResponseEntity.notFound().build()`: Crée une réponse avec le statut 404 Not Found et un corps vide.
- `ResponseEntity.badRequest().build()`: Crée une réponse avec le statut 400 Bad Request.

---

Dans le prochain chapitre, nous aborderons la couche de persistance des données avec Spring Data JPA.
