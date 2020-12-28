### Create a record

`POST /api/records`

:::note
Alternatively, this request can be used to update the record. Be careful, as it rewrites the data record with new values you provide.
:::

Headers:

|Header|Value|
|:----|:----|
|Content-Type|application/json|
|Content-Length|Calculated automatically when a request is sent|
|Host|Calculated automatically when a request is sent|
|User-Agent|Specify the user agent used|
|Accept|*/*|
|Accept-Encoding|gzip, deflate, br|
|Connection|keep-alive|

Request Body:

|Parameters|Type|Description|
|:----|:----|:----|
|`record_key`|string|It is used for storing the primary key of the record (identifier). The value from this field is also propagated to the `key` field when REST API creates a new record.|
|`keyN`|string|Can be used for storing regulated and non-regulated data. Your record can have up to 10 keys.|
|`profile_key`|string|Additional data value|
|`service_key1` `service_key2`|string|Service data value.|
|`range_key1`|integer|Numerical value. Your record can have up to 10 range keys.|
|`body`|string|Any text string that you want to store.|
|`precommit_body`|string|Any text string that you want to store. It can be used for storing the intermediate value of the `body` field.|
|`country`|ISO country code (lowercase)|Country which a new record is written to|

Example:

```json

{
  "record_key": "a98787ss87",
  "profile_key": "pk1237",
  "key1": "a1237",
  "key2": "John",
  "key3": "Doe",
  ...
  "key10": "Visa",
  "service_key1": "478",
  "service_key2": "57",
  "body": "This is a contact from Tradeshow",
  "precommit_body": "Consent received on 12/1/2020",
  "range_key1": 42,
  "range_key2": 50,
  ...
  "range_key10": 980,
  "country": "sg"
}

```

Responses:

**STATUS 201** - `application/json` Returns information and a record key for the newly created record.

Example:

```json
{
    "key": "a98787ss87",
    "profile_key": "pk1237",
    "range_key": 42,
    "record_key": "a98787ss87",
    "body": "This is a contact from Tradeshow",
    "precommit_body": "Consent received on 12/1/2020",
    "key1": "a1237",
    "key2": "John",
    "key3": "Doe",
    ...
    "key10": "Visa",
    "service_key1": "478",
    "service_key2": "57",
    "range_key1": 42,
    "range_key2": 50,
    ...
    "range_key10": 980,
    "country": "sg"
}
```

**STATUS 400** - `application/json` Bad request when it has not passed validation. The error details will be in the response.

**STATUS 401** - This error status is returned when the request is unauthorized.