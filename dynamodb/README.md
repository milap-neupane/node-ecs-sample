## Intro
- Dynamodb is key value storage document based DB.
- Dynamodb is AWS managed service
- Dynamodb has build in scalability managed by AWS
- Multimaster solution
- Backup solution

## Data types:
Scalar(Number, string), Document(list, maps), Set type(string set, int set etc)

## Attributes
- Need to define the attributes that we want to query on. All other fields can be added without migration

- Primary key can be partition key or a combination of partition key and sort key. Primary key should be unique.

## Designed for Scale

- Dynamodb is designed for scale. It auto replicates accorss multi available zone 
- Provision capacity and needed and auto scaling.

## Querying data
- By default only the primary key can be queried.

- Need to add index as query needed 

- Two types of index:

### Local index
- Needs to be defined at the time of table creation
- Must include partion key and a alternative sort key
- Uses the same provision as for main table
- Can fetch other keys from base table other than defined in index.

### Global index
- Can be defined even after the table is defined
- Partition key is not required
- Provisioned thoughput can be changed
- Only projected attributes can be fetched

### Attribute projections in index
- KEYS_ONLY
- INCLUDE
- ALL
 
### Creating table with cloudformation

#### Table
```
aws cloudformation create-stack --template-body file://table.yaml --stack-name dynamodb-table --profile <profile> --region us-east-1
```

### Inserting and fetching a record in the table

1. Create a file named items.json

```Json
{
  "id": {"S": "RandomId"},
  "name": {"S": "Milap Neupane"},
  "address": {"S": "Nepal"},
  "email": {"S": "milap.neupane@nepal.com"}
}
```

2. Use cli command to create the record:

```
aws dynamodb put-item --table-name users --item file://item.json --profile <profile> --region us-east-
```

3. Fetch all the data in dynamodb:

```
aws dynamodb scan --table-name users --region us-east-1 --profile <profile>
```

4. Find a item

```
aws dynamodb get-item --table-name users --key file://getItemKey.json --region us-east-1 --profile <profile>
```

#### Local secondary index
```
aws cloudformation create-stack --template-body file://localSecondary.yaml --stack-name dynamodb-localSecondary --profile <profile> --region us-east-1
```

1. Insert item
```
aws dynamodb put-item --table-name books --item file://bookitem.json --profile <profile> --region us-east-1
```

2. GetItem
```
aws dynamodb get-item --table-name books --key file://getBookItemKey.json --region us-east-1 --profile <profile>
```

#### Global secondary index
```
 aws cloudformation create-stack --template-body file://globalSecondary.yaml --stack-name dynamodb-globalSecondary --region us-east-1 --profile <profile>
 
```
1. Insert item
```
aws dynamodb put-item --table-name usersWithGlobalIndex --item file://item.json --profile team0workermgmt --region us-east-1
```

2. GetItem
```
aws dynamodb get-item --table-name usersWithGlobalIndex --key file://getItemKey.json --region us-east-1 --profile <profile>
```

3. GetItemFromIndex

```
aws dynamodb query \
   --table-name usersWithGlobalIndex \
   --key-condition-expression "email = :email" \
   --index-name EmailIndex \
   --expression-attribute-values  '{":email":{"S":"milap.neupane@nepal.com"}}' \
   --projection-expression "email" \
   --profile <profile>
```