# Modèle de données cible

## Principes

- Toute donnée métier porte un `stable_id`.
- Les chevaux, soins, sorties, achats, concours, documents et notifications sont isolés par écurie.
- Les suppressions métier importantes sont en soft-delete.
- Les données externes IFCE / FFE sont stockées comme sources traçables, jamais comme vérité unique.
- Les badges peuvent être manuels ou calculés par le système.

## Entités principales

```txt
profiles
  └── stable_members ── stables
                         ├── horses
                         │    ├── horse_photos
                         │    ├── horse_badges
                         │    ├── horse_group_memberships
                         │    ├── care_records
                         │    ├── care_reminders
                         │    ├── outings
                         │    ├── competition_entries
                         │    ├── mare_cycles
                         │    └── horse_external_ids
                         ├── horse_groups
                         ├── shopping_items
                         ├── inventory_items
                         ├── notifications
                         └── audit_logs
```

## Enums métier

### Rôles membres

`owner`, `admin`, `care_manager`, `trainer`, `member`, `viewer`.

### Sexe cheval

`mare`, `stallion`, `gelding`, `filly`, `colt`, `unknown`.

### Badges standards

`STALLION`, `BROODMARE`, `SPORT`, `FOR_SALE`, `DG_PLUS`, `CARE_DUE`, `CARE_OVERDUE`, `INJURED`, `READY_FOR_COMPETITION`, `CUSTOM`.

### Types de soin

`dentist`, `osteopath`, `farrier`, `veterinary`, `vaccine`, `deworming`, `treatment`, `observation`, `custom`.

### Statuts de soin

`planned`, `due_soon`, `overdue`, `done`, `cancelled`, `snoozed`.

### Types de sortie

`competition`, `training`, `clinic`, `transport`, `breeding`, `sale_visit`, `other`.

### Statuts reproduction

`none`, `suspected`, `dg_positive`, `dg_negative`, `pregnant`, `foaled`, `lost`.

## Tables de base

### `profiles`

Profil applicatif lié à `auth.users.id`.

Champs : `id`, `display_name`, `avatar_url`, `email`, `created_at`, `updated_at`.

### `stables`

Organisation propriétaire des données.

Champs : `id`, `name`, `slug`, `country`, `timezone`, `created_by`, `created_at`, `updated_at`, `deleted_at`.

### `stable_members`

Appartenance utilisateur-écurie.

Champs : `id`, `stable_id`, `profile_id`, `role`, `status`, `invited_by`, `created_at`, `updated_at`.

Contraintes : unique `stable_id + profile_id`.

### `stable_invitations`

Invitation par email.

Champs : `id`, `stable_id`, `email`, `role`, `token_hash`, `expires_at`, `accepted_at`, `created_by`, `created_at`.

Le token clair ne doit jamais être stocké.

## Tables chevaux

### `horses`

Fiche centrale d'un cheval.

Champs : `id`, `stable_id`, `name`, `usual_name`, `sex`, `birth_date`, `approx_age_years`, `breed`, `coat`, `height_cm`, `weight_kg`, `sire_name`, `dam_name`, `medical_summary`, `notes`, `primary_photo_id`, `is_active`, `for_sale`, `created_at`, `updated_at`, `deleted_at`.

Règles :

- Préférer `birth_date` à un âge manuel.
- Ne jamais supprimer physiquement si historique existant.
- `primary_photo_id` sert à l'identification visuelle de la carte.

### `horse_photos`

Photos d'identification, santé ou documents.

Champs : `id`, `stable_id`, `horse_id`, `storage_path`, `kind`, `caption`, `taken_at`, `created_by`, `created_at`.

### `horse_badges`

Badges affichés sur cartes et filtres.

Champs : `id`, `stable_id`, `horse_id`, `code`, `label`, `color_token`, `source`, `starts_at`, `ends_at`, `created_at`.

`source` vaut `manual`, `system` ou `import`.

### `horse_groups`

Groupes par bâtiment, localisation, rôle ou libre.

Champs : `id`, `stable_id`, `name`, `kind`, `description`, `created_at`.

`kind` vaut `building`, `location`, `role`, `badge`, `custom`.

### `horse_group_memberships`

Appartenance d'un cheval à un groupe avec historique possible.

Champs : `id`, `stable_id`, `horse_id`, `group_id`, `starts_at`, `ends_at`.

## Tables soins

### `care_records`

Historique des soins réalisés et observations.

Champs : `id`, `stable_id`, `horse_id`, `type`, `title`, `description`, `performed_at`, `provider_name`, `cost_amount`, `cost_currency`, `created_by`, `created_at`.

### `care_reminders`

Échéances et récurrences.

Champs : `id`, `stable_id`, `horse_id`, `care_record_id`, `type`, `title`, `due_at`, `recurrence_rule`, `assigned_to`, `status`, `priority`, `snoozed_until`, `completed_at`, `created_by`, `created_at`, `updated_at`.

