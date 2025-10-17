# Atelier PHP : Maîtriser les Formulaires Avancés

Cet atelier guidé couvre la création d'un formulaire HTML riche, sa soumission via **POST** et le traitement des données côté serveur avec PHP, en se concentrant sur les champs standards et les sélections multiples (**radio, checkbox, select multiple, textarea**).

---

## Phase 1 : Création du Formulaire HTML Avancé

### Objectifs
* Définir un formulaire avec l'action (`action="traitement.php"`) et la méthode (`method="post"`) appropriées.
* Utiliser différents types d'entrées, y compris les champs nécessitant des noms de tableau (`name="champ[]"`).

### Fichier : `index.php`

```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <title>Formulaire Avancé PHP</title>
    <style>body { font-family: sans-serif; }</style>
</head>
<body>

    <h1>Inscription / Sondage Développeur</h1>

    <form action="traitement.php" method="post">

        <label for="nom">Nom :</label>
        <input type="text" id="nom" name="nom" required><br><br>

        <label for="email">Email :</label>
        <input type="email" id="email" name="email" required><br><br>

        <p>Civilité (Radio) :</p>
        <input type="radio" id="civilite_m" name="civilite" value="M." required>
        <label for="civilite_m">Monsieur</label>
        <input type="radio" id="civilite_mme" name="civilite" value="Mme">
        <label for="civilite_mme">Madame</label><br><br>

        <p>Technologies préférées (Checkbox - Choix multiples) :</p>
        <input type="checkbox" id="tech_php" name="techs[]" value="PHP">
        <label for="tech_php">PHP</label><br>
        <input type="checkbox" id="tech_js" name="techs[]" value="JavaScript">
        <label for="tech_js">JavaScript</label><br>
        <input type="checkbox" id="tech_sql" name="techs[]" value="SQL">
        <label for="tech_sql">SQL</label><br><br>

        <label for="pays">Pays (Select unique) :</label>
        <select id="pays" name="pays">
            <option value="FR">France</option>
            <option value="CA">Canada</option>
            <option value="TN">Tunisie</option>
            <option value="AUTRE">Autre</option>
        </select><br><br>

        <label for="couleurs">Couleurs de thème (Select multiple - Ctrl/Cmd + Clic) :</label>
        <select id="couleurs" name="couleurs[]" multiple size="4">
            <option value="bleu">Bleu</option>
            <option value="vert">Vert</option>
            <option value="rouge">Rouge</option>
            <option value="noir">Noir</option>
        </select><br><br>

        <label for="commentaire">Commentaire libre (Textarea) :</label><br>
        <textarea id="commentaire" name="commentaire" rows="5" cols="40"></textarea><br><br>

        <input type="submit" value="Envoyer les données">
    </form>

</body>
</html>
````

-----

## Phase 2 : Récupération et Affichage des Données

### Objectifs

  * Utiliser la superglobale `$_POST` pour récupérer toutes les données.
  * **Point Clé :** Récupérer les données des **checkbox** et **select multiple** comme des **tableaux PHP**.
  * Utiliser la fonction PHP `implode()` pour afficher les données des tableaux.

### Fichier : `traitement.php` (Étape 1 : Récupération brute)

```php
<?php
// On inclut le traitement dans une condition de soumission POST
if ($_SERVER['REQUEST_METHOD'] === 'POST') {

    echo "<h1>Récapitulatif des données reçues</h1>";
    echo "<hr>";

    // --- A. Récupération des champs uniques (string) ---
    // Les champs Radio, Text, Email, Select unique, Textarea sont des chaînes.

    $nom = $_POST['nom'] ?? 'N/A';
    $email = $_POST['email'] ?? 'N/A';
    $civilite = $_POST['civilite'] ?? 'Non sélectionné'; // Radio
    $pays = $_POST['pays'] ?? 'N/A'; // Select unique
    $commentaire = $_POST['commentaire'] ?? 'Aucun'; // Textarea

    echo "<h2>Informations Personnelles :</h2>";
    echo "<p><strong>Civilité :</strong> $civilite</p>";
    echo "<p><strong>Nom :</strong> $nom</p>";
    echo "<p><strong>Email :</strong> $email</p>";
    echo "<p><strong>Pays :</strong> $pays</p>";
    echo "<p><strong>Commentaire :</strong><br>" . nl2br($commentaire) . "</p>";


    // --- B. Récupération des champs à sélection multiple (tableau) ---

    // 1. Checkbox : Technologies préférées (techs[])
    echo "<h2>Technologies préférées (Checkbox) :</h2>";
    if (isset($_POST['techs']) && is_array($_POST['techs'])) {
        // Implode convertit le tableau en une chaîne de caractères
        $techs_list = implode(' | ', $_POST['techs']);
        echo "<p>Vous aimez : $techs_list</p>";
    } else {
        echo "<p>Aucune technologie sélectionnée.</p>";
    }

    // 2. Select Multiple : Couleurs (couleurs[])
    echo "<h2>Couleurs de thème (Select Multiple) :</h2>";
    if (isset($_POST['couleurs']) && is_array($_POST['couleurs'])) {
        $couleurs_list = implode(' et ', $_POST['couleurs']);
        echo "<p>Vos couleurs sélectionnées sont : $couleurs_list</p>";
    } else {
        echo "<p>Aucune couleur sélectionnée.</p>";
    }

} else {
    echo "<p>Veuillez soumettre le formulaire depuis la page <a href='index.php'>d'accueil</a>.</p>";
}
?>
```

-----

## Phase 3 : Validation et Sécurité des Données

### Objectifs

  * Mettre en place la validation des champs obligatoires en utilisant `empty()` et `trim()`.
  * **Sécuriser les données affichées** en utilisant `htmlspecialchars()`.
  * Gérer l'affichage des erreurs ou le traitement final.

### Fichier : `traitement.php` (Étape 2 : Avec validation et sécurité)

```php
<?php
// On réinitialise le script pour inclure la validation
if ($_SERVER['REQUEST_METHOD'] === 'POST') {

    $erreurs = [];

    // --- 1. Validation des champs obligatoires (simples) ---

    if (!isset($_POST['nom']) || trim($_POST['nom']) === '') {
        $erreurs[] = "Le nom est obligatoire.";
    }
    if (!isset($_POST['email']) || trim($_POST['email']) === '') {
        $erreurs[] = "L'email est obligatoire.";
    }
    // Note : Le champ radio 'civilite' est marqué 'required' en HTML, mais une vérification server-side est recommandée.
    if (!isset($_POST['civilite'])) {
        $erreurs[] = "La civilité est obligatoire.";
    }

    // --- 2. Validation des sélections multiples (tableaux) ---

    // S'assurer qu'au moins une technologie est sélectionnée (si c'est obligatoire)
    if (!isset($_POST['techs']) || !is_array($_POST['techs']) || count($_POST['techs']) === 0) {
        $erreurs[] = "Veuillez choisir au moins une technologie préférée.";
    }


    // --- 3. Affichage des erreurs ou Traitement final ---

    if (count($erreurs) > 0) {
        // Affichage des erreurs
        echo "<h2>⚠️ Erreurs de validation :</h2>";
        echo "<ul>";
        foreach ($erreurs as $erreur) {
            echo "<li>$erreur</li>";
        }
        echo "</ul>";
        echo "<p><a href='index.php'>Retour au formulaire pour correction</a></p>";
    } else {
        // --- Le formulaire est VALIDE : on sécurise et on affiche ---

        echo "<h1>✅ Soumission Réussie !</h1>";
        echo "<hr>";

        // Sécurisation de toutes les données avant affichage (IMPORTANT)
        $nom_s = htmlspecialchars($_POST['nom']);
        $email_s = htmlspecialchars($_POST['email']);
        $civilite_s = htmlspecialchars($_POST['civilite']);
        $pays_s = htmlspecialchars($_POST['pays']);
        $commentaire_s = htmlspecialchars($_POST['commentaire']);

        // Sécurisation des tableaux avec array_map
        $techs_s = array_map('htmlspecialchars', $_POST['techs']);
        $couleurs_s = array_map('htmlspecialchars', $_POST['couleurs']);

        echo "<h2>Informations Personnelles :</h2>";
        echo "<p><strong>Bonjour $civilite_s $nom_s !</strong></p>";
        echo "<p><strong>Email :</strong> $email_s</p>";
        echo "<p><strong>Commentaire :</strong><br>" . nl2br($commentaire_s) . "</p>";


        // Affichage des tableaux sécurisés
        echo "<h2>Résultats du sondage :</h2>";
        echo "<p><strong>Technologies :</strong> " . implode(' | ', $techs_s) . "</p>";
        echo "<p><strong>Couleurs :</strong> " . implode(' et ', $couleurs_s) . "</p>";

        // ÉTAPE SUIVANTE : Ici, vous enverriez les données vers une base de données ou un email.
    }

} else {
    echo "<p>Accès non autorisé.</p>";
}
?>
```

```
```