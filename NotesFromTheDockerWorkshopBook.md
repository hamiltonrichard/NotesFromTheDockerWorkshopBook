# Notes From The Docker Workshop Book

These are my notes from the book, "The Docker Workshop" published by packt publishing.

## Chapter 2 - Getting Started with Docker

### Common Directives in Dockerfiles
For additional details click on the directive to see the documentation for the specific directive. For a complete list of Directives see the [Dockerfile referance](https://docs.docker.com/reference/dockerfile/).

|Directive|Description|Example|
|----|----|----|
|[**FROM**](https://docs.docker.com/reference/dockerfile/#from)|Specify the paren image of a custom Docker Image|__FROM ubuntu:20.04__
|[**LABEL**](https://docs.docker.com/reference/dockerfile/#label)|A key-value pair used to provide meta data.These can be seen using the **docker image inspect <image:tag>** command |__LABEL version=1.0__|
|[**RUN**](https://docs.docker.com/reference/dockerfile/#run)|Execute commands during a docker build.|__RUN apt-get update__|
|[**CMD**](https://docs.docker.com/reference/dockerfile/#cmd)|Execute command when the container is launched. The parameters can be overridden from the command line.|__CMD["echo","Hello world!]__|
|[**ENTRYPOINT**](https://docs.docker.com/reference/dockerfile/#entrypoint)|Similar to ***CMD***, but you cannot override it. __CMD__ and __ENTRYPOINT__ can be used to gether.|__ENTRYPOINT ["echo","hello"]__|

### Additional Dockerfile Directives

|Directive|Description|Example|
|----|----|----|
|[**ENV**](https://docs.docker.com/reference/dockerfile/#env)|Set enviornment settings for the running container.|__ENV GPG_TTY=$(tty)__|
|[**ARG**](https://docs.docker.com/reference/dockerfile/#arg)|Define a variable which can be passed at build time. This directive is passed at build time. |__docker image build -t <image>:<tag> --build-arg <varname=value> .__|
|[**WORKDIR**](https://docs.docker.com/reference/dockerfile/#workdir)|Specify the working directory in a container.|__WORKDIR /app__|
|[**COPY**](https://docs.docker.com/reference/dockerfile/#copy)|Copy files from a local system to a container during the build process.Optional parameters include _--chown_ and _--chgrp_|__COPY index.html /var/www/html/index.html__|
|[**ADD**](https://docs.docker.com/reference/dockerfile/#add)|Similar to __COPY__, but has the ability to use a URL as a source.|__ADD http://sample.com/test.txt /tmp/test.txt__|
|[**USER**](https://docs.docker.com/reference/dockerfile/#user)|Specify the user used in a container. The default is root. You can also specify group information. |__USER bob__|
|[**VOLUME**](https://docs.docker.com/reference/dockerfile/#volume)|Used for data persistance and sharing. A directory in the docker image is mapped to local directory on the Docker host.One more more paths can be included.| __VOLUME ["/APP"]__ or __VOLUME/path/to/volume1 /path/to/volume2__|
|[**EXPOSE**](https://docs.docker.com/reference/dockerfile/#expose)|Allows access to a port. The exposed port can be accessed within docker containers or to external resources using the (__-p__) parameter| __EXPOSE -p <host_port> : <remote_port>__ |
|[**HEALTHCHECK**](https://docs.docker.com/reference/dockerfile/#healthcheck)|Used to determine the health of a container.| __HEALTHCHECK CMD curl -f http://localhost/ \|\| exit 1__ |
|[**ONBUILD**](https://docs.docker.com/reference/dockerfile/#onbuild)|When creating a base image, this command will be executed in the child image|ONBUILD ENTRYPOINT __["echo","Running ONBUILD directive"]__||
## Buiding Docker Images

To build an image the following command can be used:

```
docker image build .
```

If an image is built this way, it can only be refranced by the image ID. It is better to use a __TAG__ in the build. This makes it easier to identify the docker image. 

An example of the output from __docker image list__:

```
REPOSITORY   TAG       IMAGE ID        CREATED          SIZE
<none>       <none>    dc3d4fd77861    3 minutes ago    64.2MB
ubuntu       latest    549b9b86cb8d    5 days ago       64.2MB
```

If a tag isn't used during the build, then do something like this:

```
docker image tag dc3d4fd77861 my-tagged-image:v1.0
```
Note updated _REPOSITORY_ and _TAG_ meta data:
```
REPOSITORY        TAG       IMAGE ID        CREATED         SIZE
my-tagged-image   v1.0      dc3d4fd77861    20 minutes ago  64.2MB
ubuntu            latest    549b9b86cb8d    5 days ago      64.2MB
```

The tag can be added to the image build using `-t`:
```
docker image build -t my-tagged-image .
```

You can also specify a version if needed:

```
docker image build -t my-tagged-image:v2.0 .
```
