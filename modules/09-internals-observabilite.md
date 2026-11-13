# 09 — Observabilité et fonctionnement interne

**Phase : expertise · Durée : 5 h**

## Objectifs

- Utiliser métriques Prometheus, logs, événements et UI sans confondre signal et source de vérité.
- Expliquer cache, artefacts, révisions et réconciliation dans un flux complet.
- Lire un chemin de code controller-runtime et relier le code aux statuts d'une CRD.
- Mesurer capacité, latence, files d'attente et effets de charge dans une flotte.

## Notions à comprendre — et pourquoi

- **Conditions et générations** : lire `Ready`, `Reconciling`, `Stalled`, reason/message et `observedGeneration`; un statut ancien ne prouve pas qu'une spec récente a été observée.
- **Métriques et journaux** : Prometheus donne tendance/saturation/erreurs agrégées ; logs structurés donnent l'opération et la ressource ; événements sont utiles mais expirent. L'interface Flux ne remplace pas Git ni l'API Kubernetes.
- **Artefacts et cache** : source-controller conserve des artefacts versionnés exposés par URL ; les contrôleurs consommateurs réutilisent ces révisions. Comparer les digests permet d'isoler une source inchangée d'un apply échoué.
- **Requeue** : controller-runtime observe événements, calcule le travail nécessaire et reprogramme succès/échec. Ce n'est pas un processus unique qui exécute un pipeline de bout en bout.
- **Code** : suivre `Reconcile`, appels de clients et conditions/résultats. Les détails d'implémentation varient entre composants ; éviter d'inférer le comportement d'un contrôleur d'un autre.
- **UI** : Flux n'impose pas une UI centrale dans ses composants par défaut. Le Flux Web UI est intégré à Flux Operator ; il s'agit d'un projet d'écosystème à versionner/opérer séparément. Utiliser un accès RBAC adapté : boutons de réconciliation/suspension impliquent des droits d'écriture.

## Lab — observer et lire un contrôleur

1. Installer Prometheus Operator/Grafana via Git, configurer la découverte du port métriques Flux (8080) et importer ou construire un dashboard avec `gotk_reconcile_duration_seconds`, erreurs/retries, CPU/mémoire, workload queue et métriques CRD via kube-state-metrics.
2. Déclencher un reconcile manuel et une panne temporaire. Capturer les événements, conditions, logs et métriques au même horodatage.
3. Examiner la [Flux Web UI fournie avec Flux Operator](https://fluxoperator.dev/web-ui/) sur un lab, ou installer la version choisie de l'Operator via Git. Vérifier les droits effectifs (notamment actions reconcile/suspend) et comparer son rendu avec `flux get`, Git et `kubectl`.
4. Choisir une ressource (GitRepository ou Kustomization). Dans le dépôt du contrôleur correspondant, tracer le code de watch/setup vers `Reconcile`, acquisition d'artefact, mutation et statut.
5. Produire une séquence de réconciliation annotée avec les commits et digests observés. Identifier le cache/artifact server utilisé.
6. Définir des objectifs de service : durée maximale de convergence, taux d'erreur, charge de contrôleurs, fréquence de polling et capacité cible.

## Critères de réussite

- Un incident peut être corrélé de Git à la condition Flux et à la métrique.
- L'UI est en lecture seule pour les utilisateurs non-admin.
- Le diagramme distingue événement déclencheur, réconciliation, résultat et nouvelle attente.
- La lecture de code cite les fonctions et la version de contrôleur réellement consultée.

## Pannes volontaires

Couper l'accès à la source-controller, augmenter l'intervalle, provoquer un timeout API ou une boucle d'échec, arrêter une cible Prometheus. Déterminer ce qui est une vraie panne de livraison, ce qui est une lacune d'observabilité et comment le cache influe sur le diagnostic.

## Questions de compréhension

1. Pourquoi un controller ne fait-il pas de « watch Git » continu ?
2. Quelle différence entre `observedGeneration` et révision source ?
3. Que peut-on conclure d'un artefact dont le digest est inchangé ?
4. Quelles métriques signalent la saturation sans révéler à elles seules la cause ?
5. Quel chemin précis dans `Reconcile` explique le statut que vous avez observé ?

## Progression sans solution immédiate

Voir [indices 09](../corriges/09-internals-observabilite.md#indices), puis [corrigé 09](../corriges/09-internals-observabilite.md#corrigé).
