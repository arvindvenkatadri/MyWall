---
title: "Group 7 — ERCP trial and the Americas"
subtitle: "Studio wall"
author: "Gandalf the Grey (Arvind) and 20 Peasants"
format:
  html:
    toc: true
    toc-depth: 2
code-tools: true
---

# Spine

> Does a simple pill change post-procedure pancreatitis, who is in that trial, and where in the Americas is life long?

~~This sentence stays at the top. Every panel must attach to it.~~

# Introduction — the tables we own

This folder is ours for the ten days. Three tables, three shapes.

- **Panel A** (`panel-A.csv`) — `medicaldata::indo_rct`: age and risk score, plus gender, treatment (`rx`: indomethacin vs placebo), and outcome.
- **Panel B** (`panel-B.csv`) — same trial, Quals: treatment, outcome, PEP, outpatient status, site.
- **Panel C** (`panel-C.csv`) — gapminder 2007, **Americas only**. Join by country name.

This is an RCT. `rx` is assigned, not a lifestyle. Do not talk about it as if people chose the pill.








::: {.cell}

```{.r .cell-code}
library(tidyverse)
library(mosaic)
library(skimr)
library(ggformula)
##
library(vcd) # (NEW!!) for Panel B
library(broom) # (NEW!!) for clean stat outputs
library(sf) # for Panel-C
library(rnaturalearth) # for Panel-C


A <- read_csv("../../../data/group-7-ercp-and-americas/panel-A.csv")
B <- read_csv("../../../data/group-7-ercp-and-americas/panel-B.csv")
C <- read_csv("../../../data/group-7-ercp-and-americas/panel-C.csv")
```
:::


~~Found tables train the mark. A campus Free Hunch is a **made** panel of the same pair type.~~

# Panel A — body / measure

> *age and risk are Quant. gender, rx, outcome are Quals for split.*


### 1. See the table

~~What is a case? Which columns are Quant / Qual? What is missing?~~


::: {.cell}

```{.r .cell-code}
# glimpse() / inspect() / skim()
dplyr::glimpse(A)
```

::: {.cell-output .cell-output-stdout}

```
Rows: 602
Columns: 5
$ age     <dbl> 26, 24, 57, 29, 38, 59, 60, 29, 53, 20, 51, 19, 57, 44, 33, 46…
$ risk    <dbl> 2.0, 1.0, 1.0, 2.0, 3.5, 3.0, 1.5, 1.0, 2.0, 2.0, 3.0, 2.5, 1.…
$ gender  <chr> "female", "male", "female", "female", "female", "female", "fem…
$ rx      <chr> "indomethacin", "placebo", "placebo", "placebo", "indomethacin…
$ outcome <chr> "yes", "no", "no", "yes", "no", "no", "no", "no", "no", "no", …
```


:::
:::

My Panel-A data has:

  - 602 observations and 5 variables.
  - `age`: Quantitative, numeric
  - `risk`: Quantitative, numeric
  - `gender`: Qualitative, categorical. Encoded <chr>, to be mutated
  - `rx`: Qualitative, categorical. Encoded <chr>, to be mutated
  - `outcome`: Qualitative, categorical. Encoded <chr>, to be mutated


::: {.cell}

```{.r .cell-code}
# glimpse() / inspect() / skim()
skimr::skim(A)
```

::: {.cell-output-display}

Table: Data summary

|                         |     |
|:------------------------|:----|
|Name                     |A    |
|Number of rows           |602  |
|Number of columns        |5    |
|_______________________  |     |
|Column type frequency:   |     |
|character                |3    |
|numeric                  |2    |
|________________________ |     |
|Group variables          |None |


**Variable type: character**

|skim_variable | n_missing| complete_rate| min| max| empty| n_unique| whitespace|
|:-------------|---------:|-------------:|---:|---:|-----:|--------:|----------:|
|gender        |         0|             1|   4|   6|     0|        2|          0|
|rx            |         0|             1|   7|  12|     0|        2|          0|
|outcome       |         0|             1|   2|   3|     0|        2|          0|


**Variable type: numeric**

|skim_variable | n_missing| complete_rate|  mean|    sd| p0|  p25|  p50| p75| p100|hist  |
|:-------------|---------:|-------------:|-----:|-----:|--:|----:|----:|---:|----:|:-----|
|age           |         0|             1| 45.27| 13.30| 19| 35.0| 45.0|  54| 90.0|▅▇▇▃▁ |
|risk          |         0|             1|  2.38|  0.88|  1|  1.5|  2.5|   3|  5.5|▅▇▃▁▁ |


:::
:::



Furthermore, on the Qual variables panel-A data has : 

- `gender` has 2 levels: "male" and "female"
- `rx` has 2 levels: "indomethacin" and "placebo"
- `outcome` has 2 levels: "no" and "yes"

And on the Quant variables panel-A data has :

- `age` has a mean of 45.26, sd of 13.2, median of 45, min of 19, and max of 90
- `risk` has a mean of 2.38, sd of 0.88, median of 2.5, min of 1, and max of 5.5

There is no missing data. `age` histogram shown in `skim()` output seems skewed to the right; `risk` is slightly skewed right / normal. 


::: {.cell}

```{.r .cell-code}
A_mod <- A |> 
  mutate(
    gender = as.factor(gender),
    rx = as.factor(rx),
    outcome = as.factor(outcome)
  )

glimpse(A_mod)
```

::: {.cell-output .cell-output-stdout}

```
Rows: 602
Columns: 5
$ age     <dbl> 26, 24, 57, 29, 38, 59, 60, 29, 53, 20, 51, 19, 57, 44, 33, 46…
$ risk    <dbl> 2.0, 1.0, 1.0, 2.0, 3.5, 3.0, 1.5, 1.0, 2.0, 2.0, 3.0, 2.5, 1.…
$ gender  <fct> female, male, female, female, female, female, female, male, ma…
$ rx      <fct> indomethacin, placebo, placebo, placebo, indomethacin, placebo…
$ outcome <fct> yes, no, no, yes, no, no, no, no, no, no, no, yes, no, no, no,…
```


:::
:::

All ready now. 


### 2. Ask

~~One question *this* table can answer. One it cannot. Write both.~~
Looking at the overall Spine statement, I can pose the following Questions:

Possibly can answer: 

1. Are the dataset balanced by `gender` and by `rx` (assigned medication)?
1. What are the distributions for each of the Quant variables, `age`, and `risk`?
1. How are they different based on the Qual variables? (e.g., is there a difference in age distribution between `gender`-s, or between `rx` groups?)
1. Is the `risk` different for men and women? (`Main Character` Question)

