# Widgets Grist — Ville du Bouscat

Pages HTML autonomes destinées à être ajoutées dans [Grist](https://grist.numerique.gouv.fr)
comme **vues personnalisées** (« custom widgets »).

## Widgets disponibles

| Widget | Objet | URL à coller dans Grist |
|---|---|---|
| `reconciliation-investissement` | Rapproche l'extraction comptable Grand Angle et le suivi opérationnel des services techniques | `https://ville-du-bouscat.github.io/grist-widgets/reconciliation-investissement/` |
| `dysfonctionnements-synthese` | Tableau de bord, liste de travail et synthèse de comité pour le suivi des dysfonctionnements de la restauration scolaire | `https://ville-du-bouscat.github.io/grist-widgets/dysfonctionnements-synthese/` |
| `suivi-eau-synthese` | Synthèse du suivi de l'eau potable : fuites en attente de vérification, récurrences, consommations relevées du parc communal | `https://ville-du-bouscat.github.io/grist-widgets/suivi-eau-synthese/` |

Ouverts hors de Grist, ces widgets affichent un jeu de démonstration **fictif** — voir l'avertissement ci-dessous.

### `dysfonctionnements-synthese`

Vue de lecture du document « Suivi dysfonctionnements restauration ». Trois onglets, un sélecteur
d'année scolaire commun :

- **Tableau de bord** — signalements, non résolus, retards, critiques, délai moyen, taux de
  résolution ; répartition par mois, par catégorie, par office, par gravité et par statut.
- **Suivi** — la liste de travail, triée par gravité puis par ancienneté, et le repérage des
  récurrences (même office et même catégorie au moins trois fois).
- **Comité de suivi** — une synthèse rédigée et imprimable sur l'année, le trimestre ou le mois.

Le widget **ne connaît pas l'identité de l'agent et n'en a pas besoin** : les règles d'accès du
document filtrent déjà les lignes qui lui parviennent. Un responsable d'office n'y voit donc que
son office, sans qu'aucun filtre ne soit à poser dans la page.

L'année scolaire est lue dans la colonne `Annee_scolaire` quand elle est renseignée, et recalculée
depuis la date du constat sinon — bascule au 1er septembre. La nouvelle année scolaire est proposée
dans le sélecteur même lorsqu'elle est vide : c'est l'espace vierge de la rentrée.

### `suivi-eau-synthese`

Vue de lecture du document « Suivi de l'eau ». Trois onglets, alimentés par trois tables —
`Points_livraison`, `Consommations` et `Suivi_fuites` :

- **Tableau de bord** — points de livraison et télérelève, consommation de référence, fuites
  ouvertes et volume perdu, épisodes sans vérificateur, points sans télérelève, délai moyen de
  résolution ; consommation mensuelle du parc et volume par engagement comptable.
- **Fuites** — la file d'attente : les épisodes ouverts qu'aucun agent n'a pris, du plus ancien
  au plus récent, puis ceux en cours de traitement, puis les points à récurrence anormale
  (au moins trois épisodes sur le même point).
- **Consommations** — les quinze plus gros consommateurs sur douze mois glissants, comparés à
  leur consommation de référence, et la liste des points sans télérelève, sur lesquels aucune
  alerte n'est possible.
- **Budget** — crédits ouverts, mandaté, engagé, services faits en attente de constatation avec
  leurs intérêts moratoires théoriques, rythme de mandatement année par année, et part de chaque
  exercice payée sur le suivant.

L'onglet **Fuites** est l'écran qui justifie le document. Le chiffre qui compte n'est pas le
volume perdu mais le nombre d'épisodes que personne n'a pris : c'est faute d'avoir refermé cette
boucle que 183 alertes sont restées sans suite. Renseigner « Qui a vérifié » suffit à faire
sortir une ligne de la file.

Le widget tolère qu'une colonne ait été renommée dans le document, et accepte les dates aussi
bien en texte `AAAA-MM-JJ` qu'en type Date.

## ⚠ Ce dépôt est public — règle absolue

GitHub Pages n'est gratuit que sur un **dépôt public**. Tout ce qui est déposé ici est donc
lisible par n'importe qui, indexable, et conservé dans l'historique Git même après suppression.

**Aucune donnée réelle ne doit être committée dans ce dépôt.** Cela vaut pour les données
budgétaires de la commune, mais tout autant pour ce qui les accompagne dans nos fichiers de
travail : prénoms des agents en charge, commentaires d'arbitrage, opérations mentionnées
« pour DM » donc non encore votées, et projets touchant à la sécurité publique.

Le widget livré ici embarque un **jeu de démonstration entièrement inventé**, pour qu'il reste
lisible quand on l'ouvre hors de Grist. Avant tout ajout ou toute mise à jour d'un widget,
vérifier que la page ne contient aucune donnée du document réel.

## Ce que ce dépôt héberge, et ce qu'il n'héberge pas

**Il héberge du code, jamais de la donnée.** Grist transmet les enregistrements au widget
à l'intérieur du navigateur de l'agent, par messagerie interne à la page. Le serveur qui
sert ces fichiers ne reçoit aucune donnée du document — ni budget, ni engagement, ni nom.

C'est ce qui rend l'hébergement public acceptable : la donnée réelle ne transite jamais par
GitHub, elle reste entre Grist et le navigateur de l'agent. Et le code étant lisible, chacun
peut vérifier que les seuls appels du widget à Grist sont des lectures (`fetchTable`).

La réserve de la section précédente porte donc sur une autre chose : ce que nous déposons
nous-mêmes dans les fichiers. C'est là, et seulement là, que le risque existe.

## Pourquoi héberger nous-mêmes plutôt que pointer une URL tierce

Grist n'héberge pas les pages des widgets. Beaucoup de widgets de la communauté vivent donc
sur le compte GitHub d'un particulier. Les utiliser revient à faire dépendre un outil de la
collectivité d'un dépôt que nous ne maîtrisons pas : s'il disparaît ou change de contenu, nos
vues disparaissent ou changent avec lui.

Héberger notre propre copie supprime cette dépendance, et donne une réponse simple à la
question que Grist pose à chaque ajout : « faites-vous confiance à la ressource derrière
cette URL ? »

## Publication

Le dépôt se publie tel quel : les widgets sont des pages statiques, sans build.

0. Placer le fichier `workflow-pages.yml` à l'emplacement `.github/workflows/pages.yml`
   (créer les deux dossiers ; ils commencent par un point, ce qui les rend invisibles
   dans le Finder — utiliser ⌘⇧. pour les afficher, ou passer par le terminal).
1. Dans *Settings → Pages*, choisir **GitHub Actions** comme source.
2. Pousser sur `main`. Le workflow `.github/workflows/pages.yml` publie l'ensemble du dépôt.
3. L'adresse devient `https://ville-du-bouscat.github.io/grist-widgets/<nom-du-widget>/`.

L'organisation `ville-du-bouscat` a été créée le 22 août 2026 : les adresses figurant dans ce
dépôt sont donc déjà les bonnes, il n'y a rien à substituer.

### Pourquoi GitHub et non une forge souveraine

La question a été instruite avant de choisir, elle n'a pas à être rouverte sans élément neuf.

**Le GitLab de l'ADULLACT n'a pas GitLab Pages activé** (vérifié le 22 août 2026, par deux
contrôles concordants : `gitlab.adullact.net/help/instance_configuration` y affiche comme
domaine Pages la valeur par défaut de GitLab, `example.com`, signe qu'elle n'a jamais été
configurée ; et le menu *Déploiement* d'un projet n'y propose aucune entrée *Pages*).

Activer Pages sur une instance auto-hébergée suppose un domaine dédié, des enregistrements DNS
génériques et un certificat : c'est un chantier d'infrastructure, pas une case à cocher. La
voie souveraine reste donc ouverte, mais elle commence par une demande à `support@adullact.org`.

Le jour où elle aboutirait, la migration est simple : le dépôt est identique, seul le fichier
de publication change. Il faudrait alors mettre à jour l'adresse du widget dans Grist (panneau
de droite, champ *URL du widget*) et dans `index.html` — GitHub Pages ne redirige pas.

