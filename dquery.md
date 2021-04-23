## dQuery

http://d-portal.org/dquery/

dQuery allows you to query directly into the live d-portal database of **the complete** IATI data, including non-standard attributes and extensions. The following code snippets are some examples of what you can query using this tool.

Click *Run Query* to view results in the browser or download data in various formats without viewing.  
**The latter option is preferable as queries can slow down a browser.**

Downloads of data is available as csv, json, xml and html.

The html option will only work at the top level of an activity or an organisation file; ie. when ```select *```, the root needs to be at either ```/iati-activities/iati-activity``` or ```/iati-organisations/iati-organisation```.

The html option is a Print-friendly version of SAVi (Simple Activity Viewer).

Where applicable, results are limited to 1 as examples below.


## Displays a count of certain element or attributes with no limit
```
select

count(*)

from xson where root='/iati-organisations/iati-organisation'
and xson->>'/total-budget' IS NOT NULL

```

Result

```
{
    result: [
        {
            count: "437"
        }
    ],
    duration: 0.053
}
```

## Display iati-organisation id with elements within ```total-budget```
```
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

```
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

```
select

pid as org, count(*)

from xson where xson->>'@ref'='XM-OCHA-CBPF-NGA75'
group by pid

order by 2 desc

limit 1;
```

Result

```
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
```
select

xson->>'@ref' as reference, xson->'/narrative'->0->>'' as narrative, pid as publisher, count(*)

from xson where root in ('/iati-activities/iati-activity/participating-org','/iati-activities/iati-activity/transaction/provider-org','/iati-activities/iati-activity/transaction/receiver-org')
and xson->>'@ref'='XM-OCHA-CBPF-NGA75'
group by 1, 2, pid

order by 4 desc

limit 1;
```

Result

```
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

Here we use ```LIKE``` and ```%%``` for wildcard SQL queries.

```
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

```
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
```
select

pid as org_id, count(*)

from xson where root='/iati-activities/iati-activity' and xson->>'/conditions@attached' = '1'
group by pid

order by 2 desc

limit 1;
```

Result

```
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
```
select

xson->>'/narrative' as narrative, xson->>'@type' as condition_type, pid as org_id, count(*)

from xson where root='/iati-activities/iati-activity/conditions/condition'
group by xson->>'/narrative', pid, condition_type

order by 4 desc

limit 1;
```

Result

```
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
```
select

xson->>'/narrative' as narrative, pid as org, count(*)

from xson where root='/iati-activities/iati-activity/conditions/condition' and xson->>'@type' = '1'
group by xson->>'/narrative', pid

order by 3 desc

limit 1;
```

Result

```
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
```

select

xson->>'@ref' as old_id, pid as new_id, count(*)

from xson where root='/iati-activities/iati-activity/other-identifier' and xson->>'@type' = 'B1'
group by xson->>'@ref', pid

order by 3 desc

limit 1;

```

Result

```
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
Show all information within ```/budget``` for multiple identifiers.
This works for ```/budget``` as ```root``` because this array occurs only once.
```
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

```
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
```
select

aid , JSONB_ARRAY_LENGTH(xson->'/narrative')

from xson where JSONB_ARRAY_LENGTH(xson->'/narrative') is not null

order by 2 desc
limit 1;
```

Result

```
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
```
select

JSONB_ARRAY_LENGTH(xson->'/narrative') , *

from xson where root='/iati-activities/iati-activity/sector' and xson->>'@vocabulary' = '99' and JSONB_ARRAY_LENGTH(xson->'/narrative') > 1

order by 1 desc

limit 1;
```

Result

```
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
Click on "Download XSON" and "Download XSON as XML" in the menu to get a link to run that query and return full activities as xml.

```
SELECT * FROM xson WHERE root = '/iati-activities/iati-activity' AND  aid IN (
SELECT DISTINCT aid
FROM xson WHERE
    root = '/iati-activities/iati-activity/humanitarian-scope'
AND
    xson->>'@code' = 'EP-2020-000012-001'
)
limit 1;
```

