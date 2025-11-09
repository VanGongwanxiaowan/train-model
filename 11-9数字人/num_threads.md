# num_threads 配置说明

## 配置值说明

### num_threads 可以设置的值

- **0**: 单进程模式，不使用多线程 DataLoader
- **1**: 使用 1 个 worker 进程
- **2**: 使用 2 个 worker 进程
- **4**: 使用 4 个 worker 进程（原始配置）
- **8**: 使用 8 个 worker 进程（如果CPU核心数足够）

## 问题说明

### ❌ 我并没有修改成 4

**澄清**: 我一直是将 `num_threads` 修改为 **0**，不是 4！

- **原始配置**: `num_threads: 4` (这是项目原始配置)
- **我的修复**: `num_threads: 0` (这是我推荐的修复值)
- **当前配置**: `num_threads: 1` (这是您当前的配置)

## 为什么推荐使用 0？

### 多进程 CUDA 环境下的问题

1. **多进程死锁风险**
   - `num_threads > 0` 时，DataLoader 会启动多个 worker 进程
   - 在多进程 CUDA 环境下，这些 worker 进程会竞争 GPU 资源
   - 容易导致死锁，下游处理停滞
   - 队列被填满，触发队列阻塞错误

2. **队列阻塞问题**
   - 死锁导致下游处理停滞
   - 上游继续发送数据
   - 队列被填满
   - 最终触发队列阻塞错误

### 不同值的对比

| num_threads | 说明 | 优点 | 缺点 | 推荐场景 |
|-------------|------|------|------|----------|
| **0** | 单进程模式 | 稳定，不会死锁 | 可能稍慢 | **多进程CUDA环境** ✅ |
| **1** | 1个worker | 较稳定 | 可能有轻微竞争 | 轻度多进程环境 |
| **2-4** | 多个worker | 可能更快 | 容易死锁 | 单进程环境 |
| **8+** | 多个worker | 理论上最快 | 极易死锁 | 不适合多进程CUDA环境 |

## 当前环境分析

### 您的环境特点

1. **多进程 CUDA 环境**: 使用 multiprocessing + CUDA
2. **队列阻塞问题**: 频繁出现队列阻塞错误
3. **服务稳定性**: 需要高稳定性

### 推荐配置

**推荐值: `num_threads: 0`**

**原因**:
1. ✅ 避免多进程死锁
2. ✅ 解决队列阻塞问题
3. ✅ 提高服务稳定性
4. ⚠️ 可能稍慢，但更稳定

## 如果您想尝试其他值

### num_threads: 1

**可以尝试，但不推荐**:
- 相对稳定，但仍有轻微死锁风险
- 性能可能稍好于 0
- 如果出现问题，建议改回 0

### num_threads: 2-4

**不推荐**:
- 在多进程 CUDA 环境下容易死锁
- 可能导致队列阻塞问题
- 除非您确认环境是单进程的

## 修复建议

### 立即修复

将 `num_threads` 设置为 **0**:

```bash
# 在容器内执行
docker exec starpainting-digital-human-service-1 bash -c "sed -i 's/num_threads: [0-9]*/num_threads: 0/' /app/HeyGem-Linux-Python-Hack/landmark2face_wy/checkpoints/test/opt.txt"
```

### 验证修复

```bash
docker exec starpainting-digital-human-service-1 bash -c "cat /app/HeyGem-Linux-Python-Hack/landmark2face_wy/checkpoints/test/opt.txt | grep num_threads"
# 预期输出: num_threads: 0
```

## 总结

1. **我修改的是 0，不是 4** ✅
2. **原始配置是 4** (项目自带)
3. **推荐配置是 0** (避免多进程死锁)
4. **当前配置是 1** (您当前的配置)
5. **如果队列阻塞问题仍然存在，建议改回 0**

## 更新日期

2025-11-09

