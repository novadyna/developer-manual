## Nova's Database Structure

---

### [Single Table Design](https://aws.amazon.com/blogs/compute/creating-a-single-table-design-with-amazon-dynamodb/){target=_blank}

Nova's database structure is problematic in its current state.

Below is our most common DynamoDB setup(in cloudformation syntax, simplified):

```yaml
Properties:
  KeySchema:
    - AttributeName: pk
      KeyType: HASH
    - AttributeName: sk
      KeyType: RANGE
  GlobalSecondaryIndexes:
    - IndexName: gsi0-index
      KeySchema:
        - AttributeName: sk
          KeyType: HASH
        - AttributeName: pk
          KeyType: RANGE
    - IndexName: gsi1-index
      KeySchema:
        - AttributeName: sk
          KeyType: HASH
        - AttributeName: gsi1sk
          KeyType: RANGE
    - IndexName: gsi2-index
      KeySchema:
        - AttributeName: gsi2pk
          KeyType: HASH
        - AttributeName: gsi2sk
          KeyType: RANGE
    - IndexName: gsi3-index
      KeySchema:
        - AttributeName: gsi3pk
          KeyType: HASH
        - AttributeName: gsi3sk
          KeyType: RANGE
```

The first thing you may notice, is that attribute names are generic. They only reflect the DB's key
schema.

This was an abuse of
the [Single Table Design](https://aws.amazon.com/blogs/compute/creating-a-single-table-design-with-amazon-dynamodb/){target=_blank}
.

The Single Table Design, simply put, is just placing everything in the same table, and attributes
can have variable meaning according to the object type.

It's a great way to boost performance and save cost, at the cost of developer agility.

It's an abuse in our case mainly because it was done for the wrong reasons.

The intention of picking this route I was told, was to minimize efforts in table design. The
rationale was that, having a very generic table with multiple generic indices eliminates any future
need to modify/update the table. New data objects can be invented on the fly together with new
access patterns.

This is of course, very misguided.

DynamoDB design goes hand in hand with knowing your access patterns beforehand.

Using a single table design without knowledge of access patterns introduces a cognitive overhead,
but brings none of the intended benefits.

### Example Data

Consider this example of an actual event profile, plucked from the development server, with most
non-key attributes stripped:

```json
{
  "pk": "0d48c37b-0b35-40cf-8c36-e23ddc97440f",
  "eventId": "0d48c37b-0b35-40cf-8c36-e23ddc97440f",
  "sk": "Event#Profile",
  "gsi1sk": "355dada4-fb99-4bd9-801f-bea92cd03a1d",
  "channelId": "355dada4-fb99-4bd9-801f-bea92cd03a1d"
}
```

You might have noticed that both `eventId` and `channelId` are duplicated into `pk` and `gsi1sk`
respectively.

In `Nova`'s context, `Events` are scoped under `Channels`.

To retrieve this object, issue a `get` operation with the following parameters:

```txt
// Remember HASH key + SORT key combinations are globally unique?
pk: 0d48c37b-0b35-40cf-8c36-e23ddc97440f
sk: Event#Profile
```

To retrieve a list of `event` objects under the same channel however, a query should be issued:

```txt
Index Name: gsi1-index
sk: Event#Profile (Remember sk is the HASH key for gsi-index?)
gsi1sk: "=" 355dada4-fb99-4bd9-801f-bea92cd03a1d
```

There's a lot of cognitive overhead just to remember the index name, the `HASH` key for that
index, and that `channelId` was overloaded into `gsi1sk`.

This is why one should almost never query the database directly with a single table design.

This is also why we have wrapper classes(`StorageBase`) that encapsulate our data schema and access patterns.
