# Corrigé 08 — Progressive delivery et cycle de vie

## Indices

1. Flagger s'occupe du trafic et de l'analyse du canary ; Flux déclare et réconcilie les objets Kubernetes.
2. Les données métriques et le fournisseur de routage doivent être valides avant d'évaluer un rollout.
3. Un upgrade Flux inclut contrôleurs, CRDs, valeurs/flags et versions Kubernetes supportées.

## Corrigé

Flux livre l'installation de Flagger, son provider et la ressource Canary ; Flagger analyse le rollout d'application et choisit promotion ou arrêt/rollback selon la configuration. Flux ne remplace pas le contrôleur de progressive delivery.

Le choix d'Operator dépend du besoin d'automatiser la gestion/lifecycle, de l'architecture fleet, de l'API/contrat supporté et de la capacité de l'équipe à opérer ce composant supplémentaire. Flux Operator est un projet d'écosystème séparé, même s'il gère Flux.

Une montée de version fiable commence par lire release notes et compatibilité, tester le rendu et la migration CRD en lab, puis mettre à jour un cluster canari et comparer readiness, erreurs et latence. Continuer par anneaux avec arrêt/rollback explicites. `flux bootstrap` est idempotent et peut mettre à niveau l'installation ; cela ne dispense pas de planifier validation, changements de schéma et retour arrière.

