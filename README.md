# Container Environment Setup Guide

To set up a container environment using command lines, follow these steps:

### 1. Download a Linux filesystem image

You can use Docker to download the Linux filesystem image. Execute the following commands:

```sh
mkdir /tmp/debian-fs
cd /tmp/debian-fs
docker pull debian:latest
docker export $(docker create debian) -o debian-fs.tar.gz
tar xvf debian-fs.tar.gz
```

Now you have a ready-to-use filesystem that will serve as your container's root.

### 2. Create necessary namespaces

Create the required namespaces using the following command. You can add more namespaces if needed:

```sh
# -pf -> pid namespace.
# -Ur -> user namespace.
# -m -> mount namespace.
# -u -> UTS namespace.
unshare -pf -Ur -m -u /bin/bash
```

### 3. Set the new hostname and check namespaces

Set a new hostname and verify if the new namespaces were created successfully:

```sh
hostname test-container # hostname gives the new hostname
echo $$ # pid is 1
id # we are root in this new user namespace and we have all capacities.
```

### 4. Change root to the Debian filesystem

Change the root to the Debian filesystem using `chroot`:

```sh
chroot /tmp/debian-fs /bin/bash
export PATH=/bin:/usr/bin
```

### 5. Mount the proc virtual filesystem

Mount the proc virtual filesystem:

```sh
mount -t proc proc /proc
ls -l /proc # Verify that the PID is isolated.
```
> You can mount other virtual fs if needed (tmp, sys, dev...etc)

> Note: Additional configurations can be performed in the container environment.
