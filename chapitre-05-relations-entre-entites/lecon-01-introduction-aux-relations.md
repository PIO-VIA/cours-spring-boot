# Chapitre 5 : Relations entre Entités

## Leçon 1 : Introduction aux Relations JPA

Dans une application réelle, les objets de données sont rarement isolés. Un client a plusieurs commandes, un produit appartient à une catégorie, un utilisateur a un profil. JPA permet de modéliser ces relations directement dans notre code à l'aide d'annotations.

### 1. Pourquoi modéliser les relations ?

Modéliser les relations au niveau des entités (plutôt que de manipuler des clés étrangères manuellement) présente plusieurs avantages :
- **Code plus lisible et orienté objet** : Vous pouvez naviguer entre les objets de manière intuitive (ex: `client.getCommandes()`).
- **Gestion de la cohérence** : JPA peut aider à maintenir l'intégrité des données (par exemple, en propageant les suppressions).
- **Simplification des requêtes** : Il devient plus facile de charger un objet et ses objets liés (ce qu'on appelle le "fetching").

### 2. Les Types de Relations (Cardinalités)

Il existe quatre types de relations que l'on peut définir entre deux entités A et B.

#### a) `@OneToOne` (Un-à-Un)
Chaque instance de l'entité A est liée à **une seule** instance de l'entité B, et vice-versa.
*Exemple* : Un `Utilisateur` et son `Profil`. Un utilisateur n'a qu'un seul profil, et un profil n'appartient qu'à un seul utilisateur.

#### b) `@OneToMany` (Un-à-Plusieurs)
Chaque instance de l'entité A peut être liée à **plusieurs** instances de l'entité B.
*Exemple* : Un `Auteur` et ses `Livres`. Un auteur peut écrire plusieurs livres.

#### c) `@ManyToOne` (Plusieurs-à-Un)
Plusieurs instances de l'entité A peuvent être liées à **une seule** instance de l'entité B. C'est la relation inverse de `@OneToMany`.
*Exemple* : Plusieurs `Livres` et leur `Auteur`. Plusieurs livres peuvent être écrits par le même auteur.

**Note** : Une relation `One-to-Many` est presque toujours associée à une relation `Many-to-One` dans l'autre sens.

#### d) `@ManyToMany` (Plusieurs-à-Plusieurs)
Plusieurs instances de l'entité A peuvent être liées à **plusieurs** instances de l'entité B.
*Exemple* : Un `Etudiant` et un `Cours`. Un étudiant peut s'inscrire à plusieurs cours, et un cours peut avoir plusieurs étudiants.
En base de données, ce type de relation nécessite une **table de jonction**.

### 3. Côté "Propriétaire" et Côté "Inverse"

Dans une relation bidirectionnelle (par exemple, entre `Auteur` et `Livre`), JPA a besoin de savoir quel côté est responsable de la gestion de la relation (c'est-à-dire, quelle entité va gérer la mise à jour de la clé étrangère dans la base de données).

- **Côté propriétaire (Owning Side)** : C'est l'entité qui contient la clé étrangère. Dans une relation `One-to-Many` / `Many-to-One` entre `Auteur` et `Livre`, l'entité `Livre` aura une colonne `auteur_id`. C'est donc le côté `Livre` (`@ManyToOne`) qui est le propriétaire.
- **Côté inverse (Inverse Side)** : C'est l'entité qui n'est pas propriétaire. Elle utilise l'attribut `mappedBy` pour déléguer la gestion de la relation au côté propriétaire.

Ce concept est crucial et nous le verrons en détail dans les prochaines leçons.

---

Dans la prochaine leçon, nous allons implémenter concrètement une relation `@ManyToOne` et `@OneToMany` entre deux entités.
