---
title: 创建Native集群
description: 本文介绍了如何在其他 VM 中创建和初始化 K3s 集群，以及为已有的 K3s 集群添加节点的操作步骤。
keywords:
  - k3s中文文档
  - k3s 中文文档
  - k3s中文
  - k3s 中文
  - k3s
  - k3s教程
  - k3s中国
  - rancher
  - k3s 中文教程
  - AutoK3s
  - 创建Native集群
---

## 概述

本文介绍了如何在一个能够运行主流操作系统（如 Ubuntu、Debian、Raspbian 等）的虚拟机（VM）中创建和初始化 K3s 集群，以及为已有的 K3s 集群添加节点的操作步骤。除此之外，本文还提供了在 VM 上运行 AutoK3s 的进阶操作指导，如配置私有镜像仓库和启用 UI 组件。

## 前置要求

### 虚拟机要求

提供一个运行主流操作系统（如 **Ubuntu、Debian、Raspbian** 等）的 VM，并为它们注册或设置`SSH密钥/密码`。

### 网络

VM 实例**至少**需要应用以下入站及出站规则：

```bash
Rule        Protocol    Port      Source             Description
InBound     TCP         22        ALL                SSH Connect Port
InBound     TCP         6443      K3s agent nodes    Kubernetes API
InBound     TCP         10250     K3s server & agent Kubelet
InBound     TCP         8999      K3s dashboard      (Optional) Required only for Dashboard UI
InBound     UDP         8472      K3s server & agent (Optional) Required only for Flannel VXLAN
InBound     TCP         2379,2380 K3s server nodes   (Optional) Required only for embedded ETCD
OutBound    ALL         ALL       ALL                Allow All
```

## UI 使用说明

