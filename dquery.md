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