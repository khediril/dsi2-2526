C'est une excellente idée d'enrichir l'atelier avec ces types de champs. Je vais ajouter une nouvelle phase complète à l'atelier, juste avant la validation, pour couvrir la **récupération des données complexes**. Je vais également mettre à jour la Phase 1 pour inclure ces champs dans le HTML.

Voici la version mise à jour de l'atelier en Markdown, en conservant l'enchaînement initial et en insérant la nouvelle partie.

-----

# 🛠️ Atelier PHP : Maîtriser les Formulaires Avancés (3 Heures)

Cet atelier vise à comprendre le fonctionnement des formulaires HTML et leur traitement côté serveur avec PHP, en se concentrant sur les méthodes **GET** et **POST**, la **récupération des données** (simples et complexes) et une **validation de base**.

-----

## Phase 1 : Introduction et Formulaire HTML Avancé (45 min)

### 🎯 Objectifs

  * Comprendre le rôle d'un formulaire dans une application web.
  * Créer un formulaire HTML avec tous les types de champs requis.
  * **Point clé :** Utiliser les crochets `[]` dans l'attribut `name` des champs à sélection multiple.

### 🧑‍💻 Étapes

1.  **Rappel (10 min) :** Discuter des rôles du client (HTML/CSS) et du serveur (PHP). Expliquer l'importance des attributs **`action`** et **`method`**.
2.  **Création du fichier (35 min) :**
      * Créez un fichier nommé **`index.php`**.
      * Ajoutez la structure HTML de base, y compris tous les champs.

<!-- end list -->

```html
<form action="traitement.php" method="post">
    <h2>Informations de base</h2>
    <label for="nom">Nom :</label>
    <input type="text" id="nom" name="nom" required><br><br>

    <label for="email">Email :</label>
    <input type="email" id="email" name="email" required><br><br>

    <p>Civilité (Radio) :</p>
    <input type="radio" id="civilite_m" name="civilite" value="M." required>
    <label for="civilite_m">Monsieur</label>
    <input type="radio" id="civilite_mme" name="civilite" value="Mme">
    <label for="civilite_mme">Madame</label><br><br>

    <h2>Vos préférences</h2>

    <p>Technologies (Checkbox - Choix multiples) :</p>
    <input type="checkbox" id="tech_php" name="techs[]" value="PHP">
    <label for="tech_php">PHP</label>
    <input type="checkbox" id="tech_js" name="techs[]" value="JavaScript">
    <label for="tech_js">JavaScript</label><br><br>

    <label for="pays">Pays (Select unique) :</label>
    <select id="pays" name="pays">
        <option value="FR">France</option>
        <option value="CA">Canada</option>
    </select><br><br>

    <label for="couleurs">Couleurs (Select multiple - Ctrl/Cmd + Clic) :</label>
    <select id="couleurs" name="couleurs[]" multiple size="3">
        <option value="bleu">Bleu</option>
        <option value="vert">Vert</option>
        <option value="rouge">Rouge</option>
    </select><br><br>

    <label for="commentaire">Commentaire (Textarea) :</label><br>
    <textarea id="commentaire" name="commentaire" rows="3" cols="40"></textarea><br><br>

    <input type="submit" value="Envoyer">
</form>
```

-----

## Phase 2 : Récupération des Données Simples (45 min)

### 🎯 Objectifs

  * Utiliser les tableaux superglobaux **`$_POST`** et **`$_GET`** pour récupérer les données de base (texte, email).
  * Afficher les données de base reçues sur la page de traitement.

### 🧑‍💻 Étapes

1.  **Création du script de traitement (15 min) :**
      * Créez un fichier nommé **`traitement.php`**.
      * Utilisez **`$_POST`** pour récupérer les valeurs des champs **Nom** et **Email**.

<!-- end list -->

```php
<?php
// Phase 2 : Récupération des données simples
$nom = $_POST['nom'] ?? 'Inconnu';
$email = $_POST['email'] ?? 'Non spécifié';

echo "<h1>Résultat du Formulaire</h1>";
echo "<h2>Données de base :</h2>";
echo "<p>Nom : $nom</p>";
echo "<p>Email : $email</p>";

// ... Le code des Phases 3 et 4 viendra ici ...

?>
```

2.  **Test et Observation (30 min) :**
      * Tester le formulaire.
      * **Exercice GET vs POST :** Changer la méthode du formulaire dans `index.php` de **`post`** à **`get`** et observer l'URL. Modifier le code PHP dans `traitement.php` pour utiliser **`$_GET`**. Discuter des différences.

