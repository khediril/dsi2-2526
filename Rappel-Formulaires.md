# Points Essentiels des Formulaires HTML

L'objectif de cette synthèse est de fournir une base solide pour la création de formulaires robustes, accessibles et efficaces en HTML.

## I. Les Fondamentaux du Formulaire

Un formulaire HTML est la structure essentielle pour capturer des données utilisateur.

1.  **La balise `<form>` : Le Conteneur**

    * C'est le point de départ qui englobe tous les champs de saisie.

    * **Attribut `action` :** Définit l'URL vers laquelle les données seront envoyées (le script côté serveur qui traitera l'information).

    * **Attribut `method` :** Spécifie la méthode HTTP pour l'envoi :

        * `GET` : Utilisé pour les requêtes de lecture (ex. : recherche), les données sont visibles dans l'URL.

        * `POST` : Utilisé pour l'envoi de données (création, modification), les données sont masquées dans le corps de la requête.

2.  **L'attribut `name` : L'Identifiant Serveur (Crucial)**

    * Chaque champ de saisie (`<input>`, `<textarea>`, `<select>`) **doit** avoir un attribut `name`.

    * **Rôle :** Il définit le nom de la variable que le serveur recevra avec la valeur saisie par l'utilisateur. Sans `name`, la donnée n'est pas transmise.

3.  **Accessibilité avec `<label>`**

    * **Règle :** Toujours associer un `<label>` à son champ de saisie correspondant.

    * **Liaison :** La valeur de l'attribut **`for`** du `<label>` doit correspondre exactement à l'attribut **`id`** du champ.

    * **Avantage :** Ceci améliore l'accessibilité (les lecteurs d'écran lisent l'étiquette) et l'ergonomie (cliquer sur le texte du label active le champ).

4.  **Types d'Inputs Sémantiques**

    * Utilisez des types spécifiques (`email`, `tel`, `date`, `number`, `url`) au lieu de `text` lorsque possible.

    * **Bénéfices :** Le navigateur applique une validation initiale et propose des interfaces optimisées (ex. : calendrier pour `date`, clavier numérique pour `tel` sur mobile).

5.  **Validation Côté Client**

    * **`required` :** Rends le champ obligatoire pour la soumission.

    * **`min`, `max`, `maxlength`, `minlength` :** Définissent des contraintes de valeur ou de taille.

    * **L'Attribut `pattern` (Voir Section III)** : Permet d'appliquer des règles de format complexes.


---

## II. Précisions sur l'Attribut `name` pour les Éléments de Choix

L'attribut `name` est utilisé pour grouper les éléments de choix, ce qui influence directement la manière dont les données sont envoyées au serveur.

### 1. Les Boutons Radio (`<input type="radio">`)

| Rôle | Description |
| :--- | :--- |
| **Groupe d'Exclusivité** | Tous les boutons radio qui constituent un même groupe et dont l'utilisateur ne doit sélectionner qu'**un seul** choix doivent avoir le **même attribut `name`**. |
| **Transmission** | Le serveur reçoit uniquement la paire `name=value` du bouton sélectionné. |
| **Exemple** | `<input type="radio" name="couleur" value="bleu">` et `<input type="radio" name="couleur" value="rouge">`. |

### 2. Les Cases à Cocher (`<input type="checkbox">`)

| Rôle | Description |
| :--- | :--- |
| **Sélections Multiples** | Si l'utilisateur peut choisir plusieurs options d'une liste (le cas le plus courant). |
| **Convention** | Il est **fortement recommandé** d'ajouter des **crochets `[]`** à l'attribut `name` (ex. : `name="interets[]"`). |
| **Transmission** | Les crochets signalent au serveur que toutes les valeurs cochées doivent être rassemblées dans un **tableau** (ou une liste) unique. |
| **Exemple** | `<input type="checkbox" name="options[]" value="a">` et `<input type="checkbox" name="options[]" value="b">`. Si les deux sont cochés, le serveur reçoit `options=[a, b]`. |

