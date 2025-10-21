# Leçon 2 : Relation OneToMany et ManyToOne

La relation la plus fréquente est celle qui lie "un" objet à "plusieurs" autres. Par exemple, un Auteur et ses Livres, ou une Catégorie et ses Produits. C'est une relation bidirectionnelle, implémentée avec `@OneToMany` d'un côté et `@ManyToOne` de l'autre.

Prenons l'exemple d'un `Auteur` qui peut écrire plusieurs `Livre`.

### 1. Côté `@ManyToOne` (le côté propriétaire)

L'entité `Livre` "possède" la relation, car c'est dans la table des livres que l'on trouvera la clé étrangère (`auteur_id`) qui pointe vers l'auteur.

- `@ManyToOne` : Indique que plusieurs livres peuvent être associés à un seul auteur.
- `@JoinColumn(name = "auteur_id")` : Spécifie le nom de la colonne de la clé étrangère dans la table `livre`. C'est cette annotation qui fait de `Livre` le propriétaire de la relation.

#### Entité `Livre` :

```java
import javax.persistence.*;

@Entity
public class Livre {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String titre;

    @ManyToOne(fetch = FetchType.LAZY) // Important pour la performance
    @JoinColumn(name = "auteur_id")
    private Auteur auteur;

    // Constructeurs, Getters, Setters...
    
    public Livre() {}

    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public String getTitre() { return titre; }
    public void setTitre(String titre) { this.titre = titre; }
    public Auteur getAuteur() { return auteur; }
    public void setAuteur(Auteur auteur) { this.auteur = auteur; }
}
```

### 2. Côté `@OneToMany` (le côté inverse)

L'entité `Auteur` est le côté "inverse". Elle ne gère pas la clé étrangère. Elle délègue cette responsabilité à l'entité `Livre` via l'attribut `mappedBy`.

- `@OneToMany(mappedBy = "auteur")` : Indique qu'un auteur peut avoir plusieurs livres.
- `mappedBy = "auteur"` : C'est l'attribut **crucial**. Il dit à JPA : "Ne crée pas de colonne pour cette relation dans la table `auteur`. Pour trouver les livres de cet auteur, va voir dans l'entité `Livre` et regarde le champ nommé `auteur`".

#### Entité `Auteur` :

```java
import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Entity
public class Auteur {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String nom;

    @OneToMany(
        mappedBy = "auteur",
        cascade = CascadeType.ALL, // Propage les opérations (save, delete...)
        orphanRemoval = true
    )
    private List<Livre> livres = new ArrayList<>();

    // Constructeurs, Getters, Setters...

    public Auteur() {}

    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public String getNom() { return nom; }
    public void setNom(String nom) { this.nom = nom; }
    public List<Livre> getLivres() { return livres; }
    public void setLivres(List<Livre> livres) { this.livres = livres; }
}
```

### 3. `CascadeType` et `FetchType`

- **`cascade = CascadeType.ALL`** : Très utile. Si vous sauvegardez un nouvel auteur avec une liste de nouveaux livres, les livres seront aussi sauvegardés automatiquement. Si vous supprimez un auteur, tous ses livres seront également supprimés.

- **`fetch = FetchType.LAZY`** (côté `@ManyToOne`) : C'est une optimisation de performance essentielle. Quand vous chargez un `Livre`, JPA ne chargera pas immédiatement l'objet `Auteur` associé. Il ne le fera que si vous y accédez explicitement (ex: `livre.getAuteur()`). Cela évite de charger des données inutiles.
  - Pour les collections (`@OneToMany`), le `FetchType` par défaut est déjà `LAZY`.
  - Pour les relations uniques (`@ManyToOne`, `@OneToOne`), le défaut est `EAGER`. Il est **fortement recommandé** de toujours mettre `FetchType.LAZY` pour éviter les problèmes de performance (problème N+1).

- **`orphanRemoval = true`** : Si vous retirez un livre de la liste `livres` d'un auteur et que vous sauvegardez l'auteur, le livre sera supprimé de la base de données car il est devenu "orphelin".

---

Dans la prochaine leçon, nous verrons la relation `@ManyToMany`, qui nécessite une table de jonction.
