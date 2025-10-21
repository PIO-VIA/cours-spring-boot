# Leçon 3 : Gestion des Paramètres de Requête

Dans cette leçon, nous allons explorer comment récupérer des données envoyées par le client dans une requête HTTP. Spring MVC offre plusieurs annotations puissantes pour lier les paramètres d'une requête aux arguments des méthodes de votre contrôleur.

## 1. `@RequestParam`

L'annotation `@RequestParam` est utilisée pour extraire les paramètres de la query string (par exemple, `?name=John`).

### Exemple :

```java
@RestController
public class SalutationController {

    @GetMapping("/salutation")
    public String saluer(@RequestParam String nom) {
        return "Bonjour, " + nom + " !";
    }
}
```

Pour tester, vous pouvez appeler l'URL : `http://localhost:8080/salutation?nom=Monde`. Le corps de la réponse sera `Bonjour, Monde !`.

### Paramètres optionnels

Par défaut, `@RequestParam` est requis. Si le paramètre n'est pas fourni, une erreur sera levée. Pour le rendre optionnel, vous pouvez utiliser l'attribut `required` et `defaultValue`.

```java
@GetMapping("/salutation-optionnelle")
public String saluerOptionnel(@RequestParam(required = false, defaultValue = "Visiteur") String nom) {
    return "Bonjour, " + nom + " !";
}
```
- `http://localhost:8080/salutation-optionnelle?nom=Alice` -> `Bonjour, Alice !`
- `http://localhost:8080/salutation-optionnelle` -> `Bonjour, Visiteur !`

## 2. `@PathVariable`

L'annotation `@PathVariable` est utilisée pour extraire des valeurs depuis l'URI lui-même (par exemple, `/utilisateurs/123`).

### Exemple :

```java
@RestController
@RequestMapping("/utilisateurs")
public class UtilisateurController {

    @GetMapping("/{id}")
    public String trouverUtilisateurParId(@PathVariable Long id) {
        // Logique pour récupérer l'utilisateur avec l'ID 'id'
        return "Profil de l'utilisateur avec l'ID : " + id;
    }
}
```

Pour tester, appelez l'URL : `http://localhost:8080/utilisateurs/42`. La réponse sera `Profil de l'utilisateur avec l'ID : 42`.

## 3. `@RequestBody`

Lorsque le client envoie des données complexes (comme du JSON ou du XML) dans le corps de la requête (souvent avec les méthodes POST ou PUT), vous pouvez utiliser `@RequestBody` pour désérialiser automatiquement ces données dans un objet Java (un POJO).

### Exemple :

Supposons que vous ayez une classe `Produit` :

```java
public class Produit {
    private String nom;
    private double prix;

    // Getters et Setters
    public String getNom() { return nom; }
    public void setNom(String nom) { this.nom = nom; }
    public double getPrix() { return prix; }
    public void setPrix(double prix) { this.prix = prix; }
}
```

Votre contrôleur peut alors accepter un objet `Produit` directement :

```java
@RestController
@RequestMapping("/produits")
public class ProduitController {

    @PostMapping
    public String creerProduit(@RequestBody Produit produit) {
        // Logique pour sauvegarder le produit
        return "Produit '" + produit.getNom() + "' créé avec succès !";
    }
}
```

Pour tester, vous pouvez utiliser un client comme `curl` ou Postman pour envoyer une requête POST à `http://localhost:8080/produits` avec le corps JSON suivant :

```json
{
    "nom": "Ordinateur Portable",
    "prix": 1200.50
}
```

La réponse sera `Produit 'Ordinateur Portable' créé avec succès !`.

---

Dans la prochaine leçon, nous aborderons les différents types de réponses que l'on peut retourner avec Spring MVC, notamment comment renvoyer du JSON.
