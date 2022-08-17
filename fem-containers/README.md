# Frontend Masters - Complete Intro to Containers

Course site: https://btholt.github.io/complete-intro-to-containers/
Course repo: https://github.com/btholt/complete-intro-to-containers

## Features of containers
* `chroot` - this command changes the root in linux. Typically the root is `/`, but you can change it to whatever. Linux will only have access to files in that root.  It is an isolated environment in that way, a "jailed" process.
* Namespaces - jailed processes above have limited file system access, but they can still see other running processes and possibly manipulate them.  Use `unshare` to limit those capabilities from the jailed process.
* `cgroup` - control groups.  The above mechanisms control access to file system and capabilities in the system, but even an unshared environment has full access to the physical resources of the machine (CPU, memory, etc).  `cgroup` allows you to control that.

Containers combine these above three utilities/concepts to create safe, isolated environments.

# Docker

There is an online registry of docker images and other stuff at https://hub.docker.com

Remember docker images are designed to be ephemeral.  When they shut down, they disappear entirely.

### CLI

* `docker run [options] [start_command]` - run a container.  This is what you'll use as a dev most of the time.  The start command is handy if you want to just quickly run a command on the container instead of the main CMD defined in the image's Dockerfile
  * Start command can often be `bash` to enter the container and start bash
  * Options:
    * `--interactive` - sort of like tails the docker, leaves its stdin output
    * `--tty` - starts a shell session inside the container
    * `--detach` - run in background
    * `--name` - give it a name for referencing later
    * `--rm` - remove container on exit
    * `--init` - this will kill the process when you exit the container (but not remove the container)
* `docker attach {name_or_id}` - attach (start a shell session) to a running container
* `docker kill {name_or_id}` - stop a container
* `docker rm {name_or_id}` - stop and delete all metadata/logs associated with this container
* `docker ps` - show running docker containers on this machine
* `docker logs {name_or_id}`
* `docker image prune` - delete images that have been downloaded to your machine
* `docker pull {image}` - pull an image without running
* `docker inspect {image}` - get lots of data about the image and how it's built
* `docker pause {name_or_id}` - pause the container
* `docker unpause`
* `docker exec {name_or_id} [command]` - run a command against an existing container
* `docker history {image}` - history of changes to the image's dockerfile
* `docker top {name_or_id}` - list all processes running in this container

### Tags
When you `docker run node:12`, that's saying to run the docker image called `node` with the tag `12`.  Tags aren't necessarily semvers, they can kinda be anything.  Go to the image page on docker hub to find more.

### Dockerfile
`Dockerfile` is what is used to build the image for our app.  You can run `docker build [dir]` where `[dir]` is a directory with a `Dockerfile`, and it will make an image for you

* `docker build --tag my-node-app .` - apply a tag to a build

* By default, containers are run as root user.  You can change that in the Dockerfile with `USER` entry.
* `COPY` copies files from local filesystem to container
* `ADD` can do the same but also pull data over the network, unpack it, etc.
* `WORKDIR` changes the working directory

### Running an app in a container

You can build a webserver, provide a dockerfile for it and build it.  But you can't access the container to reach the webserver because it's isolated.  So you can use the `--publish` CLI option to `docker run` to expose a container's port to the host

You can then run the container exposing the port like so `docker run --init --rm --publish 3000:3000 node-app`

Note that any references to `localhost` in your application code will not work.  You'll have to use `0.0.0.0`

You can also use `EXPOSE 3000` in your Dockerfile, but if you do that you have to run `docker run` with `-P` which will publish a random port. That port is listed in the output of `docker ps`.  Not very convenient, using `--publish` is more straightforward.

#### Layering
Dockerfiles are processed in sequence, and when it sees a diff in that file, it will only process the layers at the diff level and below, leveraging caching for everything before the diff.  This means you might put your more expensive operations higher up, so that changes after those operations will not trigger the expensive stuff when building.

#### .dockerignore
Just like .gitignore. Tells docker what files to remove when it copies.

#### Multi-stage
Dockerfiles can define multiple stages with different base images.  You can also pass things (such as files) from one stage to the next.  This allows you to do things like compile/bundle assets in one stage of the build, and pass the built assets to other stages.  So you could have a build stage with all of the compilers/bundlers you need, and pass those built assets to a small runtime server.