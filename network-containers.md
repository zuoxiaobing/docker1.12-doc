如果您正在通过用户指南工作，您只需创建并运行一个简单的应用程序.。你也建立了自己的镜象。本节教你如何对容器进行网络化.。

查看默认网络

$ docker network ls

NETWORK ID          NAME                DRIVER

18a2866682b8        none                null

c288470c46f6        host                host

7b369448dccb        bridge              bridge

你可以创建一个容器使用默认的网络

$ docker run -itd --name=networktest ubuntu

74695c9cea6d9810718fddadc01a727a5dd3ce6a69d09752239736c030599741

可以查看容器的网络和ip地址通过以下命令

$ docker network inspect bridge



\[

    {

        "Name": "bridge",

        "Id": "f7ab26d71dbd6f557852c7156ae0574bbf62c42f539b50c8ebde0f728a253b6f",

        "Scope": "local",

        "Driver": "bridge",

        "IPAM": {

            "Driver": "default",

            "Config": \[

                {

                    "Subnet": "172.17.0.1/16",

                    "Gateway": "172.17.0.1"

                }

            \]

        },

        "Containers": {

            "3386a527aa08b37ea9232cbcace2d2458d49f44bb05a6b775fba7ddd40d8f92c": {

                "EndpointID": "647c12443e91faf0fd508b6edfe59c30b642abb60dfab890b4bdccee38750bc1",

                "MacAddress": "02:42:ac:11:00:02",

                "IPv4Address": "172.17.0.2/16",

                "IPv6Address": ""

            },

            "94447ca479852d29aeddca75c28f7104df3c3196d7b6d83061879e339946805c": {

                "EndpointID": "b047d090f446ac49747d3c37d63e4307be745876db7f0ceef7b311cbba615f48",

                "MacAddress": "02:42:ac:11:00:03",

                "IPv4Address": "172.17.0.3/16",

                "IPv6Address": ""

            }

        },

        "Options": {

            "com.docker.network.bridge.default\_bridge": "true",

            "com.docker.network.bridge.enable\_icc": "true",

            "com.docker.network.bridge.enable\_ip\_masquerade": "true",

            "com.docker.network.bridge.host\_binding\_ipv4": "0.0.0.0",

            "com.docker.network.bridge.name": "docker0",

            "com.docker.network.driver.mtu": "9001"

        }

    }

\]

你还可以把一个容器从网络中删除

$ docker network disconnect bridge networktest



创建你自己的桥接网络

$ docker network create -d bridge my-bridge-network

你可以查看一下自己创建的桥接网络

$ docker network inspect my-bridge-network



\[

    {

        "Name": "my-bridge-network",

        "Id": "5a8afc6364bccb199540e133e63adb76a557906dd9ff82b94183fc48c40857ac",

        "Scope": "local",

        "Driver": "bridge",

        "IPAM": {

            "Driver": "default",

            "Config": \[

                {

                    "Subnet": "172.18.0.0/16",

                    "Gateway": "172.18.0.1/16"

                }

            \]

        },

        "Containers": {},

        "Options": {}

    }

\]

添加容器到网络

$ docker run -d --net=my-bridge-network --name db training/postgres

其中--net=my-bridge-network用于指定网络

你可以检查一下容器的网络

$ docker inspect --format='{{json .NetworkSettings.Networks}}'  db

{"my-bridge-network":{"NetworkID":"7d86d31b1478e7cca9ebed7e73aa0fdeec46c5ca29497431d3007d2d9e15ed99",

"EndpointID":"508b170d56b2ac9e4ef86694b0a76a22dd3df1983404f7321da5649645bf7043","Gateway":"172.18.0.1","IPAddress":"172.18.0.2","IPPrefixLen":16,"IPv6Gateway":"","GlobalIPv6Address":"","GlobalIPv6PrefixLen":0,"MacAddress":"02:42:ac:11:00:02"}}

现在我们重新运行一个容器使用默认网络

$ docker run -d --name web training/webapp python app.py

查看使用默认网络的容器的ip地址

$ docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' web

172.17.0.2

然后进入使用自建网络的容器，ping一下，发现是ping不同使用默认网络的容器的

$ docker exec -it db bash



root@a205f0dd33b2:/\# ping 172.17.0.2

ping 172.17.0.2

PING 172.17.0.2 \(172.17.0.2\) 56\(84\) bytes of data.

^C

--- 172.17.0.2 ping statistics ---

44 packets transmitted, 0 received, 100% packet loss, time 43185ms

使用docker network connect命令将web容器连接至自建网络后，可以ping通了

$ docker network connect my-bridge-network web

$ docker exec -it db bash

root@a205f0dd33b2:/\# ping web

PING web \(172.18.0.3\) 56\(84\) bytes of data.

64 bytes from web \(172.18.0.3\): icmp\_seq=1 ttl=64 time=0.095 ms

64 bytes from web \(172.18.0.3\): icmp\_seq=2 ttl=64 time=0.060 ms

64 bytes from web \(172.18.0.3\): icmp\_seq=3 ttl=64 time=0.066 ms

^C

--- web ping statistics ---

3 packets transmitted, 3 received, 0% packet loss, time 2000ms

rtt min/avg/max/mdev = 0.060/0.073/0.095/0.018 ms





