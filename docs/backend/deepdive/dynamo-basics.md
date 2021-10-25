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

### [Secondary Indices](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SecondaryIndexes.html){target=_blank}

Secondary indices in DynamoDB are copies of the data grouped and sorted by attributes different to
main table.

Let's look at this example:

```json
[
  {
    "userId": "user-001",
    "recordKey": "Profile",
    "timestamp": 12345
  },
  {
    "userId": "user-002",
    "recordKey": "Profile",
    "timestamp": 23456
  },
  {
    "userId": "user-003",
    "recordKey": "Profile",
    "timestamp": 34567
  }
]
```

You'd like to query for a list of profile objects, but you want only the ones created after a
certain time.

Normally you can't, since you can only query by `HASH` key, in this case, `userId`.

With secondary indices you can.

All that is required is a secondary index created for the table with `recordKey` as `HASH`
and `timestamp` as `SORT`. After which you may perform queries on the index just as you would on the
main table.

One thing of notes is that, `HASH` `SORT` combinations on a secondary index are not unique, meaning
there may exist more than 1 records with the same `recordKey` plus `timestamp`.

This is also why you cannot perform single get operations on indices.

### References

[This](https://www.dynamodbguide.com/){target=_blank} is another excellent guide on DynamoDB.
