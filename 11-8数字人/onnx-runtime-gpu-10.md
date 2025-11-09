# ONNX Runtime GPU 问题诊断报告

## 问题概述

**检查结果：ONNX Runtime 无法使用 GPU**

### 错误信息
```
Failed to load library libonnxruntime_providers_cuda.so with error: 
libcudnn.so.9: cannot open shared object file: No such file or directory

Failed to create CUDAExecutionProvider. 
Require cuDNN 9.* and CUDA 12.*
```

### 检查输出
```
ONNX Runtime is NOT using the GPU or there was an error initializing the CUDA provider.
```

## 问题分析

### 1. ONNX Runtime GPU 状态
- **已安装**: onnxruntime-gpu 1.19.2 ✅
- **可用提供程序**: `['TensorrtExecutionProvider', 'CUDAExecutionProvider', 'CPUExecutionProvider']` ✅
- **GPU 执行提供程序**: ❌ 无法初始化
- **当前使用**: CPUExecutionProvider（降级到 CPU）

### 2. 缺少的依赖
- **cuDNN 9.***: ❌ 未找到 `libcudnn.so.9`
- **CUDA 12.***: ⚠️ 需要验证版本
- **库路径**: LD_LIBRARY_PATH 可能不完整

### 3. 系统环境
- **GPU**: NVIDIA A100-SXM4-80GB ✅
- **驱动版本**: 535.104.12 ✅
- **CUDA 版本**: 12.4.0 (从容器环境变量看到) ✅
- **cuDNN**: ❌ 未找到

## 影响分析

### 对服务的影响
1. **性能问题**: 
   - ONNX Runtime 只能使用 CPU，无法利用 GPU
   - 推理速度会大幅下降
   - 可能无法满足实时处理需求

2. **初始化问题**:
   - SDK 初始化时可能因为 GPU 不可用而失败
   - 多进程创建时可能因为资源问题而失败
   - 可能导致 `ModuleNotFoundError`（间接影响）

3. **资源浪费**:
   - GPU 资源无法被利用
   - CPU 负载增加

## 解决方案

### 方案1: 安装 cuDNN 9.*（推荐）

#### 步骤1: 检查容器基础镜像
```bash
docker exec starpainting-digital-human-service-1 cat /etc/os-release
```

#### 步骤2: 安装 cuDNN
如果使用 NVIDIA CUDA 基础镜像，cuDNN 应该已经包含。可能需要：
1. 更新 Docker 镜像到包含 cuDNN 的版本
2. 或者在 Dockerfile 中安装 cuDNN

#### 步骤3: 验证安装
```bash
docker exec starpainting-digital-human-service-1 ls -la /usr/local/cuda/lib64/libcudnn*
```

### 方案2: 更新 LD_LIBRARY_PATH

如果 cuDNN 已安装但路径不对，需要更新 LD_LIBRARY_PATH：

```bash
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:/usr/local/cuda/lib:$LD_LIBRARY_PATH
```

### 方案3: 使用兼容的 onnxruntime-gpu 版本

检查 onnxruntime-gpu 1.19.2 的 CUDA/cuDNN 要求：
- 可能需要更新到支持 CUDA 12.4 的版本
- 或者降级到支持当前环境的版本

### 方案4: 检查 Docker 镜像配置

确保 Docker 镜像包含：
- CUDA 12.*
- cuDNN 9.*
- 正确的库路径配置

## 验证步骤

### 1. 检查 cuDNN 安装
```bash
docker exec starpainting-digital-human-service-1 find /usr -name "libcudnn.so*" 2>/dev/null
```

### 2. 检查 CUDA 环境
```bash
docker exec starpainting-digital-human-service-1 nvcc --version
docker exec starpainting-digital-human-service-1 ls -la /usr/local/cuda/lib64/
```

### 3. 重新运行检查脚本
```bash
docker exec starpainting-digital-human-service-1 sh -c 'cd /app/HeyGem-Linux-Python-Hack && python check_env/check_onnx_cuda.py'
```

预期输出应该包含：
```
ONNX Runtime is successfully using the GPU.
```

## 与模块导入问题的关联

### 可能的关联
1. **资源竞争**: GPU 不可用可能导致 SDK 初始化失败
2. **进程创建**: 多进程创建时可能因为 GPU 资源问题而失败
3. **错误传播**: GPU 初始化失败可能导致其他错误被掩盖

### 建议
1. **优先修复 ONNX Runtime GPU 问题**
2. **然后验证模块导入问题是否解决**
3. **如果问题仍然存在，继续排查其他原因**

## 下一步行动

1. ✅ **已完成**: 诊断 ONNX Runtime GPU 问题
2. ⏳ **待处理**: 安装或配置 cuDNN 9.*
3. ⏳ **待处理**: 验证 GPU 可用性
4. ⏳ **待处理**: 重新测试服务

## 相关资源

- [ONNX Runtime GPU 要求](https://onnxruntime.ai/docs/execution-providers/CUDA-ExecutionProvider.html#requirements)
- [cuDNN 安装指南](https://docs.nvidia.com/deeplearning/cudnn/install-guide/)
- [Docker CUDA 镜像](https://hub.docker.com/r/nvidia/cuda)

## 日期

2025-11-09

