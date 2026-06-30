# Backlog et roadmap

## Objectif

Découper Stable Enhanced en lots réalisables par agents autonomes, avec critères de sortie clairs.

## Phases

### M0 — Documentation et cadrage

Statut : initialisé.

Livrables :

- README.
- AGENTS.md.
- Docs produit, architecture, data, sécurité, UX.
- Roadmap.

### M1 — Scaffolding monorepo

Objectif : créer la structure technique exécutable.

Tâches :

- Initialiser pnpm workspace.
- Configurer Turborepo.
- Créer `apps/mobile` Expo.
- Créer `apps/dashboard` Next.js.
- Créer packages partagés.
- Ajouter ESLint, Prettier, TypeScript strict.
- Ajouter CI GitHub Actions.

Critères de sortie :

- `pnpm install` fonctionne.
- `pnpm lint`, `pnpm typecheck`, `pnpm test` existent.
- Les apps démarrent avec écrans placeholder.

### M2 — Supabase local et modèle initial

Objectif : base de données prête pour auth, écuries, chevaux.

Tâches :

- Configurer Supabase CLI.
- Créer migrations : profiles, stables, stable_members, horses.
- Activer RLS.
- Ajouter fonctions SQL de rôle.
- Générer types TypeScript.
- Ajouter seed dev.

Critères de sortie :

- Supabase local démarre.
- Tests RLS prouvent isolation entre deux écuries.
- Types DB importables dans apps.

### M3 — Auth Google / Apple et création écurie

Objectif : onboarding fonctionnel.

Tâches :

- Configurer Supabase Auth.
- Implémenter écrans mobile login.
- Implémenter auth dashboard.
- Créer profil après login.
- Créer écurie.
- Créer rôle owner.
- Gérer session et logout.

Critères de sortie :

- Un nouvel utilisateur crée une écurie.
- Un utilisateur reconnecté revient dans son écurie.
- Aucun accès sans session.

### M4 — Membres et rôles

Objectif : pré-implanter collaboration sécurisée.

Tâches :

- Table invitations.
- Edge Functions invitation/acceptation.
- UI membres dashboard.
- UI lecture rôle mobile.
- Matrice permissions.

Critères de sortie :

- Owner invite un admin/viewer.
- Invitation expirée refusée.
- Viewer ne peut pas modifier.

### M5 — Cartes chevaux et groupes

Objectif : cœur produit.

Tâches :

- CRUD chevaux.
- Photo principale.
- Badges manuels.
- Groupes bâtiments/localisations/rôles.
- Liste et filtres.
- Détail cheval.

Critères de sortie :

- Une carte affiche photo, nom, âge, sexe, badges, prochain soin placeholder.
- Filtre par groupe et badge.
- Soft-delete cheval.

### M6 — Fiches de soin et rappels

Objectif : suivi soins utilisable.

Tâches :

- Tables care_records/care_reminders.
- UI liste soins.
- UI détail cheval > soins.
- Création rappel.
- Marquer fait.
- Récurrence simple.
- Badges soin dû / retard.

Critères de sortie :

- Créer rappel dentiste.
- Recevoir statut en retard.
- Marquer fait crée historique.
- Rappel récurrent crée prochain rappel.

### M7 — Notifications

Objectif : rappels fiables.

Tâches :

- Enregistrer push token.
- Table notifications.
- Edge Function send-due-notifications.
- Préférences notification.
- Tests d'envoi simulé.

Critères de sortie :

- Notification planifiée créée.
- Notification envoyée seulement à membre actif.
- Push ne contient pas données sensibles.

### M8 — Achats et inventaire simple

Objectif : liste d'achats cheval/écurie.

Tâches :

- Catalogue objets courants.
- Liste achats.
- Statuts open/bought/cancelled.
- Affectation cheval optionnelle.
- Inventaire simple.

Critères de sortie :

- Ajouter couverture pour un cheval.
- Marquer acheté.
- Dashboard affiche achats ouverts.

### M9 — Sorties et concours

