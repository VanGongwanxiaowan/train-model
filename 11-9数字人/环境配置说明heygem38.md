# heygem38 环境配置说明

## 概述
这是数字人项目的新环境配置，使用 `heygem38` conda 环境和 `onnxruntime-gpu==1.19.2`（比之前的 1.16.0 更新）。

## 环境配置

### 1. 激活环境
```bash
source /root/miniconda3/bin/activate heygem38
```

### 2. 安装系统依赖
```bash
apt-get update
apt-get install -y ffmpeg libsndfile1
```
- `ffmpeg`：视频处理工具
- `libsndfile1`：音频文件处理库

### 3. 安装所有依赖（使用 requirements.txt 中的精确版本）
```bash
cd /app/HeyGem-Linux-Python-Hack
pip install -r requirements.txt --timeout 600
```

**关键版本**（与 requirements.txt 完全一致）：
- `torch==1.13.1`
- `torchaudio==0.13.1`
- `torchvision==0.14.1`
- `onnxruntime-gpu==1.19.2`
- `flask==3.0.3`
- `opencv-python==4.7.0.72`
- `librosa==0.8.1`
- 以及其他所有依赖的精确版本

**注意**：
- 必须使用 `requirements.txt` 中的精确版本
- 不能使用最新版本，避免版本冲突
- 所有版本必须与 requirements.txt 完全一致

## 配置脚本

### 自动配置脚本
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
export LD_LIBRARY_PATH="/root/miniconda3/envs/heygem38/lib:$LD_LIBRARY_PATH"
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
- `cudatoolkit=11.8.0`
- `cudnn=8.9.2`

### 新环境（heygem38）
- `onnxruntime-gpu==1.19.2`
- `torch==1.13.1`
- `torchaudio==0.13.1`
- `torchvision==0.14.1`
- 所有依赖版本与 requirements.txt 完全一致

## 优势

### 1. 更新的 ONNX Runtime
- `onnxruntime-gpu==1.19.2` 比 1.16.0 更新
- 可能包含性能改进和 bug 修复
- 可能更好地支持 GPU

### 2. 精确的版本控制
- 使用 requirements.txt 中的精确版本
- 避免版本冲突
- 确保环境一致性

### 3. 更好的兼容性
- 所有依赖版本经过测试
- 确保与项目代码兼容
- 避免版本不匹配问题

## 使用步骤

### 步骤 1: 配置环境
```bash
bash /app/HeyGem-Linux-Python-Hack/配置heygem38环境.sh
```

### 步骤 2: 验证安装
```bash
python -c "import torch; print(f'PyTorch version: {torch.__version__}')"
python -c "import onnxruntime as ort; print(f'ONNX Runtime version: {ort.__version__}')"
python check_env/check_onnx_cuda.py
```

### 步骤 3: 启动服务
```bash
bash /app/HeyGem-Linux-Python-Hack/启动服务_heygem38.sh
```

### 步骤 4: 验证服务
```bash
curl http://localhost:7860/
```

## 注意事项

### 1. 环境路径
- 确保 conda 环境路径正确
- 如果路径不同，需要修改启动脚本中的路径

### 2. GPU 支持
- 确保 GPU 驱动和 CUDA 版本兼容
- 验证 ONNX Runtime GPU 支持

### 3. 依赖版本
- 如果遇到依赖冲突，可能需要调整版本
- 建议使用虚拟环境隔离依赖

### 4. 系统依赖
- 确保系统依赖已安装
- 如果安装失败，可能需要更新 apt 源

## 故障排除

### 问题 1: 环境不存在
```bash
# 检查环境是否存在
conda env list

# 如果不存在，创建环境
conda create -n heygem38 python=3.8
```

### 问题 2: ONNX Runtime GPU 不可用
```bash
# 检查 ONNX Runtime GPU 支持
python check_env/check_onnx_cuda.py

# 如果不可用，检查 LD_LIBRARY_PATH
echo $LD_LIBRARY_PATH
```

### 问题 3: 依赖安装失败
```bash
# 使用国内镜像源
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple <package>

# 或者增加超时时间
pip install --timeout 600 <package>
```

## 相关文件
- `配置heygem38环境.sh` - 环境配置脚本
- `启动服务_heygem38.sh` - 服务启动脚本
- `requirements_heygem38.txt` - 依赖列表
- `heygem38环境配置说明.md` - 本文档

## 总结

### 主要改进
1. **更新的 ONNX Runtime**: 1.19.2 比 1.16.0 更新
2. **更简单的依赖管理**: 使用 pip 安装所有依赖
3. **更好的兼容性**: PyTorch 使用最新版本

### 预期效果
- 更好的 GPU 支持
- 更高的处理速度
- 更少的队列阻塞问题

### 下一步
1. 配置环境
2. 验证安装
3. 启动服务
4. 测试功能

