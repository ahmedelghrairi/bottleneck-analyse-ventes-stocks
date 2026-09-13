# BottleNeck : rapprochement et analyse des ventes et du stock

Première version de cette analyse, livrée en février 2026 pendant ma formation Data Analyst (OpenClassrooms). BottleNeck, marchand de vin en ligne, gère ses données produits sur deux systèmes non reliés, un ERP et un site WordPress, avec une table de liaison tenue à la main pour faire le pont entre les deux.

J'ai repris ce travail quelques mois plus tard avec l'aide de l'IA, en corrigeant trois faiblesses que j'avais moi-même identifiées : voir la [version 2](https://github.com/ahmedelghrairi/bottleneck-analyse-ventes-stocks-amelioration-IA).

Étude de cas complète, avec démarche et recommandations : [voir sur mon portfolio](https://ahmedelghrairi.github.io/projets/bottleneck.html)

## Contenu du dépôt

- `notebook_analyse_bottleneck.ipynb` : rapprochement des sources, nettoyage, et l'ensemble des analyses
- `data/` : les trois fichiers sources (erp.xlsx, web.xlsx, liaison.xlsx)
- `requirements.txt` : bibliothèques utilisées

**À savoir avant d'exécuter le notebook.** Il a été développé sur Google Colab et charge les fichiers depuis un chemin Google Drive en dur. Pour le relancer en local, remplacer les trois chemins `/content/drive/MyDrive/...` par `data/erp.xlsx`, `data/web.xlsx` et `data/liaison.xlsx`.

## Les données

Trois exports Excel arrêtés au 31 octobre, ventes du 1er au 31 octobre : 825 références dans l'ERP (prix, stock), un export du site web (SKU, quantités vendues, descriptions), et une table de liaison entre les deux systèmes, dont les identifiants ne se correspondent pas nativement.

## La démarche

**Nettoyage par source, avant toute fusion.** Sur l'ERP : trois prix négatifs ramenés en valeur absolue plutôt que supprimés, ce sont des erreurs de signe et non des valeurs aberrantes ; des quantités de stock négatives mises à zéro ; une colonne `stock_status_2` recréée pour que le statut affiché redevienne cohérent avec la quantité réelle. Sur le web : réduction aux colonnes utiles, deux SKU manifestement invalides supprimés, 714 doublons tranchés en faveur de la ligne "product" plutôt que "attachment", 85 lignes sans SKU écartées. Sur la table de liaison : 91 références ERP sans correspondance web identifiées et suivies plutôt qu'ignorées.

**Deux périmètres de fusion, pas un seul.** Le chiffre d'affaires et les ventes se calculent sur le périmètre des seules références avec correspondance web confirmée, alors que le stock se calcule sur l'ensemble du catalogue ERP : les deux questions n'ont pas le même périmètre de vérité, les confondre aurait faussé l'un des deux résultats.

**Détection des valeurs aberrantes sur les prix**, avec deux méthodes croisées (Z-score et écart interquartile) puis visualisation en boîte à moustaches. Au-delà d'environ 83 €, on entre dans les produits premium ; 17 références dépassent 112 € et vont jusqu'à 225 €, avec des ventes réelles cohérentes avec ce positionnement, ce ne sont donc pas des erreurs de saisie mais un segment haut de gamme légitime.

## Quelques résultats

- Chiffre d'affaires du mois : 143 680,10 €, avec une règle 80/20 confirmée mais moins concentrée qu'attendu.
- Stock valorisé selon deux logiques différentes, au prix de vente et au prix d'achat, la seconde étant la lecture la plus pertinente d'un point de vue financier.
- Taux de marge par catégorie allant de 61 % sur le vin à plus de 80 % sur le cognac et le whisky.
- Trois corrélations qui racontent une histoire cohérente : prix et ventes sont négativement liés (-0,52, plus cher se vend moins), ventes et stock sont positivement liés (+0,44, les meilleures ventes sont mieux approvisionnées), prix et stock n'ont quasiment aucun lien (-0,11).

## Limites assumées à l'époque

Cette première version avait trois angles morts, que j'ai moi-même identifiés avant de les corriger dans la version 2 : des contrôles qualité faits une fois, à la main, non rejouables d'un mois sur l'autre ; aucun traitement formalisé de la conformité RGPD sur les données personnelles présentes dans l'export web ; et une reproductibilité limitée, le notebook dépendant d'un chemin Google Drive propre à mon poste de travail.

## Outils

Python, pandas pour l'analyse, plotly pour les visualisations.

Ahmed El Ghrairi, 2026.
