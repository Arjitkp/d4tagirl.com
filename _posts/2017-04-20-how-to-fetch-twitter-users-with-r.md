---
layout: post
title:  How to fetch Twitter users with R
date: "2017-04-20 12:11:29 UYT"
published: true
tags: [rstats, r, Twitter, rtweet, purrr, map, ggmap]
description: How to fetch Twitter users and clean the data using R!
---
Here I show how to fetch Twitter users using the `rtweet` package, and clean the data using the `tidyverse` set of packages, for later usage in plotting animated maps.  

<!--more-->

Recently [I came across this post](http://spatial.ly/2017/03/mapping-5000-years-of-city-growth/), and I knew I had to make a similar map for the [R-Ladies' chapters](http://rladies.org/) (probably the purple color had plenty to do with that!). So my idea was to map all the R-Ladies' chapters according to their size, and that's when I thought of using their Twitter followers as a way to estimate it. 

If I wanted to show everything I've done in a single post, it would be almost as long as my first one! And I didn't want that :P So I decided to make 2 _tutorial-like_ posts: one for the data preparation (this very one), and the second one about making the maps and animating them. And finally other post where I don't go into too much detail about everything.

So here I go!

# Getting Twitter users

I had to learn how to retrieve data from the Twitter API, and I chose to use the `rtweet` package, which is super easy to use! 

Every R-Ladies' chapter uses a standard handle, using the *RLadiesLocation* format (thankfully they are very compliant with this!). By setting the `q` parameter to `'RLadies'` I'm setting the query to be searched. `n = 1000` sets the amount of users to retrieve, being 1000 the maximum number of users returned from a single search. As I want a dataframe as a result, I set the `parse` parameter to `TRUE`.

Since I only use public data I don't have to worry about getting my Twitter personal access token (for now at least).




```r
library(rtweet)

users <- search_users(q = 'RLadies',
                      n = 1000,
                      parse = TRUE)
```

Let's see what it returns:


```r
library(knitr)

kable(head(users[, c(2:5)]), format = "html")
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> name </th>
   <th style="text-align:left;"> screen_name </th>
   <th style="text-align:left;"> location </th>
   <th style="text-align:left;"> description </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> R-Ladies Global </td>
   <td style="text-align:left;"> RLadiesGlobal </td>
   <td style="text-align:left;"> The World </td>
   <td style="text-align:left;"> Promoting Gender Diversity in the #rstats community via meetups, mentorship &amp; global collaboration! 30+ groups worldwide: US|Europe|Oceania|LatAm|Asia #RLadies </td>
  </tr>
  <tr>
   <td style="text-align:left;"> R-Ladies SF </td>
   <td style="text-align:left;"> RLadiesSF </td>
   <td style="text-align:left;"> San Francisco </td>
   <td style="text-align:left;"> R-Ladies is the first group dedicated to women and R. It was founded in Oct 2012 to promote knowledge, support and inclusivity. #rstats #RLadies </td>
  </tr>
  <tr>
   <td style="text-align:left;"> R-Ladies London </td>
   <td style="text-align:left;"> RLadiesLondon </td>
   <td style="text-align:left;"> London, England </td>
   <td style="text-align:left;"> The first R programming meetup for Minority Genders in the UK! Promoting Diversity &amp; Inclusivity in STEM/Data Science
london@rladies.org 
#RLadiesLondon #rstats </td>
  </tr>
  <tr>
   <td style="text-align:left;"> R-Ladies RTP </td>
   <td style="text-align:left;"> RLadiesRTP </td>
   <td style="text-align:left;"> Durham, NC </td>
   <td style="text-align:left;"> NA </td>
  </tr>
  <tr>
   <td style="text-align:left;"> R-Ladies Connecticut </td>
   <td style="text-align:left;"> RLadiesCT </td>
   <td style="text-align:left;"> Connecticut, USA </td>
   <td style="text-align:left;"> Promoting &amp; connecting women (and self-identified) interested in R programming throughout Connecticut! </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Alice Data </td>
   <td style="text-align:left;"> alice_data </td>
   <td style="text-align:left;"> London </td>
   <td style="text-align:left;"> Data Scientist @BritishMuseum | Trained in quantbio+conservation loves R, science, improv | Proud Introvert #quiet | @RLadiesGlobal co-founder #RLadies🌍 </td>
  </tr>
</tbody>
</table>

```r
library(DT)
datatable(users[, c(2:5)])
```

<!--html_preserve--><div id="htmlwidget-9be5299f320b3b7776e6" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-9be5299f320b3b7776e6">{"x":{"filter":"none","data":[["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19","20","21","22","23","24","25","26","27","28","29","30","31","32","33","34","35","36","37","38","39","40","41","42","43","44","45","46","47","48","49","50","51","52","53","54","55","56","57","58","59","60","61","62","63","64","65","66","67","68","69","70","71","72","73","74","75","76","77","78","79","80","81","82","83","84","85","86","87","88","89","90","91","92","93","94","95","96","97","98","99","100"],["R-Ladies Global","R-Ladies SF","R-Ladies London","R-Ladies RTP","R-Ladies Connecticut","Alice Data","Gabriela de Queiroz","Chiin","Mine Dogucu","Jennifer Thompson","R-Ladies Istanbul","R-Ladies BCN","R-Ladies NYC","R-Ladies Boston 🐟","R-Ladies LA 🌴","R-Ladies Madrid","R-Ladies AU","R-Ladies Paris","R-Ladies Lisbon","R-Ladies Berlin","R-Ladies Valencia","R-Ladies Nashville","R-Ladies Columbus","R-Ladies Austin","R-Ladies Ames","R-Ladies DC","R-Ladies LdnOnt","R-Ladies BuenosAires","RLadiesDublin","R-Ladies Manchester","R-Ladies Tbilisi","R-Ladies Munich","R-Ladies Adelaide","Dr Louise J. Slater","Daniela Vázquez","Katherine Scranton","Nurse Stephanie","R Ladies Twin Cities","R-Ladies Budapest","RLadiesIzmir","R-Ladies Munich","R-Ladies Adelaide","Dr Louise J. Slater","Daniela Vázquez","Katherine Scranton","Nurse Stephanie","R Ladies Twin Cities","R-Ladies Budapest","RLadiesIzmir","R-Ladies Lima","R-Ladies Cape Town","RLadiesRio","楽天市場のレディースファッション配信","JuniorRangers ladies","R-Ladies Santa Rosa","WomenRLadies","pacificatré","Royal Ladies","R-Ladies Montreal","rockNroyaltyl","R-Ladies Munich","R-Ladies Adelaide","Dr Louise J. Slater","Daniela Vázquez","Katherine Scranton","Nurse Stephanie","R Ladies Twin Cities","R-Ladies Budapest","RLadiesIzmir","R-Ladies Lima","R-Ladies Cape Town","RLadiesRio","楽天市場のレディースファッション配信","JuniorRangers ladies","R-Ladies Santa Rosa","WomenRLadies","pacificatré","Royal Ladies","R-Ladies Montreal","rockNroyaltyl","R-Ladies Munich","R-Ladies Adelaide","Dr Louise J. Slater","Daniela Vázquez","Katherine Scranton","Nurse Stephanie","R Ladies Twin Cities","R-Ladies Budapest","RLadiesIzmir","R-Ladies Lima","R-Ladies Cape Town","RLadiesRio","楽天市場のレディースファッション配信","JuniorRangers ladies","R-Ladies Santa Rosa","WomenRLadies","pacificatré","Royal Ladies","R-Ladies Montreal","rockNroyaltyl"],["RLadiesGlobal","RLadiesSF","RLadiesLondon","RLadiesRTP","RLadiesCT","alice_data","gdequeiroz","AnalyticsPanda","MineDogucu","jent103","RLadiesIstanbul","RLadiesBCN","RLadiesNYC","RLadiesBoston","RLadiesLA","RLadiesMAD","RLadiesAU","RLadiesParis","RLadiesLx","RLadiesBerlin","RLadiesValencia","RLadiesNash","RLadiesColumbus","RLadiesAustin","RLadiesAmes","RLadiesDC","RLadiesLdnOnt","RLadiesBA","RLadiesDublin","RLadiesManchest","RLadiesTbilisi","RLadiesMunich","RLadiesAdelaide","LouiseJES","d4tagirl","DrScranto","QueensRLadies","RLadiesTC","RLadiesBudapest","RLadiesIzmir","RLadiesMunich","RLadiesAdelaide","LouiseJES","d4tagirl","DrScranto","QueensRLadies","RLadiesTC","RLadiesBudapest","RLadiesIzmir","RLadiesLima","RLadiesCapeTown","RLadiesRio","RLadies_LF","Junior_RLadies","RLadiesSR","WomenRLadies","Rstn_RLadies13","RLadies","RLadiesMTL","RnRladies","RLadiesMunich","RLadiesAdelaide","LouiseJES","d4tagirl","DrScranto","QueensRLadies","RLadiesTC","RLadiesBudapest","RLadiesIzmir","RLadiesLima","RLadiesCapeTown","RLadiesRio","RLadies_LF","Junior_RLadies","RLadiesSR","WomenRLadies","Rstn_RLadies13","RLadies","RLadiesMTL","RnRladies","RLadiesMunich","RLadiesAdelaide","LouiseJES","d4tagirl","DrScranto","QueensRLadies","RLadiesTC","RLadiesBudapest","RLadiesIzmir","RLadiesLima","RLadiesCapeTown","RLadiesRio","RLadies_LF","Junior_RLadies","RLadiesSR","WomenRLadies","Rstn_RLadies13","RLadies","RLadiesMTL","RnRladies"],["The World","San Francisco","London, England","Durham, NC","Connecticut, USA","London","San Francisco","London, England","Columbus, OH","Nashville","İstanbul, Türkiye","Barcelona, Spain","New York","Boston, MA","Los Angeles, CA","Madrid, Spain","Melbourne, Victoria","Paris, France",null,"Berlin, Deutschland","Valencia, España","Nashville, TN","Columbus, OH","Austin, TX","Ames, IA","Washington, DC","London, Ontario","Buenos Aires, Argentina","Dublin City, Ireland","Manchester, England","Tbilisi","Munich, Bavaria","Adelaide, South Australia","Loughborough University","Montevideo, Uruguay","Los Angeles, CA","NY-PA...NC","Twin Cities","Budapest, Magyarország","Izmir, Turkey","Munich, Bavaria","Adelaide, South Australia","Loughborough University","Montevideo, Uruguay","Los Angeles, CA","NY-PA...NC","Twin Cities","Budapest, Magyarország","Izmir, Turkey","Lima, Peru","Cape Town, South Africa","Rio de Janeiro, Brazil","東京","orpington ","Santa Rosa, Argentina","Wisconsin Rapids, WI","Buku Tahunan Sekolah","Italia",null,null,"Munich, Bavaria","Adelaide, South Australia","Loughborough University","Montevideo, Uruguay","Los Angeles, CA","NY-PA...NC","Twin Cities","Budapest, Magyarország","Izmir, Turkey","Lima, Peru","Cape Town, South Africa","Rio de Janeiro, Brazil","東京","orpington ","Santa Rosa, Argentina","Wisconsin Rapids, WI","Buku Tahunan Sekolah","Italia",null,null,"Munich, Bavaria","Adelaide, South Australia","Loughborough University","Montevideo, Uruguay","Los Angeles, CA","NY-PA...NC","Twin Cities","Budapest, Magyarország","Izmir, Turkey","Lima, Peru","Cape Town, South Africa","Rio de Janeiro, Brazil","東京","orpington ","Santa Rosa, Argentina","Wisconsin Rapids, WI","Buku Tahunan Sekolah","Italia",null,null],["Promoting Gender Diversity in the #rstats community via meetups, mentorship &amp; global collaboration! 30+ groups worldwide: US|Europe|Oceania|LatAm|Asia #RLadies","R-Ladies is the first group dedicated to women and R. It was founded in Oct 2012 to promote knowledge, support and inclusivity. #rstats #RLadies","The first R programming meetup for Minority Genders in the UK! Promoting Diversity &amp; Inclusivity in STEM/Data Science\nlondon@rladies.org \n#RLadiesLondon #rstats",null,"Promoting &amp; connecting women (and self-identified) interested in R programming throughout Connecticut!","Data Scientist @BritishMuseum | Trained in quantbio+conservation loves R, science, improv | Proud Introvert #quiet | @RLadiesGlobal co-founder #RLadies🌍","Founder of #RLadies | Lead Data Scientist @SelfScore | Past: @Sharethrough, @AlpineDataLabs, @ensp, @IMSUERJ | ❤️ #rstats","Head of #DataScience, #RLadies Tech Community Leader, Humanist. ENTJ. Gender Equality &amp; STEM access, Open Source. ex-@Cambridge_Uni Econs. F1+Burger junkie","Statistics | DataScience | MissingData | Feminism | Languages | founder of @RLadiesColumbus  | fan of #rstats | fan of #RLadies | views my own","A well-rounded conversationalist and a standup woman. Statistician, aunt, traveler, knitter, among other things. Co-founder, #RLadies Nashville (@RLadiesNash).","Istanbul's first R Programming Meetup for Women #rladies #rstats Come and join us!","We are part of a world-wide organization to promote gender diversity in the #rstats community #RLadies https://t.co/mWCmJ4f1lr Tweets by @Rebitt &amp; @ma_salmon","We are the proud Ladies who R! In New York via Meetup, mentoring and collaboration #rstats #RLadies","R-Ladies Boston is part of a world-wide organization to promote\ngender diversity in the R community #RLadies #rstats #rcodladies","R-Ladies Los Angeles is part of a world-wide organization to promote gender diversity in the R community #RLadies #rstats https://t.co/mWCmJ4wCcZ","R-Ladies es la comunidad que ayuda a aumentar la diversidad de género en la comunidad #rstats y los trabajos en el campo STEM. #RLadiesMAD | 100% A TOPE","Aims to create a community for women interested in R language in Melbourne. No matter how you define your gender, join us if you support the diversity!","We're a group of Ladies who love #Rstats. Come join us at our upcoming meet-up!","Olá! Welcome to R Ladies Lisbon - Promoting the role of women in the R community as part of #RLadiesGlobal initiative. Collaborate, learn and share with us.","R-Ladies Berlin is a local chapter of the worldwide R-Ladies group. More information at https://t.co/hmY7DgRF77.","Promoting the role of women in the R community as part of #RLadiesGlobal initiative. #rstats #RLadiesValencia","R-Ladies Nashville is part of a worldwide organization to promote gender diversity in the R community. More info: https://t.co/hmY7DgRF77 #RLadies #rstats","R-Ladies Columbus is part of a world-wide organization to promote gender diversity in the R community. #RLadies #rstats  columbus@rladies.org","R-Ladies Austin is part of a world-wide organization to promote gender diversity in the R community #RLadies #rstats","R-Ladies Chapter for the Ames, Iowa, USA, area.","R-Ladies DC is part of a worldwide organization to promote Gender Diversity in the R community.","R-Ladies London, Ontario is part of a worldwide organization to promote Gender Diversity in the R community.","R-Ladies Buenos Aires es parte de una organización mundial para promover la Diversidad de Género en la comunidad R.\n#RLadies #rstats","RLadies Dublin is part of a world-wide organization to promote gender diversity in the R community #RLadies #rstats email us dublin (at) rladies (dot) org","R-Ladies aims to increase gender diversity in #rstats community via local meetups &amp; mentorship!","R-Ladies თბილისი","R-Ladies Munich is part of a worldwide organization to promote gender diversity in the R community. More info: https://t.co/hmY7DgRF77  #RLadies #rstats","R-Ladies Adelaide is a part of global R-Ladies community. Our goal is to bring diversity into R community and get more women excited about technology.","UK University Lecturer @lborogeog | Floods | Data science | Forecasting | Fluvial geomorphology | Climate | #R #RLadies","Data Scientist; #NASADatanauts; #rstats and #RLadies fan; @RLadiesBA co-organizer; Past: @Equifax; I love painting 🎨 (she/her)","Quantitative ecologist, postdoc at UCLA, cheese addict, owner of a very good dog #Rladies #rstats","22y.o. New nurse{4/5/14}. A Tru Queen is Always a Lady 1st. Inspiring&amp;Wise #teamJamaican #NursingIsLife #PlayboyFanatic #NCCU14","Serving self identified women and gender queer folks interested in #rstats, especially #RCatLadies","Az R-Ladies Budapest célja az adatelemzés és az R nyelv iránt érdeklődő, vagy e területeken már jártas lányok számára egy barátságos szakmai fórum létrehozása.","Izmir's first R Programming Meetup for Women #rladies #rstats Come and join us!","R-Ladies Munich is part of a worldwide organization to promote gender diversity in the R community. More info: https://t.co/hmY7DgRF77  #RLadies #rstats","R-Ladies Adelaide is a part of global R-Ladies community. Our goal is to bring diversity into R community and get more women excited about technology.","UK University Lecturer @lborogeog | Floods | Data science | Forecasting | Fluvial geomorphology | Climate | #R #RLadies","Data Scientist; #NASADatanauts; #rstats and #RLadies fan; @RLadiesBA co-organizer; Past: @Equifax; I love painting 🎨 (she/her)","Quantitative ecologist, postdoc at UCLA, cheese addict, owner of a very good dog #Rladies #rstats","22y.o. New nurse{4/5/14}. A Tru Queen is Always a Lady 1st. Inspiring&amp;Wise #teamJamaican #NursingIsLife #PlayboyFanatic #NCCU14","Serving self identified women and gender queer folks interested in #rstats, especially #RCatLadies","Az R-Ladies Budapest célja az adatelemzés és az R nyelv iránt érdeklődő, vagy e területeken már jártas lányok számára egy barátságos szakmai fórum létrehozása.","Izmir's first R Programming Meetup for Women #rladies #rstats Come and join us!","R-Ladies Lima es la 1era comunidad latinoamericana de R para mujeres.Somos parte de R-Ladies Global,cuyo fin es aumentar la presencia femenina en la comunidad R","R programming community for women. Promoting gender diversity and inclusivity in the #rstats community #RLadies https://t.co/mWCmJ4f1lr","Oi! Welcome to R Ladies Rio. We are part of #RLadiesGlobal, a world-wide organization to promote gender diversity \nin the R community. Join us! Participe!","楽天市場のレディースファッション\r\nのランキングを定期的に配信\r\n相互フォロー１００％返します。","Junior Rangers is a newly established club that is looking to expand and have a ladies section and girls youth section email:juniorrangers.ladies@aol.co.uk","Grupo de mujeres que usamos R y nos gusta la Tecnología","Only here to praise and admire ALL women. \r\nI only hope to make each and everyone of them feel confident, appreciated, and self-assured each day!!!","Official Twitter Account for our beloved friends from Resistance 3 2013 &amp; VOC 2013.",null,null,null,"R-Ladies Munich is part of a worldwide organization to promote gender diversity in the R community. More info: https://t.co/hmY7DgRF77  #RLadies #rstats","R-Ladies Adelaide is a part of global R-Ladies community. Our goal is to bring diversity into R community and get more women excited about technology.","UK University Lecturer @lborogeog | Floods | Data science | Forecasting | Fluvial geomorphology | Climate | #R #RLadies","Data Scientist; #NASADatanauts; #rstats and #RLadies fan; @RLadiesBA co-organizer; Past: @Equifax; I love painting 🎨 (she/her)","Quantitative ecologist, postdoc at UCLA, cheese addict, owner of a very good dog #Rladies #rstats","22y.o. New nurse{4/5/14}. A Tru Queen is Always a Lady 1st. Inspiring&amp;Wise #teamJamaican #NursingIsLife #PlayboyFanatic #NCCU14","Serving self identified women and gender queer folks interested in #rstats, especially #RCatLadies","Az R-Ladies Budapest célja az adatelemzés és az R nyelv iránt érdeklődő, vagy e területeken már jártas lányok számára egy barátságos szakmai fórum létrehozása.","Izmir's first R Programming Meetup for Women #rladies #rstats Come and join us!","R-Ladies Lima es la 1era comunidad latinoamericana de R para mujeres.Somos parte de R-Ladies Global,cuyo fin es aumentar la presencia femenina en la comunidad R","R programming community for women. Promoting gender diversity and inclusivity in the #rstats community #RLadies https://t.co/mWCmJ4f1lr","Oi! Welcome to R Ladies Rio. We are part of #RLadiesGlobal, a world-wide organization to promote gender diversity \nin the R community. Join us! Participe!","楽天市場のレディースファッション\r\nのランキングを定期的に配信\r\n相互フォロー１００％返します。","Junior Rangers is a newly established club that is looking to expand and have a ladies section and girls youth section email:juniorrangers.ladies@aol.co.uk","Grupo de mujeres que usamos R y nos gusta la Tecnología","Only here to praise and admire ALL women. \r\nI only hope to make each and everyone of them feel confident, appreciated, and self-assured each day!!!","Official Twitter Account for our beloved friends from Resistance 3 2013 &amp; VOC 2013.",null,null,null,"R-Ladies Munich is part of a worldwide organization to promote gender diversity in the R community. More info: https://t.co/hmY7DgRF77  #RLadies #rstats","R-Ladies Adelaide is a part of global R-Ladies community. Our goal is to bring diversity into R community and get more women excited about technology.","UK University Lecturer @lborogeog | Floods | Data science | Forecasting | Fluvial geomorphology | Climate | #R #RLadies","Data Scientist; #NASADatanauts; #rstats and #RLadies fan; @RLadiesBA co-organizer; Past: @Equifax; I love painting 🎨 (she/her)","Quantitative ecologist, postdoc at UCLA, cheese addict, owner of a very good dog #Rladies #rstats","22y.o. New nurse{4/5/14}. A Tru Queen is Always a Lady 1st. Inspiring&amp;Wise #teamJamaican #NursingIsLife #PlayboyFanatic #NCCU14","Serving self identified women and gender queer folks interested in #rstats, especially #RCatLadies","Az R-Ladies Budapest célja az adatelemzés és az R nyelv iránt érdeklődő, vagy e területeken már jártas lányok számára egy barátságos szakmai fórum létrehozása.","Izmir's first R Programming Meetup for Women #rladies #rstats Come and join us!","R-Ladies Lima es la 1era comunidad latinoamericana de R para mujeres.Somos parte de R-Ladies Global,cuyo fin es aumentar la presencia femenina en la comunidad R","R programming community for women. Promoting gender diversity and inclusivity in the #rstats community #RLadies https://t.co/mWCmJ4f1lr","Oi! Welcome to R Ladies Rio. We are part of #RLadiesGlobal, a world-wide organization to promote gender diversity \nin the R community. Join us! Participe!","楽天市場のレディースファッション\r\nのランキングを定期的に配信\r\n相互フォロー１００％返します。","Junior Rangers is a newly established club that is looking to expand and have a ladies section and girls youth section email:juniorrangers.ladies@aol.co.uk","Grupo de mujeres que usamos R y nos gusta la Tecnología","Only here to praise and admire ALL women. \r\nI only hope to make each and everyone of them feel confident, appreciated, and self-assured each day!!!","Official Twitter Account for our beloved friends from Resistance 3 2013 &amp; VOC 2013.",null,null,null]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>name<\/th>\n      <th>screen_name<\/th>\n      <th>location<\/th>\n      <th>description<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"order":[],"autoWidth":false,"orderClasses":false,"columnDefs":[{"orderable":false,"targets":0}]}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

<br/>
This is great! It retrieves the user if it matches the user's _description_ as well as _name_ and _screen name_ (handle), with 36 variables regarding the user. I'm only showing the ones I'm going to use, but there is a lot of extra information there.

# Cleaning the data

First I make sure I don't have any duplicates, and then I keep only the handles that comply with the stipulated format, using a regular expression. I filter out 3 additional handles: _'RLadies'_, whose _name_ is _'Royal Ladies'_, that I assume has something to do with royalty by the crown on their picture. _'RLadies\_LF'_ is a Japanese account that translated as follows on _Google Translator_: _'Rakuten Ichiba fashion delivery'_. And finally _'RLadiesGlobal'_ because it is not a chapter, so I don't want to map it. 

I also select the variables I will use, and correct the missing values on _location_ that I'll need to geocode the chapters. 

So it's time to clean up this data:


```r
library(dplyr)
library(lubridate)
library(stringr)
library(tidyr)

rladies <- unique(users) %>%
  filter(str_detect(screen_name, '^(RLadies).*') & 
           !screen_name %in% c('RLadies', 'RLadies_LF', 'RLadiesGlobal')) %>% 
  mutate(location = ifelse(screen_name == 'RLadiesLx', 'Lisbon',
                         ifelse(screen_name == 'RLadiesMTL', 'Montreal', location))) %>%
  select(screen_name, location, created_at, followers = followers_count)

kable(head(rladies), format = "html")         
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> screen_name </th>
   <th style="text-align:left;"> location </th>
   <th style="text-align:left;"> created_at </th>
   <th style="text-align:right;"> followers </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> RLadiesSF </td>
   <td style="text-align:left;"> San Francisco </td>
   <td style="text-align:left;"> 2012-10-15 04:18:09 </td>
   <td style="text-align:right;"> 886 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> RLadiesLondon </td>
   <td style="text-align:left;"> London, England </td>
   <td style="text-align:left;"> 2016-04-20 00:52:06 </td>
   <td style="text-align:right;"> 1102 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> RLadiesRTP </td>
   <td style="text-align:left;"> Durham, NC </td>
   <td style="text-align:left;"> 2016-06-28 00:15:29 </td>
   <td style="text-align:right;"> 215 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> RLadiesCT </td>
   <td style="text-align:left;"> Connecticut, USA </td>
   <td style="text-align:left;"> 2016-11-24 14:21:03 </td>
   <td style="text-align:right;"> 134 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> RLadiesIstanbul </td>
   <td style="text-align:left;"> İstanbul, Türkiye </td>
   <td style="text-align:left;"> 2016-09-06 11:18:43 </td>
   <td style="text-align:right;"> 425 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> RLadiesBCN </td>
   <td style="text-align:left;"> Barcelona, Spain </td>
   <td style="text-align:left;"> 2016-10-11 21:06:18 </td>
   <td style="text-align:right;"> 360 </td>
  </tr>
</tbody>
</table>

There are two additional chapters with no presence on Twitter: one in Taipei, Taiwan, and the other in Warsaw, Poland. I add them according to their creation date and using the number of members on their Meetup account as followers.


```r
rladies <- rladies %>% 
  add_row(      
    screen_name = 'RLadiesTaipei',
    location = 'Taipei',
    created_at = as.Date('2014-11-15'),
    followers = 347) %>% 
  add_row(      
    screen_name = 'RLadiesWarsaw',
    location = 'Warsaw',
    created_at = as.Date('2014-11-15'),
    followers = 347)
```

As my ultimate goal is to map the chapters, I need to obtain the latitude and longitude for each one of them. The `ggmap` package really comes in handy for this kind of task. It interacts with _Google Maps_ to retrieve latitude and longitude, and I don't even have to worry about getting the location into a specific format, because it is so good at interpreting it! (I actually tried extracting the cities first, because I thought it would be the best way, but many of the chapters didn't match or matched wrongly, so I tried it like that and worked perfectly!)

