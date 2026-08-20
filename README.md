# Reasonix CLI for Android Termux (arm64)

本项目提供 DeepSeek-Reasonix CLI 在 Android Termux 环境下的快速部署、源码构建及使用教程。

* **项目原仓库**：https://github.com/esengine/DeepSeek-Reasonix
* **运行平台**：Android Termux (arm64)

---

## ⚠️ 核心环境须知

1. **`/tmp` 路径与权限依赖**：Termux 默认缺少标准 `/tmp` 目录。首次 `setup` 初始化需要路径映射，必须严格使用 `proot -b ~/tmp:/tmp` 执行。
2. **外部存储限制**：严禁直接 `cd /storage/emulated/0` 执行命令；如需处理手机存储中的项目，请传入绝对路径参数。

---

## ⚡ 快速安装（免编译，推荐）

复制以下整段命令直接在 Termux 终端执行即可一键完成部署：

```bash
# 1. 安装运行依赖与工具
pkg update -y && pkg install proot curl unzip -y

# 2. 下载并解压 Release 二进制文件
cd ~
curl -L -o reasonix.zip https://github.com/zzgs219G/reasonix-termux/releases/download/v1/reasonix.zip
unzip -o reasonix.zip
chmod +x reasonix
mv reasonix $PREFIX/bin/
rm -f reasonix.zip
mkdir -p ~/tmp

# 3. 写入 alias 别名
cat << 'EOF' >> ~/.bashrc
alias reasonix="proot -b ~/tmp:/tmp -b /storage/emulated/0:/storage/emulated/0 reasonix"
EOF

# 4. 重载配置
source ~/.bashrc

# 5. 验证安装
reasonix --version
```

---

## 🛠️ 源码编译安装（可选）

如需自行从源码构建：

```bash
# 1. 准备环境与安装编译工具
cd ~
pkg update -y
pkg install git golang make proot -y

# 2. 克隆源码并配置 Go 镜像
git clone [https://github.com/esengine/DeepSeek-Reasonix.git](https://github.com/esengine/DeepSeek-Reasonix.git)
cd DeepSeek-Reasonix
go env -w GOTOOLCHAIN=local
go env -w GOPROXY=[https://goproxy.cn](https://goproxy.cn),direct

# 3. 编译二进制并全局部署
go build -o reasonix ./cmd/reasonix
chmod +x reasonix
cp reasonix $PREFIX/bin/
mkdir -p ~/tmp

# 4. 写入 alias 别名
cat << 'EOF' >> ~/.bashrc

# Reasonix PROOT Wrapper
alias reasonix="proot -b ~/tmp:/tmp -b /storage/emulated/0:/storage/emulated/0 reasonix"
EOF

# 5. 重载配置并验证
source ~/.bashrc
reasonix --version
```

---

## 🚀 启动与使用教程

### 1. 首次初始化配置
配置 API Key 与 Provider（必须带 proot 映射以解决权限与 `/tmp` 缺失问题）：
```bash
proot -b ~/tmp:/tmp reasonix setup
```

### 2. 日常使用

* **Termux 家目录内部项目（先进入项目目录）：**
  ```bash
  cd ~/你的项目路径
  reasonix --permission-mode yolo
  ```

* **手机外部存储项目（传入绝对路径，严禁 cd 进 storage 目录）：**
  ```bash
  reasonix --permission-mode yolo --project "/storage/emulated/0/常用/工作台/你的项目"
  ```

---

## 🗑️ 卸载与清理

```bash
rm -f $PREFIX/bin/reasonix
rm -rf ~/DeepSeek-Reasonix
rm -rf ~/tmp
# 编辑 ~/.bashrc 删除对应的 alias reasonix 行即可
```
