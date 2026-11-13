# Corrigé 06 — Images et notifications

## Indices

1. Suivre dans l'ordre `ImageRepository` → `ImagePolicy` → marqueur en Git → `ImageUpdateAutomation` → nouveau commit → `GitRepository` → workload.
2. Les erreurs de push sont distinctes d'une policy sans image sélectionnable.
3. `Provider`/`Alert` traitent les événements sortants ; `Receiver` traite les webhooks entrants.

## Corrigé

L'ImageRepository reflète les tags disponibles, la policy choisit selon la règle exprimée et l'automation écrit les marqueurs reconnus dans le chemin configuré. Le commit poussé à Git est la transition entre une mise à jour candidate et l'état déclaré ; les protections de branche/PR et les permissions du robot déterminent si une approbation est requise.

Un tag alpha exige une policy de production excluant explicitement les préversions et des contrôles d'approbation séparés. Une policy n'est ni une preuve de signature ni un test fonctionnel.

Les notifications peuvent être perdues/rejouées ; l'état courant demeure observable dans les CRD et le polling permet de converger sans webhook. Le Receiver requiert une URL accessible, un secret approprié, TLS et un sélecteur de ressources précis. Un webhook accélère une sync, ne remplace pas l'authentification Source.

