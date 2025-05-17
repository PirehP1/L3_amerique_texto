# travail_final
Basile Villebrun

``` r
library(readtext)
library(quanteda)
```

    Package version: 4.2.0
    Unicode version: 14.0
    ICU version: 71.1

    Parallel computing: disabled

    See https://quanteda.io for tutorials and examples.


    Attaching package: 'quanteda'

    The following object is masked from 'package:readtext':

        texts

``` r
library(tidyverse)
```

    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ✔ ggplot2   3.5.2     ✔ tibble    3.2.1
    ✔ lubridate 1.9.4     ✔ tidyr     1.3.1
    ✔ purrr     1.0.2     

    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ dplyr::filter() masks stats::filter()
    ✖ dplyr::lag()    masks stats::lag()
    ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(topicmodels)
library(topicdoc)
source("fonctions_texto.R")
```

**1. Introduction et objectifs du protocole**

**Contexte général :** Mon corpus initial regroupe des textes portant
sur l’histoire de l’Amérique au XIXᵉ siècle et sur les rapports entre la
France et l’Amérique. Le sous-corpus que j’ai choisi d’étudier regroupe,
en particulier, l’ensemble des textes qui traitent spécifiquement de la
Louisiane. L’objectif de ce protocole est de présenter de manière
analytique et textométrique ce sous-corpus afin d’en dégager des
hypothèses de recherche historiques (par exemple, sur les dynamiques de
représentation, la construction identitaire ou les liens diplomatiques
et culturels persistants entre la France et la Louisiane).

**2. Présentation analytique du sous-corpus**

Le sous-corpus se caractérise par sa focalisation sur la Louisiane. Ce
choix permet de mettre en lumière des éléments spécifiques, comme la
persistance d’une identité française dans un territoire marqué par le
passage à l’échelle américaine, ainsi que des tensions ou continuités
entre héritages coloniaux et évolutions politiques.

**Positionnement par rapport aux critères théoriques**

**Critères d’Antoine Prost :**

- **Homogénéité :** Le sous-corpus est homogène en termes de langue
  (français) et relativement homogène au niveau des dates
  (principalement début du XIXᵉ siècle) ainsi qu’en termes de
  nationalité des auteurs. Cependant, une certaine hétérogénéité
  apparaît dans leur fonction (hommes de lettres, scientifiques,
  militaires, etc.) qui enrichit l’analyse des points de vue.

- **Contrativité :** À première vue, le sous-corpus présente peu de
  contrastes (en raison de son homogénéité), mais l’analyse fine
  (notamment en étudiant les genres discursifs) permet de relever des
  différences d’approches et de représentations concernant la Louisiane.

- **Diachronicité :** La tranche temporelle du sous-corpus est
  majoritairement synchronique (principalement autour des années
  1802–1804, avec quelques textes postérieurs). Cela offre une vision
  quasi instantanée de la période tout en permettant de détecter, le cas
  échéant, des évolutions ponctuelles.

**Critères de Bénédicte Pincemin :**

- **Signifiance :** La focalisation sur la Louisiane, sujet central et
  emblématique, confère au sous-corpus une valeur particulière pour
  l’étude des interactions transatlantiques et des héritages coloniaux.

- **Acceptabilité :** Du fait de la rigueur des sélections (textes
  correspondant à un corpus bien défini et vérifié) et de l’alignement
  avec des références bibliographiques présentées, le sous-corpus est
  particulièrement pertinent et acceptable pour une analyse historique
  approfondie.

- **Exploitabilité :** La taille (724 872 mots/107 638 occurrences) et
  la cohérence du sous-corpus, combinées aux différents travaux réalisés
  à l’aide du logiciel R Studio (analyses textométriques, courbe de
  Pareto, etc.), en font un corpus exploitable pour des analyses
  quantitatives et qualitatives.

**Description des documents composant le sous-corpus**

- **Nombre d’ouvrages :** 13 textes issus d’un corpus initial de 171.

- **Distribution temporelle :**

  - **Majorité au XIXᵉ :**

    - 3 textes en 1802

    - 6 textes en 1803

    - 1 texte en 1804

  - **Cas particuliers :**

    - 1 texte en 1829

    - 1 texte en 1860

    - 1 texte en 1929

- **Types d’auteurs :** Prédominance d’auteurs français masculins (avec
  une exception féminine), comprenant des hommes de lettres, des
  scientifiques, un militaire, un religieux et un voyageur. Ainsi, 9
  auteurs différents (et 1 texte anonyme).

- **Lieu d’édition :** Principalement Paris, avec une contribution
  particulière émanant de la Nouvelle-Orléans, ce qui renforce la
  dimension transatlantique.

- **Sujet principal :** La Louisiane, ce qui permet d’isoler les
  problématiques spécifiques à ce territoire dans le contexte des
  rapports franco-américains.

**Positionnement par rapport au corpus initial**

Le sous-corpus représente 13 textes sur 171, concentrant uniquement ceux
qui abordent la Louisiane. Il constitue ainsi une spécialisation qui, en
réduisant la masse totale (724 872 mots sur 14 656 550 mots du corpus
initial et 107 638 occurrences sur 1 793 491), permet une analyse fine
des thématiques spécifiques à la Louisiane dans le contexte des
relations entre la France et l’Amérique.

**3. Présentation des principales caractéristiques textométriques**

Chargement des paquets R. Importation des textes du corpus :

``` r
textes <- readtext("extraction_bdd_19e.csv",
                   text_field = "Texte",
                   docid_field = "id_doc")
