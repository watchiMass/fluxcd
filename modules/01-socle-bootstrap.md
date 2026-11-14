# 01 - Socle GitOps et bootstrap

**Phase : socle · Durée : 2 h**

## Objectifs

- Distinguer GitOps, CI, CD et le modèle push/pull.
- Expliquer les rôles du CLI, de l'API Kubernetes et des contrôleurs Flux.
- Amorcer Flux sur un cluster de lab GitHub et localiser l'état de bootstrap.
- Décrire la boucle de rapprochement à partir d'une modification Git.

## Notions à comprendre - et pourquoi

- **État désiré déclaratif** : Git contient la déclaration versionnée, tandis que le contrôleur réconcilie l'état réel. Cela fournit audit, répétabilité et reprise après panne ; ce n'est pas une garantie qu'un commit soit correct.
- **Pull et convergence** : les contrôleurs extraient le dépôt et observent Kubernetes. Le réseau entrant depuis CI vers l'API du cluster n'est pas nécessaire pour chaque déploiement.
- **Séparation des responsabilités** : source-controller produit les Sources/artefacts ; kustomize-controller applique les ressources ; helm-controller pilote les actions Helm ; les autres contrôleurs couvrent notifications, images et automatisation.
- **Bootstrap** : une action privilégiée installe les contrôleurs et leur configuration de sync dans Git. Les futures modifications de Flux peuvent ensuite être livrées par Git. Comprendre l'exception bootstrap évite de prétendre que l'installation initiale n'a aucun accès direct au cluster.
- **Reconciliation, pas pipeline linéaire** : événements et intervalles réveillent les contrôleurs ; l'idempotence et les retries tolèrent les erreurs temporaires.
- **ConfigMap monté en volume** : Flux met à jour l'objet ConfigMap, mais ce sont les Pods qui doivent relire le fichier. Un volume ConfigMap est rafraîchi par le kubelet avec un délai ; `subPath` ne reçoit pas ces mises à jour et les variables d'environnement ne changent qu'au redémarrage du Pod.

## Lab - bootstrap et première livraison Git

1. Suivre les prérequis du [squelette](../depot-exemple/README.md), créer un cluster kind isolé et un dépôt GitHub privé.
2. Installer une version CLI explicite `v2.10.0`, contrôler les prérequis avec `flux check --pre`, puis examiner `flux bootstrap github --help`.
3. Bootstrapper dans `clusters/lab` avec `flux bootstrap github --owner=<...> --repository=<...> --branch=main --path=clusters/lab`. Ajoutez `--personal` si le propriétaire est un compte GitHub personnel ; omettez-le pour une organisation. Le PAT (droits d'administration du dépôt) sert au bootstrap et reste local ; Flux utilise par défaut une deploy key SSH en lecture seule, dont la clé privée est stockée dans le Secret `flux-system`. Garder la branche principale protégée et conserver tout credential hors du dépôt.
4. Vérifier le commit de bootstrap et les fichiers générés sous `clusters/lab/flux-system/` (`gotk-components.yaml`, `gotk-sync.yaml`, `kustomization.yaml`), les Deployments contrôleurs, le `GitRepository/flux-system` et le `Kustomization/flux-system`. Votre `clusters/lab/kustomization.yaml` initial est conservé : il référence le dossier `flux-system/` généré.
5. Dans votre dépôt (pas le dossier du cours), modifier `apps/lab/configmap.yaml` et committer. Suivre le changement depuis la nouvelle révision source jusqu'à l'objet ConfigMap appliqué, puis jusqu'au fichier monté dans le Pod. `flux reconcile kustomization lab-apps -n flux-system` accélère la réconciliation.
6. Enregistrer un diagramme de la chaîne et noter quel composant détient chaque responsabilité.

Commandes d'observation possibles : `flux check`, `flux get sources git -A`, `flux get kustomizations -A`, `flux events -A`, `kubectl -n flux-system get deploy,pods`, `kubectl -n flux-system describe gitrepository flux-system`.

## Critères de réussite

- Tous les contrôleurs attendus sont Ready et l'état de sync signale une révision Git précise.
- Le changement du ConfigMap est visible dans l'objet Kubernetes puis, après le délai de propagation du kubelet (généralement moins de deux minutes), dans `/usr/share/nginx/html/index.html` du Pod, sans `kubectl apply` ni redémarrage manuel.
- Vous pouvez expliquer pourquoi bootstrap nécessite une autorité initiale, et ce qui doit ensuite vivre dans Git.
- Les fichiers générés de bootstrap sont compris (sans divulguer de credentials).

## Pannes volontaires

Sur un lab jetable, introduire une URL Git invalide, puis un chemin source inexistant. Pour chacune, capturer `flux get`, `flux logs`, `flux events` et `kubectl describe` avant réparation. Quelle ressource échoue en premier ? Quel statut est plus utile que le seul état des Pods ?

## Questions de compréhension

1. Pourquoi Flux est-il un opérateur de convergence plutôt qu'un ordonnanceur de tâches ?
2. Quel est le rôle de `GitRepository.status.artifact` et où Kustomize trouve-t-il son entrée ?
3. À quoi sert le commit créé par bootstrap et comment éviter qu'il soit supprimé par inadvertance ?
4. Que se passe-t-il si la source Git est temporairement inaccessible ?
5. Pourquoi le fichier monté peut-il rester inchangé quelques minutes après que le ConfigMap est déjà à jour ?

## Progression sans solution immédiate

Consultez [les indices 01](../corriges/01-socle-bootstrap.md#indices), puis, seulement après le lab, [le corrigé 01](../corriges/01-socle-bootstrap.md#corrigé).

