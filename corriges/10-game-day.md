# Corrigé 10 - Game day

## Indices

1. Lire les ressources Flux avec `Ready=False`, `Stalled=True`, `Reconciling=True` et `observedGeneration`.
2. La première ressource non Ready dans la chaîne source → Kustomization/HelmRelease → workload est souvent la meilleure piste.
3. `flux trace` montre les relations de dépendance/consommation ; `describe` apporte conditions et événements Kubernetes.
4. Corréler horodatage et commit afin de ne pas confondre anciennes erreurs avec état actuel.

## Corrigé

Un diagnostic robuste suit cet ordre :

1. Confirmer le cluster/contexte et le commit attendu.
2. Vérifier GitRepository/OCIRepository/Helm source, authentification, révision, artefact et digest.
3. Vérifier la Kustomization/HelmRelease : génération observée, dépendances, path/sourceRef, build, values, decryption, RBAC et conditions.
4. Vérifier les objets résultants via `kubectl describe`, ReplicaSets, Pods et events ; déterminer si l'application a réellement servi.
5. Vérifier que notification/monitoring n'est pas elle-même la panne, tout en séparant observabilité et livraison.
6. Faire une correction/rollback Git minimal, suivre nouvelle révision et confirmer Ready dans Flux et santé du workload.

Le dossier de game day n'a pas un unique corrigé de scénario : les causes varient. Le résultat attendu est une chronologie vérifiable, hypothèses testées, changement Git responsable, preuve de rétablissement et action préventive. Une simple sortie « Ready=True » sans révision ni test d'application ne suffit pas.