```

Création du corpus :

``` r
corpus_amerique <- corpus(textes)
```

Création du dataframe des métadonnées :

``` r
corpus_metadata_Amerique <- docvars(corpus_amerique)
View(corpus_metadata_Amerique)
```

Création du sous-corpus USA_louisiane :

``` r
USA_louisiane <- corpus_subset(corpus_amerique, Region == "Louisiane")
View(docvars(USA_louisiane))
```

Création du dataframe des métadonnées du sous-corpus USA_louisiane :

``` r
corpus_metadata_USA_louisiane <- docvars(USA_louisiane)
View(corpus_metadata_USA_louisiane)
```

**Description des documents composant le corpus**

Création du graphique à barres des différentes régions issues du corpus
initial :

``` r
corpus_metadata_Amerique |> 
 group_by(Region) |>
 summarise(eff = n()) |>
 ggplot(aes(x = Region, y=eff)) +
 geom_bar(stat = "identity") +
 coord_flip()
```

![](travailfinalbasilevlbr_files/figure-commonmark/unnamed-chunk-7-1.png)

![Figure 1 : Graphique en bâton des différents sous-corpus du corpus
initial.](Documents/TP06/figures/figure1.png)

La Figure 1 montre que sur les 171 ouvrages qui composent le corpus
initial, 13 recouvrent la Louisiane. En comparaison avec les autres
régions, la Louisiane est l’une des régions les moins représentées.

Cration de la courbe des ouvrages en fonction de l’année de publication
:

``` r
corpus_metadata_USA_louisiane |>
  group_by(Date_edition_inf) |>
  summarize(Nombre = n()) |>
  mutate(Freqcum = cumsum(Nombre)) |>
  ggplot(aes(x = Date_edition_inf, y = Freqcum)) +
  geom_line() +
  labs(x = "Date", "Fréquences cumulées")
```

![](travailfinalbasilevlbr_files/figure-commonmark/unnamed-chunk-8-1.png)

![Figure 2 : Courbe de la distribution temporelle des ouvrages du
sous-corpus USA_louisiane (fréquences
cumulées).](Documents/TP06/figures/figure2.png)

La courbe de la Figure 2 permet d’observer le nombre d’ouvrages, issus
du sous-corpus, publiés au XIXe siècle. On observe une fréquence de
publication assez élevée durant tout le XIXe siècle et une croissance
exponentielle à partir des années 1810 jusqu’au XXe siècle.

Création du graphique en barres de la distribution temporelle des
ouvrages du sous-corpus :

``` r
corpus_metadata_USA_louisiane |> 
  group_by(Date) |>
  summarise(eff = n()) |>
  ggplot(aes(x = Date, y=eff)) +
  geom_bar(stat = "identity") +
  coord_flip()
