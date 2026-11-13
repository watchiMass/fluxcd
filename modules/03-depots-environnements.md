# 03 - Dépôts, environnements et graphe de livraison

**Phase : maîtrise · Durée : 5 h**

## Objectifs

- Concevoir un monorepo ou un modèle multi-repos avec limites de responsabilité explicites.
- Organiser bases, overlays et paramètres de clusters sans duplication incontrôlée.
- Créer des étapes Flux ordonnées via `dependsOn`, et définir la notion de santé utile.
- Manipuler `prune`, `deletionPolicy`, suspension, drift, reprise, rollback et `postBuild`.

## Notions à comprendre - et pourquoi

- **Organisation du dépôt** : un monorepo simplifie la cohérence atomique ; plusieurs dépôts réduisent les périmètres d'accès et les collisions de changements, au coût de coordination et de traçabilité inter-repos.
- **Base/overlay** : Kustomize évite la duplication tout en rendant les écarts d'environnement visibles. Une base trop générique ou des patches empilés obscurcissent la configuration effective.
- **Graphe de Kustomizations** : `dependsOn` séquence des réconciliations ; il ne rend pas les opérations transactionnelles. Définir les dépendances sur les APIs/CRD et composants réellement nécessaires.
- **Health checks** : « apply accepté » n'est pas « service prêt ». Les checks et `healthCheckExprs` doivent représenter le critère métier/plateforme attendu, avec des timeouts réalistes.
- **Prune et suppression** : le garbage collection concerne les objets précédemment suivis qui disparaissent du rendu. `.spec.deletionPolicy` règle la suppression de la Kustomization elle-même ; conserver un état de sauvegarde/récupération pour les ressources stateful.
- **Drift/suspend/revert** : suspension arrête apply et drift correction ; un rollback Git rétablit une version déclarée, pas nécessairement les données ou les effets externes.
- **Substitution** : `postBuild.substitute`/`substituteFrom` apporte des valeurs d'environnement après le build ; ne pas y injecter de secrets en clair. Kustomize patches restent préférables pour des différences structurantes.

## Lab - staging et production, infra avant apps

1. Étendre le squelette en `apps/base`, `apps/staging`, `apps/production`, `infrastructure/controllers` et `infrastructure/configs`; écrire les règles de promotion dans un ADR court.
2. Créer une Kustomization pour un namespace/configuration de plateforme et une Kustomization d'application dépendante via `dependsOn`.
3. Créer des overlays staging/prod qui dérivent d'une base commune ; utiliser patches ciblés pour replicas/ressources/hostnames.
4. Ajouter une substitution non secrète, par exemple `CLUSTER_ENV`/`CLUSTER_NAME`, via ConfigMap versionnée et `postBuild.substituteFrom`. Observer exactement le manifest rendu et contrôler les `${...}` résiduels.
5. Tester le `healthChecks` sur un Deployment puis la dépendance d'app sur une CRD opérateur. Rendre l'ordre et les critères de santé vérifiables.
6. Faire une modification non destructive puis un commit de rollback ; comparer les révisions et le temps de convergence.
7. En environnement jetable, activer prune, retirer un objet de test, puis examiner l'inventaire et l'événement. Ne pas employer une PVC/CRD partagée pour cet exercice.
8. Lancer `flux suspend kustomization <nom> -n <namespace>`, provoquer un drift contrôlé et confirmer qu'il persiste ; reprendre avec `flux resume ...` et mesurer le retour à l'état Git.

## Critères de réussite

- Un changement commun et un changement spécifique produisent les manifests attendus en staging et prod.
- L'app ne progresse pas avant le prérequis et devient Ready après.
- Les valeurs substituées sont auditables et aucun secret n'est affiché.
- Vous expliquez les conséquences de la suppression d'une Kustomization pour chaque `deletionPolicy`.

## Pannes volontaires

En lab : supprimer un prérequis de `dependsOn`, rendre une référence `substituteFrom` absente, marquer une ressource comme non saine, retirer un manifest géré avec prune actif, puis suspendre une Kustomization après une modification manuelle. Diagnostiquer avant de rétablir les fichiers Git.

## Questions de compréhension

1. Quand choisiriez-vous plusieurs dépôts plutôt qu'un seul ?
2. `dependsOn` fournit-il une transaction atomique ? Quel échec partiel demeure possible ?
3. Quelle différence entre rollback du commit et rollback de l'état réel ?
4. Qui protège les ressources stateless et stateful de la suppression accidentelle ?
5. Quand un patch Kustomize est-il plus clair qu'une variable `postBuild` ?

## Progression sans solution immédiate

Voir [indices 03](../corriges/03-depots-environnements.md#indices), puis [corrigé 03](../corriges/03-depots-environnements.md#corrigé).
