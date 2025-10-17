# Atelier PHP : Maîtriser les Formulaires (3 Heures)

Cet atelier vise à comprendre le fonctionnement des formulaires HTML et leur traitement côté serveur avec PHP, en se concentrant sur les méthodes **GET** et **POST**, la **récupération des données** et une **validation de base**.

-----

## Phase 1 : Introduction et Formulaire HTML 

### Objectifs

  * Comprendre le rôle d'un formulaire dans une application web.
  * Créer un formulaire HTML de base avec les attributs essentiels (`action`, `method`).
  * Distinguer les méthodes **GET** et **POST**.

### Étapes

1.  **Rappel :** Discuter des rôles du client (HTML/CSS) et du serveur (PHP) dans la gestion des formulaires. Expliquer l'importance des attributs **`action`** (cible du script PHP) et **`method`** (`GET` ou `POST`).
2.  **Création du fichier  :**
      * Créez un fichier nommé **`index.php`**.
      * Ajoutez la structure HTML de base.
      * Créez un formulaire simple de connexion/inscription avec deux champs (Nom, Email) et un bouton de soumission.

<!-- end list -->

```html
<form action="traitement.php" method="post">
    <label for="nom">Nom :</label>
    <input type="text" id="nom" name="nom"><br><br>

    <label for="email">Email :</label>
    <input type="email" id="email" name="email"><br><br>

    <input type="submit" value="Envoyer">
</form>
```

-----

## Phase 2 : Récupération des Données en PHP 

### Objectifs

  * Utiliser les tableaux superglobaux **`$_POST`** et **`$_GET`** pour récupérer les données.
  * Afficher les données reçues sur la page de traitement.

### Étapes

1.  **Création du script de traitement :**
      * Créez un fichier nommé **`traitement.php`** (comme spécifié dans l'attribut `action`).
      * Utilisez **`$_POST`** pour récupérer les valeurs des champs Nom et Email.
      * Affichez les valeurs récupérées.

<!-- end list -->

```php
<?php
$nom = $_POST['nom'];
$email = $_POST['email'];

echo "<h2>Bonjour, $nom !</h2>";
echo "<p>Votre email est : $email</p>";
?>
```

2.  **Test et Observation  :**
      * Tester le formulaire et observer le résultat.
      * **Exercice :** Changer la méthode du formulaire dans `index.php` de **`post`** à **`get`** et observer l'URL après soumission. Modifier le code PHP dans `traitement.php` pour utiliser **`$_GET`** à la place de `$_POST`. Discuter des différences (visibilité des données, limite de taille, cas d'utilisation).

-----

## Phase 3 : Validation de Base et Sécurité 

### Objectifs

  * Vérifier si les données ont été soumises (utilisation de **`isset`**).
  * Assurer qu'un champ n'est pas vide (utilisation de **`empty`**).
  * Introduction à la sécurité de base (**`htmlspecialchars`**).
  * Conserver les valeurs saisies après une erreur.

### Étapes

1.  **Vérification de la soumission :**
      * Modifiez `traitement.php` pour vérifier si la variable est définie.

<!-- end list -->

```php
<?php
if (isset($_POST['nom']) && isset($_POST['email'])) {
    // Le traitement se fait ici
} else {
    echo "<p>Le formulaire n'a pas été soumis correctement.</p>";
}
?>
```

2.  **Contrôle de champs vides  :**

      * Ajouter une vérification pour s'assurer que les champs ne sont pas vides (**`!empty()`**). Afficher un message d'erreur si c'est le cas.

3.  **Sécurité de base  :**

      * Introduire et utiliser la fonction **`htmlspecialchars()`** lors de l'affichage des données pour prévenir les attaques XSS (Cross-Site Scripting).

<!-- end list -->

```php
$nom_securise = htmlspecialchars($_POST['nom']);
// ... afficher $nom_securise
```

4.  **Optionnel/Avancé - Conservation des données  :**
      * Expliquer comment conserver la valeur d'un champ après une erreur de soumission, en insérant la valeur reçue dans l'attribut `value` du champ HTML (Exemple : `value="<?php echo isset($_POST['nom']) ? htmlspecialchars($_POST['nom']) : ''; ?>"`).