Ce document est le rapport final d'un projet de recherche collectif portant sur les textes français traitant de l'Amérique au XIXe siècle. Il présente le sous-corpus Canada - issu du corpus Amérique du catalogue général de la BnF. Ce projet s'inscrit dans le cadre de l'enseignement d'Histoire et Informatique de l'Université Paris 1 Panthéon-Sorbonne en troisième année de licence Histoire.

Pour cela nous utilisons l'environnement de développement (IDE) Rstudio et le langage de programmation R qui nous permettent l'usage de méthodes textométriques afin de décrire et d'explorer le corpus.

Ce document a été réalisé dans un fichier quarto puis diffusé sur la plateforme github en markdown.

Ce projet s'inscrit dans une démarche de science ouverte où nous expérimentons de nouvelles méthodes pour approfondir de grandes collections de documents. Nous proposons donc une lecture distante de ce corpus à l'aide de méthodes textométriques, sans pour autant délaisser une lecture plus classique avec un retour aux concordances.

# Présentation analytique du sous-corpus choisi

Le sous corpus initial est composé de 171 ouvrages. Afin de faciliter le travail, nous avons choisi de le diviser en sous-corpus. Je me suis occupé du sous-corpus Canada. Ce sous-corpus est composé de 24 ouvrages publiés principalement dans la seconde moitié du XIXe siècle. Ils sont tous rédigés en langue française. Nous avons décidé de nous concentrer sur le XIXe siècle car la majorité des ouvrages du corpus que nous avons récupérés dans la base de données de la Bibliothèque Nationale de France (BNF) sont rédigés à cette. Rajoutons également le fait que nous avons privilégié le XIXe afin que les documents soient rédigés dans la même langue. En effet, le français du XVIe siècle n'est pas le même que celui du XIXe siècle. La langue a évolué, les outils textométriques nécessitent l'emploi d'une langue unique. 

Les auteurs ont différentes fonctions ; il y a neuf hommes de lettres, trois artistes, deux scientifiques, trois militaires, un juriste, deux hommes politiques ou administrateurs, un religieux ainsi que trois auteurs dont la fonction n'est pas précisée. 

Damon Mayaffre, historien du discours travaillant avec des méthodes textométriques, a repéré deux aspects importants dans la définition d'un corpus dans un article publié en 2002. Tout d'abord, un corpus est une mise en série de textes, autrement dit, un corpus est un rassemblement de textes. Deuxièmement, la dimension heuristique est inhérente à un corpus. Ainsi, le sous-corpus Canada s'inscrit pleinement dans la définition de Damon Mayaffre puisqu'il comprend 24 ouvrages qui nous permettent de produire de la connaissance scientifique.

# Afin d'étudier mon sous-corpus, j'ai suivi le protocole suivant :

```{r}

source(file = "fonctions_texto.R")
```

Ensuite on charge les paquets R nécessaires.

On importe les textes du corpus avec `readtext`.

```{r}
textes <- readtext("extraction_bdd_19e.csv", text_field = "text", docid_field = "doc_id")
```

On crée le corpus dans `quanteda`.

```{r}
corpus_voyageurs <- corpus(textes)
```

On crée le *dataframe* des métadonnées.

```{r}
corpus_metadata <- docvars(corpus_voyageurs)
View(corpus_metadata_Amerique)

```

On crée maintenant maintenant le sous corpus `canada`

```{r}
canada <- corpus_subset(corpus_amerique, Region == "canada")
View(docvars(canada))
```

Le dataframe est un tableau, autrement dit un objet de type tableau dans R. On crée le dataframe des métadonnées du sous-corpus `canada`.

```{r}
corpus_metadata_canada <- docvars(canada)
View(corpus_metadata_canada)
```

# Description des documents composant le corpus.

 Graphique sur la distribution temporelle des ouvrages du sous corpus. 

```{r}
corpus_metadata_canada |> 
  group_by(Date) |>
  summarise(eff = n()) |>
  ggplot(aes(x = Date, y=eff)) +
  geom_bar(stat = "identity") +
  coord_flip()
```

# Présentation des principales caractéristiques textométriques du sous-corpus

## Segmentation lexicale (*tokenization*)

Nous allons maintenant procéder à la **segmentation lexicale** du vocabulaire du sous-corpus avec les fonctions `tokens()` et `tokens_split()` de *quanteda*. Cette dernière permet d'utiliser les apostrophes comme un caractère séparateur permettant de découper les mots du corpus.

```{r}
tokens_canada <- tokens(canada,
                           remove_punct = TRUE,
                           remove_symbols = TRUE,
                           remove_numbers = TRUE)
tokens_canada <- tokens_split(canada, "'")
tokens_canada <- tokens_split(canada, "’")
```

## Taille du sous-corpus

Commençons par compter le nombre total de mots (**occurrences**) présents dans le corpus.

```{r}
sum(ntoken(canada))
```
La fonction nous donne le résultat suivant : 1840055. Cela nous montre qu'il s'agit d'un corpus assez important pour pouvoir être étudier. 

