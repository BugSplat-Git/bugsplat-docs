# Paging, Filtering, and Grouping

Many BugSplat endpoints support paging, filtering, and grouping data using the common syntax described on this page. Each page will have a different set of column names that are to be used with the filtering and grouping options.

The following URI parameters may be available:

* **sortdatafield** - the sort column's datafield.
* **sortorder** - the sort order - 'asc' or 'desc'
* **pagenum** - the current page number when the paging feature is enabled. The first page is number 0.
* **pagesize** - the number of rows in one page of data. Pagesize defaults to 100 and is capped at 10,000.
* **filterscount** - the number of filters in the query. Defaults to 0.
* **filterdatafield\#** - the filter column's datafield, or column name.
* **filtervalue\#** - the filter's value. The name for the first filter is "filtervalue0", for the second is "filtervalue1" and so on.
* **filtercondition\#** - the filter's condition. The condition can be any of these: "CONTAINS", "DOES\_NOT\_CONTAIN", "EQUAL", NOT\_EQUAL", "GREATER\_THAN", "LESS\_THAN", "NULL", "NOT\_NULL", "EMPTY", "NOT\_EMPTY".
* **filteroperator\#** - the filter's operator - "AND" or "OR". Required for filter \# greater than 0
* **filtergroupopen\#** - optional open parenthesis used to control order of operations. Any open must be paired with a close.
* **filtergroupclose\#** - a close parenthesis
* **groupscount** - the number of groups in the query. Defaults to 0.
* **group\#** - the group's name. The name for the first group is 'group0', for the second group is 'group1' and so on.

Here's an example: `https://app.bugsplat.com/api/allcrash?database=Fred&filterscount=2&filterdatafield0=appVersion&filtercondition0=EQUAL&filtervalue0=2020.10.23.0&filteroperator1=AND&filterdatafield1=crashTime&filtercondition1=GREATER_THAN&filtervalue1=2020-10-23:20:00:00MST`  
   
Let's decompose those uri parameters...  
`?database=Fred` This selects the 'Fred' database  
`&filterscount=2` We are defining two filters  
`&filterdatafield0=appVersion&filtercondition0=EQUAL  
&filtervalue0=2020.10.23.0` First filter on appVersion='2020.10.23.0'  
`&filteroperator1=AND&filterdatafield1=crashTime&filtercondition1=GREATER_THAN  
&filtervalue1=2020-10-23:20:00:00MST` Second filter AND crashTime greater than 2020-10-23:20:00:00MST

