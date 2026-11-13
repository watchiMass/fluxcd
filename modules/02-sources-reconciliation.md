# 02 - Sources, artefacts et réconciliation

**Phase : socle · Durée : 4 h**

## Objectifs

- Comparer `GitRepository`, `OCIRepository`, `HelmRepository`, `HelmChart` et `Bucket`.
- Tracer un artefact immuable d'une révision de source à un rendu Kustomize ou chart.
- Distinguer le `Kustomization` Flux du fichier `kustomization.yaml` de Kustomize.
- Décrire build, validation, apply, health assessment, inventaire et drift correction.

## Notions à comprendre - et pourquoi

- **Sources uniformisées en artefacts** : source-controller résout/fetch, archive et publie des artefacts adressables par révision. Les consommateurs n'ont pas besoin de cloner eux-mêmes chaque dépôt.
- **Révision et digest** : une branche/tag est une règle de sélection, pas nécessairement une identité immuable. Le statut donne la révision/digest réellement traité ; pour les déploiements sensibles, privilégier la traçabilité et les références immuables quand possible.
- **Kustomize Controller** : consomme un artefact, déchiffre si configuré, construit les manifests, valide puis applique. `.spec.path` et la source sont distincts.
- **Reconciliation** : une nouvelle génération ou révision déclenche un passage sans attendre l'intervalle. Les erreurs sont réessayées. Les intervalles ne sont pas un cron exact.
- **Server-side apply et inventory** : Flux suit les objets qu'il gère et corrige les écarts détectés. `prune` permet de collecter des objets retirés du rendu ; il peut être destructeur.
- **Helm** : helm-controller transforme `HelmRelease` en opérations Helm et observe le statut du release ; le chart est lui-même fourni via Source.

## Lab - suivre quatre Sources et deux consommateurs

1. Comparer puis manipuler les sources : GitRepository sur le dépôt du cours ; HelmRepository/HelmChart sur le dépôt chart public de test ; OCIRepository sur un OCI artifact ; Bucket sur un endpoint S3-compatible éphémère. Le secret d'accès Bucket est fourni hors Git. Pour chaque objet, observer si l'artefact est Ready et examiner sa révision/digest.
2. Depuis le dépôt fil rouge, examiner `GitRepository/flux-system`: URL, ref, intervalle, secret éventuel, statut, revision, URL et digest d'artefact.
3. Ajouter par commit une Kustomization enfant pour `apps/lab`, puis la suivre. Modifier une valeur et corréler le nouvel artefact, les événements et les objets gérés.
4. Déployer deux HelmRelease éphémères, un alimenté par HelmRepository/HelmChart et un par OCIRepository, en suivant le schéma v2 de la [doc officielle](https://fluxcd.io/flux/components/helm/helmreleases/). Comparer les Sources et statuts intermédiaires. Ne pas épingler `latest` en production.
5. Sur une ressource éphémère, supprimer manuellement un champ géré depuis un outil autorisé en lab et observer la correction du drift au passage suivant.
6. Produire un diagramme temporel incluant les contrôleurs, les révisions d'artefact, l'API Kubernetes et les conditions `Ready`.

## Critères de réussite

- Vous pouvez retrouver depuis un objet appliqué la Kustomization source, sa révision et le statut du Source.
- Vous pouvez distinguer un échec de fetch, de build, de validation, d'apply et de health check.
- Vous expliquez pourquoi la déclaration de chart et le release sont des ressources différentes.
- Vous savez montrer les risques de `prune`, ainsi que les moyens de protéger un objet critique.

## Pannes volontaires

Dans le dépôt de lab, provoquer successivement un ref Git inexistant, un YAML invalide, une ressource qui ne passe pas l'admission et un selector de health check erroné. Utiliser `flux trace kustomization <nom> -n <namespace>`, `flux logs`, `flux events` et `kubectl describe`. Identifier la dernière étape réussie pour chaque panne.

## Questions de compréhension

1. Quelle différence entre une révision source et l'intervalle de polling ?
2. Pourquoi un artefact source évite-t-il que chaque contrôleur utilise son propre checkout Git ?
3. Comment Flux sait-il quels objets il peut supprimer en prune ?
4. Pourquoi un `Ready=False` du workload ne signifie-t-il pas nécessairement que le fetch Git a échoué ?

## Progression sans solution immédiate

Voir [indices 02](../corriges/02-sources-reconciliation.md#indices), puis [corrigé 02](../corriges/02-sources-reconciliation.md#corrigé).
