# Leçon 03 : Gestion des Logs

La gestion des logs est un aspect crucial de toute application en production. Elle permet de suivre le comportement de l'application, de diagnostiquer les problèmes et d'auditer les événements importants. Spring Boot offre une configuration par défaut pour les logs qui est à la fois simple et puissante.

## 1. Comprendre le Logging dans Spring Boot

Par défaut, Spring Boot utilise **Logback** avec **SLF4J** (Simple Logging Facade for Java) comme API de logging. Cette configuration offre une excellente flexibilité et des performances solides.

Les niveaux de log standards sont (du plus au moins verbeux) :
- `TRACE`
- `DEBUG`
- `INFO`
- `WARN`
- `ERROR`

Le niveau par défaut est `INFO`, ce qui signifie que les messages `TRACE` et `DEBUG` ne sont pas affichés.

## 2. Configurer les Niveaux de Log

Vous pouvez facilement changer les niveaux de log dans votre fichier `application.properties` (ou `application.yml`).

Par exemple, pour passer le niveau de log général en `DEBUG` :

```properties
logging.level.root=DEBUG
```

Il est souvent plus utile de spécifier le niveau de log pour des packages spécifiques. Par exemple, pour n'activer le mode `DEBUG` que pour votre application :

```properties
# application.properties

# Met le niveau de log à DEBUG pour toutes les classes dans le package com.exemple.cours
logging.level.com.exemple.cours=DEBUG

# Garde le niveau de log de Spring Framework à INFO pour éviter le bruit
logging.level.org.springframework=INFO
```

## 3. Écrire des Logs dans le Code

Pour ajouter des logs dans vos classes, il suffit de récupérer une instance du `Logger`.

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    private static final Logger logger = LoggerFactory.getLogger(HelloController.class);

    @GetMapping("/")
    public String hello() {
        logger.info("La méthode hello() a été appelée.");
        
        try {
            // Simuler un traitement
            logger.debug("Début du traitement métier...");
            // ...
            logger.debug("Fin du traitement métier.");
        } catch (Exception e) {
            logger.error("Une erreur est survenue !", e);
        }
        
        return "Hello, World!";
    }
}
```

## 4. Configurer les Appenders

Par défaut, Spring Boot affiche les logs uniquement dans la console. Pour les écrire dans un fichier, vous pouvez spécifier un nom de fichier ou un chemin dans `application.properties`.

```properties
# Écrit les logs dans un fichier nommé 'application.log' dans le répertoire courant
logging.file.name=application.log

# Ou spécifie un chemin complet
# logging.file.path=/var/log/mon-app/
```

Spring Boot gérera automatiquement la rotation des fichiers logs lorsqu'ils atteignent 10 Mo.

---

La gestion des logs est un outil indispensable. Prenez l'habitude de logger les informations pertinentes aux bons niveaux pour faciliter la maintenance de votre application.
