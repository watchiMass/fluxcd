# 05 - Secrets, SOPS et chaîne de confiance

**Phase : maîtrise · Durée : 4 h**

## Objectifs

- Chiffrer des manifests avec SOPS et déchiffrer au moment de la réconciliation.
- Comparer age et KMS/workload identity et pratiquer une rotation.
- Vérifier les signatures cosign et la provenance des artefacts.
- Décrire les limites de confiance entre Git, registry, contrôleurs et cluster.

## Notions à comprendre - et pourquoi

- **SOPS** : le dépôt contient le ciphertext ; kustomize-controller déchiffre pendant la build à l'aide d'une clé age ou d'un fournisseur KMS configuré. Le Secret en clair existe dans l'API Kubernetes et doit être protégé aussi par RBAC, encryption at rest et sauvegardes.
- **Clé et identité** : age convient à un lab et à des clés contrôlées ; KMS/workload identity évite de distribuer une clé maître, au prix d'une dépendance d'identité cloud. La clé privée ne va jamais dans Git.
- **Signature** : pour un artefact OCI consommé comme source, `OCIRepository.spec.verify` peut faire vérifier Cosign par source-controller (clé publique ou keyless avec règles OIDC) et publier une condition `SourceVerified`. Cela bloque l'usage de l'artefact si la vérification requise échoue. La vérification locale d'une image de contrôleur Flux est une autre étape. Une signature atteste une identité/claim selon une politique, pas la qualité du logiciel.
- **Modèle de menace** : un dépôt malveillant peut demander des actions de cluster. Least privilege, revue/approbation, admission policy, signatures, source immuable et RBAC sont complémentaires.

## Lab - chiffrement, rotation et vérification

1. Installer `sops`, `age` et `cosign` localement, générer une clé age réservée au lab. Ajouter uniquement la clé publique/recipient et `.sops.yaml` au dépôt.
2. Créer un manifeste Secret factice, chiffrer ses valeurs avant commit ; vérifier que le diff Git ne révèle pas le plaintext.
3. Ajouter `spec.decryption.provider: sops` à la Kustomization concernée et publier la clé privée via un mécanisme hors Git compatible avec le lab (Secret installé par bootstrap contrôlé ou mécanisme cloud). Documenter le périmètre et le risque.
4. Faire tourner la clé en gardant temporairement les deux recipients ; ré-encrypter et vérifier l'absence de valeur lisible dans Git. Retirer l'ancien recipient après bascule.
5. Publier ou sélectionner un artefact OCI de test signé. Ajouter `verify.provider: cosign` à un `OCIRepository` et n'autoriser que la clé publique ou le couple `issuer`/`subject` OIDC attendu. Confirmer `SourceVerified=True`, puis provoquer une signature manquante ou une identité non autorisée et vérifier que l'artefact n'est pas consommé.
6. Vérifier aussi une image de contrôleur Flux avec la procédure officielle, puis examiner SBOM/provenance ; ne pas copier aveuglément une regex d'exemple dans une politique de prod.
7. Proposer une politique de promotion d'image applicative signée et l'action de refus. Exécuter uniquement sur un registre/cluster de test.

## Critères de réussite

- Le Secret en clair n'est ni dans l'historique du dépôt ni dans les logs/artefacts visibles.
- Le contrôleur peut déchiffrer seulement dans le périmètre voulu ; une clé absente/incorrecte rend le statut explicite.
- La rotation conserve l'accès pendant le basculement puis permet le retrait sûr de l'ancienne clé.
- La vérification documente l'identité, l'émetteur OIDC, le digest et l'usage de la preuve.

## Pannes volontaires

Mauvais recipient, clé absente, document SOPS altéré, clé KMS sans droit, digest sans signature ou identité signataire incorrecte. Avant correction, relever l'objet Flux et le log du contrôleur concerné sans imprimer le secret.

## Questions de compréhension

1. Pourquoi chiffrer Git ne protège-t-il pas les Secrets déjà stockés dans l'API ?
2. Quel événement déclenche le déchiffrement et quel contrôleur a besoin du droit KMS ?
3. Quelle différence entre signature d'image et signature du commit Git ?
4. Pourquoi le digest immuable est-il important même quand cosign vérifie une signature ?

## Progression sans solution immédiate

Voir [indices 05](../corriges/05-secrets-signatures.md#indices), puis [corrigé 05](../corriges/05-secrets-signatures.md#corrigé).
