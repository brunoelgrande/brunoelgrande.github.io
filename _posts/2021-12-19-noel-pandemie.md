---
layout: post
title: "Noël en pandémie"
subtitle: "Attachez vos tuques qu'ils disaient"
date: 2021-12-19
background: '/img/posts/Noel-pandemie/noel_covid_bg.jpeg'
tags: R COVID
---

# La COVID s’invite à Noël

Le réveil est brutal à la lecture de La Presse ce matin.

<a href="https://www.lapresse.ca/covid-19/2021-12-19/variant-omicron/attachez-vos-tuques.php" rel="some text" target="_blank">![Foo](/img/posts/Noel-pandemie/lapresse_omicron.png)</a>

On pourrait se croire en 2020 à pareil date l’an dernier. Mais est-ce
que nous suivons la même tendance qu’en 2020 ?

## Quelle vague déjà ?

L’analyse préliminaire présentée dans ma publication précédente nous a
permis de démontrer les tendances entre les provinces durant la 2e
vague, qui couvrait la période des Fêtes 2020.

Nous nous intéressons maintenant à la situation au Québec en 2021
comparée à 2020.

Nous utilisons encore une fois les données de
l’[INSPQ](https://www.inspq.qc.ca/covid-19/donnees/comparaisons), mais
seulement pour le Québec.

Le Code **R** créer pour cette publication est disponible sur mon
<a href="https://github.com/brunoelgrande/Portfolio" target="_blank">Github</a>.


## Dates et données couvertes

Les dates utilisées pour bâtir le graphique sont les suivantes :
    
    ##    Date.2020  Date.2021
    ##   2020-09-01 2021-09-01
    ##   2021-03-20 2021-12-16

Étant donné la période 2021 plus courte que celle 2020, nous avons ces
données disponibles pour bâtir notre graphique :
    
    ## Cas.2020 Cas.2021 
    ##      201      107

## Distribution des cas québécois - 2020 vs 2021

Si nous superposons les 2 années sur un même graphique, nous pouvons
comparer la tendance 2020 vs 2021.

![](/img/posts/Noel-pandemie/unnamed-chunk-5-1.png)<!-- -->

Nous pouvons bien voir que l’automne était sous un meilleur contrôle,
mais que la courbe s’accélère dans les derniers jours, avec une pente
plus élevée qu’à pareille date l’an dernier.

# Analyse statistique

Nous voulons effectuer une analyse statistique afin de comparer les 2
années pour les période où les données sont disponibles, tel que le
graphique suivant.

Le but de l’analyse est de déterminer si :

> Ho : la moyenne de 2020 est la même que celle 2021 
> Ha : les 2 moyennes diffèrent
> Alpha : 5%

![](/img/posts/Noel-pandemie/unnamed-chunk-6-1.png)<!-- -->

![](/img/posts/Noel-pandemie/unnamed-chunk-7-1.png)<!-- -->
Nous voyons bien que la variance des 2 périodes est différentes. Nous
pouvons aussi croire que ce type de données ne suivent pas une
distribution normale.

![](/img/posts/Noel-pandemie/unnamed-chunk-8-1.png)<!-- -->

## Transformation par rang

Pour être en mesure de réaliser la comparaison entre les 2 périodes, il
faut utiliser une approche non paramétrique appelée la transformation
par rang.

<a href="[Aligned Rank Transform ANOVA](https://www.real-statistics.com/two-way-anova/aligned-rank-transform-art-anova/" target="_blank">Aligned Rank Transform ANOVA</a>

``` r
COVID_stats$Cas_Rang <- rank(COVID_stats$Cas)

headTail(COVID_stats)
```
    ##         Date Annee   Cas Cas_Rang
    ## 1 2021-09-01  2020  1.35        2
    ## 2 2021-09-01  2021  6.49       54
    ## 3 2021-09-02  2020  1.33        1
    ## 4 2021-09-02  2021  6.65       64
    ## 5       <NA>  <NA>   ...      ...
    ## 6 2021-12-15  2020 20.87      211
    ## 7 2021-12-15  2021 22.16      213
    ## 8 2021-12-16  2020 21.15      212
    ## 9 2021-12-16  2021 23.71      214

Suite à la transformation :
![](/img/posts/Noel-pandemie/unnamed-chunk-10-1.png)<!-- -->

Nous pouvons voir que la largeur de la boîte s’est uniformisée.

![](/img/posts/Noel-pandemie/unnamed-chunk-11-1.png)<!-- -->

## Réalisation de l’ANOVA à 1 facteur sur les cas transformés

Avec les cas transformées par rang, nous pouvons réaliser la
vérification des conditions de normalité des résidus et d’homogénéité de
la variance.

![](/img/posts/Noel-pandemie/unnamed-chunk-12-1.png)<!-- -->

Nous pouvons aussi réaliser les tests formels d’Anderson-Darling, pour
la normalité des résidus.

    ##  Anderson-Darling normality test
    ## 
    ## data:  residuals(ANOVA_rang)
    ## A = 1.0138, p-value = 0.01116

Et celui de Levene, pour l’homogénéité de la variance.

    ## Levene's Test for Homogeneity of Variance (center = median)
    ##        Df F value   Pr(>F)   
    ## group   1  6.8225 0.009646 **
    ##       212                    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

La normalité des résidus et l’homogénéité de la variance ne sont
toujours pas atteint (visible graphiquement et par les tests formels (Pr
&lt; 5%) ), mais nous pouvons utiliser des tests adaptés après cette
transformation en rang.

Le test ***Oneway*** est une ANOVA non-paramétrique adaptée pour des
variances inégales.

La normalité des résidus n’est plus nécessaire après la transformation
en rang pour réaliser cette analyse.

    ##  One-way analysis of means (not assuming equal variances)
    ## 
    ## data:  Cas_Rang and Annee
    ## F = 16.416, denom df = 196.08, p-value = 7.326e-05

Nous constatons que P &lt; 0.001 et nous pouvons donc rejeter l'hypothèse nulle Ho, c’est
à dire que les moyennes des 2 années diffèrent.

## Calcul des moyennes de cas 2020 vs 2021

Nous pouvons donc calculer les moyennes et leurs intervalles de
confiance à 95%.

    ##   Annee Cas.upper  Cas.mean Cas.lower
    ##    2020 12.079800 11.065421 10.051041
    ##    2021  9.480476  8.735514  7.990552

# Conclusion

Nous avons pu confirmer que la différence de moyenne de cas déclarés de
COVID au Québec entre l’automne 2020 et l’automne 2021 est
statistiquement significative.

La Santé Publique a déclaré 8.7 cas par jour en 2021 plutôt que 11 en 2020. Nous pouvons sûrement y voir l’effet de l’immunité de la population causée par la vaccination.

Par contre, nous avons pu voir avec le graphique des données dans le
temps que la tendance actuelle au Québec accélére rapidement. Le nouveau
variant Omicron a su donner un 2e (ou 3e ?) au virus.

Espérons qu’il sera possible de renverser la tendance dans les
prochaines semaines. Sinon, ma lecture matinale de La Presse risque
d’être encore déprimante en 2022…
