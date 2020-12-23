---
title: docker
date: 2020-11-19 14:08:56
tags: 容器
---

# 底层原理

## Docker是怎么工作的

Docker是一个C-S结果系统，Docker的守护进程运行在主机上，通过Socker从客户端访问

DockerServer接收到DockerClient的指令，就会执行命令

## Docker 为什么比VM快

1. Docker有着比虚拟机更少的抽象层
2. Docker利用的是宿主机的内核，VM需要Guest OS。所以新建一个容器的时候，docker不需要像虚拟机一样重新加载一个操作系统内核，避免引导。虚拟机是加载guestOS，分钟级别，而docker是利用宿主机的操作系统，省略了这个复杂的过程。

# Docker的常用命令

## 帮助命令

`````````shell
#显示docker版本信息
docker version
#显示docker的系统信息，包括镜像和容器的数量
docker info
#帮助命令
docker 命令 --help
`````````

官方文档地址：https://docs.docker.com/reference/

## 镜像命令

### docker images

> 查看所有本地的主机上的镜像

```````shell
~  bin sudo docker images         
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
zookeeper           latest              82f175cb301d        3 weeks ago         253MB
consul              latest              4f7b214361a7        6 weeks ago         122MB
mysql               latest              8e85dd5c3255        7 weeks ago         544MB
hello-world         latest              bf756fb1ae65        11 months ago       13.3kB
```````

REPOSITORY:		镜像的仓库源

TAG:						版本标签信息

IMAGE ID:				镜像的id

### docker search

> 搜索镜像

```````shell
➜  bin sudo docker search mysql
[sudo] 10235627@zte.intra 的密码：
NAME                              DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   10202               [OK]                
mariadb                           MariaDB is a community-developed fork of MyS…   3753                [OK]                
mysql/mysql-server                Optimized MySQL Server Docker images. Create…   746                                     [OK]
percona                           Percona Server is a fork of the MySQL relati…   511                 [OK]                
centos/mysql-57-centos7           MySQL 5.7 SQL database server                   86                                      
mysql/mysql-cluster               Experimental MySQL Cluster Docker images. Cr…   78                                      
centurylink/mysql                 Image containing mysql. Optimized to be link…   60                                      [OK]
```````

### docker pull 镜像名[:tag]

> 下载镜像

``````shell
➜  bin sudo docker pull  mysql
Using default tag: latest
latest: Pulling from library/mysql
852e50cd189d: Pull complete 
29969ddb0ffb: Pull complete 
a43f41a44c48: Pull complete 
5cdd802543a3: Pull complete 
b79b040de953: Pull complete 
938c64119969: Pull complete 
7689ec51a0d9: Pull complete 
a880ba7c411f: Pull complete 
984f656ec6ca: Pull complete 
9f497bce458a: Pull complete 
b9940f97694b: Pull complete 
2f069358dc96: Pull complete 
Digest: sha256:4bb2e81a40e9d0d59bd8e3dc2ba5e1f2197696f6de39a91e90798dd27299b093		#签名
Status: Downloaded newer image for mysql:latest		
docker.io/library/mysql:latest				#真实地址									
``````

### docker rmi 镜像id

> 删除镜像

## 容器命令

**有了镜像才可以创建容器**

### docker run

> 新建容器并启动
>
> 参数
>
> --name="Name"	容器名称，用来区分容器
>
> -d							后台方式运行
>
> -it							使用交互方式运行
>
> -P							指定容器端口
>
> ​								-p	ip:主机端口:容器端口
>
> ​								-p	主机端口:容器端口
>
> ​								-p	容器端口
>
> -p							随机指定端口

**启动并进入centos容器**

```````shell
➜  ~ sudo docker run -it centos
[root@deb2fe9ceb60 bin]# 
```````

### docker ps 

> 查看所有运行的容器
>
> 参数
>
> -a		列出所有容器，默认之列出正在运行的容器
>
> -n=?	显示最近创建的容器，？是数量，最近几个，docker ps -n=1
>
> -q		只显示容器的编号

### 退出容器

在容器内部

````````shell
exit			#直接容器停止并退出
Ctrl + P +Q		#容器不停止退出
````````

