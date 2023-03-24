#### 创建副本集

    kubectl apply -f replicat-demo.yaml

#### 查看副本集

    kubectl get rs
    kubectl describe rs nginx-cisco

#### 删除副本

    命令如下：
        kubectl delete rs nginx-cisco
    删除副本集不删除Pod
        kubectl delete rs nginx-cisco --cascade=false

#### 伸缩副本

    扩展副本到4个
        kubectl scale replicasets nginx-cisco --replicas=4
    收缩副本到3个
        kubectl scale replicasets nginx-cisco --replicas=3
    自动伸缩
        kubectl autoscale rs nginx-cisco --min=5 --max=8 --cpu-percent=30 // 使用之前先创建副本
        自动伸缩查看、编辑和删除
        kubectl get hpa
        kubectl edit hpa rsName
        kubectl delete hpa rsName