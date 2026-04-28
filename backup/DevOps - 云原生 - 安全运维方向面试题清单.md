以下是为您精心整理的 **完整版 DevOps / 云原生 / 安全运维方向面试题清单**，内容涵盖：

- 个人介绍模板  
- Docker 核心知识  
- Kubernetes 架构与原理  
- 监控系统（Prometheus）  
- 服务器应急响应流程  
- 网络基础与协议分层  
- 企业资产测绘工具（ENScan_GO）实操要点  

全部内容**结构清晰、技术准确、语言简洁**，适合用于实习生/初级工程师岗位面试准备。

---

# 🧑‍💻 DevOps / 云原生 / 安全运维面试题完整版

---

## 0. 自我介绍（建议 1 分钟内）

> “您好，我是 XXX，目前就读于 XX 大学 XX 专业。我对 DevOps 和云计算方向有浓厚兴趣，自学并实践了 Docker、Kubernetes 等容器化技术。在校期间，我使用 GitLab CI/CD 实现了一套自动化部署流程，并尝试在本地部署 Stable Diffusion 等 AI 模型。  
>  
> 希望能通过这次实习机会，将理论知识应用到真实业务场景中，提升工程化能力和安全运维意识。”

---

## 1. 你常用哪些 Docker 命令？请举例说明

- `docker run -d -p 8080:80 --name mynginx nginx`：启动容器
- `docker ps / ps -a`：查看运行中 / 所有容器
- `docker images`：列出本地镜像
- `docker build -t myapp:v1 .`：构建镜像
- `docker exec -it <container> bash`：进入容器调试
- `docker logs -f <container>`：实时查看日志
- `docker stop / start / restart`：控制容器生命周期
- `docker rm / rmi`：删除容器或镜像
- `docker network create mynet`：创建自定义网络

---

## 2. Dockerfile 中常用指令有哪些？

- `FROM`：基础镜像
- `RUN`：构建时执行命令
- `COPY`：复制本地文件（推荐）
- `ADD`：支持 URL 下载和自动解压（慎用）
- `WORKDIR`：设置工作目录
- `EXPOSE`：声明端口（文档作用）
- `ENV`：设置环境变量
- `CMD`：容器启动命令（可覆盖）
- `ENTRYPOINT`：入口点（与 CMD 配合）
- `VOLUME`：定义持久化卷
- `ARG`：构建参数
- `LABEL`：添加元数据

---

## 3. `COPY` 和 `ADD` 的区别？

| 特性 | `COPY` | `ADD` |
|------|--------|--------|
| 功能 | 仅复制本地文件 | 支持 URL 下载 + 自动解压 tar |
| 安全性 | 行为明确，推荐使用 | 行为“魔法”，易出问题 |
| 最佳实践 | 优先使用 | 仅在需解压或下载时使用 |

示例：
```dockerfile
COPY app.py /app/                     # ✅ 推荐
ADD app.tar.gz /app/                  # 自动解压
ADD https://example.com/file.txt /tmp # 从网络下载（不推荐）
```

---

## 4. Kubernetes 架构是怎样的？

### 控制平面（Control Plane）
- **API Server**：集群唯一入口，处理 REST 请求
- **etcd**：存储集群所有状态
- **Scheduler**：调度 Pod 到节点
- **Controller Manager**：运行控制器（如 ReplicaSet）

### 工作节点（Node）
- **kubelet**：管理本机 Pod
- **kube-proxy**：维护网络规则，实现 Service 负载均衡
- **容器运行时**：如 containerd

### 扩展组件
- **CNI**（Calico/Flannel）：Pod 网络
- **CoreDNS**：集群 DNS 服务

---

## 5. Pod 之间如何通信？

- **同 Pod 内容器**：通过 `localhost` 共享网络命名空间
- **同节点不同 Pod**：通过虚拟网桥（veth pair）通信
- **跨节点 Pod**：由 CNI 插件（如 VXLAN、BGP）打通
- **通过 Service**：
  - `ClusterIP`：集群内虚拟 IP，kube-proxy 转发
  - `NodePort` / `LoadBalancer` / `Ingress`：对外暴露

> 所有通信基于 “每个 Pod 有唯一 IP” 的网络模型。

---

## 6. Kubernetes 存储体系（PV / PVC / StorageClass）

