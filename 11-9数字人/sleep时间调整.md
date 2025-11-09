# Sleep时间调整说明

## 📋 调整概述

已增加数字人服务中所有关键位置的sleep时间，以提高系统稳定性和避免资源竞争问题。

## 🔧 调整详情

### 1. fastapi_app.py

#### 1.1 进程监控检查间隔
- **文件位置**: `fastapi_app.py:650`
- **原值**: `check_interval = 5` (每5秒检查一次)
- **新值**: `check_interval = 10` (每10秒检查一次)
- **说明**: 减少进程健康检查频率，降低系统负载

#### 1.2 资源检查间隔
- **文件位置**: `fastapi_app.py:657`
- **原值**: `resource_check_interval = 30` (每30秒检查一次)
- **新值**: `resource_check_interval = 60` (每60秒检查一次)
- **说明**: 减少系统资源检查频率，降低开销

#### 1.3 模型初始化等待时间
- **文件位置**: `fastapi_app.py:804`
- **原值**: `time.sleep(20)` (20秒)
- **新值**: `time.sleep(30)` (30秒)
- **说明**: 增加模型加载后的等待时间，确保模型完全初始化

#### 1.4 任务执行前等待时间
- **文件位置**: `fastapi_app.py:1136`
- **原值**: `time.sleep(20)` (20秒)
- **新值**: `time.sleep(30)` (30秒)
- **说明**: 增加多进程队列初始化等待时间，避免_queue.Empty异常

#### 1.5 基础重试延迟
- **文件位置**: `fastapi_app.py:1092`
- **原值**: `retry_delay_base = 2` (2秒)
- **新值**: `retry_delay_base = 5` (5秒)
- **说明**: 增加重试延迟，给系统更多恢复时间

#### 1.6 任务状态检查间隔
- **文件位置**: `fastapi_app.py:1230`
- **原值**: `wait_interval = 1` (每秒检查一次)
- **新值**: `wait_interval = 2` (每2秒检查一次)
- **说明**: 减少任务状态检查频率，降低CPU使用率

### 2. HeyGem-Linux-Python-Hack/app.py

#### 2.1 服务初始化等待时间
- **文件位置**: `app.py:162`
- **原值**: `time.sleep(5)` (5秒)
- **新值**: `time.sleep(10)` (10秒)
- **说明**: 增加服务初始化等待时间

#### 2.2 等待服务初始化循环间隔
- **文件位置**: `app.py:173`
- **原值**: `time.sleep(1)` (1秒)
- **新值**: `time.sleep(2)` (2秒)
- **说明**: 增加等待服务初始化的循环间隔

### 3. HeyGem-Linux-Python-Hack/run.py

#### 3.1 任务初始化后等待时间
- **文件位置**: `run.py:185`
- **原值**: `time.sleep(10)` (10秒)
- **新值**: `time.sleep(15)` (15秒)
- **说明**: 增加任务初始化后的等待时间

## 📊 调整总结表

| 文件 | 位置 | 参数 | 原值 | 新值 | 增加倍数 |
|------|------|------|------|------|----------|
| fastapi_app.py | 650 | check_interval | 5秒 | 10秒 | 2x |
| fastapi_app.py | 657 | resource_check_interval | 30秒 | 60秒 | 2x |
| fastapi_app.py | 804 | 初始化等待 | 20秒 | 30秒 | 1.5x |
| fastapi_app.py | 1092 | retry_delay_base | 2秒 | 5秒 | 2.5x |
| fastapi_app.py | 1136 | work()前等待 | 20秒 | 30秒 | 1.5x |
| fastapi_app.py | 1230 | wait_interval | 1秒 | 2秒 | 2x |
| app.py | 162 | 服务初始化等待 | 5秒 | 10秒 | 2x |
| app.py | 173 | 等待初始化循环 | 1秒 | 2秒 | 2x |
| run.py | 185 | 任务初始化等待 | 10秒 | 15秒 | 1.5x |

## 🎯 调整目的

1. **提高系统稳定性**
   - 增加初始化等待时间，确保模型和队列完全加载
   - 减少资源竞争和初始化冲突

2. **降低系统负载**
   - 减少检查频率，降低CPU使用率
   - 减少不必要的资源检查开销

3. **避免异常情况**
   - 避免_queue.Empty异常
   - 减少进程初始化失败的概率
   - 给系统更多恢复时间

4. **改善重试机制**
   - 增加重试延迟，给系统更多时间恢复
   - 减少频繁重试导致的资源浪费

## ⚠️ 注意事项

1. **响应时间影响**
   - 初始化时间增加：从20秒增加到30秒
   - 任务启动延迟增加：从20秒增加到30秒
   - 状态检查间隔增加：从1秒增加到2秒

2. **性能影响**
   - 检查频率降低，CPU使用率可能降低
   - 系统响应可能稍慢，但更稳定

3. **监控影响**
   - 进程健康检查间隔增加，可能延迟发现问题
   - 需要平衡稳定性和监控及时性

## 🔍 验证方法

### 1. 检查修改是否正确
```bash
# 检查fastapi_app.py中的sleep时间
grep -n "time.sleep" fastapi_app.py
grep -n "check_interval\|wait_interval\|retry_delay" fastapi_app.py

# 检查app.py中的sleep时间
grep -n "time.sleep" HeyGem-Linux-Python-Hack/app.py

# 检查run.py中的sleep时间
grep -n "time.sleep" HeyGem-Linux-Python-Hack/run.py
```

### 2. 测试服务启动
```bash
# 重启服务，观察初始化时间
# 应该看到更长的等待时间
docker restart starpainting-digital-human-service-1
docker logs -f starpainting-digital-human-service-1
```

### 3. 监控系统资源
```bash
# 观察CPU使用率是否降低
# 观察内存使用是否更稳定
top
htop
```

## 📝 后续优化建议

1. **根据实际情况调整**
   - 如果系统稳定，可以适当减少sleep时间
   - 如果仍有问题，可以进一步增加sleep时间

2. **监控和日志**
   - 添加更详细的日志记录
   - 监控sleep时间对性能的影响

3. **动态调整**
   - 考虑根据系统负载动态调整sleep时间
   - 根据任务类型调整等待时间

## 📅 修改日期

2025-01-27

## 🔗 相关文件

- `fastapi_app.py` - 主要服务文件
- `HeyGem-Linux-Python-Hack/app.py` - Gradio应用
- `HeyGem-Linux-Python-Hack/run.py` - 运行脚本

