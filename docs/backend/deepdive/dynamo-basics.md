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

With the following item:

```json
{
  "userId": "user-001",
  "recordKey": "Profile"
  // ...omitted
}
```

Since `HASH` + `SORT` key combinations are unique, an item with the above keys can only appear once
in the entire table.

Any put operation with those specific keys will overwrite whatever item that was there beforehand.

### Partitions

A partition in `DynamoDB` simply means a collection of items that share the same `HASH` key.

These two are items in the same partition:

```json
[
  {
    "userId": "user-001",
    "recordKey": "Profile"
  },
  {
    "userId": "user-001",
    "recordKey": "Birthday"
  }
]
```

While these two are not:

```json
[
  {
    "userId": "user-001",
    "recordKey": "Profile"
  },
  {
    "userId": "user-002",
    "recordKey": "Profile"
  }
]
```

### Queries

`DynamoDB` allows querying by `HASH` key and/or a condition on the `SORT` key.

Consider a table with the following items:

```json
[
  {
    "userId": "user-001",
    "recordKey": "Profile"
  },
  {
    "userId": "user-001",
    "recordKey": "Friend#user-002"
  },
  {
    "userId": "user-001",
    "recordKey": "Friend#user-003"
  },
  {
    "userId": "user-001",
    "recordKey": "Session#2022-10-07"
  },
  {
    "userId": "user-001",
    "recordKey": "Session#2022-10-15"
  },
  {
    "userId": "user-001",
    "recordKey": "Session#2022-10-23"
  }
]
```

You can issue queries in following format:
<br>
(Pseudo query, not actual DynamoDB syntax)

- `HASH` partition
    - `userId` `equals` `user-001`
- `SORT` condition
    - `recordKey` `equals` `Profile`

You'd get one result, for obvious reasons.

You could also issue a query with no condition on `SORT` key, in which case you'd receive all 5
items above.

Or you could give it a `begins with` condition.

- `HASH` partition
    - `userId` `equals` `user-001`
- `SORT` condition
    - `recordKey` `begins with` `Friend#`

You'd get the following items:

```json
[
  {
    "userId": "user-001",
    "recordKey": "Friend#user-002"
  },
  {
    "userId": "user-001",
    "recordKey": "Friend#user-003"
  }
]
```

And also:

- `HASH` partition
    - `userId` `equals` `user-001`
- `SORT` condition
    - `recordKey` `greater than` `Session#2022-10-14`

In which case you'd get:

```json
[
  {
    "userId": "user-001",
    "recordKey": "Session#2022-10-15"
  },
  {
    "userId": "user-001",
    "recordKey": "Session#2022-10-23"
  }
]
```

*Caveat: Since recordKey is a `string` attribute, ASCII sort is employed, meaning"
Session#2022-10-10" would be sorted before "Session#2022-10-2".*




