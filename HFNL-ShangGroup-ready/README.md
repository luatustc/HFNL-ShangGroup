# ShangGroup CPU 集群使用手册

本仓库用于维护 ShangGroup 两节点 CPU SLURM 集群使用说明网页，基于 GitHub Pages + Jekyll + Minimal Mistakes 主题构建。

## 使用方法

1. 解压本压缩包。
2. 将解压后文件夹中的全部内容上传到 GitHub 仓库根目录。
3. 打开 GitHub 仓库：`Settings → Pages`。
4. 设置：`Build and deployment → Source: Deploy from a branch → main / root`。
5. 等待 GitHub Pages 自动构建完成。

## 重要：仓库名与 baseurl

本模板默认按项目仓库 `HFNL-ShangGroup` 配置：

```yaml
baseurl: "/HFNL-ShangGroup"
```

如果你的仓库名是 `你的用户名.github.io`，请将 `_config.yml` 中的 `baseurl` 改成：

```yaml
baseurl: ""
```

## 内容结构

- 首页：自动显示最近文章。
- 实验室服务器使用简明手册：总体说明。
- CPU 节点：两台 AMD EPYC 9755 节点配置。
- GPU 节点情况：当前无 GPU，说明适用范围。
- 存储与网络说明：`/data1`、`/data2`、NFS 与 1 GbE 互联说明。
- 登录说明：SSH、Windows 工具、文件传输。
- Slurm 作业提交说明：常用命令、脚本模板、日志查看。
- 常用软件环境：系统、Slurm、编译与 Python 环境建议。
- 常见问题：面向新用户的排查说明。

## 安全提醒

本模板已主动移除或弱化真实公网/内网 IP、root 信息、跳板机细节、具体在线用户等不适合公开的信息。若部署到公开 GitHub Pages，请不要继续添加密码、root 信息、真实 IP、跳板机、VPN、管理员手机号等敏感内容。
