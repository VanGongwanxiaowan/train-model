# 数字人生成服务 Docker 部署说明

## 容器信息

- **容器ID**: 
- **镜像**: heygem:v2.2 (商业SDK镜像)
- **容器名**: starpainting-digital-human-service-1
- **端口映射**: 0.0.0.0:8308 -> 容器内 8308
- **状态**: 运行中

## 源代码位置

物理机源代码：
```
/data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service
├─ fastapi_app.py              ← 主程序（26KB）
├─ start.sh                     ← 启动脚本
├─ HeyGem-Linux-Python-Hack/    ← SDK库（闭源）
│  ├─ service/
│  │  └─ trans_dh_service.py    ← 数字人生成核心
│  └─ ...
├─ requirements.txt             ← 依赖
└─ config/                       ← 配置文件
```

## Docker 启动命令

### 基本启动命令

```bash
docker run -d \
  --name starpainting-digital-human-service-1 \
  -p 8308:8308 \
  --gpus all \
  -v /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service:/app \
  -v /data2/home_back/gujiaxin/work/batchshort1/assrt:/data2/home_back/gujiaxin/work/batchshort1/assrt \
  heygem:v2.2 \
  bash start.sh
```

### 卷挂载说明

1. **源代码挂载**:
   ```bash
   -v /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service:/app
   ```
   - 将物理机的源代码目录挂载到容器内的 `/app` 目录
   - 这样容器内可以访问 `fastapi_app.py` 和 `HeyGem-Linux-Python-Hack`

2. **数据目录挂载**:
   ```bash
   -v /data2/home_back/gujiaxin/work/batchshort1/assrt:/data2/home_back/gujiaxin/work/batchshort1/assrt
   ```
   - 挂载数据目录，确保容器内可以访问 `BASE_PATH`
   - `BASE_PATH = "/data2/home_back/gujiaxin/work/batchshort1/assrt/human"`

### 完整启动命令（推荐，包含共享内存配置）

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

### 使用启动脚本（推荐，更便捷）

```bash
# 使用包含共享内存配置的启动脚本
cd /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service
bash start_with_shm.sh
```

**关键参数说明**:
- `--shm-size=2g`: 设置共享内存为 2GB
  - 对于多进程 DataLoader（num_workers > 0），推荐 2GB
  - 对于单进程模式（num_workers=0），64MB 足够
  - 可以根据需要调整: `2g`, `4g`, `8g`

## 系统架构

### 1. 镜像层
- **heygem:v2.2**: 预装所有依赖和模型
  - Conda 环境: `human`
  - Python 3.8
  - CUDA/GPU 支持
  - HeyGem SDK 依赖

### 2. 应用层
- **FastAPI** (`fastapi_app.py`): HTTP REST API 服务
  - 端口: 8308
  - 端点: `/human/generate`
  - 健康检查: `/health`

### 3. SDK层
- **HeyGem TransDhTask**: GPU 推理引擎
  - 位置: `HeyGem-Linux-Python-Hack/service/trans_dh_service.py`
  - 功能: 数字人生成核心逻辑

### 4. 后处理
- **FFmpeg**: 音视频合成
  - 音频 + 视频帧合成
  - 可选水印添加
  - 数字人标识添加

### 5. 通信
- **HTTP REST API**: 8308 端口
  - 请求格式: JSON
  - 响应格式: JSON

## 请求处理流程