Règles :

- Un rappel peut exister sans soin réalisé.
- Un soin réalisé peut générer le prochain rappel.
- Un rappel peut être assigné à un membre actif.

## Tables achats et inventaire

### `shopping_catalog_items`

Catalogue global d'objets courants : couverture, licol, granulés, pharmacie, matériel.

Champs : `id`, `name`, `category`, `default_unit`, `is_global`.

### `shopping_items`

Liste d'achats de l'écurie ou d'un cheval.

Champs : `id`, `stable_id`, `horse_id`, `catalog_item_id`, `name`, `quantity`, `unit`, `status`, `needed_by`, `created_by`, `created_at`.

### `inventory_items`

Inventaire simple.

Champs : `id`, `stable_id`, `horse_id`, `name`, `category`, `quantity`, `unit`, `min_threshold`, `location`, `updated_at`.

## Tables sport et concours

### `outings`

Historique des sorties par cheval.

Champs : `id`, `stable_id`, `horse_id`, `type`, `title`, `location`, `starts_at`, `ends_at`, `notes`, `result_summary`, `created_by`, `created_at`.

### `competition_events`

Concours ou événement sportif.

Champs : `id`, `stable_id`, `source`, `external_id`, `name`, `discipline`, `location`, `starts_at`, `ends_at`, `raw_payload`, `created_at`.

### `competition_entries`

Engagement cheval / cavalier / épreuve.

Champs : `id`, `stable_id`, `horse_id`, `competition_event_id`, `rider_name`, `class_name`, `status`, `result_rank`, `result_score`, `notes`.

### `competition_prep_plans`

Programme de préparation concours.

Champs : `id`, `stable_id`, `horse_id`, `competition_event_id`, `name`, `template_code`, `starts_at`, `target_date`, `created_by`.

### `competition_prep_tasks`

Tâches du programme de préparation.

Champs : `id`, `stable_id`, `plan_id`, `horse_id`, `title`, `category`, `due_at`, `assigned_to`, `status`.

## Tables reproduction

### `mare_cycles`

Suivi des cycles poulinières.

Champs : `id`, `stable_id`, `horse_id`, `cycle_start_date`, `heat_observed`, `covering_date`, `stallion_horse_id`, `stallion_name`, `pregnancy_status`, `dg_date`, `expected_foaling_date`, `notes`, `created_at`.

Règles :

- `dg_positive` alimente le badge `DG_PLUS`.
- La date de poulinage prévisionnelle peut être proposée puis modifiée manuellement.
- Les cycles restent historiques.

## Tables intégrations

### `horse_external_ids`

Identifiants IFCE, FFE, FEI ou autres.

Champs : `id`, `stable_id`, `horse_id`, `provider`, `external_id`, `source_url`, `last_synced_at`, `raw_payload`.

Règles :

- `provider` vaut `ifce`, `ffe`, `fei` ou `other`.
- `raw_payload` conserve la donnée source reçue ou importée.
- L'utilisateur doit voir la date de dernière synchronisation.

## Tables notifications et audit

### `notifications`

Notifications planifiées, envoyées ou échouées.

Champs : `id`, `stable_id`, `profile_id`, `horse_id`, `type`, `title`, `body`, `scheduled_for`, `sent_at`, `read_at`, `status`, `metadata`.

### `audit_logs`

Journal des actions sensibles.

Champs : `id`, `stable_id`, `actor_profile_id`, `action`, `entity_type`, `entity_id`, `before`, `after`, `created_at`.

Actions minimales à auditer : invitation, changement de rôle, suppression, soin sensible, export, import externe.

## Index recommandés

- `stable_members(stable_id, profile_id)` unique.
- `horses(stable_id, is_active, deleted_at)`.
- `horse_badges(stable_id, horse_id, code)`.
- `care_reminders(stable_id, due_at, status)`.
- `care_records(stable_id, horse_id, performed_at desc)`.
- `outings(stable_id, horse_id, starts_at desc)`.
- `competition_events(stable_id, starts_at)`.
- `mare_cycles(stable_id, horse_id, cycle_start_date desc)`.
- `notifications(profile_id, scheduled_for, status)`.

## RLS attendu

Toutes les tables métier doivent activer Row Level Security.

Pattern attendu :

- Lecture si l'utilisateur est membre actif de `stable_id`.
- Écriture si rôle suffisant.
- Suppression logique réservée à `owner` ou `admin`.
- Les buckets Storage doivent être privés et vérifiés par metadata SQL.

## Données dérivées

À calculer de préférence plutôt qu'à stocker :

- âge exact depuis `birth_date` ;
- statut soin en retard depuis `care_reminders` ;
- badge DG+ depuis `mare_cycles` ;
- statut prêt concours depuis programme et rappels.

Si performance dashboard nécessaire, utiliser une vue ou table de cache recalculable.
