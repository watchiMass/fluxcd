# 06 - Images, notifications et webhooks

**Phase : maîtrise · Durée : 4 h**

## Objectifs

- Configurer `ImageRepository`, `ImagePolicy` et `ImageUpdateAutomation`.
- Comprendre le flux scan → sélection → édition YAML → commit Git → réconciliation.
- Router les événements via `Provider`/`Alert` et déclencher une sync par `Receiver`.
- Évaluer les risques d'une écriture automatisée sur une branche de déploiement.

## Notions à comprendre - et pourquoi

- **Image reflector** : observe des tags et rend les métadonnées disponibles ; une policy choisit un candidat selon SemVer, alphabetical ou numerical. La policy doit représenter le canal de promotion attendu.
- **Image automation** : applique les marqueurs YAML puis écrit/pousse une modification Git. Le commit est la preuve et l'entrée du cycle normal de déploiement ; l'automation n'est pas une approbation de sécurité.
- **Credentials en écriture** : permissions minimales, branche dédiée/PR si possible, identité robot tracée et protections Git. Éviter qu'un contrôleur capable de pousser puisse modifier tout le dépôt.
- **Notification** : `Provider` décrit le destinataire, `Alert` filtre les événements ; les événements sont un signal de diagnostic, pas un bus garantissant une livraison exactement une fois.
- **Receiver** : webhook entrant authentifie et filtre un événement puis demande une réconciliation. Il accélère le polling sans remplacer celui-ci ; endpoint, secret, TLS, ingress et anti-abus sont à protéger.

## Lab - automatiser une image et rendre l'état observable

1. Vérifier si `image-reflector-controller` et `image-automation-controller` sont installés : ils ne font pas partie des composants Flux par défaut. Pour les ajouter sans apply direct, produire une version candidate avec `flux install --export --version=v2.10.0 --components-extra=image-reflector-controller,image-automation-controller`, intégrer/revoir le résultat dans `gotk-components.yaml`, puis laisser Git réconcilier.
2. Publier ou sélectionner une image d'essai dont vous contrôlez les tags. Créer `ImageRepository`, une policy SemVer et des marqueurs dans le manifest de workload.
3. Configurer `ImageUpdateAutomation` sur une branche de lab, avec identité Git dédiée, chemin restreint et message de commit informatif.
4. Publier un nouveau tag, suivre les statuts ImageRepository/ImagePolicy, examiner le commit produit et laisser la chaîne normale Flux appliquer le nouvel état.
5. Configurer un Provider non productif (par exemple webhook de test) et une Alert limitée aux erreurs des objets du lab.
6. Configurer un Receiver GitHub/webhook seulement si vous disposez d'un endpoint TLS de lab. Examiner `status.webhookPath`, secrets, ressources écoutées et l'authenticité du payload.
7. Comparer délai de polling et déclenchement webhooks, y compris le cas où le webhook est perdu.

Ne remplacez pas aveuglément le manifeste de composants généré si vous avez des patches bootstrap personnalisés : comparez les différences et préservez vos personnalisations versionnées.

## Critères de réussite

- La policy choisit le tag voulu, et l'automation n'édite que le champ marqué et le chemin attendu.
- Chaque upgrade d'image devient un commit auditable avant apply.
- Un événement d'erreur pertinent déclenche une notification sans exposer de token.
- Une notification/webhook manquant n'empêche pas la convergence à terme par polling.

## Pannes volontaires

Image inaccessible, policy qui ne sélectionne aucun tag, marker mal formé, token Git en lecture seule, Receiver secret incorrect, mauvais type de payload, Alert qui ne sélectionne pas le namespace. Déterminer le contrôleur propriétaire et l'étape bloquée.

## Questions de compréhension

1. Pourquoi l'ImagePolicy ne déploie-t-elle pas directement l'image ?
2. Comment empêcher les tags alpha d'être promus en production ?
3. Qu'est-ce qui garantit que le commit d'automation passe par les mêmes contrôles que les commits humains ?
4. Quelle différence entre webhook de notification et polling Source ?

## Progression sans solution immédiate

Voir [indices 06](../corriges/06-automation-notifications.md#indices), puis [corrigé 06](../corriges/06-automation-notifications.md#corrigé).
