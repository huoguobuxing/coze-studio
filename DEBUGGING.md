# 在 VS Code 中调试 Coze Studio

本文档介绍了如何在 VS Code 中调试 Coze Studio 项目，包括后端 Go 服务和前端 React 应用。

## 前置要求

1. 安装 VS Code
2. 安装 Go 扩展 (golang.go)
3. 安装 Docker Desktop
4. 安装 Node.js 和 Rush

## 后端调试 (Go)

### 1. 启动依赖服务

在调试后端服务之前，需要先启动依赖的中间件服务：

```bash
# 使用任务启动依赖服务
Ctrl+Shift+P -> Tasks: Run Task -> Start Coze Studio Dependencies
```

或者在终端中运行：
```bash
docker compose -f docker/docker-compose-debug.yml --env-file docker/.env.debug --profile middleware up -d
```

### 2. 调试模式启动

在 VS Code 中，可以通过以下方式启动调试：

1. 打开调试面板 (Ctrl+Shift+D)
2. 选择 "Launch Coze Studio Backend" 配置
3. 按 F5 开始调试

### 3. 使用 Delve 调试器

对于更高级的调试需求，可以使用 Delve 调试器：

1. 构建带调试信息的二进制文件：
   ```bash
   # 使用任务构建
   Ctrl+Shift+P -> Tasks: Run Task -> Build Backend with Debug Info
   ```

2. 在外部终端运行：
   ```bash
   dlv exec --headless --listen=:40000 --api-version=2 --accept-multiclient bin/opencoze
   ```

3. 在 VS Code 中选择 "Connect to External Delve Server" 配置并启动调试

### 4. 调试测试代码

要调试测试代码：

1. 打开调试面板 (Ctrl+Shift+D)
2. 选择 "Debug Coze Studio Tests" 配置
3. 按 F5 开始调试测试

## 前端调试 (React)

### 1. 启动开发服务器

1. 打开调试面板 (Ctrl+Shift+D)
2. 选择 "Debug Coze Studio Frontend" 配置
3. 按 F5 启动调试

这将自动启动前端开发服务器并在 Chrome 中打开应用。

### 2. 手动启动前端开发服务器

```bash
# 进入前端应用目录
cd frontend/apps/coze-studio

# 启动开发服务器
rushx dev
```

然后在 Chrome 中打开 `http://localhost:8888`

## 全栈调试

要同时调试前后端：

1. 打开调试面板 (Ctrl+Shift+D)
2. 选择 "Debug Full Coze Studio" 配置
3. 按 F5 启动全栈调试

这将同时启动后端服务和前端开发服务器。

## 调试技巧

### 1. 日志级别

在调试时，可以通过修改 [docker/.env.debug](file:///d:/work/vscode/coze-studio/docker/.env.debug) 文件中的 `LOG_LEVEL` 环境变量来控制日志详细程度：

```bash
export LOG_LEVEL="debug"  # 或 "trace" 以获取更详细的日志
```

### 2. 断点调试

在 Go 代码中设置断点：
1. 打开 `.go` 文件
2. 点击行号左侧设置断点
3. 启动调试会话

### 3. 变量检查

在调试过程中，可以：
1. 在 Variables 面板中查看当前作用域的变量
2. 在 Debug Console 中执行表达式
3. 使用 "Watch" 面板监视特定变量或表达式

### 4. 条件断点

右键点击断点可以设置条件，只有满足条件时才会中断执行。

## 故障排除

### 1. 端口冲突

如果遇到端口冲突错误，检查是否有其他服务占用了相关端口：
- 后端服务: 8888
- MySQL: 3306
- Redis: 6379
- Elasticsearch: 9200

### 2. Docker 问题

如果 Docker 服务无法启动：
1. 确保 Docker Desktop 正在运行
2. 检查 Docker 资源分配（内存、CPU）
3. 查看 Docker 日志：
   ```bash
   docker logs <container_name>
   ```

### 3. 依赖服务未启动

如果后端服务无法连接到依赖服务：
1. 检查依赖服务是否已启动：
   ```bash
   docker compose -f docker/docker-compose-debug.yml ps
   ```
2. 查看服务日志：
   ```bash
   docker logs coze-mysql
   docker logs coze-redis
   ```

通过以上配置和指南，您应该能够在 VS Code 中有效地调试 Coze Studio 项目。