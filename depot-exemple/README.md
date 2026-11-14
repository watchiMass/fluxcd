# Squelette du dépôt de lab

Ce dossier est un **point de départ à copier dans un dépôt GitHub personnel** (ou à forker depuis le projet de formation). Il n'est pas le dépôt de support lui-même. Il contient les fichiers de synchronisation du cluster, et non les fichiers générés de bootstrap.

## Structure

```text
clusters/
  lab/
    kustomization.yaml       # racine réconciliée par la Kustomization de bootstrap
    apps.yaml                # inscrit la première app depuis Git
    flux-system/             # généré et versionné par flux bootstrap
apps/
  lab/
    kustomization.yaml
    namespace.yaml
    configmap.yaml
    deployment.yaml
```

Le nom `flux-system` désigne la GitRepository créée par le bootstrap officiel. Vérifier le contenu généré pour votre version CLI plutôt que de fabriquer ces fichiers à la main.

## Démarrage sans kubectl apply

1. Copier **le contenu** de ce dossier dans un dépôt dédié et créer/committer la branche `main` avec `clusters/lab/kustomization.yaml` et les manifests.
2. Vérifier le contexte `kubectl config current-context` et le cluster jetable.
3. Installer la CLI `v2.10.0` et vérifier `flux check --pre`. Le PAT d'administration GitHub sert au bootstrap seulement ; ne pas le committer. La commande utilise par défaut une deploy key SSH pour la lecture du dépôt.
4. Depuis une identité explicitement autorisée, exécuter `flux bootstrap github --owner=<organisation-ou-compte> --repository=<depot> --branch=main --path=clusters/lab`. Ajouter `--personal` si le propriétaire est un compte GitHub personnel. La commande installe les contrôleurs et pousse les manifests de bootstrap. Elle est l'exception initiale au cycle Git ; ne pas l'exécuter sur un cluster partagé sans approbation.
5. Vérifier la révision sync et les conditions `Ready`. Le bootstrap conserve votre `clusters/lab/kustomization.yaml` et ajoute `clusters/lab/flux-system/` (`gotk-components.yaml`, `gotk-sync.yaml`, `kustomization.yaml`). Toutes les modifications suivantes se font via Git.

N'utilisez pas `kubectl apply`, `kubectl create` ou un éditeur direct sur les objets gérés pour les étapes du cours. Les commandes `kubectl get`, `describe` et logs restent disponibles pour observer. L'ajout d'un Secret initial pour le bootstrap ou une clé de déchiffrement est un sujet de sécurité à traiter explicitement ; ne stockez jamais la valeur en clair dans ce dépôt.

## Extension progressive

Commencer avec `apps/lab`, puis ajouter `infrastructure/controllers`, `infrastructure/configs`, `apps/base`, `apps/staging`, `apps/production`, `clusters/staging` et `clusters/production`. Le module 03 demande de définir les frontières et graphe avant d'étendre ce squelette.

Le ConfigMap `web-content` est monté en volume dans nginx, sans `subPath`, pour que la modification de `index.html` soit observable dans le Pod après le délai de propagation du kubelet. Un montage `subPath` ou une variable d'environnement ne se mettrait pas à jour de la même façon.
