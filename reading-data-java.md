You can read the stored records by `key` by using the `read` method.
```java
public interface Storage {
   /**
    * Read data from remote storage
    *
    * @param country   country identifier
    * @param key record unique identifier
    * @return Record object which contains required data
    * @throws StorageClientException if validation finishes with errors
    * @throws StorageServerException if server connection fails or a server response error occurs
    * @throws StorageCryptoException if decryption fails
    */
    Record read(String country, String key)
        throws StorageClientException, StorageServerException, StorageCryptoException;
    //...
}
```

Below you can find the example of how you may use `read` method:
 ```java
String key = "user_1";
Record record = storage.read("us", key);
String decryptedBody = record.getBody();
 ```

The `Record` object includes the following properties: `key`, `body`, `key2`, `key3`, `profileKey`, `rangeKey`.

These properties can be accessed using getters, for example:

```java
String key2 = record.getKey2();
String body = record.getBody();
```