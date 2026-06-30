# Setup, environnements et DevOps

## Objectif

Donner aux agents une cible claire pour installer, développer, tester et livrer Stable Enhanced.

## Prérequis locaux

- Node.js LTS courant.
- pnpm.
- Git.
- Supabase CLI.
- Expo CLI via `npx expo`.
- Xcode pour builds iOS.
- Android Studio pour builds Android.
- Docker pour Supabase local.

## Initialisation cible

```bash
pnpm install
pnpm supabase:start
pnpm supabase:migrate
pnpm supabase:types
pnpm dev
```

## Scripts package racine attendus

```json
{
  "scripts": {
    "dev": "turbo dev",
    "dev:mobile": "pnpm --filter mobile dev",
    "dev:dashboard": "pnpm --filter dashboard dev",
    "build": "turbo build",
    "lint": "turbo lint",
    "typecheck": "turbo typecheck",
    "test": "turbo test",
    "test:db": "pnpm --filter db test",
    "test:e2e": "turbo test:e2e",
    "supabase:start": "supabase start",
    "supabase:stop": "supabase stop",
    "supabase:migrate": "supabase db reset",
    "supabase:types": "supabase gen types typescript --local > packages/db/src/database.types.ts"
  }
}
```

## Environnements

### Local

Usage : développement agent.

- Supabase local.
- Données seed anonymisées.
- Notifications simulées par défaut.
- OAuth peut être mocké si credentials absents.

### Preview

Usage : PR.

- Déploiement dashboard preview.
- Build mobile non publié.
- Base preview ou projet Supabase dédié si disponible.

### Staging

Usage : recette.

- OAuth réel.
- Notifications réelles sur devices de test.
- Données de test non personnelles.

### Production

Usage : utilisateurs réels.

- RLS strict.
- Backups.
- Logs sans données sensibles.
- Monitoring erreurs.

## Variables d'environnement

### Mobile

```bash
EXPO_PUBLIC_SUPABASE_URL=
EXPO_PUBLIC_SUPABASE_ANON_KEY=
EXPO_PUBLIC_APP_ENV=local
```

### Dashboard

```bash
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
APP_ENV=local
```

`SUPABASE_SERVICE_ROLE_KEY` ne doit jamais être exposée au client.

### Edge Functions

```bash
SUPABASE_URL=
SUPABASE_SERVICE_ROLE_KEY=
EXPO_ACCESS_TOKEN=
```

## CI GitHub Actions cible

Jobs :

1. Install.
2. Lint.
3. Typecheck.
4. Unit tests.
5. DB migration check.
6. Build dashboard.
7. Expo config check.

## Exemple workflow

```yaml
name: CI
on:
  pull_request:
  push:
    branches: [main]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: pnpm
      - run: pnpm install --frozen-lockfile
      - run: pnpm lint
      - run: pnpm typecheck
      - run: pnpm test
```

À adapter selon version Node LTS retenue au scaffolding.

## Supabase

### Migrations

- Toute modification de schema passe par `supabase/migrations`.
- Les migrations doivent être idempotentes autant que possible.
- RLS doit être ajoutée dans la même PR que la table.
- Les tests RLS accompagnent les tables sensibles.

### Types

Après migration :

```bash
pnpm supabase:types
```

Committer les types générés si l'équipe choisit cette stratégie.

### Seed

`supabase/seed.sql` doit contenir :

- deux profils fictifs ;
- deux écuries ;
- quelques chevaux ;
- soins ;
- groupes ;
- achats ;
- concours.

Jamais de données réelles.

## Mobile releases

### Dev

- Expo development build si modules natifs nécessaires.
- Expo Go possible au tout début.

### iOS

- Apple Developer requis pour Sign in with Apple réel.
- EAS Build recommandé.
- Tester notifications sur appareil physique.

### Android

- EAS Build recommandé.
- Tester deep links et notifications.

## Dashboard deployment

Recommandé : Vercel ou plateforme compatible Next.js.

Contraintes :

- Variables d'environnement séparées par env.
- Pas de service role côté client.
- Headers sécurité.

## Observabilité

Outils à choisir au scaffolding :

- Sentry ou équivalent pour erreurs mobile/web.
- Logs Supabase Edge Functions.
- Audit logs applicatifs.

Ne jamais logger :

- tokens ;
- documents ;
- notes médicales détaillées ;
- données personnelles inutiles.

## Backups

Avant production :

- Backups Supabase activés.
- Restauration testée.
- Export écurie prévu post-MVP.

## Sécurité release

Checklist avant prod :

- RLS activée partout.
- Buckets privés.
- Service role absent des bundles.
- OAuth Google/Apple configuré avec domaines corrects.
- Notifications ne contiennent pas données sensibles.
- Pages dashboard protégées.
- Audit log fonctionnel.

## Qualité mobile

Avant release mobile :

- Test iPhone réel.
- Test Android réel ou émulateur + au moins un device réel.
- Test offline partiel.
- Test upload photo.
- Test notification.
- Test création soin.

## Convention version

Avant lancement public :

- `0.1.0` MVP interne.
- `0.2.0` beta écurie pilote.
- `1.0.0` première version stable.

## Changelog

Créer `CHANGELOG.md` au scaffolding. Chaque release doit mentionner :

- fonctionnalités ;
- migrations ;
- changements sécurité ;
- breaking changes ;
- limites connues.
