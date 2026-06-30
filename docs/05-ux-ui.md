# UX/UI — mobile-first vert/blanc

## Objectif UX

Stable Enhanced doit être utilisable rapidement dans une écurie, souvent dehors, avec une main, sur iPhone en priorité. Le dashboard PC sert à comprendre la situation globale, pas à remplacer l'usage mobile.

## Plateformes

### Priorité 1 — iPhone

- Navigation fluide au pouce.
- Grandes zones tactiles.
- Cartes cheval lisibles.
- Actions rapides : soin fait, appel pro, ajout note, photo.

### Priorité 2 — Android

- Même structure que iPhone.
- Respect des safe areas et back button Android.
- Notifications testées séparément.

### Priorité 3 — PC dashboard

- Vue macro.
- Tableaux, filtres, KPIs.
- Pas de workflow quotidien lourd en priorité MVP.

## Palette

### Couleurs principales

| Token | Hex | Usage |
| --- | --- | --- |
| `green.900` | `#123524` | titres forts, headers sombres |
| `green.800` | `#1B5E3A` | primary dark |
| `green.700` | `#2E7D4F` | boutons primaires |
| `green.500` | `#4CAF72` | accent actif |
| `mint.100` | `#E8F5EC` | fonds doux |
| `mint.50` | `#F4FBF6` | fond application |
| `white` | `#FFFFFF` | cartes |
| `sand.100` | `#F6F1E8` | zones secondaires |
| `gold.500` | `#C9A227` | badge premium / concours |
| `red.600` | `#B3261E` | retard / urgent |
| `orange.500` | `#E58A2A` | à venir / attention |
| `blue.600` | `#2F6FDB` | info / import externe |
| `gray.900` | `#18201B` | texte principal |
| `gray.600` | `#65706A` | texte secondaire |
| `gray.200` | `#DDE5DF` | bordures |

### Sémantique

- Vert : action principale, santé normale, validation.
- Blanc : cartes et surfaces de lecture.
- Rouge : retard ou danger.
- Orange : bientôt dû.
- Or : concours / sport / valorisation.
- Bleu : données importées / externes.

## Typographie

- Police système native.
- Titre écran mobile : 28–32 px.
- Titre carte cheval : 18–20 px.
- Métadonnées : 13–14 px.
- Texte long : 15–16 px.

## Layout mobile

### Navigation principale

Bottom tabs MVP :

1. `Chevaux`
2. `Soins`
3. `Concours`
4. `Achats`
5. `Dashboard`

### Header mobile

- Nom écurie.
- Sélecteur groupe/badge.
- Bouton recherche.
- Bouton ajout rapide.

### Actions rapides globales

Bouton `+` contextuel :

- Ajouter cheval.
- Ajouter soin.
- Ajouter sortie.
- Ajouter achat.
- Ajouter groupe.

## Carte cheval

### Informations visibles sur carte compacte

- Photo d'identification.
- Nom.
- Âge ou date naissance.
- Sexe.
- Taille/poids si disponibles.
- Badges principaux.
- Statut prochain soin.
- Groupe/localisation.

### Variantes

#### Carte compacte

Utilisée en liste mobile.

#### Carte détaillée

Utilisée en détail cheval, avec sections :

- identité ;
- antécédents ;
- soins ;
- sorties ;
- concours ;
- achats ;
- reproduction si jument/poulinière ;
- documents.

#### Groupe de cartes

Vue par bâtiment, localisation ou badge.

## Badges

### Badges standards

| Badge | Couleur | Règle |
| --- | --- | --- |
| Étalon | green.800 | manuel ou sexe stallion |
| Poulinière | mint/green | manuel ou reproduction active |
| Sportif | gold | manuel ou concours récents |
| À vendre | blue/green | champ `for_sale` |
| DG+ | gold + green | cycle poulinière positif |
| Soin dû | orange | rappel proche |
| Soin en retard | red | rappel passé |
| Blessé | red | manuel sensible |
| Prêt concours | green | programme complet / validation manuelle |

### Règles UI

- Maximum 4 badges visibles sur carte compacte.
- Les badges critiques passent avant les badges informatifs.
- `DG+`, `Soin en retard`, `Blessé` doivent être immédiatement visibles.

## Écrans mobile MVP

### Écran Chevaux

- Recherche.
- Filtres : groupe, badge, sexe, statut soin.
- Liste cartes.
- Mode grille optionnel.
- CTA ajouter cheval.

### Détail cheval

Tabs ou sections :

1. Résumé.
2. Soins.
3. Sorties.
4. Concours.
5. Achats.
6. Reproduction si applicable.
7. Documents.

### Soins

- Liste par échéance.
- Filtres : aujourd'hui, 7 jours, retard, type, responsable.
- Action rapide `Marquer fait`.
- Snooze.

### Concours

- Concours à venir.
- Programmes pré-concours.
- Historique résultats.

### Achats

- Liste simple par statut.
- Ajout depuis catalogue.
- Affectation cheval optionnelle.

### Dashboard mobile

Vue synthétique :

- Soins en retard.
- Aujourd'hui.
- Concours à venir.
- Achats urgents.
- Poulinières DG+.

## Dashboard PC

### Objectif

Donner une vue macro : décisions, charge de soin, risques, organisation.

### Pages

1. Overview.
2. Chevaux par groupe/badge.
3. Calendrier soins/concours.
4. Achats/inventaire.
5. Reproduction.
6. Membres et rôles.

### KPIs principaux

- Chevaux actifs.
- Soins en retard.
- Soins à 7 jours.
- Concours à 30 jours.
- Achats ouverts.
- Stocks bas.
- Poulinières DG+.

## États UI obligatoires

Chaque écran doit gérer :

- chargement ;
- vide ;
- erreur ;
- offline ;
- accès refusé ;
- synchronisation en attente.

## Accessibilité

- Contraste suffisant en extérieur.
- Zones tactiles minimum 44 px.
- Labels accessibles pour boutons icônes.
- Ne jamais transmettre l'information uniquement par couleur.
- Les badges doivent avoir texte + icône ou libellé.

## Microcopy

### Ton

Professionnel, calme, utile.

### Exemples

- `Soin prévu aujourd'hui`
- `Rappel repoussé`
- `Photo d'identification ajoutée`
- `Donnée importée — à vérifier`
- `Action impossible : rôle insuffisant`
- `Ce cheval a un soin en retard`

## Design tokens TypeScript attendus

Créer dans `packages/design-tokens` :

```ts
export const colors = {
  green900: '#123524',
  green800: '#1B5E3A',
  green700: '#2E7D4F',
  green500: '#4CAF72',
  mint100: '#E8F5EC',
  mint50: '#F4FBF6',
  white: '#FFFFFF',
  sand100: '#F6F1E8',
  gold500: '#C9A227',
  red600: '#B3261E',
  orange500: '#E58A2A',
  blue600: '#2F6FDB',
  gray900: '#18201B',
  gray600: '#65706A',
  gray200: '#DDE5DF',
} as const;
```

## Critères d'acceptation UX

- Sur iPhone, une carte cheval montre photo, nom, badges, prochain soin sans scroll horizontal.
- Un soin peut être marqué fait en moins de 3 taps depuis la liste soins.
- Les chevaux peuvent être filtrés par badge et groupe.
- Un utilisateur viewer voit les actions désactivées avec message clair.
- Le mode sans réseau affiche les données cache et un état de sync.
