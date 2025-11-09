# Gradio 服务状态报告

## 当前状态

### ✅ 容器内服务正常运行

- **Gradio 服务**: 运行中
- **进程**: `python app.py` (PID: 734)
- **监听地址**: 0.0.0.0:7860
- **HTTP 状态**: 200 OK
- **服务URL**: http://localhost:7860（容器内）

### ❌ 外部无法访问

- **外部访问**: http://192.168.191.56:7860 - **无法访问**
- **原因**: 容器没有映射 7860 端口到主机
- **当前端口映射**: 只有 8308 端口（FastAPI 服务）

## 问题分析

容器启动时只映射了 8308 端口（FastAPI 服务），没有映射 7860 端口（Gradio 服务）。

```bash
# 当前端口映射
8308/tcp -> 0.0.0.0:8308  ✅ FastAPI 服务
7860/tcp -> 未映射        ❌ Gradio 服务
```

## 解决方案

### 方案 1: 重新启动容器（添加端口映射）（推荐）

**注意**: 这会重启容器，FastAPI 服务会短暂中断。

#### 步骤 1: 停止当前容器

```bash
docker stop starpainting-digital-human-service-1
docker rm starpainting-digital-human-service-1
```

#### 步骤 2: 使用更新的启动脚本重新启动

```bash
cd /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service
bash start_with_shm.sh
```

**启动脚本已更新**，现在包含：
- `-p 8308:8308` - FastAPI 服务端口
- `-p 7860:7860` - Gradio 服务端口
- `--shm-size=2g` - 2GB 共享内存

#### 步骤 3: 启动 Gradio 服务

容器启动后，需要手动启动 Gradio 服务：

```bash
docker exec -d starpainting-digital-human-service-1 bash -c "source /opt/conda/etc/profile.d/conda.sh && conda activate human && cd /app/HeyGem-Linux-Python-Hack && nohup python app.py > /tmp/gradio.log 2>&1 &"
```

或者使用启动脚本：

```bash
cd /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service
bash start_gradio.sh
```

### 方案 2: 使用 docker commit 和重新运行（不推荐）

这个方案会保存当前容器状态，但比较复杂，不推荐。

### 方案 3: 使用 iptables 端口转发（临时方案）

```bash
# 获取容器 IP
CONTAINER_IP=$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' starpainting-digital-human-service-1)

# 设置端口转发（需要 root 权限）
sudo iptables -t nat -A DOCKER -p tcp --dport 7860 -j DNAT --to-destination $CONTAINER_IP:7860
sudo iptables -t nat -A POSTROUTING -p tcp -s $CONTAINER_IP -d $CONTAINER_IP --dport 7860 -j MASQUERADE
```

**注意**: 这个方案是临时的，容器重启后会失效。

## 验证方法

### 1. 检查端口映射

```bash
docker port starpainting-digital-human-service-1
```

**预期输出**:
```
8308/tcp -> 0.0.0.0:8308
7860/tcp -> 0.0.0.0:7860
```

### 2. 从外部访问 Gradio

```bash
curl http://192.168.191.56:7860/
```

**预期**: 返回 Gradio 网页内容（HTML）

### 3. 检查服务状态

```bash
# 检查容器内服务
docker exec starpainting-digital-human-service-1 curl -s http://localhost:7860/ | head -5

# 检查外部访问
curl -s http://192.168.191.56:7860/ | head -5
```

## 快速修复脚本

创建 `fix_gradio_port.sh`:

```bash
#!/bin/bash
# 修复 Gradio 端口映射

CONTAINER_NAME="starpainting-digital-human-service-1"

echo "========================================="
echo "修复 Gradio 端口映射"
echo "========================================="

# 1. 检查当前状态
echo "1. 检查当前端口映射..."
docker port $CONTAINER_NAME

# 2. 停止容器
echo ""
echo "2. 停止容器..."
docker stop $CONTAINER_NAME
docker rm $CONTAINER_NAME

# 3. 重新启动容器（包含 7860 端口映射）
echo ""
echo "3. 重新启动容器（包含 7860 端口映射）..."
cd /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service
bash start_with_shm.sh

# 4. 等待容器启动
echo ""
echo "4. 等待容器启动..."
sleep 10

# 5. 启动 Gradio 服务
echo ""
echo "5. 启动 Gradio 服务..."
docker exec -d $CONTAINER_NAME bash -c "source /opt/conda/etc/profile.d/conda.sh && conda activate human && cd /app/HeyGem-Linux-Python-Hack && nohup python app.py > /tmp/gradio.log 2>&1 &"

# 6. 等待服务启动
echo ""
echo "6. 等待 Gradio 服务启动..."
sleep 15

# 7. 验证
echo ""
echo "7. 验证服务..."
echo "端口映射:"
docker port $CONTAINER_NAME

echo ""
echo "Gradio 服务状态:"
docker exec $CONTAINER_NAME curl -s -o /dev/null -w "HTTP %{http_code}\n" http://localhost:7860/

echo ""
echo "外部访问测试:"
curl -s -o /dev/null -w "HTTP %{http_code}\n" http://192.168.191.56:7860/ || echo "无法访问"

echo ""
echo "========================================="
echo "修复完成！"
echo "Gradio 服务: http://192.168.191.56:7860"
echo "========================================="
```

## 相关文件

- `start_with_shm.sh` - 已更新，包含 7860 端口映射
- `start_gradio.sh` - Gradio 服务启动脚本
- `添加Gradio端口映射.md` - 详细说明

## 更新日期

2025-11-09

