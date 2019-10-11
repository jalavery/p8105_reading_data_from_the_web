Reading data from the web
================
Jessica Lavery
10/10/2019

# Summary of functions

`read_html` read HTML

`html_nodes` extract info from HTML document via CSS selectors

`html_text` convert from HTML to text

# National Survey on Drug Use and Health (NSDUH)

``` r
# URL
url <- "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

drug_use_xml <-  read_html(url)
```

Try to extract HTML nodes that we care about without Selector Gadget via
the table CSS tag. This will work a lot of the time, but not always.

``` r
drug_use_xml %>%
  html_nodes(css = "table")
```

    ## {xml_nodeset (15)}
    ##  [1] <table class="rti" border="1" cellspacing="0" cellpadding="1" width ...
    ##  [2] <table class="rti" border="1" cellspacing="0" cellpadding="1" width ...
    ##  [3] <table class="rti" border="1" cellspacing="0" cellpadding="1" width ...
    ##  [4] <table class="rti" border="1" cellspacing="0" cellpadding="1" width ...
    ##  [5] <table class="rti" border="1" cellspacing="0" cellpadding="1" width ...
    ##  [6] <table class="rti" border="1" cellspacing="0" cellpadding="1" width ...
    ##  [7] <table class="rti" border="1" cellspacing="0" cellpadding="1" width ...
    ##  [8] <table class="rti" border="1" cellspacing="0" cellpadding="1" width ...
    ##  [9] <table class="rti" border="1" cellspacing="0" cellpadding="1" width ...
    ## [10] <table class="rti" border="1" cellspacing="0" cellpadding="1" width ...
    ## [11] <table class="rti" border="1" cellspacing="0" cellpadding="1" width ...
    ## [12] <table class="rti" border="1" cellspacing="0" cellpadding="1" width ...
    ## [13] <table class="rti" border="1" cellspacing="0" cellpadding="1" width ...
    ## [14] <table class="rti" border="1" cellspacing="0" cellpadding="1" width ...
    ## [15] <table class="rti" border="1" cellspacing="0" cellpadding="1" width ...

It looks like this worked - there are 15 tables on the website and 15
rows in `drug_use_xml`.

To subset on the first table, rather than look at all tables at the same
time:

``` r
table_marj <- drug_use_xml %>%
  html_nodes(css = "table") %>% 
  .[[1]] %>% 
  html_table() %>% #do not print at this step, the note print shows up in the first row of every column
  slice(-1) %>% #Choose rows by their ordinal position in the tbl.
  as_tibble()
```

# Cost of living in NY

``` r
ny_cost_xml <- read_html("https://www.bestplaces.net/cost_of_living/city/new_york/new_york") 

ny_cost_xml %>%
  html_nodes(css = "table") %>% 
  .[[1]] %>% 
  html_table(header = TRUE)
```

    ##     COST OF LIVING New York New York      USA
    ## 1          Overall    209.3    129.4      100
    ## 2          Grocery    114.7    101.7      100
    ## 3           Health      101    100.8      100
    ## 4          Housing      354    150.8      100
    ## 5 Median Home Cost $680,500 $305,400 $231,200
    ## 6        Utilities    150.5    115.9      100
    ## 7   Transportation    211.5    161.5      100
    ## 8    Miscellaneous    121.2    101.6      100

# Harry Potter Movies

HP movie names

``` r
hpsaga_html <- read_html("https://www.imdb.com/list/ls000630791/")

hpsaga_html %>% 
  html_nodes(css = ".lister-item-header a") %>% 
  html_text() #convert from HTML to text
```

    ## [1] "Harry Potter and the Sorcerer's Stone"       
    ## [2] "Harry Potter and the Chamber of Secrets"     
    ## [3] "Harry Potter and the Prisoner of Azkaban"    
    ## [4] "Harry Potter and the Goblet of Fire"         
    ## [5] "Harry Potter and the Order of the Phoenix"   
    ## [6] "Harry Potter and the Half-Blood Prince"      
    ## [7] "Harry Potter and the Deathly Hallows: Part 1"
    ## [8] "Harry Potter and the Deathly Hallows: Part 2"

HP movie run times

``` r
hpsaga_html %>% 
  html_nodes(css = ".runtime") %>% 
  html_text() #convert from HTML to text
```

    ## [1] "152 min" "161 min" "142 min" "157 min" "138 min" "153 min" "146 min"
    ## [8] "130 min"

HP movie gross

``` r
hpsaga_html %>% 
  html_nodes(css = ".text-small:nth-child(7) span:nth-child(5)") %>% 
  html_text() #convert from HTML to text
```

    ## [1] "$317.58M" "$261.99M" "$249.36M" "$290.01M" "$292.00M" "$301.96M"
    ## [7] "$295.98M" "$381.01M"

# Napoleon Dynamite

``` r
nd_html <- read_html("https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_cr_arp_d_viewopt_rvwer?ie=UTF8&reviewerType=avp_only_reviews&sortBy=recent&pageNumber=1")

review_titles <- nd_html %>% 
  html_nodes(css = ".a-text-bold span") %>% 
  html_text()

# rating
review_stars <- nd_html %>% 
  html_nodes(css = "#cm_cr-review_list .review-rating") %>% 
  html_text()

# reviews
review_text <- nd_html %>%
  html_nodes(".review-text-content span") %>%
  html_text()

reviews = tibble(
  title = review_titles,
  stars = review_stars,
  text = review_text)

reviews
```

    ## # A tibble: 10 x 3
    ##    title                  stars       text                                 
    ##    <chr>                  <chr>       <chr>                                
    ##  1 Waste of money         1.0 out of… Terrible movie! Please don’t waste y…
    ##  2 Good movie             5.0 out of… Funny                                
    ##  3 A classic              5.0 out of… I like your sleeves. They're real bi…
    ##  4 FRIKKEN SWEET MOVIE, … 5.0 out of… It’s Napolean Dynamite. It’s charmin…
    ##  5 You gonna eat the res… 5.0 out of… One of my favorite movies ever.  You…
    ##  6 Tina you fat lard com… 5.0 out of… It's a great movie                   
    ##  7 Great family movie     5.0 out of… My kids as well as the adults love t…
    ##  8 Teens love it          5.0 out of… Original and funny                   
    ##  9 Great                  5.0 out of… Funny                                
    ## 10 Great Movie, Bad Pack… 4.0 out of… First off, the stick-on label on the…

# Using an API
