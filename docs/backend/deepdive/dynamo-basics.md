## DynamoDB Basics

---

### Key Structure

Each item in a `DynamoDB` table is uniquely identified by a primary key(the `HASH` key, or the
`PARTITION` key) + secondary key(sometimes called the `RANGE` key or the `SORT` key) combination.

The attributes designated to be `HASH` and `SORT` keys have to be pre-declared during table
creation.

*Note: A `DynamoDB` can be created without a range key, in which case the hash key will be the sole
identifier, and can only appear once per table.*

Let's take for instance a table with `userId` as `HASH` key and `recordKey` as `SORT`.

For example:

```json
{
  "userId": "user-001",
  "recordKey": "Profile"
  // ...
}
```

Since `HASH` + `SORT` key combinations are unique, an item with the above keys can only appear once
in the entire table.

Any put operation with those specific keys will overwrite whatever item that was there beforehand.
