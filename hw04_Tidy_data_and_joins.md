Homework 04: Tidy data and joins
================

## Tidy Cheatsheet (Data Reshaping Prompts)

This cheatsheet aims to explore several basic tools in tidyr that will
help organize our data in a consistent way in R. Tidyr is a member of
the core tidyverse package.

## Gather() and Spread()

Let’s use *gapminder* data to explore these
tools

### Gather()

<p align="left">

<img src="https://github.com/STAT545-UBC-students/hw04-huangjieying/blob/master/Figures/gather.png" height="100" width="300"/>

</p>

I will use *t1* dataset to demonstrate `gather()`:

``` r
t1=gapminder %>%
  filter(continent == "Asia" & year == "2002") %>%
  select(year, country, pop, lifeExp) %>%
  group_by(year) 
t1
```

    ## # A tibble: 33 x 4
    ## # Groups:   year [1]
    ##     year country                 pop lifeExp
    ##    <int> <fct>                 <int>   <dbl>
    ##  1  2002 Afghanistan        25268405    42.1
    ##  2  2002 Bahrain              656397    74.8
    ##  3  2002 Bangladesh        135656790    62.0
    ##  4  2002 Cambodia           12926707    56.8
    ##  5  2002 China            1280400000    72.0
    ##  6  2002 Hong Kong, China    6762476    81.5
    ##  7  2002 India            1034172547    62.9
    ##  8  2002 Indonesia         211060000    68.6
    ##  9  2002 Iran               66907826    69.5
    ## 10  2002 Iraq               24001816    57.0
    ## # ... with 23 more rows

I will use `gather()` to rearrange values from population and life
expectancy in same column and use a key to differentiate them.

``` r
t1 %>% 
  gather(pop, lifeExp, key=parameter, value=value) %>% 
  arrange(year, country) 
```

    ## # A tibble: 66 x 4
    ## # Groups:   year [1]
    ##     year country     parameter        value
    ##    <int> <fct>       <chr>            <dbl>
    ##  1  2002 Afghanistan pop         25268405  
    ##  2  2002 Afghanistan lifeExp           42.1
    ##  3  2002 Bahrain     pop           656397  
    ##  4  2002 Bahrain     lifeExp           74.8
    ##  5  2002 Bangladesh  pop        135656790  
    ##  6  2002 Bangladesh  lifeExp           62.0
    ##  7  2002 Cambodia    pop         12926707  
    ##  8  2002 Cambodia    lifeExp           56.8
    ##  9  2002 China       pop       1280400000  
    ## 10  2002 China       lifeExp           72.0
    ## # ... with 56 more rows

### Spread()

<p align="left">

<img src="https://github.com/STAT545-UBC-students/hw04-huangjieying/blob/master/Figures/spread.png" height="100" width="300"/>

</p>

Using another data, *t2*, as an example: a tibble with 24 rows: 2 per
year, giving the country with both the lowest and highest life
expectancy (in Asia)

``` r
t2=gapminder %>%
  filter(continent == "Asia") %>%
  select(year, country, lifeExp) %>%
  group_by(year) %>%
  filter(min_rank(desc(lifeExp)) < 2 | min_rank(lifeExp) < 2) %>% 
  arrange(year) %>%
  print(n = Inf)
```

    ## # A tibble: 24 x 3
    ## # Groups:   year [12]
    ##     year country     lifeExp
    ##    <int> <fct>         <dbl>
    ##  1  1952 Afghanistan    28.8
    ##  2  1952 Israel         65.4
    ##  3  1957 Afghanistan    30.3
    ##  4  1957 Israel         67.8
    ##  5  1962 Afghanistan    32.0
    ##  6  1962 Israel         69.4
    ##  7  1967 Afghanistan    34.0
    ##  8  1967 Japan          71.4
    ##  9  1972 Afghanistan    36.1
    ## 10  1972 Japan          73.4
    ## 11  1977 Cambodia       31.2
    ## 12  1977 Japan          75.4
    ## 13  1982 Afghanistan    39.9
    ## 14  1982 Japan          77.1
    ## 15  1987 Afghanistan    40.8
    ## 16  1987 Japan          78.7
    ## 17  1992 Afghanistan    41.7
    ## 18  1992 Japan          79.4
    ## 19  1997 Afghanistan    41.8
    ## 20  1997 Japan          80.7
    ## 21  2002 Afghanistan    42.1
    ## 22  2002 Japan          82  
    ## 23  2007 Afghanistan    43.8
    ## 24  2007 Japan          82.6

Now I want to turn the table into one row per year using `spread()`

