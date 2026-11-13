# Corrigé 09 — Internals et observabilité

## Indices

1. Choisir un seul contrôleur/version, puis suivre son entrypoint, l'enregistrement du reconciler et `Reconcile`.
2. Comparer `metadata.generation`, `status.observedGeneration`, révision source et digest artefact.
3. Vérifier les URLs d'artefact et les métriques du contrôleur source avant de suspecter Kustomize.

## Corrigé

Un contrôleur observe objets et événements Kubernetes, puis requeue périodiquement ou après succès/erreur. Il ne maintient pas une connexion permanente à chaque dépôt. Source-controller produit un artefact référencé ; les consommateurs prennent cette révision et réalisent leurs opérations propres.

`observedGeneration` dit quelle génération de la spec l'objet a traitée ; la révision source indique les données d'entrée. Les deux sont nécessaires pour confirmer que le dernier changement a été pris en compte.

Un digest inchangé tend à montrer que le contenu de l'artefact source est identique ; il ne démontre ni que tous les consommateurs l'ont appliqué ni que l'application runtime est saine. Métriques globales montrent saturation ou erreurs, mais les événements/logs et CRD identifient la ressource fautive.

Pour le code Go, citer la version/tag inspectée et suivre le `Reconcile` plutôt que généraliser à tous les contrôleurs. Les méthodes communes controller-runtime n'annulent pas les différences du pipeline de chaque composant.

