# 🧱 Atelier PHP : Organisation du Code avec `require` (1h30)

Cet atelier vise à démontrer l'utilité et la nécessité des constructions `require` et `require_once` pour structurer des pages web, centraliser la configuration et améliorer la maintenabilité du code.

-----

## Phase 1 : Le Problème de la Duplication de Code (30 min)

### 🎯 Objectifs

  * Comprendre pourquoi il est inefficace de répéter le même code HTML/PHP sur plusieurs pages.
  * Identifier les parties d'une page web qui sont répétitives (Header, Footer, Navigation).

### 🧑‍💻 Étapes

1.  **Création des Pages Dupliquées (15 min) :**

      * Créez deux fichiers : **`accueil.php`** et **`a_propos.php`**.
      * Dans chaque fichier, copiez-collez le même bloc HTML pour le début de la page (Doctype, `<head>`, Balise de navigation, et titre `<h1>`).

    <!-- end list -->

    ````html
    <!DOCTYPE html>
    <html lang="fr">
    <head>
        <meta charset="UTF-8">
        <title>Page Dupliquée</title>
        <style> nav a { margin-right: 15px; } </style>
    </head>
    <body>
        <header>
            <nav>
                <a href="accueil.php">Accueil</a>
                <a href="a_propos.php">À Propos</a>
            </nav>
        </header>
        <h1>PAGE ACTUELLE</h1> 
        ```

    ````

2.  **Démonstration de la Maintenance (15 min) :**

      * **Problème :** Demandez aux étudiants de changer le texte du lien "À Propos" en "Qui sommes-nous ?"
      * **Observation :** Ils doivent modifier le code dans **deux fichiers différents** (`accueil.php` et `a_propos.php`).
      * **Conclusion :** Expliquer que sur une application de 50 pages, cela devient impossible à gérer.

-----

## Phase 2 : Utilisation de `require` pour l'Inclusion (45 min)

### 💡 Concept Clé : `require` vs `include`

> Les deux fonctions **`require()`** et **`include()`** servent à insérer le contenu d'un fichier dans un autre.
>
>   * **`require()`** : Est utilisé pour les fichiers **essentiels** (comme les fichiers de connexion à la base de données ou les headers). Si le fichier n'est pas trouvé, le script s'arrête immédiatement avec une erreur fatale (`Fatal Error`).
>   * **`include()`** : Est utilisé pour les fichiers **non critiques**. Si le fichier n'est pas trouvé, le script émet juste un avertissement (`Warning`) et continue l'exécution.
>
> **Bonne pratique :** Utilisez **`require`** pour les blocs de structure de votre application.

### 🧑‍💻 Étapes

1.  **Création des Composants (15 min) :**

      * Créez un dossier nommé **`includes`**.
      * Déplacez le code HTML répétitif du début de page dans un nouveau fichier : **`includes/header.php`**.
      * Créez un fichier pour la fin de page : **`includes/footer.php`** (contenant simplement le tag de fermeture `</body>` et un copyright PHP).

2.  **Implémentation de `require` (30 min) :**

      * Dans les fichiers **`accueil.php`** et **`a_propos.php`**, remplacez le code HTML dupliqué par l'instruction `require`.

    **`includes/header.php`** : (contient le début du HTML + la navigation)

    **`accueil.php`** :

    ```php
    <?php require 'includes/header.php'; ?>

    <h1>Bienvenue sur notre page d'accueil !</h1>
    <p>Ceci est le contenu unique de la page d'accueil.</p>

    <?php require 'includes/footer.php'; ?>
    ```

    **Test et Correction :**

      * Effectuez la correction du problème initial : modifiez le lien "À Propos" en "Qui sommes-nous ?" dans **un seul fichier** (`includes/header.php`).
      * Vérifiez que le changement apparaît sur les deux pages instantanément.

-----

## Phase 3 : `require_once` et Centralisation (15 min)

### 💡 Concept Clé : `require_once`

> La fonction **`require_once()`** fonctionne exactement comme `require()`, mais elle vérifie si le fichier a **déjà été inclus** dans le script. Si c'est le cas, elle ignore l'instruction et empêche l'inclusion multiple. Ceci est crucial pour les fichiers de configuration ou de connexion à la base de données.

### 🧑‍💻 Étapes

1.  **Création du Fichier de Configuration (10 min) :**

      * Créez un fichier **`includes/config.php`** pour simuler la configuration d'une application (par exemple, des constantes).

    <!-- end list -->

    ```php
    <?php
    // includes/config.php
    define('TITRE_SITE', 'Mon Site Dynamique');
    define('ANNEE_COURANTE', date('Y'));
    ?>
    ```

2.  **Implémentation de `require_once` (5 min) :**

      * Dans **`includes/header.php`**, utilisez `require_once` pour inclure le fichier de configuration.
      * **Explication :** Si nous incluons `config.php` dans le `header.php` et aussi directement dans `accueil.php`, PHP émettra une erreur de redéfinition de constante. `require_once` empêche cela.

    <!-- end list -->

    ```php
    // Dans includes/header.php, en haut du fichier :
    <?php require_once 'config.php'; ?>

    <title><?php echo TITRE_SITE; ?></title>
    ```

-----

## Conclusion et Synthèse (15 min)

| Fonction | Utilité | Comportement en cas d'erreur | Usage typique |
| :--- | :--- | :--- | :--- |
| **`require()`** | Inclure un fichier essentiel. | Arrêt immédiat du script (Fatal Error). | Header, Footer, classes vitales. |
| **`require_once()`**| Inclure un fichier essentiel **une seule fois**. | Arrêt immédiat si le fichier n'est pas trouvé. | Fichiers de configuration, de connexion PDO. |
| **`include()`** | Inclure un fichier non critique. | Émet un avertissement, mais le script continue. | Widgets optionnels, bannières. |