``` r
t2 %>% 
  spread(key=country, value=lifeExp)
```

    ## # A tibble: 12 x 5
    ## # Groups:   year [12]
    ##     year Afghanistan Cambodia Israel Japan
    ##    <int>       <dbl>    <dbl>  <dbl> <dbl>
    ##  1  1952        28.8     NA     65.4  NA  
    ##  2  1957        30.3     NA     67.8  NA  
    ##  3  1962        32.0     NA     69.4  NA  
    ##  4  1967        34.0     NA     NA    71.4
    ##  5  1972        36.1     NA     NA    73.4
    ##  6  1977        NA       31.2   NA    75.4
    ##  7  1982        39.9     NA     NA    77.1
    ##  8  1987        40.8     NA     NA    78.7
    ##  9  1992        41.7     NA     NA    79.4
    ## 10  1997        41.8     NA     NA    80.7
    ## 11  2002        42.1     NA     NA    82  
    ## 12  2007        43.8     NA     NA    82.6

### Unite()

<p align="left">

<img src="https://github.com/STAT545-UBC-students/hw04-huangjieying/blob/master/Figures/unitepng.png" height="100" width="300"/>

</p>

We can use `unite()`function to combine two columns into one. Using *t2*
as an example, assume we want to combine country and lifeExp into one
column:

``` r
t3=t2 %>% 
  unite(lifeInCountry, country, lifeExp, sep = ", ")
t3
```

    ## # A tibble: 24 x 2
    ## # Groups:   year [12]
    ##     year lifeInCountry      
    ##    <int> <chr>              
    ##  1  1952 Afghanistan, 28.801
    ##  2  1952 Israel, 65.39      
    ##  3  1957 Afghanistan, 30.332
    ##  4  1957 Israel, 67.84      
    ##  5  1962 Afghanistan, 31.997
    ##  6  1962 Israel, 69.39      
    ##  7  1967 Afghanistan, 34.02 
    ##  8  1967 Japan, 71.43       
    ##  9  1972 Afghanistan, 36.088
    ## 10  1972 Japan, 73.42       
    ## # ... with 14 more rows

Now we have one column contain each country and its life expectancy. I
found this function is very helpful in organizing summary of a
regression model. As opposite of `unite()`, `seperate()` can be used to
seperate one column into
several.

### Seperate()

<p align="left">

<img src="https://github.com/STAT545-UBC-students/hw04-huangjieying/blob/master/Figures/separate.png" height="100" width="300"/>

</p>

``` r
t3 %>% 
  separate(lifeInCountry, c("country", "lifeExp"),sep = ", ")
```

    ## # A tibble: 24 x 3
    ## # Groups:   year [12]
    ##     year country     lifeExp
    ##    <int> <chr>       <chr>  
    ##  1  1952 Afghanistan 28.801 
    ##  2  1952 Israel      65.39  
    ##  3  1957 Afghanistan 30.332 
    ##  4  1957 Israel      67.84  
    ##  5  1962 Afghanistan 31.997 
    ##  6  1962 Israel      69.39  
    ##  7  1967 Afghanistan 34.02  
    ##  8  1967 Japan       71.43  
    ##  9  1972 Afghanistan 36.088 
    ## 10  1972 Japan       73.42  
    ## # ... with 14 more rows

One differece worth noticing is that the class of lifeExp is changed
from double to character after we unite and separate the data.

## Cheatsheet for dyplr join functions (Join Prompts)

I will use the *nycflight13* package to explore different join functions
in dyplr. *nycflight13* contains several tibbles that are related to
flights. In this deomstration, I will only use follow two datasets:

``` r
flights
```

    ## # A tibble: 336,776 x 19
    ##     year month   day dep_time sched_dep_time dep_delay arr_time
    ##    <int> <int> <int>    <int>          <int>     <dbl>    <int>
    ##  1  2013     1     1      517            515         2      830
    ##  2  2013     1     1      533            529         4      850
    ##  3  2013     1     1      542            540         2      923
    ##  4  2013     1     1      544            545        -1     1004
    ##  5  2013     1     1      554            600        -6      812
    ##  6  2013     1     1      554            558        -4      740
    ##  7  2013     1     1      555            600        -5      913
    ##  8  2013     1     1      557            600        -3      709
    ##  9  2013     1     1      557            600        -3      838
    ## 10  2013     1     1      558            600        -2      753
    ## # ... with 336,766 more rows, and 12 more variables: sched_arr_time <int>,
    ## #   arr_delay <dbl>, carrier <chr>, flight <int>, tailnum <chr>,
    ## #   origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>, hour <dbl>,
    ## #   minute <dbl>, time_hour <dttm>

