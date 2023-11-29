Systematic Literature Review using litsearchr
================
K Wolcott
16 Nov 2023

<style>
table {
  background-color: white !important;
  color: black !important;
}
</style>

## Introduction

Use this notebook to carry out a systematic literature search using
litsearchr on a Mac OS. We are investigating the morphology and
evolution of the Byttnerioideae, a taxonomically challenging clade
within the Malvaceae. First, you will use some manually selected
keywords for preliminary searches. Then, keywords will be automatically
extracted from the abstracts found during the preliminary passes.
Finally, carry out a systematic literature search using the complete
list of keywords and analyze patterns in found articles.

These steps were made following a tutorial from
<https://www.r-bloggers.com/2023/03/automated-systematic-literature-search-using-r-litsearchr-and-google-scholar-web-scraping/>

## This is an R Markdown Document

Markdown is a simple formatting syntax for authoring HTML, PDF, and MS
Word documents. For more details on using R Markdown see
<http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that
includes both content as well as the output of any embedded R code
chunks within the document.

## Install and load packages and functions

``` r
# Install litsearchr using R devtools
cat("\nInstalling litsearchr package from GitHub ")
```

    ## 
    ## Installing litsearchr package from GitHub

``` r
if(require(litsearchr)) remotes::install_github("elizagrames/litsearchr", ref = "main")
```

    ## Loading required package: litsearchr

    ## Skipping install of 'litsearchr' from a github remote, the SHA1 (0c108e30) has not changed since last install.
    ##   Use `force = TRUE` to force installation

``` r
# Dependency packages to load/install
packages = c(
  "litsearchr", "dplyr", "stopwords","readr", "devtools", "kableExtra",
  "ggplot2", "ggraph", "ggrepel", "igraph", "rvest", "httr", "knitr"
)

# Install packages not yet installed
installed_packages = packages %in% rownames(installed.packages())
if (any(installed_packages == FALSE)) {
  install.packages(packages[!installed_packages])
  install.packages("igraph", type="binary")
}

# Load packages
invisible(lapply(packages, library, character.only = TRUE))
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

    ## Loading required package: usethis

    ## 
    ## Attaching package: 'kableExtra'

    ## The following object is masked from 'package:dplyr':
    ## 
    ##     group_rows

    ## 
    ## Attaching package: 'igraph'

    ## The following objects are masked from 'package:dplyr':
    ## 
    ##     as_data_frame, groups, union

    ## The following objects are masked from 'package:stats':
    ## 
    ##     decompose, spectrum

    ## The following object is masked from 'package:base':
    ## 
    ##     union

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
cat("\nLoaded packages:\n", packages)
```

    ## 
    ## Loaded packages:
    ##  litsearchr dplyr stopwords readr devtools kableExtra ggplot2 ggraph ggrepel igraph rvest httr knitr

``` r
# List listsearchr version (will help with troubleshooting)
cat("\n\nlitsearchr version:")
```

    ## 
    ## 
    ## litsearchr version:

``` r
packageVersion("litsearchr")
```

    ## [1] '1.0.0'

``` r
# Load in scrape_gs() function from GitHub to scrape Google Scholar for articles
devtools::source_url("https://gist.githubusercontent.com/ClaudiuPapasteri/7bef34394c395e03ee074f884ddbf4d4/raw/35c20268c3e3516d267ff2e8b96a4648195ed5ce/scrape_scholargooglesearch.R")
```

    ## ℹ SHA-1 hash of file is "c9c689bc9a877edacc642b63465b66a48508c70d"

``` r
cat("\n\nLoading scrape_gs function from GitHub")
```

    ## 
    ## 
    ## Loading scrape_gs function from GitHub

``` r
#View(scrape_gs) # Un-comment this line to inspect function in new tab
```

## Setup Markdown notebook parameters

## Set working directory

TO DO: Define your wd variable below

``` r
wd = "/Users/katherinewolcott/Documents/r/jill" # TO DO: define your wd
cwd = paste(wd, "/litrev", sep="")
ifelse(!dir.exists(wd), c(cat("Creating directory", cwd), dir.create(cwd, recursive=TRUE)), c(cat("Directory already created", cwd, " "), FALSE))
```

    ## Directory already created /Users/katherinewolcott/Documents/r/jill/litrev

    ## [1] FALSE

``` r
knitr::opts_knit$set(root.dir = cwd)
cat("Working directory set to: ", cwd)
```

    ## Working directory set to:  /Users/katherinewolcott/Documents/r/jill/litrev

## TEST 1: Try out code to search Google Scholar and automatically extract new search terms

``` r
# Run test query for 1 search phrase and the first 5 pages of Google Scholar results
## intext searches abstract instead of title
phrase1 = 'intext:"Byttnerioideae" AND "morphology"'  # Use Boolean operators
test1 = scrape_gs(term = phrase1, crawl_delay = 1.2, pages = 1:5) 
cat("Test 1: Query results from Google Scholar\n")
```

    ## Test 1: Query results from Google Scholar

``` r
# Display the table as a scrollable object using kableExtra package
kbl(test1, table.attr = "style = \"color: black;\"") %>%
  kable_styling("striped", full_width = F) %>%
  scroll_box(width = "100%")
```

<div
style="border: 1px solid #ddd; padding: 5px; overflow-x: scroll; width:100%; ">

