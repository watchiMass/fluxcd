# Bilan - maîtrise Flux

## Checklist de compétences

### Socle

- [ ] Je sais décrire les flux d'identité, données et artefacts entre Git, registries, Flux et Kubernetes.
- [ ] Je sais bootstrapper un cluster isolé, examiner ce qui a été écrit dans Git et justifier l'autorité initiale.
- [ ] Je distingue Source CRD, artefact source, `Kustomization` Flux et fichier Kustomize.
- [ ] Je sais expliquer les retries, intervalles, événements, génération observée et conditions.

### Conception et opérations

- [ ] Je peux justifier mono-repo/multi-repo, overlays et frontières d'environnements.
- [ ] Je construis un graphe `dependsOn` avec health checks utiles et limites explicites.
- [ ] Je sais contrôler prune, deletionPolicy, inventaire, suspension, drift et récupération.
- [ ] Je conçois un HelmRelease v2 avec sources, values, tests, remediation, timeouts et stratégie d'upgrade.
- [ ] Je sais maintenir SOPS, rotation de clés et stockage sécurisé des Secrets déchiffrés.
- [ ] Je sais expliquer les limites d'une signature et les vérifier contre une identité/digest attendus.
- [ ] Je conçois l'automatisation d'images sans confondre sélection, approbation et déploiement.
- [ ] Je protège Alert, Receiver, tokens Git et endpoints webhook.

### Expertise

- [ ] Je peux défendre une frontière de tenant par RBAC, impersonation et politique de contrôleur.
- [ ] Je sais décrire une architecture management/targets, credentials distincts et blast radius.
- [ ] Je peux opérer un canary basé sur trafic, métriques et critères de promotion/rollback.
- [ ] Je sais choisir entre bootstrap CLI et Operator avec des critères opérationnels.
- [ ] Je sais élaborer et exécuter un plan d'upgrade Flux versionné.
- [ ] Je peux tracer le `Reconcile` d'un contrôleur Go et lier l'implémentation aux statuts observés.
- [ ] Je sais utiliser métriques, logs, événements et UI avec des droits adaptés.

### Dépannage

- [ ] Je diagnostique une chaîne de livraison depuis la source jusqu'au Pod en m'appuyant sur des preuves.
- [ ] Je sais utiliser `flux get`, `flux logs`, `flux trace`, `flux events` et `kubectl describe`.
- [ ] Je restaure l'état par Git et vérifie la révision, la santé et le temps de convergence.
- [ ] Je produis un postmortem avec cause racine et prévention mesurable.

## Projet final de certification personnelle

Présenter en 20–30 minutes une plateforme GitOps sur un cluster de lab : choix du dépôt, bootstrap, graphe d'infra/apps, promotions entre environnements, gestion des secrets, contrôle de supply chain, alertes, observabilité, droits tenant, scénario de panne et mise à jour Flux. Rejouer le même dépôt sur un deuxième cluster sans intervention manuelle sur les workloads.

**Barre de réussite suggérée :** tout changement de workload passe par Git ; les versions et identités sont explicites ; aucun secret lisible n'est commité ; un tenant ne dépasse pas son RBAC ; une panne se diagnostique avec des preuves et se corrige par Git.

## Idées de projets pour aller plus loin

- Promouvoir un artefact OCI signé entre environnements au moyen de digests immuables et de politiques d'admission.
- Concevoir une flotte multi-région avec clusters éphémères, stratégie de mise à jour par anneaux et limitation de débit.
- Ajouter des tests de rendu Kustomize/Helm et politiques Kyverno/Gatekeeper au pipeline de PR, sans transformer la CI en deuxième source de vérité.
- Automatiser SOPS avec KMS et workload identity, incluant la rotation et la reprise après perte d'identité.
- Développer une extension GitOps Toolkit à partir des bibliothèques Go Flux, puis documenter garanties et limites.
- Construire un SLO de convergence et un dashboard multi-cluster qui respecte l'isolation des équipes.

