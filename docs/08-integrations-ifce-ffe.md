# Intégrations IFCE / FFE

## Objectif

Préparer l'application à exploiter les informations officielles ou publiques utiles :

- IFCE / Info Chevaux : généalogie, fiche équidé, informations d'identification si accessibles légalement.
- FFE Compet : suivi sportif, recherche concours, recherche cheval, résultats, engagements accessibles selon droits.

## Principe clé

Aucune intégration ne doit contourner les conditions d'utilisation, une authentification, une protection technique ou une restriction de données personnelles.

## IFCE / Info Chevaux

### Données souhaitées

- Identifiant SIRE.
- Nom officiel.
- Sexe.
- Race.
- Date de naissance.
- Robe.
- Père.
- Mère.
- Généalogie.
- Éleveur / naisseur si donnée légalement accessible.
- Source et date de consultation.

### Stratégie MVP

1. L'utilisateur saisit manuellement l'identifiant SIRE ou les informations connues.
2. L'application propose un champ `Lien IFCE / Info Chevaux`.
3. L'utilisateur peut coller une URL ou saisir des données issues de sa consultation.
4. Les données sont marquées `source = manual_ifce`.
5. La fiche cheval indique `Données à vérifier` tant qu'aucune validation utilisateur n'est faite.

### Stratégie post-MVP

- Rechercher s'il existe une API officielle, un export autorisé ou un partenariat IFCE.
- Créer un connecteur `ifce-adapter` isolé.
- Ajouter une table `external_sync_runs`.
- Ne synchroniser que les données autorisées.
- Conserver le payload brut et la date de sync.

### Interdictions

- Pas de scraping de page protégée.
- Pas de contournement Incapsula, captcha, session ou restriction.
- Pas de stockage d'identifiants IFCE.
- Pas de donnée personnelle non nécessaire.

## FFE Compet

### Données souhaitées

- Recherche concours.
- Recherche cheval.
- Recherche cavalier si autorisé.
- Résultats.
- Classements permanents.
- Engagements à venir si l'utilisateur y a droit.

### Contraintes connues

FFE Compet expose des pages de recherche concours, cheval et cavalier, mais certaines données personnelles et certains engagements futurs peuvent être soumis à identification ou à profils autorisés. La documentation produit doit respecter ce principe : toute donnée non publique ou réservée doit rester accessible uniquement à l'utilisateur autorisé.

### Stratégie MVP

1. Création manuelle de concours.
2. Champ source externe optionnel : URL FFE Compet.
3. Résultats saisis manuellement.
4. Import CSV ou copier-coller structuré si l'utilisateur dispose légalement de la donnée.
5. Marquage `source = manual_ffe`.

### Stratégie post-MVP

- Étudier API officielle ou flux autorisé.
- Créer connecteur `ffe-adapter` derrière Edge Function.
- Journaliser chaque sync.
- Afficher `Donnée importée — à vérifier`.
- Ne pas importer les engagements futurs d'autres personnes sans droit.

### Interdictions

- Pas de stockage du mot de passe FFE.
- Pas de robotisation de compte utilisateur.
- Pas d'import de données personnelles sans finalité.
- Pas d'affichage d'engagements futurs à un membre non autorisé.

## Architecture connecteurs

```txt
apps/mobile ou dashboard
        |
        v
Edge Function sync-external-horse-data
        |
        v
provider adapter: ifce / ffe / manual
        |
        v
horse_external_ids + raw_payload + audit_logs
```

## Tables recommandées

### `horse_external_ids`

Stocke les identifiants externes par cheval.

Champs :

- `stable_id`
- `horse_id`
- `provider`
- `external_id`
- `source_url`
- `last_synced_at`
- `raw_payload`

### `external_sync_runs`

Journalise les syncs.

Champs :

- `id`
- `stable_id`
- `provider`
- `entity_type`
- `entity_id`
- `started_at`
- `finished_at`
- `status`
- `error_code`
- `error_message`
- `created_by`

## États de donnée externe

- `not_linked` : aucun identifiant externe.
- `manual` : renseigné par utilisateur.
- `linked` : identifiant externe enregistré.
- `synced` : synchronisé automatiquement.
- `stale` : synchronisation ancienne.
- `error` : sync échouée.
- `needs_review` : donnée importée à valider.

## UX intégrations

### Fiche cheval

Section `Données officielles` :

- SIRE / IFCE.
- FFE si sportif.
- Dernière vérification.
- Bouton `Ouvrir la source`.
- Bouton `Mettre à jour manuellement`.

### Concours

Section `Source FFE` :

- lien concours ;
- date import ;
- statut import ;
- résultat saisi/importé.

## Validation utilisateur

Toute donnée importée modifiant la carte cheval doit être validée :

- afficher ancienne valeur ;
- afficher nouvelle valeur ;
- permettre accepter/refuser champ par champ ;
- conserver audit.

## Gestion erreurs

Erreurs types :

- `EXTERNAL_SOURCE_UNAVAILABLE`
- `EXTERNAL_ACCESS_DENIED`
- `EXTERNAL_RATE_LIMITED`
- `EXTERNAL_DATA_CHANGED`
- `EXTERNAL_UNSUPPORTED_FORMAT`
- `EXTERNAL_MANUAL_REVIEW_REQUIRED`

## Critères d'acceptation MVP

- Un cheval peut stocker un SIRE et une URL IFCE.
- Un cheval sportif peut stocker une URL FFE.
- Une donnée externe est visuellement distinguée d'une donnée interne.
- Aucun mot de passe IFCE/FFE n'est demandé ou stocké.
- Un import manuel écrit un audit log.
- Un membre viewer ne peut pas lancer d'import externe.

## Références de veille

- IFCE : https://www.ifce.fr/
- Info Chevaux : https://infochevaux.ifce.fr/
- FFE : https://www.ffe.com/
- FFE Compet : https://ffecompet.ffe.com/