```
客户端 pipe_line.py
    │
    ├─ human_pack_new()
    │  └─ requests.post(
    │      url="hte",
    │      json={
    │          "audio_path": "/data2/xxx.mp3",
    │          "video_path": "/data2/xxxx/template.mp4",
    │          "save_path": "/data2/xxxx/output.mp4"
    │      }
    │  )
    │
    └─→ Docker容器 (8308) 接收请求
       └─ fastapi_app.py
          └─ @app.post("/human/generate")
             └─ async def generate_human_image(request)
                └─ task_instance.work(
                      audio_path,
                      video_path,
                      task_id,
                      watermark=0,
                      digital_auth=0
                   )
                   └─ HeyGem SDK (GPU推理)
                      └─ 返回生成的视频路径
       └─ FFmpeg 合成 (音频 + 视频帧 + 可选水印)
       └─ 返回 JSON 响应
          {
              "code": 200,
              "message": "success",
              "data": {
                  "save_path": "/data2/xxxx/output.mp4",
                  "task_id": "uuid"
              }
          }
```

## 配置说明

### BASE_PATH 配置

在 `fastapi_app.py` 中配置：
```python
BASE_PATH = "/data2/home_back/gujiaxin/work/batchshort1/assrt/human"
```

**重要**: 确保 Docker 启动时挂载了相应的卷，否则容器内无法访问此路径。

### 服务启动流程

1. **启动时** (`startup_event`):
   - 创建 `BASE_PATH` 目录（如果不存在）
   - 初始化 `TransDhTask` 实例（加载模型）
   - 等待模型加载完成

2. **请求处理**:
   - 验证参数（audio_path, video_path, save_path）
   - 检查文件是否存在
   - 调用 HeyGem SDK 生成数字人视频
   - 返回结果路径

## API 端点

### POST /human/generate

生成数字人视频

**请求体**:
```json
{
    "audio_path": "/data2/xxx.mp3",
    "video_path": "/data2/xxxx/template.mp4",
    "save_path": "/data2/xxxx/output.mp4"
}
```

**响应**:
```json
{
    "code": 200,
    "message": "success",
    "data": {
        "save_path": "/data2/xxxx/output.mp4",
        "task_id": "uuid"
    }
}
```

### GET /health

健康检查

**响应**:
```json
{
    "status": "ok",
    "service": "digital-human-service"
}
```

## 日志查看

```bash
# 查看容器日志
docker logs -f starpainting-digital-human-service-1

# 查看服务日志（如果配置了日志文件）
docker exec starpainting-digital-human-service-1 tail -f /app/HeyGem-Linux-Python-Hack/log/dh.log
```

## 故障排查

### 1. 容器无法启动
- 检查 GPU 是否可用: `nvidia-smi`
- 检查端口是否被占用: `netstat -tuln | grep 8308`
- 检查卷挂载是否正确

### 2. 模型加载失败
- 检查容器内模型文件是否存在
- 检查 GPU 内存是否充足
- 查看容器日志获取详细错误信息

### 3. 文件路径错误
- 确保 Docker 卷挂载正确
- 检查 `BASE_PATH` 在容器内是否可访问
- 验证请求中的文件路径是否存在

### 4. 多进程问题
- 已修复: `run.py` 和 `app.py` 开头已设置 `multiprocessing.set_start_method('spawn', force=True)`
- 确保在导入 `service.trans_dh_service` 之前设置

### 5. 共享内存不足
- **问题**: 队列阻塞、进程间通信失败
- **原因**: 默认共享内存只有 64MB，对于多进程 DataLoader 不够
- **解决**: 使用 `--shm-size=2g` 参数增加共享内存到 2GB
  ```bash
  # 重新启动容器时添加共享内存参数
  docker run -d ... --shm-size=2g ...
  # 或使用启动脚本
  bash start_with_shm.sh
  ```
- **验证**: 
  ```bash
  docker exec starpainting-digital-human-service-1 df -h /dev/shm
  # 预期输出: shm  2.0G  0  2.0G  0% /dev/shm
  ```

## 注意事项

1. **GPU 支持**: 需要 `--gpus all` 参数
2. **卷挂载**: 必须挂载源代码目录和数据目录
3. **路径一致性**: 确保容器内外路径一致
4. **模型加载**: 首次启动需要加载模型，可能需要几分钟
5. **多进程**: 已配置使用 `spawn` 方式启动子进程

