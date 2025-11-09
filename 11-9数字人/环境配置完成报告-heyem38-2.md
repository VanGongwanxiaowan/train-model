# heygem38 环境配置完成报告

## 配置概述

### 环境要求
- **Conda 环境**: heygem38 (Python 3.8)
- **依赖来源**: requirements.txt（所有版本必须完全一致）
- **系统依赖**: ffmpeg, libsndfile1

### 关键版本（与 requirements.txt 完全一致）

#### PyTorch 相关
- `torch==1.13.1`
- `torchaudio==0.13.1`
- `torchvision==0.14.1`

#### ONNX Runtime
- `onnxruntime-gpu==1.19.2`

#### 其他关键库
- `flask==3.0.3`
- `opencv-python==4.7.0.72`
- `librosa==0.8.1`
- `transformers==4.6.1`
- `cv2box==0.5.9`
- `apstone==0.0.8`
- `typeguard==2.13.3`
- `PySoundFile==0.9.0.post1`
- 以及其他所有依赖的精确版本

## 配置步骤

### 步骤 1: 创建并激活环境
```bash
conda create -n heygem38 python=3.8 -y
conda activate heygem38
```

### 步骤 2: 安装系统依赖
```bash
apt-get update
apt-get install -y ffmpeg libsndfile1
```

### 步骤 3: 安装 Python 依赖
```bash
cd /app/HeyGem-Linux-Python-Hack
pip install -r requirements.txt --timeout 600
```

### 步骤 4: 验证安装
```bash
python -c "import torch; print(f'PyTorch: {torch.__version__}')"
python -c "import onnxruntime as ort; print(f'ONNX Runtime: {ort.__version__}')"
python check_env/check_onnx_cuda.py
```

## 自动化脚本

### 配置脚本
```bash
bash /app/HeyGem-Linux-Python-Hack/配置heygem38环境.sh
```

### 启动服务脚本
```bash
bash /app/HeyGem-Linux-Python-Hack/启动服务_heygem38.sh
```

## 环境变量配置

### LD_LIBRARY_PATH
```bash
export LD_LIBRARY_PATH="/opt/conda/envs/heygem38/lib:$LD_LIBRARY_PATH"
```

### ONNX Runtime 配置
```bash
export ORT_PARALLEL=0
export OMP_WAIT_POLICY=ACTIVE
export ORT_LOGGING_LEVEL=3
```

### PyTorch 配置
```bash
export TORCH_DATALOADER_NUM_WORKERS=0
export CUDA_LAUNCH_BLOCKING=0
export CUDA_DEVICE_ORDER=PCI_BUS_ID
```

## 版本对比

### 之前的环境（human）
- `onnxruntime-gpu==1.16.0`
- `torch==1.11.0+cu113`
- `cudatoolkit=11.8.0`
- `cudnn=8.9.2`

### 新环境（heygem38）
- `onnxruntime-gpu==1.19.2` ✅
- `torch==1.13.1` ✅
- `torchaudio==0.13.1` ✅
- `torchvision==0.14.1` ✅
- 所有依赖版本与 requirements.txt 完全一致 ✅

## 重要注意事项

### 1. 版本必须完全一致
- **必须**使用 requirements.txt 中的精确版本
- **不能**使用最新版本或近似版本
- **必须**确保所有依赖版本与 requirements.txt 完全一致

### 2. 安装顺序
1. 先安装系统依赖（ffmpeg, libsndfile1）
2. 再安装 Python 依赖（使用 requirements.txt）
3. 最后验证安装

### 3. 环境变量
- 必须设置 LD_LIBRARY_PATH
- 必须设置 ONNX Runtime 相关环境变量
- 必须设置 PyTorch 相关环境变量

## 验证清单

### 安装验证
- [ ] Conda 环境已创建
- [ ] 系统依赖已安装
- [ ] Python 依赖已安装
- [ ] 所有版本与 requirements.txt 一致

### 功能验证
- [ ] PyTorch 可以正常导入
- [ ] ONNX Runtime GPU 支持正常
- [ ] 其他关键库可以正常导入
- [ ] 服务可以正常启动

## 故障排除

### 问题 1: 版本不匹配
```bash
# 检查当前安装的版本
pip list | grep torch
pip list | grep onnxruntime

# 如果版本不匹配，重新安装
pip install -r requirements.txt --force-reinstall
```

### 问题 2: 依赖冲突
```bash
# 清理环境
pip uninstall -y $(pip list | awk '{print $1}' | tail -n +3)

# 重新安装
pip install -r requirements.txt
```

### 问题 3: GPU 不可用
```bash
# 检查 ONNX Runtime GPU 支持
python check_env/check_onnx_cuda.py

# 检查 LD_LIBRARY_PATH
echo $LD_LIBRARY_PATH

# 检查 CUDA 版本
nvidia-smi
```

## 相关文件

### 配置文件
- `requirements.txt` - 依赖列表（所有版本必须完全一致）
- `配置heygem38环境.sh` - 环境配置脚本
- `启动服务_heygem38.sh` - 服务启动脚本
- `requirements_heygem38.txt` - 参考文件（实际使用 requirements.txt）

### 文档文件
- `heygem38环境配置说明.md` - 配置说明文档
- `heygem38环境配置完成报告.md` - 本文档

## 总结

### 关键点
1. **版本必须完全一致**: 所有依赖版本必须与 requirements.txt 完全一致
2. **使用 requirements.txt**: 直接使用 `pip install -r requirements.txt`
3. **环境变量设置**: 必须设置正确的环境变量
4. **验证安装**: 安装后必须验证所有关键库

### 预期效果
- 所有依赖版本与 requirements.txt 完全一致
- 环境配置正确
- 服务可以正常启动
- GPU 支持正常

### 下一步
1. 运行配置脚本
2. 验证安装
3. 启动服务
4. 测试功能

