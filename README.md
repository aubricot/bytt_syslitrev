# Byttnerioideae Systematic Literature Review
Accompanying code for a systematic literature review of morphology and evolution in the Byttnerioideae (cacao and its relatives) using R Markdown.   
*Last edited on 27 December 2023*   

<p align="center">
<a href="url"><img src="https://github.com/aubricot/bytt_syslitrev/blob/main/bytt_sysrev_banner.png" align="middle" width="900" ></a></p>   

<p align="center">
<sub>Some examples of unique flower morphologies of the Byttnerioideae. From left to right: West Indian Elm (<i>Guazuma ulmifolia</i>), <i>Byttneria microphylla</i>, Wild Colombian Cacao (<i>Herrania umbratica</i>), Cacao, the source of chocolate (<i>Theobroma cacao</i>), and Cupuaçu (<i>Theobroma grandiflorum</i>).</sub>

## Introduction
Use [sys_lit_rev.Rmd](https://github.com/aubricot/bytt_syslitrev/blob/main/sys_lit_rev.Rmd) to carry out a systematic literature search using the package [litsearchr](https://elizagrames.github.io/litsearchr/) on a Mac OS in R 4.3.2. We are investigating the morphology and evolution of the Byttnerioideae, a taxonomically challenging clade within the Malvaceae ([Bayer et al. 2008](https://doi.org/10.1111/j.1095-8339.1999.tb00505.x); [Whitlock et al. 2001](https://doi.org/10.1043/0363-6445-26.2.420); [Alverson et al. 1999](https://doi.org/10.2307/2656928)). Look for sections of the code with "TO DO" for areas where you will need to customize what is written based on your file directories and literature search choices.

First, you will use some manually selected keywords for preliminary searches. Then, keywords will be automatically extracted from the abstracts found during the preliminary passes. Finally, carry out a systematic literature search using the complete list of keywords and analyze patterns in found articles.

:point_right: :hibiscus: To see a rendered version (in progress) of the current R Markdown file with outputs on RPubs, [click here](http://rpubs.com/aubricot/byt_syslitrev).

## References 
<ins>Botanical Literature</ins>
* Alverson et al. 1999. Support for an expanded family concept of Malvaceae within a recircumscribed order Malvales: a combined analysis of plastid atpB and rbcL DNA sequences. Botanical Journal of the Linnean Society. https://doi.org/10.1111/j.1095-8339.1999.tb00505.x    
* Bayer et al. 2008. Phylogeny of the core Malvales: evidence from ndhF sequence data. Systematics. https://doi.org/10.2307/2656928   
* Whitlock et al. 2001. Phylogenetic Relationships and Floral Evolution of the Byttnerioideae (“Sterculiaceae” or Malvaceae s.l.) Based on Sequences of the Chloroplast Gene, ndhF. Systematic Botany. https://doi.org/10.1043/0363-6445-26.2.420

<ins>Coding Tutorials and Documentation</ins>   
These steps were made following a tutorial from https://www.r-bloggers.com/2023/03/automated-systematic-literature-search-using-r-litsearchr-and-google-scholar-web-scraping/   

The following links may also be useful:
* https://elizagrames.github.io/litsearchr/#tutorials
* https://elizagrames.github.io/litsearchr/introduction_vignette_v010.html
