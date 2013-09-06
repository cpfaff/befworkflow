---
title       : Workflow
subtitle    : BEFdata + rbefdata 
author      : Claas-Thido Pfaff
job         : 
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
github      : 
  user      : cpfaff
  repo      : befworkflow
---




## Install packages

* devtools (devel + install github)


```r
install.packages("devtools")
```


* Trim (leading/trailing spaces)


```r
install.packages("gdata")
```


* taxize (taxonomy tasks)


```r
install_github("taxize_", "ropensci")
```


* rbefdata (interface to BEFdata)


```r
remove.packages("rbefdata")
install_github(username = "befdata", repo = "rbefdata", subdir = "rbefdata")
```


---

## List Options (rbefdata)



```r
require(rbefdata)
bef.options()
```

```
## $url
## [1] "http://china.befdata.biow.uni-leipzig.de"
## 
## $tematres_url
## [1] "http://tematres.befdata.biow.uni-leipzig.de/vocab/index.php"
## 
## $tematres_service_url
## [1] "http://tematres.befdata.biow.uni-leipzig.de/vocab/services.php"
## 
## $download_dir
## [1] "downloads"
## 
## $user_credentials
## [1] ""
```


---

## Set Options (rbefdata)



- The URL ([Workshop Portal](http://stage.china.befdata.biow.uni-leipzig.de))


```r
bef.options(url = "http://stage.china.befdata.biow.uni-leipzig.de")
```


- The credentials ([your profile](http://stage.china.befdata.biow.uni-leipzig.de/profile))


```r
bef.options(user_credentials = "BCeqHdtGA7v2MHd15l")
```


---
  
## Get data
  
* Get dataset from [proposal](http://stage.china.befdata.biow.uni-leipzig.de/paperproposals/1) with id #1 


```r
datasets = bef.portal.get.datasets_for_proposal(id = 1)
length(datasets)
```

```
## [1] 2
```

```r
titles = sapply(datasets, function(x) attributes(x)$title)
titles
```

```
## [1] "Abundance of Tree and Shrub species in the Comparative Study Plots (CSP) - BEFdata tutorial"                 
## [2] "Abundance of Tree and Shrub species in the Comparative Study Plots (CSP) - BEFdata tutorial misspelt species"
```

```r
dataset = datasets[[2]]
```


---

## Inspect the data

* Inspect the dataset (metadata)
  

```r
attributes(dataset)$title
```

```
## [1] "Abundance of Tree and Shrub species in the Comparative Study Plots (CSP) - BEFdata tutorial misspelt species"
```

```r
names(attributes(dataset))
```

```
##  [1] "names"                    "class"                    "row.names"                "title"                    "abstract"                
##  [6] "publicationDate"          "language"                 "creators"                 "authors"                  "intellectualRights"      
## [11] "distribution"             "keywords"                 "generalTaxonomicCoverage" "samplingDescription"      "spatial_coverage"        
## [16] "temporal_coverage"        "related_material"         "columns"
```


---

## Inspect the data

* About the spces column and their description


```r
names(dataset)
```

```
## [1] "year"      "csp"       "species"   "abundance"
```

```r
attributes(dataset)$column$description
```

```
## [1] "Date time information, given as year or as date or as date time. (year: first year of the releve; Datagroup description: Date time information)"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                
## [2] "Reasearch plots of the Biodiversity - Ecosystem functioning experiment (BEF-China). There are three main sites for research plots in the BEF Experiment: Comparative Study Plots (CSP) in the  Gutianshan Nature Reserve, having a size of 30x30m^2, measured on the ground. Main Experiment plots have a size of 1 mu, which is about 25x25m^2 in horizontal projection. Pilot Study Plots have a size of 1x1 m^2.  \n\nResearch plots on the main experiment have a \"p\" in front of their IDs and then a 6 digit code: Plots in the main sites A and B are named according to their position in the original spreadsheet, in which they were designed.  They consist of 6 digits: _1st digit_: Site (1:A, 2:B), _digit 2and3_: southwards row: as in spreadsheets the rows are named from the top to the bottom; _digit 4 and 5_: westward column: as in the original spreadsheet, but the letters are converted to numbers (A=01, B=02); _6th digit_: indicator, if the plot has been shifted a quarter mu.  Example: \"p205260\": \"p\" means that this is a plot that is specified.  \"2\" means, that we are at site B.  Now the coordinates of the south - west corner: \"0526\".  Since \"e\" is the fifth letter of the alphabet, this is Plot E26.   The last digit \"0\" means that this plot was not moved by a quarter of a Mu, as some sites in Site A. The 6th digit can also indicate the subplot within the plot. \"5\", \"6\", \"7\", \"8\" indicate the northwest, northeast, southeast, and southwest quarter plot respectively. (csp: name of the comparative study plot; Datagroup description: BEF research plot name)"
## [3] "The scientific species fullnames are based on the \"Flora of China\" identified by Teng Fang and verified by Helge Bruelheide (trees) and Alexandra Efmeier (herbs). Scientific species names consist of epithet and genus. For identification, the Author name and the year of the publications of the description are required. (species: full name of the species, genus and epiteth; Datagroup description: Scientific plant species name)"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
## [4] "Counting the individuals of a given taxon, also called abundance. (abundance: abundance of the species; Datagroup description: Organism count)"
```


---

## Species names

* Prepare species list


```r
require(gdata)
species = trim(dataset$species)
unique_species = unique(as.character(species))
head(unique_species, 7)
```

```
## [1] "Acer amplum"         "Acer cordatum"       "Acer pubipalmatum"   "Adinandra millettii" "Adinandra illettii"  "Alangium urzii"     
## [7] "Alangium kurzii"
```


* Load ([taxize](http://ropensci.org/tutorials/taxize_tutorial.html))


```r
require(taxize)
```


---

## Species names

* Taxize offers access to databases like: 
    - Integrated Taxonomic Information System [itis](http://www.itis.gov/) 
    - National Center for Biotechnology Information [ncbi](http://www.ncbi.nlm.nih.gov/) 
    - International Union for Conservation of Nature [iucn](http://www.iucn.org/)

* But that is not all (e.g create phylogeny tree, see [tutorial](http://ropensci.org/tutorials/taxize_tutorial.html))

* Example for species resolving


```r
species_list = c("Helianthus annuus", "Pinus contort")
query_response = tnrs(query = species_list)[, -c(6:7)]
query_response
```

```
##       submittedName      acceptedName    sourceId score       matchedName
## 2 Helianthus annuus Helianthus annuus iPlant_TNRS     1 Helianthus annuus
## 3 Helianthus annuus Helianthus annuus        NCBI     1 Helianthus annuus
## 1     Pinus contort    Pinus contorta iPlant_TNRS  0.98    Pinus contorta
```


--- bg:#EEE

## Your turn

* Resolve all the species in vector unique_species


```r
tnrs(query = ...)
```


* Find the entries that need correction (Hint: score!)

---

## A solution 

* Check the unique names of our species list


```r
query_response = tnrs(query = unique_species)[, -c(6:7)]
head(query_response, 9)[c("submittedName", "acceptedName", "score")]
```

```
##           submittedName        acceptedName score
## 2           Acer amplum         Acer amplum     1
## 153       Acer cordatum       Acer cordatum     1
## 154       Acer cordatum       Acer cordatum     1
## 190   Acer pubipalmatum    Acer pauciflorum     1
## 129 Adinandra millettii Adinandra millettii     1
## 130 Adinandra millettii Adinandra millettii     1
## 289  Adinandra illettii Adinandra millettii  0.98
## 90       Alangium urzii     Alangium kurzii  0.97
## 182     Alangium kurzii     Alangium kurzii     1
```


--- 

## A solution 

* Pick the entries that are lower than 1


```r
index_of_need_attention = which(query_response$score < 1)
subset_that_needs_attention = unique(query_response[index_of_need_attention, ])
subset_that_needs_attention
```

```
##                     submittedName                 acceptedName    sourceId score                  matchedName
## 289            Adinandra illettii          Adinandra millettii iPlant_TNRS  0.98          Adinandra millettii
## 90                 Alangium urzii              Alangium kurzii iPlant_TNRS  0.97              Alangium kurzii
## 5             Camelliana fraterna            Camellia fraterna iPlant_TNRS  0.96            Camellia fraterna
## 133 Cyclobalanopsis myrsinaefolia Cyclobalanopsis myrsinifolia iPlant_TNRS  0.97 Cyclobalanopsis myrsinifolia
## 131            Glochidion puberum                              iPlant_TNRS  0.95         Glochidion puberulum
## 120                  Ilex pururea               Ilex chinensis iPlant_TNRS  0.98                Ilex purpurea
## 1             Loropetalum chinens         Loropetalum chinense iPlant_TNRS  0.98         Loropetalum chinense
## 243              Prunus discoidea                       Prunus iPlant_TNRS   0.5                       Prunus
## 163         Quercus phillyreoides       Quercus phillyraeoides iPlant_TNRS  0.99       Quercus phillyraeoides
## 61               Quercus serratas              Quercus serrata iPlant_TNRS  0.98              Quercus serrata
## 101        Symplocos oblongifolia                    Symplocos iPlant_TNRS   0.5                    Symplocos
```


---

## A solution 

* Pick the entries that are lower 1 and bigger 0.5


```r
index_of_need_attention = which(query_response$score < 1 & query_response$score > 0.5)
subset_that_needs_attention = unique(query_response[index_of_need_attention, ])
subset_that_needs_attention
```

```
##                     submittedName                 acceptedName    sourceId score                  matchedName
## 289            Adinandra illettii          Adinandra millettii iPlant_TNRS  0.98          Adinandra millettii
## 90                 Alangium urzii              Alangium kurzii iPlant_TNRS  0.97              Alangium kurzii
## 5             Camelliana fraterna            Camellia fraterna iPlant_TNRS  0.96            Camellia fraterna
## 133 Cyclobalanopsis myrsinaefolia Cyclobalanopsis myrsinifolia iPlant_TNRS  0.97 Cyclobalanopsis myrsinifolia
## 131            Glochidion puberum                              iPlant_TNRS  0.95         Glochidion puberulum
## 120                  Ilex pururea               Ilex chinensis iPlant_TNRS  0.98                Ilex purpurea
## 1             Loropetalum chinens         Loropetalum chinense iPlant_TNRS  0.98         Loropetalum chinense
## 163         Quercus phillyreoides       Quercus phillyraeoides iPlant_TNRS  0.99       Quercus phillyraeoides
## 61               Quercus serratas              Quercus serrata iPlant_TNRS  0.98              Quercus serrata
```


--- 

## Match back and combine

* Match species to subset submitted names


```r
match_index = match(as.character(species), subset_that_needs_attention$submittedName)
```


*  Create new vector containing the corrections


```r
corrected_names = subset_that_needs_attention$matchedName[match_index]
```


---

## Match back and combine 

* merge function picks from vectors (if else)


```r
merge_em <- function(base_vec, merge_vec) {
    output = vector()
    for (i in 1:length(base_vec)) {
        if (is.na(base_vec[i])) {
            output[i] = merge_vec[i]
        } else {
            output[i] = base_vec[i]
        }
    }
    return(output)
}
```


* Define base and merge vector


```r
base = corrected_names
mergein = as.character(species)
```


---

## Match back and combine

* create new dataframe


```r
merged_names = merge_em(base_vec = base, merge_vec = mergein)
corrected_dataset = data.frame(dataset, species_corrected = merged_names)
head(corrected_dataset, 15)
```

```
##    year   csp             species abundance   species_corrected
## 1  2008 CSP18         Acer amplum         2         Acer amplum
## 2  2008 CSP03         Acer amplum         1         Acer amplum
## 3  2008 CSP24         Acer amplum         1         Acer amplum
## 4  2008 CSP20         Acer amplum         1         Acer amplum
## 5  2008 CSP21       Acer cordatum         1       Acer cordatum
## 6  2008 CSP21       Acer cordatum         1       Acer cordatum
## 7  2008 CSP17       Acer cordatum         1       Acer cordatum
## 8  2008 CSP20       Acer cordatum         2       Acer cordatum
## 9  2008 CSP13       Acer cordatum         1       Acer cordatum
## 10 2008 CSP18       Acer cordatum         6       Acer cordatum
## 11 2008 CSP17   Acer pubipalmatum         1   Acer pubipalmatum
## 12 2008 CSP06 Adinandra millettii       123 Adinandra millettii
## 13 2008 CSP15 Adinandra millettii        19 Adinandra millettii
## 14 2008 CSP02 Adinandra millettii        35 Adinandra millettii
## 15 2008 CSP18 Adinandra millettii         2 Adinandra millettii
```


---

## Upload dataset

* push back a corrected version to BEFdata

* Please use own title if you execute this step 


```r
title = "Abundance of Tree and Shrub species in the Comparative Study Plots (CSP) - BEFdata tutorial (species corrected)"
bef.portal.upload.dataset(dataset = corrected_dataset, dataset_title = title)
```


---

## Get higher taxonomic names

* Use taxize to retrive higher taxon names

* Create a new dataframe 
     - corrected unique species names 
     - order
     - family
     - genus

* Can be used to calculate phylogenetic diversity

---

## Get higher taxonomic names

* Example on retrieving classifications


```r
species_examples = c("Homo sapiens", "Rattus rattus")
```


* The databases can be e.g. [itis](http://www.itis.gov/) or [ncbi](http://www.ncbi.nlm.nih.gov/)


```r
classification_list = classification(species_examples, db = "itis")
classification_list[[1]]
```

```
##      rankName    taxonName    tsn
## 1     Kingdom     Animalia 202423
## 2      Phylum     Chordata 158852
## 3   Subphylum   Vertebrata 331030
## 4       Class     Mammalia 179913
## 5    Subclass       Theria 179916
## 6  Infraclass     Eutheria 179925
## 7       Order     Primates 180089
## 8      Family    Hominidae 180090
## 9       Genus         Homo 180091
## 10    Species Homo sapiens 180092
```


---

## Get higher taxonomic names

* Get only parts of intrest


```r
tax_name(query = "Homo Sapiens", get = "genus", db = "ncbi")
```

```
##   genus
## 1  Homo
```

```r
tax_name(query = species_examples, get = "family", db = "ncbi")
```

```
##      family
## 1 Hominidae
## 2   Muridae
```


--- bg:#EEE

## Your turn

* Get the order, family and genus from ncbi for unique corrected species
    
* Be nice and only query server once!
    - do not use: tax_name(...)
    - use classification(...)

* Hint: use R apply functions
    
* When finished upload the dataset to the portal!

---

## A solution

* Get: 
    - order, family and genus (unique corrected species use "ncbi")


```r
species_list = unique(as.character(corrected_dataset$species_corrected))
classification_species = classification(species_list, db = "ncbi")
```


* Extract (order):


```r
extraction_ncbi_order = sapply(classification_species, function(x) if (length(x) != 3) {
    NA
} else {
    subset(x, x$Rank == "order")
}$ScientificName)
```

---

## A solution

* Extract (family):


```r
extraction_ncbi_family = sapply(classification_species, function(x) if (length(x) != 3) {
    NA
} else {
    subset(x, x$Rank == "family")
}$ScientificName)
```


* Extract (genus):


```r
extraction_ncbi_genus = sapply(classification_species, function(x) if (length(x) != 3) {
    NA
} else {
    subset(x, x$Rank == "genus")
}$ScientificName)
```


---

## A solution

* Create data frame:


```r
extraction_ncbi_order_fam_genus = cbind(extraction_ncbi_order, extraction_ncbi_family, extraction_ncbi_genus)
species_taxonic = data.frame(species = species_list, extraction_ncbi_order_fam_genus)
head(species_taxonic, 5)
```

```
##                                 species extraction_ncbi_order extraction_ncbi_family extraction_ncbi_genus
## Acer amplum                 Acer amplum                    NA                     NA                    NA
## Acer cordatum             Acer cordatum            Sapindales              Aceraceae                  Acer
## Acer pubipalmatum     Acer pubipalmatum                    NA                     NA                    NA
## Adinandra millettii Adinandra millettii              Ericales       Pentaphylacaceae             Adinandra
## Alangium kurzii         Alangium kurzii              Cornales              Cornaceae              Alangium
```


* Upload:


```r
bef.portal.upload.dataset(dataset = species_taxonic, dataset_title = "Taxonomic information for species in ...")
```


---

## Thanks four your attention
