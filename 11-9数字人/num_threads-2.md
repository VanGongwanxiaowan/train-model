# num_threads 配置详细说明

## 重要澄清

### ❌ 我并没有修改成 4

**澄清**: 
- **我一直在修改成 0**，不是 4！
- **原始配置是 4**（这是项目自带的配置）
- **当前配置是 1**（这可能是您自己改的，或者其他原因）

## num_threads 可以设置的值

### 可选值范围

- **0**: 单进程模式，不使用多线程 DataLoader（推荐）
- **1**: 使用 1 个 worker 进程
- **2**: 使用 2 个 worker 进程
- **4**: 使用 4 个 worker 进程（原始配置）
- **8**: 使用 8 个 worker 进程（如果CPU核心数足够）
- **16+**: 更多 worker 进程（通常不推荐）

## 不同值的详细对比

| num_threads | 说明 | 优点 | 缺点 | 推荐场景 |
|-------------|------|------|------|----------|
| **0** | 单进程模式 | ✅ 最稳定，不会死锁<br>✅ 避免队列阻塞<br>✅ 适合多进程CUDA环境 | ⚠️ 可能稍慢 | **多进程CUDA环境** ✅ **推荐** |
| **1** | 1个worker | ✅ 较稳定<br>✅ 性能可能稍好 | ⚠️ 仍有轻微死锁风险 | 可以尝试，但不推荐 |
| **2** | 2个worker | ✅ 性能可能更好 | ❌ 容易死锁 | 不推荐多进程CUDA环境 |
| **4** | 4个worker | ✅ 理论上最快 | ❌ 极易死锁<br>❌ 导致队列阻塞 | **不推荐**（原始配置，有问题） |
| **8+** | 多个worker | ✅ 理论上最快 | ❌ 极易死锁 | 不适合多进程CUDA环境 |

## 为什么推荐使用 0？

### 技术原因

1. **多进程 CUDA 环境下的死锁问题**
   - 当 `num_threads > 0` 时，DataLoader 会启动多个 worker 进程
   - 在多进程 CUDA 环境下，这些 worker 进程会竞争 GPU 资源
   - 容易导致死锁，下游处理停滞
   - 队列被填满，触发队列阻塞错误

2. **队列阻塞问题**
   - 从日志可以看到：上游快速发送数据，但下游处理停滞
   - 这是因为多进程死锁导致的
   - `num_threads: 0` 可以避免这个问题

3. **服务稳定性**
   - 单进程模式虽然可能稍慢，但在多进程 CUDA 环境下更稳定
   - 避免死锁和资源竞争
   - 提高服务可靠性

## 当前配置分析

### 您的当前配置

- **当前值**: `num_threads: 1`
- **原始值**: `num_threads: 4`（项目自带）
- **推荐值**: `num_threads: 0`（我推荐的）

### 为什么当前是 1？

可能的原因：
1. 您自己手动改成了 1
2. 有其他脚本或配置修改了它
3. 服务启动时动态修改了配置

## 修复建议

### 推荐配置: `num_threads: 0`

**原因**:
1. ✅ 避免多进程死锁
2. ✅ 解决队列阻塞问题
3. ✅ 提高服务稳定性
4. ⚠️ 可能稍慢，但更稳定

### 如果您想尝试其他值

#### num_threads: 1

**可以尝试，但不推荐**:
- 相对稳定，但仍有轻微死锁风险
- 性能可能稍好于 0
- 如果出现问题，建议改回 0

#### num_threads: 2-4

**不推荐**:
- 在多进程 CUDA 环境下容易死锁
- 可能导致队列阻塞问题
- 除非您确认环境是单进程的

## 如何修改

### 方法 1: 手动修改

编辑 `opt.txt` 文件：
```bash
# 在容器内执行
docker exec starpainting-digital-human-service-1 bash -c "sed -i 's/num_threads: [0-9]*/num_threads: 0/' /app/HeyGem-Linux-Python-Hack/landmark2face_wy/checkpoints/test/opt.txt"
```

### 方法 2: 使用修复脚本

使用 `fix_num_threads.sh` 脚本：
```bash
docker exec starpainting-digital-human-service-1 bash -c "/app/HeyGem-Linux-Python-Hack/fix_num_threads.sh"
```

### 方法 3: 直接编辑文件

在 IDE 中直接编辑 `opt.txt` 文件，将 `num_threads: 1` 改为 `num_threads: 0`

## 验证配置

```bash
docker exec starpainting-digital-human-service-1 bash -c "cat /app/HeyGem-Linux-Python-Hack/landmark2face_wy/checkpoints/test/opt.txt | grep num_threads"
# 预期输出: num_threads: 0
```

## 总结

1. **我修改的是 0，不是 4** ✅
2. **原始配置是 4**（项目自带，有问题）
3. **推荐配置是 0**（避免多进程死锁）
4. **当前配置是 1**（您当前的配置）
5. **如果队列阻塞问题仍然存在，建议改回 0**

## 相关文件

- `num_threads配置说明.md` - 简要说明
- `fix_num_threads.sh` - 自动修复脚本
- `队列阻塞问题最终解决方案_2025-11-09.md` - 解决方案

## 更新日期

2025-11-09

