
# TP PHP & MySQL avec PDO : Accès aux Données

Ce TP a pour objectif de vous guider pas à pas dans l'apprentissage de l'**accès** et de la **manipulation** des données stockées dans une base de données MySQL, en utilisant l'extension moderne et sécurisée de PHP : **PDO (PHP Data Objects)**.

### Concept Clé : PDO (PHP Data Objects)

> **PDO** est une couche d'abstraction d'accès aux bases de données pour PHP. En termes simples, c'est une interface standardisée qui vous permet de vous connecter à différents types de bases de données (MySQL, PostgreSQL, SQLite, etc.) en utilisant les mêmes fonctions PHP. Son principal avantage est de faciliter l'utilisation des **requêtes préparées**, essentielles pour prévenir les attaques par **injection SQL**.

-----

## Phase 1 : Configuration et Connexion à la Base de Données

Dans cette phase, vous allez préparer l'environnement et établir le lien vital entre votre script PHP et le serveur MySQL.

### 1.1 Préparation de la Base de Données 

1.  Ouvrez votre gestionnaire de base de données (par exemple, **phpMyAdmin** via XAMPP/WAMP).
2.  Créez une nouvelle base de données nommée **`atelier_db`**.
3.  Dans cette base de données, créez une table nommée **`utilisateurs`** avec la structure suivante :
      * `id` : Type `INT`, cochez `PRIMARY KEY` et `AUTO_INCREMENT`.
      * `nom` : Type `VARCHAR`, Longueur 100.
      * `email` : Type `VARCHAR`, Longueur 100.

### 1.2 Création de la Connexion PDO

Nous allons centraliser les paramètres et le code de connexion pour pouvoir les réutiliser facilement.

#### Fichier 1 : `config.php` (Paramètres)

Créez le fichier **`config.php`** et insérez les informations de connexion à votre base de données locale.

```php
<?php
// Paramètres de connexion MySQL
define('DB_HOST', 'localhost'); // Hôte (souvent localhost)
define('DB_NAME', 'atelier_db'); // Nom de la base de données créée
define('DB_USER', 'root');       // Utilisateur par défaut
define('DB_PASS', '');           // Mot de passe par défaut (souvent vide)
?>
```

#### Fichier 2 : `connexion.php` (Script de test)

Créez le fichier **`connexion.php`** pour établir la connexion.

```php
<?php
require 'config.php';

try {
    // 1. Définition de la chaîne de connexion (DSN)
    // mysql:host=...;dbname=...
    $dsn = "mysql:host=" . DB_HOST . ";dbname=" . DB_NAME;
    
    // 2. Création de l'objet PDO
    $pdo = new PDO($dsn, DB_USER, DB_PASS);
    
    // 3. Configuration importante : Afficher les exceptions/erreurs SQL
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

    echo "Connexion à la base de données établie avec succès !";

} catch (PDOException $e) {
    // Gestion de l'erreur
    die("Erreur de connexion : " . $e->getMessage());
}
?>
```

**Test :** Exécutez `connexion.php` dans votre navigateur. Vous devez voir le message de succès.

-----

## Phase 2 : Insertion de Données (CRUD - Create)

La création de données est l'opération la plus sensible. Nous allons utiliser la méthode sécurisée de **requêtes préparées**.

### Concept Clé : Requêtes Préparées

> Une **requête préparée** est un modèle de requête SQL envoyé au serveur de base de données séparément des données. Les données sont ensuite "injectées" via une liaison de paramètres (**`bindParam`**). Cela garantit que le serveur distingue toujours le code SQL des données utilisateur, **empêchant ainsi l'injection SQL** (la cause la plus fréquente de piratage de base de données).

### 2.1 Script d'Insertion Sécurisée

Créez un fichier **`inscription.php`**. Il simulera le traitement d'un formulaire et l'insertion des données dans la table `utilisateurs`.

