# Corrigé 04 - Helm en production

## Indices

1. Inspecter le HelmRelease, son chart/source référencé et l'historique release.
2. Chercher quel contrôleur a émis l'événement et la reason de la condition ; le statut peut montrer last attempted revision.
3. Les objets Helm ne sont pas toujours les seuls à modifier un champ ; l'autoscaling est un cas courant de conflit drift.

## Corrigé

Un chart source indisponible est une panne du chemin Source/HelmChart. Une valeur rejetée ou un hook peut faire échouer une action du helm-controller. Un test Helm échoué peut laisser une release installée mais marquer l'état de test en échec. La condition `Ready`, les raisons `Released`/`TestSuccess`, l'historique et les logs distinguent ces cas.

Le template `.spec.chart` peut mettre à jour chart et `values` dans une même spécification HelmRelease. Pour le chart de production, la référence et les valeurs doivent suivre une stratégie de versions approuvée ; ne pas utiliser `latest`. Les valeurs externes sont fusionnées selon l'ordre documenté et leurs sources doivent exister dans le namespace attendu.

Les limites de retries/remediation sont des garde-fous, pas une garantie de retour automatique à la version souhaitée dans tous les cas. Pour un upgrade bloqué, corriger la spec via Git, réconcilier, puis vérifier le release réellement installé. Configurer des exclusions de drift uniquement pour les champs effectivement possédés par un autre contrôleur.

