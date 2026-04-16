# WHUT 校园网自动登录 + 网速叠加工具

> 武汉理工大学校园网自动登录保活脚本，支持多线路叠加提速

校园网认证 (Campus Network Authentication)
网速叠加 (Bandwidth Aggregation / Load Balancing)

## 📖 项目简介

这是一个为武汉理工大学(WHUT)同学设计的校园网自动登录工具，主要解决两个问题：

1. **自动登录保活**：断线后自动重新登录，无需手动操作
2. **网速叠加**：通过多线路认证，实现带宽叠加（校园网允许一个账号登录3台设备）

## ✨ 功能特性

- ✅ 自动检测网络状态
- ✅ 断线自动重连
- ✅ 支持后台运行
- ✅ 开机自启动
- ✅ 多线路网速叠加
- ✅ 详细日志记录


## 📋 使用场景

### 场景一：单设备自动登录
在电脑或服务器上运行脚本，保持校园网持续在线，断线自动重连。

### 场景二：多线路网速叠加（进阶）
通过路由器双WAN口 + 多台设备，实现网速叠加，获得更快的网络速度。

## 🚀 快速开始

### 环境要求

- Python 3.6 或更高版本
- Linux 系统（推荐 Debian/Ubuntu）
- 校园网环境（武汉理工大学）

### 参考来源

本项目基于以下开源项目改进：
- 原始项目：[lxidea/whut-online-keeper](https://github.com/lxidea/whut-online-keeper)

### 安装步骤

#### 1. 安装 Python 环境

```bash
# 更新软件包列表
sudo apt update

# 安装 Python3 和 pip
sudo apt install python3 python3-pip -y

# 安装 nano 编辑器（方便修改文件）
sudo apt install nano -y

> 💡 **nano 编辑器使用方法：**
> - **保存**：按 `Ctrl + O`，然后按 `Enter` 确认
> - **退出**：按 `Ctrl + X`
> - **移动光标**：使用键盘方向键 ↑ ↓ ← →

# 验证安装成功
python3 --version
```

#### 2. 安装依赖

```bash
pip3 install requests
```

#### 3. 上传并配置py文件

**上传py文件到服务器**

```bash
# 进入你的个人目录
cd /home/你的用户名

# 将 wut-login.py 文件上传到此目录
# 可以使用 SCP、SFTP 或其他文件传输工具
```

**修改脚本文件（简单）**

打开 `wut-login.py` 文件，找到第 36-38 行，填入你的账号密码：

```python
userid = "你的学号"
passwd = "你的密码"
interval = 3600  # 检测间隔（秒），默认3600秒
```



#### 4. 运行py文件

**前台运行（测试用）**

```bash
python3 wut-login.py
```

**后台运行py文件（推荐）**

```bash
# 进入py文件所在目录
cd /home/你的用户名

# 后台运行
nohup python3 wut-login.py > wut-login.log 2>&1 &
```

参数说明：
- `nohup` - 后台运行，关闭终端也不会停止
- `> wut-login.log` - 日志输出到文件
- `2>&1 &` - 错误信息也写入日志，后台静默运行

## 📊 查看日志和状态

### 实时查看日志

```bash
tail -f /home/你的用户名/wut-login.log
```

### 查看py文件进程是否运行

```bash
ps aux | grep wut-login.py
```

### 停止py文件运行

```bash
pkill -f "wut-login.py"
```

## ⚙️ 开机自启动

### 配置开机自启动py文件

```bash
# 编辑 rc.local 文件
sudo nano /etc/rc.local
```

添加以下内容：

```bash
#!/bin/sh
( sleep 30
cd /home/你的用户名 && nohup python3 wut-login.py > wut-login.log 2>&1 &
) &
exit 0
```

> 💡 **注意**：将 `/home/你的用户名` 替换为实际的路径

### 赋予执行权限

```bash
sudo chmod +x /etc/rc.local
```

## 🌐 多线路网速叠加（进阶教程）

> ⚠️ 此方案需要特定硬件支持，适合有路由器配置经验的用户

### 硬件要求

- 方式一：支持双WAN口的路由器（如 TP-LINK R384G）
- 方式二：采用单网口内部虚拟WAN口
- 使用一直开机的设备（NAS或者树莓派装两个Debian虚拟机）


### 原理说明

校园网允许一个账号同时登录3台设备。通过路由器双WAN口分别连接不同的校园网面板，每台设备走不同的WAN口认证，最后在路由器端进行带宽叠加。

### 配置步骤

#### 1. 路由器设置

- **双WAN口模式**：两个WAN口都插入校园网面板
- **修改MAC地址**：两个WAN口必须使用不同的MAC地址
- **策略路由**：
  - 设备A → WAN1
  - 设备B → WAN2
- **负载均衡**：选择"带宽均衡"模式，将WAN1和WAN2的带宽设置为相同值

#### 2. 设备配置

在每台设备上重复"快速开始"的安装步骤：

- 虚拟机A：上传并运行py文件，走WAN1认证
- 虚拟机B：上传并运行py文件，走WAN2认证


#### 3. 验证叠加效果

通过测速网站测试网速，应该能看到接近单线路2倍的速度。

### 网络拓扑图

```
校园网面板1 ──→ WAN1 ──→ 路由器 ──→ 设备A（运行py文件）
                                    ↓
校园网面板2 ──→ WAN2 ──→ 路由器 ──→ 设备B（运行py文件）
                                    ↓
                              带宽叠加输出
```

## ⚠️ 免责声明

本工具仅供学习和个人使用，请遵守武汉理工大学校园网使用规定。使用本工具产生的任何问题或后果，由使用者自行承担。

---

如果觉得这个工具对你有帮助，欢迎给个 ⭐ Star！