-----

## Phase 3 : Traitement des Champs Avancés (45 min)

### 🎯 Objectifs

  * Récupérer les données des champs de sélection unique (radio, select).
  * **Point Clé :** Récupérer les données des champs de sélection multiple (**checkbox, select multiple**) comme des **tableaux PHP**.
  * Utiliser la fonction PHP **`implode()`** pour afficher le contenu des tableaux.

### 🧑‍💻 Étapes

1.  **Ajout de la récupération avancée (45 min) :**
      * Modifiez **`traitement.php`** pour récupérer et afficher les valeurs des nouveaux champs.

<!-- end list -->

```php
<?php
// ... Code de la Phase 2 (récupération Nom, Email, affichage H1, H2) ...

// Phase 3 : Récupération des données avancées
echo "<h2>Données avancées :</h2>";

// 1. Radio et Select Unique (simple string)
$civilite = $_POST['civilite'] ?? 'N/A';
$pays = $_POST['pays'] ?? 'N/A';
echo "<p>Civilité (Radio) : $civilite</p>";
echo "<p>Pays (Select unique) : $pays</p>";

// 2. Textarea (simple string, mais nl2br pour les retours à la ligne)
$commentaire = $_POST['commentaire'] ?? 'Aucun';
echo "<p>Commentaire (Textarea) : <br>" . nl2br($commentaire) . "</p>"; // nl2br conserve les sauts de ligne

// 3. Checkbox (tableau)
echo "<h3>Technologies sélectionnées (Checkbox) :</h3>";
if (isset($_POST['techs']) && is_array($_POST['techs'])) {
    // implode() transforme le tableau en chaîne pour l'affichage
    $techs_list = implode(', ', $_POST['techs']);
    echo "<p>$techs_list</p>";
} else {
    echo "<p>Aucune technologie choisie.</p>";
}

// 4. Select Multiple (tableau)
echo "<h3>Couleurs sélectionnées (Select Multiple) :</h3>";
if (isset($_POST['couleurs']) && is_array($_POST['couleurs'])) {
    $couleurs_list = implode(' / ', $_POST['couleurs']);
    echo "<p>$couleurs_list</p>";
} else {
    echo "<p>Aucune couleur choisie.</p>";
}
?>
```

-----

## Phase 4 : Validation de Base et Sécurité (75 min)

### 🎯 Objectifs

  * Vérifier la soumission du formulaire (`isset`).
  * Assurer qu'un champ n'est pas vide (`empty`).
  * Introduction à la sécurité de base (`htmlspecialchars`).
  * (Avancé) Préparer la conservation des valeurs saisies après une erreur.

### 🧑‍💻 Étapes

1.  **Vérification de la soumission globale (15 min) :**

      * Encadrer tout le script `traitement.php` par la condition `if ($_SERVER['REQUEST_METHOD'] === 'POST') { ... }` pour s'assurer que le script n'est exécuté qu'après une soumission valide.

2.  **Contrôle de champs vides et création d'erreurs (25 min) :**

      * Créez un tableau `$erreurs = []`.
      * Utilisez **`if (empty(trim($_POST['champ'])))`** pour vérifier les champs de texte obligatoires.
      * Utilisez **`if (!isset($_POST['champ']))`** pour les champs radio/checkbox obligatoires non-cochés.

3.  **Sécurité de base (20 min) :**

      * Introduire et utiliser la fonction **`htmlspecialchars()`** sur **TOUTES** les données affichées (y compris les éléments des tableaux via `array_map`) pour prévenir les attaques XSS.

<!-- end list -->

```php
// Exemple de sécurisation (à appliquer avant l'affichage de la Phase 3)
$nom_securise = htmlspecialchars($_POST['nom']);

// Exemple de sécurisation des tableaux
$techs_s = array_map('htmlspecialchars', $_POST['techs']);
// Affichage : implode(', ', $techs_s);
```

4.  **Optionnel/Avancé - Conservation des données (15 min) :**
      * Discuter du mécanisme pour réafficher le formulaire (`index.php`) en cas d'erreur tout en pré-remplissant les champs avec les valeurs déjà saisies.
      * **Exemple HTML :** `<input type="text" name="nom" value="<?php echo isset($_POST['nom']) ? htmlspecialchars($_POST['nom']) : ''; ?>">`