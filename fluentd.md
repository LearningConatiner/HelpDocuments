#Step 0

Pre installation 
Increase Max # of File Descriptors

Please increase the maximum number of file descriptors. You can check the current number using the ulimit -n command.

$ ulimit -n
65535

If your console shows 1024, it is insufficient. Please add following lines to your /etc/security/limits.conf file and reboot your machine.

root soft nofile 65536
root hard nofile 65536
* soft nofile 65536
* hard nofile 65536

Step 1 : Install from Apt Repository

For Ubuntu
curl -L https://toolbelt.treasuredata.com/sh/install-ubuntu-xenial-td-agent2.sh | sh


Step2: Launch Daemon

The /etc/init.d/td-agent script is provided to start, stop, or restart the agent.

$ /etc/init.d/td-agent restart
$ /etc/init.d/td-agent status
td-agent (pid  21678) is running...

The following commands are supported:

$ /etc/init.d/td-agent start
$ /etc/init.d/td-agent stop
$ /etc/init.d/td-agent restart
$ /etc/init.d/td-agent status

root@sri-VirtualBox:~/pythonlearn/locust# /etc/init.d/td-agent status
● td-agent.service - LSB: data collector for Treasure Data
   Loaded: loaded (/etc/init.d/td-agent; bad; vendor preset: enabled)
   Active: active (running) since Thu 2017-05-11 16:25:40 IST; 4h 57min ago
     Docs: man:systemd-sysv-generator(8)
  Process: 21831 ExecStop=/etc/init.d/td-agent stop (code=exited, status=0/SUCCESS)
  Process: 21870 ExecStart=/etc/init.d/td-agent start (code=exited, status=0/SUCCESS)
    Tasks: 9
   Memory: 70.2M
      CPU: 5.285s
   CGroup: /system.slice/td-agent.service
           ├─21904 /opt/td-agent/embedded/bin/ruby /usr/sbin/td-agent --log /var/log/td-agent/td-a...
           └─21907 /opt/td-agent/embedded/bin/ruby /usr/sbin/td-agent --log /var/log/td-agent/td-a...

May 11 16:25:39 sri-VirtualBox systemd[1]: Stopped LSB: data collector for Treasure Data.
May 11 16:25:39 sri-VirtualBox systemd[1]: Starting LSB: data collector for Treasure Data...
May 11 16:25:40 sri-VirtualBox td-agent[21870]: Starting td-agent:  * td-agent
May 11 16:25:40 sri-VirtualBox systemd[1]: Started LSB: data collector for Treasure Data.


Step3: Post Sample Logs via HTTP

By default, /etc/td-agent/td-agent.conf is configured to take logs from HTTP and route them to stdout (/var/log/td-agent/td-agent.log). You can post sample log records using the curl command.

$ curl -X POST -d 'json={"json":"message"}' http://localhost:8888/debug.test

if you see the contents of log file in  (/var/log/td-agent/td-agent.log), following data is displayed

