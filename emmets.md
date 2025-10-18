# Guide Rapide : Accélérer l'écriture HTML/CSS avec Emmet dans VS Code

Emmet est un ensemble d'abréviations (snippets) qui permet d'écrire du code HTML et CSS très rapidement en utilisant une syntaxe inspirée des sélecteurs CSS. Il est intégré par défaut dans VS Code.

**Principe :** Vous écrivez l'abréviation, puis vous appuyez sur la touche `Tab` (ou `Entrée`) pour la développer en code complet.

## I. Les Fondamentaux pour HTML

### 1. Structure de Base de la Page

L'abréviation la plus utilisée pour commencer un projet.

| Abréviation | Code Généré | Description |
| :--- | :--- | :--- |
| `!` ou `html:5` | Structure de base HTML5 complète. | Inclut le doctype, le `<head>` avec le viewport, et le `<body>`. |

### 2. Création d'Éléments Simples

Tapez le nom de l'élément que vous souhaitez créer.

| Abréviation | Code Généré |
| :--- | :--- |
| `header` | `<header></header>` |
| `section` | `<section></section>` |
| `a` | `<a href=""></a>` |
| `input:text` | `<input type="text" name="" id="">` |

### 3. Ajout d'Attributs (Classes et IDs)

Utilisez la syntaxe CSS standard pour les sélecteurs : `#` pour les IDs et `.` pour les classes.

| Abréviation | Code Généré | Description |
| :--- | :--- | :--- |
| `div#menu` | `<div id="menu"></div>` | Crée un `div` avec l'ID `menu`. |
| `p.intro` | `<p class="intro"></p>` | Crée un paragraphe avec la classe `intro`. |
| `.card.is-active` | `<div class="card is-active"></div>` | Crée un `div` avec deux classes. |
| `img[alt="Logo"][src="img/"]` | `<img src="img/" alt="Logo">` | Ajout d'attributs personnalisés entre crochets. |

### 4. Groupement et Enchaînement (Combinateurs)

Emmet utilise les mêmes symboles que les combinateurs CSS pour définir la relation entre les éléments.

| Symbole | Nom | Fonction | Exemple |
| :--- | :--- | :--- | :--- |
| `>` | Enfant | Place l'élément à l'intérieur de son parent. | `div>ul>li` → `<div><ul><li></li></ul></div>` |
| `+` | Voisin | Place l'élément après un autre, au même niveau. | `h1+p` → `<h1></h1><p></p>` |
| `^` | Monter | Remonte d'un niveau dans l'arborescence. | `div>ul>li^p` → `<div><ul><li></li></ul><p></p></div>` |
| `()` | Groupement | Isole un bloc d'éléments pour les regrouper. | `(header>ul)+main` → `<header><ul></ul></header><main></main>` |

### 5. Multiplicateurs

Le symbole `*` permet de répéter un élément un certain nombre de fois.

| Abréviation | Code Généré |
| :--- | :--- |
| `li*3` | `<li></li><li></li><li></li>` |
| `ul>li*4` | `<ul><li></li><li></li><li></li><li></li></ul>` |

### 6. Numérotation (Séquence)

Le symbole `$` insère un numéro séquentiel (de 1 à N) dans le nom de la classe ou de l'ID.

| Abréviation | Code Généré |
| :--- | :--- |
| `li.item$*3` | `<li class="item1"></li>`<br>`<li class="item2"></li>`<br>`<li class="item3"></li>` |
| `li.item$$$*2` | `<li class="item001"></li>`<br>`<li class="item002"></li>` |
| `li.item$@-*3` | `<li class="item3"></li>`<br>`<li class="item2"></li>`<br>`<li class="item1"></li>` |

---

## II. Les Fondamentaux pour CSS

Emmet dans VS Code accélère également l'écriture des propriétés et des valeurs CSS. 

### 1. Propriétés Courantes

Vous pouvez taper l'abréviation de la propriété pour obtenir le nom complet et le deux-points.

| Abréviation | Code Généré | Description |
| :--- | :--- | :--- |
| `w100` | `width: 100px;` | Raccourcis numériques simples (pour `px`). |
| `m20` | `margin: 20px;` | `m` pour margin, `p` pour padding. |
| `bgc` | `background-color: #000;` | Initiales de la propriété. |
| `pos:a` | `position: absolute;` | Propriété + Initiale de la valeur. |

### 2. Multi-directions (Margin et Padding)

| Abréviation | Code Généré |
| :--- | :--- |
| `mt20` | `margin-top: 20px;` |
| `pb10` | `padding-bottom: 10px;` |
| `m-auto` | `margin: auto;` |

### 3. Unités de Mesure

Ajoutez le nom de l'unité directement après la valeur.

| Abréviation | Code Généré |
| :--- | :--- |
| `w100p` | `width: 100%;` |
| `h100vh` | `height: 100vh;` |
| `fs1-2em` | `font-size: 1.2em;` |
| `m-10` | `margin: -10px;` |

### 4. Flexbox et Grid (Très Utile)

| Abréviation | Code Généré |
| :--- | :--- |
| `d:f` | `display: flex;` |
| `jc:sb` | `justify-content: space-between;` |
| `ai:c` | `align-items: center;` |
| `d:g` | `display: grid;` |
| `gtc` | `grid-template-columns: ;` |

---

## III. Exemple Complet (HTML)

Nous allons générer la structure d'une liste de 4 éléments avec classes et titres.

| Tâche | Abréviation Emmet | Développé par VS Code |
| :--- | :--- | :--- |
| **Créer la structure** | `div.wrapper>ul.list>(li.item$*4>h3{Titre $}+p.description)` | **Résultat :** `
<div class="wrapper">
    <ul class="list">
        <li class="item1">
            <h3>Titre 1</h3>
            <p class="description"></p>
        </li>
        <li class="item2">
            <h3>Titre 2</h3>
            <p class="description"></p>
        </li>
        <li class="item3">
            <h3>Titre 3</h3>
            <p class="description"></p>
        </li>
        <li class="item4">
            <h3>Titre 4</h3>
            <p class="description"></p>
        </li>
    </ul>
</div>
` |

---

## IV. Astuces et Configuration dans VS Code

* **Répétition de la Dernière Action :** Vous pouvez souvent répéter la dernière abréviation développée en appuyant sur `Ctrl + Maj + P` et en recherchant "Emmet: Re-run Last Command".
* **Utilisation dans d'Autres Langages :** Par défaut, Emmet est actif pour HTML, CSS, SCSS, LESS, etc. Si vous souhaitez l'activer pour d'autres types de fichiers (ex: JavaScript, React JSX), vous devez modifier les paramètres de VS Code (`Fichier > Préférences > Paramètres`, rechercher `emmet.includeLanguages` et ajouter l'extension de fichier désirée).