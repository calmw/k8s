#### 创建部署

     kubectl create deployment nginx-cisco --image=nginx --port=80

#### 删除部署

    kubectl delete deployment nginx-cisco

#### 修改部署

    修改部署，通过下面方式更新部署定义，实际上会删除并重新创建nginx-cisco
    kubectl edit deployment/nginx-cisco