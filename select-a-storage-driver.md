选择存储驱动

本页描述了docker的存储驱动程序的特点。本页提供有关选择存储驱动器的指导。

本页的材料是针对已经了解存储驱动技术的读者的。

一个可插拔的存储驱动程序结构

docker有一个可插拔的存储驱动程序结构。这使您能够灵活地“插入”最适合您的环境和用例的存储驱动程序。每个docker存储驱动程序是一个基于Linux的文件系统和卷管理器。此外，每个存储驱动程序可以自由地实现图像层和容器层的管理，以自己独特的方式。这意味着某些存储驱动程序在不同情况下比其他驱动程序性能更好。

dockers守护程序只能运行一个存储驱动程序，下表显示了支持的存储驱动程序技术及其驱动程序名称：  

|技术           |名称                   |
|--------------|-----------------------|
|OverlayFS     |`overlay` or `overlay2`|
|AUFS          |`aufs`                 |
|Btrfs         |`btrfs`                |
|Device Mapper |`devicemapper`         |
|VFS           |`vfs`                  |
|ZFS           |`zfs`                  |

可以通过`docker info`查看关于存储驱动程序的信息


    $ docker info

    Containers: 0
    Images: 0
    Storage Driver: overlay
     Backing Filesystem: extfs
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-15-generic
    Operating System: Ubuntu 15.04
    ... output truncated ...
如图所示：存储驱动类型为overlay,宿主机文件系统的格式为extfs。
存储驱动与宿主机文件格式
下表列出了每个存储驱动程序，以及它是否必须与宿主的文件系统相匹配：


|存储驱动 |通常运行在以下文件系统上 |禁用                                         |
|---------------|-----------------|----------------------------------------------------|
|`overlay`      |`ext4` `xfs`     |`btrfs` `aufs` `overlay` `zfs` `eCryptfs`|
|`overlay2`     |`ext4` `xfs`     |`btrfs` `aufs` `overlay` `zfs` `eCryptfs`|
|`aufs`         |`ext4` `xfs`     |`btrfs` `aufs` `eCryptfs`                           |
|`btrfs`        |`btrfs` _only_   |   N/A                                              |
|`devicemapper` |`direct-lvm`     |   N/A                                              |
|`vfs`          |debugging only   |   N/A                                              |
|`zfs`          |`zfs` _only_     |   N/A                                              |

“禁用”意味着某些存储驱动程序不能在某些文件系统上运行。

你可以通过命令行 `--storage-driver=<name>`设置存储驱动程序,或通过配置文件`/etc/default/docker`设置
以下是示例


    $ dockerd --storage-driver=devicemapper &

    $ docker info

    Containers: 0
    Images: 0
    Storage Driver: devicemapper
     Pool Name: docker-252:0-147544-pool
     Pool Blocksize: 65.54 kB
     Backing Filesystem: extfs
     Data file: /dev/loop0
     Metadata file: /dev/loop1
     Data Space Used: 1.821 GB
     Data Space Total: 107.4 GB
     Data Space Available: 3.174 GB
     Metadata Space Used: 1.479 MB
     Metadata Space Total: 2.147 GB
     Metadata Space Available: 2.146 GB
     Thin Pool Minimum Free Space: 10.74 GB
     Udev Sync Supported: true
     Deferred Removal Enabled: false
     Data loop file: /var/lib/docker/devicemapper/devicemapper/data
     Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
     Library Version: 1.02.90 (2014-09-01)
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-15-generic
    Operating System: Ubuntu 15.04
    <output truncated>
你的存储驱动器的选择可以影响你的容器应用程序的性能。因此，了解不同的存储驱动程序选项并为应用程序选择合适的驱动程序是很重要的。稍后，在本页中，您将找到一些选择合适的驱动程序的建议。

