You can look up for data records either by using exact match search operators or partial text match operators in almost any combinations.

##### Exact match search

The following exact match search criteria are available:
- single value:
```typescript
// Matches all records where record.key1 = 'abc' AND record.rangeKey = 1
{ key1: 'abc', rangeKey1: 1 }
```

- multiple values as an array:
```typescript
// Matches all records where (record.key2 = 'def' OR record.key2 = 'jkl') AND (record.rangeKey = 1 OR record.rangeKey = 2)
{ key2: ['def', 'jkl'], rangeKey1: [1, 2] }
```

- a logical NOT operator for [String fields](#string-fields-hashed) and `version`:
```typescript
// Matches all records where record.key3 <> 'abc'
{ key3: { $not: 'abc' } }

// Matches all records where record.key3 <> 'abc' AND record.key3 <> 'def'
{ key3: { $not: ['abc', 'def'] } }

// Matches all records where record.version <> 1
{ version: { $not: 1 }}
```

- comparison operators for [Int fields](#int-fields-plain):
```typescript
// Matches all records where record.rangeKey >= 5 AND record.rangeKey <= 100
{ rangeKey1: { $gte: 5, $lte: 100 } }
```

##### Partial text match search

You can also look up for data records by partial match using the `searchKeys` operator which performs partial match
search (similar to the `LIKE` SQL operator) within records text fields `key1, key2, ..., key10`.
```typescript
// Matches all records where record.key1 LIKE 'abc' OR record.key2 LIKE 'abc' OR ... OR record.key10 LIKE 'abc'
{ searchKeys: 'abc' }
```

**Please note:** The `searchKeys` operator cannot be used in combination with any of `key1, key2, ..., key10` keys and works only in combination with the non-hashing Storage mode (hashSearchKeys param for Storage).

```typescript
// Matches all records where (record.key1 LIKE 'abc' OR record.key2 LIKE 'abc' OR ... OR record.key10 LIKE 'abc') AND (record.rangeKey = 1 OR record.rangeKey = 2)
{ searchKeys: 'abc', rangeKey1: [1, 2] }

// Causes validation error (StorageClientError)
{ searchKeys: 'abc', key1: 'def' }
```

#### Search options

The `options` parameter defines the `limit` - number of records that are returned, and the `offset`- the starting index used for record pagination.
You can use these parameters to implement pagination. Note: The SDK returns 100 records at most.


```typescript
type FilterStringValue = string | string[];
type FilterStringQuery = FilterStringValue | { $not?: FilterStringValue };

type FilterNumberValue = number | number[];
type FilterNumberQuery =
  FilterNumberValue |
  {
    $not?: FilterNumberValue;
    $gt?: number;
    $gte?: number;
    $lt?: number;
    $lte?: number;
  };

type FindFilter = Record<string, FilterStringQuery | FilterNumberQuery>;

type FindOptions = {
  limit?: number;
  offset?: number;
};

type FindResult = {
  meta: {
    total: number;
    count: number;
    limit: number;
    offset: number;
  };
  records: Array<StorageRecord>;
  errors?: Array<{ error: StorageCryptoError; rawData: ApiRecord }>;
};

async find(
  countryCode: string,
  filter: FindFilter = {},
  options: FindOptions = {},
  requestOptions: RequestOptions = {},
): Promise<FindResult> {
  /* ... */
}
```

#### Example of usage
```typescript
const filter = {
  key1: 'abc',
  key2: ['def', 'jkl'],
  profileKey: 'test2',
  rangeKey1: { $gte: 5, $lte: 100 },
  rangeKey2: { $not: [0, 1] },
}

const options = {
  limit: 100,
  offset: 0,
};

const findResult = await storage.find(countryCode, filter, options);
```

The returned `findResult` object looks like the following:

```typescript
{
  records: [{/* StorageRecord */}],
  errors: [],
  meta: {
    limit: 100,
    offset: 0,
    total: 24
  }
}
```

### Find one record matching a filter

If you need to find only one of the records matching a specific filter, you can use the `find_one` method.
If a record is not found, it returns `null`.

```typescript
type FindOneResult = {
  record: StorageRecord | null;
};

async findOne(
  countryCode: string,
  filter: FindFilter = {},
  options: FindOptions = {},
  requestOptions: RequestOptions = {},
): Promise<FindOneResult> {
  /* ... */
}
```

Example of usage:
```typescript
const findOneResult = await storage.findOne(countryCode, filter);
```