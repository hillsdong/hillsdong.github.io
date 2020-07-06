# k8s专栏学习

## 原理
- Docker 中的进程就是宿主机中的进程
- DOcker 之所以不能只启动一个容器是因为我们能启动的是一个进程，它需要一个进程
- Namespace 资源隔离
- Cgroups，Linux Control Group 资源限制
- lxcfs /proc/* 隔离，docker 没做
- Mount Namespace，来源自 chroot，Change Root
- Rootfs 根文件系统，不包含内核，内核在开机启动时加载，一致性来源
- UnionFS，Union File System，将文件系统分层后联合挂载
- 容器运行时：Namspace/Cgroup，容器镜像：Rootfs

## 
- k8s 来源自 Borg
- 运行在大规模集群中的各种任务之间，实际上存在着各种各样的关系。这些关系的处理，才是作业编排和管理系统最困难的地方
- 编排对象： Pod/Job/CronJob
- 服务对象： Service/Secret/Horizontal Pod Autoscaler
- k8s： 具有普遍意义的容器“编排”工具
- 声明式API： yaml配置文件







## Linux容器

一组联合挂载在 /var/lib/docker/aufs/mnt 上的 **rootfs**，这一部分我们称为“容器镜像”（Container Image），是容器的**静态视图**；

一个由 **Namespace+Cgroups** 构成的隔离环境，这一部分我们称为“容器运行时”（Container Runtime），是容器的**动态视图**。

## Kubernetes

![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LZ3QJsUDNun5pm8WWfX%2F-LZ7TqEGO91WDG8dn_F9%2F-LZ7VZWWKpXH9QRuluGS%2Fimage.png?alt=media&token=ad698397-f3f9-4ad1-ae37-9975325021a1)

## 架构

控制节点：kube-apiserverAPI服务 kube-scheduler调度 kube-controller-manager编排

计算节点：核心Kubelet

CRI (Container Runtime Interface) -> OCI(CRI请求->Namespace/Cgroups)

CNI(Container Networking Interface) CSI(Container Storage Interface) Device Plugin(gRPC协议)

**设计思想**：从宏观的角度，以统一方式定义任务之间的关系。

**Pod** Job/Pod/DaemonSet/CronJob... Service暴露IP Secret

**声明式API**：

nginx-deployment.yaml
$ kubectl create -f nginx-deployment.yaml

## Kubeadm

kubeadm kubelet kubectl

init流程
-   一系列检查
-   生成证书 /etc/kubernetes/，创建目录
-   生成配置文件 /etc/kubernetes/admin.conf controller-manager.conf kubelet.conf scheduler.conf
-   生成Pod配置文件 /etc/kubernetes/manifests/etcd.yaml kube-apiserver.yaml kube-controller-manager.yaml kube-scheduler.yaml，启动
-   生成bootstrap token，证书等通过ConfigMap方式保存在Etcd
-   安装默认插件，Pod(kube-proxy/DNS)
    
join流程：通过token获取证书，连接server