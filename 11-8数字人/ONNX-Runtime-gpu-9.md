# ONNX Runtime GPU 问题详细诊断报告

## ✅ 检查结果总结

### ❌ ONNX Runtime 无法使用 GPU

**检查脚本输出：**
```
ONNX Runtime is NOT using the GPU or there was an error initializing the CUDA provider.
```

**错误信息：**
```
Failed to load library libonnxruntime_providers_cuda.so with error: 
libcudnn.so.9: cannot open shared object file: No such file or directory

Failed to create CUDAExecutionProvider. 
Require cuDNN 9.* and CUDA 12.*
```

## 🔍 详细诊断

### 1. 已安装的组件 ✅

| 组件 | 版本/状态 | 说明 |
|------|----------|------|
| onnxruntime-gpu | 1.19.2 | ✅ 已安装 |
| CUDA | 12.4.131 | ✅ 已安装 |
| NVIDIA 驱动 | 535.104.12 | ✅ 正常 |
| GPU | A100-SXM4-80GB x8 | ✅ 可用 |
| Python | 3.8 | ✅ 正常 |

### 2. 缺失的组件 ❌

| 组件 | 状态 | 影响 |
|------|------|------|
| cuDNN 9.* | ❌ **未找到** | **关键问题** |
| libcudnn.so.9 | ❌ **不存在** | ONNX Runtime 无法使用 GPU |

### 3. 库路径配置

**当前 LD_LIBRARY_PATH：**
```
/usr/local/nvidia/lib:/usr/local/nvidia/lib64
```

**缺失的路径：**
- `/usr/local/cuda/lib64` (CUDA 库路径)
- cuDNN 库路径（未安装）

**ldconfig 配置的路径：**
```
/usr/local/cuda/targets/x86_64-linux/lib
/usr/local/cuda-12/targets/x86_64-linux/lib
/usr/local/cuda-12.4/targets/x86_64-linux/lib
/usr/local/lib
/usr/local/nvidia/lib
/usr/local/nvidia/lib64
```

## 🎯 问题根源

### 核心问题
**cuDNN 9.* 未安装**，导致 ONNX Runtime 无法初始化 CUDA 执行提供程序。

### 影响链
1. **cuDNN 缺失** → ONNX Runtime 无法使用 GPU
2. **GPU 不可用** → 所有推理降级到 CPU
3. **CPU 推理** → 性能严重下降
4. **性能问题** → 可能导致 SDK 初始化超时或失败
5. **初始化失败** → 可能导致多进程创建问题

## 💡 解决方案

### 方案1: 安装 cuDNN 9.*（推荐）

#### 选项A: 使用 NVIDIA CUDA 镜像（包含 cuDNN）
```dockerfile
FROM nvidia/cuda:12.4.0-cudnn9-devel-ubuntu22.04
```

#### 选项B: 在现有镜像中安装 cuDNN
```bash
# 下载并安装 cuDNN
# 需要从 NVIDIA 官网下载 cuDNN 9.* for CUDA 12.4
```

#### 选项C: 使用 conda 安装 cuDNN
```bash
conda install -c conda-forge cudnn
```

### 方案2: 更新 LD_LIBRARY_PATH

在 Dockerfile 或启动脚本中设置：
```dockerfile
ENV LD_LIBRARY_PATH=/usr/local/cuda/lib64:/usr/local/cuda/lib:$LD_LIBRARY_PATH
```

### 方案3: 使用兼容的 onnxruntime-gpu 版本

检查 onnxruntime-gpu 1.19.2 的 CUDA/cuDNN 要求：
- 可能需要特定版本的 cuDNN
- 或者使用支持当前环境的 onnxruntime-gpu 版本

## 📋 验证步骤

### 1. 检查 cuDNN 安装
```bash
docker exec starpainting-digital-human-service-1 find /usr -name "libcudnn.so*" 2>/dev/null
```

**预期输出：** 应该找到 `libcudnn.so.9.*` 文件

### 2. 检查 CUDA 环境
```bash
docker exec starpainting-digital-human-service-1 nvcc --version
docker exec starpainting-digital-human-service-1 ls -la /usr/local/cuda/lib64/libcudnn*
```

### 3. 重新运行检查脚本
```bash
docker exec starpainting-digital-human-service-1 sh -c 'cd /app/HeyGem-Linux-Python-Hack && python check_env/check_onnx_cuda.py'
```

**预期输出：**
```
ONNX Runtime is successfully using the GPU.
[输出形状信息]
```

## 🔗 与模块导入问题的关联

### 可能的关联
1. **GPU 不可用** → SDK 初始化可能失败或超时
2. **初始化失败** → 可能导致多进程创建问题
3. **资源竞争** → CPU 推理可能导致资源不足
4. **错误传播** → GPU 初始化失败可能掩盖其他错误

### 建议的修复顺序
1. **优先修复 ONNX Runtime GPU 问题**（安装 cuDNN）
2. **验证 GPU 可用性**（运行检查脚本）
3. **重新测试服务**（验证模块导入问题是否解决）
4. **如果问题仍然存在**，继续排查其他原因

## 📊 当前状态

### ✅ 正常
- CUDA 12.4 已安装
- NVIDIA 驱动正常
- GPU 硬件可用
- onnxruntime-gpu 已安装

### ❌ 异常
- cuDNN 9.* 未安装
- ONNX Runtime 无法使用 GPU
- 所有推理降级到 CPU

### ⚠️ 影响
- 性能严重下降（CPU 推理）
- 可能导致 SDK 初始化问题
- 可能与模块导入问题相关

## 🚀 下一步行动

1. ✅ **已完成**: 诊断 ONNX Runtime GPU 问题
2. ⏳ **待处理**: 安装 cuDNN 9.*
3. ⏳ **待处理**: 更新 LD_LIBRARY_PATH（如果需要）
4. ⏳ **待处理**: 验证 GPU 可用性
5. ⏳ **待处理**: 重新测试服务
6. ⏳ **待处理**: 验证模块导入问题是否解决

## 📚 参考资源

- [ONNX Runtime GPU 要求](https://onnxruntime.ai/docs/execution-providers/CUDA-ExecutionProvider.html#requirements)
- [cuDNN 安装指南](https://docs.nvidia.com/deeplearning/cudnn/install-guide/)
- [NVIDIA CUDA Docker 镜像](https://hub.docker.com/r/nvidia/cuda)
- [cuDNN 下载页面](https://developer.nvidia.com/cudnn)

## 📅 日期

2025-11-09

---

## 总结

**关键发现：**
- ❌ cuDNN 9.* 未安装是导致 ONNX Runtime 无法使用 GPU 的根本原因
- ⚠️ 这可能导致性能问题和 SDK 初始化问题
- 🔧 需要安装 cuDNN 9.* 才能解决这个问题

**建议：**
1. 优先修复 cuDNN 安装问题
2. 验证修复后重新测试服务
3. 检查模块导入问题是否随之解决

