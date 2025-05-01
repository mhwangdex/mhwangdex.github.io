# docker

### 1 docker 常用命令

```
 镜像相关
docker images
docker pull
docker push
docker tag
docker build -t 
docker rmi

容器相关
docker ps
docker ps -a
docker run  -d -ti -p --name
docker exec
```

### 2 dockerfile

```
from
run
cmd
entrypoint
add
copy

docker tag 源  目标:版本
构建镜像    docker build -t 名字  .
```

### 3 docker网络

**使用docker network ls**

![image.png](https://cdn.jsdelivr.net/gh/mhwangdex/note-gen-image-sync@main/2025-04/8facfdc0-ca78-4d69-b7e4-0aa18c683a31.png)

1. **none** **k8s最开始就是这种模式，由网络插件负责网络，首先在pod中创建出pause容器，然后让其他容器与它共享网络**
2. **host**
3. **bridge** **桥接模式 是指虚拟网卡和真实网卡都接到真实的交换机上** **dokcer0是网桥 虚拟交换机**

# K8S

### 一 架构

#### 组件

1. **API Server** **集群访问入口** **功能：认证、权限校验、配置文件解析**
2. **etcd** **分布式键值存储，保存集群状态数据**
3. **Controller Manager** **维护服务状态（如副本数、节点健康）**
4. **Scheduler** **调度任务（分配Pod到合适的工作节点）**

### 架构

![image.png](https://cdn.jsdelivr.net/gh/mhwangdex/note-gen-image-sync@main/2025-04/91270e75-197f-4bfe-b54b-04002b70a9e9.png)

### 二 kubectl使用

#### 基础命令

**查看**

```
kubectl get deploy/pod 具体的名字 -o wide 
kubectl get deploy 名字 -o yaml

kubectl logs -f pod名

kubectl describe node 名字
```

**增加**

```
kubectl apply -f 1.yaml

kubectl run -i --image nginx:last sh
```

**改**

```
# 修改副本
kubectl scale deployment xxx --replicas=0

# 在线编辑
kubectl edit deployment xxx
```

**删**

```
kubectl delete deplogment xxx

kubectl delete pods  xxx

kubectl delete -f 1.yaml
```

**进入pod内**

```
kubectl exec -ti pod名字 bash

# 有多个容器，用-c指定查看哪一个容器
kubectl logs -c c2 xxx
kubectl exec -ti xxx -c c2 sh
```

### 三 创建与删除资源

#### 资源类型

```
pod是k8s中最小的资源单位

deployment 是一种控制器资源--》replicaset控制器----> pod
```

#### 创建pod

```
vi 1.yaml #可以编写一个yaml文件
kubectl get pod xxx -o yaml  #导出一个yaml
kubectl apply 1.yaml
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: web
  name: web
  namespace: default

spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: web
    spec:
      containers:
      - image: registry.cn-shanghai.aliyuncs.com/egon-k8s-test/busybox:v1.0
        name: c1
        command: ["sh","-c","while true;do echo 111 ;sleep 1;done"]
      - image: registry.cn-shanghai.aliyuncs.com/egon-k8s-test/busybox:v1.0
        name: c2
        command: ["sh","-c","while true;do echo 222 ;sleep 1;done"]
status: {}
```

```
# API 版本声明，指定使用的 Kubernetes API 版本
apiVersion: apps/v1
# 资源类型，这里定义的是一个 Deployment
kind: Deployment
# 元数据部分，包含资源的名称、标签等信息
metadata:
  creationTimestamp: null  # 创建时间戳，通常由系统自动生成
  labels:                  # 标签，用于标识和组织资源
    app: web               # 为该 Deployment 设置一个 app=web 的标签
  name: web                # Deployment 的名称
  namespace: default       # 部署到的命名空间，这里是默认的 default 命名空间

# Deployment 的规格说明
spec:
  replicas: 1  # 指定 Pod 的副本数量，这里设置为 1 个
  # 选择器，用于匹配要管理的 Pod
  selector:
    matchLabels:
      app: web  # 选择所有具有 app=web 标签的 Pod
  strategy: {}  # 更新策略，空对象表示使用默认策略（滚动更新）
  # Pod 模板，用于创建新的 Pod
  template:
    metadata:  # Pod 的元数据
      creationTimestamp: null
      labels:
        app: web  # 为 Pod 设置 app=web 的标签，与上面的选择器匹配
    spec:  # Pod 的规格说明
      containers:  # 容器列表
      # 第一个容器
      - image: registry.cn-shanghai.aliyuncs.com/egon-k8s-test/busybox:v1.0  # 容器镜像
        name: c1  # 容器名称
        # 容器启动时执行的命令
        command: ["sh","-c","while true;do echo 111 ;sleep 1;done"]
      # 第二个容器
      - image: registry.cn-shanghai.aliyuncs.com/egon-k8s-test/busybox:v1.0  # 容器镜像
        name: c2  # 容器名称
        # 容器启动时执行的命令
        command: ["sh","-c","while true;do echo 222 ;sleep 1;done"]
# 状态信息，通常由 Kubernetes 自动维护
status: {}

这个 Deployment 配置定义了一个名为 "web" 的部署，它会创建一个 Pod，该 Pod 中包含两个容器：=
容器 c1：每秒输出 "111"
容器 c2：每秒输出 "222"
```

### 四 水平扩缩

### 五 资源调度

**节点污点**

**pod调度策略**

### 六 secret

### 七 ConfigMap

### 八 存储编排

**本地存储**

**网络存储**

### 九 服务发现和负载均衡

**service是什么？**

### 十 自我恢复

**pod重启机制**

**pod健康检查**

### 十一 自动上线和回滚

**升级**

**回滚**