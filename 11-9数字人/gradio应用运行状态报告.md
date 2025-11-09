# Gradio 应用运行状态报告

## ✅ 应用状态：运行中

**应用已成功启动并运行！**

### 运行信息

- **进程状态**: ✅ 运行中
- **进程 PID**: 527
- **HTTP 状态**: 200 OK
- **服务端口**: 7860（容器内）
- **应用标题**: 数字人视频生成/Digital Human Video Generation
- **Gradio 版本**: 4.44.1

### 验证结果

```bash
# 进程检查
✓ python app.py 进程运行中

# 端口检查
✓ 端口 7860 已监听

# HTTP 检查
✓ HTTP 状态码: 200
✓ 页面标题: 数字人视频生成/Digital Human Video Generation
```

## 🌐 访问方式

### 当前状态

应用在容器内正常运行，但 **容器没有映射 7860 端口到主机**。

### 访问选项

#### 选项1: 添加端口映射（推荐）

需要重启容器并添加端口映射：

```bash
# 1. 停止当前容器
docker stop starpainting-digital-human-service-1

# 2. 删除容器（如果需要修改配置）
docker rm starpainting-digital-human-service-1

# 3. 重新创建容器并添加 7860 端口映射
docker run -d \
  --name starpainting-digital-human-service-1 \
  --gpus all \
  -p 8308:8308 \
  -p 7860:7860 \
  -v /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service:/app \
  heygem:v2.2

# 4. 重新启动应用
docker exec starpainting-digital-human-service-1 bash -c "source /opt/conda/etc/profile.d/conda.sh && conda activate human && cd /app/HeyGem-Linux-Python-Hack && nohup python app.py > /tmp/gradio.log 2>&1 &"
```

然后访问: **http://your-server-ip:7860**

#### 选项2: 使用 SSH 隧道

如果容器在远程服务器上：

```bash
# 在本地机器上执行
ssh -L 7860:localhost:7860 user@server_ip

# 然后在本地浏览器访问
http://localhost:7860
```

#### 选项3: 使用 Docker 端口转发

```bash
# 使用 socat 或其他工具进行端口转发
socat TCP-LISTEN:7860,fork TCP:$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' starpainting-digital-human-service-1):7860
```

#### 选项4: 在容器内测试

```bash
# 进入容器
docker exec -it starpainting-digital-human-service-1 bash

# 在容器内使用 curl 测试
curl http://localhost:7860

# 或者使用文本浏览器（如果安装）
lynx http://localhost:7860
```

## 📋 使用指南

### 1. 访问 Web 界面

打开浏览器，访问 Gradio 应用地址（根据您选择的访问方式）。

### 2. 上传文件

- **音频文件**: 
  - 格式: MP3, WAV
  - 示例: `/app/HeyGem-Linux-Python-Hack/example/audio.wav`
  
- **视频文件**: 
  - 格式: MP4
  - 示例: `/app/HeyGem-Linux-Python-Hack/example/video.mp4`

### 3. 生成视频

1. 点击"上传音频文件"按钮，选择音频文件
2. 点击"上传视频文件"按钮，选择视频模板文件
3. 点击"Submit"按钮
4. 等待处理完成（可能需要几分钟）
5. 下载生成的数字人视频

## 📁 示例文件

应用目录中提供了示例文件：

```bash
# 示例音频
/app/HeyGem-Linux-Python-Hack/example/audio.wav (188K)

# 示例视频
/app/HeyGem-Linux-Python-Hack/example/video.mp4 (6.8M)
```

## 🔧 应用管理

### 查看日志

```bash
# 查看应用日志
docker exec starpainting-digital-human-service-1 tail -f /tmp/gradio.log

# 查看 SDK 日志
docker exec starpainting-digital-human-service-1 tail -f /app/HeyGem-Linux-Python-Hack/log/dh.log
```

### 重启应用

```bash
# 停止应用
docker exec starpainting-digital-human-service-1 pkill -f "python app.py"

# 重新启动
docker exec starpainting-digital-human-service-1 bash -c "source /opt/conda/etc/profile.d/conda.sh && conda activate human && cd /app/HeyGem-Linux-Python-Hack && nohup python app.py > /tmp/gradio.log 2>&1 &"
```

### 检查应用状态

```bash
# 检查进程
docker exec starpainting-digital-human-service-1 ps aux | grep "python app.py"

# 检查端口
docker exec starpainting-digital-human-service-1 netstat -tlnp | grep 7860

# 测试连接
docker exec starpainting-digital-human-service-1 curl -s -o /dev/null -w "%{http_code}" http://localhost:7860
```

## ⚠️ 注意事项

1. **端口映射**: 容器默认只映射了 8308 端口，需要手动添加 7860 端口映射才能外部访问

2. **资源要求**: 
   - 需要 GPU 支持
   - 需要足够的内存（建议 8GB+）
   - 视频生成过程可能需要较长时间

3. **初始化时间**: 
   - 应用启动时需要初始化 TransDhTask（加载模型）
   - 初始化可能需要几分钟时间
   - 请等待初始化完成后再使用

4. **并发限制**: 
   - 当前配置可能不支持多用户同时使用
   - 建议一次处理一个任务

## 🐛 故障排除

### 问题1: 无法访问 Web 界面

**检查步骤**:
1. 确认容器运行: `docker ps | grep digital-human`
2. 确认应用进程: `docker exec starpainting-digital-human-service-1 ps aux | grep app.py`
3. 确认端口监听: `docker exec starpainting-digital-human-service-1 netstat -tlnp | grep 7860`
4. 检查端口映射: `docker port starpainting-digital-human-service-1`

**解决方案**:
- 添加端口映射（见访问方式选项1）
- 使用 SSH 隧道（见访问方式选项2）
- 检查防火墙设置

### 问题2: 应用启动失败

**检查步骤**:
1. 查看日志: `docker exec starpainting-digital-human-service-1 tail -50 /tmp/gradio.log`
2. 检查依赖: `docker exec starpainting-digital-human-service-1 bash -c "source /opt/conda/etc/profile.d/conda.sh && conda activate human && python -c 'import gradio'"`

**解决方案**:
- 安装缺失的依赖
- 检查环境变量设置
- 验证 SDK 模块是否正常

### 问题3: 视频生成失败

**检查步骤**:
1. 查看应用日志: `docker exec starpainting-digital-human-service-1 tail -f /tmp/gradio.log`
2. 查看 SDK 日志: `docker exec starpainting-digital-human-service-1 tail -f /app/HeyGem-Linux-Python-Hack/log/dh.log`
3. 检查 GPU 状态: `nvidia-smi`

**解决方案**:
- 验证音频和视频文件格式
- 检查 GPU 资源是否充足
- 查看详细错误信息

## 📊 性能优化建议

1. **GPU 内存**: 确保 GPU 有足够的内存
2. **并发处理**: 当前配置建议一次处理一个任务
3. **文件大小**: 建议音频文件 < 10MB，视频文件 < 100MB
4. **超时设置**: 视频生成可能需要较长时间，请耐心等待

## 📅 报告信息

- **报告生成时间**: 2025-11-09 10:30:00
- **应用启动时间**: 2025-11-09 10:22:00
- **运行时长**: 约 8 分钟
- **状态**: ✅ 正常运行

## 🎯 下一步操作

1. **添加端口映射**（如需要外部访问）
2. **测试应用功能**（使用示例文件）
3. **监控应用性能**（查看日志和资源使用）
4. **优化配置**（根据需要调整参数）

---

**应用已成功运行，可以开始测试数字人视频生成功能！** 🎉

