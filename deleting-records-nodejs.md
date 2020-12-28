You can use the `delete` method to delete a record from InCountry Platform. It is possible by using the `record_key` field only.
```typescript
type DeleteResult = {
  success: true;
};

async delete(
  countryCode: string,
  recordKey: string,
  requestOptions: RequestOptions = {},
): Promise<DeleteResult> {
  /* ... */
}
```

Example of usage:
```typescript
const deleteResult = await storage.delete(countryCode, recordKey);
```