```php
<?php
// inscription.php
require 'connexion.php'; // Inclut la connexion PDO et l'objet $pdo

// Données à insérer (simulées ou issues d'un $_POST validé)
$nom_saisi = "Alice Dupont";
$email_saisi = "alice.dupont@exemple.com";

try {
    // 1. Définir la requête avec des marqueurs nommés (:nom_user, :email_user)
    $sql = "INSERT INTO utilisateurs (nom, email) VALUES (:nom_user, :email_user)";

    // 2. Préparer la requête
    $stmt = $pdo->prepare($sql);

    // 3. Lier les valeurs aux marqueurs (on peut ajouter ici htmlspecialchars())
    $stmt->bindParam(':nom_user', $nom_saisi);
    $stmt->bindParam(':email_user', $email_saisi);
    
    // 4. Exécuter la requête
    $stmt->execute();
    
    echo "<p style='color: green;'> Nouvelle entrée créée avec succès !</p>";
    
    // Pour vérifier : affiche l'ID inséré
    echo "<p>ID inséré : " . $pdo->lastInsertId() . "</p>";

} catch (PDOException $e) {
    echo "<p style='color: red;'> Erreur lors de l'insertion : " . $e->getMessage() . "</p>";
}
?>
```

**Test :** Exécutez `inscription.php` plusieurs fois. Vérifiez dans phpMyAdmin que la table `utilisateurs` se remplit avec les données.

-----

## Phase 3 : Récupération et Affichage des Données (CRUD - Read)

Dans cette phase, vous allez lire toutes les données de la table et les afficher de manière structurée.

### Concept Clé : Méthodes de Récupération (Fetch)

> Après l'exécution d'une requête `SELECT`, vous devez **récupérer** les résultats.
>
>   * **`fetch()`** : Récupère la ligne suivante (utile dans une boucle `while`).
>   * **`fetchAll()`** : Récupère toutes les lignes restantes dans un tableau PHP.
>   * **`PDO::FETCH_ASSOC`** : Indique à PDO de retourner un tableau **associatif** (accès par nom de colonne : `$user['nom']`). C'est le format le plus couramment utilisé.

### 3.1 Affichage des Utilisateurs dans un Tableau 

Créez un fichier **`afficher_utilisateurs.php`** pour lire et afficher toutes les entrées.

```php
<?php
// afficher_utilisateurs.php
require 'connexion.php'; // Inclut la connexion PDO

try {
    echo "<h2>Liste des Utilisateurs Enregistrés</h2>";

    // 1. Exécuter la requête SELECT (pas besoin de préparer ici, car pas de données utilisateur)
    $resultat = $pdo->query("SELECT id, nom, email FROM utilisateurs ORDER BY id DESC");

    // 2. Récupérer toutes les lignes dans un tableau associatif
    $utilisateurs = $resultat->fetchAll(PDO::FETCH_ASSOC);
    
    if (count($utilisateurs) > 0) {
        // Début du tableau HTML
        echo "<table border='1' style='width: 80%; border-collapse: collapse;'>";
        echo "<tr><th>ID</th><th>Nom</th><th>Email</th></tr>";
        
        // 3. Parcourir le tableau des résultats (chaque $user est une ligne)
        foreach ($utilisateurs as $user) {
            echo "<tr>";
            // Sécurité : htmlspecialchars() est toujours utilisé pour l'affichage
            echo "<td>" . htmlspecialchars($user['id']) . "</td>";
            echo "<td>" . htmlspecialchars($user['nom']) . "</td>";
            echo "<td>" . htmlspecialchars($user['email']) . "</td>";
            echo "</tr>";
        }
        echo "</table>";
    } else {
        echo "<p>Aucun utilisateur trouvé dans la base de données.</p>";
    }

} catch (PDOException $e) {
    die("Erreur de lecture des données : " . $e->getMessage());
}
?>
```

**Test :** Exécutez `afficher_utilisateurs.php`. Vous devriez voir un tableau listant tous les utilisateurs que vous avez insérés précédemment.

-----

### Exercices de Fin de TP

1.  **Récupération ciblée :** Modifiez le script d'affichage pour récupérer et afficher **un seul** utilisateur spécifique (par exemple, où `id = 1`) en utilisant une requête préparée et la méthode **`fetch()`** au lieu de `fetchAll()`.
2.  **Gestion du formulaire :** Modifiez le formulaire de la leçon précédente (`index.php`) et liez sa soumission au script `inscription.php`. Assurez-vous d'utiliser `$_POST` pour récupérer les données et les insérer dans les requêtes préparées.