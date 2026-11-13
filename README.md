# Maîtriser Flux CD — parcours pratique orienté production

Formation avancée pour concevoir, exploiter, dépanner et expliquer une plateforme GitOps Flux de bout en bout. Le parcours s'appuie sur le syllabus KodeKloud pour le socle, puis approfondit les opérations de production et le fonctionnement interne des contrôleurs.

## Cible et prérequis

- **Flux ciblé : v2.10.0**, dernière version stable publiée le 26 octobre 2026 et vérifiée le 12 novembre 2026. Vérifier les versions et notes de version avant chaque nouvelle cohorte ou déploiement.
- API utilisées dans les exemples : `source.toolkit.fluxcd.io/v1`, `kustomize.toolkit.fluxcd.io/v1`, `helm.toolkit.fluxcd.io/v2`, `image.toolkit.fluxcd.io/v1`, `notification.toolkit.fluxcd.io/v1`.
- Kubernetes avancé, Git, Kustomize et notions Helm. Pour les labs, kind ou un cluster cloud, GitHub et une machine avec `kubectl`, `flux`, `git`, `cosign`, `sops` et `age`.
- Prérequis Kubernetes selon la documentation Flux consultée : Kubernetes 1.35 ou ultérieur. Contrôler cette exigence à nouveau pour le cluster et les versions utilisées.
- Environnement conseillé : kind dédié par personne ou cluster de test isolé. Les exercices de panne ne se font jamais sur la production.

**Estimation : 40 à 55 heures**, labs et projet fil rouge compris. Les modules 01–02 condensent la découverte KodeKloud ; la majorité du temps est consacrée aux phases maîtrise, expertise et dépannage.

## Progression

| Module | Phase | Sujet | Temps |
|---|---|---|---:|
| [01 — Socle et bootstrap](modules/01-socle-bootstrap.md) | 1 | GitOps, architecture, contrôleurs, bootstrap | 2 h |
| [02 — Sources et réconciliation](modules/02-sources-reconciliation.md) | 1 | Sources, artefacts, Kustomize, Helm, boucle de réconciliation | 4 h |
| [03 — Dépôts et livraison multi-environnements](modules/03-depots-environnements.md) | 2 | Structure, overlays, dépendances, santé, prune, drift, postBuild | 5 h |
| [04 — Helm en production](modules/04-helm-production.md) | 2 | HelmRelease, valeurs, remediation, tests et upgrades | 4 h |
| [05 — Secrets et chaîne de confiance](modules/05-secrets-signatures.md) | 2 | SOPS, age/KMS, cosign, provenance et menaces | 4 h |
| [06 — Images, notifications et webhooks](modules/06-automation-notifications.md) | 2 | Image automation, Alert/Provider, Receiver et Git write-back | 4 h |
| [07 — Multi-tenancy et flotte](modules/07-tenancy-flotte.md) | 3 | Impersonation, RBAC, isolation, management/target clusters | 5 h |
| [08 — Progressive delivery et cycle de vie](modules/08-livraison-cycle-vie.md) | 3 | Flagger, Operator, installation à l'échelle, upgrades Flux | 5 h |
| [09 — Visibilité et fonctionnement interne](modules/09-internals-observabilite.md) | 3 | Métriques, UI, artefacts, caches, controller-runtime, lecture Go | 5 h |
| [10 — Game day de dépannage](modules/10-game-day.md) | 4 | Pannes croisées, diagnostic structuré et postmortem | 4–6 h |
| [Bilan et projets](bilan.md) | — | Checklist de compétences et prolongements | 1 h |

Chaque module contient objectifs, pourquoi, lab concret, critères de réussite, pannes volontaires, questions de compréhension et lien vers **indices**, puis vers le corrigé séparé. Ne consultez les corrigés qu'après avoir consigné vos observations.

## Projet fil rouge

