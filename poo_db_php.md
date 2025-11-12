

-----

#  Atelier PHP Avancé : PDO, POO et Structuration (3 Heures)

Cet atelier vise à passer d'un code procédural à une approche **Orientée Objet** pour gérer la connexion à la base de données (**PDO**), tout en utilisant `require_once` pour une structuration propre.

-----

## Phase 1 : Structuration du Projet et Classe de Connexion (60 min)

### Objectifs

  * Créer une structure de projet modulaire (`includes`, `classes`).
  * Définir une **classe de connexion** unique (`Database.php`) utilisant PDO.
  * Utiliser `require_once` pour inclure la configuration et la classe.

### Étapes

1.  **Création de la Structure (15 min) :**

      * Créez la structure de dossiers suivante :
        ```
        /mon-projet-poo
        ├── includes/
        │   └── config.php
        ├── classes/
        │   └── Database.php
        ├── index.php
        └── ...
        ```

2.  **Fichier de Configuration (`includes/config.php`) (15 min) :**

      * Réutilisez vos constantes de connexion :

    <!-- end list -->

    ```php
    <?php
    // includes/config.php
    define('DB_HOST', 'localhost');
    define('DB_NAME', 'atelier_db');
    define('DB_USER', 'root');
    define('DB_PASS', '');
    ?>
    ```

3.  **Classe de Connexion PDO (`classes/Database.php`) (30 min) :**

      * Créez une classe avec un **Singleton Pattern** (un seul objet de connexion PDO est créé).

    <!-- end list -->

    ```php
    <?php
    // classes/Database.php
    class Database {
        private static $instance = null;
        private $pdo;

        // Le constructeur est privé pour empêcher l'instanciation directe
        private function __construct() {
            // 1. Inclure la configuration
            require_once dirname(__DIR__) . '/includes/config.php';
            
            $dsn = "mysql:host=" . DB_HOST . ";dbname=" . DB_NAME;
            
            try {
                // 2. Créer l'objet PDO
                $this->pdo = new PDO($dsn, DB_USER, DB_PASS);
                $this->pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
            } catch (PDOException $e) {
                die("Erreur de connexion : " . $e->getMessage());
            }
        }

        // 3. Méthode statique pour obtenir l'instance unique
        public static function getInstance() {
            if (self::$instance == null) {
                self::$instance = new Database();
            }
            return self::$instance;
        }

        // 4. Méthode publique pour obtenir l'objet PDO lui-même
        public function getConnection() {
            return $this->pdo;
        }
    }
    ?>
    ```

-----

## Phase 2 : Création de la Classe Modèle (60 min)

###  Objectifs

  * Définir une classe Modèle (`User.php`) pour les opérations **CRUD** (requêtes).
  * Séparer la logique métier de l'accès aux données.
  * Utiliser l'objet PDO de l'instance `Database`.

###  Étapes

1.  **Création du Fichier de Classe (`classes/User.php`) (15 min) :**

      * Le Modèle *dépend* de la connexion. Il a besoin d'accéder à l'objet `$pdo`.

2.  **Méthode de Récupération (Read) (45 min) :**

      * Ajoutez une méthode statique pour récupérer tous les utilisateurs.

    <!-- end list -->

    ```php
    <?php
    // classes/User.php
    class User {
        private static $pdo;

        public function __construct() {
            // Récupère l'instance PDO via la classe Database Singleton
            self::$pdo = Database::getInstance()->getConnection();
        }

        // Méthode pour récupérer TOUS les utilisateurs
        public static function getAllUsers() {
            $stmt = self::$pdo->prepare("SELECT id, nom, email FROM utilisateurs");
            $stmt->execute();
            // Utilise FETCH_CLASS pour mapper les résultats à une instance de la classe User (Avancé)
            return $stmt->fetchAll(PDO::FETCH_ASSOC); 
        }

        // Méthode pour insérer un utilisateur (Create)
        public static function insertUser($nom, $email) {
            $sql = "INSERT INTO utilisateurs (nom, email) VALUES (:nom, :email)";
            $stmt = self::$pdo->prepare($sql);
            $stmt->bindParam(':nom', $nom);
            $stmt->bindParam(':email', $email);
            return $stmt->execute();
        }
    }
    ?>
    ```

-----

## Phase 3 : Le Contrôleur (Logique d'Application) (60 min)

###  Objectifs

  * Utiliser `require_once` pour inclure les classes.
  * Mettre en œuvre la logique de l'application (`index.php`) en utilisant les méthodes des classes.
  * Afficher le résultat.

###  Étapes

1.  **Inclusion des Classes (`index.php`) (15 min) :**

      * Incluez la classe `Database` (pour la connexion) et la classe `User` (pour les requêtes).

    <!-- end list -->

    ```php
    <?php
    // index.php
    // 1. Inclure la classe de connexion. On utilise require_once pour être sûr.
    require_once 'classes/Database.php'; 

    // 2. Inclure la classe Modèle (qui contient la logique de requête)
    require_once 'classes/User.php'; 

    // 3. Initialiser la connexion et le Modèle (dans un vrai projet, ceci est géré par un Autoloader)
    new Database(); // Assure la création de l'instance Singleton
    new User(); // Assure l'accès au PDO dans la classe User

    // Logique d'insertion (simulée)
    $success = User::insertUser("Charles Dubois", "charles@exemple.com");
    if($success) {
        echo "<p style='color: green;'>✅ Utilisateur inséré.</p>";
    }

    // Récupération des données
    $utilisateurs = User::getAllUsers();

    ?>
    ```

2.  **Affichage des Données (45 min) :**

      * Terminez le script `index.php` par l'affichage des résultats dans un tableau HTML.

    <!-- end list -->

    ```php
    <h2>Liste des Utilisateurs (POO)</h2>
    <table border="1" cellpadding="10" cellspacing="0">
        <tr><th>ID</th><th>Nom</th><th>Email</th></tr>
        <?php foreach ($utilisateurs as $user): ?>
            <tr>
                <td><?php echo htmlspecialchars($user['id']); ?></td>
                <td><?php echo htmlspecialchars($user['nom']); ?></td>
                <td><?php echo htmlspecialchars($user['email']); ?></td>
            </tr>
        <?php endforeach; ?>
    </table>

    <?php
    // 4. Exemple de réutilisation (pour montrer l'avantage du require_once)
    // Le require_once empêche l'erreur de re-déclaration si nous avions défini des fonctions ici.
    require_once 'classes/Database.php'; 
    echo "<p>Fin de l'exécution.</p>";
    ?>
    ```

-----

## Synthèse de l'Approche Orientée Objet

| Concept | Fichier / Élément | Rôle | Avantage de la POO |
| :--- | :--- | :--- | :--- |
| **Structuration** | `require_once` | Assemble les composants du code. | Facilite la maintenance et l'organisation du projet. |
| **Singleton** | `Database.php` | Assure qu'une seule instance PDO existe. | Évite de gaspiller des ressources en ouvrant plusieurs connexions DB. |
| **Modèle** | `User.php` | Contient toutes les requêtes SQL (CRUD). | Sépare la logique de la base de données de la logique d'affichage. |
| **Contrôleur** | `index.php` | Coordonne les actions et affiche les vues. | Maintient le code d'application clair et facile à tester. |