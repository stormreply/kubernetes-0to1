### A non-interactive container

This container displays the time every second.

```bash
$ docker run jpetazzo/clock
Sun Jan 12 14:30:52 UTC 2020
Sun Jan 12 14:30:53 UTC 2020
Sun Jan 12 14:30:54 UTC 2020
```

- it will run forever it is killed
- `^C`to stop it

### Running a container in the background

```bash
$ docker run -d jpetazzo/clock
f31615d81aa4a91ee9cf90a91d0654d8bcb13bfbac7a11ecf0ccb4d42e53e9c6
```

To see that the container is running:

```bash
$ docker ps
```

- (truncated) ID of the containers
- image used to start the container
- uptime and other information as ports being used, etc.
- Use `-l` flag to see the last started container
- Use `-a` to see also stopped containers
- Use `-n3`to see only last 3

### Viewing container logs

```bash
$ docker logs <container-id>
```

- use `--tail 3` to get the last 3 events
- use `-f` to follow the log

### Stopping a container

```bash
$ docker kill <container-id>
```

- stops the container immediately, by using the `KILL` signal
- a `KILL` signal cannot be intercepted and will forcibly terminate the container

```bash
$ docker stop <container-id>
```

- sends a `TERM` signal to stop a container gracefully
- if the container has not been stopped after 10 seconds, it will get killed
