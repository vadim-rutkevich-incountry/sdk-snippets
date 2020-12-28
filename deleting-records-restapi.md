### Delete a record

`DELETE /api/records/:country/:key`

Headers:

|Header|Value|
|:----|:----|
|Host|Calculated automatically when a request is sent|
|User-Agent|Specify the user agent used|
|Accept|*/*|
|Accept-Encoding|gzip, deflate, br|
|Connection|keep-alive|

Request parameters:

|Parameters|Type|Value|Description|
|:----|:----|:----|:----|
|`country`|ISO country code (lowercase)|`sg`|Country which a new record is removed from|
|`key`|string/array|`k1234`|Unique primary key for record identification|

Responses:

**STATUS 204** - `plain` This response is returned when the record has been successfully deleted.

**STATUS 401**- This response is returned when the request is unauthorized.

**STATUS 404** - `plain` This response is returned when the record is not found.