Wave 2 Montreal Linkage
================
Daniel Fuller

# Montreal Wave 2 Linkage Files

### Read in Data

Reading in the data. Here we are reading in two files

-   `linkage_for_ingest_mtl_w2.csv` which is our main file

``` r
setwd("/Users/dlf545/Documents/ForDan_Linkages_072023")
mtl_w2 <- read_delim("linkage_for_ingest_mtl_w2.csv", delim = ",")
```

    ## New names:
    ## Rows: 1868 Columns: 12
    ## ── Column specification
    ## ──────────────────────────────────────────────────────── Delimiter: "," chr
    ## (2): sensedoc1_id, data_disposition dbl (3): ...1, interact_id, ethica_id lgl
    ## (3): sensedoc2_id, sensedoc2_wear_start_date, sensedoc2_wear_end_date dttm (4):
    ## ethica_start_date, ethica_end_date, sensedoc1_wear_start_date, sen...
    ## ℹ Use `spec()` to retrieve the full column specification for this data. ℹ
    ## Specify the column types or set `show_col_types = FALSE` to quiet this message.
    ## • `` -> `...1`

### Rename variables

``` r
mtl_w2 <- rename(mtl_w2, sd_id_1 = sensedoc1_id,
                ethica_start = ethica_start_date, 
                ethica_end = ethica_end_date,
                sd_start_1 = sensedoc1_wear_start_date,
                sd_end_1 = sensedoc1_wear_end_date,
                sd_id_2 = sensedoc2_id,
                sd_start_2 = sensedoc2_wear_start_date,
                sd_end_2 = sensedoc2_wear_end_date)
```

### Keeping variables

``` r
mtl_w2 <- select(mtl_w2, interact_id, ethica_id, sd_id_1, sd_start_1, sd_end_1, sd_id_2, sd_start_2, sd_end_2, data_disposition)
```

### Flag test accounts

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

``` r
mtl_w2 <- mtl_w2 %>%
  mutate(test = ifelse(ethica_id %in% ethica_tests, 1, 0)) 
```

### Adding columns

``` r
mtl_w2$sd_firmware_1 <- NA
mtl_w2$sd_firmware_2 <- NA
mtl_w2$dropout <- NA
mtl_w2$treksoft_pid <- NA
mtl_w2$treksoft_uid <- NA
mtl_w2$plg_id <- NA
mtl_w2$wave <- 2
```

### Write clean file

``` r
write.csv(mtl_w2, file= "linkage_mtl_w2.csv")
```