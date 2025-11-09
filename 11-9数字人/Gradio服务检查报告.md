# Gradio服务检查报告

## 📋 检查时间

2025-01-27

## 🔍 检查项目

### 1. 进程检查

#### 检查命令
```bash
docker exec starpainting-digital-human-service-1 ps aux | grep "python app.py" | grep -v grep
```

#### 预期结果
- ✅ 应该看到 `python app.py` 进程在运行
- ❌ 如果没有输出，说明进程未运行

#### 问题排查
如果进程未运行：
```bash
# 检查是否有其他Python进程
docker exec starpainting-digital-human-service-1 ps aux | grep python

# 检查是否有错误
docker exec starpainting-digital-human-service-1 tail -50 /app/HeyGem-Linux-Python-Hack/log/gradio.log
```

### 2. HTTP服务检查

#### 检查命令
```bash
curl -s -o /dev/null -w "Gradio HTTP: %{http_code}\n" http://localhost:7860
```

#### 预期结果
- ✅ HTTP 200 - 服务正常
- ❌ HTTP 000 - 服务未启动或无法连接
- ❌ HTTP 其他 - 服务异常

#### 问题排查
如果HTTP状态码不是200：
```bash
# 检查端口是否监听
docker exec starpainting-digital-human-service-1 netstat -tlnp | grep 7860

# 检查进程
docker exec starpainting-digital-human-service-1 ps aux | grep "python app.py"

# 查看日志
docker exec starpainting-digital-human-service-1 tail -50 /app/HeyGem-Linux-Python-Hack/log/gradio.log
```

### 3. FastAPI服务检查

#### 检查命令
```bash
curl -s http://localhost:8308/health
```

#### 预期结果
- ✅ `{"status":"ok","service":"digital-human-service"}` - 服务正常
- ❌ 无响应或错误 - 服务异常

### 4. 日志文件检查

#### 检查命令
```bash
docker exec starpainting-digital-human-service-1 ls -lh /app/HeyGem-Linux-Python-Hack/log/gradio.log
```

#### 预期结果
- ✅ 文件存在且有大小 - 日志正常
- ❌ 文件不存在 - 服务未启动或日志路径错误
- ❌ 文件大小为0 - 服务刚启动或日志未写入

#### 问题排查
如果日志文件不存在：
```bash
# 检查日志目录
docker exec starpainting-digital-human-service-1 ls -la /app/HeyGem-Linux-Python-Hack/log/

# 创建日志目录
docker exec starpainting-digital-human-service-1 mkdir -p /app/HeyGem-Linux-Python-Hack/log

# 重启服务
bash /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service/start_gradio.sh
```

## 🔧 常见问题及解决方案

### 问题1: 进程未运行

**症状**: `ps aux | grep "python app.py"` 无输出

**可能原因**:
1. 服务未启动
2. 服务启动失败
3. 进程崩溃

**解决方案**:
```bash
# 1. 检查日志查看启动失败原因
docker exec starpainting-digital-human-service-1 tail -50 /app/HeyGem-Linux-Python-Hack/log/gradio.log

# 2. 重新启动服务
bash /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service/start_gradio.sh

# 3. 等待15秒后检查
sleep 15
docker exec starpainting-digital-human-service-1 ps aux | grep "python app.py" | grep -v grep
```

### 问题2: HTTP服务无法访问

**症状**: `curl http://localhost:7860` 返回错误或超时

**可能原因**:
1. 服务未启动
2. 端口未监听
3. 防火墙或网络问题

**解决方案**:
```bash
# 1. 检查端口监听
docker exec starpainting-digital-human-service-1 netstat -tlnp | grep 7860

# 2. 检查进程
docker exec starpainting-digital-human-service-1 ps aux | grep "python app.py"

# 3. 查看日志
docker exec starpainting-digital-human-service-1 tail -50 /app/HeyGem-Linux-Python-Hack/log/gradio.log

# 4. 重启服务
bash /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service/start_gradio.sh
```

### 问题3: 日志文件不存在

**症状**: `ls -lh log/gradio.log` 文件不存在

**可能原因**:
1. 日志目录不存在
2. 服务未启动
3. 日志路径配置错误

**解决方案**:
```bash
# 1. 创建日志目录
docker exec starpainting-digital-human-service-1 mkdir -p /app/HeyGem-Linux-Python-Hack/log

# 2. 检查目录权限
docker exec starpainting-digital-human-service-1 ls -ld /app/HeyGem-Linux-Python-Hack/log

# 3. 重启服务
bash /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service/start_gradio.sh

# 4. 等待几秒后检查
sleep 5
docker exec starpainting-digital-human-service-1 ls -lh /app/HeyGem-Linux-Python-Hack/log/gradio.log
```

