#### 介绍

    管理新版本的发布，我们需要使用部署（Deployment）对象。部署可以帮助你将代码从一个版本转移到下一个版本，部署会在升级各个Pod之间等待一段时间。你可以借助部署轻松可靠的推出新的软件版本，而且不会造成停机或引发错误。
    K8S最初发布时，最受欢迎的功能演示之一就是"滚动更新"，它展示了如何通过一个命令无缝地更新正在运行的应用程序，而且不会造成停机，也不会丢失请求。
    Pod由副本集管理，而副本集由部署管理。

#### 创建部署

    部署可以使用声明式的yaml对象表示
    创建部署命令：
        1）kubectl create deployment nginx-cisco --image=nginx --port=80 // 直接通过命令行部署 
        2）kubectl create -f kuard-deployment.yaml //通过声明式的yaml对象部署

#### 管理、查看部署

    查看部署
        kubectl get deployments kuard
        kubectl describe deployments kuard
    其他命令：
        kubectl rollout history deployments kuard  // 获取与特定部署相关的推出历史记录，
        kubectl rollout status deployments kuard  // 获取推出的当前状态
        kubectl rollout pause deployments kuard  // 暂停推出

#### 删除部署

    kubectl delete deployments nginx-cisco
    kubectl delete -f nginx-cisco-deployment.yaml

#### 暂停部署

    kubectl rollout pause deployments kuard  // 暂停推出

#### 回滚到之前部署

    回滚到上一次推出
        kubectl rollout undo deployments kuard  // 回滚到上一次推出
    回滚到历史某一次推出
        1）查询退出历史
            kubectl rollout history deployments kuard
        2）根据历史版本号，回滚到指定版本
            kubectl rollout undo deployments kuard  --to-revision=3 // 根据上一步REVISION字段，回滚到指定版本

#### 修改部署

    修改部署，通过下面方式更新部署定义，实际上会删除并重新创建nginx-cisco
    kubectl edit deployments/nginx-cisco

#### 调整部署的大小

    可以使用下面命令来操作部署的大小
        kubectl scale deployments kuard --replicas=2 // 执行完之后，可以查看副本集大小： kubectl get replicasets --selector=run=kuard
        此时再去通过副本集伸缩命令去操作副本集的伸缩，会失败。因为K8S是一个在线自我修复系统，该副本集由最顶层的部署对象管理

#### 将部署下载到yaml文件中

    kubectl get deployment kuard --export -o yaml > kuard-deployment.yaml

#### 部署策略

    在更新软件的服务方面，K8S支持两种策略：
        1）Recreate 重建， 适用于科技手段暂停机的测试部署
            该策略相比较为简单，他只更新自己管理的副本集，让他们使用新镜像，并终止所有与部署关联的Pod。当副本集会注意到自己没有任何副本时，就会用新的镜像重建所有Pod。重新创建的Pod会运行新版本。尽管这个策略又快又简单，但他有一个重大缺点，可能会造成灾难性后果，就是一定会造成服务停机一段时间。
        2）RollingUpdate 滚动更新， 适用于面向用户的服务
            一次只更新几个Pod，直到左右的Pod都运行新版本的软件。比重建策略慢，也更复杂，但更健壮。该策略可以在接受用户流量的同时推出新版本的服务，不会造成停机。
    maxunavailable参数的核心思想是滚动更新的进行速度，如果设置为50%，假设有4个副本集，他会先将副本集删除到原来的一半，然后用新副本集替换掉删除的两个，在接下来会重复上面两个步骤，来完成更新。
    其实Recreate 重建策略只是将maxunavailable参数设置为100%。
    maxSurge参数控制可以创建多少资源来确保成功的实施推出。为了说明其中的工作原理，我们假设某个服务包含10个副本。我们将maxunavailable设置为0，并将maxSurge设置为20%，首先，部署需要将新的副本扩展到两个副本，这是总副本数为12个（120%）；接下来，将旧副本数量缩减到8个，这是服务的总副本数为10，这个过程会一直持续到退出完成。在此期间服务的容量至少为100%，而推出使用的最大额外资源为所有资源的20%。 将maxSurge设置到100%相当于蓝绿部署。首先控制器将新版本扩展到旧版本的100%，等到新版本运行良好之后，再将旧版本缩减到0%。
    maxunavailable和maxSurge可以在yaml文件中设置

#### 放慢部署以确保服务健康

    分阶段部署的目的是确保推出完成之后，我们能够获得一个健康、稳定的、运行新版本软件的服务。为了实现这一点，再继续推出下一个Pod之前，部署控制器会等Pod报告已准备就绪。
    部署控制器会检查Pod状态，而该状态由Pod的就绪检查（就绪探针）决定。就绪检查是Pod健康检查的一部分，如果你想使用部署可靠的退出软件，则必须为Pod中的容器指定健康检查。如果没有这些健康检查，则部署控制器只能盲目的运行。
    minReadySeconds：在大多数情况下，你需要等待一段时间，才能完全确信新版本能够完全正常运行，然后再继续更新下一个Pod。对于部署，这段等待时间由参数minReadySeconds定义：minReadySeconds设置为60的意思是部署必须在Pod运行正常60s后，然后再继续更新下一个Pod。
    progressDeadlineSeconds: 超时设置，超时是一个至关重要的例外，它可以触发回滚部署，或者创建任务票/事件，触发人为干预。例如：新版本服务存在会立即陷入死锁的错误，那么它永远无法进入就绪状态，这种情况下没有设置超时的话，推出将陷入无限的停滞。这种情况下正常行为是暂停推出。