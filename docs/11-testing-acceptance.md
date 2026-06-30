# Tests et critères d'acceptation

## Objectif

Garantir que Stable Enhanced est fiable, sécurisé et utilisable dans le contexte d'une écurie.

## Pyramide de tests

1. Tests unitaires métier dans `packages/core`.
2. Tests de contrats API dans `packages/api`.
3. Tests RLS et migrations Supabase.
4. Tests composants mobile/dashboard.
5. Tests E2E parcours critiques.
6. Tests manuels sur iPhone réel avant release.

## Commandes attendues

```bash
pnpm lint
pnpm typecheck
pnpm test
pnpm test:db
pnpm test:e2e
```

## Tests unitaires métier

### Chevaux

- Calcul âge depuis date de naissance.
- Fallback âge approximatif.
- Priorité badges critiques.
- Statut actif/archivé.

### Soins

- Calcul statut `planned`, `due_soon`, `overdue`.
- Récurrence après soin marqué fait.
- Snooze.
- Génération prochaine notification.
- Badge soin dû / retard.

### Concours

- Génération programme J-30.
- Calcul dates relatives.
- Tâches critiques.
- Badge prêt concours seulement après validation.

### Reproduction

- Statut DG+.
- Proposition date poulinage prévisionnelle.
- Historique cycles.

### Achats

- Statut achat ouvert/acheté/annulé.
- Seuil inventaire.

## Tests RLS obligatoires

Créer deux utilisateurs : A et B.

Créer deux écuries : Stable A et Stable B.

Scénarios :

- A lit ses chevaux.
- A ne lit pas chevaux de B.
- A ne modifie pas soins de B.
- Viewer lit données autorisées mais n'écrit pas.
- Care manager crée soins mais ne modifie pas rôles.
- Trainer crée sorties mais ne modifie pas soins sensibles.
- Admin ne peut pas retirer owner si règle MVP.
- Membre désactivé ne lit plus rien.

## Tests API

### Auth

- `createStable` crée stable + owner.
- `inviteMember` refuse un viewer.
- `acceptInvitation` refuse token expiré.
- `acceptInvitation` refuse token déjà utilisé.

### Chevaux

- `createHorse` refuse nom vide.
- `createHorse` crée badges initiaux.
- `archiveHorse` écrit audit.

### Soins

- `createCareReminder` refuse cheval hors écurie.
- `completeCareReminder` crée historique.
- `completeCareReminder` crée prochain rappel si récurrent.
- Notification liée à rappel créé.

### Concours

- `generateCompetitionPrepPlan` crée tâches attendues.
- Un trainer peut générer un plan.
- Un viewer ne peut pas générer un plan.

## Tests composants UI

### Mobile

- Carte cheval avec photo.
- Carte cheval sans photo.
- Badge critique visible.
- Liste vide chevaux.
- État chargement.
- État erreur.
- État offline.
- Filtre groupe.
- Filtre badge.
- Bouton `Marquer fait`.

### Dashboard

- KPIs avec données.
- KPIs sans données.
- Accès refusé.
- Tableau chevaux.
- Calendrier soins.
- Filtres.

## Tests E2E MVP

### Parcours onboarding

1. L'utilisateur se connecte.
2. Il crée son profil.
3. Il crée une écurie.
4. Il arrive sur dashboard vide.

### Parcours cheval

1. Créer cheval.
2. Ajouter photo.
3. Ajouter badge.
4. Ajouter groupe.
5. Vérifier carte.

### Parcours soin

1. Créer rappel maréchal.
2. Voir rappel dans liste soins.
3. Marquer fait.
4. Voir historique.
5. Voir prochain rappel si récurrent.

### Parcours invitation

1. Owner invite viewer.
2. Viewer accepte.
3. Viewer consulte cheval.
4. Viewer ne peut pas modifier.

### Parcours concours

1. Créer concours.
2. Associer cheval.
3. Générer programme.
4. Marquer tâche faite.
5. Ajouter résultat.

## Tests notifications

- Token push enregistré.
- Notification planifiée.
- Envoi simulé OK.
- Échec enregistré.
- Membre désactivé ignoré.
- Contenu push sans détail médical sensible.

## Tests accessibilité

- Tous les boutons icônes ont un label.
- Contraste palette vérifié.
- Navigation clavier dashboard.
- Zones tactiles mobile minimum 44 px.
- Les badges ne reposent pas uniquement sur la couleur.

## Tests performance MVP

Objectifs indicatifs :

- Liste 100 chevaux fluide sur iPhone récent.
- Dashboard charge KPIs en moins de 2 secondes sur données raisonnables.
- Requêtes filtrées par `stable_id` indexées.
- Images chevaux optimisées.

## Critères d'acceptation produit MVP

### Auth / écurie

- Un utilisateur peut créer une écurie après connexion.
- Un utilisateur peut être membre de plusieurs écuries, même si le sélecteur multi-écurie peut être simple au MVP.
- Un owner peut inviter un membre.

### Chevaux

- Une carte cheval contient photo, nom, âge, sexe, badges, groupe.
- Un cheval peut être filtré par badge.
- Un cheval peut être groupé par bâtiment/localisation.

### Soins

- Un soin peut être planifié.
- Un rappel peut être reçu.
- Un soin peut être marqué fait.
- L'historique reste visible.

### Achats

- Un achat peut être créé pour un cheval ou l'écurie.
- Un achat peut être marqué acheté.

### Concours

- Un concours manuel peut être créé.
- Un programme générique peut être généré.
- Une sortie/résultat est historisée.

### Dashboard

- Les KPIs principaux s'affichent.
- Les données sont limitées à l'écurie courante.

## Critères de non-régression

Aucune PR ne doit casser :

- isolation RLS ;
- onboarding ;
- liste chevaux ;
- création rappel ;
- marquer soin fait ;
- rôle viewer lecture seule.

## Checklist PR

- [ ] Typecheck OK.
- [ ] Lint OK.
- [ ] Tests unitaires OK.
- [ ] Tests RLS si tables/policies modifiées.
- [ ] Tests UI si composant modifié.
- [ ] Mobile iPhone vérifié ou capture fournie.
- [ ] Documentation mise à jour.
- [ ] Pas de secret ajouté.
- [ ] Audit log ajouté si action sensible.