Since the `ggmap::geocode` function returns 2 columns, the first thing I thought was to call it twice: once for the longitude and once for the latitude. But I didn't like it because it was awfully inefficient, and the geocoding takes some time. It was going to be something like this:


```r
library(ggmap)

rladies <- rladies %>% 
  mutate(lon = geocode(location)[,1],
         lat = geocode(location)[,2])
```

Doing some research I finally decided to use the `purrr::map` function for capturing both values in a single column of the dataframe, and then with `tidyr::unnest` I transform it into two separate columns. All of this with never having to leave the `tidyverse` world :)

I'm doing it in two steps to see the intermediate result, with the two columns in a single variable of the dataframe.


```r
library(ggmap)
library(purrr)

rladies <- rladies %>% 
  mutate(longlat = purrr::map(.$location, geocode)) 

kable(head(rladies), format = "html")   
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> screen_name </th>
   <th style="text-align:left;"> location </th>
   <th style="text-align:left;"> created_at </th>
   <th style="text-align:right;"> followers </th>
   <th style="text-align:left;"> longlat </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> RLadiesSF </td>
   <td style="text-align:left;"> San Francisco </td>
   <td style="text-align:left;"> 1350274689 </td>
   <td style="text-align:right;"> 886 </td>
   <td style="text-align:left;"> -122.41942, 37.77493 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> RLadiesLondon </td>
   <td style="text-align:left;"> London, England </td>
   <td style="text-align:left;"> 1461113526 </td>
   <td style="text-align:right;"> 1102 </td>
   <td style="text-align:left;"> -0.1277583, 51.5073509 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> RLadiesRTP </td>
   <td style="text-align:left;"> Durham, NC </td>
   <td style="text-align:left;"> 1467072929 </td>
   <td style="text-align:right;"> 215 </td>
   <td style="text-align:left;"> -78.89862, 35.99403 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> RLadiesCT </td>
   <td style="text-align:left;"> Connecticut, USA </td>
   <td style="text-align:left;"> 1479997263 </td>
   <td style="text-align:right;"> 134 </td>
   <td style="text-align:left;"> -73.08775, 41.60322 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> RLadiesIstanbul </td>
   <td style="text-align:left;"> İstanbul, Türkiye </td>
   <td style="text-align:left;"> 1473160723 </td>
   <td style="text-align:right;"> 425 </td>
   <td style="text-align:left;"> 28.97836, 41.00824 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> RLadiesBCN </td>
   <td style="text-align:left;"> Barcelona, Spain </td>
   <td style="text-align:left;"> 1476219978 </td>
   <td style="text-align:right;"> 360 </td>
   <td style="text-align:left;"> 2.173404, 41.385064 </td>
  </tr>
</tbody>
</table>

<div class="figure" style="text-align: center">
<!--html_preserve--><div id="htmlwidget-c55b9b207bf1944cdefe" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-c55b9b207bf1944cdefe">{"x":{"filter":"none","data":[["1","2","3","4","5","6"],["RLadiesSF","RLadiesLondon","RLadiesRTP","RLadiesCT","RLadiesIstanbul","RLadiesBCN"],["San Francisco","London, England","Durham, NC","Connecticut, USA","İstanbul, Türkiye","Barcelona, Spain"],["2012-10-15T04:18:09Z","2016-04-20T00:52:06Z","2016-06-28T00:15:29Z","2016-11-24T14:21:03Z","2016-09-06T11:18:43Z","2016-10-11T21:06:18Z"],[886,1102,215,134,425,360],[{"lon":[-122.4194155],"lat":[37.7749295]},{"lon":[-0.1277583],"lat":[51.5073509]},{"lon":[-78.898619],"lat":[35.9940329]},{"lon":[-73.087749],"lat":[41.6032207]},{"lon":[28.9783589],"lat":[41.0082376]},{"lon":[2.1734035],"lat":[41.3850639]}]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>screen_name<\/th>\n      <th>location<\/th>\n      <th>created_at<\/th>\n      <th>followers<\/th>\n      <th>longlat<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"columnDefs":[{"className":"dt-right","targets":4},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
<p class="caption">plot of chunk unnamed-chunk-3</p>
</div>



```r
rladies <- rladies %>% 
  unnest() 

