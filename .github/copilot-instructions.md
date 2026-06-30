# Instructions assistant de code — Stable Enhanced

Travaille en français pour les commentaires métier et la documentation, en anglais acceptable pour les noms techniques et l'API.

## Priorités

1. Sécurité multi-écurie.
2. UX iPhone mobile-first.
3. Données de soins fiables.
4. Simplicité de maintenance.
5. Documentation mise à jour.

## Conventions

- TypeScript strict.
- Noms de tables SQL en `snake_case`.
- Noms TypeScript en `camelCase` / `PascalCase`.
- Un cheval est toujours rattaché à une `stable_id`.
- Une action utilisateur sensible doit produire un `audit_log`.
- Les dates doivent être stockées en UTC, affichées en timezone utilisateur.
- Les rappels de soin doivent accepter : date cible, récurrence, responsable, niveau d'urgence, statut.

## Attention

- Ne jamais proposer de diagnostic vétérinaire automatisé.
- Ne pas stocker de credentials IFCE ou FFE.
- Ne pas désactiver RLS pour contourner un problème de lecture.
- Ne pas mélanger les données de deux écuries dans une même requête dashboard.

## Tests attendus

- Unit tests pour logique de rappels, âge cheval, gestation, préparation concours.
- Tests RLS pour isolation entre écuries.
- Tests composants pour cartes chevaux, badges, états vide/erreur.
- Tests E2E pour onboarding, création cheval, création soin, notification simulée.
