# Index documentation — Stable Enhanced

Ce dossier contient la documentation de référence pour construire l'application de façon autonome.

## Lecture rapide par rôle

### Agent product / UX

1. `01-product-brief.md`
2. `05-ux-ui.md`
3. `06-care-notifications.md`
4. `07-competitions-sport.md`
5. `10-backlog-roadmap.md`

### Agent frontend mobile

1. `02-stack-architecture.md`
2. `05-ux-ui.md`
3. `06-care-notifications.md`
4. `07-competitions-sport.md`
5. `11-testing-acceptance.md`

### Agent backend / Supabase

1. `03-data-model.md`
2. `04-auth-security.md`
3. `08-integrations-ifce-ffe.md`
4. `09-api-contracts.md`
5. `12-setup-devops.md`

### Agent dashboard PC

1. `01-product-brief.md`
2. `02-stack-architecture.md`
3. `03-data-model.md`
4. `05-ux-ui.md`
5. `10-backlog-roadmap.md`

## Documents

| Fichier | Rôle |
| --- | --- |
| `01-product-brief.md` | Vision, personas, périmètre, MVP. |
| `02-stack-architecture.md` | Stack, monorepo, architecture mobile/web/backend. |
| `03-data-model.md` | Entités, relations, tables, événements, données externes. |
| `04-auth-security.md` | Auth Google/Apple, écuries, membres, RLS, RGPD. |
| `05-ux-ui.md` | Palette vert/blanc, navigation iPhone, composants clés. |
| `06-care-notifications.md` | Fiches de soin, échéances, récurrences, notifications. |
| `07-competitions-sport.md` | Concours, planning pré-concours, sorties, suivi sportif. |
| `08-integrations-ifce-ffe.md` | IFCE Info Chevaux, FFE Compet, import légal et limites. |
| `09-api-contracts.md` | Contrats API, mutations, Edge Functions, erreurs. |
| `10-backlog-roadmap.md` | Roadmap et découpage MVP par lots. |
| `11-testing-acceptance.md` | Tests, critères d'acceptation, qualité. |
| `12-setup-devops.md` | Installation, environnements, CI/CD, releases. |
| `13-agent-task-list.md` | Tâches prêtes pour agents autonomes. |

## Principes de documentation

- La documentation prime sur les hypothèses de code.
- Toute fonctionnalité nouvelle doit pointer vers un document existant ou créer une section.
- Les décisions structurantes doivent être ajoutées sous `docs/adr/`.
- Les données vétérinaires et personnelles doivent rester traitées comme sensibles.

## MVP synthétique

Le MVP doit permettre :

1. Connexion Google ou Apple.
2. Création d'une écurie.
3. Invitation de membres avec rôles.
4. Création de chevaux avec photo et badges.
5. Groupes de chevaux.
6. Fiche de soin et rappels.
7. Notifications de soin.
8. Liste d'achats simple.
9. Historique de sorties.
10. Dashboard PC macro.

## Hors MVP mais préparé

- Synchronisation IFCE / FFE automatisée.
- Gestion avancée des stocks.
- Programmes de concours personnalisés par IA.
- Gestion financière complète.
- Mode multi-sites avancé.
- Export comptable.