kable(head(rladies), format = "html")         
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> screen_name </th>
   <th style="text-align:left;"> location </th>
   <th style="text-align:left;"> created_at </th>
   <th style="text-align:right;"> followers </th>
   <th style="text-align:right;"> lon </th>
   <th style="text-align:right;"> lat </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> RLadiesSF </td>
   <td style="text-align:left;"> San Francisco </td>
   <td style="text-align:left;"> 2012-10-15 04:18:09 </td>
   <td style="text-align:right;"> 886 </td>
   <td style="text-align:right;"> -122.4194155 </td>
   <td style="text-align:right;"> 37.77493 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> RLadiesLondon </td>
   <td style="text-align:left;"> London, England </td>
   <td style="text-align:left;"> 2016-04-20 00:52:06 </td>
   <td style="text-align:right;"> 1102 </td>
   <td style="text-align:right;"> -0.1277583 </td>
   <td style="text-align:right;"> 51.50735 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> RLadiesRTP </td>
   <td style="text-align:left;"> Durham, NC </td>
   <td style="text-align:left;"> 2016-06-28 00:15:29 </td>
   <td style="text-align:right;"> 215 </td>
   <td style="text-align:right;"> -78.8986190 </td>
   <td style="text-align:right;"> 35.99403 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> RLadiesCT </td>
   <td style="text-align:left;"> Connecticut, USA </td>
   <td style="text-align:left;"> 2016-11-24 14:21:03 </td>
   <td style="text-align:right;"> 134 </td>
   <td style="text-align:right;"> -73.0877490 </td>
   <td style="text-align:right;"> 41.60322 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> RLadiesIstanbul </td>
   <td style="text-align:left;"> İstanbul, Türkiye </td>
   <td style="text-align:left;"> 2016-09-06 11:18:43 </td>
   <td style="text-align:right;"> 425 </td>
   <td style="text-align:right;"> 28.9783589 </td>
   <td style="text-align:right;"> 41.00824 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> RLadiesBCN </td>
   <td style="text-align:left;"> Barcelona, Spain </td>
   <td style="text-align:left;"> 2016-10-11 21:06:18 </td>
   <td style="text-align:right;"> 360 </td>
   <td style="text-align:right;"> 2.1734035 </td>
   <td style="text-align:right;"> 41.38506 </td>
  </tr>
