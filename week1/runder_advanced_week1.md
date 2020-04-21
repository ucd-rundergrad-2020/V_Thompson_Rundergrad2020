---
title: "Runder_advanced_week1"
author: "Veronica Thompson"
date: "April 20, 2020"
output: 
  html_document: 
    keep_md: yes
---



### Load Data  

```r
data = read_csv("master_flowering_time_data_2019_10_15.csv")
```

```
## Parsed with column specification:
## cols(
##   DNA_ID = col_character(),
##   field_ID = col_character(),
##   genotype = col_character(),
##   group = col_character(),
##   row = col_character(),
##   `end?` = col_character(),
##   condition = col_character(),
##   germ = col_character(),
##   yellow = col_character(),
##   flowering = col_character(),
##   final = col_character(),
##   diameter = col_double(),
##   direction = col_character(),
##   height = col_double(),
##   leaf_num = col_double(),
##   ax_heads = col_double(),
##   notes = col_character()
## )
```

1) Let's start with the column headers. Are there any variables that are redundant? Variables that are related? Can make any guesses about how I set the experiment up based on these relationships? It might help to make a diagram here.  


```r
head(data)
```

```
## # A tibble: 6 x 17
##   DNA_ID field_ID genotype group row   `end?` condition germ  yellow
##   <chr>  <chr>    <chr>    <chr> <chr> <chr>  <chr>     <chr> <chr> 
## 1 D132   SUN_A_01 WT       A     SUN_A end    sun       06_0~ 07_30~
## 2 D133   SUN_A_02 WT       A     SUN_A end    sun       06_0~ 07_30~
## 3 D134   SUN_A_03 WT       A     SUN_A middle sun       06_0~ 07_29~
## 4 D136   SUN_A_09 WT       A     SUN_A middle sun       06_0~ 07_30~
## 5 D137   SUN_A_10 WT       A     SUN_A middle sun       06_0~ 07_30~
## 6 D138   SUN_A_11 WT       A     SUN_A middle sun       06_0~ <NA>  
## # ... with 8 more variables: flowering <chr>, final <chr>, diameter <dbl>,
## #   direction <chr>, height <dbl>, leaf_num <dbl>, ax_heads <dbl>,
## #   notes <chr>
```

```r
tail(data)
```

```
## # A tibble: 6 x 17
##   DNA_ID field_ID genotype group row   `end?` condition germ  yellow
##   <chr>  <chr>    <chr>    <chr> <chr> <chr>  <chr>     <chr> <chr> 
## 1 D453   SUN_D_19 elf3     D     SUN_D end    sun       07_0~ 08_26~
## 2 D454   SH_D_20  elf3     D     SH_D  end    shade     07_2~ 08_24~
## 3 <NA>   SH_A_34  <NA>     D     SH_A  middle shade     <NA>  <NA>  
## 4 <NA>   SH_D_15  <NA>     D     SH_D  middle shade     <NA>  <NA>  
## 5 <NA>   SUN_D_03 <NA>     D     SUN_D middle sun       07_0~ 08_30~
## 6 <NA>   SUN_D_17 <NA>     D     SUN_D middle sun       07_0~ 08_30~
## # ... with 8 more variables: flowering <chr>, final <chr>, diameter <dbl>,
## #   direction <chr>, height <dbl>, leaf_num <dbl>, ax_heads <dbl>,
## #   notes <chr>
```
Tail is ugly, mosty the deadbeats. Group and row are partially redundant, DNA and field ID are a 1 to 1 match, but I don't think we can show that at this point.  Group, row, and condition are related.  

2) There are a couple different types of data here. When you read the data into R, do the way variables are parsed make sense? Is data format consistent within each of the variables? Are there other ways we can parse this to make it easier to work with?  

Parsing is a mess here. some should clearly be dates, others should be factors.  

```r
#example of how to change parse of one column. I would just fix it in read_csv is the first place.  
data$genotype = parse_factor(data$genotype)
head(data) #can see this is a factor now
```

```
## # A tibble: 6 x 17
##   DNA_ID field_ID genotype group row   `end?` condition germ  yellow
##   <chr>  <chr>    <fct>    <chr> <chr> <chr>  <chr>     <chr> <chr> 
## 1 D132   SUN_A_01 WT       A     SUN_A end    sun       06_0~ 07_30~
## 2 D133   SUN_A_02 WT       A     SUN_A end    sun       06_0~ 07_30~
## 3 D134   SUN_A_03 WT       A     SUN_A middle sun       06_0~ 07_29~
## 4 D136   SUN_A_09 WT       A     SUN_A middle sun       06_0~ 07_30~
## 5 D137   SUN_A_10 WT       A     SUN_A middle sun       06_0~ 07_30~
## 6 D138   SUN_A_11 WT       A     SUN_A middle sun       06_0~ <NA>  
## # ... with 8 more variables: flowering <chr>, final <chr>, diameter <dbl>,
## #   direction <chr>, height <dbl>, leaf_num <dbl>, ax_heads <dbl>,
## #   notes <chr>
```

```r
data_parsed = read_csv("master_flowering_time_data_2019_10_15.csv", 
                       col_types = cols( 
                       genotype = col_factor(),
                       group = col_factor(), 
                       germ = col_date(format = "%m_%d_%Y")))
```

```
## Warning: 19 parsing failures.
## row  col           expected     actual                                        file
## 248 germ date like %m_%d_%Y 07_2019_03 'master_flowering_time_data_2019_10_15.csv'
## 251 germ date like %m_%d_%Y 07_2019_04 'master_flowering_time_data_2019_10_15.csv'
## 254 germ date like %m_%d_%Y 07_2019_04 'master_flowering_time_data_2019_10_15.csv'
## 255 germ date like %m_%d_%Y 07_2019_04 'master_flowering_time_data_2019_10_15.csv'
## 257 germ date like %m_%d_%Y 07_2019_04 'master_flowering_time_data_2019_10_15.csv'
## ... .... .................. .......... ...........................................
## See problems(...) for more details.
```
Some of these were not formatted correctly when input. Let's look at them.   


```r
print(filter(data, germ == "07_2019_03"))
```

```
## # A tibble: 8 x 17
##   DNA_ID field_ID genotype group row   `end?` condition germ  yellow
##   <chr>  <chr>    <fct>    <chr> <chr> <chr>  <chr>     <chr> <chr> 
## 1 D418   SH_D_05  WT       D     SH_D  middle shade     07_2~ 08_26~
## 2 D433   SH_D_01  elf3     D     SH_D  end    shade     07_2~ 08_27~
## 3 D434   SH_D_02  elf3     D     SH_D  end    shade     07_2~ 08_28~
## 4 D437   SH_D_03  elf3     D     SH_D  middle shade     07_2~ 08_26~
## 5 D439   SH_D_04  elf3     D     SH_D  middle shade     07_2~ 08_23~
## 6 D441   SH_D_09  elf3     D     SH_D  middle shade     07_2~ 08_22~
## 7 D442   SH_D_10  elf3     D     SH_D  middle shade     07_2~ 08_24~
## 8 D444   SH_D_11  elf3     D     SH_D  middle shade     07_2~ 08_26~
## # ... with 8 more variables: flowering <chr>, final <chr>, diameter <dbl>,
## #   direction <chr>, height <dbl>, leaf_num <dbl>, ax_heads <dbl>,
## #   notes <chr>
```
These are probably all from group D. Will correct before next week...  


3) We will be calculating some new variables from this data next week. Can you think of two suggestions? (You don't have to calculate them today)
