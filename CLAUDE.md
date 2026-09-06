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

## Widget `dysfonctionnements-synthese`

Vue de lecture du document « Suivi dysfonctionnements restauration » (restauration scolaire, DSP).

Une seule table Grist, `Dysfonctionnements`. Colonnes utilisées : `Date_du_constat`, `Ecole`,
`Office` (formule), `Categorie`, `Gravite`, `Description`, `Statut`, `Date_de_resolution`,
`Annee_scolaire`. Le widget tolère un colId renommé grâce à `pick()`, qui essaie plusieurs noms.

Points de conception à ne pas casser :

- **Le widget n'identifie pas l'agent, et ne doit pas chercher à le faire.** Les règles d'accès du
  document filtrent déjà `fetchTable` : un responsable ne reçoit que les lignes de son office. Tout
  filtrage par identité ajouté dans la page serait redondant et donnerait une fausse impression de
  sécurité — la sécurité est dans l'ACL, pas ici.
- **L'année scolaire bascule au 1er septembre.** Elle est lue dans `Annee_scolaire` si la colonne est
  renseignée, et recalculée depuis `Date_du_constat` sinon. Le sélecteur propose toujours l'année
  courante, même vide, et l'année suivante à partir de juin : c'est ce qui donne l'« espace vierge »
  de la rentrée sans dupliquer le document.
- **Aucun graphique n'utilise de bibliothèque.** Le graphique mensuel est un SVG écrit à la main.
  Ne pas introduire Chart.js ni aucun CDN : le dépôt est sans dépendance externe.
- Seuils métier en tête de script : `SEUIL_RETARD` (7 jours) et `SEUIL_RECURRENCE` (3 occurrences).

Le jeu de démonstration embarqué utilise des écoles inventées (FER, FERE, CUR, PAG, PRE, SEV) qui ne
correspondent à aucun office réel. Ne jamais y substituer les codes de la commune.

## Widget `suivi-eau-synthese`

Vue de lecture du document « Suivi de l'eau » (eau potable du parc communal, 65 points de livraison).

Trois tables Grist, clé de jointure `Reference_contrat` :

| Table | Une ligne = | Fait autorité sur |
|---|---|---|
| `Points_livraison` | un point de livraison | référentiel : site, engagement, seuil, télérelève, conso de référence |
| `Consommations` | contrat × mois | la série mensuelle télérelevée |
| `Suivi_fuites` | un épisode de fuite | le constat des agents — la seule donnée saisie à la main |

Points de conception à ne pas casser :

- **Le widget ne sert pas à afficher des m³, il sert à montrer ce qui attend quelqu'un.** La file
  des épisodes ouverts sans vérificateur est la première chose affichée, et le compteur
  correspondant est la seule tuile en rouge avec les fuites ouvertes. C'est faute d'avoir refermé
  cette boucle que 183 alertes de la Régie sont restées sans suite. Ne pas rétrograder cet écran
  au profit des graphiques de consommation.
- **`Suivi_fuites` est en lecture ici, mais c'est une table de saisie.** Le widget ne doit jamais
  y écrire : la saisie se fait dans la vue Grist native, où l'historique et les règles d'accès
  s'appliquent.
- **Relevé et facture ne se mélangent pas.** Le widget n'affiche que du relevé télérelevé. Un écart
  d'environ 43 % subsiste entre consommation facturée et relevés ; la règle retenue est *la facture
  fait foi pour le budget, le relevé fait foi pour le suivi technique*. Ne pas additionner les deux
  sources dans un même total.
- **Un mois sans mesure n'est pas un mois de faible consommation.** La série contient une queue
  de mois anciens où deux à six compteurs seulement remontaient un relevé — six points de
  livraison posés avant la généralisation de la télérelève, dont deux n'ont *que* cette histoire.
  Ces relevés sont réels et ne doivent pas être supprimés à l'import. C'est le **cumul du parc**
  qui n'a pas de sens sur ces mois-là : `moisParcs()` n'y retient donc que les mois où au moins
  `PART_MIN_MOIS` (la moitié) des compteurs habituels ont remonté, et affiche sous le graphique
  combien de mois ont été écartés et pourquoi. Le seuil est calculé sur la médiane des effectifs
  mensuels, pas figé en dur : il suit le parc. Ne pas remplacer ce garde-fou par une date de
  début écrite en dur, et ne pas le déplacer dans `preparer_imports.py`, qui effacerait la seule
  série de ces deux points.
- **Aucun graphique n'utilise de bibliothèque** : `barresV` (SVG écrit à la main) et `barresH`
  (barres en CSS dans un tableau). Pas de CDN.
- `pick()` tolère un colId renommé ; `versDate()` accepte une date en texte `AAAA-MM-JJ` comme en
  secondes depuis l'époque, au cas où la colonne serait passée en type Date.
- Seuils métier en tête de script : `SEUIL_RECURRENCE` (3 épisodes), `SEUIL_ANCIEN_JOURS` (60),
  `SEUIL_ECART_PCT` (25 %) et `PART_MIN_MOIS` (0,5).

Le jeu de démonstration embarqué utilise des sites inventés (Gymnase des Tilleuls, Parc de la
Fontaine, École du Vieux Chêne…), des contrats en `9001xx` et des engagements en `E10000xx`. Aucun
ne correspond à un point réel. Ne jamais y substituer le référentiel de la commune.

Les scripts d'alimentation du document vivent ailleurs, hors de ce dépôt, dans le dossier de
travail `Suivi 2026 - fluides/eau/grist/` : ils ne doivent pas être committés ici, ils contiennent
des chemins et des références réelles.