### docker rm 容器id

> 删除容器
>
> 默认不能删除正在运行的容器
>
> 参数
>
> -f		强制删除容器，包括正在运行的容器

### docker start 容器id

> 启动容器

### docker restart 容器id

> 重启容器

### docker stop 容器id

> 停止容器

docker kill 容器id

> 强制停止容器

## 其他命令

### 后台启动容器

> docker容器使用后台运行，就必须要有一个前台进程，docker 发现没有应用，就会自动停止
>
> 如
>
> `````````shell
> ➜  ~ sudo docker run -d centos		#后台启动centos
> ➜  ~ sudo docker ps           
> CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
> `````````
>
> docker ps 发现centos没有在运行

### docker logs 容器id

> 查看容器日志

``````shell
➜  ~ sudo docker logs -f -t --tail 10    4880af3be573
``````

### docker top 容器id

> 查看容器中进程信息

### docker inspect 容器id

> 查看容器元信息

```````shell
➜  ~ sudo docker inspect  4880af3be573
[sudo] 10235627@zte.intra 的密码：
[
    {
        "Id": "4880af3be573990cecf8c8a175e5c64d4fbd3075fed8c13c93104a257a56141e",
        "Created": "2020-12-05T10:22:45.444798168Z",
        "Path": "/bin/bash",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 49912,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-12-05T10:22:46.164488798Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:0d120b6ccaa8c5e149176798b3501d4dd1885f961922497cd0abef155c869566",
        "ResolvConfPath": "/var/lib/docker/containers/4880af3be573990cecf8c8a175e5c64d4fbd3075fed8c13c93104a257a56141e/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/4880af3be573990cecf8c8a175e5c64d4fbd3075fed8c13c93104a257a56141e/hostname",
        "HostsPath": "/var/lib/docker/containers/4880af3be573990cecf8c8a175e5c64d4fbd3075fed8c13c93104a257a56141e/hosts",
        "LogPath": "/var/lib/docker/containers/4880af3be573990cecf8c8a175e5c64d4fbd3075fed8c13c93104a257a56141e/4880af3be573990cecf8c8a175e5c64d4fbd3075fed8c13c93104a257a56141e-json.log",
        "Name": "/optimistic_faraday",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "Capabilities": null,
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/023663c25dbe9167f996b4c54afcaedad6a451078c13ecb091628f7d38d860b7-init/diff:/var/lib/docker/overlay2/d06cc3b85c1dfc52610916e3b457c294d2765c9da2b5053ac0159bbff9c1a40c/diff",
                "MergedDir": "/var/lib/docker/overlay2/023663c25dbe9167f996b4c54afcaedad6a451078c13ecb091628f7d38d860b7/merged",
                "UpperDir": "/var/lib/docker/overlay2/023663c25dbe9167f996b4c54afcaedad6a451078c13ecb091628f7d38d860b7/diff",
                "WorkDir": "/var/lib/docker/overlay2/023663c25dbe9167f996b4c54afcaedad6a451078c13ecb091628f7d38d860b7/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "4880af3be573",
            "Domainname": "",
            "User": "",
            "AttachStdin": true,
            "AttachStdout": true,
            "AttachStderr": true,
            "Tty": true,
            "OpenStdin": true,
            "StdinOnce": true,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20200809",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "57cdd39b8ab7dbb1a6be778b5cef0e9899831fd6f902b1bdddca89643e355ece",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/57cdd39b8ab7",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "42d54d3fef743b69b42e1507204d26cc966d9c10e4681d7d700a050741b04edf",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "85edb419d046ebf7b839e1e8ea14515d18ef01ee43888dc572d47ab43e32078c",
                    "EndpointID": "42d54d3fef743b69b42e1507204d26cc966d9c10e4681d7d700a050741b04edf",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```````

### docker exec -it 容器id

> 进入容器，进入容器后开启一个新的终端，可以在里面操作

### docker attach 容器id

> 进入容器，进入容器正在执行的终端，不会启动新的进程

### docker cp 容器id:容器内路径 目的主机路径

> 拷贝容器内文件到外部文件系统





