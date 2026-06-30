# Contrats API et Edge Functions

## Objectif

Définir les opérations applicatives pour mobile, dashboard et backend Supabase.

## Principes

- Le client peut lire directement certaines tables via Supabase si RLS protège correctement.
- Les mutations sensibles passent par Edge Functions.
- Les contrats sont typés avec TypeScript + Zod.
- Toute erreur doit être stable et exploitable par l'UI.

## Format erreur standard

```ts
export type ApiErrorCode =
  | 'UNAUTHENTICATED'
  | 'FORBIDDEN'
  | 'NOT_FOUND'
  | 'VALIDATION_ERROR'
  | 'STABLE_REQUIRED'
  | 'ROLE_REQUIRED'
  | 'HORSE_NOT_FOUND'
  | 'REMINDER_NOT_FOUND'
  | 'EXTERNAL_SOURCE_UNAVAILABLE'
  | 'EXTERNAL_ACCESS_DENIED'
  | 'UNKNOWN_ERROR';

export type ApiError = {
  code: ApiErrorCode;
  message: string;
  details?: Record<string, unknown>;
};
```

## Auth et écurie

### `createStable`

Crée une écurie et donne le rôle `owner` à l'utilisateur.

Input :

```ts
type CreateStableInput = {
  name: string;
  country?: string;
  timezone?: string;
};
```

Output :

```ts
type CreateStableOutput = {
  stableId: string;
  role: 'owner';
};
```

Règles :

- utilisateur authentifié ;
- créer `profile` si absent ;
- créer `stable`; 
- créer `stable_members`; 
- audit `stable.created`.

### `inviteStableMember`

Input :

```ts
type InviteStableMemberInput = {
  stableId: string;
  email: string;
  role: 'admin' | 'care_manager' | 'trainer' | 'member' | 'viewer';
};
```

Règles :

- rôles autorisés : `owner`, `admin` ;
- owner non invitables au MVP ;
- token clair envoyé par email, hash stocké.

### `acceptStableInvitation`

Input :

```ts
type AcceptStableInvitationInput = {
  token: string;
};
```

Règles :

- token valide et non expiré ;
- email auth compatible ou validation à définir ;
- crée ou active `stable_members`.

## Chevaux

### `createHorse`

Input :

```ts
type CreateHorseInput = {
  stableId: string;
  name: string;
  usualName?: string;
  sex?: HorseSex;
  birthDate?: string;
  approxAgeYears?: number;
  breed?: string;
  coat?: string;
  heightCm?: number;
  weightKg?: number;
  sireName?: string;
  damName?: string;
  medicalSummary?: string;
  notes?: string;
  badges?: HorseBadgeCode[];
  groupIds?: string[];
};
```

Règles :

- rôles : `owner`, `admin` ;
- `care_manager` peut proposer selon configuration future ;
- créer audit ;
- badges `FOR_SALE`, `STALLION` peuvent découler de champs.

### `updateHorse`

Règles :

- ne pas autoriser changement de `stableId` ;
- auditer champs sensibles : antécédents, sexe, identité externe.

### `archiveHorse`

Soft-delete ou `is_active=false` selon UX.

Règles :

- rôles : `owner`, `admin` ;
- pas de suppression physique au MVP ;
- audit obligatoire.

## Photos et documents

### `createSignedUploadUrl`

Input :

```ts
type CreateSignedUploadUrlInput = {
  stableId: string;
  horseId?: string;
  bucket: 'horse-photos' | 'care-documents' | 'stable-documents';
  fileName: string;
  contentType: string;
};
```

Règles :

- vérifier rôle ;
- vérifier appartenance cheval ;
- créer metadata après upload confirmé.

## Soins

### `createCareReminder`

Input :

```ts
type CreateCareReminderInput = {
  stableId: string;
  horseId: string;
  type: CareType;
  title: string;
  dueAt: string;
  recurrenceRule?: string;
  assignedTo?: string;
  priority?: 'low' | 'normal' | 'high' | 'urgent';
  note?: string;
};
```

Règles :

