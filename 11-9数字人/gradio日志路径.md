# Gradio日志路径更新说明

## 📋 更新概述

已将Gradio日志文件从 `/tmp/gradio.log` 移动到 `log/gradio.log`，与业务日志 `dh.log` 保存在同一目录下。

## 🔧 更新内容

### 1. 日志文件路径变更

**修改前**:
- 日志路径: `/tmp/gradio.log`
- 位置: 容器临时目录

**修改后**:
- 日志路径: `log/gradio.log`
- 完整路径: `/app/HeyGem-Linux-Python-Hack/log/gradio.log`
- 位置: 项目日志目录（与dh.log同目录）

### 2. 代码修改

#### app.py 修改
- 添加了日志目录创建代码
- 确保 `log` 目录在服务启动时存在

```python
# 确保日志目录存在
log_dir = os.path.join(os.path.dirname(__file__), "log")
try:
    os.makedirs(log_dir, exist_ok=True)
    logger.info(f"日志目录: {log_dir}")
except Exception as e:
    logger.warning(f"创建日志目录失败: {e}")
```

#### 启动脚本修改
- `restart_service.sh`: 更新日志路径
- `start_gradio.sh`: 新建启动脚本，使用新日志路径

### 3. 启动命令更新

**修改前**:
```bash
nohup python app.py > /tmp/gradio.log 2>&1 &
```

**修改后**:
```bash
nohup python app.py > log/gradio.log 2>&1 &
```

## 📁 日志目录结构

```
/app/HeyGem-Linux-Python-Hack/log/
├── dh.log          # 业务日志（原有）
└── gradio.log      # Gradio服务日志（新增）
```

## ✅ 优势

1. **统一管理**: 所有日志文件都在同一个目录下，便于管理
2. **持久化**: 日志文件保存在项目目录中，不会因容器重启而丢失（如果目录被挂载）
3. **便于查找**: 与业务日志在同一目录，方便查找和对比
4. **符合规范**: 遵循项目日志目录结构

## 🔍 查看日志

### 新路径查看方法

```bash
# 实时查看Gradio日志
docker exec starpainting-digital-human-service-1 tail -f /app/HeyGem-Linux-Python-Hack/log/gradio.log

# 查看最后50行
docker exec starpainting-digital-human-service-1 tail -50 /app/HeyGem-Linux-Python-Hack/log/gradio.log

# 查看最后100行
docker exec starpainting-digital-human-service-1 tail -100 /app/HeyGem-Linux-Python-Hack/log/gradio.log
```

### 过滤查看

```bash
# 查看BASE_PATH相关日志
docker exec starpainting-digital-human-service-1 tail -100 /app/HeyGem-Linux-Python-Hack/log/gradio.log | grep "BASE_PATH"

# 查看错误日志
docker exec starpainting-digital-human-service-1 tail -100 /app/HeyGem-Linux-Python-Hack/log/gradio.log | grep "ERROR"

# 查看初始化日志
docker exec starpainting-digital-human-service-1 tail -100 /app/HeyGem-Linux-Python-Hack/log/gradio.log | grep "初始化"
```

### 同时查看多个日志

```bash
# 查看Gradio日志和业务日志
docker exec starpainting-digital-human-service-1 tail -f /app/HeyGem-Linux-Python-Hack/log/gradio.log /app/HeyGem-Linux-Python-Hack/log/dh.log
```

## 🚀 启动服务

### 方法1: 使用启动脚本（推荐）

```bash
# 使用新的启动脚本
bash /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service/start_gradio.sh
```

### 方法2: 手动启动

```bash
# 停止旧服务
docker exec starpainting-digital-human-service-1 pkill -f "python app.py"

# 确保日志目录存在
docker exec starpainting-digital-human-service-1 mkdir -p /app/HeyGem-Linux-Python-Hack/log

# 启动服务
docker exec -d starpainting-digital-human-service-1 bash -c "source /opt/conda/etc/profile.d/conda.sh && conda activate human && cd /app/HeyGem-Linux-Python-Hack && export PYTHONPATH=/app/HeyGem-Linux-Python-Hack && export LD_LIBRARY_PATH=/opt/conda/envs/human/lib:\$LD_LIBRARY_PATH && nohup python app.py > log/gradio.log 2>&1 &"
```

## 📊 日志文件对比

| 日志文件 | 路径 | 说明 |
|---------|------|------|
| **dh.log** | `log/dh.log` | 业务日志（原有） |
| **gradio.log** | `log/gradio.log` | Gradio服务日志（新路径） |

## ⚠️ 注意事项

1. **目录权限**: 确保容器有权限在 `log` 目录下创建和写入文件
2. **目录挂载**: 如果 `log` 目录被挂载到宿主机，日志文件会持久化保存
3. **日志轮转**: 建议定期清理或轮转日志文件，避免占用过多磁盘空间
4. **向后兼容**: 旧的 `/tmp/gradio.log` 路径不再使用

## 🔄 迁移步骤

如果需要迁移旧的日志文件：

```bash
# 1. 停止Gradio服务
docker exec starpainting-digital-human-service-1 pkill -f "python app.py"

# 2. 复制旧日志到新位置（如果存在）
docker exec starpainting-digital-human-service-1 bash -c "if [ -f /tmp/gradio.log ]; then mkdir -p /app/HeyGem-Linux-Python-Hack/log && cp /tmp/gradio.log /app/HeyGem-Linux-Python-Hack/log/gradio.log.old; fi"

# 3. 使用新路径启动服务
bash /data2/home_back/gujiaxin/work/starpainting/backend/services/digital-human-service/start_gradio.sh
```

## 📝 相关文件

- `app.py` - 已添加日志目录创建代码
- `restart_service.sh` - 已更新日志路径
- `start_gradio.sh` - 新建启动脚本
- `log/gradio.log` - 新的日志文件位置

## 📅 更新日期

2025-01-27

