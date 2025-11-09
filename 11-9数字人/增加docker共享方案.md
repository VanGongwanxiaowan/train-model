# 增加 Docker 共享内存方案

## 当前状态

- **当前共享内存**: 64MB（67108864 字节）
- **推荐共享内存**: 2GB（用于多进程数据加载）
- **容器名称**: starpainting-digital-human-service-1
- **镜像**: heygem:v2.2

## 问题分析

PyTorch DataLoader 在多进程模式下需要使用共享内存来传递数据。当前 64MB 的共享内存对于多进程数据加载来说太小，可能导致：
1. 队列阻塞
2. 进程间通信失败
3. 数据加载超时

## 解决方案

### 方案 1: 使用 --shm-size 参数重新启动容器（推荐）✅

#### 步骤 1: 停止当前容器

```bash
docker stop starpainting-digital-human-service-1
docker rm starpainting-digital-human-service-1
```

#### 步骤 2: 使用增加共享内存的命令重新启动

```bash
docker run -d \
  --name starpainting-digital-human-service-1 \
  --restart unless-stopped \
  -p 8308:8308 \
  --gpus all \
  --shm-size=2g \
  -v /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service:/app \
  -v /data2/home_back/gujiaxin/work/batchshort1/assrt:/data2/home_back/gujiaxin/work/batchshort1/assrt \
  -e PYTHONUNBUFFERED=1 \
  heygem:v2.2 \
  bash start.sh
```

#### 关键参数说明

- `--shm-size=2g`: 设置共享内存为 2GB
  - `2g` = 2GB
  - `4g` = 4GB（如果 2GB 不够，可以增加到 4GB）
  - `8g` = 8GB（对于大型模型，可以增加到 8GB）

### 方案 2: 创建启动脚本（便于管理）

创建一个启动脚本 `start_with_shm.sh`:

```bash
#!/bin/bash
# 数字人服务启动脚本（增加共享内存）

# 停止并删除现有容器（如果存在）
docker stop starpainting-digital-human-service-1 2>/dev/null
docker rm starpainting-digital-human-service-1 2>/dev/null

# 启动新容器（共享内存 2GB）
docker run -d \
  --name starpainting-digital-human-service-1 \
  --restart unless-stopped \
  -p 8308:8308 \
  --gpus all \
  --shm-size=2g \
  -v /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service:/app \
  -v /data2/home_back/gujiaxin/work/batchshort1/assrt:/data2/home_back/gujiaxin/work/batchshort1/assrt \
  -e PYTHONUNBUFFERED=1 \
  heygem:v2.2 \
  bash start.sh

# 等待容器启动
sleep 3

# 检查容器状态
docker ps --filter "name=starpainting-digital-human-service-1"

# 检查共享内存
echo "检查共享内存大小:"
docker exec starpainting-digital-human-service-1 df -h /dev/shm
```

### 方案 3: 使用 docker-compose（如果未来迁移）

创建 `docker-compose.yml`:

```yaml
version: '3.8'

services:
  digital-human-service:
    image: heygem:v2.2
    container_name: starpainting-digital-human-service-1
    restart: unless-stopped
    ports:
      - "8308:8308"
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: all
              capabilities: [gpu]
    shm_size: 2g  # 共享内存 2GB
    volumes:
      - /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service:/app
      - /data2/home_back/gujiaxin/work/batchshort1/assrt:/data2/home_back/gujiaxin/work/batchshort1/assrt
    environment:
      - PYTHONUNBUFFERED=1
    command: bash start.sh
```

启动命令:
```bash
docker-compose up -d
```

## 验证方法

### 1. 检查共享内存大小

```bash
# 检查容器内的共享内存
docker exec starpainting-digital-human-service-1 df -h /dev/shm

# 预期输出:
# Filesystem      Size  Used Avail Use% Mounted on
# shm             2.0G     0  2.0G   0% /dev/shm
```

### 2. 检查容器配置

```bash
# 检查容器的共享内存配置
docker inspect starpainting-digital-human-service-1 --format '{{.HostConfig.ShmSize}}'

# 预期输出: 2147483648 (2GB = 2 * 1024 * 1024 * 1024 字节)
```

### 3. 测试服务功能

```bash
# 检查服务是否正常运行
curl http://localhost:8308/health

# 预期输出:
# {"status":"ok","service":"digital-human-service"}
```

## 共享内存大小建议

| 用途 | 推荐大小 | 说明 |
|------|---------|------|
| 单进程模式 (num_workers=0) | 64MB - 512MB | 当前配置足够 |
| 多进程模式 (num_workers=4) | 2GB - 4GB | 推荐 2GB |
| 大型模型 + 多进程 | 4GB - 8GB | 根据模型大小调整 |
| 多 GPU + 多进程 | 8GB+ | 大型部署 |

## 注意事项

1. **数据持久化**: 共享内存是临时存储，容器重启后数据会丢失
2. **系统限制**: 确保主机系统有足够的共享内存
   ```bash
   # 检查系统共享内存限制
   ipcs -lm
   ```
3. **性能影响**: 增加共享内存可能会增加内存使用，但不影响性能
4. **容器重启**: 修改共享内存需要重新创建容器

## 故障排查

### 问题 1: 容器启动失败

**错误**: `docker: Error response from daemon: invalid shm-size`

**解决**: 检查 `--shm-size` 参数格式，确保使用正确的单位（如 `2g`, `2048m`）

### 问题 2: 共享内存仍然太小

**错误**: 队列阻塞或进程间通信失败

**解决**: 增加共享内存大小到 4GB 或 8GB

### 问题 3: 系统共享内存不足

**错误**: 容器启动失败或系统报错

**解决**: 检查系统共享内存限制，必要时增加系统共享内存

```bash
# 检查系统共享内存
ipcs -lm

# 临时增加共享内存（需要 root 权限）
mount -o remount,size=4G /dev/shm
```

## 更新日期

2025-11-09