- rôles : `owner`, `admin`, `care_manager` ;
- vérifier `assignedTo` membre actif ;
- créer notifications selon préférences.

### `completeCareReminder`

Input :

```ts
type CompleteCareReminderInput = {
  stableId: string;
  reminderId: string;
  performedAt: string;
  description?: string;
  providerName?: string;
  costAmount?: number;
};
```

Règles :

- crée `care_records` ;
- passe rappel à `done` ;
- génère prochain rappel si récurrence ;
- recalcule badges.

### `snoozeCareReminder`

Input :

```ts
type SnoozeCareReminderInput = {
  stableId: string;
  reminderId: string;
  snoozedUntil: string;
  reason?: string;
};
```

## Achats

### `createShoppingItem`

Input :

```ts
type CreateShoppingItemInput = {
  stableId: string;
  horseId?: string;
  catalogItemId?: string;
  name: string;
  quantity?: number;
  unit?: string;
  neededBy?: string;
};
```

### `markShoppingItemBought`

Change statut à `bought` et peut mettre à jour l'inventaire si configuré.

## Concours

### `createCompetitionEvent`

Input :

```ts
type CreateCompetitionEventInput = {
  stableId: string;
  name: string;
  discipline?: string;
  location?: string;
  startsAt: string;
  endsAt?: string;
  sourceUrl?: string;
};
```

### `createCompetitionEntry`

Lie cheval, cavalier et épreuve.

### `generateCompetitionPrepPlan`

Input :

```ts
type GenerateCompetitionPrepPlanInput = {
  stableId: string;
  horseId: string;
  competitionEventId?: string;
  targetDate: string;
  templateCode: 'generic_30d' | 'generic_14d' | 'custom';
};
```

Output :

```ts
type GenerateCompetitionPrepPlanOutput = {
  planId: string;
  taskIds: string[];
};
```

Règles :

- rôles : `owner`, `admin`, `trainer` ;
- tâches modifiables ;
- pas de médical imposé sans validation utilisateur.

## Reproduction

### `createMareCycle`

Input :

```ts
type CreateMareCycleInput = {
  stableId: string;
  horseId: string;
  cycleStartDate?: string;
  heatObserved?: boolean;
  coveringDate?: string;
  stallionHorseId?: string;
  stallionName?: string;
  pregnancyStatus?: PregnancyStatus;
  dgDate?: string;
  expectedFoalingDate?: string;
  notes?: string;
};
```

Règles :

- cheval doit être une jument ou autoriser override ;
- statut `dg_positive` crée/propose badge `DG_PLUS` ;
- audit obligatoire.

## Intégrations externes

### `linkHorseExternalId`

Input :

```ts
type LinkHorseExternalIdInput = {
  stableId: string;
  horseId: string;
  provider: 'ifce' | 'ffe' | 'fei' | 'other';
  externalId: string;
  sourceUrl?: string;
};
```

### `syncHorseExternalData`

Post-MVP uniquement si source autorisée.

Input :

```ts
type SyncHorseExternalDataInput = {
  stableId: string;
  horseId: string;
  provider: 'ifce' | 'ffe';
};
```

Règles :

- ne jamais demander mot de passe externe ;
- vérifier source autorisée ;
- journaliser `external_sync_runs` ;
- marquer données `needs_review`.

## Edge Functions attendues

| Function | Rôle |
| --- | --- |
| `create-stable` | onboarding atomique stable + owner. |
| `invite-member` | invitation et email. |
| `accept-invitation` | acceptation sécurisée. |
| `create-signed-upload-url` | uploads privés. |
| `send-due-notifications` | job planifié notifications. |
| `generate-competition-prep-plan` | génération tâches concours. |
| `sync-external-horse-data` | post-MVP IFCE/FFE. |

## Versioning API

- Les types vivent dans `packages/api`.
- Toute breaking change change le type et met à jour docs.
- Les Edge Functions doivent retourner JSON stable.

## Observabilité

Chaque Edge Function doit logger :

- request id ;
- actor id ;
- stable id ;
- action ;
- durée ;
- erreur normalisée.

Ne pas logger données médicales détaillées ni tokens.
