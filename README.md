# swap-manager
Linux自动化交换空间管理工具​​

## 功能特性

- ✅ **智能交换文件管理** - 创建、启用、禁用和删除交换文件
- ✅ **Btrfs 文件系统优化** - 自动处理 CoW（写时复制）和压缩设置
- ✅ **自定义大小支持** - 支持 GB 和 MB 单位的灵活大小设置
- ✅ **开机自启动配置** - 自动添加到 `/etc/fstab` 实现开机挂载
- ✅ **空洞问题修复** - 针对 Btrfs 文件系统的交换文件空洞问题进行特殊处理
- ✅ **交互式操作** - 提供详细的状态反馈和确认提示
- ✅ **安全权限管理** - 自动检查 root 权限，确保操作安全

## 系统要求

- **操作系统**: Linux（已在 Manjaro/Arch 测试）
- **文件系统**: 支持所有文件系统，特别优化 Btrfs
- **权限**: 需要 root 权限执行
- **内核**: Linux 5.10+ 推荐

## 安装使用

### 1. 下载脚本
```bash
wget https://github.com/Kalpa0448/swap-manager/releases/download/1.0/swap-manager.sh
chmod +x swap-manager.sh
```

### 2. 基本使用方法
```bash
# 创建默认大小（4GB）交换文件
sudo ./swap-manager.sh create

# 创建指定大小的交换文件
sudo ./swap-manager.sh create 8G     # 8GB
sudo ./swap-manager.sh create 2G     # 2GB
sudo ./swap-manager.sh create 1024M  # 1024MB

# 删除交换文件
sudo ./swap-manager.sh remove

# 查看当前状态
sudo ./swap-manager.sh status

# 显示帮助信息
sudo ./swap-manager.sh help
```

## 命令详解

### create [size]
创建并启用交换文件
- `size`: 可选参数，指定交换文件大小
- 格式: 数字+G/M（如 8G, 2048M）
- 默认: 4G（当不指定大小时）

### remove
停用并完全删除交换文件
- 包括从 `/etc/fstab` 中移除挂载项
- 删除物理交换文件

### status
显示当前系统交换空间状态
- 激活的交换文件信息
- 内存使用情况
- 交换文件配置状态

### help
显示详细的帮助信息和使用示例

## 配置自定义

编辑脚本开头的变量来自定义行为：

```bash
# 默认配置参数
SWAP_SIZE="4G"                    # 默认交换文件大小
SWAP_DIR="/swap"                  # 交换文件目录
SWAP_FILE="$SWAP_DIR/swapfile"    # 交换文件完整路径
```

## 故障排除

### 常见问题

1. **权限错误**
   ```
   错误：必须使用 sudo 运行此脚本！
   ```
   **解决方案**: 使用 `sudo` 执行脚本

2. **Btrfs 空洞错误**
   ```
   swap file has holes
   ```
   **解决方案**: 脚本已内置针对 Btrfs 的特殊处理，自动解决空洞问题

3. **磁盘空间不足**
   ```
   错误：磁盘空间不足！
   ```
   **解决方案**: 检查目标分区空间，或选择较小的交换文件大小

4. **文件已存在**
   ```
   交换文件已存在且启用中
   ```
   **解决方案**: 脚本会提示是否覆盖，输入 `y` 确认重新创建

### 手动诊断命令

```bash
# 检查交换空间状态
sudo swapon --show
free -h

# 检查文件系统类型
df -T /swap

# 查看内核消息
dmesg | grep -i swap
```

## Btrfs 特别说明

对于 Btrfs 文件系统用户，脚本自动处理以下特殊配置：

- **禁用 CoW**: 使用 `chattr +C` 禁用写时复制
- **禁用压缩**: 设置压缩属性为 `none`
- **连续空间分配**: 使用特殊方法避免文件空洞

## 注意事项

⚠️ **重要提示**:
- 执行前确保有足够的磁盘空间
- 建议在系统空闲时操作，避免影响性能
- Btrfs 用户请注意交换文件不会被快照包含

