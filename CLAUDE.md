# grist-widgets — Ville du Bouscat

Widgets Grist (« vues personnalisées ») de la commune, publiés en pages statiques.

## Nature du dépôt

- Dépôt **public**, hébergé sur GitHub sous l'organisation `ville-du-bouscat`.
- Publié par GitHub Pages sur `https://ville-du-bouscat.github.io/grist-widgets/`.
- Chaque widget est **un fichier HTML autonome** dans son propre dossier, sans dépendance
  externe, sans build. Ne pas introduire de bundler, de framework ni de `node_modules`.

## Règle absolue : aucune donnée réelle dans ce dépôt

Le dépôt est public. Les widgets embarquent un **jeu de démonstration fictif** pour rester
lisibles hors de Grist. Ne jamais y committer de données réelles de la commune : montants
réels, prénoms d'agents, commentaires d'arbitrage, opérations « pour DM » non votées,
projets de sécurité publique.

Avant tout commit touchant un widget, vérifier que la page ne contient pas de données du
document Grist réel.

## Comment fonctionne un widget

Chaque page détecte son contexte :

- hors de Grist (double-clic, ou ouverture directe de l'URL) → affiche le jeu fictif embarqué ;
- dans Grist → charge `grist-plugin-api.js` **depuis l'instance hôte** (déduite du référent),
  appelle `grist.ready({requiredAccess:'full'})` puis `grist.docApi.fetchTable(...)`.

Le widget est en **lecture seule**. Les seuls appels à Grist sont des `fetchTable`.
Ne jamais y ajouter d'écriture sans décision explicite.

L'accès `full` est nécessaire parce que le widget lit plusieurs tables ; le niveau
« lire la table sélectionnée » ne donne accès qu'à une seule.

## Widget `reconciliation-investissement`

Rapproche l'extraction comptable Grand Angle et le suivi opérationnel des services techniques.

Trois tables Grist, clé de jointure `Cle_INV` de la forme `LB100O003|4486` :

| Table | Une ligne = | Fait autorité sur |
|---|---|---|
| `Gda` | opération × NATANA | budget voté, consommé, mandaté |
| `Suivi_natana` | opération × NATANA | enveloppe globale, engagements |
| `Sous_operations` | n° INV | affectation fine, lieu, objet, agent |

Point de conception à ne pas casser : les sous-opérations ne portent que le crédit 2026,
les reports sont portés au niveau NATANA. Le rapprochement se fait donc **au niveau NATANA**,
jamais en sommant les sous-opérations.

Noms de tables et adresse de secours de l'instance sont en tête du fichier HTML
(`TABLES`, `GRIST_URL_MANUEL`).

## Instance Grist

`https://grist.numerique.gouv.fr` (La Suite numérique, DINUM).

## Vérification d'un widget avant commit

Servir le dépôt en statique et ouvrir la page dans un navigateur — Playwright et Chromium
sont disponibles localement. Contrôler qu'il n'y a **aucune erreur console**, dans les deux
thèmes clair et sombre.