root@sri-VirtualBox:~/pythonlearn/locust# cat /var/log/td-agent/td-agent.log
2017-05-11 16:24:36 +0530 [info]: reading config file path="/etc/td-agent/td-agent.conf"
2017-05-11 16:24:36 +0530 [info]: starting fluentd-0.12.35
2017-05-11 16:24:36 +0530 [info]: gem 'fluent-mixin-config-placeholders' version '0.4.0'
2017-05-11 16:24:36 +0530 [info]: gem 'fluent-mixin-plaintextformatter' version '0.2.6'
2017-05-11 16:24:36 +0530 [info]: gem 'fluent-plugin-kafka' version '0.5.5'
2017-05-11 16:24:36 +0530 [info]: gem 'fluent-plugin-mongo' version '0.8.0'
2017-05-11 16:24:36 +0530 [info]: gem 'fluent-plugin-rewrite-tag-filter' version '1.5.5'
2017-05-11 16:24:36 +0530 [info]: gem 'fluent-plugin-s3' version '0.8.2'
2017-05-11 16:24:36 +0530 [info]: gem 'fluent-plugin-scribe' version '0.10.14'
2017-05-11 16:24:36 +0530 [info]: gem 'fluent-plugin-td' version '0.10.29'
2017-05-11 16:24:36 +0530 [info]: gem 'fluent-plugin-td-monitoring' version '0.2.2'
2017-05-11 16:24:36 +0530 [info]: gem 'fluent-plugin-webhdfs' version '0.4.2'
2017-05-11 16:24:36 +0530 [info]: gem 'fluentd' version '0.12.35'
2017-05-11 16:24:36 +0530 [info]: adding match pattern="td.*.*" type="tdlog"
2017-05-11 16:24:37 +0530 [info]: adding match pattern="debug.**" type="stdout"
2017-05-11 16:24:37 +0530 [info]: adding source type="forward"
2017-05-11 16:24:37 +0530 [info]: adding source type="http"
2017-05-11 16:24:37 +0530 [info]: adding source type="debug_agent"
2017-05-11 16:24:37 +0530 [info]: using configuration file: <ROOT>
  <match td.*.*>
    @type tdlog
    apikey xxxxxx
    auto_create_table 
    buffer_type file
    buffer_path /var/log/td-agent/buffer/td
    buffer_chunk_limit 33554432
    <secondary>
      @type file
      path /var/log/td-agent/failed_records
      buffer_path /var/log/td-agent/failed_records.*
    </secondary>
  </match>
  <match debug.**>
    @type stdout
  </match>
  <source>
    @type forward
  </source>
  <source>
    @type http
    port 8888
  </source>
  <source>
    @type debug_agent
    bind 127.0.0.1
    port 24230
  </source>
</ROOT>
2017-05-11 16:24:37 +0530 [info]: listening fluent socket on 0.0.0.0:24224
2017-05-11 16:24:37 +0530 [info]: listening dRuby uri="druby://127.0.0.1:24230" object="Engine"
2017-05-11 16:25:38 +0530 [info]: shutting down fluentd
2017-05-11 16:25:38 +0530 [info]: shutting down input type="http" plugin_id="object:3f8dae5e380c"
2017-05-11 16:25:38 +0530 [info]: shutting down input type="debug_agent" plugin_id="object:3f8dae5dfe78"
2017-05-11 16:25:38 +0530 [info]: shutting down input type="forward" plugin_id="object:3f8dae5e89ec"
2017-05-11 16:25:38 +0530 [info]: shutting down output type="stdout" plugin_id="object:3f8dad575164"
2017-05-11 16:25:38 +0530 [info]: shutting down output type="tdlog" plugin_id="object:3f8dad37b5fc"
2017-05-11 16:25:38 +0530 [info]: process finished code=0
2017-05-11 16:25:40 +0530 [info]: reading config file path="/etc/td-agent/td-agent.conf"
2017-05-11 16:25:40 +0530 [info]: starting fluentd-0.12.35
2017-05-11 16:25:40 +0530 [info]: gem 'fluent-mixin-config-placeholders' version '0.4.0'
2017-05-11 16:25:40 +0530 [info]: gem 'fluent-mixin-plaintextformatter' version '0.2.6'
2017-05-11 16:25:40 +0530 [info]: gem 'fluent-plugin-kafka' version '0.5.5'
2017-05-11 16:25:40 +0530 [info]: gem 'fluent-plugin-mongo' version '0.8.0'
2017-05-11 16:25:40 +0530 [info]: gem 'fluent-plugin-rewrite-tag-filter' version '1.5.5'
2017-05-11 16:25:40 +0530 [info]: gem 'fluent-plugin-s3' version '0.8.2'
2017-05-11 16:25:40 +0530 [info]: gem 'fluent-plugin-scribe' version '0.10.14'
2017-05-11 16:25:40 +0530 [info]: gem 'fluent-plugin-td' version '0.10.29'
2017-05-11 16:25:40 +0530 [info]: gem 'fluent-plugin-td-monitoring' version '0.2.2'
2017-05-11 16:25:40 +0530 [info]: gem 'fluent-plugin-webhdfs' version '0.4.2'
2017-05-11 16:25:40 +0530 [info]: gem 'fluentd' version '0.12.35'
2017-05-11 16:25:40 +0530 [info]: adding match pattern="td.*.*" type="tdlog"
2017-05-11 16:25:40 +0530 [info]: adding match pattern="debug.**" type="stdout"
2017-05-11 16:25:40 +0530 [info]: adding source type="forward"
2017-05-11 16:25:40 +0530 [info]: adding source type="http"
2017-05-11 16:25:40 +0530 [info]: adding source type="debug_agent"
2017-05-11 16:25:40 +0530 [info]: using configuration file: <ROOT>
  <match td.*.*>
    @type tdlog
    apikey xxxxxx
    auto_create_table 
    buffer_type file
    buffer_path /var/log/td-agent/buffer/td
    buffer_chunk_limit 33554432
    <secondary>
      @type file
      path /var/log/td-agent/failed_records
      buffer_path /var/log/td-agent/failed_records.*
    </secondary>
  </match>
  <match debug.**>
    @type stdout
  </match>
  <source>
    @type forward
  </source>
  <source>
    @type http
    port 8888
  </source>
  <source>
    @type debug_agent
    bind 127.0.0.1
    port 24230
  </source>
