# ADR 0001 — Stack initiale Stable Enhanced

## Statut

Accepté.

## Date

2026-06-30.

## Contexte

Stable Enhanced doit couvrir :

- iPhone prioritaire ;
- Android ;
- dashboard PC ;
- auth Google et Apple / iCloud ;
- multi-écurie avec rôles ;
- données sensibles de soins ;
- notifications ;
- intégrations IFCE / FFE préparées.

Le projet doit être réalisable par agents autonomes avec le moins de friction possible.

## Décision

Utiliser :

- pnpm workspaces + Turborepo ;
- TypeScript strict ;
- Expo + React Native + Expo Router pour mobile ;
- Next.js App Router pour dashboard PC ;
- Supabase Postgres/Auth/RLS/Storage/Edge Functions pour backend ;
- packages partagés `core`, `design-tokens`, `db`, `api`.

## Raisons

- Expo accélère le développement iOS/Android et reste adapté à une priorité iPhone.
- Expo Router simplifie les routes et deep links.
- Next.js est adapté au dashboard PC et aux pages admin.
- Supabase fournit auth, Postgres, RLS, storage et fonctions serveur sans construire un backend complet dès le MVP.
- TypeScript partagé limite les divergences entre mobile, dashboard et backend.

## Conséquences positives

- MVP plus rapide.
- Sécurité multi-tenant portée par RLS.
- Moins de duplication métier.
- Agents capables de travailler module par module.
- Possibilité d'évoluer vers backend plus spécialisé si nécessaire.

## Conséquences négatives

- Supabase impose une discipline RLS stricte.
- Expo nécessite des builds réels pour tester certaines capacités natives.
- Apple Sign In réel dépend d'un compte Apple Developer.
- Les Edge Functions ne doivent pas devenir un backend désorganisé.

## Alternatives rejetées

### React Native CLI pur

Rejeté car plus coûteux à maintenir pour un MVP multi-plateforme.

### Backend NestJS dédié dès le départ

Rejeté car plus long à mettre en place. Peut revenir plus tard si besoin métier fort.

### Web responsive unique

Rejeté car les notifications, photos, offline et l'usage terrain favorisent une vraie app mobile.

### Flutter

Possible techniquement, mais moins cohérent avec Next.js + TypeScript partagé.

## Critères de révision

Réviser cette ADR si :

- Supabase ne répond plus aux exigences RLS/performance ;
- Expo bloque une capacité native critique ;
- le dashboard devient l'interface principale ;
- l'équipe change fortement de compétences.
