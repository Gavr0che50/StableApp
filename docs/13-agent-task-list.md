# Liste de tâches agents autonomes

## Règles

Chaque tâche doit produire : code, tests, documentation mise à jour, et vérification RLS si la base change.

## A0 — Scaffolding monorepo

Livrables :

- `package.json` racine.
- `pnpm-workspace.yaml`.
- `turbo.json`.
- `tsconfig.base.json`.
- `apps/mobile` Expo + Expo Router.
- `apps/dashboard` Next.js App Router.
- `packages/core`, `packages/design-tokens`, `packages/db`, `packages/api`.
- scripts `dev`, `lint`, `typecheck`, `test`.

Critères :

- `pnpm install` fonctionne.
- `pnpm dev:mobile` lance Expo.
- `pnpm dev:dashboard` lance Next.js.
- `pnpm lint`, `pnpm typecheck`, `pnpm test` existent.

## A1 — Supabase et RLS

Livrables :

- Supabase CLI configuré.
- Migrations initiales : `profiles`, `stables`, `stable_members`, `stable_invitations`, `audit_logs`.
- Helpers SQL : membre actif, rôle autorisé, audit.
- Types Supabase générés.
- Seed anonymisé.

Critères :

- Supabase local démarre.
- Deux écuries de test sont isolées par RLS.
- Un membre désactivé ne lit plus les données.

## A2 — Auth Google / Apple et onboarding

Livrables :

- Client Supabase mobile et dashboard.
- Connexion Google.
- Connexion Apple / iCloud côté iOS.
- Création profil.
- Création écurie.
- Création rôle `owner`.
- Logout.

Critères :

- Un nouvel utilisateur crée son écurie.
- Un utilisateur reconnecté revient dans son écurie.
- Apple n'est proposé que si disponible ou correctement configuré.

## A3 — Membres et rôles

Livrables :

- Invitation membre.
- Acceptation invitation.
- Rôles : owner, admin, care_manager, trainer, member, viewer.
- UI dashboard membres.
- Protections UI mobile.

Critères :

- Owner invite un viewer.
- Invitation expirée refusée.
- Viewer ne peut pas modifier.
- Care manager ne gère pas les rôles.

## A4 — Chevaux, cartes, photos, groupes

Livrables :

- Tables chevaux, photos, badges, groupes.
- CRUD cheval.
- Upload photo principale privée.
- Carte compacte et détaillée.
- Badges standards.
- Groupes bâtiment/localisation/rôle/custom.
- Filtres groupe et badge.

Critères :

- Une carte affiche photo, nom, âge, sexe, badges et groupe.
- Un badge critique est visible sans ouvrir la fiche.
- Un cheval archivé reste dans l'historique.

## A5 — Soins et rappels

Livrables :

- Tables `care_records`, `care_reminders`.
- Création rappel.
- Liste soins.
- Marquer fait.
- Snooze.
- Récurrence simple.
- Badges soin dû et soin en retard.

Critères :

- Un care_manager crée un rappel vétérinaire.
- Un viewer ne peut pas créer de rappel.
- Marquer fait crée un historique.
- Un rappel récurrent crée la prochaine échéance.

## A6 — Notifications

Livrables :

- Enregistrement token push.
- Table `notifications`.
- Edge Function `send-due-notifications`.
- Préférences de rappel.
- Logs d'envoi.

Critères :

- Notification planifiée envoyée au bon membre.
- Membre désactivé ignoré.
- Push sans détail médical sensible.

## A7 — Achats et inventaire

Livrables :

- Catalogue : couverture, licol, granulés, pharmacie, matériel.
- Liste d'achats.
- Affectation cheval optionnelle.
- Statuts open, bought, cancelled.
- Inventaire simple.

Critères :

- Ajouter un achat pour un cheval.
- Marquer acheté.
- Dashboard affiche achats ouverts.

## A8 — Sorties, concours, résultats

Livrables :

- Tables sorties et concours.
- Création sortie.
- Création concours manuel.
- Engagement cheval/cavalier.
- Résultat simple.
- Timeline cheval.

Critères :

- Un trainer crée un concours.
- Un résultat apparaît dans l'historique du cheval.
- Les données restent filtrées par écurie.

## A9 — Programme pré-concours

Livrables :

- Générateur J-30 dans `packages/core`.
- Tâches par catégorie : entraînement, soin, matériel, administratif, transport.
- Calendrier.
- Statuts todo, done, skipped.

Critères :

- Générer un programme depuis une date cible.
- Les dates relatives sont correctes.
- Une tâche en retard apparaît sur dashboard.

## A10 — Poulinières et DG+

Livrables :

- Table `mare_cycles`.
- Vue reproduction dans fiche cheval.
- Statuts gestation.
- Badge DG+.
- Date poulinage prévisionnelle modifiable.

Critères :

- Marquer une poulinière DG+.
- Le badge DG+ est visible sur carte.
- Les cycles restent historisés.

## A11 — Dashboard PC

Livrables :

- Overview KPIs.
- Chevaux par groupe/badge.
- Soins retard/prochains.
- Concours à venir.
- Achats ouverts.
- Poulinières DG+.

Critères :

- Dashboard ne montre que l'écurie courante.
- Filtres groupe/badge fonctionnent.
- États vide, chargement et erreur traités.

## A12 — IFCE / FFE manuel

Livrables :

- Table `horse_external_ids`.
- Champs SIRE / IFCE / FFE.
- URL source.
- Statut `needs_review`.
- Audit import manuel.

Critères :

- Aucun mot de passe externe demandé.
- La source externe est visible.
- Un viewer ne lance pas d'import.

## A13 — Qualité et release

Livrables :

- CI GitHub Actions.
- Tests unitaires métier.
- Tests RLS.
- Tests E2E onboarding, cheval, soin.
- CHANGELOG.
- ADR stack.

Critères :

- PR bloquée si lint/typecheck/test échoue.
- Un nouvel agent peut lancer le projet localement.
- Les parcours MVP sont documentés et testés.