</tbody>
</table>

A few more minor changes and my dataframe will be ready! 

I format the date variable `created_at` as `%Y-%m-%d` (just because seeing the hours, minutes and seconds annoys me!) and generate the age in days (for reproducibility, I set a fixed date to compare it with).


```r
rladies <- rladies %>% 
  mutate(created_at = format(as.Date(created_at), format = '%Y-%m-%d'),
         age_days = difftime(as.Date('2017-4-25'), created_at, unit = 'days'))

kable(head(rladies), format = "html") 
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> screen_name </th>
   <th style="text-align:left;"> location </th>
   <th style="text-align:left;"> created_at </th>
   <th style="text-align:right;"> followers </th>
   <th style="text-align:right;"> lon </th>
   <th style="text-align:right;"> lat </th>
   <th style="text-align:left;"> age_days </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> RLadiesSF </td>
   <td style="text-align:left;"> San Francisco </td>
   <td style="text-align:left;"> 2012-10-15 </td>
   <td style="text-align:right;"> 886 </td>
   <td style="text-align:right;"> -122.4194155 </td>
   <td style="text-align:right;"> 37.77493 </td>
   <td style="text-align:left;"> 1652.917 days </td>
  </tr>
  <tr>
   <td style="text-align:left;"> RLadiesLondon </td>
   <td style="text-align:left;"> London, England </td>
   <td style="text-align:left;"> 2016-04-20 </td>
   <td style="text-align:right;"> 1102 </td>
   <td style="text-align:right;"> -0.1277583 </td>
   <td style="text-align:right;"> 51.50735 </td>
   <td style="text-align:left;"> 369.875 days </td>
  </tr>
  <tr>
   <td style="text-align:left;"> RLadiesRTP </td>
   <td style="text-align:left;"> Durham, NC </td>
   <td style="text-align:left;"> 2016-06-28 </td>
   <td style="text-align:right;"> 215 </td>
   <td style="text-align:right;"> -78.8986190 </td>
   <td style="text-align:right;"> 35.99403 </td>
   <td style="text-align:left;"> 300.875 days </td>
  </tr>
  <tr>
   <td style="text-align:left;"> RLadiesCT </td>
   <td style="text-align:left;"> Connecticut, USA </td>
   <td style="text-align:left;"> 2016-11-24 </td>
   <td style="text-align:right;"> 134 </td>
   <td style="text-align:right;"> -73.0877490 </td>
   <td style="text-align:right;"> 41.60322 </td>
   <td style="text-align:left;"> 151.875 days </td>
  </tr>
  <tr>
   <td style="text-align:left;"> RLadiesIstanbul </td>
   <td style="text-align:left;"> İstanbul, Türkiye </td>
   <td style="text-align:left;"> 2016-09-06 </td>
   <td style="text-align:right;"> 425 </td>
   <td style="text-align:right;"> 28.9783589 </td>
   <td style="text-align:right;"> 41.00824 </td>
   <td style="text-align:left;"> 230.875 days </td>
  </tr>
  <tr>
   <td style="text-align:left;"> RLadiesBCN </td>
   <td style="text-align:left;"> Barcelona, Spain </td>
   <td style="text-align:left;"> 2016-10-11 </td>
   <td style="text-align:right;"> 360 </td>
   <td style="text-align:right;"> 2.1734035 </td>
   <td style="text-align:right;"> 41.38506 </td>
   <td style="text-align:left;"> 195.875 days </td>
  </tr>
</tbody>
</table>

That's it!

Now the dataframe is ready for me to use it for visualizing these Twitter users on the map (considering their sizes and dates of creation), and make some animations! If you are interested, [you can check how I do it here]() {:target="_blank"}

Thank you for reading! Please leave your comments below or [Mention me on Twitter](https://twitter.com/intent/tweet?user_id=114258616) :)
