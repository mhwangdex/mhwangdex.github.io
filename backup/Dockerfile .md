# Dockerfile 使用指南

## 什么是 Dockerfile？

Dockerfile 是一个文本文件，包含了一系列指令，用于自动化构建 Docker 镜像。通过 Dockerfile，您可以定义容器环境的配置、安装的软件、暴露的端口等。

## 基本结构

一个典型的 Dockerfile 包含以下部分：

1. 基础镜像 (FROM)
2. 维护者信息 (LABEL)
3. 镜像操作指令 (RUN, COPY, ADD 等)
4. 容器启动时执行的命令 (CMD, ENTRYPOINT)

## 常用指令详解

### FROM
指定基础镜像，必须放在 Dockerfile 的第一条指令。

```dockerfile
FROM ubuntu:20.04
```

### LABEL
为镜像添加元数据（替代已废弃的 MAINTAINER）。

```dockerfile
LABEL maintainer="yourname@example.com"
LABEL version="1.0"
LABEL description="This is a custom Docker image"
```

### RUN
执行命令并创建新的镜像层，常用于安装软件包。

```dockerfile
RUN apt-get update && apt-get install -y \
    package1 \
    package2 \
    && rm -rf /var/lib/apt/lists/*
```

### COPY
将文件从构建上下文复制到容器中。

```dockerfile
COPY ./app /app
```

### ADD
类似于 COPY，但功能更多（支持自动解压和 URL 下载）。

```dockerfile
ADD https://example.com/big.tar.xz /usr/src/things/
```

### CMD
指定容器启动时默认执行的命令。

```dockerfile
CMD ["python", "app.py"]
```

### ENTRYPOINT
配置容器启动时运行的主命令。

```dockerfile
ENTRYPOINT ["python"]
CMD ["app.py"]
```

### ENV
设置环境变量。

```dockerfile
ENV NODE_ENV=production \
    APP_PORT=8080
```

### ARG
定义构建时的变量。

```dockerfile
ARG VERSION=latest
FROM ubuntu:$VERSION
```

### EXPOSE
声明容器运行时监听的端口。

```dockerfile
EXPOSE 80/tcp
EXPOSE 443/tcp
```

### WORKDIR
设置工作目录。

```dockerfile
WORKDIR /app
```

### USER
指定运行时的用户。

```dockerfile
USER nobody
```

### VOLUME
创建挂载点。

```dockerfile
VOLUME /data
```

## 最佳实践

1. **使用官方镜像**：尽量使用官方提供的基础镜像
2. **减少层数**：合并 RUN 指令以减少镜像层数
3. **清理缓存**：在安装软件后清理 apt/yum 缓存
4. **使用 .dockerignore**：排除不必要的文件
5. **多阶段构建**：减少最终镜像大小
6. **指定版本标签**：避免使用 latest 标签
7. **最小化镜像**：使用 alpine 等轻量级基础镜像

## 多阶段构建

多阶段构建可以显著减小最终镜像大小：

```dockerfile
# 构建阶段
FROM golang:1.16 AS builder
WORKDIR /go/src/app
COPY . .
RUN go build -o /go/bin/app

# 运行阶段
FROM alpine:latest
COPY --from=builder /go/bin/app /app
CMD ["/app"]
```

## .dockerignore 文件

类似于 .gitignore，用于排除不需要的文件：

```
.git
node_modules
*.log
Dockerfile
.dockerignore
```

## 构建和运行

构建镜像：

```bash
docker build -t my-image:1.0 .
```

运行容器：

```bash
docker run -d -p 8080:80 --name my-container my-image:1.0
```

## 示例 Dockerfile

```dockerfile
# 使用官方 Python 运行时作为基础镜像
FROM python:3.9-alpine

# 设置元数据
LABEL maintainer="dev@example.com"
LABEL version="1.0"

# 设置工作目录
WORKDIR /app

# 复制 requirements 文件
COPY requirements.txt .

# 安装依赖
RUN pip install --no-cache-dir -r requirements.txt

# 复制应用代码
COPY . .

# 暴露端口
EXPOSE 5000

# 定义环境变量
ENV FLASK_APP=app.py \
    FLASK_ENV=production

# 运行应用
CMD ["flask", "run", "--host=0.0.0.0"]
```

## 常见问题

1. **缓存问题**：使用 `--no-cache` 选项避免缓存
   ```bash
   docker build --no-cache -t my-image .
   ```

2. **调试构建**：如果构建失败，可以注释掉后续指令逐步调试

3. **查看镜像历史**：
   ```bash
   docker history my-image
   ```

4. **镜像大小优化**：使用 `docker images` 查看镜像大小，多阶段构建可以显著减小大小
```

这个 Markdown 文档涵盖了 Dockerfile 的主要方面，包括基本指令、最佳实践和实用示例。您可以根据需要进一步调整或扩展内容。