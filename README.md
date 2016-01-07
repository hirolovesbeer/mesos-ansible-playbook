
# About this playbook
This playbook builds environment Apache Mesos masters and slaves.

## System requirement
- OS
```
CentOS 7.2(x86_64)
```

### CentOS 6.7(old)
Please check "centos67" Directory.

### Sample target hosts
```
[master]
172.16.38.178
172.16.38.179
172.16.38.180

[slave]
172.16.38.139
```


## Install modules
- Mesos master/slave
- Marathon
- Chronos
- Zookeeper

## Dependency yum modules
- Development tools
- ntp, ntpdate
- epel
- sshpass

## About proxy
If mesos environment is under proxy/nat, please use proxy vars and rewrite proxy rpm's options.

Also, please set the host's proxy environment:  http_proxy, https_proxy, ftp_proxy.

Example. "~/.bash_profile"
```
export HTTP_PROXY='http://yourproxy:proxyport/'
export HTTPS_PROXY='http://yourproxy:proxyport/'
export FTP_PROXY='http://yourproxy:proxyport/'
```

## Zookeeper setting
### hosts
Mesos masters are also working on zookeeper daemons.
```
[master]
172.16.38.178
172.16.38.179
172.16.38.180
```

### zk file
"zk" file is zookeeper setting file for mesos.

This file's deploy path is "/etc/mesos/zk"
zk file's include hosts file's "[master]" hosts list like this.
```
zk://172.16.38.178:2181,172.16.38.179:2181,172.16.38.180:2181/mesos
```
add host and port '2181'.

### zoo.cfg
This is zookeeper config file.

It will be deploy "/etc/zookeeper/conf/zoo.cfg".

And add the master server's lines last position of files like "server1=ipaddr:2888:3888".

```
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

maxClientCnxns=50
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
dataDir=/var/lib/zookeeper
# the port at which the clients will connect
clientPort=2181

server.1=172.16.38.178:2888:3888
server.2=172.16.38.179:2888:3888
server.3=172.16.38.180:2888:3888
```

### ipaddr.myid
Zookeeper need to identify each server's. The id called "myid" in zookeeper inside. So, each zookeeper server need to set myid by itself.
This file only include number.

- eg. 172.16.38.178.myid
```
1
```
- eg. 172.16.38.179.myid
```
2
```
- eg. 172.16.38.180.myid
```
3
```


## Installation
### sudo user
Edit 'mesos-master.yml', 'mesos-slave.yml'
```
  remote_user: sudo-user
```
set the 'sudo-user' value for your suitable sudo user name.

### Mesos master
```
$ ansible-playbook mesos-master.yml -K -k -s
```
- Mesos Master's WebUI
If install is success, you can access Mesos master Web UI(http://172.16.38.178:5050/).

- Marathon WebUI
You can also access Marathon WebUI(http://172.16.38.179:8080/)

### Mesos slave
```
$ ansible-playbook mesos-slave.yml -K -k -s
```
If slave can add the mesos systems, you can check Master WebUI(http://172.16.38.178:5050/#/slaves).

On top page, "Acitvated" is count up in Slaves section.

Also, "Resources" section, you can check slave's resources.