La fonction `ntype()` permet de compter le nombre de **formes** dans chaque document du corpus.

```{r}
sum(ntype(canada))
```
Cela nous donne le résultat suivant : 219365.

Modélisation « sac de mots » du corpus. On utilise la fonction ``dfm()`de `quanteda` pour construire la matrice documents-termes du corpus.

```{r}

canada_dfm <- dfm(tokens_canada)
```

# Courbe de Pareto

Nous pouvons la réaliser grâce à la fonction `plot_pareto`. Elle nous permet de voir la distribution des mots dans le sous-corpus.

```{r}

plot_pareto(canada_dfm)
```

# Comparaison de la taille des documents

Nous pouvons le faire grâce à la fonction suivante : 

```{r}
groups <- group_sizes(canada_dfm, part="Numm")

plot_group_sizes(groups = groups, part="Numm")
```

# Première études des formes les plus fréquentes

Nous pouvons calculer le dictionnaire des fréquences via la fonction `textstat_frequency()`du paquet quanteda.textstats , ce qui nous permet, dans le cadre de l'analyse de données textuelles, de repérer quels sont les mots les plus fréquents dans notre sous-corpus.

```{r}
lexique_canada <- textstat_frequency(canada_dfm)
View(lexique_canada)
```
Nous obtenons un tableau comprenant le lexique du sous-corpus et affichant les mots ayant la fréquence la plus élevée jusqu'aux mots ayant la fréquence la moins élevée.

# Proportion d'hapax 
Un hapx représente le nombre de mots qui n'apparaît qu'une seule fois. Il est important de les ôter afin de pouvoir analyser le sous-corpus plus facilement étant donné qu'ils n'apparaissent qu'une seule fois.

```{r}
nb_hapax <- sum(lexique_canada == 1)
```
On obtient le nombre 77870. Dans notre sous corpus, pour 1840055 occurrences il y a 77870 hapax.

# Retrait des mots outils 

Les mots outils sont les mots les plus fréquents que l'on utilise pour la syntaxe ou encore la grammaire. Ces mots sont importants mais on les retire afin de pouvoir dans la suite analyser plus efficacement le corpus car bien qu'ils soient utiles, individuellement ils ne nous apportent pas beaucoup d'informations. 

```{r}

stopwords_fr <- read.csv("stopwords_fr.csv", encoding = "utf-8")
toks_nostop <- tokens_remove(tokens_canada,
                             stopwords_fr$fg,
                             padding=FALSE)
```
On compare les caractéristiques du corpus après les avoir retirées du corpus.

```{r}
sum(ntoken(toks_nostop))
```
Après avoir utilisé cette fonction on obtient 640352.

```{r}
sum(ntype(toks_nostop))
```
Après avoir utilisé celle-ci, on obtient 183767.


# Définition de l'unité documentaire

Nous allons découper nos 24 documents en paragraphes avec la fonction suivante :
```{r}
ss_corpus_par <- corpus_segment(canada, pattern = "\n")
```
On obtient 69554, cela correspond au nombre de paragraphes. Ce découpage n'est pas parfait car il peut compter des paragraphes vides, des numéros de chapitres etc. C'est pourquoi nous allons procéder à la tokenisation de ce nouveau corpus râce à la fonction suivante :


```{r}
tokens_par <- tokens(ss_corpus_par,
                     remove_punct = TRUE,
                     remove_symbols = TRUE,
                     remove_numbers = TRUE)
tokens_par <- tokens_split(tokens_par, "'")
tokens_par <- tokens_split(tokens_par, "’")
```

Ensuite, nous construisons un DFM en filtrant les paragraphes vides et les termes dont l'occurrence est faible, autrement dit les hapax :


```{r}
dfm_canada <- dfm(toks_nostop,
                     remove_padding = TRUE)


dfm_canada <- dfm_trim(dfm_canada,
                          min_termfreq = 5)

dfm_canada <- dfm_subset(dfm_canada,
                            ntoken(dfm_canada) > 0,
                            drop_docid = FALSE)
```



# Vers une première approche des thèmes

Il s'agit de faire une première démarche expérimentale pour déterminer un nombre de sujets à modéliser via la méthode LDA ( Allocation de Dirichlet latente ). C'est un algorithme faisant partie des méthodes de modélisation thématique (Cointet et Parasie (2018)) et qui a été mis au point en 2003 par David Blei, dont l'objectif est de détecter des thèmes dans un ensemble de documents. C'est une méthode statistique qui marche avec une loi de probabilité.


On fait une modélisation  avec quinze thèmes.
```{r}
res_lda <- LDA(dfm_canada, k=15, method="Gibbs",
               control = list(iter = 500,
                              verbose = 25,
                              seed=1307))
```


```{r}
K = 15; V = 15536; M = 24
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
```

```{r}
gamma_df <- get_gamma(res_lda)
 beta_df <- get_beta(res_lda)
 plot_tm(beta_df, 10)