Possibly cannot answer:

1. Does the medication efficacy depend upon dosage? ( No `dosage` variable.)
1. Better than some other medicine? ( No data, need an entire new table)


### 3. Choose a mark

~~Geom, map mark, or (later) test that matches the pair type. Name it before you code.~~

1. A bar chart
2. Histograms
3. Histograms, coloured by `gender` and `rx`
4. Histogram?? AND Stat Test For Means?? (NEW!!)

### 4. Predict

~~What do we think we will see? One sentence, before Run.~~

I think we will see that there is a difference in `risk` between men and women. 

And a Stat test for means will confirm this.

### 5. Make

~~Human brick first. Optional: one AI prompt, pasted below, only for what section 3 named.~~

These first few charts are what I want to check out. I may/will not have these charts in the final rendered Quarto, but want to comment on findings if needed. 

If any chart is surprising, it stays. 


::: {.cell}

```{.r .cell-code}
## Question 1
A_mod |> gf_bar(~ gender, title = "Gender Distribution")

## Question 2
A_mod |> gf_bar(~ rx, title = "Gender Boxplot Distribution")
```
:::



::: {.cell}

```{.r .cell-code}
## Question 3
A_mod |> gf_histogram(~ age)
A_mod |> gf_histogram(~ age, fill = ~ gender)
A |> gf_histogram(~ age, fill = ~ rx)
##
A_mod |> gf_histogram(~ risk)
```
:::



::: {.cell}

```{.r .cell-code}
## Question #4 (Main Character)

A_mod |> gf_histogram(~ risk, fill = ~ gender, title = "Females at Higher Risk than Males?", subtitle = "Overlapping Histograms")
```

::: {.cell-output-display}
![](studio-wall_files/figure-html/panel-A-Main-Character-question-1.png){width=2100}
:::

```{.r .cell-code}
A_mod |> gf_density(~ risk, fill = ~ gender, title = "Females at Higher Risk than Males?", subtitle = "Overlapping Densities")
```

::: {.cell-output-display}
![](studio-wall_files/figure-html/panel-A-Main-Character-question-2.png){width=2100}
:::
:::


There are more `women` than `men` in the sample. 

Distributions do change with `gender`. But hard to say outright from the overlapping histograms. *Densities* for `risk` look different for men and women, though. 