Objectif : historique sportif.

Tâches :

- Tables outings, competition_events, entries.
- Création sortie.
- Création concours manuel.
- Résultat simple.
- Timeline cheval.

Critères de sortie :

- Créer concours pour cheval.
- Ajouter résultat.
- Voir historique sorties dans cheval.

### M10 — Programme concours

Objectif : planning pré-concours.

Tâches :

- Générateur template J-30.
- Tâches pré-concours.
- Assignation membre.
- Statuts todo/done/skipped.
- Affichage calendrier.

Critères de sortie :

- Générer programme depuis une date.
- Marquer tâche faite.
- Tâche en retard visible dashboard.

### M11 — Poulinières et DG+

Objectif : reproduction MVP.

Tâches :

- Table mare_cycles.
- UI reproduction si jument/poulinière.
- Statuts gestation.
- Badge DG+.
- Rappels gestation.

Critères de sortie :

- Marquer jument DG+.
- Badge visible sur carte.
- Date poulinage prévisionnelle modifiable.

### M12 — Dashboard PC macro

Objectif : pilotage global.

Tâches :

- Overview KPIs.
- Soins retard/prochains.
- Chevaux par badge/groupe.
- Achats ouverts.
- Concours à venir.
- Poulinières DG+.

Critères de sortie :

- Dashboard affiche données isolées par écurie.
- Filtres groupe/badge.
- États vide/chargement/erreur.

### M13 — IFCE / FFE manuel

Objectif : liens et données externes sans automatisation risquée.

Tâches :

- Champs SIRE/IFCE/FFE.
- Table horse_external_ids.
- Source URL.
- Statut `needs_review`.
- Audit import manuel.

Critères de sortie :

- Ajouter SIRE à cheval.
- Ajouter URL FFE à concours.
- Voir source externe et date de vérification.

### M14 — Sync externe post-validation

Objectif : automatisation seulement si légalement validée.

Tâches :

- Étude CGU/API.
- ADR intégration.
- Adapter isolé.
- Edge Function sync.
- Journalisation.

Critères de sortie :

- Validation écrite avant code.
- Pas de credentials externes stockés.

## Priorité MVP absolue

1. Auth + écurie + rôles.
2. Chevaux + cartes + photos + badges.
3. Soins + rappels.
4. Notifications.
5. Dashboard macro minimal.

## Backlog détaillé par module

### Chevaux

- Ajouter cheval.
- Modifier cheval.
- Archiver cheval.
- Ajouter photo identité.
- Ajouter badges.
- Filtrer par badge.
- Groupes.
- Recherche nom.

### Soins

- Types standards.
- Rappels.
- Récurrence.
- Historique.
- Documents.
- Professionnels.
- Coûts.

### Achats

- Catalogue.
- Liste ouverte.
- Achat par cheval.
- Achat écurie.
- Inventaire.
- Seuils.

### Sport

- Sortie.
- Concours.
- Engagement.
- Résultat.
- Programme.
- Import manuel.

### Reproduction

- Badge poulinière.
- Cycle.
- Saillie/IA.
- DG+.
- Gestation.
- Poulinage.

## Risques planning

- Configuration Apple auth peut dépendre du compte Apple Developer.
- Push notifications nécessitent builds mobiles réels.
- RLS demande tests rigoureux.
- IFCE/FFE automatisé dépend de droits et API.
- Offline complet peut gonfler le périmètre.

## Règle de découpage PR

Une PR doit toucher un domaine fonctionnel principal. Exemple :

- Bon : `feature/horse-cards`.
- Mauvais : `feature/auth-horses-care-dashboard`.

## Definition of Ready

Une tâche est prête si :

- module identifié ;
- rôles concernés connus ;
- tables concernées connues ;
- critères d'acceptation écrits ;
- impact RLS identifié.

## Definition of Done

- Typecheck OK.
- Tests OK.
- RLS OK.
- UX iPhone vérifiée.
- Documentation mise à jour.
- Audit log si action sensible.