```

![](travailfinalbasilevlbr_files/figure-commonmark/unnamed-chunk-9-1.png)

![Figure 3 : Graphique en bâton de la distribution temporelle des
ouvrages du sous-corpus
USA_louisiane.](Documents/TP06/figures/figure3.png)

La Figure 3 montre une publication importante au tout début des années
1800, avec 7 ouvrages publiés la même année, puis un seul ouvrage par an
publié de façon ponctuelle tout au long du XIXe siècle.

Création du graphique en barres de la nationalité des auteurs :

``` r
corpus_metadata_USA_louisiane |> 
  group_by(Nationalite) |>
  summarise(eff = n()) |>
  ggplot(aes(x = Nationalite, y=eff)) +
  geom_bar(stat = "identity") +
  coord_flip()
```

![](travailfinalbasilevlbr_files/figure-commonmark/unnamed-chunk-10-1.png)

![Figure 4 : Graphique en bâton de la nationalité des auteurs du
sous-corpus USA_louisiane.](Documents/TP06/figures/figure4.png)

La Figure 4 nous montre clairement que tous les ouvrages de ce
sous-corpus ont été rédigés par des auteurs français.

Création du graphique en barres de la production d’ouvrages par auteur :

``` r
corpus_metadata_USA_louisiane |> 
  group_by(Nom_auteur) |>
  summarise(eff = n()) |>
  ggplot(aes(x = Nom_auteur, y=eff)) +
  geom_bar(stat = "identity") +
  coord_flip()
```

![](travailfinalbasilevlbr_files/figure-commonmark/unnamed-chunk-11-1.png)

![Figure 5 : Graphique en bâton des noms des différents auteurs du
sous-corpus USA_louisiane.](Documents/TP06/figures/figure5.png)

La Figure 5 nous montre une relative égalité des productions d’ouvrages
par auteurs qui en publient en moyenne un, allant au maximum jusqu’à
trois pour un seul auteur.

Création du graphique en barres de la fonction des auteurs :

``` r
corpus_metadata_USA_louisiane |> 
  group_by(Categorie_fonction) |>
  summarise(eff = n()) |>
  ggplot(aes(x = Categorie_fonction, y=eff)) +
  geom_bar(stat = "identity") +
  coord_flip()
```

![](travailfinalbasilevlbr_files/figure-commonmark/unnamed-chunk-12-1.png)

![Figure 6 : Graphique en bâton des fonctions des auteurs du sous-corpus
USA_louisiane.](Documents/TP06/figures/figure6.png)

La Figure 6 nous montre que la majorité des auteurs sont des hommes de
lettres, puis ce sont surtout des scientifiques et des militaires. Et
enfin un voyageyr et un religieux.

Création du graphique en barres du sexe des auteurs :

``` r
corpus_metadata_USA_louisiane |> 
  group_by(Sexe) |>
  summarise(eff = n()) |>
  ggplot(aes(x = Sexe, y=eff)) +
  geom_bar(stat = "identity") +
  coord_flip()
```

![](travailfinalbasilevlbr_files/figure-commonmark/unnamed-chunk-13-1.png)

![Figure 7 : Graphique en bâton sur le genre des auteurs du sous-corpus
USA_louisiane.](Documents/TP06/figures/figure7.png)

La Figure 7 montre que les auteurs sont majoritairement de sexe
masculin, contre seulement un auteur de sexe féminin.

**Segmentation lexicale (tokenisation) :**

``` r
tokens_USA_louisiane <- tokens (USA_louisiane,
                         remove_punct = TRUE,
                         remove_symbols = TRUE,
                         remove_numbers = TRUE)
