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
### Buiding Docker Images

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

### Exercise 2.04 Notes

The exercise is broken. The image file isn't available I used the following:

__Dockerfile:__
```
# WORKDIR, COPY and ADD example
FROM ubuntu:latest
RUN apt-get update && apt-get install apache2 -y
WORKDIR /var/www/html/
COPY index.html .
ADD https://upload.wikimedia.org/wikipedia/commons/thumb/1/1f/AT-6C_Texans_in_flight_1943.jpg/1280px-AT-6C_Texans_in_flight_1943.jpg AT-6.jpg
CMD ["ls"]
```
__index.html__
```
<html>
  <body>
    <h1>Welcome to The Docker Workshop</h1>
    <img src="AT-6.jpg" height="350" width="500"/>
  </body>
</html>
```
I didn't bother changing the image dimensions since it wasn't part of the exercise. 

## Chapter 3 - Managing Your Docker Images

### Docker Layers and Caching

Images consists of layers. Each command adds a layer to your image.  When the image is ran, readable and writeable layers are created.  The writable layer is known as a __container image__. 

These layers can be viewed using the _docker history \<image name>_ command:

```
$docker history workdir-copy-add:latest
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
15296942a557   29 minutes ago   CMD ["ls"]                                      0B        buildkit.dockerfile.v0
<missing>      29 minutes ago   ADD https://upload.wikimedia.org/wikipedia/c…   164kB     buildkit.dockerfile.v0
<missing>      29 minutes ago   COPY index.html . # buildkit                    139B      buildkit.dockerfile.v0
<missing>      29 minutes ago   WORKDIR /var/www/html/                          0B        buildkit.dockerfile.v0
<missing>      29 minutes ago   RUN /bin/sh -c apt-get update && apt-get ins…   152MB     buildkit.dockerfile.v0
<missing>      2 months ago     /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>      2 months ago     /bin/sh -c #(nop) ADD file:5601f441718b0d192…   78.1MB
<missing>      2 months ago     /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B
<missing>      2 months ago     /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B
<missing>      2 months ago     /bin/sh -c #(nop)  ARG LAUNCHPAD_BUILD_ARCH     0B
<missing>      2 months ago     /bin/sh -c #(nop)  ARG RELEASE                  0B
```
_docker image inspect \<image name>_ can provide addition information about the image as well. 

### Exercise 3.01 Notes

__NOTE:__ If you are using the docker file from this repository you'll need to uncomment the line that adds _wget_ and _curl_.


__Dangling images__

You may see an image where the TAG and IMAGE ID are set to \<none>:

```
REPOSITORY   TAG      IMAGE ID        CREATED           SIZE
basic-app    latest   c7918f4f95b9    25 seconds ago    8.8MB
<none>       <none>   0e86ae52098d    2 minutes ago     7.48MB
Alpine       latest   961769676411    5 weeks ago       5.58MB
```

These are image layers not associated with an image. These should be removed as they no longer have a purpose.  You can use the _docker image inspect_ command to view information about the layers. 

``` docker image inspect  0e86ae52098d```

The "Data" section of the output shows where the layer is stored on your system:

```
... 
  "Data": {
    "LowerDir": "/var/lib/docker/overlay2/
      41230f31bb6e89b6c3d619cafc309ff3d4ca169f9576fb003cd60fd4ff
      4c2f1f/diff:/var/lib/docker/overlay2/
      b8b90262d0a039db8d63c003d96347efcfcf57117081730b17585e163f
      04518a/diff",
    "MergedDir": "/var/lib/docker/overlay2/
      c7ea9cb56c5bf515a1b329ca9fcb2614f4b7f1caff30624e9f6a219049
      32f585/
      merged",
    "UpperDir": "/var/lib/docker/overlay2/
      c7ea9cb56c5bf515a1b329ca9fcb2614f4b7f1caff30624e9f6a21904
      932f585/diff",
    "WorkDir": "/var/lib/docker/overlay2/
      c7ea9cb56c5bf515a1b329ca9fcb2614f4b7f1caff30624e9f6a21904
      932f585/work"
  },
```
To see the amount of space used by your images execute:
```
du -sh /var/lib/docker/overlay2/
```
For a quick look at the space used by docker images and layers you can use use ```docker images -a```:

