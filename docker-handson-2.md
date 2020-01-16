### Writing the first `Dockerfile`

```bash
$ mkdir myimage
$ cd myimage
$ vim Dockerfile # use whatever editor you want
```

`Dockerfile`:

```bash
FROM ubuntu
RUN apt-get update && apt-get -y install figlet
```

- `FROM`indicates the base image for our build
- `RUN`will by executed by Docker during the build
  - must be non-interactive (no input can be provided to Docker during the build)

**Build it:**

```bash
$ docker build -t figlet .
Sending build context to Docker daemon  2.048kB
Step 1/2 : FROM alpine
 ---> cc0abc535e36
Step 2/2 : RUN apk add figlet
 ---> Running in 7fa90af7d37b
fetch http://dl-cdn.alpinelinux.org/alpine/v3.11/main/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/v3.11/community/x86_64/APKINDEX.tar.gz
(1/1) Installing figlet (2.2.5-r0)
Executing busybox-1.31.1-r8.trigger
OK: 6 MiB in 15 packages
Removing intermediate container 7fa90af7d37b
 ---> 75ab3d082245
Successfully built 75ab3d082245
Successfully tagged figlet:latest
```

- `-t` indicates the tag to apply to the image
- `.` indicates the location of the build context
- the build context is the `.`directory givent o `docker build`
- it is sent (as an archive) by the Docker blient to the Docker daemon
- this allows to use a remote machine to build using local files

**Run it:**

```bash
$ docker run -ti figlet
/# figlet hello
```

**Caching**

- if you run the same build again, it will be instantaneous
- Docker takes a snapshot of the resulting image
- before executing a step, Docker checks if it has already built the same sequence
  - `RUN apk add figlet cowsay` is different from `RUN apk add figlet cowsay`
- rebuilds can be foreced with `--no-cache`flag

### Defining a command

When people run our container, we want to greet them with a nice hello message:

```bash
figlet "hello stranger!"
```

`Dockerfile`:

```bash
FROM alpine
RUN apk add figlet
CMD figlet "hello stranger!"
```

- `CMD`defines a default command to run when none is given
- it can appear at any point in the file
- each `CMD`will replace and override the previous one
- as a result, while you can have multiple `CMD`lines, it is useless

```bash
$ docker build -t figlet .
$ docker run figlet
```

### Adding Files to the Container

Create a textfile, containing a message:

```bash
echo "Cowabunga!" > message
```

Add `COPY` to your dockerfile and adjust the `CMD

```bash
FROM alpine
RUN apk add figlet
COPY ./message .
CMD figlet $(cat message)
```

- `COPY`will copy files into the container

Some Details:

- you can `COPY`whole directories recursively
- older Dockerfiles also have the `ADD` instruction: it is similar but can automatically extract archives

### Mounting a directory

`Dockerfile`:

```bash
FROM alpine
RUN apk add figlet
CMD figlet $(cat message)
```

```bash
docker run -d -v $(pwd)/message:/message figlet
```

- `-v` configures the host directory which should be mounted inside the container