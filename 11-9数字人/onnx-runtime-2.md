# ONNX Runtime 降级报告

## 操作说明

### 目标
将 ONNX Runtime 从 1.19.2 降级到 1.18.0，以解决队列阻塞问题。

### 原因
- 当前版本: onnxruntime-gpu 1.19.2
- 目标版本: onnxruntime-gpu 1.18.0
- 之前我们已经尝试降级到 1.16.0，但环境又被更新了
- 1.18.0 是项目推荐版本，应该更稳定

## 操作步骤

### 1. 卸载当前版本

```bash
pip uninstall -y onnxruntime-gpu onnxruntime
```

### 2. 安装目标版本

```bash
pip install onnxruntime-gpu==1.18.0 \
    -i https://pypi.tuna.tsinghua.edu.cn/simple \
    --trusted-host pypi.tuna.tsinghua.edu.cn \
    --timeout=600
```

### 3. 验证安装

```bash
python -c "import onnxruntime as ort; print('ONNX Runtime 版本:', ort.__version__); print('可用提供者:', ort.get_available_providers())"
```

## 验证结果

### 预期输出

```
✅ ONNX Runtime 版本: 1.18.0
✅ 可用提供者: ['TensorrtExecutionProvider', 'CUDAExecutionProvider', 'AzureExecutionProvider', 'CPUExecutionProvider']
```

## 注意事项

1. **服务重启**: 降级后需要重启服务以应用新版本
2. **队列阻塞**: 降级到 1.18.0 应该有助于解决队列阻塞问题
3. **环境变量**: 确保 `ORT_PARALLEL=0` 环境变量已设置

## 更新日期

2025-11-09