</ROOT>
2017-05-11 16:25:40 +0530 [info]: listening fluent socket on 0.0.0.0:24224
2017-05-11 16:25:40 +0530 [info]: listening dRuby uri="druby://127.0.0.1:24230" object="Engine"
2017-05-11 16:31:11 +0530 debug.test: {"json":"message"}
2017-05-11 21:25:11 +0530 debug.test: {"json":"message"}


Step 4
 let’s configure td-agent to output all events with the tag prefixed with “docker” to stdout. Edit /etc/td-agent/td-agent.conf and add the following lines:

<match docker.**>

type stdout

</match>

Then, restart td-agent as follows:

sudo service td-agent restart

Step 5 : Launch a Container and Confirm

Finally, let’s launch a container and send logs to the host’s td-agent for the helloworld example.

docker run –log-driver=fluentd -d -p 4000:4000 helloworld

root@sri-VirtualBox:/var/lib/docker/containers/a51b6f2fe7b3a807665e413980217409f66514c057ca25418327b1c47f8c537a# docker run --log-driver=fluentd -p 4000:4000 helloworld
 * Running on http://0.0.0.0:4000/ (Press CTRL+C to quit)
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 462-906-265
check the fluentd log file in 
root@sri-VirtualBox:~# cat /var/log/td-agent/td-agent.log 

2017-05-12 20:16:47 +0530 docker.59b759bac541: {"container_id":"59b759bac5411b374801236c1f913f3019644af579930f25d7060dd3e9ec7017","container_name":"/boring_jones","source":"stderr","log":" * Running on http://0.0.0.0:4000/ (Press CTRL+C to quit)"}
2017-05-12 20:16:47 +0530 docker.59b759bac541: {"container_name":"/boring_jones","source":"stderr","log":" * Restarting with stat","container_id":"59b759bac5411b374801236c1f913f3019644af579930f25d7060dd3e9ec7017"}
2017-05-12 20:16:47 +0530 docker.59b759bac541: {"container_name":"/boring_jones","source":"stderr","log":" * Debugger is active!","container_id":"59b759bac5411b374801236c1f913f3019644af579930f25d7060dd3e9ec7017"}
2017-05-12 20:16:47 +0530 docker.59b759bac541: {"log":" * Debugger PIN: 462-906-265","container_id":"59b759bac5411b374801236c1f913f3019644af579930f25d7060dd3e9ec7017","container_name":"/boring_jones","source":"stderr"}

The logs get directedto fluentd
root@sri-VirtualBox:~# ^C



