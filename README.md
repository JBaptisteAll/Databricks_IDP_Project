# Traitement Intelligent de Documents (IDP) avec Databricks

## De quoi s'agit-il ?

Ce notebook implémente un pipeline de **Traitement Intelligent de Documents (IDP)** entièrement dans Databricks, en SQL pur et avec les fonctions IA natives de la plateforme.

L'objectif : ingérer un lot de documents financiers bruts, les parser automatiquement, les classifier par type, extraire les champs structurés, et alimenter des tables propres et requêtables — sans Python, sans API externe, sans modèle ML à maintenir.

---

## Avant / Après

| Avant — PDF brut | Après — Table structurée |
|:---:|:---:|
| ![Facture PDF brute](https://github.com/JBaptisteAll/dbt_tuto/blob/main/assets/before.png) | ![Table invoices extraite](https://github.com/JBaptisteAll/dbt_tuto/blob/main/assets/after.png) |

> Chaque PDF est ingéré tel quel. En sortie : une ligne par document, avec tous les champs clés extraits et typés automatiquement.

---

## Pourquoi ce projet ?

L'IDP est un cas d'usage concret et à forte valeur ajoutée, au croisement de la data engineering et de l'IA appliquée. Dans un contexte métier réel, les équipes finance traitent chaque mois des centaines de factures, bons de commande et reçus — souvent à la main. Ce projet montre comment automatiser ce flux de bout en bout sur une plateforme lakehouse moderne.

Je l'ai construit dans le cadre de ma montée en compétences sur Databricks, pour explorer les AI Functions de manière concrète sur un cas métier réaliste.

---

## Vue d'ensemble du pipeline

```
Fichiers bruts (Unity Catalog Volume)
        │
        ▼
  ai_parse_document()       → extraction du texte depuis les fichiers non structurés
        │
        ▼
  Normalisation du texte    → aplatissement des éléments du document en texte brut
        │
        ▼
  ai_classify()             → classification : Facture / Bon de commande / Reçu / Autre
        │
        ▼
  ai_extract()              → extraction des champs structurés selon le type de document
        │
        ▼
  idp.finance.*             → tables Delta propres, prêtes à l'analyse
```

---

## Tables produites

| Table | Contenu |
|---|---|
| `idp.finance.invoices` | Fournisseur, numéro de facture, dates, moyen de paiement, montant total |
| `idp.finance.purchase_order` | Marchand, numéro de commande, date, montant total |
| `idp.finance.receipt` | Marchand, numéro de reçu, date, montant total |

---

## Stack technique

- **Databricks** (Unity Catalog, Volumes, SQL Notebook)
- **Databricks AI Functions** — `ai_parse_document`, `ai_classify`, `ai_extract`
- **Delta Lake** — stockage en tables Delta managées
- **SQL uniquement** — aucun Python, aucune API externe

---

## Concepts clés illustrés

- Ingestion depuis un Volume Unity Catalog avec `read_files()`
- Parsing de documents avec `ai_parse_document()` et manipulation du type `VARIANT`
- Classification zero-shot avec `ai_classify()`
- Extraction de champs structurés avec `ai_extract()`
- Organisation multi-schéma dans Unity Catalog (`idp.finance`)