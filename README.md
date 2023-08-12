# DynamoDB


See the [DynamoDB documentation](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html) for more information.
Specifically [deploying DynamoDB locally](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.DownloadingAndRunning.html)

## Prerequisites
Create a Docker volume named `dynamodb-data` to persist the data.

## Running

```bash
docker-compose up -d
```

## Accessing the local DynamoDB

> NOTE: The Docker Compose file uses port 9000 to access the DynamoDB instance locally. Not port 8000 as the AWS documentation suggests.

### Using the CLI

```bash
aws dynamodb list-tables --endpoint-url http://localhost:9000
```

### From Python

First export the instance URL as an environment variable:
```bash
export DYNAMODB_URL=http://localhost:9000
ecport TABLE_NAME=UniqueItemTableName
```

Then use the `boto3` library to access the local DynamoDB instance:

```python
import boto3
from typing import Optional
from pydantic_settings import BaseSettings

class Config(BaseSettings):
    TABLE_NAME: str = ""
    DYNAMODB_URL: Optional[str] = None

class Store:
    def __init__(self, table_name, dynamodb_url=None):
        self.table_name = table_name
        self.dynamodb_url = dynamodb_url

    def add(self, item):
        dynamodb = boto3.resource("dynamodb", endpoint_url=self.dynamodb_url)
        table = dynamodb.Table(self.table_name)
        table.put_item(Item=item)

config = Config()
        
def get_store() -> Store:
    return Store(config.TABLE_NAME, dynamodb_url=config.DYNAMODB_URL)
```