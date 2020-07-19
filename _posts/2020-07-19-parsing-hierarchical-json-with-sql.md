---
layout: default
title:  "Flatten Hierarchical JSON with SQL"
day: 19th
month: July
year: 2020
author: sabrina
category: [SQL]
---

JSON is a widely used data format that every developer should know how to read, parse, and manipulate. Maybe I will write a JSON 101 post at a later date, but today we are going to learn how to flatten hierarchical JSON with SQL. I will specifically be using T-SQL for MS SQL Server.

First things first. We need to come up with a scenario. Let's pretend we have a list of flight itineraries in JSON. Each itinerary can have multiple passengers and multiple legs to the trip. The final goal is to completely flatten the data where we have a single record for each itinerary, flight, passenger combination.

Now we need some sample JSON that we are going to flatten.

{% highlight json %}
[
  {
    "id": 1, 
    "flights": [
        {"source":"LAX","destination":"DEN","departure":"May 5th 10:00AM"},
        {"source":"DEN","destination":"ORD","departure":"May 5th 5:00PM"},
        {"source":"ORD","destination":"LAX","departure":"May 12th 9:00AM"}
      ], 
    "passengers": [
      {"name": "Molly Brown"},
      {"name": "Sam Brown"}
    ]
  },
  {
    "id": 2, 
    "flights": [
      {"source":"DEN","destination":"ORD","departure":"May 5th 5:00PM"},
      {"source":"ORD","destination":"DEN","departure":"May 10th 11:00AM"}
    ], 
    "passengers": [
      {"name": "Sarah White"}
    ]
  },
  {
    "id": 3, 
    "flights": [
      {"source":"ORD","destination":"DEN","departure":"May 10th 11:00AM"}
    ], 
    "passengers": [
      {"name": "John Smith"},
      {"name": "Ellie Smith"},
      {"name": "Madison Smith"}
    ]
  }
]
{% endhighlight %}

Here is what we want our data to look like.

| Itinerary |     Name    | Source | Destination |    Departure   |
|:---------:|:-----------:|:------:|:-----------:|:--------------:|
|     1     |Molly Brown  |LAX     |DEN          |May 5th 10:00AM |
|     1     |Sam Brown    |LAX     |DEN          |May 5th 10:00AM |
|     1     |Molly Brown  |DEN     |ORD          |May 5th 5:00PM  |
|     1     |Sam Brown    |DEN     |ORD          |May 5th 5:00PM  |
|     1     |Molly Brown  |ORD     |LAX          |May 12th 9:00AM |
|     1     |Sam Brown    |ORD     |LAX          |May 12th 9:00AM |
|     2     |Sarah White  |DEN     |ORD          |May 5th 5:00PM  |
|     2     |Sarah White  |ORD     |DEN          |May 10th 11:00AM|
|     3     |John Smith   |ORD     |DEN          |May 10th 11:00AM|
|     3     |Ellie Smith  |ORD     |DEN          |May 10th 11:00AM|
|     3     |Madison Smith|ORD     |DEN          |May 10th 11:00AM|

Now let us walk through how we can get there.

