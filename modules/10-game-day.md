# 10 — Game day de dépannage

**Phase : dépannage · Durée : 4 à 6 h**

## Objectifs

- Diagnostiquer plusieurs symptômes mélangés sans deviner à partir d'un seul `Ready=False`.
- Construire un arbre de diagnostic de la source vers la charge applicative.
- Corriger par Git, garder un historique d'incident et prouver le retour au service.
- Présenter un postmortem expliquant mécanisme, impact, détection et prévention.

## Notions à comprendre — et pourquoi

- **Ordre de diagnostic** : commencer par la source et sa révision, puis la Kustomization/HelmRelease, leurs dépendances, l'objet rendu et finalement le workload.
- **Preuves** : `flux get`, `flux logs`, `flux trace`, `flux events`, `kubectl describe`, conditions et génération observée composent une chronologie. Ne pas effacer l'évidence en corrigeant trop tôt.
- **Symptômes combinés** : un reconcile manuel réussi n'assure pas que les références de values, les permissions ou les alertes annexes sont correctes.
- **Récupération** : revenir à une révision Git connue, suspendre uniquement si nécessaire, préserver l'inventaire et les données, puis reprendre la réconciliation.
- **Postmortem** : comprendre la chaîne causale, pas seulement identifier la ligne YAML en faute.

## Lab — incident final

Demander à un collègue ou à un script de préparation de choisir **3 à 5 pannes** dans les modules, sans vous révéler leur sélection. Si vous êtes seul, tirer une combinaison au hasard en utilisant seulement les indices, et masquer les fichiers de corrections.

Scénarios candidats : commit absent de la branche suivie ; accès Git révoqué ; dependency sur une Kustomization `Stalled` ; substitution non résolue ; Secret SOPS indéchiffrable ; chart inaccessible ; policy d'image sans sélection ; RBAC du tenant insuffisant ; webhook reçu mais mauvais resource selector ; controller prêt mais workload non sain.

Procédure :

1. Ne modifier aucun objet à la main. Consigner heure, cluster/contexte et impact.
2. Collecter `flux get all -A`, `flux events -A`, `flux logs --all-namespaces`, `flux trace` sur la ressource racine et `kubectl describe` des objets pertinents.
3. Construire une hypothèse par panne et l'associer à une preuve ; noter aussi les hypothèses réfutées.
4. Corriger par PR/commit ou rollback documenté. Réconcilier et mesurer le temps de convergence.
5. Vérifier l'état final dans Git, Flux et Kubernetes ; rédiger un postmortem avec action de prévention et propriétaire.

## Critères de réussite

- Chaque panne est localisée au contrôleur/objet/étape corrects avec au moins deux preuves indépendantes.
- Aucun correctif n'est appliqué manuellement au cluster.
- La remédiation est limitée et vérifiée ; les symptômes annexes ne sont pas ignorés.
- Le postmortem décrit un mécanisme causal et une action préventive testable.

## Pannes volontaires

Le game day est la panne volontaire intégrée. Le formateur ajoute au moins une défaillance de source, une de rendu/apply ou RBAC et une de workload/observabilité, dans un cluster jetable.

## Questions de compréhension

1. Quelle observation établit que le contrôleur a vu le dernier commit ?
2. Quel statut dit que Flux est empêché de progresser plutôt que simplement en retry ?
3. Comment prouvez-vous que le retour au vert est dû à votre changement et non à une réussite transitoire ?
4. Quelle amélioration de conception réduirait l'impact si le même incident se reproduisait ?

## Progression sans solution immédiate

Voir [indices 10](../corriges/10-game-day.md#indices), puis [corrigé 10](../corriges/10-game-day.md#corrigé).