``` r
airlines
```

    ## # A tibble: 16 x 2
    ##    carrier name                       
    ##    <chr>   <chr>                      
    ##  1 9E      Endeavor Air Inc.          
    ##  2 AA      American Airlines Inc.     
    ##  3 AS      Alaska Airlines Inc.       
    ##  4 B6      JetBlue Airways            
    ##  5 DL      Delta Air Lines Inc.       
    ##  6 EV      ExpressJet Airlines Inc.   
    ##  7 F9      Frontier Airlines Inc.     
    ##  8 FL      AirTran Airways Corporation
    ##  9 HA      Hawaiian Airlines Inc.     
    ## 10 MQ      Envoy Air                  
    ## 11 OO      SkyWest Airlines Inc.      
    ## 12 UA      United Air Lines Inc.      
    ## 13 US      US Airways Inc.            
    ## 14 VX      Virgin America             
    ## 15 WN      Southwest Airlines Co.     
    ## 16 YV      Mesa Airlines Inc.

We can see that *flights* contains too many variables and observations,
so let’s narrow the dataset before using it for demonstration.

``` r
flight2 = flights %>% 
  filter(day=="6" & hour=="8" & dest == "IAH") %>% 
  select(year:day, hour, origin, dest, tailnum, carrier)
  
flight2 ## I will use this data for following demonstration
```

    ## # A tibble: 15 x 8
    ##     year month   day  hour origin dest  tailnum carrier
    ##    <int> <int> <int> <dbl> <chr>  <chr> <chr>   <chr>  
    ##  1  2013     1     6     8 LGA    IAH   N76502  UA     
    ##  2  2013     1     6     8 EWR    IAH   N14731  UA     
    ##  3  2013    10     6     8 LGA    IAH   N438UA  UA     
    ##  4  2013    11     6     8 LGA    IAH   N73270  UA     
    ##  5  2013    11     6     8 EWR    IAH   N421UA  UA     
    ##  6  2013    12     6     8 LGA    IAH   N76265  UA     
    ##  7  2013    12     6     8 EWR    IAH   N491UA  UA     
    ##  8  2013     2     6     8 EWR    IAH   N27733  UA     
    ##  9  2013     4     6     8 LGA    IAH   N485UA  UA     
    ## 10  2013     5     6     8 LGA    IAH   N33264  UA     
    ## 11  2013     6     6     8 LGA    IAH   N471UA  UA     
    ## 12  2013     7     6     8 EWR    IAH   N38451  UA     
    ## 13  2013     8     6     8 LGA    IAH   N23707  UA     
    ## 14  2013     8     6     8 EWR    IAH   N12225  UA     
    ## 15  2013     9     6     8 LGA    IAH   N558UA  UA

### Inner\_join()

Inner\_join(x, y) is the simplest type of join. It matches pairs of
observations whenever their keys are equal, i.e. it returns all columns
from x and y. So it is a mutating join.

Let’s creat a dataset to join *flight2*

``` r
dat1=tribble(
  ~tailnum, ~month,
  "N76502", 1, 
  "N14731", 2,
  "N438UA", 12
  )
dat1
```

    ## # A tibble: 3 x 2
    ##   tailnum month
    ##   <chr>   <dbl>
    ## 1 N76502      1
    ## 2 N14731      2
    ## 3 N438UA     12

Now let’s join dat1 to *flight2*.

``` r
flight2 %>% 
  inner_join(dat1, by="month")
```

    ## # A tibble: 5 x 9
    ##    year month   day  hour origin dest  tailnum.x carrier tailnum.y
    ##   <int> <dbl> <int> <dbl> <chr>  <chr> <chr>     <chr>   <chr>    
    ## 1  2013     1     6     8 LGA    IAH   N76502    UA      N76502   
    ## 2  2013     1     6     8 EWR    IAH   N14731    UA      N76502   
    ## 3  2013    12     6     8 LGA    IAH   N76265    UA      N438UA   
    ## 4  2013    12     6     8 EWR    IAH   N491UA    UA      N438UA   
    ## 5  2013     2     6     8 EWR    IAH   N27733    UA      N14731

We can see that results only return to records when keys are equal.

### Left\_join()

left\_join(x, y) joins matching rows from y to x. Similar,
`right_join()` matches rows from x to y.

Let’s try left\_join and use *flight2* for demonstration:

