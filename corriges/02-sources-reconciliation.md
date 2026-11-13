# Corrigé 02 - Sources et réconciliation

## Indices

1. Une Source a un statut/artéfact indépendant du statut de chaque consommateur.
2. Identifier la dernière étape visible dans les événements : fetch, build, validation, apply, health.
3. Contrôler `observedGeneration`, `status.artifact.revision`, l'inventaire et les événements du contrôleur.

## Corrigé

`GitRepository` exprime où et quelle révision Git suivre ; source-controller fabrique un artefact, expose sa révision/digest et le publie pour les autres contrôleurs. `OCIRepository`, `Bucket` et les sources Helm produisent des artefacts depuis d'autres systèmes. Une `HelmRepository` indexe les charts, tandis que `HelmChart` ou `OCIRepository` fournit le chart au Helm Controller.

Le Flux `Kustomization` pilote une pipeline de déploiement, et `kustomization.yaml` déclare les ressources d'un build Kustomize : ce sont deux objets distincts. Dans le parcours, on corrèle la révision du Source, le build et le statut du consommateur.

`prune: true` permet de supprimer les ressources précédemment suivies absentes du nouveau rendu. Le controller maintient un inventaire ; un retrait du manifest peut donc être une suppression réelle. Les erreurs source ne sont pas réparées par une modification du Pod, et un health check échoué peut suivre un apply accepté.

