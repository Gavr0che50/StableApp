# Concours, préparation et suivi sportif

## Objectif

Permettre le suivi sportif par cheval : sorties, concours, programmes de préparation, historique et résultats.

## Concepts

### Sortie

Événement lié à un cheval hors gestion quotidienne : concours, entraînement extérieur, clinique, transport, vente, reproduction.

### Concours

Événement sportif structuré : lieu, dates, discipline, épreuve, cavalier, engagement, résultat.

### Programme de préparation

Calendrier pré-concours généré depuis une date cible, avec tâches assignables.

## Disciplines MVP

- CSO.
- Dressage.
- CCE.
- Hunter.
- Endurance.
- Autre discipline libre.

## Historique des sorties

Chaque sortie doit pouvoir stocker :

- cheval ;
- type ;
- lieu ;
- date début/fin ;
- cavalier ou responsable ;
- transport ;
- notes ;
- documents ;
- résultat ou bilan ;
- lien concours si applicable.

## Concours

### Création manuelle MVP

Champs :

- nom concours ;
- discipline ;
- lieu ;
- date début ;
- date fin ;
- cheval ;
- cavalier ;
- épreuve ;
- statut : prévu, engagé, terminé, annulé.

### Import FFE futur

Prévoir champs :

- source `ffe` ;
- ID externe ;
- URL source ;
- date de synchronisation ;
- payload brut ;
- statut données : `à vérifier`, `validé`.

## Programme générique pré-concours

Le générateur doit créer une base modifiable. Il ne remplace pas l'entraîneur.

### Template 30 jours

| Période | Tâches proposées |
| --- | --- |
| J-30 | Vérifier objectif, niveau, engagement, documents, vaccin requis. |
| J-21 | Planifier charge de travail, maréchal si nécessaire, équipement. |
| J-14 | Sortie/entraînement ciblé, vérifier transport, alimentation. |
| J-10 | Contrôle matériel : protections, licol, couverture, papiers. |
| J-7 | Vérifier état cheval, rappels soin, planning cavalier. |
| J-3 | Préparer malle, nettoyer matériel, confirmer horaires. |
| J-1 | Préparer cheval, documents, transport, ration, eau. |
| Jour J | Checklist départ, arrivée, détente, résultat. |
| J+1 | Bilan récupération, note soin, sortie post-concours. |

### Tâches catégories

- Entraînement.
- Soin.
- Matériel.
- Administratif.
- Transport.
- Alimentation.
- Récupération.

## Programme personnalisé

Paramètres futurs :

- discipline ;
- niveau ;
- âge cheval ;
- historique concours ;
- statut soin ;
- charge récente ;
- objectif ;
- contraintes transport ;
- préférences entraîneur.

Le système propose, l'utilisateur valide.

## Badges sport

### `SPORT`

Manuel ou activé si le cheval a des concours/sorties sportives récentes.

### `READY_FOR_COMPETITION`

Manuel ou proposé si :

- programme pré-concours sans tâche critique en retard ;
- pas de soin urgent ;
- statut cheval actif ;
- validation humaine.

### `CARE_OVERDUE`

Doit rester prioritaire sur `READY_FOR_COMPETITION`.

## Vue mobile concours

### Liste

- À venir.
- Cette semaine.
- Par cheval.
- Par discipline.
- Historique.

### Détail concours

Sections :

- infos concours ;
- chevaux engagés ;
- planning préparation ;
- documents ;
- matériel ;
- résultat ;
- bilan.

### Action rapide

- Créer programme.
- Ajouter tâche.
- Marquer tâche faite.
- Ajouter résultat.
- Ajouter sortie liée.

## Dashboard PC sport

KPIs :

- concours à 30 jours ;
- chevaux sportifs actifs ;
- tâches pré-concours en retard ;
- sorties récentes ;
- résultats par cheval ;
- charge de sorties par groupe.

## Résultats

Résultat minimal :

- rang ;
- score ;
- pénalités ;
- temps ;
- commentaire ;
- classement libre ;
- pièce jointe.

Le modèle doit rester flexible par discipline.

## Historique cheval

Dans le détail cheval, afficher :

- timeline sorties/concours ;
- meilleurs résultats ;
- prochaines échéances ;
- charge récente ;
- notes de récupération.

## Liens avec soins

Un programme peut créer des rappels de soin :

- maréchal pré-concours ;
- contrôle vaccin/document ;
- récupération post-concours ;
- observation locomotion.

Ces tâches ne doivent pas créer de soins médicaux imposés. L'utilisateur valide.

## Critères d'acceptation

- Un `trainer` peut créer un concours et un programme pour un cheval de son écurie.
- Un `care_manager` peut lire le programme mais ne modifie pas les résultats sportifs sauf rôle additionnel.
- Un concours peut être créé sans import FFE.
- Un programme J-30 génère des tâches modifiables.
- Une tâche de préparation en retard apparaît sur le dashboard.
- Un résultat peut être ajouté à l'historique du cheval.
- Les données importées FFE sont signalées comme source externe et vérifiables.

## Tests métier attendus

- Génération template J-30 avec bonnes dates relatives.
- Programme cible un seul cheval.
- Tâches critiques en retard empêchent badge `READY_FOR_COMPETITION` automatique.
- Historique sorties trié par date décroissante.
