# 软件安装指南

本指南介绍 aBMC 软件包的下载方式、版本目录结构，以及 BMC 端与子板端的安装与升级步骤。

## 下载地址

资料包[下载地址](https://pan.baidu.com/s/1uPwUVfheglaChoFf7bUY-A?pwd=xfiy)（提取码：`xfiy`）

### 目录结构

aBMC 目录按版本划分为多个子目录，版本号由大到小排列，最靠前的为最新版本。

```text
aBMC
├── v3.2.0
├── v3.1.3
└── v2.9.4
```

以 v3.2.0 为例，版本目录中包含以下文件：

| 文件名 | 说明 |
| :--- | :--- |
| `firefly-abmc-server_3.2.0-1791956975_arm64.deb` | 服务端主程序 |
| `firefly-abmc-tools_1.1.0_arm64.deb` | 工具集 |
| `firefly-abmc_3.2.0-1791956975_all.deb` | 元包，用于一次性安装服务端与工具集 |
| `firefly-abmc-client_1.0.1_all.deb` | 子板依赖包 |
| `readme_zh.txt` / `readme_en.txt` | 安装命令与更新日志说明 |

## 安装与升级

安装或升级 aBMC 时，BMC 与子板均需联网（可访问软件源），以便 `apt` 解析和安装依赖包。

### BMC 端 [step]

将以下 deb 包上传到 BMC：

- `firefly-abmc-server*.deb`
- `firefly-abmc-tool*.deb`
- `firefly-abmc_*all.deb`

登录 BMC，进入 deb 包所在目录后执行：

```bash
sudo apt install -y ./firefly-abmc-server*.deb ./firefly-abmc-tool*.deb ./firefly-abmc_*all.deb
```

### 子板端 [step]

将以下 deb 包上传到子板：

- `firefly-abmc-client*.deb`

登录子板，进入 deb 包所在目录后执行：

```bash
sudo apt install -y ./firefly-abmc-client*.deb
```

### 验证 [step]

登录 BMC 后执行以下命令，查看 aBMC 服务运行状态：

```bash
systemctl status aBMC
```

服务状态为 `active (running)`，表示安装或升级成功。之后即可登录 aBMC Web 管理页面，具体方法参见[登录指南](guide.md)。
