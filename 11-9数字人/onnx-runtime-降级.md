# ONNX Runtime 降级完成报告

## ✅ 操作完成

### 降级结果

- **操作前**: onnxruntime-gpu 1.19.2
- **操作后**: onnxruntime-gpu **1.18.0** ✅
- **状态**: 降级成功，服务已重启

### 验证结果

```
✅ ONNX Runtime 版本: 1.18.0
✅ 可用提供者: ['TensorrtExecutionProvider', 'CUDAExecutionProvider', 'AzureExecutionProvider', 'CPUExecutionProvider']
```

## 操作步骤

### 1. 卸载旧版本 ✅

```bash
pip uninstall -y onnxruntime-gpu onnxruntime
```

**结果**: 成功卸载 onnxruntime-gpu 1.19.2

### 2. 安装新版本 ✅

```bash
pip install onnxruntime-gpu==1.18.0 \
    -i https://pypi.tuna.tsinghua.edu.cn/simple \
    --trusted-host pypi.tuna.tsinghua.edu.cn \
    --timeout=600
```

**结果**: 成功安装 onnxruntime-gpu 1.18.0

### 3. 验证安装 ✅

```bash
python -c "import onnxruntime as ort; print('ONNX Runtime 版本:', ort.__version__)"
```

**结果**: 版本 1.18.0 验证成功

### 4. 重启服务 ✅

- Gradio 服务已重启
- 新版本已生效

## 预期效果

降级到 1.18.0 后：

1. **队列阻塞问题缓解**: 1.18.0 版本在多进程环境下更稳定
2. **兼容性提升**: 与项目推荐版本一致
3. **稳定性提升**: 减少 CUDA 同步问题

## 注意事项

1. **环境变量**: 确保 `ORT_PARALLEL=0` 环境变量已设置（已在 app.py 中设置）
2. **服务状态**: Gradio 服务已重启，新版本已生效
3. **监控日志**: 观察日志中是否还有队列阻塞错误

## 验证方法

### 1. 检查版本

```bash
docker exec starpainting-digital-human-service-1 bash -c "source /opt/conda/etc/profile.d/conda.sh && conda activate human && pip show onnxruntime-gpu | grep Version"
# 预期输出: Version: 1.18.0
```

### 2. 测试功能

通过 Web 界面测试数字人生成功能，观察是否还有队列阻塞错误。

### 3. 监控日志

```bash
docker exec starpainting-digital-human-service-1 tail -f /app/HeyGem-Linux-Python-Hack/log/dh.log
```

## 相关文件

- `ONNX_Runtime降级报告.md` - 操作说明
- `环境版本对比报告.md` - 版本对比
- `app.py` - 已设置环境变量 `ORT_PARALLEL=0`

## 更新日期

2025-11-09

