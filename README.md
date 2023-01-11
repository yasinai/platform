# Platform
Shared infrastructure for the Microservice March demo architecture

## Usage (WIP)
The aim is to set things up as manually as possible without creating "magic" via docker compose or involving something heavy like kubernetes.  Future iterations will have examples of those setups

### Build Containers
Make sure you have the following additional repos checked out:
* [notifier](https://github.com/microservices-march-2022/notifier)
* [messenger](https://github.com/microservices-march-2022/messenger)

1. From the `notifier` repository, run `docker build -t messenger:1 .`
1. From the `messenger` repository, run `docker build -t notifier:1 .`

### Start up Platform
This starts up shared infrastructure.
1. From this repo, run `docker-compose up` (you can run it with `-d` to have it run in background if desired)

### Start the `messenger` service
1. From the `messenger` repository, run `docker-compose up` (you can run it with `-d` to have it run in background if desired) to start the postgres database.
1. Start the service in a container configured: `docker run --rm -p 8083:8083 --name messenger -e PGPASSWORD=postgres -e CREATE_DB_NAME=messenger -e PGHOST=messenger-db-1 -e AMQPHOST=rabbitmq -e AMQPPORT=5672 -e PORT=8083 --network mm_2023 messenger:1`
1. Go into the container to set up the db: `docker exec -it messenger /bin/bash`
1. Create the db: `PGDATABASE=postgres node bin/create-db.mjs`
1. Create the tables: `node bin/create-schema.mjs`
1. Create seed data `node bin/create-seed-data.mjs`

### Start the `notifier` service
1. From the `notifier` repository, run `docker-compose up` (you can run it with `-d` to have it run in background if desired) to start the postgres database.
1. Start the service in a container configured: `docker run --rm -p 8084:8084 --name notifier -e PGPASSWORD=postgres -e CREATE_DB_NAME=notifier -e PGHOST=notifier-db-1 -e AMQPHOST=rabbitmq -e AMQPPORT=5672 -e PORT=8084 -e PGPORT=5433 --network mm_2023 notifier:1`
1. Go into the container to set up the db: `docker exec -it notifier /bin/bash`
1. Create the db: `PGDATABASE=postgres node bin/create-db.mjs`
1. Create the tables: `node bin/create-schema.mjs`
1. Create seed data `node bin/create-seed-data.mjs`

### Use the Service
Follow the instructions [here](https://github.com/microservices-march-2022/messenger#using-the-service) to test out the service. You should see notification logs coming from the `notifier:1` container.  You can see them easily by running `docker logs -f notifier`

## RabbitMQ (Message Queue)
Message queues are an important tool in microservice architectures to allow us to further decouple services from each other.
