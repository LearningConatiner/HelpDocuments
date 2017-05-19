##Folow the below steps in ubunti to upgrade docker##
Here is how to upgrade Docker using the official apt source.

First, install the necessary repo key.
$ sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D 

Then add the apt source of your distribution as follows.
$ sudo sh -c "echo 'deb https://apt.dockerproject.org/repo ubuntu-$(lsb_release -sc) main' | cat > /etc/apt/sources.list.d/docker.list"

Next, remove existing Docker installation and clean up related configuration.
$ sudo apt-get purge docker.io

Finally, install the latest Docker from the official source.
$ sudo apt-get update; sudo apt-get install docker-engine

Verify the version of Docker that you installed.
root@sri-VirtualBox:/etc/docker# docker version
Client:
 Version:      17.05.0-ce
 API version:  1.29
 Go version:   go1.7.5
 Git commit:   89658be
 Built:        Thu May  4 22:10:54 2017
 OS/Arch:      linux/amd64

Server:
 Version:      17.05.0-ce
 API version:  1.29 (minimum version 1.12)
 Go version:   go1.7.5
 Git commit:   89658be
 Built:        Thu May  4 22:10:54 2017
 OS/Arch:      linux/amd64
 Experimental: false