- **PV（PersistentVolume）**：集群中预配置的存储资源（如 NFS、云盘）
- **PVC（PersistentVolumeClaim）**：用户对存储的请求
- **StorageClass**：定义存储类型，支持**动态供给**

> 流程：用户创建 PVC → 自动绑定/创建 PV → Pod 通过 `volumeMounts` 使用

---

## 7. Kubernetes 的配置和状态存在哪里？

全部存储在 **etcd** 中：
- 分布式、强一致、基于 Raft
- 存储内容：Pod、Service、Node、ConfigMap 等所有对象
- **仅 API Server 可直接访问 etcd**
- 需定期备份（`etcdctl snapshot save`）

---

## 8. Kubernetes API Server 的作用？

- 集群的**唯一入口**
- 验证授权、读写 etcd、提供 REST API
- `kubectl` 本质是其客户端
- 可通过 `kubectl proxy` 本地代理访问：
  ```bash
  kubectl proxy --port=8080
  curl http://localhost:8080/api/v1/pods
  ```

---

## 9. Kubernetes 认证与授权机制？

### 认证（Authentication）
- **KUBECONFIG**：用户凭证（证书/Token）
- **ServiceAccount**：Pod 内身份，自动挂载 Token

### 授权（RBAC）
- **Role / ClusterRole**：定义权限规则（如 get pods）
- **RoleBinding / ClusterRoleBinding**：绑定角色到用户或 SA

> 示例：限制某 Pod 只能读取本命名空间的 Pods

---

## 10. 你了解 Prometheus 吗？如何监控 K8s？

- **Prometheus Server**：抓取、存储时序数据
- **Exporters**：暴露指标（如 node_exporter）
- **Alertmanager**：告警处理
- **kube-state-metrics**：采集 K8s 对象状态
- **Grafana**：可视化面板

> 生产中常用 **Prometheus Operator** 自动管理组件。

---

## 11. 服务器出现异常进程/网络连接，如何应急响应？

### 排查步骤：
1. **`top` / `htop`**：看 CPU/内存异常
2. **`ps auxf`**：查进程路径、用户、伪装名
3. **`/proc/<PID>/exe`**：定位文件（注意 `deleted`）
4. **检查 `/bin` 等目录时间戳**：是否被替换系统命令
5. **`netstat -antp` / `ss -tulnp`**：查异常外联
6. **`kill -9 <PID>`**：终止进程

### 若进程重启：
- 检查 **定时任务**：
  - 用户级：`crontab -l`、`/var/spool/cron/`
  - 系统级：`/etc/crontab`、`/etc/cron.d/`
- **`pstree -p <PID>`**：查父子进程，一锅端
- 检查 **systemd 服务**、`/etc/rc.local`

> ✅ 原则：先取证，再处置；必要时保留内存镜像。

---

## 12. IP 地址、交换机、路由器的作用？

- **IP 地址**：网络层唯一标识主机（如手机号）
- **路由器**（三层设备）：
  - 根据 IP 路由表转发数据包
  - 家庭网关（如 `192.168.1.1`）连通内外网
- **交换机**（二层设备）：
  - 基于 MAC 地址表转发帧
  - 三层交换机支持路由功能

---

## 13. 电脑如何访问一个网站？（以抖音为例）

1. **本地配置**：IP、子网掩码、网关、DNS（通过 `ipconfig` 查看）
2. **DNS 解析**：`douyin.com` → IP（如 `110.242.68.4`）
3. **TCP 三次握手**：建立连接（端口 443）
4. **TLS 握手**：验证证书，协商密钥（HTTPS）
5. **数据封装**（自上而下）：
   - 应用层：HTTP 请求
   - 传输层：TCP 头（端口）
   - 网络层：IP 头（IP 地址）
   - 数据链路层：MAC 帧
6. **路由转发**：发给网关 → 路由器逐跳转发至抖音服务器
7. **响应返回**：浏览器渲染页面

---

## 14. 常见网络协议分层

| 层级 | 协议 |
|------|------|
| 应用层 | HTTP、HTTPS、DNS、SSH、SMTP、FTP |
| 传输层 | TCP、UDP |
| 网络层 | IP、ICMP（ping）、ARP |
| 数据链路层 | Ethernet、Wi-Fi |
| 物理层 | 网线、光纤 |






