#  TP PHP : Authentification Sécurisée et Sessions

## Durée Estimée : 3 Heures

Cet atelier vous montre comment mettre en place un système d'authentification robuste.
Il vous guide dans la construction d'un mécanisme de connexion sécurisé en utilisant les **Sessions PHP** pour maintenir l'état de l'utilisateur et les fonctions de **hachage de mot de passe** (PDO sera réutilisé pour l'accès à la base de données).

###  Concept Clé : Les Sessions PHP

> Le protocole HTTP est "sans état" (stateless) : le serveur oublie tout de l'utilisateur entre deux requêtes. Une **Session PHP** est un mécanisme qui permet de maintenir des informations spécifiques à un utilisateur (son "état") sur plusieurs pages. L'information est stockée côté serveur, et le client (navigateur) ne garde qu'un petit identifiant (Session ID) sous forme de cookie pour que le serveur sache qui il est.

-----

## Phase 1 : Préparation de la Base de Données et Sécurité des Mots de Passe (45 min)

### 1.1 Préparation de la Table des Utilisateurs

Nous allons adapter la table `utilisateurs` pour stocker les informations nécessaires à l'authentification.

1.  Ouvrez votre gestionnaire de base de données (phpMyAdmin).
2.  Si elle existe, modifiez la table **`utilisateurs`** dans la base de données **`atelier_db`** (sinon, créez-la) avec la structure suivante :
      * `id` : INT, PRIMARY KEY, AUTO\_INCREMENT
      * `username` : VARCHAR(50), **UNIQUE** (pour que l'identifiant soit unique)
      * `password_hash` : VARCHAR(255)

### 1.2 Insertion d'un Utilisateur de Test Haché

Vous ne devez **JAMAIS** stocker des mots de passe en clair dans la base de données. PHP propose des fonctions spécifiques pour cela.

1.  Créez un fichier temporaire nommé **`creer_hachage.php`**.

<!-- end list -->

```php
<?php
// creer_hachage.php
$mot_de_passe_clair = "MotDePasse123";

// Utilisation de l'algorithme BCRYPT par défaut, sécurisé et salé automatiquement
$hachage = password_hash($mot_de_passe_clair, PASSWORD_DEFAULT);

echo "Mot de passe clair : " . $mot_de_passe_clair . "<br>";
echo "Hachage sécurisé à insérer en DB : " . $hachage;
?>
```

2.  Exécutez `creer_hachage.php`, copiez le hachage généré (une longue chaîne de caractères).
3.  Insérez un utilisateur de test directement via phpMyAdmin :
      * `username` : `testuser`
      * `password_hash` : **Collez le hachage copié**.

###  Concept Clé : `password_hash()` et `password_verify()`

> **`password_hash()`** : Prend un mot de passe en clair et retourne un hachage sécurisé, unique et salé.
> **`password_verify()`** : Prend un mot de passe en clair soumis par l'utilisateur et le compare avec un hachage stocké. Elle est la seule méthode sécurisée pour vérifier un mot de passe haché par `password_hash()`.

-----

## Phase 2 : Le Formulaire de Connexion et la Vérification (60 min)

### 2.1 Le Formulaire de Connexion

Créez le fichier **`login.php`**.

```php
<?php
// login.php
// Nous allons ajouter session_start() et la logique de connexion ici.
?>
<!DOCTYPE html>
<html lang="fr">
<head><title>Connexion Sécurisée</title></head>
<body>
    <h1>Connexion</h1>
    <form action="login.php" method="POST">
        <label for="username">Nom d'utilisateur :</label>
        <input type="text" id="username" name="username" required><br><br>

        <label for="password">Mot de passe :</label>
        <input type="password" id="password" name="password" required><br><br>

        <input type="submit" value="Se connecter">
    </form>
</body>
</html>
```

### 2.2 La Logique d'Authentification

Intégrez la logique PHP en haut du fichier `login.php`. Vous réutiliserez votre fichier `connexion.php` (Phase 1 du TP PDO).

```php
<?php
// login.php - Début du fichier
session_start(); // **TRÈS IMPORTANT** : Démarre la session

require 'connexion.php'; // Inclut l'objet $pdo

// 1. Vérification de la soumission du formulaire
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $username = $_POST['username'] ?? '';
    $password = $_POST['password'] ?? '';
    $message = '';

    // 2. Récupération du hachage dans la base de données (Requête Préparée PDO)
    $sql = "SELECT username, password_hash FROM utilisateurs WHERE username = :username";
    $stmt = $pdo->prepare($sql);
    $stmt->bindParam(':username', $username);
    $stmt->execute();
    
    $user = $stmt->fetch(PDO::FETCH_ASSOC);

    if ($user) {
        // 3. Vérification du mot de passe clair avec le hachage stocké
        if (password_verify($password, $user['password_hash'])) {
            
            // --- Authentification réussie (Phase 3) ---
            $_SESSION['logged_in'] = true;
            $_SESSION['username'] = $user['username'];
            
            // Redirection vers la page protégée
            header('Location: dashboard.php');
            exit;
            // ------------------------------------------

        } else {
            $message = "Nom d'utilisateur ou mot de passe incorrect.";
        }
    } else {
        $message = "Nom d'utilisateur ou mot de passe incorrect.";
    }
}
// Afficher $message dans le HTML pour l'utilisateur
?>
```

-----

## Phase 3 : Gestion de Session et Protection des Pages (60 min)

### 3.1 Démarrer la Session

La fonction **`session_start()`** doit être la **toute première instruction PHP** exécutée sur chaque page où vous souhaitez utiliser ou vérifier la session. Vous l'avez déjà ajoutée en haut de `login.php`.

### 3.2 Création de la Page Protégée

Créez le fichier **`dashboard.php`** (tableau de bord). C'est la page que seuls les utilisateurs connectés peuvent voir.

```php
<?php
// dashboard.php
session_start(); // Démarrer la session

// 1. Contrôle d'accès : Vérifier si l'utilisateur est connecté
if (!isset($_SESSION['logged_in']) || $_SESSION['logged_in'] !== true) {
    // Si l'utilisateur n'est PAS connecté, on le redirige immédiatement.
    header('Location: login.php');
    exit;
}

// L'utilisateur est connecté, le code de la page peut s'exécuter
$username = $_SESSION['username'];
?>
<!DOCTYPE html>
<html lang="fr">
<head><title>Tableau de Bord Protégé</title></head>
<body>
    <h1>Bienvenue, <?php echo htmlspecialchars($username); ?> !</h1>
    <p>Ceci est une zone protégée, seuls les membres peuvent y accéder.</p>
    <p><a href="logout.php">Se déconnecter</a></p>
</body>
</html>
```

**Test :**

1.  Essayez d'accéder directement à `dashboard.php`. Vous devriez être redirigé vers `login.php`.
2.  Connectez-vous avec `testuser` et `MotDePasse123`. Vous devriez accéder au tableau de bord.

-----

## Phase 4 : Déconnexion et Synthèse (30 min)

### 4.1 Script de Déconnexion

Pour déconnecter l'utilisateur, il faut détruire les données de session. Créez le fichier **`logout.php`**.

```php
<?php
// logout.php
session_start(); // **NÉCESSAIRE** pour accéder à la session existante

// 1. Supprimer toutes les variables de session
$_SESSION = array(); // Bonne pratique

// 2. Détruire la session
session_destroy();

// Redirection vers la page de connexion
header('Location: login.php');
exit;
?>
```

**Test :**

1.  Connectez-vous.
2.  Cliquez sur le lien "Se déconnecter" dans `dashboard.php`.
3.  Vérifiez que vous êtes redirigé et que l'accès à `dashboard.php` est à nouveau bloqué.

### 4.2 Synthèse de l'Atelier

| Action | Fonction PHP / Technique | Objectif de Sécurité |
| :--- | :--- | :--- |
| **Stockage MP** | `password_hash()` | Prévenir la lecture du MP si la DB est compromise. |
| **Vérification MP** | `password_verify()` | Vérifier le MP sans le déchiffrer. |
| **Démarrer session**| `session_start()` | Permettre au serveur de suivre l'utilisateur. |
| **Identification** | `$_SESSION['logged_in'] = true;` | Maintenir l'état de connexion de l'utilisateur. |
| **Contrôle d'accès** | `if (!isset($_SESSION['...'])) { redirect }` | Protéger les pages sensibles (autorisation). |
| **Déconnexion** | `session_destroy()` | Mettre fin à la session de l'utilisateur. |