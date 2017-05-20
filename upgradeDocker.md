##Folow the below steps in ubunti to upgrade docker

Here is how to upgrade Docker using the official apt source.

First, install the necessary repo key.  
`
$ sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D 
`
Then add the apt source of your distribution as follows.
`
$ sudo sh -c "echo 'deb https://apt.dockerproject.org/repo ubuntu-$(lsb_release -sc) main' | cat > /etc/apt/sources.list.d/docker.list"
`

Next, remove existing Docker installation and clean up related configuration.
`
$ sudo apt-get purge docker.io
`
Finally, install the latest Docker from the official source.

`
$ sudo apt-get update; sudo apt-get install docker-engine
`

Verify the version of Docker that you installed.

```
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
 ```
 
##update configuration of docker daemon to use 

 To configure the Docker daemon as a Prometheus target, you need to specify the metrics-address. The best way to do this is via the daemon.json, which is located at one of the following locations by default. If the file does not exist, create it.

    Linux: /etc/docker/daemon.json

If the file is currently empty, paste the following:
```
{
  "metrics-addr" : "127.0.0.1:9323",
  "experimental" : true
}
```

If the file is not empty, add those two keys, making sure that the resulting file is valid JSON. Be careful that every line ends with a comma (,) except for the last line.

Save the file, or in the case of Docker for Mac or Docker for Windows, save the configuration. Restart Docker.

Docker now exposes Prometheus-compatible metrics on port 9323.
Restart daemon using below command
Send a HUP signal to the daemon to cause it to reload its configuration. On Linux hosts, use the following command.

`
$ sudo kill -SIGHUP $(pidof dockerd)
`
##Integrate with prometheus

###Configure and run Prometheus

In this example, Prometheus runs as a Docker container on the same host.

Copy one of the following configuration files and save it to /tmp/prometheus.yml. This is a stock Prometheus configuration file, except for the addition of the Docker job definition at the bottom of the file. Docker for Mac and Docker for Windows need a slightly different configuration.


# my global config
global:
  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

  # Attach these labels to any time series or alerts when communicating with
  # external systems (federation, remote storage, Alertmanager).
  external_labels:
      monitor: 'codelab-monitor'

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first.rules"
  # - "second.rules"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: 'prometheus'

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'docker'
         # metrics_path defaults to '/metrics'
         # scheme defaults to 'http'.

    static_configs:
      - targets: ['localhost:9323']

Start prometheus using
./prometheus -config.file=prometheus.yml
INFO[0000] Starting prometheus (version=1.6.1, branch=master, revision=4666df502c0e239ed4aa1d80abbbfb54f61b23c3)  source=main.go:88
INFO[0000] Build context (go=go1.8.1, user=root@ca1b452514b7, date=20170419-13:26:24)  source=main.go:89
INFO[0000] Loading configuration file prometheus.yml     source=main.go:251
INFO[0000] Loading series map and head chunks...         source=storage.go:421
INFO[0000] 613 series loaded.                            source=storage.go:432
INFO[0000] Starting target manager...                    source=targetmanager.go:61
INFO[0000] Listening on :9090                            source=web.go:259

Now check in browser
http://localhost:9090

and also http://localhost:9090/metrics
