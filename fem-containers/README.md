# Frontend Masters - Complete Intro to Containers

Course site: https://btholt.github.io/complete-intro-to-containers/
Course repo: https://github.com/btholt/complete-intro-to-containers

## Features of containers
* `chroot` - this command changes the root in linux. Typically the root is `/`, but you can change it to whatever. Linux will only have access to files in that root.  It is an isolated environment in that way, a "jailed" process.
* Namespaces - jailed processes above have limited file system access, but they can still see other running processes and possibly manipulate them.  Use `unshare` to limit those capabilities from the jailed process.
* `cgroup` - control groups.  The above mechanisms control access to file system and capabilities in the system, but even an unshared environment has full access to the physical resources of the machine (CPU, memory, etc).  `cgroup` allows you to control that.

# Docker

There is an online registry of docker images and other stuff at https://hub.docker.com

