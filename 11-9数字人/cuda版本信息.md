# 数字人Docker环境CUDA版本信息

## 📋 CUDA版本总结

### 1. PyTorch使用的CUDA版本
- **CUDA版本**: **11.3**
- **PyTorch版本**: 1.11.0+cu113
- **说明**: PyTorch是为CUDA 11.3编译的，自带CUDA 11.3库

### 2. Conda环境中安装的CUDA工具包
- **cudatoolkit版本**: **11.8.0**
- **安装方式**: conda安装
- **安装路径**: `/opt/conda/envs/human/lib`
- **用途**: 为ONNX Runtime GPU提供CUDA库支持

### 3. cuDNN版本
- **cuDNN版本**: **8.9.2.26** (cuDNN 8.x系列)
- **安装方式**: conda安装
- **安装路径**: `/opt/conda/envs/human/lib`
- **说明**: cuDNN 8.x与CUDA 11.x兼容

### 4. 系统CUDA版本（宿主机器）
- **可能版本**: CUDA 12.x（根据文档推测）
- **说明**: 系统可能安装了CUDA 12.x，但PyTorch会使用自带的CUDA 11.3库，不会使用系统CUDA

## 🔍 详细的CUDA版本信息

### PyTorch CUDA版本
```python
import torch
print(f"PyTorch版本: {torch.__version__}")  # 1.11.0+cu113
print(f"CUDA可用: {torch.cuda.is_available()}")  # True
print(f"CUDA版本: {torch.version.cuda}")  # 11.3
print(f"cuDNN版本: {torch.backends.cudnn.version()}")  # 对应cuDNN版本
```

### ONNX Runtime GPU CUDA版本
- **ONNX Runtime GPU版本**: 1.16.0
- **需要的CUDA版本**: CUDA 11.x
- **需要的cuDNN版本**: cuDNN 8.x
- **状态**: ✅ 已配置，使用cudatoolkit 11.8.0

## 📊 版本兼容性矩阵

| 组件 | 版本 | CUDA要求 | 状态 |
|------|------|----------|------|
| **PyTorch** | 1.11.0+cu113 | CUDA 11.3 | ✅ 匹配 |
| **cudatoolkit** | 11.8.0 | CUDA 11.8 | ✅ 兼容（向后兼容11.3） |
| **cuDNN** | 8.9.2.26 | cuDNN 8.x | ✅ 匹配 |
| **ONNX Runtime GPU** | 1.16.0 | CUDA 11.x | ✅ 兼容 |
| **系统CUDA** | 12.x (推测) | - | ⚠️ 不使用（PyTorch使用自带库） |

## 🔧 环境变量配置

### 必需的环境变量
```bash
export LD_LIBRARY_PATH=/opt/conda/envs/human/lib:$LD_LIBRARY_PATH
```

### 为什么需要这个环境变量？
ONNX Runtime GPU需要找到以下CUDA库：
- `libcublasLt.so.11` (CUDA 11.8)
- `libcudnn.so.8` (cuDNN 8.9)

这些库已安装在 `/opt/conda/envs/human/lib` 目录中，需要通过 `LD_LIBRARY_PATH` 告诉系统在哪里查找这些库。

## ✅ 验证CUDA版本

### 方法1: 通过PyTorch验证
```bash
docker exec starpainting-digital-human-service-1 bash -c "source /opt/conda/etc/profile.d/conda.sh && conda activate human && python -c 'import torch; print(f\"PyTorch: {torch.__version__}\"); print(f\"CUDA available: {torch.cuda.is_available()}\"); print(f\"CUDA version: {torch.version.cuda}\")'"
```

### 方法2: 检查cudatoolkit版本
```bash
docker exec starpainting-digital-human-service-1 bash -c "source /opt/conda/etc/profile.d/conda.sh && conda activate human && conda list cudatoolkit"
```

### 方法3: 检查cuDNN版本
```bash
docker exec starpainting-digital-human-service-1 bash -c "source /opt/conda/etc/profile.d/conda.sh && conda activate human && conda list cudnn"
```

### 方法4: 检查CUDA库文件
```bash
docker exec starpainting-digital-human-service-1 bash -c "ls -la /opt/conda/envs/human/lib/libcublas*.so*"
docker exec starpainting-digital-human-service-1 bash -c "ls -la /opt/conda/envs/human/lib/libcudnn*.so*"
```

### 方法5: 检查系统CUDA版本（如果安装了nvidia-smi）
```bash
docker exec starpainting-digital-human-service-1 bash -c "nvidia-smi"
# 或者
docker exec starpainting-digital-human-service-1 bash -c "nvcc --version"
```

## 🎯 关键点总结

1. **PyTorch使用CUDA 11.3**
   - PyTorch 1.11.0+cu113自带CUDA 11.3库
   - 不依赖系统CUDA版本
   - 即使系统有CUDA 12.x，PyTorch仍使用自带的CUDA 11.3

2. **ONNX Runtime使用CUDA 11.8**
   - 通过conda安装的cudatoolkit 11.8.0提供CUDA库
   - CUDA 11.8向后兼容CUDA 11.3
   - 需要设置LD_LIBRARY_PATH才能找到CUDA库

3. **cuDNN 8.x**
   - cuDNN 8.9.2.26与CUDA 11.x兼容
   - 已通过conda安装

4. **版本兼容性**
   - ✅ PyTorch CUDA 11.3 + cudatoolkit 11.8.0 = 兼容
   - ✅ ONNX Runtime GPU 1.16.0 + CUDA 11.x = 兼容
   - ✅ cuDNN 8.x + CUDA 11.x = 兼容

## ⚠️ 注意事项

1. **不要升级CUDA版本**
   - PyTorch 1.11.0+cu113必须使用CUDA 11.3
   - 升级到CUDA 12.x会导致PyTorch无法工作

2. **环境变量必须设置**
   - 必须设置 `LD_LIBRARY_PATH=/opt/conda/envs/human/lib`
   - 否则ONNX Runtime GPU无法找到CUDA库

3. **系统CUDA版本不影响**
   - 即使系统有CUDA 12.x，也不会影响PyTorch
   - PyTorch使用自带的CUDA 11.3库

4. **Docker GPU支持**
   - 确保Docker支持GPU（nvidia-docker或--gpus选项）
   - 确保NVIDIA驱动版本支持CUDA 11.3

## 📝 相关文件

- `Dockerfile` - Docker镜像配置
- `ONNX_Runtime_GPU配置说明.md` - ONNX Runtime GPU配置详情
- `环境修复完成报告.md` - 环境修复记录
- `环境修复总结.md` - 环境修复总结

## 📅 文档生成日期

2025-01-27