接下来我们将基于 AutoK3s 本地 UI 介绍如何使用 AutoK3s 工具，在任意主机上创建和管理 K3s 集群。如果您想了解 CLI 的使用，请移步到 [CLI 使用说明](#cli-使用说明)

您可以通过[快速体验](/docs/k3s/autok3s/_index#快速体验)中的描述，通过 Docker 或者 CLI 启动本地 UI，打开浏览器，访问目标端口 `8080` 即可。

### 自定义参数创建

您可以在集群列表页点击 **Create** 按钮进入自定义参数页面进行更多参数的设置。

使用 Native 模式创建 K3s 集群的自定义参数配置分为三项，VM 配置、K3s集群配置、高级选项。接下来对每个配置项进行详细说明。

#### VM 配置

实例配置主要配置的内容为 VM 的连接信息。

![](/img/k3s/custom-create-cluster-instance-native.png)

**表 3：实例配置参数**

| 参数 | 说明 | 默认值
| :------------- | :----------------------- |:------------- 
| Master IPs | Master 主机 IP 列表 | 
| Worker IPs | Worker 主机 IP 列表 |
| SSH User | SSH 用户 | `root`
| SSH Port | SSH 端口 | `22`
| SSH Key Path | SSH 私钥目录 | `~/.ssh/id_rsa`
| SSH Key Passphrase | 如果您的私钥需要密码验证，请在这里输入密码 |
| SSH Password | 如果您使用Password连接到虚拟机，请输入密码 |
| SSH Agent Auth | 如果您配置 SSH 代理程序，可以开启此项配置 | false
| SSH Cert Path | 如果您配置了 ssh certificate，在这里您需要将 certificate path 传入进来，以保证 AutoK3s 可以通过ssh连接到远程虚拟机 |

#### K3s 参数配置

K3s 参数配置项主要对 K3s 集群进行设置，例如是否部署 HA 模式、K3s 安装版本等。

![](/img/k3s/custom-create-cluster-k3s-native.png)

**表 4：K3s 配置参数**

| 参数 | 说明 | 默认值
| :------------- | :------------------ |:------------- 
| K3s Channel | 用于获取 K3s 下载 URL 的通道。选项包括：`stable`, `latest`, `testing`。 | `stable`
| K3s Version | 安装的 K3s 版本，如果设置则覆盖 Channel 选项 |
| Cluster | 启用嵌入式 DB 高可用 K3s（即开启 `--cluster-init` 设置） | false
| Datastore | 指定 etcd、Mysql、Postgres 或 Sqlite（默认）数据源名称 |
| K3s Install Script | K3s 安装脚本，对于中国区用户，可以通过下拉菜单选择 `rancher-mirror` 脚本来安装 | `https://get.k3s.io`
| Master Extra Args | Master 节点额外参数设置，例如 `--no-deploy traefik` |
| Worker Extra Args | Worker 节点额外参数设置，例如 `--node-taint key=value:NoExecute` |
| Token | 用于将server或agent加入集群的共享secret，如果不设置，会自动生成一个Token |
| Manifests | 自动部署应用清单目录，这里支持设置某个manifest文件或者包含多个manifest文件的目录路径（仅支持一层目录），具体功能可查看[这里](http://docs.rancher.cn/docs/k3s/advanced/_index/#%E8%87%AA%E5%8A%A8%E9%83%A8%E7%BD%B2%E6%B8%85%E5%8D%95) |
| TLS Sans | 在 TLS 证书中添加其他主机名或 IP 作为主题备用名称，具体功能可查看[这里](https://docs.rancher.cn/docs/k3s/installation/install-options/server-config/_index#%E7%9B%91%E5%90%AC) |
| Registry | [私有镜像仓库配置](https://docs.rancher.cn/docs/k3s/installation/private-registry/_index) |

#### 高级选项

配置要开启的 UI 组件（kubernetes-dashboard/kube-explorer）。

![](/img/k3s/custom-create-cluster-additional-native.png)

**表 4：高级选项**

| 参数 | 说明 | 默认值
| :------------- | :------------------- |:------------- 
| UI | 开启的 UI 组件（Kubernetes Dashboard/kube-explorer） | 

### 集群模板

Native 模式不支持集群模板功能。

### 集群管理

Native 模式不支持集群列表管理功能，但您可以使用 Kubectl 功能操作和管理集群资源。

#### Kubectl

如果您想操作 K3s 集群数据，可以点击右上角 **Launch Kubectl** 按钮，在下拉框中选择要操作的集群后，便可以在 UI 控制台操作选中的集群了。

![](/img/k3s/launch-kubectl.png)

#### 开启 kube-explorer dashboard

您可以在创建集群时，通过 Additional Options 选择 explorer 选项开启 kube-explorer 功能。

![](/img/k3s/launch-kube-explorer.png)

也可以通过右侧下拉菜单中选择 Enable Explorer 功能来开启 kube-explorer。

![](/img/k3s/enable-kube-explorer.png)

开启后，在集群列表会增加 dashboard 跳转链接按钮，点击跳转链接便可以访问 kube-explorer dashboard 页面了。

![](/img/k3s/access-kube-explorer.png)

#### 关闭 kube-explorer dashboard

对于已经开启了 kube-explorer 功能的集群，可以在右侧下拉菜单中选择 Disable Explorer 功能来关闭 kube-explorer 服务。

![](/img/k3s/disable-kube-explorer.png)

## CLI 使用说明

更多参数请运行`autok3s <sub-command> --provider native --help`命令。

### 创建普通集群

运行以下命令，在 VM 上创建并启动创建一个名为 “myk3s”的集群，并为该集群配置 1 个 master 节点和 1 个 worker 节点。

```bash
autok3s -d create \
    --provider native \
    --name myk3s \
    --ssh-key-path <ssh-key-path> \
    --master-ips <master-ip-1> \
    --worker-ips <worker-ip-1>
```

### 创建高可用 K3s 集群

创建高可用集群的命令分为两种，取决于您选择使用的是内置的 etcd 还是外部数据库。

#### 嵌入式 etcd（k3s 版本 >= 1.19.1-k3s1)

运行以下命令，在 VM 上创建并启动创建了一个名为“myk3s”的高可用 K3s 集群。

```bash
autok3s -d create \
    --provider native \
    --name myk3s \
    --ssh-key-path <ssh-key-path> \
    --master-ips <master-ip-1,master-ip-2,master-ip-3> \
    --cluster
```

#### 外部数据库

在高可用模式下使用外部数据库，需要满足两个条件：

- master 节点的数量不小于 1。
- 需要提供外部数据库的存储路径。

所以在以下的代码示例中，我们通过`--master-ips <master-ip-1,master-ip-2>`指定 master 节点数量为 2，满足 master 节点的数量不小于 1 这个条件；且通过`--datastore "PATH"`指定外部数据库的存储路径，提供外部数据库的存储路径。

运行以下命令，在 VM 上创建并启动创建了一个名为“myk3s”的高可用 K3s 集群：

```bash
autok3s -d create \
    --provider native \
    --name myk3s \
    --ssh-key-path <ssh-key-path> \
    --master-ips <master-ip-1,master-ip-2> \
    --datastore "mysql://<user>:<password>@tcp(<ip>:<port>)/<db>"
```

### 添加 K3s 节点

请使用`autok3s join`命令为已有集群添加 K3s 节点。

#### 普通集群

运行以下命令，为“myk3s”集群添加 2 个 worker 节点。

```bash
autok3s -d join \
    --provider native \
    --name myk3s \
    --ssh-key-path <ssh-key-path> \
    --ip <existing-k3s-server-public-ip> \
    --worker-ips <worker-ip-2,worker-ip-3>
```

#### 高可用 K3s 集群

添加 K3s 节点的命令分为两种，取决于您选择使用的是内置的 etcd 还是外部数据库。

##### 嵌入式 etcd

运行以下命令，为高可用集群（嵌入式 etcd: k3s 版本 >= 1.19.1-k3s1）“myk3s”集群添加 2 个 master 节点。

```bash
autok3s -d join \
    --provider native \
    --name myk3s \
    --ssh-key-path <ssh-key-path> \
    --ip <existing-k3s-server-public-ip> \
    --master-ips <master-ip-2,master-ip-3>
```

##### 外部数据库

运行以下命令，为高可用集群（外部数据库）“myk3s”集群添加 2 个 master 节点。值得注意的是，添加节点时需要指定参数`--datastore`，提供外部数据库的存储路径。

```bash
autok3s -d join \
    --provider native \
    --name myk3s \
    --ssh-key-path <ssh-key-path> \
    --ip <existing-k3s-server-public-ip> \
    --master-ips <master-ip-2,master-ip-3> \
    --datastore "mysql://<user>:<password>@tcp(<ip>:<port>)/<db>"
```

### Kubectl

群创建完成后，`autok3s` 会自动合并 `kubeconfig` 文件。

```bash
autok3s kubectl config use-context myk3s
autok3s kubectl <sub-commands> <flags>
```

在多个集群的场景下，可以通过切换上下文来完成对不同集群的访问。

```bash
autok3s kubectl config get-contexts
autok3s kubectl config use-context <context>
```

### 进阶使用

AutoK3s 集成了一些与当前 provider 有关的高级组件，例如私有镜像仓库和 UI。

#### 配置私有镜像仓库

在运行`autok3s create`或`autok3s join`时，通过传递`--registry /etc/autok3s/registries.yaml`参数以使用私有镜像仓库，例如：

```bash
autok3s -d create \
    --provider native \
    --name myk3s \
    --ssh-key-path <ssh-key-path> \
    --master-ips <master-ip-1> \
    --worker-ips <worker-ip-1,worker-ip-2> \
    --registry /etc/autok3s/registries.yaml
```

使用私有镜像仓库的配置请参考以下内容，如果您的私有镜像仓库需要 TLS 认证，`autok3s`会从本地读取相关的 TLS 文件并自动上传到远程服务器中完成配置，您只需要完善`registry.yaml`即可。

```bash
mirrors:
  docker.io:
    endpoint:
      - "https://mycustomreg.com:5000"
configs:
  "mycustomreg:5000":
    auth:
      username: xxxxxx # this is the registry username
      password: xxxxxx # this is the registry password
    tls:
      cert_file: # path to the cert file used in the registry
      key_file:  # path to the key file used in the registry
      ca_file:   # path to the ca file used in the registry
```

#### 启用 UI 组件

AutoK3s 支持两种 UI 组件，包括 [kubernetes-dashboard](https://github.com/kubernetes/dashboard) 和 [kube-explorer](https://github.com/cnrancher/kube-explorer)

##### 开启 kubernetes dashboard

```bash
autok3s -d create -p native \
    ... \
    --enable dashboard
```

访问 Token 等设置请参考 [此文档](https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md) 。

##### 开启 kube-explorer

```bash
autok3s explorer --context <context> --port 9999
```
您可以通过 http://127.0.0.1:9999 访问 kube-explorer dashboard 页面。
