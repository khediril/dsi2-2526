
# 🩸 Cahier des Charges : Système de Gestion de Don de Sang (PHP Natif & Bootstrap)

## I. Objectifs du Projet

Le but de ce mini-projet est de développer une **application web simple et sécurisée** utilisant **PHP natif** (sans framework lourd) et une base de données **MySQL/MariaDB** pour simuler la gestion des donneurs, des dons, du stock et de la traçabilité au sein d'un centre de collecte.

L'objectif principal est la maîtrise technique de :
1.  L'utilisation des **sessions PHP** pour l'authentification et l'autorisation.
2.  Les interactions **CRUD** (Create, Read, Update, Delete) sécurisées avec la base de données via **requêtes préparées** (`mysqli` ou `PDO`).
3.  L'intégration et l'utilisation d'un **template Bootstrap** pour une interface utilisateur professionnelle et responsive.

---

## II. Base de Données (Schéma Physique Détaillé)

Le projet doit utiliser les **sept (7) tables** suivantes pour garantir une gestion complète et une traçabilité rigoureuse :

| Table | Rôle Primaire | Champs Clés | Contraintes Importantes |
| :--- | :--- | :--- | :--- |
| **1. `donneurs`** | Profil des personnes pouvant donner du sang. | `id_donneur` (PK), `cin`, `groupe_sanguin` | `cin` (UNIQUE), Utilisation d'un `ENUM` pour le `rhesus` |
| **2. `centres_collecte`** | Lieux physiques où les dons sont effectués. | `id_centre` (PK) | |
| **3. `utilisateurs`** | Gestion des accès et des rôles du personnel (Admin, Médecin, Secrétaire). | `id_utilisateur` (PK), `role`, `id_centre` (FK) | `mot_de_passe` **Haché** (via `password_hash`) |
| **4. `dons`** | Enregistrement de chaque poche de sang/produit collecté. | `id_don` (PK), `id_donneur` (FK), `id_centre` (FK) | Gère le `statut` (EN STOCK, UTILISÉ, REJETÉ) |
| **5. `tests_don`** | Résultats des analyses effectuées sur chaque don. | `id_test` (PK), `id_don` (FK, UNIQUE) | Indique si le don est `est_conforme` |
| **6. `transfusions`** | Traçabilité de l'utilisation finale du produit. | `id_transfusion` (PK), `id_don` (FK, UNIQUE) | Gère l'`hopital_recepteur` et la `date_transfusion` |
| **7. `besoins`** | Gestion des niveaux de stock critiques et des alertes. | `id_besoin` (PK), `groupe_sanguin`, `niveau_alerte` | `niveau_alerte` (ENUM: URGENT, CRITIQUE, NORMAL) |

---

## III. Fonctionnalités Détaillées 🚀

Les fonctionnalités doivent être implémentées en PHP natif, en respectant les autorisations basées sur le rôle de l'utilisateur connecté.

### A. Sécurité et Authentification (Rôle)
1.  **Connexion (`login.php`) :**
    * Vérification du `nom_utilisateur` et du mot de passe (via `password_verify`).
    * Création d'une **session PHP** pour stocker l'`id_utilisateur` et son `role`.
2.  **Autorisation :** Les pages et actions doivent être protégées par une vérification de session et de rôle.
    * **ADMIN** a accès à tout (gestion des utilisateurs, centres).
    * **MEDECIN** a accès à la validation des dons et aux tests.
    * **SECRETAIRE** a accès à la gestion des donneurs et à l'enregistrement des dons.
3.  **Déconnexion :** Fonctionnalité pour détruire la session.

### B. Gestion des Donneurs (CRUD - SECRETAIRE/ADMIN)
1.  **Ajout :** Formulaire pour créer un nouveau donneur (Insertion dans `donneurs`).
2.  **Liste/Recherche :** Affichage de la liste paginée des donneurs, avec filtres par **Groupe Sanguin** et **Ville**.
3.  **Détails/Historique :** Page affichant les informations complètes du donneur, y compris son **historique de dons** (jointure `donneurs` et `dons`).

### C. Gestion des Dons et du Stock (SECRETAIRE/MEDECIN/ADMIN)
1.  **Enregistrement de Don (SECRETAIRE) :**
    * Formulaire pour créer un enregistrement dans `dons`.
    * Le donneur (`id_donneur`) et le centre (`id_centre`) doivent être sélectionnés via des listes déroulantes (peuplement dynamique par PHP).
    * Statut initial : 'EN STOCK'.
2.  **Validation des Tests (MEDECIN) :**
    * Consultation des dons ayant le statut 'EN STOCK'.
    * Formulaire pour enregistrer les résultats (`tests_don`) et marquer le don comme `est_conforme`.
    * Mise à jour automatique de `dons.statut` à 'VALIDE' ou 'REJETÉ'.
3.  **Traçabilité / Transfusion (ADMIN) :**
    * Consultation des dons ayant le statut 'VALIDE'.
    * Formulaire pour enregistrer l'utilisation du don (`transfusions`).
    * Mise à jour de `dons.statut` à 'UTILISÉ'.

### D. Tableau de Bord et Alertes (INDEX)
1.  **Statistiques Clés :** Afficher via des cartes Bootstrap les totaux (Donneurs, Dons Valides, Centres).
2.  **État du Stock :** Calculer et afficher la quantité (théorique) disponible par Groupe Sanguin.
3.  **Alertes (Table `besoins`) :** Mettre en évidence les groupes sanguins dont le `niveau_alerte` est 'URGENT' ou 'CRITIQUE'.

---

## IV. Contraintes Techniques et Ergonomie 🛠️

### A. Contraintes Techniques Strictes
* **Langage/DB :** PHP (7.x/8.x) et MySQL/MariaDB.
* **Framework PHP :** **Interdit**. Utilisation de PHP natif (procédural ou POO minimaliste).
* **Sécurité (Crucial) :**
    * **Injection SQL :** Utilisation **systématique des requêtes préparées** (`mysqli` ou `PDO`).
    * **XSS :** Utilisation de `htmlspecialchars()` lors de l'affichage des données utilisateurs.
    * **Mots de passe :** Utilisation de `password_hash()` et `password_verify()`.

### B. Contraintes d'Ergonomie et d'Interface (Bootstrap)
* **Template Requis :** Le projet doit utiliser un **template d'administration Bootstrap** (version 4 ou 5) pour l'ensemble de l'interface (ex: SB Admin, AdminLTE ou un template maison basé sur Bootstrap).
* **Design Responsive :** L'interface doit être fonctionnelle sur les appareils mobiles.
* **Composants Obligatoires :**
    * **Navigation :** Mise en place d'une barre latérale (sidebar) ou d'une barre de navigation principale stylisée par Bootstrap, adaptée au rôle de l'utilisateur.
    * **Formulaires :** Tous les formulaires doivent utiliser les **classes de formulaire Bootstrap** pour l'alignement et l'affichage des validations.
    * **Tables :** Utilisation des **tables stylisées Bootstrap** pour la présentation des listes de données.
    * **Alertes :** Utilisation des composants **Alerts de Bootstrap** pour les messages de succès, d'erreur et d'information.