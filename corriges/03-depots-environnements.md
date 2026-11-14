# Corrigé 03 - Dépôts et environnements

## Indices

1. Dessine les limites d'accès et les limites de transaction avant de choisir la forme des dépôts.
2. Une `dependsOn` cible une Kustomization Flux, et la dépendance doit satisfaire son critère de santé.
3. Ne fais pas de `prune` sur un état persistant pendant l'apprentissage ; vérifie les événements et l'inventaire d'abord.
4. Une variable absente dans `substituteFrom` ou dans le dictionnaire de substitution est remplacée par une chaîne vide (sauf `${VAR:=defaut}`), ce qui peut produire un rendu valide mais faux ; inspecter le manifest rendu et les logs du build.

## Corrigé

Monorepo apporte des changements atomiques et une vue complète, avec un périmètre d'accès plus large ; multi-repos segmente mieux les accès et équipes mais rend la promotion et le suivi cross-repo plus complexes. Il n'y a pas de choix universel : documenter propriété des chemins, revue et mécanisme de promotion.

`dependsOn` ordonne les réconciliations et attend que les objets dépendants soient Ready selon leurs checks ; les étapes ne sont pas transactionnelles. Une partie des ressources peut déjà être appliquée si une étape ultérieure échoue.

Un rollback Git remet les manifests à une révision, mais n'annule pas les migrations de données ni les effets externes. La suspension arrête l'application des nouvelles révisions et la correction de drift pour la Kustomization ; elle n'est pas une stratégie de rollback. La suppression d'un objet Kustomization combinée à une politique de prune doit être évaluée avant l'opération.

Utiliser les patches pour les transformations structurelles; `postBuild` pour les valeurs d'environnement explicites. Garder les secrets chiffrés et éviter d'imprimer les valeurs substituées sensibles.

