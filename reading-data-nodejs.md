You can read the stored data records by its `recordKey` by using the `read` method. It accepts an object with the two fields - `country` and `recordKey`.
It returns a `Promise` which is resolved to `{ record }` or is rejected if there are no records for the passed `recordKey`.

#### Date fields

You can use the `createdAt` and `updatedAt` fields to access date-related information about records. The `createdAt` field stores the date when the record was initially created in the target country. The `updatedAt` field stores the date of the latest write operation for the given `recordKey`.

```typescript
type StorageRecord = {
  recordKey: string;
  body: string | null;
  profileKey: string | null;
  precommitBody: string | null;
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
  serviceKey1: string | null;
  serviceKey2: string | null;
  rangeKey1: t.Int | null;
  rangeKey2: t.Int | null;
  rangeKey3: t.Int | null;
  rangeKey4: t.Int | null;
  rangeKey5: t.Int | null;
  rangeKey6: t.Int | null;
  rangeKey7: t.Int | null;
  rangeKey8: t.Int | null;
  rangeKey9: t.Int | null;
  rangeKey10: t.Int | null;
  createdAt: Date;
  updatedAt: Date;
}

type ReadResult = {
  record: StorageRecord;
};

async read(
  countryCode: string,
  recordKey: string,
  requestOptions: RequestOptions = {},
): Promise<ReadResult> {
  /* ... */
}
```

Example of usage:
```javascript
const readResult = await storage.read(countryCode, recordKey);
```