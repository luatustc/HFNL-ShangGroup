---
title: "Slurm 作业提交说明"
toc: true
toc_label: "文章目录"
toc_icon: "list-ul"
lang: zh-CN
categories:
  - Blog
tags:
  - Slurm
  - sbatch
  - squeue
---

## 1. Slurm 基本信息

当前集群使用 Slurm 23.11.4 作为作业调度系统。Slurm 控制服务运行在 `node01`，当前分区为 `debug`，包含 `node01` 和 `node02` 两台节点。

| 项目 | 当前配置 |
|---|---|
| Slurm 版本 | 23.11.4 |
| 控制节点 | `node01` |
| 调度策略 | backfill |
| 资源选择 | cons_tres / CR_CORE |
| 计费数据库 | 已配置 slurmdbd |
| 认证 | Munge |
| 默认分区 | `debug` |
| 分区节点 | `node01`, `node02` |
| 分区总核心 | 512 |
| 分区总内存 | 约 754 GB |
| GPU 资源 | 无 |

## 2. 常用命令

| 命令 | 作用 |
|---|---|
| `sinfo` | 查看分区和节点状态 |
| `squeue -u $USER` | 查看自己的任务 |
| `sbatch job.sh` | 提交任务 |
| `scancel JOBID` | 取消任务 |
| `scontrol show job JOBID` | 查看任务详细信息 |
| `scontrol show node` | 查看节点详细信息 |
| `sacct -j JOBID` | 查看历史任务记录 |

## 3. 最小测试脚本

文件名：`test_slurm.sh`

```bash
#!/bin/bash
#SBATCH -J test_slurm
#SBATCH -p debug
#SBATCH -N 1
#SBATCH -n 4
#SBATCH -o %j.log
#SBATCH -e %j.err

set -e

echo "Job ID: $SLURM_JOB_ID"
echo "Partition: $SLURM_JOB_PARTITION"
echo "Node list: $SLURM_JOB_NODELIST"
echo "Submit dir: $SLURM_SUBMIT_DIR"
echo "Start time: $(date)"

hostname
srun hostname

echo "End time: $(date)"
```

提交：

```bash
sbatch test_slurm.sh
```

查看：

```bash
squeue -u $USER
```

## 4. 单节点 CPU 任务脚本

文件名：`cpu_job.sh`

```bash
#!/bin/bash
#SBATCH -J cpu_job
#SBATCH -p debug
#SBATCH -N 1
#SBATCH -n 64
#SBATCH -o %j.log
#SBATCH -e %j.err

set -e

cd "$SLURM_SUBMIT_DIR"

echo "Job ID: $SLURM_JOB_ID"
echo "Node list: $SLURM_JOB_NODELIST"
echo "Start time: $(date)"

# 在这里替换成你的程序命令
# 例如：srun ./my_program input.dat
srun hostname

echo "End time: $(date)"
```

## 5. 多节点测试脚本

文件名：`two_node_test.sh`

```bash
#!/bin/bash
#SBATCH -J two_node_test
#SBATCH -p debug
#SBATCH -N 2
#SBATCH -n 8
#SBATCH -o %j.log
#SBATCH -e %j.err

set -e

cd "$SLURM_SUBMIT_DIR"

echo "Job ID: $SLURM_JOB_ID"
echo "Node list: $SLURM_JOB_NODELIST"
srun hostname
```

如果日志中同时出现 `node01` 和 `node02`，说明两个节点均可被 Slurm 调用。

## 6. 日志文件

如果脚本中写了：

```bash
#SBATCH -o %j.log
#SBATCH -e %j.err
```

任务运行后会生成类似：

```text
12345.log
12345.err
```

其中 `.log` 通常是标准输出，`.err` 通常是错误输出。

## 7. 常见任务状态

| 状态 | 含义 |
|---|---|
| `PD` | Pending，等待运行 |
| `R` | Running，正在运行 |
| `CG` | Completing，正在结束 |
| `CD` | Completed，已完成 |
| `F` | Failed，失败 |
| `TO` | Timeout，超时 |

## 8. 任务异常时建议截图

向管理员反馈时，建议提供以下信息：

```bash
squeue -u $USER
scontrol show job JOBID
cat JOBID.err
cat JOBID.log
sacct -j JOBID --format=JobID,JobName,State,ExitCode,Elapsed,AllocCPUS,ReqMem,MaxRSS
```

截图中应包含作业 ID、队列名、状态、Reason、错误日志等信息。

## 9. 使用建议

当前 `debug` 分区时间限制为 UNLIMITED。虽然系统允许长任务运行，但建议用户仍然遵守以下原则：

- 新程序先用小核数、小输入测试；
- 确认日志正常后再扩大规模；
- 长任务尽量写 checkpoint；
- 不要长期占满全部节点；
- 若需要长期大规模任务，建议提前与管理员或课题负责人沟通。
