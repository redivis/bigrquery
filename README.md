
<!-- README.md is generated from README.Rmd. Please edit that file -->

# redivis.bigrquery

The `redivis.bigrquery` package is a light fork of the awesome
[bigrquery](https://github.com/r-dbi/bigrquery) package, allowing you to
leverage its functionality to interface with tables stored on Redivis.
All authentication and billing is managed via your Redivis API
credentials.

Please note that the only supported methods are those that involve
querying tables. Interfaces involved in listing BigQuery resource,
reference BigQuery datasets, or any calls to create, modify, or delete
BigQuery resources are not supported.

### Installation

Install from GitHub:

``` r
# install.packages('devtools')
devtools::install_github("redivis/bigrquery")
```

### [bigrquery](https://github.com/r-dbi/bigrquery)

The bigrquery package makes it easy to work with data stored in [Google
BigQuery](https://developers.google.com/bigquery/) by allowing you to
query BigQuery tables and retrieve metadata about your projects,
datasets, tables, and jobs. The bigrquery package provides three levels
of abstraction on top of BigQuery:

  - The low-level API provides thin wrappers over the underlying REST
    API. All the low-level functions start with `bq_`, and mostly have
    the form `bq_noun_verb()`. This level of abstraction is most
    appropriate if you’re familiar with the REST API and you want do
    something not supported in the higher-level APIs.

  - The [DBI interface](http://www.r-dbi.org) wraps the low-level API
    and makes working with BigQuery like working with any other database
    system. This is most convenient layer if you want to execute SQL
    queries in BigQuery or upload smaller amounts (i.e. \<100 MB) of
    data.

  - The [dplyr interface](http://dbplyr.tidyverse.org/) lets you treat
    BigQuery tables as if they are in-memory data frames. This is the
    most convenient layer if you don’t want to write SQL, but instead
    want dbplyr to write it for you.

## Usage

### Low-level API

``` r
library(redivis.bigrquery)
sql <- "SELECT * FROM `stanfordphs.commuting_zone_life_expectancy_trends.v1_0` LIMIT 10"

tb <- bq_project_query(sql)
bq_table_download(tb, max_results = 10)
```

### DBI

``` r
library(DBI)

con <- dbConnect(redivis.bigrquery::bigquery())
sql <- "SELECT * FROM `stanfordphs.commuting_zone_life_expectancy_trends.v1_0` LIMIT 10"

dbGetQuery(con, sql, n = 10)
```

### dplyr

``` r
library(DBI)
library(dplyr)

con <- dbConnect(redivis.bigrquery::bigquery())

providers <- tbl(con, "ianmathews91.medicare_public_example.high_cost_in_providers_in_CA_output")

providers %>%
  select(drg_definition, provider_id, provider_state, mean_drg_cost) %>%
  head(10) %>%
  collect()
```

## Important details

### Authentication and authorization

The `REDIVIS_API_TOKEN` environment variable must be set to your Redivis
API token, and the token must have `data.data` scope. Go to your
[workspace/settings](https://redivis.com/workspace/settings) to create
an API token. For example:

    REDIVIS_API_TOKEN=your_api_token R ...
    # or
    Sys.setenv(REDIVIS_API_TOKEN = "your_api_token")

## Useful links

  - [SQL
    reference](https://developers.google.com/bigquery/query-reference)
  - [API
    reference](https://developers.google.com/bigquery/docs/reference/v2/)
