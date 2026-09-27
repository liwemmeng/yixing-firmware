# 义星固件 OTA

本仓库用于发布义星设备的 OTA 应用固件。

## 当前支持的设备

- 板型：`xingzhi-cube-1.54tft-wifi`
- 芯片：ESP32-S3
- OTA APP 分区上限：`0x450000`（4,521,984 字节）

## 升级通道

仓库提供两个相互独立的更新清单：

- 正式通道：`stable/latest.json`
- 内测通道：`beta/latest.json`
- 旧版正式通道兼容入口：根目录 `latest.json`

已经烧录且读取根目录 `latest.json` 的设备不需要重新刷机。根目录清单继续作为正式通道的兼容入口，发布正式版时必须与 `stable/latest.json` 保持完全一致。

内测设备使用：

```text
https://raw.githubusercontent.com/liwemmeng/yixing-firmware/main/beta/latest.json
```

正式设备和未来的新正式固件使用：

```text
https://raw.githubusercontent.com/liwemmeng/yixing-firmware/main/stable/latest.json
```

已经发布、仍读取旧地址的正式设备使用：

```text
https://raw.githubusercontent.com/liwemmeng/yixing-firmware/main/latest.json
```

## 文件说明

- GitHub Releases 中的 `.bin` 文件是 OTA 专用 APP 镜像。
- OTA 只使用项目构建输出中的 `build/xiaozhi.bin`。
- 不要使用 `merged-binary.bin`、`bootloader.bin` 或 `partition-table.bin` 进行 APP OTA。
- `beta/latest.json` 是内测设备检查更新时读取的版本清单。
- `stable/latest.json` 是正式设备检查更新时读取的版本清单。
- 根目录 `latest.json` 是旧版正式设备的兼容入口，内容必须与 `stable/latest.json` 一致。

## 发布顺序

1. 使用正确板型和 ESP-IDF 版本完成最终构建。
2. 确认 `build/xiaozhi.bin` 不超过 APP 分区上限。
3. 计算最终固件的文件大小和 SHA-256。
4. 创建新的 GitHub Release 或上传不可变的原始 `.bin` 文件。
5. 确认下载链接可用后，先更新 `beta/latest.json`，仅向内测设备推送。
6. 在内测设备上验证下载、SHA-256 校验、分区切换、重启和主要功能。
7. 验证通过后不要重新编译，直接将已经验证的同一个 `.bin`、大小和 SHA-256 写入 `stable/latest.json`。
8. 同步更新根目录 `latest.json`，保证旧版正式设备收到相同的正式版本。

同一版本的 Release 文件不要覆盖；修复后请提升版本号重新发布。

## 通道切换规则

设备读取哪个通道由设备固件中的清单 URL 决定，不是由 GitHub 自动识别：

- 固件配置为 `beta/latest.json`，就是内测设备。
- 固件配置为 `stable/latest.json`，就是正式设备。
- 现有固件配置为根目录 `latest.json`，按兼容规则视为正式设备。

如果固件没有提供运行时切换通道的设置，那么从内测切换到正式通道需要先安装一版将清单 URL 改为正式地址的固件。这个迁移固件可以通过内测 OTA 下发，不一定需要使用 USB 重新烧录。
