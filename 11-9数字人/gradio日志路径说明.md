# Gradio日志内容说明

## 📋 概述

Gradio日志文件 (`/tmp/gradio.log`) 记录了Gradio Web应用运行过程中的所有信息，包括服务启动、任务处理、错误信息等。

## 📁 日志文件位置

- **容器内路径**: `/tmp/gradio.log`
- **查看方式**: `docker exec starpainting-digital-human-service-1 tail -f /tmp/gradio.log`

## 📝 日志内容分类

### 1. 服务启动日志

#### BASE_PATH配置日志
```
[2025-XX-XX XX:XX:XX] [app.py[line:302]] [INFO] [创建 BASE_PATH 目录: /data2/home_back/gujiaxin/work/batchshort1/assrt/human]
```
或
```
[2025-XX-XX XX:XX:XX] [app.py[line:304]] [INFO] [BASE_PATH 目录已存在: /data2/home_back/gujiaxin/work/batchshort1/assrt/human]
```

#### 服务初始化日志
```
[2025-XX-XX XX:XX:XX] [app.py[line:160]] [INFO] [开始初始化 trans_dh_service...]
[2025-XX-XX XX:XX:XX] [app.py[line:163]] [INFO] [trans_dh_service 初始化完成。]
VideoProcessor init done
```

#### Gradio服务启动日志
```
Running on local URL:  http://0.0.0.0:7860

To create a public link, set `share=True` in `launch()`.
```

### 2. 任务处理日志

#### 任务开始日志
```
[2025-XX-XX XX:XX:XX] [app.py[line:199]] [INFO] [[{task_id}] 开始处理任务，音频: {audio_path}, 视频: {video_path}]
```

#### 工作目录切换日志
```
[2025-XX-XX XX:XX:XX] [app.py[line:186]] [INFO] [切换到工作目录: /app/HeyGem-Linux-Python-Hack]
```

#### 任务执行日志
```
[2025-XX-XX XX:XX:XX] [app.py[line:221]] [INFO] [[{task_id}] task_dic结果类型: {type}, 内容: {content}]
```

#### 结果目录日志
```
[2025-XX-XX XX:XX:XX] [app.py[line:244]] [INFO] [[{task_id}] 结果目录: /data2/home_back/gujiaxin/work/batchshort1/assrt/human/result/{task_id}]
```

#### 文件移动日志
```
[2025-XX-XX XX:XX:XX] [app.py[line:244]] [INFO] [[{task_id}] 文件已移动到: {dest_path}]
```

#### 临时文件清理日志
```
[2025-XX-XX XX:XX:XX] [app.py[line:262]] [INFO] [[{task_id}] 已删除临时文件: {temp_file}]
```

#### 任务完成日志
```
[2025-XX-XX XX:XX:XX] [app.py[line:269]] [INFO] [[{task_id}] 任务完成，最终结果路径: {result_path}]
```

### 3. 错误日志

#### 初始化错误
```
[2025-XX-XX XX:XX:XX] [app.py[line:166]] [ERROR] [初始化 trans_dh_service 失败: {error_message}]
```

#### BASE_PATH创建失败
```
[2025-XX-XX XX:XX:XX] [app.py[line:306]] [WARNING] [创建 BASE_PATH 目录失败: {error}，继续启动服务]
```

#### 任务执行错误
```
[2025-XX-XX XX:XX:XX] [app.py[line:207]] [ERROR] [[{task_id}] 任务执行失败: {error_message}]
[2025-XX-XX XX:XX:XX] [app.py[line:208]] [ERROR] [[{task_id}] 详细错误信息:\n{error_traceback}]
```

#### 文件操作错误
```
[2025-XX-XX XX:XX:XX] [app.py[line:232]] [ERROR] [[{task_id}] 结果文件不存在: {result_path}]
[2025-XX-XX XX:XX:XX] [app.py[line:249]] [ERROR] [[{task_id}] 移动文件失败: {error}]
```

#### 任务结果格式错误
```
[2025-XX-XX XX:XX:XX] [app.py[line:226]] [ERROR] [[{task_id}] 任务结果格式错误: 期望list/tuple，实际: {type}, 内容: {content}]
```

### 4. 视频处理日志

#### VideoWriter初始化
```
Custom VideoWriter init done
```

#### 视频帧处理日志
```
[2025-XX-XX XX:XX:XX] [process.py[line:108]] [INFO] [Custom VideoWriter [{task_id}]视频帧队列处理已结束]
[2025-XX-XX XX:XX:XX] [process.py[line:108]] [INFO] [Custom VideoWriter Silence Video saved in {output_path}]
```

#### 视频写入完成
```
###### Custom Video Writer write over
###### Video result saved in {result_path}
```

#### 视频处理错误
```
[2025-XX-XX XX:XX:XX] [process.py[line:108]] [ERROR] [Custom VideoWriter [{task_id}]任务视频帧队列 -> 异常原因:[{reason}]]
[2025-XX-XX XX:XX:XX] [process.py[line:108]] [ERROR] [Custom VideoWriter [{task_id}]视频帧队列处理异常结束，异常原因:[{error}]]
```

### 5. 系统日志

#### 库加载日志
```
2025-XX-XX XX:XX:XX - cv2box - INFO - Use default multi mode: multi-thread, or you can set env 'CV_MULTI_MODE' to multi-process/torch-process
```

#### ONNX Runtime日志
```
2025-XX-XX XX:XX:XX [E:onnxruntime:Default, provider_bridge_ort.cc:1480 TryGetProviderInfo_CUDA] Failed to load library libonnxruntime_providers_cuda.so
2025-XX-XX XX:XX:XX [W:onnxruntime:Default, onnxruntime_pybind_state.cc:743 CreateExecutionProviderInstance] Failed to create CUDAExecutionProvider
```