## Display all activities with attribute of certain value

```
select *
from xson where root='/iati-activities/iati-activity/result/indicator'
and xson->>'@ascending'='0'
limit 1;
```

Result

```
{
    result: [
        {
            aid: "BE-BCE_KBO-0415365777-PROG2017-2021_SD1",
            pid: "BE-BCE_KBO-0415365777",
            root: "/iati-activities/iati-activity/result/indicator",
            xson: {
                /period: [
                    {
                        /actual: [
                            {
                                @value: "Discoursanalyse werd uitgevoerd",
                                /comment/narrative: [
                                    {
                                        : "Er is een geactualiseerde discoursanalyse die kijkt naar het discrous in kranten en magazines over de periode 2017 - 2018 en 2019. Hierbij zagen we een stijging in het aantal artikels die gepubliceerd werden over de drie kern thema's (eerlijke handel, leefbaar inkomen en zorgplicht). Naast de groeiende aandacht voor de thema's zien we ze ook meer en meer versmelten met elkaar. Deze trend werd eerst zichtbaar in 2018 en zette zich door in 2019. De kwalitatieve analyse over de jaren heen toont aan dat er meer urgentie gecreëerd wordt om het probleem te gaan aanpakken en dat hierbij aandacht gaat naar verschillende actoren maar dat daarbij de rol van de overheid ook steeds meer aan bod komt.",
                                        @xml:lang: "NL"
                                    },
                                    {
                                        : "There is an updated discours analysis. This showed us that there is more attention for our topics in the media as more articles were published that dealt with our priority themes (Fair Trade, Living Income and Human Rights Due Dilligence). In addition to this growing attention we see them more and more treated as elements of one and the same issue. We saw this trend first for the articles in 2018 and this continued in the articles for 2019. The analysis of the narrative of the articles indicated that these created more urgency to deal with the problems. The recongnized different actors that can contribute to the solution and amongst these there was a clear role for the government to contribute to the solution.",
                                        @xml:lang: "EN"
                                    }
                                ]
                            }
                        ],
                        /target: [
                            {
                                @value: "Actualisatie van de discoursanalyse",
                                /comment/narrative: [
                                    {
                                        : "Dit is een kwalitatieve indicator die aangeeft of een discoursanalyse werd uitgevoerd of geactualiseerd. waarbij 0 staat voor niet bereikt en 1 voor bereikt.",
                                        @xml:lang: "NL"
                                    }
                                ]
                            }
                        ],
                        /period-end@iso-date: "2019-12-31",
                        /period-start@iso-date: "2017-02-17"
                    },
                    {
                        /target: [
                            {
                                @value: "Actualisatie van de discoursanalyse"
                            }
                        ],
                        /period-end@iso-date: "2021-12-31",
                        /period-start@iso-date: "2017-02-17"
                    }
                ],
                @measure: "5",
                @ascending: 0,
                /title/narrative: [
                    {
                        : "Indicator 2 - Houding: Er is een geactualiseerde discoursanalyse binnen België over de spelregels van de internationale handelskader waaruit blijkt in hoeverre het belang aan een open en publiek debat gedeeld wordt.",
                        @xml:lang: "NL"
                    }
                ],
                @aggregation-status: 0
            }
        }
    ],
    duration: 0.006
}
```

## Display all activity identifiers with element attribute of certain value

This element may occur any number of times so the xpath has to be the root.  
Any element occurring multiple times is turned into an array.

```
SELECT DISTINCT aid
FROM xson WHERE
    root = '/iati-activities/iati-activity/humanitarian-scope'
AND
    xson->>'@code' = 'EP-2020-000012-001'
limit 1;
```

Result

```
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
```
SELECT xson->>'/humanitarian-scope@code' AS code , count(*)

FROM xson WHERE
    root='/iati-activities/iati-activity/humanitarian-scope'
AND
    xson->>'@code' IS NOT NULL

GROUP BY 1 ORDER BY 2 DESC

LIMIT 1;
```

Result

```
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
```
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

```
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