Construire un dépôt de plateforme depuis le [squelette de départ](depot-exemple/), puis le faire évoluer : bootstrap GitHub → infrastructure → workloads et environnements → secrets chiffrés → images automatisées → alertes et observabilité → isolation multi-tenant → flotte de clusters → releases progressives. À la fin, les changements ordinaires sont proposés et appliqués par Git. `kubectl` sert à observer et diagnostiquer, pas à appliquer des ressources.

Le seul acte initial est l'amorçage : `flux bootstrap` exige une identité Kubernetes d'administration et les droits nécessaires pour écrire les fichiers de bootstrap dans Git. Ensuite, le dépôt devient la source de vérité, y compris pour les modifications et mises à jour de Flux. Ne bootstrappez jamais un cluster partagé sans autorisation.

## Comment utiliser les fichiers

1. Lire le module, noter vos hypothèses et créer un cluster de lab isolé.
2. Copier le contenu de `depot-exemple/` dans **votre propre dépôt Git**. Ne pas traiter ce dossier de cours comme une cible bootstrap directement.
3. Initialiser GitHub et exécuter le bootstrap indiqué au module 01 ; ne pas créer les ressources Flux avec `kubectl apply`.
4. Réaliser les étapes par commits. Utiliser `kubectl` en lecture seule et `flux reconcile` pour demander une réconciliation.
5. Pour chaque panne : observer les conditions, événements, journaux et révisions avant de corriger. Les causes attendues ne sont indiquées que dans `corriges/`.
6. Après un module, expliquer le chemin d'une modification Git jusqu'à l'objet Kubernetes, les statuts attendus et les limites de l'architecture.

## Références de départ

- [Documentation officielle Flux](https://fluxcd.io/flux/)
- [Installation et bootstrap](https://fluxcd.io/flux/installation/)
- [Composants et API GitOps Toolkit](https://fluxcd.io/flux/components/)
- [Kustomize Kustomization](https://fluxcd.io/flux/components/kustomize/kustomizations/)
- [HelmRelease](https://fluxcd.io/flux/components/helm/helmreleases/)
- [Sécurité Flux](https://fluxcd.io/flux/security/)
- [Vérification de signatures OCI avec Cosign](https://fluxcd.io/flux/components/source/ocirepositories/#verification)
- [Multi-tenancy Flux](https://fluxcd.io/flux/installation/configuration/multitenancy/)
- [Monitoring Flux](https://fluxcd.io/flux/monitoring/)
- [Exemple multi-environnements Kustomize/Helm](https://github.com/fluxcd/flux2-kustomize-helm-example)
- [Exemple multi-tenancy](https://github.com/fluxcd/flux2-multi-tenancy)
- [Exemple officiel monitoring Prometheus/Grafana](https://github.com/fluxcd/flux2-monitoring-example)
- [KodeKloud — GitOps with FluxCD](https://kodekloud.com/courses/gitops-with-fluxcd)
- [Flux releases](https://github.com/fluxcd/flux2/releases)
- [Flux Operator](https://github.com/controlplaneio-fluxcd/flux-operator) et [Flux Web UI](https://fluxoperator.dev/web-ui/) — projets d'écosystème distincts des contrôleurs Flux standards.
- [Flagger](https://docs.flagger.app/) — projet CNCF de progressive delivery.

**Note de nommage des dépôts :** l'URL `fluxcd/flux2-kustomize-example` indiquée dans la demande renvoie 404 au contrôle ; le dépôt d'exemple officiel disponible est `fluxcd/flux2-kustomize-helm-example`, utilisé comme référence pour ce cours.

Les exemples sont rédigés contre les API mentionnées ci-dessus. La documentation web évolue indépendamment du présent cours : avant un usage production, vérifier les CRD installées, les versions compatibles, le schéma (`flux install --export` ou manifestes de la version retenue) et les notes de version. Les commandes de signature, d'identité cloud et de bootstrap doivent être adaptées au fournisseur et au modèle de menace de l'organisation.
