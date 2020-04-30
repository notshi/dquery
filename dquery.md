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