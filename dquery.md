# :tada: Introduction

http://d-portal.org/dquery/

dQuery lets you query **the complete IATI data, including non-standard attributes and extensions** directly into the live d-portal database, in a modern browser.

```diff
+ Documentation is ongoing so please bear with us.
```
dQuery works well if you are familiar with the IATI Standard activity elements and querying in SQL using JSONB data types in PostgreSQL. However, it shouldn't be too hard to pick up once you've done a few recipes.

### Contents
- [:pushpin: **Getting started**](#pushpin-getting-started)
  - [Data formats](#data-formats)
  - [Commands](#commands)
    - [Select](#select)
    - [From](#from)
    - [As](#as)
    - [Where](#where)
    - [And](#and)
    - [Group by](#group-by)
    - [Order by](#order-by)
    - [Limit](#limit)
    - [Offset](#offest)
    - [Join](#join)
- [:mag: **Basic Queries**](#mag-basic-queries)
  - [Syntax](#syntax)
  - [Advanced Queries](#advanced-queries)
- [:doughnut: **Database Dump**](#doughnut-database-dump)
  - [Server](#server)
- [:sparkles: **Recipes**](#sparkles-recipes)
  - [Display count of certain element in org file](#display-count-of-certain-element-in-org-file)
  - [Look for similar `iati-identifier` using a wildcard `%`](#look-for-similar-iati-identifier-using-a-wildcard-)
  - [Filtering on custom namespace elements](#filtering-on-custom-namespace-elements)
  - [Display iati-organisation id with curated elements within `total-budget`](#display-iati-organisation-id-with-curated-elements-within-total-budget)
  - [Group by publishers that use a particular `@ref`](#group-by-publishers-that-use-a-particular-ref)
  - [Display all unique `reporting-org/@ref` published in a dataset](#display-all-unique-reporting-orgref-published-in-a-dataset)
  - [Display first `/narrative` array in multiple roots, count and grouped for a particular `@ref`](#display-first-narrative-array-in-multiple-roots-count-and-grouped-for-a-particular-ref)
  - [Display all publishers listing (GIZ) in `participating-org/narrative`](#display-all-publishers-listing-giz-in-participating-orgnarrative)
  - [Display all publishers with `conditions@attached` as YES](#display-all-publishers-with-conditionsattached-as-yes)
  - [Display narratives and count, grouped by publishers with `condition@type`](#display-narratives-and-count-grouped-by-publishers-with-conditiontype)
  - [Display narratives grouped by publishers with `condition@type` 1](#display-narratives-grouped-by-publishers-with-conditiontype-1)
  - [Display an element attribute, grouped in descending order](#display-an-element-attribute-grouped-in-descending-order)
  - [Display full activity data within an element for multiple identifiers](#display-full-activity-data-within-an-element-for-multiple-identifiers)
  - [Display identifiers sorted by the second column (narrative) in descending order](#display-identifiers-sorted-by-the-second-column-narrative-in-descending-order)
  - [Display number of items with full activity data for an element and vocab](#display-number-of-items-with-full-activity-data-for-an-element-and-vocab)
  - [Subquery to get full activity data](#subquery-to-get-full-activity-data)
  - [Display full activity data with attribute of certain value](#display-full-activity-data-with-attribute-of-certain-value)
  - [Display unique activity identifiers with attribute of certain value](#display-unique-activity-identifiers-with-attribute-of-certain-value)
  - [Get a table of most used values sorted by count](#get-a-table-of-most-used-values-sorted-by-count)
  - [Display all activities for a country_code within COVID-19](#display-all-activities-for-a-country_code-within-covid-19)


# :pushpin: Getting started

The current interface is sparse and consists of two panels; the editor on the left and the console on the right.  
There are four buttons above these panels which perform various things.

**Examples**  
Some example recipes.

**Run Query**  
View results in the browser.  
*Please limit your results! If queries are complex, this can slow down the browser.*  

**Browse Activities**  
View results in d-portal.

**Download XSON**  
Download data in various formats without viewing results in the browser.

You can drag the partition to increase or decrease the space of either panel.  

<p align="right"><a href="#tada-introduction">To Top</a></p>
## Data formats

Downloads of data is available as csv, json, xml and html.

The html option will only work at the top level of an activity or an organisation file; ie. when `select *`, the root needs to be at either `/iati-activities/iati-activity` or `/iati-organisations/iati-organisation`.

The html option is a Print-friendly version of SAVi (Simple Activity Viewer).

Replace `/#` in the url with `?form=csv&sql=` to get the query link in different formats.  

[To Top](#tada-introduction)
## Commands

You can more or less figure out what a query does by reading it like a sentence.  
And just like a sentence, you can write out a whole query in a single line as linebreaks in these examples are mostly for legibility purposes.

The following lists the most common SQL clauses and operators you can use to create queries.

| Commands | What it does |
| --- | ----------- |
| select | Picks field(s) that contain data of interest |
| from | Picks table(s) that contain data of interest |
| where | Adds conditions to the query |
| and | Additional conditions |
| or | Additional conditions |
| not | Additional conditions |
| like | Use with `%` or `_` |
| ilike | Use with `%` or `_` |
| as | Gives a table, or a column a temporary name |
| in | Specifies multiple values |
| group by | Aggregates values across row |
| order by | Specifies sorting conditions with option for `asc` or `desc` |
| limit | Limits the number of returned results |
| offset | Skips a given number of results |
| join | Get data from 2 or more tables |

[To Top](#tada-introduction)
## Select

Select specifies the kind of data we want to get.

Multiple column names are separated by a comma `,`.  

If column names are not provided, this defaults to an asterisk `*` which means everything. The valid values here depend on which tables you are querying.

The following lists the most common, but not all commands using Select.

| Commands | What it does |
| --- | ----------- |
| * | Returns full acitivity information, in their original order |
| count(*) | Returns a number of items |
| distinct | Returns a list of unique results |
| aid | Returns a list of identifiers |
| pid | Returns a list of publishers |
| as | Gives a table, or a column a temporary name |

#### Examples
```sql
select distinct aid 
```
```sql
select * 
```

## As

Sometimes we want column names that are more human readable when they are displayed in a CSV or JSON output.  
We can give them temporary aliases that exists only for the duration of that query.

We first specify which column we want, followed by As and the temporary column name.  
Double quotation marks `""` are required if the alias name contains spaces.

#### Examples
```sql
select pid as "Organisation Identifier"
```
```sql
select pid as org_id
```

## From

Tables are where the data is stored and From specifies which tables to look at to get them.

For most queries, we are only looking at the `xson` table.  
The `xson` table includes the entire IATI activity elements, as well as non-standard attributes and extensions.

Multiple table names are separated by a comma `,`.  

For a full list of available tables in the database, please refer [here](https://github.com/devinit/D-Portal/blob/master/dstore/js/dstore_db.js#L48).  
For a full list of IATI acitivity elements, please refer [here](https://iatistandard.org/en/iati-standard/203/activity-standard/).

#### Examples
```sql
from xson
```
```sql
from location
```

## Where

At its most basic, Where is used to get data that fulfills a specified condition.  
For most queries, we do this by specifying the xpath of an element in `root`.  

Where can be combined with operators to get results based on more than one condition.  
Multiple column names are separated by a comma `,`.

| Operator | What it does |
| --- | ----------- |
| and | Returns data if all the conditions separated by And are TRUE |
| or | Returns data if all the conditions separated by Or are TRUE |
| not | Returns data if the condition(s) is NOT TRUE |
| in | Specifies multiple values, short for multiple Or conditions |
| between | Selects a range, start and end values included (values can be numbers, text, or dates) |
| like | Search for a specified pattern, case sensitive |
| ilike | Search for a specified pattern, case insensitive |


For Like and iLike, there are two wildcard options:

- The percent sign `%` for multiple characters.  
- The underscore sign `_` for a single character.


| Use | What it does |
| --- | ----------- |
| like `a%` | Finds data that starts with "a" |
| like `%a` | Finds data that ends with "a" |
| like `%aa%` | Finds data that has "aa" within it |
| like `_a%` | Finds data that has "a" as the second character |
| like `a_%` | Finds data that starts with "a" and is at least 2 characters in length |
| like `a__%` | Finds data that starts with "a" and is at least 3 characters in length |
| like `_a_` | Finds data that has 3 characters that has "a" as the second character |
| like `a%s` | Finds data that starts with "a" and ends with "s" |



#### Examples
```sql
where root='/iati-activities/iati-activity' 
```
```sql
where root='/iati-activities/iati-activity/other-identifier' and xson->>'@type' = 'B1'
```
```sql
where root in ('/iati-activities/iati-activity/participating-org', '/iati-activities/iati-activity/transaction/')
```

## And

Adds additional limits to the current query to narrow the results.  

You can use the following operators to enhance a query.

| Operator | Description |
| --- | ----------- |
| = | Equal |
| > | Greater than |
| < | Less than |
| >= | Greater than or equal |
| <= | Less than or equal |
| != | Not equal |
| <> | Not equal |
| is null | Find missing values |
| is not null | Ignore missing values |

#### Examples
```sql
and xson->>'@type' = 'B1'
```
```sql
and xson->>'/total-budget' IS NOT NULL
```

## Group by

When counting or adding up values, it is often neccesary to group columns with the same values.  
Column numbers can be used instead of column names, as well as a combination of numbers and names.

Multiple column names are separated by a comma `,`.

#### Examples
```sql
group by 1
```
```sql
group by xson->>'@role', xson->>'@type'
```

## Order by

This sorts the resulting data in ascending or descending order.  
Column numbers can be used instead of column names, as well as a combination of numbers and names.

Multiple column names are separated by a comma `,`.

The default order is in ascending order (low to high).

| Operator | Description |
| --- | ----------- |
| asc | Sorts in ascending order (low to high) |
| desc | Sorts in descending order (high to low) |

#### Examples
```sql
order by pid desc
```
```sql
order by 1 asc, xson->>'@role' desc
```

## Limit

We use Limit to specify the number of results we want in the query.  
This is especially useful if we want to test a query that might be intensive or to quickly see if it works.

This will return the first 10 results from a table.

#### Examples
```sql
limit 10;
```

## Offset

We use Offset for pagination, especially useful on large tables when we need to see the next set of results.  
When we combine it with Limit, this allows us to page through the results whilst still returning a limited number of results.

#### Examples
```sql
offset 5;
```

The following query will return only 5 results, starting on the the 11th result.
```sql
limit 5 offset 10;
```

## Join

We use Join to combine rows from two or more tables, based on a related column between them.

#### Examples
```sql
join country on act.aid = country.aid
```

We can also refer to the same table as two different table aliases, thus the `xson` table below is joined with itself.  
An example of such use is when you want to return results that includes data within **and** outside a particular element.

This can happen with IATI data due to the hierarchical nature of XML.

However, it is highly recommended that we always aim to keep queries straightforward and simple to reduce load times!

```sql
from xson as "Table 1", xson as "Table 2"
```


# :mag: Basic queries

We often refer to tables, rows and columns when querying. This is because the database contains many tables.

Each table is identified by a name, for example `xson`.  
Each table has rows and columns, and those have names too, for example `iati-identifier`.

For a full list of available tables in the database, please refer [here](https://github.com/devinit/D-Portal/blob/master/dstore/js/dstore_db.js#L48).  

When we do a query, we write SQL statements.  

SQL keywords are **not** case sensitive, so `select` is the same as `SELECT`.  
We always end a statement with a semicolon `;` so you can write many statements in a query.

However, although we allow multiple SQL statements to be executed at a time, only the latest one will display.

**We always limit our queries because the database is huge!**  
Returning large results can impact your browser performance, and ultimately the server and the d-portal website.


## Syntax

The following is a SQL statement.  
For this purpose, all SQL keywords are in upper-case.

Hopefully, you are able to figure out the different clauses and fields that make up parts of the statement.

```sql
SELECT column_name(s), column_name AS alias_name
FROM table_name(s)
WHERE condition
AND condition
GROUP BY column_name(s)
ORDER BY column_name(s)
LIMIT number;
```

All possible column names can be found by querying a table and looking at the returned JSON.  
By specifying the xpath of an element in `root`, you can filter results to within that element without displaying the entire xml.

#### Examples

This gets you all the data within the `budget` element table from a random activity.
```sql
select *
from xson where root='/iati-activities/iati-activity/budget'
limit 1;
```

Result

```sql
{
    result: [
        {
            aid: "SE-0-SE-6-10451A0101-MLI-72012",
            pid: "SE-0",
            root: "/iati-activities/iati-activity/budget",
            xson: {
                /value: 189381.7616514557,
                /value@currency: "USD",
                /value@value-date: "2019-01-01",
                /period-end@iso-date: "2019-12-28",
                /period-start@iso-date: "2019-01-01"
            }
        }
    ],
    duration: 0.014
}
```

If we change the `select` field from `*` to `aid`, the result will change.  
This is because the query has specified that we only want a list of identifiers.
```sql
select aid
from xson where root='/iati-activities/iati-activity/budget'
limit 1;
```

Result

```sql
{
    result: [
        {
            aid: "XM-DAC-903-SPI-10117"
        }
    ],
    duration: 0.035
}
```

This gets you all the data within the `total-expenditure` element table from a random organisation file.
```sql
select *
from xson where root='/iati-organisations/iati-organisation/total-expenditure'
limit 1;
```

Result

```
{
    result: [
        {
            aid: null,
            pid: "XM-DAC-41123",
            root: "/iati-organisations/iati-organisation/total-expenditure",
            xson: {
                /value: 1060488055.37,
                /value@currency: "USD",
                /value@value-date: "2013-01-01",
                /period-end@iso-date: "2013-12-31",
                /period-start@iso-date: "2013-01-01"
            }
        }
    ],
    duration: 0.009
}
```

If we tweak the query like so, the result will change.  
Here we only want the publisher identifier `pid` and `value` element which we have renamed as `Total Expenditure in USD`.
```sql
select pid, xson->>'/value' as "Total Expenditure in USD"
from xson where root='/iati-organisations/iati-organisation/total-expenditure'
limit 1;
```

Result

```sql
{
    result: [
        {
            pid: "XM-DAC-41146",
            Total Expenditure in USD: "354113927"
        }
    ],
    duration: 0.009
}
```

## Advanced Queries

For more information about the SQL language, the full list of functions and futher documentation, please refer to the following official pages.

1. [PostgreSQL Queries](https://www.postgresql.org/docs/12/queries.html)

2. [PostgreSQL Functions and Operators](https://www.postgresql.org/docs/12/functions.html)

[To Top](#tada-introduction)

# :doughnut: Database Dump

*Spin up a server, import and start querying.*

**The entire d-portal database is about 3GB in size.**

The following dumps can be imported locally and queried using the same SQL code that is used on the web interface so you can run large queries on it without clogging up d-portal.

1. PostgreSQL dump *(Updated nightly)*  
    [http://d-portal.org/db/dstore.sql.gz](http://d-portal.org/db/dstore.sql.gz)

2. `pg_dump` custom format so `pg_restore` can be used with its various options *(Updated nightly)*  
    [http://d-portal.org/db/dstore.pg](http://d-portal.org/db/dstore.pg)

3. A zip of all the raw cached xml *(Updated nightly)*  
    [http://d-portal.org/db/cache.xml.zip](http://d-portal.org/db/cache.xml.zip)


[To Top](#tada-introduction)

## Server

For us, a full restore of the database takes about 7 hours, or 2 hours if you use multithreading with `pg_restore`.

```
--jobs=(number of cpu cores)
```

Almost all of the time is spent rebuilding indexes so it's not the data itself that takes the time.  
We create a lot of indexes because, as a public facing site, we need the database queries to run as fast as possible.

For comparison, this is our test server specification that imported the data in 2 hours.

| CPU   |      RAM      |  Hard Drive(s) |
|----------|:-------------:|------|
| Intel Atom C2750 - 2.4 GHz - 8 core(s) |  16GB - DDR3 | 1x 256GB (SSD SATA) |

What we found is having a solid state drive really speeds up the import process more than anything else.

[To Top](#tada-introduction)

# :sparkles: Recipes

The following recipes are some examples of what you can query using this tool.

Some of these are requests for when more complex data is needed outside the scope of the d-portal platform.  
Where applicable, results are limited to 1 in the examples below.

Add comments like so
```sql
-- This is a comment

/*
This is a  
multi line comment
*/
```

### Display count of certain element in org file
```sql
select

count(*)

from xson where root='/iati-organisations/iati-organisation'
and xson->>'/total-budget' IS NOT NULL

```

Result

```sql
{
    result: [
        {
            count: "437"
        }
    ],
    duration: 0.053
}
```

<p align="right">[To Top](#tada-introduction)</p>

### Look for similar `iati-identifier` using a wildcard `%`
Raised https://github.com/codeforIATI/iati-ideas/issues/37

The wildcard `%` can be placed before, after or before and after a search term to get the following results.

`%<searchterm>` looks for identifiers that end with the search term.  
`<searchterm>%` looks for identifiers that start with the search term.  
`%<searchterm>%` looks for identifiers that has the search term within it.
	
```sql
select

distinct aid

from xson where root='/iati-activities/iati-activity' 
and xson->>'/iati-identifier' like '%1022474%'

limit 1;
```

Result

```sql
{
    result: [
        {
            aid: "1022474"
        },
        {
            aid: "DE-1-201022474"
        }
    ],
    duration: 18.484
}
```

### Filtering on custom namespace elements
Raised https://github.com/devinit/D-Portal/issues/568

```sql
SELECT DISTINCT aid FROM xson
WHERE
	root='/iati-activities/iati-activity/transaction'
AND
	xson->>'/usg:treasury-account/usg:regular-account@code' = '72'
```

Result

```sql
{
    result: [
        {
	        {
            aid: "US-GOV-9-ZM-F19AP00767"
        }
    ],
    duration: 10.615
}
```

### Display iati-organisation id with curated elements within `total-budget`
```sql
select

pid,

xson ->> '/value' as budgetvalue,
xson ->> '/value@currency' as currency,
xson ->> '/value@value-date' as valuedate,
xson ->> '/period-end@iso-date' as enddate,
xson ->> '/period-start@iso-date' as startdate,
xson ->> '@status' as status

from xson where root='/iati-organisations/iati-organisation/total-budget'

limit 1;

```

Result

```sql
{
    result: [
        {
            pid: "DK-CVR-12006004",
            budgetvalue: "8800000",
            currency: "DKK",
            valuedate: "2019-01-01",
            enddate: "2019-12-31",
            startdate: "2019-01-01",
            status: "2"
        },
	    ],
    duration: 0.031
}
```

### Group by publishers that use a particular `@ref`

```sql
select

pid as org, count(*)

from xson where xson->>'@ref'='XM-OCHA-CBPF-NGA75'
group by pid

order by 2 desc

limit 1;
```

Result

```sql
{
    result: [
        {
            org: "XM-OCHA-CBPF",
            count: "308"
        }
    ],
    duration: 29.328
}
```

### Display all unique `reporting-org/@ref` published in a dataset
Raised https://github.com/codeforIATI/iati-data-bugtracker/issues/7

Here we have selected the `reporting_ref` column from the `act` table and gave the column a temporary name (alias) for legibility.  
We are looking at a particular dataset `slovakaid-69_1_ac` in the `slug` column.

Finally, we have grouped the results by the temporary name (alias) `reporting_org` and sorted it alphabetically, in the default ascending order.

```sql
select act.reporting_ref as reporting_org
from act where act.slug='slovakaid-69_1_ac'
group by reporting_org
order by reporting_org
limit 1;
```

Result

```sql
{
    result: [
        {
            reporting_org: "XM-DAC-69-0"
        }
    ],
    duration: 0.015
}
```

### Display first `/narrative` array in multiple roots, count and grouped for a particular `@ref`
Raised https://github.com/devinit/D-Portal/issues/602

```sql
select

xson->>'@ref' as reference, xson->'/narrative'->0->>'' as narrative, pid as publisher, count(*)

from xson where root in ('/iati-activities/iati-activity/participating-org',
'/iati-activities/iati-activity/transaction/provider-org',
'/iati-activities/iati-activity/transaction/receiver-org')
and xson->>'@ref'='XM-OCHA-CBPF-NGA75'
group by 1, 2, pid

order by 4 desc

limit 1;
```

Result

```sql
{
    result: [
        {
            reference: "XM-OCHA-CBPF-NGA75",
            narrative: "Nigeria Humanitarian Fund",
            publisher: "XM-OCHA-CBPF",
            count: "308"
        }
    ],
    duration: 7.945
}
```

### Display all publishers listing (GIZ) in `participating-org/narrative` 
Raised https://github.com/devinit/D-Portal/issues/613

Here we use `LIKE` and `%` for wildcard SQL queries.

```sql
select

xson->>'@role' as role_, 
xson->>'@type' as type_, 
xson->'/narrative'->0->>'' as narrative, 
pid as publisher, 
count(*)

from xson where root='/iati-activities/iati-activity/participating-org' 
and xson->'/narrative'->0->>'' like '%(GIZ)%'

group by xson->>'@role', 
xson->>'@type', 
pid, 
narrative

order by 5 desc

limit 1;
```

Result

```sql
{
    result: [
        {
            role_: "3",
            type_: null,
            narrative: "Deutsche Gesellschaft für Internationale Zusammenarbeit (GIZ) GmbH",
            publisher: "DE-1",
            count: "2911"
        },
    ],
    duration: 2.757
}
```

### Display all publishers with `conditions@attached` as YES
```sql
select

pid as org_id, count(*)

from xson where root='/iati-activities/iati-activity' and xson->>'/conditions@attached' = '1'
group by pid

order by 2 desc

limit 1;
```

Result

```sql
{
    result: [
        {
            org_id: "XM-DAC-41122",
            count: "14359"
        },
    ],
    duration: 0.493
}
```

### Display narratives and count, grouped by publishers with `condition@type`
```sql
select

xson->>'/narrative' as narrative, xson->>'@type' as condition_type, pid as org_id, count(*)

from xson where root='/iati-activities/iati-activity/conditions/condition'
group by xson->>'/narrative', pid, condition_type

order by 4 desc

limit 1;
```

Result

```sql
{
    result: [
        {
            narrative: "[{\"\": \"Should contribute directly to results for children as outlined in the UNDAF, national results and/or UNICEF Strategic Plan and within the scope defined in the CPD, CPAP, OMP or other agreed upon programme documents\", \"@xml:lang\": \"EN\"}]",
            condition_type: "1",
            org_id: "XM-DAC-41122",
            count: "14359"
        },
    ],
    duration: 0.55
}
```

### Display narratives grouped by publishers with `condition@type` 1
```sql
select

xson->>'/narrative' as narrative, pid as org, count(*)

from xson where root='/iati-activities/iati-activity/conditions/condition' and xson->>'@type' = '1'
group by xson->>'/narrative', pid

order by 3 desc

limit 1;
```

Result

```sql
{
    result: [
        {
            narrative: "[{\"\": \"Should contribute directly to results for children as outlined in the UNDAF, national results and/or UNICEF Strategic Plan and within the scope defined in the CPD, CPAP, OMP or other agreed upon programme documents\", \"@xml:lang\": \"EN\"}]",
            org: "XM-DAC-41122",
            count: "14359"
        },
    ],
    duration: 1.696
}
```

### Display an element attribute, grouped in descending order
Use `as` to name the columns.  
Order follows `group by`, if stated.

```sql
select

xson->>'@ref' as old_id, pid as new_id, count(*)

from xson where root='/iati-activities/iati-activity/other-identifier' and xson->>'@type' = 'B1'
group by xson->>'@ref', pid

order by 3 desc

limit 1;

```

Result

```sql
{
    result: [
        {
            old_id: "US-GOV",
            new_id: "US-USAGOV",
            count: "279337"
        },
    ],
    duration: 2.415
}
```

### Display full activity data within an element for multiple identifiers
Raised https://github.com/devinit/D-Portal/issues/620

This works for `/budget` in `root` because this array occurs only once.

```sql
select *

from xson where root='/iati-activities/iati-activity/budget'
and aid in ('BE-10-3011287','BE-10-3011636','BE-10-3012116',
'BE-10-3013682','BE-10-3013700','BE-10-3013816','BE-10-3014022',
'BE-10-3014056','BE-10-3014752','BE-10-3014910','BE-10-3015098',
'BE-10-3016473','BE-10-3016624','BE-10-3017143','BE-10-3017144',
'BE-10-3017145','XM-DAC-2-10-1022','XM-DAC-2-10-1023',
'XM-DAC-2-10-1027','XM-DAC-2-10-1196','XM-DAC-2-10-1481',
'XM-DAC-2-10-1826','XM-DAC-2-10-2084','XM-DAC-2-10-2093',
'XM-DAC-2-10-2150','XM-DAC-2-10-2643','XM-DAC-2-10-2654',
'XM-DAC-2-10-2660','XM-DAC-2-10-2661','XM-DAC-2-10-2666',
'XM-DAC-2-10-3017783','XM-DAC-2-10-3017854','XM-DAC-2-10-3018038')

limit 1;
```

Result

```sql
{
    result: [
        {
            aid: "BE-10-3013816",
            pid: "XM-DAC-2-10",
            root: "/iati-activities/iati-activity/budget",
            xson: {
                @type: "1",
                /value: 804000,
                /value@currency: "EUR",
                /value@value-date: "2021-01-01",
                /period-end@iso-date: "2021-12-31",
                /period-start@iso-date: "2021-01-01"
            }
        }
    ],
    duration: 0.007
}
```

### Display identifiers sorted by the second column (narrative) in descending order
You can order by `aid` and `desc` as well.

```sql
select

aid , JSONB_ARRAY_LENGTH(xson->'/narrative')

from xson where JSONB_ARRAY_LENGTH(xson->'/narrative') is not null

order by 2 desc
limit 1;
```

Result

```sql
{
    result: [
        {
            aid: "SE-ON-802005-9823-LA-500399",
            jsonb_array_length: 63
        }
    ],
    duration: 13.104
}
```


### Display number of items with full activity data for an element and vocab
```sql
select

JSONB_ARRAY_LENGTH(xson->'/narrative') , *

from xson where root='/iati-activities/iati-activity/sector' 
and xson->>'@vocabulary' = '99' 
and JSONB_ARRAY_LENGTH(xson->'/narrative') > 1

order by 1 desc

limit 1;
```

Result

```sql
{
    result: [
        {
            jsonb_array_length: 2,
            aid: "XM-DAC-301-2-107720-001",
            pid: "XM-DAC-301-2",
            root: "/iati-activities/iati-activity/sector",
            xson: {
                @code: "4",
                /narrative: [
                    {
                        : "Stimulating sustainable economic growth",
                        @xml:lang: "EN"
                    },
                    {
                        : "Croissance économique durable",
                        @xml:lang: "FR"
                    }
                ],
                @percentage: 100,
                @vocabulary: "99"
            }
        }
    ],
    duration: 3.428
}
```

### Subquery to get full activity data
Raised https://github.com/devinit/D-Portal/issues/562

```sql
SELECT * FROM xson WHERE root = '/iati-activities/iati-activity' AND  aid IN (
SELECT DISTINCT aid
FROM xson WHERE
    root = '/iati-activities/iati-activity/humanitarian-scope'
AND
    xson->>'@code' = 'EP-2020-000012-001'
)
limit 1;
```

Result

```sql
{
    result: [
        {
            aid: "44000-P150481",
            pid: "44000",
            root: "/iati-activities/iati-activity",
            xson: {
                /budget: [144 items],
                /result: [24 items],
                @xml:lang: "EN",
                @hierarchy: 1,
                /description: [1 item],
                /contact-info: [1 item],
                @dstore:index: "2",
                @xmlns:dstore: http://d-portal.org/xmlns/dstore,
                /activity-date: [3 items],
                /document-link: [21 items],
                /title@xml:lang: "EN",
                @dstore:dataset: "worldbank-ps",
                /iati-identifier: "44000-P150481",
                /title/narrative: [1 item],
                /default-aid-type: [1 item],
                @default-currency: "USD",
                /participating-org: [4 items],
                /recipient-country: [1 item],
                /reporting-org@ref: "44000",
                /humanitarian-scope: [2 items],
                /reporting-org@type: "40",
                /activity-status@code: "2",
                @last-updated-datetime: "2020-07-23T11:53:45",
                @xmlns:iati-activities: http://d-portal.org/xmlns/iati-activities,
                /collaboration-type@code: "2",
                /reporting-org/narrative: [1 item],
                @iati-activities:version: 2.03,
                /capital-spend@percentage: 100,
                /default-tied-status@code: "5",
                @iati-activities:generated-datetime: "2020-08-05T04:54:41"
            }
        }
    ],
    duration: 0.029
}
```

### Display full activity data with attribute of certain value

```sql
select *
from xson where root='/iati-activities/iati-activity/result/indicator'
and xson->>'@ascending'='0'
limit 1;
```

Result

```sql
{
    result: [
        {
            aid: "XM-DAC-41114-PROJECT-00038671",
            pid: "XM-DAC-41114",
            root: "/iati-activities/iati-activity/result/indicator",
            xson: {
                /period: [2 items],
                @measure: "1",
                /baseline: [1 item],
                @ascending: 0,
                /title/narrative: [1 item],
                /description/narrative: [1 item]
            }
        }
    ],
    duration: 0.013
}
```

### Display unique activity identifiers with attribute of certain value
Raised https://github.com/devinit/D-Portal/issues/562

This element may occur any number of times so the xpath has to be the root.  
Any element occurring multiple times is turned into an array.

```sql
SELECT DISTINCT aid
FROM xson WHERE
    root = '/iati-activities/iati-activity/humanitarian-scope'
AND
    xson->>'@code' = 'EP-2020-000012-001'
limit 1;
```

Result

```sql
{
    result: [
        {
            aid: "44000-P150481"
        }
    ],
    duration: 0.017
}
```

### Get a table of most used values sorted by count
Raised https://github.com/devinit/D-Portal/issues/562

```sql
SELECT xson->>'/humanitarian-scope@code' AS code , count(*)

FROM xson WHERE
    root='/iati-activities/iati-activity/humanitarian-scope'
AND
    xson->>'@code' IS NOT NULL

GROUP BY 1 ORDER BY 2 DESC

LIMIT 1;
```

Result

```sql
{
    result: [
        {
            code: null,
            count: "28222"
        }
    ],
    duration: 0.131
}
```

## Display all activities for a country_code within COVID-19
Raised https://github.com/devinit/D-Portal/issues/589

```sql
SELECT * FROM act JOIN country on act.aid = country.aid  WHERE country_code ='AO' AND act.aid in (
SELECT DISTINCT aid FROM xson WHERE
(
	root='/iati-activities/iati-activity/humanitarian-scope' AND
	xson->>'@type'='1' AND
	xson->>'@vocabulary'='1-2' AND
	xson->>'@code'='EP-2020-000012-001'
)OR(
	root='/iati-activities/iati-activity/humanitarian-scope' AND
	xson->>'@type'='2' AND
	xson->>'@vocabulary'='2-1' AND
	xson->>'@code'='HCOVD20'
)OR(
	root='/iati-activities/iati-activity/tag' AND
	xson->>'@vocabulary'='99' AND
	xson->>'@vocabulary-uri' IS NULL AND
	UPPER(xson->>'@code')='COVID-19'
)OR(
	root='/iati-activities/iati-activity/title/narrative' AND
	to_tsvector('simple', xson->>'') @@ to_tsquery('simple','COVID-19')
)OR(
	root='/iati-activities/iati-activity/description/narrative' AND
	to_tsvector('simple', xson->>'') @@ to_tsquery('simple','COVID-19')
)OR(
	root='/iati-activities/iati-activity/transaction/description/narrative' AND
	to_tsvector('simple', xson->>'') @@ to_tsquery('simple','COVID-19')
)
)
limit 1;
```

Result

```sql
{
    result: [
        {
            aid: "CZ-ICO-25755277-AGOZ000116",
            reporting: "People in Need",
            reporting_ref: "CZ-ICO-25755277",
            funder_ref: "CZ-ICO-25755277",
            title: "EC Covid Emergency Response 2020-22",
            slug: "pin-dataset-20201231",
            status_code: 2,
            day_start: 18566,
            day_end: 19111,
            day_length: 545,
            description: "Gender in the Time of COVID-19",
            commitment: 312813.22,
            spend: 6079.097,
            commitment_eur: 257130,
            spend_eur: 4996.97,
            commitment_gbp: 233297.97,
            spend_gbp: 4533.827,
            commitment_cad: 400701.56,
            spend_cad: 7787.087,
            flags: 0,
            country_code: "AO",
            country_percent: 100
        }
    ],
    duration: 2.411
}
```
