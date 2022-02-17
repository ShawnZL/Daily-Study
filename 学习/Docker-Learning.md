# Docker-Learning

## Docker介绍

基于 `Linux` 内核的 [cgroup](https://zh.wikipedia.org/wiki/Cgroups)，[namespace](https://en.wikipedia.org/wiki/Linux_namespaces)，以及 [OverlayFS](https://docs.docker.com/storage/storagedriver/overlayfs-driver/) 类的 [Union FS](https://en.wikipedia.org/wiki/Union_mount) 等技术，对进程进行封装隔离，属于 [操作系统层面的虚拟化技术](https://en.wikipedia.org/wiki/Operating-system-level_virtualization)。由于隔离的进程独立于宿主和其它的隔离的进程，因此也称其为容器。

### cgroup

`control groups` 是 Linux 内核提供的一种可以限制单个进程或者多个进程所使用资源的机制，可以对CPU、内存等资源实现精细化控制。cgroup 为每种可以控制的资源定义了一个子系统。典型的子系统

- cpu 子系统，主要限制进程cpu使用率
- cpuacct 子系统，可以统计cgroup中的 cpu 使用报告
- cpuset 子系统，可以为 cgroups 中的进程分配单独的 cpu 节点或者内存节点。
- memory 子系统，可以限制进程的 memory 使用量。
- blkio 子系统，可以限制进程的块设备 io。
- devices 子系统，可以控制进程能够访问某些设备。
- ns 子系统，可以使不同 cgroups 下面的进程使用不同的 namespace。

每一个子系统都需要与内核的其它模块配合完成对于资源的控制，例如对 cpu 资源的限制是通过进程调度模块根据 cpu 子系统的配置来完成的。

#### cgroup Hierarchy（层级结构）

内核使用 cgroup 结构体来表示一个 control group 对某一个或者某几个 cgroups 子系统的资源限制。cgroup 结构体可以组织成一颗树的形式，每一棵cgroup 结构体组成的树称之为一个 cgroups 层级结构。cgroups层级结构可以 attach 一个或者几个 cgroups 子系统，当前层级结构可以对其 attach 的 cgroups 子系统进行资源的限制。每一个 cgroups 子系统只能被 attach 到一个 cpu 层级结构中。

![](https://awps-assets.meituan.net/mit-x/blog-images-bundle-2015/b3270d03.png)

比如上图表示两个cgroups层级结构，每一个层级结构中是一颗树形结构，树的每一个节点是一个 cgroup 结构体（比如cpu_cgrp, memory_cgrp)。第一个 cgroups 层级结构 attach 了 cpu 子系统和 cpuacct 子系统， 当前 cgroups 层级结构中的 cgroup 结构体就可以对 cpu 的资源进行限制，并且对进程的 cpu 使用情况进行统计。 第二个 cgroups 层级结构 attach 了 memory 子系统，当前 cgroups 层级结构中的 cgroup 结构体就可以对 memory 的资源进行限制。

在每一个 cgroups 层级结构中，每一个节点（cgroup 结构体）可以设置对资源不同的限制权重。比如上图中 cgrp1 组中的进程可以使用60%的 cpu 时间片，而 cgrp2 组中的进程可以使用20%的 cpu 时间片。

#### cgroup与进程

将进程与 cgroup 层级结构联系起来。

在创建了 cgroups 层级结构中的节点（cgroup 结构体）之后，可以把进程加入到某一个节点的控制任务列表中，一个节点的控制列表中的所有进程都会受到当前节点的资源限制。同时某一个进程也可以被加入到不同的 cgroups 层级结构的节点中，因为不同的 cgroups 层级结构可以负责不同的系统资源。所以说进程和 cgroup 结构体是一个多对多的关系。

![](https://awps-assets.meituan.net/mit-x/blog-images-bundle-2015/3982f44c.png)

上面这个图从整体结构上描述了进程与 cgroups 之间的关系。最下面的`P`代表一个进程。每一个进程的描述符中有一个指针指向了一个辅助数据结构`css_set`（cgroups subsystem set）。 指向某一个`css_set`的进程会被加入到当前`css_set`的进程链表中。一个进程只能隶属于一个`css_set`，一个`css_set`可以包含多个进程，隶属于同一`css_set`的进程受到同一个`css_set`所关联的资源限制。

**上图中的”M×N Linkage”说明的是`css_set`通过辅助数据结构可以与 cgroups 节点进行多对多的关联。但是 cgroups 的实现不允许`css_set`同时关联同一个cgroups层级结构下多个节点。 这是因为 cgroups 对同一种资源不允许有多个限制配置。**

**一个`css_set`关联多个 cgroups 层级结构的节点时，表明需要对当前`css_set`下的进程进行多种资源的控制。而一个 cgroups 节点关联多个`css_set`时，表明多个`css_set`下的进程列表受到同一份资源的相同限制。**

#### cgroup文件系统

Linux 使用了多种数据结构在内核中实现了 cgroups 的配置，关联了进程和 cgroups 节点，那么 Linux 又是如何让用户态的进程使用到 cgroups 的功能呢？ Linux内核有一个很强大的模块叫 VFS (Virtual File System)。 VFS 能够把具体文件系统的细节隐藏起来，给用户态进程提供一个统一的文件系统 API 接口。 cgroups 也是通过 VFS 把功能暴露给用户态的，cgroups 与 VFS 之间的衔接部分称之为 cgroups 文件系统。

##### VFS

VFS是一个内核抽象层，能够隐藏具体文件系统的实现细节，从而给用户态进程提供一套统一的 API 接口。VFS 使用了一种通用文件系统的设计，具体的文件系统只要实现了 VFS 的设计接口，就能够注册到 VFS 中，从而使内核可以读写这种文件系统。 这很像面向对象设计中的抽象类与子类之间的关系，抽象类负责对外接口的设计，子类负责具体的实现。其实，VFS本身就是用 c 语言实现的一套面向对象的接口。

VFS 通用文件模型中包含以下四种元数据结构：

1. 超级块对象(superblock object)，用于存放已经注册的文件系统的信息。比如ext2，ext3等这些基础的磁盘文件系统，还有用于读写socket的socket文件系统，以及当前的用于读写cgroups配置信息的 cgroups 文件系统等。
2. 索引节点对象(inode object)，用于存放具体文件的信息。对于一般的磁盘文件系统而言，inode 节点中一般会存放文件在硬盘中的存储块等信息；对于socket文件系统，inode会存放socket的相关属性，而对于cgroups这样的特殊文件系统，inode会存放与 cgroup 节点相关的属性信息。这里面比较重要的一个部分是一个叫做 inode_operations 的结构体，这个结构体定义了在具体文件系统中创建文件，删除文件等的具体实现。
3. 文件对象(file object)，一个文件对象表示进程内打开的一个文件，文件对象是存放在进程的文件描述符表里面的。同样这个文件中比较重要的部分是一个叫 file_operations 的结构体，这个结构体描述了具体的文件系统的读写实现。当进程在某一个文件描述符上调用读写操作时，实际调用的是 file_operations 中定义的方法。 对于普通的磁盘文件系统，file_operations 中定义的就是普通的块设备读写操作；对于socket文件系统，file_operations 中定义的就是 socket 对应的 send/recv 等操作；而对于cgroups这样的特殊文件系统，file_operations 中定义的就是操作 cgroup 结构体等具体的实现。
4. 目录项对象(dentry object)，在每个文件系统中，内核在查找某一个路径中的文件时，会为内核路径上的每一个分量都生成一个目录项对象，通过目录项对象能够找到对应的 inode 对象，目录项对象一般会被缓存，从而提高内核查找速度。

#### cgroup使用方法

##### cgroup文件系统挂载

Linux中，用户可以使用mount命令挂载 cgroups 文件系统，格式为: `mount -t cgroup -o subsystems name /cgroup/name`，其中 subsystems 表示需要挂载的 cgroups 子系统， /cgroup/name 表示挂载点，如上文所提，这条命令同时在内核中创建了一个cgroups 层级结构。

比如挂载 cpuset, cpu, cpuacct, memory 4个 subsystem 到 /cgroup/cpu_and_mem 目录下，就可以使用 `mount -t cgroup -o remount,cpu,cpuset,memory cpu_and_mem /cgroup/cpu_and_mem`

### namespace

**namespace 是 Linux 内核用来隔离内核资源的方式。**通过 namespace 可以让一些进程只能看到与自己相关的一部分资源，而另外一些进程也只能看到与它们自己相关的资源，这两拨进程根本就感觉不到对方的存在。具体的实现方式是把一个或多个进程的相关资源指定在同一个 namespace 中。

实际上，Linux 内核实现 namespace 的一个主要目的就是实现轻量级虚拟化(容器)服务。在同一个 namespace 下的进程可以感知彼此的变化，而对外界的进程一无所知。这样就可以让容器中的进程产生错觉，认为自己置身于一个独立的系统中，从而达到隔离的目的。也就是说 linux 内核提供的 namespace 技术为 docker 等容器技术的出现和发展提供了基础条件。

使用命令查看当前进程所属的 `namespace` 信息

```
$ ll /proc/$$/ns
```

这些 namespace 文件都是链接文件。链接文件的内容的格式为 xxx:[inode number]。其中的 xxx 为 namespace 的类型，inode number 则用来标识一个 namespace，我们也可以把它理解为 namespace 的 ID。如果两个进程的某个 namespace 文件指向同一个链接文件，说明其相关资源在同一个 namespace 中。
其次，在 /proc/[pid]/ns 里放置这些链接文件的另外一个作用是，一旦这些链接文件被打开，只要打开的文件描述符(fd)存在，那么就算该 namespace 下的所有进程都已结束，这个 namespace 也会一直存在，后续的进程还可以再加入进来。

除了打开文件的方式，我们还可以通过文件挂载的方式阻止 namespace 被删除。比如我们可以把当前进程中的 uts 挂载到 ~/uts 文件：

### UnionFS

#### 什么是UnionFS

联合文件系统（Union File System）它可以把多个目录(也叫分支)内容联合挂载到同一个目录下，而目录的物理位置是分开的。UnionFS允许只读和可读写目录并存，就是说可同时删除和增加内容。UnionFS应用的地方很多，比如在多个磁盘分区上合并不同文件系统的主目录，或把几张CD光盘合并成一个统一的光盘目录(归档)。另外，具有写时复制(copy-on-write)功能UnionFS可以把只读和可读写文件系统合并在一起，虚拟上允许只读文件系统的修改可以保存到可写文件系统当中。

#### docker的镜像rootfs和layer设计

任何程序运行时都会有依赖，无论是开发语言层的依赖库，还是各种系统lib、操作系统等，不同的系统上这些库可能是不一样的，或者有缺失的。为了让容器运行时一致，docker将依赖的操作系统、各种lib依赖整合打包在一起（即镜像），然后容器启动时，作为它的根目录（根文件系统rootfs），使得容器进程的各种依赖调用都在这个根目录里，这样就做到了环境的一致性。

**Docker镜像的设计中，引入了层（layer）的概念**，也就是说，用户制作镜像的每一步操作，都会生成一个层，也就是一个增量rootfs（一个目录），这样应用A和应用B所在的容器共同引用相同的Debian操作系统层、Golang环境层（作为只读层），而各自有各自应用程序层，和可写层。启动容器的时候通过UnionFS把相关的层挂载到一个目录，作为容器的根文件系统。

需要注意的是，rootfs只是一个操作系统所包含的文件、配置和目录，并不包括操作系统内核。这就意味着，如果你的应用程序需要配置内核参数、加载额外的内核模块，以及跟内核进行直接的交互，你就需要注意了：**这些操作和依赖的对象，都是宿主机操作系统的内核，它对于该机器上的所有容器来说是一个“全局变量”，牵一发而动全身。**

### rootf（根文件系统）

根文件系统首先是内核启动时所mount的第一个文件系统，内核代码映像文件保存在根文件系统中，而系统引导启动程序会在根文件系统挂载之后从中把一些基本的初始化脚本和服务等加载到内存中去运行。

根文件系统首先是一种文件系统，该文件系统不仅具有普通文件系统的存储数据文件的功能，但相对于普通的文件系统，它是内核启动时挂载（mount）的第一个文件系统，内核代码的映像文件保存在根文件系统中，系统引导启动程序会在根文件系统挂载之后从中把一些初始化脚本（如rcS,inittab）和服务加载到内存中去运行。 文件系统和内核是完全独立的两个部分。在嵌入式中移植的内核下载到开发板上，是没有办法真正的启动Linux操作系统的，会出现无法加载文件系统的错误。

##### 根文件系统常用目录简介

/etc/：存储重要的配置文件。

 /bin/：存储常用且开机时必须用到的执行文件。 

/sbin/：存储着开机过程中所需的系统执行文件。 

/lib/：存储/bin/及/sbin/的执行文件所需的链接库，以及Linux的内核模块。 

/dev/：存储设备文件。

##### Linux文件系统的常用目录

/bin目录 该目录下存放所有用户都可以使用的、基本的命令，这些命令在挂接其它文件系统之前就可以使用，所以/bin目录必须和根文件系统在同一个分区中。 /bin目录下常用的命令有：cat，chgrp，chmod，cp，ls，sh，kill，mount，umount，mkdir，mknod，test等，我们在利用Busybox制作根文件系统时，在生成的bin目录下，可以看到一些可执行的文件，也就是可用的一些命令。

/sbin 目录 该目录下存放系统命令，即只有管理员能够使用的命令，系统命令还可以存放在/usr/sbin,/usr/local/sbin目录下，/sbin目录中存放的是基本的系统命令，它们用于启动系统，修复系统等，与/bin目录相似，在挂接其他文件系统之前就可以使用/sbin，所以/sbin目录必须和根文件系统在同一个分区中。 /sbin目录下常用的命令有：shutdown，reboot，fdisk，fsck等，本地用户自己安装的系统命令放在/usr/local/sbin目录下。

/dev目录 该目录下存放的是设备文件，设备文件是Linux中特有的文件类型，在Linux系统下，以文件的方式访问各种设备，即通过读写某个设备文件操作某个具体硬件。比如通过”dev/ttySAC0”文件可以操作串口0，通过”/dev/mtdblock1”可以访问MTD设备的第2个分区。

/etc目录 该目录下存放着各种配置文件，对于PC上的Linux系统，/etc目录下的文件和目录非常多，这些目录文件是可选的，它们依赖于系统中所拥有的应用程序，依赖于这些程序是否需要配置文件。在嵌入式系统中，这些内容可以大为精减。

/lib目录 该目录下存放共享库和可加载(驱动程序)，共享库用于启动系统。运行根文件系统中的可执行程序，比如：/bin /sbin 目录下的程序。

/home目录 用户目录，它是可选的，对于每个普通用户，在/home目录下都有一个以用户名命名的子目录，里面存放用户相关的配置文件。

/root目录 根用户的目录，与此对应，普通用户的目录是/home下的某个子目录。

/usr目录 /usr目录的内容可以存在另一个分区中，在系统启动后再挂接到根文件系统中的/usr目录下。里面存放的是共享、只读的程序和数据，这表明/usr目录下的内容可以在多个主机间共享，这些主要也符合FHS标准的。/usr中的文件应该是只读的，其他主机相关的，可变的文件应该保存在其他目录下，比如/var。/usr目录在嵌入式中可以精减。

/var目录 与/usr目录相反，/var目录中存放可变的数据，比如spool目录(mail,news)，log文件，临时文件。

/proc目录 这是一个空目录，常作为proc文件系统的挂接点，proc文件系统是个虚拟的文件系统，它没有实际的存储设备，里面的目录，文件都是由内核临时生成的，用来表示系统的运行状态，也可以操作其中的文件控制系统。 Linux下的/proc目录存储的是记录当前内核运行状态的一系列特殊文件，用户可以通过访问这些文件，查看系统以及当前正在运行的进程的信息，比如CPU使用情况、内存占用率等，这些文件也是top指令查看系统信息的主要数据来源。

/mnt目录 用于临时挂载某个文件系统的挂接点，通常是空目录，也可以在里面创建一引起空的子目录，比如/mnt/cdram /mnt/hda1 。用来临时挂载光盘、硬盘。

/tmp目录 用于存放临时文件，通常是空目录，一些需要生成临时文件的程序用到的/tmp目录下，所以/tmp目录必须存在并可以访问。

### Docker架构

![](https://docs.microsoft.com/en-us/virtualization/windowscontainers/deploy-containers/media/docker-on-linux.png)

下面的图片比较了 **Docker** 和传统虚拟化方式的不同之处。传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整操作系统，在该系统上再运行所需应用进程；而容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核，而且也没有进行硬件虚拟。因此容器要比传统虚拟机更为轻便。下面的图片比较了 **Docker** 和传统虚拟化方式的不同之处。传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整操作系统，在该系统上再运行所需应用进程；而容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核，而且也没有进行硬件虚拟。因此容器要比传统虚拟机更为轻便。

传统虚拟机

<img src="https://3503645665-files.gitbook.io/~/files/v0/b/gitbook-28427.appspot.com/o/assets%2F-M5xTVjmK7ax94c8ZQcm%2F-M5xT_hHX2g5ldlyp9nm%2F-M5xTdXNYDmRWNH-Lqez%2Fvirtualization.png?generation=1588021947568736&alt=media" style="zoom: 67%;" />

Docker容器

<img src="https://3503645665-files.gitbook.io/~/files/v0/b/gitbook-28427.appspot.com/o/assets%2F-M5xTVjmK7ax94c8ZQcm%2F-M5xT_hHX2g5ldlyp9nm%2F-M5xTdXP2scg0hxytUHA%2Fdocker.png?generation=1588021947474061&alt=media" alt="Docker容器化" style="zoom:67%;" />

## Docker基本概念

### 镜像

操作系统分为 内核 和 用户空间。对于 Linux 而言，内核启动后，会挂载 root 文件系统为其提供用户空间支持。而 Docker 镜像（Image），就相当于一个 root 文件系统。

**Docker 镜像** 是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。镜像 **不包含** 任何动态数据，其内容在构建之后也不会被改变。

#### 分层存储

镜像包含操作系统完整的 root 文件系统是庞大的，在设计 Docker 时，充分利用 [Union FS](https://en.wikipedia.org/wiki/Union_mount) 的技术，将其设计为分层存储的架构。所以严格来说，镜像并非是像一个 `ISO` 那样的打包文件，镜像只是一个虚拟的概念，其实际体现并非由一个文件组成，而是由一组文件系统组成，或者说，由多层文件系统联合组成。

镜像构建时，会一层层构建，前一层是后一层的基础。每一层构建完就不会再发生改变，后一层上的任何改变只发生在自己这一层。比如，删除前一层文件的操作，实际不是真的删除前一层的文件，而是仅在当前层标记为该文件已删除。在最终容器运行的时候，虽然不会看到这个文件，但是实际上该文件会一直跟随镜像。因此，在构建镜像的时候，需要额外小心，每一层尽量只包含该层需要添加的东西，任何额外的东西应该在该层构建结束前清理掉。

分层存储的特征还使得镜像的复用、定制变的更为容易。甚至可以用之前构建好的镜像作为基础层，然后进一步添加新的层，以定制自己所需的内容，构建新的镜像。

### 容器

镜像（`Image`）和容器（`Container`）的关系，就像是面向对象程序设计中的 `类` 和 `实例` 一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。

容器的实质是进程，但与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的 [命名空间](https://en.wikipedia.org/wiki/Linux_namespaces)。因此容器可以拥有自己的 `root` 文件系统、自己的网络配置、自己的进程空间，甚至自己的用户 ID 空间。容器内的进程是运行在一个隔离的环境里，使用起来，就好像是在一个独立于宿主的系统下操作一样。这种特性使得容器封装的应用比直接在宿主运行更加安全。也因为这种隔离的特性，很多人初学 Docker 时常常会混淆容器和虚拟机。

前面讲过镜像使用的是分层存储，容器也是如此。每一个容器运行时，是以镜像为基础层，在其上创建一个当前容器的存储层，我们可以称这个为容器运行时读写而准备的存储层为 **容器存储层**。

### 仓库

镜像构建完成后，可以很容易的在当前宿主机上运行，但是，如果需要在其它服务器上使用这个镜像，我们就需要一个集中的存储、分发镜像的服务，[Docker Registry]() 就是这样的服务。

一个 **Docker Registry** 中可以包含多个 **仓库**（`Repository`）；每个仓库可以包含多个 **标签**（`Tag`）；每个标签对应一个镜像。

通常，一个仓库会包含同一个软件不同版本的镜像，而标签就常用于对应该软件的各个版本。我们可以通过 `<仓库名>:<标签>` 的格式来指定具体是这个软件哪个版本的镜像。如果不给出标签，将以 `latest` 作为默认标签。

以 [Ubuntu 镜像](https://hub.docker.com/_/ubuntu) 为例，`ubuntu` 是仓库的名字，其内包含有不同的版本标签，如，`16.04`, `18.04`。我们可以通过 `ubuntu:16.04`，或者 `ubuntu:18.04` 来具体指定所需哪个版本的镜像。如果忽略了标签，比如 `ubuntu`，那将视为 `ubuntu:latest`。

仓库名经常以 *两段式路径* 形式出现，比如 `jwilder/nginx-proxy`，前者往往意味着 Docker Registry 多用户环境下的用户名，后者则往往是对应的软件名。但这并非绝对，取决于所使用的具体 Docker Registry 的软件或服务。

## 使用镜像

### 获取镜像

[todo](https://yeasy.gitbook.io/docker_practice/image/pull)