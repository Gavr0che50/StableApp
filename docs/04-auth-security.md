# Authentification, rôles et sécurité

## Objectif

Pré-implanter une architecture d'authentification permettant :

- connexion Google ;
- connexion Apple / iCloud ;
- création d'une écurie ;
- ajout de membres ;
- rôles et niveaux de sécurité ;
- isolation stricte des données entre écuries.

## Fournisseur auth

Utiliser Supabase Auth.

### Google

- Utilisé sur iOS, Android et dashboard web.
- Configurer le provider Google dans Supabase.
- Configurer les client IDs nécessaires dans Google Cloud.
- Scopes minimum : `openid`, email, profil.
- Ne pas demander de scopes sensibles sans besoin métier validé.

### Apple / iCloud

Le terme produit affiché peut être `Continuer avec Apple`. Côté utilisateur, cela correspond au compte Apple/iCloud.

- iOS : utiliser la capacité native Sign in with Apple via Expo.
- Web dashboard : utiliser le provider Apple Supabase si activé.
- Android : proposer Google en priorité ; Apple peut être disponible via OAuth web si configuré.
- Capturer le nom complet lors de la première connexion Apple si fourni, car Apple peut ne plus le renvoyer ensuite.

## Onboarding cible

### Étape 1 — Auth

L'utilisateur choisit :

- Continuer avec Google ;
- Continuer avec Apple.

### Étape 2 — Profil

Créer ou compléter `profiles` :

- nom affiché ;
- avatar optionnel ;
- email lu depuis auth.

### Étape 3 — Écurie

Si l'utilisateur n'a aucune appartenance active :

- écran `Créer mon écurie` ;
- champs : nom écurie, pays, timezone ;
- l'utilisateur devient `owner`.

### Étape 4 — Membres

Owner/Admin peut inviter un membre par email :

- email ;
- rôle ;
- message optionnel ;
- expiration du lien.

## Rôles

| Rôle | Droits |
| --- | --- |
| `owner` | Tous droits, transfert propriété futur, suppression écurie, rôles. |
| `admin` | Gestion complète des chevaux, membres hors owner, groupes, soins, sport. |
| `care_manager` | Lecture chevaux, écriture soins, rappels, documents soins, achats santé. |
| `trainer` | Lecture chevaux, écriture sorties, concours, programmes sportifs. |
| `member` | Lecture générale, création notes simples selon modules activés. |
| `viewer` | Lecture seule limitée. |

## Matrice permissions MVP

| Ressource | owner | admin | care_manager | trainer | member | viewer |
| --- | --- | --- | --- | --- | --- | --- |
| Écurie settings | RW | RW | R | R | R | R |
| Membres | RW | RW hors owner | R | R | - | - |
| Chevaux | RW | RW | R | R | R | R |
| Photos cheval | RW | RW | RW santé | RW sport | R | R |
| Soins | RW | RW | RW | R | R limité | R limité |
| Rappels soins | RW | RW | RW | R | R | R |
| Achats | RW | RW | RW | R | RW limité | R |
| Sorties | RW | RW | R | RW | R | R |
| Concours | RW | RW | R | RW | R | R |
| Reproduction | RW | RW | RW | R | R limité | R limité |
| Exports | RW | RW | R limité | R limité | - | - |

## Row Level Security

RLS doit être activé sur toutes les tables métier.

### Lecture

Un utilisateur peut lire une ligne si :

- il est authentifié ;
- il a une ligne `stable_members` active ;
- la ligne métier a le même `stable_id`.

### Écriture

Une écriture est autorisée si :

- l'utilisateur est membre actif ;
- son rôle appartient à la liste de rôles autorisés pour l'action ;
- la ligne écrite conserve le même `stable_id`.

### Suppression

- Suppression physique interdite pour les chevaux ayant historique.
- Utiliser `deleted_at`.
- Réserver aux `owner` et `admin`.
- Écrire un audit log.

## Fonctions SQL attendues

Créer des helpers au scaffolding :

- `is_stable_member(target_stable_id uuid)` ;
- `has_stable_role(target_stable_id uuid, allowed_roles text[])` ;
- `current_user_stable_ids()` ;
- `audit_action(...)`.

## Invitations

### Règles

- Stocker seulement un hash de token.
- Expiration par défaut : 7 jours.
- Une invitation acceptée crée ou active `stable_members`.
- Une invitation expirée doit pouvoir être renvoyée.
- Les rôles `owner` ne sont pas invitables au MVP.

### États

- `pending`
- `accepted`
- `expired`
- `revoked`

## Storage

Buckets privés :

- `horse-photos` ;
- `care-documents` ;
- `stable-documents`.

Règles :

- Pas de bucket public pour données réelles.
- Générer des URLs signées courtes.
- Vérifier l'accès via metadata SQL portant `stable_id`.
- Les fichiers de soin sont considérés sensibles.

## Audit log

Auditer au minimum :

- création écurie ;
- invitation membre ;
- acceptation invitation ;
- changement rôle ;
- désactivation membre ;
- création/suppression cheval ;
- modification antécédents ;
- soin créé/modifié/supprimé ;
- export données ;
- import IFCE / FFE ;
- changement de statut DG+.

## RGPD et confidentialité

Données personnelles concernées :

- email et profil membres ;
- historique d'actions ;
- éventuelles données cavaliers ;
- documents d'écurie ;
- notes libres pouvant contenir informations personnelles.

Mesures :

- minimisation des données ;
- export futur par écurie ;
- suppression ou anonymisation utilisateur désactivé ;
- journalisation des exports ;
- pas de secrets externes en base côté client ;
- politique de rétention à définir avant production.

## Sécurité intégrations externes

- Ne jamais stocker les mots de passe IFCE, FFE ou comptes personnels.
- Privilégier liens profonds, imports manuels ou API officielle.
- Si OAuth officiel existe, stocker tokens chiffrés côté serveur uniquement.
- Toute synchronisation doit conserver `source`, `last_synced_at`, `raw_payload`.

## Menaces principales

| Menace | Mitigation |
| --- | --- |
| Fuite données autre écurie | RLS + tests d'isolation + filtres `stable_id`. |
| Vol de session mobile | SecureStore, refresh contrôlé, logout distant futur. |
| Notifications envoyées au mauvais membre | Vérifier appartenance active avant envoi. |
| Fichier accessible publiquement | Buckets privés + URLs signées. |
| Import externe illégal | Validation CGU, pas de scraping authentifié. |
| Suppression accidentelle | Soft-delete + audit + confirmation. |

## Critères d'acceptation sécurité MVP

- Un utilisateur d'une écurie A ne peut jamais lire chevaux, soins, photos ou dashboard d'une écurie B.
- Un `viewer` ne peut pas créer ou modifier de soin.
- Un `trainer` ne peut pas modifier les rôles membres.
- Un `care_manager` peut créer un rappel vétérinaire.
- Une invitation expirée ne peut pas être acceptée.
- Une URL de photo expirée ne donne plus accès au fichier.
- Tous les changements de rôle sont auditables.
