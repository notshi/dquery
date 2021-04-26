# :tada: Introduction

http://d-portal.org/dquery/

dQuery lets you query **the complete IATI data, including non-standard attributes and extensions** directly into the live d-portal database, in a modern browser.

```diff
+ Documentation is ongoing so please bear with us.
```
dQuery works well if you are familiar with the IATI Standard elements and querying in SQL using JSONB data types in PostgreSQL. However, it shouldn't be too hard to pick up once you've done a few recipes.

## Tool

The current interface is sparse and consists of two panels; the editor on the left and the console on the right.  
There are four buttons above these panels which perform various things.

**Examples**  
Some example recipes.

**Run Query**  
View results in the browser.  
*If queries are complex, this can slow down the browser.*  

**Browse Activities**  
View results in d-portal.

**Download XSON**  
Download data in various formats without viewing results in the browser.

You can drag the partition to increase or decrease the space of either panel.  

## Data formats

Downloads of data is available as csv, json, xml and html.

The html option will only work at the top level of an activity or an organisation file; ie. when ```select *```, the root needs to be at either ```/iati-activities/iati-activity``` or ```/iati-organisations/iati-organisation```.

The html option is a Print-friendly version of SAVi (Simple Activity Viewer).

Replace ```/#``` in the url with ```?form=csv&sql=``` to get the query link in different formats.  

## Commands

You can more or less figure out what a query does by reading it like a sentence.  
The following table lists the most common SQL clauses and operators you can use to create queries.

| Commands | What it does |
| --- | ----------- |
| select | Picks fields that contain data of interest |
| from | For dQuery, this is usually ```xson``` |
| where | Limits the data set we are working with |
| and | Additional limits |
| or | Additional limits |
| not | Additional limits |
| like | Use this with ```%``` for wildcard queries |
| as | Use this to name columns |
| in | Allows you to specify multiple values |
| group by | Aggregates values across row |
| order by | Specifies sorting of results with option for ```asc``` or ```desc``` |
| limit | Limits the number of returned results |
| offset | Skips a given number of results |
| join | Get data from 2 or more tables |

## Select

The following table lists the most common commands using select.

| Commands | What it does |
| --- | ----------- |
| `*` | Returns full acitivity information, in their original order |
| count(`*`) | Returns a number of items |
| distinct aid | Returns a list of unique identifiers |
| aid | Returns a list of identifiers |
| pid | Returns a list of publishers |

#### Examples
```sql
select distinct aid 
```
```sql
select pid
```
```sql
select * 
```

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

## Displays a count of certain element or attributes
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

## Displays all ```iati-identifier``` with that search term using a wildcard ```%```
Raised https://github.com/codeforIATI/iati-ideas/issues/37

The wildcard ```%``` can be placed before, after or before and after a search term to get the following results.

```%<searchterm>``` looks for identifiers that end with the search term.  
```<searchterm>%``` looks for identifiers that start with the search term.  
```%<searchterm>%``` looks for identifiers that has the search term within it.
	
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

## Filtering on custom namespace elements
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

## Display iati-organisation id with elements within ```total-budget```
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

## Display all publishers that use a particular ```@ref```

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

## Display first ```/narrative``` array under multiple roots, count and grouped for a particular ```@ref```
Raised https://github.com/devinit/D-Portal/issues/602

```sql
select

xson->>'@ref' as reference, xson->'/narrative'->0->>'' as narrative, pid as publisher, count(*)

from xson where root in ('/iati-activities/iati-activity/participating-org','/iati-activities/iati-activity/transaction/provider-org','/iati-activities/iati-activity/transaction/receiver-org')
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

## Displays all publishers listing (GIZ) in ```participating-org/narrative``` with other elements and grouped  
Raised https://github.com/devinit/D-Portal/issues/613

Here we use ```LIKE``` and ```%%``` for wildcard SQL queries.

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

## Displays all publishers with ```conditions@attached``` as YES
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

## Displays the narratives grouped by publishers with ```condition@type```
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

## Displays narratives grouped by publishers with ```condition@type``` 1
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

## Display an element attribute, grouped in descending order
Use ```as``` to name the columns  
Order always depend on the column number or names, 3 being the count  
You can use multiple groups, as you would a table grouping columns

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

## Display information within an element
https://github.com/devinit/D-Portal/issues/620

Show all information within ```/budget``` for multiple identifiers.
This works for ```/budget``` as ```root``` because this array occurs only once.

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

## Display identifier only sorted by the second array (narrative) in descending order
You can order by ```aid``` and ```descending``` as well.

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


## Display number of items and include all activities information for certain element and vocab
```sql
select

JSONB_ARRAY_LENGTH(xson->'/narrative') , *

from xson where root='/iati-activities/iati-activity/sector' and xson->>'@vocabulary' = '99' and JSONB_ARRAY_LENGTH(xson->'/narrative') > 1

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

## Sub query to get full activity data
https://github.com/devinit/D-Portal/issues/562

Click on "Download XSON" and "Download XSON as XML" in the menu to get a link to run that query and return full activities as xml.

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

## Display all activities with attribute of certain value

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

## Display all activity identifiers with element attribute of certain value
https://github.com/devinit/D-Portal/issues/562

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

## Get a table of most used values sorted by count
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