```
La méthode LDA repose sur deux théories principales :
  
  Un topic est une collection de mots ;
Un document est une collection de sujets.
Ces deux théories sont confirmées par le code suivant :


```{r}
gamma_df |>
  group_by(Document) |>
  summarise(Proba = sum(Probability)) |>
  View()

beta_df |>
  group_by(Topic) |>
  summarise(Proba = sum(Probability)) |>
  View()
```
On constate avec la matrice gamma que si on fait la somme des probabilités de chaque sujet pour un document, on obtient 1. C'est la démonstration qu'un document est une collection de sujets . Ces sujets , selon les documents vont être plus ou moins représentés, autrement dit, avoir des probabilités plus ou moins fortes.

Avec la matrice beta, on fait le constat que si on fait la somme des probabilités des tokens d'un corpus, on obtient un total de 1 pour chaque topic . C'est la démonstration qu'un topic est une collection de tokens . Chaque jeton va avoir une probabilité plus ou moins forte dans un sujet donné.


# Évaluation de la modélisation thématique à l'aide de la cohérence

La cohérence permet d'évaluer la « cohérence sémantique », c'est-à-dire qu'elle permet de mesurer les mots les plus récurrents dans un sujet qui ont plus de chance d'être dans les mêmes documents. Autrement dit, on considère que la cohérence d'un sujet est d'autant plus forte que les mots qui le composent ont plus de chance d'être souvent employés ensemble dans les mêmes documents.

La fonction `topic_cohrence()` mesure la fréquence à laquelle les principaux jetons de chaque sujet apparaissent ensemble dans le même document. Un bon score de cohérence indique la pertinence d'un sujet . Ici, prenons nous 50 mots pour chaque sujet .
```{r}
coherence <- topic_coherence(res_lda, dfm_ss_corpus, top_n_tokens = 50)
```

La fonction `mean()` permet d'obtenir la moyenne d'un ensemble de valeurs. Dans notre cas, elle calcule la moyenne des scores de cohérence des sujets obtenus par la méthode LDA. Elle nous permet donc de voir si les thèmes sont cohérents. Plus le score est proche de 0 plus la cohérence du sujet est pertinente.

```{r}
mean(coherence)
```
On obtient -419.5425. Cela nous montre que les mots n'apparaissent pas souvent dans les mêmes documents. Cela nous conduit à essayer plusieurs nombre de topics pour voir lequel nous aiderait à bien examiner notre corpus.


La fonction `k_estimation()`permet d'observer l'évolution de la cohérence moyenne selon les variations du nombre de sujets à modéliser. Elle est utilisée pour essayer différents nombres de sujets dans une modélisation LDA dans l'objectif de déterminer quelle valeur de `k`octroie la meilleure cohérence.
```{r}
k_estimation(dfm = dfm_canada, min_k = 5, max_k = 20, n_tokens = 50)
```
Cela nous a appris que les meilleures cohérences sont avec 6 ou 14 topics. Nous allons faire ces deux tests et voir lequel nous convient le mieux.

Cela nous permet d'observer 6 topics.
```{r}
res_lda <- LDA(dfm_canada, k=6, method="Gibbs",
               control = list(iter = 500,
                              verbose = 25,
                              seed=1307))
```

```{r}
gamma_df <- get_gamma(res_lda)
beta_df <- get_beta(res_lda)
plot_tm(beta_df, 10)
```


Cela nous permet d'observer 14 topics.
```{r}
res_lda <- LDA(dfm_canada, k=14, method="Gibbs",
               control = list(iter = 500,
                              verbose = 25,
                              seed=1307))
```

```{r}
gamma_df <- get_gamma(res_lda)
beta_df <- get_beta(res_lda)
plot_tm(beta_df, 10)
```

Nous pouvons voir que lorsqu'il n'y a que six topics, ils sont trop généraux. Il est par conséquent plus intéressant pour nous d'en avoir 14. 


# Description des sujets.

Le premier topic représente les peuples qui étaient présents au Canada quand les Européens sont arrivés. Nous pouvons voir " les Iroquois." 
Le deuxième évoque le commerce lié à la pêche. Ce qui est très important dans une société chrétienne qui ne mange pas de viande le vendredi. 
Le troisième la conquête du territoire par les Français, rappelons que le royaume de France a perdu le Canada en 1763, suite à la défaite de la France lors de la guerre de sept ans (1756-1763).
Le quatrième évoque l'installation des colons et de l'administration française dans la colonie.
Le cinquième évoque le commerce avec les "français", et les "américains", la monnaie le "franc" est mentionné. Cela nous indique que le commerce est encore présent au XIXe siècle entre le Canada et les Français. 
Le sixième, le milieu dans lequel vivaient les "indiens". La "ville" dans laquelle ils vivent est dans un milieu naturel.
Le septième évoque l'armée.
Le huitième les routes commerciales.
Le neuvième, le commerce côtier.
Le dixième le lien entre les hommes et la nature. Nous pouvons supposer que c'est pour l'exploiter.
Le onzième et le douzième évoquent l'armée.
Le treizième le lien entre la France et l'Amérique.
Le dernier nous donne des informations sur la population.