We will start by querying the first layer of data, the itineraries. We can use the [OPENJSON](https://docs.microsoft.com/en-us/sql/t-sql/functions/openjson-transact-sql) function to do this. Here is the SQL and results from the first level query.

{% highlight sql %}
SELECT 
  Itinerary,
  flights,
  passengers
FROM OPENJSON(@json)  
  WITH (
    Itinerary INT '$.id',
    flights NVARCHAR(MAX) '$.flights' AS JSON,
    passengers NVARCHAR(MAX) '$.passengers' AS JSON
  );
{% endhighlight %}

|Itinerary|flights|passengers|
|:-------:|:-----:|:--------:|
|1|[{"source":"LAX","destination":"DEN","departure":"May 5th 10:00AM"}, {"source":"DEN","destination":"ORD","departure":"May 5th 5:00PM"},{"source":"ORD","destination":"LAX","departure":"May 12th 9:00AM"}]|[ {"name": "Molly Brown"}, {"name": "Sam Brown"} ]|
|2|[{"source":"DEN","destination":"ORD","departure":"May 5th 5:00PM"},{"source":"ORD","destination":"DEN","departure":"May 10th 11:00AM"}]|[{"name": "Sarah White"}]|
|3|[{"source":"ORD","destination":"DEN","departure":"May 10th 11:00AM"}]|[{"name": "John Smith"},{"name": "Ellie Smith"},{"name": "Madison Smith"}]|

Now lets parse the JSON that is now labled as flights for each itenerary. We will use the [OPENJSON](https://docs.microsoft.com/en-us/sql/t-sql/functions/openjson-transact-sql) function again in conjuction with an OUTER APPLY. The OUTER APPLY will run the OPENJSON function across each record and create a recordset of data from both sides of the OUTER APPLY.

{% highlight sql %}
SELECT 
  Itinerary,
  Name,
  Source,
  Destination,
  Departure
FROM OPENJSON(@json)  
  WITH (
    Itinerary INT '$.id',
    flights NVARCHAR(MAX) '$.flights' AS JSON,
    passengers NVARCHAR(MAX) '$.passengers' AS JSON
  )
OUTER APPLY OPENJSON(flights)
  WITH (
    Source NVARCHAR(3) '$.source',
    Destination NVARCHAR(3) '$.destination',
    Departure NVARCHAR(20) '$.departure'
  );
{% endhighlight %}

|Itinerary|Source|Destination|Departure|passengers|
|:-------:|:----:|:---------:|:-------:|:--------:|
|1|LAX|DEN|May 5th 10:00AM|[{"name": "Molly Brown"},{"name": "Sam Brown"}]|
|1|DEN|ORD|May 5th 5:00PM|[{"name": "Molly Brown"},{"name": "Sam Brown"}]|
|1|ORD|LAX|May 12th 9:00AM|[{"name": "Molly Brown"},{"name": "Sam Brown"}]|
|2|DEN|ORD|May 5th 5:00PM|[{"name": "Sarah White"}]|
|2|ORD|DEN|May 10th 11:00AM|[{"name": "Sarah White"}]|
|3|ORD|DEN|May 10th 11:00AM|[{"name": "John Smith"},{"name": "Ellie Smith"},{"name": "Madison Smith"}]|

Now we have flattened the JSON to itinerary, flight level. All that is left is parsing passenger data in the same way that we used an OUTER APPLY for the flight data. Here we have the final SQL query.

{% highlight sql %}
SELECT 
  Itinerary,
  Name,
  Source,
  Destination,
  Departure
FROM OPENJSON(@json)  
  WITH (
    Itinerary INT '$.id',
    flights NVARCHAR(MAX) '$.flights' AS JSON,
    passengers NVARCHAR(MAX) '$.passengers' AS JSON
  )
OUTER APPLY OPENJSON(flights)
  WITH (
    Source NVARCHAR(3) '$.source',
    Destination NVARCHAR(3) '$.destination',
    Departure NVARCHAR(20) '$.departure'
  )
OUTER APPLY OPENJSON(passengers)
  WITH (
    Name NVARCHAR(20) '$.name'
  );
{% endhighlight %}

We can use this same method for deeper hierarchies. Imagine we added another layer of data to each passenger for their luggage. Here is some json with the deeper hierarchy, the SQL and the output. 

{% highlight json %}
[
  {
    "id": 1, 
    "flights": [
        {"source":"LAX","destination":"DEN","departure":"May 5th 10:00AM"}
      ], 
    "passengers": [
      {
        "name": "Molly Brown",
        "luggage": [
          {"id": 1,"type": "Carry On"},
          {"id": 2,"type": "Checked"}
        ]
      },
      {
        "name": "Sam Brown",
        "luggage": [
          {"id": 3,"type": "Carry On"},
          {"id": 4,"type": "Checked"},
          {"id": 5,"type": "Checked"}
        ]
      }
    ]
  }
]
{% endhighlight %}

{% highlight sql %}
SELECT 
	Itinerary,
    Name,
    Source,
    Destination,
    Departure,
    LuggageId,
    LuggageType
FROM OPENJSON(@json)  
  WITH (
    Itinerary INT '$.id',
    flights NVARCHAR(MAX) '$.flights' AS JSON,
    passengers NVARCHAR(MAX) '$.passengers' AS JSON)
OUTER APPLY OPENJSON(flights)
  WITH (
    Source NVARCHAR(3) '$.source',
    Destination NVARCHAR(3) '$.destination',
    Departure NVARCHAR(20) '$.departure'
  )
OUTER APPLY OPENJSON(passengers)
  WITH (
    Name NVARCHAR(20) '$.name',
    luggage NVARCHAR(MAX) '$.luggage' AS JSON
  )
OUTER APPLY OPENJSON(luggage)
  WITH (
    LuggageId Int '$.id',
    LuggageType NVARCHAR(20) '$.type'
  );
{% endhighlight %}

|Itinerary|Name|Source|Destination|Departure|LuggageId|LuggageType|
|:-------:|:--:|:----:|:---------:|:-------:|:-------:|:---------:|
|1|Molly Brown|LAX|DEN|May 5th 10:00AM|1|Carry On|
|1|Molly Brown|LAX|DEN|May 5th 10:00AM|2|Checked|
|1|Sam Brown|LAX|DEN|May 5th 10:00AM|3|Carry On|
|1|Sam Brown|LAX|DEN|May 5th 10:00AM|4|Checked|
|1|Sam Brown|LAX|DEN|May 5th 10:00AM|5|Checked|