# Platform
All the platform components for the microservices March demo

## Usage
Run `docker-compose up`

## Setting everything up (WIP)
The aim is to set things up as manually as possible without creating "magic" via docker compose or involving something heavy like kubernetes.  Future iterations will have examples of those setups

1. From this repo, run `docker-compose up`
1. From the `messenger` repo, run `docker-compose up`
1. `docker network connect mm_2023 rabbitmq` and `docker network connect mm_2023 messenger-dev-1`
1. From the `messenger` repo build the messenger container as `messenger:1`: `docker build -t messenger:1 .`
1. `docker run --rm -p 8083:8083 --name messenger_1 -e PGPASSWORD=postgres -e CREATE_DB_NAME=messenger -e PGHOST=messenger-db-1 -e AMQPHOST=rabbitmq -e AMQPPORT=5672 -e PORT=8083 --network mm_2023 messenger:1`
1. Go into the container to set up the db: `docker exec -it messenger_1 /bin/bash`
1. Create the db: `PGDATABASE=postgres node bin/create-db.mjs`
1. Create the tables: `node bin/create-schema.mjs`
1. Create seed data `node bin/create-seed-data.mjs`
1. Now start the notifier with `docker run --rm -p 8084:8084 --name notifier -e PGPASSWORD=postgres -e CREATE_DB_NAME=notifier -e PGHOST=notifier-db-1 -e AMQPHOST=rabbitmq -e AMQPPORT=5672 -e PORT=8084 --network mm_2023 notifier:1`
1. Go into the container to set up the db: `docker exec -it notifier /bin/bash`
1. Create the db: `PGDATABASE=postgres node bin/create-db.mjs`
1. Create the tables: `node bin/create-schema.mjs`
1. Create seed data `node bin/create-seed-data.mjs`

This depends on a shared bridge network called `mm_2023` and we're reusing the dev 

## RabbitMQ (Message Queue)
Message queues are an important tool in microservice architectures to allow us to further decouple services from each other.

https://x-team.com/blog/set-up-rabbitmq-with-docker-compose/