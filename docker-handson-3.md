### Exposing a Port

Run a simple, static python server that:

- runs its service on port `8000`
- this port should be mapped on `9090` of the docker host

```bash
$ docker run -it -v $(pwd)/message:/message.html -p 9090:8000 python:3-alpine python -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

- `-p` configuration flag for mapping a port: <host-port>:<container-port>

**Access it from Host System:**

```bash
$ curl localhost:9090/message.html
```

**Note:**

- Containers cannot have public IPv4 addresses by default
- they only have private addresses
- services have to be exposed port by port
- port have to be mapped to avoid conflicts

Port mapping allows us to run services that run on the same port:

```bash
$ docker run -d -v $(pwd)/message:/message.html -p 9091:8000 python:3-alpine python -m http.server
$ docker run -d -v $(pwd)/message:/message.html -p 9092:8000 python:3-alpine python -m http.server
$ docker run -d -v $(pwd)/message:/message.html -p 9093:8000 python:3-alpine python -m http.server
```

To know which ports are mapped you can use `docker port`:

```bash
$ docker port <container-id>
8000/tcp -> 0.0.0.0:9093
```



### Entering a container at runtime



```bash
$ docker exec -it 
```



### Communication between containers



Create a network:

```bash
$ docker network create testnetwork
```

Start a container within this network, exposing a server:

```bash
$ docker run --name server --net testnetwork -d -v $(pwd)/message:/message.html python:3-alpine python -m http.server
```

Ping that Server from within another container:

```bash
$ docker run --name pinger --net testnetwork -it nicolaka/netshoot ping server
```

Congratulations!

**Docker inspect:**

`docker inspect`is an advanced command, that can retrieve a ton of information about our containers

```bash
$ docker inspect server

# Get the IP Address of a Container
$ docker inspect server --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}'
```

More examples: https://docs.docker.com/engine/reference/commandline/inspect/

### Bonus:

Starting a container in a containers network namespace

```bash
$ docker run -it --net container:<container-id> nicolaka/netshoot curl http://server:8000/message.html
$ docker run -it --net container:ac1120c15b8e nicolaka/netshoot netstat -tulpn
```