### 问题4: 日志文件为空

**症状**: 日志文件存在但大小为0

**可能原因**:
1. 服务刚启动，还未写入日志
2. 日志输出被重定向到其他地方
3. 服务启动失败

**解决方案**:
```bash
# 1. 等待几秒后再次检查
sleep 10
docker exec starpainting-digital-human-service-1 ls -lh /app/HeyGem-Linux-Python-Hack/log/gradio.log

# 2. 检查进程是否运行
docker exec starpainting-digital-human-service-1 ps aux | grep "python app.py"

# 3. 手动启动服务查看输出
docker exec -it starpainting-digital-human-service-1 bash
source /opt/conda/etc/profile.d/conda.sh
conda activate human
cd /app/HeyGem-Linux-Python-Hack
export PYTHONPATH=/app/HeyGem-Linux-Python-Hack
export LD_LIBRARY_PATH=/opt/conda/envs/human/lib:$LD_LIBRARY_PATH
python app.py
```

### 问题5: 服务启动失败

**症状**: 进程启动后立即退出

**可能原因**:
1. Python代码错误
2. 依赖包缺失
3. 环境变量未设置
4. 端口被占用

**解决方案**:
```bash
# 1. 查看详细错误日志
docker exec starpainting-digital-human-service-1 tail -100 /app/HeyGem-Linux-Python-Hack/log/gradio.log

# 2. 检查Python环境
docker exec starpainting-digital-human-service-1 bash -c "source /opt/conda/etc/profile.d/conda.sh && conda activate human && python --version"

# 3. 检查依赖
docker exec starpainting-digital-human-service-1 bash -c "source /opt/conda/etc/profile.d/conda.sh && conda activate human && python -c 'import gradio; print(gradio.__version__)'"

# 4. 检查端口占用
docker exec starpainting-digital-human-service-1 netstat -tlnp | grep 7860
```

## 📊 完整检查流程

### 步骤1: 基础检查
```bash
# 检查容器状态
docker ps | grep starpainting-digital-human-service

# 检查进程
docker exec starpainting-digital-human-service-1 ps aux | grep "python app.py" | grep -v grep
```

### 步骤2: 服务检查
```bash
# 检查HTTP服务
curl -s -o /dev/null -w "Gradio HTTP: %{http_code}\n" http://localhost:7860
curl -s http://localhost:8308/health
```

### 步骤3: 日志检查
```bash
# 检查日志文件
docker exec starpainting-digital-human-service-1 ls -lh /app/HeyGem-Linux-Python-Hack/log/gradio.log

# 查看日志内容
docker exec starpainting-digital-human-service-1 tail -50 /app/HeyGem-Linux-Python-Hack/log/gradio.log
```

### 步骤4: 错误检查
```bash
# 检查错误日志
docker exec starpainting-digital-human-service-1 tail -100 /app/HeyGem-Linux-Python-Hack/log/gradio.log | grep -i "error"
```

## 🔍 使用检查脚本

### 自动检查脚本
```bash
bash /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service/check_gradio_service.sh
```

### 脚本检查项目
1. ✅ 容器状态
2. ✅ Gradio进程
3. ✅ 日志目录
4. ✅ 日志文件
5. ✅ HTTP服务状态
6. ✅ BASE_PATH配置
7. ✅ 初始化日志
8. ✅ 错误日志
9. ✅ 端口占用
10. ✅ FastAPI服务

## 📝 检查结果记录

### 正常状态示例
```
✅ 容器运行中
✅ Gradio进程运行中
✅ 日志文件存在 (大小: 15KB)
✅ Gradio服务正常 (HTTP 200)
✅ FastAPI服务正常 (HTTP 200)
✅ 找到BASE_PATH配置日志
✅ 找到初始化日志
✅ 未发现错误日志
✅ 端口7860已监听
```

### 异常状态示例
```
❌ Gradio进程未运行
❌ 日志文件不存在
⚠️  Gradio服务状态异常 (HTTP 000)
⚠️  发现错误日志: [错误信息]
```

## 🔧 快速修复

### 如果所有检查都失败
```bash
# 完整重启服务
bash /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service/restart_service.sh
```

### 如果只是Gradio服务失败
```bash
# 只重启Gradio服务
bash /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service/start_gradio.sh
```

## 📅 更新日期

2025-01-27

## 🔗 相关文件

- `check_gradio_service.sh` - 自动检查脚本
- `start_gradio.sh` - Gradio服务启动脚本
- `restart_service.sh` - 完整服务重启脚本
- `Gradio服务检查清单.md` - 检查清单