```
REPOSITORY   TAG      IMAGE ID      CREATED          SIZE
basic-app    latest   c7918f4f95b9  25 seconds ago   8.8MB
<none>       <none>   0e86ae52098d  2 minutes ago    7.48MB
<none>       <none>   112a4b041305  11 minutes ago   7MB
Alpine       latest   961769676411  5 weeks ago      5.58MB
```

To clean up, you can execute ```docker image prune```. 

### Exercise 3.02 Notes

Review the Dockerfile from this repository if you are using it. You'll need to modify the docker file for later exercises. 

**IF** you wanted to get rid of all the images on a system then you can use ```docker rmi -f $(docker images -a -q)```. The output would look something like this:

```
Untagged: basic-app:latest
Deleted: sha256:10c414958643f87f05b9639c609ce53319b96057f39fed93f76e3fde55bf7d39
```
The shasum is the __IMAGE ID__. Before running the _docker rmi_ command you could list your images first so you can validate which was image was deleted. 

Performing the first build showed there were 12 layers:

```
IMAGE          CREATED             CREATED BY                                      SIZE      COMMENT
d3e682506e1b   3 minutes ago       RUN /bin/sh -c cat Dockerfile # buildkit        0B        buildkit.dockerfile.v0
<missing>      3 minutes ago       RUN /bin/sh -c rm /tmp/Dockerfile.tar.gz # b…   0B        buildkit.dockerfile.v0
<missing>      3 minutes ago       RUN /bin/sh -c tar -zxvf /tmp/Dockerfile.tar…   443B      buildkit.dockerfile.v0
<missing>      3 minutes ago       COPY Dockerfile.tar.gz /tmp/ # buildkit         482B      buildkit.dockerfile.v0
<missing>      3 minutes ago       WORKDIR /var/www/html/                          0B        buildkit.dockerfile.v0
<missing>      3 minutes ago       CMD ["/bin/sh" "-c" "mkdir /var/www/html/"]     0B        buildkit.dockerfile.v0
<missing>      3 minutes ago       CMD ["/bin/sh" "-c" "mkdir /var/www/"]          0B        buildkit.dockerfile.v0
<missing>      3 minutes ago       RUN /bin/sh -c wget -O test.txt https://gith…   105MB     buildkit.dockerfile.v0
<missing>      10 minutes ago      RUN /bin/sh -c apk add wget curl # buildkit     6.44MB    buildkit.dockerfile.v0
<missing>      About an hour ago   RUN /bin/sh -c apk update # buildkit            2.32MB    buildkit.dockerfile.v0
<missing>      3 weeks ago         /bin/sh -c #(nop)  CMD ["/bin/sh"]              0B
<missing>      3 weeks ago         /bin/sh -c #(nop) ADD file:99093095d62d04215…   7.8MB
```
The time it took to build the image as shown by the time command was:

```
real    0m6.715s
user    0m0.388s
sys     0m0.219s
```


Combining the run commands reduced the number of layers in the image.

```
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
c4b5172bbb4c   53 seconds ago   RUN /bin/sh -c cat Dockerfile # buildkit        0B        buildkit.dockerfile.v0
<missing>      53 seconds ago   RUN /bin/sh -c tar -zxvf /tmp/Dockerfile.tar…   443B      buildkit.dockerfile.v0
<missing>      53 seconds ago   COPY Dockerfile.tar.gz /tmp/ # buildkit         482B      buildkit.dockerfile.v0
<missing>      53 seconds ago   WORKDIR /var/www/html/                          0B        buildkit.dockerfile.v0
<missing>      53 seconds ago   CMD ["/bin/sh" "-c" "mkdir /var/www/html/"]     0B        buildkit.dockerfile.v0
<missing>      53 seconds ago   CMD ["/bin/sh" "-c" "mkdir /var/www/"]          0B        buildkit.dockerfile.v0
<missing>      53 seconds ago   RUN /bin/sh -c wget -O test.txt https://gith…   105MB     buildkit.dockerfile.v0
<missing>      56 seconds ago   RUN /bin/sh -c apk update && apk add wget cu…   8.76MB    buildkit.dockerfile.v0
<missing>      3 weeks ago      /bin/sh -c #(nop)  CMD ["/bin/sh"]              0B
<missing>      3 weeks ago      /bin/sh -c #(nop) ADD file:99093095d62d04215…   7.8MB
```

