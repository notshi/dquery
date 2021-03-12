## Count
1. Displays a count of certain element or attributes with no limit
```
select

count(*)

from xson where root='/iati-organisations/iati-organisation'
and xson->>'/total-budget' IS NOT NULL

```

2. Display iati-organisation id with elements within ```total-budget```
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

```

3. Display all publishers that use a particular ```@ref```

```
select

pid as org, count(*)

from xson where xson->>'@ref'='XM-OCHA-CBPF-NGA75'
group by pid

order by 2 desc

limit 10;
```

4. Display first ```/narrative``` array under multiple roots, count and grouped for a particular ```@ref```
```
select

xson->>'@ref' as reference, xson->'/narrative'->0->>'' as narrative, pid as publisher, count(*)

from xson where root in ('/iati-activities/iati-activity/participating-org','/iati-activities/iati-activity/transaction/provider-org','/iati-activities/iati-activity/transaction/receiver-org')
and xson->>'@ref'='XM-OCHA-CBPF-NGA75'
group by 1, 2, pid

order by 4 desc

limit 10;
```


## Conditions
1. Displays all publishers with ```conditions@attached``` as YES, limit to 100
```
select

pid as org_id, count(*)

from xson where root='/iati-activities/iati-activity' and xson->>'/conditions@attached' = '1'
group by pid

order by 2 desc


limit 100;
```

2. Displays the narratives grouped by publishers with ```condition@type```
```
select

xson->>'/narrative' as narrative, xson->>'@type' as condition_type, pid as org_id, count(*)

from xson where root='/iati-activities/iati-activity/conditions/condition'
group by xson->>'/narrative', pid, condition_type

order by 4 desc


limit 100;
```

3. Displays narratives grouped by publishers with ```condition@type``` 1
```
select

xson->>'/narrative' as narrative, pid as org, count(*)

from xson where root='/iati-activities/iati-activity/conditions/condition' and xson->>'@type' = '1'
group by xson->>'/narrative', pid

order by 3 desc


limit 10;
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

limit 20000;

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

limit 100;
```

## Display identifier only sorted by the second array (narrative) in descending order
You can order by ```aid``` and ```descending``` as well.
```
select

 aid , JSONB_ARRAY_LENGTH(xson->'/narrative')

from xson where JSONB_ARRAY_LENGTH(xson->'/narrative') is not null

order by 2 desc
limit 100
```


## Display number of items and include all activities information for certain element and vocab
```
select

JSONB_ARRAY_LENGTH(xson->'/narrative') , *

from xson where root='/iati-activities/iati-activity/sector' and xson->>'@vocabulary' = '99' and JSONB_ARRAY_LENGTH(xson->'/narrative') > 1

order by 1 desc

limit 100;
```

## Sub query to get full activity data
Click on "Download XSON" and "Download XSON as XML" in the menu to get a link to run that query and return full activities as xml.

```
SELECT * FROM xson WHERE root = '/iati-activities/iati-activity' AND  aid IN (
SELECT DISTINCT aid
FROM xson WHERE
    root = '/iati-activities/iati-activity/transaction'
AND
    xson->>'/humanitarian-scope@code' = 'EP-2020-000012-001'
)
```

## Display all activities with attribute of certain value

```
select *
from xson where root='/iati-activities/iati-activity/result/indicator'
and xson->>'@ascending'='0'
limit 100;
```


## Display all activity identifiers with element attribute of certain value
```
SELECT DISTINCT aid
FROM xson WHERE
    root = '/iati-activities/iati-activity/transaction'
AND
    xson->>'/humanitarian-scope@code' = 'EP-2020-000012-001'
```

## Get a table of most used values sorted by count
```
SELECT xson->>'/humanitarian-scope@code' AS code , count(*)

FROM xson WHERE
    root='/iati-activities/iati-activity/transaction'
AND
    xson->>'/humanitarian-scope@code' IS NOT NULL

GROUP BY 1 ORDER BY 2 DESC

LIMIT 1000;
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
```