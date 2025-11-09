# BASE_PATH 配置说明

## 📋 配置概述

`BASE_PATH` 是数字人服务内部使用的配置路径，用于存储任务相关的数据。

## 🔧 当前配置

### 配置位置
- **文件**: `fastapi_app.py`
- **行号**: 第75行
- **当前值**: `/data2/home_back/gujiaxin/work/batchshort1/assrt/human`

### 配置代码
```python
# 内部配置路径
BASE_PATH = "/data2/home_back/gujiaxin/work/batchshort1/assrt/human"
```

## 📁 路径用途

### 1. 服务启动时检查
- 服务启动时会检查 `BASE_PATH` 目录是否存在
- 如果不存在，会自动创建
- 如果创建失败，会记录警告但继续启动服务

### 2. 日志记录
- 服务启动时会记录 `BASE_PATH` 目录的状态
- 日志示例：
  ```
  [INFO] BASE_PATH 目录已存在: /data2/home_back/gujiaxin/work/batchshort1/assrt/human
  ```
  或
  ```
  [INFO] 创建 BASE_PATH 目录: /data2/home_back/gujiaxin/work/batchshort1/assrt/human
  ```

## 🐳 Docker 配置

### 卷挂载
在 Docker 启动命令中，需要挂载数据目录：

```bash
-v /data2/home_back/gujiaxin/work/batchshort1/assrt:/data2/home_back/gujiaxin/work/batchshort1/assrt
```

### 挂载说明
- **宿主机路径**: `/data2/home_back/gujiaxin/work/batchshort1/assrt`
- **容器内路径**: `/data2/home_back/gujiaxin/work/batchshort1/assrt`
- **用途**: 确保容器内可以访问 `BASE_PATH` 目录

## ✅ 验证配置

### 1. 检查目录是否存在
```bash
# 在容器内检查
docker exec starpainting-digital-human-service-1 bash -c "ls -ld /data2/home_back/gujiaxin/work/batchshort1/assrt/human"

# 在宿主机检查
ls -ld /data2/home_back/gujiaxin/work/batchshort1/assrt/human
```

### 2. 检查目录权限
```bash
# 在容器内检查
docker exec starpainting-digital-human-service-1 bash -c "stat /data2/home_back/gujiaxin/work/batchshort1/assrt/human"
```

### 3. 检查服务日志
```bash
# 查看服务启动日志
docker logs starpainting-digital-human-service-1 | grep "BASE_PATH"
```

## 🔄 修改配置

### 如果需要修改 BASE_PATH

1. **修改 fastapi_app.py**
   ```python
   # 修改第75行
   BASE_PATH = "/新的/路径/human"
   ```

2. **更新 Docker 卷挂载**
   ```bash
   # 修改 Docker 启动命令中的卷挂载
   -v /新的/路径/assrt:/新的/路径/assrt
   ```

3. **重启服务**
   ```bash
   docker restart starpainting-digital-human-service-1
   ```

## ⚠️ 注意事项

1. **路径必须存在**: 虽然服务会自动创建目录，但建议确保父目录存在
2. **权限问题**: 确保容器有权限访问和创建目录
3. **卷挂载**: 必须正确挂载数据目录，否则容器内无法访问路径
4. **路径一致性**: 确保宿主机和容器内的路径一致

## 📊 当前状态

根据日志记录，`BASE_PATH` 目录状态：
- ✅ 目录已存在（从日志可以看出）
- ✅ 服务启动时正常检查
- ✅ 日志正常记录

## 🔍 相关文件

- `fastapi_app.py` - 主服务文件，包含 BASE_PATH 定义
- `DOCKER_SETUP.md` - Docker 配置说明
- 服务日志 - 记录 BASE_PATH 检查状态

## 📝 日志示例

### 目录已存在
```
[2025-11-09 12:43:01] [fastapi_app.py[line:825]] [INFO] [BASE_PATH 目录已存在: /data2/home_back/gujiaxin/work/batchshort1/assrt/human]
```

### 创建目录
```
[2025-11-09 10:29:06] [fastapi_app.py[line:823]] [INFO] [创建 BASE_PATH 目录: /data2/home_back/gujiaxin/work/batchshort1/assrt/human]
```

### 创建失败
```
[WARNING] 创建 BASE_PATH 目录失败: {错误信息}，继续启动服务
```

## 📅 更新日期

2025-01-27

