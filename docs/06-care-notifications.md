# Soins, rappels et notifications

## Objectif

Créer un suivi fiable des soins de chaque cheval, avec historique, échéances, rappels et notifications.

L'application organise l'information et les rappels. Elle ne remplace pas un vétérinaire et ne fournit pas de diagnostic médical.

## Types de soins MVP

- Dentiste.
- Ostéopathe.
- Maréchal.
- Vétérinaire.
- Vaccin.
- Vermifuge.
- Traitement.
- Observation.
- Soin personnalisé.

## Fiche de soin cheval

Chaque cheval possède une fiche de soin composée de :

1. résumé santé ;
2. antécédents ;
3. soins réalisés ;
4. rappels planifiés ;
5. documents et photos ;
6. professionnels associés ;
7. notes internes.

## Rappel de soin

### Champs requis

- Cheval.
- Type de soin.
- Titre.
- Date/heure d'échéance.
- Priorité.
- Responsable optionnel.

### Champs optionnels

- Récurrence.
- Note.
- Professionnel.
- Document.
- Coût estimé.
- Rappel avant échéance.

## Statuts

- `planned` : planifié.
- `due_soon` : bientôt dû.
- `overdue` : en retard.
- `done` : fait.
- `cancelled` : annulé.
- `snoozed` : repoussé.

## Priorités

- `low` : information.
- `normal` : soin courant.
- `high` : important.
- `urgent` : nécessite attention rapide.

## Récurrences

Récurrences MVP recommandées :

- aucune ;
- toutes les X semaines ;
- tous les X mois ;
- tous les X ans ;
- date personnalisée après validation du soin.

Exemples :

- Maréchal toutes les 6 à 8 semaines selon cheval.
- Dentiste annuel.
- Vaccin selon protocole défini par l'utilisateur/vétérinaire.
- Vermifuge selon protocole d'écurie.

Ne pas imposer de protocole médical unique. L'utilisateur doit pouvoir modifier toutes les échéances.

## Notification

### Canaux

- Notification push mobile.
- Notification locale si créée côté appareil.
- Badge in-app.
- Email post-MVP.

### Moments de notification

Par défaut :

- J-7 pour soin important.
- J-1.
- Jour J.
- J+1 si non fait.

Le comportement doit être paramétrable par type de soin et par utilisateur.

## Cycle de vie d'un soin

### Création rappel

1. L'utilisateur crée un rappel.
2. Le rappel est stocké en base.
3. Une notification planifiée est créée.
4. L'action est auditée.

### Marquer fait

1. L'utilisateur clique `Marquer fait`.
2. Il saisit date réelle, note, professionnel, coût optionnel.
3. Un `care_record` est créé.
4. Le rappel passe à `done`.
5. Si récurrent, le prochain rappel est créé.
6. Les badges `CARE_DUE` / `CARE_OVERDUE` sont recalculés.

### Repousser

1. L'utilisateur choisit une nouvelle date ou durée.
2. Le rappel passe à `snoozed` ou met à jour `due_at` selon choix UX.
3. Un audit log est écrit.
4. Les notifications sont replanifiées.

## Badges automatiques liés aux soins

### `CARE_DUE`

Affiché si un soin est dû dans la fenêtre configurée, par défaut 7 jours.

### `CARE_OVERDUE`

Affiché si un soin non terminé a une date passée.

### `INJURED`

Manuel. Peut être suggéré depuis un soin de type traitement, mais jamais activé automatiquement sans confirmation.

## Vue Soins mobile

### Filtres

- Aujourd'hui.
- En retard.
- 7 prochains jours.
- 30 prochains jours.
- Par type.
- Par responsable.
- Par groupe de chevaux.

### Actions rapides

- Marquer fait.
- Repousser.
- Réassigner.
- Ajouter note.
- Appeler professionnel si numéro renseigné.

## Vue détail soin

Doit afficher :

- cheval ;
- type ;
- statut ;
- échéance ;
- responsable ;
- historique modifications ;
- documents ;
- notes.

## Notifications techniques

### Table `notifications`

Créer une notification par destinataire et échéance.

### Job planifié

Un job serveur tourne régulièrement pour :

- trouver notifications planifiées non envoyées ;
- vérifier que le destinataire est toujours membre actif ;
- envoyer via Expo Push ;
- marquer `sent` ou `failed` ;
- conserver l'erreur si échec.

### Sécurité

Avant l'envoi :

- vérifier `profile_id` membre actif ;
- vérifier que le cheval appartient à la même écurie ;
- ne pas inclure données médicales détaillées dans le corps push.

Exemple de notification :

`Maréchal prévu demain pour Quador`.

Éviter :

`Traitement X pour pathologie Y`.

## Offline

- L'utilisateur peut préparer une note de soin offline.
- L'action est mise en file d'attente.
- À la reconnexion, l'action se synchronise.
- Si conflit, afficher l'historique et garder les deux notes si nécessaire.

## Données sensibles

Les notes de soin peuvent contenir des informations sensibles. Elles doivent être :

- visibles uniquement selon rôle ;
- auditables ;
- exportables par l'écurie ;
- protégées par RLS.

## Critères d'acceptation

- Un `care_manager` peut créer un rappel pour un cheval de son écurie.
- Un `viewer` ne peut pas créer de rappel.
- Un rappel en retard affiche un badge rouge sur la carte cheval.
- Marquer un rappel récurrent comme fait crée le prochain rappel.
- La notification n'est pas envoyée à un membre désactivé.
- Un soin ne peut pas être créé pour un cheval d'une autre écurie.
- Une note de soin offline est clairement marquée comme non synchronisée.

## Tests unitaires métier

À créer dans `packages/core` :

- calcul prochain rappel mensuel ;
- calcul statut `overdue` ;
- génération notification J-1 ;
- récurrence après soin fait ;
- priorité d'affichage des badges soins.
