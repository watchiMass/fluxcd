# 07 - Multi-tenancy et gestion de flotte

**Phase : expertise · Durée : 5 h**

## Objectifs

- Construire un périmètre d'équipe limité à un namespace et à un service account.
- Comprendre l'impersonation et ses effets sur l'application effective des ressources.
- Séparer les responsabilités du cluster management et des clusters cibles.
- Concevoir onboarding, politiques et stratégie multi-clusters.

## Notions à comprendre - et pourquoi

- **RBAC du contrôleur** : les contrôleurs démarrent souvent avec des permissions larges pour l'administration de plateforme. Une équipe ne doit pas hériter implicitement de ces droits.
- **Impersonation** : `spec.serviceAccountName` de Kustomization/HelmRelease limite l'application aux droits Kubernetes de ce ServiceAccount. Le contrôleur doit autoriser l'identité et les références de namespaces sont à verrouiller.
- **Lockdown** : considérer `--no-cross-namespace-refs`, `--no-remote-bases`, compte par défaut non privilégié et RBAC d'onboarding. La configuration exacte varie selon les composants et la version.
- **Management vs target** : le cluster management garde la définition de flotte ; les sources produisent des artefacts réutilisables. Les clusters cibles ont leur propre identité, credentials et source de vérité explicites.
- **Sécurité d'accès** : credentials et clés ne doivent pas être copiés indistinctement entre clusters. Workload identity réduit les secrets statiques ; valider le blast radius d'une compromission.

## Lab - deux équipes, deux clusters (ou deux contextes isolés)

1. Créer deux namespaces/équipes avec ServiceAccounts et RBAC minimaux : une équipe peut gérer ses workloads, pas namespaces, CRD, RBAC ou objets d'autrui.
2. Configurer Flux de lab en mode multi-tenant lockdown selon la doc v2.10.0. Auditer les arguments des contrôleurs, les ClusterRoles et références inter-namespace.
3. Onboarder les manifests via des ressources Flux contrôlées par l'équipe plateforme ; appliquer les workloads sous le service account de tenant.
4. Tenter depuis le Git tenant une CRD cluster-scoped, une ressource d'un autre namespace et un remote base. Chaque tentative doit être refusée à la frontière prévue.
5. Configurer un cluster secondaire jetable (ou au minimum modéliser les identités distinctes) et déployer une version avec une Kustomization par cluster.
6. Documenter récupération, révocation des credentials et procédure de retrait d'un cluster compromis.

## Critères de réussite

- Une équipe ne peut pas appliquer de ressource hors de son périmètre, même si sa PR est acceptée.
- Les identités de pull/target et les droits du contrôleur sont documentés et testés.
- Chaque cluster a un inventaire de version Flux, une source et une politique de promotion.
- Un cluster déconnecté n'empêche pas la réconciliation des autres.

## Pannes volontaires

Retirer un droit `create`, ajouter une référence source cross-namespace, essayer un manifest cluster-scoped sous le tenant, désactiver l'accès au dépôt d'un cluster cible. Distinguer erreur Git/source, interdiction RBAC et indisponibilité de cluster.

## Questions de compréhension

1. Pourquoi le namespace seul ne constitue-t-il pas une isolation suffisante ?
2. Quelles opérations restent réservées à la plateforme ?
3. Quel compromis entre centraliser la flotte et installer Flux séparément sur chaque cluster ?
4. Quel est le blast radius d'un token Git partagé par toute la flotte ?

## Progression sans solution immédiate

Voir [indices 07](../corriges/07-tenancy-flotte.md#indices), puis [corrigé 07](../corriges/07-tenancy-flotte.md#corrigé).
