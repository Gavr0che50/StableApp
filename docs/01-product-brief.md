# Product brief — Stable Enhanced

## Vision

Stable Enhanced aide une écurie à gérer son cheptel au quotidien depuis mobile, tout en offrant une vision macro sur PC. L'application centralise les cartes chevaux, soins, rappels, achats, groupes, cycles de poulinières et préparation concours.

## Proposition de valeur

- Une carte claire par cheval, identifiable par photo.
- Un suivi de soins fiable avec rappels et notifications.
- Une organisation par groupes : bâtiments, paddocks, rôles, badges.
- Un suivi sportif et concours exploitable par cheval.
- Une gestion poulinière adaptée : cycles, DG+, gestation.
- Une vue dashboard pour piloter l'écurie sans remplacer l'usage mobile.

## Personas

### Propriétaire / gérant d'écurie

Besoins : vision globale, délégation, sécurité, historique complet, coûts et alertes.

Actions clés : créer l'écurie, inviter des membres, valider les rôles, suivre les soins en retard, voir les chevaux à vendre ou sportifs.

### Responsable soins

Besoins : rappels, fiches soins, historique, documents vétérinaires, maréchal, vaccins, vermifuge.

Actions clés : créer un soin, planifier une récurrence, marquer comme fait, ajouter une note, joindre une photo/document.

### Cavalier / entraîneur

Besoins : sorties, entraînements, objectifs, concours, préparation, résultats.

Actions clés : ajouter une sortie, créer un programme de concours, consulter les échéances sportives.

### Éleveur / responsable reproduction

Besoins : suivi poulinières, cycles, saillie/IA, échographies, DG+, date prévisionnelle de poulinage.

Actions clés : enregistrer cycle, confirmer DG+, suivre la gestation, générer rappels spécifiques.

### Membre lecture seule

Besoins : accéder aux informations autorisées sans pouvoir modifier les données sensibles.

Actions clés : consulter cartes chevaux, groupes et planning selon rôle.

## Périmètre MVP

### Inclus

- Auth Google + Apple / iCloud.
- Onboarding : création d'écurie, profil, invitation membre.
- Rôles et permissions.
- CRUD chevaux.
- Carte cheval : photo, infos principales, badges, groupes.
- Groupes par bâtiment, localisation, rôle, badge.
- Fiche de soin : types standards + personnalisés.
- Rappels et notifications mobiles.
- Historique des soins et sorties.
- Liste d'achats simple avec catalogue d'objets courants.
- Dashboard PC : effectif, alertes soins, concours, achats, groupes.

### Préparé mais non bloquant MVP

- Import IFCE / FFE automatisé.
- Gestion avancée des cycles poulinières.
- Programmes concours personnalisés.
- Inventaire consommables avec seuils.
- Exports PDF/CSV.

## Définitions métier

### Écurie

Organisation propriétaire des données. Toutes les entités métier sont rattachées à une écurie.

### Cheval

Entité centrale. Un cheval peut avoir plusieurs photos, badges, groupes, soins, sorties, achats, programmes concours et données externes.

### Carte cheval

Vue synthétique d'un cheval : photo principale, nom, âge, sexe, taille, poids, badges, statut soin, statut sport, groupe principal.

### Badge

Marqueur visuel et filtrable. Exemples : `Étalon`, `Poulinière`, `Sportif`, `À vendre`, `DG+`, `Soin urgent`, `Blessé`, `Prêt concours`.

### Fiche de soin

Historique et planning de tous les soins : vétérinaire, vaccin, vermifuge, maréchal, dentiste, ostéo, traitement, observation.

### Sortie

Événement où un cheval sort du quotidien d'écurie : concours, entraînement extérieur, transport, clinique, vente, saillie, randonnée.

### Programme concours

Planning généré depuis une date de concours : tâches de préparation, soins préventifs, entraînements, matériel, documents.

## Parcours clés

### Onboarding écurie

1. Utilisateur se connecte via Google ou Apple.
2. Il crée son profil.
3. Il crée une écurie.
4. Il choisit les modules actifs au départ : soins, sport, reproduction, achats.
5. Il invite les membres.
6. Il crée son premier cheval.

### Créer une carte cheval

1. Ajouter photo principale.
2. Saisir nom, sexe, date de naissance ou âge, taille, poids.
3. Ajouter identifiants éventuels : SIRE, IFCE, FFE.
4. Ajouter badges.
5. Ajouter groupe/bâtiment/localisation.
6. Ajouter antécédents et notes.

### Planifier un soin

1. Choisir cheval.
2. Choisir type de soin.
3. Saisir date cible, responsable, récurrence.
4. Définir niveau de rappel : information, important, urgent.
5. Recevoir notification.
6. Marquer comme fait et ajouter compte-rendu.

### Préparer un concours

1. Choisir cheval.
2. Créer concours ou importer depuis FFE si disponible.
3. Choisir modèle générique ou personnalisé.
4. Générer calendrier J-30 à J+1.
5. Assigner tâches et rappels.
6. Enregistrer résultat et bilan.

## KPIs dashboard PC

- Nombre de chevaux actifs.
- Soins en retard.
- Soins à 7 / 30 jours.
- Chevaux par badge.
- Chevaux par bâtiment/localisation.
- Concours à venir.
- Sorties récentes.
- Achats ouverts.
- Poulinières DG+.
- Stocks sous seuil.

## Contraintes

- Mobile d'abord, iPhone prioritaire.
- Usage extérieur : contraste fort, grosses zones tactiles.
- Données sensibles : santé animale, membres, coordonnées, documents.
- Multi-tenant strict par écurie.
- Les intégrations externes doivent respecter les droits d'accès et CGU.

## Ton produit

Clair, fiable, calme, professionnel. L'application doit évoquer l'écurie moderne : efficace, verte, propre, sans surcharge.
