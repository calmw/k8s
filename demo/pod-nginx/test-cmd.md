#### 测试使用的命令

    1） 创建
        kubectl apply -f mysql-pod.yaml
    2）删除
        kubectl delete pods/mysql-cisco
        kubectl delete -f pods/mysql-pod.yaml
    3)查看Pod
        列表： kubectl get pods
        查看详情：kubectl describe pods mysql-cisco
    4）端口转发
        kubectl port-forward mysql-cisco 3306:3308
    5）查看日志
        kubectl logs -f mysql-cisco
    6)在容器中执行命令
        kubectl exec mysql-cisco -it  /bin/bash
    7)将本地文件复制到容器中
        kubectl cp $HOME/Desktop/a.txt mysql-cisco:/home/a.txt
    8)将容器文件复制到本地
        kubectl cp mysql-cisco:home/a.txt $HOME/Desktop/b.txt
        根据提示去掉了home文件夹前面的/才执行成功，home文件夹位于容器根目录下