::: {#tbl-panel-A-stat-test .cell tbl-cap='Risk vs Gender: Females at Significantly Higher Risk'}

```{.r .cell-code}
# Main Character Question

mosaic::t.test(risk ~ gender, data = A_mod) |> broom::tidy()
```

::: {.cell-output-display}
`````{=html}
<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["estimate"],"name":[1],"type":["dbl"],"align":["right"]},{"label":["estimate1"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["estimate2"],"name":[3],"type":["dbl"],"align":["right"]},{"label":["statistic"],"name":[4],"type":["dbl"],"align":["right"]},{"label":["p.value"],"name":[5],"type":["dbl"],"align":["right"]},{"label":["parameter"],"name":[6],"type":["dbl"],"align":["right"]},{"label":["conf.low"],"name":[7],"type":["dbl"],"align":["right"]},{"label":["conf.high"],"name":[8],"type":["dbl"],"align":["right"]},{"label":["method"],"name":[9],"type":["chr"],"align":["left"]},{"label":["alternative"],"name":[10],"type":["chr"],"align":["left"]}],"data":[{"1":"0.3014706","2":"2.444328","3":"2.142857","4":"3.386018","5":"0.0008600262","6":"191.9831","7":"0.1258602","8":"0.4770809","9":"Welch Two Sample t-test","10":"two.sided"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>
`````
:::
:::


### 6. Say

~~Caption + one sentence we will stand behind.~~


1. Women are literally at higher `risk` than men for post-procedure pancreatitis, no cap. It’s giving sad energy. 
2. Difference in mean `risk` between them is $0.3014706$, statistically significant (p-value < 0.05).

### 7. Limit

~~What this panel cannot do.~~

We’re assuming the community here is not specifically linked with pancreatitis. Would this `risk` be similar in other communities? (Lowkey assuming this community isn’t pancreatitis-coded. Would the risk vibe be similar in other communities, no cap?)

# Panel B — category / talk

*rx × outcome is the trial mosaic. site × outcome. Do not average rx.*


### 1. See the table

~~What is a case? Which columns are Quant / Qual? What is missing?~~


::: {.cell}

```{.r .cell-code}
# glimpse() / inspect() / skim()
dplyr::glimpse(B)
```

::: {.cell-output .cell-output-stdout}

```
Rows: 602
Columns: 7
$ gender  <chr> "female", "male", "female", "female", "female", "female", "fem…
$ rx      <chr> "indomethacin", "placebo", "placebo", "placebo", "indomethacin…
$ outcome <chr> "yes", "no", "no", "yes", "no", "no", "no", "no", "no", "no", …
$ pep     <chr> "no", "yes", "no", "no", "yes", "no", "no", "no", "no", "no", …
$ status  <chr> "outpatient", "outpatient", "outpatient", "outpatient", "inpat…
$ type    <chr> "1_type 1", "0_no SOD", "2_type 2", "1_type 1", "1_type 1", "1…
$ site    <chr> "1_UM", "1_UM", "1_UM", "1_UM", "1_UM", "1_UM", "1_UM", "1_UM"…
```


:::
:::

My Panel-B data has:

- 602 observations and 5 variables.
- `rx`: Qualitative, categorical. Encoded <chr>, to be mutated
- `outcome`: Qualitative, categorical. Encoded <chr>, to be mutated
- `pep`: Qualitative, categorical. Encoded <chr>, to be mutated
- `status`: Qualitative, categorical. Encoded <chr>, to be mutated
- `type`: Qualitative, categorical. Encoded <chr>, to be mutated
- `site`: Qualitative, categorical. Encoded <chr>, to be mutated


::: {.cell}

```{.r .cell-code}
# glimpse() / inspect() / skim()
skimr::skim(B)
```

::: {.cell-output-display}

Table: Data summary

|                         |     |
|:------------------------|:----|
|Name                     |B    |
|Number of rows           |602  |
|Number of columns        |7    |
|_______________________  |     |
|Column type frequency:   |     |
|character                |7    |
|________________________ |     |
|Group variables          |None |


**Variable type: character**

|skim_variable | n_missing| complete_rate| min| max| empty| n_unique| whitespace|
|:-------------|---------:|-------------:|---:|---:|-----:|--------:|----------:|
|gender        |         0|             1|   4|   6|     0|        2|          0|
|rx            |         0|             1|   7|  12|     0|        2|          0|
|outcome       |         0|             1|   2|   3|     0|        2|          0|
|pep           |         0|             1|   2|   3|     0|        2|          0|
|status        |         0|             1|   9|  10|     0|        2|          0|
|type          |         0|             1|   8|   8|     0|        4|          0|
|site          |         0|             1|   4|   6|     0|        4|          0|


:::
:::


Further, on the Qual variables panel-B data has : 

- `gender` has 2 levels: "male" and "female"
- `rx` has 2 levels: "indomethacin" and "placebo"
- `outcome` has 2 levels: "no" and "yes"
- `pep` has 2 levels: "no" and "yes"
- `status` has 2 levels: "outpatient" and "inpatient"
- `type` has 4 levels: to be listed
- `site` has 4 levels: to be listed

And on the Quant variables panel-A data has : None. All variable are Qualitative only. 

There is no missing data.


::: {.cell}

```{.r .cell-code}
B_mod <- B |> 
  mutate(across(where(is.character), as.factor)) # Whoa!!

glimpse(B_mod)
```

::: {.cell-output .cell-output-stdout}

```
Rows: 602
Columns: 7
$ gender  <fct> female, male, female, female, female, female, female, male, ma…
$ rx      <fct> indomethacin, placebo, placebo, placebo, indomethacin, placebo…
$ outcome <fct> yes, no, no, yes, no, no, no, no, no, no, no, yes, no, no, no,…
$ pep     <fct> no, yes, no, no, yes, no, no, no, no, no, no, no, no, no, no, …
$ status  <fct> outpatient, outpatient, outpatient, outpatient, inpatient, inp…
$ type    <fct> 1_type 1, 0_no SOD, 2_type 2, 1_type 1, 1_type 1, 1_type 1, 0_…
$ site    <fct> 1_UM, 1_UM, 1_UM, 1_UM, 1_UM, 1_UM, 1_UM, 1_UM, 1_UM, 1_UM, 1_…
```


:::
:::


Now, we're ready.

### 2. Ask

~~One question *this* table can answer. One it cannot. Write both.~~

Looking at the spine statement again, I can pose the following Questions:

Possibly can answer:

1. Are the dataset balanced by all Qual variables, except the measured one (`output`) ?
1. Are there differences in *how the medication was administered* ( i.e.`rx`)  across `gender`, `pep`, `site` or `type` of patient? ( Just curious. Does this matter? Need to ask the expert who did this research.)
1. Does the medication work? (Main Character Question)
1. Does it work differently for men and for women? ( Possible Main Character Question)

Possibly cannot answer:

1. Does the medication efficacy depend upon dosage? ( No `dosage` variable.)

### 3. Choose a mark

1. Bar Chart
1. Bar Chart with fill or facet
1. Mosaic Chart (NEW!!)
1.  Mosaic Chart again??

### 4. Predict

~~What do we think we will see? One sentence, before Run.~~

A well-balanced RCT experiment, balanced across all `factors` such as `gender`, `pep`, `site`, and `type`. 

I think the medicine might show as working in this experiment. 

### 5. Make

~~Human brick first. Optional: one AI prompt, pasted below, only for what section 3 named.~~

These first few charts are what I want to check out. I will not have these charts in the final rendered Quarto, but comment on findings. 

If any is surprising, it stays. 


::: {.cell}

```{.r .cell-code}
## Question 1
B_mod |> gf_bar(~ gender)
B_mod |> gf_bar(~ rx)
B_mod |> gf_bar(~ pep)
B_mod |> gf_bar(~ status)
B_mod |> gf_bar(~ type)
B_mod |> gf_bar(~ site)
```
:::

- `gender`: More women than men
- ` rx`: Medication and placebo prescribed to equal numbers. Fair. 
- All other factors have imbalanced counts. It is what it is. 

::: {.cell}

```{.r .cell-code}
## Question 2
B_mod |> gf_bar(~ gender, fill = ~ rx)
B_mod |> gf_bar(~ pep, fill = ~ rx)
B_mod |> gf_bar(~ status, fill = ~ rx)
B_mod |> gf_bar(~ type, fill = ~ rx)
B_mod |> gf_bar(~ site, fill = ~ rx)
```
:::

All kinds of people, based on ***each of the factors*** are EQUALLY divided between `indomethacin` and `placebo`. Superb experiment design!! Waah, waah!!

NOTE: this can be visually confirmed by using `gf_props` for all code lines in the preceding chunk. 



::: {.cell}

```{.r .cell-code}
# Question #3
# library(vcd) # Put this in the setup chunk above!
# 
B_table <- vcd::structable(outcome ~ rx, data = B_mod)
B_table
```

::: {.cell-output .cell-output-stdout}

```
             outcome  no yes
rx                          
indomethacin         268  27
placebo              255  52
```


:::

```{.r .cell-code}
# Main Character Question
vcd::mosaic(B_table, shade = TRUE, main = "Mosaic Chart for Indomethacin RCT")
```

::: {.cell-output-display}
![](studio-wall_files/figure-html/main-character-question-B-1.png){width=2100}
:::
:::



### 6. Say

~~Caption + one sentence we will stand behind.~~

The mosaic plot’s saying there’s a significant diff between `indomethacin` and `placebo` performance. But plot twist: the diff is wrong-sided: placebo’s actually slaying. Main character `indomethacin` is kinda flopping compared to placebo. Low-key mid, high-key not it. We need a new main. 

### 7. Limit

~~What this panel cannot do.~~

I cannot say whether this trial would show similar results if conducted in another community. I presume the experimenters thought of this beforehand. 


# Panel C — place

*Americas-only lifeExp / gdpPercap / pop. Same join brick as other map panels.*

```r
# library(sf)
# library(rnaturalearth)
# world <- ne_countries(scale = "medium", returnclass = "sf")
```


### 1. See the table

What is a case? Which columns are Quant / Qual? What is missing?


::: {.cell}

```{.r .cell-code}
# glimpse() / inspect() / skim()
dplyr::glimpse(C)
```

::: {.cell-output .cell-output-stdout}

```
Rows: 25
Columns: 6
$ country   <chr> "Argentina", "Bolivia", "Brazil", "Canada", "Chile", "Colomb…
$ continent <chr> "Americas", "Americas", "Americas", "Americas", "Americas", …
$ year      <dbl> 2007, 2007, 2007, 2007, 2007, 2007, 2007, 2007, 2007, 2007, …
$ lifeExp   <dbl> 75.320, 65.554, 72.390, 80.653, 78.553, 72.889, 78.782, 78.2…
$ pop       <dbl> 40301927, 9119152, 190010647, 33390141, 16284741, 44227550, …
$ gdpPercap <dbl> 12779.380, 3822.137, 9065.801, 36319.235, 13171.639, 7006.58…
```


:::
:::

My Panel-C data has:

- 25 rows and 6 columns.
- `country`: Qualitative, categorical. Encoded <chr>, to be mutated
- `continent`: Qualitative, categorical. Encoded <chr>, to be mutated
- `year`: Quantitative, numeric. Looks like it is just 2007 right through and data is for one year only. 
- `lifeExp`: Quantitative, numeric
- `pop`: Quantitative, numeric
- `gdpPercap`: Quantitative, numeric

Let us `join` this data with map geometries from `rnaturalearth`, so that we can make a map to show off the status in 2007. 


::: {.cell}

```{.r .cell-code}
# library(sf) ( Add this into setup chunk above!)
# library(rnaturalearth)
world <- rnaturalearth::ne_countries(scale = "medium", returnclass = "sf")
glimpse(world)
```

::: {.cell-output .cell-output-stdout}

```
Rows: 242
Columns: 169
$ featurecla <chr> "Admin-0 country", "Admin-0 country", "Admin-0 country", "A…
$ scalerank  <int> 1, 1, 1, 3, 5, 6, 1, 1, 1, 3, 5, 3, 3, 3, 3, 1, 5, 3, 3, 3,…
$ labelrank  <int> 3, 3, 3, 2, 3, 6, 4, 3, 4, 6, 6, 6, 6, 6, 4, 5, 2, 4, 5, 6,…
$ sovereignt <chr> "Zimbabwe", "Zambia", "Yemen", "Vietnam", "Venezuela", "Vat…
$ sov_a3     <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_dif   <int> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1,…
$ level      <int> 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2,…
$ type       <chr> "Sovereign country", "Sovereign country", "Sovereign countr…
$ tlc        <chr> "1", "1", "1", "1", "1", "1", "1", "1", "1", "1", "1", "1",…
$ admin      <chr> "Zimbabwe", "Zambia", "Yemen", "Vietnam", "Venezuela", "Vat…
$ adm0_a3    <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ geou_dif   <int> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,…
$ geounit    <chr> "Zimbabwe", "Zambia", "Yemen", "Vietnam", "Venezuela", "Vat…
$ gu_a3      <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ su_dif     <int> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,…
$ subunit    <chr> "Zimbabwe", "Zambia", "Yemen", "Vietnam", "Venezuela", "Vat…
$ su_a3      <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ brk_diff   <int> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0,…
$ name       <chr> "Zimbabwe", "Zambia", "Yemen", "Vietnam", "Venezuela", "Vat…
$ name_long  <chr> "Zimbabwe", "Zambia", "Yemen", "Vietnam", "Venezuela", "Vat…
$ brk_a3     <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ brk_name   <chr> "Zimbabwe", "Zambia", "Yemen", "Vietnam", "Venezuela", "Vat…
$ brk_group  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ abbrev     <chr> "Zimb.", "Zambia", "Yem.", "Viet.", "Ven.", "Vat.", "Van.",…
$ postal     <chr> "ZW", "ZM", "YE", "VN", "VE", "V", "VU", "UZ", "UY", "FSM",…
$ formal_en  <chr> "Republic of Zimbabwe", "Republic of Zambia", "Republic of …
$ formal_fr  <chr> NA, NA, NA, NA, "República Bolivariana de Venezuela", NA, N…
$ name_ciawf <chr> "Zimbabwe", "Zambia", "Yemen", "Vietnam", "Venezuela", "Hol…
$ note_adm0  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, "U.S.A.", "U.S.…
$ note_brk   <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ name_sort  <chr> "Zimbabwe", "Zambia", "Yemen, Rep.", "Vietnam", "Venezuela,…
$ name_alt   <chr> NA, NA, NA, NA, NA, "Holy See", NA, NA, NA, NA, NA, NA, NA,…
$ mapcolor7  <int> 1, 5, 5, 5, 1, 1, 6, 2, 1, 5, 2, 4, 4, 4, 4, 4, 4, 6, 6, 6,…
$ mapcolor8  <int> 5, 8, 3, 6, 3, 3, 3, 3, 2, 2, 5, 5, 5, 5, 5, 5, 5, 6, 6, 6,…
$ mapcolor9  <int> 3, 5, 3, 5, 1, 4, 7, 5, 2, 4, 5, 1, 1, 1, 1, 1, 1, 6, 6, 6,…
$ mapcolor13 <int> 9, 13, 11, 4, 4, 2, 3, 4, 10, 13, 3, 1, 1, 1, 1, 1, 1, 3, 3…
$ pop_est    <dbl> 14645468, 17861030, 29161922, 96462106, 28515829, 825, 2998…
$ pop_rank   <int> 14, 14, 15, 16, 15, 2, 10, 15, 12, 9, 8, 8, 9, 9, 8, 12, 17…
$ pop_year   <int> 2019, 2019, 2019, 2019, 2019, 2019, 2019, 2019, 2019, 2019,…
$ gdp_md     <int> 21440, 23309, 22581, 261921, 482359, -99, 934, 57921, 56045…
$ gdp_year   <int> 2019, 2019, 2019, 2019, 2014, 2019, 2019, 2019, 2019, 2018,…
$ economy    <chr> "5. Emerging region: G20", "7. Least developed region", "7.…
$ income_grp <chr> "5. Low income", "4. Lower middle income", "4. Lower middle…
$ fips_10    <chr> "ZI", "ZA", "YM", "VM", "VE", "VT", "NH", "UZ", "UY", "FM",…
$ iso_a2     <chr> "ZW", "ZM", "YE", "VN", "VE", "VA", "VU", "UZ", "UY", "FM",…
$ iso_a2_eh  <chr> "ZW", "ZM", "YE", "VN", "VE", "VA", "VU", "UZ", "UY", "FM",…
$ iso_a3     <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ iso_a3_eh  <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ iso_n3     <chr> "716", "894", "887", "704", "862", "336", "548", "860", "85…
$ iso_n3_eh  <chr> "716", "894", "887", "704", "862", "336", "548", "860", "85…
$ un_a3      <chr> "716", "894", "887", "704", "862", "336", "548", "860", "85…
$ wb_a2      <chr> "ZW", "ZM", "RY", "VN", "VE", "-99", "VU", "UZ", "UY", "FM"…
$ wb_a3      <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "-99", "VUT", "UZB", "UR…
$ woe_id     <int> 23425004, 23425003, 23425002, 23424984, 23424982, 23424986,…
$ woe_id_eh  <int> 23425004, 23425003, 23425002, 23424984, 23424982, 23424986,…
$ woe_note   <chr> "Exact WOE match as country", "Exact WOE match as country",…
$ adm0_iso   <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_diff  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ adm0_tlc   <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_us <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_fr <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_ru <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_es <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_cn <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_tw <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_in <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_np <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_pk <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_de <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_gb <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_br <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_il <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_ps <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_sa <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_eg <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_ma <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_pt <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_ar <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_jp <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_ko <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_vn <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_tr <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_id <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_pl <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_gr <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_it <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_nl <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_se <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_bd <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_ua <chr> "ZWE", "ZMB", "YEM", "VNM", "VEN", "VAT", "VUT", "UZB", "UR…
$ adm0_a3_un <int> -99, -99, -99, -99, -99, -99, -99, -99, -99, -99, -99, -99,…
$ adm0_a3_wb <int> -99, -99, -99, -99, -99, -99, -99, -99, -99, -99, -99, -99,…
$ continent  <chr> "Africa", "Africa", "Asia", "Asia", "South America", "Europ…
$ region_un  <chr> "Africa", "Africa", "Asia", "Asia", "Americas", "Europe", "…
$ subregion  <chr> "Eastern Africa", "Eastern Africa", "Western Asia", "South-…
$ region_wb  <chr> "Sub-Saharan Africa", "Sub-Saharan Africa", "Middle East & …
$ name_len   <int> 8, 6, 5, 7, 9, 7, 7, 10, 7, 10, 12, 14, 15, 4, 14, 11, 24, …
$ long_len   <int> 8, 6, 5, 7, 9, 7, 7, 10, 7, 30, 16, 24, 28, 4, 14, 11, 13, …
$ abbrev_len <int> 5, 6, 4, 5, 4, 4, 4, 4, 4, 6, 6, 6, 11, 4, 9, 4, 6, 10, 6, …
$ tiny       <int> -99, -99, -99, 2, -99, 4, 2, 5, -99, -99, 2, 3, 3, 2, 3, -9…
$ homepart   <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, -99, -99, -99, -99, -99, 1…
$ min_zoom   <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,…
$ min_label  <dbl> 2.5, 3.0, 3.0, 2.0, 2.5, 5.0, 4.0, 3.0, 3.0, 5.0, 5.0, 5.0,…
$ max_label  <dbl> 8.0, 8.0, 8.0, 7.0, 7.5, 10.0, 9.0, 8.0, 8.0, 10.0, 10.0, 1…
$ label_x    <dbl> 29.92544, 26.39530, 45.87438, 105.38729, -64.59938, 12.4534…
$ label_y    <dbl> -18.911640, -14.660804, 15.328226, 21.715416, 7.182476, 41.…
$ ne_id      <dbl> 1159321441, 1159321439, 1159321425, 1159321417, 1159321411,…
$ wikidataid <chr> "Q954", "Q953", "Q805", "Q881", "Q717", "Q237", "Q686", "Q2…
$ name_ar    <chr> "زيمبابوي", "زامبيا", "اليمن", "فيتنام", "فنزويلا", "الفاتي…
$ name_bn    <chr> "জিম্বাবুয়ে", "জাম্বিয়া", "ইয়েমেন", "ভিয়েতনাম", "ভেনেজুয়েলা", "ভ্যাটিকান সিটি", "ভানুয়াতু",…
$ name_de    <chr> "Simbabwe", "Sambia", "Jemen", "Vietnam", "Venezuela", "Vat…
$ name_en    <chr> "Zimbabwe", "Zambia", "Yemen", "Vietnam", "Venezuela", "Vat…
$ name_es    <chr> "Zimbabue", "Zambia", "Yemen", "Vietnam", "Venezuela", "Ciu…
$ name_fa    <chr> "زیمبابوه", "زامبیا", "یمن", "ویتنام", "ونزوئلا", "واتیکان"…
$ name_fr    <chr> "Zimbabwe", "Zambie", "Yémen", "Viêt Nam", "Venezuela", "Ci…
$ name_el    <chr> "Ζιμπάμπουε", "Ζάμπια", "Υεμένη", "Βιετνάμ", "Βενεζουέλα", …
$ name_he    <chr> "זימבבואה", "זמביה", "תימן", "וייטנאם", "ונצואלה", "קריית ה…
$ name_hi    <chr> "ज़िम्बाब्वे", "ज़ाम्बिया", "यमन", "वियतनाम", "वेनेज़ुएला", "वैटिकन नगर", "वानूआटू", …
$ name_hu    <chr> "Zimbabwe", "Zambia", "Jemen", "Vietnám", "Venezuela", "Vat…
$ name_id    <chr> "Zimbabwe", "Zambia", "Yaman", "Vietnam", "Venezuela", "Vat…
$ name_it    <chr> "Zimbabwe", "Zambia", "Yemen", "Vietnam", "Venezuela", "Cit…
$ name_ja    <chr> "ジンバブエ", "ザンビア", "イエメン", "ベトナム", "ベネズエラ", "バチカン", "バヌアツ", "…
$ name_ko    <chr> "짐바브웨", "잠비아", "예멘", "베트남", "베네수엘라", "바티칸 시국", "바누아투", "우즈베…
$ name_nl    <chr> "Zimbabwe", "Zambia", "Jemen", "Vietnam", "Venezuela", "Vat…
$ name_pl    <chr> "Zimbabwe", "Zambia", "Jemen", "Wietnam", "Wenezuela", "Wat…
$ name_pt    <chr> "Zimbábue", "Zâmbia", "Iémen", "Vietname", "Venezuela", "Va…
$ name_ru    <chr> "Зимбабве", "Замбия", "Йемен", "Вьетнам", "Венесуэла", "Ват…
$ name_sv    <chr> "Zimbabwe", "Zambia", "Jemen", "Vietnam", "Venezuela", "Vat…
$ name_tr    <chr> "Zimbabve", "Zambiya", "Yemen", "Vietnam", "Venezuela", "Va…
$ name_uk    <chr> "Зімбабве", "Замбія", "Ємен", "В'єтнам", "Венесуела", "Вати…
$ name_ur    <chr> "زمبابوے", "زیمبیا", "یمن", "ویتنام", "وینیزویلا", "ویٹیکن …
$ name_vi    <chr> "Zimbabwe", "Zambia", "Yemen", "Việt Nam", "Venezuela", "Th…
$ name_zh    <chr> "津巴布韦", "赞比亚", "也门", "越南", "委内瑞拉", "梵蒂冈", "瓦努阿图", "乌兹别克斯坦",…
$ name_zht   <chr> "辛巴威", "尚比亞", "葉門", "越南", "委內瑞拉", "梵蒂岡", "萬那杜", "烏茲別克", "烏拉…
$ fclass_iso <chr> "Admin-0 country", "Admin-0 country", "Admin-0 country", "A…
$ tlc_diff   <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_tlc <chr> "Admin-0 country", "Admin-0 country", "Admin-0 country", "A…
$ fclass_us  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_fr  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_ru  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_es  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_cn  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_tw  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_in  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_np  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_pk  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_de  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_gb  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_br  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_il  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_ps  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_sa  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_eg  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_ma  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_pt  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_ar  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_jp  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_ko  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_vn  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_tr  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_id  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_pl  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_gr  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_it  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_nl  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_se  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_bd  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ fclass_ua  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
$ geometry   <MULTIPOLYGON [°]> MULTIPOLYGON (((31.28789 -2..., MULTIPOLYGON (…
```


:::
:::


`world` is an `sf` data frame, with a MULTIPOLYGON `geometry` column, which we need. It has 177 rows and 64 columns. 

The `name` column has the name of the country, which is what we will use to `join` with our Panel-C data.

Let us select just the `name` and `geometry` columns first, and then join with panel-C data. In passing, we will also mutate `country` and `continent` into factors.

NOTE: this method fails for the USA, since panel-C describes that country as "United States" and world as "United States of America". We will make the change in Panel-C, before joining:


::: {.cell}

```{.r .cell-code}
world_selected <- world |> dplyr::select(name, geometry)
C_mod <- C |>
  ###
  mutate(country = str_replace(
    string = country,
    pattern = "United States",
    replacement = "United States of America"
  )) |>
  ###
  left_join(world_selected, by = c("country" = "name")) |>
  ###
  mutate(
    country = as_factor(country),
    continent = as_factor(continent)
  ) |>
  ###
  st_as_sf() # Convert to sf object, just to be careful
glimpse(C_mod)
```

::: {.cell-output .cell-output-stdout}

```
Rows: 25
Columns: 7
$ country   <fct> Argentina, Bolivia, Brazil, Canada, Chile, Colombia, Costa R…
$ continent <fct> Americas, Americas, Americas, Americas, Americas, Americas, …
$ year      <dbl> 2007, 2007, 2007, 2007, 2007, 2007, 2007, 2007, 2007, 2007, …
$ lifeExp   <dbl> 75.320, 65.554, 72.390, 80.653, 78.553, 72.889, 78.782, 78.2…
$ pop       <dbl> 40301927, 9119152, 190010647, 33390141, 16284741, 44227550, …
$ gdpPercap <dbl> 12779.380, 3822.137, 9065.801, 36319.235, 13171.639, 7006.58…
$ geometry  <MULTIPOLYGON [°]> MULTIPOLYGON (((-57.60889 -..., MULTIPOLYGON ((…
```


:::
:::


::: {.cell}

```{.r .cell-code}
skimr::skim(C_mod)
```

::: {.cell-output .cell-output-stderr}

```
Warning: Couldn't find skimmers for class: sfc_MULTIPOLYGON, sfc; No
user-defined `sfl` provided. Falling back to `character`.
```


:::

::: {.cell-output-display}

Table: Data summary

|                         |      |
|:------------------------|:-----|
|Name                     |C_mod |
|Number of rows           |25    |
|Number of columns        |7     |
|_______________________  |      |
|Column type frequency:   |      |
|character                |1     |
|factor                   |2     |
|numeric                  |4     |
|________________________ |      |
|Group variables          |None  |


**Variable type: character**

|skim_variable | n_missing| complete_rate| min|    max| empty| n_unique| whitespace|
|:-------------|---------:|-------------:|---:|------:|-----:|--------:|----------:|
|geometry      |         0|             1|   6| 390722|     0|       25|          0|


**Variable type: factor**

|skim_variable | n_missing| complete_rate|ordered | n_unique|top_counts                     |
|:-------------|---------:|-------------:|:-------|--------:|:------------------------------|
|country       |         0|             1|FALSE   |       25|Arg: 1, Bol: 1, Bra: 1, Can: 1 |
|continent     |         0|             1|FALSE   |        1|Ame: 25                        |


**Variable type: numeric**

|skim_variable | n_missing| complete_rate|        mean|          sd|         p0|        p25|       p50|         p75|         p100|hist  |
|:-------------|---------:|-------------:|-----------:|-----------:|----------:|----------:|---------:|-----------:|------------:|:-----|
|year          |         0|             1|     2007.00|        0.00|    2007.00|    2007.00|    2007.0|     2007.00|      2007.00|▁▁▇▁▁ |
|lifeExp       |         0|             1|       73.61|        4.44|      60.92|      71.75|      72.9|       76.38|        80.65|▁▁▇▇▅ |
|pop           |         0|             1| 35954847.36| 68833781.19| 1056608.00| 5675356.00| 9319622.0| 28674757.00| 301139947.00|▇▁▁▁▁ |
|gdpPercap     |         0|             1|    11003.03|     9713.21|    1201.64|    5728.35|    8948.1|    11977.57|     42951.65|▇▃▁▁▁ |


:::
:::


Finally, after joining, on the Qual variables panel-C data has :

- `country`: Qualitative, categorical. Encoded <fct> with 25 levels: "Argentina", "Bolivia", "Brazil", "Canada", "Chile", "Colombia", "Costa Rica", "Cuba", "Dominican Republic", "Ecuador", "El Salvador", "French Guiana", "Guatemala", "Guyana", "Haiti", "Honduras", "Jamaica", "Mexico", "Nicaragua", "Panama", "Paraguay", "Peru", "Suriname", "Trinidad and Tobago", and "United States".
- `continent`: Qualitative, categorical. Encoded <fct> with 1 level: "Americas"

And the Quant variables are: 

- `year`: Quantitative, numeric. Encoded <dbl> with 1 level: 2007
- `lifeExp`: Quantitative, numeric. Mean: $7.360812e+01$, sd: $4.440948e+00$, min: $60.916$	, median: $72.899$, max:  $80.65$
- `pop`: Quantitative, numeric.Mean: $3.595485e+07$, sd: $6.883378e+07$, min: $1056608.000$	, median: $9319622.000$, max:  $3.011399e+08$
- `gdpPercap`: Quantitative, numeric. Mean: $1.100303e+04$, sd: $9.713209e+03$, min: $1201.637$	, median: $8948.103$, max:  $4.295165e+04$

There is no missing data. Histograms shown in `skim()` output are extremely right-skewed for `pop` and `gdpPercap`!! 

### 2. Ask

~~One question *this* table can answer. One it cannot. Write both.~~

Based on the Spine, I can pose the following Questions:

Possibly can answer:

1. How are the Quant variables distributed?
1. Are `gdpPercap` and`lifeExp` correlated across the Americas? (Main Character Question)
2. How do the quantities `gdpPercap`, `lifeExp`, and `pop` vary across the Americas? (Also Main Character Question)

Possibly cannot answer:

1. Does global trade / wars affect longevity? ( No data)
2. Do the quant variables vary over time? ( Data is only for 2007)

### 3. Choose a mark

~~Geom, map mark, or (later) test that matches the pair type. Name it before you code.~~

1. Scatter Plot + Correlation Test (NEW!!)
2. Maps ( NEW!!)

### 4. Predict

~~What do we think we will see? One sentence, before Run.~~

1. I think `lifeExp` is correlated positively with `gdpPercap. 
2. The map will show stark variation in each of the quantities across the Americas, since there are poor-er countries there like say in Latin America, and then there are US and Canada. 

### 5. Make

~Human brick first. Optional: one AI prompt, pasted below, only for what section 3 named.~


::: {.cell}

```{.r .cell-code}
C_mod |> gf_histogram(~ lifeExp)
C_mod |> gf_histogram(~ pop)
C_mod |> gf_histogram(~ gdpPercap)
```
:::


As expected(?) `lifeExp` is skewed left, and `pop` and `gdpPercap` are skewed right!!


::: {.cell}

```{.r .cell-code}
# Question #2
C_mod |> gf_point(lifeExp ~ gdpPercap) |> 
  gf_smooth(method = "lm", title = "Higher GDP indicates higher Life Expectancy, but outliers exist", subtitle = "Should we try removing them? Later!!")
```

::: {.cell-output-display}
![](studio-wall_files/figure-html/main-character-question-C-1-1.png){width=2100}
:::

```{.r .cell-code}
mosaic::cor.test(lifeExp ~ gdpPercap, 
                 data = C_mod) |> 
  broom::tidy()
```

::: {.cell-output-display}
`````{=html}
<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["estimate"],"name":[1],"type":["dbl"],"align":["right"]},{"label":["statistic"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["p.value"],"name":[3],"type":["dbl"],"align":["right"]},{"label":["parameter"],"name":[4],"type":["int"],"align":["right"]},{"label":["conf.low"],"name":[5],"type":["dbl"],"align":["right"]},{"label":["conf.high"],"name":[6],"type":["dbl"],"align":["right"]},{"label":["method"],"name":[7],"type":["chr"],"align":["left"]},{"label":["alternative"],"name":[8],"type":["chr"],"align":["left"]}],"data":[{"1":"0.5909661","2":"3.51331","3":"0.001866495","4":"23","5":"0.2554958","6":"0.7994243","7":"Pearson's product-moment correlation","8":"two.sided"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>
`````
:::
:::



::: {.cell}

```{.r .cell-code}
# Question #3

C_mod |> gf_sf(geometry = ~ geometry, fill = ~ gdpPercap, title = "Which are the High GDPperCap Countries?", subtitle = "Everything south of Mexico....") |>
  gf_refine(
    coord_sf(
      xlim = c(-170, -30),  # longitude: 170°W to 30°W
      ylim = c(-60, 85),    # latitude: South to North America
      expand = FALSE
    )
  )
```

::: {.cell-output-display}
![](studio-wall_files/figure-html/main-character-question-C-2-1.png){width=2100}
:::

```{.r .cell-code}
C_mod |> gf_sf(geometry = ~ geometry, fill = ~ lifeExp, title = "Which are the High LifeExp Countries?", subtitle = "Is that Bolivia....and Latin America???", caption = "El Salvador has imnproved in recent times!! Not in this graph!") |>
  gf_refine(
    coord_sf(
      xlim = c(-170, -30),  # longitude: 170°W to 30°W
      ylim = c(-60, 85),    # latitude: South to North America
      expand = FALSE
    )
  )
```

::: {.cell-output-display}
![](studio-wall_files/figure-html/main-character-question-C-2-2.png){width=2100}
:::

```{.r .cell-code}
C_mod |> gf_sf(geometry = ~ geometry, fill = ~ pop, title = "Where do more people live?", subtitle = "West of the Andes is low population? Why?") |>
  gf_refine(
    coord_sf(
      xlim = c(-170, -30),  # longitude: 170°W to 30°W
      ylim = c(-60, 85),    # latitude: South to North America
      expand = FALSE
    )
  )
```

::: {.cell-output-display}
![](studio-wall_files/figure-html/main-character-question-C-2-3.png){width=2100}
:::
:::




### 6. Say

~~Caption + one sentence we will stand behind.~~

Across the Americas, based on 2007 data from `gapminder`, `lifeExp`ectancy and `gdpPercap` are positively correlated (correlation coefficient = 0.59, p-value < 0.05).

The correlation would perhaps even be higher, if we left out a few high `gddperCap` countries since these are clearly outliers with a huge amount of `leverage`. ( Which countries are these?)


::: {.cell}

```{.r .cell-code}
C_mod_2 <- C_mod |> 
  filter(!gdpPercap >= 15000)
C_mod_2
```

::: {.cell-output-display}
`````{=html}
<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":[""],"name":["_rn_"],"type":[""],"align":["left"]},{"label":["country"],"name":[1],"type":["fct"],"align":["left"]},{"label":["continent"],"name":[2],"type":["fct"],"align":["left"]},{"label":["year"],"name":[3],"type":["dbl"],"align":["right"]},{"label":["lifeExp"],"name":[4],"type":["dbl"],"align":["right"]},{"label":["pop"],"name":[5],"type":["dbl"],"align":["right"]},{"label":["gdpPercap"],"name":[6],"type":["dbl"],"align":["right"]},{"label":["geometry"],"name":[7],"type":["s_MULTIP"],"align":["right"]}],"data":[{"1":"Argentina","2":"Americas","3":"2007","4":"75.320","5":"40301927","6":"12779.380","7":"<s_MULTIP>","_rn_":"1"},{"1":"Bolivia","2":"Americas","3":"2007","4":"65.554","5":"9119152","6":"3822.137","7":"<s_MULTIP>","_rn_":"2"},{"1":"Brazil","2":"Americas","3":"2007","4":"72.390","5":"190010647","6":"9065.801","7":"<s_MULTIP>","_rn_":"3"},{"1":"Chile","2":"Americas","3":"2007","4":"78.553","5":"16284741","6":"13171.639","7":"<s_MULTIP>","_rn_":"4"},{"1":"Colombia","2":"Americas","3":"2007","4":"72.889","5":"44227550","6":"7006.580","7":"<s_MULTIP>","_rn_":"5"},{"1":"Costa Rica","2":"Americas","3":"2007","4":"78.782","5":"4133884","6":"9645.061","7":"<s_MULTIP>","_rn_":"6"},{"1":"Cuba","2":"Americas","3":"2007","4":"78.273","5":"11416987","6":"8948.103","7":"<s_MULTIP>","_rn_":"7"},{"1":"Dominican Republic","2":"Americas","3":"2007","4":"72.235","5":"9319622","6":"6025.375","7":"<s_MULTIP>","_rn_":"8"},{"1":"Ecuador","2":"Americas","3":"2007","4":"74.994","5":"13755680","6":"6873.262","7":"<s_MULTIP>","_rn_":"9"},{"1":"El Salvador","2":"Americas","3":"2007","4":"71.878","5":"6939688","6":"5728.354","7":"<s_MULTIP>","_rn_":"10"},{"1":"Guatemala","2":"Americas","3":"2007","4":"70.259","5":"12572928","6":"5186.050","7":"<s_MULTIP>","_rn_":"11"},{"1":"Haiti","2":"Americas","3":"2007","4":"60.916","5":"8502814","6":"1201.637","7":"<s_MULTIP>","_rn_":"12"},{"1":"Honduras","2":"Americas","3":"2007","4":"70.198","5":"7483763","6":"3548.331","7":"<s_MULTIP>","_rn_":"13"},{"1":"Jamaica","2":"Americas","3":"2007","4":"72.567","5":"2780132","6":"7320.880","7":"<s_MULTIP>","_rn_":"14"},{"1":"Mexico","2":"Americas","3":"2007","4":"76.195","5":"108700891","6":"11977.575","7":"<s_MULTIP>","_rn_":"15"},{"1":"Nicaragua","2":"Americas","3":"2007","4":"72.899","5":"5675356","6":"2749.321","7":"<s_MULTIP>","_rn_":"16"},{"1":"Panama","2":"Americas","3":"2007","4":"75.537","5":"3242173","6":"9809.186","7":"<s_MULTIP>","_rn_":"17"},{"1":"Paraguay","2":"Americas","3":"2007","4":"71.752","5":"6667147","6":"4172.838","7":"<s_MULTIP>","_rn_":"18"},{"1":"Peru","2":"Americas","3":"2007","4":"71.421","5":"28674757","6":"7408.906","7":"<s_MULTIP>","_rn_":"19"},{"1":"Uruguay","2":"Americas","3":"2007","4":"76.384","5":"3447496","6":"10611.463","7":"<s_MULTIP>","_rn_":"20"},{"1":"Venezuela","2":"Americas","3":"2007","4":"73.747","5":"26084662","6":"11415.806","7":"<s_MULTIP>","_rn_":"21"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>
`````
:::

```{.r .cell-code}
C_mod_2 |> gf_point(lifeExp ~ gdpPercap) |> gf_smooth(method = "lm", title = "Higher GDP indicates higher Life Expectancy, but outliers exist", subtitle = "Excluding countries with gdpPercap >= 15000")
```

::: {.cell-output-display}
![](studio-wall_files/figure-html/unnamed-chunk-1-1.png){width=2100}
:::
:::




::: {#tbl-t-test-gapminder .cell tbl-cap='Life Expectancy Correlates with GDP per Capita'}

```{.r .cell-code}
mosaic::cor.test(lifeExp ~ gdpPercap, 
                 data = C_mod_2) |> 
  broom::tidy()
```

::: {.cell-output-display}
`````{=html}
<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["estimate"],"name":[1],"type":["dbl"],"align":["right"]},{"label":["statistic"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["p.value"],"name":[3],"type":["dbl"],"align":["right"]},{"label":["parameter"],"name":[4],"type":["int"],"align":["right"]},{"label":["conf.low"],"name":[5],"type":["dbl"],"align":["right"]},{"label":["conf.high"],"name":[6],"type":["dbl"],"align":["right"]},{"label":["method"],"name":[7],"type":["chr"],"align":["left"]},{"label":["alternative"],"name":[8],"type":["chr"],"align":["left"]}],"data":[{"1":"0.7805573","2":"5.443059","3":"2.984187e-05","4":"19","5":"0.5261652","6":"0.9067193","7":"Pearson's product-moment correlation","8":"two.sided"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>
`````
:::
:::



The maps show the disparity in all the three quant variables across the Americas. Interestingly, the USA and Brazil turn out to have the highest populations, while Canada and other South American coutries are lower on the `pop` scale. 

### 7. Limit

~~What this panel cannot do.~~

Speak of trade or of aid!
Nor speak of Asia.
Nor of variations over TIME!

# Panel D - The Erin Brokovich Project

### 1. See the table
### 2. Ask
### 3. Choose a mark
### 4. Predict
### 5. Make
### 6. Say
### 7. Limit


# Panel E — Found Data

Where is my Free Hunch ???? But let us paste the sub-section titles here for reference:

### 1. See the table
### 2. Ask
### 3. Choose a mark
### 4. Predict
### 5. Make
### 6. Say
### 7. Limit


# Conclusion

- The question we will defend in ninety seconds
- What we will **not** claim
- Which lines were human, which were machine
- Limits
- Gift for the steal-this strip

