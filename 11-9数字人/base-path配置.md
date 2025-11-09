# app.py BASE_PATH 配置说明

## 📋 修改概述

已在 `app.py` 中添加 `BASE_PATH` 配置，使其与 `fastapi_app.py` 保持一致。

## 🔧 修改内容

### 1. 添加 BASE_PATH 配置（第27行）

```python
# 内部配置路径（与fastapi_app.py保持一致）
BASE_PATH = "/data2/home_back/gujiaxin/work/batchshort1/assrt/human"
```

### 2. 修改结果目录路径（第238-244行）

**修改前**:
```python
final_result_dir = os.path.join("result", code)
os.makedirs(final_result_dir, exist_ok=True)
```

**修改后**:
```python
# 使用BASE_PATH作为结果目录的基础路径
if BASE_PATH:
    final_result_dir = os.path.join(BASE_PATH, "result", code)
else:
    final_result_dir = os.path.join("result", code)
os.makedirs(final_result_dir, exist_ok=True)
logger.info(f"[{code}] 结果目录: {final_result_dir}")
```

### 3. 添加启动时目录检查（第298-306行）

```python
if __name__ == "__main__":
    # 确保 BASE_PATH 目录存在
    try:
        if BASE_PATH and not os.path.exists(BASE_PATH):
            os.makedirs(BASE_PATH, exist_ok=True)
            logger.info(f"创建 BASE_PATH 目录: {BASE_PATH}")
        elif BASE_PATH:
            logger.info(f"BASE_PATH 目录已存在: {BASE_PATH}")
    except Exception as e:
        logger.warning(f"创建 BASE_PATH 目录失败: {e}，继续启动服务")
    
    processor = VideoProcessor()
    # ... 其余代码
```

## ✅ 配置效果

### 1. 统一路径配置
- `fastapi_app.py` 和 `app.py` 现在使用相同的 `BASE_PATH`
- 确保两个服务的结果文件存储在同一位置

### 2. 结果文件路径
- **修改前**: `result/{code}/`
- **修改后**: `/data2/home_back/gujiaxin/work/batchshort1/assrt/human/result/{code}/`

### 3. 自动目录创建
- 服务启动时自动检查 `BASE_PATH` 目录
- 如果不存在，自动创建
- 创建失败时记录警告，但继续启动服务

## 📁 目录结构

```
/data2/home_back/gujiaxin/work/batchshort1/assrt/human/
├── result/
│   └── {task_id}/
│       └── {task_id}-r.mp4
└── ...
```

## 🔍 验证方法

### 1. 检查配置是否正确
```bash
# 查看app.py中的BASE_PATH配置
grep "BASE_PATH" /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service/HeyGem-Linux-Python-Hack/app.py
```

### 2. 检查服务启动日志
```bash
# 查看Gradio服务启动日志
docker exec starpainting-digital-human-service-1 tail -f /tmp/gradio.log | grep "BASE_PATH"
```

### 3. 检查结果目录
```bash
# 查看结果目录
docker exec starpainting-digital-human-service-1 ls -lh /data2/home_back/gujiaxin/work/batchshort1/assrt/human/result/
```

## ⚠️ 注意事项

1. **路径一致性**: 确保 `BASE_PATH` 与 `fastapi_app.py` 中的配置一致
2. **目录权限**: 确保容器有权限在 `BASE_PATH` 目录下创建子目录
3. **卷挂载**: 确保 Docker 正确挂载了数据目录
4. **向后兼容**: 如果 `BASE_PATH` 为空，会使用原来的相对路径 `result/`

## 🔄 重启服务

修改后需要重启 Gradio 服务：

```bash
# 停止Gradio服务
docker exec starpainting-digital-human-service-1 pkill -f "python app.py"

# 重新启动Gradio服务
docker exec -d starpainting-digital-human-service-1 bash -c "source /opt/conda/etc/profile.d/conda.sh && conda activate human && cd /app/HeyGem-Linux-Python-Hack && export PYTHONPATH=/app/HeyGem-Linux-Python-Hack && export LD_LIBRARY_PATH=/opt/conda/envs/human/lib:\$LD_LIBRARY_PATH && nohup python app.py > /tmp/gradio.log 2>&1 &"
```

## 📊 修改对比

| 项目 | 修改前 | 修改后 |
|------|--------|--------|
| **BASE_PATH配置** | ❌ 无 | ✅ 已添加 |
| **结果目录路径** | `result/{code}/` | `/data2/.../human/result/{code}/` |
| **启动时目录检查** | ❌ 无 | ✅ 已添加 |
| **路径一致性** | ❌ 不一致 | ✅ 与fastapi_app.py一致 |

## 📝 相关文件

- `app.py` - Gradio应用主文件（已修改）
- `fastapi_app.py` - FastAPI服务主文件（已有BASE_PATH配置）
- `BASE_PATH配置说明.md` - BASE_PATH详细配置说明

## 📅 修改日期

2025-01-27

