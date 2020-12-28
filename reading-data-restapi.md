### Find a record

`POST /api/records/find`

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

|Parameters|Type|Example|Description|
|:----|:----|:----|:----|
|`country`|ISO country code (lowercase)|`sg` (equals to Singapore)|Country which a new record is looked up in. You can use different countries.|
|`filter`|object|See the example.|Specify the criteria for filtration within the `filter` parameter. You can look up for records by any of record fields. For the full list of supported fields, please see the Available record fields section.|
|`keyN`|string|See the example.|Value from the key fields of your record.|
|`search_keys`|string|`abc`|Defines the search query string for lookup. The search keys should be provided within the `filter` object.|
|`range_keyN`|array|`[1, 5]`|Defines the range of keys for lookup. The range keys should be provided within the `filter` object.|
|`options`|object|See the example.|Specify the criteria for returning the search results:`limit`, `offset`|
|`limit`|integer|`10`|Sets the maximal number of search results.|
|`offset`|integer|`50`|Specifies the offset (pagination) of records from which the record lookup is performed.|

Example:

```json

{
  "country": "sg",
  "filter": {
    "key1": ["k1234", "k1235"],
    "key2": ["John", "Jane"]
  },
  "options": {
    "limit": 10,
    "offset": 50
  }
}

```

or

```json

{
  "country": "sg",
  "filter": {
    "search_keys": "abc"
  }
}
```

or

```json

{
  "country": "sg",
  "filter": {
    "search_keys": "abc",
    "range_key1": [1, 2]
  }
}

```

Responses:

**STATUS 200** - `application/json` Returns information about the found record or records.

This status is also returned when no records matching the search criteria are found.

Example:

```json

{
    "data": [
        {
            "key": "k1235",
            "profile_key": "pk1235",
            "range_key": 42,
            "record_key": "k1235",
            "body": null,
            "precommit_body": null,
            "key1": null,
            "key2": "Jane",
            "key3": "Doe",
            "key4": null,
            "key5": null,
            "key6": null,
            "key7": null,
            "key8": null,
            "key9": null,
            "key10": null,
            "service_key1": null,
            "service_key2": null,
            "range_key1": 42,
            "range_key2": null,
            "range_key3": null,
            "range_key4": null,
            "range_key5": null,
            "range_key6": null,
            "range_key7": null,
            "range_key8": null,
            "range_key9": null,
            "range_key10": null,
            "version": 0,
            "created_at": "2020-11-21T12:07:43.000Z",
            "updated_at": "2020-11-21T12:07:43.000Z"
        },
        {
            "key": "k1234",
            "profile_key": "pk1234",
            "range_key": 42,
            "record_key": "k1234",
            "body": null,
            "precommit_body": null,
            "key1": null,
            "key2": "John",
            "key3": "Doe",
            "key4": null,
            "key5": null,
            "key6": null,
            "key7": null,
            "key8": null,
            "key9": null,
            "key10": null,
            "service_key1": null,
            "service_key2": null,
            "range_key1": 42,
            "range_key2": null,
            "range_key3": null,
            "range_key4": null,
            "range_key5": null,
            "range_key6": null,
            "range_key7": null,
            "range_key8": null,
            "range_key9": null,
            "range_key10": null,
            "version": 0,
            "created_at": "2020-11-21T11:55:49.000Z",
            "updated_at": "2020-11-21T12:05:28.000Z"
        }
    ],
    "meta": {
        "count": 2,
        "limit": 100,
        "offset": 0,
        "total": 2
    }
}

```

**STATUS 400** - `application/json` Bad request when it has not passed validation. The error details will be in the response.

**STATUS 401** - This error is returned when the request is unauthorized.