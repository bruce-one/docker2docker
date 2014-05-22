# Docker-in-Docker: back with a vengeance

This recipe lets you run Docker-in-Docker, with a twist. Instead of nesting
containers, it runs a virtual machine within Docker, and it uses [boot2docker]
to run Docker within that virtual machine.

It's using KVM to start the virtual machine, so it is intended for people
running Docker on bare metal. Nested virtualization might or might not work,
or be just a pipe dream, I don't know. 


## How to use this

Clone 'dat repo. Build it. Run in `--privileged` mode on a Linux machine
with KVM support. Check the IP address of the container; then you can
connect using:

- SSH on port 22 (user=docker, password=tcuser or user=ubuntu, password=docker for the Ubuntu image)
- Docker on port 4243
- VNC on port 5900


## Persistence

If you bind-mount an empty file on /boot2docker.hdd, it will be turned into
a 10 GB sparse file and formated as an ext4 filesystem and used as the
data volume for boot2docker. If you want something larger/smaller, just
format it yourself, and make sure that the label is boot2docker-data.

## Folder sharing

When using kvm-start, KVM mounts the /mnt directory from the docker host to
the KVM host. Meaning, when starting the main docker container mounting /mnt
as a volume to the host (eg `-v /host_dir:/mnt`) will be mounted into the VM.
This means that containers running in the VM can then use /mnt as a volume
(eg `docker -H tcp://{{docker2docker}}:4243 -v /mnt:/container_dir`) will
result in a directory that's shared between the host and the container.

[boot2docker]: https://github.com/boot2docker/boot2docker/
