---
layout: post
title: "Recherche d'appareils biénergie"
subtitle: "Création d'un outil de recherche des thermopompes admissibles au programme de subvention biénergie"
date: 2023-05-20
background: "/img/posts/BiEnergie/header_bienergie.jpg"
tags: [Python, Streamlit, Docker, Pandas]
---

# Introduction à la Biénergie

![](/img/posts/BiEnergie/elec_gn.jpg)<!-- Bandeau Électricité -- Gaz Naturel -->

En 2022, [Énergir](https://energir.com/fr/a-propos/nos-energies/gaz-naturel/bienergie) et [Hydro-Québec](https://www.hydroquebec.com/residentiel/mieux-consommer/fenetres-chauffage-climatisation/offre-bienergie/) ont uni leurs efforts pour lutter contre les changements climatiques en mettant de l’avant un partenariat qui est à la fois unique au monde et essentiel pour décarboner le chauffage des bâtiments, et ce, au meilleur coût possible pour la société.

Concrètement, en remplaçant un système de chauffage fonctionnant uniquement au gaz naturel par un système biénergie, il devient possible de chauffer un bâtiment à l’électricité la majorité du temps temps et par temps froids, lorsque la demande en électricité est très forte, le gaz naturel prendra le relais. Ainsi, les ménages participants réduiront d’un peu plus de **70 %** leur consommation de gaz naturel pour ainsi diminuer les émissions de GES.

# Réalité résidentielle au Québec

Au Québec, les 2 principaux type d'installations résidentielles qui peuvent passer à la biénergie sont :

- système à air chaud central fonctionnant avec une fournaise au gaz naturel et une thermopompe

![](/img/posts/BiEnergie/schema_air_chaud_bienergie.png)<!-- -->

- système hydronique fonctionnant avec une chaudière au gaz naturel et une électrique

![](/img/posts/BiEnergie/img_hydronique_bienergie.jpeg)<!-- -->

<br/>

# Programme de subventions

Afin d'encourager l'adoption de cette initiative, un programme de [subventions](https://energir.com/fr/residentiel/bienergie/clients/) a été élaborée par les 2 distributeurs d'énergie et le gouvernement du Québec.

Au niveau des clients possédant un système à air chaud, la façon d'accéder à cette subvention est d'installer une thermopompe en complément du système à air chaud au gaz naturel.

Afin de s'assurer du niveau de performance des appareils à installer, le guide du programme demande que ceux ci soient présents sur une liste de l'<a href="https://www.ahridirectory.org/Search/SearchHome?ReturnUrl=%2f" target="_blank">AHRI</a> ou de <a href="https://www.ahridirectory.org/NewSearch?programId=69&searchTypeId=4&productTypeId=3523" target="_blank">CEE</a>. La particuliratié de cette liste est que l'admissibilité est liée à la présence de DUO ou TRIO d'équipements : les unités intérieures (évaporateur) et extérieures (condenseur) doivent avoir été validées ensemble lors de la certification de performance par les manufacturiers.

## Vérification de l'admissibilité

Une vérification est donc requise par les entrepreneurs afin de s'assurer que les appareils proposés seront bel et bien subventionnables. Malheureusement, cette dernière est complexe, du fait que :

- il y a plus de 80 000 combinaisons d'appareils admissibles
- l'outil de recherche des sites de référence n'est pas optimal
- les noms de modèles sont longs et plusieurs options n'ayant pas d'impact sur l'admissibilité peuvent empêcher de trouver le modèle recherché
- il y a présences de _wildcard_ dans les modèles de références
- s'il n'y a pas de correspondance lors de la recherche, il n'est pas simple de trouver des suggestions d'appareils compatibles avec une unité extérieure ou l'inverse

Bref, j'ai cru pouvoir améliorer l'expérience utilisateur des mes collègues chez Énergir de même que celles des entrepreneurs devant travailler avec cette liste d'appareils.

# Créaction de l'outils de recherche

C'est ainsi que j'ai créé le site <a href="https://recherchebienergie.brunogauthier.net/" target="_blank">recherchebienergie.brunogauthier.net/</a>

![](/img/posts/BiEnergie/capture_site.png)<!-- Capture écran site recherche biénergie -->

Le code est disponible sur mon site <a href="https://github.com/brunoelgrande/RechercheBiEnergie" target="_blank">Github</a>, mais en voici les principales étapes.

## Importation des données d'appareils

Le site CEE permet de rechercher l'ensemble des appareils (recherche avec critère vides), mais nous permettait d'exporter vers une fichier Excel 250 lignes à la fois. Cela implique le besoin de télécharger plus de 350 fichiers pour obtenir tous les appareils.

J'ai donc écrit un script en python afin de pouvoir télécharger chacun des fichiers un à un en effectuant du _web scrapping_.

J'ai pu utiliser le package `selenium` pour se faire, qui a pu passer sur chacune des pages de résultats de l'outil de recherche du site, "appuyant" sur le bouton pour télécharger en Excel à chaque fois.

Nous avons pu ainsi obtenir 326 fichiers Excel contenant chacun 250 combinaisons admissibles à la biénergie.

## Concaténation en un fichier unique

Pour être en mesure de faire une recherche sur cette liste, j'ai créé une liste unique contenant toutes les combinaisons.

Pour ce faire, j'ai utilisé les modules `pandas` de Python, en imporant un à un tous les fichiers Excel et en les concaténant vers un `DataFrame`, puis en exportant vers un fichier `.csv` qui sera facilement utilisable par la suite.

![](/img/posts/BiEnergie/capture_liste_csv.png)<!-- Capture liste fichier csv concaténé -->

J'en ai aussi profité pour faire le ménage dans les différentes colonnes, en conservant seulement celles qui nous seraient utiles et en corrigeant le nom de celles-ci.

## Fonction de recherche améliorée

...

## Proposition d'options et validation de résultats en Excel

![](/img/posts/BiEnergie/capture_sugg_excel.png)<!-- Capture validation en Excel -->

## Création d'unité de tests

Grâce à `pytest`, j'ai créé plusieurs scénarios typiques de TRIO ou DUO qui m'ont permis de valider les résultats attendus à différentes recherches.

Ces tests automatisés ont permis d'accélérer grandement les améliorations au code et limiter rapidement l'effet de certaines erreurs pouvant être introduites dans le code durant le travail.

![](/img/posts/BiEnergie/capture_pytest.png)<!-- Capture output terminal pytest -->

## Création d'un site web avec le module Streamlit

...

## Création d'une image Docker

...

## Publication web du site

...

J'ai aussi publié le tout sur le `community cloud` de [Streamlit](https://streamlit.io/) en y connectant le _repo_ Github, ce qui m'a permis d'avoir une version hébergée directement sur le web : <a href="https://recherchebienergie.streamlit.app/" target="_blank">recherchebienergie.streamlit.app/</a>. L'utilisation du `community cloud` évite d'avoir besoin de son propre serveur.

# Conclusion

....
