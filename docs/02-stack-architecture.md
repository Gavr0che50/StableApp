# Stack et architecture

## Décision de stack

Stable Enhanced est un produit mobile-first avec un dashboard PC. La stack doit réduire la duplication, accélérer le MVP et rester maintenable par des agents.

## Stack retenue

### Monorepo

- `pnpm` pour la gestion de packages.
- `Turborepo` pour orchestrer build, lint, tests, typecheck.
- TypeScript strict partout.

### Mobile

- Expo SDK courant.
- React Native.
- Expo Router pour navigation file-based iOS, Android et web si nécessaire.
- `expo-notifications` pour notifications push/locales.
- `expo-image-picker` pour photos chevaux.
- `expo-secure-store` pour stockage local sensible minimal.
- `@tanstack/react-query` pour cache serveur et synchronisation.

### Dashboard PC

- Next.js App Router.
- Tailwind CSS.
- Composants dashboard accessibles.
- Recharts ou Tremor-like charts pour KPIs simples.
- Auth Supabase côté web.

### Backend

- Supabase Postgres.
- Supabase Auth pour Google et Apple.
- Row Level Security obligatoire.
- Supabase Storage pour photos et documents.
- Supabase Edge Functions pour logique serveur : invitations, notifications, sync externe.
- Supabase Cron pour rappels.

## Pourquoi cette stack

- Expo Router donne une navigation commune mobile/web et des routes typées.
- Supabase évite de construire un backend complet au départ tout en gardant Postgres, SQL, RLS et fonctions serveur.
- Next.js est adapté au dashboard PC, aux vues analytiques et aux pages admin.
- TypeScript partagé permet de centraliser règles métier et contrats.

## Architecture logique

```txt
Utilisateur iPhone/Android
        |
        v
apps/mobile -- Supabase client sécurisé -- Supabase Auth/Postgres/Storage
        |                         |
        |                         v
        |                  Edge Functions
        |                         |
        v                         v
packages/core            Cron notifications / sync externe

Utilisateur PC
        |
        v
apps/dashboard -- Supabase SSR/client -- mêmes tables et politiques RLS
```

## Structure cible du dépôt

```txt
apps/
  mobile/
    app/                 # Expo Router routes
    src/components/
    src/features/
    src/lib/
    assets/
  dashboard/
    app/                 # Next.js App Router
    src/components/
    src/features/
    src/lib/
packages/
  core/
    src/horse/
    src/care/
    src/competition/
    src/breeding/
    src/inventory/
  design-tokens/
    src/colors.ts
    src/spacing.ts
    src/typography.ts
  db/
    src/types.ts         # generated Supabase types
    src/rls-helpers.ts
  api/
    src/contracts.ts
    src/errors.ts
supabase/
  migrations/
  functions/
  seed.sql
docs/
.github/
```

## Packages partagés

### `packages/core`

Contient la logique métier pure :

- Calcul âge à partir de date de naissance.
- Calcul échéances de soin.
- Génération programme pré-concours.
- Calcul date théorique de poulinage.
- Badges automatiques : soin en retard, DG+, prêt concours.

Aucune dépendance UI ou Supabase directe.

### `packages/design-tokens`

Contient les constantes UI :

- Couleurs vert/blanc.
- Rayons.
- Espacements.
- Tailles de cartes.
- Sémantique des badges.

### `packages/db`

Contient :

- Types générés Supabase.
- Helpers pour requêtes filtrées par `stable_id`.
- Validation Zod des payloads.

### `packages/api`

Contient :

- Contrats de mutations.
- Codes d'erreurs.
- Types partagés mobile/dashboard/Edge Functions.

## Modules applicatifs

### Auth et écurie

- Connexion Google ou Apple.
- Création de profil.
- Création d'écurie.
- Invitation membre.
- Gestion des rôles.

### Chevaux

- Liste cartes.
- Détail cheval.
- Photos.
- Badges.
- Groupes.
- Antécédents.

### Soins

- Fiches soins.
- Rappels.
- Notifications.
- Historique.
- Documents.

### Achats

- Catalogue courant.
- Liste d'achats.
- Inventaire simple.
- Seuils futurs.

### Sport / concours

- Sorties.
- Concours.
- Programmes de préparation.
- Résultats.
- Import FFE si légalement disponible.

### Reproduction

- Cycles poulinières.
- Saillies / IA.
- DG+.
- Gestation.
- Poulinage.

### Dashboard

- KPIs.
- Alertes.
- Filtres par groupe/badge.
- Vue calendrier.

## Flux de données

### Lecture standard

1. Client obtient session Supabase.
2. Client lit les tables exposées.
3. RLS vérifie l'appartenance à l'écurie.
4. Le client affiche données filtrées.

### Mutation standard

1. Client valide via Zod.
2. Client appelle mutation Supabase ou Edge Function.
3. RLS ou fonction vérifie rôle.
4. Mutation écrit un audit log.
5. Cache React Query est invalidé.

### Notification

1. Un soin crée ou met à jour un rappel.
2. Un job planifié détecte les rappels à notifier.
3. Edge Function envoie push Expo.
4. L'envoi est journalisé.
5. L'utilisateur peut marquer fait, snooze ou réassigner.

## Offline-first

Le MVP doit fonctionner avec réseau intermittent :

- Liste chevaux et soins récemment consultés en cache.
- Création de note locale possible avec file d'attente.
- Résolution de conflit simple : dernier write gagne avec audit.
- Les actions critiques affichent clairement l'état `en attente de synchronisation`.

Le mode offline complet avancé est post-MVP.

## Sécurité architecture

- RLS activé sur toutes les tables métier.
- Buckets Storage privés.
- URLs signées limitées dans le temps.
- Edge Functions avec service role uniquement côté serveur.
- Pas de service role dans les apps.
- Audit log pour invitations, rôles, suppression, soins sensibles.

## Environnements

- `local` : Supabase CLI + seed anonymisé.
- `preview` : branche PR, base dédiée ou schéma dédié.
- `staging` : validation interne.
- `production` : données réelles.

## ADR initial

Créer `docs/adr/0001-stack.md` au moment du scaffolding avec cette décision comme base.

## Références

- Expo Router : https://docs.expo.dev/router/introduction/
- Next.js : https://nextjs.org/docs
- Supabase Auth : https://supabase.com/docs/guides/auth
- Supabase RLS : https://supabase.com/docs/guides/database/postgres/row-level-security
- Turborepo : https://turborepo.dev/docs
