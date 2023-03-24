#### 介绍

    在Kubernetes中，基于HTTP的负载均衡系统名叫Ingress。
    Ingress有点类似于nginx的角色
    Nginx ingress是当前比较流行的开源的Ingress控制器

#### Ingress操作

    创建Ingress
        kubectl apply -f simple-ingress.yaml //文件中kind类型为： Ingress
    查看：
        kubectl get ingress
        kubectl describe ingress simple-ingress

#### 使用路径

    Ingress可以像nginx那样，根据path（/、/a/等）将将流量转发到不同服务

#### 提供TLS

    创建密钥
    kubectl create secret tls <机密名称> --cert <证书的pem文件> --key <证书的pem文件>
    证书生成后，通过命令上传，然后在操作Ingress时候使用

#### 命名空间

    Ingress只能引用同一命名空间的上游服务。