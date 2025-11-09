# ONNX Runtime GPU 修复总结

## 修复进度

### ✅ 已完成
1. **卸载旧版本**: onnxruntime-gpu 1.19.2 已卸载
2. **安装新版本**: onnxruntime-gpu 1.16.0 已成功安装
3. **版本验证**: 已确认 onnxruntime-gpu 1.16.0 安装成功

### ⏳ 进行中
4. **安装 cudatoolkit**: cudatoolkit 11.8.0 正在安装中（遇到资源问题，需要重试）

### ❌ 待完成
5. **验证 GPU 可用性**: 等待 cudatoolkit 安装完成后验证

## 当前状态

### 错误信息
```
Failed to load library libonnxruntime_providers_cuda.so with error: 
libcublasLt.so.11: cannot open shared object file: No such file or directory
```

### 问题分析
- onnxruntime-gpu 1.16.0 需要 CUDA 11.8 的库（libcublasLt.so.11）
- 系统只有 CUDA 12.4 的库（libcublasLt.so.12）
- 需要安装 cudatoolkit 11.8.0 来提供 CUDA 11.8 的库

## 解决方案

### 步骤1: 安装 cudatoolkit 11.8.0
```bash
docker exec starpainting-digital-human-service-1 bash -c \
  "source /opt/conda/etc/profile.d/conda.sh && \
   conda activate human && \
   conda install -y -c conda-forge cudatoolkit=11.8.0"
```

### 步骤2: 设置 LD_LIBRARY_PATH
在启动脚本或环境变量中设置：
```bash
export LD_LIBRARY_PATH=/opt/conda/envs/human/lib:$LD_LIBRARY_PATH
```

### 步骤3: 验证安装
```bash
docker exec starpainting-digital-human-service-1 bash -c \
  "source /opt/conda/etc/profile.d/conda.sh && \
   conda activate human && \
   export LD_LIBRARY_PATH=/opt/conda/envs/human/lib:\$LD_LIBRARY_PATH && \
   cd /app/HeyGem-Linux-Python-Hack && \
   python check_env/check_onnx_cuda.py"
```

### 预期输出
```
ONNX Runtime is successfully using the GPU.
[输出形状信息]
```

## 已验证的版本组合

| cudatoolkit | onnxruntime-gpu | 状态 |
|------------|----------------|------|
| 11.8.0 | 1.16.0 | ⏳ 安装中 |

## 下一步行动

1. ✅ **已完成**: 卸载 onnxruntime-gpu 1.19.2
2. ✅ **已完成**: 安装 onnxruntime-gpu 1.16.0
3. ⏳ **进行中**: 安装 cudatoolkit 11.8.0
4. ⏳ **待处理**: 设置 LD_LIBRARY_PATH
5. ⏳ **待处理**: 验证 GPU 可用性
6. ⏳ **待处理**: 重启服务并验证

## 注意事项

1. **cudatoolkit 安装**: 可能需要较长时间（682.5 MB），请耐心等待
2. **资源问题**: 如果遇到 "Resource temporarily unavailable" 错误，可以：
   - 清理 conda 缓存：`conda clean -y --all`
   - 重试安装
3. **LD_LIBRARY_PATH**: 必须包含 conda 环境的 lib 目录，优先于系统 CUDA 库

## 相关文件

- `setup_onnx_gpu.sh`: 环境设置脚本
- `onnxruntime_gpu修复进度.md`: 修复进度记录

## 日期

2025-11-09

