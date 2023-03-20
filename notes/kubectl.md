#### 查看运行的版本

    kubectl version  会返回kubectl版本和Kubernetes API版本

#### 诊断一下集群，验证集群整体健康状况

    kubectl get componentstatuses
        controller-manager // 负责运行各种控制器，这些控制器的作用是调节集群行为。
        scheduler          // 负责将不同的pod放置到集群的不同节点上
        etcd-0             // 集群的存储

#### 列出集群的所有节点

    kubectl get nodes

#### 获取有关特定节点的更多信息

    kubectl describe nodes node-1