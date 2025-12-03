# Tracebox 使用指南

Tracebox 是 Perfetto 项目的全能二进制工具，包含所有必要的跟踪服务和命令行客户端，为系统性能分析提供一站式解决方案。

## 快速开始

### 1. 基本使用

```bash
tracebox -t 10s -o trace.ptrace sched/sched_switch
```

### 2. 使用配置文件

创建配置文件 `trace.cfg`：

```bash
cat > trace.cfg << EOF
duration_ms: 10000
buffers {
  size_kb: 4096
  fill_policy: RING_BUFFER
}
data_sources {
  config {
    name: "linux.ftrace"
    ftrace_config {
      ftrace_events: "sched/sched_switch"
      ftrace_events: "sched/sched_wakeup"
    }
  }
}
EOF

tracebox -c trace.cfg --txt -o trace.ptrace
```

### 3. 查看跟踪结果

访问 [ui.perfetto.dev](https://ui.perfetto.dev) 上传 `.ptrace` 文件进行分析。

## 主要参数

- `-t <时间>`：跟踪持续时间（如 `10s`, `5m`, `1h`）
- `-o <文件>`：输出文件路径
- `-c <配置文件> --txt`：使用文本格式的配置文件
- `-b <大小>`：缓冲区大小（如 `64mb`）

## 常用事件类型

- `sched/sched_switch`：CPU 调度切换
- `sched/sched_wakeup`：进程唤醒
- `power/cpu_frequency`：CPU 频率变化
- `block/block_rq_issue`：磁盘 I/O 请求
- `net/net_dev_xmit`：网络数据包发送

## 官方文档

- [Perfetto 官方文档](https://perfetto.dev/docs/)
- [Trace 配置指南](https://perfetto.dev/docs/concepts/config)
- [Web UI 使用指南](https://perfetto.dev/docs/analysis/web-ui)
- [命令行工具参考](https://perfetto.dev/docs/concepts/cmdline)

## 其他功能

### 查询系统状态

```bash
tracebox --query
```

### 后台运行

```bash
tracebox -d -t 60s -o background_trace.ptrace
```

### 使用系统套接字

```bash
tracebox --system-sockets -t 10s -o system_trace.ptrace
```

### 手动模式

```bash
# 启动跟踪服务
tracebox traced

# 查看帮助
tracebox perfetto --help
```

### 简单配置示例

```bash
# 跟踪 30 秒的 CPU 调度和频率信息
tracebox -t 30s -o cpu_trace.ptrace \
  sched/sched_switch \
  power/cpu_frequency \
  power/cpu_idle

# 跟踪 I/O 性能
tracebox -t 30s -o io_trace.ptrace \
  block/block_rq_issue \
  block/block_rq_complete \
  net/net_dev_xmit
```