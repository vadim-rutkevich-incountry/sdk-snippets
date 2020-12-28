Use the `write` method to create a new record.
```java
public interface Storage {
    /**
     * Write data to a remote storage
     *
     * @param country country identifier
     * @param record  object which encapsulates data which must is written to the storage
     * @return recorded record
     * @throws StorageClientException if validation finishes with errors
     * @throws StorageServerException if server connection fails or a server response error occurs
     * @throws StorageCryptoException if encryption fails
     */
    Record write(String country, Record record)
          throws StorageClientException, StorageServerException, StorageCryptoException;
    //...
}
```

Below you can find the example of how to initialize a record object:
```java
public class Record {
    /**
     * Full constructor
     *
     * @param key        Required, record key
     * @param body       Optional, data to be stored and encrypted
     * @param profileKey Optional, profile key
     * @param rangeKey   Optional, range key
     * @param key2       Optional, key2
     * @param key3       Optional, key3
     */
    public Record(String key, String body, String profileKey, Long rangeKey, String key2, String key3)
    //...
}
```

Below you can find the example of how to use the `write` method:
```java
key = "user_1";
body = "some PII data";
profileKey = "customer";
rangeKey = 10000l;
key2 = "english";
key3 = "insurance";
Record record = new Record(key, body, profileKey, rangeKey, key2, key3);
storage.write("us", record);
```

#### Encryption
InCountry Platform uses the client-side encryption for your data. Note that only body is encrypted. Some other fields are hashed.
Below you can find the example of how data is transformed and stored in InCountry Platform:
```java
public class Record {
    private String key;          // hashed
    private String body;         // encrypted
    private String profileKey;   // hashed
    private Long rangeKey;       // plain
    private String key2;         // hashed
    private String key3;         // hashed
    //...
}
```

### Batches

Use the `batchWrite` method to write multiple records to the storage within a single request.
```java
public interface Storage {
     /**
      * Write multiple records at once in remote storage
      *
      * @param country country identifier
      * @param records record list
      * @return BatchRecord object which contains a list of recorded records
      * @throws StorageClientException if validation finishes with errors
      * @throws StorageServerException if server connection fails or a server response error occurs
      * @throws StorageCryptoException if record encryption fails
      */
     BatchRecord batchWrite(String country, List<Record> records)
          throws StorageClientException, StorageServerException, StorageCryptoException;
     //...
}
```

 Below you can find the example of how you can use the `batchWrite` method:
```java
List<Record> list = new ArrayList<>();
list.add(new Record(firstKey, firstBody, firstProfileKey, firstRangeKey, firstKey2, firstKey3));
list.add(new Record(secondKey, secondBody, secondProfileKey, secondRangeKey, secondKey2, secondKey3));
storage.batchWrite("us", list);
```