#### TransDhTask初始化日志
```
[2025-XX-XX XX:XX:XX] [process.py[line:108]] [INFO] [>>> init_wh_process进程启动]
[2025-XX-XX XX:XX:XX] [app.py[line:153]] [INFO] [TransDhTask init]
```

### 6. HTTP请求日志

#### Gradio内部请求
```
[2025-XX-XX XX:XX:XX] [_client.py[line:1025]] [INFO] [HTTP Request: GET http://localhost:7860/startup-events "HTTP/1.1 200 OK"]
[2025-XX-XX XX:XX:XX] [_client.py[line:1025]] [INFO] [HTTP Request: HEAD http://localhost:7860/ "HTTP/1.1 200 OK"]
```

#### 外部API请求
```
[2025-XX-XX XX:XX:XX] [_client.py[line:1025]] [INFO] [HTTP Request: GET https://checkip.amazonaws.com/ "HTTP/1.1 200 "]
[2025-XX-XX XX:XX:XX] [_client.py[line:1025]] [INFO] [HTTP Request: GET https://api.gradio.app/pkg-version "HTTP/1.1 200 OK"]
```

## 📊 日志格式说明

### 标准日志格式
```
[时间戳] [文件名[行号]] [日志级别] [日志内容]
```

### 示例
```
[2025-11-09 12:45:17] [app.py[line:163]] [INFO] [trans_dh_service 初始化完成。]
```

### 日志级别
- **INFO**: 信息日志（正常操作）
- **WARNING**: 警告日志（可能的问题）
- **ERROR**: 错误日志（错误信息）
- **DEBUG**: 调试日志（详细调试信息）

## 🔍 日志内容详解

### 1. 服务启动流程日志

```
1. BASE_PATH检查/创建
2. TransDhTask初始化
3. VideoProcessor初始化
4. Gradio界面启动
5. HTTP服务启动
```

### 2. 任务处理流程日志

```
1. 任务开始（接收音频和视频文件）
2. 工作目录切换
3. 文件信息获取（宽度、高度、FPS）
4. 任务执行（调用task.work()）
5. 任务结果获取
6. 结果文件处理
7. 文件移动到结果目录
8. 临时文件清理
9. 任务完成
```

### 3. 错误处理日志

```
1. 错误发生
2. 错误信息记录
3. 错误堆栈记录
4. 错误处理（抛出或记录）
```

## 📋 日志内容示例

### 完整的服务启动日志示例

```
2025-11-09 12:45:16,552 - cv2box - INFO - Use default multi mode: multi-thread
[2025-11-09 12:45:17] [app.py[line:304]] [INFO] [BASE_PATH 目录已存在: /data2/home_back/gujiaxin/work/batchshort1/assrt/human]
[2025-11-09 12:45:17] [app.py[line:160]] [INFO] [开始初始化 trans_dh_service...]
[2025-11-09 12:45:35] [process.py[line:108]] [INFO] [>>> init_wh_process进程启动]
[2025-11-09 12:45:40] [app.py[line:163]] [INFO] [trans_dh_service 初始化完成。]
VideoProcessor init done
Running on local URL:  http://0.0.0.0:7860
```

### 完整的任务处理日志示例

```
[2025-11-09 12:46:00] [app.py[line:186]] [INFO] [切换到工作目录: /app/HeyGem-Linux-Python-Hack]
[2025-11-09 12:46:00] [app.py[line:199]] [INFO] [[abc123] 开始处理任务，音频: /tmp/audio.wav, 视频: /tmp/video.mp4]
[2025-11-09 12:46:05] [app.py[line:221]] [INFO] [[abc123] task_dic结果类型: <class 'tuple'>, 内容: (Status.success, 100, '结果路径')]
[2025-11-09 12:46:05] [app.py[line:244]] [INFO] [[abc123] 结果目录: /data2/home_back/gujiaxin/work/batchshort1/assrt/human/result/abc123]
[2025-11-09 12:46:05] [app.py[line:244]] [INFO] [[abc123] 文件已移动到: /data2/home_back/gujiaxin/work/batchshort1/assrt/human/result/abc123/abc123-r.mp4]
[2025-11-09 12:46:05] [app.py[line:269]] [INFO] [[abc123] 任务完成，最终结果路径: /data2/home_back/gujiaxin/work/batchshort1/assrt/human/result/abc123/abc123-r.mp4]
```

## 🔍 查看特定日志内容

### 查看BASE_PATH相关日志
```bash
docker exec starpainting-digital-human-service-1 tail -100 /tmp/gradio.log | grep "BASE_PATH"
```

### 查看任务处理日志
```bash
docker exec starpainting-digital-human-service-1 tail -200 /tmp/gradio.log | grep "任务"
```

### 查看错误日志
```bash
docker exec starpainting-digital-human-service-1 tail -200 /tmp/gradio.log | grep "ERROR"
```

### 查看初始化日志
```bash
docker exec starpainting-digital-human-service-1 tail -100 /tmp/gradio.log | grep "初始化"
```

## 📊 日志文件大小

- **初始大小**: 通常几KB到几十KB
- **运行后**: 随着任务处理，日志文件会逐渐增大
- **建议**: 定期清理或轮转日志文件

## ⚠️ 注意事项

1. **日志文件位置**: `/tmp/gradio.log` 是容器内的临时文件，容器重启后可能会清空
2. **日志轮转**: 如果日志文件过大，建议定期清理或设置日志轮转
3. **日志级别**: 可以通过修改代码调整日志级别
4. **日志格式**: 日志格式由代码中的logger配置决定

## 📅 更新日期

2025-01-27

