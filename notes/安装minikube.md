#### Mac安装minikube

    官方文档： https://minikube.sigs.k8s.io/docs
    1)安装kubectl 核心操作管理命令
        brew install kubernetes-cli
    2)使用brew安装minikube
        brew cask install minikube

#### 常用命令

    1）启动minikube（需翻墙）
        启动集群，初始化服务
        minikube start
    2）停止
        minikube stop
    3) 删除
        minikube delete