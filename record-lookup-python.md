You can look up for records by keys or version using the `find` method.
```python
def find(
        self,
        country: str,
        limit: Optional[int] = FIND_LIMIT,
        offset: Optional[int] = 0,
        **filters: Union[TIntFilter, TStringFilter],
    ) -> Dict[str, Any]:
    ...
```
Note: SDK returns 100 records at most.

The returned object looks like the following:
```python
{
    "data": List,
    "errors": List, # optional
    "meta": {
        "limit": int,
        "offset": int,
        "total": int,  # total records matching filter, ignoring limit
    }
}
```
You can use the following options to look up for records by hashed string keys from the [list above](#list-of-available-record-fields):
```python
# single value
key1="value1" # records with key1 equal to "value1"

# list of values
key2=["value1", "value2"] # records with key2 equal to "value1" or "value2"

# dict with $not operator
key3={"$not": "value1"} # records with key3 not equal "value1"
key4={"$not": ["value1", "value2"]} # records with key4 equal to neither "value1" or "value2"
```


You can use the following options to look up for records by int keys from the [list above](#list-of-available-record-fields):
```python
# single value
range_key1=1 # records with range_key1 equal to 1

# list of values
range_key2=[1, 2] # records with range_key2 equal to 1 or 2

# dictionary with comparison operators
range_key3={"$gt": 1} # records with range_key3 greater than 1
range_key4={"$gte": 1} # records with range_key4 greater than or equal to 1
range_key5={"$lt": 1} # records with range_key5 less than 1
range_key6={"$lte": 1} # records with range_key6 less than or equal to 1

# you can combine different comparison operators
range_key7={"$gt": 1, "$lte": 10} # records with range_key7 greater than 1 and less than or equal to 10

# you cannot combine similar comparison operators - e.g. $gt and $gte, $lt and $lte
```


You can use the following option to look up for records by `version` (encryption key version):
```python
# single value
version=1 # records with version equal to 1

# list of values
version=[1, 2] # records with version equal to 1 or 2

# dictionary with $not operator
version={"$not": 1} # records with version not equal 1
version={"$not": [1, 2]} # records with version equal to neither 1 or 2
```

Below you can find the example of how you can use the `find` method:
```python
find_result = storage.find(country="us", limit=10, offset=10, key1="value1", key2=["value2", "value3"])

# find_result would be as follows
find_result = {
    "data": [
        {
            "record_key": "<record_key>",
            "body": "<body>",
            "key1": "value1",
            "key2": "value2",
            "created_at": datetime.datetime(...),
            "updated_at": datetime.datetime(...),
            ...
        }
    ],
    "meta": {
        "limit": 10,
        "offset": 10,
        "total": 100,
    }
}
```

### Find one record matching a filter

If you need to find only one of the records matching a specific filter, you can use the `find_one` method.
```python
def find_one(
        self, country: str, offset: Optional[int] = 0, **filters: Union[TIntFilter, TStringFilter],
    ) -> Union[None, Dict[str, Dict]]:
    ...


# If a record is not found, the find_one method returns `None`. Otherwise it returns a record dictionary.
{
    "record": Dict
}
```

Below you can find the example of how to use the `find_one` method:
```python
find_one_result = storage.find_one(country="us", key1="english", key2=["rolls-royce", "bmw"])

# find_one_result would be as follows
find_one_result = {
    "record": {
        "record_key": "user_1",
        "body": "some PII data",
        "profile_key": "customer",
        "range_key1": 10000,
        "key1": "english",
        "key2": "rolls-royce",
    }
}
```