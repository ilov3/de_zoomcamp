## Module 1 Homework

## Docker & SQL

## Question 1. Knowing docker tags

Run the command to get information on Docker 

Which tag has the following text? - *Automatically remove the container when it exits* 

- `--rm`


## Question 2. Understanding docker first run 

Run docker with the python:3.9 image in an interactive mode and the entrypoint of bash.
Now check the python modules that are installed ( use ```pip list``` ). 

What is version of the package *wheel* ?

`docker run --rm python:3.9 pip list | grep wheel`
- wheel package version - 0.42.0

# Prepare Postgres

Run Postgres and load data as shown in the videos
We'll use the green taxi trips from September 2019:

```wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/green/green_tripdata_2019-09.csv.gz```

You will also need the dataset with zones:

```wget https://s3.amazonaws.com/nyc-tlc/misc/taxi+_zone_lookup.csv```

Download this data and put it into Postgres (with jupyter notebooks or with a pipeline)


## Question 3. Count records 

How many taxi trips were totally made on September 18th 2019?

Tip: started and finished on 2019-09-18. 

Remember that `lpep_pickup_datetime` and `lpep_dropoff_datetime` columns are in the format timestamp (date and hour+min+sec) and not in date.

```sql
select count(*)
from yellow_taxi_data
where lpep_pickup_datetime::date = '2019-09-18'
  and lpep_dropoff_datetime::date = '2019-09-18';
```

## Question 4. Largest trip for each day

Which was the pick up day with the largest trip distance
Use the pick up time for your calculations.

```sql
select lpep_pickup_datetime::date
from ny_taxi.public.yellow_taxi_data
where trip_distance = (select max(trip_distance) from yellow_taxi_data);
```

## Question 5. Three biggest pick up Boroughs

Consider lpep_pickup_datetime in '2019-09-18' and ignoring Borough has Unknown

Which were the 3 pick up Boroughs that had a sum of total_amount superior to 50000?
```sql
select "Borough", lpep_pickup_datetime::date, sum(total_amount) as total
from yellow_taxi_data ytd
         left join ny_location pul on ytd."PULocationID" = pul."LocationID"
where lpep_pickup_datetime::date = '2019-09-18'
group by 1, 2
order by total desc
limit 3;
``` 

## Question 6. Largest tip

For the passengers picked up in September 2019 in the zone name Astoria which was the drop off zone that had the largest tip?
We want the name of the zone, not the id.

Note: it's not a typo, it's `tip` , not `trip`

```sql
select "Zone" from ny_location where "LocationID" = (
select "DOLocationID"
from yellow_taxi_data ytd
         left join ny_location pul on ytd."PULocationID" = pul."LocationID"
where pul."Zone" = 'Astoria'
order by tip_amount desc
limit 1);
```
