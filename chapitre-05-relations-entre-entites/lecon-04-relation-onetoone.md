# Leçon 04 : Relation One-to-One

## 1. Introduction

La relation **One-to-One** est utilisée lorsqu'un enregistrement d'une table est associé à un seul et unique enregistrement d'une autre table. Par exemple, un utilisateur peut avoir un seul profil, et un profil n'appartient qu'à un seul utilisateur.

Il existe deux manières principales de modéliser cette relation :
1.  **Clé étrangère partagée** : La clé primaire d'une table est également la clé étrangère de l'autre.
2.  **Clé étrangère distincte** : Une colonne de clé étrangère est ajoutée à l'une des tables, généralement avec une contrainte d'unicité (`UNIQUE`).

## 2. Implémentation avec JPA

JPA utilise l'annotation `@OneToOne` pour définir cette relation.

### Exemple : Entités `Utilisateur` et `Profil`

Considérons un `Utilisateur` qui a un `Profil`.

**Entité `Utilisateur` (Côté propriétaire) :**

```java
package com.example.cours.entity;

import jakarta.persistence.*;

@Entity
public class Utilisateur {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String nomUtilisateur;

    @OneToOne(cascade = CascadeType.ALL)
    @JoinColumn(name = "profil_id", referencedColumnName = "id")
    private Profil profil;

    // Getters et Setters
}
```

**Annotations clés :**
- `@OneToOne` : Définit la relation. `cascade = CascadeType.ALL` signifie que les opérations (persist, merge, remove, etc.) sur un `Utilisateur` seront propagées à son `Profil`.
- `@JoinColumn` : Spécifie la colonne de clé étrangère (`profil_id`) dans la table `utilisateur`. `referencedColumnName` indique la colonne de la table `profil` à laquelle elle se réfère (ici, la clé primaire `id`).

**Entité `Profil` (Côté inverse) :**

```java
package com.example.cours.entity;

import jakarta.persistence.*;

@Entity
public class Profil {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String biographie;

    @OneToOne(mappedBy = "profil")
    private Utilisateur utilisateur;

    // Getters et Setters
}
```

**Annotation clé :**
- `@OneToOne(mappedBy = "profil")` : Indique que le champ `profil` de l'entité `Utilisateur` est le propriétaire de la relation. Aucune colonne de clé étrangère ne sera créée dans la table `profil`.

## 3. Opérations CRUD

Lorsque vous sauvegardez un `Utilisateur`, son `Profil` associé est également sauvegardé grâce à la cascade.

```java
// Créer un profil
Profil profil = new Profil();
profil.setBiographie("Développeur passionné.");

// Créer un utilisateur
Utilisateur utilisateur = new Utilisateur();
utilisateur.setNomUtilisateur("dev_pro");
utilisateur.setProfil(profil);

// Sauvegarder l'utilisateur (et le profil par cascade)
utilisateurRepository.save(utilisateur);
```

Cette opération insérera une ligne dans la table `profil`, puis une ligne dans la table `utilisateur` avec la colonne `profil_id` pointant vers le profil nouvellement créé.

## 4. Conclusion

La relation `OneToOne` est utile pour séparer des données qui ne sont pas toujours nécessaires ou pour organiser logiquement le modèle de données. Le choix du côté propriétaire de la relation dépend de la logique métier. En général, l'entité qui est créée en premier ou qui a le plus de sens en tant qu'entité "principale" est le propriétaire.