### 3. Les Listes Déroulantes (`<select>`)

L'attribut `name` est placé sur le conteneur `<select>`.

| Rôle | Description |
| :--- | :--- |
| **Sélection Simple** | Par défaut, un seul choix est possible, le `name` est simple (ex. : `name="ville"`). |
| **Sélection Multiple** | Pour permettre à l'utilisateur de choisir plusieurs options (en utilisant `Ctrl`/`Cmd` + clic) : |
| **Attribut HTML** | La balise `<select>` **doit** avoir l'attribut **`multiple`**. |
| **Attribut `name`** | Le `name` doit également être suivi des **crochets `[]`** (ex. : `name="pays_visites[]"`). |

---

## III. Validation Avancée Côté Client : L'Attribut `pattern`

L'attribut **`pattern`** utilise les **Expressions Régulières (RegEx)** pour définir un format spécifique que la valeur du champ doit respecter.

### Fonctionnement et Convention

* **Règles de Format :** L'Expression Régulière est une séquence de caractères définissant un modèle.
* **Ancrage :** La saisie doit correspondre à **l'intégralité** du modèle. Utilisez les ancres de début (`^`) et de fin (`$`) pour encadrer le modèle (ex. : `pattern="^MODÈLE$"`).
* **Message d'Erreur :** Utilisez l'attribut **`title`** pour fournir un message clair à l'utilisateur si la validation échoue.

### Exemples d'Utilisation

| Objectif de Validation | Expression Régulière | Exemple HTML | Explication RegEx |
| :--- | :--- | :--- | :--- |
| **Code Postal (5 chiffres)** | `^\d{5}$` | `<input type="text" pattern="^\d{5}$" title="5 chiffres requis">` | `\d` = tout chiffre. `{5}` = exactement 5 fois. |
| **Nom d'utilisateur (3 à 15 lettres minuscules)** | `^[a-z]{3,15}$` | `<input type="text" pattern="^[a-z]{3,15}$" title="3 à 15 lettres minuscules uniquement">` | `[a-z]` = lettres minuscules. `{3,15}` = longueur entre 3 et 15. |
| **Plaque d'immatriculation (format ancien : AA-000-BB)** | `^[A-Z]{2}-[0-9]{3}-[A-Z]{2}$` | `<input type="text" pattern="^[A-Z]{2}-[0-9]{3}-[A-Z]{2}$">` | `[A-Z]` = lettres majuscules. `[0-9]` = chiffres. |

---

## IV. Validation Côté Client vs. Validation Côté Serveur

La validation doit toujours être effectuée aux deux niveaux pour garantir la sécurité et une bonne expérience utilisateur. 

### 1. Validation Côté Client (Client-Side Validation)

| Caractéristique | Rôle | Sécurité |
| :--- | :--- | :--- |
| **Où ?** | Dans le **navigateur** de l'utilisateur (HTML5, JavaScript). | **Non sécurisée.** Peut être contournée (par exemple, en désactivant JavaScript). |
| **Objectif** | Améliorer l'**expérience utilisateur (UX)** en fournissant un retour **immédiat** et en évitant des allers-retours inutiles au serveur. |
| **Méthodes** | Attributs HTML (`required`, `type`, `pattern`), JavaScript. |

### 2. Validation Côté Serveur (Server-Side Validation)

| Caractéristique | Rôle | Sécurité |
| :--- | :--- | :--- |
| **Où ?** | Sur le **serveur web** (PHP, Node.js, Python, etc.), après la soumission. | **Essentielle.** C'est la seule validation **fiable**, car elle est impossible à contourner. |
| **Objectif** | **Garantir la sécurité** et l'intégrité des données, protéger contre les injections (SQL, XSS), et s'assurer que les règles métier sont respectées. |
| **Méthodes** | Langages de programmation back-end, vérifications de base de données. |

**Conclusion :** La validation **côté client** est un confort d'utilisation, mais la validation **côté serveur** est une obligation de sécurité. **Les deux sont indispensables.**