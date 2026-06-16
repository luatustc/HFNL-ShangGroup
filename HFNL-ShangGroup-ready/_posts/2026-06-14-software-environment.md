---
title: "常用软件环境"
toc: true
toc_label: "文章目录"
toc_icon: "list-ul"
lang: zh-CN
categories:
  - Blog
tags:
  - module
  - Python
  - MPI
  - 编译器
---

## 1. 系统环境

| 项目 | 当前情况 |
|---|---|
| 操作系统 | Ubuntu 24.04.3 LTS |
| 架构 | x86_64 |
| CPU 平台 | 双路 AMD EPYC 9755 |
| 调度系统 | Slurm 23.11.4 |
| GPU/CUDA | 当前无 GPU，未配置 CUDA 计算环境 |
| 运行方式 | 建议通过 Slurm 提交任务 |

## 2. module 使用

如果服务器配置了 Environment Modules 或 Lmod，可使用以下命令查看软件环境。

查看可用软件模块：

```bash
module avail
```

清空当前环境：

```bash
module purge
```

加载软件模块：

```bash
module load 模块名
```

查看已加载模块：

```bash
module list
```

如果 `module` 命令不可用，说明当前系统可能尚未配置模块系统，请使用系统自带软件、个人 Conda 环境，或联系管理员安装公共软件环境。

## 3. 建议管理员维护的软件清单

正式投入使用后，建议逐步整理并固定以下软件环境：

| 软件类型 | 软件名称 | 用途 | 建议说明 |
|---|---|---|---|
| 编译器 | GCC / Clang / oneAPI | C/C++/Fortran 编译 | 记录版本和加载方式 |
| MPI | OpenMPI / MPICH / Intel MPI | 并行计算 | 记录 `mpirun` / `srun` 用法 |
| Python | Miniconda / Anaconda | Python 科研环境 | 建议用户使用个人环境 |
| 数学库 | OpenBLAS / BLIS / MKL / FFTW | 高性能数学计算 | 按 CPU 平台测试性能 |
| HDF5 | HDF5 | 科学数据格式 | 并行 HDF5 需单独说明 |
| 量子化学 | PySCF / Quantum ESPRESSO 等 | 电子结构计算 | 根据课题组实际需求安装 |
| QMC/AFQMC | AFQMCLAB 等 | 量子蒙特卡洛计算 | 建议记录编译选项和依赖 |

## 4. Conda 环境建议

创建新环境：

```bash
conda create -n myenv python=3.10
```

激活环境：

```bash
conda activate myenv
```

安装常用包：

```bash
pip install numpy scipy matplotlib pandas
```

建议每个项目使用独立环境，避免不同项目之间依赖冲突。

## 5. 编译程序前建议记录

```bash
which gcc
which g++
which gfortran
which mpirun
which srun
gcc --version
mpirun --version 2>/dev/null || true
module list 2>&1 || true
```

这些信息有助于复现实验，也有助于排查编译和运行错误。

## 6. 不建议直接运行 GPU 相关命令

当前服务器无 GPU，因此一般不需要加载 CUDA，也不应在作业脚本中加入：

```bash
#SBATCH --gres=gpu:1
```

如果程序提示找不到 CUDA、NVIDIA driver 或 `nvidia-smi`，通常不是程序本身的问题，而是当前集群没有 GPU 资源。
