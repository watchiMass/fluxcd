# 04 — HelmRelease en production

**Phase : maîtrise · Durée : 4 h**

## Objectifs

- Concevoir un `HelmRelease` v2 avec valeurs propres à l'environnement et valeurs externes.
- Choisir entre un chart référencé et le chart généré par `.spec.chart`.
- Tester install, upgrade, test hooks, remediation, rollback et drift Helm.
- Déboguer un release qui échoue sans contourner le contrôle Git.

## Notions à comprendre — et pourquoi

- **Source de chart** : HelmRepository/HelmChart convient aux dépôts chart ; OCIRepository convient aux OCI registries. Le choix du type se voit dans le schéma, la provenance et le statut du chart.
- **Valeurs** : `values` (inline) et `valuesFrom` (ConfigMap/Secret) ont ordre de fusion et `targetPath`; des valeurs découpées favorisent la réutilisation, mais rendent le rendu distribué. Garder la source et les versions déterministes.
- **Remediation** : les stratégies/retries d'install et upgrade modèlent les échecs. Elles n'assurent pas que l'application est saine ; définir aussi tests Helm et health assessment.
- **Tests et drift** : les tests Helm peuvent échouer après installation/upgrade ; la détection/correction de drift concerne les objets de la release. Évaluer les interactions avec les controllers externes qui modifient ces champs.
- **Upgrade** : valeurs incompatibles, CRD, hooks, timeouts et stratégie d'échec sont les causes fréquentes de releases bloqués. `HelmRelease.status` et l'historique Helm sont des données de diagnostic.

## Lab — release d'application et matrice de défaillances

1. Déployer un chart Podinfo via OCIRepository ou HelmRepository et `HelmRelease` API `helm.toolkit.fluxcd.io/v2`.
2. Mettre les valeurs communes dans `values`, les différences staging/prod dans patches, et une valeur externe non sensible dans un ConfigMap `valuesFrom`.
3. Activer `test.enable`, choisir un intervalle/timeout raisonnable, et documenter la stratégie de remediation pour install et upgrade.
4. Faire un upgrade valide, puis tester un upgrade volontairement invalide dans un chart de lab ; enregistrer `helm-controller` logs, événements, conditions et historique release.
5. Tester les modes de drift detection en espace jetable. Vérifier quelles annotations/paths vous excluez et justifier chaque exception.
6. Corriger par Git, réconcilier et vérifier la révision finale. Ne pas éditer les ressources Helm possédées directement.

## Critères de réussite

- Le HelmRelease Ready expose le chart/révision réellement installé et les tests attendus.
- Vous savez retrouver la différence entre chart indisponible, action Helm échouée et test de release échoué.
- Une panne d'upgrade n'entraîne pas de boucle destructive inattendue.
- Les valeurs d'environnement et secrets n'existent qu'aux emplacements décidés.

## Pannes volontaires

Casser la version du chart, une clé de values, un Secret `valuesFrom` requis, le timeout, puis un test hook. Pour chaque cas, déterminer si le défaut vient de source-controller, helm-controller, de l'application ou du chart.

## Questions de compréhension

1. Pourquoi un `.spec.chart` peut-il rendre une mise à jour de version et de valeurs cohérente ?
2. Quelles données permettent de départager une tentative échouée de l'état actuellement déployé ?
3. Comment éviter que drift correction de Helm entre en conflit avec un HPA ?
4. Quel est le comportement de la remediation lorsque les retries sont épuisés ?

## Progression sans solution immédiate

Voir [indices 04](../corriges/04-helm-production.md#indices), puis [corrigé 04](../corriges/04-helm-production.md#corrigé).
