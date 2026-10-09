# Main courante de crise — synthèse (widget Grist)

Widget **en lecture seule** qui présente la main courante d'un épisode de crise de façon lisible pour les élus et les pilotes de cellules : tuiles de synthèse, répartition par état et par cellule, chronologie, dossiers à traiter, journal filtrable, points de situation (SAFER) et un **mode affichage** pour écran mural.

- Un seul fichier HTML, aucune dépendance externe, aucun build.
- Il n'écrit jamais dans le document (seul `fetchTable` est appelé).
- Les données ne quittent pas le navigateur de la personne : l'hébergeur de la page ne reçoit rien du document. Chacun voit ce que ses droits Grist lui permettent de voir.
- Hors de Grist (ouverture directe), la page affiche des **données de démonstration entièrement fictives**.
- Il complète la main courante, il ne la remplace pas : la saisie se fait dans Grist (formulaire et grille natifs) et, en mode dégradé, dans l'outil HTML hors ligne.

## Table attendue

Table `Main_courante` (nom modifiable en tête de fichier ou par `?table=`). Les noms de colonnes sont reconnus sans tenir compte des accents ni de la casse.

| Colonne | Type Grist | Rôle |
|---|---|---|
| Date | Date | jour de l'entrée |
| Heure | Texte | `hh:mm` ; toute autre valeur (« à compléter ») est affichée « —:— » |
| Emetteur | Texte | identité / fonction (affiché dans le journal, jamais en mode affichage) |
| Coordonnees | Texte | **jamais lue ni affichée par le widget** |
| Canal, Lieu | Choix / Texte | |
| Cellule | Choix | Décision-Pilotage, Anticipation, Renseignement, Communication, Intervention — Population / Logistique / Sécurité |
| Information, Action | Texte | |
| Etat | Choix | À traiter, En cours, Traité, Clos sans suite |
| Evt_significatif | Bascule | événement significatif (★) |
| Cloture, Saisi_par | Texte | |
| Episode | Texte | **facultative** : permet de filtrer par épisode (le plus récent est choisi par défaut) |

Table `Parametres` (facultative, première ligne renseignée) : `Evenement`, `Activation`, `Organisation`.

Les points de situation sont des entrées dont l'Information commence par `POINT DE SITUATION` et suit la convention `S: … | A: … | F: … | E: … | R: …`. Les libellés des lettres sont à renseigner par chaque organisation dans `SAFER_LIBELLES` (en tête de fichier) ; par défaut seules les initiales sont affichées.

## Mise en place dans Grist

1. Dans une page du document, ajouter un widget → **Personnalisé** → coller l'URL de la page publiée.
2. Accès demandé : **accès complet au document** (le widget lit deux tables ; il n'écrit rien). Pour un accès minimal, remplacer `requiredAccess:"full"` par `"read table"` et renoncer à la table `Parametres`.
3. Pour les élus : leur donner le rôle **Lecteur** sur le document, ils verront ce widget sans pouvoir modifier la main courante.

Paramètres d'URL facultatifs, à ajouter à l'adresse du widget : `?org=Nom de l'organisation` · `?titre=Titre` · `?episode=Nom exact de l'épisode` (ou `*` pour tous) · `?affichage=1` (démarre en mode écran mural) · `?table=` et `?params=` pour des tables nommées autrement.

## Réutilisation par une autre organisation

Rien n'est propre au Bouscat dans le code : le nom de l'organisation vient de `Parametres.Organisation` ou de `?org=`, les cellules et états se changent dans les constantes en tête de fichier. Trois voies, de la plus simple à la plus autonome :

1. **Utiliser l'URL publiée** (aucun hébergement à prévoir) : coller l'adresse GitHub Pages du dépôt comme widget personnalisé dans le Grist de l'organisation, si son instance autorise les widgets par URL.
2. **Dupliquer le document Grist modèle** (lien de duplication), qui contient déjà la table, le formulaire, les vues et le widget configuré ; l'organisation gère ensuite ses propres droits.
3. **Forker ce dépôt** et publier sa propre copie (GitHub Pages ou serveur interne), à privilégier pour un usage opérationnel : la page ne dépend alors d'aucun tiers, et l'organisation maîtrise ses évolutions.

Réserve à connaître : comme Grist lui-même, le widget a besoin du réseau. Il sert au pilotage et à l'information des élus, jamais de registre de secours (c'est le rôle de l'outil HTML hors ligne).

## Données et vérification

- Dépôt public : **ne jamais y committer de données réelles** (entrées de main courante, noms d'agents, coordonnées). La démonstration est générée dans la page à partir de contenus inventés.
- Avant publication : ouvrir la page en direct (mode démonstration) en thème clair et sombre, console sans erreur, puis dans un document Grist de test.

## Licence

À décider avant diffusion à d'autres organisations (EUPL-1.2 ou MIT suggérées).
