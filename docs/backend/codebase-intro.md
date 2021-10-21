## Introduction to the Nova Codebase(Backend)

---

### Rough Outline

The codebase can generally be split into 4 levels of abstraction.

They are:

- Business
    - Easy to read
    - Business logic only
    - Calls Facade/Storage level modules
- Facade
    - Easy to read with some business domain knowledge
    - Consolidates multiple Storage level operations + reusable logic
    - Calls Storage level modules
- Storage
    - Harder to read without a deeper understanding of business logic
    - Your main gateway of interaction with DynamoDB, and in some cases, Redis
    - Calls integration level modules
- Integration
    - Very hard to read without AWS resource specific knowledge
    - Lower level
    - Calls third party sdks, mainly integrates with AWS resources.

### Business Level

Example of a business level function:

```js
export async function handler(event, context) {
    const {email, password} = JSON.parse(event.body);
    if(!email || !password) {
        return failure(ErrorResponse.MISSING_CREDENTIAL);
    }

    const sanitizedEmail = email.toLowerCase().trim();
    const cred = await adminStorage.tryGetCredential(sanitizedEmail);
    if(!cred) {
        return failure(ErrorResponse.INVALID_CREDENTIAL);
    }

    const {userId} = cred;

    const fiveDays = 1000 * 60 * 60 * 24 * 5;

    if(await adminStorage.matchPassword(userId, password)) {
        const session = await StorageFacade.invalidateAndCreateSession(userId, fiveDays);
        const profile = await adminStorage.getProfile(userId);
        return success({
            profile,
            session
        });
    } else {
        return failure(ErrorResponse.INVALID_CREDENTIAL);
    }
}
```

The name of the function has been deliberately removed, but can you guess what this function does?

Most people can probably guess with a pretty high accuracy the purpose of this function, even non
programmers.

It is because this is solely declarative, written in plain English, with little to no black magic.

### Facade Level

A simplified excerpt of the `StorageFacade` that was introduced earlier.

```js
export default class StorageFacade {
    static async createAdminUser(email, password, firstName, lastName, role, credType) {
        const sanitizedEmail = email.toLowerCase().trim();
        const profile = AdminFactory.getNewProfile(sanitizedEmail, firstName, lastName, role);
        const {userId} = profile;
        return await Promise.all([
            adminStorage.putProfile(userId, profile),
            adminStorage.putCredential(userId, email, credType),
            adminStorage.setPassword(userId, password)
        ]);
    }
}
```

Still relatively readable.

### Storage Level

A simplified excerpt of `AdminStorage` that both the `login` function and `StorageFacade` uses.

```js
class AdminStorage extends CompositeStorage {
    //.... omitted
    async putProfile(userId, record) {
        return await this.putSingleton(userId, this.#profileKey, record).then(cleanUpDbFields);
    }

    async getProfile(userId) {
        return this.getSingleton(userId, this.#profileKey).then(cleanUpDbFields);
    }

    async putCredential(userId, identifier, credType) {
        if(await this.credentialExists(identifier)) {
            throw "Trying to put a duplicate credential record!";
        }
        return super.putToCollection(userId, {
            userId,
            identifier,
            credType
        }, this.#credentialKey, identifier).then(cleanUpDbFields);
    }

    async tryGetCredential(identifier) {
        const creds = await DynamoDB.queryIndex(this.table, reverseIndex, {[this.sortName]: this.genKey(this.#credentialKey, identifier)});
        if(creds.length > 0) {
            return cleanUpDbFields(creds[0]);
        } else {
            return null;
        }
    }

    //.... omitted
}
```

You may have noticed immediately that, readability dropped by quite a bit.

Understanding a `Storage` class requires knowing how `DynamoDB` stores data, what our concept of
a `singleton` and a `collection` is, and what `Local` and `Global Secondary Indexes` are and how
they work.

It is not recommended for newcomers to immediately start creating or modifying a `Storage` class.

A separate entry will be written on our data structure and DynamoDB fundamentals.  

### Integration

These classes deal with integration with AWS/other 3rd party services.

Excerpt from the `DDBClient` class.

```ts
export class DDBClient {
    // ...much omitted
    async get(key: any, projection?: Projections, consistent?: boolean): Promise<any> {
        const param: any = {
            TableName: this.table,
            Key: key,
            ConsistentRead: consistent
        };
        const builder = new ExpressionBuilder();
        if(projection) {
            projectionPaths2Expression(projection, builder);
        }
        Object.assign(param, builder.build());
        return docClient.get(param).promise().then(res => res.Item);
    }

    async update(key: any, action: UpdateAction, conditions?: Conditions): Promise<any> {
        const param: any = {
            TableName: this.table,
            Key: key,
            ReturnValues: "ALL_NEW"
        };
        const builder = new ExpressionBuilder();
        action.addToBuilder(builder);
        if(conditions) {
            conditions.addToBuilder(builder)
        }
        Object.assign(param, builder.build());
        return docClient.update(param).promise().then(res => res.Attributes).catch(e => {
            if(e.code === "ConditionalCheckFailedException") {
                return null;
            }
            throw e;
        });
    }

    async batchWrite(items: any[], recursive: boolean = true): Promise<BatchWriteResult> {
        return this.batch(items, [], recursive);
    }

    async batchDelete(keys: any[], recursive: boolean = true) {
        return this.batch([], keys, recursive);
    }

    async batch(items: any[], delKeys: any[], recursive: boolean): Promise<BatchResult> {
        const result: BatchResult = {
            unprocessedWrites: [],
            unprocessedDeletes: []
        };

        const requests: any[] = [...items.map(i => ({
            PutRequest: {Item: i}
        })), ...delKeys.map(k => ({
            DeleteRequest: {Key: k}
        }))];

        do {
            const removed = requests.splice(0, 25);
            const output = await docClient.batchWrite({
                RequestItems: {
                    [this.table]: removed
                }
            }).promise();
            if(output.UnprocessedItems && output.UnprocessedItems[this.table]) {
                for(const item of output.UnprocessedItems[this.table]) {
                    if(item.PutRequest) {
                        result.unprocessedWrites.push(item.PutRequest.Item);
                    } else if(item.DeleteRequest) {
                        result.unprocessedDeletes.push(item.DeleteRequest.Key);
                    }
                }
            }
        } while(recursive && requests.length > 0);

        if(requests.length > 0) {
            for(const request of requests) {
                if(request.PutRequest) {
                    result.unprocessedWrites.push(request.PutRequest.Item);
                } else if(request.DeleteRequest) {
                    result.unprocessedDeletes.push(request.DeleteRequest.Key);
                }
            }
        }

        return result;
    }
}
```

Needless to say, readability is very low without intimate knowledge of how `AWS-SDK` works and the
syntax of a `DynamoDB` operation.

These topics are out of the scope of this manual.

Official references:

[AWS JS SDK](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html){target=_blank}

[DynamoDB Expressions](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.html){target=_blank}
