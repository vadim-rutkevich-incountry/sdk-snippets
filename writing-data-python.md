Use the `write` method to create/replace a record (by `record_key`).
```python
def write(self, country: str, record_key: str, **record_data: Union[str, int]) -> Dict[str, TRecord]:
    ...


# write returns created record dict on success
{
    "record": Dict
}
```


Below you can find the example of how you can use the `write` method.
```python
write_result = storage.write(
    country="us",
    record_key="user_1",
    body="some PII data",
    profile_key="customer",
    range_key1=10000,
    key1="english",
    key2="rolls-royce",
)

# write_result would be as follows
write_result = {
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

For the list of possible `record_data` kwargs, see the section below.


#### List of available record fields
v3.0.0 release introduced a series of new fields available for data storage. Below you can find the full list of all the fields available for storage in InCountry Platform along with their types and storage methods. Each field is either encrypted, hashed or stored as follows:
```python
# String fields, hashed
record_key
key1
key2
key3
key4
key5
key6
key7
key8
key9
key10
profile_key
service_key1
service_key2

# String fields, encrypted
body
precommit_body

# Int fields, plain
range_key1
range_key2
range_key3
range_key4
range_key5
range_key6
range_key7
range_key8
range_key9
range_key10
```

#### Batches
Use the `batch_write` method to create/replace multiple records at once.

```python
def batch_write(self, country: str, records: List[TRecord]) -> Dict[str, List[TRecord]]:
    ...


# batch_write returns the following dict of created records
{
    "records": List
}
```

Below you can find the example of how to use this method.
```python
batch_result = storage.batch_write(
    country="us",
    records=[
        {"record_key": "key1", "body": "body1", ...},
        {"record_key": "key2", "body": "body2", ...},
    ],
)

# batch_result would be as follows
batch_result = {
    "records": [
        {"record_key": "key1", "body": "body1", ...},
        {"record_key": "key2", "body": "body2", ...},
    ]
}
```