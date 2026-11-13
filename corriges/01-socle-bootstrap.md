# Corrigé 01 — Socle et bootstrap

## Indices

1. Pars de `flux check` puis inspecte séparément les Deployments, la source Git et la Kustomization racine.
2. Compare la révision Git avec `status.artifact.revision` et la révision indiquée par la Kustomization.
3. Le bootstrap écrit les composants et la sync déclarative dans le chemin Git demandé ; il initialise également les contrôleurs sur le cluster.
4. Le kubeconfig/identité bootstrap est privilégié ; les opérations ultérieures ne devraient pas réutiliser cette autorité.

## Corrigé

La chaîne attendue est : CLI bootstrap → manifests versionnés dans Git → contrôleurs Flux et CRDs → GitRepository produit un artefact de révision → Kustomization consomme cet artefact et applique → status/conditions enregistrent le résultat. Le premier accès admin initialise le mécanisme ; il ne signifie pas que les mises à jour régulières doivent provenir d'un `kubectl apply`.

Une URL invalide rend la ressource Source non Ready : la Kustomization n'a pas d'artefact exploitable et n'applique pas le nouveau rendu. Un chemin invalide survient plus tard : la Source peut rester Ready tandis que la Kustomization échoue au build. Garder les commits et les status permet d'identifier le point de rupture.

`kubectl get pods` seul indique seulement l'état des conteneurs. Les conditions Flux et leur révision disent si le contrôleur a vu le commit et si le rendu a abouti.

