---
title: 'bcdata: An R package for searching & retrieving data from the B.C. Data Catalogue'
authors:
  - name: Andy Teucher
    orcid: 0000-0002-7840-692X
    affiliation: 1
  - name: Sam Albers
    orcid: 0000-0002-9270-7884
    affiliation: 2
  - name: Stephanie Hazlitt
    orcid: 0000-0002-3161-2304
    affiliation: 2
affiliations:
  - name: State of Environment Reporting, Ministry of Environment and Climate Change Strategy, Province of British Columbia
    index: 1
  - name: Data Science Partnerships, Ministry of Citizens' Services, Province of British Columbia
    index: 2
date: "2020-11-30"
output:
  html_document:
    keep_md: yes
bibliography: paper.bib
tags:
  - R
  - open data
  - WFS
  - British Columbia
  - Canada
---






# Statement of need

`bcdata` is an R package that enables programmatic access, using the R programming language, to data sets housed in the British Columbia (B.C.) Data Catalogue. The package connects the diverse array of mapping, modeling and data processing capabilities of the R ecosystem to the hundreds of open-licensed data sets publicly available in the B.C. Data Catalogue.

# Introduction

The British Columbia government hosts over 2000 tabular and geospatial data sets in the B.C. Data Catalogue (@bcdc).  Most provincial geospatial data is available through the B.C. Data Catalogue under an open licence, via a [Web Feature Service](https://en.wikipedia.org/wiki/Web_Feature_Service) (WFS). A Web Feature Service is a powerful and flexible service for distributing geographic features over the web, supporting both geospatial and non-spatial querying.  The `bcdata` package for the R programming language (@RCore) wraps two distinct but complimentary web APIs - one for the B.C. Data Catalogue and one for the Web Feature Service.  This allows R users to search, download and import metadata and data from the B.C. Data Catalogue, as well as efficiently query and directly read geospatial data from the Web Feature Service into their R session. The `bcdata` package implements a novel application of `dbplyr` (@dbplyr) using a Web Feature Service backend---rather than a database backend---where a locally constructed query is processed by a remote server. This allows for fast and efficient geospatial data retrieval using familiar `dplyr` syntax. Through this functionality the `bcdata` package connects British Columbia government open data holdings with the vast capabilities of R.

# Usage 

`bcdata` connects to the B.C. Data Catalogue and the Web Feature Service through a few key functions:

- `bcdc_browse()` - Open the catalogue in the default browser
- `bcdc_search()` - Search records in the catalogue
- `bcdc_search_facets()` - List catalogue facet search options
- `bcdc_get_record()` - Print a catalogue record
- `bcdc_tidy_resources()` - Get a data frame of resources for a catalogue record
- `bcdc_get_data()` - Get catalogue data
- `bcdc_query_geodata()` - Get & query catalogue geospatial data available through a [Web Feature Service](https://www2.gov.bc.ca/gov/content?id=95D78D544B244F34B89223EF069DF74E)


## Search Records & Read Metadata

`bcdc_search()` let's you search records in the B.C. Data Catalogue, returning the search results in your R session. Let's search the catalogue for records that contain the word "scholarships", restricting our search results to only two:


```r
bcdc_search('scholarships', n = 2)
```
```
List of B.C. Data Catalogue Records

Number of records: 2
Titles:
1: BC Schools - District & Provincial Scholarships (xlsx, txt)
 ID: 651b60c2-6786-488b-aa96-c4897531a884
 Name: bc-schools-district-provincial-scholarships
2: BC Arts Council Annual Arts Awards Listing 2009 - 2010 (csv, xls)
 ID: b95fa84f-2328-4adc-aebe-9a214f741fa7
 Name: bc-arts-council-annual-arts-awards-listing-2009-2010 

Access a single record by calling bcdc_get_record(ID)
      with the ID from the desired record.
```

The user can retrieve the metadata for a single catalogue record by using the record name or permanent ID with `bcdc_get_record()`. A catalogue record can have one or multiple data files---or "resources". The user can use the `bcdc_tidy_resources()` function to return a data frame listing all of the data resources and corresponding resource IDs for a catalogue record.


```r
bcdc_tidy_resources("bc-schools-district-provincial-scholarships")
```


```
# A tibble: 2 x 8
  name  id    format bcdata_available url   ext   package_id
  <chr> <chr> <chr>  <lgl>            <chr> <chr> <chr>     
1 Awar~ 4e87~ xlsx   TRUE             http~ xlsx  651b60c2-~
2 Awar~ 8a2c~ txt    TRUE             http~ txt   651b60c2-~
# ... with 1 more variable: location <chr>
```



## Get Data

Once the user has located the B.C. Data Catalogue record with the data they want, `bcdata::bcdc_get_data()` can be used to download and read the data from the record.  While any of the record name, permanent ID or the result from `bcdc_get_record()` can be used to specify the data record, `bcdata` suggests supplying the more reliable  permanent ID to the `record` argument to guard against future name changes in an English string. 

Let's try to access data for scholarships in B.C. school record:


```r
scholars <- bcdc_get_data('bc-schools-district-provincial-scholarships')
```

```
The record you are trying to access appears to have more than one resource.
 Resources: 
1) AwardsScholarshipsHist.xlsx
    format: xlsx 
    url: http://www.bced.gov.bc.ca/reporting/odefiles/AwardsScholarshipsHist.xlsx 
    resource: 4e872f59-0127-4c21-9f41-52d87af9cfab 
    code: bcdc_get_data(record = '651b60c2-6786-488b-aa96-c4897531a884', 
                        resource = '4e872f59-0127-4c21-9f41-52d87af9cfab')

2) AwardsScholarshipsHist.txt
    format: txt 
    url: http://www.bced.gov.bc.ca/reporting/odefiles/AwardsScholarshipsHist.txt 
    resource: 8a2cd8d3-003d-4b09-8b63-747365582370 
    code: bcdc_get_data(record = '651b60c2-6786-488b-aa96-c4897531a884', 
                        resource = '8a2cd8d3-003d-4b09-8b63-747365582370')

--------
Please choose one option: 

1: AwardsScholarshipsHist.xlsx
2: AwardsScholarshipsHist.txt
```

Since there are multiple data resources in the record, the user will need to specify which data resource they want. `bcdata` gives the user the option to interactively choose a resource, however for scripts it is usually better to be explicit and specify the desired data resource using the `resource` argument. We are interested, in this case, in the `.xlsx` file so we choose option 1 or:


```r
scholars <- bcdc_get_data(record = '651b60c2-6786-488b-aa96-c4897531a884', 
                          resource = '4e872f59-0127-4c21-9f41-52d87af9cfab')
head(scholars)
```


```
# A tibble: 6 x 9
  SCHOOL_YEAR_ISS~ `Sub Pop Code` `Num Prov Schol~ `Num Prov Schol~
  <chr>            <chr>          <chr>            <chr>           
1 1996/1997        ALL STUDENTS   3509             20              
2 1996/1997        FEMALE         1921             7               
3 1996/1997        MALE           1588             13              
4 1997/1998        ALL STUDENTS   3748             20              
5 1997/1998        FEMALE         2094             11              
6 1997/1998        MALE           1654             9               
# ... with 5 more variables: `Num District Scholarships` <chr>, `Data
#   Level` <chr>, `Public Or Independent` <chr>, `District
#   Number` <chr>, `District Name` <chr>
```

The `bcdc_get_data()` function can be used to download geospatial data, including that which is available from the Web Feature Service. As a simple demonstration we can download the locations of airports in British Columbia:


```r
bc_airports <- bcdc_get_data(record = 'bc-airports',
                             resource = '4d0377d9-e8a1-429b-824f-0ce8f363512c')

ggplot(bc_airports) +
  geom_sf() +
  theme_minimal()
```

![](airports-1.png)<!-- -->

## Query & Read Geospatial Data

While `bcdc_get_data()` will retrieve geospatial data, sometimes the geospatial file is very large---and slow to download---or the user may only want _some_ of the data. `bcdc_query_geodata()` allows the user to query catalogue geospatial data available from the Web Feature Service using `select` and `filter` functions (just like in [`dplyr`](https://dplyr.tidyverse.org/), @dplyr). The `bcdc::collect()` function returns the `bcdc_query_geodata()` query results as an [`sf` object](https://r-spatial.github.io/sf/) in the R session. The data is only downloaded, and loaded into R as an ‘sf’ object, once the query is complete and the user requests the final result. This is implemented using a custom `dbplyr` backend---while other `dbplyr` backends interface with various databases (e.g., SQLite, PostgreSQL), the `bcdata` backend interfaces with the B.C. Data Catalogue Web Feature Service.

To demonstrate, we will query the Northern Health Authority boundary from the [Health Authority Boundaries geospatial data](https://catalogue.data.gov.bc.ca/dataset/7bc6018f-bb4f-4e5d-845e-c529e3d1ac3b)---the whole file takes 30-60 seconds to download and we only need the one polygon, so the request can be narrowed:


```r
## Get the metadata for the Health Authority Boundaries catalogue record
ha_record <- bcdc_get_record("7bc6018f-bb4f-4e5d-845e-c529e3d1ac3b")

## Have a quick look at the geospatial columns to help with filter or select
bcdc_describe_feature(ha_record)
```

```
# A tibble: 11 x 4
   col_name            sticky remote_col_type          local_col_type
   <chr>               <lgl>  <chr>                    <chr>         
 1 id                  FALSE  xsd:string               character     
 2 HLTH_HAB_SYSID      FALSE  xsd:decimal              numeric       
 3 HLTH_AUTHORITY_CODE TRUE   xsd:string               character     
 4 HLTH_AUTHORITY_NAME TRUE   xsd:string               character     
 5 HLTH_AUTHORITY_ID   TRUE   xsd:string               character     
 6 FEATURE_CODE        TRUE   xsd:string               character     
 7 FEATURE_AREA_SQM    TRUE   xsd:decimal              numeric       
 8 FEATURE_LENGTH_M    TRUE   xsd:decimal              numeric       
 9 SHAPE               TRUE   gml:GeometryPropertyType sfc geometry  
10 OBJECTID            FALSE  xsd:decimal              numeric       
11 SE_ANNO_CAD_DATA    TRUE   xsd:hexBinary            numeric       
```

```r
## Get the Northern Health polygon from the Health Authority
## Boundaries geospatial data
my_ha <- bcdc_query_geodata(ha_record) %>%
  filter(HLTH_AUTHORITY_NAME == "Northern") %>%
  collect()

## Plot the Northern Health polygon with ggplot()
ggplot(my_ha) +
  geom_sf() +
  theme_minimal()
```

![](ha-1.png)<!-- -->

# Conclusion

The `bcdata` R package connects R users with British Columbia government's vast collection of data holdings in the the B.C. Data Catalogue through an efficient and familiar interface. This enables the use of cutting edge statistical and plotting capabilities in a modern data science context, and provides a pathway to generate important insights from open and public data.

# Acknowledgements
Author order was determined randomly using the following R code: `set.seed(42); sample(c("Teucher","Hazlitt","Albers"), 3)` because all author contributions are equal.

# References