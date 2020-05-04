# Hello Kafka Connect with Docker

Instructions: [Debezium Tutorial with Docker Compose](https://github.com/debezium/debezium-examples/blob/master/tutorial/README.md)

## Using Postgres

1. Use `docker-compose` to start Zookeeper, Kafka, Postgres, Conneft

```shell
# Start the topology as defined in https://debezium.io/docs/tutorial/
export DEBEZIUM_VERSION=1.1
docker-compose -f docker-compose-postgres.yaml up

# Start Postgres connector
curl -i -X POST -H "Accept:application/json" -H  "Content-Type:application/json" http://localhost:8083/connectors/ -d @register-postgres.json

# Consume messages from a Debezium topic
docker-compose -f docker-compose-postgres.yaml exec kafka /kafka/bin/kafka-console-consumer.sh \
    --bootstrap-server kafka:9092 \
    --from-beginning \
    --property print.key=true \
    --topic dbserver1.inventory.customers
```

2. Connect to Postgres via [Azure Data Studio](https://docs.microsoft.com/en-us/sql/azure-data-studio/download-azure-data-studio?view=sql-server-ver15)
   - Instructions: [Connect and query PostgreSQL using Azure Data Studio](https://docs.microsoft.com/en-us/sql/azure-data-studio/quickstart-postgres?view=sql-server-ver15)
   - Server name: `localhost`
   - Username: `postgres`
   - Password: `postgres`
   - Database: `postgres`

3. View and update `inventory.customers` table

```sql
select  id
,first_name
,last_name
,email
from inventory.customers
```
```sql
INSERT INTO inventory.customers
(
    id
    ,first_name
    ,last_name
    ,email
)
VALUES
(
    1005
    ,'Mickey'
    ,'Mouse'
    ,'mickey@disney.com'
)
```
- View output from the kafka consumer terminal
- Execute Postgres SQL commands from terminal

```shell
# Modify records in the database via Postgres client
docker-compose -f docker-compose-postgres.yaml exec postgres env PGOPTIONS="--search_path=inventory" bash -c 'psql -U $POSTGRES_USER postgres'
```

- Shut down containers.

```shell
# Shut down the cluster
docker-compose -f docker-compose-postgres.yaml down
```