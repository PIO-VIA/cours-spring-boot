# Leçon 03 : Relation Many-to-Many

## 1. Introduction

La relation **Many-to-Many** est utilisée lorsque plusieurs enregistrements d'une table peuvent être associés à plusieurs enregistrements d'une autre table. Par exemple, un étudiant peut s'inscrire à plusieurs cours, et un cours peut avoir plusieurs étudiants.

Pour modéliser cela en SQL, on utilise une table de jonction (join table) qui contient les clés étrangères des deux tables.

## 2. Implémentation avec JPA

Avec Spring Data JPA, la relation `ManyToMany` est gérée à l'aide de l'annotation `@ManyToMany`. C'est généralement la partie "possédante" de la relation qui est responsable de la table de jonction.

### Exemple : Entités `Etudiant` et `Cours`

Imaginons que nous ayons des étudiants et des cours.

**Entité `Etudiant` (Côté possédant) :**

```java
package com.example.cours.entity;

import jakarta.persistence.*;
import java.util.Set;

@Entity
public class Etudiant {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String nom;

    @ManyToMany(cascade = { CascadeType.PERSIST, CascadeType.MERGE })
    @JoinTable(
        name = "etudiant_cours",
        joinColumns = @JoinColumn(name = "etudiant_id"),
        inverseJoinColumns = @JoinColumn(name = "cours_id")
    )
    private Set<Cours> coursInscrits;

    // Getters et Setters
}
```

**Annotations clés :**
- `@ManyToMany` : Définit la relation.
- `@JoinTable` : Spécifie les détails de la table de jonction.
  - `name` : Le nom de la table de jonction (`etudiant_cours`).
  - `joinColumns` : La colonne qui représente la clé étrangère de l'entité propriétaire (`Etudiant`).
  - `inverseJoinColumns` : La colonne qui représente la clé étrangère de l'autre côté (`Cours`).

**Entité `Cours` (Côté inverse) :**

```java
package com.example.cours.entity;

import jakarta.persistence.*;
import java.util.Set;

@Entity
public class Cours {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String titre;

    @ManyToMany(mappedBy = "coursInscrits")
    private Set<Etudiant> etudiants;

    // Getters et Setters
}
```

**Annotation clé :**
- `@ManyToMany(mappedBy = "coursInscrits")` : Indique que cette partie de la relation est la "partie inverse". La gestion de la relation (la mise à jour de la table de jonction) est effectuée par le champ `coursInscrits` de l'entité `Etudiant`.

## 3. Opérations CRUD

Lorsque vous sauvegardez un étudiant avec une liste de cours, JPA insère automatiquement les enregistrements correspondants dans la table de jonction.

```java
// Créer un cours
Cours math = new Cours();
math.setTitre("Mathématiques");

// Créer un autre cours
Cours physique = new Cours();
physique.setTitre("Physique");

// Créer un étudiant
Etudiant jean = new Etudiant();
jean.setNom("Jean");

// Inscrire l'étudiant aux cours
jean.setCoursInscrits(Set.of(math, physique));

// Sauvegarder l'étudiant (et les cours grâce à la cascade)
etudiantRepository.save(jean);
```

Cette opération créera des entrées dans les tables `etudiant`, `cours` et `etudiant_cours`.

## 4. Conclusion

La relation `ManyToMany` est puissante mais peut introduire de la complexité, notamment concernant la gestion des cascades et la récupération des données. L'utilisation de `Set` est recommandée pour éviter les doublons dans les collections.
