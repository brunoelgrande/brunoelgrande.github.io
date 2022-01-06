---
layout: post
title: "Variation du coût des énergies dans le temps"
subtitle: "Gaz Naturel, Propane et Huile no.2"
date: 2022-01-05
background: '/img/posts/Energies-Temps/oil-rig-ss.jpeg'
tags: [R, Énergies ]
---
# Introduction

Pour cette publication, nous nous intéressons à la visualisation du coût
du gaz naturel, du propane et de l’huile no.2 à travers les années.

Nous nous intéressons aux prix de ces énergies pour le marché
résidentiel aux États-Unis, des années 1990 à aujourd’hui.

# Packages and données

Nous utiliserons `R` et les packages de `tidyverse`, `lubridate`,
`readxl`, `ggplot` et `gganimate`.

Nous avons téléchargé les données historiques des prix sur le site de
[US Energy Information
Administration](https://www.eia.gov/dnav/ng/ng_pri_sum_dcu_nus_m.htm) le
21 décembre 2021.

# Visualisation des données brutes

## Gaz Naturel

![](/img/posts/Energies-Temps/unnamed-chunk-2-1.png)<!-- -->


Nous pouvons remarquer que le prix du gaz est cyclique d’année en année.
Essayons d’observer la variation annuelle.


![](/img/posts/Energies-Temps/unnamed-chunk-3-1.png)<!-- -->


Nous pouvons bien voir que le prix augmente durant l’été et diminue en
hiver. Aussi, l’augmentation estivale est de plus en plus prononcée à
mesure que nous avançons dans le temps.

Nous pouvons croire que cette augmentation peut être justifiée par le
besoin électrique pour la climatisation aux USA, dont de nombreuses
centrales électriques ont migré vers le gaz naturel à travers les
années.

## Huile no 2

![](/img/posts/Energies-Temps/unnamed-chunk-4-1.png)<!-- -->

## Propane


![](/img/posts/Energies-Temps/unnamed-chunk-5-1.png)<!-- -->


# Transformation des prix en unités communes

Les prix des 3 sources d’énergie sont exprimés en dollars US, mais
selon différentes unités de volumes et/ou d’énergies.

Pour obtenir un comparable, il vaut mieux les conserver en USD, mais
selon une unité d’énergie commune, soit le MMBH ou le million de BTU/h.

``` r
# Conversion gaz naturel : Millier de pieds cube à MMBH
ng_import$NG <- ng_import$NG / 1.037

# Conversion propane : Gallon à MMBH
prop_import$Prop <- prop_import$Prop * 10.917
 
# Conversion huile : Gallon à MMBH
oil_import$Oil <- oil_import$Oil * 7.194
```

Après cette transformation, tous les prix sont en USD / MMBH d’énergie.

# Création du fichier de données de prix

## Dates à l’étude

Vérifions les dates couvertes par chacun des jeux de données et trouvons
les dates pour lesquelles des données existent dans les 3 jeux de
données.

    ## [1] "1990-10-01" "2021-10-15"


# Variation du prix dans le temps

Intéressons nous à la variation du prix des 3 énergies sur la période à
l’étude sur un même graphique.

J’ai souligné les 3 dernières perturbation économiques (Récession début
2000, Grande récession de 2008, Crise de la Covid-19).

![](/img/posts/Energies-Temps/unnamed-chunk-9-1.png)<!-- -->

# Corrélation entre le prix du propane et de l’huile no.2 dans le marché résidentiel

On remarque qu’il semble y avoir une forte corrélation entre
le prix de l’huile et le propane dans le temps : les 2 subissent les mêmes tendances haussières ou baissières. 


![](/img/posts/Energies-Temps/unnamed-chunk-10-1.png)<!-- -->

C'est d'ailleurs normal, étant donné que ces 2 sources d'énergies sont des produits de la distillation du pétrole. 

# Conclusion

En utilisant différents packages de `R` et les données historiques des
prix du gaz naturel, du propane et de l’huile no.2 dans le marché
résidentiel américains, nous avons pu évaluer des corrélations, des
variations saisonnières et la variation dans le temps du coût des
énergies.

L’utilisation de ces puissants outils nous permette d’atteindre nos
objectifs de visualisation et de compréhension des données historiques.

![](/img/posts/Energies-Temps/Cout_Energie_Annees_Anime.gif)<!-- -->

Le Code `R` créé pour cette publication est disponible sur mon
<a href="https://github.com/brunoelgrande/Portfolio" target="_blank">Github</a>.