tokens_USA_louisiane <- tokens_split(tokens_USA_louisiane, "'")
tokens_USA_louisiane <- tokens_split(tokens_USA_louisiane, "’")
```

**Taille du sous-corpus :**

Nombre total de mots (occurrences) dans le corpus :

``` r
sum(ntoken(USA_louisiane))
```

    [1] 724872

Nombre de formes dans chaque document du corpus :

``` r
sum(ntype(USA_louisiane))
```

    [1] 107638

Construction de la matrice documents-termes du corpus :

``` r
USA_louisiane_dfm <- dfm(tokens_USA_louisiane)
```

**Courbe de Pareto**

Construction de la courbe de Pareto pour avoir une vue d’ensemble de la
distribution des mots dans le sous-corpus :

``` r
plot_pareto(USA_louisiane_dfm)
```

    Loading required package: quanteda.textstats

![](travailfinalbasilevlbr_files/figure-commonmark/unnamed-chunk-18-1.png)

![Figure 8 : Représentation Courbe de pareto du
sous-corpus.](Documents/TP06/figures/figure8.png)

**Comparaison de la taille des documents**

``` r
groups <- group_sizes(USA_louisiane_dfm, part="Numm")
plot_group_sizes(groups = groups, part="Numm")
```

![](travailfinalbasilevlbr_files/figure-commonmark/unnamed-chunk-19-1.png)

![Figure 9 : Graphique en bâton concernant la taille des ouvrages du
sous-corpus USA_louisiane.](Documents/TP06/figures/figure9.png)

La Figure 9 montre que les ouvrages du sous-corpus varient beaucoup en
taille. Certains sont bien plus longs que d’autres, ce qui pose un
problème d’homogénéité et peut fausser l’analyse. En effet, les textes
les plus longs comptent plus de mots et influencent davantage les
résultats, tandis que les plus courts pèsent moins dans l’analyse.

**Première étude des formes les plus fréquentes :**

``` r
lexique_USA_louisiane <- textstat_frequency(USA_louisiane_dfm)
View(lexique_USA_louisiane)
```

Cette commande génère un tableau lexical du sous-corpus, classant les
mots du plus fréquent au moins fréquent.

**Proportion d’Hapax**

``` r
nb_hapax <- sum(lexique_USA_louisiane == 1)
```

Cette commande n’a pas fonctionné sur mon ordinateur.J’y étais parvenu
en cours, mais en refaisant exactement la même chose, ça n’a pas marché.
Je ne peux donc pas donner la proportion d’hapax puisque j’ai
malheureusement perdu le résultat de ce que j’avais fait en cours.

**Explication des prétraitements**

**Retrait des mots outils**

Les mots-outils, ou stop words en anglais, sont des éléments
grammaticaux comme les pronoms, adverbes ou articles. Bien qu’ils soient
très fréquents, ils n’apportent que peu d’information sur le contenu
lexical d’un texte pris isolément. Comme ils ne contribuent pas à
l’identification de thèmes, il est préférable de les exclure pour éviter
de fausser l’analyse.

Filtrage des mots outils présents dans cette liste :

``` r
stopwords_fr <- read.csv("stopwords_fr.csv", encoding = "utf-8")
toks_nostop <- tokens_remove(tokens_USA_louisiane,
                             stopwords_fr$fg,
                             padding=FALSE)
```

On compare les caractéristiques du corpus après avoir retiré les mots
outils du corpus.

``` r
sum(ntoken(toks_nostop))
```

    [1] 264338

``` r
# Là encore, cela n'a pas fonctionné sur mon ordinateur.
```

``` r
sum(ntype(toks_nostop))
```

    [1] 91364

``` r
# Là encore, cela n'a pas fonctionné sur mon ordinateur.
```

**Définition de l’unité documentaire**

La Figure 9 a montré l’intérêt de diviser les documents du sous-corpus
en unités plus petites et comparables. Pour éviter que les textes les
plus longs ne dominent l’analyse, nous avons collectivement choisi de
les découper en paragraphes. Ce choix est aussi pertinent car un
paragraphe correspond souvent à une idée ou un thème, ce qui facilite
l’identification de sujets à l’aide de méthodes comme la LDA.

Découpage des 13 ouvrages du sous-corpus, en repérant les paragraphes à
partir des sauts de ligne (“”) :

``` r
ss_corpus_par <- corpus_segment(USA_louisiane, pattern = "\n")
```

Ce découpage par paragraphe n’est pas parfait puisqu’il compte également
les paragraphes vides, les numéros de chapitre, les paragraphes très
courts, etc. Tokénisation de ce nouveau corpus de paragraphes :

``` r
tokens_par <- tokens(ss_corpus_par,
                     remove_punct = TRUE,
                     remove_symbols = TRUE,
                     remove_numbers = TRUE)
tokens_par <- tokens_split(tokens_par, "'")
tokens_par <- tokens_split(tokens_par, "’")
```

Construction d’un DFM en filtrant les paragraphes vides et les hapax :

``` r
dfm_ss_corpus <- dfm(toks_nostop,
                     remove_padding = TRUE)

