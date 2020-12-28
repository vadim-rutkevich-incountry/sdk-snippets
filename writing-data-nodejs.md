Use the `write` method create/replace a record (by `recordKey`) .

#### List of available record fields
v3.0.0 release introduced a series of new fields available for data storage. Below you can find the full list of all the fields available for storage in InCountry Platform along with their types and storage methods. Each field is either encrypted, hashed or stored as follows:


##### String fields, hashed:
```typescript
recordKey
profileKey
serviceKey1
serviceKey2
```
##### String fields, hashed if Storage options "hashSearchKeys" is set to true (by default it is):
**WARNING** If the `hashSearchKeys` option is set to `false` the following string fields will have length limitation of 256 characters at most.

```typescript
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
```

##### String fields, encrypted:
```typescript
body
precommitBody
```

##### Int fields, plain:
```typescript
rangeKey1
rangeKey2
rangeKey3
rangeKey4
rangeKey5
rangeKey6
rangeKey7
rangeKey8
rangeKey9
rangeKey10
```

```typescript
type StorageRecordData = {
  recordKey: string;
  profileKey?: string | null;
  key1?: string | null;  // If `hashSearchKeys` is set to `false` key1 has length limit 256
  key2?: string | null;  // If `hashSearchKeys` is set to `false` key2 has length limit 256
  key3?: string | null;  // If `hashSearchKeys` is set to `false` key3 has length limit 256
  key4?: string | null;  // If `hashSearchKeys` is set to `false` key4 has length limit 256
  key5?: string | null;  // If `hashSearchKeys` is set to `false` key5 has length limit 256
  key6?: string | null;  // If `hashSearchKeys` is set to `false` key6 has length limit 256
  key7?: string | null;  // If `hashSearchKeys` is set to `false` key7 has length limit 256
  key8?: string | null;  // If `hashSearchKeys` is set to `false` key8 has length limit 256
  key9?: string | null;  // If `hashSearchKeys` is set to `false` key9 has length limit 256
  key10?: string | null; // If `hashSearchKeys` is set to `false` key10 has length limit 256
  serviceKey1?: string | null;
  serviceKey2?: string | null;
  body?: string | null;
  precommitBody?: string | null;
  rangeKey1?: t.Int | null;
  rangeKey2?: t.Int | null;
  rangeKey3?: t.Int | null;
  rangeKey4?: t.Int | null;
  rangeKey5?: t.Int | null;
  rangeKey6?: t.Int | null;
  rangeKey7?: t.Int | null;
  rangeKey8?: t.Int | null;
  rangeKey9?: t.Int | null;
  rangeKey10?: t.Int | null;
};

type WriteResult = {
  record: StorageRecordData;
};

async write(
  countryCode: string,
  recordData: StorageRecordData,
  requestOptions: RequestOptions = {},
): Promise<WriteResult> {
  /* ... */
}
```

Below you can find the example of how you can use the `write` method.

```typescript
const recordData = {
  recordKey: '<key>',
  body: '<body>',
  profileKey: '<profile_key>',
  rangeKey1: 0,
  key2: '<key2>',
  key3: '<key3>'
}

const writeResult = await storage.write(countryCode, recordData);
```

#### Batches

You can use the `batchWrite` method to create/replace multiple records at once.

```typescript
type BatchWriteResult = {
  records: Array<StorageRecordData>;
};

async batchWrite(
  countryCode: string,
  records: Array<StorageRecordData>,
  requestOptions: RequestOptions = {},
): Promise<BatchWriteResult> {
  /* ... */
}
```

Example of usage:
```typescript
batchResult = await storage.batchWrite(countryCode, recordDataArr);
```