``` r
flight2 %>% 
  left_join(airlines, by="carrier")
```

    ## # A tibble: 15 x 9
    ##     year month   day  hour origin dest  tailnum carrier name              
    ##    <int> <int> <int> <dbl> <chr>  <chr> <chr>   <chr>   <chr>             
    ##  1  2013     1     6     8 LGA    IAH   N76502  UA      United Air Lines …
    ##  2  2013     1     6     8 EWR    IAH   N14731  UA      United Air Lines …
    ##  3  2013    10     6     8 LGA    IAH   N438UA  UA      United Air Lines …
    ##  4  2013    11     6     8 LGA    IAH   N73270  UA      United Air Lines …
    ##  5  2013    11     6     8 EWR    IAH   N421UA  UA      United Air Lines …
    ##  6  2013    12     6     8 LGA    IAH   N76265  UA      United Air Lines …
    ##  7  2013    12     6     8 EWR    IAH   N491UA  UA      United Air Lines …
    ##  8  2013     2     6     8 EWR    IAH   N27733  UA      United Air Lines …
    ##  9  2013     4     6     8 LGA    IAH   N485UA  UA      United Air Lines …
    ## 10  2013     5     6     8 LGA    IAH   N33264  UA      United Air Lines …
    ## 11  2013     6     6     8 LGA    IAH   N471UA  UA      United Air Lines …
    ## 12  2013     7     6     8 EWR    IAH   N38451  UA      United Air Lines …
    ## 13  2013     8     6     8 LGA    IAH   N23707  UA      United Air Lines …
    ## 14  2013     8     6     8 EWR    IAH   N12225  UA      United Air Lines …
    ## 15  2013     9     6     8 LGA    IAH   N558UA  UA      United Air Lines …

The result of left join *airlines* to *flight2* return an additional
variable: name. I am wondering if any basic R functions could achieve
the same outcome?

### Comparing with R basic function `match()`

Same results as above left join can also be obtained using `mutate()`
combining wtih with R basic function `match()`

``` r
flight2 %>% 
  mutate(name=airlines$name[match(carrier, airlines$carrier)])
```

    ## # A tibble: 15 x 9
    ##     year month   day  hour origin dest  tailnum carrier name              
    ##    <int> <int> <int> <dbl> <chr>  <chr> <chr>   <chr>   <chr>             
    ##  1  2013     1     6     8 LGA    IAH   N76502  UA      United Air Lines …
    ##  2  2013     1     6     8 EWR    IAH   N14731  UA      United Air Lines …
    ##  3  2013    10     6     8 LGA    IAH   N438UA  UA      United Air Lines …
    ##  4  2013    11     6     8 LGA    IAH   N73270  UA      United Air Lines …
    ##  5  2013    11     6     8 EWR    IAH   N421UA  UA      United Air Lines …
    ##  6  2013    12     6     8 LGA    IAH   N76265  UA      United Air Lines …
    ##  7  2013    12     6     8 EWR    IAH   N491UA  UA      United Air Lines …
    ##  8  2013     2     6     8 EWR    IAH   N27733  UA      United Air Lines …
    ##  9  2013     4     6     8 LGA    IAH   N485UA  UA      United Air Lines …
    ## 10  2013     5     6     8 LGA    IAH   N33264  UA      United Air Lines …
    ## 11  2013     6     6     8 LGA    IAH   N471UA  UA      United Air Lines …
    ## 12  2013     7     6     8 EWR    IAH   N38451  UA      United Air Lines …
    ## 13  2013     8     6     8 LGA    IAH   N23707  UA      United Air Lines …
    ## 14  2013     8     6     8 EWR    IAH   N12225  UA      United Air Lines …
    ## 15  2013     9     6     8 LGA    IAH   N558UA  UA      United Air Lines …

It generates exactly same result\! But this way is harder to think
through and apply when there are multiple variables. More importantly,
the code itself require extra efforts to understand its intention.

### Full\_join()

full\_join(x, y): Return all rows and all columns from both x and y.
Where there are not matching values, returns NA for the one missing.
This type of join could be very helpful when dealing with real data,
which we want to keep all records.

Let’s use previous example from left\_join again, see what happen:

``` r
flight2 %>% 
  full_join(airlines, by="carrier")
```

    ## # A tibble: 30 x 9
    ##     year month   day  hour origin dest  tailnum carrier name              
    ##    <int> <int> <int> <dbl> <chr>  <chr> <chr>   <chr>   <chr>             
    ##  1  2013     1     6     8 LGA    IAH   N76502  UA      United Air Lines …
    ##  2  2013     1     6     8 EWR    IAH   N14731  UA      United Air Lines …
    ##  3  2013    10     6     8 LGA    IAH   N438UA  UA      United Air Lines …
    ##  4  2013    11     6     8 LGA    IAH   N73270  UA      United Air Lines …
    ##  5  2013    11     6     8 EWR    IAH   N421UA  UA      United Air Lines …
    ##  6  2013    12     6     8 LGA    IAH   N76265  UA      United Air Lines …
    ##  7  2013    12     6     8 EWR    IAH   N491UA  UA      United Air Lines …
    ##  8  2013     2     6     8 EWR    IAH   N27733  UA      United Air Lines …
    ##  9  2013     4     6     8 LGA    IAH   N485UA  UA      United Air Lines …
    ## 10  2013     5     6     8 LGA    IAH   N33264  UA      United Air Lines …
    ## # ... with 20 more rows

Using full\_join we now have 30 records with later half of them filled
by NA, compared to left\_join where we only have 15 records that matches
*flight2*.
