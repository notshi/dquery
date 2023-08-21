# :tada: Introduction

http://d-portal.org/dquery/

Use SQL to search **the complete IATI data, including non-standard attributes and extensions** directly into the live d-portal database, in a modern browser.

```diff
+ Documentation is ongoing so please bear with us.
```

**If you have a data need or question** that could benefit from a query but don't know how, do [raise an issue](https://github.com/notshi/dquery/issues/new) and we can work it out together.

dQuery works well if you are familiar with the IATI Standard and querying in SQL using JSONB data types in PostgreSQL. However, it shouldn't be too hard to pick up once you've done a few recipes.

*dQuery is an internal tool created by Wetgenes to aid the development of d-portal but publicly available as a courtesy.*

<!--
Documentation by shi Blank, 2021  
Do get in touch if you would like to contribute!  
We are on Discord https://discord.gg/UxvKPVMz
-->

### Contents
- [:pushpin: **Getting started**](#pushpin-getting-started)
  - [Data formats](#data-formats)
  - [Commands](#commands)
    - [Select](#select)
    - [From](#from)
    - [As](#as)
    - [Where](#where)
    - [In](#in)
    - [And](#and)
    - [Group by](#group-by)
    - [Order by](#order-by)
    - [Limit](#limit)
    - [Offset](#offset)
    - [Join](#join)
- [:mag: **Basic Queries**](#mag-basic-queries)
  - [Syntax](#syntax)
  - [Advanced Queries](#advanced-queries)
  - [XSON](#xson)
  - [Tables and references](#tables-and-references)
- [:doughnut: **Database Dump**](#doughnut-database-dump)
  - [Server](#server)
- [:sparkles: **Recipes**](#sparkles-recipes)
  - [Display all unique `reporting-org/@ref` published in a dataset](#display-all-unique-reporting-orgref-published-in-a-dataset)
  - [Display count of certain element in org file](#display-count-of-certain-element-in-org-file)
  - [Look for similar `iati-identifier` using a wildcard `%`](#look-for-similar-iati-identifier-using-a-wildcard-)
  - [Display IATI Registry dataset for publishers that use the same `organisation-identifier`](#display-iati-registry-dataset-for-publishers-that-use-the-same-organisation-identifier)
  - [Display IATI Registry dataset for an activity](#display-iati-registry-dataset-for-an-activity)
  - [Display duplicate activities and their count](#display-duplicate-activities-and-their-count)
  - [Display IATI Registry dataset for duplicate activities where we know the `iati-identifier`](#display-iati-registry-dataset-for-duplicate-activities-where-we-know-the-iati-identifier)
  - [Exploring traceability within IATI data](#exploring-traceability-within-iati-data)
  - [Display activities with Sector code in transactions for a specific publisher](#display-activities-with-sector-code-in-transactions-for-a-specific-publisher)
  - [Display top-level elements including namespaces](#display-top-level-elements-including-namespaces)
  - [Filtering on custom namespace elements](#filtering-on-custom-namespace-elements)
  - [Display iati-organisation id with curated elements within `total-budget`](#display-iati-organisation-id-with-curated-elements-within-total-budget)
  - [Group by publishers that use a particular `@ref`](#group-by-publishers-that-use-a-particular-ref)
  - [Display first `/narrative` array in multiple roots, count and grouped for a particular `@ref`](#display-first-narrative-array-in-multiple-roots-count-and-grouped-for-a-particular-ref)
  - [Display a random activity that has a Result Actual Comment](#display-a-random-activity-that-has-a-result-actual-comment)
  - [Display activity with the lengthiest Result Actual Comment](#display-activity-with-the-lengthiest-result-actual-comment)
  - [Text search](#text-search)
  - [Display all publishers listing (GIZ) in `participating-org/narrative`](#display-all-publishers-listing-giz-in-participating-orgnarrative)
  - [Display all `participating-org` with `@crs-channel-code`](#display-all-participating-org-with-crs-channel-code)
  - [Display `participating-org` with their `@role`, `@ref`, `@type` and include associated transaction types](#display-participating-org-with-their-role-ref-type-and-include-associated-transaction-types)
  - [Display number of `participating-org` found in activities and then display the activity with the most](#display-number-of-participating-org-found-in-activities-and-then-display-the-activity-with-the-most)
  - [Display all `participating-org` names, grouped by `@ref`](#display-all-participating-org-names-grouped-by-ref)
  - [Display the `participating-org@ref` with the most narratives](#display-the-participating-orgref-with-the-most-narratives)
  - [Display all `narrative` grouped by `participating-org@ref`](#display-all-narrative-grouped-by-participating-orgref)
  - [Display all narratives and their count for a specific `participating-org@ref`](#display-all-narratives-and-their-count-for-a-specific-participating-orgref)
  - [Display all publishers with `conditions@attached` as YES](#display-all-publishers-with-conditionsattached-as-yes)
  - [Display narratives and count, grouped by publishers with `condition@type`](#display-narratives-and-count-grouped-by-publishers-with-conditiontype)
  - [Display narratives grouped by publishers with `condition@type` 1](#display-narratives-grouped-by-publishers-with-conditiontype-1)
  - [Display a list of changed IATI organisation identifiers](#display-a-list-of-changed-iati-organisation-identifiers)
  - [Display a list of changed IATI activity identifiers](#display-a-list-of-changed-iati-activity-identifiers)
  - [Display full activity data within an element for multiple identifiers](#display-full-activity-data-within-an-element-for-multiple-identifiers)
  - [Display identifiers of activities that are published in version 1 of the standard](#display-identifiers-of-activities-that-are-published-in-version-1-of-the-standard)
  - [Find the activity with most number of `narrative` in an element](#find-the-activity-with-most-number-of-narrative-in-an-element)
  - [Display number of transactions you can find in an activity](#display-number-of-transactions-you-can-find-in-an-activity)
  - [Display active projects grouped by country](#display-active-projects-grouped-by-country)
  - [Freetext search for activities starting in year 2022](#freetext-search-for-activities-starting-in-year-2022)
  - [Count active projects from 2021 onwards](#count-active-projects-from-2021-onwards)
  - [Display location data for active projects for a country](#display-location-data-for-active-projects-for-a-country)
  - [Display `@percentage` reported for `recipient-country`, starting with the lowest number](#display-percentage-reported-for-recipient-country-starting-with-the-lowest-number)
  - [Display number of items with full activity data for an element and vocab](#display-number-of-items-with-full-activity-data-for-an-element-and-vocab)
  - [Diplay list of Publishers reporting SDG Goals and Targets](#diplay-list-of-publishers-reporting-sdg-goals-and-targets)
  - [Diplay `sum` of all transaction types that are `@code` 3](#diplay-sum-of-all-transaction-types-that-are-code-3)
  - [Display activities with more than 10 `actual` values reported in results data](#display-activities-with-more-than-10-actual-values-reported-in-results-data)
  - [Display activities with more than 1 comment reported in results data](#display-activities-with-more-than-1-comment-reported-in-results-data)
  - [Subquery to get full activity data](#subquery-to-get-full-activity-data)
  - [Display full activity data with attribute of certain value](#display-full-activity-data-with-attribute-of-certain-value)
  - [Display unique activity identifiers with attribute of certain value](#display-unique-activity-identifiers-with-attribute-of-certain-value)
  - [Get a table of most used values sorted by count](#get-a-table-of-most-used-values-sorted-by-count)
  - [Find humanitarian activities at activity level for multiple countries](#find-humanitarian-activities-at-activity-level-for-multiple-countries)
  - [Freetext search in transaction narrative for multiple countries](#freetext-search-in-transaction-narrative-for-multiple-countries)
  - [Freetext search on humanitarian activities including transactions for multiple countries](#freetext-search-on-humanitarian-activities-including-transactions-for-multiple-countries)
  - [Display all activities for a country_code within COVID-19](#display-all-activities-for-a-country_code-within-covid-19)
  - [Flattening `document-link` with higher level elements in `iati-activity`](#flattening-document-link-with-higher-level-elements-in-iati-activity)
  - [Display unique `document-link@url`, publisher and activity identifier](#display-unique-document-linkurl-publisher-and-activity-identifier)
  - [Display a count of `document-link` reported by a publisher](#display-a-count-of-document-link-reported-by-a-publisher)
  - [Display `@last-updated-datetime` for activities in Somalia](#display-last-updated-datetime-for-activities-in-somalia)
  - [Ask PostgreSQL to do as they are told with `materialized`](#ask-postgresql-to-do-as-they-are-told-with-materialized)


# :pushpin: Getting started

The current interface is sparse and consists of two panels; the editor on the left and the console on the right.

The numbers you see on the side of the editor are line numbers and are purely there as visual aid.  
The console displays results of your query when you click the *Run* button.

There are five buttons above these panels which perform various things.

**Examples**  
Some example recipes.

**Run** (optional)  
View results in the browser. ( _Keyboard Shortcut_ **Ctrl** + **Enter** )  
*Please limit your results! If queries are complex, this can slow down the browser.*  

**Explain**  
Inserts the SQL keyword, "explain" in front of your query.  
Tells you how the query will run instead of running it.  
*Can be used to optimise queries.*

**View on d-portal**  
View the results of the query on the d-portal website.  
*Only works if query includes activity identifiers using the `aid` command.*

**Download**  
Download data in various formats **without** viewing results in the browser.

**How to**  
Sends you to this documentation page in a new window/tab.

You can drag the partition to increase or decrease the space of either panel.  

<p align="right"><a href="#tada-introduction">To Top</a></p>

## Run

When you click 'Run' or press 'Ctrl+Enter', the console to the right of the editor will display the results of your query.

Generally, it will look something like this:

```sql
{
    rows: [
        {
            aid: "DK-CVR-40781218-AA"
        }
    ],
    dquery: http://d-portal.org/dquery#select%20aid%0Afrom%20xson%20where%20root='/iati-activities/iati-activity'%0Alimit%201;%0A,
    time: 0.017,
    count: 1
}
```

That's quite a bit to dissect, isn't it?

There are 4 separate things being displayed here but you will probably only need `rows`.

`rows` will contain the data you asked for.  

`dquery` is the url of the current query page.  
`time` is how long the database took to answer this query, in seconds.  
`count` is how many rows there are in total on the page.

<p align="right"><a href="#tada-introduction">To Top</a></p>

## Data formats

Downloads of data is available as csv, json, xml and html.

**The default format of the queried results is json.**

> When getting a lot of data, json has to repeat the header names so you may find that opting for csv is substantially faster for larger queries.

The html option will only work at the top level of an activity or an organisation file; ie. when `select *`, the root needs to be at either `/iati-activities/iati-activity` or `/iati-organisations/iati-organisation`.

The html option is a Print-friendly version of SAVi (Simple Activity Viewer).

Replace `/#` in the url with `?form=csv&sql=` to get the query link in different formats.  

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

By using the `select aid` command, you will get a list of activity identifiers that you can then explore on d-portal by clicking on the, 'View on d-portal' button.  
*Note that if you rename this column by using the `as` command, you will not be able to view the results on d-portal.*

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

<p align="right"><a href="#tada-introduction">To Top</a></p>

## From

Tables are where the data is stored and From specifies which tables to look at to get them.

For most queries, we are only looking at the `xson` table.  
The `xson` table includes the entire IATI activity and organisation elements, as well as non-standard attributes and extensions.

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

<p align="right"><a href="#tada-introduction">To Top</a></p>

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
| in | Specifies multiple values, arrays, short for multiple Or conditions |
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

<p align="right"><a href="#tada-introduction">To Top</a></p>

## In

Sometimes you want to look for something within multiple elements or within an element that has multiple values.  
There are many elements in the IATI Standard that have multiple values; for example, there can be multiple countries reported in an activity.

In such cases, we can use `in` to look through multiple values or a number of arrays.

For a full list of arrays in the database, please refer [here](#tables-and-references).

#### Examples

This looks into the `recipient-country` element that can occur multiple times in an activity.  
We refer to this element as an **array**.
```sql
and aid in (
    select aid from xson where root='/iati-activities/iati-activity/recipient-country'
    and xson->>'@code' = 'SO'
)
```

This looks into multiple elements seperated by a comma `,`.
```sql
where root in (
    '/iati-activities/iati-activity/participating-org',
    '/iati-activities/iati-activity/transaction/'
)
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

<p align="right"><a href="#tada-introduction">To Top</a></p>

## Limit

We use Limit to specify the number of results we want in the query.  
This is especially useful if we want to test a query that might be intensive or to quickly see if it works.

This will return the first 10 results from a table.

#### Examples
```sql
limit 10;
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

<p align="right"><a href="#tada-introduction">To Top</a></p>

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


<p align="right"><a href="#tada-introduction">To Top</a></p>

# :mag: Basic queries

We often refer to tables, rows and columns when querying. This is because the database contains many tables.

Each table is identified by a name, for example `xson`.  
Each table has rows and columns, and those have names too, for example `iati-identifier`.

For a full list of available table and column names in the database, please refer [here](https://github.com/devinit/D-Portal/blob/master/dstore/js/dstore_db.js#L48).  

When we do a query, we write SQL statements.  

SQL keywords are **not** case sensitive, so `select` is the same as `SELECT`.  
We always end a statement with a semicolon `;` so you can write many statements in a query.

However, although we allow multiple SQL statements to be executed at a time, only the latest one will display.

**We always limit our queries because the database is huge!**  
Returning large results can impact your browser performance, and ultimately the server and the d-portal website.


<p align="right"><a href="#tada-introduction">To Top</a></p>

## Syntax

The following is a SQL statement.  
For this purpose, all SQL keywords are in upper-case.

Hopefully by reading the following code snippet, you are able to figure out the different clauses and fields that make up parts of the statement.  
You can always refer back to the most common commands [here](#commands).

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

```jsonc
{
    rows: [
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
    time: 0.014
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

```jsonc
{
    rows: [
        {
            aid: "XM-DAC-903-SPI-10117"
        }
    ],
    time: 0.035
}
```

This gets you all the data within the `total-expenditure` element table from a random organisation file.
```sql
select *
from xson where root='/iati-organisations/iati-organisation/total-expenditure'
limit 1;
```

Result

```jsonc
{
    rows: [
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
    time: 0.009
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

```jsonc
{
    rows: [
        {
            pid: "XM-DAC-41146",
            Total Expenditure in USD: "354113927"
        }
    ],
    time: 0.009
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

## XSON

For the majority of our queries, we will be using the `xson` table as it includes all activity and organisation elements according to the IATI Standard, as well as non-standard attributes and extensions that can be found in published data.

You will find `root='/iati-activities/iati-activity'` in many of the example queries below.

This tells PostgreSQL where to look within the `xson` table by giving it a start of the xpath.  
When we say xpath, we mean where an IATI element is within the Activity or Organisation Standard; ie. `/iati-activities/iati-activity`.

If you are familiar with the IATI Standard, you can manipulate the xpath to look at any activity or organisation element.

When we set conditions for a query, it usually looks like the following.

```sql
and xson->>'@vocabulary' = '99'
```
```sql
and xson->>'@dstore:dataset' = 'slovakaid-69_1_ac'
```

This filters the queries to certain requirements by looking at specific values of IATI element attributes.


`xson` is a JSON representation of the XML data, such that if you concatenate the root value plus all the object key names in a tree, the result will be a valid XML path that can be looked up in the IATI schema to tell you what data it represents.

For example, within the `xson` row, the entries to the left of the colon; ie. `@type`, `@iso-date`, `/narrative` and `@xml` are key names.  
And thus, object entries on the right are the values.
 
`aid`, `pid`, `root` and `xson` are row values from the PostgreSQL database.

```jsonc
{
    aid: "ID-KHH-5018112631240040-ID012-LEAP",
    pid: "ID-KHH-5018112631240040",
    root: "/iati-activities/iati-activity/activity-date",
    xson: {
        @type: "1",
        @iso-date: "2017-09-01",
        /narrative: [
            {
                : "Grant Decision LEAP Project was signed by EKN (Minbuza) Reference: JAK-2017/859",
                @xml:lang: "EN"
            }
        ]
    }
}
```


<p align="right"><a href="#tada-introduction">To Top</a></p>

## Advanced Queries

For more information about the SQL language, the full list of functions and futher documentation, please refer to the following official pages.

1. [PostgreSQL Queries](https://www.postgresql.org/docs/12/queries.html)

2. [PostgreSQL Functions and Operators](https://www.postgresql.org/docs/12/functions.html)


<p align="right"><a href="#tada-introduction">To Top</a></p>

## Tables and references

We have a few different tables you can query but you should probably use the ones referred to in the recipes and examples.

For example, the [slug](https://github.com/devinit/D-Portal/blob/master/dstore/js/dstore_db.js#L169) table stores duplicate identifiers and would be the best place to look for such things.

The [trans](https://github.com/devinit/D-Portal/blob/master/dstore/js/dstore_db.js#L100) and [budget](https://github.com/devinit/D-Portal/blob/master/dstore/js/dstore_db.js#L124) tables would be very useful for analysts interested in IATI transaction data as the complicated job of splitting by sector / country and currency conversions to USD, CAD, GBP and Euro is already done for you.

**Please note that transaction data is in IATI Standard version 1.04 as that is when d-portal was created.**

This will return a list of available tables in the database.

```sql
select tablename from pg_tables
where schemaname='public'
order by tablename
```

This will return a list of available `xson` roots and how often they are used in the database.  
They also represent whereabouts in the json you will find **arrays**.

```sql
select root, count(*)
from xson
group by 1 order by 2 desc
```

For a complete list of column names and elements , please refer to the following pages.

1. [List of available table and column names in the database](https://github.com/devinit/D-Portal/blob/master/dstore/js/dstore_db.js#L48)

2. [List of IATI acitivity elements](https://iatistandard.org/en/iati-standard/203/activity-standard/)

Adding `explain` before your query displays how the database handles your query.

```sql
explain
select * from table
where condition
limit 1;
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

# :doughnut: Database Dump

*Spin up a server, import and start querying.*

**The entire d-portal database is about 3GB in size.**

The following dumps can be imported locally and queried using the same SQL code that is used on the web interface so you can run large queries on it without clogging up d-portal.

1. PostgreSQL `pg_dump` custom format so `pg_restore` can be used with its various options *(Updated nightly)*  
    [http://d-portal.org/db/dstore.pg](http://d-portal.org/db/dstore.pg)

2. A zip of all the raw cached xml *(Updated nightly)*  
    [http://d-portal.org/db/cache.xml.zip](http://d-portal.org/db/cache.xml.zip)


<p align="right"><a href="#tada-introduction">To Top</a></p>

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


<p align="right"><a href="#tada-introduction">To Top</a></p>

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

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

```jsonc
{
    rows: [
        {
            reporting_org: "XM-DAC-69-0"
        }
    ],
    time: 0.015
}
```

We can do the same search using the `xson` table but this will be much slower as this column is not part of the IATI Standard and so is not indexed.  

You can compare this by looking at the `duration` of the query.

This query took 17 seconds compared to almost instantaneous in the previous query.

**When creating queries, it is always good to aim at efficiency so it could be that it takes a few tries to get it right.**  
Databases are complex creatures!

Here we selected the `/reporting-org@ref` element from the `xson` table and gave the column a temporary name (alias).  
We are looking at the dataset `slovakaid-69_1_ac` in the `@dstore:dataset` column.

```sql
select xson->>'/reporting-org@ref' as reporting_org
from xson where root='/iati-activities/iati-activity'
and xson->>'@dstore:dataset' = 'slovakaid-69_1_ac'
group by reporting_org
order by reporting_org
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            reporting_org: "XM-DAC-69-0"
        }
    ],
    time: 17.329
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display count of certain element in org file
```sql
select

count(*)

from xson where root='/iati-organisations/iati-organisation'
and xson->>'/total-budget' IS NOT NULL

```

Result

```jsonc
{
    rows: [
        {
            count: "437"
        }
    ],
    time: 0.053
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

```jsonc
{
    rows: [
        {
            aid: "1022474"
        },
        {
            aid: "DE-1-201022474"
        }
    ],
    time: 18.484
}
```

Below is an alternative query using a different table `act`.

This is a much faster search because it is looking at a smaller table that has the same data in column, `iati-identifier`.  
The `act` table is a curated table and does not include all IATI elements.

For a full list of available table and column names in the database, please refer [here](https://github.com/devinit/D-Portal/blob/master/dstore/js/dstore_db.js#L48).  

```sql
select act.aid as identifier
from act where act.aid like '%1022474%'
group by act.aid
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            identifier: "1022474"
        },
        {
            identifier: "DE-1-201022474"
        }
    ],
    time: 1.457
}
```


<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display IATI Registry dataset for publishers that use the same `organisation-identifier`
From https://github.com/codeforIATI/iati-data-bugtracker/issues/19, we wanted to check if there were more publishers that used the same id.

```sql
select
distinct xson->>'@dstore:dataset' as "IATI Registry Dataset"
from xson where root='/iati-organisations/iati-organisation' 
and xson->>'/organisation-identifier' like '%NL-KVK-30285304%'
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            IATI Registry Dataset: "humana_houben-org"
        }
    ],
    time: 0.058
}
```

We can display the `narrative` of both `/name` and `/reporting-org` so that we know who these publishers are.  
We include these two different elements become sometimes publishers can publish either one or both fields.

```sql
select
distinct xson->'/reporting-org/narrative'->0->>'' as "Publisher",
xson->'/name/narrative'->0->>'' as "Publisher Name",
xson->>'@dstore:dataset' as "IATI Registry Dataset"
from xson where root='/iati-organisations/iati-organisation' 
and xson->>'/organisation-identifier' like '%NL-KVK-30285304%'
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            Publisher: "Humana people to people",
            Publisher Name: "Humana people to people",
            IATI Registry Dataset: "humana_houben-org"
        }
    ],
    time: 0.059
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display IATI Registry dataset for an activity

```sql
select
distinct xson->>'@dstore:dataset' as "IATI Registry Dataset"
from xson where root='/iati-activities/iati-activity' 
and xson->>'/iati-identifier'='XM-DAC-6-4-011752-01-6'
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            IATI Registry Dataset: "aics-jo"
        }
    ],
    time: 0.252
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display duplicate activities and their count

Multiple identifiers are only stored in the `slug` table so we should look there.

[View this query](http://d-portal.org/dquery/#select%20aid,%20count(*)%0Afrom%20slug%0Agroup%20by%20aid%20having%20count(*)%20%3E%201%0Aorder%20by%20count(*)%20desc%0Alimit%201;) on dQuery.

```sql
select aid, count(*)
from slug
group by aid having count(*) > 1
order by count(*) desc
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            aid: "ES-DIR3-E04585801-009-066169",
            count: "105"
        }
    ],
    time: 0.557
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display IATI Registry dataset for duplicate activities where we know the `iati-identifier`
From https://github.com/codeforIATI/iati-data-bugtracker/issues/10, we wanted to look for datasets containing a specific `iati-identifier`.

Multiple identifiers are only stored in the `slug` table so we should look there.

```sql
select *
from slug
where aid='XM-DAC-6-4-011752-01-6'
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            aid: "XM-DAC-6-4-011752-01-6",
            slug: "aics-679"
        },
        {
            aid: "XM-DAC-6-4-011752-01-6",
            slug: "aics-jo"
        }
    ],
    time: 0.009
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Exploring traceability within IATI data

Here are steps to reproduce the results for traceability as outlined in the [methodology used for IATI Trace](https://iatistandard.org/en/news/data-use-fund-explores-traceability-within-iati-data/).

The report used a previous version of d-portal that was not able to provide support for all the data needed.  
However, this should now be possible with dQuery.

[View this query](http://d-portal.org/dquery/#--/*%20Show%20all%20participating-org%20(include%20all%20attributes)%20for%20this%20activity%0A%0Aselect%20aid,%0Ajsonb_array_elements(xson%20-%3E%20'/participating-org')%20as%20%22/participating-org%22%0Afrom%20xson%20where%20root='/iati-activities/iati-activity'%20%0Aand%20aid='GB-CHC-1038860-28437'%0Alimit%20100;%0A%0A--*/%0A%0A%0A--/*%20Show%20all%20'Funding'%20participating-org%20(include%20activity-id,%20narrative%20and%20ref)%20for%20this%20activity%0A%0Aselect%20xson-%3E%3E'@activity-id'%20as%20%22activity-id%22,%0Axson-%3E'/narrative'-%3E0-%3E%3E''%20as%20%22narrative%22,%0Axson-%3E%3E'@ref'%20as%20%22@ref%22%0Afrom%20xson%20where%20root='/iati-activities/iati-activity/participating-org'%0Aand%20xson-%3E%3E'@role'='1'%0Aand%20aid='GB-CHC-1038860-28437'%0Alimit%20100;%0A%0A--*/%0A%0A%0A--/*%20Show%20an%20activity%20that%20has%20provider-activity-id%20in%20transaction%20type%201%20or%2011%0A%0Aselect%20distinct%20aid%0Afrom%20xson%20where%20root='/iati-activities/iati-activity/transaction'%0Aand%20xson-%3E%3E'/transaction-type@code'='1'%0Aor%20xson-%3E%3E'/transaction-type@code'='11'%0Aand%20xson-%3E%3E'/provider-org@provider-activity-id'%20is%20not%20null%0Alimit%201;%0A%0A--*/%0A%0A%0A--/*%20Show%20activities%20that%20have%20this%20identifier%20in%20provider-activity-id%0A%0Aselect%20distinct%20aid%0Afrom%20xson%20where%20root='/iati-activities/iati-activity/transaction'%0Aand%20xson-%3E%3E'/provider-org@provider-activity-id'='GB-CHC-1038860-28437'%0Alimit%20100;%0A%0A--*/%0A%0A%0A--/*%20Show%20activities%20that%20have%20this%20identifier%20in%20receiver-activity-id%0A%0Aselect%20distinct%20aid%0Afrom%20xson%20where%20root='/iati-activities/iati-activity/transaction'%0Aand%20xson-%3E%3E'/receiver-org@receiver-activity-id'='GB-CHC-1038860-28437'%0Alimit%20100;%0A%0A--*/%0A%0A%0A--/*%20Show%20all%20provider-activity-id%20(include%20narrative%20and%20transaction%20type)%20for%20this%20activity%0A%0Aselect%20xson-%3E%3E'/provider-org@provider-activity-id'%20as%20%22provider-activity-id%22,%0Axson-%3E'/receiver-org/narrative'-%3E0-%3E%3E''%20as%20%22receiver-narrative%22,%0Axson-%3E%3E'/transaction-type@code'%20as%20%22transaction-type%22%0Afrom%20xson%20where%20root='/iati-activities/iati-activity/transaction'%0Aand%20aid='GB-CHC-1038860-28437'%0Alimit%201000;%0A%0A--*/%0A%0A%0A--/*%20Show%20all%20receiver-activity-id%20(include%20narrative%20and%20transaction%20type)%20for%20this%20activity%0A%0Aselect%20xson-%3E%3E'/receiver-org@receiver-activity-id'%20as%20%22receiver-activity-id%22,%0Axson-%3E'/receiver-org/narrative'-%3E0-%3E%3E''%20as%20%22receiver-narrative%22,%0Axson-%3E%3E'/transaction-type@code'%20as%20%22transaction-type%22%0Afrom%20xson%20where%20root='/iati-activities/iati-activity/transaction'%0Aand%20aid='GB-CHC-1038860-28437'%0Alimit%201000;%0A%0A--*/%0A%0A%0A--/*%20Show%20all%20related%20activities%20(include%20related-activity%20type)%20for%20this%20activity%0A%0Aselect%20aid%20as%20%22Activity%20Identifier%22,%0Axson-%3E%3E'@type'%20as%20%22Related%20Activity%20Type%22%0Afrom%20xson%20where%20root='/iati-activities/iati-activity/related-activity'%0Aand%20xson-%3E%3E'@ref'='GB-CHC-1038860-28437'%0Alimit%20100;%0A%0A--*/%0A%0A%0A--/*%20Show%20all%20'Parent'%20related%20activities%20for%20this%20activity%0A%0Aselect%20xson-%3E%3E'@ref'%20as%20%22Activity%20Identifier%22%0Afrom%20xson%20where%20root='/iati-activities/iati-activity/related-activity'%0Aand%20xson-%3E%3E'@type'='1'%0Aand%20aid='GB-CHC-1038860-28437'%0Alimit%20100;%0A%0A--*/%0A%0A%0A--/*%20Show%20all%20'Child'%20related%20activities%20for%20this%20activity%0A%0Aselect%20xson-%3E%3E'@ref'%20as%20%22Activity%20Identifier%22%0Afrom%20xson%20where%20root='/iati-activities/iati-activity/related-activity'%0Aand%20xson-%3E%3E'@type'='2'%0Aand%20aid='GB-CHC-1038860-28437'%0Alimit%20100;%0A%0A--*/%0A%0A%0A--/*%20Show%20all%20activities%20with%20this%20identifier%20as%20'Parent'%20in%20related%20activities%0A%0Aselect%20aid%0Afrom%20xson%20where%20root='/iati-activities/iati-activity/related-activity'%0Aand%20xson-%3E%3E'@type'='1'%0Aand%20xson-%3E%3E'@ref'='GB-CHC-1038860-28437'%0Alimit%20100;%0A%0A--*/%0A%0A%0A--/*%20Show%20all%20activities%20with%20this%20identifier%20as%20'Child'%20in%20related%20activities%0A%0Aselect%20aid%0Afrom%20xson%20where%20root='/iati-activities/iati-activity/related-activity'%0Aand%20xson-%3E%3E'@type'='2'%0Aand%20xson-%3E%3E'@ref'='GB-CHC-1038860-28437'%0Alimit%20100;%0A%0A--*/%0A) on dQuery.

You can write as many SQL statements in the editor but only the latest one will be displayed as results.

Any line starting with `--` are commented out so you can delete any `--` to start a comment chunk.  
Alternatively, just delete the query chunk you don't need.

Queries are limited so it doesn't stress the server but you can increase or decrease the `limit` accordingly.

Click the _Download_ button to choose the format you want to download the data in.

```sql
--/* Show all participating-org (include all attributes) for this activity

select aid,
jsonb_array_elements(xson -> '/participating-org') as "/participating-org"
from xson where root='/iati-activities/iati-activity' 
and aid='GB-CHC-1038860-28437'
limit 100;

--*/


--/* Show all 'Funding' participating-org (include activity-id, narrative and ref) for this activity

select xson->>'@activity-id' as "activity-id",
xson->'/narrative'->0->>'' as "narrative",
xson->>'@ref' as "@ref"
from xson where root='/iati-activities/iati-activity/participating-org'
and xson->>'@role'='1'
and aid='GB-CHC-1038860-28437'
limit 100;

--*/


--/* Show an activity that has provider-activity-id in transaction type 1 or 11

select distinct aid
from xson where root='/iati-activities/iati-activity/transaction'
and xson->>'/transaction-type@code'='1'
or xson->>'/transaction-type@code'='11'
and xson->>'/provider-org@provider-activity-id' is not null
limit 1;

--*/


--/* Show activities that have this identifier in provider-activity-id

select distinct aid
from xson where root='/iati-activities/iati-activity/transaction'
and xson->>'/provider-org@provider-activity-id'='GB-CHC-1038860-28437'
limit 100;

--*/


--/* Show activities that have this identifier in receiver-activity-id

select distinct aid
from xson where root='/iati-activities/iati-activity/transaction'
and xson->>'/receiver-org@receiver-activity-id'='GB-CHC-1038860-28437'
limit 100;

--*/


--/* Show all provider-activity-id (include narrative and transaction type) for this activity

select xson->>'/provider-org@provider-activity-id' as "provider-activity-id",
xson->'/receiver-org/narrative'->0->>'' as "receiver-narrative",
xson->>'/transaction-type@code' as "transaction-type"
from xson where root='/iati-activities/iati-activity/transaction'
and aid='GB-CHC-1038860-28437'
limit 1000;

--*/


--/* Show all receiver-activity-id (include narrative and transaction type) for this activity

select xson->>'/receiver-org@receiver-activity-id' as "receiver-activity-id",
xson->'/receiver-org/narrative'->0->>'' as "receiver-narrative",
xson->>'/transaction-type@code' as "transaction-type"
from xson where root='/iati-activities/iati-activity/transaction'
and aid='GB-CHC-1038860-28437'
limit 1000;

--*/


--/* Show all related activities (include related-activity type) for this activity

select aid as "Activity Identifier",
xson->>'@type' as "Related Activity Type"
from xson where root='/iati-activities/iati-activity/related-activity'
and xson->>'@ref'='GB-CHC-1038860-28437'
limit 100;

--*/


--/* Show all 'Parent' related activities for this activity

select xson->>'@ref' as "Activity Identifier"
from xson where root='/iati-activities/iati-activity/related-activity'
and xson->>'@type'='1'
and aid='GB-CHC-1038860-28437'
limit 100;

--*/


--/* Show all 'Child' related activities for this activity

select xson->>'@ref' as "Activity Identifier"
from xson where root='/iati-activities/iati-activity/related-activity'
and xson->>'@type'='2'
and aid='GB-CHC-1038860-28437'
limit 100;

--*/


--/* Show all activities with this identifier as 'Parent' in related activities

select aid
from xson where root='/iati-activities/iati-activity/related-activity'
and xson->>'@type'='1'
and xson->>'@ref'='GB-CHC-1038860-28437'
limit 100;

--*/


--/* Show all activities with this identifier as 'Child' in related activities

select aid
from xson where root='/iati-activities/iati-activity/related-activity'
and xson->>'@type'='2'
and xson->>'@ref'='GB-CHC-1038860-28437'
limit 100;

--*/

```

For example, here's a single query from the list above.

View the [query](http://d-portal.org/dquery/#--/*%20Show%20all%20provider-activity-id%20(include%20narrative%20and%20transaction%20type)%20for%20this%20activity%0A%0Aselect%20xson-%3E%3E'/provider-org@provider-activity-id'%20as%20%22provider-activity-id%22,%0Axson-%3E'/receiver-org/narrative'-%3E0-%3E%3E''%20as%20%22receiver-narrative%22,%0Axson-%3E%3E'/transaction-type@code'%20as%20%22transaction-type%22%0Afrom%20xson%20where%20root='/iati-activities/iati-activity/transaction'%0Aand%20aid='GB-CHC-1038860-28437'%0Alimit%201;%0A%0A--*/) in dQuery.  
View the [JSON](http://d-portal.org/dquery?sql=--%2F*%20Show%20all%20provider-activity-id%20(include%20narrative%20and%20transaction%20type)%20for%20this%20activity%0A%0Aselect%20xson-%3E%3E%27%2Fprovider-org%40provider-activity-id%27%20as%20%22provider-activity-id%22%2C%0Axson-%3E%27%2Freceiver-org%2Fnarrative%27-%3E0-%3E%3E%27%27%20as%20%22receiver-narrative%22%2C%0Axson-%3E%3E%27%2Ftransaction-type%40code%27%20as%20%22transaction-type%22%0Afrom%20xson%20where%20root%3D%27%2Fiati-activities%2Fiati-activity%2Ftransaction%27%0Aand%20aid%3D%27GB-CHC-1038860-28437%27%0Alimit%201%3B%0A%0A--*%2F) generated.
View the [CSV](http://d-portal.org/dquery?form=csv&sql=--%2F*%20Show%20all%20provider-activity-id%20(include%20narrative%20and%20transaction%20type)%20for%20this%20activity%0A%0Aselect%20xson-%3E%3E%27%2Fprovider-org%40provider-activity-id%27%20as%20%22provider-activity-id%22%2C%0Axson-%3E%27%2Freceiver-org%2Fnarrative%27-%3E0-%3E%3E%27%27%20as%20%22receiver-narrative%22%2C%0Axson-%3E%3E%27%2Ftransaction-type%40code%27%20as%20%22transaction-type%22%0Afrom%20xson%20where%20root%3D%27%2Fiati-activities%2Fiati-activity%2Ftransaction%27%0Aand%20aid%3D%27GB-CHC-1038860-28437%27%0Alimit%201%3B%0A%0A--*%2F) generated.

```sql
--/* Show all provider-activity-id (include narrative and transaction type) for this activity

select xson->>'/provider-org@provider-activity-id' as "provider-activity-id",
xson->'/receiver-org/narrative'->0->>'' as "receiver-narrative",
xson->>'/transaction-type@code' as "transaction-type"
from xson where root='/iati-activities/iati-activity/transaction'
and aid='GB-CHC-1038860-28437'
limit 1;

--*/
```

Result

```jsonc
{
    rows: [
        {
            provider-activity-id: "GB-CHC-1038860-28437",
            receiver-narrative: "ACS/AMO Congo",
            transaction-type: "2"
        }
    ],
    time: 0.004
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display activities with Sector code in transactions for a specific publisher

This query looks for activities that have a 5 Digit Sector code published in the Transaction element for a specific publisher.

[View this query](http://d-portal.org/dquery/#select%20pid%20as%20%22Publisher%22,%20aid%0Afrom%20xson%20where%20root='/iati-activities/iati-activity/transaction/sector'%20%0Aand%20xson-%3E%3E'@code'%20=%20'13040'%0Aand%20pid%20='US-GOV-14'%0Alimit%201) on dQuery.

```sql
select pid as "Publisher", aid
from xson where root='/iati-activities/iati-activity/transaction/sector' 
and xson->>'@code' = '13040'
and pid ='US-GOV-14'
limit 1
```

Result

```jsonc
{
    rows: [
        {   
            Publisher: "US-GOV-14",
        aid: "US-GOV-14-NU2GGH000144"
		}
	],
	time: 0.038
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display top-level elements including namespaces
Raised https://github.com/codeforIATI/iati-ideas/issues/29

Publishers can add [all sorts of things](https://iatistandard.org/en/iati-standard/203/namespaces-extensions/) beyond the IATI Standard in their data so it would be nice to see what these things are.

Presently, this poses some difficulties in querying these custom elements because their key names can be different.

Here is a list of some of them (!).

```
@xmlns:afdb
@xmlns:aidenvironment
@xmlns:akvo
@xmlns:budgetline
@xmlns:cerf
@xmlns:cgiar
@xmlns:chd
@xmlns:cso
@xmlns:dstore
@xmlns:gavi
@xmlns:globalinitiative
@xmlns:iati-activities
@xmlns:interactions-ngoaidmap
@xmlns:ir
@xmlns:mcc
@xmlns:p2
@xmlns:unhcr
@xmlns:usg
```

To get this list, we had to use `jsonb_object_keys` to return top-level elements in the table and filter out elements that are found in the Activity standard manually. More on that [here](https://www.postgresql.org/docs/current/functions-json.html#FUNCTIONS-JSON-PROCESSING-TABLE).

```sql
select distinct jsonb_object_keys(xson)
from xson where root='/iati-activities/iati-activity'
order by 1
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            jsonb_object_keys: "@xsi:schemaLocation"
        }
    ],
    time: 11.209
}
```

The following is for the Organisation standard.

```sql
select distinct jsonb_object_keys(xson)
from xson where root='/iati-organisations/iati-organisation'
order by 1
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            jsonb_object_keys: "/aidstream:country"
        }
    ],
    time: 0.052
}
```

You can then find an activity that uses this namespace and display its value.

```sql
select distinct aid, xson->>'@xmlns:afdb' as "@xmlns:afdb url"
from xson where root='/iati-activities/iati-activity'
and xson->>'@xmlns:afdb' is not null
order by 1
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            aid: "46002-G-EG-AAG-ZZZ-001",
            @xmlns:afdb url: http://afdb.org/iati/
        }
    ],
    time: 18.862
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Filtering on custom namespace elements
Raised https://github.com/devinit/D-Portal/issues/568

There are many fields in published data that are not in the IATI Standard list of elements.

These additional data called [Namespaces & Extensions](https://iatistandard.org/en/iati-standard/203/namespaces-extensions/) can be found in many published data and they can be queried like so.

```sql
SELECT DISTINCT aid FROM xson
WHERE
	root='/iati-activities/iati-activity/transaction'
AND
	xson->>'/usg:treasury-account/usg:regular-account@code' = '72'
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            aid: "US-GOV-1-00001"
        }
    ],
    time: 7.962
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

```jsonc
{
    rows: [
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
    time: 0.031
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

```jsonc
{
    rows: [
        {
            org: "XM-OCHA-CBPF",
            count: "308"
        }
    ],
    time: 29.328
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

```jsonc
{
    rows: [
        {
            reference: "XM-OCHA-CBPF-NGA75",
            narrative: "Nigeria Humanitarian Fund",
            publisher: "XM-OCHA-CBPF",
            count: "308"
        }
    ],
    time: 7.945
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display a random activity that has a Result Actual Comment

This query gives you a random activity that has a comment in the `result/actual` element and spits out the comment narrative and language it is published in.

View this [query](http://d-portal.org/dquery/#select%20jsonb_array_elements(xson%20-%3E%20'/comment/narrative')%20as%20%22Comment%22,%20aid%0Afrom%20xson%0Awhere%20root='/iati-activities/iati-activity/result/indicator/period/actual'%0Aand%20xson-%3E%3E'/comment/narrative'%20is%20not%20null%0Alimit%201;) on dQuery.

```sql
select jsonb_array_elements(xson -> '/comment/narrative') as "Comment", aid
from xson
where root='/iati-activities/iati-activity/result/indicator/period/actual'
and xson->>'/comment/narrative' is not null
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            Comment: {
                : "Numerator: 0.88",
                @xml:lang: "EN"
            },
            aid: "47045-UZB-H-RAC"
        }
    ],
    time: 0.001
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display activity with the lengthiest Result Actual Comment

This query will give you the activity that has a comment in the `result/actual` element with the most number of words as well as the count of words found.

`length()` in [PostgreSQL](https://www.postgresql.org/docs/13/functions-string.html#id-1.5.8.10.7.2.2.9.1.1.1) returns the number of characters in the string.

We then order the length in a descending order to give us the highest number.

View this [query](http://d-portal.org/dquery/#select%20LENGTH(xson-%3E%3E'/comment/narrative'),%20jsonb_array_elements(xson%20-%3E%20'/comment/narrative')%20as%20%22Comment%22,%20aid%0Afrom%20xson%0Awhere%20root='/iati-activities/iati-activity/result/indicator/period/actual'%0Aand%20xson-%3E%3E'/comment/narrative'%20is%20not%20null%0Aorder%20by%201%20desc%0Alimit%201;) in dQuery.

```sql
select length(xson->>'/comment/narrative'), jsonb_array_elements(xson -> '/comment/narrative') as "Comment", aid
from xson
where root='/iati-activities/iati-activity/result/indicator/period/actual'
and xson->>'/comment/narrative' is not null
order by 1 desc
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            length: 593769,
            Comment: {
                : "Output1. Economic and fiscal Advisory role provided
                Prepared an advisory note to the Minister for ministerial discussions on needed economic and fiscal reforms over the short and medium terms.
                Prepared a comprehensive analysis to the minister on debt financing for the 2018-2019 period and a proposal for liability management of foreign currency debt maturities to reduce debt financing cost.
                Updated the medium-term macro-fiscal framework for 2018 onwards including projections of macroeconomic assumptions and fiscal metrics. Provided an internal assessment of the 2018 budget.
                Developed further the Oil and Gas financial Model through benchmarking analysis with Egypt and Cyprus.
                Completed monthly and quarterly reports (Personal cost Jan-June 2017, Euromain Jan-Feb 2018, Public Finance Monitor Jan-Nov 2017, Completed draft version for annual report 2016 after receiving final data, average cost for treasury bills and bonds Jan-Feb 2018).
                Followed up on the oil and gas draft laws in parliament and provided advisory notes to the Minister of Finance on the topic.
                Followed up with S&P, and Moody’s rating agencies on their draft country credit reports.

                Output 2: Legal Advisory

                Reviewed, commented and conducted negotiations for the World Bank loans and granst and followed up on the file with the Council of Ministers for approval and signature namely: “Greater Beirut Urban Transport Project” (loan negotiated on February 16, 2018).
                 Prepared with the Revenues Department a PowerPoint presentation for the introductory session of the training done by the Institute of Finance to the civil servants, regarding the Automatic Exchange of Information (AEOI) and the Exchange of Information on Request (EOIR) and present it before the directors of the departments of the ministry of finance.
                Finalized the Exchange of Information Manual
                Worked on the French version of the Lebanese model of IPPA
                Output 3: Multilateral Coordination
                Support the Minister on the technical committee of the Lebanon Crisis Response Plan (LCRP), an inter-governmental/inter-agency committee on managing the humanitarian and development aid for the Syrian crisis
                 Support the Office of the Minister of State for Women Affairs in developing policies that will improve the gender gap in Lebanon
                Output 4: Tax Reform
                E-services:
                - Data reconciliation between the e-services and SIGTAS was performed to guarantee data consistency.
                - Support to VAT e-services and e-payment continued.

                • BPR of the Tax functions
                - New tax forms were designed to simplify the tax procedures, for self-assessment and audit partial payment
                - Reconciliation between the Treasury System and VAT distribution of deductions in SIGTAS was carried out
                - VAT Arrears were prepared for the years 2002 until 2016.
                - Consolidation of all deductions and assessments reports
                - New procedures for the BDL’s VAT settlements were developed
                - New reports for compliance, data processing and refunds departments were designed
                - The automatic issuance of VAT registration certificates including the e-signature of the head of department was developed and implemented

                • Overhauling  the MOF's English Website
                - Real-time translation of MOF’s latest news, decisions, notices, procedures and activities was ensured.

                Output 5: Customs Reform
                The e-Manifest project was implemented in the airport in order to receive the manifest from abroad.
                The Part shipment was developed in order to cover the goods that arrive on different flights although they belong to the same bill of lading
                Output 6: Land Registry and Cadastre Reform
                Data Analysis was conducted to the development of multiple statistical reports concerning the foreign ownerships in Lebanon
                Data analysis was performed to extract a new report regarding sale transactions sent to the Financial Prosecution Department
                 Monitoring the nationality correction of foreign individual real estate owners",
                @xml:lang: "EN"
            },
            aid: "XM-DAC-41114-OUTPUT-00102280"
        }
    ],
    time: 0.916
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Text search
Raised https://github.com/devinit/D-Portal/issues/623

We want to look for a couple of specific word combinations in all the `narrative` element.  

We can do this easily by entering the word combinations in double quotes `"`.  
If we want to do multiple word searches at the same time, we use `or` to separate the search terms.

*Be mindful of the double quotes within the single quote when querying, as seen below.*

The default search uses `and` so if searching for two different single words, we use `spaces` to separate them, without the double quotes.  
The same method works when searching freetext on the d-portal website; ie. "hearing aids" or "learning difficulties"

For more on [websearch_to_tsquery](http://www.postgresql.cn/docs/11/textsearch-controls.html#TEXTSEARCH-PARSING-QUERIES).

```sql
select distinct aid
from xson
where to_tsvector('simple', xson->>'') 
@@ websearch_to_tsquery('simple','"hearing aids" or "learning difficulties"') 
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            aid: "BE-BCE_KBO-0423616717-KH_PE_17-21_DGD"
        }
    ],
    time: 0.079
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

```jsonc
{
    rows: [
        {
            role_: "3",
            type_: null,
            narrative: "Deutsche Gesellschaft für Internationale Zusammenarbeit (GIZ) GmbH",
            publisher: "DE-1",
            count: "2911"
        },
    ],
    time: 2.757
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display all `participating-org` with `@crs-channel-code`
```sql
select

xson->'/narrative'->0->>'' as "Name",
xson->>'@ref' as "Participating Org Ref",
xson->>'@type' as "Type",
xson->>'@role' as "Role",
xson->>'@crs-channel-code' as "CRS Channel Code"

from xson where root='/iati-activities/iati-activity/participating-org'
and xson->>'@crs-channel-code' is not null

limit 1;
```

Result

```jsonc
{
    rows: [
        {
            Name: "Donor Committee for Enterprise Development",
            Participating Org Ref: null,
            Type: "10",
            Role: "2",
            CRS Channel Code: "21000"
        }
    ],
    time: 0.007
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display `participating-org` with their `@role`, `@ref`, `@type` and include associated transaction types.

For this particular dataset, the name of the participating organisation was not published within a `narrative` element, as recommended in the latest version of the IATI Standard (2.03 at the time of writing) so we've had to use an empty string `''` for the element we want to display. Otherwise, we would have used `/narrative` in the query.

We've also had to join the `transaction` and `participating-org` tables for this query as both of those elements can occur multiple times.  
Any element occurring multiple times is turned into an array.

Here we've used the comma `,` shorthand to do a full join or a full outer join (both mean the same thing; they return all rows from the tables).

```sql
from xson as x , 
jsonb_array_elements(xson -> '/transaction') as tx , 
jsonb_array_elements(xson -> '/participating-org') as p
```

can be also be read as

```sql
from xson as x
join jsonb_array_elements(xson -> '/transaction') as tx
join jsonb_array_elements(xson -> '/participating-org') as p
```

We use `jsonb_array_elements()` when we want to look at arrays as it expands a JSON array to a set of JSON elements; essentially flattening an array so that it is possible to output the results as a csv file that we can convert into pivot tables.

```sql
select
p  ->> '' as "Participating Org" ,
p  ->> '@ref' as "ref",
p  ->> '@role' as "role",
p  ->> '@type' as "type",
tx ->> '/transaction-type@code' as "Transaction type"
from xson as x , 
jsonb_array_elements(xson -> '/transaction') as tx , 
jsonb_array_elements(xson -> '/participating-org') as p
where x.root = '/iati-activities/iati-activity' group by p,5
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            Participating Org: "Abu Dhabi Department of Finance",
            ref: "AE-2",
            role: "3",
            type: null,
            Transaction type: "3"
        }
    ],
    time: 16.211
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display number of `participating-org` found in activities and then display the activity with the most

Here we use `JSONB_ARRAY_LENGTH` because `participating-org` can occur multiple times and is therefore, an array.

We also need to group this query by `participating-org` so that we can get an aggregate of the numbers returned.

```sql
select JSONB_ARRAY_LENGTH(xson->'/participating-org')
from xson where root='/iati-activities/iati-activity'
group by 1
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            jsonb_array_length: 1
        },
        {
            jsonb_array_length: 2
        },
        ...
        {
            jsonb_array_length: 203
        },
        {
            jsonb_array_length: null
        }
    ],
    time: 7.423
}
```

*Note: The example above has been truncated at `...` to reduce space.*

From the results, we can see that the most number of `participating-org` you can find in an activity is 203.

Now we can display the activity with the most number of `participating-org` and view that activity by clicking the 'View on d-portal' button.

```sql
select aid
from xson where root='/iati-activities/iati-activity'
and JSONB_ARRAY_LENGTH(xson->'/participating-org')='203'
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            aid: "XM-DAC-21018-MDRHT008"
        }
    ],
    time: 18.859
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display all `participating-org` names, grouped by `@ref`

There are millions of organisations listed in IATI data and each have their own unique reference identifiers.

An organisation can have many different `@ref` identifiers, depending on who is publishing the data.

It can be useful, for instance, to find out if your organisation is given the same `@ref` by different publishers.

In the example below, we display the Publisher (`pid`) so that we know who has published this data, the first `/narrative` as this is usually the `participating-org` name, and the `@ref` identifier as well as other useful elements like the `@role` and `@type` so we know what role they played in the activity and what type of organisation they are.

We then group by `@ref` so that rows that have the same values are grouped together, followed by the names of the organisation and so forth.  
`@ref` is the 3rd column (there are 5 columns in total in this query).

```sql
select
pid,
xson  -> '/narrative'->0->>'' as "Participating Org" ,
xson  ->> '@ref' as "ref",
xson  ->> '@role' as "role",
xson  ->> '@type' as "type"
from xson
where root = '/iati-activities/iati-activity/participating-org'
group by 3,2,4,5,1
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            pid: "ES-DIR3-EA0035768",
            Participating Org: "ACCIONA MICROENERGÍA PANAMÁ",
            ref: "#",
            role: "2",
            type: null
        }
    ],
    time: 5.93
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display the `participating-org@ref` with the most narratives

This query gets you the reference number with the most narratives.

Usually it is better to ignore `null` results but in this case, to get a clearer picture of all data published, it is important to highlight **data issues** published in this element.

Publishing `null` as reference numbers will make it harder for data consumers to attempt traceability analysis and networked data, and thus accountablity for all organisations involved.

```sql
select ref,
count(*)
from (

    select
    xson->>'@ref' as ref ,
    xson->'/narrative'->0->>'' as narrative ,
    count(*)
    from xson where root='/iati-activities/iati-activity/participating-org' 
    group by 1,2
    
) as q1

group by 1
order by 2 desc

limit 1;
```

Result

```jsonc
{
    rows: [
        {
            ref: null,
            count: "103665"
        }
    ],
    time: 17.233
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display all `narrative` grouped by `participating-org@ref`

This query displays all the narratives found for all participating-org reference numbers.

Grouping the query by `@ref` makes it possible to view all the different narratives per organisation.

When an organisation publishes a report, there are various ways to spell a name of an organisation.  
IATI does not have an official list so it is useful to know what has been published to get a sense of what names are being used out there.

In this example, the `@ref` number XM-DAC-47066 has been published with 20 different names by different organisations in their activities data.  
The [standard states](https://iatistandard.org/en/iati-standard/203/activity-standard/iati-activities/iati-activity/participating-org/narrative/) that you can publish as many narratives as you want for an organisation name because it might differ depending on language.  
This explains some of the different names listed below but not all.

From the data we've seen, and as per the example below, it is usually a different combination of lower and uppercase letters that account for the different names.

```sql
select
ref,
count(*),
array_to_string(array_agg(narrative), '
') as narrative
from (
    
    select
    xson->>'@ref' as ref ,
    xson->'/narrative'->0->>'' as narrative ,
    count(*)
    
    from xson where root='/iati-activities/iati-activity/participating-org' 
    and xson->'/narrative'->0->>'' is not null
    and xson->>'@ref' is not null
    
    group by 1,2

) as q1

group by 1
order by 2 desc

limit 1;
```

Result

```jsonc
{
    rows: [
        {
			ref: "XM-DAC-47066",
			count: "20",
			narrative: "******
			International Organisation for Migration
			International Organisation for Migration (IOM)
			International Organization for Migration
			INTERNATIONAL ORGANIZATION FOR MIGRATION
			International Organization for Migration, China
			INTERNATIONAL ORGANIZATION FOR MIGRATION (INT)
			International Organization for Migration (IOM)
			International Organization for Migration, Regional Office for West and Central Africa, Senegal
			International Organization for Migration, Sri Lanka
			Int'l Organization for Migration
			IOM
			IOM Development Fund
			IOM (International Organisation for Migration)
			IOM (International Organization for Migration)
			IOM – International Organization for Migration
			IOM - ORGANIZZAZIONE INTERNAZIONALE PER LE MIGRAZIONI
			Organisation internationale des migrations
			Private Individual Donations
			USAID - U.S. Agency for International Development"
        },
    ],
    time: 8.885
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display all narratives and their count for a specific `participating-org@ref`

This query gets you all the narratives for one reference number and the number of times that narrative is found in the database.

```sql
select
xson->>'@ref' as participating_org_ref, 
xson->'/narrative'->0->>'' as narrative, 
count(*)

from xson where root='/iati-activities/iati-activity/participating-org' 
and xson->>'@ref'='GB-COH-213890'

group by
narrative,
participating_org_ref

order by 3 desc
limit 100;
```

Result

```jsonc
{
    rows: [
        {
            participating_org_ref: "GB-COH-213890",
            narrative: "Save The Children UK",
            count: "2286"
        },
        {
            participating_org_ref: "GB-COH-213890",
            narrative: "Save the Children UK",
            count: "564"
        },
        {
            participating_org_ref: "GB-COH-213890",
            narrative: "Save the Children",
            count: "23"
        },
        {
            participating_org_ref: "GB-COH-213890",
            narrative: "Save the Children UK (Start Fund)",
            count: "22"
        },
        {
            participating_org_ref: "GB-COH-213890",
            narrative: "Save the Children (Start Fund)",
            count: "20"
        },
        {
            participating_org_ref: "GB-COH-213890",
            narrative: "HET NEDERLANDSE RODE KRUIS (NLD)",
            count: "16"
        },
        {
            participating_org_ref: "GB-COH-213890",
            narrative: "Elrha (Hosted by Save the Children UK)",
            count: "7"
        },
        {
            participating_org_ref: "GB-COH-213890",
            narrative: "Save the Children Fund (SCUK)",
            count: "2"
        },
        {
            participating_org_ref: "GB-COH-213890",
            narrative: "Save the Children Mozambique",
            count: "1"
        },
        {
            participating_org_ref: "GB-COH-213890",
            narrative: "Save the Children Fund",
            count: "1"
        },
        {
            participating_org_ref: "GB-COH-213890",
            narrative: "Save the Children (Start Network)",
            count: "1"
        },
        {
            participating_org_ref: "GB-COH-213890",
            narrative: "Save the Children Ethiopia",
            count: "1"
        },
        {
            participating_org_ref: "GB-COH-213890",
            narrative: "Save The Children",
            count: "1"
        },
        {
            participating_org_ref: "GB-COH-213890",
            narrative: "SAVE",
            count: "1"
        }
    ],
    time: 6.761
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

```jsonc
{
    rows: [
        {
            org_id: "XM-DAC-41122",
            count: "14359"
        },
    ],
    time: 0.493
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

```jsonc
{
    rows: [
        {
            narrative: "[{\"\": \"Should contribute directly to results for children as outlined in the UNDAF, national results and/or UNICEF Strategic Plan and within the scope defined in the CPD, CPAP, OMP or other agreed upon programme documents\", \"@xml:lang\": \"EN\"}]",
            condition_type: "1",
            org_id: "XM-DAC-41122",
            count: "14359"
        },
    ],
    time: 0.55
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

```jsonc
{
    rows: [
        {
            narrative: "[{\"\": \"Should contribute directly to results for children as outlined in the UNDAF, national results and/or UNICEF Strategic Plan and within the scope defined in the CPD, CPAP, OMP or other agreed upon programme documents\", \"@xml:lang\": \"EN\"}]",
            org: "XM-DAC-41122",
            count: "14359"
        },
    ],
    time: 1.696
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display a list of changed IATI organisation identifiers
Raised https://github.com/IATI/ckanext-iati/issues/218#issuecomment-696289896

We can list old and new identifiers of various types by looking in the `other-identifier` element.  

In this example, we are looking for [Previous Reporting Organisation Identifier](https://iatistandard.org/en/iati-standard/203/codelists/otheridentifiertype/) `B1` and listing both their old and new identifier, along with the number of times this occurs in the data.

We are only looking for identifiers which have changed so we add `and xson->>'@ref' != pid` to the query as publishers can (and do) report any data, regardless of their state.

[View this query](http://d-portal.org/dquery/#select%20xson-%3E%3E'@ref'%20as%20old_id,%20pid%20as%20new_id,%20count(*)%0Afrom%20xson%20where%20root='/iati-activities/iati-activity/other-identifier'%0Aand%20xson-%3E%3E'@type'%20=%20'B1'%0Aand%20xson-%3E%3E'@ref'%20!=%20pid%0Agroup%20by%20xson-%3E%3E'@ref',%20pid%0Aorder%20by%203%20desc%0Alimit%201;) on dQuery.

```sql
select xson->>'@ref' as old_id, pid as new_id, count(*)
from xson where root='/iati-activities/iati-activity/other-identifier'
and xson->>'@type' = 'B1'
and xson->>'@ref' != pid
group by xson->>'@ref', pid
order by 3 desc
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            old_id: "US-GOV",
            new_id: "US-USAGOV",
            count: "279337"
        },
    ],
    time: 2.415
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display a list of changed IATI activity identifiers
Raised https://github.com/IATI/ckanext-iati/issues/279#issuecomment-913618337

We can list old and new identifiers of various types by looking in the `other-identifier` element.  

In this example, we are looking for [Previous Activity Identifier](https://iatistandard.org/en/iati-standard/203/codelists/otheridentifiertype/) `A3` and listing both their old and new identifier, along with the number of times this occurs in the data.

We are only looking for identifiers which have changed so we add `and aid != xson->>'@ref'` to ignore old and new identifiers that remain the same.

[View this query](http://d-portal.org/dquery/#select%20xson-%3E%3E'@ref'%20as%20old_id,%20aid%20as%20new_id,%20count(*)%0Afrom%20xson%20where%20root='/iati-activities/iati-activity/other-identifier'%0Aand%20xson-%3E%3E'@type'%20=%20'A3'%0Aand%20aid%20!=%20xson-%3E%3E'@ref'%0Agroup%20by%20xson-%3E%3E'@ref',%20aid%0Aorder%20by%203%20desc%0Alimit%201;) on dQuery.

```sql
select xson->>'@ref' as old_id, aid as new_id, count(*)
from xson where root='/iati-activities/iati-activity/other-identifier'
and xson->>'@type' = 'A3'
and aid != xson->>'@ref'
group by xson->>'@ref', aid
order by 3 desc
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            old_id: "41140",
            new_id: "XM-DAC-41140-200119",
            count: "1"
        }
    ],
    time: 0.274
}
```

Further to this, you can ask dquery to ignore any identifiers.  
In this instance, the ignored identifier comes from a single publisher that is reporting an previous organisation identifier instead of an activity identifier.

You can consider this a data quality issue as it will not register as invalid by various validator tools currently out there.  
This is one of many caveats of the IATI Standard - knowing when and where data could be invalid even when they are considered technically valid.

[View this query](http://d-portal.org/dquery/#select%20xson-%3E%3E'@ref'%20as%20old_id,%20aid%20as%20new_id,%20count(*)%0Afrom%20xson%20where%20root='/iati-activities/iati-activity/other-identifier'%0Aand%20xson-%3E%3E'@type'%20=%20'A3'%0Aand%20xson-%3E%3E'@ref'%20!=%20'41140'%0Agroup%20by%20xson-%3E%3E'@ref',%20aid%0Aorder%20by%203%20desc%0Alimit%201;) on dQuery.

```sql
select xson->>'@ref' as old_id, aid as new_id, count(*)
from xson where root='/iati-activities/iati-activity/other-identifier'
and xson->>'@type' = 'A3'
and xson->>'@ref' != '41140'
group by xson->>'@ref', aid
order by 3 desc
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            old_id: "47122-0205-CIV-02-Y",
            new_id: "47122-CIV-ISS",
            count: "1"
        }
    ],
    time: 0.327
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

```jsonc
{
    rows: [
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
    time: 0.007
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display identifiers of activities that are published in version 1 of the standard

Since there are various versions of 1, we use `like` and `%` after the number to account for it.

```sql
select aid
from xson
where root='/iati-activities/iati-activity'
and xson->>'@iati-activities:version' like '1%'
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            aid: "GB-CHC-288701-IN110"
        }
    ],
    time: 0.216
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Find the activity with most number of `narrative` in an element

For this query, we wanted to find the activity that has the most number of `narrative` elements (arrays) in a single field.  
The activity, at the time of query, has 63 `narrative` elements in a single `participating-org`.

This is a rare occurance and an example of a **data issue**.

In this context, `narrative` is being used as multiple `participating-org` and not a freetext name for the same organisation in different languages, as [stated in the standard](https://iatistandard.org/en/iati-standard/203/activity-standard/iati-activities/iati-activity/participating-org/narrative/).

You can order by `aid` and `desc` as well.

```sql
select

aid , JSONB_ARRAY_LENGTH(xson->'/narrative')

from xson where JSONB_ARRAY_LENGTH(xson->'/narrative') is not null

order by 2 desc
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            aid: "SE-ON-802005-9823-LA-500399",
            jsonb_array_length: 63
        }
    ],
    time: 13.104
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display number of transactions you can find in an activity

Here we use `jsonb_array_length` to find how many arrays are present in a specified element.

In this example, we can see that the most number of transactions you can find in the data is 10517.
```sql
select JSONB_ARRAY_LENGTH(xson->'/transaction')
from xson where root='/iati-activities/iati-activity'
group by 1
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            jsonb_array_length: 1
        },
        {
            jsonb_array_length: 215
        },
        {
            jsonb_array_length: 10517
        },
        {
            jsonb_array_length: null
        }
    ],
    time: 5.479
}
```

We can then find that activity by using `JSONB_ARRAY_LENGTH(xson->'/transaction')` and giving it a value.

```sql
select aid
from xson where root='/iati-activities/iati-activity'
and JSONB_ARRAY_LENGTH(xson->'/transaction')='10517'
limit 10;
```

Result

```jsonc
{
    rows: [
        {
            aid: "GB-CHC-1045348-Families' Empowerment Project"
        }
    ],
    time: 18.991
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display active projects grouped by country

This query lists the number of active projects grouped by `recipient-country` using the `act` table.

Publishers can sometimes report invalid countries like '999' or even leave this field empty so this query can really highlight this **data issue**.

On d-portal, we do a number of calculations to determine if an activity is still active.  
We ignore the `activity-status` as this is usually inaccurate or not up-to-date.

Instead, we use a mixture of start and end dates in relation to today's date using the `epoch from now()` PostgreSQL function.

```sql
select count(distinct aid) as count, country_code
from act join country using (aid) where
    
    day_start <= floor(extract(epoch from now())/(60*60*24)) and
    (day_end >= floor(extract(epoch from now())/(60*60*24)) or day_end is null) and  
    day_length is not null
    
group by country_code
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            count: "33",
            country_code: "KN"
        }
    ],
    time: 0.052
}
```

You can add the following to order the list by the most number of activities at the top.

```sql
order by count desc
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Freetext search for activities starting in year 2022

For this query, we use an inner join to search within certain parameters.

In this case, for a freetext search for `cookies`, we want only activities that took place from the start of 2022 to before the start of 2023.

```sql
SELECT DISTINCT x1.aid FROM (

/* Date starts from 2022 only */
select aid from act where
    day_start > ((2022-1970)*365.25) and
    day_start < ((2023-1970)*365.25)

) AS x1

INNER JOIN

(

/* Narrative search in all description */
SELECT aid FROM xson WHERE to_tsvector('simple', xson->>'') @@ to_tsquery('simple','''cookies''')

) AS x2 ON x1.aid=x2.aid

limit 1;
```

Result

```jsonc
{
    rows: [
        {
            aid: "US-EIN-300108263-GGProj:55652"
        }
    ],
    time: 0.45
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Count active projects from 2021 onwards

This query adds a regex check to make sure the `@iso-date` is valid instead of throwing up an error when unexpected data is found.  
An example of this is when we find text in `@iso-date`; ie. "Inception start date 28/10/2016".

We only want activities that have a planned start date (`@type` is 1).  
We use `distinct` so we do not double count any activities.

[View this query](https://d-portal.org/dquery/#SELECT%20count%28distinct%20aid%29%20FROM%0A%28%0A%20%20%20%20SELECT%20aid,%20%20%28regexp_matches%28%20xson-%3E%3E%27@iso-date%27%20,%20%27%5Cd%7B4%7D-%5Cd%7B2%7D-%5Cd%7B2%7D%27%20%29%29%5B1%5D::DATE%20AS%20startdate%0A%20%20%20%20FROM%20xson%0A%20%20%20%20WHERE%0A%20%20%20%20%20%20%20%20root=%27/iati-activities/iati-activity/activity-date%27%0A%20%20%20%20AND%0A%20%20%20%20%20%20%20%20xson-%3E%3E%27@type%27=%271%27%0A%20%20%20%20%20%20%20%20%0A%29%20AS%20aiddates%20WHERE%20aiddates.startdate%20%3E%20%272021-01-01%27::DATE%0A%0A) on dQuery.

```sql
SELECT count(distinct aid) FROM
(
    SELECT aid,  (regexp_matches( xson->>'@iso-date' , '\d{4}-\d{2}-\d{2}' ))[1]::DATE AS startdate
    FROM xson
    WHERE
        root='/iati-activities/iati-activity/activity-date'
    AND
        xson->>'@type'='1'
       
) AS aiddates WHERE aiddates.startdate > '2021-01-01'::DATE
```

Result

```jsonc
{
    rows: [
        {
            count: "66843"
        }
    ],
    time: 10.258
}
```

The following uses the `epoch` option to count all activities that start in 01 January 2021.

```sql
SELECT count(aid) FROM xson WHERE root = '/iati-activities/iati-activity'
AND aid IN
(
  SELECT aid from act WHERE day_start >= FLOOR(EXTRACT(epoch FROM '2021-01-01'::DATE)/(60*60*24))
)
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            count: "82366"
        }
    ],
    time: 5.735
}
```

The following uses the `epoch` option, groups the results by activity and sorts the activities by date and description.

This is an expensive and large query asking for activity identifiers and its details, so adding a `limit` will help the server and your browser. Otherwise, it will attempt to list all 82,366 results!

```sql
SELECT * FROM xson WHERE root = '/iati-activities/iati-activity'
AND aid IN
(
  SELECT aid from act WHERE day_start >= FLOOR(EXTRACT(epoch FROM '2021-01-01'::DATE)/(60*60*24))
  GROUP by aid
  ORDER BY day_start, aid desc
)
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            aid: "ZW-ROD-MA0000405/2015-4000004082",
            pid: "ZW-ROD-MA0000405/2015",
            root: "/iati-activities/iati-activity",
            xson: {25 items}
        }
    ],
    time: 0.732
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display location data for active projects for a country

We want location data, specifically the longitude and latitude, so that we can display these activities on a map.

Using the `act` table, this query also includes the IATI activity identifier and title.

```sql
select * from (
select distinct aid, title
from act join country using (aid) where
    
    day_start <= floor(extract(epoch from now())/(60*60*24))  and
    (day_end >= floor(extract(epoch from now())/(60*60*24)) or day_end is null)  and  
    day_length is not null
    
and country_code='af'
) as q1 join location using (aid)
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            aid: "41119-AF-O1-RT",
            title: "UNFPA Afghanistan Improved programming for results activities",
            location_code: null,
            location_gazetteer_ref: null,
            location_gazetteer: null,
            location_name: "Afghanistan",
            location_longitude: 67.679,
            location_latitude: 33.93,
            location_precision: null,
            location_percent: null
        }
    ],
    time: 0.099
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display `@percentage` reported for `recipient-country`, starting with the lowest number

This query lets you find the lowest `/recipient-country@percentage` published in the data.  

```sql
select xson->>'@code' as code, xson->>'@percentage' as percentage
from xson
where root='/iati-activities/iati-activity/recipient-country'
order by 2 asc
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            code: "KE",
            percentage: "0"
        }
    ],
    time: 2.845
}
```

I can tweak the query slightly to only display data with `@percentage` greater than 0 as this is more useful.

```sql
select xson->>'@code' as code, xson->>'@percentage' as percentage
from xson
where root='/iati-activities/iati-activity/recipient-country'
and xson->>'@percentage' > '0'
order by 2 asc
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            code: "TR",
            percentage: 0.0084
        }
    ],
    time: 0.007
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

```jsonc
{
    rows: [
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
    time: 3.428
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Diplay list of Publishers reporting SDG Goals and Targets

There are a few ways publishers can report these activities by using the `tag`, `sector`, `transaction` and `result` element.
We could combine them all, like we do with the complex COVID-19 query but here they are individually, in various ways, so it's easier to understand them with context.

The following query will display publishers that report UN Sustainable Development Goals (SDG) and UN SDG Targets in the `tag` element.

This query will also list the publisher with the most number of activities.

[View this query](http://d-portal.org/dquery/#select%20count(distinct%20aid)%20as%20%22Number%20of%20activities%22,%20pid%20as%20%22Publisher%22%0A%0Afrom%20xson%20where%20root='/iati-activities/iati-activity/tag'%20%0Aand%20xson-%3E%3E'@vocabulary'%20in%20('2','3')%0A%0Agroup%20by%202%0Aorder%20by%201%20desc%0A%0Alimit%201;%0A) on dQuery.

```sql
select count(distinct aid) as "Number of activities", pid as "Publisher"

from xson where root='/iati-activities/iati-activity/tag' 
and xson->>'@vocabulary' in ('2','3')

group by 2
order by 1 desc

limit 1;
```

Result

```jsonc
{
    rows: [
        {
            Number of activities: "21144",
            Publisher: "XM-DAC-41114"
        }
    ],
    time: 0.825
}
```

The following query will display publishers that report UN Sustainable Development Goals (SDG) and UN SDG Targets in the `sector` element.

This query will also list the number of activities per publisher, with the most at the top.

[View this query](http://d-portal.org/dquery/#select%20count(distinct%20aid)%20as%20%22Number%20of%20activities%22,%20pid%20as%20%22Publisher%22%0A%0Afrom%20xson%20where%20root='/iati-activities/iati-activity/sector'%20%0Aand%20xson-%3E%3E'@vocabulary'%20in%20('7','8')%0A%0Agroup%20by%202%0Aorder%20by%201%20desc%0A%0Alimit%201;) on dQuery.

```sql
select count(distinct aid) as "Number of activities", pid as "Publisher"

from xson where root='/iati-activities/iati-activity/sector' 
and xson->>'@vocabulary' in ('7','8')

group by 2
order by 1 desc

limit 1;
```

Result

```jsonc
{
    rows: [
        {
            Number of activities: "21218",
            Publisher: "XM-DAC-41114"
        }
    ],
    time: 1.656
}
```

You can go further and display just the identifiers of these activities so that you can explore that data on d-portal.  
Click 'View on d-portal' to explore these activities.

[View this query](http://d-portal.org/dquery/#select%20distinct%20aid%0A%0Afrom%20xson%20where%20root='/iati-activities/iati-activity/sector'%20%0Aand%20xson-%3E%3E'@vocabulary'%20in%20('7','8','9')%0A%0Alimit%2010;)

For this example, we have limited the query to 10 so we can get a list.

```sql
select distinct aid

from xson where root='/iati-activities/iati-activity/sector' 
and xson->>'@vocabulary' in ('7','8','9')

limit 10;
```

Result

```jsonc
{
    rows: [
        {
            aid: "XM-DAC-41114-PROJECT-00128417"
        },
        {
            aid: "XM-DAC-41122-Programme Division-456D/D0/09/102/026"
        },
        {
            aid: "NL-KVK-27378529-MAG15GH02"
        },
        {
            aid: XM-DAC-928-ET-2016-17-02.006.AF01.ETH02
        },
        {
            aid: XM-DAC-928-AM-2016-17-02.001.EU01.ARM01
        },
        {
            aid: "XM-DAC-41114-OUTPUT-00118486"
        },
        {
            aid: "XM-DAC-41114-OUTPUT-00112009"
        },
        {
            aid: "XM-DAC-41114-PROJECT-00059495"
        },
        {
            aid: "XM-DAC-41122-Nigeria-3210/A0/04/101/002"
        },
        {
            aid: "XM-DAC-41114-PROJECT-00131086"
        }
    ],
    time: 0.575
}
```

The following query will display publishers that report UN SDG Goals, Targets and Indicators in the `transaction/sector` element.  
Click 'View on d-portal' to explore these activities.

This query will also list the identifiers of these activities.

[View this query](http://d-portal.org/dquery/#select%20count(distinct%20aid)%20as%20%22Number%20of%20activities%22,%20pid%20as%20%22Publisher%22,%20aid%0A%0Afrom%20xson%20where%20root='/iati-activities/iati-activity/transaction/sector'%20%0Aand%20xson-%3E%3E'@vocabulary'%20in%20('7','8','9')%0A%0Agroup%20by%202,%203%0Aorder%20by%201%20desc%0A%0Alimit%201;)

```sql
select pid as "Publisher", aid

from xson where root='/iati-activities/iati-activity/transaction/sector' 
and xson->>'@vocabulary' in ('7','8','9')

group by 1, 2

limit 1;
```

Result

```jsonc
{
    rows: [
        {
            Publisher: "GB-EDU-133784",
            aid: "GB-EDU-133784-EquiTrauma130036"
        }
    ],
    time: 0.473
}
```

If we just want to display number of activities per publisher, with the most at the top, we can use this query.

```sql
select count(distinct aid) as "Number of activities", pid as "Publisher"

from xson where root='/iati-activities/iati-activity/transaction/sector' 
and xson->>'@vocabulary' in ('7','8','9')

group by 2
order by 1 desc

limit 1;
```

Result

```jsonc
{
    rows: [
        {
            Number of activities: "1",
            Publisher: "GB-CHC-1110434"
        }
    ],
    time: 0.559
}
```

The following query will display publishers that report UN SDG Indicators in the `result/indicator/reference` element.

This query will also list the number of activities per publisher, with the most at the top.

[View this query](http://d-portal.org/dquery/#select%20count(distinct%20aid)%20as%20%22Number%20of%20activities%22,%20pid%20as%20%22Publisher%22%0A%0Afrom%20xson%20where%20root='/iati-activities/iati-activity/result/indicator/reference'%20%0Aand%20xson-%3E%3E'@vocabulary'%20in%20('9')%0A%0Agroup%20by%202%0Aorder%20by%201%20desc%0A%0Alimit%201;)

```sql
select count(distinct aid) as "Number of activities", pid as "Publisher"

from xson where root='/iati-activities/iati-activity/result/indicator/reference' 
and xson->>'@vocabulary' in ('9')

group by 2
order by 1 desc

limit 1;
```

Result

```jsonc
{
    rows: [
        {
            Number of activities: "39",
            Publisher: "NL-KVK-40409352"
        }
    ],
    time: 0.06
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Diplay `sum` of all transaction types that are `@code` 3
```sql
select sum( cast(xson->>'/value' AS real) ) as "Sum of all Disbursments in USD"
from xson where root='/iati-activities/iati-activity/transaction'
and xson->>'/transaction-type@code'='3'
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            Sum of all Disbursements in USD: 485037470000
        }
    ],
    time: 6.617
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display activities with more than 10 `actual` values reported in results data

We also want to display the publisher and order the results with the most at the top.

```sql
select aid, pid
from xson
where root='/iati-activities/iati-activity/result/indicator/period'
and jsonb_array_length(xson->'/actual') > 10
group by 1, 2
order by 2 desc
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            aid: "NL-KVK-41160054-112859",
            pid: "NL-KVK-41160054"
        }
    ],
    time: 0.654
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display activities with more than 1 comment reported in results data

We also want to display the comments, the activity identifier and order the results with the most at the top.

```sql
select jsonb_array_length(xson -> '/comment/narrative') as "Comment", aid, xson
from xson
where root='/iati-activities/iati-activity/result/indicator/period/actual'
and jsonb_array_length(xson -> '/comment/narrative') > 1
order by 1 desc
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            Comment: 2,
            aid: "47045-SEN-H-ANCS",
            xson: {
                /dimension: [
                    {
                        @name: "Age",
                        @value: "<25"
                    }
                ],
                /comment/narrative: [
                    {
                        : "Numerator: 19.1",
                        @xml:lang: "EN"
                    },
                    {
                        : "Target for: 2018",
                        @xml:lang: "EN"
                    }
                ]
            }
        }
    ],
    time: 1.448
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

```jsonc
{
    rows: [
        {
            aid: "44000-P150481",
            pid: "44000",
            root: "/iati-activities/iati-activity",
            xson: {
                /budget: [144 items],
                /rows: [24 items],
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
    time: 0.029
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

### Display full activity data with attribute of certain value

```sql
select *
from xson where root='/iati-activities/iati-activity/result/indicator'
and xson->>'@ascending'='0'
limit 1;
```

Result

```jsonc
{
    rows: [
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
    time: 0.013
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

```jsonc
{
    rows: [
        {
            aid: "44000-P150481"
        }
    ],
    time: 0.017
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

```jsonc
{
    rows: [
        {
            code: null,
            count: "28222"
        }
    ],
    time: 0.131
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

## Find humanitarian activities at activity level for multiple countries

Use `distinct` to *only* count unique activities to avoid double counting.

Replace `count(distinct aid)` with `distinct aid` to get a list of identifiers instead.

```sql
SELECT count(distinct aid) FROM xson WHERE
root = '/iati-activities/iati-activity' AND
xson->>'@humanitarian' = '1' AND
aid in (
    SELECT aid FROM xson WHERE
    root = '/iati-activities/iati-activity/recipient-country' AND
    xson->>'@code' IN ( 'UG' , 'BD' , 'HN' )
)
```

Result

```jsonc
{
    rows: [
        {
            count: "2589"
        }
    ],
    time: 1.462
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

## Freetext search in transaction narrative for multiple countries

Use `distinct` to *only* count unique activities to avoid double counting.

Replace `count(distinct aid)` with `distinct aid` to get a list of identifiers instead.

```sql
SELECT count(distinct aid) FROM xson WHERE
root='/iati-activities/iati-activity/transaction/description/narrative' AND
to_tsvector('simple', xson->>'') @@ to_tsquery('simple','''climate change''') AND
aid in (
    SELECT aid FROM xson WHERE
    root = '/iati-activities/iati-activity/recipient-country' AND
    xson->>'@code' IN ( 'UG' , 'BD' , 'HN' )
)
```

Result

```jsonc
{
    rows: [
        {
            count: "5"
        }
    ],
    time: 1.294
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

## Freetext search on humanitarian activities including transactions for multiple countries

Use `distinct` to *only* count unique activities to avoid double counting.

Replace `count(distinct x1.aid)` with `distinct x1.aid` to get a list of identifiers instead.

```sql
SELECT COUNT(DISTINCT x1.aid) FROM (

/* The transaction is humanitarian and goes to any of the countries */
SELECT aid FROM xson WHERE
    root = '/iati-activities/iati-activity/transaction' AND
    xson->>'@humanitarian' = '1' AND
    xson->>'/recipient-country@code' IN ( 'UG' , 'BD' , 'HN' )

UNION

/* The activity is humanitarian and lists any of the countries */
SELECT aid FROM xson WHERE
    root = '/iati-activities/iati-activity' AND
    xson->>'@humanitarian' = '1' AND
    aid in (
        SELECT aid FROM xson WHERE
        root = '/iati-activities/iati-activity/recipient-country' AND
        xson->>'@code' IN ( 'UG' , 'BD' , 'HN' )
    )

) AS x1

INNER JOIN

(

/* Narrative search in all descriptions */
SELECT aid FROM xson WHERE
to_tsvector('simple', xson->>'') @@ to_tsquery('simple','''coffee''')

) AS x2 ON x1.aid=x2.aid
```

Result

```jsonc
{
    rows: [
        {
            count: "4"
        }
    ],
    time: 4.143
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

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

```jsonc
{
    rows: [
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
    time: 2.411
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

## Flattening `document-link` with higher level elements in `iati-activity`
Raised https://github.com/codeforIATI/iati-ideas/issues/26

This is a complex query that references the same table twice due to the nature of IATI hierarchical data.  
In this case, the request was to include data outside the `document-link` element.

We recommend against queries like these as they can be intensive but we are including it here to show that it is possible.

For such in-depth queries, we recommend [spinning up your own server to use locally](#doughnut-database-dump).

```sql
select

x2.pid as publisher,
x2.aid as activity,

x2.xson -> '/reporting-org/narrative'->0->>'' as reporting_org,
x2.xson ->> '/reporting-org@ref' as reporting_ref,
x2.xson ->> '/reporting-org@type' as reporting_type,
x2.xson -> '/recipient-country'->0->>'@code' as recipient,
x2.xson -> '/recipient-country'->0->>'@percentage' as recipient_percentage,
x2.xson -> '/sector'->0->>'@code' as sector,
x2.xson -> '/sector'->0->>'@vocabulary' as sector_vocab,
x2.xson -> '/sector'->0->>'@percentage' as sector_percentage,
x2.xson ->> '/activity-status@code' as status,

x1 -> '/title/narrative'->0->>'' as title,
x1 -> '/description/narrative'->0->>'' as description,
array_to_string(array( select x->>'@code' from jsonb_array_elements(x1 -> '/category') as x ),' ')  as category,
x1 -> '/language'->0->>'@code' as lang,
x1 ->> '/document-date@iso-date' as isodate,
x1 ->> '@format' as file,
x1 ->> '@url' as url

from xson as x2 , jsonb_array_elements(x2.xson -> '/document-link' )  as x1

where x2.root='/iati-activities/iati-activity' and x2.aid in
(
select aid from act order by aid limit 100000 offset 0
)

limit 1;
```

Result

```jsonc
{
    rows: [
        {
            publisher: "BD-NAB-1301",
            activity: 1301-4.0000,
            reporting_org: "Transparency International Bangladesh",
            reporting_ref: "BD-NAB-1301",
            reporting_type: "22",
            recipient: "BD",
            recipient_percentage: "100",
            sector: "15113",
            sector_vocab: "1",
            sector_percentage: "100",
            status: "2",
            title: "Organisation Website",
            description: null,
            category: "B16",
            lang: null,
            isodate: null,
            file: "APPLICATION/HTTP",
            url: https://www.ti-bangladesh.org/beta3/index.php/en/
        }
    ],
    time: 0.012
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

## Display unique `document-link@url`, publisher and activity identifier

```sql
select

max(pid) as publisher,
max(aid) as activity,

max(xson -> '/title/narrative'->0->>'') as title,
xson ->> '@url' as url

from xson where root='/iati-activities/iati-activity/document-link'

group by url
order by url

limit 1 offset 0;
```

Result

```jsonc
{
    rows: [
        {
            publisher: "US-EIN-11-3803281",
            activity: "US-EIN-11-3803281-ATF 2",
            title: "African Transformation Forum",
            url: http://acetforafrica.org/ATF/
        }
    ],
    time: 3.75
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

## Display a count of `document-link` reported by a publisher

```sql
select

pid as publisher,
count(*)

from xson where root='/iati-activities/iati-activity/document-link'
group by pid
order by 2 desc

limit 1;
```

Result

```jsonc
{
    rows: [
        {
            publisher: "US-USAGOV",
            count: "739161"
        }
    ],
    time: 3.327
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>


## Display all `reporting_ref` found in null publishers

```sql
select distinct reporting_ref
from act where reporting_ref not in
(
    select distinct pid
    from xson where root='/iati-activities/iati-activity' and pid is not null
)
limit 1;
```

Result

```jsonc
{
    rows: [
        {
            reporting_ref: "GH-DSW-4711 "
        }
    ],
    time: 3.719
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>


## Display `@last-updated-datetime` for activities in Somalia

```sql
select aid, xson->>'@last-updated-datetime' as updated
from xson where root='/iati-activities/iati-activity'
and aid in (
    select aid from xson where root='/iati-activities/iati-activity/recipient-country'
    and xson->>'@code' = 'SO'
)
limit 1;
```
```jsonc
{
    rows: [
        {
            aid: "41119-SO-O1-RT",
            updated: "2021-05-04T12:50:39"
        }
    ],
    time: 0.66
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>


## Ask PostgreSQL to do as they are told with `materialized`

PostgreSQL always tries to optimize your query and usually gets it wrong.  
Instead, we want it to do what we tell it to do.

So to be extra sure, specifically in situations where `is not null` is used as described below, we use `materialized` in our query.  

This tells PostgreSQL to follow the query as sequenced.

```sql
with q1 as MATERIALIZED (
select aid, xson->>'@last-updated-datetime' as updated
from xson where root='/iati-activities/iati-activity'
and xson @? '$."/recipient-country"[*]."@code" ? ( @ == "SO" )'
) select * from q1 where updated is not null and updated != ''
order by updated desc
```
```jsonc
{
    rows: [9310 items],
    time: 19.626
}
```

The example above uses the new PostgreSQL jsonb query but isn't as fast as the indexed tables that we already have in the database.

```sql
with q1 as MATERIALIZED (
select aid, xson->>'@last-updated-datetime' as updated
from xson where root='/iati-activities/iati-activity'
and aid in (
    select aid from xson where root='/iati-activities/iati-activity/recipient-country'
    and xson->>'@code' = 'SO'
)
) select * from q1 where updated is not null and updated != ''
order by updated desc
```
```jsonc
{
    rows: [9310 items],
    time: 4.289
}
```

<p align="right"><a href="#tada-introduction">To Top</a></p>

