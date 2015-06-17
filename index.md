---
title       : Roma province population by town
subtitle    : Developing Data Products
author      : Max Testa
job         : Data science learner
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : github        # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
runtime     : shiny
logo        : pvroma.jpg
---

## Summary

In my project I developed an interactive R web application implemented using a very powerful tool: [Shiny](http://shiny.rstudio.com/). The app is hosted on [shinyapps](https://maxmax65.shinyapps.io/DataProductProject) thanks to RStudio's kind support.

The application shows the trend of Roma province population from 2002 to 2011 giving the user the possibility to select one of the 121 towns/cities of the province and whether to present either the total numbers of Female and Male, or only the numbers related to one of the two sexes.

The files with the code and the data for the project are on [this repo on github](https://github.com/maxmax65/DataProductProject.git)

The file with the population of Rome province is retrieved from the [Italian Government open data repository](http://www.dati.gov.it/dataset).

The R object with the boundaries of Italian "Comuni" (level 3 map of Italy) is downloaded from the [GADM database](http://gadm.org/download).

--- .class #id 

## The data


```
## The dataset 'PopEtaAnno' with population information has dimension:  1210  x  6
```

<table>
<caption>'PopEtaAnno' dataset short summary</caption>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:left;">    Comune </th>
   <th style="text-align:left;">     Total </th>
   <th style="text-align:left;">      Male </th>
   <th style="text-align:left;">     Female </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Length:1210 </td>
   <td style="text-align:left;"> Min.   :    159 </td>
   <td style="text-align:left;"> Min.   :     91 </td>
   <td style="text-align:left;"> Min.   :     64.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Class :character </td>
   <td style="text-align:left;"> 1st Qu.:   1198 </td>
   <td style="text-align:left;"> 1st Qu.:    599 </td>
   <td style="text-align:left;"> 1st Qu.:    603.5 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Mode  :character </td>
   <td style="text-align:left;"> Median :   4810 </td>
   <td style="text-align:left;"> Median :   2341 </td>
   <td style="text-align:left;"> Median :   2461.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:left;"> Mean   :  32528 </td>
   <td style="text-align:left;"> Mean   :  15507 </td>
   <td style="text-align:left;"> Mean   :  17021.1 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:left;"> 3rd Qu.:  13226 </td>
   <td style="text-align:left;"> 3rd Qu.:   6582 </td>
   <td style="text-align:left;"> 3rd Qu.:   6658.0 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:left;"> Max.   :2761477 </td>
   <td style="text-align:left;"> Max.   :1301763 </td>
   <td style="text-align:left;"> Max.   :1459714.0 </td>
  </tr>
</tbody>
</table>


The Roma province map is selected as a subset (gadm$NAME_2=="Roma") of the whole Italy Level3 map; some cleansing is then performed on it to align the names of the "Comuni" ("NAME_3" var) to those of the population dataset ("Comune" variable). Then the "ProvRM" object is saved as "ProvRM_map.RDS" using saveRDS() function in order to make it available to "server.UI".


--- .class #id 

## The power of shiny (1/2)

The shinyUI component uses fluidPage() instruction to make the sidebar and the mainbar elements adaptable to the browser window.

Via sidebar instructions the user is asked to select the town (identified as "input$town") and/or the type of information (identified as "input$M_F_MF") to display: the default choices are on "Roma", the Italian capital, and on "Male+Female".
The mainbar's only instruction calls the funzione plotOutput() that displays the barplot and the map produced by the shinyServer component.

The shinyServer component loads the demographic and geographic data and, based on the inputs collected by the UI, prepares the appropriate information, rendered and presented by the UI.

--- .class #id 

## The power of shiny (2/2)

This is a simplified version of what the shinyUI code of the app presents to let the user make his choices, on which the shinyServer component prepares the objects to show in the UI


<!--html_preserve--><div class="container-fluid">
<h2>Population of Roma province</h2>
<div class="row">
<div class="col-sm-4">
<form class="well">
Select the type of info and/or the town you are interested in
<br/>
<br/>
<div id="M_F_MF" class="form-group shiny-input-radiogroup shiny-input-container">
<label class="control-label" for="M_F_MF">Choose the info to show:</label>
<div class="shiny-options-group">
<div class="radio">
<label>
<input type="radio" name="M_F_MF" value="Female"/>
<span>Female</span>
</label>
</div>
<div class="radio">
<label>
<input type="radio" name="M_F_MF" value="Male"/>
<span>Male</span>
</label>
</div>
<div class="radio">
<label>
<input type="radio" name="M_F_MF" value="Male+Female" checked="checked"/>
<span>Male+Female</span>
</label>
</div>
</div>
</div>
<div class="form-group shiny-input-container">
<label class="control-label" for="town">Choose a town to display</label>
<div>
<select id="town"><option value="Affile" selected>Affile</option>
<option value="Agosta">Agosta</option>
<option value="Albano Laziale">Albano Laziale</option>
<option value="Allumiere">Allumiere</option>
<option value="Anguillara Sabazia">Anguillara Sabazia</option>
<option value="Anticoli Corrado">Anticoli Corrado</option>
<option value="Anzio">Anzio</option>
<option value="Arcinazzo Romano">Arcinazzo Romano</option>
<option value="Ardea">Ardea</option>
<option value="Ariccia">Ariccia</option>
<option value="Arsoli">Arsoli</option>
<option value="Artena">Artena</option>
<option value="Bellegra">Bellegra</option>
<option value="Bracciano">Bracciano</option>
<option value="Camerata Nuova">Camerata Nuova</option>
<option value="Campagnano di Roma">Campagnano di Roma</option>
<option value="Canale Monterano">Canale Monterano</option>
<option value="Canterano">Canterano</option>
<option value="Capena">Capena</option>
<option value="Capranica Prenestina">Capranica Prenestina</option>
<option value="Carpineto Romano">Carpineto Romano</option>
<option value="Casape">Casape</option>
<option value="Castel Gandolfo">Castel Gandolfo</option>
<option value="Castel Madama">Castel Madama</option>
<option value="Castel San Pietro Romano">Castel San Pietro Romano</option>
<option value="Castelnuovo di Porto">Castelnuovo di Porto</option>
<option value="Cave">Cave</option>
<option value="Cerreto Laziale">Cerreto Laziale</option>
<option value="Cervara di Roma">Cervara di Roma</option>
<option value="Cerveteri">Cerveteri</option>
<option value="Ciampino">Ciampino</option>
<option value="Ciciliano">Ciciliano</option>
<option value="Cineto Romano">Cineto Romano</option>
<option value="Civitavecchia">Civitavecchia</option>
<option value="Civitella San Paolo">Civitella San Paolo</option>
<option value="Colleferro">Colleferro</option>
<option value="Colonna">Colonna</option>
<option value="Fiano Romano">Fiano Romano</option>
<option value="Filacciano">Filacciano</option>
<option value="Fiumicino">Fiumicino</option>
<option value="Fonte Nuova">Fonte Nuova</option>
<option value="Formello">Formello</option>
<option value="Frascati">Frascati</option>
<option value="Gallicano nel Lazio">Gallicano nel Lazio</option>
<option value="Gavignano">Gavignano</option>
<option value="Genazzano">Genazzano</option>
<option value="Genzano di Roma">Genzano di Roma</option>
<option value="Gerano">Gerano</option>
<option value="Gorga">Gorga</option>
<option value="Grottaferrata">Grottaferrata</option>
<option value="Guidonia Montecelio">Guidonia Montecelio</option>
<option value="Jenne">Jenne</option>
<option value="Labico">Labico</option>
<option value="Ladispoli">Ladispoli</option>
<option value="Lanuvio">Lanuvio</option>
<option value="Lariano">Lariano</option>
<option value="Licenza">Licenza</option>
<option value="Magliano Romano">Magliano Romano</option>
<option value="Mandela">Mandela</option>
<option value="Manziana">Manziana</option>
<option value="Marano Equo">Marano Equo</option>
<option value="Marcellina">Marcellina</option>
<option value="Marino">Marino</option>
<option value="Mazzano Romano">Mazzano Romano</option>
<option value="Mentana">Mentana</option>
<option value="Monte Porzio Catone">Monte Porzio Catone</option>
<option value="Montecompatri">Montecompatri</option>
<option value="Monteflavio">Monteflavio</option>
<option value="Montelanico">Montelanico</option>
<option value="Montelibretti">Montelibretti</option>
<option value="Monterotondo">Monterotondo</option>
<option value="Montorio Romano">Montorio Romano</option>
<option value="Moricone">Moricone</option>
<option value="Morlupo">Morlupo</option>
<option value="Nazzano">Nazzano</option>
<option value="Nemi">Nemi</option>
<option value="Nerola">Nerola</option>
<option value="Nettuno">Nettuno</option>
<option value="Olevano Romano">Olevano Romano</option>
<option value="Palestrina">Palestrina</option>
<option value="Palombara Sabina">Palombara Sabina</option>
<option value="Percile">Percile</option>
<option value="Pisoniano">Pisoniano</option>
<option value="Poli">Poli</option>
<option value="Pomezia">Pomezia</option>
<option value="Ponzano Romano">Ponzano Romano</option>
<option value="Riano">Riano</option>
<option value="Rignano Flaminio">Rignano Flaminio</option>
<option value="Riofreddo">Riofreddo</option>
<option value="Rocca Canterano">Rocca Canterano</option>
<option value="Rocca di Cave">Rocca di Cave</option>
<option value="Rocca di Papa">Rocca di Papa</option>
<option value="Rocca Priora">Rocca Priora</option>
<option value="Rocca Santo Stefano">Rocca Santo Stefano</option>
<option value="Roccagiovine">Roccagiovine</option>
<option value="Roiate">Roiate</option>
<option value="Roma">Roma</option>
<option value="Roviano">Roviano</option>
<option value="Sacrofano">Sacrofano</option>
<option value="Sambuci">Sambuci</option>
<option value="San Cesareo">San Cesareo</option>
<option value="San Gregorio da Sassola">San Gregorio da Sassola</option>
<option value="San Polo dei Cavalieri">San Polo dei Cavalieri</option>
<option value="San Vito Romano">San Vito Romano</option>
<option value="Sant'Angelo Romano">Sant'Angelo Romano</option>
<option value="Sant'Oreste">Sant'Oreste</option>
<option value="Santa Marinella">Santa Marinella</option>
<option value="Saracinesco">Saracinesco</option>
<option value="Segni">Segni</option>
<option value="Subiaco">Subiaco</option>
<option value="Tivoli">Tivoli</option>
<option value="Tolfa">Tolfa</option>
<option value="Torrita Tiberina">Torrita Tiberina</option>
<option value="Trevignano Romano">Trevignano Romano</option>
<option value="Vallepietra">Vallepietra</option>
<option value="Vallinfreda">Vallinfreda</option>
<option value="Valmontone">Valmontone</option>
<option value="Velletri">Velletri</option>
<option value="Vicovaro">Vicovaro</option>
<option value="Vivaro Romano">Vivaro Romano</option>
<option value="Zagarolo">Zagarolo</option></select>
<script type="application/json" data-for="town" data-nonempty="">{}</script>
</div>
</div>
</form>
</div>
<div class="col-sm-8"></div>
</div>
</div><!--/html_preserve-->
