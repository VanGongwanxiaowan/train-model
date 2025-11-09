# ONNX Runtime GPU 配置说明

## ✅ 配置完成

项目已成功配置 ONNX Runtime GPU 支持。

## 已验证的版本组合

| 组件 | 版本 | 安装方式 |
|------|------|----------|
| cudatoolkit | 11.8.0 | conda |
| cuDNN | 8.9.2.26 | conda |
| onnxruntime-gpu | 1.16.0 | pip |

## 环境变量要求

### 必须设置的环境变量

```bash
export LD_LIBRARY_PATH=/opt/conda/envs/human/lib:$LD_LIBRARY_PATH
```

### 为什么需要这个环境变量？

ONNX Runtime GPU 需要找到以下库：
- `libcublasLt.so.11` (CUDA 11.8)
- `libcudnn.so.8` (cuDNN 8.9)

这些库已安装在 `/opt/conda/envs/human/lib` 目录中，但系统默认不会在这个目录中查找动态库，因此需要将其添加到 `LD_LIBRARY_PATH` 中。

## 验证 GPU 可用性

### 运行检查脚本

```bash
cd /app/HeyGem-Linux-Python-Hack
export LD_LIBRARY_PATH=/opt/conda/envs/human/lib:$LD_LIBRARY_PATH
python check_env/check_onnx_cuda.py
```

### 预期输出

```
['CUDAExecutionProvider', 'CPUExecutionProvider']
ONNX Runtime is successfully using the GPU.
(1, 12800, 1)
```

如果看到 "ONNX Runtime is successfully using the GPU."，说明配置成功！

## 项目配置

### requirements.txt

当前项目 `requirements.txt` 中要求：
```
onnxruntime-gpu==1.11.0
```

但已验证 `onnxruntime-gpu==1.16.0` 可用。建议：
- 保持使用 `1.16.0`（已验证可用）
- 或更新 requirements.txt 为 `onnxruntime-gpu==1.16.0`

### fastapi_app.py

`fastapi_app.py` 已更新，会在启动时自动设置 `LD_LIBRARY_PATH`，确保 ONNX Runtime 能够找到 CUDA 库。

## 故障排除

### 问题：找不到 libcublasLt.so.11

**解决方案：**
1. 确认 cudatoolkit 11.8.0 已安装：`conda list cudatoolkit`
2. 确认库文件存在：`ls -la /opt/conda/envs/human/lib/libcublasLt.so.11`
3. 设置 LD_LIBRARY_PATH：`export LD_LIBRARY_PATH=/opt/conda/envs/human/lib:$LD_LIBRARY_PATH`

### 问题：找不到 libcudnn.so.8

**解决方案：**
1. 确认 cuDNN 8.9.2.26 已安装：`conda list cudnn`
2. 确认库文件存在：`ls -la /opt/conda/envs/human/lib/libcudnn.so.8`
3. 设置 LD_LIBRARY_PATH：`export LD_LIBRARY_PATH=/opt/conda/envs/human/lib:$LD_LIBRARY_PATH`

### 问题：ONNX Runtime 仍使用 CPU

**解决方案：**
1. 检查环境变量：`echo $LD_LIBRARY_PATH`
2. 确认 CUDA 库在路径中：`echo $LD_LIBRARY_PATH | grep conda/envs/human/lib`
3. 重新运行检查脚本：`python check_env/check_onnx_cuda.py`

## 安装命令参考

如果需要重新安装，使用以下命令：

```bash
# 1. 卸载旧版本
pip uninstall -y onnxruntime-gpu onnxruntime

# 2. 安装 cudatoolkit
conda install -y -c conda-forge cudatoolkit=11.8.0

# 3. 安装 cuDNN
conda install -y -c conda-forge cudnn=8.9.2.26

# 4. 安装 onnxruntime-gpu
pip install onnxruntime-gpu==1.16.0

# 5. 设置环境变量（在启动脚本中）
export LD_LIBRARY_PATH=/opt/conda/envs/human/lib:$LD_LIBRARY_PATH

# 6. 验证安装
python check_env/check_onnx_cuda.py
```

## 日期

2025-11-09