## Ajouter un widget dans Grist

1. **Ajouter** → **Ajouter une vue à la page**
2. *Choisir la vue* : **Personnalisée** → **Ajouter à la Page**
3. Carte **URL personnalisée** : coller l'adresse → **Ajouter un widget**
4. Lire l'avertissement « vues personnalisées de source inconnue », cocher, **Confirmer**
5. Panneau de droite, onglet **Vue** → **Niveau d'accès** → **Accès complet au document**

Le niveau *« Lire les données source sélectionnées »* ne donne accès qu'à une seule table.
Le widget de réconciliation en lit trois : l'accès complet est le minimum techniquement
possible. **Il n'écrit rien.**

## Structure

```
.
├── index.html                          page d'accueil, liste des widgets
├── reconciliation-investissement/
│   └── index.html                      widget autonome (ouvrable aussi en double-clic)
├── dysfonctionnements-synthese/
│   └── index.html
├── suivi-eau-synthese/
│   └── index.html
└── .github/workflows/pages.yml         publication automatique
```

Le workflow a été livré sous le nom `workflow-pages.yml`, sans son point de tête, parce que le
pont vers le poste de travail refuse d'écrire dans un chemin commençant par un point. Il a été
remis à sa place en créant le dépôt.

Chaque widget est **un fichier unique**, sans dépendance externe. Ouvert hors de Grist, il
affiche un jeu de démonstration ; ouvert dans Grist, il lit les tables en direct. La
bibliothèque `grist-plugin-api.js` est chargée depuis l'instance Grist qui affiche le widget,
jamais depuis un tiers.

## Ajouter un nouveau widget

Créer un dossier à la racine, y placer un `index.html` autonome, et l'ajouter au tableau
ci-dessus ainsi qu'à `index.html`. Rien d'autre.

## Licence

À arbitrer avant publication. Pour du code produit par une collectivité, les choix usuels
sont la **licence MIT** (permissive, simple) ou l'**EUPL-1.2** (licence publique de l'Union
européenne, rédigée en français et pensée pour le secteur public). Déposer le fichier
`LICENSE` correspondant à la racine.
