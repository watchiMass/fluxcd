# Corrigé 05 — Secrets et signatures

## Indices

1. SOPS chiffre dans Git ; le déchiffrement est réalisé lors du traitement Flux de la Kustomization.
2. Vérifie la clé privée dans l'environnement de déchiffrement sans jamais la dumper dans le terminal partagé ou les logs.
3. Une vérification cosign doit valider à la fois le digest et l'identité/émetteur attendus.

## Corrigé

La clé privée doit être accessible au controller dans le namespace et selon le mécanisme spécifié par la configuration SOPS. Le ciphertext est suivi dans Git ; après déchiffrement, le Secret résultant est stocké dans Kubernetes. Protéger l'API, le RBAC, le chiffrement etcd, les sauvegardes et l'accès opérateur est donc toujours nécessaire.

Pour une rotation age, ajouter le nouveau recipient, chiffrer/committer la nouvelle version, déployer la clé privée de lecture puis confirmer le déchiffrement avant de retirer l'ancien recipient. Une rotation KMS dépend des identités et permissions KMS/workload identity et doit être testée comme une panne d'accès externe.

Pour une source OCI, source-controller vérifie la signature avec `OCIRepository.spec.verify`. `provider: cosign` et `secretRef` utilisent les clés publiques `.pub` ; une configuration keyless peut omettre la clé et restreindre les `matchOIDCIdentity` avec les expressions `issuer` et `subject`. Une réussite expose `SourceVerified=True`. Vérifier les signatures OCI dans le réconciliateur empêche un artefact non signé d'être livré comme source.

La signature d'une image de contrôleur vérifie une identité de build ; la signature d'un commit Git est un autre objet. Une image signée ne prouve ni son innocuité ni que le dépôt est autorisé à l'exécuter. Le digest assure l'identité immuable de l'objet vérifié. Pour un cluster de production, définir une politique d'admission/approbation correspondant à ces preuves au lieu de s'en remettre à une commande de vérification ponctuelle.

