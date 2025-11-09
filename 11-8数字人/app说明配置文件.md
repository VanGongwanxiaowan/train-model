# app.py 文件说明

## 📋 文件概述

`app.py` 是 **HeyGem SDK 的 Gradio 演示应用**，提供一个基于 Web 的图形界面，用于测试和演示数字人视频生成功能。

## 🎯 主要功能

### 1. **Gradio Web 界面**
   - 提供一个简单的 Web UI，用户可以通过浏览器上传音频和视频文件
   - 界面支持中文和英文
   - 标题：`数字人视频生成/Digital Human Video Generation`

### 2. **数字人视频生成**
   - 接收用户上传的音频文件（MP3/WAV 等）
   - 接收用户上传的视频模板文件（MP4 等）
   - 调用 HeyGem SDK 生成数字人说话视频
   - 返回生成的视频文件

### 3. **核心组件**

#### `write_video_gradio()` 函数 (第42-171行)
- **作用**：视频帧队列消费者，处理 SDK 生成 video frames
- **功能**：
  - 从 `output_imgs_queue` 队列中获取视频帧
  - 使用 `cv2.VideoWriter` 将帧写入临时 MP4 文件
  - 使用 FFmpeg 将音频和视频合成最终文件
  - 支持水印和数字人标识的添加
- **关键特性**：
  - 30秒超时机制（`queue.get(timeout=30)`）
  - 错误处理和日志记录
  - 支持横向和纵向视频

#### `VideoProcessor` 类 (第177-253行)
- **作用**：封装数字人视频处理逻辑
- **方法**：
  - `__init__()`: 初始化 `TransDhTask` 实例
  - `_initialize_service()`: 初始化服务（等待5秒）
  - `process_video()`: 处理视频生成请求
    - 读取视频参数（宽度、高度、帧率）
    - 调用 `task.work()` 生成数字人视频
    - 等待任务完成（最多30分钟）
    - 返回生成的视频路径

### 4. **与 SDK 的集成**

```python
# 第174行：替换 SDK 的默认 write_video 函数
service.trans_dh_service.write_video = write_video_gradio
```

- 这个替换操作让 SDK 使用自定义的 `write_video_gradio` 函数来处理视频输出
- 这是 SDK 提供的扩展点，允许自定义视频处理逻辑

## 🔄 与 fastapi_app.py 的关系

### 相同点
- 都使用 `service.trans_dh_service.TransDhTask()` 来生成数字人视频
- 都实现 `write_video` 函数来处理视频帧队列
- 都使用相同的 SDK 核心功能

### 不同点

| 特性 | app.py (Gradio) | fastapi_app.py (FastAPI) |
|------|----------------|------------------------|
| **界面** | Web UI (Gradio) | REST API |
| **用途** | 演示和测试 | 生产环境服务 |
| **调用方式** | 浏览器上传文件 | HTTP POST 请求 |
| **部署** | 独立运行 | Docker 容器化 |
| **端口** | Gradio 默认端口 | 8308 |
| **队列超时** | 30秒 | 5秒（已优化） |
| **错误处理** | 简单 | 完善的错误处理、重试、监控 |

## 🚀 运行方式

### 直接运行
```bash
python app.py
```

### 启动后的行为
1. 创建 `VideoProcessor` 实例
2. 初始化 `TransDhTask`（加载模型）
3. 启动 Gradio Web 服务器
4. 在浏览器中访问显示的 URL（通常是 `http://0.0.0.0:7860`）

## 📝 使用场景

1. **SDK 功能测试**：快速测试 SDK 是否正常工作
2. **演示和展示**：向客户或团队成员展示数字人功能
3. **开发调试**：在开发过程中测试不同的音频和视频组合
4. **功能验证**：验证 SDK 的新功能或修复

## ⚠️ 注意事项

1. **不是生产服务**：
   - `app.py` 是演示应用，不适合生产环境
   - 生产环境应该使用 `fastapi_app.py`

2. **初始化等待时间**：
   - 第188行：`time.sleep(5)` - 初始化等待5秒
   - 这个等待时间可能不足以完全初始化 SDK 的多进程队列

3. **队列超时**：
   - 第70行：`queue.get(timeout=30)` - 30秒超时
   - 如果 SDK 内部进程启动慢，可能会超时

4. **多进程启动方法**：
   - 第5行：多进程启动方法已被注释，使用系统默认（通常是 `fork`）

## 🔧 与当前项目的关联

在当前项目中：
- **生产环境**：使用 `fastapi_app.py`（端口 8308）
- **SDK 源码**：`HeyGem-Linux-Python-Hack/` 目录
- **app.py**：主要用于 SDK 的测试和演示，**不直接用于生产环境**

## 📊 代码流程

```
用户上传文件
    ↓
Gradio Interface 接收
    ↓
VideoProcessor.process_video()
    ↓
初始化任务 (task_dic[code] = "")
    ↓
调用 task.work() (SDK 核心)
    ↓
SDK 内部多进程处理
    ↓
write_video_gradio() 从队列获取帧
    ↓
写入视频文件
    ↓
FFmpeg 合成音频+视频
    ↓
返回最终视频
```

## 🎓 总结

`app.py` 是 HeyGem SDK 的 **演示和测试工具**，提供了一个简单的 Web 界面来测试数字人视频生成功能。它不是生产服务，但可以作为理解 SDK 工作原理的参考实现。