<table style="color: black; width: auto !important; margin-left: auto; margin-right: auto;" class="table table-striped">
<thead>
<tr>
<th style="text-align:right;">
page
</th>
<th style="text-align:left;">
term
</th>
<th style="text-align:left;">
title
</th>
<th style="text-align:left;">
authors
</th>
<th style="text-align:left;">
year
</th>
<th style="text-align:right;">
n\_citations
</th>
<th style="text-align:left;">
abstract
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
Phylogenetic relationships and floral evolution of the Byttnerioideae
(“Sterculiaceae” or Malvaceae sl) based on sequences of the chloroplast
gene, ndhF
</td>
<td style="text-align:left;">
BA Whitlock, C Bayer, DA Baum
</td>
<td style="text-align:left;">
2001
</td>
<td style="text-align:right;">
143
</td>
<td style="text-align:left;">
… While never proposed prior to the molecular work, the monophyly of
Byttnerioideae is consistent with floral morphology. The majority of
these plants share a basic floral plan of perfect, …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
Pollen morphology and exine ultrastructure of selected species of
Waltheria L.(Byttnerioideae-Malvaceae)
</td>
<td style="text-align:left;">
MD Saba, FAR dos Santos
</td>
<td style="text-align:left;">
2015
</td>
<td style="text-align:right;">
18
</td>
<td style="text-align:left;">
… In the present study, the pollen morphology of five species of
Waltheria (Waltheria albicans … (TEM), with emphasis on the differences
in pollen morphology from heterostyled morphs. …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] A synopsis of Byttnerieae (Malvaceae, Byttnerioideae)
from the Atlantic Forest, with notes on geographical distribution,
nomenclature, and conservation
</td>
<td style="text-align:left;">
M Colli-Silva, JR Pirani
</td>
<td style="text-align:left;">
2021
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">
… In this work, we provide novel contributions for one of the nine
subfamilies of Malvaceae - Byttnerioideae -, by presenting a synoptic
taxonomical treatment with comments on the …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
Waltheria marielleae (Byttnerioideae, Malvaceae), a new species from
north-eastern Brazil supported by morphological and phylogenetic
evidence
</td>
<td style="text-align:left;">
TS Coutinho, MA Sader, A Pedrosa-Harand
</td>
<td style="text-align:left;">
2022
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
Background – Waltheria marielleae is a new species of Malvaceae endemic
to north-eastern Brazil that occurs only in the states of Pernambuco and
Alagoas, in areas of Atlantic Forest …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Pollen morphology of Waltheria L.
(Malvaceae-Byttnerioideae) from Bahia, Brazil
</td>
<td style="text-align:left;">
CEA Silveira, LCL Lima, MD Saba
</td>
<td style="text-align:left;">
2017
</td>
<td style="text-align:right;">
15
</td>
<td style="text-align:left;">
Waltheria encompasses about 60 species distributed in Brazil and Mexico.
To improve the palynology of the genus, we analyzed 14 species occurring
in the Brazilian State of Bahia. …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Seed morphology and its taxonomic significance in the
family Malvaceae
</td>
<td style="text-align:left;">
R Abid, A Ather, M Qaiser
</td>
<td style="text-align:left;">
2016
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:left;">
The seed morphological studies of 75 taxa belonging to 6 sub-families of
the family Malvaceae were carried out from Pakistan. In Pakistan the
family Malvaceae is represented by 6 sub-…
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
POLLEN MORPHOLOGY AND HARMOMEGATHIC CHARACTERS OF Byttneria LÖFL.
SPECIES (STERCULIACEAE S. S: SUBFAM. BYTTNERIOIDEAE)
</td>
<td style="text-align:left;">
AJ Amirul-Aiman, T Noraini
</td>
<td style="text-align:left;">
2019
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
A palynological study was conducted on seven species of Byttneria Löfl.
The objectives of this study was to understand the variation in
micromorphological and harmomegarthic …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Foliage and seeds of malvalean plants from the Eocene of
Europe
</td>
<td style="text-align:left;">
Z Kvacek, V Wilde
</td>
<td style="text-align:left;">
2010
</td>
<td style="text-align:right;">
18
</td>
<td style="text-align:left;">
… in gross morphology and … morphology has been found in the population
from Kučlín, from which evidence of epidermal anatomy is not available.
In addition to gross morphology…
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Exploring Phylogenetic Relationships in Malvaceae Using
Sequence Data of Matk Gene
</td>
<td style="text-align:left;">
I Jamil, R Abid, S Kousar
</td>
<td style="text-align:left;">
I Jamil, R Abid, S Kousar - juw.edu.pk
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">
… Byttneriina clade comprising the subfamilies, Grewioideae and
Byttnerioideae and placed at the base of Malvadendrina clade that
comprising all remaining subfamilies of extended …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
Systematics, Evolution, and Biogeography of Ayenia (Malvaceae Subfamily
Byttnerioideae)
</td>
<td style="text-align:left;">
WV Sharber
</td>
<td style="text-align:left;">
2018
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
… Ayenia is one of two genera in the Byttnerioideae with an
androgynophore, although there is substantial variation in androecium
morphology, including the length of the staminal column…
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
The phylogeny of Ayenia, Byttneria, and Rayleya (Malvaceae sl) and its
implications for the evolution of growth forms
</td>
<td style="text-align:left;">
BA Whitlock, AM Hale
</td>
<td style="text-align:left;">
2011
</td>
<td style="text-align:right;">
19
</td>
<td style="text-align:left;">
… Androecium and petal morphology separate the approximately 135 species
of Byttneria … In addition, we used sequences from five other
Byttnerioideae as outgroups (Appendix 1), …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
Male and female booths with separate entrances in the tiny flowers of
Guazuma ulmifolia (Malvaceae–Byttnerioideae). I. Structural integration
</td>
<td style="text-align:left;">
C Westerkamp, AA Soares
</td>
<td style="text-align:left;">
2006
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:left;">
In spite of their tiny dimensions (3×5mm 2 ), the strongly honey-scented
flowers of Guazuma ulmifolia (Malvaceae–Byttnerioideae) provide six
sexual chambers. A central female unit is …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] New species of Waltheria (Hermannieae, Byttnerioideae,
Malvaceae) from Paraguay, Argentina, and Venezuela, and two new records
for Paraguay
</td>
<td style="text-align:left;">
JG Saunders
</td>
<td style="text-align:left;">
2005
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
… Pollen morphology is first described for. carmensarae and Kohler’s
description for W. … the Byttnerioideae basal to the Grewioideae in an
expanded Malvaceae (Bayer & Kubitzki, 2003). …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Especies nuevas de Waltheria (Hermannieae,
Byttnerioideae, Malvaceae) de Paraguay, Argentina y Venezuela, y dos
nuevas citas para Paraguay
</td>
<td style="text-align:left;">
JG Saunders
</td>
<td style="text-align:left;">
2005
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
… New species of Waltheria (Hermannieae, Byttnerioideae, Malvaceae) from
Paraguay, Argentina, … Pollen morphology is first described for.
carmensarae and Kohler s description for W. …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Bombacoideae, Byttnerioideae, Grewioideae and
Helicterioideae (Malvaceae sl) in the Raso da Catarina Ecoregion, Bahia,
Brazil
</td>
<td style="text-align:left;">
JB Lima, MG Bovini, AS Conceição
</td>
<td style="text-align:left;">
2019
</td>
<td style="text-align:right;">
8
</td>
<td style="text-align:left;">
This work presents a floristic survey of the subfamilies Bombacoideae,
Byttnerioideae, Grewioideae and Helicterioideae (Malvaceae sl) in the
Raso da Catarina Ecoregion (RCE), Bahia…
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
Synopsis of Melochia L. (Byttnerioideae, Malvaceae) in southeastern
Brazil
</td>
<td style="text-align:left;">
VM Goncalez, GL Esteves
</td>
<td style="text-align:left;">
2015
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
… This study provides a taxonomic treatment including an identification
key, illustrations, comments on morphology, habitat, geographical data
and conservation status. Some species …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
Estimating bioregions and undercollected areas in South America by
revisiting Byttnerioideae, Helicteroideae and Sterculioideae (Malvaceae)
occurrence data
</td>
<td style="text-align:left;">
M Colli-Silva, JR Pirani
</td>
<td style="text-align:left;">
2020
</td>
<td style="text-align:right;">
11
</td>
<td style="text-align:left;">
… , its genera were rearranged into three distinct subfamilies:
Byttnerioideae (the cacao group), … distributional information of
species of Byttnerioideae, Helicteroideae and Sterculioideae in …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Novelties in Brazilian Waltheria L. (Byttnerioideae,
Malvaceae): two new species and one re-establishment
</td>
<td style="text-align:left;">
TS Coutinho, M Colli-Silva, M Alves
</td>
<td style="text-align:left;">
2020
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
… Byttnerioideae is characterized by having clawed and often caducous
petals, small flowers, shorter than 2-3 cm in length, without epicalyx
and usually with a reduced or absent …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
Theobroma flaviflorum (Malvaceae: Byttnerioideae), a New Species from
the Osa Peninsula, Costa Rica
</td>
<td style="text-align:left;">
D Santamaría-Aguilar, RA Fernández
</td>
<td style="text-align:left;">
2023
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">
… However, the easily recognizable morphology and restricted
distribution was identifiable in images provided by Leonardo Álvarez
Alcázar (some of these available at Plant …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
A new distylous Waltheria L.(Byttnerioideae, Malvaceae) from the state
of Bahia, Brazil
</td>
<td style="text-align:left;">
TS Coutinho, M Alves
</td>
<td style="text-align:left;">
2019
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
A new distylous Waltheria species from Bahia, Brazil, is described and
illustrated. Waltheria saundersiae is endemic to the campos rupestres
from Bahia and is a shrub with erect to …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] A taxonomic dataset of preserved specimen occurrences of
Theobroma and Herrania (Malvaceae, Byttnerioideae) stored in 2020
</td>
<td style="text-align:left;">
M Colli-Silva, J Richardson, J Pirani
</td>
<td style="text-align:left;">
2023
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
Species from the “cacao group” are traditionally allocated into two
genera, Theobroma and Herrania (Malvaceae, Byttnerioideae), both groups
of Neotropical species economically …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
Ayenia albiflora, (Malvaceae, Byttnerioideae) a new species from the
Cerrado of Goiás, Central‐West Brazil
</td>
<td style="text-align:left;">
M Colli‐Silva, IL de Morais
</td>
<td style="text-align:left;">
2022
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
In this work, we describe a new species of Ayenia (Malvaceae,
Byttnerioideae). Ayenia albiflora sp. nov. is known from the Cerrado
areas of southern Goiás, Central‐West Brazil. It can …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Genome size, cytogenetic data and transferability of
EST-SSRs markers in wild and cultivated species of the genus Theobroma
L. (Byttnerioideae, Malvaceae …
</td>
<td style="text-align:left;">
RA da Silva, G Souza, LSL Lemos, UV Lopes
</td>
<td style="text-align:left;">
2017
</td>
<td style="text-align:right;">
16
</td>
<td style="text-align:left;">
… grandiflorum, with most chromosome pairs similar in morphology and
size, corroborating with our findings \[45\]. Plant species native from
tropical forests tend to have a reduced genome …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Pollen morphology of Sterculiaceae (s. str.) in Egypt and
its taxonomic significance
</td>
<td style="text-align:left;">
R Hamdy, E Shamso
</td>
<td style="text-align:left;">
2010
</td>
<td style="text-align:right;">
16
</td>
<td style="text-align:left;">
… the other two subfamilies Byttnerioideae and Sterculioideae; both have
medium to small, reticulate pollen grains with trizonobrevicolpate or 3
to 4–5 zonocolporate. Byttnerioideae is an …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
A new tribal classification of Grewioideae (Malvaceae) based on
morphological and molecular phylogenetic evidence
</td>
<td style="text-align:left;">
U Brunken, AN Muellner
</td>
<td style="text-align:left;">
2012
</td>
<td style="text-align:right;">
46
</td>
<td style="text-align:left;">
… In contrast to Byttnerioideae, staminodes occur exclusively in the
periphery of an otherwise … (1926) based on resemblance in flower and
fruit morphology, and this relationship, has been …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] A revision of Lasiopetalum (Malvaceae: Byttnerioideae)
from the northern sandplains of Western Australia, including two new
species
</td>
<td style="text-align:left;">
KA Shepherd, CF Wilkins
</td>
<td style="text-align:left;">
2021
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
… to the south or the Avon Wheatbelt and Yalgoo boundaries to the east,
have been included with allied species in previous treatments along that
focused on shared morphology, such as …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Pollen flora of Pakistan-Malvaceae: Dombeyoideae-Lxii
</td>
<td style="text-align:left;">
A Perveen, M Qaiser
</td>
<td style="text-align:left;">
2009
</td>
<td style="text-align:right;">
20
</td>
<td style="text-align:left;">
… now distributed in subfamilies, Byttnerioideae, Helicteroideae, …
morphology of the subfamily Dombeyoideae from Pakistan. Present
investigations are based on the pollen morphology …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
A key to the species of Thomasia (Malvaceae: Byttnerioideae).
</td>
<td style="text-align:left;">
CF Wilkins, KA Shepherd
</td>
<td style="text-align:left;">
2019
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">
Thomasia (Malvaceae) is predominantly Western Australian; only T.
petalocalyx has populations that extend to South Australia and Victoria.
There are 31 formally named species in the …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
Pollen morphology of Malvaceae sl from Cerrado forest fragments: details
of aperture and ornamentation in the pollen types definition
</td>
<td style="text-align:left;">
ACV Lopes, CN de Souza, MD Saba
</td>
<td style="text-align:left;">
2022
</td>
<td style="text-align:right;">
10
</td>
<td style="text-align:left;">
… We analyzed the pollen morphology of 13 Brazilian native species of
Malvaceae sl from … that can expand the knowledge on the pollen
morphology of species of the family that occur in …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
Phylogenomics resolves deep subfamilial relationships in Malvaceae sl
</td>
<td style="text-align:left;">
T Cvetković, F Areces-Berazain, DD Hinsinger
</td>
<td style="text-align:left;">
2021
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
… Our analyses confirmed the placement of /Byttneriina, the clade
comprising /Grewioideae and /Byttnerioideae, as sister to all remaining
Malvaceae sl subfamilies (/Malvadendrina; …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Flowering time and harvest on floral morphology and
production of hermaphrodite flowers in the cashew tree Anacardium
occidentale (Anacardiaceae)
</td>
<td style="text-align:left;">
J Soares-Pereira, JC DoVale
</td>
<td style="text-align:left;">
2023
</td>
<td style="text-align:right;">
8
</td>
<td style="text-align:left;">
… En general los granos de polen de la subfamilia Byttnerioideae se
caracterizan por la presencia de aberturas simples: poros, o compuestas:
colporos, y por una ornamentación …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Five new clavate-stigma Waltheria species endemic to
Brazilian cerrado (Malvaceae sl, Byttnerioideae, Hermannieae)
</td>
<td style="text-align:left;">
JG Saunders
</td>
<td style="text-align:left;">
2021
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
… into different groups based upon gross morphology. Representative
samples of each group … if groups with significantly different
morphology demonstrated allopatric species distribution …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
Phylogeny of the core Malvales: evidence from ndhF sequence data
</td>
<td style="text-align:left;">
WS Alverson, BA Whitlock, R Nyffeler
</td>
<td style="text-align:left;">
1999
</td>
<td style="text-align:right;">
549
</td>
<td style="text-align:left;">
… Two subclades of /Byttnerioideae are suggested by this study. These
“byttnerioid” and “lasiopetaloid” … Members of the /Byttnerioideae clade
show a general reduction of stamen number …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
Systematics of Dombeyoideae (Malvaceae) in the Mascarene archipelago
(Indian Ocean) inferred from morphology
</td>
<td style="text-align:left;">
TL Péchon, N Cao, JY Dubuisson, LDB Gigord
</td>
<td style="text-align:left;">
2009
</td>
<td style="text-align:right;">
20
</td>
<td style="text-align:left;">
… Indeed, the inflorescence structures and the floral morphology of
Trochetia are constant in the genus and it is distinguishable by the
very peculiar epicalyx. This diagnostic character is …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Inflorescence morphology of some Australian Lasiopetaleae
(Sterculiaceae)
</td>
<td style="text-align:left;">
C Bayer, K Kubitzki
</td>
<td style="text-align:left;">
1996
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
… Another indication in favour of regarding the Lasiopetaleae as an
advanced tribe of the Sterculiaceae-Byttnerioideae is the tendency
towards reduction of the petals. This applies also to …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
Pollen morphology of subfamily Malvoideae (Malvaceae sensu lato) in
Thailand
</td>
<td style="text-align:left;">
S SAENSOUK, P SAENSOUK
</td>
<td style="text-align:left;">
2021
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:left;">
… The pollen morphology of 19 species with eight variants and ten genera
of subfamily … morphology was provided. Eight species were studied for
their pollen morphology for the first time. …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Morphological characterization of pollen grains of
Brazilian species of Bombacoideae (Malvaceae sl.)
</td>
<td style="text-align:left;">
MD Saba, GL Esteves, VJ Santos
</td>
<td style="text-align:left;">
2020
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
… Pollen morphology of selected species of the subfamily Bombacoideae
(Malvaceae sensu lato). Acta … The present work aimed to describe the
pollen morphology of Brazilian species of …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[BOOK\]\[B\] Floral diagrams: an aid to understanding flower morphology
and evolution
</td>
<td style="text-align:left;">
LPR De Craene
</td>
<td style="text-align:left;">
2022
</td>
<td style="text-align:right;">
276
</td>
<td style="text-align:left;">
Floral morphology is key for understanding floral evolution and plant
identification. Floral diagrams are two-dimensional representations of
flowers that replace extensive descriptions or …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
Support for an expanded family concept of Malvaceae within a
recircumscribed order Malvales: a combined analysis of plastid atpB and
rbcL DNA sequences
</td>
<td style="text-align:left;">
C Bayer, MF FAY, AY DE BRUIJN
</td>
<td style="text-align:left;">
1999
</td>
<td style="text-align:right;">
446
</td>
<td style="text-align:left;">
… on molecular, morphological, and biogeographical data: (1)
Byttnerioideae, including tribes Byttnerieae, Lasiopetaleae and
Theobromeae (all of … Pollen morphology and plant taxonomy …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Phylogenetic analysis of the Malvadendrina clade
(Malvaceae sl) based on plastid DNA sequences
</td>
<td style="text-align:left;">
R Nyffeler, C Bayer, WS Alverson, A Yen
</td>
<td style="text-align:left;">
2005
</td>
<td style="text-align:right;">
113
</td>
<td style="text-align:left;">
… Thirty-one terminals, encompassing the taxonomic diversity of
Malvadendrina, and four outgroups from Byttnerioideae and Grewioideae,
were included in this study (see Appendix). …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
A new penicillate-stigma species of Waltheria (Sterculiaceae,
Hermannieae) endemic to Belize
</td>
<td style="text-align:left;">
JG Saunders, R Pozner
</td>
<td style="text-align:left;">
2007
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
… The new species possesses floral morphology consistent with thrum
flowers and thrum … morphology of Waltheria belizensis JG Saunders
compared to thrum pollen aperture morphology …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
Pollen morphology in the genus Bonamia Thouars (Convolvulaceae) and its
taxonomic significance
</td>
<td style="text-align:left;">
AL da Costa Moreira, AC Mezzonato-Pires
</td>
<td style="text-align:left;">
2019
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
… In Convolvulaceae, the morphology of pollen grains relies on a set of
important characters … classification of Convolvulaceae using pollen
grain morphology, dividing the family into major …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
Seed shape quantification in the Malvaceae reveals cardioid-shaped seeds
predominantly in herbs.
</td>
<td style="text-align:left;">
JJ Martín Gómez, D Gutiérrez del Pozo, E Cervantes
</td>
<td style="text-align:left;">
2019
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
… The application of morphology to classification requires
quantification. In recent years we have established a method for seed
shape quantification based on the comparison of seed …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
Pollen morphology and ultrastructure of representatives of the
Thyrsacanthus clade (Acanthaceae)
</td>
<td style="text-align:left;">
LMC Silva, RLB de Borges, ALA Côrtes
</td>
<td style="text-align:left;">
2017
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
… Although these pioneering studies have provided important information
for understanding the morphology of the sporoderm of Acanthaceae
species, more studies are needed in order …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Pollen morphology of selected species of the subfamily
Bombacoideae (Malvaceae sensu lato)
</td>
<td style="text-align:left;">
VHR Abreu, CBF Mendonça
</td>
<td style="text-align:left;">
2014
</td>
<td style="text-align:right;">
15
</td>
<td style="text-align:left;">
… of the Malvaceae family and the need for studies to corroborate the
circumscription of the group, which remains uncertain, this study aimed
to characterize the pollen morphology of the …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
3D pollination biology using micro‐computed tomography and geometric
morphometrics in Theobroma cacao
</td>
<td style="text-align:left;">
KA Wolcott, EL Stanley, OA Gutierrez
</td>
<td style="text-align:left;">
2023
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
… We present a modern 3D update to cacao’s floral morphology … In Table
1, we report the floral morphology measurements of … with concealed
anthers in the Byttnerioideae, such as those …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
Taxonomic significances of seed morphology in some tribes of subfamily
Malvoideae (Malvaceae) in Saudi Arabia
</td>
<td style="text-align:left;">
KA Khalik, S Al-Ruzayza, A Farid
</td>
<td style="text-align:left;">
2021
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
In this study, we evaluated the taxonomic significance and seed macro
and micromorphological characteristics of 28 species belong to eight
genera of subfamily Malvoideae (Malvaceae…
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Taxonomic Update and Habitat Status to Byttneria herbacea
from Peninsular India
</td>
<td style="text-align:left;">
SR Somkuwar
</td>
<td style="text-align:left;">
2020
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
… As a result, those genera once placed under Sterculiaceae family are
treated under subfamilies like Byttnerioideae. The members of
Byttnerioideae clade show (i) a general reduction of …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Taxonomic implications of pollen of some species of the
genus Pterospermum Schreb. (Malvaceae sl. subfam. Dombeyoideae)
</td>
<td style="text-align:left;">
MAAA Juhari, N Talip, CNAC Amri, NSM Basir
</td>
<td style="text-align:left;">
2022
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">
… Pollen morphology of selected species of the subfamily Bombacoideae
(Malvaceae sensu … Therefore, the objective of this study is to
investigate and characterize the pollen morphology …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” AND “morphology”
</td>
<td style="text-align:left;">
Low-copy nuclear genes reveal new evidence of incongruence in
relationships within Malvaceae sl
</td>
<td style="text-align:left;">
R Hernández-Gutiérrez, CG Mendoza
</td>
<td style="text-align:left;">
2021
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
… Interestingly, this arrangement would provide an explanation for the
distinct floral morphology of Byttnerioideae compared to the rest of the
subfamilies. For example, many byttnerioid …
</td>
</tr>
</tbody>
</table>

</div>

``` r
# Test automatic term extraction from abstracts for the first 5 pages of results
gs_df = test1
test1_gs_terms = litsearchr::extract_terms(text = gs_df[,"abstract"],
                                      method = "fakerake", min_freq = 3, min_n = 2,
                                      stopwords = stopwords::data_stopwords_stopwordsiso$en
                                      )
cat("Test 1: Automatically extracted terms: \n")
```

    ## Test 1: Automatically extracted terms:

``` r
print(test1_gs_terms)
```

    ## [1] "floral morphology" "pollen morphology"

## TEST 2: Try running multiple queries at the same time for different search term combinations

``` r
# Run multiple queries using lists of higher taxa and key terms for the first 5 pages of Google Scholar search results
first  = list(term = 'intext:"Byttnerioideae" OR "Sterculiaceae" AND "morphology"', pages = 1:5)
second = list(term = 'intext:"Byttnerioideae" OR "Sterculiaceae" AND "evolution"', pages = 1:5)
third = list(term = 'intext:"Byttnerioideae" OR "Sterculiaceae" AND "ecology"', pages = 1:5)
test_list = list(first, second, third) 
test2 = do.call(rbind, lapply(test_list, function(x) do.call(scrape_gs, c(crawl_delay = 1.2, x))))
cat("Test 2: Query results from Google Scholar\n")
```

    ## Test 2: Query results from Google Scholar

``` r
# Display the table as a scrollable object using kableExtra package
kbl(test2, table.attr = "style = \"color: black;\"") %>%
  kable_styling("striped", full_width = F) %>%
  scroll_box(width = "100%")
```

<div style="border: 1px solid #ddd; padding: 5px; overflow-x: scroll; width:100%; ">
<table style="color: black; width: auto !important; margin-left: auto; margin-right: auto;" class="table table-striped">
<thead>
<tr>
<th style="text-align:right;">
page
</th>
<th style="text-align:left;">
term
</th>
<th style="text-align:left;">
title
</th>
<th style="text-align:left;">
authors
</th>
<th style="text-align:left;">
year
</th>
<th style="text-align:right;">
n\_citations
</th>
<th style="text-align:left;">
abstract
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Phylogenetic relationships and floral evolution of the Byttnerioideae
(“Sterculiaceae” or Malvaceae sl) based on sequences of the chloroplast
gene, ndhF
</td>
<td style="text-align:left;">
BA Whitlock, C Bayer, DA Baum
</td>
<td style="text-align:left;">
2001
</td>
<td style="text-align:right;">
143
</td>
<td style="text-align:left;">
… While never proposed prior to the molecular work, the monophyly of
Byttnerioideae is consistent with floral morphology. The majority of
these plants share a basic floral plan of perfect, …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
POLLEN MORPHOLOGY AND HARMOMEGATHIC CHARACTERS OF Byttneria LÖFL.
SPECIES (STERCULIACEAE S. S: SUBFAM. BYTTNERIOIDEAE)
</td>
<td style="text-align:left;">
AJ Amirul-Aiman, T Noraini
</td>
<td style="text-align:left;">
2019
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
A palynological study was conducted on seven species of Byttneria Löfl.
The objectives of this study was to understand the variation in
micromorphological and harmomegarthic …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Inflorescence morphology of some Australian Lasiopetaleae
(Sterculiaceae)
</td>
<td style="text-align:left;">
C Bayer, K Kubitzki
</td>
<td style="text-align:left;">
1996
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
… Lasiopetaleae (Sterculiaceae). Telopea 6(4): 721-728. The
inflorescence morphology of 17 species out of five genera of the tribe
Lasiopetaleae (Sterculiaceae) is investigated. The …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Esclereidas foliares en Byttneria coriacea Britton (Sterculiaceae)
</td>
<td style="text-align:left;">
MM Arbo
</td>
<td style="text-align:left;">
1978
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
… Morphology and ontogeny of foliar sclereids in Olea europaea. I.
Distribution and structure. … Comparative morphology of the foliar
sclereids in the genus Mouriria. Jour. Arnold Arbor. …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Pollen morphology of Waltheria L.
(Malvaceae-Byttnerioideae) from Bahia, Brazil
</td>
<td style="text-align:left;">
CEA Silveira, LCL Lima, MD Saba
</td>
<td style="text-align:left;">
2017
</td>
<td style="text-align:right;">
15
</td>
<td style="text-align:left;">
Waltheria encompasses about 60 species distributed in Brazil and Mexico.
To improve the palynology of the genus, we analyzed 14 species occurring
in the Brazilian State of Bahia. …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Phylogenetic relationships within the subfamily Sterculioideae
(Malvaceae/Sterculiaceae-Sterculieae) using the chloroplast gene ndhF
</td>
<td style="text-align:left;">
P Wilkie, A Clark, RT Pennington, M Cheek
</td>
<td style="text-align:left;">
2006
</td>
<td style="text-align:right;">
58
</td>
<td style="text-align:left;">
… The genera of other tribes in the traditional Sterculiaceae are shared
between the subfamilies … In Firmiana, floral morphology supports the
clades produced by our molecular cladistic …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] A synopsis of Byttnerieae (Malvaceae, Byttnerioideae)
from the Atlantic Forest, with notes on geographical distribution,
nomenclature, and conservation
</td>
<td style="text-align:left;">
M Colli-Silva, JR Pirani
</td>
<td style="text-align:left;">
2021
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">
This synopsis presents diagnostic characters and comments on
morphological variability, distribution, and conservation of species of
Byttnerieae (Malvaceae, Byttnerioideae) from the …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Estimating bioregions and undercollected areas in South America by
revisiting Byttnerioideae, Helicteroideae and Sterculioideae (Malvaceae)
occurrence data
</td>
<td style="text-align:left;">
M Colli-Silva, JR Pirani
</td>
<td style="text-align:left;">
2020
</td>
<td style="text-align:right;">
11
</td>
<td style="text-align:left;">
… “Sterculiaceae” was polyphyletic, its genera were rearranged into
three distinct subfamilies: Byttnerioideae … information of species of
Byttnerioideae, Helicteroideae and Sterculioideae in …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Comparative morphology and allometry of winged diaspores among the Asian
Sterculiaceae
</td>
<td style="text-align:left;">
T Yamada, E Suzuki
</td>
<td style="text-align:left;">
1999
</td>
<td style="text-align:right;">
18
</td>
<td style="text-align:left;">
… analyse the morphology and allometry of the winged diaspores of the
Asian Sterculiaceae in … Diagrammatic representation of some of the
diaspores of Asian species of Sterculiaceae …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Pollen morphology of Sterculiaceae (s. str.) in Egypt and
its taxonomic significance
</td>
<td style="text-align:left;">
R Hamdy, E Shamso
</td>
<td style="text-align:left;">
2010
</td>
<td style="text-align:right;">
16
</td>
<td style="text-align:left;">
… morphology of 20 species representing 12 genera of cultivated and wild
Sterculiaceae in … pollen results are discussed with reference to
current systematic treatment of Sterculiaceae. …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Phylogeny of the core Malvales: evidence from ndhF sequence data
</td>
<td style="text-align:left;">
WS Alverson, BA Whitlock, R Nyffeler
</td>
<td style="text-align:left;">
1999
</td>
<td style="text-align:right;">
549
</td>
<td style="text-align:left;">
… , Hermannieae, and Lasiopetaleae of Sterculiaceae. The most striking
departures from … closely related to Helicteres and Reevesia
(Sterculiaceae) than to Bombacaceae; several …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
\[CITATION\]\[C\] The pollen morphology of Australian Sterculiaceae
</td>
<td style="text-align:left;">
WH Litchfield
</td>
<td style="text-align:left;">
1966
</td>
<td style="text-align:right;">
17
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Seed morphology and its taxonomic significance in the
family Malvaceae
</td>
<td style="text-align:left;">
R Abid, A Ather, M Qaiser
</td>
<td style="text-align:left;">
2016
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:left;">
The seed morphological studies of 75 taxa belonging to 6 sub-families of
the family Malvaceae were carried out from Pakistan. In Pakistan the
family Malvaceae is represented by 6 sub-…
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
\[CITATION\]\[C\] Pollen morphology of Tiliaceae Juss. and Sterculiaceae
Vent. and their relations to Malvaceae Juss. in Egypt
</td>
<td style="text-align:left;">
N El-Husseini
</td>
<td style="text-align:left;">
2006
</td>
<td style="text-align:right;">
15
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Seed and seedling morphology, and seed anatomy of Lasiopetaleae
(Malvaceae sl or Sterculiaceae)
</td>
<td style="text-align:left;">
CF Wilkins, JA Chappill
</td>
<td style="text-align:left;">
2002
</td>
<td style="text-align:right;">
15
</td>
<td style="text-align:left;">
… These groups are only distantly related to other tribes of
Sterculiaceae. Lasiopetaleae were characterised by Gay (1821) as being
apetalous or having much reduced scale-like petals, …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] … production and composition, and morphology of floral
nectaries in Helicteres guazumifolia and Helicteres baruensis
(Sterculiaceae): two sympatric species …
</td>
<td style="text-align:left;">
L Goldberg
</td>
<td style="text-align:left;">
2009
</td>
<td style="text-align:right;">
21
</td>
<td style="text-align:left;">
… (Sterculiaceae) are two sympatric species of shrubs common along the
North Western … i also describe the morphology of extrafloral nectaries
with scanning electron microscopy. in H. …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Novelties in Brazilian Waltheria L. (Byttnerioideae,
Malvaceae): two new species and one re-establishment
</td>
<td style="text-align:left;">
TS Coutinho, M Colli-Silva, M Alves
</td>
<td style="text-align:left;">
2020
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
… Phylogenetic relationships and floral evolution of the Byttnerioideae
(“Sterculiaceae” or Malvaceae sl) on sequences of the chloroplast gene,
ndhF. Systematic Botany 26: 420-437.; …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
The genera of Sterculiaceae in the southeastern United States
</td>
<td style="text-align:left;">
GK Brizicky
</td>
<td style="text-align:left;">
1966
</td>
<td style="text-align:right;">
29
</td>
<td style="text-align:left;">
… In contrast, “Pollen morphology is in favour of referring Erioleneae
and Dombeyeae to … rest of Sterculiaceae to Tiliaceae” (Erdtman,
p. 421). Thus emended, the Sterculiaceae would be …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
VII-The wood anatomy of the family Sterculiaceae
</td>
<td style="text-align:left;">
MM Chattaway
</td>
<td style="text-align:left;">
1937
</td>
<td style="text-align:right;">
53
</td>
<td style="text-align:left;">
… In one family in particular, the Sterculiaceae, the wood anatomy
suggests a heterogeneous … evident at an early stage in the
investigation, for the family Sterculiaceae, as established by …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Pollen morphology and exine ultrastructure of selected species of
Waltheria L.(Byttnerioideae-Malvaceae)
</td>
<td style="text-align:left;">
MD Saba, FAR dos Santos
</td>
<td style="text-align:left;">
2015
</td>
<td style="text-align:right;">
18
</td>
<td style="text-align:left;">
… In the present study, the pollen morphology of five species of
Waltheria (Waltheria albicans … (TEM), with emphasis on the differences
in pollen morphology from heterostyled morphs. …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] A synoptical account of the Sterculiaceae in Bangladesh
</td>
<td style="text-align:left;">
MO Rahman, MA Hassan
</td>
<td style="text-align:left;">
2012
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
… The present study aimed at studying all species of Sterculiaceae of
Bangladesh deposited in the herbaria both at home and abroad, and
documenting and detailing all members of the …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
A Review of the Androecium in Sterculiaceae with a Key to the Genera
</td>
<td style="text-align:left;">
KC Malick, B Safui
</td>
<td style="text-align:left;">
1980
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
… of the stalnens in the family described by various authors as being
colSterculiaceae. … as well as in number of stamens in the family
Sterculiaceae. Two (three)-whorled coilditians have …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Foliar architecture of Indian members of the family
Sterculiaceae and its systematic relevance
</td>
<td style="text-align:left;">
D Maity
</td>
<td style="text-align:left;">
2011
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
The family Sterculiaceae DC. is usually having trees and shrubs, rarely
herbs or lianas. The family consist about 1500 species under 67 genera
and this has made it much resourceful …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Phylogenomics resolves deep subfamilial relationships in Malvaceae sl
</td>
<td style="text-align:left;">
T Cvetković, F Areces-Berazain, DD Hinsinger
</td>
<td style="text-align:left;">
2021
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
… , which include species previously placed in Tiliaceae and
Sterculiaceae, respectively, form a well-supported clade that has been
named /Byttneriina. The remaining subfamilies are …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Morphology of the androecium in Malvales
</td>
<td style="text-align:left;">
W van Heel
</td>
<td style="text-align:left;">
1966
</td>
<td style="text-align:right;">
138
</td>
<td style="text-align:left;">
… part in Bombacaceae and Sterculiaceae; in Malvaceae it is … rows
(mainly in Malvaceae and Sterculiaceae). Between the two … In
Sterculiaceae and Tiliaceae the stamens of many species …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Pollen flora of Pakistan-Malvaceae: Dombeyoideae-Lxii
</td>
<td style="text-align:left;">
A Perveen, M Qaiser
</td>
<td style="text-align:left;">
2009
</td>
<td style="text-align:right;">
20
</td>
<td style="text-align:left;">
… As far as pollen morphology of the subfamily is concerned number of
workers described the … while studying the Sterculiaceae pollen. Such as
pollen morphology of the tribe Dombeyeae …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Polyphenolic cells and their interrelation with
cotyledon cells in seven species of Theobroma (Sterculiaceae)
</td>
<td style="text-align:left;">
MH Martini, A Figueira, CG Lenci
</td>
<td style="text-align:left;">
2008
</td>
<td style="text-align:right;">
32
</td>
<td style="text-align:left;">
SciELO - Brasil - Polyphenolic cells and their interrelation with
cotyledon cells in seven species of Theobroma (Sterculiaceae)
Polyphenolic cells and their interrelation with cotyledon …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Chromosome Numbers of Four Nigerian Species of Cola
Schott. & Endlicher (Sterculiaceae).
</td>
<td style="text-align:left;">
PO Adebola, JA Morakinyo
</td>
<td style="text-align:left;">
2005
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:left;">
This paper reports the results of chromosome countings in four wild Cola
species (Cola lateritia, C. ballayi, C. verticillata and C. gigantea).
Cytological investigation of these species …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Phylogenetic analysis of Theobroma (Sterculiaceae) based on Kunitz-like
trypsin inhibitor sequences
</td>
<td style="text-align:left;">
CR Sousa Silva, A Figueira
</td>
<td style="text-align:left;">
2005
</td>
<td style="text-align:right;">
54
</td>
<td style="text-align:left;">
… of plastid atpB, ndhF and rbcL sequences; morphology; and chemical
characteristics were used to propose the inclusion of Sterculiaceae into
a broadly defined Malvaceae (Alverson et …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
A revision of Melochia section Physodium (Sterculiaceae) from Mexico
</td>
<td style="text-align:left;">
LJ Dorr, LC Barnett
</td>
<td style="text-align:left;">
1989
</td>
<td style="text-align:right;">
22
</td>
<td style="text-align:left;">
… Within the Sterculiaceae section Physodium shares the characters that
define Melochia (… and that differ mainly with respect to fruit
morphology and dehiscence. If, as has been proposed (…
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Comments on the taxonomic position of some genera in Sterculiaceae.
</td>
<td style="text-align:left;">
SJ Xu, HH Hsue
</td>
<td style="text-align:left;">
2000
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:left;">
… Abstract : Sterculiaceae is a polytypic family with greatly diverse
characteristics in Malvales. … to maintain it in the Sterculiaceae. On
account of the pollen morphology and some other …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
A new penicillate-stigma species of Waltheria (Sterculiaceae,
Hermannieae) endemic to Belize
</td>
<td style="text-align:left;">
JG Saunders, R Pozner
</td>
<td style="text-align:left;">
2007
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
… The new species possesses floral morphology consistent with thrum
flowers and thrum … morphology of Waltheria belizensis JG Saunders
compared to thrum pollen aperture morphology …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Nodal And Petiolar Anatomy of 16 Species of Indian
Sterculiaceae and their Systematic Relevance
</td>
<td style="text-align:left;">
S Mitra, GG Maiti, D Maity
</td>
<td style="text-align:left;">
S Mitra, GG Maiti, D Maity - researchgate.net
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">
… a comparative study on nodal and petiolar anatomy was undertaken for
16 indian species belonging to 11 genera of the family Sterculiaceae to
assess anatomical variations which may …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Pollen morphology of Craigia with reference to its systematic position
</td>
<td style="text-align:left;">
H Li‐King, H Hsiang‐Hao
</td>
<td style="text-align:left;">
1985
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:left;">
… 究竟应将它归入梧桐科(Sterculiaceae)还是橡树科
(Tiaceae).长期以来都存在着不同的见解.… morphology of the genus Craigia
its systematic position,whether in the family Sterculiaceae orin …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Research on the Sterculiaceae.
</td>
<td style="text-align:left;">
C Gazet
</td>
<td style="text-align:left;">
1940
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">
… Abstract : A comprehensive account of the morphology of the
Sterculiaceae, based on the examination of 170 species of 35 genera. The
species Sterculia platanifolia was chosen as the …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
An unusual new species of Helmiopsis H. Perrier (Sterculiaceae) from
Madagascar
</td>
<td style="text-align:left;">
LC Barnett
</td>
<td style="text-align:left;">
1987
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:left;">
… For example, its spheroid, rather than conical, capsule morphology is
unique and provides the basis for the name Helmiopsis sphaerocarpa L.
Barnett. The seed wings of H. …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
El polen de Helicteres (Sterculiaceae) y su comparación con géneros
vecinos
</td>
<td style="text-align:left;">
SM Piré, CL Cristóbal
</td>
<td style="text-align:left;">
2001
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:left;">
… According to pollen morphology Neoregnellia is closely connected with
Helicteres; this genus shares the same pollen type, IX, with sect.
Sacarolha and some species of sect. …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Taxonomic Revision of Hannafordia (Lasiopetaleae: Sterculiaceae
(Malvaceae sl))
</td>
<td style="text-align:left;">
CF Wilkins, JA Chappill
</td>
<td style="text-align:left;">
2001
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
… Taxonomic descriptions, a key, distribution maps, illustrations,
anatomical findings and seed and seedling morphology are presented. A
chromosome number of n = 10 is the first …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Floral anatomy of some Sterculiaceae with special reference to the
position of stamens.
</td>
<td style="text-align:left;">
CV Rao
</td>
<td style="text-align:left;">
1950
</td>
<td style="text-align:right;">
16
</td>
<td style="text-align:left;">
Floral anatomy of some Sterculiaceae with special reference to the
position of stamens. Cookies on CAB Direct Like most websites we use
cookies. This is to ensure that we give you …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
\[CITATION\]\[C\] Studies of Indian pollen grains in relation to plant
taxonomy: Sterculiaceae
</td>
<td style="text-align:left;">
BD Sharma
</td>
<td style="text-align:left;">
1967
</td>
<td style="text-align:right;">
24
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Helicteres ovata (Sterculiaceae), Pollinated by Bats in Southeastern
Brazil
</td>
<td style="text-align:left;">
M Sazima
</td>
<td style="text-align:left;">
1988
</td>
<td style="text-align:right;">
36
</td>
<td style="text-align:left;">
… The relatively poor correspondence between the floral morphology ofH.
ovata and the visiting behaviour of bats as its pollen … On the basis of
similar floral morphology, we suggest that H. …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Pollen morphology of the family Bombacaceae
</td>
<td style="text-align:left;">
HP Fuchs
</td>
<td style="text-align:left;">
1967
</td>
<td style="text-align:right;">
43
</td>
<td style="text-align:left;">
… The delimitation of the family Bombacaceae based on macromorphologieal
characters, and its separation from the closely related families of
Sterculiaceae, Malvaceae, and Tiliaceae …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Dombeya kefaensis, sp. nov.(Sterculiaceae) from SW. Ethiopia
</td>
<td style="text-align:left;">
I Friis, S Bidgood
</td>
<td style="text-align:left;">
1998
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
… aethiopica in indumentum and flower colour, but differs in
leaf‐morphology and habit; it resembles D. buettneri in habit, but
differs in the leaf apex being broadly acute to rounded, the …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Taxonomic revision of Guichenotia (Lasiopetaleae: Malvaceae sl or
Sterculiaceae)
</td>
<td style="text-align:left;">
CF Wilkins, JA Chappill
</td>
<td style="text-align:left;">
2003
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
… These two clades are unresolved relative to Lasiopetalum by cladistic
analysis of their morphology. Guichenotia ss is maintained as a genus,
the species of which are revised here, …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Floral morphology and embryo sac development in Burretiodendron
kydiifolium YC Hsu et R. Zhuge (Tiliaceae) and their systematic
significance
</td>
<td style="text-align:left;">
YA Tang
</td>
<td style="text-align:left;">
1998
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
… genus Ptmspennum of Sterculiaceae although they differ considerably in
gross morphology. … Within Sterculiaceae Pterospennum is isolated in
terms of combined characteristics of gross …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Phylogenetic relationships of Theobroma and Herrania (Sterculiaceae)
based on sequences of the nuclear gene Vicilin
</td>
<td style="text-align:left;">
BA Whitlock, DA Baum
</td>
<td style="text-align:left;">
1999
</td>
<td style="text-align:right;">
64
</td>
<td style="text-align:left;">
… The tribe Byttnerieae is traditionally defined by an unusual floral
morphology with five concave or cup-shaped petals, antipetalous stamens,
and antisepalous staminodes. In Theobroma …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Male and female booths with separate entrances in the tiny flowers of
Guazuma ulmifolia (Malvaceae–Byttnerioideae). I. Structural integration
</td>
<td style="text-align:left;">
C Westerkamp, AA Soares
</td>
<td style="text-align:left;">
2006
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:left;">
In spite of their tiny dimensions (3×5mm 2 ), the strongly honey-scented
flowers of Guazuma ulmifolia (Malvaceae–Byttnerioideae) provide six
sexual chambers. A central female unit is …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Genetic relationships among Fremontodendron (Sterculiaceae) populations
of the Central Sierra Nevada foothills of California
</td>
<td style="text-align:left;">
W Kelman, L Broadhurst, C Brubaker, A Franklin
</td>
<td style="text-align:left;">
2006
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:left;">
… This distinction, coupled with its unique morphology and ecology,
support the treatment of F. decumbens as a species and promote its
continued conservation as a rare and unique …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Insect visitors to extrafloral nectaries of Byttneria aculeata
(Sterculiaceae): relative importance and roles
</td>
<td style="text-align:left;">
HA Hespenheide
</td>
<td style="text-align:left;">
1985
</td>
<td style="text-align:right;">
71
</td>
<td style="text-align:left;">
… Ants, parasitoids and flies are about equally frequent at foliar
nectaries of Bytmeriu uculeutu (Sterculiaceae) in lowland Costa Rica
during the dry season, a pattern previously …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “morphology”
</td>
<td style="text-align:left;">
Leaf anatomy ofDombeya andNesogordonia (Sterculiaceae), emphasizing
epidermal and internal idioblasts
</td>
<td style="text-align:left;">
NR Lersten, JD Curtis
</td>
<td style="text-align:left;">
1997
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
… of Dombeya and 11 species of Nesogordonia (Sterculiaceae). Species
were placed in seven idioblast … In these two genera the range of foliar
idioblast morphology surpasses that known …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Phylogenetic relationships and floral evolution of the Byttnerioideae
(“Sterculiaceae” or Malvaceae sl) based on sequences of the chloroplast
gene, ndhF
</td>
<td style="text-align:left;">
BA Whitlock, C Bayer, DA Baum
</td>
<td style="text-align:left;">
2001
</td>
<td style="text-align:right;">
143
</td>
<td style="text-align:left;">
… Byttnerioideae. Here we analyze sequences of the chloroplast gene ndhF
for 37 species of Byttnerioideae. … Kleinhovia from a fifth tribe of
Sterculiaceae (Helictereae). The Hermannieae …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Biogeography and conservation of Byttnerioideae,
Helicteroideae and Sterculioideae (Malvaceae) in South America, with a
taxonomic synopsis in the Atlantic …
</td>
<td style="text-align:left;">
MC Silva
</td>
<td style="text-align:left;">
MC Silva - researchgate.net
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">
… to understand the origin and evolution of lineages, especially … of
Malvaceae— Byttnerioideae, Helicteroideae and … groups on biogeography,
evolution and conservation of the family …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
\[BOOK\]\[B\] Systematics and Evolution of Waltheria (Sterculiaceae:
Hermannieae)
</td>
<td style="text-align:left;">
JG Saunders
</td>
<td style="text-align:left;">
1995
</td>
<td style="text-align:right;">
24
</td>
<td style="text-align:left;">
This dissertation provides a monograph of the primarily distylous genus
Waltheria (Sterculiaceae: Hermannieae). Waltheria is readily
distinguished from Melochia and Dicarpidium by …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Chattawaya (Sterculiaceae): a new genus of wood from the Eocene of
Oregon and its implications for xylem evolution of the extant genus
Pterospermum
</td>
<td style="text-align:left;">
SR Manchester
</td>
<td style="text-align:left;">
1980
</td>
<td style="text-align:right;">
37
</td>
<td style="text-align:left;">
… possible to ascribe Paleogene woods of the Sterculiaceae to extant
genera. The case studies … genera ofthe Sterculiaceae has undergone
substantial evolution since the Middle Eocene. …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Evolution of leaf architecture in the chaparral species Fremontodendron
californicum ssp. Californicum (Sterculiaceae)
</td>
<td style="text-align:left;">
DR Bissing
</td>
<td style="text-align:left;">
1982
</td>
<td style="text-align:right;">
28
</td>
<td style="text-align:left;">
The chaparral species Fremontodendron californicum ssp. californicum and
the cloud forest species Cheirostemon platanoides arose from a common
mesophytic stock. The leaf of C. …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Biogeography abd conservation of Byttnerioideae, Helicteroideae and
Sterculioideae (Malvaceae) in South America, with a taxonomical synopsis
in the Atlantic Forest
</td>
<td style="text-align:left;">
MC Silva
</td>
<td style="text-align:left;">
2020
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">
… to understand the origin and evolution of lineages, especially … of
Malvaceae–Byttnerioideae, Helicteroideae and … groups on biogeography,
evolution and conservation of the family …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Phylogenomics resolves deep subfamilial relationships in Malvaceae sl
</td>
<td style="text-align:left;">
T Cvetković, F Areces-Berazain, DD Hinsinger
</td>
<td style="text-align:left;">
2021
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
… , which include species previously placed in Tiliaceae and
Sterculiaceae, respectively, form … and establish a preliminary temporal
framework for the evolution of the family. We used a …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Triplochitioxylon (Sterculiaceae): A new genus of wood from the Eocene
of Oregon and its bearing on xylem evolution in the extant genus
Triplochiton
</td>
<td style="text-align:left;">
SR Manchester
</td>
<td style="text-align:left;">
1979
</td>
<td style="text-align:right;">
37
</td>
<td style="text-align:left;">
A new sterculiaceous wood, Triplochitioxylon oregonensis gen. et sp. n.,
was collected from a Middle Eocene locality in the Clarno Formation of
Oregon. Anatomical data indicate a …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Phylogenetic relationships within the subfamily Sterculioideae
(Malvaceae/Sterculiaceae-Sterculieae) using the chloroplast gene ndhF
</td>
<td style="text-align:left;">
P Wilkie, A Clark, RT Pennington, M Cheek
</td>
<td style="text-align:left;">
2006
</td>
<td style="text-align:right;">
58
</td>
<td style="text-align:left;">
… Phylogenetic relationships and floral evolution of the Byttnerioideae
(‘‘Sterculiaceae’’ or Malvaceae sl) based on sequences of the
chloroplast gene ndhF. Systematic Botany 26: 420–…
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Estimating bioregions and undercollected areas in South America by
revisiting Byttnerioideae, Helicteroideae and Sterculioideae (Malvaceae)
occurrence data
</td>
<td style="text-align:left;">
M Colli-Silva, JR Pirani
</td>
<td style="text-align:left;">
2020
</td>
<td style="text-align:right;">
11
</td>
<td style="text-align:left;">
… The Amazon region—the most undercollected area of South America for
the groups—also shows some remarkable records, namely from arborescent
genera of Byttnerioideae (…
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Phylogenetic analysis of Theobroma (Sterculiaceae) based on Kunitz-like
trypsin inhibitor sequences
</td>
<td style="text-align:left;">
CR Sousa Silva, A Figueira
</td>
<td style="text-align:left;">
2005
</td>
<td style="text-align:right;">
54
</td>
<td style="text-align:left;">
Trypsin inhibitor gene sequences were used to investigate the
phylogenetic relationships among Theobroma and Herrania species,
considered as sister-groups, with particular interest …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Comparative morphology and allometry of winged diaspores among the Asian
Sterculiaceae
</td>
<td style="text-align:left;">
T Yamada, E Suzuki
</td>
<td style="text-align:left;">
1999
</td>
<td style="text-align:right;">
18
</td>
<td style="text-align:left;">
… diaspores of 25 species in the Asian Sterculiaceae were studied. The
diaspores of pioneer … by Suzuki & Ashton (1996): that the evolution of
small wings appears to have arisen mainly …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Phylogeny of the core Malvales: evidence from ndhF sequence data
</td>
<td style="text-align:left;">
WS Alverson, BA Whitlock, R Nyffeler
</td>
<td style="text-align:left;">
1999
</td>
<td style="text-align:right;">
549
</td>
<td style="text-align:left;">
… , and Lasiopetaleae of Sterculiaceae. The most striking … related to
Helicteres and Reevesia (Sterculiaceae) than to … of the group’s
morphological evolution and biogeography. In …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Notes on the plants of Bakossi, Cameroon, and the new Cola etugei and
Cola kodminensis (Sterculiaceae s. str.)
</td>
<td style="text-align:left;">
M Cheek, B Tchiengue, I Baldwin
</td>
<td style="text-align:left;">
2020
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:left;">
Background and aims – This paper reports a further discovery during
preparation for a monograph of the genus Cola, and also in the context
of a long-term botanical survey in the Cross …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Distyly, self-incompatibility, and evolution in Melochia
</td>
<td style="text-align:left;">
FW Martin
</td>
<td style="text-align:left;">
1967
</td>
<td style="text-align:right;">
58
</td>
<td style="text-align:left;">
… the subfamily Hermannieae, Sterculiaceae, includes about 60 …
described in the family Sterculiaceae. Knight and Rogers (… is needed in
the Sterculiaceae to elucidate the evolutionary …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Systematics, Evolution, and Biogeography of Ayenia (Malvaceae Subfamily
Byttnerioideae)
</td>
<td style="text-align:left;">
WV Sharber
</td>
<td style="text-align:left;">
2018
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
… number in Byttnerioideae (Whitlock et al., 2001). Here, I explore the
evolution of trithecate … anthers arose once within Byttnerioideae or
are the result of parallel evolution. Because of the …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Waltheria marielleae (Byttnerioideae, Malvaceae), a new species from
north-eastern Brazil supported by morphological and phylogenetic
evidence
</td>
<td style="text-align:left;">
TS Coutinho, MA Sader, A Pedrosa-Harand
</td>
<td style="text-align:left;">
2022
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
Background – Waltheria marielleae is a new species of Malvaceae endemic
to north-eastern Brazil that occurs only in the states of Pernambuco and
Alagoas, in areas of Atlantic Forest …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
An unnatural breeding system polymorphism in cacao (Theobroma cacao,
Sterculiaceae) in Trinidad
</td>
<td style="text-align:left;">
JM Warren, Kalai, S Misir
</td>
<td style="text-align:left;">
1995
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
… The evolution ofselfing vs. outbreeding has been of major interest to
plant population biology. … These populations now provide an ideal
system with which to study the evolution of selfing …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Leaf anatomy ofDombeya andNesogordonia (Sterculiaceae), emphasizing
epidermal and internal idioblasts
</td>
<td style="text-align:left;">
NR Lersten, JD Curtis
</td>
<td style="text-align:left;">
1997
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
We studied leaf anatomy, using clearings, resin sections, and scanning
electron microscopy, from mostly herbarium specimens of 123 species
ofDombeya and 11 species …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Cross-amplification and characterization of microsatellite loci for
three species of Theobroma (Sterculiaceae) from the Brazilian Amazon
</td>
<td style="text-align:left;">
MR Lemes, TM Martiniano, VM Reis, CP Faria
</td>
<td style="text-align:left;">
2007
</td>
<td style="text-align:right;">
16
</td>
<td style="text-align:left;">
This study reports on the cross-species amplification of 23
microsatellite markers previously developed for Theobroma cacao L.
(Sterculiaceae), source of chocolate in three …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
New chromosome numbers for Lasiopetaleae: Malvaceae sl (or
Sterculiaceae)
</td>
<td style="text-align:left;">
CF Wilkins, JA Chappill
</td>
<td style="text-align:left;">
2002
</td>
<td style="text-align:right;">
11
</td>
<td style="text-align:left;">
… The basal chromosome number for the Byttnerioideae, or for the two
subfamilies combined, … evolution of chromosome numbers within all
groups of Grewioideae and Byttnerioideae, …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Phylogenetic relationships of Theobroma and Herrania (Sterculiaceae)
based on sequences of the nuclear gene Vicilin
</td>
<td style="text-align:left;">
BA Whitlock, DA Baum
</td>
<td style="text-align:left;">
1999
</td>
<td style="text-align:right;">
64
</td>
<td style="text-align:left;">
… Furthermore, the relationships within Theobroma are relevant to
understanding the evolution of several economically important
characters, such as lipid biochemistry, alkaloid …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Initial growth of Pterygota macrocarpa Schumann (Sterculiaceae)
depending on the light intensity in Côte d’Ivoire
</td>
<td style="text-align:left;">
K Kouadio, K N’Guessan, KH Kouassi
</td>
<td style="text-align:left;">
2013
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
… the initial growth of Pterygota macrocarpa (Koto, Sterculiaceae), a
local trees species and … This study will determine the dynamics of
growth in height and diameter and the evolution of …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Systematics and evolution of chocolate and its relatives (Sterculiaceae
or Malvaceae sl)
</td>
<td style="text-align:left;">
BA Whitlock
</td>
<td style="text-align:left;">
2001
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">
… This study finds that the Sterculiaceae, Tiliaceae and Bombacaceae are
not monophyletic … Sterculiaceae, the Byttnerioideae. Chapter two
presents a phylogeny of the Byttnerioideae …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
The genus Melochia L.(Sterculiaceae)
</td>
<td style="text-align:left;">
A Goldberg
</td>
<td style="text-align:left;">
1967
</td>
<td style="text-align:right;">
76
</td>
<td style="text-align:left;">
… Evolution and Relationships Melochia belongs to a relatively primitive
family, the Sterculiaceae, that probably originated at a time when the
temperature did not go below freezing, at …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
A new penicillate-stigma species of Waltheria (Sterculiaceae,
Hermannieae) endemic to Belize
</td>
<td style="text-align:left;">
JG Saunders, R Pozner
</td>
<td style="text-align:left;">
2007
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
… For the new species, we follow Köhler’s (1976: 153) interpretations
for the same trends of aperture evolution in the genus in concluding
that the thrum-like pollen of W. belizensis is more …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Pollen morphology of Waltheria L.
(Malvaceae-Byttnerioideae) from Bahia, Brazil
</td>
<td style="text-align:left;">
CEA Silveira, LCL Lima, MD Saba
</td>
<td style="text-align:left;">
2017
</td>
<td style="text-align:right;">
15
</td>
<td style="text-align:left;">
Waltheria encompasses about 60 species distributed in Brazil and Mexico.
To improve the palynology of the genus, we analyzed 14 species occurring
in the Brazilian State of Bahia. …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Novelties in Brazilian Waltheria L. (Byttnerioideae,
Malvaceae): two new species and one re-establishment
</td>
<td style="text-align:left;">
TS Coutinho, M Colli-Silva, M Alves
</td>
<td style="text-align:left;">
2020
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
… Phylogenetic relationships and floral evolution of the Byttnerioideae
(“Sterculiaceae” or Malvaceae sl) on sequences of the chloroplast gene,
ndhF. Systematic Botany 26: 420-437.; …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Novitates Gabonenses 83. Two new species of Cola (Sterculiaceae s. str.)
from Gabon with an introductory note on the subdivision of the genus
</td>
<td style="text-align:left;">
FJ Breteler
</td>
<td style="text-align:left;">
2014
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
Background and aims – The existing subdivisions of the large genus Cola
of the Sterculiaceae s. str. are briefly presented and their usefulness
as regards the identification of newly …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
</td>
<td style="text-align:left;">
Two new species of Sterculiaceae from China
</td>
<td style="text-align:left;">
H Hsiang-Hao, H Hsiang‐Hao
</td>
<td style="text-align:left;">
1975
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">

1.  桂滇桐 新种 图版 11 (\*﹑-, 乔木. 高 12 米. 树皮褐色;
    小枝干时紫黑色. 被稀疏的淡黄褐色星状 短柔毛. 叶厚纸质.
    长椭圆状披针形或长椭圆形. 长 7 一 9 厘米. 宽 2.5 一 4 厘米.
    先端长渐尖. …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    4
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
    </td>
    <td style="text-align:left;">
    \[HTML\]\[HTML\] Phylogenetic analysis of the Malvadendrina clade
    (Malvaceae sl) based on plastid DNA sequences
    </td>
    <td style="text-align:left;">
    R Nyffeler, C Bayer, WS Alverson, A Yen
    </td>
    <td style="text-align:left;">
    2005
    </td>
    <td style="text-align:right;">
    113
    </td>
    <td style="text-align:left;">
    … Thirty-one terminals, encompassing the taxonomic diversity of
    Malvadendrina, and four outgroups from Byttnerioideae and
    Grewioideae, were included in this study (see Appendix). …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    4
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
    </td>
    <td style="text-align:left;">
    Insect visitors to extrafloral nectaries of Byttneria aculeata
    (Sterculiaceae): relative importance and roles
    </td>
    <td style="text-align:left;">
    HA Hespenheide
    </td>
    <td style="text-align:left;">
    1985
    </td>
    <td style="text-align:right;">
    71
    </td>
    <td style="text-align:left;">
    … Ants, parasitoids and flies are about equally frequent at foliar
    nectaries of Bytmeriu uculeutu (Sterculiaceae) in lowland Costa Rica
    during the dry season, a pattern previously …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    4
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
    </td>
    <td style="text-align:left;">
    Cryptic dioecy and leaky dioecy in endemic species of Dombeya
    (Sterculiaceae) on La Re´union
    </td>
    <td style="text-align:left;">
    L Humeau, T Pailler
    </td>
    <td style="text-align:left;">
    1999
    </td>
    <td style="text-align:right;">
    75
    </td>
    <td style="text-align:left;">
    … We have thus begun a study of the abundance, ecology, and
    evolution of dioecious groups of … The genus Dombeya (Sterculiaceae)
    in the Mascarene islands provides a particularly …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    4
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
    </td>
    <td style="text-align:left;">
    \[PDF\]\[PDF\] HELICTERES BARUENSIS (STERCULIACEAE)
    </td>
    <td style="text-align:left;">
    O von Helversen, CC Voigt
    </td>
    <td style="text-align:left;">
    O von Helversen, CC Voigt - soctropecol.eu
    </td>
    <td style="text-align:right;">
    NA
    </td>
    <td style="text-align:left;">
    Helicteres baruensis Jacq.(Sterculiaceae) is a common shrub of the
    tropical dry forest in the Pacific lowlands of Costa Rica. We
    studied the floral characteristics of Helicteres baruensis …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    4
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
    </td>
    <td style="text-align:left;">
    \[HTML\]\[HTML\] A synopsis of Byttnerieae (Malvaceae,
    Byttnerioideae) from the Atlantic Forest, with notes on geographical
    distribution, nomenclature, and conservation
    </td>
    <td style="text-align:left;">
    M Colli-Silva, JR Pirani
    </td>
    <td style="text-align:left;">
    2021
    </td>
    <td style="text-align:right;">
    NA
    </td>
    <td style="text-align:left;">
    … Phylogenetic relationships and floral evolution of the
    Byttnerioideae (‘Sterculiaceae” or Malvaceae sl) based on sequences
    of chloroplast gene ndhF. Systematic Botany 26: 420-437.; …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    4
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
    </td>
    <td style="text-align:left;">
    Microsatellite loci for two East African tree species, Leptonychia
    usambarensis (Sterculiaceae) and Sorindeia madagascariensis
    (Anacardiaceae)
    </td>
    <td style="text-align:left;">
    NJ Cordeiro, KA Feldheim, E Ikejimba
    </td>
    <td style="text-align:left;">
    2008
    </td>
    <td style="text-align:right;">
    NA
    </td>
    <td style="text-align:left;">
    We isolated 20 trinucleotide microsatellites from two African tree
    species: Sorindeia madagascariensis (nine microsatellites) and
    Leptonychia usambarensis (11 microsatellites). …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    4
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
    </td>
    <td style="text-align:left;">
    Seringia (Byttneriaceae/Malvaceae-Byttnerioideae) new to Southeast
    Asia and S. botak endangered in Indonesian New Guinea grassland and
    savannah
    </td>
    <td style="text-align:left;">
    M Cheek, J Wanma, M Jitmau
    </td>
    <td style="text-align:left;">
    2018
    </td>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    Seringia botak Cheek sp. nov., based on a collection made by the
    authors in 2017, is described from metalliferous grassland and
    savannah habitats in Indonesian New Guinea. Initially …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    4
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
    </td>
    <td style="text-align:left;">
    The evolution of staminodes in angiosperms: patterns of stamen
    reduction, loss, and functional re‐invention
    </td>
    <td style="text-align:left;">
    J Walker‐Larsen, LD Harder
    </td>
    <td style="text-align:left;">
    2000
    </td>
    <td style="text-align:right;">
    164
    </td>
    <td style="text-align:left;">
    … 10), Herrania, and Dombeya of the Sterculiaceae prevent selfing by
    mediating insect movement within the flower (Posnette, 1950;
    Cuatrecasas, 1964; Sampayan, 1966; and Entwistle, …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    4
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
    </td>
    <td style="text-align:left;">
    \[CITATION\]\[C\] Antioxidant, hypolipidemic and cardio-protective
    properties of chloroform extract of Mansonia altissima
    (Sterculiaceae) in rabbits
    </td>
    <td style="text-align:left;">
    FM Adéoti, CC Massara, YE Thé, BN Djyh, IK Kolia
    </td>
    <td style="text-align:left;">
    2016
    </td>
    <td style="text-align:right;">
    NA
    </td>
    <td style="text-align:left;">
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    4
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
    </td>
    <td style="text-align:left;">
    Molecular phylogeny of Heritiera Aiton (Sterculiaceae), a tree
    mangrove: variations in RAPD markers and nuclear DNA content
    </td>
    <td style="text-align:left;">
    AB Das, AK Mukherjee, P Das
    </td>
    <td style="text-align:left;">
    2001
    </td>
    <td style="text-align:right;">
    34
    </td>
    <td style="text-align:left;">
    … to land habit, suggests adaptability of the species through
    structural alteration of chromosomes in combination with their
    changes in the DNA level during the course of evolution. …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
    </td>
    <td style="text-align:left;">
    The bicolor unit — homology and transformation of an inflorescence
    structure unique to coreMalvales
    </td>
    <td style="text-align:left;">
    C Bayer
    </td>
    <td style="text-align:left;">
    1999
    </td>
    <td style="text-align:right;">
    39
    </td>
    <td style="text-align:left;">
    … in other taxa (eg Sterculiaceae: Hermannieae, Ayenia L.). The
    constantly two-flowered units of some Helictereae and Hermannieae
    (Sterculiaceae) are more readily understood in the …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
    </td>
    <td style="text-align:left;">
    The rotated-lamina syndrome. VII. Direct formation of lamina in the
    rotated position in Pterospermum (Sterculiaceae) and the appearance
    of hyper-rotation
    </td>
    <td style="text-align:left;">
    WA Charlton
    </td>
    <td style="text-align:left;">
    1997
    </td>
    <td style="text-align:right;">
    3
    </td>
    <td style="text-align:left;">
    … This unusual mode of lamina development appears to be a case of
    saltational evolution. In P. … The range from partial to
    near-complete rotation in Tiliaceae and Sterculiaceae. Can. J. …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
    </td>
    <td style="text-align:left;">
    Pollination biology of Theobroma and Herrania (Sterculiaceae)—I.
    Floral biology
    </td>
    <td style="text-align:left;">
    AM Young, EH Erickson, MA Strand
    </td>
    <td style="text-align:left;">
    1987
    </td>
    <td style="text-align:right;">
    44
    </td>
    <td style="text-align:left;">
    Floral characteristics related to pollination were studied in four
    species of Theobroma and one species of Herrania (Sterculiaceae) in
    Costa Rica. Anthesis was complete in T. cacao, T. …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
    </td>
    <td style="text-align:left;">
    Revision of the so-calledReevesia’fossil woods from the Tertiary in
    Japan—a proposal of the new genus Wataria (Sterculiaceae)
    </td>
    <td style="text-align:left;">
    K Terada, M Suzuki
    </td>
    <td style="text-align:left;">
    1998
    </td>
    <td style="text-align:right;">
    29
    </td>
    <td style="text-align:left;">
    … regarded as belonging to an extinct genus of Sterculiaceae, most
    similar to the extant genus … We conclude that Wataria is an extinct
    genus of the Sterculiaceae but with likely affinity with …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
    </td>
    <td style="text-align:left;">
    A revision of Fremontodendron (Sterculiaceae)
    </td>
    <td style="text-align:left;">
    WM Kelman
    </td>
    <td style="text-align:left;">
    1991
    </td>
    <td style="text-align:right;">
    19
    </td>
    <td style="text-align:left;">
    … and Bombacaceae and concluded that Fremontodendron and
    Chiranthodendron show great affinity to the Bombacaceae and not to
    the Sterculiaceae. However, the basis for this con…
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
    </td>
    <td style="text-align:left;">
    \[CITATION\]\[C\] APOCARPY IN STERCULIACEAE-STRUCTURE, DEVELOPMENT,
    FUNCTION AND EVOLUTION
    </td>
    <td style="text-align:left;">
    M Jenny
    </td>
    <td style="text-align:left;">
    1983
    </td>
    <td style="text-align:right;">
    2
    </td>
    <td style="text-align:left;">
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
    </td>
    <td style="text-align:left;">
    \[HTML\]\[HTML\] … production and composition, and morphology of
    floral nectaries in Helicteres guazumifolia and Helicteres baruensis
    (Sterculiaceae): two sympatric species …
    </td>
    <td style="text-align:left;">
    L Goldberg
    </td>
    <td style="text-align:left;">
    2009
    </td>
    <td style="text-align:right;">
    21
    </td>
    <td style="text-align:left;">
    … (Sterculiaceae) are two sympatric species of shrubs common along
    the North Western tropical dry forest of Costa Rica. i recorded
    their nectar production within a 24 hour cycle. i also …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
    </td>
    <td style="text-align:left;">
    Taxonomy, anatomy and evolution of physical dormancy in seeds
    </td>
    <td style="text-align:left;">
    JM Baskin, CC Baskin, X Li
    </td>
    <td style="text-align:left;">
    2000
    </td>
    <td style="text-align:right;">
    760
    </td>
    <td style="text-align:left;">
    … We propose that the selective force for evolution of PY was
    climatic drying, resulting in seasonally dry forest habitats within
    the tropics/subtropics. In general, recalcitrance is not …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
    </td>
    <td style="text-align:left;">
    A revision of Melochia section Physodium (Sterculiaceae) from Mexico
    </td>
    <td style="text-align:left;">
    LJ Dorr, LC Barnett
    </td>
    <td style="text-align:left;">
    1989
    </td>
    <td style="text-align:right;">
    22
    </td>
    <td style="text-align:left;">
    … and Waltheria (insufficient material of Dicarpidium and Gilesia
    was available for examination) suggests that this incompatibility
    system was an early development in the evolution of the …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “evolution”
    </td>
    <td style="text-align:left;">
    Medicinal uses, phytochemistry and pharmacology of family
    Sterculiaceae: A review
    </td>
    <td style="text-align:left;">
    LMR Al Muqarrabun, N Ahmat
    </td>
    <td style="text-align:left;">
    2015
    </td>
    <td style="text-align:right;">
    47
    </td>
    <td style="text-align:left;">
    … Several species from family Sterculiaceae have been applied … of
    species from family Sterculiaceae. The phytochemical … and floral
    evolution of the Byttnerioideae (“Sterculiaceae” or …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    \[PDF\]\[PDF\] Biogeography and conservation of Byttnerioideae,
    Helicteroideae and Sterculioideae (Malvaceae) in South America, with
    a taxonomic synopsis in the Atlantic …
    </td>
    <td style="text-align:left;">
    MC Silva
    </td>
    <td style="text-align:left;">
    MC Silva - researchgate.net
    </td>
    <td style="text-align:right;">
    NA
    </td>
    <td style="text-align:left;">
    … Neste projeto, utilizamos como modelo três subfamílias de
    Malvaceae — Byttnerioideae, Helicteroideae e Sterculioideae —
    totalizando 271 espécies em 11 gêneros na América do Sul…
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Phylogenetic relationships and floral evolution of the
    Byttnerioideae (“Sterculiaceae” or Malvaceae sl) based on sequences
    of the chloroplast gene, ndhF
    </td>
    <td style="text-align:left;">
    BA Whitlock, C Bayer, DA Baum
    </td>
    <td style="text-align:left;">
    2001
    </td>
    <td style="text-align:right;">
    143
    </td>
    <td style="text-align:left;">
    … Byttnerioideae. Here we analyze sequences of the chloroplast gene
    ndhF for 37 species of Byttnerioideae. … Kleinhovia from a fifth
    tribe of Sterculiaceae (Helictereae). The Hermannieae …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Biogeography abd conservation of Byttnerioideae, Helicteroideae and
    Sterculioideae (Malvaceae) in South America, with a taxonomical
    synopsis in the Atlantic Forest
    </td>
    <td style="text-align:left;">
    MC Silva
    </td>
    <td style="text-align:left;">
    2020
    </td>
    <td style="text-align:right;">
    NA
    </td>
    <td style="text-align:left;">
    … In this thesis, we used as model three selected subfamilies of
    Malvaceae–Byttnerioideae, Helicteroideae and Sterculioideae–summing
    271 species in 11 genera for South America, to …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Insect visitors to extrafloral nectaries of Byttneria aculeata
    (Sterculiaceae): relative importance and roles
    </td>
    <td style="text-align:left;">
    HA Hespenheide
    </td>
    <td style="text-align:left;">
    1985
    </td>
    <td style="text-align:right;">
    71
    </td>
    <td style="text-align:left;">
    … Ants, parasitoids and flies are about equally frequent at foliar
    nectaries of Bytmeriu uculeutu (Sterculiaceae) in lowland Costa Rica
    during the dry season, a pattern previously …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Pollination ecology of Helicteres isora Linn.(Sterculiaceae)
    </td>
    <td style="text-align:left;">
    JB Atluri, SP Rao, CS Reddi
    </td>
    <td style="text-align:left;">
    2000
    </td>
    <td style="text-align:right;">
    35
    </td>
    <td style="text-align:left;">
    … Despite such use fulness of the plant, little information is
    available on the pollination ecology … We report here the details of
    pollination ecology together with the flower colour changes …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Local and geographical distributions for a tropical tree genus,
    Scaphium (Sterculiaceae) in the Far East
    </td>
    <td style="text-align:left;">
    T Yamada, A Itoh, M Kanzaki, T Yamakura, E Suzuki
    </td>
    <td style="text-align:left;">
    2000
    </td>
    <td style="text-align:right;">
    42
    </td>
    <td style="text-align:left;">
    Tropical rain forests have an amazingly large number of closely
    related, sympatric species. How the sympatric species coexist is
    central to understanding the maintenance of high …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    \[HTML\]\[HTML\] A synopsis of Byttnerieae (Malvaceae,
    Byttnerioideae) from the Atlantic Forest, with notes on geographical
    distribution, nomenclature, and conservation
    </td>
    <td style="text-align:left;">
    M Colli-Silva, JR Pirani
    </td>
    <td style="text-align:left;">
    2021
    </td>
    <td style="text-align:right;">
    NA
    </td>
    <td style="text-align:left;">
    … one of the nine subfamilies of Malvaceae - Byttnerioideae -, by
    presenting a synoptic taxonomical … Phylogenetic relationships and
    floral evolution of the Byttnerioideae (‘Sterculiaceae” or …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Intra‐and interhabitat variations in Guazuma ulmifolia
    (Sterculiaceae) seed predation by Amblycerus cistelinus (Bruchidae)
    in Costa Rica
    </td>
    <td style="text-align:left;">
    DH Janzen
    </td>
    <td style="text-align:left;">
    1975
    </td>
    <td style="text-align:right;">
    107
    </td>
    <td style="text-align:left;">
    The severity of seed predation of Guazuma ulmifolia (Sterculiaceae)
    by the beetle Amblycerus cistelinus (Bruchidae) ranges from 12% to
    42% (averages of 13—31 seed crops at each …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Ecological characteristics of Brachychiton populneus (Sterculiaceae)
    (kurrajong) in relation to the invasion of urban bushland in
    south‐western Australia
    </td>
    <td style="text-align:left;">
    M Buist, CJ Yates, PG Ladd
    </td>
    <td style="text-align:left;">
    2000
    </td>
    <td style="text-align:right;">
    28
    </td>
    <td style="text-align:left;">
    Brachychiton populneus (Sterculiaceae) (Schott et Endl.) R. Br.
    (kurrajong) is a small tree that occurs naturally ranging from
    southern Queensland to Victoria. It has been widely planted …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Floral ecology and reproductive biology of Pterospermum reticulatum
    Wight and Arn.(Sterculiaceae): a vulnerable tree species of Western
    Ghats of India.
    </td>
    <td style="text-align:left;">
    P Keshavanarayan, K Rajkumar
    </td>
    <td style="text-align:left;">
    2015
    </td>
    <td style="text-align:right;">
    2
    </td>
    <td style="text-align:left;">
    Pterospermum reticulatum Wight and Arn is a vulnerable according to
    IUCN red list category (1998). In the present study observations
    were made on floral ecology and reproductive …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    2
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Effects of habitat fragmentation on the demography of the critically
    endangered tree Pterospermum kingtungense (Sterculiaceae) in Yunnan,
    China
    </td>
    <td style="text-align:left;">
    CM Yuan, SS Zhang, GP Yang, J Chen, YF Geng, XS Li
    </td>
    <td style="text-align:left;">
    2021
    </td>
    <td style="text-align:right;">
    6
    </td>
    <td style="text-align:left;">
    We compared the demographic structure and reproductive output of the
    critically endangered tree Pterospermum kingtungense population in
    different size forest fragments (5-ha and 15-…
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    2
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    \[CITATION\]\[C\] Floral ecology and pollination in Eriolaena
    lushingtonii (Sterculiaceae), an endemic and threatened deciduous
    tree species of southern peninsular India
    </td>
    <td style="text-align:left;">
    AJS Raju, K VenkataRamana, PH Chandra
    </td>
    <td style="text-align:left;">
    2013
    </td>
    <td style="text-align:right;">
    2
    </td>
    <td style="text-align:left;">
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    2
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Comparative morphology and allometry of winged diaspores among the
    Asian Sterculiaceae
    </td>
    <td style="text-align:left;">
    T Yamada, E Suzuki
    </td>
    <td style="text-align:left;">
    1999
    </td>
    <td style="text-align:right;">
    18
    </td>
    <td style="text-align:left;">
    … Therefore, the Sterculiaceae is a particularly suitable family for
    testing … of the Asian Sterculiaceae in terms of this hypothesis. …
    species of Sterculiaceae investigated in the current …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    2
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Comparative analysis of steam distilled floral oils of cacao
    cultivars (Theobroma cacao L., Sterculiaceae) and attraction of
    flying insects: Implications for aTheobroma …
    </td>
    <td style="text-align:left;">
    AM Young, DW Severson
    </td>
    <td style="text-align:left;">
    1994
    </td>
    <td style="text-align:right;">
    38
    </td>
    <td style="text-align:left;">
    Steam-distilled floral fragrance oils from nine distinctive
    cultivars ofTheobroma cacao L. (Sterculiaceae) in Costa Rica were
    examined with GC-MS to determine whether or not major …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    2
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Notes on the plants of Bakossi, Cameroon, and the new Cola etugei
    and Cola kodminensis (Sterculiaceae s. str.)
    </td>
    <td style="text-align:left;">
    M Cheek, B Tchiengue, I Baldwin
    </td>
    <td style="text-align:left;">
    2020
    </td>
    <td style="text-align:right;">
    7
    </td>
    <td style="text-align:left;">
    Background and aims – This paper reports a further discovery during
    preparation for a monograph of the genus Cola, and also in the
    context of a long-term botanical survey in the Cross …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    2
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Habitat differences between two congeneric canopy trees,
    Pterospermum javanicum and P. diversifolium (Sterculiaceae) in an
    Indonesian floodplain forest
    </td>
    <td style="text-align:left;">
    T Yamada, OP Ngakan, E Suzuki
    </td>
    <td style="text-align:left;">
    2007
    </td>
    <td style="text-align:right;">
    10
    </td>
    <td style="text-align:left;">
    A comparison of tree architectures and allometries between two
    congeneric, lightdemanding species, Pterospermum diversifolium
    and P. javanicum (Sterculiaceae), revealed that P. …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    2
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    A taxonomic revision of Brachychiton (Sterculiaceae)
    </td>
    <td style="text-align:left;">
    GP Guymer
    </td>
    <td style="text-align:left;">
    1988
    </td>
    <td style="text-align:right;">
    49
    </td>
    <td style="text-align:left;">
    … Detailed new descriptions, distribution (including a map based on
    herbarium specimens), ecology, conservation status and relationship
    are provided for each taxon, and most taxa are …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    2
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Conservation genetics of Heritiera littoralis (Sterculiaceae), a
    threatened mangrove in China, based on AFLP and ISSR markers
    </td>
    <td style="text-align:left;">
    SG Jian, T Tang, Y Zhong, SH Shi
    </td>
    <td style="text-align:left;">
    2010
    </td>
    <td style="text-align:right;">
    14
    </td>
    <td style="text-align:left;">
    AFLP and ISSR markers were used to determine the genetic variations
    in eight mangrove and non-mangrove populations of Heritiera
    littoralis (Sterculiaceae), a threatened species in …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    2
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    POLLEN MORPHOLOGY AND HARMOMEGATHIC CHARACTERS OF Byttneria LÖFL.
    SPECIES (STERCULIACEAE S. S: SUBFAM. BYTTNERIOIDEAE)
    </td>
    <td style="text-align:left;">
    AJ Amirul-Aiman, T Noraini
    </td>
    <td style="text-align:left;">
    2019
    </td>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    … In doing so, more information on the species of Sterculiaceae ss
    family can be added. Dried … identification and differentiation of
    species in subfamily Byttnerioideae and Sterculiaceae ss …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    2
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    \[PDF\]\[PDF\] Three new records of Sterculiaceae for Bangladesh
    </td>
    <td style="text-align:left;">
    MMK Mia, MO Rahman
    </td>
    <td style="text-align:left;">
    2011
    </td>
    <td style="text-align:right;">
    9
    </td>
    <td style="text-align:left;">
    … Updated nomenclature, important synonyms, description, ecology and
    geographical … of Sterculiaceae occurring in Bangladesh. During the
    course of a revisionary work on Sterculiaceae …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    3
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Waltheria marielleae (Byttnerioideae, Malvaceae), a new species from
    north-eastern Brazil supported by morphological and phylogenetic
    evidence
    </td>
    <td style="text-align:left;">
    TS Coutinho, MA Sader, A Pedrosa-Harand
    </td>
    <td style="text-align:left;">
    2022
    </td>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    Background – Waltheria marielleae is a new species of Malvaceae
    endemic to north-eastern Brazil that occurs only in the states of
    Pernambuco and Alagoas, in areas of Atlantic Forest …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    3
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Genetic relationships among Fremontodendron (Sterculiaceae)
    populations of the Central Sierra Nevada foothills of California
    </td>
    <td style="text-align:left;">
    W Kelman, L Broadhurst, C Brubaker, A Franklin
    </td>
    <td style="text-align:left;">
    2006
    </td>
    <td style="text-align:right;">
    7
    </td>
    <td style="text-align:left;">
    … This distinction, coupled with its unique morphology and ecology,
    support the treatment of F. decumbens as a species and promote its
    continued conservation as a rare and unique …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    3
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    \[HTML\]\[HTML\] Distribution and frequency of galls induced by
    Anisodiplosis waltheriae Maia (Diptera: Cecidomyiidae) on the
    invasive plant Waltheria indica L.(Sterculiaceae)
    </td>
    <td style="text-align:left;">
    FVM Almeida, JC Santos, FAO Silveira
    </td>
    <td style="text-align:left;">
    2006
    </td>
    <td style="text-align:right;">
    17
    </td>
    <td style="text-align:left;">
    … Therefore, detailed knowledge is needed on the natural history and
    ecology of invasive plants. For instance, the biological control of
    such species with insects should be preceded with …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    3
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Novitates Gabonenses 83. Two new species of Cola (Sterculiaceae s.
    str.) from Gabon with an introductory note on the subdivision of the
    genus
    </td>
    <td style="text-align:left;">
    FJ Breteler
    </td>
    <td style="text-align:left;">
    2014
    </td>
    <td style="text-align:right;">
    2
    </td>
    <td style="text-align:left;">
    Background and aims – The existing subdivisions of the large genus
    Cola of the Sterculiaceae

<!-- -->

19. str. are briefly presented and their usefulness as regards the
    identification of newly …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    3
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Microsatellite loci for two East African tree species, Leptonychia
    usambarensis (Sterculiaceae) and Sorindeia madagascariensis
    (Anacardiaceae)
    </td>
    <td style="text-align:left;">
    NJ Cordeiro, KA Feldheim, E Ikejimba
    </td>
    <td style="text-align:left;">
    2008
    </td>
    <td style="text-align:right;">
    NA
    </td>
    <td style="text-align:left;">
    We isolated 20 trinucleotide microsatellites from two African tree
    species: Sorindeia madagascariensis (nine microsatellites) and
    Leptonychia usambarensis (11 microsatellites). …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    3
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Plasticity of Biomass Allocation and Tree Form in Relation to Light
    Environment of a Tropical Tree, Scaphium macropodum (Sterculiaceae)
    in Borneo.
    </td>
    <td style="text-align:left;">
    </td>
    <td style="text-align:left;">
    1998
    </td>
    <td style="text-align:right;">
    2
    </td>
    <td style="text-align:left;">
    Growth and death of leaves and stems were studied in monoaxial
    saplings of Scaphium macropodum (Sterculiaceae) of height from 1 to
    3 m in a tropical rain forest in West Kalimantan, …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    3
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    \[PDF\]\[PDF\] GENETIC RELATIONSHIPS AMONG FREMONTODENDRON
    (STERCULIACEAE) POPULATIONS OF THE CENTRAL SIERRA NEVADA FOOTHILLS
    OF …
    </td>
    <td style="text-align:left;">
    A FRANKLIN
    </td>
    <td style="text-align:left;">
    A FRANKLIN - academia.edu
    </td>
    <td style="text-align:right;">
    NA
    </td>
    <td style="text-align:left;">
    … This distinction, coupled with its unique morphology and ecology,
    support the treatment of F. decumbens as a species and promote its
    continued conservation as a rare and unique …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    3
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Sterculia xolocotzii (Sterculiaceae), a new species of rain forest
    canopy tree from the Isthmus of Tehuantepec, Mexico
    </td>
    <td style="text-align:left;">
    T Wendt, EL Taylor
    </td>
    <td style="text-align:left;">
    1999
    </td>
    <td style="text-align:right;">
    2
    </td>
    <td style="text-align:left;">
    … 5TERCULIA XOLOCOTZII (STERCULIACEAE), Anew SPECIES OF RAIN FOREST
    CANOPY TREE … The morphology and ecology of S. xolocotzii are
    detailed, and the distribution and …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    3
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Ecology of a leaf color polymorphism in a tropical forest species:
    habitat segregation and herbivory
    </td>
    <td style="text-align:left;">
    AP Smith
    </td>
    <td style="text-align:left;">
    1986
    </td>
    <td style="text-align:right;">
    79
    </td>
    <td style="text-align:left;">
    Byttneria aculeata (Sterculiaceae), a subcanopy liane with a shrubby
    juvenile form has two distinct leaf color morphs in juvenile plants-
    a given juvenile has plain green leaves or leaves …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    3
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Habitat associations of Sterculiaceae trees in a Bornean rain forest
    plot
    </td>
    <td style="text-align:left;">
    T Yamada, A Tomita, A Itoh, T Yamakura
    </td>
    <td style="text-align:left;">
    2006
    </td>
    <td style="text-align:right;">
    54
    </td>
    <td style="text-align:left;">
    … species, using the data on Sterculiaceae species in a 52-ha plot
    in Lambir Hills National Park, Sarawak, Malaysia. We focus on
    sympatric species in the Sterculiaceae, which have been …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    4
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Male and female booths with separate entrances in the tiny flowers
    of Guazuma ulmifolia (Malvaceae–Byttnerioideae). I. Structural
    integration
    </td>
    <td style="text-align:left;">
    C Westerkamp, AA Soares
    </td>
    <td style="text-align:left;">
    2006
    </td>
    <td style="text-align:right;">
    12
    </td>
    <td style="text-align:left;">
    In spite of their tiny dimensions (3×5mm 2 ), the strongly
    honey-scented flowers of Guazuma ulmifolia
    (Malvaceae–Byttnerioideae) provide six sexual chambers. A central
    female unit is …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    4
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Effects of habitat fragmentation on Dombeya acutangula
    (Sterculiaceae), a native tree on La Réunion (Indian Ocean)
    </td>
    <td style="text-align:left;">
    L Gigord, F Picot, JA Shykoff
    </td>
    <td style="text-align:left;">
    1999
    </td>
    <td style="text-align:right;">
    81
    </td>
    <td style="text-align:left;">
    … We use the information from this study of the population ecology
    of this species to propose management and reinforcement strategies
    to prevent further population decline. …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    4
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    The complete chloroplast genome of a rare species Heritiera
    parvifolia Merr. (Malvales: Sterculiaceae)
    </td>
    <td style="text-align:left;">
    GL Xin, XL Ren, WZ Liu, GL Jia, CY Deng
    </td>
    <td style="text-align:left;">
    2018
    </td>
    <td style="text-align:right;">
    3
    </td>
    <td style="text-align:left;">
    … parvifolia, there are many problems in ecology and arrangement of
    rationality, and fine breeds selectively. At present, there are very
    little researches on H. parvifolia, and most of them …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    4
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Adaptive significance of vegetative sprouting for a tropical canopy
    tree, Scaphium longiflorum (Sterculiaceae), in a peat swamp forest
    in Central Kalimantan
    </td>
    <td style="text-align:left;">
    T Yamada, Y Kumagawa, E Suzuki
    </td>
    <td style="text-align:left;">
    2001
    </td>
    <td style="text-align:right;">
    19
    </td>
    <td style="text-align:left;">
    Scaphium longiflorum Ridley (Sterculiaceae), a common canopy tree in
    peat swamp forests in the Far East, produces vegetative sprouting in
    its juvenile stage. We investigated …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    4
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Changes in spatial distribution during the life history of a
    tropical tree,Scaphium macropodum (Sterculiaceae) in borneo
    </td>
    <td style="text-align:left;">
    T Yamada, E Suzuki
    </td>
    <td style="text-align:left;">
    1997
    </td>
    <td style="text-align:right;">
    47
    </td>
    <td style="text-align:left;">
    … Scaphium macropodum (Sterculiaceae) is a common emergent tree of
    the tropical rain … Seed and seedling ecology of a monocarpic
    tropical tree, Tachigalia versicolor. Ecology 70: …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    4
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Ayenia albiflora, (Malvaceae, Byttnerioideae) a new species from the
    Cerrado of Goiás, Central‐West Brazil
    </td>
    <td style="text-align:left;">
    M Colli‐Silva, IL de Morais
    </td>
    <td style="text-align:left;">
    2022
    </td>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    … In this work, we describe a new species of Ayenia (Malvaceae,
    Byttnerioideae). Ayenia … A distribution map with known occurrence
    records, as well as comments on the ecology, …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    4
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Quantitative approach of Sterculia setigera Del.(Sterculiaceae)
    ethnobotanical uses among rural communities in Togo (West Africa)
    </td>
    <td style="text-align:left;">
    W Atakpama, K Batawila
    </td>
    <td style="text-align:left;">
    2015
    </td>
    <td style="text-align:right;">
    44
    </td>
    <td style="text-align:left;">
    Abstract The relationship between human, their culture and their
    environment has high importance in the frame of sustainable
    management of natural resources. This study aimed to …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    4
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    \[HTML\]\[HTML\] Novelties in Brazilian Waltheria L.
    (Byttnerioideae, Malvaceae): two new species and one
    re-establishment
    </td>
    <td style="text-align:left;">
    TS Coutinho, M Colli-Silva, M Alves
    </td>
    <td style="text-align:left;">
    2020
    </td>
    <td style="text-align:right;">
    3
    </td>
    <td style="text-align:left;">
    … Phylogenetic relationships and floral evolution of the
    Byttnerioideae (“Sterculiaceae” or Malvaceae sl) on sequences of the
    chloroplast gene, ndhF. Systematic Botany 26: 420-437.; …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    4
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    \[CITATION\]\[C\] Pollination biology of Eriolaena hookeriana Wight
    and Arn.(Sterculiaceae), a rare tree species of Eastern Ghats, India
    </td>
    <td style="text-align:left;">
    AJS Raju, PH Chandra, KV Ramana, JR Krishna
    </td>
    <td style="text-align:left;">
    2014
    </td>
    <td style="text-align:right;">
    NA
    </td>
    <td style="text-align:left;">
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    4
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Anthocyanins of the Sterculiaceae: Flavonoid scores and Hennigian
    phylogenetic systematics
    </td>
    <td style="text-align:left;">
    PM Richardson
    </td>
    <td style="text-align:left;">
    1982
    </td>
    <td style="text-align:right;">
    4
    </td>
    <td style="text-align:left;">
    … Published results of the distribution of anthocyanins in the
    Sterculiaceae have been re-interpreted on the basis of the
    phylogenetic status of the compounds present. A flavonoid score
    system …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Rainfall and soil fertility as factors limiting forest species
    distributions in Ghana
    </td>
    <td style="text-align:left;">
    MD Swaine
    </td>
    <td style="text-align:left;">
    1996
    </td>
    <td style="text-align:right;">
    295
    </td>
    <td style="text-align:left;">
    … group includes the majority of the tree species tested, including
    two ectomycorrhizal legumes; the wet and dry fertile groups are
    notable for the inclusion of deciduous Sterculiaceae. No …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    \[PDF\]\[PDF\] (STERCULIACEAE) IN NORTHEASTERN COSTA RICA
    </td>
    <td style="text-align:left;">
    H ODIUS
    </td>
    <td style="text-align:left;">
    1979
    </td>
    <td style="text-align:right;">
    2
    </td>
    <td style="text-align:left;">
    … odius sucking on the drying seeds of the well known
    commercially-cultivated, tropical cash crop, Theobroma cacao
    Linnaeus (Sterculiaceae) commonly known as “cacao’” or “cocoa.’” …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    \[PDF\]\[PDF\] Tree species diversity and structure of a Nigerian
    strict nature reserve
    </td>
    <td style="text-align:left;">
    SO Akindele
    </td>
    <td style="text-align:left;">
    2013
    </td>
    <td style="text-align:right;">
    157
    </td>
    <td style="text-align:left;">
    … Sterculiaceae had the highest number of species (10 species),
    followed by Meliaceae and … by only one species in the forest and
    Sterculiaceae had the highest number of individuals (…
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Brachychiton guymeri JA Bever., Fensham & PI Forst.(Sterculiaceae),
    a new species from north Queensland
    </td>
    <td style="text-align:left;">
    RJ Fensham, JA Beveridge, PI Forster
    </td>
    <td style="text-align:left;">
    2019
    </td>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    … The association of this new species to dry rainforest patches is
    significant in relation to the speciation hypothesis that we present
    in this paper, so a short overview of the ecology of this …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    Pollination biology of the rare shrub Fremontodendron decumbens
    (Sterculiaceae)
    </td>
    <td style="text-align:left;">
    RS Boyd
    </td>
    <td style="text-align:left;">
    1994
    </td>
    <td style="text-align:right;">
    23
    </td>
    <td style="text-align:left;">
    Pollination is crucial to successful sexual reproduction in plants.
    I studied the pollination biology of the rare shrub Fremontodendron
    decumbens to obtain basic information on floral …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    The status of cacao (Theobroma cacao, Sterculiaceae) in the western
    hemisphere
    </td>
    <td style="text-align:left;">
    JR Hunter
    </td>
    <td style="text-align:left;">
    1990
    </td>
    <td style="text-align:right;">
    61
    </td>
    <td style="text-align:left;">
    … , however, no references were made to the ecology of the region in
    which the cultivar was … None of this body of work includes detailed
    information on the ecology of the collection site …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    \[PDF\]\[PDF\] Hermannia grandi? ora Aiton (Sterculiaceae)
    </td>
    <td style="text-align:left;">
    A Harrower
    </td>
    <td style="text-align:left;">
    2009
    </td>
    <td style="text-align:right;">
    NA
    </td>
    <td style="text-align:left;">
    This striking species is as yet unknown in horticulture despite its
    alluring name, and is really worth growing as a pot plant, or in the
    garden or rock garden. When viewed from a distance, …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    FLORAL BIOLOGY AND BREEDING SYSTEM OF STERCULIA CHICHA ST. HIL.
    (STERCULIACEAE)
    </td>
    <td style="text-align:left;">
    N Taroda, PE Gibbs
    </td>
    <td style="text-align:left;">
    1982
    </td>
    <td style="text-align:right;">
    54
    </td>
    <td style="text-align:left;">
    … No studies have been published on the floral biology/pollination
    ecology or breeding system of any Sterculia species. This is perhaps
    not surprising since they occur naturally as isolated…
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    A new species of Cola (Sterculiaceae) from the Usambara Mountains,
    Tanzania
    </td>
    <td style="text-align:left;">
    M Cheek
    </td>
    <td style="text-align:left;">
    2002
    </td>
    <td style="text-align:right;">
    8
    </td>
    <td style="text-align:left;">
    Cola lukei Cheek is described from the Usambara Mts, Tanzania, and
    its affinities with species in the Kenyan coastal forest and the
    Udzungwa Mts of Tanzania are discussed. …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Byttnerioideae” OR “Sterculiaceae” AND “ecology”
    </td>
    <td style="text-align:left;">
    \[PDF\]\[PDF\] TOMENTOSA L.(STERCULIACEAE)
    </td>
    <td style="text-align:left;">
    LC Leal, CEM de Sá, DGP de Lima, TB Guedes
    </td>
    <td style="text-align:left;">
    LC Leal, CEM de Sá, DGP de Lima, TB Guedes… - Ecologia da … -
    researchgate.net
    </td>
    <td style="text-align:right;">
    NA
    </td>
    <td style="text-align:left;">
    … O objetivo deste trabalho foi verificar a influência da paisagem
    floral de uma população de Melochia tomentosa (Sterculiaceae) na
    distância de transferência do pólen por vetores …
    </td>
    </tr>
    </tbody>
    </table>
    </div>

``` r
# Test automatic term extraction from abstracts for the first 5 pages of results
gs_df = test2
test2_gs_terms = litsearchr::extract_terms(text = gs_df[,"abstract"],
                                           method = "fakerake", min_freq = 3, min_n = 2,
                                           stopwords = stopwords::data_stopwords_stopwordsiso$en)
cat("Test 2: Automatically extracted terms: \n")
```

    ## Test 2: Automatically extracted terms:

``` r
print(test2_gs_terms)
```

    ##  [1] "asian sterculiaceae"        "bytmeriu \nuculeutu"       
    ##  [3] "continued conservation"     "equally frequent"          
    ##  [5] "family sterculiaceae"       "floral evolution"          
    ##  [7] "floral morphology"          "foliar nectaries"          
    ##  [9] "lowland costa"              "paper reports"             
    ## [11] "phylogenetic relationships" "pollen morphology"         
    ## [13] "sympatric species"          "systematic botany"         
    ## [15] "unique morphology"

# TEST 3: Try many different search term combinations for all genera within the Byttnerioideae

``` r
# Run multiple queries using lists of specific genera in the Byttnerioideae
first  = list(term = 'intext:"Hermannieae" OR "Lasiopetaleae" OR "Bytttnerieae" OR "Theobromeae"', pages = 1:5)
second = list(term = 'intext:"Waltheria" OR "Hermannia" OR "Melochia" OR "Hannafordia" OR "Maxwellia"', pages = 1:5)
third = list(term = 'intext:"Lasiopetalum" OR "Thomasia" OR "Guichenotia" OR "Commersonia" OR "Rulingia"', pages = 1:5)
fourth = list(term = 'intext:"Keraudrenia" OR "Seringia" OR "Abroma" OR "Byttneria" OR "Ayenia" OR "Rayleya"', pages = 1:5)
fifth = list(term = 'intext:"Kleinhovia" OR "Leptonychia" OR "Scaphopetalum" OR "Theobroma" OR "Herrania" OR "Glossostemon" OR "Guazuma"', pages = 1:5)
test_list = list(first, second, third, fourth, fifth) 

test3 = do.call(rbind, lapply(test_list, function(x) do.call(scrape_gs, c(crawl_delay = 1.2, x))))
cat("Test 3: Query results from Google Scholar\n")
```

    ## Test 3: Query results from Google Scholar

``` r
# Display the table as a scrollable object using kableExtra package
kbl(test3, table.attr = "style = \"color: black;\"") %>%
  kable_styling("striped", full_width = F) %>%
  scroll_box(width = "100%")
```

<div style="border: 1px solid #ddd; padding: 5px; overflow-x: scroll; width:100%; ">
<table style="color: black; width: auto !important; margin-left: auto; margin-right: auto;" class="table table-striped">
<thead>
<tr>
<th style="text-align:right;">
page
</th>
<th style="text-align:left;">
term
</th>
<th style="text-align:left;">
title
</th>
<th style="text-align:left;">
authors
</th>
<th style="text-align:left;">
year
</th>
<th style="text-align:right;">
n\_citations
</th>
<th style="text-align:left;">
abstract
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
Phylogenetic relationships and floral evolution of the Byttnerioideae
(“Sterculiaceae” or Malvaceae sl) based on sequences of the chloroplast
gene, ndhF
</td>
<td style="text-align:left;">
BA Whitlock, C Bayer, DA Baum
</td>
<td style="text-align:left;">
2001
</td>
<td style="text-align:right;">
143
</td>
<td style="text-align:left;">
… (Byttnerieae, Theobromeae, Hermannieae, Lasiopetaleae) appear to … The
Hermannieae is the only tribe that appears to be … members of the
Lasiopetaleae. The remaining Byttnerieae, …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Inflorescence morphology of some Australian Lasiopetaleae
(Sterculiaceae)
</td>
<td style="text-align:left;">
C Bayer, K Kubitzki
</td>
<td style="text-align:left;">
1996
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
… The inflorescence morphology of 17 species out of five genera of the
tribe Lasiopetaleae (… links the predominently Australian tribe
Lasiopetaleae with the pantropical tribe Byttnerieae. …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[BOOK\]\[B\] Systematics and Evolution of Waltheria (Sterculiaceae:
Hermannieae)
</td>
<td style="text-align:left;">
JG Saunders
</td>
<td style="text-align:left;">
1995
</td>
<td style="text-align:right;">
24
</td>
<td style="text-align:left;">
This dissertation provides a monograph of the primarily distylous genus
Waltheria (Sterculiaceae: Hermannieae). Waltheria is readily
distinguished from Melochia and Dicarpidium by …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
New chromosome numbers for Lasiopetaleae: Malvaceae sl (or
Sterculiaceae)
</td>
<td style="text-align:left;">
CF Wilkins, JA Chappill
</td>
<td style="text-align:left;">
2002
</td>
<td style="text-align:right;">
11
</td>
<td style="text-align:left;">
… records for these genera and other Lasiopetaleae Lysiosepalum and
Hannafordia. There is … but with affinities to Lasiopetaleae. Comparison
of Lasiopetaleae chromosome numbers with …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] A revision of species from the tribe Lasiopetaleae
(Byttnerioideae: Malvaceae) with rostrate anthers
</td>
<td style="text-align:left;">
KA Shepherd, CF Wilkins
</td>
<td style="text-align:left;">
2015
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
Shepherd, KA & Wilkins, CF A revision of species from the tribe
Lasiopetaleae (Byttnerioideae: Malvaceae) with rostrate anthers. Nuytsia
25: 171–189 (2015). An informal group of …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
Seed and seedling morphology, and seed anatomy of Lasiopetaleae
(Malvaceae sl or Sterculiaceae)
</td>
<td style="text-align:left;">
CF Wilkins, JA Chappill
</td>
<td style="text-align:left;">
2002
</td>
<td style="text-align:right;">
15
</td>
<td style="text-align:left;">
… Three further genera have been suggested to belong within
Lasiopetaleae. These are … that were absent in other observed
Lasiopetaleae. The Lasiopetaleae tegmen is wider than the …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
Genomic data and morphological re‐assessment reveals synonymy and
hybridisation among Seringia taxa (Lasiopetaleae, Malvaceae) in remote
north‐western …
</td>
<td style="text-align:left;">
RM Binks, CF Wilkins, AS Markey, MN Lyons, M Byrne
</td>
<td style="text-align:left;">
2020
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
Conservation of rare or threatened species requires a range of
information, including a sound taxonomic foundation, to ensure
appropriate management. However, rare species are …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
Polyphyly of Rulingia and Commersonia (Lasiopetaleae, Malvaceae sl)
</td>
<td style="text-align:left;">
BA Whitlock, AM Hale, JL Indorf
</td>
<td style="text-align:left;">
2011
</td>
<td style="text-align:right;">
8
</td>
<td style="text-align:left;">
… in stamen number occurs within Lasiopetaleae: the monotypic Maxwellia
has 10 stamens (Baillon 1871), whereas all other Lasiopetaleae and its
sister group Hermannieae have five or …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
Seringia revised to include Keraudrenia (Lasiopetaleae: Malvaceae sl)
</td>
<td style="text-align:left;">
CF Wilkins, BA Whitlock
</td>
<td style="text-align:left;">
2016
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:left;">
Seringia J.Gay and Keraudrenia J.Gay are widely spread in Australia, and
one species occurs in Madagascar. Revision of these closely related
genera suggested that neither genus is …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
Waltheria berteroi (Sterculiaceae, Hermannieae), a new combination from
Colombia and Venezuela
</td>
<td style="text-align:left;">
JG Saunders
</td>
<td style="text-align:left;">
2005
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
A new combination in Sterculiaceae, Hermannieae, Waltheria berteroi
(Sprengel) JG Saunders, is created and lectotypified. Waltheria
subcordata Standley from Colombia and …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Resurrección de la especie endémica de Maui, Waltheria
pyrolifolia (Sterculiaceae, Hermannieae)
</td>
<td style="text-align:left;">
JG Saunders
</td>
<td style="text-align:left;">
2011
</td>
<td style="text-align:right;">
8
</td>
<td style="text-align:left;">
Old and new morphological characters confirm that Waltheria pyrolifolia
is abundantly distinct from W. indica sl, not its synonym, nor
teratological material of it. The first illustrations of W. …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] New species of Waltheria (Hermannieae, Byttnerioideae,
Malvaceae) from Paraguay, Argentina, and Venezuela, and two new records
for Paraguay
</td>
<td style="text-align:left;">
JG Saunders
</td>
<td style="text-align:left;">
2005
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
Saunders, JG 2005. New species of Waltheria (Hermannieae,
Byttnerioideae, Malvaceae) from Paraguay, Argentina, and Venezuela, and
two new records for Paraguay. Darwiniana 43 …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
A new penicillate-stigma species of Waltheria (Sterculiaceae,
Hermannieae) endemic to Belize
</td>
<td style="text-align:left;">
JG Saunders, R Pozner
</td>
<td style="text-align:left;">
2007
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
Waltheria belizensis JG Saunders, a new distinctive Sterculiaceae
species known only from Mountain Pine Ridge National Forest Reserve,
Cayo District, Belize, and potentially …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
Taxonomic Revision of Hannafordia (Lasiopetaleae: Sterculiaceae
(Malvaceae sl))
</td>
<td style="text-align:left;">
CF Wilkins, JA Chappill
</td>
<td style="text-align:left;">
2001
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
… been no comprehensive treatment of Hannafordia since ‘Flora
Australiensis’ (Bentham 1863), this paper presents a formal revision of
the genus as part of a revision of Lasiopetaleae for …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Five new clavate-stigma Waltheria species endemic to
Brazilian cerrado (Malvaceae sl, Byttnerioideae, Hermannieae)
</td>
<td style="text-align:left;">
JG Saunders
</td>
<td style="text-align:left;">
2021
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
… Subconduplicate petals are first reported here in a tribe
(Hermannieae) usually keyed by presence of flat petals to oppose those
with cucullate-ligulate petals found in Theobromeae or …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Commersonia rosea (Malvaceae sl: Lasiopetaleae): a new,
rare fire-ephemeral species from the upper Hunter Valley of New South
Wales
</td>
<td style="text-align:left;">
SAJ Bell, LM Copeland
</td>
<td style="text-align:left;">
2004
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:left;">
Copel., a fire-ephemeral species from the Central Western Slopes of New
South Wales is described as new. Notes on its distribution, ecology and
conservation status are given. The …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
Taxonomic revision of Guichenotia (Lasiopetaleae: Malvaceae sl or
Sterculiaceae)
</td>
<td style="text-align:left;">
CF Wilkins, JA Chappill
</td>
<td style="text-align:left;">
2003
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
… been no comprehensive treatment of Guichenotia since ‘Flora
Australiensis’ (Bentham 1863), this paper presents a formal revision of
the genus as part of a revision of Lasiopetaleae for …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
Lasiopetalum pterocarpum (Malvaceae sl: Lasiopetaleae), a new and rare
species from south-west Western Australia
</td>
<td style="text-align:left;">
K Shepherd, E Bennett, C Wilkins, L Sage
</td>
<td style="text-align:left;">
2006
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
… pterocarpum is named, is unique within Lasiopetaleae.
Lasiopetalumpterocarpum is allied to L. floribundum, which shares a
similar habit, large ovate leaves and loosedichasial …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Especies nuevas de Waltheria (Hermannieae,
Byttnerioideae, Malvaceae) de Paraguay, Argentina y Venezuela, y dos
nuevas citas para Paraguay
</td>
<td style="text-align:left;">
JG Saunders
</td>
<td style="text-align:left;">
2005
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
Saunders, JG 2005. New species of Waltheria (Hermannieae,
Byttnerioideae, Malvaceae) from Paraguay, Argentina, and Venezuela, and
two new records for Paraguay. Darwiniana 43 …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
Guichenotia anota and Guichenotia apetala (Lasiopetaleae: Byttneriaceae
or Malvaceae s. lat.) a new and revised species endemic to the
Ravensthorpe Range …
</td>
<td style="text-align:left;">
C Wilkins, BA Whitlock
</td>
<td style="text-align:left;">
2009
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
Wilkins, CF & Whitlock, BA Guichenotia anota and Guichenotia apetala
(Lasiopetaleae: Byttneriaceae or Malvaceae s. lat.) a new and a revised
species endemic to the Ravensthorpe …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
Support for an expanded family concept of Malvaceae within a
recircumscribed order Malvales: a combined analysis of plastid atpB and
rbcL DNA sequences
</td>
<td style="text-align:left;">
C Bayer, MF FAY, AY DE BRUIJN
</td>
<td style="text-align:left;">
1999
</td>
<td style="text-align:right;">
446
</td>
<td style="text-align:left;">
… data: (1) Byttnerioideae, including tribes Byttnerieae, Lasiopetaleae
and Theobromeae (all of which have cucullate petals) and Hermannieae;
(2) Grewioideae, including most genera of …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[CITATION\]\[C\] A systematic study of Lasiopetaleae (Malvaceae sl or
Sterculiaceae)
</td>
<td style="text-align:left;">
CF Wilkins
</td>
<td style="text-align:left;">
2002
</td>
<td style="text-align:right;">
8
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[CITATION\]\[C\] Buttnerieae, Lasiopetaleae, Malveae, Malopeae, Ureneae
</td>
<td style="text-align:left;">
IE WEBBER
</td>
<td style="text-align:left;">
1934
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
Phylogeny of the core Malvales: evidence from ndhF sequence data
</td>
<td style="text-align:left;">
WS Alverson, BA Whitlock, R Nyffeler
</td>
<td style="text-align:left;">
1999
</td>
<td style="text-align:right;">
549
</td>
<td style="text-align:left;">
… Byttnerieae, Hermannieae, and Lasiopetaleae of Sterculiaceae. … and,
presumably independently, in some Lasiopetaleae. If … then they have
been lost in most members of …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[CITATION\]\[C\] A taxonomic revision of the Western Australian genus
Lysiosepalum (Malvaceae: Lasiopetaleae).
</td>
<td style="text-align:left;">
C Wilkins, J Chappill
</td>
<td style="text-align:left;">
2001
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
A taxonomic revision of the Western Australian genus Lysiosepalum
(Malvaceae: Lasiopetaleae). — the UWA Profiles and Research Repository …
A taxonomic revision of …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[CITATION\]\[C\] Systematic and evolution of Waltheria
(Sterculiaceae-Hermannieae)(Tesis doctoral)
</td>
<td style="text-align:left;">
JG Saunders
</td>
<td style="text-align:left;">
1995
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[CITATION\]\[C\] Systematics and evolution of
Waltheria(Sterculiaceae-Hermannieae). v. 1-3
</td>
<td style="text-align:left;">
JG Saunders
</td>
<td style="text-align:left;">
1995
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[CITATION\]\[C\] Systematics and Evolution of Evolution of Waltheria
(Sterculiaceae: Hermannieae)
</td>
<td style="text-align:left;">
JG SAUDERS
</td>
<td style="text-align:left;">
1995
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[CITATION\]\[C\] Surveillance morpho-molecular characterization and in
vitro management of Lasiodiplodia theobromeae causing twig die back of
loquat
</td>
<td style="text-align:left;">
F Naz
</td>
<td style="text-align:left;">
2017
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[CITATION\]\[C\] Lasiopetalum ferraricollinum (Malvaceae s. lat.:
Lasiopetaleae), a new species from the ironstone hills near Forrestania,
Western Australia
</td>
<td style="text-align:left;">
EM Bennett, KA Shepherd
</td>
<td style="text-align:left;">
2007
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
Seringia (Byttneriaceae/Malvaceae-Byttnerioideae) new to Southeast Asia
and S. botak endangered in Indonesian New Guinea grassland and savannah
</td>
<td style="text-align:left;">
M Cheek, J Wanma, M Jitmau
</td>
<td style="text-align:left;">
2018
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
… The Byttneriaceae have been divided into three tribes since the 19th
century: Hermannieae, Byttnerieae and Lasiopetaleae (Cheek 2007a). The
first two are both centred in tropical …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[CITATION\]\[C\] … and Chocolate: Quantifying drought stress tolerance
in Cacao (Theobroma cacao L.) and its wild relatives in the tribe
Theobromeae based on predictive …
</td>
<td style="text-align:left;">
WA Harris
</td>
<td style="text-align:left;">
2021
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">
Blood and Chocolate: Quantifying drought stress tolerance in Cacao
(Theobroma cacao L.) and its wild relatives in the tribe Theobromeae
based on predictive characterization and …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] WESTERN AUSTRALIA’S JOURNAL OF SYSTEMATIC BOTANY
</td>
<td style="text-align:left;">
EM Bennett, KA Shepherd
</td>
<td style="text-align:left;">
EM Bennett, KA Shepherd - researchgate.net
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">
… Lasiopetaleae), a new species from the ironstone hills near
Forrestania, Western Australia … Lasiopetaleae), a new species from the
ironstone hills near Forrestania, Western Australia. …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] The age of chocolate: a diversification history of
Theobroma and Malvaceae
</td>
<td style="text-align:left;">
JE Richardson, BA Whitlock, AW Meerow
</td>
<td style="text-align:left;">
2015
</td>
<td style="text-align:right;">
92
</td>
<td style="text-align:left;">
… We demonstrate that diversification in Theobromeae coincided with
major periods of uplift of the Andean mountains and that diversity in
the tribe is greatest in areas flanking the Andes. …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Genome size, cytogenetic data and transferability of
EST-SSRs markers in wild and cultivated species of the genus Theobroma
L. (Byttnerioideae, Malvaceae …
</td>
<td style="text-align:left;">
RA da Silva, G Souza, LSL Lemos, UV Lopes
</td>
<td style="text-align:left;">
2017
</td>
<td style="text-align:right;">
16
</td>
<td style="text-align:left;">
The genus Theobroma comprises several trees species native to the
Amazon. Theobroma cacao L. plays a key economic role mainly in the
chocolate industry. Both cultivated and wild …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
Morphology of the androecium in Malvales
</td>
<td style="text-align:left;">
W van Heel
</td>
<td style="text-align:left;">
1966
</td>
<td style="text-align:right;">
138
</td>
<td style="text-align:left;">
In order to test the applicability of the telome and the classic
theories to the nature of stamens a study was made of flowers in
Malvaceae, Bombacaceae, Sterculiaceae, Tiliaceae, and …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
The genus Melochia L.(Sterculiaceae)
</td>
<td style="text-align:left;">
A Goldberg
</td>
<td style="text-align:left;">
1967
</td>
<td style="text-align:right;">
76
</td>
<td style="text-align:left;">
… The petals of the Hermannieae and Helictereae are flat and usually
longer than the calyx; those of the Buettnerieae are cuculla te at the
base and embrace the anthers, often ligulate at …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Pollen morphology of Sterculiaceae (s. str.) in Egypt and
its taxonomic significance
</td>
<td style="text-align:left;">
R Hamdy, E Shamso
</td>
<td style="text-align:left;">
2010
</td>
<td style="text-align:right;">
16
</td>
<td style="text-align:left;">
… Byttnerioideae is an europalynous, comprises three tribes of which
tribes Byttnerieae and Theobromeae characterized by the presence of only
one pollen type. The Abroma type is …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
A revision of Melochia section Physodium (Sterculiaceae) from Mexico
</td>
<td style="text-align:left;">
LJ Dorr, LC Barnett
</td>
<td style="text-align:left;">
1989
</td>
<td style="text-align:right;">
22
</td>
<td style="text-align:left;">
… It is included in the Hermannieae Sprengel, a tribe that is
circumscribed by the possession of five … Melochia is easily
distinguished from the other four members of the Hermannieae, …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] WILLIAM S. ALVERSON, 2, 4 BARBARA A. WHITLOCK, 2
</td>
<td style="text-align:left;">
R NYFFELER, C BAYER, DA BAUM
</td>
<td style="text-align:left;">
1999
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">
… , Hermannieae, and Lasiopetaleae of Sterculiaceae. The most striking
departures from traditional classifications are the following: Durio and
relatives appear to be more closely related …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[CITATION\]\[C\] Notes on Malvaceæ and Sterculiaceæ
</td>
<td style="text-align:left;">
G Bentham
</td>
<td style="text-align:left;">
1862
</td>
<td style="text-align:right;">
27
</td>
<td style="text-align:left;">
BOTANISTB appear to be unanimous in bringing together the group of
orders designated by Endlicher under the name of Colunanifra, by
Brongniart under that of Mnlvoidecc, and …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] A synoptical account of the Sterculiaceae in Bangladesh
</td>
<td style="text-align:left;">
MO Rahman, MA Hassan
</td>
<td style="text-align:left;">
2012
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
… is regarded as the most primitive because of the presence of
pentacyclic flowers, numerous stamens, apocarpous gynoecia, and many
ovulate locules, while the Hermannieae is the …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[CITATION\]\[C\] Heterostyly, homostyly and incompatibility in
Waltheria indica L.(Sterculiaceae)
</td>
<td style="text-align:left;">
B Bahadur, NP Reddy
</td>
<td style="text-align:left;">
1977
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
A critical revision of certain taxonomic groups of the Malvales. Part II
</td>
<td style="text-align:left;">
HL Edlin
</td>
<td style="text-align:left;">
1935
</td>
<td style="text-align:right;">
47
</td>
<td style="text-align:left;">
… On these grounds, the Buettnerieae and their allies, especially the
Hermannieae (eg Waltheria with 5 stamens) and Lasiopetaleae, are more
highly evolved than the Tiliaceae. Free and …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[CITATION\]\[C\] Effect of phytochemical constituents of Ricinus
communis, Pterocarpus santalinus, Terminalia belerica on antibacterial,
antifungal and cytotoxic activity
</td>
<td style="text-align:left;">
P Vandita, N Amin, P Khyati, K Monisha
</td>
<td style="text-align:left;">
2013
</td>
<td style="text-align:right;">
28
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
Acetylcholinesterase activity of alkaloids from the leaves of Waltheria
brachypetala
</td>
<td style="text-align:left;">
MMC Lima, JA López, JM David, EP Silva
</td>
<td style="text-align:left;">
2009
</td>
<td style="text-align:right;">
16
</td>
<td style="text-align:left;">
… The Hermannieae tribe of this family is composed of only two genera,
Waltheria and … brachypetala confirms that quinoline alkaloids are
common in the Hermannieae, especially …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Pollen morphology of Waltheria L.
(Malvaceae-Byttnerioideae) from Bahia, Brazil
</td>
<td style="text-align:left;">
CEA Silveira, LCL Lima, MD Saba
</td>
<td style="text-align:left;">
2017
</td>
<td style="text-align:right;">
15
</td>
<td style="text-align:left;">
Waltheria encompasses about 60 species distributed in Brazil and Mexico.
To improve the palynology of the genus, we analyzed 14 species occurring
in the Brazilian State of Bahia. …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
Bioactive alkaloids isolated from Waltheria indica (Malvaceae)
</td>
<td style="text-align:left;">
R de Medeiros Silva, L Domingos, A Ferreira-Pereira
</td>
<td style="text-align:left;">
2019
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">
… The Waltheria and Melochia genera of the Hermannieae tribe (family
Malvaceae) are the most studied under phytochemistry point of view,
beeing related to species that genera a new …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
Corrigendum to: A revision of Commersonia including Rulingia (Malvaceae
sl or Byttneriaceae)
</td>
<td style="text-align:left;">
CF Wilkins, BA Whitlock
</td>
<td style="text-align:left;">
2012
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:left;">
… data), both genera have recently been included in Lasiopetaleae J.Gay.
Rulingia was … Gay (1821) defined Lasiopetaleae as apetalous or having
much reduced scale-like petals, with …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Hermannieae” OR “Lasiopetaleae” OR “Bytttnerieae” OR
“Theobromeae”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Vale Lorraine Cobb April 12th 1951–April 5th 2008
</td>
<td style="text-align:left;">
K Shepherd, R Butcher
</td>
<td style="text-align:left;">
2008
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
I would like to tell you all a little about our memories of Lori on
behalf of the group of women referred to in some circles as the
‘Herbarium Harem’. We are a close group of friends that …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Botany, traditional uses, phytochemistry and pharmacology of Waltheria
indica L.(syn. Waltheria americana): a review
</td>
<td style="text-align:left;">
F Zongo, C Ribuot, A Boumendjel, I Guissou
</td>
<td style="text-align:left;">
2013
</td>
<td style="text-align:right;">
102
</td>
<td style="text-align:left;">
… RESULTS: Crude extracts and isolated compounds from Waltheria indica
were investigated … indicated that Waltheria indica can be toxic.
CONCLUSION: Waltheria indica possess …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Antifungal Quinoline Alkaloids from Waltheria indica
</td>
<td style="text-align:left;">
S Cretton, S Dorsaz, A Azzollini
</td>
<td style="text-align:left;">
2016
</td>
<td style="text-align:right;">
104
</td>
<td style="text-align:left;">
Chemical investigation of a dichloromethane extract of the aerial parts
of Waltheria indica led to the isolation and characterization of five
polyhydroxymethoxyflavonoids, namely, …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Ethnobotanical, phytochemistry, and pharmacological
property of Waltheria Indica Linn
</td>
<td style="text-align:left;">
C Nirmala, M Sridevi
</td>
<td style="text-align:left;">
2021
</td>
<td style="text-align:right;">
15
</td>
<td style="text-align:left;">
Background In modern therapeutics, various human pathological
disturbances were treated with the plant-based products. Waltheria
indica Linn, a perennial herb, was commonly used …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Cancer chemopreventive activity of compounds isolated from Waltheria
indica
</td>
<td style="text-align:left;">
A Monteillier, S Cretton, O Ciclet, L Marcourt
</td>
<td style="text-align:left;">
2017
</td>
<td style="text-align:right;">
37
</td>
<td style="text-align:left;">
… This study aimed first to confirm the use of Waltheria indica as a
traditional anti-inflammatory remedy by assessing the NF-κB inhibitory
activity and then to identify the major bioactive …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Antitrypanosomal Quinoline Alkaloids from the Roots of Waltheria indica
</td>
<td style="text-align:left;">
S Cretton, L Breant, L Pourrez, C Ambuehl
</td>
<td style="text-align:left;">
2014
</td>
<td style="text-align:right;">
103
</td>
<td style="text-align:left;">
Chemical investigation of the dichloromethane root extract of Waltheria
indica led to the isolation and characterization of 10 quinoline
alkaloids, namely, 8-deoxoantidesmone (1), …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Inhibitory effects of the flavonoids isolated from Waltheria indica on
the production of NO, TNF-α and IL-12 in activated macrophages
</td>
<td style="text-align:left;">
YK Rao, SH Fang, YM Tzeng
</td>
<td style="text-align:left;">
2005
</td>
<td style="text-align:right;">
105
</td>
<td style="text-align:left;">
… Three flavonoids were isolated from the whole plants of Waltheria
indica and biological properties investigated. On the basis of their
spectroscopic data, these compounds were …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Antibacterial and phytochemical screening of crude
ethanolic extracts of Waltheria indica Linn
</td>
<td style="text-align:left;">
OO Olajuyigbe, AE Babalola
</td>
<td style="text-align:left;">
2011
</td>
<td style="text-align:right;">
59
</td>
<td style="text-align:left;">
The antibacterial activity and phytochemical properties of Waltheria
indica Linn. was investigated, the result obtained showed that the
ethanol extracts from the roots, stem and leaves …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Antidiarrhoeal activity of Waltheria americana, Commelina coelestis and
Alternanthera repens
</td>
<td style="text-align:left;">
MA Zavala, S Perez, C Perez, R Vargas
</td>
<td style="text-align:left;">
1998
</td>
<td style="text-align:right;">
172
</td>
<td style="text-align:left;">
The antidiarrhoeal activity of the hexane, chloroform, methanol and
aqueous extracts of Waltheria americana, Commelina coelestis and
Alternathera repens, was tested on mice with …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Microemulsion preparation of Waltheria indica extracts and preliminary
antifungal mechanism exploration
</td>
<td style="text-align:left;">
F Yin, Q Liu, B Zhang, X Zhang, J He, J Xie, Z Hu
</td>
<td style="text-align:left;">
2021
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:left;">
Waltheria indica L. is a traditional officinal plant with high medicinal
values. This work aims to develop a stable microemulsion of W. indica
extract and study its antifungal activity against …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
An unusual quinolinone alkaloid from Waltheria douradinha
</td>
<td style="text-align:left;">
SCSM Hoelzel, ER Vieira, SR Giacomelli, II Dalcol
</td>
<td style="text-align:left;">
2005
</td>
<td style="text-align:right;">
54
</td>
<td style="text-align:left;">
The chemical investigation of the methanolic extract of the root bark of
Waltheria douradinha (Sterculiaceae) afforded an unusual quinolinone
alkaloid named waltherione-A (1). Its …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Sterculiaceae of Paraguay. II. Waltheria
</td>
<td style="text-align:left;">
JG Saunders
</td>
<td style="text-align:left;">
2007
</td>
<td style="text-align:right;">
34
</td>
<td style="text-align:left;">
The biodiversity and geographical distribution for Waltheria species in
Paraguay are presented to accompany the floristic treatment for the rest
of the family given separately in Part I (…
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
4-Quinolone Alkaloids from Melochia odorata
</td>
<td style="text-align:left;">
RC Jadulco, CD Pond, RM Van Wagoner
</td>
<td style="text-align:left;">
2014
</td>
<td style="text-align:right;">
66
</td>
<td style="text-align:left;">
The methanol extract of Melochia odorata yielded three 4-quinolone
alkaloids including waltherione A (1) and two new alkaloids, waltherione
C (2) and waltherione D (3). Waltheriones …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Quinolone alkaloids from Waltheria douradinha
</td>
<td style="text-align:left;">
V Gressler, CZ Stüker, G de OC Dias, II Dalcol
</td>
<td style="text-align:left;">
2008
</td>
<td style="text-align:right;">
46
</td>
<td style="text-align:left;">
A phytochemical investigation of the stems of Waltheria douradinha
resulted in isolation of two 4-quinolone alkaloids, waltherione B and
vanessine, along with three known alkaloids, …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] GC-MS analysis of bioactive compounds from ethanolic leaf
extract of Waltheria indica Linn. and their pharmacological activities
</td>
<td style="text-align:left;">
P Banakar, M Jayaraj
</td>
<td style="text-align:left;">
2018
</td>
<td style="text-align:right;">
53
</td>
<td style="text-align:left;">
… identify the compounds present in the leaf of Waltheria indica Linn.
by Gas Chromatography-… Waltheria indica L. is a highly valued medicinal
plant used to treat the cancer 3, 4, malaria 5 …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Analgesic activity of Waltheria indica Linn
</td>
<td style="text-align:left;">
Z Mohammed, M Shok, N Ilyas, KY Musa
</td>
<td style="text-align:left;">
2005
</td>
<td style="text-align:right;">
27
</td>
<td style="text-align:left;">
The analgesic effects and the acute toxicity studies of the aqueous
extracts of the leaves, stem and root of Waltheria indica Linn were
studied. The analgesic activity was carried out …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Evaluation of anticataract potential of Waltheria indica
in albino rats
</td>
<td style="text-align:left;">
M Atif, M Azharuddin, SA Rahman, MI Ahmed
</td>
<td style="text-align:left;">
2014
</td>
<td style="text-align:right;">
13
</td>
<td style="text-align:left;">
… the Anticataract activity of Waltheria indica. The ability of
Waltheria indica to tweak the … Rats in the test gathering were
sustained orally with 100mg/kg and 200mg/kg of Waltheria …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Control of root‐knot nematodes using Waltheria indica producing
4‐quinolone alkaloids
</td>
<td style="text-align:left;">
JY Jang, Q Le Dang, GJ Choi, HW Park
</td>
<td style="text-align:left;">
2019
</td>
<td style="text-align:right;">
34
</td>
<td style="text-align:left;">
BACKGROUND Waltheria indica exhibited strong nematocidal activity
against Meloidogyne incognita, a causal agent of root‐knot nematode
disease. This study aimed to characterize …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Chemical constituents from Waltheria indica exert in vitro activity
against Trypanosoma brucei and T. cruzi
</td>
<td style="text-align:left;">
S Cretton, L Bréant, L Pourrez, C Ambuehl, R Perozzo
</td>
<td style="text-align:left;">
2015
</td>
<td style="text-align:right;">
54
</td>
<td style="text-align:left;">
Six extracts from the roots and the aerial parts of Waltheria indica L.
(Malvaceae) were screened for their in vitro antitrypanosomal activity
towards Trypanosoma brucei brucei STIB 427 …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Phytochemical study of Waltheria viscosissima and
evaluation of its larvicidal activity against Aedes aegypti
</td>
<td style="text-align:left;">
MDL Ferreira, DA Fernandes, FC Nunes
</td>
<td style="text-align:left;">
2019
</td>
<td style="text-align:right;">
24
</td>
<td style="text-align:left;">
The species Waltheria viscosissima A.St.–Hil, Malvaceae, which is known
as ‘malva-branca’, is traditionally used in the Brazilian northeast for
the treatment of coughs. This research …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Anti-inflammatory activities of Waltheria indica extracts by modulating
expression of IL-1B, TNF-α, TNFRII and NF-κB in human macrophages
</td>
<td style="text-align:left;">
R Laczko, A Chang, L Watanabe, M Petelo
</td>
<td style="text-align:left;">
2020
</td>
<td style="text-align:right;">
20
</td>
<td style="text-align:left;">
… ‘uhaloa, Waltheria indica var. Americana is one of the most recognized
plants. Waltheria is also … supported anti-inflammatory activity for the
Waltheria flavonoid quercetin, and for crude …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Broad-spectrum antifungal activity of dichloromethane extract of
Waltheria indica stems and isolated compounds
</td>
<td style="text-align:left;">
C Liang, L Yang, Y Shao, X Zhu, H Zhao
</td>
<td style="text-align:left;">
2019
</td>
<td style="text-align:right;">
20
</td>
<td style="text-align:left;">
… of stems of Waltheria indica L. … Waltheria indica stems could
effectively control the development of eleven species of phytopathogenic
fungi in vivo. These results suggest that Waltheria …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Antioxidant, analgesic and anti-inflammatory activities
of the leafy stems of Waltheria indica L.(Sterculiaceae)
</td>
<td style="text-align:left;">
MN YOUGBARE-ZIEBROU, M LOMPO
</td>
<td style="text-align:left;">
2016
</td>
<td style="text-align:right;">
38
</td>
<td style="text-align:left;">
… Aim of the study: The existence of numerous side effects following the
use of anti inflammatory drugs has led to the present study about
Waltheria indica (Sterculiaceae), prescribed in …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Chemical composition of four different species of the Waltheria genus
</td>
<td style="text-align:left;">
TNS Caridade, RD Araújo, ANA Oliveira
</td>
<td style="text-align:left;">
2018
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:left;">
This study aimed the phytochemical investigation of four different
species of the genus Waltheria (W. douradinha, W. ferruginea, W. indica
and W. cinerencens), collected in the …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
\[BOOK\]\[B\] Systematics and Evolution of Waltheria (Sterculiaceae:
Hermannieae)
</td>
<td style="text-align:left;">
JG Saunders
</td>
<td style="text-align:left;">
1995
</td>
<td style="text-align:right;">
24
</td>
<td style="text-align:left;">
This dissertation provides a monograph of the primarily distylous genus
Waltheria (Sterculiaceae: Hermannieae). Waltheria is readily
distinguished from Melochia and Dicarpidium by …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
A new cyclopeptide alkaloid from the bark of Waltheria douradinha
</td>
<td style="text-align:left;">
AF Morel, A Flach, N Zanatta, EM Ethur
</td>
<td style="text-align:left;">
1999
</td>
<td style="text-align:right;">
34
</td>
<td style="text-align:left;">
Waltherine-C (5), a novel 14-membered cyclopeptide alkaloid has been
isolated, together with four known cyclopeptide alkaloids, waltherine-A
(1) and -B (2), scutianine-B (3) and …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Antidiabetic and antioxidant properties of Waltheria
indica L., an ethnomedicinal plant
</td>
<td style="text-align:left;">
M Kannan, TS Kumar, MV Rao
</td>
<td style="text-align:left;">
2016
</td>
<td style="text-align:right;">
19
</td>
<td style="text-align:left;">
The present investigation was carried out to study the antidiabetic
activity of methanolic extract of entire plant of Waltheria indica L.
and its effect on the lipid profile, antioxidant …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Polyphenolic components of Waltheria indica.
</td>
<td style="text-align:left;">
AJA Petrus
</td>
<td style="text-align:left;">
1990
</td>
<td style="text-align:right;">
15
</td>
<td style="text-align:left;">
Eight new constituents of the aerial parts are reported for this
medicinal species. They include kaempferol (0.002%),
kaempferol-3-O-β-smallcapD-galactoside (0.018%), and …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
\[CITATION\]\[C\] Antifungal flavonoids from Waltheria americana
</td>
<td style="text-align:left;">
CY Ragasa, CA Cruz, ID Chiong, M Tada, JA Rideout
</td>
<td style="text-align:left;">
1997
</td>
<td style="text-align:right;">
20
</td>
<td style="text-align:left;">
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Pollen morphology of Waltheria L.
(Malvaceae-Byttnerioideae) from Bahia, Brazil
</td>
<td style="text-align:left;">
CEA Silveira, LCL Lima, MD Saba
</td>
<td style="text-align:left;">
2017
</td>
<td style="text-align:right;">
15
</td>
<td style="text-align:left;">
… Waltheria is characterized morphologically by a … (Waltheria, Pringley
and Virgata) and two sections Waltheria and … We selected 14 Waltheria
species listed as occurring in the State of …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Phytochemical screening, antioxidant activity and analgesic effect of
Waltheria ovata Cav. roots in mice
</td>
<td style="text-align:left;">
O Herrera-Calderon, E Enciso-Roca
</td>
<td style="text-align:left;">
2016
</td>
<td style="text-align:right;">
23
</td>
<td style="text-align:left;">
Phytochemical screening, antioxidant activity and analgesic effect of
Waltheria ovata Cav. … and analgesic effect of crude ethanol extract of
Waltheria ovata (W. ovata) Cav. root in mice. …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Novelties in Brazilian Waltheria L. (Byttnerioideae,
Malvaceae): two new species and one re-establishment
</td>
<td style="text-align:left;">
TS Coutinho, M Colli-Silva, M Alves
</td>
<td style="text-align:left;">
2020
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
… This work validates two Waltheria species endemic to campo … Waltheria
biribiriensis and Waltheria terminans are restricted … Furthermore,
Waltheria brachypetala, a species of dry …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Pollen morphology and exine ultrastructure of selected species of
Waltheria L.(Byttnerioideae-Malvaceae)
</td>
<td style="text-align:left;">
MD Saba, FAR dos Santos
</td>
<td style="text-align:left;">
2015
</td>
<td style="text-align:right;">
18
</td>
<td style="text-align:left;">
… In Malvaceae, heterostyly is common in the genera Melochia and
Waltheria (Byttnerioideae), … species of Waltheria (Waltheria albicans
Turcz., Waltheria brachypetala Turcz., Waltheria …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Acetylcholinesterase activity of alkaloids from the leaves of Waltheria
brachypetala
</td>
<td style="text-align:left;">
MMC Lima, JA López, JM David, EP Silva
</td>
<td style="text-align:left;">
2009
</td>
<td style="text-align:right;">
16
</td>
<td style="text-align:left;">
… genera, Waltheria and Melochia. W. douradinha \[1\], and W. … was
first reported from Melochia tomentosa which afforded a 4-… of
melochinone was isolated from Waltheria douradinha \[1\], M…
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
The genus Waltheria in southern Africa
</td>
<td style="text-align:left;">
IC Verdoorn
</td>
<td style="text-align:left;">
1981
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:left;">
Waltheria indica L., the only species of Waltheria represented in
southern Africa, is revised. This species, which occurs throughout the
tropics and substropics of the world, is found …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] A Nomenclatural Conspectus Of Waltheria L.(Malvaceae,
Bytterioideae) In Brazil
</td>
<td style="text-align:left;">
JG Saunders, LJ Dorr
</td>
<td style="text-align:left;">
2022
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
… Of these three we consider one to be a species of Waltheria, one a
species … Waltheria that he described from other American countries are
considered here to be synonyms of Waltheria …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Anti-inflammatory Quinoline Alkaloids from the Roots of Waltheria indica
</td>
<td style="text-align:left;">
F Liu, TJ O’Donnell, EJ Park, S Kovacs
</td>
<td style="text-align:left;">
2023
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
… An example is Waltheria indica (Malvaceae), also known as ‘uhaloa or
Waltheria indica var. americana in Hawaii. (5,6) In Hawaiian traditional
medicinal practices, ‘uhaloa is one of the …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Antibacterial and antioxidant activity of the extracts of
Waltheria indica Linn. collected from Capricorn District, Limpopo
Province, South Africa
</td>
<td style="text-align:left;">
NI Mongalo, AR Opoku, AM Zobolo
</td>
<td style="text-align:left;">
2012
</td>
<td style="text-align:right;">
25
</td>
<td style="text-align:left;">
Herbal medicines still remain the mainstay of about 75 to 80% of the
whole population in developing countries, for primary health care
because of cultural acceptability (Parekh and …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Characterization of cellulosic plant fiber extracted from Waltheria
indica Linn. stem
</td>
<td style="text-align:left;">
GS Priyadharshini, T Velmurugan
</td>
<td style="text-align:left;">
2023
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
… The Waltheria indica Linnaeus plant is a common fiber-producing plant
in Asia and Africa. It … obtained from Waltheria indica Linnaeus stems.
In this work, Waltheria indica Linnaeus stem …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Cyclopeptide alkaloids from the bark of Waltheria douradinha
</td>
<td style="text-align:left;">
AF Morel, ITS Gehrke, MA Mostardeiro, EM Ethur
</td>
<td style="text-align:left;">
1999
</td>
<td style="text-align:right;">
28
</td>
<td style="text-align:left;">
… Two peptide alkaloids, waltherine-A and waltherine-B were isolated
from Waltheria douradinha, together with two known peptide alkaloids,
adouetine-Y′ and scutianine-B …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Four new distylous species of Waltheria (Sterculiaceae) and a key to the
Mexican and Central American species and species groups
</td>
<td style="text-align:left;">
JG Saunders
</td>
<td style="text-align:left;">
1993
</td>
<td style="text-align:right;">
25
</td>
<td style="text-align:left;">
… Four new distylous species of Waltheria endemic to Mexico … Mexican
and Central American Waltheria species. Three … Waltheria is a primarily
neotropical genus of approximately 50 …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Identification of plant metabolite classes from Waltheria Indica L.
extracts regulating inflammatory immune responses via COX-2 inhibition
</td>
<td style="text-align:left;">
M Termer, C Carola, A Salazar, CM Keck
</td>
<td style="text-align:left;">
2021
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:left;">
Ethnopharmacological relevance Waltheria Indica L. is traditionally used
in Africa, South America and Hawaii to treat pain, anemia, diarrhea,
epilepsy and inflammatory related diseases…
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Pharmacological Evaluation of the Bronchorelaxant
Effect of Waltheria indica L. (Malvaceae) Extracts on Rat Trachea
</td>
<td style="text-align:left;">
R Boly, Z Yabre, M Nitiema, B Yaro, J Yoda
</td>
<td style="text-align:left;">
2021
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:left;">
Waltheria indica L. (Malvaceae) is a plant used in Burkina Faso for the
treatment of various ailments including asthma. The aim of the study was
to evaluate the pharmacological …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Coumarinolignans with Reactive Oxygen Species (ROS) and
NF-κB Inhibitory Activities from the Roots of Waltheria indica
</td>
<td style="text-align:left;">
F Liu, S Mallick, TJ O’Donnell, R Rouzimaimaiti, Y Luo
</td>
<td style="text-align:left;">
2022
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
Seven new coumarinolignans, walthindicins A–F (1a, 1b, 2–5, 7), along
with five known analogs (6, 8–11), were isolated from the roots of
Waltheria indica. The structures of the new …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Phytochemical screening and in vitro antimicrobial
activity of Waltheria indica Linn leaf extracts
</td>
<td style="text-align:left;">
OS Koma, OA Fatokun, OA Theophilus
</td>
<td style="text-align:left;">
2017
</td>
<td style="text-align:right;">
10
</td>
<td style="text-align:left;">
The plant, Waltherica indica Linn is known to possess medicinal
properties according to African folklore. Scientific verification of its
bioactive constituents backing its use for the …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Effects of aqueous extract of Waltheria indica leaves on
blood profile of male albino rats
</td>
<td style="text-align:left;">
A Basiru, FO Olayemi
</td>
<td style="text-align:left;">
2014
</td>
<td style="text-align:right;">
10
</td>
<td style="text-align:left;">
This study was to investigate the effect of oral administration of
aqueous extract of Waltheria indica leaf on the blood profile of male
albino rats. The extract showed significantly (p lt; 0.01…
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Antimicrobial efficacy of biosynthesized silver
nanoparticles from different solvent extracts of Waltheria americana
root
</td>
<td style="text-align:left;">
JJ Deshi, JT Barminas, JC Onwuka, PM Dass
</td>
<td style="text-align:left;">
2016
</td>
<td style="text-align:right;">
13
</td>
<td style="text-align:left;">
Background The extensive application of silver compounds especially in
nanomedicine, has increased the need to develop environmental friendly
and cost effective route to …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Muscarinic agonist properties of the hydrobutanol extract from aerial
parts of Waltheria viscosissima St. Hil. (Sterculiaceae) in Rats†
</td>
<td style="text-align:left;">
CAR Vasques, SF Côrtes, MS Silva
</td>
<td style="text-align:left;">
1999
</td>
<td style="text-align:right;">
18
</td>
<td style="text-align:left;">
The cardiovascular effects of the hydrobutanol phase of the ethanolíc
extract from the aerial parts (HBWV) of Waltheria viscosissima A.
St. Hil. (Sterculiaceae) were tested in rats by …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
Antinociceptive, anti-inflammatory and antioxidant activities of the
crude ethanolic extract and alkaloid fraction of Waltheria viscosissima
A. St.-Hil.(Malvaceae)
</td>
<td style="text-align:left;">
CCSD Viegas, AS Silva, RM Braga
</td>
<td style="text-align:left;">
2022
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
Ethnopharmacological relevance The Waltheria viscosissima A. St.- Hil
(Malvaceae) is also known as ‘Malva branca’, has been reported as
ethnopharmacologically useful plant …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] New species of Waltheria (Hermannieae, Byttnerioideae,
Malvaceae) from Paraguay, Argentina, and Venezuela, and two new records
for Paraguay
</td>
<td style="text-align:left;">
JG Saunders
</td>
<td style="text-align:left;">
2005
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
… Both species are assigned to Waltheria sect. Waltheria Linnaeus as
presently circumscribed and within this section, are allied to the
species described as having elongate-plumose (…
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Waltheria” OR “Hermannia” OR “Melochia” OR “Hannafordia” OR
“Maxwellia”
</td>
<td style="text-align:left;">
\[HTML\]\[HTML\] Distribution and frequency of galls induced by
Anisodiplosis waltheriae Maia (Diptera: Cecidomyiidae) on the invasive
plant Waltheria indica L.(Sterculiaceae)
</td>
<td style="text-align:left;">
FVM Almeida, JC Santos, FAO Silveira
</td>
<td style="text-align:left;">
2006
</td>
<td style="text-align:right;">
17
</td>
<td style="text-align:left;">
The frequency of galls induced by Anisodiplosis waltheriae Maia, a
recently described species, on Waltheria indica L. was studied. W.
indica is an invasive weed in regeneration areas …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
New chromosome numbers for Lasiopetaleae: Malvaceae sl (or
Sterculiaceae)
</td>
<td style="text-align:left;">
CF Wilkins, JA Chappill
</td>
<td style="text-align:left;">
2002
</td>
<td style="text-align:right;">
11
</td>
<td style="text-align:left;">
… of Keraudrenia and Thomasia match … for Thomasia angustifolia which is
interpreted as a polyploid number. New counts of n = 10 are recorded for
species of Rulingia and Commersonia, …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Inflorescence morphology of some Australian Lasiopetaleae
(Sterculiaceae)
</td>
<td style="text-align:left;">
C Bayer, K Kubitzki
</td>
<td style="text-align:left;">
1996
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:left;">
… between Keraudrenia and Commersonia because of similarities in the
structure of the gynoecium; according to him, Thomasia, Guichenotia and
Lasiopetalum are farther derived …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
Quality retention and shelf life extension of fresh beef using Lepidium
sativum seed mucilage-based edible coating containing Heracleum
lasiopetalum essential oil …
</td>
<td style="text-align:left;">
H Barzegar, B Alizadeh Behbahani
</td>
<td style="text-align:left;">
2020
</td>
<td style="text-align:right;">
63
</td>
<td style="text-align:left;">
The instability and strong flavor or odor of essential oils (EO) limit
their direct incorporation into food products. In this study, the
antioxidant and antimicrobial Heracleum lasiopetalum …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
Polyphyly of Rulingia and Commersonia (Lasiopetaleae, Malvaceae sl)
</td>
<td style="text-align:left;">
BA Whitlock, AM Hale, JL Indorf
</td>
<td style="text-align:left;">
2011
</td>
<td style="text-align:right;">
8
</td>
<td style="text-align:left;">
… three species referable to Commersonia and 17 to Rulingia and Clade 2
includes 20 species … to Commersonia and six to Rulingia. Type species
of both Commersonia and Rulingia are …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
Ferulago angulata and Tetrataenium lasiopetalum: Essential oils
composition and antibacterial activity of the oils and extracts
</td>
<td style="text-align:left;">
H Mumivand, A Aghemiri, A Aghemiri
</td>
<td style="text-align:left;">
2019
</td>
<td style="text-align:right;">
20
</td>
<td style="text-align:left;">
In the present research, the chemical composition of the essential oils
from Ferulago angulata and Tetrataenium lasiopetalum were analyzed by
the gas chromatography-mass …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
Corrigendum to: A revision of Commersonia including Rulingia (Malvaceae
sl or Byttneriaceae)
</td>
<td style="text-align:left;">
CF Wilkins, BA Whitlock
</td>
<td style="text-align:left;">
2012
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:left;">
… resulted in two major groupings, referred to a redefined Commersonia
and a new genus (… species of Rulingia, and 14 species previously
included in Rulingia and 10 in Commersonia. …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
Volatile constituents and antimicrobial activity of the essential oil of
Tetrataenium lasiopetalum (Apiaceae) from Iran
</td>
<td style="text-align:left;">
A Sonboli, D Azizian, M Yousefzadi
</td>
<td style="text-align:left;">
2007
</td>
<td style="text-align:right;">
33
</td>
<td style="text-align:left;">
The aerial parts of Tetrataenium lasiopetalum were collected from
Oshtoran Kuh (Lorestan province) of Iran and its volatile oil was
obtained by hydrodistillation and analysed by GC and …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
Konsep Perlindungan Hukum Terhadap Wali Amanat di Pasar Modal
</td>
<td style="text-align:left;">
…, F Lovano, E Elisah, G Thomasia
</td>
<td style="text-align:left;">
2019
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
Wali amanat berperan sebagai pihak yang mewakili kepentingan pemegang
obligasi Emiten setelah dinyatakan efektif pernyataan pendaftarannya
mempunyai kewajiban untuk selalu …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
On-line (HPLC-NMR) and Off-line Phytochemical Profiling of the
Australian Plant, Lasiopetalum macrophyllum
</td>
<td style="text-align:left;">
M Timmers, S Urban
</td>
<td style="text-align:left;">
2012
</td>
<td style="text-align:right;">
30
</td>
<td style="text-align:left;">
On-line (HPLC-NMR) and off-line (HPLC, NMR and MS) methodologies were
used to profile the constituents present in the crude extract of
Lasiopetalum macrophyllum. On-flow and …
</td>
</tr>
<tr>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
Evaluation of antioxidant properties of Heracleum Lasiopetalum extract
in multilayer nanoemulsion with biopolymer coating to control oxidative
stability of sunflower …
</td>
<td style="text-align:left;">
M Yazdan-Bakhsh, M Nasr-Esfahani
</td>
<td style="text-align:left;">
2021
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:left;">
The antioxidant potential of Heracleum Lasiopetalum extract (GE) as a
natural antioxidant in multilayer nanoemulsion was measured. GE obtained
using an ultrasonic bath. Extraction …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Taxonomic studies in Thomasia and Lasiopetalum
(Sterculiaceae)
</td>
<td style="text-align:left;">
S Paust
</td>
<td style="text-align:left;">
1974
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
… Thomqsict tremandroides is distinguished from other species of
Thomasia by its small … Lasiopetalum linesre has floral characteristics
similar to those of L. dtum’ mondii Benth., but …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
Antioxidant activity of different parts of Tetrataenium lasiopetalum
</td>
<td style="text-align:left;">
MM Dehshiri, H Aghamollaei, M Zarini
</td>
<td style="text-align:left;">
2013
</td>
<td style="text-align:right;">
19
</td>
<td style="text-align:left;">
… lasiopetalum were collected, dried and then extracted by ethanol and
water (70:30). … lasiopetalum flower showed the highest activity in
scavenging of DPPH (IC 50 = 170 ± 7 μg/mL). In …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
A new Australian genus, Androcalva, separated from Commersonia
(Malvaceae sl or Byttneriaceae)
</td>
<td style="text-align:left;">
CF Wilkins, BA Whitlock
</td>
<td style="text-align:left;">
2011
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:left;">
… Commersonia, four species formerly described as Rulingia … Rulingia
pauciflora Turcz. is here placed in synonymy with C. … that neither
Rulingia nor Commersonia as previously …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] A revision of the Lasiopetalum floribundum group
(Malvaceae), including recognition of four new species
</td>
<td style="text-align:left;">
KA Shepherd, CF Wilkins
</td>
<td style="text-align:left;">
2017
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
… This revision focuses on Lasiopetalum floribundum Benth. and six
morphologically allied Western Australian species. This group is
characterised by loose dichasial or monochasial …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
Analysis of anticancer activity and chemical sensitization effects of
Dendropanax morbifera and Commersonia bartramia extracts
</td>
<td style="text-align:left;">
S Kim, SG Park, YJ Song, JK Park, CH Choi
</td>
<td style="text-align:left;">
2018
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:left;">
Background/Aim: Dendropanax morbifera (DM) and Commersonia bartramia
(CB) are possible candidates for immunotherapy. In this study, the
cytotoxicity and chemical sensitization of …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
New species of Commersonia JR Forst. & G. Forst.(Sterculiaceae) from
eastern Australia and Vanuatu
</td>
<td style="text-align:left;">
GP Guymer
</td>
<td style="text-align:left;">
2005
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
… The new subgenus Commersonia subgenus Verticillata Guymer is described
for the species … the genus Rulingia requires reassessment with respect
to its relationship to Commersonia. …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
Development of an in vitro propagation protocol for ex situ conservation
of two critically endangered species of Commersonia (Malvaceae) from
Western Australia
</td>
<td style="text-align:left;">
S Nikabadi, E Bunn, S Turner, J Stevens
</td>
<td style="text-align:left;">
2010
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
Protocols for in vitro propagation of two critically endangered species,
Commersonia adenothalia CFWilkins ms and Commersonia sp. Mt Groper (R.
Cranfield & D. Kabay 9157), from …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
Wildfire reveals new populations of the endangered’Commersonia
rosea’and’Monotaxis macrophylla’in northern Wollemi National Park, NSW
</td>
<td style="text-align:left;">
S Bell, B Holzinger
</td>
<td style="text-align:left;">
2015
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
… Also present within part of the Commersonia population … Commersonia.
We estimate that several hundred plants, perhaps thousands, are present,
although as with the Commersonia …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Volatile and non-volatile phytochemicals from roots and
leaves of Heracleum lasiopetalum and their radical scavenging ability
</td>
<td style="text-align:left;">
E Javidnia, MB Bahadori
</td>
<td style="text-align:left;">
2020
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
Abstract: Heracleum lasiopetalum belongs to the Apiaceae family …
lasiopetalum, aerial parts of the plant were conducted to a …
lasiopetalum contains important natural products such as …
</td>
</tr>
<tr>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Phytochemistry analysis of essential oils of Heracleum
lasiopetalum fruits, Thymus carmanicus aerial parts, and Myrtus communis
leaves
</td>
<td style="text-align:left;">
L Sedaghat Boroujeni, M Hojjatoleslami
</td>
<td style="text-align:left;">
2013
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
… Considering thatHeracleum lasiopetalum, Thymus carmanicus and Myrtus
communis have … lasiopetalum, aerial parts of T. carmanicus and leaves
of M. communis were extracted using …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Commersonia rosea (Malvaceae sl: Lasiopetaleae): a new,
rare fire-ephemeral species from the upper Hunter Valley of New South
Wales
</td>
<td style="text-align:left;">
SAJ Bell, LM Copeland
</td>
<td style="text-align:left;">
2004
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:left;">
Copel., a fire-ephemeral species from the Central Western Slopes of New
South Wales is described as new. Notes on its distribution, ecology and
conservation status are given. The …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
Taxonomic revision of Guichenotia (Lasiopetaleae: Malvaceae sl or
Sterculiaceae)
</td>
<td style="text-align:left;">
CF Wilkins, JA Chappill
</td>
<td style="text-align:left;">
2003
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
… clarification of its relationship to Lasiopetalum. The second clade
will be … Lasiopetalum–Guichenotia clade have been better resolved. A
key is presented for all species of Guichenotia sl …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
New Species of Commersonia JR Forst. & G. Forst.(Sterculiaceae) from
Queensland
</td>
<td style="text-align:left;">
GP Guymer
</td>
<td style="text-align:left;">
2006
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
The new species Commersonia perkinsiana Guymer, C. inglewoodensis Guymer
and C. … , and a key to Queensland species of Commersonia subg.
Commersonia is provided. Notes on …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
\[BOOK\]\[B\] National recovery plan for the dwarf kerrawang Rulingia
prostrata
</td>
<td style="text-align:left;">
O Carter, N Walsh
</td>
<td style="text-align:left;">
2010
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
… The response of Rulingia prostrata to fire is not known. In Victoria,
sites with low population … Further determination of appropriate fire
regimes is needed to adequately manage Rulingia …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
Optimizing nanoencapsulation of Heracleum lasiopetalum by response
surface methodology
</td>
<td style="text-align:left;">
M Yazdan‐Bakhsh, M Nasr‐Esfahani
</td>
<td style="text-align:left;">
2022
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">
This study was carried out to optimize formulation for Heracleum
lasiopetalum (golpar) extract nanoencapsulation by response surface
methodology (RSM). The primary water‐in‐oil …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Typification of Lasiopetalum and an interim key to the
Western Australian species of the genus (Malvaceae: Byttnerioideae)
</td>
<td style="text-align:left;">
KA Shepherd, CF Wilkins
</td>
<td style="text-align:left;">
2018
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
… Lasiopetalum ferrugineum Sm. is lectotypified and selected as the type
for Lasiopetalum Sm… (and similar to hairs in the closely related genus
Thomasia J.Gay, some species of which …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] A revision of Lasiopetalum (Malvaceae: Byttnerioideae)
from the northern sandplains of Western Australia, including two new
species
</td>
<td style="text-align:left;">
KA Shepherd, CF Wilkins
</td>
<td style="text-align:left;">
2021
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
… This paper revises a small group of Lasiopetalum centred around this
region, which have not already been treated in our ongoing revision of
this genus. These species have non-…
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
Using hierarchies of cause to inform conservation of a naturally rare
but critically endangered shrub Lasiopetalum pterocarpum (Malvaceae sl)
</td>
<td style="text-align:left;">
CF Wilkins, PG Ladd, BJ Vincent
</td>
<td style="text-align:left;">
2009
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
Understanding the causes of rarity and ways of managing populations of
rare species is essential for their successful conservation. The present
study applies the conceptual model of a …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] A taxonomic revision of species with a petaloid epicalyx
bract allied to Lasiopetalum bracteatum (Malvaceae: Byttnerioideae)
</td>
<td style="text-align:left;">
KA Shepherd, CF Wilkins
</td>
<td style="text-align:left;">
2018
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
… This treatment focuses on an informal group of six species where
Lasiopetalum bracteatum (… Lasiopetalum membranaceum may be
distinguished from other species in the genus with …
</td>
</tr>
<tr>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Lasiopetalum adenotrichum (Malvaceae s. lat.), a new
species from Fitzgerald River National Park
</td>
<td style="text-align:left;">
RA Meissner, DA Rathbone, CF Wilkins
</td>
<td style="text-align:left;">
2014
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
… Lasiopetalum adenotrichum is recorded as flowering from … Lasiopetalum
adenotrichum is known only from scattered … Lasiopetalum adenotrichum
also has leaf bases that are …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
Chemical Constituents and Biological Activities of Commersonia bartramia
Stems.
</td>
<td style="text-align:left;">
KC Nhung, TT Bach, VT Hue, BT Ha
</td>
<td style="text-align:left;">
2023
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">
Commersonia bartramia (Sterculiaceae family) is a wooden tree, widely
occurring in Australia, China and South East Asia. The MeOH extract of
C. bartramia has been reported to show …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Thomasia julietiae (Malvaceae: Byttnerioideae), a
delicate new species with horticultural potential from south-western
Australia
</td>
<td style="text-align:left;">
KA Shepherd, CF Wilkins
</td>
<td style="text-align:left;">
2020
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
This poorly known but highly attractive new species belongs to the
unusual Thomasiastelligera (Turcz.) Benth. species group, which is
defined by an absence of stipules (a feature …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
Antioxidant activity of essential oil of Heracleum lasiopetalum fruits
on chemical properties of potato chips
</td>
<td style="text-align:left;">
L Sedaghat Boroujeni, M Hojjatoleslami
</td>
<td style="text-align:left;">
2013
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
Background & Aim: Chemicals antioxidants and lipid oxidation products
are known as cancer-causing factors in food systems. In this study, the
effect of essential oil from Heracleum …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
Lasiopetalum pterocarpum (Malvaceae sl: Lasiopetaleae), a new and rare
species from south-west Western Australia
</td>
<td style="text-align:left;">
K Shepherd, E Bennett, C Wilkins, L Sage
</td>
<td style="text-align:left;">
2006
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:left;">
Shepherd, KA, Bennett, EM, Wilkins, CF and Sage, LW Lasiopetalum
pterocarpum (Malvaceaes. l.: Lasiopetaleae), a new and rare species from
south-west Western Australia. Nuytsia …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] Persistence pays off: resolution of Lasiopetalum
hapalocalyx (Malvaceae: Byttnerioideae), a new species from
south-western Australia
</td>
<td style="text-align:left;">
KA Shepherd, CF Wilkins
</td>
<td style="text-align:left;">
2020
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
This new species was first collected in 1995 near Cape Riche on the
south coast of Western Australia but its taxonomic status has remained
unresolved, in part due to a lack of …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
Palynological studies of the genus Tetrataenium (Apiaceae) from Iran
</td>
<td style="text-align:left;">
M Yousefzadi, D Azizian, A Sonboli
</td>
<td style="text-align:left;">
2006
</td>
<td style="text-align:right;">
16
</td>
<td style="text-align:left;">
… The pollen morphology of Tetrataenium lasiopetalum and T.
nephrophyllum was studied by … lasiopetalum and T. nephrophyllum are
similar in palynological characters and could be …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
JURIDICAL REVIEW OF BANKRUPTCY IN SIGNING AGREEMENT ON BUYING SELLING OF
FLAT IN MEDAN
</td>
<td style="text-align:left;">
…, A Azharuddin, G Thomasia
</td>
<td style="text-align:left;">
2022
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">
Flats as a place to live with the hope of meeting the needs of the
community, especially those who live in cities, there is no reduction in
excessive land use and creating infiltration areas …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] A new species of Commersonia (Malvaceae sl), from the
Eyre Peninsula, South Australia.
</td>
<td style="text-align:left;">
CF Wilkins, BA Whitlock
</td>
<td style="text-align:left;">
2005
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:left;">
… Rulingia is currently delimited from Commersonia by having one rather
than three staminodes between the stamens. However, taxonomic confusion
between the two genera …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
Guichenotia anota and Guichenotia apetala (Lasiopetaleae: Byttneriaceae
or Malvaceae s. lat.) a new and revised species endemic to the
Ravensthorpe Range …
</td>
<td style="text-align:left;">
C Wilkins, BA Whitlock
</td>
<td style="text-align:left;">
2009
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:left;">
… Guichenotia anota CF Wilkins is described as new and Guichenotia
apetala AS George is … be most closely related to each other, and more
closely related to Lasiopetalum Sm. than to …
</td>
</tr>
<tr>
<td style="text-align:right;">
4
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
\[PDF\]\[PDF\] TRIGWELL’S RULINGIA
</td>
<td style="text-align:left;">
G Stack, R Evans, V English
</td>
<td style="text-align:left;">
1999
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">
… KPBG have had success with the propagation of Rulingia sp. Trigwell
Bridge through tissue culture, cuttings, and grafting, and the
transition into soil has been successful. … • Lack …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
Corrigendum to: A new Australian genus Androcalva separated from
Commersonia (Malvaceae sl or Byttneriaceae)
</td>
<td style="text-align:left;">
CF Wilkins, BA Whitlock
</td>
<td style="text-align:left;">
2012
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">
… as Rulingia and six species recently described as Commersonia. The
taxonomic history of Commersonia, Rulingia and Androcalva is described
in the revision of Commersonia (Wilkins …
</td>
</tr>
<tr>
<td style="text-align:right;">
5
</td>
<td style="text-align:left;">
intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR “Commersonia” OR
“Rulingia”
</td>
<td style="text-align:left;">
Thomasia, Minoress
</td>
<td style="text-align:left;">
T Charlton
</td>
<td style="text-align:left;">
T Charlton - cambridge.org
</td>
<td style="text-align:right;">
NA
</td>
<td style="text-align:left;">

1.  The Fraunceys family \| Royal Historical Society Camden Fifth Series
    \| Cambridge Core Skip to main content Accessibility help We use
    cookies to distinguish you from other users and to …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR
    “Commersonia” OR “Rulingia”
    </td>
    <td style="text-align:left;">
    \[PDF\]\[PDF\] Two new species of Commersonia (Malvaceae sensu lato)
    from south-eastern Australia
    </td>
    <td style="text-align:left;">
    CF Wilkins, LM Copeland, BA Whitlock
    </td>
    <td style="text-align:left;">
    2008
    </td>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    … Results of additional unpublished morphological and molecular
    phylogenetic analyses (Wilkins & Whitlock in prep.), with near
    exhaustive sampling of species in both genera …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR
    “Commersonia” OR “Rulingia”
    </td>
    <td style="text-align:left;">
    \[PDF\]\[PDF\] Many-flowered Commersonia (Commersonia apella)
    </td>
    <td style="text-align:left;">
    IR Plan
    </td>
    <td style="text-align:left;">
    2016
    </td>
    <td style="text-align:right;">
    NA
    </td>
    <td style="text-align:left;">
    … Commersonia apella was first collected at Big Brook, Pemberton by
    Max Koch in 1919 … Rulingia corylifolia (now Commersonia
    corylifolia). However, it was later placed in Commersonia …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR
    “Commersonia” OR “Rulingia”
    </td>
    <td style="text-align:left;">
    \[PDF\]\[PDF\] should be cited as: Department of Environment and
    Conservation (2009) Guichenotia seorsiflora Interim Recovery Plan
    2009-2014
    </td>
    <td style="text-align:left;">
    IRP This
    </td>
    <td style="text-align:left;">
    IRP This - Interim Recovery Plan No.\#. Department of … -
    library.dbca.wa.gov.au
    </td>
    <td style="text-align:right;">
    NA
    </td>
    <td style="text-align:left;">
    … the habitat of Guichenotia seorsiflora will also improve the
    status of associated native vegetation, including a number of
    Priority flora. International obligations: Guichenotia seorsiflora
    is …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR
    “Commersonia” OR “Rulingia”
    </td>
    <td style="text-align:left;">
    THE APPLICATION OF PLANT TISSUE CULTURE IN THE PROPAGATION OF
    Guichenotia macrantha Turcz.
    </td>
    <td style="text-align:left;">
    Z Zulkarnain
    </td>
    <td style="text-align:left;">
    2017
    </td>
    <td style="text-align:right;">
    NA
    </td>
    <td style="text-align:left;">
    Guichenotia macrantha Turcz. is an important woody ornamental
    species of Western Australia. This species is commonly grown as a
    cut flower, container or rockery plant and, is …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR
    “Commersonia” OR “Rulingia”
    </td>
    <td style="text-align:left;">
    Phytochemistry analysis of essential oils of Heracleum lasiopetalum
    fruits, Thymus carmanicus aerial parts, and Myrtus communis leaves
    </td>
    <td style="text-align:left;">
    </td>
    <td style="text-align:left;">
    صداقت بروجنی, حجت الاسلامی, قاسمی پیربلوطی, عبدالله… - مجله گیاهان
    دارویی - sanad.iau.ir
    </td>
    <td style="text-align:right;">
    2
    </td>
    <td style="text-align:left;">
    … Considering thatHeracleum lasiopetalum, Thymus carmanicus and
    Myrtus communis have … lasiopetalum, aerial parts of T. carmanicus
    and leaves of M. communis were extracted using …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR
    “Commersonia” OR “Rulingia”
    </td>
    <td style="text-align:left;">
    Antioxidant activity of essential oil of Heracleum lasiopetalum
    fruits on chemical properties of potato chips
    </td>
    <td style="text-align:left;">
    </td>
    <td style="text-align:left;">
    صداقت بروجنی, حجت الاسلامی - مجله گیاهان دارویی - sanad.iau.ir
    </td>
    <td style="text-align:right;">
    NA
    </td>
    <td style="text-align:left;">
    … effect of essential oil from Heracleum lasiopetalum fruits on
    lipid … lasiopetalum extracted using a Clevenger-type apparatus, …
    lasiopetalum fruits could reduce oxidation of potato chips the …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR
    “Commersonia” OR “Rulingia”
    </td>
    <td style="text-align:left;">
    A key to the species of Thomasia (Malvaceae: Byttnerioideae).
    </td>
    <td style="text-align:left;">
    CF Wilkins, KA Shepherd
    </td>
    <td style="text-align:left;">
    2019
    </td>
    <td style="text-align:right;">
    NA
    </td>
    <td style="text-align:left;">
    Thomasia (Malvaceae) is predominantly Western Australian; only T.
    petalocalyx has populations that extend to South Australia and
    Victoria. There are 31 formally named species in the …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    5
    </td>
    <td style="text-align:left;">
    intext:“Lasiopetalum” OR “Thomasia” OR “Guichenotia” OR
    “Commersonia” OR “Rulingia”
    </td>
    <td style="text-align:left;">
    \[PDF\]\[PDF\] Chemical composition and antioxidant activity of
    essential oils of three endemic medicinal plants of Iran
    </td>
    <td style="text-align:left;">
    AG Pirbalouti, L Sedaghat, B Hamedi
    </td>
    <td style="text-align:left;">
    2013
    </td>
    <td style="text-align:right;">
    27
    </td>
    <td style="text-align:left;">
    … lasiopetalum. As a determined by GC and GC–MS analyses, H.
    lasiopetalum contained 2… lasiopetalum. (E)anethole has been
    characterized as the main constituent of the leaves, …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
    “Ayenia” OR “Rayleya”
    </td>
    <td style="text-align:left;">
    The phylogeny of Ayenia, Byttneria, and Rayleya (Malvaceae sl) and
    its implications for the evolution of growth forms
    </td>
    <td style="text-align:left;">
    BA Whitlock, AM Hale
    </td>
    <td style="text-align:left;">
    2011
    </td>
    <td style="text-align:right;">
    19
    </td>
    <td style="text-align:left;">
    … of Byttneria. If upheld, these results have implications for the
    evolution of the floral characters used to separate Ayenia and
    Byttneria, … phylogenetic analyses of Ayenia and Byttneria, with …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
    “Ayenia” OR “Rayleya”
    </td>
    <td style="text-align:left;">
    Systematics, Evolution, and Biogeography of Ayenia (Malvaceae
    Subfamily Byttnerioideae)
    </td>
    <td style="text-align:left;">
    WV Sharber
    </td>
    <td style="text-align:left;">
    2018
    </td>
    <td style="text-align:right;">
    2
    </td>
    <td style="text-align:left;">
    … of Ayenia in relation to its close relatives, Byttneria, Rayleya,
    and Megatritheca, and the parallel evolution of unusual trithecate
    anthers in Ayenia and … history of Ayenia specifically, …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
    “Ayenia” OR “Rayleya”
    </td>
    <td style="text-align:left;">
    … , hypolipidemic and antioxidant properties of combination
    ofCurcumin fromCurcuma longa, Linn, and partially purified product
    fromAbroma augusta, Linn. in …
    </td>
    <td style="text-align:left;">
    HEM Ali Hussain
    </td>
    <td style="text-align:left;">
    2002
    </td>
    <td style="text-align:right;">
    296
    </td>
    <td style="text-align:left;">
    Dietary spice components ofCurcuma longa andAbroma augusta have been
    screened for their protective effect against reactive oxygen species
    induced lipid peroxidation. They have …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
    “Ayenia” OR “Rayleya”
    </td>
    <td style="text-align:left;">
    Seringia (Byttneriaceae/Malvaceae-Byttnerioideae) new to Southeast
    Asia and S. botak endangered in Indonesian New Guinea grassland and
    savannah
    </td>
    <td style="text-align:left;">
    M Cheek, J Wanma, M Jitmau
    </td>
    <td style="text-align:left;">
    2018
    </td>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    … specimen, Cheek 18779, is identified in this paper as a new
    species of Seringia J.Gay … Traditionally, Seringia was regarded as
    monotypic and close to the genus Keraudrenia J.Gay. …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
    “Ayenia” OR “Rayleya”
    </td>
    <td style="text-align:left;">
    \[HTML\]\[HTML\] Abroma augusta L.(Malvaceae) leaf extract
    attenuates diabetes induced nephropathy and cardiomyopathy via
    inhibition of oxidative stress and inflammatory …
    </td>
    <td style="text-align:left;">
    R Khanra, S Dewanjee, TK Dua
    </td>
    <td style="text-align:left;">
    2015
    </td>
    <td style="text-align:right;">
    137
    </td>
    <td style="text-align:left;">
    Abroma augusta L. (Malvaceae) leaf is traditionally used to treat
    diabetes in India and Southern Asia. Therefore, current study was
    performed to evaluate the protective effect of defatted …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
    “Ayenia” OR “Rayleya”
    </td>
    <td style="text-align:left;">
    Lowering of blood sugar by water extract of Azadirachta indica and
    Abroma augusta in diabetes rats
    </td>
    <td style="text-align:left;">
    M Halim
    </td>
    <td style="text-align:left;">
    2003
    </td>
    <td style="text-align:right;">
    100
    </td>
    <td style="text-align:left;">
    Combination (1:1) of water extract of dried powder of root and
    leaves (200 mg/kg body wt) of A. augusta and A. indica respectively
    was administered orally to alloxan diabetic rats once a …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
    “Ayenia” OR “Rayleya”
    </td>
    <td style="text-align:left;">
    Effect ofCoccinia indica (L.) andAbroma augusta (L.) on glycemia,
    lipid profile and on indicators of end-organ damage in
    streptozotocin induced diabetic rats
    </td>
    <td style="text-align:left;">
    MH Eshrat
    </td>
    <td style="text-align:left;">
    2003
    </td>
    <td style="text-align:right;">
    91
    </td>
    <td style="text-align:left;">
    … The present paper reports the combined effect of Abroma augusta
    and … Reported that Abroma augusta possesses not only … extract of a
    mixture Abroma augusta roots and Coccinai …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
    “Ayenia” OR “Rayleya”
    </td>
    <td style="text-align:left;">
    Seringia revised to include Keraudrenia (Lasiopetaleae: Malvaceae
    sl)
    </td>
    <td style="text-align:left;">
    CF Wilkins, BA Whitlock
    </td>
    <td style="text-align:left;">
    2016
    </td>
    <td style="text-align:right;">
    6
    </td>
    <td style="text-align:left;">
    … Four species of Keraudrenia initially described as Seringia and …
    combinations of Keraudrenia and Seringia as Seringia are …
    Keraudrenia collina var. multiflora Domin is placed in …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
    “Ayenia” OR “Rayleya”
    </td>
    <td style="text-align:left;">
    Fucose-containing Abroma augusta mucilage hydrogel as a potential
    probiotic carrier with prebiotic function
    </td>
    <td style="text-align:left;">
    A Roy, M Patra, S Sarkhel, S Sengupta, S Saha, S Jha
    </td>
    <td style="text-align:left;">
    2022
    </td>
    <td style="text-align:right;">
    13
    </td>
    <td style="text-align:left;">
    The mucilaginous polysaccharide from Abroma augusta stem was
    examined for its physicochemical, thermal, and functional behavior
    and explored as a carrier for probiotic bacteria. …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    1
    </td>
    <td style="text-align:left;">
    intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
    “Ayenia” OR “Rayleya”
    </td>
    <td style="text-align:left;">
    Taraxerol, a pentacyclic triterpenoid, from Abroma augusta leaf
    attenuates diabetic nephropathy in type 2 diabetic rats
    </td>
    <td style="text-align:left;">
    R Khanra, N Bhattacharjee, TK Dua, A Nandy
    </td>
    <td style="text-align:left;">
    2017
    </td>
    <td style="text-align:right;">
    52
    </td>
    <td style="text-align:left;">
    … Present study examined the therapeutic potential of taraxerol
    isolated from the methanol extract of Abroma augusta leaf against DN
    using rodent model of type 2 diabetes (T2D). T2D …
    </td>
    </tr>
    <tr>
    <td style="text-align:right;">
    2
    </td>
    <td style="text-align:left;">
    intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
    “Ayenia” OR “Rayleya”
    </td>
    <td style="text-align:left;">
    Insect visitors to extrafloral nectaries of Byttneria aculeata
    (Sterculiaceae): relative importance and roles
    </td>
    <td style="text-align:left;">
    HA Hespenheide
    </td>
    <td style="text-align:left;">
    1985
    </td>
    <td style="text-align:right;">
    71
    </td>
    <td style="text-align:left;">

    1.  Ants, parasitoids and flies are about equally frequent at foliar
        nectaries of Byttneria aculeata (Sterculiaceae) in lowland Costa
        Rica during the dry season, a pattern previously …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        2
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        Green Fabrication of silver nanoparticles by leaf extract of
        Byttneria Herbacea Roxb and their promising therapeutic
        applications and its interesting insightful …
        </td>
        <td style="text-align:left;">
        GK Subramanyam, SA Gaddam
        </td>
        <td style="text-align:left;">
        2023
        </td>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        The present research was carried out to look into therapeutic
        insight of biosynthesized silver nanoparticles (AgNPs) by leaf
        extract of Byttneria herbacea Roxb (BH). The analysis of …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        2
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        (1947-1958) Proposals to reject twelve names emanating from
        Loefling’s Iter Hispanicum (1758), Ayenia sidiformis
        (Malvaceae), Cofer (Symplocaceae), Cruzeta and …
        </td>
        <td style="text-align:left;">
        LJ Dorr, JH Wiersema
        </td>
        <td style="text-align:left;">
        2010
        </td>
        <td style="text-align:right;">
        4
        </td>
        <td style="text-align:left;">
        … Ayenia tomentosa is one of the few species of the genus that
        has a wide geographic range being found in drier areas of
        Venezuela, Guyana, Suriname, Brazil, and the Chaco of Bolivia …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        2
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        \[PDF\]\[PDF\] 1. Isolation and Characterization of Ethyl
        Acetate Fraction from Abroma augusta L as an Anti-Inflammatory
        Agent
        </td>
        <td style="text-align:left;">
        M Latief
        </td>
        <td style="text-align:left;">
        2021
        </td>
        <td style="text-align:right;">
        7
        </td>
        <td style="text-align:left;">
        Artikel Page 1 Artikel by Madyawati Latief Submission date:
        06-Jul-2021 10:41PM (UTC+0700) Submission ID: 1616401255 File
        name: SS20-023\_Tracked\_Proofreading\_2.doc (805.5K) Word …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        2
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        Fabrication of 3D Hierarchical Byttneria Aspera‐Like Ni@
        Graphitic Carbon Yolk–Shell Microspheres as Bifunctional
        Catalysts for Ultraefficient Oxidation/Reduction of …
        </td>
        <td style="text-align:left;">
        X Liu, D Xu, Q Wang, L Zhang
        </td>
        <td style="text-align:left;">
        2018
        </td>
        <td style="text-align:right;">
        33
        </td>
        <td style="text-align:left;">
        The search for earth‐abundant, low‐cost, recyclable,
        multifunctional as well as highly active catalysts remains the
        most pressing demand for heterogeneous catalytic elimination of
        …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        2
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        Above-and below-ground competition in high and low irradiance:
        tree seedling responses to a competing liana Byttneria
        grandifolia
        </td>
        <td style="text-align:left;">
        YJ Chen, F Bongers, KF Cao, Z Cai
        </td>
        <td style="text-align:left;">
        2008
        </td>
        <td style="text-align:right;">
        49
        </td>
        <td style="text-align:left;">
        In tropical forests, trees compete not only with other trees,
        but also with lianas, which may limit tree growth and
        regeneration. Liana effects may depend on the availability of
        above- and …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        2
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        Abroma augusta Linn f: a review.
        </td>
        <td style="text-align:left;">
        G Babita, N Satish, S Sarita
        </td>
        <td style="text-align:left;">
        2011
        </td>
        <td style="text-align:right;">
        38
        </td>
        <td style="text-align:left;">
        … This article discusses about the medicinal values of Abroma
        augusta Linn f. In this communication, we reviewed the
        pharmacological and phytochemistry of Abroma augusta Linn f. and
        …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        2
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        Phytochemical screening and evaluation of anti-inflammatory
        activity of methanolic extract of Abroma augusta Linn
        </td>
        <td style="text-align:left;">
        S Das, R Datta, S Nandy
        </td>
        <td style="text-align:left;">
        2012
        </td>
        <td style="text-align:right;">
        31
        </td>
        <td style="text-align:left;">
        … study of different parts of Abroma augusta Linn methanolic
        extract by the … of Abroma augusta Linn methanolic extract.
        Conclusions: The methanolic extract of different parts of Abroma
        …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        2
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        \[PDF\]\[PDF\] Comparative study of antidiabetic effect of
        Abroma augusta and Syzygium cumini on alloxan induced diabetic
        rat
        </td>
        <td style="text-align:left;">
        L Nahar, FA Ripa, AHM Zulfiker
        </td>
        <td style="text-align:left;">
        2010
        </td>
        <td style="text-align:right;">
        42
        </td>
        <td style="text-align:left;">
        Herbal formulations are getting more importance in the treatment
        of diabetes, cancer and hepatic disorder because of the
        hazardous adverse effects of the current therapy. Especially …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        2
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        Unravelling the Biological Activities of the Byttneria pilosa
        Leaves Using Experimental and Computational Approaches
        </td>
        <td style="text-align:left;">
        MA Jyoti, N Barua, MS Hossain, M Hoque, TA Bristy
        </td>
        <td style="text-align:left;">
        2020
        </td>
        <td style="text-align:right;">
        17
        </td>
        <td style="text-align:left;">
        Byttneria pilosa is locally known as Harijora, and used by the
        native hill-tract people of Bangladesh for the treatment of
        rheumatalgia, snake bite, syphilis, fractured bones,
        elephantiasis …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        \[PDF\]\[PDF\] COX and LOX inhibitory potential of Abroma
        augusta and Desmodium gangeticum
        </td>
        <td style="text-align:left;">
        R Bisht, S Bhattacharya, YA Jaliwala
        </td>
        <td style="text-align:left;">
        2014
        </td>
        <td style="text-align:right;">
        28
        </td>
        <td style="text-align:left;">
        The present study was aimed to evaluate the cyclooxygenase (COX)
        and lipoxygenase (LOX) inhibitory activity of Abroma augusta
        (AA) and Desmodium gangeticum (DG). Initially In-…
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        \[HTML\]\[HTML\] A synopsis of Byttnerieae (Malvaceae,
        Byttnerioideae) from the Atlantic Forest, with notes on
        geographical distribution, nomenclature, and conservation
        </td>
        <td style="text-align:left;">
        M Colli-Silva, JR Pirani
        </td>
        <td style="text-align:left;">
        2021
        </td>
        <td style="text-align:right;">
        NA
        </td>
        <td style="text-align:left;">
        … Additionally, for this work, we examined the following
        herbarium collections of Ayenia and Byttneria: K, R, RB, SP and
        SPF (acronyms according to Thiers (2020Thiers B. 2020; …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        Ontogeny, anatomical structure and function of lobed stems in
        the evolution of the climbing growth form in Malvaceae
        (Byttneria Loefl.)
        </td>
        <td style="text-align:left;">
        L Luna-Márquez, WV Sharber, BA Whitlock
        </td>
        <td style="text-align:left;">
        2021
        </td>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        … Within Byttneria and allies, lobed young stems appeared at
        least three times, once in Ayenia and twice in the paraphyletic
        Byttneria. Lobed adult stems were conserved in Byttneria ss, …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        Antimicrobial and cytotoxic activities of Abroma augusta Lnn.
        leaves extract
        </td>
        <td style="text-align:left;">
        FK Saikot, A Khan, MF Hasan
        </td>
        <td style="text-align:left;">
        2012
        </td>
        <td style="text-align:right;">
        26
        </td>
        <td style="text-align:left;">
        … the antimicrobial and cytotoxic activity of Abroma augusta. …
        of acetone extract of leaves of Abroma augusta. Methods: Disc …
        Conclusions: The Abroma leaves extract may be consider as a …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        Taraxerol, a pentacyclic triterpene from Abroma augusta leaf,
        attenuates acute inflammation via inhibition of NF-κB signaling
        </td>
        <td style="text-align:left;">
        R Khanra, S Dewanjee, TK Dua
        </td>
        <td style="text-align:left;">
        2017
        </td>
        <td style="text-align:right;">
        22
        </td>
        <td style="text-align:left;">
        Abroma augusta L. (Malvaceae) leaf is traditionally used to
        treat inflammatory disorders. In our laboratory, we have
        scientifically validated the anti-inflammatory effect of A.
        augusta leaf …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        \[HTML\]\[HTML\] Abroma augusta Linn bark extract-mediated green
        synthesis of gold nanoparticles and its application in catalytic
        reduction
        </td>
        <td style="text-align:left;">
        S Das, BG Bag, R Basu
        </td>
        <td style="text-align:left;">
        2015
        </td>
        <td style="text-align:right;">
        21
        </td>
        <td style="text-align:left;">
        The bark extract of Abroma augusta Linn is rich in medicinally
        important phytochemicals including antioxidants and polyphenols.
        First one step green synthesis of gold nanoparticles (…
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        \[PDF\]\[PDF\] Alkaline peroxide oxidation pretreatment of corn
        cob and rice husks for bioconversion into bio-commodities: part
        a-enzymatic convertibility of pretreated rice …
        </td>
        <td style="text-align:left;">
        AO Ayenia, R Ogub, AA Awosusia
        </td>
        <td style="text-align:left;">
        2016
        </td>
        <td style="text-align:right;">
        8
        </td>
        <td style="text-align:left;">
        In this study, a 23-central composite design (CCD) of
        experiments was adopted using MINITAB 15 statistical software to
        investigate the effect of pretreatment conditions on enzymatic …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        Estimating bioregions and undercollected areas in South America
        by revisiting Byttnerioideae, Helicteroideae and Sterculioideae
        (Malvaceae) occurrence data
        </td>
        <td style="text-align:left;">
        M Colli-Silva, JR Pirani
        </td>
        <td style="text-align:left;">
        2020
        </td>
        <td style="text-align:right;">
        11
        </td>
        <td style="text-align:left;">
        … (Theobroma spp.) and lianescent Byttneria. A second group is …
        of Ayenia and Byttneria, as well as by Helicteres from Central
        Brazil. A third group is represented by Ayenia and Byttneria, …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        \[HTML\]\[HTML\] Evaporation mediated encapsulation of tea
        polyphenol in Abroma augusta mucilage polysaccharide:
        Physiochemical characterization and its use asinstant …
        </td>
        <td style="text-align:left;">
        A Roy, SL Shrivastava
        </td>
        <td style="text-align:left;">
        2021
        </td>
        <td style="text-align:right;">
        7
        </td>
        <td style="text-align:left;">
        … This study develops tea polyphenol microcapsules using Abroma
        augusta mucilage polysaccharide as a continuous phase. Abroma
        augusta mucilage (AAM) microcapsules containing …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        Isolation of active compounds from the leaf extract of patah
        kemudi (Abroma augusta L.) and its anti-inflammatory activity
        </td>
        <td style="text-align:left;">
        M Latief, H Amanda, A Utami
        </td>
        <td style="text-align:left;">
        2019
        </td>
        <td style="text-align:right;">
        10
        </td>
        <td style="text-align:left;">
        … contained in the leaves of Abroma augusta and determine anti…
        methanol extract of all parts of Abroma augusta plant has anti…
        isolate the compounds found in Abroma augusta leaves and …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        4
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        Typification of names of American species of vascular plants
        proposed by Linnaeus and based on Loefling’s Iter
        Hispanicum (1758)
        </td>
        <td style="text-align:left;">
        LJ Dorr, JH Wiersema
        </td>
        <td style="text-align:left;">
        2010
        </td>
        <td style="text-align:right;">
        21
        </td>
        <td style="text-align:left;">
        … Ayenia and “foliis” but he failed to italicize the epithet and
        it therefore reads as if it were a polynomial (viz., “AYENIA …
        Ayenia tomentosa as currently interpreted is known from
        Venezuela, …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        4
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        \[PDF\]\[PDF\] Effects of aqueous extract of fresh leaves of
        Abroma augusta L. on oral absorption of glucose and metformin
        hydrochloride in experimental rats
        </td>
        <td style="text-align:left;">
        T Islam, A Rahman, AU Islam
        </td>
        <td style="text-align:left;">
        2012
        </td>
        <td style="text-align:right;">
        21
        </td>
        <td style="text-align:left;">
        … of metformin activity by the aqueous fraction of leafs of
        Abroma augusta L. may be due to the … We observed that the
        aqueous fraction of Abroma augusta L. significantly hamper the …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        4
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        \[PDF\]\[PDF\] Phytochemical and Antioxidant analysis of the
        leaf extract of Malaysian Medicinal Plant Abroma augusta L.
        </td>
        <td style="text-align:left;">
        P Sunitha, N Sathyanarayana
        </td>
        <td style="text-align:left;">
        2018
        </td>
        <td style="text-align:right;">
        16
        </td>
        <td style="text-align:left;">
        Abroma augusta has been used in folk Indian system of
        alternative medicine, for treating various conditions like
        diabetes mellitus, amenorrhea, nervous dysmenorrhea,
        antifertility and …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        4
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        Efficacy of Abroma augusta on biochemical and histomorphological
        features of alloxan-induced diabetic rabbits
        </td>
        <td style="text-align:left;">
        S Hussain Mir, M Maqbool Darzi
        </td>
        <td style="text-align:left;">
        2013
        </td>
        <td style="text-align:right;">
        17
        </td>
        <td style="text-align:left;">
        Background and Objectives: Medicinal plants are well documented
        for possessing antidiabetic or antihyperglycaemic potential.
        Abroma augusta seems to have the same effect in …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        4
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        Structural studies of a mucilage from Abroma augusta root bark
        </td>
        <td style="text-align:left;">
        N Nahar, BK Hazra, M Mosihuzzaman
        </td>
        <td style="text-align:left;">
        1994
        </td>
        <td style="text-align:right;">
        15
        </td>
        <td style="text-align:left;">
        The structure of an acidic polysaccharide isolated from Abroma
        augusta root bark was determined by sugar and methylation
        analyses and high resolution 1 H- and 13 C-NMR …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        4
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        \[HTML\]\[HTML\] Antibacterial and antibiofilm activity of
        Abroma augusta stabilized silver (Ag) nanoparticles against
        drug-resistant clinical pathogens
        </td>
        <td style="text-align:left;">
        S Kumar, HM Khan, FM Husain, R Ahmad
        </td>
        <td style="text-align:left;">
        2023
        </td>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        Infectious diseases remain among the most pressing concerns for
        human health. This issue has grown even more complex with the
        emergence of multidrug-resistant (MDR) bacteria. To …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        4
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        In vitro prevention of chick pancreatic lipase activity by
        Abroma augusta extract
        </td>
        <td style="text-align:left;">
        N Gupta, A Ganeshpurkar, N Jatav, D Bansal
        </td>
        <td style="text-align:left;">
        2012
        </td>
        <td style="text-align:right;">
        16
        </td>
        <td style="text-align:left;">
        … Objective: To investigate chick pancreatic lipase inhibitory
        activities of the Abroma augusta (… of Abroma augusta. Authors
        also kindly acknowledge the support from Rewa Shiksha …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        4
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        Anti-inflammatory activities of squalene compound of methanol
        extract of Abroma augusta L
        </td>
        <td style="text-align:left;">
        M Latief, M Muhaimin, H Amanda
        </td>
        <td style="text-align:left;">
        2020
        </td>
        <td style="text-align:right;">
        6
        </td>
        <td style="text-align:left;">
        Abroma augusta L plant traditionally was used to treat
        swellings, cuts, sores, and bruises. In the province of
        Jambi, A. augusta is used in folk medicine to treat wounds. This
        study aims to …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        4
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        \[PDF\]\[PDF\] Anticholinesterase and antioxidant potentials of
        a medicinal plant Abroma augusta: Implications for the
        alternative treatment therapy of cognitive deficits in …
        </td>
        <td style="text-align:left;">
        MM Begum, K Biswas, A Sarker, T Huq
        </td>
        <td style="text-align:left;">
        2015
        </td>
        <td style="text-align:right;">
        11
        </td>
        <td style="text-align:left;">
        Oxidative stress and low level of neurotransmitter (especially
        acetylcholine) are main characteristics of Alzheimer’s disease
        (AD), a progressive neurodegenerative disease. Prolonging …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        4
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        \[PDF\]\[PDF\] Repellent activity of Abroma augusta extracts
        against Tribolium castaneum (herbst) adults
        </td>
        <td style="text-align:left;">
        OA Mondal, J Haque, E Haque
        </td>
        <td style="text-align:left;">
        2012
        </td>
        <td style="text-align:right;">
        13
        </td>
        <td style="text-align:left;">
        Context: Repellency test can play an important role in special
        environmental conditions. Objectives: The main objective of this
        study was to make a standard protocol to control the pest …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        \[HTML\]\[HTML\] Phytochemical and pharmacological investigation
        of the ethanol extract of Byttneria pilosa Roxb.
        </td>
        <td style="text-align:left;">
        L Sikder, MR Khan, SZ Smrity, MT Islam, SA Khan
        </td>
        <td style="text-align:left;">
        2022
        </td>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        … Traditionally, the herb Byttneria pilosa Roxb. is used for
        bone fractures, boils, scabies, rheumatalgia, snake bites,
        syphilis, elephantiasis, poisoning, and eye infection.
        Scientific reports …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        \[PDF\]\[PDF\] In vitro thrombolytic and anti-oxidant activity
        study of Abroma augusta (Ulatkambal)
        </td>
        <td style="text-align:left;">
        MAM Bhuiya, B Talukder, M Ajrin, S Akter, R Sen
        </td>
        <td style="text-align:left;">
        2013
        </td>
        <td style="text-align:right;">
        9
        </td>
        <td style="text-align:left;">
        Antioxidants lavishly present in many natural source attributed
        their capability of slowing or preventing the oxidation of other
        molecules, helpful in the prevention of many age-related …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        \[HTML\]\[HTML\] Methanolic Bark Extract of Abroma augusta (L.)
        Induces Apoptosis in EAC Cells through Altered Expression of
        Apoptosis Regulatory Genes
        </td>
        <td style="text-align:left;">
        M Miah, AS Shimu, S Mahmud, FB Omar
        </td>
        <td style="text-align:left;">
        2020
        </td>
        <td style="text-align:right;">
        9
        </td>
        <td style="text-align:left;">
        Abroma augusta (L.), one of the herbal medicinal plants, is
        widely used for treatment of various maladies. The present study
        was initiated to determine the antioxidant, hemolytic, …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        \[PDF\]\[PDF\] Possible anti-obesity activity of methanol
        extract of Byttneria pilosa Roxb. leaves
        </td>
        <td style="text-align:left;">
        R Zaman, M Parvez, S Ali, M Islam
        </td>
        <td style="text-align:left;">
        2015
        </td>
        <td style="text-align:right;">
        9
        </td>
        <td style="text-align:left;">
        The purpose of this study was to investigate the possible
        anti-obesity activity of methanol extract of Byttneria pilosa
        Roxb.(MEBP) on Swiss albino mice by inducing anorexia. 18 h
        fasted …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        POLLEN MORPHOLOGY AND HARMOMEGATHIC CHARACTERS OF Byttneria
        LÖFL. SPECIES (STERCULIACEAE S. S: SUBFAM. BYTTNERIOIDEAE)
        </td>
        <td style="text-align:left;">
        AJ Amirul-Aiman, T Noraini
        </td>
        <td style="text-align:left;">
        2019
        </td>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        … Dried pollen samples of seven Byttneria species were … This
        study proved that pollen morphological characteristics of
        Byttneria … Five of the Byttneria species in this study showed …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        Phylogenetic relationships and floral evolution of the
        Byttnerioideae (“Sterculiaceae” or Malvaceae sl) based on
        sequences of the chloroplast gene, ndhF
        </td>
        <td style="text-align:left;">
        BA Whitlock, C Bayer, DA Baum
        </td>
        <td style="text-align:left;">
        2001
        </td>
        <td style="text-align:right;">
        143
        </td>
        <td style="text-align:left;">
        … Our results suggest that Byttneria is paraphyletic with
        respect to Ayenia (Fig. 4). The primary … aments differing from
        the condition in Abroma, Byttneria, and Ayenia, with anthers
        sessile on …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        \[PDF\]\[PDF\] Impact of thiocyanate on the catecholase activity
        of Cu (II) and Fe (III) complexes of
        2-((4-(2-hydroxy-4-methylbenzyl) piperazin-1-yl)
        methyl)-5-methylphenol (a …
        </td>
        <td style="text-align:left;">
        AO Ayenia, GM Watkinsa
        </td>
        <td style="text-align:left;">
        2018
        </td>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        Four metal complexes viz Cu (II) and Fe (III) with or without
        thiocyanate have been synthesized from a Mannich base prepared
        by a simple synthetic route. These complexes were …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        \[PDF\]\[PDF\] Comparative study of antibacterial, antifungal
        and cytotoxic effects of different extracts of Dillenia indica
        thunb and Abroma augusta Linn
        </td>
        <td style="text-align:left;">
        MZ Uddin, S Dibyajyoti, KN Aninda, J Anowara
        </td>
        <td style="text-align:left;">
        2012
        </td>
        <td style="text-align:right;">
        7
        </td>
        <td style="text-align:left;">
        The present study was designed to evaluate in vitro
        antibacterial, antifungal and cytotoxic effects of ethanolic and
        petroleum ether extracts of two Bangladeshi medicinal plants
        Dillenia …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        Genomic data and morphological re‐assessment reveals synonymy
        and hybridisation among Seringia taxa (Lasiopetaleae, Malvaceae)
        in remote north‐western …
        </td>
        <td style="text-align:left;">
        RM Binks, CF Wilkins, AS Markey, MN Lyons, M Byrne
        </td>
        <td style="text-align:left;">
        2020
        </td>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        Conservation of rare or threatened species requires a range of
        information, including a sound taxonomic foundation, to ensure
        appropriate management. However, rare species are …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        intext:“Keraudrenia” OR “Seringia” OR “Abroma” OR “Byttneria” OR
        “Ayenia” OR “Rayleya”
        </td>
        <td style="text-align:left;">
        \[PDF\]\[PDF\] In-vitro screening of an antioxidant potential of
        Byttneria herbacea Roxb
        </td>
        <td style="text-align:left;">
        SR Somkuwar, UJ Dongre, RR Chaudhary
        </td>
        <td style="text-align:left;">
        2014
        </td>
        <td style="text-align:right;">
        7
        </td>
        <td style="text-align:left;">
        … We have not cited any work done with an antioxidant activity
        of Byttneria herbacea hence, we have undertaken this study to
        evaluate an antioxidant properties of this plant species and …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        1
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        \[HTML\]\[HTML\] The genome of Theobroma cacao
        </td>
        <td style="text-align:left;">
        X Argout, J Salse, JM Aury, MJ Guiltinan, G Droc
        </td>
        <td style="text-align:left;">
        2011
        </td>
        <td style="text-align:right;">
        804
        </td>
        <td style="text-align:left;">
        We sequenced and assembled the draft genome of Theobroma cacao,
        an economically important tropical-fruit tree crop that is the
        source of chocolate. This assembly corresponds to 76…
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        1
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Theobroma cacao L., the Food of the Gods: A scientific approach
        beyond myths and claims
        </td>
        <td style="text-align:left;">
        M Rusconi, A Conti
        </td>
        <td style="text-align:left;">
        2010
        </td>
        <td style="text-align:right;">
        383
        </td>
        <td style="text-align:left;">
        Cocoa beans are rich source of polyphenols, contributing about
        10% of the dry weight of the whole bean and its derivative
        chocolate, particularly dark chocolate, is considered one of …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        1
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        \[BOOK\]\[B\] Cacao and its allies: a taxonomic revision of the
        genus Theobroma
        </td>
        <td style="text-align:left;">
        J Cuatrecasas
        </td>
        <td style="text-align:left;">
        1964
        </td>
        <td style="text-align:right;">
        558
        </td>
        <td style="text-align:left;">
        … ): Theobroma Cacao, with the short specific diagnosis “foliis
        integerrimis.” He also names a second species Theobroma Guazuma
        … the Guazuma of Plumier, under the title of Theobroma, …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        1
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        The major seed proteins of Theobroma cacao L.
        </td>
        <td style="text-align:left;">
        J Voigt, B Biehl, SKS Wazir
        </td>
        <td style="text-align:left;">
        1993
        </td>
        <td style="text-align:right;">
        153
        </td>
        <td style="text-align:left;">
        … Differential extractions of proteins from Theobroma cacao
        seeds … The negative globulin of Theobroma cacao is a
        glycoprotein … Therefore, Theobroma cacao is the first plant
        described to …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        1
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        \[PDF\]\[PDF\] Isolation and characterization of microsatellites
        in Theobroma cacao L.
        </td>
        <td style="text-align:left;">
        C Lanaud, AM Risterucci, I Pieretti, M Falque
        </td>
        <td style="text-align:left;">
        1999
        </td>
        <td style="text-align:right;">
        225
        </td>
        <td style="text-align:left;">
        Molecular markers, mainly restriction fragment length
        polymorphisms (RFLP) and random amplified polymorphic DNA (RAPD)
        have been applied to evaluate genetic resources and …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        1
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Cupuassu—Theobroma grandiflorum
        </td>
        <td style="text-align:left;">
        ALF Pereira, VKG Abreu, S Rodrigues
        </td>
        <td style="text-align:left;">
        2018
        </td>
        <td style="text-align:right;">
        30
        </td>
        <td style="text-align:left;">
        Among the Amazonian fruits, the cupuaçu stands out for its
        economic potential. The pulp of the fruit has a yellowish-white
        color and strong flavor, being very appreciated by local …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        1
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Cocoa (Theobroma cacao L.)
        </td>
        <td style="text-align:left;">
        KP Nair, KP Nair
        </td>
        <td style="text-align:left;">
        2021
        </td>
        <td style="text-align:right;">
        88
        </td>
        <td style="text-align:left;">
        Next to Coffee and Tea, Cocoa is a very important beverage crop.
        The chapter discusses its production parameters, because of its
        importance in the confectionery industry. The chapter …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        1
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Antioxidative Polyphenols Isolated from Theobroma cacao
        </td>
        <td style="text-align:left;">
        C Sanbongi, N Osakabe, M Natsume
        </td>
        <td style="text-align:left;">
        1998
        </td>
        <td style="text-align:right;">
        317
        </td>
        <td style="text-align:left;">
        The antioxidant components of cacao liquor, which is a major
        ingredient of chocolate, were isolated with column
        chromatography and high-performance liquid chromatography. …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        1
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Cacao—Theobroma cacao
        </td>
        <td style="text-align:left;">
        PA de Souza, LF Moreira, DHA Sarmento, FB da Costa
        </td>
        <td style="text-align:left;">
        2018
        </td>
        <td style="text-align:right;">
        57
        </td>
        <td style="text-align:left;">
        Cacao originates from the rainforest regions of tropical America
        from Peru to Mexico. Cacao has about 80% of its constitution in
        the bark and the remaining 20% includes seeds, pulp …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        1
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        A physiological production model for cocoa (Theobroma cacao):
        model presentation, validation and application
        </td>
        <td style="text-align:left;">
        PA Zuidema, PA Leffelaar, W Gerritsma, L Mommer
        </td>
        <td style="text-align:left;">
        2005
        </td>
        <td style="text-align:right;">
        282
        </td>
        <td style="text-align:left;">
        In spite of the economic importance and extensive agronomic
        literature on cocoa, no physiological production model has been
        developed for cocoa so far. Such a model would be very …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        2
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Polyphenols in cocoa (Theobroma cacao L.).
        </td>
        <td style="text-align:left;">
        CL Hii, CL Law, S Suzannah, M Cloke
        </td>
        <td style="text-align:left;">
        2009
        </td>
        <td style="text-align:right;">
        242
        </td>
        <td style="text-align:left;">
        Polyphenols have gained much interest recently due to its
        antioxidant capacity and possible benefits to human health such
        as anti-carcinogenic, anti-atherogenic, anti-ulcer, …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        2
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Tracing the native ancestors of the modern Theobroma cacao L.
        population in Ecuador
        </td>
        <td style="text-align:left;">
        RG Loor, AM Risterucci, B Courtois, O Fouet
        </td>
        <td style="text-align:left;">
        2009
        </td>
        <td style="text-align:right;">
        85
        </td>
        <td style="text-align:left;">
        The native Theobroma cacao L. population from Ecuador, known as
        Nacional, is famous for its fine cocoa flavour. From the
        beginning of the twentieth century, however, it has been …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        2
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        A high-density linkage map of Theobroma cacao L.
        </td>
        <td style="text-align:left;">
        AM Risterucci, L Grivet, JAK N’Goran, I Pieretti
        </td>
        <td style="text-align:left;">
        2000
        </td>
        <td style="text-align:right;">
        300
        </td>
        <td style="text-align:left;">
        … In Theobroma cacao, structural rearrangements have never been
        reported, and the genes … du cacaoyer Theobroma cacao L. II.
        Etude du polymorphisme de six systèmes enzymatiques. …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        2
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Review on polyphenols in Theobroma cacao: changes in composition
        during the manufacture of chocolate and methodology for
        identification and quantification
        </td>
        <td style="text-align:left;">
        J Wollgast, E Anklam
        </td>
        <td style="text-align:left;">
        2000
        </td>
        <td style="text-align:right;">
        947
        </td>
        <td style="text-align:left;">
        Polyphenols have become an intense focus of research interest
        because of their perceived health-beneficial effects, such as
        anti-carcinogenic, anti-atherogenic, anti-inflammatory, anti-…
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        2
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        \[HTML\]\[HTML\] Geographic and Genetic Population
        Differentiation of the Amazonian Chocolate Tree (Theobroma
        cacao L)
        </td>
        <td style="text-align:left;">
        JC Motamayor, P Lachenaud, JW Da Silva e Mota
        </td>
        <td style="text-align:left;">
        2008
        </td>
        <td style="text-align:right;">
        546
        </td>
        <td style="text-align:left;">
        Numerous collecting expeditions of Theobroma cacao L. germplasm
        have been undertaken in Latin-America. However, most of this
        germplasm has not contributed to cacao …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        2
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Isolation, comprehensive characterization and antioxidant
        activities of Theobroma cacao extract
        </td>
        <td style="text-align:left;">
        ML Cádiz-Gurrea, J Lozano-Sanchez
        </td>
        <td style="text-align:left;">
        2014
        </td>
        <td style="text-align:right;">
        105
        </td>
        <td style="text-align:left;">
        Cocoa (Theobroma cacao) is a major, economically important,
        international crop and has been associated with several
        nutritional benefits including high antioxidant capacity. The
        aim …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        2
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Endophytic fungi as biocontrol agents of Theobroma cacao
        pathogens
        </td>
        <td style="text-align:left;">
        LC Mejía, EI Rojas, Z Maynard, S Van Bael, AE Arnold
        </td>
        <td style="text-align:left;">
        2008
        </td>
        <td style="text-align:right;">
        548
        </td>
        <td style="text-align:left;">
        Fungal endophytes isolated from healthy Theobroma cacao tissues
        were screened in vitro for antagonism against major pathogens of
        cacao. Of tested endophytic morphospecies, 40% (…
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        2
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        New Bioactive Polyphenols from Theobroma grandiflorum
        (“Cupuaçu”)
        </td>
        <td style="text-align:left;">
        H Yang, P Protiva, B Cui, C Ma, S Baggett
        </td>
        <td style="text-align:left;">
        2003
        </td>
        <td style="text-align:right;">
        114
        </td>
        <td style="text-align:left;">
        Activity-guided fractionation of Theobroma grandiflorum
        (“cupuaçu”) seeds resulted in the identification of two new
        sulfated flavonoid glycosides, theograndins I (1) and II (2). In
        addition, …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        2
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        \[PDF\]\[PDF\] Cocoa (Theobroma cacao) Production in Central
        Java
        </td>
        <td style="text-align:left;">
        Y Dwiningsih, A Rafael, S Titihalawa
        </td>
        <td style="text-align:left;">
        2008
        </td>
        <td style="text-align:right;">
        97
        </td>
        <td style="text-align:left;">
        Cocoa (Theobroma cacao) Production in Central Java … Cocoa
        (Theobroma cacao) Production in Central Java … Cocoa (Theobroma
        cacao) Production in Central Java. Satya …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        2
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Cacao pod husks (Theobroma cacao L.): Composition and
        hot-water-soluble pectins
        </td>
        <td style="text-align:left;">
        LC Vriesmann, RDMC Amboni
        </td>
        <td style="text-align:left;">
        2011
        </td>
        <td style="text-align:right;">
        205
        </td>
        <td style="text-align:left;">
        The composition of cacao pod husks (CPHs), the main waste
        product of cocoa production, and some of the characteristics of
        their water-soluble pectins were investigated. Milled and …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Selection of international molecular standards for DNA
        fingerprinting of Theobroma cacao
        </td>
        <td style="text-align:left;">
        JA Saunders, S Mischke, EA Leamy
        </td>
        <td style="text-align:left;">
        2004
        </td>
        <td style="text-align:right;">
        128
        </td>
        <td style="text-align:left;">
        A collaborative international program was initiated to identify
        and describe the genetic diversity of living germplasm
        collections of Theobroma cacao genotypes that are maintained in
        …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Reexamining the classification of Theobroma cacao L. using
        molecular markers
        </td>
        <td style="text-align:left;">
        A Figueira, J Janick, M Levy
        </td>
        <td style="text-align:left;">
        1994
        </td>
        <td style="text-align:right;">
        86
        </td>
        <td style="text-align:left;">
        … among eight Theobroma and two Herrania species, … one cluster
        included all Herrania and Theobroma species, except … There was
        no clear distinction between Herrania and Theobroma …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Fertilization and embryogeny in Theobroma cacao, L
        </td>
        <td style="text-align:left;">
        EE Cheesman
        </td>
        <td style="text-align:left;">
        1927
        </td>
        <td style="text-align:right;">
        78
        </td>
        <td style="text-align:left;">
        Methods. Flowers were taken from twelve-year old trees growing
        at St. Augustine Experiment Station; several trees were used,
        and no differences were noticed in the material from them…
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Theobroma
        </td>
        <td style="text-align:left;">
        D Zhang, A Figueira, L Motilal, P Lachenaud
        </td>
        <td style="text-align:left;">
        2011
        </td>
        <td style="text-align:right;">
        16
        </td>
        <td style="text-align:left;">
        … In addition, seven related species in Theobroma and ten
        species in the genus Herrania … of the Theobroma and Herrania
        species based on morphology. Herrania and Theobroma were …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Comparative genetic diversity studies of Theobroma cacao L.
        using RFLP and RAPD markers
        </td>
        <td style="text-align:left;">
        JAK N’Goran, V Laurent, AM Risterucci, C Lanaud
        </td>
        <td style="text-align:left;">
        1994
        </td>
        <td style="text-align:right;">
        171
        </td>
        <td style="text-align:left;">
        The genetic diversity of 106 genotypes of Theobroma cacao L. was
        assessed using RFLP and RAPD methods. Thirty-one cDNA probes and
        19 primers generated, respectively, 87 and …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Factors influencing quality variation in cocoa (Theobroma cacao)
        bean flavour profile—A review
        </td>
        <td style="text-align:left;">
        JE Kongor, M Hinneh, D Van de Walle
        </td>
        <td style="text-align:left;">
        2016
        </td>
        <td style="text-align:right;">
        445
        </td>
        <td style="text-align:left;">
        This review examined the factors that influence flavour
        volatiles of cocoa beans and the volume of work that needs to be
        done on these factors and their impact on the flavour volatiles
        of …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Genetic fingerprinting of Theobroma clones using randomly
        amplified polymorphic DNA markers
        </td>
        <td style="text-align:left;">
        J Wilde, R Waugh, W Powell
        </td>
        <td style="text-align:left;">
        1992
        </td>
        <td style="text-align:right;">
        315
        </td>
        <td style="text-align:left;">
        Randomly amplified polymorphic DNA (RAPD) markers have been used
        to characterise cocoa clones representing the three main
        cultivated subpopulations: Criollo, Forastero and …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        The water relations and irrigation requirements of cocoa
        (Theobroma cacao L.): a review
        </td>
        <td style="text-align:left;">
        MKV Carr, G Lockwood
        </td>
        <td style="text-align:left;">
        2011
        </td>
        <td style="text-align:right;">
        216
        </td>
        <td style="text-align:left;">
        The results of research into the water relations of cocoa are
        reviewed in the context of drought mitigation and irrigation
        need. Background information on the centres of production of the
        …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Extracts of cocoa (Theobroma cacao L.) leaves and their
        antioxidation potential
        </td>
        <td style="text-align:left;">
        H Osman, R Nasarudin, SL Lee
        </td>
        <td style="text-align:left;">
        2004
        </td>
        <td style="text-align:right;">
        170
        </td>
        <td style="text-align:left;">
        Cocoa shoot (CS), young leaves (CL) and tea leaves (GT) were
        processed according to green tea processing procedures.
        Polyphenol components was extracted and analysed using …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        3
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Management of black pod rot in cacao (Theobroma cacao L.): a
        review
        </td>
        <td style="text-align:left;">
        Y Acebo-Guerrero, A Hernández-Rodríguez
        </td>
        <td style="text-align:left;">
        2012
        </td>
        <td style="text-align:right;">
        86
        </td>
        <td style="text-align:left;">
        Introduction Theobroma cacao L. is economically speaking the
        most important species of the genus Theobroma. Cacao is cultured
        in tropical regions and its yield is affected by several …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        4
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Flavonoids from Theobroma cacao Down-Regulate Inflammatory
        Mediators
        </td>
        <td style="text-align:left;">
        E Ramiro, À Franch, C Castellote
        </td>
        <td style="text-align:left;">
        2005
        </td>
        <td style="text-align:right;">
        142
        </td>
        <td style="text-align:left;">
        In the present study, we report the effects of a cocoa extract
        on the secretion and RNA expression of various proinflammatory
        mediators by macrophages. Monocyte chemoattractant …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        4
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Characterization of Pectins and Some Secondary Compounds from
        Theobroma cacao Hulls
        </td>
        <td style="text-align:left;">
        M Arlorio, JD Coisson, P Restani
        </td>
        <td style="text-align:left;">
        2001
        </td>
        <td style="text-align:right;">
        62
        </td>
        <td style="text-align:left;">
        This paper describes the chemical characterization of cocoa
        hulls, a potential source of high‐methoxyl pectins (HMP). The
        content of some antinutritive compounds and potentially toxic …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        4
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Comparative analyses of chloroplast genomes of Theobroma cacao
        and Theobroma grandiflorum
        </td>
        <td style="text-align:left;">
        Abdullah, S Waseem, B Mirza, I Ahmed, MT Waheed
        </td>
        <td style="text-align:left;">
        2020
        </td>
        <td style="text-align:right;">
        27
        </td>
        <td style="text-align:left;">
        … Theobroma is a plant genus included in tribe Theobromeae, …
        genomes of Theobroma cacao and Theobroma grandiflorum … for
        inferring of genus Theobroma phylogeny. The inferring of …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        4
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        The use and domestication of Theobroma cacao during the
        mid-Holocene in the upper Amazon
        </td>
        <td style="text-align:left;">
        S Zarrillo, N Gaikwad, C Lanaud, T Powis
        </td>
        <td style="text-align:left;">
        2018
        </td>
        <td style="text-align:right;">
        189
        </td>
        <td style="text-align:left;">
        Cacao (Theobroma cacao L.) is an important economic crop, yet
        studies of its domestication history and early uses are limited.
        Traditionally, cacao is thought to have been first …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        4
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        The shoot‐growth rhythm of a tropical tree, Theobroma cacao
        </td>
        <td style="text-align:left;">
        DC Greathouse, WM Laetsch
        </td>
        <td style="text-align:left;">
        1971
        </td>
        <td style="text-align:right;">
        160
        </td>
        <td style="text-align:left;">
        … As an initial part of a project to study shoot‐growth rhythms
        in tropical trees this paper describes the rhythm in Theobroma
        cacao L. An individual shoot passes through alternate periods …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        4
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Theobroma cacao L., “The Food of the Gods”: Quality Determinants
        of Commercial Cocoa Beans, with Particular Reference to the
        Impact of Fermentation
        </td>
        <td style="text-align:left;">
        LJR Lima, MH Almeida, MJR Nout
        </td>
        <td style="text-align:left;">
        2011
        </td>
        <td style="text-align:right;">
        276
        </td>
        <td style="text-align:left;">
        The quality of commercial cocoa beans, the principal raw
        material for chocolate production, relies on the combination of
        factors that include the type of planting material, the
        agricultural …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        4
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        \[HTML\]\[HTML\] The age of chocolate: a diversification history
        of Theobroma and Malvaceae
        </td>
        <td style="text-align:left;">
        JE Richardson, BA Whitlock, AW Meerow
        </td>
        <td style="text-align:left;">
        2015
        </td>
        <td style="text-align:right;">
        92
        </td>
        <td style="text-align:left;">
        … that the diversification of the genera Theobroma and Herrania
        occurred from 12.7 (11.6–… We also demonstrate that Theobroma
        cacao, the source of chocolate, diverged from its most …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        4
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Integrated System for Propagation of Theobroma cacao L.
        </td>
        <td style="text-align:left;">
        SN Maximova, A Young, S Pishak, C Miller
        </td>
        <td style="text-align:left;">
        2005
        </td>
        <td style="text-align:right;">
        43
        </td>
        <td style="text-align:left;">
        Theobroma cacao L. is a tropical tree, with origins in the
        Amazon basin, currently cultivated throughout the tropics to
        supply the global demand for cocoa, the main ingredient for the
        …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        4
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Chemical, technological and in vitro antioxidant properties of
        cocoa (Theobroma cacao L.) co-products
        </td>
        <td style="text-align:left;">
        R Martínez, P Torres, MA Meneses, JG Figueroa
        </td>
        <td style="text-align:left;">
        2012
        </td>
        <td style="text-align:right;">
        226
        </td>
        <td style="text-align:left;">
        The aim of this work was to determine the chemical,
        technological and in vitro antioxidant properties of cocoa
        co-products such as cocoa pod husks, cocoa bean shell and cocoa
        …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        4
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Efficiency, genotypic variability, and cellular origin of
        primary and secondary somatic embryogenesis of Theobroma
        cacao L.
        </td>
        <td style="text-align:left;">
        SN Maximova, L Alemanno, A Young
        </td>
        <td style="text-align:left;">
        2002
        </td>
        <td style="text-align:right;">
        203
        </td>
        <td style="text-align:left;">
        The development of efficient tissue culture systems for cacao
        holds the potential to contribute to the improvement of this
        tropical erop by providing a rapid and efficient vegetative …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        \[HTML\]\[HTML\] Agronomic and forage characteristics of Guazuma
        ulmifolia Lam.
        </td>
        <td style="text-align:left;">
        LY Manríquez-Mendoza, S López-Ortíz
        </td>
        <td style="text-align:left;">
        2011
        </td>
        <td style="text-align:right;">
        40
        </td>
        <td style="text-align:left;">
        Native trees are an important source of forage for livestock,
        particularly in regions having prolonged dry periods. Some tree
        species have fast growth rates, good nutritional quality, and …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Antibiosis, mycoparasitism, and colonization success for
        endophytic Trichoderma isolates with biological control
        potential in Theobroma cacao
        </td>
        <td style="text-align:left;">
        BA Bailey, H Bae, MD Strem, J Crozier, SE Thomas
        </td>
        <td style="text-align:left;">
        2008
        </td>
        <td style="text-align:right;">
        296
        </td>
        <td style="text-align:left;">
        Theobroma cacao (cacao) suffers severe yield losses in many … of
        trunks of Theobroma species, pods of Theobroma species, … All
        the isolates studied were able to colonize Theobroma …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        A meta–QTL analysis of disease resistance traits of Theobroma
        cacao L.
        </td>
        <td style="text-align:left;">
        C Lanaud, O Fouet, D Clément, M Boccara
        </td>
        <td style="text-align:left;">
        2009
        </td>
        <td style="text-align:right;">
        117
        </td>
        <td style="text-align:left;">
        Theobroma cacao, is a tropical understorey tree that is a major
        economic resource to several tropical countries. However, the
        crop is under increased threat from several diseases that …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Smallholder cacao (Theobroma cacao Linn.) cultivation in
        agroforestry systems of West and Central Africa: challenges and
        opportunities
        </td>
        <td style="text-align:left;">
        B Duguma, J Gockowski, J Bakala
        </td>
        <td style="text-align:left;">
        2001
        </td>
        <td style="text-align:right;">
        358
        </td>
        <td style="text-align:left;">
        The cultural features, management practice, environmental
        sustainability, and economic profitability of smallholder cacao
        (Theobroma cacao)production in West and Central Africa are …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Cacao (Theobroma cacao L.) Breeding
        </td>
        <td style="text-align:left;">
        F Bekele, W Phillips-Mora
        </td>
        <td style="text-align:left;">
        2019
        </td>
        <td style="text-align:right;">
        27
        </td>
        <td style="text-align:left;">
        The suboptimal productivity in cocoa farmers’ fields,
        particularly those of small-holders who produce over 80% of the
        global supply, and the demand for cocoa that meets stringent …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Fertilizer requirements of cacao (Theobroma cacao L.) in
        South-Western Nigeria
        </td>
        <td style="text-align:left;">
        M Wessel
        </td>
        <td style="text-align:left;">
        1971
        </td>
        <td style="text-align:right;">
        70
        </td>
        <td style="text-align:left;">
        The studies reported on were conducted in the period 1961-1970
        when the author was employed by the Cocoa Research Institute of
        Nigeria at Ibadan, formerly a sub-station of the …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Differences between the content of phenolic compounds in
        Criollo, Forastero and Trinitario cocoa seed (Theobroma cacao
        L.)
        </td>
        <td style="text-align:left;">
        S Elwers, A Zambrano, C Rohsius
        </td>
        <td style="text-align:left;">
        2009
        </td>
        <td style="text-align:right;">
        146
        </td>
        <td style="text-align:left;">
        Phenolic compounds contribute to the quality of raw cocoa, the
        basis of all chocolate products. Detailed research is needed
        about the variability of these substances in unprocessed …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Physiological and biochemical responses of Theobroma cacao L.
        genotypes to flooding
        </td>
        <td style="text-align:left;">
        FZ Bertolde, AAF Almeida, CP Pirovani, FP Gomes
        </td>
        <td style="text-align:left;">
        2012
        </td>
        <td style="text-align:right;">
        75
        </td>
        <td style="text-align:left;">
        Flooding is common in lowlands and areas with high rainfall or
        excessive irrigation. A major effect of flooding is the
        deprivation of O 2 in the root zone, which affects several
        biochemical …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Genetic diversity and spatial structure in a new distinct
        Theobroma cacao L. population in Bolivia
        </td>
        <td style="text-align:left;">
        D Zhang, WJ Martínez, ES Johnson
        </td>
        <td style="text-align:left;">
        2012
        </td>
        <td style="text-align:right;">
        65
        </td>
        <td style="text-align:left;">
        Cacao (Theobroma cacao L.) is an important economic crop in the
        Bolivian Amazon. Bolivian farmers both cultivate cacao, and
        extract fruits from wild stands in the Beni River region …
        </td>
        </tr>
        <tr>
        <td style="text-align:right;">
        5
        </td>
        <td style="text-align:left;">
        intext:“Kleinhovia” OR “Leptonychia” OR “Scaphopetalum” OR
        “Theobroma” OR “Herrania” OR “Glossostemon” OR “Guazuma”
        </td>
        <td style="text-align:left;">
        Characterization of cocoa butter extracted from Criollo
        cultivars of Theobroma cacao L.
        </td>
        <td style="text-align:left;">
        R Liendo, FC Padilla, A Quintana
        </td>
        <td style="text-align:left;">
        1997
        </td>
        <td style="text-align:right;">
        116
        </td>
        <td style="text-align:left;">
        Theobroma cacao L. cultivars grown in Venezuela are
        internationally, the most appreciated, due to their excellent
        flavor and aroma. The main objective of the present study was to
        …
        </td>
        </tr>
        </tbody>
        </table>
        </div>

``` r
# Test automatic term extraction from abstracts for the first 5 pages of results
gs_df = test3
test3_gs_terms = litsearchr::extract_terms(text = gs_df[,"abstract"],
                                           method = "fakerake", min_freq = 3, min_n = 2,
                                           stopwords = stopwords::data_stopwords_stopwordsiso$en)
cat("Test 3: Automatically extracted terms: \n")
```

    ## Test 3: Automatically extracted terms:

``` r
print(test3_gs_terms)
```

    ##  [1] "abroma augusta"            "byttneria pilosa"         
    ##  [3] "chemical investigation"    "cocoa beans"              
    ##  [5] "genus waltheria"           "heracleum lasiopetalum"   
    ##  [7] "medicinal plants"          "study aimed"              
    ##  [9] "tetrataenium lasiopetalum" "theobroma cacao"          
    ## [11] "waltheria douradinha"      "waltheria indica"         
    ## [13] "waltheria species"         "waltheria viscosissima"

# First pass: Define preliminary terms list for first full pass of Google Scholar scraping

``` r
# Combine Byttnerioideae genera, families, and topics of interest
genera = c("Hermannieae", "Lasiopetaleae", "Bytttnerieae", "Theobromeae", "Waltheria", "Hermannia", "Melochia", "Hannafordia", "Maxwellia", "Lasiopetalum", "Thomasia", "Guichenotia", "Commersonia",  "Rulingia", "Keraudrenia", "Seringia", "Abroma", "Byttneria", "Ayenia", "Rayleya", "Kleinhovia",  "Leptonychia", "Scaphopetalum", "Theobroma", "Herrania", "Glossostemon", "Guazuma") # TO DO: Add any missing genera, if any
families = c("Byttnerioideae", "Sterculiaceae") # Including old nomenclature
topics = c("morphology", "evolution", "ecology", "phylogenetics") # TO DO: Add any relevant topics
taxa = c(genera, families)

# Make an ordered list by family, genus, and topic
gs_grouped_selected_terms <- list(
taxa = taxa,
topics = topics
)

# Write the search
search_string = litsearchr::write_search(
gs_grouped_selected_terms,
languages = "English",
exactphrase = TRUE,
stemming = FALSE,
closure = "left",
writesearch = FALSE
)
```

    ## [1] "English is written"

``` r
cat("\nSearch string: \n")
```

    ## 
    ## Search string:

``` r
print(search_string)
```

    ## [1] "((Hermannieae OR Lasiopetaleae OR Bytttnerieae OR Theobromeae OR Waltheria OR Hermannia OR Melochia OR Hannafordia OR Maxwellia OR Lasiopetalum OR Thomasia OR Guichenotia OR Commersonia OR Rulingia OR Keraudrenia OR Seringia OR Abroma OR Byttneria OR Ayenia OR Rayleya OR Kleinhovia OR Leptonychia OR Scaphopetalum OR Theobroma OR Herrania OR Glossostemon OR Guazuma OR Byttnerioideae OR Sterculiaceae) AND (morphology OR evolution OR ecology OR phylogenetics))"

# First pass: Scrape Google Scholar

## Run 1-2 scraping blocks per day to avoid Google Scholar kicking you out

``` r
# Define useragent
useragent = httr::user_agent("Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.0.0 Safari/537.36")
# proxy1 = httr::use_proxy("5.78.83.190", port = 8080) # can pass proxy to function; here we just scrape patiently and don't use proxy
# Scrape pages 1:20
#gs_df1 = scrape_gs(term = 'intext:"Byttnerioideae" AND "morphology"', pages = 1:20, crawl_delay = 1.2, useragent) # scrape first 20 pages (200 published works)
# Scrape pages 21:40
#gs_df2 = scrape_gs(term = 'intext:"Byttnerioideae" AND "morphology"', pages = 21:40, crawl_delay = 1.2, useragent) 
# Scrape pages 41:60
#gs_df3 = scrape_gs(term = 'intext:"Byttnerioideae" AND "morphology"', pages = 41:60, crawl_delay = 1.2, useragent) 
# Scrape pages 61:80
#gs_df4 = scrape_gs(term = 'intext:"Byttnerioideae" AND "morphology"', pages = 61:80, crawl_delay = 1.2, useragent)
# Scrape last 19 pages provided by Google Scholar
#gs_df5 = scrape_gs(term = 'intext:"Byttnerioideae" AND "morphology"', pages = 81:99, crawl_delay = 1.2, useragent) 
```

# Create Co-Occurrence Network

``` r
# Combine search results
combined = rbind(test1, test2)
combined = rbind(combined, test3)
# Remove duplicates
gs_df = combined %>% distinct(title, .keep_all = TRUE)
cat("\nRows in original df: ", nrow(combined))
```

    ## 
    ## Rows in original df:  450

``` r
cat("\nRows in df with duplicates removed: ", nrow(gs_df))
```

    ## 
    ## Rows in df with duplicates removed:  351

``` r
# Combine key term results
combined_terms = c(test1_gs_terms, test2_gs_terms, test3_gs_terms)
# Remove duplicates
gs_terms = unique(combined_terms)
cat("\nTerms in original list: ", length(combined_terms))
```

    ## 
    ## Terms in original list:  31

``` r
cat("\nTerms in list with duplicates removed: ", length(gs_terms))
```

    ## 
    ## Terms in list with duplicates removed:  29

``` r
# Use the title and abstract of each article
gs_docs = paste(gs_df[, "title"], gs_df[, "abstract"]) 
gs_dfm = litsearchr::create_dfm(elements = gs_docs, features = gs_terms) # document-feature matrix
gs_coocnet = litsearchr::create_network(gs_dfm, min_studies = 3)
ggraph(gs_coocnet, layout = "stress") +
coord_fixed() +
expand_limits(x = c(-3, 3)) +
geom_edge_link(aes(alpha = weight)) +
geom_node_point(shape = "circle filled", fill = "white") +
geom_node_text(aes(label = name), hjust = "outward", check_overlap = TRUE) +
guides(edge_alpha = "none") +
theme_void()
```

    ## Warning: Using the `size` aesthetic in this geom was deprecated in ggplot2 3.4.0.
    ## ℹ Please use `linewidth` in the `default_aes` field and elsewhere instead.
    ## This warning is displayed once every 8 hours.
    ## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
    ## generated.

![](sys_lit_rev_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

# Prune the Network based on node strength

``` r
gs_node_strength = igraph::strength(gs_coocnet)
gs_node_rankstrength = data.frame(term = names(gs_node_strength), strength = gs_node_strength, row.names = NULL)
gs_node_rankstrength$rank = rank(gs_node_rankstrength$strength, ties.method = "min")
gs_node_rankstrength = gs_node_rankstrength[order(gs_node_rankstrength$rank),]
gs_plot_strength =
ggplot(gs_node_rankstrength, aes(x = rank, y = strength, label = term)) +
geom_line(lwd = 0.8) +
geom_point() +
ggrepel::geom_text_repel(size = 3, hjust = "right", nudge_y = 3, max.overlaps = 30) +
theme_bw()
gs_plot_strength
```

![](sys_lit_rev_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

# Determine which terms to prune for a more manageable list

``` r
# Cumulatively - retain 80% of the total strength of the network of search terms
gs_cutoff_cum = litsearchr::find_cutoff(gs_coocnet, method = "cumulative", percent = 0.8)
# Changepoints - points along the term rankings where the strength of the next strongest term is much greater than that of the previous one
gs_cutoff_change = litsearchr::find_cutoff(gs_coocnet, method = "changepoint", knot_num = 3)
gs_plot_strength +
geom_hline(yintercept = gs_cutoff_cum, color = "red", lwd = 0.7, linetype = "longdash", alpha = 0.6) +
geom_hline(yintercept = gs_cutoff_change, color = "orange", lwd = 0.7, linetype = "dashed", alpha = 0.6)
```

![](sys_lit_rev_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

# Prune the terms

``` r
# Determine cutoff criteria
gs_cutoff_crit = gs_cutoff_change[which.min(abs(gs_cutoff_change - gs_cutoff_cum))] # e.g. nearest cutpoint to cumulative criterion (cumulative produces one value, changepoints may be many)
gs_maxselected_terms = litsearchr::get_keywords(litsearchr::reduce_graph(gs_coocnet, gs_cutoff_crit))

# Inspect selected terms
cat("Selected terms: \n")
```

    ## Selected terms:

``` r
print(gs_maxselected_terms)
```

    ##  [1] "floral morphology"          "pollen morphology"         
    ##  [3] "family sterculiaceae"       "floral evolution"          
    ##  [5] "phylogenetic relationships" "abroma augusta"            
    ##  [7] "chemical investigation"     "cocoa beans"               
    ##  [9] "genus waltheria"            "medicinal plants"          
    ## [11] "study aimed"                "theobroma cacao"           
    ## [13] "waltheria douradinha"       "waltheria indica"

``` r
# Keep only shortest unique substrings
superstring = rep(FALSE, length(gs_maxselected_terms))
for(i in seq_len(length(gs_maxselected_terms))) {
superstring[i] = any(stringr::str_detect(gs_maxselected_terms[i], gs_maxselected_terms[-which(gs_maxselected_terms == gs_maxselected_terms[i])]))
}
gs_selected_terms = gs_maxselected_terms[!superstring]
cat("\nSelected terms as short substrings: \n")
```

    ## 
    ## Selected terms as short substrings:

``` r
print(gs_selected_terms)
```

    ##  [1] "floral morphology"          "pollen morphology"         
    ##  [3] "family sterculiaceae"       "floral evolution"          
    ##  [5] "phylogenetic relationships" "abroma augusta"            
    ##  [7] "chemical investigation"     "cocoa beans"               
    ##  [9] "genus waltheria"            "medicinal plants"          
    ## [11] "study aimed"                "theobroma cacao"           
    ## [13] "waltheria douradinha"       "waltheria indica"

# Manually add and remove substrings

# TO DO

``` r
# Manually remove all unwanted substrings 
to_remove = c("genus waltheria", "study aimed", "abroma augusta", "family sterculiaceae", "waltheria indica", "waltheria douradinha", "theobroma cacao") # TO DO: Modify this list based on terms displayed above that you do not want to search using
gs_selected_terms = gs_selected_terms[!gs_selected_terms %in% to_remove] 
genera = c("Hermannieae", "Lasiopetaleae", "Bytttnerieae", "Theobromeae", "Waltheria", "Hermannia", "Melochia", "Hannafordia", "Maxwellia", "Lasiopetalum", "Thomasia", "Guichenotia", "Commersonia",  "Rulingia", "Keraudrenia", "Seringia", "Abroma", "Byttneria", "Ayenia", "Rayleya", "Kleinhovia",  "Leptonychia", "Scaphopetalum", "Theobroma", "Herrania", "Glossostemon", "Guazuma") # TO DO: Add any missing genera, if any
families = c("Byttnerioideae", "Sterculiaceae")
topics = c("morphology", "evolution", "ecology", "phylogenetics") # TO DO: Add any relevant topics
to_add = c(genera, families, topics)
gs_selected_terms = c(gs_selected_terms, to_add)
cat("\nFinal list of terms: \n")
```

    ## 
    ## Final list of terms:

``` r
print(gs_selected_terms)
```

    ##  [1] "floral morphology"          "pollen morphology"         
    ##  [3] "floral evolution"           "phylogenetic relationships"
    ##  [5] "chemical investigation"     "cocoa beans"               
    ##  [7] "medicinal plants"           "Hermannieae"               
    ##  [9] "Lasiopetaleae"              "Bytttnerieae"              
    ## [11] "Theobromeae"                "Waltheria"                 
    ## [13] "Hermannia"                  "Melochia"                  
    ## [15] "Hannafordia"                "Maxwellia"                 
    ## [17] "Lasiopetalum"               "Thomasia"                  
    ## [19] "Guichenotia"                "Commersonia"               
    ## [21] "Rulingia"                   "Keraudrenia"               
    ## [23] "Seringia"                   "Abroma"                    
    ## [25] "Byttneria"                  "Ayenia"                    
    ## [27] "Rayleya"                    "Kleinhovia"                
    ## [29] "Leptonychia"                "Scaphopetalum"             
    ## [31] "Theobroma"                  "Herrania"                  
    ## [33] "Glossostemon"               "Guazuma"                   
    ## [35] "Byttnerioideae"             "Sterculiaceae"             
    ## [37] "morphology"                 "evolution"                 
    ## [39] "ecology"                    "phylogenetics"

# Manual grouping into clusters - for more rigorous search we will need a combination of OR and AND operators

``` r
topics = c(topics, gs_selected_terms[c(1:7)]) # TO DO: Make sure the indices for all returned topics are shown here
taxa = c(families, genera)

# Check that all terms were grouped
all.equal(length(gs_selected_terms), sum(length(taxa), length(topics)))
```

    ## [1] TRUE

``` r
# Make an ordered list by family, genus, and topic
gs_grouped_selected_terms <- list(
taxa = taxa,
topics = topics
)
```

# Automatically write the search string

``` r
# Write the search
search_string = litsearchr::write_search(
gs_grouped_selected_terms,
languages = "English",
exactphrase = TRUE,
stemming = FALSE,
closure = "left",
writesearch = FALSE
)
```

    ## [1] "English is written"

``` r
cat("\nSearch string: \n")
```

    ## 
    ## Search string:

``` r
print(search_string)
```

    ## [1] "((Byttnerioideae OR Sterculiaceae OR Hermannieae OR Lasiopetaleae OR Bytttnerieae OR Theobromeae OR Waltheria OR Hermannia OR Melochia OR Hannafordia OR Maxwellia OR Lasiopetalum OR Thomasia OR Guichenotia OR Commersonia OR Rulingia OR Keraudrenia OR Seringia OR Abroma OR Byttneria OR Ayenia OR Rayleya OR Kleinhovia OR Leptonychia OR Scaphopetalum OR Theobroma OR Herrania OR Glossostemon OR Guazuma) AND (morphology OR evolution OR ecology OR phylogenetics OR \"floral morphology\" OR \"pollen morphology\" OR \"floral evolution\" OR \"phylogenetic relationships\" OR \"chemical investigation\" OR \"cocoa beans\" OR \"medicinal plants\"))"
