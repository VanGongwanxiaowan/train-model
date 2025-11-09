# 数字人Docker版本清单

## 📦 基础镜像版本

### Docker基础镜像
- **镜像**: `registry.cn-hangzhou.aliyuncs.com/huace_nlp/python:12.4.0-devel-ubuntu22.04`
- **Python版本**: 12.4.0 (基础镜像自带)
- **操作系统**: Ubuntu 22.04
- **架构**: Linux x86_64

### Conda环境
- **Conda版本**: Miniconda3-latest (从清华镜像源下载)
- **Python环境**: Python 3.8
- **环境名称**: `human`
- **安装路径**: `/opt/conda`

## 🐍 Python核心依赖版本

### 深度学习框架
| 包名 | 版本 | 说明 |
|------|------|------|
| **torch** | 1.11.0+cu113 | PyTorch，CUDA 11.3版本 |
| **torchvision** | 0.12.0+cu113 | 图像处理库，CUDA 11.3版本 |
| **torchaudio** | 0.11.0+cu113 | 音频处理库，CUDA 11.3版本 |
| **onnxruntime-gpu** | 1.16.0 | ONNX Runtime GPU版本 |

### CUDA相关
- **CUDA版本**: 11.3 (PyTorch内置)
- **cuDNN版本**: 8.x (要求)
- **cudatoolkit**: 11.8.0 (conda安装)
- **cuDNN**: 8.9.2.26 (conda安装)

## 📚 Python包版本清单

### Web框架
| 包名 | 版本 |
|------|------|
| fastapi | 0.115.11 |
| uvicorn | 0.33.0 |
| starlette | 0.44.0 |
| gradio | 4.44.1 |
| gradio_client | 1.3.0 |
| Flask | 3.0.3 |
| Werkzeug | 3.0.6 |

### 数据处理
| 包名 | 版本 |
|------|------|
| numpy | 1.24.4 |
| pandas | 2.0.3 |
| scipy | 1.10.1 |
| scikit-learn | 1.3.2 |
| scikit-image | 0.21.0 |

### 图像处理
| 包名 | 版本 |
|------|------|
| opencv-python | 4.11.0.86 |
| pillow | 10.4.0 |
| imageio | 2.35.1 |
| matplotlib | 3.7.5 |

### 音频处理
| 包名 | 版本 |
|------|------|
| librosa | 0.11.0 |
| soundfile | 0.13.1 |
| soxr | 0.3.7 |
| audioread | 3.0.1 |
| pydub | 0.25.1 |

### 工具库
| 包名 | 版本 |
|------|------|
| pydantic | 2.10.6 |
| pydantic_core | 2.27.2 |
| PyYAML | 6.0.2 |
| click | 8.1.8 |
| tqdm | 4.67.1 |
| requests | 2.32.3 |
| httpx | 0.28.1 |

### 科学计算
| 包名 | 版本 |
|------|------|
| numba | 0.58.1 |
| llvmlite | 0.41.1 |
| numexpr | 2.8.6 |
| sympy | 1.13.3 |
| mpmath | 1.3.0 |

### 其他依赖
| 包名 | 版本 |
|------|------|
| einops | 0.8.1 |
| huggingface-hub | 0.29.3 |
| protobuf | 5.29.4 |
| flatbuffers | 25.2.10 |
| orjson | 3.10.15 |
| aiofiles | 23.2.1 |
| anyio | 4.5.2 |
| websockets | 12.0 |
| python-multipart | 0.0.20 |

### 项目特定库
| 包名 | 版本 |
|------|------|
| cv2box | 0.5.9 |
| apstone | 0.0.8 |
| ffmpy | 0.5.0 |

## 🔧 系统工具版本

### 系统软件
- **ffmpeg**: 通过conda安装
- **curl**: 系统包管理器安装
- **wget**: 系统包管理器安装
- **tzdata**: 系统时区数据

### 时区配置
- **时区**: Asia/Shanghai
- **时间格式**: UTC+8

## 📋 完整依赖列表

### requirements_0.txt (目标版本)
包含104个依赖包，主要版本如上所示。

### requirements.txt (原始版本)
包含66个依赖包，部分版本较旧：
- numpy: 1.21.6
- opencv-python: 4.7.0.72
- librosa: 0.8.1
- onnxruntime-gpu: 1.9.0

## 🔍 版本检查

### 关键版本要求
根据 `check_environment.py` 脚本，以下包必须匹配指定版本：
- torch: 1.11.0+cu113
- torchvision: 0.12.0+cu113
- torchaudio: 0.11.0+cu113
- onnxruntime-gpu: 1.16.0
- gradio: 4.44.1
- fastapi: 0.115.11
- uvicorn: 0.33.0
- numpy: 1.24.4
- opencv-python: 4.11.0.86
- librosa: 0.11.0
- scipy: 1.10.1
- pandas: 2.0.3
- pillow: 10.4.0
- pydantic: 2.10.6
- starlette: 0.44.0
- pydantic_core: 2.27.2
- numba: 0.58.1
- soundfile: 0.13.1
- scikit-learn: 1.3.2
- scikit-image: 0.21.0

## 🚀 服务配置版本

### HTTP服务
- **服务IP**: 0.0.0.0
- **服务端口**: 8383
- **框架**: FastAPI + Uvicorn

### 日志配置
- **日志目录**: ./log
- **日志文件**: dh.log

### 临时文件
- **临时目录**: ./
- **清理开关**: 1 (启用)

### 结果文件
- **结果目录**: ./result
- **清理开关**: 0 (禁用)

### 数字人配置
- **batch_size**: 1

## 📝 版本来源文件

1. **Dockerfile**: `/data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service/HeyGem-Linux-Python-Hack/Dockerfile`
2. **requirements_0.txt**: 目标版本依赖列表
3. **requirements.txt**: 原始版本依赖列表
4. **requirements_target.txt**: 目标版本清单
5. **check_environment.py**: 环境版本检查脚本
6. **config/config.ini**: 服务配置文件

## ⚠️ 版本兼容性说明

### CUDA版本要求
- PyTorch 1.11.0+cu113 需要 CUDA 11.3
- onnxruntime-gpu 1.16.0 需要 CUDA 11.x 和 cuDNN 8.x
- 已安装 cudatoolkit 11.8.0 和 cuDNN 8.9.2.26

### Python版本要求
- 必须使用 Python 3.8
- Conda环境名称: `human`

### 关键依赖版本锁定
以下包版本必须严格匹配，否则会导致模型加载失败：
- torch: 1.11.0+cu113
- torchvision: 0.12.0+cu113
- torchaudio: 0.11.0+cu113
- onnxruntime-gpu: 1.16.0

## 📅 文档生成日期

2025-01-27

## 🔗 相关文档

- `环境版本检查报告.md` - 详细版本检查报告
- `环境版本问题报告.md` - 版本问题分析
- `环境修复说明.md` - 版本修复指南
- `check_environment.py` - 版本检查脚本

