You can search for data records by random keys using the `find` method.
```java
public interface Storage {
   /**
    * Find records in remote storage according to filters
    *
    * @param country country identifier
    * @param builder object representing find filters and search options
    * @return BatchRecord object which contains required records
    * @throws StorageClientException if validation finishes with errors
    * @throws StorageServerException if server connection fails or a server response error occurs
    * @throws StorageCryptoException if decryption fails
    */
    BatchRecord find(String country, FindFilterBuilder builder)
         throws StorageClientException, StorageServerException, StorageCryptoException;
    //...
}
```

You can use the `FindFilterBuilder` class to refine your find request.

Below you can find the example of how to use the `find` method along with the `FindFilterBuilder` class:
```java
FindFilterBuilder builder = FindFilterBuilder.create()
                  .key2Eq("someKey")
                  .key3Eq("firstValue","secondValue")
                  .rangeKeyBetween(123l, 456l);

BatchRecord findResult = storage.find("us", builder);
if (findResult.getCount() > 0) {
    Record record = findResult.getRecords().get(0);
    //...
}
```

The request will return records that are filtered according to the following pseudo-sql:
```sql
key2 = 'someKey' AND key3 in ('firstValue' , 'secondValue') AND (123 < = `rangeKey` < = 456)
```

All conditions added with the `FindFilterBuilder` class are joined using the logical operator `AND`. You cannot add multiple conditions for the same key, elsewise only the last condition will be used.

The SDK returns 100 records at most. Use the `limit` and `offset` parameters to iterate through the list of records.
```java
FindFilterBuilder builder = FindFilterBuilder.create()
                  //...
                  .limitAndOffset(20, 80);
BatchRecord records = storage.find("us", builder);
```

Next predicate types are available for each string key field of the `Record` class through individual methods of the `FindFilterBuilder` class:
```java
EQUALS         (FindFilterBuilder::keyEq)
               (FindFilterBuilder::key2Eq)
               (FindFilterBuilder::key3Eq)
               (FindFilterBuilder::profileKeyEq)
NOT_EQUALS     (FindFilterBuilder::keyNotEq)
               (FindFilterBuilder::key2NotEq)
               (FindFilterBuilder::key3NotEq)
               (FindFilterBuilder::profileKeyNotEq)
```

You can use the following builder methods for record filtering by the numerical `rangeKey` field:
```java
EQUALS              (FindFilterBuilder::rangeKeyEq)
IN                  (FindFilterBuilder::rangeKeyIn)
GREATER             (FindFilterBuilder::rangeKeyGT)
GREATER OR EQUALS   (FindFilterBuilder::rangeKeyGTE)
LESS                (FindFilterBuilder::rangeKeyLT)
LESS OR EQUALS      (FindFilterBuilder::rangeKeyLTE)
BETWEEN             (FindFilterBuilder::rangeKeyBetween)
```

The `find` method returns the `BatchRecord` object which contains a list of `Record` and some metadata:
```java
class BatchRecord {
    private int count;
    private int limit;
    private int offset;
    private int total;
    private List<Record> records;
    //...
}
```

These fields can be accessed using getters, for example:

```java
int limit = records.getTotal();
```

The `BatchRecord.getErrors()` method allows you to get a list of the `RecordException` objects with detailed information about records that were not correctly processed during execution of the `find` request.

### Find one record matching a filter

If you need to find the first record matching filter, you can use `findOne` method:
```java
public interface Storage {
   /**
    * Find only one first record in remote storage according to filters
    *
    * @param country country identifier
    * @param builder object representing find filters
    * @return founded record or null
    * @throws StorageClientException if validation finishes with errors
    * @throws StorageServerException if server connection fails or a server response error occurs
    * @throws StorageCryptoException if decryption fails
    */
    Record findOne(String country, FindFilterBuilder builder)
           throws StorageClientException, StorageServerException, StorageCryptoException;
    //...
}
```

It works the same way as the `find` method but returns the first record or `null` if no records found.

Below you can find the example of how the `findOne` method can be used:
```java
FindFilterBuilder builder = FindFilterBuilder.create()
                .key2Eq("someKey")
                .key3Eq("firstValue", "secondValue")
                .rangeKeyBetween(123l, 456l);

Record record = storage.findOne("us", builder);
//...
```