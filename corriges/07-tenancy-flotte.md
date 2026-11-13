# Corrigé 07 — Multi-tenancy et flotte

## Indices

1. Vérifier les droits effectifs de l'identité impersonnée, pas uniquement le rôle du contrôleur.
2. Essayer de créer un objet refusé par le serveur avec des logs d'audit ou conditions adaptées.
3. Vérifier les flags du contrôleur et les références namespace par namespace.

## Corrigé

Un namespace limite plusieurs noms d'objets, mais n'interdit pas à lui seul les ressources cluster-scoped, les références cross-namespace, les appels vers une source distante ou des droits excessifs. L'impersonation via ServiceAccount contraint la création/édition selon le RBAC associé ; les ressources plateforme et CRD doivent rester sous responsabilité admin.

Le lockdown doit inclure le refus des références inter-namespace, les remote bases et un compte par défaut non privilégié, avec les exceptions explicitement contrôlées pour les ressources plateforme. Les flags disponibles dépendent des versions : la documentation Flux v2.10 mentionne notamment une nouveauté `source-watcher` pour `--default-service-account`; ne pas la copier dans une installation plus ancienne.

Dans une flotte, chaque cible doit avoir une identité et une autorisation source définies. Les secrets partagés multiplient le rayon d'impact d'une fuite. La séparation management/target peut aider à déléguer, mais rend la perte du management cluster critique : documenter la reconstruction et le mode de récupération.