And the build time was reduced by 0.515 seconds
```
real    0m6.200s
user    0m0.381s
sys     0m0.267s
```
Additional time can be saved when building images with the __--cache-from__ option. For example:

```
docker build --cache-from basic-base -t basic-app .
```

#### Docker commit notes

The **_docker commit_** allows you to create a new image from an existing container.  In the lab the basic-app container is executed:

```
docker run -it basic-app sh
```
In the working directory of the image a new text file was created.

Once the change was made the following command was executed to create a new image from the running basic-app container:

```
docker commit ede3d51bba9e basic-app-test
```
A ```docker history``` on the image showed the addition of the file.

```
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
f920dfc6b302   14 minutes ago   sh                                              34B
d2919acc6dfe   26 minutes ago   RUN /bin/sh -c cat Dockerfile # buildkit        0B        buildkit.dockerfile.v0
<missing>      26 minutes ago   ADD Dockerfile.tar.gz /var/www/html # buildk…   443B      buildkit.dockerfile.v0
<missing>      32 minutes ago   WORKDIR /var/www/html/                          0B        buildkit.dockerfile.v0
<missing>      32 minutes ago   CMD ["/bin/sh" "-c" "mkdir -p /var/www/html"]   0B        buildkit.dockerfile.v0
<missing>      32 minutes ago   RUN /bin/sh -c wget -O test.txt https://gith…   105MB     buildkit.dockerfile.v0
<missing>      32 minutes ago   RUN /bin/sh -c apk update && apk add wget cu…   8.76MB    buildkit.dockerfile.v0
<missing>      3 weeks ago      /bin/sh -c #(nop)  CMD ["/bin/sh"]              0B
<missing>      3 weeks ago      /bin/sh -c #(nop) ADD file:99093095d62d04215…   7.8MB
```

Executing ```docker run basic-app-test cat prod_test_data.txt```. displayed the contents of the prod_test-data.txt file.

llama3 had this to say about __docker commit__:

The `docker commit` command is used to create a new Docker image from an existing container. It's a powerful tool that allows you to save changes made to a running container as a new image, which can
then be used to spin up new containers.

Here's the basic syntax:
```
docker commit <container_id> [<repository_name>][:<tag>]
```
Where:

* `<container_id>` is the ID of the container you want to commit. You can find this by running `docker ps -a` and looking at the CONTAINER ID column.
* `<repository_name>` is the name of the new image that will be created. If you omit this, Docker will use the same name as the original container.
* `<tag>` is an optional tag for the new image.

Here's what happens when you run `docker commit`:

1. Docker creates a new image by capturing the file system changes made to the container.
2. The new image is created with the specified repository name and tag (if provided).
3. The image is based on the same base image as the original container, but it includes all the changes made to the container.

Some key things to keep in mind:

* When you commit a container, Docker creates a new layer on top of the existing layers that make up the original container's image.
* The new image has its own unique ID and is not directly related to the original container's ID.
* If you want to preserve the changes made to the original container, you should commit it regularly or create a snapshot (more on this later).
* You can also use `docker commit` to update an existing image. For example, if you have an image named "my-image" and you've made some changes to a container based on that image, you can run `docker
commit <container_id> my-image:latest` to create a new version of the image with the latest changes.

Some common use cases for `docker commit` include:

* Creating a snapshot of a running container to preserve its state.
* Updating an existing image with new features or bug fixes.
* Creating a custom base image for other containers.

Remember that when you commit a container, it's essentially creating a "moment in time" snapshot of the container's file system. If you want to create a reusable and maintainable image, it's often better
to use `docker build` instead, which allows you to define an image using a Dockerfile.



