# Guide agents autonomes — Stable Enhanced

Ce fichier est la source d'instructions pour tout agent de code travaillant dans ce dépôt.

## Mission

Construire Stable Enhanced, application de gestion de cheptel équin pour écuries, avec priorité iPhone, compatibilité Android et dashboard PC.

L'agent doit être capable de travailler à partir de cette documentation sans poser de questions sauf blocage réel de sécurité, juridique ou accès externe.

## Ordre de lecture obligatoire

1. `README.md`
2. `docs/00-index.md`
3. `docs/01-product-brief.md`
4. `docs/02-stack-architecture.md`
5. `docs/03-data-model.md`
6. `docs/04-auth-security.md`
7. Le document fonctionnel lié à la tâche courante
8. `docs/11-testing-acceptance.md`
9. `docs/13-agent-task-list.md`

## Stack contractuelle

- TypeScript partout.
- Monorepo pnpm + Turborepo.
- Mobile : Expo + React Native + Expo Router.
- Web dashboard : Next.js App Router.
- Backend : Supabase Postgres, Auth, RLS, Storage, Edge Functions.
- État serveur : TanStack Query ou client Supabase typé.
- Design : tokens partagés vert/blanc + composants accessibles.

Toute divergence importante doit être consignée dans un ADR sous `docs/adr/`.

## Structure cible

```txt
apps/
  mobile/              # Expo iOS/Android, priorité iPhone
  dashboard/           # Next.js dashboard PC
packages/
  core/                # logique métier pure
  design-tokens/       # palette, spacing, radius, typographie
  db/                  # types générés Supabase, helpers RLS-safe
  api/                 # clients API et contrats partagés
supabase/
  migrations/          # SQL versionné
  functions/           # Edge Functions
  seed.sql             # données dev anonymisées
docs/
  adr/                 # décisions d'architecture
.github/
  workflows/           # CI
```

## Commandes obligatoires

Les scripts suivants doivent exister à la fin du scaffolding :

```bash
pnpm dev
pnpm dev:mobile
pnpm dev:dashboard
pnpm lint
pnpm typecheck
pnpm test
pnpm test:e2e
pnpm supabase:start
pnpm supabase:migrate
pnpm supabase:types
```

## Règles de code

- Pas de JavaScript non typé sauf configuration impossible autrement.
- Pas de secrets dans le dépôt.
- Pas de requêtes directes multi-tenant sans filtre `stable_id`.
- Pas de désactivation de RLS en environnement applicatif.
- Pas de scraping d'IFCE ou FFE sans validation CGU et stratégie documentée.
- Les fonctions métier doivent être testables sans UI.
- Les calculs d'âge, rappels, cycles et préparation concours doivent vivre dans `packages/core`.

## Règles produit non négociables

- L'identité visuelle est verte et blanche, sobre, lisible en extérieur.
- Une carte cheval doit pouvoir être comprise en moins de 3 secondes.
- Les soins en retard ou proches doivent être visibles sans navigation profonde.
- La photo principale sert d'identification visuelle de la carte.
- Les badges doivent filtrer, trier et alerter.
- Les poulinières DG+ doivent être visibles immédiatement.
- Le dashboard PC est macro : il ne remplace pas l'usage mobile quotidien.

## Sécurité et rôles

Rôles minimum :

1. `owner` : propriétaire de l'écurie, tous droits, gestion facturation future.
2. `admin` : gestion complète hors suppression propriétaire.
3. `care_manager` : soins, rappels, historique médical, achats santé.
4. `trainer` : sport, concours, sorties, programme de préparation.
5. `member` : lecture et saisie limitée selon modules.
6. `viewer` : lecture seule.

Tous les accès doivent passer par une appartenance active dans `stable_members`.

## Définition of Done

Une tâche est terminée seulement si :

- Le code compile en TypeScript.
- Les tests liés passent.
- Les politiques RLS couvrent les nouvelles tables.
- Le parcours mobile iPhone est priorisé.
- Les états vide, chargement, erreur et offline sont traités si UI.
- La documentation concernée est mise à jour.
- Le changelog de la PR décrit les impacts produit et sécurité.

## Stratégie de branches

- `main` doit rester stable.
- Utiliser des branches courtes : `feature/auth-stables`, `feature/horse-cards`, etc.
- Une PR ne doit traiter qu'un module cohérent.

## Interdictions

- Stocker des mots de passe IFCE / FFE.
- Contourner un accès authentifié externe.
- Créer une fonctionnalité médicale de diagnostic.
- Supprimer un cheval sans soft-delete et audit.
- Exposer des données d'une autre écurie via dashboard ou API.

## Priorité de développement

1. Scaffolding monorepo.
2. Supabase local + migrations + types.
3. Auth Google / Apple + création d'écurie.
4. Cartes chevaux et groupes.
5. Soins et notifications.
6. Achats et inventaire.
7. Concours et sorties.
8. Poulinières et cycles.
9. Dashboard PC.
10. Intégrations IFCE / FFE.

## Quand documenter une décision

Créer un ADR si la décision touche :

- Stack ou framework.
- Modèle de données.
- Sécurité, RLS ou rôles.
- Intégration externe.
- Offline-first.
- Notifications critiques.
- Déploiement ou coûts récurrents.
