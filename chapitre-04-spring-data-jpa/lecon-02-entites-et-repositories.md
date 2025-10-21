# Leçon 2 : Entités et Repositories

Maintenant que la configuration de base est prête, nous pouvons créer notre premier modèle de données persistant. En JPA, une classe qui est mappée à une table de base de données est appelée une **Entité**.

## 1. Création d'une Entité JPA

Une entité est une simple classe Java (POJO) annotée avec `@Entity`. Chaque instance de cette classe correspond à une ligne dans une table.

- `@Entity` : Indique que la classe est une entité JPA. Par défaut, le nom de la table sera le même que celui de la classe.
- `@Id` : Spécifie la clé primaire de l'entité.
- `@GeneratedValue` : Configure la manière dont la clé primaire est générée (par exemple, auto-incrémentée).
- `@Column` : Permet de personnaliser le mappage entre un attribut et une colonne (nom, longueur, etc.). C'est souvent optionnel.

### Exemple : Entité `Produit`

Créons une entité pour représenter un produit.

```java
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class Produit {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String nom;

    private double prix;

    // Constructeur par défaut requis par JPA
    public Produit() {}

    // Autres constructeurs, getters, setters...
    public Produit(String nom, double prix) {
        this.nom = nom;
        this.prix = prix;
    }

    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public String getNom() { return nom; }
    public void setNom(String nom) { this.nom = nom; }
    public double getPrix() { return prix; }
    public void setPrix(double prix) { this.prix = prix; }
}
```
**Note** : `GenerationType.IDENTITY` est une stratégie courante qui s'appuie sur une colonne auto-incrémentée de la base de données.

## 2. Création d'un Repository

Un Repository est une interface qui fournit les méthodes pour effectuer les opérations CRUD (Create, Read, Update, Delete) sur une entité. Spring Data JPA va générer automatiquement l'implémentation de cette interface à notre place.

Pour créer un repository, il suffit de créer une interface qui étend `JpaRepository`.

### `JpaRepository<T, ID>`

`JpaRepository` est une interface générique qui prend deux paramètres :
1.  `T` : Le type de l'entité à gérer (par exemple, `Produit`).
2.  `ID` : Le type de la clé primaire de l'entité (par exemple, `Long`).

### Exemple : `ProduitRepository`

```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface ProduitRepository extends JpaRepository<Produit, Long> {
    // Pas besoin d'écrire de code ici !
}
```

C'est tout ! En étendant `JpaRepository`, notre `ProduitRepository` hérite instantanément d'un ensemble complet de méthodes pour manipuler les produits :

- `save(Produit produit)` : Sauvegarde ou met à jour un produit.
- `findById(Long id)` : Trouve un produit par son ID. Retourne un `Optional<Produit>`.
- `findAll()` : Retourne la liste de tous les produits.
- `deleteById(Long id)` : Supprime un produit par son ID.
- `count()` : Compte le nombre total de produits.
- Et bien d'autres...

L'annotation `@Repository` indique à Spring que cette interface est un bean de repository, bien qu'elle soit souvent optionnelle si l'interface étend `JpaRepository`.

## 3. Utilisation du Repository

Vous pouvez maintenant injecter `ProduitRepository` dans n'importe quel autre bean Spring (comme un `Service` ou un `RestController`) et utiliser ses méthodes.

```java
@RestController
@RequestMapping("/produits")
public class ProduitController {

    @Autowired
    private ProduitRepository produitRepository;

    @PostMapping
    public Produit creerProduit(@RequestBody Produit produit) {
        return produitRepository.save(produit);
    }

    @GetMapping
    public List<Produit> listerProduits() {
        return produitRepository.findAll();
    }
}
```

---

Dans la prochaine leçon, nous verrons comment créer des requêtes personnalisées avec Spring Data JPA pour aller au-delà des méthodes CRUD de base.
