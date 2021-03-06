# bcdata (development version)
### IMPROVEMENTS
* Added `bcdc_list_groups` and `bcdc_list_group_records` to provide the ability to query on the group endpoint of the catalogue API. #234

# bcdata 0.2.1

### BUG FIXES
* Remove link for pipe documentation for simplicity.
* Fixed bug where using many `as.` functions (e.g., `as.Date()`, `as.character()`, `as.numeric()`) in a filter statement would fail. (#218, #219)

### MAINTENANCE
* Updated internal SQL translation to use `DBI` S4 generics (`DBI::dbQuoteIdentifier()` is now used instead of 
  `dbplyr::sql_escape_ident()` and `DBI::dbQuoteString()` instead of `dbplyr::sql_escape_string()`), to comply 
  with upcoming `dbplyr` 2.0 release (#225, #225; https://github.com/tidyverse/dbplyr/issues/385)
* Wrapped all examples that call web resources in `try()` to avoid spurious check failures (#229).

# bcdata 0.2.0

### BREAKING CHANGES
* Rename `selectable` column from `bcdc_describe_feature` to `sticky` and modify corresponding docs and tests (#180).

### IMPROVEMENTS
* Add explore-silviculture-data-using-bcdata vignette/article. h/t @hgriesbauer 
* Add `head` and `tail` methods for `bcdc.promise` objects. Thanks to @hgriesbauer for the suggestion! (#182, #186)
* Provide `as_tibble` as an alias for `collect` in line with `dbplyr` behaviour (#166)
* Geometry predicates can now take a `bbox` object as well as an `sf*` object (#176)
* When reading in excel files, `bcdc_get_data` now outputs a messages indicating the presence and names of any sheets (#190)
* `bcdc_get_data()` & `bcdc_query_geodata()` will now work with full B.C. data catalogue url including resource (#125, #196)
* `bcdc_sf` objects now have an `time_downloaded` attribute
* Authorized B.C. Data Catalogue editors can now authenticate with the catalogue by setting 
a `BCDC_KEY` environment variable with their catalogue API token (https://github.com/bcgov/bcdata#bcdc-authentication; #208).

### BUG FIXES
* Fix `select`, `filter` and `mutate` roxygen so that bcdata specific documentation to these methods is available
* Add tests for attributes

# bcdata 0.1.2

### IMPROVEMENTS
* Add `bcdc_tidy_resources` for retrieving a data frame containing the metadata for all resources from a single B.C. Data Catalogue record (PR#149, #147)
* Add a more decorative record print method  (#73)
* More reliable detection of layer name for a wfs call in `bcdc_query_geodata()` (#129, #138, #139)
* Add `mutate` method for bcdc_promise that only fails and suggest an alternative approach. (PR#134)
* Add back in querying vignette
* Using `tidyselect` so that `select.bcdc_promise` behaviour is typical of `dplyr::select` ($140, #138)
* Using GitHub actions for CI. 

### MINOR BREAKING CHANGES
* Remove `BEYOND()` and `RELATE()` geometry predicates as they are currently not fully supported by geoserver
                                                        
### BUG FIXES
* Now precompiling vignettes so that queries are submitted locally and no actually requests are made from CRAN (#151)
* Fix `NOTE: Namespace in Imports field not imported from: ‘methods’` error on CRAN (#145)
* Fixed a bug where functions nested inside geometry predicates were not evaluated (#146, #154)
* Fixed a bug where `DWITHIN` wasn't working because `units` needed to be unquoted (#154)
* Fixed a bug where `BBOX()` used in a `filter()` statement combined with `bcdc_query_geodata()` did not work (#135, #137, #131)
* Fixed a bug where layer names with a number in them would not work in `bcdc_query_geodata()` (#126, #127)


# bcdata 0.1.1

* Expand and standardize checking w[ms]f features to make package more resistant to slight warehouse API changes. 
* Data retrieval functions now work with BCGW name (#106)
* Add CITATION file (#104)
* Increased test coverage (#112)
* Skipping all tests on CRAN that require a web connection
* Better and more informative error message when experiencing http failures occur (#121)
* Added print methods for `show_query`
* Change examples to donttest
* Added verbose argument to `bcdc_get_record` to enable suppressing console writing

# bcdata 0.1.0

* Initial Release
