# Golang

## Dockerfile

```dockerfile
# 使用官方的 Go 语言镜像作为基础镜像
# 这里使用 Go 1.23.0 版本的 Alpine Linux 镜像
FROM golang:1.23.1-alpine AS builder

LABEL authors="wangqiang"

# 设置工作目录为 /app
# 所有后续操作都会在这个目录下进行
WORKDIR /app

# 将当前项目目录的所有文件拷贝到容器的 /app 目录中
COPY . .

# 设置 Go 模块代理为 https://goproxy.cn（在中国加速模块下载），并下载项目的依赖
RUN go env -w GOPROXY=https://goproxy.cn,direct && go mod download

# 编译 Go 项目，生成可执行文件 lang-chain-chat-server
RUN go build -o lang-chain-chat-server


# 使用一个更小的基础镜像（Alpine）来运行应用程序
# Alpine 是一个极简的 Linux 发行版，适合部署阶段
FROM alpine:latest

# 设置工作目录为 /app
WORKDIR /app

# 从编译阶段的镜像中拷贝编译后的二进制文件到运行镜像中
COPY --from=builder /app/lang-chain-chat-server /app/lang-chain-chat-server

# 暴露容器的 8080 端口，用于外部访问
EXPOSE 8000

# 设置容器启动时运行的命令
# 这里是运行编译好的可执行文件 lang-chain-chat-server
CMD ["/app/lang-chain-chat-server"]
```

