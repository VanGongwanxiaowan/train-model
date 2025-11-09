# 运行 run.py 说明

## 📋 概述

`run.py` 是数字人视频生成服务的命令行工具，用于直接运行视频生成任务。

## 🚀 运行方式

### 方法1: 在容器内直接运行（推荐）

```bash
# 进入容器
docker exec -it starpainting-digital-human-service-1 bash

# 激活conda环境
source /opt/conda/etc/profile.d/conda.sh
conda activate human

# 设置环境变量
export PYTHONPATH=/app/HeyGem-Linux-Python-Hack
export LD_LIBRARY_PATH=/opt/conda/envs/human/lib:$LD_LIBRARY_PATH

# 切换到工作目录
cd /app/HeyGem-Linux-Python-Hack

# 运行任务（使用默认示例文件）
python run.py

# 或指定音频和视频文件
python run.py --audio_path example/audio.wav --video_path example/video.mp4
```

### 方法2: 在后台运行

```bash
# 在后台运行，输出到日志文件
docker exec -d starpainting-digital-human-service-1 bash -c "source /opt/conda/etc/profile.d/conda.sh && conda activate human && cd /app/HeyGem-Linux-Python-Hack && export PYTHONPATH=/app/HeyGem-Linux-Python-Hack && export LD_LIBRARY_PATH=/opt/conda/envs/human/lib:\$LD_LIBRARY_PATH && nohup python run.py > /tmp/run_py.log 2>&1 &"

# 查看日志
docker exec starpainting-digital-human-service-1 tail -f /tmp/run_py.log

# 查看服务日志
docker exec starpainting-digital-human-service-1 tail -f /app/HeyGem-Linux-Python-Hack/log/dh.log
```

### 方法3: 使用启动脚本

```bash
# 使用创建的启动脚本
docker exec starpainting-digital-human-service-1 bash /app/run_digital_human.sh

# 或在后台运行
docker exec -d starpainting-digital-human-service-1 bash -c "cd /app && nohup bash run_digital_human.sh > /tmp/run_digital_human.log 2>&1 &"

# 查看日志
docker exec starpainting-digital-human-service-1 tail -f /tmp/run_digital_human.log
```

## 📝 参数说明

- `--audio_path`: 音频文件路径（默认: `example/audio.wav`）
- `--video_path`: 视频文件路径（默认: `example/video.mp4`）

## ⏱️ 执行流程

1. **初始化阶段**（约15秒）
   - 创建 TransDhTask 实例
   - 等待15秒确保模型加载完成（根据新的sleep时间设置）

2. **任务执行阶段**（时间取决于视频长度）
   - 任务ID: `1004`
   - 调用 `task.work()` 方法生成视频
   - 处理音频和视频文件
   - 生成数字人视频

3. **结果输出**
   - 视频保存到: `result/1004-r.mp4`
   - 任务完成后退出（exit(0)）

## 📊 监控任务

### 查看进程状态

```bash
# 检查run.py进程
docker exec starpainting-digital-human-service-1 ps aux | grep "run.py" | grep -v grep

# 检查所有Python进程
docker exec starpainting-digital-human-service-1 ps aux | grep python
```

### 查看日志

```bash
# 查看服务日志（实时）
docker exec starpainting-digital-human-service-1 tail -f /app/HeyGem-Linux-Python-Hack/log/dh.log

# 查看任务日志（如果使用后台运行）
docker exec starpainting-digital-human-service-1 tail -f /tmp/run_py.log

# 查看最近的日志
docker exec starpainting-digital-human-service-1 tail -50 /app/HeyGem-Linux-Python-Hack/log/dh.log | grep "1004"
```

### 查看结果文件

```bash
# 查看结果目录
docker exec starpainting-digital-human-service-1 ls -lh /app/HeyGem-Linux-Python-Hack/result/

# 查看任务1004的结果
docker exec starpainting-digital-human-service-1 ls -lh /app/HeyGem-Linux-Python-Hack/result/1004-r.mp4
```

## ⚠️ 注意事项

1. **任务ID固定**: run.py 使用固定的任务ID `1004`，如果同时运行多个任务可能会有冲突

2. **执行时间**: 任务执行时间取决于视频长度，可能需要几分钟到几十分钟

3. **资源占用**: 任务会占用GPU资源，确保GPU可用

4. **文件路径**: 确保音频和视频文件存在且路径正确

5. **工作目录**: 必须切换到 `/app/HeyGem-Linux-Python-Hack` 目录运行

6. **环境变量**: 必须设置 `PYTHONPATH` 和 `LD_LIBRARY_PATH`

## 🔍 故障排查

### 问题1: 找不到模块

```bash
# 确保设置了PYTHONPATH
export PYTHONPATH=/app/HeyGem-Linux-Python-Hack
```

### 问题2: CUDA错误

```bash
# 确保设置了LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/opt/conda/envs/human/lib:$LD_LIBRARY_PATH
```

### 问题3: 文件不存在

```bash
# 检查示例文件是否存在
docker exec starpainting-digital-human-service-1 ls -lh /app/HeyGem-Linux-Python-Hack/example/
```

### 问题4: 任务失败

```bash
# 查看详细错误日志
docker exec starpainting-digital-human-service-1 tail -100 /app/HeyGem-Linux-Python-Hack/log/dh.log | grep -A 10 "1004"
```

## 📅 更新日期

2025-01-27

