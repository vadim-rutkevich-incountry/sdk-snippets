You can use the `delete` method to delete a record from InCountry Platform. It is possible by using the `record_key` field only.
```python
def delete(self, country: str, record_key: str) -> Dict[str, bool]:
    ...


# the delete method returns the following dictionary upon success
{
    "success": True
}
```

Below you can find the example of how to use the delete method:
```python
delete_result = storage.delete(country="us", record_key="<record_key>")

# delete_result would be as follows
delete_result = {
    "success": True
}