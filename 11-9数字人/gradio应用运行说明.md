# Gradio 应用运行说明

## ✅ 应用状态

**应用已成功启动！**

- **进程状态**: 运行中（PID: 527）
- **服务端口**: 7860（容器内）
- **访问地址**: http://localhost:7860（容器内）
- **Gradio 版本**: 4.44.1

## 🌐 访问方式

### 方式1: 通过 Docker 端口映射（推荐）

由于容器目前只映射了 8308 端口，需要添加 7860 端口的映射。

#### 选项A: 重启容器并添加端口映射

```bash
# 停止当前容器
docker stop starpainting-digital-human-service-1

# 启动容器时添加端口映射
docker run -d \
  --name starpainting-digital-human-service-1 \
  --gpus all \
  -p 8308:8308 \
  -p 7860:7860 \
  -v /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service:/app \
  heygem:v2.2
```

#### 选项B: 使用 SSH 隧道（如果容器在远程服务器上）

```bash
# 在本地机器上执行
ssh -L 7860:localhost:7860 user@server_ip
```

然后在本地浏览器访问: http://localhost:7860

### 方式2: 通过容器内访问

如果需要直接在容器内测试：

```bash
# 进入容器
docker exec -it starpainting-digital-human-service-1 bash

# 在容器内访问
curl http://localhost:7860
```

### 方式3: 使用 socat 进行端口转发

```bash
# 安装 socat（如果未安装）
sudo apt-get install socat

# 创建端口转发
socat TCP-LISTEN:7860,fork TCP:localhost:7860 &
```

## 📋 使用步骤

1. **访问 Web 界面**
   - 打开浏览器
   - 访问: http://your-server-ip:7860

2. **上传文件**
   - **音频文件**: 上传 MP3 或 WAV 格式的音频文件
   - **视频文件**: 上传 MP4 格式的视频模板文件

3. **生成视频**
   - 点击"Submit"按钮
   - 等待处理完成（可能需要几分钟）
   - 下载生成的数字人视频

## 📁 示例文件

应用目录中有示例文件可供测试：

```bash
# 示例音频文件
/app/HeyGem-Linux-Python-Hack/example/audio.wav

# 示例视频文件
/app/HeyGem-Linux-Python-Hack/example/video.mp4
```

## 🔧 应用配置

### 环境变量

应用会自动设置以下环境变量：

- `PYTHONPATH`: `/app/HeyGem-Linux-Python-Hack`
- `LD_LIBRARY_PATH`: `/opt/conda/envs/human/lib`
- `GRADIO_SERVER_NAME`: `0.0.0.0`（允许外部访问）

### 工作目录

- 应用工作目录: `/app/HeyGem-Linux-Python-Hack`
- 结果输出目录: `./result/`
- 临时文件目录: `./temp/`

## 📊 应用功能

1. **数字人视频生成**
   - 接收音频和视频文件
   - 使用 HeyGem SDK 生成数字人说话视频
   - 支持自定义水印和数字人标识

2. **Web 界面**
   - 简单的文件上传界面
   - 实时处理状态显示
   - 视频预览和下载

## ⚠️ 注意事项

1. **服务初始化**
   - 应用启动时需要初始化 TransDhTask（加载模型）
   - 初始化时间可能需要几分钟
   - 请等待初始化完成后再使用

2. **资源要求**
   - 需要 GPU 支持
   - 需要足够的内存和磁盘空间
   - 视频生成过程可能需要较长时间

3. **端口冲突**
   - 确保 7860 端口未被其他服务占用
   - 如果端口被占用，可以修改 app.py 中的端口配置

## 🐛 故障排除

### 问题1: 无法访问 Web 界面

**解决方案**:
- 检查容器是否运行: `docker ps | grep digital-human`
- 检查端口是否映射: `docker port starpainting-digital-human-service-1`
- 检查防火墙设置

### 问题2: 模块导入错误

**解决方案**:
- 确保 PYTHONPATH 已正确设置
- 检查工作目录是否正确
- 验证 SDK 模块是否存在

### 问题3: 视频生成失败

**解决方案**:
- 检查日志文件: `log/dh.log`
- 验证音频和视频文件格式
- 检查 GPU 资源是否充足

## 📝 日志查看

```bash
# 查看应用日志
docker exec starpainting-digital-human-service-1 tail -f /app/HeyGem-Linux-Python-Hack/log/dh.log

# 查看容器日志
docker logs -f starpainting-digital-human-service-1
```

## 🔄 重启应用

如果需要重启应用：

```bash
# 停止应用
docker exec starpainting-digital-human-service-1 pkill -f "python app.py"

# 重新启动
docker exec starpainting-digital-human-service-1 bash -c "source /opt/conda/etc/profile.d/conda.sh && conda activate human && cd /app/HeyGem-Linux-Python-Hack && nohup python app.py > /tmp/gradio.log 2>&1 &"
```

## 📅 更新时间

- **创建时间**: 2025-11-09
- **最后更新**: 2025-11-09 10:25:00

