# docker
### 1 docker 常用命令
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

### 2 dockerfile
from
run
cmd
entrypoint
add
copy

docker tag 源  目标:版本
构建镜像 	docker build -t 名字  .


### 3 docker网络
使用docker network ls

![Image](https://github.com/user-attachments/assets/ed12680a-6512-40b6-bd0d-9f236a5f7c24)
1. none 
k8s最开始就是这种模式，由网络插件负责网络，首先在pod中创建出pause容器，然后让其他容器与它共享网络
2. host
3. bridge
桥接模式 是指虚拟网卡和真实网卡都接到真实的交换机上
dokcer0是网桥 虚拟交换机
# K8S
### 架构

#### 组件

1. **API Server**  
   - 集群访问入口
   - 功能：认证、权限校验、配置文件解析
2. **etcd**  
   - 分布式键值存储，保存集群状态数据
3. **Controller Manager**  
   - 维护服务状态（如副本数、节点健康）
4. **Scheduler**  
   - 调度任务（分配Pod到合适的工作节点）

### kubectl使用

### 资源调度

### 存储