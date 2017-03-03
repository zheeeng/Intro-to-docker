# Docker 是什么？

![docker log](./imgs/docker-logo.png)

Build, Ship and Run Any App, Anywhere


> Docker 原本是 dotCloud 公司工程师的业余作品，使用 Go 语言开发，基于 Linux Container （一种内核虚拟技术）进一步封装，开源后引起了广泛的讨论，最终变成了公司的主力产品，公司也改名为 Docker Inc.


> 一句话的描述： Docker 把App 装在 Container 内，通过 LXC 技术的包装将 App 变成一种标准化的、可移植的、自管理的组件，这种组件可以在任何地方开发、调试、运行，最终非常方便和一致地运行在生产环境下。


这些公司已经将 docker 应用在生产中：

![docker log](./imgs/companies.jpg)

远不止他们...


![docker log](./imgs/ecosystem.png)



<!-- .slide: data-background="#24B8EB" -->
# Docker 对比传统 VM


![docker tec](./imgs/virtualization.png)

![docker tec](./imgs/docker.png)


Docker 是轻量级操作系统虚拟化解决方案（简单说就是快速轻量级的虚拟机），原理是基于 Linux 容器（LXC）等技术。

> Docker 是在操作系统层面上实现虚拟化，直接复用本地主机的操作系统，而传统虚拟化则是在硬件层面实现。



<!-- .slide: data-background="#24B8EB" -->
# 容器又是什么？

容器运行起来感觉就是一个虚拟机，或者说，像一台真实的机器

* 本质是宿主机的进程，通过 namespace 实现隔离
* 将一个容器内的进程、数据、网络、系统资源独立出来
* 可以看作一个 Linux 环境


Docker 通过 namespace 实现了资源隔离，通过 cgroups 实现资源限制，通过写时复制机制(Copy-on-write)实现了高效的文件操作。


## namespace 资源隔离

Linux内核实现namespace的主要目的之一就是实现轻量级虚拟化(容器)服务。在同一个namespace下的进程可以感知彼此的变化，而对外界的进程一无所知。这样就可以让容器中的进程产生错觉，仿佛自己置身于一个独立的系统环境中，以达到独立和隔离的目的。


1. UTS : 主机与域名
2. IPC : 信号量、消息队列和共享内存
3. PID : 进程编号
4. NETWORK : 网络设备、网络栈、端口等
5. Mount : 挂载点(文件系统)
6. User : 用户和用户组


## cgroups 资源隔离

cgroup是Linux内核提供的一种机制，这种机制可以根据需求把一系列 任务及其子任务整合(或分隔)到按资源划分等级的不同组内，从而为系统资源管理提供一个统一的框架。


1. 资源限制： 可以对任务使用的资源的总额进行限制。如设定应用运行时使用内存的上限，一旦超过这个配额就发出OOM(Out of memory)提示。
2. 优先级分配 ：通过分配的CPU时间片数量及磁盘IO带宽大小，实际上就相当于控制了任务运行的优先级。
3. 资源统计：cgroups可以统计系统的资源使用量，如CPU使用时长、内存用量等，这个功能非常适用于计费。
4. 任务控制: cgroups可以对任务执行挂起、恢复等操作。


## Docker 分层存储(AUFS)

采用写时复制

1. 读：向下读取
2. 写：如果文件不存在，写入读写层，否则提取文件到读写层再写入
3. 删：如果文件仅读写层有，直接删除，否则，只在读写删除并标记为 without
4. 新建：如果己有对应 without 文件存在删除底层文件，否则在读写层新建


更多原理：

<https://goto.docker.com/docker-for-the-virtualization-admin.html>



<!-- .slide: data-background="#24B8EB" -->
# 容器有什么特征？


隔离性

> 进程隔离、数据独立、应用在 namespace 下运作，但是有合适的方法让容器相互通信（端口）

* [No] 一个容器可以装很多应用  
* [Yes] 但是建议使用多个容器构建一个应用


跨平台**(Build Once, Run Every)**，环境一致性

> Docker 的内容文件有唯一的标准，但他的运行时环境可以有多种方案。

* 环境安装、宿主机复杂的系统设置、不同的操作系统
* 软件版本
* 使用情景的冲突：开发者 vs 开源贡献者、公司环境 vs 家庭环境、开发环境 vs 生产环境


* 轻量化

> 快速启动、销毁，比传统 VM 有更优的 CPU、Memory、Disk I/O、Network I/O 性能


| 特性 | 容器 | 虚拟机 |
| --- | --- | --- |
| 启动 | 秒级 | 分钟级  |
| 大小 | MB | GB  |
| 效能 | 接近原生 | 比较慢 |
| 数量 | 单机上千个 | 单机十几个 |


* 可以做成大规模的集群
    * 技术上天然技术
    * 天然有负载平衡的方案


* 不仅运维在学习，开发者也很有必要掌握

    DevOps = software **Dev**elopment + information technology **Op**eration**s**

    ![docker tec](./imgs/apps.png)



<!-- .slide: data-background="#24B8EB" -->
# Docker 安装

* Windows:
    * Docker Toolbox
    * Docker for Windows (Microsoft Hyper-V )
* Linux:
    * 命令行
* MacOS:
    * Docker Toolbox
    * Docker for Mac (Mac HyperKit)



<!-- .slide: data-background="#24B8EB" -->
# Docker 的基本概念

* Image
* Container
* Registry



<!-- .slide: data-background="#24B8EB" -->
## Image

它是一个只读模板，容器构建在镜像之上

    docker commit
    docker build
    docker images
    docker rmi
    docker rmi $(docker images -f dangling=true -q)


### Dockfile

    ADD
    COPY
    ENTRYPOINT
    USER
    ONBUILD
    LABEL


<!-- .slide: data-background="#24B8EB" -->
## Container

    docker run
    docker create
    docker ps
    docker start
    docker stop
    docker rm


<!-- .slide: data-background="#24B8EB" -->
## Repository

仓库是集中存放镜像文件的场所。

    docker pull
    docker push



<!-- .slide: data-background="#24B8EB" -->
# 进阶的概念

* volumn

    查看 volumes
    
        docker inspect -f '{{ json .Config.volumes }}' container|image

    清除无用 volumes

        docker volume rm $(docker volume ls -f dangling=true -q)

    使用数据卷
    
        docker run -d --volumes-from container-with-dv --name db1 image


* network
* service
* swarm
* node



<!-- .slide: data-background="#24B8EB" -->
# 一些使用实践

