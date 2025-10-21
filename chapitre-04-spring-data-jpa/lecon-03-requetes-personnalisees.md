# Leçon 3 : Requêtes Personnalisées

Spring Data JPA est extrêmement puissant car il ne se limite pas aux méthodes CRUD de base. Il existe plusieurs façons de créer des requêtes plus complexes pour rechercher des données selon des critères spécifiques.

## 1. Méthodes de Requête (Query Methods)

La manière la plus simple de créer une requête est de déclarer une nouvelle méthode dans votre interface de repository. Spring Data JPA va automatiquement "comprendre" le nom de la méthode et générer la requête correspondante. C'est ce qu'on appelle les "Derived Queries".

La convention est de commencer par `findBy`, `readBy`, `queryBy`, `countBy`, ou `getBy`, suivi des noms des attributs de votre entité.

### Exemples dans `ProduitRepository` :

```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;
import java.util.List;

@Repository
public interface ProduitRepository extends JpaRepository<Produit, Long> {

    // Trouve une liste de produits par leur nom exact
    List<Produit> findByNom(String nom);

    // Trouve une liste de produits dont le prix est supérieur à une certaine valeur
    List<Produit> findByPrixGreaterThan(double prix);

    // Trouve une liste de produits dont le nom contient une chaîne de caractères, en ignorant la casse
    List<Produit> findByNomContainingIgnoreCase(String chaine);

    // Compte le nombre de produits pour un certain prix
    long countByPrix(double prix);
}
```

Vous n'avez rien d'autre à faire ! Vous pouvez maintenant injecter `ProduitRepository` et appeler ces méthodes directement.

```java
// Exemple d'utilisation
List<Produit> produitsChers = produitRepository.findByPrixGreaterThan(100.0);
```

## 2. Annotation `@Query` avec JPQL

Pour des requêtes plus complexes qui ne peuvent pas être exprimées par un nom de méthode, vous pouvez utiliser l'annotation `@Query` pour fournir votre propre requête en **JPQL (Java Persistence Query Language)**.

JPQL ressemble beaucoup à SQL, mais il opère sur les entités JPA et leurs attributs, pas sur les tables et les colonnes de la base de données.

### Exemples dans `ProduitRepository` :

```java
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;

// ... dans l'interface ProduitRepository

// Une requête pour trouver des produits dans une fourchette de prix
@Query("SELECT p FROM Produit p WHERE p.prix BETWEEN :prixMin AND :prixMax")
List<Produit> rechercherParFourchetteDePrix(@Param("prixMin") double min, @Param("prixMax") double max);

// Une requête qui trie les résultats
@Query("SELECT p FROM Produit p WHERE p.nom LIKE %:chaine% ORDER BY p.prix DESC")
List<Produit> rechercherEtTrierParNom(@Param("chaine") String chaine);
```

- `SELECT p FROM Produit p`: `Produit` est le nom de l'**entité**, pas de la table. `p` est un alias.
- `:prixMin`, `:prixMax`, `:chaine`: Ce sont des paramètres nommés.
- `@Param("prixMin")`: Cette annotation lie le paramètre de la méthode (`min`) au paramètre nommé dans la requête (`:prixMin`).

## 3. `@Query` avec SQL Natif (Optionnel)

Dans de rares cas, vous pourriez avoir besoin d'utiliser des fonctionnalités spécifiques à votre base de données (par exemple, des fonctions PostgreSQL spécifiques). Vous pouvez écrire une requête SQL native en ajoutant l'attribut `nativeQuery = true`.

```java
// Exemple (syntaxe peut varier selon la BDD)
@Query(value = "SELECT * FROM produit WHERE nom ILIKE %:chaine%", nativeQuery = true)
List<Produit> rechercherAvecSqlNatif(@Param("chaine") String chaine);
```
**Attention** : Utiliser des requêtes natives rend votre code moins portable d'une base de données à une autre. À utiliser avec parcimonie.

---

Maintenant que nous savons lire et écrire des données, le prochain chapitre portera sur les relations entre les entités (One-to-One, One-to-Many, etc.).
