# Stable Enhanced

Stable Enhanced est une application de gestion de cheptel équin pour écuries, pensée mobile-first avec priorité iPhone, compatibilité Android, et dashboard PC pour la vision macro.

## Objectif produit

L'application doit permettre de gérer individuellement les chevaux d'une écurie, de les regrouper par bâtiment, localisation ou rôle, de suivre les soins, les sorties, les achats, les cycles des poulinières, les programmes de concours et les données sportives.

## Stack retenue

- **Monorepo TypeScript** : pnpm workspaces + Turborepo.
- **Mobile iOS/Android** : Expo + React Native + Expo Router, priorité UX iPhone.
- **Dashboard PC** : Next.js App Router + Tailwind CSS + composants dashboard.
- **Backend** : Supabase Postgres, Auth, Row Level Security, Storage, Edge Functions, Cron.
- **Notifications** : expo-notifications côté mobile + jobs serveur pour rappels récurrents.
- **Données externes** : intégrations IFCE / Info Chevaux et FFE Compet conçues comme connecteurs légaux, traçables et remplaçables par API officielle si disponible.

## Modules fonctionnels

1. Authentification Google et Apple / iCloud, création d'écurie, invitation de membres et niveaux de sécurité.
2. Cartes chevaux individuelles avec photo d'identification, âge, taille, poids, sexe, antécédents, fiche de soin et badges.
3. Badges différenciateurs : étalon, poulinière, sportif, à vendre, DG+, soin urgent, blessé, prêt concours.
4. Groupes de chevaux par bâtiment, localisation, rôle, badge ou groupe personnalisé.
5. Fiches de soin avec rappels dentiste, ostéo, maréchal, vétérinaire, vaccin, vermifuge et soins libres.
6. Suivi sportif : historique des sorties, préparation concours, résultats, import ou consultation FFE Compet.
7. Poulinières : cycles, saillie / IA, échographie, DG+, gestation, poulinage.
8. Achats et inventaire : couverture, licol, granulés, pharmacie, matériel courant et liste d'achats.
9. Dashboard PC : vue macro des soins à venir, alertes, effectif, groupes, concours, dépenses et stocks.

## Documentation disponible

- `AGENTS.md` : règles de travail autonomes pour agents de code.
- `docs/00-index.md` : index global de la documentation.
- `docs/01-product-brief.md` : vision produit, personas, périmètre MVP.
- `docs/02-stack-architecture.md` : architecture, choix techniques, structure repo.
- `docs/03-data-model.md` : modèle métier et schéma relationnel cible.
- `docs/04-auth-security.md` : auth, rôles, RLS, sécurité, audit, RGPD.
- `docs/05-ux-ui.md` : palette vert/blanc, navigation, composants, accessibilité.
- `docs/06-care-notifications.md` : fiches de soin, rappels, notifications.
- `docs/07-competitions-sport.md` : concours, planning de préparation, sorties sportives.
- `docs/08-integrations-ifce-ffe.md` : stratégie IFCE / FFE Compet et contraintes légales.
- `docs/09-api-contracts.md` : contrats d'API et Edge Functions.
- `docs/10-backlog-roadmap.md` : roadmap agent-ready et découpage MVP.
- `docs/11-testing-acceptance.md` : critères d'acceptation, QA, tests.
- `docs/12-setup-devops.md` : setup local, environnements, CI/CD, release.
- `docs/13-agent-task-list.md` : tâches autonomes prêtes à exécuter.

## Commandes cibles

```bash
pnpm install
pnpm dev
pnpm test
pnpm lint
pnpm typecheck
pnpm supabase:start
pnpm supabase:migrate
```

Ces commandes sont contractuelles pour les agents : si elles ne fonctionnent pas encore, la première tâche de scaffolding consiste à les créer.

## Principes non négociables

- L'expérience iPhone passe avant le dashboard PC.
- Aucune donnée d'une autre écurie ne doit être lisible via API ou base de données.
- Les notifications de soin doivent être fiables, auditables et modifiables.
- Les intégrations IFCE / FFE doivent respecter les CGU, la protection des données et l'accès authentifié éventuel.
- L'application ne remplace pas un vétérinaire : elle organise l'information, les rappels et l'historique.

## Références techniques

- Expo Router : https://docs.expo.dev/router/introduction/
- Expo Apple Authentication : https://docs.expo.dev/versions/latest/sdk/apple-authentication/
- Supabase Auth Social Login : https://supabase.com/docs/guides/auth/social-login
- Supabase RLS : https://supabase.com/docs/guides/database/postgres/row-level-security
- Next.js Docs : https://nextjs.org/docs
- Turborepo Docs : https://turborepo.dev/docs
