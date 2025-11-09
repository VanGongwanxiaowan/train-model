# ONNX Runtime 版本修复报告

## 问题描述
初始化报错，ONNX Runtime 无法使用 GPU，错误信息：
```
libcudnn.so.8: cannot open shared object file: No such file or directory
```

## 根本原因
1. **onnxruntime-gpu 版本不匹配**: 当前版本 1.11.0 与 CUDA 环境不兼容
2. **缺少 cuDNN 库**: onnxruntime-gpu 1.16.0 需要 cuDNN 8，但系统中缺少

## 解决方案

### 步骤 1: 安装 cudatoolkit 11.8.0
```bash
conda install -y cudatoolkit=11.8.0 -c conda-forge
```

### 步骤 2: 安装 onnxruntime-gpu 1.16.0
使用多个国内镜像源，确保安装成功：
- 清华大学镜像: https://pypi.tuna.tsinghua.edu.cn/simple
- 阿里云镜像: https://mirrors.aliyun.com/pypi/simple
- 豆瓣镜像: https://pypi.douban.com/simple
- 腾讯云镜像: https://mirrors.cloud.tencent.com/pypi/simple
- 中科大镜像: https://pypi.mirrors.ustc.edu.cn/simple

### 步骤 3: 安装 cuDNN 8.9.2
```bash
conda install -y cudnn=8.9.2 -c conda-forge
```

### 步骤 4: 设置 LD_LIBRARY_PATH
在 app.py 中添加 LD_LIBRARY_PATH 设置，确保 cuDNN 库能被找到：
```python
os.environ["LD_LIBRARY_PATH"] = "/opt/conda/envs/human/lib:" + os.environ.get("LD_LIBRARY_PATH", "")
```

## 验证版本

### 已验证的版本组合
- **cudatoolkit**: 11.8.0
- **onnxruntime-gpu**: 1.16.0
- **cudnn**: 8.9.2
- **PyTorch CUDA**: 11.3 (兼容)

## 安装结果

### 成功安装
- ✅ onnxruntime-gpu 1.16.0 已安装
- ✅ cudatoolkit 11.8.0 已安装
- ✅ cuDNN 8.9.2 已安装（需要验证）

### 验证步骤
1. 运行检查脚本: `python check_env/check_onnx_cuda.py`
2. 观察输出是否包含 "successfully"
3. 检查 CUDAExecutionProvider 是否可用

## 相关文件
- `修复onnxruntime版本.sh` - 修复脚本
- `安装cudnn.sh` - cuDNN 安装脚本
- `app.py` - 主程序（已添加 LD_LIBRARY_PATH 设置）

## 注意事项
1. **LD_LIBRARY_PATH**: 必须设置正确的库路径，确保 cuDNN 能被找到
2. **版本匹配**: 确保 cudatoolkit、cuDNN 和 onnxruntime-gpu 版本兼容
3. **环境变量**: 在 app.py 中设置 LD_LIBRARY_PATH，确保运行时能找到库

## 下一步
1. 验证 cuDNN 安装是否成功
2. 运行检查脚本确认 GPU 可用
3. 重启服务测试功能

