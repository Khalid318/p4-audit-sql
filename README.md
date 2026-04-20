# Audit d'un environnement de données — SuperSmartMarket

Audit du chiffre d'affaires d'un supermarché suite à une variation inexpliquée dans le reporting Power BI. Identification de la root cause, analyse SQL, et mise en place d'un prototype de fiabilisation.

<img width="2346" height="426" alt="p4" src="https://github.com/user-attachments/assets/98213672-3b16-4433-b34d-ddba11b86dbc" />


## Contexte

SuperSmartMarket observe une variation de CA de +9 057,29€ entre le 14 et le 15 août 2024 dans son dashboard Power BI. L'entreprise ne sait pas si c'est un vrai pic de ventes ou un problème de données.

Mission : identifier la cause racine et proposer des actions correctives.

## Démarche

**Étape 1 — Recréation de la base en local**

Recréation de l'environnement de données à partir de l'extraction fournie par l'entreprise. 5 tables reconstituées dans MySQL Workbench : Clients, Produits, Employe, Calendrier, Vente_Detail. Schéma relationnel et dictionnaire de données rédigés.

**Étape 2 — Analyse SQL**

Requêtes d'investigation pour comparer le CA jour par jour et identifier les incohérences. Croisement avec les logs d'insertion pour retracer le parcours des données.

**Étape 3 — Identification de la root cause**

Des ventes réalisées le 14 août ont été enregistrées en base le 15 août. Ces insertions tardives gonflent artificiellement le CA du 15 et faussent le reporting. Le problème est lié à l'absence de traçabilité des dates d'insertion.

**Étape 4 — Prototype de fiabilisation**

Mise en place d'un mécanisme pour détecter et traiter les insertions tardives.

## Solution proposée

Quatre composants SQL pour fiabiliser le CA :

| Composant | Rôle |
|-----------|------|
| Colonnes `date_insertion` et `statut_consolidation` | Tracer quand chaque vente est insérée et si elle est consolidée |
| Trigger à l'INSERT | Remplissage automatique de `date_insertion` à chaque nouvelle vente |
| Procédure stockée | Fige le CA en fin de journée (fenêtre J+1) et passe le statut à "consolidé" |
| Vue SQL | Source fiable pour Power BI — n'affiche que les ventes consolidées |

## Résultats

| Métrique | Valeur |
|----------|--------|
| Variation identifiée | +9 057,29€ |
| Root cause | Insertions tardives (ventes du 14/08 enregistrées le 15/08) |
| Tables analysées | 5 (Clients, Produits, Employe, Calendrier, Vente_Detail) |
| Solution | Trigger + procédure stockée + vue SQL |

## Stack

- **MySQL Workbench** — requêtes SQL, triggers, procédures stockées, vues
- **Power BI** — dashboard de reporting (source du constat initial)
- **LibreOffice Calc** — analyse des logs d'extraction

## Livrables

- Rapport d'audit (identification root cause + recommandations)
- Présentation de restitution
- Prototype SQL (trigger, procédure, vue)
- Schéma relationnel + dictionnaire de données
