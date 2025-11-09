# Gradio服务重启指南

## 📋 概述

本文档说明如何重启数字人Gradio服务，包括停止旧服务、启动新服务、验证服务状态等步骤。

## 🚀 快速重启

### 方法1: 使用启动脚本（推荐）

```bash
# 使用启动脚本
bash /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service/start_gradio.sh
```

### 方法2: 使用重启脚本

```bash
# 使用重启脚本（会重启FastAPI和Gradio）
bash /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service/restart_service.sh
```

### 方法3: 手动重启

```bash
# 1. 停止旧的Gradio服务
docker exec starpainting-digital-human-service-1 pkill -f "python app.py"

# 2. 等待2秒
sleep 2

# 3. 确保日志目录存在
docker exec starpainting-digital-human-service-1 mkdir -p /app/HeyGem-Linux-Python-Hack/log

# 4. 启动Gradio服务（使用新的日志路径）
docker exec -d starpainting-digital-human-service-1 bash -c "source /opt/conda/etc/profile.d/conda.sh && conda activate human && cd /app/HeyGem-Linux-Python-Hack && export PYTHONPATH=/app/HeyGem-Linux-Python-Hack && export LD_LIBRARY_PATH=/opt/conda/envs/human/lib:\$LD_LIBRARY_PATH && nohup python app.py > log/gradio.log 2>&1 &"

# 5. 等待服务初始化（15秒）
sleep 15

# 6. 验证服务状态
curl http://localhost:7860
```

## ✅ 验证服务状态

### 检查进程

```bash
# 检查Gradio进程是否运行
docker exec starpainting-digital-human-service-1 ps aux | grep "python app.py" | grep -v grep
```

### 检查HTTP服务

```bash
# 检查Gradio服务
curl http://localhost:7860

# 或检查HTTP状态码
curl -s -o /dev/null -w "%{http_code}" http://localhost:7860
```

### 查看日志

```bash
# 实时查看Gradio日志（新路径）
docker exec starpainting-digital-human-service-1 tail -f /app/HeyGem-Linux-Python-Hack/log/gradio.log

# 查看最后50行
docker exec starpainting-digital-human-service-1 tail -50 /app/HeyGem-Linux-Python-Hack/log/gradio.log
```

## 📊 服务信息

### 服务地址
- **Gradio服务**: http://localhost:7860
- **FastAPI服务**: http://localhost:8308

### 日志文件
- **Gradio日志**: `/app/HeyGem-Linux-Python-Hack/log/gradio.log`
- **业务日志**: `/app/HeyGem-Linux-Python-Hack/log/dh.log`

## 🔍 日志查看

### 实时查看日志

```bash
# 查看Gradio日志
docker exec starpainting-digital-human-service-1 tail -f /app/HeyGem-Linux-Python-Hack/log/gradio.log

# 查看业务日志
docker exec starpainting-digital-human-service-1 tail -f /app/HeyGem-Linux-Python-Hack/log/dh.log
```

### 查看启动日志

```bash
# 查看Gradio启动日志
docker exec starpainting-digital-human-service-1 tail -50 /app/HeyGem-Linux-Python-Hack/log/gradio.log | grep -E "BASE_PATH|初始化|启动|Gradio|日志目录"
```

### 查看错误日志

```bash
# 查看Gradio错误日志
docker exec starpainting-digital-human-service-1 tail -100 /app/HeyGem-Linux-Python-Hack/log/gradio.log | grep -i "error"
```

## ⚠️ 注意事项

1. **初始化时间**: Gradio服务需要约15秒初始化
2. **日志路径**: 新日志路径为 `log/gradio.log`（不再是 `/tmp/gradio.log`）
3. **目录权限**: 确保容器有权限在 `log` 目录下创建和写入文件
4. **服务端口**: Gradio服务使用7860端口，确保端口未被占用

## 🔧 故障排查

### 问题1: 服务无法启动

```bash
# 检查进程
docker exec starpainting-digital-human-service-1 ps aux | grep "python app.py"

# 查看日志
docker exec starpainting-digital-human-service-1 tail -50 /app/HeyGem-Linux-Python-Hack/log/gradio.log

# 检查端口
docker exec starpainting-digital-human-service-1 netstat -tlnp | grep 7860
```

### 问题2: 日志文件不存在

```bash
# 检查日志目录
docker exec starpainting-digital-human-service-1 ls -la /app/HeyGem-Linux-Python-Hack/log/

# 创建日志目录
docker exec starpainting-digital-human-service-1 mkdir -p /app/HeyGem-Linux-Python-Hack/log
```

### 问题3: 端口被占用

```bash
# 检查端口占用
docker exec starpainting-digital-human-service-1 netstat -tlnp | grep 7860

# 停止占用端口的进程
docker exec starpainting-digital-human-service-1 pkill -f "python app.py"
```

## 📝 完整重启命令（一键执行）

```bash
# 完整的重启命令
docker exec starpainting-digital-human-service-1 pkill -f "python app.py" && \
sleep 2 && \
docker exec starpainting-digital-human-service-1 mkdir -p /app/HeyGem-Linux-Python-Hack/log && \
docker exec -d starpainting-digital-human-service-1 bash -c "source /opt/conda/etc/profile.d/conda.sh && conda activate human && cd /app/HeyGem-Linux-Python-Hack && export PYTHONPATH=/app/HeyGem-Linux-Python-Hack && export LD_LIBRARY_PATH=/opt/conda/envs/human/lib:\$LD_LIBRARY_PATH && nohup python app.py > log/gradio.log 2>&1 &" && \
sleep 15 && \
echo "Gradio服务重启完成！" && \
curl -s -o /dev/null -w "Gradio服务状态: HTTP %{http_code}\n" http://localhost:7860
```

## 📅 更新日期

2025-01-27

## 🔗 相关文件

- `start_gradio.sh` - Gradio服务启动脚本
- `restart_service.sh` - 完整服务重启脚本
- `app.py` - Gradio应用（已配置新日志路径）
- `log/gradio.log` - Gradio日志文件（新路径）

