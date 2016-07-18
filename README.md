Deploying Titan using Machine + Compose + Swarm
=================================

Note: In this example we are going to use `virtualbox` as our provider.
You can replace it for other `docker-machine` [supported drivers](https://docs.docker.com/v1.11/machine/drivers/).

### Requirements

- [Docker](https://docs.docker.com/v1.11/engine/installation/)
- [Docker Machine](https://docs.docker.com/v1.11/machine/install-machine/)
- [Docker Compose](https://docs.docker.com/v1.11/compose/install/)
- VirtualBox (for local usage)

### Running your API

Let's create a docker-machine to our Titan API.

```sh
docker-machine create -d virtualbox titan-api
```

Now we can use that docker-machine environment to start our Titan API.

```sh
eval $(docker-machine env titan-api)
```

```sh
docker-compose up -d titan
```

### Creating a worker swarm

```sh
docker run swarm create
```

Note: This command will return a token. Save that token and replace every `SWARM-TOKEN` for it.

### Creating a worker master

Now we create our swarm master using the token given by the last command.

```sh
docker-machine create -d virtualbox --swarm --swarm-master --swarm-discovery=token://SWARM-TOKEN worker-master
```

### Adding worker machines to our Swarm

You can use this command to add as many machines as you want

Note: Replace the `ID` to a number to identify the new worker

```sh
docker-machine create -d virtualbox --swarm --swarm-discovery=token://SWARM-TOKEN worker-ID
```

### Running our titan workers

Let's change our environment to the worker master

```sh
eval $(docker-machine env --swarm worker-master)
```

And start our first titan worker using Docker Compose.

```sh
TITAN_API=$(docker-machine ip titan-api) docker-compose up -d worker
```

Now we can scale the number of titan workers running in our swarm

```sh
TITAN_API=$(docker-machine ip titan-api) docker-compose scale worker=10
```