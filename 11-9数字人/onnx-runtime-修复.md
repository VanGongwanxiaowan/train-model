# ONNX Runtime 修复成功报告

## 修复时间
2025-11-09

## 问题描述
初始化报错，ONNX Runtime 无法使用 GPU，错误信息：
```
libcudnn.so.8: cannot open shared object file: No such file or directory
Failed to create CUDAExecutionProvider
```

## 根本原因
1. **onnxruntime-gpu 版本不匹配**: 当前版本 1.11.0 与 CUDA 环境不兼容
2. **缺少 cuDNN 库**: onnxruntime-gpu 1.16.0 需要 cuDNN 8，但系统中缺少

## 解决方案

### 步骤 1: 安装 cudatoolkit 11.8.0 ✅
```bash
conda install -y cudatoolkit=11.8.0 -c conda-forge
```
**结果**: 安装成功

### 步骤 2: 安装 onnxruntime-gpu 1.16.0 ✅
使用多个国内镜像源，确保安装成功：
- 清华大学镜像: https://pypi.tuna.tsinghua.edu.cn/simple ✅ (成功)
- 阿里云镜像: https://mirrors.aliyun.com/pypi/simple (备用)
- 豆瓣镜像: https://pypi.douban.com/simple (备用)
- 腾讯云镜像: https://mirrors.cloud.tencent.com/pypi/simple (备用)
- 中科大镜像: https://pypi.mirrors.ustc.edu.cn/simple (备用)

**结果**: 使用清华大学镜像成功安装 onnxruntime-gpu 1.16.0

### 步骤 3: 安装 cuDNN 8.9.2 ✅
```bash
conda install -y cudnn=8.9.2 -c conda-forge
```
**结果**: 安装成功（469.4 MB）

### 步骤 4: 设置 LD_LIBRARY_PATH ✅
在 app.py 中添加 LD_LIBRARY_PATH 设置，确保 cuDNN 库能被找到：
```python
if "LD_LIBRARY_PATH" not in os.environ:
    os.environ["LD_LIBRARY_PATH"] = "/opt/conda/envs/human/lib"
else:
    os.environ["LD_LIBRARY_PATH"] = "/opt/conda/envs/human/lib:" + os.environ["LD_LIBRARY_PATH"]
```
**结果**: 已添加到 app.py

## 验证结果

### 检查脚本输出
```bash
python check_env/check_onnx_cuda.py
```

**输出**:
```
['CUDAExecutionProvider', 'CPUExecutionProvider']
ONNX Runtime is successfully using the GPU.
(1, 12800, 1)
```

### 版本验证
```python
import onnxruntime as ort
print(f'ONNX Runtime version: {ort.__version__}')
print(f'Available providers: {ort.get_available_providers()}')
print(f'CUDA available: {"CUDAExecutionProvider" in ort.get_available_providers()}')
```

**输出**:
```
ONNX Runtime version: 1.16.0
Available providers: ['TensorrtExecutionProvider', 'CUDAExecutionProvider', 'AzureExecutionProvider', 'CPUExecutionProvider']
CUDA available: True
```

## 最终版本

### 已验证的版本组合 ✅
- **cudatoolkit**: 11.8.0
- **onnxruntime-gpu**: 1.16.0
- **cudnn**: 8.9.2
- **PyTorch CUDA**: 11.3 (兼容)

### 安装的包
```
onnxruntime-gpu          1.16.0
cudatoolkit              11.8.0
cudnn                    8.9.2.26
```

## 修复状态

### ✅ 成功修复
1. ✅ onnxruntime-gpu 1.16.0 已安装
2. ✅ cudatoolkit 11.8.0 已安装
3. ✅ cuDNN 8.9.2 已安装
4. ✅ LD_LIBRARY_PATH 已设置
5. ✅ ONNX Runtime GPU 支持已启用
6. ✅ 检查脚本显示 "ONNX Runtime is successfully using the GPU."

### 文件更新
1. ✅ `requirements.txt` - 更新 onnxruntime-gpu 版本为 1.16.0
2. ✅ `app.py` - 添加 LD_LIBRARY_PATH 设置
3. ✅ `修复onnxruntime版本.sh` - 修复脚本（支持多镜像源）
4. ✅ `安装cudnn.sh` - cuDNN 安装脚本

## 关键修复点

### 1. 多镜像源支持
- 使用多个国内镜像源，避免单个镜像源下载慢的问题
- 清华大学镜像速度最快，成功安装

### 2. cuDNN 安装
- 安装 cuDNN 8.9.2，与 CUDA 11.8 和 onnxruntime-gpu 1.16.0 兼容
- 文件大小: 469.4 MB

### 3. LD_LIBRARY_PATH 设置
- 在 app.py 中设置 LD_LIBRARY_PATH，确保 cuDNN 库能被找到
- 这是关键修复，没有这个设置，ONNX Runtime 无法找到 cuDNN 库

## 下一步

### 1. 重启服务
```bash
# 停止服务
ps aux | grep 'python app.py' | grep -v grep | awk '{print $2}' | xargs -r kill -9

# 重启服务
source /opt/conda/etc/profile.d/conda.sh
conda activate human
cd /app/HeyGem-Linux-Python-Hack
export LD_LIBRARY_PATH=/opt/conda/envs/human/lib:$LD_LIBRARY_PATH
nohup python app.py > /tmp/gradio.log 2>&1 &
```

### 2. 验证 GPU 使用
- 监控 GPU 利用率
- 检查任务执行情况
- 验证队列阻塞问题是否解决

### 3. 测试功能
- 提交测试任务
- 观察 GPU 使用情况
- 检查任务完成情况

## 相关文件
- `修复onnxruntime版本.sh` - 修复脚本（支持多镜像源）
- `安装cudnn.sh` - cuDNN 安装脚本
- `app.py` - 主程序（已添加 LD_LIBRARY_PATH 设置）
- `requirements.txt` - 依赖文件（已更新 onnxruntime-gpu 版本）
- `onnxruntime修复报告.md` - 修复报告

## 总结

### 修复成功 ✅
- ONNX Runtime GPU 支持已正常启用
- 检查脚本显示 "ONNX Runtime is successfully using the GPU."
- 所有依赖已正确安装

### 关键点
1. **版本匹配**: cudatoolkit 11.8.0 + onnxruntime-gpu 1.16.0 + cuDNN 8.9.2
2. **LD_LIBRARY_PATH**: 必须设置，确保 cuDNN 库能被找到
3. **多镜像源**: 使用多个国内镜像源，避免下载慢的问题

### 预期效果
- GPU 利用率应该 > 0%
- 任务处理速度应该提高
- 队列阻塞问题应该得到缓解

