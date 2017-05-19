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
 
 update configuration of docker daemon to use 
 To configure the Docker daemon as a Prometheus target, you need to specify the metrics-address. The best way to do this is via the daemon.json, which is located at one of the following locations by default. If the file does not exist, create it.

    Linux: /etc/docker/daemon.json

If the file is currently empty, paste the following:

{
  "metrics-addr" : "127.0.0.1:9323",
  "experimental" : true
}

If the file is not empty, add those two keys, making sure that the resulting file is valid JSON. Be careful that every line ends with a comma (,) except for the last line.

Save the file, or in the case of Docker for Mac or Docker for Windows, save the configuration. Restart Docker.

Docker now exposes Prometheus-compatible metrics on port 9323.
Restart daemon using below command
Send a HUP signal to the daemon to cause it to reload its configuration. On Linux hosts, use the following command.

$ sudo kill -SIGHUP $(pidof dockerd)


