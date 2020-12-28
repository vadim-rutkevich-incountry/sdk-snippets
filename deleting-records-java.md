Use the `delete` method to delete records from InCountry storage. It is only possible by using the `key` field.
```java
public interface Storage {
    /**
    * Delete a record from remote storage
    *
    * @param country   country code of the record
    * @param key the record's key
    * @return true when record was deleted
    * @throws StorageClientException if validation finishes with errors
    * @throws StorageServerException if server connection fails
    */
    boolean delete(String country, String key)
            throws StorageClientException, StorageServerException;
    //...
}
```

Below you can find the the example of how you may use the `delete` method:
 ```java
String key = "user_1";
storage.delete("us", key);
 ```