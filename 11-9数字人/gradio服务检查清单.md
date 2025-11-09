# Gradio服务检查清单

## 📋 检查项目

### 1. 容器状态检查
```bash
docker ps | grep starpainting-digital-human-service
```
- ✅ 容器运行中
- ❌ 容器未运行

### 2. Gradio进程检查
```bash
docker exec starpainting-digital-human-service-1 ps aux | grep "python app.py" | grep -v grep
```
- ✅ 进程运行中
- ❌ 进程未运行

### 3. 日志目录检查
```bash
docker exec starpainting-digital-human-service-1 ls -la /app/HeyGem-Linux-Python-Hack/log/
```
- ✅ 目录存在
- ❌ 目录不存在

### 4. 日志文件检查
```bash
docker exec starpainting-digital-human-service-1 ls -lh /app/HeyGem-Linux-Python-Hack/log/gradio.log
```
- ✅ 文件存在且有内容
- ❌ 文件不存在或为空

### 5. HTTP服务检查
```bash
curl http://localhost:7860
```
- ✅ HTTP 200 - 服务正常
- ❌ 其他状态码 - 服务异常

### 6. BASE_PATH配置检查
```bash
docker exec starpainting-digital-human-service-1 tail -50 /app/HeyGem-Linux-Python-Hack/log/gradio.log | grep "BASE_PATH"
```
- ✅ 找到配置日志
- ❌ 未找到配置日志

### 7. 服务初始化检查
```bash
docker exec starpainting-digital-human-service-1 tail -50 /app/HeyGem-Linux-Python-Hack/log/gradio.log | grep -E "初始化|启动|Gradio|Running"
```
- ✅ 找到初始化日志
- ❌ 未找到初始化日志

### 8. 错误日志检查
```bash
docker exec starpainting-digital-human-service-1 tail -100 /app/HeyGem-Linux-Python-Hack/log/gradio.log | grep -i "error"
```
- ✅ 未发现错误
- ❌ 发现错误

### 9. 端口占用检查
```bash
docker exec starpainting-digital-human-service-1 netstat -tlnp | grep 7860
```
- ✅ 端口已监听
- ❌ 端口未监听

### 10. FastAPI服务检查
```bash
curl http://localhost:8308/health
```
- ✅ 服务正常
- ❌ 服务异常

## 🔍 快速检查命令

### 使用检查脚本（推荐）
```bash
bash /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service/check_gradio_service.sh
```

### 一键检查命令
```bash
echo "=== Gradio服务检查 ===" && \
docker exec starpainting-digital-human-service-1 ps aux | grep "python app.py" | grep -v grep && echo "✅ 进程运行" || echo "❌ 进程未运行" && \
curl -s -o /dev/null -w "HTTP: %{http_code}\n" http://localhost:7860 && \
docker exec starpainting-digital-human-service-1 ls -lh /app/HeyGem-Linux-Python-Hack/log/gradio.log 2>&1
```

## 📊 检查结果示例

### 正常状态
```
✅ 容器运行中
✅ Gradio进程运行中
✅ 日志目录存在
✅ 日志文件存在且有内容
✅ Gradio服务正常 (HTTP 200)
✅ FastAPI服务正常 (HTTP 200)
✅ 找到BASE_PATH配置日志
✅ 找到初始化日志
✅ 未发现错误日志
✅ 端口7860已监听
```

### 异常状态
```
❌ Gradio进程未运行
❌ 日志文件不存在
⚠️  Gradio服务状态异常 (HTTP 000)
⚠️  发现错误日志
```

## 🔧 故障排查

### 如果进程未运行
```bash
# 重启服务
bash /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service/start_gradio.sh
```

### 如果日志文件不存在
```bash
# 创建日志目录
docker exec starpainting-digital-human-service-1 mkdir -p /app/HeyGem-Linux-Python-Hack/log

# 重启服务
bash /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service/start_gradio.sh
```

### 如果服务无法访问
```bash
# 检查进程
docker exec starpainting-digital-human-service-1 ps aux | grep "python app.py"

# 查看日志
docker exec starpainting-digital-human-service-1 tail -50 /app/HeyGem-Linux-Python-Hack/log/gradio.log

# 检查端口
docker exec starpainting-digital-human-service-1 netstat -tlnp | grep 7860
```

## 📝 检查脚本

已创建检查脚本: `check_gradio_service.sh`

使用方法:
```bash
bash /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service/check_gradio_service.sh
```

## 📅 更新日期

2025-01-27