dfm_ss_corpus <- dfm_trim(dfm_ss_corpus,
                          min_termfreq = 5)

dfm_ss_corpus <- dfm_subset(dfm_ss_corpus,
                            ntoken(dfm_ss_corpus) > 0,
                            drop_docid = FALSE)
```

**Vers une première approche des thèmes**

Modélisation avec 5 sujets :

``` r
res_lda <- LDA(dfm_ss_corpus, k=5, method="Gibbs",
               control = list(iter = 500,
                              verbose = 25,
                              seed=1307))
```

    K = 5; V = 8681; M = 13
    Sampling 500 iterations!
    Iteration 25 ...
    Iteration 50 ...
    Iteration 75 ...
    Iteration 100 ...
    Iteration 125 ...
    Iteration 150 ...
    Iteration 175 ...
    Iteration 200 ...
    Iteration 225 ...
    Iteration 250 ...
    Iteration 275 ...
    Iteration 300 ...
    Iteration 325 ...
    Iteration 350 ...
    Iteration 375 ...
    Iteration 400 ...
    Iteration 425 ...
    Iteration 450 ...
    Iteration 475 ...
    Iteration 500 ...
    Gibbs sampling completed!

``` r
gamma_df <- get_gamma(res_lda)
beta_df <- get_beta(res_lda)
```

``` r
plot_tm(beta_df, 10)
```

![](travailfinalbasilevlbr_files/figure-commonmark/unnamed-chunk-32-1.png)

Là encore, les trois dernières commandes n’ont pas fonctionné sur mon
ordinateur, j’ai donc récupéré un tableau que j’avais obtenu en cours
avec 9 sujets/topics.

![Figure 10 : Modélisation LDA de 9
sujets.](Documents/TP06/figures/figure10.png)

**Evaluation de la modélisation thématique à l’aide de la cohérence**

Mesure de la fréquence à laquelle les principaux tokens d’un même topic
apparaissent ensemble dans un document. Un bon score de cohérence
reflète la qualité et la pertinence du topic. Utilisation des 50 mots
les plus représentatifs de chaque topic :

``` r
coherence <- topic_coherence(res_lda, dfm_ss_corpus, top_n_tokens = 50)
```

Calcul de la moyenne d’un ensemble de valeurs :

``` r
mean(coherence)
```

    [1] -166.7441

``` r
# Là encore, la commande n'a pas fonctionné sur mon ordinateur.
```

Évolution de la cohérence moyenne selon les variations du nombre de
topics à modéliser :

``` r
k_estimation(dfm = dfm_ss_corpus, min_k = 5, max_k = 20, n_tokens = 50)
```

![](travailfinalbasilevlbr_files/figure-commonmark/unnamed-chunk-36-1.png)

![](travailfinalbasilevlbr_files/figure-commonmark/unnamed-chunk-36-2.png)

        k coherence
    1   5 -211.3256
    2   6 -214.1912
    3   7 -233.3745
    4   8 -240.2766
    5   9 -229.0364
    6  10 -211.1839
    7  11 -224.2703
    8  12 -237.9105
    9  13 -212.2076
    10 14 -228.8524
    11 15 -250.9870
    12 16 -236.5342
    13 17 -263.5416
    14 18 -275.7820
    15 19 -280.0256
    16 20 -270.0312

Là encore, la commande n’a pas fonctionné sur mon ordinateur…

Modélisation LDA avec 8 sujets :

``` r
res_lda <- LDA(dfm_ss_corpus, k=8, method="Gibbs",
               control = list(iter = 500,
                              verbose = 25,
                              seed=1307))
