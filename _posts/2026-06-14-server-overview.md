---
title: "实验室服务器使用简明手册"
toc: true
toc_label: "文章目录"
toc_icon: "list-ul"
lang: zh-CN
categories:
  - Blog
tags:
  - 服务器
  - HPC
  - Slurm
---

## 0. 服务器简介

本页面用于介绍 ShangGroup 两节点 CPU SLURM 集群的基本情况、登录方式、队列设置、作业提交方法和使用注意事项。

该集群为**纯 CPU 集群**，主要用于量子化学计算、AFQMC 计算、数值模拟、代码编译测试和科研数据分析等任务。当前两台节点均未检测到 GPU 设备，因此不适合直接运行 CUDA/GPU 深度学习训练任务。

> 本文档根据 2026-06-16 的服务器探查报告整理。若本文档与服务器实时状态不一致，请以管理员通知和实时命令输出为准。

## 1. 集群总览

| 项目 | 配置 |
|---|---|
| 集群名称 | `cluster` |
| 节点数量 | 2 台：`node01`、`node02` |
| 节点角色 | `node01` 为登录节点 / 控制节点，`node02` 为计算节点 |
| 总 CPU 核心数 | 512 物理核心，每节点 256 核 |
| CPU 型号 | 每节点双路 AMD EPYC 9755 128-Core Processor |
| 总内存 | 约 754 GB，每节点约 377 GiB |
| 操作系统 | Ubuntu 24.04.3 LTS |
| 调度系统 | Slurm 23.11.4 |
| 当前分区 | `debug`，包含 `node01`、`node02` |
| GPU | 无 GPU |
| 高速互联 | 无 Infiniband，节点间为 1 GbE 以太网互联 |
| 数据盘 | 每节点约 54.6 TB 数据盘，通过 NFS 互挂 |

## 2. 服务器组成

| 类型 | 数量 | 用途 |
|---|---:|---|
| 登录 / 控制节点 | 1 | 用户登录、代码编辑、任务提交、Slurm 控制服务 |
| CPU 计算节点 | 2 | CPU 并行计算、量子化学、AFQMC、数值模拟 |
| GPU 计算节点 | 0 | 当前无 GPU 节点 |
| 数据存储 | 每节点 1 块约 54.6 TB 数据盘 | 计算数据、输入输出文件、结果归档 |

## 3. 适合运行的任务

适合：

- CPU 并行程序；
- 量子化学与电子结构计算；
- AFQMC / QMC 类 CPU 任务；
- Python / C++ / Fortran 科研程序；
- 中小规模 MPI 或 OpenMP 程序；
- 需要较大内存的科学计算任务。

不适合或需要谨慎：

- CUDA / GPU 深度学习训练；
- 强依赖高速互联的多节点 MPI 通信任务；
- 大规模并行 I/O 或高频读写共享文件系统的任务；
- 长时间占满所有核心且没有检查点的任务。

## 4. 使用流程

1. 向服务器管理员申请账号；
2. 获取登录方式和初始密码；
3. 使用 SSH 登录 `node01`；
4. 在个人目录或数据目录中准备代码和输入文件；
5. 编写 Slurm 作业脚本；
6. 使用 `sbatch` 提交任务；
7. 使用 `squeue -u $USER` 查看任务状态；
8. 任务结束后检查 `.log` 和 `.err` 文件；
9. 整理结果并及时备份重要数据。

## 5. 重要原则

- 不要在登录节点直接运行大型计算任务；
- 大型任务应通过 Slurm 提交；
- 不要公开服务器密码、root 信息、真实公网 IP、跳板机信息等敏感内容；
- 不要占用他人目录或删除公共软件；
- 大文件读写应尽量减少跨节点 NFS 访问；
- 任务异常、长时间无输出或大量占用资源时，应及时检查并联系管理员。

## 6. 常用入口

- [服务器 CPU 节点]({{ site.baseurl }}/blog/cpu-nodes/)
- [GPU 节点情况]({{ site.baseurl }}/blog/gpu-nodes/)
- [存储与网络说明]({{ site.baseurl }}/blog/storage-network/)
- [服务器登录说明]({{ site.baseurl }}/blog/login-guide/)
- [Slurm 作业提交说明]({{ site.baseurl }}/blog/slurm-guide/)
- [常用软件环境]({{ site.baseurl }}/blog/software-environment/)
- [常见问题]({{ site.baseurl }}/blog/faq/)
