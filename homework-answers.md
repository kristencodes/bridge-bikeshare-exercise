## Part 1

### 1. Write a query that fills out this table. Try your best to pick the correct station name for each ID. You may have to make some manual choices or editing based on the inconsistencies we've found. Do try to pick the correct name for each station ID based on how popular it is in the trip data.

_get ids that map to more than one station_

```
SELECT from_station_id, count(distinct from_station_name)
FROM trips
GROUP BY from_station_id
ORDER BY count desc
```

^ this query shows how many ids have multiple station names (they need to be manually edited) and how many have only one station name (these can be written straight to the stations table)

_write a query that inserts ids with only one associated station into the stations table_

```
 INSERT INTO stations(
  SELECT from_station_id as id, max(from_station_name) as name
  FROM trips
  GROUP BY from_station_id
  HAVING count(distinct from_station_name) = 1
  ORDER BY name desc
)
```

_Used this query to get all the ids with more than one station:_

```
SELECT
array_agg(
distinct from_station_name),
count(DISTINCT from_station_name) as ct, from_station_id from trips
WHERE from_station_id IS NOT NULL
group by from_station_id
HAVING COUNT(distinct from_station_name) > 1
ORDER BY ct desc
```

_but I could not figure out a way to order the `array_agg` by which name had the higher count. So instead_

```
  insert into stations(
  SELECT from_station_id as id, max(from_station_name) as name
  FROM trips
  WHERE from_station_id IS NOT NULL
  GROUP BY from_station_id
  HAVING count(distinct from_station_name) > 1
  ORDER BY name desc
  )
```

_I think this way at least took ONE of the station names associated with each id that had duplicates, and inserted that into stations._

### 2. Should we add any indexes to the stations table, why or why not?

_It might make sense to add an index to the ID in the stations table for quicker lookup by id._

### 3. Fill in the missing data in the trips table based on the work you did above
