You can read a stored record by its `record_key` by using the `read` method. It accepts an object with two fields - `country` and `record_key`.
```python
def read(self, country: str, record_key: str) -> Dict[str, TRecord]:
    ...


# The read method returns the record dictionary if the record is found
{
    "record": Dict
}
```

#### Date fields
You can use the `created_at` and `updated_at` fields to access date-related information about records. The `created_at` field stores a date when a record was initially created in the target country. The `updated_at` field stores a date of the latest write operation for the given recordKey.

You can use the `read` method, as follows:
```python
read_result = storage.read(country="us", record_key="user1")

# read_result would be as follows
read_result = {
    "record": {
        "record_key": "user_1",
        "body": "some PII data",
        "profile_key": "customer",
        "range_key1": 10000,
        "key1": "english",
        "key2": "rolls-royce",
        "created_at": datetime.datetime(...),
        "updated_at": datetime.datetime(...),
    }
}
```