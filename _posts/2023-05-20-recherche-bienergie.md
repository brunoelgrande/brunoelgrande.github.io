---
layout: post
title: "Recherche d'appareils biénergie"
subtitle: "Création d'un outil de recherche des thermopompes admissibles au programme de subvention biénergie"
date: 2023-05-20
background: "/img/posts/BiEnergie/header_bienergie.jpg"
tags: [Python, Streamlit, Docker, Pandas]
---

# Introduction à la biénergie

![](/img/posts/BiEnergie/elec_gn.jpg)<!-- Bandeau Électricité -- Gaz Naturel -->

En 2022, [Énergir](https://energir.com/fr/a-propos/nos-energies/gaz-naturel/bienergie) et [Hydro-Québec](https://www.hydroquebec.com/residentiel/mieux-consommer/fenetres-chauffage-climatisation/offre-bienergie/) ont uni leurs efforts pour lutter contre les changements climatiques en mettant de l’avant un partenariat qui est à la fois unique au monde et essentiel pour décarboner le chauffage des bâtiments, et ce, au meilleur coût possible pour la société.

Concrètement, en remplaçant un système de chauffage fonctionnant uniquement au gaz naturel par un système biénergie, il devient possible de chauffer un bâtiment à l’électricité la majorité du temps temps, et par temps froids, lorsque la demande en électricité est très forte, le gaz naturel prendra le relais. Ainsi, les ménages participants réduiront d’un peu plus de **70 %** leur consommation de gaz naturel pour ainsi diminuer les émissions de GES.

# Réalité résidentielle au Québec

Au Québec, les 2 principaux type d'installations résidentielles qui peuvent passer à la biénergie sont :

- système à air chaud central fonctionnant avec une fournaise au gaz naturel et une thermopompe

![](/img/posts/BiEnergie/schema_air_chaud_bienergie.png)<!-- -->

- système hydronique fonctionnant avec une chaudière au gaz naturel et une électrique

![](/img/posts/BiEnergie/img_hydronique_bienergie.jpeg)<!-- -->

<br/>

# Programme de subventions

Afin d'encourager l'adoption de cette initiative, un programme de [subventions](https://energir.com/fr/residentiel/bienergie/clients/) a été élaborée par les 2 distributeurs d'énergie et le gouvernement du Québec.

Pour les clients possédant un système à air chaud, la façon d'accéder à cette subvention est d'installer une thermopompe, en complément du système à air chaud au gaz naturel.

Afin de s'assurer du niveau de performance des appareils installés, le guide du programme demande que ceux-ci soient présents sur une liste de l'<a href="https://www.ahridirectory.org/Search/SearchHome?ReturnUrl=%2f" target="_blank">AHRI</a> ou de <a href="https://www.ahridirectory.org/NewSearch?programId=69&searchTypeId=4&productTypeId=3523" target="_blank">CEE</a>. La particularité de cette liste est que l'admissibilité est liée à la présence de DUO ou TRIO d'équipements : les unités intérieures (évaporateur) et extérieures (condenseur) doivent avoir été testées et validées ensemble lors de la certification de performance par les manufacturiers.

## Vérification de l'admissibilité

Une vérification est donc requise par les entrepreneurs afin de s'assurer que les appareils proposés seront bel et bien subventionnables. Malheureusement, cette dernière est complexe, du fait que :

- il y a plus de 80 000 combinaisons d'appareils admissibles
- l'outil de recherche des sites de référence n'est pas _optimal_
- les noms de modèles sont longs et plusieurs options n'ayant pas d'impact sur l'admissibilité peuvent empêcher de trouver le modèle recherché
- il y a présences de _wildcard_ dans les modèles de références
- s'il n'y a pas de correspondance totale lors de la recherche, il n'est pas simple de trouver des suggestions d'appareils compatibles avec une unité extérieure ou l'inverse

Bref, j'ai cru être en mesure d'améliorer l'expérience utilisateur de mes collègues chez Énergir de même que celles des entrepreneurs devant travailler avec cette liste d'appareils.

# Création de l’outil de recherche

C'est ainsi que j'ai créé le site <a href="https://recherchebienergie.brunogauthier.net/" target="_blank">recherchebienergie.brunogauthier.net/</a>

![](/img/posts/BiEnergie/capture_site.png)<!-- Capture écran site recherche biénergie -->

Le code est disponible sur mon site <a href="https://github.com/brunoelgrande/RechercheBiEnergie" target="_blank">Github</a>, mais en voici les principales étapes.

## Importation des données d'appareils

Le site CEE permet de rechercher l'ensemble des appareils (recherche avec critères vides), mais permet seulement d'exporter vers une fichier Excel 250 lignes à la fois. Cela implique le besoin de télécharger plus de 350 fichiers pour obtenir tous les appareils.

J'ai donc écrit un script en `Python` afin de pouvoir télécharger chacun des fichiers un à un en effectuant du _web scrapping_.

J'ai pu utiliser le package `selenium` pour ce faire, qui a pu passer sur chacune des pages de résultats de l'outil de recherche du site, "appuyant" sur le bouton pour télécharger en Excel à chaque fois.

J'ai obtenu ainsi 326 fichiers `Excel`, contenant chacun 250 combinaisons admissibles à la biénergie.

## Concaténation en un fichier unique

Pour être en mesure de faire une recherche sur cette liste, j'ai regroupé en une liste unique contenant toutes les combinaisons.

Pour ce faire, j'ai utilisé les modules `pandas` de Python, en important un à un tous les fichiers Excel et en les concaténant vers un `DataFrame`, puis en exportant vers un fichier `.csv` qui sera facilement utilisable par la suite.

![](/img/posts/BiEnergie/capture_liste_csv.png)<!-- Capture liste fichier csv concaténé -->

J'en ai aussi profité pour faire le ménage dans les différentes colonnes, en conservant seulement celles qui nous seraient utiles et en corrigeant le nom de celles-ci.

## Fonction de recherche améliorée

À partir des données regroupées, j'ai pu utiliser `regex` permettant la recherche de termes précis et complets, mais aussi des recherches de modèles incomplets ou partiellement incorrects.

Par exmple, un modèle dans la liste qui serait **CH6B3021** ne serait pas trouvé si l'utilisateur recherche **CH6B3021-RED**, un indicateur de la couleur qui n'a pas d'influence sur la spécification réelle de l'appareil.

J'ai donc implémenté une boucle qui permet d'éliminer un caractères par itération, de façon à avoir une meilleur chance de trouver une correspondance. C'est ce que ferait manuellement un utilisateur en enlevant le dernier caractère et en effectuant une nouvelle recheche.

![](/img/posts/BiEnergie/capture_surcomplet.png)

Un autre cas de figure est si l'utilisateur omet un ou des caractères à la fin du modèle, **CH6B3** par exemple. La boucle permet de trouver les modèles dans la liste de référence qui sont les plus près de la chaîne de caractères entrée par l'utilisateur. Il peut donc rafiner sa recherche avec des modèles dans la liste, orthographiés de la bonne façon.

![](/img/posts/BiEnergie/capture_incomplet.png)

## Proposition d'options d'appareils

Dans le cas où un seul appareil est trouvé, le programme offre une liste d'appareils complémentaires qui sont approuvés avec ce premier.

Dans notre exemple précédant, une vingtaine d'évaporateurs pourraient créer un DUO admissible avec le condenseur Coleman **CH6B3021**. La liste présentée à l'utilisateur permet de raffiner sa recherche, dans le but d'offrir un DUO admissible au client.

![](/img/posts/BiEnergie/capture_sugg.png)

Le même scénario est possible avec les fournaises ou même avec un TRIO d'équipements.

## Validation de résultats exportables en Excel

Une fois un DUO ou un TRIO trouvé, il est possible de télécharger un fichier `Excel` afin de garder la preuve de la vérification ou d'envoyer à un fournisseur ou un client.

![](/img/posts/BiEnergie/capture_sugg_DUO.png)

![](/img/posts/BiEnergie/capture_sugg_excel.png)<!-- Capture validation en Excel -->

## Recherche par numéro AHRI

La recherche inverse est aussi possible, en partant du numéro AHRI.

![](/img/posts/BiEnergie/capture_AHRI.png)

## Création d'unité de tests

Grâce à `pytest`, j'ai créé plusieurs scénarios typiques de TRIO ou DUO qui m'ont permis de valider les résultats attendus à différentes recherches.

Ces tests automatisés ont permis d'accélérer grandement les améliorations au code et limiter rapidement l'effet de certaines erreurs pouvant être introduites durant le travail.

![](/img/posts/BiEnergie/capture_pytest.png)<!-- Capture output terminal pytest -->

## Création d'un site web avec le module Streamlit

Le site web a été créé grâce aux modules `Streamlit` (_app framework_), qui permet de partager rapidement des analyses de données créées avec `Python`. Il permet de transformer facilement le code `Python` en un site web interactif.

## Création d'une image Docker

Pour être en mesure de déployer facilement l'application sur un serveur différent de mon propre ordinateur, j'ai créé une image `Docker` de l'application, que j'ai rendu publique sur [Docker Hub](https://hub.docker.com/r/brunoelgrande/recherchebienergie).

![](/img/posts/BiEnergie/capture_docker.png) <!-- Capture Docker hub -->

## Publication web du site

Avec cette image, j'ai pu déployer une instance de l'application sur mon petit serveur local dans `Portainer`.

J'ai aussi publié le tout sur le `community cloud` de [Streamlit](https://streamlit.io/) en y connectant le _repo_ Github, ce qui m'a permis d'avoir une version hébergée directement sur le web : <a href="https://recherchebienergie.streamlit.app/" target="_blank">recherchebienergie.streamlit.app/</a>.

L'utilisation du `community cloud` évite d'avoir besoin de son propre serveur.

# Conclusion

Malheureusement, ou heureusement, les critères ont changé en cours d'année et nous n'avons pas eu à mettre en production cette application.

Tout de même, j'ai pu démontrer la fonctionnalité du programme et je l'ai essayé sur certains de mes dossiers à plusieurs reprises.

De plus, les concepts mis en application dans ce projet peuvent être appliqués à bien d'autres situations d'entreprise ou personnelles.

Je vous invite à visiter le site <a href="https://recherchebienergie.brunogauthier.net/" target="_blank">recherchebienergie.brunogauthier.net/</a> et d'explorer ce qui est possible de réaliser rapidement avec `Python` et `Streamlit`.

Ou bien vous trouver une nouvelle thermopompe !
