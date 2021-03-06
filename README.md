# cricd-bowling-processor
This HTTP API is a micro-service that produced statistics of a bowler's performance during a match. Including: runs conceded, wickets taken, no balls, wides, strike rate, economy rate and other stats.
It is intended to provide a component to assist the [cricd](https://github.com/cricd/cricd) project which is an open platform for cricket scoring

## Running the service in Docker
This API is dependent on a connection to an EventStore instance containing cricd match events. The host, port, authentication credentials and Eventstore stream name are all configured using the following environment variables: `EVENTSTORE_HOST`, `EVENTSTORE_PORT`, `EVENTSTORE_USER`, `EVENTSTORE_PASSWORD`, `EVENTSTORE_STREAM`

You can specify these environment variables when running the docker container. For example `docker run -d -p 3001:3001 -e EVENTSTORE_HOST=172.18.0.2 cricd/bowling-processor`

If your EventStore instance is running in a Docker container as well then network connectivity will need to be established between these instances. This is explained in the [Docker networking documentation](https://docs.docker.com/engine/userguide/networking/dockernetworks/) but the steps at a high level are:
1. Create a user defined network using a command like `docker network create --driver bridge cricd-network`
2. Start your EventStore container using the --network parameter `docker run --net=cricd-network`
3. Find the IP address of the EventStore container using the command `docker network inspect cricd-network`
4. Start this Docker container using the `--net=cricd-network` parameter and using the `EVENTSTORE_HOST` variable set to the IP address you just found

Alternatively, you can clone the [code repository for this service](https://github.com/cricd/bowling-processor) and use Docker-Compose to spin up a environment containing both EventStore and this service which removes the need to perform these steps

## Accessing the service
This service exposes a single endpoint at port 3001 by default which responds to GET requests. The service takes 2 parameters: match (Id of the match), innings (the innings number)

For example: `http://localhost:3001?match=10&innings=1`
