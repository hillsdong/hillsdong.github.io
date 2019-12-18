# k8s专栏学习

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