```

    K = 8; V = 8681; M = 13
    Sampling 500 iterations!
    Iteration 25 ...
    Iteration 50 ...
    Iteration 75 ...
    Iteration 100 ...
    Iteration 125 ...
    Iteration 150 ...
    Iteration 175 ...
    Iteration 200 ...
    Iteration 225 ...
    Iteration 250 ...
    Iteration 275 ...
    Iteration 300 ...
    Iteration 325 ...
    Iteration 350 ...
    Iteration 375 ...
    Iteration 400 ...
    Iteration 425 ...
    Iteration 450 ...
    Iteration 475 ...
    Iteration 500 ...
    Gibbs sampling completed!

``` r
gamma_df <- get_gamma(res_lda)
beta_df <- get_beta(res_lda)
```

``` r
plot_tm(beta_df, 10)
```

![](travailfinalbasilevlbr_files/figure-commonmark/unnamed-chunk-39-1.png)

Là encore, ça n’a pas fonctionné…

Modélisation avec 12 sujets :

``` r
res_lda <- LDA(dfm_ss_corpus, k=12, method="Gibbs",
               control = list(iter = 500,
                              verbose = 25,
                              seed=1307))
```

    K = 12; V = 8681; M = 13
    Sampling 500 iterations!
    Iteration 25 ...
    Iteration 50 ...
    Iteration 75 ...
    Iteration 100 ...
    Iteration 125 ...
    Iteration 150 ...
    Iteration 175 ...
    Iteration 200 ...
    Iteration 225 ...
    Iteration 250 ...
    Iteration 275 ...
    Iteration 300 ...
    Iteration 325 ...
    Iteration 350 ...
    Iteration 375 ...
    Iteration 400 ...
    Iteration 425 ...
    Iteration 450 ...
    Iteration 475 ...
    Iteration 500 ...
    Gibbs sampling completed!

``` r
gamma_df <- get_gamma(res_lda)
beta_df <- get_beta(res_lda)
```

``` r
plot_tm(beta_df, 10)
```

![](travailfinalbasilevlbr_files/figure-commonmark/unnamed-chunk-42-1.png)

Là encore, ça n’a pas fonctionné…

**Description des sujets :**

Étant donné que mon ordinateur n’a pas voulu classer les thèmes comme
souhaité, et que j’ai du récupérer un tableau que j’avais fait en cours,
ce dernier n’est pas très fiable et ne propose pas des thèmes très
convainquants. J’ai tout de même tenté de faire quelque chose avec ces
listes de mots.

Thème 1 :

eaux, temps, grand, france, mississipi, pays, colonies, français,
louisiane, colonie. On peut penser à un thème sur l’héritage colonial
français et mémoire de la Louisiane française.

Liste 2 :

eau, premier, manière, gouvernement, fleuve, partie, état, sauvages,
nom, i. Thème : Administration et description des débuts de la colonie.

Liste 3 :

sol, rendre, nombre, états-unis, rivière, grande, hommes, jour, salle,
général. Thème : Expansion des États-Unis et rivalités territoriales

Liste 4 :

mer, années, espèce, europe, lieues, terre, homme, grand, mois, lieu.
Thème : Voyages transatlantiques et descriptions de la nature.

Liste 5 :

effet, doute, trouve, the, lac, bois, colons, tems, p, bientôt. Thème :
Découverte d’un environnement inconnu

Liste 6 :

pieds, grands, cas, commerce, bords, ville, moyens, pays, année, voir.
Thème : Urbanisation et commerce en Louisiane.

Liste 7 :

fond, travaux, voit, anglais, cours, culture, gouvernement, nation,
nouvelle, grondel. Thème : Influences culturelles.

Liste 8 :

abord, peine, également, guerre, nord, eft, commerce, ordre, jours,
reste. Thème : Difficultés des conflits.

Liste 9 :

question, vue, raison, états, golfe, terres, donner, nations, père,
gouverneur. Thème : Cessions de terres.

**Bibliographie :**

Aïssatou Sy-Wonyu, Les États-Unis et le monde au XIXe siècle, Nouv.éd.,
Paris, A. Colin, 2017.

Élise Marienstras (dir.), L’Amérique et la France : Deux révolutions,
Paris, Éditions de la Sorbonne, 1991.

Marjorie Bourdelais, « Les immigrants français à La Nouvelle-Orléans au
XIXe siècle : une longue stabilité des formes d’intégration », Annales
de démographie historique, 2000, p. 27-42.

Georges Spillmann, « L’irrédentisme français en Louisiane : la France en
Amérique du Nord », Revue du Souvenir Napoléonien, 1980.

Marcel Dorigny, Marie-Jeanne Rossignol (dir.), La France et les
Amériques entre révolutions et nations : 1776-1871, Rennes, Presses
universitaires de Rennes, 2021.
