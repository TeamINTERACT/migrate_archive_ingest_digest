Wave 1 Saskatoon Linkage
================
Daniel Fuller

# Saskatoon Wave 1 Linkage Files

### Read in Data

Reading in the data. Here we are reading in two files

-   `linkage_for_ingest_skt_w1.csv` which is our main file
-   `lut_skt.csv` which is a look up table for looking up previous
    versions of the health and VERITAS ID variables. These will not be
    used after Wave 1.

``` r
setwd("/Users/dlf545/Documents/ForDan_Linkages_072023")
skt_w1 <- read_delim("linkage_for_ingest_skt_w1.csv", delim = ";")
```

    ## Rows: 401 Columns: 14
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ";"
    ## chr (6): sd_start_1, sd_end_1, sd_start_2, sd_end_2, notes, data_disposition
    ## dbl (4): interact_id, ethica_id, sd_id_1, sd_id_2
    ## lgl (4): sd_firmware_1, sd_firmware_2, test, dropout
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
lut_skt <- read_delim("lut_skt.csv", delim = ",")
```

    ## Rows: 403 Columns: 4
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): ethica_id
    ## dbl (3): interact_id, treksoft_pid, treksoft_uid
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
skt <- full_join(skt_w1, lut_skt, by="interact_id")
```

## Ethica test accounts

List of test accounts used by our research team to test the Ethica app.
These will be flagged later.

``` r
ethica_tests <- c("1451", "2036", 
                  "5256", "1454", 
                  "8911", "1469",
                  "1462", "9321", 
                  "12641", "12638", 
                  "12617", "1470",
                  "4579", "5367",
                  "5399","3962")
```

## Cleaning up dates

There are problems with SD dates in Saskatoon Wave 1

-   this file: `linkage_for_ingest_skt_w1.csv` has wrongly encoded
    dates, but generally 9 day periods. Dates in 2010. This is what Jeff
    used. I believe this is correct data.
-   this file: `Participant_Tracking_SASK_W1-norm-filtered-redated.csv`
    has 11-day SD periods, correctly encoded, but I think are the wrong
    dates. Just an automated +11 rule on excel.

### Add treksoft IDs

Here we are adding the Treksoft (Health and VERITAS) IDs to the data.
These are `treksoft_pid` and `treksoft_uid`.

``` r
city <- select(skt, interact_id, treksoft_pid, treksoft_uid, ethica_id.x, sd_id_1, sd_firmware_1, sd_start_1, sd_end_1, sd_id_2, sd_firmware_2, sd_start_2, sd_end_2, dropout, data_disposition, test)
```

### Rename ethica

``` r
city <- rename (city, ethica_id = ethica_id.x)
```

### Flag Ethica test

Here we create a 0/1 flag for Ethica test accounts.

``` r
city <- city %>%
  mutate(test = ifelse(ethica_id %in% ethica_tests, 1, 0)) 
```

### Adding columns

Here we add the columns

-   `plg_id`: This is a ID column that will supersede the `treksoft_pid`
    and `treksoft_uid`. Note that the company Treksoft and Polygone are
    the same, they just changed names during the study period.
-   `wave`: The wave ID for the study.

``` r
city$plg_id <- ""
city$wave <- "1"
```

## Write clean csv file

``` r
write.csv(city, file= "linkage_skt_w1.csv")
```