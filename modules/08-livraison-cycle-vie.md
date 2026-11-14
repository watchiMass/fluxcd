# 08 - Progressive delivery, Operator et cycle de vie Flux

**Phase : expertise · Durée : 5 h**

## Objectifs

- Distinguer Flux (état désiré) de Flagger (analyse progressive et promotion).
- Construire une release canary ou blue/green, ses métriques et ses conditions d'arrêt.
- Comparer bootstrap CLI et Flux Operator pour le cycle de vie d'une installation.
- Mettre à jour Flux de façon contrôlée, observable et réversible.

## Notions à comprendre - et pourquoi

- **Flagger** : génère/observe des ressources de release progressives avec un mesh, ingress ou Gateway API compatible ; analyse métriques et tests avant promotion/rollback. Sans fournisseur de routage et métriques, le canary n'est pas une preuve.
- **Deux boucles déclaratives** : Flux gère les CRDs/contrôleurs Flagger et déclare la ressource Canary ; Flagger agit sur le rollout de l'application. Définir propriétaires de champs et éviter les conflits.
- **Flux Operator** : projet d'écosystème pour installer/gérer Flux de manière déclarative et multi-clusters ; API et mode d'exploitation diffèrent du bootstrap CLI. Évaluer maturité, version, support et implications avant choix.
- **Mise à jour des contrôleurs** : compatibilité CRD/controller/Kubernetes, ordre de mise à jour, canary dans un cluster non critique, fenêtre de rollback et vérification des nouveaux comportements. Le bootstrap CLI est idempotent mais ne remplace pas une stratégie.
- **Image pinning** : l'état Git doit rendre la version des contrôleurs auditable ; automatisation d'upgrade n'équivaut pas à déploiement immédiat en production.

## Lab - progressive delivery et exercice de version

1. Choisir en lab un seul backend Flagger que vous pouvez lancer (ex. Gateway API ou mesh) et suivre son tutoriel officiel d'installation par Flux.
2. Déclarer une Canary pour une app, définir seuils de trafic, métriques Prometheus et tests de conformance. Simuler une version saine puis une version qui échoue aux métriques.
3. Si le backend le permet, réaliser aussi un exercice blue/green avec pré-analyse, bascule contrôlée, observabilité du trafic et stratégie de retour ; expliquer ses différences de ressources et de capacité avec le canary.
4. Vérifier que promotion et rollback sont observables et que Flux ramène les déclarations à leur état souhaité sans écraser une release gérée par Flagger.
5. Examiner le Flux Operator comme alternative sur papier ou sur un second kind. Comparer APIs, responsabilités, opération d'upgrade, état et blast radius.
6. Dans un cluster de test, établir un plan version→version, vérifier les notes Flux/contrôleurs et Kubernetes, mettre à jour via Git/outil officiellement choisi, suivre CRDs, Deployments et réconciliation.
7. Définir des critères Go/No-Go, fenêtre, pilote, pause, rollback et communication aux équipes.

## Critères de réussite

- Un canary échoue si ses métriques dépassent les seuils et ne promeut pas le trafic.
- La version de contrôleurs n'est pas laissée à `latest` et chaque changement est traçable.
- Le choix CLI bootstrap vs Operator est motivé par exigences opérationnelles, pas par préférence d'outil.
- Le runbook de mise à jour décrit les tests et la récupération.

## Pannes volontaires

MetricTemplate indisponible, métrique au-dessus du seuil, référence au mauvais service, CRD non prête avant la ressource, mise à jour contrôleur sans CRD compatible. Diagnostiquer quel réconciliateur possède l'état en échec.

## Questions de compréhension

1. Pourquoi Flux ne fait-il pas à lui seul un canary basé sur métriques ?
2. Qui décide de promouvoir ou d'interrompre le rollout ?
3. Quels composants et ressources versionner lors d'un upgrade Flux ?
4. Quels critères justifieraient l'adoption de Flux Operator dans votre organisation ?

## Progression sans solution immédiate

Voir [indices 08](../corriges/08-livraison-cycle-vie.md#indices), puis [corrigé 08](../corriges/08-livraison-cycle-vie.md#corrigé).
